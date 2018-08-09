---

copyright:
  years: 2018
lastupdated: "2018-07-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# iccs-cluster-autoscaler イメージ概説 (アルファ)
{: #ibm-cluster-autoscaler}

iccs-cluster-autoscaler イメージには、{{site.data.keyword.containerlong}} クラスター内のワーカー・ノードを自動的にスケーリングするために必要なプリインストール・パッケージが入っています。iccs-cluster-autoscaler は、[Kubernetes Cluster-Autoscaler プロジェクト](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler)に基づいています。
{:shortdesc}

autoscaler のアルファ・バージョンは現在、単一ゾーン・アベイラビリティーのみをサポートしており、物理的なベアメタル・サーバー上のワーカー・ノードには使用できません。
{: tip}

## 機能
{: #how-it-works}

iccs-cluster-autoscaler イメージを使用して、{{site.data.keyword.containerlong}} での作業時に、指定された任意の自動スケーリング・グループで、Kubernetes クラスター内のワーカー・ノードをスケールアップまたはスケールダウンすることができます。autoscaler は、クラスターの `kube-system` 名前空間でデプロイメントとして実行され、Kubernetes クラスター autoscaler のプラグインです。
{: shortdesc}

iccs-cluster-autoscaler は、Kubernetes API サーバーの監視によってクラスター内のポッドをモニターし、クラスターのサイズを自動的にスケールアップまたはスケールダウンできます。

<dl>
  <dt>スケールアップ</dt>
    <dd>スケジュール不能のポッドがあるかを iccs-cluster-autoscaler は定期的にチェックします。ポッドを実行するノードを Kubernetes スケジューラーが見つけられないときに、ポッドはスケジュール不能になります。autoscaler は、スケジュール不能と示されたポッドを検出すると、クラスターにワーカー・ノードを追加します。</dd>
  <dt>スケールダウン</dt>
    <dd>デフォルトで iccs-cluster-autoscaler は、不要なノードについて 10 秒ごとにチェックします。不要の状態が 10 分を超えたノードは、削除されます。</br> ノードが不要と見なされるのは、以下の場合です。</br>
    <ul><li>当該ノードで実行されている全ポッドの CPU 要求とメモリー要求の合計が、それに割り振られた量の 50% 未満である。</li>
    <li>当該ノードで実行されている全ポッドは、他のノードに移動可能である。デフォルトで全ノードで実行されるポッド (マニフェスト実行のポッドや daemonset によって作成されるポッド) は、この検査から除外されます。</li>
    <li>スケールダウンを使用不可にするアノテーションがない。</li></ul></dd>
</dl>

では、これらにより、どうなるのでしょうか?

![クラスターの自動スケーリング](/images/autoscaler.png)

IBM Cloud で Kubernetes クラスターを作成する際に、ハードウェア設定を指定します。必ず正しい量のリソースを使用するようにするために、autoscaler によって、マシン・タイプに基づいてグループが作成されます。この図には、自動スケーリング・ポリシーの例が示されています。構成に、マシン・タイプと、ノードの最小数と最大数が指定されています。ある一連のノードを autoscaler が無視してスケーリングしないようにする場合は、それらを構成に含めないでください。図では、リソースの変化に対応してクラスターがスケールアップしています。b2c.16x64 ノードは、構成ファイルにポリシーが設定されていなかったため、スケーリングされませんでした。

## 使用上の制約事項
{: #usage}
このイメージは、Kubernetes API バージョン 1.7 以上とともに {{site.data.keyword.containershort}} で使用できます。

## 前提条件
{: #prerequisites}

iccs-cluster-autoscaler で作業する前に実行する必要がある前提条件がいくつかあります。

* autoscaler のプラグインを使用するには、標準クラスターが必要です。自動スケーリングのグループは、同じ [マシン・タイプ](/docs/containers/cs_cli_reference.html#cs_machine_types)のワーカー・ノードで構成されます。要件を満たしていることを検証するには、以下のコマンドを実行し、**Machine Type** が _free_ ではないことを確認します。**Version** が `version 1.7` 以降であることを確認してください。</br>
    ```
    ibmcloud ks workers <cluster-name>
    ```
    {: pre}
    出力例: </br>
    ```
    OK
    ID                                                   Public IP        Private IP     Machine Type   State    Status   Version
    stage-dal06-crffef8b930b214442802bdcaa551b5ec0-w1    169.54.180.171   10.146.32.7    u2c.2x4        normal   Ready    1.8.2_1504
    ```
    {: screen}
* 以下の CLI とプラグインをインストールする必要があります。要件のインストールと最初のクラスターの作成に役立つ情報については、[資料の該当チュートリアル](/docs/containers/cs_tutorials.html#cs_cluster_tutorial)を確認してください。
    <ul><li>{{site.data.keyword.Bluemix_notm}} CLI</li><li>Kubectl CLI</li><li>Docker CLI</li><li>{{site.data.keyword.containerlong_notm}} プラグイン</li><li>{{site.data.keyword.registrylong_notm}} プラグイン</li></ul>
* ユーザーに代わってワーカー・ノードをスケーリングするために、autoscaler は、ユーザーの ID とアクセス管理の詳細を認識している必要があります。資格情報は、有料クラスターで秘密として入手可能です。秘密が使用可能であることを確認するには、次のコマンドを実行します。このコマンドで、秘密が返されます。
```
		$kubectl get secrets -n kube-system | grep storage-secret-store
    ```
    {: pre}

## autoscaler のインストールと構成
{: #install-configure}

autoscaler をインストールするには、まず、Helm をクラスターにインストールして初期化する必要があります。autoscaler は、稼働後、自動的にリソースを節約し、クラスターをスケーリングします。

1. {{site.data.keyword.containershort_notm}} にログインし、自分のクラスターをターゲットにします。
```
    ibmcloud ks cluster-config <cluster_name>`.
    ```
    {: pre}

2. [Helm をインストールし、クラスター内で初期化します](https://docs.helm.sh/using_helm/#quickstart)。

3. 提供されたテンプレートを使用して、構成ファイルを作成します。

   ```
   cp incubator/helm/autoscaler/config.yaml.template config.yaml
   ```
   {: pre}

    スケーリング・グループが機能するには、最小と最大の範囲を指定しない場合でも、すべてのマシン・タイプを構成ファイルに指定する必要があります。
    {: tip}

  <table summary="表の 1 行目は、2 列を対象としています。その他の行は左から右に読んでください。1 列目にパラメーター、2 列目に対応する説明、3 列目にデフォルトが示されています。">
  <caption>構成可能なパラメーターとそのデフォルト値</caption>
    <thead>
      <th colspan=3><img src="../images/idea.png" alt="アイデア・アイコン"/>構成可能なパラメーターとそのデフォルト値</th>
    </thead>
    <tbody>
      <tr>
        <td><code>autoscalingGroups.name</code></td>
        <td> スケーリングするマシン・タイプ</td>
        <td> デフォルトはなく、ユーザーが指定します。マシン・タイプを判別するには、<code>ibmcloud ks workers <cluster-name></code> を実行します。</td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.minSize</code></td>
        <td> クラスターがスケーリングする際のワーカー・ノードの最小数。</td>
        <td> デフォルトはなく、ユーザーが指定します。</td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.maxSize</code></td>
        <td> クラスターがスケーリングする際のワーカー・ノードの最大数。</td>
        <td> デフォルトはなく、ユーザーが指定します。</td>
      </tr>
      <tr>
        <td><code>api_route</code></td>
        <td> クラスターが稼働している地域の {{site.data.keyword.containershort_notm}} API への経路。</td>
        <td> https://containers.bluemix.net/swagger-api/ </td>
      </tr>
      <tr>
        <td><code>resources.limits.cpu</code></td>
        <td> autoscaler ポッドに対する CPU 量の限度。</td>
        <td> 300m </td>
      </tr>
      <tr>
        <td><code>resources.limits.memory</code></td>
        <td> autoscaler ポッドに対するメモリー量の限度。</td>
        <td> 300Mi </td>
      </tr>
      <tr>
        <td><code>resources.requests.cpu</code></td>
        <td> autoscaler ポッドの最初の CPU 量。</td>
        <td> 100m </td>
      </tr>
      <tr>
        <td><code>resources.requests.memory</code></td>
        <td> autoscaler ポッドの最初のメモリー量。</td>
        <td> 100Mi </td>
      </tr>
      <tr>
        <td><code>scale_down_delay</code></td>
        <td> autoscaler が、スケールアップ要求の発行後に、スケールダウンするのを待つ必要がある時間。</td>
        <td> 10m </td>
      </tr>
      <tr>
        <td><code>scale_down_unneeded_time</code></td>
        <td> スケールダウンに適格となるために、ノードが不要となっている必要がある時間。</td>
        <td> 10m </td>
      </tr>
      <tr>
        <td><code>skipNodes.withLocalStorage</code></td>
        <td> true に設定されると、ローカル・ストレージを含むノードをスキップし、スケールダウンしません。</td>
        <td> true </td>
      </tr>
      <tr>
        <td><code>skipNodes.withSystemPods</code></td>
        <td> true に設定されると、kube-system ポッドを含むノードをスキップし、スケールダウンしません。</td>
        <td> true </td>
      </tr>
      <tr>
        <td><code>image.repository</code></td>
        <td> クラスター autoscaler の Docker イメージ。</td>
        <td> registry.bluemix.net/iccs-cluster-autoscaler </td>
      </tr>
      <tr>
        <td><code>image.tag</code></td>
        <td> プルするイメージのバージョン。</td>
        <td> 12 </td>
      </tr>
      <tr>
        <td><code>image.pullPolicy</code></td>
        <td> Docker イメージをいつプルするかを指定します。</td>
        <td> Always</td>
      </tr>
    </tbody>
  </table>
  
  iccs-cluster-autoscaler は、さまざまな構成パラメーターをサポートします。その動作について詳しくは、[Kubernetes Autoscaler FAQ](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md) を参照してください。

4. `kube-system` 名前空間でクラスターに Helm チャートをインストールします。

  ```
  helm install incubator/helm/autoscaler -f config.yaml --namespace kube-system
  ```
  {: pre}

    出力は以下のようになります。
  ```
  NAMESPACE: kube-system
  STATUS: DEPLOYED

  RESOURCES:
  ==> v1/Service
  NAME                CLUSTER-IP    EXTERNAL-IP  PORT(S)   AGE
  cluster-autoscaler  10.10.10.189  <none>       8085/TCP  5s

  ==> v1beta1/Deployment
  NAME                DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
  cluster-autoscaler  1        1        1           0          3s
  ```
  {: screen}

## インストールの検証

1. autoscaler ポッドを含むクラスターが実行中であることを確認します。

  ```
  kubectl get pods --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    出力例:
  ```
    cluster-autoscaler-4289984493-zldfk        1/1       Running   0          3m
  ```
  {: screen}

2. クラスター autoscaler が実行中であることを確認します。

  ```
  kubectl get service --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    出力例:
  ```
  cluster-autoscaler       10.10.10.204   <none>         8085/TCP                     4m
  ```
  {: screen}


## autoscaler のアンインストール

クラスター autoscaler のデプロイメントをアンインストールすることができます。

1. `ibmcloud ks cluster-config <cluster_name>` を実行し、{{site.data.keyword.Bluemix_notm}} にログインして自分のクラスターをターゲットにします。

2. デプロイメントの名前を取得します。

  ```
  helm ls | grep autoscaler
  <deployment_name>	1       	Mon Dec 18 13:45:09 2017	DEPLOYED	autoscaler-1.0.3	kube-system
  ```
  {: pre}

3. デプロイメントを削除します。

  ```
  helm delete <deployment_name>
  ```
  {: pre}
