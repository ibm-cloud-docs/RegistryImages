---

copyright:
  years: 2018
lastupdated: "2018-08-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Getting started with the ibmcloud-secure-perimeter-health image
{: #ibmcloud-secure-perimeter-health}

The **ibmcloud-secure-perimeter-health** image contains a tool for scanning vulnerabilities in a Secure Perimeter in IBM Cloud.
{:shortdesc}

## How it works
{: #how-it-works}

To ensure your Secure Perimeter is functioning properly, **ibmcloud-secure-perimeter-health** can scan public or private networks in your IBM Cloud infrastructure account and report vulnerabilities. You can use **ibmcloud-secure-perimeter-health** image in two ways:

-   Use **ibmcloud-secure-perimeter-health** as a pod on a Kubernetes cluster within your Secure Perimeter to scan for private network exposures.
-   Use **ibmcloud-secure-perimeter-health** as a stand-alone Docker container on your workstation to scan for public network exposures.

More information on Secure Perimeter can be found in these blog articles:
  * [Set up a Secure Perimeter in IBM Cloud](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/)
  * [Set up an automated Secure Perimeter in IBM Cloud](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

After scanning, the **ibmcloud-secure-perimeter-health** image produces a report on which networks were reachable from within the Secure Perimeter Segment. Each report details the name of the network gateway, VLAN, its subnets, and any offending hosts. An example report of a user that scanned for private network vulnerabilities:
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

## What is included
{: #whats_included}

The **ibmcloud-secure-perimeter-health** image provides the following software packages.
{:shortdesc}

-   Alpine Linux
-   Python runtime
-   SoftLayer Python Client
-   Nmap port scanner

## Getting started
{: #how_to_get_started}

Review the following tasks to learn how to use **ibmcloud-secure-perimeter-health**:

1.  [Provision a Kubernetes cluster within a Secure Perimeter using {{site.data.keyword.containerlong}}](#provision_cluster)
2.  [Scan private networks within a Secure Perimeter](#private_networks)
3.  [Scan public networks outside a Secure Perimeter](#public_networks)
4.  [Understanding scan results](#scan_results)
5.  [Container argument reference](#reference_container_arg)
6.  [Environment variable reference](#reference_env_var)


## Provision a Kubernetes cluster within a Secure Perimeter by using {{site.data.keyword.containerlong_notm}}
{: #provision_cluster}

1.  Provision your Kubernetes cluster from the **Containers** section in IBM Cloud catalog.
2.  Click Create.
3.  Select the Secure Perimeter Segment public and private VLANs from the VLAN dropdown menus.
4.  Fill out all other details as you deem fit.
5.  Click Create Cluster.

Review [{{site.data.keyword.containerlong}}](/docs/containers/container_index.html#container_index) documentation on how to get access to your cluster once it has been deployed.

## Scan private networks within a Secure Perimeter
{: #private_networks}

Create a container pod from the **ibmcloud-secure-perimeter-health** image, and set up a routine scan.

Before you begin:

-   Install the required [CLIs](/docs/containers/cs_cli_install.html#cs_cli_install).
-   [Target your CLI](/docs/containers/cs_cli_install.html#cs_cli_configure) to your cluster.

1. Create a configuration file that is named _health-pod.yaml_. This file creates a highly-available deployment of the container pod.

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
            image: registry.bluemix.net/ibm/ibmcloud-secure-perimeter-health:1.0.0
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

2. Create the deployment.

    ```
    kubectl apply -f health-pod.yaml
    ```
    {: pre}

3. Confirm the pod is running.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    health-pod-<random-id>                  1/1       Running   0          1hr
    ```
    {: screen}

## Scan public networks outside a Secure Perimeter
{: #public_networks}

Create a Docker container from the **ibmcloud-secure-perimeter-health** image and scan public networks.

Before you begin:

-  Install Docker

1. Create a Docker container from your own workstation as follows:

    ```
    docker run -it -e SL_USER='$SL_USER' -e SL_APIKEY='$SL_APIKEY' registry.bluemix.net/ibm/ibmcloud-secure-perimeter-health:1.0.0 /usr/local/bin/python run.py --scan public --allowed-public-ports 80 443 9000-9999
    ```
    {: pre}

2. After the container produces a report, review the [Analysing scan results](#scan_results) section to understand the results.

## Analysing scan results
{: #scan_results}

The **ibmcloud-secure-perimeter-health** produces a formatted report on functioning health of a Secure Perimeter:
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

The format of the report is as follows:
```
<gateway name>:
  <vlan name>:
    <subnet>:    PASS
    <subnet>:    FAIL
      host = <ip address>, ports = [<exposed port>, <exposed port>, ...]
```
{: screen}

The **ibmcloud-secure-perimeter-health** determines a subnet as `PASS` if no hosts in the subnet were reachable, otherwise it returns with `FAIL` and lists the hosts that were reachable, along with the ports that were accessible.

## Container argument reference
{: #reference_container_arg}

|Key|Description|Default
|---|-------------|---|
|scan|The type of exposure scan ("public" or "private") |None (scan both)
|exclude-vlan-ids|List of VLANs by IDs to avoid scanning|None
|poll-interval|Set the number of seconds until the next scan|0 (run once)
|allowed-public-ports|List of ports to whitelist under the scan|80, 443, 9000-9999
{: caption="Table 1. container arguments" caption-side="top"}

## Environment variable reference
{: #reference_env_var}

|Key|Description|
|---|-------------|
|SL_USER|Your IBM Cloud infrastructure user name|
|SL_APIKEY|Your IBM Cloud infrastructure API key|
{: caption="Table 2. environment variables" caption-side="top"}
