---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-24"

keywords: IBM Cloud Container Registry, IBM Liberty, ibmliberty, container image, IBM WebSphere Application Server Liberty, liberty, public image

subcollection: RegistryImages

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:download: .download}

# Introduzione all'immagine `ibm/liberty`
{: #ibmliberty}

Le immagini di IBM® WebSphere® Application Server Liberty \(`ibm/liberty`\) vengono fornite per {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

Puoi accedere alle immagini fornite da {{site.data.keyword.IBM_notm}} utilizzando la riga di comando, consulta [Immagini pubbliche IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Come funziona
{: #ibmliberty_how_it_works}

Puoi utilizzare un'immagine `ibm/liberty` come immagine principale per creare la tua propria immagine e distribuire le tue applicazioni WAR, EAR o OSGi basate su Java in un contenitore IBM WebSphere Application Server Liberty.
{:shortdesc}

## Elementi inclusi
{: #ibmliberty_whats_included}

Ogni immagine Liberty fornisce i seguenti pacchetti software.
{:shortdesc}

- IBM WebSphere Application Server for Developers Liberty
- IBM Java Runtime Environment 8.0

Le specifiche funzioni di Liberty che vengono installate nell'immagine dipendono dalla tag che hai selezionato. La seguente tabella mostra le funzioni che sono incluse in ciascuna delle immagini `ibm/liberty`. Per ulteriori informazioni su ogni funzione, vedi la [panoramica delle funzioni Liberty in IBM Knowledge Center ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSAW57_liberty/com.ibm.websphere.wlp.nd.multiplatform.doc/ae/rwlp_feat.html).

|Tag|Descrizione|
|---|-----------|
|Tutte le immagini `ibm/liberty`|Tutte le immagini `ibm/liberty` includono le seguenti funzioni. <ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|`ibm/liberty:latest`|Questa immagine punta all'immagine `ibm/liberty:javaee8`.|
|`ibm/liberty:microProfile1`|Questa immagine contiene le funzioni che forniscono le capacità specificate da [MicroProfile 1.x ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://microprofile.io/).|
|`ibm/liberty:microProfile2`|Questa immagine contiene le funzioni che forniscono le capacità specificate da [MicroProfile 2.x ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://microprofile.io/).|
|`ibm/liberty:springBoot1`|Questa immagine contiene le funzioni che forniscono le capacità specificate da [Spring Boot 1.x ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://spring.io/projects/spring-boot).|
|`ibm/liberty:springBoot2`|Questa immagine contiene le funzioni che forniscono le capacità specificate da [Spring Boot 2.x ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://spring.io/projects/spring-boot).|
|`ibm/liberty:webProfile7`|Questa immagine include tutte le funzioni richieste per la conformità a Java EE 7 Web Profile.|
|`ibm/liberty:webProfile8`|Questa immagine include tutte le funzioni richieste per la conformità a Java EE 8 Web Profile.|
|`ibm/liberty:javaee7`|Questa immagine include tutte le funzioni dall'immagine `ibm/liberty:webProfile7`, oltre alle funzioni richieste per la conformità a Java EE 7 Full Platform.|
|`ibm/liberty:javaee8`|Questa immagine include tutte le funzioni dall'immagine `ibm/liberty:webProfile8`, oltre alle funzioni richieste per la conformità a Java EE 8 Full Platform.|
{: caption="Tabella 1. Le funzioni che sono incluse in ciascuna delle immagini <codeibm/liberty</code>" caption-side="top"}>

## Restrizioni di utilizzo
{: #ibmliberty_usage}

La seguente tabella mostra le restrizioni che si applicano all'utilizzo gratuito dell'immagine `ibm/liberty` in {{site.data.keyword.cloud_notm}}.
{:shortdesc}

Il prezzo dell'immagine `ibm/liberty` è indipendente dal prezzo dei contenitori che utilizzi in {{site.data.keyword.cloud_notm}}.
{:tip}

|Ambiente|Restrizioni di utilizzo gratuito|
|-----------|-----------------------|
|Sviluppo|Utilizzo gratuito **illimitato** dell'immagine `ibm/liberty`.|
|Produzione|L'utilizzo gratuito dell'immagine `ibm/liberty` è limitato a un **massimo di 2 GB di spazio heap Java** tra tutte le istanze del contenitore che eseguono l'immagine. Ad esempio, puoi avere 2 x 1 GB o 4 x 512 MB istanze liberty heap gratuite.|
{: caption="Tabella 2. Prezzi" caption-side="top"}

Per monitorare l'utilizzo heap Java delle tue istanze del contenitore, vedi [Monitoraggio dell'utilizzo dello spazio heap Java per un contenitore con la CLI](#ibmliberty_monitor_heap).

Rivedi i termini di utilizzo per le immagini certificate da IBM nella sezione Licenze dell'[immagine websphere-liberty ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://hub.docker.com/_/websphere-liberty/) su Docker Hub.

## Introduzione
{: #ibmliberty_get_started}

Utilizza una delle immagini gratuite `ibm/liberty` dal catalogo {{site.data.keyword.cloud_notm}} o seleziona la tua propria immagine con licenza di produzione per creare un singolo contenitore o un gruppo di contenitori.
{:shortdesc}

Prima di iniziare, esamina le [restrizioni di utilizzo](#ibmliberty_usage) per le immagini `ibm/liberty`.
{: important}

1. Dal catalogo, seleziona **Contenitori** > **IBM Cloud Container Registry** > **Repository pubblici IBM** nel pannello laterale. Ricerca l'immagine `ibm/liberty` da cui creare il tuo contenitore. Se hai creato la tua propria immagine con licenza di produzione e l'hai distribuita a {{site.data.keyword.cloud_notm}}, selezionala dal catalogo. Viene visualizzata la pagina di creazione del contenitore.
2. Dall'elenco **TAG/VERSION**, seleziona la versione dell'immagine `ibm/liberty` che vuoi utilizzare.
3. Per ulteriori informazioni sulla creazione dei contenitori dalle immagini, sulla configurazione dei cluster e sulla distribuzione delle applicazioni nei cluster, utilizza i seguenti link:

    - [Creazione dei contenitori dalle immagini](/docs/containers?topic=containers-images#images)
    - [Introduzione al servizio IBM Cloud Kubernetes](/docs/containers?topic=containers-getting-started#getting-started)
    - [Distribuzione di applicazioni nei cluster](/docs/containers?topic=containers-app#app)

    L'immagine `ibm/liberty` richiede che la porta 9080 sia pubblica. Quando crei un contenitore dal dashboard {{site.data.keyword.cloud_notm}}, la porta viene aggiunta nel campo **Porta pubblica** per impostazione predefinita. Se crei un contenitore dalla CLI, rendi pubblica la porta eseguendo il comando `kubectl run` con l'opzione `--port=9080`.
    {:tip}

## Monitoraggio dell'utilizzo dello spazio heap Java per un contenitore con la CLI
{: #ibmliberty_monitor_heap}

Dopo aver creato un contenitore dall'immagine `ibm/liberty`, puoi visualizzare le metriche di un pod in particolare e i relativi contenitori e controllare l'utilizzo heap Java. Lo spazio heap Java è la quantità di memoria disponibile per l'applicazione Java durante il runtime.
{:shortdesc}

1. Ottieni il nome del pod per cui vuoi visualizzare le metriche.
  
   ```
   kubectl get pods
   ```
   {: pre}

2. Visualizza le metriche di un pod in particolare e i relativi contenitori.

   ```
   kubectl top pod POD_NAME --containers
   ```
   {: pre}

3. Per controllare l'utilizzo heap Java, devi accedere alla statistica di memoria **RSS**; vedi [Get a Shell to a Running Container ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/). L'utilizzo heap Java viene visualizzato in kilobyte (KB). Se il tuo utilizzo heap è inferiore a 2097152 KB (2 GB) tra tutte le istanze, non dovrai acquistare una licenza di WebSphere Application Server.

4. Regola l'utilizzo heap massimo per la tua istanza WebSphere Application Server. Per ulteriori informazioni, vedi [Setting generic JVM arguments in the WebSphere Application Server V8.5 Liberty profile ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://www-01.ibm.com/support/docview.wss?uid=swg21596474).

## Come ottenere una licenza WebSphere Application Server
{: #ibm/liberty_license}

Le licenze WebSphere Application Server si basano sul numero di Processor Value Unit \(PVU\) di cui hai bisogno. PVU è un'unità di misura per la licenza del software IBM Middleware. Il numero di PVU indica il numero di processori \(core\) disponibili per il software.
{:shortdesc}

Ogni dimensione del contenitore in {{site.data.keyword.cloud_notm}} richiede un numero specifico di titolarità PVU che devono essere disponibili nella licenza WebSphere Application Server. Pertanto, devi pianificare i tuoi contenitori `ibm/liberty` prima di acquistare la licenza.

Per acquistare una licenza WebSphere Application Server, contatta il [servizio IBM ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://www.ibm.com/us-en/marketplace/java-ee-runtime/purchase). Se già disponi di una licenza per WebSphere Application Server v8.5 o successiva, puoi utilizzare qualsiasi PVU non utilizzato dalla tua titolarità esistente per la distribuzione del tuo contenitore.

Se scopri di aver bisogno di altri PVU dopo aver acquistato la licenza, puoi incrementarne la quantità contattando il [servizio IBM ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://www.ibm.com/us-en/marketplace/java-ee-runtime/purchase).

## Creazione di un'immagine `ibm/liberty` con licenza di produzione da utilizzare con {{site.data.keyword.containershort_notm}}
{: #ibmliberty_prod_image}

Utilizza la tua licenza WebSphere Application Server per creare un'immagine `ibm/liberty` con licenza di produzione che puoi utilizzare con {{site.data.keyword.containershort_notm}}. Scegli una delle seguenti attività.
{:shortdesc}

- [Upgrade the image from Docker Hub to a production image ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade).
- [Build your own production-licensed image ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://github.com/WASdev/ci.docker/tree/master/ga).

Dopo aver creato un'immagine con licenza di produzione, [inserisci l'immagine nel tuo registro privato](/docs/services/Registry?topic=registry-getting-started#getting-started) per utilizzarla con {{site.data.keyword.containershort_notm}}.

## Creazione di un'immagine dalle immagini fornite
{: #ibmliberty_creating_image}

Puoi utilizzare una delle immagini `ibm/liberty` come immagine principale per creare un'immagine secondaria che include il tuo codice applicazione. Personalizza il Dockerfile di esempio e crea l'immagine sul tuo computer. Puoi quindi aggiungere la tua immagine al registro delle immagini privato della tua organizzazione e creare i contenitori con essa.
{:shortdesc}

Prima di iniziare, esamina la seguente procedura.

- Crea il tuo codice applicazione in un file WAR, EAR o OSGi.
- Copia il file nella directory da cui desideri creare la tua immagine.

Per creare un'immagine con il tuo codice applicazione dall'immagine `ibm/liberty`, completa la seguente procedura:

1. Con un editor di testo, crea un file denominato `Dockerfile` e copia al suo interno le seguenti informazioni.

   ```
   FROM icr.io/ibm/liberty:<tag>
   COPY <app_name>.<file_extension> /config/dropins/
   ```
   {: pre}

    La directory `/config` è un percorso rapido per `/opt/ibm/wlp/usr/servers/defaultServer`.
    {: note}

2. Sostituisci `<tag>` con la versione dell'immagine `ibm/liberty` che include le funzioni richieste dalla tua applicazione.

3. Sostituisci `<app_name>` con il nome del file della tua applicazione.

4. Sostituisci `<file_extension>` con `.war`, `.ear` o `.eba`.

5. Aggiungi eventuali altre dipendenze per la tua applicazione al Dockerfile.

6. Crea e inserisci l'immagine nel tuo registro delle immagini privato. Per ulteriori informazioni, vedi [Introduzione a {{site.data.keyword.registrylong_notm}}](/docs/services/Registry?topic=registry-getting-started#getting-started).

Tutte le immagini `ibm/liberty` sono configurate per scrivere i file di log Liberty nella directory `/logs` all'interno del contenitore. Tutti gli altri file scritti dal server Liberty, vengono creati nella directory `/opt/ibm/wlp/output/defaultServer`. Puoi accedere a questi file utilizzando il percorso rapido `/output`.
{:tip}

## Guida di riferimento al Dockerfile `ibm/liberty`
{: #ibmliberty_reference_dockerfile}

I Dockerfile dell'immagine `ibm/liberty` sono disponibili come riferimento nel [repository GitHub WASdev/ci.docker ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://github.com/WASdev/ci.docker/tree/master/ga).
{:shortdesc}
