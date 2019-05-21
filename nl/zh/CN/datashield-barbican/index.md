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

# 开始使用 `datashield-barbican` 映像
{: #datashield-barbican_starter}

此容器映像为在 {{site.data.keyword.datashield_short}} 环境中运行 Barbican 所使用的数据提供保护。有关服务和保护“使用中的数据”的含义的更多信息，请参阅 [{{site.data.keyword.datashield_short}} 文档](/docs/services/data-shield?topic=data-shield-about#about)。
{: shortdesc}

您可以使用命令行来访问 {{site.data.keyword.IBM}} 提供的映像，请参阅 [IBM 公共映像](/docs/services/Registry?topic=registry-public_images#public_images)。
{: tip}

## 部署映像
{: #datashield-barbican-deploy}

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
<caption>表 1. 必需的输入变量</caption>
  <tr>
    <th>变量</th>
    <th>描述</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>要部署的映像的名称。</td>
  </tr>
</table>

容器的部署和启动可能需要几分钟才能完成。Barbican 演示实例是在没有 Keystone 认证的情况下配置的。请求必须包含头 `X-Project-Id: 12345`。
{: tip}

## 创建私钥
{: #datashield-barbican-secret}

您可以通过运行以下命令在 Barbican 演示实例中创建一个私钥：

```
    curl -sS -X POST -H 'content-type:application/json' -H 'X-Project-Id: 12345' -d \
        '{"payload": "my-secret-here", "payload_content_type": "text/plain"}' \
        http://<node>:9311/v1/secrets
```
{: codeblock}
    
响应包含所创建私钥的 URL。您可以通过运行以下命令来检索该私钥：

```
    curl -sS -H 'X-Project-Id: 12345' <url>
```
{: codeblock}
