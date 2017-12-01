---

copyright:
  years: 2015, 2017
lastupdated: "2017-10-30"

  ---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}

# Iniciación a la imagen ibm-node-strong-pm
{: #getting_started_node_strong}

La imagen de {{site.data.keyword.IBM}} con el gestor de procesos de StrongLoop \(**ibm-node-strong-pm**\) se proporciona para {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

## Modo de funcionamiento 
{: #how_it_works}

Utilice la imagen **ibm-node-strong-pm** para crear un solo contenedor que despliegue una instancia en ejecución del [Gestor de procesos de StrongLoop](https://www.strongloop.com). El Gestor de procesos de StrongLoop le permite desplegar, gestionar y supervisar apps Node.js en {{site.data.keyword.IBM_notm}} Cloud que se han implementado en una máquina remota.
{:shortdesc}

La máquina remota debe tener instalada una instancia de StrongLoop Arc. StrongLoop Arc es una interfaz gráfica de usuario para la plataforma StrongLoop e incluye herramientas para crear, supervisar y supervisar apps Node.js. Después de crear una app con StrongLoop Arc, debe enviarla por push y desplegarla en el contenedor en {{site.data.keyword.Bluemix_notm}} que ejecuta el Gestor de procesos de StrongLoop. Para establecer una conexión entre la máquina remota y el contenedor, debe enlazar una dirección IP pública al contenedor y configurar esta dirección IP como el servidor del Gestor de procesos de StrongLoop en StrongLoop Arc. Cuando la app se despliega a través del Gestor de procesos de StrongLoop, puede supervisar métricas y el rendimiento de la app en el Gestor de procesos de StrongLoop. Para actualizar o escalar la app, debe cambiarla utilizando StrongLoop Arc y, a continuación, enviar por push los cambios en el contenedor.

**Nota:** **ibm-node-strong-pm** no está soportado para utilizarse con grupos de controladores en {{site.data.keyword.Bluemix_notm}}.

## Qué incluye
{: #whats_included}

-   Todos los paquetes de software incluidos en la imagen **ibmnode:latest**. Consulte [Qué incluye](/docs/services/RegistryImages/ibmnode/index.html#getting_started_node) en Iniciación a la imagen **ibmnode** para el tema {{site.data.keyword.Bluemix_notm}}.
-   Gestor de procesos de StrongLoop

## Iniciación
{: #how_to_get_started}

Puede utilizar la imagen **ibm-node-strong-pm** desde el Catálogo de {{site.data.keyword.Bluemix_notm}} para probar un contenedor único en {{site.data.keyword.containershort_notm}}. La imagen se inicia con una instancia en ejecución del gestor de procesos de StrongLoop, a la que puede acceder en cuanto el contenedor esté disponible. Puede utilizar una instancia de StrongLoop Arc conectada a dicho gestor de procesos para enviar por push una aplicación Node.js y acceder a ella.
{:shortdesc}

**Nota:** Antes de empezar, revise los términos de uso de las imágenes certificadas de {{site.data.keyword.IBM_notm}} en la sección Uso de la [biblioteca de Docker websphere-liberty](https://github.com/docker-library/docs/tree/master/websphere-liberty).

1.  Desde el catálogo, seleccione **Contenedores** y elija la imagen **ibm-node-strong-pm** a partir de la que se va a crear el contenedor.
2.  Cree un contenedor único.
    -   En el campo **Tamaño**, seleccione un tamaño de contenedor de como mínimo 1 GB de memoria \(**Pequeño**\).
    -   En el campo **Puertos públicos**, escriba los puertos 8701 y 3001. Estos puertos se exponen automáticamente cuando se despliega el contenedor en {{site.data.keyword.Bluemix_notm}} y un requisito previo para enlazar una dirección IP con el contenedor. El puerto 8701 se utiliza para acceder al Gestor de procesos de StrongLoop en el contenedor. El puerto 3001 es el puerto en el que la app escucha después de desplegarse en el Gestor de procesos de StrongLoop.

        **Nota:** Para desplegar apps adicionales en el Gestor de procesos de StrongLoop, exponga más puertos. Los puertos deben ser consecutivos, empezando por 3002. Por ejemplo, 3002, 3003, 3004, etc.

    -   Para obtener más información, consulte [Creación de un contenedor mediante el panel de control de {{site.data.keyword.Bluemix_notm}}](/docs/containers/container_single_ui.html#gui).
3.  Enlace una dirección IP pública con el contenedor. Para obtener más información, consulte [Solicitud y enlace de direcciones IP](/docs/containers/container_single_ui.html#container_cli_ips).

## Configuración de StrongLoop Arc en una máquina remota
{: #UsingSLA}

Después de crear un contenedor único a partir de la imagen **ibm-node-strong-pm**
en {{site.data.keyword.Bluemix_notm}}, configure StrongLoop Arc en la máquina remota.
{:shortdesc}

Antes de empezar, instale Node.js y el gestor de paquetes de nodos \(npm\) en la máquina remota.

1.  Descargue e instale StrongLoop especificando el siguiente mandato. StrongLoop incluye todos los paquetes necesarios para ejecutar Arc.

    ```
    npm install -g strongloop
 ```
    {: pre}

2.  Después de instalar StrongLoop, ejecute StrongLoop Arc.

    ```
    slc arc
 ```
    {: pre}

3.  Cuando se le solicite, regístrese o inicie sesión en StrongLoop.
4.  Añada el Gestor de procesos de StrongLoop que se ejecuta en el contenedor a la lista del gestor de procesos en StrongLoop Arc.
    1.  En el selector de módulos de Arc, vaya a **Gestor de procesos**.
    2.  Añada la dirección IP pública que enlaza al contenedor y el puerto público 8701 al Gestor de procesos de Arc.
    3.  Pulse **Activar**.

## Envío por push de una app Node.js al contenedor mediante StrongLoop Arc
{: #pushing_app_to_container}

Después de conectarse al Gestor de procesos de StrongLoop que se ejecuta en el contenedor con el cliente de StrongLoop Arc en la máquina remota, compile y envíe por push las apps de Node.js a {{site.data.keyword.Bluemix_notm}}.
{:shortdesc}

1.  En el selector de módulos de Arc, vaya a **Compilación y despliegue**.
2.  Compile la app siguiendo las instrucciones de la interfaz de usuario de Arc.

    **Importante:** Asegúrese de que la app de Node.js que ha creado con StrongLoop Arc está a la escucha en la variable de entorno PORT y el puerto que ha expuesto cuando ha creado el contenedor.

    Ejemplo

    ```
    app.listen(process.env.PORT || 3001);
 ```
    {: pre}

3.  Despliegue la app siguiendo las instrucciones de la interfaz de usuario de Arc. Seleccione el Gestor de procesos que ha configurado para desplegar la app en {{site.data.keyword.Bluemix_notm}}.
4.  Espere a que finalice el proceso de despliegue.
5.  Acceda a la app.
    1.  Abra un navegador web.
    2.  Escriba la dirección IP del contenedor y puerto en el siguiente formato
<var class="keyword varname">IP_ADDRESS:PORT</var>.

        Ejemplo

        ```
        192.0.2.26:3001
 ```
        {: pre}

    3.  Repita estos pasos para cada app que ha desplegado en {{site.data.keyword.Bluemix_notm}}.
6.  Utilice el rastreo, las métricas y el perfilador de Arc para obtener información del rendimiento sobre la app mientras se ejecuta en {{site.data.keyword.Bluemix_notm}}. Consulte [Operating Node applications](https://docs.strongloop.com/display/SLC/Operating+Node+applications) en la documentación de StrongLoop
para obtener más información.

## Referencia del Dockerfile de **ibm-node-strong-pm** 
{: #reference_dockerfile}

Este Dockerfile se ha utilizado para crear la imagen **ibm-node-strong-pm** en el catálogo de {{site.data.keyword.Bluemix_notm}}. Esta información sirve únicamente como referencia.
No se proporcionan los otros archivos incluidos en la versión de compilación de esta imagen.

```
FROM registry.ng.bluemix.net/ibmnode:latest
RUN useradd -ms /bin/bash strong-pm \
    && chown -R strong-pm:strong-pm /usr/local \
    && su strong-pm -c "npm install -g strong-pm && npm cache clear"

  # Configurar algo parecido a un entorno
WORKDIR /home/strong-pm
ENV HOME=/home/strong-pm PORT=3000

  # Ejecutar como usuario sin privilegios dentro de un contenedor
USER strong-pm

  # Exponer puertos de strong-pm y de aplicación
EXPOSE 8701
EXPOSE 3001-3010

  ENTRYPOINT ["/usr/local/bin/sl-pm", "--base", ".", "--listen", "8701"]
```
{: screen}


