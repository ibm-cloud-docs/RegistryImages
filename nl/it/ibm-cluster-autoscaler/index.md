---

copyright:
  years: 2018
lastupdated: "2018-07-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Introduzione all'immagine iccs-cluster-autoscaler (alpha)
{: #ibm-cluster-autoscaler}

L'immagine iccs-cluster-autoscaler contiene i pacchetti pre-installati necessari per ridimensionare automaticamente i nodi di lavoro nel tuo cluster {{site.data.keyword.containerlong}}. L'immagine iccs-cluster-autoscaler si basa su [Kubernetes Cluster-Autoscaler project](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler).
{:shortdesc}

La versione alpha dell'autoscaler al momento supporta solo la disponibilità a singola zona e non è disponibile per i nodi di lavoro su server bare metal fisici.
{: tip}

## Come funziona
{: #how-it-works}

Con l'immagine iccs-cluster-autoscaler puoi ridimensionare i nodi di lavoro all'interno del tuo cluster Kubernetes, in qualsiasi gruppo di ridimensionamento automatico specificato quando utilizzi {{site.data.keyword.containerlong}}. L'autoscaler viene eseguito come una distribuzione nello spazio dei nomi `kube-system` del tuo cluster ed è un plugin nell'autoscaler del cluster Kubernetes.
{: shortdesc}

L'immagine iccs-cluster-autoscaler monitora i pod nel tuo cluster visualizzando il server API Kubernetes e può automaticamente ridimensionare il tuo cluster.

<dl>
  <dt>Aumento </dt>
    <dd>Periodicamente, l'immagine iccs-cluster-autoscaler controlla tutti i pod che non sono pianificabili. Un pod non è pianificabile quando lo scheduler Kubernetes non riesce a trovare un nodo in cui eseguire il pod. Quando l'autoscaler rileva dei pod che sono etichettati come non pianificabili, aggiunge un nodo di lavoro al tuo cluster.</dd>
  <dt>Riduzione</dt>
    <dd>Per impostazione predefinita, l'immagine iccs-cluster-autoscaler controlla i nodi non necessari ogni 10 secondi. Se un nodo non è necessario per più di 10 minuti, viene eliminato.</br> I nodi sono considerati non necessari quando:</br>
    <ul><li>Il riepilogo delle richieste di CPU e memoria di tutti i pod in esecuzione su questo nodo è inferiore al 50% della quantità assegnata ad esso.</li>
    <li>Tutti i pod sul nodo possono essere spostati ad altri nodi. I pod che vengono eseguiti su tutti i nodi per impostazione predefinita, come i pod manifest-run o quelli creati dalle serie daemon, sono esclusi da questo test.</li>
    <li>Non è presente un'annotazione disabilitata di riduzione. </li></ul></dd>
</dl>

Ma cosa significa tutto questo?

![Ridimensionamento automatico di un cluster](/images/autoscaler.png)

Quando crei il tuo cluster Kubernetes in IBM Cloud, specifichi le tue preferenze hardware. L'autoscaler crea i gruppi in base ai tipi di macchina per garantirti di stare utilizzando la quantità corretta di risorse. Nell'immagine, puoi visualizzare una politica di ridimensionamento automatico di esempio. Nella configurazione, vengono specificati il tipo di macchina e il numero di nodi minimo e massimo. Se desideri che l'autoscaler ignori una serie di nodi e non li ridimensioni, non includerli nella tua configurazione. Nell'immagine, il cluster viene aumentato per gestire le modifiche nelle risorse. Il nodo b2c.16x64 non è stato ridimensionato perché non è stata impostata alcuna politica nel file di configurazione.

## Restrizioni di utilizzo
{: #usage}
Questa immagine può essere utilizzata in {{site.data.keyword.containershort}} con l'API Kubernetes versione 1.7 o successiva.

## Prerequisiti
{: #prerequisites}

Esistono alcuni prerequisiti che devi completare prima di utilizzare l'immagine iccs-cluster-autoscaler.

* Devi disporre di un cluster standard per utilizzare il plugin dell'autoscaler. Il ridimensionamento automatico dei gruppi viene eseguito per i nodi di lavoro che hanno gli stessi [tipi di macchina](/docs/containers/cs_cli_reference.html#cs_machine_types). Per verificare di soddisfare i requisiti, immetti il seguente comando e controlla che il tuo **Tipo di macchina** non sia _gratuito_. Controlla che la tua **Versione** sia `versione 1.7` o successiva </br>
    ```
    ibmcloud ks workers <cluster-name>
    ```
    {: pre}
    Output di esempio: </br>
    ```
    OK
    ID                                                   Public IP        Private IP     Machine Type   State    Status   Version
    stage-dal06-crffef8b930b214442802bdcaa551b5ec0-w1    169.54.180.171   10.146.32.7    u2c.2x4        normal   Ready    1.8.2_1504
    ```
    {: screen}
* Devi installare i seguenti plugin e CLI. Per assistenza nell'installazione dei requisiti e nella creazione del tuo primo cluster, [consulta questa esercitazione nella documentazione](/docs/containers/cs_tutorials.html#cs_cluster_tutorial).
    <ul><li>CLI {{site.data.keyword.Bluemix_notm}} </li><li>CLI Kubectl</li><li>CLI Docker</li><li>Plugin {{site.data.keyword.containerlong_notm}} </li><li>Plugin {{site.data.keyword.registrylong_notm}} </li></ul>
* Per ridimensionare i nodi di lavoro al tuo posto, l'autoscaler deve conoscere i tuoi dettagli Identity and Access Management. Le credenziali sono disponibili come un segreto in un cluster a pagamento. Per verificare che il segreto sia disponibile, immetti il seguente comando. Il comando restituisce un segreto.
    ```
		$kubectl get secrets -n kube-system | grep storage-secret-store
    ```
    {: pre}

## Installazione e configurazione dell'autoscaler
{: #install-configure}

Per installare l'autoscaler, devi prima installare e avviare Helm nel tuo cluster. Dopo che l'autoscaler è in esecuzione, conserva automaticamente le risorse e ridimensiona i tuoi cluster.

1. Accedi a {{site.data.keyword.containershort_notm}} e seleziona il tuo cluster.
    ```
    ibmcloud ks cluster-config <cluster_name>`.
    ```
    {: pre}

2. [Installa helm e avvialo nel tuo cluster](https://docs.helm.sh/using_helm/#quickstart).

3. Utilizzando il template fornito, crea un file di configurazione.

   ```
   cp incubator/helm/autoscaler/config.yaml.template config.yaml
   ```
   {: pre}

    In modo che il ridimensionamento dei gruppi funzioni, devono essere specificati tutti i tipi di macchina nel tuo file di configurazione, anche se non fornisci un intervallo massimo e minimo.
    {: tip}

  <table summary="La prima riga nella tabella si estende su entrambe le colonne. Le rimanenti righe devono essere lette da sinistra a destra, con il parametro nella colonna uno, la descrizione corrispondente nella colonna due e il valore predefinito nella colonna tre.">
  <caption>Parametri configurabili e loro valore predefinito</caption>
    <thead>
      <th colspan=3><img src="../images/idea.png" alt="Icona idea"/>Parametri configurabili e loro valore predefinito </th>
    </thead>
    <tbody>
      <tr>
        <td><code>autoscalingGroups.name</code></td>
        <td> Il tipo di macchina che vuoi ridimensionare. </td>
        <td> Nessun valore predefinito, specificato dall'utente. Per trovare il tuo tipo di macchina esegui <code>ibmcloud ks workers <cluster-name></code></td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.minSize</code></td>
        <td> Il numero minimo di nodi di lavoro che il tuo cluster dovrebbe ridimensionare. </td>
        <td> Nessun valore predefinito, specificato dall'utente. </td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.maxSize</code></td>
        <td> Il numero massimo di nodi di lavoro che il tuo cluster dovrebbe ridimensionare. </td>
        <td> Nessun valore predefinito, specificato dall'utente. </td>
      </tr>
      <tr>
        <td><code>api_route</code></td>
        <td> La rotta all'API {{site.data.keyword.containershort_notm}} della regione in cui è in esecuzione il tuo cluster. </td>
        <td> https://containers.bluemix.net/swagger-api/ </td>
      </tr>
      <tr>
        <td><code>resources.limits.cpu</code></td>
        <td> La quantità di CPU a cui è limitato il pod dell'autoscaler. </td>
        <td> 300m </td>
      </tr>
      <tr>
        <td><code>resources.limits.memory</code></td>
        <td> La quantità di memoria a cui è limitato il pod dell'autoscaler. </td>
        <td> 300Mi </td>
      </tr>
      <tr>
        <td><code>resources.requests.cpu</code></td>
        <td> La quantità di CPU con cui vuoi inizi il pod dell'autoscaler. </td>
        <td> 100m </td>
      </tr>
      <tr>
        <td><code>resources.requests.memory</code></td>
        <td> La quantità di memoria con cui vuoi inizi il pod dell'autoscaler. </td>
        <td> 100Mi </td>
      </tr>
      <tr>
        <td><code>scale_down_delay</code></td>
        <td> La quantità di tempo in cui lo scaler dovrebbe attendere dopo l'emissione di una richiesta di aumento. </td>
        <td> 10m </td>
      </tr>
      <tr>
        <td><code>scale_down_unneeded_time</code></td>
        <td> La quantità di tempo in cui un nodo dovrebbe essere non necessario prima che sia eleggibile per la riduzione. </td>
        <td> 10m </td>
      </tr>
      <tr>
        <td><code>skipNodes.withLocalStorage</code></td>
        <td> Quando impostato su true, i nodi che contengono l'archiviazione locale saranno ignorati e non saranno ridotti. </td>
        <td> true </td>
      </tr>
      <tr>
        <td><code>skipNodes.withSystemPods</code></td>
        <td> Quando impostato su true, i nodi che contengono i pod del sistema kube saranno ignorati e non saranno ridotti. </td>
        <td> true </td>
      </tr>
      <tr>
        <td><code>image.repository</code></td>
        <td> L'immagine Docker dell'autoscaler del cluster. </td>
        <td> registry.bluemix.net/iccs-cluster-autoscaler </td>
      </tr>
      <tr>
        <td><code>image.tag</code></td>
        <td> La versione dell'immagine di cui vuoi eseguire il pull. </td>
        <td> 12 </td>
      </tr>
      <tr>
        <td><code>image.pullPolicy</code></td>
        <td> Specifica quando eseguire il pull dell'immagine Docker. </td>
        <td> Sempre.</td>
      </tr>
    </tbody>
  </table>
  
  L'immagine iccs-cluster-autoscaler supporta vari parametri di configurazione. Per ulteriori informazioni dettagliate su come funziona, consulta [the Kubernetes Autoscaler FAQ](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md).

4. Installa il grafico helm nel tuo cluster nello spazio dei nomi `kube-system`.

  ```
  helm install incubator/helm/autoscaler -f config.yaml --namespace kube-system
  ```
  {: pre}

    Il tuo output dovrebbe essere simile al seguente:
  ```
  NAMESPACE: kube-system
  STATUS: DEPLOYED

  RESOURCES:
  ==> v1/Service
  NAME                CLUSTER-IP    EXTERNAL-IP  PORT(S)   AGE
  cluster-autoscaler  10.10.10.189  <none>       8085/TCP  5s

  ==> v1beta1/Deployment
  NAME                DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
  cluster-autoscaler  1        1        1           0          3s
  ```
  {: screen}

## Verifica dell'installazione

1. Verifica che il tuo cluster contenga il pod dell'autoscaler in esecuzione.

  ```
  kubectl get pods --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    Output di esempio:
  ```
    cluster-autoscaler-4289984493-zldfk        1/1       Running   0          3m
  ```
  {: screen}

2. Verifica che l'autoscaler del cluster è in esecuzione.

  ```
  kubectl get service --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    Output di esempio:
  ```
  cluster-autoscaler       10.10.10.204   <none>         8085/TCP                     4m
  ```
  {: screen}


## Disinstallazione dell'autoscaler

Puoi disinstallare la distribuzione dell'autoscaler del cluster. 

1. Accedi a {{site.data.keyword.Bluemix_notm}} e seleziona il tuo cluster eseguendo `ibmcloud ks cluster-config <cluster_name>`.

2. Ottieni il nome della tua distribuzione.

  ```
  helm ls | grep autoscaler
  <deployment_name>	1       	Mon Dec 18 13:45:09 2017	DEPLOYED	autoscaler-1.0.3	kube-system
  ```
  {: pre}

3. Elimina la distribuzione.

  ```
  helm delete <deployment_name>
  ```
  {: pre}
