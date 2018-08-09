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

# 開始使用 iccs-cluster-autoscaler 映像檔 (alpha)
{: #ibm-cluster-autoscaler}

iccs-cluster-autoscaler 映像檔包含在 {{site.data.keyword.containerlong}} 叢集內自動擴充工作者節點所需的預先安裝套件。iccs-cluster-autoscaler 是根據 [Kubernetes Cluster-Autoscaler 專案](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler)。
{:shortdesc}

autoscaler 的 alpha 版本目前只支援單一區域可用性，且不適用於實體裸機伺服器上的工作者節點。
{: tip}

## 如何運作
{: #how-it-works}

使用 iccs-cluster-autoscaler 映像檔，您可以在 {{site.data.keyword.containerlong}} 中工作時，在 Kubernetes 叢集內跨越任何指定的自動調整群組來擴增或縮減工作者節點。autoscaler 會執行為您叢集之 `kube-system` 名稱空間裡的部署，並且是 Kubernetes 叢集 autoscaler 上的外掛程式。
{: shortdesc}

iccs-cluster-autoscaler 會監看 Kubernetes API 伺服器來監視叢集裡的 Pod，並且可以自動地擴增或縮減叢集的大小。

<dl>
  <dt>擴增</dt>
    <dd>iccs-cluster-autoscaler 會定期檢查是否有任何無法排定的 Pod。當 Kubernetes 排程器找不到執行 Pod 的節點時，Pod 便無法排定。當 autoscaler 發現標示為無法排定的 Pod 時，它會將工作者節點新增至您的叢集。</dd>
  <dt>縮減</dt>
    <dd>依預設，iccs-cluster-autoscaler 每 10 秒會檢查是否有不需要的節點。如果不需要節點的時間超過 10 分鐘，便會將它刪除。</br> 節點在下列情況下會視為不需要：</br>
    <ul><li>在此節點上執行的所有 Pod，CPU 與記憶體要求的總和少於分配給它的數量 50%。</li>
    <li>在節點上執行的所有 Pod 可以移至其他節點。依預設在所有節點上執行的 Pod，例如 manifest-run Pod 或由常駐程式集所建立的 Pod，會排除在此測試之外。</li>
    <li>沒有已停用縮減的註釋。</li></ul></dd>
</dl>

但這一切表示什麼？

![自動調整叢集](/images/autoscaler.png)

當您在 IBM Cloud 中建立 Kubernetes 叢集時，會指定硬體喜好設定。autoscaler 根據機型建立群組，以確保您使用正確的資源量。在映像檔中，您可以看到自動調整原則範例。在配置中，會指定機型和節點數目的上下限。如果您想要 autoscaler 忽略一組節點不要調整，請不要將它們包含在您的配置中。在映像檔中，叢集會擴增以便處理資源變更。b2c.16x64 節點未調整，因為配置檔中沒有設定任何原則。

## 使用限制
{: #usage}
此映像檔可以用在 {{site.data.keyword.containershort}} 並搭配 Kubernetes API 1.7 版或更新版本。

## 必要條件
{: #prerequisites}

有一些必要條件您必須先完成，才能使用 iccs-cluster-autoscaler。

* 您必須有標準叢集才能使用 autoscaler 外掛程式。自動調整群組包含了相同[機型](/docs/containers/cs_cli_reference.html#cs_machine_types)的工作者節點。若要驗證您符合需求，請執行下列指令並確認**機型**不是_免費_。確認您的**版本**是 `1.7 版`或更新版本</br>
    ```
    ibmcloud ks workers <cluster-name>
    ```
    {: pre}
    輸出範例：</br>
    ```
    OK
    ID                                                   Public IP        Private IP     Machine Type   State    Status   Version
    stage-dal06-crffef8b930b214442802bdcaa551b5ec0-w1    169.54.180.171   10.146.32.7    u2c.2x4        normal   Ready    1.8.2_1504
    ```
    {: screen}
* 您必須安裝下列 CLI 和外掛程式。如需安裝需求及建立第一個叢集的協助，[請參閱文件中的本指導教學](/docs/containers/cs_tutorials.html#cs_cluster_tutorial)。
    <ul><li>{{site.data.keyword.Bluemix_notm}} CLI</li><li>Kubectl CLI</li><li>Docker CLI</li><li>{{site.data.keyword.containerlong_notm}} 外掛程式</li><li>{{site.data.keyword.registrylong_notm}} 外掛程式</li></ul>
* 為了代表您調整工作者節點，autoscaler 需要知道您的 Identity and Access Management 詳細資料。認證在付費叢集中以密碼的方式提供。若要驗證密碼是否可用，請執行下列指令。指令會傳回密碼。
    ```
		$kubectl get secrets -n kube-system | grep storage-secret-store
    ```
    {: pre}

## 安裝及配置 autoscaler
{: #install-configure}

若要安裝 autoscaler，您需要先將 Helm 安裝至叢集並起始設定。autoscaler 執行之後，它會自動節約資源並調整您的叢集。

1. 登入 {{site.data.keyword.containershort_notm}} 並將目標設為您的叢集。
    ```
    ibmcloud ks cluster-config <cluster_name>`.
    ```
    {: pre}

2. [在叢集中安裝 helm 並起始設定](https://docs.helm.sh/using_helm/#quickstart)。

3. 使用所提供的範本，建立配置檔。

   ```
   cp incubator/helm/autoscaler/config.yaml.template config.yaml
   ```
   {: pre}

    調整群組若要運作，您的所有機型都必須指定在配置檔中，即使是您未提供最小值和最大值範圍。
    {: tip}

  <table summary="表格中的第一列橫跨兩直欄。其餘列應該從左到右閱讀，第一欄為參數，第二欄為符合的說明，第三欄為預設值。">
  <caption>可配置的參數及其預設值</caption>
    <thead>
      <th colspan=3><img src="../images/idea.png" alt="構想圖示"/> 可配置的參數及其預設值</th>
    </thead>
    <tbody>
      <tr>
        <td><code>autoscalingGroups.name</code></td>
        <td> 您想要調整的機型。</td>
        <td> 無預設值，由使用者指定。若要尋找您的機型，請執行 <code>ibmcloud ks workers <cluster-name></code></td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.minSize</code></td>
        <td> 您的叢集應該調整的工作者節點數目下限。</td>
        <td> 無預設值，由使用者指定。</td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.maxSize</code></td>
        <td> 您的叢集應該調整的工作者節點數目上限。</td>
        <td> 無預設值，由使用者指定。</td>
      </tr>
      <tr>
        <td><code>api_route</code></td>
        <td> 您的叢集執行所在地區的 {{site.data.keyword.containershort_notm}} API 路徑。</td>
        <td> https://containers.bluemix.net/swagger-api/ </td>
      </tr>
      <tr>
        <td><code>resources.limits.cpu</code></td>
        <td> autoscaler Pod 受限制的 CPU 數量。</td>
        <td> 300m </td>
      </tr>
      <tr>
        <td><code>resources.limits.memory</code></td>
        <td> autoscaler Pod 受限制的記憶體數量。</td>
        <td> 300Mi </td>
      </tr>
      <tr>
        <td><code>resources.requests.cpu</code></td>
        <td> autoscaler Pod 一開始的 CPU 數量</td>
        <td> 100m </td>
      </tr>
      <tr>
        <td><code>resources.requests.memory</code></td>
        <td> autoscaler Pod 一開始的記憶體數量</td>
        <td> 100Mi </td>
      </tr>
      <tr>
        <td><code>scale_down_delay</code></td>
        <td> 調整程式發出擴增要求之後應該等待縮減的時間量。</td>
        <td> 10m </td>
      </tr>
      <tr>
        <td><code>scale_down_unneeded_time</code></td>
        <td> 節點在符合縮減資格之前應該處於不需要狀態的時間量。</td>
        <td> 10m </td>
      </tr>
      <tr>
        <td><code>skipNodes.withLocalStorage</code></td>
        <td> 設為 true 時，將會跳過包含本端儲存空間的節點，且不會縮減。</td>
        <td> true</td>
      </tr>
      <tr>
        <td><code>skipNodes.withSystemPods</code></td>
        <td> 設為 true 時，將會跳過包含 kube-system Pod 的節點，且不會縮減。</td>
        <td> true</td>
      </tr>
      <tr>
        <td><code>image.repository</code></td>
        <td> 叢集 autoscaler Docker 映像檔。</td>
        <td> registry.bluemix.net/iccs-cluster-autoscaler </td>
      </tr>
      <tr>
        <td><code>image.tag</code></td>
        <td> 您要取回的映像檔版本。</td>
        <td> 12 </td>
      </tr>
      <tr>
        <td><code>image.pullPolicy</code></td>
        <td> 指定取回 Docker 映像檔的時機。</td>
        <td> 一律。</td>
      </tr>
    </tbody>
  </table>
  
  iccs-cluster-autoscaler 支援各種配置參數。如需運作方式的相關詳細資訊，請參閱 [Kubernetes Autoscaler 常見問題](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md)。

4. 將 helm 圖表安裝到叢集中的 `kube-system` 名稱空間。

  ```
  helm install incubator/helm/autoscaler -f config.yaml --namespace kube-system
  ```
  {: pre}

    您的輸出應該類似如下：
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

## 驗證安裝

1. 驗證包含 autoscaler Pod 的叢集在執行中。

  ```
  kubectl get pods --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    輸出範例：
  ```
    cluster-autoscaler-4289984493-zldfk        1/1       Running   0          3m
  ```
  {: screen}

2. 驗證叢集 autoscaler 在執行中。

  ```
  kubectl get service --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    輸出範例：
  ```
  cluster-autoscaler       10.10.10.204   <none>         8085/TCP                     4m
  ```
  {: screen}


## 解除安裝 autoscaler

您可以解除安裝叢集 autoscaler 部署。

1. 登入 {{site.data.keyword.Bluemix_notm}} 並執行 `ibmcloud ks cluster-config <cluster_name>` 將目標設為您的叢集。

2. 取得部署的名稱。

  ```
  helm ls | grep autoscaler
  <deployment_name>	1       	Mon Dec 18 13:45:09 2017	DEPLOYED	autoscaler-1.0.3	kube-system
  ```
  {: pre}

3. 刪除部署。

  ```
  helm delete <deployment_name>
  ```
  {: pre}
