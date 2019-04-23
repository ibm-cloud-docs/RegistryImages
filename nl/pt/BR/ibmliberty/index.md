---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-03"

keywords: IBM Cloud Container Registry, IBM Liberty, ibmliberty, container image, IBM WebSphere Application Server Liberty, liberty, public image

subcollection: RegistryImages

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:download: .download}

# Introdução à imagem `ibmliberty`
{: #ibmliberty}

As imagens do IBM® WebSphere® Application Server Liberty \(`ibmliberty`\) são fornecidas para o {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

É possível acessar as imagens que são fornecidas pela {{site.data.keyword.IBM_notm}} usando a linha de comandos. Consulte [Imagens públicas da IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Como ele Funciona
{: #ibmliberty_how_it_works}

É possível usar uma imagem `ibmliberty` como um pai para criar sua própria imagem e implementar seus próprios apps WAR, EAR ou OSGi baseados em Java em um contêiner do IBM WebSphere Application Server Liberty.
{:shortdesc}

## O que está incluído
{: #ibmliberty_whats_included}

Cada
imagem do Liberty fornece os pacotes de software a seguir.
{:shortdesc}

- IBM WebSphere Application Server for Developers Liberty
- IBM Java Runtime Environment 8.0

Os
recursos específicos do Liberty que são instalados na imagem dependem da tag que você
seleciona. A tabela a seguir mostra os recursos que são incluídos em cada uma das imagens `ibmliberty`. Para obter mais informações sobre cada recurso, consulte a [Visão geral dos recursos do Liberty no IBM Knowledge Center ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSAW57_liberty/com.ibm.websphere.wlp.nd.multiplatform.doc/ae/rwlp_feat.html).

|Marcação|Descrição|
|---|-----------|
|Todas as imagens `ibmliberty`|Todas as imagens `ibmliberty` incluem os recursos a
seguir. <ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|`ibmliberty:latest`|Esta imagem aponta para a imagem `ibmliberty:javaee7`.|
|` ibmliberty:microProfile `|Essa imagem contém os recursos que fornecem as capacidades especificadas pelo [MicroProfile ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://microprofile.io).|
|`ibmliberty:webProfile7`|Esta imagem inclui todos os recursos necessários para conformidade com o
perfil da web do Java EE7.|
|`ibmliberty:javaee7`|Esta imagem inclui todos os recursos da imagem
`ibmliberty:webProfile7`, mais os recursos que são requeridos para
conformidade com a plataforma integral do Java EE7.|
{: caption="Tabela 1. Os recursos que são incluídos em cada uma das imagens `ibmliberty`" caption-side="top"}

## Restrições de uso
{: #ibmliberty_usage}

A tabela a seguir mostra as restrições que se aplicam ao uso grátis da imagem
`ibmliberty` no {{site.data.keyword.Bluemix_notm}}.
{:shortdesc}

A precificação para a
imagem `ibmliberty` independe da precificação para os contêineres
usados no {{site.data.keyword.Bluemix_notm}}.
{:tip}

|Ambiente|Restrições de uso grátis|
|-----------|-----------------------|
|Desenvolvimento|Uso grátis **ilimitado** da imagem `ibmliberty`.|
|Produção|O uso grátis da imagem `ibmliberty` está limitado a um **máximo
de 2 GB de espaço de heap Java** em todas as instâncias do contêiner que executam a imagem. Por exemplo, é possível ter instâncias do Liberty com heap de 2 x 1 GB ou 4 x 512 MB gratuitamente.|
{: caption="Tabela 2. Precificação" caption-side="top"}

Para monitorar o uso de heap Java de suas instâncias do contêiner, consulte [Monitorando o uso de espaço de heap Java para um contêiner com a CLI](#ibmliberty_monitor_heap).

Revise os termos de uso para imagens certificadas pela IBM na seção Licença da [imagem websphere-liberty ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://hub.docker.com/_/websphere-liberty/) no Docker Hub.

## Iniciar
{: #ibmliberty_get_started}

Use uma das imagens `ibmliberty` grátis do catálogo do {{site.data.keyword.Bluemix_notm}} ou selecione sua própria
imagem licenciada para produção para criar um contêiner único ou um grupo de contêiner.
{:shortdesc}

Antes de iniciar, revise as [restrições de uso](#ibmliberty_usage) para as imagens
`ibmliberty`.
{: important}

1. No catálogo, selecione **Contêineres** > **IBM Cloud Container Registry** > **IBM Public Repositories** no painel lateral. Procure a imagem `ibmliberty` da qual construir seu contêiner. Se você criou sua própria imagem licenciada para produção e a implementou no {{site.data.keyword.Bluemix_notm}}, selecione essa imagem no catálogo. A página de criação do contêiner é aberta.
2. Selecione a versão da imagem `ibmliberty` que você deseja usar na
caixa suspensa **TAG/VERSÃO**.
3. Para obter mais informações sobre como criar contêineres por meio de imagens, configurar clusters e implementar apps em clusters, use os links a seguir:

    - [Construindo contêineres de imagens](/docs/containers?topic=containers-images#images)
    - [Introdução ao IBM Cloud Kubernetes Service](/docs/containers?topic=containers-getting-started#getting-started)
    - [Implementando apps em clusters](/docs/containers?topic=containers-app#app)

    A imagem `ibmliberty` requer que a porta 9080 seja exposta publicamente. Ao criar um contêiner por meio do Painel do {{site.data.keyword.Bluemix_notm}}, a porta é incluída no campo **Porta pública** por padrão. Se você criar um contêiner por meio da CLI, exponha a porta em seu comando `kubectl run` com a opção `--port=9080`.
    {:tip}

## Monitorando o uso de espaço de heap Java para um contêiner com a CLI
{: #ibmliberty_monitor_heap}

Depois de criar um contêiner por meio da imagem `ibmliberty`, será possível visualizar métricas de um pod específico e seus contêineres e revisar o uso de heap Java. O espaço de heap Java é a memória que está
disponível para o aplicativo Java durante o tempo de execução.
{:shortdesc}

1. Obtenha o nome do pod do qual você deseja visualizar métricas.
  
   ```
   kubectl get pods
   ```
   {: pre}

2. Veja as métricas de um determinado pod e seus contêineres.

   ```
   kubectl top pod POD_NAME -- contêineres
   ```
   {: pre}

3. Para revisar o uso de heap Java, é necessário acessar a estatística de memória **RSS**. Siga as diretrizes sobre como acessar um shell de um contêiner. Consulte [Obter um shell para um contêiner em execução ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo") ](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/). O uso de heap Java é exibido em kilobytes. Se seu uso de heap estiver abaixo de 2.097.152 kilobytes
(2 GB) em todas as instâncias, não será necessário comprar uma licença do WebSphere Application Server.

4. Ajuste o uso máximo de heap para sua instância do WebSphere Application Server. Consulte [Configurando argumentos genéricos da JVM no perfil Liberty do WebSphere Application Server V8.5 ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](http://www-01.ibm.com/support/docview.wss?uid=swg21596474) para obter mais informações.

## Obtendo uma licença do WebSphere Application Server
{: #ibmliberty_license}

As licenças do WebSphere Application Server são baseadas no número de Unidades de valor do processador \(PVUs\) que você precisa. PVU é uma unidade de medida para o licenciamento de software Middleware IBM. O número de PVUs indica o número de processadores \(núcleos\) que estão disponíveis para o software.
{:shortdesc}

Cada tamanho de contêiner no {{site.data.keyword.Bluemix_notm}} requer um número específico de titularidades da Unidade de Valor do Processador que devem estar disponíveis na licença
do WebSphere Application Server. Portanto, deve-se planejar seus contêineres do `ibmliberty`
antes de comprar a licença.

Para comprar uma licença do WebSphere Application Server, entre em contato com o [Serviço IBM ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://www.ibm.com/us-en/marketplace/application-server-on-cloud/purchase). Se você já tiver uma licença para o WebSphere
Application Server v8.5 ou mais recente, será possível usar qualquer PVU não usada de sua autorização existente
para a implementação de seu contêiner.

Se você descobrir que precisa de mais PVUs depois de comprar a licença, será possível aumentar a quantia entrando em contato com o [Serviço IBM ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://www.ibm.com/us-en/marketplace/application-server-on-cloud/purchase).

## Criando uma imagem `ibmliberty` licenciada para produção para ser usada com o
{{site.data.keyword.containershort_notm}}
{: #ibmliberty_prod_image}

Use a licença do WebSphere Application Server para criar uma imagem
`ibmliberty` licenciada para produção que pode ser usada com o {{site.data.keyword.containershort_notm}}. Escolha entre uma das tarefas
a seguir.
{:shortdesc}

- [Faça upgrade da imagem do Docker Hub para uma imagem de produção ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade).
- [Construa a sua própria imagem licenciada de produção ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://github.com/WASdev/ci.docker/tree/master/ga).

Depois de criar uma imagem licenciada para produção, [envie por push imagem para seu registro privado](/docs/services/Registry?topic=registry-getting-started#getting-started) para usá-la com o {{site.data.keyword.containershort_notm}}.

## Criando uma imagem a partir das imagens fornecidas
{: #ibmliberty_creating_image}

É possível usar uma das imagens `ibmliberty` como um pai
para criar uma imagem-filha que inclua seu próprio código de aplicativo. Customize o Dockerfile de amostra e construa sua imagem em seu
computador. Em seguida, é possível incluir sua imagem no registro de imagens privadas de sua organização e criar contêineres com ela.
{:shortdesc}

Antes de iniciar, considere as etapas a seguir.

- Construa seu código de app em um arquivo WAR, EAR ou OSGi.
- Copie o arquivo para o diretório, a partir do qual você deseja construir sua imagem.

Para criar uma imagem com seu código de app por meio da imagem `ibmliberty`, conclua as etapas a seguir:

1. Com um editor de texto, crie um arquivo nomeado `Dockerfile` e copie as
informações a seguir nele.

   ```
   FROM registry.bluemix.net/ibmliberty:<tag>
    COPY <app_name>.<file_extension> /config/dropins/
   ```
   {: pre}

    O diretório `/config` é um atalho para `/opt/ibm/wlp/usr/servers/defaultServer`.
    {: note}

2. Substitua  `<tag>` pela versão da imagem `ibmliberty` que inclui os recursos que seu app requer.

3. Substitua  `<app_name>` pelo nome de seu arquivo de app.

4. Substitua  `<file_extension>` por `.war`, `.ear` ou `.eba`.

5. Inclua quaisquer outras dependências para seu app no Dockerfile.

6. Construa e envie por push a imagem para seu registro de imagens privadas. Para obter mais informações, consulte [Introdução ao {{site.data.keyword.registrylong_notm}}](/docs/services/Registry?topic=registry-getting-started#getting-started).

Todas as imagens `ibmliberty` são configuradas para
gravar arquivos de log do Liberty no diretório `/logs` dentro do
contêiner. Todos os outros arquivos que são gravados pelo servidor Liberty são criados
no diretório `/opt/ibm/wlp/output/defaultServer`. É possível acessar
esses arquivos usando o atalho `/output`.
{:tip}

## Referência do Dockerfile `ibmliberty`
{: #ibmliberty_reference_dockerfile}

Esse Dockerfile ilustra como a imagem `ibmliberty:webProfile7` em {{site.data.keyword.Bluemix_notm}} é construída por meio das imagens websphere-liberty públicas no Docker Hub. Estas informações destinam-se apenas à referência.
{:shortdesc}

```
FROM websphere-liberty:webProfile7

# Update packages
RUN apt-get update &&\
    DEBIAN_FRONTEND=noninteractive apt-get upgrade -y &&\
    apt-get clean &&\
    rm -Rf /var/cache/*

# Set password length and expiry for compliance with vulnerability advisor
RUN sed -i 's/^PASS_MAX_DAYS.*/PASS_MAX_DAYS   90/' /etc/login.defs
RUN sed -i 's/sha512/sha512 minlen=8/' /etc/pam.d/common-password

# Add docs label
LABEL doc.url="/docs/services/RegistryImages/ibmliberty/index.html"
```
{: screen}
