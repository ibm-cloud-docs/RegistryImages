---

copyright:
  years: 2018
lastupdated: "2018-10-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip} 
{:table: .aria-labeledby="caption"}

# Introdução à imagem `datashield-barbican`
{: #datashield-barbican_starter}

Essa imagem de contêiner é executada em Barbican no ambiente Data Shield, fornecendo proteção aos dados em uso.
{:shortdesc}

É possível acessar as imagens que são fornecidas pela {{site.data.keyword.IBM}} usando a linha de comandos. Consulte [Imagens públicas da IBM](/docs/services/Registry/registry_public_images.html#public_images).
{: tip}

É possível usar a seguinte especificação de pod do Kubernetes para implementar a imagem:

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

Pode levar alguns minutos para que o contêiner seja implementado e iniciado. A instância demo do Barbican é configurada sem a autenticação Keystone. A solicitação deve incluir o cabeçalho `X-Project-Id: 12345`
{: tip}

É possível criar um segredo na instância demo do Barbican executando o comando a seguir:

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
