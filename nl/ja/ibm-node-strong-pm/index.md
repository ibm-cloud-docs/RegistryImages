---

copyright:
  years: 2015, 2017
lastupdated: "2017-10-30"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}

# ibm-node-strong-pm イメージ概説
{: #getting_started_node_strong}

{{site.data.keyword.IBM}} Node with StrongLoop (**ibm-node-strong-pm**) イメージは {{site.data.keyword.containerlong_notm}} 用に提供されています。
{:shortdesc}

## 機能 
{: #how_it_works}

**ibm-node-strong-pm** イメージを使用して、[StrongLoop Process Manager](https://www.strongloop.com) の実行中インスタンスをデプロイする単一コンテナーを作成します。Strongloop Process Manager を使用すると、リモート・マシン上に実装された Node.js アプリを {{site.data.keyword.IBM_notm}} Cloud 中でデプロイ、管理、およびモニターすることができます。
{:shortdesc}

StrongLoop Arc のインスタンスがリモート・マシンにインストールされている必要があります。StrongLoop Arc は、StrongLoop プラットフォームのためのグラフィカル・ユーザー・インターフェースであり、Node.js アプリのビルド、プロファイル作成、およびモニタリングのためのさまざまなツールが含まれています。StrongLoop Arc を使用してアプリをビルドした後、StrongLoop Process Manager が実行されている {{site.data.keyword.Bluemix_notm}} 内のコンテナーにそのアプリをプッシュし、デプロイします。リモート・マシンとコンテナーの間の接続を確立するため、コンテナーにパブリック IP アドレスをバインドし、StrongLoop Arc で StrongLoop Process Manager サーバーとしてその IP アドレスをセットアップする必要があります。StrongLoop Process Manager を介してアプリがデプロイされると、StrongLoop Process Manager でアプリのメトリックおよびパフォーマンスをモニターすることができます。アプリを更新したり、拡大または縮小したりするには、StrongLoop Arc を使用してアプリを変更した後、変更内容をコンテナーにプッシュする必要があります。

**注:** **ibm-node-strong-pm** を {{site.data.keyword.Bluemix_notm}} 内のコンテナー・グループと共に使用することはサポートされていません。

## 含まれている内容
{: #whats_included}

-   **ibmnode:latest** イメージに含まれているすべてのソフトウェア・パッケージ。{{site.data.keyword.Bluemix_notm}} 用 **ibmnode** イメージ概説のトピックで『[含まれている内容](/docs/services/RegistryImages/ibmnode/index.html#getting_started_node)』を参照してください。
-   StrongLoop Process Manager

## 概説
{: #how_to_get_started}

{{site.data.keyword.Bluemix_notm}} カタログから **ibm-node-strong-pm** イメージを使用して、{{site.data.keyword.containershort_notm}} で単一コンテナーを試すことができます。このイメージは、コンテナーが使用可能になったらすぐにアクセスできる、StrongLoop Process Manager の実行中インスタンスと共に開始されます。その Process Manager に接続された StrongLoop Arc のインスタンスを使用して、Node.js アプリケーションをプッシュし、それにアクセスすることができます。
{:shortdesc}

**注:** 始める前に、[websphere-liberty Docker ライブラリー](https://github.com/docker-library/docs/tree/master/websphere-liberty) の『Usage』セクションで、{{site.data.keyword.IBM_notm}} 認定イメージのご利用条件を確認してください。

1.  カタログから、**「コンテナー」**を選択し、コンテナーをビルドする元になる **ibm-node-strong-pm** イメージを選択します。
2.  単一コンテナーを作成します。
    -   **「サイズ」**フィールドで、1 GB メモリー (**「スモール」**) 以上のコンテナー・サイズを選択します。
    -   **「パブリック・ポート」**フィールドに、ポート 8701 および 3001 を入力します。これらのポートは、{{site.data.keyword.Bluemix_notm}} にコンテナーがデプロイされるときに自動的に公開され、コンテナーにパブリック IP アドレスをバインドする前提条件です。ポート 8701 は、コンテナー内の StrongLoop Process Manager にアクセスするために使用されます。ポート 3001 は、StrongLoop Process Manager にデプロイされた後にアプリが listen するポートです。

        **注:** 追加のアプリを StrongLoop Process Manager にデプロイするには、さらに多くのポートを公開してください。それらのポートは、3002 から始まる連続した番号でなければなりません。例えば、3002、3003、3004 などです。

    -   詳しくは、[{{site.data.keyword.Bluemix_notm}} ダッシュボードを使用した単一コンテナーの作成](/docs/containers/container_single_ui.html#gui)を参照してください。
3.  コンテナーにパブリック IP アドレスをバインドします。詳しくは、[IP アドレスの要求とバインド](/docs/containers/container_single_ui.html#container_cli_ips)を参照してください。

## リモート・マシン上での StrongLoop Arc のセットアップ
{: #UsingSLA}

{{site.data.keyword.Bluemix_notm}} で **ibm-node-strong-pm** イメージから単一コンテナーを作成した後、リモート・マシンに StrongLoop Arc をセットアップします。
{:shortdesc}

始めに、リモート・マシンに Node.js およびノード・パッケージ・マネージャー (npm) をインストールします。

1.  StrongLoop をダウンロードし、以下のコマンドを入力してインストールします。StrongLoop には、Arc 実行に必要なすべてのパッケージが含まれています。

    ```
    npm install -g strongloop
    ```
    {: pre}

2.  StrongLoop がインストールされたら、StrongLoop Arc を実行します。

    ```
    slc arc
    ```
    {: pre}

3.  プロンプトが出されたら、StrongLoop に登録またはログインします。
4.  StrongLoop Arc で、コンテナー内で実行される StrongLoop Process Manager を Process Manager リストに追加します。
    1.  Arc モジュール・セレクターから、**「Process Manager」**に移動します。
    2.  コンテナーにバインドしたパブリック IP アドレス、およびパブリック・ポート 8701 を、Arc Process Manager に追加します。
    3.  **「有効にする (Activate)」**をクリックします。

## StrongLoop Arc の使用によるコンテナーへの Node.js アプリのプッシュ
{: #pushing_app_to_container}

リモート・マシンで StrongLoop Arc クライアントを使用して、コンテナー内で実行されている StrongLoop Process Manager に接続した後、Node.js アプリをビルドし、{{site.data.keyword.Bluemix_notm}} にプッシュします。
{:shortdesc}

1.  Arc モジュール・セレクターから、**「ビルドおよびデプロイ (Build and Deploy)」**に移動します。
2.  Arc ユーザー・インターフェースの指示に従ってアプリをビルドします。

    **重要:** StrongLoop Arc を使用して作成した Node.js アプリが、環境変数 PORT とコンテナー作成時に公開したポートの両方で listen していることを確認してください。

    例

    ```
    app.listen(process.env.PORT || 3001);
    ```
    {: pre}

3.  Arc ユーザー・インターフェースの指示に従ってアプリをデプロイします。{{site.data.keyword.Bluemix_notm}} にアプリをデプロイするように構成したプロセス・マネージャーを選択します。
4.  デプロイメント・プロセスが終了するまで待ちます。
5.  アプリにアクセスします。
    1.  Web ブラウザーを開きます。
    2.  コンテナーの IP アドレスとポートを、<var class="keyword varname">IP_ADDRESS:PORT</var> という形式で入力します。

        例

        ```
        192.0.2.26:3001
        ```
        {: pre}

    3.  {{site.data.keyword.Bluemix_notm}} にデプロイしたアプリごとに、これらのステップを繰り返します。
6.  Arc のトレース、メトリック、およびプロファイラーを使用して、{{site.data.keyword.Bluemix_notm}} で実行中のアプリについてのパフォーマンス情報を取得します。詳細については、StrongLoop 資料の [Operating Node applications](https://docs.strongloop.com/display/SLC/Operating+Node+applications) を参照してください。

## **ibm-node-strong-pm** Dockerfile リファレンス 
{: #reference_dockerfile}

この Dockerfile は、{{site.data.keyword.Bluemix_notm}} カタログにある **ibm-node-strong-pm** イメージの作成に使用されたものです。この情報は、あくまでも参考用です。このイメージのビルド済みバージョンに含まれている他のファイルは提供されません。

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


