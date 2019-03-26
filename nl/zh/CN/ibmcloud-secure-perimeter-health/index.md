---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-21"

keywords: IBM Cloud Container Registry, ibmcloud-secure-perimeter-health, container image, health, Secure Perimeter, scan, public image

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

# 开始使用 `ibmcloud-secure-perimeter-health` 映像
{: #ibmcloud-secure-perimeter-health}

`ibmcloud-secure-perimeter-health` 映像包含用于扫描 {{site.data.keyword.cloud}} 上 Secure Perimeter 中的漏洞的工具。
{:shortdesc}

您可以使用命令行来访问 {{site.data.keyword.IBM_notm}} 提供的映像，请参阅 [IBM 公共映像](/docs/services/Registry?topic=registry-public_images#public_images)。
{: tip}

## 工作原理
{: #sph_how-it-works}

要确保 Secure Perimeter 运行正常，可以使用 `ibmcloud-secure-perimeter-health` 来扫描 {{site.data.keyword.cloud_notm}} 基础架构帐户中的公用或专用网络，然后查看漏洞报告。您可以通过以下两种方式使用 **ibmcloud-secure-perimeter-health** 映像：

- 将 `ibmcloud-secure-perimeter-health` 用作 Secure Perimeter 中 Kubernetes 集群上的 pod 以扫描专用网络暴露。
- 将 `ibmcloud-secure-perimeter-health` 用作工作站上的独立 Docker 容器以扫描公用网络暴露。

可在以下博客文章中找到有关 Secure Perimeter 的更多信息：

- [Set up a Secure Perimeter in {{site.data.keyword.cloud_notm}} ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/)。
- [Set up an automated Secure Perimeter in {{site.data.keyword.cloud_notm}} ![外部链接图标](../../../icons/launch-glyph.svg "外部链接图标")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/)。

扫描后，`ibmcloud-secure-perimeter-health` 映像生成有关可从 Secure Perimeter Segment 访问的网络的报告。每个报告详述网络网关、VLAN、其子网和任何违规主机的名称。以下是扫描了专用网络漏洞的用户示例报告：

```
#-------- Running Secure Perimeter exposure scan 2018-05-24 12:00:00 --------#

RESULTS:

sp-gateway-af6053a9:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   FAIL
			host = 10.73.84.198, ports = [179, 22]
		10.73.63.8/29:     PASS
		10.73.72.128/26:   PASS
sp-gateway-8a9031ab:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   PASS
		10.73.63.8/29:     PASS
```
{: screen}

## 所含内容
{: #sph_whats_included}

`ibmcloud-secure-perimeter-health` 映像提供以下软件包。
{:shortdesc}

- Alpine Linux
- Python 运行时
- SoftLayer Python 客户机
- Nmap 端口扫描程序

## 使用 {{site.data.keyword.containerlong_notm}} 在 Secure Perimeter 中供应 Kubernetes 集群
{: #sph_provision_cluster}

1. 从 {{site.data.keyword.cloud_notm}}“目录”中的**容器**部分供应 Kubernetes 集群。
2. 单击**创建**。
3. 从 VLAN 下拉菜单中选择 Secure Perimeter Segment 公用和专用 VLAN。
4. 根据需要输入其他所有详细信息。
5. 单击**创建集群**。

查看 [{{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-container_index#container_index) 文档以了解如何在部署后获取集群的访问权。

## 扫描 Secure Perimeter 中的专用网络
{: #sph_private_networks}

从 `ibmcloud-secure-perimeter-health` 映像创建容器 pod，并设置例程扫描。

**开始之前**

- 安装必需的 [CLI](/docs/containers?topic=containers-cs_cli_install#cs_cli_install)。
- [设定 CLI 的目标](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)为集群。

1. 创建名为 _health-pod.yaml_ 的配置文件。此文件创建容器 pod 的高可用性部署。

    ```
apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: health-pod
      labels:
        app: health-pod
    spec:
      replicas: 1
      selector:
        app: health-pod
      template:
        spec:
          containers:
          - name: health-pod
            image: registry.bluemix.net/ibm/ibmcloud-secure-perimeter-health:1.0.0
            args:
            - /usr/local/bin/python
            - /run.py
            - --scan
            - private
            - --exclude-vlan-ids
            - <Private Secure Perimeter Segment VLAN ID>
            - --poll-interval
            - 1800
            env:
            - name: SL_USER
              value: <IBM Cloud infrastructure user name>
            - name: SL_APIKEY
              value: <IBM Cloud infrastructure api key>
    ```
    {: codeblock}
    
2. 创建部署。

    ```
kubectl apply -f health-pod.yaml
    ```
    {: pre}

3. 确认 pod 正在运行。

    ```
    kubectl get pods
    ```
    {: pre}

    ```
NAME                                    READY     STATUS    RESTARTS   AGE
    health-pod-<random-id>                  1/1       Running   0          1hr
    ```
    {: screen}

## 扫描 Secure Perimeter 外部的公用网络
{: #sph_public_networks}

从 `ibmcloud-secure-perimeter-health` 映像创建 Docker 容器，并扫描公用网络。

**开始之前**

- 安装 Docker。

1. 从自己的工作站创建 Docker 容器，如下所示：

    ```
    docker run -it -e SL_USER='$SL_USER' -e SL_APIKEY='$SL_APIKEY' registry.bluemix.net/ibm/ibmcloud-secure-perimeter-health:1.0.0 /usr/local/bin/python run.py --scan public --allowed-public-ports 80 443 9000-9999
    ```
    {: pre}

2. 在容器生成报告后，查看[分析扫描结果](#sph_scan_results)部分以了解结果。

## 分析扫描结果
{: #sph_scan_results}

`ibmcloud-secure-perimeter-health` 生成有关 Secure Perimeter 的运行状况的格式化报告：

```
#-------- Running Secure Perimeter exposure scan 2018-05-24 12:00:00 --------#

RESULTS:

sp-gateway-af6053a9:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   FAIL
			host = 10.73.84.198, ports = [179, 22]
		10.73.63.8/29:     PASS
		10.73.72.128/26:   PASS
sp-gateway-8a9031ab:
	sps-priv-3deb5748:
		10.73.71.168/29:   PASS
	prv-neb1-cc4ee985:
		10.73.84.192/29:   PASS
		10.73.63.8/29:     PASS
```
{: screen}

报告的格式如下所示：

```
<gateway name>:
  <vlan name>:
    <subnet>:    PASS
    <subnet>:    FAIL
      host = <ip address>, ports = [<exposed port>, <exposed port>, ...]
```
{: screen}

如果子网中无可访问的主机，`ibmcloud-secure-perimeter-health`将该子网确定为 `PASS`，否则其返回 `FAIL` 并列出可访问的主机，以及可访问的端口。

## 容器自变量参考
{: #sph_reference_container_arg}

|键|描述|缺省值
|---|-------------|---|
|scan|暴露扫描的类型（“public”或“private”）|无（全部扫描）
|exclude-vlan-ids|按标识列出的要避免扫描的 VLAN 列表|无
|poll-interval|设置直至下一次扫描的秒数|0（立即运行）
|allowed-public-ports|要列入扫描白名单的端口列表|80, 443, 9000-9999
{: caption="表 1. 容器自变量" caption-side="top"}

## 环境变量参考
{: #sph_reference_env_var}

|键|描述|
|---|-------------|
|SL_USER|您的 IBM Cloud 基础架构用户名|
|SL_APIKEY|您的 IBM Cloud 基础架构 API 密钥|
{: caption="表 2. 环境变量" caption-side="top"}
