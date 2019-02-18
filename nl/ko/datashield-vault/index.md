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

# `datashield-vault` 이미지 시작하기
{: #datashield-vault_starter}

이 컨테이너 이미지는 Data Shield 환경에서 볼트를 실행하며 사용 중인 데이터에 대한 보호를 제공합니다.
{:shortdesc}

명령행을 사용하여 {{site.data.keyword.IBM}}에서 제공한 이미지에 액세스할 수 있습니다. [IBM 공용 이미지](/docs/services/Registry/registry_public_images.html#public_images)를 참조하십시오.
{: tip}

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
        image: <TODO INSERT APPROPRIATE IMAGE NAME HERE>
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

`kubectl get pod -owide` 또는 `kubectl describe pod`을 사용하여 볼트 인스턴스에 대한 노드 IP 주소를 검색하십시오. Data Shield 보호 볼트 인스턴스에 액세스하려면 `볼트_ADDR` 환경 변수를 `http://<IP>:8200`으로 설정하십시오.

`vault init`, `vault unseal`, `vault auth`, `vault write` 및 `vault read`와 같은 vault CLI 명령을 사용하여 시크릿을 인증하고 액세스할 수 있습니다. Vault CLI 사용 방법에 대한 자세한 정보는 [Vault Commands (CLI) ![외부 링크 아이콘](../../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.vaultproject.io/docs/commands/index.html)를 참조하십시오.
