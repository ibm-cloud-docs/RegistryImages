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

# 开始使用 `datashield-barbican` 映像
{: #datashield-barbican_starter}

此容器映像会在 Data Shield 环境中运行 Barbican，为使用中的数据提供保护。
{:shortdesc}

您可以使用命令行来访问 {{site.data.keyword.IBM}} 提供的映像，请参阅 [IBM 公共映像](/docs/services/Registry/registry_public_images.html#public_images)。
{: tip}

您可以使用以下 Kubernetes pod 规范来部署映像：

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

容器的部署和启动可能需要几分钟才能完成。Barbican 演示实例是在没有 Keystone 认证的情况下配置的。请求必须包含头 `X-Project-Id: 12345`
{: tip}

您可以通过运行以下命令在 Barbican 演示实例中创建一个私钥：

```
    curl -sS -X POST -H 'content-type:application/json' -H 'X-Project-Id: 12345' -d \
        '{"payload": "my-secret-here", "payload_content_type": "text/plain"}' \
        http://<node>:9311/v1/secrets
```
{: pre}
    
响应包含所创建私钥的 URL。您可以通过运行以下命令来检索该私钥：

```
    curl -sS -H 'X-Project-Id: 12345' <url>
```
{: pre}
