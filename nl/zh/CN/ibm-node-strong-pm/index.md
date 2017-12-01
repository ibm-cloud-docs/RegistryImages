---

copyright:
  years: 2015, 2017
lastupdated: "2017-10-30"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}

# 开始使用 ibm-node-strong-pm 映像
{: #getting_started_node_strong}

为 {{site.data.keyword.containerlong_notm}} 提供了 {{site.data.keyword.IBM}} Node with StrongLoop \(**ibm-node-strong-pm**\) 映像。
{:shortdesc}

## 工作原理 
{: #how_it_works}

使用 **ibm-node-strong-pm** 映像可创建单个容器，用于部署正在运行的 [StrongLoop Process Manager](https://www.strongloop.com) 实例。通过 Strongloop Process Manager，您可以在 {{site.data.keyword.IBM_notm}} Cloud 中，部署、管理和监视在远程机器上实施的 Node.js 应用程序。
{:shortdesc}

远程机器必须已安装 StrongLoop Arc 的实例。StrongLoop Arc 是 StrongLoop 平台的图形用户界面，包含用于对 Node.js 应用程序进行构建、概要分析和监视的多个工具。在使用 StrongLoop Arc 构建应用程序之后，您将其推送和部署至 {{site.data.keyword.Bluemix_notm}} 中运行 StrongLoop Process Manager 的容器。要在远程机器和容器之间建立连接，您必须将公共 IP 地址绑定到容器，并将此 IP 地址设置为 StrongLoop Arc 中的 StrongLoop Process Manager 服务器。当通过 StrongLoop Process Manager 部署应用程序时，您可以监视 StrongLoop Process Manager 中应用程序的度量和性能。
要更新或扩展应用程序，您必须使用 StrongLoop Arc 对其进行更改，然后将更改推送至容器。


**注：**不支持将 **ibm-node-strong-pm** 与 {{site.data.keyword.Bluemix_notm}} 中的容器组一起使用。

## 所含内容
{: #whats_included}

-   **ibmnode:latest** 映像中包含的所有软件包。请参阅“开始使用 {{site.data.keyword.Bluemix_notm}} 的 **ibmnode** 映像”主题中的[所含内容](/docs/services/RegistryImages/ibmnode/index.html#getting_started_node)。
-   StrongLoop Process Manager

## 入门
{: #how_to_get_started}

可以在 {{site.data.keyword.Bluemix_notm}}“目录”中使用 **ibm-node-strong-pm** 映像来尝试使用 {{site.data.keyword.containershort_notm}} 中的单个容器。通过运行一个 StrongLoop Process Manager 实例，即可启动此映像。一旦容器可用，即可访问该实例。使用连接到该 Process Manager 的 StrongLoop Arc 实例，可以推送 Node.js 应用程序并对其进行访问。
{:shortdesc}

**注：**在开始之前，请在 [websphere-liberty Docker 库](https://github.com/docker-library/docs/tree/master/websphere-liberty)的“用法”部分中查看经 {{site.data.keyword.IBM_notm}} 认证的映像的使用条款。

1.  在目录中，选择**容器**，然后选择 **ibm-node-strong-pm** 映像以基于此映像构建容器。
2.  创建单个容器。
    -   在**大小**字段中，选择至少 1 GB 内存的容器大小（**小**）。

    -   在**公共端口**字段中，输入端口 8701 和 3001。当在 {{site.data.keyword.Bluemix_notm}} 中部署容器时，会自动公开这些端口，这是将公共 IP 地址绑定到容器的一个先决条件。
端口 8701 用于访问容器中的 StrongLoop Process Manager。
端口 3001 是将应用程序部署到 StrongLoop Process Manager 之后应用程序侦听的端口。

        **注：**要将其他应用程序部署到 StrongLoop Process Manager，请公开更多端口。端口必须连续，从 3002 开始。例如，3002、3003、3004，以此类推。


    -   有关进一步的信息，请参阅[使用 {{site.data.keyword.Bluemix_notm}} 仪表板创建单个容器](/docs/containers/container_single_ui.html#gui)。
3.  将公共 IP 地址绑定到容器。有关更多信息，请参阅[请求并绑定 IP 地址](/docs/containers/container_single_ui.html#container_cli_ips)。

## 在远程机器上设置 StrongLoop Arc
{: #UsingSLA}

通过 {{site.data.keyword.Bluemix_notm}} 中的 **ibm-node-strong-pm** 映像创建单个容器之后，在远程机器上设置 StrongLoop Arc。
{:shortdesc}

开始之前，请在远程机器上安装 Node.js 和 Node 软件包管理器 \(npm\)。

1.  通过输入以下命令来下载并安装 StrongLoop。StrongLoop 包含运行 Arc 所需的所有包。

    ```
    npm install -g strongloop
    ```
    {: pre}

2.  安装 StrongLoop 后，运行 StrongLoop Arc。

    ```
    slc arc
    ```
    {: pre}

3.  系统提示时，注册或登录到 StrongLoop。
4.  将容器中运行的 StrongLoop Process Manager 添加到 StrongLoop Arc 的 Process Manager 列表中。
    1.  在 Arc 模块选择器中，浏览至 **Process Manager**。
    2.  将您绑定到容器的公共 IP 地址以及公共端口 8701 添加到 Arc Process Manager。
    3.  单击**激活**。

## 使用 StrongLoop Arc 将 Node.js 应用程序推送到容器
{: #pushing_app_to_container}

将容器中运行的 StrongLoop Process Manager 与远程机器上的 StrongLoop Arc 客户端连接之后，构建 Node.js 应用程序并将其推送至 {{site.data.keyword.Bluemix_notm}}。
{:shortdesc}

1.  在 Arc 模块选择器中，浏览至**构建和部署**。
2.  通过遵循 Arc 用户界面的指示来构建应用程序。

    **重要事项：**确保使用 StrongLoop Arc 创建的 Node.js 应用程序侦听的是环境变量 PORT 和创建容器时公开的端口。

    示例

    ```
    app.listen(process.env.PORT || 3001);
    ```
    {: pre}

3.  通过遵循 Arc 用户界面的指示来部署应用程序。选择所配置的 Process Manager，以将应用程序部署到 {{site.data.keyword.Bluemix_notm}}。
4.  等待部署过程完成。
5.  访问应用程序。
    1.  打开 Web 浏览器。
    2.  以下列格式 <var class="keyword varname">IP_ADDRESS:PORT</var> 输入容器的 IP 地址和端口。

        示例

        ```
        192.0.2.26:3001
        ```
        {: pre}

    3.  针对您部署至 {{site.data.keyword.Bluemix_notm}} 的每一个应用程序，重复上述步骤。
6.  使用 Arc 的跟踪、度量和概要分析程序来获取有关应用程序在 {{site.data.keyword.Bluemix_notm}} 上运行时的性能信息。请参阅 StrongLoop 文档中的 [Operating Node application](https://docs.strongloop.com/display/SLC/Operating+Node+applications) 以获取更多信息。

## **ibm-node-strong-pm** Dockerfile 参考 
{: #reference_dockerfile}

此 Dockerfile 用于在 {{site.data.keyword.Bluemix_notm}}“目录”中创建 **ibm-node-strong-pm** 映像。这些信息仅供参考。此映像构建版本中包含的其他文件均未提供。

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


