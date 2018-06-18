---

copyright:
  years: 2016, 2017
lastupdated: "2017-11-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# Introdução à imagem *ibm-integration-bus*
{: #iib}

A imagem **ibm-integration-bus** é fornecida para o {{site.data.keyword.containerlong}}. Ela inclui a edição IBM Integration Bus for Developers.
{:shortdesc}


## Como ele Funciona
{: #how_it_works}

O IBM Integration Bus for Developers contém tudo o que você precisa para poder começar a desenvolver suas próprias soluções de integração.
{:shortdesc}

Depois de criar uma solução de integração, é possível usar a imagem **ibm-integration-bus** para provisionar um contêiner único no {{site.data.keyword.Bluemix_notm}}. É possível então implementar sua solução de integração nesse contêiner usando a UI da web ou a partir de um terminal.

**Nota**: é possível usar a imagem **ibm-integration-bus** para desenvolvimento e teste de unidade apenas. Também é possível usar a imagem para explorar o produto, aprender com tutoriais e avaliar a contribuição que o IBM Integration Bus pode fazer para sua organização.


## O que está incluído
{: #whats_included}

Esta imagem do IBM Integration Bus contém o pacote de software para o IBM Integration Bus for Developers Versão 10 \(também conhecida como Developer Edition\), que é uma versão de função integral do produto que pode ser usada para desenvolvimento e teste de unidade. É possível fazer download dessa versão gratuitamente e você terá a liberdade de utilizá-la dentro dos termos da licença.
{:shortdesc}

O IBM Integration Bus for Developers Versão 10.0 está disponível em sistemas operacionais Windows™ 64 bits e em sistemas operacionais Linux™ em x86-64. Todos os pré-requisitos do produto estão incluídos no pacote de download.

Para obter informações sobre os recursos que estão incluídos no IBM Integration Bus for Developers, consulte [IBM Integration Bus no IBM Knowledge Center ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.msgbroker.helphome.doc/help_home_msgbroker.htm){: new_window}.

É possível acessar a Developer Edition por meio da [página de download do IBM Integration Bus for Developers ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://www.ibm.com/marketing/iwm/iwm/web/pick.do?source=swg-wmbfd){: new_window}.


## Restrições de uso
{: #usage}

A tabela a seguir mostra as restrições que se aplicam ao uso grátis da imagem **ibm-integration-bus** no {{site.data.keyword.Bluemix_notm}}.
{:shortdesc}

**Nota**: a precificação para a imagem **ibm-integration-bus** independe da precificação para os contêineres que você usa em {{site.data.keyword.Bluemix_notm}}.

|Ambiente|Restrições de uso grátis|
|-----------|-----------------------|
|Desenvolvimento|Uso grátis **ilimitado** da imagem **ibm-integration-bus** para criar contêineres únicos para desenvolvimento e teste de unidade.|
|Produção|A Developer Edition está limitada a processar uma transação por segundo e não é suportada para uso em um ambiente de produção.|
{: caption="Tabela 1. Restrições no uso grátis da imagem ibm-integration-bus" caption-side="top"}


## Licença
{: #license}

Informações sobre licenças.
{:shortdesc}

*   Os arquivos do Docker e os scripts associados estão licenciados no [Apache License 2.0 ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](http://www.apache.org/licenses/LICENSE-2.0.html){: new_window}.
*   [Informações sobre licença para o IBM Integration Bus for Developers Versão 10.0 ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](http://www.ibm.com/software/sla/sladb.nsf/displaylis/1BD32B141C9FDAE38525802C0055A956?OpenDocument){: new_window}.

    **Nota**: a licença não permite distribuição adicional. Os termos para o IBM Integration Bus na imagem restringem o uso ao desenvolvimento e teste de unidade.


## Iniciar
{: #get_started}

Use a imagem **ibm-integration-bus** a partir do catálogo do {{site.data.keyword.Bluemix_notm}} ou selecione sua própria imagem licenciada para produção para criar um contêiner único.
{:shortdesc}

**Importante**: antes de iniciar, revise as [restrições de uso](#usage) para a imagem **ibm-integration-bus**.

**Nota**: é possível usar comandos nativos do Docker em todas as etapas que estiverem marcadas com um asterisco (\*).

   Conclua as etapas a seguir para executar o IBM Integration Bus em um contêiner:

1.  [Provisione um contêiner que se baseia na imagem ibm-integration-bus por meio da UI do {{site.data.keyword.Bluemix_notm}}](#provision)
2.  [Valide o contêiner e a instalação do IBM Integration Bus](#validate)
3.  [Criar e implementar um fluxo](#createdeployflow)
4.  [Monitore logs do IBM Integration Bus dentro do contêiner](#monitor)
5.  [Gerencie recursos do IBM Integration Bus que estão em execução no contêiner](#manage)


## Provisione um contêiner que se baseia na imagem **ibm-integration-bus**
{: #provision}

Provisione um contêiner do Docker em {{site.data.keyword.Bluemix_notm}}, com base na imagem **ibm-integration-bus** que é fornecida pelo {{site.data.keyword.IBM_notm}}.
{:shortdesc}

  Conclua as etapas a seguir:

1.  Efetue login no {{site.data.keyword.Bluemix_notm}}.
2.  No catálogo, selecione **Contêineres** e escolha a imagem **ibm-integration-bus** da qual construir seu contêiner.
3.  Selecione **Único** para criar um contêiner de instância única, que pode ser usado
para propósitos de desenvolvimento e teste.
4.  Insira o nome do contêiner; por exemplo, `iib`.
5.  Selecione o tamanho do contêiner.
6.  No campo **Endereço IP público**, selecione *Ligar IP público*.
7.  No campo **Portas públicas**, especifique `4414, 7800`.
8.  Expanda as opções **Avançadas** e clique em **Incluir novas variáveis de ambiente**.
9.  Inclua as seguintes variáveis de ambiente:

    <ul>
      <li>LICENSE com um valor de <i>accept</i>
      <li>NODENAME com um valor de &lt;MYNODE&gt; &lpar;em que &lt;MYNODE&gt; é o seu nome de nó escolhido&rpar;</li>
      <li>LOG_LOCATIONS com um valor de <i>/var/log/syslog</i>
    </ul>

    **Nota**: se você não especificar valores para as propriedades LICENSE e NODENAME, o contêiner será criado, mas não iniciado.

    A propriedade LOG_LOCATIONS especifica os logs dentro do contêiner que você deseja expor por meio da UI do {{site.data.keyword.Bluemix_notm}}.
10. Clique em **Criar** e, em seguida, espere o contêiner iniciar a execução.


## Valide o contêiner e a instalação do IBM Integration Bus
{: #validate}

Depois que o contêiner que executa o IBM Integration Bus estiver implementado no {{site.data.keyword.Bluemix_notm}}, verifique o status do contêiner e valide a instalação do IBM Integration Bus.
{:shortdesc}

  Conclua as etapas a seguir para verificar a instalação e configuração do IBM Integration Bus no contêiner:
  
1.  Configure a CLI do {{site.data.keyword.containershort_notm}}, conforme descrito em [Configurando o plug-in {{site.data.keyword.containershort_notm}} \(`bx ic`\) para usar a CLI nativa do Docker](/docs/containers/container_cli_cfic_install.html).

2.  Em um terminal, efetue login no {{site.data.keyword.Bluemix_notm}}. Execute os seguintes comandos:

    <ol>
       <li>
          <pre class="codeblock">bx login</pre>
       </li>
       <li>
          <pre class="codeblock">bx ic init</pre>
       </li>
    </ol>

3.  Verifique o status do contêiner.\*

    ```
    bx ic ps
    ```
    {: pre}

    **Nota**: nesse comando, é possível substituir **bx ic** por **docker** quando você [efetuar login no {{site.data.keyword.containershort_notm}}](/docs/containers/container_cli_cfic_install.html#container_cli_login) e configurar as variáveis de ambiente para usar comandos nativos do Docker. É possível usar comandos nativos do Docker em todas as etapas que são marcadas com um asterisco (\*).

    ```
    $ bx ic ps
    CONTAINER ID        IMAGE                                                       COMMAND             CREATED             STATUS                 PORTS                                                        NAMES
    46d9ec1e-952        registry.ng.bluemix.net/ibm-integration-bus:latest   ""                  2 minutes ago       Running a minute ago   xxx.xxx.xxx.xxx:4414->4414/tcp, xxx.xxx.xxx.xxx:7800->7800/tcp   iib
    ```
    {: screen}

4.  Anexe uma sessão Bash ao seu contêiner.\*

    ```
    bx ic exec -it <container_name> /bin/bash
    ```
    {: pre}

    em que *container_name* é o nome do contêiner.

    Por exemplo:

    ```
    $ bx ic exec -it iib /bin/bash
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

5.  Inicialize o ambiente executando o comando **mqsiprofile**, conforme descrito em [Ambiente de comando: sistemas Linux e UNIX™ ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](http://www.ibm.com/support/knowledgecenter/SSMKHH_9.0.0/com.ibm.etools.mft.doc/an26230_.htm){: new_window}.

    O comando para inicializar o ambiente está disponível no diretório: `/opt/ibm/iib-10.0.0.6/server/bin/`

    Por exemplo:

    ```
    (IIB_10:)iibuser@instance-0098858e:/$ . ./opt/ibm/iib-10.0.0.6/server/bin/mqsiprofile

    MQSI 10.0.0.6
    /opt/ibm/iib-10.0.0.6/server

    # Check the environment variable <MQSI_WORKPATH> is set
    (IIB_10:)iibuser@instance-0098858e:/$ echo $<MQSI_WORKPATH>
    /var/mqsi
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

6.  Execute o comando **mqsilist** para mostrar o status de seu nó, que deve ser listado como `running`. Por exemplo:

    ```
    (IIB_10:)iibuser@instance-0098858e:/$ mqsilist
    BIP1325I: Integration node 'MYNODE' with administration URI 'http://instance-0098858e:4414' is running.
    BIP8071I: Successful command completion.
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

Seu contêiner está agora em execução e é possível implementar soluções de integração para ele usando qualquer um dos métodos suportados.


## Monitore os logs do IBM Integration Bus dentro do contêiner
{: #monitor}

Monitore os logs por meio da UI do {{site.data.keyword.Bluemix_notm}} ou da linha de comandos.
{:shortdesc}

Antes de iniciar, configure a CLI do {{site.data.keyword.containershort_notm}} para monitorar os logs a partir da linha de comandos. Para obter mais informações, consulte [Configurando o plug-in {{site.data.keyword.containershort_notm}} (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

A imagem **ibm-integration-bus** é configurada para colocar mensagens do IBM Integration Bus em `/var/log/syslog` dentro do contêiner.

Escolha uma das maneiras a seguir para monitorar logs do IBM Integration Bus:

*  Se você configurou a variável de ambiente LOG_LOCATIONS quando provisionou o contêiner, será possível monitorar os logs por meio da UI do {{site.data.keyword.Bluemix_notm}}.

    Conclua as etapas a seguir para acessar os logs:

    <ol>
       <li>No painel de contêiner, selecione o contêiner que você deseja monitorar.</li>
       <li>Selecione <b>Monitoramento e criação de log</b>.</li>
       <li>Selecione <b>Registro</b>. O painel lista as entradas de log.</li>
       <li>Para customizar a visualização de logs, selecione <b>Visualização avançada</b> na página Criação de log para abrir o Kibana. Para obter mais informações, consulte
           <a href="https://console.bluemix.net/docs/containers/container_planning.html#container_planning_monlog" target="_blank">Monitoramento e criação de log</a> para iniciar.
       </li>
    </ol>

*  Em um terminal, use o Docker ou a CLI do {{site.data.keyword.containerlong_notm}} para monitorar as mensagens. Conclua as etapas a seguir:

    <ol>
       <li>
       Configure seu terminal.

        <ul>
        <li>Para usar a CLI do {{site.data.keyword.containerlong_notm}} para gerenciar contêineres no {{site.data.keyword.Bluemix_notm}} enquanto ainda estiver usando a CLI do Docker diretamente para gerenciar seu host local do Docker, conclua as etapas a seguir para configurar o ambiente:
           <ol>
             <li>
               <pre class="codeblock">bx login</pre>
             </li>
             <li>
               <pre class="codeblock">bx ic init</pre>
             </li>
           </ol>
        </li>
        <li>Para usar a CLI do Docker para gerenciar contêineres no {{site.data.keyword.Bluemix_notm}}, conclua as etapas a seguir:
          <ol>
            <li>
              <pre class="codeblock">bx login</pre>
            </li>
            <li>
              <pre class="codeblock">bx ic init</pre>
               Copie os valores fornecidos para as variáveis de ambiente a seguir: &lt;DOCKER_HOST&gt;, &lt;DOCKER_CERT_PATH&gt; e &lt;DOCKER_TLS_VERIFY&gt;.
            </li>
            <li>
            Substitua o ambiente local do Docker, configurando as variáveis a seguir para se conectar ao {{site.data.keyword.containerlong_notm}}:
               <ul>
                 <li><pre class="codeblock">export DOCKER_HOST=&lt;your_host_value&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;your_cert_path_value&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
               </ul>
                <b>Nota</b>: apenas alguns comandos do Docker são suportados por esta opção.
            </li>
          </ol>
        </li>
      </ul>
      </li>
      <li>
      Acesse o log. Escolha uma das opções a seguir:

      <ul>
         <li>Se seu terminal estiver configurado para executar comandos da CLI do {{site.data.keyword.containerlong_notm}}, execute o comando a seguir:

            <pre class="codeblock">bx ic exec &lt;container_id&gt; tail -f /var/log/syslog</pre>

            em que *container_id* é o nome do seu contêiner.

            Por exemplo:

            <pre class="screen">bx ic exec iib tail -f /var/log/syslog</pre>

          </li>
          <li>
           Se seu terminal estiver configurado para executar comandos da CLI do Docker, execute o comando a seguir:

            <pre class="codeblock">docker exec &lt;container_id&gt; tail -f /var/log/syslog</pre>

            em que *container_id* é o nome do seu contêiner.

            Por exemplo:

            <pre class="screen">docker exec iib tail -f /var/log/syslog</pre>

          </li>
        </ul>
       </li>
    </ol>


## Gerencie recursos do IBM Integration Bus que estão em execução no contêiner
{: #manage}

Ative a UI da web do IBM Integration Bus ou execute comandos por meio de um terminal para gerenciar recursos do IBM Integration Bus que estão em execução em um contêiner.
{:shortdesc}

  Escolha uma das opções a seguir para gerenciar recursos do IBM Integration Bus no contêiner:

*  [Ative a UI da web do IBM Integration Bus](#launchwebUI) e gerencie o IBM Integration Bus graficamente.
*  Em um terminal, use o Docker ou a CLI do {{site.data.keyword.containershort_notm}} para gerenciar recursos do IBM Integration Bus:
    *   [Execute comandos de administração no contêiner usando a CLI do {{site.data.keyword.containerlong_notm}}](#admin_commands_containers_cli)
    *   [Execute comandos de administração no contêiner usando a CLI do Docker](#admin_commands_docker_cli)


## Ative a UI da web do IBM Integration Bus
{: #launchwebUI}

Ative a UI da web para gerenciar recursos do IBM Integration Bus que estão em execução no contêiner por meio do seu navegador.
{:shortdesc}

Siga estas etapas para conectar um navegador a seu host, que foi exposto em [Introdução à imagem ibm-integration-bus para {{site.data.keyword.Bluemix_notm}}](#get_started). A interface com o usuário da web do IBM Integration Bus será exibida.

1.  Selecione seu contêiner por meio da UI do {{site.data.keyword.Bluemix_notm}} e verifique se o status do contêiner é `Em execução`.
2.  Verifique os detalhes do contêiner para localizar o IP público.
3.  Abra um navegador da web com a URL a seguir:

    ```
    http://<DockerContainerPublicIP>:4414
    ```
    {: pre}

    em que *DockerContainerPublicIP* é o IP público do contêiner que você verificou na etapa anterior.

4.  O navegador abre a interface com o usuário da web e você está pronto para administrar o IBM Integration Bus.


## Executar comandos de administração no contêiner usando a CLI do {{site.data.keyword.containerlong_notm}}
{: #admin_commands_containers_cli}

Use a CLI do {{site.data.keyword.containershort_notm}} para executar comandos de administração do IBM Integration Bus diretamente em um contêiner.
{:shortdesc}

Antes de iniciar, configure a CLI do {{site.data.keyword.containershort_notm}} para executar comandos de administração a partir da linha de comandos. Para obter mais informações, consulte [Configurando o plug-in {{site.data.keyword.containershort_notm}} (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

  Conclua as etapas a seguir para configurar um terminal para executar os comandos **bx ic** para gerenciar o IBM Integration Bus:

1.  Em um terminal, efetue login no {{site.data.keyword.Bluemix_notm}}. Execute os seguintes comandos:

      <ol>
        <li>
           <pre class="codeblock">bx login</pre>
        </li>
        <li>
           <pre class="codeblock">bx ic init</pre>
        </li>
       </ol>
           
2.  Anexe uma sessão Bash ao contêiner para configurar uma sessão interativa.\*

    ```
    bx ic exec -it <container_name> /bin/bash
    ```
    {: pre}

    em que *container_name* é o nome do contêiner.

    Por exemplo:

    ```
    bx ic exec -it iib /bin/bash
    ```
    {: screen}

    É possível originar o mqsiprofile e executar seus comandos por meio deste ponto, em um shell dentro do contêiner.

3.  Como alternativa, use o comando **bx ic exec** para executar uma sessão Bash não interativa que possa executar qualquer um dos comandos do IBM Integration Bus.\*

    Por exemplo:

    ```
    bx ic exec iib <container_name> /bin/bash -c mqsilist
    ```
    {: screen}


## Execute comandos de administração no contêiner usando a CLI do Docker
{: #admin_commands_docker_cli}

Use a CLI do Docker para executar comandos de administração do IBM Integration Bus diretamente em um contêiner.
{:shortdesc}

Antes de iniciar, configure a CLI do {{site.data.keyword.containershort_notm}} para executar comandos de administração a partir da linha de comandos. Para obter mais informações, consulte [Configurando o plug-in {{site.data.keyword.containershort_notm}} (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

  Conclua as etapas a seguir para configurar um terminal que execute comandos do Docker a fim de gerenciar o IBM Integration Bus:

1.  Em um terminal, efetue login no {{site.data.keyword.Bluemix_notm}}. Execute os seguintes comandos:

    <ol>
     <li>
       <pre class="codeblock">bx login</pre>
     </li>
     <li>
       <pre class="codeblock">bx ic init</pre>

      <p>Copie os valores fornecidos para as variáveis de ambiente a seguir: &lt;DOCKER_HOST&gt;, &lt;DOCKER_CERT_PATH&gt; e &lt;DOCKER_TLS_VERIFY&gt;.</p>
      </li>
      <li>  Substitua o ambiente local do Docker, configurando as variáveis a seguir para se conectar ao {{site.data.keyword.containershort_notm}}:
        <ul>
           <li><pre class="codeblock">export DOCKER_HOST=&lt;your_host_value&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;your_cert_path_value&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
        </ul>

          <p><b>Nota</b>: apenas alguns comandos do Docker são suportados por esta opção.</p>
     </li>
    </ol>

2.  Anexe uma sessão bash ao contêiner para configurar uma sessão interativa. Execute o comando a seguir:

    ```
    docker exec -it <container_name> /bin/bash
    ```
    {: pre}

    em que *container_name* é o nome do contêiner.

    Por exemplo:

    ```
    docker exec -it iib /bin/bash
    ```
    {: screen}

    É possível originar o mqsiprofile e executar seus comandos por meio deste ponto, em um shell dentro do contêiner.

3.  É possível originar o mqsiprofile e executar seus comandos por meio deste ponto, em um shell dentro do contêiner.

    Como alternativa, use docker exec para executar uma sessão Bash não interativa que possa executar qualquer um dos comandos do IBM Integration Bus. 
    
    Por exemplo:

    ```
    docker exec iib <container_name> /bin/bash -c mqsilist
    ```
    {: screen}


## Criar e implementar um fluxo
{: #createdeployflow}

Crie e implemente um fluxo de mensagens seguindo as instruções no IBM Integration Bus Knowledge Center.
{:shortdesc}

Para obter informações sobre como criar um fluxo de mensagens, consulte [Como criar e gerenciar fluxos de mensagens? ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40150_.htm){: new_window}.

Para obter informações sobre como implementar um fluxo de mensagens, consulte [Como implementar e configurar fluxos de mensagens? ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40180_.htm){: new_window}.
