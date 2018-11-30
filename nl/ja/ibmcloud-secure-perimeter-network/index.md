---

copyright:
  years: 2018
lastupdated: "2018-10-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# `ibmcloud-secure-perimeter-network` イメージの概説
{: #ibmcloud-secure-perimeter-network}

`ibmcloud-secure-perimeter-network` イメージには、Secure Perimeter 内の Vyatta 仮想ルーター・アプライアンスの構成を自動化するためのツールが含まれています。
{:shortdesc}

{{site.data.keyword.IBM}} によって提供されるイメージには、コマンド・ラインを使用してアクセスできます。[IBM のパブリック・イメージ](/docs/services/Registry/registry_public_images.html#public_images)を参照してください。
{: tip}

## 機能
{: #how-it-works}

`ibmcloud-secure-perimeter-network` を使用すると、Secure Perimeter の Vyatta 仮想ルーター・アプライアンスの構成を自動化できます。

Secure Perimeter について詳しくは、以下のブログ記事を参照してください。
  * [Set up a Secure Perimeter in IBM Cloud](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/)
  * [Set up an automated Secure Perimeter in IBM Cloud](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/)

`ibmcloud-secure-perimeter-network` イメージは、以下の 2 つの方法で使用できます。
-  `ibmcloud-secure-perimeter-network` を Docker コンテナーとして使用して、Secure Perimeter ファイアウォール・ルール構成を初期化する。
-  `ibmcloud-secure-perimeter-network` を Kubernetes クラスター上のポッドとして使用して、Secure Perimeter Segment VLAN 上に作成された新規サブネットについて IBM Cloud インフラストラクチャー・アカウントをポーリングし、それらを Vyatta ファイアウォール構成に追加する。

## 含まれている内容
{: #whats_included}

`ibmcloud-secure-perimeter-network` イメージに、以下のソフトウェア・パッケージが用意されています。
{:shortdesc}

-   Alpine Linux
-   Python ランタイム
-   SoftLayer Python クライアント
-   Ansible

## 概説
{: #how_to_get_started}

`ibmcloud-secure-perimeter-network` の使用法について、以下のタスクで確認してください。

1.  [{{site.data.keyword.containerlong}} を使用して Secure Perimeter 内で Kubernetes クラスターをプロビジョンする](#provision_cluster)
2.  [Secure Perimeter Vyatta の初期構成を実行する](#initial_setup)
3.  [Secure Perimeter 内の Kubernetes ポッドとしてセットアップする](#setup)
4.  [config.json リファレンス](#reference_config_json)
5.  [rules.conf リファレンス](#reference_rules_conf)

## {{site.data.keyword.containerlong_notm}} を使用して Secure Perimeter 内で Kubernetes クラスターをプロビジョンする
{: #provision_cluster}

1.  IBM Cloud カタログ内の**「コンテナー」**セクションから Kubernetes クラスターをプロビジョンします。
2.  **「作成」**をクリックします。
3.  VLAN ドロップダウン・メニューから Secure Perimeter Segment パブリックおよびプライベート VLAN を選択します。
4.  必要に応じて他のすべての詳細を入力します。
5.  **「クラスターの作成」**をクリックします。

クラスターがデプロイされた後、クラスターにアクセスする方法については、[{{site.data.keyword.containerlong_notm}}](/docs/containers/container_index.html#container_index) 資料を参照してください。

## Secure Perimeter Vyatta の初期構成を実行する
{: #initial_setup}

1. _config.json_ という名前のファイルを作成します。 このファイルには、Vyatta にアクセスするために `ibmcloud-secure-perimeter-network` が必要とする基本パラメーターが含まれます。

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

  _config.json_ にデータを設定する方法の詳細については、[config.json リファレンス表](#reference_config_json)を参照してください。 このファイルは、[Kubernetes ポッドとしての `ibmcloud-secure-perimeter-network` のセットアップ](#setup)のプロセスでも使用できます。

2. `ibmcloud-secure-perimeter-network` を Docker コンテナーとして実行して、初期セットアップを開始します。

  ```
  docker run registry.bluemix.net/ibm/ibmcloud-secure-perimeter-network:1.0.0 python config-secure-perimeter.py -v /path/to/current/dir:/opt/secure-perimeter
  ```
  {: pre}

  この操作により、作業ディレクトリー内に _state.json_ ファイルが作成されます。 このファイルを [Kubernetes ポッドとしての `ibmcloud-secure-perimeter-network` のセットアップ](#setup)で使用します。

## Secure Perimeter 内の Kubernetes ポッドとしてセットアップする
{: #setup}

`ibmcloud-secure-perimeter-network` イメージが Secure Perimeter 上のサブネットを管理できるように、Kubernetes ポッドを使用して長期継続プロセスとしてこのイメージを実行できます。 `ibmcloud-secure-perimeter-network` には、ポッドにコピーしたうえで Vyatta 用に構成する必要があるいくつかの構成ファイルとフォルダーがあります。

1. _pvc.yaml_ という名前のファイルを作成します。 この構成ファイルは、ポッドにボリュームとしてマウントできる、永続ボリューム・クレーム (pvc) を作成します。

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

2. pvc を作成します。

    ```
    kubectl apply -f restore-pvc.yaml
    ```
    {: pre}

3. _network-pod.yaml_ という名前のファイルを作成します。 この構成ファイルは、`ibmcloud-secure-perimeter-network` イメージを Kubernetes クラスター内のポッドとしてデプロイし、永続ボリューム・クレームをボリュームとしてマウントします。

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
          image: registry.bluemix.net/ibm/ibmcloud-secure-perimeter-network:1.0.0
          volumeMounts:
          - name: network-vol
            mountPath: /opt/secure-perimeter
        volumes:
        - name: network-vol
          PersistentVolumeClaim:
            claimName: network-pvc
  ```
  {: codeblock}

4. _rules.conf_ という名前のファイルを作成します。 この構成ファイルは、パブリック・インターネットからどの外部サブネットおよび外部ポートを Secure Perimeter へのホワイトリストに登録するかを `ibmcloud-secure-perimeter-network` に指示します。

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

5. ファイルを `ibmcloud-secure-perimeter-network` ポッドにコピーします。

  ```
  kubectl cp keys network-pod:/opt/secure-perimeter/
  kubectl cp state.json network-pod:/opt/secure-perimeter/state.json
  kubectl cp config.json network-pod:/opt/secure-perimeter/config.json
  kubectl cp rules.conf network-pod:/opt/secure-perimeter/rules.conf
  ```
  {: pre}

  _keys_ ディレクトリーに、`ibmcloud-secure-perimeter-network` が Vyatta にアクセスするために必要な SSH 鍵が含まれています。 SSH 鍵について詳しくは、[前提条件セクション](#prerequisites)を参照してください。

## config.json リファレンス
{: #reference_config_json}

|キー|説明
|---|-------------|---|
|slid|IBM Cloud インフラストラクチャー・ユーザー名
|apikey|IBM Cloud インフラストラクチャー API 鍵
|region|Vyatta がデプロイされる IBM Cloud 地域
|inf_name_private|Vyatta プライベート・インターフェースの名前
|inf_name_public|Vyatta パブリック・インターフェースの名前
|gatewayid|Vyatta ゲートウェイ ID
|vlans|タイプ、VLAN 番号、および VLAN ID を含む、Secure Perimeter Segment VLAN のリスト
|vyatta_gateway_vip|ゲートウェイの VIP
|vyatta_primary|1 次 Vyatta メンバーのプライベートおよびパブリック IP を含んでいるオブジェクト
|vyatta_secondary|2 次 Vyatta メンバーのプライベートおよびパブリック IP を含んでいるオブジェクト
{: caption="表 1. config.json" caption-side="top"}

## rules.conf リファレンス
{: #reference_rules_conf}

|キー|説明
|---|-------------|---|
|external_subnets|Secure Perimeter を公開するパブリック・インターネット上のサブネットのリスト
|external_ports|Secure Perimeter を公開するポートのリスト
|userips|Secure Perimeter へのホワイトリストに登録するユーザー IP のリスト
{: caption="表 2. rules.conf" caption-side="top"}

## 前提条件
{: #prerequisites}

-   IBM Cloud インフラストラクチャー・ポータルから Vyatta および VLAN を注文済みで、VLAN が Vyatta に関連付けられていること。
-   自動化された Secure Perimeter デプロイメントによって、`ibmcloud-secure-perimeter-network` がゲートウェイにアクセスするために使用する SSH 鍵を使用して Vyatta がプリロードされること。 SSH 鍵は手動でロードされるか、Secure Perimeter インストール・プロセスを介してロードされる必要があります。 詳しくは、[Set up an automated Secure Perimeter in IBM Cloud ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/) の記事を参照してください。
