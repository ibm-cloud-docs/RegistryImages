---

copyright:
  years: 2017, 2018
lastupdated: "2018-07-25"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Einführung in das Image **ibmliberty**
{: #ibmliberty}

Die Images von IBM® WebSphere® Application Server Liberty \(**ibmliberty**\) werden für {{site.data.keyword.containerlong_notm}} zur Verfügung gestellt.
{:shortdesc}

## Funktionsweise 
{: #how_it_works}

Sie können ein Image **ibmliberty** als übergeordnetes Element verwenden, um Ihr eigenes Image zu erstellen und Ihre eigenen WAR-, EAR- oder OSGi-Anwendungen auf der Basis von Java in einem IBM WebSphere Application Server Liberty-Container bereitzustellen.
{:shortdesc}

## Enthaltene Elemente 
{: #whats_included}

Jedes Liberty-Image bietet die folgenden Softwarepakete.
{:shortdesc}

-   IBM WebSphere Application Server for Developers Liberty
-   IBM Java Runtime Environment 8.0

Welche Liberty-Features jeweils in einem Image installiert sind, hängt davon ab, welchen Tag Sie auswählen. Die folgende Tabelle gibt an, welche Features jedes einzelne **ibmliberty**-Image enthält. Weitere Informationen zu den einzelnen Features finden Sie in der [Übersicht über die Liberty-Features im IBM Knowledge Center](http://www.ibm.com/support/knowledgecenter/SSAW57_8.5.5/com.ibm.websphere.wlp.nd.doc/ae/rwlp_feat.html).

|Tag|Beschreibung|
|---|-----------|
|Alle **ibmliberty**-Images|Alle **ibmliberty**-Images enthalten die folgenden Features. <ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|**ibmliberty:latest**|Dieses Image verweist auf das Image **ibmliberty:javaee7**.|
|**ibmliberty:microProfile**|Dieses Image enthält die Features, die die von [MicroProfile](https://microprofile.io) angegebenen Funktionen bereitstellen.|
|**ibmliberty:webProfile6**|Dieses Image enthält alle Features, die für die Konformität mit Java EE6 Web Profile erforderlich sind. Ferner enthält dieses Image zusätzliche Features, um den Inhalt an die Features anzupassen, die mit der Laufzeit-JAR von [http://wasdev.net/](http://wasdev.net/) heruntergeladen werden können, insbesondere die für OSGi-Anwendungen erforderlichen Features.|
|**ibmliberty:webProfile7**|Dieses Image enthält alle Features, die für die Konformität mit Java EE7 Web Profile erforderlich sind.|
|**ibmliberty:javaee7**|Dieses Image enthält alle Features von **ibmliberty:webProfile7** sowie die Features, die für die Konformität mit Java EE7 Full Platform erforderlich sind.|

## Nutzungsbeschränkungen 
{: #usage}

In der folgenden Tabelle sind die Beschränkungen für die kostenlose Nutzung des Images **ibmliberty** in {{site.data.keyword.Bluemix_notm}} angegeben.
{:shortdesc}

**Anmerkung:** Die Preisstruktur für das Image **ibmliberty** ist unabhängig von der Preisstruktur für die Container, die Sie in {{site.data.keyword.Bluemix_notm}} verwenden.

|Umgebung|Beschränkungen für die kostenlose Nutzung|
|-----------|-----------------------|
|Entwicklung|**Uneingeschränkte** kostenlose Nutzung des Images **ibmliberty**.|
|Produktion|Die kostenlose Nutzung des Images **ibmliberty** ist auf einen **Java-Heapspeicher von maximal 2 GB** über alle Containerinstanzen begrenzt, auf denen das Image ausgeführt wird. Sie können z. B. 2 x 1 GB oder 4 x 512 MB Liberty-Heapinstanzen kostenlos nutzen.

Weitere Informationen zum Überwachen der Java-Heapspeicherbelegung Ihrer Containerinstanzen finden Sie unter [Java-Heapspeicherbelegung für einen Container über die Befehlszeilenschnittstelle überwachen](#monitor_heap).


Lesen Sie zunächst die Nutzungsbedingungen für die von IBM zertifizierten Images im Abschnitt zum [Image 'websphere-liberty'](https://hub.docker.com/_/websphere-liberty/) in Docker Hub.

## Einführung 
{: #get_started}

Verwenden Sie eines der kostenlosen **ibmliberty**-Images aus dem {{site.data.keyword.Bluemix_notm}}-Katalog oder wählen Sie Ihr eigenes Image mit Produktionslizenz aus, um einen einzelnen Container oder eine Containergruppe zu erstellen.
{:shortdesc}

**Wichtig:** Bevor Sie beginnen, lesen Sie die [Nutzungsbeschränkungen](#usage) für die **ibmliberty**-Images.

1.  Wählen Sie im Katalog in der seitlichen Anzeige die Option **Container** > **IBM Cloud Container-Registry** > **Öffentliche IBM Repositorys** aus. Suchen Sie nach dem Image **ibmliberty**, aus dem Sie den Container erstellen möchten. Wenn Sie ein eigenes Image mit Produktionslizenz erstellt und in {{site.data.keyword.Bluemix_notm}} bereitgestellt haben, wählen Sie dieses Image im Katalog aus. Die Seite zum Erstellen von Containern wird geöffnet.
2.  Wählen Sie die Version des Images **ibmliberty**, das Sie verwenden möchten, im Dropdown-Feld für **TAG/ VERSION** aus.
3.  Weitere Informationen zum Erstellen von Containern aus Images, zum Einrichten von Clustern und zum Bereitstellen von Apps in Clustern finden Sie unter den nachfolgenden Links.

    -   [Container auf Grundlage von Images erstellen](/docs/containers/cs_images.html#images)
    -   [Einführung in IBM Cloud Kubernetes Service](/docs/containers/container_index.html#container_index)
    -   [Apps in Clustern bereitstellen](docs/containers/cs_app.html#app)
    
    **Anmerkung:** Für das Image **ibmliberty** muss Port 9080 öffentlich zugänglich gemacht werden. Wenn Sie einen Container aus dem {{site.data.keyword.Bluemix_notm}}-Dashboard erstellen, wird der Port standardmäßig im Feld **Öffentlicher Port** hinzugefügt. Wenn Sie einen Container über die CLI erstellen, legen Sie den Port in Ihrem Befehl `kubectl run` mit der Option `--port=9080` frei.


## Java-Heapspeicherbelegung für einen Container über die Befehlszeilenschnittstelle überwachen 
{: #monitor_heap}


Nachdem Sie einen Container aus dem Image **ibmliberty** erstellt haben, können Sie die Metriken für einen bestimmten Pod und seine Container anzeigen und die Java-Heapspeicherbelegung überprüfen. Der Java-Heapspeicher ist der Speicher, der der Java-Anwendung während der Laufzeit zur Verfügung steht.
{:shortdesc}

1.  Rufen Sie den Namen des Pods ab, für den Sie Metriken anzeigen möchten.
  
    ```
    kubectl get pods
    ```

2.  Zeigen Sie Metriken für einen bestimmten Pod und seine Container an.

    ```
    kubectl top pod POD_NAME --containers
    ```
    {: pre}

3.  Um die Java-Heapspeicherbelegung zu überprüfen, müssen Sie auf die **RSS**-Speicherstatistik zugreifen. Befolgen Sie die [hier](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/) aufgeführten Anweisungen für den Zugriff auf eine Shell eines Containers und überprüfen Sie anschließend [Laufzeitmetriken](containers/runmetrics/#metrics-from-cgroups-memory-cpu-block-io), um zu erfahren, wie Sie nach Speicherstatistikinformationen für einen Container suchen und diese formatieren können.
Die Java-Heapspeicherbelegung wird in Kilobyte angezeigt. Wenn die Heapspeicherbelegung für alle Instanzen kleiner als 2097152 Kilobyte (2GB) ist, müssen Sie keine WebSphere Application Server-Lizenz erwerben.

4.  Passen Sie die maximale Heapspeicherbelegung für Ihre WebSphere Application Server-Instanz an. Weitere Informationen finden Sie unter [Setting generic JVM arguments in the WebSphere Application Server V8.5 Liberty profile](http://www-01.ibm.com/support/docview.wss?uid=swg21596474).

## WebSphere Application Server-Lizenz abrufen 
{: #license}

WebSphere Application Server basieren auf der Anzahl der benötigten Processor-Value-Units \(PVUs\). PVU ist eine Maßeinheit für die Lizenzierung von IBM Middleware-Software. Die Anzahl der PVUs gibt die Anzahl der Prozessoren \(Kerne\) an, die für die Software zur Verfügung stehen.
{:shortdesc}

Für jede Containergröße in {{site.data.keyword.Bluemix_notm}} ist eine bestimmte Anzahl von PVU-Berechtigungen erforderlich, die in der WebSphere Application Server-Lizenz verfügbar sein müssen. Sie müssen daher Ihre **ibmliberty**-Container planen, bevor Sie die Lizenz erwerben.

Wenden Sie sich für den Kauf einer WebSphere Application Server-Lizenz an den [IBM Service](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us). Wenn Sie bereits über eine Lizenz für WebSphere Application Server Version 8.5 oder höher verfügen, können Sie alle nicht genutzten PVUs der bestehenden Berechtigung für die Bereitstellung Ihres Containers verwenden.

Wenn Sie nach dem Erwerb der Lizenz feststellen, dass Sie weitere PVUs benötigen, können Sie die Anzahl der PVUs nach Rücksprache mit dem [IBM Service](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us) erhöhen.

## Image **ibmliberty** mit Produktionslizenz für die Verwendung mit {{site.data.keyword.containershort_notm}} erstellen 
{: #prod_image}

Verwenden Sie Ihre Lizenz für WebSphere Application Server zum Erstellen eines Images **ibmliberty** mit Produktionslizenz, das Sie mit {{site.data.keyword.containershort_notm}} verwenden können. Wählen Sie eine der folgenden Tasks aus.
{:shortdesc}

-   [Durchführen eines Upgrades des Images von Docker Hub auf ein Produktionsimage](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade).
-   [Erstellen eines eigenen Images mit Produktionslizenz](https://github.com/WASdev/ci.docker/tree/master/ga/production-install).

Nachdem Sie ein Image mit Produktionslizenz erstellt haben, [übertragen Sie das Image in Ihre private Registry](/docs/services/Registry/index.html), um es mit {{site.data.keyword.containershort_notm}} zu verwenden.

## Image über die bereitgestellten Images erstellen 
{: #creating_image}

Mit einem **ibmliberty**-Image als übergeordnetem Image können Sie ein untergeordnetes Image erstellen, das Ihren eigenen App-Code enthält. Passen Sie die Dockerfile-Beispieldatei an und erstellen Sie Ihr Image auf Ihrem Computer. Anschließend können Sie Ihr Image im privaten Bluemix-Repository Ihrer Organisation hinzufügen und Container mit diesem Image erstellen.
{:shortdesc}

Führen Sie zunächst die folgenden Schritte aus.

-   Erstellen Sie eine WAR-, EAR- oder OSGi-Datei mit Ihrem App-Code.
-   Kopieren Sie die Datei in das Verzeichnis, in dem Sie das Image erstellen möchten.


Gehen Sie wie folgt vor, um ein Image mit Ihrem App-Code aus dem Image **ibmliberty** zu erstellen:

1. Erstellen Sie mit einem Texteditor eine Datei mit dem Namen "Dockerfile" und kopieren Sie die folgenden Informationen in diese Datei.

    ```
    FROM registry.bluemix.net/ibmliberty:<tag>
    COPY <app-name>.<dateierweiterung> /config/dropins/
    
    ```
    {: screen}

    **Anmerkung:** Das Verzeichnis /config ist ein Direktaufruf für /opt/ibm/wlp/usr/servers/defaultServer.
    
2. Ersetzen Sie <tag\> durch die Version des Images **ibmliberty**, das die von der App benötigten Features enthält.

3. Ersetzen Sie <app-name\> durch den Namen der App-Datei.

4. Ersetzen Sie <dateierweiterung\> entweder durch .war, .ear oder .eba.

5. Fügen Sie sonstige Abhängigkeiten für Ihre App zur Dockerfile hinzu.

6. Erstellen Sie das Image und übertragen Sie es mit Push-Operation in Ihr privates Bluemix-Repository. Weitere Informationen finden Sie unter [Einführung in {{site.data.keyword.registrylong_notm}}](/docs/services/Registry/index.html).

**Anmerkung:** Alle **ibmliberty**-Images sind so konfiguriert, dass die Liberty-Protokolldateien in das Verzeichnis /logs des Containers geschrieben werden. Alle anderen vom Liberty-Server geschriebenen Dateien werden im Verzeichnis /opt/ibm/wlp/output/defaultServer erstellt. Der Zugriff auf diese Dateien ist über den Direktaufruf /output möglich.

## **ibmliberty**-Dockerfile - Referenz 
{: #reference_dockerfile}

Diese Dockerfile zeigt, wie das Image **ibmliberty:webProfile7** in {{site.data.keyword.Bluemix_notm}} aus den öffentlichen websphere-liberty-Images in Docker Hub erstellt wird. Die folgenden Informationen dienen nur zu Referenzzwecken.
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
