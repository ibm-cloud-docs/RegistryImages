---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-03"

keywords: IBM Cloud Container Registry, Data Shield environment, datashield-barbican image, container image, Barbican, Registry, data in use, memory encryption, Intel SGX, Fortanix,

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

Esta imagen de contenedor proporciona protección para los datos que se utilizan cuando se ejecuta Barbican en el entorno {{site.data.keyword.datashield_short}}. Para obtener más información sobre el servicio y sobre lo que significa proteger "datos en uso", consulte la [documentación de {{site.data.keyword.datashield_short}}](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

Puede acceder a las imágenes que suministra {{site.data.keyword.IBM}} desde la línea de mandatos; consulte las [imágenes públicas de IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Despliegue de la imagen
{: #datashield-barbican-deploy}

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
    image: <IMAGE_NAME>
    volumeMounts:
    - mountPath: /dev/isgx
      name: isgx
    - mountPath: /dev/gsgx
      name: gsgx
    - mountPath: /var/run/aesmd/aesm.socket
      name: aesm-socket
    env:
    - name: NODE_IP
      valueFrom:
        fieldRef:
          fieldPath: status.hostIP
    - name: NODE_AGENT_BASE_URL
      value: http://$(NODE_IP):9092/v1
    ports:
    - containerPort: 9311
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
---
apiVersion: v1
kind: Service
metadata:
  name: data-shield-barbican
  labels:
    app: data-shield-barbican
spec:
  type: LoadBalancer
  selector:
    app: data-shield-barbican
  ports:
    - name: barbican
      port: 9311
      targetPort: 9311
```
{: codeblock}

<table>
<caption>Tabla 1. Variables de entrada necesarias</caption>
  <tr>
    <th>Variable</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>El nombre de la imagen que desea desplegar.</td>
  </tr>
  <tr>
    <td><code>NODE_IP</code></td>
    <td>La dirección IP de nodo de la instancia de Barbican.</td>
  </tr>
</table>

El contenedor puede tardar unos minutos en desplegarse e iniciarse. La instancia de Barbican de demostración se ha configurado sin autenticación Keystone. Las solicitudes deben incluir la cabecera `X-Project-Id: 12345`.
{: tip}

## Creación de un secreto
{: #datashield-barbican-secret}

Para crear un secreto en la instancia de demostración de Barbican ejecute el siguiente mandato:

```
curl -sS -X POST -H 'content-type:application/json' -H 'X-Project-Id: 12345' -d \
        '{"payload": "my-secret-here", "payload_content_type": "text/plain"}' \
        http://<node>:9311/v1/secrets
```
{: codeblock}

La respuesta contiene el URL del secreto creado. Puede recuperar el secreto con el siguiente mandato:

```
curl -sS -H 'X-Project-Id: 12345' <url>
```
{: codeblock}