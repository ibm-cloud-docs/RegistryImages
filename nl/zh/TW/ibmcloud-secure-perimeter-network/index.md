---

copyright:
  years: 2018
lastupdated: "2018-05-25"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# 開始使用 ibmcloud-secure-perimeter-network 映像檔
{: #ibmcloud-secure-perimeter-network}

**ibmcloud-secure-perimeter-network** 映像檔包含一些工具，可用來在 Secure Perimeter 內自動化 Vyatta Virtual Router Appliance 的配置。
{:shortdesc}

## 如何運作
{: #how-it-works}

使用 **ibmcloud-secure-perimeter-network**，您可以自動化 Secure Perimeter Vyatta Virtual Router Appliance 的配置。

在下列部落格文章中可以找到 Secure Perimeter 的相關資訊：
  * [在 IBM Cloud 中設定 Secure Perimeter](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/)
  * [在 IBM Cloud 中設定自動化 Secure Perimeter](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/)。

您可以利用兩種方式來使用 **ibmcloud-secure-perimeter-network** 映像檔：
-  使用 **ibmcloud-secure-perimeter-network** 作為 Docker 儲存器，以起始設定 Secure Perimeter 防火牆規則配置。
-  使用 **ibmcloud-secure-perimeter-network** 作為 Kubernetes 叢集上的 Pod，以輪詢 IBM Cloud 基礎架構帳戶中建立在 Secure Perimeter Segment VLAN 上的新子網路，並將其新增至 Vyatta 防火牆配置。

## 包含的內容
{: #whats_included}

**ibmcloud-secure-perimeter-network** 映像檔會提供下列套裝軟體。
{:shortdesc}

-   Alpine Linux
-   Python 運行環境
-   SoftLayer Python Client
-   Ansible

## 開始使用
{: #how_to_get_started}

檢閱下列作業以學習如何使用 **ibmcloud-secure-perimeter-network**：

1.  [使用 {{site.data.keyword.containerlong}} 在 Secure Perimeter 內佈建 Kubernetes 叢集](#provision_cluster)
2.  [執行 Secure Perimeter Vyatta 的起始配置](#initial_setup)
3.  [設定為 Secure Perimeter 內的 Kubernetes Pod](#setup)
4.  [config.json 參考資料](#reference_config_json)
5.  [rules.conf 參考資料](#reference_rules_conf)

## 使用 {{site.data.keyword.containerlong}} 在 Secure Perimeter 內佈建 Kubernetes 叢集
{: #provision_cluster}

1.  從 IBM Cloud 型錄中的**容器**區段中，佈建您的 Kubernetes 叢集。
2.  按一下「建立」。
3.  從 VLAN 下拉功能表中，選取 Secure Perimeter Segment 公用和專用 VLAN。
4.  填寫您認為適當的所有其他詳細資料。
5.  按一下「建立叢集」。

檢閱 [{{site.data.keyword.containerlong}}](../../../containers/container_index.html#container_index) 文件，瞭解如何在部署完叢集之後取得該叢集的存取權。

## 執行 Secure Perimeter Vyatta 的起始配置
{: #initial_setup}

1. 建立名為 _config.json_ 的檔案。此檔案包含 **ibmcloud-secure-perimeter-network** 存取 Vyatta 時所需的基本參數。

  ```
  {
    "slid": "XXXX",
    "apikey": "XXXX",
    "region": "XXXX",
    "inf_name_private": "dp0bond0",
    "inf_name_public": "dp0bond1",
    "gatewayid": "XXXX",
    "vlans": [
      {
        "type": "XXXX",
        "vlan_num": XXXX,
        "vlanid": XXXX
      },
      ...
    ],
    "vyatta_gateway_vip": "X.X.X.X",
    "vyatta_primary": {
      "private_ip": "X.X.X.X",
      "public_ip": "X.X.X.X"
    },
    "vyatta_secondary": {
      "private_ip": "X.X.X.X",
      "public_ip": "X.X.X.X"
    }
  }
  ```
  {: codeblock}

  如需如何移入 _config.json_ 的詳細資料，請參閱 [config.json 參考資料表格](#reference_config_json)。此檔案也可以在[設定 **ibmcloud-secure-perimeter-network** 作為 Kubernetes Pod](#setup) 處理程序中使用。

2. 執行 **ibmcloud-secure-perimeter-network** 作為 Docker 儲存器，以開始起始設定。

  ```
  docker run registry.bluemix.net/ibm/ibmcloud-secure-perimeter-network:1.0.0 python config-secure-perimeter.py -v /path/to/current/dir:/opt/secure-perimeter
  ```
  {: pre}

  這會在您的工作目錄中建立 _state.json_ 檔案。此檔案用於[設定 **ibmcloud-secure-perimeter-network** 作為 Kubernetes Pod](#setup)。

## 設定為 Secure Perimeter 內的 Kubernetes Pod
{: #setup}

為了讓 **ibmcloud-secure-perimeter-network** 映像檔管理 Secure Perimeter 上的子網路，您可以使用 Kubernetes Pod，將其當作長期執行的處理程序來執行。**ibmcloud-secure-perimeter-network** 有數個配置檔及資料夾需要複製到 Pod 中，才能針對 Vyatta 進行配置：

1. 建立名為 _pvc.yaml_ 的檔案。這個配置檔會建立持續性磁區宣告 (pvc)，您可以將其裝載至您的 Pod 作為磁區。

  ```
  apiVersion: v1
  kind: PersistentVolumeClaim
  metadata:
    name: network-pvc
    annotations:
      volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
  spec:
    accessModes:
      - ReadWriteMany
    resources:
      requests:
        storage: 20Gi
  ```
  {: codeblock}

2. 建立 pvc。

    ```
    kubectl apply -f restore-pvc.yaml
    ```
    {: pre}

3. 建立名為 _network-pod.yaml_ 的檔案。此配置檔會將 **ibmcloud-secure-perimeter-network** 映像檔部署為您 Kubernetes 叢集中的 Pod，並將您的持續性磁區宣告裝載為磁區。

  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: network-pod
    labels:
      app: network-pod
  spec:
    template:
      spec:
        containers:
        - name: network-pod
          image: registry.<region>.bluemix.net/ibm/ibmcloud-secure-perimeter-network:1.0.0
          volumeMounts:
          - name: network-vol
            mountPath: /opt/secure-perimeter
        volumes:
        - name: network-vol
          PersistentVolumeClaim:
            claimName: network-pvc
  ```
  {: codeblock}

4. 建立名為 _rules.conf_ 的檔案。此配置檔會告知**ibmcloud-secure-perimeter-network**，來自公用網際網路的哪些外部子網路及埠，要列入 Secure Perimeter 的白名單。

  ```
  {
      "external_subnets": [
        "X.X.X.X/X",
        "X.X.X.X/X"
      ],
      "external_ports": [
        "XX",
        "XX"
      ],
      "userips": [
        "X.X.X.X",
        "X.X.X.X"
      ]
  }
  ```
  {: codeblock}

5. 將檔案複製到 **ibmcloud-secure-perimeter-network** Pod 中。

  ```
  kubectl cp keys network-pod:/opt/secure-perimeter/
  kubectl cp state.json network-pod:/opt/secure-perimeter/state.json
  kubectl cp config.json network-pod:/opt/secure-perimeter/config.json
  kubectl cp rules.conf network-pod:/opt/secure-perimeter/rules.conf
  ```
  {: pre}

  _keys_ 目錄包含 **ibmcloud-secure-perimeter-network** 存取 Vyatta 時所需的 SSH 金鑰。如需 SSH 金鑰的相關資訊，請參閱[必要條件小節](#prerequisites)。

## config.json 參考資料
{: #reference_config_json}

|索引鍵|說明
|---|-------------|---|
|slid|您的 IBM Cloud 基礎架構使用者名稱
|apikey|您的 IBM Cloud 基礎架構 API 金鑰
|region|在其中部署 Vyatta 的 IBM Cloud 地區
|inf_name_private|Vyatta 專用介面的名稱
|inf_name_public|Vyatta 公用介面的名稱
|gatewayid|Vyatta 閘道 ID
|vlans|Secure Perimeter Segment VLAN 的清單，包括類型、VLAN 號碼及 VLAN ID
|vyatta_gateway_vip|閘道的 VIP
|vyatta_primary|包含主要 Vyatta 成員之專用及公用 IP 的物件
|vyatta_secondary|包含次要 Vyatta 成員之專用及公用 IP 的物件
{: caption="表 1. config.json" caption-side="top"}

## rules.conf 參考資料
{: #reference_rules_conf}

|索引鍵|說明
|---|-------------|---|
|external_subnets|公用網際網路上要公開 Secure Perimeter 的子網路清單
|external_ports|要公開 Secure Perimeter 的埠清單
|userips|要列入 Secure Perimeter 白名單的使用者 IP 清單
{: caption="表 2. rules.conf" caption-side="top"}

## 必要條件
{: #prerequisites}

-   已從 IBM Cloud 基礎架構入口網站購買 Vyatta 與 VLAN，且 VLAN 已與 Vyatta 相關聯。
-   自動化 Secure Perimeter 部署已預先載入 Vyatta 以及 **ibmcloud-secure-perimeter-network** 用來存取閘道的 SSH 金鑰。SSH 金鑰需要以手動方式載入，或者透過 Secure Perimeter 安裝處理程序來載入。如需相關資訊，請檢閱[在 IBM Cloud 中設定自動化 Secure Perimeter](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/) 文章。
