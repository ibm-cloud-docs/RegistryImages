---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-09"

keywords: IBM Cloud Container Registry, Data Shield environment, nginx image, container image, public image, data in use, memory encryption, intel sgx, fortanix,

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

# Introdução à imagem `datashield-nginx`
{: #datashield-nginx_starter}

Essa imagem de contêiner fornece proteção para os dados em uso ao executar o NGINX no ambiente do {{site.data.keyword.datashield_short}}. Para obter mais informações sobre o serviço e o que significa proteger "dados em uso", consulte a [Documentação do {{site.data.keyword.datashield_short}}](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

É possível acessar as imagens que são fornecidas pela {{site.data.keyword.IBM}} usando a linha de comandos. Consulte [Imagens públicas da IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Implementando a imagem
{: #datashield-nginx-deploy}

É possível usar a seguinte especificação de pod do Kubernetes para implementar a imagem:

```
apiVersion: v1
    kind: Pod
    metadata:
      name: data-shield-nginx
      labels:
        app: data-shield-nginx
    spec:
      containers:
      - name: data-shield-nginx
        image: <IMAGE_NAME>
        volumeMounts:
        - mountPath: /dev/isgx
          name: isgx
        - mountPath: /dev/gsgx
          name: gsgx
        - mountPath: /var/run/aesmd/aesm.socket
          name: aesm-socket
        ports:
        - containerPort: 80
          hostPort: 80
          name: http
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

Para fornecer seu próprio conteúdo para o NGINX entregar, coloque-o sob `/usr/local/nginx/html` no contêiner.
{: tip}
