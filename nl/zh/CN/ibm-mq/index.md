---

copyright:
  years: 2015, 2017

lastupdated: "2017-10-30"

---


{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:screen: .screen}

# 开始使用 **ibm-mq** 映像
{: #ibm_mq}

为 {{site.data.keyword.containershort}} 提供了 ibm-mq 映像。它包括 {{site.data.keyword.IBM_notm}} MQ Advanced for Developers（不保证）。 

{:shortdesc}


## 工作原理
{: #ibm_mq_how}

{{site.data.keyword.IBM_notm}}MQ Advanced for Developers 包含开始开发自己的消息传递解决方案和 {{site.data.keyword.IBM_notm}} MQ 应用程序所需的一切内容。 

要在 {{site.data.keyword.Bluemix_notm}} 中开发消息传递解决方案和 {{site.data.keyword.IBM_notm}} MQ 应用程序，您可以在 {{site.data.keyword.containershort_notm}} 中部署 ibm-mq Docker 映像。然后，您可以创建和运行队列管理器，并使用 Web UI 或终端来对其进行配置，以满足消息传递解决方案或应用程序需求。

**注：**只能将 ibm-mq 映像用于开发和单元测试。此映像还可用于探索产品，学习教程以及评估 {{site.data.keyword.IBM_notm}} MQ 可对您组织做出的贡献。

## 所含内容
{: #ibm_mq_what}

此 ibm-mq 映像包含 {{site.data.keyword.IBM_notm}} MQ Advanced for Developers V9.0.x 的软件包，它是可用于开发和单元测试的完整功能版本的产品。您可以免费下载此版本，并可根据许可证条款免费使用。

{{site.data.keyword.IBM_notm}} MQ Advanced for Developers 可在 Windows 64 位操作系统和 Linux on x86-64 操作系统上使用。所有产品先决条件均包含在下载包中。有关更多信息，请参阅 [IBM MQ 下载 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](http://www-03.ibm.com/software/products/lv/ibm-mq-advanced-for-developers){: new_window}

有关 {{site.data.keyword.IBM_notm}} MQ Advanced for Developers 中包含的功能部件的信息，请参阅 [IBM MQ ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://www.ibm.com/support/knowledgecenter/SSFKSJ_9.0.0/com.ibm.mq.helphome.v90.doc/WelcomePagev9r0.htm){: new_window}



## 使用限制
{: #ibm_mq_usage}

下表显示适用于 {{site.data.keyword.Bluemix_notm}} 中 ibm-mq 映像免费使用的限制：

| 环境| 免费使用限制|
|-------------|-------------------------|
| 开发| 无限免费使用 ibm-mq 映像，以创建单个容器用于开发。|
| 测试| 无限免费使用 ibm-mq 映像，以创建单个容器用于单元测试。检查[许可证信息](#ibm_mq_license )以获取有关允许的测试的更多信息。|
| 生产| {{site.data.keyword.IBM_notm}} MQ Advanced for Developer 许可证不允许用于生产。|

**注**：ibm-mq 映像的定价与 {{site.data.keyword.Bluemix_notm}} 中使用的容器的定价无关。


## 许可证信息
{: #ibm_mq_license}

关于许可证的信息：

*	Dockerfile 和关联的脚本根据 [Apache License 2.0 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](http://www.apache.org/licenses/LICENSE-2.0.html){: new_window} 授予许可。
* [{{site.data.keyword.IBM_notm}}MQ Advanced for Developers 的许可证信息 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](http://www-03.ibm.com/software/sla/sladb.nsf/displaylis/973FA648A5DE42948525806E004CC757?OpenDocument){: new_window}。

**注：**许可证不允许进一步分发。映像中 {{site.data.keyword.IBM_notm}} MQ 的条款将用途限制为开发和单元测试。


## 入门
{: #ibm_mq_get_started}

完成下列步骤，以便在 {{site.data.keyword.Bluemix_notm}} 上运行的容器中运行和管理 {{site.data.keyword.IBM_notm}} MQ：

1. [供应基于 ibm-mq 映像的容器](#ibm_mq_provision)

    **注：**容器与其内的“队列管理器”的运行状态相关。如果使用控制台或命令行停止“队列管理器”，那么容器将在稍后停止。 

2. 管理正在容器中运行的 {{site.data.keyword.IBM_notm}} MQ 资源。您可以从 IBM MQ Web 控制台以图形方式执行此操作，也可以通过使用来自终端的命令以编程方式执行此操作。 

    1. 选择以下任一选项来管理容器中的 {{site.data.keyword.IBM_notm}} MQ 资源：
    
        *	启动 {{site.data.keyword.IBM_notm}} MQ Web 控制台并以图形方式管理 {{site.data.keyword.IBM_notm}}。有关更多信息，请参阅[启动 IBM MQ Web 控制台](#ibm_mq_webui)。
        *	通过终端，使用 Docker CLI。有关更多信息，请参阅[设置终端以使用 Docker CLI](#ibm_mq_docker_commands)。
        * 通过终端，使用 {{site.data.keyword.containershort_notm}} CLI。有关更多信息，请参阅[设置终端以使用 {{site.data.keyword.containershort_notm}} CLI](#ibm_mq_containers_cli)。
        
    2. 配置在容器中运行的 {{site.data.keyword.IBM_notm}} MQ“队列管理器”。 

        * 要通过命令行管理“队列管理器”，请使用 *runmqsc* 程序。运行以下命令：`runmqsc QM_Name`，其中 *QM_Name* 是“队列管理器”的名称。有关更多信息，请参阅[通过命令行管理容器](#ibm_mq_access)。
    
        * 要以图形方式管理“队列管理器”，请使用 Web UI。
        
    3. 遵循 [IBM MQ Knowledge Center ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](http://www.ibm.com/support/knowledgecenter/en/SSFKSJ_9.0.0/com.ibm.mq.helphome.v90.doc/WelcomePagev9r0.htm){: new_window} 上的指示信息，以在您的“队列管理器”上创建消息队列，并将消息放入队列或从中获取消息。
    
        例如，在 `runmqsc` 中运行以下命令，以创建新本地队列：`DEFINE QLOCAL('MY.Q')`，其中 *MY.Q* 是来自终端的本地队列的名称。您可以在 Web 控制台中查看该队列，也可以通过在 `runmqsc` 中运行以下命令来显示该队列及其属性：DISPLAY QLOCAL('MY.Q')。
    
3. （可选）监视容器内的 {{site.data.keyword.IBM_notm}} MQ 日志。有关更多信息，请参阅[通过命令行管理容器](#ibm_mq_access)。

有关 IBM MQ 入门简介的更多信息，请参阅 [IBMMQ V9.0.x 欢迎页面 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](http://www.ibm.com/support/knowledgecenter/en/SSFKSJ_9.0.0/com.ibm.mq.helphome.v90.doc/WelcomePagev9r0.htm){: new_window}

有关该映像以及如何在 Docker 本地构建您自己的映像的更多信息，请参阅 [MQ Docker GitHub 页面 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://github.com/ibm-messaging/mq-docker){: new_window}
    
有关 IBM MQ 的最新博客和信息，请参阅 [MQ Developer 工作博客 ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://www.ibm.com/developerworks/community/blogs/messaging){: new_window}


### 供应基于 ibm-mq 映像的容器
{: #ibm_mq_provision}

完成下列步骤，以便在 {{site.data.keyword.IBM_notm}} 中供应基于 {{site.data.keyword.Bluemix_notm}} 所提供的 ibm-mq 映像的 Docker 容器：

1.	登录到 {{site.data.keyword.Bluemix_notm}}。使用您的 {{site.data.keyword.Bluemix_notm}} 标识进行登录。
2.	在目录中，选择**容器**，然后选择 *ibm-mq* 映像。
3.	选择**单个**以创建单个实例容器，此容器可用于开发和测试用途。
4.	输入容器的名称；例如，mq。
5.	选择容器的大小。
6.	在“公共 IP 地址”字段中，选择**请求和绑定公共 IP**。
7.	在“公共端口”字段中，指定 **1414/tcp、9443/tcp**。
8.	展开“高级”选项，单击**添加新环境变量**，然后输入以下环境变量：LICENSE 和 MQ_QMGR_NAME。

    * 将 **LICENSE** 的值设置为 **accept** 以同意 IBM MQ Advanced for Developers 许可条款。 
    
        如果您希望在接受许可证之前查看许可证，那么可以将 LICENSE 设置为“view”，许可证将在容器日志中打印。 
    
        如果英语不是您的第一种语言，可以通过将名为 **LANG** 的另一个环境变量设置为以下值之一，以便用不同的语言来查看许可证文件：*zh_TW* 表示繁体中文、*zh* 表示简体中文、*cs* 表示捷克语、*en* 表示英语、*fr* 表示法语、*de* 表示德语、*el* 表示希腊语、*id* 表示印度语、*it* 表示意大利语、*ja* 表示日语、*ko* 表示韩语、*lt* 表示立陶宛语、*pl* 表示波兰语、*pt* 表示葡萄牙语、*ru* 表示俄语、*sl* 表示斯洛文尼亚语、*es* 表示西班牙语或 *tr* 表示土耳其语。

    * 将 **MQ_QMGR_NAME** 的值设置为值 *MYMQ*，其中*MYMQ* 是您选择的“队列管理器”名称。
 
        **注：**如果未指定环境变量 LICENSE 和 MQ_QMGR_NAME 的值，那么将创建容器，但不会启动该容器。 
    
    * （可选）缺省情况下，{{site.data.keyword.IBM_notm}} MQ Web 控制台会在容器中启动。要更改缺省行为，请将 **MQ_DISABLE_WEB_CONSOLE** 环境变量值设置为 *true*。
    
        **注：**要使用 Web 控制台，请勿设置 *MQ_DISABLE_WEB_CONSOLE* 环境变量。

    * （可选）要通过 {{site.data.keyword.Bluemix_notm}} UI 查看 MQ 日志，请将环境变量 **LOG_LOCATIONS** 设置为值 `/var/mqm/qmgr/QM_Name/errors/AMQERR01.LOG`，其中 *QM_Name* 是您为环境变量 *MQ_QMGR_NAME* 设置的值。
    
    *	（可选）缺省情况下，将创建 {{site.data.keyword.IBM_notm}} MQ Developer 缺省对象。要更改缺省行为，请将 **MQ_DEV** 环境变量设置为 *false*，然后使用定制值修改 IBM MQ 的缺省安装值。有关更多信息，请参阅[修改缺省安装值](#ibm_mq_dev_default)。
 
9. （可选）在“高级选项”部分中，分配卷。使用 {{site.data.keyword.Bluemix_notm}} 卷通过容器迁移，持久存储 {{site.data.keyword.IBM_notm}} MQ 数据和配置。
如果容器终止，那么未存储在卷上的 {{site.data.keyword.IBM_notm}} MQ 数据会丢失。

    可以将 ibm-mq 映像与 {{site.data.keyword.Bluemix_notm}} 卷配合使用，但是，必须将卷安装到 **/mnt/mqm**，{{site.data.keyword.IBM_notm}} MQ 才能检测到并加以利用。

    缺省情况下，在没有卷的情况下创建的任何容器都将在容器停止时丢失任何 IBM MQ 数据。如果要防止发生此情况，您必须使用持久消息传递和 {{site.data.keyword.Bluemix_notm}} 卷。

    1. 单击**创建卷**，输入卷名称，然后选择文件共享。
    2. 单击**分配现有卷**，然后选择您创建的卷。在路径框中输入 **/mnt/mqm**。确保未选中**只读**框。

    **注：**安装卷以用作 IBM MQ 数据目录时，必须将其安装到 */mnt/mqm* 位置。通过将其安装到 */mnt/mqm*，ibm-mq 脚本将检测该卷并配置 IBM MQ 文件系统来使用该卷。
     
10.	单击**创建**，然后等待容器开始运行。

    创建容器后，容器仪表板将打开。检查容器的状态是否设置为**正在运行**。 

    要检查容器的日志，请单击**监视和日志**，然后选择**日志记录**选项卡。此时将显示日志。要获取日志的高级分析，请单击**高级视图**以在 Kibana 中显示日志。

您已创建并启动了一个队列管理器。现在，您可以使用以下连接详细信息来连接 MQ 应用程序：

| 连接属性| 值|
|-------|-------|
| IP 地址| 容器的公共 IP 地址|
| 端口| 1414 |
| 通道| DEV.APP.SVRCONN |


### 通过命令行管理容器
{: #ibm_mq_access}

在 {{site.data.keyword.Bluemix_notm}} 中部署了运行 {{site.data.keyword.IBM_notm}} MQ 的容器之后，您可以访问容器和容器的状态，并验证 {{site.data.keyword.IBM_notm}} MQ 安装。

完成以下步骤以在容器中验证 {{site.data.keyword.IBM_notm}} MQ 的设置和配置：

1.	设置 {{site.data.keyword.containershort_notm}} CLI。有关更多信息，请参阅 [{{site.data.keyword.Bluemix_notm}} CLI 页面](https://plugins.ng.bluemix.net/ui/home.html)，并遵循指示信息来安装最新的 {{site.data.keyword.containershort_notm}} 插件。

2.	通过终端，使用 {{site.data.keyword.Bluemix_notm}} 标识登录到容器运行所在的 {{site.data.keyword.Bluemix_notm}} 组织和空间。运行以下命令：

    1. `  bx login
  `
    2. `bx ic init`
    
3.	检查容器的状态。运行以下命令：`bx ic ps`

4.	将 Bash 会话附加到容器：

    `bx ic exec -it container_name /bin/bash`
    
    其中 container_name 是容器的名称
    
5.	运行命令以管理 MQ 设置，例如：

| 命令| 操作|
|---------|---------|
| dspmqver | 验证 IBM MQ 版本和构建信息。|
| dspmq | 检查正在容器中运行的“队列管理器”的状态。| 
| runmqsc | 管理 MQ 资源。|

要监视 MQ 日志，请选择以下某个选项：

* 如果您的终端配置为运行 {{site.data.keyword.containershort_notm}} CLI 命令，请运行以下命令：`bx ic exec container_id tail -f /var/mqm/qmgr/QM_ Name/errors/AMQERR01.LOG`，其中 *container_id* 是容器的名称，*QM_ Name* 是“队列管理器”的名称。
    
* 如果您的终端配置为运行 Docker CLI 命令，请运行以下命令：`docker exec container_id tail -f /var/mqm/qmgr/QM_Name/errors/AMQERR01.LOG`，其中 *container_id* 是容器的名称，*QM_Name* 是“队列管理器”的名称。
    
    

### 启动 IBM MQ Web 控制台
{: #ibm_mq_webui}

完成以下步骤以启动 {{site.data.keyword.IBM_notm}} MQ Web 控制台：

1.	在 {{site.data.keyword.Bluemix_notm}} 仪表板中，选择容器，并检查容器的状态是否设置为*正在运行*。
2.	验证容器是否已配置公共端口 9443。
3.	检查容器详细信息以查找公共 IP 或请求并绑定一个（如果尚未执行）。
4.	在 Web 浏览器中，启动以下 URL：`https://DockerContainerPublicIP:9443/ibmmq/console/`，其中 DockerContainerPublicIP 是容器的公共 IP。

    这将打开一个安全警告，因为 Web 服务器托管 MQ 控制台时使用的 TLS 证书已生成并已自签名。单击**添加例外**并确认。

    **注：**在生产环境中，使用浏览器识别并信任的自己的证书。此证书应由认证中心发放。 
    
    浏览器将打开 Web 用户界面。
    
5. 使用凭证登录到 {{site.data.keyword.IBM_notm}} MQ 控制台：

    1.	用户：admin
    2.	密码：passw0rd
    
您现在已准备好管理 IBM MQ。

### 设置终端以使用 {{site.data.keyword.containershort_notm}} CLI
{: #ibm_mq_containers_cli}

使用 {{site.data.keyword.containershort_notm}} CLI 直接在容器中运行 {{site.data.keyword.IBM_notm}} MQ 管理命令。

完成以下步骤以将终端设置为运行 `bx ic` 命令来管理 IBM MQ：

1.	设置 {{site.data.keyword.containershort_notm}} CLI。有关更多信息，请参阅 [{{site.data.keyword.Bluemix_notm}} CLI 页面](https://plugins.ng.bluemix.net/ui/home.html)，并遵循指示信息来安装最新的 {{site.data.keyword.containershort_notm}} 插件。

2.	通过终端登录到 {{site.data.keyword.Bluemix_notm}}。运行以下命令：

    1. `  bx login
  `
    2. `bx ic init`
    
3.	将 Bash 会话附加到容器：

    `bx ic exec -it container_name /bin/bash`
    
    其中，container_name 是容器的名称。
    


### 设置终端以使用 Docker CLI
{: #ibm_mq_docker_commands}

使用 Docker CLI 直接在容器中运行 {{site.data.keyword.IBM_notm}} MQ 管理命令。

完成以下步骤以将终端设置为运行 Docker 命令来管理 IBM MQ：

1.	设置 {{site.data.keyword.containershort_notm}} CLI。有关更多信息，请参阅 [{{site.data.keyword.Bluemix_notm}} CLI 页面](https://plugins.ng.bluemix.net/ui/home.html)，并遵循指示信息来安装最新的 {{site.data.keyword.containershort_notm}} 插件。

2.	通过终端登录到 {{site.data.keyword.Bluemix_notm}}。运行以下命令：

    1. `  bx login
  `
    2. `bx ic init`
    3. 复制为以下环境变量提供的值：DOCKER_HOST、DOCKER_CERT_PATH 和 DOCKER_TLS_VERIFY。
    4. 通过设置以下变量来覆盖本地 Docker 环境，以连接到 {{site.data.keyword.containershort_notm}}：
        
        `export DOCKER_HOST=<your_host_value>`
        
        `export DOCKER_CERT_PATH=<your_cert_path_value>`
        
        `export DOCKER_TLS_VERIFY=1`

3.	将 Bash 会话附加到容器：

    `bx ic exec -it container_name /bin/bash`
    
    其中 container_name 是容器的名称
    
**注：**此选项仅支持某些 Docker 命令。




### 修改缺省安装值
{: #ibm_mq_dev_default}

创建队列管理器时，将使用 {{site.data.keyword.IBM_notm}} MQ Developer 缺省值来创建该队列管理器。这些缺省对象旨在帮助您快速开始开发应用程序，甚至尝试使用 {{site.data.keyword.IBM_notm}} MQ。 

将使用以下缺省对象来创建“队列管理器”：

* MQ 管理员用户 **admin**，其密码为 *passw0rd*。
*	没有密码的 MQ 应用程序用户 **app**。
*	配置为绑定到端口 1414/TCP 的侦听器 **DEV.LISTENER.TCP**。
*	配置为处理 MQ 客户机应用程序连接的通道 **DEV.APP.SVRCONN**。
*	配置为处理 MQ 管理连接的通道 **DEV.ADMIN.SVRCONN**。
*	您可在其中放置消息的 3 个队列 **DEV.QUEUE.1**、**DEV.QUEUE.2**、**DEV.QUEUE.3**。
*	死信队列 **DEV.DEAD.LETTER.QUEUE**，其中将放置无法传递的消息。
*	基本主题 **DEV.BASE.TOPIC**，主题字符串为 */dev*。
*	“通道认证”记录，用于阻止所有客户机连接，除非它们通过通道 **DEV.APP.SVRCONN** 或 **DEV.ADMIN.SVRCONN** 进行连接。
*	“通道认证”记录，用于阻止所有管理用户连接，通过 **DEV.ADMIN.SVRCONN** 通道进行的连接除外。
*	“通道认证”记录，仅允许管理用户连接到 **DEV.ADMIN.SVRCONN** 通道。
*	连接认证，设置为要求管理应用程序在连接时传递有效凭证，并采用该用户标识进行权限检查。

借助这些缺省对象，您可以将可放入或获取消息的客户机应用程序连接到队列，或者预订和发布主题。

如果您想要定义所创建的开发者缺省值，那么您可以在通过 ibm-mq 映像创建容器时，添加以下环境变量。

| 环境变量| 用途| 缺省值|
|----------------------|---------|---------|
| MQ_ADMIN_PASSWORD | 设置此变量以将已创建的 MQ 管理员的密码从缺省值更改为您自己的选择。密码的长度必须至少为 8 个字符。
admin 用户的用户名为固定为 admin。| passw0rd |
| MQ_APP_PASSWORD | 设置此变量以设置应用程序用户的密码。应用程序用户的用户名固定为 app。如果设置此环境变量，那么在连接 MQ 客户机时必须提供用户标识和密码。<br> 密码的长度必须至少为 8 个字符。
|  |
| MQ_TLS_KEYSTORE | 将此环境变量设置为包含您希望 MQ Web 控制台和 IBM MQ“队列管理器”用于 TLS 操作的证书的 PKCS#12 密钥库所在的位置。<br> 当设置了此环境变量时，将使用 CipherSpec“TLS_RSA_WITH_AES_256_GCM_SHA384”为 TLS 启用创建的开发者通道。
<br> **注：**您需要使密钥库文件在 {{site.data.keyword.Bluemix_notm}} 容器中可用。为此，您可以在创建容器时安装包含密钥库的卷。|  |
| MQ_TLS_PASSPHRASE | 设置此环境变量以设置环境变量 MQ_TLS_KEYSTORE 中指定的密钥库的密码。|  |
| MQ_DEV | 设置此环境变量以控制是否创建 {{site.data.keyword.IBM_notm}} MQ Developer 缺省对象。| true|
| MQ_DISABLE_WEB_CONSOLE | 如果希望保留容器的 CPU/RAM 使用量，请设置此环境变以禁用 Web 控制台的配置和启动。|  |
