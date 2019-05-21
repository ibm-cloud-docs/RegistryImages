---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-09"

keywords: IBM Cloud Container Registry, Data Shield, data in use, memory encryption, intel sgx, fortanix, mysql image, mariaDB, container image, public image

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

# Iniciación a la imagen `datashield-mariadb`
{: #datashield-mariadb_starter}

Esta imagen de contenedor proporciona protección para los datos que se utilizan cuando se ejecuta MariaDB en el entorno {{site.data.keyword.datashield_short}}. Para obtener más información sobre el servicio y sobre lo que significa proteger datos en uso, consulte la [documentación de {{site.data.keyword.datashield_short}}](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

Puede acceder a las imágenes que suministra {{site.data.keyword.IBM}} desde la línea de mandatos; consulte las [imágenes públicas de IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Despliegue de la imagen
{: #datashield-mariadb-deploy}

Puede utilizar la siguiente especificación de pod de Kubernetes para desplegar la imagen:

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
    image: <IMAGE_NAME>
    env:
      name: MARIADB_ROOT_PASSWORD
      value: <ROOT_PASSWORD>
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
      name: mariadb
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
    <tr>
    <td><code>MARIADB_ROOT_PASSWORD</code></td>
    <td>Establece la contraseña para el usuario root de MariaDB.</td>
  </tr>
  <tr>
    <td><code>ROOT_PASSWORD</code></td>
    <td>La contraseña que desea establecer para el usuario root de MariaDB.</td>
  </tr>
</table>

### Variables de entorno aceptadas
{: #datashield-mariadb-variables}

El contenedor MariaDB también acepta las siguientes variables de entorno. Para utilizar otra variable, añádala a las especificaciones, como se muestra en el siguiente ejemplo:

```
spec:
  containers:
  - name: data-shield-mariadb
    image: <IMAGE_NAME>
    env:
      name: MARIADB_ROOT_PASSWORD
      value: <ROOT_PASSWORD>
      name: MARIADB_USER
      value: <USER_NAME>
```
{: screen}

<table>
<caption>Tabla 2. Las variables de entorno que acepta el contenedor MariaDB</caption>
  <tr>
    <th>Variable</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td><code>MARIADB_ALLOW_EMPTY_PASSWORD</code></td>
    <td>Permite que la base de datos comience con una contraseña vacía para el usuario root. No es recomendable.</td>
  </tr>
  <tr>
    <td><code>MARIADB_RANDOM_ROOT_PASSWORD</code></td>
    <td>Genera una contraseña aleatoria para el usuario root de MariaDB.</td>
  </tr>
  <tr>
    <td><code>MARIADB_USER</code></td>
    <td>Crea un usuario normal de MariaDB con el nombre de usuario especificado.</td>
  </tr>
  <tr>
    <td><code>MARIADB_PASSWORD</code></td>
    <td>Contraseña para <code>MYSQL_USER</code>.</td>
  </tr>
  <tr>
    <td><code>MARIADB_DATABASE</code></td>
    <td>Crea una base de datos vacía con el nombre especificado.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_MODE</code></td>
    <td>Permite la réplica. Las opciones incluyen <code>master</code> o <code>slave</code>.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_USER</code> (maestro o esclavo)</td>
    <td>Establece el nombre del usuario de réplica. Este usuario se ha creado en maestro y lo utiliza el esclavo para conectarse.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_PASSWORD</code> (maestro o esclavo)</td>
    <td>Establece la contraseña para el usuario de réplica. Esta contraseña se utiliza en el maestro para crear el usuario y en el esclavo para iniciar sesión.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_MASTER_HOST</code> (esclavo)</td>
    <td>Establece el nombre de host para conectarse a la réplica.</td>
  </tr>
</table>

## Conexión a una instancia de MariaDB
{: #datashield-mariadb-connect}

Puede conectar con la instancia de MariaDB con el siguiente mandato:

```
mariadb -h <node> --protocol tcp -uroot
```
{: codeblock}

La imagen de MariaDB de {{site.data.keyword.datashield_short}} requiere que las conexiones del cliente utilicen TLS (seguridad de la capa de transporte). En función de su versión del cliente, es posible que tenga que añadir `--ssl` o `--ssl-mode require` a la línea de mandatos del cliente.
{: note}
