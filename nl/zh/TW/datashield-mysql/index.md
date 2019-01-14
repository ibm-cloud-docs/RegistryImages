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

# 開始使用 `datashield-mysql` 映像檔
{: #datashield-mysql_starter}

此容器映像檔會在 Data Shield 環境中執行 MariaDB，並為使用中的資料提供保護。
{:shortdesc}

您可以使用指令行存取 {{site.data.keyword.IBM}} 所提供的映像檔，請參閱 [IBM 公用映像檔](/docs/services/Registry/registry_public_images.html#public_images)。
{: tip}

您可以使用下列 Kubernetes Pod 規格來部署映像檔：

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
    
您可以執行下列指令來連接至 MariaDB 實例：

```
    mysql -h <node> --protocol tcp -uroot
```
{: pre}

Data Shield MariaDB 映像檔需要用戶端連線使用 TLS。視您的用戶端版本而定，您可能需要在用戶端指令行新增 `--ssl` 或 `--ssl-mode require`。
