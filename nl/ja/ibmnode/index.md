---

copyright:
  years: 2017
lastupdated: "2017-10-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# **ibmnode** イメージ概説{: #getting_started_node}

パブリック {{site.data.keyword.IBM}} Node (**ibmnode**) イメージは {{site.data.keyword.containerlong_notm}} 用に提供されています。
{:shortdesc}

**注:** {{site.data.keyword.IBM_notm}} Node (**ibmnode**) イメージには、サンプル Node.js アプリは含まれていません。このイメージからコンテナーをデプロイすると、長時間実行プロセスが存在しないため、コンテナーはビルド直後にシャットダウンします。これを回避するには、{{site.data.keyword.IBM_notm}} Node (**ibmnode**) イメージを親として使用し、独自の Node.js アプリ・コードを追加することのできる独自のイメージを作成します。詳しくは、[この提供されたイメージからのイメージの作成](#creating_image)を参照してください。

# 機能{: #how_it_works}

{{site.data.keyword.IBM_notm}} Node (**ibmnode**) イメージを、独自の Node.js アプリ・コードを含む独自のイメージをビルドするための親として使用します。
{:shortdesc}

**注:** 始める前に、[websphere-liberty Docker ライブラリー ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/docker-library/docs/tree/master/websphere-liberty){: new_window} の『Usage』セクションで、IBM 認定イメージのご利用条件を確認してください。

# 含まれている内容{: #included}

* Ubuntu 14.04
* Python、Git、および build-essentials apt-get パッケージ
* {{site.data.keyword.IBM_notm}} Node SDK

    **注:** IBM Node SDK のさまざまなバージョンを実行できるよう、ibmnode イメージは異なるタグで使用可能です。

    <table>
    <caption> 表 1. さまざまな SDK バージョンのためのタグ </caption>
      <tr>
        <th> タグ </th>
        <th> 説明 </th>
      </tr>
      <tr>
        <td> ibmnode:latest </td>
        <td> IBM Node の最新バージョン。ibmnode:v4 を使用することと等価。</td>
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


# この提供されたイメージからのイメージの作成{: #creating_image}

**ibmnode** イメージを、独自のアプリ・コードを含んだ子イメージを作成するための親として使用できます。サンプルの `Hello World` アプリ・コンテナーを **ibmnode** および Express Node モジュールと共に使用して、ご使用のコンピューターにイメージをビルドします。そのイメージを組織のプライベート {{site.data.keyword.registrylong_notm}} に追加し、そのイメージを使用してコンテナーを作成します。その後、Hello World サンプルからの Dockerfile を使用して、独自のアプリで別のイメージを作成できます。
{:shortdesc}

始める前に、以下のステップを検討してください。

* 以下のツールをローカルにインストールする必要があります。
  * [Cloud Foundry および Docker CLI 用の {{site.data.keyword.registrylong_notm}} プラグイン](/docs/containers/container_cli_cfic_install.html)
  * [Node.js ソース・コード ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://nodejs.org/en/download/){: new_window}
  * [npm ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/npm/npm){: new_window}
* [組織のプライベート {{site.data.keyword.registryshort_notm}} からの名前空間を知っている必要があります。](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__namespace)

    例

    registry.DomainName/<var class="keyword varname">namespace</var>

* [IP アドレスを持っている必要があります。](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__ip_request)

**ibmnode** イメージをベースにした、Hello World アプリを含むイメージを作成します。
1.  [hellonode.zip ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](ftp://public.dhe.ibm.com/cloud/bluemix/containers/hellonode.zip){: new_window} をダウンロードし、解凍します。
1.  テキスト・エディターで Dockerfile を開き、FROM 命令内で、使用したい地域に合わせてレジストリー URL を更新します。

    <ul>
    <li>米国南部

        ```
		registry.ng.bluemix.net/ibmnode
		```
		{: codeblock}

    </li>
    <li>英国

        ```
		registry.eu-gb.bluemix.net/ibmnode
		```
		{: codeblock}

      </li>
    </ul>

1.  CLI から、**app** ディレクトリーに移動します。
1.  **app** ディレクトリー内で以下のコマンドを実行します。
    1.  package.json ファイルを作成します。

        ```
        npm init
        ```
        {: pre}

        **ヒント:** それぞれのプロンプトに対して、リターンキーを押してデフォルト値を受け入れます。

    2.  Express (Node.js 用のフレームワーク) をインストールします。package.json は、dependencies セクション内の Express のバージョン情報で更新されます。

        ```
        npm install express -save
        ```
        {: pre}

1.  **hellonode** ディレクトリーまで戻ります。
1.  イメージをビルドし、それをプライベート {{site.data.keyword.registryshort_notm}} にプッシュします。*

    ```
    bx ic build -t registry.<DomainName>/<namespace>/hellonode .
    ```
    {: pre}

    **ヒント:** 名前空間情報を取得するには、`bx ic namespace get` を実行します。_registry.DomainName_ を、以前に Dockerfile 内で使用したのと同じ地域に設定します。

    **注:** * [{{site.data.keyword.containershort_notm}} にログイン](/docs/containers/container_cli_cfic_install.html#container_cli_login) していて、ネイティブ Docker コマンドを使用するように環境変数を設定した場合は、このコマンドの `bx ic` を `docker` に置き換えることができます。このトピックでは、アスタリスク (*) のマークが付いているすべてのステップで、ネイティブ Docker コマンドを使用できます。

1.  イメージからコンテナーを作成します。*

    ```
    bx ic run -p 3000 --name helloworld registry.<DomainName>/<namespace>/hellonode
    ```
    {: pre}

1.  パブリック IP アドレスをコンテナーにバインドします。

    ```
    bx ic ip bind <IPaddress> helloworld
    ```
    {: pre}

    **ヒント:** `bx ic ip list` を実行することによってパブリック IP アドレスを確認できます。新しいものを要求するには、`bx ic ip request` を実行します。

1. ブラウザーで、**<var class="varname">IPaddress</var>:3000** を開くことによって、アプリをテストします。Hello World! というメッセージが表示されます。

これで hellonode イメージの作成が終わりました。代わりに独自のアプリを含むように Dockerfile を変更し、そのアプリ用に別のイメージを作成することができます。

1.  ADD 命令では、独自のアプリケーション・コードを追加します。
1.  EXPOSE 命令では、ポート情報を更新します。
1.  CMD 命令では、アプリを開始するコマンドを作成します。


# **ibmnode** Dockerfile リファレンス {: #reference_dockerfile}

この Dockerfile は、{{site.data.keyword.Bluemix_notm}} カタログ内の **ibmnode** イメージを作成するために使用されたものです。この情報は、あくまでも参考用です。このイメージのビルド済みバージョンに含まれている他のファイルは提供されません。
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
