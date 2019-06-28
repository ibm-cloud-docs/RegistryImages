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

# `ibmcloud-backup-restore` 이미지 시작하기
{: #ibmbackup_restore_starter}

`ibmcloud-backup-restore` 이미지에는 {{site.data.keyword.containerlong}}에서 지속적 스토리지를 백업하고 복원하는 데 필요한 사전 설치된 패키지가 포함됩니다.
{:shortdesc}

`ibmcloud-backup-restore` 이미지는 `icr.io` 레지스트리 도메인에서만 사용할 수 있으며 `registry.bluemix.net` 도메인에서는 더 이상 사용되지 않습니다. 이미지에 액세스하고 이미지에서 컨테이너를 배치하려면 클러스터가 {{site.data.keyword.registryshort_notm}}에 대한 액세스 권한을 부여하기 위해 이미지 풀 시크릿에 API 키를 저장해야 합니다. 2019년 2월 25일 이전에 작성된 클러스터의 경우 `ibmcloud-backup-restore` 이미지를 사용할 수 있게 `icr.io` 레지스트리 도메인에 액세스할 수 있도록 클러스터를 업데이트해야 합니다. 변경된 내용과 클러스터를 업데이트하는 방법에 대한 자세한 정보는 [레지스트리에서 이미지를 가져올 수 있는 권한을 클러스터에 부여하는 방법 이해](/docs/containers?topic=containers-images#cluster_registry_auth) 및 [API 키 이미지 풀 시크릿을 사용하도록 기존 클러스터 업데이트](/docs/containers?topic=containers-images#imagePullSecret_migrate_api_key)를 참조하십시오. 
{: important}

명령행을 사용하여 {{site.data.keyword.IBM_notm}}에서 제공한 이미지에 액세스할 수 있습니다. [IBM 공용 이미지](/docs/services/Registry?topic=registry-public_images#public_images)를 참조하십시오.
{: tip}

## 작동 방식
{: #backup_restore_how_it_works}

`ibmcloud-backup-restore` 이미지를 사용하여 클러스터의 PV(Persistent Volume)에 저장된 앱 데이터에 대한 일회성 백업 또는 스케줄된 백업을 작성할 수 있거나 앱 데이터를 PV에 복원할 수 있습니다. 데이터를 백업하고 복원하려면 `ibmcloud-backup-restore` 이미지에서 팟(Pod)을 배치하십시오. 그런 다음 백업할 PV 또는 데이터를 팟(Pod)에 복원하기 위해 사용할 PV를 바인드하는 PVC를 마운트하십시오.

**내 데이터의 이동 위치와 액세스 방법은 무엇입니까?**

백업한 데이터는 {{site.data.keyword.cos_full_notm}} 서비스 인스턴스에 저장됩니다. 서비스에 액세스하려면 `ibmcloud-backup-restore` 팟(Pod)에서 환경 변수로 {{site.data.keyword.cos_full_notm}} 서비스 인증 정보를 사용하거나 실행 중인 팟(Pod)에서 `config.conf` 파일을 편집하십시오.

**백업된 데이터를 다른 앱 또는 다른 PV에 복원할 수 있습니까?**

예, {{site.data.keyword.cos_full_notm}} 서비스 인스턴스에서 클러스터의 PV로 저장된 데이터를 복원할 수 있습니다. 데이터를 복원하려면 `ibmcloud-backup-restore` 이미지에서 복원 팟(Pod)을 작성해야 합니다. 그런 다음 팟(Pod)에 사용할 PV를 바인드하는 PVC를 마운트하십시오.  

## 포함된 항목
{: #backup_restore_whats_included}

모든 `ibmcloud-backup-restore` 이미지에는 다음 소프트웨어 패키지가 포함됩니다.

- Alpine 3.7
- Duplicity 0.7.10
- Python 및 GnuPG Made Easy(GPGme) 패키지

## {{site.data.keyword.cos_full_notm}} 서비스 인스턴스 설정
{: #backup_restore_setup_object_storage}

백업할 데이터를 위해 데이터의 저장소로 제공할 {{site.data.keyword.cos_full_notm}} 서비스 인스턴스를 작성하고 구성하십시오.
{: shortdesc}

1. {{site.data.keyword.cos_full_notm}} 서비스 인스턴스를 배치하십시오.
   1. [{{site.data.keyword.cloud_notm}} 카탈로그 ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://cloud.ibm.com/catalog/services/cloud-object-storage)를 여십시오.
   2. `cos-backup`과 같이 서비스 인스턴스의 이름을 입력하고 리소스 그룹으로 **기본값**을 선택하십시오.
   3. 가격 정보를 보려면 [plan options![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.ibm.com/cloud-computing/bluemix/pricing-object-storage#s3api)를 검토하고 플랜을 선택하십시오.
   4. **작성**을 클릭하십시오.
2. {{site.data.keyword.cos_full_notm}} 서비스 인스턴스 인증 정보를 검색하십시오.
   1. 서비스 세부사항 페이지의 탐색에서 **서비스 인증 정보**를 클릭하십시오.
   2. **새 인증 정보**를 클릭하십시오. 대화 상자가 표시됩니다.
   3. 인증 정보의 이름을 입력하십시오.
   4. **인라인 구성 매개변수 추가(선택사항)** 필드에 `{"HMAC":true}`를 입력하여 `ibmcloud-backup-restore` 팟(Pod)이 {{site.data.keyword.cos_full_notm}} 서비스에 대한 HMAC 인증에 사용하는 추가 HMAC 인증 정보를 작성하십시오.
   5. **추가**를 클릭하십시오. 새 인증 정보가 **서비스 인증 정보** 표에 나열됩니다.
   6. **인증 정보 보기**를 클릭하십시오.
   7. **cos_hmac_keys** 섹션에서 찾을 수 있는 **access_key_id** 및 **secret_access_key**를 기록해 두십시오.
3. 첫 번째 {{site.data.keyword.cos_full_notm}} 버킷을 작성하십시오.
   1. 서비스 세부사항 페이지의 탐색에서 **버킷**을 클릭하십시오.
   2. **버킷 작성**을 클릭하십시오. 대화 상자가 표시됩니다.
   3. 버킷의 고유한 이름을 입력하십시오. 이 이름은 모든 지역 및 모든 {{site.data.keyword.cloud_notm}} 계정의 {{site.data.keyword.cos_full_notm}} 내에서 고유해야 합니다.
   4. **복원** 목록에서 데이터에 설정할 가용성의 레벨을 선택하십시오. 자세한 정보는 [{{site.data.keyword.cos_full_notm}} 지역 및 엔드포인트](/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-endpoints#endpoints)를 참조하십시오.
   5. 데이터를 저장할 지역으로 **위치**를 변경하십시오. 법률적인 이유로 모든 지역에서 데이터를 저장하지 못할 수 있습니다.  
   6. **작성**을 클릭하십시오.
4. 버킷의 {{site.data.keyword.cos_full_notm}} 호스트 이름을 검색하십시오.
   1. 이전 단계에서 작성한 버킷 이름을 클릭하십시오.
   2. 서비스 세부사항 페이지의 탐색에서 **버킷** > **구성**을 클릭하십시오.
   3. 버킷의 데이터에 액세스하는 데 사용할 수 있는 공용 URL을 기록해 두십시오.

서비스 인스턴스 구성에 대한 자세한 정보는 [{{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage?topic=cloud-object-storage-about-ibm-cloud-object-storage) 문서를 검토하십시오.

## pv(persistent volume)에서 데이터 백업
{: #backup_restore_scheduled_backup}

pvc(persistent volume claim)를 통해 앱 팟(Pod)에 마운트되는 pv(persistent volume)에 대해 일회성 백업 또는 스케줄된 백업을 작성할 수 있습니다.  
{: shortdesc}

다음 예제에서는 `ibmcloud-backup-restore` 이미지에서 백업 팟(Pod)을 배치하고, PVC를 사용하여 기존 PV를 백업 팟(Pod)에 마운트하고, PV에서 {{site.data.keyword.cos_full_notm}} 서비스 인스턴스로 데이터를 백업하는 방법을 보여줍니다.  

**시작하기 전에**

- [Set up an {{site.data.keyword.cos_full_notm}} 서비스 인스턴스](#backup_restore_setup_object_storage).
- 필요한 [CLI](/docs/containers?topic=containers-cs_cli_install#cs_cli_install)를 설치하여 클러스터를 작성하고 클러스터 관련 작업을 수행하십시오.
- [표준 클러스터를 작성](/docs/containers?topic=containers-clusters#clusters_cli)하거나 기존 클러스터를 사용하십시오.
- [클러스터를 CLI의 대상으로 지정](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)하십시오.
- [File Storage](/docs/containers?topic=containers-file_storage#add_file) 또는 [Block Storage](/docs/containers?topic=containers-block_storage#add_block)의 PVC(Persistent Volume Claim)를 작성하고 앱 배치에 마운트하십시오.

기존 PV를 백업하려면 다음 단계를 완료하십시오.

1. 백업할 PV를 바인드하는 PVC의 이름을 가져오십시오.

   ```
   kubectl get pvc
   ```
   {: pre}

2. `ibmcloud-backup-restore` 이미지에서 백업 팟(Pod)을 작성하십시오. PV에서 데이터에 액세스하려면 PV를 백업 팟(Pod)에 바인드하는 PVC를 마운트해야 합니다. 다음 예제는 증분 백업을 실행하는 백업 팟(Pod)을 매일 작성합니다. 다른 설정으로 백업을 작성하려면 [환경 변수 옵션](#backup_restore_env_reference)의 전체 목록을 검토하십시오.

   `ibmcloud-backup-restore` 이미지를 단일 팟(Pod)에 배치해야 하고 Kubernetes 배치의 일부로 사용할 수 없습니다.
   {: important}

   이미지를 보려면 `ibmcloud cr region-set global` 명령을 실행하여 글로벌 레지스트리를 대상으로 지정하십시오. 그런 다음 `ibmcloud cr images --include-ibm`을 실행하여 IBM 공용 이미지를 나열하십시오.
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
   <caption>표 1. YAML 파일 컴포넌트</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Idea icon"/> YAML 파일 컴포넌트 이해</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>{{site.data.keyword.cos_full_notm}} 서비스 인스턴스 인증 정보의 일부로 검색한 액세스 키 ID입니다.</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</code></td>
     <td>{{site.data.keyword.cos_full_notm}} 서비스 인스턴스 인증 정보의 일부로 검색한 시크릿 액세스 키입니다.</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>특정 지역에서 {{site.data.keyword.cos_full_notm}}에 액세스하는 데 사용하는 지역 API 엔드포인트에 대한 URL입니다. </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>{{site.data.keyword.cos_full_notm}}에 백업을 저장하는 데 사용할 버킷의 이름입니다. </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>버킷에 백업을 보유하고 있는 오브젝트의 고유한 이름입니다. </td>
     </tr>
     <tr>
     <td><code>&lt;mount_path&gt;</code></td>
     <td>백업할 PVC를 마운트하는 백업 컨테이너 내부의 볼륨 마운트 경로입니다. 다중 PVC를 백업하려면 각 PVC마다 별도의 마운트 경로를 지정하십시오. <code>volumeMount.name</code>에서 사용하는 이름이 YAML 파일의 <code>volumes.name</code> 섹션에 지정하는 볼륨의 이름과 일치하는지 확인하십시오. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>백업할 PVC의 이름입니다. 다중 PVC를 백업하려면 백업할 각 PVC의 볼륨 이름과 해당 PVC를 지정하십시오. <code>volumes.name</code>에서 사용하는 이름이 YAML 파일의 <code>volumeMount.name</code> 섹션에 있는 이름과 일치하는지 확인하십시오.</td>
     </tr>
     </tbody>
     </table>

3. 백업 팟(Pod)을 작성하고 PV 데이터의 백업을 시작하십시오.

    ```
    kubectl apply -f backuppod.yaml
    ```
    {: pre}

4. 팟(Pod)이 실행 중인지 확인하십시오.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

5. 백업이 실행 완료되었는지 확인하십시오.

    ```
    kubectl logs backuppod
    ```
    {: pre}

6. {{site.data.keyword.cloud_notm}} GUI에서 {{site.data.keyword.cos_full_notm}}의 백업을 검토하십시오.
    1. {{site.data.keyword.cloud_notm}} 대시보드에서 {{site.data.keyword.cos_full_notm}} 서비스 인스턴스를 찾으십시오.
    2. 탐색에서 **버킷**을 선택하고 백업 구성에 사용한 버킷을 클릭하십시오. 백업은 버킷의 오브젝트로 표시됩니다.
    3. 압축된 파일을 검토하십시오. `vol1.difftar.gz` 파일을 다운로드하고, 파일을 추출하고, 백업된 데이터를 확인할 수 있습니다.

       {{site.data.keyword.cos_full_notm}}에서 파일을 삭제하거나 수정하는 경우 해당 파일을 복구할 수 없습니다.
       {: important}

백업을 사용할 수 있습니다. 일회성 전체 백업을 작성하도록 백업을 구성한 경우 새 백업을 작성할 때마다 백업 스크립트를 실행해야 합니다. 증분식 백업을 정기적으로 실행하도록 컨테이너를 구성한 경우 백업이 예정대로 실행됩니다.

## {{site.data.keyword.cos_full_notm}}에서 클러스터로 데이터 복원
{: #backup_restore_restore_script_cli}

{{site.data.keyword.cos_full_notm}} 서비스 인스턴스에서 클러스터의 PV로 데이터를 복원할 수 있습니다.

{{site.data.keyword.cos_full_notm}} 서비스 인스턴스에 전체 백업이 여러 개이면 마지막 전체 백업 데이터로 PVC가 복원됩니다. 증분식 백업이 있으면 복원을 시작한 날짜까지 증분식 백업을 모두 포함하여, 마지막 전체 백업 데이터로 PVC가 복원됩니다.

**시작하기 전에**

- [클러스터를 CLI의 대상으로 지정](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)하십시오.
- [클러스터의 PV에 대한 백업을 작성](#backup_restore_scheduled_backup)하십시오.

{{site.data.keyword.cos_full_notm}}에서 PV로 데이터를 복원하려면 다음 단계를 완료하십시오.

1. 데이터를 복원할 PV를 바인드하는 PVC의 이름을 가져오십시오.

   ```
   kubectl get pvc
   ```
   {: pre}

2. `ibmcloud-backup-restore` 이미지에서 복원 팟(Pod)을 작성하십시오. 데이터를 PV에 복원하려면 PV를 복원 팟(Pod)에 바인드하는 PVC를 마운트해야 합니다.
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
   <caption>표 2. YAML 파일 컴포넌트</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Idea icon"/> YAML 파일 컴포넌트 이해</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>{{site.data.keyword.cos_full_notm}} 서비스 인스턴스 인증 정보의 일부로 검색한 액세스 키 ID입니다.</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</code></td>
     <td>{{site.data.keyword.cos_full_notm}} 서비스 인스턴스 인증 정보의 일부로 검색한 시크릿 액세스 키입니다.</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>특정 지역에서 {{site.data.keyword.cos_full_notm}}에 액세스하는 데 사용하는 지역 API 엔드포인트에 대한 URL입니다. </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>{{site.data.keyword.cos_full_notm}}에 백업을 저장하는 데 사용하는 버킷의 이름입니다. </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>버킷에 백업을 보유하고 있는 오브젝트의 고유한 이름입니다. {{site.data.keyword.cos_full_notm}}에 데이터를 저장하려면 백업 팟(Pod)에 사용한 이름을 사용해야 합니다. </td>
     </tr>
     <td><code>&lt;mount_path&gt;</code></td>
     <td>데이터를 복원할 PVC를 마운트하는 복원 컨테이너 내부의 볼륨 마운트 경로입니다. 다중 PVC에 데이터를 복원하려면 각 PVC마다 별도의 마운트 경로를 지정하십시오. <code>volumeMount.name</code>에서 사용하는 이름이 YAML 파일의 <code>volumes.name</code> 섹션에 지정하는 볼륨의 이름과 일치하는지 확인하십시오. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>데이터를 복원할 PVC의 이름입니다. 다중 PVC에 데이터를 복원하려면 각 PVC에 대한 <code>volumes</code> 항목을 추가하십시오. <code>volumes.name</code>에서 사용하는 이름이 YAML 파일의 <code>volumeMount.name</code> 섹션에 있는 이름과 일치하는지 확인하십시오.</td>
     </tr> 
     </tbody>
     </table>

3. 복원 팟(Pod)을 작성하고 데이터 복원을 시작하십시오.

    ```
    kubectl apply -f restorepod.yaml
    ```
    {: pre}

4. 팟(Pod)이 실행 중인지 확인하십시오.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME              READY     STATUS             RESTARTS   AGE
    restorepod        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

팟(Pod)이 복원 명령을 실행하고 중지됩니다. `CrashLoopBackOff` 메시지는 Kubernetes가 팟(Pod)을 다시 시작하려고 시도 중임을 의미합니다.
5. 데이터 복원이 완료되었는지 확인하십시오.

    ```
    kubectl logs restorepod
    ```
    {: pre}

6. 팟(Pod)이 더 많은 리소스를 사용하지 못하도록 팟(Pod)을 제거하십시오.

    ```
    kubectl delete -f restorepod.yaml
    ```
    {: pre}

성공적으로 백업을 복원했습니다. 이제 클러스터의 다른 팟(Pod)에 PV를 바인드하는 PVC를 마운트하여 복원된 파일에 액세스할 수 있습니다. 백업된 컨테이너 데이터에 비루트 사용자가 포함된 경우 새 컨테이너에 비루트 권한을 추가해야 합니다. 자세한 정보는 [볼륨에 대한 비루트 사용자 액세스 권한 추가](/docs/containers?topic=containers-cs_troubleshoot_storage#cs_storage_nonroot)를 참조하십시오.


## 환경 변수 참조
{: #backup_restore_env_reference}

환경 변수로 전달할 수 있거나 실행 중인 팟(Pod)의 `config.conf` 파일에서 편집할 수 있는 필드의 전체 목록을 검토합니다. 환경 변수로 전달된 값은 `config.conf` 파일의 값을 대체합니다. 팟(Pod)에 대한 환경 변수를 검토하려면 `kubectl exec` 명령을 사용하여 팟(Pod)에 로그인하고 `env`를 실행하십시오.

|키|값 옵션|
|---|-------------|
|ACCESS_KEY_ID|{{site.data.keyword.cos_full_notm}}에서 HMAC 인증 정보의 일부인 **access_key_id**입니다.|
|SECRET_ACCESS_KEY|{{site.data.keyword.cos_full_notm}}에서 HMAC 인증 정보의 일부인 **secret_access_key**입니다.|
|ENDPOINT|{{site.data.keyword.cos_full_notm}} 버킷 데이터에 액세스하는 호스트 이름입니다.|
|BUCKET|백업된 데이터가 저장되는 {{site.data.keyword.cos_full_notm}}의 버킷 이름입니다.|
{: caption="표 4. {{site.data.keyword.cos_full_notm}} 변수" caption-side="top"}

|키|값 옵션|
|---|-------------|
|BACKUP_DIRECTORY|`/backup`. 기본값. 볼륨이 마운트되는 디렉토리의 절대 파일 경로입니다. 데이터가 이 디렉토리에서 백업됩니다. 해당 디렉토리가 백업 및 복원 프로세스를 위한 파일을 포함하므로 `backup_restore` 디렉토리를 선택하지 마십시오.|
|BACKUP_NAME|`volume_backup`. 기본값. 백업 이름을 선택하십시오.|
|BACKUP_TYPE|`full`. 기본값. 모든 파일을 항상 백업합니다.<br/> `incremental`. 새 파일이나 변경된 파일만 백업합니다. `incremental`을 선택하는 경우 **SCHEDULING_INFO** 및 **SCHEDULING_TYPE**의 값을 선택해야 합니다.|
|SCHEDULE_TYPE|`none`. 기본값. 일회성 백업을 작성합니다.<br/> **참고:** 일회성 백업을 작성하도록 선택한 경우 백업이 완료되면 팟(Pod)이 클러스터에서 제거됩니다. <br/> `periodic`. 스케줄된 백업을 작성하려면 값을 주기적으로 변경하십시오.|
|SCHEDULE_INFO|`hourly`. 시간별 백업을 작성합니다.<br/>`daily`. 기본값. 일별 백업을 작성합니다.<br/>`weekly`. 주별 백업을 작성합니다. 주기적 업데이트를 스케줄링하는 경우 이 변수를 포함해야 합니다.|
|EXCLUDE_DIRECTORIES|`none`. 기본값. 백업에서 제외할 디렉토리의 절대 파일 경로를 포함합니다. 쉼표로 디렉토리를 구분하십시오.|
{: caption="표 5. 백업 변수" caption-side="top"}

|키|값 옵션|
|---|-------------|
|BACKUP_NAME|`volume_backup`. 기본값. {{site.data.keyword.cos_full_notm}}에서 복원할 백업의 이름을 포함합니다.|
|RESTORE_DIRECTORY|`/backup`. 기본값. 볼륨이 마운트되는 절대 디렉토리입니다. 데이터가 이 디렉토리로 복원됩니다. 해당 디렉토리가 백업 및 복원 프로세스를 위한 파일을 포함하므로 `backup_restore` 디렉토리를 선택하지 마십시오.|
{: caption="표 6. 복원 변수" caption-side="top"}

