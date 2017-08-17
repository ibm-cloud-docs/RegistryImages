---

copyright:
  years: 2016, 2017
lastupdated: "2017-06-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# Getting started with the **ibm-integration-bus** image
{: #iib}

The **ibm-integration-bus** image is supplied for {{site.data.keyword.containerlong}}. It includes the IBM Integration Bus for Developers edition.
{:shortdesc}


## How it works
{: #how_it_works}

IBM Integration Bus for Developers contains everything that you require so that you can start developing your own integration solutions.
{:shortdesc}

After you create an integration solution, you can use the **ibm-integration-bus** image to provision a single container in {{site.data.keyword.Bluemix_notm}}. You can then deploy your integration solution into this container by using the web UI or from a terminal.

**Note**: You can use the **ibm-integration-bus** image for development and unit test only. You can also use the image to explore the product, learn from tutorials, and evaluate the contribution that IBM Integration Bus can make to your organization.


## What is included
{: #whats_included}

This IBM Integration Bus image contains the software package for IBM Integration Bus for Developers Version 10 \(also known as Developer Edition\), which is a full-function version of the product that you can use for development and unit test. You can download this version at no charge and you are free to use it within the terms of the license.
{:shortdesc}

IBM Integration Bus for Developers Version 10.0 is available on Windows™ 64-bit operating systems and Linux™ on x86-64 operating systems. All product prerequisites are included in the download package.

For information about the features that are included in IBM Integration Bus for Developers, see [IBM Integration Bus in the IBM Knowledge Center ![External link icon](../../../icons/launch-glyph.svg "External link icon")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.msgbroker.helphome.doc/help_home_msgbroker.htm){: new_window}.

You can access the Developer Edition from the [IBM Integration Bus for Developers download page ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/marketing/iwm/iwm/web/pick.do?source=swg-wmbfd){: new_window}.


## Usage restrictions
{: #usage}

The following table shows the restrictions that apply to the free usage of the **ibm-integration-bus** image in {{site.data.keyword.Bluemix_notm}}.
{:shortdesc}

**Note**: The pricing for the **ibm-integration-bus** image is independent of the pricing for the containers that you use in {{site.data.keyword.Bluemix_notm}}.

|Environment|Free usage restrictions|
|-----------|-----------------------|
|Development|**Unlimited** free usage of the **ibm-integration-bus** image to create single containers for development and unit test.|
|Production|The Developer Edition is limited to processing one transaction per second, and is not supported for use in a production environment.|
{: caption="Table 1. Restrictions on the free use of the ibm-integration-bus image" caption-side="top"}


## License
{: #license}

Information about licenses.
{:shortdesc}

*   The Dockerfiles and associated scripts are licensed under the [Apache License 2.0 ![External link icon](../../../icons/launch-glyph.svg "External link icon")](http://www.apache.org/licenses/LICENSE-2.0.html){: new_window}.
*   [License information for IBM Integration Bus for Developers Version 10.0 ![External link icon](../../../icons/launch-glyph.svg "External link icon")](http://www.ibm.com/software/sla/sladb.nsf/displaylis/1BD32B141C9FDAE38525802C0055A956?OpenDocument){: new_window}.

    **Note**: The license does not allow further distribution. The terms for IBM Integration Bus in the image restrict usage for development and unit testing.


## Getting started
{: #get_started}

Use the **ibm-integration-bus** image from the {{site.data.keyword.Bluemix_notm}} catalog or select your own production-licensed image to create a single container.
{:shortdesc}

**Important**: Before you begin, review the [usage restrictions](#usage) for the **ibm-integration-bus** image.

**Note**: You can use native Docker commands in all steps that are marked with an asterisk (\*).

   Complete the following steps to run IBM Integration Bus in a container:

1.  [Provision a container that is based on the ibm-integration-bus image from the {{site.data.keyword.Bluemix_notm}} UI](#provision)
2.  [Validate the container and the IBM Integration Bus installation](#validate)
3.  [Create and deploy a flow](#createdeployflow)
4.  [Monitor IBM Integration Bus logs inside the container](#monitor)
5.  [Manage IBM Integration Bus resources that are running in the container](#manage)


## Provision a container that is based on the **ibm-integration-bus** image
{: #provision}

Provision a Docker container in {{site.data.keyword.Bluemix_notm}}, based on the **ibm-integration-bus** image that is provided by {{site.data.keyword.IBM_notm}}.
{:shortdesc}

  Complete the following steps:

1.  Log in to {{site.data.keyword.Bluemix_notm}}.
2.  From the catalog, select **Containers** and choose the **ibm-integration-bus** image to build your container from.
3.  Select **Single** to create a single instance container, which can be used for development and testing purposes.
4.  Enter the name of the container; for example, `iib`.
5.  Select the size of the container.
6.  In the **Public IP address** field, select *Bind Public IP*.
7.  In the **Public Ports** field, specify `4414, 7800`.
8.  Expand the **Advanced** options, and click **Add new environment variables**.
9.  Add the following environment variables:

    <ul>
      <li>LICENSE with a value of <i>accept</i>
      <li>NODENAME with a value of &lt;MYNODE&gt; &lpar;where &lt;MYNODE&gt; is your chosen node name&rpar;</li>
      <li>LOG_LOCATIONS with a value of <i>/var/log/syslog</i>
    </ul>

    **Note**: If you do not specify values for the LICENSE and NODENAME properties, the container is created but does not start.

    The LOG_LOCATIONS property specifies the logs inside the container that you want to expose through the {{site.data.keyword.Bluemix_notm}} UI.
10. Click **Create** and then wait for the container to start running.


## Validate the container and the IBM Integration Bus installation
{: #validate}

After the container that runs IBM Integration Bus is deployed in {{site.data.keyword.Bluemix_notm}}, check the status of the container and validate the IBM Integration Bus installation.
{:shortdesc}

  Complete the following steps to verify the setup and configuration of IBM Integration Bus in the container:
  
1.  Set up the {{site.data.keyword.containershort_notm}} CLI, as described in [Setting up the {{site.data.keyword.containershort_notm}} plug-in \(`bx ic`\) to use the native Docker CLI](/docs/containers/container_cli_cfic_install.html).

2.  From a terminal, log in to {{site.data.keyword.Bluemix_notm}}. Run the following commands:

    <ol>
       <li>
          <pre class="codeblock">bx login</pre>
       </li>
       <li>
          <pre class="codeblock">bx ic init</pre>
       </li>
    </ol>

3.  Check the status of the container.\*

    ```
    bx ic ps
    ```
    {: pre}

    **Note**: In this command, you can replace **bx ic** with **docker** when you [log in to {{site.data.keyword.containershort_notm}}](/docs/containers/container_cli_login.html) and set your environment variables to use native Docker commands. You can use native Docker commands in all steps that are marked with an asterisk (\*).

    ```
    $ bx ic ps
    CONTAINER ID        IMAGE                                                       COMMAND             CREATED             STATUS                 PORTS                                                        NAMES
    46d9ec1e-952        registry.ng.bluemix.net/ibm-integration-bus:latest   ""                  2 minutes ago       Running a minute ago   xxx.xxx.xxx.xxx:4414->4414/tcp, xxx.xxx.xxx.xxx:7800->7800/tcp   iib
    ```
    {: screen}

4.  Attach a Bash session to your container.\*

    ```
    bx ic exec -it <container_name> /bin/bash
    ```
    {: pre}

    where *container_name* is the name of the container.

    For example:

    ```
    $ bx ic exec -it iib /bin/bash
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

5.  Initialize the environment by running the **mqsiprofile** command, as described in [Command environment: Linux and UNIX™ systems ![External link icon](../../../icons/launch-glyph.svg "External link icon")](http://www.ibm.com/support/knowledgecenter/SSMKHH_9.0.0/com.ibm.etools.mft.doc/an26230_.htm){: new_window}.

    The command to initialize the environment is available in the directory: `/opt/ibm/iib-10.0.0.6/server/bin/`

    For example:

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

6.  Run the **mqsilist** command to show the status of your node, which should be listed as `running`. For example:

    ```
    (IIB_10:)iibuser@instance-0098858e:/$ mqsilist
    BIP1325I: Integration node 'MYNODE' with administration URI 'http://instance-0098858e:4414' is running.
    BIP8071I: Successful command completion.
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

Your container is now running, and you can deploy integration solutions to it by using any of the supported methods.


## Monitor IBM Integration Bus logs inside the container
{: #monitor}

Monitor the logs through the {{site.data.keyword.Bluemix_notm}} UI or from the command line.
{:shortdesc}

Before you start, configure the {{site.data.keyword.containershort_notm}} CLI to monitor the logs from the command line. For more information, see [Setting up the {{site.data.keyword.containershort_notm}} plug-in (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

The **ibm-integration-bus** image is configured to output IBM Integration Bus messages into `/var/log/syslog` inside the container.

  Choose one of the following ways to monitor IBM Integration Bus logs:

*  If you configured the environment variable LOG_LOCATIONS when you provisioned the container, you can monitor the logs from the {{site.data.keyword.Bluemix_notm}} UI.

  Complete the following steps to access the logs:

  <ol>
  <li>From the container dashboard, select the container that you want to monitor.</li>
  <li>Select <b>Monitoring and Logging</b>.</li>
  <li>Select <b>Logging</b>. The dashboard lists the log entries.</li>
  <li>To customize the logs view, select <b>Advanced View</b> in the Logging page to open Kibana. For more information, see
  <a href="https://console.bluemix.net/docs/containers/container_planning.html#container_planning_monlog" target="_blank">Monitoring and logging</a> to get started.
  </li>
  </ol>


*  From a terminal, use the Docker or the IBM Bluemix Container Service CLI to monitor the messages. Complete the following steps:

<ol>
 <li>
      Configure your terminal.

        <ul>
        <li>To use the IBM Bluemix Container Service CLI to manage containers in Bluemix while still using the Docker CLI directly to manage your local Docker host, complete the following steps to set up the environment:
           <ol>
             <li>
               <pre class="codeblock">bx login</pre>
             </li>
             <li>
               <pre class="codeblock">bx ic init</pre>
             </li>
           </ol>
        </li>
        <li>To use the Docker CLI to manage containers in Bluemix, complete the following steps:
          <ol>
            <li>
              <pre class="codeblock">bx login</pre>
            </li>
            <li>
              <pre class="codeblock">bx ic init</pre>
               Copy the values that are provided for the following environment variables: &lt;DOCKER_HOST&gt;, &lt;DOCKER_CERT_PATH&gt;, and &lt;DOCKER_TLS_VERIFY&gt;.
            </li>
            <li>
            Override the local Docker environment by setting the following variables to connect to IBM Bluemix Container Service:
               <ul>
                 <li><pre class="codeblock">export DOCKER_HOST=&lt;your_host_value&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;your_cert_path_value&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
               </ul>
                <b>Note</b>: Only some Docker commands are supported by this option.
            </li>
          </ol>
        </li>
      </ul>
</li>
<li>
      Access the log. Choose one of the following options:

      <ul>
         <li>If your terminal is configured to run IBM Bluemix Container Service CLI commands, run the following command:

            <pre class="codeblock">bx ic exec &lt;container_id&gt; tail -f /var/log/syslog</pre>

            where *container_id* is the name of your container.

            For example:

            <pre class="screen">bx ic exec iib tail -f /var/log/syslog</pre>

          </li>
          <li>
           If your terminal is configured to run Docker CLI commands, run the following command:

            <pre class="codeblock">docker exec &lt;container_id&gt; tail -f /var/log/syslog</pre>

            where *container_id* is the name of your container.

            For example:

            <pre class="screen">docker exec iib tail -f /var/log/syslog</pre>

          </li>
        </ul>
       </li>
     </ol>


## Manage IBM Integration Bus resources that are running in the container
{: #manage}

Launch the IBM Integration Bus web UI or run commands from a terminal to manage IBM Integration Bus resources that are running in a container.
{:shortdesc}

  Choose one of the following options to manage IBM Integration Bus resources in the container:

*  [Launch the IBM Integration Bus Web UI](#launchwebUI) and manage IBM Integration Bus graphically.
*  From a terminal, use the Docker or the {{site.data.keyword.containershort_notm}} CLI to manage IBM Integration Bus resources:
    *   [Run administration commands in the container by using the IBM Bluemix Container Service CLI](#admin_commands_containers_cli)
    *   [Run administration commands in the container by using the Docker CLI](#admin_commands_docker_cli)


## Launch the IBM Integration Bus Web UI
{: #launchwebUI}

Launch the web UI to manage IBM Integration Bus resources that are running in the container from your browser.
{:shortdesc}

Follow these steps to connect a browser to your host, which you exposed in [Getting started with the ibm-integration-bus image for {{site.data.keyword.Bluemix_notm}}](#get_started). The IBM Integration Bus web user interface is displayed.

1.  Select your container from the {{site.data.keyword.Bluemix_notm}} UI, and check if the status of the container is `Running`.
2.  Check the container details to find the Public IP.
3.  Open a web browser with the following URL:

    ```
    http://<DockerContainerPublicIP>:4414
    ```
    {: pre}

    where *DockerContainerPublicIP* is the public IP of the container that you checked in the previous step.

4.  The browser opens the web user interface, and you are ready to administer IBM Integration Bus.


## Run administration commands in the container by using the IBM Bluemix Container Service CLI
{: #admin_commands_containers_cli}

Use the {{site.data.keyword.containershort_notm}} CLI to run IBM Integration Bus administration commands directly in a container.
{:shortdesc}

Before you start, configure the {{site.data.keyword.containershort_notm}} CLI to run administration commands from the command line. For more information, see [Setting up the IBM Containers plug-in (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

  Complete the following steps to set up a terminal to run **bx ic** commands to manage IBM Integration Bus:

1.  From a terminal, log in to {{site.data.keyword.Bluemix_notm}}. Run the following commands:

      <ol>
        <li>
           <pre class="codeblock">bx login</pre>
        </li>
        <li>
           <pre class="codeblock">bx ic init</pre>
        </li>
       </ol>
           
2.  Attach a Bash session to your container to set up an interactive session.\*

    ```
    bx ic exec -it <container_name> /bin/bash
    ```
    {: pre}

    where *container_name* is the name of the container.

    For example:

    ```
    bx ic exec -it iib /bin/bash
    ```
    {: screen}

    You can source the mqsiprofile and run your commands from this point, in a shell inside the container.

3.  Alternatively, use the  **bx ic exec** command to run a non-interactive Bash session that can run any of the IBM Integration Bus commands.\*

    For example:

    ```
    bx ic exec iib <container_name> /bin/bash -c mqsilist
    ```
    {: screen}


## Run administration commands in the container by using the Docker CLI
{: #admin_commands_docker_cli}

Use the Docker CLI to run IBM Integration Bus administration commands directly in a container.
{:shortdesc}

Before you start, configure the {{site.data.keyword.containershort_notm}} CLI to run administration commands from the command line. For more information, see [Setting up the {{site.data.keyword.containershort_notm}} plug-in (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

  Complete the following steps to set up a terminal to run Docker commands to manage IBM Integration Bus:

1.  From a terminal, log in to {{site.data.keyword.Bluemix_notm}}. Run the following commands:

   <ol>
     <li>
       <pre class="codeblock">bx login</pre>
     </li>
     <li>
       <pre class="codeblock">bx ic init</pre>

      <p>Copy the values that are provided for the following environment variables: &lt;DOCKER_HOST&gt;, &lt;DOCKER_CERT_PATH&gt;, and &lt;DOCKER_TLS_VERIFY&gt;.</p>
      </li>
      <li>  Override the local Docker environment by setting the following variables to connect to IBM Bluemix Container Service:
        <ul>
           <li><pre class="codeblock">export DOCKER_HOST=&lt;your_host_value&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;your_cert_path_value&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
        </ul>

          <p><b>Note</b>: Only some Docker commands are supported by this option.</p>
     </li>
    </ol>

2.  Attach a Bash session to your container to set up an interactive session. Run the following command:

    ```
    docker exec -it <container_name> /bin/bash
    ```
    {: pre}

    where *container_name* is the name of the container.

    For example:

    ```
    docker exec -it iib /bin/bash
    ```
    {: screen}

    You can source the mqsiprofile and run your commands from this point, in a shell inside the container.

3.  You can source the mqsiprofile and run your commands from this point, in a shell inside the container.

    Alternatively, use docker exec to run a non-interactive Bash session that can run any of the IBM Integration Bus commands. 
    
    For example:

    ```
    docker exec iib <container_name> /bin/bash -c mqsilist
    ```
    {: screen}


## Create and deploy a flow
{: #createdeployflow}

Create and deploy a message flow by following the instructions in the IBM Integration Bus Knowledge Center.
{:shortdesc}

For information about how to create a message flow, see [How do I create and manage message flows? ![External link icon](../../../icons/launch-glyph.svg "External link icon")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40150_.htm){: new_window}.

For information about how to deploy a message flow, see [How do I deploy and configure message flows? ![External link icon](../../../icons/launch-glyph.svg "External link icon")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40180_.htm){: new_window}.
