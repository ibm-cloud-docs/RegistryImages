---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-13"

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

# `ibm-backup-restore` イメージの概説
{: #ibmbackup_restore_starter}

`ibm-backup-restore` イメージには、{{site.data.keyword.containerlong}} での永続ストレージのバックアップとリストアに必要なプリインストール・パッケージが入っています。
{:shortdesc}

{{site.data.keyword.IBM_notm}} によって提供されるイメージには、コマンド・ラインを使用してアクセスできます。[IBM のパブリック・イメージ](/docs/services/Registry?topic=registry-public_images#public_images)を参照してください。
{: tip}

## 機能
{: #backup_restore_how_it_works}

`ibm-backup-restore` イメージを使用して、クラスター内の永続ボリューム (PV) に保管されたアプリケーション・データの一回限りのバックアップまたはスケジュールされたバックアップを作成することや、アプリケーション・データを PV にリストアすることができます。 データのバックアップとリストアを行うには、`ibm-backup-restore` イメージからポッドをデプロイします。 その後、バックアップする PV、またはデータのリストアに使用する PV をバインドする PVC をポッドにマウントします。

**自分のデータはどこに行くのですか? どのようにしてアクセスしますか?**

バックアップしたデータは、{{site.data.keyword.cos_full_notm}} サービス・インスタンス内に保管されます。 サービスにアクセスするには、`ibm-backup-restore` ポッドの環境変数として {{site.data.keyword.cos_full_notm}} サービス資格情報を使用するか、実行中のポッドの `config.conf` ファイルを編集します。

**バックアップしたデータを別のアプリケーションまたは別の PV にリストアできますか?**

はい。保存したデータを {{site.data.keyword.cos_full_notm}} サービス・インスタンスからクラスター内の PV にリストアすることが可能です。 データをリストアするには、`ibm-backup-restore` イメージからリストア・ポッドを作成します。 その後、使用する PV をバインドする PVC をポッドにマウントします。  

## 含まれている内容
{: #backup_restore_whats_included}

各 `ibm-backup-restore` イメージには、以下のソフトウェア・パッケージが含まれています。

- Alpine 3.7
- Duplicity 0.7.10
- Python および GnuPG Made Easy (GPGME) パッケージ

## {{site.data.keyword.cos_full_notm}} サービス・インスタンスのセットアップ
{: #backup_restore_setup_object_storage}

バックアップするデータのリポジトリーとなる {{site.data.keyword.cos_full_notm}} サービス・インスタンスを作成して構成します。
{: shortdesc}

1. {{site.data.keyword.cos_full_notm}} サービス・インスタンスをデプロイします。
   1. [{{site.data.keyword.Bluemix_notm}} カタログ ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://cloud.ibm.com/catalog/services/cloud-object-storage) を開きます。
   2. サービス・インスタンスの名前 (`cos-backup` など) を入力し、リソース・グループとして**「デフォルト」**を選択します。
   3. [プランのオプション ![外部リンク・アイコン](../../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.ibm.com/cloud-computing/bluemix/pricing-object-storage#s3api) で料金情報を確認し、プランを選択します。
   4. **「作成」**をクリックします。
2. {{site.data.keyword.cos_full_notm}} サービス・インスタンスの資格情報を取得します。
   1. サービス詳細ページのナビゲーションで、**「サービス資格情報」**をクリックします。
   2. **「新規資格情報」**をクリックします。 ダイアログ・ボックスが表示されます。
   3. 資格情報に対する名前を入力します。
   4. **「インラインの構成パラメーターの追加 (オプション)」**に、`{"HMAC":true}` と入力します。これにより、`ibm-backup-restore` ポッドが {{site.data.keyword.cos_full_notm}} サービスでの HMAC 認証に使用する、追加の HMAC 資格情報を作成します。
   5. **「追加」**をクリックします。 新しい資格情報が**「サービス資格情報」**の表にリストされます。
   6. **「資格情報の表示」**をクリックします。
   7. **cos_hmac_keys** セクションの **access_key_id** と **secret_access_key** をメモします。
3. 最初の {{site.data.keyword.cos_full_notm}} バケットを作成します。
   1. サービス詳細ページのナビゲーションで、**「バケット」**をクリックします。
   2. **「バケットの作成」**をクリックします。 ダイアログ・ボックスが表示されます。
   3. バケットに対する固有の名前を入力します。 この名前は、{{site.data.keyword.cos_full_notm}} 内で、すべての地域およびすべての {{site.data.keyword.Bluemix_notm}} アカウントにわたって固有でなければなりません。
   4. **「回復力」**ドロップダウンから、データに必要な可用性のレベルを選択します。 詳しくは、[{{site.data.keyword.cos_full_notm}} の地域とエンドポイント](/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-endpoints#endpoints)を参照してください。
   5. **「ロケーション」**を変更して、データを保管する地域に設定します。 法律上の理由により、データをすべての地域で保管できるとは限らないことに注意してください。  
   6. **「作成」**をクリックします。
4. バケットの {{site.data.keyword.cos_full_notm}} ホスト名を取得します。
   1. 前のステップで作成したバケット名をクリックします。
   2. サービス詳細ページのナビゲーションで、**「バケット」**>**「構成」**をクリックします。
   3. バケットのデータにアクセスするためのパブリック URL をメモします。

サービス・インスタンスの構成について詳しくは、[{{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage?topic=cloud-object-storage-about-ibm-cloud-object-storage#about-ibm-cloud-object-storage) の資料を参照してください。

## 永続ボリュームからのデータのバックアップ
{: #backup_restore_scheduled_backup}

永続ボリューム・クレーム (PVC) を通じてアプリケーション・ポッドにマウントされた永続ボリューム (PV) の一回限りのバックアップまたはスケジュールされたバックアップを作成することができます。  
{: shortdesc}

以下の例では、`ibm-backup-restore` イメージからバックアップ・ポッドをデプロイし、PVC を使用して既存の PV をバックアップ・ポッドにマウントし、PV から {{site.data.keyword.cos_full_notm}} サービス・インスタンスにデータをバックアップする一連の方法を順に示します。  

**開始する前に**

- [{{site.data.keyword.cos_full_notm}} サービス・インスタンスをセットアップします](#backup_restore_setup_object_storage)。
- クラスターの作成と作業に必要な [CLI](/docs/containers?topic=containers-cs_cli_install#cs_cli_install) をインストールします。
- [標準クラスターを作成する](/docs/containers?topic=containers-clusters#clusters_cli)か、既存のものを使用します。
- [CLI のターゲットを自分のクラスターに設定します](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)。
- [ファイル・ストレージ](/docs/containers?topic=containers-file_storage#add_file)または[ブロック・ストレージ](/docs/containers?topic=containers-block_storage#add_block)用の永続ボリューム・クレーム (PVC) を作成して、アプリケーション・デプロイメントにマウントします。

既存の PV をバックアップするには、以下のステップを実行します。

1. バックアップする PV をバインドする PVC の名前を取得します。

   ```
   kubectl get pvc
   ```
   {: pre}

2. `ibm-backup-restore` イメージからバックアップ・ポッドを作成します。 PV のデータにアクセスするには、PV をバインドする PVC をバックアップ・ポッドにマウントする必要があります。 以下の例では、日次増分バックアップを実行するバックアップ・ポッドを作成します。 別の設定のバックアップを作成するには、すべての[環境変数オプション](#backup_restore_env_reference)のリストを参照してください。

   `ibm-backup-restore` イメージは、単一ポッドにデプロイされる必要があり、Kubernetes デプロイメントの一部として使用することはできません。
   {: important}

   イメージを表示するには、`ibmcloud cr region-set global` コマンドを実行してグローバル・レジストリーをターゲットにします。 その後、`ibmcloud cr images --include-ibm` を実行して、IBM パブリック・イメージをリストします。
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
         value: '<access_key_id>'
       - name: SECRET_ACCESS_KEY
         value: '<secret_access_key>'
       - name: ENDPOINT
         value: '<regional_endpoint>'
       - name: BUCKET_NAME
         value: '<bucket_name>'
       - name: BACKUP_DIRECTORY
         value: /myvol
       - name: BACKUP_NAME
         value: <backup_name>
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
         claimName: <pvc_name>  
   ```
   {: codeblock}
   
   <table>
   <caption>表 1. YAML ファイルの構成要素</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="アイデア・アイコン"/> YAML ファイル構成要素の説明</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>{{site.data.keyword.cos_full_notm}} サービス・インスタンスの資格情報の一部として取得したアクセス・キー ID。</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</code></td>
     <td>{{site.data.keyword.cos_full_notm}} サービス・インスタンスの資格情報の一部として取得した秘密アクセス・キー。</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>特定地域で {{site.data.keyword.cos_full_notm}} にアクセスするために使用する地域 API エンドポイントの URL。 </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>{{site.data.keyword.cos_full_notm}} にバックアップを保管するために使用するバケットの名前。 </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>バケットでバックアップを保持するオブジェクトの固有の名前 </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>バックアップする PV をバインドする PVC の名前。 </td>
     </tr>
     </tbody>
     </table>
    
3. バックアップ・ポッドを作成し、PV データのバックアップを開始します。

    ```
    kubectl apply -f backuppod.yaml
    ```
    {: pre}

4. ポッドが実行中であることを確認します。

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

5. バックアップが正常に実行されたことを確認します。

    ```
    kubectl logs backuppod
    ```
    {: pre}

6. {{site.data.keyword.Bluemix_notm}} GUI で {{site.data.keyword.cos_full_notm}} 内のバックアップを確認します。
    1. {{site.data.keyword.Bluemix_notm}} ダッシュボードで {{site.data.keyword.cos_full_notm}} サービス・インスタンスを見つけます。
    2. ナビゲーションで**「バケット」**を選択し、バックアップ構成で使用したバケットをクリックします。 バックアップが、バケット内のオブジェクトとして表示されます。
    3. 圧縮ファイルを確認します。 `vol1.difftar.gz` ファイルをダウンロードし、ファイルを解凍して、バックアップ・データを検証できます。

       {{site.data.keyword.cos_full_notm}} からのファイルを削除または変更すると、それらのファイルはリカバリーできません。
       {: important}

バックアップが有効になりました。 一回限りのフルバックアップを作成するようにバックアップを構成した場合、新規バックアップの作成が必要になるたびに、バックアップ・スクリプトを実行する必要があります。 定期的に増分バックアップを実行するようにコンテナーを構成した場合は、スケジュールに従ってバックアップが実行されます。

## {{site.data.keyword.cos_full_notm}} からクラスターへのデータのリストア
{: #backup_restore_restore_script_cli}

{{site.data.keyword.cos_full_notm}} サービス・インスタンスからクラスター内の PV にデータをリストアすることができます。

**開始する前に**

- [CLI のターゲットを自分のクラスターに設定します](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)。
- [クラスター内の PV のバックアップを作成します](#backup_restore_scheduled_backup)。

{{site.data.keyword.cos_full_notm}} から PV にデータをリストアするには、以下のステップを実行します。

1. データをリストアする PV をバインドする PVC の名前を取得します。

   ```
   kubectl get pvc
   ```
   {: pre}

2. `ibm-backup-restore` イメージからリストア・ポッドを作成します。 PV にデータをリストアするには、PV をバインドする PVC をリストア・ポッドにマウントする必要があります。

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
         value: '<access_key_ID>'
       - name: SECRET_ACCESS_KEY
         value: '<secret_access_key>'
       - name: ENDPOINT
         value: '<regional_endpoint>'
       - name: BUCKET_NAME
         value: '<bucket_name>'
       - name: RESTORE_DIRECTORY
         value: /myvol
       - name: BACKUP_NAME
         value: <backup_name>
       command: ["/bin/sh", "./vrestore"]
       volumeMounts:
       - mountPath: /myvol
         name: restore-volume
     volumes:
     - name: restore-volume
       persistentVolumeClaim:
         claimName: <pvc_name>
   ```
   {: codeblock}

   <table>
   <caption>表 2. YAML ファイルの構成要素</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="アイデア・アイコン"/> YAML ファイル構成要素の説明</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>{{site.data.keyword.cos_full_notm}} サービス・インスタンスの資格情報の一部として取得したアクセス・キー ID。</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</code></td>
     <td>{{site.data.keyword.cos_full_notm}} サービス・インスタンスの資格情報の一部として取得した秘密アクセス・キー。</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>特定地域で {{site.data.keyword.cos_full_notm}} にアクセスするために使用する地域 API エンドポイントの URL。 </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>{{site.data.keyword.cos_full_notm}} にバックアップを保管するために使用したバケットの名前。 </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>バケットでバックアップを保持するオブジェクトの固有の名前。 {{site.data.keyword.cos_full_notm}} にデータを保管するためにバックアップ・ポッドで使用した名前を必ず使用してください。 </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>データをリストアする PV をバインドする PVC の名前。 </td>
     </tr>
     </tbody>
     </table>

3. リストア・ポッドを作成し、データのリストアを開始します。

    ```
    kubectl apply -f restorepod.yaml
    ```
    {: pre}

4. ポッドが実行中であることを確認します。

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME              READY     STATUS             RESTARTS   AGE
    restorepod        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    ポッドは、リストア・コマンドを実行して、停止します。 `CrashLoopBackOff` メッセージは、Kubernetes がポッドの再始動しようとしていることを示しています。

5. ポッドがさらにリソースを消費しないように、ポッドを削除します。

    ```
    kubectl delete -f restorepod.yaml
    ```
    {: pre}
    
6. データが正常にリストアされたことを確認します。

    ```
    kubectl logs restorepod
    ```
    {: pre}

バックアップが正常にリストアされました。 これで、PV をバインドする PVC をクラスター内の他の任意のポッドにマウントして、リストアされたファイルにアクセスすることができます。 バックアップされていたコンテナー・データに非 root ユーザーが含まれていた場合は、新規コンテナーに非 root の権限を追加する必要があります。 詳しくは、[非 root ユーザーのボリューム・アクセス権限の追加](/docs/containers?topic=containers-cs_troubleshoot_storage#cs_storage_nonroot)を参照してください。

## バックアップを暗号化する
{: #backup_restore_encrypting_backups}

{{site.data.keyword.cos_full_notm}} インスタンス内のデータを暗号化します。

1. <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="外部リンク・アイコン"></a> をダウンロードして暗号鍵を作成します。
2. ローカル・ドライブで暗号鍵を作成します。 ENTER を押して、デフォルト値を受け入れることができます。

    作成したパスフレーズをメモします。 パスフレーズを紛失すると、鍵を使用して暗号化した情報を復号できなくなります。
    {: important}

    ```
    gpg --gen-key
    ```
    {: pre}

    <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="外部リンク・アイコン"></a> のバージョンにより、コマンドで `gpg` ではなく `gpg2` を使用する必要がある場合があります。

3. 鍵を確認します。

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

4. `sub` 鍵の値を使用して暗号鍵をエクスポートします。 ファイルに `encryption.asc` という名前を付けます。

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    この例では、sub 鍵の値は `YYYYYYYY` です。

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  ローカル・ディレクトリー内に暗号化バックアップ・コンテナーの環境変数ファイルを作成します。

    ```
    touch <encryption_env-file_name>
    ```
    {: pre}

6.  ポッド構成ファイルを編集して、以下のフィールドを追加します。 ブランクの環境変数には、前にメモした {{site.data.keyword.cos_full_notm}} 資格情報からの値を入力します。 資格情報に使用されている引用符を含めてください。 **ENCRYPTION_PASSPHRASE** には、バックアップをパスワードで保護するためのパスフレーズを含めます。 このパスフレーズは、暗号鍵を作成したときに作成したフレーズとは別のパスフレーズです。 このパスフレーズは、データのバックアップ時とリストア時に指定する必要があります。

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
          value: '<access_key_id>'
        - name: SECRET_ACCESS_KEY
          value: '<secret_access_key>'
        - name: ENDPOINT
          value: '<regional_endpoint>'
        - name: BUCKET_NAME
          value: '<bucket_name>'
        - name: BACKUP_DIRECTORY  
          value: /myvol
        - name: BACKUP_NAME
          value: <backup_name> 
        - name: SCHEDULE_TYPE
            value: periodic
        - name: SCHEDULE_INFO
            value: daily
        - name: BACKUP_TYPE
          value: incremental
        - name: ENCRYPTION_REQUIRED
            value: yes
        - name: ENCRYPTION_PASSPHRASE
          value: <passphrase>
        volumeMounts:
        - mountPath: /myvol
          name: backup-volume
      volumes:
      - name: backup-volume
        persistentVolumeClaim:
          claimName: <pvc_name>  
    ```
    {: codeblock}
   
    <table>
    <caption>表 3. YAML ファイルの構成要素</caption>
    <thead>
    <th colspan=2><img src="../images/idea.png" alt="アイデア・アイコン"/> YAML ファイル構成要素の説明</th>
    </thead>
     <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>{{site.data.keyword.cos_full_notm}} サービス・インスタンスの資格情報の一部として取得したアクセス・キー ID。</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</em></code></td>
     <td>{{site.data.keyword.cos_full_notm}} サービス・インスタンスの資格情報の一部として取得した秘密アクセス・キー。</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>特定地域で {{site.data.keyword.cos_full_notm}} にアクセスするために使用する地域 API エンドポイントの URL。 </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>{{site.data.keyword.cos_full_notm}} にバックアップを保管するために使用するバケットの名前。 </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>バケットでバックアップを保持するオブジェクトの固有の名前。</td>
     </tr>
     <tr>
     <td><code>&lt;passphrase&gt;</code></td>
     <td>バックアップに使用するストリング。 データのリストア時に、このパスフレーズを含める必要があります。 </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>バックアップする PV をバインドする PVC の名前。 </td>
     </tr>
     </tbody>
     </table>
   
    これらの設定では、暗号化された日次増分バックアップが作成されます。 別の設定のバックアップを作成するには、すべての[環境変数オプション](#backup_restore_env_reference)のリストを参照してください。
    
7.  バックアップ・ポッドを作成します。 

    ```
    kubectl apply -f backuppod.yaml 
    ```
    {: pre}

8.  ポッドが実行中であることを確認します。

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

9.  `ibm-backup-restore` イメージから作成されたコンテナーの `/backup_restore` ディレクトリーに、暗号鍵をコピーします。

    ```
    kubectl cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    データを暗号化解除するために、暗号鍵のコピーをローカルに保管します。

10. ポッドにログインし、`backup_restore` フォルダーにナビゲートします。 

    ```
    kubecl exec -it <pod_name> bash
    ```
    {: pre}

11. `encryption.asc` ファイルが `backup_restore` フォルダーにコピーされたことを確認します。

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

13. バックアップが暗号化されたことを確認するために、{{site.data.keyword.cos_full_notm}} サービス・インスタンス内のファイルを確認します。 ファイル名の最後に、`.gpg` が付加されています。

バックアップが暗号化されました。 ファイルをリストアするには、[{{site.data.keyword.cos_full_notm}} からクラスター内の PVC へのデータのリストア](#backup_restore_restore_script_cli)の手順に従って、リストア処理を実行するポッドの `backup_restore` ディレクトリーに `encryption.asc` ファイルを含めます。 バックアップが暗号化されている場合は、リストア・ポッドの作成時に、**ENCRYPTION_REQUIRED** と **ENCRYPTION_PASSPHRASE** の環境変数を指定する必要があります。

## 環境変数リファレンス
{: #backup_restore_env_reference}

フィールドの全リストを確認してください。フィールドの値は、環境変数として渡すことも、実行中のポッドの `config.conf` ファイルで編集することもできます。 環境変数として渡された値は、`config.conf` ファイルの値より優先されます。 ポッドの環境変数を確認するには、`kubectl exec` コマンドを使用してポッドにログインし、`env` を実行します。

|キー|値のオプション|
|---|-------------|
|ACCESS_KEY_ID|{{site.data.keyword.cos_full_notm}} の HMAC 資格情報の一部である **access_key_id**。|
|SECRET_ACCESS_KEY|{{site.data.keyword.cos_full_notm}} の HMAC 資格情報の一部である **secret_access_key**。|
|ENDPOINT|{{site.data.keyword.cos_full_notm}} バケット・データにアクセスするためのホスト名。|
|BUCKET|{{site.data.keyword.cos_full_notm}} 内でバックアップ・データが保管されるバケットの名前。|
{: caption="表 4. {{site.data.keyword.cos_full_notm}} の変数" caption-side="top"}

|キー|値のオプション|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: デフォルト。 ボリュームのマウント先のディレクトリーの絶対ファイル・パス。 データはこのディレクトリーからバックアップされます。 backup_restore ディレクトリーは選択しないでください。このディレクトリーには、バックアップとリストアの処理のためのファイルが入っています。|
|BACKUP_NAME|*volume_backup*: デフォルト。 バックアップ名を選択します。|
|BACKUP_TYPE|*full*: デフォルト。 毎回すべてのファイルがバックアップされます。<br/> *incremental*: 新規ファイルおよび変更されたファイルのみがバックアップされます。 *incremental* を選択した場合は、**SCHEDULING_INFO** と **SCHEDULING_TYPE** の値を選択する必要があります。|
|SCHEDULE_TYPE|*none*: デフォルト。 1 回限りのバックアップを作成します。<br/> **注:** 一回限りのバックアップを作成するよう選択した場合、ポッドは、バックアップ終了後にクラスターから削除されます。 <br/> *periodic*: スケジュール・バックアップを作成するには、値を periodic に変更します。|
|SCHEDULE_INFO|*hourly*: 毎時バックアップを作成します。<br/>*daily*: デフォルト。 日次バックアップを作成します。<br/>*weekly*: 週次バックアップを作成します。 定期的更新をスケジュールする場合は、この変数を含める必要があります。|
|EXCLUDE_DIRECTORIES|*none*: デフォルト。 バックアップから除外するディレクトリーの絶対ファイル・パスを含めます。 複数のディレクトリーはコンマで区切ります。|
{: caption="表 5. バックアップの変数" caption-side="top"}

|キー|値のオプション|
|---|-------------|
|BACKUP_NAME|*volume_backup*: デフォルト。 {{site.data.keyword.cos_full_notm}} からリストアするバックアップの名前を入れます。|
|RESTORE_DIRECTORY|*/backup*: デフォルト。 ボリュームのマウント先の絶対ディレクトリー。 データはこのディレクトリーにリストアされます。 `backup_restore` ディレクトリーは選択しないでください。このディレクトリーには、バックアップとリストアの処理のためのファイルが入っています。|
{: caption="表 6. リストアの変数" caption-side="top"}

|キー|値のオプション|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: デフォルト。 暗号鍵のファイル名を変更する場合、または `backup_restore` 以外のディレクトリーに鍵が配置されている場合、この環境変数を含めます。|
|ENCRYPTION_REQUIRED|*no*: デフォルト。<br/> *yes*: バックアップを暗号化しない場合は、暗号化の環境変数を含めないでください。 バックアップを暗号化する場合は、値を `yes` に設定してこのキーを含めてください。|
|ENCRYPTION_PASSPHRASE|バックアップを保護するためのパスフレーズを含めます。 このパスフレーズは、暗号鍵を作成したときに作成したフレーズとは別のパスフレーズです。 データのバックアップ時とリストア時に、このパスフレーズを含める必要があります。|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: デフォルト。<br/> *yes*: 暗号鍵をコンテナーに直接生成した場合は、`yes` に設定してこの環境変数を含めます。 ほとんどのユーザーはローカル・コンピューターで鍵を生成してポッドにコピーするため、デフォルトの `no` のままでかまいません。|
{: caption="表 7. 暗号化の変数" caption-side="top"}
