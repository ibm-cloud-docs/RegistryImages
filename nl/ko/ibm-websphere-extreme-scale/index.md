---

copyright:
  years: 2017
lastupdated: "2017-10-30"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# **ibm-websphere-extreme-scale** 이미지 시작하기 
{: #ibm-websphere-extreme-scale}

**ibm-websphere-extreme-scale** 이미지는 {{site.data.keyword.containerlong}}용으로 제공됩니다.

## 작동 방식 
{: #how_it_works}

IBM WebSphere&reg; eXtreme Scale 이미지는 Liberty 런타임 환경에서 실행 되는 분산형 인메모리 캐시 기능입니다. 최신 산업 표준 및 스펙을 기반으로 Liberty 캐싱 서버, 캐싱 오퍼레이션 도구, 관리 콘솔 및 바로 사용할 수 있는 REST 관리 서비스를 제공합니다. 이 이미지는 코어 IBM WebSphere eXtreme Scale 기술을 기반으로 빌드됩니다. 이 이미지에 로드된 캐싱 기술은 JCache\(JSR107\) 스펙과 규제 준수를 완전히 지원합니다.

`ibm-websphere-extreme-scale` 이미지를 사용할 때 다음과 같은 기본 강조사항이 제공됩니다.

-   eXtreme Scale 기술을 기반으로 하는 배치가 쉽고 구성이 쉬운 분산 캐싱 솔루션
-   단순하고 사용하기 쉬운 관리 콘솔 및 REST API
-   모든 eXtreme Scale 캐싱 유스 케이스를 지원하기 위한 전체 사용자 정의
-   몇 분 내에 캐싱 환경에 통합되는 쉽게 구서알 수 있는 이미지
-   JSR107 캐싱 스펙을 지원하는 JCache 규제 준수


## 포함된 항목 
{: #whats_included}

모든 **ibm-websphere-extreme-scale** 이미지에서는 다음 소프트웨어 패키지를 제공합니다.

-   Ubuntu 16.04 기본 이미지, 64비트
-   IBM WebSphere eXtreme Scale
-   IBM Java Runtime Environment 8.0

이미지에 설치된 특정 기능은 사용자가 선택하는 태그에 따라 다릅니다. 다음 표에서는 각 **ibm-websphere-extreme-scale** 이미지에 포함된 코드 레벨을 보여줍니다. 이 기능에 대한 자세한 정보는 [eXtreme Scale 문서](http://www.ibm.com/support/knowledgecenter/SSTVLU)를 참조하십시오.

|태그|설명|
|---|-----------|
|**ibm-websphere-extreme-scale:latest**|최신 IBM WebSphere eXtreme Scale 이미지를 지정하려면 이 태그를 사용하십시오.|
|**ibm-websphere-extreme-scale:8.6.1.x**|WebSphere eXtreme Scale의 특정 릴리스 레벨을 지정하려면 이 태그를 사용하십시오.|
{: caption="표 1. IBM WebSphere eXtreme Scale 이미지" caption-side="top"}

## 사용 제한사항 
{: #usage}

다음 표에서는 {{site.data.keyword.Bluemix_notm}}에서 **ibm-websphere-extreme-scale** 이미지의 무료 사용량에 적용되는 제한사항을 보여줍니다.

|환경|사용 제한사항|
|-----------|------------------|
|개발|`ibm-websphere-extreme-scale` 이미지는 개발에 무료 사용량을 무제한으로 사용할 수 있습니다.|
|프로덕션|**ibm-websphere-extreme-scale** 이미지의 프로덕션 사용량은 라이센스가 부여된 IBM WebSphere eXtreme Scale 제품 사용자만 사용할 수 있습니다. 프로덕션 사용에 대한 자세한 정보는 [가격 책정 보기 및 구매](https://www-112.ibm.com/software/howtobuy/buyingtools/paexpress/Express?P0=E1&part_number=D56YTLL,D56YVLL,D572PLL,D572MLL,D0594LL,D059GLL&catalogLocale=en_US&Locale=en_US&country=USA&PT=jsp&CC=USA&VP=&TACTICS=&S_TACT=&S_CMP=&brand=SB05)를 참조하십시오.|
{: caption="표 2. {{site.data.keyword.Bluemix_notm}}에서 ibm-websphere-extreme-scale 이미지의 사용량 제한사항" caption-side="top"}

**참고:** **ibm-websphere-extreme-scale** 이미지의 가격은 {{site.data.keyword.Bluemix_notm}}에서 사용하는 컨테이너의 가격 책정과 별도로 책정됩니다.

[라이센스 정보 문서](http://www-03.ibm.com/software/sla/sladb.nsf/displaylis/A44EE6829150B2998525801700516718?OpenDocument)의 라이센스 섹션에서 IBM 인증 이미지의 이용 약관을 검토하십시오.


## 시작하기 
{: #get_started}

콘솔 또는 명령행을 사용하여 **ibm-websphere-extreme-scale** 이미지를 배치할 수 있습니다.

이 프로시저에는 콘솔 또는 명령행을 사용하여 이미지를 배치하는 데 사용하는 다음 옵션이 포함되어 있습니다.

-   **카탈로그**의 {{site.data.keyword.Bluemix_notm}} [콘솔](#bmconsole)에서 이미지를 선택하십시오.
-   cf ic 명령을 사용하여 CF [명령행](#bmcommand)에서 이미지를 시작하십시오.

**중요:** 시작하기 전에 **ibm-websphere-extreme-scale** 이미지의 이전 섹션에 설명된 사용량 제한사항을 검토하십시오.


### {{site.data.keyword.Bluemix_notm}} 콘솔에서 배치
{: #bmconsole}

   1.  카탈로그에서 **컨테이너**를 선택하고 **ibm-websphere-extreme-scale** 이미지를 선택하여 컨테이너 빌드를 시작하십시오.
   2.  **태그/버전** 드롭다운 메뉴에서 사용할 **ibm-websphere-extreme-scale** 이미지의 버전을 선택하십시오.
   3.  단일 컨테이너를 작성하십시오.

        **참고:** 컨테이너를 두 개 이상 작성하려면 **확장 가능** 옵션을 사용하여 두 개 이상의 eXtreme Scale 캐시 멤버 그룹을 작성하지 마십시오. 대신 **단일** 옵션을 선택하여 여러 컨테이너를 작성하고 eXtreme Scale 대시보드 UI를 사용하여 캐시 멤버에 참여하십시오.

        자세한 정보는 [{{site.data.keyword.Bluemix_notm}} 대시보드를 사용하여 단일 컨테이너 작성](/docs/containers/container_single_ui.html#gui)을 참조하십시오.

        1.  컨테이너 작성 패널에서 **컨테이너 이름**(예: `wxs1`)을 지정하십시오. 컨테이너 이름에는 공백이나 기타 인쇄할 수 없는 문자를 사용할 수 없습니다.
        2.  **크기**에는 ibm-eXtreme-scale 이미지를 시작하는 데 사용한 컨테이너의 크기를 지정하십시오.
        3.  **공인 IP 주소**에는 공인 IP 요청 및 바인드를 지정하십시오.
        4.  고급 옵션 패널을 여십시오. 나중에 롤링 업그레이드를 설치할 때 eXtreme Scale 구성 데이터를 지속할 수 있도록 볼륨을 작성하십시오. 예를 들어 다음과 같습니다.
            1. 고급 옵션에서 **볼륨 작성**을 크릭하고 **볼륨 이름**을 지정하십시오. 예를 들어 다음 명령을 입력하십시오.
            2. **새 환경 변수 추가**를 클릭하십시오. **ibm-websphere-extreme-scale** 이미지에는 컨테이너에 저장되고 권한 부여된 사용자가 액세스하는 환경 변수가 필요합니다.
            <table>
            <caption>표 3. ibm-websphere-extreme-scale 이미지의 환경 변수</caption>
               <tr>
                 <th>변수 이름</th>
                 <th>지정될 값 </th>
               </tr>
               <tr>
                 <td>SECRET_KEY</td>
                 <td>비밀 키의 값(예: <var class="keyword varname">s3cretKey!</var>)입니다. 캐시 멤버 그룹의 여러 멤버 간에 내부 통신을 전송하기 위해 eXtreme Scale 서버에서 사용하는 비밀 키입니다. 자세한 정보는 [캐시 멤버 그룹 섹션](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txscreategridtut1.html)을 참조하십시오.</td>
               </tr>
               <tr>
                 <td>XSADMIN_PWD</td>
                 <td>eXtremeScale 대시보드 UI에 로그온하는 xsadmin 사용자의 비밀번호입니다(예: <var class="keyword varname">xsadmin4Me!</var>).</td>
               </tr>
               </table>
  
              **참고:** eXtreme Scale 컨테이너가 시작된 후 비밀번호와 비밀 키를 변경할 수 있습니다. 비밀 키와 비밀번호에는 모두 다음과 같은 비밀번호 규칙이 있습니다. 다음 규칙에 따라 두 값을 모두 입력하십시오.

              비밀번호와 비밀 키에는 최소 하나의 번호[`0-9`], 대문자 하나[`A-Z`], 특수 문자 하나[`~!@#$%^&*()_-+=:;'[]{}|<>,.?/`] 가 있어야 하며 10자 이상이어야 합니다.

              비밀번호 변경 방법에 대한 자세한 정보는 [관리 문서](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txsadministerxsld.html)를 참조하십시오.

              기본적으로 **공용 포트** 필드에 포트가 추가됩니다.

   4.  **작성**을 클릭하여 {{site.data.keyword.Bluemix_notm}} 대시보드에서 컨테이너를 작성하십시오.
    
   5.  컨테이너와 eXtreme Scale 서버가 완전히 시작되고 나면 eXtreme Scale 대시보드 UI에 로그온하여 배치를 확인하십시오. {{site.data.keyword.Bluemix_notm}} 콘솔에서 컨테이너를 클릭하여 공인 IP를 보십시오. WebSphere eXtreme Scale 컨테이너는 다음 URL에서 액세스할 수 있습니다.

    ```
    https://<Public_IP>:9443
    ```
    {: pre}

   **참고:** 서버를 완전히 시작하는 데 5-10분 정도 걸릴 수 있습니다.


### 명령행에서 이미지 배치
{: #bmcommand}
 1.  Docker 볼륨을 작성하여 IBM WebSphere eXtreme Scale 지속적 데이터를 저장하십시오. 예를 들어 다음 명령을 입력하십시오.

  ```
  cf ic volume create <vol-wxs1>
  ```
  {: pre}

 2.  다음 명령을 입력하여 {{site.data.keyword.Bluemix_notm}}에서 IBM WebSphere eXtreme Scale 컨테이너를 시작하십시오.

    **참고:** 앞서 설명한 비밀번호 규칙에 따라 작성된 고유 비밀번호로 yourSecretKey와 yourXsadminPassword를 바꾸십시오.

   ```
   cf ic run -itdP --volume <vol-wxs1:/vol> --name <wxs1> -m 1024 -e SECRET_KEY=<yourSecretKey> -e XSADMIN_PWD=<yourXsadminPassword> registry.ng.bluemix.net/ibm-websphere-extreme-scale:latest
   ```
   {: pre}
        
     **참고:** 컨테이너 이름은 영숫자 문자만 포함해야 하며 문자로 시작되어야 합니다. 또는 컨테이너 이름에 영숫자가 아닌 기타 문자를 포함해야 하는 경우 <code>-e XSLD_CONTAINER_ALIAS=<wxs1></code>을 설정할 수 있습니다.

 3.  첫 번째 IBM WebSphere eXtreme Scale 컨테이너에 사용할 공인 IP를 요청하십시오. 다음 명령을 입력하여 IP 주소를 작성하십시오.

   ```
   cf ic ip request
   ```
   {: pre}

 4.  다음 명령을 입력하여 WebSphere eXtreme Scale 컨테이너를 사용자가 요청한 공인 IP 주소에 바인드하십시오.

   ```
   cf ic ip bind <REQUESTED_IP> wxs1
   ```
   {: pre}

 5.  다음 명령을 입력하여 `wxs1` 컨테이너 상태가 실행 중인지 확인하십시오.

   ```
   cf ic ps -a
   ```
   {: pre}

 6.  다음 명령을 입력하여 `wxs1` 컨테이너로 연결하고 nanny 로그를 모니터하십시오.

   ```
   cf ic exec -it <wxs1> tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
        ```
   {: pre}

    **참고:** 서버를 시작하는 데 5-10분 정도 걸릴 수 있습니다.

 7.  컨테이너와 eXtreme Scale 서버가 완전히 시작되고 나면 eXtreme Scale 대시보드 UI에 로그온하여 배치를 확인하십시오.

   ```
   https://<REQUESTED_IP>:9443
   ```
   {: pre}


## 캐싱 용량 추가 
{: #caching}

1.  이전과 동일한 단계를 따라 콘솔 또는 명령행에서 다른 이미지를 배치하십시오.
2.  컨테이너와 eXtreme Scale 서버가 완전히 시작되고 나면 이 멤버의 eXtreme Scale 대시보드 UI에 로그온하십시오.

    ```
    https://<REQUESTED_IP>:9443
    ```
    {: pre}

3.  데이터 캐시 멤버 페이지를 열고 이 멤버의 **조치** 열에서 **결합**을 선택하십시오.
4.  첫 번째 멤버의 요청된 공인 IP, 첫 번째 멤버의 비밀 키 및 캐시 그룹에서 이 멤버의 고유한 이름을 입력하십시오.
5.  **결합**을 클릭하고 태스크가 완료될 때까지 기다리십시오. 태스크가 완료되면 모든 멤버가 데이터 캐시 멤버 페이지에 표시됩니다.

## 롤링 업그레이드 수행 
{: #rolling_upgrade}

{{site.data.keyword.containershort_notm}}에서 `ibm-websphere-extreme-scale`을 실행하는 컨테이너으 롤링 업그레이드를 수행하려면 다음 단계가 필요합니다.

**참고:** eXtreme Scale 캐시 멤버 그룹에 멤버가 하나뿐이면 이 {{site.data.keyword.IBM_notm}} 컨테이너를 제거한 다음 완전히 새로 작성하십시오. 그러나 eXtreme Scale 캐시 멤버 그룹에 두 개 이상의 멤버와 여러 카탈로그 서버가 배치되어 있으며 {{site.data.keyword.containershort_notm}}에 캐시된 데이터를 유지하려는 경우 다음 롤링 업그레이드 단계를 완료하여 캐시된 데이터의 손실을 방지하십시오. 컨테이너 서버만 있는 캐시 멤버를 먼저 업그레이드한 다음 카탈로그 서버도 있는 캐시 멤버를 업그레이드하십시오.

1.  wxsmigrate라는 새 컨테이너를 시작한 다음 이 컨테이너를 사용하여 기존 eXtreme Scale 컨테이너를 모두 업그레이드하십시오. 자세한 정보는 이 페이지의 [시작하기 섹션](#get_started)을 참조하십시오. 최신 eXtreme Scale 업데이트 .zip 파일은 새 wxsmigrate 컨테이너의 /tmp 폴더에 배치됩니다.
2.  업데이트된 eXtreme Scale .zip 파일을 wxsmigrate 컨테이너에서 {{site.data.keyword.containershort_notm}} CLI가 실행 중인 로컬 시스템으로 복사하십시오.

    ```
    cf ic cp wxsmigrate:/tmp/xsld-bin-update.zip
    ```
    {: pre}
    
    ```
    cf ic cp wxsmigrate:/tmp/xsld-derby-update.zip
    ```
    {: pre}

3.  이제 캐시 멤버 그룹의 각 캐시 멤버에 대해 한 번에 하나의 컨테이너씩 다음 단계를 완료하십시오.

    1. 업데이트된 eXtreme Scale .zip 파일을 로컬 시스템에서 업그레이드할 eXtreme Scale 컨테이너로 복사하십시오.
    
      ```
      cf ic cp  xsld-bin-update.zip  <container_name>:/tmp/xsld-bin-update.zip
      ```
      {: pre}
  
      ```
      cf ic cp  xsld-derby-update.zip  <container_name>:/tmp/xsld-derby-update.zip
      ```
      {: pre}
     
    2. 업그레이드할 컨테이너에 연결하십시오. 
    
      ```
      cf ic exec -it <container_name> bash
      ```
      {: pre}

    3. 업그레이드할 준비가 된 컨테이너에서 XSLD 서버를 중지하십시오.
      
      ```
      /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
      ```
      {: pre}
      
      서버가 중지될 때까지 기다리십시오.
      
    4. 컨테이너에 있는 최신 eXtreme Scale 업데이트 코드의 압축을 해제하십시오.
      
      ```
      unzip  -o  /tmp/xsld-bin-update.zip   -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
      
      ```
      unzip  -o  /tmp/xsld-derby-update.zip -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
    
    5. 다음과 같이 XSLD 서버를 다시 시작하십시오.
    
      ```
      /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
      ```
      {: pre}
      
    6. nanny 로그를 모니터하여 서버가 시작되었는지 확인하십시오.
    
      ```
      tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
      ```
      {: pre}
      
4. 모든 IBM 컨테이너에서 업그레이드를 완료한 후 wxsmigrate 컨테이너를 제거하십시오.

 ```
 cf ic rm -f <wxsmigrate>
 ```
 {: pre}

## 문제점 해결 
{: #troubleshoot}

###호스트 맵핑이 유실되면 캐시 멤버 연결 문제점 해결 
{: #troubleshoot_cache}

컨테이너 사이에 유실된 호스트 맵핑으로 인해 캐시 그룹의 멤버가 연결이 끊기면 다음 단계를 완료하십시오.

1.  다음과 같이 각 컨테이너에 연결하십시오.

    ```
    cf ic exec -it <container_name> bash
    ```
    {: pre}

2.  각 멤버의 사설 IP, 호스트 이름 및 호스트 별명을 수집하십시오.

    호스트 별명은 XSLD_CONTAINER_ALIAS 환경 변수의 값입니다.

    ```
    echo ${<XSLD_CONTAINER_ALIAS>}
    ```
    {: pre}

    사설 IP는 컨테이너에서 hostname -I 명령을 실행하면 리턴되는 IP 주소입니다.

    ```
    hostname -I
    ```
    {: pre}

    호스트 이름은 컨테이너에서 hostname 명령을 실행하면 리턴되는 호스트 이름입니다.


3.  기타 모든 멤버 별명, IP 및 호스트 이름으로 전달되는 각 멤버에서 recoverXSLD.sh 스크립트를 실행하십시오.

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/recoverXSLD.sh <member2_alias> <member2_private_ip> <member2_hostname> ... <memberN_alias> <memberN_private_ip> <memberN_hostname>
    ```
    {: pre}

4.  캐시 그룹의 각 컨테이너에서 stopXSLD.sh를 실행하여 실행 중인 서버를 중지하십시오. 다음과 같이 stopXSLD.sh를 실행하십시오.

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
    ```
    {: pre}

5.  카탈로그 서버를 중지하려는 중에 stopXSLD.sh가 진행되지 않는 경우 다음 단계를 따르십시오.
    1.  다음과 같이 카탈로그 서버 프로세스 목록을 표시하십시오.

        ```
        ps -ef | grep cs-default
        ```
        {: pre}

    2.  다음과 같이 프로세스 ID를 사용하여 카탈로그 서버 프로세스를 종료하십시오.

        ```
        kill -9 <catalog_server_process_id>
        ```
        {: pre}

    3.  다음과 같이 stopXSLD.sh를 다시 실행하십시오.

        ```
        /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
        ```
        {: pre}

6.  모든 컨테이너에서 거의 동시에 XSLD를 시작하십시오. 다음과 같이 startXSLD.sh를 실행하십시오.

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
    ```
    {: pre}


### 중지된 Derby 복제 태스크의 문제점 해결 
{: #troubleshoot_derby}

Derby 복제 태스크가 10분이 넘도록 45% 이상 진행되지 않으면 네트워크 문제 때문일 가능성이 큽니다. 다음 단계를 완료하여 문제점을 해결하십시오.

1.  cURL과 같은 REST POSTing 유틸리티를 사용하여 Derby 복제 태스크를 취소하십시오.

    ```
    curl -X PUT --header "Content-Type: application/json" --header "Accept: application/json" "https://<PUBLIC_IP>:9445/wxsadmin/v1/task/cancel/<DERBY_REPLICATION_TASK_ID>/force"
    ```
    {: pre}

    **참고:** <PUBLIC_IP>는 Derby 복제 태스크를 시작하기 위해 명령에서 사용된 IP이며 <DERBY_REPLICATION_TASK_ID>는 해당 명령에서 출력된 태스크 ID입니다.

2.  Derby 복제 태스크를 다시 실행하십시오.

## REST API에 액세스 
{: #api}

eXtreme Scale이 {{site.data.keyword.containershort_notm}}에 배치된 경우 다음 단계를 완료하여 REST API에 액세스하십시오.

1.  REST API에 연결하는데 사용할 컨테이너의 호스트 이름을 검색하십시오.
    1.  다음과 같이 컨테이너에 연결하십시오.

        ```
        cf ic exec -it <container_name> bash
        ```
        {: pre}

    2.  호스트 이름을 나열하십시오.

        ```
        hostname
        ```
        {: pre}

2.  동일한 컨테이너의 공인 IP를 검색하십시오.

    컨테이너에 공인 IP가 없는 경우 {{site.data.keyword.Bluemix_notm}} [콘솔](#bmconsole) 또는 [명령행](#bmcommand)을 사용하여 컨테이너에 공인 IP를 제공하기 위해 시작하기 절의 다음 단계를 따르십시오.

3.  공인 IP와 컨테이너 호스트 이름을 함께 맵핑하는 로컬 클라이언트 컴퓨터의 호스트 파일에 항목을 작성하십시오. 이 항목은 다음과 유사합니다.

    ```
    169.44.8.87 instance-05106102
    ```
    {: pre}

    호스트 파일의 일반적인 전체 경로 이름은 다음과 같습니다.

    -   UNIX: /etc/hosts
    -   Windows: C:\\Windows\\System32\\drivers\\etc\\hosts
4.  브라우저에서 Swagger REST API에 액세스하십시오.
    -   CRUD 오퍼레이션의 경우:

        ```
        https://<host_name>:9444/ibm/api/explorer
        ```
        {: pre}

    -   관리 오퍼레이션의 경우:

        ```
        https://<host_name>:9445/ibm/api/explorer
        ```
        {: pre}


샘플과 최신 업데이트를 받으려면 [GitHub의 WebSphere eXtreme Scale](https://github.com/ibmWebsphereExtremeScale)을 방문하십시오. eXtreme Scale을 배치하는 데 대한 자세한 도움말은 다음 지원 문서와 동영상을 참조하십시오.

-   [IBM WebSphere eXtreme Scale 문서](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/cxsgetstartxsld.html)
-   [XS developerWorks 포럼](https://www.ibm.com/developerworks/community/forums/html/forum?id=11111111-0000-0000-0000-000000000778&ps=25)
-   [WebSphere eXtreme Scale V8.6.x 시작하기](https://www.youtube.com/watch?v=xw7Ck3c40rA)
-   [WebSphere eXtreme Scale - Liberty Deployment로 캐시 추가](https://www.youtube.com/watch?v=I1g88bnstn8)
-   [WebSphere eXtreme Scale Liberty Deployment DynaCache 눈금으로 작업하도록 WAS 클라이언트 설정](https://www.youtube.com/watch?v=RvhcknNX-4Q)
-   [WebSphere eXtreme Scale Liberty Deployment Session Cache 눈금으로 작업하도록 WAS 클라이언트 설정](https://www.youtube.com/watch?v=GKTy9I66Klc)

