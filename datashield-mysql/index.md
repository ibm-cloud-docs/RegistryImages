---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-13"

keywords: IBM Cloud Container Registry, Data Shield, data in use, memory encryption, intel sgx, fortanix, mysql image, container image, public image

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

# Getting started with the `datashield-mysql` image
{: #datashield-mysql_starter}

This container image provides protection for data that is in use by running MariaDB in the Data Shield environment. For more information about the service and what it means to protect "data in use", see the [IBM Cloud Data Shield documentation](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

You can access the images that are provided by {{site.data.keyword.IBM}} by using the command line, see [IBM public images](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Deploying the image
{: datashield-mysql-deploy}

You can use the following Kubernetes pod spec to deploy the image:

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
      name: MYSQL_ROOT_PASSWORD
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
{: pre}
  
<table>
<caption>Table 1. Required input variables</caption>
  <tr>
    <th>Variable</th>
    <th>Description</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>The name of the image that you want to deploy.</td>
  </tr>
    <tr>
    <td><code>MYSQL_ROOT_PASSWORD</code></td>
    <td>Sets the password for the MariaDB root user.</td>
  </tr>
  <tr>
    <td><code>ROOT_PASSWORD</code></td>
    <td>The password that you want to set for the MariaDB root user.</td>
  </tr>
</table>

### Accepted environment variables
{: #datashield-mysql-variables}

The MariaDB container also accepts the following environment variables. To use another variable, add them to the spec as shown in the following example:

```
spec:
  containers:
  - name: data-shield-mariadb
    image: <IMAGE_NAME>
    env:
      name: MYSQL_ROOT_PASSWORD
      value: <ROOT_PASSWORD>
      name: MYSQL_USER
      value: <USER_NAME>   
```
{: screen}

<table>
<caption>Table 2. The environment variables that the MariaDB container accepts</caption>
  <tr>
    <th>Variable</th>
    <th>Description</th>
  </tr>
  <tr>
    <td><code>MYSQL_ALLOW_EMPTY_PASSWORD</code></td>
    <td>Allows the database to start with an empty password for the root user. Not recommended.</td>
  </tr>
  <tr>
    <td><code>MYSQL_RANDOM_ROOT_PASSWORD</code></td>
    <td>Generates a random password for the MariaDB root user.</td>
  </tr>
  <tr>
    <td><code>MYSQL_USER</code></td>
    <td>Creates a regular MariaDB user with the specified user name.</td>
  </tr>
  <tr>
    <td><code>MYSQL_PASSWORD</code></td>
    <td>Password for <code>MYSQL_USER</code>.</td>
  </tr>
  <tr>
    <td><code>MYSQL_DATABASE</code></td>
    <td>Creates an empty database with the specified name.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MODE</code></td>
    <td>Enables replication. Options include <code>master</code> or <code>slave</code>.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_USER</code> (master or slave)</td>
    <td>Sets the name of the replication user. This user is created on the master and is used by the slave to connect.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_PASSWORD</code> (master or slave)</td>
    <td>Sets the password for the replication user. This password is used on the master to create the user and by the slave to log in.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MASTER_HOST</code> (slave)</td>
    <td>Sets the host name to connect to for replication.</td>
  </tr>
</table>



## Connecting to the MariaDB instance
{: #datashield-mysql-connect}

You can connect to the MariaDB instance by running the following command:

```
mysql -h <node> --protocol tcp -uroot
```
{: pre}

The Data Shield MariaDB image requires that client connections use Transport Layer Security (TLS). Depending on your client version, you might need to add `--ssl` or `--ssl-mode require` to the client command line.
{: note}

