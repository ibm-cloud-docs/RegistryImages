---

copyright:
  years: 2018
lastupdated: "2018-05-25"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# ibmcloud-secure-perimeter-network 이미지 시작하기
{: #ibmcloud-secure-perimeter-network}

**ibmcloud-secure-perimeter-network** 이미지는 보안 경계 내 Vyatta 가상 라우터 어플라이언스의 구성을 자동화하기 위한 도구를 포함합니다.
{:shortdesc}

## 작동 방식
{: #how-it-works}

**ibmcloud-secure-perimeter-network**를 사용하면 보안 경계의 Vyatta 가상 라우터 어플라이언스의 구성을 자동화할 수 있습니다.

보안 경계에 대한 자세한 정보는 다음 블로그 기사에 있습니다.
  * [IBM Cloud에서 보안 경계 설정](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/)
  * [IBM Cloud에서 자동화된 보안 경계 설정](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

다음 두 가지 방법으로 **ibmcloud-secure-perimeter-network** 이미지를 사용할 수 있습니다.
-  **ibmcloud-secure-perimeter-network**를 Docker 컨테이너로 사용하여 보안 경계 방화벽 규칙 구성을 초기화하십시오.
-  **ibmcloud-secure-perimeter-network**를 Kubernetes 클러스터의 팟(Pod)으로 사용하여 보안 경계 세그먼트 VLAN에 작성된 새 서브넷의 IBM Cloud 인프라 계정을 폴링하고 Vyatta 방화벽 구성에 추가하십시오.

## 포함된 항목
{: #whats_included}

**ibmcloud-secure-perimeter-network** 이미지는 다음 소프트웨어 패키지를 제공합니다.
{:shortdesc}

-   Alpine Linux
-   Python 런타임
-   SoftLayer Python Client
-   Ansible

## 시작하기
{: #how_to_get_started}

다음 태스크를 검토하여 **ibmcloud-secure-perimeter-network** 사용 방법을 학습하십시오.

1.  [{{site.data.keyword.containerlong}}를 사용하여 보안 경계 내에서 Kubernetes 클러스터 프로비저닝](#provision_cluster)
2.  [보안 경계 Vyatta의 초기 구성 실행](#initial_setup)
3.  [보안 경계 내에서 Kubernetes 팟(Pod)으로 설정](#setup)
4.  [config.json 참조](#reference_config_json)
5.  [rules.conf 참조](#reference_rules_conf)

## {{site.data.keyword.containerlong}}를 사용하여 보안 경계 내에서 Kubernetes 클러스터 프로비저닝
{: #provision_cluster}

1.  IBM Cloud 카탈로그의 **컨테이너** 섹션에서 Kubernetes 클러스터를 프로비저닝하십시오.
2.  작성을 클릭하십시오.
3.  VLAN 드롭 다운 메뉴에서 보안 경계 세그먼트의 공용 및 사설 VLAN을 선택하십시오.
4.  적합하다고 여기는 다른 모든 세부사항을 입력하십시오.
5.  클러스터 작성을 클릭하십시오.

배치된 클러스터의 액세스를 확보하는 방법에 대한 [{{site.data.keyword.containerlong}}](../../../containers/container_index.html#container_index) 문서를 검토하십시오.

## 보안 경계 Vyatta의 초기 구성 실행
{: #initial_setup}

1. _config.json_ 파일을 작성하십시오. 이 파일에는 Vyatta에 액세스하기 위해 **ibmcloud-secure-perimeter-network**에 필요한 기본 매개변수가 있습니다.

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

  _config.json_을 채우는 방법에 대한 세부사항은 [config.json 참조 테이블](#reference_config_json)을 참조하십시오. 또한 이 파일은 [**ibmcloud-secure-perimeter-network**를 Kubernetes 팟(Pod)으로 설정](#setup) 프로세스에서 사용할 수 있습니다.

2. **ibmcloud-secure-perimeter-network**를 Docker 컨테이너로 실행하여 초기 설정을 시작하십시오.

  ```
  docker run registry.bluemix.net/ibm/ibmcloud-secure-perimeter-network:1.0.0 python config-secure-perimeter.py -v /path/to/current/dir:/opt/secure-perimeter
  ```
  {: pre}

  이렇게 하면 작업 디렉토리에 _state.json_ 파일이 작성됩니다. 이 파일은 [**ibmcloud-secure-perimeter-network**를 Kubernetes 팟(Pod)으로 설정](#setup)에 사용됩니다.

## 보안 경계 내에서 Kubernetes 팟(Pod)으로 설정
{: #setup}

**ibmcloud-secure-perimeter-network** 이미지에서 보안 경계의 서브넷을 관리하려면 Kubernetes 팟(Pod)을 사용하여 장기 프로세스로 실행할 수 있습니다. **ibmcloud-secure-perimeter-network**에는 Vyatta에 맞게 구성하기 위해 팟(Pod)에 복사해야 하는 여러 구성 파일 및 폴더가 있습니다.

1. _pvc.yaml_ 파일을 작성하십시오. 이 구성 파일은 팟(Pod)에 볼륨으로 마운트할 수 있는 지속적 볼륨 클레임(pvc)을 작성합니다.

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

2. pvc를 작성하십시오.

    ```
        kubectl apply -f restore-pvc.yaml
    ```
    {: pre}

3. _network-pod.yaml_ 파일을 작성하십시오. 이 구성 파일은 Kubernetes 클러스터에 **ibmcloud-secure-perimeter-network** 이미지를 팟(Pod)으로 배치하고 지속적 볼륨 클레임을 볼륨으로 마운트합니다.

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
          image: registry.<region>.bluemix.net/ibm/ibmcloud-secure-perimeter-network:1.0.0
          volumeMounts:
          - name: network-vol
            mountPath: /opt/secure-perimeter
        volumes:
        - name: network-vol
          PersistentVolumeClaim:
            claimName: network-pvc
  ```
  {: codeblock}

4. _rules.conf_ 파일을 작성하십시오. 이 구성 파일은 보안 경계에 화이트리스트로 지정할 공용 인터넷의 외부 서브넷과 포트를 **ibmcloud-secure-perimeter-network**에 알립니다.

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

5. **ibmcloud-secure-perimeter-network** 팟(Pod)에 파일을 복사하십시오.

  ```
  kubectl cp keys network-pod:/opt/secure-perimeter/
  kubectl cp state.json network-pod:/opt/secure-perimeter/state.json
  kubectl cp config.json network-pod:/opt/secure-perimeter/config.json
  kubectl cp rules.conf network-pod:/opt/secure-perimeter/rules.conf
  ```
  {: pre}

  _keys_ 디렉토리에는 Vyatta에 액세스하기 위해 **ibmcloud-secure-perimeter-network**에 필요한 SSH 키가 포함됩니다. SSH 키에 대한 자세한 정보는 [전제조건 절](#prerequisites)을 참조하십시오.

## config.json 참조
{: #reference_config_json}

|키|설명
|---|-------------|---|
|slid|IBM Cloud 인프라 사용자 이름
|apikey|IBM Cloud 인프라 API 키
|region|Vyatta가 배치된 IBM Cloud 지역
|inf_name_private|Vyatta 개인용 인터페이스의 이름
|inf_name_public|Vyatta 공용 인터페이스의 이름
|gatewayid|Vyatta 게이트웨이 ID
|vlans|유형, VLAN 번호 및 VLAN ID가 포함된 보안 경계 세그먼트 VLAN의 목록
|vyatta_gateway_vip|게이트웨이의 VIP
|vyatta_primary|기본 Vyatta 멤버의 사설 및 공인 IP가 포함된 오브젝트
|vyatta_secondary|보조 Vyatta 멤버의 사설 및 공인 IP가 포함된 오브젝트
{: caption="표 1. config.json" caption-side="top"}

## rules.conf 참조
{: #reference_rules_conf}

|키|설명
|---|-------------|---|
|external_subnets|보안 경계를 노출할 공용 인터넷의 서브넷 목록
|external_ports|보안 경계를 노출할 포트 목록
|userips|보안 경계에 화이트리스트로 지정할 사용자 IP의 목록
{: caption="표 2. rules.conf" caption-side="top"}

## 전제조건
{: #prerequisites}

-   Vyatta 및 VLAN은 IBM Cloud 인프라 포털을 통해 주문되었으며 VLAN은 Vyatta에 연관되어 있습니다.
-   자동화된 보안 경계 배치는 **ibmcloud-secure-perimeter-network**가 게이트웨이에 액세스하는 데 사용하는 SSH 키로 Vyatta를 미리 로드합니다. SSH 키는 보안 경계 설치 프로세스를 통해 또는 수동으로 로드해야 합니다. 자세한 정보는 [IBM Cloud에서 자동화된 보안 경계 설정](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/) 기사를 검토하십시오.
