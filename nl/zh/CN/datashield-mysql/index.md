---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-21"

keywords: mysql image, Data Shield environment, container image, public image

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

# 开始使用 `datashield-mysql` 映像
{: #datashield-mysql_starter}

此容器映像会在 Data Shield 环境中运行 MariaDB，为使用中的数据提供保护。
{:shortdesc}

您可以使用命令行来访问 {{site.data.keyword.IBM}} 提供的映像，请参阅 [IBM 公共映像](/docs/services/Registry?topic=registry-public_images#public_images)。
{: tip}

您可以使用以下 Kubernetes pod 规范来部署映像：

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

您可以通过运行以下命令连接到 MariaDB 实例：

```
    mysql -h <node> --protocol tcp -uroot
```
{: pre}

Data Shield MariaDB 映像要求客户机连接必须使用 TLS。您可能需要将 `--ssl` 或 `--ssl-mode require` 添加到客户机命令行，具体取决于您的客户机版本。

有关 MariaDB 容器接受的环境变量，请参阅下表：

<table>
<caption>表 1. MariaDB 容器接受的环境变量</caption>
  <tr>
    <th>变量</th>
    <th>描述</th>
  </tr>
  <tr>
    <td><code>MYSQL_ROOT_PASSWORD</code></td>
    <td>为 MariaDB root 用户设置密码。</td>
  </tr>
  <tr>
    <td><code>MYSQL_ALLOW_EMPTY_PASSWORD</code></td>
    <td>允许数据库以 root 用户的空密码开始。不建议。</td>
  </tr>
  <tr>
    <td><code>MYSQL_RANDOM_ROOT_PASSWORD</code></td>
    <td>为 MariaDB root 用户生成随机密码。</td>
  </tr>
  <tr>
    <td><code>MYSQL_USER</code></td>
    <td>创建具有指定用户名的常规 MariaDB 用户。</td>
  </tr>
  <tr>
    <td><code>MYSQL_PASSWORD</code></td>
    <td><code>MYSQL_USER</code> 的密码。</td>
  </tr>
  <tr>
    <td><code>MYSQL_DATABASE</code></td>
    <td>创建具有指定名称的空数据库。</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MODE</code></td>
    <td>启用复制。选项包括 <code>master</code> 或 <code>slave</code>。</td>
  </tr>
  <tr>
    <td><code>YSQL_REPLICATION_USER</code>（master 或 slave）</td>
    <td>设置复制用户的名称。此用户在主服务器上创建，由从属服务器在连接时使用。</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_PASSWORD</code>（master 或 slave）</td>
    <td>为复制用户设置密码。此密码在主服务器上用于创建用户，由从属服务器在登录时使用。</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MASTER_HOST</code> (slave)</td>
    <td>设置要连接到的主机名以进行复制。</td>
  </tr>
</table>
