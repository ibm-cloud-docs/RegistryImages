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

# Einführung zum Image `datashield-vault`
{: #datashield-vault_starter}

Dieses Container-Image bietet den verwendeten Daten durch die Vault-Ausführung in der {{site.data.keyword.datashield_short}}-Umgebung Schutz. Weitere Informationen zu dem Service und die Bedeutung des Schutzes von "verwendeten Daten" finden Sie in der [{{site.data.keyword.datashield_short}}-Dokumentation](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

Sie können auf die von {{site.data.keyword.IBM}} bereitgestellten Images über die Befehlszeile zugreifen. Informationen hierzu finden Sie in [öffentliche IBM Images](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}


## Image bereitstellen
{: #datashield-vault-deploy}

Mithilfe der folgenden Kubernetes-Podspezifikationen können Sie das Image bereitstellen:

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
<caption>Tabelle 1. Erforderliche Eingabevariablen</caption>
  <tr>
    <th>Variable</th>
    <th>Beschreibung</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>Der Name des Image, das Sie bereitstellen möchten.</td>
  </tr>
</table>

Gehen Sie wie folgt vor, um auf die geschützte Instanz von Vault zuzugreifen:

1. Suchen Sie nach der Knoten-IP-Adresse Ihrer Instanz, indem Sie einen der folgenden Befehle ausführen.

  * Option 1:

    ```
    kubectl get pod -owide
    ```
    {: codeblock}

  * Option 2:
    ```
    kubectl describe pod
    ```
    {: codeblock}

2. Legen Sie für die Umgebungsvariable `VAULT_ADDR` den Wert `http://<YOUR_IP>:8200` fest.
  

Verwenden Sie `kubectl get pod -owide` oder `kubectl describe pod`, um die Knoten-IP-Adresse für Ihre Vaultinstanz abzurufen. Legen Sie für die Umgebungsvariable `VAULT_ADDR` den Wert `http://<IP>:8200` fest, um auf die durch {{site.data.keyword.datashield_short}} geschützte Vault-Instanz zuzugreifen.

Sie können Befehle der Vaultbefehlszeilenschnittstelle, wie z. B. `vault init`, `vault unseal`, `vault auth`, `vault write` und `vault read`, für die Authentifizierung und den Zugriff auf geheime Schlüssel verwenden. Weitere Informationen zur Verwendung der Vaultbefehlszeilenschnittstelle finden Sie in [Vaultbefehle (Befehlszeilenschnittstelle) ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.vaultproject.io/docs/commands/index.html).
{: tip}
