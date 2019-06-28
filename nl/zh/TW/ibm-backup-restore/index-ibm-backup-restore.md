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

# 開始使用 `ibmcloud-backup-restore` 映像檔
{: #ibmbackup_restore_starter}

`ibmcloud-backup-restore` 映像檔包含在 {{site.data.keyword.containerlong}} 中備份及還原持續性儲存空間所需的預先安裝套件。
{:shortdesc}

`ibmcloud-backup-restore` 映像檔僅能在 `icr.io` 登錄網域中使用，並在 `registry.bluemix.net` 網域中遭到淘汰。若要存取映像檔，並從中部署容器，您的叢集必須將 API 金鑰儲存在映像檔取回密碼中，以授權對 {{site.data.keyword.registryshort_notm}} 的存取。若為 2019 年 2 月 25 日之前建立的叢集，您必須更新叢集，才能存取 `icr.io` 登錄網域，以便您可以使用 `ibmcloud-backup-restore` 映像檔。如需已變更什麼及如何更新叢集的相關資訊，請參閱[瞭解如何授權叢集以從登錄中取回映像檔](/docs/containers?topic=containers-images#cluster_registry_auth)，以及[更新現有叢集來使用 API 金鑰映像檔取回密碼](/docs/containers?topic=containers-images#imagePullSecret_migrate_api_key)。
{: important}

您可以使用指令行存取 {{site.data.keyword.IBM_notm}} 所提供的映像檔，請參閱 [IBM 公用映像檔](/docs/services/Registry?topic=registry-public_images#public_images)。
{: tip}

## 如何運作
{: #backup_restore_how_it_works}

使用 `ibmcloud-backup-restore` 映像檔，您可以為儲存在叢集中之持續性磁區 (PV) 的應用程式資料建立一次性或排定的備份，或是將應用程式資料還原到 PV。若要備份及還原資料，請從 `ibmcloud-backup-restore` 映像檔部署 Pod。然後，裝載負責連結您要備份之 PV 或您要用於將資料還原至 Pod 之 PV 的 PVC。

**我的資料去哪裡？如何存取它？** 您備份的資料儲存在 {{site.data.keyword.cos_full_notm}} 服務實例內。若要存取服務，請使用 {{site.data.keyword.cos_full_notm}} 服務認證作為 `ibmcloud-backup-restore` Pod 中的環境變數，或編輯執行中 Pod 的 `config.conf` 檔。

**我可以將備份的資料還原到不同應用程式或不同 PV 嗎？** 是的，您可以將儲存的資料從 {{site.data.keyword.cos_full_notm}} 服務實例還原至叢集裡的 PV。若要還原資料，您必須從 `ibmcloud-backup-restore` 映像檔建立一個還原 Pod。然後，裝載負責連結您要用於 Pod 之 PV 的 PVC。  

## 包含的內容
{: #backup_restore_whats_included}

每個 `ibmcloud-backup-restore` 映像檔都會包含下列軟體套件：

- Alpine 3.7
- Duplicity 0.7.10
- Python 及 GnuPG Made Easy (GPGme) 套件

## 設定 {{site.data.keyword.cos_full_notm}} 服務實例
{: #backup_restore_setup_object_storage}

建立並配置 {{site.data.keyword.cos_full_notm}} 服務實例，以作為您要備份之資料的儲存庫。
{: shortdesc}

1. 部署 {{site.data.keyword.cos_full_notm}} 服務實例。
   1. 開啟 [{{site.data.keyword.cloud_notm}} 型錄 ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://cloud.ibm.com/catalog/services/cloud-object-storage)。
   2. 輸入服務實例的名稱，例如 `cos-backup`，然後選取 **default** 作為您的資源群組。
   3. 檢閱[方案選項 ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://www.ibm.com/cloud-computing/bluemix/pricing-object-storage#s3api) 以取得定價資訊，並選取方案。
   4. 按一下**建立**。
2. 擷取 {{site.data.keyword.cos_full_notm}} 服務實例認證。
   1. 在服務詳細資料頁面上的導覽，按一下**服務認證**。
   2. 按一下**新建認證**。會顯示對話框。
   3. 輸入認證的名稱。
   4. 在**新增線型配置參數（選用）**欄位中，輸入 `{"HMAC":true}` 以建立額外的 HMAC 認證，供 `ibmcloud-backup-restore` Pod 用於向 {{site.data.keyword.cos_full_notm}} 服務進行 HMAC 鑑別。
   5. 按一下**新增**。新的認證會列在**服務認證**表格中。
   6. 按一下**檢視認證**。
   7. 記下 **cos_hmac_keys** 區段中找到的 **access_key_id** 及 **secret_access_key**。
3. 建立第一個 {{site.data.keyword.cos_full_notm}} 儲存區。
   1. 在服務詳細資料頁面上的導覽，按一下**儲存區**。
   2. 按一下**建立儲存區**。會顯示對話框。
   3. 輸入儲存區的唯一名稱。名稱必須在跨所有地區且跨所有 {{site.data.keyword.cloud_notm}} 帳戶的 {{site.data.keyword.cos_full_notm}} 內都唯一。
   4. 從**備援**清單，選取您的資料所需要的可用性層次。如需相關資訊，請參閱 [{{site.data.keyword.cos_full_notm}} 地區及端點](/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-endpoints#endpoints)。
   5. 將**位置**變更為您要還原資料的地區。請記住，您的資料可能因為法律因素而無法還原到每個地區。  
   6. 按一下**建立**。
4. 擷取儲存區的 {{site.data.keyword.cos_full_notm}} 主機名稱。
   1. 按一下您在前一個步驟中建立的儲存區名稱。
   2. 在服務詳細資料頁面上的導覽，按一下**儲存區** > **配置**。
   3. 記下公用 URL，您可以用來存取儲存區中的資料。

檢閱 [{{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage?topic=cloud-object-storage-about-ibm-cloud-object-storage) 文件，以取得配置服務實例的相關資訊。

## 從持續性磁區備份資料
{: #backup_restore_scheduled_backup}

您可以為透過持續性磁區要求 (PVC) 裝載至應用程式 Pod 的任何持續性磁區 (PV)，建立一次性或排定的備份。  
{: shortdesc}

下列範例會示範如何從 `ibmcloud-backup-restore` 映像檔部署備份 Pod、使用 PVC 將現有 PV 裝載至備份 Pod，然後將資料從 PV 備份到 {{site.data.keyword.cos_full_notm}} 服務實例。  

**開始之前**

- [設定 {{site.data.keyword.cos_full_notm}} 服務實例](#backup_restore_setup_object_storage)。
- 安裝必要的 [CLI](/docs/containers?topic=containers-cs_cli_install#cs_cli_install) 以建立及使用叢集。
- [建立標準叢集](/docs/containers?topic=containers-clusters#clusters_cli)或使用現有叢集。
- [將 CLI 的目標設為叢集](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)。
- 為您的[檔案儲存空間](/docs/containers?topic=containers-file_storage#add_file)或[區塊儲存空間](/docs/containers?topic=containers-block_storage#add_block)建立持續性磁區要求 (PVC)，並將其裝載至您的應用程式部署。

若要備份現有 PV，請完成下列步驟：

1. 取得連結您要備份之 PV 的 PVC 名稱。

   ```
   kubectl get pvc
   ```
   {: pre}

2. 從 `ibmcloud-backup-restore` 映像檔建立備份 Pod。若要存取 PV 中的資料，您必須裝載將 PV 連結至備份 Pod 的 PVC。下列範例會建立備份 Pod，以執行每日增量備份。若要使用不同的設定來建立備份，請檢閱完整[環境變數選項](#backup_restore_env_reference)清單。

   `ibmcloud-backup-restore` 映像檔必須部署在單一 Pod，而不能用來作為 Kubernetes 部署的一部分。
   {: important}

   若要檢視映像檔，請執行 `ibmcloud cr region-set global` 指令設定全球登錄的目標。然後，執行 `ibmcloud cr images --include-ibm` 以列出 IBM 公用映像檔。
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
   <caption>表 1. YAML 檔案元件</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="構想圖示"/> 瞭解 YAML 檔案元件</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>您在 {{site.data.keyword.cos_full_notm}} 服務實例認證之中擷取的存取金鑰 ID。</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</code></td>
     <td>您在 {{site.data.keyword.cos_full_notm}} 服務實例認證之中擷取的密碼存取金鑰。</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>您用來存取特定地區之 {{site.data.keyword.cos_full_notm}} 的地區 API 端點 URL。</td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>您想要用來在 {{site.data.keyword.cos_full_notm}} 儲存備份的儲存區名稱。</td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>在儲存區中存放備份之物件的唯一名稱。</td>
     </tr>
     <tr>
     <td><code>&lt;mount_path&gt;</code></td>
     <td>備份容器內的磁區裝載路徑，而此容器裝載您要備份的 PVC。如果您想要備份多個 PVC，請為每一個 PVC 指定個別的裝載路徑。請確定您在 <code>volumeMount.name</code> 中使用的名稱，符合您在 YAML 檔之 <code>volumes.name</code> 區段中指定的磁區名稱。</td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>您要備份的 PVC 名稱。如果您想要備份多個 PVC，請為您要備份的每一個 PVC 指定磁區名稱及對應 PVC。請確定您在 <code>volumes.name</code> 中使用的名稱，符合 YAML 檔之 <code>volumeMount.name</code> 區段中的名稱。</td>
     </tr>
     </tbody>
     </table>

3. 建立備份 Pod 並起始 PV 資料備份。

    ```
    kubectl apply -f backuppod.yaml
    ```
    {: pre}

4. 驗證 Pod 在執行中。

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

5. 驗證備份已順利執行。
    

    ```
    kubectl logs backuppod
    ```
    {: pre}

6. 在 {{site.data.keyword.cloud_notm}} GUI 中，檢閱 {{site.data.keyword.cos_full_notm}} 中的備份。
    1. 從 {{site.data.keyword.cloud_notm}} 儀表板，尋找 {{site.data.keyword.cos_full_notm}} 服務實例。
    2. 從導覽選取**儲存區**，然後按一下您在備份配置中使用的儲存區。您的備份會顯示為儲存區中的物件。
    3. 檢閱壓縮檔。您可以下載 `vol1.difftar.gz` 檔案、解壓縮該檔案，然後驗證已備份的資料。

       如果您從 {{site.data.keyword.cos_full_notm}} 中刪除或修改任何檔案，將無法回復這些檔案。
       {: important}

您的備份可供使用。如果您已配置您的備份來建立一次性完整備份，則必須在每次要建立新的備份時執行備份 Script。如果您已配置容器定期執行增量備份，則會依排程執行備份。

## 將資料從 {{site.data.keyword.cos_full_notm}} 還原至叢集
{: #backup_restore_restore_script_cli}

您可以將資料從 {{site.data.keyword.cos_full_notm}} 服務實例還原至叢集裡的 PV。

如果您在 {{site.data.keyword.cos_full_notm}} 服務實例中有多個完整備份，則 PVC 會使用最後一個完整備份的資料進行還原。如果您有增量備份，則 PVC 會使用最後一個完整備份的資料進行還原，並且包括直到您開始還原當天為止的所有增量備份。

**開始之前**

- [將 CLI 的目標設為叢集](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)。
- [在叢集中建立 PV 的備份](#backup_restore_scheduled_backup)。

若要將資料從 {{site.data.keyword.cos_full_notm}} 還原至 PV，請完成下列步驟：

1. 取得連結您要還原資料之 PV 的 PVC 名稱。

   ```
   kubectl get pvc
   ```
   {: pre}

2. 從 `ibmcloud-backup-restore` 映像檔建立還原 Pod。若要將資料還原至 PV，您必須裝載將 PV 連結至還原 Pod 的 PVC。{: codeblock}

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
   <caption>表 2. YAML 檔案元件</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="構想圖示"/> 瞭解 YAML 檔案元件</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>您在 {{site.data.keyword.cos_full_notm}} 服務實例認證之中擷取的存取金鑰 ID。</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</code></td>
     <td>您在 {{site.data.keyword.cos_full_notm}} 服務實例認證之中擷取的密碼存取金鑰。</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>您用來存取特定地區之 {{site.data.keyword.cos_full_notm}} 的地區 API 端點 URL。</td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>您用來在 {{site.data.keyword.cos_full_notm}} 儲存備份的儲存區名稱。</td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>在儲存區中存放備份之物件的唯一名稱。您必須使用備份 Pod 中使用的名稱，將資料儲存在 {{site.data.keyword.cos_full_notm}}。</td>
     </tr>
     <td><code>&lt;mount_path&gt;</code></td>
     <td>還原容器內的磁區裝載路徑，而此容器裝載您要在其中還原資料的 PVC。如果您想要將資料還原至多個 PVC，請為每一個 PVC 指定個別的裝載路徑。請確定您在 <code>volumeMount.name</code> 中使用的名稱，符合您在 YAML 檔之 <code>volumes.name</code> 區段中指定的磁區名稱。</td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>您要在其中還原資料的 PVC 名稱。若要將資料還原至多個 PVC，請為每一個 PVC 新增一個 <code>volumes</code> 項目。請確定您在 <code>volumes.name</code> 中使用的名稱，符合 YAML 檔之 <code>volumeMount.name</code> 區段中的名稱。</td>
     </tr> 
     </tbody>
     </table>

3. 建立還原 Pod 並開始還原資料。

    ```
    kubectl apply -f restorepod.yaml
    ```
    {: pre}

4. 驗證 Pod 在執行中。

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME              READY     STATUS             RESTARTS   AGE
    restorepod        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    Pod 會執行 restore 指令並停止。`CrashLoopBackOff` 訊息表示 Kubernetes 正在試圖重新啟動 Pod。
5. 驗證您的資料已順利還原。

    ```
    kubectl logs restorepod
    ```
    {: pre}

6. 移除 Pod 以防止該 Pod 使用更多資源。

    ```
    kubectl delete -f restorepod.yaml
    ```
    {: pre}

您已順利還原備份。您現在可以裝載負責將 PV 連結至叢集內任何其他 Pod 的 PVC，以便存取還原檔案。如果所備份的容器資料包含非 root 使用者，您必須將非 root 許可權新增至新的容器。如需相關資訊，請參閱[新增非 root 使用者對磁區的存取權](/docs/containers?topic=containers-cs_troubleshoot_storage#cs_storage_nonroot)。


## 環境變數參考資料
{: #backup_restore_env_reference}

請檢閱可作為環境變數傳遞或在執行中 Pod 的 `config.conf` 檔案中進行編輯的完整欄位清單。以環境變數傳遞的任何值會取代 `config.conf` 檔案中的值。若要檢閱 Pod 的環境變數，請使用 `kubectl exec` 指令登入 Pod，然後執行 `env`。

|索引鍵|值選項|
|---|-------------|
|ACCESS_KEY_ID|屬於 {{site.data.keyword.cos_full_notm}} HMAC 認證一部分的 **access_key_id**。|
|SECRET_ACCESS_KEY|屬於 {{site.data.keyword.cos_full_notm}} HMAC 認證一部分的 **secret_access_key**。|
|ENDPOINT|用來存取 {{site.data.keyword.cos_full_notm}} 儲存區資料的主機名稱。|
|BUCKET|備份資料儲存所在的 {{site.data.keyword.cos_full_notm}} 儲存區名稱。|
{: caption="表 4. {{site.data.keyword.cos_full_notm}} 變數" caption-side="top"}

|索引鍵|值選項|
|---|-------------|
|BACKUP_DIRECTORY|`/backup`。預設。要在其中裝載磁區的目錄的絕對檔案路徑。資料是從此目錄進行備份。請不要選取 `backup_restore` 目錄，因為該目錄包含用於備份及還原處理程序的檔案。|
|BACKUP_NAME|`volume_backup`。預設。選擇備份名稱。|
|BACKUP_TYPE|`full`。預設。每次都備份所有檔案。<br/> `incremental`。只備份新的或已變更的檔案。如果您選擇 `incremental`，則必須選擇 **SCHEDULING_INFO** 及 **SCHEDULING_TYPE** 的值。|
|SCHEDULE_TYPE|`none`。預設。建立一次性備份。<br/> **附註：**如果您選擇建立一次性備份，則會在完成備份之後，從叢集中移除 Pod。<br/> `periodic`。將值變更為 periodic，以建立排定的備份。|
|SCHEDULE_INFO|`hourly`。建立每小時備份。<br/>`daily`。預設。建立每日備份。<br/>`weekly`。建立每週備份。如果您要排定 periodic 更新，則必須包含此變數。|
|EXCLUDE_DIRECTORIES|`none`。預設。包括您要從備份中排除之目錄的絕對檔案路徑。請使用逗點來區隔目錄。|
{: caption="表 5. 備份變數" caption-side="top"}

|索引鍵|值選項|
|---|-------------|
|BACKUP_NAME|`volume_backup`。預設。包括從 {{site.data.keyword.cos_full_notm}} 中所還原的備份名稱。|
|RESTORE_DIRECTORY|`/backup`。預設。要在其中裝載磁區的絕對目錄。資料會還原至此目錄。請不要選取 `backup_restore` 目錄，因為該目錄包含用於備份及還原處理程序的檔案。|
{: caption="表 6. 還原變數" caption-side="top"}

