---

copyright:
  years: 2016, 2017
lastupdated: "2017-10-25"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# *ibm-integration-bus* 이미지 시작하기
{: #iib}

**ibm-integration-bus** 이미지가 {{site.data.keyword.containerlong}}를 위해 제공됩니다. 여기에는 IBM Integration Bus for Developers Edition이 포함됩니다.
{:shortdesc}


## 작동 방식
{: #how_it_works}

IBM Integration Bus for Developers에는 고유 통합 솔루션의 개발을 시작하는 데 필요한 모든 사항이 포함되어 있습니다.
{:shortdesc}

통합 솔루션을 작성한 후 **ibm-integration-bus** 이미지를 사용하여 {{site.data.keyword.Bluemix_notm}}에서 단일 컨테이너를 프로비저닝할 수 있습니다. 그런 다음 터미널에서 또는 웹 UI를 사용하여 통합 솔루션을 이 컨테이너에 배치할 수 있습니다.

**참고**: 개발 및 단위 테스트에만 **ibm-integration-bus** 이미지를 사용할 수 있습니다. 또한 이미지를 사용하여 제품을 탐색하고 튜토리얼을 통해 배우며 IBM Integration Bus가 조직에 제공할 수 있는 기여를 평가할 수 있습니다. 


## 포함된 항목
{: #whats_included}

이 IBM Integration Bus 이미지에는 개발 및 단위 테스트에 사용할 수 있는 제품의 전기능 버전인 IBM Integration Bus for Developers 버전 10\(Developer Edition이라고도 함\)의 소프트웨어 패키지가 포함되어 있습니다. 무료로 이 버전을 다운로드할 수 있으며 라이센스 조건에 따라 자유롭게 사용할 수 있습니다.
{:shortdesc}

IBM Integration Bus for Developers 버전 10.0은 Windows™ 64비트 운영 체제 및 x86-64 운영 체제용 Linux™에서 사용할 수 있습니다. 모든 제품 필수 소프트웨어는 다운로드 패키지에 포함되어 있습니다. 

IBM Integration Bus for Developers에 포함된 기능에 대한 정보는 [IBM Knowledge Center의 IBM Integration Bus ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.msgbroker.helphome.doc/help_home_msgbroker.htm){: new_window}를 참조하십시오.

[IBM Integration Bus for Developers 다운로드 페이지 ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.ibm.com/marketing/iwm/iwm/web/pick.do?source=swg-wmbfd){: new_window}에서 Developer Edition에 액세스할 수 있습니다.


## 사용 제한사항
{: #usage}

다음 표에서는 {{site.data.keyword.Bluemix_notm}}에서 **ibm-integration-bus** 이미지의 무료 사용량에 적용되는 제한사항을 보여줍니다.
{:shortdesc}

**참고**: **ibm-integration-bus** 이미지의 가격은 {{site.data.keyword.Bluemix_notm}}에서 사용하는 컨테이너의 가격 책정과 별도로 책정됩니다.

|환경|무료 사용량 제한사항|
|-----------|-----------------------|
|개발|개발 및 단위 테스트용 단일 컨테이너를 작성하기 위해 **ibm-integration-bus** 이미지를 **무제한**으로 무료 사용합니다. |
|프로덕션 |Developer Edition은 초당 하나의 트랜잭션 처리로 제한되며 프로덕션 환경에서 사용이 지원되지 않습니다. |
{: caption="표 1. ibm-integration-bus 이미지의 무료 사용량 제한사항" caption-side="top"}


## 라이센스
{: #license}

라이센스에 대한 정보.
{:shortdesc}

*   Dockerfiles와 관련 스크립트는 [Apache License 2.0 ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](http://www.apache.org/licenses/LICENSE-2.0.html){: new_window}에 의거하여 라이센스가 부여됩니다.
*   [IBM Integration Bus for Developers 버전 10.0의 라이센스 정보 ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](http://www.ibm.com/software/sla/sladb.nsf/displaylis/1BD32B141C9FDAE38525802C0055A956?OpenDocument){: new_window}.

    **참고**: 라이센스에서 추가 배포를 허용하지 않습니다. 이미지로 된 IBM Integration Bus는 개발 및 단위 테스트를 위한 사용량을 제한합니다.



## 시작하기
{: #get_started}

{{site.data.keyword.Bluemix_notm}} 카탈로그에서 **ibm-integration-bus** 이미지를 사용하거나 자체 프로덕션 라이센스 이미지를 선택하여 단일 컨테이너를 작성하십시오.
{:shortdesc}

**중요**: 시작하기 전에 **ibm-integration-bus** 이미지의 [사용량 제한사항](#usage)을 검토하십시오. 

**참고**: 별표(\*)로 표시된 모든 단계에서 고유 Docker 명령을 사용할 수 있습니다.

   다음 단계를 완료하여 컨테이너에서 IBM Integration Bus를 실행하십시오.

1.  [{{site.data.keyword.Bluemix_notm}} UI에서 ibm-integration-bus 이미지를 기반으로 하는 컨테이너 프로비저닝](#provision)
2.  [컨테이너와 IBM Integration Bus 설치 유효성 검증](#validate)
3.  [플로우 작성 및 배치](#createdeployflow)
4.  [컨테이너의 IBM Integration Bus 로그 모니터](#monitor)
5.  [컨테이너에서 실행 중인 IBM Integration Bus 리소스 관리](#manage)


## **ibm-integration-bus** 이미지를 기반으로 하는 컨테이너 프로비저닝
{: #provision}

{{site.data.keyword.IBM_notm}}에서 제공하는 **ibm-integration-bus** 이미지를 기반으로 {{site.data.keyword.Bluemix_notm}}에서 Docker 컨테이너 프로비저닝
{:shortdesc}

  다음 단계를 완료하십시오.

1.  {{site.data.keyword.Bluemix_notm}}에 로그인하십시오. 
2.  카탈로그에서 **컨테이너**를 선택하고 컨테이너를 빌드할 **ibm-integration-bus** 이미지를 선택하십시오.
3.  단일 인스턴스 컨테이너를 작성하려면 **단일**을 선택하십시오. 이는 개발 및 테스트용도로 사용될 수 있습니다.
4.  컨테이너의 이름을 입력하십시오(예: `iib`). 
5.  컨테이너의 크기를 선택하십시오.
6.  **공인 IP 주소** 필드에서 *공인 IP 바인드*를 선택하십시오. 
7.  **공용 포트** 필드에 `4414, 7800`을 지정하십시오. 
8.  **고급** 옵션을 펼치고 **새 환경 변수 추가**를 클릭하십시오. 
9.  다음 환경 변수를 추가하십시오. 

    <ul>
      <li>값이 <i>accept</i>인 LICENSE
      <li>값이 &lt;MYNODE&gt;인 NODENAME. &lpar;여기서 &lt;MYNODE&gt;는 선택한 노드 이름임&rpar;</li>
      <li>값이 <i>/var/log/syslog</i>인 LOG_LOCATIONS
    </ul>

    **참고**: LICENSE 및 NODENAME 특성의 값을 지정하지 않으면 컨테이너가 작성되지만 시작되지는 않습니다.

    LOG_LOCATIONS 특성은 {{site.data.keyword.Bluemix_notm}} UI를 통해 노출할 컨테이너에 로그를 지정합니다.
10. **작성**를 클릭한 다음 컨테이너가 실행을 시작하기를 기다리십시오. 


## 컨테이너와 IBM Integration Bus 설치 유효성 검증
{: #validate}

IBM Integration Bus를 실행하는 컨테이너가 {{site.data.keyword.Bluemix_notm}}에 배치되고 나면 컨테이너의 상태를 확인하고 IBM Integration Bus 설치의 유효성을 검증하십시오.
{:shortdesc}

  다음 단계를 완료하여 컨테이너에서 IBM Integration Bus의 설정 및 구성을 검증하십시오.
  
1.  [고유 Docker CLI를 사용하도록 {{site.data.keyword.containershort_notm}} 플러그인\(`bx ic`\) 설정](/docs/containers/container_cli_cfic_install.html)에 설명된 대로 {{site.data.keyword.containershort_notm}} CLI를 설정하십시오.

2.  터미널에서 {{site.data.keyword.Bluemix_notm}}에 로그인하십시오. 다음 명령을 실행하십시오.

    <ol>
       <li>
          <pre class="codeblock">bx login</pre>
       </li>
       <li>
          <pre class="codeblock">bx ic init</pre>
       </li>
    </ol>

3.  컨테이너의 상태를 확인하십시오.\*

    ```
    bx ic ps
    ```
    {: pre}

    **참고**: [{{site.data.keyword.containershort_notm}}에 로그인](/docs/containers/container_cli_cfic_install.html#container_cli_login)할 때 이 명령에서 **bx ic**를 **docker**로 바꾸고 고유 Docker 명령을 사용하도록 환경 변수를 설정할 수 있습니다. 별표(\*)로 표시된 모든 단계에서 고유 Docker 명령을 사용할 수 있습니다.

    ```
    $ bx ic ps
    CONTAINER ID        IMAGE                                                       COMMAND             CREATED             STATUS                 PORTS                                                        NAMES
    46d9ec1e-952        registry.ng.bluemix.net/ibm-integration-bus:latest   ""                  2 minutes ago       Running a minute ago   xxx.xxx.xxx.xxx:4414->4414/tcp, xxx.xxx.xxx.xxx:7800->7800/tcp   iib
    ```
    {: screen}

4.  Bash 세션을 컨테이너에 연결하십시오.\*

    ```
    bx ic exec -it <container_name> /bin/bash
    ```
    {: pre}

    여기서 *container_name*은 컨테이너의 이름입니다.

    예를 들면 다음과 같습니다.

    ```
    $ bx ic exec -it iib /bin/bash
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

5.  [명령 환경: Linux 및 UNIX™ 시스템 ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](http://www.ibm.com/support/knowledgecenter/SSMKHH_9.0.0/com.ibm.etools.mft.doc/an26230_.htm){: new_window}에 설명된 대로 **mqsiprofile** 명령을 실행하여 환경을 초기화하십시오.

    환경을 초기화하는 명령은 디렉토리: `/opt/ibm/iib-10.0.0.6/server/bin/`에서 사용할 수 있습니다.

    예를 들면 다음과 같습니다.

    ```
    (IIB_10:)iibuser@instance-0098858e:/$ . ./opt/ibm/iib-10.0.0.6/server/bin/mqsiprofile

    MQSI 10.0.0.6
    /opt/ibm/iib-10.0.0.6/server

    # Check the environment variable <MQSI_WORKPATH> is set
    (IIB_10:)iibuser@instance-0098858e:/$ echo $<MQSI_WORKPATH>
    /var/mqsi
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

6.  **mqsilist** 명령을 사용하여 노드의 상태를 표시하십시오. 노드의 상태는 `running`으로 나열되어야 합니다. 예를 들면 다음과 같습니다.

    ```
    (IIB_10:)iibuser@instance-0098858e:/$ mqsilist
    BIP1325I: Integration node 'MYNODE' with administration URI 'http://instance-0098858e:4414' is running.
    BIP8071I: Successful command completion.
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

이제 컨테이너가 실행되고 있으며 지원되는 방법을 사용하여 통합 솔루션을 컨테이너에 배치할 수 있습니다. 


## 컨테이너의 IBM Integration Bus 로그 모니터
{: #monitor}

{{site.data.keyword.Bluemix_notm}} UI를 통해 또는 명령행에서 로그를 모니터링하십시오.
{:shortdesc}

시작하기 전에 {{site.data.keyword.containershort_notm}} CLI를 구성하여 명령행에서 로그를 모니터하십시오. 자세한 정보는 [{{site.data.keyword.containershort_notm}} 플러그인 설정(`bx ic`)](/docs/containers/container_cli_cfic_install.html)을 참조하십시오.

**ibm-integration-bus** 이미지는 IBM Integration Bus 메시지를 컨테이너의 `/var/log/syslog` 로그에 출력하도록 구성되어 있습니다.

다음 방법 중 하나를 선택하여 IBM Integration Bus 로그를 모니터하십시오.

*  컨테이너를 프로비저닝할 때 LOG_LOCATIONS 환경 변수를 구성한 경우 {{site.data.keyword.Bluemix_notm}} UI에서 로그를 모니터할 수 있습니다.

    다음 단계를 완료하여 로그에 액세스하십시오.

    <ol>
       <li>컨테이너 대시보드에서 모니터할 컨테이너를 선택하십시오.</li>
       <li><b>모니터링 및 로깅</b>을 선택하십시오.</li>
       <li><b>로깅</b>을 선택하십시오. 대시보드에 로그 항목이 나열됩니다. </li>
       <li>로그 보기를 사용자 정의하려면 로깅 페이지에서 <b>고급 보기</b>를 선택하여 Kibana를 여십시오. 시작하기에 대한 자세한 정보는
           <a href="https://console.bluemix.net/docs/containers/container_planning.html#container_planning_monlog" target="_blank">모니터링 및 로깅</a>을 확인하십시오.
       </li>
    </ol>

*  터미널에서 Docker 또는 {{site.data.keyword.containerlong_notm}} CLI를 사용하여 메시지를 모니터하십시오. 다음 단계를 완료하십시오.

    <ol>
       <li>
        터미널을 구성하십시오.

        <ul>
        <li>로컬 Docker 호스트를 직접 관리하기 위해 Docker CLI를 사용하는 동안 {{site.data.keyword.Bluemix_notm}}에서 컨테이너를 관리하려면 {{site.data.keyword.containerlong_notm}} CLI를 사용하려면 다음 단계를 완료하여 환경을 설정하십시오.
           <ol>
             <li>
               <pre class="codeblock">bx login</pre>
             </li>
             <li>
               <pre class="codeblock">bx ic init</pre>
             </li>
           </ol>
        </li>
        <li>{{site.data.keyword.Bluemix_notm}}에서 컨테이너를 관리하기 위해 Docker CLI를 사용하려면 다음 단계를 완료하십시오.
          <ol>
            <li>
              <pre class="codeblock">bx login</pre>
            </li>
            <li>
              <pre class="codeblock">bx ic init</pre>
               환경 변수 &lt;DOCKER_HOST&gt;, &lt;DOCKER_CERT_PATH&gt; 및 &lt;DOCKER_TLS_VERIFY&gt;에 제공된 값을 복사하십시오.
            </li>
            <li>
            {{site.data.keyword.containerlong_notm}}에 연결하기 위해 다음 변수를 설정하여 로컬 Docker 환경을 대체하십시오.
               <ul>
                 <li><pre class="codeblock">export DOCKER_HOST=&lt;your_host_value&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;your_cert_path_value&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
               </ul>
                <b>참고</b>: 이 옵션에서는 일부 Docker 명령만 지원됩니다.
            </li>
          </ol>
        </li>
      </ul>
      </li>
      <li>
         로그에 액세스하십시오. 다음 옵션 중 하나를 선택하십시오.

      <ul>
         <li>터미널이 {{site.data.keyword.containerlong_notm}} CLI 명령을 실행하도록 구성된 경우 다음 명령을 실행하십시오.

            <pre class="codeblock">bx ic exec &lt;container_id&gt; tail -f /var/log/syslog</pre>

            여기서 *container_id*는 컨테이너의 이름입니다.

            예를 들면 다음과 같습니다.

            <pre class="screen">bx ic exec iib tail -f /var/log/syslog</pre>

          </li>
          <li>
             터미널이 Docker CLI 명령을 실행하도록 구성된 경우 다음 명령을 실행하십시오.

            <pre class="codeblock">docker exec &lt;container_id&gt; tail -f /var/log/syslog</pre>

            여기서 *container_id*는 컨테이너의 이름입니다.

            예를 들면 다음과 같습니다.

            <pre class="screen">docker exec iib tail -f /var/log/syslog</pre>

          </li>
        </ul>
       </li>
    </ol>


## 컨테이너에서 실행 중인 IBM Integration Bus 리소스 관리
{: #manage}

IBM Integration Bus 웹 UI를 실행하거나 터미널에서 명령을 실행하여 컨테이너에서 실행 중인 IBM Integration Bus 리소스를 관리하십시오.
{:shortdesc}

  다음 옵션 중 하나를 선택하여 컨테이너에서 IBM Integration Bus 리소스를 관리하십시오.

*  [IBM Integration Bus 웹 UI를 실행](#launchwebUI)하고 그래픽 방식으로 IBM Integration Bus를 관리하십시오.
*  터미널에서 Docker 또는 {{site.data.keyword.containershort_notm}} CLI를 사용하여 IBM Integration Bus 리소스를 사용하십시오.
    *   [{{site.data.keyword.containerlong_notm}} CLI를 사용하여 컨테이너에서 관리 명령 실행](#admin_commands_containers_cli)
    *   [Docker CLI를 사용하여 컨테이너에서 관리 명령 실행](#admin_commands_docker_cli)


## IBM Integration Bus 웹 UI 실행
{: #launchwebUI}

브라우저의 컨테이너에서 실행 중인 IBM Integration Bus 리소스를 관리하려면 웹 UI를 실행하십시오.
{:shortdesc}

[{{site.data.keyword.Bluemix_notm}}의 ibm-integration-bus 이미지 시작하기](#get_started)에 노출한 호스트에 브라우저를 연결하려면 다음 단계를 따르십시오. IBM Integration Bus 웹 사용자 인터페이스가 표시됩니다.

1.  {{site.data.keyword.Bluemix_notm}} UI에서 컨테이너를 선택하고 컨테이너의 상태가 `Running`인지 확인하십시오.
2.  컨테이너 세부사항을 확인하여 공인 IP를 찾으십시오.
3.  웹 브라우저에서 다음 URL을 여십시오.

    ```
    http://<DockerContainerPublicIP>:4414
    ```
    {: pre}

    여기서 *DockerContainerPublicIP*는 이전 단계에서 확인한 컨테이너의 공인 IP입니다. 

4.  브라우저에서 웹 사용자 인터페이스가 열리고 IBM Integration Bus를 관리할 수 있게 됩니다.


## {{site.data.keyword.containerlong_notm}} CLI를 사용하여 컨테이너에서 관리 명령 실행
{: #admin_commands_containers_cli}

{{site.data.keyword.containershort_notm}} CLI를 사용하여 컨테이너에서 직접 IBM Integration Bus 관리 명령을 실행하십시오.
{:shortdesc}

시작하기 전에 {{site.data.keyword.containershort_notm}} CLI를 구성하여 명령행에서 관리 명령을 실행하십시오. 자세한 정보는 [{{site.data.keyword.containershort_notm}} 플러그인 설정(`bx ic`)](/docs/containers/container_cli_cfic_install.html)을 참조하십시오.

  다음 단계를 완료하여 터미널을 설정하고 **bx ic** 명령을 실행하여 IBM Integration Bus를 관리하십시오.

1.  터미널에서 {{site.data.keyword.Bluemix_notm}}에 로그인하십시오. 다음 명령을 실행하십시오.

      <ol>
        <li>
           <pre class="codeblock">bx login</pre>
        </li>
        <li>
           <pre class="codeblock">bx ic init</pre>
        </li>
       </ol>
           
2.  Bash 세션을 컨테이너에 연결하여 대화식 세션을 설정하십시오.\*

    ```
    bx ic exec -it <container_name> /bin/bash
    ```
    {: pre}

    여기서 *container_name*은 컨테이너의 이름입니다.

    예를 들면 다음과 같습니다.

    ```
    bx ic exec -it iib /bin/bash
    ```
    {: screen}

    mqsiprofile의 소스를 지정하고 컨테이너 내부 쉘의 이 위치에서 명령을 실행할 수 있습니다.

3.  또는 **bx ic exec** 명령을 사용하여 IBM Integration Bus 명령을 실행할 수 있는 비대화식 Bash 세션을 실행하십시오.\*

    예를 들면 다음과 같습니다.

    ```
    bx ic exec iib <container_name> /bin/bash -c mqsilist
    ```
    {: screen}


## Docker CLI를 사용하여 컨테이너에서 관리 명령 실행
{: #admin_commands_docker_cli}

Docker CLI를 사용하여 컨테이너에서 직접 IBM Integration Bus 관리 명령을 실행하십시오.
{:shortdesc}

시작하기 전에 {{site.data.keyword.containershort_notm}} CLI를 구성하여 명령행에서 관리 명령을 실행하십시오. 자세한 정보는 [{{site.data.keyword.containershort_notm}} 플러그인 설정(`bx ic`)](/docs/containers/container_cli_cfic_install.html)을 참조하십시오.

  다음 단계를 완료하여 터미널을 설정하고 Docker 명령을 실행하여 IBM Integration Bus를 관리하십시오.

1.  터미널에서 {{site.data.keyword.Bluemix_notm}}에 로그인하십시오. 다음 명령을 실행하십시오.

    <ol>
     <li>
       <pre class="codeblock">bx login</pre>
     </li>
     <li>
       <pre class="codeblock">bx ic init</pre>

      <p>               환경 변수 &lt;DOCKER_HOST&gt;, &lt;DOCKER_CERT_PATH&gt; 및 &lt;DOCKER_TLS_VERIFY&gt;에 제공된 값을 복사하십시오.
            </p>
      </li>
      <li>  {{site.data.keyword.containershort_notm}}에 연결하기 위해 다음 변수를 설정하여 로컬 Docker 환경을 대체하십시오. <ul>
           <li><pre class="codeblock">export DOCKER_HOST=&lt;your_host_value&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;your_cert_path_value&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
        </ul>

          <p><b>참고</b>: 이 옵션에서는 일부 Docker 명령만 지원됩니다.
            </p>
     </li>
    </ol>

2.  Bash 세션을 컨테이너에 연결하여 대화식 세션을 설정하십시오. 다음 명령을 실행하십시오.

    ```
    docker exec -it <container_name> /bin/bash
    ```
    {: pre}

    여기서 *container_name*은 컨테이너의 이름입니다.

    예를 들면 다음과 같습니다.

    ```
    docker exec -it iib /bin/bash
    ```
    {: screen}

    mqsiprofile의 소스를 지정하고 컨테이너 내부 쉘의 이 위치에서 명령을 실행할 수 있습니다.

3.  mqsiprofile의 소스를 지정하고 컨테이너 내부 쉘의 이 위치에서 명령을 실행할 수 있습니다.

    또는 docker exec를 사용하여 IBM Integration Bus 명령을 실행할 수 있는 비대화식 Bash 세션을 실행하십시오. 
    
    예를 들면 다음과 같습니다.

    ```
    docker exec iib <container_name> /bin/bash -c mqsilist
    ```
    {: screen}


## 플로우 작성 및 배치
{: #createdeployflow}

IBM Integration Bus Knowledge Center의 지시사항에 따라 메시지 플로우를 작성하고 배치하십시오.
{:shortdesc}

메시지 플로우를 작성하는 방법에 대한 정보는 [메시지 플로우 작성 및 관리 방법 ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40150_.htm){: new_window}을 참조하십시오.

메시지 플로우를 배치하는 방법에 대한 정보는 [메시지 플로우 배치 및 구성 방법 ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40180_.htm){: new_window}을 참조하십시오.
