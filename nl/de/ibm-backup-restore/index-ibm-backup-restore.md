---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-04"

keywords: IBM Cloud Container Registry, IBM Cloud Kubernetes Service, ibm-backup-restore, container image, back up data, restore data

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

# Einführung zum Image `ibmcloud-backup-restore`
{: #ibmbackup_restore_starter}

Das Image `ibmcloud-backup-restore` enthält die vorinstallierten Pakete, die zum Sichern und Wiederherstellen von persistentem Speicher in {{site.data.keyword.containerlong}} erforderlich sind.
{:shortdesc}

Das Image `ibmcloud-backup-restore` ist nur in den Registry-Domänen `icr.io` verfügbar und in den Domänen `registry.bluemix.net` veraltet. Um auf das Image zuzugreifen und Container von diesem aus bereitzustellen, muss Ihr Cluster einen API-Schlüssel in einem geheimen Pull-Schlüssel für Images speichern, um den Zugriff auf {{site.data.keyword.registryshort_notm}} zu autorisieren. Bei Clustern, die vor dem 25. Februar 2019 erstellt wurden, müssen Sie Ihren Cluster aktualisieren, um Zugriff auf die Registry-Domänen `icr.io` zu erhalten, damit Sie das Image `ibmcloud-backup-restore` verwenden können. Weitere Informationen zu den Änderungen und zur Aktualisierung Ihres Clusters finden Sie in [Grundlegendes zum Autorisieren Ihres Clusters zum Abrufen von Images aus einer Registry](/docs/containers?topic=containers-images#cluster_registry_auth) und [Vorhandene Cluster für die Verwendung des API-Schlüssels in einem geheimen Pull-Schlüssel für Images aktualisieren](/docs/containers?topic=containers-images#imagePullSecret_migrate_api_key). 
{: important}

Sie können auf die von {{site.data.keyword.IBM_notm}} bereitgestellten Images über die Befehlszeile zugreifen. Informationen hierzu finden Sie in [öffentliche IBM Images](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Funktionsweise
{: #backup_restore_how_it_works}

Mit dem Image `ibmcloud-backup-restore` können Sie eine einmalige oder geplante Sicherung für App-Daten erstellen, die in einem persistenten Datenträger (PV) in Ihrem Cluster gespeichert sind, oder App-Daten auf einen physischen Datenträger zurückschreiben. Wenn Sie Daten sichern und wiederherstellen möchten, stellen Sie einen Pod aus dem Image `ibmcloud-backup-restore` bereit. Anschließend hängen Sie den Persistent Volume Claim (PVC) an, der den physischen Datenträger bindet, den Sie sichern möchten, oder den physischen Datenträger, den Sie für die Wiederherstellung Ihrer Daten in Ihrem Pod verwenden möchten.

**Wohin gehen meine Daten? Wie kann ich auf sie zugreifen?**

Die Daten, die Sie sichern, werden in einer {{site.data.keyword.cos_full_notm}}-Serviceinstanz gespeichert. Um auf den Service zuzugreifen, verwenden Sie Ihre {{site.data.keyword.cos_full_notm}}-Serviceberechtigungsnachweise als Umgebungsvariablen im Pod `ibmcloud-backup-restore` oder bearbeiten Sie die Datei `config.conf` in dem aktiven Pod.

**Kann ich gesicherte Daten in einer anderen App oder in einem anderen physischen Datenträger wiederherstellen?**

Ja, Sie können Ihre gespeicherten Daten aus der {{site.data.keyword.cos_full_notm}}-Serviceinstanz in einem physischen Datenträger in Ihrem Cluster wiederherstellen. Zum Wiederherstellen von Daten müssen Sie einen Wiederherstellungs-Pod aus dem Image `ibmcloud-backup-restore` erstellen. Anschließend hängen Sie den Persistent Volume Claim (PVC) an, der den physischen Datenträger bindet, den Sie für Ihren Pod verwenden möchten.  

## Enthaltene Elemente
{: #backup_restore_whats_included}

Jedes Image `ibmcloud-backup-restore` enthält die folgenden Softwarepakete:

- Alpine 3.7
- Duplicity 0.7.10
- Python- und GPGME-Pakete (GnuPG Made Easy)

## {{site.data.keyword.cos_full_notm}}-Serviceinstanz einrichten
{: #backup_restore_setup_object_storage}

Erstellen und konfigurieren Sie eine {{site.data.keyword.cos_full_notm}}-Serviceinstanz als Repository für die Daten, die gesichert werden sollen.
{: shortdesc}

1. Stellen Sie eine {{site.data.keyword.cos_full_notm}}-Serviceinstanz bereit.
   1. Öffnen Sie den [{{site.data.keyword.cloud_notm}}-Katalog ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://cloud.ibm.com/catalog/services/cloud-object-storage).
   2. Geben Sie einen Namen für Ihre Serviceinstanz ein, z. B. `cos-backup`, und wählen Sie **default** als Ihre Ressourcengruppe aus.
   3. Überprüfen Sie die [Planoptionen ![Symbol für externen Link](../../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.ibm.com/cloud-computing/bluemix/pricing-object-storage#s3api) auf Preisinformationen und wählen Sie einen Plan aus.
   4. Klicken Sie auf **Erstellen**.
2. Rufen Sie die Berechtigungsnachweise für die {{site.data.keyword.cos_full_notm}}-Serviceinstanz ab.
   1. Klicken Sie in der Navigation auf der Seite mit den Servicedetails auf **Serviceberechtigungsnachweise**.
   2. Klicken Sie auf **Neuer Berechtigungsnachweis**. Ein Dialogfenster wird angezeigt.
   3. Geben Sie einen Namen für Ihre Berechtigungsnachweise ein.
   4. Geben Sie im Feld **Lineare Konfigurationsparameter hinzufügen (optional)** den Eintrag `{"HMAC":true}` ein, um zusätzliche HMAC-Berechtigungsnachweise zu erstellen, die der Pod `ibmcloud-backup-restore` für die HMAC-Authentifizierung mit dem {{site.data.keyword.cos_full_notm}}-Service verwendet.
   5. Klicken Sie auf **Hinzufügen**. Ihre neuen Berechtigungsnachweise werden in der Tabelle **Serviceberechtigungsnachweise** aufgelistet.
   6. Klicken Sie auf **Berechtigungsnachweise anzeigen**.
   7. Notieren Sie sich die Werte für **access_key_id** und **secret_access_key** im Abschnitt **cos_hmac_keys**.
3. Erstellen Sie Ihr erstes {{site.data.keyword.cos_full_notm}}-Bucket.
   1. Klicken Sie in der Navigation auf der Seite mit den Servicedetails auf **Buckets**.
   2. Klicken Sie auf **Bucket erstellen**. Ein Dialogfenster wird angezeigt.
   3. Geben Sie einen eindeutigen Namen für das Bucket ein. Der Name muss innerhalb von {{site.data.keyword.cos_full_notm}} in allen Regionen und in allen {{site.data.keyword.cloud_notm}}-Konten eindeutig sein.
   4. Wählen Sie in der Liste für die **Ausfallsicherheit** die Verfügbarkeitsstufe aus, die Sie für Ihre Daten benötigen. Weitere Informationen finden Sie in [{{site.data.keyword.cos_full_notm}}-Regionen und -Endpunkte](/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-endpoints#endpoints).
   5. Ändern Sie den **Standort** in die Region, in der die Daten gespeichert werden sollen. Denken Sie daran, dass Ihre Daten aus rechtlichen Gründen nicht in jeder Region gespeichert werden dürfen.  
   6. Klicken Sie auf **Erstellen**.
4. Rufen Sie den {{site.data.keyword.cos_full_notm}}-Hostnamen für Ihr Bucket ab.
   1. Klicken Sie auf den Namen des Buckets, das Sie im vorherigen Schritt erstellt haben.
   2. Klicken Sie in der Navigation auf der Seite mit den Servicedetails auf **Buckets** > **Konfiguration**.
   3. Notieren Sie sich die öffentliche URL, die Sie für den Zugriff auf die Daten in Ihrem Bucket verwenden können.

In der Dokumentation zu [{{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage?topic=cloud-object-storage-about#about) finden Sie weitere Informationen zur Konfiguration Ihrer Serviceinstanz.

## Daten aus einem persistenten Datenträger sichern
{: #backup_restore_scheduled_backup}

Sie können eine einmalige oder geplante Sicherung für einen beliebigen persistenten Datenträger (PV) erstellen, der über einen Persistent Volume Claim (PVC) an Ihren App-Pod angehängt wird.  
{: shortdesc}

Im folgenden Beispiel wird erläutert, wie Sie einen Sicherungs-Pod aus dem Image `ibmcloud-backup-restore` bereitstellen, einen vorhandenen physischen Datenträger mit einem PVC an den Sicherungs-Pod anhängen und die Daten aus dem physischen Datenträger in Ihrer {{site.data.keyword.cos_full_notm}}-Serviceinstanz sichern.  

**Vorbereitende Schritte**

- [Richten Sie eine {{site.data.keyword.cos_full_notm}}-Serviceinstanz ein](#backup_restore_setup_object_storage).
- Installieren Sie die erforderlichen [CLIs](/docs/containers?topic=containers-cs_cli_install#cs_cli_install), um den Cluster zu erstellen und mit ihm zu arbeiten.
- [Erstellen Sie einen Standardcluster](/docs/containers?topic=containers-clusters#clusters_cli) oder verwenden Sie einen vorhandenen Cluster.
- [Richten Sie die CLI auf Ihren Cluster aus](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure).
- Erstellen Sie einen Persistent Volume Claim (PVC) für den [Dateispeicher](/docs/containers?topic=containers-file_storage#add_file) oder den [Blockspeicher](/docs/containers?topic=containers-block_storage#add_block) und hängen Sie ihn an die App-Bereitstellung an.

Führen Sie die folgenden Schritte aus, um einen vorhandenen physischen Datenträger zu sichern:

1. Rufen Sie den Namen des PVC ab, der den zu sichernden physischen Datenträger bindet.

   ```
   kubectl get pvc
   ```
   {: pre}

2. Erstellen Sie einen Sicherungs-Pod aus dem Image `ibmcloud-backup-restore`. Um auf die Daten im physischen Datenträger zugreifen zu können, müssen Sie den PVC, der den physischen Datenträger bindet, an Ihren Sicherungs-Pod anhängen. Im folgenden Beispiel wird ein Sicherungs-Pod erstellt, der eine tägliche inkrementelle Sicherung ausführt. Wenn Sie eine Sicherung mit anderen Einstellungen erstellen möchten, finden Sie weitere Informationen dazu in der vollständigen Liste von [Umgebungsvariablenoptionen](#backup_restore_env_reference).

   Das Image `ibmcloud-backup-restore` muss in einem einzelnen Pod bereitgestellt werden und kann nicht als Teil einer Kubernetes-Bereitstellung verwendet werden.
   {: important}

   Um das Image anzuzeigen, geben Sie die globale Registry als Ziel an, indem Sie den Befehl `ibmcloud cr region-set global` ausführen. Führen Sie anschließend `ibmcloud cr images --include-ibm` aus, um öffentliche IBM Images aufzuführen.
   {: tip}
  
   ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: backuppod
   spec:
     containers:
     - image: icr.io/ibm/ibmcloud-backup-restore
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
       - mountPath: <Mountpfad1>
         name: <PVC-Name1>
       - mountPath: <Mountpfad1>
         name: <PVC-Name2>
     volumes:
     - name: pvc_name1
       persistentVolumeClaim:
         claimName: <PVC-Name1>
     - name: pvc_name2
       persistentVolumeClaim:
         claimName: <PVC-Name2> 
   ```
   {: codeblock}

   <table>
   <caption>Tabelle 1. Komponenten der YAML-Datei</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Ideensymbol"/> Komponenten der YAML-Datei verstehen</th>
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
     <td><code>&lt;Mountpfad&gt;</code></td>
     <td>Der Datenträgermountpfad im Backupcontainer, der die zu sichernde PVC-Datei bereitstellt. Wenn Sie mehrere PVCs sichern möchten, geben Sie für jeden PVC einen separaten Mountpfad an. Stellen Sie sicher, dass der Name, den Sie in <code>volumeMount.name</code> verwenden, mit dem Namen des Datenträgers übereinstimmt, den Sie im Abschnitt <code>volumes.name</code> Ihrer YAML-Datei angegeben haben. </td>
     </tr>
     <tr>
     <td><code>&lt;PVC-Name&gt;</code></td>
     <td>Der Name des PVC, für den Sie eine Sicherungskopie erstellen möchten. Wenn Sie mehrere PVCs sichern möchten, geben Sie den Datenträgernamen und den entsprechenden PVC für jeden PVC an, die Sie sichern möchten. Stellen Sie sicher, dass der Name, den Sie in <code>volumes.name</code> verwenden, mit dem Namen im Abschnitt <code>volumeMount.name</code> Ihrer YAML-Datei übereinstimmt.</td>
     </tr>
     </tbody>
     </table>

3. Erstellen Sie den Sicherungs-Pod und initiieren Sie eine Sicherung Ihrer PV-Daten.

    ```
    kubectl apply -f backuppod.yaml
    ```
    {: pre}

4. Stellen Sie sicher, dass der Pod ausgeführt wird.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

5. Stellen Sie sicher, dass die Sicherung erfolgreich durchgeführt wurde.

    ```
    kubectl logs backuppod
    ```
    {: pre}

6. Überprüfen Sie die Sicherung in {{site.data.keyword.cos_full_notm}} in der {{site.data.keyword.cloud_notm}}-Benutzerschnittstelle.
    1. Suchen Sie im {{site.data.keyword.cloud_notm}}-Dashboard die {{site.data.keyword.cos_full_notm}}-Serviceinstanz.
    2. Wählen Sie in der Navigation **Buckets** aus und klicken Sie auf das Bucket, das Sie in der Sicherungskonfiguration verwendet haben. Ihre Sicherung wird als Objekt in Ihrem Bucket angezeigt.
    3. Überprüfen Sie die komprimierten Dateien. Sie können die Datei `vol1.difftar.gz` herunterladen, die Datei extrahieren und die Sicherungsdaten überprüfen.

       Wenn Sie Dateien aus {{site.data.keyword.cos_full_notm}} löschen oder ändern, können diese Dateien nicht wiederhergestellt werden.
       {: important}

Ihre Sicherung ist verfügbar. Wenn Sie Ihre Sicherung so konfiguriert haben, dass eine einmalige Gesamtsicherung erstellt wird, müssen Sie das Sicherungsscript jedes Mal ausführen, wenn Sie eine neue Sicherung erstellen wollen. Wenn Sie den Container so konfiguriert haben, dass er in regelmäßigen Abständen eine inkrementelle Sicherung ausführt, wird Ihre Sicherung wie geplant ausgeführt.

## Daten aus {{site.data.keyword.cos_full_notm}} in Ihrem Cluster wiederherstellen
{: #backup_restore_restore_script_cli}

Sie können Daten aus Ihrer {{site.data.keyword.cos_full_notm}}-Serviceinstanz auf einem physischen Datenträger in Ihrem Cluster wiederherstellen.

Bei mehreren Gesamtsicherungen in Ihrer {{site.data.keyword.cos_full_notm}}-Serviceinstanz wird der PVC mit den Daten der letzten Gesamtsicherung wiederhergestellt. Bei inkrementellen Sicherungen wird der PVC mit den Daten der letzten Gesamtsicherung wiederhergestellt; dazu gehören alle inkrementellen Sicherungen bis zu dem Tag, an dem Sie die Wiederherstellung starten.

**Vorbereitende Schritte**

- [Richten Sie die CLI auf Ihren Cluster aus](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure).
- [Erstellen Sie eine Sicherung für einen physischen Datenträger in Ihrem Cluster](#backup_restore_scheduled_backup).

Führen Sie die folgenden Schritte aus, um Daten von {{site.data.keyword.cos_full_notm}} auf einem physischen Datenträger wiederherzustellen:

1. Rufen Sie den Namen des PVC ab, der den physischen Datenträger bindet, auf dem Sie die Daten wiederherstellen möchten.

   ```
   kubectl get pvc
   ```
   {: pre}

2. Erstellen Sie einen Wiederherstellungs-Pod aus dem Image `ibmcloud-backup-restore`. Um Daten auf einem physischen Datenträger wiederherstellen zu können, müssen Sie den PVC, der den physischen Datenträger bindet, an den Wiederherstellungs-Pod anhängen.
   {: codeblock}

    ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: restorepod
   spec:
     containers:
     - image: icr.io/ibm/ibmcloud-backup-restore
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
       - mountPath: <Mountpfad1>
         name: <PVC-Name1>
       - mountPath: <Mountpfad1>
         name: <PVC-Name2>
     volumes:
     - name: pvc_name1
       persistentVolumeClaim:
         claimName: <PVC-Name1>  
     - name: pvc_name2
       persistentVolumeClaim:
         claimName: <PVC-Name2> 
   ```
   {: codeblock}

   <table>
   <caption>Tabelle 2. Komponenten der YAML-Datei</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Ideensymbol"/> Komponenten der YAML-Datei verstehen</th>
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
     <td><code>&lt;Mountpfad&gt;</code></td>
     <td>Der Datenträgermountpfad im Wiederherstellungscontainer, der den PVC an die Position anhängt, an der Sie Ihre Daten wiederherstellen möchten. Wenn Sie mehrere PVCs wiederherstellen möchten, geben Sie für jeden PVC einen separaten Mountpfad an. Stellen Sie sicher, dass der Name, den Sie in <code>volumeMount.name</code> verwenden, mit dem Namen des Datenträgers übereinstimmt, den Sie im Abschnitt <code>volumes.name</code> Ihrer YAML-Datei angegeben haben. </td>
     </tr>
     <tr>
     <td><code>&lt;PVC-Name&gt;</code></td>
     <td>Der Name des PVC, für den Sie Ihre Daten wiederherstellen möchten. Wenn Sie die Daten mehrerer PVCs wiederherstellen möchten, fügen Sie für jeden PVC den Eintrag <code>volumes</code> hinzu. Stellen Sie sicher, dass der Name, den Sie in <code>volumes.name</code> verwenden, mit dem Namen im Abschnitt <code>volumeMount.name</code> Ihrer YAML-Datei übereinstimmt.</td>
     </tr> 
     </tbody>
     </table>

3. Erstellen Sie den Wiederherstellungs-Pod und beginnen Sie mit der Wiederherstellung Ihrer Daten.

    ```
    kubectl apply -f restorepod.yaml
    ```
    {: pre}

4. Stellen Sie sicher, dass der Pod ausgeführt wird.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME              READY     STATUS             RESTARTS   AGE
    restorepod        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    Der Pod führt den Wiederherstellungsbefehl aus und wird gestoppt. Die Nachricht `CrashLoopBackOff` bedeutet, dass Kubernetes versucht, den Pod erneut zu starten.
5. Stellen Sie sicher, dass Ihre Daten erfolgreich wiederhergestellt wurden.

    ```
    kubectl logs restorepod
    ```
    {: pre}

6. Entfernen Sie den Pod, um zu verhindern, dass er weitere Ressourcen nutzt.

    ```
    kubectl delete -f restorepod.yaml
    ```
    {: pre}

Ihre Sicherung wurde erfolgreich wiederhergestellt. Sie können jetzt den PVC, der den physischen Datenträger bindet, an einen anderen Pod in Ihrem Cluster anhängen, um auf die wiederhergestellten Dateien zugreifen zu können. Befinden sich unter den gesicherten Containerdaten Daten, für die die Berechtigungen eines Benutzers ohne Rootberechtigung erforderlich sind, müssen Sie die Berechtigungen von Benutzern ohne Rootberechtigung zu dem neuen Container hinzufügen. Weitere Informationen hierzu finden Sie unter [Zugriff für Benutzer ohne Rootberechtigung zu Datenträgern hinzufügen](/docs/containers?topic=containers-cs_troubleshoot_storage#cs_storage_nonroot).


## Referenzinformationen zu Umgebungsvariablen
{: #backup_restore_env_reference}

Sehen Sie sich die vollständige Liste der Felder an, die als Umgebungsvariablen übergeben oder in der Datei `config.conf` in einem aktiven Pod bearbeitet werden können. Jeder Wert, der als Umgebungsvariable übergeben wird, ersetzt den Wert in der Datei `config.conf`. Wenn Sie die Umgebungsvariablen für einen Pod überprüfen möchten, melden Sie sich mit dem Befehl `kubectl exec` am Pod an und führen Sie `env` aus.

|Schlüssel|Wertoptionen|
|---|-------------|
|ACCESS_KEY_ID|Die **access_key_id** (Zugriffsschlüssel-ID), die Teil der HMAC-Berechtigungsnachweise in {{site.data.keyword.cos_full_notm}} ist.|
|SECRET_ACCESS_KEY|Das Element **secret_access_key** (geheimer Zugrifsschlüssel) ist Teil der HMAC-Berechtigungsnachweise in {{site.data.keyword.cos_full_notm}}.|
|ENDPOINT|Der Hostname für den Zugriff auf die {{site.data.keyword.cos_full_notm}}-Bucketdaten.|
|BUCKET|Der Name des Buckets in {{site.data.keyword.cos_full_notm}}, in dem Ihre Sicherungsdaten gespeichert werden.|
{: caption="Tabelle 4. {{site.data.keyword.cos_full_notm}}-Variablen" caption-side="top"}

|Schlüssel|Wertoptionen|
|---|-------------|
|BACKUP_DIRECTORY|`/backup`. Standardwert. Absoluter Dateipfad des Verzeichnisses, an das der Datenträger angehängt ist. Die Daten werden aus diesem Verzeichnis gesichert. Verwenden Sie nicht das Verzeichnis `backup_restore`, da dieses Verzeichnis Dateien für die Sicherungs- und Wiederherstellungsprozesse enthält.|
|BACKUP_NAME|`volume_backup`. Standardwert. Wählen Sie einen Namen für die Sicherung aus.|
|BACKUP_TYPE|`full`. Standardwert. Es werden jedes Mal alle Dateien gesichert.<br/> `incremental`. Es werden nur neue oder geänderte Dateien gesichert. Wenn Sie `incremental` auswählen, müssen Sie Werte für **SCHEDULING_INFO** und **SCHEDULING_TYPE** auswählen.|
|SCHEDULE_TYPE|`none`. Standardwert. Es wird eine einmalige Sicherung erstellt.<br/> **Hinweis:** Wenn Sie eine einmalige Sicherung erstellen möchten, wird Ihr Pod nach Abschluss der Sicherung aus dem Cluster entfernt. <br/> `periodic`. Ändern Sie den Wert in 'periodic', wenn Sicherungen zu geplanten Zeitpunkten erstellt werden sollen.|
|SCHEDULE_INFO|`hourly`. Es wird jede Stunde eine Sicherung erstellt.<br/>`daily`. Standardwert. Es wird jeden Tag eine Sicherung erstellt.<br/>`weekly`. Es wird jede Woche eine Sicherung erstellt. Sie müssen diese Variable einschließen, wenn Sie eine regelmäßige Aktualisierung planen.|
|EXCLUDE_DIRECTORIES|`none`. Standardwert. Geben Sie den absoluten Dateipfad der Verzeichnisse an, die bei der Sicherung ausgeschlossen werden sollen. Trennen Sie die Verzeichnisse durch ein Komma.|
{: caption="Tabelle 5. Sicherungsvariablen" caption-side="top"}

|Schlüssel|Wertoptionen|
|---|-------------|
|BACKUP_NAME|`volume_backup`. Standardwert. Geben Sie den Namen der Sicherung an, die über {{site.data.keyword.cos_full_notm}} wiederhergestellt werden soll.|
|RESTORE_DIRECTORY|`/backup`. Standardwert. Absoluter Dateipfad des Verzeichnisses, an das der Datenträger angehängt ist. Die Daten werden in dieses Verzeichnis wiederhergestellt. Verwenden Sie nicht das Verzeichnis `backup_restore`, da dieses Verzeichnis Dateien für die Sicherungs- und Wiederherstellungsprozesse enthält.|
{: caption="Tabelle 6. Wiederherstellungsvariablen" caption-side="top"}

