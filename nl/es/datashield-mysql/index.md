---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-26"

keywords: IBM Cloud Container Registry, Data Shield environment, mysql image, container image, public image

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

# Iniciación a la imagen `datashield-mysql`
{: #datashield-mysql_starter}

Esta imagen del contenedor ejecuta MariaDB en el entorno Data Shield, que proporciona protección para sus datos.
{:shortdesc}

Puede acceder a las imágenes que suministra {{site.data.keyword.IBM}} desde la línea de mandatos; consulte las [imágenes públicas de IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

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

Puede conectar con la instancia de MariaDB con el siguiente mandato:

```
    mysql -h <node> --protocol tcp -uroot
```
{: pre}

La imagen de MariaDB de Data Shield requiere que las conexiones del cliente utilicen TLS. En función de su versión del cliente, es posible que tenga que añadir `--ssl` o `--ssl-mode require` a la línea de mandatos del cliente.

Verifique la siguiente tabla para ver las variables de entorno que acepta el contenedor MariaDB:

<table>
<caption>Tabla 1. Las variables de entorno que acepta el contenedor MariaDB</caption>
  <tr>
    <th>Variable</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td><code>MYSQL_ROOT_PASSWORD</code></td>
    <td>Establece la contraseña para el usuario root de MariaDB.</td>
  </tr>
  <tr>
    <td><code>MYSQL_ALLOW_EMPTY_PASSWORD</code></td>
    <td>Permite que la base de datos comience con una contraseña vacía para el usuario root. No es recomendable.</td>
  </tr>
  <tr>
    <td><code>MYSQL_RANDOM_ROOT_PASSWORD</code></td>
    <td>Genera una contraseña aleatoria para el usuario root de MariaDB.</td>
  </tr>
  <tr>
    <td><code>MYSQL_USER</code></td>
    <td>Crea un usuario normal de MariaDB con el nombre de usuario especificado.</td>
  </tr>
  <tr>
    <td><code>MYSQL_PASSWORD</code></td>
    <td>Contraseña para <code>MYSQL_USER</code>.</td>
  </tr>
  <tr>
    <td><code>MYSQL_DATABASE</code></td>
    <td>Crea una base de datos vacía con el nombre especificado.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MODE</code></td>
    <td>Permite la réplica. Las opciones incluyen <code>master</code> o <code>slave</code>.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_USER</code> (maestro o esclavo)</td>
    <td>Establece el nombre del usuario de réplica. Este usuario se ha creado en maestro y lo utiliza el esclavo para conectarse.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_PASSWORD</code> (maestro o esclavo)</td>
    <td>Establece la contraseña para el usuario de réplica. Esta contraseña se utiliza en el maestro para crear el usuario y en el esclavo para iniciar sesión.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MASTER_HOST</code> (esclavo)</td>
    <td>Establece el nombre de host para conectarse a la réplica.</td>
  </tr>
</table>
