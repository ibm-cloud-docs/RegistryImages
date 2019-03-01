---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-21"

keywords: ibmliberty, container image, IBM WebSphere Application Server Liberty, liberty, public image

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

# `ibmliberty` イメージ概説
{: #ibmliberty}

IBM® WebSphere® Application Server Liberty (`ibmliberty`) イメージは {{site.data.keyword.containerlong_notm}} 用に提供されています。
{:shortdesc}

{{site.data.keyword.IBM_notm}} によって提供されるイメージには、コマンド・ラインを使用してアクセスできます。[IBM のパブリック・イメージ](/docs/services/Registry?topic=registry-public_images#public_images)を参照してください。
{: tip}

## 機能
{: #ibmliberty_how_it_works}

`ibmliberty` イメージを親として使用して独自のイメージを作成し、Java ベースの独自の WAR アプリ、EAR アプリ、または OSGi アプリを IBM WebSphere Application Server Liberty コンテナーにデプロイできます。
{:shortdesc}

## 含まれている内容
{: #ibmliberty_whats_included}

すべての Liberty イメージが以下のソフトウェア・パッケージを提供します。
{:shortdesc}

- IBM WebSphere Application Server for Developers Liberty
- IBM Java Runtime Environment 8.0

イメージにインストールされている具体的な Liberty フィーチャーは、選択したタグによって異なります。 次の表に、各 `ibmliberty` イメージに含まれているフィーチャーを示します。 各フィーチャーについて詳しくは、[IBM Knowledge Center にある Liberty フィーチャーの概要 ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](http://www.ibm.com/support/knowledgecenter/SSAW57_8.5.5/com.ibm.websphere.wlp.nd.doc/ae/rwlp_feat.html)を参照してください。

|タグ|説明|
|---|-----------|
|すべての `ibmliberty` イメージ|すべての `ibmliberty` イメージに次のフィーチャーが含まれています。 <ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|`ibmliberty:latest`|このイメージは `ibmliberty:javaee7` イメージをポイントします。|
|`ibmliberty:microProfile`|このイメージには、[MicroProfile ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://microprofile.io) で指定された機能を提供するフィーチャーが含まれています。|
|`ibmliberty:webProfile7`|このイメージには、Java EE7 Web Profile 準拠に必要なすべてのフィーチャーが含まれています。|
|`ibmliberty:javaee7`|このイメージには、`ibmliberty:webProfile7` イメージのすべてのフィーチャーに加えて、Java EE7 Full Platform 準拠に必要なフィーチャーが含まれています。|
{: caption="表 1. 各 `ibmliberty` イメージに含まれているフィーチャーを示します。" caption-side="top"}

## 使用上の制約事項
{: #ibmliberty_usage}

次の表は、{{site.data.keyword.Bluemix_notm}} での `ibmliberty` イメージの無料使用に適用される制約事項を示しています。
{:shortdesc}

`ibmliberty` イメージの価格設定は、{{site.data.keyword.Bluemix_notm}} で使用するコンテナーの価格設定とは無関係です。
{:tip}

|環境|無料使用上の制約事項|
|-----------|-----------------------|
|開発|**ibmliberty** イメージの`無制限`の無料使用。|
|実動|`ibmliberty` イメージの無料使用は、イメージが実行されるすべてのコンテナー・インスタンスを合わせて**最大 2 GB の Java ヒープ・スペース**に制限されています。 例えば、2 x 1 GB または 4 x 512 MB のヒープ Liberty インスタンスを無料で使用できます。|
{: caption="表 2. 価格設定" caption-side="top"}

コンテナー・インスタンスの Java ヒープ使用量をモニターするには、[CLI を使用した、コンテナーの Java ヒープ・スペース使用量のモニター](#ibmliberty_monitor_heap)を参照してください。

Docker Hub の [websphere-liberty イメージ ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://hub.docker.com/_/websphere-liberty/) の『License』セクションで、IBM 認定イメージのご利用条件を確認してください。

## 概説
{: #ibmliberty_get_started}

{{site.data.keyword.Bluemix_notm}} カタログにある無料の `ibmliberty` イメージのいずれかを使用するか、実動ライセンス交付を受けた独自のイメージを選択して、単一コンテナーまたはコンテナー・グループを作成します。
{:shortdesc}

始める前に、`ibmliberty` イメージの[使用上の制約事項](#ibmliberty_usage)を確認してください。
{: important}

1. カタログから、サイド・パネルで**「コンテナー」**>**「IBM Cloud Container Registry」**>**「IBM パブリック・リポジトリー (IBM Public Repositories)」**を選択します。 コンテナーをビルドする元になる `ibmliberty` イメージを検索します。 実動ライセンス交付を受けた独自のイメージを作成して {{site.data.keyword.Bluemix_notm}} にデプロイした場合は、そのイメージをカタログから選択してください。 コンテナー作成ページが開きます。
2. `「タグ/バージョン」`ドロップダウン・ボックスから、使用する **ibmliberty** イメージのバージョンを選択します。
3. イメージからのコンテナーのビルド、クラスターのセットアップ、クラスターでのアプリのデプロイについて詳しくは、以下のリンク先を参照してください。

    - [イメージからのコンテナーのビルド](/docs/containers?topic=containers-images#images)
    - [IBM Cloud Kubernetes Service 概説](/docs/containers?topic=containers-container_index#container_index)
    - [アプリをクラスターにデプロイする](/docs/containers?topic=containers-app#app)

    `ibmliberty` イメージを使用するには、ポート 9080 がパブリックに公開される必要があります。 {{site.data.keyword.Bluemix_notm}} ダッシュボードからコンテナーを作成する場合、このポートが**「パブリック・ポート」**フィールドにデフォルトで追加されます。 CLI からコンテナーを作成する場合は、`kubectl run` コマンドに `--port=9080` オプションを指定して実行し、ポートを公開してください。
    {:tip}

## CLI を使用した、コンテナーの Java ヒープ・スペース使用量のモニター
{: #ibmliberty_monitor_heap}

`ibmliberty` イメージからコンテナーを作成した後、特定ポッドとそのコンテナーのメトリックを表示して、Java ヒープ使用量を確認できます。 Java ヒープ・スペースは、Java アプリケーションが実行時に使用できるメモリーです。
{:shortdesc}

1. メトリックを表示するポッドの名前を取得します。
  
   ```
   kubectl get pods
   ```
   {: pre}

2. 特定ポッドとそのコンテナーのメトリックを表示します。

   ```
   kubectl top pod POD_NAME --containers
   ```
   {: pre}

3. Java ヒープ使用量を確認するには、**RSS** のメモリー統計にアクセスする必要があります。 コンテナーのシェルへのアクセス方法に関するガイドラインに従って、[Get a Shell to a Running Container ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/) を参照してください。 Java ヒープ使用量はキロバイト単位で表示されます。 すべてのインスタンスを合わせてヒープ使用量が 2097152 キロバイト (2 GB) 未満であれば、WebSphere Application Server ライセンスを購入する必要はありません。

4. WebSphere Application Server インスタンスの最大ヒープ使用量を調整します。 詳しくは、[Setting generic JVM arguments in the WebSphere Application Server V8.5 Liberty profile ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](http://www-01.ibm.com/support/docview.wss?uid=swg21596474)を参照してください。

## WebSphere Application Server ライセンスの取得
{: #ibmliberty_license}

WebSphere Application Server ライセンスは、必要なプロセッサー・バリュー・ユニット (PVU) の数に基づきます。 PVU は、IBM ミドルウェア・ソフトウェアのライセンス交付のための測定単位です。 PVU の数は、ソフトウェアが使用できるプロセッサー (コア) の数を示します。
{:shortdesc}

{{site.data.keyword.Bluemix_notm}} 内のコンテナーのサイズごとに、特定の数の PVU 使用権が WebSphere Application Server ライセンスで使用可能でなければなりません。 したがって、ライセンスを購入する前に、`ibmliberty` コンテナーを計画する必要があります。

WebSphere Application Server ライセンスを購入するには、[IBM サービス![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us)にお問い合わせください。 WebSphere Application Server v8.5 以降のライセンスを既にお持ちの場合は、既存の使用権の中の未使用 PVU を、コンテナーのデプロイメントに使用できます。

ライセンス購入後にさらに PVU が必要であることが分かった場合は、[IBM サービス ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us) に連絡して量を増やすことができます。

## {{site.data.keyword.containershort_notm}} で使用する、実動ライセンス交付を受けた `ibmliberty` イメージの作成
{: #ibmliberty_prod_image}

WebSphere Application Server ライセンスを使用して、{{site.data.keyword.containershort_notm}} で使用できる、実動ライセンス交付を受けた `ibmliberty` イメージを作成できます。 以下のタスクのいずれかを選択してください。
{:shortdesc}

- [Docker Hub からのイメージを実動イメージにアップグレードする ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade)。
- [実動ライセンス交付を受けた独自のイメージを作成する ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/WASdev/ci.docker/tree/master/ga)。

実動ライセンス交付を受けたイメージを作成した後、{{site.data.keyword.containershort_notm}} で使用するために、[イメージをプライベート・レジストリーにプッシュ](/docs/services/Registry?topic=registry-index#index)します。

## 提供イメージからのイメージの作成
{: #ibmliberty_creating_image}

`ibmliberty` イメージのいずれかを、独自のアプリ・コードを含む子イメージを作成するための親として使用できます。 サンプル Dockerfile をカスタマイズし、ご使用のコンピューターで独自のイメージをビルドします。 次に、イメージを組織のプライベート・イメージ・レジストリーに追加し、そのイメージを使用してコンテナーを作成できます。
{:shortdesc}

始める前に、以下のステップを検討してください。

- 独自のアプリ・コードを、WAR ファイル、EAR ファイル、または OSGi ファイル内にビルドします。
- イメージをビルドするときに使用するディレクトリーにファイルをコピーします。

独自のアプリ・コードを含むイメージを `ibmliberty` イメージから作成するには、以下のステップを実行します。

1. テキスト・エディターで、`Dockerfile` という名前のファイルを作成し、その中に以下の情報をコピーします。

   ```
   FROM registry.bluemix.net/ibmliberty:<tag>
   COPY <app_name>.<file_extension> /config/dropins/
   ```
   {: pre}

    ディレクトリー `/config` は、`/opt/ibm/wlp/usr/servers/defaultServer` のショートカットです。
    {: note}

2. `<tag>` を、アプリに必要なフィーチャーが含まれている `ibmliberty` イメージのバージョンに置き換えます。

3. `<app_name>` をアプリ・ファイルの名前に置き換えます。

4. `<file_extension>` を、`.war`、`.ear`、`.eba` のいずれかに置き換えます。

5. 独自のアプリに関する他の従属物があれば Dockerfile に追加します。

6. イメージをビルドしてプライベート・イメージ・レジストリーにプッシュします。 詳しくは、[{{site.data.keyword.registrylong_notm}} 概説](/docs/services/Registry?topic=registry-index#index)を参照してください。

すべての `ibmliberty` イメージは、コンテナー内のディレクトリー `/logs` に Liberty ログ・ファイルを書き込むように構成されています。 Liberty サーバーによって書き込まれる他のファイルはすべて、ディレクトリー `/opt/ibm/wlp/output/defaultServer` に作成されます。 これらのファイルには、ショートカット `/output` を使用してアクセスできます。
{:tip}

## `ibmliberty` Dockerfile リファレンス
{: #ibmliberty_reference_dockerfile}

この Dockerfile は、Docker Hub 上のパブリック websphere-liberty イメージから {{site.data.keyword.Bluemix_notm}} の `ibmliberty:webProfile7` イメージがどのようにビルドされるかを示しています。 この情報は、あくまでも参考用です。
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
