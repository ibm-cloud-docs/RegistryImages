---

copyright:
  years: 2017
lastupdated: "2017-11-09"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip} 
{:table: .aria-labeledby="caption"}

# 开始使用 ibm-backup-restore 映像
{: #ibmbackup_restore_starter}

**ibm-backup-restore** 映像包含在 {{site.data.keyword.containerlong}} 中备份和复原持久性存储器所需的预安装软件包。
{:shortdesc}

## 工作原理 
{: #how_it_works}

使用 **ibm-backup-restore** 映像，您可以为任何持久性卷申领 (pvc) 创建一次性备份或所安排的备份。备份存储在 {{site.data.keyword.objectstoragefull}} 实例内。您可以将 {{site.data.keyword.objectstorageshort}} 凭证作为环境变量或者通过编辑正在运行的容器中的 `config.conf` 文件，传递给 **ibm-backup-restore** 容器。您还可以将已保存的数据从 {{site.data.keyword.objectstorageshort}} 实例复原到卷。由于映像包含用于运行备份和复原的脚本，因此用户必须输入命令以启动相应的脚本。

## 所含内容 
{: #whats_included}

每个 **ibm-backup-restore** 映像都包含以下软件包：

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   python、gnupg 和 wget 软件包

## 入门 
{: #how_to_get_started}

复查以下任务以备份和复原数据：
1.  [创建 {{site.data.keyword.objectstorageshort}} 服务实例](#object_storage)
2.  [运行所安排的备份](#scheduled_backup)
3.  [运行复原脚本](#restore_script_cli)
4.  [加密备份](#encrypting_backups)
5.  [环境变量参考](#reference_backup_restore)

## 创建 {{site.data.keyword.objectstorageshort}} 服务实例 
{: #object_storage}

创建 {{site.data.keyword.objectstorageshort}} 实例，以充当卷备份的存储库。


1.  从 {{site.data.keyword.Bluemix_notm}} 目录的**存储器**部分供应 {{site.data.keyword.objectstorageshort}} 实例。
2.  单击 {{site.data.keyword.objectstorageshort}}。
3.  选择 {{site.data.keyword.objectstorageshort}} OpenStack Swift for {{site.data.keyword.Bluemix_notm}}。然后，单击“创建”。
3.  单击**服务凭证**选项卡。
4.  单击**新建凭证**。
5.  完成名称字段但将其他字段保留为空白。单击**添加**。
6.  新凭证现在会在**服务凭证**表中列出。
单击**查看凭证**。
7.  记下**项目标识**、**区域**、**用户标识**和**密码**。这些凭证允许您的 **ibm-backup-restore** 容器访问此 {{site.data.keyword.objectstorageshort}} 实例。

复查 [{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) 文档，以获取有关配置实例的更多信息，并复查[套餐选项](../../ObjectStorage/objectstorage_faq.html#account-payment)，以获取有关 {{site.data.keyword.objectstorageshort}} 定价的更多信息。

## 运行所安排的备份 
{: #scheduled_backup}

通过 **ibm-backup-restore** 映像创建容器 pod，并启动定期安排的备份。

开始之前：

-   安装必需的 [CLI](../../../containers/cs_cli_install.html#cs_cli_install)。
-   [设定 CLI 的目标](../../../containers/cs_cli_install.html#cs_cli_configure)为集群。


1. 创建名为 _backup-pvc.yaml_ 的配置文件。此配置文件会创建持久性卷申领 (pvc)，可将此 pvc 作为卷安装到备份 pod。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: backuppvc
      annotations:
        volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
    ```
    {: codeblock}
    
2. 创建 pvc。

    ```
    kubectl apply -f backup-pvc.yaml
    ```
    {: pre}

3.  创建名为 _backup.yaml_ 的配置文件。对于空白的环境变量，请从之前记下的 {{site.data.keyword.objectstorageshort}} 凭证输入值。包含在凭证中使用的引号。将 <em>&lt;pod_name&gt;</em>、<em>&lt;container_name&gt;</em> 和映像注册表 <em>&lt;region&gt;</em> 替换为相应值。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: <pod_name>
    spec:
      containers:
        - name: <container_name>
          image: registry.<region>.bluemix.net/ibm-backup-restore
          env:
          - name: USERID
            value:
          - name: PASSWORD
            value:
          - name: PROJECTID
            value:
          - name: REGION
            value:
          - name: SCHEDULE_TYPE
            value: periodic
          - name: SCHEDULE_INFO
            value: daily
          - name: BACKUP_TYPE
            value: incremental
          - name: BACKUP_DIRECTORY
            value: /backup
          - name: BACKUP_NAME
            value: mybackup
          command: ["/bin/bash", "./vbackup"]
          volumeMounts:
          - mountPath: /backup
            name: backup-volume
      volumes:
      - name: backup-volume 
        persistentVolumeClaim:
          claimName: backuppvc
    ```
    {: codeblock}
    
    这些设置可使用缺省名称 _mybackup_ 创建每日增量备份。要使用不同的设置创建备份，请复查[环境变量选项](#reference_backup_restore)的完整列表。

4.  创建用于运行备份脚本的 pod。

    ```
    kubectl apply -f backup.yaml
    ```
    {: pre}

5.  确认 pod 正在运行。

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

4.  在 {{site.data.keyword.Bluemix_notm}} GUI 中复查 {{site.data.keyword.objectstorageshort}} 中的备份。
    1.  单击您为备份所创建的 {{site.data.keyword.objectstorageshort}} 实例。

    2.  从**容器**表中的**管理**选项卡，单击 {{site.data.keyword.objectstorageshort}} 容器。
    3.  复查压缩文件。![位于 {{site.data.keyword.Bluemix_notm}} GUI 中的 Object Storage 容器显示备份的文件。](images/volume_backup_screenshot.png)您可以下载 vol1.difftar.gz 文件，解压缩该文件，然后验证备份数据。**重要信息**：如果您从 {{site.data.keyword.objectstorageshort}} 删除或修改任何文件，那么无法恢复那些文件。

您的备份可用。如果您已将备份配置为创建一次性完整备份，那么在您每次想要创建新备份时必须运行备份脚本。如果您已将容器配置为定期运行增量备份，那么您的备份会按所安排的时间运行。

## 运行复原脚本 
{: #restore_script_cli}

将备份从 {{site.data.keyword.objectstorageshort}} 复原到现有或新卷。

开始之前：

-   安装必需的 [CLI](../../../containers/cs_cli_install.html#cs_cli_install)。
-   [设定 CLI 的目标](../../../containers/cs_cli_install.html#cs_cli_configure)为集群。


1. 创建名为 _restore-pvc.yaml_ 的配置文件。此配置文件会创建持久性卷申领 (pvc)，可将此 pvc 作为卷安装到复原 pod。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: restorepvc
      annotations:
        volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
    ```
    {: codeblock}
    
2. 创建 pvc。

    ```
    kubectl apply -f restore-pvc.yaml
    ```
    {: pre}

3.  创建名为 _restore.yaml_ 的配置文件。对于空白的环境变量，请从之前记下的 {{site.data.keyword.objectstorageshort}} 凭证输入值。包含在凭证中使用的引号。BACKUP_NAME 必须与 {{site.data.keyword.objectstorageshort}} 中要复原的备份名称匹配。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: <pod_name>
    spec:
      containers:
        - name: <container_name>
          image: registry.<region>.bluemix.net/ibm-backup-restore
          env:
          - name: USERID
            value:
          - name: PASSWORD
            value:
          - name: PROJECTID
            value:
          - name: REGION
            value:
          - name: RESTORE_DIRECTORY
            value: /restore
          - name: BACKUP_NAME
            value: mybackup
          command: ["/bin/bash", "./vrestore"]
          volumeMounts:
          - mountPath: /restore
            name: restore-volume
      volumes:
      - name: restore-volume 
        persistentVolumeClaim:
          claimName: restorepvc
    ```
    {: codeblock}

4.  创建用于运行复原脚本的 pod。
    
    ```
    kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  确认 pod 正在运行。

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    pod 运行复原命令并停止。_CrashLoopBackOff_ 消息表示 Kubernetes 正尝试重新启动 pod。

6.  除去 pod 以阻止 pod 使用更多资源。

    ```
    kubectl delete -f restore.yaml
    ```
    {: pre}

您已成功复原备份。现在，您可以将任何新 pod 安装到 pvc，以向该容器提供对已复原文件的访问权。如果备份的容器数据包含非 root 用户，那么您必须向新容器添加非 root 许可权。有关更多信息，请参阅[添加卷的非 root 用户访问权](../../../containers/container_volumes_ov.html#container_volumes_write)。

## 加密备份 
{: #encrypting_backups}

加密 {{site.data.keyword.objectstorageshort}} 实例中的数据。

1.  下载 <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="外部链接图标"></a> 以创建加密密钥。
2.  在本地驱动器上创建加密密钥。通过按 ENTER 键，您可以接受缺省值。

    **注意：**请记下您创建的口令。如果您丢失口令，那么将无法解密使用密钥加密的任何信息。


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

4.  从 `sub` 密钥导出具有值的加密密钥。将该文件命名为 encryption.asc。

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    在本示例中，sub 密钥具有值 *YYYYYYYY*

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  在本地目录中为已加密的备份容器创建环境变量文件。

    ```
    touch <encryption_env-file_name>
    ```
    {: pre}
    
6. 创建名为 _backup-pvc.yaml_ 的配置文件。此配置文件会创建持久性卷申领 (pvc)，可将此 pvc 作为卷安装到备份 pod。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: backuppvc
      annotations:
        volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
    ```
    {: codeblock}

7. 创建 pvc。

    ```
    kubectl apply -f backup-pvc.yaml
    ```
    {: pre}

8.  编辑部署配置文件并添加以下字段。对于空白的环境变量，请从之前记下的 {{site.data.keyword.objectstorageshort}} 凭证输入值。包含在凭证中使用的引号。对于 *ENCRYPTION_PASSPHRASE*，请包含口令，以用密码保护备份。此口令与创建加密密钥时生成的口令不同。当您备份数据和复原数据时，必须包含此口令。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: <pod_name>
    spec:
      containers:
        - name: <container_name>
          image: registry.<region>.bluemix.net/ibm-backup-restore
          env:
          - name: USERID
            value:
          - name: PASSWORD
            value:
          - name: PROJECTID
            value:
          - name: REGION
            value:
          - name: SCHEDULE_TYPE
            value: periodic
          - name: SCHEDULE_INFO
            value: daily
          - name: BACKUP_TYPE
            value: incremental
          - name: BACKUP_DIRECTORY
            value: /backup
          - name: BACKUP_NAME
            value: mybackup
          - name: ENCRYPTION_REQUIRED
            value: yes
          - name: ENCRYPTION_PASSPHRASE
           # Include this passphrase when your are backing up and restoring encrypted data.
            value:
          volumeMounts:
          - mountPath: /backup
            name: backup-volume
      volumes:
      - name: backup-volume 
        persistentVolumeClaim:
          claimName: backuppvc
   ```
   
   这些设置可创建使用名称 <em>&lt;volume_name&gt;</em> 加密的每日增量备份。要使用不同的设置创建备份，请复查 [环境变量选项](#reference_backup_restore) 的完整列表。

9.  创建用于运行备份脚本的 pod。

    ```
    kubectl apply -f backup.yaml
    ```
    {: pre}

9.  确认 pod 正在运行。

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

10.  将加密密钥复制到容器的 /backup_restore 目录，该容器是基于 **ibm-backup-restore** 映像构建的。

    ```
    kubectl cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    在本地保留加密密钥的副本，以便解密数据。

11. 登录到容器。

    ```
    kubecl exec -it <container_name> bash
    ```
    {: pre}

12. 确认 *encryption.asc* 已复制到 backup_restore 文件夹。

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

13. 从 backup_restore 文件夹运行备份脚本。

    ```
    ./vbackup &
    ```
    {: codeblock}

13. 要确认已加密备份，请复查 {{site.data.keyword.objectstorageshort}} 实例中的文件。现在，文件在文件名末尾附加了 `.gpg`。![Object Storage GUI 显示附加了 .gpg 的所有备份文件，这说明这些文件已加密。](images/volume_backup_encrypt_screenshot.png)

您的备份已加密。要复原文件，请遵循 [运行复原脚本] (#restore_script_cli) 中的步骤，并在运行复原过程的容器上的 backup_restore 目录中包含 encryption.asc 文件。如果备份已加密，那么您必须在创建复原容器时提供备份 yaml 文件中的 ENCRYPTION_REQUIRED 和 ENCRYPTION_PASSPHRASE 作为环境变量。

## 环境变量参考
{: #reference_backup_restore}

复查可作为环境变量传递或可在运行中容器的 `config.conf` 文件中编辑的字段的完整列表。作为环境变量传递的任何值会取代 `config.conf` 文件中的值。要复查容器的环境变量，请使用 `exec` 登录到容器并运行 `env` 。

|密钥|值选项|
|---|-------------|
|PROJECTID|{{site.data.keyword.objectstorageshort}} 中的项目标识|
|REGION|{{site.data.keyword.objectstorageshort}} 中的区域|
|USERID|{{site.data.keyword.objectstorageshort}} 中的用户标识|
|PASSWORD|{{site.data.keyword.objectstorageshort}} 中的密码|
{: caption="表 1. {{site.data.keyword.objectstorageshort}} 变量" caption-side="top"}

从 {{site.data.keyword.objectstorageshort}} 凭证检索这些变量。在运行备份或复原操作的每个容器中包含这些环境变量。

|密钥|值选项|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*：缺省值。卷要安装到的目录的绝对文件路径。数据从此目录进行备份。请勿选择目录 backup_restore，因为该目录包含用于备份和复原过程的文件。|
|BACKUP_NAME|*volume_backup*：缺省值。选择备份名称。|
|BACKUP_TYPE|*full*：缺省值。每次备份所有文件。<br/> *incremental*：仅备份新的或更改的文件。如果选择 *incremental*，那么必须选择 SCHEDULING_INFO 和 SCHEDULING_TYPE 的值。|
|SCHEDULE_TYPE|*none*：缺省值。创建一次性备份。<br/> *periodic*：将值更改为 periodic，以创建所安排的备份。|
|SCHEDULE_INFO|*hourly*：创建每小时备份。<br/>*daily*：缺省值。创建每日备份。<br/>*weekly*：创建每周备份。如果您安排定期更新，那么您必须包括此变量。|
|EXCLUDE_DIRECTORIES|*none*：缺省值。包括您要从备份排除的目录的绝对文件路径。以逗号分隔目录。|
{: caption="表 2. 备份变量" caption-side="top"}

|密钥|值选项|
|---|-------------|
|BACKUP_NAME|*volume_backup*：缺省值。包括从 {{site.data.keyword.objectstorageshort}} 复原的备份的名称。|
|RESTORE_DIRECTORY|*/backup*：缺省值。卷要安装到的绝对目录。数据复原到此目录。请勿选择目录 backup_restore，因为该目录包含用于备份和复原过程的文件。|
{: caption="表 3. 复原变量" caption-side="top"}

|密钥|值选项|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*：缺省值。如果您更改加密密钥的文件名或密钥位于 backup_restore 之外的目录中，请包含此环境变量。|
|ENCRYPTION_REQUIRED|*no*：缺省值。<br/> *yes*：如果您未加密备份，请勿包含任何加密环境变量。如果您要加密备份，请包含此密钥并将值设置为 yes。|
|ENCRYPTION_PASSPHRASE|包含用于保护备份的口令。此口令与创建加密密钥时生成的口令不同。当您备份数据和复原数据时，必须包含此口令。|
|IS_KEY_GENERATED_ON_SYSTEM|*no*：缺省值。<br/> *yes*：如果您已在容器上直接生成加密密钥，请包含此环境变量并将值设置为 yes。大部分用户在其本地计算机上生成密钥，并将密钥复制到容器，并且可保留缺省值 no。|
{: caption="表 4. 加密变量" caption-side="top"}

## 将卷数据从单个可扩展容器迁移到 Kubernetes
{: #migrate_data}

为任何容器卷创建一次性备份。备份存储在 {{site.data.keyword.objectstoragefull}} 实例内。然后，可以将数据迁移到 Kubernetes 中的持久性卷申领。
{:shortdesc}

### 入门
{: #how_to_get_started}

开始之前：

- [复查用于将应用程序移动到 Kubernetes 的完整迁移路径](../../../containers/cs_classic.html)
- [安装单个可扩展容器 CLI (bx ic)](../../../containers/container_cli_cfic_install.html)
- [安装 {{site.data.keyword.containershort}} CLI（bx cs 和 kubectl）](../../../containers/cs_cli_install.html#cs_cli_install)
- [创建要将数据迁移到其中的标准 Kubernetes 集群](../../../containers/cs_cluster.html#cs_cluster_cli)

完成以下任务以执行备份和复原操作：
1.  [创建 {{site.data.keyword.objectstorageshort}} 服务实例](#object_storage)（先前已进行说明）
2.  [运行一次性备份](#migrate_backup)
3.  [在 Kubernetes 中运行复原脚本](#migrate_restore)

### 运行一次性备份
{: #migrate_backup}

从 **ibm-backup-restore** 映像创建单个容器并启动备份。

1.  登录到 {{site.data.keyword.containershort}} CLI。

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

2.  在本地目录中创建环境变量文件。

    ```
    touch <backup_env-file_name>
    ```
    {: pre}

3.  编辑环境变量文件并添加以下字段。对于空白的环境变量，请从之前记下的 {{site.data.keyword.objectstorageshort}} 凭证输入值。**请勿**包含在凭证中使用的引号。

    ```
    BACKUP_NAME=volume_backup
    BACKUP_DIRECTORY=/backup
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    ```
    {: codeblock}

   这些设置可使用缺省名称 _volume_backup_ 创建一次性备份。

4.  通过 **ibm-backup-restore** 映像运行具有要备份的已安装卷的容器。请包含用于启动备份脚本的命令。

    -   请确保处于与 <em>&lt;backup_env-file&gt;</em> 相同的本地目录中。
    -   卷要安装到的目录必须与环境变量文件中的 BACKUP_DIRECTORY 匹配。
    -   将 <em>&lt;volume_name&gt;</em> 替换为要备份的卷名称。

    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<backup_env-file_name> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    备份运行后，容器会关闭。如果容器未开始运行，请运行 `bx ic logs <container_name>` 来查看日志中的错误消息。

5.  在 {{site.data.keyword.Bluemix_notm}} GUI 中复查 {{site.data.keyword.objectstorageshort}} 中的备份。
    1.  单击您为备份所创建的 {{site.data.keyword.objectstorageshort}} 实例。
    2.  单击 {{site.data.keyword.objectstorageshort}} 容器。在本示例中，容器名称为 volume_backup。
    3.  复查压缩文件。![{{site.data.keyword.Bluemix_notm}} GUI 中的 Object Storage 容器显示备份的文件。](images/volume_backup_screenshot.png)您可以下载 difftar.gz 文件，解压缩该文件，然后验证备份数据。**重要信息**：如果您从 {{site.data.keyword.objectstorageshort}} 删除或修改任何文件，那么无法恢复那些文件。

### 将数据复原到 Kubernetes 集群
{: #migrate_restore}

将备份从 {{site.data.keyword.objectstorageshort}} 复原到 Kubernetes 集群中的持久性卷申领。

开始之前：

- [设定 CLI 的目标](../../../containers/cs_cli_install.html#cs_cli_configure) 为集群。


1. 创建名为 _restore-pvc.yaml_ 的配置文件。此配置文件会创建持久性卷申领 (pvc)，可将此 pvc 作为卷安装到备份 pod。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: restorepvc
      annotations:
        volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
    ```
    {: codeblock}

2. 创建 pvc。

    ```
    kubectl apply -f restore-pvc.yaml
    ```
    {: pre}

3.  创建名为 _restore.yaml_ 的配置文件。对于空白的环境变量，请从之前记下的 {{site.data.keyword.objectstorageshort}} 凭证输入值。包含在凭证中使用的引号。BACKUP_NAME 必须与 {{site.data.keyword.objectstorageshort}} 中要复原的备份名称匹配。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: <pod_name>
    spec:
      containers:
        - name: <container_name>
          image: registry.<region>.bluemix.net/ibm-backup-restore
          env:
          - name: USERID
            value:
          - name: PASSWORD
            value:
          - name: PROJECTID
            value:
          - name: REGION
            value:
          - name: RESTORE_DIRECTORY
            value: /restore
          - name: BACKUP_NAME
            value: volume_backup
          command: ["/bin/bash", "./vrestore"]
          volumeMounts:
          - mountPath: /restore
            name: restore-volume
      volumes:
      - name: restore-volume 
        persistentVolumeClaim:
          claimName: restorepvc
    ```
    {: codeblock}

4.  创建用于运行复原脚本的 pod。

    ```
    kubectl apply -f restore.yaml
    ```
    {: pre}

5.  确认 pod 已运行。

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    pod 运行复原命令并停止。_CrashLoopBackOff_ 消息表示 Kubernetes 正尝试重新启动 pod。

6.  除去 pod 以阻止 pod 使用更多资源。

    ```
    kubectl delete -f restore.yaml
    ```
    {: pre}

您已成功将数据迁移到 Kubernetes 集群。现在，您可以将任何新 pod 安装到 pvc，以向该 pod 提供对已复原文件的访问权。

**注**：如果备份的容器数据包含非 root 用户，那么您必须添加新容器的非 root 许可权。有关更多信息，请参阅 [添加卷的非 root 用户访问权](../../../containers/container_volumes_ov.html#container_volumes_write)。
