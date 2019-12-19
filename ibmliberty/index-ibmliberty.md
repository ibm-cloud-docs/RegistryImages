---

copyright:
  years: 2017, 2019
lastupdated: "2019-12-16"

keywords: registry, IBM Liberty, ibmliberty, container image, IBM WebSphere Application Server Liberty, liberty, public images,

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
{:term: .term}

# Getting started with the `ibm/liberty` image
{: #ibmliberty}

The {{site.data.keyword.IBM}} {{site.data.keyword.appserver_short}} Liberty \(`ibm/liberty`\) images are supplied for {{site.data.keyword.containerlong}}.
{:shortdesc}

You can access the images that are provided by {{site.data.keyword.IBM_notm}} by using the command line, see [{{site.data.keyword.IBM_notm}} public images](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## How it works
{: #ibmliberty_how_it_works}

You can use an `ibm/liberty` image as a parent to create your own image and deploy your own WAR, EAR, or OSGi apps based on Java in an {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty container.
{:shortdesc}

## What is included
{: #ibmliberty_whats_included}

Every Liberty image provides the following software packages.
{:shortdesc}

- {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} for Developers Liberty
- IBM Java Runtime Environment 8.0

The specific Liberty features that are installed in the image depend on the tag that you select. The following table shows the features that are included in each of the `ibm/liberty` images. For more information about each feature, see the [Liberty features overview in {{site.data.keyword.IBM_notm}} Knowledge Center ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSAW57_liberty/com.ibm.websphere.wlp.nd.multiplatform.doc/ae/rwlp_feat.html).

|Tag|Description|
|---|-----------|
|All `ibm/liberty` images|All `ibm/liberty` images include the following features: </br></br>`appSecurity-2.0`</br></br>`collectiveMember-1.0`</br></br>`localConnector-1.0`</br></br>`IdapRegistry-3.0`</br></br>`monitor-1.0`</br></br>`requestTiming-1.0`</br></br>`restConnector-1.0`</br></br>`sessionDatabase-1.0`</br></br>`ssl-1.0`</br></br>`webCache-1.0`|
|`ibm/liberty:latest`|This image points to the `ibm/liberty:javaee8` image.|
|`ibm/liberty:microProfile1`|This image contains the features that provide the capabilities that are specified by [MicroProfile 1.x ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://microprofile.io/).|
|`ibm/liberty:microProfile2`|This image contains the features that provide the capabilities that are specified by [MicroProfile 2.x ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://microprofile.io/).|
|`ibm/liberty:springBoot1`|This image contains the features that provide the capabilities that are specified by [Spring Boot 1.x ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://spring.io/projects/spring-boot).|
|`ibm/liberty:springBoot2`|This image contains the features that provide the capabilities that are specified by [Spring Boot 2.x ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://spring.io/projects/spring-boot).|
|`ibm/liberty:webProfile7`|This image includes all features that are required for Java EE 7 Web Profile compliance.|
|`ibm/liberty:webProfile8`|This image includes all features that are required for Java EE 8 Web Profile compliance.|
|`ibm/liberty:javaee7`|This image includes all features from the `ibm/liberty:webProfile7` image, plus features that are required for Java EE 7 Full Platform compliance.|
|`ibm/liberty:javaee8`|This image includes all features from the `ibm/liberty:webProfile8` image, plus features that are required for Java EE 8 Full Platform compliance.|
{: caption="Table 1. The features that are included in each of the <code>ibm/liberty</code> images" caption-side="top"}

## Usage restrictions
{: #ibmliberty_usage}

The following table shows the restrictions that apply to the free usage of the `ibm/liberty` image in {{site.data.keyword.cloud_notm}}.
{:shortdesc}

The pricing for the `ibm/liberty` image is independent of the pricing for the containers that you use in {{site.data.keyword.cloud_notm}}.
{:tip}

|Environment|Free usage restrictions|
|-----------|-----------------------|
|Development|**Unlimited** free usage of the `ibm/liberty` image.|
|Production|Free usage of the `ibm/liberty` image is limited to a **maximum of 2 GB Java heap space** across all container instances that run the image. For example, you can have 2 x 1 GB, or 4 x 512 MB heap liberty instances for free.|
{: caption="Table 2. Pricing" caption-side="top"}

To monitor the Java heap usage of your container instances, see [Monitoring the Java heap space usage for a container with the CLI](#ibmliberty_monitor_heap).

Review the terms of use for {{site.data.keyword.IBM_notm}} certified images in the License section of the [websphere-liberty image ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://hub.docker.com/_/websphere-liberty/) on Docker Hub.

## Getting started
{: #ibmliberty_get_started}

Use one of the free `ibm/liberty` images from the {{site.data.keyword.cloud_notm}} catalog or select your own production-licensed image to create a single container or a container group.
{:shortdesc}

Before you begin, review the [usage restrictions](#ibmliberty_usage) for the `ibm/liberty` images.
{: important}

1. From the catalog, select **Containers** > **IBM Cloud Container Registry** > **IBM Public Repositories** in the side pane. Search for the `ibm/liberty` image to build your container from. If you created your own production-licensed image and deployed it to {{site.data.keyword.cloud_notm}}, select this image from the catalog. The container creation page opens.
2. From the **TAG/VERSION** list, select the version of the `ibm/liberty` image that you want to use.
3. For more information about building containers from images, setting up clusters, and deploying apps in clusters, use the following links:

    - [Building containers from images](/docs/containers?topic=containers-images#images)
    - [Getting started with {{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-getting-started#getting-started)
    - [Deploying apps in clusters](/docs/containers?topic=containers-app#app)

    The `ibm/liberty` image requires that port 9080 is public. When you create a container from the {{site.data.keyword.cloud_notm}} Dashboard, the port is added in the **Public Port** field by default. If you create a container from the CLI, make the port public by running the `kubectl run` command with the `--port=9080` option.
    {:tip}

## Monitoring the Java heap space usage for a container with the CLI
{: #ibmliberty_monitor_heap}

After you create a container from the `ibm/liberty` image, you can view metrics on a particular pod and its containers and review the Java heap usage. The Java heap space is the memory that is available to the Java application at run time.
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

3. To review the Java heap usage, you must access the **RSS** memory stat, see [Kubernetes: Get a shell to a running container ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/). The Java heap usage is displayed in KB. If your heap usage is under 2097152 KB (2 GB) across all instances, then you do not have to purchase a {{site.data.keyword.appserver_short}} license.

4. Adjust the maximum heap usage for your {{site.data.keyword.appserver_short}} instance. For more information, see [Setting generic JVM arguments in the {{site.data.keyword.appserver_short}} V8.5 Liberty profile ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://www-01.ibm.com/support/docview.wss?uid=swg21596474).

## Getting a {{site.data.keyword.appserver_short}} license
{: #ibm/liberty_license}

{{site.data.keyword.appserver_short}} licenses are based on the number of Processor Value Units \(PVUs\) that you need. PVU is a unit of measurement for the licensing of {{site.data.keyword.IBM_notm}} Middleware software. The number of PVUs indicates the number of processors \(cores\) that are available to the software.
{:shortdesc}

Every container size in {{site.data.keyword.cloud_notm}} requires a specific number of PVU entitlements that must be available in the {{site.data.keyword.appserver_short}} license. Therefore, you must plan your `ibm/liberty` containers prior to purchasing the license.

To purchase a {{site.data.keyword.appserver_short}} License, contact [{{site.data.keyword.IBM_notm}} Service ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/us-en/marketplace/java-ee-runtime/purchase). If you already have a license for {{site.data.keyword.appserver_short}} v8.5 or newer, then you can use any unused PVUs from your existing entitlement for the deployment of your container.

If you find that you require more PVUs after you purchased the license, you can increase the amount by contacting [{{site.data.keyword.IBM_notm}} Service ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/us-en/marketplace/java-ee-runtime/purchase).

## Creating a production-licensed `ibm/liberty` image to be used with {{site.data.keyword.containershort_notm}}
{: #ibmliberty_prod_image}

Use your {{site.data.keyword.appserver_short}} license to create a production-licensed `ibm/liberty` image that you can use with {{site.data.keyword.containershort_notm}}. Choose between one of the following tasks.
{:shortdesc}

- [Upgrade the image from Docker Hub to a production image ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade).
- [Build your own production-licensed image ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://github.com/WASdev/ci.docker/tree/master/ga).

After you create a production-licensed image, [push the image to your private registry](/docs/services/Registry?topic=registry-getting-started#getting-started) to use it with {{site.data.keyword.containershort_notm}}.

## Creating an image from the provided images
{: #ibmliberty_creating_image}

You can use one of the `ibm/liberty` images as a parent for creating a child image that includes your own app code. Customize the sample Dockerfile and build your image on your computer. Then, you can add your image to your organization's private images registry and create containers with it.
{:shortdesc}

Before you begin, consider the following steps.

- Build your app code into a WAR, EAR, or OSGi file.
- Copy the file to the directory, from which you want to build your image.

To create an image with your app code from the `ibm/liberty` image, complete the following steps:

1. With a text editor, create a file that is named `Dockerfile` and copy the following information into it.

   ```
   FROM icr.io/ibm/liberty:<tag>
   COPY <app_name>.<file_extension> /config/dropins/
   ```
   {: pre}

    The directory `/config` is a shortcut for `/opt/ibm/wlp/usr/servers/defaultServer`.
    {: note}

2. Replace `<tag>` with the version of the `ibm/liberty` image that includes the features that your app requires.

3. Replace `<app_name>` with the name of your app file.

4. Replace `<file_extension>` with either `.war`, `.ear`, or `.eba`.

5. Add any other dependencies for your app to the Dockerfile.

6. Build and push the image to your private images registry. For more information, see [Getting started with {{site.data.keyword.registrylong_notm}}](/docs/services/Registry?topic=registry-getting-started#getting-started).

All `ibm/liberty` images are configured to write Liberty log files to the directory `/logs` inside the container. All other files that are written by the Liberty server, are created in the directory `/opt/ibm/wlp/output/defaultServer`. You can access these files by using the shortcut `/output`.
{:tip}

## `ibm/liberty` Dockerfile reference
{: #ibmliberty_reference_dockerfile}

The `ibm/liberty` image Dockerfiles can be found in the [WASdev/ci.docker GitHub repository ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://github.com/WASdev/ci.docker/tree/master/ga) for reference.
{:shortdesc}
