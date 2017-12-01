---

copyright:
  years: 2017
lastupdated: "2017-10-26"

  ---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Iniciación a la imagen **ibmnode** {: #getting_started_node}

La imagen de {{site.data.keyword.IBM}} Node pública (**ibmnode**) se suministra para {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

**Nota:** La imagen de {{site.data.keyword.IBM_notm}} Node (**ibmnode**) no incluye una app Node.js de ejemplo. Al desplegar un contenedor desde esta imagen, el contenedor se cierra inmediatamente después de crearse ya que no existe un proceso de larga ejecución. Para evitarlo, utilice la imagen de {{site.data.keyword.IBM_notm}} Node (**ibmnode**) como padre y cree su propia imagen en la que puede añadir el código de su app Node.js. Para obtener más información, consulte [Creación de una imagen a partir de esta imagen proporcionada](#creating_image).

# Modo de funcionamiento{: #how_it_works}

Utilice la imagen de {{site.data.keyword.IBM_notm}} Node (**ibmnode**) como padre para crear su propia imagen con el código de su app Node.js.
{:shortdesc}

**Nota:** Antes de empezar, revise los términos de uso de las imágenes certificadas de IBM en la sección Uso de la [biblioteca de Docker websphere-liberty ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://github.com/docker-library/docs/tree/master/websphere-liberty){: new_window}.

# Qué incluye{: #included}

* Ubuntu 14.04
* Python, Git y paquetes apt-get build-essentials
* SDK de {{site.data.keyword.IBM_notm}} Node

    **Nota:** La imagen ibmnode está disponible con distintas etiquetas, por lo que puede ejecutar distintas versiones del SDK de IBM Node.

    <table>
    <caption> Tabla 1. Etiquetas para distintas versiones SDK </caption>
      <tr>
        <th> Etiqueta</th>
        <th> Descripción</th>
      </tr>
      <tr>
        <td> ibmnode:latest </td>
        <td> La versión más reciente de IBM Node, que equivale a utilizar ibmnode:v4 </td>
      </tr>
      <tr>
        <td> ibmnode:v4 </td>
        <td> IBM Node 4.4.0 </td>
      </tr>
      <tr>
        <td> ibmnode:v1.2 </td>
        <td> IBM Node 1.2.0.8 (0.12.10) </td>
      </tr>
      <tr>
        <td> ibmnode:v1.1 </td>
        <td> IBM Node 1.1.0.18 (0.10.43) </td>
      </tr>
    </table>


# Creación de una imagen a partir de esta imagen proporcionada {: #creating_image}

Puede utilizar la imagen **ibmnode** como padre para crear una imagen hijo que incluya su propio código de app. Utilice el contenedor de apps de ejemplo `Hello World` con **ibmnode** y el módulo Express Node para crear una imagen en el sistema. Añada la imagen a {{site.data.keyword.registrylong_notm}} privado de su organización y cree un contenedor con esta. A continuación, puede utilizar el Dockerfile del ejemplo Hello World para crear otra imagen con su propia app.
{:shortdesc}

Antes de empezar, tenga en cuenta los siguientes pasos.

* Debe instalar localmente las herramientas siguientes:
  * [{{site.data.keyword.registrylong_notm}} Plug-in para Cloud Foundry y la CLI de Docker](/docs/containers/container_cli_cfic_install.html)
  * [Código fuente de Node.js ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://nodejs.org/en/download/){: new_window}
  * [npm ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](https://github.com/npm/npm){: new_window}
* [Debe saber cuál es el espacio de nombres de {{site.data.keyword.registryshort_notm}} privado de su organización.](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__namespace)

    Ejemplo

    registry.DomainName/<var class="keyword varname">espacio_nombres</var>

* [Debe tener una dirección IP.](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__ip_request)

Cree una imagen basada en la imagen **ibmnode** e incluya una app Hello World.
1.  Descargue el archivo [hellonode.zip ![Icono de enlace externo](../../../icons/launch-glyph.svg "Icono de enlace externo")](ftp://public.dhe.ibm.com/cloud/bluemix/containers/hellonode.zip){: new_window} y extráigalo.
1.  Abra el Dockerfile con un editor de texto y, en la instrucción FROM, actualice el URL de registro de la región que desea utilizar.

    <ul>
    <li>EE.UU. sur

        ```
		registry.ng.bluemix.net/ibmnode
		```
		{: codeblock}

    </li>
    <li>Reino Unido

        ```
		registry.eu-gb.bluemix.net/ibmnode
		```
		{: codeblock}

      </li>
    </ul>

1.  En la CLI, vaya al directorio **app**.
1.  En el directorio **app**, ejecute los siguientes mandatos.
    1.  Cree un archivo package.json.

        ```
        npm init
        ```
        {: pre}

        **Consejo:** Pulse devolver para aceptar el valor predeterminado para cada una de las solicitudes.

    2.  Instale Express, una infraestructura para Node.js. package.json se actualiza con la información de versión para Express en la sección dependencies.

        ```
        npm install express -save
        ```
        {: pre}

1.  Retroceda al directorio **hellonode**.
1.  Compile la imagen y envíela a {{site.data.keyword.registryshort_notm}} privado.*

  ```
    bx ic build -t registry.<NombreDominio>/<espacio_nombres>/hellonode .
 ```
            {: pre}

  **Consejo:** Ejecute `bx ic namespace get` para recuperar la información del espacio de nombres.  Establezca _registry.DomainName_ en la misma región que ha utilizado anteriormente en el Dockerfile.

  **Nota:** \*En este mandato, puede sustituir `bx ic` por `docker` al [iniciar sesión en {{site.data.keyword.containershort_notm}}](/docs/containers/container_cli_cfic_install.html#container_cli_login) y establecer las variables de entorno para utilizar mandatos Docker nativos. Puede utilizar mandatos Docker nativos en todos los pasos marcados con un asterisco (*) en este tema.

  1.  Cree un contenedor a partir de la imagen.*

  ```
    bx ic run -p 3000 --name helloworld registry.<NombreDominio>/<espacio_nombres>/hellonode
 ```
            {: pre}

  1.  Enlace una dirección IP pública al contenedor.

  ```
    bx ic ip bind <dirección_IP> helloworld
 ```
            {: pre}

  **Consejo:** Podrá ver las direcciones IP públicas ejecutando `bx ic ip list`. Para solicitar una nueva, ejecute `bx ic ip request`.

  1. En un navegador, pruebe la app abriendo **<var class="varname">dirección_IP</var>:3000**. Se muestra el mensaje Hello World!. 

  Ahora que ha creado la imagen hellonode, puede modificar el Dockerfile para incluir su app y crear otra imagen para dicha app.

  1.  Para la instrucción ADD, añada su propio código de aplicación.
1.  Para la instrucción EXPOSE, actualice la información del puerto.
1.  Para la instrucción CMD, cree un mandato para iniciar la app.


  # Referencia del Dockerfile de **ibmnode** {: #reference_dockerfile}

Este Dockerfile se ha utilizado para crear la imagen ibmnode en el catálogo de {{site.data.keyword.Bluemix_notm}}. Esta información sirve únicamente como referencia.
No se proporcionan los otros archivos incluidos en la versión de compilación de esta imagen.{:shortdesc}

  ```
FROM ubuntu:14.04

  # Actualizar SO base
RUN apt-get update &&\
DEBIAN_FRONTEND=noninteractive apt-get -y install \
git \
build-essential \
python && \
DEBIAN_FRONTEND=noninteractive apt-get upgrade -y &&\
apt-get clean &&\
rm -Rf /var/cache/*

  # Mejorar reglas de contraseñas predeterminadas
RUN sed -i 's/^PASS_MAX_DAYS.*/PASS_MAX_DAYS 90/' /etc/login.defs
RUN sed -i 's/sha512/sha512 minlen=8/' /etc/pam.d/common-password

  # Instalar SDK de Node
ONBUILD COPY ./prereq/node.tar.gz /tmp/prereq/
ONBUILD RUN cd /usr/local &&\
tar --strip-components 1 -xzf /tmp/prereq/node.tar.gz &&\
rm -rf /tmp/prereq

  # Vía de acceso de documentación para URL
LABEL doc.url="/docs/services/RegistryImages/ibmnode/index.html"
```
{: screen}
