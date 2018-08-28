---

copyright:
  years: 2017, 2018
lastupdated: "2018-07-25"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# 開始使用 **ibmliberty** 映像檔
{: #ibmliberty}

IBM® WebSphere® Application Server Liberty (**ibmliberty**) 映像檔是提供給 {{site.data.keyword.containerlong_notm}}。
{:shortdesc}

## 如何運作 
{: #how_it_works}

您可以使用 **ibmliberty** 映像檔作為母項，在 IBM WebSphere Application Server Liberty 容器中自行建立映像檔，並部署自己的 Java 型 WAR、EAR 或 OSGi 應用程式。
{:shortdesc}

## 包含的內容 
{: #whats_included}

每個 Liberty 映像檔都會提供下列套裝軟體。
{:shortdesc}

-   IBM WebSphere Application Server for Developers Liberty
-   IBM Java Runtime Environment 8.0

映像檔中安裝的特定 Liberty 特性取決於您選取的標籤。下表顯示每一個 **ibmliberty** 映像檔中包含哪些特性。如需每一個特性的相關資訊，請參閱 [IBM Knowledge Center 中的 Liberty 特性概觀](http://www.ibm.com/support/knowledgecenter/SSAW57_8.5.5/com.ibm.websphere.wlp.nd.doc/ae/rwlp_feat.html)。

|標籤|說明|
|---|-----------|
|所有 **ibmliberty** 映像檔|所有 **ibmliberty** 映像檔包含下列特性。<ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|**ibmliberty:latest**|此映像檔指向 **ibmliberty:javaee7** 映像檔。|
|**ibmliberty:microProfile**|此映像檔包含提供 [MicroProfile](https://microprofile.io) 指定功能的特性。|
|**ibmliberty:webProfile6**|此映像檔包含「Java EE6 Web 設定檔」相符性所需的一切特性。它也會取回其他特性，使內容與可以使用運行環境 JAR 從 [http://wasdev.net/](http://wasdev.net/) 下載之特性保持一致，最值得注意的是 OSGi 應用程式所需的特性。|
|**ibmliberty:webProfile7**|此映像檔包含「Java EE7 Web 設定檔」相符性所需的一切特性。|
|**ibmliberty:javaee7**|此映像檔包含來自 **ibmliberty:webProfile7** 映像檔的所有特性，加上「Java EE7 完整平台」相符性所需的特性。|

## 使用限制 
{: #usage}

下表顯示適用於 {{site.data.keyword.Bluemix_notm}} 中免費使用 **ibmliberty** 映像檔的限制。
{:shortdesc}

**附註：****ibmliberty** 映像檔的定價與 {{site.data.keyword.Bluemix_notm}} 中所使用容器的定價無關。

|環境|免費使用限制|
|-----------|-----------------------|
|開發|**無限制**免費使用 **ibmliberty** 映像檔。|
|正式作業|**ibmliberty** 映像檔的免費使用，侷限於所有執行該映像檔的容器實例中，**最多 2 GB Java 資料堆空間**。例如，您可以免費有 2 個 1GB 或 4 個 512 MB 資料堆的 Liberty 實例。

若要監視容器實例的 Java 資料堆用量，請參閱[使用 CLI 監視容器的 Java 資料堆空間用量](#monitor_heap)。


請在 Docker Hub 上 [websphere-liberty 映像檔](https://hub.docker.com/_/websphere-liberty/)的 License 一節中，檢閱 IBM 認證映像檔的使用條款。

## 開始使用 
{: #get_started}

使用 {{site.data.keyword.Bluemix_notm}} 型錄中的其中一個免費 **ibmliberty** 映像檔，或選取您自己的正式作業授權映像檔來建立單一容器或容器群組。
{:shortdesc}

**重要事項：**開始之前，請檢閱 **ibmliberty** 映像檔的[使用限制](#usage)。

1.  從型錄選取側邊畫面上的**容器** > **IBM Cloud Container Registry** > **IBM 公用儲存庫**。搜尋 **ibmliberty** 映像檔，以用來建置容器。如果您已自行建立正式作業授權映像檔，並將它部署至 {{site.data.keyword.Bluemix_notm}}，請從型錄中選取此映像檔。即會開啟建立容器頁面。
2.  從**標籤/版本**下拉方框中，選取您要使用的 **ibmliberty** 映像檔的版本。
3.  如需從映像檔建置容器、設定叢集以及在叢集裡部署應用程式的相關資訊，請遵循底下的鏈結。

    -   [從映像檔建置容器](/docs/containers/cs_images.html#images)
    -   [開始使用 IBM Cloud Kubernetes Service](/docs/containers/container_index.html#container_index)
    -   [在叢集中部署應用程式](/docs/containers/cs_app.html#app)
    
    **附註：****ibmliberty** 映像檔需要公開埠 9080。從「{{site.data.keyword.Bluemix_notm}} 儀表板」中建立容器時，預設會在**公用埠**欄位中新增埠。如果從 CLI 建立容器，請在 `kubectl run` 指令中以 `--port=9080` 選項公開埠。


## 使用 CLI 監視容器的 Java 資料堆空間用量 
{: #monitor_heap}


從 **ibmliberty** 映像檔中建立容器之後，即可檢視特定 Pod 及其容器的度量值，以及檢閱 Java 資料堆用量。Java 資料堆空間是在執行時可供 Java 應用程式使用的記憶體。
{:shortdesc}

1.  取得您要檢視其度量值的 Pod 名稱。
  
    ```
    kubectl get pods
    ```

2.  查看特定 Pod 及其容器的度量值

    ```
    kubectl top pod POD_NAME --containers
    ```
    {: pre}

3.  若要檢閱 Java 資料堆使用情形，您需要存取 **RSS** 記憶體統計資料。請遵循[這裡](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/)的容器 Shell 存取準則，然後檢閱[運行環境度量值](/containers/runmetrics/#metrics-from-cgroups-memory-cpu-block-io)，以瞭解如何尋找和格式化容器的記憶體統計資訊。Java 資料堆用量會以 KB 為單位來顯示。如果所有實例的資料堆用量低於 2097152 KB (2GB)，則不需要購買 WebSphere Application Server 授權。

4.  調整 WebSphere Application Server 實例的資料堆用量上限。如需相關資訊，請參閱[在 WebSphere Application Server 8.5 版 Liberty 設定檔中設定通用 JVM 引數](http://www-01.ibm.com/support/docview.wss?uid=swg21596474)。

## 取得 WebSphere Application Server 授權 
{: #license}

WebSphere Application Server 授權是根據所需的「處理器價值單位 (PVU)」數目。PVU 是 IBM Middleware 軟體授權的度量單位。PVU 數目指出軟體可使用的處理器（核心）數目。
{:shortdesc}

{{site.data.keyword.Bluemix_notm}} 中的每個容器大小都需要在 WebSphere Application Server 授權中必須具有特定數目的 PVU 授權。因此，您必須先計劃 **ibmliberty** 容器，再購買授權。

若要購買「WebSphere Application Server 授權」，請與 [IBM 服務中心](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us)聯絡。如果您已有 WebSphere Application Server 8.5 版或更新版本的授權，則可以使用現有授權中的任何未用 PVU 來部署容器。

如果您在購買授權之後發現需要更多的 PVU，則可以與 [IBM 服務中心](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us)聯絡來增加數量。

## 建立要與 {{site.data.keyword.containershort_notm}} 搭配使用的正式作業授權 **ibmliberty** 映像檔 
{: #prod_image}

使用 WebSphere Application Server 授權，來建立可與 {{site.data.keyword.containershort_notm}} 搭配使用的正式作業授權 **ibmliberty** 映像檔。請選擇下列其中一個作業。
{:shortdesc}

-   [將映像檔從 Docker Hub 升級至正式作業映像檔](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade)。
-   [自行建置正式作業授權映像檔](https://github.com/WASdev/ci.docker/tree/master/ga/production-install)。

在建立正式作業授權映像檔之後，請[將映像檔推送至專用登錄](/docs/services/Registry/index.html)，以與 {{site.data.keyword.containershort_notm}} 搭配使用。

## 從提供的映像檔建立映像檔 
{: #creating_image}

您可以使用其中一個 **ibmliberty** 映像檔作為母項，以建立包含您自己的應用程式碼的子映像檔。在您的電腦上自訂範例 Dockerfile 並建置您的映像檔。然後，您可以將映像檔新增至組織的專用映像檔登錄，並利用它來建立容器。
{:shortdesc}

開始之前，請考量下列步驟。

-   將應用程式碼建置成 WAR、EAR 或 OSGi 檔案。
-   將檔案複製到您想要從中建置映像檔的目錄。


若要從 **ibmliberty** 映像檔利用您的應用程式碼建立映像檔，請執行下列動作：

1. 使用文字編輯器，建立名為 Dockerfile 的檔案，並將下列資訊複製到其中：

    ```
    FROM registry.bluemix.net/ibmliberty:<tag>
    COPY <app_name>.<file_extension> /config/dropins/
    
    ```
    {: screen}

    **附註：**目錄 /config 是 /opt/ibm/wlp/usr/servers/defaultServer 的捷徑。
    
2. 將 <tag\> 取代為包括應用程式所需之特性的 **ibmliberty** 映像檔版本。

3. 將 <app\_name\> 取代為應用程式檔案的名稱。

4. 將 <file\_extension\> 取代為 .war、.ear 或 .eba。

5. 將應用程式的任何其他相依關係新增至 Dockerfile。

6. 建置映像檔並將它推送至專用映像檔登錄。如需相關資訊，請參閱[開始使用 {{site.data.keyword.registrylong_notm}}](/docs/services/Registry/index.html)。

**附註：**所有 **ibmliberty** 映像檔都會配置為將 Liberty 日誌檔寫入至容器內的目錄 /logs。所有其他由 Liberty 伺服器寫入的檔案都建立在目錄 /opt/ibm/wlp/output/defaultServer 中。您可以使用捷徑 /output 來存取這些檔案。

## **ibmliberty** Dockerfile 參考資料 
{: #reference_dockerfile}

此 Dockerfile 說明如何從 Docker Hub 上的公用 websphere-liberty 映像檔建置 {{site.data.keyword.Bluemix_notm}} 中的 **ibmliberty:webProfile7** 映像檔。本資訊僅供參考。
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
