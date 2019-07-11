---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-24"

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

# `ibm/liberty` イメージの概説
{: #ibmliberty}

IBM® WebSphere® Application Server Liberty (`ibm/liberty`) イメージは {{site.data.keyword.containerlong_notm}} 用に提供されています。
{:shortdesc}

{{site.data.keyword.IBM_notm}} によって提供されるイメージには、コマンド・ラインを使用してアクセスできます。[IBM のパブリック・イメージ](/docs/services/Registry?topic=registry-public_images#public_images)を参照してください。
{: tip}

## 機能
{: #ibmliberty_how_it_works}

`ibm/liberty` イメージを親として使用して独自のイメージを作成し、Java ベースの独自の WAR アプリ、EAR アプリ、または OSGi アプリを IBM WebSphere Application Server Liberty コンテナーにデプロイできます。
{:shortdesc}

## 含まれている内容
{: #ibmliberty_whats_included}

すべての Liberty イメージが以下のソフトウェア・パッケージを提供します。
{:shortdesc}

- IBM WebSphere Application Server for Developers Liberty
- IBM Java Runtime Environment 8.0

イメージにインストールされている具体的な Liberty フィーチャーは、選択したタグによって異なります。 次の表に、各 `ibm/liberty` イメージに含まれているフィーチャーを示します。 各フィーチャーについて詳しくは、[IBM Knowledge Center にある Liberty フィーチャーの概要 ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/SSAW57_liberty/com.ibm.websphere.wlp.nd.multiplatform.doc/ae/rwlp_feat.html)を参照してください。

|タグ|説明|
|---|-----------|
|すべての `ibm/liberty` イメージ|すべての `ibm/liberty` イメージに次のフィーチャーが含まれています。 <ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|`ibm/liberty:latest`|このイメージは `ibm/liberty:javaee8` イメージをポイントします。|
|`ibm/liberty:microProfile1`|このイメージには、[MicroProfile 1.x ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://microprofile.io/) で指定された機能を提供するフィーチャーが含まれています。|
|`ibm/liberty:microProfile2`|このイメージには、[MicroProfile 2.x ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://microprofile.io/) で指定された機能を提供するフィーチャーが含まれています。|
|`ibm/liberty:springBoot1`|このイメージには、[Spring Boot 1.x ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://spring.io/projects/spring-boot) で指定された機能を提供するフィーチャーが含まれています。|
|`ibm/liberty:springBoot2`|このイメージには、[Spring Boot 2.x ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://spring.io/projects/spring-boot) で指定された機能を提供するフィーチャーが含まれています。|
|`ibm/liberty:webProfile7`|このイメージには、Java EE 7 Web Profile 準拠に必要なすべてのフィーチャーが含まれています。|
|`ibm/liberty:webProfile8`|このイメージには、Java EE 8 Web Profile 準拠に必要なすべてのフィーチャーが含まれています。|
|`ibm/liberty:javaee7`|このイメージには、`ibm/liberty:webProfile7` イメージのすべてのフィーチャーに加えて、Java EE 7 Full Platform 準拠に必要なフィーチャーが含まれています。|
|`ibm/liberty:javaee8`|このイメージには、`ibm/liberty:webProfile8` イメージのすべてのフィーチャーに加えて、Java EE 8 Full Platform 準拠に必要なフィーチャーが含まれています。|
{: caption="表 1. 各 <code>ibm/liberty</code> イメージに含まれているフィーチャー" caption-side="top"}

## 使用上の制約事項
{: #ibmliberty_usage}

次の表は、{{site.data.keyword.cloud_notm}} での `ibm/liberty` イメージの無料使用に適用される制約事項を示しています。
{:shortdesc}

`ibm/liberty` イメージの価格設定は、{{site.data.keyword.cloud_notm}} で使用するコンテナーの価格設定とは無関係です。
{:tip}

|環境|無料使用上の制約事項|
|-----------|-----------------------|
|開発|**ibm/liberty** イメージの`無制限`の無料使用。|
|実動|`ibm/liberty` イメージの無料使用は、イメージが実行されるすべてのコンテナー・インスタンスを合わせて**最大 2 GB の Java ヒープ・スペース**に制限されています。 例えば、2 x 1 GB または 4 x 512 MB のヒープ Liberty インスタンスを無料で使用できます。|
{: caption="表 2. 価格設定" caption-side="top"}

コンテナー・インスタンスの Java ヒープ使用量をモニターするには、[CLI を使用した、コンテナーの Java ヒープ・スペース使用量のモニター](#ibmliberty_monitor_heap)を参照してください。

Docker Hub の [websphere-liberty イメージ ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://hub.docker.com/_/websphere-liberty/) の『License』セクションで、IBM 認定イメージのご利用条件を確認してください。

## 概説
{: #ibmliberty_get_started}

{{site.data.keyword.cloud_notm}} カタログにある無料の `ibm/liberty` イメージのいずれかを使用するか、実動ライセンス交付を受けた独自のイメージを選択して、単一コンテナーまたはコンテナー・グループを作成します。
{:shortdesc}

始める前に、`ibm/liberty` イメージの[使用上の制約事項](#ibmliberty_usage)を確認してください。
{: important}

1. カタログから、サイド・ペインで**「コンテナー」**>**「IBM Cloud Container Registry」**>**「IBM パブリック・リポジトリー (IBM Public Repositories)」**を選択します。 コンテナーをビルドする元になる `ibm/liberty` イメージを検索します。 実動ライセンス交付を受けた独自のイメージを作成して {{site.data.keyword.cloud_notm}} にデプロイした場合は、そのイメージをカタログから選択してください。 コンテナー作成ページが開きます。
2. 「**タグ/バージョン**」リストから、使用する `ibm/liberty` イメージのバージョンを選択します。
3. イメージからのコンテナーのビルド、クラスターのセットアップ、クラスターでのアプリのデプロイについて詳しくは、以下のリンク先を参照してください。

    - [イメージからのコンテナーのビルド](/docs/containers?topic=containers-images#images)
    - [IBM Cloud Kubernetes Service 概説](/docs/containers?topic=containers-getting-started#getting-started)
    - [アプリをクラスターにデプロイする](/docs/containers?topic=containers-app#app)

    `ibm/liberty` イメージでは、ポート 9080 がパブリックである必要があります。 {{site.data.keyword.cloud_notm}} ダッシュボードからコンテナーを作成する場合、このポートが**「パブリック・ポート」**フィールドにデフォルトで追加されます。 CLI からコンテナーを作成する場合は、`kubectl run` コマンドに `--port=9080` オプションを指定して実行し、ポートを公開してください。
    {:tip}

## CLI を使用した、コンテナーの Java ヒープ・スペース使用量のモニター
{: #ibmliberty_monitor_heap}

`ibm/liberty` イメージからコンテナーを作成した後、特定ポッドとそのコンテナーのメトリックを表示して、Java ヒープ使用量を確認できます。 Java ヒープ・スペースは、Java アプリケーションが実行時に使用できるメモリーです。
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

3. Java ヒープ使用量を確認するには、**RSS** のメモリー統計にアクセスする必要があります。[Get a Shell to a Running Container ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/) を参照してください。 Java ヒープ使用量はキロバイト (KB) 単位で表示されます。 すべてのインスタンスを合わせてヒープ使用量が 2097152 KB (2 GB) 未満であれば、WebSphere Application Server ライセンスを購入する必要はありません。

4. WebSphere Application Server インスタンスの最大ヒープ使用量を調整します。 詳しくは、[Setting generic JVM arguments in the WebSphere Application Server V8.5 Liberty profile ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www-01.ibm.com/support/docview.wss?uid=swg21596474)を参照してください。

## WebSphere Application Server ライセンスの取得
{: #ibm/liberty_license}

WebSphere Application Server ライセンスは、必要なプロセッサー・バリュー・ユニット (PVU) の数に基づきます。 PVU は、IBM ミドルウェア・ソフトウェアのライセンス交付のための測定単位です。 PVU の数は、ソフトウェアが使用できるプロセッサー (コア) の数を示します。
{:shortdesc}

{{site.data.keyword.cloud_notm}} 内のコンテナーのサイズごとに、特定の数の PVU 使用権が WebSphere Application Server ライセンスで使用可能でなければなりません。 したがって、ライセンスを購入する前に、`ibm/liberty` コンテナーを計画する必要があります。

WebSphere Application Server ライセンスを購入するには、[IBM サービス![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.ibm.com/us-en/marketplace/java-ee-runtime/purchase)にお問い合わせください。 WebSphere Application Server v8.5 以降のライセンスを既にお持ちの場合は、既存の使用権の中の未使用 PVU を、コンテナーのデプロイメントに使用できます。

ライセンス購入後にさらに PVU が必要であることが分かった場合は、[IBM サービス ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.ibm.com/us-en/marketplace/java-ee-runtime/purchase) に連絡して量を増やすことができます。

## {{site.data.keyword.containershort_notm}} で使用する、実動ライセンス交付を受けた `ibm/liberty` イメージの作成
{: #ibmliberty_prod_image}

WebSphere Application Server ライセンスを使用して、{{site.data.keyword.containershort_notm}} で使用できる、実動ライセンス交付を受けた `ibm/liberty` イメージを作成できます。 以下のタスクのいずれかを選択してください。
{:shortdesc}

- [Docker Hub からのイメージを実動イメージにアップグレードする ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade)。
- [実動ライセンス交付を受けた独自のイメージを作成する ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/WASdev/ci.docker/tree/master/ga)。

実動ライセンス交付を受けたイメージを作成した後、{{site.data.keyword.containershort_notm}} で使用するために、[イメージをプライベート・レジストリーにプッシュ](/docs/services/Registry?topic=registry-getting-started#getting-started)します。

## 提供イメージからのイメージの作成
{: #ibmliberty_creating_image}

`ibm/liberty` イメージのいずれかを、独自のアプリ・コードを含む子イメージを作成するための親として使用できます。 サンプル Dockerfile をカスタマイズし、ご使用のコンピューターで独自のイメージをビルドします。 次に、イメージを組織のプライベート・イメージ・レジストリーに追加し、そのイメージを使用してコンテナーを作成できます。
{:shortdesc}

始める前に、以下のステップを検討してください。

- 独自のアプリ・コードを、WAR ファイル、EAR ファイル、または OSGi ファイル内にビルドします。
- イメージをビルドするときに使用するディレクトリーにファイルをコピーします。

独自のアプリ・コードを含むイメージを `ibm/liberty` イメージから作成するには、以下のステップを実行します。

1. テキスト・エディターで、`Dockerfile` という名前のファイルを作成し、その中に以下の情報をコピーします。

   ```
   FROM icr.io/ibm/liberty:<tag>
   COPY <app_name>.<file_extension> /config/dropins/
   ```
   {: pre}

    ディレクトリー `/config` は、`/opt/ibm/wlp/usr/servers/defaultServer` のショートカットです。
    {: note}

2. `<tag>` を、アプリに必要なフィーチャーが含まれている `ibm/liberty` イメージのバージョンに置き換えます。

3. `<app_name>` をアプリ・ファイルの名前に置き換えます。

4. `<file_extension>` を、`.war`、`.ear`、`.eba` のいずれかに置き換えます。

5. 独自のアプリに関する他の従属物があれば Dockerfile に追加します。

6. イメージをビルドしてプライベート・イメージ・レジストリーにプッシュします。 詳しくは、[{{site.data.keyword.registrylong_notm}} 概説](/docs/services/Registry?topic=registry-getting-started#getting-started)を参照してください。

すべての `ibm/liberty` イメージは、コンテナー内のディレクトリー `/logs` に Liberty ログ・ファイルを書き込むように構成されています。 Liberty サーバーによって書き込まれる他のファイルはすべて、ディレクトリー `/opt/ibm/wlp/output/defaultServer` に作成されます。 これらのファイルには、ショートカット `/output` を使用してアクセスできます。
{:tip}

## `ibm/liberty` Dockerfile リファレンス
{: #ibmliberty_reference_dockerfile}

`ibm/liberty` イメージ Dockerfiles は、参照用に [WASdev/ci.docker GitHub リポジトリー ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/WASdev/ci.docker/tree/master/ga)にあります。
{:shortdesc}
