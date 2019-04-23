---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-27"

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

# 開始使用 `datashield-mariadb` 映像檔
{: #datashield-mariadb_starter}

此容器映像檔藉由在 Data Shield 環境中執行 MariaDB，而為使用中的資料提供保護。如需服務的相關資訊及保護「使用中的資料」有何涵義，請參閱 [IBM Cloud Data Shield 文件](/docs/services/data-shield?topic=data-shield-about#about)。
{: shortdesc}

您可以使用指令行存取 {{site.data.keyword.IBM}} 所提供的映像檔，請參閱 [IBM 公用映像檔](/docs/services/Registry?topic=registry-public_images#public_images)。
{: tip}

## 部署映像檔
{: #datashield-mariadb-deploy}

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
{: pre}
  
<table>
<caption>表 1. 必要的輸入變數</caption>
  <tr>
    <th>變數</th>
    <th>說明</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>您要部署的映像檔名稱。</td>
  </tr>
    <tr>
    <td><code>MARIADB_ROOT_PASSWORD</code></td>
    <td>設定 MariaDB root 使用者的密碼。</td>
  </tr>
  <tr>
    <td><code>ROOT_PASSWORD</code></td>
    <td>您要為 MariaDB root 使用者設定的密碼。</td>
  </tr>
</table>

### 接受的環境變數
{: #datashield-mariadb-variables}

MariaDB 容器也接受下列環境變數。若要使用另一個變數，請將它們新增至規格中，如下列範例中所示：

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
<caption>表 2. MariaDB 容器接受的環境變數</caption>
  <tr>
    <th>變數</th>
    <th>說明</th>
  </tr>
  <tr>
    <td><code>MARIADB_ALLOW_EMPTY_PASSWORD</code></td>
    <td>容許資料庫在一開始為 root 使用者採用空的密碼。不建議使用。</td>
  </tr>
  <tr>
    <td><code>MARIADB_RANDOM_ROOT_PASSWORD</code></td>
    <td>為 MariaDB root 使用者產生隨機密碼。</td>
  </tr>
  <tr>
    <td><code>MARIADB_USER</code></td>
    <td>使用指定的使用者名稱建立一般 MariaDB 使用者。</td>
  </tr>
  <tr>
    <td><code>MARIADB_PASSWORD</code></td>
    <td><code>MYSQL_USER</code> 的密碼。</td>
  </tr>
  <tr>
    <td><code>MARIADB_DATABASE</code></td>
    <td>使用指定的名稱建立空的資料庫。</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_MODE</code></td>
    <td>啟用抄寫。選項包括 <code>master</code> 或 <code>slave</code>。</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_USER</code>（主節點或從屬節點）</td>
    <td>設定抄寫使用者的名稱。這個使用者會建立在主節點上，並由從屬節點用來連接。</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_PASSWORD</code>（主節點或從屬節點）</td>
    <td>設定抄寫使用者的密碼。這個密碼用於在主節點上建立使用者，並由從屬節點用來登入。</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_MASTER_HOST</code>（從屬節點）</td>
    <td>設定抄寫要連接至的主機名稱。</td>
  </tr>
</table>

## 連接至 MariaDB 實例
{: #datashield-mariadb-connect}

您可以執行下列指令來連接至 MariaDB 實例：

```
mariadb -h <node> --protocol tcp -uroot
```
{: pre}

Data Shield MariaDB 映像檔需要用戶端連線使用傳輸層安全 (TLS)。視您的用戶端版本而定，您可能需要在用戶端指令行新增 `--ssl` 或 `--ssl-mode require`。
{: note}
