---

copyright:
  years: 2016, 2017
lastupdated: "2017-10-25"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# Initiation à l'image *ibm-integration-bus*
{: #iib}

L'image **ibm-integration-bus** est fournie pour {{site.data.keyword.containerlong}}. Elle inclut l'édition IBM Integration Bus for Developers.
{:shortdesc}


## Fonctionnement
{: #how_it_works}

IBM Integration Bus for Developers contient tout ce dont vous avez besoin pour commencer à développer vos propres solutions d'intégration.
{:shortdesc}

Après avoir créé une solution d'intégration, vous pouvez utiliser l'image **ibm-integration-bus** pour mettre à disposition un conteneur unique dans {{site.data.keyword.Bluemix_notm}}. Vous pouvez ensuite déployer votre solution d'intégration dans ce conteneur en utilisant l'interface utilisateur Web ou à partir d'un terminal.

**Remarque** : Vous pouvez utiliser l'image **ibm-integration-bus** uniquement à des fins de développement et de test unitaire. Vous pouvez également utiliser l'image pour explorer le produit, dérouler des tutoriels et évaluer la contribution d'IBM Integration Bus à votre organisation.


## Eléments inclus
{: #whats_included}

Cette image IBM Integration Bus contient le package logiciel pour IBM Integration Bus for Developers version 10 \(également appelée Developer Edition\), version complète du produit que vous pouvez utiliser pour le développement et les tests unitaires. Vous pouvez télécharger gratuitement cette version et l'utiliser conformément aux dispositions de la licence.
{:shortdesc}

IBM Integration Bus for Developers version 10.0 est disponible sur les systèmes d'exploitation Windows™ 64 bits et Linux™ x86-64. Tous les services prérequis du produit sont inclus dans le package de téléchargement.

Pour toute information sur les fonctions incluses dans IBM Integration Bus for Developers, voir [IBM Integration Bus dans IBM Knowledge Center ![External link icon](../../../icons/launch-glyph.svg "External link icon")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.msgbroker.helphome.doc/help_home_msgbroker.htm){: new_window}.

Vous pouvez accéder à Developer Edition à partir de la page de téléchargement d'[IBM Integration Bus for Developers![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/marketing/iwm/iwm/web/pick.do?source=swg-wmbfd){: new_window}.


## Restrictions d'utilisation
{: #usage}

Le tableau ci-après présente les restrictions qui s'appliquent à l'utilisation gratuite de l'image
**ibm-integration-bus** dans {{site.data.keyword.Bluemix_notm}}.
{:shortdesc}

**Remarque** : La tarification de l'image **ibm-integration-bus** est indépendante de celle des conteneurs que vous utilisez dans {{site.data.keyword.Bluemix_notm}}.

|Environnement|Restrictions quant à l'utilisation gratuite|
|-----------|-----------------------|
|Développement|Utilisation gratuite **illimitée** de l'image **ibm-integration-bus** pour créer des conteneurs uniques à des fins de développement et de test unitaire.|
|Production|Developer Edition est limité au traitement d'une transaction par seconde et son utilisation n'est pas prise en charge dans un environnement de production.|
{: caption="Tableau 1. Restrictions quant à l'utilisation gratuite de l'image ibm-integration-bus " caption-side="top"}


## Licence
{: #license}

Informations sur les licences.
{:shortdesc}

*   Les documents Dockerfile et les scripts qui leur sont associés sous licence [Apache License 2.0 ![External link icon](../../../icons/launch-glyph.svg "External link icon")](http://www.apache.org/licenses/LICENSE-2.0.html){: new_window}.
*   [Informations sur la licence pour IBM Integration Bus for Developers version 10.0 ![External link icon](../../../icons/launch-glyph.svg "External link icon")](http://www.ibm.com/software/sla/sladb.nsf/displaylis/1BD32B141C9FDAE38525802C0055A956?OpenDocument){: new_window}.

    **Remarque** : La licence ne permet pas de distribution supplémentaire. Les dispositions relatives à IBM Integration Bus dans l'image limitent l'utilisation à des fins de développement et de test unitaire.


## Initiation
{: #get_started}

Utilisez l'image **ibm-integration-bus** du catalogue {{site.data.keyword.Bluemix_notm}} ou sélectionnez votre propre image avec licence pour environnement de production afin de créer un conteneur unique.
{:shortdesc}

**Important** : Avant de commencer, passez en revue les [restrictions d'utilisation](#usage) de l'image **ibm-integration-bus**.

**Remarque** : Vous pouvez utiliser des commandes Docker natives dans toutes les étapes accompagnées d'un astérisque (\*).

   Procédez comme suit pour exécuter IBM Integration Bus dans un conteneur :

1.  [Mise à disposition d'un conteneur basé sur l'image ibm-integration-bus à partir de l'interface utilisateur {{site.data.keyword.Bluemix_notm}}](#provision)
2.  [Validation du conteneur et de l'installation d'IBM Integration Bus](#validate)
3.  [Création et déploiement d'un flux](#createdeployflow)
4.  [Surveillance de journaux IBM Integration Bus dans le conteneur](#monitor)
5.  [Gestion des ressources IBM Integration Bus qui s'exécutent dans le conteneur](#manage)


## Mise à disposition d'un conteneur basé sur l'image **ibm-integration-bus**
{: #provision}

Mise à disposition d'un conteneur Docker dans {{site.data.keyword.Bluemix_notm}}, basé sur l'image **ibm-integration-bus** fournie par {{site.data.keyword.IBM_notm}}.
{:shortdesc}

  Procédez comme suit :

1.  Connectez-vous à {{site.data.keyword.Bluemix_notm}}.
2.  Dans le catalogue, sélectionnez **Conteneurs** et choisissez l'image **ibm-integration-bus** à partir de laquelle générer votre conteneur.
3.  Sélectionnez **Unique** pour créer un conteneur d'instance unique, lequel peut être utilisé à des fins de développement et de test.
4.  Entrez le nom du conteneur, par exemple, `iib`.
5.  Sélectionnez la taille du conteneur.
6.  Dans la zone **Adresse IP publique**, sélectionnez *Lier une adresse IP publique*.
7.  Dans la zone **Ports publics**, spécifiez `4414, 7800`.
8.  Développez les options **Avancé** et cliquez sur **Ajouter une nouvelle variable d'environnement**.
9.  Ajoutez les variables d'environnement suivantes :

    <ul>
      <li>LICENSE, avec la valeur <i>accept</i>
      <li>NODENAME, avec la valeur &lt;MYNODE&gt; &lpar;où &lt;MYNODE&gt; est le nom de noeud que vous avez choisi&rpar;</li>
      <li>LOG_LOCATIONS, avec la valeur <i>/var/log/syslog</i>
    </ul>

    **Remarque** : Si vous ne spécifiez pas de valeurs pour les propriétés LICENSE et NODENAME, le conteneur est créé, mais ne démarre pas.

    La propriété LOG_LOCATIONS spécifie les journaux au sein du conteneur que vous souhaitez exposer via l'interface utilisateur {{site.data.keyword.Bluemix_notm}}.
10. Cliquez sur **Créer**, puis attendez que le conteneur commence à s'exécuter.


## Validation du conteneur et de l'installation d'IBM Integration Bus
{: #validate}

Une fois que le conteneur exécutant IBM Integration Bus est déployé dans {{site.data.keyword.Bluemix_notm}}, vérifiez l'état du conteneur et validez l'installation d'IBM Integration Bus.
{:shortdesc}

  Procédez comme suit pour vérifier l'installation et la configuration d'IBM Integration Bus dans le conteneur :
  
1.  Configurez l'interface de ligne de commande {{site.data.keyword.containershort_notm}}, comme indiqué dans [Configuration du plug-in {{site.data.keyword.containershort_notm}} (`bx ic`\) pour utiliser l'interface de ligne de commande Docker native](/docs/containers/container_cli_cfic_install.html).

2.  A partir d'un terminal, connectez-vous à {{site.data.keyword.Bluemix_notm}}. Exécutez les commandes suivantes :

    <ol>
       <li>
          <pre class="codeblock">bx login</pre>
       </li>
       <li>
          <pre class="codeblock">bx ic init</pre>
       </li>
    </ol>

3.  Vérifiez le statut du conteneur.\*

    ```
    bx ic ps
    ```
    {: pre}

    **Remarque** : Dans cette commande, vous pouvez remplacer **bx ic** par **docker** lorsque vous êtes [connecté à {{site.data.keyword.containershort_notm}}](/docs/containers/container_cli_cfic_install.html#container_cli_login) et définir vos variables d'environnement afin d'utiliser des commandes Docker natives. Vous pouvez utiliser des commandes Docker natives dans toutes les étapes accompagnées d'un astérisque (\*).

    ```
    $ bx ic ps
    CONTAINER ID        IMAGE                                                       COMMAND             CREATED             STATUS                 PORTS                                                        NAMES
    46d9ec1e-952        registry.ng.bluemix.net/ibm-integration-bus:latest   ""                  2 minutes ago       Running a minute ago   xxx.xxx.xxx.xxx:4414->4414/tcp, xxx.xxx.xxx.xxx:7800->7800/tcp   iib
    ```
    {: screen}

4.  Associez une session Bash à votre conteneur.\*

    ```
    bx ic exec -it <container_name> /bin/bash
    ```
    {: pre}

    où *container_name* est le nom du conteneur.

    Exemple :

    ```
    $ bx ic exec -it iib /bin/bash
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

5.  Initialisez l'environnement en exécutant la commande **mqsiprofile**, comme décrit dans [Command environment: Linux and UNIX™ systems ![External link icon](../../../icons/launch-glyph.svg "External link icon")](http://www.ibm.com/support/knowledgecenter/SSMKHH_9.0.0/com.ibm.etools.mft.doc/an26230_.htm){: new_window}.

    La commande permettant d'initialiser l'environnement est disponible dans le répertoire suivant :
`/opt/ibm/iib-10.0.0.6/server/bin/`

    Exemple :

    ```
    (IIB_10:)iibuser@instance-0098858e:/$ . ./opt/ibm/iib-10.0.0.6/server/bin/mqsiprofile

    MQSI 10.0.0.6
    /opt/ibm/iib-10.0.0.6/server

    # Check the environment variable <MQSI_WORKPATH> is set
    (IIB_10:)iibuser@instance-0098858e:/$ echo $<MQSI_WORKPATH>
    /var/mqsi
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

6.  Exécutez la commande **mqsilist** pour afficher l'état de votre noeud, qui devrait être `running`. Exemple :

    ```
    (IIB_10:)iibuser@instance-0098858e:/$ mqsilist
    BIP1325I: Integration node 'MYNODE' with administration URI 'http://instance-0098858e:4414' is running.
    BIP8071I: Successful command completion.
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

Votre conteneur est maintenant en cours d'exécution et vous pouvez déployer dessus des solutions d'intégration à l'aide de l'une des méthodes prises en charge.


## Surveillance des journaux IBM Integration Bus dans le conteneur
{: #monitor}

Surveillez les journaux via l'interface utilisateur {{site.data.keyword.Bluemix_notm}} ou à partir de la ligne de commande.
{:shortdesc}

Avant de commencer, configurez l'interface de ligne de commande {{site.data.keyword.containershort_notm}} pour surveiller les journaux à partir de la ligne de commande. Pour plus d'informations, voir [Configuration du plug-in {{site.data.keyword.containershort_notm}} (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

L'image **ibm-integration-bus** est configurée pour générer des messages IBM Integration Bus dans `/var/log/syslog` au sein du conteneur.

Choisissez l'une des méthodes suivantes pour surveiller les journaux IBM Integration Bus :

*  Si vous avez configuré la variable d'environnement LOG_LOCATIONS lors de la mise à disposition du conteneur, vous pouvez surveiller les journaux depuis l'interface utilisateur {{site.data.keyword.Bluemix_notm}}.

    Procédez comme suit pour accéder aux journaux :

    <ol>
       <li>Dans le tableau de bord des conteneurs, sélectionnez le conteneur que vous souhaitez surveiller.</li>
       <li>Sélectionnez <b>Surveillance et journalisation</b>.</li>
       <li>Sélectionnez <b>Journalisation</b>. Le tableau de bord  répertorie les entrée de journal.</li>
       <li>Pour personnaliser la vue des journaux, sélectionnez <b>Vue avancée</b> dans la page Journalisation pour ouvrir Kibana. Pour plus d'informations, voir <a href="https://console.bluemix.net/docs/containers/container_planning.html#container_planning_monlog" target="_blank">Surveillance et journalisation</a>.
       </li>
    </ol>

*  A partir d'un terminal, utilisez Docker ou l'interface de ligne de commande {{site.data.keyword.containerlong_notm}} pour surveiller les messages. Procédez comme suit :

    <ol>
       <li>
       Configurez votre terminal.

        <ul>
        <li>Pour utiliser l'interface de ligne de commande {{site.data.keyword.containerlong_notm}} afin de gérer des conteneurs dans {{site.data.keyword.Bluemix_notm}} tout en continuant d'utiliser directement l'interface de ligne de commande Docker pour gérer votre hôte Docker local, procédez comme suit pour configurer l'environnement :
           <ol>
             <li>
               <pre class="codeblock">bx login</pre>
             </li>
             <li>
               <pre class="codeblock">bx ic init</pre>
             </li>
           </ol>
        </li>
        <li>Pour utiliser l'interface de ligne de commande Docker afin de gérer des conteneurs dans {{site.data.keyword.Bluemix_notm}}, procédez comme suit :
          <ol>
            <li>
              <pre class="codeblock">bx login</pre>
            </li>
            <li>
              <pre class="codeblock">bx ic init</pre>
               Copiez les valeurs fournies pour les variables d'environnement suivantes : &lt;DOCKER_HOST&gt;, &lt;DOCKER_CERT_PATH&gt; et &lt;DOCKER_TLS_VERIFY&gt;.

            </li>
            <li>
            Remplacez l'environnement Docker local en définissant les variables suivantes pour vous connecter à {{site.data.keyword.containerlong_notm}} :
               <ul>
                 <li><pre class="codeblock">export DOCKER_HOST=&lt;your_host_value&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;your_cert_path_value&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
               </ul>
                <b>Remarque</b> : Cette option ne prend en charge que certaines commandes Docker.
            </li>
          </ol>
        </li>
      </ul>
      </li>
      <li>
      Accédez au journal. Choisissez l'une des options suivantes :

      <ul>
         <li>Si votre terminal est configuré pour exécuter des commandes de l'interface de ligne de commande {{site.data.keyword.containerlong_notm}}, exécutez la commande suivante :

            <pre class="codeblock">bx ic exec &lt;container_id&gt; tail -f /var/log/syslog</pre>

            où *container_id* est le nom de votre conteneur.

            Exemple :

            <pre class="screen">bx ic exec iib tail -f /var/log/syslog</pre>

          </li>
          <li>
           Si votre terminal est configuré pour exécuter des commandes de l'interface de ligne de commande Docker, exécutez la commande suivante :

            <pre class="codeblock">docker exec &lt;ID_conteneur&gt; tail -f /var/log/syslog</pre>

            où *container_id* est le nom de votre conteneur.

            Exemple :

            <pre class="screen">docker exec iib tail -f /var/log/syslog</pre>

          </li>
        </ul>
       </li>
    </ol>


## Gestion des ressources IBM Integration Bus qui s'exécutent dans le conteneur
{: #manage}

Lancez l'interface utilisateur Web IBM Integration Bus ou exécutez des commandes depuis un terminal pour gérer les ressources IBM Integration Bus qui s'exécutent dans un conteneur.
{:shortdesc}

  Choisissez l'une des options suivantes pour gérer les ressources IBM Integration Bus du conteneur :

*  [Lancez l'interface utilisateur Web IBM Integration Bus](#launchwebUI) et gérez IBM Integration Bus sous forme graphique.
*  A partir d'un terminal, utilisez Docker ou l'interface de ligne de commande {{site.data.keyword.containershort_notm}} pour gérer les ressources IBM Integration Bus :
    *   [Exécution de commandes d'administration dans le conteneur à l'aide de l'interface de ligne de commande {{site.data.keyword.containerlong_notm}}](#admin_commands_containers_cli)
    *   [Exécution de commandes d'administration dans le conteneur à l'aide de l'interface de ligne de commande Docker](#admin_commands_docker_cli)


## Lancez l'interface utilisateur Web IBM Integration Bus
{: #launchwebUI}

Lancez l'interface utilisateur Web pour gérer les ressources IBM Integration Bus qui s'exécutent dans le conteneur à partir de votre navigateur.
{:shortdesc}

Procédez comme indiqué ci-après pour connecter un navigateur à votre hôte, que vous avez exposé dans [Initiation à l'image ibm-integration-bus pour {{site.data.keyword.Bluemix_notm}}](#get_started). L'interface utilisateur Web IBM Integration Bus s'affiche.

1.  Sélectionnez votre conteneur à partir de l'interface graphique {{site.data.keyword.Bluemix_notm}} et vérifiez s'il a pour état `En cours d'exécution`.
2.  Recherchez l'adresse IP publique dans les détails du conteneur.
3.  Ouvrez un navigateur Web à l'aide de l'URL suivante :

    ```
    http://<DockerContainerPublicIP>:4414
    ```
    {: pre}

    où
*DockerContainerPublicIP* est l'adresse IP publique du conteneur que vous avez recherchée à l'étape précédente.

4.  Le navigateur ouvre l'interface utilisateur Web et vous êtes prêt à administrer IBM Integration Bus.


## Exécution de commandes d'administration dans le conteneur à l'aide de l'interface de ligne de commande {{site.data.keyword.containerlong_notm}}
{: #admin_commands_containers_cli}

Utilisez l'interface de ligne de commande {{site.data.keyword.containershort_notm}} pour exécuter les commandes d'administration IBM Integration Bus directement dans un conteneur.
{:shortdesc}

Avant de commencer, configurez l'interface de ligne de commande {{site.data.keyword.containershort_notm}} pour exécuter des commandes d'administration à partir de la ligne de commande. Pour plus d'informations, voir [Configuration du plug-in {{site.data.keyword.containershort_notm}} (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

  Procédez comme indiqué ci-après pour configurer un terminal afin qu'il exécute des commandes **bx ic** pour gérer IBM Integration Bus :

1.  A partir d'un terminal, connectez-vous à {{site.data.keyword.Bluemix_notm}}. Exécutez les commandes suivantes :

      <ol>
        <li>
           <pre class="codeblock">bx login</pre>
        </li>
        <li>
           <pre class="codeblock">bx ic init</pre>
        </li>
       </ol>
           
2.  Associez une session Bash à votre conteneur pour configurer une session interactive.\*

    ```
    bx ic exec -it <container_name> /bin/bash
    ```
    {: pre}

    où *container_name* est le nom du conteneur.

    Exemple :

    ```
    bx ic exec -it iib /bin/bash
    ```
    {: screen}

    Vous pouvez afficher la source du profil mqsiprofile et exécuter vos commandes à partir de ce stade, dans un interpréteur de commandes au sein du conteneur.

3.  Vous pouvez aussi utiliser la commande **bx ic exec** pour exécuter une session Bash non interactive qui peut exécuter n'importe laquelle des commandes IBM Integration Bus.\*

    Exemple :

    ```
    bx ic exec iib <container_name> /bin/bash -c mqsilist
    ```
    {: screen}


## Exécution de commandes d'administration dans le conteneur à l'aide de l'interface de ligne de commande Docker
{: #admin_commands_docker_cli}

Utilisez l'interface de ligne de commande Docker pour exécuter les commandes d'administration IBM Integration Bus directement dans un conteneur.
{:shortdesc}

Avant de commencer, configurez l'interface de ligne de commande {{site.data.keyword.containershort_notm}} pour exécuter des commandes d'administration à partir de la ligne de commande. Pour plus d'informations, voir [Configuration du plug-in {{site.data.keyword.containershort_notm}} (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

  Procédez comme indiqué ci-après pour configurer un terminal afin qu'il exécute des commandes Docker pour gérer IBM Integration Bus :

1.  A partir d'un terminal, connectez-vous à {{site.data.keyword.Bluemix_notm}}. Exécutez les commandes suivantes :

    <ol>
     <li>
       <pre class="codeblock">bx login</pre>
     </li>
     <li>
       <pre class="codeblock">bx ic init</pre>

      <p>Copiez les valeurs fournies pour les variables d'environnement suivantes : &lt;DOCKER_HOST&gt;, &lt;DOCKER_CERT_PATH&gt; et &lt;DOCKER_TLS_VERIFY&gt;.</p>
      </li>
      <li>  Remplacez l'environnement Docker local en définissant les variables suivantes pour vous connecter à {{site.data.keyword.containershort_notm}} :<ul>
           <li><pre class="codeblock">export DOCKER_HOST=&lt;your_host_value&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;your_cert_path_value&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
        </ul>

          <p><b>Remarque</b> : Cette option ne prend en charge que certaines commandes Docker.
            </p>
     </li>
    </ol>

2.  Associez une session Bash à votre conteneur pour configurer une session interactive. Exécutez la commande suivante :

    ```
    docker exec -it <container_name> /bin/bash
    ```
    {: pre}

    où *container_name* est le nom du conteneur.

    Exemple :

    ```
    docker exec -it iib /bin/bash
    ```
    {: screen}

    Vous pouvez afficher la source du profil mqsiprofile et exécutez vos commandes à partir de ce stade, dans un interpréteur de commandes au sein du conteneur.

3.  Vous pouvez afficher la source du profil mqsiprofile et exécutez vos commandes à partir de ce stade, dans un interpréteur de commandes au sein du conteneur.

    Sinon, utilisez docker exec pour exécuter une session Bash non interactive qui peut exécuter n'importe laquelle des commandes IBM Integration Bus. 
    
    Exemple :

    ```
    docker exec iib <container_name> /bin/bash -c mqsilist
    ```
    {: screen}


## Création et déploiement d'un flux
{: #createdeployflow}

Créez et déployez un flux de messages en suivant les instructions décrites dans IBM Integration Bus Knowledge Center.
{:shortdesc}

Pour toute information sur la création d'un flux de messages, voir [How do I create and manage message flows? ![External link icon](../../../icons/launch-glyph.svg "External link icon")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40150_.htm){: new_window}.

Pour toute information sur le déploiement d'un flux de messages, voir [How do I deploy and configure message flows? ![External link icon](../../../icons/launch-glyph.svg "External link icon")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40180_.htm){: new_window}.
