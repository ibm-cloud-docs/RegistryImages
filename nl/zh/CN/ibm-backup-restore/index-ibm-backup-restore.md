---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-21"

keywords: IBM Cloud Container Registry, IBM Cloud Kubernetes Service, ibm-backup-restore, container image, back up data, restore data

subcollection: RegistryImages

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:table: .aria-labeledby="caption"}

# 开始使用 `ibmcloud-backup-restore` 映像
{: #ibmbackup_restore_starter}

`ibmcloud-backup-restore` 映像包含在 {{site.data.keyword.containerlong}} 中备份和复原持久性存储器所需的预安装软件包。
{:shortdesc}

`ibmcloud-backup-restore` 映像仅在 `icr.io` 注册表域中可用，而不推荐在 `registry.bluemix.net` 域中使用。要访问映像并从中部署容器，您的集群必须在映像拉取私钥中存储 API 密钥，以授权访问 {{site.data.keyword.registryshort_notm}}。对于在 2019 年 2 月 25 日之前创建的集群，必须更新集群以有权访问 `icr.io` 注册表域，以便您可以使用 `ibmcloud-backup-restore` 映像。有关集群的更改内容和更新方式的更多信息，请参阅[了解如何授权集群从注册表中拉取映像](/docs/containers?topic=containers-images#cluster_registry_auth)和[更新现有集群以使用 API 密钥映像拉取私钥](/docs/containers?topic=containers-images#imagePullSecret_migrate_api_key)。
{: important}

您可以使用命令行来访问 {{site.data.keyword.IBM_notm}} 提供的映像，请参阅 [IBM 公共映像](/docs/services/Registry?topic=registry-public_images#public_images)。
{: tip}

## 工作原理
{: #backup_restore_how_it_works}

通过 `ibmcloud-backup-restore` 映像，您可以为集群中持久性卷 (PV) 中存储的应用程序数据创建一次性备份或安排的备份，或者将应用程序数据复原到 PV。要备份和复原数据，请基于 `ibmcloud-backup-restore` 映像部署 pod。然后，安装特定 PVC，该 PVC 绑定了要备份的 PV，或绑定了要用于将数据复原到 pod 的 PV。

**我的数据在哪里？如何访问我的数据？**备份的数据存储在 {{site.data.keyword.cos_full_notm}} 服务实例内。要访问该服务，请将 {{site.data.keyword.cos_full_notm}} 服务凭证用作 `ibmcloud-backup-restore` pod 中的环境变量，或者编辑正在运行的 pod 中的 `config.conf` 文件。

**可以将备份数据复原到其他应用程序或其他 PV 吗？**可以，您可以将已保存的数据从 {{site.data.keyword.cos_full_notm}} 服务实例复原到集群中的 PV。要复原数据，请基于 `ibmcloud-backup-restore` 映像创建复原 pod。然后，安装绑定了要用于 pod 的 PV 的 PVC。  

## 所含内容
{: #backup_restore_whats_included}

每个 `ibmcloud-backup-restore` 映像都包含以下软件包：

- Alpine 3.7
- Duplicity 0.7.10
- Python 和 GnuPG Made Easy (GPGme) 软件包

## 设置 {{site.data.keyword.cos_full_notm}} 服务实例
{: #backup_restore_setup_object_storage}

创建并配置 {{site.data.keyword.cos_full_notm}} 服务实例，以充当要备份的数据的存储库。
{: shortdesc}

1. 部署 {{site.data.keyword.cos_full_notm}} 服务实例。
   1. 打开 [{{site.data.keyword.cloud_notm}}“目录”![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/catalog/services/cloud-object-storage)。
   2. 输入服务实例的名称，例如 `cos-backup`，然后选择 **default** 作为资源组。
   3. 查看[套餐选项 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://www.ibm.com/cloud-computing/bluemix/pricing-object-storage#s3api) 以获取定价信息，然后选择套餐。
   4. 单击**创建**。
2. 检索 {{site.data.keyword.cos_full_notm}} 服务实例凭证。
   1. 在“服务详细信息”页面的导航中，单击**服务凭证**。
   2. 单击**新建凭证**。此时会显示一个对话框。
   3. 输入凭证的名称。
   4. 在**添加内联配置参数（可选）**字段中，输入 `{"HMAC":true}` 以创建其他 HMAC 凭证，供 `ibmcloud-backup-restore` pod 用于向 {{site.data.keyword.cos_full_notm}} 服务进行 HMAC 认证。
   5. 单击**添加**。新凭证会在**服务凭证**表中列出。
   6. 单击**查看凭证**。
   7. 记下可以在 **cos_hmac_keys** 部分中找到的 **access_key_id** 和 **secret_access_key**。
3. 创建第一个 {{site.data.keyword.cos_full_notm}} 存储区。
   1. 在“服务详细信息”页面的导航中，单击**存储区**。
   2. 单击**创建存储区**。此时会显示一个对话框。
   3. 输入存储区的唯一名称。该名称必须在所有区域上的 {{site.data.keyword.cos_full_notm}} 中以及在所有 {{site.data.keyword.cloud_notm}} 帐户上唯一。
   4. 从**弹性**列表中，选择希望数据具有的可用性级别。有关更多信息，请参阅 [{{site.data.keyword.cos_full_notm}} 区域和端点](/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-endpoints#endpoints)。
   5. 将**位置**更改为要存储数据的区域。请记住，由于法律方面的原因，并非每个区域都允许存储您的数据。  
   6. 单击**创建**。
4. 检索存储区的 {{site.data.keyword.cos_full_notm}} 主机名。
   1. 单击在上一步中创建的存储区名称。
   2. 在“服务详细信息”页面的导航中，单击**存储区** > **配置**。
   3. 记下可用于访问存储区中数据的公共 URL。

查看 [{{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage?topic=cloud-object-storage-about-ibm-cloud-object-storage) 文档，以获取有关配置服务实例的更多信息。

## 备份持久性卷中的数据
{: #backup_restore_scheduled_backup}

您可以为通过持久性卷申领 (PVC) 安装到应用程序 pod 的任何持久性卷 (PV) 创建一次性备份或安排的备份。  
{: shortdesc}

以下示例显示了如何基于 `ibmcloud-backup-restore` 映像部署备份 pod，使用 PVC 将现有 PV 安装到备份 pod，以及将 PV 中的数据备份到 {{site.data.keyword.cos_full_notm}} 服务实例。  

**开始之前**

- [设置 {{site.data.keyword.cos_full_notm}} 服务实例](#backup_restore_setup_object_storage)。
- 安装必需的 [CLI](/docs/containers?topic=containers-cs_cli_install#cs_cli_install)，以创建并使用集群。
- [创建标准集群](/docs/containers?topic=containers-clusters#clusters_cli)或使用现有标准集群。
- [设定 CLI 的目标为集群](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)。
- 针对[文件存储器](/docs/containers?topic=containers-file_storage#add_file)或[块存储器](/docs/containers?topic=containers-block_storage#add_block)创建持久性卷申领 (PVC) 并将其安装到应用程序部署。

要备份现有 PV，请完成以下步骤：

1. 获取绑定了要备份的 PV 的 PVC 的名称。

   ```
   kubectl get pvc
   ```
   {: pre}

2. 基于 `ibmcloud-backup-restore` 映像创建备份 pod。要访问 PV 中的数据，必须将绑定了该 PV 的 PVC 安装到备份 pod。以下示例创建的是运行每日增量备份的备份 pod。要使用不同的设置创建备份，请复查[环境变量选项](#backup_restore_env_reference)的完整列表。

   `ibmcloud-backup-restore` 映像必须部署在单个 pod 中，不能用作 Kubernetes 部署的一部分。
   {: important}

   要查看映像，请通过运行 `ibmcloud cr region-set global` 命令将全局注册表设定为目标。然后，运行 `ibmcloud cr images --include-ibm` 以列出 IBM 公共映像。
   {: tip}
  
   ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: backuppod
   spec:
     containers:
     - image: icr.io/ibm/ibmcloud-backup-restore
       name: backupcontainer
       env:
       - name: OBJECTSTORAGE
         value: S3
       - name: ACCESS_KEY_ID
         value: '<access_key_id>'
       - name: SECRET_ACCESS_KEY
         value: '<secret_access_key>'
       - name: ENDPOINT
         value: '<regional_endpoint>'
       - name: BUCKET_NAME
         value: '<bucket_name>'
       - name: BACKUP_DIRECTORY  
         value: /myvol
       - name: BACKUP_NAME
         value: <backup_name>
       - name: SCHEDULE_TYPE
         value: periodic
       - name: SCHEDULE_INFO
         value: daily
       - name: BACKUP_TYPE
         value: incremental
       command: ["/bin/bash", "./vbackup"]
       volumeMounts:
       - mountPath: <mount_path1>
         name: <pvc_name1>
       - mountPath: <mount_path1>
         name: <pvc_name2>
     volumes:
     - name: pvc_name1
       persistentVolumeClaim:
         claimName: <pvc_name1>  
     - name: pvc_name2
       persistentVolumeClaim:
         claimName: <pvc_name2> 
   ```
   {: codeblock}

   <table>
   <caption>表 1. YAML 文件的组成部分</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="“构想”图标"/> 了解 YAML 文件的组成部分</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>作为 {{site.data.keyword.cos_full_notm}} 服务实例凭证的一部分检索到的访问密钥标识。</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</code></td>
     <td>作为 {{site.data.keyword.cos_full_notm}} 服务实例凭证的一部分检索到的访问密钥。</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>用于访问特定区域中 {{site.data.keyword.cos_full_notm}} 的区域 API 端点的 URL。</td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>要用于在 {{site.data.keyword.cos_full_notm}} 中存储备份的存储区的名称。</td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>用于将备份保存在存储区中的对象的唯一名称。</td>
     </tr>
     <tr>
     <td><code>&lt;mount_path&gt;</code></td>
     <td>用于安装要备份的 PVC 的备份容器中的卷安装路径。如果要备份多个 PVC，请为每个 PVC 指定单独的安装路径。请确保您在 <code>volumeMount.name</code> 中使用的名称与您在 YAML 文件 <code>volumes.name</code> 部分中指定的卷的名称相匹配。</td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>要备份的 PVC 的名称。如果要备份多个 PVC，请为每个要备份的 PVC 指定卷名和对应的 PVC。请确保您在 <code>volumes.name</code> 中使用的名称与您 YAML 文件 <code>volumeMount.name</code> 部分中的名称相匹配。</td>
     </tr>
     </tbody>
     </table>

3. 创建备份 pod 并启动 PV 数据的备份。

    ```
    kubectl apply -f backuppod.yaml
    ```
    {: pre}

4. 验证 pod 是否正在运行。

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

5. 验证备份是否已成功运行。

    ```
    kubectl logs backuppod
    ```
    {: pre}

6. 在 {{site.data.keyword.cos_full_notm}} GUI 中复查 {{site.data.keyword.cloud_notm}} 中的备份。
    1. 在 {{site.data.keyword.cloud_notm}}“仪表板”中，找到 {{site.data.keyword.cos_full_notm}} 服务实例。
    2. 在导航中，选择**存储区**，然后单击在备份配置中使用的存储区。您的备份会显示为存储区中的对象。
    3. 复查压缩文件。您可以下载 `vol1.difftar.gz` 文件，解压缩该文件，然后验证备份数据。

       如果您从 {{site.data.keyword.cos_full_notm}} 删除或修改任何文件，那么无法恢复那些文件。
       {: important}

您的备份可用。如果您已将备份配置为创建一次性完整备份，那么在您每次想要创建新备份时必须运行备份脚本。如果您已将容器配置为定期运行增量备份，那么您的备份会按所安排的时间运行。

## 将数据从 {{site.data.keyword.cos_full_notm}} 复原到集群
{: #backup_restore_restore_script_cli}

您可以将数据从 {{site.data.keyword.cos_full_notm}} 服务实例复原到集群中的 PV。

如果您的 {{site.data.keyword.cos_full_notm}} 服务实例中有多个完全备份，那么会使用上次完全备份的数据复原 PVC。如果有增量备份，PVC 将使用上次完全备份的数据进行复原，包括截至开始复原的日期的所有增量备份。

**开始之前**

- [设定 CLI 的目标为集群](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)。
- [为集群中的 PV 创建备份](#backup_restore_scheduled_backup)。

要将数据从 {{site.data.keyword.cos_full_notm}} 复原到 PV，请完成以下步骤：

1. 获取绑定了要在其中复原数据的 PV 的 PVC 的名称。

   ```
   kubectl get pvc
   ```
   {: pre}

2. 基于 `ibmcloud-backup-restore` 映像创建复原 pod。要将数据复原到 PV，必须将绑定了该 PV 的 PVC 安装到复原 pod。
   {: codeblock}

    ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: restorepod
   spec:
     containers:
     - image: icr.io/ibm/ibmcloud-backup-restore
       name: restorecontainer
       env:
       - name: OBJECTSTORAGE
          value: S3
       - name: ACCESS_KEY_ID
         value: '<access_key_ID>'
       - name: SECRET_ACCESS_KEY 
          value: '<secret_access_key>'
       - name: ENDPOINT 
          value: '<regional_endpoint>'
       - name: BUCKET_NAME 
          value: '<bucket_name>'
       - name: RESTORE_DIRECTORY
         value: /myvol
       - name: BACKUP_NAME
         value: <backup_name>
       command: ["/bin/sh", "./vrestore"]
       volumeMounts:
       - mountPath: <mount_path1>
         name: <pvc_name1>
       - mountPath: <mount_path1>
         name: <pvc_name2>
     volumes:
     - name: pvc_name1
       persistentVolumeClaim:
         claimName: <pvc_name1>  
     - name: pvc_name2
       persistentVolumeClaim:
         claimName: <pvc_name2> 
   ```
   {: codeblock}

   <table>
   <caption>表 2. YAML 文件的组成部分</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="“构想”图标"/> 了解 YAML 文件的组成部分</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>作为 {{site.data.keyword.cos_full_notm}} 服务实例凭证的一部分检索到的访问密钥标识。</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</code></td>
     <td>作为 {{site.data.keyword.cos_full_notm}} 服务实例凭证的一部分检索到的访问密钥。</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>用于访问特定区域中 {{site.data.keyword.cos_full_notm}} 的区域 API 端点的 URL。</td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>已用于在 {{site.data.keyword.cos_full_notm}} 中存储备份的存储区的名称。</td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>用于将备份保存在存储区中的对象的唯一名称。必须使用在备份 pod 中用于在 {{site.data.keyword.cos_full_notm}} 中存储数据的名称。</td>
     </tr>
     <td><code>&lt;mount_path&gt;</code></td>
     <td>用于安装要复原数据的 PVC 的复原容器中的卷安装路径。如果要为多个 PVC 复原数据，请为每个 PVC 指定单独的安装路径。请确保您在 <code>volumeMount.name</code> 中使用的名称与您在 YAML 文件 <code>volumes.name</code> 部分中指定的卷的名称相匹配。</td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>要复原数据的 PVC 的名称。要为多个 PVC 复原数据，请为每个 PVC 添加一个<code>卷</code>条目。请确保您在 <code>volumes.name</code> 中使用的名称与您 YAML 文件 <code>volumeMount.name</code> 部分中的名称相匹配。</td>
     </tr> 
     </tbody>
     </table>

3. 创建复原 pod，然后开始复原数据。

    ```
    kubectl apply -f restorepod.yaml
    ```
    {: pre}

4. 验证 pod 是否正在运行。

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME              READY     STATUS             RESTARTS   AGE
    restorepod        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

pod 运行复原命令并停止。`CrashLoopBackOff` 消息表示 Kubernetes 正尝试重新启动 pod。
5. 验证数据是否已成功复原。

    ```
    kubectl logs restorepod
    ```
    {: pre}

6. 除去 pod 以阻止 pod 使用更多资源。

    ```
    kubectl delete -f restorepod.yaml
    ```
    {: pre}

您已成功复原备份。现在，可以将绑定了 PV 的 PVC 安装到集群中的其他任何 pod，以访问已复原的文件。如果备份的容器数据包含非 root 用户，那么您必须向新容器添加非 root 许可权。有关更多信息，请参阅[添加卷的非 root 用户访问权](/docs/containers?topic=containers-cs_troubleshoot_storage#cs_storage_nonroot)。


## 环境变量参考
{: #backup_restore_env_reference}

复查可作为环境变量传递或可在运行中 pod 的 `config.conf` 文件中编辑的字段完整列表。作为环境变量传递的任何值会取代 `config.conf` 文件中的值。要复查 pod 的环境变量，请使用 `kubectl exec` 命令登录到 pod 并运行 `env`。

|键|值选项|
|---|-------------|
|ACCESS_KEY_ID|作为 {{site.data.keyword.cos_full_notm}} 中 HMAC 凭证一部分的 **access_key_id**。|
|SECRET_ACCESS_KEY|作为 {{site.data.keyword.cos_full_notm}} 中 HMAC 凭证一部分的 **secret_access_key**。|
|ENDPOINT|用于访问 {{site.data.keyword.cos_full_notm}} 存储区数据的主机名。|
|BUCKET|在 {{site.data.keyword.cos_full_notm}} 中存储备份数据的存储区的名称。|
{: caption="表 4. {{site.data.keyword.cos_full_notm}} 变量" caption-side="top"}

|键|值选项|
|---|-------------|
|BACKUP_DIRECTORY|`/backup`。缺省值。卷要安装到的目录的绝对文件路径。数据从此目录进行备份。请勿选择 `backup_restore` 目录，因为该目录包含用于备份和复原过程的文件。|
|BACKUP_NAME|`volume_backup`。缺省值。选择备份名称。|
|BACKUP_TYPE|`full`。缺省值。每次备份所有文件。<br/> `incremental`。仅备份新的或更改的文件。如果您选择 `incremental`，那么您必须选择 **SCHEDULING_INFO** 和 **SCHEDULING_TYPE** 的值。|
|SCHEDULE_TYPE|`none`。缺省值。创建一次性备份。<br/> **注：**如果选择创建一次性备份，那么备份完成后，会从集群中除去您的 pod。<br/> `periodic`。将值更改为 periodic，以创建安排的备份。|
|SCHEDULE_INFO|`hourly`。创建每小时备份。<br/>`daily`。缺省值。创建每日备份。<br/>`weekly`。创建每周备份。如果安排定期更新，那么必须包括此变量。|
|EXCLUDE_DIRECTORIES|`none`。缺省值。包括您要从备份排除的目录的绝对文件路径。以逗号分隔目录。|
{: caption="表 5. 备份变量" caption-side="top"}

|键|值选项|
|---|-------------|
|BACKUP_NAME|`volume_backup`。缺省值。包括从 {{site.data.keyword.cos_full_notm}} 复原的备份的名称。|
|RESTORE_DIRECTORY|`/backup`。缺省值。卷要安装到的绝对目录。数据复原到此目录。请勿选择 `backup_restore` 目录，因为该目录包含用于备份和复原过程的文件。|
{: caption="表 6. 复原变量" caption-side="top"}

