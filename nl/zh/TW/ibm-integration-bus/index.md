---

copyright:
  years: 2016, 2017
lastupdated: "2017-11-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# 開始使用 *ibm-integration-bus* 映像檔
{: #iib}

**ibm-integration-bus** 映像檔是提供給 {{site.data.keyword.containerlong}}。它包括 IBM Integration Bus for Developers 版本。
{:shortdesc}


## 如何運作
{: #how_it_works}

IBM Integration Bus for Developers 包含您所需的一切，讓您可以開始開發自己的整合解決方案。
{:shortdesc}

在您建立整合解決方案之後，可以使用 **ibm-integration-bus** 映像檔在 {{site.data.keyword.Bluemix_notm}} 中佈建單一容器。您可以接著使用 Web 使用者介面或從終端機中，將整合解決方案部署至此容器。

**附註**：您只能使用 **ibm-integration-bus** 映像檔進行開發及單元測試。您也可以使用映像檔來探索產品、透過指導教學學習，以及評估 IBM Integration Bus 對您組織的貢獻。


## 包含的內容
{: #whats_included}

此 IBM Integration Bus 映像檔包含 IBM Integration Bus for Developers 第 10 版（也稱為 Developer Edition）的套裝軟體，這個版本是可用於進行開發及單元測試的產品的完整功能版本。您可以免費下載此版本，並根據授權條款自由使用。
{:shortdesc}

IBM Integration Bus for Developers 10.0 版適用於 Windows™ 64 位元作業系統及 Linux™ on x86-64 作業系統。下載套件中包含所有產品必備項目。

如需 IBM Integration Bus for Developers 內含特性的相關資訊，請參閱 [IBM Knowledge Center 中的 IBM Integration Bus![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.msgbroker.helphome.doc/help_home_msgbroker.htm){: new_window}。

您可以從 [IBM Integration Bus for Developers 下載頁面 ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://www.ibm.com/marketing/iwm/iwm/web/pick.do?source=swg-wmbfd){: new_window} 存取 Developer Edition。


## 使用限制
{: #usage}

下表顯示適用於 {{site.data.keyword.Bluemix_notm}} 中免費使用 **ibm-integration-bus** 映像檔的限制。
{:shortdesc}

**附註**：**ibm-integration-bus** 映像檔的定價與 {{site.data.keyword.Bluemix_notm}} 中所使用容器的定價無關。

|環境|免費使用限制|
|-----------|-----------------------|
|開發|**無限制**免費使用 **ibm-integration-bus** 映像檔來建立進行開發及單元測試的單一容器。|
|正式作業|Developer Edition 限於每秒處理一個交易，且不支援用於正式作業環境。|
{: caption="表 1. 免費使用 ibm-integration-bus 映像檔的限制" caption-side="top"}


## 授權
{: #license}

授權的相關資訊。
{:shortdesc}

*   Dockerfiles 及關聯的 Script 是根據 [Apache License 2.0 ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](http://www.apache.org/licenses/LICENSE-2.0.html){: new_window} 進行授權。
*   [IBM Integration Bus for Developers 10.0 版的授權資訊 ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](http://www.ibm.com/software/sla/sladb.nsf/displaylis/1BD32B141C9FDAE38525802C0055A956?OpenDocument){: new_window}。

    **附註**：授權不容許進一步配送。映像檔中 IBM Integration Bus 的條款限制開發及單元測試的使用。


## 開始使用
{: #get_started}

使用 {{site.data.keyword.Bluemix_notm}} 型錄中的 **ibm-integration-bus** 映像檔，或選取您自己的正式作業授權映像檔來建立單一容器。
{:shortdesc}

**重要事項**：開始之前，請檢閱 **ibm-integration-bus** 映像檔的[使用限制](#usage)。

**附註**：您可以在所有標上星號 (\*) 的步驟中使用原生 Docker 指令。

   完成下列步驟，以在容器中執行 IBM Integration Bus：

1.  [從 {{site.data.keyword.Bluemix_notm}} 使用者介面](#provision)佈建以 ibm-integration-bus 映像檔為基礎的容器
2.  [驗證容器及 IBM Integration Bus 安裝](#validate)
3.  [建立及部署流程](#createdeployflow)
4.  [監視容器內的 IBM Integration Bus 日誌](#monitor)
5.  [管理在容器中執行的 IBM Integration Bus 資源](#manage)


## 佈建以 **ibm-integration-bus** 映像檔為基礎的容器
{: #provision}

在 {{site.data.keyword.Bluemix_notm}} 中佈建 Docker 容器，該 Docker 容器是以 {{site.data.keyword.IBM_notm}} 所提供的 **ibm-integration-bus** 映像檔為基礎。
{:shortdesc}

  請完成下列步驟：

1.  登入 {{site.data.keyword.Bluemix_notm}}。
2.  從型錄中，選取**容器**，然後選擇用來建置容器的 **ibm-integration-bus** 映像檔。
3.  選取**單一**，以建立可用於進行開發及測試的單一實例容器。
4.  輸入容器的名稱；例如，`iib`。
5.  輸入容器的大小。
6.  在**公用 IP 位址**欄位中，選取*連結公用 IP*。
7.  在**公用埠**欄位中，指定 `4414, 7800`。
8.  展開**進階**選項，然後按一下**新增環境變數**。
9.  新增下列環境變數：

    <ul>
      <li>值為 <i>accept</i> 的 LICENSE
      <li>值為 &lt;MYNODE&gt; 的 NODENAME（其中 &lt;MYNODE&gt; 是您選擇的節點名稱）</li>
      <li>值為 <i>/var/log/syslog</i> 的 LOG_LOCATIONS
    </ul>

    **附註**：如果您未指定 LICENSE 及 NODENAME 內容的值，則容器會建立，但不會啟動。

    LOG_LOCATIONS 內容指定容器內您要透過 {{site.data.keyword.Bluemix_notm}} 使用者介面公開的日誌。
10. 按一下**建立**，然後等待容器開始執行。


## 驗證容器及 IBM Integration Bus 安裝
{: #validate}

在執行 IBM Integration Bus 的容器部署至 {{site.data.keyword.Bluemix_notm}} 之後，請檢查容器狀態，並驗證 IBM Integration Bus 安裝。
{:shortdesc}

  完成下列步驟，以驗證容器中 IBM Integration Bus 的設定及配置：
  
1.  設定 {{site.data.keyword.containershort_notm}} CLI（如[設定 {{site.data.keyword.containershort_notm}} 外掛程式 (`bx ic`) 來使用原生 Docker CLI 所述](/docs/containers/container_cli_cfic_install.html)）。

2.  從終端機中，登入 {{site.data.keyword.Bluemix_notm}}。執行下列指令：

    <ol>
       <li>
          <pre class="codeblock">bx login</pre>
       </li>
       <li>
          <pre class="codeblock">bx ic init</pre>
       </li>
    </ol>

3.  檢查容器的狀態。

    ```
        bx ic ps
    ```
    {: pre}

    **附註**：在[登入 {{site.data.keyword.containershort_notm}}](/docs/containers/container_cli_cfic_install.html#container_cli_login)，並將環境變數設為使用原生 Docker 指令時，您可以將此指令中的 **bx ic** 取代為 **docker**。您可以在所有標上星號 (\*) 的步驟中使用原生 Docker 指令。

    ```
        $ bx ic ps
    CONTAINER ID        IMAGE                                                       COMMAND             CREATED             STATUS                 PORTS                                                        NAMES
    46d9ec1e-952        registry.ng.bluemix.net/ibm-integration-bus:latest   ""                  2 minutes ago       Running a minute ago   xxx.xxx.xxx.xxx:4414->4414/tcp, xxx.xxx.xxx.xxx:7800->7800/tcp   iib
    ```
    {: screen}

4.  將 Bash 階段作業附加至容器。\*

    ```
        bx ic exec -it <container_name> /bin/bash
    ```
    {: pre}

    其中 *container_name* 是容器的名稱。

    例如：

    ```
        $ bx ic exec -it iib /bin/bash
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

5.  藉由執行 **mqsiprofile** 指令來起始設定環境（如[指令環境：Linux 及 UNIX™ 系統 ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](http://www.ibm.com/support/knowledgecenter/SSMKHH_9.0.0/com.ibm.etools.mft.doc/an26230_.htm){: new_window} 所述）。

    用來起始設定環境的指令位於下列目錄中：`/opt/ibm/iib-10.0.0.6/server/bin/`

    例如：

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

6.  執行 **mqsilist** 指令以顯示應該列為 `running` 的節點狀態。例如：

    ```
        (IIB_10:)iibuser@instance-0098858e:/$ mqsilist
    BIP1325I: Integration node 'MYNODE' with administration URI 'http://instance-0098858e:4414' is running.
    BIP8071I: Successful command completion.
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

您的容器現在正在執行中，而且您可以使用任何支援的方法，以在其中部署整合解決方案。


## 監視容器內的 IBM Integration Bus 日誌
{: #monitor}

透過 {{site.data.keyword.Bluemix_notm}} 使用者介面或從指令行中監視日誌。
{:shortdesc}

開始之前，從指令行中配置 {{site.data.keyword.containershort_notm}} CLI 來監視日誌。如需相關資訊，請參閱[設定 {{site.data.keyword.containershort_notm}} 外掛程式 (`bx ic`)](/docs/containers/container_cli_cfic_install.html)。

**ibm-integration-bus** 映像檔已配置成將 IBM Integration Bus 訊息輸出至容器內的 `/var/log/syslog`。

請選擇下列其中一種方式來監視 IBM Integration Bus 日誌：

*  如果您已在佈建容器時配置環境變數 LOG_LOCATIONS，則可以從 {{site.data.keyword.Bluemix_notm}} 使用者介面監視日誌。

    請完成下列步驟，以存取日誌：

    <ol>
       <li>從容器儀表板中，選取您要監視的容器。</li>
       <li>選取<b>監視及記載</b>。</li>
       <li>選取<b>記載</b>。儀表板會列出日誌項目。</li>
       <li>若要自訂日誌視圖，請選取「記載」頁面中的<b>進階視圖</b>來開啟 Kibana。如需相關資訊，請參閱<a href="https://console.bluemix.net/docs/containers/container_planning.html#container_planning_monlog" target="_blank">監視及記載</a>以開始使用。
       </li>
    </ol>

*  從終端機中，使用 Docker 或 {{site.data.keyword.containerlong_notm}} CLI 來監視訊息。請完成下列步驟：

    <ol>
       <li>
       配置終端機。

        <ul>
        <li>若要在仍然直接使用 Docker CLI 管理本端 Docker 主機的同時，使用 {{site.data.keyword.containerlong_notm}} CLI 來管理 {{site.data.keyword.Bluemix_notm}} 中的容器，請完成下列步驟來設定環境：<ol>
             <li>
               <pre class="codeblock">bx login</pre>
             </li>
             <li>
               <pre class="codeblock">bx ic init</pre>
             </li>
           </ol>
        </li>
        <li>若要使用 Docker CLI 來管理 {{site.data.keyword.Bluemix_notm}} 中的容器，請完成下列步驟：<ol>
            <li>
              <pre class="codeblock">bx login</pre>
            </li>
            <li>
              <pre class="codeblock">bx ic init</pre>
               複製針對下列環境變數所提供的值：&lt;DOCKER_HOST&gt;、&lt;DOCKER_CERT_PATH&gt; 及 &lt;DOCKER_TLS_VERIFY&gt;。
            </li>
            <li>
            設定下列變數來連接至 {{site.data.keyword.containerlong_notm}}，以置換本端 Docker 環境：
               <ul>
                 <li><pre class="codeblock">export DOCKER_HOST=&lt;your_host_value&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;your_cert_path_value&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
               </ul>
                <b>附註</b>：此選項只支援部分 Docker 指令。
            </li>
          </ol>
        </li>
      </ul>
      </li>
      <li>
      存取日誌。請選擇下列其中一項：

      <ul>
         <li>如果您的終端機配置成執行 {{site.data.keyword.containerlong_notm}} CLI 指令，請執行下列指令：

            <pre class="codeblock">bx ic exec &lt;container_id&gt; tail -f /var/log/syslog</pre>

            其中 *container_id* 是容器的名稱。

            例如：

            <pre class="screen">bx ic exec iib tail -f /var/log/syslog</pre>

          </li>
          <li>
           如果您的終端機配置成執行 Docker CLI 指令，請執行下列指令：

            <pre class="codeblock">docker exec &lt;container_id&gt; tail -f /var/log/syslog</pre>

            其中 *container_id* 是容器的名稱。

            例如：

            <pre class="screen">docker exec iib tail -f /var/log/syslog</pre>

          </li>
        </ul>
       </li>
    </ol>


## 管理在容器中執行的 IBM Integration Bus 資源
{: #manage}

啟動 IBM Integration Bus Web 使用者介面，或從終端機中執行指令，以管理在容器中執行的 IBM Integration Bus 資源。
{:shortdesc}

  請選擇下列其中一個選項來管理容器中的 IBM Integration Bus 資源：

*  [啟動 IBM Integration Bus Web 使用者介面](#launchwebUI)，然後以圖形方式管理 IBM Integration Bus。
*  從終端機中，使用 Docker 或 {{site.data.keyword.containershort_notm}} CLI 來管理 IBM Integration Bus 資源：
    *   [使用 {{site.data.keyword.containerlong_notm}} CLI 以執行容器中的管理指令](#admin_commands_containers_cli)
    *   [使用 Docker CLI 以執行容器中的管理指令](#admin_commands_docker_cli)


## 啟動 IBM Integration Bus Web 使用者介面
{: #launchwebUI}

啟動 Web 使用者介面，以從瀏覽器管理在容器中執行的 IBM Integration Bus 資源。
{:shortdesc}

請遵循下列步驟，將瀏覽器連接至[開始使用 {{site.data.keyword.Bluemix_notm}} 的 ibm-integration-bus 映像檔](#get_started)中所公開的主機。即會顯示 IBM Integration Bus Web 使用者介面。

1.  從 {{site.data.keyword.Bluemix_notm}} 使用者介面中選取容器，並檢查容器狀態是否為 `Running`。
2.  檢查容器詳細資料，以尋找「公用 IP」。
3.  開啟具有下列 URL 的 Web 瀏覽器：

    ```
        http://<DockerContainerPublicIP>:4414
    ```
    {: pre}

    其中 *DockerContainerPublicIP* 是前一個步驟中所檢查之容器的公用 IP。

4.  瀏覽器會開啟 Web 使用者介面，而且您已經備妥可管理 IBM Integration Bus。


## 使用 {{site.data.keyword.containerlong_notm}} CLI 以執行容器中的管理指令
{: #admin_commands_containers_cli}

使用 {{site.data.keyword.containershort_notm}} CLI，直接在容器中執行 IBM Integration Bus 管理指令。
{:shortdesc}

開始之前，從指令行中配置 {{site.data.keyword.containershort_notm}} CLI 來執行管理指令。如需相關資訊，請參閱[設定 {{site.data.keyword.containershort_notm}} 外掛程式 (`bx ic`)](/docs/containers/container_cli_cfic_install.html)。

  完成下列步驟，以設定終端機執行 **bx ic** 指令來管理 IBM Integration Bus：

1.  從終端機中，登入 {{site.data.keyword.Bluemix_notm}}。執行下列指令：

      <ol>
        <li>
           <pre class="codeblock">bx login</pre>
        </li>
        <li>
           <pre class="codeblock">bx ic init</pre>
        </li>
       </ol>
           
2.  將 Bash 階段作業附加至容器，以設定互動式階段作業。

    ```
        bx ic exec -it <container_name> /bin/bash
    ```
    {: pre}

    其中 *container_name* 是容器的名稱。

    例如：

    ```
        bx ic exec -it iib /bin/bash
    ```
    {: screen}

    您可以將 mqsiprofile 設為來源，並從容器的 Shell 中的這個點執行指令。

3.  或者，您也可以使用 **bx ic exec** 來執行可執行任何 IBM Integration Bus 指令的非互動式 Bash 階段作業。

    例如：

    ```
        bx ic exec iib <container_name> /bin/bash -c mqsilist
    ```
    {: screen}


## 使用 Docker CLI 以執行容器中的管理指令
{: #admin_commands_docker_cli}

使用 Docker CLI，直接在容器中執行 IBM Integration Bus 管理指令。
{:shortdesc}

開始之前，從指令行中配置 {{site.data.keyword.containershort_notm}} CLI 來執行管理指令。如需相關資訊，請參閱[設定 {{site.data.keyword.containershort_notm}} 外掛程式 (`bx ic`)](/docs/containers/container_cli_cfic_install.html)。

  完成下列步驟，以設定終端機執行 Docker 指令來管理 IBM Integration Bus：

1.  從終端機中，登入 {{site.data.keyword.Bluemix_notm}}。執行下列指令：

    <ol>
     <li>
       <pre class="codeblock">bx login</pre>
     </li>
     <li>
       <pre class="codeblock">bx ic init</pre>

      <p>請複製針對下列環境變數所提供的值：&lt;DOCKER_HOST&gt;、&lt;DOCKER_CERT_PATH&gt; 及 &lt;DOCKER_TLS_VERIFY&gt;。</p>
      </li>
      <li>  設定下列變數來連接至 {{site.data.keyword.containershort_notm}}，以置換本端 Docker 環境：<ul>
           <li><pre class="codeblock">export DOCKER_HOST=&lt;your_host_value&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;your_cert_path_value&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
        </ul>

          <p><b>附註</b>：此選項只支援部分 Docker 指令。</p>
     </li>
    </ol>

2.  將 Bash 階段作業附加至容器，以設定互動式階段作業。請執行下列指令：

    ```
        docker exec -it <container_name> /bin/bash
    ```
    {: pre}

    其中 *container_name* 是容器的名稱。

    例如：

    ```
        docker exec -it iib /bin/bash
    ```
    {: screen}

    您可以將 mqsiprofile 設為來源，並從容器的 Shell 中的這個點執行指令。

3.  您可以將 mqsiprofile 設為來源，並從容器的 Shell 中的這個點執行指令。

    或者，您也可以使用 docker exec 來執行可執行任何 IBM Integration Bus 指令的非互動式 Bash 階段作業。 
    
    例如：

    ```
        docker exec iib <container_name> /bin/bash -c mqsilist
    ```
    {: screen}


## 建立及部署流程
{: #createdeployflow}

請遵循 IBM Integration Bus Knowledge Center 中的指示，以建立及部署訊息流程。
{:shortdesc}

如需如何建立訊息流程的相關資訊，請參閱[如何建立及管理訊息流程？![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40150_.htm){: new_window}。

如需如何部署訊息流程的相關資訊，請參閱[如何部署及配置訊息流程？![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40180_.htm){: new_window}。
