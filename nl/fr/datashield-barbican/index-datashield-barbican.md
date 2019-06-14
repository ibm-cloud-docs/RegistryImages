---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-03"

keywords: IBM Cloud Container Registry, Data Shield environment, datashield-barbican image, container image, Barbican, Registry, data in use, memory encryption, Intel SGX, Fortanix,

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

# Initiation à l'image `datashield-barbican`
{: #datashield-barbican_starter}

Cette image de conteneur fournit une protection pour les données en cours d'utilisation en exécutant Barbican dans l'environnement {{site.data.keyword.datashield_short}}. Pour plus d'informations sur le service et ce qu'implique la protection des données en cours d'utilisation, voir la [documentation {{site.data.keyword.datashield_short}}](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

Vous pouvez accéder aux images fournies par {{site.data.keyword.IBM}} à l'aide de la ligne de commande. Voir [Images IBM publiques](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Déploiement de l'image
{: #datashield-barbican-deploy}

Vous pouvez utiliser les spécifications suivantes sur les pods Kubernetes pour déployer l'image :

```
apiVersion: v1
kind: Pod
metadata:
  name: data-shield-barbican
  labels:
    app: data-shield-barbican
spec:
  containers:
  - name: data-shield-barbican
    image: <IMAGE_NAME>
    volumeMounts:
    - mountPath: /dev/isgx
      name: isgx
    - mountPath: /dev/gsgx
      name: gsgx
    - mountPath: /var/run/aesmd/aesm.socket
      name: aesm-socket
    env:
    - name: NODE_IP
      valueFrom:
        fieldRef:
          fieldPath: status.hostIP
    - name: NODE_AGENT_BASE_URL
      value: http://$(NODE_IP):9092/v1
    ports:
    - containerPort: 9311
      name: barbican
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
---
apiVersion: v1
kind: Service
metadata:
  name: data-shield-barbican
  labels:
    app: data-shield-barbican
spec:
  type: LoadBalancer
  selector:
    app: data-shield-barbican
  ports:
    - name: barbican
      port: 9311
      targetPort: 9311
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
    <td><code>NODE_IP</code></td>
    <td>Adresse IP de noeud de votre instance Barbican.</td>
  </tr>
</table>

Le processus de déploiement et de démarrage du conteneur peut prendre quelques minutes. L'instance Barbican de démonstration est configurée sans authentification Keystone. Les demandes doivent inclure l'en-tête `X-Project-Id: 12345`.
{: tip}

## Création d'un secret
{: #datashield-barbican-secret}

Vous pouvez créer un secret dans l'instance Barbican de démonstration en exécutant la commande suivante :

```
curl -sS -X POST -H 'content-type:application/json' -H 'X-Project-Id: 12345' -d \
        '{"payload": "my-secret-here", "payload_content_type": "text/plain"}' \
        http://<node>:9311/v1/secrets
```
{: codeblock}

La réponse contient l'URL du secret créé. Vous pouvez extraire le secret à l'aide de la commande suivante :

```
curl -sS -H 'X-Project-Id: 12345' <url>
```
{: codeblock}
