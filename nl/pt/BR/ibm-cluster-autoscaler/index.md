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

# Introdução à imagem iccs-cluster-autoscaler (alfa)
{: #ibm-cluster-autoscaler}

A imagem iccs-cluster-autoscaler contém os pacotes pré-instalados que são necessários para escalar automaticamente os nós do trabalhador no cluster do {{site.data.keyword.containerlong}}. O iccs-cluster-autoscaler baseia-se no projeto [Kubernetes Cluster-Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler).
{:shortdesc}

A versão alfa do autoescalador suporta atualmente apenas a disponibilidade de zona única e não está disponível para nós do trabalhador em servidores bare metal físicos.
{: tip}

## Como ele Funciona
{: #how-it-works}

Com a imagem iccs-cluster-autoscaler, é possível aumentar ou reduzir a capacidade dos nós do trabalhador no cluster do Kubernetes, em qualquer grupo de auto-scaling especificado ao trabalhar no {{site.data.keyword.containerlong}}. O autoescalador é executado como uma implementação no namespace `kube-system` de seu cluster e é um plug-in no autoescalador do cluster do Kubernetes.
{: shortdesc}

O iccs-cluster-autoscaler monitora os pods no cluster observando o servidor API do Kubernetes e pode aumentar ou reduzir o tamanho do cluster automaticamente.

<dl>
  <dt>Aumentar a capacidade</dt>
    <dd>Periodicamente, o iccs-cluster-autoscaler verifica se há algum pod que não permite planejamento. Um pod não permite planejamento quando o planejador do Kubernetes falha ao localizar um nó no qual executar o pod. Quando o autoescalador encontra pods identificados como não planejáveis, ele inclui um nó trabalhador no cluster.</dd>
  <dt>Reduzir a capacidade</dt>
    <dd>Por padrão, o iccs-cluster-autoscaler verifica os nós desnecessários a cada 10 segundos. Se um nó for desnecessário por mais de 10 minutos, ele será excluído.</br> Nodes são considerados desnecessários quando:</br>
    <ul><li>A soma de solicitações de CPU e de memória de todos os pods em execução nesse nó é menor que 50% da quantia alocada para ele.</li>
    <li>Todos os pods em execução no nó podem ser movidos para outros nós. Os pods executados em todos os nós por padrão, como pods manifest-run ou aqueles criados por conjuntos de daemons, são excluídos desse teste.</li>
    <li>Não há nenhuma anotação desativada para escalas. </li></ul></dd>
</dl>

Mas o que tudo isso significa?

![Automatically scaling a cluster](/images/autoscaler.png)

Ao criar o cluster do Kubernetes no IBM Cloud, você especifica suas preferências de hardware. O autoescalador cria grupos com base nos tipos de máquina para permitir que você assegure que está usando a quantia correta de recursos. Na imagem, é possível ver um exemplo de política de auto-scaling. Na configuração, o tipo de máquina e o número mínimo e máximo de nós são especificados. Se desejar que o autoescalador ignore um conjunto de nós e não escale, não o inclua na configuração. Na imagem, a capacidade do cluster foi aumentada para manipular as mudanças nos recursos. O nó b2c.16x64 não foi escalado porque não havia política configurada no arquivo de configuração.

## Restrições de uso
{: #usage}
Esta imagem pode ser usada no {{site.data.keyword.containershort}} com a API do Kubernetes versão 1.7 ou mais recente.

## Pré-requisitos
{: #prerequisites}

Há alguns pré-requisitos que devem ser concluídos antes de trabalhar com o iccs-cluster-autoscaler.

* Deve-se ter um cluster padrão para usar o plug-in do autoescalador. Os grupos de auto-scaling são compostos de nós do trabalhador que têm os mesmos [tipos de máquina](/docs/containers/cs_cli_reference.html#cs_machine_types). Para verificar se você atende aos requisitos, execute o comando a seguir e verifique se seu **Tipo de máquina** não diz _grátis_. Verifique se a **Versão** é `version 1.7` ou mais recente </br>
    ```
    ibmcloud ks workers <cluster-name>
    ```
    {: pre}
    Saída de exemplo: </br>
    ```
    OK
    ID                                                   Public IP        Private IP     Machine Type   State    Status   Version
    stage-dal06-crffef8b930b214442802bdcaa551b5ec0-w1    169.54.180.171   10.146.32.7    u2c.2x4        normal   Ready    1.8.2_1504
    ```
    {: screen}
* Deve-se instalar as CLIs e plug-ins a seguir. Para ajudar a instalar os requisitos e criar seu primeiro cluster, [efetue check-out deste tutorial nos docs](/docs/containers/cs_tutorials.html#cs_cluster_tutorial).
    <ul><li>CLI do {{site.data.keyword.Bluemix_notm}}</li><li>CLI do Kubectl</li><li>Docker CLI</li><li>{{site.data.keyword.containerlong_notm}}  plug-in</li><li>{{site.data.keyword.registrylong_notm}}  plug-in</li></ul>
* Para escalar os nós do trabalhador em seu nome, o autoescalador precisa conhecer os detalhes do Identity and Access Management (IAM). As credenciais estão disponíveis como um segredo em um cluster pago. Para verificar se o segredo está disponível, execute o comando a seguir. O comando retorna um segredo.
    ```
		$kubectl get secrets -n kube-system | grep storage-secret-store
    ```
    {: pre}

## Instalando e Configurando o Autoscaler
{: #install-configure}

Para instalar o autoescalador, é necessário primeiro instalar e inicializar o Helm em seu cluster. Depois que o autoescalador estiver em execução, ele preservará automaticamente os recursos e escalará os clusters.

1. Efetue login no {{site.data.keyword.containershort_notm}} e direcione seu cluster.
    ```
    ibmcloud ks cluster-config < cluster_name> `.
    ```
    {: pre}

2. [Instale o helm e inicialize-o no cluster](https://docs.helm.sh/using_helm/#quickstart).

3. Usando o modelo fornecido, crie um arquivo de configuração.

   ```
   cp incubator/helm/autoscaler/config.yaml.template config.yaml
   ```
   {: pre}

    Para que os grupos de ajuste de escala funcionem, todos os tipos de máquina devem ser especificados no arquivo de configuração, mesmo que você não forneça um intervalo mínimo e máximo.
    {: tip}

  <table summary="A primeira linha na tabela atravessa ambas as colunas. O restante das linhas deve ser lido da esquerda para a direita, com o parâmetro na coluna um, a descrição correspondente na coluna dois e o padrão na coluna três.">
  <caption>Parâmetros configuráveis e seu valor padrão</caption>
    <thead>
      <th colspan=3><img src="../images/idea.png" alt="Ícone de ideia"/>Parâmetros configuráveis e seus valores padrão </th>
    </thead>
    <tbody>
      <tr>
        <td><code> autoscalingGroups.name </code></td>
        <td> O tipo de máquina que você gostaria de escalar. </td>
        <td> Nenhum padrão, especificado pelo usuário. Para localizar seu tipo de máquina, execute <code>ibmcloud ks workers <cluster-name></code></td>
      </tr>
      <tr>
        <td><code> autoscalingGroups.minSize </code></td>
        <td> O número mínimo de nós do trabalhador para os quais o cluster deve ser escalado. </td>
        <td> Nenhum padrão, especificado pelo usuário. </td>
      </tr>
      <tr>
        <td><code> autoscalingGroups.maxSize </code></td>
        <td> O número máximo de nós do trabalhador para os quais o cluster deve ser escalado. </td>
        <td> Nenhum padrão, especificado pelo usuário. </td>
      </tr>
      <tr>
        <td><code> api_route </code></td>
        <td> A rota para a API do {{site.data.keyword.containershort_notm}} da região na qual o cluster está em execução. </td>
        <td> https://containers.bluemix.net/swagger-api/ </td>
      </tr>
      <tr>
        <td><code> resources.limits.cpu </code></td>
        <td> A quantia de CPU à qual o pod do autoescalador está limitado. </td>
        <td> 300 m </td>
      </tr>
      <tr>
        <td><code> resources.limits.memory </code></td>
        <td> A quantia de memória à qual o pod do autoescalador está limitado. </td>
        <td> 300Mi </td>
      </tr>
      <tr>
        <td><code> resources.requests.cpu </code></td>
        <td> A quantia de CPU com a qual você deseja que o pod do autoescalador seja iniciado. </td>
        <td> 100 m </td>
      </tr>
      <tr>
        <td><code> resources.requests.memory </code></td>
        <td> A quantia de memória com a qual você deseja que o pod do autoescalador seja iniciado. </td>
        <td> 100Mi </td>
      </tr>
      <tr>
        <td><code> scale_down_delay </code></td>
        <td> O período de tempo que o escalador precisa esperar para reduzir a capacidade depois de emitir uma solicitação de aumento de capacidade. </td>
        <td> 10m </td>
      </tr>
      <tr>
        <td><code> scale_down_unneeded_time </code></td>
        <td> O período em que um nó deve ser desnecessário antes que seja elegível para redução de capacidade. </td>
        <td> 10m </td>
      </tr>
      <tr>
        <td><code> skipNodes.withLocalStorage </code></td>
        <td> Quando configurados como true, nós que contiverem armazenamento local serão ignorados e não terão a capacidade reduzida. </td>
        <td> true </td>
      </tr>
      <tr>
        <td><code> skipNodes.withSystemPods </code></td>
        <td> Quando configurados como true, nós que contiverem pods kube-system serão ignorados e não terão a capacidade reduzida. </td>
        <td> true </td>
      </tr>
      <tr>
        <td><code> image.repository </code></td>
        <td> A imagem do Docker do calcador de cluster. </td>
        <td> registry.bluemix.net/iccs-cluster-autoscaler </td>
      </tr>
      <tr>
        <td><code> image.tag </code></td>
        <td> A versão da imagem que você deseja puxar. </td>
        <td> 12 </td>
      </tr>
      <tr>
        <td><code> image.pullPolicy </code></td>
        <td> Especifica quando puxar a imagem do Docker. </td>
        <td> Sempre. </td>
      </tr>
    </tbody>
  </table>
  
  O iccs-cluster-autoscaler suporta vários parâmetros de configuração. Para obter informações mais detalhadas sobre como ele funciona, consulte [a Pergunta mais frequente do Autoescalador do Kubernetes](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md).

4. Instale o gráfico de helm no cluster no namespace `kube-system`.

  ```
  helm install incubator/helm/autoscaler -f config.yaml --namespace kube-system
  ```
  {: pre}

    Sua saída deve ser semelhante à seguinte:
  ```
  NAMESPACE: STATUS do sistema kube: DEPLOYED

  RESOURCES:
  ==> v1/Service
  NAME                CLUSTER-IP    EXTERNAL-IP  PORT(S)   AGE
  cluster-autoscaler  10.10.10.189  <none>       8085/TCP  5s

  ==> v1beta1/Deployment
  NAME                DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
  cluster-autoscaler  1        1        1           0          3s
  ```
  {: screen}

## Verificando a Instalação

1. Verifique se o cluster que contém o autoescalador está em execução.

  ```
  kubectl get pods --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    Saída de exemplo:
  ```
    cluster-autoscaler-4289984493-zldfk        1/1       Running   0          3m
  ```
  {: screen}

2. Verifique se o autoescalador do cluster está em execução.

  ```
  kubectl get service --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    Saída de exemplo:
  ```
  cluster-autoscaler       10.10.10.204   <none>         8085/TCP                     4m
  ```
  {: screen}


## Desinstalando o Autoscaler

É possível desinstalar a implementação do autoescalador do cluster.

1. Efetue login no {{site.data.keyword.Bluemix_notm}} e direcione o cluster executando `ibmcloud ks cluster-config <cluster_name>`.

2. Obtenha o nome de sua implementação.

  ```
  helm ls | grep autoscaler
  <deployment_name>	1       	Mon Dec 18 13:45:09 2017	DEPLOYED	autoscaler-1.0.3	kube-system
  ```
  {: pre}

3. Exclua a implementação.

  ```
  helm delete <deployment_name>
  ```
  {: pre}
