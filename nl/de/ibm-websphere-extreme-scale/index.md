---

copyright:
  years: 2017
lastupdated: "2017-10-30"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# Einführung in das Image **ibm-websphere-extreme-scale** 
{: #ibm-websphere-extreme-scale}

Die **ibm-websphere-extreme-scale**-Images werden für {{site.data.keyword.containerlong}} zur Verfügung gestellt.

## Funktionsweise 
{: #how_it_works}

Das IBM WebSphere&reg; eXtreme Scale-Image ist eine verteilte Speichercachefunktion innerhalb der Liberty-Laufzeitumgebung. Es stellt Liberty-Caching-Server, Tools für das Caching, eine Verwaltungskonsole und sofort einsatzfähige REST-Verwaltungsservices zur Verfügung, die auf den neuesten Branchenstandards und Spezifikationen basieren. Dieses Image basiert auf der Technologie der zentralen Bestandteile von IBM WebSphere eXtreme Scale. Die in diesem Image geladene Caching-Technologie bietet vollständige Unterstützung der JCache-Spezifikation \(JSR107\) und die entsprechende Compliance.

Die Verwendung des Images `ibm-websphere-extreme-scale` weist die folgenden Highlights auf: 

-   Einfach bereitzustellende, einfach zu konfigurierende verteilte Caching-Lösung auf der Grundlage der eXtreme Scale-Technologie
-   Einfache und benutzerfreundliche Verwaltungskonsole und REST-API
-   Voll anpassungsfähig zur Unterstützung aller Anwendungsfälle für eXtreme Scale-Caching
-   Ohne großen Aufwand konfigurierbares Image, das sich innerhalb weniger Minuten in Ihre Caching-Umgebung integrieren lässt
-   Konformität mit JCache zur Unterstützung der Caching-Spezifikation JSR107


## Enthaltene Elemente 
{: #whats_included}

Jedes Image **ibm-websphere-extreme-scale** bietet die folgenden Softwarepakete.

-   Ubuntu 16.04-Basisimage, 64-Bit
-   IBM WebSphere eXtreme Scale
-   IBM Java Runtime Environment 8.0

Welche Features jeweils in einem Image installiert sind, hängt davon ab, welchen Tag Sie auswählen. Die folgende Tabelle gibt an, welche Codeversion jedes einzelne Image **ibm-websphere-extreme-scale** enthält. Weitere Informationen zu diesen Features finden Sie in der Dokumentation zu [eXtreme Scale](http://www.ibm.com/support/knowledgecenter/SSTVLU).

|Tag|Beschreibung|
|---|-----------|
|**ibm-websphere-extreme-scale:latest**|Mit diesem Tag geben Sie das aktuellste IBM WebSphere eXtreme Scale-Image an.|
|**ibm-websphere-extreme-scale:8.6.1.x**|Mit diesem Tag geben Sie ein bestimmtes Release-Level von WebSphere eXtreme Scale an.|
{: caption="Tabelle 1. IBM WebSphere eXtreme Scale-Images" caption-side="top"}

## Nutzungsbeschränkungen 
{: #usage}

In der folgenden Tabelle sind die Beschränkungen für die kostenlose Nutzung des Images **ibm-websphere-extreme-scale** in {{site.data.keyword.Bluemix_notm}} angegeben.

|Umgebung|Nutzungsbeschränkungen|
|-----------|------------------|
|Entwicklung|Das Image `ibm-websphere-extreme-scale` lässt die unbegrenzte kostenlose Nutzung für die Entwicklung zu.|
|Produktion|Die Nutzung des Images **ibm-websphere-extreme-scale** im Produktionsbetrieb ist auf lizenzierte Benutzer von IBM WebSphere eXtreme Scale-Produkten beschränkt. Weitere Informationen zum Produktionseinsatz finden Sie unter [Preise anzeigen und kaufen](https://www-112.ibm.com/software/howtobuy/buyingtools/paexpress/Express?P0=E1&part_number=D56YTLL,D56YVLL,D572PLL,D572MLL,D0594LL,D059GLL&catalogLocale=en_US&Locale=en_US&country=USA&PT=jsp&CC=USA&VP=&TACTICS=&S_TACT=&S_CMP=&brand=SB05).|
{: caption="Tabelle 2. Nutzungsbeschränkungen für ibm-websphere-extreme-scale-Images in {{site.data.keyword.Bluemix_notm}}" caption-side="top"}

**Anmerkung:** Die Preisstruktur für das Image **ibm-websphere-extreme-scale** ist unabhängig von der Preisstruktur für die Container, die Sie in {{site.data.keyword.Bluemix_notm}} verwenden.

Lesen Sie zunächst die Nutzungsbedingungen für die von IBM zertifizierten Images im Abschnitt 'License' der [Dokumente mit Lizenzinformationen](http://www-03.ibm.com/software/sla/sladb.nsf/displaylis/A44EE6829150B2998525801700516718?OpenDocument).


## Einführung 
{: #get_started}

Sie können das Image **ibm-websphere-extreme-scale** wahlweise über die Konsole oder über die Befehlszeile bereitstellen.

Diese Prozedur enthält die folgenden Optionen für die Bereitstellung des Images über die Konsole oder über die Befehlszeile:

-   Wählen Sie das Image in der {{site.data.keyword.Bluemix_notm}}[-Konsole](#bmconsole) unter **Katalog** aus.
-   Starten Sie das Image über die CF-[Befehlszeile](#bmcommand). Führen Sie dazu den Befehl 'cf ic' aus. 

**Wichtig:** Bevor Sie beginnen, lesen Sie die Nutzungsbeschränkungen für **ibm-websphere-extreme-scale**-Images wie im vorhergehenden Abschnitt beschrieben.


### Bereitstellung über die {{site.data.keyword.Bluemix_notm}}-Konsole
{: #bmconsole}

   1.  Wählen Sie im Katalog **Container** und dann das Image **ibm-websphere-extreme-scale** aus, um mit der Erstellung Ihres Containers zu beginnen.
   2.  Wählen Sie die Version des Images **ibm-websphere-extreme-scale**, das Sie verwenden möchten, im Dropdown-Menü **TAG/VERSION** aus.
   3.  Erstellen Sie einen einzelnen Container.

        **Anmerkung:** Wenn Sie mehr als nur einen Container erstellen wollen, verwenden Sie hierbei nicht die Option **Skalierbar**, um zwei oder mehr Gruppen von eXtreme Scale-Cache-Membern zu erstellen. Wählen Sie vielmehr die Option **Einzeln** aus und erstellen Sie mehrere Container. Verbinden Sie dann über die grafische Benutzerschnittstelle des eXtreme Scale-Dashboards Cache-Member miteinander.

        Weitere Informationen hierzu finden Sie im Abschnitt [Einzelnen Container über das {{site.data.keyword.Bluemix_notm}}-Dashboard erstellen](/docs/containers/container_single_ui.html#gui).

        1.  Geben Sie im Fenster 'Container erstellen' unter **Containername** einen Containernamen an, zum Beispiel `wxs1`. Der Containername darf keine Leerzeichen oder andere nicht druckbare Zeichen enthalten.
        2.  Geben Sie bei **Größe** die Größe des Containers an, der zum Starten des Images 'ibm-eXtreme-scale' verwendet wird.
        3.  Geben Sie bei **Öffentliche IP-Adresse** die Option 'Öffentliche IP anfordern und binden' an.
        4.  Öffnen Sie das Fenster 'Erweiterte Optionen'. Erstellen Sie einen Datenträger, sodass die Daten der eXtreme Scale-Konfiguration erhalten bleiben, wenn zu einem späteren Zeitpunkt ein schrittweises Upgrade installiert wird. Beispiel:
            1. Klicken Sie unter 'Erweiterte Optionen' auf **Datenträger erstellen** und geben Sie in **Datenträgername** den Namen für den Datenträger an. Geben Sie z. B. 'vol-wxs1' ein.
            2. Klicken Sie auf **Neue Umgebungsvariable hinzufügen**. Das Image **ibm-websphere-extreme-scale** erfordert Umgebungsvariablen, die im Container gespeichert werden und auf die durch berechtigte Benutzer zugegriffen wird.

            <table>
            <caption>Tabelle 3. Umgebungsvariablen für das Image 'ibm-websphere-extreme-scale'</caption>
               <tr>
                 <th>Variablenname</th>
                 <th>Anzugebender Wert</th>
               </tr>
               <tr>
                 <td>SECRET_KEY</td>
                 <td>Der Wert des geheimen Schlüssels, z. B. <var class="keyword varname">s3cretKey!</var>. Diesen geheimen Schlüssel verwenden eXtreme Scale-Server, um interne Kommunikation an die diversen Member in einer Cache-Member-Gruppe zu senden. Weitere Informationen finden Sie im Abschnitt [Cache member group](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txscreategridtut1.html).</td>
               </tr>
               <tr>
                 <td>XSADMIN_PWD</td>
                 <td>Das Kennwort des 'xsadmin'-Benutzers, der sich an der grafischen Benutzerschnittstelle des eXtreme Scale-Dashboards anmeldet. Beispiel: <var class="keyword varname">xsadmin4Me!</var>.</td>
               </tr>
               </table>
  
              **Anmerkung:** Sie können das Kennwort und den geheimen Schlüssel ändern, nachdem der eXtreme Scale-Container gestartet wurde. Sowohl der geheime Schlüssel als auch das Kennwort müssen den folgenden Kennwortregeln entsprechen. Stellen Sie sicher, dass Sie beide auf der Grundlage der folgenden Regel definieren: 

              Kennwort und geheimer Schlüssel müssen jeweils mindestens eine Ziffer [`0-9`], einen Großbuchstaben [`A-Z`] sowie ein Sonderzeichen [`~!@#$%^&*()_-+=:;'[]{}|<>,.?/`] enthalten und sie müssen mindestens 10 Zeichen lang sein.

              Weitere Informationen zum Ändern des Kennworts enthält die [Dokumentation zur Verwaltung (Administering)](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txsadministerxsld.html).

              Der Port wird standardmäßig im Feld **Öffentlicher Port** hinzugefügt.

   4.  Klicken Sie auf **Erstellen**, um einen Container über das {{site.data.keyword.Bluemix_notm}}-Dashboard zu erstellen.
    
   5.  Nachdem der Container und die eXtreme Scale-Server vollständig gestartet wurden, melden Sie sich an der Dashboard-Benutzerschnittstelle von eXtreme Scale an und überprüfen Sie die Bereitstellung. Klicken Sie in der {{site.data.keyword.Bluemix_notm}}-Konsole auf einen Container, um dessen öffentliche IP anzuzeigen. Der Zugriff auf den Container von WebSphere eXtreme Scale kann über die folgende URL erfolgen:

    ```
    https://<öffentliche_IP>:9443
    ```
    {: pre}

   **Anmerkung:** Es kann zwischen 5 und 10 Minuten dauern, bis der Server vollständig gestartet wurde.


### Stellen Sie das Image über die Befehlszeile bereit.
{: #bmcommand}
 1.  Erstellen Sie einen Docker-Datenträger zum Speichern persistenter IBM WebSphere eXtreme Scale-Daten. Geben Sie zum Beispiel den folgenden Befehl ein:

  ```
  cf ic volume create <vol-wxs1>
  ```
  {: pre}

 2.  Geben Sie den folgenden Befehl ein, um Ihren IBM WebSphere eXtreme Scale-Container in {{site.data.keyword.Bluemix_notm}} zu starten:

    **Anmerkung:** Ersetzen Sie 'eigener_geheimer_schlüssel' und 'eigenes_Xsadmin-kennwort' durch Ihre eigenen Angaben, die den bereits erwähnten Kennwortregeln entsprechen müssen. 

   ```
   cf ic run -itdP --volume <vol-wxs1:/vol> --name <wxs1> -m 1024 -e SECRET_KEY=<eigener_geheimer_schlüssel> -e XSADMIN_PWD=<eigenes_Xsadmin-kennwort> registry.ng.bluemix.net/ibm-websphere-extreme-scale:latest
   ```
   {: pre}
        
     **Anmerkung:** Der Containername darf nur alphanumerische Zeichen enthalten und muss mit einem Buchstaben beginnen. Alternativ können Sie <code>-e XSLD_CONTAINER_ALIAS=<wxs1></code> festlegen, wenn der Name Ihres Containers andere nicht alphanumerische Zeichen beinhalten muss.

 3.  Fordern Sie eine öffentliche IP an, die für Ihren ersten IBM WebSphere eXtreme Scale-Container verwendet werden soll. Geben Sie den folgenden Befehl ein, damit die IP-Adresse ausgegeben wird:

   ```
   cf ic ip request
   ```
   {: pre}

 4.  Binden Sie Ihren WebSphere eXtreme Scale-Container mit dem folgenden Befehl an die von Ihnen angeforderte öffentliche IP-Adresse:

   ```
   cf ic ip bind <ANGEFORDERTE_IP> wxs1
   ```
   {: pre}

 5.  Stellen Sie durch Eingabe des folgenden Befehls sicher, dass der Container `wxs1` den Status 'Aktiv' aufweist:

   ```
   cf ic ps -a
   ```
   {: pre}

 6.  Geben Sie den folgenden Befehl ein, um eine Verbindung zum Container `wxs1` herzustellen und das Nanny-Protokoll zu überwachen:

   ```
   cf ic exec -it <wxs1> tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
        ```
   {: pre}

    **Anmerkung:** Es kann zwischen 5 und 10 Minuten dauern, bis der Server gestartet wurde.

 7.  Nachdem der Container und die eXtreme Scale-Server vollständig gestartet wurden, melden Sie sich an der Dashboard-Benutzerschnittstelle von eXtreme Scale an und überprüfen Sie die Bereitstellung:

   ```
   https://<ANGEFORDERTE_IP>:9443
   ```
   {: pre}


## Caching-Kapazität hinzufügen 
{: #caching}

1.  Stellen Sie ein weiteres Image über die Konsole oder die Befehlszeile bereit. Führen Sie hierzu dieselben Schritte wie zuvor aus.
2.  Nachdem der Container und die eXtreme Scale-Server vollständig gestartet wurden, melden Sie sich an der eXtreme Scale-Dashboard-Benutzerschnittstelle dieses Members an:

    ```
   https://<ANGEFORDERTE_IP>:9443
   ```
    {: pre}

3.  Öffnen Sie die Seite für Data Cache-Member und wählen Sie unter der Aktionsspalte**** für dieses Member die Option **Join** aus.
4.  Geben Sie die angeforderte öffentliche IP sowie den geheimen Schlüssel für das erste Member ein und geben Sie einen eindeutigen Namen für dieses Member in der Cachegruppe an.
5.  Klicken Sie auf **Join** und warten Sie, bis die Task abgeschlossen ist. Wenn die Task vollständig ausgeführt worden ist, werden auf der Seite für Data Cache-Member alle Member angezeigt.

## Schrittweises Upgrade durchführen 
{: #rolling_upgrade}

Die folgenden Schritte sind erforderlich, um ein schrittweises Upgrade von Containern durchzuführen, die `ibm-websphere-extreme-scale` in {{site.data.keyword.containershort_notm}} ausführen.

**Anmerkung:** Wenn Ihre eXtreme Scale-Cache-Member-Gruppe lediglich ein Member umfasst, entfernen Sie diesen {{site.data.keyword.IBM_notm}} Container und erstellen Sie ihn von Grund auf neu. Wenn Ihre eXtreme Scale-Cache-Member-Gruppe jedoch aus mindestens zwei oder mehr Membern besteht, mehrere Katalogserver für sie bereitgestellt sind und Sie die zwischengespeicherten Daten in {{site.data.keyword.containershort_notm}} aufbewahren wollen, führen Sie die nachfolgend genannten Schritte für ein schrittweises Upgrade aus, um jeglichen Verlust zwischengespeicherter Daten zu vermeiden. Führen Sie das Upgrade zuerst für Cache-Member aus, die nur über Container-Server verfügen, und fahren Sie danach mit dem Upgrade für Cache-Member fort, die auch Katalogserver besitzen.

1.  Starten Sie einen neuen Container mit dem Namen 'wxsmigrate' und verwenden Sie ihn für Upgrades aller vorhandenen eXtreme Scale-Container. Weitere Informationen finden Sie im Abschnitt [Einführung](#get_started) auf dieser Seite. Die aktuellsten komprimierten Dateien für die eXtreme Scale-Aktualisierung werden im Ordner /tmp Ihres neuen Containers 'wxsmigrate' bereitgestellt.
2.  Kopieren Sie die komprimierten eXtreme Scale-Dateien (.zip) für die Aktualisierung aus dem Container 'wxsmigrate' auf Ihr lokales System, auf dem Sie das Plug-in für die {{site.data.keyword.containershort_notm}}-Befehlszeilenschnittstelle ausführen:

    ```
    cf ic cp wxsmigrate:/tmp/xsld-bin-update.zip
    ```
    {: pre}
    
    ```
    cf ic cp wxsmigrate:/tmp/xsld-derby-update.zip
    ```
    {: pre}

3.  Führen Sie für jedes Cache-Member innerhalb einer Cache-Member-Gruppe die folgenden Schritte aus, und zwar jeweils immer nur für einen Container:

    1. Kopieren Sie die komprimierten eXtreme Scale-Dateien (.zip) für die Aktualisierung von Ihrem lokalen System in den eXtreme Scale-Container, für den Sie ein Upgrade durchführen wollen:
    
      ```
      cf ic cp  xsld-bin-update.zip  <containername>:/tmp/xsld-bin-update.zip
      ```
      {: pre}
  
      ```
      cf ic cp  xsld-derby-update.zip  <containername>:/tmp/xsld-derby-update.zip
      ```
      {: pre}
     
    2. Stellen Sie eine Verbindung zu dem Container her, für den Sie ein Upgrade durchführen wollen: 
    
      ```
      cf ic exec -it <containername> bash
      ```
      {: pre}

    3. Stoppen Sie die XSLD-Server auf dem Container, der jetzt für das Upgrade bereit ist.
      
      ```
      /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
      ```
      {: pre}
      
      Warten Sie, bis die Server gestoppt worden sind.
      
    4. Dekomprimieren Sie den neuesten eXtreme Scale-Aktualisierungscode im Container:
      
      ```
      unzip  -o  /tmp/xsld-bin-update.zip   -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
      
      ```
      unzip  -o  /tmp/xsld-derby-update.zip -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
    
    5. Starten Sie den XSLD-Server erneut:
    
      ```
      /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
      ```
      {: pre}
      
    6. Überwachen Sie das Nanny-Protokoll, um sicherzustellen, dass die Server gestartet werden:
    
      ```
      tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
      ```
      {: pre}
      
4. Wenn das Upgrade auf allen IBM Containern abgeschlossen ist, entfernen Sie den Container 'wxsmigrate' wie folgt:

 ```
 cf ic rm -f <wxsmigrate>
 ```
 {: pre}

## Fehlerbehebung 
{: #troubleshoot}

###Fehlerbehebung bei der Cache-Member-Konnektivität, wenn die Hostzuordnung verloren gegangen ist 
{: #troubleshoot_cache}

Führen Sie die folgenden Schritte aus, wenn Member einer Cachegruppe die Konnektivität verlieren, weil eine Hostzuordnung zwischen Containern verloren gegangen ist.

1.  Stellen Sie zu jedem Container eine Verbindung her:

    ```
    cf ic exec -it <containername> bash
    ```
    {: pre}

2.  Ermitteln Sie für jedes Member die private IP, den Hostnamen und den Hostaliasnamen.

    Der Hostaliasname ist der Wert der Umgebungsvariablen 'XSLD_CONTAINER_ALIAS':

    ```
    echo ${<XSLD_CONTAINER_ALIAS>}
    ```
    {: pre}

    Die private IP-Adresse ist die IP-Adresse, die bei der Ausführung des Befehls 'host name -I' im Container selbst zurückgegeben wurde:

    ```
    hostname -I
    ```
    {: pre}

    Der Hostname ist der Name, der bei der Ausführung des Befehls 'hostname' im Container selbst zurückgegeben wurde:


3.  Führen Sie auf jedem Member das Script 'recoverXSLD.sh' aus und übergeben Sie dabei die Aliasnamen, IP-Adressen und Hostnamen aller übrigen Member:

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/recoverXSLD.sh <member2_alias> <member2_private_ip> <member2_hostname> ... <memberN_alias> <memberN_private_ip> <memberN_hostname>
    ```
    {: pre}

4.  Stoppen Sie die aktiven Server, indem Sie für jeden Container in der Cachegruppe den Befehl stopXSLD.sh ausführen. Führen Sie stopXSLD.sh wie folgt aus:

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
    ```
    {: pre}

5.  Wenn stopXSLD.sh bei dem Versuch, den Katalogserver zu stoppen, keinen Fortschritt macht, gehen Sie anhand der folgenden Schritte vor:
    1.  Listen Sie den Katalogserverprozess auf:

        ```
        ps -ef | grep cs-default
        ```
        {: pre}

    2.  Beenden Sie den Katalogserverprozess unter Verwendung der Prozess-ID:

        ```
        kill -9 <katalogserverprozess-ID>
        ```
        {: pre}

    3.  Führen Sie den Befehl stopXSLD.sh erneut aus:

        ```
        /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
        ```
        {: pre}

6.  Starten Sie XSLD ungefähr zur selben Zeit auf allen Containern. Führen Sie startXSLD.sh wie folgt aus:

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
    ```
    {: pre}


### Fehlerbehebung für eine gestoppte Derby-Replikationstask 
{: #troubleshoot_derby}

Wenn eine Derby-Replikationstask für mehr als zehn Minuten nicht mehr als 45% fortgeschritten ist, ist dies wahrscheinlich auf ein Netzproblem zurückzuführen. Führen Sie die folgenden Schritte aus, um die Ursache für das Problem zu suchen und den Fehler zu beheben:

1.  Brechen Sie die Derby-Replikationstask ab. Verwenden Sie hierzu ein REST POST-fähiges Dienstprogramm wie cURL.

    ```
    curl -X PUT --header "Content-Type: application/json" --header "Accept: application/json" "https://<ÖFFENTLICHE_IP>:9445/wxsadmin/v1/task/cancel/<DERBY-REPLIkATIONSTASK-ID>/force"
    ```
    {: pre}

    **Anmerkung:** Die <ÖFFENTLICHE_IP> ist die IP-Adresse, die im Befehl zum Starten der Derby-Replikationstask verwendet wurde, und die <DERBY-REPLIKATIONSTASK-ID> ist die Task-ID, die von diesem Befehl ausgegeben wurde.

2.  Führen Sie die Derby-Replikationstask erneut aus.

## Auf die REST-API zugreifen 
{: #api}

Wenn eXtreme Scale in {{site.data.keyword.containershort_notm}} bereitgestellt ist, führen Sie die folgenden Schritte aus, um auf die REST-API zuzugreifen:

1.  Rufen Sie den Hostnamen des Containers ab, den Sie für die Verbindung zur REST-API verwenden wollen.
    1.  Stellen Sie eine Verbindung zu dem Container her:

        ```
        cf ic exec -it <containername> bash
        ```
        {: pre}

    2.  Listen Sie den Hostnamen auf:

        ```
        hostname
        ```
        {: pre}

2.  Rufen Sie die öffentliche IP desselben Containers ab.

    Falls der Container keine öffentliche IP hat, führen Sie die Schritte im Abschnitt 'Einführung' aus, um den Container mithilfe der {{site.data.keyword.Bluemix_notm}} [Konsole](#bmconsole) oder [Befehlszeile](#bmcommand) mit einer öffentlichen IP auszustatten.

3.  Erstellen Sie einen Eintrag in der Hostdatei Ihres lokalen Clientcomputers, der die öffentliche IP-Adresse und den Namen des Container-Hosts einander zuordnet. Der Eintrag sieht wie folgt aus:

    ```
    169.44.8.87 instance-05106102
    ```
    {: pre}

    Typische Namen für den vollständigen Pfad für Hostdateien sind:

    -   Bei UNIX: /etc/hosts
    -   Bei Windows: C:\\Windows\\System32\\drivers\\etc\\hosts
4.  Greifen Sie in einem Browser auf die Swagger-REST-APIs zu. 
    -   Bei CRUD-Operationen:

        ```
        https://<hostname>:9444/ibm/api/explorer
        ```
        {: pre}

    -   Bei Verwaltungsoperationen:

        ```
        https://<hostname>:9445/ibm/api/explorer
        ```
        {: pre}


Beispiele und neue Aktualisierungen finden Sie unter [WebSphere eXtreme Scale in GitHub](https://github.com/ibmWebsphereExtremeScale). Weitere Hilfe bei der Bereitstellung von eXtreme Scale finden Sie in den folgenden unterstützenden Dokumentationen und Videos:

-   [Dokumentation zu IBM WebSphere eXtreme Scale](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/cxsgetstartxsld.html)
-   [XS developerWorks Forum](https://www.ibm.com/developerworks/community/forums/html/forum?id=11111111-0000-0000-0000-000000000778&ps=25)
-   [Getting started with WebSphere eXtreme Scale V8.6.x](https://www.youtube.com/watch?v=xw7Ck3c40rA)
-   [Adding cache with WebSphere eXtreme Scale - Liberty Deployment](https://www.youtube.com/watch?v=I1g88bnstn8)
-   [Setting up a WAS client to work with an WebSphere eXtreme Scale Liberty Deployment DynaCache grid](https://www.youtube.com/watch?v=RvhcknNX-4Q)
-   [Setting up a WAS client to work with an WebSphere eXtreme Scale Liberty Deployment Session Cache grid](https://www.youtube.com/watch?v=GKTy9I66Klc)

