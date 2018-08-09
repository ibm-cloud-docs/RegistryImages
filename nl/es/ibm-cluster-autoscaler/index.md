---

copyright:
  years: 2018
lastupdated: "2018-07-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Iniciación a la imagen iccs-cluster-autoscaler (alpha)
{: #ibm-cluster-autoscaler}

La imagen iccs-cluster-autoscaler contiene los paquetes preinstalados que se necesitan para escalar automáticamente los nodos trabajadores dentro del clúster de {{site.data.keyword.containerlong}}. La imagen iccs-cluster-autoscaler se basa en el [proyecto Cluster-Autoscaler de Kubernetes](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler).
{:shortdesc}

La versión alpha del programa de escalado automático da soporte a la disponibilidad en una sola zona y no está disponible para los nodos trabajadores de los servidores físicos nativos.
{: tip}

## Modo de funcionamiento
{: #how-it-works}

Con la imagen iccs-cluster-autoscaler, puede escalar los nodos trabajadores dentro del clúster de Kubernetes hacia arriba o hacia abajo, a través de cualquier grupo de escalado automático cuando trabaje en {{site.data.keyword.containerlong}}. El programa de escalado automático se ejecuta como un despliegue en el espacio de nombres `kube-system` del clúster y es un plugin en el programa de escalado automático del clúster de Kubernetes.
{: shortdesc}

iccs-cluster-autoscaler supervisa los pods del clúster examinando el servidor de la API de Kubernetes y puede escalar automáticamente el tamaño de su clúster hacia arriba o hacia abajo.

<dl>
  <dt>Escalado hacia arriba</dt>
    <dd>Periódicamente, iccs-cluster-autoscaler comprueba si hay pods que no se pueden planificar. Un pod no se puede planificar cuando el planificador de Kubernetes no puede encontrar un nodo en el que ejecutar el pod. Cuando el programa de escalado automático encuentra pods que están etiquetados como no planificables, añade un nodo trabajador al clúster.</dd>
  <dt>Escalar hacia abajo</dt>
    <dd>De forma predeterminada, iccs-cluster-autoscaler comprueba si hay nodos innecesarios cada 10 segundos. Si un nodo no se necesita durante más de 10 minutos, se suprime.</br> Se considera que un nodo es innecesario cuando:</br>
    <ul><li>La suma de solicitudes de CPU y de memoria de todos los pods que se ejecutan en este nodo es menos del 50% de la cantidad que tiene asignada.</li>
    <li>Todos los pods que se ejecutan en el nodo se pueden mover a otros nodos. Las pods que se ejecutan en todos los nodos de forma predeterminada, como las pods de ejecución de manifiesto (manifest-run) o los creados por daemonsets, no se incluyen en esta prueba.</li>
    <li>No hay anotación de escalado hacia abajo inhabilitado. </li></ul></dd>
</dl>

Pero, ¿qué significa todo esto?

![Escalado automático de un clúster](/images/autoscaler.png)

Cuando cree el clúster de Kubernetes en IBM Cloud, especifica las preferencias de hardware. El programa de escalado automático crea grupos en función de los tipos de máquina para permitirle asegurarse de que está utilizando la cantidad correcta de recursos. En la imagen puede ver una política de escalado automático de ejemplo. En la configuración, se especifica el tipo de máquina y el número mínimo y máximo de nodos. Si desea que el programa de escalado automático ignore un conjunto de nodos y no escala, no los incluya en la configuración. En la imagen, el clúster se ha escalado hacia arriba para gestionar los cambios en los recursos. El nodo b2c.16x64 no se ha escalado porque no se ha establecido ninguna política en el archivo de configuración.

## Restricciones de uso
{: #usage}
Esta imagen se puede utilizar en {{site.data.keyword.containershort}} con la API de Kubernetes versión 1.7 o posterior.

## Requisitos previos
{: #prerequisites}

Hay algunos requisitos previos que debe completar para poder trabajar con iccs-cluster-autoscaler.

* Debe tener un clúster estándar para utilizar el plugin del programa de escalado automático. Los grupos de escalado automático están formados por nodos trabajadores que son del mismo [tipo de máquina](/docs/containers/cs_cli_reference.html#cs_machine_types). Para comprobar que cumple con los requisitos, ejecute el mandato siguiente y compruebe que el campo **Machine Type** no indica _free_. Compruebe que su **Versión** es la `versión 1.7` o posterior </br>
    ```
    ibmcloud ks workers <cluster-name>
    ```
    {: pre}
    Salida de ejemplo: </br>
    ```
    OK
    ID                                                   Public IP        Private IP     Machine Type   State    Status   Version
    stage-dal06-crffef8b930b214442802bdcaa551b5ec0-w1    169.54.180.171   10.146.32.7    u2c.2x4        normal   Ready    1.8.2_1504
    ```
    {: screen}
* Debe instalar los siguientes CLI y plugins. Para obtener ayuda sobre la instalación de los requisitos y la creación del primer clúster, [consulte esta guía de aprendizaje en la documentación](/docs/containers/cs_tutorials.html#cs_cluster_tutorial).
    <ul><li>CLI de {{site.data.keyword.Bluemix_notm}}</li><li>CLI de Kubectl</li><li>CLI de Docker</li><li>Plugin de {{site.data.keyword.containerlong_notm}}</li><li>Plugin de {{site.data.keyword.registrylong_notm}}</li></ul>
* Para poder escalar nodos trabajadores en su nombre, el programa de escalado automático necesita conocer sus detalles de Identity and Access Management. Las credenciales están disponibles como un secreto en un clúster de pago. Para verificar que el secreto está disponible, ejecute el mandato siguiente. El mandato devuelve un secreto.
    ```
		$kubectl get secrets -n kube-system | grep storage-secret-store
    ```
    {: pre}

## Instalación y configuración del programa de escalado automático
{: #install-configure}

Para instalar el programa de escalado automático, primero debe instalar e inicializar Helm en el clúster. Cuando el programa de escalado automático se ejecute, conserva automáticamente los recursos y escala los clústeres.

1. Inicie una sesión en {{site.data.keyword.containershort_notm}} y seleccione el clúster como destino.
    ```
    ibmcloud ks cluster-config <cluster_name>`.
    ```
    {: pre}

2. [Instale helm e inicialícelo en el clúster](https://docs.helm.sh/using_helm/#quickstart).

3. Utilizando la plantilla proporcionada, cree un archivo de configuración.

   ```
   cp incubator/helm/autoscaler/config.yaml.template config.yaml
   ```
   {: pre}

    Para que los grupos de escalado funcionen, se deben especificar todos los tipos de máquina en el archivo de configuración, incluso si no proporciona un rango mínimo y máximo.
    {: tip}

  <table summary="La primera fila de la tabla abarca ambas columnas. El resto de las filas se debe leer de izquierda a derecha, con el parámetro en la columna uno, la descripción que coincide en la columna dos y el valor predeterminado en la columna tres.">
  <caption>Parámetros configurables y su valor predeterminado</caption>
    <thead>
      <th colspan=3><img src="../images/idea.png" alt="Icono de idea"/>Parámetros configurables y su valor predeterminado </th>
    </thead>
    <tbody>
      <tr>
        <td><code>autoscalingGroups.name</code></td>
        <td> El tipo de máquina que desea escalar. </td>
        <td> No hay valor predeterminado, especificado por el usuario. Para ver su tipo de máquina, ejecute <code>ibmcloud ks workers <cluster-name></code></td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.minSize</code></td>
        <td> El número mínimo de nodos trabajadores a los que se debe escalar el clúster. </td>
        <td> No hay valor predeterminado, especificado por el usuario. </td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.maxSize</code></td>
        <td> El número máximo de nodos trabajadores a los que se debe escalar el clúster. </td>
        <td> No hay valor predeterminado, especificado por el usuario. </td>
      </tr>
      <tr>
        <td><code>api_route</code></td>
        <td> La ruta a la API de {{site.data.keyword.containershort_notm}} correspondiente a la región en la que se está ejecutando el clúster. </td>
        <td> https://containers.bluemix.net/swagger-api/ </td>
      </tr>
      <tr>
        <td><code>resources.limits.cpu</code></td>
        <td> La cantidad de CPU a la que se limita el pod del programa de escalado automático. </td>
        <td> 300m </td>
      </tr>
      <tr>
        <td><code>resources.limits.memory</code></td>
        <td> La cantidad de memoria a la que se limita el pod del programa de escalado automático. </td>
        <td> 300Mi </td>
      </tr>
      <tr>
        <td><code>resources.requests.cpu</code></td>
        <td> La cantidad de CPU con la que desea que comience el pod del programa de escalado automático. </td>
        <td> 100m </td>
      </tr>
      <tr>
        <td><code>resources.requests.memory</code></td>
        <td> La cantidad de memoria con la que desea que comience el pod del programa de escalado automático. </td>
        <td> 100Mi </td>
      </tr>
      <tr>
        <td><code>scale_down_delay</code></td>
        <td> El intervalo de tiempo que debe esperar el programa de escalado para escalar hacia abajo después de emitir una solicitud de escalado hacia arriba. </td>
        <td> 10m </td>
      </tr>
      <tr>
        <td><code>scale_down_unneeded_time</code></td>
        <td> El intervalo de tiempo que un nodo debe estar considerado innecesario para que sea apto para el escalado hacia abajo. </td>
        <td> 10m </td>
      </tr>
      <tr>
        <td><code>skipNodes.withLocalStorage</code></td>
        <td> Si tiene el valor true, los nodos que contienen almacenamiento local se omitirán y no se escalarán hacia abajo. </td>
        <td> true </td>
      </tr>
      <tr>
        <td><code>skipNodes.withSystemPods</code></td>
        <td> Si tiene el valor true, los nodos que contienen pods kube-system se omitirán y no se escalarán hacia abajo. </td>
        <td> true </td>
      </tr>
      <tr>
        <td><code>image.repository</code></td>
        <td> La imagen de Docker de programa de escalado automático del clúster. </td>
        <td> registry.bluemix.net/iccs-cluster-autoscaler </td>
      </tr>
      <tr>
        <td><code>image.tag</code></td>
        <td> La versión de la imagen que desea extraer. </td>
        <td> 12 </td>
      </tr>
      <tr>
        <td><code>image.pullPolicy</code></td>
        <td> Especifica cuándo se debe extraer la imagen de Docker. </td>
        <td> Siempre. </td>
      </tr>
    </tbody>
  </table>
  
  iccs-cluster-autoscaler da soporte a diversos parámetros de configuración. Para obtener información más detallada sobre cómo funciona, consulte [Preguntas más frecuentes sobre el programa de escalado automático de Kubernetes](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md).

4. Instale el diagrama de helm en el clúster en el espacio de nombres `kube-system`.

  ```
  helm install incubator/helm/autoscaler -f config.yaml --namespace kube-system
  ```
  {: pre}

    La salida debe ser parecida a la siguiente:
  ```
  NAMESPACE: kube-system
  STATUS: DEPLOYED

  RESOURCES:
  ==> v1/Service
  NAME                CLUSTER-IP    EXTERNAL-IP  PORT(S)   AGE
  cluster-autoscaler  10.10.10.189  <none>       8085/TCP  5s

  ==> v1beta1/Deployment
  NAME                DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
  cluster-autoscaler  1        1        1           0          3s
  ```
  {: screen}

## Verificación de la instalación

1. Compruebe que el clúster que contiene el pod del programa de escalado automático se está ejecutando.

  ```
  kubectl get pods --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    Salida de ejemplo:
  ```
    cluster-autoscaler-4289984493-zldfk        1/1       Running   0          3m
  ```
  {: screen}

2. Compruebe que el programa de escalado automático del clúster se está ejecutando.

  ```
  kubectl get service --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    Salida de ejemplo:
  ```
  cluster-autoscaler       10.10.10.204   <none>         8085/TCP                     4m
  ```
  {: screen}


## Desinstalación del programa de escalado automático

Puede desinstalar el despliegue del programa de escalado automático del clúster.

1. Inicie una sesión en {{site.data.keyword.Bluemix_notm}} y seleccione como destino el clúster ejecutando `ibmcloud ks cluster-config <cluster_name>`.

2. Obtenga el nombre del despliegue.

  ```
  helm ls | grep autoscaler
  <deployment_name>	1       	Mon Dec 18 13:45:09 2017	DEPLOYED	autoscaler-1.0.3	kube-system
  ```
  {: pre}

3. Suprima el despliegue.

  ```
  helm delete <deployment_name>
  ```
  {: pre}
