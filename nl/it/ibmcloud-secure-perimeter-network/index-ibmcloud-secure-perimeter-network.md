---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-04"

keywords: IBM Cloud Container Registry, ibmcloud-secure-perimeter-network, container image, network, Secure Perimeter, public image

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

# Introduzione all'immagine `ibmcloud-secure-perimeter-network`
{: #ibmcloud-secure-perimeter-network}

L'immagine `ibmcloud-secure-perimeter-network` contiene gli strumenti per automatizzare la configurazione delle applicazioni di router virtuale Vyatta in un Secure Perimeter.
{:shortdesc}

Puoi accedere alle immagini fornite da {{site.data.keyword.IBM}} utilizzando la riga di comando, consulta [Immagini pubbliche IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Come funziona
{: #spn_how-it-works}

Con `ibmcloud-secure-perimeter-network`, puoi automatizzare la configurazione della tua applicazione di router virtuale di Vyatta del Secure Perimeter.

Per ulteriori informazioni sul Secure Perimeter, consulta questi articoli di blog:

- [Set up a Secure Perimeter in IBM Cloud ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/).
- [Set up an automated Secure Perimeter in IBM Cloud ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

Puoi utilizzare l'immagine `ibmcloud-secure-perimeter-network` in due modi:

- Utilizza `ibmcloud-secure-perimeter-network` come un contenitore Docker per inizializzare la configurazione delle regole del firewall del Secure Perimeter.
- Utilizza `ibmcloud-secure-perimeter-network` come pod su un cluster Kubernetes per eseguire il polling dell'account dell'infrastruttura IBM Cloud per ottenere le nuove sottoreti create sulle VLAN del Secure Perimeter Segment e aggiungerle alla configurazione del firewall Vyatta.

## Elementi inclusi
{: #spn_whats_included}

L'immagine `ibmcloud-secure-perimeter-network` fornisce i seguenti pacchetti software.
{:shortdesc}

- Alpine Linux
- Runtime Python
- SoftLayer Python Client
- Ansible

## Prerequisiti
{: #spn_prerequisites}

- Vyatta e VLAN ordinate dal portale dell'infrastruttura IBM Cloud e VLAN associate a Vyatta.
- La distribuzione del Secure Perimeter automatizzata precarica il Vyatta con le chiavi SSH utilizzate da `ibmcloud-secure-perimeter-network` per accedere al gateway. Le chiavi SSH devono essere caricate manualmente o tramite il processo di installazione del Secure Perimeter. Per ulteriori informazioni, vedi [Set up an automated Secure Perimeter in IBM Cloud ![Icona link esterno](../../../icons/launch-glyph.svg "Icona link esterno")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

## Esegui il provisioning di un cluster Kubernetes in un Secure Perimeter utilizzando {{site.data.keyword.containerlong_notm}}
{: #spn_provision_cluster}

1. Esegui il provisioning del tuo cluster Kubernetes dalla sezione **Contenitori** nel catalogo IBM Cloud.
2. Fai clic su **Crea**.
3. Seleziona le VLAN pubbliche e private del Secure Perimeter Segment dall'elenco di VLAN.
4. Immetti tutti gli altri dettagli come richiesto.
5. Fai clic su **Crea cluster**.

Per accedere al tuo cluster dopo che è stato distribuito, vedi [{{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-getting-started#getting-started).

## Esegui la configurazione iniziale del Vyatta del tuo Secure Perimeter
{: #spn_initial_setup}

1. Crea un file denominato `config.json`. Questo file contiene i parametri di base richiesti da `ibmcloud-secure-perimeter-network` per accedere a Vyatta.

   ```
   {
     "slid": "XXXX",
    "apikey": "XXXX",
    "region": "XXXX",
    "inf_name_private": "dp0bond0",
    "inf_name_public": "dp0bond1",
    "gatewayid": "XXXX",
    "vlans": [
      {
         "type": "XXXX",
        "vlan_num": XXXX,
        "vlanid": XXXX
      },
       ...
     ],
    "vyatta_gateway_vip": "X.X.X.X",
    "vyatta_primary": {
       "private_ip": "X.X.X.X",
      "public_ip": "X.X.X.X"
    },
    "vyatta_secondary": {
       "private_ip": "X.X.X.X",
      "public_ip": "X.X.X.X"
    }
   }
   ```
   {: codeblock}

   Per ulteriori informazioni su come compilare il file `config.json`, vedi la [tabella di riferimento `config.json`](#spn_reference_config_json). Questo file può essere utilizzato anche nel processo di [configurazione di `ibmcloud-secure-perimeter-network` come un pod Kubernetes](#spn_setup).

2. Esegui `ibmcloud-secure-perimeter-network` come un contenitore Docker per avviare la configurazione iniziale.

   ```
   docker run icr.io/ibm/ibmcloud-secure-perimeter-network:1.0.0 python config-secure-perimeter.py -v /path/to/current/dir:/opt/secure-perimeter
   ```
   {: pre}

   Questo crea un file `state.json` nella tua directory di lavoro. Questo file viene utilizzato per [configurare `ibmcloud-secure-perimeter-network` come pod Kubernetes](#spn_setup).

## Configura un pod Kubernetes nel tuo Secure Perimeter
{: #spn_setup}

Se vuoi che l'immagine `ibmcloud-secure-perimeter-network` gestisca le sottoreti nel tuo Secure Perimeter, puoi eseguirla come un processo di lunga durata utilizzando un pod Kubernetes. Per configurare il pod per Vyatta, devi copiare diversi file e cartelle di configurazione dall'immagine `ibmcloud-secure-perimeter-network` al pod:

1. Crea un file denominato `pvc.yaml`. Questo file di configurazione crea un'attestazione del volume persistente (PVC) che puoi montare al tuo pod come un volume.

   ```
   apiVersion: v1
  kind: PersistentVolumeClaim
  metadata:
    name: network-pvc
    annotations:
      volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
  spec:
    accessModes:
      - ReadWriteMany
    resources:
      requests:
        storage: 20Gi
   ```
   {: codeblock}

2. Crea la PVC.

   ```
   kubectl apply -f restore-pvc.yaml
   ```
   {: pre}

3. Crea un file denominato `network-pod.yaml`. Questo file di configurazione distribuisce l'immagine `ibmcloud-secure-perimeter-network` come un pod nel tuo cluster Kubernetes e monta la tua attestazione di volume persistente come un volume.

   ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: network-pod
     labels:
       app: network-pod
   spec:
     template:
       spec:
         containers:
         - name: network-pod
           image: icr.io/ibm/ibmcloud-secure-perimeter-network:1.0.0
           volumeMounts:
           - name: network-vol
             mountPath: /opt/secure-perimeter
         volumes:
         - name: network-vol
           PersistentVolumeClaim:
             claimName: network-pvc
   ```
   {: codeblock}

4. Crea un file denominato `rules.conf`. Questo file di configurazione indica a `ibmcloud-secure-perimeter-network` quali sottoreti e porte esterne da Internet pubblica inserire nella whitelist all'interno del Secure Perimeter.

   ```
   {
       "external_subnets": [
         "X.X.X.X/X",
        "X.X.X.X/X"
       ],
      "external_ports": [
         "XX",
        "XX"
       ],
      "userips": [
         "X.X.X.X",
        "X.X.X.X"
      ]
   }
   ```
   {: codeblock}

5. Copia i file nel pod `ibmcloud-secure-perimeter-network`.

   ```
   kubectl cp keys network-pod:/opt/secure-perimeter/
  kubectl cp state.json network-pod:/opt/secure-perimeter/state.json
  kubectl cp config.json network-pod:/opt/secure-perimeter/config.json
  kubectl cp rules.conf network-pod:/opt/secure-perimeter/rules.conf
   ```
   {: pre}

   La directory `keys` contiene le chiavi SSH richieste da `ibmcloud-secure-perimeter-network` per accedere a Vyatta. Per ulteriori informazioni sulle chiavi SSH, vedi la [sezione dei prerequisiti](#spn_prerequisites).

## Guida di riferimento `config.json`
{: #spn_reference_config_json}

|Chiave|Descrizione
|---|-------------|---|
|`slid`|Il tuo nome utente dell'infrastruttura IBM Cloud
|`apikey`|La tua chiave API dell'infrastruttura IBM Cloud
|`region`|La regione IBM Cloud dove viene eseguita la distribuzione del Vyatta
|`inf_name_private`|Il nome dell'interfaccia privata Vyatta
|`inf_name_public`|Il nome dell'interfaccia pubblica Vyatta
|`gatewayid`|L'ID gateway di Vyatta
|`vlans`|L'elenco delle VLAN di Secure Perimeter Segment contenente il tipo, il numero della VLAN e l'ID della VLAN
|`vyatta_gateway_vip`|Il VIP del gateway
|`vyatta_primary`|L'oggetto che contiene l'IP privato e pubblico del membro Vyatta primario
|`vyatta_secondary`|L'oggetto che contiene l'IP privato e pubblico del membro Vyatta secondario
{: caption="Tabella 1. <codeconfig.json</code>" caption-side="top"}>

## Guida di riferimento `rules.conf`
{: #spn_reference_rules_conf}

|Chiave|Descrizione
|---|-------------|---|
|`external_subnets`|L'elenco di sottoreti su Internet pubblica che Secure Perimeter può utilizzare
|`external_ports`|L'elenco di porte che Secure Perimeter può utilizzare
|`userips`|L'elenco di IP utente da includere nella whitelist per il Secure Perimeter
{: caption="Tabella 2. <coderules.conf</code>" caption-side="top"}>
