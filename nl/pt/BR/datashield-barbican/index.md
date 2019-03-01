---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-21"

keywords: datashield-barbican image, container image, barbican, Data Shield environment, public image

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

Essa imagem de contêiner é executada em Barbican no ambiente Data Shield, o que fornece proteção aos dados que estão em
uso.
{:shortdesc}

É possível acessar as imagens que são fornecidas pela {{site.data.keyword.IBM}} usando a linha de comandos. Consulte [Imagens públicas da IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

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
        image: <TODO INSERT APPROPRIATE IMAGE NAME HERE>
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

Pode levar alguns minutos para que o contêiner seja implementado e iniciado. A instância de demonstração do Barbican é
configurada sem a autenticação Keystone. A solicitação deve incluir o cabeçalho `X-Project-Id: 12345`
{: tip}

É possível criar um segredo na instância de demonstração do Barbican executando o comando a seguir:

```
    curl -sS -X POST -H 'content-type:application/json' -H 'X-Project-Id: 12345' -d \
        '{"payload": "my-secret-here", "payload_content_type": "text/plain"}' \
        http://<node>:9311/v1/secrets
```
{: pre}
    
A resposta contém a URL para o segredo criado. É possível recuperar o segredo executando o comando a seguir:

```
    curl -sS -H 'X-Project-Id: 12345' <url>
```
{: pre}
