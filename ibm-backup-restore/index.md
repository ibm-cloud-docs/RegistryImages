---

copyright:
  years: 2017
lastupdated: "2017-06-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Getting started with the ibm-backup-restore image for {{site.data.keyword.Bluemix_notm}} 
{: #ibmbackup_restore_starter}

The **ibm-backup-restore** image contains the preinstalled packages that are needed to back up and restore container volumes in {{site.data.keyword.containerlong}}.
{:shortdesc}

## How it works 
{: #how_it_works}

With the **ibm-backup-restore** image, you can create a one-time or scheduled backup for any container volume. The backup is stored within an {{site.data.keyword.objectstoragefull}} instance. You can pass the {{site.data.keyword.objectstorageshort}} credentials to your **ibm-backup-restore** container as environment variables or by editing the `config.conf` file in the running container. You can also restore your saved data from the {{site.data.keyword.objectstorageshort}} instance to a volume. Because the image contains scripts for running both backups and restores, the user must issue a command to start the appropriate script in a running container.

## What is included 
{: #whats_included}

Every **ibm-backup-restore** image contains the following software packages:

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   python, gnupg, and wget packages

## Getting started 
{: #how_to_get_started}

Complete the following steps to perform backup and restore operations:
1.  [Creating an {{site.data.keyword.objectstorageshort}} service instance](#object_storage)
2.  [Running a scheduled backup](#scheduled_backup)
3.  [Running the restore script](#backup_script_cli)
4.  [Encrypting your backups](#encrypting_backups)
5.  [Environment variable reference](#reference_backup_restore)

## Creating an {{site.data.keyword.objectstoragefull}} service instance 
{: #object_storage}

Create an {{site.data.keyword.objectstorageshort}} instance to serve as the repository for your volume backup.

1.  Provision your {{site.data.keyword.objectstorageshort}} instance from the **Services** section of the {{site.data.keyword.Bluemix_notm}}  catalog.
2.  Select the {{site.data.keyword.objectstorageshort}} instance.
3.  Click the **Service Credentials** tab.
4.  Click **New Credential**.
5.  Complete the name field but leave the other fields blank. Click **Add**.
6.  Your new credentials are now listed in the **Service Credentials** table. Click **View Credentials**.
7.  Make note of **projectid**, **region**, **userId**, and **password**. These credentials allow your **ibm-backup-restore** container to access this {{site.data.keyword.objectstorageshort}} instance.

Review [{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) documentation for more information on configuring your instance and review [plan options](../../ObjectStorage/objectstorage_faq.html#account-payment) for information about {{site.data.keyword.objectstorageshort}} pricing.

## Running a scheduled backup 
{: #scheduled_backup}

Create a container from the **ibm-backup-restore** image, and start a regularly scheduled backup.

1.  Log in to the {{site.data.keyword.containershort}} CLI.

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

2.  Create an environment variable file in a local directory.

    ```
    touch <backup_env-file_name>
    ```
    {: pre}

3.  Edit the environment variable file and add the following fields. For the blank environment variables, enter the values from the {{site.data.keyword.objectstorageshort}} credentials that you noted earlier. Don't include the quotation marks that are used in the credentials.

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

    These settings create a daily incremental backup with the default name _volume_backup_. To create a backup with different settings, review a full list of [environment variable options](#reference_backup_restore).

4.  Run a container from the **ibm-backup-restore** image with a mounted volume to back up. Include the command to start the backup script.

    -   Make sure that you are in the same local directory as the <em>&lt;backup_env-file&gt;</em>.
    -   The directory that the volume is mounted to must match the BACKUP_DIRECTORY in the environment variable file.
    -   Replace the volume name with the name of the volume that is being backed up.
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<backup_env-file_name> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    If the container does not start running, run `bx ic logs <container_name>` to review the logs for error messages.

5.  Review the backup in {{site.data.keyword.objectstorageshort}} in the {{site.data.keyword.Bluemix_notm}}  GUI.
    1.  Click the {{site.data.keyword.objectstorageshort}} instance that you created for the backup.
    2.  Click the {{site.data.keyword.objectstorageshort}} container. In this example, the container name is volume_backup.
    3.  Review the compressed files.![The Object Storage container in the Bluemix GUI shows the files that are backed up.](images/volume_backup_screenshot.png)You can download the vol1.difftar.gz file, extract the file, and verify the backed-up data. Note that if you delete or modify any files from {{site.data.keyword.objectstorageshort}}, those files cannot be recovered.

Your backup is available. If you configured *<container_name>* to create a one-time full backup, you must run the backup script each time that you want to create a new backup. If you configured the container to run an incremental backup periodically, then your backup runs as scheduled.

## Running the restore script 
{: #restore_script_cli}

Restore your backup from {{site.data.keyword.objectstorageshort}} to an existing or new container volume.

1.  Create an environment variable file for your restore container in a local directory.

    ```
    touch <restore_env-file_name>
    ```
    {: pre}

2.  Edit the environment variable file and add the following fields. For the blank environment variables, enter the values from the {{site.data.keyword.objectstorageshort}} credentials that you noted earlier. The BACKUP_NAME must match the name of the backup in {{site.data.keyword.objectstorageshort}} that is being restored.

    ```
    BACKUP_NAME=<volume_name>
    RESTORE_DIRECTORY=/restore
    USERID=
    REGION=
    PROJECTID=
    PASSWORD=
    ```
    {: codeblock}

3.  Create a volume to restore the backed-up files to.

    ```
    bx ic volume create <volume_name>
    ```
    {: pre}

4.  Run a container from the **ibm-backup-restore** image. Include the command to start the restore script.

    -   Make sure that you are in the same local directory as the <em>&lt;restore_env-file&rt;</em>.
    -   The directory that the volume is mounted to must match the BACKUP_DIRECTORY in the environment variable file.
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/restore --env-file ./<restore_env-file_name> registry.ng.buemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vrestore"
    ```
    {: pre}

5.  Check the container logs to confirm that the restore process completed.

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

    When the restore process completes, the container shuts down.

6.  Optional: Remove the container so that it doesn't consume any resources.

    ```
    bx ic rm -f <container_name>
    ```
    {: pre}

7. Optional: To confirm that your backed-up files are located in the volume, log in to the container, navigate to the mount path, and list the files.

    ```
    $ bx ic exec -it <container_name> bash
    root@instance:/backup_restore# cd ..
    root@instance/# cd restore
    root@instance:/restore# ls
    duplicity-I3_pJv-tempdir  new_file
    root@instance:/restore# 
    ```
    {: screen}

    The tempdir is a product of the backup process and can be manually deleted.


You successfully restored your backup to the volume *volume_name*. You can now mount any new container to the volume to give that container access to the restored files. If the container data that was backed up included a non-root user, you must add non-root permissions to your new container. See [Adding non-root user access to volumes](../../../containers/container_volumes_ov.html#container_volumes_write) for more information.

## Encrypting your backups 
{: #encrypting_backups}

Encrypt the data in your {{site.data.keyword.objectstorageshort}} instance.

1.  Download <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="External link icon"></a> to create an encryption key.
2.  Create an encryption key on your local drive. You can accept the default values by pressing **ENTER**.

    **Warning:** Make note of the passphrase that you create. If you lose your passphrase, any information encrypted with your key cannot be decrypted.

    ```
    gpg --gen-key
    ```
    {: pre}

    Depending on the version of <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="External link icon"></a> , you might need to use `gpg2` instead of `gpg` in your commands.

3.  Verify the key.

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

4.  Export the encryption key with the value from the `sub` key. Name the file encryption.asc.

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    In this example, the sub key has the value *YYYYYYYY*

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  Create an environment variable file for your encrypted backup container in a local directory.

    ```
    touch <encryption_env-file_name>
    ```
    {: pre}

6.  Edit the environment variable file and add the following fields. For the blank environment variables, enter the values from the {{site.data.keyword.objectstorageshort}} credentials that you noted earlier. Don't include the quotation marks that are used in the credentials. For *ENCRYPTION_PASSPHRASE*, include a passphrase to password protect the backup. This passphrase is a different passphrase from the phrase that you made when you created the encryption key. You must include this passphrase when you are backing up data and restoring data.

    ```
    BACKUP_NAME=<volume_name>
    BACKUP_DIRECTORY=/backup
    # PROJECTID, REGION, USERID, PASSWORD come from the {{site.data.keyword.objectstorageshort}} credentials.
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

    These settings create a daily incremental backup that is encrypted with the name *<volume_name>*. To create a backup with different settings, review a full list of [environment variable options](#reference_backup_restore).

7.  Log in to the {{site.data.keyword.containershort}} CLI.

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

8.  Run a container from the **ibm-backup-restore** image with a mounted volume to back up. Include the command to start the backup script.

    -   Make sure that you are in the same local directory as the <em>&lt;encryption_env-file_name&gt;</em>.
    -   The directory that the volume is mounted to must match the BACKUP_DIRECTORY in the environment variable file.
    -   Replace the volume name with the name of the volume that is being backed up.
   
   ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<encrypted_env-file_name> registry.ng.bluemix.net/ibm-backup-restore
    ```
    {: pre}

    If the container does not start running, run `bx ic logs <container_name>` to review the logs for error messages.

9.  Copy the encryption key into the /backup_restore directory of the container that is built from the **ibm-backup-restore** image.

    ```
    bx ic cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    Keep a copy of the encryption key locally in order to decrypt your data.

10. Log in to the container.

    ```
    bx ic exec -it <container_name> bash
    ```
    {: pre}

11. Confirm that *encryption.asc* copied to the backup_restore folder.

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

12. Run the backup script from the backup_restore folder.

    ```
    ./vbackup &
    ```
    {: codeblock}

13. To confirm that your backup is encrypted, review the files in your {{site.data.keyword.objectstorageshort}} instance. The files now have `.gpg` appended to the end of the file name.![The Object Storage GUI shows all backed-up files appended with .gpg, which shows that they are encrypted.](images/volume_backup_encrypt_screenshot.png)

Your backup is encrypted. To restore the files, follow the steps in [Running the restore script](#backup_script) and include the encryption.asc file in the backup_restore directory on the container that is running the restore process. If the backup is encrypted, you must provide the ENCRYPTION_REQUIRED and ENCRYPTION_PASSPHRASE as environment variables when you create the restore container.

## Environment variable reference 
{: #reference_backup_restore}

Review the full list of fields that can be passed as environment variables or edited in the `config.conf` file in a running container. Any value that is passed as an environment variable supersedes the value in the `config.conf` file. To review the environment variables for a container, log in to the container by using `exec` and run `env`.

|Key|Value options|
|---|-------------|
|PROJECTID|ProjectID from {{site.data.keyword.objectstorageshort}}|
|REGION|Region from {{site.data.keyword.objectstorageshort}}|
|USERID|Userid from {{site.data.keyword.objectstorageshort}}|
|PASSWORD|Password from {{site.data.keyword.objectstorageshort}}|
{: caption="Table 1. {{site.data.keyword.objectstorageshort}} variables" caption-side="top"}

Retrieve these variables from the {{site.data.keyword.objectstorageshort}} credentials. Includes these environment variables with every container that is running a backup or restore operation.

|Key|Value options|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: Default. Absolute file path of the directory that the volume is mounted to. Data is backed up from this directory. Do not select the directory backup_restore as that directory contains files for the backup and restore processes.|
|BACKUP_NAME|*volume_backup*: Default. Choose a backup name.|
|BACKUP_TYPE|*full*: Default. All files are backed up every time.<br/> *incremental*: Only new or changed files are backed up. If you choose *incremental*, you must choose values for SCHEDULING_INFO and SCHEDULING_TYPE.|
|SCHEDULE_TYPE|*none*: Default. Create a one-time backup.<br/> *periodic*: Change the value to periodic to create scheduled back ups.|
|SCHEDULE_INFO|*hourly*: Create an hourly backup.<br/>*daily*: Default. Create a daily backup.<br/>*weekly*: Create a weekly backup. You must include this variable if you are scheduling a periodic update.|
|EXCLUDE_DIRECTORIES|*none*: Default. Include the absolute file path of directories that you want excluded from the backup. Separate directories with a comma.|
{: caption="Table 2. Backup variables" caption-side="top"}

|Key|Value Options|
|---|-------------|
|BACKUP_NAME|*volume_backup*: Default. Include the name of the backup that is being restored from {{site.data.keyword.objectstorageshort}}.|
|RESTORE_DIRECTORY|*/backup*: Default. Absolute directory that the volume is mounted to. Data is restored to this directory. Do not select the directory backup_restore as that directory contains files for the backup and restore processes.|
{: caption="Table 3. Restore variables" caption-side="top"}

|Key|Value options|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: Default. Include this environment variable if you change the file name of the encryption key or the key is located in a directory other than backup_restore.|
|ENCRYPTION_REQUIRED|*no*: Default.<br/> *yes*: If you are not encrypting your backup, do not include any encryption environment variables. If you are encrypting your backup, include this key with the value yes.|
|ENCRYPTION_PASSPHRASE|Include a passphrase to secure a backup. This passphrase is a different passphrase from the phrase that you made when you created the encryption key. You must include this passphrase when you are backing up data and restoring data.|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: Default.<br/> *yes*: Include this environment variable with yes if you generated the encryption key directly on the container. Most users generate the key on their local computer and copy the key to the container and can leave the default as no.|
{: caption="Table 4. Encryption variables" caption-side="top"}

