---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-26"

keywords: IBM Cloud Container Registry, IBM Liberty, ibmliberty, container image, IBM WebSphere Application Server Liberty, liberty, public image

subcollection: RegistryImages

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:download: .download}

# Einführung zum Image `ibmliberty`
{: #ibmliberty}

Die Images von IBM® WebSphere® Application Server Liberty \(`ibmliberty`\) werden für {{site.data.keyword.containerlong_notm}} zur Verfügung gestellt.
{:shortdesc}

Sie können auf die von {{site.data.keyword.IBM_notm}} bereitgestellten Images über die Befehlszeile zugreifen. Informationen hierzu finden Sie in [öffentliche IBM Images](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Funktionsweise
{: #ibmliberty_how_it_works}

Sie können ein Image `ibmliberty` als übergeordnetes Element verwenden, um Ihr eigenes Image zu erstellen und Ihre eigenen WAR-, EAR- oder OSGi-Anwendungen auf der Basis von Java in einem IBM WebSphere Application Server Liberty-Container bereitzustellen.
{:shortdesc}

## Enthaltene Elemente
{: #ibmliberty_whats_included}

Jedes Liberty-Image bietet die folgenden Softwarepakete.
{:shortdesc}

- IBM WebSphere Application Server for Developers Liberty
- IBM Java Runtime Environment 8.0

Welche Liberty-Features jeweils in einem Image installiert sind, hängt davon ab, welchen Tag Sie auswählen. In der folgenden Tabelle sind die Features aufgeführt, die in den jeweiligen `ibmliberty`-Images enthalten sind. Weitere Informationen zu den einzelnen Features finden Sie in der [Übersicht über die Liberty-Features im IBM Knowledge Center ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/SSAW57_liberty/com.ibm.websphere.wlp.nd.multiplatform.doc/ae/rwlp_feat.html).

|Tag|Beschreibung|
|---|-----------|
|Alle `ibmliberty`-Images|Alle `ibmliberty`-Images enthalten die folgenden Features. <ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|`ibmliberty:latest`|Dieses Image verweist auf das Image `ibmliberty:javaee7`.|
|`ibmliberty:microProfile`|Dieses Image enthält die Features, die die von [MicroProfile ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://microprofile.io) angegebenen Funktionen bereitstellen.|
|`ibmliberty:webProfile7`|Dieses Image enthält alle Features, die für die Konformität mit Java EE7 Web Profile erforderlich sind.|
|`ibmliberty:javaee7`|Dieses Image enthält alle Features von `ibmliberty:webProfile7` sowie die Features, die für die Konformität mit Java EE7 Full Platform erforderlich sind.|
{: caption="Tabelle 1. In den jeweiligen `ibmliberty`-Images enthaltene Features" caption-side="top"}

## Nutzungsbeschränkungen
{: #ibmliberty_usage}

In der folgenden Tabelle sind die Beschränkungen für die kostenlose Nutzung des Images `ibmliberty` in {{site.data.keyword.cloud_notm}} angegeben.
{:shortdesc}

Die Preisstruktur für das Image `ibmliberty` ist unabhängig von der Preisstruktur für die Container, die Sie in {{site.data.keyword.cloud_notm}} verwenden.
{:tip}

|Umgebung|Beschränkungen für die kostenlose Nutzung|
|-----------|-----------------------|
|Entwicklung|**Uneingeschränkte** kostenlose Nutzung des Images `ibmliberty`.|
|Produktion|Die kostenlose Nutzung des Images `ibmliberty` ist auf einen **Java-Heapspeicher von maximal 2 GB** über alle Containerinstanzen begrenzt, auf denen das Image ausgeführt wird. Sie können z. B. 2 x 1 GB oder 4 x 512 MB Liberty-Heapinstanzen kostenlos nutzen.|
{: caption="Tabelle 2. Preisstruktur" caption-side="top"}

Weitere Informationen zum Überwachen der Java-Heapspeicherbelegung Ihrer Containerinstanzen finden Sie unter [Java-Heapspeicherbelegung für einen Container über die Befehlszeilenschnittstelle überwachen](#ibmliberty_monitor_heap).

Lesen Sie zunächst die Nutzungsbedingungen für die von IBM zertifizierten Images im Abschnitt zum [Image 'websphere-liberty' ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://hub.docker.com/_/websphere-liberty/) in Docker Hub.

## Einführung
{: #ibmliberty_get_started}

Verwenden Sie eines der kostenlosen `ibmliberty`-Images aus dem {{site.data.keyword.cloud_notm}}-Katalog oder wählen Sie Ihr eigenes Image mit Produktionslizenz aus, um einen einzelnen Container oder eine Containergruppe zu erstellen.
{:shortdesc}

Lesen Sie die [Nutzungseinschränkungen](#ibmliberty_usage) für die `ibmliberty`-Images, bevor Sie beginnen.
{: important}

1. Wählen Sie im Katalog in der seitlichen Anzeige die Option **Container** > **IBM Cloud Container-Registry** > **Öffentliche IBM Repositorys** aus. Suchen Sie nach dem Image `ibmliberty`, aus dem Sie den Container erstellen möchten. Wenn Sie ein eigenes Image mit Produktionslizenz erstellt und in {{site.data.keyword.cloud_notm}} bereitgestellt haben, wählen Sie dieses Image im Katalog aus. Die Seite zum Erstellen von Containern wird geöffnet.
2. Wählen Sie die Version des Images `ibmliberty`, das Sie verwenden möchten, im Dropdown-Feld für **TAG/ VERSION** aus.
3. Weitere Informationen zum Erstellen von Containern aus Images, zum Einrichten von Clustern und zum Bereitstellen von Apps in Clustern finden Sie unter den folgenden Links:

    - [Container auf Grundlage von Images erstellen](/docs/containers?topic=containers-images#images)
    - [Einführung in IBM Cloud Kubernetes Service](/docs/containers?topic=containers-getting-started#getting-started)
    - [Apps in Clustern bereitstellen](/docs/containers?topic=containers-app#app)

    Für das Image `ibmliberty` muss Port 9080 öffentlich zugänglich gemacht werden. Wenn Sie einen Container aus dem {{site.data.keyword.cloud_notm}}-Dashboard erstellen, wird der Port standardmäßig im Feld **Öffentlicher Port** hinzugefügt. Wenn Sie einen Container über die CLI erstellen, legen Sie den Port in Ihrem Befehl `kubectl run` mit der Option `--port=9080` frei.
    {:tip}

## Java-Heapspeicherbelegung für einen Container über die Befehlszeilenschnittstelle überwachen
{: #ibmliberty_monitor_heap}

Nachdem Sie einen Container aus dem Image `ibmliberty` erstellt haben, können Sie die Metriken für einen bestimmten Pod und seine Container anzeigen und die Java-Heapspeicherbelegung überprüfen. Der Java-Heapspeicher ist der Speicher, der der Java-Anwendung während der Laufzeit zur Verfügung steht.
{:shortdesc}

1. Rufen Sie den Namen des Pods ab, für den Sie Metriken anzeigen möchten.
  
   ```
   kubectl get pods
   ```
   {: pre}

2. Zeigen Sie Metriken für einen bestimmten Pod und seine Container an.

   ```
   kubectl top pod POD_NAME --containers
   ```
   {: pre}

3. Um die Java-Heapspeicherbelegung zu überprüfen, müssen Sie auf die **RSS**-Speicherstatistik zugreifen. Gehen Sie den Anweisungen für den Zugriff auf die Shell eines Containers entsprechend vor. Informationen hierzu finden Sie in [Shell für einen aktiven Container abrufen ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/). Die Java-Heapspeicherbelegung wird in Kilobyte angezeigt. Wenn die Heapspeicherbelegung für alle Instanzen kleiner als 2097152 Kilobyte (2GB) ist, müssen Sie keine WebSphere Application Server-Lizenz erwerben.

4. Passen Sie die maximale Heapspeicherbelegung für Ihre WebSphere Application Server-Instanz an. Weitere Informationen finden Sie unter [Setting generic JVM arguments in the WebSphere Application Server V8.5 Liberty profile ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](http://www.ibm.com/support/docview.wss?uid=swg21596474).

## WebSphere Application Server-Lizenz abrufen
{: #ibmliberty_license}

WebSphere Application Server basieren auf der Anzahl der benötigten Processor-Value-Units \(PVUs\). PVU ist eine Maßeinheit für die Lizenzierung von IBM Middleware-Software. Die Anzahl der PVUs gibt die Anzahl der Prozessoren \(Kerne\) an, die für die Software zur Verfügung stehen.
{:shortdesc}

Für jede Containergröße in {{site.data.keyword.cloud_notm}} ist eine bestimmte Anzahl von PVU-Berechtigungen erforderlich, die in der WebSphere Application Server-Lizenz verfügbar sein müssen. Sie müssen daher Ihre `ibmliberty`-Container planen, bevor Sie die Lizenz erwerben.

Wenden Sie sich für den Kauf einer WebSphere Application Server-Lizenz an den [IBM Service ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.ibm.com/us-en/marketplace/application-server-on-cloud/purchase). Wenn Sie bereits über eine Lizenz für WebSphere Application Server Version 8.5 oder höher verfügen, können Sie alle nicht genutzten PVUs der bestehenden Berechtigung für die Bereitstellung Ihres Containers verwenden.

Wenn Sie nach dem Erwerb der Lizenz feststellen, dass Sie weitere PVUs benötigen, können Sie die Anzahl der PVUs nach Rücksprache mit dem [IBM Service ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.ibm.com/us-en/marketplace/application-server-on-cloud/purchase) erhöhen.

## Image `ibmliberty` mit Produktionslizenz für die Verwendung mit {{site.data.keyword.containershort_notm}} erstellen
{: #ibmliberty_prod_image}

Verwenden Sie Ihre Lizenz für WebSphere Application Server zum Erstellen eines Images `ibmliberty` mit Produktionslizenz, das Sie mit {{site.data.keyword.containershort_notm}} verwenden können. Wählen Sie eine der folgenden Tasks aus.
{:shortdesc}

- [Durchführen eines Upgrades des Images von Docker Hub auf ein Produktionsimage ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade).
- [Erstellen eines eigenen Images mit Produktionslizenz ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://github.com/WASdev/ci.docker/tree/master/ga).

Nachdem Sie ein Image mit Produktionslizenz erstellt haben, [übertragen Sie das Image in Ihre private Registry](/docs/services/Registry?topic=registry-getting-started#getting-started), um es mit {{site.data.keyword.containershort_notm}} zu verwenden.

## Image über die bereitgestellten Images erstellen
{: #ibmliberty_creating_image}

Mit einem `ibmliberty`-Image als übergeordnetem Image können Sie ein untergeordnetes Image erstellen, das Ihren eigenen App-Code enthält. Passen Sie die Dockerfile-Beispieldatei an und erstellen Sie Ihr Image auf Ihrem Computer. Anschließend können Sie Ihr Image im privaten Bluemix-Repository Ihrer Organisation hinzufügen und Container mit diesem Image erstellen.
{:shortdesc}

Führen Sie zunächst die folgenden Schritte aus.

- Erstellen Sie eine WAR-, EAR- oder OSGi-Datei mit Ihrem App-Code.
- Kopieren Sie die Datei in das Verzeichnis, in dem Sie das Image erstellen möchten.

Führen Sie die folgenden Schritte aus, um ein Image mit Ihrem App-Code aus dem Image `ibmliberty` zu erstellen:

1. Erstellen Sie mit einem Texteditor eine Datei mit dem Namen `Dockerfile` und kopieren Sie die folgenden Informationen in diese Datei.

   ```
   FROM registry.bluemix.net/ibmliberty:<tag>
   COPY <app_name>.<file_extension> /config/dropins/
   ```
   {: pre}

    Das Verzeichnis `/config` ist ein Direktaufruf für `/opt/ibm/wlp/usr/servers/defaultServer`.
    {: note}

2. Ersetzen Sie `<tag>` durch die Version des Images `ibmliberty`, das die von der App benötigten Features enthält.

3. Ersetzen Sie `<app_name>` durch den Namen der App-Datei.

4. Ersetzen Sie `<file_extension>` entweder durch `.war`, `.ear` oder `.eba`.

5. Fügen Sie sonstige Abhängigkeiten für Ihre App zur Dockerfile hinzu.

6. Erstellen Sie das Image und übertragen Sie es mit Push-Operation in Ihr privates Bluemix-Repository. Weitere Informationen finden Sie unter [Einführung in {{site.data.keyword.registrylong_notm}}](/docs/services/Registry?topic=registry-getting-started#getting-started).

Alle `ibmliberty`-Images sind so konfiguriert, dass die Liberty-Protokolldateien in das Verzeichnis `/logs` des Containers geschrieben werden. Alle anderen vom Liberty-Server geschriebenen Dateien werden im Verzeichnis `/opt/ibm/wlp/output/defaultServer` erstellt. Der Zugriff auf diese Dateien ist über den Direktaufruf `/output` möglich.
{:tip}

## `ibmliberty`-Dockerfile - Referenz
{: #ibmliberty_reference_dockerfile}

Diese Dockerfile zeigt, wie das Image `ibmliberty:webProfile7` in {{site.data.keyword.cloud_notm}} aus den öffentlichen websphere-liberty-Images in Docker Hub erstellt wird. Die folgenden Informationen dienen nur zu Referenzzwecken.
{:shortdesc}

```
FROM websphere-liberty:webProfile7

# Update packages
RUN apt-get update &&\
    DEBIAN_FRONTEND=noninteractive apt-get upgrade -y &&\
    apt-get clean &&\
    rm -Rf /var/cache/*

# Set password length and expiry for compliance with vulnerability advisor
RUN sed -i 's/^PASS_MAX_DAYS.*/PASS_MAX_DAYS   90/' /etc/login.defs
RUN sed -i 's/sha512/sha512 minlen=8/' /etc/pam.d/common-password

# Add docs label
LABEL doc.url="/docs/services/RegistryImages/ibmliberty/index.html"
```
{: screen}
