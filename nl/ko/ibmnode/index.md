---

copyright:
  years: 2017
lastupdated: "2017-11-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# **ibmnode** 이미지 시작하기 {: #getting_started_node}

공용 {{site.data.keyword.IBM}} Node(**ibmnode**) 이미지는 {{site.data.keyword.containerlong_notm}}용으로 지원됩니다.
{:shortdesc}

**참고:** {{site.data.keyword.IBM_notm}} Node(**ibmnode**) 이미지에는 샘플 Node.js 앱이 포함되지 않습니다. 이 이미지에서 컨테이너를 배치하면 더 이상 실행 중인 프로세스가 없으므로 빌드된 직후 컨테이너가 종료됩니다. 이 문제를 방지하려면 {{site.data.keyword.IBM_notm}} 노드(**ibmnode**) 이미지를 상위로 사용하여 고유 Node.js 앱 코드를 추가할 수 있는 고유 이미지를 작성하십시오. 자세한 정보는 [제공된 이 이미지에서 이미지 작성](#creating_image)을 참조하십시오.

# 작동 방식 {: #how_it_works}

{{site.data.keyword.IBM_notm}} Node(**ibmnode**) 이미지를 상위로 사용하여 고유 Node.js 앱 코드로 고유 이미지를 빌드하십시오.
{:shortdesc}

**참고:** 시작하기 전에 [websphere-liberty Docker 라이브러리 ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘:")](https://github.com/docker-library/docs/tree/master/websphere-liberty){: new_window}의 사용 섹션에서 IBM 인증 이미지의 이용 약관을 검토하십시오.

# 포함된 항목 {: #included}

* Ubuntu 14.04
* Python, Git 및 build-essentials apt-get 패키지
* {{site.data.keyword.IBM_notm}} Node SDK

    **참고:** IBM Node SDK의 여러 다른 버전을 실행할 수 있도록 ibmnode 이미지는 다른 태그와 함께 지원됩니다.

    <table>
    <caption> 표 1. 여러 다른 SDK 버전의 태그 </caption>
      <tr>
        <th> 태그 </th>
        <th> 설명 </th>
      </tr>
      <tr>
        <td> ibmnode:latest </td>
        <td> 최신 버전의 IBM Node, ibmnode:v4를 사용하는 것과 동일 </td>
      </tr>
      <tr>
        <td> ibmnode:v4 </td>
        <td> IBM Node 4.4.0 </td>
      </tr>
      <tr>
        <td> ibmnode:v1.2 </td>
        <td> IBM Node 1.2.0.8(0.12.10) </td>
      </tr>
      <tr>
        <td> ibmnode:v1.1 </td>
        <td> IBM Node 1.1.0.18(0.10.43) </td>
      </tr>
    </table>


# 제공된 이미지에서 이미지 작성 {: #creating_image}

자체 앱 코드를 포함하는 하위 이미지를 작성하기 위해 **ibmnode** 이미지를 상위로 사용할 수 있습니다. 샘플 `Hello World` 앱 컨테이너와 **ibmnode** 및 Express Node 모듈을 사용하여 컴퓨터에 이미지를 빌드하십시오. 조직의 사설 {{site.data.keyword.registrylong_notm}}에 이미지를 추가한 다음 이 이미지로 컨테이너를 작성하십시오. 그런 다음 Hello World 샘플의 Dockerfile을 사용하여 사용자 고유의 앱이 포함된 다른 이미지를 작성할 수 있습니다.
{:shortdesc}

시작하기 전에 다음과 같은 단계를 고려하십시오.

* 다음과 같은 도구를 로컬 환경에 설치해야 합니다.
  * [Cloud Foundry 및 Docker CLI용 {{site.data.keyword.registrylong_notm}} 플러그인](/docs/containers/container_cli_cfic_install.html)
  * [Node.js 소스 코드 ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://nodejs.org/en/download/){: new_window}
  * [npm ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://github.com/npm/npm){: new_window}
* [조직의 사설 {{site.data.keyword.registryshort_notm}}의 네임스페이스를 알아야 합니다.](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__namespace)

    예

    registry.DomainName/<var class="keyword varname">namespace</var>

* [IP 주소가 있어야 합니다.](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__ip_request)

**ibmnode** 이미지를 기반으로 하고 Hello World 앱을 포함하는 이미지를 작성하십시오.
1.  [hellonode.zip ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](ftp://public.dhe.ibm.com/cloud/bluemix/containers/hellonode.zip){: new_window}을 다운로드하여 압축을 푸십시오.
1.  문서 편집기로 Dockerfile 파일을 연 후, FROM 명령어에서, 사용하기 원하는 지역에 따라 레지스트리 URL을 업데이트하십시오.

    <ul>
    <li>US 남부

        ```
		registry.ng.bluemix.net/ibmnode
		```
		{: codeblock}

    </li>
    <li>영국

        ```
		registry.eu-gb.bluemix.net/ibmnode
		```
		{: codeblock}

      </li>
    </ul>

1.  CLI에서 **app** 디렉토리로 이동하십시오.
1.  **app** 디렉토리에서 다음 명령을 실행하십시오.
    1.  package.json 파일을 작성하십시오.

        ```
        npm init
        ```
        {: pre}

        **팁:** 리턴 키를 눌러 각 프롬프트의 기본값을 승인하십시오.

    2.  Node.js의 프레임워크인 Express를 설치하십시오. package.json이 종속 항목 섹션의 Express 버전 정보로 업데이트됩니다.

        ```
        npm install express -save
        ```
        {: pre}

1.  **hellonode** 디렉토리로 다시 이동하십시오.
1.  이미지를 빌드한 다음 사설 {{site.data.keyword.registryshort_notm}}에 푸시하십시오.*

    ```
    bx ic build -t registry.<DomainName>/<namespace>/hellonode .
    ```
    {: pre}

    **팁:** `bx ic namespace get`을 실행하여 네임스페이스 정보를 검색하십시오.  _registry.DomainName_을 Dockerfile에서 이전에 사용한 동일한 영역으로 설정하십시오.

    **참고:** \*이 명령에서 [{{site.data.keyword.containershort_notm}}에 로그인](/docs/containers/container_cli_cfic_install.html#container_cli_login)할 때 `bx ic`를 `docker`로 바꾸고 고유 Docker 명령을 사용하도록 환경 변수를 설정할 수 있습니다. 이 주제에서 별표(*)로 표시된 모든 단계에서 고유 Docker 명령을 사용할 수 있습니다.

1.  이미지에서 컨테이너를 작성하십시오.*

    ```
    bx ic run -p 3000 --name helloworld registry.<DomainName>/<namespace>/hellonode
    ```
    {: pre}

1.  공인 IP 주소를 컨테이너에 바인드하십시오.

    ```
    bx ic ip bind <IPaddress> helloworld
    ```
    {: pre}

    **팁:** `bx ic ip list`를 실행하여 공인 IP 주소를 볼 수 있습니다. 새 IP 주소를 요청하려면 `bx ic ip request`를 실행하십시오.

1. 브라우저에서 **<var class="varname">IPaddress</var>:3000**을 열어 앱을 테스트하십시오. Hello World! 메시지가 표시됩니다.

hellonode 이미지를 작성했으므로 사용자 고유의 앱이 대신 포함되도록 Dockerfile을 수정하고 이 앱에 대한 이미지를 추가 작성할 수 있습니다.

1.  ADD 명령어에 대해서는 사용자 고유의 애플리케이션 코드를 추가하십시오.
1.  EXPOSE 명령어에 대해서는 포트 정보를 업데이트하십시오.
1.  CMD 명령어에 대해서는 앱을 시작하기 위한 명령을 작성하십시오.


# **ibmnode** Dockerfile 참조 {: #reference_dockerfile}

이 Dockerfile을 사용하여 {{site.data.keyword.Bluemix_notm}} 카탈로그에 **ibmnode** 이미지를 작성합니다. 이 정보는 참조 전용입니다. 이 이미지의 빌드 버전에 포함된 기타 파일은 제공되지 않습니다.
{:shortdesc}

```
FROM ubuntu:14.04

# Update base OS
RUN apt-get update &&\
DEBIAN_FRONTEND=noninteractive apt-get -y install \
git \
build-essential \
python && \
DEBIAN_FRONTEND=noninteractive apt-get upgrade -y &&\
apt-get clean &&\
rm -Rf /var/cache/*

# Enhance default password rules
RUN sed -i 's/^PASS_MAX_DAYS.*/PASS_MAX_DAYS 90/' /etc/login.defs
RUN sed -i 's/sha512/sha512 minlen=8/' /etc/pam.d/common-password

# Install Node SDK
ONBUILD COPY ./prereq/node.tar.gz /tmp/prereq/
ONBUILD RUN cd /usr/local &&\
tar --strip-components 1 -xzf /tmp/prereq/node.tar.gz &&\
rm -rf /tmp/prereq

#Documentation path for URL
LABEL doc.url="/docs/services/RegistryImages/ibmnode/index.html"
```
{: screen}
