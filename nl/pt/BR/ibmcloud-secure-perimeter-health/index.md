---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-21"

keywords: IBM Cloud Container Registry, ibmcloud-secure-perimeter-health, container image, health, Secure Perimeter, scan, public image

subcollection: RegistryImages

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:table: .aria-labeledby="caption"}

# Introdução à imagem `ibmcloud-secure-perimeter-health`
{: #ibmcloud-secure-perimeter-health}

A imagem `ibmcloud-secure-perimeter-health` contém uma ferramenta para varrer as vulnerabilidades em um perímetro seguro no {{site.data.keyword.cloud}}.
{:shortdesc}

É possível acessar as imagens que são fornecidas pela {{site.data.keyword.IBM_notm}} usando a linha de comandos. Consulte [Imagens públicas da IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Como ele Funciona
{: #sph_how-it-works}

Para assegurar que o perímetro seguro esteja funcionando adequadamente, o `ibmcloud-secure-perimeter-health` pode varrer as redes públicas ou privadas em sua conta de infraestrutura do {{site.data.keyword.cloud_notm}} e relatar as vulnerabilidades. É possível usar a imagem **ibmcloud-secure-perimeter-health** de duas maneiras:

- Use `ibmcloud-secure-perimeter-health` como um pod em um cluster do Kubernetes em seu perímetro seguro para varrer exposições de rede privada.
- Use `ibmcloud-secure-perimeter-health` como um contêiner do Docker independente em sua estação de trabalho para varrer exposições de rede pública.

Mais informações sobre o perímetro seguro podem ser encontradas nestes artigos do blog:

- [Configure um perímetro seguro no {{site.data.keyword.cloud_notm}} ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/).
- [Configure um perímetro seguro automatizado no {{site.data.keyword.cloud_notm}} ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

Após a varredura, a imagem `ibmcloud-secure-perimeter-health` produz um relatório sobre quais redes estavam acessíveis de dentro do segmento de perímetro seguro. Cada relatório detalha o nome do gateway de rede, da VLAN, de suas sub-redes e de quaisquer hosts ofensivos. Um relatório de exemplo de um usuário que varreu em busca de vulnerabilidades da rede privada:

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
{: #sph_whats_included}

A imagem `ibmcloud-secure-perimeter-health` fornece os pacotes de software a seguir.
{:shortdesc}

- Alpine Linux
- Tempo de execução Python
- Cliente Python do SoftLayer
- Scanner de porta Nmap

## Provisionar um cluster do Kubernetes dentro de um Perímetro seguro usando o {{site.data.keyword.containerlong_notm}}
{: #sph_provision_cluster}

1. Forneça o cluster do Kubernetes por meio da seção **Contêineres** no catálogo do {{site.data.keyword.cloud_notm}}.
2. Clique em **Criar**.
3. Selecione as VLANs públicas e privadas do segmento de perímetro seguro dos menus suspensos da VLAN.
4. Insira todos os outros detalhes, conforme necessário.
5. Clique em  ** Criar Cluster **.

Revise a documentação do [{{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-container_index#container_index) sobre como obter acesso ao seu cluster após ele ser implementado.

## Varrer as redes privadas em um perímetro seguro
{: #sph_private_networks}

Crie um pod de contêiner por meio da imagem `ibmcloud-secure-perimeter-health` e configure uma varredura de rotina.

**Antes de começar**

- Instale as [CLIs](/docs/containers?topic=containers-cs_cli_install#cs_cli_install) necessárias.
- [Destine sua CLI](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure) para seu cluster.

1. Crie um arquivo de configuração denominado _health-pod.yaml_. Esse arquivo cria uma implementação altamente disponível do pod do contêiner.

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
          - nome: health-pod
            imagem: registry.bluemix.net/ibm/ibmcloud-secure-perimeter-health:1.0.0
            argumentos:
            - /usr/local/bin/python
            - /run.py
            - --scan
            - privada
            - --excluir-vlan-ids
            - < Private Secure Perimeter Segment VLAN ID>
            - -- poll-interval
            - 1800
            ambiente:
            - nome: SL_USER
              valor: <IBM Cloud infrastructure user name>
            - nome: SL_APIKEY
              valor: <IBM Cloud infrastructure api key>
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
{: #sph_public_networks}

Crie um contêiner do Docker por meio da imagem `ibmcloud-secure-perimeter-healt` e varra as redes públicas.

**Antes de começar**

- Instale o Docker.

1. Crie um contêiner do Docker de sua própria estação de trabalho, da seguinte forma:

    ```
    docker run -it -e SL_USER='$SL_USER' -e SL_APIKEY='$SL_APIKEY' registry.bluemix.net/ibm/ibmcloud-secure-perimeter-health:1.0.0 /usr/local/bin/python run.py --scan public --allowed-public-ports 80 443 9000-9999
    ```
    {: pre}

2. Depois que o contêiner produzir um relatório, revise a seção [Analisando os resultados da varredura](#sph_scan_results) para entender os resultados.

## Analisando os resultados da varredura
{: #sph_scan_results}

A `ibmcloud-secure-perimeter-health` produz um relatório formatado sobre o funcionamento de um perímetro seguro:

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

A `ibmcloud-secure-perimeter-health` determinará uma sub-rede como `PASS` se nenhum host na sub-rede estiver acessível, caso contrário, retornará com `FAIL` e listará os hosts que estavam acessíveis, juntamente com as portas que estavam acessíveis.

## Referência de argumento do contêiner
{: #sph_reference_container_arg}

|Chave|Descrição|Padrão
|---|-------------|---|
|scan|O tipo de varredura de exposição ("pública" ou
"privada") |None (varrer ambas)
|exclude-vlan-ids|Lista de VLANs por IDs a evitar a
varredura|None
|poll-interval|Configurar o número de segundos até a
próxima varredura|0 (executar uma única vez)
|allowed-public-ports|Lista de portas para a
lista de desbloqueio da varredura|80, 443, 9000-9999
{: caption="Tabela 1. Argumentos do contêiner" caption-side="top"}

## Referência de variável de ambiente
{: #sph_reference_env_var}

|Chave|Descrição|
|---|-------------|
|SL_USER|Seu nome do usuário da infraestrutura no IBM Cloud|
|SL_APIKEY|Sua chave API da infraestrutura no IBM Cloud|
{: caption="Tabela 2. Variáveis de ambiente" caption-side="top"}
