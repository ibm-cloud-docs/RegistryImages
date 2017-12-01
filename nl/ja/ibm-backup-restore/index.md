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

# ibm-backup-restore イメージ概説 
{: #ibmbackup_restore_starter}

**ibm-backup-restore** イメージには、{{site.data.keyword.containerlong}} のコンテナー・ボリュームのバックアップとリストアに必要なプリインストール・パッケージが含まれています。
{:shortdesc}

## 機能 
{: #how_it_works}

**ibm-backup-restore** イメージを使用すると、任意のコンテナー・ボリュームの一回限りのバックアップを作成したり、スケジュールによるバックアップを作成したりできます。バックアップは {{site.data.keyword.objectstoragefull}} インスタンス内に保管されます。{{site.data.keyword.objectstorageshort}} 資格情報は、環境変数として **ibm-backup-restore** コンテナーに渡すことも、実行中のコンテナーの `config.conf` ファイルを編集して渡すこともできます。また、保存したデータを {{site.data.keyword.objectstorageshort}} インスタンスからボリュームにリストアすることもできます。イメージにはバックアップ用とリストア用の両方のスクリプトが含まれているので、ユーザーは実行中のコンテナーで対象のスクリプトを開始するようにコマンドを実行する必要があります。

## 含まれている内容 
{: #whats_included}

各 **ibm-backup-restore** イメージには、以下のソフトウェア・パッケージが含まれています。

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   python、gnupg、および wget の各パッケージ

## 概説 
{: #how_to_get_started}

以下のステップに従って、バックアップおよびリストアの操作を実行します。
1.  [{{site.data.keyword.objectstorageshort}} サービス・インスタンスを作成する](#object_storage)
2.  [スケジュール・バックアップを実行する](#scheduled_backup)
3.  [リストア・スクリプトを実行する](#restore_script_cli)
4.  [バックアップを暗号化する](#encrypting_backups)
5.  [環境変数リファレンス](#reference_backup_restore)

## {{site.data.keyword.objectstorageshort}} サービス・インスタンスを作成する 
{: #object_storage}

ボリューム・バックアップのリポジトリーとなる {{site.data.keyword.objectstorageshort}} インスタンスを作成します。

1.  {{site.data.keyword.Bluemix_notm}} カタログの**「サービス」**セクションから、{{site.data.keyword.objectstorageshort}} インスタンスをプロビジョンします。
2.  {{site.data.keyword.objectstorageshort}} インスタンスを選択します。
3.  **「サービス資格情報」**タブをクリックします。
4.  **「新規資格情報」**をクリックします。
5.  名前フィールドに入力し、その他のフィールドはブランクのままにします。**「追加」**をクリックします。
6.  新しい資格情報が**「サービス資格情報」**の表にリストされます。**「資格情報の表示」**をクリックします。
7.  **projectid**、**region**、**userId**、および **password** をメモします。これらの資格情報を使用して、**ibm-backup-restore** コンテナーはこの {{site.data.keyword.objectstorageshort}} インスタンスにアクセスできます。

このインスタンスの構成について詳しくは、[{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) の資料を参照してください。また、{{site.data.keyword.objectstorageshort}} の価格設定については、[プランの種類](../../ObjectStorage/objectstorage_faq.html#account-payment)を参照してください。

## スケジュール・バックアップを実行する 
{: #scheduled_backup}

**ibm-backup-restore** イメージからコンテナーを作成し、定期的にスケジュールされたバックアップを開始します。

1.  {{site.data.keyword.containerlong_notm}} CLI にログインします。

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

2.  ローカル・ディレクトリーに環境変数ファイルを作成します。

    ```
    touch <backup_env-file_name>
    ```
    {: pre}

3.  環境変数ファイルを編集して、以下のフィールドを追加します。ブランクの環境変数には、先でメモした {{site.data.keyword.objectstorageshort}} 資格情報からの値を入力します。資格情報で引用符が使用されていても含めないでください。


    ```
    BACKUP_NAME=volume_name
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

    この設定では、_volume_backup_ というデフォルト名の日次増分バックアップが作成されます。
別の設定のバックアップを作成するには、すべての[環境変数オプション](#reference_backup_restore)のリストを参照してください。

4.  バックアップ対象のマウントされたボリュームを指定して、**ibm-backup-restore** イメージからコンテナーを実行します。バックアップ・スクリプトを開始するコマンドを含めます。

    -   <em>&lt;backup_env-file&gt;</em> と同じローカル・ディレクトリー内にいることを確認します。
    -   ボリュームのマウント先のディレクトリーは、環境変数ファイル内の BACKUP_DIRECTORY と一致していなければなりません。
    -   ボリューム名を、バックアップしようとしているボリュームの名前に置き換えます。
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<backup_env-file_name> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    コンテナーが実行を開始しない場合は、`bx ic logs <container_name>` を実行し、ログにエラー・メッセージが書き込まれていないか確認します。

5.  {{site.data.keyword.Bluemix_notm}} GUI で {{site.data.keyword.objectstorageshort}} 内のバックアップを確認します。
    1.  バックアップ用に作成した {{site.data.keyword.objectstorageshort}} インスタンスをクリックします。
    2.  {{site.data.keyword.objectstorageshort}} コンテナーをクリックします。この例で、コンテナー名は volume_backup です。
    3.  圧縮ファイルを確認してください。![ {{site.data.keyword.Bluemix_notm}} GUI 内の {{site.data.keyword.objectstorageshort}} コンテナーは、バックアップされるファイルを示しています。](images/volume_backup_screenshot.png) `vol1.difftar.gz` ファイルをダウンロードし、ファイルを解凍し、バックアップ・データを検証することができます。{{site.data.keyword.objectstorageshort}} でファイルを削除または変更したら、それらのファイルはリカバリーできないことに注意してください。

バックアップが有効になりました。一回限りのフルバックアップを作成するように *<container_name>* を構成した場合は、新しいバックアップを作成するたびにバックアップ・スクリプトを実行する必要があります。定期的に増分バックアップを実行するようにコンテナーを構成した場合は、スケジュールに従ってバックアップが実行されます。

## リストア・スクリプトを実行する 
{: #restore_script_cli}

{{site.data.keyword.objectstorageshort}} にあるバックアップを、既存か新規のコンテナー・ボリュームにリストアします。

1.  ローカル・ディレクトリー内にリストア・コンテナーの環境変数ファイルを作成します。

    ```
    touch <restore_env-file_name>
    ```
    {: pre}

2.  環境変数ファイルを編集して、以下のフィールドを追加します。ブランクの環境変数には、先でメモした {{site.data.keyword.objectstorageshort}} 資格情報からの値を入力します。BACKUP_NAME は、{{site.data.keyword.objectstorageshort}} 内のリストアしようとしているバックアップの名前と一致していなければなりません。


    ```
    BACKUP_NAME=<volume_name>
    RESTORE_DIRECTORY=/restore
    USERID=
    REGION=
    PROJECTID=
    PASSWORD=
    ```
    {: codeblock}

3.  バックアップ・ファイルのリストア先のボリュームを作成します。

    ```
    bx ic volume create <volume_name>
    ```
    {: pre}

4.  **ibm-backup-restore** イメージからコンテナーを実行します。リストア・スクリプトを開始するコマンドを含めます。

    -   <em>&lt;restore_env-file&gt;</em> と同じローカル・ディレクトリー内にいることを確認します。
    -   ボリュームのマウント先のディレクトリーは、環境変数ファイル内の BACKUP_DIRECTORY と一致していなければなりません。
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/restore --env-file ./<restore_env-file_name> registry.ng.buemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vrestore"
    ```
    {: pre}

5.  コンテナー・ログを検査して、リストア・プロセスが完了したことを確認します。

    ```
    bx ic logs <container_name>
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

    リストア・プロセスが完了すると、コンテナーはシャットダウンします。

6.  オプション: コンテナーを削除して、リソースが消費されないようにします。

    ```
    bx ic rm -f <container_name>
    ```
    {: pre}

7. オプション: バックアップ･ファイルがボリューム内にあることを確認するために、コンテナーにログインし、マウント・パスにナビゲートして、ファイルをリストします。

    ```
    $ bx ic exec -it <container_name> bash
    root@instance:/backup_restore# cd ..
    root@instance/# cd restore
    root@instance:/restore# ls
    duplicity-I3_pJv-tempdir  new_file
    root@instance:/restore# 
    ```
    {: screen}

    tempdir は、バックアップ処理の生成物であり、手動で削除できます。


ボリューム *volume_name* にバックアップを正常にリストアしました。このボリュームに新規コンテナーをマウントすれば、リストアされたファイルをそのコンテナーで利用することができます。バックアップされていたコンテナー・データに非 root ユーザーが含まれていた場合は、新規コンテナーに非 root の権限を追加する必要があります。詳しくは、[非 root ユーザーのボリューム・アクセス権限の追加](../../../containers/container_volumes_ov.html#container_volumes_write)を参照してください。

## バックアップを暗号化する 
{: #encrypting_backups}

{{site.data.keyword.objectstorageshort}} インスタンス内のデータを暗号化します。

1.  <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="外部リンク・アイコン"></a> をダウンロードして暗号鍵を作成します。
2.  ローカル・ドライブで暗号鍵を作成します。ENTER を押して、デフォルト値を受け入れることができます。

    **注意:** 作成したパスフレーズをメモします。パスフレーズを紛失すると、鍵を使用して暗号化した情報を暗号化解除できなくなります。

    ```
    gpg --gen-key
    ```
    {: pre}

    <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="外部リンク・アイコン"></a> のバージョンにより、コマンドで `gpg` ではなく `gpg2` を使用する必要がある場合があります。

3.  鍵を確認します。

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

4.  `sub` 鍵の値を使用して暗号鍵をエクスポートします。ファイルに encryption.asc という名前を付けます。

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    この例では、sub 鍵の値は *YYYYYYYY* です。

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  ローカル・ディレクトリー内に暗号化バックアップ・コンテナーの環境変数ファイルを作成します。

    ```
    touch <encryption_env-file_name>
    ```
    {: pre}

6.  環境変数ファイルを編集して、以下のフィールドを追加します。ブランクの環境変数には、先でメモした {{site.data.keyword.objectstorageshort}} 資格情報からの値を入力します。資格情報で引用符が使用されていても含めないでください。*ENCRYPTION_PASSPHRASE* には、バックアップをパスワードで保護するためのパスフレーズを含めます。このパスフレーズは、暗号鍵を作成したときに作成したフレーズとは別のパスフレーズです。このパスフレーズは、データのバックアップ時とリストア時に指定する必要があります。

    ```
    BACKUP_NAME=<volume_name>
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

    これらの設定では、*<volume_name>* という名前で暗号化された日次増分バックアップが作成されます。別の設定のバックアップを作成するには、すべての[環境変数オプション](#reference_backup_restore)のリストを参照してください。

7.  {{site.data.keyword.containerlong_notm}} CLI にログインします。

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

8.  バックアップ対象のマウントされたボリュームを指定して、**ibm-backup-restore** イメージからコンテナーを実行します。バックアップ・スクリプトを開始するコマンドを含めます。

    -   <em>&lt;encryption_env-file_name&gt;</em> と同じローカル・ディレクトリー内にいることを確認します。
    -   ボリュームのマウント先のディレクトリーは、環境変数ファイル内の BACKUP_DIRECTORY と一致していなければなりません。
    -   ボリューム名を、バックアップしようとしているボリュームの名前に置き換えます。
   
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<encrypted_env-file_name> registry.ng.bluemix.net/ibm-backup-restore
    ```
    {: pre}

    コンテナーが実行を開始しない場合は、`bx ic logs <container_name>` を実行し、ログにエラー・メッセージが書き込まれていないか確認します。

9.  **ibm-backup-restore** イメージから作成したコンテナーの /backup_restore ディレクトリー内に、暗号鍵をコピーします。

    ```
    bx ic cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    データを暗号化解除するために、暗号鍵のコピーをローカルに保管します。

10. コンテナーにログインします。

    ```
    bx ic exec -it <container_name> bash
    ```
    {: pre}

11. *encryption.asc* が backup_restore フォルダーにコピーされたことを確認します。

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

12. backup_restore フォルダーからバックアップ・スクリプトを実行します。

    ```
    ./vbackup &
    ```
    {: codeblock}

13. バックアップが暗号化されたことを確認するために、{{site.data.keyword.objectstorageshort}} インスタンス内のファイルを確認します。ファイル名の最後に `.gpg` が付加されています。![{{site.data.keyword.objectstorageshort}} GUI は、暗号化されていることを示す .gpg が付加されたすべてのバックアップ済みファイルを表示しています。](images/volume_backup_encrypt_screenshot.png)

バックアップが暗号化されました。ファイルをリストアするには、[リストア・スクリプトを実行する](#restore_script_cli)のステップに従い、リストア処理を実行しているコンテナーの backup_restore ディレクトリーに encryption.asc ファイルを含めます。バックアップが暗号化されている場合は、リストア・コンテナーを作成する時に ENCRYPTION_REQUIRED と ENCRYPTION_PASSPHRASE を環境変数として指定する必要があります。

## 環境変数リファレンス 
{: #reference_backup_restore}

環境変数として渡すことも、実行中のコンテナーの `config.conf` ファイルで編集することもできるすべてのフィールドのリストを確認してください。環境変数として渡された値は、`config.conf` ファイル内の値より優先されます。
コンテナーの環境変数を確認するには、`exec` を使用してコンテナーにログインし、`env` を実行します。

|キー |値のオプション|
|---|-------------|
|PROJECTID|{{site.data.keyword.objectstorageshort}} のプロジェクト ID|
|REGION|{{site.data.keyword.objectstorageshort}} の地域|
|USERID|{{site.data.keyword.objectstorageshort}} のユーザー ID|
|PASSWORD|{{site.data.keyword.objectstorageshort}} のパスワード|
{: caption="表 1. {{site.data.keyword.objectstorageshort}} の変数" caption-side="top"}

{{site.data.keyword.objectstorageshort}} 資格情報から以下の変数を取得します。これらの環境変数は、バックアップまたはリストアの操作を実行するすべてのコンテナーに含めてください。


|キー |値のオプション|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: デフォルト。ボリュームのマウント先のディレクトリーの絶対ファイル・パス。データはこのディレクトリーからバックアップされます。backup_restore ディレクトリーは選択しないでください。このディレクトリーには、バックアップとリストアの処理のためのファイルが入っています。|
|BACKUP_NAME|*volume_backup*: デフォルト。バックアップ名を選択します。|
|BACKUP_TYPE|*full*: デフォルト。毎回すべてのファイルがバックアップされます。<br/> *incremental*: 新しいファイルと変更されたファイルのみバックアップされます。*incremental* を選択した場合、SCHEDULING_INFO と SCHEDULING_TYPE の値を選択する必要があります。|
|SCHEDULE_TYPE|*none*: デフォルト。1 回限りのバックアップを作成します。<br/> *periodic*: スケジュール・バックアップを作成するには、値を periodic に変更します。|
|SCHEDULE_INFO|*hourly*: 毎時バックアップを作成します。<br/>*daily*: デフォルト。日次バックアップを作成します。<br/>*weekly*: 週次バックアップを作成します。定期的更新をスケジューリングする場合は、この変数を含める必要があります。|
|EXCLUDE_DIRECTORIES|*none*: デフォルト。バックアップから除外するディレクトリーの絶対ファイル・パスを含めます。ディレクトリーが複数ある場合はコンマで区切ります。|
{: caption="表 2. バックアップの変数" caption-side="top"}

|キー |値のオプション|
|---|-------------|
|BACKUP_NAME|*volume_backup*: デフォルト。{{site.data.keyword.objectstorageshort}} からリストアするバックアップの名前を含めます。|
|RESTORE_DIRECTORY|*/backup*: デフォルト。ボリュームのマウント先の絶対ディレクトリー。データはこのディレクトリーにリストアされます。backup_restore ディレクトリーは選択しないでください。このディレクトリーには、バックアップとリストアの処理のためのファイルが入っています。|
{: caption="表 3. リストアの変数" caption-side="top"}

|キー |値のオプション|
|---|-------------|
|ENCRYPTION_KEY_FILE|*/encryption.asc*: デフォルト。暗号鍵のファイル名を変更する場合、または backup_restore 以外のディレクトリーに鍵が配置されている場合に、この環境変数を含めます。|
|ENCRYPTION_REQUIRED|*no*: デフォルト。<br/> *yes*: バックアップを暗号化しない場合は、暗号化の環境変数を含めないでください。バックアップを暗号化する場合は、値 yes を指定してこのキーを含めてください。|
|ENCRYPTION_PASSPHRASE|バックアップを保護するためのパスフレーズを含めます。このパスフレーズは、暗号鍵を作成したときに作成したフレーズとは別のパスフレーズです。このパスフレーズは、データのバックアップ時とリストア時に指定する必要があります。|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: デフォルト。<br/> *yes*: 暗号鍵をコンテナーに直接生成した場合は、yes を指定してこの環境変数を含めます。ほとんどのユーザーはローカル・コンピューターで鍵を生成してコンテナーにコピーするので、デフォルトの no のままでかまいません。|
{: caption="表 4. 暗号化の変数" caption-side="top"}

