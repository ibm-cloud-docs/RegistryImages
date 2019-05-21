---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-09"

keywords: IBM Cloud Container Registry, Data Shield, data in use, memory encryption, intel sgx, fortanix, mysql image, mariaDB, container image, public image

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

# Introdução à imagem `datashield-mariadb`
{: #datashield-mariadb_starter}

Essa imagem de contêiner fornece proteção para os dados em uso ao executar o MariaDB no ambiente do {{site.data.keyword.datashield_short}}. Para obter mais informações sobre o serviço e o que significa proteger dados em uso, consulte a [Documentação do {{site.data.keyword.datashield_short}}](/docs/services/data-shield?topic=data-shield-about#about).
{: shortdesc}

É possível acessar as imagens que são fornecidas pela {{site.data.keyword.IBM}} usando a linha de comandos. Consulte [Imagens públicas da IBM](/docs/services/Registry?topic=registry-public_images#public_images).
{: tip}

## Implementando a imagem
{: #datashield-mariadb-deploy}

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
    image: <IMAGE_NAME>
    env:
      name: MARIADB_ROOT_PASSWORD
      value: <ROOT_PASSWORD>
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
      name: mariadb
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
  
<table>
<caption>Tabela 1. Variáveis de entrada necessárias</caption>
  <tr>
    <th>Consolidação</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>O nome da imagem que você deseja implementar.</td>
  </tr>
    <tr>
    <td><code>MARIADB_ROOT_PASSWORD</code></td>
    <td>Configura a senha para o usuário raiz do MariaDB.</td>
  </tr>
  <tr>
    <td><code>ROOT_PASSWORD</code></td>
    <td>A senha que você deseja configurar para o usuário raiz do MariaDB.</td>
  </tr>
</table>

### Variáveis de ambiente aceitas
{: #datashield-mariadb-variables}

O contêiner do MariaDB também aceita as variáveis de ambiente a seguir. Para usar outra variável, inclua-a na especificação, conforme mostrado no exemplo a seguir:

```
spec:
  containers:
  - name: data-shield-mariadb
    image: <IMAGE_NAME>
    env:
      name: MARIADB_ROOT_PASSWORD
      value: <ROOT_PASSWORD>
      name: MARIADB_USER
      value: <USER_NAME>
```
{: screen}

<table>
<caption>Tabela 2. As variáveis de ambiente aceitas pelo contêiner do MariaDB</caption>
  <tr>
    <th>Consolidação</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td><code>MARIADB_ALLOW_EMPTY_PASSWORD</code></td>
    <td>Permite que o banco de dados inicie com uma senha vazia para o usuário raiz. Não recomendado.</td>
  </tr>
  <tr>
    <td><code>MARIADB_RANDOM_ROOT_PASSWORD</code></td>
    <td>Gera uma senha aleatória para o usuário raiz do MariaDB.</td>
  </tr>
  <tr>
    <td><code>MARIADB_USER</code></td>
    <td>Cria um usuário regular do MariaDB com o nome de usuário especificado.</td>
  </tr>
  <tr>
    <td><code>MARIADB_PASSWORD</code></td>
    <td>Senha para <code>MYSQL_USER</code>.</td>
  </tr>
  <tr>
    <td><code>MARIADB_DATABASE</code></td>
    <td>Cria um banco de dados vazio com o nome especificado.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_MODE</code></td>
    <td>Permite replicação. As opções incluem <code>master</code> ou <code>slave</code>.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_USER</code> (principal ou escravo)</td>
    <td>Configura o nome do usuário de replicação. Esse usuário é criado no mestre e é usado pelo escravo para se
conectar.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_PASSWORD</code> (principal ou escravo)</td>
    <td>Configura a senha para o usuário de replicação. Essa senha é usada no mestre para criar o usuário e pelo escravo
para efetuar login.</td>
  </tr>
  <tr>
    <td><code>MARIADB_REPLICATION_MASTER_HOST</code> (escravo)</td>
    <td>Configura o nome do host ao qual se conectar para replicação.</td>
  </tr>
</table>

## Conectando-se à instância do MariaDB
{: #datashield-mariadb-connect}

É possível conectar-se à instância do MariaDB executando o comando a seguir:

```
mariadb -h <node> --protocol tcp -uroot
```
{: codeblock}

A imagem do {{site.data.keyword.datashield_short}} MariaDB requer que as conexões do cliente usem a Segurança da Camada de Transporte (TLS). Dependendo da versão do cliente, talvez seja necessário incluir `--ssl` ou `--ssl-mode require` em sua linha de comandos.
{: note}
