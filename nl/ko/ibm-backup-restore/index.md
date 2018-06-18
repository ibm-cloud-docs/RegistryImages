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

# ibm-backup-restore 이미지 시작하기
{: #ibmbackup_restore_starter}

**ibm-backup-restore** 이미지에는 {{site.data.keyword.containerlong}}에서 지속적 스토리지를 백업하고 복원하는 데 필요한 사전 설치된 패키지가 포함됩니다.
{:shortdesc}

## 작동 방식 
{: #how_it_works}

**ibm-backup-restore** 이미지를 사용하여 지속적 볼륨 클레임(pvc)에 대해 일회성 백업 또는 스케줄된 백업을 작성할 수 있습니다. 백업은 {{site.data.keyword.objectstoragefull}} 인스턴스 내에 저장됩니다. 실행중인 컨테이너의 `config.conf` 파일을 편집하여 {{site.data.keyword.objectstorageshort}} 신임 정보를 **ibm-backup-restore** 컨테이너에 환경 변수로 전달할 수 있습니다. 또한 {{site.data.keyword.objectstorageshort}} 인스턴스에서 볼륨으로 저장된 데이터를 복구할 수 있습니다. 이미지에는 백업 및 복원을 둘 다 실행하기 위한 스크립트가 포함되므로 사용자가 명령을 입력하여 적절한 스크립트를 시작해야 합니다.

## 포함된 항목 
{: #whats_included}

모든 **ibm-backup-restore** 이미지는 다음 소프트웨어 패키지를 포함합니다.

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   python, gnupg 및 wget 패키지

## 시작하기 
{: #how_to_get_started}

데이터를 백업하고 복원하려면 다음 태스크를 검토하십시오.
1.  [{{site.data.keyword.objectstorageshort}} 서비스 인스턴스 작성](#object_storage)
2.  [스케줄된 백업 실행](#scheduled_backup)
3.  [복원 스크립트 실행](#restore_script_cli)
4.  [백업 암호화](#encrypting_backups)
5.  [환경 변수 참조](#reference_backup_restore)

## {{site.data.keyword.objectstorageshort}} 서비스 인스턴스 작성 
{: #object_storage}

{{site.data.keyword.objectstorageshort}} 인스턴스를 작성하여 볼륨 백업을 위한 저장소를 제공합니다.

1.  {{site.data.keyword.Bluemix_notm}} 카탈로그의 **스토리지** 섹션에서 {{site.data.keyword.objectstorageshort}} 인스턴스를 프로비저닝하십시오.
2.  {{site.data.keyword.objectstorageshort}}를 클릭하십시오.
3.  {{site.data.keyword.Bluemix_notm}}용 {{site.data.keyword.objectstorageshort}} OpenStack Swift를 선택하십시오. 그런 다음 저장을 클릭하십시오.
3.  **서비스 신임 정보** 탭을 클릭하십시오.
4.  **새 신임 정보**를 클릭하십시오.
5.  이름 필드를 채우고 나머지 필드를 공백으로 두십시오. **추가**를 클릭하십시오.
6.  이제 새 신임 정보가 **서비스 신임 정보** 표에 나열됩니다. **신임 정보 보기**를 클릭합니다.
7.  **프로젝트 ID**, **지역**, **사용자 ID** 및 **비밀번호**를 기록하십시오. 이 신임 정보는 **ibm-backup-restore** 컨테이너가 {{site.data.keyword.objectstorageshort}} 인스턴스에 액세스하는 데 필요합니다.

인스턴스 구성에 대한 자세한 정보는 [{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) 문서를, {{site.data.keyword.objectstorageshort}} 가격 책정에 대한 정보는 [플랜 옵션](../../ObjectStorage/objectstorage_faq.html#account-payment)을 검토하십시오.

## 스케줄된 백업 실행 
{: #scheduled_backup}

**ibm-backup-restore** 이미지에서 컨테이너 팟(Pod)을 작성하고 정기적으로 스케줄된 백업을 시작합니다.

시작하기 전에 다음을 수행하십시오.

-   필수 [CLI](../../../containers/cs_cli_install.html#cs_cli_install)를 설치하십시오.
-   클러스터를 [CLI의 대상으로 지정](../../../containers/cs_cli_install.html#cs_cli_configure)하십시오.


1. _backup-pvc.yaml_로 이름 지정된 구성 파일을 작성하십시오. 이 구성 파일은 백업 팟(Pod)에 볼륨으로 마운트할 수 있는 지속적 볼륨 클레임(pvc)을 작성합니다.

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
    
2. pvc를 작성하십시오.

    ```
        kubectl apply -f backup-pvc.yaml 
    ```
    {: pre}

3.  _backup.yaml_로 이름 지정된 구성 파일을 작성하십시오. 비어 있는 환경 변수의 경우, 이전에 기록해 둔 {{site.data.keyword.objectstorageshort}} 신임 정보에서 값을 입력하십시오. 신임 정보에 사용된 따옴표를 포함시키십시오. <em>&lt;pod_name&gt;</em>, <em>&lt;container_name&gt;</em> 및 이미지 레지스트리 <em>&lt;region&gt;</em>을 적절한 값으로 대체하십시오.

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
    
    이 설정은 기본 이름 _mybackup_으로 일별 증분식 백업을 작성합니다. 다른 설정으로 백업을 작성하려면 [환경 변수 옵션](#reference_backup_restore)의 전체 목록을 검토하십시오.

4.  백업 스크립트를 실행하는 팟(Pod)을 작성하십시오.

    ```
        kubectl apply -f backup.yaml
    ```
    {: pre}

5.  팟(Pod)이 실행 중인지 확인하십시오.

    ```
        kubectl get pods 
    ```
    {: pre}
    
    ```
        NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

4.  {{site.data.keyword.Bluemix_notm}} GUI의 {{site.data.keyword.objectstorageshort}}에서 백업을 검토하십시오.
    1.  백업을 위해 작성한 {{site.data.keyword.objectstorageshort}} 인스턴스를 클릭하십시오.
    2.  **컨테이너** 테이블의 **관리** 탭에서 {{site.data.keyword.objectstorageshort}} 컨테이너를 클릭하십시오.
    3.  압축된 파일을 검토하십시오.![{{site.data.keyword.Bluemix_notm}} GUI의 Object Storage 컨테이너는 백업된 파일을 보여줍니다.](images/volume_backup_screenshot.png)vol1.difftar.gz 파일을 다운로드하고 파일을 추출하여 백업된 데이터를 확인할 수 있습니다. **중요**: {{site.data.keyword.objectstorageshort}}의 파일을 삭제하거나 수정한 경우 해당 파일을 복구할 수 없습니다.

백업을 사용할 수 있습니다. 일회성 전체 백업을 작성하도록 백업을 구성한 경우 새 백업을 작성할 때마다 백업 스크립트를 실행해야 합니다. 증분식 백업을 정기적으로 실행하도록 컨테이너를 구성한 경우 백업이 예정대로 실행됩니다.

## 복원 스크립트 실행 
{: #restore_script_cli}

{{site.data.keyword.objectstorageshort}}의 백업을 기존 볼륨 또는 새 볼륨에 복원합니다.

시작하기 전에 다음을 수행하십시오.

-   필수 [CLI](../../../containers/cs_cli_install.html#cs_cli_install)를 설치하십시오.
-   클러스터를 [CLI의 대상으로 지정](../../../containers/cs_cli_install.html#cs_cli_configure)하십시오.


1. _restore-pvc.yaml_로 이름 지정된 구성 파일을 작성하십시오. 이 구성 파일은 복원 팟(Pod)에 볼륨으로 마운트할 수 있는 지속적 볼륨 클레임(pvc)을 작성합니다.

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
    
2. pvc를 작성하십시오.

    ```
        kubectl apply -f restore-pvc.yaml 
    ```
    {: pre}

3.  _restore.yaml_로 이름 지정된 구성 파일을 작성하십시오. 비어 있는 환경 변수의 경우, 이전에 기록해 둔 {{site.data.keyword.objectstorageshort}} 신임 정보에서 값을 입력하십시오. 신임 정보에 사용된 따옴표를 포함시키십시오. BACKUP_NAME은 복원할 {{site.data.keyword.objectstorageshort}}의 백업 이름과 일치해야 합니다.

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

4.  복원 스크립트를 실행하는 팟(Pod)을 작성하십시오.
    
    ```
        kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  팟(Pod)이 실행 중인지 확인하십시오.

    ```
        kubectl get pods 
    ```
    {: pre}
    
    ```
        NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    팟(Pod)이 복원 명령을 실행하고 중지됩니다. _CrashLoopBackOff_ 메시지는 Kubernetes가 팟(Pod)을 다시 시작하려고 시도 중임을 의미합니다.

6.  팟(Pod)이 더 많은 자원을 이용하지 못하도록 팟(Pod)을 제거하십시오.

    ```
        kubectl delete -f restore.yaml
    ```
    {: pre}

성공적으로 백업을 복원했습니다. 이제 컨테이너가 복원된 파일에 액세스할 수 있도록 새 팟(Pod)을 pvc에 마운트할 수 있습니다. 백업된 컨테이너 데이터에 비루트 사용자가 포함된 경우 새 컨테이너에 비루트 권한을 추가해야 합니다. 자세한 정보는 [볼륨에 대한 비루트 사용자 액세스 권한 추가](../../../containers/container_volumes_ov.html#container_volumes_write)를 참조하십시오.

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
    
6. _backup-pvc.yaml_로 이름 지정된 구성 파일을 작성하십시오. 이 구성 파일은 백업 팟(Pod)에 볼륨으로 마운트할 수 있는 지속적 볼륨 클레임(pvc)을 작성합니다.

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

7. pvc를 작성하십시오.

    ```
        kubectl apply -f backup-pvc.yaml 
    ```
    {: pre}

8.  배치 구성 파일을 편집하고 다음 필드를 추가하십시오. 비어 있는 환경 변수의 경우, 이전에 기록해 둔 {{site.data.keyword.objectstorageshort}} 신임 정보에서 값을 입력하십시오. 신임 정보에 사용된 따옴표를 포함시키십시오. *ENCRYPTION_PASSPHRASE*의 경우, 백업을 비밀번호로 보호할 비밀번호 문구를 포함하십시오. 이 비밀번호 문구는 암호화 키를 작성할 때 만든 문구와 다른 것입니다. 데이터를 백업하고 복원할 때 이 비밀번호 문구를 포함해야 합니다.

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
   
이 설정은 <em>&lt;volume_name&gt;</em> 이름을 사용하여 암호화된 매일 증분 백업을 작성합니다. 다른 설정으로 백업을 작성하려면 [환경 변수 옵션](#reference_backup_restore)의 전체 목록을 검토하십시오.
    
9.  백업 스크립트를 실행하는 팟(Pod)을 작성하십시오.

    ```
    kubectl apply -f backup.yaml 
    ```
    {: pre}

9.  팟(Pod)이 실행 중인지 확인하십시오.

    ```
        kubectl get pods
    ```
    {: pre}
    
    ```
        NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

10.  **ibm-backup-restore** 이미지에서 빌드된 컨테이너의 /backup_restore 디렉토리에 암호화 키를 복사하십시오.

    ```
        kubectl cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    데이터를 복호화하려면 암호화 키의 사본을 로컬로 보관하십시오.

11. 컨테이너에 로그인하십시오.

    ```
        kubecl exec -it <container_name> bash
    ```
    {: pre}

12. *encryption.asc*가 backup_restore 폴더로 복사되었는지 확인하십시오.

    ```
        root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

13. backup_restore 폴더에서 백업 스크립트를 실행하십시오.

    ```
        ./vbackup &
    ```
    {: codeblock}

13. 백업이 암호화되었는지 확인하려면 {{site.data.keyword.objectstorageshort}} 인스턴스의 파일을 검토하십시오. 이제 파일의 파일 이름 끝에 `.gpg`가 추가됩니다. ![Object Storage GUI에 암호화되었음을 나타내는 .gpg 가 추가된 모든 백업 파일이 표시됩니다.](images/volume_backup_encrypt_screenshot.png)

백업이 암호화되었습니다. 파일을 복원하려면 [복원 스크립트 실행](#restore_script_cli)의 단계를 따르고 복원 프로세스를 실행 중인 컨테이너의 backup_restore 디렉토리에 encryption.asc 파일을 포함시키십시오. 백업이 암호화된 경우 복원 컨테이너를 작성할 때 백업 yaml 파일의 ENCRYPTION_REQUIRED 및 ENCRYPTION_PASSPHRASE를 환경 변수로 제공해야 합니다.

## 환경 변수 참조 
{: #reference_backup_restore}

환경 변수로 전달할 수 있거나 실행 중인 컨테이너의 `config.conf` 파일에서 편집할 수 있는 필드의 전체 목록을 검토합니다. 환경 변수로 전달된 값은 `config.conf` 파일의 값을 대체합니다. 컨테이너에 대한 환경 변수를 검토하려면 `exec`를 사용하여 컨테이너에 로그인하고 `env`를 실행하십시오.

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
|BACKUP_TYPE|*full*: 기본값. 모든 파일을 항상 백업합니다.<br/> *incremental*: 새 파일이나 변경된 파일만 백업합니다. *incremental*을 선택하는 경우 SCHEDULING_INFO 및 SCHEDULING_TYPE의 값을 선택해야 합니다.||
|SCHEDULE_TYPE|*none*: 기본값. 일회성 백업을 작성합니다.<br/> **참고:** 일회성 백업을 작성하도록 선택한 경우 백업이 완료되면 팟(Pod)이 클러스터에서 제거됩니다. <br/>*periodic*: 값을 주기적으로 변경하려면 스케줄된 백업을 작성하십시오.|
|SCHEDULE_INFO|*hourly*: 시간별 백업을 작성합니다.<br/>*daily*: 기본값. 매일 백업을 작성합니다.<br/>*weekly*: 주별 백업을 작성합니다. 주기적 업데이트를 스케줄링하는 경우 이 변수를 포함해야 합니다.|
|EXCLUDE_DIRECTORIES|*none*: 기본값. 백업에서 제외할 디렉토리의 절대 파일 경로를 포함합니다. 쉼표로 디렉토리를 구분하십시오.|
{: caption="표 2. 백업 변수" caption-side="top"}

|키|값 옵션|
|---|-------------|
|BACKUP_NAME|*volume_backup*: 기본값. {{site.data.keyword.objectstorageshort}}에서 복원할 백업의 이름을 포함합니다.|
|RESTORE_DIRECTORY|*/backup*: 기본값. 볼륨이 마운트되는 절대 디렉토리입니다. 데이터가 이 디렉토리로 복원됩니다. 해당 디렉토리가 백업 및 복원 프로세스를 위한 파일을 포함하므로 backup_restore 디렉토리를 선택하지 마십시오.|
{: caption="표 3. 복원 변수" caption-side="top"}

|키|값 옵션|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: 기본값. 암호화 키의 파일 이름을 변경하거나 키가 backup_restore가 아닌 디렉토리에 있는 경우 이 환경 변수를 포함하십시오.|
|ENCRYPTION_REQUIRED|*no*: 기본값.<br/> *yes* 백업을 암호화하지 않는 경우 암호화 환경 변수를 포함하지 마십시오. 백업을 암호화하는 경우 이 키와 yes 값을 포함하십시오.|
|ENCRYPTION_PASSPHRASE|백업의 보안을 설정하기 위한 비밀번호 문구를 포함합니다. 이 비밀번호 문구는 암호화 키를 작성할 때 만든 문구와 다른 것입니다. 데이터를 백업하고 복원할 때 이 비밀번호 문구를 포함해야 합니다.|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: 기본값.<br/> *yes*: 컨테이너에서 직접 암호화 키를 생성한 경우 이 환경 변수를 yes로 포함하십시오. 대부분의 사용자는 로컬 컴퓨터에 키를 생성하고 키를 컨테이너로 복사하므로 기본값을 no로 유지할 수 있습니다.|
{: caption="표 4. 암호화 변수" caption-side="top"}

## 단일 및 확장 가능 컨테이너에서 Kubernetes로 볼륨 데이터 마이그레이션
{: #migrate_data}

컨테이너 볼륨에 대한 일회성 백업을 작성합니다. 백업은 {{site.data.keyword.objectstoragefull}} 인스턴스 내에 저장됩니다. 그런 다음 Kubernetes의 지속적 볼륨 클레임으로 데이터를 마이그레이션할 수 있습니다.
{:shortdesc}

### 시작하기 
{: #how_to_get_started_migrating}

시작하기 전에 다음을 수행하십시오.

- [앱을 Kubernetes로 이동하기 위한 전체 마이그레이션 경로 검토](../../../containers/cs_classic.html)
- [단일 및 확장 가능 컨테이너 CLI 설치(bx ic)](../../../containers/container_cli_cfic_install.html)
- [{{site.data.keyword.containershort}}CLI 설치(bx cs and kubectl)](../../../containers/cs_cli_install.html#cs_cli_install)
- [데이터를 마이그레이션할 표준 Kubernetes 클러스터 작성](../../../containers/cs_clusters.html#clusters_cli)

백업 및 복원 오퍼레이션을 수행하려면 다음 태스크를 완료하십시오.
1.  [{{site.data.keyword.objectstorageshort}} 서비스 인스턴스 작성](#object_storage)(이전에 처리됨)
2.  [일회성 백업 실행](#migrate_backup)
3.  [Kubernetes에서 복원 스크립트 실행](#migrate_restore)

### 일회성 백업 실행 
{: #migrate_backup}

**ibm-backup-restore** 이미지에서 단일 컨테이너를 작성하고 백업을 시작합니다.

1.  {{site.data.keyword.containershort}} CLI에 로그인하십시오.

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

3.  환경 변수 파일을 편집하고 다음 필드를 추가하십시오. 비어 있는 환경 변수의 경우, 이전에 기록해 둔 {{site.data.keyword.objectstorageshort}} 신임 정보에서 값을 입력하십시오. 신임 정보에 사용된 따옴표는 포함시키지 **마십시오**.

    ```
    BACKUP_NAME=volume_backup
    BACKUP_DIRECTORY=/backup
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    ```
    {: codeblock}

    이 설정은 기본 이름 _volume_backup_으로 일회성 백업을 작성합니다.

4.  백업할 마운트된 볼륨과 함께 **ibm-backup-restore** 이미지에서 컨테이너를 실행하십시오. 백업 스크립트를 시작할 명령을 포함하십시오.

    -   <em>&lt;backup_env-file&gt;</em>과 동일한 로컬 디렉토리에 있는지 확인하십시오.
    -   볼륨이 마운트되는 디렉토리는 환경 변수 파일에서 BACKUP_DIRECTORY와 일치해야 합니다.
    -   <em>&lt;volume_name&gt;</em>을 백업할 볼륨의 이름으로 대체하십시오.
    
    ```
        bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<backup_env-file_name> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    백업이 실행된 후 컨테이너가 종료됩니다. 컨테이너 실행이 시작되지 않으면 `bx ic logs<container_name>`를 실행하여 로그에서 오류 메시지를 검토하십시오.

5.  {{site.data.keyword.Bluemix_notm}} GUI의 {{site.data.keyword.objectstorageshort}}에서 백업을 검토하십시오.
    1.  백업을 위해 작성한 {{site.data.keyword.objectstorageshort}} 인스턴스를 클릭하십시오.
    2.  {{site.data.keyword.objectstorageshort}} 컨테이너를 클릭하십시오. 이 예제에서 컨테이너 이름은 volume_backup입니다.
    3.  압축 파일을 검토하십시오.![{{site.data.keyword.Bluemix_notm}} GUI의 오브젝트 스토리지 컨테이너에 백업된 파일이 표시됩니다.](images/volume_backup_screenshot.png)difftar.gz 파일을 다운로드하고 파일을 추출하고 백업된 데이터를 확인할 수 있습니다. **중요**: {{site.data.keyword.objectstorageshort}}에서 파일을 삭제하거나 수정하는 경우 해당 파일을 복구할 수 없습니다.

### Kubernetes 클러스터로 데이터 복원 
{: #migrate_restore}

{{site.data.keyword.objectstorageshort}}에서 Kubernetes 클러스터의 지속적 볼륨 클레임으로 백업을 복원하십시오.

시작하기 전에 다음을 수행하십시오.

- 클러스터를 [CLI의 대상으로 지정](../../../containers/cs_cli_install.html#cs_cli_configure)하십시오.


1. _restore-pvc.yaml_로 이름 지정된 구성 파일을 작성하십시오. 이 구성 파일은 백업 팟(Pod)에 볼륨으로 마운트할 수 있는 지속적 볼륨 클레임(pvc)을 작성합니다.

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

2. pvc를 작성하십시오.

    ```
        kubectl apply -f restore-pvc.yaml 
    ```
    {: pre}

3.  _restore.yaml_로 이름 지정된 구성 파일을 작성하십시오. 비어 있는 환경 변수의 경우, 이전에 기록해 둔 {{site.data.keyword.objectstorageshort}} 신임 정보에서 값을 입력하십시오. 신임 정보에 사용된 따옴표를 포함시키십시오. BACKUP_NAME은 복원할 {{site.data.keyword.objectstorageshort}}의 백업 이름과 일치해야 합니다.

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

4.  복원 스크립트를 실행하는 팟(Pod)을 작성하십시오.

    ```
        kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  팟(Pod)이 실행되는지 확인하십시오.

    ```
        kubectl get pods 
    ```
    {: pre}
    
    ```
        NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}
    
    팟(Pod)이 복원 명령을 실행하고 중지됩니다. _CrashLoopBackOff_ 메시지는 Kubernetes가 팟(Pod)을 다시 시작하려고 시도 중임을 의미합니다.

6.  팟(Pod)이 더 많은 자원을 이용하지 못하도록 팟(Pod)을 제거하십시오.

    ```
        kubectl delete -f restore.yaml
    ```
    {: pre}

Kubernetes 클러스터로 데이터를 마이그레이션했습니다. 이제 팟(Pod)이 복원된 파일에 액세스할 수 있도록 새 팟(Pod)을 pvc에 마운트할 수 있습니다. 

**참고**: 백업된 컨테이너 데이터에 비루트 사용자가 포함된 경우 새 컨테이너에 비루트 권한을 추가해야 합니다. 자세한 정보는 [볼륨에 대한 비루트 사용자 액세스 권한 추가](../../../containers/container_volumes_ov.html#container_volumes_write)를 참조하십시오.
