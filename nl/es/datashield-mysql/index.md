---

copyright:
  years: 2018, 2019
lastupdated: "2019-01-03"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Iniciación a la imagen `datashield-mysql`
{: #datashield-mysql_starter}

Esta imagen del contenedor ejecuta MariaDB en el entorno Data Shield, lo que protege los datos utilizados.
{:shortdesc}

Puede acceder a las imágenes que suministra {{site.data.keyword.IBM}} desde la línea de mandatos; consulte las [imágenes públicas de IBM](/docs/services/Registry/registry_public_images.html#public_images).
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
