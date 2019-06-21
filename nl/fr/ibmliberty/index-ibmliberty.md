---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-04"

keywords: IBM Cloud Container Registry, IBM Liberty, ibmliberty, container image, IBM WebSphere Application Server Liberty, liberty, public image

subcollection: RegistryImages

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:download: .download}

# Initiation à l'image `ibm/liberty`
{: #ibmliberty}

Les images IBM WebSphere Application Server Liberty \(`ibm/liberty`\) sont fournies pour {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

Vous pouvez accéder aux images fournies par {{site.data.keyword.IBM_notm}} à l'aide de la ligne de commande. Voir [Images IBM publiques](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Fonctionnement
{: #ibmliberty_how_it_works}

Vous pouvez utiliser une image `ibm/liberty` comme parent pour créer votre propre image et déployer vos propres applications WAR, EAR ou OSGi basées sur Java dans un conteneur IBM WebSphere Application Server Liberty.
{:shortdesc}

## Eléments inclus
{: #ibmliberty_whats_included}

Chaque image
Liberty contient les progiciels suivants.
{:shortdesc}

- IBM WebSphere Application Server for Developers Liberty
- IBM Java Runtime Environment 8.0

Les fonctions Liberty spécifiques qui sont installées dépendent
de la balise que vous sélectionnez. Le tableau suivant indique les fonctions incluses dans chaque image `ibm/liberty`. Pour plus d'informations sur chaque fonction, voir la section [Fonctions Liberty dans IBM Knowledge Center ![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/SSAW57_liberty/com.ibm.websphere.wlp.nd.multiplatform.doc/ae/rwlp_feat.html).

|Etiquette|Description|
|---|-----------|
|Toutes les images `ibm/liberty`|Toutes les images `ibm/liberty` incluent les fonctions suivantes. <ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|`ibm/liberty:latest`|Cette image pointe sur l'image `ibm/liberty:javaee8`.|
|`ibm/liberty:microProfile1`|Cette image contient les options qui fournissent les fonctionnalités spécifiées par [MicroProfile 1.x ![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://microprofile.io/).|
|`ibm/liberty:microProfile2`|Cette image contient les options qui fournissent les fonctionnalités spécifiées par [MicroProfile 2.x ![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://microprofile.io/).|
|`ibm/liberty:springBoot1`|Cette image contient les options qui fournissent les fonctionnalités spécifiées par [Spring Boot 1.x ![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://spring.io/projects/spring-boot).|
|`ibm/liberty:springBoot2`|Cette image contient les options qui fournissent les fonctionnalités spécifiées par [Spring Boot 2.x ![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://spring.io/projects/spring-boot).|
|`ibm/liberty:webProfile7`|Cette image inclut toutes les fonctions requises pour conformité avec Java EE 7 Web Profile.|
|`ibm/liberty:webProfile8`|Cette image inclut toutes les fonctions requises pour conformité avec Java EE 8 Web Profile.|
|`ibm/liberty:javaee7`|Cette image inclut toutes les fonctions de l'image `ibm/liberty:webProfile7`, plus celles requises pour conformité avec Java EE 7 Full Platform.|
|`ibm/liberty:javaee8`|Cette image inclut toutes les fonctions de l'image `ibm/liberty:webProfile8`, plus celles requises pour conformité avec Java EE 8 Full Platform.|
{: caption="Tableau 1. Fonctions incluses dans chaque image <codeibm/liberty</code>" caption-side="top"}

## Restrictions d'utilisation
{: #ibmliberty_usage}

Le tableau suivant présente les restrictions s'appliquant à l'utilisation gratuite de l'image `ibm/liberty` dans {{site.data.keyword.cloud_notm}}.
{:shortdesc}

La tarification de l'image `ibm/liberty` est indépendante de celle des conteneurs que vous utilisez dans {{site.data.keyword.cloud_notm}}.
{:tip}

|Environnement|Restrictions quant à l'utilisation gratuite|
|-----------|-----------------------|
|Développement|Utilisation gratuite **illimitée** de l'image `ibm/liberty`.|
|Production|L'utilisation gratuite de l'image `ibm/liberty` est limitée à un **maximum de 2 Go de segment de mémoire Java** entre toutes les instances de conteneur qui exécutent l'image. Par exemple, vous pouvez utiliser gratuitement 2 instances avec segment de mémoire de 1 Go, ou 4 instances avec segment de mémoire de 512 Mo.|
{: caption="Tableau 2. Tarification" caption-side="top"}

Pour surveiller l'utilisation de segment de mémoire Java par vos instances de conteneur, voir [Surveillance de l'utilisation du segment de mémoire Java par un conteneur avec l'interface de ligne de commande](#ibmliberty_monitor_heap).

Examinez les conditions d'utilisation des images certifiées IBM dans la section Licence du document [websphere-liberty image ![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://hub.docker.com/_/websphere-liberty/) sur Docker Hub.

## Initiation
{: #ibmliberty_get_started}

Utilisez l'une des images `ibm/liberty` gratuites du catalogue {{site.data.keyword.cloud_notm}} ou sélectionnez votre propre image pour environnement de production afin de créer un conteneur unique ou un groupe de conteneurs.
{:shortdesc}

Avant de commencer, passez en revue les [restrictions d'utilisation](#ibmliberty_usage) applicables aux images `ibm/liberty`.
{: important}

1. Dans le catalogue, sélectionnez **Conteneurs** > **IBM Cloud Container Registry** > **Référentiels publics IBM** sur le panneau latéral. Recherchez l'image `ibm/liberty` à partir de laquelle générer votre conteneur. Si vous avez créé votre propre image avec licence pour environnement de production et l'avez déployée dans {{site.data.keyword.cloud_notm}}, sélectionnez cette image dans le catalogue. La page de création de conteneur s'ouvre.
2. Dans la liste **TAG/VERSION**, sélectionnez la version de l'image `ibm/liberty` que vous voulez utiliser.
3. Pour plus d'informations sur la génération de conteneurs à partir d'images, la configuration de clusters et le déploiement d'applications dans des clusters, utilisez les liens suivants :

    - [Génération de conteneurs à partir d'images](/docs/containers?topic=containers-images#images)
    - [Initiation à IBM Cloud Kubernetes Service](/docs/containers?topic=containers-getting-started#getting-started)
    - [Déploiement d'applications dans des clusters](/docs/containers?topic=containers-app#app)

    L'image `ibm/liberty` nécessite que le port 9080 soit public. Lorsque vous créez un conteneur depuis le tableau de bord {{site.data.keyword.cloud_notm}}, le port est ajouté par défaut dans la zone **Port public**. Si vous créez un conteneur depuis l'interface de ligne de commande, définissez le port comme public en exécutant la commande `kubectl run` avec l'option `--port=9080`.
    {:tip}

## Surveillance de l'utilisation du segment de mémoire Java par un conteneur avec l'interface de ligne de commande
{: #ibmliberty_monitor_heap}

Après avoir créé un conteneur depuis l'image `ibm/liberty`, vous pouvez afficher les métriques sur un pod particulier et ses conteneurs et examiner l'utilisation du segment de mémoire Java. Ce segment de mémoire Java représente la mémoire disponible pour l'application
Java en phase d'exécution.
{:shortdesc}

1. Obtenez le nom du pod pour lequel vous voulez afficher les métriques.
  
   ```
   kubectl get pods
   ```
   {: pre}

2. Affichez les métriques sur un port particulier et ses conteneurs.

   ```
   kubectl top pod POD_NAME --containers
   ```
   {: pre}

3. Pour examiner l'utilisation du segment de mémoire Java, vous devez accéder aux statistiques de mémoire **RSS** (voir [Get a Shell to a Running Container ![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/)). L'utilisation du segment de mémoire Java est affichée en kilooctets (ko). Si son utilisation est en-dessous de 2097152 ko (2 Go) entre toutes les instances, vous n'avez pas besoin d'acquérir une licence de WebSphere Application Server.

4. Paramétrez l'utilisation maximale du segment de mémoire par votre instance WebSphere Application Server. Pour plus d'informations, voir [Définition d'arguments JVM génériques dans le profil WebSphere Application Server V8.5 Liberty![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://www-01.ibm.com/support/docview.wss?uid=swg21596474).

## Obtention d'une licence WebSphere Application Server
{: #ibm/liberty_license}

Les licences WebSphere Application Server sont basées sur le nombre d'unités de valeur par processeur \(PVU\) dont vous avez besoin. Le PVU est une unité de mesure pour l'octroi de licences du middleware IBM. Le nombre de PVU indique le nombre de processeurs \(coeurs\) disponibles pour le logiciel.
{:shortdesc}

Chaque taille de conteneur dans {{site.data.keyword.cloud_notm}} requiert un nombre spécifique
d'autorisations d'utilisation de PVU qui doit être disponible dans la licence WebSphere
Application Server. Par conséquent, vous devez planifier vos conteneurs `ibm/liberty` avant d'acquérir la licence.

Pour acquérir une licence WebSphere Application Server, contactez le [service IBM![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://cloud.ibm.com/catalog/services/websphere-application-server). Si vous disposez
déjà d'une licence pour WebSphere Application Server version 8.5, ou ultérieure, vous pouvez utiliser les autorisations d'utilisation de PVU inutilisées pour le
déploiement de votre conteneur.

Si vous constatez que vous avez besoin de plus de PVU après l'acquisition de la licence, vous pouvez vous en procurer d'autres en contactant le [service IBM![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://cloud.ibm.com/catalog/services/websphere-application-server).

## Création d'une image `ibm/liberty` avec licence pour environnement de production pour son utilisation avec {{site.data.keyword.containershort_notm}}
{: #ibmliberty_prod_image}

Utilisez votre licence WebSphere Application Server pour créer une image `ibm/liberty` avec licence pour environnement de production que vous pourrez utiliser avec {{site.data.keyword.containershort_notm}}. Sélectionnez l'une des tâches suivantes.
{:shortdesc}

- [Mise à niveau de l'image Docker Hub vers une image pour environnement de production ![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade).
- [Génération de votre propre image avec licence pour environnement de production ![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://github.com/WASdev/ci.docker/tree/master/ga).

Après avoir créé cette image production-licensed, [transférez-la vers votre référentiel privé](/docs/services/Registry?topic=registry-getting-started#getting-started) afin de l'utiliser avec {{site.data.keyword.containershort_notm}}.

## Création d'une image à partir des images fournies
{: #ibmliberty_creating_image}

Vous pouvez utiliser l'une des images `ibm/liberty` comme parent pour créer une image enfant contenant votre propre code d'application. Personnalisez le modèle de fichier Docker et générez votre image sur votre ordinateur. Vous pouvez ensuite ajouter votre image au registre d'images privé de votre organisation et créer des conteneurs depuis cette image.
{:shortdesc}

Avant de commencer, prenez en compte les points suivants.

- Générez votre code d'application dans un fichier WAR, EAR ou OSGi.
- Copiez le fichier dans le répertoire depuis lequel vous désirez générer l'image.

Pour créer une image avec votre code d'application à partir de l'image `ibm/liberty`, procédez comme suit :

1. Dans un éditeur de texte, créez un fichier nommé `Dockerfile` et copiez les informations suivantes dans ce fichier.

   ```
   FROM icr.io/ibm/liberty:<tag>
   COPY <app_name>.<file_extension> /config/dropins/
   ```
   {: pre}

    Le répertoire `/config` est un raccourci vers `/opt/ibm/wlp/usr/servers/defaultServer`.
    {: note}

2. Remplacez `<tag>` par la version de l'image `ibm/liberty` incluant les fonctions requises par votre application.

3. Remplacez `<app_name>` par le nom de votre fichier d'application.

4. Remplacez `<file_extension>` par `.war`, `.ear` ou `.eba`.

5. Ajoutez toutes les autres dépendances de votre application au fichier Docker.

6. Générez et transférez l'image à votre registre d'images privé. Pour
plus d'informations, voir [Initiation à {{site.data.keyword.registrylong_notm}}](/docs/services/Registry?topic=registry-getting-started#getting-started).

Toutes les images `ibm/liberty` sont configurées pour consigner les fichiers journaux Liberty dans le répertoire `/logs` du conteneur. Les autres fichiers générés par le serveur
Liberty sont créés dans le répertoire `/opt/ibm/wlp/output/defaultServer`. Vous pouvez accéder à ces fichiers à l'aide du raccourci `/output`.
{:tip}

## Référence du fichier Docker `ibm/liberty`
{: #ibmliberty_reference_dockerfile}

Les fichiers Docker d'image `ibm/liberty` se trouvent dans le répertoire GitHub [WASdev/ci.docker![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://github.com/WASdev/ci.docker/tree/master/ga) pour référence.
{:shortdesc}