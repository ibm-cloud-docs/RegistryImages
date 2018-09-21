---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-21"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip} 
{:table: .aria-labeledby="caption"}

# Introdução à imagem `ibm-backup-restore`
{: #ibmbackup_restore_starter}

A imagem `ibm-backup-restore` contém os pacotes pré-instalados que são necessários para fazer backup e restaurar armazenamento persistente no {{site.data.keyword.containerlong}}.
{:shortdesc}

## Como ele Funciona 
{: #how_it_works}

Com a imagem `ibm-backup-restore`, é possível criar um backup único ou planejado para dados do app que são armazenados em um volume persistente (PV) em seu cluster ou restaurar dados do app para um PV. Para fazer backup e restaurar dados, implemente um pod por meio da imagem `ibm-backup-restore`. Em seguida, monte o PVC que liga o PV do qual você deseja fazer backup ou o PV que deseja usar para restaurar os dados para seu pod. 

** Onde meus dados vão? Como posso acessá-los?** 

Os dados dos quais você faz backup são armazenados em uma instância de serviço do {{site.data.keyword.cos_full_notm}}. Para acessar o serviço, use as credenciais de serviço do {{site.data.keyword.cos_full_notm}} como variáveis de ambiente no pod `ibm-backup-restore` ou edite o arquivo `config.conf` no pod em execução.

**Posso restaurar dados submetidos a backup para um app ou um PV diferente?** 

Sim, é possível restaurar os dados salvos da instância de serviço do {{site.data.keyword.cos_full_notm}} para um PV em seu cluster. Para restaurar dados, deve-se criar um pod de restauração por meio da imagem `ibm-backup-restore`. Em seguida, você monta o PVC que liga o PV que deseja usar para o pod.  

## O que está incluído 
{: #whats_included}

Cada imagem `ibm-backup-restore` contém os seguintes pacotes de software:

-   Alpino 3.7
-   Duplicity 0.7.10
-   pacotes python e gpgme


## Configurando uma instância de serviço do  {{site.data.keyword.cos_full_notm}} 
{: #object_storage}

Crie e configure uma instância de serviço do {{site.data.keyword.cos_full_notm}} para servir como o repositório para os dados dos quais deseja fazer backup.
{: shortdesc}

1. Implemente uma instância de serviço do  {{site.data.keyword.cos_full_notm}} .
   1.  Abra o  [ {{site.data.keyword.Bluemix_notm}}  catálogo ](https://console.bluemix.net/catalog/services/cloud-object-storage).
   2.  Insira um nome para a instância de serviço, como `cos-backup` e selecione **padrão** como seu grupo de recursos. 
   3.  Revise as [opções de plano ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://www.ibm.com/cloud-computing/bluemix/pricing-object-storage#s3api) para obter informações de precificação e selecionar um plano. 
   4.  Clique em **Criar**.
2. Recupere as credenciais da instância de serviço do {{site.data.keyword.cos_full_notm}}.
   1.  Na navegação na página de detalhes do serviço, clique em **Credenciais de serviço**.
   2.  Clique em  ** Nova credencial **. Uma caixa de diálogo é exibida. 
   3.  Insira um nome para suas credenciais.
   4.  Em **Incluir parâmetros de configuração sequencial (Opcional)**, insira `{"HMAC":true}` para criar credenciais HMAC adicionais que o pod `ibm-backup-restore` usará para autenticação do HMAC com o serviço {{site.data.keyword.cos_full_notm}}. 
   5.  Clique em **Incluir**. Suas novas credenciais são listadas na tabela **Credenciais de serviço**.
   6.  Clique em  ** Visualizar credenciais **. 
   7.  Anote o **access_key_id** e a **secret_access_key** que podem ser localizados na seção **cos_hmac_keys**. 
3. Crie seu primeiro depósito  {{site.data.keyword.cos_full_notm}} .
   1. Na navegação na página de detalhes do serviço, clique em **Depósitos**. 
   2. Clique em  ** Criar depósito **. Uma caixa de diálogo é exibida.
   3. Insira um nome exclusivo para seu depósito. O nome deve ser exclusivo no {{site.data.keyword.cos_full_notm}} em todas as regiões e em todas as contas do {{site.data.keyword.Bluemix_notm}}. 
   4. Na lista suspensa **Resiliência**, selecione o nível de disponibilidade que deseja para seus dados. Para obter mais informações, consulte [Regiões e terminais do {{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage/basics/endpoints.html#select-regions-and-endpoints). 
   5. Mude o **Local** para a região na qual deseja armazenar os dados. Lembre-se de que seus dados podem não ter permissão para serem armazenados em todas as regiões devido a razões legais.  
   6. Clique em **Criar**. 
4. Recupere o nome de host do {{site.data.keyword.cos_full_notm}} para o depósito. 
   1. Clique no nome do depósito criado na etapa anterior. 
   2. Na navegação na página de detalhes do serviço, clique em **Depósitos** > **Configuração**. 
   3. Anote a URL pública que pode ser usada para acessar os dados no depósito. 


Revise a documentação do [{{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage/about-cos.html#about-ibm-cloud-object-storage) para obter mais informações sobre como configurar sua instância de serviço.


## Fazendo backup de dados de um volume persistente
{: #scheduled_backup}

É possível criar um backup único ou planejado para qualquer volume persistente (PV) montado no pod de seu app por meio de uma solicitação de volume persistente (PVC).  
{: shortdesc}

O exemplo a seguir fornece orientação sobre como implementar um pod de backup por meio da imagem `ibm-backup-restore`, montar um PV existente no pod de backup usando um PVC e fazer backup dos dados do PV na instância de serviço do {{site.data.keyword.cos_full_notm}}.  

**Antes de começar**

-   [ Configure uma  {{site.data.keyword.cos_full_notm}}  instância de serviço ](#object_storage). 
-   Instale as [CLIs](/docs/containers/cs_cli_install.html#cs_cli_install) necessárias para criar e trabalhar com seu cluster.
-   [Crie um cluster padrão](/docs/containers/cs_clusters.html#clusters_cli) ou use um existente.
-   [Destine sua CLI para seu cluster](/docs/containers/cs_cli_install.html#cs_cli_configure).
-   Crie uma solicitação de volume persistente (PVC) para seu [armazenamento de arquivo](/docs/containers/cs_storage_file.html#add_file) ou seu [armazenamento de bloco](/docs/containers/cs_storage_block.html#add_block) e monte-a em sua implementação de app.

Para fazer backup de um PV existente, conclua as etapas a seguir: 

1. Obtenha o nome do PVC que liga o PV do qual você deseja fazer backup.

   ```
   kubectl get pvc
   ```
   {: pre}

2. Crie um pod de backup por meio da imagem `ibm-backup-restore`. Para acessar os dados no PV, deve-se montar o PVC que liga o PV ao pod de backup. O exemplo a seguir cria um pod de backup que executa um backup incremental diário. Para criar um backup com configurações diferentes, revise uma lista completa de [opções de variável de ambiente](#reference_backup_restore).

   **Importante**: a imagem `ibm-backup-restore` deve ser implementada em um único pod e não pode ser usada como parte de uma implementação do Kubernetes.
   
   Para visualizar a imagem, direcione o registro global executando o comando `ibmcloud cr region-set global`. Em seguida, execute `ibmcloud cr images --include-ibm` para listar as imagens públicas da IBM. 
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
   <caption>Componentes de arquivo YAML</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Ícone de ideia"/> Entendendo os componentes do arquivo yaml</th>
   </thead>
    <tbody>
     <tr>
     <td><code> &lt;access_key_ID&gt; </code></td>
     <td>O ID da chave de acesso recuperado como parte de suas credenciais da instância de serviço do {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code> &lt;secret_access_key&gt; </code></td>
     <td>A chave de acesso secreta recuperada como parte de suas credenciais da instância de serviço do {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code> &lt;regional_endpoint&gt; </code></td>
     <td>A URL para o terminal de API regional usada para acessar o {{site.data.keyword.cos_full_notm}} em uma região específica. </td>
     </tr>
     <tr>
     <td><code> &lt;bucket_name&gt; </code></td>
     <td>O nome do depósito que você deseja usar para armazenar seus backups no {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code> &lt;backup_name&gt; </code></td>
     <td>Um nome exclusivo para o objeto que contém seu backup no depósito </td>
     </tr>
     <tr>
     <td><code> &lt;pvc_name&gt; </code></td>
     <td>O nome do PVC que liga o PV do qual você deseja fazer backup. </td>
     </tr>
     </tbody>
     </table>
    
3.  Crie o pod de backup e inicie um backup de seus dados do PV. 

    ```
    kubectl aplicar -f backuppod.yaml
    ```
    {: pre}

4.  Verifique se o pod está em execução.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}
    
5.  Verifique se o backup foi executado com sucesso. 

    ```
    logs kubectl logs backuppod
    ```
    {: pre}

6.  Revise o backup no {{site.data.keyword.cos_full_notm}} na GUI do {{site.data.keyword.Bluemix_notm}}.
    1.  No painel do {{site.data.keyword.Bluemix_notm}}, localize a instância de serviço do {{site.data.keyword.cos_full_notm}}. 
    2.  Na navegação, selecione **Depósitos** e clique no depósito usado em sua configuração de backup. O backup é exibido como um objeto em seu depósito. 
    3.  Revise os arquivos compactados. É possível fazer download do arquivo `vol1.difftar.gz`, extrair o arquivo e verificar os dados submetidos a backup. 
        
        **Importante**: se você excluir ou modificar algum arquivo do {{site.data.keyword.cos_full_notm}}, esses arquivos não poderão ser recuperados.

Seu backup está disponível. Se você configurou seu backup para criar um backup completo único, deve-se executar o script de backup toda vez que deseja criar um novo backup. Se você configurou o contêiner para executar um backup incremental periodicamente, o backup será executado como planejado.

## Restaurando Dados do  {{site.data.keyword.cos_full_notm}}  para seu Cluster
{: #restore_script_cli}

É possível restaurar dados de sua instância de serviço do {{site.data.keyword.cos_full_notm}} para um PV em seu cluster. 

**Antes de começar**

-   [Destine sua CLI para seu cluster](/docs/containers/cs_cli_install.html#cs_cli_configure).
-   [Crie um backup para um PV em seu cluster](#scheduled_backup).

Para restaurar os dados do {{site.data.keyword.cos_full_notm}} para um PV, conclua as etapas a seguir: 

1. Obtenha o nome do PVC que liga o PV no qual você deseja restaurar seus dados. 

   ```
   kubectl get pvc
   ```
   {: pre}

2. Crie um pod de restauração por meio da imagem `ibm-backup-restore`. Para restaurar dados para um PV, deve-se montar o PVC que liga o PV ao pod de restauração. 
   
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
   <caption>Componentes de arquivo YAML</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Ícone de ideia"/> Entendendo os componentes do arquivo yaml</th>
   </thead>
    <tbody>
     <tr>
     <td><code> &lt;access_key_ID&gt; </code></td>
     <td>O ID da chave de acesso recuperado como parte de suas credenciais da instância de serviço do {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code> &lt;secret_access_key&gt; </code></td>
     <td>A chave de acesso secreta recuperada como parte de suas credenciais da instância de serviço do {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code> &lt;regional_endpoint&gt; </code></td>
     <td>A URL para o terminal de API regional usada para acessar o {{site.data.keyword.cos_full_notm}} em uma região específica. </td>
     </tr>
     <tr>
     <td><code> &lt;bucket_name&gt; </code></td>
     <td>O nome do depósito usado para armazenar backups no {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code> &lt;backup_name&gt; </code></td>
     <td>O nome exclusivo para o objeto que contém seu backup no depósito. Deve-se usar o nome usado no pod de backup para armazenar seus dados no {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code> &lt;pvc_name&gt; </code></td>
     <td>O nome do PVC que liga o PV no qual você deseja restaurar os dados. </td>
     </tr>
     </tbody>
     </table>

3.  Crie o pod de restauração e inicie a restauração dos dados.

    ```
    kubectl aplicar -f restorepod.yaml
    ```
    {: pre}
    
4.  Verifique se o pod está em execução.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    restorepod        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    O pod executa o comando de restauração e é parado. A mensagem _CrashLoopBackOff_ significa que o Kubernetes está tentando reiniciar o pod.

5.  Remova o pod para evitar que ele consuma mais recursos.

    ```
    kubectl delete -f restorepod.yaml
    ```
    {: pre}

6.  Verifique se seus dados foram restaurados com sucesso.

    ```
    logs kubectl logs restorepod
    ```
    {: pre}

Você restaurou com êxito o backup. Agora é possível montar o PVC que liga o PV a qualquer outro pod no cluster para acessar os arquivos restaurados. Se os dados do contêiner cujo backup foi feito incluíam um usuário não raiz, deve-se incluir permissões não raiz no novo contêiner. Para obter mais informações, veja [Incluindo acesso de usuário não raiz para os volumes](/docs/containers/cs_troubleshoot_storage.html#cs_storage_nonroot).


## Criptografando seus backups 
{: #encrypting_backups}

Criptografe os dados em sua instância do {{site.data.keyword.cos_full_notm}}.

1.  Faça download do <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Ícone de link externo"></a> para criar uma chave de criptografia.
2.  Crie uma chave de criptografia em sua unidade local. É possível aceitar os valores padrão pressionando ENTER.

    **Importante:** anote a passphrase criada. Se você perder sua passphrase, qualquer informação criptografada com sua chave não poderá ser decriptografada.

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
    uid       [ultimate] Nome de exemplo (Esta é uma chave de exemplo) <example_email_address>
    sub   2048R/YYYYYYYY 2016-10-27
    ```
    {: screen}

4.  Exporte a chave de criptografia com o valor da chave `sub`. Nomeie
o arquivo `encryption.asc`.

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    Neste exemplo, a subchave tem o valor `YYYYYYYY`.

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  Crie um arquivo de variável de ambiente para seu contêiner de backup criptografado em um diretório local.

    ```
    touch <encryption_env-file_name>
    ```
    {: pre}

6.  Edite o arquivo de configuração do pod e inclua os campos a seguir. Para as variáveis de ambiente em branco, insira os valores das credenciais do {{site.data.keyword.cos_full_notm}} que você anotou anteriormente. Inclua as aspas usadas nas credenciais. Parar **ENCRYPTION_PASSPHRASE**, inclua uma passphrase para proteger o backup com senha. Essa passphrase é diferente da frase que você criou quando criou a chave de criptografia. Deve-se incluir essa passphrase ao fazer backup dos dados e restaurá-los.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: backuppod
    spec:
      containers:
      - imagem: registry.bluemix.net/ibm-backup-restore
        nome: backupcontainer
        ambiente:
        - nome: OBJECTSTORAGE
          valor: S3
        - nome: ACCESS_KEY_ID
          valor: '<access_key_id>'
        - nome: SECRET_ACCESS_KEY
          valor: '<secret_access_key>'
        - nome: ENDPOINT
          valor: '<regional_endpoint>'
        - nome: BUCKET_NAME
          valor: '<bucket_name>'
        - nome: BACKUP_DIRECTORY  
          valor: /myvol
        - nome: BACKUP_NAME
          valor: <backup_name> 
        - nome: SCHEDULE_TYPE
          valor: periodic
        - nome: SCHEDULE_INFO
          valor: daily
        - nome: BACKUP_TYPE
          valor: incremental
        - nome: ENCRYPTION_REQUIRED
          valor: yes
        - nome: ENCRYPTION_PASSPHRASE
          valor: <passphrase>
        volumeMounts:
        - mountPath: /myvol
          nome: volume de backup
      volumes:
      - nome: volume de backup
        persistentVolumeClaim:
          claimName: <pvc_name>  
    ```
    {: codeblock}
   
    <table>
    <caption>Componentes de arquivo YAML</caption>
    <thead>
    <th colspan=2><img src="../images/idea.png" alt="Ícone de ideia"/> Entendendo os componentes do arquivo yaml</th>
    </thead>
     <tbody>
     <tr>
     <td><code> &lt;access_key_ID&gt; </code></td>
     <td>O ID da chave de acesso recuperado como parte de suas credenciais da instância de serviço do {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code> &lt;secret_access_key&gt; </em> </code></td>
     <td>A chave de acesso secreta recuperada como parte de suas credenciais da instância de serviço do {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code> &lt;regional_endpoint&gt; </code></td>
     <td>A URL para o terminal de API regional usada para acessar o {{site.data.keyword.cos_full_notm}} em uma região específica. </td>
     </tr>
     <tr>
     <td><code> &lt;bucket_name&gt; </code></td>
     <td>O nome do depósito que você deseja usar para armazenar seus backups no {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code> &lt;backup_name&gt; </code></td>
     <td>Um nome exclusivo para o objeto que contém seu backup no depósito.</td>
     </tr>
     <tr>
     <td><code> &lt;passphrase&gt; </code></td>
     <td>Qualquer sequência que você deseja usar para o backup. Deve-se incluir essa passphrase ao restaurar os dados. </td>
     </tr>
     <tr>
     <td><code> &lt;pvc_name&gt; </code></td>
     <td>O nome do PVC que liga o PV do qual você deseja fazer backup. </td>
     </tr>
     </tbody>
     </table>
   
    Essas configurações criam um backup incremental diário que é criptografado. Para criar um backup com configurações diferentes, revise uma lista completa de [opções de variável de ambiente](#reference_backup_restore).
    
7.  Crie o pod de backup. 

    ```
    kubectl aplicar -f backuppod.yaml 
    ```
    {: pre}

8.  Verifique se o pod está em execução.

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

9.  Copie a chave de criptografia no diretório `/backup_restore` do
contêiner que é construído a partir da imagem `ibm-backup-restore`.

    ```
    kubectl cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    Mantenha uma cópia da chave de criptografia localmente para decriptografar seus dados.

10. Efetue login no pod e navegue para a pasta `backup_restore`. 

    ```
    kubecl exec -it < pod_name > bash
    ```
    {: pre}

11. Verifique se o arquivo `encryption.asc` foi copiado para a pasta `backup_restore`.

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

13. Para confirmar se o backup está criptografado, revise os arquivos na instância de serviço do {{site.data.keyword.cos_full_notm}}. Os
arquivos agora têm `.gpg` anexado ao final do nome do arquivo.

Seu backup está criptografado. Para restaurar os arquivos, siga as etapas em [Restaurando dados do {{site.data.keyword.cos_full_notm}} para um PVC no cluster](#restore_script_cli) e inclua o arquivo `encryption.asc` no diretório `backup_restore` do pod que executa o processo de restauração. Quando o backup é criptografado, deve-se fornecer as variáveis de ambiente **ENCRYPTION_REQUIRED** e **ENCRYPTION_PASSPHRASE** ao criar o pod de restauração.


## Referência de variável de ambiente 
{: #reference_backup_restore}

Revise a lista completa de campos que podem ser passados como variáveis de ambiente ou editados no arquivo `config.conf` em um pod em execução. Qualquer valor que é passado como variável de ambiente suplanta o valor no arquivo `config.conf`. Para revisar as variáveis de ambiente de um pod, efetue login no pod usando o comando `kubectl exec` e execute `env`.

|Chave|Opções de valor|
|---|-------------|
|ACCESS_KEY_ID|O **access_key_id** que faz parte das credenciais HMAC no {{site.data.keyword.cos_full_notm}}.|
|SECRET_ACCESS_KEY|A **secret_access_key** que faz parte das credenciais HMAC no {{site.data.keyword.cos_full_notm}}.|
|TERRETAL|O nome do host para acessar dados do depósito do {{site.data.keyword.cos_full_notm}}.|
|BUCKET|O nome do depósito no {{site.data.keyword.cos_full_notm}} no qual os dados de backup são armazenados.|
{: caption="Tabela 1. Variáveis do {{site.data.keyword.cos_full_notm}}" caption-side="top"}

|Chave|Opções de valor|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: padrão. Caminho de arquivo absoluto do diretório no qual o volume é montado. O backup dos dados é feito a partir desse diretório. Não selecione o diretório backup_restore, pois ele contém arquivos para os processos de backup e restauração.|
|BACKUP_NAME|*volume_backup*: padrão. Escolha um nome de backup.|
|BACKUP_TYPE|*full*: padrão. É feito backup de todos os arquivos toda vez.<br/> *incremental*: é feito backup apenas de arquivos novos ou mudados. Se você escolher *incremental*, deverá
escolher valores para **SCHEDULING_INFO** e
**SCHEDULING_TYPE**.|
|SCHEDULE_TYPE|*none*: padrão. Crie um backup único.<br/> **Nota:** se você optar por criar um backup único, seu pod será removido do cluster após a conclusão do backup. <br/> *periodic*: mude o valor para periódico para criar backups planejados.|
|SCHEDULE_INFO|*hourly*: crie um backup a cada hora.<br/>*daily*: padrão. Crie um backup diário.<br/>*weekly*: crie um backup semanal. Deve-se incluir essa variável ao planejar uma atualização periódica.|
|EXCLUDE_DIRECTORIES|*none*: padrão. Inclua o caminho de arquivo absoluto dos diretórios que você deseja excluir do backup. Separe os diretórios com uma vírgula.|
{: caption="Tabela 2. Variáveis de backup" caption-side="top"}

|Chave|Opções de Valor|
|---|-------------|
|BACKUP_NAME|*volume_backup*: padrão. Inclua o nome do backup que está sendo restaurado do {{site.data.keyword.cos_full_notm}}.|
|RESTORE_DIRECTORY|*/backup*: padrão. Diretório absoluto no qual o volume é montado. Os dados são restaurados para esse diretório. Não selecione o diretório `backup_restore` visto que
esse diretório contém arquivos para os processos de backup e restauração.|
{: caption="Tabela 3. Variáveis de restauração" caption-side="top"}

|Chave|Opções de valor|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: padrão. Inclua essa variável de ambiente
se você mudar o nome do arquivo da chave de criptografia ou a chave estiver localizada em
um diretório diferente de `backup_restore`.|
|ENCRYPTION_REQUIRED|*no*: padrão.<br/> *yes*: se você não criptografar o backup, não inclua nenhuma variável de ambiente de criptografia. Se você criptografar o backup, inclua essa chave com o valor `yes`.|
|ENCRYPTION_PASSPHRASE|Inclua uma passphrase para proteger um backup. Essa passphrase é diferente da frase que você criou quando criou a chave de criptografia. Deve-se incluir essa passphrase ao fazer backup de dados e restaurar dados.|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: padrão.<br/> *yes*: inclua essa variável de ambiente com `yes` se você tiver gerado a chave de criptografia diretamente no contêiner. A maioria dos usuários gera a chave em seu computador local e copia a chave para o pod e pode deixar o padrão como `no`.|
{: caption="Tabela 4. Variáveis de criptografia" caption-side="top"}

