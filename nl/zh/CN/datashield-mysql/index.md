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

# 开始使用 `datashield-mysql` 映像
{: #datashield-mysql_starter}

此容器映像会在 Data Shield 环境中运行 MariaDB，为使用中的数据提供保护。
{:shortdesc}

您可以使用命令行来访问 {{site.data.keyword.IBM}} 提供的映像，请参阅 [IBM 公共映像](/docs/services/Registry/registry_public_images.html#public_images)。
{: tip}

您可以使用以下 Kubernetes pod 规范来部署映像：

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
    
您可以通过运行以下命令连接到 MariaDB 实例：

```
    mysql -h <node> --protocol tcp -uroot
```
{: pre}

Data Shield MariaDB 映像要求客户机连接必须使用 TLS。您可能需要将 `--ssl` 或 `--ssl-mode require` 添加到客户机命令行，具体取决于您的客户机版本。
