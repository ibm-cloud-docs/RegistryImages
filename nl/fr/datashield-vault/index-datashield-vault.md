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

# Initiation à l'image `datashield-vault`
{: #datashield-vault_starter}

Cette image de conteneur fournit une protection pour les données en cours d'utilisation en exécutant Vault dans l'environnement {{site.data.keyword.datashield_short}}. Pour plus d'informations sur le service et ce qu'implique la protection des données en cours d'utilisation, voir la [documentation {{site.data.keyword.datashield_short}}](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

Vous pouvez accéder aux images fournies par {{site.data.keyword.IBM}} à l'aide de la ligne de commande. Voir [Images IBM publiques](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Déploiement de l'image
{: #datashield-vault-deploy}

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
<caption>Tableau 1. Variables d'entrée requises</caption>
  <tr>
    <th>Variable</th>
    <th>Description</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>Nom de l'image que vous voulez déployer.</td>
  </tr>
  <tr>
    <td><code>NODE-IP</code></td>
    <td>Adresse IP de noeud de votre instance Vault. Pour trouver cette adresse IP, vous pouvez exécuter la commande <code>kubectl get pod -owide</code> ou la commande <code>kubectl describe pod</code>.</td>
  </tr>
</table>

## Accès à une instance protégée de Vault
{: #datashield-vault-access}

Pour accéder à l'instance protégée de Vault, procédez comme suit :

1. Recherchez l'adresse IP de noeud pour votre instance en exécutant l'une des commandes suivantes.

   * Option 1 :

   ```
   kubectl get pod -owide
   ```
   {: codeblock}

   * Option 2 :
  
   ```
   kubectl describe pod
   ```
   {: codeblock}

2. Définissez la variable d'environnement `VAULT_ADDR` sur `http://<YOUR_IP>:8200`.

Vous pouvez utiliser des commandes de ligne de commande Vault comme `vault init`, `vault unseal`, `vault auth`, `vault write` et `vault read` pour authentifier les valeurs confidentielles et y accéder. Pour en savoir plus sur l'utilisation de l'interface de ligne de commande de Vault, voir [Vault Commands (CLI) ![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://www.vaultproject.io/docs/commands/index.html).
{: tip}
