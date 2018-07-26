---

copyright:
  years: 2017, 2018
lastupdated: "2018-07-26"

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

With the **ibm-backup-restore** image, you can create a one-time or scheduled backup for app data that is stored in a persistent volume (PV) in your cluster or restore app data to a PV. To back up and restore data, you deploy a pod from the **ibm-backup-restore** image. Then, you mount the PVC that binds the PV that you want to backup or the PV that you want to use for restoring your data to your pod. 

**Where does my data go? How do I access it?** </br>
Data that you back up is stored within an {{site.data.keyword.cos_full_notm}} service instance. To access the service, use your {{site.data.keyword.cos_full_notm}} service credentials as environment variables in the **ibm-backup-restore** pod, or edit the `config.conf` file in the running pod.

**Can I restore backed up data to a different app or a different PV?** </br>
Yes, you can restore your saved data from the {{site.data.keyword.cos_full_notm}} service instance to a PV in your cluster. To restore data, you must create a restore pod from the **ibm-backup-restore** image. Then, you mount the PVC that binds the PV that you want to use to your pod.  

## What is included 
{: #whats_included}

Every **ibm-backup-restore** image contains the following software packages:

-   Alpine 3.7
-   Duplicity 0.7.10
-   python, and gpgme packages


## Setting up an {{site.data.keyword.cos_full_notm}} service instance 
{: #object_storage}

Create and configure an {{site.data.keyword.cos_full_notm}} service instance to serve as the repository for the data that you want to back up.
{: shortdesc}

1. Deploy an {{site.data.keyword.cos_full_notm}} service instance.
   1.  Open the [{{site.data.keyword.Bluemix_notm}} catalog](https://console.bluemix.net/catalog/services/cloud-object-storage).
   2.  Enter a name for your service instance, such as `cos-backup`, and select **default** as your resource group. 
   3.  Review the [plan options ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/cloud-computing/bluemix/pricing-object-storage#s3api) for pricing information and select a plan. 
   4.  Click **Create**.
2. Retrieve the {{site.data.keyword.cos_full_notm}} service instance credentials.
   1.  In the navigation on the service details page, click **Service Credentials**.
   2.  Click **New credential**. A dialog box displays. 
   3.  Enter a name for your credentials.
   4.  In **Add Inline Configuration Parameters (Optional)**, enter `{"HMAC":true}` to create additional HMAC credentials that the **ibm-backup-restore** pod uses for HMAC authentication with the {{site.data.keyword.cos_full_notm}} service. 
   5.  Click **Add**. Your new credentials are listed in the **Service Credentials** table.
   6.  Click **View credentials**. 
   7.  Make note of the **access_key_id** and the **secret_access_key** that you can find in the **cos_hmac_keys** section. 
3. Create your first {{site.data.keyword.cos_full_notm}} bucket.
   1. In the navigation on the service details page, click **Buckets**. 
   2. Click **Create bucket**. A dialog box displays.
   3. Enter a unique name for your bucket. The name must be unique within {{site.data.keyword.cos_full_notm}} across all regions and across all {{site.data.keyword.Bluemix_notm}} accounts. 
   4. From the **Resiliency** drop down, select the level of availability that you want for your data. For more information, see [{{site.data.keyword.cos_full_notm}} regions and endpoints](/docs/services/cloud-object-storage/basics/endpoints.html#select-regions-and-endpoints). 
   5. Change the **Location** to the region where you want to store your data. Keep in mind that your data might not be allowed to be stored in every region due to legal reasons.  
   6. Click **Create**. 
4. Retrieve the {{site.data.keyword.cos_full_notm}} host name for your bucket. 
   1. Click on your bucket name that you created in the previous step. 
   2. In the navigation on the service details page, click **Buckets** > **Configuration**. 
   3. Make note of the public URL that you can use to access the data in your bucket. 


Review the [{{site.data.keyword.cos_full_notm}}](/services/cloud-object-storage/about-cos.html#about-ibm-cloud-object-storage) documentation for more information on configuring your service instance.

## Backing up data from a persistent volume
{: #scheduled_backup}

You can create a one-time or scheduled backup for any persistent volume (PV) that is mounted to your app pod through a persistent volume claim (PVC).  
{: shortdesc}

The following example walks you through how to deploy a backup pod from the **ibm-backup-restore** image, mount an existing PV to the backup pod by using a PVC, and back up the data from the PV to your {{site.data.keyword.cos_full_notm}} service instance.  

Before you begin:

-   [Set up an {{site.data.keyword.cos_full_notm}} service instance](#object_storage). 
-   Install the required [CLIs](/docs/containers/cs_cli_install.html#cs_cli_install) to create and work with your cluster.
-   [Create a standard cluster](/docs/containers/cs_clusters.html#clusters_cli) or use an existing one.
-   [Target your CLI to your cluster](/docs/containers/cs_cli_install.html#cs_cli_configure).
-   [Create a persistent volume claim (PVC) and mount it to your app deployment](/docs/containers/cs_storage.html#create).

To back up an existing PV: 

1. Get the name of the PVC that binds the PV that you want to back up. 
   ```
   kubectl get pvc
   ```
   {: pre}

2. Create a backup pod from the **ibm-backup-restore** image. To access the data in the PV, you must mount the PVC that binds the PV to your backup pod. The following example creates a backup pod that runs a daily incremental backup. To create a backup with different settings, review a full list of [environment variable options](#reference_backup_restore).</br>
   **Important:** The **ibm-backup-restore** image must be deployed in a single pod and cannot be used as part of a Kubernetes deployment.
   
   To view the image, target the global registry by running the `ibmcloud cr region-set global` command. Then, run `ibmcloud cr images --include-ibm` to list IBM public images. 
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
   <caption>YAML file components</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Idea icon"/> Understanding the yaml file components</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>The access key ID that you retrieved as part of your {{site.data.keyword.cos_full_notm}} service instance credentials.</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</code></td>
     <td>The secret access key that you retrieved as part of your {{site.data.keyword.cos_full_notm}} service instance credentials.</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>The URL to the regional API endpoint that you use to access {{site.data.keyword.cos_full_notm}} in a specific region. </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>The name of the bucket that you want to use to store your backups in {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>A unique name for the object that holds your backup in the bucket </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>The name of the PVC that binds the PV that you want to back up. </td>
     </tr>
     </tbody>
     </table>
    
3.  Create the backup pod and initiate a backup of your PV data. 

    ```
    kubectl apply -f backuppod.yaml
    ```
    {: pre}

4.  Verify that the pod is running.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}
    
5.  Verify that the backup ran successfully. 
    ```
    kubectl logs backuppod
    ```
    {: pre}

6.  Review the backup in {{site.data.keyword.cos_full_notm}} in the {{site.data.keyword.Bluemix_notm}} GUI.
    1.  From the {{site.data.keyword.Bluemix_notm}} dashboard, find the {{site.data.keyword.cos_full_notm}} service instance. 
    2.  From the navigation, select **Buckets** and click on the bucket that you used in your backup configuration. Your backup is displayed as an object in your bucket. 
    3.  Review the compressed files. You can download the `vol1.difftar.gz` file, extract the file, and verify the backed-up data. </br> **Important**: If you delete or modify any files from {{site.data.keyword.cos_full_notm}}, those files cannot be recovered.

Your backup is available. If you configured your backup to create a one-time full backup, you must run the backup script each time that you want to create a new backup. If you configured the container to run an incremental backup periodically, then your backup runs as scheduled.

## Restoring data from {{site.data.keyword.cos_full_notm}} to your cluster
{: #restore_script_cli}

You can restore data from your {{site.data.keyword.cos_full_notm}} service instance to a PV in your cluster. 

Before you begin:

-   [Target your CLI to your cluster](/docs/containers/cs_cli_install.html#cs_cli_configure).
-   [Create a backup for a PV in your cluster](#scheduled_backup).

To restore data from {{site.data.keyword.cos_full_notm}} to a PV: 

1. Get the name of the PVC that binds the PV where you want to restore your data. 
   ```
   kubectl get pvc
   ```
   {: pre}

2. Create a restore pod from the **ibm-backup-restore** image. To restore data to a PV, you must mount the PVC that binds the PV to your restore pod. 
   
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
   <caption>YAML file components</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Idea icon"/> Understanding the yaml file components</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>The access key ID that you retrieved as part of your {{site.data.keyword.cos_full_notm}} service instance credentials.</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</code></td>
     <td>The secret access key that you retrieved as part of your {{site.data.keyword.cos_full_notm}} service instance credentials.</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>The URL to the regional API endpoint that you use to access {{site.data.keyword.cos_full_notm}} in a specific region. </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>The name of the bucket that you used to store your backups in {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>The unique name for the object that holds your backup in the bucket. You must use the name that you used in the backup pod to store your data in {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>The name of the PVC that binds the PV where you want to restore your data. </td>
     </tr>
     </tbody>
     </table>

3.  Create the restore pod and start restoring your data. 
    ```
    kubectl apply -f restorepod.yaml
    ```
    {: pre}
    
4.  Verify that the pod is running.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    restorepod        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    The pod runs the restore command and stops. The _CrashLoopBackOff_ message means Kubernetes is attempting to restart the pod.

5.  Remove the pod to prevent the pod from consuming more resources.

    ```
    kubectl delete -f restorepod.yaml
    ```
    {: pre}

6.  Verify that your data is successfully restored. 
    ```
    kubectl logs restorepod
    ```
    {: pre}

You successfully restored your backup. You can now mount the PVC that binds the PV to any other pod in your cluster to access the restored files. If the container data that was backed up included a non-root user, you must add non-root permissions to your new container. For more information, see [Adding non-root user access to volumes](../../../containers/container_volumes_ov.html#container_volumes_write).

## Encrypting your backups 
{: #encrypting_backups}

Encrypt the data in your {{site.data.keyword.cos_full_notm}} instance.

1.  Download <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="External link icon"></a> to create an encryption key.
2.  Create an encryption key on your local drive. You can accept the default values by pressing ENTER.

    **Important:** Make note of the passphrase that you create. If you lose your passphrase, any information encrypted with your key cannot be decrypted.

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

4.  Export the encryption key with the value from the `sub` key. Name the file `encryption.asc`.

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    In this example, the sub key has the value `YYYYYYYY`.

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  Create an environment variable file for your encrypted backup container in a local directory.

    ```
    touch <encryption_env-file_name>
    ```
    {: pre}

6.  Edit the pod configuration file and add the following fields. For the blank environment variables, enter the values from the {{site.data.keyword.cos_full_notm}} credentials that you noted earlier. Include the quotation marks that are used in the credentials. For **ENCRYPTION_PASSPHRASE**, include a passphrase to password protect the backup. This passphrase is a different passphrase from the phrase that you made when you created the encryption key. You must include this passphrase when you are backing up data and restoring data.

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
    <caption>YAML file components</caption>
    <thead>
    <th colspan=2><img src="../images/idea.png" alt="Idea icon"/> Understanding the yaml file components</th>
    </thead>
     <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>The access key ID that you retrieved as part of your {{site.data.keyword.cos_full_notm}} service instance credentials.</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</em></code></td>
     <td>The secret access key that you retrieved as part of your {{site.data.keyword.cos_full_notm}} service instance credentials.</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>The URL to the regional API endpoint that you use to access {{site.data.keyword.cos_full_notm}} in a specific region. </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>The name of the bucket that you want to use to store your backups in {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>A unique name for the object that holds your backup in the bucket.</td>
     </tr>
     <tr>
     <td><code>&lt;passphrase&gt;</code></td>
     <td>Any string that you want to use for your backup. You must include this passphrase when you restore your data. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>The name of the PVC that binds the PV that you want to back up. </td>
     </tr>
     </tbody>
     </table>
   
    These settings create a daily incremental backup that is encrypted. To create a backup with different settings, review a full list of [environment variable options](#reference_backup_restore).
    
7.  Create the backup pod. 

    ```
    kubectl apply -f backuppod.yaml 
    ```
    {: pre}

8.  Verify that the pod is running.

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

9.  Copy the encryption key into the `/backup_restore` directory of the container that is built from the **ibm-backup-restore** image.

    ```
    kubectl cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    Keep a copy of the encryption key locally in order to decrypt your data.

10. Log in to the pod and navigate to the `backup_restore` folder. 

    ```
    kubecl exec -it <pod_name> bash
    ```
    {: pre}

11. Verify that the `encryption.asc` file is copied to the `backup_restore` folder.

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

13. To confirm that your backup is encrypted, review the files in your {{site.data.keyword.cos_full_notm}} service instance. The files now have `.gpg` appended to the end of the file name.

Your backup is encrypted. To restore the files, follow the steps in [Restoring data from {{site.data.keyword.cos_full_notm}} to a PVC in your cluster](#restore_script_cli) and include the `encryption.asc` file in the `backup_restore` directory of the pod that runs the restore process. If the backup is encrypted, you must provide the **ENCRYPTION_REQUIRED** and **ENCRYPTION_PASSPHRASE** environment variables when you create the restore pod.

## Environment variable reference 
{: #reference_backup_restore}

Review the full list of fields that can be passed as environment variables or edited in the `config.conf` file in a running pod. Any value that is passed as an environment variable supersedes the value in the `config.conf` file. To review the environment variables for a pod, log in to the pod by using the `kubectl exec` command and run `env`.

|Key|Value options|
|---|-------------|
|ACCESS_KEY_ID|The **access_key_id** that is part of the HMAC credentials in {{site.data.keyword.cos_full_notm}}.|
|SECRET_ACCESS_KEY|The **secret_access_key** is part of the HMAC credentials in {{site.data.keyword.cos_full_notm}}.|
|ENDPOINT|The host name to access {{site.data.keyword.cos_full_notm}} bucket data.|
|BUCKET|The name of the bucket in {{site.data.keyword.cos_full_notm}} where your backed-up data is stored. |
{: caption="Table 1. {{site.data.keyword.cos_full_notm}} variables" caption-side="top"}

|Key|Value options|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: Default. Absolute file path of the directory that the volume is mounted to. Data is backed up from this directory. Do not select the directory backup_restore as that directory contains files for the backup and restore processes.|
|BACKUP_NAME|*volume_backup*: Default. Choose a backup name.|
|BACKUP_TYPE|*full*: Default. All files are backed up every time.<br/> *incremental*: Only new or changed files are backed up. If you choose *incremental*, you must choose values for **SCHEDULING_INFO** and **SCHEDULING_TYPE**.|
|SCHEDULE_TYPE|*none*: Default. Create a one-time backup.<br/> **Note:** If you choose to create a one-time backup, your pod is removed from the cluster after the backup is finished. <br/> *periodic*: Change the value to periodic to create scheduled backups.|
|SCHEDULE_INFO|*hourly*: Create an hourly backup.<br/>*daily*: Default. Create a daily backup.<br/>*weekly*: Create a weekly backup. You must include this variable if you schedule a periodic update.|
|EXCLUDE_DIRECTORIES|*none*: Default. Include the absolute file path of directories that you want excluded from the backup. Separate directories with a comma.|
{: caption="Table 2. Backup variables" caption-side="top"}

|Key|Value Options|
|---|-------------|
|BACKUP_NAME|*volume_backup*: Default. Include the name of the backup that is being restored from {{site.data.keyword.cos_full_notm}}.|
|RESTORE_DIRECTORY|*/backup*: Default. Absolute directory that the volume is mounted to. Data is restored to this directory. Do not select the directory `backup_restore` as that directory contains files for the backup and restore processes.|
{: caption="Table 3. Restore variables" caption-side="top"}

|Key|Value options|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: Default. Include this environment variable if you change the file name of the encryption key or the key is located in a directory other than `backup_restore`.|
|ENCRYPTION_REQUIRED|*no*: Default.<br/> *yes*: If you do not encrypt your backup, do not include any encryption environment variables. If you encrypt your backup, include this key with the value `yes`.|
|ENCRYPTION_PASSPHRASE|Include a passphrase to secure a backup. This passphrase is a different passphrase from the phrase that you made when you created the encryption key. You must include this passphrase when you back up data and restore data.|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: Default.<br/> *yes*: Include this environment variable with `yes` if you generated the encryption key directly on the container. Most users generate the key on their local computer and copy the key to the pod and can leave the default as `no`.|
{: caption="Table 4. Encryption variables" caption-side="top"}

