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

# Introduzione all'immagine ibm-backup-restore
{: #ibmbackup_restore_starter}

L'immagine **ibm-backup-restore** contiene i pacchetti pre-installati necessari per il backup e il ripristino della memoria persistente in {{site.data.keyword.containerlong}}.
{:shortdesc}

## Come funziona 
{: #how_it_works}

Con l'immagine **ibm-backup-restore**, puoi creare un backup una tantum o pianificato per ogni attestazione del volume persistente (pvc). Il backup viene archiviato nell'istanza {{site.data.keyword.objectstoragefull}}. Puoi trasmettere le credenziali {{site.data.keyword.objectstorageshort}} al tuo contenitore **ibm-backup-restore** come variabili di ambiente o modificando il file `config.conf` nel contenitore in esecuzione. Puoi anche ripristinare i tuoi dati salvati dall'istanza {{site.data.keyword.objectstorageshort}} in un volume. Poiché l'immagine contiene gli script per l'esecuzione sia dei backup che dei ripristini, l'utente deve immettere un comando per avviare lo script appropriato.

## Elementi inclusi 
{: #whats_included}

Ogni immagine **ibm-backup-restore** contiene i seguenti pacchetti software:

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   Pacchetti python, gnupg e wget

## Introduzione 
{: #how_to_get_started}

Rivedi le seguenti attività per eseguire il backup e ripristinare i dati:
1.  [Creazione di un'istanza del servizio {{site.data.keyword.objectstorageshort}}](#object_storage)
2.  [Esecuzione di un backup pianificato](#scheduled_backup)
3.  [Esecuzione dello script di ripristino](#restore_script_cli)
4.  [Crittografia dei tuoi backup](#encrypting_backups)
5.  [Riferimenti alla variabile di ambiente](#reference_backup_restore)

## Creazione di un'istanza del servizio {{site.data.keyword.objectstorageshort}} 
{: #object_storage}

Crea un'istanza {{site.data.keyword.objectstorageshort}} da utilizzare come repository per il tuo backup del volume.

1.  Esegui il provisioning della tua istanza {{site.data.keyword.objectstorageshort}} dalla sezione **Memoria** del catalogo {{site.data.keyword.Bluemix_notm}}.
2.  Fai clic su {{site.data.keyword.objectstorageshort}}.
3.  Seleziona {{site.data.keyword.objectstorageshort}} OpenStack Swift per {{site.data.keyword.Bluemix_notm}}. Quindi fai clic su crea.
3.  Fai clic sulla scheda **Credenziali del servizio**.
4.  Fai clic su **Nuova credenziale**.
5.  Completa il campo nome ma lascia vuoti gli altri campi. Fai clic su **Aggiungi**.
6.  Le tue nuove credenziali sono ora elencate nella tabella **Credenziali del servizio**. Fai clic su **Visualizza credenziali**.
7.  Prendi nota di **projectid**, **region**, **userId** e **password**. Queste credenziali consentono al tuo contenitore **ibm-backup-restore** di accedere a questa istanza {{site.data.keyword.objectstorageshort}}.

Rivedi la documentazione [{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) per ulteriori informazioni sulla configurazione della tua istanza e rivedi le [opzioni del piano](../../ObjectStorage/objectstorage_faq.html#account-payment) per informazioni sui prezzi {{site.data.keyword.objectstorageshort}}.

## Esecuzione di un backup pianificato 
{: #scheduled_backup}

Crea un pod del contenitore dall'immagine **ibm-backup-restore** e avvia un backup pianificato regolarmente.

Prima di iniziare:

-   Installa le [CLI](../../../containers/cs_cli_install.html#cs_cli_install) obbligatorie.
-   [Indirizza la tua CLI](../../../containers/cs_cli_install.html#cs_cli_configure) al tuo cluster.


1. Crea un file di configurazione denominato _backup-pvc.yaml_. Questo file di configurazione crea un'attestazione del volume persistente (pvc) che puoi montare nel tuo pod di backup come un volume.

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
    
2. Crea il pvc.

    ```
    kubectl apply -f backup-pvc.yaml 
    ```
    {: pre}

3.  Crea un file di configurazione denominato _backup.yaml_. Per le variabili di ambiente vuote, immetti i valori dalle credenziali {{site.data.keyword.objectstorageshort}} di cui hai preso nota precedentemente. Includi le virgolette utilizzate nelle credenziali. Sostituisci <em>&lt;pod_name&gt;</em>, <em>&lt;container_name&gt;</em> e il registro delle immagini <em>&lt;region&gt;</em> con i valori appropriati.

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
    
    Queste impostazioni creano un backup incrementale giornaliero con il nome predefinito _mybackup_. Per creare un backup con impostazioni differenti, rivedi l'elenco completo di [opzioni di variabili di ambiente](#reference_backup_restore).

4.  crea un pod che esegue lo script di backup.

    ```
    kubectl apply -f backup.yaml
    ```
    {: pre}

5.  Conferma che il pod è in esecuzione.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

4.  Controlla il backup in {{site.data.keyword.objectstorageshort}} nella GUI {{site.data.keyword.Bluemix_notm}}.
    1.  Fai clic sull'istanza {{site.data.keyword.objectstorageshort}} creata per il backup.
    2.  Dalla scheda **Gestisci** nella tabella **Contenitori**, fai clic sul contenitore {{site.data.keyword.objectstorageshort}}.
    3.  Rivedi i file compressi.![Il contenitore Object Storage nella GUI {{site.data.keyword.Bluemix_notm}} mostra i file di cui è stato eseguito il backup.](images/volume_backup_screenshot.png)Puoi scaricare il file vol1.difftar.gz, estrarlo e verificare i dati di cui è stato eseguito il backup. **Importante**: se elimini o modifichi i file da {{site.data.keyword.objectstorageshort}}, questi file non potranno essere ripristinati.

Il tuo backup è disponibile. Se hai configurato il tuo backup per creare un backup completo una tantum, devi eseguire lo script di backup ogni volta che vuoi creare un nuovo backup. Se hai configurato il contenitore per eseguire un backup incrementale periodico, i tuoi backup vengono eseguiti come pianificato.

## Esecuzione dello script di ripristino 
{: #restore_script_cli}

Ripristina il tuo backup da {{site.data.keyword.objectstorageshort}} in un volume nuovo o esistente.

Prima di iniziare:

-   Installa le [CLI](../../../containers/cs_cli_install.html#cs_cli_install) obbligatorie.
-   [Indirizza la tua CLI](../../../containers/cs_cli_install.html#cs_cli_configure) al tuo cluster.


1. Crea un file di configurazione denominato _restore-pvc.yaml_. Questo file di configurazione crea un'attestazione del volume persistente (pvc) che puoi montare nel tuo pod di ripristino come un volume.

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
    
2. Crea il pvc.

    ```
    kubectl apply -f restore-pvc.yaml 
    ```
    {: pre}

3.  Crea un file di configurazione denominato _restore.yaml_. Per le variabili di ambiente vuote, immetti i valori dalle credenziali {{site.data.keyword.objectstorageshort}} di cui hai preso nota precedentemente. Includi le virgolette utilizzate nelle credenziali. Il BACKUP_NAME deve corrispondere al nome del backup in {{site.data.keyword.objectstorageshort}} che verrà ripristinato.

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

4.  crea un pod che esegue lo script di ripristino.
    
    ```
    kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  Conferma che il pod è in esecuzione.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    Il pod esegue il comando restore e si arresta. Il messaggio _CrashLoopBackOff_ significa che Kubernetes sta tentando di riavviare il pod.

6.  Rimuovi il pod per impedire che utilizzi ulteriori risorse.

    ```
    kubectl delete -f restore.yaml
    ```
    {: pre}

Il backup è stato ripristinato correttamente. Puoi ora montare ogni nuovo pod nel pvc per fornire a tale contenitore l'accesso ai file ripristinati. Se i dati del contenitore di cui è stato eseguito il backup includevano un utente non root, devi aggiungere le autorizzazioni non root al tuo nuovo contenitore. Per ulteriori informazioni, consulta [Aggiunta di accesso utente non root ai volumi](../../../containers/container_volumes_ov.html#container_volumes_write).

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
    uid       [ultimate] Example Name (Questa è una chiave di esempio) <example_email_address>
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
    
6. Crea un file di configurazione denominato _backup-pvc.yaml_. Questo file di configurazione crea un'attestazione del volume persistente (pvc) che puoi montare nel tuo pod di backup come un volume.

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

7. Crea il pvc.

    ```
    kubectl apply -f backup-pvc.yaml 
    ```
    {: pre}

8.  Modifica il file di configurazione di distribuzione e aggiungi i seguenti campi. Per le variabili di ambiente vuote, immetti i valori dalle credenziali {{site.data.keyword.objectstorageshort}} di cui hai preso nota precedentemente. Includi le virgolette utilizzate nelle credenziali. Per *ENCRYPTION_PASSPHRASE*, includi una passphrase per proteggere tramite password il backup. Questa passphrase è diversa dalla frase che hai utilizzato quando hai creato la chiave di crittografia. Devi includere questa passphrase quando eseguirai il backup e il ripristino dei dati.

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
           # Includi questa passphrase quando esegui il backup e il ripristino di dati crittografati.
            value: 
          volumeMounts:
          - mountPath: /backup
            name: backup-volume
      volumes:
      - name: backup-volume 
        persistentVolumeClaim:
          claimName: backuppvc
   ```
   
   Queste impostazioni creano un backup incrementale giornaliero che viene crittografato con il nome <em>&lt;volume_name&gt;</em>. Per creare un backup con impostazioni differenti, rivedi l'elenco completo di [opzioni di variabili di ambiente](#reference_backup_restore).
    
9.  crea un pod che esegue lo script di backup.

    ```
    kubectl apply -f backup.yaml 
    ```
    {: pre}

9.  Conferma che il pod è in esecuzione.

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

10.  Copia la chiave di crittografia nella directory /backup_restore del contenitore creato dall'immagine **ibm-backup-restore**.

    ```
    kubectl cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    Conserva una copia della chiave di crittografia in locale per decrittografare i tuoi dati.

11. Accedi al contenitore.

    ```
    kubecl exec -it <container_name> bash
    ```
    {: pre}

12. Conferma che *encryption.asc* sia stato copiato nella cartella backup_restore.

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

13. Esegui lo script di backup dalla cartella backup_restore.

    ```
    ./vbackup &
    ```
    {: codeblock}

13. Per confermare che il tuo backup è crittografato, controlla i file nella tua istanza {{site.data.keyword.objectstorageshort}}. I file hanno ora `.gpg` accodato al nome. ![La GUI Object Storage mostra i file oggetto del backup con accodato .gpg, che mostra che sono crittografati.](images/volume_backup_encrypt_screenshot.png)

Il tuo backup è crittografato. Per ripristinare i file, segui la procedura in [Esecuzione dello script di ripristino](#restore_script_cli) e includi il file encryption.asc nella directory backup_restore sul contenitore che sta eseguendo il processo di ripristino. Se il backup è crittografato, devi fornire ENCRYPTION_REQUIRED e ENCRYPTION_PASSPHRASE dal file yaml di backup come variabili di ambiente durante la creazione del contenitore di ripristino.

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
|BACKUP_DIRECTORY|*/backup*: predefinito. Il percorso file assoluto della directory su cui è montato il volume. Viene eseguito il backup dei dati da questa directory. Non selezionare la directory backup_restore poiché tale directory contiene i file per i processi di backup e ripristino.|
|BACKUP_NAME|*volume_backup*: predefinito. Scegli un nome di backup.|
|BACKUP_TYPE|*full*: predefinito. Viene eseguito ogni volta il backup di tutti i file.<br/> *incremental*: viene eseguito il backup solo dei file nuovi o modificati. Se scegli *incremental*, devi scegliere i valori per SCHEDULING_INFO e SCHEDULING_TYPE.|
|SCHEDULE_TYPE|*none*: predefinito. Crea un backup una tantum.<br/> **Nota:** se scegli di creare un backup una tantum, il tuo pod viene rimosso dal cluster dopo che il backup è terminato. <br/>*periodic*: modifica il valore con periodic per creare backup pianificati.|
|SCHEDULE_INFO|*hourly*: crea un backup orario.<br/>*daily*: predefinito. Crea un backup giornaliero.<br/>*weekly*: Crea un backup settimanale. Devi includere questa variabile se stai pianificando un aggiornamento periodico.|
|EXCLUDE_DIRECTORIES|*none*: predefinito. Includi il percorso file assoluto delle directory che desideri escludere dal backup. Separa le directory con una virgola.|
{: caption="Tabella 2. Variabili di backup" caption-side="top"}

|Chiave|Opzioni valore|
|---|-------------|
|BACKUP_NAME|*volume_backup*: predefinito. Includi il nome del backup che verrà ripristinato da {{site.data.keyword.objectstorageshort}}.|
|RESTORE_DIRECTORY|*/backup*: predefinito. La directory assoluta su cui è montato il volume. I dati vengono ripristinati in questa directory. Non selezionare la directory backup_restore poiché tale directory contiene i file per i processi di backup e ripristino.|
{: caption="Tabella 3. Variabili di ripristino" caption-side="top"}

|Chiave|Opzioni valore|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: predefinito. Includi questa variabile di ambiente se modifichi il nome file della chiave di crittografia o se la chiave è ubicata in una directory diversa da backup_restore.|
|ENCRYPTION_REQUIRED|*no*: predefinito.<br/> *yes*: se non stai crittografando il tuo backup, non includere le variabili di ambiente di crittografia. Se stai crittografando il tuo backup, includi questa chiave con il valore yes.|
|ENCRYPTION_PASSPHRASE|Includi una passphrase per proteggere un backup. Questa passphrase è diversa dalla frase che hai utilizzato quando hai creato la chiave di crittografia. Devi includere questa passphrase quando eseguirai il backup e il ripristino dei dati.|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: predefinito.<br/> *yes*: includi questa variabile di ambiente con yes se hai generato la chiave di crittografia direttamente nel contenitore. La maggior parte degli utenti genera la chiave sul proprio computer locale e la copia nel contenitore e può lasciare il valore predefinito di no.|
{: caption="Tabella 4. Variabili di crittografia" caption-side="top"}

## Migrazione dei tuoi dati del volume da contenitori singoli e scalabili a Kubernetes
{: #migrate_data}

Crea un backup una tantum per qualsiasi volume del contenitore. Il backup viene archiviato nell'istanza {{site.data.keyword.objectstoragefull}}. Successivamente, puoi migrare i dati a un'attestazione del volume persistente in Kubernetes.
{:shortdesc}

### Introduzione 
{: #how_to_get_started_migrating}

Prima di iniziare:

- [Controlla il percorso di migrazione completo per lo spostamento delle applicazioni a Kubernetes](../../../containers/cs_classic.html)
- [Installa la CLI dei contenitori singoli e scalabili (bx ic)](../../../containers/container_cli_cfic_install.html)
- [Installa la CLI {{site.data.keyword.containershort}} (bx cs e kubectl)](../../../containers/cs_cli_install.html#cs_cli_install)
- [Crea un cluster Kubernetes standard a cui migrare i tuoi dati](../../../containers/cs_clusters.html#clusters_cli)

Completa le seguenti attività per eseguire le operazioni di backup e ripristino:
1.  [Creazione di un'istanza del servizio {{site.data.keyword.objectstorageshort}}](#object_storage) (come ce ne si occupava in precedenza)
2.  [Esecuzione di un backup una tantum](#migrate_backup)
3.  [Esecuzione dello script di ripristino in Kubernetes](#migrate_restore)

### Esecuzione di un backup una tantum 
{: #migrate_backup}

Crea un singolo contenitore dall'immagine **ibm-backup-restore** e avvia un backup.

1.  Accedi alla CLI {{site.data.keyword.containershort}}.

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

3.  Modifica il file di variabili di ambiente e aggiungi i seguenti campi. Per le variabili di ambiente vuote, immetti i valori dalle credenziali {{site.data.keyword.objectstorageshort}} di cui hai preso nota precedentemente. **Non** includere le virgolette utilizzate nelle credenziali.

    ```
    BACKUP_NAME=volume_backup
    BACKUP_DIRECTORY=/backup
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    ```
    {: codeblock}

    Queste impostazioni creano un backup una tantum con il nome predefinito _volume_backup_. 

4.  Esegui un contenitore dall'immagine **ibm-backup-restore** con un volume montato di cui eseguire il backup. Includi il comando per avviare lo script di backup.

    -   Assicurati di essere nella stessa directory locale di <em>&lt;backup_env-file&gt;</em>.
    -   La directory su cui è montato il volume deve corrispondere alla BACKUP_DIRECTORY nel file di variabili di ambiente.
    -   Sostituisci <em>&lt;volume_name&gt;</em> con il nome del volume di cui si sta eseguendo il backup
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<backup_env-file_name> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    Dopo aver eseguito il backup, il contenitore si arresta. Se il contenitore non avvia l'esecuzione, esegui `bx ic logs <container_name>` per controllare i messaggi di errore nei log.

5.  Controlla il backup in {{site.data.keyword.objectstorageshort}} nella GUI {{site.data.keyword.Bluemix_notm}}.
    1.  Fai clic sull'istanza {{site.data.keyword.objectstorageshort}} creata per il backup.
    2.  Fai clic sul contenitore {{site.data.keyword.objectstorageshort}}. In questo esempio, il nome del contenitore è volume_backup.
    3.  Controlla i file compressi.![Il contenitore Object Storage nella GUI {{site.data.keyword.Bluemix_notm}} mostra i file di cui è stato eseguito il backup.](images/volume_backup_screenshot.png)Puoi scaricare il file difftar.gz, estrarlo e verificare i dati di cui è stato eseguito il backup. **Importante**: se elimini o modifichi i file da {{site.data.keyword.objectstorageshort}}, questi file non potranno essere ripristinati.

### Ripristino dei tuoi dati in un cluster Kubernetes 
{: #migrate_restore}

Ripristina il tuo backup da {{site.data.keyword.objectstorageshort}} in un'attestazione del volume persistente in un cluster Kubernetes.

Prima di iniziare:

- [Indirizza la tua CLI](../../../containers/cs_cli_install.html#cs_cli_configure) al tuo cluster.


1. Crea un file di configurazione denominato _restore-pvc.yaml_. Questo file di configurazione crea un'attestazione del volume persistente (pvc) che puoi montare nel tuo pod di backup come un volume.

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

2. Crea il pvc.

    ```
    kubectl apply -f restore-pvc.yaml 
    ```
    {: pre}

3.  Crea un file di configurazione denominato _restore.yaml_. Per le variabili di ambiente vuote, immetti i valori dalle credenziali {{site.data.keyword.objectstorageshort}} di cui hai preso nota precedentemente. Includi le virgolette utilizzate nelle credenziali. Il BACKUP_NAME deve corrispondere al nome del backup in {{site.data.keyword.objectstorageshort}} che verrà ripristinato.

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

4.  Crea un pod che esegue lo script di ripristino.

    ```
    kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  Conferma che il pod è in esecuzione.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}
    
    Il pod esegue il comando restore e si arresta. Il messaggio _CrashLoopBackOff_ significa che Kubernetes sta tentando di riavviare il pod.

6.  Rimuovi il pod per impedire che utilizzi ulteriori risorse.

    ```
    kubectl delete -f restore.yaml
    ```
    {: pre}

Hai correttamente migrato i tuoi dati al tuo cluster Kubernetes. Puoi ora montare ogni nuovo pod nel pvc per fornire a tale pod l'accesso ai file ripristinati. 

**Nota**: se i dati del contenitore di cui è stato eseguito il backup includevano un utente non root, devi aggiungere le autorizzazioni non root al tuo nuovo contenitore. Per ulteriori informazioni, consulta [Aggiunta di accesso utente non root ai volumi](../../../containers/container_volumes_ov.html#container_volumes_write).
