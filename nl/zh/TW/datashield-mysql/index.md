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

# 開始使用 `datashield-mysql` 映像檔
{: #datashield-mysql_starter}

此容器映像檔會在 Data Shield 環境中執行 MariaDB，並為使用中的資料提供保護。
{:shortdesc}

您可以使用指令行存取 {{site.data.keyword.IBM}} 所提供的映像檔，請參閱 [IBM 公用映像檔](/docs/services/Registry?topic=registry-public_images#public_images)。
{: tip}

您可以使用下列 Kubernetes Pod 規格來部署映像檔：

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

您可以執行下列指令來連接至 MariaDB 實例：

```
    mysql -h <node> --protocol tcp -uroot
```
{: pre}

Data Shield MariaDB 映像檔需要用戶端連線使用 TLS。視您的用戶端版本而定，您可能需要在用戶端指令行新增 `--ssl` 或 `--ssl-mode require`。

請參閱下表以查看 MariaDB 容器接受的環境變數：

<table>
<caption>表 1. MariaDB 容器接受的環境變數</caption>
  <tr>
    <th>變數</th>
    <th>說明</th>
  </tr>
  <tr>
    <td><code>MYSQL_ROOT_PASSWORD</code></td>
    <td>設定 MariaDB root 使用者的密碼。</td>
  </tr>
  <tr>
    <td><code>MYSQL_ALLOW_EMPTY_PASSWORD</code></td>
    <td>容許資料庫在一開始為 root 使用者採用空的密碼。不建議使用。</td>
  </tr>
  <tr>
    <td><code>MYSQL_RANDOM_ROOT_PASSWORD</code></td>
    <td>為 MariaDB root 使用者產生隨機密碼。</td>
  </tr>
  <tr>
    <td><code>MYSQL_USER</code></td>
    <td>使用指定的使用者名稱建立一般 MariaDB 使用者。</td>
  </tr>
  <tr>
    <td><code>MYSQL_PASSWORD</code></td>
    <td><code>MYSQL_USER</code> 的密碼。</td>
  </tr>
  <tr>
    <td><code>MYSQL_DATABASE</code></td>
    <td>使用指定的名稱建立空的資料庫。</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MODE</code></td>
    <td>啟用抄寫。選項包括 <code>master</code> 或 <code>slave</code>。</td>
  </tr>
  <tr>
    <td><code>YSQL_REPLICATION_USER</code>（主節點或從屬節點）</td>
    <td>設定抄寫使用者的名稱。這個使用者會建立在主節點上，並由從屬節點用來連接。</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_PASSWORD</code>（主節點或從屬節點）</td>
    <td>設定抄寫使用者的密碼。這個密碼用於在主節點上建立使用者，並由從屬節點用來登入。</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MASTER_HOST</code>（從屬節點）</td>
    <td>設定抄寫要連接至的主機名稱。</td>
  </tr>
</table>
