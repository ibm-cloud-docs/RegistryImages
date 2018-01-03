---

copyright:
  years: 2016, 2017
lastupdated: "2017-11-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# Iniciación a la imagen *ibm-integration-bus*
{: #iib}

La imagen **ibm-integration-bus** se proporciona para {{site.data.keyword.containerlong}}. Incluye la edición IBM Integration Bus for Developers.
{:shortdesc}


## Modo de funcionamiento
{: #how_it_works}

IBM Integration Bus for Developers contiene todo lo que necesita para empezar a desarrollar sus propias soluciones de integración.
{:shortdesc}

Después de crear una solución de integración, puede utilizar la imagen **ibm-integration-bus** para suministrar un contenedor único en {{site.data.keyword.Bluemix_notm}}. A continuación, podrá desplegar la solución de integración en este contenedor utilizando la interfaz de usuario web o desde un terminal.

**Nota**: Puede utilizar la imagen **ibm-integration-bus** solamente para el desarrollo y la prueba de unidad. También puede utilizar la imagen para explorar el producto, aprender de guías de aprendizaje y evaluar la contribución que IBM Integration Bus puede realizar en la organización.


## Qué incluye
{: #whats_included}

Esta imagen de IBM Integration Bus contiene el paquete de software para IBM Integration Bus for Developers Versión 10 \(también conocido como Developer Edition\), que es una versión con todas las funciones del producto que puede utilizar para el desarrollo y la prueba de unidad. Puede descargar esta versión de forma gratuita y puede utilizarla dentro de los términos de la licencia.
{:shortdesc}

IBM Integration Bus for Developers Versión 10.0 está disponible en sistemas operativos Windows™ de 64 bits y sistemas operativos Linux™ en x86-64. Todos los requisitos previos del producto se incluyen en el paquete de descarga.

Para obtener información sobre las características que se incluyen en IBM Integration Bus for Developers, consulte [IBM Integration Bus en IBM Knowledge Center ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.msgbroker.helphome.doc/help_home_msgbroker.htm){: new_window}.

Puede acceder a Developer Edition desde la [página de descarga de IBM Integration Bus for Developers ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.ibm.com/marketing/iwm/iwm/web/pick.do?source=swg-wmbfd){: new_window}.


## Restricciones de uso
{: #usage}

La siguiente tabla muestra las restricciones de uso que se aplican al uso gratuito de la imagen **ibm-integration-bus** en {{site.data.keyword.Bluemix_notm}}.
{:shortdesc}

**Nota**: El precio de la imagen **ibm-integration-bus** es independiente del precio de los contenedores que utiliza en {{site.data.keyword.Bluemix_notm}}.

|Entorno|Restricciones para el uso gratuito|
|-----------|-----------------------|
|Desarrollo|Uso gratuito **ilimitado** de la imagen **ibm-integration-bus** para crear contenedores únicos para el desarrollo y prueba de unidad.|
|Producción|Developer Edition está limitada al proceso de una transacción por segundo y no está soportada para su uso en un entorno de producción.|
{: caption="Tabla 1. Restricciones para el uso gratuito de la imagen ibm-integration-bus" caption-side="top"}


## Licencia
{: #license}

Información sobre licencias.
{:shortdesc}

*   Los Dockerfiles y scripts asociados están bajo licencia [Apache License 2.0 ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](http://www.apache.org/licenses/LICENSE-2.0.html){: new_window}.
*   [Información de la licencia para IBM Integration Bus for Developers Versión 10.0 ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](http://www.ibm.com/software/sla/sladb.nsf/displaylis/1BD32B141C9FDAE38525802C0055A956?OpenDocument){: new_window}.

    **Nota**: La licencia no permite más distribución. Los términos para IBM Integration Bus en la imagen restringen el uso para el desarrollo y las pruebas de unidad.


## Iniciación
{: #get_started}

Utilice la imagen **ibm-integration-bus** del catálogo de {{site.data.keyword.Bluemix_notm}} o seleccione su propia imagen con licencia de producción para crear un contenedor único.
{:shortdesc}

**Importante**: Antes de empezar, revise las [restricciones de uso](#usage) para la imagen **ibm-integration-bus**.

**Nota**: Puede utilizar mandatos Docker nativos en todos los pasos marcados con un asterisco (\*).

   Realice los siguientes pasos para ejecutar IBM Integration Bus en un contenedor:

1.  [Suministrar un contenedor basado en la imagen ibm-integration-bus desde la IU de {{site.data.keyword.Bluemix_notm}}](#provision)
2.  [Validar el contenedor y la instalación de IBM Integration Bus](#validate)
3.  [Crear y desplegar un flujo](#createdeployflow)
4.  [Supervisar registros de IBM Integration Bus dentro del contenedor](#monitor)
5.  [Gestionar recursos de IBM Integration Bus que están ejecutándose en el contenedor](#manage)


## Suministrar un contenedor basado en la imagen **ibm-integration-bus**
{: #provision}

Suministrar un contenedor Docker en {{site.data.keyword.Bluemix_notm}}, basándose en la imagen **ibm-integration-bus** proporcionada por {{site.data.keyword.IBM_notm}}.
{:shortdesc}

  Siga estos pasos:

1.  Inicie una sesión en {{site.data.keyword.Bluemix_notm}}.
2.  Desde el catálogo, seleccione **Contenedores** y elija la imagen **ibm-integration-bus** a partir de la que se va a crear el contenedor.
3.  Seleccione **Único** para crear un contenedor de instancia única, que puede utilizarse para fines de desarrollo y pruebas.
4.  Especifique el nombre del contenedor; por ejemplo, `iib`.
5.  Seleccione el tamaño del contenedor.
6.  En el campo **Dirección IP pública**, seleccione *Enlazar IP pública*.
7.  En el campo **Puertos públicos**, especifique `4414, 7800`.
8.  Expanda las opciones **Avanzadas** y pulse **Añadir nuevas variables de entorno**.
9.  Añada las siguientes variables de entorno:

    <ul>
      <li>LICENSE con un valor <i>accept</i>
      <li>NODENAME con un valor &lt;MYNODE&gt; &lpar;donde &lt;MYNODE&gt; es el nombre de nodo elegido&rpar;</li>
      <li>LOG_LOCATIONS con un valor <i>/var/log/syslog</i>
    </ul>

    **Nota**: Si no especifica valores para propiedades LICENSE y NODENAME, el contenedor se crea pero no se inicia.

    La propiedad LOG_LOCATIONS especifica los registros dentro del contenedor que desea exponer a través de la IU de {{site.data.keyword.Bluemix_notm}}.
10. Pulse **Crear** y espere a que el contenedor empiece a ejecutarse.


## Validar el contenedor y la instalación de IBM Integration Bus
{: #validate}

Después de desplegar el contenedor que ejecuta IBM Integration Bus en {{site.data.keyword.Bluemix_notm}}, pulse el estado del contenedor y valide la instalación de IBM Integration Bus.
{:shortdesc}

  Complete los siguientes pasos para verificar la instalación y configuración de IBM Integration Bus en el contenedor:
  
1.  Configure la CLI de {{site.data.keyword.containershort_notm}}, tal como se describe en [Configuración del plug-in de {{site.data.keyword.containershort_notm}} \(`bx ic`\) para utilizar la CLI de Docker nativa](/docs/containers/container_cli_cfic_install.html).

2.  En un terminal, inicie la sesión en {{site.data.keyword.Bluemix_notm}}. Ejecute los siguientes mandatos:

    <ol>
       <li>
          <pre class="codeblock">bx login</pre>
       </li>
       <li>
          <pre class="codeblock">bx ic init</pre>
       </li>
    </ol>

3.  Compruebe el estado del contenedor.\*

    ```
    bx ic ps
    ```
    {: pre}

    **Nota**: En este mandato, puede sustituir **bx ic** con **docker** al [iniciar sesión en {{site.data.keyword.containershort_notm}}](/docs/containers/container_cli_cfic_install.html#container_cli_login) y establecer las variables de entorno para utilizar mandatos Docker nativos. Puede utilizar mandatos Docker nativos en todos los pasos marcados con un asterisco (\*).

    ```
    $ bx ic ps
    CONTAINER ID        IMAGE                                                       COMMAND             CREATED             STATUS                 PORTS                                                        NAMES
    46d9ec1e-952        registry.ng.bluemix.net/ibm-integration-bus:latest   ""                  2 minutes ago       Running a minute ago   xxx.xxx.xxx.xxx:4414->4414/tcp, xxx.xxx.xxx.xxx:7800->7800/tcp   iib
    ```
    {: screen}

4.  Adjunte una sesión Bash al contenedor.\*

    ```
    bx ic exec -it <nombre_contenedor> /bin/bash
    ```
    {: pre}

    donde *nombre_contenedor* es el nombre del contenedor.

    Por ejemplo:

    ```
    $ bx ic exec -it iib /bin/bash
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

5.  Inicialice el entorno ejecutando el mandato **mqsiprofile**, tal como se describe en [Entorno de mandatos: sistemas Linux y UNIX™ ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](http://www.ibm.com/support/knowledgecenter/SSMKHH_9.0.0/com.ibm.etools.mft.doc/an26230_.htm){: new_window}.

    El mandato para inicializar el entorno está disponible en el directorio: `/opt/ibm/iib-10.0.0.6/server/bin/`

    Por ejemplo:

    ```
    (IIB_10:)iibuser@instance-0098858e:/$ . ./opt/ibm/iib-10.0.0.6/server/bin/mqsiprofile

    MQSI 10.0.0.6
    /opt/ibm/iib-10.0.0.6/server

    # Comprobar que la variable de entorno <MQSI_WORKPATH> se ha establecido
    (IIB_10:)iibuser@instance-0098858e:/$ echo $<MQSI_WORKPATH>
    /var/mqsi
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

6.  Ejecute el mandato **mqsilist** para mostrar el estado de un nodo, que debe aparecer como `running`. Por ejemplo:

    ```
    (IIB_10:)iibuser@instance-0098858e:/$ mqsilist
    BIP1325I: Nodo de integración 'MYNODE' con URI de administración 'http://instance-0098858e:4414' está en ejecución.
    BIP8071I: El mandato ha finalizado correctamente.
    (IIB_10:)iibuser@instance-0098858e:/$
    ```
    {: screen}

El contenedor se está ejecutando y puede desplegar soluciones de integración en el mismo utilizando cualquiera de los métodos soportados.


## Supervisar registros de IBM Integration Bus dentro del contenedor
{: #monitor}

Supervise los registros a través de la interfaz de usuario de {{site.data.keyword.Bluemix_notm}} o desde la línea de mandatos.
{:shortdesc}

Antes de empezar, configure la CLI de {{site.data.keyword.containershort_notm}} para que supervise los registros de la línea de mandatos. Para obtener más información, consulte [Configuración del plug-in de {{site.data.keyword.containershort_notm}} (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

La imagen **ibm-integration-bus** está configurada para imprimir los mensajes de IBM Integration Bus en `/var/log/syslog` dentro del contenedor.

Elija una de las siguientes formas de supervisar los registros de IBM Integration Bus:

*  Si ha configurado la variable de entorno LOG_LOCATIONS cuando ha suministrado el contenedor, puede supervisar los registros desde la IU de {{site.data.keyword.Bluemix_notm}}.

    Realice los siguientes pasos para acceder a los registros:

    <ol>
       <li>En el panel de control del contenedor, seleccione el contenedor que desea supervisar.</li>
       <li>Seleccione <b>Supervisión y registro</b>.</li>
       <li>Seleccione <b>Registro</b>. El panel de control lista las entradas del registro.</li>
       <li>Para personalizar la vista de registro, seleccione <b>Vista avanzada</b> en la página Registro para abrir Kibana. Para obtener más información, consulte <a href="https://console.bluemix.net/docs/containers/container_planning.html#container_planning_monlog" target="_blank">Supervisión y registro</a> para empezar.
       </li>
    </ol>

*  Desde un terminal, utilice la CLI de Docker o de {{site.data.keyword.containerlong_notm}} para supervisar los mensajes. Siga estos pasos:

    <ol>
       <li>
       Configure el terminal.

        <ul>
        <li>Para utilizar la CLI de {{site.data.keyword.containerlong_notm}} CLI para gestionar contenedores en {{site.data.keyword.Bluemix_notm}} mientras sigue utilizando la CLI de Docker directamente para gestionar el host de Docker local, realice los siguientes pasos para configurar el entorno:
           <ol>
             <li>
               <pre class="codeblock">bx login</pre>
             </li>
             <li>
               <pre class="codeblock">bx ic init</pre>
             </li>
           </ol>
        </li>
        <li>Para utilizar la CLI de Docker para gestionar contenedores en {{site.data.keyword.Bluemix_notm}}, siga estos pasos:
          <ol>
            <li>
              <pre class="codeblock">bx login</pre>
            </li>
            <li>
              <pre class="codeblock">bx ic init</pre>
               Copie los valores que se proporcionan para las siguientes variables de entorno: &lt;DOCKER_HOST&gt;, &lt;DOCKER_CERT_PATH&gt; y &lt;DOCKER_TLS_VERIFY&gt;.
            </li>
            <li>
            Altere temporalmente el entorno de Docker local estableciendo las siguientes variables para conectarse a {{site.data.keyword.containerlong_notm}}:
               <ul>
                 <li><pre class="codeblock">export DOCKER_HOST=&lt;valor_host&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;valor_via_acceso_cert&gt;</pre></li>
                 <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
               </ul>
                <b>Nota</b>: Esta opción solamente da soporte a algunos mandatos de Docker.
            </li>
          </ol>
        </li>
      </ul>
      </li>
      <li>
      Acceda al registro. Elija una de las siguientes opciones:

      <ul>
         <li>Si el terminal se ha configurado para ejecutar mandatos de CLI de {{site.data.keyword.containerlong_notm}}, ejecute el siguiente mandato:

            <pre class="codeblock">bx ic exec &lt;ID_contenedor&gt; tail -f /var/log/syslog</pre>

            donde *ID_contenedor* es el nombre del contenedor.

            Por ejemplo:

            <pre class="screen">bx ic exec iib tail -f /var/log/syslog</pre>

          </li>
          <li>
           Si el terminal se ha configurado para ejecutar mandatos de CLI de Docker, ejecute el siguiente mandato:

            <pre class="codeblock">docker exec &lt;ID_contenedor&gt; tail -f /var/log/syslog</pre>

            donde *ID_contenedor* es el nombre del contenedor.

            Por ejemplo:

            <pre class="screen">docker exec iib tail -f /var/log/syslog</pre>

          </li>
        </ul>
       </li>
    </ol>


## Gestionar recursos de IBM Integration Bus que están ejecutándose en el contenedor
{: #manage}

Inicie la interfaz de usuario web de IBM Integration Bus o ejecute mandatos desde un terminal para gestionar los recursos de IBM Integration Bus que están ejecutándose en un contenedor.
{:shortdesc}

  Elija una de las siguientes opciones para gestionar recursos de IBM Integration Bus en el contenedor:

*  [Iniciar la interfaz de usuario web de IBM Integration Bus](#launchwebUI) y gestionar IBM Integration Bus de forma gráfica.
*  Desde un terminal, utilizar la CLI de Docker o de {{site.data.keyword.containershort_notm}} para gestionar recursos de IBM Integration Bus:
    *   [Ejecutar mandatos de administración en el contenedor utilizando la CLI de {{site.data.keyword.containerlong_notm}}](#admin_commands_containers_cli)
    *   [Ejecutar mandatos de administración en el contenedor utilizando la CLI de Docker](#admin_commands_docker_cli)


## Iniciar la interfaz de usuario web de IBM Integration Bus
{: #launchwebUI}

Inicie la interfaz de usuario web para gestionar los recursos de IBM Integration Bus que se ejecutan en el contenedor desde un navegador.
{:shortdesc}

Siga estos pasos para conectar un navegador al host, que ha expuesto en [Iniciación a la imagen ibm-integration-bus para {{site.data.keyword.Bluemix_notm}}](#get_started). Se visualiza la interfaz de usuario de web de IBM Integration Bus.

1.  Seleccione el contenedor desde la interfaz de usuario de {{site.data.keyword.Bluemix_notm}} y compruebe si el estado del contenedor es `En ejecución`.
2.  Compruebe los detalles de contenedor para buscar la IP pública.
3.  Abra un navegador web con el siguiente URL:

    ```
    http://<IPPúblicaContenedorDocker>:4414
    ```
    {: pre}

    donde *IPPúblicaContenedorDocker* es la IP pública del contenedor que ha comprobado en el paso anterior.

4.  El navegador abre la interfaz de usuario de web y está listo para administrar IBM Integration Bus.


## Ejecutar mandatos de administración en el contenedor utilizando la CLI de {{site.data.keyword.containerlong_notm}}
{: #admin_commands_containers_cli}

Utilice la CLI de {{site.data.keyword.containershort_notm}} para ejecutar los mandatos de administración de IBM Integration Bus directamente en un contenedor.
{:shortdesc}

Antes de empezar, configure la CLI de {{site.data.keyword.containershort_notm}} para ejecutar mandatos de administración desde la línea de mandatos. Para obtener más información, consulte [Configuración del plug-in de {{site.data.keyword.containershort_notm}} (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

  Complete los siguientes pasos para configurar un terminal para ejecutar mandatos **bx ic** para gestionar IBM Integration Bus:

1.  En un terminal, inicie la sesión en {{site.data.keyword.Bluemix_notm}}. Ejecute los siguientes mandatos:

      <ol>
        <li>
           <pre class="codeblock">bx login</pre>
        </li>
        <li>
           <pre class="codeblock">bx ic init</pre>
        </li>
       </ol>
           
2.  Adjunte una sesión Bash al contenedor para configurar una sesión interactiva.\*

    ```
    bx ic exec -it <nombre_contenedor> /bin/bash
    ```
    {: pre}

    donde *nombre_contenedor* es el nombre del contenedor.

    Por ejemplo:

    ```
    bx ic exec -it iib /bin/bash
    ```
    {: screen}

    Puede suministrar mqsiprofile y ejecutar los mandatos a partir de este punto, en un shell dentro del contenedor.

3.  Como alternativa, utilice el mandato **bx ic exec** para ejecutar una sesión Bash no interactiva que pueda ejecutar cualquier mandato de IBM Integration Bus.\*

    Por ejemplo:

    ```
    bx ic exec iib <nombre_contenedor> /bin/bash -c mqsilist
    ```
    {: screen}


## Ejecutar mandatos de administración en el contenedor utilizando la CLI de Docker
{: #admin_commands_docker_cli}

Utilice la CLI de Docker para ejecutar los mandatos de administración de IBM Integration Bus directamente en un contenedor.
{:shortdesc}

Antes de empezar, configure la CLI de {{site.data.keyword.containershort_notm}} para ejecutar mandatos de administración desde la línea de mandatos. Para obtener más información, consulte [Configuración del plug-in de {{site.data.keyword.containershort_notm}} (`bx ic`)](/docs/containers/container_cli_cfic_install.html).

  Complete los siguientes pasos para configurar un terminal para ejecutar mandatos para gestionar IBM Integration Bus:

1.  En un terminal, inicie la sesión en {{site.data.keyword.Bluemix_notm}}. Ejecute los siguientes mandatos:

    <ol>
     <li>
       <pre class="codeblock">bx login</pre>
     </li>
     <li>
       <pre class="codeblock">bx ic init</pre>

      <p>Copie los valores que se proporcionan para las siguientes variables de entorno: &lt;DOCKER_HOST&gt;, &lt;DOCKER_CERT_PATH&gt; y &lt;DOCKER_TLS_VERIFY&gt;.</p>
      </li>
      <li>  Altere temporalmente el entorno de Docker local estableciendo las siguientes variables para conectarse a {{site.data.keyword.containershort_notm}}:
        <ul>
           <li><pre class="codeblock">export DOCKER_HOST=&lt;valor_host&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_CERT_PATH=&lt;valor_via_acceso_cert&gt;</pre></li>
           <li><pre class="codeblock">export DOCKER_TLS_VERIFY=1</pre></li>
        </ul>

          <p><b>Nota</b>: Esta opción solamente da soporte a algunos mandatos de Docker.</p>
     </li>
    </ol>

2.  Adjunte una sesión Bash al contenedor para configurar una sesión interactiva. Ejecute el mandato siguiente:

    ```
    docker exec -it <nombre_contenedor> /bin/bash
    ```
    {: pre}

    donde *nombre_contenedor* es el nombre del contenedor.

    Por ejemplo:

    ```
    docker exec -it iib /bin/bash
    ```
    {: screen}

    Puede suministrar mqsiprofile y ejecutar los mandatos a partir de este punto, en un shell dentro del contenedor.

3.  Puede suministrar mqsiprofile y ejecutar los mandatos a partir de este punto, en un shell dentro del contenedor.

    Como alternativa, utilice docker exec para ejecutar una sesión Bash no interactiva que puede ejecutar cualquiera de los mandatos de IBM Integration Bus. 
    
    Por ejemplo:

    ```
    docker exec iib <nombre_contenedor> /bin/bash -c mqsilist
    ```
    {: screen}


## Crear y desplegar un flujo
{: #createdeployflow}

Cree y despliegue un flujo de mensajes siguiendo las instrucciones en IBM Integration Bus Knowledge Center.
{:shortdesc}

Para obtener información sobre cómo crear un flujo de mensajes, consulte [¿Cómo se pueden crear y gestionar flujos de mensajes? ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40150_.htm){: new_window}.

Para obtener información sobre cómo desplegar un flujo de mensajes, consulte [¿Cómo se pueden desplegar y configurar flujos de mensajes? ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](http://www.ibm.com/support/knowledgecenter/SSMKHH_10.0.0/com.ibm.etools.mft.doc/aa40180_.htm){: new_window}.
