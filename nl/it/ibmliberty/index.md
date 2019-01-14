---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:download: .download}

# Introduzione all'immagine `ibmliberty`
{: #ibmliberty}

Le immagini di IBM® WebSphere® Application Server Liberty \(`ibmliberty`\) vengono fornite per {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

Puoi accedere alle immagini fornite da {{site.data.keyword.IBM_notm}} utilizzando la riga di comando, consulta [Immagini pubbliche IBM](/docs/services/Registry/registry_public_images.html#public_images).
{: tip}

## Come funziona
{: #how_it_works}

Puoi utilizzare un'immagine `ibmliberty` come immagine principale per creare la tua propria immagine e distribuire le tue applicazioni WAR, EAR o OSGi basate su Java in un contenitore IBM WebSphere Application Server Liberty.
{:shortdesc}

## Elementi inclusi
{: #whats_included}

Ogni immagine Liberty fornisce i seguenti pacchetti software.
{:shortdesc}

- IBM WebSphere Application Server for Developers Liberty
- IBM Java Runtime Environment 8.0

Le specifiche funzioni di Liberty che vengono installate nell'immagine dipendono dalla tag che hai selezionato. La seguente tabella mostra le funzioni che sono incluse in ciascuna delle immagini `ibmliberty`. Per ulteriori informazioni su ogni funzione, vedi la [panoramica delle funzioni Liberty in IBM Knowledge Center ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](http://www.ibm.com/support/knowledgecenter/SSAW57_8.5.5/com.ibm.websphere.wlp.nd.doc/ae/rwlp_feat.html).

|Tag|Descrizione|
|---|-----------|
|Tutte le immagini `ibmliberty`|Tutte le immagini `ibmliberty` includono le seguenti funzioni. <ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|`ibmliberty:latest`|Questa immagine punta all'immagine `ibmliberty:javaee7`.|
|`ibmliberty:microProfile`|Questa immagine contiene le funzioni che forniscono le capacità specificate da [MicroProfile ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://microprofile.io).|
|`ibmliberty:webProfile7`|Questa immagine include tutte le funzioni richieste per la conformità a Java EE7 Web Profile.|
|`ibmliberty:javaee7`|Questa immagine include tutte le funzioni dall'immagine `ibmliberty:webProfile7`, più le funzioni richieste per la conformità a Java EE7 Full Platform.|
{: caption="Tabella 1. Le funzioni che sono incluse in ciascuna delle immagini `ibmliberty`" caption-side="top"}

## Restrizioni di utilizzo
{: #usage}

La seguente tabella mostra le restrizioni che si applicano all'utilizzo gratuito dell'immagine `ibmliberty` in {{site.data.keyword.Bluemix_notm}}.
{:shortdesc}

Il prezzo dell'immagine `ibmliberty` è indipendente dal prezzo dei contenitori che utilizzi in {{site.data.keyword.Bluemix_notm}}.
{:tip}

|Ambiente|Restrizioni di utilizzo gratuito|
|-----------|-----------------------|
|Sviluppo|Utilizzo gratuito **illimitato** dell'immagine `ibmliberty`.|
|Produzione|L'utilizzo gratuito dell'immagine `ibmliberty` è limitato a un **massimo di 2 GB di spazio heap Java** in tutte le istanze del contenitore che eseguono l'immagine. Ad esempio, puoi avere 2 x 1 GB o 4 x 512 MB istanze liberty heap gratuite. |
{: caption="Tabella 2. Prezzi" caption-side="top"}

Per monitorare l'utilizzo heap Java delle tue istanze del contenitore, vedi [Monitoraggio dell'utilizzo dello spazio heap Java per un contenitore con la CLI](#monitor_heap).

Rivedi i termini di utilizzo per le immagini certificate da IBM nella sezione Licenze dell'[immagine websphere-liberty ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://hub.docker.com/_/websphere-liberty/) su Docker Hub.

## Introduzione
{: #get_started}

Utilizza una delle immagini gratuite `ibmliberty` dal catalogo {{site.data.keyword.Bluemix_notm}} o seleziona la tua propria immagine con licenza di produzione per creare un singolo contenitore o un gruppo di contenitori.
{:shortdesc}

**Importante:** prima di iniziare, controlla le [restrizioni di utilizzo](#usage) per le immagini `ibmliberty`.

1. Dal catalogo, seleziona **Contenitori** > **Registro IBM Cloud Container** > **Repository pubblici IBM** nel pannello laterale. Ricerca l'immagine `ibmliberty` da cui creare il tuo contenitore. Se hai creato la tua propria immagine con licenza di produzione e l'hai distribuita a {{site.data.keyword.Bluemix_notm}}, selezionala dal catalogo. Viene visualizzata la pagina di creazione del contenitore.
2. Seleziona la versione dell'immagine `ibmliberty` che desideri utilizzare dalla casella a discesa **TAG/VERSIONE**.
3. Per ulteriori informazioni sulla creazione dei contenitori dalle immagini, sulla configurazione dei cluster e sulla distribuzione delle applicazioni nei cluster, utilizza i seguenti link:

    - [Creazione dei contenitori dalle immagini](/docs/containers/cs_images.html#images)
    - [Introduzione al servizio IBM Cloud Kubernetes](/docs/containers/container_index.html#container_index)
    - [Distribuzione di applicazioni nei cluster](/docs/containers/cs_app.html#app)

    L'immagine `ibmliberty` richiede che la porta 9080 sia esposta pubblicamente. Quando crei un contenitore dal dashboard {{site.data.keyword.Bluemix_notm}}, la porta viene aggiunta nel campo **Porta pubblica** per impostazione predefinita. Se crei un contenitore dalla CLI, esponi la porta nel tuo comando `kubectl run` con l'opzione `--port=9080`.
    {:tip}

## Monitoraggio dell'utilizzo dello spazio heap Java per un contenitore con la CLI
{: #monitor_heap}

Dopo aver creato un contenitore dall'immagine `ibmliberty`, puoi visualizzare le metriche di un pod in particolare e i relativi contenitori e controllare l'utilizzo heap Java. Lo spazio heap Java è la quantità di memoria disponibile per l'applicazione Java durante il runtime.
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

3. Per controllare l'utilizzo heap Java, devi accedere alla statistica di memoria **RSS**. Segui le linee guida su come accedere a una shell di un contenitore, consulta [Get a Shell to a Running Container ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/). L'utilizzo heap Java viene visualizzato in kilobyte. Se il tuo utilizzo heap è inferiore a 2097152 kilobyte (2GB) tra tutte le istanze, non dovrai acquistare una licenza WebSphere Application Server.

4. Regola l'utilizzo heap massimo per la tua istanza WebSphere Application Server. Per ulteriori informazioni, vedi [Setting generic JVM arguments in the WebSphere Application Server V8.5 Liberty profile ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](http://www-01.ibm.com/support/docview.wss?uid=swg21596474).

## Come ottenere una licenza WebSphere Application Server
{: #license}

Le licenze WebSphere Application Server si basano sul numero di Processor Value Unit \(PVU\) di cui hai bisogno. PVU è un'unità di misura per la licenza del software IBM Middleware. Il numero di PVU indica il numero di processori \(core\) disponibili per il software.
{:shortdesc}

Ogni dimensione del contenitore in {{site.data.keyword.Bluemix_notm}} richiede un numero specifico di titolarità PVU che devono essere disponibili nella licenza WebSphere Application Server. Pertanto, devi pianificare i tuoi contenitori `ibmliberty` prima di acquistare la licenza.

Per acquistare una licenza WebSphere Application Server, contatta il [servizio IBM ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us). Se già disponi di una licenza per WebSphere Application Server v8.5 o successiva, puoi utilizzare qualsiasi PVU non utilizzato dalla tua titolarità esistente per la distribuzione del tuo contenitore.

Se scopri di aver bisogno di altri PVU dopo aver acquistato la licenza, puoi incrementarne la quantità contattando il [servizio IBM ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us).

## Creazione di un'immagine `ibmliberty` con licenza di produzione da utilizzare con {{site.data.keyword.containershort_notm}}
{: #prod_image}

Utilizza la tua licenza WebSphere Application Server per creare un'immagine `ibmliberty` con licenza di produzione che puoi utilizzare con {{site.data.keyword.containershort_notm}}. Scegli una delle seguenti attività.
{:shortdesc}

- [Upgrade the image from Docker Hub to a production image ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade).
- [Build your own production-licensed image ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://github.com/WASdev/ci.docker/tree/master/ga/production-install).

Dopo aver creato un'immagine con licenza di produzione, [inserisci l'immagine nel tuo registro privato](/docs/services/Registry/index.html) per utilizzarla con {{site.data.keyword.containershort_notm}}.

## Creazione di un'immagine dalle immagini fornite
{: #creating_image}

Puoi utilizzare una delle immagini `ibmliberty` come immagine principale per creare un'immagine secondaria che include il tuo codice applicazione. Personalizza il Dockerfile di esempio e crea l'immagine sul tuo computer. Puoi quindi aggiungere la tua immagine al registro delle immagini privato della tua organizzazione e creare i contenitori con essa.
{:shortdesc}

Prima di iniziare, esamina la seguente procedura.

- Crea il tuo codice applicazione in un file WAR, EAR o OSGi.
- Copia il file nella directory da cui desideri creare la tua immagine.

Per creare un'immagine con il tuo codice applicazione dall'immagine `ibmliberty`, completa la seguente procedura:

1. Con un editor di testo, crea un file denominato `Dockerfile` e copia al suo interno le seguenti informazioni.

   ```
   FROM registry.bluemix.net/ibmliberty:<tag>
    COPY <app_name>.<file_extension> /config/dropins/
   ```
   {: screen}

    **Nota:** la directory `/config` è un percorso rapido per `/opt/ibm/wlp/usr/servers/defaultServer`.

2. Sostituisci `<tag>` con la versione dell'immagine `ibmliberty` che include le funzioni richieste dalla tua applicazione.

3. Sostituisci `<app_name>` con il nome del file della tua applicazione.

4. Sostituisci `<file_extension>` con `.war`, `.ear` o `.eba`.

5. Aggiungi eventuali altre dipendenze per la tua applicazione al Dockerfile.

6. Crea e inserisci l'immagine nel tuo registro delle immagini privato. Per ulteriori informazioni, vedi [Introduzione a {{site.data.keyword.registrylong_notm}}](/docs/services/Registry/index.html).

Tutte le immagini `ibmliberty` sono configurate per scrivere i file di log Liberty nella directory `/logs` all'interno del contenitore. Tutti gli altri file scritti dal server Liberty, vengono creati nella directory `/opt/ibm/wlp/output/defaultServer`. Puoi accedere a questi file utilizzando il percorso rapido `/output`.
{:tip}

## Guida di riferimento al Dockerfile `ibmliberty`
{: #reference_dockerfile}

Questo Dockerfile illustra come viene creata l'immagine `ibmliberty:webProfile7` in {{site.data.keyword.Bluemix_notm}} dalle immagini websphere-liberty pubbliche nel Docker Hub. Queste informazioni sono fornite solo come riferimento.
{:shortdesc}

```
FROM websphere-liberty:webProfile7

# Aggiorna i pacchetti
RUN apt-get update &&\
    DEBIAN_FRONTEND=noninteractive apt-get upgrade -y &&\
    apt-get clean &&\
    rm -Rf /var/cache/*

# Imposta la lunghezza e la scadenza della password per la conformità con il Controllo vulnerabilità
RUN sed -i 's/^PASS_MAX_DAYS.*/PASS_MAX_DAYS   90/' /etc/login.defs
RUN sed -i 's/sha512/sha512 minlen=8/' /etc/pam.d/common-password

# Aggiungi l'etichetta dei documenti
LABEL doc.url="/docs/services/RegistryImages/ibmliberty/index.html"
```
{: screen}
