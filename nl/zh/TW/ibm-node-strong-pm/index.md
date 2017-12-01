---

copyright:
  years: 2015, 2017
lastupdated: "2017-10-30"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}

# 開始使用 ibm-node-strong-pm 映像檔
{: #getting_started_node_strong}

具有 StrongLoop 的 {{site.data.keyword.IBM}} Node (**ibm-node-strong-pm**) 映像檔是提供給 {{site.data.keyword.containerlong_notm}}。
{:shortdesc}

## 如何運作 
{: #how_it_works}

使用 **ibm-node-strong-pm** 映像檔，建立單一容器來部署 [StrongLoop Process Manager](https://www.strongloop.com) 的執行中實例。Strongloop Process Manager 可讓您部署、管理及監視 {{site.data.keyword.IBM_notm}} Cloud 中已在遠端機器上實作的 Node.js 應用程式。
{:shortdesc}

遠端機器必須已安裝 StrongLoop Arc 的實例。StrongLoop Arc 是 StrongLoop 平台的圖形使用者介面，且包含建置、側寫及監視 Node.js 應用程式的工具。建置含有 StrongLoop Arc 的應用程式之後，您會將它推送並部署至 {{site.data.keyword.Bluemix_notm}} 中執行 StrongLoop Process Manager 的容器。若要建立遠端機器與容器之間的連線，您必須將公用 IP 位址連結至該容器，並設定此 IP 位址作為 StrongLoop Arc 中的 StrongLoop Process Manager 伺服器。當您的應用程式透過 StrongLoop Process Manager 部署之後，您可以在 StrongLoop Process Manager 中監視您應用程式的度量值和效能。若要更新或擴充您的應用程式，您必須使用 StrongLoop Arc 進行變更，然後將變更推送至該容器。

**附註：**不支援 **ibm-node-strong-pm** 與 {{site.data.keyword.Bluemix_notm}} 中的容器群組搭配使用。

## 包含的內容
{: #whats_included}

-   包含在 **ibmnode:latest** 映像檔中的所有套裝軟體。請參閱「開始使用 {{site.data.keyword.Bluemix_notm}} 的 **ibmnode** 映像檔」主題中的[包含的內容](/docs/services/RegistryImages/ibmnode/index.html#getting_started_node)。
-   StrongLoop Process Manager

## 開始使用
{: #how_to_get_started}

您可以使用「{{site.data.keyword.Bluemix_notm}} 型錄」中的 **ibm-node-strong-pm** 映像檔，在 {{site.data.keyword.containershort_notm}} 中試用單一容器。此映像檔是以執行中的 StrongLoop Process Manager 實例開始，容器一旦可供使用，您就可以存取該映像檔。使用連接至該 Process Manager 的 StrongLoop Arc 實例，您可以推送 Node.js 應用程式並進行存取。
{:shortdesc}

**附註：**開始之前，請在 [websphere-liberty Docker 程式庫](https://github.com/docker-library/docs/tree/master/websphere-liberty)的 Usage 一節中檢閱 {{site.data.keyword.IBM_notm}} 認證映像檔的使用條款。

1.  從型錄中，選取**容器**，然後選擇用來建置容器的 **ibm-node-strong-pm** 映像檔。
2.  建立單一容器。
    -   在**大小**欄位中，選取至少 1 GB 記憶體的容器大小（**小**）。
    -   在**公用埠**欄位中，輸入埠 8701 及 3001。當您的容器已部署在 {{site.data.keyword.Bluemix_notm}} 中，而且具備將公用 IP 位址連結至容器的必備項目時，就會自動公開這些埠。埠 8701 是用來存取容器中的 StrongLoop Process Manager。埠 3001 是應用程式部署至 StrongLoop Process Manager 之後用來接聽的埠。

        **附註：**若要將其他應用程式部署至 StrongLoop Process Manager，請公開更多埠。埠必須是連續的，並起始於 3002。例如 3002、3003、3004 依此類推。

    -   如需進一步資訊，請參閱[使用 {{site.data.keyword.Bluemix_notm}} 儀表板建立單一容器](/docs/containers/container_single_ui.html#gui)。
3.  將公用 IP 位址連結至容器。如需相關資訊，請參閱[要求及連結 IP 位址](/docs/containers/container_single_ui.html#container_cli_ips)。

## 在遠端機器上設定 StrongLoop Arc
{: #UsingSLA}

從 {{site.data.keyword.Bluemix_notm}} 中的 **ibm-node-strong-pm** 映像檔中建立單一容器之後，請在遠端機器上設定 StrongLoop Arc。
{:shortdesc}

開始之前，請在遠端機器上安裝 Node.js 及 Node 套件管理程式 (npm)。

1.  輸入下列指令，以下載並安裝 StrongLoop。StrongLoop 包含執行 Arc 所需的所有套件。

    ```
    npm install -g strongloop
    ```
    {: pre}

2.  在安裝 StrongLoop 之後，請執行 StrongLoop Arc。

    ```
    slc arc
    ```
    {: pre}

3.  系統提示時，請登錄或登入 StrongLoop。
4.  將在容器中執行的 StrongLoop Process Manager 新增至 StrongLoop Arc 中的 Process Manager 清單。
    1.  從 Arc 模組選取器中，導覽至**程序管理程式**。
    2.  將連結至您容器的公用 IP 位址以及公用埠 8701 新增至 Arc Process Manager。
    3.  按一下**啟動**。

## 使用 StrongLoop Arc 將 Node.js 應用程式推送至您的容器
{: #pushing_app_to_container}

連接在您容器中執行的 StrongLoop Process Manager 與您遠端機器上的 StrongLoop Arc 用戶端之後，請建置 Node.js 應用程式，並將其推送至 {{site.data.keyword.Bluemix_notm}}。
{:shortdesc}

1.  從 Arc 模組選取器中，導覽至**建置並部署**。
2.  遵循 Arc 使用者介面指示來建置應用程式。

    **重要事項：**確保您使用 StrongLoop Arc 建立的 Node.js 應用程式正在接聽環境變數 PORT 以及您在建立該容器時所公開的埠。

    範例

    ```
    app.listen(process.env.PORT || 3001);
    ```
    {: pre}

3.  遵循 Arc 使用者介面指示來部署應用程式。選取您已配置的「程序管理程式」，將應用程式部署至 {{site.data.keyword.Bluemix_notm}}。
4.  等待部署程序完成。
5.  存取您的應用程式。
    1.  開啟 Web 瀏覽器。
    2.  以下列格式輸入容器的 IP 位址和埠：<var class="keyword varname">IP_ADDRESS:PORT</var>。

        範例

        ```
        192.0.2.26:3001
        ```
        {: pre}

    3.  針對部署至 {{site.data.keyword.Bluemix_notm}} 的每個應用程式，重複這些步驟。
6.  使用 Arc 的追蹤、度量及側寫程式，以取得應用程式在 {{site.data.keyword.Bluemix_notm}} 上執行時的效能資訊。如需相關資訊，請參閱 StrongLoop 文件中的 [Operating Node applications](https://docs.strongloop.com/display/SLC/Operating+Node+applications)。

## **ibm-node-strong-pm** Dockerfile 參考資料 
{: #reference_dockerfile}

此 Dockerfile 是用來在「{{site.data.keyword.Bluemix_notm}} 型錄」中建立 **ibm-node-strong-pm** 映像檔。本資訊僅供參考。不提供此映像檔的建置版本中包含的其他檔案。

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


