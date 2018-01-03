---

copyright:
  years: 2017
lastupdated: "2017-11-09"

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

L'image **ibm-backup-restore** contient les packages préinstallés nécessaires pour sauvegarder et restaurer le stockage permanent dans {{site.data.keyword.containerlong}}.{:shortdesc}

## Fonctionnement 
{: #how_it_works}

L'image **ibm-backup-restore** vous permet de créer une sauvegarde ponctuelle ou planifiée de n'importe quelle réclamation de volume permanent. La sauvegarde est stockée dans une instance {{site.data.keyword.objectstoragefull}}. Vous pouvez transmettre les données d'identification {{site.data.keyword.objectstorageshort}} à votre conteneur **ibm-backup-restore** sous forme de variables d'environnement ou en éditant le fichier `config.conf` dans le conteneur en cours d'exécution. Vous pouvez également restaurer vos données sauvegardées à partir de l'instance {{site.data.keyword.objectstorageshort}} sur un volume. Etant donné que l'image contient des scripts permettant d'exécuter des sauvegardes et des restaurations, l'utilisateur doit exécuter une commande pour démarrer le script approprié. 

## Eléments inclus 
{: #whats_included}

Chaque image **ibm-backup-restore** contient les progiciels suivants :

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   Packages python, gnupg et wget

## Initiation 
{: #how_to_get_started}

Passez en revue les tâches suivantes pour sauvegarder et restaurer des données :
1.  [Création d'une instance de service {{site.data.keyword.objectstorageshort}}](#object_storage)
2.  [Exécution d'une sauvegarde planifiée](#scheduled_backup)
3.  [Exécution du script de restauration](#restore_script_cli)
4.  [Chiffrage de vos sauvegardes](#encrypting_backups)
5.  [Référence de variable d'environnement](#reference_backup_restore)

## Création d'une instance de service {{site.data.keyword.objectstorageshort}} 
{: #object_storage}

Créez une instance {{site.data.keyword.objectstorageshort}} qui servira de référentiel pour votre sauvegarde de volume.

1.  Mettez à disposition votre instance {{site.data.keyword.objectstorageshort}} à partir de la section **Storage** du catalogue {{site.data.keyword.Bluemix_notm}}.
2.  Cliquez sur {{site.data.keyword.objectstorageshort}}.
3.  Sélectionnez {{site.data.keyword.objectstorageshort}} OpenStack Swift pour {{site.data.keyword.Bluemix_notm}}. Ensuite, cliquez sur Créer. 
3.  Cliquez sur l'onglet **Données d'identification pour le service**.
4.  Cliquez sur **Nouvelles données d'identification**.
5.  Renseignez la zone de nom, mais laissez les autres zones non renseignées. Cliquez sur **Ajouter**.
6.  Vos nouvelles données d'identification sont désormais répertoriées dans le tableau **Données d'identification pour le service**. Cliquez sur **Afficher les données d'identification**.
7.  Notez le contenu des zones **projectid**, **region**,
**userId** et **password**. Ces données d'identification permettent à votre conteneur **ibm-backup-restore** d'accéder à cette instance {{site.data.keyword.objectstorageshort}}.

Examinez la documentation [{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) pour plus d'informations sur la configuration de votre instance et passez en revue les [options de planification](../../ObjectStorage/objectstorage_faq.html#account-payment) pour plus d'informations concernant la tarification du service {{site.data.keyword.objectstorageshort}}.

## Exécution d'une sauvegarde planifiée 
{: #scheduled_backup}

Créez un pod de conteneur à partir de l'image **ibm-backup-restore** et lancez une sauvegarde planifiée pour une exécution régulière.

Avant de commencer :

-   Installez les [interfaces de ligne de commande](../../../containers/cs_cli_install.html#cs_cli_install) requises.
-   [Ciblez votre interface de ligne de commande](../../../containers/cs_cli_install.html#cs_cli_configure) vers votre cluster.


1. Créez un fichier de configuration nommé _backup-pvc.yaml_. Ce fichier de configuration crée une réclamation de volume permanent que vous pouvez monter sur votre pod de sauvegarde en tant que volume. 

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: backuppvc
      annotations:
        volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
    ```
    {: codeblock}
    
2. Créez la réclamation de volume permanent. 

    ```
    kubectl apply -f backup-pvc.yaml 
    ```
    {: pre}

3.  Créez un fichier de configuration nommé _backup.yaml_. Pour les variables d'environnement vides, entrez les valeurs des données d'identification
d'{{site.data.keyword.objectstorageshort}} que vous aviez notées plus tôt. Ajoutez les apostrophes utilisées dans les données d'identification. Remplacez <em>&lt;nom_pode&gt;</em>, <em>&lt;nom_conteneur&gt;</em> et la <em>&lt;région&gt;</em> du registre d'images par des valeurs appropriées. 

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: <pod_name>
    spec:
      containers:
        - name: <container_name>
          image: registry.<region>.bluemix.net/ibm-backup-restore
          env:
          - name: USERID
            value: 
          - name: PASSWORD
            value: 
          - name: PROJECTID
            value:
          - name: REGION
            value:
          - name: SCHEDULE_TYPE
            value: periodic
          - name: SCHEDULE_INFO
            value: daily
          - name: BACKUP_TYPE
            value: incremental
          - name: BACKUP_DIRECTORY
            value: /backup
          - name: BACKUP_NAME
            value: mybackup
          command: ["/bin/bash", "./vbackup"]
          volumeMounts:
          - mountPath: /backup
            name: backup-volume
      volumes:
      - name: backup-volume 
        persistentVolumeClaim:
          claimName: backuppvc
    ```
    {: codeblock}
    
    Ces paramètres créent une sauvegarde incrémentielle quotidienne portant le nom par défaut _mybackup_. Pour créer une sauvegarde avec des paramètres différents, examinez la liste complète des [options de variable d'environnement](#reference_backup_restore).

4.  Créez un pod qui exécute le script de sauvegarde. 

    ```
    kubectl apply -f backup.yaml
    ```
    {: pre}

5.  Assurez-vous que le pod est en cours d'exécution. 

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

4.  Examinez la sauvegarde dans le service {{site.data.keyword.objectstorageshort}} dans l'interface graphique de {{site.data.keyword.Bluemix_notm}}.
    1.  Cliquez sur l'instance {{site.data.keyword.objectstorageshort}} que vous avez créée pour la sauvegarde.
    2.  Sur l'onglet **Gérer** de la table **Conteneurs**, cliquez sur le conteneur {{site.data.keyword.objectstorageshort}}. 
    3.  Passez en revue les fichiers compressés.![Le conteneur Object Storage dans l'interface graphique {{site.data.keyword.Bluemix_notm}} présente les fichiers qui ont été sauvegardés.](images/volume_backup_screenshot.png)Vous pouvez télécharger le fichier vol1.difftar.gz, extraire le fichier, puis vérifier les données sauvegardées. **Important** : si vous supprimez ou modifiez des fichiers dans {{site.data.keyword.objectstorageshort}}, ces fichiers ne peuvent pas être récupérés. 

Votre sauvegarde est disponible. Si vous avez configuré votre sauvegarde pour créer une sauvegarde intégrale ponctuelle, vous devez exécuter le script de sauvegarde chaque fois que vous souhaitez créer une nouvelle sauvegarde. Si vous avez configuré le conteneur pour qu'il exécute périodiquement une sauvegarde incrémentielle, celle-ci s'exécute de manière planifiée.

## Exécution du script de restauration 
{: #restore_script_cli}

Restaurez votre sauvegarde depuis {{site.data.keyword.objectstorageshort}} vers un volume existant ou nouveau. 

Avant de commencer :

-   Installez les [interfaces de ligne de commande](../../../containers/cs_cli_install.html#cs_cli_install) requises.
-   [Ciblez votre interface de ligne de commande](../../../containers/cs_cli_install.html#cs_cli_configure) vers votre cluster.


1. Créez un fichier de configuration nommé _restore-pvc.yaml_. Ce fichier de configuration crée une réclamation de volume permanent que vous pouvez monter sur votre pod de restauration en tant que volume. 

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: restorepvc
      annotations:
        volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
    ```
    {: codeblock}
    
2. Créez la réclamation de volume permanent. 

    ```
    kubectl apply -f restore-pvc.yaml 
    ```
    {: pre}

3.  Créez un fichier de configuration nommé _restore.yaml_. Pour les variables d'environnement vides, entrez les valeurs des données d'identification
d'{{site.data.keyword.objectstorageshort}} que vous aviez notées plus tôt. Ajoutez les apostrophes utilisées dans les données d'identification. La valeur de BACKUP_NAME doit correspondre au nom de la sauvegarde dans {{site.data.keyword.objectstorageshort}} que vous
restaurez.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: <pod_name>
    spec:
      containers:
        - name: <container_name>
          image: registry.<region>.bluemix.net/ibm-backup-restore
          env:
          - name: USERID
            value: 
          - name: PASSWORD
            value: 
          - name: PROJECTID
            value:
          - name: REGION
            value:
          - name: RESTORE_DIRECTORY
            value: /restore
          - name: BACKUP_NAME
            value: mybackup
          command: ["/bin/bash", "./vrestore"]
          volumeMounts:
          - mountPath: /restore
            name: restore-volume
      volumes:
      - name: restore-volume 
        persistentVolumeClaim:
          claimName: restorepvc
    ```
    {: codeblock}

4.  Créez un pod qui exécute le script de restauration. 
    
    ```
    kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  Assurez-vous que le pod est en cours d'exécution. 

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    Le pod exécute la commande de restauration et s'arrête. Le message _CrashLoopBackOff_ signifie que Kubernetes est en train d'essayer de redémarrer le pod.

6.  Retirez le pod pour l'empêcher de consommer davantage de ressources. 

    ```
    kubectl delete -f restore.yaml
    ```
    {: pre}

Vous avez correctement restauré votre sauvegarde. Vous pouvez désormais monter n'importe quel nouveau pod sur la réclamation de volume permanent pour permettre à ce conteneur d'accéder aux fichiers restaurés. Si les données de conteneur qui ont été sauvegardées incluaient un utilisateur non root, vous devez ajouter des droits non root à votre nouveau conteneur. Pour plus d'informations, voir [Ajout d'un accès utilisateur non root aux volumes](../../../containers/container_volumes_ov.html#container_volumes_write).

## Chiffrage de vos sauvegardes 
{: #encrypting_backups}

Chiffrez les données de votre instance {{site.data.keyword.objectstorageshort}}.

1.  Téléchargez <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Icône de lien externe"></a> pour créer une clé de chiffrement.
2.  Créez une clé de chiffrement sur votre unité locale. Vous pouvez accepter les valeurs par défaut en appuyant sur la touche Entrée.

    **Attention :** Notez la phrase passe que vous créez. Si vous perdez votre phrase passe, les informations chiffrées avec votre clé ne peuvent pas être déchiffrées.

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
    
6. Créez un fichier de configuration nommé _backup-pvc.yaml_. Ce fichier de configuration crée une réclamation de volume permanent que vous pouvez monter sur votre pod de sauvegarde en tant que volume. 

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: backuppvc
      annotations:
        volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
    ```
    {: codeblock}

7. Créez la réclamation de volume permanent. 

    ```
    kubectl apply -f backup-pvc.yaml
    ```
    {: pre}

8.  Editez le fichier de configuration de déploiement en lui ajoutant les zones ci-après. Pour les variables d'environnement vides, entrez les valeurs des données d'identification
d'{{site.data.keyword.objectstorageshort}} que vous aviez notées plus tôt. Ajoutez les apostrophes utilisées dans les données d'identification. Pour
*ENCRYPTION_PASSPHRASE*, incluez une phrase passe afin de protéger par mot de passe la sauvegarde. Cette phrase passe est différente de la phrase que vous avez créée lors de la création de la clé de chiffrement. Vous devez inclure cette phrase passe lorsque vous sauvegardez et restaurez des données.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: <pod_name>
    spec:
      containers:
        - name: <container_name>
          image: registry.<region>.bluemix.net/ibm-backup-restore
          env:
          - name: USERID
            value: 
          - name: PASSWORD
            value: 
          - name: PROJECTID
            value:
          - name: REGION
            value:
          - name: SCHEDULE_TYPE
            value: periodic
          - name: SCHEDULE_INFO
            value: daily
          - name: BACKUP_TYPE
            value: incremental
          - name: BACKUP_DIRECTORY
            value: /backup
          - name: BACKUP_NAME
            value: mybackup
          - name: ENCRYPTION_REQUIRED
            value: yes
          - name: ENCRYPTION_PASSPHRASE
           # Include this passphrase when your are backing up and restoring encrypted data.
            value: 
          volumeMounts:
          - mountPath: /backup
            name: backup-volume
      volumes:
      - name: backup-volume 
        persistentVolumeClaim:
          claimName: backuppvc
   ```
   
   Ces paramètres créent une sauvegarde incrémentielle quotidienne chiffrée portant le nom <em>&lt;volume_name&gt;</em>. Pour créer une sauvegarde avec des paramètres différents, examinez la liste complète des [options de variable d'environnement](#reference_backup_restore).
9.  Créez un pod qui exécute le script de sauvegarde.

```
    kubectl apply -f backup.yaml
    ```
    {: pre}

9.  Assurez-vous que le pod est en cours d'exécution.

```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

10.  Copiez la clé de chiffrement dans le répertoire /backup_restore du conteneur qui est généré à partir de l'image **ibm-backup-restore**.

    ```
    kubectl cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    Conservez une copie de la clé de chiffrement en local afin de déchiffrer vos données.

11. Connectez-vous au conteneur.

    ```
    kubecl exec -it <container_name> bash
    ```
    {: pre}

12. Vérifiez que *encryption.asc* est copié dans le dossier backup_restore.

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

13. Exécutez le script de sauvegarde à partir du dossier backup_restore.

    ```
    ./vbackup &
    ```
    {: codeblock}

13. Pour vérifier que votre sauvegarde est chiffrée, examinez les fichiers de votre instance {{site.data.keyword.objectstorageshort}}. '.gpg' a été ajouté à la fin du nom de fichier.![L'interface utilisateur d'Object Storage affiche tous les fichiers sauvegardés, accompagnés d'une extension .gpg indiquant qu'ils sont chiffrés.](images/volume_backup_encrypt_screenshot.png)

Votre sauvegarde est chiffrée. Pour restaurer les fichiers, suivez les étapes décrites dans [Exécution du script de restauration](#restore_script_cli) et ajoutez le fichier encryption.asc dans le répertoire backup_restore du conteneur qui exécute le processus de restauration. Si la sauvegarde est chiffrée, vous devez fournir ENCRYPTION_REQUIRED et ENCRYPTION_PASSPHRASE à partir du fichier yaml de sauvegarde sous forme de variables d'environnement lorsque vous créez le conteneur de restauration.

## Environment variable reference
{: #reference_backup_restore}

Vérifiez la liste complète des zones qui peuvent être transmises en tant que variables d'environnement ou éditées dans le fichier 'config.conf' dans un conteneur en cours d'exécution. Toute valeur transmise en tant que variable d'environnement remplace celle qui est contenue dans le fichier 'config.conf'. Pour passer en revue les variables d'environnement pour un conteneur, connectez-vous à celui-ci en utilisant 'exec' et exécutez 'env'. |Clé|Options de valeur|
|---|-------------|
|PROJECTID|ID projet issu d'{{site.data.keyword.objectstorageshort}}|
|REGION|Région issue d'{{site.data.keyword.objectstorageshort}}|
|USERID|ID utilisateur issu d'{{site.data.keyword.objectstorageshort}}|
|PASSWORD|Mot de passe issu d'{{site.data.keyword.objectstorageshort}}|
{: caption="Tableau 1. Variables {{site.data.keyword.objectstorageshort}} " caption-side="top"}

Récupérez ces variables à partir des données d'identification {{site.data.keyword.objectstorageshort}}. Ajoutez ces variables d'environnement à chaque conteneur qui exécute une opération de sauvegarde ou de restauration.

|Clé|Options de valeur|
|---|-------------|
|BACKUP_DIRECTORY|*/backup* : valeur par défaut. Chemin de fichier absolu du répertoire dans lequel le volume est monté. Les données sont sauvegardées à partir de ce répertoire. Ne sélectionnez pas le répertoire backup_restore, car ce répertoire contient des fichiers pour les processus de sauvegarde et de restauration.|
|BACKUP_NAME|*volume_backup* : valeur par défaut. Choisissez un nom de sauvegarde.|
|BACKUP_TYPE|*full* : valeur par défaut. Tous les fichiers sont systématiquement sauvegardés. <br/> *incremental* : seuls les fichiers nouveaux ou modifiés sont sauvegardés. Si vous choisissez *incremental*, vous devez choisir des valeurs pour SCHEDULING_INFO et SCHEDULING_TYPE.|
|SCHEDULE_TYPE|*none* : valeur par défaut. Créez une sauvegarde ponctuelle.<br/> *periodic* : remplacez la valeur par periodic pour créer des sauvegardes planifiées.|
|SCHEDULE_INFO|*hourly* : créez une sauvegarde sur une base horaire.<br/>*daily* : valeur par défaut. Créez une sauvegarde quotidienne.<br/>*weekly* : créez une sauvegarde hebdomadaire. Vous devez inclure cette variable si vous planifiez une mise à jour de type periodic.|
|EXCLUDE_DIRECTORIES|*none* : valeur par défaut. Incluez le chemin de fichier absolu des répertoires que vous désirez exclure de la sauvegarde. Séparez les répertoires par une virgule.|
{: caption="Tableau 2. Variables de sauvegarde" caption-side="top"}

|Clé|Options de valeur|
|---|-------------|
|BACKUP_NAME|*volume_backup* : valeur par défaut. Incluez le nom de la sauvegarde qui est restaurée depuis {{site.data.keyword.objectstorageshort}}.|
|RESTORE_DIRECTORY|*/backup* : valeur par défaut. Répertoire absolu dans lequel le volume est monté. Les données sont restaurées dans ce répertoire. Ne sélectionnez pas le répertoire backup_restore, car ce répertoire contient des fichiers pour les processus de sauvegarde et de restauration.|
{: caption="Tableau 3. Variables de restauration" caption-side="top"}

|Clé|Options de valeur|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc* : valeur par défaut. Ajoutez cette variable d'environnement si vous modifiez le nom de fichier de la clé de chiffrement ou si la clé se trouve dans un autre répertoire que backup_restore.|
|ENCRYPTION_REQUIRED|*no* : valeur par défaut.<br/> *yes* : si vous ne chiffrez pas votre sauvegarde, n'incluez pas de variables d'environnement de chiffrement. Si vous chiffrez votre sauvegarde, incluez cette clé avec la valeur yes.|
|ENCRYPTION_PASSPHRASE|Ajoutez une phrase passe pour sécuriser une sauvegarde. Cette phrase passe est différente de la phrase que vous avez créée lors de la création de la clé de chiffrement. Vous devez inclure cette phrase passe lorsque vous sauvegardez et restaurez des données.|
|IS_KEY_GENERATED_ON_SYSTEM|*no* : valeur par défaut.<br/> *yes* : incluez cette variable d'environnement avec la valeur yes si vous avez généré la clé de chiffrement sur le conteneur. La plupart des utilisateurs génèrent la clé sur leur ordinateur local, copient la clé dans le conteneur et peuvent conserver la valeur par défaut no.|
{: caption="Tableau 4. Variables de chiffrement" caption-side="top"}

## Migration de vos données de volume depuis des conteneurs uniques et évolutifs vers Kubernetes
{: #migrate_data}

Créez une sauvegarde ponctuelle pour n'importe quel volume de conteneur. La sauvegarde est stockée dans une instance {{site.data.keyword.objectstoragefull}}. Vous pouvez ensuite faire migrer les données vers une réclamation de volume permanent dans Kubernetes.
{:shortdesc}

### Initiation
{: #how_to_get_started}

Avant de commencer :

- [Passez en revue le chemin de migration complet pour déplacer des applications vers Kubernetes](../../../containers/cs_classic.html)
- [Installez l'interface de ligne de commande de conteneurs unique et évolutive (bx ic)](../../../containers/container_cli_cfic_install.html)
- [Installez l'interface de ligne de commande {{site.data.keyword.containershort}} (bx cs and kubectl)](../../../containers/cs_cli_install.html#cs_cli_install)
- [Créez un cluster Kubernetes standard pour faire migrer vos données vers](../../../containers/cs_cluster.html#cs_cluster_cli)

Pour effectuer des opérations de sauvegarde et de restauration, exécutez les tâches suivantes :
1.  [Création d'une instance de service {{site.data.keyword.objectstorageshort}}](#object_storage) (comme indiqué précédemment)
2.  [Exécution d'une sauvegarde ponctuelle](#migrate_backup)
3.  [Exécution du script de restauration dans Kubernetes](#migrate_restore)

### Exécution d'une sauvegarde ponctuelle
{: #migrate_backup}

Créez un conteneur unique à partir de l'image **ibm-backup-restore** et démarrez une sauvegarde.

1.  Connectez-vous à l'interface de ligne de commande {{site.data.keyword.containershort}}.

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
d'{{site.data.keyword.objectstorageshort}} que vous aviez notées plus tôt. N'incluez **pas** les apostrophes utilisées dans les données d'identification.

    ```
    BACKUP_NAME=volume_backup
    BACKUP_DIRECTORY=/backup
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    ```
    {: codeblock}

    Ces paramètres créent une sauvegarde ponctuelle portant le nom par défaut _volume_backup_.

4.  Exécutez un conteneur depuis l'image **ibm-backup-restore** avec un volume monté à sauvegarde. Incluez la commande de lancement du script de sauvegarde.

    -   Vérifiez que vous êtes dans le même répertoire local que le fichier <em>&lt;backup_env-file&gt;</em>.
    -   Le répertoire sur lequel est monté le volume doit correspondre à la valeur de BACKUP_DIRECTORY dans le fichier de variables d'environnement.
    -   Remplacez <em>&lt;volume_name&gt;</em> par le nom du volume qui est sauvegardé.

    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<backup_env-file_name> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    Au terme de la procédure de sauvegarde, le conteneur s'arrête. Si l'exécution du conteneur ne
débute pas, exécutez la commande 'bx ic logs <container_name>' pour rechercher des messages d'erreur dans les journaux.

5.  Examinez la sauvegarde dans le service {{site.data.keyword.objectstorageshort}} dans l'interface graphique de {{site.data.keyword.Bluemix_notm}}.
1.  Cliquez sur l'instance {{site.data.keyword.objectstorageshort}} que vous avez créée pour la sauvegarde.
    2.  Cliquez sur le conteneur {{site.data.keyword.objectstorageshort}}. Dans cet exemple, le nom du conteneur est volume_backup.
    3.  Examinez les fichiers compressés.![Le conteneur Object Storage de l'interface graphique {{site.data.keyword.Bluemix_notm}} présente les fichiers qui ont été sauvegardés.](images/volume_backup_screenshot.png)Vous pouvez télécharger le fichier difftar.gz, extraire le fichier et vérifier les données sauvegardées. **Important** : si vous supprimez ou modifiez des fichiers dans {{site.data.keyword.objectstorageshort}}, ces fichiers ne peuvent pas être récupérés.
### Restauration de vos données vers un cluster Kubernetes
{: #migrate_restore}

Restaurez votre sauvegarde depuis {{site.data.keyword.objectstorageshort}} vers une réclamation de volume permanent dans un cluster Kubernetes.

Avant de commencer :

- [Ciblez votre interface de ligne de commande](../../../containers/cs_cli_install.html#cs_cli_configure) vers votre cluster.

1. Créez un fichier de configuration nommé _restore-pvc.yaml_. Ce fichier de configuration crée une réclamation de volume permanent que vous pouvez monter sur votre pod de sauvegarde en tant que volume.

```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: restorepvc
      annotations:
        volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
    ```
    {: codeblock}

2. Créez la réclamation de volume permanent.

    ```
    kubectl apply -f restore-pvc.yaml 
    ```
    {: pre}

3.  Créez un fichier de configuration nommé _restore.yaml_. Pour les variables d'environnement vides, entrez les valeurs des données d'identification
d'{{site.data.keyword.objectstorageshort}} que vous aviez notées plus tôt. Ajoutez les apostrophes utilisées dans les données d'identification. La valeur de BACKUP_NAME doit correspondre au nom de la sauvegarde dans {{site.data.keyword.objectstorageshort}} que vous restaurez.

```
    apiVersion: v1
    kind: Pod
    metadata:
      name: <pod_name>
    spec:
      containers:
        - name: <container_name>
          image: registry.<region>.bluemix.net/ibm-backup-restore
          env:
          - name: USERID
            value: 
          - name: PASSWORD
            value: 
          - name: PROJECTID
            value:
          - name: REGION
            value:
          - name: RESTORE_DIRECTORY
            value: /restore
          - name: BACKUP_NAME
            value: volume_backup
          command: ["/bin/bash", "./vrestore"]
          volumeMounts:
          - mountPath: /restore
            name: restore-volume
      volumes:
      - name: restore-volume 
        persistentVolumeClaim:
          claimName: restorepvc
    ```
    {: codeblock}

4.  Créez un pod qui exécute le script de restauration.

    ```
    kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  Assurez-vous que le pod s'exécute.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}
    
    Le pod exécute la commande de restauration et s'arrête. Le message _CrashLoopBackOff_ signifie que Kubernetes est en train d'essayer de redémarrer le pod.

6.  Retirez le pod pour l'empêcher de consommer davantage de ressources.

```
    kubectl delete -f restore.yaml
    ```
    {: pre}

Vous avez fait migrer vos données vers votre cluster Kubernetes. Vous pouvez désormais monter n'importe quel nouveau pod sur la réclamation de volume permanent pour permettre à ce pod d'accéder aux fichiers restaurés.

**Remarque** : si les données de conteneur qui ont été sauvegardées incluaient un utilisateur non root, vous devez ajouter des droits non root à votre nouveau conteneur. Pour plus d'informations, voir [Ajout d'un accès utilisateur non root aux volumes](../../../containers/container_volumes_ov.html#container_volumes_write).
