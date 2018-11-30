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

# `datashield-mysql` 이미지 시작하기
{: #datashield-mysql_starter}

이 컨테이너 이미지는 Data Shield 환경에서 MariaDB를 실행하며 사용 중인 데이터에 대한 보호를 제공합니다.
{:shortdesc}

명령행을 사용하여 {{site.data.keyword.IBM}}에서 제공한 이미지에 액세스할 수 있습니다. [IBM 공용 이미지](/docs/services/Registry/registry_public_images.html#public_images)를 참조하십시오.
{: tip}

다음 Kubernetes 팟(Pod) 스펙을 사용하여 이미지를 배치할 수 있습니다.

```
    apiVersion: v1
    kind: Pod
    metadata:
      name: data-shield-mariadb
      labels:
        app: data-shield-mariadb
    spec:
      containers:
      - name: data-shield-mariadb
        image: <TODO INSERT APPROPRIATE IMAGE NAME HERE>
        volumeMounts:
        - mountPath: /dev/isgx
          name: isgx
        - mountPath: /dev/gsgx
          name: gsgx
        - mountPath: /var/run/aesmd/aesm.socket
          name: aesm-socket
        ports:
        - containerPort: 3306
          hostPort: 3306
          name: mysql
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
    
다음 명령을 실행하여 MariaDB 인스턴스를 연결할 수 있습니다. 

```
    mysql -h <node> --protocol tcp -uroot
```
{: pre}

Data Shield MariaDB 이미지는 클라이언트 연결에서 TLS를 사용해야 합니다. 클라이언트 버전에 따라 `--ssl` 또는 `--ssl-mode require`을 클라이언트 명령행에 추가해야 할 수 있습니다. 
