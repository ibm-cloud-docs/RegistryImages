---

copyright:
  years: 2018, 2019
lastupdated: "2019-01-03"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Introdução à imagem `datashield-mysql`
{: #datashield-mysql_starter}

Essa imagem de contêiner executa o MariaDB no ambiente Data Shield, fornecendo proteção aos dados em uso.
{:shortdesc}

É possível acessar as imagens que são fornecidas pela {{site.data.keyword.IBM}} usando a linha de comandos. Consulte [Imagens públicas da IBM](/docs/services/Registry/registry_public_images.html#public_images).
{: tip}

É possível usar a seguinte especificação de pod do Kubernetes para implementar a imagem:

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

É possível conectar-se à instância do MariaDB executando o comando a seguir:

```
    mysql -h <node> --protocol tcp -uroot
```
{: pre}

A imagem do MariaDB do Data Shield requer que as conexões do cliente usem o TLS. Dependendo da versão do cliente, talvez seja necessário incluir `--ssl` ou `--ssl-mode require` em sua linha de comandos.
