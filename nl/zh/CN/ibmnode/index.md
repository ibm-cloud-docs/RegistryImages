---

copyright:
  years: 2017
lastupdated: "2017-11-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# 开始使用 **ibmnode** 映像
{: #getting_started_node}

为 {{site.data.keyword.containerlong_notm}} 提供公共 {{site.data.keyword.IBM}} 节点 (**ibmnode**) 映像。
{:shortdesc}

**注：**{{site.data.keyword.IBM_notm}} Node (**ibmnode**) 映像不包括样本 Node.js 应用程序。基于此映像部署容器时，容器构建后将立即关闭，因为不再存在运行中的进程。为了避免这种情况，请将 {{site.data.keyword.IBM_notm}} Node (**ibmnode**) 映像用作父映像来创建您自己的映像，然后可以在自己的映像中添加您自己的 Node.js 应用程序代码。有关更多信息，请参阅[通过此提供的映像创建映像](#creating_image)。

# 工作原理 {: #how_it_works}

将 {{site.data.keyword.IBM_notm}} Node (**ibmnode**) 映像用作父映像，并搭配您自己的 Node.js 应用程序代码，构建您自己的映像。
{:shortdesc}

**注：**在开始之前，请在 [websphere-liberty Docker 库 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://github.com/docker-library/docs/tree/master/websphere-liberty){: new_window} 的“用法”部分中查看经 IBM 认证的映像的使用条款。

# 所含内容 {: #included}

* Ubuntu 14.04
* Python、Git 和 build-essentials apt-get 软件包
* {{site.data.keyword.IBM_notm}} Node SDK

    **注：**ibmnode 映像可用于不同的标记，以便您可以运行不同版本的 IBM Node SDK。

    <table>
    <caption> 表 1. 不同 SDK 版本的标记</caption>
      <tr>
        <th> 标记</th>
        <th> 描述</th>
      </tr>
      <tr>
        <td> ibmnode:latest </td>
        <td> IBM Node 的最新版本（相当于使用 ibmnode:v4）</td>
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


# 基于此提供的映像创建映像 {: #creating_image}

可将 **ibmnode** 映像用作父映像以创建子映像，并可在子映像中包含您自己的应用程序代码。将样本 `Hello World` 应用程序容器与 **ibmnode** 和 Express Node 模块配合使用，在您的计算机上构建一个映像。将该映像添加到您组织的专用 {{site.data.keyword.registrylong_notm}}，并使用该映像来创建容器。然后，可以使用 Hello World 样本中的 Dockerfile 为您自己的应用程序创建另一个映像。
{:shortdesc}

开始之前，请考虑以下步骤。

* 必须在本地安装以下工具：
  * [用于 Cloud Foundry 和 Docker CLI 的 {{site.data.keyword.registrylong_notm}} 插件](/docs/containers/container_cli_cfic_install.html)
  * [Node.js 源代码 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://nodejs.org/en/download/){: new_window}
  * [npm ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://github.com/npm/npm){: new_window}
* [您必须了解组织专用 {{site.data.keyword.registryshort_notm}} 的名称空间。](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__namespace)

    示例

    registry.DomainName/<var class="keyword varname">namespace</var>

* [您必须有一个 IP 地址。](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__ip_request)

创建基于 **ibmnode** 映像并包含 Hello World 应用程序的映像。
1.  下载 [hellonode.zip ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](ftp://public.dhe.ibm.com/cloud/bluemix/containers/hellonode.zip){: new_window} 并将其解压缩。
1.  使用文本编辑器打开 Dockerfile，然后在 FROM 指令中，针对要使用的区域更新注册表 URL。

    <ul>
    <li>美国南部

        ```
		registry.ng.bluemix.net/ibmnode
		```
		{: codeblock}

    </li>
    <li>英国

        ```
		registry.eu-gb.bluemix.net/ibmnode
		```
		{: codeblock}

      </li>
    </ul>

1.  在 CLI 中，浏览到 **app** 目录中。
1.  在 **app** 目录中，运行以下命令。
1.  创建 package.json 文件。

        ```
        npm init
        ```
  {: pre}


    **提示：** 按 Return 键以接受每个提示的缺省值。

2.  安装 Express，这是 Node.js 的框架。package.json 会使用 dependencies 部分中 Express 的版本信息进行更新。

```
        npm install express -save
        ```
  {: pre}


1.  浏览回 **hellonode** 目录。
1.  构建映像，然后将其推送到专用 {{site.data.keyword.registryshort_notm}}。*

    ```
    bx ic build -t registry.<DomainName>/<namespace>/hellonode .
    ```
    {: pre}

    **提示：** 运行“bx ic namespace get”以检索名称空间信息。将 _registry.DomainName_ 设置为 Dockerfile 中先前使用的相同区域。

    **注：** \* 在此命令中，您可以在 [登录到 {{site.data.keyword.containershort_notm}}](/docs/containers/container_cli_cfic_install.html#container_cli_login)时将“bx ic”替换为“docker”，并将环境变量设置为使用本机 Docker 命令。您可以在本主题中用星号 (*) 标记的所有步骤中使用本机 Docker 命令。
1.  基于您的映像创建一个容器。*

    ```
    bx ic run -p 3000 --name helloworld registry.<DomainName>/<namespace>/hellonode
    ```
    {: pre}

1.  将公共 IP 地址绑定到容器。

    ```
    bx ic ip bind <IPaddress> helloworld
    ```
    {: pre}

    ** 提示 **：您可以通过运行“bx ic ip list”来查看公共 IP 地址。要请求新的 IP 地址，请运行“bx ic ip request”。

1. 在浏览器中，通过打开 **<var class="varname">IPaddress</var>:3000** 来测试应用程序。这将显示 Hello World! 消息。

现在，hellonode 映像已创建完毕，您可以修改 Dockerfile，使其包含您自己的应用程序，然后为该应用程序创建另一个映像。

1.  对于 ADD 指令，添加您自己的应用程序代码。
1.  对于 EXPOSE 指令，更新端口信息。
1.  对于 CMD 指令，创建一个用于启动您应用程序的命令。


# **ibmnode** Dockerfile reference {: #reference_dockerfile}

此 Dockerfile 用于在 {{site.data.keyword.Bluemix_notm}} 目录中创建 **ibmnode** 映像。这些信息仅供参考。此映像构建版本中包含的其他文件均未提供。
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
