---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-21"

keywords: datashield-barbican image, container image, barbican, Data Shield environment, public image

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

Cette image de conteneur exécute Barbican dans l'environnement Data Shield et offre une protection pour les données utilisées.
{:shortdesc}

Vous pouvez accéder aux images fournies par {{site.data.keyword.IBM}} à l'aide de la ligne de commande. Voir [Images IBM publiques](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

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
        image: <TODO INSERT APPROPRIATE IMAGE NAME HERE>
        volumeMounts:
        - mountPath: /dev/isgx
          name: isgx
        - mountPath: /dev/gsgx
          name: gsgx
        - mountPath: /var/run/aesmd/aesm.socket
          name: aesm-socket
        ports:
        - containerPort: 9311
          hostPort: 9311
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
```
{: codeblock}

Le processus de déploiement et de démarrage du conteneur peut prendre quelques minutes. L'instance Barbican de démonstration est configurée sans authentification Keystone. Les demandes doivent inclure l'en-tête `X-Project-Id: 12345`
{: tip}

Vous pouvez créer un secret dans l'instance Barbican de démonstration en exécutant la commande suivante :

```
    curl -sS -X POST -H 'content-type:application/json' -H 'X-Project-Id: 12345' -d \
        '{"payload": "my-secret-here", "payload_content_type": "text/plain"}' \
        http://<node>:9311/v1/secrets
```
{: pre}
    
La réponse contient l'URL du secret créé. Vous pouvez extraire le secret à l'aide de la commande suivante :

```
    curl -sS -H 'X-Project-Id: 12345' <url>
```
{: pre}
