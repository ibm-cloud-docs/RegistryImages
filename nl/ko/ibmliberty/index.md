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

# **ibmliberty** 이미지 시작하기
{: #ibmliberty}

IBM® WebSphere® Application Server Liberty \(**ibmliberty**\) 이미지는 {{site.data.keyword.containerlong_notm}}용으로 제공됩니다.
{:shortdesc}

## 작동 방식 
{: #how_it_works}

IBM WebSphere Application Server Liberty 컨테이너에서 Java를 기반으로 고유 이미지를 작성하고 고유 WAR, EAR 또는 OSGi 앱을 배치하기 위해 **ibmliberty** 이미지를 상위로 사용할 수 있습니다.
{:shortdesc}

## 포함된 항목 
{: #whats_included}

모든 Liberty 이미지는 다음 소프트웨어 패키지를 제공합니다.
{:shortdesc}

-   IBM WebSphere Application Server for Developers Liberty
-   IBM Java Runtime Environment 8.0

이미지에 설치된 특정 Liberty 기능은 사용자가 선택하는 태그에 따라 다릅니다. 다음 표는 각각의 **ibmliberty** 이미지에 포함되는 기능을 보여줍니다. 각 기능에 대한 자세한 정보는 [IBM Knowledge Center의 Liberty 기능 개요](http://www.ibm.com/support/knowledgecenter/SSAW57_8.5.5/com.ibm.websphere.wlp.nd.doc/ae/rwlp_feat.html)를 참조하십시오.

|태그|설명|
|---|-----------|
|모든 **ibmliberty** 이미지|모든 **ibmliberty** 이미지에는 다음 기능이 포함됩니다. <ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|**ibmliberty:latest**|이 이미지는 **ibmliberty:javaee7** 이미지를 지시합니다.|
|**ibmliberty:webProfile6**|이 이미지에는 Java EE6 웹 프로파일 준수에 필요한 모든 기능이 포함됩니다. [http://wasdev.net/](http://wasdev.net/)에서 런타임 JAR을 사용하여 다운로드할 수 있는 기능, 특히 OSGi 애플리케이션에 필요한 기능과 일치하게 컨텐츠를 가져오는 추가 기능도 가져옵니다.|
|**ibmliberty:webProfile7**|이 이미지에는 Java EE7 웹 프로파일 준수에 필요한 모든 기능이 포함됩니다.|
|**ibmliberty:javaee7**|이 이미지에는 Java EE7 전체 플랫폼 준수에 필요한 기능과 함께 **ibmliberty:webProfile7** 이미지의 모든 기능이 포함됩니다.|

## 사용 제한사항 
{: #usage}

다음 표에서는 {{site.data.keyword.Bluemix_notm}}에서 **ibmliberty** 이미지의 무료 사용량에 적용되는 제한사항을 보여줍니다.
{:shortdesc}

**참고:** **ibmliberty** 이미지의 가격은 {{site.data.keyword.Bluemix_notm}}에서 사용하는 컨테이너의 가격 책정과 별도로 책정됩니다.

|환경|무료 사용량 제한사항|
|-----------|-----------------------|
|개발|**ibmliberty** 이미지의 **무제한** 무료 사용량입니다.|
|프로덕션|**ibmliberty** 이미지의 무료 사용량은 이미지를 실행하는 모든 컨테이너 인스턴스에서 **최대 2GB Java 힙 영역**으로 제한됩니다. 예를 들어, 2 x 1GB 또는 4 x 512 MB 힙 liberty 인스턴스를 무료로 보유할 수 있습니다.

컨테이너 인스턴스의 Java 힙 사용량을 모니터하려면 [CLI로 컨테이너의 Java 힙 영역 사용량 모니터링](#monitor_heap)을 참조하십시오.


Docker 허브의 [websphere-liberty 이미지](https://hub.docker.com/_/websphere-liberty/)에 있는 라이센스 섹션에서 IBM 인증 이미지의 이용 약관을 검토하십시오.

## 시작하기 
{: #get_started}

{{site.data.keyword.Bluemix_notm}} 카탈로그에서 무료 **ibmliberty** 이미지 중 하나를 사용하거나 자체 프로덕션 라이센스 이미지를 선택하여 단일 컨테이너 또는 컨테이너 그룹을 작성하십시오.
{:shortdesc}

**중요:** 시작하기 전에 **ibmliberty** 이미지의 [사용량 제한사항](#usage)을 검토하십시오.

1.  카탈로그에서 **컨테이너**를 선택하고 컨테이너를 빌드할 **ibmliberty** 이미지를 선택하십시오. 자체 프로덕션 라이센스 이미지를 작성하고 이를 {{site.data.keyword.Bluemix_notm}}에 배치한 경우에는 카탈로그에서 이 이미지를 선택하십시오. 컨테이너 작성 페이지가 열립니다.
2.  **태그/ 버전** 드롭 다운 상자에서 사용하고자 하는 **ibmliberty** 이미지의 버전을 선택하십시오.
3.  단일 컨테이너 또는 확장 가능한 컨테이너 그룹을 작성할 것인지를 선택하십시오. 컨테이너 작성 방법에 대한 자세한 정보는 다음 주제를 참조하십시오.

    -   [{{site.data.keyword.Bluemix_notm}} 대시보드를 사용하여 단일 컨테이너 작성](/docs/containers/container_single_ui.html#gui)
    -   [{{site.data.keyword.Bluemix_notm}} 대시보드를 사용하여 컨테이너 그룹 작성](/docs/containers/container_ha.html#container_group_ui)
    
    **참고:** **ibmliberty** 이미지에는 9080 포트가 공개적으로 노출되어야 합니다. {{site.data.keyword.Bluemix_notm}} 대시보드에서 컨테이너를 작성할 때 기본적으로 **공용 포트** 필드에서 포트가 추가됩니다. CLI에서 컨테이너를 작성하려면 `bx ic run` 명령으로 포트를 노출하십시오.


## CLI로 컨테이너에 대한 Java 힙 영역 사용량 모니터링 
{: #monitor_heap}


**ibmliberty** 이미지에서 컨테이너를 작성한 후에는 모든 실행 중인 프로세스를 나열하고 Java 힙 사용량을 검토할 수 있습니다. Java 힙 영역은 런타임 중에 Java 애플리케이션이 사용할 수 있는 메모리입니다.
{:shortdesc}

1.  컨테이너 내의 모든 실행 중인 프로세스를 나열하십시오.

    ```
    bx ic top CONTAINER -aux
    ```
    {: pre}

    CLI 출력은 다음과 같이 표시됩니다.

    ```    
    USER        PID       %CPU   %MEM    VSZ         RSS        TTY     STAT   START    TIME   COMMAND
    contain+    3322245   3.2    0.0     11522856    216192     ?       Ssl    14:43    0:35   /opt/ibm/java/jre/bin/java -javaagent:/opt/ibm/wlp/bin/tools/ws-javaagent.jar -Djava.awt.headless=true -jar /opt/ibm/wlp/bin/tools/ws-server.jar defaultServer 
    ```
    {: screen}

2.  **RSS** 열에서 Java 힙 사용량을 검토하십시오. Java 힙 사용량은 KB 단위로 표시됩니다. 힙 사용량이 모든 인스턴스에서 2097152KB(2GB) 미만이면 WebSphere Application Server 라이센스를 구매할 필요가 없습니다.
3.  WebSphere Application Server 인스턴스의 최대 힙 사용량을 조정하십시오. 자세한 정보는 [WebSphere Application Server V8.5 Liberty 프로파일에서 일반 JVM 인수 설정](http://www-01.ibm.com/support/docview.wss?uid=swg21596474)의 내용을 참조하십시오.

## WebSphere Application Server 라이센스 가져오기 
{: #license}

WebSphere Application Server 라이센스는 필요한 PVU\(Processor Value Unit\) 수를 기반으로 합니다. PVU는 IBM Middleware 소프트웨어의 라이센싱에 대한 측정 단위입니다. PVU 수는 소프트웨어에 사용할 수 있는 프로세서\(코어\) 수를 나타냅니다.
{:shortdesc}

{{site.data.keyword.Bluemix_notm}}의 모든 컨테이너 크기에는 WebSphere Application Server 라이센스에서 사용 가능해야 하는 특정 수의 PVU 인타이틀먼트가 있어야 합니다. 따라서 라이센스를 구매하기 전에 **ibmliberty** 컨테이너를 계획해야 합니다.

WebSphere Application Server 라이센스를 구매하려면 [IBM 서비스](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us)에 문의하십시오. WebSphere Application Server v8.5 이상에 대한 라이센스가 이미 있으면, 컨테이너의 배치를 위해 기존 인타이틀먼트의 미사용 PVU를 사용할 수 있습니다.

라이센스를 구매한 후 추가 PVU가 필요하면 [IBM 서비스](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us)에 문의하여 양을 늘릴 수 있습니다.

## {{site.data.keyword.containershort_notm}}에서 사용될 프로덕션 라이센스 **ibmliberty** 이미지 작성 
{: #prod_image}

WebSphere Application Server 라이센스를 사용하여 {{site.data.keyword.containershort_notm}}에서 사용할 수 있는 프로덕션 라이센스 **ibmliberty** 이미지를 작성합니다. 다음 태스크 중 하나를 선택하십시오.
{:shortdesc}

-   [Docker 허브에서 프로덕션 이미지로 이미지를 업그레이드하십시오](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade).
-   [자체 프로덕션 라이센스 이미지를 빌드하십시오](https://github.com/WASdev/ci.docker/tree/master/ga/production-install).

프로덕션 라이센스가 부여된 이미지를 작성한 후 {{site.data.keyword.containershort_notm}}와 함께 사용하도록 [이미지를 사설 레지스트리에 푸시](/docs/services/Registry/index.html)하십시오.

## 제공된 이미지에서 이미지 작성 
{: #creating_image}

**ibmliberty** 이미지 중 하나를 자체 앱 코드가 포함된 하위 이미지를 작성하기 위한 상위로서 사용할 수 있습니다. 샘플 Dockerfile을 사용자 정의하고 컴퓨터에서 이미지를 빌드하십시오. 그런 다음 조직의 사설 이미지 레지스트리에 이미지를 추가하고 이 이미지를 사용하여 컨테이너를 작성할 수 있습니다.
{:shortdesc}

시작하기 전에 다음과 같은 단계를 고려하십시오.

-   앱 코드를 WAR, EAR 또는 OSGi 파일에 빌드합니다.
-   이미지가 빌드될 디렉토리에 파일을 복사합니다.


**ibmliberty** 이미지에서 앱 코드를 사용하여 이미지를 작성하려면 다음을 수행하십시오.

1. 문서 편집기를 사용하여 이름이 Dockerfile 인 파일을 작성하고 다음 정보를 이 파일에 복사하십시오.

    ```
    FROM registry.{{site.data.keyword.domainname}}/ibmliberty:<tag>
    COPY <app_name>.<file_extension> /config/dropins/
    
    ```
    {: screen}

    **참고:** /config 디렉토리는 /opt/ibm/wlp/usr/servers/defaultServer의 바로 가기입니다.
    
2. <tag\>를 앱에 필요한 기능이 포함된 **ibmliberty** 이미지의 버전으로 바꾸십시오.

3. <app\_name\>을 앱 파일의 이름으로 바꾸십시오.

4. <file\_extension\>을 .war, .ear 또는 .eba로 바꾸십시오.

5. Dockerfile에 앱의 기타 종속 항목을 추가하십시오.

6. 이미지를 빌드한 다음 사설 이미지 레지스트리에 푸시하십시오. 자세한 정보는 [{{site.data.keyword.registrylong_notm}} 시작하기](/docs/services/Registry/index.html)를 참조하십시오.

**참고:** 컨테이너의 /logs 디렉토리에 Liberty 로그 파일을 작성하도록 모든 **ibmliberty** 이미지가 구성됩니다. Liberty 서버에서 쓰는 기타 모든 파일은 /opt/ibm/wlp/output/defaultServer 디렉토리에 작성됩니다. 바로 가기 /output을 사용하여 이 파일에 액세스할 수 있습니다.

## **ibmliberty** Dockerfile 참조 
{: #reference_dockerfile}

이 Dockerfile에서는 {{site.data.keyword.Bluemix_notm}}의 **ibmliberty:webProfile7** 이미지가 Docker 허브의 공용 websphere-liberty 이미지에서 빌드되는 방법을 보여줍니다. 이 정보는 참조 전용입니다.
{:shortdesc}

```
FROM websphere-liberty:webProfile7

# Update packages
RUN apt-get update &&\
    DEBIAN_FRONTEND=noninteractive apt-get upgrade -y &&\
    apt-get clean &&\
    rm -Rf /var/cache/*

# Set password length and expiry for compliance with vulnerability advisor
RUN sed -i 's/^PASS_MAX_DAYS.*/PASS_MAX_DAYS   90/' /etc/login.defs
RUN sed -i 's/sha512/sha512 minlen=8/' /etc/pam.d/common-password

# Add docs label
LABEL doc.url="/docs/services/RegistryImages/ibmliberty/index.html"
```
{: screen}
