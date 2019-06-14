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

# Introduzione all'immagine `datashield-nginx`
{: #datashield-nginx_starter}

Questa immagine contenitore fornisce la protezione per i dati in uso eseguendo NGINX nell'ambiente {{site.data.keyword.datashield_short}}. Per ulteriori informazioni sul servizio e su cosa significa proteggere i "dati in uso", vedi la [documentazione di {{site.data.keyword.datashield_short}}](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

Puoi accedere alle immagini fornite da {{site.data.keyword.IBM}} utilizzando la riga di comando, consulta [Immagini pubbliche IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Distribuzione dell'immagine
{: #datashield-nginx-deploy}

Puoi utilizzare le seguenti specifiche del pod Kubernetes per distribuire l'immagine:

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
    <td><code>NODE_IP</code></td>
    <td>L'indirizzo IP del nodo della tua istanza NGINX.</td>
  </tr>
</table>

Per fornire il tuo contenuto in modo che venga utilizzato da NGINX, posizionalo in `/usr/local/nginx/html` nel contenitore.
{: tip}
