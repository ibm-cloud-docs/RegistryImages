---

copyright:
  years: 2018
lastupdated: "2018-08-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Introduzione all'immagine ibmcloud-secure-perimeter-health
{: #ibmcloud-secure-perimeter-health}

L'immagine **ibmcloud-secure-perimeter-health** contiene uno strumento per eseguire la scansione delle vulnerabilità in un perimetro sicuro in IBM Cloud.
{:shortdesc}

## Come funziona
{: #how-it-works}

Per assicurare che il tuo Secure Perimeter stia funzionando correttamente, **ibmcloud-secure-perimeter-health** può eseguire la scansione di reti pubbliche o private nel tuo account dell'infrastruttura IBM Cloud e notificare le vulnerabilità. Puoi usare l'immagine **ibmcloud-secure-perimeter-health** in due modi:

-   Usa **ibmcloud-secure-perimeter-health** come un pod su un cluster Kubernetes nel tuo Secure Perimeter per eseguire la scansione per rilevare eventuali esposizioni delle reti private.
-   Usa **ibmcloud-secure-perimeter-health** come un contenitore Docker autonomo sulla tua workstation per eseguire la scansione per rilevare eventuali esposizioni delle reti pubbliche.

Ulteriori informazioni sul perimetro sicuro sono disponibili in questi articoli di blog:
  * [Set up a Secure Perimeter in IBM Cloud](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/)
  * [Set up an automated Secure Perimeter in IBM Cloud](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

Dopo la scansione, l'immagine **ibmcloud-secure-perimeter-health** produce un report relativo a quali reti erano raggiungibili dall'interno del Secure Perimeter Segment. Ogni report fornisce i dettagli del nome del gateway di rete, della VLAN, delle relative sottoreti e degli eventuali host che stanno causando la violazione. Un report di esempio di un utente che ha eseguito la scansione per rilevare le vulnerabilità delle reti private:
```
#-------- Running Secure Perimeter exposure scan 2018-05-24 12:00:00 --------#

RESULTS:

sp-gateway-af6053a9:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   FAIL
			host = 10.73.84.198, ports = [179, 22]
		10.73.63.8/29:     PASS
		10.73.72.128/26:   PASS
sp-gateway-8a9031ab:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   PASS
		10.73.63.8/29:     PASS
```
{: screen}

## Elementi inclusi
{: #whats_included}

L'immagine **ibmcloud-secure-perimeter-health** fornisce i seguenti pacchetti software.
{:shortdesc}

-   Alpine Linux
-   Runtime Python
-   SoftLayer Python Client
-   Scanner di porte Nmap

## Introduzione
{: #how_to_get_started}

Rivedi le seguenti attività per informazioni sulla modalità di utilizzo di **ibmcloud-secure-perimeter-health**:

1.  [Esegui il provisioning di un cluster Kubernetes in un Secure Perimeter utilizzando {{site.data.keyword.containerlong}}](#provision_cluster)
2.  [Esegui una scansione di reti private in un Secure Perimeter](#private_networks)
3.  [Esegui la scansione di reti pubbliche esternamente a un Secure Perimeter](#public_networks)
4.  [Informazioni sui risultati della scansione](#scan_results)
5.  [Guida di riferimento agli argomenti del contenitore](#reference_container_arg)
6.  [Riferimenti alla variabile di ambiente](#reference_env_var)


## Esegui il provisioning di un cluster Kubernetes in un Secure Perimeter utilizzando {{site.data.keyword.containerlong_notm}}
{: #provision_cluster}

1.  Esegui il provisioning del tuo cluster Kubernetes dalla sezione **Contenitori** nel catalogo IBM Cloud.
2.  Fai clic su Crea.
3.  Seleziona le VLAN private e pubbliche del Secure Perimeter Segment dai menu a discesa delle VLAN.
4.  Compila tutti gli altri dettagli come ritieni opportuno.
5.  Fai clic su Crea cluster.

Controlla la [documentazione di {{site.data.keyword.containerlong}}](/docs/containers/container_index.html#container_index) su come ottenere l'accesso al tuo cluster dopo che è stato distribuito.

## Esegui la scansione di reti private in un Secure Perimeter
{: #private_networks}

Crea un pod del contenitore dall'immagine **ibmcloud-secure-perimeter-health** e configura una scansione di routine.

Prima di iniziare:

-   Installa le [CLI](/docs/containers/cs_cli_install.html#cs_cli_install) obbligatorie.
-   [Indirizza la tua CLI](/docs/containers/cs_cli_install.html#cs_cli_configure) al tuo cluster.

1. Crea un file di configurazione denominato _health-pod.yaml_. Questo file crea una distribuzione altamente disponibile del pod del contenitore.

    ```
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: health-pod
      labels:
        app: health-pod
    spec:
      replicas: 1
      selector:
        app: health-pod
      template:
        spec:
          containers:
          - name: health-pod
            image: registry.bluemix.net/ibm/ibmcloud-secure-perimeter-health:1.0.0
            args:
            - /usr/local/bin/python
            - /run.py
            - --scan
            - private
            - --exclude-vlan-ids
            - <Private Secure Perimeter Segment VLAN ID>
            - --poll-interval
            - 1800
            env:
            - name: SL_USER
              value: <IBM Cloud infrastructure user name>
            - name: SL_APIKEY
              value: <IBM Cloud infrastructure api key>
    ```
    {: codeblock}

2. Crea la distribuzione.

    ```
    kubectl apply -f health-pod.yaml
    ```
    {: pre}

3. Conferma che il pod è in esecuzione.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    health-pod-<random-id>                  1/1       Running   0          1hr
    ```
    {: screen}

## Esegui la scansione di reti pubbliche esterne a un Secure Perimeter
{: #public_networks}

Crea un contenitore Docker dall'immagine **ibmcloud-secure-perimeter-health** ed esegui la scansione delle reti pubbliche.

Prima di iniziare:

-  Installa Docker

1. Crea un contenitore Docker dalla tua workstation nel seguente modo:

    ```
    docker run -it -e SL_USER='$SL_USER' -e SL_APIKEY='$SL_APIKEY' registry.bluemix.net/ibm/ibmcloud-secure-perimeter-health:1.0.0 /usr/local/bin/python run.py --scan public --allowed-public-ports 80 443 9000-9999
    ```
    {: pre}

2. Dopo che il contenitore ha prodotto un report, rivedi la sezione [Analisi dei risultati della scansione](#scan_results) per comprendere i risultati.

## Analisi dei risultati della scansione
{: #scan_results}

**ibmcloud-secure-perimeter-health** produce un report formattato sullo stato di funzionamento di un Secure Perimeter:
```
#-------- Running Secure Perimeter exposure scan 2018-05-24 12:00:00 --------#

RESULTS:

sp-gateway-af6053a9:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   FAIL
			host = 10.73.84.198, ports = [179, 22]
		10.73.63.8/29:     PASS
		10.73.72.128/26:   PASS
sp-gateway-8a9031ab:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   PASS
		10.73.63.8/29:     PASS
```
{: screen}

Il formato del report è il seguente:
```
<gateway name>:
  <vlan name>:
    <subnet>:    PASS
    <subnet>:    FAIL
      host = <ip address>, ports = [<exposed port>, <exposed port>, ...]
```
{: screen}

**ibmcloud-secure-perimeter-health** determina una sottorete come `PASS` se nessun host nella sottorete era raggiungibile, altrimenti esegue la restituzione con `FAIL` ed elenca gli host che erano raggiungibili, insieme alle porte che erano accessibili.

## Guida di riferimento agli argomenti del contenitore
{: #reference_container_arg}

|Chiave|Descrizione|Impostazione predefinita
|---|-------------|---|
|scan|Il tipo di scansione dell'esposizione ("public" o "private") |Nessuna (viene eseguita la scansione di entrambe)
|exclude-vlan-ids|L'elenco di VLAN in base agli ID per cui evitare la scansione|Nessuna
|poll-interval|Imposta il numero di secondi fino alla prossima scansione|0 (eseguire una sola volta)
|allowed-public-ports|L'elenco di IP utente da includere nella whitelist nella scansione.|80, 443, 9000-9999
{: caption="Tabella 1. Argomenti del contenitore" caption-side="top"}

## Riferimenti alla variabile di ambiente
{: #reference_env_var}

|Chiave|Descrizione|
|---|-------------|
|SL_USER|Il tuo nome utente dell'infrastruttura IBM Cloud|
|SL_APIKEY|La tua chiave API dell'infrastruttura IBM Cloud|
{: caption="Tabella 2. Variabili di ambiente" caption-side="top"}
