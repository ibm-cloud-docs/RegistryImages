---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-19"

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

# 開始使用 `ibm/liberty` 映像檔
{: #ibmliberty}

IBM® WebSphere® Application Server Liberty (`ibm/liberty`) 映像檔是提供給 {{site.data.keyword.containerlong_notm}}。
{:shortdesc}

您可以使用指令行存取 {{site.data.keyword.IBM_notm}} 所提供的映像檔，請參閱 [IBM 公用映像檔](/docs/services/Registry?topic=registry-public_images#public_images)。
{: tip}

## 如何運作
{: #ibmliberty_how_it_works}

您可以使用 `ibm/liberty` 映像檔作為母項，在 IBM WebSphere Application Server Liberty 容器中自行建立映像檔，並部署自己的 Java 型 WAR、EAR 或 OSGi 應用程式。
{:shortdesc}

## 包含的內容
{: #ibmliberty_whats_included}

每個 Liberty 映像檔都會提供下列套裝軟體。
{:shortdesc}

- IBM WebSphere Application Server for Developers Liberty
- IBM Java Runtime Environment 8.0

映像檔中安裝的特定 Liberty 特性取決於您選取的標籤。下表顯示每一個 `ibm/liberty` 映像檔中包含哪些特性。如需每一個特性的相關資訊，請參閱 [IBM Knowledge Center 中的 Liberty 特性概觀 ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/SSAW57_liberty/com.ibm.websphere.wlp.nd.multiplatform.doc/ae/rwlp_feat.html)。

|標籤|說明|
|---|-----------|
|所有 `ibm/liberty` 映像檔|所有 `ibm/liberty` 映像檔包含下列特性。<ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|`ibm/liberty:latest`|此映像檔指向 `ibm/liberty:javaee8` 映像檔。|
|`ibm/liberty:microProfile1`|此映像檔包含提供 [MicroProfile 1.x ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://microprofile.io/) 指定功能的特性。|
|`ibm/liberty:microProfile2`|此映像檔包含提供 [MicroProfile 2.x ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://microprofile.io/) 指定功能的特性。|
|`ibm/liberty:springBoot1`|此映像檔包含提供 [Spring Boot 1.x ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://spring.io/projects/spring-boot) 指定功能的特性。|
|`ibm/liberty:springBoot2`|此映像檔包含提供 [Spring Boot 2.x ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://spring.io/projects/spring-boot) 指定功能的特性。|
|`ibm/liberty:webProfile7`|此映像檔包含「Java EE 7 Web 設定檔」相符性所需的一切特性。|
|`ibm/liberty:webProfile8`|此映像檔包含「Java EE 8 Web 設定檔」相符性所需的一切特性。|
|`ibm/liberty:javaee7`|此映像檔包含來自 `ibm/liberty:webProfile7` 映像檔的所有特性，加上「Java EE 7 完整平台」相符性所需的特性。|
|`ibm/liberty:javaee8`|此映像檔包含來自 `ibm/liberty:webProfile8` 映像檔的所有特性，加上「Java EE 8 完整平台」相符性所需的特性。|
{: caption="表 1. 每一個 <code>ibm/liberty</code> 映像檔中包含的特性" caption-side="top"}

## 使用限制
{: #ibmliberty_usage}

下表顯示適用於 {{site.data.keyword.cloud_notm}} 中免費使用 `ibm/liberty` 映像檔的限制。
{:shortdesc}

`ibm/liberty` 映像檔的定價與 {{site.data.keyword.cloud_notm}} 中所使用容器的定價無關。
{:tip}

|環境|免費使用限制|
|-----------|-----------------------|
|開發|**無限制**免費使用 `ibm/liberty` 映像檔。|
|正式作業|`ibm/liberty` 映像檔的免費使用，侷限於所有執行該映像檔的容器實例中，**最多 2 GB Java 資料堆空間**。例如，您可以免費有 2 個 1 GB 或 4 個 512 MB 資料堆的 Liberty 實例。|
{: caption="表 2. 定價" caption-side="top"}

若要監視容器實例的 Java 資料堆用量，請參閱[使用 CLI 監視容器的 Java 資料堆空間用量](#ibmliberty_monitor_heap)。

請在 Docker Hub 上 [websphere-liberty 映像檔 ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://hub.docker.com/_/websphere-liberty/) 的 License 一節中，檢閱 IBM 認證映像檔的使用條款。

## 開始使用
{: #ibmliberty_get_started}

使用 {{site.data.keyword.cloud_notm}} 型錄中的其中一個免費 `ibm/liberty` 映像檔，或選取您自己的正式作業授權映像檔來建立單一容器或容器群組。
{:shortdesc}

開始之前，請檢閱 `ibm/liberty` 映像檔的[使用限制](#ibmliberty_usage)。
{: important}

1. 從型錄選取側邊畫面上的**容器** > **IBM Cloud Container Registry** > **IBM 公用儲存庫**。搜尋 `ibm/liberty` 映像檔，以用來建置容器。如果您已自行建立正式作業授權映像檔，並將它部署至 {{site.data.keyword.cloud_notm}}，請從型錄中選取此映像檔。即會開啟建立容器頁面。
2. 從`標籤/版本`清單，選取您要使用的 **ibm/liberty** 映像檔的版本。
3. 如需從映像檔建置容器、設定叢集以及在叢集裡部署應用程式的相關資訊，請使用下列鏈結：

    - [從映像檔建置容器](/docs/containers?topic=containers-images#images)
    - [開始使用 IBM Cloud Kubernetes Service](/docs/containers?topic=containers-getting-started#getting-started)
    - [在叢集中部署應用程式](/docs/containers?topic=containers-app#app)

    `ibm/liberty` 映像檔要求埠 9080 得是公用埠。從「{{site.data.keyword.cloud_notm}} 儀表板」中建立容器時，預設會在**公用埠**欄位中新增埠。如果從 CLI 建立容器，請執行 `kubectl run` 指令並搭配 `--port=9080` 選項，使埠成為公開。
    {:tip}

## 使用 CLI 監視容器的 Java 資料堆空間用量
{: #ibmliberty_monitor_heap}

從 `ibm/liberty` 映像檔中建立容器之後，即可檢視特定 Pod 及其容器的度量值，以及檢閱 Java 資料堆用量。Java 資料堆空間是在執行時可供 Java 應用程式使用的記憶體。
{:shortdesc}

1. 取得您要檢視其度量值的 Pod 名稱。
  
   ```
    kubectl get pods
    ```
   {: pre}

2. 查看特定 Pod 及其容器的度量值。

   ```
    kubectl top pod POD_NAME --containers
    ```
   {: pre}

3. 若要檢閱 Java 資料堆使用情形，您必須存取 **RSS** 記憶體統計資料，請參閱 [Get a Shell to a Running Container ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/)。Java 資料堆用量會以 KB 為單位來顯示。如果所有實例的資料堆用量低於 2097152 KB (2 GB)，則不需要購買 WebSphere Application Server 授權。

4. 調整 WebSphere Application Server 實例的資料堆用量上限。如需相關資訊，請參閱 [Setting generic JVM arguments in the WebSphere Application Server V8.5 Liberty profile ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://www-01.ibm.com/support/docview.wss?uid=swg21596474)。

## 取得 WebSphere Application Server 授權
{: #ibm/liberty_license}

WebSphere Application Server 授權是根據所需的「處理器價值單位 (PVU)」數目。PVU 是 IBM Middleware 軟體授權的度量單位。PVU 數目指出軟體可使用的處理器（核心）數目。
{:shortdesc}

{{site.data.keyword.cloud_notm}} 中的每個容器大小都需要在 WebSphere Application Server 授權中必須具有特定數目的 PVU 授權。因此，您必須先計劃 `ibm/liberty` 容器，再購買授權。

若要購買 WebSphere Application Server 授權，請與 [IBM 服務中心 ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://www.ibm.com/us-en/marketplace/java-ee-runtime/purchase) 聯絡。如果您已有 WebSphere Application Server 8.5 版或更新版本的授權，則可以使用現有授權中的任何未用 PVU 來部署容器。

如果您在購買授權之後發現需要更多的 PVU，則可以與 [IBM 服務中心 ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://cloud.ibm.com/catalog/services/websphere-application-server) 聯絡來增加數量。

## 建立要與 {{site.data.keyword.containershort_notm}} 搭配使用的正式作業授權 `ibm/liberty` 映像檔
{: #ibmliberty_prod_image}

使用 WebSphere Application Server 授權，來建立可與 {{site.data.keyword.containershort_notm}} 搭配使用的正式作業授權 `ibm/liberty` 映像檔。請選擇下列其中一個作業。
{:shortdesc}

- [將映像檔從 Docker Hub 升級至正式作業映像檔 ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade)。
- [自行建置正式作業授權映像檔 ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://github.com/WASdev/ci.docker/tree/master/ga)。

在建立正式作業授權映像檔之後，請[將映像檔推送至專用登錄](/docs/services/Registry?topic=registry-getting-started#getting-started)，以與 {{site.data.keyword.containershort_notm}} 搭配使用。

## 從提供的映像檔建立映像檔
{: #ibmliberty_creating_image}

您可以使用其中一個 `ibm/liberty` 映像檔作為母項，以建立包含您自己的應用程式碼的子映像檔。在您的電腦上自訂範例 Dockerfile 並建置您的映像檔。然後，您可以將映像檔新增至組織的專用映像檔登錄，並利用它來建立容器。
{:shortdesc}

開始之前，請考量下列步驟。

- 將應用程式碼建置成 WAR、EAR 或 OSGi 檔案。
- 將檔案複製到您想要從中建置映像檔的目錄。

若要從 `ibm/liberty` 映像檔利用您的應用程式碼建立映像檔，請完成下列步驟：

1. 使用文字編輯器，建立名為 `Dockerfile` 的檔案，並將下列資訊複製到其中：

   ```
   FROM icr.io/ibm/liberty:<tag>
   COPY <app_name>.<file_extension> /config/dropins/
   ```
   {: pre}

    目錄 `/config` 是 `/opt/ibm/wlp/usr/servers/defaultServer` 的捷徑。
    {: note}

2. 將 `<tag>` 取代為包括應用程式所需之特性的 `ibm/liberty` 映像檔版本。

3. 將 `<app_name>` 取代為應用程式檔案的名稱。

4. 將 `<file_extension>` 取代為 `.war`、`.ear` 或 `.eba`。

5. 將應用程式的任何其他相依關係新增至 Dockerfile。

6. 建置映像檔並將它推送至專用映像檔登錄。如需相關資訊，請參閱[開始使用 {{site.data.keyword.registrylong_notm}}](/docs/services/Registry?topic=registry-getting-started#getting-started)。

所有 `ibm/liberty` 映像檔都會配置為將 Liberty 日誌檔寫入至容器內的目錄 `/logs`。所有其他由 Liberty 伺服器寫入的檔案都建立在目錄 `/opt/ibm/wlp/output/defaultServer` 中。您可以使用捷徑 `/output` 來存取這些檔案。
{:tip}

## `ibm/liberty` Dockerfile 參考資料
{: #ibmliberty_reference_dockerfile}

`ibm/liberty` 映像檔 Dockerfile 位於 [WASdev/ci.docker GitHub 儲存庫 ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://github.com/WASdev/ci.docker/tree/master/ga)，可供參考。
{:shortdesc}
