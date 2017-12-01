---

copyright:
  years: 2016, 2017
lastupdated: "2017-10-25"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# 开始使用 *ibm-integration-bus* 映像
{: #iib}

为 {{site.data.keyword.containerlong}} 提供了 **ibm-integration-bus** 映像。此映像包含 IBM Integration Bus for Developers 版本。
{:shortdesc}


## 工作原理
{: #how_it_works}

IBM Integration Bus for Developers 包含您开始开发自己的集成解决方案所需的一切内容。
{:shortdesc}

创建集成解决方案后，可以使用 **ibm-integration-bus** 映像在 {{site.data.keyword.Bluemix_notm}} 中供应单个容器。随后，可以使用 Web UI 或通过终端将集成解决方案部署到此容器中。

**注**：**ibm-integration-bus** 映像只能用于开发和单元测试。此映像还可用于探索产品，学习教程以及评估 IBM Integration Bus 可对您组织做出的贡献。


## 所含内容
{: #whats_included}

此 IBM Integration Bus 映像包含 IBM Integration Bus for Developers V10（也称为 Developer Edition）的软件包，这是产品的全功能版本，可用于开发和单元测试。您可以免费下载此版本，并可根据许可证条款免费使用。
{:shortdesc}

IBM Integration Bus for Developers V10.0 可在 Windows™ 64 位操作系统和 Linux™ on x86-64 操作系统上使用。所有产品先决条件均包含在下载包中。

有关 IBM Integration Bus for Developers 中随附的功能的信息，请参阅 [IBM Knowledge Center 中的 IBM Integration Bus ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.msgbroker.helphome.doc/help_home_msgbroker.htm){: new_window}。

可以从 [IBM Integration Bus for Developers 下载页面 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://www.ibm.com/marketing/iwm/iwm/web/pick.do?source=swg-wmbfd){: new_window} 访问 Developer Edition。


## 使用限制
{: #usage}

下表显示适用于 {{site.data.keyword.Bluemix_notm}} 中 **ibm-integration-bus** 映像免费使用的限制。
{:shortdesc}

**注**：**ibm-integration-bus** 映像的定价与 {{site.data.keyword.Bluemix_notm}} 中使用的容器的定价无关。

|环境|免费使用限制|
|-----------|-----------------------|
|开发|**无限**免费使用 **ibm-integration-bus** 映像，以创建单个容器用于开发和单元测试。|
|生产|Developer Edition 限制为每秒处理 1 个事务，并且不支持用于生产环境。|
{: caption="表 1. 免费使用 ibm-integration-bus 映像的限制" caption-side="top"}


## 许可证
{: #license}

关于许可证的信息。
{:shortdesc}

*   Dockerfile 和关联的脚本根据 [Apache License 2.0 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](http://www.apache.org/licenses/LICENSE-2.0.html){: new_window} 授予许可。
*   [IBMIntegration Bus for Developers V10.0 的许可证信息 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](http://www.ibm.com/software/sla/sladb.nsf/displaylis/1BD32B141C9FDAE38525802C0055A956?OpenDocument){: new_window}。

    **注**：许可证不允许进一步分发。映像中 IBM Integration Bus 的条款将用途限制为开发和单元测试。


## 入门
{: #get_started}

使用 {{site.data.keyword.Bluemix_notm}} 目录中的 **ibm-integration-bus** 映像，或选择自己的生产许可映像来创建单个容器。
{:shortdesc}

**重要事项**：开始之前，请查看 **ibm-integration-bus** 映像的[使用限制](#usage)。

**注**：您可以在所有用星号 (\*) 标记的步骤中使用本机 Docker 命令。

   完成以下步骤以在容器中运行 IBM Integration Bus：

1.  [在 {{site.data.keyword.Bluemix_notm}} UI](#provision) 中基于 ibm-integration-bus 映像供应容器
2.  [验证容器和 IBM Integration Bus 安装](#validate)
3.  [创建和部署流](#createdeployflow)
4.  [监视容器内部的 IBM Integration Bus 日志](#monitor)
5.  [管理在容器中运行的 IBM Integration Bus 资源](#manage)


## 供应基于 **ibm-integration-bus** 映像的容器
{: #provision}

在 {{site.data.keyword.Bluemix_notm}} 中，基于 {{site.data.keyword.IBM_notm}} 提供的 **ibm-integration-bus** 映像供应 Docker 容器。
{:shortdesc}

  完成以下步骤：

1.  登录到 {{site.data.keyword.Bluemix_notm}}。
2.  在目录中，选择**容器**，然后选择 **ibm-integration-bus** 映像以基于此映像构建容器。
3.  选择**单个**以创建单个实例容器，此容器可用于开发和测试用途。
4.  输入容器的名称；例如，`iib`。
5.  选择容器的大小。
6.  在**公共 IP 地址**字段中，选择*绑定公共 IP*。
7.  在**公共端口**字段中，指定 `4414, 7800`。
8.  展开**高级**选项，然后单击**添加新环境变量**。
9.  添加以下环境变量：

    <ul>
      <li>LICENSE，值为 <i>accept</i>
      <li>NODENAME，值为 &lt;MYNODE&gt;（其中，&lt;MYNODE&gt; 是所选节点名）</li>
      <li>LOG_LOCATIONS，值为 <i>/var/log/syslog</i>
    </ul>

    **注**：如果未指定 LICENSE 和 NODENAME 属性的值，那么将创建容器，但不会启动该容器。

LOG_LOCATIONS 属性指定容器内部要通过 {{site.data.keyword.Bluemix_notm}} UI 公开的日志。
10. 单击**创建**，然后等待容器开始运行。


## 验证容器和 IBM Integration Bus 安装
{: #validate}

在 {{site.data.keyword.Bluemix_notm}} 中部署了运行 IBM Integration Bus 的容器后，请检查容器的状态，并验证 IBM Integration Bus 安装。
{:shortdesc}

  完成以下步骤以在容器中验证 IBM Integration Bus 的设置和配置：
  
1.  设置 {{site.data.keyword.containershort_notm}} CLI，如[设置 {{site.data.keyword.containershort_notm}} 插件 \(`bx ic`\)，以使用本机 Docker CLI 中所述](/docs/containers/container_cli_cfic_install.html)。

2.  通过终端登录到 {{site.data.keyword.Bluemix_notm}}。运行以下命令：

    <ol>
       <li>
          <pre class="codeblock">bx login</pre>
       </li>
       <li>
          <pre class="codeblock">bx ic init</pre>
       </li>
    </ol>

3.  检查容器的状态。\*

    ```
    bx ic ps
    ```
    {: pre}

    **注**：在此命令中，您可以在[登录到 {{site.data.keyword.containershort_notm}}](/docs/containers/container_cli_cfic_install.html#container_cli_login) 时，将 **bx ic** 替换为 **docker**，并设置环境变量以使用本机 Docker 命令。您可以在所有用星号 (\*) 标记的步骤中使用本机 Docker 命令。

    ```
    $ bx ic ps
    CONTAINER ID        IMAGE                                                       COMMAND             CREATED             STATUS                 PORTS                                                        NAMES
    46d9ec1e-952        registry.ng.bluemix.net/ibm-integration-bus:latest   ""                  2 minutes ago       Running a minute ago   xxx.xxx.xxx.xxx:4414->4414/tcp, xxx.xxx.xxx.xxx:7800->7800/tcp   iib
    ```
    {: screen}

4.  将 Bash 会话附加到容器。\*

    ```
    bx ic exec -it <container_name> /bin/bash
    ```
    {: pre}

    其中，*container_name* 是容器的名称。

    例如：

    ```
    $ bx ic exec -it iib /bin/bash
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

5.  通过运行 **mqsiprofile** 命令来初始化环境，如[命令环境：Linux 和 UNIX™ 系统 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](http://www.ibm.com/support/knowledgecenter/SSMKHH_9.0.0/com.ibm.etools.mft.doc/an26230_.htm){: new_window} 中所述。

    用于初始化环境的命令位于以下目录中：`/opt/ibm/iib-10.0.0.6/server/bin/`

    例如：

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

6.  运行 **mqsilist** 命令以显示节点的状态，列出的状态应该为 `running`。例如：

    ```
    (IIB_10:)iibuser@instance-0098858e:/$ mqsilist
    BIP1325I: Integration node 'MYNODE' with administration URI 'http://instance-0098858e:4414' is running.
    BIP8071I: Successful command completion.
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

现在，容器正在运行，并且您可以使用支持的任一方法来部署集成解决方案。


## 监视容器内部的 IBM Integration Bus 日志
{: #monitor}

通过 {{site.data.keyword.Bluemix_notm}} UI 或命令行监视日志。
{:shortdesc}

开始之前，将 {{site.data.keyword.containershort_notm}} CLI 配置为通过命令行监视日志。有关更多信息，请参阅[设置 {{site.data.keyword.containershort_notm}} 插件 (`bx ic`)](/docs/containers/container_cli_cfic_install.html)。

**ibm-integration-bus** 映像配置为将 IBM Integration Bus 消息输出到容器内部的 `/var/log/syslog` 中。

选择以下其中一种方式来监视 IBM Integration Bus 日志：

*  如果供应容器时配置了环境变量 LOG_LOCATIONS，那么可以在 {{site.data.keyword.Bluemix_notm}} UI 中监视日志。

    完成以下步骤以访问日志：

    <ol>
       <li>在容器仪表板中，选择要监视的容器。</li>
       <li>选择<b>监视和日志记录</b>。</li>
       <li>选择<b>日志记录</b>。仪表板将列出日志条目。</li>
       <li>要定制日志视图，请选择“日志记录”页面中的<b>高级视图</b>以打开 Kibana。有关更多信息，请参阅
           <a href="https://console.bluemix.net/docs/containers/container_planning.html#container_planning_monlog" target="_blank">监视和日志记录</a>以开始。
       </li>
    </ol>

*  通过终端，使用 Docker 或 {{site.data.keyword.containerlong_notm}} CLI 来监视消息。完成以下步骤：

    <ol>
       <li>
       配置终端。

        <ul>
        <li>要使用 {{site.data.keyword.containerlong_notm}} CLI 来管理 {{site.data.keyword.Bluemix_notm}} 中的容器，同时仍直接使用 Docker CLI 管理本地 Docker 主机，请完成以下步骤来设置环境：
           <ol>
             <li>
               <pre class="codeblock">bx login</pre>
             </li>
             <li>
               <pre class="codeblock">bx ic init</pre>
             </li>
           </ol>
        </li>
        <li>要使用 Docker CLI 来管理 {{site.data.keyword.Bluemix_notm}} 中的容器，请完成以下步骤：
          <ol>
            <li>
              <pre class="codeblock">bx login</pre>
            </li>
            <li>
              <pre class="codeblock">bx ic init</pre>
               复制为以下环境变量提供的值：&lt;DOCKER_HOST&gt;、&lt;DOCKER_CERT_PATH&gt; 和 &lt;DOCKER_TLS_VERIFY&gt;。
            </li>
            <li>
            通过设置以下变量来覆盖本地 Docker 环境，以连接到 {{site.data.keyword.containerlong_notm}}：
               <ul>
                 <li><pre class="codeblock">export DOCKER_HOST=&lt;your_host_value&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;your_cert_path_value&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
               </ul>
                <b>注</b>：此选项仅支持某些 Docker 命令。
            </li>
          </ol>
        </li>
      </ul>
      </li>
      <li>
访问日志。选择以下某个选项：

      <ul>
         <li>如果终端配置为运行 {{site.data.keyword.containerlong_notm}} CLI 命令，请运行以下命令：

            <pre class="codeblock">bx ic exec &lt;container_id&gt; tail -f /var/log/syslog</pre>

            其中，*container_id* 是容器的名称。

            例如：

            <pre class="screen">bx ic exec iib tail -f /var/log/syslog</pre>

          </li>
          <li>
           如果终端配置为运行 Docker CLI 命令，请运行以下命令：

            <pre class="codeblock">docker exec &lt;container_id&gt; tail -f /var/log/syslog</pre>

            其中，*container_id* 是容器的名称。

            例如：

            <pre class="screen">docker exec iib tail -f /var/log/syslog</pre>

          </li>
        </ul>
       </li>
    </ol>


## 管理在容器中运行的 IBM Integration Bus 资源
{: #manage}

启动 IBM Integration Bus Web UI 或通过终端运行命令来管理在容器中运行的 IBM Integration Bus 资源。
{:shortdesc}

  选择以下其中一个选项来管理容器中的 IBM Integration Bus 资源：

*  [启动 IBM Integration Bus Web UI](#launchwebUI)，并以图形方式管理 IBM Integration Bus。
*  通过终端，使用 Docker 或 {{site.data.keyword.containershort_notm}} CLI 来管理 IBM Integration Bus 资源：
    *   [使用 {{site.data.keyword.containerlong_notm}} CLI](#admin_commands_containers_cli) 在容器中运行管理命令
    *   [使用 Docker CLI 在容器中运行管理命令](#admin_commands_docker_cli)


## 启动 IBM Integration Bus Web UI
{: #launchwebUI}

启动 Web UI 以通过浏览器管理容器中运行的 IBM Integration Bus 资源。
{:shortdesc}

按照以下步骤将浏览器连接到在 [开始使用 {{site.data.keyword.Bluemix_notm}} 的 ibm-integration-bus 映像](#get_started)中公开的主机。这将显示 IBM Integration Bus Web 用户界面。

1.  在 {{site.data.keyword.Bluemix_notm}} UI 中选择容器，并检查容器的状态是否为 `Running`。
2.  检查容器详细信息以找到公共 IP。
3.  使用以下 URL 打开 Web 浏览器：

    ```
    http://<DockerContainerPublicIP>:4414
    ```
    {: pre}

    其中，*DockerContainerPublicIP* 是在上一步中检查的容器的公共 IP。

4.  浏览器将打开 Web 用户界面，您可随时管理 IBM Integration Bus。


## 使用 {{site.data.keyword.containerlong_notm}} CLI 在容器中运行管理命令
{: #admin_commands_containers_cli}

使用 {{site.data.keyword.containershort_notm}} CLI 在容器中直接运行 IBM Integration Bus 管理命令。
{:shortdesc}

开始之前，将 {{site.data.keyword.containershort_notm}} CLI 配置为通过命令行运行管理命令。有关更多信息，请参阅[设置 {{site.data.keyword.containershort_notm}} 插件 (`bx ic`)](/docs/containers/container_cli_cfic_install.html)。

  完成以下步骤以将终端设置为运行 **bx ic** 命令来管理 IBM Integration Bus：

1.  通过终端登录到 {{site.data.keyword.Bluemix_notm}}。运行以下命令：

      <ol>
        <li>
           <pre class="codeblock">bx login</pre>
        </li>
        <li>
           <pre class="codeblock">bx ic init</pre>
        </li>
       </ol>
           
2.  将 Bash 会话附加到容器，以设置交互式会话。\*

    ```
    bx ic exec -it <container_name> /bin/bash
    ```
    {: pre}

    其中，*container_name* 是容器的名称。

    例如：

    ```
    bx ic exec -it iib /bin/bash
    ```
    {: screen}

    您可以在容器内的 shell 中确定 mqsiprofile 的源并从此点运行命令。

3.  或者，使用 **bx ic exec** 命令来运行非交互式 Bash 会话，此会话可运行任何 IBM Integration Bus 命令。\*

    例如：

    ```
    bx ic exec iib <container_name> /bin/bash -c mqsilist
    ```
    {: screen}


## 使用 Docker CLI 在容器中运行管理命令
{: #admin_commands_docker_cli}

使用 Docker CLI 在容器中直接运行 IBM Integration Bus 管理命令。
{:shortdesc}

开始之前，将 {{site.data.keyword.containershort_notm}} CLI 配置为通过命令行运行管理命令。有关更多信息，请参阅[设置 {{site.data.keyword.containershort_notm}} 插件 (`bx ic`)](/docs/containers/container_cli_cfic_install.html)。

  完成以下步骤以将终端设置为运行 Docker 命令来管理 IBM Integration Bus：

1.  通过终端登录到 {{site.data.keyword.Bluemix_notm}}。运行以下命令：

    <ol>
     <li>
       <pre class="codeblock">bx login</pre>
     </li>
     <li>
       <pre class="codeblock">bx ic init</pre>

      <p>复制为以下环境变量提供的值：&lt;DOCKER_HOST&gt;、&lt;DOCKER_CERT_PATH&gt; 和 &lt;DOCKER_TLS_VERIFY&gt;。</p>
      </li>
      <li>  通过设置以下变量来覆盖本地 Docker 环境，以连接到 {{site.data.keyword.containershort_notm}}：
        <ul>
           <li><pre class="codeblock">export DOCKER_HOST=&lt;your_host_value&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;your_cert_path_value&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
        </ul>

          <p><b>注</b>：此选项仅支持某些 Docker 命令。</p>
     </li>
    </ol>

2.  将 Bash 会话附加到容器，以设置交互式会话。运行以下命令：

    ```
    docker exec -it <container_name> /bin/bash
    ```
    {: pre}

    其中，*container_name* 是容器的名称。

    例如：

    ```
    docker exec -it iib /bin/bash
    ```
    {: screen}

    您可以在容器内的 shell 中确定 mqsiprofile 的源并从此点运行命令。

3.  您可以在容器内的 shell 中确定 mqsiprofile 的源并从此点运行命令。

    或者，使用 docker exec 来运行非交互式 Bash 会话，此会话可运行任何 IBM Integration Bus 命令。 
    
    例如：

    ```
    docker exec iib <container_name> /bin/bash -c mqsilist
    ```
    {: screen}


## 创建和部署流
{: #createdeployflow}

通过遵循 IBM Integration Bus Knowledge Center 中的指示信息来创建和部署消息流。
{:shortdesc}

有关如何创建消息流的信息，请参阅[如何创建和管理消息流？![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40150_.htm){: new_window}。

有关如何部署消息流的信息，请参阅[如何部署和配置消息流？![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40180_.htm){: new_window}。
