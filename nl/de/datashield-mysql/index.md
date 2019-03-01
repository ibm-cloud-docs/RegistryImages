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

# Einführung zum Image `datashield-mysql`
{: #datashield-mysql_starter}

Mit diesem Container-Image wird MariaDB in der Data Shield-Umgebung ausgeführt und bietet Schutz für Daten, die sich in Gebrauch befinden.
{:shortdesc}

Sie können auf die von {{site.data.keyword.IBM}} bereitgestellten Images über die Befehlszeile zugreifen. Informationen hierzu finden Sie in [öffentliche IBM Images](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

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
        image: <Entsprechenden Imagenamen hier einfügen>
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

Sie können eine Verbindung zur MariaDB-Instanz herstellen, indem Sie den folgenden Befehl ausführen:

```
    mysql -h <node> --protocol tcp -uroot
```
{: pre}

Für das Data Shield-MariaDB-Image ist es erforderlich, dass die Clientverbindungen TLS verwenden. Abhängig von der verwendeten Clientversion müssen Sie möglicherweise `--ssl` oder `--ssl-mode require` zur Clientbefehlszeile hinzufügen.

Die folgende Tabelle enthält die Umgebungsvariablen, die vom MariaDB-Container akzeptiert werden:

<table>
<caption>Tabelle 1. Umgebungsvariablen, die vom MariaDB-Container akzeptiert werden</caption>
  <tr>
    <th>Variable</th>
    <th>Beschreibung</th>
  </tr>
  <tr>
    <td><code>MYSQL_ROOT_PASSWORD</code></td>
    <td>Kennwort für den MariaDB-Rootbenutzer festlegen. </td>
  </tr>
  <tr>
    <td><code>MYSQL_ALLOW_EMPTY_PASSWORD</code></td>
    <td>Starten der Datenbank mit einem leeren Kennwort für den Rootbenutzer ermöglichen. Nicht empfohlen. </td>
  </tr>
  <tr>
    <td><code>MYSQL_RANDOM_ROOT_PASSWORD</code></td>
    <td>Automatisch generiertes Kennwort für den MariaDB-Rootbenutzer erstellen. </td>
  </tr>
  <tr>
    <td><code>MYSQL_USER</code></td>
    <td>MariaDB-Standardbenutzer mit dem angegebenen Benutzernamen erstellen. </td>
  </tr>
  <tr>
    <td><code>MYSQL_PASSWORD</code></td>
    <td>Kennwort für <code>MYSQL_USER</code>. </td>
  </tr>
  <tr>
    <td><code>MYSQL_DATABASE</code></td>
    <td>Leere Datenbank mit dem angegebenen Namen erstellen. </td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MODE</code></td>
    <td>Replikation aktivieren. Optionen sind <code>master</code> oder <code>slave</code>. </td>
  </tr>
  <tr>
    <td><code>YSQL_REPLICATION_USER</code> (master oder slave)</td>
    <td>Namen des Replikationsbenutzers festlegen. Dieser Benutzer wird auf dem Mastersystem erstellt und vom Slavesystem für die Verbindungsherstellung verwendet. </td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_PASSWORD</code> (master or slave)</td>
    <td>Kennwort für den Replikationsbenutzer festlegen. Dieses Kennwort wird auf dem Mastersystem verwendet, um den Benutzer zu erstellen; es wird vom Slavesystem für die Anmeldung verwendet. </td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MASTER_HOST</code> (slave)</td>
    <td>Namen des Hosts festlegen, zu dem eine Verbindung für die Replikation hergestellt wird. </td>
  </tr>
</table>
