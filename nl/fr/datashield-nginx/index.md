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

# Initiation à l'image `datashield-nginx`
{: #datashield-nginx_starter}

Cette image de conteneur exécute NGINX dans l'environnement Data Shield et offre une protection pour les données utilisées.
{:shortdesc}

Vous pouvez accéder aux images fournies par {{site.data.keyword.IBM}} à l'aide de la ligne de commande. Voir [Images IBM publiques](/docs/services/Registry/registry_public_images.html#public_images).
{: tip}

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
        image: <TODO INSERT APPROPRIATE IMAGE NAME HERE>
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
{: codeblock}

Pour fournir vos propres contenus à NGINX, placez-les sous `/usr/local/nginx/html` dans le conteneur.
