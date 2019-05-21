---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-09"

keywords: IBM Cloud Container Registry, Data Shield environment, container image, public image, vault image, data in use, memory encryption, intel sgx, fortanix,

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

# `datashield-vault` イメージの概説
{: #datashield-vault_starter}

このコンテナー・イメージは、{{site.data.keyword.datashield_short}} 環境で Vault を実行することにより使用されるデータを保護します。このサービスについて、および「使用されるデータ」を保護することの意味について詳しくは、[{{site.data.keyword.datashield_short}} の資料](/docs/services/data-shield?topic=data-shield-about#about)を参照してください。
{: shortdesc}

{{site.data.keyword.IBM}} によって提供されるイメージには、コマンド・ラインを使用してアクセスできます。[IBM のパブリック・イメージ](/docs/services/Registry?topic=registry-public_images#public_images)を参照してください。
{: tip}


## イメージのデプロイ
{: #datashield-vault-deploy}

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
    image: <IMAGE_NAME>
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

<table>
<caption>表 1. 必須の入力変数</caption>
  <tr>
    <th>変数</th>
    <th>説明</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>デプロイするイメージの名前。</td>
  </tr>
</table>

保護された Vault のインスタンスにアクセスするには、以下のようにします。

1. 以下のいずれかのコマンドを実行して、インスタンスのノード IP アドレスを検索します。

  * オプション 1:

    ```
    kubectl get pod -owide
    ```
    {: codeblock}

  * オプション 2:
    ```
    kubectl describe pod
    ```
    {: codeblock}

2. `VAULT_ADDR` 環境変数を `http://<YOUR_IP>:8200` に設定します。
  

Vault インスタンスのノード IP アドレスを検索するには、`kubectl get pod -owide` または `kubectl describe pod` を使用します。 {{site.data.keyword.datashield_short}} で保護された Vault インスタンスにアクセスするには、`VAULT_ADDR` 環境変数を `http://<IP>:8200` に設定します。

`vault init`、`vault unseal`、`vault auth`、`vault write`、`vault read` のような Vault CLI コマンドを使用して、秘密を認証してアクセスできます。 Vault CLI の使用方法について詳しくは、[Vault Commands (CLI) ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.vaultproject.io/docs/commands/index.html) を参照してください。
{: tip}
