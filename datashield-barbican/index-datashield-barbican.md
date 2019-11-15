---

copyright:
  years: 2018, 2019
lastupdated: "2019-11-15"

keywords: registry, Data Shield environment, datashield-barbican image, container image, Barbican, Registry, data in use, memory encryption, Intel SGX, Fortanix, public images, 

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

# Getting started with the `datashield-barbican` image
{: #datashield-barbican_starter}

This container image provides protection for data that is in use by running Barbican in the {{site.data.keyword.datashield_full}} environment. For more information about the service and what it means to protect data in use, see the [{{site.data.keyword.datashield_short}} documentation](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

You can access the images that are provided by {{site.data.keyword.IBM}} by using the command line, see [{{site.data.keyword.IBM_notm}} public images](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Deploying the image
{: #datashield-barbican-deploy}

You can use the following Kubernetes pod specification to deploy the image:

```
apiVersion: v1
kind: Pod
metadata:
  name: data-shield-barbican
  labels:
    app: data-shield-barbican
spec:
  containers:
  - name: data-shield-barbican
    image: <IMAGE_NAME>
    volumeMounts:
    - mountPath: /dev/isgx
      name: isgx
    - mountPath: /dev/gsgx
      name: gsgx
    - mountPath: /var/run/aesmd/aesm.socket
      name: aesm-socket
    env:
    - name: NODE_IP
      valueFrom:
        fieldRef:
          fieldPath: status.hostIP
    - name: NODE_AGENT_BASE_URL
      value: http://$(NODE_IP):9092/v1
    ports:
    - containerPort: 9311
      name: barbican
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
---
apiVersion: v1
kind: Service
metadata:
  name: data-shield-barbican
  labels:
    app: data-shield-barbican
spec:
  type: LoadBalancer
  selector:
    app: data-shield-barbican
  ports:
    - name: barbican
      port: 9311
      targetPort: 9311
```
{: codeblock}

| Variable | Description |
|-----------------|-----------------|
| `IMAGE_NAME` | The name of the image that you want to deploy. |
| `NODE_IP` | The node IP address of your Barbican instance. |
{: caption="Table 1. Required input variables" caption-side="top"}

It might take a few minutes for the container to deploy and start. The demonstration Barbican instance is configured without Keystone authentication. Requests must include the header `X-Project-Id: 12345`.
{: tip}

## Creating a secret
{: #datashield-barbican-secret}

You can create a secret in the Barbican demonstration instance by running the following command:

```
curl -sS -X POST -H 'content-type:application/json' -H 'X-Project-Id: 12345' -d \
    '{"payload": "my-secret-here", "payload_content_type": "text/plain"}' \
    http://<node>:9311/v1/secrets
```
{: codeblock}

The response contains the URL for the created secret. You can retrieve the secret by running the following command:

```
curl -sS -H 'X-Project-Id: 12345' <url>
```
{: codeblock}
