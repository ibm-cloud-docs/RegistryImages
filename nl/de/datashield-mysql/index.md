---

copyright:
  years: 2018
lastupdated: "2018-10-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip} 
{:table: .aria-labeledby="caption"}

# Einführung zum Image `datashield-mysql`
{: #datashield-mysql_starter}

Mit diesem Container-Image wird MariaDB in der Data Shield-Umgebung ausgeführt und bietet Schutz für Daten, die sich in Gebrauch befinden.
{:shortdesc}

Sie können auf die von {{site.data.keyword.IBM}} bereitgestellten Images über die Befehlszeile zugreifen. Informationen hierzu finden Sie in [öffentliche IBM Images](/docs/services/Registry/registry_public_images.html#public_images).
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
