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

# Einführung zum Image `datashield-vault`
{: #datashield-vault_starter}

Mit diesem Container-Image wird Vault in der Data Shield-Umgebung ausgeführt und bietet Schutz für Daten, die sich in Gebrauch befinden.
{:shortdesc}

Sie können auf die von {{site.data.keyword.IBM}} bereitgestellten Images über die Befehlszeile zugreifen. Informationen hierzu finden Sie in [öffentliche IBM Images](/docs/services/Registry/registry_public_images.html#public_images).
{: tip}

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
        image: <Entsprechenden Imagenamen hier einfügen>
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

Verwenden Sie `kubectl get pod -owide` oder `kubectl describe pod`, um die Knoten-IP-Adresse für Ihre Vaultinstanz abzurufen. Legen Sie für die Umgebungsvariable `VAULT_ADDR` den Wert `http://<IP>:8200` fest, um auf die durch Data Shield geschützte Vaultinstanz zuzugreifen.

Sie können Befehle der Vaultbefehlszeilenschnittstelle, wie z. B. `vault init`, `vault unseal`, `vault auth`, `vault write` und `vault read`, für die Authentifizierung und den Zugriff auf geheime Schlüssel verwenden. Weitere Informationen zur Verwendung der Vaultbefehlszeilenschnittstelle finden Sie in [Vaultbefehle (Befehlszeilenschnittstelle) ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.vaultproject.io/docs/commands/index.html).
