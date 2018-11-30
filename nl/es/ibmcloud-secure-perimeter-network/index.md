---

copyright:
  years: 2018
lastupdated: "2018-10-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Iniciación a la imagen `ibmcloud-secure-perimeter-network`
{: #ibmcloud-secure-perimeter-network}

La imagen `ibmcloud-secure-perimeter-network` contiene herramientas para automatizar la configuración de los dispositivos de direccionador virtual Vyatta dentro de un Secure Perimeter.
{:shortdesc}

Puede acceder a las imágenes que suministra {{site.data.keyword.IBM}} desde la línea de mandatos; consulte las [imágenes públicas de IBM](/docs/services/Registry/registry_public_images.html#public_images).
{: tip}

## Modo de funcionamiento
{: #how-it-works}

Con `ibmcloud-secure-perimeter-network`, puede automatizar la configuración del dispositivo de direccionador virtual Vyatta de Secure Perimeter.

Para obtener más información sobre Secure Perimeter, consulte estos artículos de blog:
  * [Configure un Secure Perimeter en IBM Cloud](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/)
  * [Configure un Secure Perimeter automatizado en IBM Cloud](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

Puede utilizar la imagen `ibmcloud-secure-perimeter-network` de dos formas:
-  Utilice `ibmcloud-secure-perimeter-network` como un contenedor de Docker para inicializar la configuración de las reglas de cortafuegos de Secure Perimeter.
-  Utilice `ibmcloud-secure-perimeter-network` como un pod en un clúster de Kubernetes para sondear la cuenta de infraestructura de IBM Cloud en busca de subredes nuevas creadas en las VLAN de Secure Perimeter Segment y para añadirlas a la configuración de cortafuegos de Vyatta.

## Qué incluye
{: #whats_included}

La imagen `ibmcloud-secure-perimeter-network` proporciona los siguientes paquetes de software.
{:shortdesc}

-   Alpine Linux
-   Tiempo de ejecución de Python
-   SoftLayer Python Client
-   Ansible

## Iniciación
{: #how_to_get_started}

Revise las siguientes tareas para aprender a utilizar `ibmcloud-secure-perimeter-network`:

1.  [Suministre un clúster de Kubernetes dentro de un Secure Perimeter utilizando {{site.data.keyword.containerlong}}](#provision_cluster)
2.  [Ejecute la configuración inicial de su Secure Perimeter Vyatta](#initial_setup)
3.  [Configure como un pod de Kubernetes dentro del Secure Perimeter](#setup)
4.  [Referencia de config.json](#reference_config_json)
5.  [Referencia de rules.conf](#reference_rules_conf)

## Suministro de un clúster de Kubernetes dentro de un perímetro seguro mediante {{site.data.keyword.containerlong_notm}}
{: #provision_cluster}

1.  Suministre el clúster de Kubernetes desde la sección **Contenedores** del catálogo de IBM Cloud.
2.  Pulse **Crear**.
3.  Seleccione las VLAN públicas y privadas de Secure Perimeter Segment desde los menús desplegables de VLAN.
4.  Especifique los demás detalles según sea necesario.
5.  Pulse **Crear clúster**.

Revise la documentación de [{{site.data.keyword.containerlong_notm}}](/docs/containers/container_index.html#container_index) sobre cómo obtener acceso al clúster una vez que se haya desplegado.

## Ejecute la configuración inicial de su Secure Perimeter Vyatta
{: #initial_setup}

1. Cree un archivo denominado _config.json_. Este archivo contiene los parámetros básicos que necesita `ibmcloud-secure-perimeter-network` para acceder a Vyatta.

  ```
  {
    "slid": "XXXX",
    "apikey": "XXXX",
    "region": "XXXX",
    "inf_name_private": "dp0bond0",
    "inf_name_public": "dp0bond1",
    "gatewayid": "XXXX",
    "vlans": [
      {
        "type": "XXXX",
        "vlan_num": XXXX,
        "vlanid": XXXX
      },
      ...
    ],
    "vyatta_gateway_vip": "X.X.X.X",
    "vyatta_primary": {
      "private_ip": "X.X.X.X",
      "public_ip": "X.X.X.X"
    },
    "vyatta_secondary": {
      "private_ip": "X.X.X.X",
      "public_ip": "X.X.X.X"
    }
  }
  ```
  {: codeblock}

  Consulte la [tabla de referencia de config.json](#reference_config_json) para obtener detalles sobre cómo rellenar _config.json_. Este archivo también se puede utilizar en el proceso de [configuración de `ibmcloud-secure-perimeter-network` como un pod de Kubernetes](#setup).

2. Ejecute `ibmcloud-secure-perimeter-network` como un contenedor de Docker para comenzar la configuración inicial.

  ```
  docker run registry.bluemix.net/ibm/ibmcloud-secure-perimeter-network:1.0.0 python config-secure-perimeter.py -v /path/to/current/dir:/opt/secure-perimeter
  ```
  {: pre}

  Esta acción creará un archivo _state.json_ en su directorio de trabajo. Este archivo se utiliza en la [configuración de `ibmcloud-secure-perimeter-network` como un pod de Kubernetes](#setup).

## Configure como un pod de Kubernetes dentro del Secure Perimeter
{: #setup}

Para que la imagen `ibmcloud-secure-perimeter-network` gestione subredes en el Secure Perimeter, puede ejecutarla como un proceso de larga duración mediante un pod de Kubernetes. `ibmcloud-secure-perimeter-network` tiene varios archivos de configuración y carpetas que se deben copiar en el pod para configurarlo para Vyatta:

1. Cree un archivo denominado _pvc.yaml_. Este archivo de configuración crea una reclamación de volumen persistente (pvc) que puede montar en su pod como un volumen.

  ```
  apiVersion: v1
  kind: PersistentVolumeClaim
  metadata:
    name: network-pvc
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

3. Cree un archivo denominado _network-pod.yaml_. Este archivo de configuración despliega la imagen `ibmcloud-secure-perimeter-network` como un pod en el clúster de Kubernetes y monta la reclamación de volumen persistente como un volumen.

  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: network-pod
    labels:
      app: network-pod
  spec:
    template:
      spec:
        containers:
        - name: network-pod
          image: registry.bluemix.net/ibm/ibmcloud-secure-perimeter-network:1.0.0
          volumeMounts:
          - name: network-vol
            mountPath: /opt/secure-perimeter
        volumes:
        - name: network-vol
          PersistentVolumeClaim:
            claimName: network-pvc
  ```
  {: codeblock}

4. Cree un archivo denominado _rules.conf_. Este archivo de configuración indica a `ibmcloud-secure-perimeter-network` qué subredes externas y puertos de Internet público se incluirán en la lista blanca en el Secure Perimeter.

  ```
  {
      "external_subnets": [
        "X.X.X.X/X",
        "X.X.X.X/X"
      ],
      "external_ports": [
        "XX",
        "XX"
      ],
      "userips": [
        "X.X.X.X",
        "X.X.X.X"
      ]
  }
  ```
  {: codeblock}

5. Copie los archivos en el pod `ibmcloud-secure-perimeter-network`.

  ```
  kubectl cp keys network-pod:/opt/secure-perimeter/
  kubectl cp state.json network-pod:/opt/secure-perimeter/state.json
  kubectl cp config.json network-pod:/opt/secure-perimeter/config.json
  kubectl cp rules.conf network-pod:/opt/secure-perimeter/rules.conf
  ```
  {: pre}

  El directorio _keys_ contiene las claves SSH necesarias para que `ibmcloud-secure-perimeter-network` acceda a Vyatta. Consulte la [sección requisitos previos](#prerequisites) para obtener más información sobre las claves SSH.

## Referencia de config.json
{: #reference_config_json}

|Clave|Descripción
|---|-------------|---|
|slid|El nombre de usuario de la infraestructura de IBM Cloud
|apikey|La clave de API de la infraestructura de IBM Cloud
|region|La región de IBM Cloud donde se despliega Vyatta
|inf_name_private|El nombre de la interfaz privada de Vyatta
|inf_name_public|El nombre de la interfaz pública de Vyatta
|gatewayid|El ID de pasarela de Vyatta
|vlans|Lista de VLAN de Secure Perimeter Segment que contienen el tipo, el número y el ID de VLAN
|vyatta_gateway_vip|VIP de la pasarela
|vyatta_primary|Objeto que contiene la IP privada y pública del miembro primario de Vyatta
|vyatta_secondary|Objeto que contiene la IP privada y pública del miembro secundario de Vyatta
{: caption="Tabla 1. config.json" caption-side="top"}

## Referencia de rules.conf
{: #reference_rules_conf}

|Clave|Descripción
|---|-------------|---|
|external_subnets|Lista de subredes en Internet pública a las que exponer Secure Perimeter
|external_ports|Lista de puertos a los que exponer Secure Perimeter
|userips|Lista de IP de usuarios a incluir en una lista blanca a Secure Perimeter
{: caption="Tabla 2. rules.conf" caption-side="top"}

## Requisitos previos
{: #prerequisites}

-   Vyatta y VLAN que se han ordenado desde el portal de infraestructura de IBM Cloud y VLAN que se han asociado a Vyatta.
-   El despliegue automatizado de Secure Perimeter precarga Vyatta con claves SSH que `ibmcloud-secure-perimeter-network` utiliza para acceder a la pasarela. Las claves SSH se deben cargar ya sea manualmente o mediante el proceso de instalación de Secure Perimeter. Consulte el artículo sobre cómo [configurar un perímetro seguro automático en IBM Cloud ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/) para obtener más información.
