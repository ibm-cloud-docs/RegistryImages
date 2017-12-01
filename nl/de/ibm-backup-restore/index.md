---

copyright:
  years: 2017
lastupdated: "2017-10-30"

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

Das Image **ibm-backup-restore** enthält die vorinstallierten Pakete, die zum Sichern und Wiederherstellen von Containerdatenträgern in {{site.data.keyword.containerlong}} erforderlich sind.
{:shortdesc}

## Funktionsweise 
{: #how_it_works}

Mit dem Image **ibm-backup-restore** können Sie eine einmalige oder geplante Sicherung für einen beliebigen Containerdatenträger erstellen. Die Sicherung wird in einer Instanz von {{site.data.keyword.objectstoragefull}} gespeichert. Sie können die {{site.data.keyword.objectstorageshort}}-Berechtigungsnachweise als Umgebungsvariablen an Ihren **ibm-backup-restore**-Container übergeben oder indem Sie die Datei `config.conf` in dem aktiven Container bearbeiten. Sie können die gespeicherten Daten auch von der {{site.data.keyword.objectstorageshort}}-Instanz auf einen Datenträger wiederherstellen. Da das Image Scripts für die Ausführung von Sicherungen und Wiederherstellungen enthält, muss der Benutzer einen Befehl ausgeben, um das entsprechende Script in einem aktiven Container zu starten.

## Enthaltene Elemente 
{: #whats_included}

Jedes Image **ibm-backup-restore** enthält die folgenden Softwarepakete:

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   python-, gnupg- und wget-Pakete

## Einführung 
{: #how_to_get_started}

Führen Sie die folgenden Schritte aus, um Sicherungs- und Wiederherstellungsoperationen auszuführen:
1.  [{{site.data.keyword.objectstorageshort}}-Serviceinstanz erstellen](#object_storage)
2.  [Geplante Sicherung ausführen](#scheduled_backup)
3.  [Wiederherstellungsscript ausführen](#restore_script_cli)
4.  [Sicherungen verschlüsseln](#encrypting_backups)
5.  [Umgebungsvariablen - Referenz](#reference_backup_restore)

## {{site.data.keyword.objectstorageshort}}-Serviceinstanz erstellen 
{: #object_storage}

Erstellen Sie eine {{site.data.keyword.objectstorageshort}}-Instanz, die als Repository für Ihre Datenträgersicherung verwendet werden soll.

1.  Richten Sie Ihre {{site.data.keyword.objectstorageshort}}-Instanz über den Abschnitt **Services** des {{site.data.keyword.Bluemix_notm}}-Katalogs ein.
2.  Wählen Sie die {{site.data.keyword.objectstorageshort}}-Instanz aus.
3.  Klicken Sie auf die Registerkarte **Serviceberechtigungsnachweise**.
4.  Klicken Sie auf **Neuer Berechtigungsnachweis**.
5.  Füllen Sie das Namensfeld aus, aber lassen Sie die anderen Felder leer. Klicken Sie auf **Hinzufügen**.
6.  Ihre neuen Berechtigungsnachweise werden jetzt in der Tabelle **Serviceberechtigungsnachweise** aufgelistet. Klicken Sie auf **Berechtigungsnachweise anzeigen**.
7.  Notieren Sie sich die Projekt-ID (**projectid**), die Region (**region**), die Benutzer-ID (**userId**) und das Kennwort (**password**). Diese Berechtigungsnachweise ermöglichen Ihrem **ibm-backup-restore**-Container den Zugriff auf diese {{site.data.keyword.objectstorageshort}}-Instanz.

Weitere Informationen zur Konfiguration Ihrer Instanz können Sie der [{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html)-Dokumentation entnehmen. Weitere Informationen zur {{site.data.keyword.objectstorageshort}}-Preisgestaltung finden Sie unter [Planoptionen](../../ObjectStorage/objectstorage_faq.html#account-payment).

## Geplante Sicherung ausführen 
{: #scheduled_backup}

Erstellen Sie einen Container aus dem Image **ibm-backup-restore** und starten Sie eine regelmäßige Sicherung.

1.  Melden Sie sich an der {{site.data.keyword.containerlong_notm}}-Befehlszeilenschnittstelle an.

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

2.  Erstellen Sie eine Datei mit Umgebungsvariablen in einem lokalen Verzeichnis.

    ```
    touch <umgebungsvariablendateiname_für_sicherung>
    ```
    {: pre}

3.  Bearbeiten Sie die Umgebungsvariablendatei und fügen Sie die folgenden Felder hinzu. Geben Sie für die leeren Umgebungsvariablen die Werte aus den {{site.data.keyword.objectstorageshort}}-Berechtigungsnachweisen ein, die Sie zuvor notiert haben. Geben Sie nicht die Anführungszeichen an, die in den Berechtigungsnachweisen verwendet werden.

    ```
    BACKUP_NAME=datenträgername
    BACKUP_DIRECTORY=/backup
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    SCHEDULE_TYPE=periodic
    SCHEDULE_INFO=daily
    BACKUP_TYPE=incremental
    ```
    {: codeblock}

    Mit diesen Einstellungen wird eine tägliche inkrementelle Sicherung mit dem Standardnamen _volume_backup_ erstellt. Wenn Sie eine Sicherung mit anderen Einstellungen erstellen möchten, finden Sie weitere Informationen dazu in der vollständigen Liste von [Umgebungsvariablenoptionen](#reference_backup_restore).

4.  Führen Sie einen Container aus dem Image **ibm-backup-restore** mit einem angehängten Datenträger aus, um eine Sicherungskopie zu erstellen. Geben Sie den Befehl zum Starten des Sicherungsscripts an.

    -   Stellen Sie sicher, dass Sie sich in demselben lokalen Verzeichnis befinden wie die Datei <em>&lt;umgebungsvariablendatei_für_sicherung&gt;</em>.
    -   Das Verzeichnis, an das der Datenträger angehängt ist, muss mit dem Wert von BACKUP_DIRECTORY in der Umgebungsvariablendatei übereinstimmen.
    -   Ersetzen Sie den Datenträgernamen durch den Namen des Datenträgers, der gesichert wird.
    
    ```
    bx ic run --name <containername> --volume <datenträgername>:/backup --env-file ./<umgebungsvariablendateiname_für_sicherung> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    Wenn der Container nicht gestartet wird, führen Sie `bx ic logs <containername>` aus, um die Protokolle auf Fehlernachrichten zu prüfen.

5.  Überprüfen Sie die Sicherung in {{site.data.keyword.objectstorageshort}} in der {{site.data.keyword.Bluemix_notm}}-Benutzerschnittstelle.
    1.  Klicken Sie auf die {{site.data.keyword.objectstorageshort}}-Instanz, die Sie für die Sicherung erstellt haben.
    2.  Klicken Sie auf den {{site.data.keyword.objectstorageshort}}-Container. In diesem Beispiel ist der Containername 'volume_backup'.
    3.  Überprüfen Sie die komprimierten Dateien.![Der {{site.data.keyword.objectstorageshort}}-Container in der {{site.data.keyword.Bluemix_notm}}-Benutzerschnittstelle zeigt die Dateien, die gesichert werden.](images/volume_backup_screenshot.png) Sie können die Datei `vol1.difftar.gz` herunterladen, die Datei extrahieren und die Sicherungsdaten überprüfen. Wenn Sie Dateien aus {{site.data.keyword.objectstorageshort}} löschen oder ändern, können diese Dateien nicht wiederhergestellt werden.

Ihre Sicherung ist verfügbar. Wenn Sie *<containername>* so konfiguriert haben, dass eine einmalige Gesamtsicherung erstellt wird, müssen Sie das Sicherungsscript jedes Mal ausführen, wenn Sie eine neue Sicherung erstellen wollen. Wenn Sie den Container so konfiguriert haben, dass er in regelmäßigen Abständen eine inkrementelle Sicherung ausführt, wird Ihre Sicherung wie geplant ausgeführt.

## Wiederherstellungsscript ausführen 
{: #restore_script_cli}

Stellen Sie Ihre Sicherung von {{site.data.keyword.objectstorageshort}} auf einem vorhandenen oder neuen Containerdatenträger wieder her.

1.  Erstellen Sie eine Umgebungsvariablendatei für Ihren Wiederherstellungscontainer in einem lokalen Verzeichnis.

    ```
    touch <umgebungsvariablendateiname_für_wiederherstellung>
    ```
    {: pre}

2.  Bearbeiten Sie die Umgebungsvariablendatei und fügen Sie die folgenden Felder hinzu. Geben Sie für die leeren Umgebungsvariablen die Werte aus den {{site.data.keyword.objectstorageshort}}-Berechtigungsnachweisen ein, die Sie zuvor notiert haben. BACKUP_NAME muss mit dem Namen der Sicherung in {{site.data.keyword.objectstorageshort}} übereinstimmen, die wiederhergestellt wird.

    ```
    BACKUP_NAME=<datenträgername>
    RESTORE_DIRECTORY=/restore
    USERID=
    REGION=
    PROJECTID=
    PASSWORD=
    ```
    {: codeblock}

3.  Erstellen Sie einen Datenträger, in dem die Sicherungsdateien wiederhergestellt werden sollen.

    ```
    bx ic volume create <datenträgername>
    ```
    {: pre}

4.  Führen Sie einen Container aus dem Image **ibm-backup-restore** aus. Geben Sie den Befehl zum Starten des Wiederherstellungsscripts an.

    -   Stellen Sie sicher, dass Sie sich in demselben lokalen Verzeichnis befinden wie die <em>&lt;umgebungsvariablendateiname_für_wiederherstellung&gt;</em>.
    -   Das Verzeichnis, an das der Datenträger angehängt ist, muss mit dem Wert von BACKUP_DIRECTORY in der Umgebungsvariablendatei übereinstimmen.
    
    ```
    bx ic run --name <containername> --volume <datenträgername>:/restore --env-file ./<umgebungsvariablendateiname_für_wiederherstellung> registry.ng.buemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vrestore"
    ```
    {: pre}

5.  Überprüfen Sie die Containerprotokolle, um sicherzustellen, dass der Wiederherstellungsprozess abgeschlossen ist.

    ```
    bx ic logs <containername>
    ```
    {: pre}

    ```
    [2016-10-26 18:01:51,031] [utilities : 152] [INFO] *****************Start logging to ./Restore.log with rollover at 102400 bytes**************
    [2016-10-26 18:01:51,031] [restore : 28] [INFO] Starting the restore process.
    [2016-10-26 18:01:51,032] [configureOS : 22] [INFO] Configuring duplicity with IBM Bluemix ObjectStorage.
    [2016-10-26 18:01:51,032] [configureOS : 13] [INFO] Configuring swift client.
    [2016-10-26 18:01:51,032] [restore : 40] [INFO] Configuration is completed.
    [2016-10-26 18:01:54,022] [restore : 70] [INFO] Restoring the backup that is named 'volume_backup' is completed. Synchronizing remote metadata to local cache...
    Copying duplicity-full-signatures.20161026T173856Z.sigtar.gz to local cache.
    Copying duplicity-full.20161026T173856Z.manifest to local cache.
    Last full backup date: Wed Oct 26 17:38:56 2016
    ```
    {: screen}

    Wenn der Wiederherstellungsprozess abgeschlossen ist, wird der Container beendet.

6.  Optional: Sie können den Container entfernen, damit er keine Ressourcen belegt.

    ```
    bx ic rm -f <containername>
    ```
    {: pre}

7. Optional: Um zu überprüfen, ob sich Ihre Sicherungsdateien auf dem Datenträger befinden, melden Sie sich am Container an, navigieren Sie zum Mountpfad und listen Sie die Dateien auf.

    ```
    $ bx ic exec -it <containername> bash
    root@instance:/backup_restore# cd ..
    root@instance/# cd restore
    root@instance:/restore# ls
    duplicity-I3_pJv-tempdir  new_file
    root@instance:/restore# 
    ```
    {: screen}

    Das Verzeichnis 'tempdir' ist infolge des Sicherungsprozesses entstanden und kann manuell gelöscht werden.


Ihre Sicherung wurde erfolgreich auf dem Datenträger *datenträgername* wiederhergestellt. Sie können einen beliebigen neuen Container an den Datenträger anhängen, um dem betreffenden Container den Zugriff auf die wiederhergestellten Dateien zu ermöglichen. Befinden sich unter den gesicherten Containerdaten Daten, für die die Berechtigungen eines Benutzers ohne Rootberechtigung erforderlich sind, müssen Sie die Berechtigungen von Benutzern ohne Rootberechtigung zu dem neuen Container hinzufügen. Weitere Informationen hierzu finden Sie unter [Zugriff für Benutzer ohne Rootberechtigung zu Datenträgern hinzufügen](../../../containers/container_volumes_ov.html#container_volumes_write).

## Sicherungen verschlüsseln 
{: #encrypting_backups}

Verschlüsseln Sie die Daten in Ihrer {{site.data.keyword.objectstorageshort}}-Instanz.

1.  Laden Sie <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Symbol für externen Link"></a> herunter, um einen Verschlüsselungsschlüssel zu erstellen.
2.  Erstellen Sie einen Verschlüsselungsschlüssel auf Ihrem lokalen Laufwerk. Sie können die Standardwerte übernehmen, indem Sie die Eingabetaste drücken.

    **Achtung:** Notieren Sie sich die von Ihnen erstellte Kennphrase. Wenn Sie nicht mehr wissen, wie diese Kennphrase lautet, können die mit Ihrem Schlüssel verschlüsselten Informationen nicht mehr entschlüsselt werden.

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
    uid       [ultimate] Example Name (This is an example key) <example_email_address>
    sub   2048R/YYYYYYYY 2016-10-27
    ```
    {: screen}

4.  Exportieren Sie den Verschlüsselungsschlüssel mit dem Wert aus dem Schlüssel `sub`. Geben Sie der Datei den Namen encryption.asc.

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    In diesem Beispiel hat der Unterschlüssel den Wert *YYYYYYYY*.

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  Erstellen Sie eine Umgebungsvariable für Ihren verschlüsselten Sicherungscontainer in einem lokalen Verzeichnis.

    ```
    touch <umgebungsvariablendateiname_für_sicherung>
    ```
    {: pre}

6.  Bearbeiten Sie die Umgebungsvariablendatei und fügen Sie die folgenden Felder hinzu. Geben Sie für die leeren Umgebungsvariablen die Werte aus den {{site.data.keyword.objectstorageshort}}-Berechtigungsnachweisen ein, die Sie zuvor notiert haben. Geben Sie nicht die Anführungszeichen an, die in den Berechtigungsnachweisen verwendet werden. Geben Sie für *ENCRYPTION_PASSPHRASE* eine Kennphrase ein, um die Sicherung zu schützen. Bei dieser Kennphrase handelt es sich um eine andere Kennphrase als die, die Sie beim Erstellen des Verschlüsselungsschlüssel erstellt haben. Sie müssen diese Kennphrase einfügen, wenn Sie Daten sichern und Daten wiederherstellen.

    ```
    BACKUP_NAME=<datenträgername>
    BACKUP_DIRECTORY=/backup
    # PROJECTID, REGION, USERID, PASSWORD come from the Object Storage credentials.
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    SCHEDULE_TYPE=periodic
    SCHEDULE_INFO=daily
    BACKUP_TYPE=incremental
    ENCRYPTION_REQUIRED=yes
    # Include this passphrase when backing up and restoring encrypted data.
    ENCRYPTION_PASSPHRASE=
    ```
    {: codeblock}

    Mit diesen Einstellungen wird eine tägliche inkrementelle Sicherung erstellt, die mit dem Namen *<datenträgername>* verschlüsselt wird. Wenn Sie eine Sicherung mit anderen Einstellungen erstellen möchten, finden Sie weitere Informationen dazu in der vollständigen Liste von [Umgebungsvariablenoptionen](#reference_backup_restore).

7.  Melden Sie sich an der {{site.data.keyword.containerlong_notm}}-Befehlszeilenschnittstelle an.

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

8.  Führen Sie einen Container aus dem Image **ibm-backup-restore** mit einem angehängten Datenträger aus, um eine Sicherungskopie zu erstellen. Geben Sie den Befehl zum Starten des Sicherungsscripts an.

    -   Stellen Sie sicher, dass Sie sich in demselben lokalen Verzeichnis befinden wie die Datei <em>&lt;umgebungsvariablendateiname_für_verschlüsselung&gt;</em>.
    -   Das Verzeichnis, an das der Datenträger angehängt ist, muss mit dem Wert von BACKUP_DIRECTORY in der Umgebungsvariablendatei übereinstimmen.
    -   Ersetzen Sie den Datenträgernamen durch den Namen des Datenträgers, der gesichert wird.
   
    ```
    bx ic run --name <containername> --volume <datenträgername>:/backup --env-file ./<name_der_verschlüsselten_umgebungsvariablendatei> registry.ng.bluemix.net/ibm-backup-restore
    ```
    {: pre}

    Wenn der Container nicht gestartet wird, führen Sie `bx ic logs <containername>` aus, um die Protokolle auf Fehlernachrichten zu prüfen.

9.  Kopieren Sie den Verschlüsselungsschlüssel in das Verzeichnis /backup_restore des Containers, der aus dem Image **ibm-backup-restore** erstellt wird.

    ```
    bx ic cp ./encryption.asc <containername>:/backup_restore
    ```
    {: pre}

    Bewahren Sie eine Kopie des Verschlüsselungsschlüssels lokal auf, um Ihre Daten zu entschlüsseln.

10. Melden Sie sich am Container an.

    ```
    bx ic exec -it <containername> bash
    ```
    {: pre}

11. Bestätigen Sie, dass die Datei *encryption.asc* in den Ordner 'backup_restore' kopiert wurde.

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

13. Überprüfen Sie die Dateien in Ihrer {{site.data.keyword.objectstorageshort}}-Instanz, um sicherzustellen, dass die Sicherung verschlüsselt ist. Dem Namen der Dateien ist jetzt die Endung `.gpg` angehängt.![Die {{site.data.keyword.objectstorageshort}}-Benutzerschnittstelle zeigt alle gesicherten Dateien mit der Erweiterung .gpg. Dadurch werden sie als verschlüsselte Dateien gekennzeichnet.](images/volume_backup_encrypt_screenshot.png)

Ihre Sicherung ist verschlüsselt. Führen Sie zum Wiederherstellen der Dateien die unter [Wiederherstellungsscript ausführen](#restore_script_cli) beschriebenen Schritte aus und nehmen Sie die Datei 'encryption.asc' in das Verzeichnis 'backup_restore' des Containers auf, der für die Ausführung des Wiederherstellungsprozesses verwendet wird. Wenn die Sicherung verschlüsselt ist, müssen Sie ENCRYPTION_REQUIRED und ENCRYPTION_PASSPHRASE beim Erstellen des Wiederherstellungscontainers als Umgebungsvariablen angeben.

## Umgebungsvariablen - Referenz 
{: #reference_backup_restore}

Überprüfen Sie die vollständige Liste der Felder, die als Umgebungsvariablen übergeben oder in der Datei `config.conf` in einem aktiven Container bearbeitet werden können. Die als Umgebungsvariablen übergebenen Werte setzen den entsprechenden Wert in der Datei `config.conf` außer Kraft. Wenn Sie die Umgebungsvariablen für einen Container überprüfen möchten, melden Sie sich mit `exec` am Container an und führen Sie `env` aus.

|Schlüssel|Wertoptionen|
|---|-------------|
|PROJECTID|Projekt-ID aus {{site.data.keyword.objectstorageshort}}|
|REGION|Region aus {{site.data.keyword.objectstorageshort}}|
|USERID|Benutzer-ID aus {{site.data.keyword.objectstorageshort}}|
|PASSWORD|Kennwort aus {{site.data.keyword.objectstorageshort}}|
{: caption="Tabelle 1. {{site.data.keyword.objectstorageshort}}-Variablen" caption-side="top"}

Rufen Sie diese Variablen aus den {{site.data.keyword.objectstorageshort}}-Berechtigungsnachweisen ab. Nehmen Sie diese Umgebungsvariablen bei jedem Container auf, über den eine Sicherungs- oder Wiederherstellungsoperation ausgeführt wird.

|Schlüssel|Wertoptionen|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: Standardwert. Absoluter Dateipfad des Verzeichnisses, an das der Datenträger angehängt ist. Die Daten werden aus diesem Verzeichnis gesichert. Verwenden Sie nicht das Verzeichnis backup_restore, da dieses Verzeichnis Dateien für die Sicherungs- und Wiederherstellungsprozesse enthält.|
|BACKUP_NAME|*volume_backup*: Standardwert. Wählen Sie einen Namen für die Sicherung aus.|
|BACKUP_TYPE|*full*: Standardwert. Es werden jedes Mal alle Dateien gesichert.<br/> *incremental*: Es werden nur neue oder geänderte Dateien gesichert. Bei Auswahl von *incremental* müssen Sie Werte für SCHEDULING_INFO und SCHEDULING_TYPE festlegen.|
|SCHEDULE_TYPE|*none*: Standardwert. Es wird eine einmalige Sicherung erstellt.<br/> *periodic*: Ändern Sie den Wert in 'periodic', wenn Sicherungen zu geplanten Zeitpunkten erstellt werden sollen.|
|SCHEDULE_INFO|*hourly*: Es wird jede Stunde eine Sicherung erstellt. <br/>*daily*: Standardwert. Es wird jeden Tag eine Sicherung erstellt.<br/>*weekly*: Es wird jede Woche eine Sicherung erstellt. Sie müssen diese Variable angeben, wenn Sie eine periodische Aktualisierung planen.|
|EXCLUDE_DIRECTORIES|*none*: Standardwert. Geben Sie den absoluten Dateipfad der Verzeichnisse an, die bei der Sicherung ausgeschlossen werden sollen. Trennen Sie die Verzeichnisse durch ein Komma.|
{: caption="Tabelle 2. Sicherungsvariablen" caption-side="top"}

|Schlüssel|Wertoptionen|
|---|-------------|
|BACKUP_NAME|*volume_backup*: Standardwert. Geben Sie den Namen der Sicherung an, die über {{site.data.keyword.objectstorageshort}} wiederhergestellt werden soll.|
|RESTORE_DIRECTORY|*/backup*: Standardwert. Absoluter Dateipfad des Verzeichnisses, an das der Datenträger angehängt ist. Die Daten werden in dieses Verzeichnis wiederhergestellt. Verwenden Sie nicht das Verzeichnis backup_restore, da dieses Verzeichnis Dateien für die Sicherungs- und Wiederherstellungsprozesse enthält.|
{: caption="Tabelle 3. Wiederherstellungsvariablen" caption-side="top"}

|Schlüssel|Wertoptionen|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: Standardwert. Fügen Sie diese Umgebungsvariable ein, wenn Sie den Dateinamen des Verschlüsselungsschlüssels ändern oder wenn sich der Schlüssel in einem anderem Verzeichnis als dem Verzeichnis 'backup_restore' befindet.|
|ENCRYPTION_REQUIRED|*no*: Standardwert. <br/> *yes*: Fügen Sie keine Umgebungsvariablen für die Verschlüsselung ein, wenn Ihre Sicherung nicht verschlüsselt werden soll. Nehmen Sie diesen Schlüssel mit dem Wert 'yes' auf, wenn Ihre Sicherung verschlüsselt werden soll.|
|ENCRYPTION_PASSPHRASE|Fügen Sie eine Kennphrase ein, um die Sicherung zu schützen. Bei dieser Kennphrase handelt es sich um eine andere Kennphrase als die, die Sie beim Erstellen des Verschlüsselungsschlüssel erstellt haben. Sie müssen diese Kennphrase einfügen, wenn Sie Daten sichern und Daten wiederherstellen.|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: Standardwert. <br/> *yes*: Fügen Sie diese Umgebungsvariable mit dem Wert 'yes' ein, wenn Sie den Verschlüsselungsschlüssel direkt im Container generiert haben. Die Mehrzahl der Benutzer generiert den Schlüssel auf dem lokalen Computer und kopiert ihn in den Container. In diesem Fall kann der Standardwert 'no' beibehalten werden.|
{: caption="Tabelle 4. Verschlüsselungsvariablen" caption-side="top"}

