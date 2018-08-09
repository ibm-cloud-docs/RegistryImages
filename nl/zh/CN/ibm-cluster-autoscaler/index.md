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

# 开始使用 iccs-cluster-autoscaler 映像 (alpha)
{: #ibm-cluster-autoscaler}

iccs-cluster-autoscaler 映像包含在 {{site.data.keyword.containerlong}} 集群中自动扩展工作程序节点所需的预安装包。
iccs-cluster-autoscaler 基于 [Kubernetes Cluster-Autoscaler 项目](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler)。
{:shortdesc}

Autoscaler 的 alpha 版本目前仅支持单专区可用性，不适用于物理裸机服务器上的工作程序节点。
{: tip}

## 工作原理
{: #how-it-works}

通过 iccs-cluster-autoscaler 映像，在 {{site.data.keyword.containerlong}} 中工作时，可以跨任何指定的自动扩展组，在 Kubernetes 集群中向上或向下扩展工作程序节点。Autoscaler 作为集群的 `kube-system` 名称空间中的部署运行，是 Kubernetes Cluster Autoscaler 上的一个插件。
{: shortdesc}

iccs-cluster-autoscaler 通过监控 Kubernetes API 服务器来监视集群中的 pod，并可以自动向上或向下扩展集群大小。

<dl>
  <dt>向上扩展</dt>
    <dd>iccs-cluster-autoscaler 会定期检查是否有任何不可安排的 pod。Kubernetes 调度程序找不到要运行 pod 的节点时，该 pod 即为不可安排的 pod。Autoscaler 遇到标记为不可安排的 pod 时，会向集群添加一个工作程序节点。</dd>
  <dt>向下扩展</dt>
    <dd>缺省情况下，iccs-cluster-autoscaler 每 10 秒检查一次是否有不需要的节点。如果节点处于不需要状态的时间超过 10 分钟，即会删除该节点。</br> 在以下情况下，节点会视为是不需要的节点：</br>
    <ul><li>此节点上运行的所有 pod 的 CPU 和内存请求数总和低于其分配量的 50%。</li>
    <li>此节点上运行的所有 pod 都可以移至其他节点。此测试中会排除缺省情况下在所有节点上运行的 pod，例如清单运行 pod 或由守护程序集创建的 pod。</li>
    <li>没有向下扩展禁用注释。</li></ul></dd>
</dl>

但这一切意味着什么呢？

![自动扩展集群](/images/autoscaler.png)

在 IBM Cloud 中创建 Kubernetes 集群时，可指定硬件首选项。Autoscaler 会根据机器类型创建组，以允许您确保使用正确的资源量。在映像中，可以看到示例自动扩展策略。在配置中，指定了机器类型以及最小和最大节点数。如果希望 Autoscaler 忽略一组节点而不进行扩展，请勿将这组节点包含在配置中。在映像中，集群会向上扩展以处理资源中的更改。b2c.16x64 节点并未扩展，因为配置文件中未设置任何策略。

## 使用限制
{: #usage}
此映像可以在具有 Kubernetes API V1.7 或更高版本的 {{site.data.keyword.containershort}} 中使用。

## 先决条件
{: #prerequisites}

在使用 iccs-cluster-autoscaler 之前，必须完成一些先决条件。

* 您必须具有标准集群才能使用 Autoscaler 插件。自动扩展组由相同[机器类型](/docs/containers/cs_cli_reference.html#cs_machine_types)的工作程序节点组成。要验证是否满足这些需求，请运行以下命令并检查 **Machine Type** 是否未显示 _free_。检查 **Version** 是否为 `V1.7` 或更高版本</br>
    ```
    ibmcloud ks workers <cluster-name>
    ```
    {: pre}
    示例输出：</br>
    ```
    OK
    ID                                                   Public IP        Private IP     Machine Type   State    Status   Version
    stage-dal06-crffef8b930b214442802bdcaa551b5ec0-w1    169.54.180.171   10.146.32.7    u2c.2x4        normal   Ready    1.8.2_1504
    ```
    {: screen}
* 必须安装以下 CLI 和插件。有关安装需求和创建第一个集群的帮助，请[检查文档中的本教程](/docs/containers/cs_tutorials.html#cs_cluster_tutorial) 。
    <ul><li>{{site.data.keyword.Bluemix_notm}} CLI</li><li>Kubectl CLI</li><li>Docker CLI</li><li>{{site.data.keyword.containerlong_notm}} 插件</li><li>{{site.data.keyword.registrylong_notm}} 插件</li></ul>
* 为了代表您扩展工作程序节点，Autoscaler 需要知道您的 Identity and Access Management 详细信息。凭证在付费集群中作为私钥提供。要验证该私钥是否可用，请运行以下命令。此命令会返回私钥。
    ```
		$kubectl get secrets -n kube-system | grep storage-secret-store
    ```
    {: pre}

## 安装和配置 Autoscaler
{: #install-configure}

要安装 Autoscaler，您需要先将 Helm 安装到集群，并对其进行初始化。Autoscaler 开始运行后，会自动节省资源并扩展集群。

1. 登录到 {{site.data.keyword.containershort_notm}}，并将您的集群设定为目标。
    ```
    ibmcloud ks cluster-config <cluster_name>`.
    ```
    {: pre}

2. [在集群中安装 Helm 并对其进行初始化](https://docs.helm.sh/using_helm/#quickstart)。

3. 使用提供的模板，创建配置文件。

   ```
   cp incubator/helm/autoscaler/config.yaml.template config.yaml
   ```
   {: pre}

    为了使扩展组正常运行，必须在配置文件中指定所有机器类型，即使未提供最小和最大范围。
    {: tip}

  <table summary="表中的第一行跨两列。其余行应该从左向右阅读，其中第一列是参数，第二列是匹配的描述，第三列是缺省值。">
  <caption>可配置参数及其缺省值</caption>
    <thead>
      <th colspan=3><img src="../images/idea.png" alt="“构想”图标"/> 可配置参数及其缺省值</th>
    </thead>
    <tbody>
      <tr>
        <td><code>autoscalingGroups.name</code></td>
        <td> 要扩展的机器类型。</td>
        <td> 没有缺省值，由用户指定。要找到您的机器类型，请运行 <code>ibmcloud ks workers <cluster-name></code>。</td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.minSize</code></td>
        <td> 集群应该扩展到的最小工作程序节点数。</td>
        <td> 没有缺省值，由用户指定。</td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.maxSize</code></td>
        <td> 集群应该扩展到的最大工作程序节点数。</td>
        <td> 没有缺省值，由用户指定。</td>
      </tr>
      <tr>
        <td><code>api_route</code></td>
        <td> 运行集群的区域的 {{site.data.keyword.containershort_notm}} API 的路径。</td>
        <td> https://containers.bluemix.net/swagger-api/</td>
      </tr>
      <tr>
        <td><code>resources.limits.cpu</code></td>
        <td> Autoscaler pod 限制为使用的 CPU 量。</td>
        <td> 300m</td>
      </tr>
      <tr>
        <td><code>resources.limits.memory</code></td>
        <td> Autoscaler pod 限制为使用的内存量。</td>
        <td> 300Mi</td>
      </tr>
      <tr>
        <td><code>resources.requests.cpu</code></td>
        <td> 希望 Autoscaler pod 一开始使用的 CPU 量。</td>
        <td> 100m</td>
      </tr>
      <tr>
        <td><code>resources.requests.memory</code></td>
        <td> 希望 Autoscaler pod 一开始使用的内存量。</td>
        <td> 100Mi</td>
      </tr>
      <tr>
        <td><code>scale_down_delay</code></td>
        <td> 发出向上扩展请求后，扩展器应该等待向下扩展的时间量。</td>
        <td> 10m</td>
      </tr>
      <tr>
        <td><code>scale_down_unneeded_time</code></td>
        <td> 节点在有资格向下扩展之前应该处于不需要状态的时间。</td>
        <td> 10m</td>
      </tr>
      <tr>
        <td><code>skipNodes.withLocalStorage</code></td>
        <td> 设置为 true 时，将跳过包含本地存储器的节点，并且不会向下扩展这些节点。</td>
        <td> true</td>
      </tr>
      <tr>
        <td><code>skipNodes.withSystemPods</code></td>
        <td> 设置为 true 时，将跳过包含 kube-system pod 的节点，并且不会向下扩展这些节点。</td>
        <td> true</td>
      </tr>
      <tr>
        <td><code>image.repository</code></td>
        <td> Cluster Autoscaler Docker 映像。</td>
        <td> registry.bluemix.net/iccs-cluster-autoscaler</td>
      </tr>
      <tr>
        <td><code>image.tag</code></td>
        <td> 要拉取的映像的版本。</td>
        <td> 12</td>
      </tr>
      <tr>
        <td><code>image.pullPolicy</code></td>
        <td> 指定要拉取 Docker 映像的时间。</td>
        <td> Always。</td>
      </tr>
    </tbody>
  </table>
  
  iccs-cluster-autoscaler 支持各种配置参数。有关其工作方式的更详细信息，请参阅 [Kubernetes Autoscaler 常见问题](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md)。

4. 将 Helm 图表安装到 `kube-system` 名称空间中的集群。

  ```
  helm install incubator/helm/autoscaler -f config.yaml --namespace kube-system
  ```
  {: pre}

    输出应该类似于以下内容：
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

## 验证安装

1. 验证包含 Autoscaler pod 的集群是否正在运行。

  ```
  kubectl get pods --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    示例输出：
  ```
    cluster-autoscaler-4289984493-zldfk        1/1       Running   0          3m
  ```
  {: screen}

2. 验证 Cluster Autoscaler 是否正在运行。

  ```
  kubectl get service --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    示例输出：
  ```
  cluster-autoscaler       10.10.10.204   <none>         8085/TCP                     4m
  ```
  {: screen}


## 卸载 Autoscaler

可以卸载 Cluster Autoscaler 部署。

1. 登录到 {{site.data.keyword.Bluemix_notm}}，然后通过运行 `ibmcloud ks cluster-config <cluster_name>` 将您的集群设定为目标。

2. 获取部署的名称。

  ```
  helm ls | grep autoscaler
  <deployment_name>	1       	Mon Dec 18 13:45:09 2017	DEPLOYED	autoscaler-1.0.3	kube-system
  ```
  {: pre}

3. 删除部署。

  ```
  helm delete <deployment_name>
  ```
  {: pre}
