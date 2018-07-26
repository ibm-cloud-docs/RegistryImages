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

# Getting started with the iccs-cluster-autoscaler image (alpha)
{: #ibm-cluster-autoscaler}

The iccs-cluster-autoscaler image contains the preinstalled packages that are needed to automatically scale the worker nodes within your {{site.data.keyword.containerlong}} cluster. The iccs-cluster-autoscaler is based on the [Kubernetes Cluster-Autoscaler project](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler).
{:shortdesc}

The alpha version of the autoscaler currently supports single zone availability only and is not available for worker nodes on physical bare metal servers.
{: tip}

## How it works
{: #how-it-works}

With the iccs-cluster-autoscaler image you can scale the worker nodes within your Kubernetes cluster up or down, across any specified autoscaling group when working in {{site.data.keyword.containerlong}}. The autoscaler runs as a deployment in the `kube-system` namespace of your cluster and is a plug-in on the Kubernetes cluster autoscaler.
{: shortdesc}

The iccs-cluster-autoscaler monitors the pods in your cluster by watching the Kubernetes API server and can automatically scale the size of your cluster up or down.

<dl>
  <dt>Scale-up</dt>
    <dd>Periodically, the iccs-cluster-autoscaler checks for any pods that are unschedulable. A pod is unschedulable when the Kubernetes scheduler fails to find a node on which to run the pod. When the autoscaler encounters pods that are labeled unschedulable, it adds a worker node to your cluster.</dd>
  <dt>Scale-down</dt>
    <dd>By default, the iccs-cluster-autoscaler checks for unneeded nodes every 10 seconds. If a node is unneeded for more than 10 minutes, it is deleted.</br> Nodes are considered unneeded when:</br>
    <ul><li>The sum of CPU and memory requests of all pods running on this node is less than 50% of the amount that is allocated to it.</li>
    <li>All pods running on the node can be moved to other nodes. Pods that run on all nodes by default, such as manifest-run pods or those created by daemonsets, are excluded from this test.</li>
    <li>There is no scale-down disabled annotation. </li></ul></dd>
</dl>

But what does all of this mean?

![Automatically scaling a cluster](/images/autoscaler.png)

When you create your Kubernetes cluster in IBM Cloud, you specify your hardware preferences. The autoscaler creates groups based on the machine types to allow you to ensure that you are using the correct amount of resources. In the image, you can see an example autoscaling policy. In the configuration, the machine type and minimum and maximum number of nodes is specified. If you want the autoscaler to ignore a set of nodes and not scale, don't include them in your configuration. In the image, the cluster scaled up to handle changes in resources. The b2c.16x64 node was not scaled because no policy was set in the configuration file.

## Usage restrictions
{: #usage}
This image can be used in {{site.data.keyword.containershort}} with Kubernetes API version 1.7 or later.

## Prerequisites
{: #prerequisites}

There are some prerequisites that you must complete before working with the iccs-cluster-autoscaler.

* You must have a standard cluster to use the autoscaler plug-in. Autoscaling groups are made up of worker nodes that are the same [machine types](/docs/containers/cs_cli_reference.html#cs_machine_types). To verify that you meet the requirements, run the following command and check that your **Machine Type** does not say _free_. Check that your **Version** is `version 1.7` or later </br>
    ```
    ibmcloud ks workers <cluster-name>
    ```
    {: pre}
    Example output: </br>
    ```
    OK
    ID                                                   Public IP        Private IP     Machine Type   State    Status   Version
    stage-dal06-crffef8b930b214442802bdcaa551b5ec0-w1    169.54.180.171   10.146.32.7    u2c.2x4        normal   Ready    1.8.2_1504
    ```
    {: screen}
* You must install the following CLIs and plugins. For help installing the requirements and creating your first cluster, [check out this tutorial in the docs](/docs/containers/cs_tutorials.html#cs_cluster_tutorial).
    <ul><li>{{site.data.keyword.Bluemix_notm}} CLI</li><li>Kubectl CLI</li><li>Docker CLI</li><li>{{site.data.keyword.containerlong_notm}} plugin</li><li>{{site.data.keyword.registrylong_notm}} plugin</li></ul>
* To scale worker nodes on your behalf, the autoscaler needs to know your Identity and Access Management details. The credentials are available as a secret in a paid cluster. To verify that the secret is available, run the following command. The command returns a secret.
    ```
		$kubectl get secrets -n kube-system | grep storage-secret-store
    ```
    {: pre}

## Installing and configuring the autoscaler
{: #install-configure}

To install the autoscaler, you need to first install and initialize Helm to your cluster. After the autoscaler is running, it automatically conserves resources and scales your clusters.

1. Log in to {{site.data.keyword.containershort_notm}} and target your cluster.
    ```
    ibmcloud ks cluster-config <cluster_name>`.
    ```
    {: pre}

2. [Install helm and initialize it in your cluster](https://docs.helm.sh/using_helm/#quickstart).

3. Using the provided template, create a configuration file.

   ```
   cp incubator/helm/autoscaler/config.yaml.template config.yaml
   ```
   {: pre}

    In order for scaling groups to work, all of your machine types must be specified in your configuration file, even if you don't provide a minimum and maximum range.
    {: tip}

  <table summary="The first row in the table spans both columns. The rest of the rows should be read left to right, with the parameter in column one, the description that matches in column two, and the default in column three.">
  <caption>Configurable parameters and their default value</caption>
    <thead>
      <th colspan=3><img src="../images/idea.png" alt="Idea icon"/>Configurable parameters and their default value </th>
    </thead>
    <tbody>
      <tr>
        <td><code>autoscalingGroups.name</code></td>
        <td> The machine-type that you would like to scale. </td>
        <td> No default, user-specified. To find your machine-type run <code>ibmcloud ks workers <cluster-name></code></td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.minSize</code></td>
        <td> The minimum number of worker nodes to which your cluster should scale. </td>
        <td> No default, user-specified. </td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.maxSize</code></td>
        <td> The maximum number of worker nodes to which your cluster should scale. </td>
        <td> No default, user-specified. </td>
      </tr>
      <tr>
        <td><code>api_route</code></td>
        <td> The route to the {{site.data.keyword.containershort_notm}} API for the region in which your cluster is running. </td>
        <td> https://containers.bluemix.net/swagger-api/ </td>
      </tr>
      <tr>
        <td><code>resources.limits.cpu</code></td>
        <td> The amount of CPU that the autoscaler pod is limited to. </td>
        <td> 300m </td>
      </tr>
      <tr>
        <td><code>resources.limits.memory</code></td>
        <td> The amount of memory the autoscaler pod is limited to. </td>
        <td> 300Mi </td>
      </tr>
      <tr>
        <td><code>resources.requests.cpu</code></td>
        <td> The amount of CPU you want the autoscaler pod to start with. </td>
        <td> 100m </td>
      </tr>
      <tr>
        <td><code>resources.requests.memory</code></td>
        <td> The amount of memory you want the autoscaler pod to start with. </td>
        <td> 100Mi </td>
      </tr>
      <tr>
        <td><code>scale_down_delay</code></td>
        <td> The amount of time the scaler should wait to scale down after issuing a scale up request. </td>
        <td> 10m </td>
      </tr>
      <tr>
        <td><code>scale_down_unneeded_time</code></td>
        <td> The mount of time a node should be unneeded before it is eligible to be scaled down. </td>
        <td> 10m </td>
      </tr>
      <tr>
        <td><code>skipNodes.withLocalStorage</code></td>
        <td> When set to true, nodes that contain local storage will be skipped and will not be scaled down. </td>
        <td> true </td>
      </tr>
      <tr>
        <td><code>skipNodes.withSystemPods</code></td>
        <td> When set to true, nodes that contain kube-system pods will be skipped and will not be scaled down. </td>
        <td> true </td>
      </tr>
      <tr>
        <td><code>image.repository</code></td>
        <td> The cluster autoscaler Docker image. </td>
        <td> registry.bluemix.net/iccs-cluster-autoscaler </td>
      </tr>
      <tr>
        <td><code>image.tag</code></td>
        <td> The version of the image that you want to pull. </td>
        <td> 12 </td>
      </tr>
      <tr>
        <td><code>image.pullPolicy</code></td>
        <td> Specifies when to pull the Docker image. </td>
        <td> Always. </td>
      </tr>
    </tbody>
  </table>
  
  The iccs-cluster-autoscaler supports various configuration parameters. For more detailed information about how it works, see [the Kubernetes Autoscaler FAQ](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md).

4. Install the helm chart to your cluster in the `kube-system` namespace.

  ```
  helm install incubator/helm/autoscaler -f config.yaml --namespace kube-system
  ```
  {: pre}

    Your output should be similar to the following:
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

## Verifying the installation

1. Verify that your cluster containing the autoscaler pod is running.

  ```
  kubectl get pods --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    Example output:
  ```
    cluster-autoscaler-4289984493-zldfk        1/1       Running   0          3m
  ```
  {: screen}

2. Verify that the cluster autoscaler is running.

  ```
  kubectl get service --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    Example output:
  ```
  cluster-autoscaler       10.10.10.204   <none>         8085/TCP                     4m
  ```
  {: screen}


## Uninstalling the autoscaler

You can uninstall the cluster autoscaler deployment.

1. Log in to {{site.data.keyword.Bluemix_notm}} and target your cluster by running `ibmcloud ks cluster-config <cluster_name>`.

2. Get the name of your deployment.

  ```
  helm ls | grep autoscaler
  <deployment_name>	1       	Mon Dec 18 13:45:09 2017	DEPLOYED	autoscaler-1.0.3	kube-system
  ```
  {: pre}

3. Delete the deployment.

  ```
  helm delete <deployment_name>
  ```
  {: pre}
