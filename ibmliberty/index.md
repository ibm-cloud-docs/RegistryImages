---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-21"

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

# Getting started with the `ibmliberty` image
{: #ibmliberty}

The IBM® WebSphere® Application Server Liberty \(`ibmliberty`\) images are supplied for {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

You can access the images that are provided by {{site.data.keyword.IBM_notm}} by using the command line, see [IBM public images](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## How it works
{: #ibmliberty_how_it_works}

You can use an `ibmliberty` image as a parent to create your own image and deploy your own WAR, EAR, or OSGi apps based on Java in an IBM WebSphere Application Server Liberty container.
{:shortdesc}

## What is included
{: #ibmliberty_whats_included}

Every Liberty image provides the following software packages.
{:shortdesc}

- IBM WebSphere Application Server for Developers Liberty
- IBM Java Runtime Environment 8.0

The specific Liberty features that are installed in the image depend on the tag that you select. The following table shows the features that are included in each of the `ibmliberty` images. For more information about each feature, see the [Liberty features overview in IBM Knowledge Center ![External link icon](../../../icons/launch-glyph.svg "External link icon")](http://www.ibm.com/support/knowledgecenter/SSAW57_8.5.5/com.ibm.websphere.wlp.nd.doc/ae/rwlp_feat.html).

|Tag|Description|
|---|-----------|
|All `ibmliberty` images|All `ibmliberty` images include the following features. <ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|`ibmliberty:latest`|This image points to the `ibmliberty:javaee7` image.|
|`ibmliberty:microProfile`|This image contains the features that provide the capabilities specified by [MicroProfile ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://microprofile.io).|
|`ibmliberty:webProfile7`|This image includes all features required for Java EE7 Web Profile compliance.|
|`ibmliberty:javaee7`|This image includes all features from the `ibmliberty:webProfile7` image, plus features that are required for Java EE7 Full Platform compliance.|
{: caption="Table 1. The features that are included in each of the `ibmliberty` images" caption-side="top"}

## Usage restrictions
{: #ibmliberty_usage}

The following table shows the restrictions that apply to the free usage of the `ibmliberty` image in {{site.data.keyword.Bluemix_notm}}.
{:shortdesc}

The pricing for the `ibmliberty` image is independent of the pricing for the containers that you use in {{site.data.keyword.Bluemix_notm}}.
{:tip}

|Environment|Free usage restrictions|
|-----------|-----------------------|
|Development|**Unlimited** free usage of the `ibmliberty` image.|
|Production|Free usage of the `ibmliberty` image is limited to a **maximum of 2 GB Java heap space** across all container instances that run the image. For example, you can have 2 x 1 GB, or 4 x 512 MB heap liberty instances for free.|
{: caption="Table 2. Pricing" caption-side="top"}

To monitor the Java heap usage of your container instances, see [Monitoring the Java heap space usage for a container with the CLI](#ibmliberty_monitor_heap).

Review the terms of use for IBM certified images in the License section of the [websphere-liberty image ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://hub.docker.com/_/websphere-liberty/) on Docker Hub.

## Getting started
{: #ibmliberty_get_started}

Use one of the free `ibmliberty` images from the {{site.data.keyword.Bluemix_notm}} catalog or select your own production-licensed image to create a single container or a container group.
{:shortdesc}

Before you begin, review the [usage restrictions](#ibmliberty_usage) for the `ibmliberty` images.
{: important}

1. From the catalog, select **Containers** > **IBM Cloud Container Registry** > **IBM Public Repositories** on the side panel. Search for the `ibmliberty` image to build your container from. If you created your own production-licensed image and deployed it to {{site.data.keyword.Bluemix_notm}}, select this image from the catalog. The container creation page opens.
2. Select the version of the `ibmliberty` image that you want to use from the **TAG/ VERSION** drop down box.
3. For more information about building containers from images, setting up clusters, and deploying apps in clusters, use the following links:

    - [Building containers from images](/docs/containers?topic=containers-images#images)
    - [Getting started with IBM Cloud Kubernetes Service](/docs/containers?topic=containers-container_index#container_index)
    - [Deploying apps in clusters](/docs/containers?topic=containers-app#app)

    The `ibmliberty` image requires port 9080 to be exposed publicly. When you create a container from the {{site.data.keyword.Bluemix_notm}} Dashboard, the port is added in the **Public Port** field by default. If you create a container from the CLI, expose the port in your `kubectl run` command with `--port=9080` option.
    {:tip}

## Monitoring the Java heap space usage for a container with the CLI
{: #ibmliberty_monitor_heap}

After you create a container from the `ibmliberty` image, you can view metrics on a particular pod and its containers and review the Java heap usage. The Java heap space is the memory that is available to the Java application during runtime.
{:shortdesc}

1. Get the name of the pod that you want to view metrics for.
  
   ```
   kubectl get pods
   ```
   {: pre}

2. See metrics on a particular pod and its containers.

   ```
   kubectl top pod POD_NAME --containers
   ```
   {: pre}

3. To review the Java heap usage, you need to access the **RSS** memory stat. Follow the guidelines on how to access a shell of a container, see [Get a Shell to a Running Container ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/). The Java heap usage is displayed in kilobytes. If your heap usage is under 2097152 kilobytes (2GB) across all instances, then you do not have to purchase a WebSphere Application Server license.

4. Adjust the maximum heap usage for your WebSphere Application Server instance. See [Setting generic JVM arguments in the WebSphere Application Server V8.5 Liberty profile ![External link icon](../../../icons/launch-glyph.svg "External link icon")](http://www-01.ibm.com/support/docview.wss?uid=swg21596474) for more information.

## Getting a WebSphere Application Server license
{: #ibmliberty_license}

WebSphere Application Server licenses are based on the number of Processor Value Units \(PVUs\) that you need. PVU is a unit of measurement for the licensing of IBM Middleware software. The number of PVUs indicates the number of processors \(cores\) that are available to the software.
{:shortdesc}

Every container size in {{site.data.keyword.Bluemix_notm}} requires a specific number of PVU entitlements that must be available in the WebSphere Application Server license. Therefore, you must plan your `ibmliberty` containers prior to purchasing the license.

To purchase a WebSphere Application Server License, contact [IBM Service ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us). If you already have a license for WebSphere Application Server v8.5 or newer, then you can use any unused PVUs from your existing entitlement for the deployment of your container.

If you find that you require more PVUs after you purchased the license, you can increase the amount by contacting [IBM Service ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/marketplace/cloud/application-server-on-cloud/purchase/us/en-us).

## Creating a production-licensed `ibmliberty` image to be used with {{site.data.keyword.containershort_notm}}
{: #ibmliberty_prod_image}

Use your WebSphere Application Server license to create a production-licensed `ibmliberty` image that you can use with {{site.data.keyword.containershort_notm}}. Choose between one of the following tasks.
{:shortdesc}

- [Upgrade the image from Docker Hub to a production image ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade).
- [Build your own production-licensed image ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://github.com/WASdev/ci.docker/tree/master/ga).

After you create a production-licensed image, [push the image to your private registry](/docs/services/Registry?topic=registry-index#index) to use it with {{site.data.keyword.containershort_notm}}.

## Creating an image from the provided images
{: #ibmliberty_creating_image}

You can use one of the `ibmliberty` images as a parent for creating a child image that includes your own app code. Customize the sample Dockerfile and build your image on your computer. Then, you can add your image to your organization's private images registry and create containers with it.
{:shortdesc}

Before you begin, consider the following steps.

- Build your app code into a WAR, EAR, or OSGi file.
- Copy the file to the directory, from which you want to build your image.

To create an image with your app code from the `ibmliberty` image, complete the following steps:

1. With a text editor, create a file that is named `Dockerfile` and copy the following information into it.

   ```
   FROM registry.bluemix.net/ibmliberty:<tag>
   COPY <app_name>.<file_extension> /config/dropins/
   ```
   {: pre}

    The directory `/config` is a shortcut for `/opt/ibm/wlp/usr/servers/defaultServer`.
    {: note}

2. Replace `<tag>` with the version of the `ibmliberty` image that includes the features that your app requires.

3. Replace `<app_name>` with the name of your app file.

4. Replace `<file_extension>` with either `.war`, `.ear`, or `.eba`.

5. Add any other dependencies for your app to the Dockerfile.

6. Build and push the image to your private images registry. For more information, see [Getting started with {{site.data.keyword.registrylong_notm}}](/docs/services/Registry?topic=registry-index#index).

All `ibmliberty` images are configured to write Liberty log files to the directory `/logs` inside the container. All other files that are written by the Liberty server, are created in the directory `/opt/ibm/wlp/output/defaultServer`. You can access these files by using the shortcut `/output`.
{:tip}

## `ibmliberty` Dockerfile reference
{: #ibmliberty_reference_dockerfile}

This Dockerfile illustrates how the `ibmliberty:webProfile7` image in {{site.data.keyword.Bluemix_notm}} is built from the public websphere-liberty images on Docker Hub. This information is for reference only.
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
