---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-13"

keywords: IBM Cloud Container Registry, Data Shield environment, container image, public image, vault image, data in use, memory encryption, intel sgx, fortanix,

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

# Getting started with the `datashield-vault` image
{: #datashield-vault_starter}

This container image provides protection for data that is in use by running Vault in the Data Shield environment. For more information about the service and what it means to protect "data in use", see the [IBM Cloud Data Shield documentation](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

You can access the images that are provided by {{site.data.keyword.IBM}} by using the command line, see [IBM public images](/docs/services/Registry?topic=registry-public_images#public_images).
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
        image: <IMAGE_NAME>
        volumeMounts:
        - mountPath: /dev/isgx
          name: isgx
        - mountPath: /dev/gsgx
          name: gsgx
        - mountPath: /var/run/aesmd/aesm.socket
          name: aesm-socket
        ports:
        - containerPort: 8200
          hostPort: 8200
          name: vault
          protocol: TCP
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
```
{: pre}

<table>
<caption>Table 1. Required input variables</caption>
  <tr>
    <th>Variable</th>
    <th>Description</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>The name of the image that you want to deploy.</td>
  </tr>
</table>

To access the protected instance of Vault:

1. Look up the node IP address for your instance by running one of the following commands.

  * Option 1:

    ```
    kubectl get pod -owide
    ```
    {: pre}

  * Option 2:
    ```
    kubectl describe pod
    ```
    {: pre}

2. Set the `VAULT_ADDR` environment variable to `http://<YOUR_IP>:8200`.
  

Use `kubectl get pod -owide` or `kubectl describe pod` to look up the node IP address for your vault instance. Set the `VAULT_ADDR` environment variable to `http://<IP>:8200` to access the Data Shield-protected Vault instance.

You can use Vault CLI commands like `vault init`, `vault unseal`, `vault auth`, `vault write`, and `vault read` to authenticate and access secrets. For more information about how to use the Vault CLI, see [Vault Commands (CLI) ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://www.vaultproject.io/docs/commands/index.html).
{: tip}
