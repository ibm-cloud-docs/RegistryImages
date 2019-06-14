---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-03"

keywords: IBM Cloud Container Registry, Data Shield environment, container image, public image, Vault image, data in use, memory encryption, Intel SGX, Fortanix,

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
    image: <IMAGE-NAME>
    volumeMounts:
    - mountPath: /dev/isgx
      name: isgx
    - mountPath: /dev/gsgx
      name: gsgx
    - mountPath: /var/run/aesmd/aesm.socket
      name: aesm-socket
    ports:
    - containerPort: 8200
      name: vault
      protocol: TCP
    env:
    - name: NODE_IP
      valueFrom:
        fieldRef:
          fieldPath: status.hostIP
    - name: NODE_AGENT_BASE_URL
      value: http://$(NODE_IP):9092/v1
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
  name: data-shield-vault
  labels:
    app: data-shield-vault
spec:
  type: LoadBalancer
  selector:
    app: data-shield-vault
  ports:
    - name: vault
      port: 8200
      targetPort: 8200
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
  <tr>
    <td><code>NODE-IP</code></td>
    <td>Die Knoten-IP-Adresse Ihrer Vault-Instanz. Um nach der IP-Adresse zu suchen, können Sie entweder <code>kubectl get pod -owide</code> oder <code>kubectl describe pod</code> verwenden.</td>
  </tr>
</table>

## Zugriff auf eine geschützte Instanz von Vault
{: #datashield-vault-access}

Führen Sie die folgenden Schritte aus, um auf die geschützte Instanz von Vault zuzugreifen:

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

Sie können Befehle der Vaultbefehlszeilenschnittstelle, wie z. B. `vault init`, `vault unseal`, `vault auth`, `vault write` und `vault read`, für die Authentifizierung und den Zugriff auf geheime Schlüssel verwenden. Weitere Informationen zur Verwendung der Vaultbefehlszeilenschnittstelle finden Sie in [Vaultbefehle (Befehlszeilenschnittstelle) ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.vaultproject.io/docs/commands/index.html).
{: tip}
