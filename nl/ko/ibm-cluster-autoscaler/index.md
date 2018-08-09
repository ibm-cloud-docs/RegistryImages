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

# iccs-cluster-autoscaler 이미지 시작하기(alpha)
{: #ibm-cluster-autoscaler}

iccs-cluster-autoscaler 이미지에는 {{site.data.keyword.containerlong}} 클러스터 내에 작업자 노드를 자동으로 스케일링하는 데 필요한 사전 설치된 패키지가 포함되어 있습니다. iccs-cluster-autoscaler는 [Kubernetes Cluster-Autoscaler 프로젝트](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler)를 기반으로 합니다.
{:shortdesc}

Auto-Scaler의 alpha 버전은 현재 단일 구역 가용성만 지원하고 실제 베어메탈 서버의 작업 노드에 사용할 수 없습니다.
{: tip}

## 작동 방식
{: #how-it-works}

iccs-cluster-autoscaler 이미지를 사용하여 {{site.data.keyword.containerlong}}에서 작업 중일 때 Kubernetes 클러스터 위 또는 아래로, 지정된 Auto-Scaling 그룹 전체에 작업자 노드를 스케일링할 수 있습니다. Auto-Scaler는 클러스터의 `kube-system` 네임스페이스에 있는 배치로 실행되고 Kubernetes 클러스터 Auto-Scaler에 있는 플러그인입니다.
{: shortdesc}

iccs-cluster-autoscaler는 Kubernetes API 서버를 보면서 클러스터의 팟(Pod)을 모니터하고 클러스터의 크기를 위 또는 아래로 자동 스케일링할 수 있습니다.

<dl>
  <dt>스케일 업</dt>
    <dd>iccs-cluster-autoscaler는 주기적으로 스케줄링 불가능한 팟(Pod)을 확인합니다. 팟(Pod)은 Kubernetes 스케줄러가 팟(Pod)을 실행하는 노드를 찾지 못하는 경우 스케줄링 불가능합니다. Auto-Scaler에 스케줄링 불가능으로 레이블 지정된 팟(Pod)이 발생하는 경우 작업자 노드를 클러스터에 추가합니다.</dd>
  <dt>스케일 다운</dt>
    <dd>기본적으로 iccs-cluster-autoscaler는 10초마다 필요하지 않은 노드를 확인합니다. 노드가 10분 넘게 필요하지 않으면 삭제됩니다.</br> 노드가 필요하지 않다고 간주되는 경우는 다음과 같습니다.</br>
    <ul><li>이 노드에서 실행되는 모든 팟(Pod)의 CPU 및 메모리 요청의 총계는 할당된 양의 50% 미만입니다.</li>
    <li>노드에서 실행되는 모든 팟(Pod)은 다른 노드로 이동될 수 있습니다. 기본적으로 모든 노드에 실행되는 팟(Pod)(예: manifest-run 팟(Pod) 또는 디먼 세트로 작성되는 팟(Pod))은 이 테스트에서 제외됩니다. </li>
    <li>어노테이션이 사용 안함으로 설정된 스케일 다운이 없습니다.</li></ul></dd>
</dl>

무엇을 의미합니까?

![클러스터 자동 스케일링](/images/autoscaler.png)

IBM Cloud에서 Kubernetes 클러스터를 작성하는 경우 하드웨어 환경 설정을 지정합니다. Auto-Scaler는 올바른 양의 리소스를 사용하고 있는지 확인할 수 있는 머신 유형을 기반으로 그룹을 작성합니다. 이미지에서 Auto-Scaling 정책 예제를 볼 수 있습니다. 구성에서 머신 유형과 노드의 최소값 및 최대값이 지정됩니다. Auto-Scaler에서 스케일이 아닌 노드의 세트를 무시하려면 구성에 노드를 포함하지 마십시오. 이미지에서 클러스터는 리소스의 변경사항을 처리하도록 스케일 업합니다. b2c.16x64 노드는 정책이 구성 파일에 설정되지 않았으므로 스케일링되지 않았습니다.

## 사용 제한사항
{: #usage}
이 이미지는 Kubernetes API 버전 1.7 이상으로 {{site.data.keyword.containershort}}에서 사용할 수 있습니다.

## 전제조건
{: #prerequisites}

iccs-cluster-autoscaler에 대해 작업하기 전에 완료해야 하는 일부 전제조건이 있습니다.

* Auto-Scaler 플러그인을 사용하려면 표준 클러스터가 있어야 합니다. Autos-Saling 그룹은 동일한 [머신 유형](/docs/containers/cs_cli_reference.html#cs_machine_types)인 작업자 노드로 구성됩니다. 요구사항을 충족하는지 확인하려면 다음 명령을 실행하여 **머신 유형**에 _free_가 표시되어 있지 않은지 확인하십시오. **버전**이 `version 1.7` 이상인지 확인하십시오.</br>
    ```
    ibmcloud ks workers <cluster-name>
    ```
    {: pre}
    출력 예제: </br>
    ```
    OK
    ID                                                   Public IP        Private IP     Machine Type   State    Status   Version
    stage-dal06-crffef8b930b214442802bdcaa551b5ec0-w1    169.54.180.171   10.146.32.7    u2c.2x4        normal   Ready    1.8.2_1504
    ```
    {: screen}
* 다음 CLI 및 플러그인을 설치해야 합니다. 요구사항 설치 및 첫 번째 클러스터 작성에 대한 도움말은 [문서에서 이 튜토리얼을 확인](/docs/containers/cs_tutorials.html#cs_cluster_tutorial)하십시오.
    <ul><li>{{site.data.keyword.Bluemix_notm}} CLI</li><li>Kubectl CLI</li><li>Docker CLI</li><li>{{site.data.keyword.containerlong_notm}} 플러그인</li><li>{{site.data.keyword.registrylong_notm}} 플러그인</li></ul>
* 작업자 노드를 대신 스케일링하려면 Auto-Scaler는 ID 및 액세스 관리 세부사항에 대해 알고 있어야 합니다. 신임 정보는 유료 클러스터의 시크릿으로 사용할 수 있습니다. 시크릿이 사용 가능한지 확인하려면 다음 명령을 실행하십시오. 명령은 시크릿을 리턴합니다.
```
		$kubectl get secrets -n kube-system | grep storage-secret-store
    ```
    {: pre}

## Auto-Scaler 설치 및 구성
{: #install-configure}

Auto-Scaler를 설치하려면 클러스터에서 Helm을 처음 설치하고 초기화해야 합니다. Auto-Scaler가 실행된 후 리소스를 자동으로 보존하고 클러스터를 스케일링합니다. 

1. {{site.data.keyword.containershort_notm}}에 로그인하고 클러스터를 대상으로 지정하십시오.
    ```
    ibmcloud ks cluster-config <cluster_name>`.
    ```
    {: pre}

2. [클러스터에서 helm을 설치하고 초기화](https://docs.helm.sh/using_helm/#quickstart)하십시오.

3. 제공된 템플리트를 사용하여 구성 파일을 작성하십시오.

   ```
   cp incubator/helm/autoscaler/config.yaml.template config.yaml
   ```
   {: pre}

    최소 및 최대 범위를 제공하지 않은 경우에도 스케일링 그룹이 작동하도록 모든 머신 유형이 구성 파일에 지정되어야 합니다.
    {: tip}

  <table summary="표의 첫 번째 행이 모든 열에 걸쳐 표시됩니다. 나머지 행은 왼쪽에서 오른쪽으로 읽어야 하며 첫 번째 열에는 매개변수, 두 번째 열에는 일치하는 설명, 세 번째 열에는 기본값이 표시됩니다.">
  <caption>구성 가능한 매개변수 및 기본값</caption>
    <thead>
      <th colspan=3><img src="../images/idea.png" alt="아이디어 아이콘"/>구성 가능한 매개변수 및 기본값</th>
    </thead>
    <tbody>
      <tr>
        <td><code>autoscalingGroups.name</code></td>
        <td> 스케일링할 머신 유형입니다.</td>
        <td> 기본값이 없으며 사용자가 지정합니다. 머신 유형을 찾으려면 <code>ibmcloud ks workers <cluster-name></code>을 실행하십시오.</td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.minSize</code></td>
        <td> 클러스터가 스케일링해야 할 작업자 노드의 최소값입니다.</td>
        <td> 기본값이 없으며 사용자가 지정합니다. </td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.maxSize</code></td>
        <td> 클러스터가 스케일링해야 할 작업자 노드의 최대값입니다.</td>
        <td> 기본값이 없으며 사용자가 지정합니다. </td>
      </tr>
      <tr>
        <td><code>api_route</code></td>
        <td> 클러스터가 실행 중인 지역에 대한 {{site.data.keyword.containershort_notm}} API의 라우트입니다.</td>
        <td> https://containers.bluemix.net/swagger-api/ </td>
      </tr>
      <tr>
        <td><code>resources.limits.cpu</code></td>
        <td> Auto-Scaler 팟(Pod)이 제한되는 CPU의 양입니다.</td>
        <td> 300m </td>
      </tr>
      <tr>
        <td><code>resources.limits.memory</code></td>
        <td> Auto-Scaler 팟(Pod)이 제한되는 메모리의 양입니다.</td>
        <td> 300Mi </td>
      </tr>
      <tr>
        <td><code>resources.requests.cpu</code></td>
        <td> Auto-Scaler 팟(Pod)이 시작할 CPU의 양입니다.</td>
        <td> 100m </td>
      </tr>
      <tr>
        <td><code>resources.requests.memory</code></td>
        <td> Auto-Scaler 팟(Pod)이 시작할 메모리의 양입니다.</td>
        <td> 100Mi </td>
      </tr>
      <tr>
        <td><code>scale_down_delay</code></td>
        <td> 스케일 업 요청 발행 후 스케일러가 스케일 다운될 때까지 기다려야 하는 시간입니다.</td>
        <td> 10m </td>
      </tr>
      <tr>
        <td><code>scale_down_unneeded_time</code></td>
        <td> 스케일 다운될 수 있기 전에 노드가 필요하지 않아야 하는 시간의 마운트입니다. </td>
        <td> 10m </td>
      </tr>
      <tr>
        <td><code>skipNodes.withLocalStorage</code></td>
        <td> true로 설정된 경우 로컬 스토리지가 포함된 노드는 건너뛰고 스케일 다운되지 않습니다.</td>
        <td> true </td>
      </tr>
      <tr>
        <td><code>skipNodes.withSystemPods</code></td>
        <td> true로 설정된 경우  kube-system 팟(Pod)이 포함된 노드는 건너뛰고 스케일 다운되지 않습니다.</td>
        <td> true </td>
      </tr>
      <tr>
        <td><code>image.repository</code></td>
        <td> 클러스터 Auto-Scaler Docker 이미지입니다.</td>
        <td> registry.bluemix.net/iccs-cluster-autoscaler </td>
      </tr>
      <tr>
        <td><code>image.tag</code></td>
        <td> 가져올 이미지의 버전입니다.</td>
        <td> 12 </td>
      </tr>
      <tr>
        <td><code>image.pullPolicy</code></td>
        <td> Docker 이미지를 가져오는 시기를 지정합니다.</td>
        <td> 항상</td>
      </tr>
    </tbody>
  </table>
  
  iccs-cluster-autoscaler는 다양한 구성 매개변수를 지원합니다. 작동 방법에 대한 자세한 정보는 [Kubernetes Auto-Scaler FAQ](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md)를 참조하십시오.

4. `kube-system` 네임스페이스에 클러스터에 대한 helm 차트를 설치하십시오.

  ```
  helm install incubator/helm/autoscaler -f config.yaml --namespace kube-system
  ```
  {: pre}

    출력은 다음과 같이 표시되어야 합니다.
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

## 설치 확인

1. Auto-Scaler 팟(Pod)이 포함된 클러스터가 실행 중인지 확인하십시오.

  ```
  kubectl get pods --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    출력 예제:
  ```
    cluster-autoscaler-4289984493-zldfk        1/1       Running   0          3m
  ```
  {: screen}

2. 클러스터 Auto-Scaler가 실행 중인지 확인하십시오.

  ```
  kubectl get service --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    출력 예제:
  ```
  cluster-autoscaler       10.10.10.204   <none>         8085/TCP                     4m
  ```
  {: screen}


## Auto-Scaler 설치 제거

클러스터 Auto-Scaler 배치를 설치 제거할 수 있습니다.

1. {{site.data.keyword.Bluemix_notm}}에 로그인하고 `ibmcloud ks cluster-config <cluster_name>`를 실행하여 클러스터를 대상으로 지정하십시오.

2. 배치의 이름을 가져오십시오.

  ```
  helm ls | grep autoscaler
  <deployment_name>	1       	Mon Dec 18 13:45:09 2017	DEPLOYED	autoscaler-1.0.3	kube-system
  ```
  {: pre}

3. 배치를 삭제하십시오.

  ```
  helm delete <deployment_name>
  ```
  {: pre}
