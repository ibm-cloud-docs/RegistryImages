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

# Iniciación a la imagen `datashield-barbican`
{: #datashield-barbican_starter}

Esta imagen del contenedor ejecuta Barbican en el entorno Data Shield, lo que protege los datos utilizados.
{:shortdesc}

Puede acceder a las imágenes que suministra {{site.data.keyword.IBM}} desde la línea de mandatos; consulte las [imágenes públicas de IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

Puede utilizar la siguiente especificación de pod de Kubernetes para desplegar la imagen:

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

El contenedor puede tardar unos minutos en desplegarse e iniciarse. La instancia de Barbican de demostración se ha configurado sin autenticación Keystone. Las solicitudes deben incluir la cabecera `X-Project-Id: 12345`
{: tip}

Para crear un secreto en la instancia de demostración de Barbican ejecute el siguiente mandato:

```
    curl -sS -X POST -H 'content-type:application/json' -H 'X-Project-Id: 12345' -d \
        '{"payload": "my-secret-here", "payload_content_type": "text/plain"}' \
        http://<node>:9311/v1/secrets
```
{: pre}
    
La respuesta contiene el URL del secreto creado. Puede recuperar el secreto con el siguiente mandato:

```
    curl -sS -H 'X-Project-Id: 12345' <url>
```
{: pre}
