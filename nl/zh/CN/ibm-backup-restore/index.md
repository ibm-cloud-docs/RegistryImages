---

copyright:
  years: 2017
lastupdated: "2017-10-30"

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

**ibm-backup-restore** 映像包含在 {{site.data.keyword.containerlong}} 中备份和复原容器卷所需的预安装软件包。
{:shortdesc}

## 工作原理 
{: #how_it_works}

使用 **ibm-backup-restore** 映像，您可以为任何容器卷创建一次性备份或所安排的备份。
备份存储在 {{site.data.keyword.objectstoragefull}} 实例内。您可以将 {{site.data.keyword.objectstorageshort}} 凭证作为环境变量或者通过编辑正在运行的容器中的 `config.conf` 文件，传递给 **ibm-backup-restore** 容器。您还可以将已保存的数据从 {{site.data.keyword.objectstorageshort}} 实例复原到卷。
由于映像包含用于运行备份和复原的脚本，因此用户必须发出命令，以在正在运行的容器中启动相应的脚本。


## 所含内容 
{: #whats_included}

每个 **ibm-backup-restore** 映像都包含以下软件包：

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   python、gnupg 和 wget 软件包

## 入门 
{: #how_to_get_started}

完成以下步骤以执行备份和复原操作：
1.  [创建 {{site.data.keyword.objectstorageshort}} 服务实例](#object_storage)
2.  [运行所安排的备份](#scheduled_backup)
3.  [运行复原脚本](#restore_script_cli)
4.  [加密备份](#encrypting_backups)
5.  [环境变量参考](#reference_backup_restore)

## 创建 {{site.data.keyword.objectstorageshort}} 服务实例 
{: #object_storage}

创建 {{site.data.keyword.objectstorageshort}} 实例，以充当卷备份的存储库。


1.  从 {{site.data.keyword.Bluemix_notm}} 目录的**服务**部分供应 {{site.data.keyword.objectstorageshort}} 实例。
2.  选择 {{site.data.keyword.objectstorageshort}} 实例。
3.  单击**服务凭证**选项卡。
4.  单击**新建凭证**。
5.  完成名称字段但将其他字段保留为空白。单击**添加**。
6.  新凭证现在会在**服务凭证**表中列出。
单击**查看凭证**。
7.  记下**项目标识**、**区域**、**用户标识**和**密码**。这些凭证允许您的 **ibm-backup-restore** 容器访问此 {{site.data.keyword.objectstorageshort}} 实例。

复查 [{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) 文档，以获取有关配置实例的更多信息，并复查[套餐选项](../../ObjectStorage/objectstorage_faq.html#account-payment)，以获取有关 {{site.data.keyword.objectstorageshort}} 定价的更多信息。

## 运行所安排的备份 
{: #scheduled_backup}

通过 **ibm-backup-restore** 映像创建容器，并启动定期安排的备份。

1.  登录到 {{site.data.keyword.containerlong_notm}} CLI。

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

3.  编辑环境变量文件并添加以下字段。对于空白的环境变量，请从之前记下的 {{site.data.keyword.objectstorageshort}} 凭证输入值。
不要包含在凭证中使用的引号。


    ```
    BACKUP_NAME=volume_name
    BACKUP_DIRECTORY=/backup
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    SCHEDULE_TYPE=periodic
    SCHEDULE_INFO=daily
    BACKUP_TYPE=incremental
    ```
    {: codeblock}

    这些设置可使用缺省名称 _volume_backup_ 创建每日增量备份。要使用不同的设置创建备份，请复查[环境变量选项](#reference_backup_restore)的完整列表。

4.  通过 **ibm-backup-restore** 映像运行具有要备份的已安装卷的容器。请包含用于启动备份脚本的命令。

    -   请确保处于与 <em>&lt;backup_env-file&gt;</em> 相同的本地目录中。
    -   卷要安装到的目录必须与环境变量文件中的 BACKUP_DIRECTORY 匹配。

    -   将卷名称替换为要备份的卷名称。
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<backup_env-file_name> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    如果容器未开始运行，请运行 `bx ic logs <container_name>` 来查看日志中的错误消息。


5.  在 {{site.data.keyword.Bluemix_notm}} GUI 中复查 {{site.data.keyword.objectstorageshort}} 中的备份。
    1.  单击您为备份所创建的 {{site.data.keyword.objectstorageshort}} 实例。

    2.  单击 {{site.data.keyword.objectstorageshort}} 容器。
在本示例中，容器名称为 volume_backup。
    3.  查看压缩文件。![{{site.data.keyword.Bluemix_notm}} GUI 中的 {{site.data.keyword.objectstorageshort}} 容器显示备份的文件。](images/volume_backup_screenshot.png) 您可以下载 `vol1.difftar.gz` 文件，解压缩该文件，然后验证备份数据。请注意，如果您从 {{site.data.keyword.objectstorageshort}} 删除或修改任何文件，那么无法恢复那些文件。


您的备份可用。如果您已配置 *<container_name>* 创建一次性完整备份，那么您必须在每次想要创建新备份时运行备份脚本。
如果您已配置容器定期运行增量备份，那么您的备份会按所安排的时间运行。


## 运行复原脚本 
{: #restore_script_cli}

将备份从 {{site.data.keyword.objectstorageshort}} 复原到现有或新容器卷。


1.  在本地目录中为复原容器创建环境变量文件。

    ```
    touch <restore_env-file_name>
    ```
    {: pre}

2.  编辑环境变量文件并添加以下字段。对于空白的环境变量，请从之前记下的 {{site.data.keyword.objectstorageshort}} 凭证输入值。
BACKUP_NAME 必须与 {{site.data.keyword.objectstorageshort}} 中要复原的备份名称匹配。


    ```
    BACKUP_NAME=<volume_name>
    RESTORE_DIRECTORY=/restore
    USERID=
    REGION=
    PROJECTID=
    PASSWORD=
    ```
    {: codeblock}

3.  创建要将备份文件复原到的卷。

    ```
    bx ic volume create <volume_name>
    ```
    {: pre}

4.  通过 **ibm-backup-restore** 映像运行容器。请包含用于启动复原脚本的命令。

    -   请确保处于与 <em>&lt;restore_env-file&gt;</em> 相同的本地目录中。
    -   卷要安装到的目录必须与环境变量文件中的 BACKUP_DIRECTORY 匹配。

    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/restore --env-file ./<restore_env-file_name> registry.ng.buemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vrestore"
    ```
    {: pre}

5.  检查容器日志，以确认复原过程是否已完成。

    ```
    bx ic logs <container_name>
    ```
    {: pre}

    ```
    [2016-10-26 18:01:51,031] [utilities : 152] [INFO] *****************Start logging to ./Restore.log with rollover at 102400 bytes**************
    [2016-10-26 18:01:51,031] [restore : 28] [INFO] Starting the restore process.
    [2016-10-26 18:01:51,032] [configureOS : 22] [INFO] Configuring duplicity with IBM Bluemix ObjectStorage.
    [2016-10-26 18:01:51,032] [configureOS : 13] [INFO] Configuring swift client.
    [2016-10-26 18:01:51,032] [restore : 40] [INFO] Configuration is completed.
    [2016-10-26 18:01:54,022] [restore : 70] [INFO] Restoring the backup that is named 'volume_backup' is completed. Synchronizing remote metadata to local cache...
    Copying duplicity-full-signatures.20161026T173856Z.sigtar.gz to local cache.
    Copying duplicity-full.20161026T173856Z.manifest to local cache.
    Last full backup date: Wed Oct 26 17:38:56 2016
    ```
    {: screen}

    复原过程完成后，容器会关闭。

6.  可选：除去容器，以便它不会消耗任何资源。

    ```
    bx ic rm -f <container_name>
    ```
    {: pre}

7. 可选：要确认备份的文件是否位于卷中，请登录到容器，浏览到安装路径，然后列出文件。

    ```
    $ bx ic exec -it <container_name> bash
    root@instance:/backup_restore# cd ..
    root@instance/# cd restore
    root@instance:/restore# ls
    duplicity-I3_pJv-tempdir  new_file
    root@instance:/restore# 
    ```
    {: screen}

    tempdir 是备份过程的产物，可以手动删除。


您已成功将备份复原到卷 *volume_name*。
现在，您可以将任何新容器安装到卷，以向该容器提供对已复原文件的访问权。
如果备份的容器数据包含非 root 用户，那么您必须向新容器添加非 root 许可权。
有关更多信息，请参阅[添加卷的非 root 访问权](../../../containers/container_volumes_ov.html#container_volumes_write)。


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

6.  编辑环境变量文件并添加以下字段。对于空白的环境变量，请从之前记下的 {{site.data.keyword.objectstorageshort}} 凭证输入值。
不要包含在凭证中使用的引号。
对于 *ENCRYPTION_PASSPHRASE*，请包含口令，以用密码保护备份。此口令与创建加密密钥时生成的口令不同。
当您备份数据和复原数据时，必须包含此口令。


    ```
    BACKUP_NAME=<volume_name>
    BACKUP_DIRECTORY=/backup
    # PROJECTID, REGION, USERID, PASSWORD come from the Object Storage credentials.
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    SCHEDULE_TYPE=periodic
    SCHEDULE_INFO=daily
    BACKUP_TYPE=incremental
    ENCRYPTION_REQUIRED=yes
    # Include this passphrase when backing up and restoring encrypted data.
    ENCRYPTION_PASSPHRASE=
    ```
    {: codeblock}

    这些设置可创建使用名称 *<volume_name>* 加密的每日增量备份。要使用不同的设置创建备份，请复查[环境变量选项](#reference_backup_restore)的完整列表。

7.  登录到 {{site.data.keyword.containerlong_notm}} CLI。

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

8.  通过 **ibm-backup-restore** 映像运行具有要备份的已安装卷的容器。请包含用于启动备份脚本的命令。

    -   请确保处于与 <em>&lt;encryption_env-file_name&gt;</em> 相同的本地目录中。
    -   卷要安装到的目录必须与环境变量文件中的 BACKUP_DIRECTORY 匹配。

    -   将卷名称替换为要备份的卷名称。
   
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<encrypted_env-file_name> registry.ng.bluemix.net/ibm-backup-restore
    ```
    {: pre}

    如果容器未开始运行，请运行 `bx ic logs <container_name>` 来查看日志中的错误消息。


9.  将加密密钥复制到容器的 /backup_restore 目录，该容器是基于 **ibm-backup-restore** 映像构建的。

    ```
    bx ic cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    在本地保留加密密钥的副本，以便解密数据。

10. 登录到容器。

    ```
    bx ic exec -it <container_name> bash
    ```
    {: pre}

11. 确认 *encryption.asc* 已复制到 backup_restore 文件夹。

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

13. 要确认已加密备份，请复查 {{site.data.keyword.objectstorageshort}} 实例中的文件。现在，这些文件的文件名末尾会附加 `.gpg`。![{{site.data.keyword.objectstorageshort}} GUI 会显示附加 .gpg 的所有已备份文件，其中显示它们已加密。](images/volume_backup_encrypt_screenshot.png) 

您的备份已加密。要复原文件，请遵循[运行复原脚本](#restore_script_cli)中的步骤，并在运行复原过程的容器上的 backup_restore 目录中包含 encryption.asc 文件。
如果备份已加密，那么您必须在创建复原容器时提供 ENCRYPTION_REQUIRED 和 ENCRYPTION_PASSPHRASE 作为环境变量。


## 环境变量参考 
{: #reference_backup_restore}

复查可作为环境变量传递或可在运行中容器的 `config.conf` 文件中编辑的字段的完整列表。作为环境变量传递的任何值会取代 `config.conf` 文件中的值。要复查容器的环境变量，请使用 `exec` 并运行 `env` 登录到容器。

|密钥|值选项|
|---|-------------|
|PROJECTID|来自 {{site.data.keyword.objectstorageshort}} 的项目标识|
|REGION|来自 {{site.data.keyword.objectstorageshort}} 的区域|
|USERID|来自 {{site.data.keyword.objectstorageshort}} 的用户标识|
|PASSWORD|来自 {{site.data.keyword.objectstorageshort}} 的密码|
{: caption="表 1. {{site.data.keyword.objectstorageshort}} 变量" caption-side="top"}

从 {{site.data.keyword.objectstorageshort}} 凭证检索这些变量。在运行备份或复原操作的每个容器中包含这些环境变量。


|密钥|值选项|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*：缺省值。卷要安装到的目录的绝对文件路径。数据从此目录进行备份。
请勿选择目录 backup_restore，因为该目录包含用于备份和复原过程的文件。
|
|BACKUP_NAME|*volume_backup*：缺省值。选择备份名称。|
|BACKUP_TYPE|*full*：缺省值。每次备份所有文件。<br/> *incremental*：仅备份新的或更改的文件。如果选择 *incremental*，那么必须选择 SCHEDULING_INFO 和 SCHEDULING_TYPE 的值。|
|SCHEDULE_TYPE|*none*：缺省值。创建一次性备份。<br/> *periodic*：将值更改为 periodic，以创建所安排的备份。|
|SCHEDULE_INFO|*hourly*：创建每小时备份。<br/>*daily*：缺省值。创建每日备份。<br/>*weekly*：创建每周备份。
如果您安排 periodic 更新，那么您必须包括此变量。|
|EXCLUDE_DIRECTORIES|*none*：缺省值。包括您要从备份排除的目录的绝对文件路径。以逗号分隔目录。
|
{: caption="表 2. 备份变量" caption-side="top"}

|密钥|值选项|
|---|-------------|
|BACKUP_NAME|*volume_backup*：缺省值。包括从 {{site.data.keyword.objectstorageshort}} 复原的备份的名称。|
|RESTORE_DIRECTORY|*/backup*：缺省值。卷要安装到的绝对目录。数据复原到此目录。
请勿选择目录 backup_restore，因为该目录包含用于备份和复原过程的文件。
|
{: caption="表 3. 复原变量" caption-side="top"}

|密钥|值选项|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*：缺省值。如果您更改加密密钥的文件名或密钥位于 backup_restore 之外的目录中，请包含此环境变量。|
|ENCRYPTION_REQUIRED|*no*：缺省值。<br/> *yes*：如果您未加密备份，请勿包含任何加密环境变量。
如果您在加密备份，请包括此密钥并将值设置为 yes。|
|ENCRYPTION_PASSPHRASE|包含用于保护备份的口令。此口令与创建加密密钥时生成的口令不同。
当您备份数据和复原数据时，必须包含此口令。
|
|IS_KEY_GENERATED_ON_SYSTEM|*no*：缺省值。<br/> *yes*：如果您已在容器上直接生成加密密钥，请包含此环境变量并将值设置为 yes。
大部分用户在其本地计算机上生成密钥，并将密钥复制到容器，并且可保留缺省值为 no。|
{: caption="表 4. 加密变量" caption-side="top"}

