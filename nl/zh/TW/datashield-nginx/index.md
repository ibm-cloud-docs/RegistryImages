---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-27"

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

# 開始使用 `datashield-nginx` 映像檔
{: #datashield-nginx_starter}

此容器映像檔藉由在 Data Shield 環境中執行 NGINX，而為使用中的資料提供保護。如需服務的相關資訊及保護「使用中的資料」有何涵義，請參閱 [IBM Cloud Data Shield 文件](/docs/services/data-shield?topic=data-shield-about#about)。
{: shortdesc}

您可以使用指令行存取 {{site.data.keyword.IBM}} 所提供的映像檔，請參閱 [IBM 公用映像檔](/docs/services/Registry?topic=registry-public_images#public_images)。
{: tip}

## 部署映像檔
{: #datashield-nginx-deploy}

您可以使用下列 Kubernetes Pod 規格來部署映像檔：

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
{: pre}

<table>
<caption>表 1. 必要的輸入變數</caption>
  <tr>
    <th>變數</th>
    <th>說明</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>您要部署的映像檔名稱。</td>
  </tr>
</table>

若要提供您自己的內容讓 NGINX 處理，請將它放在容器裡的 `/usr/local/nginx/html` 下。
{: tip}
