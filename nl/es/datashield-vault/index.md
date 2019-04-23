---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-13"

keywords: IBM Cloud Container Registry, Data Shield environment, container image, public image, vault image, data in use, memory encryption, intel sgx, fortanix,

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

# Iniciación a la imagen `datashield-vault`
{: #datashield-vault_starter}

Esta imagen de contenedor proporciona protección para datos en uso al ejecutar Vault en el entorno Data Shield. Para obtener más información sobre el servicio y qué significa proteger "datos en uso", consulte la [documentación de IBM Cloud Data Shield](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

Puede acceder a las imágenes que suministra {{site.data.keyword.IBM}} desde la línea de mandatos; consulte las [imágenes públicas de IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}


## Despliegue de la imagen
{: #datashield-vault-deploy}

Puede utilizar la siguiente especificación de pod de Kubernetes para desplegar la imagen:

```
    apiVersion: v1
    kind: Pod
    metadata:
      name: data-shield-vault
      labels:
        app: data-shield-vault
    spec:
      containers:
      - name: data-shield-vault
        image: <IMAGE_NAME>
        volumeMounts:
        - mountPath: /dev/isgx
          name: isgx
        - mountPath: /dev/gsgx
          name: gsgx
        - mountPath: /var/run/aesmd/aesm.socket
          name: aesm-socket
        ports:
        - containerPort: 8200
          hostPort: 8200
          name: vault
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
{: pre}

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

Para acceder a la instancia protegida de Vault:

1. Busque la dirección IP del nodo para su instancia ejecutando uno de los siguientes mandatos.

  * Opción 1:

    ```
    kubectl get pod -owide
    ```
    {: pre}

  * Opción 2:
    ```
    kubectl describe pod
    ```
    {: pre}

2. Establezca la variable de entorno `VAULT_ADDR` en `http://<YOUR_IP>:8200`.
  

Utilice `kubectl get pod -owide` o `kubectl describe pod` para buscar la dirección IP del nodo correspondiente a su instancia de Vault. Establezca la variable de entorno `VAULT_ADDR` en `http://<IP>:8200` para acceder a la instancia de Vault protegida por Data Shield.

Puede utilizar mandatos de CLI de Vault, como `vault init`, `vault unseal`, `vault auth`, `vault write` y `vault read`, para autenticar y acceder a secretos. Para obtener más información sobre cómo utilizar la CLI de Vault, consulte [Mandatos de Vault (CLI) ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.vaultproject.io/docs/commands/index.html).
{: tip}
