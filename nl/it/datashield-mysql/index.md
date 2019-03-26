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

# Introduzione all'immagine `datashield-mysql`
{: #datashield-mysql_starter}

Questa immagine contenitore esegue MariaDB nell'ambiente Data Shield, fornendo la protezione per i tuoi dati.
{:shortdesc}

Puoi accedere alle immagini fornite da {{site.data.keyword.IBM}} utilizzando la riga di comando, consulta [Immagini pubbliche IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

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

Puoi collegarti all'istanza MariaDB immettendo il seguente comando:

```
    mysql -h <node> --protocol tcp -uroot
```
{: pre}

L'immagine Data Shield MariaDB richiede che le connessioni client utilizzano TLS. A seconda della versione del client, potrebbe essere necessario aggiungere `--ssl` o `--ssl-mode require` alla riga di comando del client.

Consulta la seguente tabella per vedere le variabili di ambiente accettate dal contenitore MariaDB:

<table>
<caption>Tabella 1. Variabili di ambiente accettate dal contenitore MariaDB</caption>
  <tr>
    <th>Variabile</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td><code>MYSQL_ROOT_PASSWORD</code></td>
    <td>Imposta la password per l'utente root MariaDB.</td>
  </tr>
  <tr>
    <td><code>MYSQL_ALLOW_EMPTY_PASSWORD</code></td>
    <td>Consente l'avvio del database con una password vuota per l'utente root. Non consigliato.</td>
  </tr>
  <tr>
    <td><code>MYSQL_RANDOM_ROOT_PASSWORD</code></td>
    <td>Genera una password casuale per l'utente root MariaDB.</td>
  </tr>
  <tr>
    <td><code>MYSQL_USER</code></td>
    <td>Crea un utente MariaDB regolare con il nome utente specificato.</td>
  </tr>
  <tr>
    <td><code>MYSQL_PASSWORD</code></td>
    <td>Password per <code>MYSQL_USER</code>.</td>
  </tr>
  <tr>
    <td><code>MYSQL_DATABASE</code></td>
    <td>Crea un database vuoto con il nome specificato.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MODE</code></td>
    <td>Abilita la replica. Le opzioni includono <code>master</code> o <code>slave</code>.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_USER</code> (master o slave)</td>
    <td>Imposta il nome dell'utente della replica. Questo utente viene creato sul master e viene utilizzato dallo slave per stabilire una connessione.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_PASSWORD</code> (master o slave)</td>
    <td>Imposta la password dell'utente della replica. Questa password viene utilizzata sul master per creare l'utente e dallo slave per eseguire l'accesso.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MASTER_HOST</code> (slave)</td>
    <td>Imposta il nome host a cui connettersi per la replica.</td>
  </tr>
</table>
