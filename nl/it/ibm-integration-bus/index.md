---

copyright:
  years: 2016, 2017
lastupdated: "2017-11-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# Introduzione all'immagine *ibm-integration-bus*
{: #iib}

L'immagine **ibm-integration-bus** viene fornita per {{site.data.keyword.containerlong}}. Include l'edizione IBM Integration Bus for Developers.
{:shortdesc}


## Come funziona
{: #how_it_works}

IBM Integration Bus for Developers contiene tutto ciò che ti serve per iniziare a sviluppare le tue proprie soluzioni di integrazione.
{:shortdesc}

Dopo aver creato una soluzione di integrazione, puoi utilizzare l'immagine **ibm-integration-bus** per eseguire il provisioning di un singolo contenitore in {{site.data.keyword.Bluemix_notm}}. Puoi quindi distribuire la tua soluzione di integrazione in questo contenitore utilizzando la IU web o da un terminale.

**Nota**: puoi utilizzare l'immagine **ibm-integration-bus** solo per lo sviluppo e la verifica di unità. Puoi anche utilizzare l'immagine per esplorare il prodotto, imparare dalle esercitazioni e valutare il contributo che IBM Integration Bus può fornire alla tua organizzazione.


## Elementi inclusi
{: #whats_included}

Questa immagine di IBM Integration Bus contiene il pacchetto software per IBM Integration Bus for Developers versione 10 \(noto anche come Developer Edition\), che è una versione completa del prodotto che puoi utilizzare per lo sviluppo e la verifica di unità. Puoi scaricare questa versione gratuitamente e sei libero di utilizzarla nei termini della licenza.
{:shortdesc}

IBM Integration Bus for Developers versione 10.0 è disponibile per i sistemi operativi Windows™ a 64 bit e Linux™ su x86-64. Tutti i prerequisiti del prodotto sono inclusi nel pacchetto di download.

Per informazioni sulle funzioni incluse in IBM Integration Bus for Developers, vedi [IBM Integration Bus in IBM Knowledge Center ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.msgbroker.helphome.doc/help_home_msgbroker.htm){: new_window}.

Puoi accedere alla Developer Edition dalla [pagina di download di IBM Integration Bus for Developers ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://www.ibm.com/marketing/iwm/iwm/web/pick.do?source=swg-wmbfd){: new_window}.


## Restrizioni di utilizzo
{: #usage}

La seguente tabella mostra le restrizioni che si applicano all'utilizzo gratuito dell'immagine **ibm-integration-bus** in {{site.data.keyword.Bluemix_notm}}.
{:shortdesc}

**Nota**: il prezzo dell'immagine **ibm-integration-bus** è indipendente dal prezzo dei contenitori che utilizzi in {{site.data.keyword.Bluemix_notm}}.

|Ambiente|Restrizioni di utilizzo gratuito|
|-----------|-----------------------|
|Sviluppo|Utilizzo gratuito **illimitato** dell'immagine **ibm-integration-bus** per la creazione di singoli contenitori per lo sviluppo e la verifica di unità.|
|Produzione|La Developer Edition è limitata all'elaborazione di una transazione al secondo e non è supportata per l'utilizzo in un ambiente di produzione.|
{: caption="Tabella 1. Restrizioni sull'utilizzo gratuito dell'immagine ibm-integration-bus" caption-side="top"}


## Licenza
{: #license}

Informazioni sulle licenze.
{:shortdesc}

*   I Dockerfile e gli script associati sono concessi in licenza con la [Licenza Apache 2.0 ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](http://www.apache.org/licenses/LICENSE-2.0.html){: new_window}.
*   [Informazioni sulla licenza per IBM Integration Bus for Developers versione 10.0 ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](http://www.ibm.com/software/sla/sladb.nsf/displaylis/1BD32B141C9FDAE38525802C0055A956?OpenDocument){: new_window}.

    **Nota**: la licenza non consente un'ulteriore distribuzione. I termini per IBM Integration Bus nell'immagine limitano l'utilizzo per lo sviluppo e la verifica di unità.


## Introduzione
{: #get_started}

Utilizza l'immagine **ibm-integration-bus** dal catalogo {{site.data.keyword.Bluemix_notm}} o seleziona la tua propria immagine con licenza di produzione per creare un singolo contenitore.
{:shortdesc}

**Importante**: prima di iniziare, esamina le [restrizioni di utilizzo](#usage) per l'immagine **ibm-integration-bus**.

**Nota**: puoi utilizzare i comandi Docker nativi in tutti i passi contrassegnati con un asterisco (\*).

   Completa la seguente procedura per eseguire IBM Integration Bus in un contenitore.

1.  [Esegui il provisioning di un contenitore basato sull'immagine ibm-integration-bus dalla IU {{site.data.keyword.Bluemix_notm}}](#provision)
2.  [Convalida il contenitore e l'installazione di IBM Integration Bus](#validate)
3.  [Crea e distribuisci un flusso](#createdeployflow)
4.  [Monitora i log di IBM Integration Bus all'interno del contenitore](#monitor)
5.  [Gestisci le risorse di IBM Integration Bus in esecuzione nel contenitore](#manage)


## Esegui il provisioning di un contenitore basato sull'immagine **ibm-integration-bus**
{: #provision}

Esegui il provisioning di un contenitore Docker in {{site.data.keyword.Bluemix_notm}}, basato sull'immagine **ibm-integration-bus** fornita da {{site.data.keyword.IBM_notm}}.
{:shortdesc}

  Completa la seguente procedura:

1.  Accedi a {{site.data.keyword.Bluemix_notm}}.
2.  Dal catalogo, seleziona **Contenitori** e scegli l'immagine **ibm-integration-bus** da cui creare il tuo contenitore.
3.  Seleziona **Singolo** per creare un singolo contenitore dell'istanza, che può essere utilizzato per scopi di sviluppo e verifica.
4.  Immetti il nome del contenitore, ad esempio `iib`.
5.  Seleziona la dimensione del contenitore.
6.  Nel campo **Indirizzo IP pubblico**, seleziona *Esegui il bind dell'IP pubblico*.
7.  Nel campo **Porte pubbliche**, specifica `4414, 7800`.
8.  Espandi le opzioni **Avanzate** e fai clic su **Aggiungi nuove variabili di ambiente**.
9.  Aggiungi le seguenti variabili di ambiente:

    <ul>
      <li>LICENSE con il valore <i>accept</i>
      <li>NODENAME con il valore &lt;MYNODE&gt; &lpar;dove &lt;MYNODE&gt; è il nome del nodo che hai scelto&rpar;</li>
      <li>LOG_LOCATIONS con il valore <i>/var/log/syslog</i>
    </ul>

    **Nota**: se non specifichi i valori per le proprietà LICENSE e NODENAME, il contenitore viene creato ma non avviato.

    La proprietà LOG_LOCATIONS specifica i log nel contenitore che desideri esporre tramite la IU {{site.data.keyword.Bluemix_notm}}.
10. Fai clic su **Crea** e attendi che il contenitore avvii l'esecuzione.


## Convalida il contenitore e l'installazione di IBM Integration Bus
{: #validate}

Dopo che il contenitore che esegue IBM Integration Bus viene distribuito in {{site.data.keyword.Bluemix_notm}}, controlla lo stato del contenitore e convalida l'installazione di IBM Integration Bus.
{:shortdesc}

  Completa la seguente procedura per verificare il setup e la configurazione di IBM Integration Bus nel contenitore:
  
1.  Imposta la CLI {{site.data.keyword.containershort_notm}}, come descritto in [Configurazione del plug-in {{site.data.keyword.containershort_notm}} \(`bx ic`\) per utilizzare la CLI Docker nativa](/docs/containers/container_cli_cfic_install.html).

2.  Da un terminale, accedi a {{site.data.keyword.Bluemix_notm}}. Esegui questi comandi:

    <ol>
       <li>
          <pre class="codeblock">bx login</pre>
       </li>
       <li>
          <pre class="codeblock">bx ic init</pre>
       </li>
    </ol>

3.  Controlla lo stato del contenitore.\*

    ```
    bx ic ps
    ```
    {: pre}

    **Nota**: in questo comando, puoi sostituire **bx ic** con **docker** quando [accedi a {{site.data.keyword.containershort_notm}}](/docs/containers/container_cli_cfic_install.html#container_cli_login) e impostare le tue variabili di ambiente per utilizzare i comandi Docker nativi. Puoi utilizzare i comandi Docker nativi in tutti i passi contrassegnati con un asterisco (\*).

    ```
    $ bx ic ps
    CONTAINER ID        IMAGE                                                       COMMAND             CREATED             STATUS                 PORTS                                                        NAMES
    46d9ec1e-952        registry.ng.bluemix.net/ibm-integration-bus:latest   ""                  2 minutes ago       Running a minute ago   xxx.xxx.xxx.xxx:4414->4414/tcp, xxx.xxx.xxx.xxx:7800->7800/tcp   iib
    ```
    {: screen}

4.  Allega una sessione bash al tuo contenitore.\*

    ```
    bx ic exec -it <container_name> /bin/bash
    ```
    {: pre}

    dove *container_name* è il nome del contenitore.

    Ad esempio:

    ```
    $ bx ic exec -it iib /bin/bash
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

5.  Inizializza l'ambiente eseguendo il comando **mqsiprofile**, come descritto in [Command environment: Linux and UNIX™ systems ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](http://www.ibm.com/support/knowledgecenter/SSMKHH_9.0.0/com.ibm.etools.mft.doc/an26230_.htm){: new_window}.

    Il comando per inizializzare l'ambiente è disponibile nella directory: `/opt/ibm/iib-10.0.0.6/server/bin/`

    Ad esempio:

    ```
    (IIB_10:)iibuser@instance-0098858e:/$ . ./opt/ibm/iib-10.0.0.6/server/bin/mqsiprofile

    MQSI 10.0.0.6
    /opt/ibm/iib-10.0.0.6/server

    # Controlla che la variabile di ambiente <MQSI_WORKPATH> sia impostata
    (IIB_10:)iibuser@instance-0098858e:/$ echo $<MQSI_WORKPATH>
    /var/mqsi
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

6.  Esegui il comando **mqsilist** per visualizzare lo stato del tuo nodo, che dovrebbe essere elencato come `running`. Ad esempio:

    ```
    (IIB_10:)iibuser@instance-0098858e:/$ mqsilist
    BIP1325I: Integration node 'MYNODE' with administration URI 'http://instance-0098858e:4414' is running.
    BIP8071I: Successful command completion.
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

Il tuo contenitore è ora in esecuzione e puoi distribuire le soluzioni di integrazione ad esso utilizzando uno qualsiasi dei metodi supportati.


## Monitora i log di IBM Integration Bus all'interno del contenitore
{: #monitor}

Monitora i log tramite la IU {{site.data.keyword.Bluemix_notm}} o dalla riga di comando.
{:shortdesc}

Prima di iniziare, configura la CLI {{site.data.keyword.containershort_notm}} per monitorare i log dalla riga di comando. Per ulteriori informazioni, vedi [Configurazione del plug-in {{site.data.keyword.containershort_notm}} (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

L'immagine **ibm-integration-bus** è configurata per inviare i messaggi IBM Integration Bus in `/var/log/syslog` all'interno del contenitore.

Scegli uno dei seguenti modi per monitorare i log di IBM Integration Bus:

*  Se hai configurato la variabile di ambiente  LOG_LOCATIONS quando hai eseguito il provisioning del contenitore, puoi monitorare i log dalla IU {{site.data.keyword.Bluemix_notm}}.

    Completa la seguente procedura per accedere ai log:

    <ol>
       <li>Dal dashboard del contenitore, seleziona il contenitore che desideri monitorare.</li>
       <li>Seleziona <b>Monitoraggio e registrazione</b>.</li>
       <li>Seleziona <b>Registrazione</b>. Il dashboard elenca le voci di log.</li>
       <li>Per personalizzare la vista dei log, seleziona <b>Vista avanzata</b> nella pagina Registrazione per aprire Kibana. Per ulteriori informazioni, vedi
           <a href="https://console.bluemix.net/docs/containers/container_planning.html#container_planning_monlog" target="_blank">Monitoraggio e registrazione</a> per iniziare.
       </li>
    </ol>

*  Da un terminale, utilizza la CLI di Docker o di {{site.data.keyword.containerlong_notm}} per monitorare i messaggi. Completa la seguente procedura:

    <ol>
       <li>
       Configura il tuo terminale.

        <ul>
        <li>Per utilizzare la CLI {{site.data.keyword.containerlong_notm}} per gestire i contenitori in {{site.data.keyword.Bluemix_notm}} mentre stai ancora utilizzando la CLI Docker per gestire direttamente il tuo host Docker locale, completa la seguente procedura per configurare l'ambiente:
           <ol>
             <li>
               <pre class="codeblock">bx login</pre>
             </li>
             <li>
               <pre class="codeblock">bx ic init</pre>
             </li>
           </ol>
        </li>
        <li>Per utilizzare la CLI Docker per gestire i contenitori in {{site.data.keyword.Bluemix_notm}}, completa la seguente procedura:
          <ol>
            <li>
              <pre class="codeblock">bx login</pre>
            </li>
            <li>
              <pre class="codeblock">bx ic init</pre>
               Copia i valori forniti per le seguenti variabili di ambiente: &lt;DOCKER_HOST&gt;, &lt;DOCKER_CERT_PATH&gt; e &lt;DOCKER_TLS_VERIFY&gt;.
            </li>
            <li>
            Sovrascrivi l'ambiente Docker locale impostando le seguenti variabili per il collegamento a {{site.data.keyword.containerlong_notm}}:
               <ul>
                 <li><pre class="codeblock">export DOCKER_HOST=&lt;your_host_value&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;your_cert_path_value&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
               </ul>
                <b>Nota</b>: questa opzione supporta solo alcuni comandi Docker.
            </li>
          </ol>
        </li>
      </ul>
      </li>
      <li>
      Accedi al log. Scegli una delle seguenti opzioni:

      <ul>
         <li>Se il tuo terminale è configurato per eseguire i comandi della CLI {{site.data.keyword.containerlong_notm}}, immetti il seguente comando:

            <pre class="codeblock">bx ic exec &lt;container_id&gt; tail -f /var/log/syslog</pre>

            dove *container_id* è il nome del tuo contenitore.

            Ad esempio:

            <pre class="screen">bx ic exec iib tail -f /var/log/syslog</pre>

          </li>
          <li>
           Se il tuo terminale è configurato per eseguire i comandi della CLI Docker, immetti il seguente comando:

            <pre class="codeblock">docker exec &lt;container_id&gt; tail -f /var/log/syslog</pre>

            dove *container_id* è il nome del tuo contenitore.

            Ad esempio:

            <pre class="screen">docker exec iib tail -f /var/log/syslog</pre>

          </li>
        </ul>
       </li>
    </ol>


## Gestisci le risorse di IBM Integration Bus in esecuzione nel contenitore
{: #manage}

Avvia la IU web IBM Integration Bus o esegui i comandi da un terminale per gestire le risorse di IBM Integration Bus che sono in esecuzione in un contenitore.
{:shortdesc}

  Scegli una delle seguenti opzioni per gestire le risorse di IBM Integration Bus nel contenitore:

*  [Avvia la IU web IBM Integration Bus](#launchwebUI) e gestisci IBM Integration Bus graficamente.
*  Da un terminale, utilizza la CLI di Docker o di {{site.data.keyword.containershort_notm}} per gestire le risorse di IBM Integration Bus:
    *   [Esegui i comandi di amministrazione nel contenitore utilizzando la CLI {{site.data.keyword.containerlong_notm}}](#admin_commands_containers_cli)
    *   [Esegui i comandi di amministrazione nel contenitore utilizzando la CLI Docker](#admin_commands_docker_cli)


## Avvia la IU web IBM Integration Bus
{: #launchwebUI}

Avvia la IU web per gestire le risorse di IBM Integration Bus in esecuzione nel contenitore dal tuo browser.
{:shortdesc}

Segui questa procedura per connettere un browser al tuo host, che hai esposto in  [Introduzione all'immagine ibm-integration-bus per {{site.data.keyword.Bluemix_notm}}](#get_started). Viene visualizzata l'interfaccia utente web IBM Integration Bus.

1.  Seleziona il tuo contenitore dalla IU {{site.data.keyword.Bluemix_notm}} e verifica che lo stato del contenitore sia `In esecuzione`.
2.  Controlla i dettagli del contenitore per trovare l'IP pubblico.
3.  Apri un browser web con il seguente URL:

    ```
    http://<DockerContainerPublicIP>:4414
    ```
    {: pre}

    dove *DockerContainerPublicIP* è l'IP pubblico del contenitore che hai verificato nel passo precedente.

4.  Il browser apre l'interfaccia utente web e sei ora pronto a gestire IBM Integration Bus.


## Esegui i comandi di amministrazione nel contenitore utilizzando la CLI {{site.data.keyword.containerlong_notm}}
{: #admin_commands_containers_cli}

Utilizza la CLI {{site.data.keyword.containershort_notm}} per eseguire i comandi di amministrazione di IBM Integration Bus direttamente in un contenitore.
{:shortdesc}

Prima di iniziare, configura la CLI {{site.data.keyword.containershort_notm}} per eseguire i comandi di amministrazione dalla riga di comando. Per ulteriori informazioni, vedi [Configurazione del plug-in {{site.data.keyword.containershort_notm}} (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

  Completa la seguente procedura per configurare un terminale per l'esecuzione dei comandi **bx ic** per gestire IBM Integration Bus:

1.  Da un terminale, accedi a {{site.data.keyword.Bluemix_notm}}. Esegui questi comandi:

      <ol>
        <li>
           <pre class="codeblock">bx login</pre>
        </li>
        <li>
           <pre class="codeblock">bx ic init</pre>
        </li>
       </ol>
           
2.  Allega una sessione bash al tuo contenitore per configurare una sessione interattiva.\*

    ```
    bx ic exec -it <container_name> /bin/bash
    ```
    {: pre}

    dove *container_name* è il nome del contenitore.

    Ad esempio:

    ```
    bx ic exec -it iib /bin/bash
    ```
    {: screen}

    Puoi originare il mqsiprofile ed eseguire i tuoi comandi da questo punto, in una shell all'interno del contenitore.

3.  In alternativa, utilizza il comando **bx ic exec** per eseguire una sessione Bash non interattiva in grado di eseguire qualsiasi comando di IBM Integration Bus.\*

    Ad esempio:

    ```
    bx ic exec iib <container_name> /bin/bash -c mqsilist
    ```
    {: screen}


## Esegui i comandi di amministrazione nel contenitore utilizzando la CLI Docker
{: #admin_commands_docker_cli}

Utilizza la CLI Docker per eseguire i comandi di amministrazione di IBM Integration Bus direttamente in un contenitore.
{:shortdesc}

Prima di iniziare, configura la CLI {{site.data.keyword.containershort_notm}} per eseguire i comandi di amministrazione dalla riga di comando. Per ulteriori informazioni, vedi [Configurazione del plug-in {{site.data.keyword.containershort_notm}} (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

  Completa la seguente procedura per configurare un terminale per l'esecuzione dei comandi Docker per gestire IBM Integration Bus:

1.  Da un terminale, accedi a {{site.data.keyword.Bluemix_notm}}. Esegui questi comandi:

    <ol>
     <li>
       <pre class="codeblock">bx login</pre>
     </li>
     <li>
       <pre class="codeblock">bx ic init</pre>

      <p>Copia i valori forniti per le seguenti variabili di ambiente: &lt;DOCKER_HOST&gt;, &lt;DOCKER_CERT_PATH&gt; e &lt;DOCKER_TLS_VERIFY&gt;.</p>
      </li>
      <li>  Sovrascrivi l'ambiente Docker locale impostando le seguenti variabili per il collegamento a {{site.data.keyword.containershort_notm}}:
        <ul>
           <li><pre class="codeblock">export DOCKER_HOST=&lt;your_host_value&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;your_cert_path_value&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
        </ul>

          <p><b>Nota</b>: questa opzione supporta solo alcuni comandi Docker.</p>
     </li>
    </ol>

2.  Allega una sessione bash al tuo contenitore per configurare una sessione interattiva. Immetti il seguente comando:

    ```
    docker exec -it <container_name> /bin/bash
    ```
    {: pre}

    dove *container_name* è il nome del contenitore.

    Ad esempio:

    ```
    docker exec -it iib /bin/bash
    ```
    {: screen}

    Puoi originare il mqsiprofile ed eseguire i tuoi comandi da questo punto, in una shell all'interno del contenitore.

3.  Puoi originare il mqsiprofile ed eseguire i tuoi comandi da questo punto, in una shell all'interno del contenitore.

    In alternativa, utilizza docker exec per eseguire una sessione Bash non interattiva in grado di eseguire qualsiasi comando di IBM Integration Bus. 
    
    Ad esempio:

    ```
    docker exec iib <container_name> /bin/bash -c mqsilist
    ```
    {: screen}


## Crea e distribuisci un flusso
{: #createdeployflow}

Crea e distribuisci un flusso di messaggi seguendo le istruzioni nel Knowledge Center di IBM Integration Bus.
{:shortdesc}

Per informazioni su come creare un flusso di messaggi, vedi [How do I create and manage message flows? ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40150_.htm){: new_window}.

Per informazioni su come distribuire un flusso di messaggi, vedi [How do I deploy and configure message flows? ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40180_.htm){: new_window}.
