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

# Initiation à l'image `ibmcloud-secure-perimeter-health`
{: #ibmcloud-secure-perimeter-health}

L'image `ibmcloud-secure-perimeter-health` contient un outil qui permet d'analyser les vulnérabilités dans un périmètre sécurisé dans {{site.data.keyword.cloud}}.
{:shortdesc}

Vous pouvez accéder aux images fournies par {{site.data.keyword.IBM_notm}} à l'aide de la ligne de commande. Voir [Images IBM publiques](/docs/services/Registry/registry_public_images.html#public_images).
{: tip}

## Fonctionnement
{: #how-it-works}

Pour garantir le bon fonctionnement de votre périmètre sécurisé, `ibmcloud-secure-perimeter-health` peut analyser le réseau public ou le réseau privé dans votre compte d'infrastructure {{site.data.keyword.cloud_notm}} et signaler les vulnérabilités. Vous pouvez utiliser l'image **ibmcloud-secure-perimeter-health** de deux façons :

- Utiliser `ibmcloud-secure-perimeter-health` en tant que pod sur un cluster Kubernetes au sein de votre périmètre sécurisé pour rechercher les expositions de réseau privé.
- Utiliser `ibmcloud-secure-perimeter-health` en tant que conteneur Docker autonome sur votre poste de travail pour rechercher les expositions de réseau public.

Vous trouverez des informations supplémentaires sur le périmètre sécurisé dans les articles de blogue suivants :

- [Set up a Secure Perimeter in {{site.data.keyword.cloud_notm}} ![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://developer.ibm.com/dwblog/2018/ibm-cloud-vyatta-set-up-secure-perimeter/).
- [Set up an automated Secure Perimeter in {{site.data.keyword.cloud_notm}} ![Icône de lien externe](../../../icons/launch-glyph.svg "Icône de lien externe")](https://developer.ibm.com/dwblog/2018/set-automated-secure-perimeter-ibm-cloud/).

Après l'analyse, l'image `ibmcloud-secure-perimeter-health` génère un rapport sur les réseaux accessibles depuis le segment de périmètre sécurisé (Secure Perimeter Segment). Chaque rapport indique le nom de la passerelle réseau, le réseau local virtuel (VLAN), ses sous-réseaux ainsi que les éventuels hôtes incriminés. Exemple de rapport d'un utilisateur ayant recherché des vulnérabilité du réseau privé :

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

## Eléments inclus
{: #whats_included}

L'image `ibmcloud-secure-perimeter-health` fournit les progiciels suivants.
{:shortdesc}

- Alpine Linux
- Exécution Python
- Client SoftLayer Python
- Analyseur de port Nmap

## Initiation
{: #how_to_get_started}

Passez en revue les tâches suivantes pour apprendre à utiliser `ibmcloud-secure-perimeter-health` :

1. [Mettre à disposition un cluster Kubernetes dans un périmètre sécurisé via {{site.data.keyword.containerlong_notm}}](#provision_cluster)
2. [Analyser les réseaux privés au sein d'un périmètre sécurisé](#private_networks)
3. [Analyser les réseaux publics hors d'un périmètre sécurisé](#public_networks)
4. [Comprendre les résultats d'analyse](#scan_results)
5. [Référence d'argument de conteneur](#reference_container_arg)
6. [Référence de variable d'environnement](#reference_env_var)

## Mettre à disposition un cluster Kubernetes dans un périmètre sécurisé via {{site.data.keyword.containerlong_notm}}
{: #provision_cluster}

1. Mettez à disposition votre cluster Kubernetes à partir de la section **Conteneurs** du catalogue {{site.data.keyword.cloud_notm}}.
2. Cliquez sur **Créer**.
3. Sélectionnez les réseaux locaux virtuels public et privé du segment de paramètre sécurisé (Secure Perimeter Segment) à partir des menus déroulants VLAN.
4. Entrez toutes les autres informations requises.
5. Cliquez sur **Créer un cluster**.

Consultez la documentation [{{site.data.keyword.containerlong_notm}}](/docs/containers/container_index.html#container_index) savoir comment accéder à votre cluster une fois celui-ci déployé.

## Analyser les réseaux privés au sein d'un périmètre sécurisé
{: #private_networks}

Créez un pod de conteneur à partir de l'image `ibmcloud-secure-perimeter-health`, puis configurez une analyse de routine.

**Avant de commencer**

- Installez les [interfaces de ligne de commande](/docs/containers/cs_cli_install.html#cs_cli_install) requises.
- [Ciblez votre interface de ligne de commande](/docs/containers/cs_cli_install.html#cs_cli_configure) vers votre cluster.

1. Créez un fichier de configuration nommé _health-pod.yaml_. Ce fichier crée un déploiement hautement disponible du pod de conteneur.

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

2. Créez le déploiement.

    ```
    kubectl apply -f health-pod.yaml
    ```
    {: pre}

3. Assurez-vous que le pod est en cours d'exécution.

    ```
    kubectl get pods
    ```
    {: pre}

    ```
    NAME                                    READY     STATUS    RESTARTS   AGE
    health-pod-<random-id>                  1/1       Running   0          1hr
    ```
    {: screen}

## Analyser les réseaux publics hors d'un périmètre sécurisé
{: #public_networks}

Créez un conteneur Docker à partir de l'image `ibmcloud-secure-perimeter-health` et analysez des réseaux publics.

**Avant de commencer**

- Installez Docker.

1. Créez un conteneur Docker à partir de votre propre poste de travail à l'aide de la commande suivante :

    ```
    docker run -it -e SL_USER='$SL_USER' -e SL_APIKEY='$SL_APIKEY' registry.bluemix.net/ibm/ibmcloud-secure-perimeter-health:1.0.0 /usr/local/bin/python run.py --scan public --allowed-public-ports 80 443 9000-9999
    ```
    {: pre}

2. Une fois que le conteneur a produit un rapport, passez à la section [Comprendre les résultats d'analyse](#scan_results).

## Comprendre les résultats d'analyse
{: #scan_results}

`ibmcloud-secure-perimeter-health` produit un rapport mis en forme sur la santé du fonctionnement d'un périmètre sécurisé :

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

Le format du rapport est le suivant :

```
<nom de la passerelle> :
  <nom du VLAN> :
    <sous-réseau> :    PASS
    <sous-réseau> :    FAIL
      host = <adresse IP>, ports = [<port exposé>, <port exposé>, ...]
```
{: screen}

`ibmcloud-secure-perimeter-health` détermine un sous-réseau comme `PASS` si aucun hôte du sous-réseau n'est accessible, ou bien renvoie `FAIL` et répertorie les hôtes accessibles ainsi que les ports accessibles.

## Référence d'argument de conteneur
{: #reference_container_arg}

|Clé|Description|Valeur par défaut
|---|-------------|---|
|scan|Type d'analyse d'exposition ("publique" ou "privée") |Aucun (analyser les deux)
|exclude-vlan-ids|Liste des réseaux locaux virtuels (VLAN) par ID pour éviter l'analyse|Aucun
|poll-interval|Définit le nombre de secondes écoulées entre deux analyses|0 (une seule exécution)
|allowed-public-ports|Liste des ports à placer sur liste blanche sous l'analyse|80, 443, 9000-9999
{: caption="Tableau 1. Arguments de conteneur" caption-side="top"}

## Référence de variable d'environnement
{: #reference_env_var}

|Clé|Description|
|---|-------------|
|SL_USER|Votre nom d'utilisateur d'infrastructure IBM Cloud|
|SL_APIKEY|Votre clé d'API d'infrastructure IBM Cloud|
{: caption="Tableau 2. Variables d'environnement" caption-side="top"}
