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

# `datashield-vault` イメージの概説
{: #datashield-vault_starter}


このコンテナー・イメージは、データ・シールド環境で Vault を実行し、使用中のデータを保護します。
{:shortdesc}

{{site.data.keyword.IBM}} によって提供されるイメージには、コマンド・ラインを使用してアクセスできます。[IBM のパブリック・イメージ](/docs/services/Registry/registry_public_images.html#public_images)を参照してください。
{: tip}

Kubernetes ポッドの次の仕様を使用して、イメージをデプロイできます。

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
    
Vault インスタンスのノード IP アドレスを検索するには、`kubectl get pod -owide` または `kubectl describe pod` を使用します。 データ・シールドで保護された Vault インスタンスにアクセスするには、`VAULT_ADDR` 環境変数を `http://<IP>:8200` に設定します。

`vault init`、`vault unseal`、`vault auth`、`vault write`、`vault read` のような Vault CLI コマンドを使用して、秘密を認証してアクセスできます。 Vault CLI の使用方法について詳しくは、[Vault Commands (CLI) ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.vaultproject.io/docs/commands/index.html) を参照してください。
