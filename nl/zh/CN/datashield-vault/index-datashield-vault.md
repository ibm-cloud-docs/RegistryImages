---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-03"

keywords: IBM Cloud Container Registry, Data Shield environment, container image, public image, Vault image, data in use, memory encryption, Intel SGX, Fortanix,

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

# 开始使用 `datashield-vault` 映像
{: #datashield-vault_starter}

此容器映像为在 {{site.data.keyword.datashield_short}} 环境中运行 Vault 所使用的数据提供保护。有关服务和保护“使用中的数据”的含义的更多信息，请参阅 [{{site.data.keyword.datashield_short}} 文档](/docs/services/data-shield?topic=data-shield-about#about)。
{: shortdesc}

您可以使用命令行来访问 {{site.data.keyword.IBM}} 提供的映像，请参阅 [IBM 公共映像](/docs/services/Registry?topic=registry-public_images#public_images)。
{: tip}

## 部署映像
{: #datashield-vault-deploy}

您可以使用以下 Kubernetes pod 规范来部署映像：

```
apiVersion: v1
kind: Pod
metadata:
  name: data-shield-vault
  labels:
    app: data-shield-vault
spec:
  containers:
  - name: data-shield-vault
    image: <IMAGE-NAME>
    volumeMounts:
    - mountPath: /dev/isgx
      name: isgx
    - mountPath: /dev/gsgx
      name: gsgx
    - mountPath: /var/run/aesmd/aesm.socket
      name: aesm-socket
    ports:
    - containerPort: 8200
      name: vault
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
  name: data-shield-vault
  labels:
    app: data-shield-vault
spec:
  type: LoadBalancer
  selector:
    app: data-shield-vault
  ports:
    - name: vault
      port: 8200
      targetPort: 8200
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
    <td><code>NODE-IP</code></td>
    <td>Vault 实例的节点 IP 地址。要查找 IP 地址，您可以使用 <code>kubectl get pod -owide</code> 或 <code>kubectl describe pod</code>。</td>
  </tr>
</table>

## 访问 Vault 的受保护实例
{: #datashield-vault-access}

要访问 Vault 的受保护实例，请完成以下步骤：

1. 通过运行以下命令之一查找实例的节点 IP 地址。

   * 选项 1：

   ```
    kubectl get pod -owide
    ```
   {: codeblock}

   * 选项 2：
    
  
   ```
    kubectl describe pod
    ```
   {: codeblock}

2. 将 `VAULT_ADDR` 环境变量设置为 `http://<YOUR_IP>:8200`。

您可以使用 Vault CLI 命令（例如 `vault init`、`vault unseal`、`vault auth`、`vault write` 和 `vault read`）来认证和访问私钥。有关如何使用 Vault CLI 的更多信息，请参阅 [Vault Commands (CLI) ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://www.vaultproject.io/docs/commands/index.html)。
{: tip}
