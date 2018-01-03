---

copyright:
  years: 2017
lastupdated: "2017-11-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Einführung in das Image **ibmnode** {: #getting_started_node}

Das öffentliche {{site.data.keyword.IBM}} Node-Image (**ibmnode**) wird für {{site.data.keyword.containerlong_notm}} zur Verfügung gestellt.
{:shortdesc}

**Anmerkung:** Im {{site.data.keyword.IBM_notm}} Node-Image (**ibmnode**) ist keine Node.js-Beispiel-App enthalten. Wenn Sie einen Container aus diesem Image bereitstellen, wird der Container unverzüglich nach seiner Erstellung beendet, da kein aktiver Prozess vorhanden ist. Um dies zu vermeiden, verwenden Sie das {{site.data.keyword.IBM_notm}} Node-Image (**ibmnode**) als übergeordnetes Image und erstellen ein eigenes Image, zu dem Sie einen eigenen Node.js hinzufügen können. Weitere Informationen finden Sie unter [Image mit dem bereitgestellten Image erstellen](#creating_image).

# Funktionsweise {: #how_it_works}

Verwenden Sie das {{site.data.keyword.IBM_notm}} Node-Image (**ibmnode**) als übergeordnetes Image zum Erstellen eines eigenen Images mit Ihrem eigenen Node.js-App-Code.
{:shortdesc}

**Anmerkung:** Lesen Sie zunächst die Nutzungsbedingungen für die von IBM zertifizierten Images im Abschnitt 'Usage' in der [WebSphere Liberty Docker-Bibliothek![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://github.com/docker-library/docs/tree/master/websphere-liberty){: new_window}.

# Enthaltene Elemente {: #included}

* Ubuntu 14.04
* Pakete 'Python', 'Git' und 'build-essential' für apt-get
* {{site.data.keyword.IBM_notm}} Node SDK

    **Anmerkung:** Das Image 'ibmnode' ist mit verschiedenen Tags verfügbar, sodass Sie unterschiedliche Versionen von IBM Node SDK ausführen können.

    <table>
    <caption> Tabelle 1. Tags für verschiedene SDK-Versionen </caption>
      <tr>
        <th> Tag </th>
        <th> Beschreibung </th>
      </tr>
      <tr>
        <td> ibmnode:latest </td>
        <td> Die neueste Version von IBM Node, die der Verwendung von ibmnode:v4 entspricht. </td>
      </tr>
      <tr>
        <td> ibmnode:v4 </td>
        <td> IBM Node 4.4.0 </td>
      </tr>
      <tr>
        <td> ibmnode:v1.2 </td>
        <td> IBM Node 1.2.0.8 (0.12.10) </td>
      </tr>
      <tr>
        <td> ibmnode:v1.1 </td>
        <td> IBM Node 1.1.0.18 (0.10.43) </td>
      </tr>
    </table>


# Image mit dem bereitgestellten Image erstellen {: #creating_image}

Mit dem Image **ibmnode** als übergeordnetem Image können Sie ein untergeordnetes Image erstellen, das Ihren eigenen App-Code enthält. Verwenden Sie den Beispiel-App-Container `Hello World` mit **ibmnode** und das Modul 'Express Node' zum Erstellen eines Images auf Ihrem Computer. Fügen Sie das Image dem privaten {{site.data.keyword.registrylong_notm}}-Repository Ihrer Organisation hinzu und erstellen Sie damit einen Container. Anschließend können Sie die Dockerfile aus dem Beispielprogramm 'Hello World' verwenden, um ein anderes Image mit Ihrer eigenen App zu erstellen.
{:shortdesc}

Führen Sie zunächst die folgenden Schritte aus.

* Sie müssen folgende Tools lokal installieren:
  * [{{site.data.keyword.registrylong_notm}}-Plug-in für Cloud Foundry und die Docker-Befehlszeilenschnittstelle](/docs/containers/container_cli_cfic_install.html)
  * [Node.js-Quellcode ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://nodejs.org/en/download/){: new_window}
  * [npm ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://github.com/npm/npm){: new_window}
* [Sie müssen den Namensbereich des privaten {{site.data.keyword.registryshort_notm}}-Repositorys Ihrer Organisation kennen.](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__namespace)

    Beispiel

    registry.DomainName/<var class="keyword varname">namensbereich</var>

* [Sie benötigen eine IP-Adresse. ](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__ip_request)

Erstellen Sie ein Image, das auf dem Image **ibmnode** basiert und eine 'Hello World'-App enthält.
1.  Laden Sie die Datei [hellonode.zip ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](ftp://public.dhe.ibm.com/cloud/bluemix/containers/hellonode.zip){: new_window} herunter und extrahieren Sie sie.
1.  Öffnen Sie die Dockerfile mit einem Texteditor und aktualisieren Sie in der Anweisung FROM die Registry-URL für die Region, die Sie verwenden möchten.

    <ul>
    <li>Vereinigte Staaten (Süden)

        ```
		registry.ng.bluemix.net/ibmnode
		```
		{: codeblock}

    </li>
    <li>Vereinigtes Königreich

        ```
		registry.eu-gb.bluemix.net/ibmnode
		```
		{: codeblock}

      </li>
    </ul>

1.  Navigieren Sie in der Befehlszeilenschnittstelle in das Verzeichnis **app**.
1.  Führen Sie im Verzeichnis **app** die folgenden Befehle aus.
    1.  Erstellen Sie eine Datei 'package.json'.

        ```
        npm init
        ```
        {: pre}

        **Tipp:** Drücken Sie die Eingabetaste, um die Standardwerte der einzelnen Eingabeaufforderungen zu akzeptieren.

    2.  Installieren Sie Express, ein Framework für Node.js. Das Paket 'package.json' wird mit den Versionsinformationen für Express im Abschnitt 'dependencies' aktualisiert.

        ```
        npm install express -save
        ```
        {: pre}

1.  Navigieren Sie zurück zum Verzeichnis **hellonode**.
1.  Erstellen Sie Ihr Image und übertragen Sie es mit einer Push-Operation in die private {{site.data.keyword.registryshort_notm}}.*

    ```
    bx ic build -t registry.<domänenname>/<namensbereich>/hellonode .
    ```
    {: pre}

    **Tipp:** Führen Sie `bx ic namespace get` aus, um Ihre Namensbereichsinformationen abzurufen.  Geben Sie für _registry.DomainName_ die zuvor in der Dockerfile verwendete Region an.

    **Anmerkung:** \*In diesem Befehl können Sie `bx ic` durch `docker` ersetzen, wenn Sie [bei {{site.data.keyword.containershort_notm}} angemeldet sind](/docs/containers/container_cli_cfic_install.html#container_cli_login) und legen Sie Ihre Umgebungsvariablen so fest, dass sie native Docker-Befehle verwenden. Sie können native Docker-Befehle in allen Schritten verwenden, die in diesem Abschnitt mit einem Stern (*) markiert sind.

1.  Erstellen Sie einen Container von Ihrem Image.*

    ```
    bx ic run -p 3000 --name helloworld registry.<domänenname>/<namensbereich>/hellonode
    ```
    {: pre}

1.  Binden Sie eine öffentliche IP-Adresse an Ihren Container.

    ```
    bx ic ip bind <IP-adresse> helloworld
    ```
    {: pre}

    **Tipp:** Sie können Ihre öffentlichen IP-Adressen anzeigen, indem Sie `bx ic ip list` ausführen. Zum Anfordern einer neuen IP-Adresse führen Sie `bx ic ip request` aus.

1. Testen Sie die App in einem Browser, indem Sie **<var class="varname">IP-adresse</var>:3000** öffnen. Die Nachricht 'Hello World!' wird angezeigt.

Nachdem Sie nun das hellonode-Image erstellt haben, können Sie die Dockerfile so ändern, dass sie stattdessen Ihre eigene App enthält, und Sie können ein weiteres Image für diese App erstellen.

1.  Fügen Sie für die Anweisung ADD Ihren eigenen Anwendungscode hinzu.
1.  Aktualisieren Sie die Portinformationen für die Anweisung EXPOSE.
1.  Erstellen Sie für die Anweisung CMD einen Befehl zum Starten Ihrer App.


# **ibmnode**-Dockerfile - Referenz {: #reference_dockerfile}

Diese Dockerfile wurde verwendet, um das Image **ibmnode** im {{site.data.keyword.Bluemix_notm}}-Katalog zu erstellen. Die folgenden Informationen dienen nur zu Referenzzwecken. Die anderen Dateien, die in der Buildversion dieses Images enthalten sind, werden nicht zur Verfügung gestellt.
{:shortdesc}

```
FROM ubuntu:14.04

# Update base OS
RUN apt-get update &&\
DEBIAN_FRONTEND=noninteractive apt-get -y install \
git \
build-essential \
python && \
DEBIAN_FRONTEND=noninteractive apt-get upgrade -y &&\
apt-get clean &&\
rm -Rf /var/cache/*

# Enhance default password rules
RUN sed -i 's/^PASS_MAX_DAYS.*/PASS_MAX_DAYS 90/' /etc/login.defs
RUN sed -i 's/sha512/sha512 minlen=8/' /etc/pam.d/common-password

# Install Node SDK
ONBUILD COPY ./prereq/node.tar.gz /tmp/prereq/
ONBUILD RUN cd /usr/local &&\
tar --strip-components 1 -xzf /tmp/prereq/node.tar.gz &&\
rm -rf /tmp/prereq

#Documentation path for URL
LABEL doc.url="/docs/services/RegistryImages/ibmnode/index.html"
```
{: screen}
