---

copyright:
  years: 2018
lastupdated: "2018-08-21"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Einführung in das Image `ibmcloud-secure-perimeter-network`
{: #ibmcloud-secure-perimeter-network}

Das Image `ibmcloud-secure-perimeter-network` enthält Tools für die Automatisierung der Konfiguration von Vyatta-Appliances für virtuelle Router in einem Secure Perimeter.
{:shortdesc}

## Funktionsweise
{: #how-it-works}

Mit `ibmcloud-secure-perimeter-network` können Sie die Konfiguration der Vyatta-Appliance für virtuelle Router in Ihrem Secure Perimeter automatisieren.

Weitere Informationen zu Secure Perimeter finden Sie in den folgenden Blog-Artikeln: 
  * [Secure Perimeter in IBM Cloud einrichten](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/)
  * [Automatisierten Secure Perimeter in IBM Cloud einrichten](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/)

Für die Verwendung des Images `ibmcloud-secure-perimeter-network` stehen zwei Möglichkeiten zur Verfügung:
-  Verwendung von `ibmcloud-secure-perimeter-network` als Docker-Container zur Initialisierung der Konfiguration von Secure Perimeter-Firewallregeln.
-  Verwendung von `ibmcloud-secure-perimeter-network` als Pod in einem Kubernetes-Cluster, um das IBM Cloud Infrastructure-Konto nach neuen Teilnetzen abzufragen, die in den Secure Perimeter Segment-VLANs erstellt wurden, und diese zur Vyatta-Firewallkonfiguration hinzuzufügen.

## Enthaltene Elemente
{: #whats_included}

Das `ibmcloud-secure-perimeter-network`-Image bietet die folgenden Softwarepakete.
{:shortdesc}

-   Alpine Linux
-   Python-Laufzeit
-   SoftLayer-Python-Client
-   Ansible

## Einführung
{: #how_to_get_started}

Die folgenden Aufgaben beschreiben die Verwendung von `ibmcloud-secure-perimeter-network`:

1.  [Bereitstellen eines Kubernetes-Clusters in einem Secure Perimeter mit {{site.data.keyword.containerlong}}](#provision_cluster)
2.  [Ausführen der Erstkonfiguration von Vyatta für Secure Perimeter](#initial_setup)
3.  [Einrichten als Kubernetes-Pod im Secure Perimeter](#setup)
4.  [Referenzinformationen zu 'config.json'](#reference_config_json)
5.  [Referenzinformationen zu 'rules.conf'](#reference_rules_conf)

## Bereitstellen eines Kubernetes-Clusters in einem Secure Perimeter mit {{site.data.keyword.containerlong_notm}}
{: #provision_cluster}

1.  Stellen Sie den Kubernetes-Cluster im Abschnitt **Container** im IBM Cloud-Katalog bereit.
2.  Klicken Sie auf **Erstellen**.
3.  Wählen Sie die öffentlichen und privaten Secure Perimeter Segment-VLANs in den VLAN-Dropdown-Menüs aus.
4.  Geben Sie alle weiteren erforderlichen Details ein. 
5.  Klicken Sie auf **Cluster erstellen**. 

Informationen zum Einrichten des Zugriffs auf den Cluster nach seiner Bereitstellung finden Sie in der [{{site.data.keyword.containerlong}}](/docs/containers/container_index.html#container_index)-Dokumentation. 

## Ausführen der Erstkonfiguration von Vyatta für Secure Perimeter
{: #initial_setup}

1. Erstellen Sie eine Datei mit dem Namen _config.json_. Diese Datei enthält die Basisparameter, die `ibmcloud-secure-perimeter-network` für den Zugriff auf Vyatta benötigt.

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

  Die [Referenztabelle zu 'config.json'](#reference_config_json) enthält Details zum Füllen von _config.json_. Diese Datei kann auch beim Prozess für das [Einrichten von `ibmcloud-secure-perimeter-network` als Kubernetes-Pod](#setup) verwendet werden.

2. Führen Sie `ibmcloud-secure-perimeter-network` als Docker-Container aus, um die Erstkonfiguration zu starten.

  ```
  docker run registry.bluemix.net/ibm/ibmcloud-secure-perimeter-network:1.0.0 python config-secure-perimeter.py -v /path/to/current/dir:/opt/secure-perimeter
  ```
  {: pre}

  Hierdurch wird die Datei _state.json_ im Arbeitsverzeichnis erstellt. Diese Datei wird beim [Einrichten von `ibmcloud-secure-perimeter-network` als Kubernetes-Pod](#setup) verwendet.

## Einrichten als Kubernetes-Pod im Secure Perimeter
{: #setup}

Damit das Image `ibmcloud-secure-perimeter-network` zur Verwaltung von Teilnetzen in Ihrem Secure Perimeter verwendet werden kann, können Sie es als Prozess mit langer Laufzeit mithilfe eines Kubernetes-Pods ausführen. Das Image `ibmcloud-secure-perimeter-network` verfügt über eine Reihe von Konfigurationsdateien und Ordnern, die in den Pod kopiert werden müssen, um die Konfiguration für Vyatta zu ermöglichen: 

1. Erstellen Sie eine Datei mit dem Namen _pvc.yaml_. Die Konfigurationsdatei erstellt einen Persistent Volume Claim (PVC), den Sie als Datenträger an den Pod anhängen können.

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

2. Erstellen Sie den PVC.

    ```
    kubectl apply -f restore-pvc.yaml
    ```
    {: pre}

3. Erstellen Sie eine Datei mit dem Namen _network-pod.yaml_. Mit dieser Konfigurationsdatei wird das Image `ibmcloud-secure-perimeter-network` als Pod im Kubernetes-Cluster bereitgestellt und der PVC (Persistent Volume Claim) wird als Datenträger angehängt.

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

4. Erstellen Sie eine Datei mit dem Namen _rules.conf_. Diese Konfigurationsdatei liefert `ibmcloud-secure-perimeter-network` Informationen darüber, welche externen Teilnetze und Ports des öffentlichen Internets in eine Whitelist für Secure Perimeter aufgenommen werden sollen.

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

5. Kopieren Sie die Dateien auf den `ibmcloud-secure-perimeter-network`-Pod.

  ```
  kubectl cp keys network-pod:/opt/secure-perimeter/
  kubectl cp state.json network-pod:/opt/secure-perimeter/state.json
  kubectl cp config.json network-pod:/opt/secure-perimeter/config.json
  kubectl cp rules.conf network-pod:/opt/secure-perimeter/rules.conf
  ```
  {: pre}

  Das Verzeichnis _keys_ enthält die SSH-Schlüssel, die `ibmcloud-secure-perimeter-network` für den Zugriff auf Vyatta benötigt. Weitere Informationen zu den SSH-Schlüsseln finden Sie im Abschnitt [Voraussetzungen](#prerequisites).

## Referenzinformationen zu 'config.json'
{: #reference_config_json}

|Schlüssel|Beschreibung
|---|-------------|---|
|slid|Ihr Benutzername für IBM Cloud Infrastructure
|apikey|Ihr API-Schlüssel für IBM Cloud Infrastructure
|region|IBM Cloud-Region, in der Vyatta bereitgestellt wird
|inf_name_private|Name der privaten Vyatta-Schnittstelle
|inf_name_public|Name der öffentlichen Vyatta-Schnittstelle
|gatewayid|Vyatta-Gateway-ID
|vlans|Liste der Secure Perimeter Segment-VLANs mit Typ, VLAN-Nummer und VLAN-ID
|vyatta_gateway_vip|VIP des Gateways
|vyatta_primary|Objekt mit der privaten und öffentlichen IP des primären Vyatta-Mitglieds
|vyatta_secondary|Objekt mit der privaten und öffentlichen IP des sekundären Vyatta-Mitglieds
{: caption="Tabelle 1. config.json" caption-side="top"}

## Referenzinformationen zu 'rules.conf'
{: #reference_rules_conf}

|Schlüssel|Beschreibung
|---|-------------|---|
|external_subnets|Liste mit Teilnetzen im öffentlichen Internet, für die Secure Perimeter zugänglich gemacht werden soll
|external_ports|Liste mit Ports, für die Secure Perimeter zugänglich gemacht werden soll
|userips|Liste mit Benutzer-IPs, die in eine Whitelist für Secure Perimeter aufgenommen werden sollen
{: caption="Tabelle 2. rules.conf" caption-side="top"}

## Voraussetzungen
{: #prerequisites}

-   Vyatta und VLANs, die über das IBM Cloud Infrastructure-Portal bestellt wurden. Die VLANs müssen Vyatta zugeordnet worden sein.
-   Die automatisierte Secure Perimeter-Bereitstellung lädt Vyatta vorab mit den SSH-Schlüsseln, die `ibmcloud-secure-perimeter-network` für den Zugriff auf das Gateway verwendet. Die SSH-Schlüssel müssen entweder manuell oder über den Secure Perimeter-Installationsprozess geladen werden. Weitere Informationen finden Sie im Artikel [Automatisierten Secure Perimeter in IBM Cloud einrichten](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).
