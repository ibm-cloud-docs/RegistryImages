---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-13"

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

# 開始使用 `datashield-barbican` 映像檔
{: #datashield-barbican_starter}

此容器映像檔藉由在 Data Shield 環境中執行 Barbican，而為使用中的資料提供保護。如需服務的相關資訊及保護「使用中的資料」有何涵義，請參閱 [IBM Cloud Data Shield 文件](/docs/services/data-shield?topic=data-shield-about#about)。
{: shortdesc}

您可以使用指令行存取 {{site.data.keyword.IBM}} 所提供的映像檔，請參閱 [IBM 公用映像檔](/docs/services/Registry?topic=registry-public_images#public_images)。
{: tip}

## 部署映像檔
{: #datashield-barbican-deploy}

您可以使用下列 Kubernetes Pod 規格來部署映像檔：

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

容器部署及啟動可能需要幾分鐘的時間。展示的 Barbican 實例已配置為不使用 Keystone 鑑別。要求必須包含標頭 `X-Project-Id: 12345`。
{: tip}

## 建立密碼
{: #datashield-barbican-secret}

您可以執行下列指令，在 Barbican 展示實例中建立密碼：

```
    curl -sS -X POST -H 'content-type:application/json' -H 'X-Project-Id: 12345' -d \
        '{"payload": "my-secret-here", "payload_content_type": "text/plain"}' \
        http://<node>:9311/v1/secrets
```
{: pre}
    
回應包含所建立密碼的 URL。您可以執行下列指令來擷取密碼：

```
    curl -sS -H 'X-Project-Id: 12345' <url>
```
{: pre}
