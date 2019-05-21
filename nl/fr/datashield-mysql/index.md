---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-09"

keywords: IBM Cloud Container Registry, Data Shield, data in use, memory encryption, intel sgx, fortanix, mysql image, mariaDB, container image, public image

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

# Initiation à l'image `datashield-mariadb`
{: #datashield-mariadb_starter}

Cette image de conteneur fournit une protection pour les données en cours d'utilisation en exécutant MariaDB dans l'environnement {{site.data.keyword.datashield_short}}. Pour plus d'informations sur le service et ce qu'implique la protection des données en cours d'utilisation, voir la [documentation {{site.data.keyword.datashield_short}}](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

Vous pouvez accéder aux images fournies par {{site.data.keyword.IBM}} à l'aide de la ligne de commande. Voir [Images IBM publiques](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Déploiement de l'image
{: #datashield-mariadb-deploy}

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
    image: <IMAGE_NAME>
    env:
      name: MARIADB_ROOT_PASSWORD
      value: <ROOT_PASSWORD>
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
      name: mariadb
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
    <td><code>MARIADB_ROOT_PASSWORD</code></td>
    <td>Définit le mot de passe de l'utilisateur racine MariaDB.</td>
  </tr>
  <tr>
    <td><code>ROOT_PASSWORD</code></td>
    <td>Mot de passe que vous voulez définir pour l'utilisateur racine MariaDB.</td>
  </tr>
</table>

### Variables d'environnement acceptées
{: #datashield-mariadb-variables}

Le conteneur MariaDB accepte aussi les variables d'environnement ci-après. Pour utiliser une autre variable, ajoutez-la à la spécification, comme illustré dans l'exemple suivant :

```
spec:
  containers:
  - name: data-shield-mariadb
    image: <IMAGE_NAME>
    env:
      name: MARIADB_ROOT_PASSWORD
      value: <ROOT_PASSWORD>
      name: MARIADB_USER
      value: <USER_NAME>
```
{: screen}

<table>
<caption>Tableau 2. Variables d'environnement acceptées par le conteneur MariaDB</caption>
  <tr>
    <th>Variable</th>
    <th>Description</th>
  </tr>
  <tr>
    <td><code>MARIADB_ALLOW_EMPTY_PASSWORD</code></td>
    <td>Autorise la base de données à démarrer avec un mot de passe vide pour l'utilisateur racine. Déconseillé.</td>
  </tr>
  <tr>
    <td><code>MARIADB_RANDOM_ROOT_PASSWORD</code></td>
    <td>Génère un mot de passe aléatoire pour l'utilisateur racine MariaDB.</td>
  </tr>
  <tr>
    <td><code>MARIADB_USER</code></td>
    <td>Crée un utilisateur MariaDB ordinaire avec le nom d'utilisateur spécifié.</td>
  </tr>
  <tr>
    <td><code>MARIADB_PASSWORD</code></td>
    <td>Mot de passe pour <code>MYSQL_USER</code>.</td>
  </tr>
  <tr>
    <td><code>MARIADB_DATABASE</code></td>
    <td>Crée une base de données vide avec le nom spécifié.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_MODE</code></td>
    <td>Active la réplication. Les options incluent <code>maître</code> ou <code>esclave</code>.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_USER</code> (maître ou esclave)</td>
    <td>Définit le nom de l'utilisateur de la réplication. Cet utilisateur est créé sur le maître et est utilisé par l'esclave pour se connecter.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_PASSWORD</code> (maître ou esclave)</td>
    <td>Définit le mot de passe de l'utilisateur de la réplication. Ce mot de passe est utilisé sur le maître pour créer l'utilisateur et par l'esclave pour se connecter.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_MASTER_HOST</code> (esclave)</td>
    <td>Définit le nom d'hôte auquel se connecter pour la réplication.</td>
  </tr>
</table>

## Connexion à l'instance MariaDB
{: #datashield-mariadb-connect}

Vous pouvez vous connecter à l'instance MariaDB en exécutant la commande suivante :

```
mariadb -h <node> --protocol tcp -uroot
```
{: codeblock}

L'image {{site.data.keyword.datashield_short}} MariaDB requiert que les connexions client utilisent TLS (Transport Layer Security). Suivant votre version client, il sera éventuellement nécessaire d'ajouter `--ssl` ou `--ssl-mode require` à la ligne de commande du client.
{: note}
