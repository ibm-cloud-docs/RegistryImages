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

# Introdução à imagem ibm-backup-restore
{: #ibmbackup_restore_starter}

A imagem **ibm-backup-restore** contém os pacotes pré-instalados que são necessários para fazer backup e restaurar armazenamento persistente no {{site.data.keyword.containerlong}}.
{:shortdesc}

## Como ele Funciona 
{: #how_it_works}

Com a imagem **ibm-backup-restore**, é possível criar um backup único ou planejado para qualquer persistent volume claim (pvc). O backup é armazenado em uma instância do {{site.data.keyword.objectstoragefull}}. É possível passar as credenciais do {{site.data.keyword.objectstorageshort}} ao contêiner de **ibm-backup-restore** como variáveis de ambiente ou editando o arquivo `config.conf` no contêiner em execução. Você também pode restaurar os dados salvos da instância do {{site.data.keyword.objectstorageshort}} para um volume. Como a imagem contém scripts para executar backups e restaurações, o usuário deve inserir um comando para iniciar o script apropriado.

## O que está incluído 
{: #whats_included}

Cada imagem **ibm-backup-restore** contém os seguintes pacotes de software:

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   pacotes python, gnupg e wget

## Iniciar 
{: #how_to_get_started}

Revise as tarefas a seguir para fazer backup e restaurar dados:
1.  [Criando uma instância de serviço {{site.data.keyword.objectstorageshort}}](#object_storage)
2.  [Executando um backup planejado](#scheduled_backup)
3.  [Executando o script de restauração](#restore_script_cli)
4.  [Criptografando seus backups](#encrypting_backups)
5.  [Referência de variável de ambiente](#reference_backup_restore)

## Criando uma instância de serviço do {{site.data.keyword.objectstorageshort}} 
{: #object_storage}

Crie uma instância do {{site.data.keyword.objectstorageshort}} para servir como repositório para seu backup de volume.

1.  Provisione sua instância do {{site.data.keyword.objectstorageshort}} na seção **Armazenamento** do catálogo do {{site.data.keyword.Bluemix_notm}}.
2.  Clique em {{site.data.keyword.objectstorageshort}}.
3.  Selecione {{site.data.keyword.objectstorageshort}} OpenStack Swift for {{site.data.keyword.Bluemix_notm}}. Em seguida, clique em Criar.
3.  Clique na guia **Credenciais de serviço**.
4.  Clique em **Nova credencial**.
5.  Preencha o campo de nome, mas deixe os outros campos em branco. Clique em **Incluir**.
6.  Suas novas credenciais agora estão listadas na tabela **Credenciais de serviço**. Clique em **Visualizar credenciais**.
7.  Anote **projectid**, **region**, **userId** e **password**. Essas credenciais permitem que o contêiner de **ibm-backup-restauração** acesse a instância do {{site.data.keyword.objectstorageshort}}.

Revise a documentação do [{{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) para obter mais informações sobre como configurar a instância e revise as [opções de plano](../../ObjectStorage/objectstorage_faq.html#account-payment) para obter informações sobre precificação do {{site.data.keyword.objectstorageshort}}.

## Executando um backup planejado 
{: #scheduled_backup}

Crie um pod de contêiner da imagem **ibm-backup-restore** e inicie um backup planejado regularmente.

Antes de começar:

-   Instale as [CLIs](../../../containers/cs_cli_install.html#cs_cli_install) necessárias.
-   [Destine sua CLI](../../../containers/cs_cli_install.html#cs_cli_configure) para seu cluster.


1. Crie um arquivo de configuração denominado _backup-pvc.yaml_. Esse arquivo de configuração cria um persistent volume claim (pvc) que é possível montar em seu pod de backup como um volume.

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
    
2. Crie o pvc.

    ```
    kubectl apply -f backup-pvc.yaml 
    ```
    {: pre}

3.  Crie um arquivo de configuração denominado _backup.yaml_. Para as variáveis de ambiente em branco, insira os valores das credenciais do {{site.data.keyword.objectstorageshort}} que você anotou anteriormente. Inclua as aspas usadas nas credenciais. Substitua <em>&lt;pod_name&gt;</em>, <em>&lt;container_name&gt;</em> e o registro de imagem <em>&lt;region&gt;</em> pelos valores apropriados.

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
    
    Essas configurações criam um backup incremental diário com o nome padrão _mybackup_. Para criar um backup com configurações diferentes, revise uma lista completa de [opções de variável de ambiente](#reference_backup_restore).

4.  Crie um pod que execute o script de backup.

    ```
    kubectl apply -f backup.yaml
    ```
    {: pre}

5.  Confirme se o pod está em execução.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

4.  Revise o backup no {{site.data.keyword.objectstorageshort}} na GUI do {{site.data.keyword.Bluemix_notm}}.
    1.  Clique na instância do {{site.data.keyword.objectstorageshort}} que você criou para o backup.
    2.  Na guia **Gerenciar** na tabela **Contêineres**, clique no contêiner do {{site.data.keyword.objectstorageshort}}.
    3.  Revise os arquivos compactados.![O contêiner do Object Storage na GUI do {{site.data.keyword.Bluemix_notm}} mostra os arquivos que submetidos a backup.](images/volume_backup_screenshot.png)É possível fazer download do arquivo vol1.difftar.gz, extrair o arquivo e verificar os dados de backup. **Importante**: se você excluir ou modificar quaisquer arquivos do {{site.data.keyword.objectstorageshort}}, esses arquivos não poderão ser recuperados.

Seu backup está disponível. Se você configurou seu backup para criar um backup completo único, deve-se executar o script de backup toda vez que deseja criar um novo backup. Se você configurou o contêiner para executar um backup incremental periodicamente, o backup será executado como planejado.

## Executando o script de restauração 
{: #restore_script_cli}

Restaure seu backup do {{site.data.keyword.objectstorageshort}} para um volume novo ou existente.

Antes de começar:

-   Instale as [CLIs](../../../containers/cs_cli_install.html#cs_cli_install) necessárias.
-   [Destine sua CLI](../../../containers/cs_cli_install.html#cs_cli_configure) para seu cluster.


1. Crie um arquivo de configuração denominado _restore-pvc.yaml_. Esse arquivo de configuração cria um persistent volume claim (pvc) que é possível montar em seu pod de restauração como um volume.

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
    
2. Crie o pvc.

    ```
    kubectl apply -f restore-pvc.yaml 
    ```
    {: pre}

3.  Crie um arquivo de configuração denominado _restore.yaml_. Para as variáveis de ambiente em branco, insira os valores das credenciais do {{site.data.keyword.objectstorageshort}} que você anotou anteriormente. Inclua as aspas usadas nas credenciais. O BACKUP_NAME deve corresponder ao nome do backup no {{site.data.keyword.objectstorageshort}} que está sendo restaurado.

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

4.  Crie um pod que execute o script de restauração.
    
    ```
    kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  Confirme se o pod está em execução.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    O pod executa o comando de restauração e é parado. A mensagem _CrashLoopBackOff_ significa que o Kubernetes está tentando reiniciar o pod.

6.  Remova o pod para evitar que o pod consuma mais recursos.

    ```
    kubectl delete -f restore.yaml
    ```
    {: pre}

Você restaurou com êxito o backup. Agora é possível montar qualquer novo pod no pvc para conceder a esse contêiner acesso aos arquivos restaurados. Se os dados do contêiner cujo backup foi feito incluíam um usuário não raiz, deve-se incluir permissões não raiz no novo contêiner. Para obter mais informações, veja [Incluindo acesso de usuário não raiz para os volumes](../../../containers/container_volumes_ov.html#container_volumes_write).

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
    
6. Crie um arquivo de configuração denominado _backup-pvc.yaml_. Esse arquivo de configuração cria um persistent volume claim (pvc) que é possível montar em seu pod de backup como um volume.

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

7. Crie o pvc.

    ```
    kubectl apply -f backup-pvc.yaml 
    ```
    {: pre}

8.  Edite o arquivo de configuração de implementação e inclua os campos a seguir. Para as variáveis de ambiente em branco, insira os valores das credenciais do {{site.data.keyword.objectstorageshort}} que você anotou anteriormente. Inclua as aspas usadas nas credenciais. Parar *ENCRYPTION_PASSPHRASE*, inclua uma passphrase para proteger o backup com senha. Essa passphrase é diferente da frase que você criou quando criou a chave de criptografia. Deve-se incluir essa passphrase ao fazer backup dos dados e restaurá-los.

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
           # Inclua essa passphrase quando estiver fazendo backup e restaurando dados criptografados.
            value: 
          volumeMounts:
          - mountPath: /backup
            name: backup-volume
      volumes:
      - name: backup-volume 
        persistentVolumeClaim:
          claimName: backuppvc
   ```
   
   Essas configurações criam um backup incremental diário criptografado com o nome <em>&lt;volume_name&gt;</em>. Para criar um backup com configurações diferentes, revise uma lista integral de [opções de variável de ambiente](#reference_backup_restore).
9. Crie um pod que execute o script de backup.

    ```
    kubectl apply -f backup.yaml 
    ```
    {: pre}

9. Confirme se o pod está em execução.

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

10.  Copie a chave de criptografia no diretório /backup_restore do contêiner que é construído da imagem **ibm-backup-restore**.

    ```
    kubectl cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    Mantenha uma cópia da chave de criptografia localmente para decriptografar seus dados.

11. Efetue login no contêiner.

    ```
    kubecl exec -it <container_name> bash
    ```
    {: pre}

12. Confirme se *encryption.asc* foi copiado para a pasta backup_restore.

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

13. Execute o script de backup na pasta backup_restore.

    ```
    ./vbackup &
    ```
    {: codeblock}

13. Para confirmar se seu backup é criptografado, revise os arquivos em sua instância do {{site.data.keyword.objectstorageshort}}. Os arquivos agora têm `.gpg` anexado ao final do nome do arquivo![A GUI do Object Storage mostra todos os arquivos de backup anexados com .gpg, mostrando que eles estão criptografados.](images/volume_backup_encrypt_screenshot.png)

Seu backup está criptografado. Para restaurar os arquivos, siga as etapas em [Executando o script de restauração](#restore_script_cli) e inclua o arquivo encryption.asc no diretório backup_restore no contêiner que está executando o processo de restauração. Se o backup está criptografado, deve-se fornecer o ENCRYPTION_REQUIRED e o ENCRYPTION_PASSPHRASE do arquivo yaml de backup como variáveis de ambiente ao criar o contêiner de restauração.

## Referência de variável de ambiente
{: #reference_backup_restore}

Revise a lista integral de campos que podem ser passados como variáveis de ambiente ou editados no arquivo `config.conf` em um contêiner em execução. Qualquer valor que é passado como variável de ambiente suplanta o valor no arquivo `config.conf`. Para revisar as variáveis de ambiente para um contêiner, efetue login no contêiner usando `exec` e execute `env`.

|Chave|Opções de valor|
|---|-------------|
|PROJECTID|ID do projeto de {{site.data.keyword.objectstorageshort}}|
|REGION|Região de {{site.data.keyword.objectstorageshort}}|
|USERID|ID do usuário de {{site.data.keyword.objectstorageshort}}|
|PASSWORD|Senha de {{site.data.keyword.objectstorageshort}}|
{: caption="Tabela 1. {{site.data.keyword.objectstorageshort}} variables" caption-side="top"}

Recupere essas variáveis das credenciais do {{site.data.keyword.objectstorageshort}}. Inclui essas variáveis de ambiente com cada contêiner que está executando uma operação de backup ou restauração.

|Chave|Opções de valor|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: padrão. Caminho de arquivo absoluto do diretório no qual o volume é montado. O backup dos dados é feito a partir desse diretório. Não selecione o diretório backup_restore porque ele contém arquivos para os processos de backup e restauração.|
|BACKUP_NAME|*volume_backup*: padrão. Escolha um nome de backup.|
|BACKUP_TYPE|*full*: padrão. É feito backup de todos os arquivos toda vez.<br/> *incremental*: somente arquivos novos ou mudados são submetidos a backup. Se você escolhe *incremental*, deve-se escolher valores para SCHEDULING_INFO e SCHEDULING_TYPE.|
|SCHEDULE_TYPE|*none*: padrão. Crie um backup único.<br/> *periodic*: mude o valor para periodic para criar backups planejados.|
|SCHEDULE_INFO|*hourly*: crie um backup a cada hora.<br/>*daily*: padrão. Crie um backup diário.<br/>*weekly*: crie um backup semanal. Deve-se incluir essa variável se você está planejando uma atualização periódica.|
|EXCLUDE_DIRECTORIES|*none*: padrão. Inclua o caminho de arquivo absoluto dos diretórios que você deseja excluir do backup. Separe os diretórios com uma vírgula.|
{: caption="Tabela 2. Variáveis de backup" caption-side="top"}

|Chave|Opções de valor|
|---|-------------|
|BACKUP_NAME|*volume_backup*: padrão. Inclua o nome do backup que está sendo restaurado do {{site.data.keyword.objectstorageshort}}.|
|RESTORE_DIRECTORY|*/backup*: padrão. Diretório absoluto no qual o volume é montado. Os dados são restaurados para esse diretório. Não selecione o diretório backup_restore porque ele contém arquivos para os processos de backup e restauração.|
{: caption="Tabela 3. Variáveis de restauração" caption-side="top"}

|Chave|Opções de valor|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: padrão. Inclua essa variável de ambiente se você mudar o nome do arquivo da chave de criptografia ou a chave estiver localizada em um diretório diferente de backup_restore.|
|ENCRYPTION_REQUIRED|*no*: padrão.<br/> *yes*: se você não estiver criptografando seu backup, não inclua nenhuma variável de ambiente de criptografia. Se você estiver criptografando seu backup, inclua essa chave com o valor yes.|
|ENCRYPTION_PASSPHRASE|Inclua uma passphrase para assegurar um backup. Essa passphrase é diferente da frase que você criou quando criou a chave de criptografia. Deve-se incluir essa passphrase quando você estiver fazendo backup dos dados e restaurando os dados.|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: padrão.<br/> *yes*: inclua essa variável de ambiente com yes se você gerou a chave de criptografia diretamente no contêiner. A maioria dos usuários gera a chave em seu computador local e copia a chave para o contêiner e pode deixar o padrão como no.|
{: caption="Tabela 4. Variáveis de criptografia" caption-side="top"}

## Migrando seus dados de volume de contêineres únicos e escaláveis para o Kubernetes
{: #migrate_data}

Crie um backup único para qualquer volume de contêiner. O backup é armazenado em uma instância do {{site.data.keyword.objectstoragefull}}. Em seguida, é possível migrar os dados para um persistent volume claim no Kubernetes.
{:shortdesc}

### Introdução
{: #how_to_get_started}

Antes de iniciar:

- [Revise o caminho completo de migração para mover apps para o Kubernetes](../../../containers/cs_classic.html)
- [Instale a CLI de contêineres únicos e escaláveis (bx ic)](../../../containers/container_cli_cfic_install.html)
- [Instale a CLI do {{site.data.keyword.containershort}} (bx cs e kubectl)](../../../containers/cs_cli_install.html#cs_cli_install)
- [Crie um cluster do Kubernetes padrão para migrar seus dados para](../../../containers/cs_cluster.html#cs_cluster_cli)

Conclua as tarefas a seguir para executar as operações de backup e restauração:
1.  [Criando uma instância de serviço do {{site.data.keyword.objectstorageshort}}](#object_storage) (conforme abordado anteriormente)
2.  [Executando um backup único](#migrate_backup)
3.  [Executando o script de restauração no Kubernetes](#migrate_restore)

### Executando um backup único
{: #migrate_backup}

Crie um contêiner único da imagem **ibm-backup-restore** e inicie um backup.

1.  Efetue login na CLI do {{site.data.keyword.containershort}}.

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

3.  Edite o arquivo de variável de ambiente e inclua os campos a seguir. Para as variáveis de ambiente em branco, insira os valores das credenciais do {{site.data.keyword.objectstorageshort}} que você anotou anteriormente. **Não** inclua as aspas usadas nas credenciais.

    ```
    BACKUP_NAME=volume_backup
    BACKUP_DIRECTORY=/backup
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    ```
    {: codeblock}

    Essas configurações criam um backup único com o nome padrão _volume_backup_.

4.  Execute um contêiner da imagem **ibm-backup-restore** com um volume montado para fazer backup. Inclua o comando para iniciar o script de backup.

    -  Assegure-se de que você esteja no mesmo diretório local que o <em>&lt;backup_env-file&gt;</em>.
    -  O diretório no qual o volume é montado deve corresponder ao BACKUP_DIRECTORY no arquivo de variável de ambiente.
    -  Substitua <em>&lt;volume_name&gt;</em> pelo nome do volume que está sendo submetido a backup.

    ```
    bx ic run --name <container_name> --volume <volume_name>:/backup --env-file ./<backup_env-file_name> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    Após o backup ser executado, o contêiner é encerrado. Se a execução do contêiner não for iniciada, execute `bx ic logs <container_name>` para revisar os logs para mensagens de erro.

5.  Revise o backup no {{site.data.keyword.objectstorageshort}} na GUI do {{site.data.keyword.Bluemix_notm}}.
    1.  Clique na instância do {{site.data.keyword.objectstorageshort}} que você criou para o backup.
    2.  Clique no contêiner do {{site.data.keyword.objectstorageshort}}. Neste exemplo, o nome do contêiner é volume_backup.
    3.  Revise os arquivos compactados.![O contêiner do Object Storage na GUI do {{site.data.keyword.Bluemix_notm}} mostra os arquivos que são submetidos a backup.](images/volume_backup_screenshot.png)É possível fazer download do arquivo difftar.gz, extrair o arquivo e verificar os dados de backup. **Importante**: se você excluir ou modificar quaisquer arquivos do {{site.data.keyword.objectstorageshort}}, esses arquivos não poderão ser recuperados.

### Restaurando seus dados para um cluster do Kubernetes
{: #migrate_restore}

Restaure seu backup do {{site.data.keyword.objectstorageshort}} para um persistent volume claim em um cluster do Kubernetes.

Antes de começar:

- [Direcione sua CLI](../../../containers/cs_cli_install.html#cs_cli_configure) para o cluster.


1. Crie um arquivo de configuração denominado _restore-pvc.yaml_. Esse arquivo de configuração cria um persistent volume claim (pvc) que é possível montar em seu pod de backup como um volume.

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

2. Crie o pvc.

    ```
    kubectl apply -f restore-pvc.yaml 
    ```
    {: pre}

3.  Crie um arquivo de configuração denominado _restore.yaml_. Para as variáveis de ambiente em branco, insira os valores das credenciais do {{site.data.keyword.objectstorageshort}} que você anotou anteriormente. Inclua as aspas usadas nas credenciais. O BACKUP_NAME deve corresponder ao nome do backup no {{site.data.keyword.objectstorageshort}} que está sendo restaurado.

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

4.  Crie um pod que execute o script de restauração.

    ```
    kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  Confirme se o pod é executado.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}
    
    O pod executa o comando de restauração e é parado. A mensagem _CrashLoopBackOff_ significa que o Kubernetes está tentando reiniciar o pod.

6.  Remova o pod para evitar que o pod consuma mais recursos.

    ```
    kubectl delete -f restore.yaml
    ```
    {: pre}

Você migrou com êxito seus dados para o cluster do Kubernetes. Agora é possível montar qualquer novo pod no pvc para conceder a esse pod acesso aos arquivos restaurados.

**Nota**: se os dados do contêiner que foram submetidos a backup incluíam um usuário não raiz, deve-se incluir permissões não raiz no novo contêiner. Para obter mais informações, veja [Incluindo acesso de usuário não raiz para volumes](../../../containers/container_volumes_ov.html#container_volumes_write).
