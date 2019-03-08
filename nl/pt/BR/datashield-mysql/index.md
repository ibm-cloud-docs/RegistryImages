---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-26"

keywords: mysql image, Data Shield environment, container image, public image

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

# Introdução à imagem `datashield-mysql`
{: #datashield-mysql_starter}

Essa imagem de contêiner executa o MariaDB no ambiente Data Shield, fornecendo proteção aos dados em uso.
{:shortdesc}

É possível acessar as imagens que são fornecidas pela {{site.data.keyword.IBM}} usando a linha de comandos. Consulte [Imagens públicas da IBM](/docs/services/Registry?topic=registry-public_images#public_images).
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

Consulte a tabela a seguir para ver as variáveis de ambiente aceitas pelo contêiner do MariaDB:

<table>
<caption>Tabela 1. As variáveis de ambiente aceitas pelo contêiner do MariaDB</caption>
  <tr>
    <th>Consolidação</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td><code>MYSQL_ROOT_PASSWORD</code></td>
    <td>Configura a senha para o usuário raiz do MariaDB.</td>
  </tr>
  <tr>
    <td><code>MYSQL_ALLOW_EMPTY_PASSWORD</code></td>
    <td>Permite que o banco de dados inicie com uma senha vazia para o usuário raiz. Não recomendado.</td>
  </tr>
  <tr>
    <td><code>MYSQL_RANDOM_ROOT_PASSWORD</code></td>
    <td>Gera uma senha aleatória para o usuário raiz do MariaDB.</td>
  </tr>
  <tr>
    <td><code>MYSQL_USER</code></td>
    <td>Cria um usuário do MariaDB regular com o nome de usuário especificado.</td>
  </tr>
  <tr>
    <td><code>MYSQL_PASSWORD</code></td>
    <td>Senha para <code>MYSQL_USER</code>.</td>
  </tr>
  <tr>
    <td><code>MYSQL_DATABASE</code></td>
    <td>Cria um banco de dados vazio com o nome especificado.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MODE</code></td>
    <td>Permite replicação. As opções incluem <code>master</code> ou <code>slave</code>.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_USER</code> (mestre ou escravo)</td>
    <td>Configura o nome do usuário de replicação. Esse usuário é criado no mestre e é usado pelo escravo para se
conectar.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_PASSWORD</code> (mestre ou escravo)</td>
    <td>Configura a senha para o usuário de replicação. Essa senha é usada no mestre para criar o usuário e pelo escravo
para efetuar login.</td>
  </tr>
  <tr>
    <td><code>MYSQL_REPLICATION_MASTER_HOST</code> (escravo)</td>
    <td>Configura o nome do host ao qual se conectar para replicação.</td>
  </tr>
</table>
