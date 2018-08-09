---

copyright:
  years: 2017, 2018
lastupdated: "2018-07-24"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip} 
{:table: .aria-labeledby="caption"}

# Initiation à l'image ibm-backup-restore
{: #ibmbackup_restore_starter}

L'image **ibm-backup-restore** contient les packages préinstallés nécessaires pour sauvegarder et restaurer le stockage de persistance dans {{site.data.keyword.containerlong}}.
{:shortdesc}

## Fonctionnement 
{: #how_it_works}

L'image **ibm-backup-restore** vous permet de créer une sauvegarde ponctuelle ou planifiée des données d'application qui sont stockées dans un volume persistant de votre cluster ou de restaurer des données d'applications dans un volume persistant. Pour sauvegarder et restaurer les données, vous déployez un pod depuis l'image **ibm-backup-restore**. Vous montez ensuite la réservation de volume persistant qui lie le volume persistant que vous voulez utiliser pour la restauration de vos données dans votre pod. 

**Où vont mes données ? Comment puis-je y accéder? ** </br>
Les données que vous sauvegardez sont stockées dans une instance de service {{site.data.keyword.cos_full_notm}}. Pour accéder au service, utilisez vos données d'identification du service {{site.data.keyword.cos_full_notm}} en tant que variables d'environnements dans le pod **ibm-backup-restore**, ou éditez le fichier `config.conf` dans le pod en cours d'exécution.

**Puis-je restaurer les données sauvegardées dans une application ou un volume persistant différent ?** </br>
Oui, vous pouvez restaurer vos données sauvegardées depuis l'instance de service {{site.data.keyword.cos_full_notm}} dans un volume persistant de votre cluster. Pour restaurer les données, vous devez créer un pod de restauration depuis l'image **ibm-backup-restore**. Vous montez ensuite la réservation de volume persistant qui lie le volume persistant que vous voulez utiliser dans votre pod.  

## Eléments inclus 
{: #whats_included}

Chaque image **ibm-backup-restore** contient les progiciels suivants :

-   Alpine 3.7
-   Duplicity 0.7.10
-   Packages python et gpgme


## Configuration d'une instance de service {{site.data.keyword.cos_full_notm}} 
{: #object_storage}

Créez et configurez une instance de service {{site.data.keyword.cos_full_notm}} qui servira de référentiel pour les données que vous voulez sauvegarder.
{: shortdesc}

1. Déployez une instance de service {{site.data.keyword.cos_full_notm}}.
   1.  Ouvrez le catalogue [{{site.data.keyword.Bluemix_notm}}](https://console.bluemix.net/catalog/services/cloud-object-storage).
   2.  Entrez un nom pour votre instance de service, tel que `cos-backup` et sélectionnez **default** comme groupe de ressources. 
   3.  Consultez les plans de tarification dans [Tarification d'IBM Cloud Object Storage ![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://www.ibm.com/cloud-computing/bluemix/fr/pricing-object-storage#s3api) pour prendre connaissance des informations de tarification. 
   4.  Cliquez sur **Créer**.
2. Extrayez les données d'identification d'instance de service {{site.data.keyword.cos_full_notm}}.
   1.  Dans la navigation de la page des détails du service, cliquez sur **Données d'identification pour le service**.
   2.  Cliquez sur **Nouvelles données d'identification**. Une boîte de dialogue s'affiche. 
   3.  Entrez un nom pour vos données d'identification.
   4.  Dans **Ajouter des paramètres de configuration en ligne (facultatif)**, entrez `{"HMAC":true}` pour créer des données d'identification HMAC supplémentaires que le pod **ibm-backup-restore** utilise pour l'authentification HMAC avec le service {{site.data.keyword.cos_full_notm}}. 
   5.  Cliquez sur **Ajouter**. Vos nouvelles données d'identification sont répertoriées dans le tableau **Données d'identification pour le service**.
   6.  Cliquez sur **Afficher les données d'identification**. 
   7.  Notez les informations relatives aux sections **access_key_id** et **secret_access_key** que vous pouvez trouver dans la section **cos_hmac_keys**. 
3. Créez votre premier compartiment {{site.data.keyword.cos_full_notm}}.
   1. Dans la navigation de la page des détails du service, cliquez sur **Buckets**. 
   2. Cliquez sur **Create bucket**. Une boîte de dialogue s'affiche.
   3. Entrez un nom unique pour votre compartiment. Le nom doit être unique dans {{site.data.keyword.cos_full_notm}} sur toutes les régions et sur tous les comptes {{site.data.keyword.Bluemix_notm}}. 
   4. Dans la liste déroulante **Resiliency**, sélectionnez le niveau de disponibilité que vous voulez pour vos données. Pour plus d'informations, voir la rubrique relative aux [ régions et noeuds finaux {{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage/basics/endpoints.html#select-regions-and-endpoints). 
   5. Changez la valeur de l'option **Emplacement** pour qu'elle corresponde à la région dans laquelle vous voulez stocker vos données. Gardez à l'esprit qu'il est possible que vous ne soyez pas autorisé à stocker vos données dans toutes les régions, pour des raisons juridiques.  
   6. Cliquez sur **Créer**. 
4. Extrayez le nom d'hôte {{site.data.keyword.cos_full_notm}} pour votre compartiment. 
   1. Cliquez sur le nom du compartiment que vous avez créé à l'étape précédente. 
   2. Dans la navigation de la page des détails du service, cliquez sur **Buckets** > **Configuration**. 
   3. Prenez note de l'URL publique que vous pouvez utiliser pour accéder aux données de votre compartiment. 


Consultez la documentation [{{site.data.keyword.cos_full_notm}}](/services/cloud-object-storage/about-cos.html#about-ibm-cloud-object-storage) pour plus d'informations sur la configuration de votre instance de service.

## Sauvegarde des données depuis un volume persistant
{: #scheduled_backup}

Vous pouvez créer une sauvegarde ponctuelle ou planifiée de tout volume persistant qui est monté dans votre pod d'application via une réservation de volume persistant.  
{: shortdesc}

L'exemple suivant vous montre comment déployer un pod de sauvegarde depuis l'image **ibm-backup-restore**, monter un volume persistant dans le pod de sauvegarde en utilisant une réclamation de volume persistant et sauvegarder les données depuis le volume persistant dans votre instance de service {{site.data.keyword.cos_full_notm}}.  

Avant de commencer :

-   [Configurez une instance de service {{site.data.keyword.cos_full_notm}}](#object_storage). 
-   Installez les [interfaces de ligne de commande](/docs/containers/cs_cli_install.html#cs_cli_install) requises pour créer et utiliser votre cluster.
-   [Créez un cluster standard](/docs/containers/cs_clusters.html#clusters_cli) ou utilisez un cluster existant.
-   [Ciblez votre interface de ligne de commande vers votre cluster](/docs/containers/cs_cli_install.html#cs_cli_configure).
-   [Créez une réservation de volume persistant et montez-la sur votre déploiement d'application](/docs/containers/cs_storage.html#create).

Pour sauvegarder un volume persistant :  

1. Obtenez le nom de la réservation de volume persistant qui lie le volume persistant que vous voulez sauvegarder. 
   ```
   kubectl get pvc
   ```
   {: pre}

2. Créez un pod de sauvegarde depuis l'image **ibm-backup-restore**. Pour accéder aux données du volume persistant, vous devez monter la réclamation de volume persistant qui lie le volume persistant à votre pod de sauvegarde. L'exemple ci-après crée une pod de sauvegarde qui exécute une sauvegarde incrémentielle quotidienne. Pour créer une sauvegarde avec des paramètres différents, examinez la liste complète des [options de variable d'environnement](#reference_backup_restore).</br>
   **Important :** L'image **ibm-backup-restore**, qui doit être déployée dans un pod unique, ne peut être utilisée dans le cadre d'un déploiement Kubernetes.
   
   Pour afficher l'image, ciblez le registre global en exécutant la commande `ibmcloud cr region-set global`. Exécutez ensuite `ibmcloud cr images --include-ibm` pour répertorier les images publiques IBM.
   {: tip}
  
   ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: backuppod
   spec:
     containers:
     - image: registry.bluemix.net/ibm-backup-restore
       name: backupcontainer
       env:
       - name: OBJECTSTORAGE
         value: S3
       - name: ACCESS_KEY_ID 
         value: '<access_key_id>'
       - name: SECRET_ACCESS_KEY 
         value: '<secret_access_key>'
       - name: ENDPOINT 
         value: '<regional_endpoint>'
       - name: BUCKET_NAME 
         value: '<bucket_name>'
       - name: BACKUP_DIRECTORY  
         value: /myvol
       - name: BACKUP_NAME
         value: <backup_name> 
       - name: SCHEDULE_TYPE
         value: periodic
       - name: SCHEDULE_INFO
         value: daily
       - name: BACKUP_TYPE
         value: incremental
       command: ["/bin/bash", "./vbackup"]
       volumeMounts:
       - mountPath: /myvol 
         name: backup-volume 
     volumes:
     - name: backup-volume 
       persistentVolumeClaim:
         claimName: <pvc_name>  
   ```
   {: codeblock}
   
   <table>
   <caption>Composants du fichier YAML</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Icône d'idée"/> Présentation des composants du fichier yaml</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>ID de clé d'accès que vous avez extrait en tant qu'élément de vos données d'identification d'instance de service {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</code></td>
     <td>Clé d'accès secrète que vous avez extraite en tant qu'élément de vos données d'identification d'instance de service {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>URL d'accès au noeud final d'API de région que vous utilisez pour accéder à {{site.data.keyword.cos_full_notm}} dans une région spécifique. </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>Nom du compartiment que vous voulez utiliser pour stocker vos sauvegardes dans {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>Nom unique pour l'objet contenant votre sauvegarde dans le compartiment</td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>Nom de la réservation de volume persistant qui lie le volume persistant que vous voulez sauvegarder. </td>
     </tr>
     </tbody>
     </table>
    
3.  Créez le pod de sauvegarde et lancez une sauvegarde de vos données de volume persistant. 

    ```
    kubectl apply -f backuppod.yaml
    ```
    {: pre}

4.  Vérifiez que le pod s'exécute.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}
    
5.  Vérifiez que la sauvegarde s'exécute.
    ```
    kubectl logs backuppod
    ```
    {: pre}

6.  Examinez la sauvegarde dans le service {{site.data.keyword.cos_full_notm}} dans l'interface graphique de {{site.data.keyword.Bluemix_notm}}.
    1.  Depuis le tableau de bord {{site.data.keyword.Bluemix_notm}}, trouvez l'instance de service {{site.data.keyword.cos_full_notm}}. 
    2.  Depuis la navigation, sélectionnez **Buckets** et cliquez sur le compartiment que vous avez utilisé dans votre configuration de sauvegarde. La sauvegarde s'affiche en tant qu'objet dans votre compartiment. 
    3.  Examinez les fichiers compressés. Vous pouvez télécharger le fichier `vol1.difftar.gz`, extraire le fichier et vérifier les données  sauvegardées. </br> **Important** : si vous supprimez ou modifiez des fichiers dans {{site.data.keyword.cos_full_notm}}, ces fichiers ne peuvent pas être récupérés.

Votre sauvegarde est disponible. Si vous avez configuré votre sauvegarde pour créer une sauvegarde intégrale ponctuelle, vous devez exécuter le script de sauvegarde chaque fois que vous souhaitez créer une nouvelle sauvegarde. Si vous avez configuré le conteneur pour qu'il exécute périodiquement une sauvegarde incrémentielle, celle-ci s'exécute de manière planifiée.

## Restauration des données depuis {{site.data.keyword.cos_full_notm}} vers votre cluster
{: #restore_script_cli}

Vous pouvez restaurer vos données depuis votre instance de service {{site.data.keyword.cos_full_notm}} dans un volume persistant de votre cluster. 

Avant de commencer :

-   [Ciblez votre interface de ligne de commande vers votre cluster](/docs/containers/cs_cli_install.html#cs_cli_configure).
-   [Créez une sauvegarde pour un volume persistant dans votre cluster](#scheduled_backup).

Pour restaurer des données depuis {{site.data.keyword.cos_full_notm}} dans un volume persistant : 

1. Obtenez le nom de la réservation de volume persistant qui lie le volume persistant dans lequel vous voulez restaurer vos données. 
   ```
   kubectl get pvc
   ```
   {: pre}

2. Créez un pod de restauration depuis l'image **ibm-backup-restore**. Pour restaurer des données dans un volume persistant, vous devez monter la réclamation de volume persistant qui lie le volume persistant à votre pod de restauration. 
   
   ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: restorepod
   spec:
     containers:
     - image: registry.bluemix.net/ibm-backup-restore 
       name: restorecontainer
       env:
       - name: OBJECTSTORAGE
         value: S3
       - name: ACCESS_KEY_ID
         value: '<access_key_ID>'
       - name: SECRET_ACCESS_KEY
         value: '<secret_access_key>'
       - name: ENDPOINT 
         value: '<regional_endpoint>'
       - name: BUCKET_NAME 
         value: '<bucket_name>'
       - name: RESTORE_DIRECTORY 
         value: /myvol 
       - name: BACKUP_NAME 
         value: <backup_name>
       command: ["/bin/sh", "./vrestore"]
       volumeMounts:
       - mountPath: /myvol  
         name: restore-volume
     volumes:
     - name: restore-volume  
       persistentVolumeClaim:
         claimName: <pvc_name> 
   ```
   {: codeblock}
   
   <table>
   <caption>Composants du fichier YAML</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Icône d'idée"/> Présentation des composants du fichier yaml</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>ID de clé d'accès que vous avez extrait en tant qu'élément de vos données d'identification d'instance de service {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</code></td>
     <td>Clé d'accès secrète que vous avez extraite en tant qu'élément de vos données d'identification d'instance de service {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>URL d'accès au noeud final d'API de région que vous utilisez pour accéder à {{site.data.keyword.cos_full_notm}} dans une région spécifique. </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>Nom du compartiment que vous avez utilisé pour stocker vos sauvegardes dans {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>Nom unique pour l'objet contenant votre sauvegarde dans le compartiment. Vous devez vous servir du nom que vous avez utilisé dans le pod de sauvegarde pour stocker vos données dans {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>Nom de la réservation de volume persistant qui lie le volume persistant dans lequel vous voulez restaurer vos données. </td>
     </tr>
     </tbody>
     </table>

3.  Créez le pod de restauration et commencez à restaurer vos données.
    ```
    kubectl apply -f restorepod.yaml
    ```
    {: pre}
    
4.  Vérifiez que le pod s'exécute.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    restorepod        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    Le pod exécute la commande de restauration et s'arrête. Le message _CrashLoopBackOff_ signifie que Kubernetes est en train d'essayer de redémarrer le pod.

5.  Retirez le pod pour l'empêcher de consommer davantage de ressources.

    ```
    kubectl delete -f restorepod.yaml
    ```
    {: pre}

6.  Vérifiez que vos données ont été correctement restaurées.
    ```
    kubectl logs restorepod
    ```
    {: pre}

Vous avez correctement restauré votre sauvegarde. Vous pouvez maintenant monter la réservation de volume persistant qui lie le volume persistant à un autre pod de votre cluster pour accéder aux fichiers restaurés. Si les données de conteneur qui ont été sauvegardées incluaient un utilisateur non root, vous devez ajouter des droits non root à votre nouveau conteneur. Pour plus d'informations, voir [Ajout d'un accès utilisateur non root aux volumes](../../../containers/container_volumes_ov.html#container_volumes_write).

## Chiffrage de vos sauvegardes 
{: #encrypting_backups}

Chiffrez les données de votre instance {{site.data.keyword.cos_full_notm}}.

1.  Téléchargez <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Icône de lien externe"></a> pour créer une clé de chiffrement.
2.  Créez une clé de chiffrement sur votre unité locale. Vous pouvez accepter les valeurs par défaut en appuyant sur la touche Entrée.

    **Important :** Notez la phrase passe que vous créez. Si vous perdez votre phrase passe, les informations chiffrées avec votre clé ne peuvent pas être déchiffrées.

    ```
    gpg --gen-key
    ```
    {: pre}

    Selon la version de <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Icône de lien externe"></a>, vous devrez éventuellement utiliser `gpg2` au lieu de `gpg` dans vos commandes.

3.  Vérifiez la clé.

    ```
    gpg --list-keys
    ```
    {: pre}

    ```
    $ gpg --list-keys
    /Users/Username/.gnupg/pubring.gpg
    ------------------------------------
    pub   2048R/XXXXXXXX 2016-10-27
    uid       [ultimate] Example Name (This is an example key) <example_email_address>
    sub   2048R/YYYYYYYY 2016-10-27
    ```
    {: screen}

4.  Exportez la clé de chiffrement avec la valeur de la clé `sub`. Nommez le fichier `encryption.asc`.

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    Dans cet exemple, la clé sub a la valeur `YYYYYYYY`.

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  Créez dans un répertoire local un fichier de variables d'environnement pour votre conteneur de sauvegarde chiffré.

    ```
    touch <encryption_env-file_name>
    ```
    {: pre}

6.  Editez le fichier de configuration de pod en lui ajoutant les zones ci-après. Pour les variables d'environnement vides, entrez les valeurs des données d'identification
d'{{site.data.keyword.cos_full_notm}} que vous aviez notées plus tôt. Ajoutez les apostrophes utilisées dans les données d'identification. Pour **ENCRYPTION_PASSPHRASE**, incluez une phrase passe afin de protéger par mot de passe la sauvegarde. Cette phrase passe est différente de la phrase que vous avez créée lors de la création de la clé de chiffrement. Vous devez inclure cette phrase passe lorsque vous sauvegardez et restaurez des données.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: backuppod
    spec:
      containers:
      - image: registry.bluemix.net/ibm-backup-restore
        name: backupcontainer
        env:
        - name: OBJECTSTORAGE
          value: S3
        - name: ACCESS_KEY_ID 
          value: '<access_key_id>'
        - name: SECRET_ACCESS_KEY 
          value: '<secret_access_key>'
        - name: ENDPOINT 
          value: '<regional_endpoint>'
        - name: BUCKET_NAME 
          value: '<bucket_name>'
        - name: BACKUP_DIRECTORY  
          value: /myvol
        - name: BACKUP_NAME
          value: <backup_name> 
        - name: SCHEDULE_TYPE
          value: periodic
        - name: SCHEDULE_INFO
          value: daily
        - name: BACKUP_TYPE
          value: incremental
        - name: ENCRYPTION_REQUIRED
          value: yes
        - name: ENCRYPTION_PASSPHRASE 
          value: <passphrase>
        volumeMounts:
        - mountPath: /myvol 
          name: backup-volume 
      volumes:
      - name: backup-volume 
        persistentVolumeClaim:
          claimName: <pvc_name>  
    ```
    {: codeblock}
   
    <table>
    <caption>Composants du fichier YAML</caption>
    <thead>
    <th colspan=2><img src="../images/idea.png" alt="Icône d'idée"/> Présentation des composants du fichier yaml</th>
    </thead>
     <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>ID de clé d'accès que vous avez extrait en tant qu'élément de vos données d'identification d'instance de service {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</em></code></td>
     <td>Clé d'accès secrète que vous avez extraite en tant qu'élément de vos données d'identification d'instance de service {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>URL d'accès au noeud final d'API de région que vous utilisez pour accéder à {{site.data.keyword.cos_full_notm}} dans une région spécifique. </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>Nom du compartiment que vous voulez utiliser pour stocker vos sauvegardes dans {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>Nom unique pour l'objet contenant votre sauvegarde dans le compartiment.</td>
     </tr>
     <tr>
     <td><code>&lt;passphrase&gt;</code></td>
     <td>Toute chaîne que vous voulez utiliser pour votre sauvegarde. Vous devez inclure cette phrase passe quand vous restaurez vos données. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>Nom de la réservation de volume persistant qui lie le volume persistant que vous voulez sauvegarder. </td>
     </tr>
     </tbody>
     </table>
   
    Ces paramètres créent une sauvegarde incrémentielle quotidienne qui est chiffrée. Pour créer une sauvegarde avec des paramètres différents, examinez la liste complète des [options de variable d'environnement](#reference_backup_restore).
    
7.  Créez le pod de sauvegarde. 

    ```
    kubectl apply -f backuppod.yaml 
    ```
    {: pre}

8.  Vérifiez que le pod s'exécute.

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

9.  Copiez la clé de chiffrement dans le répertoire `/backup_restore` du conteneur qui est généré à partir de l'image **ibm-backup-restore**.

    ```
    kubectl cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    Conservez une copie de la clé de chiffrement en local afin de déchiffrer vos données.

10. Connectez-vous au pod et naviguez jusqu'au dossier `backup_restore`. 

    ```
    kubecl exec -it <pod_name> bash
    ```
    {: pre}

11. Vérifiez que le fichier `encryption.asc` est copié dans le dossier `backup_restore`.

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

12. Exécutez le script de sauvegarde à partir du dossier backup_restore.

    ```
    ./vbackup &
    ```
    {: codeblock}

13. Pour vérifier que votre sauvegarde est chiffrée, examinez les fichiers de votre instance de service {{site.data.keyword.cos_full_notm}}. `.gpg` a été ajouté à la fin du nom de fichier.

Votre sauvegarde est chiffrée. Pour restaurer les fichiers, suivez les étapes décrites dans [Restauration des données depuis {{site.data.keyword.cos_full_notm}} vers votre cluster](#restore_script_cli) et incluez le fichier `encryption.asc` dans le répertoire `backup_restore` du pod  qui exécute le processus de restauration. Si la sauvegarde est chiffrée, vous devez fournir les variables d'environnement **ENCRYPTION_REQUIRED** et **ENCRYPTION_PASSPHRASE** quand vous créez le pod de restauration.

## Référence de variable d'environnement 
{: #reference_backup_restore}

Vérifiez la liste complète des zones qui peuvent être transmises en tant que variables d'environnement ou éditées dans le fichier `config.conf` d'un pod en cours d'exécution. Toute valeur transmise en tant que variable d'environnement remplace celle qui est contenue dans le fichier `config.conf`. Pour passer en revue les variables d'environnement pour un pod, connectez-vous à celui-ci en utilisant la commande `kubectl exec` et exécutez `env`.

|Clé|Options de valeur|
|---|-------------|
|ACCESS_KEY_ID|ID **access_key_id** qui fait partie des données d'identification HMAC dans {{site.data.keyword.cos_full_notm}}.|
|SECRET_ACCESS_KEY|Clé **secret_access_key** qui fait partie des données d'identification HMAC dans {{site.data.keyword.cos_full_notm}}.|
|ENDPOINT|Nom d'hôte pour accéder aux données de compartiment {{site.data.keyword.cos_full_notm}}.|
|BUCKET|Nom du compartiment {{site.data.keyword.cos_full_notm}} où vos données sauvegardées sont stockées.|
{: caption="Tableau 1. Variables {{site.data.keyword.cos_full_notm}}" caption-side="top"}

|Clé|Options de valeur|
|---|-------------|
|BACKUP_DIRECTORY|*/backup* : valeur par défaut. Chemin de fichier absolu du répertoire dans lequel le volume est monté. Les données sont sauvegardées à partir de ce répertoire. Ne sélectionnez pas le répertoire backup_restore, car ce répertoire contient des fichiers pour les processus de sauvegarde et de restauration.|
|BACKUP_NAME|*volume_backup* : valeur par défaut. Choisissez un nom de sauvegarde.|
|BACKUP_TYPE|*full* : valeur par défaut. Tous les fichiers sont systématiquement sauvegardés.<br/> *incremental* : seuls les fichiers nouveaux ou modifiés sont sauvegardés. Si vous choisissez *incremental*, vous devez choisir des valeurs pour **SCHEDULING_INFO** et **SCHEDULING_TYPE**.|
|SCHEDULE_TYPE|*none* : valeur par défaut. Créez une sauvegarde ponctuelle.<br/> **Remarque :** Si vous choisissez de créer une sauvegarde ponctuelle, votre pod est retiré du cluster une fois la sauvegarde terminée. <br/> *periodic* : remplacez la valeur par periodic pour créer des sauvegardes planifiées.|
|SCHEDULE_INFO|*hourly*: créez une sauvegarde sur une base horaire.<br/>*daily* : valeur par défaut. Créez une sauvegarde quotidienne.<br/>*weekly* : créez une sauvegarde hebdomadaire. Vous devez inclure cette variable si vous planifiez une mise à jour de type periodic.|
|EXCLUDE_DIRECTORIES|*none* : valeur par défaut. Incluez le chemin de fichier absolu des répertoires que vous désirez exclure de la sauvegarde. Séparez les répertoires par une virgule.|
{: caption="Tableau 2. Variables de sauvegarde" caption-side="top"}

|Clé|Options de valeur|
|---|-------------|
|BACKUP_NAME|*volume_backup* : valeur par défaut. Incluez le nom de la sauvegarde qui est restaurée depuis {{site.data.keyword.cos_full_notm}}.|
|RESTORE_DIRECTORY|*/backup* : valeur par défaut. Répertoire absolu dans lequel le volume est monté. Les données sont restaurées dans ce répertoire. Ne sélectionnez pas le répertoire `backup_restore`, car ce répertoire contient des fichiers pour les processus de sauvegarde et de restauration.|
{: caption="Tableau 3. Variables de restauration" caption-side="top"}

|Clé|Options de valeur|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc* : valeur par défaut. Ajoutez cette variable d'environnement si vous modifiez le nom de fichier de la clé de chiffrement ou si la clé se trouve dans un autre répertoire que `backup_restore`.|
|ENCRYPTION_REQUIRED|*no* : valeur par défaut. <br/> *yes* : si vous ne chiffrez pas votre sauvegarde, n'incluez pas de variables d'environnement de chiffrement. Si vous chiffrez votre sauvegarde, incluez cette clé avec la valeur `yes`.|
|ENCRYPTION_PASSPHRASE|Incluez une phrase passe pour sécuriser une sauvegarde. Cette phrase passe est différente de la phrase que vous avez créée lors de la création de la clé de chiffrement. Vous devez inclure cette phrase passe quand vous sauvegardez et restaurez des données.|
|IS_KEY_GENERATED_ON_SYSTEM|*no* : valeur par défaut.<br/> *yes* : incluez cette variable d'environnement avec la valeur `yes` si vous avez généré directement la clé de chiffrement sur le
conteneur. La plupart des utilisateurs génèrent la clé sur leur ordinateur local, copient la clé dans le pod et peuvent conserver la valeur par défaut `no`.|
{: caption="Tableau 4. Variables de chiffrement" caption-side="top"}

