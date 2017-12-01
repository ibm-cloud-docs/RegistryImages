---

copyright:
  years: 2017
lastupdated: "2017-10-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Introdução à imagem **ibmnode** {: #getting_started_node}

A imagem pública Nó {{site.data.keyword.IBM}} (**ibmnode**) é fornecida para {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

**Nota:** a imagem Nó {{site.data.keyword.IBM_notm}} (**ibmnode**) não inclui um app Node.js de amostra. Ao implementar um contêiner a partir dessa imagem, seu contêiner será
encerrado imediatamente após ele ser construído, já que nenhum processo de execução longa
existe. Para evitar isso, use a imagem Nó {{site.data.keyword.IBM_notm}} (**ibmnode**) como um pai e crie sua própria imagem na qual é possível incluir seu próprio código de app do Node.js. Para obter mais informações, consulte [Criando uma imagem por meio desta imagem fornecida](#creating_image).

# Como ele Funciona {: #how_it_works}

Use a imagem Nó {{site.data.keyword.IBM_notm}} (**ibmnode**) como um pai para construir sua própria imagem com seu próprio código de app do Node.js.
{:shortdesc}

**Nota:** antes de iniciar, revise os termos de uso para imagens certificadas pela IBM na seção Uso da [biblioteca websphere-liberty Docker ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo:")](https://github.com/docker-library/docs/tree/master/websphere-liberty){: new_window}.

# O que está incluído {: #included}

* Ubuntu 14.04
* Python, Git e pacotes apt-get essenciais para construção
* {{site.data.keyword.IBM_notm}} Node SDK

    **Nota:** a imagem ibmnode está disponível com diferentes tags para que seja possível executar diferentes versões do IBM Node SDK.

    <table>
    <caption> Tabela 1. Tags para diferentes versões do SDK</caption>
      <tr>
        <th> Marcação </th>
        <th> Descrição </th>
      </tr>
      <tr>
        <td> ibmnode:latest </td>
        <td> A versão mais recente do IBM Node, equivalente a usar ibmnode:v4 </td>
      </tr>
      <tr>
        <td> ibmnode:v4 </td>
        <td> IBM Node 4.4.0 </td>
      </tr>
      <tr>
        <td> ibmnode:v1.2 </td>
        <td> IBM Node 1.2.0.8 (0.12.10) </td>
      </tr>
      <tr>
        <td> ibmnode:v1.1 </td>
        <td> IBM Node 1.1.0.18 (0.10.43) </td>
      </tr>
    </table>


# Criando uma imagem a partir desta imagem fornecida {: #creating_image}

É possível usar a imagem **ibmnode** como um pai para criar uma
imagem-filha que inclua seu próprio código de aplicativo. Use o contêiner de app `Hello World` de amostra
com **ibmnode** e o módulo Express Node para construir uma imagem em seu computador. Inclua a imagem no {{site.data.keyword.registrylong_notm}} privado da sua organização e crie um contêiner com ela. Em seguida, é possível usar o Dockerfile da amostra Hello World para criar outra imagem com seu
próprio aplicativo.
{:shortdesc}

Antes de iniciar, considere as etapas a seguir.

* Deve-se instalar as ferramentas a seguir localmente:
  * Plug-in do [{{site.data.keyword.registrylong_notm}} para o Cloud Foundry e
o Docker CLI](/docs/containers/container_cli_cfic_install.html)
  * [Código-fonte do Node.js ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://nodejs.org/en/download/){: new_window}
  * [npm ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://github.com/npm/npm){: new_window}
* [Deve-se conhecer o namespace do {{site.data.keyword.registryshort_notm}} privado da sua organização.](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__namespace)

    Exemplo

    registry.DomainName/<var class="keyword varname">namespace</var>

* [Deve-se ter um endereço IP.](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__ip_request)

Crie uma imagem que seja baseada na imagem **ibmnode** e inclua um app Hello World.
1.  Faça download do [hellonode.zip ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](ftp://public.dhe.ibm.com/cloud/bluemix/containers/hellonode.zip){: new_window} e extraia-o.
1.  Abra o Dockerfile com um editor de texto e, na instrução FROM, atualize a URL do registro para
a região que você deseja usar.

    <ul>
    <li>Sul dos EUA

        ```
		registry.ng.bluemix.net/ibmnode
		```
		{: codeblock}

    </li>
    <li>United Kingdom

        ```
		registry.eu-gb.bluemix.net/ibmnode
		```
		{: codeblock}

      </li>
    </ul>

1.  Na CLI, navegue até o diretório **app**.
1.  No diretório **app**, execute os comandos a seguir.
    1.  Crie um arquivo package.json.

        ```
        npm init
        ```
        {: pre}

        **Dica:** pressione retornar para aceitar o valor padrão para cada um dos prompts.

    2.  Instale o Express, uma estrutura para o Node.js. O package.json é atualizado com as informações da versão para o Express na seção de dependências.

        ```
        npm install express -save
        ```
        {: pre}

1.  Navegue de volta para o diretório **hellonode**.
1.  Construa sua imagem e envie-a por push para o seu {{site.data.keyword.registryshort_notm}} privado.*
    ```
    bx ic build -t registry.<DomainName>/<namespace>/hellonode .
    ```
    {: pre}

    **Dica:** execute `bx ic namespace get` para recuperar suas informações de namespace. Configure o _registry.DomainName_ para a mesma região que você usou anteriormente no Dockerfile.

    **Nota:** \*neste comando, é possível substituir `bx ic` por `docker` quando estiver [conectado a {{site.data.keyword.containershort_notm}}](/docs/containers/container_cli_cfic_install.html#container_cli_login) e configurar suas variáveis de ambiente para usar comandos do Docker nativos. É possível usar comandos nativos do Docker em todas as etapas que estão marcadas com um asterisco (*) neste tópico.

1.  Crie um contêiner por meio de sua imagem.*

    ```
    bx ic run -p 3000 --name helloworld registry.<DomainName>/<namespace>/hellonode
    ```
    {: pre}

1.  Ligue um endereço IP público ao contêiner.

    ```
    bx ic ip bind <IPaddress> helloworld
    ```
    {: pre}

    **Dica:** é possível ver seus endereços IP públicos executando `bx ic ip list`. Para solicitar um novo, execute `bx ic ip request`.
1. Em um navegador, teste o app abrindo **<var class="varname">IPaddress</var>:3000**. A mensagem Hello World! será exibida.
Agora que você criou a imagem hellonode, é possível modificar o Dockerfile para incluir seu
próprio app e criar outra imagem para esse app.

1.  Para a instrução ADD, inclua seu próprio código do aplicativo.
1.  Para a instrução EXPOSE, atualize as informações da porta.
1.  Para a instrução CMD, crie um comando para iniciar seu app.


# **ibmnode** Dockerfile reference {: #reference_dockerfile}

Esse Dockerfile foi usado para criar a imagem **ibmnode** no catálogo do {{site.data.keyword.Bluemix_notm}}. Estas informações destinam-se apenas à referência. Os outros arquivos que estão
incluídos na versão construída desta imagem não são fornecidos.
{:shortdesc}

```
FROM ubuntu:14.04

# Update base OS
RUN apt-get update &&\
DEBIAN_FRONTEND=noninteractive apt-get -y install \
git \
build-essential \
python && \
DEBIAN_FRONTEND=noninteractive apt-get upgrade -y &&\
apt-get clean &&\
rm -Rf /var/cache/*

# Enhance default password rules
RUN sed -i 's/^PASS_MAX_DAYS.*/PASS_MAX_DAYS 90/' /etc/login.defs
RUN sed -i 's/sha512/sha512 minlen=8/' /etc/pam.d/common-password

# Install Node SDK
ONBUILD COPY ./prereq/node.tar.gz /tmp/prereq/
ONBUILD RUN cd /usr/local &&\
tar --strip-components 1 -xzf /tmp/prereq/node.tar.gz &&\
rm -rf /tmp/prereq

#Documentation path for URL
LABEL doc.url="/docs/services/RegistryImages/ibmnode/index.html"
```
{: screen}
