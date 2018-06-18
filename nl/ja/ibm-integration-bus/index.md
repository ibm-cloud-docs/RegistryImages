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


# *ibm-integration-bus* イメージ概説
{: #iib}

{{site.data.keyword.containerlong}} には、**ibm-integration-bus** イメージが提供されています。 IBM Integration Bus for Developers エディションが含まれています。
{:shortdesc}


## 機能
{: #how_it_works}

IBM Integration Bus for Developers には、独自の integration ソリューションの開発を開始することができるように、必要なものがすべて含まれています。
{:shortdesc}

Integration ソリューションを作成後に、**ibm-integration-bus** イメージを使用して {{site.data.keyword.Bluemix_notm}} に単一コンテナーをプロビジョンできます。 その後、Web UI を使用するかまたは端末から操作して、Integration ソリューションをこのコンテナーにデプロイできます。

**注**: **ibm-integration-bus** イメージは開発および単体テストのみに使用できます。 製品の検討、チュートリアルでの学習、そして IBM Integration Bus を使った場合の組織にとってのメリットの評価にも、このイメージを使用できます。


## 含まれている内容
{: #whats_included}

この IBM Integration Bus イメージには、IBM Integration Bus for Developers Version 10 \(Developer Edition ともいう\) のソフトウェア・パッケージが含まれており、これは開発と単体テストに使用可能な製品完全機能バージョンです。 このバージョンは無料でダウンロードでき、ライセンス条件の範囲内で自由に使用できます。
{:shortdesc}

IBM Integration Bus for Developers バージョン 10.0 は、Windows™ 64 ビット版オペレーティング・システムと Linux™ (x86-64) オペレーティング・システムで利用できます。 この製品の前提条件のすべてがダウンロード・パッケージに含まれています。

IBM Integration Bus for Developers に組み込まれている機能については、[IBM Integration Bus (IBM Knowledge Center 内) ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.msgbroker.helphome.doc/help_home_msgbroker.htm){: new_window} を参照してください。

Developer Edition には、[IBM Integration Bus for Developers のダウンロード・ページ ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.ibm.com/marketing/iwm/iwm/web/pick.do?source=swg-wmbfd){: new_window} からアクセスできます。


## 使用上の制約事項
{: #usage}

次の表は、{{site.data.keyword.Bluemix_notm}} での **ibm-integration-bus** イメージの無料使用に適用される制約事項を示しています。
{:shortdesc}

**注**: **ibm-integration-bus** イメージの価格設定は、{{site.data.keyword.Bluemix_notm}} で使用するコンテナーの価格設定とは無関係です。

|環境|無料使用上の制約事項|
|-----------|-----------------------|
|開発|**ibm-integration-bus** イメージを**無制限に**無料使用して、開発および単体テスト用に単一コンテナーを作成できます。|
|実動|Developer Edition は 1 秒に 1 つのトランザクションを処理するように制限されており、実稼働環境での使用はサポートされていません。|
{: caption="表 1. ibm-integration-bus イメージの無料使用に関する制約事項" caption-side="top"}


## ライセンス
{: #license}

ライセンスに関する情報。
{:shortdesc}

*   Dockerfile および関連するスクリプトは、[Apache License 2.0 ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](http://www.apache.org/licenses/LICENSE-2.0.html){: new_window} で使用許諾されます。
*   [IBM Integration Bus for Developers Version 10.0 ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](http://www.ibm.com/software/sla/sladb.nsf/displaylis/1BD32B141C9FDAE38525802C0055A956?OpenDocument){: new_window} のライセンス情報。

    **注**: このライセンスでは、他社への配布は許可されていません。 イメージ内の IBM Integration Bus のご利用条件では、開発と単体テストのための使用が制限されています。


## 概説
{: #get_started}

{{site.data.keyword.Bluemix_notm}} カタログにある **ibm-integration-bus** イメージを使用するか、実動ライセンス交付を受けた独自のイメージを選択して、単一コンテナーを作成します。
{:shortdesc}

**重要**: 開始する前に、**ibm-integration-bus** イメージの[使用上の制約事項](#usage)を確認してください。

**注**: アスタリスク (\*) のマークが付いたすべてのステップではネイティブ Docker コマンドを使用できます。

   以下のステップを行って、コンテナー内で IBM Integration Bus を実行します。

1.  [ibm-integration-bus イメージに基づくコンテナーを {{site.data.keyword.Bluemix_notm}} UI](#provision) からプロビジョンする
2.  [コンテナーおよび IBM Integration Bus インストール済み環境を検証する](#validate)
3.  [フローを作成してデプロイする](#createdeployflow)
4.  [コンテナー内の IBM Integration Bus ログをモニターする](#monitor)
5.  [コンテナー内で実行中の IBM Integration Bus リソースを管理する](#manage)


## **ibm-integration-bus** イメージに基づくコンテナーをプロビジョンする
{: #provision}

{{site.data.keyword.IBM_notm}} から提供されている **ibm-integration-bus** イメージに基づいて、{{site.data.keyword.Bluemix_notm}} に Docker コンテナーをプロビジョンします。
{:shortdesc}

  以下のステップを実行します。

1.  {{site.data.keyword.Bluemix_notm}} にログインします。
2.  カタログから、**「コンテナー」**を選択し、コンテナーをビルドする元になる **ibm-integration-bus** イメージを選択します。
3.  **「単一」**を選択して単一インスタンス・コンテナーを作成します。このコンテナーは開発およびテストの目的に使用できます。
4.  コンテナーの名前を入力します。例: `iib`。
5.  コンテナーのサイズを選択します。
6.  **「パブリック IP アドレス」**フィールドで、*「パブリック IP のバインド (Bind Public IP)」*を選択します。
7.  **「パブリック・ポート」**フィールドに、`4414, 7800` を指定します。
8.  **「詳細」**オプションを展開して、**「新規環境変数の追加 (Add new environment variables)」**をクリックします。
9.  以下の環境変数を追加します。

    <ul>
      <li>LICENSE に <i>accept</i> の値を指定します。
      <li>NODENAME に「MYNODE」の値を指定します。&lpar;ここで、「MYNODE」は選択したノード名です&rpar;</li>
      <li>LOG_LOCATIONS に <i>/var/log/syslog</i> の値を指定します。
    </ul>

    **注**: LICENSE プロパティーと NODENAME プロパティーの値を指定しないと、コンテナーは作成されますが、開始されません。

    LOG_LOCATIONS プロパティーは、{{site.data.keyword.Bluemix_notm}} UI 経由で公開するコンテナー内部のログを指定します。
10. **「作成」**をクリックして、コンテナーが実行を開始するまで待ちます。


## コンテナーおよび IBM Integration Bus インストール済み環境を検証する
{: #validate}

IBM Integration Bus を実行するコンテナーが {{site.data.keyword.Bluemix_notm}} にデプロイされた後、コンテナーの状況を確認し、IBM Integration Bus インストール済み環境を検証します。
{:shortdesc}

  以下のステップを実行して、コンテナー内で IBM Integration Bus のセットアップと構成を検証します。
  
1.  [ネイティブ Docker CLI を使用するための {{site.data.keyword.containershort_notm}} プラグイン \(`bx ic`\) のセットアップ](/docs/containers/container_cli_cfic_install.html)に説明されている手順で、{{site.data.keyword.containershort_notm}} CLI をセットアップします。

2.  端末から、{{site.data.keyword.Bluemix_notm}} にログインします。 以下のコマンドを実行します。

    <ol>
       <li>
          <pre class="codeblock">bx login</pre>
       </li>
       <li>
          <pre class="codeblock">bx ic init</pre>
       </li>
    </ol>

3.  コンテナーの状況を確認します。\*

    ```
    bx ic ps
    ```
    {: pre}

    **注**: このコマンドで、[{{site.data.keyword.containershort_notm}}](/docs/containers/container_cli_cfic_install.html#container_cli_login) にログインし、ネイティブ Docker コマンドを使用するように環境変数を設定する時に **bx ic** を **docker** に置き換えることができます。 アスタリスク (\*) のマークが付いたすべてのステップではネイティブ Docker コマンドを使用できます。

    ```
    $ bx ic ps
    CONTAINER ID        IMAGE                                                       COMMAND             CREATED             STATUS                 PORTS                                                        NAMES
    46d9ec1e-952        registry.ng.bluemix.net/ibm-integration-bus:latest   ""                  2 minutes ago       Running a minute ago   xxx.xxx.xxx.xxx:4414->4414/tcp, xxx.xxx.xxx.xxx:7800->7800/tcp   iib
    ```
    {: screen}

4.  Bash セッションをコンテナーにアタッチします。\*

    ```
    bx ic exec -it <container_name> /bin/bash
    ```
    {: pre}

    ここで、*container_name* はコンテナーの名前です。

    例えば、以下のようになります。

    ```
    $ bx ic exec -it iib /bin/bash
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

5.  [Command environment: Linux and UNIX™ systems ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](http://www.ibm.com/support/knowledgecenter/SSMKHH_9.0.0/com.ibm.etools.mft.doc/an26230_.htm){: new_window}に説明されているように、**mqsiprofile** コマンドを実行して環境を初期化します。

    環境を初期化するコマンドは、`/opt/ibm/iib-10.0.0.6/server/bin/` ディレクトリーにあります。

    例えば、以下のようになります。

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

6.  **mqsilist** コマンドを実行してノードの状況を表示します。`running` という状況がリストされるはずです。 例えば、以下のようになります。

    ```
    (IIB_10:)iibuser@instance-0098858e:/$ mqsilist
    BIP1325I: Integration node 'MYNODE' with administration URI 'http://instance-0098858e:4414' is running.
    BIP8071I: Successful command completion.
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

コンテナーは実行中です。サポートされている任意の方法を使用して、これに Integration ソリューションをデプロイできます。


## コンテナー内の IBM Integration Bus ログをモニターする
{: #monitor}

{{site.data.keyword.Bluemix_notm}} UI またはコマンド・ラインからログをモニターします。
{:shortdesc}

開始する前に、コマンド・ラインからログをモニターするように {{site.data.keyword.containershort_notm}} CLI を構成します。 詳しくは、[{{site.data.keyword.containershort_notm}} プラグイン (`bx ic`) のセットアップ](/docs/containers/container_cli_cfic_install.html)を参照してください。

**ibm-integration-bus** イメージが、IBM Integration Bus メッセージをコンテナー内部の `/var/log/syslog` に出力するよう構成されます。

次の IBM Integration Bus ログのモニター方法のいずれかを選択します。

*  コンテナーをプロビジョンした時に環境変数 LOG_LOCATIONS を構成した場合は、ログを {{site.data.keyword.Bluemix_notm}} UI からモニターできます。

    以下のステップを実行して、ログにアクセスしてください。

    <ol>
       <li>コンテナー・ダッシュボードから、モニターするコンテナーを選択します。</li>
       <li><b>「モニタリングおよびロギング (Monitoring and Logging)」</b>を選択します。</li>
       <li><b>「ロギング」</b>を選択します。 ダッシュボードにログ項目がリストされます。</li>
       <li>ログ・ビューをカスタマイズするには、「ロギング」ページで<b>「詳細ビュー」</b>を選択して Kibana を開きます。 詳しくは、<a href="https://console.bluemix.net/docs/containers/container_planning.html#container_planning_monlog" target="_blank">モニタリングとロギング</a>を参照して開始してください。
       </li>
    </ol>

*  端末から、Docker または {{site.data.keyword.containerlong_notm}} CLI を使用してメッセージをモニターします。 以下のステップを実行します。

    <ol>
       <li>
       端末を構成します。

        <ul>
        <li>Docker CLI を使用して直接ローカル Docker ホストを管理しながら、{{site.data.keyword.containerlong_notm}} CLI を使用して {{site.data.keyword.Bluemix_notm}} のコンテナーを管理するには、以下のステップを実行して環境をセットアップします。
           <ol>
             <li>
               <pre class="codeblock">bx login</pre>
             </li>
             <li>
               <pre class="codeblock">bx ic init</pre>
             </li>
           </ol>
        </li>
        <li>Docker CLI を使用して {{site.data.keyword.Bluemix_notm}} のコンテナーを管理するには、以下のステップを実行します。
          <ol>
            <li>
              <pre class="codeblock">bx login</pre>
            </li>
            <li>
              <pre class="codeblock">bx ic init</pre>
               環境変数 &lt;DOCKER_HOST&gt;、&lt;DOCKER_CERT_PATH&gt;、および &lt;DOCKER_TLS_VERIFY&gt; に指定されている値をコピーします。
            </li>
            <li>
            {{site.data.keyword.containerlong_notm}} に接続するため、以下の変数を設定してローカル Docker 環境を指定変更します。
               <ul>
                 <li><pre class="codeblock">export DOCKER_HOST=&lt;your_host_value&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;your_cert_path_value&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
               </ul>
                <b>注</b>: このオプションでは一部の Docker コマンドのみサポートされています。
            </li>
          </ol>
        </li>
      </ul>
      </li>
      <li>
      ログにアクセスします。 次のオプションのいずれかを選択してください。

      <ul>
         <li>端末が {{site.data.keyword.containerlong_notm}} CLI コマンドを実行するように構成されている場合は、次のコマンドを実行します。

            <pre class="codeblock">bx ic exec &lt;container_id&gt; tail -f /var/log/syslog</pre>

            ここで、*container_id* はコンテナーの名前です。

            例えば、以下のようになります。

            <pre class="screen">bx ic exec iib tail -f /var/log/syslog</pre>

          </li>
          <li>
           端末が Docker CLI コマンドを実行するように構成されている場合は、次のコマンドを実行します。

            <pre class="codeblock">docker exec &lt;container_id&gt; tail -f /var/log/syslog</pre>

            ここで、*container_id* はコンテナーの名前です。

            例えば、以下のようになります。

            <pre class="screen">docker exec iib tail -f /var/log/syslog</pre>

          </li>
        </ul>
       </li>
    </ol>


## コンテナーで実行中の IBM Integration Bus リソースを管理する
{: #manage}

IBM Integration Bus Web UI を起動するか、または端末からコマンドを実行して、コンテナー内で実行中の IBM Integration Bus リソースを管理します。
{:shortdesc}

  以下のいずれかのオプションを選択して、コンテナー内の IBM Integration Bus リソースを管理します。

*  [IBM Integration Bus Web UI を起動して](#launchwebUI) IBM Integration Bus をグラフィックで管理します。
*  端末から、以下のように Docker または {{site.data.keyword.containershort_notm}} CLI を使用して、IBM Integration Bus リソースを管理します。
    *   [{{site.data.keyword.containerlong_notm}} CLI](#admin_commands_containers_cli) を使用してコンテナー内で管理コマンドを実行する
    *   [Docker CLI を使用してコンテナー内で管理コマンドを実行する](#admin_commands_docker_cli)


## IBM Integration Bus Web UI を起動する
{: #launchwebUI}

この Web UI を起動して、コンテナー内で実行中の IBM Integration Bus リソースをブラウザーから管理します。
{:shortdesc}

以下のステップを実行して、[{{site.data.keyword.Bluemix_notm}} の ibm-integration-bus イメージ概説](#get_started)で公開したホストにブラウザーを接続します。 IBM Integration Bus Web ユーザー・インターフェースが表示されます。

1.  {{site.data.keyword.Bluemix_notm}} UI からコンテナーを選択し、コンテナーの状況が `Running` (稼働中) であるかどうかを確認します。
2.  コンテナーの詳細を確認して、 パブリック IP を見つけます。
3.  Web ブラウザーを開いて、以下の URL にアクセスします。

    ```
    http://<DockerContainerPublicIP>:4414
    ```
    {: pre}

    ここで、
*DockerContainerPublicIP* は前のステップで確認したコンテナーのパブリック IP です。

4.  ブラウザーで Web ユーザー・インターフェースが開き、IBM Integration Bus を管理する準備ができました。


## {{site.data.keyword.containerlong_notm}} CLI を使用してコンテナー内で管理コマンドを実行する
{: #admin_commands_containers_cli}

{{site.data.keyword.containershort_notm}} CLI を使用して、コンテナー内で IBM Integration Bus 管理コマンドを直接実行します。
{:shortdesc}

開始する前に、コマンド・ラインから管理コマンドを実行するように {{site.data.keyword.containershort_notm}} CLI を構成します。 詳しくは、[{{site.data.keyword.containershort_notm}} プラグイン (`bx ic`) のセットアップ](/docs/containers/container_cli_cfic_install.html)を参照してください。

  以下のステップを実行し、**bx ic** コマンドを実行して IBM Integration Bus を管理するように端末をセットアップします。

1.  端末から、{{site.data.keyword.Bluemix_notm}} にログインします。 以下のコマンドを実行します。

      <ol>
        <li>
           <pre class="codeblock">bx login</pre>
        </li>
        <li>
           <pre class="codeblock">bx ic init</pre>
        </li>
       </ol>
           
2.  Bash セッションをコンテナーにアタッチして対話式セッションをセットアップします。\*

    ```
    bx ic exec -it <container_name> /bin/bash
    ```
    {: pre}

    ここで、*container_name* はコンテナーの名前です。

    例えば、以下のようになります。

    ```
    bx ic exec -it iib /bin/bash
    ```
    {: screen}

    mqsiprofile をソースに指定し、このポイント (コンテナー内部のシェル) からコマンドを実行します。

3.  または、**bx ic exec** コマンドを使用して、どの IBM Integration Bus コマンドも実行できる非対話式 Bash セッションを実行します。\*

    例えば、以下のようになります。

    ```
    bx ic exec iib <container_name> /bin/bash -c mqsilist
    ```
    {: screen}


## Docker CLI を使用してコンテナー内で管理コマンドを実行する
{: #admin_commands_docker_cli}

Docker CLI を使用して、コンテナー内で IBM Integration Bus 管理コマンドを直接実行します。
{:shortdesc}

開始する前に、コマンド・ラインから管理コマンドを実行するように {{site.data.keyword.containershort_notm}} CLI を構成します。 詳しくは、[{{site.data.keyword.containershort_notm}} プラグイン (`bx ic`) のセットアップ](/docs/containers/container_cli_cfic_install.html)を参照してください。

  以下のステップを実行し、Docker コマンドを実行して IBM Integration Bus を管理するために端末をセットアップします。

1.  端末から、{{site.data.keyword.Bluemix_notm}} にログインします。 以下のコマンドを実行します。

    <ol>
     <li>
       <pre class="codeblock">bx login</pre>
     </li>
     <li>
       <pre class="codeblock">bx ic init</pre>

      <p>環境変数 &lt;DOCKER_HOST&gt;、&lt;DOCKER_CERT_PATH&gt;、および &lt;DOCKER_TLS_VERIFY&gt; に指定されている値をコピーします。</p>
      </li>
      <li>  {{site.data.keyword.containershort_notm}} に接続するため、以下の変数を設定してローカル Docker 環境を指定変更します。
        <ul>
           <li><pre class="codeblock">export DOCKER_HOST=&lt;your_host_value&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;your_cert_path_value&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
        </ul>

          <p><b>注</b>: このオプションでは一部の Docker コマンドのみサポートされています。</p>
     </li>
    </ol>

2.  Bash セッションをコンテナーにアタッチして対話式セッションをセットアップします。 次のコマンドを実行します。

    ```
    docker exec -it <container_name> /bin/bash
    ```
    {: pre}

    ここで、*container_name* はコンテナーの名前です。

    例えば、以下のようになります。

    ```
    docker exec -it iib /bin/bash
    ```
    {: screen}

    mqsiprofile をソースに指定し、このポイント (コンテナー内部のシェル) からコマンドを実行します。

3.  mqsiprofile をソースに指定し、このポイント (コンテナー内部のシェル) からコマンドを実行します。

    または、docker exec を使用して、どの IBM Integration Bus コマンドも実行できる非対話式 Bash セッションを実行します。 
    
    例えば、以下のようになります。

    ```
    docker exec iib <container_name> /bin/bash -c mqsilist
    ```
    {: screen}


## フローを作成してデプロイする
{: #createdeployflow}

IBM Integration Bus Knowledge Center 内の指示に従ってメッセージ・フローを作成し、デプロイします。
{:shortdesc}

メッセージ・フローの作成方法について詳しくは、[How do I create and manage message flows?![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40150_.htm){: new_window} を参照してください。

メッセージ・フローのデプロイ方法について詳しくは、[How do I deploy and configure message flows? ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40180_.htm){: new_window}を参照してください。
