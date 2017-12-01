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


# **ibm-websphere-extreme-scale** イメージ概説 
{: #ibm-websphere-extreme-scale}

{{site.data.keyword.containerlong}} には、**ibm-websphere-extreme-scale** イメージが提供されています。

## 機能 
{: #how_it_works}

IBM WebSphere&reg; eXtreme Scale イメージは、Liberty ランタイム環境内で実行される分散型のメモリー内キャッシュ・フィーチャーです。そこには最新の業界標準および仕様に基づく Liberty キャッシング・サーバー、キャッシング操作ツール、管理コンソール、すぐに使用可能な REST 管理サービスが用意されています。このイメージを構築する上で使用される中核となるテクノロジーは、IBM WebSphere eXtreme Scale です。このイメージ内にロードされるキャッシング・テクノロジーは、JCache \(JSR107\) 仕様とコンプライアンスを完全にサポートしています。

`ibm-websphere-extreme-scale` イメージを使用すると、以下のような主要な機能を活用できます。

-   eXtreme Scale テクノロジーに基づく、デプロイが簡単で構成が容易な分散キャッシング・ソリューション
-   単純で使いやすい管理コンソールと REST API
-   eXtreme Scale キャッシングのすべてのユース・ケースをサポートするための完全なカスタマイズ
-   キャッシング環境内で数分で統合できる、構成が容易なイメージ
-   JSR107 キャッシング仕様をサポートする JCache 準拠の機能


## 含まれている内容 
{: #whats_included}

すべての **ibm-websphere-extreme-scale** イメージに、以下のソフトウェア・パッケージが用意されています。

-   Ubuntu 16.04 ベース・イメージ、64 ビット
-   IBM WebSphere eXtreme Scale
-   IBM Java Runtime Environment 8.0

イメージにインストールされている固有のフィーチャーは、選択するタグによって異なります。次の表に、**ibm-websphere-extreme-scale** の各イメージに含まれているコード・レベルを示します。これらのフィーチャーについて詳しくは、[eXtreme Scale の資料](http://www.ibm.com/support/knowledgecenter/SSTVLU)を参照してください。

|タグ|説明|
|---|-----------|
|**ibm-websphere-extreme-scale:latest**|このタグを使用して、最新の IBM WebSphere eXtreme Scale イメージを指定します。|
|**ibm-websphere-extreme-scale:8.6.1.x**|このタグを使用して、WebSphere eXtreme Scale の特定のリリース・レベルを指定します。|
{: caption="表 1. IBM WebSphere eXtreme Scale イメージ" caption-side="top"}

## 使用上の制約事項 
{: #usage}

次の表は、{{site.data.keyword.Bluemix_notm}} での **ibm-websphere-extreme-scale** イメージの無料使用に適用される制約事項を示しています。

|環境|使用上の制約事項|
|-----------|------------------|
|開発|`ibm-websphere-extreme-scale` イメージは、開発時には無制限かつ無料で使用できます。|
|実動|**ibm-websphere-extreme-scale** イメージの実動使用は、ライセンス交付を受けた IBM WebSphere eXtreme Scale 製品ユーザーに限定されます。実動使用について詳しくは、[View Pricing and Buy](https://www-112.ibm.com/software/howtobuy/buyingtools/paexpress/Express?P0=E1&part_number=D56YTLL,D56YVLL,D572PLL,D572MLL,D0594LL,D059GLL&catalogLocale=en_US&Locale=en_US&country=USA&PT=jsp&CC=USA&VP=&TACTICS=&S_TACT=&S_CMP=&brand=SB05) を参照してください。|
{: caption="表 2. {{site.data.keyword.Bluemix_notm}} での ibm-websphere-extreme-scale イメージの使用上の制約事項" caption-side="top"}

**注:** **ibm-websphere-extreme-scale** イメージの価格設定は、{{site.data.keyword.Bluemix_notm}} で使用するコンテナーの価格設定とは無関係です。


[License information documents](http://www-03.ibm.com/software/sla/sladb.nsf/displaylis/A44EE6829150B2998525801700516718?OpenDocument) の『License』セクションで、IBM 認定イメージのご利用条件を確認してください。


## 概説 
{: #get_started}

**ibm-websphere-extreme-scale** イメージは、コンソールとコマンド・ラインのいずれを使用してもデプロイできます。


この手順には、イメージをコンソールまたはコマンド・ラインのいずれかを使用してデプロイする場合の、以下のようなオプションが含まれています。

-   {{site.data.keyword.Bluemix_notm}} [ コンソール](#bmconsole)の**「カタログ」**からイメージを選択する。
-   CF [コマンド・ライン](#bmcommand)で cf ic コマンドを使用してイメージを開始する。

**重要:** 始めに、**ibm-websphere-extreme-scale** イメージに関して前のセクションで説明されている、使用上の制約事項を確認してください。


### {{site.data.keyword.Bluemix_notm}} コンソールからデプロイします。
{: #bmconsole}

   1.  カタログから**「コンテナー」**を選択し、**ibm-websphere-extreme-scale** イメージを選択してコンテナーのビルドを開始します。
   2.  **「タグ/バージョン」**ドロップダウン・メニューから、使用する **ibm-websphere-extreme-scale** イメージのバージョンを選択します。
   3.  単一コンテナーを作成します。

        **注:** 複数のコンテナーを作成する場合、**「スケーラブル」**オプションを使用して複数の eXtreme Scale キャッシュ・メンバー・グループを作成することはしないでください。代わりに、**「単一」**オプションを選択して複数のコンテナーを作成し、eXtreme Scale ダッシュボード UI を使用してキャッシュ・メンバーを結合してください。

        詳しくは、[{{site.data.keyword.Bluemix_notm}} ダッシュボードを使用した単一コンテナーの作成](/docs/containers/container_single_ui.html#gui)を参照してください。

        1.  「コンテナーの作成」パネルから**コンテナー名** (例えば、`wxs1`) を指定します。コンテナー名にスペースやその他の印刷できない文字を使用することはできません。
        2.  **「サイズ」**には、ibm-eXtreme-scale イメージの開始に使用するコンテナーのサイズを指定します。
        3.  **「パブリック IP アドレス」**には、「要求およびバインド・パブリック IP」を指定します。
        4.  「詳細オプション」パネルを開きます。後でローリング・アップグレードのインストール時に eXtreme Scale 構成データが永続するようにボリュームを作成します。例えば、次のようにします。
            1. 「詳細オプション」で**「ボリュームの作成」**をクリックし、**「ボリューム名」**を指定します。例えば、「vol-wxs1」と入力します。
            2. **「新規環境変数の追加」**をクリックします。**ibm-websphere-extreme-scale** イメージで必要な環境変数は、コンテナー内に保管されて、許可ユーザーがアクセスします。
<table>
            <caption>表 3. ibm-websphere-extreme-scale イメージの環境変数</caption>
               <tr>
                 <th>変数名</th>
                 <th>指定される値</th>
               </tr>
               <tr>
                 <td>SECRET_KEY</td>
                 <td>秘密鍵の値 (例えば、<var class="keyword varname">s3cretKey!</var>)。これは、キャッシュ・メンバー・グループに属する複数のメンバー間で内部通信を送信するときに、eXtreme Scale サーバーで使用される秘密鍵です。詳しくは、[キャッシュ・メンバー・グループのセクション](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txscreategridtut1.html)を参照してください。</td>
               </tr>
               <tr>
                 <td>XSADMIN_PWD</td>
                 <td>eXtremeScale ダッシュボード UI にログオンする xsadmin ユーザーのパスワード (例えば、<var class="keyword varname">xsadmin4Me!</var>)。</td>
               </tr>
               </table>
  
              **注:** パスワードと秘密鍵は、eXtreme Scale コンテナーの開始後に変更できます。秘密鍵とパスワードの両方に、以下のパスワード規則があります。以下の規則に基づいて、これら 2 つを入力するようにしてください。

              パスワードと秘密鍵は最低 10 文字で、この中には、少なくとも 1 つの数字 [`0-9`]、1 つの大文字 [`A-Z`]、1 つの特殊文字 [`~!@#$%^&*()_-+=:;'[]{}|<>,.?/`] が含まれていなければなりません。

              パスワードの変更方法について詳しくは、[管理](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txsadministerxsld.html)の資料を参照してください。

              ポートは、デフォルトで**「パブリック・ポート」**フィールドに追加されます。

   4.  {{site.data.keyword.Bluemix_notm}} ダッシュボードで**「作成」**をクリックしてコンテナーを作成します。
    
   5.  コンテナーと eXtreme Scale サーバーが完全に開始した後で、eXtreme Scale ダッシュボード UI にログオンし、デプロイメントを検証します。{{site.data.keyword.Bluemix_notm}} コンソールでコンテナーをクリックしてパブリック IP を確認します。WebSphere eXtreme Scale コンテナーには、次の URL からアクセスできます。

    ```
    https://<Public_IP>:9443
    ```
    {: pre}

   **注:** サーバーが完全に開始するまで 5 分から 10 分かかる場合があります。


### コマンド・ラインからイメージをデプロイします。
{: #bmcommand}
 1.  Docker ボリュームを作成して、IBM WebSphere eXtreme Scale の永続データを保管します。例えば、次のコマンドを入力します。

  ```
  cf ic volume create <vol-wxs1>
  ```
  {: pre}

 2.  次のコマンドを入力して、{{site.data.keyword.Bluemix_notm}} で IBM WebSphere eXtreme Scale コンテナーを開始します。

    **注:** 前述のパスワード規則に基づいて、yourSecretKey と yourXsadminPassword を独自のパスワードに置き換えます。

   ```
   cf ic run -itdP --volume <vol-wxs1:/vol> --name <wxs1> -m 1024 -e SECRET_KEY=<yourSecretKey> -e XSADMIN_PWD=<yourXsadminPassword> registry.ng.bluemix.net/ibm-websphere-extreme-scale:latest
   ```
   {: pre}
        
     **注:** コンテナー名には英数字のみを使用し、文字で始める必要があります。または、コンテナー名に他の非英数字を含める必要がある場合は、<code>-e XSLD_CONTAINER_ALIAS=<wxs1></code> を設定することもできます。

 3.  最初の IBM WebSphere eXtreme Scale コンテナーに使用するパブリック IP を要求します。次のコマンドを入力して、IP アドレスを書き留めます。

   ```
   cf ic ip request
   ```
   {: pre}

 4.  次のコマンドを入力して、要求したパブリック IP アドレスに WebSphere eXtreme Scale コンテナーをバインドします。

   ```
   cf ic ip bind <REQUESTED_IP> wxs1
   ```
   {: pre}

 5.  次のコマンドを入力して、`wxs1` コンテナーの状況が「Running」であることを確認します。

   ```
   cf ic ps -a
   ```
   {: pre}

 6.  次のコマンドを入力して、`wxs1` コンテナーに接続し、Nanny ログをモニターします。

   ```
   cf ic exec -it <wxs1> tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
        ```
   {: pre}

    **注:** サーバーが開始するまで 5 分から 10 分かかる場合があります。

 7.  コンテナーと eXtreme Scale サーバーが完全に開始した後で、eXtreme Scale ダッシュボード UI にログオンし、デプロイメントを検証します。

   ```
   https://<REQUESTED_IP>:9443
   ```
   {: pre}


## キャッシング容量の追加 
{: #caching}

1.  前と同じ手順に従って、コンソールまたはコマンド・ラインから、別のイメージをデプロイします。
2.  コンテナーと eXtreme Scale サーバーが完全に開始した後で、このメンバーの eXtreme Scale ダッシュボード UI にログオンします。

    ```
    https://<REQUESTED_IP>:9443
    ```
    {: pre}

3.  「データ・キャッシュ・メンバー」ページを開き、このメンバーの**「アクション」**列の下で**「結合」**を選択します。
4.  最初のメンバーが要求したパブリック IP、最初のメンバーの秘密鍵、およびキャッシュ・グループ内でのこのメンバーの固有名を入力します。
5.  **「結合」**をクリックして、タスクが完了するのを待ちます。タスクが完了すると、「データ・キャッシュ・メンバー」ページにすべてのメンバーが表示されます。

## ローリング・アップグレードの実行 
{: #rolling_upgrade}

{{site.data.keyword.containershort_notm}} で `ibm-websphere-extreme-scale` を実行しているコンテナーのローリング・アップグレードを実行するには、以下の手順を実行する必要があります。

**注:** eXtreme Scale キャッシュ・メンバー・グループのメンバーが 1 人だけである場合は、この {{site.data.keyword.IBM_notm}} コンテナーを削除し、最初から作成しなおします。ただし、eXtreme Scale キャッシュ・メンバー・グループに複数のメンバーが含まれていて、複数のカタログ・サーバーがデプロイされている場合に、キャッシュされたデータを {{site.data.keyword.containershort_notm}} で保持するときは、以下のローリング・アップグレードの手順を実行して、キャッシュされたデータが失われないようにしてください。最初にコンテナー・サーバーだけを持っているキャッシュ・メンバーをアップグレードしてから、カタログ・サーバーも持っているキャッシュ・メンバーをアップグレードしてください。

1.  wxsmigrate という新規コンテナーを開始し、それをすべての既存の eXtreme Scale コンテナーのアップグレードに使用します。詳しくは、このページの『[概説](#get_started)』セクションを参照してください。eXtreme Scale の最新の更新が含まれる .zip ファイルは、新規コンテナー wxsmigrate 上の /tmp フォルダーの下にデプロイされます。
2.  更新した eXtreme Scale .zip ファイルを、wxsmigrate コンテナーから {{site.data.keyword.containershort_notm}} CLI プラグインが実行されているローカル・システムにコピーします。

    ```
    cf ic cp wxsmigrate:/tmp/xsld-bin-update.zip
    ```
    {: pre}
    
    ```
    cf ic cp wxsmigrate:/tmp/xsld-derby-update.zip
    ```
    {: pre}

3.  次に、キャッシュ・メンバー・グループ内のキャッシュ・メンバーごとに、以下の手順を一度に 1 つのコンテナーで実行します。

    1. ローカル・システムに置かれている更新した eXtreme Scale .zip ファイルを、アップグレードが必要な eXtreme Scale コンテナーにコピーします。
    
      ```
      cf ic cp  xsld-bin-update.zip  <container_name>:/tmp/xsld-bin-update.zip
      ```
      {: pre}
  
      ```
      cf ic cp  xsld-derby-update.zip  <container_name>:/tmp/xsld-derby-update.zip
      ```
      {: pre}
     
    2. アップグレードするコンテナーに接続します。 
    
      ```
      cf ic exec -it <container_name> bash
      ```
      {: pre}

    3. アップグレードの準備ができているコンテナー上の XSLD サーバーを停止します。
      
      ```
      /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
      ```
      {: pre}
      
      サーバーが停止するまで待ちます。
      
    4. コンテナー内にある最新の eXtreme Scale 更新コードを unzip します。
      
      ```
      unzip  -o  /tmp/xsld-bin-update.zip   -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
      
      ```
      unzip  -o  /tmp/xsld-derby-update.zip -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
    
    5. XSLD サーバーを再度開始します。
    
      ```
      /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
      ```
      {: pre}
      
    6. Nanny ログをモニターして、サーバーが開始したことを確認します。
    
      ```
      tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
      ```
      {: pre}
      
4. すべての IBM コンテナーでアップグレードが完了したら、wxsmigrate コンテナーを削除します。

 ```
 cf ic rm -f <wxsmigrate>
 ```
 {: pre}

## トラブルシューティング 
{: #troubleshoot}

###ホスト・マッピングが失われた場合のキャッシュ・メンバー接続のトラブルシューティング 
{: #troubleshoot_cache}

コンテナー間のホスト・マッピングが失われたためにキャッシュ・グループのメンバーの接続が失われている場合は、以下の手順を実行します。

1.  各コンテナーに接続します。

    ```
    cf ic exec -it <container_name> bash
    ```
    {: pre}

2.  各メンバーのプライベート IP、ホスト名、およびホスト別名を収集します。

    ホスト別名は、XSLD_CONTAINER_ALIAS 環境変数の値です。

    ```
    echo ${<XSLD_CONTAINER_ALIAS>}
    ```
    {: pre}

    プライベート IP は、コンテナー内で hostname -I コマンドを実行して返された IP アドレスです。

    ```
    hostname -I
    ```
    {: pre}

    ホスト名は、コンテナー内で hostname コマンドを実行して返されたホスト名です。


3.  その他すべてのメンバー別名、IP、およびホスト名を渡している各メンバーについて recoverXSLD.sh スクリプトを実行します。

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/recoverXSLD.sh <member2_alias> <member2_private_ip> <member2_hostname> ... <memberN_alias> <memberN_private_ip> <memberN_hostname>
    ```
    {: pre}

4.  キャッシュ・グループ内の各コンテナーについて stopXSLD.sh を実行して、実行中のサーバーを停止します。stopXSLD.sh を実行します。

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
    ```
    {: pre}

5.  カタログ・サーバーを停止しようとしているときに stopXSLD.sh が進行しない場合は、以下の手順に従います。
    1.  カタログ・サーバー・プロセスをリストします。

        ```
        ps -ef | grep cs-default
        ```
        {: pre}

    2.  プロセス ID を使用してカタログ・サーバー・プロセスを終了します。

        ```
        kill -9 <catalog_server_process_id>
        ```
        {: pre}

    3.  stopXSLD.sh を再度実行します。

        ```
        /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
        ```
        {: pre}

6.  すべてのコンテナー上でほぼ同時に XSLD を開始します。startXSLD.sh を実行します。

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
    ```
    {: pre}


### 停止した Derby レプリケーション・タスクのトラブルシューティング 
{: #troubleshoot_derby}

Derby レプリケーション・タスクが 45% より先に進まない状態が 10 分以上続く場合は、ネットワークの問題が原因である可能性があります。以下の手順を実行して、この問題をトラブルシューティングしてください。

1.  cURL などの REST POSTing ユーティリティーを使用して、Derby レプリケーション・タスクを取り消します。

    ```
    curl -X PUT --header "Content-Type: application/json" --header "Accept: application/json" "https://<PUBLIC_IP>:9445/wxsadmin/v1/task/cancel/<DERBY_REPLICATION_TASK_ID>/force"
    ```
    {: pre}

    **注:** <PUBLIC_IP> は Derby レプリケーション・タスクを開始するためにコマンドで使用されたものであり、<DERBY_REPLICATION_TASK_ID> はこのコマンドから出力されたタスク ID です。

2.  Derby レプリケーション・タスクを再度実行します。

## REST API へのアクセス 
{: #api}

eXtreme Scale が {{site.data.keyword.containershort_notm}} にデプロイされる場合は、以下のステップを実行して REST API にアクセスしてください。

1.  REST API への接続に使用するコンテナーのホスト名を取得します。
    1.  コンテナーに接続します。

        ```
        cf ic exec -it <container_name> bash
        ```
        {: pre}

    2.  ホスト名をリストします。

        ```
        hostname
        ```
        {: pre}

2.  同じコンテナーのパブリック IP を取得します。

    コンテナーにパブリック IP がない場合は『概説』セクションのステップに従って、{{site.data.keyword.Bluemix_notm}} [コンソール](#bmconsole)または[コマンド・ライン](#bmcommand)を使用してコンテナーにパブリック IP を指定します。

3.  ご使用のローカル・クライアント・コンピューターの hosts ファイルに、パブリック IP とコンテナーのホスト名を一緒にマッピングするエントリーを作成します。このエントリーは、以下のようになります。

    ```
    169.44.8.87 instance-05106102
    ```
    {: pre}

    hosts ファイルの通常のフルパス名は以下のようになります。

    -   UNIX の場合: /etc/hosts
    -   Windows の場合: C:\\Windows\\System32\\drivers\\etc\\hosts
4.  ブラウザーで Swagger REST API にアクセスします。
    -   CRUD オペレーションの場合:

        ```
        https://<host_name>:9444/ibm/api/explorer
        ```
        {: pre}

    -   管理オペレーションの場合:

        ```
        https://<host_name>:9445/ibm/api/explorer
        ```
        {: pre}


サンプルと最新の更新情報については、[GitHub の WebSphere eXtreme Scale](https://github.com/ibmWebsphereExtremeScale) にアクセスしてください。また、eXtreme Scale のデプロイに役立つ以下のサポート資料とビデオも参照してください。

-   [IBM WebSphere eXtreme Scale の資料](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/cxsgetstartxsld.html)
-   [XS developerWorks フォーラム](https://www.ibm.com/developerworks/community/forums/html/forum?id=11111111-0000-0000-0000-000000000778&ps=25)
-   [Getting started with WebSphere eXtreme Scale V8.6.x](https://www.youtube.com/watch?v=xw7Ck3c40rA)
-   [Adding cache with WebSphere eXtreme Scale - Liberty Deployment](https://www.youtube.com/watch?v=I1g88bnstn8)
-   [Setting up a WAS client to work with an WebSphere eXtreme Scale Liberty Deployment DynaCache grid](https://www.youtube.com/watch?v=RvhcknNX-4Q)
-   [Setting up a WAS client to work with an WebSphere eXtreme Scale Liberty Deployment Session Cache grid](https://www.youtube.com/watch?v=GKTy9I66Klc)

