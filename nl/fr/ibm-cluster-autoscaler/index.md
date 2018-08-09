---

copyright:
  years: 2018
lastupdated: "2018-07-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Initiation à l'image iccs-cluster-autoscaler (alpha)
{: #ibm-cluster-autoscaler}

L'image iccs-cluster-autoscaler contient les packages préinstallés nécessaires pour mettre automatiquement à l'échelle les noeuds worker dans votre cluster {{site.data.keyword.containerlong}}. L'image iccs-cluster-autoscaler repose sur le [projet Kubernetes Cluster-Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler).
{:shortdesc}

La version alpha d'Autoscaler ne prend actuellement en charge que la disponibilité de zone unique et n'est pas disponible pour les noeuds worker des serveurs bare metal physiques.
{: tip}

## Fonctionnement
{: #how-it-works}

Avec l'image iccs-cluster-autoscaler, vous pouvez mettre à l'échelle les noeuds worker dans votre cluster Kubernetes, en augmentant ou diminuant leur nombre, dans tout groupe de mise à l'échelle automatique spécifié, quand vous utilisez {{site.data.keyword.containerlong}}. La fonction de mise à l'échelle automatique, qui s'exécute en tant que déploiement dans l'espace de nom `kube-system` de votre cluster, est un plug-in du dispositif Autoscaler de cluster Kubernetes.
{: shortdesc}

iccs-cluster-autoscaler, qui surveille les pods de votre cluster en observant le serveur d'API Kubernetes, peut mettre automatiquement à l'échelle la taille de votre cluster en l'augmentant ou la diminuant.

<dl>
  <dt>Augmentation du nombre de noeuds</dt>
    <dd>Périodiquement, iccs-cluster-autoscaler recherche la présence de pods non planifiables. Un pod est non planifiable quand le planificateur Kubernetes ne parvient pas à trouver un noeud sur lequel l'exécuter. Quand Autoscaler rencontre un pod de ce type, il ajoute un noeud worker à votre cluster.</dd>
  <dt>Diminution du nombre de noeuds</dt>
    <dd>Par défaut, iccs-cluster-autoscaler recherche toutes les 10 secondes la présence de noeuds inutiles. Si un noeud ne sert pas pendant plus de 10 minutes, il est supprimé. </br> Les noeuds sont considérés comme inutiles quand :</br>
    <ul><li>La somme des demandes d'unité centrale et de mémoire effectuées par tous les pods s'exécutant sur ce noeud est inférieure à 50 % du volume qui lui est alloué.</li>
    <li>Tous les pods s'exécutant sur le noeud peuvent être déplacés sur d'autres noeuds. Les pods qui s'exécutent par défaut sur tous les noeuds, tels les pods manifest-run ou les pods créés par des objets DaemonSet, sont exclus de ce test.</li>
    <li>Il n'y a pas d'annotation de désactivation de la mise à l'échelle par diminution du nombre de noeuds.</li></ul></dd>
</dl>

Comment ces principes sont-ils mis en pratique ?

![Mise à l'échelle automatique d'un cluster](/images/autoscaler.png)

Quand vous créez votre cluster Kubernetes dans IBM Cloud, vous spécifiez les préférences relatives à votre matériel. Autoscaler crée des groupes basés sur les types de machine pour vous permettre de vous assurer que vous utilisez le volume correct de ressources. L'image propose un exemple de politique de mise à l'échelle automatique. Dans la configuration, le type de machine ainsi que le nombre minimal et maximal de noeuds sont spécifiés. Si vous voulez qu'Autoscaler ignore un ensemble de noeuds et ne le mette pas à l'échelle, ne l'incluez pas dans votre configuration. L'image illustre une mise à l'échelle du cluster par augmentation du nombre de noeuds pour gérer les changements dans les ressources. Le noeud b2c.16x64 n'a pas été mis à l'échelle, car aucune politique n'a été définie dans le fichier de configuration.

## Restrictions d'utilisation
{: #usage}
Cette image peut être utilisée dans {{site.data.keyword.containershort}} avec Kubernetes API version 1.7 ou ultérieure.

## Prérequis
{: #prerequisites}

Certains prérequis doivent être respectés avant d'utiliser iccs-cluster-autoscaler.

* Vous devez disposer d'un cluster standard pour vous servir du plug-in Autoscaler. Les groupes de dimensionnement automatique se composent de noeuds worker dont les [types de machine](/docs/containers/cs_cli_reference.html#cs_machine_types) sont identiques. Pour vérifier que vous répondez aux exigences, exécutez la commande ci-après et assurez-vous que **Machine Type** n'indique pas _free_. Vérifiez que la zone **Version** affiche bien `version 1.7` ou ultérieure. </br>
    ```
    ibmcloud ks workers <cluster-name>
    ```
    {: pre}
    Exemple de sortie : </br>
    ```
    OK
    ID                                                   Public IP        Private IP     Machine Type   State    Status   Version
    stage-dal06-crffef8b930b214442802bdcaa551b5ec0-w1    169.54.180.171   10.146.32.7    u2c.2x4        normal   Ready    1.8.2_1504
    ```
    {: screen}
* Vous devez installer les interfaces de ligne de commande et plug-ins suivants. Pour obtenir de l'aide sur l'installation des prérequis et la création de votre premier cluster, [consultez ce tutoriel dans la documentation](/docs/containers/cs_tutorials.html#cs_cluster_tutorial).
    <ul><li>Interface de ligne de commande d'{{site.data.keyword.Bluemix_notm}}</li><li>Interface de ligne de commande Kubectl</li><li>Interface de ligne de commande Docker</li><li>Plug-in {{site.data.keyword.containerlong_notm}}</li><li>Plug-in {{site.data.keyword.registrylong_notm}}</li></ul>
* Pour dimensionner les noeuds worker en votre nom, Autoscaler a besoin de connaître les détails relatifs à votre identité et à la gestion des accès. Les données d'identification sont disponibles sous forme de secret dans un cluster payant. Pour vérifier que le secret est disponible, exécutez la commande ci-après. Cette dernière retourne un secret.
    ```
		$kubectl get secrets -n kube-system | grep storage-secret-store
    ```
    {: pre}

## Installation et configuration d'Autoscaler
{: #install-configure}

Pour installer Autoscaler, vous devez d'abord installer et initialiser Helm dans votre cluster. Une fois qu'Autoscaler s'exécute, il conserve automatiquement les ressources et met à l'échelle vos clusters.

1. Connectez-vous à {{site.data.keyword.containershort_notm}} et ciblez votre cluster.
    ```
    ibmcloud ks cluster-config <cluster_name>`.
    ```
    {: pre}

2. [Installez Helm et initialisez-le dans votre cluster](https://docs.helm.sh/using_helm/#quickstart).

3. En utilisant le modèle fourni, créez un fichier de configuration.

   ```
   cp incubator/helm/autoscaler/config.yaml.template config.yaml
   ```
   {: pre}

    Pour que les groupes de mise à l'échelle fonctionne, tous vos types de machine doivent être spécifiés dans votre fichier de configuration, même si vous ne fournissez pas de plage minimale et maximale.{: tip}

  <table summary="La première ligne du tableau porte sur les deux colonnes. Les autres lignes doivent être lues de gauche à droite, avec le paramètre dans la première colonne, la description correspondante, dans la deuxième colonne, et la valeur par défaut, dans la troisième colonne.">
  <caption>Paramètres configurables et valeurs par défaut</caption>
    <thead>
      <th colspan=3><img src="../images/idea.png" alt="Icône d'idée"/>Paramètres configurables et valeurs par défaut</th>
    </thead>
    <tbody>
      <tr>
        <td><code>autoscalingGroups.name</code></td>
        <td> Type de machine que vous voulez mettre à l'échelle. </td>
        <td> Pas de valeur par défaut, défini par l'utilisateur. Pour connaître votre type de machine, exécutez <code>ibmcloud ks workers <cluster-name></code></td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.minSize</code></td>
        <td> Nombre minimal de noeuds worker dans lesquels la mise à l'échelle du cluster doit s'effectuer. </td>
        <td> Pas de valeur par défaut, défini par l'utilisateur. </td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.maxSize</code></td>
        <td> Nombre maximal de noeuds worker dans lesquels la mise à l'échelle du cluster doit s'effectuer.</td>
        <td> Pas de valeur par défaut, défini par l'utilisateur. </td>
      </tr>
      <tr>
        <td><code>api_route</code></td>
        <td> Route menant à l'API {{site.data.keyword.containershort_notm}} pour la région dans laquelle votre cluster s'exécute. </td>
        <td> https://containers.bluemix.net/swagger-api/ </td>
      </tr>
      <tr>
        <td><code>resources.limits.cpu</code></td>
        <td> Limite d'unité centrale appliquée au pod Autoscaler. </td>
        <td> 300 m </td>
      </tr>
      <tr>
        <td><code>resources.limits.memory</code></td>
        <td> Limite de mémoire appliquée au pod Autoscaler. </td>
        <td> 300 Mi </td>
      </tr>
      <tr>
        <td><code>resources.requests.cpu</code></td>
        <td> Quantité d'unité centrale avec laquelle le pod Autoscaler doit démarrer. </td>
        <td> 100 m </td>
      </tr>
      <tr>
        <td><code>resources.requests.memory</code></td>
        <td> Quantité de mémoire avec laquelle le pod Autoscaler doit démarrer.</td>
        <td> 100 Mi </td>
      </tr>
      <tr>
        <td><code>scale_down_delay</code></td>
        <td> Durée pendant laquelle Autoscaler doit attendre pour procéder à une diminution du nombre de noeuds après avoir émis une demande d'augmentation du nombre de noeuds. </td>
        <td> 10 m </td>
      </tr>
      <tr>
        <td><code>scale_down_unneeded_time</code></td>
        <td> Durée pendant laquelle un noeud doit être considéré comme inutile avant de pouvoir être mis à l'échelle et supprimé.</td>
        <td> 10 m </td>
      </tr>
      <tr>
        <td><code>skipNodes.withLocalStorage</code></td>
        <td> Quand défini à true, les noeuds contenant un stockage local seront ignorés et aucune mise à l'échelle par diminution du nombre de noeuds ne leur sera appliquée. </td>
        <td> true </td>
      </tr>
      <tr>
        <td><code>skipNodes.withSystemPods</code></td>
        <td> Quand défini à true, les noeuds contenant des pods kube-system seront ignorés et aucune mise à l'échelle par diminution du nombre de noeuds ne leur sera appliquée. </td>
        <td> true </td>
      </tr>
      <tr>
        <td><code>image.repository</code></td>
        <td> Image Docker Autoscaler de cluster. </td>
        <td> registry.bluemix.net/iccs-cluster-autoscaler </td>
      </tr>
      <tr>
        <td><code>image.tag</code></td>
        <td> Version de l'image que vous voulez extraire. </td>
        <td> 12 </td>
      </tr>
      <tr>
        <td><code>image.pullPolicy</code></td>
        <td> Spécifie quand extraire l'image Docker. </td>
        <td> Toujours. </td>
      </tr>
    </tbody>
  </table>
  
  iccs-cluster-autoscaler prend en charge différents paramètres de configuration. Pour des informations plus détaillées sur son mode de fonctionnement, voir [kubernetes / autoscaler - Frequently Asked Questions](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md).

4. Installez la charte Helm dans l'espace de nom `kube-system`.

  ```
  helm install incubator/helm/autoscaler -f config.yaml --namespace kube-system
  ```
  {: pre}

    La sortie doit ressembler à ceci :
  ```
  NAMESPACE: kube-system
  STATUS: DEPLOYED

  RESOURCES:
  ==> v1/Service
  NAME                CLUSTER-IP    EXTERNAL-IP  PORT(S)   AGE
  cluster-autoscaler  10.10.10.189  <none>       8085/TCP  5s

  ==> v1beta1/Deployment
  NAME                DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
  cluster-autoscaler  1        1        1           0          3s
  ```
  {: screen}

## Vérification de l'installation

1. Vérifiez que le cluster qui contient le pod Autoscaler s'exécute.

  ```
  kubectl get pods --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    Exemple de sortie :
  ```
    cluster-autoscaler-4289984493-zldfk        1/1       Running   0          3m
  ```
  {: screen}

2. Vérifiez que le dispositif Autoscaler de cluster s'exécute.

  ```
  kubectl get service --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    Exemple de sortie :
  ```
  cluster-autoscaler       10.10.10.204   <none>         8085/TCP                     4m
  ```
  {: screen}


## Désinstallation d'Autoscaler

Vous pouvez désinstaller le déploiement Autoscaler de cluster.

1. Connectez-vous à {{site.data.keyword.Bluemix_notm}} et ciblez votre cluster en exécutant `ibmcloud ks cluster-config <cluster_name>`.

2. Obtenez le nom de votre déploiement.

  ```
  helm ls | grep autoscaler
  <deployment_name>	1       	Mon Dec 18 13:45:09 2017	DEPLOYED	autoscaler-1.0.3	kube-system
  ```
  {: pre}

3. Supprimez le déploiement.

  ```
  helm delete <deployment_name>
  ```
  {: pre}
