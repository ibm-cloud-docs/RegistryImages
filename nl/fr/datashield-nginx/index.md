---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-27"

keywords: IBM Cloud Container Registry, Data Shield environment, nginx image, container image, public image, data in use, memory encryption, intel sgx, fortanix,

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

# Initiation à l'image `datashield-nginx`
{: #datashield-nginx_starter}

Cette image de conteneur fournit une protection pour les données en cours d'utilisation en exécutant NGINX dans l'environnement Data Shield. Pour plus d'informations sur le service et ce qu'implique la protection des données en cours d'utilisation, voir la [documentation IBM Cloud Data Shield](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

Vous pouvez accéder aux images fournies par {{site.data.keyword.IBM}} à l'aide de la ligne de commande. Voir [Images IBM publiques](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Déploiement de l'image
{: #datashield-nginx-deploy}

Vous pouvez utiliser les spécifications suivantes sur les pods Kubernetes pour déployer l'image :

```
    apiVersion: v1
    kind: Pod
    metadata:
      name: data-shield-nginx
      labels:
        app: data-shield-nginx
    spec:
      containers:
      - name: data-shield-nginx
        image: <IMAGE_NAME>
        volumeMounts:
        - mountPath: /dev/isgx
          name: isgx
        - mountPath: /dev/gsgx
          name: gsgx
        - mountPath: /var/run/aesmd/aesm.socket
          name: aesm-socket
        ports:
        - containerPort: 80
          hostPort: 80
          name: http
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
<caption>Tableau 1. Variables d'entrée requises</caption>
  <tr>
    <th>Variable</th>
    <th>Description</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>Nom de l'image que vous voulez déployer.</td>
  </tr>
</table>

Pour fournir vos propres contenus à NGINX, placez-les sous `/usr/local/nginx/html` dans le conteneur.
{: tip}
