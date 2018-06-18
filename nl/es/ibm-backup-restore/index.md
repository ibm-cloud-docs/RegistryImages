---

copyright:
  years: 2017
lastupdated: "2017-11-09"

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

La imagen **ibm-backup-restore** contiene los paquetes preinstalados necesarios para realizar copia de seguridad y restaurar el almacén persistente de {{site.data.keyword.containerlong}}.
{:shortdesc}

## Modo de funcionamiento 
{: #how_it_works}

Con la imagen **ibm-backup-restore**, puede crear una copia de seguridad puntual o programada para cualquier reclamación de volumen permanente (pvc). La copia de seguridad está almacenada en una instancia de {{site.data.keyword.objectstoragefull}}. Puede pasar las credenciales de {{site.data.keyword.objectstorageshort}} a su contenedor **ibm-backup-restore** como variables de entorno o editando el archivo `config.conf` en el contenedor en ejecución. También puede restaurar los datos guardados de la instancia de {{site.data.keyword.objectstorageshort}} en un volumen. Como la imagen contiene scripts para ejecutar copias de seguridad y restauraciones, el usuario debe especificar un mandato para iniciar el script adecuado.

## Qué incluye 
{: #whats_included}

Cada imagen **ibm-backup-restore** contiene los siguientes paquetes de software:

-   Ubuntu 14.04
-   Duplicity 0.7.10
-   paquetes python, gnupg y wget

## Iniciación 
{: #how_to_get_started}

Revise las siguientes tareas para hacer copia de seguridad de datos y restaurarlos:
1.  [Creación de una instancia de servicio de {{site.data.keyword.objectstorageshort}}](#object_storage)
2.  [Ejecución de una copia de seguridad planificada](#scheduled_backup)
3.  [Ejecución del script de restauración](#restore_script_cli)
4.  [Cifrado de las copias de seguridad](#encrypting_backups)
5.  [Referencia de variables de entorno](#reference_backup_restore)

## Creación de una instancia de servicio de {{site.data.keyword.objectstorageshort}} 
{: #object_storage}

Cree una instancia de {{site.data.keyword.objectstorageshort}} que sirva como repositorio para la copia de seguridad de su volumen.

1.  Suministre su instancia de {{site.data.keyword.objectstorageshort}} desde la sección **Almacén** del catálogo de {{site.data.keyword.Bluemix_notm}}.
2.  Pulse {{site.data.keyword.objectstorageshort}}.
3.  Seleccione {{site.data.keyword.objectstorageshort}} OpenStack Swift para {{site.data.keyword.Bluemix_notm}}. A continuación, pulse crear.
3.  Pulse el separador **Credenciales de servicio**.
4.  Pulse **Nueva credencial**.
5.  Complete el campo de nombre pero deje los otros campos en blanco. Pulse **Añadir**.
6.  Sus nuevas credenciales aparecerán ahora en la tabla **Credenciales de servicio**. Pulse **Ver credenciales**.
7.  Tome nota de **projectid**, **region**,
**userId** y **password**. Estas credenciales permiten al contenedor **ibm-backup-restore** acceder a esta instancia de {{site.data.keyword.objectstorageshort}}.

Revise la [documentación de {{site.data.keyword.objectstorageshort}}](../../ObjectStorage/index.html) para obtener más información sobre cómo configurar la instancia y revise las [opciones del plan](../../ObjectStorage/objectstorage_faq.html#account-payment) para obtener información sobre los precios de {{site.data.keyword.objectstorageshort}}.

## Ejecución de una copia de seguridad planificada 
{: #scheduled_backup}

Cree un pod del contenedor a partir de la imagen **ibm-backup-restore**, e inicie una
copia de seguridad planificada regularmente.

Antes de empezar:

-   Instale las [CLI](../../../containers/cs_cli_install.html#cs_cli_install) necesarias.
-   [Defina su CLI](../../../containers/cs_cli_install.html#cs_cli_configure) como destino de su clúster.


1. Cree un archivo de configuración con el nombre _backup-pvc.yaml_. Este archivo de configuración crea una reclamación de volumen permanente (pvc) que puede montar en su pod de copias de seguridad como un volumen.

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: backuppvc
      annotations:
        volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
    ```
    {: codeblock}
    
2. Cree la pvc.

    ```
    kubectl apply -f backup-pvc.yaml 
    ```
    {: pre}

3.  Cree un archivo de configuración con el nombre _backup.yaml_. Para las variables de entorno en blanco, especifique los valores a partir de las credenciales de {{site.data.keyword.objectstorageshort}} que ha anotado anteriormente. Incluya las comillas que se utilizan en las
credenciales.Substituya <em>&lt;pod_name&gt;</em>, <em>&lt;container_name&gt;</em>, y el registro de imagen
<em>&lt;region&gt;</em> con valores adecuados.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: <pod_name>
    spec:
      containers:
        - name: <container_name>
          image: registry.<region>.bluemix.net/ibm-backup-restore
          env:
          - name: USERID
            value: 
          - name: PASSWORD
            value: 
          - name: PROJECTID
            value:
          - name: REGION
            value:
          - name: SCHEDULE_TYPE
            value: periodic
          - name: SCHEDULE_INFO
            value: daily
          - name: BACKUP_TYPE
            value: incremental
          - name: BACKUP_DIRECTORY
            value: /backup
          - name: BACKUP_NAME
            value: mybackup
          command: ["/bin/bash", "./vbackup"]
          volumeMounts:
          - mountPath: /backup
            name: backup-volume
      volumes:
      - name: backup-volume 
        persistentVolumeClaim:
          claimName: backuppvc
    ```
    {: codeblock}
    
    Estos valores crean una copia de seguridad incremental diaria con el nombre predeterminado _mybackup_. Para crear una copia de seguridad con valores diferentes, revise una lista completa de [opciones de variables de entorno](#reference_backup_restore).

4.  Cree un pod que ejecute el script de copia de seguridad.

    ```
    kubectl apply -f backup.yaml
    ```
    {: pre}

5.  Confirme que se esté ejecutando el pod.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

4.  Revise la copia de seguridad en {{site.data.keyword.objectstorageshort}} en la GUI de {{site.data.keyword.Bluemix_notm}}.
    1.  Pulse la instancia de {{site.data.keyword.objectstorageshort}} que haya creado para la copia de seguridad.
    2.  Desde el separador **Administrar** en la tabla **Contenedores**, pulse el contenedor {{site.data.keyword.objectstorageshort}}.
    3.  Revise los archivos comprimidos.![El contenedor de almacenamiento de objetos en la GUI {{site.data.keyword.Bluemix_notm}} muestra los archivos de los que se hecho copia de seguridad.](images/volume_backup_screenshot.png)Puede descargar el archivo vol1.difftar.gz, extraer el archivo y verificar los datos de copia de seguridad. **Importante**: si elimina o modifica cualquier archivo de {{site.data.keyword.objectstorageshort}}, estos archivos no podrán recuperarse.

Su copia de seguridad está disponible. Si ha configurado las copias de seguridad para crear una copia de seguridad completa puntual, deberá ejecutar el script de copia de seguridad cada vez que desee crear una nueva copia de seguridad. Si ha configurado el contenedor para ejecutar una copia de seguridad incremental periódicamente, la copia de seguridad se ejecutará según lo previsto.

## Ejecución del script de restauración 
{: #restore_script_cli}

Restaure la copia de seguridad desde {{site.data.keyword.objectstorageshort}} en un nuevo volumen existente.

Antes de empezar:

-   Instale las [CLI](../../../containers/cs_cli_install.html#cs_cli_install) necesarias.
-   [Defina su CLI](../../../containers/cs_cli_install.html#cs_cli_configure) como destino de su clúster.


1. Cree un archivo de configuración con el nombre _restore-pvc.yaml_. Este archivo de configuración crea una reclamación de volumen permanente (pvc) que puede montar en su pod de restauración como un volumen.

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: restorepvc
      annotations:
        volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
    ```
    {: codeblock}
    
2. Cree la pvc.

    ```
    kubectl apply -f restore-pvc.yaml 
    ```
    {: pre}

3.  Cree un archivo de configuración con el nombre _restore.yaml_. Para las variables de entorno en blanco, especifique los valores a partir de las credenciales de {{site.data.keyword.objectstorageshort}} que ha anotado anteriormente. Incluya las comillas que se utilizan en las credenciales. El BACKUP_NAME debe coincidir con el nombre de la copia de seguridad de {{site.data.keyword.objectstorageshort}} que se esté restaurando.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: <pod_name>
    spec:
      containers:
        - name: <container_name>
          image: registry.<region>.bluemix.net/ibm-backup-restore
          env:
          - name: USERID
            value: 
          - name: PASSWORD
            value: 
          - name: PROJECTID
            value:
          - name: REGION
            value:
          - name: RESTORE_DIRECTORY
            value: /restore
          - name: BACKUP_NAME
            value: mybackup
          command: ["/bin/bash", "./vrestore"]
          volumeMounts:
          - mountPath: /restore
            name: restore-volume
      volumes:
      - name: restore-volume 
        persistentVolumeClaim:
          claimName: restorepvc
    ```
    {: codeblock}

4.  cree un pod que ejecute el script de restauración.
    
    ```
    kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  Confirme que se esté ejecutando el pod.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}

    El pod ejecuta el mandato restaurar y se detiene. El mensaje _CrashLoopBackOff_ significa que Kubernetes intenta reiniciar el pod.

6.  Elimine el pod para evitar que consuma más recursos.

    ```
    kubectl delete -f restore.yaml
    ```
    {: pre}

Ha restaurado satisfactoriamente la copia de seguridad. Ahora puede montar cualquier nuevo pod en el pvc para dar acceso a ese contenedor a los archivos restaurados. Si los datos del contenedor a los que se ha realizado copia de seguridad incluían un usuario no root, deberá añadir permisos no root al nuevo contenedor. Para obtener más información, consulte [Adición de usuario no root a volúmenes](../../../containers/container_volumes_ov.html#container_volumes_write).

## Cifrado de las copias de seguridad 
{: #encrypting_backups}

Cifre los datos de la instancia de {{site.data.keyword.objectstorageshort}}.

1.  Descargue <a href="https://www.gnupg.org/download/index.html" target="_blank">GnuPG <img src="../../../icons/launch-glyph.svg" alt="Icono de enlace externo"></a> para crear una clave de cifrado.
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
    
6. Cree un archivo de configuración con el nombre _backup-pvc.yaml_. Este archivo de configuración crea una reclamación de volumen permanente (pvc) que puede montar en su pod de copias de seguridad como un volumen.

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: backuppvc
      annotations:
        volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
    ```
    {: codeblock}

7. Cree la pvc.

    ```
    kubectl apply -f backup-pvc.yaml 
    ```
    {: pre}

8.  Edite el archivo de configuración de despliegue y añada los campos siguientes. Para las variables de entorno en blanco, especifique los valores a partir de las credenciales de {{site.data.keyword.objectstorageshort}} que ha anotado anteriormente. Incluya las comillas que se utilizan en las credenciales. Para *ENCRYPTION_PASSPHRASE*, incluya una frase de contraseña para proteger mediante contraseña la copia de seguridad. Esta frase de contraseña es distinta de la que especificó al crear la clave de cifrado. Debe incluir esta frase de contraseña al realizar copia de seguridad de los datos y restaurarlos.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: <pod_name>
    spec:
      containers:
        - name: <container_name>
          image: registry.<region>.bluemix.net/ibm-backup-restore
          env:
          - name: USERID
            value:
          - name: PASSWORD
            value:
          - name: PROJECTID
            value:
          - name: REGION
            value:
          - name: SCHEDULE_TYPE
            value: periodic
          - name: SCHEDULE_INFO
            value: daily
          - name: BACKUP_TYPE
            value: incremental
          - name: BACKUP_DIRECTORY
            value: /backup
          - name: BACKUP_NAME
            value: mybackup
          - name: ENCRYPTION_REQUIRED
            value: yes
          - name: ENCRYPTION_PASSPHRASE
           # Include this passphrase when your are backing up and restoring encrypted data.
            value:
          volumeMounts:
          - mountPath: /backup
            name: backup-volume
      volumes:
      - name: backup-volume 
        persistentVolumeClaim:
          claimName: backuppvc
   ```
   
   Estos valores crean una copia de seguridad incremental diaria cifrada con el nombre <em>&lt;nombre_volumen&gt;</em>. Para crear una copia de seguridad con valores diferentes, revise una lista completa de [opciones de variables de entorno](#reference_backup_restore).

9.  Cree un pod que ejecute el script de copia de seguridad.

    ```
    kubectl apply -f backup.yaml 
    ```
    {: pre}

9.  Confirme que se esté ejecutando el pod.

    ```
    kubectl get pods
    ```
    {: pre}
    
    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    <pod_name>                              1/1       Running   0          1hr
    ```
    {: screen}

10.  Copie la clave de cifrado en el directorio /backup_restore del contenedor que se ha creado a partir de la imagen **ibm-backup-restore**.

    ```
    kubectl cp ./encryption.asc <container_name>:/backup_restore
    ```
    {: pre}

    Guarde una copia de la clave de cifrado localmente para descifrar sus datos.

11. Inicie la sesión en el contenedor.

    ```
    kubecl exec -it <container_name> bash
    ```
    {: pre}

12. Confirme que *encryption.asc* se haya copiado a la carpeta backup_restore.

    ```
    root@instance:/backup_restore# ls                                                                                                                                                         
    __init__.py  backup.py  config.conf  configureOS.py  encryption.asc  restore.py  run.py  utilities.py  vbackup  vrestore
    ```
    {: screen}

13. Ejecute el script de copia de seguridad en la carpeta backup_restore.

    ```
    ./vbackup &
    ```
    {: codeblock}

13. Para confirmar que la copia de seguridad se ha cifrado, revise los archivos de su instancia de {{site.data.keyword.objectstorageshort}}. Los archivos tienen `.gpg` añadido al final del nombre del archivo.![La GUI de Object Storage muestra todos los archivos copiados a los que se les
ha añadido .gpg, lo que muestra que están cifrados.](images/volume_backup_encrypt_screenshot.png)

Su copia de seguridad está cifrada. Para restaurar los archivos, siga los pasos de [Ejecución del script de restauración](#restore_script_cli) e incluya el archivo encryption.asc en el directorio backup_restore en el contenedor que se ejecuta en el proceso de restauración. Si la copia de seguridad está cifrada, debe proporcionar ENCRYPTION_REQUIRED y ENCRYPTION_PASSPHRASE desde el archivo de copia de seguridad yaml como variables de entorno al crear el contenedor de restauración.
## Environment variable reference
{: #reference_backup_restore}

Revise la lista completa de campos que pueden pasar como variables de entorno o se pueden editar en el archivo `config.conf` en un contenedor de ejecución. Cualquier valor que se pase como una variable del entorno supera el valor del archivo `config.conf`. Para revisar las variables del entorno para un contenedor, inicie sesión en el contenedor usando `exec` y ejecute `env`.

|Clave|Opciones de valor|
|---|-------------|
|PROJECTID|ProjectID from {{site.data.keyword.objectstorageshort}}|
|REGION|Region from {{site.data.keyword.objectstorageshort}}|
|USERID|Userid from {{site.data.keyword.objectstorageshort}}|
|PASSWORD|Password from {{site.data.keyword.objectstorageshort}}|
{: caption="Tabla 1. Variables de {{site.data.keyword.objectstorageshort}} " caption-side="top"}

Elimine estas variables de las credenciales {{site.data.keyword.objectstorageshort}}. Incluya estas variables de entorno con cada contenedor que ejecute una operación de copia de seguridad o restauración.

|Clave|Opciones de valor|
|---|-------------|
|BACKUP_DIRECTORY|*/backup*: Predeterminado. Vía de acceso de archivo absoluta del directorio en el que está montado el volumen. La copia de seguridad de los datos se realiza desde este directorio. No seleccione el directorio backup_restore ya que dicho directorio contiene archivos para los procesos de copia de seguridad y restauración.||BACKUP_NAME|*volume_backup*: Predeterminado. Seleccione un nombre de copia de seguridad.|
|BACKUP_TYPE|*full*: Predeterminado. Todos los archivos se copiarán cada vez.<br/> *incremental*: Solo se realiza la copia de seguridad de los archivos nuevos o modificados. Si selecciona *incremental*, debe seleccionar los valores para SCHEDULING_INFO y SCHEDULING_TYPE.|
|SCHEDULE_TYPE|*none*: Predeterminado. Cree una copia de seguridad puntual.<br/> **Nota:** Si elige crear una copia de seguridad puntual, el pod se eliminará del clúster una vez que termine la copia de seguridad. <br/>*periodic*: Cambie el valor a periódico para crear copias de seguridad programadas.|
|SCHEDULE_INFO|*hourly*: Cree una copia de seguridad por hora.<br/>*daily*: Predeterminado. Cree una copia de seguridad diaria.<br/>*weekly*: Crear una copia de seguridad semanal. Debe incluir esta variable si va a programar una actualización periódica.|
|EXCLUDE_DIRECTORIES|*none*: Predeterminado. Incluya la vía de acceso de archivos absoluta de los directorios que desee excluir de la copia de seguridad. Separe los directorios con una coma.|
{: caption="Tabla 2. Variables de copia de seguridad" caption-side="top"}

|Clave|Opciones de valor|
|---|-------------|
|BACKUP_NAME|*volume_backup*: Predeterminado. Incluya el nombre de la copia de seguridad que se esté restaurando a partir de {{site.data.keyword.objectstorageshort}}.|RESTORE_DIRECTORY|*/backup*: Predeterminado. Directorio absoluto en el que se monta el volumen. Los datos se restauran en este directorio. No seleccione el directorio backup_restore ya que dicho directorio contiene archivos para los procesos de copia de seguridad y restauración.|
{: caption="Tabla 3. Variables de restauración" caption-side="top"}

|Clave|Opciones de valor|
|---|-------------|
|ENCRYPTION_KEY_FILE|.*/encryption.asc*: Predeterminado. Incluya esta variable de entorno si cambia el nombre de archivo de la clave de cifrado o la clave se encuentra en un directorio distinto a backup_restore.|
|ENCRYPTION_REQUIRED|*no*: Predeterminado.<br/> *yes*: Si no va a cifrar su copia de seguridad, no incluya ninguna variable de cifrado. Si va a cifrar la copia de seguridad, incluya esta clave con el valor yes.|
|ENCRYPTION_PASSPHRASE|Incluya una frase de contraseña para proteger la copia de seguridad. Esta frase de contraseña es distinta de la que especificó al crear la clave de cifrado. Debe incluir esta frase de contraseña al realizar copia de
seguridad de los datos y restaurarlos.|
|IS_KEY_GENERATED_ON_SYSTEM|*no*: Predeterminado.<br/> *yes*: Incluya esta variable de entorno con yes si ha generado la clave de cifrado directamente en el contenedor. La mayoría de los usuarios generan la clave en su equipo local y copian la clave en el contenedor, así que pueden dejar el valor predeterminado como no.|
{: caption="Tabla 4. Variables de copia de seguridad" caption-side="top"}

## Migrando sus datos de volumen desde contenedores únicos y escalables a Kubernetes
{: #migrate_data}

Cree una copia de seguridad puntual para cualquier volumen de contendedor. La copia de seguridad está almacenada en una instancia de {{site.data.keyword.objectstoragefull}}. A continuación puede migrar los datos a una reclamación de volumen permanente en Kubernetes.
{:shortdesc}

### Guía de inicio
{: #how_to_get_started_migrating}

Antes de empezar:

- [Revise la vía de acceso de migración para pasar apps a Kubernetes](../../../containers/cs_classic.html)
- [Instale contenedores individuales y escalables CLI (bx ic)](../../../containers/container_cli_cfic_install.html)
- [Instale {{site.data.keyword.containershort}} CLI (bx cs and kubectl)](../../../containers/cs_cli_install.html#cs_cli_install)
- [Cree un clúster de Kubernetes estándar para migrar sus datos a](../../../containers/cs_clusters.html#clusters_cli)

Complete las siguientes tareas para realizar copias de seguridad y restaurar operaciones:
1.  [Crear una instancia de servicio de {{site.data.keyword.objectstorageshort}}](#object_storage) (como se ha gestionado anteriormente)
2.  [Ejecución de una copia de seguridad puntual](#migrate_backup)
3.  [Ejecución de un script de restauración en Kubernetes](#migrate_restore)

### Ejecución de una copia de seguridad puntual
{: #migrate_backup}

Crear un contenedor único desde la imagen **ibm-backup-restore**, e inicie una copia de seguridad.

1.  Inicie la sesión en la CLI de {{site.data.keyword.containershort}}.

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

3.  Edite el archivo de variable de entorno y añada los campos siguientes. Para las variables de entorno en blanco, especifique los valores a partir de las credenciales de {{site.data.keyword.objectstorageshort}} que ha anotado anteriormente. **No** incluya las comillas que se utilizan en las credenciales.

    ```
    BACKUP_NAME=volume_backup
    BACKUP_DIRECTORY=/backup
    PROJECTID=
    REGION=
    USERID=
    PASSWORD=
    ```
    {: codeblock}

    Estos valores crean una copia de seguridad puntual con el nombre predeterminado _volume_backup_.

4.  Ejecute un contenedor desde la imagen **ibm-backup-restore** con un volumen montado para realizar copia de seguridad. Incluya el mandato para iniciar el script de copia de seguridad.

    -   Asegúrese de que se encuentre en el mismo directorio local que <em>&lt;backup_env-file&gt;</em>.
    -   El directorio en el que está montado el volumen debe coincidir con el  BACKUP_DIRECTORY del archivo de variable de entorno.
    -   Sustituya <em>&lt;volume_name&gt;</em> por el nombre del volumen del que se ha realizado copia de seguridad.
    ```
    bx ic run --name <nombre_contenedor> --volume <nombre_volumen>:/backup --env-file ./<nombre_archivo-entorno_copia_seguridad> registry.ng.bluemix.net/ibm-backup-restore /bin/bash -c "/backup_restore/vbackup"
    ```
    {: pre}

    Cuando se haya ejecutado la copia de seguridad, el contenedor finalizará. Si el contenedor no se empieza a ejecutar, ejecute `bx ic logs <container_name>` para revisar los mensajes de error.

5.  Revise la copia de seguridad en {{site.data.keyword.objectstorageshort}} en la GUI de {{site.data.keyword.Bluemix_notm}}.
    1.  Pulse la instancia de {{site.data.keyword.objectstorageshort}} que haya creado para la copia de seguridad.
    2.  Pulse el contenedor de {{site.data.keyword.objectstorageshort}}. En este ejemplo, el nombre de contenedor es volume_backup.
    3.  Revise los archivos comprimidos.![El contenedor de almacenamiento de objetos de la GUI {{site.data.keyword.Bluemix_notm}} muestra los archivos de los que se han hecho copia de seguridad.](images/volume_backup_screenshot.png)Puede descargar el archivo difftar.gz file, extraer el archivo y comprobar los datos de copia de seguridad. **Importante**: si elimina o modifica cualquier archivo de {{site.data.keyword.objectstorageshort}}, estos archivos no podrán recuperarse.

### Restauración de sus datos a un clúster de Kubernetes
{: #migrate_restore}

Restaure su copia de seguridad desde {{site.data.keyword.objectstorageshort}} a una reclamación de volumen permanente en un clúster de Kubernetes.

Antes de empezar:

- [Defina el destino de su CLI](../../../containers/cs_cli_install.html#cs_cli_configure) en su clúster.


1. Cree un archivo de configuración con el nombre _restore-pvc.yaml_. Este archivo de configuración crea una reclamación de volumen permanente (pvc) que puede montar en su pod de copias de seguridad como un volumen.

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: restorepvc
      annotations:
        volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 20Gi
    ```
    {: codeblock}

2. Cree la pvc.

    ```
    kubectl apply -f restore-pvc.yaml 
    ```
    {: pre}

3.  Cree un archivo de configuración con el nombre _restore.yaml_. Para las variables de entorno en blanco, especifique los valores a partir de las credenciales de {{site.data.keyword.objectstorageshort}} que ha anotado anteriormente. Incluya las comillas que se utilizan en las credenciales. El BACKUP_NAME debe coincidir con el nombre de la copia de seguridad de {{site.data.keyword.objectstorageshort}} que se esté restaurando.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: <pod_name>
    spec:
      containers:
        - name: <container_name>
          image: registry.<region>.bluemix.net/ibm-backup-restore
          env:
          - name: USERID
            value: 
          - name: PASSWORD
            value: 
          - name: PROJECTID
            value:
          - name: REGION
            value:
          - name: RESTORE_DIRECTORY
            value: /restore
          - name: BACKUP_NAME
            value: volume_backup
          command: ["/bin/bash", "./vrestore"]
          volumeMounts:
          - mountPath: /restore
            name: restore-volume
      volumes:
      - name: restore-volume 
        persistentVolumeClaim:
          claimName: restorepvc
    ```
    {: codeblock}

4.  Cree un pod que ejecute el script de restauración.

    ```
    kubectl apply -f restore.yaml
    ```
    {: pre}
    
5.  Confirme que se está ejecutando el pod.

    ```
    kubectl get pods 
    ```
    {: pre}
    
    ```
    NAME              READY     STATUS             RESTARTS   AGE
    <pod_name>        0/1       CrashLoopBackOff   1          1m
    ```
    {: screen}
    
    El pod ejecuta el mandato restaurar y se detiene. El mensaje de _CrashLoopBackOff_ significa que Kubernetes intenta reiniciar el pod.
6.  Elimine el pod para evitar que consuma más recursos.

    ```
    kubectl delete -f restore.yaml
    ```
    {: pre}

Ha migrado correctamente sus datos al clúster de Kubernetes. Ahora puede montar cualquier nuevo pod en el pvc para darle acceso a los archivos restaurados.

**Nota**: Si los datos del contenedor de los que se ha realizado copia de seguridad incluían un usuario no root, deberá añadir permisos no root al nuevo contenedor. Para obtener más información, consulte [Adición de acceso de usuario no root a volúmenes](../../../containers/container_volumes_ov.html#container_volumes_write).
