---

copyright:
  years: 2017
lastupdated: "2017-10-30"

  ---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Iniciación a la imagen ibm-backup-restore 
{: #ibmbackup_restore_starter}

La imagen **ibm-backup-restore** contiene los paquetes preinstalados necesarios para realizar copia de seguridad y restaurar los volúmenes de contenedor de {{site.data.keyword.containerlong}}.
{:shortdesc}

## Modo de funcionamiento 
{: #how_it_works}

Con la imagen **ibm-backup-restore**, puede crear una copia de seguridad puntual o programada de cualquier volumen de contenedor. La copia de seguridad está almacenada en una instancia de {{site.data.keyword.objectstoragefull}}. Puede pasar las credenciales de {{site.data.keyword.objectstorageshort}} a su contenedor **ibm-backup-restore** como variables de entorno o editando el archivo `config.conf` en el contenedor en ejecución. También puede restaurar los datos guardados de la instancia de {{site.data.keyword.objectstorageshort}} en un volumen. Como la imagen contiene scripts para ejecutar copias de seguridad y restauraciones, el usuario debe emitir un mandato para iniciar el script adecuado en un contenedor en ejecución.

## Qué incluye 
{: #whats_included}

Cada imagen **ibm-backup-restore** contiene los siguientes paquetes de software:

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   paquetes python, gnupg y wget

## Iniciación 
{: #how_to_get_started}

Lleve a cabo los siguientes pasos para realizar las operaciones de copia de seguridad y restauración:
1.  [Creación de una instancia de servicio de {{site.data.keyword.objectstorageshort}}](#object_storage)
2.  [Ejecución de una copia de seguridad planificada](#scheduled_backup)
3.  [Ejecución del script de restauración](#restore_script_cli)
4.  [Cifrado de las copias de seguridad](#encrypting_backups)
5.  [Referencia de variables de entorno](#reference_backup_restore)

## Creación de una instancia de servicio de {{site.data.keyword.objectstorageshort}} 
{: #object_storage}

Cree una instancia de {{site.data.keyword.objectstorageshort}} que sirva como repositorio para la copia de seguridad de su volumen.

1.  Suministre su instancia de {{site.data.keyword.objectstorageshort}} desde la sección **Servicios** del catálogo de {{site.data.keyword.Bluemix_notm}}.
2.  Seleccione la instancia de {{site.data.keyword.objectstorageshort}}.
3.  Pulse el separador **Credenciales de servicio**.
4.  Pulse **Nueva credencial**.
5.  Complete el campo de nombre pero deje los otros campos en blanco. Pulse **Añadir**.
6.  Sus nuevas credenciales aparecerán ahora en la tabla **Credenciales de servicio**. Pulse **Ver credenciales**.
7.  Tome nota de **projectid**, **region**,
**userId** y **password**. Estas credenciales permiten al contenedor **ibm-backup-restore** acceder a esta instancia de {{site.data.keyword.objectstorageshort}}.

Revise la [documentación de {{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) para obtener más información sobre cómo configurar la instancia y revise las [opciones del plan](../../ObjectStorage/objectstorage_faq.html#account-payment) para obtener información sobre los precios de {{site.data.keyword.objectstorageshort}}.

## Ejecución de una copia de seguridad planificada 
{: #scheduled_backup}

Cree un contenedor a partir de la imagen **ibm-backup-restore**, e inicie una
copia de seguridad planificada regularmente.

1.  Inicie la sesión en la CLI de {{site.data.keyword.containerlong_notm}}.

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

2.  Cree un archivo de variable de entorno en un directorio local.

    ```
    touch <nombre_archivo-entorno_copia_seguridad>
 ```
    {: pre}

3.  Edite el archivo de variable de entorno y añada los campos siguientes. Para las variables de entorno en blanco, especifique los valores a partir de las credenciales de {{site.data.keyword.objectstorageshort}} que ha anotado anteriormente. No incluya las comillas que se utilizan en las
credenciales.

    ```
    BACKUP_NAME=volume_name
    BACKUP_DIRECTORY=/backup
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    SCHEDULE_TYPE=periodic
    SCHEDULE_INFO=daily
    BACKUP_TYPE=incremental
 ```
    {: codeblock}

    Estos valores crean una copia de seguridad incremental diaria con el nombre predeterminado _volume_backup_. Para crear una copia de seguridad con valores diferentes, revise una lista completa de [opciones de variables de entorno](#reference_backup_restore).

4.  Ejecute un contenedor a partir de la imagen **ibm-backup-restore** con un volumen montado
para realizar copia de seguridad. Incluya el mandato para iniciar el script de copia de seguridad.

    -   Asegúrese de que se encuentre en el mismo directorio local que
<em>&lt;backup_env-file&gt;</em>.
    -   El directorio en el que está montado el volumen debe coincidir con el BACKUP_DIRECTORY del archivo de variable de entorno.
    -   Sustituya el nombre de volumen por el nombre del volumen del que se ha realizado copia de seguridad.
    
    ```
    bx ic run --name <nombre_contenedor> --volume <nombre_volumen>:/backup --env-file ./<nombre_archivo-entorno_copia_seguridad> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
 ```
    {: pre}

    Si el contenedor no se empieza a ejecutar, ejecute `bx ic logs <container_name>` para revisar los registros en busca de mensajes de error.

5.  Revise la copia de seguridad en {{site.data.keyword.objectstorageshort}} en la GUI de {{site.data.keyword.Bluemix_notm}}.
    1.  Pulse la instancia de {{site.data.keyword.objectstorageshort}} que haya creado para la copia de seguridad.
    2.  Pulse el contenedor de {{site.data.keyword.objectstorageshort}}. En este ejemplo, el nombre de contenedor es volume_backup.
    3.  Revise los archivos comprimidos.![El contenedor de {{site.data.keyword.objectstorageshort}} de la GUI de {{site.data.keyword.Bluemix_notm}} muestra los archivos a los que se ha realizado copia de seguridad.](images/volume_backup_screenshot.png) Puede descargar el archivo `vol1.difftar.gz`, extraer el archivo y verificar los datos de copia de seguridad. Tenga en cuenta que si ha suprimido o modificado algún archivo de {{site.data.keyword.objectstorageshort}}, tales archivos no podrán
recuperarse.

Su copia de seguridad está disponible. Si ha configurado *<nombre_contenedor>* para crear una copia de seguridad completa puntual, deberá ejecutar el script de copia de seguridad cada vez que desee crear una nueva copia de seguridad. Si ha configurado el contenedor para ejecutar una copia de seguridad incremental periódicamente, la copia de seguridad se ejecutará según lo previsto.

## Ejecución del script de restauración 
{: #restore_script_cli}

Restaure la copia de seguridad de {{site.data.keyword.objectstorageshort}} en un volumen de contenedor nuevo o existente.

1.  Cree un archivo de variable de entorno para el contenedor de restauración en un directorio local.

    ```
    touch <nombre_archivo-entorno_restauración>
 ```
    {: pre}

2.  Edite el archivo de variable de entorno y añada los campos siguientes. Para las variables de entorno en blanco, especifique los valores a partir de las credenciales de {{site.data.keyword.objectstorageshort}} que ha anotado anteriormente. El BACKUP_NAME debe coincidir con el nombre de la copia de seguridad de {{site.data.keyword.objectstorageshort}} que se esté
restaurando.

    ```
    BACKUP_NAME=<nombre_volumen>
    RESTORE_DIRECTORY=/restore
    USERID=
    REGION=
    PROJECTID=
    PASSWORD=
 ```
    {: codeblock}

3.  Cree un volumen en el que restaurar los archivos copiados.

    ```
    bx ic volume create <nombre_volumen>
 ```
    {: pre}

4.  Ejecute un contenedor a partir de la imagen **ibm-backup-restore**. Incluya el mandato
para iniciar el script de restauración.

    -   Asegúrese de que se encuentre en el mismo directorio local que
<em>&lt;restore_env-file&gt;</em>.
    -   El directorio en el que está montado el volumen debe coincidir con el BACKUP_DIRECTORY del archivo de variable de entorno.
    
    ```
    bx ic run --name <nombre_contenedor> --volume <nombre_volumen>:/restore --env-file ./<nombre_archivo-entorno_restauración> registry.ng.buemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vrestore"
 ```
    {: pre}

5.  Compruebe los registros del contenedor para confirmar que el proceso de restauración se haya completado.

    ```
    bx ic logs <nombre_contenedor>
 ```
    {: pre}

    ```
    [2016-10-26 18:01:51,031] [utilities : 152] [INFO] *****************Start logging to ./Restore.log with rollover at 102400 bytes**************
    [2016-10-26 18:01:51,031] [restore : 28] [INFO] Starting the restore process.
 [2016-10-26 18:01:51,032] [configureOS : 22] [INFO] Configuring duplicity with IBM Bluemix ObjectStorage.
 [2016-10-26 18:01:51,032] [configureOS : 13] [INFO] Configuring swift client.
 [2016-10-26 18:01:51,032] [restore : 40] [INFO] Configuration is completed.
 [2016-10-26 18:01:54,022] [restore : 70] [INFO] Restoring the backup that is named 'volume_backup' is completed. Synchronizing remote metadata to local cache...
    Copying duplicity-full-signatures.20161026T173856Z.sigtar.gz to local cache.
 Copying duplicity-full.20161026T173856Z.manifest to local cache.
 Last full backup date: Wed Oct 26 17:38:56 2016
 ```
    {: screen}

    Cuando haya finalizado el proceso de restauración, el contenedor finalizará.

6.  Opcional: Elimine el contenedor para que no consuma ningún recurso.

    ```
    bx ic rm -f <nombre_contenedor>
 ```
    {: pre}

7. Opcional: Para confirmar que sus archivos copiados se encuentren en el volumen, inicie sesión en el contenedor,
navegue hasta la vía de acceso de montaje y liste los archivos.

    ```
    $ bx ic exec -it <nombre_contenedor> bash
    root@instance:/backup_restore# cd ..
 root@instance/# cd restore
    root@instance:/restore# ls
    duplicity-I3_pJv-tempdir  new_file
    root@instance:/restore#
 ```
    {: screen}

    tempdir es un producto del proceso de copia de seguridad y se puede suprimir manualmente.


Ha restaurado satisfactoriamente la copia de seguridad al volumen *nombre_volumen*. Ahora puede montar cualquier nuevo contenedor en el volumen para darle acceso a los archivos restaurados. Si los datos del contenedor a los que se ha realizado copia de seguridad incluían un usuario no root, deberá añadir permisos no root al nuevo contenedor. Consulte [Adición de acceso de usuario no root a volúmenes](../../../containers/container_volumes_ov.html#container_volumes_write) para obtener más información.

## Cifrado de las copias de seguridad 
{: #encrypting_backups}

Cifre los datos de la instancia de {{site.data.keyword.objectstorageshort}}.

1.  Download <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Icono de enlace externo"></a> para crear una clave de cifrado.
2.  Cree una clave de cifrado en su unidad de cifrado local. Puede aceptar los valores predeterminados pulsando ENTER.

    **Atención:** Anote la frase de contraseña que haya creado. Si pierde la frase de contraseña, no podrá descifrar ninguna información cifrada con la clave.

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

4.  Exporte la clave de cifrado con el valor de la clave `sub`. Dé nombre al archivo encryption.asc.

    ```
    gpg --export-secret-keys -a <SUB_KEY> > encryption.asc
 ```
    {: pre}

    En este ejemplo, la subclave tiene el valor *YYYYYYYY*

    ```
    gpg --export-secret-keys -a YYYYYYYY > encryption.asc
 ```
    {: pre}

5.  Cree un archivo de variable de entorno para el contenedor de copia de seguridad de cifrado en un directorio local.

    ```
    touch <nombre_archivo-entorno_cifrado>
 ```
    {: pre}

6.  Edite el archivo de variable de entorno y añada los campos siguientes. Para las variables de entorno en blanco, especifique los valores a partir de las credenciales de {{site.data.keyword.objectstorageshort}} que ha anotado anteriormente. No incluya las comillas que se utilizan en las
credenciales.Para
*ENCRYPTION_PASSPHRASE*, incluya una frase de contraseña para proteger mediante contraseña la copia de seguridad. Esta frase de contraseña es distinta de la que especificó al crear la clave de cifrado. Debe incluir esta frase de contraseña al realizar copia de seguridad de los datos y restaurarlos.

    ```
    BACKUP_NAME=<nombre_volumen>
    BACKUP_DIRECTORY=/backup
    # PROJECTID, REGION, USERID, PASSWORD vienen de las credenciales de Object Storage.
 PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    SCHEDULE_TYPE=periodic
    SCHEDULE_INFO=daily
    BACKUP_TYPE=incremental
    ENCRYPTION_REQUIRED=yes
    # Incluya esta frase de contraseña al realizar una copia de seguridad y restaurar datos cifrados.
 ENCRYPTION_PASSPHRASE=
 ```
    {: codeblock}

    Estos valores crean una copia de seguridad incremental diaria cifrada con el nombre *<nombre_volumen>*. Para crear una copia de seguridad con valores diferentes, revise una lista completa de [opciones de variables de entorno](#reference_backup_restore).

7.  Inicie la sesión en la CLI de {{site.data.keyword.containerlong_notm}}.

    ```
    bx login
    ```
    {: pre}

    ```
    bx ic init
    ```
    {: pre}

8.  Ejecute un contenedor a partir de la imagen **ibm-backup-restore** con un volumen montado
para realizar copia de seguridad. Incluya el mandato para iniciar el script de copia de seguridad.

    -   Asegúrese de que se encuentre en el mismo directorio local que <em>&lt;nombre_archivo-entorno_cifrado&gt;</em>.
    -   El directorio en el que está montado el volumen debe coincidir con el BACKUP_DIRECTORY del archivo de variable de entorno.
    -   Sustituya el nombre de volumen por el nombre del volumen del que se ha realizado copia de seguridad.
   
    ```
    bx ic run --name <nombre_contenedor> --volume <nombre_volumen>:/backup --env-file ./<nombre_archivo-entorno_cifrado> registry.ng.bluemix.net/ibm-backup-restore
 ```
    {: pre}

    Si el contenedor no se empieza a ejecutar, ejecute `bx ic logs <container_name>` para revisar los registros en busca de mensajes de error.

9.  Copie la clave de cifrado en el directorio /backup_restore del contenedor que se ha creado a partir de la imagen **ibm-backup-restore**.

    ```
    bx ic cp ./encryption.asc <nombre_contenedor>:/backup_restore
 ```
    {: pre}

    Guarde una copia de la clave de cifrado localmente para descifrar sus datos.

10. Inicie la sesión en el contenedor.

    ```
    bx ic exec -it <nombre_contenedor> bash
 ```
    {: pre}

11. Confirme que *encryption.asc* se haya copiado en la carpeta backup_restore.

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

13. Para confirmar que la copia de seguridad se ha cifrado, revise los archivos de su instancia de {{site.data.keyword.objectstorageshort}}. Los archivos tendrán ahora añadido `.gpg` al final del nombre de archivo.![La GUI de {{site.data.keyword.objectstorageshort}} muestra todos los archivos a los que se ha hecho copia de seguridad añadidos con .gpg, lo que muestra que están cifrados.](images/volume_backup_encrypt_screenshot.png)

Su copia de seguridad está cifrada. Para restaurar los archivos, siga los pasos de [Ejecución del script de restauración](#restore_script_cli) e incluya el archivo encryption.asc en el directorio backup_restore del contenedor que esté ejecutando el proceso de restauración. Si la copia de seguridad está cifrada, deberá proporcionar las variables de entorno ENCRYPTION_REQUIRED y ENCRYPTION_PASSPHRASE al crear el contenedor de restauración.

## Referencia de variables de entorno 
{: #reference_backup_restore}

Revise la lista completa de campos que se pueden pasar como variables de entorno o se pueden editar en el archivo `config.conf` de un contenedor de ejecución. Cualquier valor que se pase como variable de entorno reemplaza el valor en el archivo `config.conf`. Para revisar las variables de entorno de un contenedor, inicie sesión en el contenedor usando `exec` y ejecute `env`.

|Clave|Opciones de valor|
|---|-------------|
|PROJECTID|ProjectID de {{site.data.keyword.objectstorageshort}}|
|REGION|Región de {{site.data.keyword.objectstorageshort}}|
|USERID|ID de usuario de {{site.data.keyword.objectstorageshort}}|
|PASSWORD|Contraseña de {{site.data.keyword.objectstorageshort}}|
{: caption="Tabla 1. Variables de {{site.data.keyword.objectstorageshort}}" caption-side="top"}

Recupere estas variables de las credenciales de {{site.data.keyword.objectstorageshort}}. Incluya estas variables de entorno con cada contenedor que ejecute una operación de copia de seguridad o restauración.

|Clave|Opciones de valor|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: Valor predeterminado. Vía de acceso de archivo absoluta del directorio en el que está montado el volumen. La copia de seguridad de los datos se realiza desde este directorio. No seleccione el directorio backup_restore ya que dicho directorio contiene archivos para los procesos de copia de seguridad y restauración.|
|BACKUP_NAME|*volume_backup*: Valor predeterminado. Elija un nombre de copia de seguridad.|
|BACKUP_TYPE|*full*: Valor predeterminado. Todos los archivos se copiarán cada vez.<br/> *incremental*: Sólo se realiza copia de seguridad de los archivos nuevos o modificados. Si selecciona *incremental*, debe seleccionar valores para SCHEDULING_INFO y SCHEDULING_TYPE.|
|SCHEDULE_TYPE|*none*: Valor predeterminado. Cree una copia de seguridad puntual.<br/> *periodic*: Cambie el valor a periodic para crear copias de seguridad programadas.|
|SCHEDULE_INFO|*hourly*: Cree una copia de seguridad por hora.<br/>*daily*: Valor predeterminado. Cree una copia de seguridad diaria.<br/>*weekly*: Cree una copia de seguridad semanal. Debe incluir esta variable si va a programar una actualización periodic.|
|EXCLUDE_DIRECTORIES|*none*: Valor predeterminado. Incluya la vía de acceso de archivos absoluta de los directorios que desee excluir de la copia de seguridad. Separe los directorios con una coma.|
{: caption="Tabla 2. Variables de copia de seguridad" caption-side="top"}

|Clave|Opciones de valor|
|---|-------------|
|BACKUP_NAME|*volume_backup*: Valor predeterminado. Incluya el nombre de la copia de seguridad que se esté restaurando a partir de {{site.data.keyword.objectstorageshort}}.|
|RESTORE_DIRECTORY|*/backup*: Valor predeterminado. Directorio absoluto en el que se monta el volumen. Los datos se restauran en este directorio. No seleccione el directorio backup_restore ya que dicho directorio contiene archivos para los procesos de copia de seguridad y restauración.|
{: caption="Tabla 3. Variables de restauración" caption-side="top"}

|Clave|Opciones de valor|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: Valor predeterminado. Incluya esta variable de entorno si cambia el nombre de archivo de la clave de cifrado o la clave se encuentra en un directorio distinto a backup_restore.|
|ENCRYPTION_REQUIRED|*no*: Valor predeterminado.<br/> *yes*: Si no va a cifrar su copia de seguridad, no incluya ninguna variable de cifrado. Si va a cifrar la copia de seguridad, incluya esta clave con el valor yes.|
|ENCRYPTION_PASSPHRASE|Incluya una frase de contraseña para proteger la copia de seguridad. Esta frase de contraseña es distinta de la que especificó al crear la clave de cifrado. Debe incluir esta frase de contraseña al realizar copia de seguridad de los datos y restaurarlos.|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: Valor predeterminado.<br/> *yes*: Incluya esta variable de entorno con yes si ha generado la clave de cifrado directamente en el contenedor. La mayoría de los usuarios generan la clave en su equipo local y copian la clave en el contenedor, así que pueden dejar el valor predeterminado como no.|
{: caption="Tabla 4. Variables de cifrado" caption-side="top"}

