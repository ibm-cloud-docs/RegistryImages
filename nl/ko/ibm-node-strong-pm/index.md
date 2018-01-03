---

copyright:
  years: 2015, 2017
lastupdated: "2017-10-30"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}

# ibm-node-strong-pm 이미지 시작하기
{: #getting_started_node_strong}

{{site.data.keyword.IBM}} Node with StrongLoop\(**ibm-node-strong-pm**\) 이미지는 {{site.data.keyword.containerlong_notm}}용으로 제공됩니다.
{:shortdesc}

## 작동 방식 
{: #how_it_works}

**ibm-node-strong-pm** 이미지를 사용하여 실행 중인 [StrongLoop Process Manager](https://www.strongloop.com) 인스턴스를 배치하는 단일 컨테이너를 작성하십시오. Strongloop Process Manager를 사용하면 원격 시스템에서 구현된 {{site.data.keyword.IBM_notm}} 클라우드에서 Node.js 앱을 배치, 관리 및 모니터할 수 있습니다.
{:shortdesc}

원격 시스템에 StrongLoop Arc의 인스턴스가 설치되어 있어야 합니다. StrongLoop Arc는 StrongLoop 플랫폼용 그래픽 사용자 인터페이스이고, Node.js 앱을 빌드, 프로파일링 및 모니터링하기 위한 도구를 포함합니다. StrongLoop Arc로 앱을 빌드한 다음 StrongLoop Process Manager를 실행하는 {{site.data.keyword.Bluemix_notm}}의 컨테이너에 푸시하여 배치합니다. 원격 시스템과 컨테이너 사이의 연결을 설정하려면 공인 IP 주소를 컨테이너에 바인드한 다음 이 IP 주소를 StrongLoop Arc의 StrongLoop Process Manager 서버로 설정해야 합니다. StrongLoop Process Manager를 통해 앱을 배치하면 StrongLoop Process Manager에서 앱의 성능과 메트릭을 모니터할 수 있습니다. 앱을 업데이트하거나 스케일링하려면 StrongLoop Arc를 사용하여 변경한 다음 변경사항을 컨테이너에 푸시해야 합니다.

**참고:** **ibm-node-strong-pm**은 {{site.data.keyword.Bluemix_notm}}의 컨테이너 그룹과 사용하도록 지원되지 않습니다.

## 포함된 항목
{: #whats_included}

-   **ibmnode:latest** 이미지에 포함된 모든 소프트웨어 패키지입니다. {{site.data.keyword.Bluemix_notm}}용 **ibmnode** 이미지 시작하기의 [포함된 항목](/docs/services/RegistryImages/ibmnode/index.html#getting_started_node)을 참조하십시오.
-   StrongLoop Process Manager

## 시작하기
{: #how_to_get_started}

{{site.data.keyword.Bluemix_notm}} Catalog의 **ibm-node-strong-pm** 이미지를 사용하여 {{site.data.keyword.containershort_notm}}의 단일 컨테이너를 사용해 볼 수 있습니다. 이미지는 컨테이너가 사용 가능하면 바로 액세스할 수 있는 StrongLoop Process Manager의 실행 중 인스턴스로 시작됩니다. Process Manager에 연결된 StrongLoop Arc 인스턴스를 사용하면 Node.js 애플리케이션을 푸시하고 이 애플리케이션에 액세스할 수 있습니다.
{:shortdesc}

**참고:** 시작하기 전에 [websphere-liberty Docker 라이브러리](https://github.com/docker-library/docs/tree/master/websphere-liberty)의 사용 섹션에서 {{site.data.keyword.IBM_notm}} 인증 이미지의 이용 약관을 검토하십시오.

1.  카탈로그에서 **컨테이너**를 선택하고 컨테이너를 빌드할 **ibm-node-strong-pm** 이미지를 선택하십시오.
2.  단일 컨테이너를 작성하십시오.
    -   **크기** 필드에 메모리가 1GB 이상인\(**작음**\) 컨테이너 크기를 선택하십시오.
    -   **공용 포트** 필드에서 포트 8701 및 3001을 입력하십시오. 공인 IP 주소를 컨테이너에 바인드하기 위해 컨테이너를 {{site.data.keyword.Bluemix_notm}} 및 전제조건에 배치하면 이러한 포트가 자동으로 노출됩니다. 포트 8701을 사용하여 컨테이너의 StrongLoop Process Manager에 액세스합니다. 포트 3001은 StrongLoop Process Manager에 배치된 후에 앱이 청취하는 포트입니다.

        **참고:** StrongLoop Process Manager에 추가 앱을 배치하려면 추가 포트를 노출하십시오. 포트는 3002로 시작하여 연속적이어야 합니다. 예를 들어, 3002, 3003, 3004 등입니다.

    -   자세한 정보는 [{{site.data.keyword.Bluemix_notm}} 대시보드를 사용하여 단일 컨테이너 작성](/docs/containers/container_single_ui.html#gui)을 참조하십시오.
3.  컨테이너에 공인 IP 주소를 바인드하십시오. 자세한 정보는 [IP 주소 요청 및 바인딩](/docs/containers/container_single_ui.html#container_cli_ips)을 참조하십시오.

## 원격 시스템에 StrongLoop Arc 설정
{: #UsingSLA}

{{site.data.keyword.Bluemix_notm}}의 **ibm-node-strong-pm** 이미지에서 단일 컨테이너를 작성한 후에 원격 시스템에서 StrongLoop Arc를 설정하십시오.
{:shortdesc}

시작하기 전에 원격 시스템에 Node.js 및 노드 패키지 관리자\(npm\)를 설치하십시오.

1.  다음 명령을 입력하여 StrongLoop를 다운로드하여 설치하십시오. StrongLoop에는 Arc를 실행하는 데 필요한 모든 패키지가 포함되어 있습니다.

    ```
    npm install -g strongloop
    ```
    {: pre}

2.  StrongLoop가 설치된 후 StrongLoop Arc를 실행하십시오.

    ```
    slc arc
    ```
    {: pre}

3.  프롬프트가 표시되면 StrongLoop에 등록하거나 이에 로그인하십시오.
4.  컨테이너에서 실행되는 StrongLoop Process Manager를 StrongLoop Arc의 Process Manager 목록에 추가하십시오.
    1.  Arc 모듈 선택기에서 **Process Manager**로 이동하십시오.
    2.  공용 포트 8701 및 컨테이너에 바인드된 공인 IP 주소를 Arc Process Manager에 추가하십시오.
    3.  **활성화**를 클릭하십시오.

## StrongLoop Arc를 사용하여 컨테이너에 Node.js 앱 푸시
{: #pushing_app_to_container}

컨테이너에서 실행되는 StrongLoop Process Manager를 원격 시스템의 StrongLoop Arc 클라이언트와 연결한 후에 Node.js 앱을 빌드하고 {{site.data.keyword.Bluemix_notm}}에 푸시합니다.
{:shortdesc}

1.  Arc 모듈 선택기에서 **빌드 맻 배치**로 이동하십시오.
2.  Arc 사용자 인터페이스 지시사항을 따라 앱을 빌드하십시오.

    **중요:** StrongLoop Arc를 사용하여 작성한 Node.js에서 환경 변수 PORT와 컨테이너를 작성할 때 노출한 포트를 모두 청취 중인지 확인하십시오.

    예

    ```
    app.listen(process.env.PORT || 3001);
    ```
    {: pre}

3.  Arc 사용자 인터페이스 지시사항을 따라 앱을 배치하십시오. {{site.data.keyword.Bluemix_notm}}에 앱을 배치하기 위해 구성한 Process Manager를 선택하십시오.
4.  배치 프로세스가 완료될 때까지 대기하십시오.
5.  앱에 액세스하십시오.
    1.  웹 브라우저를 여십시오.
    2.  <var class="keyword varname">IP_ADDRESS:PORT</var> 형식으로 컨테이너의 IP 주소와 포트를 입력하십시오.

        예

        ```
        192.0.2.26:3001
        ```
        {: pre}

    3.  {{site.data.keyword.Bluemix_notm}}에 배치된 모든 앱에 대해 이 단계를 반복하십시오.
6.  Arc의 추적, 메트릭 및 프로파일러를 사용하여 {{site.data.keyword.Bluemix_notm}}에서 실행될 때 앱에 대한 성능 정보를 가져오십시오. 자세한 정보는 StrongLoop 문서의 [운영 노드 애플리케이션](https://docs.strongloop.com/display/SLC/Operating+Node+applications)을 참조하십시오.

## **ibm-node-strong-pm** Dockerfile 참조 
{: #reference_dockerfile}

이 Dockerfile은 {{site.data.keyword.Bluemix_notm}} Catalog에 **ibm-node-strong-pm** 이미지를 작성하는 데 사용합니다. 이 정보는 참조 전용입니다. 이 이미지의 빌드 버전에 포함된 기타 파일은 제공되지 않습니다.

```
FROM registry.ng.bluemix.net/ibmnode:latest
RUN useradd -ms /bin/bash strong-pm \    
    && chown -R strong-pm:strong-pm /usr/local \    
    && su strong-pm -c "npm install -g strong-pm && npm cache clear"

# Set up some semblance of an environment
WORKDIR /home/strong-pm
ENV HOME=/home/strong-pm PORT=3000

# Run as non-privileged user inside container
USER strong-pm

# Expose strong-pm and application ports
EXPOSE 8701
EXPOSE 3001-3010

ENTRYPOINT ["/usr/local/bin/sl-pm", "--base", ".", "--listen", "8701"]
```
{: screen}


