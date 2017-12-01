---

copyright:
  years: 2015, 2017
lastupdated: "2017-10-30"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}

# Initiation avec l'image ibm-node-strong-pm
{: #getting_started_node_strong}

L'image {{site.data.keyword.IBM}} Node avec StrongLoop \(**ibm-node-strong-pm**\) est fournie pour {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

## Fonctionnement 
{: #how_it_works}

Utilisez l'image **ibm-node-strong-pm** pour créer un conteneur unique déployant une instance en cours de [StrongLoop Process Manager](https://www.strongloop.com). Strongloop Process Manager vous permet de déployer, de gérer et de surveiller les applications Node.js dans le cloud {{site.data.keyword.IBM_notm}} implémentées sur une machine distante.
{:shortdesc}

Une instance de StrongLoop Arc doit avoir été installée sur la machine distante. StrongLoop Arc est une interface graphique
destinée à la plateforme StrongLoop et inclut des outils pour la construction, le profilage et la surveillance d'applications Node.js. Après avoir construit une
application avec StrongLoop Arc, vous devez la transférer et la déployer sur le conteneur dans {{site.data.keyword.Bluemix_notm}}
qui exécute StrongLoop Process Manager. Pour établir une connexion entre la machine distante et le conteneur, vous devez lier une adresse IP publique au conteneur et
l'associer à votre serveur StrongLoop
Process Manager dans StrongLoop Arc. Une fois votre application déployée via StrongLoop Process
Manager, vous pouvez surveiller les métriques et les performances de votre application dans StrongLoop Process Manager. Pour mettre à jour ou redimensionner votre application, vous devez la modifier à l'aide de StrongLoop Arc, puis propager vos modifications au conteneur.

**Remarque :** L'image **ibm-node-strong-pm** n'est pas prise en charge pour les groupes de conteneurs dans {{site.data.keyword.Bluemix_notm}}.

## Eléments inclus
{: #whats_included}

-   Tous les progiciels inclus dans l'image **ibmnode:latest**. Voir
[Eléments inclus](/docs/services/RegistryImages/ibmnode/index.html#getting_started_node) dans la rubrique Initiation à l'image
**ibmnode** pour {{site.data.keyword.Bluemix_notm}}.
-   StrongLoop Process Manager

## Initiation
{: #how_to_get_started}

Vous pouvez utiliser l'image **ibm-node-strong-pm** du catalogue {{site.data.keyword.Bluemix_notm}} pour vous familiariser avec un conteneur unique dans {{site.data.keyword.containershort_notm}}. L'image est
démarrée avec une instance en cours d'exécution de StrongLoop Process Manager, à laquelle vous pouvez accéder dès que le conteneur est disponible. L'utilisation d'une instance de StrongLoop Arc qui est connectée à Process
Manager vous permet d'envoyer une application Node.js par commande push et d'y accéder.
{:shortdesc}

**Remarque :** Avant de commencer, consultez les conditions d'utilisation des images certifiées {{site.data.keyword.IBM_notm}} à la section Utilisation de la [bibliothèque websphere-liberty Docker](https://github.com/docker-library/docs/tree/master/websphere-liberty).

1.  Dans le catalogue, sélectionnez **Conteneurs** et choisissez l'image **ibm-node-strong-pm** à partir de laquelle générer votre conteneur.
2.  Créez un conteneur unique.
    -   Dans la zone **Taille**, sélectionnez une taille de conteneur avec au moins 1 Go de mémoire \(**Petit**\).
    -   Dans la zone **Ports publics**, indiquez les ports 8701 et 3001. Ces ports sont exposés automatiquement lorsque vous déployez votre
conteneur dans {{site.data.keyword.Bluemix_notm}} et constituent un prérequis pour lier une adresse IP
publique au conteneur. Le port 8701 est utilisé pour accéder à StrongLoop Process Manager dans le conteneur. Le port 3001 est celui sur lequel l'application est à
l'écoute après son déploiement sur StrongLoop Process Manager.

        **Remarque :** Pour déployer des applications supplémentaires sur StrongLoop Process Manager, exposez d'autres ports. Les ports doivent être consécutifs, en débutant par le port 3002. Par exemple, 3002, 3003, 3004 etc.

    -   Pour plus d'informations, voir [Création d'un conteneur unique à l'aide du tableau de bord {{site.data.keyword.Bluemix_notm}}](/docs/containers/container_single_ui.html#gui).
3.  Liez une adresse IP publique au conteneur. Pour plus d'informations, voir [Demande et liaison d'adresses
IP](/docs/containers/container_single_ui.html#container_cli_ips).

## Configuration de StrongLoop Arc sur une machine distante
{: #UsingSLA}

Après avoir créé un conteneur unique depuis l'image **ibm-node-strong-pm** dans
{{site.data.keyword.Bluemix_notm}}, configurez StrongLoop Arc sur votre machine distante.
{:shortdesc}

Avant de commencer, installez Node.js et le gestionnaire de package Node \(npm\) sur votre machine distante.

1.  Téléchargez et installez StrongLoop en entrant la commande suivante. StrongLoop inclut tous les packages requis pour exécuter Arc.

    ```
    npm install -g strongloop
    ```
    {: pre}

2.  Une fois StrongLoop installé, exécutez StrongLoop Arc.

    ```
    slc arc
    ```
    {: pre}

3.  A l'invite, enregistrez-vous auprès de StrongLoop ou connectez-vous.
4.  Ajoutez le gestionnaire StrongLoop Process Manager s'exécutant dans votre conteneur à la liste Process Manager dans
StrongLoop Arc.
    1.  Dans le sélecteur de module Arc, accédez à **Process Manager**.
    2.  Ajoutez l'adresse IP publique que vous avez liée à votre conteneur et le port public 8701 à Arc Process Manager.
    3.  Cliquez sur **Activate**.

## Propagation d'une application Node.js à votre conteneur à l'aide de StrongLoop Arc
{: #pushing_app_to_container}

Après vous être connecté au StrongLoop Process Manager s'exécutant dans votre conteneur depuis le client StrongLoop Arc sur votre machine distante,
construisez et propagez vos applications Node.js à {{site.data.keyword.Bluemix_notm}}.
{:shortdesc}

1.  Dans le sélecteur de module Arc, accédez à **Build and Deploy**.
2.  Générez votre application en suivant les instructions de l'interface utilisateur Arc.

    **Important :** Vérifiez que l'application Node.js que vous avez créée avec StrongLoop Arc est à l'écoute à la fois sur la variable d'environnement PORT et sur le port que vous avez exposé lors de la création du conteneur.

    Exemple

    ```
    app.listen(process.env.PORT || 3001);
    ```
    {: pre}

3.  Déployez votre application en suivant les instructions de l'interface utilisateur Arc. Sélectionnez le gestionnaire de processus que vous avez configuré pour
déployer votre application sur {{site.data.keyword.Bluemix_notm}}.
4.  Attendez la fin du processus de déploiement.
5.  Accédez à votre application.
    1.  Ouvrez un navigateur web.
    2.  Entrez l'adresse IP et le port de votre conteneur sous le format suivant :
<var class="keyword varname">ADRESSE_IP:PORT</var>.

        Exemple

        ```
        192.0.2.26:3001
        ```
        {: pre}

    3.  Répétez ces étapes pour chaque application que vous déployez sur {{site.data.keyword.Bluemix_notm}}.
6.  Utilisez les fonctions de trace, de métriques et de profilage Arc pour obtenir des informations de performances sur vos applications alors qu'elles s'exécutent
sur {{site.data.keyword.Bluemix_notm}}. Pour plus d'informations, voir [Operating Node applications](https://docs.strongloop.com/display/SLC/Operating+Node+applications) dans la documentation StrongLoop.

## Informations de référence du fichier Dockerfile de **ibm-node-strong-pm** 
{: #reference_dockerfile}

Ce fichier Dockerfile a été utilisé pour créer l'image **ibm-node-strong-pm** dans le catalogue {{site.data.keyword.Bluemix_notm}}. Ces informations sont fournies pour référence uniquement. Les autres fichiers inclus dans la version générée de cette image ne sont pas fournis.

```
FROM registry.ng.bluemix.net/ibmnode:latest
RUN useradd -ms /bin/bash strong-pm \    
    && chown -R strong-pm:strong-pm /usr/local \    
    && su strong-pm -c "npm install -g strong-pm && npm cache clear"

# Set up some semblance of an environment
WORKDIR /home/strong-pm
ENV HOME=/home/strong-pm PORT=3000

# Run as non-privileged user inside container
USER strong-pm

# Expose strong-pm and application ports
EXPOSE 8701
EXPOSE 3001-3010

ENTRYPOINT ["/usr/local/bin/sl-pm", "--base", ".", "--listen", "8701"]
```
{: screen}


