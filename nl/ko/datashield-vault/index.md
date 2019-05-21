---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-09"

keywords: IBM Cloud Container Registry, Data Shield environment, container image, public image, vault image, data in use, memory encryption, intel sgx, fortanix,

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

# `datashield-vault` 이미지 시작하기
{: #datashield-vault_starter}

이 컨테이너 이미지는 {{site.data.keyword.datashield_short}} 환경에서 Vault를 실행하여 사용 중인 데이터를 보호합니다. 서비스 및 "사용 중인 데이터"를 보호하는 것의 의미에 대한 자세한 정보는 [{{site.data.keyword.datashield_short}} 문서](/docs/services/data-shield?topic=data-shield-about#about)를 참조하십시오.
{: shortdesc}

명령행을 사용하여 {{site.data.keyword.IBM}}에서 제공한 이미지에 액세스할 수 있습니다. [IBM 공용 이미지](/docs/services/Registry?topic=registry-public_images#public_images)를 참조하십시오.
{: tip}


## 이미지 배치
{: #datashield-vault-deploy}

다음 Kubernetes 팟(Pod) 스펙을 사용하여 이미지를 배치할 수 있습니다.

```
    apiVersion: v1
    kind: Pod
    metadata:
      name: data-shield-vault
      labels:
        app: data-shield-vault
    spec:
      containers:
      - name: data-shield-vault
        image: <IMAGE_NAME>
        volumeMounts:
        - mountPath: /dev/isgx
          name: isgx
        - mountPath: /dev/gsgx
          name: gsgx
        - mountPath: /var/run/aesmd/aesm.socket
          name: aesm-socket
        ports:
        - containerPort: 8200
          hostPort: 8200
          name: vault
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
<caption>표 1. 필수 입력 변수</caption>
  <tr>
    <th>변수</th>
    <th>설명</th>
  </tr>
  <tr>
    <td><code>IMAGE_NAME</code></td>
    <td>배치하려는 이미지 이름입니다.</td>
  </tr>
</table>

Vault의 보호된 인스턴스에 액세스하려면 다음을 실행하십시오.

1. 다음 명령 중 하나를 실행하여 인스턴스의 노드 IP 주소를 찾으십시오.

  * 옵션 1:

    ```
    kubectl get pod -owide
    ```
    {: codeblock}

  * 옵션 2:
    ```
    kubectl describe pod
    ```
    {: codeblock}

2. `VAULT_ADDR` 환경 변수를 `http://<YOUR_IP>:8200`으로 설정하십시오.
  

`kubectl get pod -owide` 또는 `kubectl describe pod`을 사용하여 볼트 인스턴스에 대한 노드 IP 주소를 검색하십시오. {{site.data.keyword.datashield_short}} 보호 Vault 인스턴스에 액세스하려면 `VAULT_ADDR` 환경 변수를 `http://<IP>:8200`으로 설정하십시오.

`vault init`, `vault unseal`, `vault auth`, `vault write` 및 `vault read`와 같은 Vault CLI 명령을 사용하여 시크릿을 인증하고 액세스할 수 있습니다. Vault CLI 사용 방법에 대한 자세한 정보는 [Vault Commands (CLI) ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.vaultproject.io/docs/commands/index.html)를 참조하십시오.
{: tip}
