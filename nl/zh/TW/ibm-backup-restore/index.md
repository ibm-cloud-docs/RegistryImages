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

# 開始使用 ibm-backup-restore 映像檔
{: #ibmbackup_restore_starter}

**ibm-backup-restore** 映像檔包含在 {{site.data.keyword.containerlong}} 中備份及還原持續性儲存空間所需的預先安裝套件。
{:shortdesc}

## 如何運作 
{: #how_it_works}

使用 **ibm-backup-restore** 映像檔，您可以為任何持續性磁區宣告 (pvc) 建立一次性或排定的備份。備份會儲存在 {{site.data.keyword.objectstoragefull}} 實例內。您可以藉由環境變數的方式將 {{site.data.keyword.objectstorageshort}} 認證傳遞至 **ibm-backup-restore** 容器，或是藉由編輯執行中容器的 `config.conf` 檔案來完成。您也可以將儲存的資料從 {{site.data.keyword.objectstorageshort}} 實例還原至磁區。因為映像檔包含用於執行備份及還原的 Script，所以使用者必須輸入啟動適當 Script 的指令。

## 包含的內容 
{: #whats_included}

每個 **ibm-backup-restore** 映像檔都會包含下列軟體套件：

-   Ubuntu 14.04 
-   Duplicity 0.7.10
-   python、gnupg 及 wget 套件

## 開始使用 
{: #how_to_get_started}

檢閱下列作業以備份及還原資料：
1.  [建立 {{site.data.keyword.objectstorageshort}} 服務實例](#object_storage)
2.  [執行排程的備份](#scheduled_backup)
3.  [執行還原 Script](#restore_script_cli)
4.  [加密備份](#encrypting_backups)
5.  [環境變數參照](#reference_backup_restore)

## 建立 {{site.data.keyword.objectstorageshort}} 服務實例 
{: #object_storage}

建立 {{site.data.keyword.objectstorageshort}} 實例，以作為磁區備份的儲存庫。

1.  從 {{site.data.keyword.Bluemix_notm}} 型錄的**儲存空間**區段中，佈建 {{site.data.keyword.objectstorageshort}} 實例。
2.  按一下 {{site.data.keyword.objectstorageshort}}。
3.  選取適用於 {{site.data.keyword.Bluemix_notm}} 的 {{site.data.keyword.objectstorageshort}} OpenStack Swift。然後按一下「建立」。
3.  按一下**服務認證**標籤。
4.  按一下**新建認證**。
5.  完成名稱欄位，但將其他欄位保留為空白。按一下**新增**。
6.  新的認證現在會列在**服務認證**表格中。按一下**檢視認證**。
7.  記下 **projectid**、**region**、**userId** 及 **password**。這些認證容許您的 **ibm-backup-restore** 容器存取此 {{site.data.keyword.objectstorageshort}} 實例。

如需配置實例的相關資訊，請檢閱 [{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) 文件，如需 {{site.data.keyword.objectstorageshort}} 定價的相關資訊，請檢閱[方案選項](../../ObjectStorage/objectstorage_faq.html#account-payment)。

## 執行排程的備份 
{: #scheduled_backup}

從 **ibm-backup-restore** 映像檔中建立容器 Pod，並啟動定期排程的備份。

開始之前：

-   安裝必要的 [CLI](../../../containers/cs_cli_install.html#cs_cli_install)。
-   [將 CLI 的目標設為](../../../containers/cs_cli_install.html#cs_cli_configure)您的叢集。


1. 建立名為 _backup-pvc.yaml_ 的配置檔。這個配置檔會建立持續性磁區宣告 (pvc)，您可以將其裝載至備份 Pod 作為磁區。

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
    
2. 建立 pvc。

    ```
    kubectl apply -f backup-pvc.yaml 
    ```
    {: pre}

3.  建立名為 _backup.yaml_ 的配置檔。針對空白環境變數，輸入您先前所記下的 {{site.data.keyword.objectstorageshort}} 認證值。請包含認證中所使用的引號。請將 <em>&lt;pod_name&gt;</em>、<em>&lt;container_name&gt;</em> 以及映像檔登錄 <em>&lt;region&gt;</em> 取代為適當的值。

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
    
    這些設定會建立預設名稱為 _mybackup_ 的每日增量備份。若要使用不同的設定來建立備份，請檢閱完整[環境變數選項](#reference_backup_restore)清單。

4.  建立執行備份 Script 的 Pod。

    ```
    kubectl apply -f backup.yaml
    ```
    {: pre}

5.  確認 Pod 在執行中。

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

4.  在 {{site.data.keyword.Bluemix_notm}} GUI 中，檢閱 {{site.data.keyword.objectstorageshort}} 中的備份。
    1.  按一下您為備份所建立的 {{site.data.keyword.objectstorageshort}} 實例。
    2.  從**容器**表格的**管理**標籤中，按一下 {{site.data.keyword.objectstorageshort}} 容器。
    3.  檢閱壓縮檔。![在 {{site.data.keyword.Bluemix_notm}} GUI 中的 Object Storage 容器會顯示備份的檔案。](images/volume_backup_screenshot.png) 您可以下載 vol1.difftar.gz 檔案、解壓縮該檔案，然後驗證已備份的資料。**重要事項**：如果您從 {{site.data.keyword.objectstorageshort}} 中刪除或修改任何檔案，將無法回復這些檔案。

您的備份可供使用。如果您已配置您的備份來建立一次性完整備份，則必須在每次要建立新的備份時執行備份 Script。如果您已配置容器定期執行增量備份，則會依排程執行備份。

## 執行還原 Script 
{: #restore_script_cli}

將備份從 {{site.data.keyword.objectstorageshort}} 還原至現有或新的磁區。

開始之前：

-   安裝必要的 [CLI](../../../containers/cs_cli_install.html#cs_cli_install)。
-   [將 CLI 的目標設為](../../../containers/cs_cli_install.html#cs_cli_configure)您的叢集。


1. 建立名為 _restore-pvc.yaml_ 的配置檔。這個配置檔會建立持續性磁區宣告 (pvc)，您可以將其裝載至還原 Pod 作為磁區。

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
    
2. 建立 pvc。

    ```
    kubectl apply -f restore-pvc.yaml 
    ```
    {: pre}

3.  建立名為 _restore.yaml_ 的配置檔。針對空白環境變數，輸入您先前所記下的 {{site.data.keyword.objectstorageshort}} 認證值。請包含認證中所使用的引號。BACKUP_NAME 必須符合 {{site.data.keyword.objectstorageshort}} 中所還原的備份名稱。

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

4.  建立執行還原 Script 的 Pod。
    
    ```
    kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  確認 Pod 在執行中。

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    Pod 會執行 restore 指令並停止。_CrashLoopBackOff_ 訊息表示 Kubernetes 正在試圖重新啟動 Pod。

6.  移除 Pod 以防止該 Pod 耗用更多資源。

    ```
    kubectl delete -f restore.yaml
    ```
    {: pre}

您已順利還原備份。您現在可以將任何新的 Pod 裝載至 pvc 中，讓該容器可以存取所還原的檔案。如果所備份的容器資料包含非 root 使用者，您必須將非 root 許可權新增至新的容器。如需相關資訊，請參閱[新增非 root 使用者對磁區的存取權](../../../containers/container_volumes_ov.html#container_volumes_write)。

## 加密備份 
{: #encrypting_backups}

加密 {{site.data.keyword.objectstorageshort}} 實例中的資料。

1.  下載 <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="外部鏈結圖示"></a>，以建立加密金鑰。
2.  在本端磁碟機上建立加密金鑰。您可以按 ENTER 鍵來接受預設值。

    **注意：**請記下所建立的通行詞組。如果遺失通行詞組，將無法解密任何使用金鑰所加密的資訊。

    ```
    gpg --gen-key
    ```
    {: pre}

    視 <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="外部鏈結圖示"></a> 的版本而定，您可能需要在指令中使用 `gpg2`，而非 `gpg`。

3.  驗證金鑰。

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

4.  從 `sub` 機碼，匯出含有值的加密金鑰。請將檔案命名為 encryption.asc。

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    在此範例中，sub 機碼的值為 *YYYYYYYY*
```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  在本端目錄中建立加密備份容器的環境變數檔案。

    ```
    touch <encryption_env-file_name>
    ```
    {: pre}
    
6. 建立名為 _backup-pvc.yaml_ 的配置檔。這個配置檔會建立持續性磁區宣告 (pvc)，您可以將其裝載至備份 Pod 作為磁區。

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

7. 建立 pvc。

    ```
    kubectl apply -f backup-pvc.yaml 
    ```
    {: pre}

8.  編輯部署配置檔，然後新增下列欄位。針對空白環境變數，輸入您先前所記下的 {{site.data.keyword.objectstorageshort}} 認證值。請包含認證中所使用的引號。若為 *ENCRYPTION_PASSPHRASE*，包括通行詞組，以透過密碼保護備份。此通行詞組不同於建立加密金鑰時所建立的詞組。當您備份資料以及還原資料時，必須包含此通行詞組。

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
   
    這些設定會建立加密的每日增量備份，名稱為 <em>&lt;volume_name&gt;</em>。若要使用不同的設定來建立備份，請檢閱完整[環境變數選項](#reference_backup_restore)清單。
    
9.  建立執行備份 Script 的 Pod。

    ```
    kubectl apply -f backup.yaml 
    ```
    {: pre}

9.  確認 Pod 在執行中。

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

10.  將加密金鑰複製到從 **ibm-backup-restore** 映像檔建置之容器的 /backup_restore 目錄。

    ```
    kubectl cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    請將加密金鑰的副本保留在本端，以解密資料。

11. 登入容器。

    ```
    kubecl exec -it <container_name> bash
    ```
    {: pre}

12. 確認 *encryption.asc* 已複製到 backup_restore 資料夾。

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

13. 從 backup_restore 資料夾執行備份 Script。

    ```
    ./vbackup &
    ```
    {: codeblock}

13. 若要確認您的備份已加密，請檢閱 {{site.data.keyword.objectstorageshort}} 實例中的檔案。這些檔案現在已在檔名尾端附加 `.gpg`。![Object Storage GUI 顯示所有已附加 .gpg（表示已加密）的備份檔案。](images/volume_backup_encrypt_screenshot.png)

您的備份已加密。若要還原檔案，請遵循[執行還原 Script](#restore_script_cli) 中的步驟，並在執行還原處理程序的容器的 backup_restore 目錄中併入 encryption.asc 檔案。如果備份已加密，您必須在建立還原容器時提供備份 yaml 檔案中的 ENCRYPTION_REQUIRED 及 ENCRYPTION_PASSPHRASE 作為環境變數。

## 環境變數參照 
{: #reference_backup_restore}

請檢閱可作為環境變數傳遞或在執行中容器的 `config.conf` 檔案中進行編輯的完整欄位清單。以環境變數傳遞的任何值會取代 `config.conf` 檔案中的值。若要檢閱容器的環境變數，請使用 `exec` 登入容器，然後執行 `env`。

|索引鍵|值選項|
|---|-------------|
|PROJECTID|{{site.data.keyword.objectstorageshort}} 中的 ProjectID|
|REGION|{{site.data.keyword.objectstorageshort}} 中的地區|
|USERID|{{site.data.keyword.objectstorageshort}} 中的 Userid|
|PASSWORD|{{site.data.keyword.objectstorageshort}} 中的密碼|
{: caption="表 1. {{site.data.keyword.objectstorageshort}} 變數" caption-side="top"}

從 {{site.data.keyword.objectstorageshort}} 認證擷取這些變數。每個執行備份或還原作業的容器，都包含這些環境變數。


|索引鍵|值選項|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*：預設值。要在其中裝載磁區的目錄的絕對檔案路徑。資料是從此目錄進行備份。請不要選取 backup_restore 目錄，因為該目錄包含用於備份及還原處理程序的檔案。|
|BACKUP_NAME|*volume_backup*：預設值。選擇備份名稱。|
|BACKUP_TYPE|*full*：預設值。每次都備份所有檔案。<br/> *incremental*：只備份新的或已變更的檔案。如果您選擇 *incremental*，則必須選擇 SCHEDULING_INFO 及 SCHEDULING_TYPE 的值。|
|SCHEDULE_TYPE|*none*：預設值。建立一次性備份。<br/> *periodic*：將值變更為 periodic，以建立排定的備份。|
|SCHEDULE_INFO|*hourly*：建立每小時備份。<br/>*daily*：預設值。建立每日備份。<br/>*weekly*：建立每週備份。如果您要排定 periodic 更新，則必須併入此變數。|
|EXCLUDE_DIRECTORIES|*none*：預設值。包括您要從備份中排除之目錄的絕對檔案路徑。請使用逗點來區隔目錄。|
{: caption="表 2. 備份變數" caption-side="top"}

|索引鍵|值選項|
|---|-------------|
|BACKUP_NAME|*volume_backup*：預設值。包括從 {{site.data.keyword.objectstorageshort}} 中所還原的備份名稱。|
|RESTORE_DIRECTORY|*/backup*：預設值。要在其中裝載磁區的絕對目錄。資料會還原至此目錄。請不要選取 backup_restore 目錄，因為該目錄包含用於備份及還原處理程序的檔案。|
{: caption="表 3. 還原變數" caption-side="top"}

|索引鍵|值選項|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*：預設值。如果您變更加密金鑰的檔名，或金鑰位於 backup_restore 以外的目錄，請併入此環境變數。|
|ENCRYPTION_REQUIRED|*no*：預設值。<br/> *yes*：如果您未加密備份，則不包括任何加密環境變數。如果您加密備份，請併入此索引鍵並使用值 yes。|
|ENCRYPTION_PASSPHRASE|包含通行詞組，以保護備份。此通行詞組不同於建立加密金鑰時所建立的詞組。當您備份資料以及還原資料時，必須包含此通行詞組。|
|IS_KEY_GENERATED_ON_SYSTEM|*no*：預設值。<br/> *yes*：如果您直接在容器上產生了加密金鑰，請包含這個環境變數與 yes 值。大部分使用者都會在其本端電腦上產生金鑰，並將金鑰複製到容器，而且可以將預設值保留為 no。|
{: caption="表 4. 加密變數" caption-side="top"}

## 將單一及可擴充容器的磁區資料移轉至 Kubernetes
{: #migrate_data}

為任何容器磁區建立一次性備份。備份會儲存在 {{site.data.keyword.objectstoragefull}} 實例內。接著，您可以將資料移轉至 Kubernetes 中的持續性磁區宣告。
{:shortdesc}

### 開始使用 
{: #how_to_get_started}

開始之前：

- [檢閱將應用程式移至 Kubernetes 的完整移轉路徑](../../../containers/cs_classic.html)
- [安裝單一及可擴充容器 CLI (bx ic)](../../../containers/container_cli_cfic_install.html)
- [安裝 {{site.data.keyword.containershort}} CLI（bx cs 及 kubectl）](../../../containers/cs_cli_install.html#cs_cli_install)
- [建立要將資料移轉至其中的標準 Kubernetes 叢集](../../../containers/cs_cluster.html#cs_cluster_cli)

請完成下列作業，以執行備份及還原作業：
1.  [建立 {{site.data.keyword.objectstorageshort}} 服務實例](#object_storage)（如之前所述）
2.  [執行一次性備份](#migrate_backup)
3.  [在 Kubernetes 中執行還原 Script](#migrate_restore)

### 執行一次性備份 
{: #migrate_backup}

從 **ibm-backup-restore** 映像檔中建立單一容器，並啟動備份。

1.  登入 {{site.data.keyword.containershort}} CLI。

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

2.  在本端目錄中建立環境變數檔案。

    ```
    touch <backup_env-file_name>
    ```
    {: pre}

3.  編輯環境變數檔案，然後新增下列欄位。針對空白環境變數，輸入您先前所記下的 {{site.data.keyword.objectstorageshort}} 認證值。請**不要**包含認證中所使用的引號。

    ```
    BACKUP_NAME=volume_backup
    BACKUP_DIRECTORY=/backup
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    ```
    {: codeblock}

    這些設定會建立預設名稱為 _volume_backup_ 的一次性備份。

4.  從 **ibm-backup-restore** 映像檔中，執行要備份所裝載磁區的容器。包括啟動備份 Script 的指令。

    -   確定您位在與 <em>&lt;backup_env-file&gt;</em> 相同的本端目錄中。
    -   要在其中裝載磁區的目錄必須符合環境變數檔案中的 BACKUP_DIRECTORY。
    -   將 <em>&lt;volume_name&gt;</em> 取代為所備份磁區的名稱。
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<backup_env-file_name> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    備份執行之後，即會關閉容器。如果容器未開始執行，請執行 `bx ic logs <container_name>` 來檢閱日誌，以尋找錯誤訊息。

5.  在 {{site.data.keyword.Bluemix_notm}} GUI 中，檢閱 {{site.data.keyword.objectstorageshort}} 中的備份。
    1.  按一下您為備份所建立的 {{site.data.keyword.objectstorageshort}} 實例。
    2.  按一下 {{site.data.keyword.objectstorageshort}} 容器。在此範例中，容器名稱是 volume_backup。
    3.  檢閱壓縮檔。![在 {{site.data.keyword.Bluemix_notm}} GUI 中的 Object Storage 容器會顯示已備份的檔案。](images/volume_backup_screenshot.png)您可以下載 difftar.gz 檔案、解壓縮該檔案，然後驗證已備份的資料。**重要事項**：如果您從 {{site.data.keyword.objectstorageshort}} 中刪除或修改任何檔案，將無法回復這些檔案。

### 將資料還原至 Kubernetes 叢集 
{: #migrate_restore}

將 {{site.data.keyword.objectstorageshort}} 的備份還原至 Kubernetes 叢集中的持續性磁區宣告。

開始之前：

- [將 CLI 的目標設為](../../../containers/cs_cli_install.html#cs_cli_configure)您的叢集。


1. 建立名為 _restore-pvc.yaml_ 的配置檔。這個配置檔會建立持續性磁區宣告 (pvc)，您可以將其裝載至備份 Pod 作為磁區。

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

2. 建立 pvc。

    ```
    kubectl apply -f restore-pvc.yaml 
    ```
    {: pre}

3.  建立名為 _restore.yaml_ 的配置檔。針對空白環境變數，輸入您先前所記下的 {{site.data.keyword.objectstorageshort}} 認證值。請包含認證中所使用的引號。BACKUP_NAME 必須符合 {{site.data.keyword.objectstorageshort}} 中所還原的備份名稱。

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

4.  建立執行還原 Script 的 Pod。

    ```
    kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  確認 Pod 已執行。

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}
    
    Pod 會執行 restore 指令並停止。_CrashLoopBackOff_ 訊息表示 Kubernetes 正在試圖重新啟動 Pod。

6.  移除 Pod 以防止該 Pod 耗用更多資源。

    ```
    kubectl delete -f restore.yaml
    ```
    {: pre}

您已順利將資料移轉至 Kubernetes 叢集。您現在可以將任何新的 Pod 裝載至 pvc 中，讓該 Pod 可以存取所還原的檔案。 

**附註**：如果所備份的容器資料包含非 root 使用者，您必須將非 root 許可權新增至新的容器。如需相關資訊，請參閱[新增非 root 使用者對磁區的存取權](../../../containers/container_volumes_ov.html#container_volumes_write)。
