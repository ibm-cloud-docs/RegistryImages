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

# Introduzione all'immagine `datashield-vault`
{: #datashield-vault_starter}


Questa immagine contenitore esegue Vault nell'ambiente Data Shield, fornendo la protezione dei dati in utilizzo.
{:shortdesc}

Puoi accedere alle immagini fornite da {{site.data.keyword.IBM}} utilizzando la riga di comando, consulta [Immagini pubbliche IBM](/docs/services/Registry/registry_public_images.html#public_images).
{: tip}

Puoi utilizzare le seguenti specifiche del pod Kubernetes per distribuire l'immagine:

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
    
Utilizza `kubectl get pod -owide` o `kubectl describe pod` per ricercare l'indirizzo IP del nodo della tua istanza Vault. Imposta la variabile di ambiente `VAULT_ADDR` su `http://<IP>:8200` per accedere all'istanza Vault protetta da Data Shield.

Puoi utilizzare i comandi della CLI Vault come `vault init`, `vault unseal`, `vault auth`, `vault write` e `vault read` per l'autenticazione e l'accesso ai segreti. Per ulteriori informazioni su come utilizzare la CLI Vault, consulta [Vault Commands (CLI) ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://www.vaultproject.io/docs/commands/index.html).
