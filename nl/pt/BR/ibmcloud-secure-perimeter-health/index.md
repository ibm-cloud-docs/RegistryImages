---

copyright:
  years: 2018
lastupdated: "2018-05-25"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Introdução à imagem ibmcloud-secure-perimeter-health
{: #ibmcloud-secure-perimeter-health}

A imagem **ibmcloud-secure-perimeter-health** contém uma ferramenta para varrer vulnerabilidades em um perímetro seguro no IBM Cloud.
{:shortdesc}

## Como ele Funciona
{: #how-it-works}

Para assegurar que seu perímetro seguro esteja funcionando corretamente, a **ibmcloud-secure-perimeter-health** pode varrer redes públicas ou privadas em sua conta de infraestrutura no IBM Cloud e relatar vulnerabilidades. É possível usar a imagem **ibmcloud-secure-perimeter-health** de duas maneiras:

-   Use **ibmcloud-secure-perimeter-health** como um pod em um cluster do Kubernetes em seu perímetro seguro para varrer exposições de rede privada.
-   Use **ibmcloud-secure-perimeter-health** como um contêiner do Docker independente em sua estação de trabalho para varrer exposições de rede pública.

Mais informações sobre o perímetro seguro podem ser encontradas nestes artigos do blog:
  * [Configure um perímetro seguro no IBM Cloud](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/)
  * [Configure um perímetro seguro automatizado no IBM Cloud](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

Após a varredura, a imagem **ibmcloud-secure-perimeter-health** produz um relatório sobre quais redes estavam acessíveis de dentro do segmento de perímetro seguro. Cada relatório detalha o nome do gateway de rede, da VLAN, de suas sub-redes e de quaisquer hosts ofensivos. Um relatório de exemplo de um usuário que varreu em busca de vulnerabilidades da rede privada:
```
#-------- Running Secure Perimeter exposure scan 2018-05-24 12:00:00 --------#

RESULTS:

sp-gateway-af6053a9:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   FAIL
			host = 10.73.84.198, ports = [179, 22]
		10.73.63.8/29:     PASS
		10.73.72.128/26:   PASS
sp-gateway-8a9031ab:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   PASS
		10.73.63.8/29:     PASS
```
{: screen}

## O que está incluído
{: #whats_included}

A imagem **ibmcloud-secure-perimeter-health** fornece os pacotes de software a seguir.
{:shortdesc}

-   Alpine Linux
-   Tempo de execução Python
-   Cliente Python do SoftLayer 
-   Scanner de porta Nmap

## Iniciar
{: #how_to_get_started}

Revise as tarefas a seguir para aprender a usar **ibmcloud-secure-perimeter-health**:

1.  [Provisionar um cluster do Kubernetes dentro de um perímetro seguro usando o {{site.data.keyword.containerlong}}](#provision_cluster)
2.  [Varrer as redes privadas dentro de um perímetro seguro](#private_networks)
3.  [Varrer as redes públicas fora de um perímetro seguro](#public_networks)
4.  [Entendendo os resultados da varredura](#scan_results)
5.  [Referência de argumento do contêiner](#reference_container_arg)
6.  [Referência de variável de ambiente](#reference_env_var)


## Provisionar um cluster do Kubernetes dentro de um perímetro seguro usando o {{site.data.keyword.containerlong}}
{: #provision_cluster}

1.  Provisione o cluster do Kubernetes por meio da seção **Contêineres** no catálogo do IBM Cloud.
2.  Clique em Criar.
3.  Selecione as VLANs públicas e privadas do segmento de perímetro seguro dos menus suspensos da VLAN.
4.  Preencha todos os outros detalhes que achar necessários.
5.  Clique em Criar cluster.

Revise a documentação do [{{site.data.keyword.containerlong}}](../../../containers/container_index.html#container_index) sobre como obter acesso ao cluster depois que ele for implementado.

## Varrer as redes privadas em um perímetro seguro
{: #private_networks}

Crie um pod de contêiner por meio da imagem **ibmcloud-secure-perimeter-health** e configure uma varredura de rotina.

Antes de começar:

-   Instale as [CLIs](../../../containers/cs_cli_install.html#cs_cli_install) necessárias.
-   [Destine sua CLI](../../../containers/cs_cli_install.html#cs_cli_configure) para seu cluster.

1. Crie um arquivo de configuração denominado _health-pod.yaml_. Esse arquivo cria uma
implementação altamente disponível do pod do contêiner.

    ```
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: health-pod
      labels:
        app: health-pod
    spec:
      replicas: 1
      selector:
        app: health-pod
      template:
        spec:
          containers:
          - name: health-pod
            image: registry.<region>.bluemix.net/ibm/ibmcloud-secure-perimeter-health:1.0.0
            args:
            - /usr/local/bin/python
            - /run.py
            - --scan
            - private
            - --exclude-vlan-ids
            - <Private Secure Perimeter Segment VLAN ID>
            - --poll-interval
            - 1800
            env:
            - name: SL_USER
              value: <IBM Cloud infrastructure user name>
            - name: SL_APIKEY
              value: <IBM Cloud infrastructure api key>
    ```
    {: codeblock}

2. Crie a implementação.

    ```
    kubectl apply -f health-pod.yaml
    ```
    {: pre}

3. Confirme se o pod está em execução.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    health-pod-<random-id>                  1/1       Running   0          1hr
    ```
    {: screen}

## Varrer as redes públicas fora de um perímetro seguro
{: #public_networks}

Crie um contêiner do Docker por meio da imagem **ibmcloud-secure-perimeter-healt** e varra as redes públicas.

Antes de começar:

-  Instale o Docker

1. Crie um contêiner do Docker de sua própria estação de trabalho, da seguinte forma:

    ```
    docker run -it -e SL_USER='$SL_USER' -e SL_APIKEY='$SL_APIKEY' registry.<region>.bluemix.net/ibm/ibmcloud-secure-perimeter-health:1.0.0 /usr/local/bin/python run.py --scan public --allowed-public-ports 80 443 9000-9999
    ```
    {: pre}

2. Depois que o contêiner produzir um relatório, revise a seção [Analisando os resultados da varredura](#scan_results) para entender os resultados.

## Analisando os resultados da varredura
{: #scan_results}

A **ibmcloud-secure-perimeter-health** produz um relatório formatado sobre o funcionamento de um perímetro seguro:
```
#-------- Running Secure Perimeter exposure scan 2018-05-24 12:00:00 --------#

RESULTS:

sp-gateway-af6053a9:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   FAIL
			host = 10.73.84.198, ports = [179, 22]
		10.73.63.8/29:     PASS
		10.73.72.128/26:   PASS
sp-gateway-8a9031ab:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   PASS
		10.73.63.8/29:     PASS
```
{: screen}

O formato do relatório é o seguinte:
```
<gateway name>:
  <vlan name>:
    <subnet>:    PASS
    <subnet>:    FAIL
      host = <ip address>, ports = [<exposed port>, <exposed port>, ...]
```
{: screen}

A **ibmcloud-secure-perimeter-health** determinará uma sub-rede como `PASS` se nenhum host na sub-rede estiver acessível, caso contrário, retornará com `FAIL` e listará os hosts que estavam acessíveis, juntamente com as portas que estavam acessíveis.

## Referência de argumento do contêiner

{: #reference_container_arg}

|Chave|Descrição|Padrão
|---|-------------|---|
|scan|O tipo de varredura de exposição ("pública" ou
"privada") |None (varrer ambas)
|exclude-vlan-ids|Lista de VLANs por IDs a evitar a
varredura
|None
|poll-interval|Configurar o número de segundos até a
próxima varredura|0 (executar uma única vez)
|allowed-public-ports|Lista de portas para a
lista de desbloqueio da varredura|80, 443, 9000-9999
{: caption="Tabela 1. Argumentos do contêiner" caption-side="top"}

## Referência de variável de ambiente
{: #reference_env_var}

|Chave|Descrição|
|---|-------------|
|SL_USER|Seu nome do usuário da infraestrutura no IBM Cloud|
|SL_APIKEY|Sua chave API da infraestrutura no IBM Cloud|
{: caption="Tabela 2. Variáveis de ambiente" caption-side="top"}
