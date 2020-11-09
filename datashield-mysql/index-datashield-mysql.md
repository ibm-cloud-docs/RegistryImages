---

copyright:
  years: 2018, 2020
lastupdated: "2020-11-09"

keywords: registry, Data Shield, data in use, memory encryption, Intel SGX, Fortanix, mysql image, mariaDB, container image, public images,

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
{:term: .term}
{:external: target="_blank" .external}

# Getting started with the `datashield-mariadb` image
{: #datashield-mariadb_starter}

This container image provides protection for data that is in use by running MariaDB in the {{site.data.keyword.datashield_full}} environment. For more information about the service and what it means to protect data in use, see the [{{site.data.keyword.datashield_short}} documentation](/docs/data-shield?topic=data-shield-about#about).
{: shortdesc}

You can access the images that are provided by {{site.data.keyword.IBM}} by using the command line, see [{{site.data.keyword.IBM_notm}} public images](/docs/Registry?topic=Registry-public_images#public_images).
{: tip}

## Deploying the image
{: #datashield-mariadb-deploy}

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
    - name: MARIADB_ROOT_PASSWORD
      value: <ROOT_PASSWORD>
    - name: NODE_IP
      valueFrom:
        fieldRef:
          fieldPath: status.hostIP
    - name: NODE_AGENT_BASE_URL
      value: http://$(NODE_IP):9092/v1
    volumeMounts:
    - mountPath: /dev/isgx
      name: isgx
    - mountPath: /dev/gsgx
      name: gsgx
    - mountPath: /var/run/aesmd/aesm.socket
      name: aesm-socket
    ports:
    - containerPort: 3306
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
---
apiVersion: v1
kind: Service
metadata:
  name: data-shield-mariadb
  labels:
    app: data-shield-mariadb
spec:
  type: LoadBalancer
  selector:
    app: data-shield-mariadb
  ports:
    - name: mariadb
      port: 3306
      targetPort: 3306
```
{: codeblock}

The following table shows the required input variables.

| Variable | Description |
|-----------------|-----------------|
| `IMAGE_NAME` | The name of the image that you want to deploy. |
| `MARIADB_ROOT_PASSWORD` | Sets the password for the MariaDB root user. |
| `ROOT_PASSWORD` | The password that you want to set for the MariaDB root user. |
| `NODE_IP` | The node IP address of your MariaDB instance. |
{: caption="Table 1. Required input variables" caption-side="top"}

### Accepted environment variables
{: #datashield-mariadb-variables}

The MariaDB container also accepts the following environment variables. To use another variable, add them to the spec as shown in the following example:

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

The following table shows the environment variables that the MariaDB container accepts.

| Variable | Description |
|-----------------|-----------------|
| `MARIADB_ALLOW_EMPTY_PASSWORD` | Allows the database to start with an empty password for the root user. Not recommended. |
| `MARIADB_RANDOM_ROOT_PASSWORD` | Generates a random password for the MariaDB root user. |
| `MARIADB_USER` | Creates a regular MariaDB user with the specified user name. |
| `MARIADB_PASSWORD` | Password for `MARIADB_USER`. |
| `MARIADB_DATABASE` | Creates an empty database with the specified name. |
| `MARIADB_REPLICATION_MODE` | Enables replication. Options include `primary` or `secondary`. |
| `MARIADB_REPLICATION_USER` | Sets the name of the replication user. This user is created on the primary and is used by the secondary to connect. |
| `MARIADB_REPLICATION_PASSWORD` | Sets the password for the replication user. This password is used on the primary to create the user and by the secondary to log in. |
| `MARIADB_REPLICATION_PRIMARY_HOST` | Sets the host name for the secondary to connect to for replication. |
{: caption="Table 2. The environment variables that the MariaDB container accepts" caption-side="top"}

## Connecting to the MariaDB instance
{: #datashield-mariadb-connect}

You can connect to the MariaDB instance by running the following command:

```
mariadb -h <node> --protocol tcp -uroot
```
{: codeblock}

The {{site.data.keyword.datashield_short}} MariaDB image requires that client connections use Transport Layer Security (TLS). Depending on your client version, you might need to add `--ssl` or `--ssl-mode require` to the client command line.
{: note}
