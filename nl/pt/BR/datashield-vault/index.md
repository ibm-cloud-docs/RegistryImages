---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-13"

keywords: IBM Cloud Container Registry, Data Shield environment, container image, public image, vault image, data in use, memory encryption, intel sgx, fortanix,

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

# Introdução à imagem `datashield-vault`
{: #datashield-vault_starter}

Essa imagem de contêiner fornece proteção para os dados em uso executando o Vault no ambiente do Data Shield. Para obter mais informações sobre o serviço e o que significa proteger os "dados em uso", consulte a [Documentação do IBM Cloud Data Shield](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

É possível acessar as imagens que são fornecidas pela {{site.data.keyword.IBM}} usando a linha de comandos. Consulte [Imagens públicas da IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}


## Implementando a imagem
{: #datashield-vault-deploy}

É possível usar a seguinte especificação de pod do Kubernetes para implementar a imagem:

```
    apiVersion: v1
    kind: Pod
    metadata:
      name: data-shield-vault
      labels:
        app: data-shield-vault
    spec:
      containers:
      - name: data-shield-vault
        image: <IMAGE_NAME>
        volumeMounts:
        - mountPath: /dev/isgx
          name: isgx
        - mountPath: /dev/gsgx
          name: gsgx
        - mountPath: /var/run/aesmd/aesm.socket
          name: aesm-socket
        ports:
        - containerPort: 8200
          hostPort: 8200
          name: vault
          protocol: TCP
      volumes:
      - name: isgx
        hostPath:
          path: /dev/isgx
      - name: gsgx
        hostPath:
          path: /dev/gsgx
      - name: aesm-socket
        hostPath:
          path: /var/run/aesmd/aesm.socket
```
{: pre}

<table>
<caption>Tabela 1. Variáveis de entrada necessárias</caption>
  <tr>
    <th>Consolidação</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>O nome da imagem que você deseja implementar.</td>
  </tr>
</table>

Para acessar a instância protegida do Vault:

1. Consulte o endereço IP do nó da sua instância executando um dos comandos a seguir.

  * Opção 1:

    ```
    kubectl get pod -owide
    ```
    {: pre}

  * Opção 2:
    ```
    kubectl describe pod
    ```
    {: pre}

2. Configure a variável de ambiente `VAULT_ADDR` para `http://<YOUR_IP>:8200`.
  

Use `kubectl get pod -owide` ou `kubectl describe pod` para consultar o endereço IP do nó para a instância da área segura. Configure a variável de ambiente `VAULT_ADDR` para `http://<IP>:8200` para acessar a instância do Vault protegida pelo Data Shield.

É possível usar comandos da CLI do Vault, como `vault init`, `vault unseal`, `vault auth`, `vault write` e `vault read`, para autenticar e acessar segredos. Para obter mais informações sobre como usar a CLI da área segura, consulte [Comando da área segura (CLI) ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://www.vaultproject.io/docs/commands/index.html).
{: tip}
