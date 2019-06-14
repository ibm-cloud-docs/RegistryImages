---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-03"

keywords: IBM Cloud Container Registry, Data Shield environment, nginx image, container image, public image, data in use, memory encryption, Intel SGX, Fortanix,

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

# `datashield-nginx` 이미지 시작하기
{: #datashield-nginx_starter}

이 컨테이너 이미지는 {{site.data.keyword.datashield_short}} 환경에서 NGINX를 실행하여 사용 중인 데이터를 보호합니다. 서비스 및 "사용 중인 데이터"를 보호하는 것의 의미에 대한 자세한 정보는 [{{site.data.keyword.datashield_short}} 문서](/docs/services/data-shield?topic=data-shield-about#about)를 참조하십시오.
{: shortdesc}

명령행을 사용하여 {{site.data.keyword.IBM}}에서 제공한 이미지에 액세스할 수 있습니다. [IBM 공용 이미지](/docs/services/Registry?topic=registry-public_images#public_images)를 참조하십시오.
{: tip}

## 이미지 배치
{: #datashield-nginx-deploy}

다음 Kubernetes 팟(Pod) 스펙을 사용하여 이미지를 배치할 수 있습니다.

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

<table>
<caption>표 1. 필수 입력 변수</caption>
  <tr>
    <th>변수</th>
    <th>설명</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>배치하려는 이미지 이름입니다.</td>
  </tr>
  <tr>
    <td><code>NODE_IP</code></td>
    <td>NGINX 인스턴스의 노드 IP 주소입니다.</td>
  </tr>
</table>

기여할 NGINX에 대한 고유 컨텐츠를 제공하려면, 컨테이너에서 `/usr/local/nginx/html` 아래 배치하십시오.
{: tip}
