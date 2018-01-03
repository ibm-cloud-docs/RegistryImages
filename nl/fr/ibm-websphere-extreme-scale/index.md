---

copyright:
  years: 2017
lastupdated: "2017-10-30"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# Initiation à l'image **ibm-websphere-extreme-scale** 
{: #ibm-websphere-extreme-scale}

Les images **ibm-websphere-extreme-scale** sont fournies pour {{site.data.keyword.containerlong}}.

## Fonctionnement 
{: #how_it_works}

L'image IBM WebSphere&reg; eXtreme Scale est une fonction de mémoire cache interne distribuée qui s'exécute dans l'environnement d'exécution Liberty. Elle fournit des serveurs de mise en cache Liberty, des outils d'exploitation de mise en cache, une console d'administration et les services d'administration REST prêts à l'emploi basés sur les dernières normes et spécifications de l'industrie. Cette image est générée sur la technologie IBM WebSphere eXtreme Scale centrale. La technologie de mise en cache chargée dans cette image prend totalement en charge la spécification et la conformité JCache \(JSR107\).

Les points forts de l'utilisation de l'image `ibm-websphere-extreme-scale` sont les suivants :

-   Solution de mise en cache distribuée facile à déployer et à configurer, basée sur la technologie eXtreme Scale
-   Console d'administration et API REST faciles à utiliser
-   Personnalisation complète permettant la prise en charge de tous les cas d'utilisation de la mise en cache eXtreme Scale
-   Image facilement configurable qui s'intègre à votre environnement de mise en cache en quelques minutes
-   Conformité JCache permettant la prise en charge de la spécification de mise en cache JSR107


## Eléments inclus 
{: #whats_included}

Chaque
image **ibm-websphere-extreme-scale** fournit les progiciels suivants :

-   Image de base Ubuntu 16.04, 64 bits
-   IBM WebSphere eXtreme Scale
-   IBM Java Runtime Environment 8.0

Les fonctions spécifiques qui sont installées dépendent de l'étiquette que vous sélectionnez. Le tableau suivant répertorie le niveau de code inclus dans chacune des images **ibm-websphere-extreme-scale**. Pour plus d'informations sur ces fonctions, voir la [documentation eXtreme Scale](http://www.ibm.com/support/knowledgecenter/SSTVLU).

|Etiquette|Description|
|---|-----------|
|**ibm-websphere-extreme-scale:latest**|Cette étiquette vous permet de spécifier l'image IBM WebSphere eXtreme Scale la plus récente.|
|**ibm-websphere-extreme-scale:8.6.1.x**|Cette étiquette vous permet de spécifier un niveau d'édition spécifique de WebSphere eXtreme Scale.|
{: caption="Tableau 1. Images IBM WebSphere eXtreme Scale" caption-side="top"}

## Restrictions d'utilisation 
{: #usage}

Le tableau suivant présente les restrictions qui s'appliquent à l'utilisation gratuite de l'image
**ibm-websphere-extreme-scale** dans {{site.data.keyword.Bluemix_notm}}.

|Environnement|Restrictions d'utilisation|
|-----------|------------------|
|Développement|L'image `ibm-websphere-extreme-scale` permet une utilisation gratuite et illimitée à des fins de développement.|
|Production|L'utilisation de l'image **ibm-websphere-extreme-scale** à des fins de production est limitée aux utilisateurs disposant d'un produit IBM WebSphere eXtreme Scale sous licence. Pour plus d'informations sur l'utilisation à des fins de production, voir [View Pricing and Buy](https://www-112.ibm.com/software/howtobuy/buyingtools/paexpress/Express?P0=E1&part_number=D56YTLL,D56YVLL,D572PLL,D572MLL,D0594LL,D059GLL&catalogLocale=en_US&Locale=en_US&country=USA&PT=jsp&CC=USA&VP=&TACTICS=&S_TACT=&S_CMP=&brand=SB05).|
{: caption="Tableau 2. Restrictions quant à l'utilisation des images ibm-websphere-extreme-scale dans {{site.data.keyword.Bluemix_notm}}" caption-side="top"}

**Remarque :** La tarification de l'image **ibm-websphere-extreme-scale** est indépendante de celle des conteneurs que vous utilisez dans {{site.data.keyword.Bluemix_notm}}.

Examinez les conditions d'utilisation des images certifiées IBM dans la section Licence des documents [Informations sur la licence](http://www-03.ibm.com/software/sla/sladb.nsf/displaylis/A44EE6829150B2998525801700516718?OpenDocument).


## Initiation 
{: #get_started}

Vous pouvez utiliser la console ou la ligne de commande pour déployer l'image **ibm-websphere-extreme-scale**.

Cette procédure couvre les options suivantes pour déploiement de l'image depuis la console ou depuis la ligne de commande :

-   Sélectionnez l'image en vous servant de la [console](#bmconsole) {{site.data.keyword.Bluemix_notm}} dans le **catalogue**.
-   Utilisez la commande cf ic pour lancer l'image depuis la [ligne de commande](#bmcommand) CF.

**Important :** Avant de commencer, passez en revue les restrictions d'utilisation décrites dans la section précédente pour les images **ibm-websphere-extreme-scale**.


### Effectuez un déploiement depuis la console {{site.data.keyword.Bluemix_notm}}.
{: #bmconsole}

   1.  Dans le catalogue, sélectionnez **Conteneurs** et choisissez l'image **ibm-websphere-extreme-scale** afin de commencer à générer votre conteneur.
   2.  Sélectionnez dans le menu déroulant **Etiquette/Version** la version de l'image **ibm-websphere-extreme-scale** que vous souhaitez utiliser.
   3.  Créez un conteneur unique.

        **Remarque :** Si vous souhaitez créer plus d'un conteneur, n'utilisez pas l'option **Evolutif** pour créer au moins deux groupes de membres de cache eXtreme Scale. Sélectionnez à la place l'option **Unique** pour créer plusieurs conteneurs et utilisez l'interface utilisateur du tableau de bord eXtreme Scale pour joindre les membres de cache.

        Pour plus d'informations, voir [Création d'un conteneur unique à l'aide du tableau de bord {{site.data.keyword.Bluemix_notm}}](/docs/containers/container_single_ui.html#gui).

        1.  Sur le panneau Créer un conteneur, renseignez la zone **Nom de conteneur** ; par exemple, entrez `wxs1`. Aucun espace ou autre caractère non imprimable n'est autorisé dans le nom de conteneur.
        2.  Pour la zone **Taille**, spécifiez la taille des conteneurs utilisés pour lancer l'image ibm-eXtreme-scale.
        3.  Pour la zone **Adresse IP publique**, spécifiez Demander et lier une adresse IP publique.
        4.  Ouvrez le panneau Options avancées. Créez un volume de sorte que les données de configuration eXtreme Scale soient conservées lors de l'installation d'une mise à jour tournante à un moment ultérieur ; par exemple :
            1. Cliquez sur **Créer un volume** dans Options avancées et renseignez la zone **Nom de volume**. Par exemple, tapez vol-wxs1.
            2. Cliquez sur **Ajoutez une nouvelle variable d'environnement**. l'image **ibm-websphere-extreme-scale** requiert des variables d'environnement stockées dans le conteneur et accessibles par les utilisateurs autorisés.
            <table>
            <caption>Tableau 3. Variables d'environnement pour l'image ibm-websphere-extreme-scale</caption>
               <tr>
                 <th>Nom de variable</th>
                 <th>Valeur à spécifier </th>
               </tr>
               <tr>
                 <td>SECRET_KEY</td>
                 <td>Valeur de la clé secrète ; par exemple, <var class="keyword varname">s3cretKey!</var>. Il s'agit de la clé secrète utilisée par les serveurs eXtreme Scale pour envoyer les communications internes entre les membres d'un groupe de membres de cache. Pour plus d'informations, voir la [section sur le groupe de membres de cache](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txscreategridtut1.html).</td>
               </tr>
               <tr>
                 <td>XSADMIN_PWD</td>
                 <td>Mot de passe de l'utilisateur xsadmin qui se connecte à l'interface utilisateur du tableau de bord eXtremeScale ; par exemple, <var class="keyword varname">xsadmin4Me!</var>.</td>
               </tr>
               </table>
  
              **Remarque :** Vous pouvez modifier le mot de passe et la clé secrète après le démarrage du conteneur eXtreme Scale. Les règles d'administration de mot de passe décrites ci-après s'appliquent à la clé secrète et au mot de passe. Prenez soin d'entrer la clé secrète et le mot de passe en respectant la règle suivante :

              Le mot de passe et la clé secrète doivent comporter au moins un chiffre [`0-9`], un caractère en majuscules [`A-Z`], un caractère spécial [`~!@#$%^&*()_-+=:;'[]{}|<>,.?/`] et être composés d'au moins 10 caractères.

              Pour plus d'informations sur la modification du mot de passe, voir la [documentation Administration](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txsadministerxsld.html).

              Le port est ajouté dans la zone **Port public** par défaut.

   4.  Cliquez sur **Créer** pour créer un conteneur à partir du tableau de bord {{site.data.keyword.Bluemix_notm}}.
    
   5.  Après le démarrage complet du conteneur et des serveurs eXtreme Scale, connectez-vous à l'interface graphique d'eXtreme Scale et vérifiez le déploiement. A partir de la console {{site.data.keyword.Bluemix_notm}}, cliquez sur un conteneur pour afficher l'adresse IP publique. Le conteneur WebSphere eXtreme Scale est accessible à partir de l'URL suivante :

    ```
    https://<Public_IP>:9443
    ```
    {: pre}

   **Remarque :** Il peut s'écouler 5 à 10 minutes avant que le serveur soit complètement démarré.


### Déployez l'image à partir de la ligne de commande.
{: #bmcommand}
 1.  Créez un volume Docker dans lequel stocker des données persistantes IBM WebSphere eXtreme Scale. Par exemple, entrez la commande suivante :

  ```
  cf ic volume create <vol-wxs1>
  ```
  {: pre}

 2.  Entrez la commande suivante pour lancer votre conteneur IBM WebSphere eXtreme Scale dans {{site.data.keyword.Bluemix_notm}} :

    **Remarque :** Remplacez yourSecretKey et yourXsadminPassword par votre propre mot de passe basé sur la règle d'administration de mot de passe précédemment mentionnée.

   ```
   cf ic run -itdP --volume <vol-wxs1:/vol> --name <wxs1> -m 1024 -e SECRET_KEY=<yourSecretKey> -e XSADMIN_PWD=<yourXsadminPassword> registry.ng.bluemix.net/ibm-websphere-extreme-scale:latest
   ```
   {: pre}
        
     **Remarque :** Le nom de conteneur doit inclure uniquement des caractères alphanumériques et commencer par une lettre. Vous pouvez aussi définir <code>-e XSLD_CONTAINER_ALIAS=<wxs1></code> si votre nom de conteneur doit comporter d'autres caractères non alphanumériques.

 3.  Réclamez une adresse IP publique pour son utilisation avec votre premier conteneur IBM WebSphere eXtreme Scale. Entrez la commande suivante pour écrire l'adresse IP :

   ```
   cf ic ip request
   ```
   {: pre}

 4.  Entrez la commande suivante pour lier votre conteneur WebSphere eXtreme Scale à l'adresse IP publique que vous avez demandée :

   ```
   cf ic ip bind <REQUESTED_IP> wxs1
   ```
   {: pre}

 5.  Entrez la commande suivante pour vérifier que le statut de conteneur `wxs1` est En cours d'exécution :

   ```
   cf ic ps -a
   ```
   {: pre}

 6.  Entrez la commande suivante pour vous connecter au conteneur `wxs1` et surveiller le journal de contrôle d'accès :

   ```
   cf ic exec -it <wxs1> tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
        ```
   {: pre}

    **Remarque :** Il peut s'écouler 5 à 10 minutes avant que le serveur soit complètement démarré.

 7.  Après le démarrage complet du conteneur et des serveurs eXtreme Scale, connectez-vous à l'interface graphique d'eXtreme Scale et vérifiez son déploiement:

   ```
   https://<REQUESTED_IP>:9443
   ```
   {: pre}


## Ajout de capacité du cache 
{: #caching}

1.  Déployez une autre image à partir de la console ou de la ligne de commande en exécutant les mêmes étapes que celles décrites précédemment.
2.  Une fois le conteneur et les serveurs eXtreme Scale complètement démarrés, connectez-vous à l'interface utilisateur du tableau de bord eXtreme Scale de ce membre :

    ```
    https://<REQUESTED_IP>:9443
    ```
    {: pre}

3.  Ouvrez la page Data Cache Members et sous la colonne **Action** de ce membre, sélectionnez **Join**.
4.  Entrez l'adresse IP publique demandée pour le premier membre, la clé secrète du premier membre et un nom unique pour ce membre dans le groupe de caches.
5.  Cliquez sur **Join** et attendez que la tâche soit exécutée. Lorsque la tâche est terminée, tous les membres s'affichent sur la page Data Cache Members.

## Exécution d'une mise à jour en continu 
{: #rolling_upgrade}

Les étapes ci-après sont requises pour effectuer une mise à jour en continu des conteneurs qui exécutent `ibm-websphere-extreme-scale` dans {{site.data.keyword.containershort_notm}}.

**Remarque :** Si votre groupe de membres de cache eXtreme Scale ne comprend qu'un seul membre, retirez ce conteneur {{site.data.keyword.IBM_notm}} et recréez-le entièrement. Toutefois, si votre groupe de membres de cache eXtreme Scale comprend plus d'un membre et plusieurs serveurs de catalogue déployés et que vous souhaitez conserver les données mises en cache dans {{site.data.keyword.containershort_notm}}, exécutez les étapes de mise à jour en continu décrites ci-après pour éviter toute perte de données mises en cache. Mettez à niveau en premier les membres de cache qui ne contiennent que des serveurs de conteneur, puis mettez à niveau les membres de cache qui comportent également des serveurs de catalogue.

1.  Démarrez un nouveau conteneur appelé wxsmigrate, et utilisez-le pour mettre à niveau tous les conteneurs eXtreme Scale existants. Pour plus d'informations, reportez-vous à la section [Initiation](#get_started) sur cette page. Les fichiers .zip eXtreme Scale les plus récents seront déployés dans le dossier /tmp de votre nouveau conteneur wxsmigrate.
2.  Copiez les fichiers .zip eXtreme Scale mis à jour depuis le conteneur wxsmigrate sur le système local où vous exécutez le plug-in d'interface de ligne de commande {{site.data.keyword.containershort_notm}} :

    ```
    cf ic cp wxsmigrate:/tmp/xsld-bin-update.zip
    ```
    {: pre}
    
    ```
    cf ic cp wxsmigrate:/tmp/xsld-derby-update.zip
    ```
    {: pre}

3.  A présent, pour chaque membre de cache d'un groupe de membres de cache, suivez la procédure ci-après pour un conteneur à la fois :

    1. Copiez les fichiers .zip eXtreme Scale mis à jour depuis le système local sur le conteneur eXtreme Scale que vous souhaitez mettre à niveau :
    
      ```
      cf ic cp  xsld-bin-update.zip  <container_name>:/tmp/xsld-bin-update.zip
      ```
      {: pre}
  
      ```
      cf ic cp  xsld-derby-update.zip  <container_name>:/tmp/xsld-derby-update.zip
      ```
      {: pre}
     
    2. Connectez-vous au conteneur que vous souhaitez mettre à niveau : 
    
      ```
      cf ic exec -it <container_name> bash
      ```
      {: pre}

    3. Arrêtez les serveurs XSLD sur le conteneur prêt à être mis à niveau.
      
      ```
      /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
      ```
      {: pre}
      
      Attendez que les serveurs soient arrêtés.
      
    4. Décompressez le code de mise à jour eXtreme Scale le plus récent dans le conteneur :
      
      ```
      unzip  -o  /tmp/xsld-bin-update.zip   -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
      
      ```
      unzip  -o  /tmp/xsld-derby-update.zip -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
    
    5. Redémarrez le serveur XSLD :
    
      ```
      /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
      ```
      {: pre}
      
    6. Surveillez le journal de contrôle d'accès pour vous assurer que les serveurs sont démarrés :
    
      ```
      tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
      ```
      {: pre}
      
4. Une fois la mise à niveau terminée sur tous les conteneurs IBM, retirez le conteneur wxsmigrate :

 ```
 cf ic rm -f <wxsmigrate>
 ```
 {: pre}

## Identification et résolution des problèmes 
{: #troubleshoot}

###Traitement des incidents liés à la connectivité de membre de cache lorsque le mappage d'hôte est perdu 
{: #troubleshoot_cache}

Procédez comme indiqué ci-après en cas de perte de connectivité pour les membres d'un groupe de cache lorsque le mappage d'hôte entre les conteneurs est perdu.

1.  Connectez-vous à chaque conteneur :

    ```
    cf ic exec -it <container_name> bash
    ```
    {: pre}

2.  Rassemblez l'adresse IP privée, le nom d'hôte et l'alias d'hôte pour chaque membre.

    L'alias d'hôte est la valeur de la variable d'environnement XSLD_CONTAINER_ALIAS :

    ```
    echo ${<XSLD_CONTAINER_ALIAS>}
    ```
    {: pre}

    L'adresse IP privée est celle renvoyée par l'exécution de la commande hostname -I dans le conteneur :

    ```
    hostname -I
    ```
    {: pre}

    Le nom d'hôte est celui renvoyé par l'exécution de la commande hostname dans le conteneur.


3.  Exécutez le script recoverXSLD.sh sur chaque membre pour transmettre tous les autres alias, adresses IP et noms d'hôte de membre :

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/recoverXSLD.sh <member2_alias> <member2_private_ip> <member2_hostname> ... <memberN_alias> <memberN_private_ip> <memberN_hostname>
    ```
    {: pre}

4.  Arrêtez les serveurs en cours d'exécution en exécutant stopXSLD.sh sur chaque conteneur du groupe de caches. Exécutez stopXSLD.sh :

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
    ```
    {: pre}

5.  Si stopXSLD.sh n'est pas en cours de progression lorsque vous tentez d'arrêter le serveur de catalogue, procédez comme suit :
    1.  Répertoriez le processus de serveur de catalogue :

        ```
        ps -ef | grep cs-default
        ```
        {: pre}

    2.  Arrêtez le processus de serveur de catalogue à l'aide de l'ID de processus :

        ```
        kill -9 <catalog_server_process_id>
        ```
        {: pre}

    3.  Exécutez de nouveau stopXSLD.sh :

        ```
        /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
        ```
        {: pre}

6.  Lancement de XSLD sur tous les conteneurs approximativement au même moment. Exécutez startXSLD.sh :

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
    ```
    {: pre}


### Traitement des incidents liés à une tâche de réplication Derby arrêtée 
{: #troubleshoot_derby}

Si la progression d'une tâche de réplication Derby n'a pas atteint plus 45 % au bout de plus de dix minutes, il s'agit probablement d'un problème réseau. Procédez comme suit pour résoudre ce problème :

1.  Utilisez un utilitaire de publication REST, tel que cURL, pour annuler la tâche de réplication Derby.

    ```
    curl -X PUT --header "Content-Type: application/json" --header "Accept: application/json" "https://<IP_publique>:9445/wxsadmin/v1/task/cancel/<tâche_réplication_Derby>/force"
    ```
    {: pre}

    **Remarque :** La variable <PUBLIC_IP> est la même que celle qui a été utilisée dans la commande pour démarrer la tâche de réplication Derby, et la variable <DERBY_REPLICATION_TASK_ID> correspond au résultat d'ID tâche généré par cette commande.

2.  Relancez la tâche de réplication Derby.

## Accès à l'API REST 
{: #api}

Lorsque eXtreme Scale est déployé dans {{site.data.keyword.containershort_notm}}, procédez comme suit pour accéder à l'API REST :

1.  Extrayez le nom d'hôte du conteneur que vous comptez utiliser pour vous connecter à l'API REST.
    1.  Connectez-vous au conteneur :

        ```
        cf ic exec -it <container_name> bash
        ```
        {: pre}

    2.  Listez son nom d'hôte :

        ```
        hostname
        ```
        {: pre}

2.  Extrayez l'IP publique de ce même conteneur.

    Si le conteneur ne dispose pas d'une adresse IP publique, suivez la procédure de la section Initiation pour lui en attribuer une depuis la [console](#bmconsole) ou la [ligne de commande](#bmcommand) {{site.data.keyword.Bluemix_notm}}.

3.  Créez dans le fichier hôte de votre ordinateur client local une entrée mappant l'adresse IP publique au nom d'hôte du conteneur. L'entrée doit être similaire à ceci :

    ```
    169.44.8.87 instance-05106102
    ```
    {: pre}

    Noms de chemin usuels complets pour les fichiers hôtes :

    -   UNIX : /etc/hosts
    -   Windows : C:\\Windows\\System32\\drivers\\etc\\hosts
4.  Accédez aux API REST Swagger dans un navigateur.
    -   Pour opérations CRUD :

        ```
        https://<nom_d'hote>:9444/ibm/api/explorer
        ```
        {: pre}

    -   Pour opérations d'administration :

        ```
        https://<nom_d'hôte>:9445/ibm/api/explorer
        ```
        {: pre}


Pour consulter des exemples et pour obtenir les mises à jour récentes, visitez le site [WebSphere eXtreme
Scale sur GitHub](https://github.com/ibmWebsphereExtremeScale). Pour obtenir plus d'aide sur le déploiement d'eXtreme Scale, voir la documentation et les vidéos mentionnés ci-dessous :

-   [Documentation IBM WebSphere eXtreme Scale](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/cxsgetstartxsld.html)
-   [Forum XS developerWorks](https://www.ibm.com/developerworks/community/forums/html/forum?id=11111111-0000-0000-0000-000000000778&ps=25)
-   [Getting started with WebSphere eXtreme Scale V8.6.x](https://www.youtube.com/watch?v=xw7Ck3c40rA)
-   [Adding cache with WebSphere eXtreme Scale - Liberty Deployment](https://www.youtube.com/watch?v=I1g88bnstn8)
-   [Setting up a WAS client to work with an WebSphere eXtreme Scale Liberty Deployment DynaCache grid](https://www.youtube.com/watch?v=RvhcknNX-4Q)
-   [Setting up a WAS client to work with an WebSphere eXtreme Scale Liberty Deployment Session Cache grid](https://www.youtube.com/watch?v=GKTy9I66Klc)

