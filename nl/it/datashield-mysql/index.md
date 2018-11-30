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

# Introduzione all'immagine `datashield-mysql`
{: #datashield-mysql_starter}

Questa immagine contenitore esegue MariaDB nell'ambiente Data Shield, fornendo la protezione dei dati in utilizzo.
{:shortdesc}

Puoi accedere alle immagini fornite da {{site.data.keyword.IBM}} utilizzando la riga di comando, consulta [Immagini pubbliche IBM](/docs/services/Registry/registry_public_images.html#public_images).
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
