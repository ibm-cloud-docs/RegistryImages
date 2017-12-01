---

copyright:
  years: 2017
lastupdated: "2017-10-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Introduzione all'immagine **ibmnode** {: #getting_started_node}

L'immagine pubblica {{site.data.keyword.IBM}} Node (**ibmnode**) viene fornita per {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

**Nota:** l'immagine {{site.data.keyword.IBM_notm}} Node (**ibmnode**) non include un'applicazione Node.js di esempio. Quando distribuisci un contenitore da questa immagine, il tuo contenitore viene arrestato immediatamente dopo la sua creazione in quanto non esiste alcun processo di lunga durata. Per evitarlo, utilizza l'immagine {{site.data.keyword.IBM_notm}} Node (**ibmnode**) come immagine principale e crea la tua propria immagine in cui puoi aggiungere il tuo codice applicazione Node.js. Per ulteriori informazioni, vedi [Creazione di un'immagine da questa immagine fornita](#creating_image).

# Come funziona{: #how_it_works}

Utilizza l'immagine {{site.data.keyword.IBM_notm}} Node (**ibmnode**) come immagine principale per creare la tua propria immagine con il tuo codice applicazione Node.js.
{:shortdesc}

**Nota:** prima di iniziare, esamina i termini di utilizzo per le immagini certificate da IBM nella sezione Utilizzo della [libreria Docker websphere-liberty![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno:")](https://github.com/docker-library/docs/tree/master/websphere-liberty){: new_window}.

# Elementi inclusi{: #included}

* Ubuntu 14.04
* Pacchetti Python, Git e apt-get build-essential
* SDK {{site.data.keyword.IBM_notm}} Node

    **Nota:** l'immagine ibmnode è disponibile con diverse tag, così da permetterti di eseguire differenti versioni dell'SDK IBM Node.

    <table>
    <caption> Tabella 1. Tag per le diverse versioni SDK</caption>
      <tr>
        <th> Tag </th>
        <th> Descrizione</th>
      </tr>
      <tr>
        <td> ibmnode:latest </td>
        <td> L'ultima versione di IBM Node, equivalente all'utilizzo di ibmnode:v4 </td>
      </tr>
      <tr>
        <td> ibmnode:v4 </td>
        <td> IBM Node 4.4.0 </td>
      </tr>
      <tr>
        <td> ibmnode:v1.2 </td>
        <td> IBM Node 1.2.0.8 (0.12.10) </td>
      </tr>
      <tr>
        <td> ibmnode:v1.1 </td>
        <td> IBM Node 1.1.0.18 (0.10.43) </td>
      </tr>
    </table>


# Creazione di un'immagine da questa immagine fornita{: #creating_image}

Puoi utilizzare l'immagine **ibmnode** come immagine principale per creare un'immagine secondaria che include il tuo codice applicazione. Utilizza il contenitore dell'applicazione di esempio `Hello World` con **ibmnode** e il modulo Express Node per creare un'immagine sul tuo computer. Aggiungi l'immagine al {{site.data.keyword.registrylong_notm}} privato della tua organizzazione e crea un contenitore con tale immagine. Puoi quindi utilizzare il Dockerfile dell'esempio Hello World per creare un'altra immagine con la tua applicazione.
{:shortdesc}

Prima di iniziare, esamina la seguente procedura.

* Devi installare i seguenti strumenti in locale:
  * [Plug-in di {{site.data.keyword.registrylong_notm}} per Cloud Foundry e la CLI Docker](/docs/containers/container_cli_cfic_install.html)
  * [Codice sorgente Node.js ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://nodejs.org/en/download/){: new_window}
  * [npm ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://github.com/npm/npm){: new_window}
* [Devi conoscere lo spazio dei nomi del {{site.data.keyword.registryshort_notm}} privato della tua organizzazione.](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__namespace)

    Esempio

    registry.DomainName/<var class="keyword varname">namespace</var>

* [Devi disporre di un indirizzo IP.](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__ip_request)

Crea un'immagine basata sull'immagine **ibmnode** e che includa un'applicazione Hello World.
1.  Scarica il file [hellonode.zip ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](ftp://public.dhe.ibm.com/cloud/bluemix/containers/hellonode.zip){: new_window} ed estrailo.
1.  Apri il Dockerfile con un editor di testo e, nell'istruzione FROM, aggiorna l'URL del registro per la regione che desideri utilizzare.

    <ul>
    <li>Stati Uniti Sud

        ```
		registry.ng.bluemix.net/ibmnode
		```
		{: codeblock}

    </li>
    <li>Regno Unito

        ```
		registry.eu-gb.bluemix.net/ibmnode
		```
		{: codeblock}

      </li>
    </ul>

1.  Dalla CLI, passa alla directory **app**.
1.  Nella directory **app**, immetti i seguenti comandi.
    1.  Crea un file package.json.

        ```
        npm init
        ```
        {: pre}

        **Suggerimento:** Premi indietro per accettare il valore predefinito per ogni richiesta.

    2.  Installa Express, un framework per Node.js. Il package.json viene aggiornato con le informazioni sulla versione di Express nella sezione delle dipendenze.

        ```
        npm install express -save
        ```
        {: pre}

1.  Ritorna alla directory **hellonode**.
1.  Crea l'immagine e inseriscila nel tuo {{site.data.keyword.registryshort_notm}} privato.*

    ```
    bx ic build -t registry.<DomainName>/<namespace>/hellonode .
    ```
    {: pre}

    **Suggerimento:** esegui `bx ic namespace get` per richiamare le informazioni del tuo spazio dei nomi.  Imposta _registry.DomainName_ sulla stessa regione che hai utilizzato precedentemente nel Dockerfile.

    **Nota:** \*in questo comando, puoi sostituire `bx ic` con `docker` quando [hai effettuato l'accesso a {{site.data.keyword.containershort_notm}}](/docs/containers/container_cli_cfic_install.html#container_cli_login) e impostare le tue variabili di ambiente per utilizzare i comandi Docker nativi. Puoi utilizzare i comandi Docker nativi in tutti i passi contrassegnati con un asterisco (*) in questo argomento.

1.  Crea un contenitore dalla tua immagine.*

    ```
    bx ic run -p 3000 --name helloworld registry.<DomainName>/<namespace>/hellonode
    ```
    {: pre}

1.  Esegui il bind di un indirizzo IP pubblico al tuo contenitore.

    ```
    bx ic ip bind <IPaddress> helloworld
    ```
    {: pre}

    **Suggerimento:** puoi vedere i tuoi indirizzi IP pubblici eseguendo `bx ic ip list`. Per richiederne uno nuovo, esegui `bx ic ip request`.

1. In un browser, verifica l'applicazione aprendo **<var class="varname">IPaddress</var>:3000**. Viene visualizzato il messaggio Hello World!.

Ora che hai creato l'immagine hellonode, puoi modificare il Dockerfile in modo da includere invece la tua applicazione e creare un'altra immagine per tale applicazione.

1.  Per l'istruzione ADD, aggiungi il tuo codice applicazione.
1.  Per l'istruzione EXPOSE, aggiorna le informazioni sulla porta.
1.  Per l'istruzione CMD, crea un comando per avviare la tua applicazione.


# Guida di riferimento al Dockerfile **ibmnode** {: #reference_dockerfile}

Questo Dockerfile è stato utilizzato per creare l'immagine **ibmnode** nel catalogo {{site.data.keyword.Bluemix_notm}}. Queste informazioni sono fornite solo come riferimento. Gli altri file inclusi nella versione creata di questa immagine non sono forniti.
{:shortdesc}

```
FROM ubuntu:14.04

# Aggiorna il sistema operativo di base
RUN apt-get update &&\
DEBIAN_FRONTEND=noninteractive apt-get -y install \
git \
build-essential \
python && \
DEBIAN_FRONTEND=noninteractive apt-get upgrade -y &&\
apt-get clean &&\
rm -Rf /var/cache/*

# Migliora le regole di password predefinite
RUN sed -i 's/^PASS_MAX_DAYS.*/PASS_MAX_DAYS 90/' /etc/login.defs
RUN sed -i 's/sha512/sha512 minlen=8/' /etc/pam.d/common-password

# Installa SDK Node
ONBUILD COPY ./prereq/node.tar.gz /tmp/prereq/
ONBUILD RUN cd /usr/local &&\
tar --strip-components 1 -xzf /tmp/prereq/node.tar.gz &&\
rm -rf /tmp/prereq

# Percorso della documentazione per l'URL
LABEL doc.url="/docs/services/RegistryImages/ibmnode/index.html"
```
{: screen}
