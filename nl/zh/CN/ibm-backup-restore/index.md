---

copyright:
  years: 2017, 2018
lastupdated: "2018-10-29"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip} 
{:table: .aria-labeledby="caption"}

# 开始使用 `ibm-backup-restore` 映像
{: #ibmbackup_restore_starter}

`ibm-backup-restore` 映像包含在 {{site.data.keyword.containerlong}} 中备份和复原持久性存储器所需的预安装软件包。
{:shortdesc}

您可以使用命令行来访问 {{site.data.keyword.IBM_notm}} 提供的映像，请参阅 [IBM 公共映像](/docs/services/Registry/registry_public_images.html#public_images)。
{: tip}

## 工作原理 
{: #how_it_works}

通过 `ibm-backup-restore` 映像，您可以为集群中持久性卷 (PV) 中存储的应用程序数据创建一次性备份或安排的备份，或者将应用程序数据复原到 PV。要备份和复原数据，请基于 `ibm-backup-restore` 映像部署 pod。然后，安装特定 PVC，该 PVC 绑定了要备份的 PV，或绑定了要用于将数据复原到 pod 的 PV。 

**我的数据在哪里？如何访问我的数据？**备份的数据存储在 {{site.data.keyword.cos_full_notm}} 服务实例内。要访问该服务，请将 {{site.data.keyword.cos_full_notm}} 服务凭证用作 `ibm-backup-restore` pod 中的环境变量，或者编辑正在运行的 pod 中的 `config.conf` 文件。

**可以将备份数据复原到其他应用程序或其他 PV 吗？**可以，您可以将已保存的数据从 {{site.data.keyword.cos_full_notm}} 服务实例复原到集群中的 PV。要复原数据，请基于 `ibm-backup-restore` 映像创建复原 pod。然后，安装绑定了要用于 pod 的 PV 的 PVC。  

## 所含内容 
{: #whats_included}

每个 `ibm-backup-restore` 映像都包含以下软件包：

-   Alpine 3.7
-   Duplicity 0.7.10
-   python 和 gpgme 包


## 设置 {{site.data.keyword.cos_full_notm}} 服务实例 
{: #object_storage}

创建并配置 {{site.data.keyword.cos_full_notm}} 服务实例，以充当要备份的数据的存储库。
{: shortdesc}

1. 部署 {{site.data.keyword.cos_full_notm}} 服务实例。
   1.  打开 [{{site.data.keyword.Bluemix_notm}}“目录”![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://console.bluemix.net/catalog/services/cloud-object-storage)。
   2.  输入服务实例的名称，例如 `cos-backup`，然后选择 **default** 作为资源组。 
   3.  查看[套餐选项 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://www.ibm.com/cloud-computing/bluemix/pricing-object-storage#s3api) 以获取定价信息，然后选择套餐。 
   4.  单击**创建**。
2. 检索 {{site.data.keyword.cos_full_notm}} 服务实例凭证。
   1.  在“服务详细信息”页面的导航中，单击**服务凭证**。
   2.  单击**新建凭证**。这将显示一个对话框。 
   3.  输入凭证的名称。
   4.  在**添加内联配置参数（可选）**中，输入 `{"HMAC":true}` 以创建其他 HMAC 凭证，供 `ibm-backup-restore` pod 用于向 {{site.data.keyword.cos_full_notm}} 服务进行 HMAC 认证。 
   5.  单击**添加**。新凭证会在**服务凭证**表中列出。
   6.  单击**查看凭证**。 
   7.  记下可以在 **cos_hmac_keys** 部分中找到的 **access_key_id** 和 **secret_access_key**。 
3. 创建第一个 {{site.data.keyword.cos_full_notm}} 存储区。
   1. 在“服务详细信息”页面的导航中，单击**存储区**。 
   2. 单击**创建存储区**。这将显示一个对话框。
   3. 输入存储区的唯一名称。该名称必须在所有区域上的 {{site.data.keyword.cos_full_notm}} 中以及在所有 {{site.data.keyword.Bluemix_notm}} 帐户上唯一。 
   4. 从**弹性**下拉列表中，选择希望数据具有的可用性级别。有关更多信息，请参阅 [{{site.data.keyword.cos_full_notm}} 区域和端点](/docs/services/cloud-object-storage/basics/endpoints.html#select-regions-and-endpoints)。 
   5. 将**位置**更改为要存储数据的区域。请记住，由于法律方面的原因，并非每个区域都允许存储您的数据。  
   6. 单击**创建**。 
4. 检索存储区的 {{site.data.keyword.cos_full_notm}} 主机名。 
   1. 单击在上一步中创建的存储区名称。 
   2. 在“服务详细信息”页面的导航中，单击**存储区** > **配置**。 
   3. 记下可用于访问存储区中数据的公共 URL。 


查看 [{{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage/about-cos.html#about-ibm-cloud-object-storage) 文档，以获取有关配置服务实例的更多信息。


## 备份持久性卷中的数据
{: #scheduled_backup}

您可以为通过持久性卷申领 (PVC) 安装到应用程序 pod 的任何持久性卷 (PV) 创建一次性备份或安排的备份。  
{: shortdesc}

以下示例将指导您如何基于 `ibm-backup-restore` 映像部署备份 pod，使用 PVC 将现有 PV 安装到备份 pod，以及将 PV 中的数据备份到 {{site.data.keyword.cos_full_notm}} 服务实例。  

**开始之前**

-   [设置 {{site.data.keyword.cos_full_notm}} 服务实例](#object_storage)。 
-   安装必需的 [CLI](/docs/containers/cs_cli_install.html#cs_cli_install)，以创建并使用集群。
-   [创建标准集群](/docs/containers/cs_clusters.html#clusters_cli)或使用现有标准集群。
-   [设定 CLI 的目标为集群](/docs/containers/cs_cli_install.html#cs_cli_configure)。
-   针对[文件存储器](/docs/containers/cs_storage_file.html#add_file)或[块存储器](/docs/containers/cs_storage_block.html#add_block)创建持久性卷申领 (PVC) 并将其安装到应用程序部署。

要备份现有 PV，请完成以下步骤： 

1. 获取绑定了要备份的 PV 的 PVC 的名称。

   ```
   kubectl get pvc
   ```
   {: pre}

2. 基于 `ibm-backup-restore` 映像创建备份 pod。要访问 PV 中的数据，必须将绑定了该 PV 的 PVC 安装到备份 pod。以下示例创建的是运行每日增量备份的备份 pod。要使用不同的设置创建备份，请复查[环境变量选项](#reference_backup_restore)的完整列表。

   **重要信息**：`ibm-backup-restore` 映像必须部署在单个 pod 中，不能用作 Kubernetes 部署的一部分。
   
   要查看映像，请通过运行 `ibmcloud cr region-set global` 命令将全局注册表设定为目标。然后，运行 `ibmcloud cr images --include-ibm` 以列出 IBM 公共映像。
   {: tip}
  
   ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: backuppod
   spec:
     containers:
     - image: registry.bluemix.net/ibm-backup-restore
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
       - mountPath: /myvol 
         name: backup-volume 
     volumes:
     - name: backup-volume 
       persistentVolumeClaim:
         claimName: <pvc_name>  
   ```
   {: codeblock}
   
   <table>
   <caption>YAML 文件的组成部分</caption>
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
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>绑定了要备份的 PV 的 PVC 的名称。</td>
     </tr>
     </tbody>
     </table>
    
3.  创建备份 pod 并启动 PV 数据的备份。 

    ```
    kubectl apply -f backuppod.yaml
    ```
    {: pre}

4.  验证 pod 是否正在运行。

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}
    
5.  验证备份是否已成功运行。
     

    ```
    kubectl logs backuppod
    ```
    {: pre}

6.  在 {{site.data.keyword.Bluemix_notm}} GUI 中复查 {{site.data.keyword.cos_full_notm}} 中的备份。
    1.  在 {{site.data.keyword.Bluemix_notm}}“仪表板”中，找到 {{site.data.keyword.cos_full_notm}} 服务实例。 
    2.  在导航中，选择**存储区**，然后单击在备份配置中使用的存储区。您的备份会显示为存储区中的对象。 
    3.  复查压缩文件。您可以下载 `vol1.difftar.gz` 文件，解压缩该文件，然后验证备份数据。 
        
        **重要信息**：如果您从 {{site.data.keyword.cos_full_notm}} 删除或修改任何文件，那么无法恢复那些文件。

您的备份可用。如果您已将备份配置为创建一次性完整备份，那么在您每次想要创建新备份时必须运行备份脚本。如果您已将容器配置为定期运行增量备份，那么您的备份会按所安排的时间运行。

## 将数据从 {{site.data.keyword.cos_full_notm}} 复原到集群
{: #restore_script_cli}

您可以将数据从 {{site.data.keyword.cos_full_notm}} 服务实例复原到集群中的 PV。 

**开始之前**

-   [设定 CLI 的目标为集群](/docs/containers/cs_cli_install.html#cs_cli_configure)。
-   [为集群中的 PV 创建备份](#scheduled_backup)。

要将数据从 {{site.data.keyword.cos_full_notm}} 复原到 PV，请完成以下步骤： 

1. 获取绑定了要在其中复原数据的 PV 的 PVC 的名称。 

   ```
   kubectl get pvc
   ```
   {: pre}

2. 基于 `ibm-backup-restore` 映像创建复原 pod。要将数据复原到 PV，必须将绑定了该 PV 的 PVC 安装到复原 pod。 
   
   ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: restorepod
   spec:
     containers:
     - image: registry.bluemix.net/ibm-backup-restore 
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
       - mountPath: /myvol  
         name: restore-volume
     volumes:
     - name: restore-volume  
       persistentVolumeClaim:
         claimName: <pvc_name> 
   ```
   {: codeblock}
   
   <table>
   <caption>YAML 文件的组成部分</caption>
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
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>绑定了要在其中复原数据的 PV 的 PVC 的名称。</td>
     </tr>
     </tbody>
     </table>

3.  创建复原 pod，然后开始复原数据。
    

    ```
    kubectl apply -f restorepod.yaml
    ```
    {: pre}
    
4.  验证 pod 是否正在运行。

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    restorepod        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    pod 运行复原命令并停止。_CrashLoopBackOff_ 消息表示 Kubernetes 正尝试重新启动 pod。

5.  除去 pod 以阻止 pod 使用更多资源。

    ```
    kubectl delete -f restorepod.yaml
    ```
    {: pre}

6.  验证数据是否已成功复原。
    

    ```
    kubectl logs restorepod
    ```
    {: pre}

您已成功复原备份。现在，可以将绑定了 PV 的 PVC 安装到集群中的其他任何 pod，以访问已复原的文件。如果备份的容器数据包含非 root 用户，那么您必须向新容器添加非 root 许可权。有关更多信息，请参阅[添加卷的非 root 用户访问权](/docs/containers/cs_troubleshoot_storage.html#cs_storage_nonroot)。


## 加密备份 
{: #encrypting_backups}

加密 {{site.data.keyword.cos_full_notm}} 实例中的数据。

1.  下载 <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="外部链接图标"></a> 以创建加密密钥。
2.  在本地驱动器上创建加密密钥。通过按 ENTER 键，您可以接受缺省值。

    **重要信息：**请记下您创建的口令。如果您丢失口令，那么将无法解密使用密钥加密的任何信息。


    ```
    gpg --gen-key
    ```
    {: pre}

    根据 <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="外部链接图标"></a> 的版本，您可能需要在命令中使用 `gpg2` 而非 `gpg`。

3.  验证密钥。

    ```
    gpg --list-keys
    ```
    {: pre}

    ```
$ gpg --list-keys
    /Users/Username/.gnupg/pubring.gpg
    ------------------------------------
    pub   2048R/XXXXXXXX 2016-10-27
    uid       [ultimate] Example Name (This is an example key) <example_email_address>
    sub   2048R/YYYYYYYY 2016-10-27
    ```
    {: screen}

4.  从 `sub` 密钥导出具有值的加密密钥。将该文件命名为 `encryption.asc`。

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    在本示例中，sub 键的值为 `YYYYYYYY`。

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  在本地目录中为已加密的备份容器创建环境变量文件。

    ```
    touch <encryption_env-file_name>
    ```
    {: pre}

6.  编辑 pod 配置文件并添加以下字段。对于空白的环境变量，请从之前记下的 {{site.data.keyword.cos_full_notm}} 凭证输入值。包含在凭证中使用的引号。对于 **ENCRYPTION_PASSPHRASE**，请包含口令，以用密码保护备份。此口令与创建加密密钥时生成的口令不同。当您备份数据和复原数据时，必须包含此口令。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: backuppod
    spec:
      containers:
      - image: registry.bluemix.net/ibm-backup-restore
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
        - name: ENCRYPTION_REQUIRED
            value: yes
        - name: ENCRYPTION_PASSPHRASE 
          value: <passphrase>
        volumeMounts:
        - mountPath: /myvol 
          name: backup-volume 
      volumes:
      - name: backup-volume 
        persistentVolumeClaim:
          claimName: <pvc_name>  
    ```
    {: codeblock}
   
    <table>
    <caption>YAML 文件的组成部分</caption>
    <thead>
    <th colspan=2><img src="../images/idea.png" alt="“构想”图标"/> 了解 YAML 文件的组成部分</th>
    </thead>
     <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>作为 {{site.data.keyword.cos_full_notm}} 服务实例凭证的一部分检索到的访问密钥标识。</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</em></code></td>
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
     <td><code>&lt;passphrase&gt;</code></td>
     <td>要用于备份的任何字符串。复原数据时，必须包含此口令。</td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>绑定了要备份的 PV 的 PVC 的名称。</td>
     </tr>
     </tbody>
     </table>
   
    这些设置会创建加密的每日增量备份。要使用不同的设置创建备份，请复查[环境变量选项](#reference_backup_restore)的完整列表。
    
7.  创建备份 pod。 

    ```
    kubectl apply -f backuppod.yaml 
    ```
    {: pre}

8.  验证 pod 是否正在运行。

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

9.  将加密密钥复制到容器的 `/backup_restore` 目录，该容器是基于 `ibm-backup-restore` 映像构建的。

    ```
    kubectl cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    在本地保留加密密钥的副本，以便解密数据。

10. 登录到 pod，并浏览至 `backup_restore` 文件夹。 

    ```
    kubecl exec -it <pod_name> bash
    ```
    {: pre}

11. 验证 `encryption.asc` 文件是否已复制到 `backup_restore` 文件夹。

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

12. 从 backup_restore 文件夹运行备份脚本。

    

    ```
    ./vbackup &
    ```
    {: codeblock}

13. 要确认备份是否已加密，请复查 {{site.data.keyword.cos_full_notm}} 服务实例中的文件。现在，这些文件的文件名末尾会附加 `.gpg`。

您的备份已加密。要复原文件，请遵循[将数据从 {{site.data.keyword.cos_full_notm}} 复原到集群中的 PVC](#restore_script_cli) 中的步骤，并在运行复原过程的 pod 的 `backup_restore` 目录中包含 `encryption.asc` 文件。如果备份已加密，那么您必须在创建复原 pod 时提供 **ENCRYPTION_REQUIRED** 和 **ENCRYPTION_PASSPHRASE** 环境变量。


## 环境变量参考 
{: #reference_backup_restore}

复查可作为环境变量传递或可在运行中 pod 的 `config.conf` 文件中编辑的字段完整列表。作为环境变量传递的任何值会取代 `config.conf` 文件中的值。要复查 pod 的环境变量，请使用 `kubectl exec` 命令登录到 pod 并运行 `env`。

|键|值选项|
|---|-------------|
|ACCESS_KEY_ID|作为 {{site.data.keyword.cos_full_notm}} 中 HMAC 凭证一部分的 **access_key_id**。|
|SECRET_ACCESS_KEY|作为 {{site.data.keyword.cos_full_notm}} 中 HMAC 凭证一部分的 **secret_access_key**。|
|ENDPOINT|用于访问 {{site.data.keyword.cos_full_notm}} 存储区数据的主机名。|
|BUCKET|在 {{site.data.keyword.cos_full_notm}} 中存储备份数据的存储区的名称。|
{: caption="表 1. {{site.data.keyword.cos_full_notm}} 变量" caption-side="top"}

|键|值选项|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*：缺省值。卷要安装到的目录的绝对文件路径。数据从此目录进行备份。请勿选择目录 backup_restore，因为该目录包含用于备份和复原过程的文件。|
|BACKUP_NAME|*volume_backup*：缺省值。选择备份名称。|
|BACKUP_TYPE|*full*：缺省值。每次备份所有文件。<br/> *incremental*：仅备份新的或更改的文件。如果您选择 *incremental*，那么您必须选择 **SCHEDULING_INFO** 和 **SCHEDULING_TYPE** 的值。|
|SCHEDULE_TYPE|*none*：缺省值。创建一次性备份。<br/> **注：**如果选择创建一次性备份，那么备份完成后，会从集群中除去您的 pod。<br/> *periodic*：将值更改为 periodic，以创建安排的备份。|
|SCHEDULE_INFO|*hourly*：创建每小时备份。<br/>*daily*：缺省值。创建每日备份。<br/>*weekly*：创建每周备份。如果安排定期更新，那么必须包括此变量。|
|EXCLUDE_DIRECTORIES|*none*：缺省值。包括您要从备份排除的目录的绝对文件路径。以逗号分隔目录。|
{: caption="表 2. 备份变量" caption-side="top"}

|键|值选项|
|---|-------------|
|BACKUP_NAME|*volume_backup*：缺省值。包括从 {{site.data.keyword.cos_full_notm}} 复原的备份的名称。|
|RESTORE_DIRECTORY|*/backup*：缺省值。卷要安装到的绝对目录。数据复原到此目录。请勿选择 `backup_restore` 目录，因为该目录包含用于备份和复原过程的文件。
|
{: caption="表 3. 复原变量" caption-side="top"}

|键|值选项|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*：缺省值。如果您更改加密密钥的文件名或密钥位于 `backup_restore` 之外的目录中，请包含此环境变量。|
|ENCRYPTION_REQUIRED|*no*：缺省值。<br/> *yes*：如果您未加密备份，请勿包含任何加密环境变量。如果您要加密备份，请包含此密钥并将值设置为 `yes`。|
|ENCRYPTION_PASSPHRASE|包含用于保护备份的口令。此口令与创建加密密钥时生成的口令不同。备份数据和复原数据时，必须包含此口令。|
|IS_KEY_GENERATED_ON_SYSTEM|*no*：缺省值。<br/> *yes*：如果您已在容器上直接生成加密密钥，请包含此环境变量并将值设置为 `yes`。大部分用户在其本地计算机上生成密钥，并将密钥复制到 pod，因此可保留缺省值 `no`。|
{: caption="表 4. 加密变量" caption-side="top"}

