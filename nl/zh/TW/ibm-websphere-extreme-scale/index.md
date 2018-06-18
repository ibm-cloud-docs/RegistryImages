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


# 開始使用 **ibm-websphere-extreme-scale** 映像檔 
{: #ibm-websphere-extreme-scale}

**ibm-websphere-extreme-scale** 映像檔是提供給 {{site.data.keyword.containerlong}}。

## 如何運作 
{: #how_it_works}

IBM WebSphere&reg; eXtreme Scale 映像檔是一個在 Liberty 運行環境內執行的分散式記憶體內快取特性。它會根據最新業界標準及規格來提供 Liberty 快取伺服器、快取作業工具、管理主控台及立即可用的 REST 管理服務。此映像檔是以核心 IBM WebSphere eXtreme Scale 技術為建置基礎。此映像檔內所載入的快取技術完整支援 JCache (JSR107) 規格及相符性。

當您使用 `ibm-websphere-extreme-scale` 映像檔時，主要重點如下：

-   以 eXtreme Scale 技術為基礎的易於部署、易於配置的分散式快取解決方案
-   簡單且易於使用的管理主控台和 REST API
-   支援所有 eXtreme Scale 快取使用案例的完整自訂
-   可輕鬆在快取環境內快速整合的可配置映像檔
-   支援 JSR107 快取規格的 JCache 相符性


## 包含的內容 
{: #whats_included}

每個 **ibm-websphere-extreme-scale** 映像檔都會提供下列套裝軟體。

-   Ubuntu 16.04 基礎映像檔，64 位元
-   IBM WebSphere eXtreme Scale
-   IBM Java Runtime Environment 8.0

映像檔中安裝的特定特性取決於您選取的標籤。下表顯示每一個 **ibm-websphere-extreme-scale** 映像檔中包括的程式碼層次。如需這些特性的相關資訊，請參閱 [eXtreme Scale 文件](http://www.ibm.com/support/knowledgecenter/SSTVLU)。

|標籤|說明|
|---|-----------|
|**ibm-websphere-extreme-scale:latest**|使用此標籤來指定最新的 IBM WebSphere eXtreme Scale 映像檔。|
|**ibm-websphere-extreme-scale:8.6.1.x**|使用此標籤來指定 WebSphere eXtreme Scale 的特定版次層次。|
{: caption="表 1. IBM WebSphere eXtreme Scale 映像檔" caption-side="top"}

## 使用限制 
{: #usage}

下表顯示適用於 {{site.data.keyword.Bluemix_notm}} 中免費使用 **ibm-websphere-extreme-scale** 映像檔的限制。

|環境|使用限制|
|-----------|------------------|
|開發|`ibm-websphere-extreme-scale` 映像檔容許無限制免費使用進行開發。|
|正式作業|**ibm-websphere-extreme-scale** 映像檔的正式作業使用限於授權的 IBM WebSphere eXtreme Scale 產品使用者。如需正式作業使用的相關資訊，請參閱 [View Pricing and Buy](https://www-112.ibm.com/software/howtobuy/buyingtools/paexpress/Express?P0=E1&part_number=D56YTLL,D56YVLL,D572PLL,D572MLL,D0594LL,D059GLL&catalogLocale=en_US&Locale=en_US&country=USA&PT=jsp&CC=USA&VP=&TACTICS=&S_TACT=&S_CMP=&brand=SB05)。|
{: caption="表 2. {{site.data.keyword.Bluemix_notm}} 中 ibm-websphere-extreme-scale 映像檔的使用限制" caption-side="top"}

**附註：****ibm-websphere-extreme-scale** 映像檔的定價與 {{site.data.keyword.Bluemix_notm}} 中所使用容器的定價無關。

請在 [License information documents](http://www-03.ibm.com/software/sla/sladb.nsf/displaylis/A44EE6829150B2998525801700516718?OpenDocument) 的 License 一節中，檢閱 IBM 認證映像檔的使用條款。


## 開始使用 
{: #get_started}

您可以使用主控台或指令行來部署 **ibm-websphere-extreme-scale** 映像檔。

此程序包括下列使用主控台或指令行來部署映像檔的選項：

-   從 {{site.data.keyword.Bluemix_notm}} [主控台](#bmconsole)的**型錄**中，選取映像檔。
-   透過 CF [指令行](#bmcommand)，使用 cf ic 指令來啟動映像檔。

**重要事項：**開始之前，請檢閱前一節所述的 **ibm-websphere-extreme-scale** 映像檔的使用限制。


### 從 {{site.data.keyword.Bluemix_notm}} 主控台部署。
{: #bmconsole}

   1.  從型錄中，選取**容器**，然後選擇用來開始建置容器的 **ibm-websphere-extreme-scale** 映像檔。
   2.  從**標籤/版本**下拉功能表中，選取您要使用的 **ibm-websphere-extreme-scale** 映像檔的版本。
   3.  建立單一容器。

        **附註：**如果您要建立多個容器，請不要使用**可擴充**選項來建立兩個以上的 eXtreme Scale 快取成員群組。而是選取**單一**選項來建立多個容器，以及使用 eXtreme Scale 儀表板使用者介面來加入快取成員。

        如需相關資訊，請參閱[使用 {{site.data.keyword.Bluemix_notm}} 儀表板建立單一容器](/docs/containers/container_single_ui.html#gui)。

        1.  從「建立容器」畫面中，指定**容器名稱**；例如，`wxs1`。容器名稱中不容許空格或其他不可列印字元。
        2.  在**大小**中，指定用於啟動 ibm-eXtreme-scale 映像檔的容器的大小。
        3.  針對**公用 IP 位址**，指定「要求並連結公用 IP」。
        4.  開啟「進階選項」畫面。稍後安裝漸進式升級時，建立磁區以持續保存 eXtreme Scale 配置資料；例如：
            1. 按一下「進階選項」中的**建立磁區**，然後指定**磁區名稱**。例如，鍵入 vol-wxs1。
            2. 按一下**新增環境變數**。**ibm-websphere-extreme-scale** 映像檔需要儲存在容器內並可供授權使用者存取的環境變數。<table>
            <caption>表 3. ibm-websphere-extreme-scale 映像檔的環境變數</caption>
               <tr>
                 <th>變數名稱</th>
                 <th>要指定的值</th>
               </tr>
               <tr>
                 <td>SECRET_KEY</td>
                 <td>秘密金鑰的值；例如，<var class="keyword varname">s3cretKey!</var>。eXtreme Scale 伺服器使用這個秘密金鑰，在快取成員群組的多個成員之間傳送內部通訊。如需相關資訊，請參閱 [Cache member group section](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txscreategridtut1.html)。</td>
               </tr>
               <tr>
                 <td>XSADMIN_PWD</td>
                 <td>登入 eXtremeScale 儀表板使用者介面的 xsadmin 使用者的密碼；例如，<var class="keyword varname">xsadmin4Me!</var>。</td>
               </tr>
               </table>
  
              **附註：**您可以在啟動 eXtreme Scale 容器之後變更密碼及秘密金鑰。秘密金鑰及密碼都會具有下列密碼規則。請務必根據下列規則來輸入兩者：

              密碼及秘密金鑰必須至少包含一個數字 [`0-9`]、一個大寫字元 [`A-Z`]、一個特殊字元 [`~!@#$%^&*()_-+=:;'[]{}|<>,.?/`] 以及至少 10 個字元。

              如需如何變更密碼的相關資訊，請參閱 [Administering](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txsadministerxsld.html) 文件。

              預設會在**公用埠**欄位中新增埠。

   4.  按一下**建立**，以從「{{site.data.keyword.Bluemix_notm}} 儀表板」建立容器。
    
   5.  完全啟動容器及 eXtreme Scale 伺服器之後，請登入 eXtreme Scale 儀表板使用者介面，並驗證部署。從 {{site.data.keyword.Bluemix_notm}} 主控台，按一下容器來檢視「公用 IP」。您可以從下列 URL 存取 WebSphere eXtreme Scale 容器：

    ```
        https://<Public_IP>:9443
    ```
    {: pre}

   **附註：**可能需要 5 到 10 分鐘的時間才能完全啟動伺服器。


### 從指令行部署映像檔。
{: #bmcommand}
 1.  建立用來儲存 IBM WebSphere eXtreme Scale 持續資料的 Docker 磁區。例如，輸入下列指令：

  ```
  cf ic volume create <vol-wxs1>
  ```
  {: pre}

 2.  輸入下列指令，以在 {{site.data.keyword.Bluemix_notm}} 中啟動 IBM WebSphere eXtreme Scale 容器：

    **附註：**根據先前提及的密碼規則，將 yourSecretKey 及 yourXsadminPassword 取代為自己的密碼。

   ```
   cf ic run -itdP --volume <vol-wxs1:/vol> --name <wxs1> -m 1024 -e SECRET_KEY=<yourSecretKey> -e XSADMIN_PWD=<yourXsadminPassword> registry.ng.bluemix.net/ibm-websphere-extreme-scale:latest
   ```
   {: pre}
        
     **附註：**容器名稱只能包含英數字元，且開頭為字母。或者，如果需要容器名稱包含其他非英數字元，您可以設定 <code>-e XSLD_CONTAINER_ALIAS=<wxs1></code>。

 3.  要求將公用 IP 用於第一個 IBM WebSphere eXtreme Scale 容器。請輸入下列指令，以寫入 IP 位址：

   ```
   cf ic ip request
   ```
   {: pre}

 4.  輸入下列指令，以將 WebSphere eXtreme Scale 容器連結至您所要求的公用 IP 位址：

   ```
   cf ic ip bind <REQUESTED_IP> wxs1
   ```
   {: pre}

 5.  輸入下列指令，以驗證 `wxs1` 容器狀態為「執行中」：

   ```
   cf ic ps -a
   ```
   {: pre}

 6.  輸入下列指令，以連接至 `wxs1` 容器並監視 nanny 日誌：

   ```
   cf ic exec -it <wxs1> tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
   ```
   {: pre}

    **附註：**可能需要 5 到 10 分鐘的時間才能啟動伺服器。

 7.  完全啟動容器及 eXtreme Scale 伺服器之後，請登入 eXtreme Scale 儀表板使用者介面，並驗證部署：

   ```
   https://<REQUESTED_IP>:9443
   ```
   {: pre}


## 新增快取容量 
{: #caching}

1.  遵循與之前相同的步驟，從主控台或指令行部署另一個映像檔。
2.  完全啟動容器及 eXtreme Scale 伺服器之後，請登入此成員的 eXtreme Scale 儀表板使用者介面：

    ```
        https://<REQUESTED_IP>:9443
    ```
    {: pre}

3.  開啟「Data Cache 成員」頁面，然後在此成員的**動作**直欄下選取**加入**。
4.  輸入第一個成員的所要求公用 IP、第一個成員的秘密金鑰，以及此成員在快取群組中的唯一名稱。
5.  按一下**加入**，等待作業完成。作業完成時，所有成員都會顯示在「Data Cache 成員」頁面上。

## 執行漸進式升級 
{: #rolling_upgrade}

需要有下列步驟，才能對 {{site.data.keyword.containershort_notm}} 中執行 `ibm-websphere-extreme-scale` 的容器執行漸進式升級。

**附註：**如果您的 eXtreme Scale 快取成員群組只有一位成員，請移除此 {{site.data.keyword.IBM_notm}} 容器，然後從頭開始予以重建。不過，如果您的 eXtreme Scale 快取成員群組已部署多位成員及多部型錄伺服器，並且要將快取資料保存在 {{site.data.keyword.containershort_notm}} 中，請完成下列漸進式升級步驟，以避免失去任何快取的資料。請先升級只有容器伺服器的快取成員，然後再升級同時有型錄伺服器的快取成員。

1.  啟動稱為 wxsmigrate 的新容器，並將它用來升級所有現有 eXtreme Scale 容器。如需相關資訊，請參閱此頁面上的[開始使用](#get_started)小節。最新的 eXtreme Scale 更新 .zip 檔案將會部署在新 wxsmigrate 容器的 /tmp 資料夾下。
2.  將更新過的 eXtreme Scale .zip 檔案從 wxsmigrate 容器複製到本端系統，您可以在其中執行 {{site.data.keyword.containershort_notm}} CLI 外掛程式：

    ```
        cf ic cp wxsmigrate:/tmp/xsld-bin-update.zip
    ```
    {: pre}
    
    ```
        cf ic cp wxsmigrate:/tmp/xsld-derby-update.zip
    ```
    {: pre}

3.  現在，針對快取成員群組內的每一位快取成員，一次對一個容器完成下列步驟：

    1. 將更新過的 eXtreme Scale .zip 檔案從本端系統複製到您要升級的 eXtreme Scale 容器：
    
      ```
            cf ic cp  xsld-bin-update.zip  <container_name>:/tmp/xsld-bin-update.zip
      ```
      {: pre}
  
      ```
            cf ic cp  xsld-derby-update.zip  <container_name>:/tmp/xsld-derby-update.zip
      ```
      {: pre}
     
    2. 連接至您要升級的容器： 
    
      ```
              cf ic exec -it <container_name> bash
        ```
      {: pre}

    3. 停止已備妥可升級的容器上的 XSLD 伺服器。
      
      ```
              /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
        ```
      {: pre}
      
      等待伺服器停止。
      
    4. 解壓縮容器內的最新 eXtreme Scale 更新碼：
      
      ```
            unzip  -o  /tmp/xsld-bin-update.zip   -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
      
      ```
            unzip  -o  /tmp/xsld-derby-update.zip -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
    
    5. 重新啟動 XSLD 伺服器：
    
      ```
          /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
    ```
      {: pre}
      
    6. 監視 nanny 日誌，確保已啟動伺服器：
    
      ```
            tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
      ```
      {: pre}
      
4. 在所有 IBM 容器上完成升級之後，請移除 wxsmigrate 容器：

 ```
 cf ic rm -f <wxsmigrate>
 ```
 {: pre}

## 疑難排解 
{: #troubleshoot}

###遺失主機對映時的快取成員連線功能的疑難排解 
{: #troubleshoot_cache}

快取群組成員因遺失容器之間的主機對映而中斷連線時，請完成下列步驟。

1.  連接至每一個容器:

    ```
            cf ic exec -it <container_name> bash
        ```
    {: pre}

2.  收集每一位成員的專用 IP、主機名稱及主機別名。

    主機別名是 XSLD_CONTAINER_ALIAS 環境變數的值：

    ```
        echo ${<XSLD_CONTAINER_ALIAS>}
    ```
    {: pre}

    專用 IP 是在容器內執行 hostname -I 指令所傳回的 IP 位址：

    ```
        hostname -I
    ```
    {: pre}

    主機名稱是在容器內執行 hostname 指令所傳回的主機名稱。


3.  對每一位傳入所有其他成員別名、IP 及主機名稱的成員，執行 recoverXSLD.sh Script：

    ```
        /opt/ibm/WebSphere/eXtremeScale/wxs/recoverXSLD.sh <member2_alias> <member2_private_ip> <member2_hostname> ... <memberN_alias> <memberN_private_ip> <memberN_hostname>
    ```
    {: pre}

4.  在快取群組的每一個容器上執行 stopXSLD.sh，以停止執行中的伺服器。請執行 stopXSLD.sh：

    ```
            /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
        ```
    {: pre}

5.  如果 stopXSLD.sh 在嘗試停止型錄伺服器時未進行，請遵循下列步驟：
    1.  列出型錄伺服器處理程序：

        ```
                ps -ef | grep cs-default
        ```
        {: pre}

    2.  使用處理程序 ID 來結束型錄伺服器處理程序：

        ```
                kill -9 <catalog_server_process_id>
        ```
        {: pre}

    3.  請重新執行 stopXSLD.sh：

        ```
                /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
        ```
        {: pre}

6.  同時在所有容器上啟動 XSLD。請執行 startXSLD.sh：

    ```
        /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
    ```
    {: pre}


### 已停止的 Derby 抄寫作業的疑難排解 
{: #troubleshoot_derby}

如果 Derby 抄寫作業在十分鐘之後還未超過 45%，則可能是網路問題所造成。請完成下列步驟，以對此問題進行疑難排解：

1.  使用 REST POSTing 公用程式（例如 cURL），以取消 Derby 抄寫作業。

    ```
        curl -X PUT --header "Content-Type: application/json" --header "Accept: application/json" "https://<PUBLIC_IP>:9445/wxsadmin/v1/task/cancel/<DERBY_REPLICATION_TASK_ID>/force"
    ```
    {: pre}

    **附註：**<PUBLIC_IP> 用於啟動 Derby 抄寫作業的指令中，而 <DERBY_REPLICATION_TASK_ID> 是該指令的作業 ID 輸出。

2.  重新執行 Derby 抄寫作業。

## 存取 REST API 
{: #api}

在 eXtreme Scale 部署於 {{site.data.keyword.containershort_notm}} 中時，請完成下列步驟來存取 REST API：

1.  擷取您計劃用來連接至 REST API 的容器的主機名稱。
    1.  連接至容器：

        ```
                cf ic exec -it <container_name> bash
        ```
        {: pre}

    2.  列出主機名稱：

        ```
                hostname
        ```
        {: pre}

2.  擷取相同容器的公用 IP。

    如果容器沒有公用 IP，請遵循「開始使用」一節中的步驟，使用 {{site.data.keyword.Bluemix_notm}} [主控台](#bmconsole)或[指令行](#bmcommand)，將公用 IP 提供給容器。

3.  在本端用戶端電腦的 hosts 檔案中，建立將公用 IP 與容器主機名稱對映在一起的項目。項目看起來如下：

    ```
        169.44.8.87 instance-05106102
    ```
    {: pre}

    hosts 檔案的一般完整路徑名稱如下：

    -   若為 UNIX：/etc/hosts
    -   若為 Windows：C:\\Windows\\System32\\drivers\\etc\\hosts
4.  使用瀏覽器存取 Swagger REST API。
    -   若為 CRUD 作業：

        ```
                https://<host_name>:9444/ibm/api/explorer
        ```
        {: pre}

    -   若為管理作業：

        ```
                https://<host_name>:9445/ibm/api/explorer
        ```
        {: pre}


如需範例及最新更新，請造訪 [WebSphere eXtreme Scale in GitHub](https://github.com/ibmWebsphereExtremeScale)。如需部署 eXtreme Scale 的相關協助，也請參閱下列支援文件及視訊：

-   [IBM WebSphere eXtreme Scale 文件](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/cxsgetstartxsld.html)
-   [XS developerWorks 討論區](https://www.ibm.com/developerworks/community/forums/html/forum?id=11111111-0000-0000-0000-000000000778&ps=25)
-   [Getting started with WebSphere eXtreme Scale V8.6.x](https://www.youtube.com/watch?v=xw7Ck3c40rA)
-   [Adding cache with WebSphere eXtreme Scale - Liberty Deployment](https://www.youtube.com/watch?v=I1g88bnstn8)
-   [Setting up a WAS client to work with an WebSphere eXtreme Scale Liberty Deployment DynaCache grid ](https://www.youtube.com/watch?v=RvhcknNX-4Q)
-   [Setting up a WAS client to work with an WebSphere eXtreme Scale Liberty Deployment Session Cache grid](https://www.youtube.com/watch?v=GKTy9I66Klc)

