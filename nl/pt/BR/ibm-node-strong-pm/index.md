---

copyright:
  years: 2015, 2017
lastupdated: "2017-10-30"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}

# Introdução à imagem ibm-node-strong-pm
{: #getting_started_node_strong}

O Nó {{site.data.keyword.IBM}} com a imagem StrongLoop \(**ibm-node-strong-pm**\) é fornecido para {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

## Como ele Funciona 
{: #how_it_works}

Use a imagem **ibm-node-strong-pm** para criar um contêiner único que implemente uma instância em execução do [StrongLoop Process Manager](https://www.strongloop.com). O Strongloop Process Manager permite implementar, gerenciar e monitorar apps Node.js na nuvem {{site.data.keyword.IBM_notm}} que foram implementados em uma máquina remota.
{:shortdesc}

A máquina remota deve ter instalado uma instância do StrongLoop Arc. StrongLoop Arc é uma
interface gráfica com o usuário para a plataforma do StrongLoop e inclui ferramentas para construção, criação de perfil e
monitoramento de apps Node.js. Depois de construir um app com o StrongLoop Arc, você envia por push e o implementa no
contêiner no {{site.data.keyword.Bluemix_notm}} que executa o
StrongLoop Process Manager. Para estabelecer uma conexão entre a máquina remota e o contêiner,
deve-se ligar um endereço IP público ao contêiner e configurar esse endereço IP como seu servidor
StrongLoop Process Manager no StrongLoop Arc. Quando seu app é implementado por meio do StrongLoop Process
Manager, é possível monitorar métricas e o desempenho de seu app no StrongLoop Process Manager. Para atualizar ou escalar seu app, deve-se mudá-lo usando o StrongLoop Arc e, em seguida, enviar as mudanças por push
para o contêiner.

**Nota:** o **ibm-node-strong-pm** não é suportado para ser usado com grupos de contêiner no {{site.data.keyword.Bluemix_notm}}.

## O que está incluído
{: #whats_included}

-   Todos os pacotes de software que estão incluídos na imagem **ibmnode:latest**. Consulte o
tópico [O que está incluído](/docs/services/RegistryImages/ibmnode/index.html#getting_started_node) na Introdução à imagem **ibmnode** do {{site.data.keyword.Bluemix_notm}}.
-   StrongLoop Process Manager

## Iniciar
{: #how_to_get_started}

É possível usar a imagem **ibm-node-strong-pm** do Catálogo {{site.data.keyword.Bluemix_notm}} para testar um contêiner único em {{site.data.keyword.containershort_notm}}. A imagem é
iniciada com uma instância em execução do StrongLoop Process Manager, que você pode acessar assim que
o contêiner está disponível. Usando uma instância de StrongLoop Arc que está conectada a esse Process
Manager, você pode enviar um aplicativo Node.js por push e acessá-lo.
{:shortdesc}

**Nota:** antes de iniciar, revise os termos de uso para imagens certificadas {{site.data.keyword.IBM_notm}} na seção Uso da [biblioteca websphere-liberty Docker](https://github.com/docker-library/docs/tree/master/websphere-liberty).

1.  No catálogo, selecione **Contêineres** e escolha a
imagem **ibm-node-strong-pm** da qual construir seu contêiner.
2.  Crie um contêiner único.
    -   No campo **Tamanho**, selecione um tamanho de contêiner de pelo menos 1 GB de memória \(**Pequeno**\).
    -   No campo **Portas públicas**, insira as portas 8701 e 3001. Essas portas são
expostas automaticamente quando o contêiner é implementado no {{site.data.keyword.Bluemix_notm}} e um pré-requisito para ligar um endereço
IP público ao contêiner. A porta 8701 é usada para acessar o StrongLoop Process Manager no
contêiner. A porta 3001 é a porta na qual o app atende depois de ser implementado no StrongLoop
Process Manager.

        **Nota:** para implementar apps adicionais no StrongLoop Process Manager, exponha mais portas. As portas devem ser consecutivas, começando com 3002. Por exemplo, 3002, 3003, 3004,
etc.

    -   Para obter informações adicionais, consulte [Criando um único contêiner usando o Painel {{site.data.keyword.Bluemix_notm}}](/docs/containers/container_single_ui.html#gui).
3.  Ligue um endereço IP público ao contêiner. Para obter mais informações, consulte [Solicitando e ligando endereços IP](/docs/containers/container_single_ui.html#container_cli_ips).

## Configurando o StrongLoop Arc em uma máquina remota
{: #UsingSLA}

Depois de criar um contêiner único a partir da imagem **ibm-node-strong-pm**
no {{site.data.keyword.Bluemix_notm}}, configure o StrongLoop Arc
em sua máquina remota.
{:shortdesc}

Antes de iniciar, instale o Node.js e o gerenciador de pacote do Nó \(npm\) em sua máquina remota.

1.  Faça download e instale o StrongLoop inserindo o comando a seguir. O StrongLoop inclui todos os pacotes que são necessários para executar o Arc.

    ```
    npm install -g strongloop
    ```
    {: pre}

2.  Após StrongLoop ser instalado, execute StrongLoop Arc.

    ```
    slc arc
    ```
    {: pre}

3.  Registre-se ou efetue login no StrongLoop, quando solicitado.
4.  Inclua o StrongLoop Process Manager executado em seu contêiner na lista do Process Manager no
StrongLoop Arc.
    1.  No seletor de módulo do Arc, navegue para **Process Manager**.
    2.  Inclua o endereço IP público que você ligou ao contêiner e a porta pública 8701 no Arc
Process Manager.
    3.  Clique
em **Ativar**.

## Enviando por push um app Node.js para seu contêiner usando o StrongLoop Arc
{: #pushing_app_to_container}

Depois de conectar o StrongLoop Process Manager executado em seu contêiner ao
cliente StrongLoop Arc na máquina remota, construa e envie por push os apps Node.js para o {{site.data.keyword.Bluemix_notm}}.
{:shortdesc}

1.  No seletor de módulo do Arc, navegue para **Construir e implementar**.
2.  Construa seu app seguindo as instruções da interface com o usuário do Arc.

    **Importante:** assegure-se de que o app Node.js que você criou com StrongLoop Arc esteja atendendo na variável de ambiente PORT e na porta que você expôs quando criou o contêiner.

    Exemplo

    ```
    app.listen(process.env.PORT || 3001);
    ```
    {: pre}

3.  Implemente seu app seguindo as instruções da interface com o usuário do Arc. Selecione o Process Manager
que você configurou para implementar seu app no {{site.data.keyword.Bluemix_notm}}.
4.  Aguarde até que o processo de implementação seja concluído.
5.  Acesse seu aplicativo.
    1.  Abra um navegador da web.
    2.  Insira o endereço IP de seu contêiner e a porta no formato a seguir
<var class="keyword varname">IP_ADDRESS:PORT</var>.

        Exemplo

        ```
        192.0.2.26:3001
        ```
        {: pre}

    3.  Repita essas etapas para cada app implementado no {{site.data.keyword.Bluemix_notm}}.
6.  Use o rastreio, as métricas e o gerenciador de perfis do Arc para obter informações de desempenho de seus apps à medida que eles são
executados no {{site.data.keyword.Bluemix_notm}}. Consulte [Aplicativos do Nó Operacional](https://docs.strongloop.com/display/SLC/Operating+Node+applications) na documentação do StrongLoop
para obter mais informações.

## Referência do Dockerfile **ibm-node-strong-pm** 
{: #reference_dockerfile}

Esse Dockerfile foi usado para criar a imagem **ibm-node-strong-pm** no Catálogo {{site.data.keyword.Bluemix_notm}}. Estas informações destinam-se apenas à referência. Os outros arquivos que estão incluídos na versão construída desta imagem não são
fornecidos.

```
FROM registry.ng.bluemix.net/ibmnode:latest
RUN useradd -ms /bin/bash strong-pm \    
    && chown -R strong-pm:strong-pm /usr/local \    
    && su strong-pm -c "npm install -g strong-pm && npm cache clear"

# Set up some semblance of an environment
WORKDIR /home/strong-pm
ENV HOME=/home/strong-pm PORT=3000

# Run as non-privileged user inside container
USER strong-pm

# Expose strong-pm and application ports
EXPOSE 8701
EXPOSE 3001-3010

ENTRYPOINT ["/usr/local/bin/sl-pm", "--base", ".", "--listen", "8701"]
```
{: screen}


