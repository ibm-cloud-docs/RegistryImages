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

# Introdução à imagem `datashield-vault`
{: #datashield-vault_starter}


Essa imagem de contêiner executa uma área segura no ambiente Data Shield, fornecendo proteção aos dados em uso.
{:shortdesc}

É possível acessar as imagens que são fornecidas pela {{site.data.keyword.IBM}} usando a linha de comandos. Consulte [Imagens públicas da IBM](/docs/services/Registry/registry_public_images.html#public_images).
{: tip}

É possível usar a seguinte especificação de pod do Kubernetes para implementar a imagem:

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
    
Use `kubectl get pod -owide` ou `kubectl describe pod` para consultar o endereço IP do nó para a instância da área segura. Configure a variável de ambiente `VAULT_ADDR` para `http://<IP>:8200` para acessar a instância da área segura protegida pelo Data Shield.

É possível usar os comandos da CLI da área segura como `vault init`, `vault unseal`, `vault auth`, `vault write` e `vault read` para autenticar e acessar os segredos. Para obter mais informações sobre como usar a CLI da área segura, consulte [Comando da área segura (CLI) ![Ícone de link externo](../../../icons/launch-glyph.svg "Ícone de link externo")](https://www.vaultproject.io/docs/commands/index.html).
