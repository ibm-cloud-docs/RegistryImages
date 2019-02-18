---

copyright:
  years: 2017, 2019
lastupdated: "2019-01-03"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip} 
{:table: .aria-labeledby="caption"}

# Iniciación a la imagen `ibm-backup-restore`
{: #ibmbackup_restore_starter}

La imagen `ibm-backup-restore` contiene los paquetes preinstalados necesarios para realizar copia de seguridad y restaurar el almacén persistente de {{site.data.keyword.containerlong}}.
{:shortdesc}

Puede acceder a las imágenes que suministra {{site.data.keyword.IBM_notm}} desde la línea de mandatos; consulte las [imágenes públicas de IBM](/docs/services/Registry/registry_public_images.html#public_images).
{: tip}

## Modo de funcionamiento 
{: #how_it_works}

Con la imagen `ibm-backup-restore`, puede crear una copia de seguridad puntual o planificada para datos de app almacenados en un volumen persistente (PV) del clúster o para restaurar datos de app en un PV. Para realizar copia de seguridad y restaurar datos, despliegue un pod desde la imagen `ibm-backup-restore`. A continuación, monte el PVC que enlace con el pod el PV del que desea hacer copia de seguridad o el PV que desea utilizar para restaurar los datos. 

**¿A dónde van mis datos? ¿Cómo puedo acceder a ellos?** 

Los datos de los que se hace copia de seguridad se almacenan en una instancia de servicio de {{site.data.keyword.cos_full_notm}}. Para acceder al servicio, utilice sus credenciales de servicio de {{site.data.keyword.cos_full_notm}} como variables de entorno en el pod `ibm-backup-restore` o edite el archivo `config.conf` en el pod en ejecución.

**¿Puedo restaurar datos copiados en otra app o en otro PV?** 

Sí, también puede restaurar los datos guardados de la instancia de servicio de {{site.data.keyword.cos_full_notm}} en un PV del clúster. Para restaurar datos, debe crear un pod de restauración desde la imagen `ibm-backup-restore`. A continuación, monte el PVC que enlaza PV que desea utilizar al pod.  

## Qué incluye 
{: #whats_included}

Cada imagen `ibm-backup-restore` contiene los siguientes paquetes de software:

-   Alpine 3.7
-   Duplicity 0.7.10
-   Paquetes python y gpgme


## Configuración de una instancia de servicio de {{site.data.keyword.cos_full_notm}} 
{: #object_storage}

Cree y configure una instancia de servicio de {{site.data.keyword.cos_full_notm}} para que sirva como repositorio para los datos de los que desea hacer copia de seguridad.
{: shortdesc}

1. Despliegue una instancia de servicio de {{site.data.keyword.cos_full_notm}}.
   1.  Abra el [catálogo de {{site.data.keyword.Bluemix_notm}} ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/cloud-object-storage).
   2.  Especifique un nombre para la instancia de servicio, como por ejemplo `cos-backup`, y seleccione **default**
como grupo de recursos. 
   3.  Revise las [opciones del plan ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.ibm.com/cloud-computing/bluemix/pricing-object-storage#s3api) para ver la información sobre precios y seleccione un plan. 
   4.  Pulse **Crear**.
2. Recupere las credenciales de la instancia de servicio de {{site.data.keyword.cos_full_notm}}.
   1.  En el área de navegación de la página de detalles del servicio, pulse **Credenciales de servicio**.
   2.  Pulse **Nueva credencial**. Se abre un recuadro de diálogo. 
   3.  Especifique un nombre para las credenciales.
   4.  En **Añadir parámetros de configuración en línea (opcional)**, escriba `{"HMAC":true}` para crear las credenciales HMAC adicionales que utiliza el pod `ibm-backup-restore` para la autenticación HMAC con el servicio {{site.data.keyword.cos_full_notm}}. 
   5.  Pulse **Añadir**. Sus nuevas credenciales aparecerán en la tabla **Credenciales de servicio**.
   6.  Pulse **Ver credenciales**. 
   7.  Anote el valor de **access_key_id** y de **secret_access_key**, que encontrará en la sección **cos_hmac_keys**. 
3. Cree su primer grupo de {{site.data.keyword.cos_full_notm}}.
   1. En el área de navegación de la página de detalles del servicio, pulse **Grupos**. 
   2. Pulse **Crear grupo**. Se abre un recuadro de diálogo.
   3. Especifique un nombre exclusivo para el grupo. El nombre debe ser exclusivo dentro de {{site.data.keyword.cos_full_notm}} entre todas las regiones y entre todas las cuentas de {{site.data.keyword.Bluemix_notm}}. 
   4. En el menú desplegable **Capacidad de recuperación**, seleccione el nivel de disponibilidad que desea para los datos. Para obtener más información, consulte [Regiones y puntos finales de {{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage/basics/endpoints.html#select-regions-and-endpoints). 
   5. Cambie la **Ubicación** por la región en la que desea almacenar los datos. Tenga en cuenta que es posible que los datos no se puedan almacenar en todas las regiones por motivos legales.  
   6. Pulse **Crear**. 
4. Recupere el nombre de host de {{site.data.keyword.cos_full_notm}} para el grupo. 
   1. Pulse el nombre del grupo que ha creado en el paso anterior. 
   2. En el área de navegación de la página de detalles de servicio, pulse **Grupos** > **Configuración**. 
   3. Anote el URL público que puede utilizar para acceder a los datos de su grupo. 


Consulte la documentación de [{{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage/about-cos.html#about-ibm-cloud-object-storage) para obtener más información sobre cómo configurar la instancia de servicio.


## Copia de seguridad de los datos de un volumen persistente
{: #scheduled_backup}

Puede crear una copia de seguridad puntual o planificada para cualquier volumen persistente (PV) montado en el pod de la app mediante una reclamación de volumen persistente (PVC).  
{: shortdesc}

El siguiente ejemplo le guía por los pasos a seguir para desplegar un pod de copia de seguridad de la imagen `ibm-backup-restore`, montar un PV existente en el pod de copia de seguridad mediante un PVC y hacer copia de seguridad de los datos del PV en la instancia de servicio de {{site.data.keyword.cos_full_notm}}.  

**Antes de empezar**

-   [Configure una instancia de servicio de {{site.data.keyword.cos_full_notm}}](#object_storage). 
-   Instale las [CLI](/docs/containers/cs_cli_install.html#cs_cli_install) necesarias para crear un clúster y trabajar con el mismo.
-   [Cree un clúster estándar](/docs/containers/cs_clusters.html#clusters_cli) o utilice uno existente.
-   [Defina su clúster como destino de la CLI](/docs/containers/cs_cli_install.html#cs_cli_configure).
-   Cree una reclamación de volumen persistente (PVC) para su [almacenamiento de archivos](/docs/containers/cs_storage_file.html#add_file) o [almacenamiento en bloque](/docs/containers/cs_storage_block.html#add_block) y móntela en el despliegue de la app.

Para hacer una copia de seguridad de un PV existente, complete los pasos siguientes: 

1. Obtenga el nombre del PVC que enlaza el PV del que desea realizar una copia de seguridad.

   ```
   kubectl get pvc
   ```
   {: pre}

2. Cree un pod de copia de seguridad desde la imagen `ibm-backup-restore`. Para acceder a los datos del PV, debe montar el PVC que enlaza el PV al pod de copia de seguridad. En el ejemplo siguiente se crea un pod de copia de seguridad que ejecuta una copia de seguridad incremental diaria. Para crear una copia de seguridad con valores diferentes, revise una lista completa de [opciones de variables de entorno](#reference_backup_restore).

   **Importante**: la imagen `ibm-backup-restore` se debe desplegar en un solo pod y no se puede utilizar como parte de un despliegue de Kubernetes.
   
   Para ver la imagen, seleccione un destino para el registro global con el mandato `ibmcloud cr region-set global`. Luego ejecute `ibmcloud cr images --include-ibm` para ver una lista de imágenes públicas de IBM. 
   {: tip}
  
   ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: backuppod
   spec:
     containers:
     - image: registry.bluemix.net/ibm-backup-restore
       name: backupcontainer
       env:
       - name: OBJECTSTORAGE
         value: S3
       - name: ACCESS_KEY_ID
         value: '<access_key_id>'
       - name: SECRET_ACCESS_KEY
         value: '<secret_access_key>'
       - name: ENDPOINT
         value: '<regional_endpoint>'
       - name: BUCKET_NAME
         value: '<bucket_name>'
       - name: BACKUP_DIRECTORY
         value: /myvol
       - name: BACKUP_NAME
         value: <backup_name>
       - name: SCHEDULE_TYPE
         value: periodic
       - name: SCHEDULE_INFO
         value: daily
       - name: BACKUP_TYPE
         value: incremental
       command: ["/bin/bash", "./vbackup"]
       volumeMounts:
       - mountPath: /myvol
         name: backup-volume
     volumes:
     - name: backup-volume
       persistentVolumeClaim:
         claimName: <pvc_name>  
   ```
   {: codeblock}
   
   <table>
   <caption>Componentes del archivo YAML</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Icono de idea"/> Visión general de los componentes del archivo yaml</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>El ID de la clave de acceso que ha recuperado como parte de las credenciales de la instancia de servicio de {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</code></td>
     <td>La clave de acceso secreta que ha recuperado como parte de las credenciales de la instancia de servicio de {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>El URL del punto final de la API regional que utiliza para acceder a {{site.data.keyword.cos_full_notm}} en una región específica. </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>El nombre del grupo que desea utilizar para almacenar las copias de seguridad en {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>Un nombre exclusivo para el objeto que contiene la copia de seguridad en el grupo </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>El nombre del PVC que enlaza el PV del que desea realizar una copia de seguridad. </td>
     </tr>
     </tbody>
     </table>
    
3.  Cree el pod de la copia de seguridad e inicie una copia de seguridad de los datos de PV. 

    ```
    kubectl apply -f backuppod.yaml
    ```
    {: pre}

4.  Compruebe que el pod se está ejecutando.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}
    
5.  Compruebe que la copia de seguridad se ha ejecutado correctamente. 

    ```
    kubectl logs backuppod
    ```
    {: pre}

6.  Revise la copia de seguridad en {{site.data.keyword.cos_full_notm}} en la GUI de {{site.data.keyword.Bluemix_notm}}.
    1.  En el panel de control de {{site.data.keyword.Bluemix_notm}}, busque la instancia de servicio de {{site.data.keyword.cos_full_notm}}. 
    2.  En el área de navegación, seleccione **Grupos** y pulse en el grupo que ha utilizado en la configuración de la copia de seguridad. La copia de seguridad se visualiza como un objeto en el grupo. 
    3.  Revise los archivos comprimidos. Puede descargar el archivo `vol1.difftar.gz`, extraer el archivo y verificar los datos que se han copiado. 
        
        **Importante**: si suprime o modifica algún archivo de {{site.data.keyword.cos_full_notm}}, estos archivos no se podrán recuperar.

Su copia de seguridad está disponible. Si ha configurado las copias de seguridad para crear una copia de seguridad completa puntual, deberá ejecutar el script de copia de seguridad cada vez que desee crear una nueva copia de seguridad. Si ha configurado el contenedor para ejecutar una copia de seguridad incremental periódicamente, la copia de seguridad se ejecutará según lo previsto.

## Restauración de datos de {{site.data.keyword.cos_full_notm}} en su clúster
{: #restore_script_cli}

Puede restaurar datos de la instancia de servicio de {{site.data.keyword.cos_full_notm}} en un PV del clúster. 

**Antes de empezar**

-   [Defina su clúster como destino de la CLI](/docs/containers/cs_cli_install.html#cs_cli_configure).
-   [Cree una copia de seguridad para un PV en el clúster](#scheduled_backup).

Para restaurar datos de {{site.data.keyword.cos_full_notm}} en un PV, complete los pasos siguientes: 

1. Obtenga el nombre del PVC que enlaza el PV en el que desea restaurar los datos. 

   ```
   kubectl get pvc
   ```
   {: pre}

2. Cree un pod de restauración desde la imagen `ibm-backup-restore`. Para restaurar datos en un PV, debe montar el PVC que enlaza el PV con el pod de restauración. 
   
   ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: restorepod
   spec:
     containers:
     - image: registry.bluemix.net/ibm-backup-restore
       name: restorecontainer
       env:
       - name: OBJECTSTORAGE
         value: S3
       - name: ACCESS_KEY_ID
         value: '<access_key_ID>'
       - name: SECRET_ACCESS_KEY
         value: '<secret_access_key>'
       - name: ENDPOINT
         value: '<regional_endpoint>'
       - name: BUCKET_NAME
         value: '<bucket_name>'
       - name: RESTORE_DIRECTORY
         value: /myvol
       - name: BACKUP_NAME
         value: <backup_name>
       command: ["/bin/sh", "./vrestore"]
       volumeMounts:
       - mountPath: /myvol
         name: restore-volume
     volumes:
     - name: restore-volume
       persistentVolumeClaim:
         claimName: <pvc_name> 
   ```
   {: codeblock}
   
   <table>
   <caption>Componentes del archivo YAML</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Icono de idea"/> Visión general de los componentes del archivo yaml</th>
   </thead>
    <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>El ID de la clave de acceso que ha recuperado como parte de las credenciales de la instancia de servicio de {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</code></td>
     <td>La clave de acceso secreta que ha recuperado como parte de las credenciales de la instancia de servicio de {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>El URL del punto final de la API regional que utiliza para acceder a {{site.data.keyword.cos_full_notm}} en una región específica. </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>El nombre del grupo que ha utilizado para almacenar las copias de seguridad en {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>El nombre exclusivo del objeto que contiene la copia de seguridad en el grupo. Debe utilizar el nombre que ha utilizado en el pod de copia de seguridad para almacenar los datos en {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>El nombre del PVC que enlaza el PV en el que desea restaurar los datos. </td>
     </tr>
     </tbody>
     </table>

3.  Cree el pod de restauración y empiece a restaurar sus datos.

    ```
    kubectl apply -f restorepod.yaml
    ```
    {: pre}
    
4.  Compruebe que el pod se está ejecutando.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    restorepod        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    El pod ejecuta el mandato restaurar y se detiene. El mensaje _CrashLoopBackOff_ significa que Kubernetes intenta reiniciar el pod.

5.  Elimine el pod para evitar que consuma más recursos.

    ```
    kubectl delete -f restorepod.yaml
    ```
    {: pre}

6.  Compruebe que los datos se han restaurado correctamente.

    ```
    kubectl logs restorepod
    ```
    {: pre}

Ha restaurado satisfactoriamente la copia de seguridad. Ahora puede montar el PVC que enlaza el PV con cualquier otro pod del clúster para acceder a los archivos restaurados. Si los datos del contenedor a los que se ha realizado copia de seguridad incluían un usuario no root, deberá añadir permisos no root al nuevo contenedor. Para obtener más información, consulte [Adición de usuario no root a volúmenes](/docs/containers/cs_troubleshoot_storage.html#cs_storage_nonroot).


## Cifrado de las copias de seguridad 
{: #encrypting_backups}

Cifre los datos de la instancia de {{site.data.keyword.cos_full_notm}}.

1.  Descargue <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Icono de enlace externo"></a> para crear una clave de cifrado.
2.  Cree una clave de cifrado en su unidad de cifrado local. Puede aceptar los valores predeterminados pulsando ENTER.

    **Importante:** anote la frase de contraseña que cree. Si pierde la frase de contraseña, no podrá descifrar ninguna información cifrada con la clave.

    ```
    gpg --gen-key
    ```
    {: pre}

    En función de la versión de <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Icono de enlace externo"></a>, es posible que tenga que utilizar `gpg2` en lugar de `gpg` en sus mandatos.

3.  Verifique la clave.

    ```
    gpg --list-keys
    ```
    {: pre}

    ```
    $ gpg --list-keys
    /Users/Username/.gnupg/pubring.gpg
    ------------------------------------
    pub   2048R/XXXXXXXX 2016-10-27
    uid       [ultimate] Example Name (This is an example key) <example_email_address>
    sub   2048R/YYYYYYYY 2016-10-27
    ```
    {: screen}

4.  Exporte la clave de cifrado con el valor de la clave `sub`. Asigne al archivo el nombre `encryption.asc`.

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
    ```
    {: pre}

    En este ejemplo, la subclave tiene el valor `YYYYYYYY`.

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
    ```
    {: pre}

5.  Cree un archivo de variable de entorno para el contenedor de copia de seguridad de cifrado en un directorio local.

    ```
    touch <encryption_env-file_name>
    ```
    {: pre}

6.  Edite el archivo de configuración del pod y añada los campos siguientes. Para las variables de entorno en blanco, especifique los valores a partir de las credenciales de {{site.data.keyword.cos_full_notm}} que ha anotado anteriormente. Incluya las comillas que se utilizan en las
credenciales. Para **ENCRYPTION_PASSPHRASE**, incluya una frase de contraseña para proteger mediante contraseña la copia de seguridad. Esta frase de contraseña es distinta de la que especificó al crear la clave de cifrado. Debe incluir esta frase de contraseña al realizar copia de seguridad de los datos y restaurarlos.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: backuppod
    spec:
      containers:
      - image: registry.bluemix.net/ibm-backup-restore
        name: backupcontainer
        env:
        - name: OBJECTSTORAGE
          value: S3
        - name: ACCESS_KEY_ID
          value: '<access_key_id>'
        - name: SECRET_ACCESS_KEY
          value: '<secret_access_key>'
        - name: ENDPOINT
          value: '<regional_endpoint>'
        - name: BUCKET_NAME
          value: '<bucket_name>'
        - name: BACKUP_DIRECTORY  
          value: /myvol
        - name: BACKUP_NAME
          value: <backup_name> 
        - name: SCHEDULE_TYPE
          value: periodic
        - name: SCHEDULE_INFO
          value: daily
        - name: BACKUP_TYPE
          value: incremental
        - name: ENCRYPTION_REQUIRED
          value: yes
        - name: ENCRYPTION_PASSPHRASE
          value: <passphrase>
        volumeMounts:
        - mountPath: /myvol
          name: backup-volume
      volumes:
      - name: backup-volume
        persistentVolumeClaim:
          claimName: <pvc_name>  
    ```
    {: codeblock}
   
    <table>
    <caption>Componentes del archivo YAML</caption>
    <thead>
    <th colspan=2><img src="../images/idea.png" alt="Icono de idea"/> Visión general de los componentes del archivo yaml</th>
    </thead>
     <tbody>
     <tr>
     <td><code>&lt;access_key_ID&gt;</code></td>
     <td>El ID de la clave de acceso que ha recuperado como parte de las credenciales de la instancia de servicio de {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;secret_access_key&gt;</em></code></td>
     <td>La clave de acceso secreta que ha recuperado como parte de las credenciales de la instancia de servicio de {{site.data.keyword.cos_full_notm}}.</td>
     </tr>
     <tr>
     <td><code>&lt;regional_endpoint&gt;</code></td>
     <td>El URL del punto final de la API regional que utiliza para acceder a {{site.data.keyword.cos_full_notm}} en una región específica. </td>
     </tr>
     <tr>
     <td><code>&lt;bucket_name&gt;</code></td>
     <td>El nombre del grupo que desea utilizar para almacenar las copias de seguridad en {{site.data.keyword.cos_full_notm}}. </td>
     </tr>
     <tr>
     <td><code>&lt;backup_name&gt;</code></td>
     <td>Un nombre exclusivo para el objeto que contiene la copia de seguridad en el grupo.</td>
     </tr>
     <tr>
     <td><code>&lt;passphrase&gt;</code></td>
     <td>La serie de caracteres que desee utilizar para la copia de seguridad. Debe incluir esta frase de contraseña cuando restaure sus datos. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>El nombre del PVC que enlaza el PV del que desea realizar una copia de seguridad. </td>
     </tr>
     </tbody>
     </table>
   
    Estos valores crean una copia de seguridad incremental diaria que está cifrada. Para crear una copia de seguridad con valores diferentes, revise una lista completa de [opciones de variables de entorno](#reference_backup_restore).
    
7.  Cree el pod de copia de seguridad. 

    ```
    kubectl apply -f backuppod.yaml 
    ```
    {: pre}

8.  Compruebe que el pod se está ejecutando.

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

9.  Copie la clave de cifrado en el directorio `/backup_restore` del contenedor que se ha creado a partir de la imagen `ibm-backup-restore`.

    ```
    kubectl cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    Guarde una copia de la clave de cifrado localmente para descifrar sus datos.

10. Inicie una sesión en el pod y vaya a la carpeta `backup_restore`. 

    ```
    kubecl exec -it <pod_name> bash
    ```
    {: pre}

11. Compruebe que el archivo `encryption.asc` se ha copiado en la carpeta `backup_restore`.

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

12. Ejecute el script de copia de seguridad en la carpeta backup_restore.

    ```
    ./vbackup &
    ```
    {: codeblock}

13. Para confirmar que la copia de seguridad está cifrada, revise los archivos de la instancia de servicio de {{site.data.keyword.cos_full_notm}}. Ahora se ha añadido `.gpg` al final del nombre de los archivos.

Su copia de seguridad está cifrada. Para restaurar los archivos, siga los pasos del apartado [Restauración de datos de {{site.data.keyword.cos_full_notm}} en un PVC del clúster](#restore_script_cli) e incluya el archivo `encryption.asc` en el directorio `backup_restore` del pod que ejecuta el proceso de restauración. Si la copia de seguridad está cifrada, debe proporcionar las variables de entorno **ENCRYPTION_REQUIRED** y **ENCRYPTION_PASSPHRASE** cuando cree el pod de restauración.


## Referencia de variables de entorno 
{: #reference_backup_restore}

Revise la lista completa de campos que se pueden pasar como variables de entorno o que se pueden editar en el archivo `config.conf` en un pod en ejecución. Cualquier valor que se pase como variable de entorno prevalece sobre el valor del archivo `config.conf`. Para revisar las variables de entorno para un pod, inicie una sesión en el pod con el mandato `kubectl exec` y ejecute `env`.

|Clave|Opciones del valor|
|---|-------------|
|ACCESS_KEY_ID|El valor **access_key_id** que forma parte de las credenciales de HMAC en {{site.data.keyword.cos_full_notm}}.|
|SECRET_ACCESS_KEY|El valor **secret_access_key** que forma parte de las credenciales de HMAC en {{site.data.keyword.cos_full_notm}}.|
|ENDPOINT|El nombre de host para acceder a los datos de grupo de {{site.data.keyword.cos_full_notm}}.|
|BUCKET|El nombre del grupo de {{site.data.keyword.cos_full_notm}} en el que están almacenados los datos de los que se ha hecho copia de seguridad.|
{: caption="Tabla 1. Variables de {{site.data.keyword.cos_full_notm}}" caption-side="top"}

|Clave|Opciones del valor|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: valor predeterminado. Vía de acceso de archivo absoluta del directorio en el que está montado el volumen. La copia de seguridad de los datos se realiza desde este directorio. No seleccione el directorio backup_restore, ya que dicho directorio contiene archivos correspondientes a los procesos de copia de seguridad y restauración.|
|BACKUP_NAME|*volume_backup*: valor predeterminado. Seleccione un nombre de copia de seguridad.|
|BACKUP_TYPE|*full*: valor predeterminado. Todos los archivos se copiarán cada vez.<br/> *incremental*: sólo se realiza copia de seguridad de los archivos nuevos o modificados. Si selecciona *incremental*, debe seleccionar valores para **SCHEDULING_INFO** y **SCHEDULING_TYPE**.|
|SCHEDULE_TYPE|*none*: valor predeterminado. Crear una copia de seguridad puntual.<br/> **Nota:** si elige crear una copia de seguridad puntual, el pod se elimina del clúster después de que finalice la copia de seguridad. <br/> *periodic*: cambie el valor por periodic para crear copias de seguridad planificadas.|
|SCHEDULE_INFO|*hourly*: crear una copia de seguridad por hora.<br/>*daily*: valor predeterminado. Crear una copia de seguridad diaria.<br/>*weekly*: crear una copia de seguridad semanal. Debe incluir esta variable si planifica una actualización periódica.|
|EXCLUDE_DIRECTORIES|*none*: valor predeterminado. Incluya la vía de acceso de archivos absoluta de los directorios que desee excluir de la copia de seguridad. Separe los directorios con una coma.|
{: caption="Tabla 2. Variables de copia de seguridad" caption-side="top"}

|Clave|Opciones del valor|
|---|-------------|
|BACKUP_NAME|*volume_backup*: valor predeterminado. Incluya el nombre de la copia de seguridad que se esté restaurando a partir de {{site.data.keyword.cos_full_notm}}.|
|RESTORE_DIRECTORY|*/backup*: valor predeterminado. Directorio absoluto en el que se monta el volumen. Los datos se restauran en este directorio. No seleccione el directorio `backup_restore`, ya que dicho directorio contiene archivos correspondientes a los procesos de copia de seguridad y restauración.|
{: caption="Tabla 3. Variables de restauración" caption-side="top"}

|Clave|Opciones del valor|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: valor predeterminado. Incluya esta variable de entorno si cambia el nombre de archivo de la clave de cifrado o si la clave se encuentra en un directorio distinto a `backup_restore`.|
|ENCRYPTION_REQUIRED|*no*: valor predeterminado.<br/> *yes*: si no cifra la copia de seguridad, no incluya ninguna variable de entorno de cifrado. Si va a cifrar la copia de seguridad, incluya esta clave con el valor `yes`.|
|ENCRYPTION_PASSPHRASE|Incluya una frase de contraseña para proteger la copia de seguridad. Esta frase de contraseña es distinta de la que especificó al crear la clave de cifrado. Debe incluir esta frase de contraseña cuando realice una copia de seguridad de los datos y cuando restaure los datos.|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: valor predeterminado.<br/> *yes*: incluya esta variable de entorno con `yes` si ha generado
la clave de cifrado directamente en el contenedor. La mayoría de los usuarios generan la clave en su sistema local y copian la clave en el pod, por lo que pueden dejar el valor predeterminado `no`.|
{: caption="Tabla 4. Variables de cifrado" caption-side="top"}

