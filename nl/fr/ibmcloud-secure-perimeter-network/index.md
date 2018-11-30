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

# Initiation à l'image `ibmcloud-secure-perimeter-network`
{: #ibmcloud-secure-perimeter-network}

L'image `ibmcloud-secure-perimeter-network` contient des outils pour l'automatisation de la configuration des dispositifs de routeur virtuel Vyatta au sein d'un paramètre sécurisé.
{:shortdesc}

Vous pouvez accéder aux images fournies par {{site.data.keyword.IBM}} à l'aide de la ligne de commande. Voir [Images IBM publiques](/docs/services/Registry/registry_public_images.html#public_images).
{: tip}

## Fonctionnement
{: #how-it-works}

`ibmcloud-secure-perimeter-network` permet d'automatiser la configuration du dispositif de routeur virtuel Vyatta de votre périmètre sécurisé.

Pour plus d'informations sur le périmètre sécurisé, voir les articles de blogue suivants :
  * [Set up a Secure Perimeter in IBM Cloud](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/)
  * [Set up an automated Secure Perimeter in IBM Cloud](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

Vous pouvez utiliser l'image `ibmcloud-secure-perimeter-network` de deux façons :
-  Utiliser `ibmcloud-secure-perimeter-network` en tant que conteneur Docker pour initialiser la configuration des règles de pare-feu du périmètre sécurisé.
-  Utiliser `ibmcloud-secure-perimeter-network` en tant que pod sur un cluster Kubernetes afin d'interroger le compte d'infrastructure IBM Cloud à propos de nouveaux sous-réseaux créés sur les réseaux locaux virtuels (VLAN) de votre segment de périmètre sécurisé (Secure Perimeter Segment) et les ajouter à la configuration de pare-feu Vyatta.

## Eléments inclus
{: #whats_included}

L'image `ibmcloud-secure-perimeter-network` fournit les progiciels suivants.
{:shortdesc}

-   Alpine Linux
-   Exécution Python
-   Client SoftLayer Python
-   Ansible

## Initiation
{: #how_to_get_started}

Passez en revue les tâches suivantes pour apprendre à utiliser `ibmcloud-secure-perimeter-network` :

1.  [Mettre à disposition un cluster Kubernetes dans un périmètre sécurisé via {{site.data.keyword.containerlong}}](#provision_cluster)
2.  [Exécuter la configuration initiale de votre périmètre sécurisé Vyatta](#initial_setup)
3.  [Configuration en tant que pod Kubernetes au sein de votre périmètre sécurisé](#setup)
4.  [Référence config.json](#reference_config_json)
5.  [Référence rules.conf](#reference_rules_conf)

## Mettre à disposition un cluster Kubernetes dans un périmètre sécurisé via {{site.data.keyword.containerlong_notm}}
{: #provision_cluster}

1.  Mettez votre cluster Kubernetes à disposition depuis la section **Conteneurs** du catalogue IBM Cloud.
2.  Cliquez sur **Créer**.
3.  Sélectionnez les réseaux locaux virtuels public et privé du segment de paramètre sécurisé (Secure Perimeter Segment) à partir des menus déroulants VLAN.
4.  Au besoin, entrez toutes les autres informations requises.
5.  Cliquez sur **Créer un cluster**.

Consultez la documentation [{{site.data.keyword.containerlong_notm}}](/docs/containers/container_index.html#container_index) savoir comment accéder à votre cluster une fois celui-ci déployé.

## Exécuter la configuration initiale de votre périmètre sécurisé Vyatta
{: #initial_setup}

1. Créez un fichier nommé _config.json_. Ce fichier contiendra les paramètres de base nécessaires à `ibmcloud-secure-perimeter-network` pour accéder à Vyatta.

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

  Voir le [tableau de référence config.json](#reference_config_json) pour plus de détails sur la façon de renseigner le fichier _config.json_. Ce fichier peut également être utilisé dans le processus de [configuration de `ibmcloud-secure-perimeter-network` en tant que pod Kubernetes](#setup).

2. Exécutez `ibmcloud-secure-perimeter-network` en tant que conteneur Docker pour commencer la configuration initiale.

  ```
  docker run registry.bluemix.net/ibm/ibmcloud-secure-perimeter-network:1.0.0 python config-secure-perimeter.py -v /path/to/current/dir:/opt/secure-perimeter
  ```
  {: pre}

  Cette action crée un fichier _state.json_ dans votre répertoire de travail. Ce fichier est utilisé dans la [configuration de `ibmcloud-secure-perimeter-network` en tant que pod Kubernetes](#setup).

## Configuration en tant que pod Kubernetes au sein de votre périmètre sécurisé
{: #setup}

Pour que l'image `ibmcloud-secure-perimeter-network` puisse gérer des sous-réseaux dans votre périmètre sécurisé, vous pouvez l'exécuter en tant que processus de longue durée à l'aide d'un pod Kubernetes. `ibmcloud-secure-perimeter-network` comporte plusieurs dossiers et fichiers de configuration qui doivent être copiés sur le pod pour le configurer pour Vyatta :

1. Créez un fichier nommé _pvc.yaml_. Ce fichier de configuration crée une réclamation de volume persistant (pvc) que vous pouvez monter sur votre pod en tant que volume.

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

2. Créez la réclamation de volume persistant.

    ```
    kubectl apply -f restore-pvc.yaml
    ```
    {: pre}

3. Créez un fichier nommé _network-pod.yaml_. Ce fichier de configuration déploie l'image `ibmcloud-secure-perimeter-network` en tant que pod dans votre cluster Kubernetes et monte votre réclamation de volume persistant en tant que volume.

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
          image: registry.bluemix.net/ibm/ibmcloud-secure-perimeter-network:1.0.0
          volumeMounts:
          - name: network-vol
            mountPath: /opt/secure-perimeter
        volumes:
        - name: network-vol
          PersistentVolumeClaim:
            claimName: network-pvc
  ```
  {: codeblock}

4. Créez un fichier nommé _rules.conf_. Ce fichier de configuration indique à `ibmcloud-secure-perimeter-network` quels sous-réseaux et ports externes de l'Internet public placer sur liste blanche dans le périmètre sécurisé.

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

5. Copiez les fichier sur le pod `ibmcloud-secure-perimeter-network`.

  ```
  kubectl cp keys network-pod:/opt/secure-perimeter/
  kubectl cp state.json network-pod:/opt/secure-perimeter/state.json
  kubectl cp config.json network-pod:/opt/secure-perimeter/config.json
  kubectl cp rules.conf network-pod:/opt/secure-perimeter/rules.conf
  ```
  {: pre}

  Le répertoire _keys_ contient les clés SSH permettant à `ibmcloud-secure-perimeter-network` d'accéder à Vyatta. Voir la [section des prérequis](#prerequisites) pour plus d'informations sur les clés SSH.

## Référence config.json
{: #reference_config_json}

|Clé|Description
|---|-------------|---|
|slid|Votre nom d'utilisateur d'infrastructure IBM Cloud
|apikey|Votre clé d'API d'infrastructure IBM Cloud
|region|Région IBM Cloud où Vyatta est déployé
|inf_name_private|Nom de l'interface privée Vyatta
|inf_name_public|Nom de l'interface publique Vyatta
|gatewayid|ID passerelle Vyatta
|vlans|Liste des réseaux locaux virtuels (VLAN) de segment de périmètre sécurisé (Secure Perimeter Segment), avec le type, le numéro VLAN et l'ID VLAN
|vyatta_gateway_vip|IP virtuelle de la passerelle
|vyatta_primary|Objet contenant les IP privée et publique du membre Vyatta principal
|vyatta_secondary|Objet contenant les IP privée et publique du membre Vyatta secondaire
{: caption="Tableau 1. Fichier config.json" caption-side="top"}

## Référence rules.conf
{: #reference_rules_conf}

|Clé|Description
|---|-------------|---|
|external_subnets|Liste des sous-réseaux sur l'Internet public sur lesquels exposer le périmètre sécurisé
|external_ports|Liste des ports sur lesquels exposer le périmètre sécurisé
|userips|Liste des IP utilisateur à placer sur liste blanche pour le périmètre sécurisé
{: caption="Tableau 2. Fichier rules.conf" caption-side="top"}

## Prérequis
{: #prerequisites}

-   Vyatta et réseaux locaux virtuels (VLAN) classés à partir du portail d'infrastructure IBM Cloud et VLAN associés à Vyatta.
-   Le déploiement automatisé du paramètre sécurisé précharge Vyatta avec les clés SSH utilisées par `ibmcloud-secure-perimeter-network` pour accéder à la passerelle. Les clés SSH doivent être chargées manuellement ou via le processus d'installation du périmètre sécurisé. Consultez l'article [Set up an automated Secure Perimeter in IBM Cloud ![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/) pour plus d'informations.
