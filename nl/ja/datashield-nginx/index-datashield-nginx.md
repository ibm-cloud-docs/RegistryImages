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

# `datashield-nginx` イメージの概説
{: #datashield-nginx_starter}

このコンテナー・イメージは、{{site.data.keyword.datashield_short}} 環境で NGINX を実行することにより使用されるデータを保護します。 このサービスについて、および「使用されるデータ」を保護することの意味について詳しくは、[{{site.data.keyword.datashield_short}} の資料](/docs/services/data-shield?topic=data-shield-about#about)を参照してください。
{: shortdesc}

{{site.data.keyword.IBM}} によって提供されるイメージには、コマンド・ラインを使用してアクセスできます。[IBM のパブリック・イメージ](/docs/services/Registry?topic=registry-public_images#public_images)を参照してください。
{: tip}

## イメージのデプロイ
{: #datashield-nginx-deploy}

Kubernetes ポッドの次の仕様を使用して、イメージをデプロイできます。

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
<caption>表 1. 必須の入力変数</caption>
  <tr>
    <th>変数</th>
    <th>説明</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>デプロイするイメージの名前。</td>
  </tr>
  <tr>
    <td><code>NODE_IP</code></td>
    <td>NGINX インスタンスのノード IP アドレス。</td>
  </tr>
</table>

処理対象の NGINX の独自のコンテンツを提供するには、コンテナーの `/usr/local/nginx/html` の下に置きます。
{: tip}
