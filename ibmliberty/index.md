---

copyright:
  years: 2017
lastupdated: "2017-09-21"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Getting started with the **ibmliberty** image
{: #ibmliberty}

The IBM® WebSphere® Application Server Liberty \(**ibmliberty**\) images are supplied for {{site.data.keyword.containerlong}}.
{:shortdesc}

## How it works 
{: #how_it_works}

You can use an **ibmliberty** image as a parent to create your own image and deploy your own WAR, EAR, or OSGi apps based on Java in an IBM WebSphere Application Server Liberty container.
{:shortdesc}

## What is included 
{: #whats_included}

Every Liberty image provides the following software packages.
{:shortdesc}

-   IBM WebSphere Application Server for Developers Liberty
-   IBM Java Runtime Environment 8.0

The specific Liberty features that are installed in the image depend on the tag that you select. The following table shows, what features are included in each of the **ibmliberty** images. For more information about each feature, see the [Liberty features overview in IBM Knowledge Center](http://www.ibm.com/support/knowledgecenter/SSAW57_8.5.5/com.ibm.websphere.wlp.nd.doc/ae/rwlp_feat.html).

|Tag|Description|
|---|-----------|
|All **ibmliberty** images|All **ibmliberty** images include the following features. <ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|**ibmliberty:latest**|This image points to the **ibmliberty:javaee7** image.|
|**ibmliberty:webProfile6**|This image includes all features that are required for Java EE6 Web Profile compliance. It also pulls in additional features to bring the contents in to line with the features available for download by using the runtime JAR from [http://wasdev.net/](http://wasdev.net/), most notably the features that are required for OSGi applications.|
|**ibmliberty:webProfile7**|This image includes all features required for Java EE7 Web Profile compliance.|
|**ibmliberty:javaee7**|This image includes all features from the **ibmliberty:webProfile7** image, plus features that are required for Java EE7 Full Platform compliance.|

## Usage restrictions 
{: #usage}

The following table shows the restrictions that apply to the free usage of the **ibmliberty** image in {{site.data.keyword.Bluemix_short}}.
{:shortdesc}

**Note:** The pricing for the **ibmliberty** image is independent of the pricing for the containers that you use in {{site.data.keyword.Bluemix_notm}}.

|Environment|Free usage restrictions|
|-----------|-----------------------|
|Development|**Unlimited** free usage of the **ibmliberty** image.|
|Production|Free usage of the **ibmliberty** image is limited to a **maximum of 2 GB Java heap space** across all container instances that run the image. For example, you can have 2 x 1GB, or 4 x 512 MB heap liberty instances for free.

To monitor the Java heap usage of your container instances, see [Monitoring the Java heap space usage for a container with the CLI](#monitor_heap).


Review the terms of use for IBM certified images in the License section of the [websphere-liberty image](https://hub.docker.com/_/websphere-liberty/) on Docker Hub.

## Getting started 
{: #get_started}

Use one of the free **ibmliberty** images from the {{site.data.keyword.Bluemix_notm}} catalog or select your own production-licensed image to create a single container or a container group.
{:shortdesc}

**Important:** Before you begin, review the [usage restrictions](#usage) for the **ibmliberty** images.

1.  From the catalog, select **Containers** and choose the **ibmliberty** image to build your container from. If you created your own production-licensed image and deployed it to {{site.data.keyword.Bluemix_notm}}, select this image from the catalog. The container creation page opens.
2.  Select the version of the **ibmliberty** image that you want to use from the **TAG/ VERSION** drop down box.
3.  Choose whether to create a single container or a scalable container group. Refer to the following topics for more information about how to create containers.

    -   [Creating a single container by using the {{site.data.keyword.Bluemix_notm}} Dashboard](/docs/containers/container_single_ui.html#gui)
    -   [Creating a container group by using the {{site.data.keyword.Bluemix_notm}} Dashboard](/docs/containers/container_ha.html#container_group_ui)
    
    **Note:** The **ibmliberty** image requires port 9080 to be exposed publicly. When you create a container from the {{site.data.keyword.Bluemix_notm}} Dashboard, the port is added in the **Public Port** field by default. If you create a container from the CLI, expose the port in your `bx ic run` command.


## Monitoring the Java heap space usage for a container with the CLI 
{: #monitor_heap}


After you create a container from the **ibmliberty** image, you can list all running processes and review the Java heap usage. The Java heap space is the memory that is available to the Java application during runtime.
{:shortdesc}

1.  List all running processes inside the container.

    ```
    bx ic top CONTAINER -aux
    ```
    {: pre}

    Your CLI output looks as follows.

    ```    
    USER        PID       %CPU   %MEM    VSZ         RSS        TTY     STAT   START    TIME   COMMAND
    contain+    3322245   3.2    0.0     11522856    216192     ?       Ssl    14:43    0:35   /opt/ibm/java/jre/bin/java -javaagent:/opt/ibm/wlp/bin/tools/ws-javaagent.jar -Djava.awt.headless=true -jar /opt/ibm/wlp/bin/tools/ws-server.jar defaultServer 
    ```
    {: screen}

2.  Review the Java heap usage in the **RSS** column. The Java heap usage is displayed in kilobytes. If your heap usage is under 2097152 kilobytes (2GB) across all instances, then you do not have to purchase a WebSphere Application Server license.
3.  Adjust the maximum heap usage for your WebSphere Application Server instance. See [Setting generic JVM arguments in the WebSphere Application Server V8.5 Liberty profile](http://www-01.ibm.com/support/docview.wss?uid=swg21596474) for more information.

## Getting a WebSphere Application Server license 
{: #license}

WebSphere Application Server licenses are based on the number of Processor Value Units \(PVUs\) that you need. PVU is a unit of measurement for the licensing of IBM Middleware software. The number of PVUs indicates the number of processors \(cores\) that are available to the software.
{:shortdesc}

Every container size in {{site.data.keyword.Bluemix_notm}} requires a specific number of PVU entitlements that must be available in the WebSphere Application Server license. Therefore, you must plan your **ibmliberty** containers prior to purchasing the license.

To purchase a WebSphere Application Server License, contact the [IBM Service](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us). If you already have a license for WebSphere Application Server v8.5 or newer, then you can use any unused PVUs from your existing entitlement for the deployment of your container.

If you find that you require more PVUs after you purchased the license, you can increase the amount by contacting the IBM Service.

## Creating a production-licensed **ibmliberty** image to be used with {{site.data.keyword.containershort_notm}} 
{: #prod_image}

Use your WebSphere Application Server license to create a production-licensed **ibmliberty** image that you can use with {{site.data.keyword.containershort_notm}}. Choose between one of the following tasks.
{:shortdesc}

-   [Upgrade the image from Docker Hub to a production image](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade).
-   [Build your own production-licensed image](https://github.com/WASdev/ci.docker/tree/master/ga/production-install).

After you create a production-licensed image, [push the image to your private {{site.data.keyword.Bluemix_notm}} registry](/docs/services/Registry/index.html) to use it with {{site.data.keyword.containershort_notm}}.

## Creating an image from the provided images 
{: #creating_image}

You can use one of the **ibmliberty** images as a parent for creating a child image that includes your own app code. Customize the sample Dockerfile and build your image on your computer. Then, you can add your image to your organization's private {{site.data.keyword.Bluemix_notm}} images registry and create containers with it.
{:shortdesc}

Before you begin, consider the following steps.

-   Build your app code into a WAR, EAR, or OSGi file.
-   Copy the file to the directory, from which you want to build your image.


To create an image with your app code from the **ibmliberty** image:

2. With a text editor, create a file that is named Dockerfile and copy the following information into it.

    ```
    FROM registry.{{site.data.keyword.domainname}}/ibmliberty:<tag>
    COPY <app_name>.<file_extension> /config/dropins/
    
    ```
    {: screen}

    **Note:** The directory /config is a shortcut for /opt/ibm/wlp/usr/servers/defaultServer.
    
3. Replace <tag\> with the version of the **ibmliberty** image that includes the features that your app requires.

4. Replace <app\_name\> with the name of your app file.

5. Replace <file\_extension\> with either .war, .ear, or .eba.

6. Add any other dependencies for your app to the Dockerfile.

7. Build and push the image to your private {{site.data.keyword.Bluemix_notm}} images registry. For more information, see [Getting started with IBM Bluemix Container Registry](/docs/services/Registry/index.html).

**Note:** All **ibmliberty** images are configured to write Liberty log files to the directory /logs inside the container. All other files that are written by the Liberty server, are created in the directory /opt/ibm/wlp/output/defaultServer. You can access these files by using the shortcut /output.

## **ibmliberty** Dockerfile reference 
{: #reference_dockerfile}

This Dockerfile illustrates how the **ibmliberty:webProfile7** image in {{site.data.keyword.Bluemix_notm}} is built from the public websphere-liberty images on Docker Hub. This information is for reference only.
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
