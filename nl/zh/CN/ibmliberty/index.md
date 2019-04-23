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

# 开始使用 `ibmliberty` 映像
{: #ibmliberty}

为 {{site.data.keyword.containerlong_notm}} 提供了 IBM® WebSphere® Application Server Liberty \(`ibmliberty`\) 映像。
{:shortdesc}

您可以使用命令行来访问 {{site.data.keyword.IBM_notm}} 提供的映像，请参阅 [IBM 公共映像](/docs/services/Registry?topic=registry-public_images#public_images)。
{: tip}

## 工作原理
{: #ibmliberty_how_it_works}

您可以将 `ibmliberty` 映像用作父映像来创建自己的映像，并在 IBM WebSphere Application Server Liberty 容器中部署您自己的基于 Java 的 WAR、EAR 或 OSGi 应用程序。
{:shortdesc}

## 所含内容
{: #ibmliberty_whats_included}

每个 Liberty 映像都会提供以下软件包。
{:shortdesc}

- IBM WebSphere Application Server for Developers Liberty
- IBM Java Runtime Environment 8.0

映像中具体安装了哪些 Liberty 功能部件取决于您所选择的标记。下表显示了每个 `ibmliberty` 映像中包含的功能部件。有关每个功能部件的更多信息，请参阅 [IBM Knowledge Center 上的 Liberty 功能部件概述 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://www.ibm.com/support/knowledgecenter/SSAW57_liberty/com.ibm.websphere.wlp.nd.multiplatform.doc/ae/rwlp_feat.html)。

|标记|描述|
|---|-----------|
|所有 `ibmliberty` 映像|所有 `ibmliberty` 映像都包含以下功能部件。<ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|`ibmliberty:latest`|此映像指向 `ibmliberty:javaee7` 映像。|
|`ibmliberty:microProfile`|此映像包含的功能部件提供 [MicroProfile ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://microprofile.io) 指定的功能。|
|`ibmliberty:webProfile7`|此映像包含 Java EE7 Web Profile 合规性所要求的所有功能部件。|
|`ibmliberty:javaee7`|此映像包含 `ibmliberty:webProfile7` 映像中的所有功能部件，以及 Java EE7 Full Platform 合规性所要求的功能部件。|
{: caption="表 1. 每个“ibmliberty”映像中包含的功能部件" caption-side="top"}

## 使用限制
{: #ibmliberty_usage}

下表显示适用于 {{site.data.keyword.Bluemix_notm}} 中 `ibmliberty` 映像免费使用的限制。
{:shortdesc}

`ibmliberty` 映像的定价与 {{site.data.keyword.Bluemix_notm}} 中使用的容器的定价无关。
{:tip}

|环境|免费使用限制|
|-----------|-----------------------|
|开发|**无限**免费使用 `ibmliberty` 映像。|
|生产|`ibmliberty` 映像的免费使用限制为在运行映像的所有容器实例中，**最大为 2 GB 的 Java 堆空间**。例如，您可以免费拥有 2 x 1 GB 或 4 x 512 MB 的堆 Liberty 实例。|
{: caption="表 2. 定价" caption-side="top"}

要监视容器实例的 Java 堆使用量，请参阅[使用 CLI 监视容器的 Java 堆空间使用量](#ibmliberty_monitor_heap)。

在 Docker Hub 上 [websphere-liberty 映像 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://hub.docker.com/_/websphere-liberty/) 的“许可”部分中复查 IBM 认证映像的使用条款。

## 入门
{: #ibmliberty_get_started}

使用 {{site.data.keyword.Bluemix_notm}} 目录中的某个免费 `ibmliberty` 映像，或选择自己的生产许可映像，创建单个容器或容器组。
{:shortdesc}

开始之前，请查看 `ibmliberty` 映像的[使用限制](#ibmliberty_usage)。
{: important}

1. 在目录中，选择侧面板上的**容器** > **IBM Cloud Container Registry** > **IBM 公共存储库**。搜索 `ibmliberty` 映像以基于该映像构建容器。如果您已创建自己的生产许可映像并已将其部署至 {{site.data.keyword.Bluemix_notm}}，请从目录选择此映像。这将打开“容器创建”页面。
2. 从`标记/版本`下拉框中，选择要使用的 **ibmliberty** 映像的版本。
3. 有关基于映像构建容器、设置集群以及在集群中部署应用程序的更多信息，请使用以下链接：

    - [基于映像构建容器](/docs/containers?topic=containers-images#images)
    - [IBM Cloud Kubernetes 服务入门](/docs/containers?topic=containers-getting-started#getting-started)
    - [在集群中部署应用程序](/docs/containers?topic=containers-app#app)

    `ibmliberty` 映像要求以公共方式公开端口 9080。在 {{site.data.keyword.Bluemix_notm}}“仪表板”中创建容器时，缺省情况下会在**公共端口**字段中添加此端口。如果在 CLI 中创建容器，请在带 `--port=9080` 选项的 `kubectl ic run` 命令中公开此端口。
    {:tip}

## 使用 CLI 监视容器的 Java 堆空间使用
{: #ibmliberty_monitor_heap}

基于 `ibmliberty` 映像创建容器之后，您可以查看特定 pod 及其容器的度量值，并复查 Java 堆使用量。Java 堆空间是在运行时可用于 Java 应用程序的内存。
{:shortdesc}

1. 获取要查看其度量值的 pod 的名称。
  
   ```
    kubectl get pods
    ```
   {: pre}

2. 查看特定 pod 及其容器的度量值。

   ```
    kubectl top pod POD_NAME --containers
    ```
   {: pre}

3. 要复查 Java 堆使用量，您需要访问 **RSS** 内存统计信息。请按照有关如何访问容器 shell 的准则来执行操作。相关信息，请参阅 [Get a Shell to a Running Container ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/)。Java 堆使用量以 KB 显示。如果跨所有实例堆使用量低于 2097152 KB (2GB)，那么您无需购买 WebSphere Application Server 许可。

4. 针对 WebSphere Application Server 实例，调整最大堆使用量。有关更多信息，请参阅[在 WebSphere Application Server V8.5 Liberty 概要文件中设置通用 JVM 参数 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](http://www-01.ibm.com/support/docview.wss?uid=swg21596474)。

## 获取 WebSphere Application Server 许可
{: #ibmliberty_license}

WebSphere Application Server 许可基于您所需的处理器价值单元 \(PVU\) 数。PVU 是 IBM 中间件软件许可的计量单位。PVU 数表示软件可用的处理器（核心）数。
{:shortdesc}

{{site.data.keyword.Bluemix_notm}} 中的每一个容器大小都需要特定的 PVU 授权数，这些授权必须在 WebSphere
Application Server 许可中提供。因此，在购买许可之前，必须计划 `ibmliberty` 容器。


要购买 WebSphere Application Server 许可，请联系 [IBM 服务中心 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://www.ibm.com/us-en/marketplace/application-server-on-cloud/purchase)。如果您已拥有 WebSphere
Application Server V8.5 或更新版本的许可，那么可以使用现有授权中未使用的任何 PVU 来部署容器。


如果您在购买许可之后发现还需要更多的 PVU，那么可以联系 [IBM 服务中心 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://www.ibm.com/us-en/marketplace/application-server-on-cloud/purchase) 以增加数量。

## 创建与 {{site.data.keyword.containershort_notm}} 一起使用的生产许可 `ibmliberty` 映像
{: #ibmliberty_prod_image}

使用 WebSphere Application Server 许可，可以创建能够与 {{site.data.keyword.containershort_notm}} 一起使用的生产许可 `ibmliberty` 映像。选择以下其中一个任务。
{:shortdesc}

- [将 Docker Hub 中的映像升级到生产映像 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade)。
- [构建自己的生产许可映像 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://github.com/WASdev/ci.docker/tree/master/ga)。

创建生产许可映像之后，[将映像推送至专用注册表](/docs/services/Registry?topic=registry-getting-started#getting-started)，以与 {{site.data.keyword.containershort_notm}} 一起使用。

## 基于提供的映像创建映像
{: #ibmliberty_creating_image}

您可以将某个 `ibmliberty` 映像用作父映像来创建子映像，使其中包含您自己的应用程序代码。定制样本 Dockerfile，并在计算机上构建映像。然后，可以将映像添加到组织的专用映像注册表，并使用该映像来创建容器。
{:shortdesc}

开始之前，请考虑以下步骤。

- 将应用程序代码构建成 WAR、EAR 或 OSGi 文件。
- 将该文件复制到您要从中构建映像的目录内。

要使用应用程序代码基于 `ibmliberty` 映像创建映像，请完成以下步骤：

1. 使用文本编辑器，创建一个名为 `Dockerfile` 的文件，并将以下信息复制到该文件中。

   ```
   FROM registry.bluemix.net/ibmliberty:<tag>
   COPY <app_name>.<file_extension> /config/dropins/
   ```
   {: pre}

    `/config` 目录是 `/opt/ibm/wlp/usr/servers/defaultServer` 的快捷方式。
    {: note}

2. 将 `<tag>` 替换为包含应用程序所需功能部件的 `ibmliberty` 映像的版本。

3. 将 `<app_name>` 替换为应用程序文件的名称。

4. 将 `<file_extension>` 替换为 `.war`、`.ear` 或 `.eba`。

5. 将应用程序的其他所有依赖项添加到 Dockerfile 中。

6. 构建映像并将其推送到专用映像注册表。有关更多信息，请参阅 [{{site.data.keyword.registrylong_notm}} 入门](/docs/services/Registry?topic=registry-getting-started#getting-started)。

所有 `ibmliberty` 映像都已配置为将 Liberty 日志文件写入容器内的 `/logs` 目录中。由 Liberty 服务器写入的所有其他文件都会在 `/opt/ibm/wlp/output/defaultServer` 目录中进行创建。您可以使用快捷方式 `/output` 来访问这些文件。
{:tip}

## `ibmliberty` Dockerfile 参考
{: #ibmliberty_reference_dockerfile}

以下 Dockerfile 说明了 {{site.data.keyword.Bluemix_notm}} 中的 `ibmliberty:webProfile7` 映像是如何基于 Docker Hub 上的公共 websphere-liberty 映像进行构建的。这些信息仅供参考。
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
