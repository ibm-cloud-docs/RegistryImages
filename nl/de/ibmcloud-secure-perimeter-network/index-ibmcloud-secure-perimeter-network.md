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

# Einführung zum Image `ibmcloud-secure-perimeter-network`
{: #ibmcloud-secure-perimeter-network}

Das Image `ibmcloud-secure-perimeter-network` enthält Tools für die Automatisierung der Konfiguration von Vyatta-Appliances für virtuelle Router in einem Secure Perimeter.
{:shortdesc}

Sie können auf die von {{site.data.keyword.IBM}} bereitgestellten Images über die Befehlszeile zugreifen. Informationen hierzu finden Sie in [öffentliche IBM Images](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Funktionsweise
{: #spn_how-it-works}

Mit `ibmcloud-secure-perimeter-network` können Sie die Konfiguration der Vyatta-Appliance für virtuelle Router in Ihrem Secure Perimeter automatisieren.

Weitere Informationen zu Secure Perimeter finden Sie in den folgenden Blog-Artikeln:

- [Secure Perimeter in IBM Cloud einrichten ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/).
- [Automatisierten Secure Perimeter in IBM Cloud einrichten ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

Für die Verwendung des Images `ibmcloud-secure-perimeter-network` stehen zwei Möglichkeiten zur Verfügung:

- Verwendung von `ibmcloud-secure-perimeter-network` als Docker-Container zur Initialisierung der Konfiguration von Secure Perimeter-Firewallregeln.
- Verwendung von `ibmcloud-secure-perimeter-network` als Pod in einem Kubernetes-Cluster, um das IBM Cloud-Infrastrukturkonto nach neuen Teilnetzen abzufragen, die in den Secure Perimeter Segment-VLANs erstellt wurden, und diese zur Vyatta-Firewallkonfiguration hinzuzufügen.

## Enthaltene Elemente
{: #spn_whats_included}

Das `ibmcloud-secure-perimeter-network`-Image bietet die folgenden Softwarepakete.
{:shortdesc}

- Alpine Linux
- Python-Laufzeit
- SoftLayer-Python-Client
- Ansible

## Voraussetzungen
{: #spn_prerequisites}

- Vyatta und VLANs, die über das IBM Cloud-Infrastrukturportal bestellt wurden, sowie VLANs, die Vyatta zugeordnet sein müssen.
- Die automatisierte Secure Perimeter-Bereitstellung lädt Vyatta vorab mit den SSH-Schlüsseln, die `ibmcloud-secure-perimeter-network` für den Zugriff auf das Gateway verwendet. Die SSH-Schlüssel müssen entweder manuell oder über den Secure Perimeter-Installationsprozess geladen werden. Weitere Informationen finden Sie unter [Automatisierten Secure Perimeter in IBM Cloud einrichten ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

## Bereitstellen eines Kubernetes-Clusters in einem Secure Perimeter mit {{site.data.keyword.containerlong_notm}}
{: #spn_provision_cluster}

1. Stellen Sie den Kubernetes-Cluster im Abschnitt **Container** im IBM Cloud-Katalog bereit.
2. Klicken Sie auf **Erstellen**.
3. Wählen Sie die öffentlichen und privaten Secure Perimeter Segment-VLANs in den VLAN-Listen aus.
4. Geben Sie alle weiteren erforderlichen Details ein.
5. Klicken Sie auf **Cluster erstellen**.

Für den Zugriff auf Ihren Cluster nach der Bereitstellung finden Sie Informationen in [{{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-getting-started#getting-started).

## Ausführen der Erstkonfiguration von Vyatta für Secure Perimeter
{: #spn_initial_setup}

1. Erstellen Sie eine Datei mit dem Namen `config.json`. Diese Datei enthält die Basisparameter, die `ibmcloud-secure-perimeter-network` für den Zugriff auf Vyatta benötigt.

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

   Weitere Informationen zum Füllen der Datei `config.json` finden Sie in der Referenztabelle zu [`config.json`](#spn_reference_config_json). Diese Datei kann auch beim Prozess für das [Einrichten von `ibmcloud-secure-perimeter-network` als Kubernetes-Pod](#spn_setup) verwendet werden.

2. Führen Sie `ibmcloud-secure-perimeter-network` als Docker-Container aus, um die Erstkonfiguration zu starten.

   ```
   docker run icr.io/ibm/ibmcloud-secure-perimeter-network:1.0.0 python config-secure-perimeter.py -v /path/to/current/dir:/opt/secure-perimeter
   ```
   {: pre}

   Hierdurch wird die Datei `state.json` im Arbeitsverzeichnis erstellt. Diese Datei wird beim [Einrichten von `ibmcloud-secure-perimeter-network` als Kubernetes-Pod](#spn_setup) verwendet.

## Einrichten eines Kubernetes-Pods im Secure Perimeter
{: #spn_setup}

Wenn Sie das Image `ibmcloud-secure-perimeter-network` zur Verwaltung von Teilnetzen in Ihrem Secure Perimeter verwenden möchten, können Sie es als Prozess mit langer Laufzeit mithilfe eines Kubernetes-Pods ausführen. Zur Konfiguration des Pods für Vyatta müssen Sie eine Reihe von Konfigurationsdateien und Ordnern aus dem Image `ibmcloud-secure-perimeter-network` in den Pod kopieren, um die Konfiguration für Vyatta zu ermöglichen:

1. Erstellen Sie eine Datei mit dem Namen `pvc.yaml`. Die Konfigurationsdatei erstellt einen Persistent Volume Claim (PVC), den Sie als Datenträger an den Pod anhängen können.

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

3. Erstellen Sie eine Datei mit dem Namen `network-pod.yaml`. Mit dieser Konfigurationsdatei wird das Image `ibmcloud-secure-perimeter-network` als Pod im Kubernetes-Cluster bereitgestellt und der PVC (Persistent Volume Claim) wird als Datenträger angehängt.

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

4. Erstellen Sie eine Datei mit dem Namen `rules.conf`. Diese Konfigurationsdatei liefert `ibmcloud-secure-perimeter-network` Informationen darüber, welche externen Teilnetze und Ports des öffentlichen Internets in eine Whitelist für Secure Perimeter aufgenommen werden sollen.

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

   Das Verzeichnis `keys` enthält die SSH-Schlüssel, die `ibmcloud-secure-perimeter-network` für den Zugriff auf Vyatta benötigt. Weitere Informationen zu den SSH-Schlüsseln finden Sie im Abschnitt mit den [Voraussetzungen](#spn_prerequisites).

## Referenzinformationen zu `config.json`
{: #spn_reference_config_json}

|Schlüssel|Beschreibung
|---|-------------|---|
|`slid`|Ihr Benutzername für die IBM Cloud-Infrastruktur
|`apikey`|Ihr API-Schlüssel für die IBM Cloud-Infrastruktur
|`region`|IBM Cloud-Region, in der Vyatta bereitgestellt wird
|`inf_name_private`|Name der privaten Vyatta-Schnittstelle
|`inf_name_public`|Name der öffentlichen Vyatta-Schnittstelle
|`gatewayid`|Vyatta-Gateway-ID
|`vlans`|Liste der Secure Perimeter Segment-VLANs mit Typ, VLAN-Nummer und VLAN-ID
|`vyatta_gateway_vip`|VIP des Gateways
|`vyatta_primary`|Objekt mit der privaten und öffentlichen IP des primären Vyatta-Mitglieds
|`vyatta_secondary`|Objekt mit der privaten und öffentlichen IP des sekundären Vyatta-Mitglieds
{: caption="Tabelle 1. <codeconfig.json</code>" caption-side="top"}>

## Referenzinformationen zu `rules.conf`
{: #spn_reference_rules_conf}

|Schlüssel|Beschreibung
|---|-------------|---|
|`external_subnets`|Liste mit Teilnetzen im öffentlichen Internet, die Secure Perimeter nutzen kann
|`external_ports`|Liste der Ports, die Secure Perimeter nutzen kann
|`userips`|Liste mit Benutzer-IPs, die in eine Whitelist für Secure Perimeter aufgenommen werden sollen
{: caption="Tabelle 2. <coderules.conf</code>" caption-side="top"}>
