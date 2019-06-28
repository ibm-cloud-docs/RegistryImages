---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-04"

keywords: IBM Cloud Container Registry, ibmcloud-secure-perimeter-health, container image, health, Secure Perimeter, scan, public image

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

# Iniciación a la imagen `ibmcloud-secure-perimeter-health`
{: #ibmcloud-secure-perimeter-health}

La imagen `ibmcloud-secure-perimeter-health` contiene una herramienta para explorar vulnerabilidades en un Secure Perimeter en {{site.data.keyword.cloud}}.
{:shortdesc}

Puede acceder a las imágenes que suministra {{site.data.keyword.IBM_notm}} desde la línea de mandatos; consulte las [imágenes públicas de IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Cómo funciona
{: #sph_how-it-works}

Para asegurarse de que su Secure Perimeter esté funcionando correctamente, `ibmcloud-secure-perimeter-health` puede explorar redes públicas o privadas en su cuenta de infraestructura de {{site.data.keyword.cloud_notm}} e informar de las vulnerabilidades. Puede utilizar la imagen **ibmcloud-secure-perimeter-health** de dos formas:

- Utilice `ibmcloud-secure-perimeter-health` como un pod en un clúster de Kubernetes dentro del Secure Perimeter para explorar en busca de exposiciones en redes privadas.
- Utilice `ibmcloud-secure-perimeter-health` como un contenedor autónomo de Docker de su estación de trabajo para explorar en busca de exposiciones en redes públicas.

Para obtener más información sobre Secure Perimeter, consulte estos artículos de blog:

- [Configure un perímetro seguro en {{site.data.keyword.cloud_notm}} ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/).
- [Configure un perímetro seguro automatizado en {{site.data.keyword.cloud_notm}} ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

Después de la exploración, la imagen `ibmcloud-secure-perimeter-health` crea un informe sobre a qué redes se ha llegado desde dentro del Secure Perimeter Segment. Cada informe detalla el nombre de la pasarela de red, VLAN, sus subredes, y cualquier host erróneo. El código siguiente es un informe de ejemplo de un usuario que ha explorado vulnerabilidades de red privada:

```
#-------- Running Secure Perimeter exposure scan 2018-05-24 12:00:00 --------#

RESULTS:

sp-gateway-af6053a9:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   FAIL
			host = 10.73.84.198, ports = [179, 22]
		10.73.63.8/29:     PASS
		10.73.72.128/26:   PASS
sp-gateway-8a9031ab:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   PASS
		10.73.63.8/29:     PASS
```
{: screen}

## Qué incluye
{: #sph_whats_included}

La imagen `ibmcloud-secure-perimeter-health` proporciona los siguientes paquetes de software.
{:shortdesc}

- Alpine Linux
- Tiempo de ejecución de Python
- SoftLayer Python Client
- Explorador de puerto de nmap

## Suministro de un clúster de Kubernetes dentro de un perímetro seguro mediante {{site.data.keyword.containerlong_notm}}
{: #sph_provision_cluster}

1. Suministre el clúster de Kubernetes desde la sección **Contenedores** del catálogo de the {{site.data.keyword.cloud_notm}}.
2. Pulse **Crear**.
3. Seleccione las VLAN públicas y privadas de Secure Perimeter Segment desde las listas de VLAN.
4. Especifique los demás detalles que necesite.
5. Pulse **Crear clúster**.

Para obtener más información sobre cómo obtener acceso al clúster después de su despliegue, consulte
[{{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-getting-started#getting-started).

## Explorar las redes privadas dentro de un Secure Perimeter
{: #sph_private_networks}

Cree un pod del contenedor a partir de la imagen `ibmcloud-secure-perimeter-health`, y configure una exploración rutinaria.

**Antes de empezar**

- Instale las [CLI](/docs/containers?topic=containers-cs_cli_install#cs_cli_install) necesarias.
- [Defina su clúster](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure) como destino de la CLI.

1. Cree un archivo de configuración con el nombre `health-pod.yaml`. Este archivo crea un despliegue altamente disponible del pod de contenedor.

    ```
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: health-pod
      labels:
        app: health-pod
    spec:
      replicas: 1
      selector:
        app: health-pod
      template:
        spec:
          containers:
          - name: health-pod
            image: icr.io/ibm/ibmcloud-secure-perimeter-health:1.0.0
            args:
            - /usr/local/bin/python
            - /run.py
            - --scan
            - private
            - --exclude-vlan-ids
            - <Private Secure Perimeter Segment VLAN ID>
            - --poll-interval
            - 1800
            env:
            - name: SL_USER
              value: <IBM Cloud infrastructure user name>
            - name: SL_APIKEY
              value: <IBM Cloud infrastructure api key>
    ```
    {: codeblock}

2. Cree el despliegue.

    ```
    kubectl apply -f health-pod.yaml
    ```
    {: pre}

3. Confirme que se esté ejecutando el pod.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    health-pod-<random-id>                  1/1       Running   0          1hr
    ```
    {: screen}

## Explorar redes públicas fuera de un Secure Perimeter
{: #sph_public_networks}

Cree un contenedor de Docker desde la imagen `ibmcloud-secure-perimeter-health` y explore redes públicas.

**Antes de empezar**

- Instale Docker.

1. Cree un contenedor de Docker desde su propia estación de trabajo ejecutando el siguiente mandato:

    ```
    docker run -it -e SL_USER='$SL_USER' -e SL_APIKEY='$SL_APIKEY' icr.io/ibm/ibmcloud-secure-perimeter-health:1.0.0 /usr/local/bin/python run.py --scan public --allowed-public-ports 80 443 9000-9999
    ```
    {: pre}

2. Una vez que el contenedor cree un informe, revise la sección [Análisis de resultados de la exploración](#sph_scan_results) para comprender los resultados.

## Análisis de los resultados de la exploración
{: #sph_scan_results}

`ibmcloud-secure-perimeter-health` crea un informe formateado en estado de funcionamiento de un Secure Perimeter:

```
#-------- Running Secure Perimeter exposure scan 2018-05-24 12:00:00 --------#

RESULTS:

sp-gateway-af6053a9:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   FAIL
			host = 10.73.84.198, ports = [179, 22]
		10.73.63.8/29:     PASS
		10.73.72.128/26:   PASS
sp-gateway-8a9031ab:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   PASS
		10.73.63.8/29:     PASS
```
{: screen}

El informe se presenta en el siguiente formato:

```
<gateway name>:
  <vlan name>:
    <subnet>:    PASS
    <subnet>:    FAIL
      host = <ip address>, ports = [<exposed port>, <exposed port>, ...]
```
{: screen}

`ibmcloud-secure-perimeter-health` determina una subred como `PASS` si no se han encontrado hosts en la subred; de lo contrario, devolverá `FAIL` y listará los hosts a los que se ha llegado, junto con los puertos que estaban accesibles.

## Referencia de argumentos del contenedor
{: #sph_reference_container_arg}

|Clave|Descripción|Valor predeterminado
|---|-------------|---|
|`scan`|El tipo de exploración de exposición ("public" o "private") |Ninguno (explorar ambos)
|`exclude-vlan-ids`|Lista de VLAN por ID para evitar la exploración|Ninguno
|`poll-interval`|Establecer el número de segundos hasta la siguiente exploración|0 (ejecutar una vez)
|`allowed-public-ports`|Lista de puertos a incluir en una lista blanca en la exploración|80, 443, 9000-9999
{: caption="Tabla 1. Argumentos del contenedor" caption-side="top"}

## Referencia de variables de entorno
{: #sph_reference_env_var}

|Clave|Descripción|
|---|-------------|
|`SL_USER`|El nombre de usuario de la infraestructura de IBM Cloud|
|`SL_APIKEY`|La clave de API de la infraestructura de IBM Cloud|
{: caption="Tabla 2. Variables de entorno" caption-side="top"}
