---

copyright:
  years: 2017
lastupdated: "2017-10-30"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# 开始使用 **ibm-websphere-extreme-scale** 映像 
{: #ibm-websphere-extreme-scale}

为 {{site.data.keyword.containerlong}} 提供了 **ibm-websphere-extreme-scale** 映像。

## 工作原理 
{: #how_it_works}

IBM WebSphere&reg; eXtreme Scale 映像是一种在 Liberty 运行时环境内运行的分布式内存中高速缓存功能。此映像根据最新的业界标准和规范，提供了 Liberty 高速缓存服务器、高速缓存操作工具、管理控制台和开箱即用型 REST 管理服务。此映像基于核心 IBM WebSphere eXtreme Scale 技术构建。此映像内装入的高速缓存技术完全支持 JCache \(JSR107\) 规范和合规性。

使用 `ibm-websphere-extreme-scale` 映像时，有以下主要突出特点：

-   基于 eXtreme Scale 技术的易于部署、易于配置的分布式高速缓存解决方案
-   简单易用的管理控制台和 REST API
-   用于支持所有 eXtreme Scale 高速缓存用例的完全定制
-   集成在高速缓存环境内的可轻松配置的映像，在几分钟内就能完成配置
-   用于支持 JSR107 高速缓存规范的 JCache 合规性


## 所含内容 
{: #whats_included}

每个 **ibm-websphere-extreme-scale** 映像都会提供以下软件包。

-   Ubuntu 16.04 基本映像（64 位）
-   IBM WebSphere eXtreme Scale
-   IBM Java Runtime Environment 8.0

映像中具体安装了哪些功能部件取决于您所选择的标记。下表显示了每个 **ibm-websphere-extreme-scale** 映像中包含的代码级别。有关这些功能部件的更多信息，请参阅 [eXtreme Scale 文档](http://www.ibm.com/support/knowledgecenter/SSTVLU)。

|标记|描述|
|---|-----------|
|**ibm-websphere-extreme-scale:latest**|使用此标记可指定最新的 IBM WebSphere eXtreme Scale 映像。|
|**ibm-websphere-extreme-scale:8.6.1.x**|使用此标记可指定特定发行版级别的 WebSphere eXtreme Scale。|
{: caption="表 1. IBM WebSphere eXtreme Scale 映像" caption-side="top"}

## 使用限制 
{: #usage}

下表显示适用于 {{site.data.keyword.Bluemix_notm}} 中 **ibm-websphere-extreme-scale** 映像免费使用的限制。

|环境|使用限制|
|-----------|------------------|
|开发|`ibm-websphere-extreme-scale` 映像支持针对开发的无限免费使用。|
|生产|**ibm-websphere-extreme-scale** 映像的生产使用限制为许可的 IBM WebSphere eXtreme Scale 产品用户。有关生产使用的更多信息，请参阅 [View Pricing and Buy](https://www-112.ibm.com/software/howtobuy/buyingtools/paexpress/Express?P0=E1&part_number=D56YTLL,D56YVLL,D572PLL,D572MLL,D0594LL,D059GLL&catalogLocale=en_US&Locale=en_US&country=USA&PT=jsp&CC=USA&VP=&TACTICS=&S_TACT=&S_CMP=&brand=SB05)。|
{: caption="表 2. {{site.data.keyword.Bluemix_notm}} 中 ibm-websphere-extreme-scale 映像的使用限制" caption-side="top"}

**注**：**ibm-websphere-extreme-scale** 映像的定价与 {{site.data.keyword.Bluemix_notm}} 中使用的容器的定价无关。

在[许可证信息文件](http://www-03.ibm.com/software/sla/sladb.nsf/displaylis/A44EE6829150B2998525801700516718?OpenDocument)的“许可证”部分中查看经 IBM 认证的映像的使用条款。


## 入门 
{: #get_started}

可以使用控制台或命令行来部署 **ibm-websphere-extreme-scale** 映像。

此过程包含以下选项，可选择是使用控制台还是使用命令行来部署映像：

-   从**目录**的 {{site.data.keyword.Bluemix_notm}} [控制台](#bmconsole) 中选择映像。
-   使用 cf ic 命令，通过 CF [命令行](#bmcommand)启动映像。

**重要事项**：开始之前，请查看先前部分中针对 **ibm-websphere-extreme-scale** 映像所述的使用限制。


### 通过 {{site.data.keyword.Bluemix_notm}} 控制台进行部署。
{: #bmconsole}

   1.  在目录中，选择**容器**，然后选择 **ibm-websphere-extreme-scale** 映像以开始构建容器。
   2.  从**标记/版本**下拉菜单中，选择要使用的 **ibm-websphere-extreme-scale** 映像的版本。
   3.  创建单个容器。

        **注：**如果要创建多个容器，请勿使用**可扩展**选项来创建两个或更多 eXtreme Scale 高速缓存成员组。请改为选择**单个**选项来创建多个容器，然后使用 eXtreme Scale 仪表板 UI 来加入高速缓存成员。

        有关更多信息，请参阅[使用 {{site.data.keyword.Bluemix_notm}} 仪表板创建单个容器](/docs/containers/container_single_ui.html#gui)。

        1.  在“创建容器”面板中，指定**容器名称**；例如，`wxs1`。容器名称中不允许使用空格或其他不可打印的字符。
        2.  对于**大小**，指定用于启动 ibm-eXtreme-scale 映像的容器的大小。
        3.  对于**公共 IP 地址**，指定请求和绑定公共 IP。
        4.  打开“高级选项”面板。创建卷，以便在以后安装卷动升级时可以持久存储 eXtreme Scale 配置数据；例如：
            1. 在“高级选项”中单击**创建卷**，然后指定**卷名**。例如，输入 vol-wxs1。
            2. 单击**添加新环境变量**。**ibm-websphere-extreme-scale** 映像需要存储在容器内并由授权用户访问的环境变量。<table>
            <caption>表 3. ibm-websphere-extreme-scale 映像的环境变量</caption>
               <tr>
                 <th>变量名称</th>
                 <th>要指定的值</th>
               </tr>
               <tr>
                 <td>SECRET_KEY</td>
                 <td>密钥的值；例如 <var class="keyword varname">s3cretKey!</var>。这是 eXtreme Scale 服务器用于在高速缓存成员组中的多个成员之间发送内部通信的密钥。有关更多信息，请参阅[高速缓存成员组](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txscreategridtut1.html)部分。</td>
               </tr>
               <tr>
                 <td>XSADMIN_PWD</td>
                 <td>登录到 eXtremeScale 仪表板 UI 的 xsadmin 用户的密码；例如，<var class="keyword varname">xsadmin4Me!</var>。</td>
               </tr>
               </table>
  
              **注：**您可以在 eXtreme Scale 容器启动后更改密码和密钥。密钥和密码都具有以下密码规则。请确保根据以下规则输入密钥和密码：

              密码和密钥必须至少包含一个数字 [`0-9`]、一个大写字符 [`A-Z`]、一个特殊字符 [`~!@#$%^&*()_-+=:;'[]{}|<>,.?/`] 并且至少为10 个字符。

              有关如何更改密码的更多信息，请参阅[管理](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txsadministerxsld.html)文档。

              缺省情况下会在**公共端口**字段中添加此端口。

   4.  单击**创建**以通过 {{site.data.keyword.Bluemix_notm}} 仪表板创建容器。
    
   5.  容器和 eXtreme Scale 服务器完全启动后，登录到 eXtreme Scale 仪表板 UI，并验证部署。在 {{site.data.keyword.Bluemix_notm}} 控制台中，单击要查看其公共 IP 的容器。WebSphere eXtreme Scale 容器可以通过以下 URL 访问：

    ```
        https://<Public_IP>:9443
    ```
    {: pre}

   **注：**服务器可能需要 5-10 分钟才能完全启动。


### 通过命令行部署映像。
{: #bmcommand}
 1.  创建 Docker 卷来存储 IBM WebSphere eXtreme Scale 持久数据。例如，输入以下命令：

  ```
  cf ic volume create <vol-wxs1>
  ```
  {: pre}

 2.  输入以下命令以在 {{site.data.keyword.Bluemix_notm}} 中启动 IBM WebSphere eXtreme Scale 容器：

    **注：**将 yourSecretKey 和 yourXsadminPassword 替换为您自己的密码（基于前述密码规则）。

   ```
   cf ic run -itdP --volume <vol-wxs1:/vol> --name <wxs1> -m 1024 -e SECRET_KEY=<yourSecretKey> -e XSADMIN_PWD=<yourXsadminPassword> registry.ng.bluemix.net/ibm-websphere-extreme-scale:latest
   ```
   {: pre}
        
     **注：**容器名称只能包含字母数字字符并且必须以字母开头。或者，如果需要容器名称包含其他非字母数字字符，那么可以设置 <code>-e XSLD_CONTAINER_ALIAS=<wxs1></code>。

 3.  请求要用于第一个 IBM WebSphere eXtreme Scale 容器的公共 IP。输入以下命令以编写 IP 地址：

   ```
   cf ic ip request
   ```
   {: pre}

 4.  输入以下命令以将 WebSphere eXtreme Scale 容器绑定到请求的公共 IP 地址：

   ```
   cf ic ip bind <REQUESTED_IP> wxs1
   ```
   {: pre}

 5.  输入以下命令以验证 `wxs1` 容器的状态是否为 Running：

   ```
   cf ic ps -a
   ```
   {: pre}

 6.  输入以下命令以连接到 `wxs1` 容器并监视 nanny 日志：

   ```
   cf ic exec -it <wxs1> tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
        ```
   {: pre}

    **注：**服务器可能需要 5-10 分钟才能启动。

 7.  容器和 eXtreme Scale 服务器完全启动后，登录到 eXtreme Scale 仪表板 UI，并验证部署：

   ```
   https://<REQUESTED_IP>:9443
   ```
   {: pre}


## 添加高速缓存容量 
{: #caching}

1.  按照先前的相同步骤，通过控制台或命令行部署其他映像。
2.  容器和 eXtreme Scale 服务器完全启动后，登录到此成员的 eXtreme Scale 仪表板 UI：

    ```
       https://<REQUESTED_IP>:9443
   ```
    {: pre}

3.  打开“数据高速缓存成员”页面，并在此成员的**操作**列下选择**加入**。
4.  输入第一个成员请求的公共 IP、第一个成员的密钥以及高速缓存组中此成员的唯一名称。
5.  单击**加入**，并等待任务完成。任务完成后，将在“数据高速缓存成员”页面上显示所有成员。

## 执行卷动升级 
{: #rolling_upgrade}

下面是在 {{site.data.keyword.containershort_notm}} 中对运行 `ibm-websphere-extreme-scale` 的容器执行卷动升级所必需的步骤。

**注：**如果 eXtreme Scale 高速缓存成员组只有一个成员，请除去此 {{site.data.keyword.IBM_notm}} 容器，然后从头开始创建 IBM 容器。但是，如果 eXtreme Scale 高速缓存成员组具有多个成员，部署了多个目录服务器，并且您希望在 {{site.data.keyword.containershort_notm}} 中保留高速缓存的数据，请完成以下卷动升级步骤以避免丢失任何高速缓存的数据。首先升级只具有容器服务器的高速缓存成员，然后升级还具有目录服务器的高速缓存成员。

1.  启动名为 wxsmigrate 的新容器，并将其用于升级所有现有的 eXtreme Scale 容器。有关更多信息，请参阅此页面上的[开始使用](#get_started)部分。最新的 eXtreme Scale 更新 .zip 文件将部署在新 wxsmigrate 容器的 /tmp 文件夹下。
2.  将更新后的 eXtreme Scale .zip 文件从 wxsmigrate 容器复制到您在其中运行 {{site.data.keyword.containershort_notm}} CLI 插件的本地系统：

    ```
        cf ic cp wxsmigrate:/tmp/xsld-bin-update.zip
    ```
    {: pre}
    
    ```
        cf ic cp wxsmigrate:/tmp/xsld-derby-update.zip
    ```
    {: pre}

3.  现在，对于高速缓存成员组中的每个高速缓存成员，完成以下步骤，一次一个容器：

    1. 将更新后的 eXtreme Scale .zip 文件从本地系统复制到要升级的 eXtreme Scale 容器：
    
      ```
            cf ic cp  xsld-bin-update.zip  <container_name>:/tmp/xsld-bin-update.zip
      ```
      {: pre}
  
      ```
            cf ic cp  xsld-derby-update.zip  <container_name>:/tmp/xsld-derby-update.zip
      ```
      {: pre}
     
    2. 连接到要升级的容器： 
    
      ```
          cf ic exec -it <container_name> bash
    ```
      {: pre}

    3. 停止已准备就绪可升级的容器上的 XSLD 服务器。
      
      ```
          /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
    ```
      {: pre}
      
      等待这些服务器停止。
      
    4. 解压缩容器内最新的 eXtreme Scale 更新代码：
      
      ```
            unzip  -o  /tmp/xsld-bin-update.zip   -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
      
      ```
            unzip  -o  /tmp/xsld-derby-update.zip -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
    
    5. 再次启动 XSLD 服务器：
    
      ```
            /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
      ```
      {: pre}
      
    6. 监视 nanny 日志以确保服务器已启动：
    
      ```
            tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
      ```
      {: pre}
      
4. 在所有 IBM 容器上完成升级后，除去 wxsmigrate 容器：

 ```
 cf ic rm -f <wxsmigrate>
 ```
 {: pre}

## 故障诊断 
{: #troubleshoot}

###对失去主机映射时的高速缓存成员连接进行故障诊断 
{: #troubleshoot_cache}

高速缓存组的成员因失去容器之间的主机映射而断开连接时，请完成以下步骤。

1.  连接到每个容器：

    ```
        cf ic exec -it <container_name> bash
    ```
    {: pre}

2.  收集每个成员的专用 IP、主机名和主机别名。

    主机别名是 XSLD_CONTAINER_ALIAS 环境变量的值：

    ```
        echo ${<XSLD_CONTAINER_ALIAS>}
    ```
    {: pre}

    专用 IP 是在容器内部运行 hostname -I 命令时返回的 IP 地址：

    ```
        hostname -I
    ```
    {: pre}

    主机名是在容器内部运行 hostname 命令时返回的主机名。


3.  对每个成员运行 recoverXSLD.sh 脚本，同时传入其他所有成员别名、IP 和主机名：

    ```
        /opt/ibm/WebSphere/eXtremeScale/wxs/recoverXSLD.sh <member2_alias> <member2_private_ip> <member2_hostname> ... <memberN_alias> <memberN_private_ip> <memberN_hostname>
    ```
    {: pre}

4.  通过对高速缓存组中的每个容器运行 stopXSLD.sh 来停止正在运行的服务器。运行 stopXSLD.sh：

    ```
        /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
    ```
    {: pre}

5.  如果 stopXSLD.sh 在尝试停止目录服务器时没有任何进展，请执行以下步骤：
    1.  列出目录服务器进程：

        ```
                ps -ef | grep cs-default
        ```
        {: pre}

    2.  使用进程标识结束目录服务器进程：

        ```
                kill -9 <catalog_server_process_id>
        ```
        {: pre}

    3.  再次运行 stopXSLD.sh：

        ```
            /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
    ```
        {: pre}

6.  大约同一时间在所有容器上启动 XSLD。运行 startXSLD.sh：

    ```
          /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
      ```
    {: pre}


### 对已停止的 Derby 复制任务进行故障诊断 
{: #troubleshoot_derby}

如果 Derby 复制任务在执行到 45% 之后超过 10 分钟都没有任何进展，有可能是网络问题造成的。请完成以下步骤以对该问题进行故障诊断：

1.  使用 REST POSTing 实用程序（如 cURL）来取消 Derby 复制任务。

    ```
        curl -X PUT --header "Content-Type: application/json" --header "Accept: application/json" "https://<PUBLIC_IP>:9445/wxsadmin/v1/task/cancel/<DERBY_REPLICATION_TASK_ID>/force"
    ```
    {: pre}

    **注：**<PUBLIC_IP> 是命令中用于启动 Derby 复制任务的 IP，<DERBY_REPLICATION_TASK_ID> 是从该命令输出的任务标识。

2.  再次运行 Derby 复制任务。

## 访问 REST API 
{: #api}

eXtreme Scale 部署在 {{site.data.keyword.containershort_notm}} 中时，请完成以下步骤来访问 REST API：

1.  检索计划用于连接到该 REST API 的容器的主机名。
    1.  连接到容器：

        ```
            cf ic exec -it <container_name> bash
    ```
        {: pre}

    2.  列出主机名：

        ```
                hostname
        ```
        {: pre}

2.  检索同一容器的公共 IP。

    如果容器没有公共 IP，请执行“开始使用”部分中的步骤，以使用 {{site.data.keyword.Bluemix_notm}} [控制台](#bmconsole)或[命令行](#bmcommand)来为容器提供公共 IP。

3.  在本地客户端计算机的 hosts 文件中创建一个条目，用于将公共 IP 和容器主机名映射在一起。该条目类似于：

    ```
        169.44.8.87 instance-05106102
    ```
    {: pre}

    hosts 文件的典型完整路径名为：

    -   对于 UNIX：/etc/hosts
    -   对于 Windows：C:\\Windows\\System32\\drivers\\etc\\hosts
4.  在浏览器中访问 Swagger REST API。
    -   对于 CRUD 操作：

        ```
                https://<host_name>:9444/ibm/api/explorer
        ```
        {: pre}

    -   对于管理操作：

        ```
                https://<host_name>:9445/ibm/api/explorer
        ```
        {: pre}


有关样本以及最新的更新，请访问 [GitHub 中的 WebSphere eXtreme Scale](https://github.com/ibmWebsphereExtremeScale)。此外，请参阅以下支持文档和视频，以获取有关部署 eXtreme Scale 的更多帮助：

-   [IBM WebSphere eXtreme Scale 文档](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/cxsgetstartxsld.html)
-   [XS developerWorks 论坛](https://www.ibm.com/developerworks/community/forums/html/forum?id=11111111-0000-0000-0000-000000000778&ps=25)
-   [Getting started with WebSphere eXtreme Scale V8.6.x](https://www.youtube.com/watch?v=xw7Ck3c40rA)
-   [Adding cache with WebSphere eXtreme Scale - Liberty Deployment](https://www.youtube.com/watch?v=I1g88bnstn8)
-   [Setting up a WAS client to work with an WebSphere eXtreme Scale Liberty Deployment DynaCache grid ](https://www.youtube.com/watch?v=RvhcknNX-4Q)
-   [Setting up a WAS client to work with an WebSphere eXtreme Scale Liberty Deployment Session Cache grid](https://www.youtube.com/watch?v=GKTy9I66Klc)

