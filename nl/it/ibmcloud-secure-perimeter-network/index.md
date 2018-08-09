---

copyright:
  years: 2018
lastupdated: "2018-07-25"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Introduzione all'immagine ibmcloud-secure-perimeter-network
{: #ibmcloud-secure-perimeter-network}

L'immagine **ibmcloud-secure-perimeter-network** contiene gli strumenti per automatizzare la configurazione delle applicazioni di router virtuale Vyatta in un Secure Perimeter.
{:shortdesc}

## Come funziona
{: #how-it-works}

Con **ibmcloud-secure-perimeter-network**, puoi automatizzare la configurazione della tua applicazione di router virtuale di Vyatta del perimetro sicuro.

Ulteriori informazioni sul Secure Perimeter sono disponibili in questi articoli di blog:
  * [Set up a Secure Perimeter in IBM Cloud](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/)
  * [Set up an automated Secure Perimeter in IBM Cloud](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

Puoi utilizzare l'immagine **ibmcloud-secure-perimeter-network** in due modi:
-  Utilizza **ibmcloud-secure-perimeter-network** come un contenitore Docker per inizializzare la configurazione delle regole del firewall del Secure Perimeter.
-  Utilizza **ibmcloud-secure-perimeter-network** come un pod su un cluster Kubernetes per eseguire il polling dell'account dell'infrastruttura IBM Cloud per le nuove sottoreti creati sulle tue VLAN del Secure Perimeter Segment e aggiungerle alla configurazione del firewall Vyatta.

## Elementi inclusi
{: #whats_included}

L'immagine **ibmcloud-secure-perimeter-network** fornisce i seguenti pacchetti software.
{:shortdesc}

-   Alpine Linux
-   Runtime Python
-   SoftLayer Python Client
-   Ansible

## Introduzione
{: #how_to_get_started}

Rivedi le seguenti attività per informazioni sulla modalità di utilizzo di **ibmcloud-secure-perimeter-network**:

1.  [Esegui il provisioning di un cluster Kubernetes in un Secure Perimeter utilizzando {{site.data.keyword.containerlong}}](#provision_cluster)
2.  [Esegui la configurazione iniziale del Vyatta del tuo Secure Perimeter](#initial_setup)
3.  [Esegui la configurazione come un pod Kubernetes nel tuo Secure Perimeter](#setup)
4.  [Guida di riferimento a config.json](#reference_config_json)
5.  [Guida di riferimento a rules.conf](#reference_rules_conf)

## Esegui il provisioning di un cluster Kubernetes in un Secure Perimeter utilizzando {{site.data.keyword.containerlong_notm}}
{: #provision_cluster}

1.  Esegui il provisioning del tuo cluster Kubernetes dalla sezione **Contenitori** nel catalogo IBM Cloud.
2.  Fai clic su Crea.
3.  Seleziona le VLAN private e pubbliche del Secure Perimeter Segment dai menu a discesa delle VLAN.
4.  Compila tutti gli altri dettagli come ritieni opportuno.
5.  Fai clic su Crea cluster.

Rivedi la [documentazione di {{site.data.keyword.containerlong}}](../../../containers/container_index.html#container_index) su come ottenere l'accesso al tuo cluster dopo che è stato distribuito.

## Esegui la configurazione iniziale del Vyatta del tuo Secure Perimeter
{: #initial_setup}

1. Crea un file denominato _config.json_. Il file contiene i parametri di base richiesti da **ibmcloud-secure-perimeter-network** per accedere al Vyatta.

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

  Consulta la [tabella di riferimento di config.json](#reference_config_json) per informazioni dettagliate sulla modalità di compilazione di _config.json_. Questo file può essere utilizzato anche nel processo di [configurazione di **ibmcloud-secure-perimeter-network** come un pod Kubernetes](#setup).

2. Esegui **ibmcloud-secure-perimeter-network** come un contenitore Docker per avviare la configurazione iniziale.

  ```
  docker run registry.bluemix.net/ibm/ibmcloud-secure-perimeter-network:1.0.0 python config-secure-perimeter.py -v /path/to/current/dir:/opt/secure-perimeter
  ```
  {: pre}

  Questo creerà un file _state.json_ nella tua directory di lavoro. Questo file viene utilizzato dalla [configurazione di **ibmcloud-secure-perimeter-network** come un pod Kubernetes](#setup).

## Configurazione di un pod Kubernetes nel tuo Secure Perimeter
{: #setup}

Per fare in modo che l'immagine **ibmcloud-secure-perimeter-network** gestisca le sottoreti nel tuo Secure Perimeter, puoi eseguirla come un processo di lunga durata utilizzando un pod Kubernetes. **ibmcloud-secure-perimeter-network** ha diversi file di configurazione e cartelle che devono essere copiati nel pod per eseguirne la configurazione per il Vyatta:

1. Crea un file denominato _pvc.yaml_. Questo file di configurazione crea un'attestazione del volume persistente (o pvc, persistent volume claim) che puoi montare al tuo pod come un volume.

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

2. Crea il pvc.

    ```
    kubectl apply -f restore-pvc.yaml
    ```
    {: pre}

3. Crea un file denominato _network-pod.yaml_. Questo file di configurazione distribuisce l'immagine **ibmcloud-secure-perimeter-network** come un pod nel tuo cluster Kubernetes e monta la tua attestazione di volume persistente come un volume.

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
          image: registry.bluemix.net/ibm/ibmcloud-secure-perimeter-network:1.0.0
          volumeMounts:
          - name: network-vol
            mountPath: /opt/secure-perimeter
        volumes:
        - name: network-vol
          PersistentVolumeClaim:
            claimName: network-pvc
  ```
  {: codeblock}

4. Crea un file denominato _rules.conf_. Questo file di configurazione indica a **ibmcloud-secure-perimeter-network** quali sottoreti e porte esterne da Internet pubblica inserire nella whitelist all'interno del Secure Perimeter.

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

5. Copia i file nel pod **ibmcloud-secure-perimeter-network**.

  ```
  kubectl cp keys network-pod:/opt/secure-perimeter/
  kubectl cp state.json network-pod:/opt/secure-perimeter/state.json
  kubectl cp config.json network-pod:/opt/secure-perimeter/config.json
  kubectl cp rules.conf network-pod:/opt/secure-perimeter/rules.conf
  ```
  {: pre}

  La directory _keys_ contiene le chiavi SSH necessarie perché **ibmcloud-secure-perimeter-network** acceda al Vyatta. Consulta la [sezione dei prerequisiti](#prerequisites) per ulteriori informazioni sulle chiavi SSH.

## Guida di riferimento a config.json
{: #reference_config_json}

|Chiave|Descrizione
|---|-------------|---|
|slid|Il tuo nome utente dell'infrastruttura IBM Cloud
|apikey|La tua chiave API dell'infrastruttura IBM Cloud
|region|La regione IBM Cloud dove viene eseguita la distribuzione del Vyatta
|inf_name_private|Il nome dell'interfaccia privata Vyatta
|inf_name_public|Il nome dell'interfaccia pubblica Vyatta
|gatewayid|L'ID gateway di Vyatta
|vlans|L'elenco delle VLAN di Secure Perimeter Segment contenente il tipo, il numero della VLAN e l'ID della VLAN
|vyatta_gateway_vip|Il VIP del gateway
|vyatta_primary|L'oggetto che contiene l'IP privato e pubblico del membro Vyatta primario
|vyatta_secondary|L'oggetto che contiene l'IP privato e pubblico del membro Vyatta secondario
{: caption="Tabella 1. config.json" caption-side="top"}

## Guida di riferimento a rules.conf
{: #reference_rules_conf}

|Chiave|Descrizione
|---|-------------|---|
|external_subnets|L'elenco di sottoreti su Internet pubblica a cui esporre il perimetro sicuro
|external_ports|L'elenco di porte a cui esporre il perimetro sicuro
|userips|L'elenco di IP utente da includere nella whitelist per il Secure Perimeter
{: caption="Tabella 2. rules.conf" caption-side="top"}

## Prerequisiti
{: #prerequisites}

-   Vyatta e le VLAN di cui è stata eseguita l'ordinazione dal portale dell'infrastruttura IBM Cloud e le VLAN sono state associate al Vyatta.
-   La distribuzione del Secure Perimeter automatizzata precarica il Vyatta con le chiavi SSH utilizzate da **ibmcloud-secure-perimeter-network** per accedere al gateway. Le chiavi SSH dovranno essere caricate manualmente o tramite il processo di installazione del perimetro sicuro. Per ulteriori informazioni, rivedi l'articolo [Set up an automated Secure Perimeter in IBM Cloud](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).
