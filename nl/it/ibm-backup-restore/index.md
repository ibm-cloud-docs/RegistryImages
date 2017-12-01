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

# Introduzione all'immagine ibm-backup-restore 
{: #ibmbackup_restore_starter}

L'immagine **ibm-backup-restore** contiene i pacchetti pre-installati necessari per il backup e il ripristino dei volumi del contenitore in {{site.data.keyword.containerlong}}.
{:shortdesc}

## Come funziona 
{: #how_it_works}

Con l'immagine **ibm-backup-restore**, puoi creare un backup monouso o pianificato per ogni volume del contenitore. Il backup viene archiviato nell'istanza {{site.data.keyword.objectstoragefull}}. Puoi trasmettere le credenziali {{site.data.keyword.objectstorageshort}} al tuo contenitore **ibm-backup-restore** come variabili di ambiente o modificando il file `config.conf` nel contenitore in esecuzione. Puoi anche ripristinare i tuoi dati salvati dall'istanza {{site.data.keyword.objectstorageshort}} in un volume. Poiché l'immagine contiene gli script per l'esecuzione sia dei backup che dei ripristini, l'utente deve immettere un comando per avviare lo script appropriato in un contenitore in esecuzione. 

## Elementi inclusi 
{: #whats_included}

Ogni immagine **ibm-backup-restore** contiene i seguenti pacchetti software:

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   Pacchetti python, gnupg e wget

## Introduzione 
{: #how_to_get_started}

Completa la seguente procedura per eseguire le operazioni di backup e ripristino:
1.  [Creazione di un'istanza del servizio {{site.data.keyword.objectstorageshort}}](#object_storage)
2.  [Esecuzione di un backup pianificato](#scheduled_backup)
3.  [Esecuzione dello script di ripristino](#restore_script_cli)
4.  [Crittografia dei tuoi backup](#encrypting_backups)
5.  [Riferimenti alla variabile di ambiente](#reference_backup_restore)

## Creazione di un'istanza del servizio {{site.data.keyword.objectstorageshort}} 
{: #object_storage}

Crea un'istanza {{site.data.keyword.objectstorageshort}} da utilizzare come repository per il tuo backup del volume.

1.  Esegui il provisioning della tua istanza {{site.data.keyword.objectstorageshort}} dalla sezione **Servizi** del catalogo {{site.data.keyword.Bluemix_notm}}.
2.  Seleziona l'istanza {{site.data.keyword.objectstorageshort}}.
3.  Fai clic sulla scheda **Credenziali del servizio**.
4.  Fai clic su **Nuova credenziale**.
5.  Completa il campo nome ma lascia vuoti gli altri campi. Fai clic su **Aggiungi**.
6.  Le tue nuove credenziali sono ora elencate nella tabella **Credenziali del servizio**. Fai clic su **Visualizza credenziali**.
7.  Prendi nota di **projectid**, **region**, **userId** e **password**. Queste credenziali consentono al tuo contenitore **ibm-backup-restore** di accedere a questa istanza {{site.data.keyword.objectstorageshort}}.

Rivedi la documentazione [{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) per ulteriori informazioni sulla configurazione della tua istanza e rivedi le [opzioni del piano](../../ObjectStorage/objectstorage_faq.html#account-payment) per informazioni sui prezzi {{site.data.keyword.objectstorageshort}}.

## Esecuzione di un backup pianificato 
{: #scheduled_backup}

Crea un contenitore dall'immagine **ibm-backup-restore** e avvia un backup pianificato regolarmente.

1.  Accedi alla CLI {{site.data.keyword.containerlong_notm}}.

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

2.  Crea un file di variabili di ambiente in una directory locale.

    ```
    touch <backup_env-file_name>
    ```
    {: pre}

3.  Modifica il file di variabili di ambiente e aggiungi i seguenti campi. Per le variabili di ambiente vuote, immetti i valori dalle credenziali {{site.data.keyword.objectstorageshort}} di cui hai preso nota precedentemente. Non includere le virgolette utilizzate nelle credenziali.

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

    Queste impostazioni creano un backup incrementale giornaliero con il nome predefinito _volume_backup_. Per creare un backup con impostazioni differenti, rivedi l'elenco completo di [opzioni di variabili di ambiente](#reference_backup_restore).

4.  Esegui un contenitore dall'immagine **ibm-backup-restore** con un volume montato di cui eseguire il backup. Includi il comando per avviare lo script di backup.

    -   Assicurati di essere nella stessa directory locale di <em>&lt;backup_env-file&gt;</em>.
    -   La directory su cui è montato il volume deve corrispondere alla BACKUP_DIRECTORY nel file di variabili di ambiente.
    -   Sostituisci il nome del volume con il nome del volume di cui si eseguirà il backup.
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<backup_env-file_name> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    Se il contenitore non avvia l'esecuzione, esegui `bx ic logs <container_name>` per controllare i messaggi di errore nei log.

5.  Controlla il backup in {{site.data.keyword.objectstorageshort}} nella GUI {{site.data.keyword.Bluemix_notm}}.
    1.  Fai clic sull'istanza {{site.data.keyword.objectstorageshort}} creata per il backup.
    2.  Fai clic sul contenitore {{site.data.keyword.objectstorageshort}}. In questo esempio, il nome del contenitore è volume_backup.
    3.  Rivedi i file compressi. ![Il contenitore {{site.data.keyword.objectstorageshort}} nella GUI {{site.data.keyword.Bluemix_notm}} mostra i file di cui è stato eseguito il backup.](images/volume_backup_screenshot.png) Puoi scaricare il file `vol1.difftar.gz`, estrarlo e verificare i dati del backup. Tieni presente che se elimini o modifichi i file da {{site.data.keyword.objectstorageshort}}, questi file non potranno essere ripristinati. 

Il tuo backup è disponibile. Se hai configurato *<container_name>* per creare un backup completo monouso, devi eseguire lo script di backup ogni volta che vuoi creare un nuovo backup. Se hai configurato il contenitore per eseguire un backup incrementale periodico, i tuoi backup vengono eseguiti come pianificato.

## Esecuzione dello script di ripristino 
{: #restore_script_cli}

Ripristina il tuo backup da {{site.data.keyword.objectstorageshort}} in un volume del contenitore nuovo o esistente.

1.  Crea un file di variabili di ambiente per il tuo contenitore di ripristino in una directory locale.

    ```
    touch <restore_env-file_name>
    ```
    {: pre}

2.  Modifica il file di variabili di ambiente e aggiungi i seguenti campi. Per le variabili di ambiente vuote, immetti i valori dalle credenziali {{site.data.keyword.objectstorageshort}} di cui hai preso nota precedentemente. Il BACKUP_NAME deve corrispondere al nome del backup in {{site.data.keyword.objectstorageshort}} che verrà ripristinato.

    ```
    BACKUP_NAME=<volume_name>
    RESTORE_DIRECTORY=/restore
    USERID=
    REGION=
    PROJECTID=
    PASSWORD=
    ```
    {: codeblock}

3.  Crea un volume in cui ripristinare i file di backup.

    ```
    bx ic volume create <volume_name>
    ```
    {: pre}

4.  Esegui un contenitore dall'immagine **ibm-backup-restore**. Includi il comando per avviare lo script di ripristino.

    -   Assicurati di essere nella stessa directory locale di <em>&lt;restore_env-file&gt;</em>.
    -   La directory su cui è montato il volume deve corrispondere alla BACKUP_DIRECTORY nel file di variabili di ambiente.
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/restore --env-file ./<restore_env-file_name> registry.ng.buemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vrestore"
    ```
    {: pre}

5.  Controlla i log del contenitore per confermare che il processo di ripristino sia stato completato.

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

    Al termine del processo di ripristino, il contenitore si arresta.

6.  Facoltativo: rimuovi il contenitore in modo che non consumi risorse.

    ```
    bx ic rm -f <container_name>
    ```
    {: pre}

7. Facoltativo: per confermare che i file di cui è stato eseguito il backup si trovano nel volume, accedi al contenitore, passa al percorso di montaggio ed elenca i file.

    ```
    $ bx ic exec -it <container_name> bash
    root@instance:/backup_restore# cd ..
    root@instance/# cd restore
    root@instance:/restore# ls
    duplicity-I3_pJv-tempdir  new_file
    root@instance:/restore# 
    ```
    {: screen}

    La tempdir è un prodotto del processo e può essere eliminata manualmente. 


Il backup è stato ripristinato correttamente nel volume *volume_name*. Puoi ora montare ogni nuovo contenitore nel volume per fornire a tale contenitore l'accesso ai file ripristinati. Se i dati del contenitore di cui è stato eseguito il backup includono un utente non root, devi aggiungere le autorizzazioni non root al tuo nuovo contenitore. Per ulteriori informazioni, vedi [Aggiunta di accesso utente non root ai volumi](../../../containers/container_volumes_ov.html#container_volumes_write).

## Crittografia dei tuoi backup 
{: #encrypting_backups}

Codifica i dati nella tua istanza {{site.data.keyword.objectstorageshort}}.

1.  Scarica <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Icona link esterno"></a> per creare una chiave di crittografia.
2.  Crea una chiave di crittografia nella tua unità locale. Puoi accettare i valori predefiniti premendo INVIO.

    **Attenzione:** prendi nota della passphrase che crei. Se la perdi, tutte le informazioni crittografate con la tua chiave non possono essere decrittografate.

    ```
    gpg --gen-key
    ```
    {: pre}

    A seconda della versione di <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Icona link esterno"></a>, potresti dover utilizzare `gpg2` invece di `gpg` nei tuoi comandi.

3.  Verifica la chiave.

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

4.  Esporta la chiave di crittografia con il valore dalla chiave `sub`. Denomina il file encryption.asc.

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    In questo esempio, la chiave secondaria ha il valore *YYYYYYYY*

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  Crea un file di variabili di ambiente per il tuo contenitore di backup crittografato in una directory locale. 

    ```
    touch <encryption_env-file_name>
    ```
    {: pre}

6.  Modifica il file di variabili di ambiente e aggiungi i seguenti campi. Per le variabili di ambiente vuote, immetti i valori dalle credenziali {{site.data.keyword.objectstorageshort}} di cui hai preso nota precedentemente. Non includere le virgolette utilizzate nelle credenziali. Per *ENCRYPTION_PASSPHRASE*, includi una passphrase per proteggere tramite password il backup. Questa passphrase è diversa dalla frase che hai utilizzato quando hai creato la chiave di crittografia. Devi includere questa passphrase quando eseguirai il backup e il ripristino dei dati.

    ```
    BACKUP_NAME=<volume_name>
    BACKUP_DIRECTORY=/backup
    # PROJECTID, REGION, USERID, PASSWORD provengono dalle credenziali di Object Storage.
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    SCHEDULE_TYPE=periodic
    SCHEDULE_INFO=daily
    BACKUP_TYPE=incremental
    ENCRYPTION_REQUIRED=yes
    # Includi questa passphrase durante il backup e il ripristino dei dati crittografati.
    ENCRYPTION_PASSPHRASE=
    ```
    {: codeblock}

    Queste impostazioni creano un backup incrementale giornaliero che viene crittografato con il nome *<volume_name>*. Per creare un backup con impostazioni differenti, rivedi l'elenco completo di [opzioni di variabili di ambiente](#reference_backup_restore).

7.  Accedi alla CLI {{site.data.keyword.containerlong_notm}}.

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

8.  Esegui un contenitore dall'immagine **ibm-backup-restore** con un volume montato di cui eseguire il backup. Includi il comando per avviare lo script di backup.

    -   Assicurati di essere nella stessa directory locale di <em>&lt;encryption_env-file_name&gt;</em>.
    -   La directory su cui è montato il volume deve corrispondere alla BACKUP_DIRECTORY nel file di variabili di ambiente.
    -   Sostituisci il nome del volume con il nome del volume di cui si eseguirà il backup.
   
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<encrypted_env-file_name> registry.ng.bluemix.net/ibm-backup-restore
    ```
    {: pre}

    Se il contenitore non avvia l'esecuzione, esegui `bx ic logs <container_name>` per controllare i messaggi di errore nei log.

9.  Copia la chiave di crittografia nella directory /backup_restore del contenitore creato dall'immagine **ibm-backup-restore**.

    ```
    bx ic cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    Conserva una copia della chiave di crittografia in locale per decrittografare i tuoi dati.

10. Accedi al contenitore.

    ```
    bx ic exec -it <container_name> bash
    ```
    {: pre}

11. Conferma che *encryption.asc* sia stato copiato nella cartella backup_restore.

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

12. Esegui lo script di backup dalla cartella backup_restore.

    ```
    ./vbackup &
    ```
    {: codeblock}

13. Per confermare che il tuo backup è crittografato, controlla i file nella tua istanza {{site.data.keyword.objectstorageshort}}. I file hanno ora `.gpg` aggiunto alla fine del nome file. ![La GUI {{site.data.keyword.objectstorageshort}} mostra tutti i file di cui è stato eseguito il backup con accodato .gpg, che significa che sono stati crittografati.](images/volume_backup_encrypt_screenshot.png)

Il tuo backup è crittografato. Per ripristinare i file, segui la procedura in [Esecuzione dello script di ripristino](#restore_script_cli) e includi il file encryption.asc nella directory backup_restore sul contenitore che sta eseguendo il processo di ripristino. Se il backup è crittografato, devi fornire ENCRYPTION_REQUIRED e ENCRYPTION_PASSPHRASE come variabili di ambiente durante la creazione del contenitore di ripristino.

## Riferimenti alla variabile di ambiente 
{: #reference_backup_restore}

Rivedi l'elenco completo dei campi che possono essere trasmessi come variabili di ambiente o modificati nel file `config.conf` in un contenitore in esecuzione. Ogni valore trasmesso come variabile di ambiente sostituisce il valore nel file `config.conf`. Per controllare le variabili di ambiente di un contenitore, accedi ad esso utilizzando `exec` ed esegui `env`.

|Chiave|Opzioni valore|
|---|-------------|
|PROJECTID|Il ProjectID da {{site.data.keyword.objectstorageshort}}|
|REGION|La regione da {{site.data.keyword.objectstorageshort}}|
|USERID|L''Userid da {{site.data.keyword.objectstorageshort}}|
|PASSWORD|La password da {{site.data.keyword.objectstorageshort}}|
{: caption="Tabella 1. Variabili {{site.data.keyword.objectstorageshort}}" caption-side="top"}

Richiama queste variabili dalle credenziali {{site.data.keyword.objectstorageshort}}. Includi queste variabili di ambiente in ogni contenitore che sta eseguendo un'operazione di backup o ripristino.

|Chiave|Opzioni valore|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: predefinito. Il percorso file assoluto della directory su cui è montato il volume. Viene eseguito il backup dei dati da questa directory. Non selezionare la directory backup_restore poiché tale directory contiene i file per i processi di backup e ripristino. |
|BACKUP_NAME|*volume_backup*: predefinito. Scegli un nome di backup. |
|BACKUP_TYPE|*full*: predefinito. Viene eseguito ogni volta il backup di tutti i file. <br/> *incremental*: viene eseguito il backup solo dei file nuovi o modificati. Se scegli *incremental*, devi scegliere i valori per SCHEDULING_INFO e SCHEDULING_TYPE.|
|SCHEDULE_TYPE|*none*: predefinito. Crea un backup monouso. <br/> *periodic*: modifica il valore con periodic per creare backup pianificati. |
|SCHEDULE_INFO|*hourly*: crea un backup orario. <br/>*daily*: predefinito. Crea un backup giornaliero.<br/>*weekly*: Crea un backup settimanale. Devi includere questa variabile se stai pianificando un aggiornamento periodico.|
|EXCLUDE_DIRECTORIES|*none*: predefinito. Includi il percorso file assoluto delle directory che desideri escludere dal backup. Separa le directory con una virgola.|
{: caption="Tabella 2. Variabili di backup" caption-side="top"}

|Chiave|Opzioni valore|
|---|-------------|
|BACKUP_NAME|*volume_backup*: predefinito. Includi il nome del backup che verrà ripristinato da {{site.data.keyword.objectstorageshort}}.|
|RESTORE_DIRECTORY|*/backup*: predefinito. La directory assoluta su cui è montato il volume. I dati vengono ripristinati in questa directory. Non selezionare la directory backup_restore poiché tale directory contiene i file per i processi di backup e ripristino. |
{: caption="Tabella 3. Variabili di ripristino" caption-side="top"}

|Chiave|Opzioni valore|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: predefinito. Includi questa variabile di ambiente se modifichi il nome file della chiave di crittografia o se la chiave è ubicata in una directory diversa da backup_restore.|
|ENCRYPTION_REQUIRED|*no*: predefinito.<br/> *yes*: se non stai crittografando il tuo backup, non includere le variabili di ambiente di crittografia. Se stai crittografando il tuo backup, includi questa chiave con il valore yes.|
|ENCRYPTION_PASSPHRASE|Includi una passphrase per proteggere un backup. Questa passphrase è diversa dalla frase che hai utilizzato quando hai creato la chiave di crittografia. Devi includere questa passphrase quando eseguirai il backup e il ripristino dei dati.|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: predefinito.<br/> *yes*: includi questa variabile di ambiente con yes se hai generato la chiave di crittografia direttamente nel contenitore. La maggior parte degli utenti genera la chiave sul proprio computer locale e la copia nel contenitore e può lasciare il valore predefinito di no.|
{: caption="Tabella 4. Variabili di crittografia" caption-side="top"}

