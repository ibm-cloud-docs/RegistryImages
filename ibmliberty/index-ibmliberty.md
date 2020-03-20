---

copyright:
  years: 2017, 2020
lastupdated: "2020-01-30"

keywords: registry, IBM Liberty, ibmliberty, container image, IBM WebSphere Application Server Liberty, Open Liberty, liberty, public images,

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
{:external: target="_blank" .external}

# Getting started with the `ibm/liberty` image
{: #ibmliberty}

You can access the images that are provided by {{site.data.keyword.IBM_notm}} by using the command line, see [{{site.data.keyword.IBM_notm}} public images](/docs/Registry?topic=registry-public_images#public_images).
{: tip}

## How it works
{: #ibmliberty_how_it_works}

You can use an `ibm/liberty` image as a parent to create your own image and deploy your own Java application (Jarkata EE, MicroProfile or Spring Boot) in an Liberty container. 
{:shortdesc}

The recommended pattern is to build a new application image by having a `Dockerfile` similar to:

   ```
   FROM icr.io/ibm/liberty:<tag>

   # Add my app and config
   COPY --chown=1001:0  Sample1.war /config/dropins/
   COPY --chown=1001:0  server.xml /config/
   
   RUN configure.sh
   ```

You basically just add your application and corresponding configuration, then call the built-in script `configure.sh` to install any needed features (if using the `kernel` based tag) and prime the shared class cache for improved startup performance.

For more information on building applications images you can check out the documentation for [Open Liberty](https://github.com/OpenLiberty/ci.docker#building-an-application-image) or [WebSphere Liberty](https://github.com/WASdev/ci.docker#building-an-application-image),using the tags below as the `FROM` value in your `Dockerfile`.
 
## Tags
{: #ibmliberty_tags}

The following table shows the features that are included in each of the `ibm/liberty` image tags.

|Tag|Description|
|---|-----------|
|`icr.io/ibm/liberty:20.0.0.3-wl-kernel`| This tag contains Red Hat's Universal Base Image 8, IBM Java Runtime Environment 8.0, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 20.0.0.3 with just the `kernel` feature enabled, so you must either call Liberty's `installUtility` or the container's `configure.sh` to add features. See [this page](https://github.com/WASdev/ci.docker#building-an-application-image) for more details. |
|`icr.io/ibm/liberty:20.0.0.3-wl-full`| This tag contains Red Hat's Universal Base Image 8, IBM Java Runtime Environment 8.0, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 20.0.0.3 with all of its features enabled. |
|`icr.io/ibm/liberty:20.0.0.3-ol-kernel`|  This tag contains Red Hat's Universal Base Image 8, AdoptOpenJDK 8 with OpenJ9, and Open Liberty 20.0.0.3 with the kernel and all of its features already loaded. |
|`icr.io/ibm/liberty:20.0.0.3-ol-full`| This tag contains Red Hat's Universal Base Image 8, AdoptOpenJDK 8 with OpenJ9, and Open Liberty 20.0.0.3 with all of its features enabled. |
{: caption="Table 1. Each of the image tags for <code>ibm/liberty</code>" caption-side="top"}

## Getting started
{: #ibmliberty_get_started}

Use your application image, derived from of the free `ibm/liberty` image tags, to create a single container or a container group.
{:shortdesc}

1. From the catalog, select **Containers** > **IBM Cloud Container Registry** > **IBM Public Repositories** in the side pane. Search for your own application image and that was deployed to {{site.data.keyword.cloud_notm}}, select this image from the catalog. The container creation page opens.
2. From the **TAG/VERSION** list, select the version of the image that you want to use.
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

3. To review the Java heap usage, you must access the **RSS** memory stat, see [Kubernetes: Get a shell to a running container](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/){: external}. The Java heap usage is displayed in KB. If your heap usage is under 2097152 KB (2 GB) across all instances, then you do not have to purchase a {{site.data.keyword.appserver_short}} license.

4. Adjust the maximum heap usage for your {{site.data.keyword.appserver_short}} instance. For more information, see [Setting generic JVM arguments in the {{site.data.keyword.appserver_short}} V8.5 Liberty profile](https://www-01.ibm.com/support/docview.wss?uid=swg21596474){: external}.

## License for production use
{: #ibm/liberty_license}

The {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty images contain an International License Agreement for Non-Warranted Programs (ILAN) license which allows entitled {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty customers to use these same images under an International Program License Agreement (IPLA) term.

For customers that still wish to apply a {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty license to their docker image can do so following [these instructions](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade).

{{site.data.keyword.appserver_short}} licenses are based on the number of Processor Value Units \(PVUs\) that you need. PVU is a unit of measurement for the licensing of {{site.data.keyword.IBM_notm}} Middleware software. The number of PVUs indicates the number of processors \(cores\) that are available to the software.
{:shortdesc}

Every container size in {{site.data.keyword.cloud_notm}} requires a specific number of PVU entitlements that must be available in the {{site.data.keyword.appserver_short}} license. Therefore, you must plan your `ibm/liberty` containers prior to purchasing the license.

To purchase a {{site.data.keyword.appserver_short}} License, contact [{{site.data.keyword.IBM_notm}} Service](https://www.ibm.com/us-en/marketplace/java-ee-runtime/purchase){: external}. If you already have a license for {{site.data.keyword.appserver_short}} v8.5 or newer, then you can use any unused PVUs from your existing entitlement for the deployment of your container.

If you find that you require more PVUs after you purchased the license, you can increase the amount by contacting [{{site.data.keyword.IBM_notm}} Service](https://www.ibm.com/us-en/marketplace/java-ee-runtime/purchase){: external}.

