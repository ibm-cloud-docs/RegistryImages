---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-13"

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

# `datashield-barbican` イメージの概説
{: #datashield-barbican_starter}

このコンテナー・イメージは、データ・シールド環境で Barbican を実行することにより使用されるデータを保護します。このサービスについて、および「使用されるデータ」を保護することの意味について詳しくは、[IBM Cloud Data Shield の資料](/docs/services/data-shield?topic=data-shield-about#about)を参照してください。
{: shortdesc}

{{site.data.keyword.IBM}} によって提供されるイメージには、コマンド・ラインを使用してアクセスできます。[IBM のパブリック・イメージ](/docs/services/Registry?topic=registry-public_images#public_images)を参照してください。
{: tip}

## イメージのデプロイ
{: #datashield-barbican-deploy}

Kubernetes ポッドの次の仕様を使用して、イメージをデプロイできます。

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

コンテナーのデプロイと開始には数分かかる場合があります。 Barbican デモンストレーション・インスタンスは、Keystone 認証なしで構成されます。 要求には、ヘッダー `X-Project-Id: 12345` を含める必要があります。
{: tip}

## 秘密の作成
{: #datashield-barbican-secret}

次のコマンドを実行して、Barbican デモンストレーション・インスタンスで秘密を作成することができます。

```
    curl -sS -X POST -H 'content-type:application/json' -H 'X-Project-Id: 12345' -d \
        '{"payload": "my-secret-here", "payload_content_type": "text/plain"}' \
        http://<node>:9311/v1/secrets
```
{: pre}
    
応答には、作成された秘密の URL が含まれています。 次のコマンドを実行して、秘密を取得することができます。

```
    curl -sS -H 'X-Project-Id: 12345' <url>
```
{: pre}
