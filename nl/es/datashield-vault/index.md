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

# Iniciación a la imagen `datashield-vault`
{: #datashield-vault_starter}


Esta imagen del contenedor ejecuta Vault en el entorno Data Shield, lo que protege los datos utilizados.
{:shortdesc}

Puede acceder a las imágenes que suministra {{site.data.keyword.IBM}} desde la línea de mandatos; consulte las [imágenes públicas de IBM](/docs/services/Registry/registry_public_images.html#public_images).
{: tip}

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
        image: <TODO INSERT APPROPRIATE IMAGE NAME HERE>
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
{: codeblock}    
    
Utilice `kubectl get pod -owide` o `kubectl describe pod` para buscar la dirección IP del nodo correspondiente a su instancia de Vault. Establezca la variable de entorno `VAULT_ADDR` en `http://<IP>:8200` para acceder a la instancia de Vault protegida por Data Shield.

Puede utilizar mandatos de CLI de Vault, como `vault init`, `vault unseal`, `vault auth`, `vault write` y `vault read`, para autenticar y acceder a secretos. Para obtener más información sobre cómo utilizar la CLI de Vault, consulte [Mandatos de Vault (CLI) ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.vaultproject.io/docs/commands/index.html).
