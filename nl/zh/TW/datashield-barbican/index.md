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

# 開始使用 `datashield-barbican` 映像檔
{: #datashield-barbican_starter}

此容器映像檔會在 Data Shield 環境中執行 Barbican，並為使用中的資料提供保護。
{:shortdesc}

您可以使用指令行存取 {{site.data.keyword.IBM}} 所提供的映像檔，請參閱 [IBM 公用映像檔](/docs/services/Registry/registry_public_images.html#public_images)。
{: tip}

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

容器部署及啟動可能需要幾分鐘的時間。展示的 Barbican 實例已配置為不使用 Keystone 鑑別。要求必須包含標頭 `X-Project-Id: 12345`
{: tip}

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
