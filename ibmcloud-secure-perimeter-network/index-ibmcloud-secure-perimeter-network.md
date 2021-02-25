---

copyright:
  years: 2018, 2021
lastupdated: "2021-02-25"

keywords: Registry, ibmcloud-secure-perimeter-network, container image, network, Secure Perimeter, public images,

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
{:term: .term}
{:external: target="_blank" .external}

# Getting started with the `ibmcloud-secure-perimeter-network` image
{: #ibmcloud-secure-perimeter-network}

The `ibmcloud-secure-perimeter-network` image contains tools for automating the configuration of Vyatta virtual router appliances within a Secure Perimeter.
{:shortdesc}

You can access the images that are provided by {{site.data.keyword.IBM}} by using the command line, see [{{site.data.keyword.IBM_notm}} public images](/docs/Registry?topic=Registry-public_images#public_images).
{: tip}

## How it works
{: #spn_how-it-works}

With `ibmcloud-secure-perimeter-network`, you can automate the configuration of your Secure Perimeter's Vyatta virtual router appliance.

You can use the `ibmcloud-secure-perimeter-network` image in two ways:

- Use `ibmcloud-secure-perimeter-network` as a Docker container to initialize the Secure Perimeter firewall rules configuration.
- Use `ibmcloud-secure-perimeter-network` as a pod on a Kubernetes cluster to poll the {{site.data.keyword.cloud}} infrastructure account for new subnets that are created on your Secure Perimeter Segment VLANs and add them to the Vyatta firewall configuration.

## What is included
{: #spn_whats_included}

The `ibmcloud-secure-perimeter-network` image provides the following software packages.
{:shortdesc}

- Alpine Linux
- Python runtime
- SoftLayer Python Client
- Ansible

## Prerequisites
{: #spn_prerequisites}

- Vyatta and VLANs ordered from the {{site.data.keyword.cloud_notm}} infrastructure portal and VLANs that are associated to the Vyatta.
- The automated Secure Perimeter deployment preinstalls the Vyatta with SSH keys that `ibmcloud-secure-perimeter-network` uses to access the gateway. SSH keys must be loaded either manually or through the Secure Perimeter installation process.

## Provision a Kubernetes cluster within a Secure Perimeter by using {{site.data.keyword.containerlong_notm}}
{: #spn_provision_cluster}

1. Provision your Kubernetes cluster from the **Containers** section in {{site.data.keyword.cloud_notm}} catalog.
2. Click **Create**.
3. Select the Secure Perimeter Segment public and private VLANs from the VLAN lists.
4. Enter all other details as required.
5. Click **Create Cluster**.

To access your cluster after deployment, see [Getting started with {{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-getting-started#getting-started).

## Run initial config of your Secure Perimeter Vyatta
{: #spn_initial_setup}

1. Create a file that is named `config.json`. This file contains the basic options that are required by `ibmcloud-secure-perimeter-network` to access the Vyatta.

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

   For more information about how to populate `config.json`, see the [`config.json` reference table](#spn_reference_config_json). This file can also be used in the [setup of `ibmcloud-secure-perimeter-network` as a Kubernetes pod](#spn_setup) process.

2. Run `ibmcloud-secure-perimeter-network` as a Docker container to begin initial setup.

   ```
   docker run icr.io/ibm/ibmcloud-secure-perimeter-network:1.0.0 python config-secure-perimeter.py -v /path/to/current/dir:/opt/secure-perimeter
   ```
   {: pre}

   This action creates a file `state.json` in your working directory. This file is used to [set up `ibmcloud-secure-perimeter-network` as a Kubernetes pod](#spn_setup).

## Set up a Kubernetes pod within your Secure Perimeter
{: #spn_setup}

If you want the `ibmcloud-secure-perimeter-network` image to manage subnets on your Secure Perimeter, you can run it as a long-lived process by using a Kubernetes pod. To configure the pod for the Vyatta, you must copy several configuration files and folders from the `ibmcloud-secure-perimeter-network` image to the pod to configure it for the Vyatta:

1. Create a file that is named `pvc.yaml`. This configuration file creates a persistent volume claim (PVC) that you can mount to your pod as a volume.

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

2. Create the PVC.

   ```
   kubectl apply -f restore-pvc.yaml
   ```
   {: pre}

3. Create a file that is named `network-pod.yaml`. This configuration file deploys the `ibmcloud-secure-perimeter-network` image as a pod in your Kubernetes cluster and mounts your persistent volume claim as a volume.

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
           image: icr.io/ibm/ibmcloud-secure-perimeter-network:1.0.0
           volumeMounts:
           - name: network-vol
             mountPath: /opt/secure-perimeter
         volumes:
         - name: network-vol
           PersistentVolumeClaim:
             claimName: network-pvc
   ```
   {: codeblock}

4. Create a file that is named `rules.conf`. This configuration file tells `ibmcloud-secure-perimeter-network` which external subnets and ports from the public internet to allowlist into the Secure Perimeter.

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

5. Copy the files onto the `ibmcloud-secure-perimeter-network` pod.

   ```
   kubectl cp keys network-pod:/opt/secure-perimeter/
   kubectl cp state.json network-pod:/opt/secure-perimeter/state.json
   kubectl cp config.json network-pod:/opt/secure-perimeter/config.json
   kubectl cp rules.conf network-pod:/opt/secure-perimeter/rules.conf
   ```
   {: pre}

   The directory `keys` contains the SSH keys that are required for `ibmcloud-secure-perimeter-network` to access the Vyatta. For more information about the SSH keys, see the [prerequisites section](#spn_prerequisites).

## `config.json` reference
{: #spn_reference_config_json}

The following table shows the `config.json`.

| Key | Description |
|---|-------------|
| `slid` | Your {{site.data.keyword.cloud_notm}} infrastructure username. |
| `apikey` | Your {{site.data.keyword.cloud_notm}} infrastructure API key. |
| `region` | The {{site.data.keyword.cloud_notm}} region where the Vyatta is deployed. |
| `inf_name_private` | The name of the Vyatta private interface. |
| `inf_name_public` | The name of the Vyatta public interface. |
| `gatewayid` | The Vyatta gateway ID. |
| `vlans` | List of Secure Perimeter Segment VLANs containing the type, VLAN number, and VLAN ID. |
| `vyatta_gateway_vip` | The VIP of the Gateway. |
| `vyatta_primary` |Object that contains the private and public IP of the primary Vyatta member. |
| `vyatta_secondary` |Object that contains the private and public IP of the secondary Vyatta member. |
{: caption="Table 1. <code>config.json</code>" caption-side="top"}

## `rules.conf` reference
{: #spn_reference_rules_conf}

The following table shows the `rules.conf`.

| Key | Description |
|---|-------------|
| `external_subnets` | List of subnets on the public internet that Secure Perimeter can use. |
| `external_ports` | List of ports that Secure Perimeter can use. |
| `userips` | List of user IP addresses to allowlist to Secure Perimeter. |
{: caption="Table 2. <code>rules.conf</code>" caption-side="top"}
