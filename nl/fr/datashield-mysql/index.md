---

copyright:
  years: 2018
lastupdated: "2018-10-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip} 
{:table: .aria-labeledby="caption"}

# Initiation à l'image `datashield-mysql`
{: #datashield-mysql_starter}

Cette image de conteneur exécute MariaDB dans l'environnement Data Shield et offre une protection pour les données utilisées.
{:shortdesc}

Vous pouvez accéder aux images fournies par {{site.data.keyword.IBM}} à l'aide de la ligne de commande. Voir [Images IBM publiques](/docs/services/Registry/registry_public_images.html#public_images).
{: tip}

Vous pouvez utiliser les spécifications suivantes sur les pods Kubernetes pour déployer l'image :

```
    apiVersion: v1
    kind: Pod
    metadata:
      name: data-shield-mariadb
      labels:
        app: data-shield-mariadb
    spec:
      containers:
      - name: data-shield-mariadb
        image: <TODO INSERT APPROPRIATE IMAGE NAME HERE>
        volumeMounts:
        - mountPath: /dev/isgx
          name: isgx
        - mountPath: /dev/gsgx
          name: gsgx
        - mountPath: /var/run/aesmd/aesm.socket
          name: aesm-socket
        ports:
        - containerPort: 3306
          hostPort: 3306
          name: mysql
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
    
Vous pouvez vous connecter à l'instance MariaDB en exécutant la commande suivante :

```
    mysql -h <node> --protocol tcp -uroot
```
{: pre}

L'image Data Shield MariaDB nécessite que les connexions de client utilisent TLS. Suivant votre version client, il sera éventuellement nécessaire d'ajouter `--ssl` ou `--ssl-mode require` à la ligne de commande du client.
