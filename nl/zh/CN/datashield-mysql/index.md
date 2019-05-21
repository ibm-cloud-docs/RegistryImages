---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-09"

keywords: IBM Cloud Container Registry, Data Shield, data in use, memory encryption, intel sgx, fortanix, mysql image, mariaDB, container image, public image

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

# 开始使用 `datashield-mariadb` 映像
{: #datashield-mariadb_starter}

此容器映像为在 {{site.data.keyword.datashield_short}} 环境中运行 MariaDB 所使用的数据提供保护。有关服务和保护“使用中的数据”的含义的更多信息，请参阅 [{{site.data.keyword.datashield_short}} 文档](/docs/services/data-shield?topic=data-shield-about#about)。
{: shortdesc}

您可以使用命令行来访问 {{site.data.keyword.IBM}} 提供的映像，请参阅 [IBM 公共映像](/docs/services/Registry?topic=registry-public_images#public_images)。
{: tip}

## 部署映像
{: #datashield-mariadb-deploy}

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
    image: <IMAGE_NAME>
    env:
      name: MARIADB_ROOT_PASSWORD
      value: <ROOT_PASSWORD>
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
      name: mariadb
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
<caption>表 1. 必需的输入变量</caption>
  <tr>
    <th>变量</th>
    <th>描述</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>要部署的映像的名称。</td>
  </tr>
    <tr>
    <td><code>MARIADB_ROOT_PASSWORD</code></td>
    <td>为 MariaDB root 用户设置密码。</td>
  </tr>
  <tr>
    <td><code>ROOT_PASSWORD</code></td>
    <td>要为 MariaDB root 用户设置的密码。</td>
  </tr>
</table>

### 接受的环境变量
{: #datashield-mariadb-variables}

MariaDB 容器还接受以下环境变量。要使用其他变量，请将其添加到规范中，如以下示例所示：

```
spec:
  containers:
  - name: data-shield-mariadb
    image: <IMAGE_NAME>
    env:
      name: MARIADB_ROOT_PASSWORD
      value: <ROOT_PASSWORD>
      name: MARIADB_USER
      value: <USER_NAME>
```
{: screen}

<table>
<caption>表 2. MariaDB 容器接受的环境变量</caption>
  <tr>
    <th>变量</th>
    <th>描述</th>
  </tr>
  <tr>
    <td><code>MARIADB_ALLOW_EMPTY_PASSWORD</code></td>
    <td>允许数据库以 root 用户的空密码开始。不建议。</td>
  </tr>
  <tr>
    <td><code>MARIADB_RANDOM_ROOT_PASSWORD</code></td>
    <td>为 MariaDB root 用户生成随机密码。</td>
  </tr>
  <tr>
    <td><code>MARIADB_USER</code></td>
    <td>创建具有指定用户名的常规 MariaDB 用户。</td>
  </tr>
  <tr>
    <td><code>MARIADB_PASSWORD</code></td>
    <td><code>MYSQL_USER</code> 的密码。</td>
  </tr>
  <tr>
    <td><code>MARIADB_DATABASE</code></td>
    <td>创建具有指定名称的空数据库。</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_MODE</code></td>
    <td>启用复制。选项包括 <code>master</code> 或 <code>slave</code>。</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_USER</code>（master 或 slave）</td>
    <td>设置复制用户的名称。此用户在主服务器上创建，由从属服务器在连接时使用。</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_PASSWORD</code>（master 或 slave）</td>
    <td>为复制用户设置密码。此密码在主服务器上用于创建用户，由从属服务器在登录时使用。</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_MASTER_HOST</code> (slave)</td>
    <td>设置要连接到的主机名以进行复制。</td>
  </tr>
</table>

## 连接到 MariaDB 实例
{: #datashield-mariadb-connect}

您可以通过运行以下命令连接到 MariaDB 实例：

```
mariadb -h <node> --protocol tcp -uroot
```
{: codeblock}

{{site.data.keyword.datashield_short}} MariaDB 映像要求客户机连接使用传输层安全性 (TLS)。您可能需要将 `--ssl` 或 `--ssl-mode require` 添加到客户机命令行，具体取决于您的客户机版本。
{: note}
