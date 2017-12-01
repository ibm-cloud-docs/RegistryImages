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

# Introdução à imagem ibm-backup-restore 
{: #ibmbackup_restore_starter}

A imagem **ibm-backup-restore** contém os pacotes pré-instalados que são necessários para fazer backup e restaurar volumes do contêiner no {{site.data.keyword.containerlong}}.
{:shortdesc}

## Como ele Funciona 
{: #how_it_works}

Com a imagem **ibm-backup-restore**, é possível criar um backup único ou planejado para qualquer volume do contêiner. O backup é armazenado em uma instância do {{site.data.keyword.objectstoragefull}}. É possível passar as credenciais do {{site.data.keyword.objectstorageshort}} ao contêiner de **ibm-backup-restore** como variáveis de ambiente ou editando o arquivo `config.conf` no contêiner em execução. Você também pode restaurar os dados salvos da instância do {{site.data.keyword.objectstorageshort}} para um volume. Como a imagem contém scripts para executar backups e restaurações, o usuário deve emitir um comando para iniciar o script apropriado em um contêiner em execução.

## O que está incluído 
{: #whats_included}

Cada imagem **ibm-backup-restore** contém os seguintes pacotes de software:

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   pacotes python, gnupg e wget

## Iniciar 
{: #how_to_get_started}

Conclua as etapas a seguir para executar as operações de backup e restauração:
1.  [Criando uma instância de serviço {{site.data.keyword.objectstorageshort}}](#object_storage)
2.  [Executando um backup planejado](#scheduled_backup)
3.  [Executando o script de restauração](#restore_script_cli)
4.  [Criptografando seus backups](#encrypting_backups)
5.  [Referência de variável de ambiente](#reference_backup_restore)

## Criando uma instância de serviço do {{site.data.keyword.objectstorageshort}} 
{: #object_storage}

Crie uma instância do {{site.data.keyword.objectstorageshort}} para servir como repositório para seu backup de volume.

1.  Provisione sua instância do {{site.data.keyword.objectstorageshort}} na seção **Serviços** do catálogo do {{site.data.keyword.Bluemix_notm}}.
2.  Selecione a instância do {{site.data.keyword.objectstorageshort}}.
3.  Clique na guia **Credenciais de serviço**.
4.  Clique em **Nova credencial**.
5.  Preencha o campo de nome, mas deixe os outros campos em branco. Clique em **Incluir**.
6.  Suas novas credenciais agora estão listadas na tabela **Credenciais de serviço**. Clique em **Visualizar credenciais**.
7.  Anote **projectid**, **region**, **userId** e **password**. Essas credenciais permitem que o contêiner de **ibm-backup-restauração** acesse a instância do {{site.data.keyword.objectstorageshort}}.

Revise a documentação do [{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) para obter mais informações sobre como configurar a instância e revise as [opções de plano](../../ObjectStorage/objectstorage_faq.html#account-payment) para obter informações sobre precificação do {{site.data.keyword.objectstorageshort}}.

## Executando um backup planejado 
{: #scheduled_backup}

Crie um contêiner a partir da imagem **ibm-backup-restore** e inicie um backup planejado regularmente.

1.  Efetue login na CLI do {{site.data.keyword.containerlong_notm}}.

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

2.  Crie um arquivo de variável de ambiente em um diretório local.

    ```
    touch <backup_env-file_name>
    ```
    {: pre}

3.  Edite o arquivo de variável de ambiente e inclua os campos a seguir. Para as variáveis de ambiente em branco, insira os valores das credenciais do {{site.data.keyword.objectstorageshort}} que você anotou anteriormente. Não inclua as aspas usadas nas credenciais.

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

    Essas configurações criam um backup incremental diário com o nome padrão _volume_backup_. Para criar um backup com configurações diferentes, revise uma lista completa de [opções de variável de ambiente](#reference_backup_restore).

4.  Execute um contêiner a partir da imagem **ibm-backup-restore** com um volume montado para backup. Inclua o comando para iniciar o script de backup.

    -   Assegure-se de que você esteja no mesmo diretório local que o <em>&lt;backup_env-file&gt;</em>.
    -   O diretório no qual o volume é montado deve corresponder ao BACKUP_DIRECTORY no arquivo de variável de ambiente.
    -   Substitua o nome do volume pelo nome do volume do qual está sendo feito backup.
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<backup_env-file_name> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    Se o contêiner não começar a executar, execute `bx ic logs <container_name>` para revisar os logs para mensagens de erro.

5.  Revise o backup no {{site.data.keyword.objectstorageshort}} na GUI do {{site.data.keyword.Bluemix_notm}}.
    1.  Clique na instância do {{site.data.keyword.objectstorageshort}} que você criou para o backup.
    2.  Clique no contêiner do {{site.data.keyword.objectstorageshort}}. Neste exemplo, o nome do contêiner é volume_backup.
    3.  Revise os arquivos compactados.![O contêiner {{site.data.keyword.objectstorageshort}} na GUI do {{site.data.keyword.Bluemix_notm}} mostra os arquivos que foram submetidos a backup.](images/volume_backup_screenshot.png) É possível fazer download do arquivo `vol1.difftar.gz`, extrair o arquivo e verificar os dados submetidos a backup. Observe que se você excluir ou modificar quaisquer arquivos do {{site.data.keyword.objectstorageshort}}, esses arquivos não poderão ser recuperados.

Seu backup está disponível. Se você configurou *<container_name>* para criar um backup completo único, deverá executar o script de backup toda vez que desejar criar um novo backup. Se você configurou o contêiner para executar um backup incremental periodicamente, o backup será executado como planejado.

## Executando o script de restauração 
{: #restore_script_cli}

Restaure seu backup do {{site.data.keyword.objectstorageshort}} para um volume de contêiner existente ou novo.

1.  Crie um arquivo de variável de ambiente para seu contêiner de restauração em um diretório local.

    ```
    touch <restore_env-file_name>
    ```
    {: pre}

2.  Edite o arquivo de variável de ambiente e inclua os campos a seguir. Para as variáveis de ambiente em branco, insira os valores das credenciais do {{site.data.keyword.objectstorageshort}} que você anotou anteriormente. O BACKUP_NAME deve corresponder ao nome do backup no {{site.data.keyword.objectstorageshort}} que está sendo restaurado.

    ```
    BACKUP_NAME=<volume_name>
    RESTORE_DIRECTORY=/restore
    USERID=
    REGION=
    PROJECTID=
    PASSWORD=
    ```
    {: codeblock}

3.  Crie um volume para o qual restaurar os arquivos de backup.

    ```
    bx ic volume create <volume_name>
    ```
    {: pre}

4.  Execute um contêiner a partir da imagem **ibm-backup-restore**. Inclua o comando para iniciar o script de restauração.

    -   Assegure-se de que você esteja no mesmo diretório local que o <em>&lt;restore_env-file&gt;</em>.
    -   O diretório no qual o volume é montado deve corresponder ao BACKUP_DIRECTORY no arquivo de variável de ambiente.
    
    ```
    bx ic run --name <container_name> --volume <volume_name>:/restore --env-file ./<restore_env-file_name> registry.ng.buemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vrestore"
    ```
    {: pre}

5.  Verifique os logs do contêiner para confirmar se o processo de restauração foi concluído.

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

    Quando o processo de restauração for concluído, o contêiner será encerrado.

6.  Opcional: remova o contêiner de maneira que ele não consuma nenhum recurso.

    ```
    bx ic rm -f <container_name>
    ```
    {: pre}

7. Opcional: para confirmar que seus arquivos submetidos a backup estão localizados no volume, efetue login no contêiner, navegue até o caminho de montagem e liste os arquivos.

    ```
    $ bx ic exec -it <container_name> bash
    root@instance:/backup_restore# cd ..
    root@instance/# cd restore
    root@instance:/restore# ls
    duplicity-I3_pJv-tempdir  new_file
    root@instance:/restore# 
    ```
    {: screen}

    O tempdir é um produto do processo de backup e pode ser excluído manualmente.


Você restaurou com êxito o backup para o volume *volume_name*. Agora é possível montar qualquer novo contêiner no volume para dar a esse contêiner acesso aos arquivos restaurados. Se os dados do contêiner cujo backup foi feito incluíam um usuário não raiz, deve-se incluir permissões não raiz no novo contêiner. Consulte [Incluindo acesso de usuário não raiz nos volumes](../../../containers/container_volumes_ov.html#container_volumes_write) para obter mais informações.

## Criptografando seus backups 
{: #encrypting_backups}

Criptografe os dados em sua instância do {{site.data.keyword.objectstorageshort}}.

1.  Faça download do <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Ícone de link externo"></a> para criar uma chave de criptografia.
2.  Crie uma chave de criptografia em sua unidade local. É possível aceitar os valores padrão pressionando ENTER.

    **Atenção:** anote a passphrase que você criar. Se você perder sua passphrase, qualquer informação criptografada com sua chave não poderá ser decriptografada.

    ```
    gpg --gen-key
    ```
    {: pre}

    Dependendo da versão do <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Ícone de link externo"></a>, talvez seja necessário usar o `gpg2` em vez de `gpg` em seus comandos.

3.  Verifique a chave.

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

4.  Exporte a chave de criptografia com o valor da chave `sub`. Nomeie o arquivo encryption.asc.

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    Nesse exemplo, a chave sub tem o valor *YYYYYYYY*.

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  Crie um arquivo de variável de ambiente para seu contêiner de backup criptografado em um diretório local.

    ```
    touch <encryption_env-file_name>
    ```
    {: pre}

6.  Edite o arquivo de variável de ambiente e inclua os campos a seguir. Para as variáveis de ambiente em branco, insira os valores das credenciais do {{site.data.keyword.objectstorageshort}} que você anotou anteriormente. Não inclua as aspas usadas nas credenciais. Parar *ENCRYPTION_PASSPHRASE*, inclua uma passphrase para proteger o backup com senha. Essa passphrase é diferente da frase que você criou quando criou a chave de criptografia. Deve-se incluir essa passphrase ao fazer backup dos dados e restaurá-los.

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

    Essas configurações criam um backup incremental diário criptografado com o nome *<volume_name>*. Para criar um backup com configurações diferentes, revise uma lista completa de [opções de variável de ambiente](#reference_backup_restore).

7.  Efetue login na CLI do {{site.data.keyword.containerlong_notm}}.

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

8.  Execute um contêiner a partir da imagem **ibm-backup-restore** com um volume montado para backup. Inclua o comando para iniciar o script de backup.

    -   Assegure-se de que você esteja no mesmo diretório local que o <em>&lt;encryption_env-file_name&gt;</em>.
    -   O diretório no qual o volume é montado deve corresponder ao BACKUP_DIRECTORY no arquivo de variável de ambiente.
    -   Substitua o nome do volume pelo nome do volume do qual está sendo feito backup.
   
    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<encrypted_env-file_name> registry.ng.bluemix.net/ibm-backup-restore
    ```
    {: pre}

    Se o contêiner não começar a executar, execute `bx ic logs <container_name>` para revisar os logs para mensagens de erro.

9.  Copie a chave de criptografia no diretório /backup_restore do contêiner que é construído por meio da imagem **ibm-backup-restore**.

    ```
    bx ic cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    Mantenha uma cópia da chave de criptografia localmente para decriptografar seus dados.

10. Efetue login no contêiner.

    ```
    bx ic exec -it <container_name> bash
    ```
    {: pre}

11. Confirme se *encryption.asc* foi copiado na pasta backup_restore.

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

12. Execute o script de backup na pasta backup_restore.

    ```
    ./vbackup &
    ```
    {: codeblock}

13. Para confirmar se seu backup é criptografado, revise os arquivos em sua instância do {{site.data.keyword.objectstorageshort}}. Os arquivos agora têm `.gpg` anexado ao final do nome do arquivo.![A GUI {{site.data.keyword.objectstorageshort}} mostra todos os arquivos submetidos a backup anexados com .gpg, o que mostra que eles estão criptografados.](images/volume_backup_encrypt_screenshot.png)

Seu backup está criptografado. Para restaurar os arquivos, siga as etapas em [Executando o script de restauração](#restore_script_cli) e inclua o arquivo encryption.asc no diretório backup_restore no contêiner que está executando o processo de restauração. Se o backup for criptografado, ENCRYPTION_REQUIRED e ENCRYPTION_PASSPHRASE deverão ser fornecidas como variáveis de ambiente ao criar o contêiner de restauração.

## Referência de variável de ambiente 
{: #reference_backup_restore}

Revise a lista completa de campos que podem ser passados como variáveis de ambiente ou editados no arquivo `config.conf` em um contêiner em execução. Qualquer valor que é passado como variável de ambiente suplanta o valor no arquivo `config.conf`. Para revisar as variáveis de ambiente para um contêiner, efetue login no contêiner usando `exec` e execute `env`.

|Chave|Opções de valor|
|---|-------------|
|PROJECTID|ProjectID do {{site.data.keyword.objectstorageshort}}|
|REGION|Região do {{site.data.keyword.objectstorageshort}}|
|USERID|ID do usuário do {{site.data.keyword.objectstorageshort}}|
|PASSWORD|Senha do {{site.data.keyword.objectstorageshort}}|
{: caption="Tabela 1. Variáveis do {{site.data.keyword.objectstorageshort}}" caption-side="top"}

Recupere essas variáveis a partir das credenciais do {{site.data.keyword.objectstorageshort}}. Inclui essas variáveis de ambiente com cada contêiner que está executando uma operação de backup ou restauração.

|Chave|Opções de valor|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: padrão. Caminho de arquivo absoluto do diretório no qual o volume é montado. O backup dos dados é feito a partir desse diretório. Não selecione o diretório backup_restore, pois ele contém arquivos para os processos de backup e restauração.|
|BACKUP_NAME|*volume_backup*: padrão. Escolha um nome de backup.|
|BACKUP_TYPE|*full*: padrão. É feito backup de todos os arquivos toda vez.<br/> *incremental*: é feito backup apenas de arquivos novos ou mudados. Se você escolher *incremental*, deverá escolher valores para SCHEDULING_INFO e SCHEDULING_TYPE.|
|SCHEDULE_TYPE|*none*: padrão. Crie um backup único.<br/> *periodic*: mude o valor para periódico para criar backups planejados.|
|SCHEDULE_INFO|*hourly*: crie um backup a cada hora.<br/>*daily*: padrão. Crie um backup diário.<br/>*weekly*: crie um backup semanal. Deve-se incluir essa variável se você estiver planejando uma atualização periódica.|
|EXCLUDE_DIRECTORIES|*none*: padrão. Inclua o caminho de arquivo absoluto dos diretórios que você deseja excluir do backup. Separe os diretórios com uma vírgula.|
{: caption="Tabela 2. Variáveis de backup" caption-side="top"}

|Chave|Opções de Valor|
|---|-------------|
|BACKUP_NAME|*volume_backup*: padrão. Inclua o nome do backup que está sendo restaurado do {{site.data.keyword.objectstorageshort}}.|
|RESTORE_DIRECTORY|*/backup*: padrão. Diretório absoluto no qual o volume é montado. Os dados são restaurados para esse diretório. Não selecione o diretório backup_restore, pois ele contém arquivos para os processos de backup e restauração.|
{: caption="Tabela 3. Variáveis de restauração" caption-side="top"}

|Chave|Opções de valor|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: padrão. Inclua essa variável de ambiente se você mudar o nome do arquivo da chave de criptografia ou a chave estiver localizada em um diretório diferente de backup_restore.|
|ENCRYPTION_REQUIRED|*no*: padrão.<br/> *yes*: se você não estiver criptografando seu backup, não inclua quaisquer variáveis de ambiente de criptografia. Se você estiver criptografando o seu backup, inclua essa chave com o valor yes.|
|ENCRYPTION_PASSPHRASE|Inclua uma passphrase para proteger um backup. Essa passphrase é diferente da frase que você criou quando criou a chave de criptografia. Deve-se incluir essa passphrase ao fazer backup dos dados e restaurá-los.|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: padrão.<br/> *yes*: inclua essa variável de ambiente com yes se você gerou a chave de criptografia diretamente no contêiner. A maioria dos usuários gera a chave em seu computador local e copia a chave no contêiner e podem deixar o padrão como no.|
{: caption="Tabela 4. Variáveis de criptografia" caption-side="top"}

