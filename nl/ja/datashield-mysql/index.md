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

# `datashield-mysql` イメージの概説
{: #datashield-mysql_starter}

このコンテナー・イメージは、データ・シールド環境で MariaDB を実行し、使用中のデータを保護します。
{:shortdesc}

{{site.data.keyword.IBM}} によって提供されるイメージには、コマンド・ラインを使用してアクセスできます。[IBM のパブリック・イメージ](/docs/services/Registry/registry_public_images.html#public_images)を参照してください。
{: tip}

Kubernetes ポッドの次の仕様を使用して、イメージをデプロイできます。

```
    apiVersion: v1
    kind: Pod
    metadata:
      name: data-shield-mariadb
      labels:
        app: data-shield-mariadb
    spec:
      containers:
      - name: data-shield-mariadb
        image: <TODO INSERT APPROPRIATE IMAGE NAME HERE>
        volumeMounts:
        - mountPath: /dev/isgx
          name: isgx
        - mountPath: /dev/gsgx
          name: gsgx
        - mountPath: /var/run/aesmd/aesm.socket
          name: aesm-socket
        ports:
        - containerPort: 3306
          hostPort: 3306
          name: mysql
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
    
次のコマンドを実行して、MariaDB インスタンスに接続することができます。

```
    mysql -h <node> --protocol tcp -uroot
```
{: pre}

データ・シールド MariaDB イメージでは、クライアント接続は TLS を使用する必要があります。 ご使用のクライアント・バージョンによっては、`--ssl` または `--ssl-mode require` をクライアントのコマンド・ラインに追加しなければならないことがあります。
