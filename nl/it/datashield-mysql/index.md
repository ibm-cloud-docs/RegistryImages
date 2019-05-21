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

# Introduzione all'immagine `datashield-mariadb`
{: #datashield-mariadb_starter}

Questa immagine contenitore fornisce la protezione per i dati in uso eseguendo MariaDB nell'ambiente {{site.data.keyword.datashield_short}}. Per ulteriori informazioni sul servizio e su cosa significa proteggere i dati in uso, vedi la [documentazione di {{site.data.keyword.datashield_short}}](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

Puoi accedere alle immagini fornite da {{site.data.keyword.IBM}} utilizzando la riga di comando, consulta [Immagini pubbliche IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Distribuzione dell'immagine
{: #datashield-mariadb-deploy}

Puoi utilizzare le seguenti specifiche del pod Kubernetes per distribuire l'immagine:

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
<caption>Tabella 1. Variabili di input obbligatorie</caption>
  <tr>
    <th>Variabile</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>Il nome dell'immagine che vuoi distribuire.</td>
  </tr>
    <tr>
    <td><code>MARIADB_ROOT_PASSWORD</code></td>
    <td>Imposta la password per l'utente root MariaDB.</td>
  </tr>
  <tr>
    <td><code>ROOT_PASSWORD</code></td>
    <td>La password che vuoi impostare per l'utente root MariaDB.</td>
  </tr>
</table>

### Variabili di ambiente accettate
{: #datashield-mariadb-variables}

Il contenitore MariaDB accetta anche le seguenti variabili di ambiente. Per utilizzare un'altra variabile, aggiungile alla specifica come mostrato nel seguente esempio:

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
<caption>Tabella 2. Variabili di ambiente accettate dal contenitore MariaDB</caption>
  <tr>
    <th>Variabile</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td><code>MARIADB_ALLOW_EMPTY_PASSWORD</code></td>
    <td>Consente l'avvio del database con una password vuota per l'utente root. Non consigliato.</td>
  </tr>
  <tr>
    <td><code>MARIADB_RANDOM_ROOT_PASSWORD</code></td>
    <td>Genera una password casuale per l'utente root MariaDB.</td>
  </tr>
  <tr>
    <td><code>MARIADB_USER</code></td>
    <td>Crea un utente MariaDB regolare con il nome utente specificato.</td>
  </tr>
  <tr>
    <td><code>MARIADB_PASSWORD</code></td>
    <td>Password per <code>MYSQL_USER</code>.</td>
  </tr>
  <tr>
    <td><code>MARIADB_DATABASE</code></td>
    <td>Crea un database vuoto con il nome specificato.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_MODE</code></td>
    <td>Abilita la replica. Le opzioni includono <code>master</code> o <code>slave</code>.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_USER</code> (master o slave)</td>
    <td>Imposta il nome dell'utente della replica. Questo utente viene creato sul master e viene utilizzato dallo slave per stabilire una connessione.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_PASSWORD</code> (master o slave)</td>
    <td>Imposta la password dell'utente della replica. Questa password viene utilizzata sul master per creare l'utente e dallo slave per eseguire l'accesso.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_MASTER_HOST</code> (slave)</td>
    <td>Imposta il nome host a cui connettersi per la replica.</td>
  </tr>
</table>

## Connessione all'istanza MariaDB
{: #datashield-mariadb-connect}

Puoi collegarti all'istanza MariaDB immettendo il seguente comando:

```
mariadb -h <node> --protocol tcp -uroot
```
{: codeblock}

L'immagine {{site.data.keyword.datashield_short}} MariaDB richiede che le connessioni client utilizzino TLS (Transport Layer Security). A seconda della versione del client, potrebbe essere necessario aggiungere `--ssl` o `--ssl-mode require` alla riga di comando del client.
{: note}
