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

# `datashield-mariadb` 이미지 시작하기
{: #datashield-mariadb_starter}

이 컨테이너 이미지는 Data Shield 환경에서 MariaDB를 실행하여 사용 중인 데이터를 보호합니다. 서비스에 대한 자세한 내용과 사용 중인 데이터를 보호하는 방법에 대한 자세한 내용은 [IBM Cloud Data Shield 문서](/docs/services/data-shield?topic=data-shield-about#about)를 참조하십시오.
{: shortdesc}

명령행을 사용하여 {{site.data.keyword.IBM}}에서 제공한 이미지에 액세스할 수 있습니다. [IBM 공용 이미지](/docs/services/Registry?topic=registry-public_images#public_images)를 참조하십시오.
{: tip}

## 이미지 배치
{: #datashield-mariadb-deploy}

다음 Kubernetes 팟(Pod) 스펙을 사용하여 이미지를 배치할 수 있습니다.

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
<caption>표 1. 필수 입력 변수</caption>
  <tr>
    <th>변수</th>
    <th>설명</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>배치하려는 이미지 이름입니다. </td>
  </tr>
    <tr>
    <td><code>MARIADB_ROOT_PASSWORD</code></td>
    <td>MariaDB 루트 사용자의 비밀번호를 설정합니다.</td>
  </tr>
  <tr>
    <td><code>ROOT_PASSWORD</code></td>
    <td>MariaDB 루트 사용자에 설정하려는 비밀번호입니다. </td>
  </tr>
</table>

### 허용되는 환경 변수
{: #datashield-mariadb-variables}

MariaDB 컨테이너는 다음과 같은 환경 변수를 허용합니다. 다른 변수를 사용하려면 다음 예제와 같이 해당 변수를 스펙에 추가하십시오. 

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
<caption>표 2. MariaDB 컨테이너에서 허용하는 환경 변수</caption>
  <tr>
    <th>변수</th>
    <th>설명</th>
  </tr>
  <tr>
    <td><code>MARIADB_ALLOW_EMPTY_PASSWORD</code></td>
    <td>루트 사용자가 빈 비밀번호로 데이터베이스를 시작할 수 있게 허용합니다. 권장하지 않습니다.</td>
  </tr>
  <tr>
    <td><code>MARIADB_RANDOM_ROOT_PASSWORD</code></td>
    <td>MariaDB 루트 사용자의 비밀번호를 생성합니다.</td>
  </tr>
  <tr>
    <td><code>MARIADB_USER</code></td>
    <td>지정된 사용자 이름으로 일반 MariaDB 사용자를 작성합니다.</td>
  </tr>
  <tr>
    <td><code>MARIADB_PASSWORD</code></td>
    <td><code>MYSQL_USER</code>의 비밀번호입니다.</td>
  </tr>
  <tr>
    <td><code>MARIADB_DATABASE</code></td>
    <td>지정된 이름으로 빈 데이터베이스를 작성합니다.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_MODE</code></td>
    <td>복제를 사용합니다. 옵션에 <code>master</code> 또는 <code>slave</code>가 포함됩니다.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_USER</code>(master 또는 slave)</td>
    <td>복제 사용자의 이름을 설정합니다. 이 사용자는 마스터에 작성되며 슬레이브에서 연결하는 데 사용합니다.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_PASSWORD</code>(master 또는 slave)</td>
    <td>복제 사용자의 비밀번호를 설정합니다. 이 비밀번호는 마스터에서 사용자를 작성하는 데 사용되며 슬레이브에서는 로그인하는 데 사용됩니다.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_MASTER_HOST</code>(slave)</td>
    <td>복제를 위해 연결할 호스트 이름을 설정합니다.</td>
  </tr>
</table>

## MariaDB 인스턴스에 연결
{: #datashield-mariadb-connect}

다음 명령을 실행하여 MariaDB 인스턴스를 연결할 수 있습니다.

```
mariadb -h <node> --protocol tcp -uroot
```
{: pre}

Data Shield MariaDB 이미지는 클라이언트 연결에서 TLS(Transport Layer Security)를 사용해야 합니다. 클라이언트 버전에 따라 `--ssl` 또는 `--ssl-mode require`을 클라이언트 명령행에 추가해야 할 수 있습니다.
{: note}
