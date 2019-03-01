---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-21"

keywords: ibmcloud-secure-perimeter-health, container image, health, Secure Perimeter, scan, public image

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

# `ibmcloud-secure-perimeter-health` 이미지 시작하기
{: #ibmcloud-secure-perimeter-health}

`ibmcloud-secure-perimeter-health` 이미지에 {{site.data.keyword.cloud}}의 보안 경계에서 취약성을 스캔하기 위한 도구가 포함되어 있습니다.
{:shortdesc}

명령행을 사용하여 {{site.data.keyword.IBM_notm}}에서 제공한 이미지에 액세스할 수 있습니다. [IBM 공용 이미지](/docs/services/Registry?topic=registry-public_images#public_images)를 참조하십시오.
{: tip}

## 작동 방식
{: #sph_how-it-works}

보안 경계가 올바르게 작동하는지 확인하기 위해, `ibmcloud-secure-perimeter-health`에서 {{site.data.keyword.cloud_notm}} 인프라 계정의 공용 또는 사설 네트워크를 스캔하고 취약성을 보고할 수 있습니다. 다음 두 가지 방법으로 **ibmcloud-secure-perimeter-health** 이미지를 사용할 수 있습니다.

- 보안 경계 내 Kubernetes 클러스터에서 `ibmcloud-secure-perimeter-health`를 팟(Pod)으로 사용하여 사설 네트워크 노출을 스캔하십시오.
- 워크스테이션에서 `ibmcloud-secure-perimeter-health`를 독립형 Docker 컨테이너로 사용하여 사설 네트워크 노출을 스캔하십시오.

보안 경계에 대한 자세한 정보는 다음 블로그 기사에 있습니다.

- [Set up a Secure Perimeter in {{site.data.keyword.cloud_notm}} ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/).
- [Set up an automated Secure Perimeter in {{site.data.keyword.cloud_notm}} ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

스캔 후 `ibmcloud-secure-perimeter-health` 이미지는 보안 경계 세그먼트에서 도달할 수 있는 네트워크에 대한 보고서를 생성합니다. 각 보고서에서는 네트워크 게이트웨이의 이름, VLAN, 해당 서브넷 및 문제 호스트에 대해 자세히 설명합니다. 사설 네트워크 취약성을 스캔한 사용자의 보고서 예는 다음과 같습니다.

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

## 포함된 항목
{: #sph_whats_included}

`ibmcloud-secure-perimeter-health` 이미지는 다음 소프트웨어 패키지를 제공합니다.
{:shortdesc}

- Alpine Linux
- Python runtime
- SoftLayer Python Client
- Nmap port scanner

## {{site.data.keyword.containerlong_notm}}를 사용하여 보안 경계 내에서 Kubernetes 클러스터 프로비저닝
{: #sph_provision_cluster}

1. {{site.data.keyword.cloud_notm}} 카탈로그의 **컨테이너** 섹션에서 Kubernetes 클러스터를 프로비저닝하십시오.
2. **작성**을 클릭하십시오.
3. VLAN 드롭 다운 메뉴에서 보안 경계 세그먼트의 공용 및 사설 VLAN을 선택하십시오.
4. 필요에 따라 다른 모든 세부사항을 입력하십시오.
5. **클러스터 작성**을 클릭하십시오.

클러스터가 배치된 후 클러스터에 대한 액세스를 확보하는 방법에 대한 [{{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-container_index#container_index) 문서를 검토하십시오.

## 보안 경계 내에서 사설 네트워크 스캔
{: #sph_private_networks}

`ibmcloud-secure-perimeter-health` 이미지에서 컨테이너 팟(Pod)을 작성하고 루틴 스캔을 설정하십시오.

**시작하기 전에**

- 필수 [CLI](/docs/containers?topic=containers-cs_cli_install#cs_cli_install)를 설치하십시오.
- 클러스터를 [CLI의 대상으로 지정](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)하십시오.

1. 구성 파일 _health-pod.yaml_을 작성하십시오. 이 파일은 컨테이너 팟(Pod)의 고가용성 배치를 작성합니다.

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
    
2. 배치를 작성하십시오.

    ```
kubectl apply -f health-pod.yaml
    ```
    {: pre}

3. 팟(Pod)이 실행 중인지 확인하십시오.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
NAME                                    READY     STATUS    RESTARTS   AGE
    health-pod-<random-id>                  1/1       Running   0          1hr
    ```
    {: screen}

## 보안 경계 외부에서 공용 네트워크 스캔
{: #sph_public_networks}

`ibmcloud-secure-perimeter-health` 이미지에서 Docker 컨테이너를 작성하고 공용 네트워크를 스캔하십시오.

**시작하기 전에**

- Docker를 설치하십시오.

1. 다음과 같이 고유 워크스테이션에서 Docker 컨테이너 작성:

    ```
    docker run -it -e SL_USER='$SL_USER' -e SL_APIKEY='$SL_APIKEY' registry.bluemix.net/ibm/ibmcloud-secure-perimeter-health:1.0.0 /usr/local/bin/python run.py --scan public --allowed-public-ports 80 443 9000-9999
    ```
    {: pre}

2. 컨테이너가 보고서를 생성한 후 [스캔 결과 분석](#sph_scan_results) 섹션을 검토하여 결과를 파악하십시오.

## 스캔 결과 분석
{: #sph_scan_results}

`ibmcloud-secure-perimeter-health`는 보안 경계의 작동 상태에 대한 형식화된 보고서를 생성합니다.

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

보고서의 형식은 다음과 같습니다.

```
<gateway name>:
  <vlan name>:
    <subnet>:    PASS
    <subnet>:    FAIL
      host = <ip address>, ports = [<exposed port>, <exposed port>, ...]
```
{: screen}

서브넷의 호스트에 연결할 수 없는 경우 `ibmcloud-secure-perimeter-health`는 서브넷을 `PASS`로 판별하고, 그렇지 않은 경우 `FAIL`과 함께 리턴되며 액세스 가능한 포트와 함께 연결 가능한 호스트를 나열합니다.

## 컨테이너 인수 참조
{: #sph_reference_container_arg}

|키|설명|기본값
|---|-------------|---|
|scan|노출 스캔의 유형("public" 또는 "private") |없음(둘 다 스캔)
|exclude-vlan-ids|스캔을 피하기 위한 ID별 VLAN 목록|없음
|poll-interval|다음 스캔 때까지 시간(초) 설정|0(한 번 실행)
|allowed-public-ports|스캔에 따라 화이트리스트로 지정할 포트 목록|80, 443, 9000 - 9999
{: caption="표 1. 컨테이너 인수" caption-side="top"}

## 환경 변수 참조
{: #sph_reference_env_var}

|키|설명|
|---|-------------|
|SL_USER|IBM Cloud 인프라 사용자 이름|
|SL_APIKEY|IBM Cloud 인프라 API 키|
{: caption="표 2. 환경 변수" caption-side="top"}
