---

copyright:
  years: 2017
lastupdated: "2017-10-30"

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

L'image **ibm-backup-restore** contient les packages préinstallés nécessaires pour sauvegarder et restaurer des volumes de conteneur dans {{site.data.keyword.containerlong}}.
{:shortdesc}

## Fonctionnement 
{: #how_it_works}

L'image **ibm-backup-restore** vous permet de créer une sauvegarde ponctuelle ou planifiée de n'importe quel volume de conteneur. La sauvegarde est stockée dans une instance {{site.data.keyword.objectstoragefull}}. Vous pouvez transmettre les données d'identification {{site.data.keyword.objectstorageshort}} à votre conteneur **ibm-backup-restore** sous forme de variables d'environnement ou en éditant le fichier `config.conf` dans le conteneur en cours d'exécution. Vous pouvez également restaurer vos données sauvegardées à partir de l'instance {{site.data.keyword.objectstorageshort}} sur un volume. Etant donné que l'image contient des scripts permettant d'exécuter des sauvegardes et des restaurations, l'utilisateur doit exécuter une commande pour démarrer le script approprié dans un conteneur en cours d'exécution.

## Eléments inclus 
{: #whats_included}

Chaque image **ibm-backup-restore** contient les progiciels suivants :

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   Packages python, gnupg et wget

## Initiation 
{: #how_to_get_started}

Pour exécuter des opérations de sauvegarde et de restauration, procédez comme suit :
1.  [Création d'une instance de service {{site.data.keyword.objectstorageshort}}](#object_storage)
2.  [Exécution d'une sauvegarde planifiée](#scheduled_backup)
3.  [Exécution du script de restauration](#restore_script_cli)
4.  [Chiffrage de vos sauvegardes](#encrypting_backups)
5.  [Référence de variable d'environnement](#reference_backup_restore)

## Création d'une instance de service {{site.data.keyword.objectstorageshort}} 
{: #object_storage}

Créez une instance {{site.data.keyword.objectstorageshort}} qui servira de référentiel pour votre sauvegarde de volume.

1.  Mettez à disposition votre instance {{site.data.keyword.objectstorageshort}} à partir de la section **Services** du catalogue{{site.data.keyword.Bluemix_notm}}.
2.  Sélectionnez l'instance {{site.data.keyword.objectstorageshort}}.
3.  Cliquez sur l'onglet **Données d'identification pour le service**.
4.  Cliquez sur **Nouvelles données d'identification**.
5.  Renseignez la zone de nom, mais laissez les autres zones non renseignées. Cliquez sur **Ajouter**.
6.  Vos nouvelles données d'identification sont désormais répertoriées dans le tableau **Données d'identification pour le service**. Cliquez sur **Afficher les données d'identification**.
7.  Notez le contenu des zones **projectid**, **region**,
**userId** et **password**. Ces données d'identification permettent à votre conteneur **ibm-backup-restore** d'accéder à cette instance {{site.data.keyword.objectstorageshort}}.

Examinez la documentation [{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) pour plus d'informations sur la configuration de votre instance et passez en revue les [options de planification](../../ObjectStorage/objectstorage_faq.html#account-payment) pour plus d'informations concernant la tarification du service {{site.data.keyword.objectstorageshort}}.

## Exécution d'une sauvegarde planifiée 
{: #scheduled_backup}

Créez un conteneur à partir de l'image **ibm-backup-restore** et lancez une sauvegarde planifiée pour une exécution régulière.

1.  Connectez-vous à l'interface de ligne de commande {{site.data.keyword.containerlong_notm}}. 

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

2.  Créez un fichier de variables d'environnement dans un répertoire local.

    ```
    touch <backup_env-file_name>
    ```
    {: pre}

3.  Modifiez le fichier de variables d'environnement en lui ajoutant les zones suivantes. Pour les variables d'environnement vides, entrez les valeurs des données d'identification
d'{{site.data.keyword.objectstorageshort}} que vous aviez notées plus tôt. N'incluez pas les apostrophes utilisées dans les données d'identification.

    ```
    BACKUP_NAME=volume_name
    BACKUP_DIRECTORY=/backup
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    SCHEDULE_TYPE=periodic
    SCHEDULE_INFO=daily
    BACKUP_TYPE=incremental
    ```
    {: codeblock}

    Ces paramètres créent une sauvegarde incrémentielle quotidienne portant le nom par défaut _volume_backup_. Pour créer une sauvegarde avec des paramètres différents, examinez la liste complète des [options de variable d'environnement](#reference_backup_restore).

4.  Exécutez un conteneur depuis l'image **ibm-backup-restore** avec un volume monté à sauvegarder. Incluez la commande de lancement du script de
sauvegarde.

    -   Vérifiez que vous êtes dans le même répertoire local que le fichier <em>&lt;backup_env-file&gt;</em>.
    -   Le répertoire sur lequel est monté le volume doit correspondre à la valeur de BACKUP_DIRECTORY dans le fichier de variables d'environnement.
    -   Remplacez le nom du volume par celui du volume que vous sauvegardez.
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<backup_env-file_name> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    Si l'exécution du conteneur ne débute pas, exécutez la commande logs `bx ic <container_name>` pour examiner les messages d'erreur des journaux.

5.  Examinez la sauvegarde dans le service {{site.data.keyword.objectstorageshort}} dans l'interface graphique de {{site.data.keyword.Bluemix_notm}}.
    1.  Cliquez sur l'instance {{site.data.keyword.objectstorageshort}} que vous avez créée pour la sauvegarde.
    2.  Cliquez sur le conteneur {{site.data.keyword.objectstorageshort}}. Dans cet exemple, le nom du conteneur est volume_backup.
    3.  Passez en revue les fichiers compressés.![Le conteneur {{site.data.keyword.objectstorageshort}} dans l'interface utilisateur {{site.data.keyword.Bluemix_notm}} présente les fichiers qui ont été sauvegardés.](images/volume_backup_screenshot.png) Vous pouvez télécharger le fichier `vol1.difftar.gz`, extraire le fichier et vérifier les données sauvegardées. Notez que si vous supprimez ou modifiez des fichiers
d'{{site.data.keyword.objectstorageshort}}, ces fichiers ne peuvent pas être récupérés.

Votre sauvegarde est disponible. Si vous avez configuré *<container_name>* pour créer une sauvegarde intégrale ponctuelle, vous devez exécuter le script de sauvegarde chaque fois que vous souhaitez créer une nouvelle sauvegarde. Si vous avez configuré le conteneur pour qu'il exécute périodiquement une sauvegarde incrémentielle, celle-ci s'exécute de manière planifiée.

## Exécution du script de restauration 
{: #restore_script_cli}

Restaurez votre sauvegarde depuis {{site.data.keyword.objectstorageshort}} vers un volume de conteneur nouveau
ou existant.

1.  Créez dans un répertoire local un fichier de variables d'environnement pour votre conteneur de restauration.

    ```
    touch <restore_env-file_name>
    ```
    {: pre}

2.  Modifiez le fichier de variables d'environnement en lui ajoutant les zones suivantes. Pour les variables d'environnement vides, entrez les valeurs des données d'identification
d'{{site.data.keyword.objectstorageshort}} que vous aviez notées plus tôt. La valeur de BACKUP_NAME doit correspondre au nom de la sauvegarde dans {{site.data.keyword.objectstorageshort}} que vous
restaurez.

    ```
    BACKUP_NAME=<volume_name>
    RESTORE_DIRECTORY=/restore
    USERID=
    REGION=
    PROJECTID=
    PASSWORD=
    ```
    {: codeblock}

3.  Créez un volume dans lequel restaurer les fichiers sauvegardés.

    ```
    bx ic volume create <volume_name>
    ```
    {: pre}

4.  Générez un conteneur depuis l'image **ibm-backup-restore**. Incluez la commande de lancement du script de restauration.

    -   Vérifiez que vous êtes dans le même répertoire local que le fichier <em>&lt;restore_env-file&gt;</em>.
    -   Le répertoire sur lequel est monté le volume doit correspondre à la valeur de BACKUP_DIRECTORY dans le fichier de variables d'environnement.
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/restore --env-file ./<restore_env-file_name> registry.ng.buemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vrestore"
    ```
    {: pre}

5.  Vérifiez dans les journaux du conteneur que la procédure de restauration a abouti.

    ```
    bx ic logs <container_name>
    ```
    {: pre}

    ```
    [2016-10-26 18:01:51,031] [utilities : 152] [INFO] *****************Start logging to ./Restore.log with rollover at 102400 bytes**************
    [2016-10-26 18:01:51,031] [restore : 28] [INFO] Starting the restore process.
    [2016-10-26 18:01:51,032] [configureOS : 22] [INFO] Configuring duplicity with IBM Bluemix ObjectStorage.
    [2016-10-26 18:01:51,032] [configureOS : 13] [INFO] Configuring swift client.
    [2016-10-26 18:01:51,032] [restore : 40] [INFO] Configuration is completed.
    [2016-10-26 18:01:54,022] [restore : 70] [INFO] Restoring the backup that is named 'volume_backup' is completed. Synchronizing remote metadata to local cache...
    Copying duplicity-full-signatures.20161026T173856Z.sigtar.gz to local cache.
    Copying duplicity-full.20161026T173856Z.manifest to local cache.
    Last full backup date: Wed Oct 26 17:38:56 2016
    ```
    {: screen}

    Au terme de la procédure de restauration, le conteneur s'arrête.

6.  Facultatif : Supprimez le conteneur afin qu'il ne monopolise pas de ressources.

    ```
    bx ic rm -f <container_name>
    ```
    {: pre}

7. Facultatif : Pour vérifier que vos fichiers sauvegardés sont présents dans le volume, connectez-vous au conteneur, accédez au chemin de montage et répertoriez les fichiers.

    ```
    $ bx ic exec -it <container_name> bash
    root@instance:/backup_restore# cd ..
    root@instance/# cd restore
    root@instance:/restore# ls
    duplicity-I3_pJv-tempdir  new_file
    root@instance:/restore# 
    ```
    {: screen}

    tempdir est un produit du processus de sauvegarde et peut être supprimé.


Vous avez correctement restauré votre sauvegarde sur le volume *volume_name*. Vous pouvez désormais monter n'importe quel nouveau conteneur sur le volume pour permettre à ce conteneur d'accéder aux fichiers restaurés. Si les données de conteneur qui ont été sauvegardées incluaient un utilisateur non root, vous devez ajouter des droits non root à votre nouveau conteneur. Pour plus d'informations, voir [Ajout d'un accès utilisateur non root aux volumes](../../../containers/container_volumes_ov.html#container_volumes_write).

## Chiffrage de vos sauvegardes 
{: #encrypting_backups}

Chiffrez les données de votre instance {{site.data.keyword.objectstorageshort}}.

1.  Téléchargez <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="External link icon"></a> pour créer une clé de chiffrement.
2.  Créez une clé de chiffrement sur votre unité locale. Vous pouvez accepter les valeurs par défaut en appuyant sur la touche Entrée.

    **Attention :** Notez la phrase passe que vous créez. Si vous perdez votre phrase passe, les informations chiffrées avec votre clé ne peuvent pas être déchiffrées.

    ```
    gpg --gen-key
    ```
    {: pre}

    Selon la version de <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="External link icon"></a>, vous devrez éventuellement utiliser `gpg2` au lieu de `gpg` dans vos commandes.

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

4.  Exportez la clé de chiffrement avec la valeur de la clé `sub`. Nommez le fichier encryption.asc.

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    Dans cet exemple, la clé
sub a la valeur *YYYYYYYY*

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  Créez dans un répertoire local un fichier de variables d'environnement pour votre conteneur de sauvegarde chiffré.

    ```
    touch <encryption_env-file_name>
    ```
    {: pre}

6.  Modifiez le fichier de variables d'environnement en lui ajoutant les zones suivantes. Pour les variables d'environnement vides, entrez les valeurs des données d'identification
d'{{site.data.keyword.objectstorageshort}} que vous aviez notées plus tôt. N'incluez pas les apostrophes utilisées dans les données d'identification. Pour
*ENCRYPTION_PASSPHRASE*, incluez une phrase passe afin de protéger par mot de passe la sauvegarde. Cette phrase passe est différente de la phrase que vous avez créée lors de la création de la clé de chiffrement. Vous devez inclure cette phrase passe lorsque vous sauvegardez et restaurez des données.

    ```
    BACKUP_NAME=<volume_name>
    BACKUP_DIRECTORY=/backup
    # PROJECTID, REGION, USERID, PASSWORD come from the Object Storage credentials.
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    SCHEDULE_TYPE=periodic
    SCHEDULE_INFO=daily
    BACKUP_TYPE=incremental
    ENCRYPTION_REQUIRED=yes
    # Include this passphrase when backing up and restoring encrypted data.
    ENCRYPTION_PASSPHRASE=
    ```
    {: codeblock}

    Ces paramètres créent une sauvegarde incrémentielle quotidienne chiffrée portant le nom *<volume_name>*. Pour créer une sauvegarde avec des paramètres différents, examinez la liste complète des [options de variable d'environnement](#reference_backup_restore).

7.  Connectez-vous à l'interface de ligne de commande {{site.data.keyword.containerlong_notm}}. 

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

8.  Exécutez un conteneur depuis l'image **ibm-backup-restore** avec un volume monté à sauvegarder. Incluez la commande de lancement du script de sauvegarde.

    -   Vérifiez que vous êtes dans le même répertoire local que le fichier <em>&lt;encryption_env-file_name&gt;</em>.
    -   Le répertoire sur lequel est monté le volume doit correspondre à la valeur de BACKUP_DIRECTORY dans le fichier de variables d'environnement.
    -   Remplacez le nom du volume par celui du volume que vous sauvegardez.
   
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<encrypted_env-file_name> registry.ng.bluemix.net/ibm-backup-restore
    ```
    {: pre}

    Si l'exécution du conteneur ne débute pas, exécutez la commande logs `bx ic <container_name>` pour examiner les messages d'erreur des journaux.

9.  Copiez la clé de chiffrement dans le répertoire /backup_restore du conteneur généré à partir de l'image **ibm-backup-restore**.

    ```
    bx ic cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    Conservez une copie de la clé de chiffrement en local afin de déchiffrer vos données.

10. Connectez-vous au conteneur.

    ```
    bx ic exec -it <container_name> bash
    ```
    {: pre}

11. Vérifiez que le fichier *encryption.asc* a bien été copié dans le dossier backup_restore.

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

13. Pour vérifier que votre sauvegarde est chiffrée, examinez les fichiers de votre instance {{site.data.keyword.objectstorageshort}}. `.gpg` a été ajouté à la fin du nom de fichiers.![L'interface utilisateur {{site.data.keyword.objectstorageshort}} présente tous les fichiers sauvegardés avec le suffixe .gpg, qui indique qu'ils ont été chiffrés.](images/volume_backup_encrypt_screenshot.png)

Votre sauvegarde est chiffrée. Pour restaurer les fichiers, suivez les étapes décrites dans [Exécution du script de restauration](#restore_script_cli) et ajoutez le fichier encryption.asc dans le répertoire backup_restore du conteneur qui exécute le processus de restauration. Si la sauvegarde est chiffrée, vous devez soumettre ENCRYPTION_REQUIRED et ENCRYPTION_PASSPHRASE sous forme de variables d'environnement lorsque vous créez le conteneur de restauration.

## Référence de variable d'environnement 
{: #reference_backup_restore}

Vérifiez la liste complète des zones qui peuvent être transmises en tant que variables d'environnement ou éditées dans le fichier `config.conf` dans un conteneur en cours d'exécution. Toute valeur transmise en tant que variable d'environnement remplace celle qui est contenue dans le fichier `config.conf`. Pour passer en revue les variables d'environnement pour un conteneur, connectez-vous à celui-ci à l'aide d'`exec` et exécutez `env`.

|Clé|Options de valeur|
|---|-------------|
|PROJECTID|ID de projet provenant du service {{site.data.keyword.objectstorageshort}}.|
|REGION|Région provenant du service {{site.data.keyword.objectstorageshort}}.|
|USERID|ID utilisateur provenant du service {{site.data.keyword.objectstorageshort}}.|
|PASSWORD|Mot de passe provenant du service {{site.data.keyword.objectstorageshort}}.|
{: caption="Tableau 1. Variables {{site.data.keyword.objectstorageshort}}" caption-side="top"}

Récupérez ces variables à partir des données d'identification du service {{site.data.keyword.objectstorageshort}}. Ajoutez ces variables d'environnement à chaque conteneur qui exécute une opération de sauvegarde ou de restauration.

|Clé|Options de valeur|
|---|-------------|
|BACKUP_DIRECTORY|*/backup* : valeur par défaut. Chemin de fichier absolu du répertoire dans lequel le volume est monté. Les données sont sauvegardées à partir de ce répertoire. Ne sélectionnez pas le répertoire backup_restore, car ce répertoire contient des fichiers pour les processus de sauvegarde et de restauration.|
|BACKUP_NAME|*volume_backup* : valeur par défaut. Sélectionnez un nom de sauvegarde.|
|BACKUP_TYPE|*full* : valeur par défaut. Tous les fichiers sont sauvegardés chaque fois.<br/> *incremental* : seuls les fichiers nouveaux ou modifiés sont sauvegardés. Si vous sélectionnez *incremental*, vous devez choisir des valeurs pour SCHEDULING_INFO et SCHEDULING_TYPE.|
|SCHEDULE_TYPE|*none* : valeur par défaut. Indique de créer une sauvegarde ponctuelle.<br/> *periodic* : changez la valeur en periodic pour créer des sauvegardes planifiées.|
|SCHEDULE_INFO|*hourly* : indique de créer une sauvegarde sur une base horaire.<br/>*daily* : valeur par défaut. Indique de créer une sauvegarde quotidienne.<br/>*weekly* : indique de créer une sauvegarde hebdomadaire. Vous devez inclure cette variable si vous planifiez une mise à jour de type periodic.|
|EXCLUDE_DIRECTORIES|*none* : valeur par défaut. Incluez le chemin de fichier absolu des répertoires que vous désirez exclure de la sauvegarde. Séparez les répertoires par une virgule.|
{: caption="Tableau 2. Variables de sauvegarde" caption-side="top"}

|Clé|Options de valeur|
|---|-------------|
|BACKUP_NAME|*volume_backup* : valeur par défaut. Incluez le nom de la sauvegarde que vous restaurez depuis {{site.data.keyword.objectstorageshort}}.|
|RESTORE_DIRECTORY|*/backup* : valeur par défaut. Répertoire absolu dans lequel le volume est monté. Les données sont restaurées dans ce répertoire. Ne sélectionnez pas le répertoire backup_restore car ce répertoire contient des fichiers pour les processus de sauvegarde et de restauration.|
{: caption="Tableau 3. Variables de restauration" caption-side="top"}

|Clé|Options de valeur|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc* : valeur par défaut. Ajoutez cette variable d'environnement si vous modifiez le nom de fichier de la clé de chiffrement ou si la clé se trouve dans un autre répertoire que backup_restore.|
|ENCRYPTION_REQUIRED|*no* : valeur par défaut.<br/> *yes* : si vous ne chiffrez pas votre sauvegarde, n'incluez pas de variables d'environnement de chiffrement. Si vous chiffrez votre sauvegarde, incluez cette clé avec la valeur yes.|
|ENCRYPTION_PASSPHRASE|Inclure une phrase passe afin de sécuriser une sauvegarde. Cette phrase passe est différente de la phrase que vous avez créée lors de la création de la clé de chiffrement. Vous devez inclure cette phrase passe lorsque vous sauvegardez et restaurez des données.|
|IS_KEY_GENERATED_ON_SYSTEM|*no* : valeur par défaut.<br/> *yes* : incluez cette variable d'environnement avec la valeur yes si vous avez généré la clé de chiffrement sur le conteneur. La plupart des utilisateurs génèrent la clé sur leur ordinateur local, copient la clé dans le conteneur et peuvent conserver la valeur par défaut no.|
{: caption="Tableau 4. Variables de chiffrement" caption-side="top"}

