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


# Introduzione all'immagine **ibm-websphere-extreme-scale** 
{: #ibm-websphere-extreme-scale}

Le immagini **ibm-websphere-extreme-scale** vengono fornite per {{site.data.keyword.containerlong}}.

## Come funziona 
{: #how_it_works}

L'immagine IBM WebSphere&reg; eXtreme Scale è una funzione distribuita della cache in memoria eseguita all'interno dell'ambiente di runtime Liberty. Fornisce server di cache Liberty, strumenti di gestione della cache, una console di gestione e i servizi di amministrazione REST predefiniti basati  sugli standard e sulle specifiche del settore più recenti. Questa immagine si basa sulla tecnologia core di IBM WebSphere eXtreme Scale. La tecnologia di caching caricata all'interno di questa immagine supporta pienamente le specifiche e la conformità di JCache \(JSR107\).

Quando utilizzi l'immagine `ibm-websphere-extreme-scale`, sono disponibili i seguenti elementi di rilievo principali:

-   Una soluzione di cache distribuita semplice da distribuire e da configurare basata sulla tecnologia eXtreme Scale 
-   API REST e console di gestione semplici e facili da utilizzare
-   Completa personalizzazione per supportare tutti i casi di utilizzo della cache di eXtreme Scale 
-   Immagine facilmente configurabile che si integra nell'ambiente di cache in pochi minuti
-   Conformità JCache per supportare la specifica della cache JSR107


## Elementi inclusi 
{: #whats_included}

Ogni immagine **ibm-websphere-extreme-scale** fornisce i seguenti pacchetti software.

-   Immagine di base Ubuntu 16.04, 64 bit
-   IBM WebSphere eXtreme Scale
-   IBM Java Runtime Environment 8.0

Le specifiche funzioni che vengono installate nell'immagine dipendono dalla tag che hai selezionato. La seguente tabella mostra quale livello di codice è incluso in ciascuna immagine **ibm-websphere-extreme-scale**. Per ulteriori informazioni su queste funzioni, consulta la [documentazione di eXtreme Scale](http://www.ibm.com/support/knowledgecenter/SSTVLU).

|Tag|Descrizione|
|---|-----------|
|**ibm-websphere-extreme-scale:latest**|Utilizza questa tag per specificare l'immagine più recente di IBM WebSphere eXtreme Scale.|
|**ibm-websphere-extreme-scale:8.6.1.x**|Utilizza questa tag per specificare uno specifico livello di release di WebSphere eXtreme Scale.|
{: caption="Tabella 1. Immagini IBM WebSphere eXtreme Scale" caption-side="top"}

## Restrizioni di utilizzo 
{: #usage}

La seguente tabella mostra le restrizioni che si applicano all'utilizzo gratuito dell'immagine **ibm-websphere-extreme-scale** in {{site.data.keyword.Bluemix_notm}}.

|Ambiente|Restrizioni di utilizzo|
|-----------|------------------|
|Sviluppo|L'immagine `ibm-websphere-extreme-scale`  consente un utilizzo gratuito illimitato per lo sviluppo.|
|Produzione|L'utilizzo in produzione dell'immagine **ibm-websphere-extreme-scale** è limitato agli utenti del prodotto IBM WebSphere eXtreme Scale con licenza. Per ulteriori informazioni sull'utilizzo in produzione, vedi [View Pricing and Buy](https://www-112.ibm.com/software/howtobuy/buyingtools/paexpress/Express?P0=E1&part_number=D56YTLL,D56YVLL,D572PLL,D572MLL,D0594LL,D059GLL&catalogLocale=en_US&Locale=en_US&country=USA&PT=jsp&CC=USA&VP=&TACTICS=&S_TACT=&S_CMP=&brand=SB05).|
{: caption="Tabella 2. Restrizioni di utilizzo per le immagini ibm-websphere-extreme-scale in {{site.data.keyword.Bluemix_notm}}" caption-side="top"}

**Nota:** il prezzo dell'immagine **ibm-websphere-extreme-scale** è indipendente dal prezzo dei contenitori che utilizzi in {{site.data.keyword.Bluemix_notm}}.

Rivedi i termini di utilizzo per le immagini certificate da IBM nella sezione Licenze dei [documenti di informazioni sulle licenze](http://www-03.ibm.com/software/sla/sladb.nsf/displaylis/A44EE6829150B2998525801700516718?OpenDocument).


## Introduzione 
{: #get_started}

Per distribuire l'immagine **ibm-websphere-extreme-scale** puoi utilizzare la console o la riga di comando.

Questa procedura include le seguenti opzioni per la distribuzione dell'immagine con la console o con la riga di comando:

-   Seleziona l'immagine dalla [console](#bmconsole) {{site.data.keyword.Bluemix_notm}} nel **Catalogo**.
-   Utilizza il comando cf ic per avviare l'immagine con la [riga di comando](#bmcommand) CF.

**Importante:** prima di iniziare, esamina le restrizioni di utilizzo come descritto nella sezione precedente per le immagini **ibm-websphere-extreme-scale**.


### Distribuisci dalla console {{site.data.keyword.Bluemix_notm}}.
{: #bmconsole}

   1.  Dal catalogo, seleziona **Contenitori** e scegli l'immagine **ibm-websphere-extreme-scale** per iniziare a creare il tuo contenitore.
   2.  Seleziona la versione dell'immagine **ibm-websphere-extreme-scale** che desideri utilizzare dal menu a discesa **TAG/Versione**.
   3.  Crea un singolo contenitore.

        **Nota:** se vuoi creare più di un contenitore, non utilizzare l'opzione **Scalabile** per creare due o più gruppi di membri della cache di eXtreme Scale. Seleziona invece l'opzione **Singolo** per creare più contenitori e utilizza l'IU del dashboard eXtreme Scale per unire i membri di cache.

        Per ulteriori informazioni, vedi [Creazione di un singolo contenitore utilizzando il dashboard {{site.data.keyword.Bluemix_notm}}](/docs/containers/container_single_ui.html#gui).

        1.  Dal pannello Crea un contenitore, specifica un **Nome contenitore**; ad esempio, `wxs1`. Nel nome contenitore non sono consentiti spazi altri caratteri non stampabili.
        2.  Per **Dimensione**, specifica la dimensione dei contenitori utilizzati per avviare l'immagine ibm-eXtreme-scale.
        3.  Per **Indirizzo IP pubblico**, specifica Richiedi ed esegui il bind dell'IP pubblico.
        4.  Apri il pannello Opzioni avanzate. Crea un volume in modo che i dati di configurazione di eXtreme Scale possano essere conservati durante l'installazione di un aggiornamento progressivo in un secondo momento; ad esempio:
            1. Fai clic su **Crea un volume** da Opzioni avanzate e specifica il **Nome volume**. Immetti, ad esempio, vol-wxs1.
            2. Fai clic su **Aggiungi una nuova variabile di ambiente**. L'immagine **ibm-websphere-extreme-scale** richiede le variabili di ambiente memorizzate nel contenitore e accessibili da utenti autorizzati.
            <table>
            <caption>Tabella 3. Variabili di ambiente per l'immagine ibm-websphere-extreme-scale</caption>
               <tr>
                 <th>Nome Variabile</th>
                 <th>Valore da specificare </th>
               </tr>
               <tr>
                 <td>SECRET_KEY</td>
                 <td>Il valore della chiave segreta; ad esempio, <var class="keyword varname">s3cretKey!</var>. Questa è la chiave segreta utilizzata dai server eXtreme Scale per inviare comunicazioni interne tra più membri in un gruppo di membri della cache. Per ulteriori informazioni, vedi la [sezione sui gruppi di membri della cache](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txscreategridtut1.html).</td>
               </tr>
               <tr>
                 <td>XSADMIN_PWD</td>
                 <td>La password dell'utente xsadmin che accede alla IU del dashboard eXtremeScale; ad esempio, <var class="keyword varname">xsadmin4Me!</var>.</td>
               </tr>
               </table>
  
              **Nota:** puoi modificare la password e la chiave segreta dopo l'avvio del contenitore eXtreme Scale. Sia la chiave segreta che la password hanno le seguenti regole della password. Assicurati di immetterle entrambe in base alla seguente regola:

              La password e la chiave segreta devono contenere almeno un numero [`0-9`], un carattere maiuscolo [`A-Z`], un carattere speciale [`~!@#$%^&*()_-+=:;'[]{}|<>,.?/`]  e contenere almeno 10 caratteri.

              Per ulteriori informazioni su come modificare la password, consulta la [documentazione di amministrazione](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txsadministerxsld.html).

              La porta viene aggiunta nel campo **Porta pubblica** per impostazione predefinita.

   4.  Fai clic su **Crea** per creare un contenitore dal dashboard {{site.data.keyword.Bluemix_notm}}.
    
   5.  Una volta che i server contenitore e di eXtreme Scale sono completamente avviati, accedi all'IU del dashboard eXtreme Scale e verifica la distribuzione. Dalla console {{site.data.keyword.Bluemix_notm}}, fai clic su un contenitore per visualizzare l'IP pubblico. È possibile accedere al contenitore WebSphere eXtreme Scale dal seguente URL:

    ```
    https://<Public_IP>:9443
    ```
    {: pre}

   **Nota:** l'avvio completo del server può richiedere da 5 a 10 minuti.


### Distribuisci l'immagine dalla riga di comando.
{: #bmcommand}
 1.  Crea un volume Docker per memorizzare i dati persistenti di IBM WebSphere eXtreme Scale. Ad esempio, immetti il seguente comando:

  ```
  cf ic volume create <vol-wxs1>
  ```
  {: pre}

 2.  Immetti il seguente comando per avviare il tuo contenitore IBM WebSphere eXtreme Scale in {{site.data.keyword.Bluemix_notm}}:

    **Nota:** sostituisci yourSecretKey e yourXsadminPassword con la tua password in base alla regola della password precedentemente menzionata.

   ```
   cf ic run -itdP --volume <vol-wxs1:/vol> --name <wxs1> -m 1024 -e SECRET_KEY=<yourSecretKey> -e XSADMIN_PWD=<yourXsadminPassword> registry.ng.bluemix.net/ibm-websphere-extreme-scale:latest
   ```
   {: pre}
        
     **Nota:** il nome del contenitore deve contenere solo caratteri alfanumerici e iniziare con una lettera. In alternativa, puoi impostare <code>-e XSLD_CONTAINER_ALIAS=<wxs1></code> se richiedi che il nome del contenitore contenga altri caratteri non alfanumerici.

 3.  Richiedi un IP pubblico da utilizzare per il tuo primo contenitore IBM WebSphere eXtreme Scale. Immetti il seguente comando per scrivere l'indirizzo IP:

   ```
   cf ic ip request
   ```
   {: pre}

 4.  Immetti il seguente comando per eseguire il bind del tuo contenitore WebSphere eXtreme Scale all'indirizzo IP pubblico che hai richiesto:

   ```
   cf ic ip bind <REQUESTED_IP> wxs1
   ```
   {: pre}

 5.  Immetti il seguente comando per verificare che lo stato del contenitore `wxs1` sia In esecuzione:

   ```
   cf ic ps -a
   ```
   {: pre}

 6.  Immetti il seguente comando per stabilire una connessione al contenitore `wxs1` e monitorare il log nanny:

   ```
   cf ic exec -it <wxs1> tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
        ```
   {: pre}

    **Nota:** l'avvio del server può richiedere da 5 a 10 minuti.

 7.  Una volta che i server contenitore e di eXtreme Scale sono completamente avviati, accedi all'IU del dashboard eXtreme Scale e verifica la distribuzione: 

   ```
   https://<REQUESTED_IP>:9443
   ```
   {: pre}


## Aggiunta della capacità di memorizzazione nella cache 
{: #caching}

1.  Distribuisci un'altra immagine dalla console o dalla riga di comando seguendo gli stessi passi di prima.
2.  Una volta che i server contenitore e di eXtreme Scale sono completamente avviati, accedi all'IU del dashboard eXtreme Scale di questo membro:

    ```
    https://<REQUESTED_IP>:9443
    ```
    {: pre}

3.  Apri la pagina Membri di Data Cache e sotto la colonna **Azione** di questo membro seleziona **Unisci**.
4.  Immetti l'IP pubblico richiesto e la chiave segreta del primo membro e un nome univoco per questo membro nel gruppo di cache.
5.  Fai clic su **Unisci** e attendi il completamento dell'attività. Una volta completata l'attività, tutti i membri vengono visualizzati nella pagina Membri di Data Cache.

## Esecuzione di un aggiornamento progressivo 
{: #rolling_upgrade}

I seguenti passi sono necessari per eseguire un aggiornamento progressivo dei contenitori che eseguono `ibm-websphere-extreme-scale` in {{site.data.keyword.containershort_notm}}.

**Nota:** se il tuo gruppo di membri della cache di eXtreme Scale ha un solo membro, rimuovi questo contenitore {{site.data.keyword.IBM_notm}} e crealo nuovamente da zero. Tuttavia, se tale gruppo ha più di un membro e più server di catalogo distribuiti e desideri conservare i dati memorizzati nella cache in {{site.data.keyword.containershort_notm}}, completa la seguente procedura di aggiornamento progressivo per evitare la perdita dei dati della cache. Aggiorna per primi i membri della cache che hanno solo i server contenitore e quindi aggiorna i membri della cache che dispongono anche di server di catalogo.

1.  Avvia un nuovo contenitore chiamato wxsmigrate e utilizzalo per aggiornare tutti i contenitori eXtreme Scale esistenti. Per ulteriori informazioni, vedi la [sezione Introduzione](#get_started) in questa pagina. Gli ultimi file .zip di aggiornamento di eXtreme Scale verranno distribuiti nella cartella /tmp sul tuo nuovo contenitore wxsmigrate.
2.  Copia i file .zip aggiornati di eXtreme Scale dal contenitore wxsmigrate al tuo sistema locale, in cui è in esecuzione il plug-in della CLI {{site.data.keyword.containershort_notm}}:

    ```
    cf ic cp wxsmigrate:/tmp/xsld-bin-update.zip
    ```
    {: pre}
    
    ```
    cf ic cp wxsmigrate:/tmp/xsld-derby-update.zip
    ```
    {: pre}

3.  Ora, per ogni membro della cache all'interno di un gruppo di membri della cache completa la seguente procedura, un contenitore alla volta:

    1. Copia i file .zip aggiornati di eXtreme Scale dal tuo sistema locale al contenitore eXtreme Scale che vuoi aggiornare:
    
      ```
      cf ic cp  xsld-bin-update.zip  <container_name>:/tmp/xsld-bin-update.zip
      ```
      {: pre}
  
      ```
      cf ic cp  xsld-derby-update.zip  <container_name>:/tmp/xsld-derby-update.zip
      ```
      {: pre}
     
    2. Connettiti al contenitore che vuoi aggiornare: 
    
      ```
      cf ic exec -it <container_name> bash
      ```
      {: pre}

    3. Arresta i server XSLD sul contenitore pronto per l'aggiornamento.
      
      ```
      /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
      ```
      {: pre}
      
      Attendi che i server siano arrestati.
      
    4. Decomprimi l'ultimo codice di aggiornamento di eXtreme Scale all'interno del contenitore:
      
      ```
      unzip  -o  /tmp/xsld-bin-update.zip   -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
      
      ```
      unzip  -o  /tmp/xsld-derby-update.zip -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
    
    5. Avvia di nuovo il server XSLD:
    
      ```
      /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
      ```
      {: pre}
      
    6. Monitora il log nanny per assicurarti che i server siano avviati:
    
      ```
      tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
      ```
      {: pre}
      
4. Al termine dell'aggiornamento su tutti i contenitori IBM, rimuovi il contenitore wxsmigrate:

 ```
 cf ic rm -f <wxsmigrate>
 ```
 {: pre}

## Risoluzione dei problemi 
{: #troubleshoot}

###Risoluzione dei problemi relativi alla connettività dei membri della cache quando l'associazione di host viene persa 
{: #troubleshoot_cache}

Completa la seguente procedura se i membri di un gruppo di cache perdono la connettività a causa della perdita di un'associazione di host tra i contenitori.

1.  Connettiti a ciascun contenitore:

    ```
    cf ic exec -it <container_name> bash
    ```
    {: pre}

2.  Raccogli l'IP privato, il nome host e l'alias host per ciascun membro.

    L'alias host è il valore della variabile di ambiente XSLD_CONTAINER_ALIAS:

    ```
    echo ${<XSLD_CONTAINER_ALIAS>}
    ```
    {: pre}

    L'IP privato è l'indirizzo IP restituito dall'esecuzione del comando hostname -I nel contenitore:

    ```
    hostname -I
    ```
    {: pre}

    Il nome host è il nome host restituito dall'esecuzione del comando hostname nel contenitore.


3.  Esegui lo script recoverXSLD.sh su ciascun membro passando tutti gli altri alias membro, IP e nomi host:

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/recoverXSLD.sh <member2_alias> <member2_private_ip> <member2_hostname> ... <memberN_alias> <memberN_private_ip> <memberN_hostname>
    ```
    {: pre}

4.  Interrompi l'esecuzione dei server eseguendo stopXSLD.sh su ciascun contenitore nel gruppo di cache. Esegui stopXSLD.sh:

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
    ```
    {: pre}

5.  Se stopXSLD.sh non avanza durante il tentativo di arrestare il server di catalogo, attieniti alla seguente procedura:
    1.  Elenca il processo del server di catalogo:

        ```
        ps -ef | grep cs-default
        ```
        {: pre}

    2.  Termina il processo del server di catalogo utilizzando l'ID processo:

        ```
        kill -9 <catalog_server_process_id>
        ```
        {: pre}

    3.  Esegui di nuovo stopXSLD.sh:

        ```
        /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
        ```
        {: pre}

6.  Avvia XSLD su tutti i contenitori contemporaneamente. Esegui startXSLD.sh:

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
    ```
    {: pre}


### Risoluzione dei problemi relativi a un'attività di replica Derby arrestata 
{: #troubleshoot_derby}

Se un'attività di replica Derby non avanza oltre il 45%  per più di dieci minuti, è probabile che la causa sia un problema di rete. Completa la seguente procedura per risolvere il problema:

1.  Utilizza un programma di utilità di POST REST, come cURL, per annullare l'attività di replica Derby.

    ```
    curl -X PUT --header "Content-Type: application/json" --header "Accept: application/json" "https://<PUBLIC_IP>:9445/wxsadmin/v1/task/cancel/<DERBY_REPLICATION_TASK_ID>/force"
    ```
    {: pre}

    **Nota:** il <PUBLIC_IP> è quello che è stato utilizzato nel comando per avviare l'attività di replica Derby e <DERBY_REPLICATION_TASK_ID> è l'output dell'ID attività da tale comando.

2.  Esegui di nuovo l'attività di replica Derby.

## Accesso all'API REST 
{: #api}

Quando eXtreme Scale viene distribuito in {{site.data.keyword.containershort_notm}}, completa la seguente procedura per accedere all'API REST:

1.  Recupera il nome host del contenitore che intendi utilizzare per connettersi all'API REST.
    1.  Connettiti al contenitore:

        ```
        cf ic exec -it <container_name> bash
        ```
        {: pre}

    2.  Elenca il nome host:

        ```
        hostname
        ```
        {: pre}

2.  Recupera l'IP pubblico dello stesso contenitore.

    Se il contenitore non ha un IP pubblico, segui la procedura nella sezione Introduzione per fornire un IP pubblico al contenitore utilizzando la [console](#bmconsole) {{site.data.keyword.Bluemix_notm}} o la [riga di comando](#bmcommand).

3.  Crea una voce nel file host del tuo computer client locale che associ l'IP pubblico e il nome host del contenitore. La voce è simile alla seguente:

    ```
    169.44.8.87 instance-05106102
    ```
    {: pre}

    I tipici nomi di percorso completi per i file host sono:

    -   Per UNIX: /etc/hosts
    -   Per Windows: C:\\Windows\\System32\\drivers\\etc\\hosts
4.  Accedi alle API REST Swagger in un browser.
    -   Per le operazioni CRUD:

        ```
        https://<host_name>:9444/ibm/api/explorer
        ```
        {: pre}

    -   Per le operazioni di gestione:

        ```
        https://<host_name>:9445/ibm/api/explorer
        ```
        {: pre}


Per esempi ed aggiornamenti recenti, visita [WebSphere eXtreme Scale in GitHub](https://github.com/ibmWebsphereExtremeScale). Inoltre, consulta la seguente documentazione e i video di supporto per ulteriori informazioni sulla distribuzione di eXtreme Scale:

-   [Documentazione di IBM WebSphere eXtreme Scale](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/cxsgetstartxsld.html)
-   [Forum XS developerWorks](https://www.ibm.com/developerworks/community/forums/html/forum?id=11111111-0000-0000-0000-000000000778&ps=25)
-   [Introduzione a WebSphere eXtreme Scale V8.6.x](https://www.youtube.com/watch?v=xw7Ck3c40rA)
-   [Aggiunta di cache con WebSphere eXtreme Scale - Liberty Deployment](https://www.youtube.com/watch?v=I1g88bnstn8)
-   [Configurazione di un client WAS per lavorare con una griglia di DynaCache Liberty Deployment di WebSphere eXtreme Scale](https://www.youtube.com/watch?v=RvhcknNX-4Q)
-   [Configurazione di un client WAS per lavorare con una griglia Session Cache Liberty Deployment di WebSphere eXtreme Scale](https://www.youtube.com/watch?v=GKTy9I66Klc)

