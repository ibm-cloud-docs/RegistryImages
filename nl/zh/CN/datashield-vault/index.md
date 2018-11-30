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

# 开始使用 `datashield-vault` 映像
{: #datashield-vault_starter}


此容器映像会在 Data Shield 环境中运行 Vault，为使用中的数据提供保护。
{:shortdesc}

您可以使用命令行来访问 {{site.data.keyword.IBM}} 提供的映像，请参阅 [IBM 公共映像](/docs/services/Registry/registry_public_images.html#public_images)。
{: tip}

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
        image: <TODO INSERT APPROPRIATE IMAGE NAME HERE>
        volumeMounts:
        - mountPath: /dev/isgx
          name: isgx
        - mountPath: /dev/gsgx
          name: gsgx
        - mountPath: /var/run/aesmd/aesm.socket
          name: aesm-socket
        ports:
        - containerPort: 8200
          hostPort: 8200
          name: vault
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
    
使用 `kubectl get pod -owide` 或 `kubectl describe pod` 来查找 Vault 实例的节点 IP 地址。将 `VAULT_ADDR` 环境变量设置为 `http://<IP>:8200`，以访问受 Data Shield 保护的 Vault 实例。

您可以使用 Vault CLI 命令（例如 `vault init`、`vault unseal`、`vault auth`、`vault write` 和 `vault read`）来认证和访问私钥。有关如何使用 Vault CLI 的更多信息，请参阅 [Vault Commands (CLI) ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://www.vaultproject.io/docs/commands/index.html)。
