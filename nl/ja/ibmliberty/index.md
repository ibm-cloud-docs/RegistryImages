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

# **ibmliberty** イメージ概説
{: #ibmliberty}

IBM® WebSphere® Application Server Liberty (**ibmliberty**) イメージは {{site.data.keyword.containerlong_notm}} 用に提供されています。
{:shortdesc}

## 機能 
{: #how_it_works}

**ibmliberty** イメージを親として使用して独自のイメージを作成し、Java ベースの独自の WAR アプリ、EAR アプリ、または OSGi アプリを IBM WebSphere Application Server Liberty コンテナーにデプロイできます。
{:shortdesc}

## 含まれている内容 
{: #whats_included}

すべての Liberty イメージが以下のソフトウェア・パッケージを提供します。
{:shortdesc}

-   IBM WebSphere Application Server for Developers Liberty
-   IBM Java Runtime Environment 8.0

イメージにインストールされている具体的な Liberty フィーチャーは、選択したタグによって異なります。次の表に、各 **ibmliberty** イメージに含まれているフィーチャーを示します。各フィーチャーの詳細については、[IBM Knowledge Center にある Liberty フィーチャーの概要](http://www.ibm.com/support/knowledgecenter/SSAW57_8.5.5/com.ibm.websphere.wlp.nd.doc/ae/rwlp_feat.html)を参照してください。

|タグ|説明|
|---|-----------|
|すべての **ibmliberty** イメージ|すべての **ibmliberty** イメージに次のフィーチャーが含まれています。<ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|**ibmliberty:latest**|このイメージは **ibmliberty:javaee7** イメージをポイントします。|
|**ibmliberty:webProfile6**|このイメージには、Java EE6 Web Profile 準拠に必要なすべてのフィーチャーが含まれています。ランタイム JAR を使用して [http://wasdev.net/](http://wasdev.net/) からダウンロード可能なフィーチャーとコンテンツを調和させるために、追加のフィーチャーも取り込まれます (特に、OSGi アプリケーションに必要なフィーチャー)。|
|**ibmliberty:webProfile7**|このイメージには、Java EE7 Web Profile 準拠に必要なすべてのフィーチャーが含まれています。|
|**ibmliberty:javaee7**|このイメージには、**ibmliberty:webProfile7** イメージのすべてのフィーチャーに加えて、Java EE7 Full Platform 準拠に必要なフィーチャーが含まれています。|

## 使用上の制約事項 
{: #usage}

次の表は、{{site.data.keyword.Bluemix_notm}} での **ibmliberty** イメージの無料使用に適用される制約事項を示しています。
{:shortdesc}

**注:** **ibmliberty** イメージの価格設定は、{{site.data.keyword.Bluemix_notm}} で使用するコンテナーの価格設定とは無関係です。

|環境|無料使用上の制約事項|
|-----------|-----------------------|
|開発|**ibmliberty** イメージの**無制限**の無料使用。|
|実動|**ibmliberty** イメージの無料使用は、イメージが実行されるすべてのコンテナー・インスタンスを合わせて**最大 2 GB の Java ヒープ・スペース**に制限されています。例えば、2 x 1 GB または 4 x 512 MB のヒープ Liberty インスタンスを無料で使用できます。
コンテナー・インスタンスの Java ヒープ使用量をモニターするには、[CLI を使用した、コンテナーの Java ヒープ・スペース使用量のモニター](#monitor_heap)を参照してください。


Docker Hub の [websphere-liberty イメージ](https://hub.docker.com/_/websphere-liberty/)の『License』セクションで、IBM 認定イメージのご利用条件を確認してください。

## 概説 
{: #get_started}

{{site.data.keyword.Bluemix_notm}} カタログにある無料の **ibmliberty** イメージのいずれかを使用するか、実動ライセンス交付を受けた独自のイメージを選択して、単一コンテナーまたはコンテナー・グループを作成します。
{:shortdesc}

**重要:** 始める前に、**ibmliberty** イメージの[使用上の制約事項](#usage)を確認してください。

1.  カタログから、**「コンテナー」**を選択し、コンテナーをビルドする元になる **ibmliberty** イメージを選択します。実動ライセンス交付を受けた独自のイメージを作成して {{site.data.keyword.Bluemix_notm}} にデプロイした場合は、そのイメージをカタログから選択してください。コンテナー作成ページが開きます。
2.  **「タグ/バージョン」**ドロップダウン・ボックスから、使用する **ibmliberty** イメージのバージョンを選択します。
3.  単一コンテナーを作成するか、スケーラブル・コンテナー・グループを作成するかを選択します。コンテナーの作成方法について詳しくは、以下のトピックを参照してください。

    -   [{{site.data.keyword.Bluemix_notm}} ダッシュボードを使用した単一コンテナーの作成](/docs/containers/container_single_ui.html#gui)
    -   [{{site.data.keyword.Bluemix_notm}} ダッシュボードを使用したコンテナー・グループの作成](/docs/containers/container_ha.html#container_group_ui)
    
    **注:** **ibmliberty** イメージを使用するは、ポート 9080 がパブリックに公開される必要があります。{{site.data.keyword.Bluemix_notm}} ダッシュボードからコンテナーを作成する場合、このポートが**「パブリック・ポート」**フィールドにデフォルトで追加されます。CLI からコンテナーを作成する場合は、`bx ic run` コマンドでこのポートを公開してください。


## CLI を使用した、コンテナーの Java ヒープ・スペース使用量のモニター 
{: #monitor_heap}


**ibmliberty** イメージからコンテナーを作成した後、実行中のプロセスをすべてリストして、Java ヒープ使用量を確認できます。Java ヒープ・スペースは、Java アプリケーションが実行時に使用できるメモリーです。
{:shortdesc}

1.  コンテナー内の実行中のプロセスをすべてリストします。

    ```
    bx ic top CONTAINER -aux
    ```
    {: pre}

    CLI 出力は、以下のようになります。

    ```    
    USER        PID       %CPU   %MEM    VSZ         RSS        TTY     STAT   START    TIME   COMMAND
    contain+    3322245   3.2    0.0     11522856    216192     ?       Ssl    14:43    0:35   /opt/ibm/java/jre/bin/java -javaagent:/opt/ibm/wlp/bin/tools/ws-javaagent.jar -Djava.awt.headless=true -jar /opt/ibm/wlp/bin/tools/ws-server.jar defaultServer 
    ```
    {: screen}

2.  **RSS** 列で Java ヒープ使用量を確認します。Java ヒープ使用量はキロバイト単位で表示されます。すべてのインスタンスを合わせてヒープ使用量が 2097152 キロバイト (2 GB) 未満であれば、WebSphere Application Server ライセンスを購入する必要はありません。
3.  WebSphere Application Server インスタンスの最大ヒープ使用量を調整します。詳しくは、[Setting generic JVM arguments in the WebSphere Application Server V8.5 Liberty profile](http://www-01.ibm.com/support/docview.wss?uid=swg21596474) を参照してください。

## WebSphere Application Server ライセンスの取得 
{: #license}

WebSphere Application Server ライセンスは、必要なプロセッサー・バリュー・ユニット (PVU) の数に基づきます。PVU は、IBM ミドルウェア・ソフトウェアのライセンス交付のための測定単位です。PVU の数は、ソフトウェアが使用できるプロセッサー (コア) の数を示します。
{:shortdesc}

{{site.data.keyword.Bluemix_notm}} 内のコンテナーのサイズごとに、特定の数の PVU 使用権が WebSphere Application Server ライセンスで使用可能でなければなりません。したがって、ライセンスを購入する前に、**ibmliberty** コンテナーを計画する必要があります。

WebSphere Application Server ライセンスを購入するには、[IBM サービス](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us)にお問い合わせください。WebSphere Application Server v8.5 以降のライセンスを既にお持ちの場合は、既存の使用権の中の未使用 PVU を、コンテナーのデプロイメントに使用できます。

ライセンス購入後にさらに PVU が必要であることが分かった場合は、[IBM サービス](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us)に連絡して量を増やすことができます。

## {{site.data.keyword.containershort_notm}} で使用する、実動ライセンス交付を受けた **ibmliberty** イメージの作成 
{: #prod_image}

WebSphere Application Server ライセンスを使用して、{{site.data.keyword.containershort_notm}} で使用できる、実動ライセンス交付を受けた **ibmliberty** イメージを作成できます。以下のタスクのいずれかを選択してください。
{:shortdesc}

-   [Docker Hub からのイメージを実動イメージにアップグレードする](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade)。
-   [実動ライセンス交付を受けた独自のイメージを作成する](https://github.com/WASdev/ci.docker/tree/master/ga/production-install)。

実動ライセンス交付を受けたイメージを作成した後、{{site.data.keyword.containershort_notm}} で使用するために、[イメージをプライベート・レジストリーにプッシュ](/docs/services/Registry/index.html)します。

## 提供イメージからのイメージの作成 
{: #creating_image}

**ibmliberty** イメージのいずれかを、独自のアプリ・コードを含む子イメージを作成するための親として使用できます。サンプル Dockerfile をカスタマイズし、ご使用のコンピューターで独自のイメージをビルドします。次に、イメージを組織のプライベート・イメージ・レジストリーに追加し、そのイメージを使用してコンテナーを作成できます。
{:shortdesc}

始める前に、以下のステップを検討してください。

-   独自のアプリ・コードを、WAR ファイル、EAR ファイル、または OSGi ファイル内にビルドします。
-   イメージをビルドするときに使用するディレクトリーにファイルをコピーします。


独自のアプリ・コードを含むイメージを **ibmliberty** イメージから作成するには、以下のようにします。

1. テキスト・エディターで、Dockerfile という名前のファイルを作成し、その中に以下の情報をコピーします。

    ```
    FROM registry.{{site.data.keyword.domainname}}/ibmliberty:<tag>
    COPY <app_name>.<file_extension> /config/dropins/
    
    ```
    {: screen}

    **注:** ディレクトリー /config は、/opt/ibm/wlp/usr/servers/defaultServer のショートカットです。
    
2. <tag> を、アプリに必要なフィーチャーが含まれている **ibmliberty** イメージのバージョンに置き換えます。

3. <app_name> をアプリ・ファイルの名前に置き換えます。

4. <file_extension> を、.war、.ear、または .eba のいずれかに置き換えます。

5. 独自のアプリに関する他の従属物があれば Dockerfile に追加します。

6. イメージをビルドしてプライベート・イメージ・レジストリーにプッシュします。詳しくは、[{{site.data.keyword.registrylong_notm}} 概説](/docs/services/Registry/index.html)を参照してください。

**注:** すべての **ibmliberty** イメージは、コンテナー内のディレクトリー /logs に Liberty ログ・ファイルを書き込むように構成されています。Liberty サーバーによって書き込まれる他のファイルはすべて、ディレクトリー /opt/ibm/wlp/output/defaultServer に作成されます。これらのファイルには、ショートカット /output を使用してアクセスできます。

## **ibmliberty** Dockerfile リファレンス 
{: #reference_dockerfile}

この Dockerfile は、Docker Hub 上のパブリック websphere-liberty イメージから {{site.data.keyword.Bluemix_notm}} の **ibmliberty:webProfile7** イメージがどのようにビルドされるかを示しています。この情報は、あくまでも参考用です。
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
