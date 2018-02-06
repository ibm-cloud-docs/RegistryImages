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

# Getting started with the ibm-backup-restore image
{: #ibmbackup_restore_starter}

The **ibm-backup-restore** image contains the preinstalled packages that are needed to back up and restore persistent storage in {{site.data.keyword.containerlong}}.
{:shortdesc}

## How it works 
{: #how_it_works}

With the **ibm-backup-restore** image, you can create a one-time or scheduled backup for any persistent volume claim (pvc). The backup is stored within an {{site.data.keyword.objectstoragefull}} instance. You can pass the {{site.data.keyword.objectstorageshort}} credentials to your **ibm-backup-restore** container as environment variables or by editing the `config.conf` file in the running container. You can also restore your saved data from the {{site.data.keyword.objectstorageshort}} instance to a volume. Because the image contains scripts for running both backups and restores, the user must enter a command to start the appropriate script.

## What is included 
{: #whats_included}

Every **ibm-backup-restore** image contains the following software packages:

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   python, gnupg, and wget packages

## Getting started 
{: #how_to_get_started}

Review the following tasks to back up and restore data:
1.  [Creating an {{site.data.keyword.objectstorageshort}} service instance](#object_storage)
2.  [Running a scheduled backup](#scheduled_backup)
3.  [Running the restore script](#restore_script_cli)
4.  [Encrypting your backups](#encrypting_backups)
5.  [Environment variable reference](#reference_backup_restore)

## Creating an {{site.data.keyword.objectstorageshort}} service instance 
{: #object_storage}

Create an {{site.data.keyword.objectstorageshort}} instance to serve as the repository for your volume backup.

1.  Provision your {{site.data.keyword.objectstorageshort}} instance from the **Storage** section of the {{site.data.keyword.Bluemix_notm}} catalog.
2.  Click {{site.data.keyword.objectstorageshort}}.
3.  Select {{site.data.keyword.objectstorageshort}} OpenStack Swift for {{site.data.keyword.Bluemix_notm}}. Then click create.
3.  Click the **Service Credentials** tab.
4.  Click **New Credential**.
5.  Complete the name field but leave the other fields blank. Click **Add**.
6.  Your new credentials are now listed in the **Service Credentials** table. Click **View Credentials**.
7.  Make note of **projectid**, **region**, **userId**, and **password**. These credentials allow your **ibm-backup-restore** container to access this {{site.data.keyword.objectstorageshort}} instance.

Review [{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) documentation for more information on configuring your instance and review [plan options](../../ObjectStorage/objectstorage_faq.html#account-payment) for information about {{site.data.keyword.objectstorageshort}} pricing.

## Running a scheduled backup 
{: #scheduled_backup}

Create a container pod from the **ibm-backup-restore** image, and start a regularly scheduled backup.

Before you begin:

-   Install the required [CLIs](../../../containers/cs_cli_install.html#cs_cli_install).
-   [Target your CLI](../../../containers/cs_cli_install.html#cs_cli_configure) to your cluster.


1. Create a configuration file that is named _backup-pvc.yaml_. This configuration file creates a persistent volume claim (pvc) that you can mount to your backup pod as a volume.

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
    
2. Create the pvc.

    ```
    kubectl apply -f backup-pvc.yaml 
    ```
    {: pre}

3.  Create a configuration file that is named _backup.yaml_. For the blank environment variables, enter the values from the {{site.data.keyword.objectstorageshort}} credentials that you noted earlier. Include the quotation marks that are used in the credentials. Replace <em>&lt;pod_name&gt;</em>, <em>&lt;container_name&gt;</em>, and the image registry <em>&lt;region&gt;</em> with appropriate values.

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
    
    These settings create a daily incremental backup with the default name _mybackup_. To create a backup with different settings, review a full list of [environment variable options](#reference_backup_restore).

4.  create a pod that runs the backup script.

    ```
    kubectl apply -f backup.yaml
    ```
    {: pre}

5.  Confirm that the pod is running.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

4.  Review the backup in {{site.data.keyword.objectstorageshort}} in the {{site.data.keyword.Bluemix_notm}} GUI.
    1.  Click the {{site.data.keyword.objectstorageshort}} instance that you created for the backup.
    2.  From the **Manage** tab in the **Containers** table, click the {{site.data.keyword.objectstorageshort}} container.
    3.  Review the compressed files.![The Object Storage container in the {{site.data.keyword.Bluemix_notm}} GUI shows the files that are backed up.](images/volume_backup_screenshot.png)You can download the vol1.difftar.gz file, extract the file, and verify the backed-up data. **Important**: that if you delete or modify any files from {{site.data.keyword.objectstorageshort}}, those files cannot be recovered.

Your backup is available. If you configured your backup to create a one-time full backup, you must run the backup script each time that you want to create a new backup. If you configured the container to run an incremental backup periodically, then your backup runs as scheduled.

## Running the restore script 
{: #restore_script_cli}

Restore your backup from {{site.data.keyword.objectstorageshort}} to an existing or new volume.

Before you begin:

-   Install the required [CLIs](../../../containers/cs_cli_install.html#cs_cli_install).
-   [Target your CLI](../../../containers/cs_cli_install.html#cs_cli_configure) to your cluster.


1. Create a configuration file that is named _restore-pvc.yaml_. This configuration file creates a persistent volume claim (pvc) that you can mount to your restore pod as a volume.

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
    
2. Create the pvc.

    ```
    kubectl apply -f restore-pvc.yaml 
    ```
    {: pre}

3.  Create a configuration file that is named _restore.yaml_. For the blank environment variables, enter the values from the {{site.data.keyword.objectstorageshort}} credentials that you noted earlier. Include the quotation marks that are used in the credentials. The BACKUP_NAME must match the name of the backup in {{site.data.keyword.objectstorageshort}} that is being restored.

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

4.  create a pod that runs the restore script.
    
    ```
    kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  Confirm that the pod is running.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    The pod runs the restore command and stops. The _CrashLoopBackOff_ message means Kubernetes is attempting to restart the pod.

6.  Remove the pod to prevent the pod from consuming more resources.

    ```
    kubectl delete -f restore.yaml
    ```
    {: pre}

You successfully restored your backup. You can now mount any new pod to the pvc to give that container access to the restored files. If the container data that was backed up included a non-root user, you must add non-root permissions to your new container. For more information, see [Adding non-root user access to volumes](../../../containers/container_volumes_ov.html#container_volumes_write).

## Encrypting your backups 
{: #encrypting_backups}

Encrypt the data in your {{site.data.keyword.objectstorageshort}} instance.

1.  Download <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="External link icon"></a> to create an encryption key.
2.  Create an encryption key on your local drive. You can accept the default values by pressing ENTER.

    **Attention:** Make note of the passphrase that you create. If you lose your passphrase, any information encrypted with your key cannot be decrypted.

    ```
    gpg --gen-key
    ```
    {: pre}

    Depending on the version of <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="External link icon"></a>, you might need to use `gpg2` instead of `gpg` in your commands.

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
    
6. Create a configuration file that is named _backup-pvc.yaml_. This configuration file creates a persistent volume claim (pvc) that you can mount to your backup pod as a volume.

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

7. Create the pvc.

    ```
    kubectl apply -f backup-pvc.yaml 
    ```
    {: pre}

8.  Edit the deployment configuration file and add the following fields. For the blank environment variables, enter the values from the {{site.data.keyword.objectstorageshort}} credentials that you noted earlier. Include the quotation marks that are used in the credentials. For *ENCRYPTION_PASSPHRASE*, include a passphrase to password protect the backup. This passphrase is a different passphrase from the phrase that you made when you created the encryption key. You must include this passphrase when you are backing up data and restoring data.

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
   
   These settings create a daily incremental backup that is encrypted with the name <em>&lt;volume_name&gt;</em>. To create a backup with different settings, review a full list of [environment variable options](#reference_backup_restore).
    
9.  create a pod that runs the backup script.

    ```
    kubectl apply -f backup.yaml 
    ```
    {: pre}

9.  Confirm that the pod is running.

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

10.  Copy the encryption key into the /backup_restore directory of the container that is built from the **ibm-backup-restore** image.

    ```
    kubectl cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    Keep a copy of the encryption key locally in order to decrypt your data.

11. Log in to the container.

    ```
    kubecl exec -it <container_name> bash
    ```
    {: pre}

12. Confirm that *encryption.asc* copied to the backup_restore folder.

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

13. Run the backup script from the backup_restore folder.

    ```
    ./vbackup &
    ```
    {: codeblock}

13. To confirm that your backup is encrypted, review the files in your {{site.data.keyword.objectstorageshort}} instance. The files now have `.gpg` appended to the end of the file name.![The Object Storage GUI shows all backed-up files appended with .gpg, which shows that they are encrypted.](images/volume_backup_encrypt_screenshot.png)

Your backup is encrypted. To restore the files, follow the steps in [Running the restore script](#restore_script_cli) and include the encryption.asc file in the backup_restore directory on the container that is running the restore process. If the backup is encrypted, you must provide the ENCRYPTION_REQUIRED and ENCRYPTION_PASSPHRASE from the backup yaml file as environment variables when you create the restore container.

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
|SCHEDULE_TYPE|*none*: Default. Create a one-time backup.<br/> **Note:** If you choose to create a one-time backup, your pod is removed from the cluster after the backup is finished. <br/>*periodic*: Change the value to periodic to create scheduled back ups.|
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

## Migrating your volume data from single and scalable containers to Kubernetes
{: #migrate_data}

Create a one-time backup for any container volume. The backup is stored within an {{site.data.keyword.objectstoragefull}} instance. Then, you can migrate the data to a persistent volume claim in Kubernetes.
{:shortdesc}

### Getting started 
{: #how_to_get_started}

Before you begin:

- [Review the full migration path for moving apps to Kubernetes](../../../containers/cs_classic.html)
- [Install single and scalable containers CLI (bx ic)](../../../containers/container_cli_cfic_install.html)
- [Install {{site.data.keyword.containershort}} CLI (bx cs and kubectl)](../../../containers/cs_cli_install.html#cs_cli_install)
- [Create a standard Kubernetes cluster to migrate your data to](../../../containers/cs_cluster.html#cs_cluster_cli)

Complete the following tasks to perform backup and restore operations:
1.  [Creating an {{site.data.keyword.objectstorageshort}} service instance](#object_storage) (as addressed previously)
2.  [Running a one-time backup](#migrate_backup)
3.  [Running the restore script in Kubernetes](#migrate_restore)

### Running a one-time backup 
{: #migrate_backup}

Create a single container from the **ibm-backup-restore** image, and start a backup.

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

3.  Edit the environment variable file and add the following fields. For the blank environment variables, enter the values from the {{site.data.keyword.objectstorageshort}} credentials that you noted earlier. Do **not** include the quotation marks that are used in the credentials.

    ```
    BACKUP_NAME=volume_backup
    BACKUP_DIRECTORY=/backup
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    ```
    {: codeblock}

    These settings create a one-time backup with the default name _volume_backup_.

4.  Run a container from the **ibm-backup-restore** image with a mounted volume to back up. Include the command to start the backup script.

    -   Make sure that you are in the same local directory as the <em>&lt;backup_env-file&gt;</em>.
    -   The directory that the volume is mounted to must match the BACKUP_DIRECTORY in the environment variable file.
    -   Replace <em>&lt;volume_name&gt;</em> with the name of the volume that is being backed up.
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<backup_env-file_name> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    After the backup runs, the container shuts down. If the container does not start running, run `bx ic logs <container_name>` to review the logs for error messages.

5.  Review the backup in {{site.data.keyword.objectstorageshort}} in the {{site.data.keyword.Bluemix_notm}} GUI.
    1.  Click the {{site.data.keyword.objectstorageshort}} instance that you created for the backup.
    2.  Click the {{site.data.keyword.objectstorageshort}} container. In this example, the container name is volume_backup.
    3.  Review the compressed files.![The Object Storage container in the {{site.data.keyword.Bluemix_notm}} GUI shows the files that are backed up.](images/volume_backup_screenshot.png)You can download the difftar.gz file, extract the file, and verify the backed-up data. **Important**: If you delete or modify any files from {{site.data.keyword.objectstorageshort}}, those files cannot be recovered.

### Restoring your data to a Kubernetes cluster 
{: #migrate_restore}

Restore your backup from {{site.data.keyword.objectstorageshort}} to a persistent volume claim in a Kubernetes cluster.

Before you begin:

- [Target your CLI](../../../containers/cs_cli_install.html#cs_cli_configure) to your cluster.


1. Create a configuration file that is named _restore-pvc.yaml_. This configuration file creates a persistent volume claim (pvc) that you can mount to your backup pod as a volume.

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

2. Create the pvc.

    ```
    kubectl apply -f restore-pvc.yaml 
    ```
    {: pre}

3.  Create a configuration file that is named _restore.yaml_. For the blank environment variables, enter the values from the {{site.data.keyword.objectstorageshort}} credentials that you noted earlier. Include the quotation marks that are used in the credentials. The BACKUP_NAME must match the name of the backup in {{site.data.keyword.objectstorageshort}} that is being restored.

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

4.  Create a pod that runs the restore script.

    ```
    kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  Confirm that the pod runs.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}
    
    The pod runs the restore command and stops. The _CrashLoopBackOff_ message means Kubernetes is attempting to restart the pod.

6.  Remove the pod to prevent the pod from consuming more resources.

    ```
    kubectl delete -f restore.yaml
    ```
    {: pre}

You successfully migrated your data to your Kubernetes cluster. You can now mount any new pod to the pvc to give that pod access to the restored files. 

**Note**: If the container data that was backed up included a non-root user, you must add non-root permissions to your new container. For more information, see [Adding non-root user access to volumes](../../../containers/container_volumes_ov.html#container_volumes_write).
