---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-09"

keywords: IBM Cloud Container Registry, Data Shield environment, datashield-barbican image, container image, barbican, Registry, data in use, memory encryption, intel sgx, fortanix,

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

# Introduzione all'immagine `datashield-barbican`
{: #datashield-barbican_starter}

Questa immagine contenitore fornisce la protezione per i dati in uso eseguendo Barbican nell'ambiente {{site.data.keyword.datashield_short}}. Per ulteriori informazioni sul servizio e su cosa significa proteggere i "dati in uso", vedi la [documentazione di {{site.data.keyword.datashield_short}}](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

Puoi accedere alle immagini fornite da {{site.data.keyword.IBM}} utilizzando la riga di comando, consulta [Immagini pubbliche IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Distribuzione dell'immagine
{: #datashield-barbican-deploy}

Puoi utilizzare la seguente specifica del pod Kubernetes per distribuire l'immagine:

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
        image: <IMAGE_NAME>
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
</table>

L'avvio e la distribuzione del contenitore potrebbero richiedere alcuni minuti. L'istanza Barbican di dimostrazione è configurata senza l'autenticazione Keystone. Le richieste devono includere l'intestazione `X-Project-Id: 12345`.
{: tip}

## Creazione di un segreto
{: #datashield-barbican-secret}

Puoi creare un segreto nell'istanza Barbican di dimostrazione immettendo il seguente comando:

```
curl -sS -X POST -H 'content-type:application/json' -H 'X-Project-Id: 12345' -d \
        '{"payload": "my-secret-here", "payload_content_type": "text/plain"}' \
        http://<node>:9311/v1/secrets
```
{: codeblock}
    
La risposta contiene l'URL per il segreto creato. Puoi richiamare il segreto immettendo il seguente comando:

```
curl -sS -H 'X-Project-Id: 12345' <url>
```
{: codeblock}
