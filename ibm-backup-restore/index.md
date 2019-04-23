---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-23"

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

# Getting started with the `ibmcloud-backup-restore` image
{: #ibmbackup_restore_starter}

The `ibmcloud-backup-restore` image contains the preinstalled packages that are needed to back up and restore persistent storage in {{site.data.keyword.containerlong}}.
{:shortdesc}

The `ibmcloud-backup-restore` image is available only in the `icr.io` registry domains, and is deprecated in the `registry.bluemix.net` domains. To access the image and deploy containers from it, your cluster must store an API key in an image pull secret to authorize access to {{site.data.keyword.registryshort_notm}}. For clusters that were created before 25 February 2019, you must update your cluster to have access to the `icr.io` registry domains so that you can use the `ibmcloud-backup-restore` image. For more information about what changed and how to update your cluster, see [Understanding how to authorize your cluster to pull images from a registry](/docs/containers?topic=containers-images#cluster_registry_auth) and [Updating existing clusters to use the API key image pull secret](/docs/containers?topic=containers-images#imagePullSecret_migrate_api_key). 
{: important}

You can access the images that are provided by {{site.data.keyword.IBM_notm}} by using the command line, see [IBM public images](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## How it works
{: #backup_restore_how_it_works}

With the `ibmcloud-backup-restore` image, you can create a one-time or scheduled backup for app data that is stored in a persistent volume (PV) in your cluster or restore app data to a PV. To back up and restore data, you deploy a pod from the `ibmcloud-backup-restore` image. Then, you mount the PVC that binds the PV that you want to backup or the PV that you want to use for restoring your data to your pod.

**Where does my data go? How do I access it?**

Data that you back up is stored within an {{site.data.keyword.cos_full_notm}} service instance. To access the service, use your {{site.data.keyword.cos_full_notm}} service credentials as environment variables in the `ibmcloud-backup-restore` pod, or edit the `config.conf` file in the running pod.

**Can I restore backed up data to a different app or a different PV?**

Yes, you can restore your saved data from the {{site.data.keyword.cos_full_notm}} service instance to a PV in your cluster. To restore data, you must create a restore pod from the `ibmcloud-backup-restore` image. Then, you mount the PVC that binds the PV that you want to use to your pod.  

## What is included
{: #backup_restore_whats_included}

Every `ibmcloud-backup-restore` image contains the following software packages:

- Alpine 3.7
- Duplicity 0.7.10
- Python, and  GnuPG Made Easy (GPGME) packages

## Setting up an {{site.data.keyword.cos_full_notm}} service instance
{: #backup_restore_setup_object_storage}

Create and configure an {{site.data.keyword.cos_full_notm}} service instance to serve as the repository for the data that you want to back up.
{: shortdesc}

1. Deploy an {{site.data.keyword.cos_full_notm}} service instance.
   1. Open the [{{site.data.keyword.cloud_notm}} catalog ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://cloud.ibm.com/catalog/services/cloud-object-storage).
   2. Enter a name for your service instance, such as `cos-backup`, and select **default** as your resource group.
   3. Review the [plan options ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/cloud-computing/bluemix/pricing-object-storage#s3api) for pricing information and select a plan.
   4. Click **Create**.
2. Retrieve the {{site.data.keyword.cos_full_notm}} service instance credentials.
   1. In the navigation on the service details page, click **Service Credentials**.
   2. Click **New credential**. A dialog box displays.
   3. Enter a name for your credentials.
   4. In **Add Inline Configuration Parameters (Optional)**, enter `{"HMAC":true}` to create additional HMAC credentials that the `ibmcloud-backup-restore` pod uses for HMAC authentication with the {{site.data.keyword.cos_full_notm}} service.
   5. Click **Add**. Your new credentials are listed in the **Service Credentials** table.
   6. Click **View credentials**.
   7. Make note of the **access_key_id** and the **secret_access_key** that you can find in the **cos_hmac_keys** section.
3. Create your first {{site.data.keyword.cos_full_notm}} bucket.
   1. In the navigation on the service details page, click **Buckets**.
   2. Click **Create bucket**. A dialog box displays.
   3. Enter a unique name for your bucket. The name must be unique within {{site.data.keyword.cos_full_notm}} across all regions and across all {{site.data.keyword.cloud_notm}} accounts.
   4. From the **Resiliency** drop down, select the level of availability that you want for your data. For more information, see [{{site.data.keyword.cos_full_notm}} regions and endpoints](/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-endpoints#endpoints).
   5. Change the **Location** to the region where you want to store your data. Keep in mind that your data might not be allowed to be stored in every region due to legal reasons.  
   6. Click **Create**.
4. Retrieve the {{site.data.keyword.cos_full_notm}} host name for your bucket.
   1. Click on your bucket name that you created in the previous step.
   2. In the navigation on the service details page, click **Buckets** > **Configuration**.
   3. Make note of the public URL that you can use to access the data in your bucket.

Review the [{{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage?topic=cloud-object-storage-about#about) documentation for more information on configuring your service instance.

## Backing up data from a persistent volume
{: #backup_restore_scheduled_backup}

You can create a one-time or scheduled backup for any persistent volume (PV) that is mounted to your app pod through a persistent volume claim (PVC).  
{: shortdesc}

The following example walks you through how to deploy a backup pod from the `ibmcloud-backup-restore` image, mount an existing PV to the backup pod by using a PVC, and back up the data from the PV to your {{site.data.keyword.cos_full_notm}} service instance.  

**Before you begin**

- [Set up an {{site.data.keyword.cos_full_notm}} service instance](#backup_restore_setup_object_storage).
- Install the required [CLIs](/docs/containers?topic=containers-cs_cli_install#cs_cli_install) to create and work with your cluster.
- [Create a standard cluster](/docs/containers?topic=containers-clusters#clusters_cli) or use an existing one.
- [Target your CLI to your cluster](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure).
- Create a persistent volume claim (PVC) for your [file storage](/docs/containers?topic=containers-file_storage#add_file) or [block storage](/docs/containers?topic=containers-block_storage#add_block) and mount it to your app deployment.

To back up an existing PV, complete the following steps:

1. Get the name of the PVC that binds the PV that you want to back up.

   ```
   kubectl get pvc
   ```
   {: pre}

2. Create a backup pod from the `ibmcloud-backup-restore` image. To access the data in the PV, you must mount the PVC that binds the PV to your backup pod. The following example creates a backup pod that runs a daily incremental backup. To create a backup with different settings, review a full list of [environment variable options](#backup_restore_env_reference).

   The `ibmcloud-backup-restore` image must be deployed in a single pod and cannot be used as part of a Kubernetes deployment.
   {: important}

   To view the image, target the global registry by running the `ibmcloud cr region-set global` command. Then, run `ibmcloud cr images --include-ibm` to list IBM public images.
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
   <caption>Table 1. YAML file components</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Idea icon"/> Understanding the YAML file components</th>
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
     <td><code>&lt;mount_path&gt;</code></td>
     <td>The volume mount path inside the backup container that mounts the PVC that you want to back up. If you want to back up multiple PVCs, specify a separate mount path for each PVC. Make sure that the name that you use in <code>volumeMount.name</code> matches the name of the volume that you specify in the <code>volumes.name</code> section of your YAML file. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>The name of the PVC that you want to back up. If you want to back up multiple PVCs, specify the volume name and the corresponding PVC for each PVC that you want to back up. Make sure that the name that you use in <code>volumes.name</code> matches the name in the <code>volumeMount.name</code> section of your YAML file.</td>
     </tr>
     </tbody>
     </table>

3. Create the backup pod and initiate a backup of your PV data.

    ```
    kubectl apply -f backuppod.yaml
    ```
    {: pre}

4. Verify that the pod is running.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

5. Verify that the backup ran successfully.

    ```
    kubectl logs backuppod
    ```
    {: pre}

6. Review the backup in {{site.data.keyword.cos_full_notm}} in the {{site.data.keyword.cloud_notm}} GUI.
    1. From the {{site.data.keyword.cloud_notm}} dashboard, find the {{site.data.keyword.cos_full_notm}} service instance.
    2. From the navigation, select **Buckets** and click on the bucket that you used in your backup configuration. Your backup is displayed as an object in your bucket.
    3. Review the compressed files. You can download the `vol1.difftar.gz` file, extract the file, and verify the backed-up data.

       If you delete or modify any files from {{site.data.keyword.cos_full_notm}}, those files cannot be recovered.
       {: important}

Your backup is available. If you configured your backup to create a one-time full backup, you must run the backup script each time that you want to create a new backup. If you configured the container to run an incremental backup periodically, then your backup runs as scheduled.

## Restoring data from {{site.data.keyword.cos_full_notm}} to your cluster
{: #backup_restore_restore_script_cli}

You can restore data from your {{site.data.keyword.cos_full_notm}} service instance to a PV in your cluster.

**Before you begin**

- [Target your CLI to your cluster](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure).
- [Create a backup for a PV in your cluster](#backup_restore_scheduled_backup).

To restore data from {{site.data.keyword.cos_full_notm}} to a PV, complete the following steps:

1. Get the name of the PVC that binds the PV where you want to restore your data.

   ```
   kubectl get pvc
   ```
   {: pre}

2. Create a restore pod from the `ibmcloud-backup-restore` image. To restore data to a PV, you must mount the PVC that binds the PV to your restore pod.
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
   <caption>Table 2. YAML file components</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Idea icon"/> Understanding the YAML file components</th>
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
     <td><code>&lt;mount_path&gt;</code></td>
     <td>The volume mount path inside the restore container that mounts the PVC where you want to restore your data. If you want to restore data to multiple PVCs, specify a separate mount path for each PVC. Make sure that the name that you use in <code>volumeMount.name</code> matches the name of the volume that you specify in the <code>volumes.name</code> section of your YAML file. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>The name of the PVC where you want to restore your data. To restore data to multiple PVCs, add a <code>volumes</code> entry for each PVC. Make sure that the name that you use in <code>volumes.name</code> matches the name in the <code>volumeMount.name</code> section of your YAML file.</td>
     </tr> 
     </tbody>
     </table>

3. Create the restore pod and start restoring your data.

    ```
    kubectl apply -f restorepod.yaml
    ```
    {: pre}

4. Verify that the pod is running.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME              READY     STATUS             RESTARTS   AGE
    restorepod        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    The pod runs the restore command and stops. The `CrashLoopBackOff` message means Kubernetes is attempting to restart the pod.
5. Verify that your data is successfully restored.

    ```
    kubectl logs restorepod
    ```
    {: pre}

6. Remove the pod to prevent the pod from consuming more resources.

    ```
    kubectl delete -f restorepod.yaml
    ```
    {: pre}

You successfully restored your backup. You can now mount the PVC that binds the PV to any other pod in your cluster to access the restored files. If the container data that was backed up included a non-root user, you must add non-root permissions to your new container. For more information, see [Adding non-root user access to volumes](/docs/containers?topic=containers-cs_troubleshoot_storage#cs_storage_nonroot).


## Environment variable reference
{: #backup_restore_env_reference}

Review the full list of fields that can be passed as environment variables or edited in the `config.conf` file in a running pod. Any value that is passed as an environment variable supersedes the value in the `config.conf` file. To review the environment variables for a pod, log in to the pod by using the `kubectl exec` command and run `env`.

|Key|Value options|
|---|-------------|
|ACCESS_KEY_ID|The **access_key_id** that is part of the HMAC credentials in {{site.data.keyword.cos_full_notm}}.|
|SECRET_ACCESS_KEY|The **secret_access_key** is part of the HMAC credentials in {{site.data.keyword.cos_full_notm}}.|
|ENDPOINT|The host name to access {{site.data.keyword.cos_full_notm}} bucket data.|
|BUCKET|The name of the bucket in {{site.data.keyword.cos_full_notm}} where your backed-up data is stored.|
{: caption="Table 4. {{site.data.keyword.cos_full_notm}} variables" caption-side="top"}

|Key|Value options|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: Default. Absolute file path of the directory that the volume is mounted to. Data is backed up from this directory. Do not select the directory backup_restore as that directory contains files for the backup and restore processes.|
|BACKUP_NAME|*volume_backup*: Default. Choose a backup name.|
|BACKUP_TYPE|*full*: Default. All files are backed up every time.<br/> *incremental*: Only new or changed files are backed up. If you choose *incremental*, you must choose values for **SCHEDULING_INFO** and **SCHEDULING_TYPE**.|
|SCHEDULE_TYPE|*none*: Default. Create a one-time backup.<br/> **Note:** If you choose to create a one-time backup, your pod is removed from the cluster after the backup is finished. <br/> *periodic*: Change the value to periodic to create scheduled backups.|
|SCHEDULE_INFO|*hourly*: Create an hourly backup.<br/>*daily*: Default. Create a daily backup.<br/>*weekly*: Create a weekly backup. You must include this variable if you schedule a periodic update.|
|EXCLUDE_DIRECTORIES|*none*: Default. Include the absolute file path of directories that you want excluded from the backup. Separate directories with a comma.|
{: caption="Table 5. Backup variables" caption-side="top"}

|Key|Value Options|
|---|-------------|
|BACKUP_NAME|*volume_backup*: Default. Include the name of the backup that is being restored from {{site.data.keyword.cos_full_notm}}.|
|RESTORE_DIRECTORY|*/backup*: Default. Absolute directory that the volume is mounted to. Data is restored to this directory. Do not select the directory `backup_restore` as that directory contains files for the backup and restore processes.|
{: caption="Table 6. Restore variables" caption-side="top"}

