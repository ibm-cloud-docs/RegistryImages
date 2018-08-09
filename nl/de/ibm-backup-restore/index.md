---

copyright:
  years: 2017, 2018
lastupdated: "2018-07-24"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip} 
{:table: .aria-labeledby="caption"}

# Einführung in das Image 'ibm-backup-restore'
{: #ibmbackup_restore_starter}

Das Image **ibm-backup-restore** enthält die vorinstallierten Pakete, die zum Sichern und Wiederherstellen von persistentem Speicher in {{site.data.keyword.containerlong}} erforderlich sind.
{:shortdesc}

## Funktionsweise 
{: #how_it_works}

Mit dem Image **ibm-backup-restore** können Sie eine einmalige oder geplante Sicherung für App-Daten erstellen, die in einem persistenten Datenträger (PV) in Ihrem Cluster gespeichert sind, oder App-Daten auf einen physischen Datenträger zurückschreiben. Wenn Sie Daten sichern und wiederherstellen möchten, stellen Sie einen Pod aus dem Image **ibm-backup-restore** bereit. Anschließend hängen Sie den Persistent Volume Claim (PVC) an, der den physischen Datenträger bindet, den Sie sichern möchten, oder den physischen Datenträger, den Sie für die Wiederherstellung Ihrer Daten in Ihrem Pod verwenden möchten. 

**Wohin gehen meine Daten? Wie kann ich auf sie zugreifen?** </br>
Die Daten, die Sie sichern, werden in einer {{site.data.keyword.cos_full_notm}}-Serviceinstanz gespeichert. Um auf den Service zuzugreifen, verwenden Sie Ihre {{site.data.keyword.cos_full_notm}}-Serviceberechtigungsnachweise als Umgebungsvariablen im Pod **ibm-backup-restore** oder bearbeiten Sie die Datei `config.conf` in dem aktiven Pod.

**Kann ich gesicherte Daten in einer anderen App oder in einem anderen physischen Datenträger wiederherstellen?** </br>
Ja, Sie können Ihre gespeicherten Daten aus der {{site.data.keyword.cos_full_notm}}-Serviceinstanz in einem physischen Datenträger in Ihrem Cluster wiederherstellen. Zum Wiederherstellen von Daten müssen Sie einen Wiederherstellungs-Pod aus dem Image **ibm-backup-restore** erstellen. Anschließend hängen Sie den Persistent Volume Claim (PVC) an, der den physischen Datenträger bindet, den Sie für Ihren Pod verwenden möchten.  

## Enthaltene Elemente 
{: #whats_included}

Jedes Image **ibm-backup-restore** enthält die folgenden Softwarepakete:

-   Alpine 3.7
-   Duplicity 0.7.10
-   python- und gpgme-Pakete


## {{site.data.keyword.cos_full_notm}}-Serviceinstanz einrichten 
{: #object_storage}

Erstellen und konfigurieren Sie eine {{site.data.keyword.cos_full_notm}}-Serviceinstanz als Repository für die Daten, die gesichert werden sollen.
{: shortdesc}

1. Stellen Sie eine {{site.data.keyword.cos_full_notm}}-Serviceinstanz bereit.
   1.  Öffnen Sie den [{{site.data.keyword.Bluemix_notm}}-Katalog](https://console.bluemix.net/catalog/services/cloud-object-storage).
   2.  Geben Sie einen Namen für Ihre Serviceinstanz ein, z. B. `cos-backup`, und wählen Sie **default** als Ihre Ressourcengruppe aus. 
   3.  Überprüfen Sie die [Planoptionen ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.ibm.com/cloud-computing/bluemix/pricing-object-storage#s3api) auf Preisinformationen und wählen Sie einen Plan aus. 
   4.  Klicken Sie auf **Erstellen**.
2. Rufen Sie die Berechtigungsnachweise für die {{site.data.keyword.cos_full_notm}}-Serviceinstanz ab.
   1.  Klicken Sie in der Navigation auf der Seite mit den Servicedetails auf **Serviceberechtigungsnachweise**.
   2.  Klicken Sie auf **Neuer Berechtigungsnachweis**. Ein Dialogfenster wird angezeigt. 
   3.  Geben Sie einen Namen für Ihre Berechtigungsnachweise ein.
   4.  Geben Sie unter **Lineare Konfigurationsparameter hinzufügen (optional)** den Eintrag `{"HMAC":true}` ein, um zusätzliche HMAC-Berechtigungsnachweise zu erstellen, die der Pod **ibm-backup-restore** für die HMAC-Authentifizierung mit dem {{site.data.keyword.cos_full_notm}}-Service verwendet. 
   5.  Klicken Sie auf **Hinzufügen**. Ihre neuen Berechtigungsnachweise werden in der Tabelle **Serviceberechtigungsnachweise** aufgelistet.
   6.  Klicken Sie auf **Berechtigungsnachweise anzeigen**. 
   7.  Notieren Sie sich die Werte für **access_key_id** und **secret_access_key** im Abschnitt **cos_hmac_keys**. 
3. Erstellen Sie Ihr erstes {{site.data.keyword.cos_full_notm}}-Bucket.
   1. Klicken Sie in der Navigation auf der Seite mit den Servicedetails auf **Buckets**. 
   2. Klicken Sie auf **Bucket erstellen**. Ein Dialogfenster wird angezeigt.
   3. Geben Sie einen eindeutigen Namen für das Bucket ein. Der Name muss innerhalb von {{site.data.keyword.cos_full_notm}} in allen Regionen und in allen {{site.data.keyword.Bluemix_notm}}-Konten eindeutig sein. 
   4. Wählen Sie in der Dropdown-Liste für die **Ausfallsicherheit** die Verfügbarkeitsstufe aus, die Sie für Ihre Daten benötigen. Weitere Informationen finden Sie in [{{site.data.keyword.cos_full_notm}}-Regionen und -Endpunkte](/docs/services/cloud-object-storage/basics/endpoints.html#select-regions-and-endpoints). 
   5. Ändern Sie den **Standort** in die Region, in der die Daten gespeichert werden sollen. Denken Sie daran, dass Ihre Daten aus rechtlichen Gründen nicht in jeder Region gespeichert werden dürfen.  
   6. Klicken Sie auf **Erstellen**. 
4. Rufen Sie den {{site.data.keyword.cos_full_notm}}-Hostnamen für Ihr Bucket ab. 
   1. Klicken Sie auf den Namen des Buckets, das Sie im vorherigen Schritt erstellt haben. 
   2. Klicken Sie in der Navigation auf der Seite mit den Servicedetails auf **Buckets** > **Konfiguration**. 
   3. Notieren Sie sich die öffentliche URL, die Sie für den Zugriff auf die Daten in Ihrem Bucket verwenden können. 


In der Dokumentation zu [{{site.data.keyword.cos_full_notm}}](/services/cloud-object-storage/about-cos.html#about-ibm-cloud-object-storage) finden Sie weitere Informationen zur Konfiguration Ihrer Serviceinstanz.

## Daten aus einem persistenten Datenträger sichern
{: #scheduled_backup}

Sie können eine einmalige oder geplante Sicherung für einen beliebigen persistenten Datenträger (PV) erstellen, der über einen Persistent Volume Claim (PVC) an Ihren App-Pod angehängt wird.  
{: shortdesc}

Im folgenden Beispiel wird erläutert, wie Sie einen Sicherungs-Pod aus dem Image **ibm-backup-restore** bereitstellen, einen vorhandenen physischen Datenträger mit einem PVC an den Sicherungs-Pod anhängen und die Daten aus dem physischen Datenträger in Ihrer {{site.data.keyword.cos_full_notm}}-Serviceinstanz sichern.  

Führen Sie zunächst die folgenden Schritte aus:

-   [Richten Sie eine {{site.data.keyword.cos_full_notm}}-Serviceinstanz ein](#object_storage). 
-   Installieren Sie die erforderlichen [CLIs](/docs/containers/cs_cli_install.html#cs_cli_install), um den Cluster zu erstellen und mit ihm zu arbeiten.
-   [Erstellen Sie einen Standardcluster](/docs/containers/cs_clusters.html#clusters_cli) oder verwenden Sie einen vorhandenen Cluster.
-   [Richten Sie die CLI auf Ihren Cluster aus](/docs/containers/cs_cli_install.html#cs_cli_configure).
-   [Erstellen Sie einen Persistent Volume Claim (PVC) und hängen Sie ihn an die App-Bereitstellung an](/docs/containers/cs_storage.html#create).

Gehen Sie wie folgt vor, um einen vorhandenen physischen Datenträger zu sichern: 

1. Rufen Sie den Namen des PVC ab, der den zu sichernden physischen Datenträger bindet. 
   ```
   kubectl get pvc
   ```
   {: pre}

2. Erstellen Sie einen Sicherungs-Pod aus dem Image **ibm-backup-restore**. Um auf die Daten im physischen Datenträger zugreifen zu können, müssen Sie den PVC, der den physischen Datenträger bindet, an Ihren Sicherungs-Pod anhängen. Im folgenden Beispiel wird ein Sicherungs-Pod erstellt, der eine tägliche inkrementelle Sicherung ausführt. Wenn Sie eine Sicherung mit anderen Einstellungen erstellen möchten, finden Sie weitere Informationen dazu in der vollständigen Liste von [Umgebungsvariablenoptionen](#reference_backup_restore).</br>
   ** Wichtig:** Das Image **ibm-backup-restore** muss in einem einzigen Pod implementiert werden und kann nicht als Teil einer Kubernetes-Bereitstellung verwendet werden.
   
   Um das Image anzuzeigen, geben Sie die globale Registry als Ziel an, indem Sie den Befehl `ibmcloud cr region-set global` ausführen. Führen Sie anschließend `ibmcloud cr images --include-ibm` aus, um öffentliche IBM Images aufzuführen.
   {: tip}
  
   ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: backuppod
   spec:
     containers:
     - image: registry.bluemix.net/ibm-backup-restore
       name: backupcontainer
       env:
       - name: OBJECTSTORAGE
         value: S3
       - name: ACCESS_KEY_ID
         value: '<Zugriffsschlüssel-ID>'
       - name: SECRET_ACCESS_KEY
         value: '<geheimer Zugriffsschlüssel>'
       - name: ENDPOINT
         value: '<regionaler Endpunkt>'
       - name: BUCKET_NAME
         value: '<Bucketname>'
       - name: BACKUP_DIRECTORY
         value: /myvol
       - name: BACKUP_NAME
         value: <Sicherungsname>
       - name: SCHEDULE_TYPE
         value: periodic
       - name: SCHEDULE_INFO
         value: daily
       - name: BACKUP_TYPE
         value: incremental
       command: ["/bin/bash", "./vbackup"]
       volumeMounts:
       - mountPath: /myvol
         name: backup-volume
     volumes:
     - name: backup-volume
       persistentVolumeClaim:
         claimName: <PVC-Name>  
   ```
   {: codeblock}
   
   <table>
   <caption>Komponenten der YAML-Datei</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Ideensymbol"/> Komponenten der YAML-Datei</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;Zugriffsschlüssel-ID&gt;</code></td>
     <td>Die Zugriffsschlüssel-ID, die Sie als Teil der Berechtigungsnachweise Ihrer {{site.data.keyword.cos_full_notm}}-Serviceinstanz abgerufen haben.</td>
     </tr>
     <tr>
     <td><code>&lt;geheimer Zugriffsschlüssel&gt;</code></td>
     <td>Der geheime Zugriffsschlüssel, den Sie als Teil der Berechtigungsnachweise Ihrer {{site.data.keyword.cos_full_notm}}-Serviceinstanz abgerufen haben.</td>
     </tr>
     <tr>
     <td><code>&lt;regionaler Endpunkt&gt;</code></td>
     <td>Die URL für den regionalen API-Endpunkt, den Sie für den Zugriff auf {{site.data.keyword.cos_full_notm}} in einer bestimmten Region verwenden. </td>
     </tr>
     <tr>
     <td><code>&lt;Bucketname&gt;</code></td>
     <td>Der Name des Buckets, das Sie zum Speichern Ihrer Sicherungen in {{site.data.keyword.cos_full_notm}} verwenden möchten. </td>
     </tr>
     <tr>
     <td><code>&lt;Sicherungsname&gt;</code></td>
     <td>Ein eindeutiger Name für das Objekt, das Ihre Sicherung im Bucket enthält. </td>
     </tr>
     <tr>
     <td><code>&lt;PVC-Name&gt;</code></td>
     <td>Der Name des PVC, der den zu sichernden physischen Datenträger bindet.</td>
     </tr>
     </tbody>
     </table>
    
3.  Erstellen Sie den Sicherungs-Pod und initiieren Sie eine Sicherung Ihrer PV-Daten. 

    ```
    kubectl apply -f backuppod.yaml
    ```
    {: pre}

4.  Stellen Sie sicher, dass der Pod ausgeführt wird.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}
    
5.  Stellen Sie sicher, dass die Sicherung erfolgreich durchgeführt wurde.
    ```
    kubectl logs backuppod
    ```
    {: pre}

6.  Überprüfen Sie die Sicherung in {{site.data.keyword.cos_full_notm}} in der {{site.data.keyword.Bluemix_notm}}-Benutzerschnittstelle.
    1.  Suchen Sie im {{site.data.keyword.Bluemix_notm}}-Dashboard die {{site.data.keyword.cos_full_notm}}-Serviceinstanz. 
    2.  Wählen Sie in der Navigation **Buckets** aus und klicken Sie auf das Bucket, das Sie in der Sicherungskonfiguration verwendet haben. Ihre Sicherung wird als Objekt in Ihrem Bucket angezeigt. 
    3.  Überprüfen Sie die komprimierten Dateien. Sie können die Datei `vol1.difftar.gz` herunterladen, die Datei extrahieren und die Sicherungsdaten überprüfen. </br> **Wichtig**: Wenn Sie Dateien aus {{site.data.keyword.cos_full_notm}} löschen oder ändern, können diese Dateien nicht wiederhergestellt werden.

Ihre Sicherung ist verfügbar. Wenn Sie Ihre Sicherung so konfiguriert haben, dass eine einmalige Gesamtsicherung erstellt wird, müssen Sie das Sicherungsscript jedes Mal ausführen, wenn Sie eine neue Sicherung erstellen wollen. Wenn Sie den Container so konfiguriert haben, dass er in regelmäßigen Abständen eine inkrementelle Sicherung ausführt, wird Ihre Sicherung wie geplant ausgeführt.

## Daten aus {{site.data.keyword.cos_full_notm}} in Ihrem Cluster wiederherstellen
{: #restore_script_cli}

Sie können Daten aus Ihrer {{site.data.keyword.cos_full_notm}}-Serviceinstanz auf einem physischen Datenträger in Ihrem Cluster wiederherstellen. 

Führen Sie zunächst die folgenden Schritte aus:

-   [Richten Sie die CLI auf Ihren Cluster aus](/docs/containers/cs_cli_install.html#cs_cli_configure).
-   [Erstellen Sie eine Sicherung für einen physischen Datenträger in Ihrem Cluster](#scheduled_backup).

Gehen Sie wie folgt vor, um Daten von {{site.data.keyword.cos_full_notm}} auf einem physischen Datenträger wiederherzustellen: 

1. Rufen Sie den Namen des PVC ab, der den physischen Datenträger bindet, auf dem Sie die Daten wiederherstellen möchten. 
   ```
   kubectl get pvc
   ```
   {: pre}

2. Erstellen Sie einen Wiederherstellungs-Pod aus dem Image **ibm-backup-restore**. Um Daten auf einem physischen Datenträger wiederherstellen zu können, müssen Sie den PVC, der den physischen Datenträger bindet, an den Wiederherstellungs-Pod anhängen. 
   
   ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: restorepod
   spec:
     containers:
     - image: registry.bluemix.net/ibm-backup-restore
       name: restorecontainer
       env:
       - name: OBJECTSTORAGE
         value: S3
       - name: ACCESS_KEY_ID
         value: '<Zugriffsschlüssel-ID>'
       - name: SECRET_ACCESS_KEY
         value: '<geheimer Zugriffsschlüssel>'
       - name: ENDPOINT
         value: '<regionaler Endpunkt>'
       - name: BUCKET_NAME
         value: '<Bucketname>'
       - name: RESTORE_DIRECTORY
         value: /myvol
       - name: BACKUP_NAME
         value: <Sicherungsname>
       command: ["/bin/sh", "./vrestore"]
       volumeMounts:
       - mountPath: /myvol
         name: restore-volume
     volumes:
     - name: restore-volume
       persistentVolumeClaim:
         claimName: <PVC-Name> 
   ```
   {: codeblock}
   
   <table>
   <caption>Komponenten der YAML-Datei</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Ideensymbol"/> Komponenten der YAML-Datei</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;Zugriffsschlüssel-ID&gt;</code></td>
     <td>Die Zugriffsschlüssel-ID, die Sie als Teil der Berechtigungsnachweise Ihrer {{site.data.keyword.cos_full_notm}}-Serviceinstanz abgerufen haben.</td>
     </tr>
     <tr>
     <td><code>&lt;geheimer Zugriffsschlüssel&gt;</code></td>
     <td>Der geheime Zugriffsschlüssel, den Sie als Teil der Berechtigungsnachweise Ihrer {{site.data.keyword.cos_full_notm}}-Serviceinstanz abgerufen haben.</td>
     </tr>
     <tr>
     <td><code>&lt;regionaler Endpunkt&gt;</code></td>
     <td>Die URL für den regionalen API-Endpunkt, den Sie für den Zugriff auf {{site.data.keyword.cos_full_notm}} in einer bestimmten Region verwenden. </td>
     </tr>
     <tr>
     <td><code>&lt;Bucketname&gt;</code></td>
     <td>Der Name des Buckets, das Sie zum Speichern Ihrer Sicherungen in {{site.data.keyword.cos_full_notm}} verwendet haben. </td>
     </tr>
     <tr>
     <td><code>&lt;Sicherungsname&gt;</code></td>
     <td>Der eindeutige Name für das Objekt, das Ihre Sicherung in dem Bucket enthält. Sie müssen den Namen verwenden, den Sie in der Sicherungskopie verwendet haben, um Ihre Daten in {{site.data.keyword.cos_full_notm}} zu speichern. </td>
     </tr>
     <tr>
     <td><code>&lt;PVC-Name&gt;</code></td>
     <td>Der Namen des PVC, der den physischen Datenträger bindet, auf dem Sie die Daten wiederherstellen möchten.</td>
     </tr>
     </tbody>
     </table>

3.  Erstellen Sie den Wiederherstellungs-Pod und beginnen Sie mit der Wiederherstellung Ihrer Daten.
    ```
    kubectl apply -f restorepod.yaml
    ```
    {: pre}
    
4.  Stellen Sie sicher, dass der Pod ausgeführt wird.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    restorepod        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    Der Pod führt den Wiederherstellungsbefehl aus und wird gestoppt. Die Nachricht _CrashLoopBackOff_ bedeutet, dass Kubernetes versucht, den Pod erneut zu starten.

5.  Entfernen Sie den Pod, um zu verhindern, dass er weitere Ressourcen verbraucht.

    ```
    kubectl delete -f restorepod.yaml
    ```
    {: pre}

6.  Stellen Sie sicher, dass Ihre Daten erfolgreich wiederhergestellt wurden.
    ```
    kubectl logs restorepod
    ```
    {: pre}

Ihre Sicherung wurde erfolgreich wiederhergestellt. Sie können jetzt den PVC, der den physischen Datenträger bindet, an einen anderen Pod in Ihrem Cluster anhängen, um auf die wiederhergestellten Dateien zugreifen zu können. Befinden sich unter den gesicherten Containerdaten Daten, für die die Berechtigungen eines Benutzers ohne Rootberechtigung erforderlich sind, müssen Sie die Berechtigungen von Benutzern ohne Rootberechtigung zu dem neuen Container hinzufügen. Weitere Informationen hierzu finden Sie unter [Zugriff für Benutzer ohne Rootberechtigung zu Datenträgern hinzufügen](../../../containers/container_volumes_ov.html#container_volumes_write).

## Sicherungen verschlüsseln 
{: #encrypting_backups}

Verschlüsseln Sie die Daten in Ihrer {{site.data.keyword.cos_full_notm}}-Instanz.

1.  Laden Sie <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Symbol für externen Link"></a> herunter, um einen Verschlüsselungsschlüssel zu erstellen.
2.  Erstellen Sie einen Verschlüsselungsschlüssel auf Ihrem lokalen Laufwerk. Sie können die Standardwerte übernehmen, indem Sie die Eingabetaste drücken.

    **Wichtig:** Notieren Sie sich die von Ihnen erstellte Kennphrase. Wenn Sie nicht mehr wissen, wie diese Kennphrase lautet, können die mit Ihrem Schlüssel verschlüsselten Informationen nicht mehr entschlüsselt werden.

    ```
    gpg --gen-key
    ```
    {: pre}

    Abhängig von der Version von <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Symbol für externen Link"></a> müssen Sie unter Umständen `gpg2` anstelle von `gpg` in Ihren Befehlen verwenden.

3.  Überprüfen Sie, ob der Schlüssel erstellt wurde.

    ```
    gpg --list-keys
    ```
    {: pre}

    ```
    $ gpg --list-keys
    /Users/Username/.gnupg/pubring.gpg
    ------------------------------------
    pub   2048R/XXXXXXXX 2016-10-27
    uid       [ultimate] Beispielname (Beispielschlüssel) <Beispiel-E-Mail-Adresse>
    sub   2048R/YYYYYYYY 2016-10-27
    ```
    {: screen}

4.  Exportieren Sie den Verschlüsselungsschlüssel mit dem Wert aus dem Schlüssel `sub`. Nennen Sie die Datei `encryption.asc`.

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    In diesem Beispiel hat der Unterschlüssel den Wert `YYYYYYYY`.

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  Erstellen Sie eine Umgebungsvariable für Ihren verschlüsselten Sicherungscontainer in einem lokalen Verzeichnis.

    ```
    touch <umgebungsvariablendateiname_für_sicherung>
    ```
    {: pre}

6.  Bearbeiten Sie die Podkonfigurationsdatei und fügen Sie die folgenden Felder hinzu. Geben Sie für die leeren Umgebungsvariablen die Werte aus den {{site.data.keyword.cos_full_notm}}-Berechtigungsnachweisen ein, die Sie zuvor notiert haben. Geben Sie die Anführungszeichen an, die in den Berechtigungsnachweisen verwendet werden. Geben Sie für **ENCRYPTION_PASSPHRASE** eine Kennphrase ein, um die Sicherung zu schützen. Bei dieser Kennphrase handelt es sich um eine andere Kennphrase als die, die Sie beim Erstellen des Verschlüsselungsschlüssel erstellt haben. Sie müssen diese Kennphrase einfügen, wenn Sie Daten sichern und Daten wiederherstellen.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: backuppod
    spec:
      containers:
      - image: registry.bluemix.net/ibm-backup-restore
        name: backupcontainer
        env:
        - name: OBJECTSTORAGE
          value: S3
        - name: ACCESS_KEY_ID
          value: '<Zugriffsschlüssel-ID>'
        - name: SECRET_ACCESS_KEY
          value: '<geheimer Zugriffsschlüssel>'
        - name: ENDPOINT
          value: '<regionaler Endpunkt>'
        - name: BUCKET_NAME
          value: '<Bucketname>'
        - name: BACKUP_DIRECTORY  
          value: /myvol
        - name: BACKUP_NAME
          value: <Sicherungsname> 
        - name: SCHEDULE_TYPE
          value: periodic
        - name: SCHEDULE_INFO
          value: daily
        - name: BACKUP_TYPE
          value: incremental
        - name: ENCRYPTION_REQUIRED
          value: yes
        - name: ENCRYPTION_PASSPHRASE
          value: <Kennphrase>
        volumeMounts:
        - mountPath: /myvol 
          name: backup-volume 
      volumes:
      - name: backup-volume
        persistentVolumeClaim:
          claimName: <PVC-Name>  
    ```
    {: codeblock}
   
    <table>
    <caption>Komponenten der YAML-Datei</caption>
    <thead>
    <th colspan=2><img src="../images/idea.png" alt="Ideensymbol"/> Komponenten der YAML-Datei</th>
    </thead>
     <tbody>
     <tr>
     <td><code>&lt;Zugriffsschlüssel-ID&gt;</code></td>
     <td>Die Zugriffsschlüssel-ID, die Sie als Teil der Berechtigungsnachweise Ihrer {{site.data.keyword.cos_full_notm}}-Serviceinstanz abgerufen haben.</td>
     </tr>
     <tr>
     <td><code>&lt;geheimer Zugriffsschlüssel&gt;</em></code></td>
     <td>Der geheime Zugriffsschlüssel, den Sie als Teil der Berechtigungsnachweise Ihrer {{site.data.keyword.cos_full_notm}}-Serviceinstanz abgerufen haben.</td>
     </tr>
     <tr>
     <td><code>&lt;regionaler Endpunkt&gt;</code></td>
     <td>Die URL für den regionalen API-Endpunkt, den Sie für den Zugriff auf {{site.data.keyword.cos_full_notm}} in einer bestimmten Region verwenden. </td>
     </tr>
     <tr>
     <td><code>&lt;Bucketname&gt;</code></td>
     <td>Der Name des Buckets, den Sie zum Speichern Ihrer Sicherungen in {{site.data.keyword.cos_full_notm}} verwenden möchten. </td>
     </tr>
     <tr>
     <td><code>&lt;Sicherungsname&gt;</code></td>
     <td>Ein eindeutiger Name für das Objekt, das Ihre Sicherung in dem Bucket enthält.</td>
     </tr>
     <tr>
     <td><code>&lt;Kennphrase&gt;</code></td>
     <td>Eine beliebige Zeichenfolge, die Sie für Ihre Sicherung verwenden möchten. Sie müssen diese Kennphrase einschließen, wenn Sie Ihre Daten wiederherstellen. </td>
     </tr>
     <tr>
     <td><code>&lt;PVC-Name&gt;</code></td>
     <td>Der Name des PVC, der den zu sichernden physischen Datenträger bindet. </td>
     </tr>
     </tbody>
     </table>
   
    Mit diesen Einstellungen wird eine tägliche inkrementelle Sicherung erstellt, die verschlüsselt ist. Wenn Sie eine Sicherung mit anderen Einstellungen erstellen möchten, finden Sie weitere Informationen dazu in der vollständigen Liste von [Umgebungsvariablenoptionen](#reference_backup_restore).
    
7.  Erstellen Sie den Sicherungs-Pod. 

    ```
    kubectl apply -f backuppod.yaml 
    ```
    {: pre}

8.  Stellen Sie sicher, dass der Pod ausgeführt wird.

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

9.  Kopieren Sie den Verschlüsselungsschlüssel in das Verzeichnis `/backup_restore` des Containers, das aus dem Image **ibm-backup-restore** erstellt wurde.

    ```
    kubectl cp ./encryption.asc <containername>:/backup_restore
    ```
    {: pre}

    Bewahren Sie eine Kopie des Verschlüsselungsschlüssels lokal auf, um Ihre Daten zu entschlüsseln.

10. Melden Sie sich bei dem Pod an und navigieren Sie zum Ordner `backup_restore`. 

    ```
    kubecl exec -it <Pod-Name> bash
    ```
    {: pre}

11. Stellen Sie sicher, dass die Datei `encryption.asc` in den Ordner `backup_restore` kopiert wird.

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

12. Führen Sie das Sicherungsscript aus dem Ordner backup_restore aus.

    ```
    ./vbackup &
    ```
    {: codeblock}

13. Prüfen Sie die Dateien in Ihrer {{site.data.keyword.cos_full_notm}}-Serviceinstanz, um zu bestätigen, dass Ihre Sicherung verschlüsselt ist. Die Dateien weisen nun die Dateierweiterung `.gpg` auf.

Ihre Sicherung ist verschlüsselt. Um die Dateien wiederherzustellen, führen Sie die Schritte unter [Daten aus {{site.data.keyword.cos_full_notm}} in einem PVC im Cluster wiederherstellen](#restore_script_cli) aus und schließen Sie die Datei `encryption.asc` in das Verzeichnis `backup_restore` des Pods ein, auf dem der Wiederherstellungsprozess ausgeführt wird. Wenn die Sicherung verschlüsselt ist, müssen Sie beim Erstellen des Wiederherstellungs-Pods die Umgebungsvariablen **ENCRYPTION_REQUIRED** und **ENCRYPTION_PASSPHRASE** angeben.

## Referenzinformationen zu Umgebungsvariablen 
{: #reference_backup_restore}

Sehen Sie sich die vollständige Liste der Felder an, die als Umgebungsvariablen übergeben oder in der Datei `config.conf` in einem aktiven Pod bearbeitet werden können. Jeder Wert, der als Umgebungsvariable übergeben wird, ersetzt den Wert in der Datei `config.conf`. Wenn Sie die Umgebungsvariablen für einen Pod überprüfen möchten, melden Sie sich mit dem Befehl `kubectl exec` am Pod an und führen Sie `env` aus.

|Schlüssel|Wertoptionen|
|---|-------------|
|ACCESS_KEY_ID|Die **access_key_id** (Zugriffsschlüssel-ID), die Teil der HMAC-Berechtigungsnachweise in {{site.data.keyword.cos_full_notm}} ist.|
|SECRET_ACCESS_KEY|Das Element **secret_access_key** (geheimer Zugrifsschlüssel) ist Teil der HMAC-Berechtigungsnachweise in {{site.data.keyword.cos_full_notm}}.|
|ENDPOINT|Der Hostname für den Zugriff auf die {{site.data.keyword.cos_full_notm}}-Bucketdaten.|
|BUCKET|Der Name des Buckets in {{site.data.keyword.cos_full_notm}}, in dem Ihre Sicherungsdaten gespeichert werden. |
{: caption="Tabelle 1. {{site.data.keyword.cos_full_notm}}-Variablen" caption-side="top"}

|Schlüssel|Wertoptionen|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: Standardwert. Absoluter Dateipfad des Verzeichnisses, an das der Datenträger angehängt ist. Die Daten werden aus diesem Verzeichnis gesichert. Verwenden Sie nicht das Verzeichnis backup_restore, da dieses Verzeichnis Dateien für die Sicherungs- und Wiederherstellungsprozesse enthält.|
|BACKUP_NAME|*volume_backup*: Standardwert. Wählen Sie einen Namen für die Sicherung aus.|
|BACKUP_TYPE|*full*: Standardwert. Es werden jedes Mal alle Dateien gesichert.<br/> *incremental*: Es werden nur neue oder geänderte Dateien gesichert. Wenn Sie *incremental* auswählen, müssen Sie Werte für **SCHEDULING_INFO** und **SCHEDULING_TYPE** auswählen.|
|SCHEDULE_TYPE|*none*: Standardwert. Es wird eine einmalige Sicherung erstellt.<br/> **Anmerkung:** Wenn Sie eine einmalige Sicherung erstellen möchten, wird Ihr Pod nach Abschluss der Sicherung aus dem Cluster entfernt.<br/> *periodic*: Ändern Sie den Wert in 'periodic', wenn Sicherungen zu geplanten Zeitpunkten erstellt werden sollen.|
|SCHEDULE_INFO|*hourly*: Es wird jede Stunde eine Sicherung erstellt.<br/>*daily*: Standardwert. Es wird jeden Tag eine Sicherung erstellt.<br/>*weekly*: Es wird jede Woche eine Sicherung erstellt. Sie müssen diese Variable einschließen, wenn Sie eine regelmäßige Aktualisierung planen.|
|EXCLUDE_DIRECTORIES|*none*: Standardwert. Geben Sie den absoluten Dateipfad der Verzeichnisse an, die bei der Sicherung ausgeschlossen werden sollen. Trennen Sie die Verzeichnisse durch ein Komma.|
{: caption="Tabelle 2. Sicherungsvariablen" caption-side="top"}

|Schlüssel|Wertoptionen|
|---|-------------|
|BACKUP_NAME|*volume_backup*: Standardwert. Geben Sie den Namen der Sicherung an, die über {{site.data.keyword.cos_full_notm}} wiederhergestellt werden soll.|
|RESTORE_DIRECTORY|*/backup*: Standardwert. Absoluter Dateipfad des Verzeichnisses, an das der Datenträger angehängt ist. Die Daten werden in dieses Verzeichnis wiederhergestellt. Verwenden Sie nicht das Verzeichnis `backup_restore`, da dieses Verzeichnis Dateien für die Sicherungs- und Wiederherstellungsprozesse enthält.|
|
{: caption="Tabelle 3. Wiederherstellungsvariablen" caption-side="top"}

|Schlüssel|Wertoptionen|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: Standardwert. Fügen Sie diese Umgebungsvariable ein, wenn Sie den Dateinamen des Verschlüsselungsschlüssels ändern oder wenn sich der Schlüssel in einem anderem Verzeichnis als dem Verzeichnis `backup_restore` befindet.|
|
|ENCRYPTION_REQUIRED|*no*: Standardwert.<br/> *yes*: Fügen Sie keine Umgebungsvariablen für die Verschlüsselung ein, wenn Ihre Sicherung nicht verschlüsselt werden soll. Nehmen Sie diesen Schlüssel mit dem Wert `yes` auf, wenn Ihre Sicherung verschlüsselt werden soll.|
|
|ENCRYPTION_PASSPHRASE|Schließen Sie eine Kennphrase ein, um eine Sicherung zu schützen. Bei dieser Kennphrase handelt es sich um eine andere Kennphrase als die, die Sie beim Erstellen des Verschlüsselungsschlüssel erstellt haben. Sie müssen diese Kennphrase einschließen, wenn Sie Daten sichern und Daten wiederherstellen.|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: Standardwert.<br/> *yes*: Fügen Sie diese Umgebungsvariable mit dem Wert `yes` ein, wenn Sie den Verschlüsselungsschlüssel direkt im Container generiert haben. Die Mehrzahl der Benutzer generiert den Schlüssel auf dem lokalen Computer und kopiert ihn in den Pod. In diesem Fall kann der Standardwert `no` beibehalten werden.|
|
{: caption="Tabelle 4. Verschlüsselungsvariablen" caption-side="top"}

