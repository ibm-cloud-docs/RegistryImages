---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-04"

keywords: IBM Cloud Container Registry, ibmcloud-secure-perimeter-network, container image, network, Secure Perimeter, public image

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
- Use `ibmcloud-secure-perimeter-network` como um pod em um cluster Kubernetes para pesquisar, na conta de infraestrutura do IBM Cloud, novas sub-redes criadas em suas VLANs de Segmento de perímetro seguro e incluí-las na configuração de firewall do Vyatta.

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

- O Vyatta e as VLANs solicitadas no portal de infraestrutura do IBM Cloud e as VLANs associadas ao Vyatta.
- A implementação automatizada do perímetro seguro pré-carrega o Vyatta com as chaves SSH que a `ibmcloud-secure-perimeter-network` usa para acessar o gateway. As chaves SSH devem ser carregadas manualmente ou por meio do processo de instalação do Perímetro seguro. Para obter mais informações, consulte [Configurar um Perímetro seguro automatizado no IBM Cloud ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

## Provisionar um cluster do Kubernetes dentro de um Perímetro seguro usando o {{site.data.keyword.containerlong_notm}}
{: #spn_provision_cluster}

1. Provisione o cluster do Kubernetes por meio da seção **Contêineres** no catálogo do IBM Cloud.
2. Clique em **Criar**.
3. Selecione as VLANs públicas e privadas do Segmento de perímetro seguro nas listas de VLAN.
4. Insira todos os outros detalhes, conforme necessário.
5. Clique em  ** Criar Cluster **.

Para acessar seu cluster após a implementação, consulte [{{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-getting-started#getting-started).

## Executar a configuração inicial do Vyatta de seu Perímetro seguro
{: #spn_initial_setup}

1. Crie um arquivo denominado `config.json`. Esse arquivo contém os parâmetros básicos requeridos por `ibmcloud-secure-perimeter-network` para acessar o Vyatta.

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

   Para obter mais informações sobre como preencher o `config.json`, consulte a [tabela de referência `config.json`](#spn_reference_config_json). Esse arquivo também pode ser usado no processo de [configuração da `ibmcloud-secure-perimeter-network` como um pod do Kubernetes](#spn_setup).

2. Execute a `ibmcloud-secure-perimeter-networt` como um contêiner do Docker para começar a configuração inicial.

   ```
   docker run icr.io/ibm/ibmcloud-secure-perimeter-network:1.0.0 python config-secure-perimeter.py -v /path/to/current/dir:/opt/secure-perimeter
   ```
   {: pre}

   Essa ação criará um arquivo `state.json` em seu diretório de trabalho. Esse arquivo é usado para [configurar o `ibmcloud-secure-perimeter-network` como o pod do Kubernetes](#spn_setup).

## Configurar um pod do Kubernetes dentro do seu Perímetro seguro
{: #spn_setup}

Se desejar que a imagem `ibmcloud-secure-perimeter-network` gerencie sub-redes em seu Perímetro seguro, será possível executá-la como um processo de longa duração usando um pod do Kubernetes. Deve-se copiar diversos arquivos e pastas de configuração da imagem `ibmcloud-secure-perimeter-network` para o pod para configurá-lo para o Vyatta:

1. Crie um arquivo denominado `pvc.yaml`. Esse arquivo de configuração cria uma solicitação de volume persistente (PVC) que pode ser montada em seu pod como um volume.

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

2. Crie o PVC.

   ```
   kubectl apply -f restore-pvc.yaml
   ```
   {: pre}

3. Crie um arquivo denominado `network-pod.yaml`. Esse arquivo de configuração implementa a imagem `ibmcloud-secure-perimeter-network` como um pod em seu cluster do Kubernetes e monta seu persistent volume claim como um volume.

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
           image: icr.io/ibm/ibmcloud-secure-perimeter-network:1.0.0
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
`rules.conf`. Esse arquivo de configuração informa a `ibmcloud-secure-perimeter-network` sobre quais sub-redes e portas externas da Internet pública devem ser inseridas na lista desbloqueio no perímetro seguro.

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

   O diretório `keys` contém as chaves SSH necessárias para que o `ibmcloud-secure-perimeter-network` acesse o Vyatta. Para obter mais informações sobre as chaves SSH, consulte a [seção de pré-requisitos](#spn_prerequisites).

## Referência do `config.json`
{: #spn_reference_config_json}

|Chave|Descrição
|---|-------------|---|
|`slid`|Seu nome do usuário da infraestrutura no IBM Cloud
|`apikey`|Sua chave API da infraestrutura no IBM Cloud
|`region`|A região do IBM Cloud em que o Vyatta está implementado
|`inf_name_private`|O nome da interface privada do Vyatta
|`inf_name_public`|O nome da interface pública do Vyatta
|`gatewayid`|O ID do gateway do Vyatta
|`vlans`|Lista de VLANs de segmento de perímetro seguro que contêm o tipo, o número de VLAN e o ID de VLAN
|`vyatta_gateway_vip`|O VIP do gateway
|`vyatta_primary`|Objeto que contém o IP privado e público do membro primário do Vyatta
|`vyatta_secondary`|Objeto que contém o IP privado e público do membro secundário do Vyatta
{: caption="Tabela 1. <code>config.json</code>" caption-side="top"}

## Referência do `rules.conf`
{: #spn_reference_rules_conf}

|Chave|Descrição
|---|-------------|---|
|`external_subnets`|Lista de sub-redes na Internet pública que podem ser usadas pelo Perímetro seguro
|`external_ports`|Lista de portas que podem ser usadas pelo Perímetro seguro
|`userips`|Lista de IPs do usuário a serem inseridos na lista de desbloqueio para o perímetro seguro
{: caption="Tabela 2. <code>rules.conf</code>" caption-side="top"}
