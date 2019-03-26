---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-26"

keywords: IBM Cloud Container Registry, Data Shield environment, mysql image, container image, public image

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

# `datashield-mysql` イメージの概説
{: #datashield-mysql_starter}

このコンテナー・イメージは、データ・シールド環境で MariaDB を実行し、データを保護します。
{:shortdesc}

{{site.data.keyword.IBM}} によって提供されるイメージには、コマンド・ラインを使用してアクセスできます。[IBM のパブリック・イメージ](/docs/services/Registry?topic=registry-public_images#public_images)を参照してください。
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

MariaDB コンテナーで受け入れる環境変数を確認するには、次の表を調べてください。

<table>
<caption>表 1. MariaDB コンテナーで受け入れる環境変数</caption>
  <tr>
    <th>変数</th>
    <th>説明</th>
  </tr>
  <tr>
    <td><code>MYSQL_ROOT_PASSWORD</code></td>
    <td>MariaDB root ユーザーのパスワードを設定します。</td>
  </tr>
  <tr>
    <td><code>MYSQL_ALLOW_EMPTY_PASSWORD</code></td>
    <td>root ユーザーのパスワードが空でもデータベースを始動することを許可します。 推奨されません。</td>
  </tr>
  <tr>
    <td><code>MYSQL_RANDOM_ROOT_PASSWORD</code></td>
    <td>MariaDB root ユーザーのランダム・パスワードを生成します。</td>
  </tr>
  <tr>
    <td><code>MYSQL_USER</code></td>
    <td>指定されたユーザー名で通常の MariaDB ユーザーを作成します。</td>
  </tr>
  <tr>
    <td><code>MYSQL_PASSWORD</code></td>
    <td><code>MYSQL_USER</code> のパスワード。</td>
  </tr>
  <tr>
    <td><code>MYSQL_DATABASE</code></td>
    <td>指定された名前で空のデータベースを作成します。</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MODE</code></td>
    <td>レプリケーションを使用可能にします。 オプションには、<code>master</code> と <code>slave</code> があります。</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_USER</code> (master または slave)</td>
    <td>レプリケーション・ユーザーの名前を設定します。 このユーザーは master で作成され、slave によって接続のために使用されます。</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_PASSWORD</code> (master または slave)</td>
    <td>レプリケーション・ユーザーのパスワードを設定します。 このパスワードは、ユーザーを作成するために master で使用され、slave によってログインのために使用されます。</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MASTER_HOST</code> (slave)</td>
    <td>レプリケーション用の接続先のホスト名を設定します。</td>
  </tr>
</table>
