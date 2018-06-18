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


# Getting started with the **ibm-websphere-extreme-scale** image 
{: #ibm-websphere-extreme-scale}

The **ibm-websphere-extreme-scale** images are supplied for {{site.data.keyword.containerlong}}.

## How it works 
{: #how_it_works}

The IBM WebSphere&reg; eXtreme Scale image is a distributed, in-memory cache feature running within the Liberty runtime environment. It provides Liberty caching servers, caching operation tools, an administrative console, and the out-of-the-box REST administration services based on the latest industry standards and specifications. This image is built on the core IBM WebSphere eXtreme Scale technology. The caching technology loaded within this image fully supports JCache \(JSR107\) specification and compliance.

The following main highlights are available when you use the `ibm-websphere-extreme-scale` image:

-   An easy-to-deploy, easy-to-configure, distributed caching solution based on eXtreme Scale technology
-   Simple and easy-to-use administrative console and REST API
-   Full customization to support all eXtreme Scale caching use cases
-   Easily configurable image that integrates within your caching environment in minutes
-   JCache compliance to support JSR107 caching specification


## What is included 
{: #whats_included}

Every **ibm-websphere-extreme-scale** image provides the following software packages.

-   Ubuntu 16.04 base image, 64-bit
-   IBM WebSphere eXtreme Scale
-   IBM Java Runtime Environment 8.0

The specific features that are installed in the image depend on the tag that you select. The following table shows which code level is included in each of the **ibm-websphere-extreme-scale** images. For more information about these features, see the [eXtreme Scale documentation](http://www.ibm.com/support/knowledgecenter/SSTVLU).

|Tag|Description|
|---|-----------|
|**ibm-websphere-extreme-scale:latest**|Use this tag to specify the latest IBM WebSphere eXtreme Scale image.|
|**ibm-websphere-extreme-scale:8.6.1.x**|Use this tag to specify a specific release level of WebSphere eXtreme Scale.|
{: caption="Table 1. IBM WebSphere eXtreme Scale images" caption-side="top"}

## Usage restrictions 
{: #usage}

The following table shows the restrictions that apply to the free usage of the **ibm-websphere-extreme-scale** image in {{site.data.keyword.Bluemix_notm}}.

|Environment|Usage restrictions|
|-----------|------------------|
|Development|The `ibm-websphere-extreme-scale` image allows for unlimited free usage for development.|
|Production|Production usage of the **ibm-websphere-extreme-scale** image is limited to licensed IBM WebSphere eXtreme Scale product users. For more information about production use, see [View Pricing and Buy](https://www-112.ibm.com/software/howtobuy/buyingtools/paexpress/Express?P0=E1&part_number=D56YTLL,D56YVLL,D572PLL,D572MLL,D0594LL,D059GLL&catalogLocale=en_US&Locale=en_US&country=USA&PT=jsp&CC=USA&VP=&TACTICS=&S_TACT=&S_CMP=&brand=SB05).|
{: caption="Table 2. Usage restrictions for the ibm-websphere-extreme-scale images in {{site.data.keyword.Bluemix_notm}}" caption-side="top"}

**Note:** The pricing for the **ibm-websphere-extreme-scale** image is independent of the pricing for the containers that you use in {{site.data.keyword.Bluemix_notm}}.

Review the terms of use for IBM certified images in the License section of the [License information documents](http://www-03.ibm.com/software/sla/sladb.nsf/displaylis/A44EE6829150B2998525801700516718?OpenDocument).


## Getting started 
{: #get_started}

You can either use the console or the command line to deploy the **ibm-websphere-extreme-scale** image.

This procedure includes the following options for either deploying the image with the console or the command line:

-   Select the image from the {{site.data.keyword.Bluemix_notm}} [console](#bmconsole) from the **Catalog**.
-   Use the cf ic command to start the image with the CF [command line](#bmcommand).

**Important:** Before you begin, review the usage restrictions as described in the previous section for the **ibm-websphere-extreme-scale** images.


### Deploy from the {{site.data.keyword.Bluemix_notm}} console.
{: #bmconsole}

   1.  From the catalog, select **Containers** and choose the **ibm-websphere-extreme-scale** image to start building your container.
   2.  Select the version of the **ibm-websphere-extreme-scale** image that you want to use from the **TAG/Version** drop-down menu.
   3.  Create a single container.

        **Note:** If you want to create more than one container, do not use the **Scalable** option to create two or more eXtreme Scale cache member groups. Instead, select the **Single** option to create multiple containers, and use the eXtreme Scale dashboard UI to join cache members.

        For more information, see [Creating a single container by using the {{site.data.keyword.Bluemix_notm}} Dashboard](/docs/containers/container_single_ui.html#gui).

        1.  From the Create a Container panel, specify a **Container name**; for example, `wxs1`. No spaces or other non-printable characters are allowed in the container name.
        2.  For **Size**, specify the size of the containers used for starting the ibm-eXtreme-scale image.
        3.  For **Public IP address**, specify Request and Bind Public IP.
        4.  Open the Advanced Options panel. Create a volume so that the eXtreme Scale configuration data can be persisted when installing a rolling upgrade at a later time; for example:
            1. Click **Create a volume** from Advanced Options, and specify the **Volume name**. For example, type vol-wxs1.
            2. Click **Add a new environment variable**. The **ibm-websphere-extreme-scale** image requires environment variables that are stored within the container and accessed by authorized users.
            <table>
            <caption>Table 3. Environment variables for the ibm-websphere-extreme-scale image</caption>
               <tr>
                 <th>Variable Name</th>
                 <th>Value to be Specified </th>
               </tr>
               <tr>
                 <td>SECRET_KEY</td>
                 <td>The value of the secret key; for example, <var class="keyword varname">s3cretKey!</var>. This is the secret key used by eXtreme Scale servers to send internal communication across the multiple members in a cache member group. For more information, see the [Cache member group section](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txscreategridtut1.html).</td>
               </tr>
               <tr>
                 <td>XSADMIN_PWD</td>
                 <td>The password of the xsadmin user that logs on to the eXtremeScale dashboard UI; for example, <var class="keyword varname">xsadmin4Me!</var>.</td>
               </tr>
               </table>
  
              **Note:** You can change the password and secret key after the eXtreme Scale container starts. Both the secret key and password have the following password rules. Be sure to enter them both based on the following rule:

              The password and secret key must contain at least one number [`0-9`], one uppercase character [`A-Z`], one special character [`~!@#$%^&*()_-+=:;'[]{}|<>,.?/`] and be at least 10 characters.

              For more information about how to change the password, see the [Administering documentation](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txsadministerxsld.html).

              The port is added in the **Public Port** field by default.

   4.  Click **Create** to create a container from the {{site.data.keyword.Bluemix_notm}} Dashboard.
    
   5.  After the container and eXtreme Scale servers are fully started, log on to the eXtreme Scale dashboard UI and verify the deployment. From the {{site.data.keyword.Bluemix_notm}} console, click a container to view the Public IP. The WebSphere eXtreme Scale container can be accessed from the following URL:

    ```
    https://<Public_IP>:9443
    ```
    {: pre}

   **Note:** It can take anywhere from 5-10 minutes for the server to completely start.


### Deploy the image from the command line.
{: #bmcommand}
 1.  Create a Docker volume to store IBM WebSphere eXtreme Scale persistent data. For example, enter the following command:

  ```
  cf ic volume create <vol-wxs1>
  ```
  {: pre}

 2.  Enter the following command to start your IBM WebSphere eXtreme Scale container in {{site.data.keyword.Bluemix_notm}}:

    **Note:** Replace yourSecretKey and yourXsadminPassword with your own password based on the password rule previously mentioned.

   ```
   cf ic run -itdP --volume <vol-wxs1:/vol> --name <wxs1> -m 1024 -e SECRET_KEY=<yourSecretKey> -e XSADMIN_PWD=<yourXsadminPassword> registry.ng.bluemix.net/ibm-websphere-extreme-scale:latest
   ```
   {: pre}
        
     **Note:** The container name must contain only alphanumeric characters and begin with a letter. Alternatively, you can set <code>-e XSLD_CONTAINER_ALIAS=<wxs1></code> if you require your container name to contain other non-alphanumeric characters.

 3.  Request a public IP to be used for your first IBM WebSphere eXtreme Scale container. Enter the following command to write the IP address:

   ```
   cf ic ip request
   ```
   {: pre}

 4.  Enter the following command to bind your WebSphere eXtreme Scale container to the public IP address that you requested:

   ```
   cf ic ip bind <REQUESTED_IP> wxs1
   ```
   {: pre}

 5.  Enter the following command to verify that the `wxs1` container status is Running:

   ```
   cf ic ps -a
   ```
   {: pre}

 6.  Enter the following command to connect to the `wxs1` container and monitor the nanny log:

   ```
   cf ic exec -it <wxs1> tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
        ```
   {: pre}

    **Note:** It can take anywhere from 5-10 minutes for the server to start.

 7.  After the container and eXtreme Scale servers are fully started, log on to the eXtreme Scale dashboard UI and verify the deployment:

   ```
   https://<REQUESTED_IP>:9443
   ```
   {: pre}


## Adding caching capacity 
{: #caching}

1.  Deploy another image from the console or from the command line following the same steps as before.
2.  After the container and eXtreme Scale servers are fully started, log on to this member’s eXtreme Scale dashboard UI:

    ```
    https://<REQUESTED_IP>:9443
    ```
    {: pre}

3.  Open the Data Cache Members page and under the **Action** column of this member select **Join**.
4.  Enter the first member’s requested public IP, the first member’s secret key, and a unique name for this member in the cache group.
5.  Click **Join**, and wait for the task to complete. When the task is complete, all members are displayed on the Data Cache Members page.

## Performing a rolling upgrade 
{: #rolling_upgrade}

The following steps are required to perform a rolling upgrade of containers that run `ibm-websphere-extreme-scale` in {{site.data.keyword.containershort_notm}}.

**Note:** If your eXtreme Scale cache member group only has one member, remove this {{site.data.keyword.IBM_notm}} container, and create it again from scratch. However, if your eXtreme Scale cache member group has more than one member and multiple catalog servers deployed, and you want to keep the cached data in {{site.data.keyword.containershort_notm}}, complete the following rolling upgrade steps to avoid the loss of any cached data. Upgrade cache members that only have container servers first, and then upgrade cache members that also have catalog servers.

1.  Start a new container called wxsmigrate, and use it for upgrading all existing eXtreme Scale containers. See the [Getting started section](#get_started) on this page for more information. The latest eXtreme Scale update .zip files will be deployed under the /tmp folder on your new wxsmigrate container.
2.  Copy the updated eXtreme Scale .zip files from the wxsmigrate container to your local system, where you are running the {{site.data.keyword.containershort_notm}} CLI plug-in:

    ```
    cf ic cp wxsmigrate:/tmp/xsld-bin-update.zip
    ```
    {: pre}
    
    ```
    cf ic cp wxsmigrate:/tmp/xsld-derby-update.zip
    ```
    {: pre}

3.  Now, for each cache member within a cache member group, complete the following steps one container at a time:

    1. Copy the updated eXtreme Scale .zip files from your local system to the eXtreme Scale container that you want to upgrade:
    
      ```
      cf ic cp  xsld-bin-update.zip  <container_name>:/tmp/xsld-bin-update.zip
      ```
      {: pre}
  
      ```
      cf ic cp  xsld-derby-update.zip  <container_name>:/tmp/xsld-derby-update.zip
      ```
      {: pre}
     
    2. Connect to the container that you want to upgrade: 
    
      ```
      cf ic exec -it <container_name> bash
      ```
      {: pre}

    3. Stop the XSLD servers on the container that is ready for the upgrade.
      
      ```
      /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
      ```
      {: pre}
      
      Wait until the servers are stopped.
      
    4. Unzip the latest eXtreme Scale update code inside the container:
      
      ```
      unzip  -o  /tmp/xsld-bin-update.zip   -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
      
      ```
      unzip  -o  /tmp/xsld-derby-update.zip -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
    
    5. Start the XSLD server again:
    
      ```
      /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
      ```
      {: pre}
      
    6. Monitor the nanny log to ensure servers are started:
    
      ```
      tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
      ```
      {: pre}
      
4. After the upgrade is complete on all IBM containers, remove the wxsmigrate container:

 ```
 cf ic rm -f <wxsmigrate>
 ```
 {: pre}

## Troubleshooting 
{: #troubleshoot}

###Troubleshooting cache member connectivity when host mapping is lost 
{: #troubleshoot_cache}

Complete the following steps when members of a cache group lose connectivity because of a lost host mapping between containers.

1.  Connect to each container:

    ```
    cf ic exec -it <container_name> bash
    ```
    {: pre}

2.  Gather the private IP, host name, and host alias for each member.

    The host alias is the value of the XSLD_CONTAINER_ALIAS environment variable:

    ```
    echo ${<XSLD_CONTAINER_ALIAS>}
    ```
    {: pre}

    The private IP is the IP address returned from running the hostname -I command inside the container:

    ```
    hostname -I
    ```
    {: pre}

    The host name is the host name returned from running the hostname command inside the container.


3.  Run the recoverXSLD.sh script on each member passing in all other member aliases, IPs, and host names:

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/recoverXSLD.sh <member2_alias> <member2_private_ip> <member2_hostname> ... <memberN_alias> <memberN_private_ip> <memberN_hostname>
    ```
    {: pre}

4.  Stop running servers by running stopXSLD.sh on each container in the cache group. Run stopXSLD.sh:

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
    ```
    {: pre}

5.  If stopXSLD.sh does not progress while trying to stop the catalog server, follow the following steps:
    1.  List the catalog server process:

        ```
        ps -ef | grep cs-default
        ```
        {: pre}

    2.  End the catalog server process using the process ID:

        ```
        kill -9 <catalog_server_process_id>
        ```
        {: pre}

    3.  Run stopXSLD.sh again:

        ```
        /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
        ```
        {: pre}

6.  Start XSLD on all containers at around the same time. Run startXSLD.sh:

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
    ```
    {: pre}


### Troubleshooting a stopped Derby replication task 
{: #troubleshoot_derby}

If a Derby replication task has not progressed beyond 45% for more than ten minutes, it is likely due to a network issue. Complete the following steps to troubleshoot the issue:

1.  Use a REST POSTing utility, such as cURL, to cancel the derby replication task.

    ```
    curl -X PUT --header "Content-Type: application/json" --header "Accept: application/json" "https://<PUBLIC_IP>:9445/wxsadmin/v1/task/cancel/<DERBY_REPLICATION_TASK_ID>/force"
    ```
    {: pre}

    **Note:** The <PUBLIC_IP> is the one that was used in the command to start the Derby replication task, and the <DERBY_REPLICATION_TASK_ID> is the task ID output from that command.

2.  Run the derby replication task again.

## Accessing the REST API 
{: #api}

When eXtreme Scale is deployed in {{site.data.keyword.containershort_notm}}, complete the following steps to access the  REST API:

1.  Retrieve the host name of the container you plan to use to connect to the REST API.
    1.  Connect to the container:

        ```
        cf ic exec -it <container_name> bash
        ```
        {: pre}

    2.  List the host name:

        ```
        hostname
        ```
        {: pre}

2.  Retrieve the public IP of the same container.

    If the container does not have a public IP, follow the steps in the Getting started section to provide the container a public IP by using the {{site.data.keyword.Bluemix_notm}} [console](#bmconsole) or [command line](#bmcommand).

3.  Create an entry in the hosts file of your local client computer that maps the public IP and the container host name together. The entry looks like:

    ```
    169.44.8.87 instance-05106102
    ```
    {: pre}

    Typical full path names for hosts files are:

    -   For UNIX: /etc/hosts
    -   For Windows: C:\\Windows\\System32\\drivers\\etc\\hosts
4.  Access the Swagger REST APIs in a browser.
    -   For CRUD operations:

        ```
        https://<host_name>:9444/ibm/api/explorer
        ```
        {: pre}

    -   For administrative operations:

        ```
        https://<host_name>:9445/ibm/api/explorer
        ```
        {: pre}


For samples and for recent updates, visit [WebSphere eXtreme Scale in GitHub](https://github.com/ibmWebsphereExtremeScale). Also, see the following supporting documentation and videos for more help with deploying eXtreme Scale:

-   [IBM WebSphere eXtreme Scale documentation](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/cxsgetstartxsld.html)
-   [XS developerWorks Forum](https://www.ibm.com/developerworks/community/forums/html/forum?id=11111111-0000-0000-0000-000000000778&ps=25)
-   [Getting started with WebSphere eXtreme Scale V8.6.x](https://www.youtube.com/watch?v=xw7Ck3c40rA)
-   [Adding cache with WebSphere eXtreme Scale - Liberty Deployment](https://www.youtube.com/watch?v=I1g88bnstn8)
-   [Setting up a WAS client to work with an WebSphere eXtreme Scale Liberty Deployment DynaCache grid](https://www.youtube.com/watch?v=RvhcknNX-4Q)
-   [Setting up a WAS client to work with an WebSphere eXtreme Scale Liberty Deployment Session Cache grid](https://www.youtube.com/watch?v=GKTy9I66Klc)

