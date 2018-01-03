---

copyright:
  years: 2015, 2017

lastupdated: "2017-10-30"

---


{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:screen: .screen}

# Einführung in das Image **ibm-mq**
{: #ibm_mq}

Das Image 'ibm-mq' wird für {{site.data.keyword.containershort}} zur Verfügung gestellt. Es beinhaltet {{site.data.keyword.IBM_notm}} MQ Advanced for Developers (vorveröffentlicht). 

{:shortdesc}


## Funktionsweise
{: #ibm_mq_how}

{{site.data.keyword.IBM_notm}} MQ Advanced for Developers enthält alles, was Sie für die Entwicklung Ihrer eigenen Messaging-Lösungen und {{site.data.keyword.IBM_notm}} MQ-Anwendungen benötigen. 

Wenn Sie Messaging-Lösungen und {{site.data.keyword.IBM_notm}} MQ-Anwendungen in {{site.data.keyword.Bluemix_notm}} entwickeln möchten, können Sie das Docker-Image 'ibm-mq' in {{site.data.keyword.containershort_notm}} bereitstellen. Anschließend können Sie Warteschlangenmanager erstellen und ausführen und die Webbenutzerschnittstelle oder ein Terminal verwenden, um sie zur Erfüllung der Anforderungen Ihrer Messaging-Lösungen oder Anwendungen zu konfigurieren.

**Anmerkung:** Sie können das Image 'ibm-mq' für die Entwicklung und für Komponententests verwenden. Sie können das Image außerdem verwenden, um das Produkt kennenzulernen, um Lernprogramme zu nutzen und um den Beitrag zu bewerten, den {{site.data.keyword.IBM_notm}} MQ für Ihre Organisation leisten kann.

## Enthaltene Elemente
{: #ibm_mq_what}

Das Image 'ibm-mq' enthält das Softwarepaket für {{site.data.keyword.IBM_notm}} MQ Advanced for Developers Version 9.0.x. Dabei handelt es sich um eine Produktversion mit vollem Funktionsumfang, die Sie für die Entwicklung und für Komponententests verwenden können. Sie können diese Version kostenlos herunterladen und sie gemäß den Lizenzbedingungen nutzen.

{{site.data.keyword.IBM_notm}} MQ Advanced for Developers ist für Windows-64-Bit-Betriebssystemen und unter Linux on x86-64-Betriebssystemen verfügbar. Alle Produktvoraussetzungen sind im Downloadpaket enthalten. Weitere Informationen finden Sie unter [IBM MQ-Downloads ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](http://www-03.ibm.com/software/products/lv/ibm-mq-advanced-for-developers){: new_window}.

Informationen zu den Features, die in {{site.data.keyword.IBM_notm}} MQ Advanced for Developers enthalten sind, finden Sie unter [IBM MQ ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/SSFKSJ_9.0.0/com.ibm.mq.helphome.v90.doc/WelcomePagev9r0.htm){: new_window}.



## Nutzungsbeschränkungen
{: #ibm_mq_usage}

In der folgenden Tabelle sind die Beschränkungen für die kostenlose Nutzung des Images 'ibm-mq' in {{site.data.keyword.Bluemix_notm}} angegeben:

| Umgebung	| Beschränkungen für die kostenlose Nutzung |
|-------------|-------------------------|
| Entwicklung	| Uneingeschränkte kostenlose Nutzung des Images 'ibm-mq' zur Erstellung von einzelnen Containern für die Entwicklung. |
| Test | Uneingeschränkte kostenlose Nutzung des Images 'ibm-mq' zur Erstellung von einzelnen Containern für den Komponententest. In den [Lizenzinformationen](#ibm_mq_license ) finden Sie weitere Informationen darüber, welche Tests zulässig sind. |
| Produktion | Die Nutzung der {{site.data.keyword.IBM_notm}} MQ Advanced for Developer-Lizenz im Produktionsbetrieb ist nicht zulässig. |

**Anmerkung:** Die Preisstruktur für das Image 'ibm-mq' ist unabhängig von der Preisstruktur für die Container, die Sie in {{site.data.keyword.Bluemix_notm}} verwenden.


## Lizenzinformationen
{: #ibm_mq_license}

Informationen zu Lizenzen:

*	Die Dockerfiles und die zugehörigen Scripts werden unter [Apache License 2.0![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link") ](http://www.apache.org/licenses/LICENSE-2.0.html){: new_window} lizenziert.
* [Lizenzinformationen für {{site.data.keyword.IBM_notm}} MQ Advanced for Developers ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](http://www-03.ibm.com/software/sla/sladb.nsf/displaylis/973FA648A5DE42948525806E004CC757?OpenDocument){: new_window}.

**Anmerkung:** Die Lizenz lässt keine weitere Verteilung zu. Die Bedingungen für {{site.data.keyword.IBM_notm}} MQ in dem Image schränken die Nutzung auf die Entwicklung und Komponententests ein.


## Einführung
{: #ibm_mq_get_started}

Führen Sie die folgenden Schritte aus, um {{site.data.keyword.IBM_notm}} MQ in einem Container auszuführen und zu verwalten, der unter {{site.data.keyword.Bluemix_notm}} ausgeführt wird:

1. [Richten Sie einen Container ein, der auf dem Image 'ibm-mq' basiert.](#ibm_mq_provision)

    **Anmerkung:** Der Container ist an den aktiven Status des enthaltenen Warteschlangenmanagers gebunden. Wenn Sie den Warteschlangenmanager über die Konsole oder die Befehlszeile stoppen, wird der Container kurz danach gestoppt. 

2. Verwalten Sie die {{site.data.keyword.IBM_notm}} MQ-Ressourcen, die in einem Container ausgeführt werden. Sie können dies über eine grafische Oberfläche von der IBM MQ-Webkonsole oder durch Eingabe von Befehlen von einem Terminal aus tun. 

    1. Wählen Sie eine der folgenden Optionen aus, um {{site.data.keyword.IBM_notm}} MQ-Ressourcen im Container zu verwalten:
    
        *	Starten Sie die {{site.data.keyword.IBM_notm}} MQ-Webkonsole und verwalten Sie {{site.data.keyword.IBM_notm}} MQ so über eine grafische Oberfläche. Weitere Informationen finden Sie unter [IBM MQ-Webkonsole starten](#ibm_mq_webui).
        *	Verwenden Sie von einem Terminal aus die Docker-Befehlszeilenschnittstelle. Weitere Informationen finden Sie unter [Terminal zur Verwendung der Docker-Befehlszeilenschnittstelle einrichten](#ibm_mq_docker_commands).
        * Verwenden Sie von einem Terminal aus die {{site.data.keyword.containershort_notm}}Befehlszeilenschnittstelle. Weitere Informationen finden Sie unter [Terminal zur Verwendung der {{site.data.keyword.containershort_notm}}-Befehlszeilenschnittstelle einrichten](#ibm_mq_containers_cli).
        
    2. Konfigurieren Sie den {{site.data.keyword.IBM_notm}} MQ-Warteschlangenmanager, der in dem Container ausgeführt wird. 

        * Zum Verwalten des Warteschlangenmanagers über die Befehlszeile verwenden Sie das Programm *runmqsc*. Führen Sie den folgenden Befehl aus: `runmqsc QM-name`. Dabei ist *QM-name* der Name Ihres Warteschlangenmanagers. Weitere Informationen hierzu finden Sie unter [Container über die Befehlszeile verwalten](#ibm_mq_access).
    
        * Verwenden Sie die Webbenutzerschnittstelle, um den Warteschlangenmanager über eine grafische Oberfläche zu verwalten.
        
    3. Folgen Sie den Anweisungen im [ IBM MQ Knowledge Center![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link") ](http://www.ibm.com/support/knowledgecenter/en/SSFKSJ_9.0.0/com.ibm.mq.helphome.v90.doc/WelcomePagev9r0.htm){: new_window}, um Nachrichtenwarteschlangen in Ihrem Warteschlangenmanager zu erstellen und um Nachrichten in diese Warteschlangen zu stellen oder aus ihnen abzurufen.
    
        Beispiel: Führen Sie den folgenden Befehl in `runmqsc` aus, um eine neue lokale Warteschlange zu erstellen: `DEFINE QLOCAL('MY.Q')`. Dabei ist *MY.Q* der Name der lokalen Warteschlange vom Terminal. Sie können die Warteschlange auf der Webkonsole anzeigen oder Sie können die Warteschlange und ihre Eigenschaften durch Ausführen des folgenden Befehls in 'runmqsc' anzeigen: `DISPLAY QLOCAL('MY.Q')``.
    
3. (Optional) Überwachen Sie die {{site.data.keyword.IBM_notm}} MQ-Protokolle in dem Container. Weitere Informationen hierzu finden Sie unter [Container über die Befehlszeile verwalten](#ibm_mq_access).

Einführende Informationen zu IBM MQ finden Sie auf der [IBM MQ Version 9.0.x-Begrüßungsseite ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](http://www.ibm.com/support/knowledgecenter/en/SSFKSJ_9.0.0/com.ibm.mq.helphome.v90.doc/WelcomePagev9r0.htm){: new_window}.

Weitere Informationen zum Image und dazu, wie Sie Ihr eigenes Image in Docker erstellen können, finden Sie auf der [Seite 'MQ Docker GitHub'![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link") ](https://github.com/ibm-messaging/mq-docker){: new_window}.
    
Die neuesten Blogs und Informationen zu IBM MQ finden Sie im [MQ developerWorks-Blog ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link") ](https://www.ibm.com/developerworks/community/blogs/messaging){: new_window}.


### Auf dem Image 'ibm-mq' basierenden Container einrichten
{: #ibm_mq_provision}

Führen Sie die folgenden Schritte aus, um einen Docker-Container in {{site.data.keyword.Bluemix_notm}} auf der Basis des von {{site.data.keyword.IBM_notm}} zur Verfügung gestellten Images 'ibm-mq' einzurichten:

1.	Melden Sie sich bei {{site.data.keyword.Bluemix_notm}} an. Melden Sie sich mit Ihrer {{site.data.keyword.Bluemix_notm}}-ID an.
2.	Wählen Sie im Katalog **Container** aus und dann das Image *ibm-mq*.
3.	Wählen Sie **Einzeln** aus, um eine einzelne Instanz eines Containers zu erstellen, der für Entwicklungs- und Testzwecke verwendet werden kann.
4.	Geben Sie den Namen des Containers ein, z. B. 'mq'.
5.	Wählen Sie die Größe des Containers aus.
6.	Wählen Sie im Feld 'Öffentliche IP-Adresse' die Option **Öffentliche IP anfordern und binden** aus.
7.	Geben Sie im Feld 'Öffentliche Ports' die Nummern **1414/tcp, 9443/tcp** ein.
8.	Erweitern Sie die erweiterten Optionen, klicken Sie auf **Neue Umgebungsvariable hinzufügen** und geben Sie die folgenden Umgebungsvariablen ein: LICENSE und MQ_QMGR_NAME.

    * Setzen Sie den Wert von **LICENSE** auf **accept**, um die Lizenzbedingungen für IBM MQ Advanced for Developers zu akzeptieren. 
    
        Wenn Sie die Lizenz anzeigen möchten, bevor Sie sie akzeptieren, können Sie LICENSE auf "view" setzen und die Lizenz wird in den Containerprotokollen ausgegeben. 
    
        Wenn Englisch nicht Ihre erste Sprache ist, können Sie die Lizenzdatei in einer anderen Sprache anzeigen, indem Sie eine andere Umgebungsvariable mit dem Namen **LANG** auf einen der folgenden Werte setzen: *zh_TW* für Chinesisch (Taiwan), *zh* für Chinesisch, *cs* für Tschechisch, *en* für Englisch, *fr* für Französisch, *de* für Deutsch, *el* für Griechisch, *id* für Indonesisch, *it* für Italienisch, *ja* für Japanisch, *ko* für Koreanisch, *lt* für Litauisch, *pl* für Polnisch, *pt* für Portugiesisch, *ru* für Russisch, *sl* für Slowenisch, *es* für Spanisch oder *tr* für Türkisch.

    * Geben Sie für **MQ_QMGR_NAME** den Wert *MYMQ* an. Hierbei steht *MYMQ* für den von Ihnen gewählten Warteschlangenmanagernamen.
 
        **Anmerkung**: Wenn Sie keine Werte für die Umgebungsvariablen LICENSE und MQ_QMGR_NAME angeben, wird der Container erstellt, aber nicht gestartet. 
    
    * (Optional) Standardmäßig wird die {{site.data.keyword.IBM_notm}} MQ-Webkonsole in dem Container gestartet. Wenn Sie das Standardverhalten ändern möchten, setzen Sie den Wert der Umgebungsvariable **MQ_DISABLE_WEB_CONSOLE** auf *true*.
    
        **Anmerkung:** Wenn Sie die Webkonsole verwenden möchten, legen Sie die Umgebungsvariable *MQ_DISABLE_WEB_CONSOLE* nicht fest.

    * (Optional) Wenn Sie die MQ-Protokolle über die {{site.data.keyword.Bluemix_notm}}-Benutzerschnittstelle anzeigen möchten, setzen Sie die Umgebungsvariable **LOG_LOCATIONS** auf den Wert `/var/mqm/qmgr/QM-name/errors/AMQERR01.LOG`, wobei *QM-name* der Wert ist, den Sie für die Umgebungsvariable *MQ_QMGR_NAME* festgelegt haben.
    
    *	(Optional) Standardmäßig werden die {{site.data.keyword.IBM_notm}} MQ Developer-Standardobjekte erstellt. Wenn Sie das Standardverhalten ändern möchten, setzen Sie die Umgebungsvariable **MQ_DEV** auf *false* und ändern Sie die Standardinstallationswerte für IBM MQ in Ihre angepassten Werte. Weitere Informationen finden Sie unter [Standardinstallationswerte ändern](#ibm_mq_dev_default).
 
9. (Optional) Ordnen Sie im Abschnitt 'Erweiterte Optionen' einen Datenträger zu. Verwenden Sie {{site.data.keyword.Bluemix_notm}}-Datenträger, um {{site.data.keyword.IBM_notm}} MQ-Daten und die Konfiguration persistent über Containermigrationen zu speichern. {{site.data.keyword.IBM_notm}} MQ-Daten, die nicht auf einem Datenträger gespeichert sind, gehen verloren, wenn ein Container beendet wird.

    Das Image 'ibm-mq' kann mit {{site.data.keyword.Bluemix_notm}}-Datenträgern verwendet werden, aber die Datenträger müssen an **/mnt/mqm** angehängt sein, damit sie von {{site.data.keyword.IBM_notm}} MQ erkannt und verwendet werden können.

    Standardmäßig gehen die IBM MQ-Daten in allen Containern, die ohne einen Datenträger erstellt wurden, verloren, wenn der Container gestoppt wird. Um dies zu verhindern, müssen Sie persistente Nachrichtenübertragung und {{site.data.keyword.Bluemix_notm}}-Datenträger verwenden.

    1. Klicken Sie auf **Datenträger erstellen**, geben Sie einen Datenträgernamen ein und wählen Sie eine Dateifreigabe aus.
    2. Klicken Sie auf **Vorhandenen Datenträger zuweisen** und wählen Sie den von Ihnen erstellten Datenträger aus. Geben Sie **/mnt/mqm** in das Pfadfeld ein. Stellen Sie sicher, dass das Feld **Schreibgeschützt** nicht ausgewählt ist.

    **Anmerkung:** Wenn Sie einen Datenträger für die Verwendung als IBM MQ-Datenverzeichnis anhängen, müssen Sie ihn an die Position */mnt/mqm* anhängen. Nach dem Anhängen an */mnt/mqm* erkennt das 'ibm-mq'-Script den Datenträger und konfiguriert das IBM MQ-Dateisystem so, dass es ihn verwenden kann.
     
10.	Klicken Sie auf **Erstellen** und warten Sie, bis der Container gestartet wird.

    Nachdem der Container erstellt wurde, wird das Container-Dashboard geöffnet. Stellen Sie sicher, dass der Status des Containers auf **Aktiv** gesetzt ist. 

    Wenn Sie die Protokolle Ihres Containers prüfen wollen, klicken Sie auf **Überwachung und Protokolle** und wählen Sie die Registerkarte **Protokollierung** aus. Die Protokolle werden angezeigt. Für eine weitergehende Analyse Ihrer Protokolle klicken Sie auf **Erweiterte Ansicht**, um Ihre Protokolle in Kibana anzuzeigen.

Sie haben einen Warteschlangenmanager erstellt und gestartet. Sie können jetzt eine MQ-Anwendung mit den folgenden Verbindungsdetails verbinden:

| Verbindungseigenschaft | Wert |
|-------|-------|
| IP-Adresse  | Öffentliche IP-Adresse des Containers |
| Port | 1414 |
| Kanal | DEV.APP.SVRCONN |


### Container über die Befehlszeile verwalten
{: #ibm_mq_access}

Wenn der Container, in dem {{site.data.keyword.IBM_notm}} MQ ausgeführt wird, in {{site.data.keyword.Bluemix_notm}} bereitgestellt wurde, können Sie auf den Container und seinen Status zugreifen und die Installation von {{site.data.keyword.IBM_notm}} MQ Bus validieren.

Führen Sie die folgenden Schritte aus, um die Einrichtung und Konfiguration von {{site.data.keyword.IBM_notm}} MQ im Container zu überprüfen:

1.	Richten Sie die {{site.data.keyword.containershort_notm}}-Befehlszeilenschnittstelle ein. Weitere Informationen finden Sie auf [der Seite der {{site.data.keyword.Bluemix_notm}}-Befehlszeilenschnittstelle](https://plugins.ng.bluemix.net/ui/home.html). Folgen Sie den Anweisungen des neuesten {{site.data.keyword.containershort_notm}}-Plug-ins.

2.	Melden Sie sich von einem Terminal aus bei Ihrer Organisation und dem Bereich unter {{site.data.keyword.Bluemix_notm}} an, wo der Container mit Ihrer {{site.data.keyword.Bluemix_notm}}-ID ausgeführt wird. Führen Sie die folgenden Befehle aus:

    1. `    bx login
    `
    2. `    bx ic init
    `
    
3.	Überprüfen Sie den Status des Containers. Führen Sie den folgenden Befehl aus: `bx ic ps`

4.	Ordnen Sie Ihrem Container eine Bash-Sitzung zu:

    `bx ic exec -it containername /bin/bash`
    
    Dabei steht 'containername' für den Namen des Containers.
    
5.	Führen Sie Befehle aus, um die MQ-Konfiguration zu verwalten, z. B.:

| Befehl | Aktion |
|---------|---------|
| dspmqver | IBM MQ-Version und Buildinformationen überprüfen. |
| dspmq | Status des Warteschlangenmanagers prüfen, der in dem Container ausgeführt wird. | 
| runmqsc | MQ-Ressourcen verwalten. |

Wählen Sie eine der folgenden Optionen aus, um MQ-Protokolle zu überwachen:

* Wenn Ihr Terminal für die Ausführung von {{site.data.keyword.containershort_notm}}-CLI-Befehlen konfiguriert ist, führen Sie den folgenden Befehl aus: `bx ic exec container-ID tail -f /var/mqm/qmgr/QM-name/errors/AMQERR01.LOG`. Dabei steht *container-ID* für den Namen Ihres Containers und *QM-name* für den Namen Ihres Warteschlangenmanagers.
    
* Wenn Ihr Terminal für die Ausführung von Docker-CLI-Befehlen konfiguriert ist, führen Sie den folgenden Befehl aus: `docker exec container-ID tail -f /var/mqm/qmgr/QM-name/errors/AMQERR01.LOG`. Dabei steht *container-ID* für den Namen Ihres Containers und *QM-name* für den Namen Ihres Warteschlangenmanagers.
    
    

### IBM MQ-Webkonsole starten
{: #ibm_mq_webui}

Führen Sie die folgenden Schritte aus, um die {{site.data.keyword.IBM_notm}} MQ-Webkonsole zu starten:

1.	Wählen Sie im {{site.data.keyword.Bluemix_notm}}-Dashboard Ihren Container aus und stellen Sie sicher, dass der Status des Containers auf *Aktiv* gesetzt ist.
2.	Stellen Sie sicher, dass für Ihren Container der öffentliche Port 9443 konfiguriert ist.
3.	Ermitteln Sie die öffentliche IP durch eine Prüfung der Containerdetails oder fordern Sie eine öffentliche IP an und binden sie, wenn dies noch nicht geschehen ist.
4.	Starten Sie über einen Web-Browser die folgende URL: `https://öffentliche_ip_für_docker-container: 9443/ibmmq/console/`, wobei 'öffentliche_ip_für_docker-container' die öffentliche IP-Adresse Ihres Containers ist.

    Eine Sicherheitswarnung wird angezeigt, weil das TLS-Zertifikat, das der Web-Server beim Hosten der MQ-Konsole verwendet, generiert und selbst signiert wird. Klicken Sie auf **Ausnahme hinzufügen** und bestätigen Sie.

    **Anmerkung:** Verwenden Sie in einer Produktionsumgebung Ihr eigenes Zertifikat, das von Ihrem Browser erkannt und anerkannt wird. Dieses Zertifikat sollte von einer Zertifizierungsstelle ausgestellt worden sein. 
    
    Im Browser wird die Webbenutzerschnittstelle geöffnet.
    
5. Melden Sie sich an der {{site.data.keyword.IBM_notm}} MQ-Konsole mit den folgenden Berechtigungsnachweisen an:

    1.	Benutzer: admin
    2.	Kennwort: passw0rd
    
Sie sind jetzt bereit, IBM MQ zu verwalten.

### Terminal zur Verwendung der {{site.data.keyword.containershort_notm}}-Befehlszeilenschnittstelle einrichten
{: #ibm_mq_containers_cli}

Mit der {{site.data.keyword.containershort_notm}}-Befehlszeilenschnittstelle können Sie Verwaltungsbefehle von {{site.data.keyword.IBM_notm}} MQ direkt in einem Container ausführen.

Führen Sie die folgenden Schritte aus, um ein Terminal für die Ausführung von `bx ic`-Befehlen zur Verwaltung von IBM MQ zu konfigurieren:

1.	Richten Sie die {{site.data.keyword.containershort_notm}}-Befehlszeilenschnittstelle ein. Weitere Informationen finden Sie auf [der Seite der {{site.data.keyword.Bluemix_notm}}-Befehlszeilenschnittstelle](https://plugins.ng.bluemix.net/ui/home.html). Folgen Sie den Anweisungen des neuesten {{site.data.keyword.containershort_notm}}-Plug-ins.

2.	Melden Sie sich von einem Terminal aus bei {{site.data.keyword.Bluemix_notm}} an. Führen Sie die folgenden Befehle aus:

    1. `    bx login
    `
    2. `    bx ic init
    `
    
3.	Ordnen Sie Ihrem Container eine Bash-Sitzung zu:

    `bx ic exec -it containername /bin/bash`
    
    Dabei steht 'containername' für den Namen des Containers.
    


### Terminal zur Verwendung der Docker-Befehlszeilenschnittstelle einrichten
{: #ibm_mq_docker_commands}

Mit der Docker-Befehlszeilenschnittstelle können Sie Verwaltungsbefehle von {{site.data.keyword.IBM_notm}} MQ direkt in einem Container ausführen.

Führen Sie die folgenden Schritte aus, um ein Terminal für die Ausführung von Docker-Befehlen zur Verwaltung von IBM MQ zu konfigurieren:

1.	Richten Sie die {{site.data.keyword.containershort_notm}}-Befehlszeilenschnittstelle ein. Weitere Informationen finden Sie auf [der Seite der {{site.data.keyword.Bluemix_notm}}-Befehlszeilenschnittstelle](https://plugins.ng.bluemix.net/ui/home.html). Folgen Sie den Anweisungen des neuesten {{site.data.keyword.containershort_notm}}-Plug-ins.

2.	Melden Sie sich von einem Terminal aus bei {{site.data.keyword.Bluemix_notm}} an. Führen Sie die folgenden Befehle aus:

    1. `    bx login
    `
    2. `    bx ic init
    `
    3. Kopieren Sie die Werte, die für die folgenden Umgebungsvariablen zur Verfügung gestellt werden: DOCKER_HOST, DOCKER_CERT_PATH und DOCKER_TLS_VERIFY.
    4. Überschreiben Sie die lokale Docker-Umgebung, indem Sie die folgenden Variablen festlegen, um eine Verbindung zu {{site.data.keyword.containershort_notm}} herzustellen:
        
        `export DOCKER_HOST=<your_host_value>`
        
        `export DOCKER_CERT_PATH=<your_cert_path_value>`
        
        `export DOCKER_TLS_VERIFY=1`

3.	Ordnen Sie Ihrem Container eine Bash-Sitzung zu:

    `bx ic exec -it containername /bin/bash`
    
    Dabei steht 'containername' für den Namen des Containers.
    
**Anmerkung:** Durch diese Option werden nur einige Docker-Befehle unterstützt.




### Standardinstallationswerte ändern
{: #ibm_mq_dev_default}

Beim Erstellen des Warteschlangenmanagers werden die Standardeinstellungen von {{site.data.keyword.IBM_notm}} MQ Developer verwendet. Diese Standardobjekte sollen Sie beim Schnelleinstieg in die Entwicklung von Anwendungen oder sogar beim Testen von {{site.data.keyword.IBM_notm}} MQ unterstützen. 

Der Warteschlangenmanager wird mit den folgenden Standardobjekten erstellt:

* Ein MQ-Benutzer **admin** mit Administratorberechtigung und dem Kennwort *passw0rd*.
*	Ein MQ-Anwendungsbenutzer **app** ohne Kennwort.
*	Ein Listener **DEV.LISTENER.TC**, der für das Binden an Port 1414/TCP konfiguriert ist.
*	Ein Kanal **DEV.APP.SVRCONN**, der für das Handhaben von MQ-Clientanwendungsverbindungen konfiguriert ist.
*	Ein Kanal **DEV.ADMIN.SVRCONN**, der für das Handhaben von MQ-Verwaltungsverbindungen konfiguriert ist.
*	Drei Warteschlangen **DEV.QUEUE.1**, **DEV.QUEUE.2** und **DEV.QUEUE.3**, in die Nachrichten gestellt werden können.
*	Eine Warteschlange **DEV.DEAD.LETTER.QUEUE** für nicht zustellbare Nachrichten.
*	Ein Basis-Topic **DEV.BASE.TOPIC** mit der Topic-Zeichenfolge */dev*.
*	Ein Kanalauthentifizierungssatz zum Blockieren aller Clientverbindungen, sofern sie nicht eine Verbindung über den Kanal **DEV.APP.SVRCONN** oder **DEV.ADMIN.SVRCONN** herstellen.
*	Ein Kanalauthentifizierungssatz zum Blockieren aller Benutzer mit Administratorberechtigungen, die eine Verbindung herstellen - außer über den Kanal **DEV.ADMIN.SVRCONN**.
*	Ein Kanalauthentifizierungssatz, der nur dem Benutzer 'admin' erlaubt, eine Verbindung zum Kanal **DEV.ADMIN.SVRCONN** herzustellen.
*	Verbindungsauthentifizierung mit der Anforderung an Verwaltungsanwendungen, gültige Berechtigungsnachweise beim Herstellen einer Verbindung zu übergeben und diese Benutzer-ID für Berechtigungsprüfungen anzunehmen.

Mit diesen Standardobjekten können Sie eine Clientanwendung verbinden, die Nachrichten in eine Warteschlange stellen oder von dort abrufen kann, oder Sie können Nachrichten eines Topics abonnieren bzw. Nachrichten an ein Topic publizieren.

Wenn Sie die erstellten Entwicklerstandardwerte anpassen möchten, können Sie die folgenden Umgebungsvariablen hinzufügen, wenn Sie Ihren Container aus dem Image 'ibm-mq' erstellen.

| Umgebungsvariable | Zweck | Standardwert |
|----------------------|---------|---------|
| MQ_ADMIN_PASSWORD | Durch Festlegen dieser Variable wird das Kennwort des erstellten MQ-Administrators vom Standardwert in einen von Ihnen gewählten Wert geändert. Das Kennwort muss mindestens acht Zeichen lang sein. Der Benutzername für den Benutzer mit Administratorberechtigung wurde als 'admin' festgelegt. | passw0rd |
| MQ_APP_PASSWORD | Durch Festlegen dieser Variable definieren Sie das Kennwort für den Anwendungsbenutzer. Der Benutzername für den Anwendungsbenutzer wurde als 'app' festgelegt. Wenn Sie diese Umgebungsvariable festlegen, müssen Sie die Benutzer-ID und das Kennwort angeben, wenn Sie MQ-Clients verbinden. <br> Das Kennwort muss mindestens acht Zeichen lang sein. |  |
| MQ_TLS_KEYSTORE | Legen Sie diese Umgebungsvariable auf die Position eines PKCS#12-Schlüsselspeichers fest, der das Zertifikat enthält, das die MQ-Webkonsole und der IBM MQ-Warteschlangenmanager für TLS-Operationen verwenden soll. <br> Wenn diese Umgebungsvariable festgelegt ist, werden die erstellten Entwicklerkanäle unter Verwendung der CipherSpec 'TLS_RSA_WITH_AES_256_GCM_SHA384' für TLS aktiviert. <br> **Anmerkung:** Sie müssen die Schlüsselspeicherdatei in Ihrem {{site.data.keyword.Bluemix_notm}}-Container verfügbar machen. Hierfür können Sie beim Erstellen des Containers einen Datenträger anhängen, der Ihren Schlüsselspeicher enthält. |  |
| MQ_TLS_PASSPHRASE | Durch Festlegen dieser Umgebungsvariable wird das Kennwort für den in der Umgebungsvariablen MQ_TLS_KEYSTORE angegebenen Schlüsselspeicher definiert. |  |
| MQ_DEV | Durch Festlegen dieser Umgebungsvariable wird gesteuert, ob die {{site.data.keyword.IBM_notm}} MQ Developer-Standardobjekte erstellt werden. | true |
| MQ_DISABLE_WEB_CONSOLE | Durch Festlegen dieser Umgebungsvariable wird die Konfiguration und der Start der Webkonsole inaktiviert, um die CPU/RAM-Auslastung Ihres Containers zu reduzieren. |  |
