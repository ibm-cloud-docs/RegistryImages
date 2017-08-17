---

copyright:
  years: 2015, 2017
lastupdated: "2017-04-19"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}

# Getting started with the ibm-node-strong-pm image
{: #getting_started_node_strong}

The IBM® Node with StrongLoop \(**ibm-node-strong-pm**\) image is supplied for {{site.data.keyword.containerlong}}.
{:shortdesc}

## How it works 
{: #how_it_works}

Use the **ibm-node-strong-pm** image to create a single container that deploys a running instance of the [StrongLoop Process Manager](https://www.strongloop.com). The Strongloop Process Manager enables you to deploy, manage, and monitor Node.js apps in the IBM Cloud that were implemented on a remote machine.
{:shortdesc}

The remote machine must have installed an instance of StrongLoop Arc. StrongLoop Arc is a graphical user interface for the StrongLoop platform and includes tools for building, profiling, and monitoring Node.js apps. After you built an app with StrongLoop Arc, you push and deploy it to the container in {{site.data.keyword.Bluemix_short}} that runs the StrongLoop Process Manager. To establish a connection between the remote machine and the container, you must bind a public IP address to the container and set up this IP address as your StrongLoop Process Manager server in StrongLoop Arc. When your app is deployed through the StrongLoop Process Manager, you can monitor metrics and the performance of your app in the StrongLoop Process Manager. To update or scale your app, you must change it by using StrongLoop Arc, and then push your changes to the container.

**Note:** The **ibm-node-strong-pm** is not supported to be used with container groups in {{site.data.keyword.Bluemix_notm}}.

## What is included
{: #whats_included}

-   All software packages that are included in the **ibmnode:latest** image. See the [What is included](/docs/services/RegistryImages/ibmliberty/index.html#whats_included) in the Getting started with the **ibmnode** image for {{site.data.keyword.Bluemix_notm}} topic.
-   StrongLoop Process Manager

## Getting started
{: #how_to_get_started}

You can use the **ibm-node-strong-pm** image from the {{site.data.keyword.Bluemix_notm}} Catalog to try out a single container in {{site.data.keyword.containershort_notm}}. The image is started with a running instance of the StrongLoop Process Manager, which you can access as soon as the container is available. Using an instance of StrongLoop Arc that is connected to that Process Manager, you can push a Node.js application and access it.
{:shortdesc}

**Note:** Before you begin, review the terms of use for IBM certified images in the Usage section of the [websphere-liberty Docker library](https://github.com/docker-library/docs/tree/master/websphere-liberty).

1.  From the catalog, select **Containers** and choose the **ibm-node-strong-pm** image to build your container from.
2.  Create a single container.
    -   In the **Size** field, select a container size of at least 1 GB memory \(**Small**\).
    -   In the **Public ports** field, enter the ports 8701 and 3001. These ports are automatically exposed when your container is deployed in {{site.data.keyword.Bluemix_notm}} and a prerequisite to bind a public IP address to the container. Port 8701 is used to access the StrongLoop Process Manager in the container. Port 3001 is the port on which the app listens after it is deployed to the StrongLoop Process Manager.

        **Note:** To deploy additional apps to the StrongLoop Process Manager, expose more ports. The ports must be consecutive, starting with 3002. For example 3002, 3003, 3004, and so on.

    -   For further information, see [Creating a single container by using the Bluemix Dashboard](/docs/containers/container_single_ui.html#gui).
3.  Bind a public IP address to the container. For more information, see [Requesting and binding IP addresses](/docs/containers/container_single_ui.html#container_cli_ips).

## Setting up StrongLoop Arc on a remote machine
{: #UsingSLA}

After you create a single container from the **ibm-node-strong-pm** image in {{site.data.keyword.Bluemix_notm}}, set up StrongLoop Arc on your remote machine.
{:shortdesc}

Before you begin, install Node.js and the Node package manager \(npm\) on your remote machine.

1.  Download and install StrongLoop by entering the following command. StrongLoop includes all the packages that are required to run Arc.

    ```
    npm install -g strongloop
    ```
    {: pre}

2.  After StrongLoop is installed, run StrongLoop Arc.

    ```
    slc arc
    ```
    {: pre}

3.  Register or log in to StrongLoop, when you are prompted.
4.  Add the StrongLoop Process Manager that runs in your container to the Process Manager list in StrongLoop Arc.
    1.  From the Arc module selector, navigate to **Process Manager**.
    2.  Add the public IP address that you bound to your container and the public port 8701 to the Arc Process Manager.
    3.  Click **Activate**.

## Pushing a Node.js app to your container by using StrongLoop Arc
{: #pushing_app_to_container}

After you connect the StrongLoop Process Manager that runs in your container with the StrongLoop Arc client on your remote machine, build and push your Node.js apps to {{site.data.keyword.Bluemix_notm}}.
{:shortdesc}

1.  From the Arc module selector, navigate to **Build and Deploy**.
2.  Build your app by following the Arc user interface instructions.

    **Important:** Ensure that your Node.js app that you created with StrongLoop Arc is listening on both the environment variable PORT and the port that you exposed when you created the container.

    Example

    ```
    app.listen(process.env.PORT || 3001);
    ```
    {: pre}

3.  Deploy your app by following the Arc user interface instructions. Select the Process Manager that you configured to deploy your app to Bluemix.
4.  Wait for the deployment process to finish.
5.  Access your app.
    1.  Open a web browser.
    2.  Enter the IP address of your container and port in the following format <var class="keyword varname">IP_ADDRESS:PORT</var>.

        Example

        ```
        192.0.2.26:3001
        ```
        {: pre}

    3.  Repeat these steps for every app that you deployed to {{site.data.keyword.Bluemix_notm}}.
6.  Use Arc's tracing, metrics, and profiler to get performance information about your apps as they run on {{site.data.keyword.Bluemix_notm}}. See [Operating Node applications](https://docs.strongloop.com/display/SLC/Operating+Node+applications) in the StrongLoop documentation for more information.

## **ibm-node-strong-pm** Dockerfile reference 
{: #reference_dockerfile}

This Dockerfile was used to create the **ibm-node-strong-pm** image in the {{site.data.keyword.Bluemix_notm}} Catalog. This information is for reference only. The other files that are included in the built version of this image are not provided.

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


