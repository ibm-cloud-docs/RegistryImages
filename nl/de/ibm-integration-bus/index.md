---

copyright:
  years: 2016, 2017
lastupdated: "2017-11-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# Einführung in das Image *ibm-integration-bus*
{: #iib}

Das Image **ibm-integration-bus** wird für {{site.data.keyword.containerlong}} zur Verfügung gestellt. Es enthält die IBM Integration Bus for Developers-Edition.
{:shortdesc}


## Funktionsweise
{: #how_it_works}

IBM Integration Bus for Developers enthält alles, was Sie benötigen, um mit der Entwicklung Ihrer eigenen Integrationslösungen zu beginnen.
{:shortdesc}

Wenn Sie eine Integrationslösung erstellt haben, können Sie das Image **ibm-integration-bus** verwenden, um einen einzelnen Container in {{site.data.keyword.Bluemix_notm}} einzurichten. Anschließend können Sie Ihre Integrationslösung in diesem Container über die Webbenutzerschnittstelle oder ein Terminal bereitstellen.

**Anmerkung**: Sie können das Image **ibm-integration-bus** für die Entwicklung und für Komponententests verwenden. Sie können das Image außerdem verwenden, um das Produkt kennenzulernen, um Lernprogramme zu nutzen und um den Beitrag zu bewerten, den IBM Integration Bus für Ihre Organisation leisten kann.


## Enthaltene Elemente
{: #whats_included}

Das Image 'IBM Integration Bus' enthält das Softwarepaket für IBM Integration Bus for Developers Version 10 \(auch als Developer Edition bezeichnet\). Dabei handelt es sich um eine Produktversion mit vollem Funktionsumfang, die Sie für die Entwicklung und für Komponententests verwenden können. Sie können diese Version kostenlos herunterladen und sie gemäß den Lizenzbedingungen nutzen.
{:shortdesc}

IBM Integration Bus for Developers Version 10.0 ist für Windows™-64-Bit-Betriebssystemen und auf Linux™ on x86-64-Betriebssystemen verfügbar. Alle Produktvoraussetzungen sind im Downloadpaket enthalten.

Informationen zu den Features, die in IBM Integration Bus for Developers enthalten sind, finden Sie unter [IBM Integration Bus im IBM Knowledge Center ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.msgbroker.helphome.doc/help_home_msgbroker.htm){: new_window}.

Der Zugriff auf die Developer Edition ist über die [IBM Integration Bus for Developers-Downloadseite ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.ibm.com/marketing/iwm/iwm/web/pick.do?source=swg-wmbfd){: new_window} möglich.


## Nutzungsbeschränkungen
{: #usage}

In der folgenden Tabelle sind die Beschränkungen für die kostenlose Nutzung des Images **ibm-integration-bus** in {{site.data.keyword.Bluemix_notm}} angegeben.
{:shortdesc}

**Anmerkung**: Die Preisstruktur für das Image **ibm-integration-bus** ist unabhängig von der Preisstruktur für die Container, die Sie in {{site.data.keyword.Bluemix_notm}} verwenden.

|Umgebung|Beschränkungen für die kostenlose Nutzung|
|-----------|-----------------------|
|Entwicklung|**Uneingeschränkte** kostenlose Nutzung des Images **ibm-integration-bus** zur Erstellung von einzelnen Containern für die Entwicklung und den Komponententest.|
|Produktion|Developer Edition ist auf die Verarbeitung einer Transaktion pro Sekunde begrenzt. Der Einsatz in einer Produktionsumgebung wird nicht unterstützt.|
{: caption="Tabelle 1. Einschränkungen bei der kostenlosen Nutzung des Images 'ibm-integration-bus'" caption-side="top"}


## Lizenz
{: #license}

Informationen zu Lizenzen.
{:shortdesc}

*   Die Dockerfiles und die zugehörigen Scripts werden unter [Apache License 2.0![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link") ](http://www.apache.org/licenses/LICENSE-2.0.html){: new_window} lizenziert.
*   [Lizenzinformationen für IBM Integration Bus for Developers Version 10.0 ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](http://www.ibm.com/software/sla/sladb.nsf/displaylis/1BD32B141C9FDAE38525802C0055A956?OpenDocument){: new_window}.

    **Anmerkung**: Die Lizenz lässt keine weitere Verteilung zu. Die Bedingungen für IBM Integration Bus in dem Image schränken die Nutzung auf die Entwicklung und Komponententests ein.


## Einführung
{: #get_started}

Verwenden Sie das Image **ibm-integration-bus** aus dem {{site.data.keyword.Bluemix_notm}}-Katalog oder wählen Sie Ihr eigenes Image mit Produktionslizenz aus, um einen einzelnen Container zu erstellen.
{:shortdesc}

**Wichtig**: Bevor Sie beginnen, lesen Sie die [Nutzungseinschränkungen](#usage) für das Image **ibm-integration-bus**.

**Anmerkung**: Sie können native Docker-Befehle in allen Schritten verwenden, die mit einem Stern (\*) markiert sind.

   Führen Sie die folgenden Schritte aus, um IBM Integration Bus in einem Container auszuführen:

1.  [Über die {{site.data.keyword.Bluemix_notm}}-Benutzerschnittstelle](#provision) einen Container einrichten, der auf dem Image 'ibm-integration-bus' basiert.
2.  [Container und Installation von IBM Integration Bus überprüfen](#validate)
3.  [Nachrichtenfluss erstellen und bereitstellen](#createdeployflow)
4.  [IBM Integration Bus-Protokolle im Container überwachen](#monitor)
5.  [Im Container ausgeführte IBM Integration Bus-Ressourcen verwalten](#manage)


## Container einrichten, der auf dem Image **ibm-integration-bus** basiert
{: #provision}

Richten Sie einen Docker-Container in {{site.data.keyword.Bluemix_notm}} ein, der auf dem Image **ibm-integration-bus** basiert, das von {{site.data.keyword.IBM_notm}} zur Verfügung gestellt wird.
{:shortdesc}

  Führen Sie die folgenden Schritte aus:

1.  Melden Sie sich bei {{site.data.keyword.Bluemix_notm}} an.
2.  Wählen Sie im Katalog **Container** und dann das Image **ibm-integration-bus** aus, aus dem Sie den Container erstellen wollen.
3.  Wählen Sie **Einzeln** aus, um eine einzelne Instanz eines Containers zu erstellen, der für Entwicklungs- und Testzwecke verwendet werden kann.
4.  Geben Sie den Namen des Containers ein, z. B. `iib`.
5.  Wählen Sie die Größe des Containers aus.
6.  Wählen Sie im Feld **Öffentliche IP-Adresse** die Option *Öffentliche IP anfordern und binden* aus.
7.  Geben Sie im Feld  **Öffentliche Ports** die Nummern `4414, 7800` ein.
8.  Erweitern Sie **Erweiterte Optionen** und klicken Sie auf **Neue Umgebungsvariablen hinzufügen**.
9.  Fügen Sie die folgenden Umgebungsvariablen hinzu:

    <ul>
      <li>LICENSE mit dem Wert <i>accept</i>
      <li>NODENAME mit dem Wert &lt;MYNODE&gt; &lpar;wobei &lt;MYNODE&gt; der von Ihnen gewählte Knotennamen ist&rpar;</li>
      <li>LOG_LOCATIONS mit dem Wert <i>/var/log/syslog</i>
    </ul>

    **Anmerkung**: Wenn Sie keine Werte für die Eigenschaften LICENSE und NODENAME angeben, wird der Container erstellt, aber nicht gestartet.

    Die Eigenschaft LOG_LOCATIONS gibt die Protokolle im Container an, die Sie über die {{site.data.keyword.Bluemix_notm}}-Benutzerschnittstelle zugänglich machen wollen.
10. Klicken Sie auf **Erstellen** und warten Sie, bis der Container gestartet wird.


## Container und Installation von IBM Integration Bus überprüfen
{: #validate}

Wenn der Container, in dem IBM Integration Bus ausgeführt wird, in {{site.data.keyword.Bluemix_notm}} bereitgestellt wurde, überprüfen Sie den Status des Containers und validieren Sie die Installation von IBM Integration Bus.
{:shortdesc}

  Führen Sie die folgenden Schritte aus, um die Einrichtung und Konfiguration von IBM Integration Bus im Container zu überprüfen:
  
1.  Richten Sie die {{site.data.keyword.containershort_notm}}-Befehlszeilenschnittstelle ein, wie dies im folgenden Abschnitt beschrieben ist: [{{site.data.keyword.containershort_notm}}-Plug-in \(`bx ic`\) zur Verwendung der nativen Docker-CLI installieren](/docs/containers/container_cli_cfic_install.html).

2.  Melden Sie sich von einem Terminal aus bei {{site.data.keyword.Bluemix_notm}} an. Führen Sie die folgenden Befehle aus:

    <ol>
       <li>
          <pre class="codeblock">bx login</pre>
       </li>
       <li>
          <pre class="codeblock">bx ic init</pre>
       </li>
    </ol>

3.  Überprüfen Sie den Status des Containers.\*

    ```
    bx ic ps
    ```
    {: pre}

    **Anmerkung**: In diesem Befehl können Sie **bx ic** durch **docker** ersetzen, wenn Sie [sich bei {{site.data.keyword.containershort_notm}} anmelden](/docs/containers/container_cli_cfic_install.html#container_cli_login) und die Umgebungsvariablen für die Verwendung von nativen Docker-Befehlen einstellen. Sie können native Docker-Befehle in allen Schritten verwenden, die mit einem Stern (\*) markiert sind.

    ```
    $ bx ic ps
    CONTAINER ID        IMAGE                                                       COMMAND             CREATED             STATUS                 PORTS                                                        NAMES
    46d9ec1e-952        registry.ng.bluemix.net/ibm-integration-bus:latest   ""                  2 minutes ago       Running a minute ago   xxx.xxx.xxx.xxx:4414->4414/tcp, xxx.xxx.xxx.xxx:7800->7800/tcp   iib
    ```
    {: screen}

4.  Ordnen Sie Ihrem Container eine Bash-Sitzung zu.\*

    ```
    bx ic exec -it <containername> /bin/bash
    ```
    {: pre}

    Dabei steht *containername* für den Namen des Containers.

    Beispiel:

    ```
    $ bx ic exec -it iib /bin/bash
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

5.  Initialisieren Sie die Umgebung, indem Sie den Befehl **mqsiprofile** ausführen. Eine Beschreibung finden Sie unter [Befehlsumgebung: Linux- und UNIX™-Systeme ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](http://www.ibm.com/support/knowledgecenter/SSMKHH_9.0.0/com.ibm.etools.mft.doc/an26230_.htm){: new_window}.

    Der Befehl zum Initialisieren der Umgebung steht im folgenden Verzeichnis zur Verfügung: `/opt/ibm/iib-10.0.0.6/server/bin/`

    Beispiel:

    ```
    (IIB_10:)iibuser@instance-0098858e:/$ . ./opt/ibm/iib-10.0.0.6/server/bin/mqsiprofile

    MQSI 10.0.0.6
    /opt/ibm/iib-10.0.0.6/server

    # Check the environment variable <MQSI_WORKPATH> is set
    (IIB_10:)iibuser@instance-0098858e:/$ echo $<MQSI_WORKPATH>
    /var/mqsi
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

6.  Führen Sie den Befehl **mqsilist** aus, um den Status Ihres Knotens anzuzeigen, der als `Aktiv` aufgelistet werden sollte. Beispiel:

    ```
    (IIB_10:)iibuser@instance-0098858e:/$ mqsilist
    BIP1325I: Integration node 'MYNODE' with administration URI 'http://instance-0098858e:4414' is running.
    BIP8071I: Successful command completion.
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

Ihr Container wird jetzt ausgeführt und Sie können Integrationslösungen mit einer der unterstützten Methoden in diesem Container bereitstellen.


## IBM Integration Bus-Protokolle im Container überwachen
{: #monitor}

Sie können die Protokolle über die {{site.data.keyword.Bluemix_notm}}-Benutzerschnittstelle oder über die Befehlszeile überwachen.
{:shortdesc}

Konfigurieren Sie zuvor die {{site.data.keyword.containershort_notm}}-Befehlszeilenschnittstelle, um die Protokolle über die Befehlszeile zu überwachen. Weitere Informationen finden Sie im Abschnitt [{{site.data.keyword.containershort_notm}}-Plug-in einrichten (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

Das Image **ibm-integration-bus** ist so konfiguriert, dass IBM Integration Bus-Nachrichten in `/var/log/syslog` in dem Container ausgegeben werden.

Wählen Sie eine der folgenden Möglichkeiten aus, um IBM Integration Bus-Protokolle zu überwachen:

*  Wenn Sie beim Bereitstellen des Containers die Umgebungsvariable LOG_LOCATIONS konfiguriert haben, können Sie die Protokolle über die {{site.data.keyword.Bluemix_notm}}-Benutzerschnittstelle überwachen.

    Führen Sie die folgenden Schritte aus, um auf die Protokolle zuzugreifen:

    <ol>
       <li>Wählen Sie auf dem Container-Dashboard den Container aus, den Sie überwachen wollen.</li>
       <li>Wählen Sie <b>Überwachung und Protokollierung</b> aus.</li>
       <li>Wählen Sie <b>Protokollierung</b> aus. Das Dashboard listet die Protokolleinträge auf.</li>
       <li>Wenn Sie die Protokollansicht anpassen möchten, wählen Sie auf der Seite 'Protokollierung' die Option <b>Erweiterte Ansicht</b> aus, um Kibana zu öffnen. Weitere einführende Informationen finden Sie unter <a href="https://console.bluemix.net/docs/containers/container_planning.html#container_planning_monlog" target="_blank">Überwachung und Protokollierung</a>.
       </li>
    </ol>

*  Verwenden Sie Docker oder die {{site.data.keyword.containerlong_notm}}-Befehlszeilenschnittstelle von einem Terminal aus, um die Nachrichten zu überwachen. Führen Sie die folgenden Schritte aus:

    <ol>
       <li>
       Konfigurieren Sie Ihr Terminal.

        <ul>
        <li>Führen Sie die folgenden Schritte zum Einrichten der Umgebung aus, um die {{site.data.keyword.containerlong_notm}}-Befehlszeilenschnittstelle zum Verwalten von Containern in {{site.data.keyword.Bluemix_notm}} zu verwenden, während Sie die Docker-Befehlszeilenschnittstelle weiterhin direkt zum Verwalten des lokalen Docker-Hosts verwenden:
           <ol>
             <li>
               <pre class="codeblock">bx login</pre>
             </li>
             <li>
               <pre class="codeblock">bx ic init</pre>
             </li>
           </ol>
        </li>
        <li>Führen Sie die folgenden Schritte aus, um die Docker-Befehlszeilenschnittstelle zur Verwaltung von Containern in {{site.data.keyword.Bluemix_notm}} zu verwenden:
          <ol>
            <li>
              <pre class="codeblock">bx login</pre>
            </li>
            <li>
              <pre class="codeblock">bx ic init</pre>
               Kopieren Sie die Werte, die für die folgenden Umgebungsvariablen zur Verfügung gestellt werden: &lt;DOCKER_HOST&gt;, &lt;DOCKER_CERT_PATH&gt; und &lt;DOCKER_TLS_VERIFY&gt;.
            </li>
            <li>
            Überschreiben Sie die lokale Docker-Umgebung, indem Sie die folgenden Variablen festlegen, um eine Verbindung zu {{site.data.keyword.containerlong_notm}} herzustellen:
               <ul>
                 <li><pre class="codeblock">export DOCKER_HOST=&lt;ihr_hostwert&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;ihr_wert_für_den_zertifikatspfad&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
               </ul>
                <b>Anmerkung</b>: Durch diese Option werden nur einige Docker-Befehle unterstützt.
            </li>
          </ol>
        </li>
      </ul>
      </li>
      <li>
      Greifen Sie auf das Protokoll zu. Wählen Sie eine der folgenden Optionen aus:

      <ul>
         <li>Wenn Ihr Terminal für die Ausführung von {{site.data.keyword.containerlong_notm}}-CLI-Befehlen konfiguriert ist, führen Sie den folgenden Befehl aus:

            <pre class="codeblock">bx ic exec &lt;container-ID&gt; tail -f /var/log/syslog</pre>

            Dabei steht *container-ID* für den Namen des Containers.

            Beispiel:

            <pre class="screen">bx ic exec iib tail -f /var/log/syslog</pre>

          </li>
          <li>
           Wenn Ihr Terminal für die Ausführung von Docker-CLI-Befehlen konfiguriert ist, führen Sie den folgenden Befehl aus:

            <pre class="codeblock">docker exec &lt;container-ID&gt; tail -f /var/log/syslog</pre>

            Dabei steht *container-ID* für den Namen des Containers.

            Beispiel:

            <pre class="screen">docker exec iib tail -f /var/log/syslog</pre>

          </li>
        </ul>
       </li>
    </ol>


## Im Container ausgeführte IBM Integration Bus-Ressourcen verwalten
{: #manage}

Starten Sie die Webbenutzerschnittstelle von IBM Integration Bus oder führen Sie Befehle von einem Terminal aus, um IBM Integration Bus-Ressourcen zu verwalten, die in einem Container ausgeführt werden.
{:shortdesc}

  Wählen Sie eine der folgenden Optionen aus, um IBM Integration Bus-Ressourcen im Container zu verwalten:

*  [Webbenutzerschnittstelle von IBM Integration Bus starten](#launchwebUI) und IBM Integration Bus über eine grafische Oberfläche verwalten.
*  Ressourcen von IBM Integration Bus mit der Docker- oder der {{site.data.keyword.containershort_notm}}-Befehlszeilenschnittstelle über ein Terminal verwalten:
    *   [Verwaltungsbefehle mit der {{site.data.keyword.containerlong_notm}}-Befehlszeilenschnittstelle im Container ausführen](#admin_commands_containers_cli)
    *   [Verwaltungsbefehle mit der Docker-Befehlszeilenschnittstelle im Container ausführen](#admin_commands_docker_cli)


## Webbenutzerschnittstelle von IBM Integration Bus starten
{: #launchwebUI}

Starten Sie die Webbenutzerschnittstelle, um IBM Integration Bus-Ressourcen, die in einem Container ausgeführt werden, über Ihren Browser zu verwalten.
{:shortdesc}

Führen Sie die folgenden Schritte aus, um einen Browser mit Ihrem Host zu verbinden, den Sie anhand der Anweisungen unter [Einführung in das Image 'ibm-integration-bus' für {{site.data.keyword.Bluemix_notm}}](#get_started) zugänglich gemacht haben. Die Webbenutzerschnittstelle von IBM Integration Bus wird angezeigt.

1.  Wählen Sie Ihren Container in der {{site.data.keyword.Bluemix_notm}}-Benutzerschnittstelle aus und überprüfen Sie, ob für den Container der Status `Aktiv` angegeben ist.
2.  Ermitteln Sie die öffentliche IP durch eine Prüfung der Containerdetails.
3.  Öffnen Sie einen Web-Browser mit der folgenden URL:

    ```
    http://<öffentliche_ip_für_docker-container>:4414
    ```
    {: pre}

    Hierbei steht *öffentliche_ip_für_docker-container* für die öffentliche IP des Containers, den Sie im vorherigen Schritt überprüft haben.

4.  Im Browser wird die Webbenutzerschnittstelle geöffnet. Nun können Sie IBM Integration Bus verwalten.


## Verwaltungsbefehle mit der {{site.data.keyword.containerlong_notm}}-Befehlszeilenschnittstelle im Container ausführen
{: #admin_commands_containers_cli}

Mit der {{site.data.keyword.containershort_notm}}-Befehlszeilenschnittstelle können Sie Verwaltungsbefehle von IBM Integration Bus direkt in einem Container ausführen.
{:shortdesc}

Konfigurieren Sie zuvor die {{site.data.keyword.containershort_notm}}-Befehlszeilenschnittstelle, um Verwaltungsbefehle über die Befehlszeile auszuführen. Weitere Informationen finden Sie im Abschnitt [{{site.data.keyword.containershort_notm}}-Plug-in einrichten (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

  Führen Sie die folgenden Schritte aus, um ein Terminal für die Ausführung von **bx ic**-Befehlen zur Verwaltung von IBM Integration Bus zu konfigurieren:

1.  Melden Sie sich von einem Terminal aus bei {{site.data.keyword.Bluemix_notm}} an. Führen Sie die folgenden Befehle aus:

      <ol>
        <li>
           <pre class="codeblock">bx login</pre>
        </li>
        <li>
           <pre class="codeblock">bx ic init</pre>
        </li>
       </ol>
           
2.  Ordnen Sie Ihrem Container eine Bash-Sitzung zu, um eine interaktive Sitzung einzurichten.\*

    ```
    bx ic exec -it <containername> /bin/bash
    ```
    {: pre}

    Dabei steht *containername* für den Namen des Containers.

    Beispiel:

    ```
    bx ic exec -it iib /bin/bash
    ```
    {: screen}

    Sie können 'mqsiprofile' als Quelle verwenden und Ihre Befehle von dieser Position aus in einer Shell innerhalb des Containers ausführen.

3.  Alternativ können Sie mit dem Befehl **bx ic exec** eine nicht interaktive Bash-Sitzung ausführen, in der alle Befehle von IBM Integration Bus ausgeführt werden können.\*

    Beispiel:

    ```
    bx ic exec iib <containername> /bin/bash -c mqsilist
    ```
    {: screen}


## Verwaltungsbefehle mit der Docker-Befehlszeilenschnittstelle im Container ausführen
{: #admin_commands_docker_cli}

Mit der Docker-Befehlszeilenschnittstelle können Sie Verwaltungsbefehle von IBM Integration Bus direkt in einem Container ausführen.
{:shortdesc}

Konfigurieren Sie zuvor die {{site.data.keyword.containershort_notm}}-Befehlszeilenschnittstelle, um Verwaltungsbefehle über die Befehlszeile auszuführen. Weitere Informationen finden Sie im Abschnitt [{{site.data.keyword.containershort_notm}}-Plug-in einrichten (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

  Führen Sie die folgenden Schritte aus, um ein Terminal für die Ausführung von Docker-Befehlen zur Verwaltung von IBM Integration Bus zu konfigurieren:

1.  Melden Sie sich von einem Terminal aus bei {{site.data.keyword.Bluemix_notm}} an. Führen Sie die folgenden Befehle aus:

    <ol>
     <li>
       <pre class="codeblock">bx login</pre>
     </li>
     <li>
       <pre class="codeblock">bx ic init</pre>

      <p>Kopieren Sie die Werte, die für die folgenden Umgebungsvariablen zur Verfügung gestellt werden: &lt;DOCKER_HOST&gt;, &lt;DOCKER_CERT_PATH&gt; und &lt;DOCKER_TLS_VERIFY&gt;.</p>
      </li>
      <li>  Überschreiben Sie die lokale Docker-Umgebung, indem Sie die folgenden Variablen festlegen, um eine Verbindung zu {{site.data.keyword.containershort_notm}} herzustellen:
        <ul>
           <li><pre class="codeblock">export DOCKER_HOST=&lt;ihr_hostwert&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;ihr_wert_für_den_zertifikatspfad&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
        </ul>

          <p><b>Anmerkung</b>: Durch diese Option werden nur einige Docker-Befehle unterstützt.</p>
     </li>
    </ol>

2.  Ordnen Sie Ihrem Container eine Bash-Sitzung zu, um eine interaktive Sitzung einzurichten. Führen Sie den folgenden Befehl aus:

    ```
    docker exec -it <containername> /bin/bash
    ```
    {: pre}

    Dabei steht *containername* für den Namen des Containers.

    Beispiel:

    ```
    docker exec -it iib /bin/bash
    ```
    {: screen}

    Sie können 'mqsiprofile' als Quelle verwenden und Ihre Befehle von dieser Position aus in einer Shell innerhalb des Containers ausführen.

3.  Sie können 'mqsiprofile' als Quelle verwenden und Ihre Befehle von dieser Position aus in einer Shell innerhalb des Containers ausführen.

    Alternativ können Sie mit 'docker exec' eine nicht interaktive Bash-Sitzung ausführen, in der alle Befehle von IBM Integration Bus ausgeführt werden können. 
    
    Beispiel:

    ```
    docker exec iib <containername> /bin/bash -c mqsilist
    ```
    {: screen}


## Nachrichtenfluss erstellen und bereitstellen
{: #createdeployflow}

Zur Erstellung und Bereitstellung eines Nachrichtenflusses befolgen Sie die Anweisungen im Knowledge Center für IBM Integration Bus.
{:shortdesc}

Informationen zum Erstellen eines Nachrichtenflusses finden Sie unter [Wie erstelle und verwalte ich Nachrichtenflüsse? ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40150_.htm){: new_window}.

Informationen zum Bereitstellen eines Nachrichtenflusses finden Sie unter [Wie stelle ich Nachrichtenflüsse bereit und wie konfiguriere ich sie? ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40180_.htm){: new_window}.
