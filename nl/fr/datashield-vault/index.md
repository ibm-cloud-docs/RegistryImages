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

# Initiation à l'image `datashield-vault`
{: #datashield-vault_starter}

Cette image de conteneur exécute Vault dans l'environnement Data Shield et offre une protection pour les données utilisées.
{:shortdesc}

Vous pouvez accéder aux images fournies par {{site.data.keyword.IBM}} à l'aide de la ligne de commande. Voir [Images IBM publiques](/docs/services/Registry/registry_public_images.html#public_images).
{: tip}

Vous pouvez utiliser les spécifications suivantes sur les pods Kubernetes pour déployer l'image :

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

Utilisez `kubectl get pod -owide` ou `kubectl describe pod` pour rechercher l'adresse IP de noeud pour votre instance Vault. Définissez la variable d'environnement `VAULT_ADDR` sur `http://<IP>:8200` pour accéder à l'instance Vault protégée par Data Shield.

Vous pouvez utiliser les commandes de l'interface de ligne de commande de Vault (`vault init`, `vault unseal`, `vault auth`, `vault write` et `vault read`) pour vous authentifier et accéder aux secrets. Pour en savoir plus sur l'utilisation de l'interface de ligne de commande de Vault, voir [Vault Commands (CLI) ![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://www.vaultproject.io/docs/commands/index.html).
