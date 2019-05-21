---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-09"

keywords: IBM Cloud Container Registry, Data Shield environment, datashield-barbican image, container image, barbican, Registry, data in use, memory encryption, intel sgx, fortanix,

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

# Introdução à imagem `datashield-barbican`
{: #datashield-barbican_starter}

Essa imagem de contêiner fornece proteção para os dados que estão em uso por meio da execução do Barbican no ambiente do {{site.data.keyword.datashield_short}}. Para obter mais informações sobre o serviço e o que significa proteger "dados em uso", consulte a [Documentação do {{site.data.keyword.datashield_short}}](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

É possível acessar as imagens que são fornecidas pela {{site.data.keyword.IBM}} usando a linha de comandos. Consulte [Imagens públicas da IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Implementando a imagem
{: #datashield-barbican-deploy}

É possível usar a especificação de pod do Kubernetes a seguir para implementar a imagem:

```
apiVersion: v1
    kind: Pod
    metadata:
      name: data-shield-barbican
      labels:
        app: data-shield-barbican
    spec:
      containers:
      - name: data-shield-barbican
        image: <IMAGE_NAME>
        volumeMounts:
        - mountPath: /dev/isgx
          name: isgx
        - mountPath: /dev/gsgx
          name: gsgx
        - mountPath: /var/run/aesmd/aesm.socket
          name: aesm-socket
        ports:
        - containerPort: 9311
          hostPort: 9311
          name: barbican
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
{: codeblock}

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

Pode levar alguns minutos para que o contêiner seja implementado e iniciado. A instância de demonstração do Barbican é
configurada sem a autenticação Keystone. As solicitações devem incluir o cabeçalho `X-Project-Id: 12345`.
{: tip}

## Criando um segredo
{: #datashield-barbican-secret}

É possível criar um segredo na instância de demonstração do Barbican executando o comando a seguir:

```
curl -sS -X POST -H 'content-type:application/json' -H 'X-Project-Id: 12345' -d \
        '{"payload": "my-secret-here", "payload_content_type": "text/plain"}' \
        http://<node>:9311/v1/secrets
```
{: codeblock}
    
A resposta contém a URL para o segredo criado. É possível recuperar o segredo executando o comando a seguir:

```
curl -sS -H 'X-Project-Id: 12345' <url>
```
{: codeblock}
