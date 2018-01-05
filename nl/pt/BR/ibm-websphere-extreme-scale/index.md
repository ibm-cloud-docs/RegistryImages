---

copyright: years: 2017 lastupdated: "2017-10-30"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# Introdução à imagem **ibm-websphere-extreme-scale** 
{: #ibm-websphere-extreme-scale}

As imagens **ibm-websphere-extreme-scale** são fornecidas para o {{site.data.keyword.containerlong}}.

## Como ele Funciona 
{: #how_it_works}

A imagem do IBM WebSphere&reg; eXtreme Scale é um recurso de cache distribuído em memória em execução dentro do ambiente de tempo de execução do Liberty. Ela fornece servidores de armazenamento em cache do Liberty, ferramentas de operação de armazenamento em cache, um console administrativo e os serviços de administração REST prontos para utilização com base nos padrões de mercado e especificações mais recentes. Essa imagem é construída na tecnologia IBM WebSphere eXtreme Scale de núcleo. A tecnologia de armazenamento em cache carregada nessa imagem suporta totalmente a especificação e a conformidade JCache \(JSR107\).

Os destaques principais a seguir estarão disponíveis ao usar a imagem `ibm-websphere-extreme-scale`:

-   Uma solução de armazenamento em cache distribuído fácil de implementar, fácil de configurar, com base na tecnologia do eXtreme Scale
-   Console administrativo simples e fácil de usar e a API de REST
-   Customização completa para suportar todos os casos de uso de armazenamento em cache do eXtreme Scale
-   Imagem facilmente configurável que se integra a seu ambiente de armazenamento em cache em minutos
-   Conformidade de JCache para suportar a especificação de armazenamento em cache JSR107


## O que está incluído 
{: #whats_included}

Cada imagem **ibm-websphere-extreme-scale** fornece os pacotes de software a seguir.

-   Imagem base do Ubuntu 16.04, 64 bits
-   IBM WebSphere eXtreme Scale
-   IBM Java Runtime Environment 8.0

Os recursos específicos que são instalados na imagem dependem da tag que você seleciona. A tabela a seguir mostra qual nível de código está incluído em cada uma das imagens **ibm-websphere-extreme-scale**. Para obter mais informações sobre esses recursos, veja a [documentação do eXtreme Scale](http://www.ibm.com/support/knowledgecenter/SSTVLU).

|Marcação|Descrição|
|---|-----------|
|**ibm-websphere-extreme-scale:latest**|Use essa tag para especificar a imagem mais recente do IBM WebSphere eXtreme Scale.|
|**ibm-websphere-extreme-scale:8.6.1.x**|Use essa tag para especificar um nível de liberação específico do WebSphere eXtreme Scale.|
{: caption="Tabela 1. Imagens do IBM WebSphere eXtreme Scale" caption-side="top"}

## Restrições de uso 
{: #usage}

A tabela a seguir mostra as restrições que se aplicam ao uso grátis da imagem **ibm-websphere-extreme-scale** no {{site.data.keyword.Bluemix_notm}}.

|Ambiente|Restrições de uso|
|-----------|------------------|
|Desenvolvimento|A imagem `ibm-websphere-extreme-scale` permite uso grátis ilimitado para desenvolvimento.|
|Produção|O uso de produção da imagem **ibm-websphere-extreme-scale** é limitado a usuários do produto IBM WebSphere eXtreme Scale licenciado. Para obter mais informações sobre o uso de produção, veja [Visualizar precificação e compra](https://www-112.ibm.com/software/howtobuy/buyingtools/paexpress/Express?P0=E1&part_number=D56YTLL,D56YVLL,D572PLL,D572MLL,D0594LL,D059GLL&catalogLocale=en_US&Locale=en_US&country=USA&PT=jsp&CC=USA&VP=&TACTICS=&S_TACT=&S_CMP=&brand=SB05).|
{: caption="Tabela 2. Restrições de uso para as imagens ibm-websphere-extreme-scale no {{site.data.keyword.Bluemix_notm}}" caption-side="top"}

**Nota:** a precificação para a imagem **ibm-websphere-extreme-scale** independe da precificação para os contêineres que você usar no {{site.data.keyword.Bluemix_notm}}.

Revise os termos de uso para imagens certificadas pela IBM na seção Licença dos [Documentos de informações sobre licença](http://www-03.ibm.com/software/sla/sladb.nsf/displaylis/A44EE6829150B2998525801700516718?OpenDocument).


## Iniciar 
{: #get_started}

É possível usar o console ou a linha de comandos para implementar a imagem **ibm-websphere-extrema-scale**.

Este procedimento inclui as opções a seguir para a implementação da imagem com o console ou a linha de comandos:

-   Selecione a imagem do [console](#bmconsole) do {{site.data.keyword.Bluemix_notm}} no **Catálogo**.
-   Use o comando cf ic para iniciar a imagem com a [linha de comandos](#bmcommand) CF.

**Importante:** antes de iniciar, revise as restrições de uso, conforme descrito na seção anterior para as imagens **ibm-websphere-extreme-scale**.


### Implemente a partir do console do {{site.data.keyword.Bluemix_notm}}.
{: #bmconsole}

   1.  No catálogo, selecione **Contêineres** e escolha a imagem **ibm-websphere-extreme-scale** para iniciar a construção de seu contêiner.
   2.  Selecione a versão da imagem **ibm-websphere-extreme-scale** que você deseja usar no menu suspenso **TAG/Versão**.
   3.  Crie um contêiner único.

        **Nota:** se você desejar criar mais de um contêiner, não use a opção **Escalável** para criar dois ou mais grupos de membros de cache do eXtreme Scale. Em vez disso, selecione a opção **Único** para criar múltiplos contêineres e use a UI do painel do eXtreme Scale para associar membros do cache.

        Para obter mais informações, consulte [Criando um único contêiner usando o Painel do {{site.data.keyword.Bluemix_notm}}](/docs/containers/container_single_ui.html#gui).

        1.  No painel Criar um contêiner, especifique um **Nome do contêiner**; por exemplo, `wxs1`. Nenhum espaço ou outro caractere não imprimível é permitido no nome do contêiner.
        2.  Para **Tamanho**, especifique o tamanho dos contêineres usados para iniciar a imagem ibm-eXtreme-scale.
        3.  Para **Endereço IP público**, especifique Solicitar e ligar IP público
        4.  Abra o painel Opções avançadas. Crie um volume de forma que os dados de configuração do eXtreme Scale possam ser persistidos ao instalar um upgrade contínuo em um momento posterior; por exemplo:
            1. Clique em **Criar um volume** em Opções avançadas e especifique o **Nome do volume**. Por exemplo, digite vol-wxs1.
            2. Clique em **Incluir uma nova variável de ambiente**. A imagem **ibm-websphere-extreme-scale** requer variáveis de ambiente que são armazenadas dentro do contêiner e acessadas por usuários autorizados.
            <table>
            <caption>Tabela 3. Variáveis de ambiente para a imagem ibm-websphere-extreme-scale</caption>
               <tr>
                 <th>Nome da variável</th>
                 <th>Valor a ser especificado </th>
               </tr>
               <tr>
                 <td>SECRET_KEY</td>
                 <td>O valor da chave secreta; por exemplo, <var class="keyword varname">s3cretKey!</var>. Esta é a chave secreta usada por servidores eXtreme Scale para enviar a comunicação interna entre os múltiplos membros em um grupo de membros em cache. Para obter mais informações, veja a [seção Grupo de membros do cache](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txscreategridtut1.html).</td>
               </tr>
               <tr>
                 <td>XSADMIN_PWD</td>
                 <td>A senha do usuário xsadmin que efetua logon na UI do painel eXtremeScale; por exemplo, <var class="keyword varname">xsadmin4Me!</var>.</td>
               </tr>
               </table>
  
              **Nota:** é possível mudar a senha e a chave secreta após o contêiner do eXtreme Scale ser iniciado. A chave secreta e a senha têm as regras de senha a seguir. Certifique-se de inserir ambas com base na regra a seguir:

              A senha e a chave secreta devem conter pelo menos um número [`0-9`], um caractere em maiúscula [`A-Z`], uma caractere especial [`~!@#$%^&*()_-+=:;'[]{}|<>,.?/`] e ter pelo menos dez caracteres.

              Para obter mais informações sobre como mudar a senha, veja a [documentação Administrando](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txsadministerxsld.html).

              A porta é incluída no campo **Porta pública** por padrão.

   4.  Clique em **Criar** para criar um contêiner por meio do Painel do {{site.data.keyword.Bluemix_notm}}.
    
   5.  Após o início completo do contêiner e dos servidores eXtreme Scale, efetue logon na UI do painel do eXtreme Scale e verifique a implementação. No console do {{site.data.keyword.Bluemix_notm}}, clique em um contêiner para visualizar o IP público. O contêiner do WebSphere eXtreme Scale pode ser acessado a partir da URL a seguir:

    ```
    https://<Public_IP>:9443
    ```
    {: pre}

   **Nota:** pode levar de 5 a 10 minutos para o servidor iniciar completamente.


### Implemente a imagem a partir da linha de comandos.
{: #bmcommand}
 1.  Crie um volume do Docker para armazenar dados persistentes do IBM WebSphere eXtreme Scale. Por exemplo, insira o comando a seguir:

  ```
  cf ic volume create <vol-wxs1>
  ```
  {: pre}

 2.  Insira o comando a seguir para iniciar o contêiner do IBM WebSphere eXtreme Scale no {{site.data.keyword.Bluemix_notm}}:

    **Nota:** substitua yourSecretKey e yourXsadminPassword pela sua própria senha com base na regra de senha mencionada anteriormente.

   ```
   cf ic run -itdP --volume <vol-wxs1:/vol> --name <wxs1> -m 1024 -e SECRET_KEY=<yourSecretKey> -e XSADMIN_PWD=<yourXsadminPassword> registry.ng.bluemix.net/ibm-websphere-extreme-scale:latest
   ```
   {: pre}
        
     **Nota:** o nome do contêiner deve conter apenas caracteres alfanuméricos e começar com uma letra. Como alternativa, é possível configurar <code>-e XSLD_CONTAINER_ALIAS=<wxs1></code> se precisar que seu nome do contêiner contenha outros caracteres não alfanuméricos.

 3.  Solicite um IP público a ser usado para seu primeiro contêiner do IBM WebSphere eXtreme Scale. Insira o comando a seguir para gravar o endereço IP:

   ```
   cf ic ip request
   ```
   {: pre}

 4.  Insira o comando a seguir para ligar o contêiner do WebSphere eXtreme Scale ao endereço IP público que você solicitou:

   ```
   cf ic ip bind <REQUESTED_IP> wxs1
   ```
   {: pre}

 5.  Insira o comando a seguir para verificar se o status do contêiner `wxs1` é Em execução:

   ```
   cf ic ps -a
   ```
   {: pre}

 6.  Insira o comando a seguir para conectar-se ao contêiner `wxs1` e monitorar o log nanny:

   ```
   cf ic exec -it <wxs1> tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
        ```
   {: pre}

    **Nota:** pode levar de 5 a 10 minutos para o servidor iniciar.

 7.  Após o contêiner e os servidores eXtreme Scale estarem totalmente iniciados, efetue logon na UI do painel do eXtreme Scale e verifique a implementação:

   ```
   https://<REQUESTED_IP>:9443
   ```
   {: pre}


## Incluindo capacidade de armazenamento em cache 
{: #caching}

1.  Implemente outra imagem a partir do console ou da linha de comandos seguindo as mesmas etapas como antes.
2.  Após o contêiner e os servidores eXtreme Scale estarem totalmente iniciados, efetue logon na UI do painel eXtreme Scale do membro:

    ```
    https://<REQUESTED_IP>:9443
    ```
    {: pre}

3.  Abra a página Membros do cache de dados e, sob a coluna **Ação** desse membro, selecione **Associar**.
4.  Insira o IP público solicitado do primeiro membro, a chave secreta do primeiro membro e um nome exclusivo para esse membro no grupo de cache.
5.  Clique em **Associar** e aguarde a tarefa ser concluída. Quando a tarefa for concluída, todos os membros serão exibidos na página Membros do cache de dados.

## Executando um upgrade contínuo 
{: #rolling_upgrade}

As etapas a seguir são necessárias para executar um upgrade contínuo de contêineres que executam `ibm-websphere-extreme-scale` em {{site.data.keyword.containershort_notm}}.

**Nota:** se seu grupo de membros do cache do eXtreme Scale só tiver um membro, remova este contêiner do {{site.data.keyword.IBM_notm}} e crie-o novamente do zero. No entanto, se seu grupo de membros do cache do eXtreme Scale tiver mais de um membro e diversos servidores de catálogos implementados e você desejar manter os dados em cache em {{site.data.keyword.containershort_notm}}, conclua as etapas de upgrade contínuo a seguir para evitar a perda de quaisquer dados em cache. Faça upgrade de membros do cache que tenham somente servidores de contêiner primeiro e, em seguida, faça upgrade de membros do cache que também tenham servidores de catálogos.

1.  Inicie um novo contêiner chamado wxsmigrate e use-o para fazer upgrade de todos os contêineres existentes do eXtreme Scale. Veja a [seção Introdução](#get_started) nesta página para obter mais informações. Os arquivos .zip de atualização do eXtreme Scale mais recentes serão implementados sob a pasta /tmp em seu novo contêiner wxsmigrate.
2.  Copie os arquivos .zip do eXtreme Scale atualizados do contêiner wxsmigrate em seu sistema local, no qual você está executando o plug-in da CLI do {{site.data.keyword.containershort_notm}}:

    ```
    cf ic cp wxsmigrate:/tmp/xsld-bin-update.zip
    ```
    {: pre}
    
    ```
    cf ic cp wxsmigrate:/tmp/xsld-derby-update.zip
    ```
    {: pre}

3.  Agora, para cada membro do cache dentro de um grupo de membros do cache, conclua as etapas a seguir, um contêiner por vez:

    1. Copie os arquivos .zip do eXtreme Scale atualizados do seu sistema local no contêiner do eXtreme Scale que você deseja fazer upgrade:
    
      ```
      cf ic cp  xsld-bin-update.zip  <container_name>:/tmp/xsld-bin-update.zip
      ```
      {: pre}
  
      ```
      cf ic cp  xsld-derby-update.zip  <container_name>:/tmp/xsld-derby-update.zip
      ```
      {: pre}
     
    2. Conecte-se ao contêiner do qual você deseja fazer upgrade: 
    
      ```
      cf ic exec -it <container_name> bash
      ```
      {: pre}

    3. Pare os servidores XSLD no contêiner que está pronto para o upgrade.
      
      ```
      /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
      ```
      {: pre}
      
      Aguarde até que os servidores estejam interrompidos.
      
    4. Descompacte o arquivo ZIP do código de atualização mais recente do eXtreme Scale dentro do contêiner:
      
      ```
      unzip  -o  /tmp/xsld-bin-update.zip   -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
      
      ```
      unzip  -o  /tmp/xsld-derby-update.zip -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
    
    5. Inicie o servidor XSLD novamente:
    
      ```
      /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
      ```
      {: pre}
      
    6. Monitore o log nanny para assegurar que os servidores tenham sido iniciados:
    
      ```
      tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
      ```
      {: pre}
      
4. Após o upgrade ser concluído em todos os contêineres IBM, remova o contêiner wxsmigrate:

 ```
 cf ic rm -f <wxsmigrate>
 ```
 {: pre}

## Resolução de problemas 
{: #troubleshoot}

###Resolução de problemas de conectividade de membro do cache quando o mapeamento de host é perdido 
{: #troubleshoot_cache}

Conclua as etapas a seguir quando os membros de um grupo de cache perderem a conectividade devido a um mapeamento de host perdido entre os contêineres.

1.  Conecte-se a cada contêiner:

    ```
    cf ic exec -it <container_name> bash
    ```
    {: pre}

2.  Reúna o IP privado, nome do host e alias de host para cada membro.

    O alias de host é o valor da variável de ambiente XSLD_CONTAINER_ALIAS:

    ```
    echo ${<XSLD_CONTAINER_ALIAS>}
    ```
    {: pre}

    O IP privado é o endereço IP retornado da execução do comando hostname -I dentro do contêiner:

    ```
    hostname -I
    ```
    {: pre}

    O nome do host é o nome do host retornado da execução do comando hostname dentro do contêiner.


3.  Execute o script recoverXSLD.sh em cada membro que estiver passando todos os outros aliases de membro, IPs e nomes de host:

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/recoverXSLD.sh <member2_alias> <member2_private_ip> <member2_hostname> ... <memberN_alias> <memberN_private_ip> <memberN_hostname>
    ```
    {: pre}

4.  Pare a execução dos servidores, executando stopXSLD.sh em cada contêiner no grupo de cache. Execute stopXSLD.sh:

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
    ```
    {: pre}

5.  Se stopXSLD.sh não progredir ao tentar parar o servidor de catálogos, siga as etapas a seguir:
    1.  Liste o processo do servidor de catálogos:

        ```
        ps -ef | grep cs-default
        ```
        {: pre}

    2.  Termine o processo do servidor de catálogos usando o ID do processo:

        ```
        kill -9 <catalog_server_process_id>
        ```
        {: pre}

    3.  Execute stopXSLD.sh novamente:

        ```
        /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
        ```
        {: pre}

6.  Inicie o XSLD em todos os contêineres aproximadamente ao mesmo tempo. Execute startXSLD.sh:

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
    ```
    {: pre}


### Resolução de problemas de uma tarefa de replicação do Derby interrompida 
{: #troubleshoot_derby}

Se uma tarefa de replicação do Derby não progrediu além de 45% por mais de dez minutos, isso é provavelmente devido a um problema de rede. Conclua as etapas a seguir para solucionar o problema:

1.  Use um utilitário REST POSTing, como cURL, para cancelar a tarefa de replicação do derby.

    ```
    curl -X PUT --header "Content-Type: application/json" --header "Accept: application/json" "https://<PUBLIC_IP>:9445/wxsadmin/v1/task/cancel/<DERBY_REPLICATION_TASK_ID>/force"
    ```
    {: pre}

    **Nota:** o <PUBLIC_IP> é aquele que foi usado no comando para iniciar a tarefa de replicação do Derby e <DERBY_REPLICATION_TASK_ID> é a saída do ID de tarefa desse comando.

2.  Execute a tarefa de replicação do derby novamente.

## Acessando a API de REST 
{: #api}

Quando o eXtreme Scale estiver implementado no {{site.data.keyword.containershort_notm}}, conclua as etapas a seguir para acessar a API de REST:

1.  Recupere o nome do host do contêiner que você planeja usar para se conectar à API de REST.
    1.  Conecte-se ao contêiner:

        ```
        cf ic exec -it <container_name> bash
        ```
        {: pre}

    2.  Liste o nome do host:

        ```
        hostname
        ```
        {: pre}

2.  Recupere o IP público do mesmo contêiner.

    Se o contêiner não tiver um IP público, siga as etapas na seção Introdução para fornecer ao contêiner um IP público usando o {{site.data.keyword.Bluemix_notm}} [console](#bmconsole) ou a [linha de comandos](#bmcommand).

3.  Crie uma entrada no arquivo hosts de seu computador cliente local que mapeie o IP público e o nome do host do contêiner juntos. A entrada é semelhante a:

    ```
    169.44.8.87 instance-05106102
    ```
    {: pre}

    Nomes de caminho completos típicos para arquivos hosts são:

    -   Para UNIX: /etc/hosts
    -   Para Windows: C:\\Windows\\System32\\drivers\\etc\\hosts
4.  Acesse as APIs de REST do Swagger em um navegador.
    -   Para operações CRUD:

        ```
        https://<host_name>:9444/ibm/api/explorer
        ```
        {: pre}

    -   Para operações administrativas:

        ```
        https://<host_name>:9445/ibm/api/explorer
        ```
        {: pre}


Para obter amostras e para atualizações recentes, visite [WebSphere eXtreme Scale no GitHub](https://github.com/ibmWebsphereExtremeScale). Além disso, veja a documentação e os vídeos de apoio a seguir para obter mais ajuda com a implementação do eXtreme Scale:

-   [Documentação do IBM WebSphere eXtreme Scale](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/cxsgetstartxsld.html)
-   [Fórum do XS developerWorks](https://www.ibm.com/developerworks/community/forums/html/forum?id=11111111-0000-0000-0000-000000000778&ps=25)
-   [Introdução ao WebSphere eXtreme Scale V8.6.x](https://www.youtube.com/watch?v=xw7Ck3c40rA)
-   [Incluindo o cache com o WebSphere eXtreme Scale - Implementação do Liberty](https://www.youtube.com/watch?v=I1g88bnstn8)
-   [Configurando um cliente do WAS para trabalhar com uma grade DynaCache de Implementação do Liberty do WebSphere eXtreme Scale ](https://www.youtube.com/watch?v=RvhcknNX-4Q)
-   [Configurando um cliente do WAS para trabalhar com uma grade de Cache de Sessão de Implementação do Liberty do WebSphere eXtreme Scale ](https://www.youtube.com/watch?v=GKTy9I66Klc)

