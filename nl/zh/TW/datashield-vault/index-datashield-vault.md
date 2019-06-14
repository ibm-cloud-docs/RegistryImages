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

# 開始使用 `datashield-vault` 映像檔
{: #datashield-vault_starter}

此容器映像檔藉由在 {{site.data.keyword.datashield_short}} 環境中執行 Vault，而為使用中的資料提供保護。如需服務的相關資訊及保護「使用中的資料」有何涵義，請參閱 [{{site.data.keyword.datashield_short}} 文件](/docs/services/data-shield?topic=data-shield-about#about)。
{: shortdesc}

您可以使用指令行存取 {{site.data.keyword.IBM}} 所提供的映像檔，請參閱 [IBM 公用映像檔](/docs/services/Registry?topic=registry-public_images#public_images)。
{: tip}

## 部署映像檔
{: #datashield-vault-deploy}

您可以使用下列 Kubernetes Pod 規格來部署映像檔：

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
<caption>表 1. 必要的輸入變數</caption>
  <tr>
    <th>變數</th>
    <th>說明</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>您要部署的映像檔名稱。</td>
  </tr>
  <tr>
    <td><code>NODE-IP</code></td>
    <td>Vault 實例的節點 IP 位址。若要尋找 IP 位址，您可以使用 <code>kubectl get pod -owide</code> 或 <code>kubectl describe pod</code>。</td>
  </tr>
</table>

## 存取 Vault 的受保護實例
{: #datashield-vault-access}

若要存取受保護的 Vault 實例，請完成下列步驟：

1. 執行下列其中一個指令，查閱實例的節點 IP 位址。

   * 選項 1：

   ```
   kubectl get pod -owide
   ```
   {: codeblock}

   * 選項 2：
  
   ```
   kubectl describe pod
   ```
   {: codeblock}

2. 將 `VAULT_ADDR` 環境變數設為 `http://<YOUR_IP>:8200`。

您可以使用 Vault CLI 指令，例如 `vault init`、`vault unseal`、`vault auth`、`vault write` 及 `vault read` 以鑑別和存取密碼。如需如何使用 Vault CLI 的相關資訊，請參閱 [Vault Commands (CLI) ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://www.vaultproject.io/docs/commands/index.html)。
{: tip}
