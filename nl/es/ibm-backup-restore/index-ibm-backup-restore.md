---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-21"

keywords: IBM Cloud Container Registry, IBM Cloud Kubernetes Service, ibm-backup-restore, container image, back up data, restore data

subcollection: RegistryImages

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:table: .aria-labeledby="caption"}

# Iniciación a la imagen `ibmcloud-backup-restore`
{: #ibmbackup_restore_starter}

La imagen `ibmcloud-backup-restore` contiene los paquetes preinstalados necesarios para realizar copia de seguridad y restaurar el almacén persistente de {{site.data.keyword.containerlong}}.
{:shortdesc}

La imagen `ibmcloud-backup-restore` solo está disponible en los dominios de registro de `icr.io` y ha quedado en desuso en los dominios `registry.bluemix.net`. Para acceder a la imagen y desplegar contenedores desde la misma, el clúster debe almacenar una clave de API en un secreto de extracción de imagen para autorizar el acceso a {{site.data.keyword.registryshort_notm}}. Para los clústeres creados antes del 25 de febrero de 2019, debe actualizar el clúster para que tenga acceso a los dominios de registro de `icr.io` para poder utilizar la imagen `ibmcloud-backup-restore`. Para obtener más información sobre lo que ha cambiado y sobre cómo actualizar el clúster, consulte [Cómo autorizar al clúster para que extraiga imágenes de un registro](/docs/containers?topic=containers-images#cluster_registry_auth) y [Actualización de los clústeres existentes para que utilicen el secreto de extracción de imágenes de clave de API](/docs/containers?topic=containers-images#imagePullSecret_migrate_api_key). 
{: important}

Puede acceder a las imágenes que suministra {{site.data.keyword.IBM_notm}} desde la línea de mandatos; consulte las [imágenes públicas de IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Cómo funciona
{: #backup_restore_how_it_works}

Con la imagen `ibmcloud-backup-restore`, puede crear una copia de seguridad puntual o planificada para datos de app almacenados en un volumen persistente (PV) del clúster o para restaurar datos de app en un PV. Para realizar copia de seguridad y restaurar datos, despliegue un pod desde la imagen `ibmcloud-backup-restore`. A continuación, monte el PVC que enlace con el pod el PV del que desea hacer copia de seguridad o el PV que desea utilizar para restaurar los datos.

**¿A dónde van mis datos? ¿Cómo puedo acceder a ellos?**

Los datos de los que se hace copia de seguridad se almacenan en una instancia de servicio de {{site.data.keyword.cos_full_notm}}. Para acceder al servicio, utilice sus credenciales de servicio de {{site.data.keyword.cos_full_notm}} como variables de entorno en el pod `ibmcloud-backup-restore` o edite el archivo `config.conf` en el pod en ejecución.

**¿Puedo restaurar datos copiados en otra app o en otro PV?**

Sí, también puede restaurar los datos guardados de la instancia de servicio de {{site.data.keyword.cos_full_notm}} en un PV del clúster. Para restaurar datos, debe crear un pod de restauración desde la imagen `ibmcloud-backup-restore`. A continuación, monte el PVC que enlaza PV que desea utilizar al pod.  

## Qué incluye
{: #backup_restore_whats_included}

Cada imagen `ibmcloud-backup-restore` contiene los siguientes paquetes de software:

- Alpine 3.7
- Duplicity 0.7.10
- Python y paquetes GnuPG Made Easy (GPGme)

## Configuración de una instancia de servicio de {{site.data.keyword.cos_full_notm}}
{: #backup_restore_setup_object_storage}

Cree y configure una instancia de servicio de {{site.data.keyword.cos_full_notm}} para que sirva como repositorio para los datos de los que desea hacer copia de seguridad.
{: shortdesc}

1. Despliegue una instancia de servicio de {{site.data.keyword.cos_full_notm}}.
   1. Abra el [catálogo de {{site.data.keyword.cloud_notm}} ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://cloud.ibm.com/catalog/services/cloud-object-storage).
   2. Especifique un nombre para la instancia de servicio, como por ejemplo `cos-backup`, y seleccione **default**
como grupo de recursos.
   3. Revise las [opciones del plan ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.ibm.com/cloud-computing/bluemix/pricing-object-storage#s3api) para ver la información sobre precios y seleccione un plan.
   4. Pulse **Crear**.
2. Recupere las credenciales de la instancia de servicio de {{site.data.keyword.cos_full_notm}}.
   1. En el área de navegación de la página de detalles del servicio, pulse **Credenciales de servicio**.
   2. Pulse **Nueva credencial**. Aparecerá un recuadro de diálogo.
   3. Especifique un nombre para las credenciales.
   4. En el campo **Añadir parámetros de configuración en línea (opcional)**, escriba `{"HMAC":true}` para crear las credenciales HMAC adicionales que utiliza el pod `ibmcloud-backup-restore` para la autenticación HMAC con el servicio {{site.data.keyword.cos_full_notm}}.
   5. Pulse **Añadir**. Sus nuevas credenciales aparecerán en la tabla **Credenciales de servicio**.
   6. Pulse **Ver credenciales**.
   7. Anote el valor de **access_key_id** y de **secret_access_key**, que encontrará en la sección **cos_hmac_keys**.
3. Cree su primer grupo de {{site.data.keyword.cos_full_notm}}.
   1. En el área de navegación de la página de detalles del servicio, pulse **Grupos**.
   2. Pulse **Crear grupo**. Aparecerá un recuadro de diálogo.
   3. Especifique un nombre exclusivo para el grupo. El nombre debe ser exclusivo dentro de {{site.data.keyword.cos_full_notm}} entre todas las regiones y entre todas las cuentas de {{site.data.keyword.cloud_notm}}.
   4. En la lista **Capacidad de recuperación**, seleccione el nivel de disponibilidad que desea para los datos. Para obtener más información, consulte [Regiones y puntos finales de {{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-endpoints#endpoints).
   5. Cambie la **Ubicación** por la región en la que desea almacenar los datos. Tenga en cuenta que es posible que los datos no se puedan almacenar en todas las regiones por motivos legales.  
   6. Pulse **Crear**.
4. Recupere el nombre de host de {{site.data.keyword.cos_full_notm}} para el grupo.
   1. Pulse el nombre del grupo que ha creado en el paso anterior.
   2. En el área de navegación de la página de detalles de servicio, pulse **Grupos** > **Configuración**.
   3. Anote el URL público que puede utilizar para acceder a los datos de su grupo.

Consulte la documentación de [{{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage?topic=cloud-object-storage-about-ibm-cloud-object-storage) para obtener más información sobre cómo configurar la instancia de servicio.

## Copia de seguridad de los datos de un volumen persistente
{: #backup_restore_scheduled_backup}

Puede crear una copia de seguridad puntual o planificada para cualquier volumen persistente (PV) montado en el pod de la app mediante una reclamación de volumen persistente (PVC).  
{: shortdesc}

El siguiente ejemplo le mostrará cómo desplegar un pod de copia de seguridad de la imagen `ibmcloud-backup-restore`, montar un PV existente en el pod de copia de seguridad mediante un PVC y hacer copia de seguridad de los datos del PV en la instancia de servicio de {{site.data.keyword.cos_full_notm}}.  

**Antes de empezar**

- [Configure una instancia de servicio de {{site.data.keyword.cos_full_notm}}](#backup_restore_setup_object_storage).
- Instale las [CLI](/docs/containers?topic=containers-cs_cli_install#cs_cli_install) necesarias para crear un clúster y trabajar con el mismo.
- [Cree un clúster estándar](/docs/containers?topic=containers-clusters#clusters_cli) o utilice uno existente.
- [Defina su clúster como destino de la CLI](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure).
- Cree una reclamación de volumen persistente (PVC) para su [almacenamiento de archivos](/docs/containers?topic=containers-file_storage#add_file) o [almacenamiento en bloque](/docs/containers?topic=containers-block_storage#add_block) y móntela en el despliegue de la app.

Para hacer una copia de seguridad de un PV existente, complete los pasos siguientes:

1. Obtenga el nombre del PVC que enlaza el PV del que desea realizar una copia de seguridad.

   ```
   kubectl get pvc
   ```
   {: pre}

2. Cree un pod de copia de seguridad desde la imagen `ibmcloud-backup-restore`. Para acceder a los datos del PV, debe montar el PVC que enlaza el PV al pod de copia de seguridad. En el ejemplo siguiente se crea un pod de copia de seguridad que ejecuta una copia de seguridad incremental diaria. Para crear una copia de seguridad con valores diferentes, revise una lista completa de [opciones de variables de entorno](#backup_restore_env_reference).

   La imagen `ibmcloud-backup-restore` se debe desplegar en un solo pod y no se puede utilizar como parte de un despliegue de Kubernetes.
   {: important}

   Para ver la imagen, seleccione un destino para el registro global con el mandato `ibmcloud cr region-set global`. Luego ejecute `ibmcloud cr images --include-ibm` para ver una lista de imágenes públicas de IBM.
   {: tip}
  
   ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: backuppod
   spec:
     containers:
     - image: icr.io/ibm/ibmcloud-backup-restore
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
       - mountPath: <mount_path1>
         name: <pvc_name1>
       - mountPath: <mount_path1>
         name: <pvc_name2>
     volumes:
     - name: pvc_name1
       persistentVolumeClaim:
         claimName: <pvc_name1>
     - name: pvc_name2
       persistentVolumeClaim:
         claimName: <pvc_name2> 
   ```
   {: codeblock}

   <table>
   <caption>Tabla 1. Componentes del archivo YAML</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Icono de idea"/> Visión general de los componentes del archivo YAML</th>
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
     <td><code>&lt;mount_path&gt;</code></td>
     <td>La vía de acceso de montaje del volumen dentro del contenedor de copia de seguridad que monta la PVC de la que desea realizar una copia de seguridad. Si desea realizar copia de seguridad de varias PVC, especifique una vía de acceso de montaje para cada PVC. Asegúrese de que el nombre que utilice en <code>volumeMount.name</code> coincida con el nombre del volumen especificado en la sección <code>volumes.name</code> del archivo YAML. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>El nombre de la PVC de la que desea realizar una copia de seguridad. Si desea realizar copia de seguridad de varias PVC, especifique el nombre de volumen y la PVC correspondiente para cada PVC de la que desee realizar copia de seguridad. Asegúrese de que el nombre que utilice en <code>volumes.name</code> coincida con el nombre especificado en la sección <code>volumeMount.name</code> del archivo YAML.</td>
     </tr>
     </tbody>
     </table>

3. Cree el pod de la copia de seguridad e inicie una copia de seguridad de los datos de PV.

    ```
    kubectl apply -f backuppod.yaml
    ```
    {: pre}

4. Compruebe que el pod se está ejecutando.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME               READY     STATUS    RESTARTS   AGE
    backuppod          1/1       Running   0          1hr
    ```
    {: screen}

5. Compruebe que la copia de seguridad se ha ejecutado correctamente.

    ```
    kubectl logs backuppod
    ```
    {: pre}

6. Revise la copia de seguridad en {{site.data.keyword.cos_full_notm}} en la GUI de {{site.data.keyword.cloud_notm}}.
    1. En el panel de control de {{site.data.keyword.cloud_notm}}, busque la instancia de servicio de {{site.data.keyword.cos_full_notm}}.
    2. En el área de navegación, seleccione **Grupos** y pulse en el grupo que ha utilizado en la configuración de la copia de seguridad. La copia de seguridad se visualiza como un objeto en el grupo.
    3. Revise los archivos comprimidos. Puede descargar el archivo `vol1.difftar.gz`, extraer el archivo y verificar los datos que se han copiado.

       Si suprime o modifica algún archivo de {{site.data.keyword.cos_full_notm}}, estos archivos no se podrán recuperar.
       {: important}

Su copia de seguridad está disponible. Si ha configurado las copias de seguridad para crear una copia de seguridad completa puntual, deberá ejecutar el script de copia de seguridad cada vez que desee crear una nueva copia de seguridad. Si ha configurado el contenedor para ejecutar una copia de seguridad incremental periódicamente, la copia de seguridad se ejecutará según lo previsto.

## Restauración de datos de {{site.data.keyword.cos_full_notm}} en su clúster
{: #backup_restore_restore_script_cli}

Puede restaurar datos de la instancia de servicio de {{site.data.keyword.cos_full_notm}} en un PV del clúster.

Si tiene varias copias de seguridad completas en su instancia de servicio de {{site.data.keyword.cos_full_notm}}, el PVC se restaura con los datos de la copia de seguridad completa más reciente. Si tiene copias de seguridad incrementales, el PVC se restaura con los datos de la última copia de seguridad completa, incluyendo todas las copias de seguridad incrementales hasta el día en que inicie la restauración.

**Antes de empezar**

- [Defina su clúster como destino de la CLI](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure).
- [Cree una copia de seguridad para un PV en el clúster](#backup_restore_scheduled_backup).

Para restaurar datos de {{site.data.keyword.cos_full_notm}} en un PV, complete los pasos siguientes:

1. Obtenga el nombre del PVC que enlaza el PV en el que desea restaurar los datos.

   ```
   kubectl get pvc
   ```
   {: pre}

2. Cree un pod de restauración desde la imagen `ibmcloud-backup-restore`. Para restaurar datos en un PV, debe montar el PVC que enlaza el PV con el pod de restauración.
   {: codeblock}

    ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: restorepod
   spec:
     containers:
     - image: icr.io/ibm/ibmcloud-backup-restore
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
       - mountPath: <mount_path1>
         name: <pvc_name1>
       - mountPath: <mount_path1>
         name: <pvc_name2>
     volumes:
     - name: pvc_name1
       persistentVolumeClaim:
         claimName: <pvc_name1>  
     - name: pvc_name2
       persistentVolumeClaim:
         claimName: <pvc_name2> 
   ```
   {: codeblock}

   <table>
   <caption>Tabla 2. Componentes del archivo YAML</caption>
   <thead>
   <th colspan=2><img src="../images/idea.png" alt="Icono de idea"/> Visión general de los componentes del archivo YAML</th>
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
     <td><code>&lt;mount_path&gt;</code></td>
     <td>La vía de acceso de montaje del volumen dentro del contenedor de restauración que monta la PVC en la que desea restaurar los datos. Si desea restaurar datos en varias PVC, especifique una vía de acceso de montaje para cada PVC. Asegúrese de que el nombre que utilice en <code>volumeMount.name</code> coincida con el nombre del volumen especificado en la sección <code>volumes.name</code> del archivo YAML. </td>
     </tr>
     <tr>
     <td><code>&lt;pvc_name&gt;</code></td>
     <td>El nombre de la PVC en la que desea restaurar los datos. Para restaurar datos en varias PVC, añada una entrada <code>volumes</code> para cada PVC. Asegúrese de que el nombre que utilice en <code>volumes.name</code> coincida con el nombre especificado en la sección <code>volumeMount.name</code> del archivo YAML.</td>
     </tr> 
     </tbody>
     </table>

3. Cree el pod de restauración y empiece a restaurar sus datos.

    ```
    kubectl apply -f restorepod.yaml
    ```
    {: pre}

4. Compruebe que el pod se está ejecutando.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME              READY     STATUS             RESTARTS   AGE
    restorepod        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    El pod ejecuta el mandato restaurar y se detiene. El mensaje `CrashLoopBackOff` significa que Kubernetes intenta reiniciar el pod.
5. Compruebe que los datos se han restaurado correctamente.

    ```
    kubectl logs restorepod
    ```
    {: pre}

6. Elimine el pod para evitar que el pod utilice más recursos.

    ```
    kubectl delete -f restorepod.yaml
    ```
    {: pre}

Ha restaurado satisfactoriamente la copia de seguridad. Ahora puede montar el PVC que enlaza el PV con cualquier otro pod del clúster para acceder a los archivos restaurados. Si los datos del contenedor a los que se ha realizado copia de seguridad incluían un usuario no root, deberá añadir permisos no root al nuevo contenedor. Para obtener más información, consulte [Adición de usuario no root a volúmenes](/docs/containers?topic=containers-cs_troubleshoot_storage#cs_storage_nonroot).


## Referencia de variables de entorno
{: #backup_restore_env_reference}

Revise la lista completa de campos que se pueden pasar como variables de entorno o que se pueden editar en el archivo `config.conf` en un pod en ejecución. Cualquier valor que se pase como variable de entorno prevalece sobre el valor del archivo `config.conf`. Para revisar las variables de entorno para un pod, inicie una sesión en el pod con el mandato `kubectl exec` y ejecute `env`.

|Clave|Opciones del valor|
|---|-------------|
|ACCESS_KEY_ID|El valor **access_key_id** que forma parte de las credenciales de HMAC en {{site.data.keyword.cos_full_notm}}.|
|SECRET_ACCESS_KEY|El valor **secret_access_key** que forma parte de las credenciales de HMAC en {{site.data.keyword.cos_full_notm}}.|
|ENDPOINT|El nombre de host para acceder a los datos de grupo de {{site.data.keyword.cos_full_notm}}.|
|BUCKET|El nombre del grupo de {{site.data.keyword.cos_full_notm}} en el que están almacenados los datos de los que se ha hecho copia de seguridad.|
{: caption="Tabla 4.Variables de {{site.data.keyword.cos_full_notm}}" caption-side="top"}

|Clave|Opciones del valor|
|---|-------------|
|BACKUP_DIRECTORY|`/backup`. Predeterminado. Vía de acceso de archivo absoluta del directorio en el que está montado el volumen. La copia de seguridad de los datos se realiza desde este directorio. No seleccione el directorio `backup_restore`, ya que dicho directorio contiene archivos correspondientes a los procesos de copia de seguridad y restauración.|
|BACKUP_NAME|`volume_backup`. Predeterminado. Seleccione un nombre de copia de seguridad.|
|BACKUP_TYPE|`full`. Predeterminado. Todos los archivos se copiarán a la vez.<br/> `incremental`. Solo se realiza copia de seguridad de los archivos nuevos o modificados. Si selecciona `incremental`, debe seleccionar valores para **SCHEDULING_INFO** y **SCHEDULING_TYPE**.|
|SCHEDULE_TYPE|`none`. Predeterminado. Crear una copia de seguridad puntual.<br/> **Nota:** si elige crear una copia de seguridad puntual, el pod se elimina del clúster después de que finalice la copia de seguridad. <br/> `periodic`. Cambie el valor por periodic para crear copias de seguridad planificadas.|
|SCHEDULE_INFO|`hourly`. Cree una copia de seguridad por hora. <br/>`daily`. Predeterminado. Crear una copia de seguridad diaria.<br/>`weekly`. Cree una copia de seguridad semanal. Debe incluir esta variable si planifica una actualización periódica.|
|EXCLUDE_DIRECTORIES|`none`. Predeterminado. Incluya la vía de acceso de archivos absoluta de los directorios que desee excluir de la copia de seguridad. Separe los directorios con una coma.|
{: caption="Tabla 5. Variables de copia de seguridad" caption-side="top"}

|Clave|Opciones del valor|
|---|-------------|
|BACKUP_NAME|`volume_backup`. Predeterminado. Incluya el nombre de la copia de seguridad que se esté restaurando a partir de {{site.data.keyword.cos_full_notm}}.|
|RESTORE_DIRECTORY|`/backup`. Predeterminado. Directorio absoluto en el que se monta el volumen. Los datos se restauran en este directorio. No seleccione el directorio `backup_restore`, ya que dicho directorio contiene archivos correspondientes a los procesos de copia de seguridad y restauración.|
{: caption="Tabla 6. Variables de restauración" caption-side="top"}

