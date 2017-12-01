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

# 開始使用 ibm-backup-restore 映像檔 
{: #ibmbackup_restore_starter}

**ibm-backup-restore** 映像檔包含在 {{site.data.keyword.containerlong}} 中備份及還原容器磁區所需的預先安裝套件。
{:shortdesc}

## 如何運作 
{: #how_it_works}

使用 **ibm-backup-restore** 映像檔，您可以為任何容器磁區建立一次性或排定的備份。備份會儲存在 {{site.data.keyword.objectstoragefull}} 實例內。您可以藉由環境變數的方式將 {{site.data.keyword.objectstorageshort}} 認證傳遞至 **ibm-backup-restore** 容器，或是藉由編輯執行中容器的 `config.conf` 檔案來完成。您也可以將儲存的資料從 {{site.data.keyword.objectstorageshort}} 實例還原至磁區。因為映像檔包含用於執行備份及還原的 Script，所以使用者必須在執行中容器中發出啟動適當 Script 的指令。

## 包含的內容 
{: #whats_included}

每個 **ibm-backup-restore** 映像檔都會包含下列軟體套件：

-   Ubuntu 14.04 
-   Duplicity 0.7.10
-   python、gnupg 及 wget 套件

## 開始使用 
{: #how_to_get_started}

請完成下列步驟，以執行備份及還原作業：
1.  [建立 {{site.data.keyword.objectstorageshort}} 服務實例](#object_storage)
2.  [執行排程的備份](#scheduled_backup)
3.  [執行還原 Script](#restore_script_cli)
4.  [加密備份](#encrypting_backups)
5.  [環境變數參照](#reference_backup_restore)

## 建立 {{site.data.keyword.objectstorageshort}} 服務實例 
{: #object_storage}

建立 {{site.data.keyword.objectstorageshort}} 實例，以作為磁區備份的儲存庫。

1.  從 {{site.data.keyword.Bluemix_notm}} 型錄的**服務**區段中，佈建 {{site.data.keyword.objectstorageshort}} 實例。
2.  選取 {{site.data.keyword.objectstorageshort}} 實例。
3.  按一下**服務認證**標籤。
4.  按一下**新建認證**。
5.  完成名稱欄位，但將其他欄位保留為空白。按一下**新增**。
6.  新的認證現在會列在**服務認證**表格中。按一下**檢視認證**。
7.  記下 **projectid**、**region**、**userId** 及 **password**。這些認證容許您的 **ibm-backup-restore** 容器存取此 {{site.data.keyword.objectstorageshort}} 實例。

如需配置實例的相關資訊，請檢閱 [{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) 文件，如需 {{site.data.keyword.objectstorageshort}} 定價的相關資訊，請檢閱[方案選項](../../ObjectStorage/objectstorage_faq.html#account-payment)。

## 執行排程的備份 
{: #scheduled_backup}

從 **ibm-backup-restore** 映像檔中建立容器，並啟動定期排程的備份。

1.  登入 {{site.data.keyword.containerlong_notm}} CLI。

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

3.  編輯環境變數檔案，然後新增下列欄位。針對空白環境變數，輸入您先前所記下的 {{site.data.keyword.objectstorageshort}} 認證值。請不要包含認證中所使用的引號。

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

    這些設定會建立預設名稱為 _volume_backup_ 的每日增量備份。若要使用不同的設定來建立備份，請檢閱完整[環境變數選項](#reference_backup_restore)清單。

4.  從 **ibm-backup-restore** 映像檔中，執行要備份所裝載磁區的容器。包括啟動備份 Script 的指令。

    -   確定您位在與 <em>&lt;backup_env-file&gt;</em> 相同的本端目錄中。
    -   要在其中裝載磁區的目錄必須符合環境變數檔案中的 BACKUP_DIRECTORY。
    -   將磁區名稱取代為所備份的磁區名稱。
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<backup_env-file_name> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    如果容器未開始執行，請執行 `bx ic logs <container_name>` 來檢閱日誌，以尋找錯誤訊息。

5.  在 {{site.data.keyword.Bluemix_notm}} GUI 中，檢閱 {{site.data.keyword.objectstorageshort}} 中的備份。
    1.  按一下您為備份所建立的 {{site.data.keyword.objectstorageshort}} 實例。
    2.  按一下 {{site.data.keyword.objectstorageshort}} 容器。在此範例中，容器名稱是 volume_backup。
    3.  檢閱壓縮檔。![ {{site.data.keyword.Bluemix_notm}} GUI 中的 {{site.data.keyword.objectstorageshort}} 容器會顯示備份的檔案。](images/volume_backup_screenshot.png) 您可以下載 `vol1.difftar.gz` 檔案、解壓縮該檔案，然後驗證已備份的資料。請注意，如果您從 {{site.data.keyword.objectstorageshort}} 中刪除或修改任何檔案，將無法回復這些檔案。

您的備份可供使用。如果您已配置 *<container_name>* 來建立一次性完整備份，則必須在每次要建立新的備份時執行備份 Script。如果您已配置容器定期執行增量備份，則會依排程執行備份。

## 執行還原 Script 
{: #restore_script_cli}

將備份從 {{site.data.keyword.objectstorageshort}} 還原至現有或新的容器磁區。

1.  在本端目錄中建立還原容器的環境變數檔案。

    ```
    touch <restore_env-file_name>
    ```
    {: pre}

2.  編輯環境變數檔案，然後新增下列欄位。針對空白環境變數，輸入您先前所記下的 {{site.data.keyword.objectstorageshort}} 認證值。BACKUP_NAME 必須符合 {{site.data.keyword.objectstorageshort}} 中所還原的備份名稱。

    ```
    BACKUP_NAME=<volume_name>
    RESTORE_DIRECTORY=/restore
    USERID=
    REGION=
    PROJECTID=
    PASSWORD=
    ```
    {: codeblock}

3.  建立要將備份檔案還原至其中的磁區。

    ```
    bx ic volume create <volume_name>
    ```
    {: pre}

4.  從 **ibm-backup-restore** 映像檔中，執行容器。包括啟動還原 Script 的指令。

    -   確定您位在與 <em>&lt;restore_env-file&gt;</em> 相同的本端目錄中。
    -   要在其中裝載磁區的目錄必須符合環境變數檔案中的 BACKUP_DIRECTORY。
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/restore --env-file ./<restore_env-file_name> registry.ng.buemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vrestore"
    ```
    {: pre}

5.  檢查容器日誌，確認還原處理程序已完成。

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

    還原處理程序完成時，即會關閉容器。

6.  選用項目：移除容器，不再耗用任何資源。

    ```
    bx ic rm -f <container_name>
    ```
    {: pre}

7. 選用項目：若要確認備份檔案位於磁區中，請登入容器、導覽至裝載路徑，然後列出檔案。

    ```
    $ bx ic exec -it <container_name> bash
    root@instance:/backup_restore# cd ..
    root@instance/# cd restore
    root@instance:/restore# ls
    duplicity-I3_pJv-tempdir  new_file
    root@instance:/restore# 
    ```
    {: screen}

    tempdir 是備份處理程序的產物，可以手動將它刪除。


您已順利將備份還原至磁區 *volume_name*。您現在可以將任何新的容器裝載至磁區中，讓該容器可以存取所還原的檔案。如果所備份的容器資料包含非 root 使用者，您必須將非 root 許可權新增至新的容器。如需相關資訊，請參閱[新增非 root 使用者對磁區的存取權](../../../containers/container_volumes_ov.html#container_volumes_write)。

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

6.  編輯環境變數檔案，然後新增下列欄位。針對空白環境變數，輸入您先前所記下的 {{site.data.keyword.objectstorageshort}} 認證值。請不要包含認證中所使用的引號。若為 *ENCRYPTION_PASSPHRASE*，包括通行詞組，以透過密碼保護備份。此通行詞組不同於建立加密金鑰時所建立的詞組。當您備份資料以及還原資料時，必須包含此通行詞組。

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

    這些設定會建立加密的每日增量備份，名稱為 *<volume_name>*。若要使用不同的設定來建立備份，請檢閱完整[環境變數選項](#reference_backup_restore)清單。

7.  登入 {{site.data.keyword.containerlong_notm}} CLI。

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

8.  從 **ibm-backup-restore** 映像檔中，執行要備份所裝載磁區的容器。包括啟動備份 Script 的指令。

    -   確定您位在與 <em>&lt;encryption_env-file_name&gt;</em> 相同的本端目錄中。
    -   要在其中裝載磁區的目錄必須符合環境變數檔案中的 BACKUP_DIRECTORY。
    -   將磁區名稱取代為所備份的磁區名稱。
   
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<encrypted_env-file_name> registry.ng.bluemix.net/ibm-backup-restore
    ```
    {: pre}

    如果容器未開始執行，請執行 `bx ic logs <container_name>` 來檢閱日誌，以尋找錯誤訊息。

9.  將加密金鑰複製到從 **ibm-backup-restore** 映像檔建置之容器的 /backup_restore 目錄。

    ```
    bx ic cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    請將加密金鑰的副本保留在本端，以解密資料。

10. 登入容器。

    ```
    bx ic exec -it <container_name> bash
    ```
    {: pre}

11. 確認 *encryption.asc* 已複製到 backup_restore 資料夾。

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

12. 從 backup_restore 資料夾執行備份 Script。

    ```
    ./vbackup &
    ```
    {: codeblock}

13. 若要確認您的備份已加密，請檢閱 {{site.data.keyword.objectstorageshort}} 實例中的檔案。這些檔案現在已在檔名尾端附加 `.gpg`。![ {{site.data.keyword.objectstorageshort}} GUI 顯示所有已附加 .gpg（表示已加密）的備份檔案。](images/volume_backup_encrypt_screenshot.png)

您的備份已加密。若要還原檔案，請遵循[執行還原 Script](#restore_script_cli) 中的步驟，並在執行還原處理程序的容器的 backup_restore 目錄中併入 encryption.asc 檔案。如果備份已加密，您必須在建立還原容器時提供 ENCRYPTION_REQUIRED 及 ENCRYPTION_PASSPHRASE 作為環境變數。

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

