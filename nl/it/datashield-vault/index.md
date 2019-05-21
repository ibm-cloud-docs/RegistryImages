---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-09"

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

# Introduzione all'immagine `datashield-vault`
{: #datashield-vault_starter}

Questa immagine contenitore fornisce la protezione per i dati in uso eseguendo Vault nell'ambiente {{site.data.keyword.datashield_short}}. Per ulteriori informazioni sul servizio e su cosa significa proteggere i "dati in uso", vedi la [documentazione di {{site.data.keyword.datashield_short}}](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

Puoi accedere alle immagini fornite da {{site.data.keyword.IBM}} utilizzando la riga di comando, consulta [Immagini pubbliche IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}


## Distribuzione dell'immagine
{: #datashield-vault-deploy}

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
{: codeblock}

<table>
<caption>Tabella 1. Variabili di input obbligatorie</caption>
  <tr>
    <th>Variabile</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>Il nome dell'immagine che vuoi distribuire.</td>
  </tr>
</table>

Per accedere all'istanza protetta di Vault:

1. Ricerca l'indirizzo IP del nodo per la tua istanza eseguendo uno dei seguenti comandi.

  * Opzione 1:

    ```
    kubectl get pod -owide
    ```
    {: codeblock}

  * Opzione 2:
    ```
    kubectl describe pod
    ```
    {: codeblock}

2. Imposta la variabile di ambiente `VAULT_ADDR` su `http://<YOUR_IP>:8200`.
  

Utilizza `kubectl get pod -owide` o `kubectl describe pod` per ricercare l'indirizzo IP del nodo della tua istanza Vault. Imposta la variabile di ambiente `VAULT_ADDR` su `http://<IP>:8200` per accedere all'istanza Vault protetta da {{site.data.keyword.datashield_short}}.

Puoi utilizzare i comandi della CLI Vault come `vault init`, `vault unseal`, `vault auth`, `vault write` e `vault read` per l'autenticazione e l'accesso ai segreti. Per ulteriori informazioni su come utilizzare la CLI Vault, consulta [Vault Commands (CLI) ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://www.vaultproject.io/docs/commands/index.html).
{: tip}
