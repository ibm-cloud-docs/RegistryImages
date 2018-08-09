---

copyright:
  years: 2018
lastupdated: "2018-07-25"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# 開始使用 ibmcloud-secure-perimeter-health 映像檔
{: #ibmcloud-secure-perimeter-health}

**ibmcloud-secure-perimeter-health** 映像檔包含一個工具，可用來在 IBM Cloud 中掃描 Secure Perimeter 中的漏洞。
{:shortdesc}

## 如何運作
{: #how-it-works}

為了確保您的 Secure Perimeter 正常運作，**ibmcloud-secure-perimeter-health** 可掃描 IBM Cloud 基礎架構帳戶中的公用或專用網路，並報告漏洞。您可以利用兩種方式來使用 **ibmcloud-secure-perimeter-health** 映像檔：

-   使用 **ibmcloud-secure-perimeter-health** 作為 Secure Perimeter 內 Kubernetes 叢集上的 Pod，以掃描專用網路曝光。
-   使用 **ibmcloud-secure-perimeter-health** 作為您工作站上的獨立式 Docker 儲存器，以掃描公用網路曝光。

在下列部落格文章中可以找到 Secure Perimeter 的相關資訊：
  * [在 IBM Cloud 中設定 Secure Perimeter](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/)
  * [在 IBM Cloud 中設定自動化 Secure Perimeter](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/)。

掃描之後，**ibmcloud-secure-perimeter-health** 映像檔會產生一個報告，說明可以從 Secure Perimeter Segment 內聯繫到的網路。每個報告都會詳述網路閘道的名稱、VLAN、其子網路及任何不當的主機。掃描專用網路漏洞的使用者報告範例：
```
#-------- Running Secure Perimeter exposure scan 2018-05-24 12:00:00 --------#

RESULTS:

sp-gateway-af6053a9:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   FAIL
			host = 10.73.84.198, ports = [179, 22]
		10.73.63.8/29:     PASS
		10.73.72.128/26:   PASS
sp-gateway-8a9031ab:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   PASS
		10.73.63.8/29:     PASS
```
{: screen}

## 包含的內容
{: #whats_included}

**ibmcloud-secure-perimeter-health** 映像檔會提供下列套裝軟體。
{:shortdesc}

-   Alpine Linux
-   Python 運行環境
-   SoftLayer Python Client
-   Nmap 埠掃描器

## 開始使用
{: #how_to_get_started}

檢閱下列作業以學習如何使用 **ibmcloud-secure-perimeter-health**：

1.  [使用 {{site.data.keyword.containerlong}} 在 Secure Perimeter 內佈建 Kubernetes 叢集](#provision_cluster)
2.  [掃描 Secure Perimeter 內的專用網路](#private_networks)
3.  [掃描 Secure Perimeter 外的公用網路](#public_networks)
4.  [瞭解掃描結果](#scan_results)
5.  [容器引數參考資料](#reference_container_arg)
6.  [環境變數參考資料](#reference_env_var)


## 使用 {{site.data.keyword.containerlong_notm}} 在 Secure Perimeter 內佈建 Kubernetes 叢集
{: #provision_cluster}

1.  從 IBM Cloud 型錄中的**容器**區段中，佈建您的 Kubernetes 叢集。
2.  按一下「建立」。
3.  從 VLAN 下拉功能表中，選取 Secure Perimeter Segment 公用和專用 VLAN。
4.  填寫您認為適當的所有其他詳細資料。
5.  按一下「建立叢集」。

檢閱 [{{site.data.keyword.containerlong}}](../../../containers/container_index.html#container_index) 文件，瞭解如何在部署完叢集之後取得該叢集的存取權。

## 掃描 Secure Perimeter 內的專用網路
{: #private_networks}

從 **ibmcloud-secure-perimeter-health** 映像檔中建立容器 Pod，並設定日常掃描。

開始之前：

-   安裝必要的 [CLI](../../../containers/cs_cli_install.html#cs_cli_install)。
-   [將 CLI 的目標設為](../../../containers/cs_cli_install.html#cs_cli_configure)您的叢集。

1. 建立名為 _health-pod.yaml_ 的配置檔。此檔案會建立可用性高的容器 Pod 部署。

    ```
apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: health-pod
      labels:
        app: health-pod
    spec:
      replicas: 1
      selector:
        app: health-pod
      template:
        spec:
          containers:
          - name: health-pod
            image: registry.bluemix.net/ibm/ibmcloud-secure-perimeter-health:1.0.0
            args:
            - /usr/local/bin/python
            - /run.py
            - --scan
            - private
            - --exclude-vlan-ids
            - <Private Secure Perimeter Segment VLAN ID>
            - --poll-interval
            - 1800
            env:
            - name: SL_USER
              value: <IBM Cloud infrastructure user name>
            - name: SL_APIKEY
              value: <IBM Cloud infrastructure api key>
    ```
    {: codeblock}

2. 建立部署。

    ```
kubectl apply -f health-pod.yaml
    ```
    {: pre}

3. 確認 Pod 在執行中。

    ```
    kubectl get pods
    ```
    {: pre}

    ```
NAME                                    READY     STATUS    RESTARTS   AGE
    health-pod-<random-id>                  1/1       Running   0          1hr
    ```
    {: screen}

## 掃描 Secure Perimeter 外的公用網路
{: #public_networks}

從 **ibmcloud-secure-perimeter-health** 映像檔建立一個 Docker 儲存器，並掃描公用網路。

開始之前：

-  安裝 Docker

1. 從您自己的工作站建立一個 Docker 儲存器，如下所示：

    ```
    docker run -it -e SL_USER='$SL_USER' -e SL_APIKEY='$SL_APIKEY' registry.bluemix.net/ibm/ibmcloud-secure-perimeter-health:1.0.0 /usr/local/bin/python run.py --scan public --allowed-public-ports 80 443 9000-9999
    ```
    {: pre}

2. 容器產生報告之後，請檢閱[分析掃描結果](#scan_results)區段，以瞭解結果。

## 分析掃描結果
{: #scan_results}

**ibmcloud-secure-perimeter-health** 會產生格式化的報告，說明 Secure Perimeter 的運作性能：
```
#-------- Running Secure Perimeter exposure scan 2018-05-24 12:00:00 --------#

RESULTS:

sp-gateway-af6053a9:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   FAIL
			host = 10.73.84.198, ports = [179, 22]
		10.73.63.8/29:     PASS
		10.73.72.128/26:   PASS
sp-gateway-8a9031ab:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   PASS
		10.73.63.8/29:     PASS
```
{: screen}

報告的格式如下所示：
```
<gateway name>:
  <vlan name>:
    <subnet>:    PASS
    <subnet>:    FAIL
      host = <ip address>, ports = [<exposed port>, <exposed port>, ...]
```
{: screen}

如果無法聯繫子網路中的任何主機，則 **ibmcloud-secure-perimeter-health** 會判斷子網路為 `PASS`，否則會傳回 `FAIL`，並列出可以聯繫的主機以及可存取的埠。

## 容器引數參考資料
{: #reference_container_arg}

|索引鍵|說明|預設值
|---|-------------|---|
|scan|曝光掃描的類型（「公用」或「專用」）|無（兩者都掃描）
|exclude-vlan-ids|要避免掃描且依據 ID 的 VLAN 清單|無
|poll-interval|設定下次掃描之前的秒數|0（執行一次）
|allowed-public-ports|要列入掃描白名單的埠清單|80、443、9000-9999
{: caption="表 1. 容器引數" caption-side="top"}

## 環境變數參考資料
{: #reference_env_var}

|索引鍵|說明|
|---|-------------|
|SL_USER|您的 IBM Cloud 基礎架構使用者名稱|
|SL_APIKEY|您的 IBM Cloud 基礎架構 API 金鑰|
{: caption="表 2. 環境變數" caption-side="top"}
