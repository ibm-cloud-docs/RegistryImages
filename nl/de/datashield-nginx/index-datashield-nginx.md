---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-03"

keywords: IBM Cloud Container Registry, Data Shield environment, nginx image, container image, public image, data in use, memory encryption, Intel SGX, Fortanix,

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

# Einführung zum Image `datashield-nginx`
{: #datashield-nginx_starter}

Dieses Container-Image bietet den verwendeten Daten durch die NGINX-Ausführung in der {{site.data.keyword.datashield_short}}-Umgebung Schutz. Weitere Informationen zu dem Service und die Bedeutung des Schutzes von "verwendeten Daten" finden Sie in der [{{site.data.keyword.datashield_short}}-Dokumentation](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

Sie können auf die von {{site.data.keyword.IBM}} bereitgestellten Images über die Befehlszeile zugreifen. Informationen hierzu finden Sie in [öffentliche IBM Images](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Image bereitstellen
{: #datashield-nginx-deploy}

Mithilfe der folgenden Kubernetes-Podspezifikationen können Sie das Image bereitstellen:

```
apiVersion: v1
kind: Pod
metadata:
  name: data-shield-nginx
  labels:
    app: data-shield-nginx
spec:
  containers:
  - name: data-shield-nginx
    image: <IMAGE_NAME>
    volumeMounts:
    - mountPath: /dev/isgx
      name: isgx
    - mountPath: /dev/gsgx
      name: gsgx
    - mountPath: /var/run/aesmd/aesm.socket
      name: aesm-socket
    ports:
    - containerPort: 443
      name: https
      protocol: TCP
    env:
    - name: NODE_IP
      valueFrom:
        fieldRef:
          fieldPath: status.hostIP
    - name: NODE_AGENT_BASE_URL
      value: http://$(NODE_IP):9092/v1
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
  name: data-shield-nginx
  labels:
    app: data-shield-nginx
spec:
  type: LoadBalancer
  selector:
    app: data-shield-nginx
  ports:
    - name: https
      port: 443
      targetPort: 443
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
    <td><code>NODE_IP</code></td>
    <td>Die Knoten-IP-Adresse Ihrer NGINX-Instanz.</td>
  </tr>
</table>

Wenn Sie eigene Inhalte für die Bereitstellung durch NGINX angeben möchten, legen Sie sie unter `/usr/local/nginx/html` im Container ab.
{: tip}
