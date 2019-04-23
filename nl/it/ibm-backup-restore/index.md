---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-13"

keywords: IBM Cloud Container Registry, IBM Cloud Kubernetes Service, ibm-backup-restore, container image, back up data, restore data

subcollection: RegistryImages

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:table: .aria-labeledby="caption"}

# Introduzione all'immagine `ibm-backup-restore`
{: #ibmbackup_restore_starter}

L'immagine `ibm-backup-restore` contiene i pacchetti pre-installati necessari per il backup e il ripristino della memoria persistente in {{site.data.keyword.containerlong}}.
{:shortdesc}

Puoi accedere alle immagini fornite da {{site.data.keyword.IBM_notm}} utilizzando la riga di comando, consulta [Immagini pubbliche IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Come funziona
{: #backup_restore_how_it_works}

Con l'immagine `ibm-backup-restore`, puoi creare un backup monouso o pianificato per i dati dell'applicazione che vengono archiviati in un volume persistente (PV) nel tuo cluster o ripristinarli da un PV. Per eseguire il backup e il ripristino dei dati, distribuisci un pod dall'immagine `ibm-backup-restore`. In seguito, monta il PVC che associa il PV di cui vuoi eseguire il backup o che vuoi utilizzare per ripristinare i tuoi dati nel tuo pod.

**Dove vanno i miei dati? Come posso accedervi?**

I dati di cui esegui il backup vengono archiviati in un'istanza del servizio {{site.data.keyword.cos_full_notm}}. Per accedere al servizio, utilizza le tue credenziali del servizio {{site.data.keyword.cos_full_notm}} come variabili di ambiente nel pod `ibm-backup-restore` o modifica il file `config.conf` nel pod in esecuzione.

**Posso ripristinare i dati di backup in un'applicazione o un PV differente?**

Sì, puoi ripristinare i tuoi dati salvati dall'istanza del servizio {{site.data.keyword.cos_full_notm}} in un PV nel tuo cluster. Per ripristinare i dati, devi creare un pod di ripristino dall'immagine `ibm-backup-restore`. In seguito, monta il PVC che associa il PV che vuoi utilizzare nel tuo pod.  

## Elementi inclusi
{: #backup_restore_whats_included}

Ogni immagine `ibm-backup-restore` contiene i seguenti pacchetti software:

- Alpine 3.7
- Duplicity 0.7.10
- Pacchetti Python e GnuPG Made Easy (GPGME)

## Configurazione di un'istanza del servizio {{site.data.keyword.cos_full_notm}}
{: #backup_restore_setup_object_storage}

Crea e configura un'istanza del servizio {{site.data.keyword.cos_full_notm}} da utilizzare come repository per i dati di cui vuoi eseguire il backup.
{: shortdesc}

1. Distribuisci un'istanza del servizio {{site.data.keyword.cos_full_notm}}.
   1. Apri il [catalogo {{site.data.keyword.Bluemix_notm}} ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://cloud.ibm.com/catalog/services/cloud-object-storage).
   2. Immetti un nome per la tua istanza del servizio, come `cos-backup` e seleziona **predefinito** per il tuo gruppo di risorse.
   3. Controlla le [opzioni del piano ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://www.ibm.com/cloud-computing/bluemix/pricing-object-storage#s3api) per informazioni sui prezzi e seleziona un piano.
   4. Fai clic su **Crea**.
2. Controlla le credenziali dell'istanza del servizio {{site.data.keyword.cos_full_notm}}.
   1. Nella navigazione nella pagina dei dettagli del servizio, fai clic su **Credenziali del servizio**.
   2. Fai clic su **Nuova credenziale**. Viene visualizzata una casella di dialogo.
   3. Immetti un nome per le tue credenziali.
   4. In **Aggiungi parametri di configurazione inline (facoltativo)**, immetti `{"HMAC":true}` per creare ulteriori credenziali HMAC che il pod `ibm-backup-restore` utilizza per l'autenticazione con il servizio {{site.data.keyword.cos_full_notm}}.
   5. Fai clic su **Aggiungi**. Le tue nuove credenziali sono elencate nella tabella **Credenziali del servizio**.
   6. Fai clic su **Visualizza credenziali**.
   7. Prendi nota di **access_key_id** e **secret_access_key** che puoi trovare nella sezione **cos_hmac_keys**.
3. Crea il tuo primo bucket {{site.data.keyword.cos_full_notm}}.
   1. Nella navigazione nella pagina dei dettagli del servizio, fai clic su **Bucket**.
   2. Fai clic su **Crea bucket**. Viene visualizzata una casella di dialogo.
   3. Immetti un nome univoco per il tuo bucket. Il nome deve essere univoco all'interno di {{site.data.keyword.cos_full_notm}} in tutte le regioni e in tutti gli account {{site.data.keyword.Bluemix_notm}}.
   4. Dal menu a discesa **Resilienza**, seleziona il livello di disponibilità che desideri per i tuoi dati. Per ulteriori informazioni, vedi [Endpoint e regioni {{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-endpoints#endpoints).
   5. Modifica l'**Ubicazione** con la regione in cui vuoi archiviare i tuoi dati. Tieni presente che potrebbe non essere consentito archiviare i tuoi dati in tutte le regioni per motivi legali.  
   6. Fai clic su **Crea**.
4. Controlla il nome host {{site.data.keyword.cos_full_notm}} del tuo bucket.
   1. Fai clic sul nome del bucket che hai creato nel passo precedente.
   2. Nella navigazione nella pagina dei dettagli del servizio, fai clic su **Bucket** > **Configurazione**.
   3. Prendi nota dell'URL pubblico che puoi utilizzare per accedere ai dati nel tuo bucket.

Controlla la [documentazione {{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage?topic=cloud-object-storage-about-ibm-cloud-object-storage#about-ibm-cloud-object-storage) per ulteriori informazioni sulla configurazione della tua istanza del servizio.

## Backup dei dati da un volume persistente
{: #backup_restore_scheduled_backup}

Puoi creare un backup pianificato o monouso per ogni volume persistente (PV) che viene montato nel tuo pod dell'applicazione tramite un'attestazione del volume persistente (PVC).  
{: shortdesc}

Il seguente esempio ti illustra come distribuire un pod di backup dall'immagine `ibm-backup-restore`, montare un PV esistente nel pod di backup utilizzando una PVC e come eseguire il backup dei dati dal PV nella tua istanza del servizio {{site.data.keyword.cos_full_notm}}.  

**Prima di iniziare**

- [Configurazione di un'istanza del servizio {{site.data.keyword.cos_full_notm}}](#backup_restore_setup_object_storage).
- Installa le [CLI](/docs/containers?topic=containers-cs_cli_install#cs_cli_install) richieste per creare e utilizzare il tuo cluster.
- [Crea un cluster standard](/docs/containers?topic=containers-clusters#clusters_cli) o utilizzane uno esistente.
- [Indirizza la tua CLI al tuo cluster](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure).
- Crea un'attestazione del volume persistente (o PVC; persistent volume claim) per la tua [archiviazione file](/docs/containers?topic=containers-file_storage#add_file) o [archiviazione blocchi](/docs/containers?topic=containers-block_storage#add_block) e montala nella tua distribuzione dell'applicazione.

Per eseguire il backup di un PV esistente, completa la seguente procedura:

1. Ottieni il nome della PVC che associa il PV di cui vuoi eseguire il backup.

   ```
   kubectl get pvc
   ```
   {: pre}

2. Crea un pod di backup dall'immagine `ibm-backup-restore`. Per accedere ai dati nel PV, devi montare la PVC che associa il PV al tuo pod di backup. Il seguente esempio crea un pod di backup che esegue un backup incrementale giornaliero. Per creare un backup con impostazioni differenti, rivedi l'elenco completo di [opzioni di variabili di ambiente](#backup_restore_env_reference).

   L'immagine `ibm-backup-restore` deve essere distribuita in un solo pod e non può essere utilizzata come parte di una distribuzione Kubernetes.
   {: important}

   Per visualizzare l'immagine, specifica il registro globale eseguendo il comando `ibmcloud cr region-set global`. Quindi, esegui `ibmcloud cr images --include-ibm` per elencare le immagini pubbliche di IBM.
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
   <caption>Tabella 1. Componenti file YAML</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Icona Idea"/> Descrizione dei componenti del file YAML</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>L'ID della chiave di accesso che hai richiamato come parte delle tue credenziali dell'istanza del servizio {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</code></td>
     <td>La chiave di accesso del segreto che hai richiamato come parte delle tue credenziali dell'istanza del servizio {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>L'URL dell'endpoint API regionale che utilizzi per accedere a {{site.data.keyword.cos_full_notm}} in una regione specifica. </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>Il nome del bucket che vuoi utilizzare per archiviare i tuoi backup in {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>Un nome univoco per l'oggetto che contiene il tuo backup nel bucket. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>Il nome della PVC che associa il PV di cui vuoi eseguire il backup. </td>
     </tr>
     </tbody>
     </table>
    
3. Crea il pod di backup e avvia un backup dei tuoi dati del PV.

    ```
    kubectl apply -f backuppod.yaml
    ```
    {: pre}

4. Verifica che il pod è in esecuzione.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

5. Verifica che il backup sia stato eseguito correttamente.

    ```
    kubectl logs backuppod
    ```
    {: pre}

6. Controlla il backup in {{site.data.keyword.cos_full_notm}} nella GUI {{site.data.keyword.Bluemix_notm}}.
    1. Dal dashboard {{site.data.keyword.Bluemix_notm}}, trova l'istanza del servizio {{site.data.keyword.cos_full_notm}}.
    2. Dalla navigazione, seleziona **Bucket** e fai clic sul bucket che hai utilizzato nella tua configurazione del backup. Il tuo backup viene visualizzato come un oggetto nel tuo bucket.
    3. Rivedi i file compressi. Puoi scaricare il file `vol1.difftar.gz`, estrarlo e verificare i dati del backup.

       Se elimini o modifichi eventuali file da {{site.data.keyword.cos_full_notm}}, questi file non possono essere ripristinati.
       {: important}

Il tuo backup è disponibile. Se hai configurato il tuo backup per creare un backup completo monouso, devi eseguire lo script di backup ogni volta che vuoi creare un nuovo backup. Se hai configurato il contenitore per eseguire un backup incrementale periodico, i tuoi backup vengono eseguiti come pianificato.

## Ripristino dei dati da {{site.data.keyword.cos_full_notm}} al tuo cluster
{: #backup_restore_restore_script_cli}

Puoi ripristinare i dati dalla tua istanza del servizio {{site.data.keyword.cos_full_notm}} in un PV nel tuo cluster.

**Prima di iniziare**

- [Indirizza la tua CLI al tuo cluster](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure).
- [Crea un backup per un PV nel tuo cluster](#backup_restore_scheduled_backup).

Per ripristinare i dati da {{site.data.keyword.cos_full_notm}} a un PV, completa la seguente procedura:

1. Ottieni il nome della PVC che associa il PV in cui vuoi ripristinare i tuoi dati.

   ```
   kubectl get pvc
   ```
   {: pre}

2. Crea un pod di ripristino dall'immagine `ibm-backup-restore`. Per ripristinare i dati in un PV, devi montare la PVC che associa il PV al tuo pod di ripristino.

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
   <caption>Tabella 2. Componenti file YAML</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Icona Idea"/> Descrizione dei componenti del file YAML</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>L'ID della chiave di accesso che hai richiamato come parte delle tue credenziali dell'istanza del servizio {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</code></td>
     <td>La chiave di accesso del segreto che hai richiamato come parte delle tue credenziali dell'istanza del servizio {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>L'URL dell'endpoint API regionale che utilizzi per accedere a {{site.data.keyword.cos_full_notm}} in una regione specifica. </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>Il nome del bucket che hai utilizzato per archiviare i tuoi backup in {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>Il nome univoco per l'oggetto che contiene il tuo backup nel bucket. Devi utilizzare il nome utilizzato nel pod di backup per archiviare i tuoi dati in {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>Il nome della PVC che associa il PV in cui vuoi ripristinare i tuoi dati. </td>
     </tr>
     </tbody>
     </table>

3. Crea il pod di ripristino e avvia il ripristino dei tuoi dati.

    ```
    kubectl apply -f restorepod.yaml
    ```
    {: pre}

4. Verifica che il pod è in esecuzione.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME              READY     STATUS             RESTARTS   AGE
    restorepod        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    Il pod esegue il comando restore e si arresta. Il messaggio `CrashLoopBackOff` significa che Kubernetes sta tentando di riavviare il pod.

5. Rimuovi il pod per impedire che utilizzi ulteriori risorse.

    ```
    kubectl delete -f restorepod.yaml
    ```
    {: pre}
    
6. Verifica che i tuoi dati siano stati ripristinati correttamente.

    ```
    kubectl logs restorepod
    ```
    {: pre}

Il backup è stato ripristinato correttamente. Puoi ora montare la PVC che associa il PV in tutti gli altri pod nel tuo cluster per accedere ai file ripristinati. Se i dati del contenitore di cui è stato eseguito il backup includevano un utente non root, devi aggiungere le autorizzazioni non root al tuo nuovo contenitore. Per ulteriori informazioni, consulta [Aggiunta di accesso utente non root ai volumi](/docs/containers?topic=containers-cs_troubleshoot_storage#cs_storage_nonroot).

## Crittografia dei tuoi backup
{: #backup_restore_encrypting_backups}

Codifica i dati nella tua istanza {{site.data.keyword.cos_full_notm}}.

1. Scarica <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Icona link esterno"></a> per creare una chiave di crittografia.
2. Crea una chiave di crittografia nella tua unità locale. Puoi accettare i valori predefiniti premendo INVIO.

    Prendi nota della passphrase che hai creato. Se la perdi, tutte le informazioni crittografate con la tua chiave non possono essere decrittografate.
    {: important}

    ```
    gpg --gen-key
    ```
    {: pre}

    A seconda della versione di <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Icona link esterno"></a>, potresti dover utilizzare `gpg2` invece di `gpg` nei tuoi comandi.

3. Verifica la chiave.

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

4. Esporta la chiave di crittografia con il valore dalla chiave `sub`. Denomina il file `encryption.asc`.

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    In questo esempio, la chiave secondaria ha il valore `YYYYYYYY`.

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  Crea un file di variabili di ambiente per il tuo contenitore di backup crittografato in una directory locale.

    ```
    touch <encryption_env-file_name>
    ```
    {: pre}

6.  Modifica il file di configurazione del pod e aggiungi i seguenti campi. Per le variabili di ambiente vuote, immetti i valori dalle credenziali {{site.data.keyword.cos_full_notm}} di cui hai preso nota precedentemente. Includi le virgolette utilizzate nelle credenziali. Per **ENCRYPTION_PASSPHRASE**, includi una passphrase per proteggere tramite password il backup. Questa passphrase è diversa dalla frase che hai utilizzato quando hai creato la chiave di crittografia. Devi includere questa passphrase quando eseguirai il backup e il ripristino dei dati.

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
    <caption>Tabella 3. Componenti file YAML</caption>
    <thead>
    <th colspan=2><img src="../images/idea.png" alt="Icona Idea"/> Descrizione dei componenti del file YAML</th>
    </thead>
     <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>L'ID della chiave di accesso che hai richiamato come parte delle tue credenziali dell'istanza del servizio {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</em></code></td>
     <td>La chiave di accesso del segreto che hai richiamato come parte delle tue credenziali dell'istanza del servizio {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>L'URL dell'endpoint API regionale che utilizzi per accedere a {{site.data.keyword.cos_full_notm}} in una regione specifica. </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>Il nome del bucket che vuoi utilizzare per archiviare i tuoi backup in {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>Un nome univoco per l'oggetto che contiene il tuo backup nel bucket.</td>
     </tr>
     <tr>
     <td><code>&lt;passphrase&gt;</code></td>
     <td>Qualsiasi stringa che desideri utilizzare per il tuo backup. Devi includere questa passphrase quando ripristini i tuoi dati. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>Il nome della PVC che associa il PV di cui vuoi eseguire il backup. </td>
     </tr>
     </tbody>
     </table>
   
    Queste impostazioni creano un backup incrementale giornaliero che viene crittografato. Per creare un backup con impostazioni differenti, rivedi l'elenco completo di [opzioni di variabili di ambiente](#backup_restore_env_reference).
    
7.  Crea il pod di backup. 

    ```
    kubectl apply -f backuppod.yaml 
    ```
    {: pre}

8.  Verifica che il pod è in esecuzione.

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

9.  Copia la chiave di crittografia nella directory `/backup_restore` del contenitore creato dall'immagine `ibm-backup-restore`.

    ```
    kubectl cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    Conserva una copia della chiave di crittografia in locale per decrittografare i tuoi dati.

10. Accedi al pod e passa alla cartella `backup_restore`. 

    ```
    kubecl exec -it <pod_name> bash
    ```
    {: pre}

11. Verifica che il file `encryption.asc` sia stato copiato nella cartella `backup_restore`.

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

13. Per confermare che il tuo backup è crittografato, controlla i file nella tua istanza del servizio {{site.data.keyword.cos_full_notm}}. I file hanno ora accodato `.gpg` alla fine del nome file.

Il tuo backup è crittografato. Per ripristinare i file, segui la procedura in [Ripristino dei dati da {{site.data.keyword.cos_full_notm}} a una PVC nel tuo cluster](#backup_restore_restore_script_cli) e includi il file `encryption.asc` nella directory `backup_restore` del pod che esegue il processo di ripristino. Se il backup è crittografato, devi fornire le variabili di ambiente **ENCRYPTION_REQUIRED** e **ENCRYPTION_PASSPHRASE** quando crei il pod di ripristino.

## Riferimenti alla variabile di ambiente
{: #backup_restore_env_reference}

Rivedi l'elenco completo dei campi che possono essere trasmessi come variabili di ambiente o modificati nel file `config.conf` in un pod in esecuzione. Ogni valore trasmesso come variabile di ambiente sostituisce il valore nel file `config.conf`. Per controllare le variabili di ambiente di un pod, accedi ad esso utilizzando il comando `kubectl exec` ed esegui `env`.

|Chiave|Opzioni valore|
|---|-------------|
|ACCESS_KEY_ID|L'**access_key_id** che fa parte delle credenziali HMAC in {{site.data.keyword.cos_full_notm}}.|
|SECRET_ACCESS_KEY|Il **secret_access_key** che fa parte delle credenziali HMAC in {{site.data.keyword.cos_full_notm}}.|
|ENDPOINT|Il nome host per accedere ai dati del bucket {{site.data.keyword.cos_full_notm}}.|
|BUCKET|Il nome del bucket in {{site.data.keyword.cos_full_notm}} in cui vengono archiviati i tuoi dati di backup.|
{: caption="Tabella 4. Variabili {{site.data.keyword.cos_full_notm}}" caption-side="top"}

|Chiave|Opzioni valore|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: predefinito. Il percorso file assoluto della directory su cui è montato il volume. Viene eseguito il backup dei dati da questa directory. Non selezionare la directory backup_restore poiché tale directory contiene i file per i processi di backup e ripristino.|
|BACKUP_NAME|*volume_backup*: predefinito. Scegli un nome di backup.|
|BACKUP_TYPE|*full*: predefinito. Viene eseguito ogni volta il backup di tutti i file.<br/> *incremental*: viene eseguito il backup solo dei file nuovi o modificati. Se scegli *incremental*, devi scegliere i valori per **SCHEDULING_INFO** e **SCHEDULING_TYPE**.|
|SCHEDULE_TYPE|*none*: predefinito. Crea un backup monouso.<br/> **Nota:** se scegli di creare un backup una tantum, il tuo pod viene rimosso dal cluster dopo che il backup è terminato. <br/> *periodic*: modifica il valore con periodic per creare backup pianificati.|
|SCHEDULE_INFO|*hourly*: crea un backup orario.<br/>*daily*: predefinito. Crea un backup giornaliero.<br/>*weekly*: Crea un backup settimanale. Devi includere questa variabile se stai pianificando un aggiornamento periodico.|
|EXCLUDE_DIRECTORIES|*none*: predefinito. Includi il percorso file assoluto delle directory che desideri escludere dal backup. Separa le directory con una virgola.|
{: caption="Tabella 5. Variabili di backup" caption-side="top"}

|Chiave|Opzioni valore|
|---|-------------|
|BACKUP_NAME|*volume_backup*: predefinito. Includi il nome del backup che verrà ripristinato da {{site.data.keyword.cos_full_notm}}.|
|RESTORE_DIRECTORY|*/backup*: predefinito. La directory assoluta su cui è montato il volume. I dati vengono ripristinati in questa directory. Non selezionare la directory `backup_restore` come la directory che contiene i file per i processi di ripristino e backup.|
{: caption="Tabella 6. Variabili di ripristino" caption-side="top"}

|Chiave|Opzioni valore|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: predefinito. Includi questa variabile di ambiente se modifichi il nome del file della chiave di crittografia o se la chiave è ubicata in una directory diversa da `backup_restore`.|
|ENCRYPTION_REQUIRED|*no*: predefinito.<br/> *yes*: se non codifichi il tuo backup, non includere le variabili di ambiente di crittografia. Se codifichi il tuo backup, includi questa chiave con il valore `yes`.|
|ENCRYPTION_PASSPHRASE|Includi una passphrase per proteggere un backup. Questa passphrase è diversa dalla frase che hai utilizzato quando hai creato la chiave di crittografia. Devi includere questa passphrase quando esegui il backup e il ripristino dei dati.|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: predefinito.<br/> *yes*: includi questa variabile di ambiente con `yes` se hai generato la chiave di crittografia direttamente nel contenitore. La maggior parte degli utenti genera la chiave sul proprio computer locale e la copia nel pod e può lasciare il valore predefinito di `no`.|
{: caption="Tabella 7. Variabili di crittografia" caption-side="top"}
