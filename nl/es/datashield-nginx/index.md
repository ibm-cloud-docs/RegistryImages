---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-09"

keywords: IBM Cloud Container Registry, Data Shield environment, nginx image, container image, public image, data in use, memory encryption, intel sgx, fortanix,

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

# Iniciación a la imagen `datashield-nginx`
{: #datashield-nginx_starter}

Esta imagen de contenedor proporciona protección para los datos que se utilizan cuando se ejecuta NGINX en el entorno {{site.data.keyword.datashield_short}}. Para obtener más información sobre el servicio y sobre lo que significa proteger "datos en uso", consulte la [documentación de {{site.data.keyword.datashield_short}}](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

Puede acceder a las imágenes que suministra {{site.data.keyword.IBM}} desde la línea de mandatos; consulte las [imágenes públicas de IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Despliegue de la imagen
{: #datashield-nginx-deploy}

Puede utilizar la siguiente especificación de pod de Kubernetes para desplegar la imagen:

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
        - containerPort: 80
          hostPort: 80
          name: http
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
<caption>Tabla 1. Variables de entrada necesarias</caption>
  <tr>
    <th>Variable</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>El nombre de la imagen que desea desplegar.</td>
  </tr>
</table>

Para proporcionar su propio contenido que servirá NGINX, colóquelo bajo `/usr/local/nginx/html` en el contenedor.
{: tip}
