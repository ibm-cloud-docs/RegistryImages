---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-04"

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

# Introduzione all'immagine `ibmcloud-backup-restore`
{: #ibmbackup_restore_starter}

L'immagine `ibmcloud-backup-restore` contiene i pacchetti pre-installati necessari per il backup e il ripristino della memoria persistente in {{site.data.keyword.containerlong}}.
{:shortdesc}

L'immagine `ibmcloud-backup-restore` è disponibile solo nei domini di registro `icr.io` ed è considerata obsoleta nei domini `registry.bluemix.net`. Per accedere all'immagine e distribuire contenitori da essa, il tuo cluster deve archiviare una chiave API in un segreto di pull dell'immagine per autorizzare l'accesso a {{site.data.keyword.registryshort_notm}}. Per i cluster creati prima del 25 febbraio 2019, devi aggiornare il cluster per avere accesso ai domini di registro `icr.io` in modo da poter utilizzare l'immagine `ibmcloud-backup-restore`. Per ulteriori informazioni su cosa è stato modificato e come aggiornare il tuo cluster, vedi [Understanding how to authorize your cluster to pull images from a registry](/docs/containers?topic=containers-images#cluster_registry_auth) and [Aggiornamento dei cluster esistenti per utilizzare il segreto di pull dell'immagine della chiave API](/docs/containers?topic=containers-images#imagePullSecret_migrate_api_key). 
{: important}

Puoi accedere alle immagini fornite da {{site.data.keyword.IBM_notm}} utilizzando la riga di comando, consulta [Immagini pubbliche IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Come funziona
{: #backup_restore_how_it_works}

Con l'immagine `ibmcloud-backup-restore`, puoi creare un backup monouso o pianificato per i dati dell'applicazione che vengono archiviati in un volume persistente (PV) nel tuo cluster o ripristinarli da un PV. Per eseguire il backup e il ripristino dei dati, distribuisci un pod dall'immagine `ibmcloud-backup-restore`. In seguito, monta la PVC che associa il PV di cui vuoi eseguire il backup o che vuoi utilizzare per ripristinare i dati nel tuo pod.

**Dove vanno i miei dati? Come posso accedervi?**

I dati di cui esegui il backup vengono archiviati in un'istanza del servizio {{site.data.keyword.cos_full_notm}}. Per accedere al servizio, utilizza le tue credenziali del servizio {{site.data.keyword.cos_full_notm}} come variabili di ambiente nel pod `ibmcloud-backup-restore` o modifica il file `config.conf` nel pod in esecuzione.

**Posso ripristinare i dati di backup in un'applicazione o un PV differente?**

Sì, puoi ripristinare i tuoi dati salvati dall'istanza del servizio {{site.data.keyword.cos_full_notm}} in un PV nel tuo cluster. Per ripristinare i dati, devi creare un pod di ripristino dall'immagine `ibmcloud-backup-restore`. In seguito, monta la PVC che associa il PV che vuoi utilizzare nel tuo pod.  

## Elementi inclusi
{: #backup_restore_whats_included}

Ogni immagine `ibmcloud-backup-restore` contiene i seguenti pacchetti software:

- Alpine 3.7
- Duplicity 0.7.10
- Pacchetti Python e GnuPG Made Easy (GPGME)

## Configurazione di un'istanza del servizio {{site.data.keyword.cos_full_notm}}
{: #backup_restore_setup_object_storage}

Crea e configura un'istanza del servizio {{site.data.keyword.cos_full_notm}} da utilizzare come repository per i dati di cui vuoi eseguire il backup.
{: shortdesc}

1. Distribuisci un'istanza del servizio {{site.data.keyword.cos_full_notm}}.
   1. Apri il [catalogo {{site.data.keyword.cloud_notm}} ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://cloud.ibm.com/catalog/services/cloud-object-storage).
   2. Immetti un nome per la tua istanza del servizio, come `cos-backup` e seleziona **predefinito** per il tuo gruppo di risorse.
   3. Controlla le [opzioni del piano ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://www.ibm.com/cloud-computing/bluemix/pricing-object-storage#s3api) per informazioni sui prezzi e seleziona un piano.
   4. Fai clic su **Crea**.
2. Controlla le credenziali dell'istanza del servizio {{site.data.keyword.cos_full_notm}}.
   1. Nella navigazione nella pagina dei dettagli del servizio, fai clic su **Credenziali del servizio**.
   2. Fai clic su **Nuova credenziale**. Viene visualizzata una casella di dialogo.
   3. Immetti un nome per le tue credenziali.
   4. Nel campo **Aggiungi parametri di configurazione inline (facoltativo)**, immetti `{"HMAC":true}` per creare ulteriori credenziali HMAC che il pod `ibmcloud-backup-restore` utilizza per l'autenticazione con il servizio {{site.data.keyword.cos_full_notm}}.
   5. Fai clic su **Aggiungi**. Le tue nuove credenziali sono elencate nella tabella **Credenziali del servizio**.
   6. Fai clic su **Visualizza credenziali**.
   7. Prendi nota di **access_key_id** e **secret_access_key** che puoi trovare nella sezione **cos_hmac_keys**.
3. Crea il tuo primo bucket {{site.data.keyword.cos_full_notm}}.
   1. Nella navigazione nella pagina dei dettagli del servizio, fai clic su **Bucket**.
   2. Fai clic su **Crea bucket**. Viene visualizzata una casella di dialogo.
   3. Immetti un nome univoco per il tuo bucket. Il nome deve essere univoco all'interno di {{site.data.keyword.cos_full_notm}} in tutte le regioni e in tutti gli account {{site.data.keyword.cloud_notm}}.
   4. Dall'elenco **Resilienza**, seleziona il livello di disponibilità che desideri per i tuoi dati. Per ulteriori informazioni, vedi [Endpoint e regioni {{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-endpoints#endpoints).
   5. Modifica l'**Ubicazione** con la regione in cui vuoi archiviare i tuoi dati. Tieni presente che potrebbe non essere consentito archiviare i tuoi dati in tutte le regioni per motivi legali.  
   6. Fai clic su **Crea**.
4. Controlla il nome host {{site.data.keyword.cos_full_notm}} del tuo bucket.
   1. Fai clic sul nome del bucket che hai creato nel passo precedente.
   2. Nella navigazione nella pagina dei dettagli del servizio, fai clic su **Bucket** > **Configurazione**.
   3. Prendi nota dell'URL pubblico che puoi utilizzare per accedere ai dati nel tuo bucket.

Controlla la [documentazione {{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage?topic=cloud-object-storage-about#about) per ulteriori informazioni sulla configurazione della tua istanza del servizio.

## Backup dei dati da un volume persistente
{: #backup_restore_scheduled_backup}

Puoi creare un backup pianificato o monouso per ogni volume persistente (PV) che viene montato nel tuo pod dell'applicazione tramite un'attestazione del volume persistente (PVC).  
{: shortdesc}

Il seguente esempio mostra come distribuire un pod di backup dall'immagine `ibmcloud-backup-restore`, montare un PV esistente nel pod di backup utilizzando una PVC ed eseguire il backup dei dati dal PV alla tua istanza del servizio {{site.data.keyword.cos_full_notm}}.  

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

2. Crea un pod di backup dall'immagine `ibmcloud-backup-restore`. Per accedere ai dati nel PV, devi montare la PVC che associa il PV al tuo pod di backup. Il seguente esempio crea un pod di backup che esegue un backup incrementale giornaliero. Per creare un backup con impostazioni differenti, rivedi l'elenco completo di [opzioni di variabili di ambiente](#backup_restore_env_reference).

   L'immagine `ibmcloud-backup-restore` deve essere distribuita in un solo pod e non può essere utilizzata come parte di una distribuzione Kubernetes.
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
     - image: icr.io/ibm/ibmcloud-backup-restore
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
       - mountPath: <mount_path1>
         name: <pvc_name1>
       - mountPath: <mount_path1>
         name: <pvc_name2>
     volumes:
     - name: pvc_name1
       persistentVolumeClaim:
         claimName: <pvc_name1>
     - name: pvc_name2
       persistentVolumeClaim:
         claimName: <pvc_name2> 
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
     <td><code>&lt;mount_path&gt;</code></td>
     <td>Il percorso di montaggio del volume all'interno del contenitore di backup che monta la PVC di cui desideri eseguire il backup. Se desideri eseguire il backup di più PVC, specifica un percorso di montaggio separato per ogni PVC. Assicurati che il nome che utilizzi in <code>volumeMount.name</code> corrisponda al nome del volume che specifichi nella sezione <code>volumes.name</code> del tuo file YAML. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>Il nome della PVC di cui desideri eseguire il backup. Se desideri eseguire il backup di più PVC, specifica il nome del volume e la PVC corrispondente per ogni PVC di cui desideri eseguire il backup. Assicurati che il nome che utilizzi in <code>volumes.name</code> corrisponda al nome nella sezione <code>volumeMount.name</code> del tuo file YAML.</td>
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

6. Controlla il backup in {{site.data.keyword.cos_full_notm}} nella GUI {{site.data.keyword.cloud_notm}}.
    1. Dal dashboard {{site.data.keyword.cloud_notm}}, trova l'istanza del servizio {{site.data.keyword.cos_full_notm}}.
    2. Dalla navigazione, seleziona **Bucket** e fai clic sul bucket che hai utilizzato nella tua configurazione del backup. Il tuo backup viene visualizzato come un oggetto nel tuo bucket.
    3. Rivedi i file compressi. Puoi scaricare il file `vol1.difftar.gz`, estrarlo e verificare i dati del backup.

       Se elimini o modifichi eventuali file da {{site.data.keyword.cos_full_notm}}, questi file non possono essere ripristinati.
       {: important}

Il tuo backup è disponibile. Se hai configurato il tuo backup per creare un backup completo monouso, devi eseguire lo script di backup ogni volta che vuoi creare un nuovo backup. Se hai configurato il contenitore per eseguire un backup incrementale periodico, i tuoi backup vengono eseguiti come pianificato.

## Ripristino dei dati da {{site.data.keyword.cos_full_notm}} al tuo cluster
{: #backup_restore_restore_script_cli}

Puoi ripristinare i dati dalla tua istanza del servizio {{site.data.keyword.cos_full_notm}} in un PV nel tuo cluster.

Se hai più backup completi nella tua istanza del servizio {{site.data.keyword.cos_full_notm}}, la PVC viene ripristinata con i dati dell'ultimo backup completo. Se hai dei backup incrementali, la PVC viene ripristinata con i dati dell'ultimo backup completo, inclusi tutti i backup incrementali fino al giorno in cui avvii il ripristino.

**Prima di iniziare**

- [Indirizza la tua CLI al tuo cluster](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure).
- [Crea un backup per un PV nel tuo cluster](#backup_restore_scheduled_backup).

Per ripristinare i dati da {{site.data.keyword.cos_full_notm}} a un PV, completa la seguente procedura:

1. Ottieni il nome della PVC che associa il PV in cui vuoi ripristinare i tuoi dati.

   ```
   kubectl get pvc
   ```
   {: pre}

2. Crea un pod di ripristino dall'immagine `ibmcloud-backup-restore`. Per ripristinare i dati in un PV, devi montare la PVC che associa il PV al tuo pod di ripristino.
   {: codeblock}

    ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: restorepod
   spec:
     containers:
     - image: icr.io/ibm/ibmcloud-backup-restore
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
       - mountPath: <mount_path1>
         name: <pvc_name1>
       - mountPath: <mount_path1>
         name: <pvc_name2>
     volumes:
     - name: pvc_name1
       persistentVolumeClaim:
         claimName: <pvc_name1>  
     - name: pvc_name2
       persistentVolumeClaim:
         claimName: <pvc_name2> 
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
     <td><code>&lt;mount_path&gt;</code></td>
     <td>Il percorso di montaggio del volume all'interno del contenitore di ripristino che monta la PVC dove desideri ripristinare i tuoi dati. Se desideri ripristinare i dati su più PVC, specifica un percorso di montaggio separato per ogni PVC. Assicurati che il nome che utilizzi in <code>volumeMount.name</code> corrisponda al nome del volume che specifichi nella sezione <code>volumes.name</code> del tuo file YAML. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>Il nome della PVC dove desideri ripristinare i tuoi dati. Per ripristinare i dati su più PVC, aggiungi una voce <code>volumes</code> per ogni PVC. Assicurati che il nome che utilizzi in <code>volumes.name</code> corrisponda al nome nella sezione <code>volumeMount.name</code> del tuo file YAML.</td>
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
5. Verifica che i tuoi dati siano stati ripristinati correttamente.

    ```
    kubectl logs restorepod
    ```
    {: pre}

6. Rimuovi il pod per evitare che utilizzi ulteriori risorse.

    ```
    kubectl delete -f restorepod.yaml
    ```
    {: pre}

Il backup è stato ripristinato correttamente. Puoi ora montare la PVC che associa il PV in tutti gli altri pod nel tuo cluster per accedere ai file ripristinati. Se i dati del contenitore di cui è stato eseguito il backup includevano un utente non root, devi aggiungere le autorizzazioni non root al tuo nuovo contenitore. Per ulteriori informazioni, consulta [Aggiunta di accesso utente non root ai volumi](/docs/containers?topic=containers-cs_troubleshoot_storage#cs_storage_nonroot).


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
|BACKUP_DIRECTORY|`/backup`. Predefinito. Il percorso file assoluto della directory su cui è montato il volume. Viene eseguito il backup dei dati da questa directory. Non selezionare la directory `backup_restore` poiché tale directory contiene i file per i processi di backup e ripristino.|
|BACKUP_NAME|`volume_backup`. Predefinito. Scegli un nome di backup.|
|BACKUP_TYPE|`full`. Predefinito. Viene eseguito ogni volta il backup di tutti i file.<br/> `incremental`. Viene eseguito il backup solo dei file nuovi o modificati. Se scegli `incremental`, devi scegliere i valori per **SCHEDULING_INFO** e **SCHEDULING_TYPE**.|
|SCHEDULE_TYPE|`none`. Predefinito. Crea un backup monouso.<br/> **Nota:** se scegli di creare un backup una tantum, il tuo pod viene rimosso dal cluster dopo che il backup è terminato.<br/> `periodic`. Modifica il valore con periodic per creare backup pianificati.|
|SCHEDULE_INFO|`hourly`. Crea un backup orario.<br/>`daily`. Predefinito. Crea un backup giornaliero.<br/>`weekly`. Crea un backup settimanale. Devi includere questa variabile se stai pianificando un aggiornamento periodico.|
|EXCLUDE_DIRECTORIES|`none`. Predefinito. Includi il percorso file assoluto delle directory che desideri escludere dal backup. Separa le directory con una virgola.|
{: caption="Tabella 5. Variabili di backup" caption-side="top"}

|Chiave|Opzioni valore|
|---|-------------|
|BACKUP_NAME|`volume_backup`. Predefinito. Includi il nome del backup che verrà ripristinato da {{site.data.keyword.cos_full_notm}}.|
|RESTORE_DIRECTORY|`/backup`. Predefinito. La directory assoluta su cui è montato il volume. I dati vengono ripristinati in questa directory. Non selezionare la directory `backup_restore` poiché tale directory contiene i file per i processi di backup e ripristino.|
{: caption="Tabella 6. Variabili di ripristino" caption-side="top"}

