---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-26"

keywords: IBM Cloud Container Registry, Data Shield environment, mysql image, container image, public image

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

# Initiation à l'image `datashield-mysql`
{: #datashield-mysql_starter}

Cette image de conteneur exécute MariaDB dans l'environnement Data Shield et offre une protection pour vos données.
{:shortdesc}

Vous pouvez accéder aux images fournies par {{site.data.keyword.IBM}} à l'aide de la ligne de commande. Voir [Images IBM publiques](/docs/services/Registry?topic=registry-public_images#public_images).
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

Le tableau suivant répertorie les variables d'environnement que le conteneur MariaDB accepte :

<table>
<caption>Tableau 1. Variables d'environnement acceptées par le conteneur MariaDB</caption>
  <tr>
    <th>Variable</th>
    <th>Description</th>
  </tr>
  <tr>
    <td><code>MYSQL_ROOT_PASSWORD</code></td>
    <td>Définit le mot de passe de l'utilisateur racine MariaDB.</td>
  </tr>
  <tr>
    <td><code>MYSQL_ALLOW_EMPTY_PASSWORD</code></td>
    <td>Autorise la base de données à démarrer avec un mot de passe vide pour l'utilisateur racine. Déconseillé.</td>
  </tr>
  <tr>
    <td><code>MYSQL_RANDOM_ROOT_PASSWORD</code></td>
    <td>Génère un mot de passe aléatoire pour l'utilisateur racine MariaDB.</td>
  </tr>
  <tr>
    <td><code>MYSQL_USER</code></td>
    <td>Crée un utilisateur MariaDB normal avec le nom d'utilisateur spécifié.</td>
  </tr>
  <tr>
    <td><code>MYSQL_PASSWORD</code></td>
    <td>Mot de passe pour <code>MYSQL_USER</code>.</td>
  </tr>
  <tr>
    <td><code>MYSQL_DATABASE</code></td>
    <td>Crée une base de données vide avec le nom spécifié.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MODE</code></td>
    <td>Active la réplication. Les options incluent <code>maître</code> ou <code>esclave</code>.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_USER</code> (maître ou esclave)</td>
    <td>Définit le nom de l'utilisateur de la réplication. Cet utilisateur est créé sur le maître et est utilisé par l'esclave pour se connecter.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_PASSWORD</code> (maître ou esclave)</td>
    <td>Définit le mot de passe de l'utilisateur de la réplication. Ce mot de passe est utilisé sur le maître pour créer l'utilisateur et par l'esclave pour se connecter.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MASTER_HOST</code> (esclave)</td>
    <td>Définit le nom d'hôte pour la connexion pour réplication.</td>
  </tr>
</table>
