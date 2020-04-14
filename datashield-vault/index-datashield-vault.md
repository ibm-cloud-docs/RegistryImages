---

copyright:
  years: 2018, 2020
lastupdated: "2020-04-10"

keywords: registry, Data Shield environment, container image, public images, Vault image, data in use, memory encryption, Intel SGX, Fortanix,

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

# Getting started with the `datashield-vault` image
{: #datashield-vault_starter}

This container image provides protection for data that is in use by running Vault in the {{site.data.keyword.datashield_full}} environment. For more information about the service and what it means to protect data in use, see the [{{site.data.keyword.datashield_short}} documentation](/docs/data-shield?topic=data-shield-about#about).
{: shortdesc}

You can access the images that are provided by {{site.data.keyword.IBM}} by using the command line, see [{{site.data.keyword.IBM_notm}} public images](/docs/Registry?topic=registry-public_images#public_images).
{: tip}

## Deploying the image
{: #datashield-vault-deploy}

You can use the following Kubernetes pod spec to deploy the image:

```
apiVersion: v1
kind: Pod
metadata:
  name: data-shield-vault
  labels:
    app: data-shield-vault
spec:
  containers:
  - name: data-shield-vault
    image: <IMAGE-NAME>
    volumeMounts:
    - mountPath: /dev/isgx
      name: isgx
    - mountPath: /dev/gsgx
      name: gsgx
    - mountPath: /var/run/aesmd/aesm.socket
      name: aesm-socket
    ports:
    - containerPort: 8200
      name: vault
      protocol: TCP
    env:
    - name: NODE_IP
      valueFrom:
        fieldRef:
          fieldPath: status.hostIP
    - name: NODE_AGENT_BASE_URL
      value: http://$(NODE_IP):9092/v1
  volumes:
  - name: isgx
    hostPath:
      path: /dev/isgx
  - name: gsgx
    hostPath:
      path: /dev/gsgx
  - name: aesm-socket
    hostPath:
      path: /var/run/aesmd/aesm.socket
---
apiVersion: v1
kind: Service
metadata:
  name: data-shield-vault
  labels:
    app: data-shield-vault
spec:
  type: LoadBalancer
  selector:
    app: data-shield-vault
  ports:
    - name: vault
      port: 8200
      targetPort: 8200
```
{: codeblock}

The following table shows the required input variables.

| Variable | Description |
|-----------------|-----------------|
| `IMAGE_NAME` | The name of the image that you want to deploy. |
| `NODE_IP` | The node IP address of your Vault instance. To find the IP address, you can use either `kubectl get pod -owide` or `kubectl describe pod`. |
{: caption="Table 1. Required input variables" caption-side="top"}

## Accessing a protected instance of Vault
{: #datashield-vault-access}

To access the protected instance of Vault, complete the following steps:

1. Look up the node IP address for your instance by running one of the following commands.

   - Option 1:

   ```
   kubectl get pod -owide
   ```
   {: codeblock}

   - Option 2:
  
   ```
   kubectl describe pod
   ```
   {: codeblock}

2. Set the `VAULT_ADDR` environment variable to `http://<YOUR_IP>:8200`.

You can use Vault CLI commands like `vault init`, `vault unseal`, `vault auth`, `vault write`, and `vault read` to authenticate and access secrets. For more information about how to use the Vault CLI, see [Vault Commands (CLI)](https://www.vaultproject.io/docs/commands){: external}.
{: tip}
