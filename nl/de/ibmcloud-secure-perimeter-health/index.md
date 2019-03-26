---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-21"

keywords: IBM Cloud Container Registry, ibmcloud-secure-perimeter-health, container image, health, Secure Perimeter, scan, public image

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

# Einführung zum Image `ibmcloud-secure-perimeter-health`
{: #ibmcloud-secure-perimeter-health}

Das Image `ibmcloud-secure-perimeter-health` enthält ein Tool für das Scannen nach Sicherheitslücken in einem Secure Perimeter in {{site.data.keyword.cloud}}.
{:shortdesc}

Sie können auf die von {{site.data.keyword.IBM_notm}} bereitgestellten Images über die Befehlszeile zugreifen. Informationen hierzu finden Sie in [öffentliche IBM Images](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Funktionsweise
{: #sph_how-it-works}

Damit sichergestellt wird, dass Secure Perimeter korrekt funktioniert, kann `ibmcloud-secure-perimeter-health` öffentliche oder private Netze in Ihrem {{site.data.keyword.cloud_notm}}-Infrastrukturkonto scannen und potenzielle Sicherheitslücken melden. Für die Verwendung des Images **ibmcloud-secure-perimeter-health** stehen zwei Möglichkeiten zur Verfügung:

- Verwendung von `ibmcloud-secure-perimeter-health` als Pod in einem Kubernetes-Cluster in Secure Perimeter zum Scannen nach Sicherheitslücken in privaten Netzen.
- Verwendung von `ibmcloud-secure-perimeter-health` als eigenständiger Docker-Container auf Ihrer Workstation zum Scannen nach Sicherheitslücken in öffentlichen Netzen.

Weitere Informationen zu Secure Perimeter finden Sie in den folgenden Blog-Artikeln:

- [Secure Perimeter in {{site.data.keyword.cloud_notm}} einrichten ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/).
- [Automatisierten Secure Perimeter in {{site.data.keyword.cloud_notm}} einrichten ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

Nach dem Scannen generiert das Image `ibmcloud-secure-perimeter-health` einen Bericht, der Informationen dazu enthält, welche Netze vom Secure Perimeter Segment aus erreichbar waren. Jeder Bericht enthält Details zum Namen des Netzgateways, zum VLAN, den zugehörigen Teilnetzen sowie gegebenenfalls zu Hosts, die Sicherheitsverstöße verursachen. Im Folgenden ist ein Beispielbericht eines Benutzers dargestellt, der nach Sicherheitslücken in einem privaten Netz gescannt hat:

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

## Enthaltene Elemente
{: #sph_whats_included}

Das `ibmcloud-secure-perimeter-health`-Image bietet die folgenden Softwarepakete.
{:shortdesc}

- Alpine Linux
- Python-Laufzeit
- SoftLayer-Python-Client
- Nmap-Portscanner

## Bereitstellen eines Kubernetes-Clusters in einem Secure Perimeter mit {{site.data.keyword.containerlong_notm}}
{: #sph_provision_cluster}

1. Stellen Sie den Kubernetes-Cluster im Abschnitt **Container** im {{site.data.keyword.cloud_notm}}-Katalog bereit.
2. Klicken Sie auf **Erstellen**.
3. Wählen Sie die öffentlichen und privaten Secure Perimeter Segment-VLANs in den VLAN-Dropdown-Menüs aus.
4. Geben Sie alle weiteren erforderlichen Details ein.
5. Klicken Sie auf **Cluster erstellen**.

Informationen zum Einrichten des Zugriffs auf den Cluster nach seiner Bereitstellung finden Sie in der [{{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-container_index#container_index)-Dokumentation.

## Scannen von privaten Netzen in einem Secure Perimeter
{: #sph_private_networks}

Erstellen Sie einen Container-Pod, der auf dem Image `ibmcloud-secure-perimeter-health` basiert, und richten Sie einen Routinescan ein.

**Vorbereitende Schritte**

- Installieren Sie die erforderlichen [Befehlszeilenschnittstellen (CLIs)](/docs/containers?topic=containers-cs_cli_install#cs_cli_install).
- [Geben Sie Ihren Cluster als Ziel in der Befehlszeilenschnittstelle an.](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)

1. Erstellen Sie eine Konfigurationsdatei mit dem Namen _health-pod.yaml_. Mit dieser Datei wird eine Hochverfügbarkeitsbereitstellung des Container-Pods erstellt.

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
            - <VLAN-ID des privaten Secure Perimeter-Segments>
            - --poll-interval
            - 1800
            env:
            - name: SL_USER
              value: <Benutzername für die IBM Cloud-Infrastruktur>
            - name: SL_APIKEY
              value: <API-Schlüssel für die IBM Cloud-Infrastruktur>
    ```
    {: codeblock}
    
2. Erstellen Sie die Bereitstellung.

    ```
    kubectl apply -f health-pod.yaml
    ```
    {: pre}

3. Stellen Sie sicher, dass der Pod ausgeführt wird.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    health-pod-<random-id>                  1/1       Running   0          1hr
    ```
    {: screen}

## Scannen von öffentlichen Netzen außerhalb eines Secure Perimeter
{: #sph_public_networks}

Erstellen Sie einen Docker-Container, der auf dem Image `ibmcloud-secure-perimeter-health` basiert, und scannen Sie öffentliche Netze.

**Vorbereitende Schritte**

- Installieren Sie Docker.

1. Erstellen Sie wie folgt einen Docker-Container für die eigene Workstation:

    ```
    docker run -it -e SL_USER='$SL_USER' -e SL_APIKEY='$SL_APIKEY' registry.bluemix.net/ibm/ibmcloud-secure-perimeter-health:1.0.0 /usr/local/bin/python run.py --scan public --allowed-public-ports 80 443 9000-9999
    ```
    {: pre}

2. Nachdem der Container einen Bericht generiert hat, lesen Sie die Informationen im Abschnitt [Scanergebnisse analysieren](#sph_scan_results), um die Ergebnisse zu interpretieren.

## Scanergebnisse analysieren
{: #sph_scan_results}

`ibmcloud-secure-perimeter-health` generiert einen formatierten Bericht zum Secure Perimeter-Funktionsstatus:

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

Das Format des Berichts ist im Folgenden dargestellt:

```
<Gateway-Name>:
  <VLAN-Name>:
    <Teilnetz>:    PASS
    <Teilnetz>:    FAIL
      host = <IP-Adresse>, ports = [<zugänglicher Port>, <zugänglicher Port>, ...]
```
{: screen}

Das Image `ibmcloud-secure-perimeter-health` gibt für ein Teilnetz den Status `PASS` ('Bestanden') zurück, wenn kein Host im Teilnetz erreichbar war; andernfalls wird der Status `FAIL` ('Nicht bestanden') zurückgegeben und eine Liste der erreichbaren Host sowie der zugänglichen Ports wird ausgegeben.

## Referenzinformationen zu Containerargumenten
{: #sph_reference_container_arg}

|Schlüssel|Beschreibung|Standardwert
|---|-------------|---|
|scan|Typ des Sicherheitslückenscans ("öffentlich" oder "privat") |Ohne (beide Scantypen)
|exclude-vlan-ids|Liste der VLANs nach IDs ohne Scannen|Ohne
|poll-interval|Sekunden bis zum nächsten Scan festlegen|0 (einmalige Ausführung)
|allowed-public-ports|Liste mit Ports, die in eine Whitelist für den Scan aufgenommen werden sollen|80, 443, 9000-9999
{: caption="Tabelle 1. Containerargumente" caption-side="top"}

## Referenzinformationen zu Umgebungsvariablen
{: #sph_reference_env_var}

|Schlüssel|Beschreibung|
|---|-------------|
|SL_USER|Ihr Benutzername für die IBM Cloud-Infrastruktur|
|SL_APIKEY|Ihr API-Schlüssel für die IBM Cloud-Infrastruktur|
{: caption="Tabelle 2. Umgebungsvariablen" caption-side="top"}
