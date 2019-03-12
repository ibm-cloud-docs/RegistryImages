---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-21"

keywords: datashield-barbican image, container image, barbican, Data Shield environment, public image

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

# Einführung zum Image `datashield-barbican`
{: #datashield-barbican_starter}

Mit diesem Container-Image wird Barbican in der Data Shield-Umgebung ausgeführt und bietet Schutz für Daten, die sich in Gebrauch befinden.
{:shortdesc}

Sie können auf die von {{site.data.keyword.IBM}} bereitgestellten Images über die Befehlszeile zugreifen. Informationen hierzu finden Sie in [öffentliche IBM Images](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

Mithilfe der folgenden Kubernetes-Podspezifikationen können Sie das Image bereitstellen:

```
    apiVersion: v1
    kind: Pod
    metadata:
      name: data-shield-barbican
      labels:
        app: data-shield-barbican
    spec:
      containers:
      - name: data-shield-barbican
        image: <Entsprechenden Imagenamen hier einfügen>
        volumeMounts:
        - mountPath: /dev/isgx
          name: isgx
        - mountPath: /dev/gsgx
          name: gsgx
        - mountPath: /var/run/aesmd/aesm.socket
          name: aesm-socket
        ports:
        - containerPort: 9311
          hostPort: 9311
          name: barbican
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

Das Bereitstellen und Starten des Containers kann einige Minuten in Anspruch nehmen. Die Barbican-Demonstrationsinstanz ist ohne Keystone-Authentifizierung konfiguriert. Anforderungen müssen den Header `X-Project-Id: 12345` enthalten.
{: tip}

Sie können einen geheimen Schlüssel in der Barbican-Demonstrationsinstanz erstellen, indem Sie den folgenden Befehl ausführen:

```
    curl -sS -X POST -H 'content-type:application/json' -H 'X-Project-Id: 12345' -d \
        '{"payload": "my-secret-here", "payload_content_type": "text/plain"}' \
        http://<node>:9311/v1/secrets
```
{: pre}
    
Die Antwort enthält die URL für den erstellten geheimen Schlüssel. Sie können den geheimen Schlüssel abrufen, indem Sie den folgenden Befehl ausführen:

```
    curl -sS -H 'X-Project-Id: 12345' <url>
```
{: pre}
