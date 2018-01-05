---

copyright:
  years: 2015, 2017
lastupdated: "2017-10-30"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}

# Introduzione all'immagine ibm-node-strong-pm
{: #getting_started_node_strong}

L'immagine {{site.data.keyword.IBM}} Node con StrongLoop \(**ibm-node-strong-pm**\) viene fornita per {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

## Come funziona 
{: #how_it_works}

Utilizza l'immagine **ibm-node-strong-pm** per creare un singolo contenitore che distribuisce un'istanza in esecuzione di [StrongLoop Process Manager](https://www.strongloop.com). Strongloop Process Manager ti consente di distribuire, gestire e monitorare le applicazioni Node.js nel cloud {{site.data.keyword.IBM_notm}} che sono state implementate in una macchina remota.
{:shortdesc}

Nella macchina remota deve essere installata un'istanza di StrongLoop Arc. StrongLoop Arc è un'interfaccia utente grafica per la piattaforma StrongLoop e include strumenti per lo sviluppo, la creazione di profili e il monitoraggio di applicazioni Node.js. Dopo aver creato un'applicazione con StrongLoop Arc, trasmettila e distribuiscila al contenitore in {{site.data.keyword.Bluemix_notm}} che esegue StrongLoop Process Manager. Per stabilire una connessione tra la macchina remota e il contenitore, devi eseguire il bind di un indirizzo IP pubblico a un contenitore e configurare tale indirizzo IP come il tuo server StrongLoop Process Manager in StrongLoop Arc. Quando la tua applicazione viene distribuita tramite StrongLoop Process Manager, puoi monitorare le metriche e le prestazioni della tua applicazione in StrongLoop Process Manager. Per aggiornare o ridimensionare la tua applicazione, devi modificarla utilizzando StrongLoop Arc e quindi trasmettere le tue modifiche al contenitore.

**Nota:** l'utilizzo dell'immagine **ibm-node-strong-pm** non è supportato con i gruppi di contenitori in {{site.data.keyword.Bluemix_notm}}.

## Elementi inclusi
{: #whats_included}

-   Tutti i pacchetti software inclusi nell'immagine **ibmnode:latest**. Vedi [Elementi inclusi](/docs/services/RegistryImages/ibmnode/index.html#getting_started_node) nell'argomento Introduzione all'immagine **ibmnode** per {{site.data.keyword.Bluemix_notm}}.
-   StrongLoop Process Manager

## Introduzione
{: #how_to_get_started}

Puoi utilizzare l'immagine **ibm-node-strong-pm** dal catalogo {{site.data.keyword.Bluemix_notm}} per provare un singolo contenitore in {{site.data.keyword.containershort_notm}}. L'immagine viene avviata con un'istanza in esecuzione di StrongLoop Process Manager, a cui puoi accedere appena il contenitore è disponibile. Utilizzando un'istanza di StrongLoop Arc connessa a tale Process Manager, puoi distribuire un'applicazione Node.js e accedere ad essa.
{:shortdesc}

**Nota:** prima di iniziare, esamina i termini di utilizzo per le immagini certificate da {{site.data.keyword.IBM_notm}} nella sezione Utilizzo della [libreria Docker websphere-liberty](https://github.com/docker-library/docs/tree/master/websphere-liberty).

1.  Dal catalogo, seleziona **Contenitori** e scegli l'immagine **ibm-node-strong-pm** da cui creare il tuo contenitore.
2.  Crea un singolo contenitore.
    -   Nel campo **Dimensione**, seleziona una dimensione del contenitore di almeno 1 GB di memoria \(**Small**\).
    -   Nel campo **Porte pubbliche**, immetti le porte 8701 e 3001. Queste porte sono automaticamente esposte quando il tuo contenitore viene distribuito in {{site.data.keyword.Bluemix_notm}} e sono un prerequisito per eseguire il bind di un indirizzo IP pubblico al contenitore. La porta 8701 viene utilizzata per accedere a StrongLoop Process Manager nel contenitore. La porta 3001 è la porta su cui è in ascolto l'applicazione dopo essere stata distribuita a StrongLoop Process Manager.

        **Nota:** per distribuire ulteriori applicazioni a StrongLoop Process Manager, esponi più porte. Le porte devono essere consecutive, a partire da 3002. Ad esempio 3002, 3003, 3004 e così via.

    -   Per ulteriori informazioni, vedi [Creazione di un singolo contenitore utilizzando il dashboard {{site.data.keyword.Bluemix_notm}}](/docs/containers/container_single_ui.html#gui).
3.  Esegui il bind di un indirizzo IP pubblico al contenitore. Per ulteriori informazioni, vedi [Richiesta ed esecuzione del bind di indirizzi IP](/docs/containers/container_single_ui.html#container_cli_ips).

## Configurazione di StrongLoop Arc su una macchina remota
{: #UsingSLA}

Dopo aver creato un singolo contenitore dall'immagine **ibm-node-strong-pm** in {{site.data.keyword.Bluemix_notm}}, configura StrongLoop Arc sulla tua macchina remota.
{:shortdesc}

Prima di iniziare, installa Node.js e il Node package manager \(npm\) sulla macchina remota.

1.  Scarica e installa StrongLoop immettendo il seguente comando. StrongLoop include tutti i pacchetti necessari per eseguire Arc.

    ```
    npm install -g strongloop
    ```
    {: pre}

2.  Una volta installato StrongLoop, esegui StrongLoop Arc.

    ```
    slc arc
    ```
    {: pre}

3.  Quando ti viene richiesto, esegui la registrazione o l'accesso a StrongLoop.
4.  Aggiungi StrongLoop Process Manager in esecuzione nel tuo contenitore all'elenco Process Manager in StrongLoop Arc.
    1.  Dal selettore di moduli Arc, passa a **Process Manager**.
    2.  Aggiungi l'indirizzo IP pubblico da associare al tuo contenitore e la porta pubblica 8701 al Process Manager Arc.
    3.  Fai clic su **Attiva**.

## Distribuzione di un'applicazione Node.js nel tuo contenitore utilizzando StrongLoop Arc
{: #pushing_app_to_container}

Dopo aver connesso StrongLoop Process Manager in esecuzione nel tuo contenitore con il client StrongLoop Arc sulla tua macchina remota, crea e distribuisci le tue applicazioni Node.js in {{site.data.keyword.Bluemix_notm}}.
{:shortdesc}

1.  Dal selettore di moduli Arc, passa a **Crea e distribuisci**.
2.  Crea la tua applicazione seguendo le istruzioni dell'interfaccia utente Arc.

    **Importante:** assicurati che l'applicazione Node.js che hai creato con StrongLoop Arc sia in ascolto sia sulla variabile di ambiente PORT sia sulla porta che hai esposto quando hai creato il contenitore.

    Esempio

    ```
    app.listen(process.env.PORT || 3001);
    ```
    {: pre}

3.  Distribuisci la tua applicazione seguendo le istruzioni dell'interfaccia utente Arc. Seleziona il Process Manager che hai configurato per distribuire la tua applicazione a {{site.data.keyword.Bluemix_notm}}.
4.  Attendi il completamento del processo di distribuzione.
5.  Accedi alla tua applicazione.
    1.  Apri un browser web.
    2.  Immetti l'indirizzo IP del tuo contenitore e la porta nel seguente formato <var class="keyword varname">IP_ADDRESS:PORT</var>.

        Esempio

        ```
        192.0.2.26:3001
        ```
        {: pre}

    3.  Ripeti questi passi per ogni applicazione che hai distribuito a {{site.data.keyword.Bluemix_notm}}.
6.  Utilizza la traccia, le metriche e il profiler di Arc per ottenere informazioni sulle prestazioni delle tue applicazioni mentre vengono eseguite in {{site.data.keyword.Bluemix_notm}}. Per ulteriori informazioni, vedi [Operating Node applications](https://docs.strongloop.com/display/SLC/Operating+Node+applications) nella documentazione di StrongLoop.

## Guida di riferimento al Dockerfile **ibm-node-strong-pm** 
{: #reference_dockerfile}

Questo Dockerfile è stato utilizzato per creare l'immagine **ibm-node-strong-pm** nel catalogo {{site.data.keyword.Bluemix_notm}}. Queste informazioni sono fornite solo come riferimento. Gli altri file inclusi nella versione creata di questa immagine non sono forniti.

```
FROM registry.ng.bluemix.net/ibmnode:latest
RUN useradd -ms /bin/bash strong-pm \    
    && chown -R strong-pm:strong-pm /usr/local \    
    && su strong-pm -c "npm install -g strong-pm && npm cache clear"

# Imposta una parvenza di un ambiente
WORKDIR /home/strong-pm
ENV HOME=/home/strong-pm PORT=3000

# Esegui come utente non privilegiato all'interno del contenitore
USER strong-pm

# Esponi le porte strong-pm e dell'applicazione
EXPOSE 8701
EXPOSE 3001-3010

ENTRYPOINT ["/usr/local/bin/sl-pm", "--base", ".", "--listen", "8701"]
```
{: screen}


