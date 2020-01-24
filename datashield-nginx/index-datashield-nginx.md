---

copyright:
  years: 2018, 2020
lastupdated: "2020-01-23"

keywords: registry, Data Shield environment, nginx image, container image, public images, data in use, memory encryption, Intel SGX, Fortanix,

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

# Getting started with the `datashield-nginx` image
{: #datashield-nginx_starter}

This container image provides protection for data that is in use by running NGINX in the {{site.data.keyword.datashield_full}} environment. For more information about the service and what it means to protect data in use, see the [{{site.data.keyword.datashield_short}} documentation](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

You can access the images that are provided by {{site.data.keyword.IBM}} by using the command line, see [{{site.data.keyword.IBM_notm}} public images](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Deploying the image
{: #datashield-nginx-deploy}

You can use the following Kubernetes pod spec to deploy the image:

```
apiVersion: v1
kind: Pod
metadata:
  name: data-shield-nginx
  labels:
    app: data-shield-nginx
spec:
  containers:
  - name: data-shield-nginx
    image: <IMAGE_NAME>
    volumeMounts:
    - mountPath: /dev/isgx
      name: isgx
    - mountPath: /dev/gsgx
      name: gsgx
    - mountPath: /var/run/aesmd/aesm.socket
      name: aesm-socket
    ports:
    - containerPort: 443
      name: https
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
  name: data-shield-nginx
  labels:
    app: data-shield-nginx
spec:
  type: LoadBalancer
  selector:
    app: data-shield-nginx
  ports:
    - name: https
      port: 443
      targetPort: 443
```
{: codeblock}

The following table shows the required input variables.

| Variable | Description |
|-----------------|-----------------|
| `IMAGE_NAME` | The name of the image that you want to deploy. |
| `NODE_IP` | The node IP address of your NGINX instance. |
{: caption="Table 1. Required input variables" caption-side="top"}

To provide your own content for NGINX to serve, place it under `/usr/local/nginx/html` in the container.
{: tip}
