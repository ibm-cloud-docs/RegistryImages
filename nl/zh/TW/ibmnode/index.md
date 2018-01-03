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

# 開始使用 **ibmnode** 映像檔 {: #getting_started_node}

公用 {{site.data.keyword.IBM}} Node (**ibmnode**) 映像檔是提供給 {{site.data.keyword.containerlong_notm}}。
{:shortdesc}

**附註：**{{site.data.keyword.IBM_notm}} Node (**ibmnode**) 映像檔不包括範例 Node.js 應用程式。從此映像檔部署容器時，容器會在建置之後立即關閉，因為沒有長時間執行的處理程序。若要避免該問題，請使用 {{site.data.keyword.IBM_notm}} Node (**ibmnode**) 映像檔作為母項，並建立自己的映像檔，您可以在其中自行新增 Node.js 應用程式碼。如需相關資訊，請參閱[從這個提供的映像檔建立映像檔](#creating_image)。

# 如何運作 {: #how_it_works}

使用 {{site.data.keyword.IBM_notm}} Node (**ibmnode**) 映像檔作為母項，建置含有自己的 Node.js 應用程式碼的映像檔。
{:shortdesc}

**附註：**開始之前，請在 [websphere-liberty Docker 程式庫 ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://github.com/docker-library/docs/tree/master/websphere-liberty){: new_window} 的 "Usage" 一節中檢閱 IBM 認證映像檔的使用條款。

# 包含的內容 {: #included}

* Ubuntu 14.04
* Python、Git 及 build-essentials apt-get 套件
* {{site.data.keyword.IBM_notm}} Node SDK

    **附註：**ibmnode 映像檔有不同的標籤，因此，您可以執行不同版本的 IBM Node SDK。

    <table>
    <caption> 表 1. 不同 SDK 版本的標籤</caption>
      <tr>
        <th> 標籤</th>
        <th> 說明</th>
      </tr>
      <tr>
        <td> ibmnode:latest </td>
        <td> 最新版的 IBM Node，相當於使用 ibmnode:v4 </td>
      </tr>
      <tr>
        <td> ibmnode:v4 </td>
        <td> IBM Node 4.4.0 </td>
      </tr>
      <tr>
        <td> ibmnode:v1.2 </td>
        <td> IBM Node 1.2.0.8 (0.12.10) </td>
      </tr>
      <tr>
        <td> ibmnode:v1.1 </td>
        <td> IBM Node 1.1.0.18 (0.10.43) </td>
      </tr>
    </table>


# 從這個提供的映像檔建立映像檔 {: #creating_image}

您可以使用 **ibmnode** 映像檔作為母項，以建立包括您自己的應用程式碼的子映像檔。請使用 `Hello World` 應用程式容器範例來搭配 **ibmnode** 及 Express Node 模組，以在您的電腦上建置映像檔。將映像檔新增至組織的專用 {{site.data.keyword.registrylong_notm}}，並利用它來建立容器。然後，您可以使用 Hello World 範例中的 Dockerfile，利用您自己的應用程式建立另一個映像檔。
{:shortdesc}

開始之前，請考量下列步驟。

* 您必須在本端安裝下列工具：
  * [Cloud Foundry 及 Docker CLI 的 {{site.data.keyword.registrylong_notm}} 外掛程式](/docs/containers/container_cli_cfic_install.html)
  * [Node.js 原始碼 ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://nodejs.org/en/download/){: new_window}
  * [npm ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://github.com/npm/npm){: new_window}
* [您必須知道組織的專用 {{site.data.keyword.registryshort_notm}} 中的名稱空間。](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__namespace)

    範例

    registry.DomainName/<var class="keyword varname">namespace</var>

* [您必須具有一個 IP 位址。](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__ip_request)

建立以 **ibmnode** 映像檔為基礎，並且包括 Hello World 應用程式的映像檔。
1.  下載 [hellonode.zip ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](ftp://public.dhe.ibm.com/cloud/bluemix/containers/hellonode.zip){: new_window}，並將其解壓縮。
1.  使用文字編輯器開啟 Dockerfile，並在 FROM 指示中，為您要使用的地區更新登錄 URL。

    <ul>
    <li>美國南部

	```
	registry.ng.bluemix.net/ibmnode
	```
	{: codeblock}

    </li>
    <li>英國

	```
	registry.eu-gb.bluemix.net/ibmnode
	```
	{: codeblock}

      </li>
    </ul>

1.  從 CLI 中，導覽至 **app** 目錄。
1.  在 **app** 目錄中，執行下列指令。
    1.  建立 package.json 檔案。

        

        ```
        npm init
        ```
        {: pre}

        **提示：**按 Enter 鍵接受每一個提示的預設值。

    2.  安裝 Express（Node.js 的架構）。package.json 會以 dependencies 區段中的 Express 的版本資訊進行更新。

        ```
        npm install express -save
        ```
        {: pre}

1.  導覽回到 **hellonode** 目錄。
1.  建置映像檔，並將它推送至專用 {{site.data.keyword.registryshort_notm}}。

    ```
    bx ic build -t registry.<DomainName>/<namespace>/hellonode .
    ```
    {: pre}

    **提示：**執行 `bx ic namespace get`，以擷取名稱空間資訊。將 _registry.DomainName_ 設為您先前在 Dockerfile 中使用的同一個地區。

    **附註：**在[登入 {{site.data.keyword.containershort_notm}}](/docs/containers/container_cli_cfic_install.html#container_cli_login)，並將環境變數設為使用原生 Docker 指令後，您可以將此指令中的 `bx ic` 取代為 `docker`。您可以在本主題中所有標上星號 (*) 的步驟中使用原生 Docker 指令。

1.  從您的映像檔建立容器。

    ```
    bx ic run -p 3000 --name helloworld registry.<DomainName>/<namespace>/hellonode
    ```
    {: pre}

1.  將公用 IP 位址連結至容器。

    ```
    bx ic ip bind <IPaddress> helloworld
    ```
    {: pre}

    **提示：**您可以執行 `bx ic ip list` 來查看公用 IP 位址。若要要求新的 IP 位址，請執行 `bx ic ip request`。

1. 在瀏覽器中，開啟 **<var class="varname">IPaddress</var>:3000** 測試應用程式。即會顯示 Hello World! 訊息。

現在，您已建立 hellonode 映像檔，即可修改 Dockerfile，改為包括您自己的應用程式，並為該應用程式建立另一個映像檔。

1.  在 ADD 指示中，請新增您自己的應用程式碼。
1.  在 EXPOSE 指示中，請更新埠資訊。
1.  在 CMD 指示中，請建立指令來啟動您的應用程式。


# **ibmnode** Dockerfile 參考資料 {: #reference_dockerfile}

此 Dockerfile 是用來在 {{site.data.keyword.Bluemix_notm}} 型錄中建立 **ibmnode** 映像檔。本資訊僅供參考。不提供此映像檔的建置版本中包含的其他檔案。
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
