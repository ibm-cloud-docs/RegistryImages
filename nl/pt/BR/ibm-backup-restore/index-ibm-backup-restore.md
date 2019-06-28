---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-21"

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

# Introdução à imagem `ibmcloud-backup-restore`
{: #ibmbackup_restore_starter}

A imagem `ibmcloud-backup-restore` contém os pacotes pré-instalados que são necessários para fazer backup e restaurar o armazenamento persistente no {{site.data.keyword.containerlong}}.
{:shortdesc}

A imagem `ibmcloud-backup-restore` está disponível somente nos domínios de registro `icr.io` e foi descontinuada nos domínios `registry.bluemix.net`. Para acessar a imagem e implementar os contêineres por meio dela, seu cluster deve armazenar uma chave de API em um segredo de pull de imagem para autorizar o acesso ao {{site.data.keyword.registryshort_notm}}. Para clusters que foram criados antes de 25 de fevereiro de 2019, deve-se atualizar o cluster para ter acesso aos domínios de registro `icr.io` para que seja possível usar a imagem `ibmcloud-backup-restore`. Para obter mais informações sobre o que mudou e como atualizar seu cluster, consulte [Entendendo como autorizar seu cluster a fazer pull de imagens de um registro](/docs/containers?topic=containers-images#cluster_registry_auth) e [Atualizando clusters existentes para usar o segredo de pull de imagem da chave de API](/docs/containers?topic=containers-images#imagePullSecret_migrate_api_key). 
{: important}

É possível acessar as imagens que são fornecidas pela {{site.data.keyword.IBM_notm}} usando a linha de comandos. Consulte [Imagens públicas da IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Como ele Funciona
{: #backup_restore_how_it_works}

Com a imagem `ibmcloud-backup-restore`, é possível criar um backup único ou planejado para dados do app que são armazenados em um volume persistente (PV) em seu cluster ou restaurar dados do app para um PV. Para fazer backup e restaurar dados, implemente um pod por meio da imagem `ibmcloud-backup-restore`. Em seguida, você monta a PVC que liga o PV cujo backup você deseja fazer ou o PV que você deseja usar para restaurar os dados ao seu pod.

** Onde meus dados vão? Como posso acessá-los?**

Os dados que passaram por backup são armazenados em uma instância de serviço do {{site.data.keyword.cos_full_notm}}. Para acessar o serviço, use as credenciais de serviço do {{site.data.keyword.cos_full_notm}} como variáveis de ambiente no pod `ibmcloud-backup-restore` ou edite o arquivo `config.conf` no pod em execução.

**Posso restaurar dados submetidos a backup para um app ou um PV diferente?**

Sim, é possível restaurar os dados salvos da instância de serviço do {{site.data.keyword.cos_full_notm}} para um PV em seu cluster. Para restaurar dados, deve-se criar um pod de restauração por meio da imagem `ibmcloud-backup-restore`. Em seguida, você monta o PVC que liga o PV que deseja usar para o pod.  

## O que está incluído
{: #backup_restore_whats_included}

Cada imagem `ibmcloud-backup-restore` contém os pacotes de software a seguir:

- Alpino 3.7
- Duplicity 0.7.10
- Pacotes Python e GnuPG Made Easy (GPGme)

## Configurando uma instância de serviço do  {{site.data.keyword.cos_full_notm}}
{: #backup_restore_setup_object_storage}

Crie e configure uma instância de serviço do {{site.data.keyword.cos_full_notm}} para servir como o repositório para os dados dos quais deseja fazer backup.
{: shortdesc}

1. Implemente uma instância de serviço do  {{site.data.keyword.cos_full_notm}} .
   1. Abra o [catálogo do {{site.data.keyword.cloud_notm}} ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://cloud.ibm.com/catalog/services/cloud-object-storage).
   2. Insira um nome para a instância de serviço, como `cos-backup` e selecione **padrão** como seu grupo de recursos.
   3. Revise as [opções de plano ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://www.ibm.com/cloud-computing/bluemix/pricing-object-storage#s3api) para obter informações de precificação e selecionar um plano.
   4. Clique em **Criar**.
2. Recupere as credenciais da instância de serviço do {{site.data.keyword.cos_full_notm}}.
   1. Na navegação na página de detalhes do serviço, clique em **Credenciais de serviço**.
   2. Clique em  ** Nova credencial **. Uma caixa de diálogo é exibida.
   3. Insira um nome para suas credenciais.
   4. No campo **Incluir parâmetros de configuração sequencial (opcional)**, insira `{"HMAC":true}` para criar credenciais HMAC adicionais usadas pelo pod `ibmcloud-backup-restore` para a autenticação HMAC com o serviço {{site.data.keyword.cos_full_notm}}.
   5. Clique em **Incluir**. Suas novas credenciais são listadas na tabela **Credenciais de serviço**.
   6. Clique em  ** Visualizar credenciais **.
   7. Anote o **access_key_id** e a **secret_access_key** que podem ser localizados na seção **cos_hmac_keys**.
3. Crie seu primeiro depósito  {{site.data.keyword.cos_full_notm}} .
   1. Na navegação na página de detalhes do serviço, clique em **Depósitos**.
   2. Clique em  ** Criar depósito **. Uma caixa de diálogo é exibida.
   3. Insira um nome exclusivo para seu depósito. O nome deve ser exclusivo no {{site.data.keyword.cos_full_notm}} em todas as regiões e em todas as contas do {{site.data.keyword.cloud_notm}}.
   4. Na lista **Resiliência**, selecione o nível de disponibilidade que você deseja para seus dados. Para obter mais informações, consulte [Regiões e terminais do {{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-endpoints#endpoints).
   5. Mude o **Local** para a região na qual deseja armazenar os dados. Lembre-se de que seus dados podem não ter permissão para serem armazenados em todas as regiões devido a razões legais.  
   6. Clique em **Criar**.
4. Recupere o nome de host do {{site.data.keyword.cos_full_notm}} para o depósito.
   1. Clique no nome do depósito criado na etapa anterior.
   2. Na navegação na página de detalhes do serviço, clique em **Depósitos** > **Configuração**.
   3. Anote a URL pública que pode ser usada para acessar os dados no depósito.

Revise a documentação do [{{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage?topic=cloud-object-storage-about-ibm-cloud-object-storage) para obter mais informações sobre como configurar sua instância de serviço.

## Fazendo backup de dados de um volume persistente
{: #backup_restore_scheduled_backup}

É possível criar um backup único ou planejado para qualquer volume persistente (PV) montado no pod de seu app por meio de uma solicitação de volume persistente (PVC).  
{: shortdesc}

O exemplo a seguir mostra como implementar um pod de backup por meio da imagem `ibmcloud-backup-restore`, como montar um PV existente no pod de backup usando uma PVC e como fazer backup dos dados do PV para sua instância de serviço do {{site.data.keyword.cos_full_notm}}.  

**Antes de começar**

- [ Configure uma  {{site.data.keyword.cos_full_notm}}  instância de serviço ](#backup_restore_setup_object_storage).
- Instale as [CLIs](/docs/containers?topic=containers-cs_cli_install#cs_cli_install) necessárias para criar e trabalhar com seu cluster.
- [Crie um cluster padrão](/docs/containers?topic=containers-clusters#clusters_cli) ou use um existente.
- [Destine sua CLI para seu cluster](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure).
- Crie uma solicitação de volume persistente (PVC) para seu [armazenamento de arquivo](/docs/containers?topic=containers-file_storage#add_file) ou seu [armazenamento de bloco](/docs/containers?topic=containers-block_storage#add_block) e monte-a em sua implementação de app.

Para fazer backup de um PV existente, conclua as etapas a seguir:

1. Obtenha o nome do PVC que liga o PV do qual você deseja fazer backup.

   ```
   kubectl get pvc
   ```
   {: pre}

2. Crie um pod de backup por meio da imagem `ibmcloud-backup-restore`. Para acessar os dados no PV, deve-se montar o PVC que liga o PV ao pod de backup. O exemplo a seguir cria um pod de backup que executa um backup incremental diário. Para criar um backup com configurações diferentes, revise uma lista completa de [opções de variável de ambiente](#backup_restore_env_reference).

   A imagem `ibmcloud-backup-restore` deve ser implementada em um pod único e não pode ser usada como parte de uma implementação do Kubernetes.
   {: important}

   Para visualizar a imagem, direcione o registro global executando o comando `ibmcloud cr region-set global`. Em seguida, execute `ibmcloud cr images --include-ibm` para listar as imagens públicas da IBM.
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
       - mountPath: <mount_path1>
         name: <pvc_name1>
       - mountPath: <mount_path1>
         name: <pvc_name2>
     volumes:
     - name: pvc_name1
       persistentVolumeClaim:
         claimName: <pvc_name1>  
     - name: pvc_name2
       persistentVolumeClaim:
         claimName: <pvc_name2> 
   ```
   {: codeblock}

   <table>
   <caption>Tabela 1. Componentes de arquivo YAML</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Ícone de ideia"/> Entendendo os componentes do arquivo YAML</th>
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
     <td><code>&lt;mount_path&gt;</code></td>
     <td>O caminho de montagem do volume dentro do contêiner de backup que monta o PVC do qual você deseja fazer backup. Se você desejar fazer backup de múltiplos PVCs, especifique um caminho de montagem separado para cada PVC. Certifique-se de que o nome que você usa em <code>volumeMount.name</code> corresponda ao nome do volume especificado na seção <code>volumes.name</code> de seu arquivo YAML. </td>
     </tr>
     <tr>
     <td><code> &lt;pvc_name&gt; </code></td>
     <td>O nome do PVC do qual você deseja fazer backup. Se você desejar fazer backup de múltiplos PVCs, especifique o nome do volume e o PVC correspondente para cada PVC do qual deseja fazer backup. Certifique-se de que o nome que você usa em <code>volumes.name</code> corresponda ao nome na seção <code>volumeMount.name</code> de seu arquivo YAML.</td>
     </tr>
     </tbody>
     </table>

3. Crie o pod de backup e inicie um backup de seus dados do PV.

    ```
    kubectl aplicar -f backuppod.yaml
    ```
    {: pre}

4. Verifique se o pod está em execução.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

5. Verifique se o backup foi executado com sucesso.

    ```
    logs kubectl logs backuppod
    ```
    {: pre}

6. Revise o backup no {{site.data.keyword.cos_full_notm}} na GUI do {{site.data.keyword.cloud_notm}}.
    1. No painel do {{site.data.keyword.cloud_notm}}, localize a instância de serviço do {{site.data.keyword.cos_full_notm}}.
    2. Na navegação, selecione **Depósitos** e clique no depósito usado em sua configuração de backup. O backup é exibido como um objeto em seu depósito.
    3. Revise os arquivos compactados. É possível fazer download do arquivo `vol1.difftar.gz`, extrair o arquivo e verificar os dados submetidos a backup.

       Se você excluir ou modificar qualquer arquivo do {{site.data.keyword.cos_full_notm}}, ele não poderá ser recuperado.
       {: important}

Seu backup está disponível. Se você configurou seu backup para criar um backup completo único, deve-se executar o script de backup toda vez que deseja criar um novo backup. Se você configurou o contêiner para executar um backup incremental periodicamente, o backup será executado como planejado.

## Restaurando Dados do  {{site.data.keyword.cos_full_notm}}  para seu Cluster
{: #backup_restore_restore_script_cli}

É possível restaurar dados de sua instância de serviço do {{site.data.keyword.cos_full_notm}} para um PV em seu cluster.

Se você tiver diversos backups completos em sua instância de serviço do {{site.data.keyword.cos_full_notm}}, a PVC será restaurada com os dados do último backup completo. Se você tiver backups incrementais, a PVC será restaurada com os dados do último backup completo, incluindo todos os backups incrementais até o dia no qual a restauração será iniciada.

**Antes de começar**

- [Destine sua CLI para seu cluster](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure).
- [Crie um backup para um PV em seu cluster](#backup_restore_scheduled_backup).

Para restaurar os dados do {{site.data.keyword.cos_full_notm}} para um PV, conclua as etapas a seguir:

1. Obtenha o nome do PVC que liga o PV no qual você deseja restaurar seus dados.

   ```
   kubectl get pvc
   ```
   {: pre}

2. Crie um pod de restauração por meio da imagem `ibmcloud-backup-restore`. Para restaurar dados para um PV, deve-se montar o PVC que liga o PV ao pod de restauração.
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
       - nome: OBJECTSTORAGE
          valor: S3
       - name: ACCESS_KEY_ID
         value: '<access_key_ID>'
       - nome: SECRET_ACCESS_KEY
          valor: '<secret_access_key>'
       - nome: ENDPOINT
          valor: '<regional_endpoint>'
       - nome: BUCKET_NAME
          valor: '<bucket_name>'
       - name: RESTORE_DIRECTORY
         value: /myvol
       - name: BACKUP_NAME
         value: <backup_name>
       command: ["/bin/sh", "./vrestore"]
       volumeMounts:
       - mountPath: <mount_path1>
         name: <pvc_name1>
       - mountPath: <mount_path1>
         name: <pvc_name2>
     volumes:
     - name: pvc_name1
       persistentVolumeClaim:
         claimName: <pvc_name1>  
     - name: pvc_name2
       persistentVolumeClaim:
         claimName: <pvc_name2> 
   ```
   {: codeblock}

   <table>
   <caption>Tabela 2. Componentes de arquivo YAML</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Ícone de ideia"/> Entendendo os componentes do arquivo YAML</th>
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
     <td><code>&lt;mount_path&gt;</code></td>
     <td>O caminho de montagem do volume dentro do contêiner de restauração que monta o PVC no qual você deseja restaurar seus dados. Se você desejar restaurar dados para múltiplos PVCs, especifique um caminho de montagem separado para cada PVC. Certifique-se de que o nome que você usa em <code>volumeMount.name</code> corresponda ao nome do volume especificado na seção <code>volumes.name</code> de seu arquivo YAML. </td>
     </tr>
     <tr>
     <td><code> &lt;pvc_name&gt; </code></td>
     <td>O nome do PVC no qual você deseja restaurar seus dados. Para restaurar dados para múltiplos PVCs, inclua uma entrada <code>volumes</code> para cada PVC. Certifique-se de que o nome que você usa em <code>volumes.name</code> corresponda ao nome na seção <code>volumeMount.name</code> de seu arquivo YAML.</td>
     </tr> 
     </tbody>
     </table>

3. Crie o pod de restauração e inicie a restauração de seus dados.

    ```
    kubectl aplicar -f restorepod.yaml
    ```
    {: pre}

4. Verifique se o pod está em execução.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME              READY     STATUS             RESTARTS   AGE
    restorepod        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    O pod executa o comando de restauração e é parado. A mensagem `CrashLoopBackOff` significa que o Kubernetes está tentando reiniciar o pod.
5. Verifique se seus dados foram restaurados com sucesso.

    ```
    logs kubectl logs restorepod
    ```
    {: pre}

6. Remova o pod para evitar que ele use mais recursos.

    ```
    kubectl delete -f restorepod.yaml
    ```
    {: pre}

Você restaurou com êxito o backup. Agora é possível montar o PVC que liga o PV a qualquer outro pod no cluster para acessar os arquivos restaurados. Se os dados do contêiner cujo backup foi feito incluíam um usuário não raiz, deve-se incluir permissões não raiz no novo contêiner. Para obter mais informações, veja [Incluindo acesso de usuário não raiz para os volumes](/docs/containers?topic=containers-cs_troubleshoot_storage#cs_storage_nonroot).


## Referência de variável de ambiente
{: #backup_restore_env_reference}

Revise a lista completa de campos que podem ser passados como variáveis de ambiente ou editados no arquivo `config.conf` em um pod em execução. Qualquer valor que é passado como variável de ambiente suplanta o valor no arquivo `config.conf`. Para revisar as variáveis de ambiente de um pod, efetue login no pod usando o comando `kubectl exec` e execute `env`.

|Chave|Opções de valor|
|---|-------------|
|ACCESS_KEY_ID|O **access_key_id** que faz parte das credenciais HMAC no {{site.data.keyword.cos_full_notm}}.|
|SECRET_ACCESS_KEY|A **secret_access_key** que faz parte das credenciais HMAC no {{site.data.keyword.cos_full_notm}}.|
|TERRETAL|O nome do host para acessar dados do depósito do {{site.data.keyword.cos_full_notm}}.|
|BUCKET|O nome do depósito no {{site.data.keyword.cos_full_notm}} no qual os dados de backup são armazenados.|
{: caption="Tabela 4. Variáveis do {{site.data.keyword.cos_full_notm}}" caption-side="top"}

|Chave|Opções de valor|
|---|-------------|
|BACKUP_DIRECTORY|`/backup`. Padrão. Caminho de arquivo absoluto do diretório no qual o volume é montado. O backup dos dados é feito a partir desse diretório. Não selecione o diretório `backup_restore`, pois ele contém arquivos para os processos de backup e restauração.|
|BACKUP_NAME|`volume_backup`. Padrão. Escolha um nome de backup.|
|BACKUP_TYPE|`full`. Padrão. É feito backup de todos os arquivos toda vez.<br/> `incremental`. O backup é feito somente para arquivos novos ou mudados. Se você escolher `incremental`, deverá escolher valores para **SCHEDULING_INFO** e **SCHEDULING_TYPE**.|
|SCHEDULE_TYPE|`none`. Padrão. Crie um backup único.<br/> **Nota:** se você optar por criar um backup único, seu pod será removido do cluster depois que o backup for concluído. <br/> `periodic`. Mude o valor para periodic para criar backups planejados.|
|SCHEDULE_INFO|`hourly`. Crie um backup por hora.<br/>`daily`. Padrão. Crie um backup por dia.<br/>`weekly`. Crie um backup por semana. Deve-se incluir essa variável ao planejar uma atualização periódica.|
|EXCLUDE_DIRECTORIES|`none`. Padrão. Inclua o caminho de arquivo absoluto dos diretórios que você deseja excluir do backup. Separe os diretórios com uma vírgula.|
{: caption="Tabela 5. Variáveis de backup" caption-side="top"}

|Chave|Opções de Valor|
|---|-------------|
|BACKUP_NAME|`volume_backup`. Padrão. Inclua o nome do backup que está sendo restaurado do {{site.data.keyword.cos_full_notm}}.|
|RESTORE_DIRECTORY|`/backup`. Padrão. Diretório absoluto no qual o volume é montado. Os dados são restaurados para esse diretório. Não selecione o diretório `backup_restore`, pois ele contém arquivos para os processos de backup e restauração.|
{: caption="Tabela 6. Variáveis de restauração" caption-side="top"}

