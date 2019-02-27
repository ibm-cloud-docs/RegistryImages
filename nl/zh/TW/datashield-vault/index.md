---

copyright:
  years: 2018, 2019
lastupdated: "2019-01-03"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# 開始使用 `datashield-vault` 映像檔
{: #datashield-vault_starter}

此容器映像檔會在 Data Shield 環境中執行 Vault，並為使用中的資料提供保護。
{:shortdesc}

您可以使用指令行存取 {{site.data.keyword.IBM}} 所提供的映像檔，請參閱 [IBM 公用映像檔](/docs/services/Registry/registry_public_images.html#public_images)。
{: tip}

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

請使用 `kubectl get pod -owide` 或 `kubectl describe pod` 來查閱 Vault 實例的節點 IP 位址。請將 `VAULT_ADDR` 環境變數設為 `http://<IP>:8200` 以存取 Data Shield 保護的 Vault 實例。

您可以使用 Vault CLI 指令，例如 `vault init`、`vault unseal`、`vault auth`、`vault write` 及 `vault read` 以鑑別和存取密碼。如需如何使用 Vault CLI 的相關資訊，請參閱 [Vault Commands (CLI) ![外部鏈結圖示](../../../icons/launch-glyph.svg "外部鏈結圖示")](https://www.vaultproject.io/docs/commands/index.html)。
