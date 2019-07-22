---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-24"

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

# Iniciación a la imagen `ibm/liberty`
{: #ibmliberty}

Las imágenes de IBM® WebSphere® Application Server Liberty \(`ibm/liberty`\) se suministran para {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

Puede acceder a las imágenes que suministra {{site.data.keyword.IBM_notm}} desde la línea de mandatos; consulte las [imágenes públicas de IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Cómo funciona
{: #ibmliberty_how_it_works}

Puede utilizar las imágenes `ibm/liberty` como padre para crear su propia imagen y desplegar sus apps WAR, EAR u OSGi basadas en Java en un contenedor de IBM WebSphere Application Server Liberty.
{:shortdesc}

## Qué incluye
{: #ibmliberty_whats_included}

Cada imagen de Liberty proporciona los siguientes paquetes de software.
{:shortdesc}

- IBM WebSphere Application Server for Developers Liberty
- IBM Java Runtime Environment 8.0

Las características de Liberty específicas que se instalan en la imagen dependen de la etiqueta que seleccione. La siguiente tabla muestra las características que se incluyen en cada una de las imágenes `ibm/liberty`. Para obtener más información sobre cada característica, consulte [Visión general de características de Liberty en IBM Knowledge Center ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSAW57_liberty/com.ibm.websphere.wlp.nd.multiplatform.doc/ae/rwlp_feat.html).

|Etiqueta|Descripción|
|---|-----------|
|Todas las imágenes `ibm/liberty`|Todas las imágenes `ibm/liberty` incluyen las siguientes características. <ul><li>`appSecurity-2.0`</li><li>`collectiveMember-1.0`</li><li>`localConnector-1.0`</li><li>`IdapRegistry-3.0`</li><li>`monitor-1.0`</li><li>`requestTiming-1.0`</li><li>`restConnector-1.0`</li><li>`sessionDatabase-1.0`</li><li>`ssl-1.0`</li><li>`webCache-1.0`</li></ul>|
|`ibm/liberty:latest`|Esta imagen apunta a la imagen `ibm/liberty:javaee8`.|
|`ibm/liberty:microProfile1`|Esta imagen contiene las características que proporcionan las prestaciones especificadas por [MicroProfile 1.x ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://microprofile.io/).|
|`ibm/liberty:microProfile2`|Esta imagen contiene las características que proporcionan las prestaciones especificadas por [MicroProfile 2.x ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://microprofile.io/).|
|`ibm/liberty:springBoot1`|Esta imagen contiene las características que proporcionan las prestaciones especificadas por [Spring Boot 1.x ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://spring.io/projects/spring-boot).|
|`ibm/liberty:springBoot2`|Esta imagen contiene las características que proporcionan las prestaciones especificadas por [Spring Boot 2.x ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://spring.io/projects/spring-boot).|
|`ibm/liberty:webProfile7`|Esta imagen incluye todas las características necesarias para la conformidad con Java EE 7 Web Profile.|
|`ibm/liberty:webProfile8`|Esta imagen incluye todas las características necesarias para la conformidad con Java EE 8 Web Profile.|
|`ibm/liberty:javaee7`|Esta imagen incluye todas las características de la imagen `ibm/liberty:webProfile7`, más las características que son necesarias para la conformidad con Java EE 7 Full Platform.|
|`ibm/liberty:javaee8`|Esta imagen incluye todas las características de la imagen `ibm/liberty:webProfile8`, más las características que son necesarias para la conformidad con Java EE 8 Full Platform.|
{: caption="Tabla 1. Características que se incluyen en cada una de las imágenes <code>ibm/liberty</code>" caption-side="top"}

## Restricciones de uso
{: #ibmliberty_usage}

La siguiente tabla muestra las restricciones de uso que se aplican al uso gratuito de la imagen `ibm/liberty` en {{site.data.keyword.cloud_notm}}.
{:shortdesc}

El precio de la imagen `ibm/liberty` es independiente del precio de los contenedores que utiliza en {{site.data.keyword.cloud_notm}}.
{:tip}

|Entorno|Restricciones para el uso gratuito|
|-----------|-----------------------|
|Desarrollo|Uso gratuito **ilimitado** de la imagen `ibm/liberty`.|
|Producción|El uso gratuito de la imagen `ibm/liberty` está limitado a un **máximo de 2 GB de espacio del almacenamiento dinámico de Java** en todas las instancias de contenedor que ejecutan la imagen. Por ejemplo, puede tener 2 x 1 GB, o 4 x 512 MB instancias de liberty de almacenamiento dinámico de forma gratuita.|
{: caption="Tabla 2. Precios" caption-side="top"}

Para supervisar el uso del almacenamiento dinámico de Java de las instancias de contenedor, consulte [Supervisión del uso del espacio de almacenamiento dinámico de Java de un contenedor con la CLI](#ibmliberty_monitor_heap).

Revise los términos de uso para las imágenes certificadas de IBM en la sección Licencia de la [imagen websphere-liberty ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://hub.docker.com/_/websphere-liberty/) en Docker Hub.

## Iniciación
{: #ibmliberty_get_started}

Utilice una de las imágenes gratuitas `ibm/liberty` del catálogo de {{site.data.keyword.cloud_notm}} o seleccione su propia imagen con licencia de producción para crear un contenedor único o un grupo de contenedores.
{:shortdesc}

Antes de empezar, revise las [restricciones de uso](#ibmliberty_usage) para las imágenes de `ibm/liberty`.
{: important}

1. En el catálogo, seleccione **Contenedores** > **Registro de contenedores de IBM Cloud** > **Repositorios públicos de IBM** en el panel lateral. Busque la imagen `ibm/liberty` como base a partir de la cual crear su contenedor. Si ha creado su propia imagen con licencia de producción y la ha desplegado en {{site.data.keyword.cloud_notm}}, seleccione esta imagen en el catálogo. Se abre la página de creación de contenedor.
2. En la lista **TAG/VERSION**, seleccione la versión de la imagen `ibm/liberty` que desee utilizar.
3. Para obtener más información sobre la creación de contenedores a partir de imágenes, la configuración de clústeres y el despliegue de apps en clústeres, utilice los enlaces siguientes:

    - [Creación de contenedores a partir de imágenes](/docs/containers?topic=containers-images#images)
    - [Iniciación al servicio IBM Cloud Kubernetes](/docs/containers?topic=containers-getting-started#getting-started)
    - [Despliegue de apps en clústeres](/docs/containers?topic=containers-app#app)

    La imagen `ibm/liberty` requiere que el puerto 9080 sea público. Cuando crea un contenedor desde el Panel de control de {{site.data.keyword.cloud_notm}}, de forma predeterminada el puerto se añade en el campo **Puerto público**. Si crea un contenedor desde la CLI, haga público el puerto ejecutando el mandato
`kubectl run` con la opción `--port=9080`.
    {:tip}

## Supervisión del uso del espacio de almacenamiento dinámico de Java de un contenedor con la CLI
{: #ibmliberty_monitor_heap}

Después de crear un contenedor a partir de la imagen `ibm/liberty`, puede ver métricas sobre un determinado pod y sus contenedores y revisar el uso del almacenamiento dinámico de Java. El espacio del almacenamiento dinámico de Java es la memoria que está disponible para la aplicación Java durante el tiempo de ejecución.
{:shortdesc}

1. Obtenga el nombre del pod cuyas métricas desea ver.
  
   ```
   kubectl get pods
   ```
   {: pre}

2. Consulte las métricas en un pod determinado y sus contenedores.

   ```
   kubectl top pod POD_NAME --containers
   ```
   {: pre}

3. Para revisar el uso del almacenamiento dinámico de Java, debe acceder a la estadística de memoria **RSS**; consulte
[Obtención de un shell de un contenedor en ejecución ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/). El uso del almacenamiento dinámico de Java se visualiza en kilobytes (KB). Si el uso del almacenamiento dinámico está por debajo de 2097152 KB (2 GB) en todas las instancias, no tiene que adquirir una licencia de WebSphere Application Server.

4. Ajuste el máximo uso del almacenamiento dinámico para la instancia de WebSphere Application Server. Para obtener más información, consulte
[Establecer argumentos JVM genéricos en el perfil de WebSphere Application Server V8.5 ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://www-01.ibm.com/support/docview.wss?uid=swg21596474).

## Obtención de una licencia de WebSphere Application Server
{: #ibm/liberty_license}

Las licencias de WebSphere Application Server se basan en el número de value unit de procesador \(PVU\) que es necesario. PVU es una unidad de medida para la licencia del software de middleware de IBM. El número de PVU indica el número de procesadores \(núcleos\) que están disponibles para el software.
{:shortdesc}

Cada tamaño de contenedor en {{site.data.keyword.cloud_notm}} requiere un número específico de autorización por value unit de procesador (PVU) que deben estar disponibles en la licencia de WebSphere Application Server. Por lo tanto, debe planificar los contenedores de `ibm/liberty` antes de adquirir la licencia.

Para adquirir una licencia de WebSphere Application Server, póngase en contacto con el [servicio de IBM ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.ibm.com/us-en/marketplace/java-ee-runtime/purchase). Si ya tiene una licencia para WebSphere
Application Server v8.5 o posterior, puede utilizar todos los PVU no utilizados desde su titularidad existente para el despliegue del contenedor.

Si encuentra que requiere más PVU después de adquirir la licencia, puede aumentar la cantidad contactando con el [servicio de IBM ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.ibm.com/us-en/marketplace/java-ee-runtime/purchase).

## Creación de una imagen `ibm/liberty` con licencia para producción para utilizarla con {{site.data.keyword.containershort_notm}}
{: #ibmliberty_prod_image}

Utilice la licencia de WebSphere Application Server para crear una imagen `ibm/liberty` con licencia de producción que puede utilizar con {{site.data.keyword.containershort_notm}}. Elija entre una de las siguientes tareas.
{:shortdesc}

- [Actualice la imagen desde Docker Hub a una imagen de producción ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://github.com/WASdev/ci.docker/tree/master/ga/production-upgrade).
- [Cree su propia imagen con licencia de producción ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://github.com/WASdev/ci.docker/tree/master/ga).

Después de crear una imagen con licencia de producción, [envíe por push la imagen a su registro privado](/docs/services/Registry?topic=registry-getting-started#getting-started) para utilizarlo con {{site.data.keyword.containershort_notm}}.

## Creación de una imagen a partir de las imágenes proporcionadas
{: #ibmliberty_creating_image}

Puede utilizar una de las imágenes `ibm/liberty` como padre para crear una imagen hijo que incluya su propio código de app. Personalice el Dockerfile de ejemplo y cree la imagen en su sistema. A continuación, puede añadir la imagen al registro de imágenes privado de la organización y crear contenedores con esta.
{:shortdesc}

Antes de empezar, tenga en cuenta los siguientes pasos.

- Compile el código de app en un archivo WAR, EAR u OSGi.
- Copie el archivo en el directorio en el que desea compilar la imagen.

Para crear una imagen con el código de la app a partir de la imagen `ibm/liberty`, efectúe los pasos siguientes:

1. Con un editor de texto, cree un archivo denominado `Dockerfile` y copie en él la siguiente información.

   ```
   FROM icr.io/ibm/liberty:<tag>
    COPY <app_name>.<file_extension> /config/dropins/
   ```
   {: pre}

    El directorio `/config` es un atajo para `/opt/ibm/wlp/usr/servers/defaultServer`.
    {: note}

2. Sustituya `<tag>` por la versión de la imagen `ibm/liberty` que incluye las características que la app requiere.

3. Sustituya `<app_name>` por el nombre de archivo de la app.

4. Sustituya `<file_extension>` por `.war`, `.ear` o bien `.eba`.

5. Añada cualquier otra dependencia de la app al Dockerfile.

6. Compile y envíe por push la imagen al registro de imágenes privado. Para obtener más información, consulte [Iniciación a {{site.data.keyword.registrylong_notm}}](/docs/services/Registry?topic=registry-getting-started#getting-started).

Todas las imágenes `ibm/liberty` están configuradas para grabar los archivos de registro de Liberty en el directorio `/logs` dentro del contenedor. Todos los demás archivos grabados por el servidor Liberty se crean en el directorio `/opt/ibm/wlp/output/defaultServer`. Puede acceder a estos archivos utilizando el atajo `/output`.
{:tip}

## Referencia del Dockerfile de `ibm/liberty`
{: #ibmliberty_reference_dockerfile}

Se pueden encontrar los archivos de Docker de la imagen `ibm/liberty` en el
[repositorio de GitHub WASdev/ci.docker
![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://github.com/WASdev/ci.docker/tree/master/ga) como referencia.
{:shortdesc}
