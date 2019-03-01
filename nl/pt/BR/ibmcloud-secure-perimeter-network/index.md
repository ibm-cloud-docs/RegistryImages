---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-21"

keywords: ibmcloud-secure-perimeter-network, container image, network, Secure Perimeter, public image

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

# Introdução à imagem `ibmcloud-secure-perimeter-network`
{: #ibmcloud-secure-perimeter-network}

A imagem `ibmcloud-secure-perimeter-network` contém ferramentas para automatizar a configuração de dispositivos roteadores virtuais Vyatta dentro de um perímetro seguro.
{:shortdesc}

É possível acessar as imagens que são fornecidas pela {{site.data.keyword.IBM}} usando a linha de comandos. Consulte [Imagens públicas da IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Como ele Funciona
{: #spn_how-it-works}

Com a `ibmcloud-secure-perimeter-network`, é possível automatizar a configuração de seu dispositivo roteador virtual Vyatta do perímetro seguro.

Para obter mais informações sobre Perímetro seguro, consulte os artigos do blog:

- [Configure um perímetro seguro no IBM Cloud ![Ícone de link externo](../../../icons/launch-glyph.svg "ícone de link externo")](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/).
- [Configure um perímetro seguro automatizado no IBM Cloud ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

É possível usar a imagem `ibmcloud-secure-perimeter-network` de duas maneiras:

- Use `ibmcloud-secure-perimeter-network` como um contêiner do Docker para inicializar a configuração de regras de firewall do perímetro seguro.
- Use `ibmcloud-secure-perimeter-network` como um pod em um cluster do Kubernetes para pesquisar a conta de infraestrutura no IBM Cloud das novas sub-redes criadas em suas VLANs do segmento de perímetro seguro e incluí-las na configuração de firewall do Vyatta.

## O que está incluído
{: #spn_whats_included}

A imagem `ibmcloud-secure-perimeter-network` fornece os pacotes de software a seguir.
{:shortdesc}

- Alpine Linux
- Tempo de execução Python
- Cliente Python do SoftLayer
- Ansible

## Pré-requisitos
{: #spn_prerequisites}

- O Vyatta e as VLANs que foram solicitados por meio do portal de infraestrutura no IBM Cloud e as VLANs que foram associadas ao Vyatta.
- A implementação automatizada do perímetro seguro pré-carrega o Vyatta com as chaves SSH que a `ibmcloud-secure-perimeter-network` usa para acessar o gateway. As chaves SSH devem ser carregadas manualmente ou por meio do processo de instalação do Perímetro seguro. Revise o artigo [Configure um perímetro seguro automatizado no IBM Cloud ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/) para obter informações adicionais.

## Provisionar um cluster do Kubernetes dentro de um Perímetro seguro usando o {{site.data.keyword.containerlong_notm}}
{: #spn_provision_cluster}

1. Provisione o cluster do Kubernetes por meio da seção **Contêineres** no catálogo do IBM Cloud.
2. Clique em **Criar**.
3. Selecione as VLANs públicas e privadas do
segmento de perímetro seguro dos menus suspensos da VLAN.
4. Insira todos os outros detalhes, conforme necessário.
5. Clique em  ** Criar Cluster **.

Revise a documentação do [{{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-container_index#container_index) sobre como obter acesso ao seu cluster após ele ser implementado.

## Executar a configuração inicial do seu Vyatta de perímetro seguro
{: #spn_initial_setup}

1. Crie um arquivo denominado _config.json_. Esse arquivo contém os parâmetros básicos necessários para que a `ibmcloud-secure-perimeter-network` acesse o Vyatta.

  ```
  {
    "slid": "XXXX",
    "apikey": "XXXX",
    "region": "XXXX",
    "inf_name_private": "dp0bond0",
    "inf_name_public": "dp0bond1",
    "gatewayid": "XXXX",
    "vlans": [
      {
        "type": "XXXX",
        "vlan_num": XXXX,
        "vlanid": XXXX
      },
      ...
    ],
    "vyatta_gateway_vip": "X.X.X.X",
    "vyatta_primary": {
      "private_ip": "X.X.X.X",
      "public_ip": "X.X.X.X"
    },
    "vyatta_secondary": {
      "private_ip": "X.X.X.X",
      "public_ip": "X.X.X.X"
    }
  }
  ```
  {: codeblock}

  Consulte a [Tabela de referência de config.json](#spn_reference_config_json) para obter detalhes sobre como preencher _config.json_. Esse arquivo também pode ser usado no processo de [configuração da `ibmcloud-secure-perimeter-network` como um pod do Kubernetes](#spn_setup).

2. Execute a `ibmcloud-secure-perimeter-networt` como um contêiner do Docker para começar a configuração inicial.

  ```
  docker run registry.bluemix.net/ibm/ibmcloud-secure-perimeter-network:1.0.0 python config-secure-perimeter.py -v /path/to/current/dir:/opt/secure-perimeter
  ```
  {: pre}

  Essa ação criará um arquivo _state.json_ em seu diretório de trabalho. Esse arquivo é usado na [configuração da `ibmcloud-secure-perimeter-network` como um pod do Kubernetes](#spn_setup).

## Configurar como um pod do Kubernetes em seu
perímetro seguro
{: #spn_setup}

Para que a imagem `ibmcloud-secure-perimeter-network` gerencie as sub-redes em seu perímetro seguro, é possível executá-la como um processo de longa duração usando um pod do Kubernetes. O `ibmcloud-secure-perimeter-network` tem vários arquivos e pastas de configuração que devem ser copiados para o pod para configurá-lo para o Vyatta:

1. Crie um arquivo denominado _pvc.yaml_. Esse arquivo de configuração cria um persistent volume claim (pvc) que pode ser montado em seu pod como um volume.

  ```
  apiVersion: v1
  kind: PersistentVolumeClaim
  metadata:
    name: network-pvc
    annotations:
      volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
  spec:
    accessModes:
      - ReadWriteMany
    resources:
      requests:
        storage: 20Gi
  ```
  {: codeblock}

2. Crie o pvc.

    ```
    kubectl apply -f restore-pvc.yaml
    ```
    {: pre}

3. Crie um arquivo denominado _network-pod.yaml_. Esse arquivo de configuração implementa a imagem `ibmcloud-secure-perimeter-network` como um pod em seu cluster do Kubernetes e monta seu persistent volume claim como um volume.

  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: network-pod
    labels:
      app: network-pod
  spec:
    template:
      spec:
        containers:
        - name: network-pod
          image: registry.bluemix.net/ibm/ibmcloud-secure-perimeter-network:1.0.0
          volumeMounts:
          - name: network-vol
            mountPath: /opt/secure-perimeter
        volumes:
        - name: network-vol
          PersistentVolumeClaim:
            claimName: network-pvc
  ```
  {: codeblock}

4. Crie um arquivo denominado
_rules.conf_. Esse arquivo de configuração informa a `ibmcloud-secure-perimeter-network` sobre quais sub-redes e portas externas da Internet pública devem ser inseridas na lista desbloqueio no perímetro seguro.

  ```
  {
      "external_subnets": [
        "X.X.X.X/X",
        "X.X.X.X/X"
      ],
      "external_ports": [
        "XX",
        "XX"
      ],
      "userips": [
        "X.X.X.X",
        "X.X.X.X"
      ]
  }
  ```
  {: codeblock}

5. Copie os arquivos no pod da `ibmcloud-secure-perimeter-network`.

  ```
  kubectl cp keys network-pod:/opt/secure-perimeter/
  kubectl cp state.json network-pod:/opt/secure-perimeter/state.json
  kubectl cp config.json network-pod:/opt/secure-perimeter/config.json
  kubectl cp rules.conf network-pod:/opt/secure-perimeter/rules.conf
  ```
  {: pre}

  O diretório _keys_ contém as chaves SSH necessárias para que a `ibmcloud-secure-perimeter-network` acesse o Vyatta. Consulte a [seção de pré-requisitos](#spn_prerequisites) para obter mais informações sobre as chaves SSH.

## Referência do config.json
{: #spn_reference_config_json}

|Chave|Descrição
|---|-------------|---|
|slid|Seu nome do usuário da infraestrutura no IBM Cloud
|apikey|Sua chave API da infraestrutura no IBM Cloud
|region|A região do IBM Cloud em que o Vyatta está implementado
|inf_name_private|O nome da interface privada do Vyatta
|inf_name_public|O nome da interface pública do Vyatta
|gatewayid|O ID do gateway do Vyatta
|vlans|Lista de VLANs de segmento de perímetro seguro que contêm o tipo, o número de VLAN e o ID de VLAN
|vyatta_gateway_vip|O VIP do gateway
|vyatta_primary|Objeto que contém o IP privado e público do membro primário do Vyatta
|vyatta_secondary|Objeto que contém o IP privado e público do membro secundário do Vyatta
{: caption="Tabela 1. config.json" caption-side="top"}

## Referência do rules.conf
{: #spn_reference_rules_conf}

|Chave|Descrição
|---|-------------|---|
|external_subnets|Lista de sub-redes na Internet pública às quais o perímetro seguro será exposto
|external_ports|Lista de portas às quais o perímetro seguro será exposto
|userips|Lista de IPs do usuário a serem inseridos na lista de desbloqueio para o perímetro seguro
{: caption="Tabela 2. rules.conf" caption-side="top"}
