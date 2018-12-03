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

# Introduzione all'immagine `datashield-barbican`
{: #datashield-barbican_starter}

Questa immagine contenitore esegue Barbican nell'ambiente Data Shield, fornendo la protezione dei dati in utilizzo.
{:shortdesc}

Puoi accedere alle immagini fornite da {{site.data.keyword.IBM}} utilizzando la riga di comando, consulta [Immagini pubbliche IBM](/docs/services/Registry/registry_public_images.html#public_images).
{: tip}

Puoi utilizzare le seguenti specifiche del pod Kubernetes per distribuire l'immagine:

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
        image: <TODO INSERT APPROPRIATE IMAGE NAME HERE>
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

L'avvio e la distribuzione del contenitore potrebbero richiedere alcuni minuti. L'istanza demo Barbican è configurata senza l'autenticazione Keystone. Le richieste devono includere l'intestazione `X-Project-Id: 12345`
{: tip}

Puoi creare un segreto nell'istanza demo Barbican immettendo il seguente comando:

```
    curl -sS -X POST -H 'content-type:application/json' -H 'X-Project-Id: 12345' -d \
        '{"payload": "my-secret-here", "payload_content_type": "text/plain"}' \
        http://<node>:9311/v1/secrets
```
{: pre}
    
La risposta contiene l'URL per il segreto creato. Puoi richiamare il segreto immettendo il seguente comando:

```
    curl -sS -H 'X-Project-Id: 12345' <url>
```
{: pre}