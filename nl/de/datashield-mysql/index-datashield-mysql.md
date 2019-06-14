---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-03"

keywords: IBM Cloud Container Registry, Data Shield, data in use, memory encryption, Intel SGX, Fortanix, mysql image, mariaDB, container image, public image

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

# Einführung zum Image `datashield-mariadb`
{: #datashield-mariadb_starter}

Dieses Container-Image bietet den verwendeten Daten durch die MariaDB-Ausführung in der {{site.data.keyword.datashield_short}}-Umgebung Schutz. Weitere Informationen zu dem Service und die Bedeutung des Schutzes von "verwendeten Daten" finden Sie in der [{{site.data.keyword.datashield_short}}-Dokumentation](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

Sie können auf die von {{site.data.keyword.IBM}} bereitgestellten Images über die Befehlszeile zugreifen. Informationen hierzu finden Sie in [öffentliche IBM Images](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Image bereitstellen
{: #datashield-mariadb-deploy}

Mithilfe der folgenden Kubernetes-Podspezifikationen können Sie das Image bereitstellen:

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
  
<table>
<caption>Tabelle 1. Erforderliche Eingabevariablen</caption>
  <tr>
    <th>Variable</th>
    <th>Beschreibung</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>Der Name des Image, das Sie bereitstellen möchten.</td>
  </tr>
    <tr>
    <td><code>MARIADB_ROOT_PASSWORD</code></td>
    <td>Kennwort für den MariaDB-Rootbenutzer festlegen.</td>
  </tr>
  <tr>
    <td><code>ROOT_PASSWORD</code></td>
    <td>Das Kennwort, das Sie für den MariaDB-Rootbenutzer festlegen möchten.</td>
  </tr>
  <tr>
    <td><code>NODE_IP</code></td>
    <td>Die Knoten-IP-Adresse Ihrer MariaDB-Instanz.</td>
  </tr>
</table>

### Zulässige Umgebungsvariablen
{: #datashield-mariadb-variables}

Der MariaDB-Container akzeptiert auch die folgenden Umgebungsvariablen. Um eine weitere Variable zu verwenden, müssen Sie sie wie im folgenden Beispiel dargestellt zu den Spezifikationen hinzufügen:

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
<caption>Tabelle 2. Umgebungsvariablen, die vom MariaDB-Container akzeptiert werden</caption>
  <tr>
    <th>Variable</th>
    <th>Beschreibung</th>
  </tr>
  <tr>
    <td><code>MARIADB_ALLOW_EMPTY_PASSWORD</code></td>
    <td>Starten der Datenbank mit einem leeren Kennwort für den Rootbenutzer ermöglichen. Nicht empfohlen.</td>
  </tr>
  <tr>
    <td><code>MARIADB_RANDOM_ROOT_PASSWORD</code></td>
    <td>Automatisch generiertes Kennwort für den MariaDB-Rootbenutzer erstellen.</td>
  </tr>
  <tr>
    <td><code>MARIADB_USER</code></td>
    <td>MariaDB-Standardbenutzer mit dem angegebenen Benutzernamen erstellen.</td>
  </tr>
  <tr>
    <td><code>MARIADB_PASSWORD</code></td>
    <td>Kennwort für <code>MARIADB_USER</code>.</td>
  </tr>
  <tr>
    <td><code>MARIADB_DATABASE</code></td>
    <td>Leere Datenbank mit dem angegebenen Namen erstellen.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_MODE</code></td>
    <td>Replikation aktivieren. Optionen sind <code>master</code> oder <code>slave</code>.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_USER</code> (master oder slave)</td>
    <td>Namen des Replikationsbenutzers festlegen. Dieser Benutzer wird auf dem Mastersystem erstellt und vom Slavesystem für die Verbindungsherstellung verwendet.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_PASSWORD</code> (master oder slave)</td>
    <td>Kennwort für den Replikationsbenutzer festlegen. Dieses Kennwort wird auf dem Mastersystem verwendet, um den Benutzer zu erstellen; es wird vom Slavesystem für die Anmeldung verwendet.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_MASTER_HOST</code> (slave)</td>
    <td>Namen des Hosts festlegen, zu dem eine Verbindung für die Replikation hergestellt wird.</td>
  </tr>
</table>

## Herstellen einer Verbindung zur MariaDB-Instanz
{: #datashield-mariadb-connect}

Sie können eine Verbindung zur MariaDB-Instanz herstellen, indem Sie den folgenden Befehl ausführen:

```
mariadb -h <node> --protocol tcp -uroot
```
{: codeblock}

Für das {{site.data.keyword.datashield_short}}-MariaDB-Image ist es erforderlich, dass die Clientverbindungen TLS (Transport Layer Security) verwenden. Abhängig von der verwendeten Clientversion müssen Sie möglicherweise `--ssl` oder `--ssl-mode require` zur Clientbefehlszeile hinzufügen.
{: note}
