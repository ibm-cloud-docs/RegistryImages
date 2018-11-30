---

copyright:
  years: 2018
lastupdated: "2018-10-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip} 
{:table: .aria-labeledby="caption"}

# `datashield-barbican` 이미지 시작하기
{: #datashield-barbican_starter}

이 컨테이너 이미지는 Data Shield 환경에서 Barbican을 실행하며 사용 중인 데이터에 대한 보호를 제공합니다.
{:shortdesc}

명령행을 사용하여 {{site.data.keyword.IBM}}에서 제공한 이미지에 액세스할 수 있습니다. [IBM 공용 이미지](/docs/services/Registry/registry_public_images.html#public_images)를 참조하십시오.
{: tip}

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
        image: <TODO INSERT APPROPRIATE IMAGE NAME HERE>
        volumeMounts:
        - mountPath: /dev/isgx
          name: isgx
        - mountPath: /dev/gsgx
          name: gsgx
        - mountPath: /var/run/aesmd/aesm.socket
          name: aesm-socket
        ports:
        - containerPort: 9311
          hostPort: 9311
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
```
{: codeblock}

컨테이너가 배치하고 시작하는 데 몇 분이 소요될 수 있습니다. 데모 Barbican 인스턴스는 Keystone 인증 없이 구성됩니다. 요청에는 헤더 `X-Project-Id: 12345`가 포함되어야 합니다.
{: tip}

다음 명령을 실행하여 Barbican 데모 인스턴스에 시크릿을 작성할 수 있습니다.

```
    curl -sS -X POST -H 'content-type:application/json' -H 'X-Project-Id: 12345' -d \
        '{"payload": "my-secret-here", "payload_content_type": "text/plain"}' \
        http://<node>:9311/v1/secrets
```
{: pre}
    
응답에는 작성된 시크릿에 대한 URL이 포함되어 있습니다. 다음 명령을 실행하여 시크릿을 검색할 수 있습니다.

```
    curl -sS -H 'X-Project-Id: 12345' <url>
```
{: pre}
