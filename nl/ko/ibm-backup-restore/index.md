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

# ibm-backup-restore 이미지 시작하기 
{: #ibmbackup_restore_starter}

**ibm-backup-restore** 이미지는 {{site.data.keyword.containerlong}}에서 컨테이너 볼륨을 백업하고 복원하는 데 필요한 사전 설치된 패키지를 포함합니다.
{:shortdesc}

## 작동 방식 
{: #how_it_works}

**ibm-backup-restore** 이미지를 사용하여 컨테이너 볼륨에 대해 일회성 또는 예정된 백업을 작성할 수 있습니다. 백업은 {{site.data.keyword.objectstoragefull}} 인스턴스 내에 저장됩니다. 실행중인 컨테이너의 `config.conf` 파일을 편집하여 {{site.data.keyword.objectstorageshort}} 신임 정보를 **ibm-backup-restore** 컨테이너에 환경 변수로 전달할 수 있습니다. 또한 {{site.data.keyword.objectstorageshort}} 인스턴스에서 볼륨으로 저장된 데이터를 복구할 수 있습니다. 이미지에는 실행 중인 백업과 복원 둘 다에 대한 스크립트가 포함되므로 사용자는 실행 중인 컨테이너에서 적절한 스크립트를 시작하는 명령을 발행해야 합니다. 

## 포함된 항목 
{: #whats_included}

모든 **ibm-backup-restore** 이미지는 다음 소프트웨어 패키지를 포함합니다.

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   python, gnupg 및 wget 패키지

## 시작하기 
{: #how_to_get_started}

백업 및 복원 오퍼레이션을 수행하려면 다음 단계를 완료하십시오.
1.  [{{site.data.keyword.objectstorageshort}} 서비스 인스턴스 작성](#object_storage)
2.  [스케줄된 백업 실행](#scheduled_backup)
3.  [복원 스크립트 실행](#restore_script_cli)
4.  [백업 암호화](#encrypting_backups)
5.  [환경 변수 참조](#reference_backup_restore)

## {{site.data.keyword.objectstorageshort}} 서비스 인스턴스 작성 
{: #object_storage}

{{site.data.keyword.objectstorageshort}} 인스턴스를 작성하여 볼륨 백업을 위한 저장소를 제공합니다.

1.  {{site.data.keyword.Bluemix_notm}} 카탈로그의 **서비스**섹션에서 {{site.data.keyword.objectstorageshort}} 인스턴스를 프로비저닝하십시오.
2.  {{site.data.keyword.objectstorageshort}} 인스턴스를 선택하십시오.
3.  **서비스 신임 정보** 탭을 클릭하십시오.
4.  **새 신임 정보**를 클릭하십시오.
5.  이름 필드를 채우고 나머지 필드를 공백으로 두십시오. **추가**를 클릭하십시오.
6.  이제 새 신임 정보가 **서비스 신임 정보** 표에 나열됩니다. **신임 정보 보기**를 클릭합니다.
7.  **프로젝트 ID**, **지역**, **사용자 ID**, **비밀번호**를 기록하십시오. 이 신임 정보는 **ibm-backup-restore** 컨테이너가 {{site.data.keyword.objectstorageshort}} 인스턴스에 액세스하는 데 필요합니다.

인스턴스 구성에 대한 자세한 정보는 [{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) 문서를, {{site.data.keyword.objectstorageshort}} 가격 책정에 대한 정보는 [플랜 옵션](../../ObjectStorage/objectstorage_faq.html#account-payment)을 검토하십시오.

## 스케줄된 백업 실행 
{: #scheduled_backup}

**ibm-backup-restore** 이미지에서 컨테이너를 작성하고 정기적으로 스케줄된 백업을 시작합니다.

1.  {{site.data.keyword.containerlong_notm}} CLI에 로그인하십시오.

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

2.  로컬 디렉토리에서 환경 변수 파일을 작성하십시오.

    ```
    touch <backup_env-file_name>
    ```
    {: pre}

3.  환경 변수 파일을 편집하고 다음 필드를 추가하십시오. 비어 있는 환경 변수의 경우, 이전에 기록해 둔 {{site.data.keyword.objectstorageshort}} 신임 정보에서 값을 입력하십시오. 신임 정보에 사용된 따옴표는 사용하지 마십시오.

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

    이 설정은 기본 이름 _volume_backup_으로 일별 증분식 백업을 작성합니다. 다른 설정으로 백업을 작성하려면 [환경 변수 옵션](#reference_backup_restore)의 전체 목록을 검토하십시오.

4.  백업할 마운트된 볼륨과 함께 **ibm-backup-restore** 이미지에서 컨테이너를 실행하십시오. 백업 스크립트를 시작할 명령을 포함하십시오.

    -   <em>&lt;backup_env-file&gt;</em>과 동일한 로컬 디렉토리에 있는지 확인하십시오.
    -   볼륨이 마운트되는 디렉토리는 환경 변수 파일에서 BACKUP_DIRECTORY와 일치해야 합니다.
    -   볼륨 이름을 백업할 볼륨 이름으로 대체하십시오.
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<backup_env-file_name> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    컨테이너가 실행을 시작하지 않으면 `bx ic logs <container_name>`를 실행하여 로그에서 오류 메시지를 검토하십시오.

5.  {{site.data.keyword.Bluemix_notm}} GUI의 {{site.data.keyword.objectstorageshort}}에서 백업을 검토하십시오.
    1.  백업을 위해 작성한 {{site.data.keyword.objectstorageshort}} 인스턴스를 클릭하십시오.
    2.  {{site.data.keyword.objectstorageshort}} 컨테이너를 클릭하십시오. 이 예제에서 컨테이너 이름은 volume_backup입니다.
    3.  압축된 파일을 검토하십시오.![{{site.data.keyword.Bluemix_notm}} GUI의 {{site.data.keyword.objectstorageshort}} 컨테이너에는 백업된 파일이 표시됩니다.](images/volume_backup_screenshot.png) `vol1.difftar.gz` 파일을 다운로드하고 파일의 압축을 푼 다음 백업된 데이터를 확인할 수 있습니다. {{site.data.keyword.objectstorageshort}}의 파일을 삭제하거나 수정한 경우 해당 파일을 복구할 수 없습니다.

백업을 사용할 수 있습니다. 일회성 전체 백업을 작성하도록 *<container_name>*을 구성한 경우 새 백업을 작성할 때마다 백업 스크립트를 실행해야 합니다. 증분식 백업을 정기적으로 실행하도록 컨테이너를 구성한 경우 백업이 예정대로 실행됩니다. 

## 복원 스크립트 실행 
{: #restore_script_cli}

{{site.data.keyword.objectstorageshort}}에서 기존 또는 신규 컨테이너 볼륨으로 백업을 복원합니다.

1.  로컬 디렉토리에서 복원 컨테이너의 환경 변수 파일을 작성하십시오.

    ```
    touch <restore_env-file_name>
    ```
    {: pre}

2.  환경 변수 파일을 편집하고 다음 필드를 추가하십시오. 비어 있는 환경 변수의 경우, 이전에 기록해 둔 {{site.data.keyword.objectstorageshort}} 신임 정보에서 값을 입력하십시오. BACKUP_NAME은 복원할 {{site.data.keyword.objectstorageshort}}의 백업 이름과 일치해야 합니다.

    ```
    BACKUP_NAME=<volume_name>
    RESTORE_DIRECTORY=/restore
    USERID=
    REGION=
    PROJECTID=
    PASSWORD=
    ```
    {: codeblock}

3.  백업 파일을 복원할 볼륨을 작성하십시오.

    ```
    bx ic volume create <volume_name>
    ```
    {: pre}

4.  **ibm-backup-restore** 이미지에서 컨테이너를 실행하십시오. 복원 스크립트를 시작할 명령을 포함하십시오.

    -   <em>&lt;restore_env-file&gt;</em>과 동일한 로컬 디렉토리에 있는지 확인하십시오.
    -   볼륨이 마운트되는 디렉토리는 환경 변수 파일에서 BACKUP_DIRECTORY와 일치해야 합니다.
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/restore --env-file ./<restore_env-file_name> registry.ng.buemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vrestore"
    ```
    {: pre}

5.  컨테이너 로그를 통해 복원 프로세스가 완료되었는지 확인하십시오.

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

    복원 프로세스가 완료되면 컨테이너가 종료됩니다.

6.  선택사항: 리소스를 이용하지 않도록 컨테이너를 제거하십시오.

    ```
    bx ic rm -f <container_name>
    ```
    {: pre}

7. 선택사항: 백업 파일이 볼륨에 있는지 확인하려면 컨테이너에 로그인하고 마운트 경로로 이동한 다음 파일을 나열하십시오.

    ```
    $ bx ic exec -it <container_name> bash
    root@instance:/backup_restore# cd ..
    root@instance/# cd restore
    root@instance:/restore# ls
    duplicity-I3_pJv-tempdir  new_file
    root@instance:/restore# 
    ```
    {: screen}

    tempdir은 백업 프로세스의 제품이며 수동으로 삭제할 수 있습니다.


*volume_name* 볼륨에 백업이 복원되었습니다. 이제 컨테이너가 복원된 파일에 액세스할 수 있도록 새 컨테이너를 볼륨에 마운트할 수 있습니다. 백업된 컨테이너 데이터에 비루트 사용자가 포함된 경우 새 컨테이너에 비루트 권한을 추가해야 합니다. 자세한 정보는 [볼륨에 대한 비루트 사용자 액세스 권한 추가](../../../containers/container_volumes_ov.html#container_volumes_write)를 참조하십시오.

## 백업 암호화 
{: #encrypting_backups}

{{site.data.keyword.objectstorageshort}} 인스턴스의 데이터를 암호화합니다.

1.  <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="외부 링크 아이콘"></a>를 다운로드하여 암호화 키를 작성하십시오.
2.  로컬 드라이브에 암호화 키를 작성하십시오. ENTER를 눌러 기본값을 사용할 수 있습니다.

    **주의:** 작성하는 비밀번호 문구를 기록해 두십시오. 비밀번호 문구를 잃어버린 경우 키로 암호화된 정보는 복호화할 수 없습니다. 

    ```
    gpg --gen-key
    ```
    {: pre}

    <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="외부 링크 아이콘"></a>의 버전에 따라 명령에서 `gpg`가 아니라 `gpg2`를 사용해야 할 수도 있습니다.

3.  키를 확인하십시오.

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

4.  `sub` 키에서 값과 함께 암호화 키를 내보내십시오. 파일의 이름을 encryption.asc로 지정하십시오.

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    이 예제에서 하위 키에는 값 *YYYYYYYY*가 있습니다.

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  로컬 디렉토리에서 암호화된 백업 컨테이너의 환경 변수 파일을 작성하십시오.

    ```
    touch <encryption_env-file_name>
    ```
    {: pre}

6.  환경 변수 파일을 편집하고 다음 필드를 추가하십시오. 비어 있는 환경 변수의 경우, 이전에 기록해 둔 {{site.data.keyword.objectstorageshort}} 신임 정보에서 값을 입력하십시오. 신임 정보에 사용된 따옴표는 사용하지 마십시오. *ENCRYPTION_PASSPHRASE*의 경우, 백업을 비밀번호로 보호할 비밀번호 문구를 포함하십시오. 이 비밀번호 문구는 암호화 키를 작성할 때 만든 문구와 다른 것입니다. 데이터를 백업하고 복원할 때 이 비밀번호 문구를 포함해야 합니다.

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

    이 설정은 *<volume_name>* 이름을 사용하여 암호화된 매일 증분 백업을 작성합니다. 다른 설정으로 백업을 작성하려면 [환경 변수 옵션](#reference_backup_restore)의 전체 목록을 검토하십시오.

7.  {{site.data.keyword.containerlong_notm}} CLI에 로그인하십시오.

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

8.  백업할 마운트된 볼륨과 함께 **ibm-backup-restore** 이미지에서 컨테이너를 실행하십시오. 백업 스크립트를 시작할 명령을 포함하십시오.

    -   <em>&lt;encryption_env-file_name&gt;</em>과 동일한 로컬 디렉토리에 있는지 확인하십시오.
    -   볼륨이 마운트되는 디렉토리는 환경 변수 파일에서 BACKUP_DIRECTORY와 일치해야 합니다.
    -   볼륨 이름을 백업할 볼륨 이름으로 대체하십시오.
   
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<encrypted_env-file_name> registry.ng.bluemix.net/ibm-backup-restore
    ```
    {: pre}

    컨테이너가 실행을 시작하지 않으면 `bx ic logs <container_name>`를 실행하여 로그에서 오류 메시지를 검토하십시오.

9.  **ibm-backup-restore** 이미지에서 빌드된 컨테이너의 /backup_restore 디렉토리에 암호화 키를 복사하십시오.

    ```
    bx ic cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    데이터를 복호화하려면 암호화 키의 사본을 로컬로 보관하십시오. 

10. 컨테이너에 로그인하십시오.

    ```
    bx ic exec -it <container_name> bash
    ```
    {: pre}

11. *encryption.asc*가 backup_restore 폴더로 복사되었는지 확인하십시오.

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

12. backup_restore 폴더에서 백업 스크립트를 실행하십시오.

    ```
    ./vbackup &
    ```
    {: codeblock}

13. 백업이 암호화되었는지 확인하려면 {{site.data.keyword.objectstorageshort}} 인스턴스의 파일을 검토하십시오. 이제 파일 이름의 끝에 `.gpg`가 추가되어 있습니다.![{{site.data.keyword.objectstorageshort}} GUI에서는 백업된 모들 파일에 .gpg가 추가되어 해당 파일이 암호화되었음을 보여줍니다.](images/volume_backup_encrypt_screenshot.png)

백업이 암호화되었습니다. 파일을 복원하려면 [복원 스크립트 실행](#restore_script_cli)의 단계를 따르고 복원 프로세스를 실행 중인 컨테이너의 backup_restore 디렉토리에 encryption.asc 파일을 포함시키십시오. 백업이 암호화된 경우 복원 컨테이너를 작성할 때 환경 변수로 ENCRYPTION_REQUIRED 및 ENCRYPTION_PASSPHRASE를 제공해야 합니다.

## 환경 변수 참조 
{: #reference_backup_restore}

환경 변수로 전달할 수 있거나 실행 중인 컨테이너의 `config.conf` 파일에서 편집할 수 있는 필드의 전체 목록을 검토합니다. 환경 변수로 전달된 값은 `config.conf`파일의 값을 대체합니다. 컨테이너에 대한 환경 변수를 검토하려면 `exec`를 사용하여 컨테이너에 로그인하고 `env`를 실행하십시오.

|키|값 옵션|
|---|-------------|
|PROJECTID|{{site.data.keyword.objectstorageshort}}의 프로젝트 ID|
|REGION|{{site.data.keyword.objectstorageshort}}의 지역|
|USERID|{{site.data.keyword.objectstorageshort}}의 사용자 ID|
|PASSWORD|{{site.data.keyword.objectstorageshort}}의 비밀번호|
{: caption="표 1. {{site.data.keyword.objectstorageshort}} 변수" caption-side="top"}

{{site.data.keyword.objectstorageshort}} 신임 정보에서 이러한 변수를 검색합니다. 백업 또는 복원 오퍼레이션을 실행 중인 모든 컨테이너에 이러한 환경 변수를 포함합니다.

|키|값 옵션|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: 기본값. 볼륨이 마운트되는 디렉토리의 절대 파일 경로입니다. 데이터가 이 디렉토리에서 백업됩니다. 해당 디렉토리가 백업 및 복원 프로세스를 위한 파일을 포함하므로 backup_restore 디렉토리를 선택하지 마십시오.|
|BACKUP_NAME|*volume_backup*: 기본값. 백업 이름을 선택하십시오.|
|BACKUP_TYPE|*full*: 기본값. 모든 파일을 항상 백업합니다.<br/> *incremental*: 새 파일이나 변경된 파일만 백업합니다. *incremental*을 선택하면 SCHEDULING_INFO 및 SCHEDULING_TYPE의 값을 선택해야 합니다.|
|SCHEDULE_TYPE|*none*: 기본값. 일회성 백업을 작성합니다.<br/> *periodic*: 스케줄된 백업을 작성하려면 값을 periodic으로 변경하십시오.|
|SCHEDULE_INFO|*hourly*: 시간별 백업을 작성합니다.<br/>*daily*: 기본값. 매일 백업을 작성합니다.<br/>*weekly*: 매주 백업을 작성합니다. 주기적 업데이트를 스케줄링하는 경우 이 변수를 포함해야 합니다.|
|EXCLUDE_DIRECTORIES|*none*: 기본값. 백업에서 제외할 디렉토리의 절대 파일 경로를 포함합니다. 쉼표로 디렉토리를 구분하십시오.|
{: caption="표 2. 백업 변수" caption-side="top"}

|키|값 옵션|
|---|-------------|
|BACKUP_NAME|*volume_backup*: 기본값. {{site.data.keyword.objectstorageshort}}에서 복원할 백업의 이름을 포함합니다.|
|RESTORE_DIRECTORY|*/backup*: 기본값. 볼륨이 마운트되는 절대 디렉토리입니다. 데이터가 이 디렉토리로 복원됩니다. 해당 디렉토리가 백업 및 복원 프로세스를 위한 파일을 포함하므로 backup_restore 디렉토리를 선택하지 마십시오.|
{: caption="표 3. 복원 변수" caption-side="top"}

|키|값 옵션|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: 기본값. 암호화 키의 파일 이름을 변경하거나 키가 backup_restore이 아닌 다른 디렉토리에 위치한 경우 이 환경 변수를 포함하십시오.|
|ENCRYPTION_REQUIRED|*no*: 기본값. <br/> *yes*: 백업을 암호화하지 않는 경우 암호화 환경 변수를 포함하지 마십시오. 백업을 암호화하고 있는 경우 이 키와 yes 값을 포함하십시오.|
|ENCRYPTION_PASSPHRASE|백업을 보호할 비밀번호 문구를 포함합니다. 이 비밀번호 문구는 암호화 키를 작성할 때 만든 문구와 다른 것입니다. 데이터를 백업하고 복원할 때 이 비밀번호 문구를 포함해야 합니다.|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: 기본값. <br/> *yes*: 컨테이너에서 직접 암호화 키를 생성한 경우 이 환경 변수를 yes로 포함하십시오. 대부분 사용자는 로컬 컴퓨터에 키를 생성하고 키를 컨테이너로 복사하므로 기본값인 no를 사용할 수 있습니다.|
{: caption="표 4. 암호화 변수" caption-side="top"}

