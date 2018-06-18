---

copyright:
  years: 2017
lastupdated: "2017-11-09"

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

**ibm-backup-restore** イメージには、{{site.data.keyword.containerlong}} での永続ストレージのバックアップとリストアに必要なプリインストール・パッケージが入っています。
{:shortdesc}

## 機能 
{: #how_it_works}

**ibm-backup-restore** イメージを使用して、永続ボリューム・クレーム (pvc) の一回限りのバックアップを作成したり、スケジュール・バックアップを実行したりできます。 バックアップは {{site.data.keyword.objectstoragefull}} インスタンス内に保管されます。 {{site.data.keyword.objectstorageshort}} 資格情報は、環境変数として **ibm-backup-restore** コンテナーに渡すことも、実行中のコンテナーの `config.conf` ファイルを編集して渡すこともできます。 また、保存したデータを {{site.data.keyword.objectstorageshort}} インスタンスからボリュームにリストアすることもできます。 イメージにはバックアップ用とリストア用の両方のスクリプトが含まれているため、ユーザーはコマンドを入力して該当するスクリプトを開始する必要があります。

## 含まれている内容 
{: #whats_included}

各 **ibm-backup-restore** イメージには、以下のソフトウェア・パッケージが含まれています。

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   python、gnupg、および wget の各パッケージ

## 概説 
{: #how_to_get_started}

データのバックアップおよびリストアを行うには、以下のタスクを確認します。
1.  [{{site.data.keyword.objectstorageshort}} サービス・インスタンスを作成する](#object_storage)
2.  [スケジュール・バックアップを実行する](#scheduled_backup)
3.  [リストア・スクリプトを実行する](#restore_script_cli)
4.  [バックアップを暗号化する](#encrypting_backups)
5.  [環境変数リファレンス](#reference_backup_restore)

## {{site.data.keyword.objectstorageshort}} サービス・インスタンスを作成する 
{: #object_storage}

ボリューム・バックアップのリポジトリーとなる {{site.data.keyword.objectstorageshort}} インスタンスを作成します。

1.  {{site.data.keyword.Bluemix_notm}} カタログの**「ストレージ」**セクションから、{{site.data.keyword.objectstorageshort}} インスタンスをプロビジョンします。
2.  {{site.data.keyword.objectstorageshort}} をクリックします。
3.  {{site.data.keyword.objectstorageshort}} OpenStack Swift for {{site.data.keyword.Bluemix_notm}} を選択します。 次に「作成」をクリックします。
3.  **「サービス資格情報」**タブをクリックします。
4.  **「新規資格情報」**をクリックします。
5.  名前フィールドに入力し、その他のフィールドはブランクのままにします。 **「追加」**をクリックします。
6.  新しい資格情報が**「サービス資格情報」**の表にリストされます。 **「資格情報の表示」**をクリックします。
7.  **projectid**、**region**、**userId**、および **password** をメモします。 これらの資格情報を使用して、**ibm-backup-restore** コンテナーはこの {{site.data.keyword.objectstorageshort}} インスタンスにアクセスできます。

このインスタンスの構成について詳しくは、[{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) の資料を参照してください。また、{{site.data.keyword.objectstorageshort}} の価格設定については、[プランの種類](../../ObjectStorage/objectstorage_faq.html#account-payment)を参照してください。

## スケジュール・バックアップを実行する 
{: #scheduled_backup}

**ibm-backup-restore** イメージからコンテナー・ポッドを作成し、定期的にスケジュールされたバックアップを開始します。

始める前に、以下を実行してください。

-   必要な [CLI](../../../containers/cs_cli_install.html#cs_cli_install) をインストールします。
-   [CLI のターゲット](../../../containers/cs_cli_install.html#cs_cli_configure)を自分のクラスターに設定します。


1. _backup-pvc.yaml_ という名前の構成ファイルを作成します。 この構成ファイルは、バックアップ・ポッドにボリュームとしてマウントできる、永続ボリューム・クレーム (pvc) を作成します。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: backuppvc
      annotations:
        volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
    ```
    {: codeblock}
    
2. pvc を作成します。

    ```
    kubectl apply -f backup-pvc.yaml 
    ```
    {: pre}

3.  _backup.yaml_ という名前の構成ファイルを作成します。 ブランクの環境変数には、前にメモした {{site.data.keyword.objectstorageshort}} 資格情報からの値を入力します。 資格情報に使用されている引用符を含めてください。 <em>&lt;pod_name&gt;</em>、<em>&lt;container_name&gt;</em>、およびイメージ・レジストリー <em>&lt;region&gt;</em> を適切な値に置き換えます。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: <pod_name>
    spec:
      containers:
        - name: <container_name>
          image: registry.<region>.bluemix.net/ibm-backup-restore
          env:
          - name: USERID
            value: 
          - name: PASSWORD
            value: 
          - name: PROJECTID
            value:
          - name: REGION
            value:
          - name: SCHEDULE_TYPE
            value: periodic
          - name: SCHEDULE_INFO
            value: daily
          - name: BACKUP_TYPE
            value: incremental
          - name: BACKUP_DIRECTORY
            value: /backup
          - name: BACKUP_NAME
            value: mybackup
          command: ["/bin/bash", "./vbackup"]
          volumeMounts:
          - mountPath: /backup
            name: backup-volume
      volumes:
      - name: backup-volume 
        persistentVolumeClaim:
          claimName: backuppvc
    ```
    {: codeblock}
    
    この設定では、デフォルト名 _mybackup_ で日次増分バックアップが作成されます。 別の設定のバックアップを作成するには、すべての[環境変数オプション](#reference_backup_restore)のリストを参照してください。

4.  バックアップ・スクリプトを実行するポッドを作成します。

    ```
    kubectl apply -f backup.yaml
    ```
    {: pre}

5.  ポッドが実行中であることを確認します。

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

4.  {{site.data.keyword.Bluemix_notm}} GUI で {{site.data.keyword.objectstorageshort}} 内のバックアップを確認します。
    1.  バックアップ用に作成した {{site.data.keyword.objectstorageshort}} インスタンスをクリックします。
    2.  **「コンテナー」**テーブルの**「管理」**タブで、{{site.data.keyword.objectstorageshort}} コンテナーをクリックします。
    3.  圧縮ファイルを確認します。![ {{site.data.keyword.Bluemix_notm}} GUI の Object Storage コンテナーに、バックアップされたファイルが表示されます。](images/volume_backup_screenshot.png)vol1.difftar.gz ファイルをダウンロードし、ファイルを解凍して、バックアップ・データを検証できます。 **重要**: {{site.data.keyword.objectstorageshort}} のファイルを削除または変更すると、それらのファイルはリカバリーできません。

バックアップが有効になりました。 一回限りのフルバックアップを作成するようにバックアップを構成した場合、新規バックアップの作成が必要になるたびに、バックアップ・スクリプトを実行する必要があります。 定期的に増分バックアップを実行するようにコンテナーを構成した場合は、スケジュールに従ってバックアップが実行されます。

## リストア・スクリプトを実行する 
{: #restore_script_cli}

{{site.data.keyword.objectstorageshort}} からのバックアップを既存のボリュームまたは新規ボリュームにリストアします。

始める前に、以下を実行してください。

-   必要な [CLI](../../../containers/cs_cli_install.html#cs_cli_install) をインストールします。
-   [CLI のターゲット](../../../containers/cs_cli_install.html#cs_cli_configure)を自分のクラスターに設定します。


1. _restore-pvc.yaml_ という名前の構成ファイルを作成します。 この構成ファイルは、リストア・ポッドにボリュームとしてマウントできる、永続ボリューム・クレーム (pvc) を作成します。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: restorepvc
      annotations:
        volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
    ```
    {: codeblock}
    
2. pvc を作成します。

    ```
    kubectl apply -f restore-pvc.yaml 
    ```
    {: pre}

3.  _restore.yaml_ という名前の構成ファイルを作成します。 ブランクの環境変数には、前にメモした {{site.data.keyword.objectstorageshort}} 資格情報からの値を入力します。 資格情報に使用されている引用符を含めてください。 BACKUP_NAME は、{{site.data.keyword.objectstorageshort}} 内のリストアしようとしているバックアップの名前と一致していなければなりません。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: <pod_name>
    spec:
      containers:
        - name: <container_name>
          image: registry.<region>.bluemix.net/ibm-backup-restore
          env:
          - name: USERID
            value: 
          - name: PASSWORD
            value: 
          - name: PROJECTID
            value:
          - name: REGION
            value:
          - name: RESTORE_DIRECTORY
            value: /restore
          - name: BACKUP_NAME
            value: mybackup
          command: ["/bin/bash", "./vrestore"]
          volumeMounts:
          - mountPath: /restore
            name: restore-volume
      volumes:
      - name: restore-volume 
        persistentVolumeClaim:
          claimName: restorepvc
    ```
    {: codeblock}

4.  リストア・スクリプトを実行するポッドを作成します。
    
    ```
    kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  ポッドが実行中であることを確認します。

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    ポッドは、リストア・コマンドを実行して、停止します。 _CrashLoopBackOff_ メッセージは、Kubernetes がポッドの再始動しようとしていることを示しています。

6.  ポッドがさらにリソースを消費しないように、ポッドを削除します。

    ```
    kubectl delete -f restore.yaml
    ```
    {: pre}

バックアップが正常にリストアされました。 これで、新規ポッドを pvc にマウントし、そのコンテナーに、リストアされたファイルへのアクセス権限を与えることができます。 バックアップされていたコンテナー・データに非 root ユーザーが含まれていた場合は、新規コンテナーに非 root の権限を追加する必要があります。 詳しくは、[非 root ユーザーのボリューム・アクセス権限の追加](../../../containers/container_volumes_ov.html#container_volumes_write)を参照してください。

## バックアップを暗号化する 
{: #encrypting_backups}

{{site.data.keyword.objectstorageshort}} インスタンス内のデータを暗号化します。

1.  <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="外部リンク・アイコン"></a> をダウンロードして暗号鍵を作成します。
2.  ローカル・ドライブで暗号鍵を作成します。 ENTER を押して、デフォルト値を受け入れることができます。

    **注意:** 作成したパスフレーズをメモします。 パスフレーズを紛失すると、鍵を使用して暗号化した情報を暗号化解除できなくなります。

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

4.  `sub` 鍵の値を使用して暗号鍵をエクスポートします。 ファイルに encryption.asc という名前を付けます。

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
    
6. _backup-pvc.yaml_ という名前の構成ファイルを作成します。 この構成ファイルは、バックアップ・ポッドにボリュームとしてマウントできる、永続ボリューム・クレーム (pvc) を作成します。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: backuppvc
      annotations:
        volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
    ```
    {: codeblock}

7. pvc を作成します。

    ```
    kubectl apply -f backup-pvc.yaml 
    ```
    {: pre}

8.  デプロイメント構成ファイルを編集して、以下のフィールドを追加します。 ブランクの環境変数には、前にメモした {{site.data.keyword.objectstorageshort}} 資格情報からの値を入力します。 資格情報に使用されている引用符を含めてください。 *ENCRYPTION_PASSPHRASE* には、バックアップをパスワードで保護するためのパスフレーズを含めます。 このパスフレーズは、暗号鍵を作成したときに作成したフレーズとは別のパスフレーズです。 このパスフレーズは、データのバックアップ時とリストア時に指定する必要があります。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: <pod_name>
    spec:
      containers:
        - name: <container_name>
          image: registry.<region>.bluemix.net/ibm-backup-restore
          env:
          - name: USERID
            value: 
          - name: PASSWORD
            value: 
          - name: PROJECTID
            value:
          - name: REGION
            value:
          - name: SCHEDULE_TYPE
            value: periodic
          - name: SCHEDULE_INFO
            value: daily
          - name: BACKUP_TYPE
            value: incremental
          - name: BACKUP_DIRECTORY
            value: /backup
          - name: BACKUP_NAME
            value: mybackup
          - name: ENCRYPTION_REQUIRED
            value: yes
          - name: ENCRYPTION_PASSPHRASE
           # 暗号化データをバックアップおよびリストアする際、このパスフレーズを組み込みます。
            value: 
          volumeMounts:
          - mountPath: /backup
            name: backup-volume
      volumes:
      - name: backup-volume 
        persistentVolumeClaim:
          claimName: backuppvc
   ```
   
   これらの設定では、<em>&lt;volume_name&gt;</em> という名前で暗号化された日次増分バックアップが作成されます。 別の設定でバックアップを作成するには、[環境変数のオプション](#reference_backup_restore) の全リストを参照してください。
    
9.  バックアップ・スクリプトを実行するポッドを作成します。

    ```
    kubectl apply -f backup.yaml 
    ```
    {: pre}

9.  ポッドが実行中であることを確認します。

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

10.  **ibm-backup-restore** イメージから作成されたコンテナーの /backup_restore ディレクトリーに、暗号鍵をコピーします。

    ```
    kubectl cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    データを暗号化解除するために、暗号鍵のコピーをローカルに保管します。

11. コンテナーにログインします。

    ```
    kubecl exec -it <container_name> bash
    ```
    {: pre}

12. *encryption.asc*  が backup_restore フォルダーにコピーされたことを確認します。

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

13. backup_restore フォルダーからバックアップ・スクリプトを実行します。

    ```
    ./vbackup &
    ```
    {: codeblock}

13. バックアップが暗号化されたことを確認するために、{{site.data.keyword.objectstorageshort}} インスタンス内のファイルを確認します。 現在、ファイルには、ファイル名の最後に `.gpg` が追加されています。![オブジェクト・ストレージ GUI に、ファイルが暗号化されていることを示す .gpg が追加されたすべてのバックアップ・ファイルが表示されています。](images/volume_backup_encrypt_screenshot.png)

バックアップが暗号化されました。 ファイルをリストアするには、[リストア・スクリプトを実行する](#restore_script_cli)の手順に従って、リストア処理を実行しているコンテナーの backup_restore ディレクトリーに encryption.asc ファイルを含めます。 バックアップが暗号化されている場合は、リストア・コンテナーの作成時に、環境変数としてバックアップ yaml ファイルから ENCRYPTION_REQUIRED と ENCRYPTION_PASSPHRASE を指定する必要があります。

## 環境変数リファレンス 
{: #reference_backup_restore}

フィールドの全リストを確認してください。フィールドの値は、環境変数として渡すことも、実行中のコンテナーの `config.conf` ファイルで編集することもできます。 環境変数として渡された値は、`config.conf` ファイルの値より優先されます。 コンテナーの環境変数を確認するには、`exec` を使用してコンテナーにログインし、`env` を実行します。

|キー|値のオプション|
|---|-------------|
|PROJECTID|{{site.data.keyword.objectstorageshort}} から取得したプロジェクト ID|
|REGION|{{site.data.keyword.objectstorageshort}} から取得した地域|
|USERID|{{site.data.keyword.objectstorageshort}} から取得したユーザー ID|
|PASSWORD|{{site.data.keyword.objectstorageshort}} から取得したパスワード|
{: caption="表 1. {{site.data.keyword.objectstorageshort}} の変数" caption-side="top"}

{{site.data.keyword.objectstorageshort}} 資格情報から以下の変数を取得します。 これらの環境変数は、バックアップまたはリストアの操作を実行するすべてのコンテナーに含めてください。

|キー|値のオプション|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: デフォルト。 ボリュームのマウント先のディレクトリーの絶対ファイル・パス。 データはこのディレクトリーからバックアップされます。 backup_restore ディレクトリーは選択しないでください。このディレクトリーには、バックアップとリストアの処理のためのファイルが入っています。|
|BACKUP_NAME|*volume_backup*: デフォルト。 バックアップ名を選択します。|
|BACKUP_TYPE|*full*: デフォルト。 毎回すべてのファイルがバックアップされます。<br/> *incremental*: 新規ファイルおよび変更されたファイルのみがバックアップされます。 *incremental* を選択した場合は、SCHEDULING_INFO と SCHEDULING_TYPE の値を選択する必要があります。|
|SCHEDULE_TYPE|*none*: デフォルト。 1 回限りのバックアップを作成します。<br/> **注:** 一回限りのバックアップを作成するよう選択した場合、ポッドは、バックアップ終了後にクラスターから削除されます。<br/>*periodic*: スケジュール・バックアップを作成するには、値を periodic に変更します。|
|SCHEDULE_INFO|*hourly*: 毎時バックアップを作成します。<br/>*daily*: デフォルト。 日次バックアップを作成します。<br/>*weekly*: 週次バックアップを作成します。 定期的更新をスケジュールする場合は、この変数を含める必要があります。|
|EXCLUDE_DIRECTORIES|*none*: デフォルト。 バックアップから除外するディレクトリーの絶対ファイル・パスを含めます。 複数のディレクトリーはコンマで区切ります。|
{: caption="表 2. バックアップの変数" caption-side="top"}

|キー|値のオプション|
|---|-------------|
|BACKUP_NAME|*volume_backup*: デフォルト。 {{site.data.keyword.objectstorageshort}} からリストアするバックアップの名前を入れます。|
|RESTORE_DIRECTORY|*/backup*: デフォルト。 ボリュームのマウント先の絶対ディレクトリー。 データはこのディレクトリーにリストアされます。 backup_restore ディレクトリーは選択しないでください。このディレクトリーには、バックアップとリストアの処理のためのファイルが入っています。|
{: caption="表 3. リストアの変数" caption-side="top"}

|キー|値のオプション|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: デフォルト。暗号鍵のファイル名を変更する場合、または backup_restore 以外のディレクトリーに鍵が配置されている場合、この環境変数を含めます。|
|ENCRYPTION_REQUIRED|*no*: デフォルト。<br/> *yes*: バックアップを暗号化しない場合は、暗号化の環境変数を含めないでください。 バックアップを暗号化する場合は、値を yes に設定してこのキーを含めてください。|
|ENCRYPTION_PASSPHRASE|バックアップを保護するためのパスフレーズを含めます。 このパスフレーズは、暗号鍵を作成したときに作成したフレーズとは別のパスフレーズです。 このパスフレーズは、データのバックアップ時とリストア時に指定する必要があります。|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: デフォルト。<br/> *yes*: 暗号鍵をコンテナーに直接生成した場合は、yes に設定してこの環境変数を含めます。 ほとんどのユーザーはローカル・コンピューターで鍵を生成してコンテナーにコピーするため、デフォルトの no のままでかまいません。|
{: caption="表 4. 暗号化の変数" caption-side="top"}

## 単一コンテナーおよびスケーラブル・コンテナーから Kubernetes にボリューム・データをマイグレーションする
{: #migrate_data}

任意のコンテナー・ボリュームの一回限りのバックアップを作成します。バックアップは {{site.data.keyword.objectstoragefull}} インスタンス内に保管されます。 その後、Kubernetes 内の永続ボリューム・クレームにデータをマイグレーションすることができます。
{:shortdesc}

### 概説 
{: #how_to_get_started_migrating}

始める前に、以下を実行してください。

- [アプリケーションを Kubernetes に移動するための完全なマイグレーション・パスを確認します](../../../containers/cs_classic.html)
- [単一コンテナーおよびスケーラブル・コンテナー CLI をインストールします (bx ic)](../../../containers/container_cli_cfic_install.html)
- [{{site.data.keyword.containershort}} CLI (bx cs および kubectl) をインストールします。](../../../containers/cs_cli_install.html#cs_cli_install)
- [データのマイグレーション先の標準 Kubernetes クラスターを作成します](../../../containers/cs_clusters.html#clusters_cli)

バックアップおよびリストアの操作を実行するには、以下のタスクを実行します。
1.  [{{site.data.keyword.objectstorageshort}} サービス・インスタンスの作成](#object_storage) (前の処理と同様)
2.  [一回限りのバックアップの実行](#migrate_backup)
3.  [Kubernetes でのリストア・スクリプトの実行](#migrate_restore)

### 一回限りのバックアップの実行 
{: #migrate_backup}

**ibm-backup-restore** イメージから単一コンテナーを作成し、バックアップを開始します。

1.  {{site.data.keyword.containershort}} CLI にログインします。

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

3.  環境変数ファイルを編集して、以下のフィールドを追加します。 ブランクの環境変数には、前にメモした {{site.data.keyword.objectstorageshort}} 資格情報からの値を入力します。 資格情報に使用されている引用符を含め**ない**でください。

    ```
    BACKUP_NAME=volume_backup
    BACKUP_DIRECTORY=/backup
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    ```
    {: codeblock}

    この設定では、デフォルト名 _volume_backup_ で一回限りのバックアップが作成されます。

4.  バックアップ対象のマウントされたボリュームを指定して、**ibm-backup-restore** イメージからコンテナーを実行します。 バックアップ・スクリプトを開始するコマンドを含めます。

    -   <em>&lt;backup_env-file&gt;</em> と同じローカル・ディレクトリー内にいることを確認します。
    -   ボリュームのマウント先のディレクトリーは、環境変数ファイルの BACKUP_DIRECTORY と一致していなければなりません。
    -   <em>&lt;volume_name&gt;</em> を、バックアップしようとしているボリュームの名前に置き換えます。
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<backup_env-file_name> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    バックアップの実行後、コンテナーをシャットダウンします。 コンテナーが実行を開始しない場合は、`bx ic logs <container_name>` を実行して、ログにエラー・メッセージが書き込まれていないか確認します。

5.  {{site.data.keyword.Bluemix_notm}} GUI で {{site.data.keyword.objectstorageshort}} 内のバックアップを確認します。
    1.  バックアップ用に作成した {{site.data.keyword.objectstorageshort}} インスタンスをクリックします。
    2.  {{site.data.keyword.objectstorageshort}} コンテナーをクリックします。 この例で、コンテナー名は volume_backup です。
    3.  圧縮ファイルを確認します。![ {{site.data.keyword.Bluemix_notm}} GUI の Object Storage コンテナーに、バックアップされたファイルが表示されます。](images/volume_backup_screenshot.png)difftar.gz ファイルをダウンロードし、ファイルを解凍して、バックアップ・データを検証できます。 **重要**: {{site.data.keyword.objectstorageshort}} のファイルを削除または変更すると、それらのファイルはリカバリーできません。

### Kubernetes クラスターへのデータのリストア 
{: #migrate_restore}

{{site.data.keyword.objectstorageshort}} からのバックアップを Kubernetes クラスター内の永続ボリューム・クレームにリストアします。

始める前に、以下を実行してください。

- [CLI のターゲット](../../../containers/cs_cli_install.html#cs_cli_configure)を自分のクラスターに設定します。


1. _restore-pvc.yaml_ という名前の構成ファイルを作成します。 この構成ファイルは、バックアップ・ポッドにボリュームとしてマウントできる、永続ボリューム・クレーム (pvc) を作成します。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: restorepvc
      annotations:
        volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
    ```
    {: codeblock}

2. pvc を作成します。

    ```
    kubectl apply -f restore-pvc.yaml 
    ```
    {: pre}

3.  _restore.yaml_ という名前の構成ファイルを作成します。 ブランクの環境変数には、前にメモした {{site.data.keyword.objectstorageshort}} 資格情報からの値を入力します。 資格情報に使用されている引用符を含めてください。 BACKUP_NAME は、{{site.data.keyword.objectstorageshort}} 内のリストアしようとしているバックアップの名前と一致していなければなりません。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: <pod_name>
    spec:
      containers:
        - name: <container_name>
          image: registry.<region>.bluemix.net/ibm-backup-restore
          env:
          - name: USERID
            value: 
          - name: PASSWORD
            value: 
          - name: PROJECTID
            value:
          - name: REGION
            value:
          - name: RESTORE_DIRECTORY
            value: /restore
          - name: BACKUP_NAME
            value: volume_backup
          command: ["/bin/bash", "./vrestore"]
          volumeMounts:
          - mountPath: /restore
            name: restore-volume
      volumes:
      - name: restore-volume 
        persistentVolumeClaim:
          claimName: restorepvc
    ```
    {: codeblock}

4.  リストア・スクリプトを実行するポッドを作成します。

    ```
    kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  ポッドが実行されていることを確認します。

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}
    
    ポッドは、リストア・コマンドを実行して、停止します。 _CrashLoopBackOff_ メッセージは、Kubernetes がポッドの再始動しようとしていることを示しています。

6.  ポッドがさらにリソースを消費しないように、ポッドを削除します。

    ```
    kubectl delete -f restore.yaml
    ```
    {: pre}

データが Kubernetes クラスターに正常にマイグレーションされました。 これで、新規ポッドを pvc にマウントし、そのポッドに、リストアされたファイルへのアクセス権限を与えることができます。 

**注**: バックアップされたコンテナー・データに非 root ユーザーが含まれていた場合は、新規コンテナーに非 root の権限を追加する必要があります。 詳しくは、[非 root ユーザーのボリューム・アクセス権限の追加](../../../containers/container_volumes_ov.html#container_volumes_write)を参照してください。
