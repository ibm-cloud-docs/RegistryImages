---

copyright:
  years: 2017, 2021
lastupdated: "2021-06-22"

keywords: Registry, IBM Liberty, ibmliberty, container image, IBM WebSphere Application Server Liberty, Open Liberty, liberty, public images,

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

You can use an `ibm/liberty` image as a parent to create your own image and deploy your own Java application (Jakarta EE, Eclipse MicroProfile, or Spring Boot) in a Liberty container.
{:shortdesc}

You can access the images that are provided by {{site.data.keyword.IBM}} by using the command line, see [{{site.data.keyword.IBM_notm}} public images](/docs/Registry?topic=Registry-public_images#public_images).
{: tip}

## How it works
{: #ibmliberty_how_it_works}

You can build a new application image by having a `Dockerfile` that is similar to the following code:

   ```
   FROM icr.io/ibm/liberty:<tag>

   # Add the application server configuration
   COPY --chown=1001:0  server.xml /config/

   # For Open Liberty only
   #RUN features.sh

   # Add the application
   COPY --chown=1001:0  Sample1.war /config/dropins/

   RUN configure.sh
   ```
   {: codeblock}

Add the server configuration, then, if you are using the `kernel-slim` tag from Open Liberty, install the required features by calling the `features.sh` built-in script. You can then add your application and prime the shared class cache to improve the startup performance by calling the `configure.sh` built-in script. If you are using the `kernel` tag from {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty, the `configure.sh` script installs the required features, equivalent to Open Liberty's `feature.sh` script.

For more information about building applications images by using the [tags](#ibmliberty_tags) as the `FROM` value in your `Dockerfile`, see [Open Liberty](https://github.com/OpenLiberty/ci.docker#building-an-application-image){: external} and [WebSphere Liberty](https://github.com/WASdev/ci.docker#building-an-application-image){: external}.

## Tags
{: #ibmliberty_tags}

The following table shows the features that are included in each of the `ibm/liberty` image tags. Each of these tags is supported for the `amd64`, `s390x`, and `ppc64le` architectures by a manifest list, which means that if you use the tags as is, you get the architecture that matches the requesting computer.

|Tag|Description|
|---|-----------|
|`icr.io/ibm/liberty:21.0.0.6-wl-kernel` or `icr.io/ibm/liberty:21.0.0.6-wl-kernel-java8-ibmjava` | These tags contain {{site.data.keyword.redhat_full}} Universal Base Image 8, {{site.data.keyword.IBM_notm}} Java Runtime Environment 8.0, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 21.0.0.6 with the `kernel` feature enabled. To add features, you must either call Liberty's `installUtility` or the container's `configure.sh`. For more information, see [Building an application image](https://github.com/WASdev/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.6-wl-full` or `icr.io/ibm/liberty:21.0.0.6-wl-full-java8-ibmjava` | These tags contain {{site.data.keyword.redhat_notm}} Universal Base Image 8, {{site.data.keyword.IBM_notm}} Java Runtime Environment 8.0, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 21.0.0.6 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.6-wl-kernel-java8-ibmjava-ubuntu`| This tag contains the Ubuntu 18.04 image, {{site.data.keyword.IBM_notm}} Java Runtime Environment 8.0, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 21.0.0.6 with the `kernel` feature enabled. To add features, you must either call Liberty's `installUtility` or the container's `configure.sh`. For more information, see [Building an application image](https://github.com/WASdev/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.6-wl-full-java8-ibmjava-ubuntu` | This tag contains the Ubuntu 18.04 image, {{site.data.keyword.IBM_notm}} Java Runtime Environment 8.0, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 21.0.0.6 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.6-wl-kernel-java11-openj9` | This tag contains {{site.data.keyword.redhat_notm}} Universal Base Image 8, AdoptOpenJDK 11 with OpenJ9, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 21.0.0.6 with the `kernel` feature enabled. To add features, you must either call Liberty's `installUtility` or the container's `configure.sh`. For more information, see [Building an application image](https://github.com/WASdev/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.6-wl-full-java11-openj9` | This tag contains {{site.data.keyword.redhat_notm}} Universal Base Image 8, AdoptOpenJDK 11 with OpenJ9, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 21.0.0.6 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.6-wl-kernel-java11-openj9-ubuntu`| This tag contains the Ubuntu 18.04 image, AdoptOpenJDK 11 with OpenJ9, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 21.0.0.6 with the `kernel` feature enabled. To add features, you must either call Liberty's `installUtility` or the container's `configure.sh`. For more information, see [Building an application image](https://github.com/WASdev/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.6-wl-full-java11-openj9-ubuntu` | This tag contains the Ubuntu 18.04 image, AdoptOpenJDK 11 with OpenJ9, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 21.0.0.6 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.6-ol-kernel-slim` or `icr.io/ibm/liberty:21.0.0.6-ol-kernel-slim-java8-openj9` | These tags contain {{site.data.keyword.redhat_notm}} Universal Base Image 8, AdoptOpenJDK 8 with OpenJ9, and Open Liberty 21.0.0.6 with the `kernel` feature enabled. To add features, you must either call Open Liberty's `featureUtility` or the container's `features.sh`. For more information, see [Building an application image](https://github.com/OpenLiberty/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.6-ol-full` or `icr.io/ibm/liberty:21.0.0.6-ol-full-java8-openj9` | These tags contain {{site.data.keyword.redhat_notm}} Universal Base Image 8, AdoptOpenJDK 8 with OpenJ9, and Open Liberty 21.0.0.6 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.6-ol-kernel-slim-java8-openj9-ubuntu` | This tag contains the Ubuntu 18.04 image, AdoptOpenJDK 8 with OpenJ9, and Open Liberty 21.0.0.6 with the `kernel` feature enabled. To add features, you must either call Open Liberty's `featureUtility` or the container's `features.sh`. For more information, see [Building an application image](https://github.com/OpenLiberty/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.6-ol-full-java8-openj9-ubuntu` | This tag contains the Ubuntu 18.04 image, AdoptOpenJDK 8 with OpenJ9, and Open Liberty 21.0.0.6 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.6-ol-kernel-slim-java11-openj9` | This tag contains {{site.data.keyword.redhat_notm}} Universal Base Image 8, AdoptOpenJDK 11 with OpenJ9, and Open Liberty 21.0.0.6 with the `kernel` feature enabled. To add features, you must either call Open Liberty's `featureUtility` or the container's `features.sh`. For more information, see [Building an application image](https://github.com/OpenLiberty/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.6-ol-full-java11-openj9` | This tag contains {{site.data.keyword.redhat_notm}} Universal Base Image 8, AdoptOpenJDK 11 with OpenJ9, and Open Liberty 21.0.0.6 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.6-ol-kernel-slim-java11-openj9-ubuntu` | This tag contains the Ubuntu 18.04 image, AdoptOpenJDK 11 with OpenJ9, and Open Liberty 21.0.0.6 with the `kernel` feature enabled. To add features, you must either call Open Liberty's `featureUtility` or the container's `features.sh`. For more information, see [Building an application image](https://github.com/OpenLiberty/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.6-ol-full-java11-openj9-ubuntu` | This tag contains the Ubuntu 18.04 image, AdoptOpenJDK 11 with OpenJ9, and Open Liberty 21.0.0.6 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.6-ol-kernel-slim-java15-openj9` | This tag contains {{site.data.keyword.redhat_notm}} Universal Base Image 8, AdoptOpenJDK 15 with OpenJ9, and Open Liberty 21.0.0.6 with the `kernel` feature enabled. To add features, you must either call Open Liberty's `featureUtility` or the container's `features.sh`. For more information, see [Building an application image](https://github.com/OpenLiberty/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.6-ol-full-java15-openj9` | This tag contains {{site.data.keyword.redhat_notm}} Universal Base Image 8, AdoptOpenJDK 15 with OpenJ9, and Open Liberty 21.0.0.6 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.3-wl-kernel` or `icr.io/ibm/liberty:21.0.0.3-wl-kernel-java8-ibmjava` | These tags contain {{site.data.keyword.redhat_full}} Universal Base Image 8, {{site.data.keyword.IBM_notm}} Java Runtime Environment 8.0, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 21.0.0.3 with the `kernel` feature enabled. To add features, you must either call Liberty's `installUtility` or the container's `configure.sh`. For more information, see [Building an application image](https://github.com/WASdev/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.3-wl-full` or `icr.io/ibm/liberty:21.0.0.3-wl-full-java8-ibmjava` | These tags contain {{site.data.keyword.redhat_notm}} Universal Base Image 8, {{site.data.keyword.IBM_notm}} Java Runtime Environment 8.0, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 21.0.0.3 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.3-wl-kernel-java8-ibmjava-ubuntu`| This tag contains the Ubuntu 18.04 image, {{site.data.keyword.IBM_notm}} Java Runtime Environment 8.0, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 21.0.0.3 with the `kernel` feature enabled. To add features, you must either call Liberty's `installUtility` or the container's `configure.sh`. For more information, see [Building an application image](https://github.com/WASdev/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.3-wl-full-java8-ibmjava-ubuntu` | This tag contains the Ubuntu 18.04 image, {{site.data.keyword.IBM_notm}} Java Runtime Environment 8.0, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 21.0.0.3 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.3-wl-kernel-java11-openj9` | This tag contains {{site.data.keyword.redhat_notm}} Universal Base Image 8, AdoptOpenJDK 11 with OpenJ9, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 21.0.0.3 with the `kernel` feature enabled. To add features, you must either call Liberty's `installUtility` or the container's `configure.sh`. For more information, see [Building an application image](https://github.com/WASdev/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.3-wl-full-java11-openj9` | This tag contains {{site.data.keyword.redhat_notm}} Universal Base Image 8, AdoptOpenJDK 11 with OpenJ9, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 21.0.0.3 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.3-wl-kernel-java11-openj9-ubuntu`| This tag contains the Ubuntu 18.04 image, AdoptOpenJDK 11 with OpenJ9, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 21.0.0.3 with the `kernel` feature enabled. To add features, you must either call Liberty's `installUtility` or the container's `configure.sh`. For more information, see [Building an application image](https://github.com/WASdev/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.3-wl-full-java11-openj9-ubuntu` | This tag contains the Ubuntu 18.04 image, AdoptOpenJDK 11 with OpenJ9, and {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty 21.0.0.3 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.3-ol-kernel-slim` or `icr.io/ibm/liberty:21.0.0.3-ol-kernel-slim-java8-openj9` | These tags contain {{site.data.keyword.redhat_notm}} Universal Base Image 8, AdoptOpenJDK 8 with OpenJ9, and Open Liberty 21.0.0.3 with the `kernel` feature enabled. To add features, you must either call Open Liberty's `featureUtility` or the container's `features.sh`. For more information, see [Building an application image](https://github.com/OpenLiberty/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.3-ol-full` or `icr.io/ibm/liberty:21.0.0.3-ol-full-java8-openj9` | These tags contain {{site.data.keyword.redhat_notm}} Universal Base Image 8, AdoptOpenJDK 8 with OpenJ9, and Open Liberty 21.0.0.3 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.3-ol-kernel-slim-java8-openj9-ubuntu` | This tag contains the Ubuntu 18.04 image, AdoptOpenJDK 8 with OpenJ9, and Open Liberty 21.0.0.3 with the `kernel` feature enabled. To add features, you must either call Open Liberty's `featureUtility` or the container's `features.sh`. For more information, see [Building an application image](https://github.com/OpenLiberty/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.3-ol-full-java8-openj9-ubuntu` | This tag contains the Ubuntu 18.04 image, AdoptOpenJDK 8 with OpenJ9, and Open Liberty 21.0.0.3 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.3-ol-kernel-slim-java11-openj9` | This tag contains {{site.data.keyword.redhat_notm}} Universal Base Image 8, AdoptOpenJDK 11 with OpenJ9, and Open Liberty 21.0.0.3 with the `kernel` feature enabled. To add features, you must either call Open Liberty's `featureUtility` or the container's `features.sh`. For more information, see [Building an application image](https://github.com/OpenLiberty/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.3-ol-full-java11-openj9` | This tag contains {{site.data.keyword.redhat_notm}} Universal Base Image 8, AdoptOpenJDK 11 with OpenJ9, and Open Liberty 21.0.0.3 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.3-ol-kernel-slim-java11-openj9-ubuntu` | This tag contains the Ubuntu 18.04 image, AdoptOpenJDK 11 with OpenJ9, and Open Liberty 21.0.0.3 with the `kernel` feature enabled. To add features, you must either call Open Liberty's `featureUtility` or the container's `features.sh`. For more information, see [Building an application image](https://github.com/OpenLiberty/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.3-ol-full-java11-openj9-ubuntu` | This tag contains the Ubuntu 18.04 image, AdoptOpenJDK 11 with OpenJ9, and Open Liberty 21.0.0.3 with all of its features enabled. |
|`icr.io/ibm/liberty:21.0.0.3-ol-kernel-slim-java15-openj9` | This tag contains {{site.data.keyword.redhat_notm}} Universal Base Image 8, AdoptOpenJDK 15 with OpenJ9, and Open Liberty 21.0.0.3 with the `kernel` feature enabled. To add features, you must either call Open Liberty's `featureUtility` or the container's `features.sh`. For more information, see [Building an application image](https://github.com/OpenLiberty/ci.docker#building-an-application-image){: external}. |
|`icr.io/ibm/liberty:21.0.0.3-ol-full-java15-openj9` | This tag contains {{site.data.keyword.redhat_notm}} Universal Base Image 8, AdoptOpenJDK 15 with OpenJ9, and Open Liberty 21.0.0.3 with all of its features enabled. |
{: caption="Table 1. Each of the image tags for <code>ibm/liberty</code>" caption-side="top"}

## Getting started
{: #ibmliberty_get_started}

Use your application image, which is derived from the free `ibm/liberty` image tags, to create a single container or a container group.
{:shortdesc}

1. From the catalog, in the side pane, select **Containers** > **IBM Cloud Container Registry** > **IBM Public Repositories**. Search for your application image that you deployed to {{site.data.keyword.cloud_notm}}, select this image from the catalog. The container creation page opens.
2. From the **TAG/VERSION** list, select the version of the image that you want to use.
3. For more information about building containers from images, setting up clusters, and deploying apps in clusters, use the following links:

    - [Building containers from images](/docs/containers?topic=containers-images#images)
    - [Getting started with {{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-getting-started#getting-started)
    - [Deploying apps in clusters](/docs/containers?topic=containers-app#app)

    The `ibm/liberty` image requires that port 9080 is public. When you create a container from the {{site.data.keyword.cloud_notm}} Dashboard, the port is added in the **Public Port** field by default. If you create a container from the CLI, make the port public by running the `kubectl run` command with the `--port=9080` option.
    {:tip}

## Monitoring the Java heap space usage for a container with the CLI
{: #ibmliberty_monitor_heap}

After you create a container from the `ibm/liberty` image, you can view metrics about a particular pod and its containers and review the Java heap usage. The Java heap space is the memory that is available to the Java application at run time.
{:shortdesc}

1. Get the name of the pod for which you want to view metrics.

   ```
   kubectl get pods
   ```
   {: pre}

2. View metrics about a particular pod and its containers.

   ```
   kubectl top pod POD_NAME --containers
   ```
   {: pre}

3. To review the Java heap usage, you must access the **RSS** memory stat, see [Kubernetes: Get a shell to a running container](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/){: external}. The Java heap usage is displayed in KB. If your heap usage is under 2097152 KB (2 GB) across all instances, then you don't have to buy a {{site.data.keyword.appserver_short}} license.

4. Adjust the maximum heap usage for your {{site.data.keyword.appserver_short}} instance. For more information, see [Setting generic JVM arguments in the {{site.data.keyword.appserver_short}} 8.5 Liberty profile](https://www.ibm.com/support/pages/node/476495){: external}.

## License for production use
{: #ibm/liberty_license}

The {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty images contain an International License Agreement for Non-Warranted Programs (ILAN) license that allows entitled {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty customers to use these same images under an International Program License Agreement (IPLA) term.

If you want to apply an {{site.data.keyword.IBM_notm}} {{site.data.keyword.appserver_short}} Liberty license to your Docker image, see [Using WebSphere Liberty Docker in production](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade).

{{site.data.keyword.appserver_short}} licenses are based on the number of Processor Value Units (PVUs) that you require. PVU is a unit of measurement for the licensing of {{site.data.keyword.IBM_notm}} Middleware software. The number of PVUs indicates the number of processors (cores) that are available to the software.
{:shortdesc}

Every container size in {{site.data.keyword.cloud_notm}} requires a specific number of PVU entitlements that must be available in the {{site.data.keyword.appserver_short}} license. Therefore, you must plan your `ibm/liberty` containers before you buy the license.

To buy a {{site.data.keyword.appserver_short}} license, contact [{{site.data.keyword.IBM_notm}} Service](https://www.ibm.com/cloud/websphere-application-server/pricing){: external}. If you already have a license for {{site.data.keyword.appserver_short}} 8.5 or later, you can use any unused PVUs from your existing entitlement for the deployment of your container.

After you buy the license, if you require more PVUs, you can increase the amount by contacting [{{site.data.keyword.IBM_notm}} Service](https://www.ibm.com/cloud/websphere-application-server/pricing){: external}.
