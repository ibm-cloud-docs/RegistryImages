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

# `datashield-nginx` 이미지 시작하기
{: #datashield-nginx_starter}

이 컨테이너 이미지는 Data Shield 환경에서 NGINX를 실행하며 사용 중인 데이터에 대한 보호를 제공합니다.
{:shortdesc}

명령행을 사용하여 {{site.data.keyword.IBM}}에서 제공한 이미지에 액세스할 수 있습니다. [IBM 공용 이미지](/docs/services/Registry/registry_public_images.html#public_images)를 참조하십시오.
{: tip}


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
        image: <TODO INSERT APPROPRIATE IMAGE NAME HERE>
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
{: codeblock}    
    
기여할 NGINX에 대한 고유 컨텐츠를 제공하려면, 컨테이너에서 `/usr/local/nginx/html` 아래 배치하십시오.
