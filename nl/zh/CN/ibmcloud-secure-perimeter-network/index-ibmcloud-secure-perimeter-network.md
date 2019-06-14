---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-04"

keywords: IBM Cloud Container Registry, ibmcloud-secure-perimeter-network, container image, network, Secure Perimeter, public image

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

# 开始使用 `ibmcloud-secure-perimeter-network` 映像
{: #ibmcloud-secure-perimeter-network}

`ibmcloud-secure-perimeter-network` 映像包含用于自动配置 Secure Perimeter 中 Vyatta 虚拟路由器设备的工具。
{:shortdesc}

您可以使用命令行来访问 {{site.data.keyword.IBM}} 提供的映像，请参阅 [IBM 公共映像](/docs/services/Registry?topic=registry-public_images#public_images)。
{: tip}

## 工作原理
{: #spn_how-it-works}

利用 `ibmcloud-secure-perimeter-network`，您可以自动配置 Secure Perimeter 的 Vyatta 虚拟路由器设备。

有关 Secure Perimeter 的更多信息，请参阅以下博客文章：

- [Set up a Secure Perimeter in IBM Cloud ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/)。
- [Set up an automated Secure Perimeter in IBM Cloud ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/)。

您可以通过以下两种方式使用 `ibmcloud-secure-perimeter-network` 映像：

- 使用 `ibmcloud-secure-perimeter-network` 作为 Docker 容器以初始化 Secure Perimeter 防火墙规则配置。
- 使用 `ibmcloud-secure-perimeter-network` 作为 Kubernetes 集群上的 pod 以轮询在 Secure Perimeter Segment VLAN 上创建的新子网的 IBM Cloud 基础架构帐户并将它们添加到 Vyatta 防火墙配置。

## 所含内容
{: #spn_whats_included}

`ibmcloud-secure-perimeter-network` 映像提供以下软件包。
{:shortdesc}

- Alpine Linux
- Python 运行时
- SoftLayer Python 客户机
- Ansible

## 先决条件
{: #spn_prerequisites}

- Vyatta 和 VLAN 从 IBM Cloud 基础架构门户网站订购，并且 VLAN 关联到 Vyatta。
- 自动化的 Secure Perimeter 部署通过 `ibmcloud-secure-perimeter-network` 用于访问网关的 SSH 密钥预装入 Vyatta。必须手动或者通过 Secure Perimeter 安装过程装入 SSH 密钥。有关更多信息，请参阅 [Set up an automated Secure Perimeter in IBM Cloud ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/)。

## 使用 {{site.data.keyword.containerlong_notm}} 在 Secure Perimeter 中供应 Kubernetes 集群
{: #spn_provision_cluster}

1. 从 IBM Cloud 目录中的**容器**部分供应 Kubernetes 集群。
2. 单击**创建**。
3. 从 VLAN 列表中选择 Secure Perimeter Segment 公用和专用 VLAN。
4. 根据需要输入其他所有详细信息。
5. 单击**创建集群**。

要在集群部署后对其进行访问，请参阅 [{{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-getting-started#getting-started)。

## 运行 Secure Perimeter Vyatta 的初始配置
{: #spn_initial_setup}

1. 创建名为 `config.json` 的文件。此文件包含 `ibmcloud-secure-perimeter-network` 访问 Vyatta 所需的基本参数。

   ```
   {
     "slid": "XXXX",
    "apikey": "XXXX",
    "region": "XXXX",
    "inf_name_private": "dp0bond0",
    "inf_name_public": "dp0bond1",
    "gatewayid": "XXXX",
    "vlans": [
      {
        "type": "XXXX",
        "vlan_num": XXXX,
        "vlanid": XXXX
      },
      ...
     ],
    "vyatta_gateway_vip": "X.X.X.X",
    "vyatta_primary": {
      "private_ip": "X.X.X.X",
      "public_ip": "X.X.X.X"
    },
    "vyatta_secondary": {
      "private_ip": "X.X.X.X",
      "public_ip": "X.X.X.X"
    }
  }
   ```
   {: codeblock}

   有关如何填充 `config.json` 的更多信息，请参阅 [`config.json` 参考表](#spn_reference_config_json)。此文件也可用于[将 `ibmcloud-secure-perimeter-network` 设置为 Kubernetes pod](#spn_setup) 的过程。

2. 作为 Docker 容器运行 `ibmcloud-secure-perimeter-network` 以开始初始设置。

   ```
   docker run icr.io/ibm/ibmcloud-secure-perimeter-network:1.0.0 python config-secure-perimeter.py -v /path/to/current/dir:/opt/secure-perimeter
   ```
   {: pre}

   此操作将在工作目录中创建文件 `state.json`。此文件用于[将 `ibmcloud-secure-perimeter-network` 设置为 Kubernetes pod](#spn_setup)。

## 在 Secure Perimeter 中设置 Kubernetes pod
{: #spn_setup}

如果希望 `ibmcloud-secure-perimeter-network` 映像管理 Secure Perimeter 上的子网，可使用 Kubernetes pod 将其作为长期存在的进程运行。要为 Vyatta 配置 pod，必须从 `ibmcloud-secure-perimeter-network` 映像中复制多个配置文件和文件夹到 pod 以针对 Vyatta 进行配置：

1. 创建名为 `pvc.yaml` 的文件。此配置文件会创建持久性卷申领 (PVC)，可将此 PVC 作为卷安装到 pod。

   ```
  apiVersion: v1
  kind: PersistentVolumeClaim
  metadata:
    name: network-pvc
    annotations:
      volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
  spec:
    accessModes:
      - ReadWriteMany
    resources:
      requests:
        storage: 20Gi
  ```
   {: codeblock}

2. 创建 PVC。

   ```
    kubectl apply -f restore-pvc.yaml
    ```
   {: pre}

3. 创建名为 `network-pod.yaml` 的文件。此配置文件将 `ibmcloud-secure-perimeter-network` 映像作为 pod 部署在 Kubernetes 集群中，并作为卷安装持久性卷申领。

   ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: network-pod
     labels:
       app: network-pod
   spec:
     template:
       spec:
         containers:
         - name: network-pod
           image: icr.io/ibm/ibmcloud-secure-perimeter-network:1.0.0
           volumeMounts:
           - name: network-vol
             mountPath: /opt/secure-perimeter
         volumes:
         - name: network-vol
           PersistentVolumeClaim:
             claimName: network-pvc
   ```
   {: codeblock}

4. 创建名为 `rules.conf` 的文件。此配置文件告知 `ibmcloud-secure-perimeter-network` 将公用因特网中的哪些外部子网和端口列入到 Secure Perimeter 白名单。

   ```
   {
       "external_subnets": [
        "X.X.X.X/X",
        "X.X.X.X/X"
      ],
      "external_ports": [
        "XX",
        "XX"
      ],
      "userips": [
        "X.X.X.X",
        "X.X.X.X"
      ]
  }
   ```
   {: codeblock}

5. 将文件复制到 `ibmcloud-secure-perimeter-network` pod。

   ```
  kubectl cp keys network-pod:/opt/secure-perimeter/
  kubectl cp state.json network-pod:/opt/secure-perimeter/state.json
  kubectl cp config.json network-pod:/opt/secure-perimeter/config.json
  kubectl cp rules.conf network-pod:/opt/secure-perimeter/rules.conf
  ```
   {: pre}

   目录 `keys` 包含 `ibmcloud-secure-perimeter-network` 访问 Vyatta 所需的 SSH 密钥。有关 SSH 密钥的更多信息，请参阅[先决条件部分](#spn_prerequisites)。

## `config.json` 参考
{: #spn_reference_config_json}

|键|描述
|---|-------------|---|
|`slid`|您的 IBM Cloud 基础架构用户名
|`apikey`|您的 IBM Cloud 基础架构 API 密钥|`region`|部署 Vyatta 的 IBM Cloud 区域
|`inf_name_private`|Vyatta 专用接口的名称
|`inf_name_public`|Vyatta 公共接口的名称
|`gatewayid`|Vyatta 网关标识
|`vlans`|包含类型、VLAN 编号和 VLAN 标识的 Secure Perimeter Segment VLAN 的列表
|`vyatta_gateway_vip`|网关 VIP
|`vyatta_primary`|包含主 Vyatta 成员的专用 IP 和公共 IP 的对象
|`vyatta_secondary`|包含辅助 Vyatta 成员的专用 IP 和公共 IP 的对象
{: caption="表 1. <code>config.json</code>" caption-side="top"}

## `rules.conf` 参考
{: #spn_reference_rules_conf}

|键|描述
|---|-------------|---|
|`external_subnets`|Secure Perimeter 可以使用的公用因特网上子网的列表
|`external_ports`|Secure Perimeter 可以使用的端口的列表
|`userips`|要列入 Secure Perimeter 白名单的用户 IP 的列表
{: caption="表 2. <code>rules.conf</code>" caption-side="top"}
