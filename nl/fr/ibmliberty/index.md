---

copyright:
  years: 2017, 2018
lastupdated: "2018-07-25"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Initiation à l'image **ibmliberty**
{: #ibmliberty}

Les images IBM WebSphere Application Server Liberty \(**ibmliberty**\) sont fournies pour {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

## Fonctionnement 
{: #how_it_works}

Vous pouvez utiliser une image **ibmliberty** comme parent pour créer votre propre image et déployer vos propres applications WAR, EAR ou OSGi basées sur Java dans un conteneur IBM WebSphere Application Server Liberty.
{:shortdesc}

## Eléments inclus 
{: #whats_included}

Chaque image
Liberty contient les progiciels suivants.
{:shortdesc}

-   IBM WebSphere Application Server for Developers Liberty
-   IBM Java Runtime Environment 8.0

Les fonctions Liberty spécifiques qui sont installées dépendent
de la balise que vous sélectionnez. Le tableau suivant indique les fonctions incluses dans chaque image
**ibmliberty**. Pour plus d'informations sur chaque fonction, reportez-vous
à la section
[Fonctions Liberty
dans IBM Knowledge Center](http://www.ibm.com/support/knowledgecenter/SSAW57_8.5.5/com.ibm.websphere.wlp.nd.doc/ae/rwlp_feat.html).

|Etiquette|Description|
|---|-----------|
|Toutes les images **ibmliberty**|Toutes les images **ibmliberty** incluent les fonctions suivantes. <ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|**ibmliberty:latest**|Cette image pointe sur l'image **ibmliberty:javaee7**.|
|**ibmliberty:microProfile**|Cette image contient les options qui fournissent les fonctionnalités spécifiées par [MicroProfile](https://microprofile.io).|
|**ibmliberty:webProfile6**|Cette image inclut toutes les fonctions requises pour conformité avec Java EE6 Web Profile. Elle adjoint également des fonctions supplémentaires disponibles pour leur téléchargement dans un fichier d'exécution JAR depuis [http://wasdev.net/](http://wasdev.net/), notamment celles requises pour les applications OSGi.|
|**ibmliberty:webProfile7**|Cette image inclut toutes les fonctions requises pour conformité avec Java EE7 Web Profile.|
|**ibmliberty:javaee7**|Cette image inclut toutes les fonctions de l'image **ibmliberty:webProfile7**, en leur adjoignant celles requises pour conformité avec Java EE7 Full Platform.|

## Restrictions d'utilisation 
{: #usage}

Le tableau suivant présente les restrictions s'appliquant à l'utilisation gratuite de l'image
**ibmliberty** dans {{site.data.keyword.Bluemix_notm}}.
{:shortdesc}

**Remarque :** La tarification de l'image **ibmliberty** est indépendante de celle des conteneurs que vous utilisez dans {{site.data.keyword.Bluemix_notm}}.

|Environnement|Restrictions quant à l'utilisation gratuite|
|-----------|-----------------------|
|Développement|Utilisation gratuite **illimitée** de l'image **ibmliberty**.|
|Production|L'utilisation gratuite de l'image **ibmliberty** est limitée à **un maximum de 2 Go de segment de mémoire Java** entre toutes les instances de conteneur qui exécutent l'image. Par exemple, vous pouvez utiliser gratuitement 2 instances avec segment de mémoire de 1 Go, ou 4 instances avec segment de mémoire de 512 Mo.

Pour surveiller l'utilisation de segment de mémoire Java par vos instances de conteneur, voir [Surveillance de l'utilisation du segment de mémoire Java par un conteneur avec l'interface de ligne de commande](#monitor_heap).


Examinez les conditions d'utilisation des images certifiées IBM dans la section Licence du document [websphere-liberty image](https://hub.docker.com/_/websphere-liberty/) sur Docker Hub.

## Initiation 
{: #get_started}

Utilisez l'une des images **ibmliberty** gratuites du catalogue
{{site.data.keyword.Bluemix_notm}} ou sélectionnez votre propre image
avec licence pour environnement de production afin de créer un conteneur unique ou un groupe de conteneurs.
{:shortdesc}

**Important :** Avant de commencer, passez en revue les [restrictions d'utilisation](#usage) des images **ibmliberty**.

1.  Dans le catalogue, sélectionnez **Conteneurs** > **IBM Cloud Container Registry** > **Référentiels publics IBM** sur le panneau latéral. Recherchez l'image **ibmliberty** à partir de laquelle générer votre conteneur. Si vous avez créé votre propre image avec licence pour environnement de production et l'avez déployée dans {{site.data.keyword.Bluemix_notm}}, sélectionnez cette image dans le catalogue. La page de création de conteneur s'ouvre.
2.  Sélectionnez la version de l'image **ibmliberty** que vous désirez utiliser dans la zone déroulante
**TAG/ VERSION**.
3.  Pour plus d'informations sur la génération de conteneurs à partir d'images, la configuration de clusters et le déploiement d'applications dans des clusters, suivez les liens ci-après.

    -   [Génération de conteneurs à partir d'images](/docs/containers/cs_images.html#images)
    -   [Initiation à IBM Cloud Kubernetes Service](/docs/containers/container_index.html#container_index)
    -   [Déploiement d'applications dans des clusters](/docs/containers/cs_app.html#app)
    
    **Remarque :** L'image **ibmliberty** exige que le port 9080 soit exposé comme port public. Lorsque vous créez un conteneur depuis le tableau de bord {{site.data.keyword.Bluemix_notm}}, le port est ajouté par défaut dans la zone **Port public**. Si vous créez un conteneur depuis l'interface de ligne de commande, exposez le port dans votre commande `kubectl run` avec l'option `--port=9080`.


## Surveillance de l'utilisation du segment de mémoire Java par un conteneur avec l'interface de ligne de commande 
{: #monitor_heap}


Après avoir créé un conteneur depuis l'image **ibmliberty**, vous pouvez afficher les métriques sur un pod particulier et ses conteneurs et
examiner l'utilisation du segment de mémoire Java. Ce segment de mémoire Java représente la mémoire disponible pour l'application
Java en phase d'exécution.
{:shortdesc}

1.  Obtenez le nom du pod pour lequel vous voulez afficher les métriques.
  
    ```
    kubectl get pods
    ```

2.  Affichez les métriques sur un port particulier et ses conteneurs.

    ```
    kubectl top pod POD_NAME --containers
    ```
    {: pre}

3.  Pour examiner l'utilisation du segment de mémoire Java, vous devez accéder aux statistiques de mémoire **RSS**. Suivez les instructions sur la façon d'accéder à un shell de conteneur, proposées [ici](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/), puis prenez connaissance des [métriques d'exécution](/containers/runmetrics/#metrics-from-cgroups-memory-cpu-block-io) pour trouver et mettre en forme les informations statistiques relatives à la mémoire pour un conteneur.
L'utilisation du segment de mémoire Java est affichée en kilooctets. Si son utilisation est en-dessous de 2097152 kilooctets
(2 Go) entre toutes les instances, vous n'avez pas besoin d'acquérir une licence de WebSphere Application Server.

4.  Paramétrez l'utilisation maximale du segment de mémoire par votre instance WebSphere Application Server. Pour plus d'informations, voir
[Définition d'arguments JVM génériques dans le profil
WebSphere Application Server V8.5 Liberty](http://www-01.ibm.com/support/docview.wss?uid=swg21596474).

## Obtention d'une licence WebSphere Application Server 
{: #license}

Les licences WebSphere Application Server sont basées sur le nombre d'unités de valeur par processeur \(PVU\) dont vous avez besoin. Le PVU est une unité de mesure pour l'octroi de licences du middleware IBM. Le nombre de PVU indique le nombre de processeurs \(coeurs\) disponibles pour le logiciel.
{:shortdesc}

Chaque taille de conteneur dans {{site.data.keyword.Bluemix_notm}} requiert un nombre spécifique
d'autorisations d'utilisation de PVU qui doit être disponible dans la licence WebSphere
Application Server. Vous devez par conséquent planifier vos conteneurs **ibmliberty** avant d'acquérir la licence.

Pour acquérir une licence WebSphere Application Server, contactez le [service IBM](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us). Si vous disposez
déjà d'une licence pour WebSphere Application Server version 8.5, ou ultérieure, vous pouvez utiliser les autorisations d'utilisation de PVU inutilisées pour le
déploiement de votre conteneur.

Si vous constatez que vous avez besoin de plus de PVU après l'acquisition de la licence, vous pouvez vous en procurer d'autres en contactant le [service IBM](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us).

## Création d'une image **ibmliberty** avec licence pour environnement de production pour son utilisation avec
{{site.data.keyword.containershort_notm}} 
{: #prod_image}

Utilisez votre licence WebSphere Application Server pour créer une image
**ibmliberty** avec licence pour environnement de production que vous pourrez utiliser avec
{{site.data.keyword.containershort_notm}}. Sélectionnez l'une des tâches suivantes.
{:shortdesc}

-   [Mise à niveau de l'image Docker Hub vers une image pour
environnement de production](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade).
-   [Génération de votre propre image avec licence pour
environnement de production](https://github.com/WASdev/ci.docker/tree/master/ga/production-install).

Après avoir créé cette image production-licensed, [transférez-la vers votre référentiel privé](/docs/services/Registry/index.html) afin de l'utiliser avec {{site.data.keyword.containershort_notm}}.

## Création d'une image à partir des images fournies 
{: #creating_image}

Vous pouvez utiliser l'une des images **ibmliberty** comme parent pour créer une image enfant contenant votre propre code
d'application. Personnalisez le modèle de fichier Dockerfile et générez votre image sur votre ordinateur. Vous pouvez ensuite ajouter votre image au registre d'images privé de votre organisation et créer des conteneurs depuis cette image.
{:shortdesc}

Avant de commencer, prenez en compte les points suivants.

-   Générez votre code d'application dans un fichier WAR, EAR ou OSGi.
-   Copiez le fichier dans le répertoire depuis lequel vous désirez générer l'image.


Pour créer une image avec votre code d'application à partir de l'image **ibmliberty** :

1. Dans un éditeur de texte, créez un fichier nommé Dockerfile et copiez les informations suivantes dans ce fichier.

    ```
    FROM registry.bluemix.net/ibmliberty:<tag>
    COPY <app_name>.<file_extension> /config/dropins/
    
    ```
    {: screen}

    **Remarque :** Le répertoire /config constitue un raccourci vers /opt/ibm/wlp/usr/servers/defaultServer.
    
2. Remplacez <tag\> par la version d'image **ibmliberty** contenant les fonctions requises par votre application.

3. Remplacez <app\_name\> par le nom de votre fichier d'application.

4. Remplacez <file\_extension\> par .war, .ear ou .eba.

5. Ajoutez toutes les autres dépendances de votre application au fichier Dockerfile.

6. Générez et transférez l'image à votre registre d'images privé. Pour
plus d'informations, voir [Initiation à {{site.data.keyword.registrylong_notm}}](/docs/services/Registry/index.html).

**Remarque :** Toutes les images **ibmliberty** sont configurées pour consigner les fichiers journaux Liberty dans le répertoire /logs du conteneur. Les autres fichiers générés par le serveur Liberty sont créés dans le répertoire /opt/ibm/wlp/output/defaultServer. Vous pouvez accéder à ces fichiers à l'aide du raccourci /output.

## Référence du fichier Dockerfile **ibmliberty** 
{: #reference_dockerfile}

Ce fichier Dockerfile illustre comment l'image **ibmliberty:webProfile7** dans {{site.data.keyword.Bluemix_notm}} est générée depuis les images websphere-liberty publiques de Docker Hub. Ces informations sont fournies pour référence uniquement.
{:shortdesc}

```
FROM websphere-liberty:webProfile7

# Update packages
RUN apt-get update &&\
    DEBIAN_FRONTEND=noninteractive apt-get upgrade -y &&\
    apt-get clean &&\
    rm -Rf /var/cache/*

# Set password length and expiry for compliance with vulnerability advisor
RUN sed -i 's/^PASS_MAX_DAYS.*/PASS_MAX_DAYS   90/' /etc/login.defs
RUN sed -i 's/sha512/sha512 minlen=8/' /etc/pam.d/common-password

# Add docs label
LABEL doc.url="/docs/services/RegistryImages/ibmliberty/index.html"
```
{: screen}
