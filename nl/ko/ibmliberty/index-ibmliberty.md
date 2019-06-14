---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-04"

keywords: IBM Cloud Container Registry, IBM Liberty, ibmliberty, container image, IBM WebSphere Application Server Liberty, liberty, public image

subcollection: RegistryImages

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:download: .download}

# `ibm/liberty` 이미지 시작하기
{: #ibmliberty}

IBM® WebSphere® Application Server Liberty \(`ibm/liberty`\) 이미지는 {{site.data.keyword.containerlong_notm}}용으로 제공됩니다.
{:shortdesc}

명령행을 사용하여 {{site.data.keyword.IBM_notm}}에서 제공한 이미지에 액세스할 수 있습니다. [IBM 공용 이미지](/docs/services/Registry?topic=registry-public_images#public_images)를 참조하십시오.
{: tip}

## 작동 방식
{: #ibmliberty_how_it_works}

IBM WebSphere Application Server Liberty 컨테이너에서 Java를 기반으로 고유 이미지를 작성하고 고유 WAR, EAR 또는 OSGi 앱을 배치하기 위해 `ibm/liberty` 이미지를 상위로 사용할 수 있습니다.
{:shortdesc}

## 포함된 항목
{: #ibmliberty_whats_included}

모든 Liberty 이미지는 다음 소프트웨어 패키지를 제공합니다.
{:shortdesc}

- IBM WebSphere Application Server for Developers Liberty
- IBM Java Runtime Environment 8.0

이미지에 설치된 특정 Liberty 기능은 사용자가 선택하는 태그에 따라 다릅니다. 다음 표에서는 각 `ibm/liberty` 이미지에 포함되는 기능을 보여줍니다. 각 기능에 대한 자세한 정보는 [Liberty features overview in IBM Knowledge Center![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSAW57_liberty/com.ibm.websphere.wlp.nd.multiplatform.doc/ae/rwlp_feat.html)를 참조하십시오.

|태그|설명|
|---|-----------|
|모든 `ibm/liberty` 이미지|모든 `ibm/liberty` 이미지에는 다음 기능이 포함됩니다. <ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|`ibm/liberty:latest`|이 이미지는 `ibm/liberty:javaee8` 이미지를 지시합니다.|
|`ibm/liberty:microProfile1`|이 이미지에는 [MicroProfile 1.x ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://microprofile.io/)로 지정된 기능을 제공하는 기능이 포함됩니다.|
|`ibm/liberty:microProfile2`|이 이미지에는 [MicroProfile 2.x ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://microprofile.io/)로 지정된 기능을 제공하는 기능이 포함됩니다.|
|`ibm/liberty:springBoot1`|이 이미지에는 [Spring Boot 1.x ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://spring.io/projects/spring-boot)로 지정된 기능을 제공하는 기능이 포함됩니다.|
|`ibm/liberty:springBoot2`|이 이미지에는 [Spring Boot 2.x ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://spring.io/projects/spring-boot)로 지정된 기능을 제공하는 기능이 포함됩니다.|
|`ibm/liberty:webProfile7`|이 이미지에는 Java EE 7 웹 프로파일 준수에 필요한 모든 기능이 포함됩니다.|
|`ibm/liberty:webProfile8`|이 이미지에는 Java EE 8 웹 프로파일 준수에 필요한 모든 기능이 포함됩니다.|
|`ibm/liberty:javaee7`|이 이미지에는 Java EE 7 전체 플랫폼 준수에 필요한 기능과 함께 `ibm/liberty:webProfile7` 이미지의 모든 기능이 포함됩니다.|
|`ibm/liberty:javaee8`|이 이미지에는 Java EE 8 전체 플랫폼 준수에 필요한 기능과 함께 `ibm/liberty:webProfile8` 이미지의 모든 기능이 포함됩니다.|
{: caption="표 1. 각 <code>ibm/liberty</code> 이미지에 포함될 기능" caption-side="top"}

## 사용 제한사항
{: #ibmliberty_usage}

다음 표에서는 {{site.data.keyword.cloud_notm}}에서 `ibm/liberty` 이미지의 무료 사용량에 적용되는 제한사항을 보여줍니다.
{:shortdesc}

`ibm/liberty` 이미지의 가격은 {{site.data.keyword.cloud_notm}}에서 사용하는 컨테이너의 가격과 별도로 책정됩니다.
{:tip}

|환경|무료 사용량 제한사항|
|-----------|-----------------------|
|개발|`ibm/liberty` 이미지의 **무제한** 무료 사용량입니다.|
|프로덕션|`ibm/liberty` 이미지의 무료 사용량은 이미지를 실행하는 모든 컨테이너 인스턴스에서 **최대 2GB Java 힙 영역**으로 제한됩니다. 예를 들어, 2 x 1GB 또는 4 x 512MB 힙 liberty 인스턴스를 무료로 보유할 수 있습니다.|
{: caption="표 2. 가격" caption-side="top"}

컨테이너 인스턴스의 Java 힙 사용량을 모니터하려면 [CLI로 컨테이너의 Java 힙 영역 사용량 모니터링](#ibmliberty_monitor_heap)을 참조하십시오.

Docker 허브의 [websphere-liberty image ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://hub.docker.com/_/websphere-liberty/)에 있는 라이센스 섹션에서 IBM 인증 이미지의 이용 약관을 검토하십시오.

## 시작하기
{: #ibmliberty_get_started}

{{site.data.keyword.cloud_notm}} 카탈로그에서 무료 `ibm/liberty` 이미지 중 하나를 사용하거나 자체 프로덕션 라이센스 이미지를 선택하여 단일 컨테이너 또는 컨테이너 그룹을 작성하십시오.
{:shortdesc}

시작하기 전에, `ibm/liberty` 이미지의 [사용량 제한사항](#ibmliberty_usage)을 검토하십시오.
{: important}

1. 카탈로그의 측면 분할창에서 **컨테이너** > **IBM Cloud Container Registry** > **IBM 공용 저장소**를 선택하십시오. 컨테이너를 빌드할 `ibm/liberty` 이미지를 검색하십시오. 자체 프로덕션 라이센스 이미지를 작성하고 이를 {{site.data.keyword.cloud_notm}}에 배치한 경우에는 카탈로그에서 이 이미지를 선택하십시오. 컨테이너 작성 페이지가 열립니다.
2. **태그/버전** 목록에서 사용하려는 `ibm/liberty` 이미지의 버전을 선택하십시오.
3. 이미지에서 컨테이너 빌드, 클러스터 설정 및 클러스터에 앱 배치에 대한 자세한 정보를 보려면 다음 링크를 사용하십시오.

    - [이미지에서 컨테이너 빌드](/docs/containers?topic=containers-images#images)
    - [IBM Cloud Kubernetes Service 시작하기](/docs/containers?topic=containers-getting-started#getting-started)
    - [클러스터에 앱 배치](/docs/containers?topic=containers-app#app)

    `ibm/liberty` 이미지에서는 9080 포트가 공용이어야 합니다. {{site.data.keyword.cloud_notm}} 대시보드에서 컨테이너를 작성할 때 기본적으로 **공용 포트** 필드에서 포트가 추가됩니다. CLI에서 컨테이너를 작성하려면 `--port=9080` 옵션이 포함된 `kubectl run` 명령을 실행하여 포트를 공개하십시오.
    {:tip}

## CLI로 컨테이너에 대한 Java 힙 영역 사용량 모니터링
{: #ibmliberty_monitor_heap}

`ibm/liberty` 이미지에서 컨테이너를 작성한 후에는 특정 팟(Pod)의 메트릭을 보고 Java 힙 사용량을 검토할 수 있습니다. Java 힙 영역은 런타임 중에 Java 애플리케이션이 사용할 수 있는 메모리입니다.
{:shortdesc}

1. 메트릭을 볼 팟(Pod)의 이름을 가져오십시오.
  
   ```
    kubectl get pods
   ```
   {: pre}

2. 특정 팟(Pod) 및 해당 컨테이너에 대한 메트릭을 참조하십시오.

   ```
    kubectl top pod POD_NAME --containers
   ```
   {: pre}

3. Java 힙 사용량을 검토하려면 **RSS** 메모리 stat에 액세스해야 합니다([Get a Shell to a Running Container ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/) 참조). Java 힙 사용량은 KB 단위로 표시됩니다. 힙 사용량이 모든 인스턴스에서 2097152KB(2GB) 미만이면 WebSphere Application Server 라이센스를 구매할 필요가 없습니다.

4. WebSphere Application Server 인스턴스의 최대 힙 사용량을 조정하십시오. 자세한 정보는 [Setting generic JVM arguments in the WebSphere Application Server V8.5 Liberty profile ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www-01.ibm.com/support/docview.wss?uid=swg21596474)을 참조하십시오.

## WebSphere Application Server 라이센스 가져오기
{: #ibm/liberty_license}

WebSphere Application Server 라이센스는 필요한 PVU\(Processor Value Unit\) 수를 기반으로 합니다. PVU는 IBM Middleware 소프트웨어의 라이센싱에 대한 측정 단위입니다. PVU 수는 소프트웨어에 사용할 수 있는 프로세서\(코어\) 수를 나타냅니다.
{:shortdesc}

{{site.data.keyword.cloud_notm}}의 모든 컨테이너 크기에는 WebSphere Application Server 라이센스에서 사용 가능해야 하는 특정 수의 PVU 인타이틀먼트가 있어야 합니다. 따라서 라이센스를 구매하기 전에 `ibm/liberty` 컨테이너를 계획해야 합니다.

WebSphere Application Server 라이센스를 구매하려면 [IBM Service ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://cloud.ibm.com/catalog/services/websphere-application-server)에 문의하십시오. WebSphere Application Server v8.5 이상에 대한 라이센스가 이미 있으면, 컨테이너의 배치를 위해 기존 인타이틀먼트의 미사용 PVU를 사용할 수 있습니다.

라이센스를 구매한 후 추가 PVU가 필요하면 [IBM Service ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://cloud.ibm.com/catalog/services/websphere-application-server)에 문의하여 양을 늘릴 수 있습니다.

## {{site.data.keyword.containershort_notm}}에서 사용될 프로덕션 라이센스 `ibm/liberty` 이미지 작성
{: #ibmliberty_prod_image}

WebSphere Application Server 라이센스를 사용하여 {{site.data.keyword.containershort_notm}}에서 사용할 수 있는 프로덕션 라이센스 `ibm/liberty` 이미지를 작성합니다. 다음 태스크 중 하나를 선택하십시오.
{:shortdesc}

- [Upgrade the image from Docker Hub to a production image ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade).
- [Build your own production-licensed image ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://github.com/WASdev/ci.docker/tree/master/ga)

프로덕션 라이센스가 부여된 이미지를 작성한 후 {{site.data.keyword.containershort_notm}}와 함께 사용하도록 [이미지를 사설 레지스트리에 푸시](/docs/services/Registry?topic=registry-getting-started#getting-started)하십시오.

## 제공된 이미지에서 이미지 작성
{: #ibmliberty_creating_image}

`ibm/liberty` 이미지 중 하나를 자체 앱 코드가 포함된 하위 이미지를 작성하기 위한 상위로서 사용할 수 있습니다. 샘플 Dockerfile을 사용자 정의하고 컴퓨터에서 이미지를 빌드하십시오. 그런 다음 조직의 사설 이미지 레지스트리에 이미지를 추가하고 이 이미지를 사용하여 컨테이너를 작성할 수 있습니다.
{:shortdesc}

시작하기 전에 다음과 같은 단계를 고려하십시오.

- 앱 코드를 WAR, EAR 또는 OSGi 파일에 빌드합니다.
- 이미지가 빌드될 디렉토리에 파일을 복사합니다.

`ibm/liberty` 이미지에서 앱 코드를 사용하여 이미지를 작성하려면 다음 단계를 완료하십시오.

1. 문서 편집기를 사용하여 이름이 `Dockerfile`인 파일을 작성하고 다음 정보를 이 파일에 복사하십시오.

   ```
   FROM icr.io/ibm/liberty:<tag>
   COPY <app_name>.<file_extension> /config/dropins/
   ```
   {: pre}

    `/config` 디렉토리는 `/opt/ibm/wlp/usr/servers/defaultServer`의 바로 가기입니다.
    {: note}

2. `<tag>`를 앱에 필요한 기능이 포함된 `ibm/liberty` 이미지의 버전으로 바꾸십시오.

3. `<app_name>`을 앱 파일의 이름으로 바꾸십시오.

4. `<file_extension>`을 `.war`, `.ear` 또는 `.eba`로 바꾸십시오.

5. Dockerfile에 앱의 기타 종속 항목을 추가하십시오.

6. 이미지를 빌드한 다음 사설 이미지 레지스트리에 푸시하십시오. 자세한 정보는 [{{site.data.keyword.registrylong_notm}} 시작하기](/docs/services/Registry?topic=registry-getting-started#getting-started)를 참조하십시오.

컨테이너 내부의 `/logs` 디렉토리에 Liberty 로그 파일을 작성하도록 모든 `ibm/liberty` 이미지가 구성됩니다. Liberty 서버에서 쓰는 기타 모든 파일은 `/opt/ibm/wlp/output/defaultServer` 디렉토리에 작성됩니다. 바로 가기(`/output`)를 사용하여 이러한 파일에 액세스할 수 있습니다.
{:tip}

## `ibm/liberty` Dockerfile 참조
{: #ibmliberty_reference_dockerfile}

`ibm/liberty` 이미지 Dockerfile은 [WASdev/ci.docker GitHub 저장소 ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://github.com/WASdev/ci.docker/tree/master/ga)에서 참조할 수 있습니다.
{:shortdesc}
