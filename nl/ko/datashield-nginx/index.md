---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-27"

keywords: IBM Cloud Container Registry, Data Shield environment, nginx image, container image, public image, data in use, memory encryption, intel sgx, fortanix,

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

이 컨테이너 이미지는 Data Shield 환경에서 NGINX를 실행하여 사용 중인 데이터를 보호합니다. 서비스에 대한 자세한 내용과 "사용 중인 데이터"를 보호하는 방법에 대한 자세한 내용은 [IBM Cloud Data Shield 문서](/docs/services/data-shield?topic=data-shield-about#about)를 참조하십시오.
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
        - containerPort: 80
          hostPort: 80
          name: http
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
<caption>표 1. 필수 입력 변수</caption>
  <tr>
    <th>변수</th>
    <th>설명</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>배치하려는 이미지 이름입니다. </td>
  </tr>
</table>

기여할 NGINX에 대한 고유 컨텐츠를 제공하려면, 컨테이너에서 `/usr/local/nginx/html` 아래 배치하십시오.
{: tip}
