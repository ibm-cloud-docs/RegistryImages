---

copyright:
  years: 2017
lastupdated: "2017-10-30"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# Iniciación a la imagen **ibm-websphere-extreme-scale** 
{: #ibm-websphere-extreme-scale}

Las imágenes **ibm-websphere-extreme-scale** se proporcionan para {{site.data.keyword.containerlong}}.

## Modo de funcionamiento 
{: #how_it_works}

La imagen de IBM WebSphere&reg; eXtreme Scale es una característica distribuida de memoria caché que se ejecuta en el entorno de tiempo de ejecución de Liberty. Proporciona servidores de memoria caché
de Liberty, herramientas de operación de memoria caché, una consola administrativa y servicios de administración REST preconfigurados basa dos en los últimos estándares y especificaciones de la industria. Esta imagen se basa en la tecnología IBM WebSphere eXtreme Scale principal. La tecnología de almacenamiento en memoria caché que se carga con esta imagen da soporte completo a la especificación JCache \(JSR107\) y a su conformidad.

Dispone de las siguientes características principales cuando utiliza la imagen `ibm-websphere-extreme-scale`:

-   Una solución de almacenamiento en memoria caché fácil de desplegar, fácil de configurar y distribuida basada en la tecnología eXtreme Scale
-   Consola administrativa sencilla y fácil de utilizar y API REST
-   Completa personalización para dar soporte a todos los casos de uso de almacenamiento en memoria caché de eXtreme Scale
-   Imagen fácil de configurar que se integra en el entorno de almacenamiento en memoria caché en cuestión de minutos
-   Conformidad con JCache para dar soporte a la especificación de almacenamiento en memoria caché JSR107


## Qué incluye 
{: #whats_included}

Cada imagen
**ibm-websphere-extreme-scale** ofrece los siguientes paquetes de software.

-   Imagen base Ubuntu 16.04 de 64 bits
-   IBM WebSphere eXtreme Scale
-   IBM Java Runtime Environment 8.0

Las características específicas que se instalan en la imagen dependen de la etiqueta que seleccione. En la siguiente tabla se el nivel de código que se incluye en cada una de las imágenes **ibm-websphere-extreme-scale**. Para obtener más información sobre estas características, consulte la [documentación de eXtreme Scale ](http://www.ibm.com/support/knowledgecenter/SSTVLU).

|Etiqueta|Descripción|
|---|-----------|
|**ibm-websphere-extreme-scale:latest**|Utilice esta etiqueta para especificar la última imagen de IBM WebSphere eXtreme Scale.|
|**ibm-websphere-extreme-scale:8.6.1.x**|Utilice esta etiqueta para especificar un nivel de release específico de WebSphere eXtreme Scale.|
{: caption="Tabla 1. Imágenes de IBM WebSphere eXtreme Scale" caption-side="top"}

## Restricciones de uso 
{: #usage}

En la siguiente tabla se muestran las restricciones de uso que se aplican al uso gratuito de la imagen **ibm-websphere-extreme-scale** en {{site.data.keyword.Bluemix_notm}}.

|Entorno|Restricciones de uso|
|-----------|------------------|
|Desarrollo|La imagen `ibm-websphere-extreme-scale` permite un uso gratuito ilimitado para el desarrollo.|
|Producción|El uso en producción de la imagen **ibm-websphere-extreme-scale** se limita a los usuarios con licencia del producto IBM WebSphere eXtreme Scale. Para obtener más información sobre el uso en producción, consulte [Ver precios y comprar](https://www-112.ibm.com/software/howtobuy/buyingtools/paexpress/Express?P0=E1&part_number=D56YTLL,D56YVLL,D572PLL,D572MLL,D0594LL,D059GLL&catalogLocale=en_US&Locale=en_US&country=USA&PT=jsp&CC=USA&VP=&TACTICS=&S_TACT=&S_CMP=&brand=SB05).|
{: caption="Tabla 2. Restricciones de uso para las imágenes ibm-websphere-extreme-scale en {{site.data.keyword.Bluemix_notm}}" caption-side="top"}

**Nota:** El precio de la imagen **ibm-websphere-extreme-scale** es independiente del precio de los contenedores que utiliza en {{site.data.keyword.Bluemix_notm}}.

Revise los términos de uso de las imágenes certificadas de IBM en la sección Licencia de los [documentos de información sobre licencias](http://www-03.ibm.com/software/sla/sladb.nsf/displaylis/A44EE6829150B2998525801700516718?OpenDocument).


## Iniciación 
{: #get_started}

Puede utilizar la consola o la línea de mandatos para desplegar la imagen **ibm-websphere-extreme-scale**.

Este procedimiento incluye las siguientes opciones para desplegar la imagen con la consola o la línea de mandatos:

-   Seleccione la imagen en la [consola](#bmconsole) de {{site.data.keyword.Bluemix_notm}} desde el **Catálogo**.
-   Utilice el mandato cf ic para iniciar la imagen con la [línea de mandatos](#bmcommand) CF.

**Importante:** Antes de empezar, revise las restricciones de uso describas en la sección anterior para imágenes **ibm-websphere-extreme-scale**.


### Despliegue desde la consola de {{site.data.keyword.Bluemix_notm}}.
{: #bmconsole}

   1.  En el catálogo, seleccione **Contenedores** y elija la imagen **ibm-websphere-extreme-scale** para empezar a crear el contenedor.
   2.  Seleccione la versión de la imagen **ibm-websphere-extreme-scale** que desea utilizar en el menú desplegable **ETIQUETA/Versión**.
   3.  Cree un contenedor único.

        **Nota:** Si desea crear más de un contenedor, no utilice la opción **Escalable** para crear dos o más grupos de miembros de memoria caché de eXtreme Scale. Utilice la opción **Único** para crear varios contenedores y utilice la IU del panel de control de eXtreme Scale para unir miembros de memoria caché.

        Para obtener más información, consulte [Creación de un contenedor mediante el panel de control de {{site.data.keyword.Bluemix_notm}}](/docs/containers/container_single_ui.html#gui).

        1.  En el panel Crear un contenedor, especifique un **Nombre de contenedor**; por ejemplo, `wxs1`. No se permiten espacios ni otros caracteres no imprimibles en el nombre del contenedor.
        2.  Para **Tamaño**, especifique el tamaño de los contenedores utilizados para iniciar la imagen ibm-eXtreme-scale.
        3.  Para **Dirección IP pública**, especifique Solicitar y enlazar IP pública.
        4.  Abra el panel Opciones avanzadas. Cree un volumen de modo que los datos de configuración de eXtreme Scale se conserven cuando se instale una actualización continuada más adelante; por ejemplo:
            1. Pulse **Crear un volumen** en Opciones avanzadas y especifique el **Nombre de volumen**. Por ejemplo, escriba vol-wxs1.
            2. Pulse **Añadir una variable de entorno nueva**. La imagen **ibm-websphere-extreme-scale** necesita variables de entorno que se almacenan dentro del contenedor y a las que pueden acceder los usuarios autorizados.
            <table>
            <caption>Tabla 3. Variables de entorno para la imagen ibm-websphere-extreme-scale</caption>
               <tr>
                 <th>Nombre de la variable</th>
                 <th>Valor que se debe especificar </th>
               </tr>
               <tr>
                 <td>SECRET_KEY</td>
                 <td>El valor de la clave secreta; por ejemplo, <var class="keyword varname">s3cretKey!</var>. Es la clave secreta que utilizan los servidores de eXtreme Scale para enviar la comunicación interna a través de los múltiples miembros de un grupo de miembros de memoria caché. Para obtener más información, consulte la [sección Grupo de miembros de memoria caché](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txscreategridtut1.html).</td>
               </tr>
               <tr>
                 <td>XSADMIN_PWD</td>
                 <td>La contraseña del usuario xsadmin que inicia la sesión en la interfaz de usuario del panel de control de eXtremeScale; por ejemplo, <var class="keyword varname">xsadmin4Me!</var>.</td>
               </tr>
               </table>
  
              **Nota:** Puede cambiar la contraseña y la clave secreta después de que se inicie el contenedor de eXtreme Scale. Tanto la clave secreta como la contraseña tienen las siguientes reglas de contraseña. Asegúrese de especificar ambas siguiendo la regla siguiente:

              La contraseña y la clave secreta deben contener al menos un número [`0-9`], un carácter en mayúscula [`A-Z`], un carácter especial [`~!@#$%^&*()_-+=:;'[]{}|<>,.?/`] y deben tener al menos 10 caracteres.

              Para obtener más información sobre cómo modificar la contraseña, consulte la [documentación de administración](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/txsadministerxsld.html).

              El puerto se añade en el campo **Puerto público** de forma predeterminada.

   4.  Pulse **Crear** para crear un contenedor desde el Panel de control de {{site.data.keyword.Bluemix_notm}}.
    
   5.  Cuando se hayan iniciado por completo el contenedor y los servidores de eXtreme Scale, inicie una sesión en la interfaz de usuario del panel de control de eXtreme Scale y verifique el despliegue. En la consola de {{site.data.keyword.Bluemix_notm}}, pulse un contenedor para ver la IP Pública. Se puede acceder al contenedor de WebSphere eXtreme Scale desde el siguiente URL:

    ```
    https://<IP_Pública>:9443
    ```
    {: pre}

   **Nota:** El servidor tarda entre 5 y 10 minutos en iniciarse por completo.


### Despliegue la imagen desde la línea de mandatos.
{: #bmcommand}
 1.  Cree un volumen de Docker para almacenar los datos permanentes de IBM WebSphere eXtreme Scale. Por ejemplo, escriba el mandato siguiente:

  ```
  cf ic volume create <vol-wxs1>
  ```
  {: pre}

 2.  Escriba el siguiente mandato para iniciar el contenedor de IBM WebSphere eXtreme Scale en {{site.data.keyword.Bluemix_notm}}:

    **Nota:** Sustituya yourSecretKey y yourXsadminPassword por una contraseña que cumpla la regla de contraseña explicada anteriormente.

   ```
   cf ic run -itdP --volume <vol-wxs1:/vol> --name <wxs1> -m 1024 -e SECRET_KEY=<yourSecretKey> -e XSADMIN_PWD=<yourXsadminPassword> registry.ng.bluemix.net/ibm-websphere-extreme-scale:latest
   ```
   {: pre}
        
     **Nota:** El nombre del contenedor solo debe contener caracteres alfanuméricos y debe empezar por una letra. Como alternativa, puede establecer <code>-e XSLD_CONTAINER_ALIAS=<wxs1></code> si necesita que el nombre del contenedor contenga otros caracteres no alfanuméricos.

 3.  Solicite una IP pública que se utilizará para el primer contenedor de IBM WebSphere eXtreme Scale. Especifique el siguiente mandato para escribir la dirección IP:

   ```
   cf ic ip request
   ```
   {: pre}

 4.  Especifique el siguiente mandato para enlazar el contenedor de WebSphere eXtreme Scale a la dirección IP pública que ha solicitado:

   ```
   cf ic ip bind <REQUESTED_IP> wxs1
   ```
   {: pre}

 5.  Especifique el siguiente mandato para verificar que el estado del contenedor `wxs1` es Running:

   ```
   cf ic ps -a
   ```
   {: pre}

 6.  Especifique el mandato siguiente para conectar con el contenedor `wxs1` y supervisar el registro nanny:

   ```
   cf ic exec -it <wxs1> tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
        ```
   {: pre}

    **Nota:** El servidor tarda entre 5 y 10 minutos en iniciarse.

 7.  Cuando se hayan iniciado por completo el contenedor y los servidores de eXtreme Scale, inicie una sesión en la interfaz de usuario del panel de control de eXtreme Scale y verifique el despliegue:

   ```
   https://<REQUESTED_IP>:9443
   ```
   {: pre}


## Adición de capacidad de memoria caché 
{: #caching}

1.  Despliegue otra imagen desde la consola o desde la línea de mandatos siguiendo los mismos pasos que antes.
2.  Cuando se hayan iniciado por completo el contenedor y los servidores de eXtreme Scale, inicie una sesión en la interfaz de usuario del panel de control de eXtreme Scale de este miembro:

    ```
    https://<REQUESTED_IP>:9443
    ```
    {: pre}

3.  Abra la página Miembros de memoria caché y, bajo la columna **Acción** de este miembro, seleccione **Unirse**.
4.  Especifique la IP pública solicitada del primer miembro, la clave secreta del primer miembro y un nombre exclusivo para este miembro en el grupo de memoria caché.
5.  Pulse **Unirse** y espere a que finalice la tarea. Cuando la tarea se haya completado, se mostrarán todos los miembros en la página Miembros de memoria caché de datos.

## Realización de una actualización continuada 
{: #rolling_upgrade}

Hay que seguir los pasos siguientes para realizar una actualización continuada de los contenedores que ejecutan `ibm-websphere-extreme-scale` en {{site.data.keyword.containershort_notm}}.

**Nota:** Si su grupo de miembros de memoria caché de eXtreme Scale solo contiene un miembro, elimine este contenedor de {{site.data.keyword.IBM_notm}} y vuélvalo a crear desde cero. Sin embargo, si el grupo de miembros de memoria caché de eXtreme Scale tiene más de un miembro y varios servidores de catálogos desplegados y desea conservar los datos de la memoria caché en {{site.data.keyword.containershort_notm}}, siga los pasos siguientes de una actualización continuada para que no se pierda ningún dato de la memoria caché. Primero actualice los miembros de la memoria caché que solo tengan servidores de contenedores y luego actualice los miembros de la memoria caché que también tengan servidores de catálogos.

1.  Inicie un nuevo contenedor denominado wxsmigrate y utilícelo para actualizar todos los contenedores existentes de eXtreme Scale. Consulte la [sección de iniciación](#get_started) de esta página para obtener más información. Los últimos archivos .zip de actualización de eXtreme Scale se desplegarán bajo la carpeta /tmp del nuevo contenedor wxsmigrate.
2.  Copie los archivos .zip de eXtreme Scale actualizados del contenedor wxsmigrate en el sistema local en el que ejecuta el plug-in de la CLI de {{site.data.keyword.containershort_notm}}:

    ```
    cf ic cp wxsmigrate:/tmp/xsld-bin-update.zip
    ```
    {: pre}
    
    ```
    cf ic cp wxsmigrate:/tmp/xsld-derby-update.zip
    ```
    {: pre}

3.  Ahora, para cada miembro de memoria caché del grupo de miembros de memoria caché, siga los pasos siguientes contenedor a contenedor:

    1. Copie los archivos .zip actualizados de eXtreme Scale del sistema local en el contenedor de eXtreme Scale que desea actualizar:
    
      ```
      cf ic cp  xsld-bin-update.zip  <nombre_contenedor>:/tmp/xsld-bin-update.zip
      ```
      {: pre}
  
      ```
      cf ic cp  xsld-derby-update.zip  <nombre_contenedor>:/tmp/xsld-derby-update.zip
      ```
      {: pre}
     
    2. Conecte con el contenedor que desea actualizar: 
    
      ```
      cf ic exec -it <nombre_contenedor> bash
      ```
      {: pre}

    3. Detenga los servidores XSLD del contenedor que está preparado para la actualización.
      
      ```
      /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
      ```
      {: pre}
      
      Espere hasta que los servidores se hayan detenido.
      
    4. Descomprima el código de actualización más reciente de eXtreme Scale dentro del contenedor:
      
      ```
      unzip  -o  /tmp/xsld-bin-update.zip   -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
      
      ```
      unzip  -o  /tmp/xsld-derby-update.zip -d /opt/ibm/WebSphere/eXtremeScale
      ```
      {: pre}
    
    5. Vuelva a iniciar el servidor XSLD:
    
      ```
      /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
      ```
      {: pre}
      
    6. Supervise el registro nanny para asegurarse de que se han iniciado los servidores:
    
      ```
      tail -f /opt/ibm/WebSphere/eXtremeScale/wlp/startscripts/nanny/log/nanny.log
      ```
      {: pre}
      
4. Una vez se haya completado la actualización en todos los contenedores de IBM, elimine el contenedor wxsmigrate:

 ```
 cf ic rm -f <wxsmigrate>
 ```
 {: pre}

## Resolución de problemas 
{: #troubleshoot}

###Resolución de problemas de conectividad de miembros de memoria caché cuando se pierde la correlación de host 
{: #troubleshoot_cache}

Siga los pasos siguientes cuando miembros de un grupo de memoria caché pierdan la conectividad debido a la pérdida de la correlación de host entre contenedores.

1.  Conecte con cada contenedor:

    ```
    cf ic exec -it <nombre_contenedor> bash
    ```
    {: pre}

2.  Obtenga la IP privada, el nombre de host y el alias de host de cada miembro.

    El alias de host es el valor de la variable de entorno XSLD_CONTAINER_ALIAS:

    ```
    echo ${<XSLD_CONTAINER_ALIAS>}
    ```
    {: pre}

    La IP privada es la dirección IP que devuelve la ejecución del mandato hostname -I dentro del contenedor:

    ```
    hostname -I
    ```
    {: pre}

    El nombre de host es el nombre de host que devuelve la ejecución del mandato hostname dentro del contenedor.


3.  Ejecute el script recoverXSLD.sh en cada miembro pasando todos los alias, IP y nombres de host de los otros miembros:

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/recoverXSLD.sh <alias_miembro2> <IP_privada_miembro2> <nombrehost_miembro2> ... <alias_miembroN> <IP_privada_miembroN> <nombrehost_miembroN>
    ```
    {: pre}

4.  Detenga la ejecución de los servidores ejecutando stopXSLD.sh en cada contenedor del grupo de memoria caché. Ejecute stopXSLD.sh:

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
    ```
    {: pre}

5.  Si stopXSLD.sh no progresa mientras intenta detener el servidor de catálogos, siga estos pasos:
    1.  Liste el proceso del servidor de catálogos:

        ```
        ps -ef | grep cs-default
        ```
        {: pre}

    2.  Finalice el proceso del servidor de catálogos utilizando el ID de proceso:

        ```
        kill -9 <ID_proceso_servidor_catálogo>
        ```
        {: pre}

    3.  Vuelva a ejecutar stopXSLD.sh:

        ```
        /opt/ibm/WebSphere/eXtremeScale/wxs/stopXSLD.sh
        ```
        {: pre}

6.  Inicie XSLD en todos los contenedores aproximadamente a la misma hora. Ejecute startXSLD.sh:

    ```
    /opt/ibm/WebSphere/eXtremeScale/wxs/startXSLD.sh
    ```
    {: pre}


### Resolución de problemas de una tarea de duplicación de Derby detenida 
{: #troubleshoot_derby}

Si una tarea de duplicación de Derby no ha pasado del 45% en más de diez minutos, es probable que se deba a un problema de la red. Siga los siguientes pasos para solucionar este problema:

1.  Utilice un programa de utilidad REST POSTing, como cURL, para cancelar la tarea de duplicación de derby.

    ```
    curl -X PUT --header "Content-Type: application/json" --header "Accept: application/json" "https://<PUBLIC_IP>:9445/wxsadmin/v1/task/cancel/<DERBY_REPLICATION_TASK_ID>/force"
    ```
    {: pre}

    **Nota:** <PUBLIC_IP> es la utilizada en el mandato para iniciar la tarea de duplicación de Derby y <DERBY_REPLICATION_TASK_ID> es el ID de tarea recibido en la salida del mandato.

2.  Vuelva a ejecutar la tarea de duplicación de derby.

## Acceso a la API REST 
{: #api}

Cuando eXtreme Scale se haya desplegado en {{site.data.keyword.containershort_notm}}, siga los pasos siguientes para acceder a la API REST:

1.  Recupere el nombre de host del contenedor que desea utilizar para conectar con la API REST.
    1.  Conecte con el contenedor:

        ```
        cf ic exec -it <nombre_contenedor> bash
        ```
        {: pre}

    2.  Liste el nombre de host:

        ```
        hostname
        ```
        {: pre}

2.  Recupere la IP pública del mismo contenedor.

    Si el contenedor no tiene una IP pública, siga los pasos de la sección de iniciación para proporcionar al contenedor una IP pública utilizando la [consola](#bmconsole) o la [línea de mandatos](#bmcommand) de {{site.data.keyword.Bluemix_notm}}.

3.  Cree una entrada en el archivo hosts del sistema cliente local que correlacione la IP pública y el nombre de host del contenedor. La entrada se parecerá a esta:

    ```
    169.44.8.87 instance-05106102
    ```
    {: pre}

    Los nombres típicos de vía de acceso completa para archivos hosts son:

    -   Para UNIX: /etc/hosts
    -   Para Windows: C:\\Windows\\System32\\drivers\\etc\\hosts
4.  Acceda a las API REST de Swagger en un navegador.
    -   Para operaciones CRUD:

        ```
        https://<host_name>:9444/ibm/api/explorer
        ```
        {: pre}

    -   Para operaciones administrativas:

        ```
        https://<host_name>:9445/ibm/api/explorer
        ```
        {: pre}


Para ver ejemplos y actualizaciones recientes, visite [WebSphere eXtreme
Scale en GitHub](https://github.com/ibmWebsphereExtremeScale). Consulte también la siguiente documentación y vídeos de soporte para obtener más ayuda con el despliegue
de eXtreme Scale:

-   [Documentación de IBM WebSphere eXtreme Scale](http://www.ibm.com/support/knowledgecenter/SSTVLU_8.6.1/com.ibm.websphere.extremescale.doc/cxsgetstartxsld.html)
-   [Foro de XS developerWorks](https://www.ibm.com/developerworks/community/forums/html/forum?id=11111111-0000-0000-0000-000000000778&ps=25)
-   [Iniciación
a WebSphere eXtreme Scale V8.6.x](https://www.youtube.com/watch?v=xw7Ck3c40rA)
-   [Adición de memoria caché
con WebSphere eXtreme Scale - Despliegue de Liberty](https://www.youtube.com/watch?v=I1g88bnstn8)
-   [Configuración de un cliente
WAS para que trabaje con una tabla DynaCache de despliegue de Liberty de WebSphere eXtreme Scale](https://www.youtube.com/watch?v=RvhcknNX-4Q)
-   [Configuración de un cliente
WAS para que trabaje con una tabla de memoria caché de sesión de despliegue de Liberty de WebSphere eXtreme Scale](https://www.youtube.com/watch?v=GKTy9I66Klc)

