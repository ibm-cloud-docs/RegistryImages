---

copyright:
  years: 2015, 2017
lastupdated: "2017-10-30"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}

# Einführung in das Image 'ibm-node-strong-pm'
{: #getting_started_node_strong}

Das {{site.data.keyword.IBM}} Node-Image mit StrongLoop \(**ibm-node-strong-pm**\) wird für {{site.data.keyword.containerlong_notm}} zur Verfügung gestellt.
{:shortdesc}

## Funktionsweise 
{: #how_it_works}

Verwenden Sie das Image **ibm-node-strong-pm** zum Erstellen eines Containers, von dem eine aktive Instanz von [StrongLoop Process Manager](https://www.strongloop.com) bereitgestellt wird. Strongloop Process Manager ermöglicht die Bereitstellung, Verwaltung und Überwachung von Node.js-Apps in der {{site.data.keyword.IBM_notm}}, die auf einer fernen Maschine implementiert wurden.
{:shortdesc}

Auf der fernen Maschine muss eine Instanz von StrongLoop Arc installiert sein. StrongLoop Arc ist eine grafische Benutzerschnittstelle für die StrongLoop-Plattform. Sie umfasst Tools zur Erstellung, Profilerstellung und Überwachung für Node.js-Apps. Nachdem Sie eine App mit StrongLoop Arc erstellt haben, übertragen Sie sie mit einer Push-Operation und stellen sie in den Container in {{site.data.keyword.Bluemix_notm}} bereit, der StrongLoop Process Manager ausführt. Um eine Verbindung zwischen der fernen Maschine und dem Container herzustellen, müssen Sie eine öffentliche IP-Adresse an den Container binden und diese IP-Adresse als StrongLoop Process Manager-Server in StrongLoop Arc definieren. Wenn Ihre App über den StrongLoop Process Manager bereitgestellt wird, können Sie die Metriken und die Leistung Ihrer App in StrongLoop Process Manager überwachen. Zum Aktualisieren oder Skalieren Ihrer App müssen Sie mithilfe von StrongLoop Arc Änderungen an ihr vornehmen und die Änderungen dann anhand einer Push-Operation an den Container übertragen.

**Anmerkung:** Es besteht keine Unterstützung für die Verwendung von **ibm-node-strong-pm** in Verbindung mit Containergruppen in {{site.data.keyword.Bluemix_notm}}.

## Enthaltene Elemente
{: #whats_included}

-   Alle Softwarepakete, die im Image **ibmnode:latest** enthalten sind. Informationen hierzu finden Sie unter [Enthaltene Elemente](/docs/services/RegistryImages/ibmnode/index.html#getting_started_node) im Abschnitt 'Einführung in das Image **ibmnode** für {{site.data.keyword.Bluemix_notm}}'.
-   StrongLoop Process Manager

## Einführung
{: #how_to_get_started}

Sie können das Image **ibm-node-strong-pm** im {{site.data.keyword.Bluemix_notm}}-Katalog verwenden, um einen einzelnen Container in {{site.data.keyword.containershort_notm}} auszuprobieren. Das Image wird mit einer aktiven Instanz von StrongLoop Process Manager gestartet, auf die Sie zugreifen können, sobald der Container verfügbar ist. Mithilfe einer StrongLoop Arc-Instanz, die mit dieser Process Manager-Instanz verbunden ist, können Sie eine Node.js-Anwendung mit Push-Operation übertragen und auf die Anwendung zugreifen.
{:shortdesc}

**Anmerkung:** Lesen Sie zunächst die Nutzungsbedingungen für die von {{site.data.keyword.IBM_notm}} zertifizierten Images im Abschnitt 'Usage' in der [WebSphere Liberty Docker-Bibliothek](https://github.com/docker-library/docs/tree/master/websphere-liberty).

1.  Wählen Sie im Katalog **Container** und dann das Image **ibm-node-strong-pm** aus, aus dem Sie den Container erstellen wollen. 
2.  Erstellen Sie einen einzelnen Container.
    -   Wählen Sie im Feld **Größe** eine Containergröße von mindestens 1 GB Speicher \(**Small**\) aus.
    -   Geben Sie in das Feld **Öffentliche Ports** die Ports 8701 und 3001 ein. Diese Ports werden automatisch zugänglich gemacht, wenn Ihr Container in {{site.data.keyword.Bluemix_notm}} bereitgestellt wird, und stellen eine Voraussetzung zum Binden einer öffentlichen IP-Adresse an den Container dar. Port 8701 wird für den Zugriff auf StrongLoop Process Manager im Container verwendet. Port 3001 ist der Port, an dem die App nach Ihrer Bereitstellung bei StrongLoop Process Manager empfangsbereit ist.

        **Anmerkung:** Wenn Sie weitere Apps an den StrongLoop Process Manager bereitstellen möchten, müssen Sie weitere Ports zugänglich machen. Diese Ports müssen fortlaufende Portnummern ab 3002 aufweisen. Beispiel: 3002, 3003, 3004 und so weiter.

    -   Weitere Informationen hierzu finden Sie im Abschnitt [Einzelnen Container über das {{site.data.keyword.Bluemix_notm}}-Dashboard erstellen](/docs/containers/container_single_ui.html#gui).
3.  Binden Sie eine öffentliche IP-Adresse an den Container. Weitere Informationen finden Sie unter [IP-Adressen anfordern und binden](/docs/containers/container_single_ui.html#container_cli_ips).

## StrongLoop Arc auf einer fernen Maschine einrichten
{: #UsingSLA}

Nachdem Sie aus dem Image **ibm-node-strong-pm** in {{site.data.keyword.Bluemix_notm}} einen einzelnen Container erstellt haben, müssen Sie StrongLoop Arc auf Ihrer fernen Maschine einrichten.
{:shortdesc}

Stellen Sie zunächst sicher, dass 'Node.js' und Node Package Manager \(npm\) auf Ihrer fernen Maschine installiert sind.

1.  Geben Sie den folgenden Befehl ein, um StrongLoop herunterzuladen und zu installieren. StrongLoop enthält alle Pakete, die zum Ausführen von Arc erforderlich sind.

    ```
    npm install -g strongloop
    ```
    {: pre}

2.  Führen Sie nach der StrongLoop-Installation StrongLoop Arc aus.

    ```
    slc arc
    ```
    {: pre}

3.  Registrieren Sie sich bei StrongLoop bzw. melden Sie sich bei StrongLoop an, wenn Sie dazu aufgefordert werden.
4.  Fügen Sie den in Ihrem Container ausgeführten StrongLoop Process Manager zur Process Manager-Liste in StrongLoop Arc hinzu.
    1.  Navigieren Sie von der Arc-Modulauswahl aus zu **Process Manager**.
    2.  Fügen Sie die öffentliche IP-Adresse, die Sie an Ihren Container gebunden haben, und den öffentlichen Port 8701 zum Process Manager von Arc hinzu.
    3.  Klicken Sie auf **Aktivieren**.

## Node.js-App unter Verwendung von StrongLoop Arc mit einer Push-Operation an den Container übertragen
{: #pushing_app_to_container}

Nachdem Sie StrongLoop Process Manager, der in Ihrem Container ausgeführt wird, mit dem StrongLoop Arc-Client auf der fernen Maschine verbunden haben, erstellen Sie Ihre Node.js-Apps und übertragen Sie sie mit Push-Operationen an {{site.data.keyword.Bluemix_notm}}.{:shortdesc}

1.  Navigieren Sie von der Arc-Modulauswahl aus zu **Build and Deploy**.
2.  Erstellen Sie Ihre App wie in den Anweisungen der Benutzerschnittstelle von Arc angegeben.

    **Wichtig:** Stellen Sie sicher, dass Ihre mit StrongLoop Arc erstellte Node.js-App unter der Umgebungsvariablen PORT und dem Port empfangsbereit ist, den Sie bei der Erstellung des Containers zugänglich gemacht haben.

    Beispiel

    ```
    app.listen(process.env.PORT || 3001);
    ```
    {: pre}

3.  Stellen Sie Ihre App wie in den Anweisungen der Benutzerschnittstelle von Arc angegeben bereit. Wählen Sie den von Ihnen konfigurierten Process Manager aus, um Ihre App in {{site.data.keyword.Bluemix_notm}} bereitzustellen.
4.  Warten Sie, bis der Bereitstellungsprozess abgeschlossen ist.
5.  Greifen Sie auf Ihre App zu.
    1.  Öffnen Sie einen Web-Browser.
    2.  Geben Sie die IP-Adresse für Ihren Container und Port im Format <var class="keyword varname">IP-ADRESSE:PORT</var> ein.

        Beispiel

        ```
        192.0.2.26:3001
        ```
        {: pre}

    3.  Wiederholen Sie diese Schritte für jede App, die Sie in {{site.data.keyword.Bluemix_notm}} bereitgestellt haben.
6.  Verwenden Sie das Arc-Tracing sowie die Metriken und den Profiler, um Informationen zum Leistungsverhalten Ihrer Apps abzurufen, während die Apps in {{site.data.keyword.Bluemix_notm}} ausgeführt werden. Weitere Informationen finden Sie unter [Operating Node applications](https://docs.strongloop.com/display/SLC/Operating+Node+applications) in der Dokumentation zu StrongLoop.

## **ibm-node-strong-pm**-Dockerfile - Referenz 
{: #reference_dockerfile}

Diese Dockerfile wurde verwendet, um das Image **ibm-node-strong-pm** im {{site.data.keyword.Bluemix_notm}}-Katalog zu erstellen. Die folgenden Informationen dienen nur zu Referenzzwecken. Die anderen Dateien, die in der Buildversion dieses Images enthalten sind, werden nicht zur Verfügung gestellt.

```
FROM registry.ng.bluemix.net/ibmnode:latest
RUN useradd -ms /bin/bash strong-pm \    
    && chown -R strong-pm:strong-pm /usr/local \    
    && su strong-pm -c "npm install -g strong-pm && npm cache clear"

# Set up some semblance of an environment
WORKDIR /home/strong-pm
ENV HOME=/home/strong-pm PORT=3000

# Run as non-privileged user inside container
USER strong-pm

# Expose strong-pm and application ports
EXPOSE 8701
EXPOSE 3001-3010

ENTRYPOINT ["/usr/local/bin/sl-pm", "--base", ".", "--listen", "8701"]
```
{: screen}


