---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-03"

keywords: IBM Cloud Container Registry, Data Shield environment, nginx image, container image, public image, data in use, memory encryption, Intel SGX, Fortanix,

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

# 开始使用 `datashield-nginx` 映像
{: #datashield-nginx_starter}

此容器映像为在 {{site.data.keyword.datashield_short}} 环境中运行 NGINX 所使用的数据提供保护。有关服务和保护“使用中的数据”的含义的更多信息，请参阅 [{{site.data.keyword.datashield_short}} 文档](/docs/services/data-shield?topic=data-shield-about#about)。
{: shortdesc}

您可以使用命令行来访问 {{site.data.keyword.IBM}} 提供的映像，请参阅 [IBM 公共映像](/docs/services/Registry?topic=registry-public_images#public_images)。
{: tip}

## 部署映像
{: #datashield-nginx-deploy}

您可以使用以下 Kubernetes pod 规范来部署映像：

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
    - containerPort: 443
      name: https
      protocol: TCP
    env:
    - name: NODE_IP
      valueFrom:
        fieldRef:
          fieldPath: status.hostIP
    - name: NODE_AGENT_BASE_URL
      value: http://$(NODE_IP):9092/v1
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
---
apiVersion: v1
kind: Service
metadata:
  name: data-shield-nginx
  labels:
    app: data-shield-nginx
spec:
  type: LoadBalancer
  selector:
    app: data-shield-nginx
  ports:
    - name: https
      port: 443
      targetPort: 443
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
  <tr>
    <td><code>NODE_IP</code></td>
    <td>NGINX 实例的节点 IP 地址。</td>
  </tr>
</table>

要提供您自己的内容供 NGINX 来处理，请将其放置在容器中的 `/usr/local/nginx/html` 之下。
{: tip}
