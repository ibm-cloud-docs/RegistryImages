---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-03"

keywords: IBM Cloud Container Registry, Data Shield environment, datashield-barbican image, container image, Barbican, Registry, data in use, memory encryption, Intel SGX, Fortanix,

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

# `datashield-barbican` 이미지 시작하기
{: #datashield-barbican_starter}

이 컨테이너 이미지는 {{site.data.keyword.datashield_short}} 환경에서 Barbican을 실행하여 사용 중인 데이터를 보호합니다. 서비스 및 "사용 중인 데이터"를 보호하는 것의 의미에 대한 자세한 정보는 [{{site.data.keyword.datashield_short}} 문서](/docs/services/data-shield?topic=data-shield-about#about)를 참조하십시오.
{: shortdesc}

명령행을 사용하여 {{site.data.keyword.IBM}}에서 제공한 이미지에 액세스할 수 있습니다. [IBM 공용 이미지](/docs/services/Registry?topic=registry-public_images#public_images)를 참조하십시오.
{: tip}

## 이미지 배치
{: #datashield-barbican-deploy}

다음 Kubernetes 팟(Pod) 스펙을 사용하여 이미지를 배치할 수 있습니다.

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
    <td>Barbican 인스턴스의 노드 IP 주소입니다.</td>
  </tr>
</table>

컨테이너가 배치하고 시작하는 데 몇 분이 소요될 수 있습니다. 데모 Barbican 인스턴스는 Keystone 인증 없이 구성됩니다. 요청에는 헤더 `X-Project-Id: 12345`가 포함되어야 합니다.
{: tip}

## 시크릿 작성
{: #datashield-barbican-secret}

다음 명령을 실행하여 Barbican 데모 인스턴스에 시크릿을 작성할 수 있습니다.

```
    curl -sS -X POST -H 'content-type:application/json' -H 'X-Project-Id: 12345' -d \
        '{"payload": "my-secret-here", "payload_content_type": "text/plain"}' \
        http://<node>:9311/v1/secrets
```
{: codeblock}

응답에는 작성된 시크릿에 대한 URL이 포함되어 있습니다. 다음 명령을 실행하여 시크릿을 검색할 수 있습니다.

```
    curl -sS -H 'X-Project-Id: 12345' <url>
```
{: codeblock}
