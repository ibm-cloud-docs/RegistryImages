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

# Einführung in das Image 'iccs-cluster-autoscaler' (Alpha)
{: #ibm-cluster-autoscaler}

Das Image 'iccs-cluster-autoscaler' enthält die vorinstallierten Pakete, die zum automatischen Skalieren der Workerknoten in Ihrem {{site.data.keyword.containerlong}}-Cluster erforderlich sind. Das Image basiert auf dem [Kubernetes-Projekt 'cluster-autoscaler'](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler).
{:shortdesc}

Die Alphaversion des Autoscalers unterstützt derzeit nur die Verfügbarkeit einzelner Zonen und ist für Workerknoten auf physischen Bare-Metal-Servern nicht verfügbar.
{: tip}

## Funktionsweise
{: #how-it-works}

Mit dem Image 'iccs-cluster-autoscaler' können Sie die Workerknoten in Ihrem Kubernetes-Cluster bei der Arbeit in {{site.data.keyword.containerlong}} über alle angegebenen automatischen Skalierungsgruppen hinweg auf- oder abwärts skalieren. Der Autoscaler wird als Bereitstellung im Namensbereich `kube-system` des Clusters ausgeführt und ist ein Plug-in für das Kubernetes-Tool Cluster Autoscaler.
{: shortdesc}

Das Image 'iccs-cluster-autoscaler' überwacht die Pods in Ihrem Cluster, indem es den Kubernetes-API-Server beobachtet und die Größe des Clusters automatisch nach oben oder unten skalieren kann.

<dl>
  <dt>Scale-up</dt>
    <dd>Das Image 'iccs-cluster-autoscaler' sucht regelmäßig nach Pods, die nicht planbar sind. Ein Pod ist nicht planbar, wenn der Kubernetes-Scheduler keinen Knoten findet, auf dem der Pod ausgeführt werden kann. Wenn der Autoscaler auf Pods stößt, die als nicht planbar gekennzeichnet sind, fügt er einen Workerknoten zu Ihrem Cluster hinzu.</dd>
  <dt>Scale-down</dt>
    <dd>Das Image 'iccs-cluster-autoscaler' führt standardmäßig alle 10 Sekunden eine Überprüfung auf nicht benötigte Knoten durch. Wenn ein Knoten für mehr als 10 Minuten nicht benötigt wird, wird er gelöscht.</br> Knoten gelten unter den folgenden Umständen als nicht erforderlich:</br>
    <ul><li>Die Summe von CPU und Speicheranforderungen von allen Pods, die auf diesem Knoten ausgeführt werden, beträgt weniger als 50 % der zugeordneten Menge.</li>
    <li>Alle auf dem Knoten ausgeführten Pods können auf andere Knoten verschoben werden. Pods, die standardmäßig auf allen Knoten ausgeführt werden, wie z. B. Manifest-Pods oder von DaemonSets erstellte Pods, sind nicht Bestandteil dieses Tests.</li>
    <li>Es gibt keine Annotation des Typs 'scale-down-disabled'. </li></ul></dd>
</dl>

Doch was bedeutet das alles?

![Automatische Skalierung eines Clusters](/images/autoscaler.png)

Wenn Sie Ihren Kubernetes-Cluster in IBM Cloud erstellen, geben Sie Ihre Hardwarevorgaben an. Der Autoscaler erstellt Gruppen auf der Basis der Maschinentypen, damit Sie sicherstellen können, dass Sie die richtige Menge an Ressourcen verwenden. In der Abbildung sehen Sie eine Beispielautoskalierungsrichtlinie. In der Konfiguration sind der Maschinentyp und die minimale und maximale Anzahl an Knoten angegeben. Wenn der Autoscaler eine Gruppe von Knoten ignorieren und keine Skalierung durchführen soll, schließen Sie diese Knoten nicht in die Konfiguration ein. In der Abbildung wurde für den Cluster ein Scale-up durchgeführt, um Änderungen an Ressourcen zu verarbeiten. Der Knoten 'b2c.16x64' wurde nicht skaliert, da in der Konfigurationsdatei keine Richtlinie definiert wurde.

## Nutzungsbeschränkungen
{: #usage}
Dieses Image kann in {{site.data.keyword.containershort}} mit der Kubernetes API-Version 1.7 oder höher verwendet werden.

## Voraussetzungen
{: #prerequisites}

Bevor Sie mit dem Image 'iccs-cluster-autoscaler' arbeiten können, müssen einige Voraussetzungen erfüllt sein.

* Sie müssen über einen Standardcluster verfügen, um das Autoscaler-Plug-in verwenden zu können. Automatische Skalierungsgruppen bestehen aus Workerknoten, die dieselben [Maschinentypen](/docs/containers/cs_cli_reference.html#cs_machine_types) sind. Um zu überprüfen, ob Sie die Voraussetzungen erfüllen, führen Sie den folgenden Befehl aus und stellen Sie sicher, dass für Ihren **Maschinentyp** nicht der Wert _free_ angegeben ist. Prüfen Sie außerdem, dass Ihre **Version** die `Version 1.7` oder höher ist. </br>
    ```
    ibmcloud ks workers <clustername>
    ```
    {: pre}
    Beispielausgabe: </br>
    ```
    OK
    ID                                                   Public IP        Private IP     Machine Type   State    Status   Version
    stage-dal06-crffef8b930b214442802bdcaa551b5ec0-w1    169.54.180.171   10.146.32.7    u2c.2x4        normal   Ready    1.8.2_1504
    ```
    {: screen}
* Sie müssen die folgenden CLIs und Plug-ins installieren. Unterstützende Informationen zum Installieren der Anforderungen und Erstellen Ihres ersten Clusters [finden Sie in diesem Lernprogramm in der Dokumentation](/docs/containers/cs_tutorials.html#cs_cluster_tutorial).
    <ul><li>{{site.data.keyword.Bluemix_notm}}-CLI</li><li>Kubectl-CLI</li><li>Docker-CLI</li><li>{{site.data.keyword.containerlong_notm}}-Plug-in</li><li>{{site.data.keyword.registrylong_notm}}-Plug-in</li></ul>
* Um die Workerknoten in Ihrem Namen zu skalieren, muss der Autoscaler Ihre Identitäts- und Zugriffsmanagementdetails kennen. Die Berechtigungsnachweise sind als geheimer Schlüssel in einem bezahlten Cluster verfügbar. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob der geheime Schlüssel verfügbar ist. Der Befehl gibt einen geheimen Schlüssel zurück.
    ```
		$kubectl get secrets -n kube-system | grep storage-secret-store
    ```
    {: pre}

## Installation und Konfiguration des Autoscalers
{: #install-configure}

Um das Autoscaler zu installieren, müssen Sie zunächst Helm in Ihrem Cluster installieren und initialisieren. Sobald der Autoscaler ausgeführt wird, speichert er automatisch Ressourcen und skaliert Ihre Cluster.

1. Melden Sie sich bei {{site.data.keyword.containershort_notm}} an und geben Sie Ihren Cluster als Ziel an.
    ```
    ibmcloud ks cluster-config <Clustername>`.
    ```
    {: pre}

2. [Installieren Sie Helm und initialisieren Sie das Programm in Ihrem Cluster](https://docs.helm.sh/using_helm/#quickstart).

3. Erstellen Sie mit der bereitgestellten Vorlage eine Konfigurationsdatei.

   ```
   cp incubator/helm/autoscaler/config.yaml.template config.yaml
   ```
   {: pre}

    Damit Skalierungsgruppen funktionieren können, müssen alle Maschinentypen in Ihrer Konfigurationsdatei angegeben werden, auch wenn Sie keinen Mindest- und Maximalbereich bereitstellen.
    {: tip}

  <table summary="Die erste Zeile in der Tabelle umfasst beide Spalten. Die übrigen Zeilen sollten von links nach rechts gelesen werden, mit dem Parameter in Spalte 1, der entsprechenden Beschreibung in Spalte 2 und dem Standard in Spalte 3.">
  <caption>Konfigurierbare Parameter und die zugehörigen Standardwerte</caption>
    <thead>
      <th colspan=3><img src="../images/idea.png" alt="Ideensymbol"/>Konfigurierbare Parameter und die zugehörigen Standardwerte </th>
    </thead>
    <tbody>
      <tr>
        <td><code>autoscalingGroups.name</code></td>
        <td> Der Maschinentyp, den Sie skalieren möchten. </td>
        <td> Kein Standardwert, benutzerdefiniert. Um nach dem Maschinentyp zu suchen, führen Sie <code>ibmcloud ks workers <clustername></code> aus.</td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.minSize</code></td>
        <td> Die minimale Anzahl an Workerknoten, auf die Ihr Cluster skalieren sollte. </td>
        <td> Kein Standardwert, benutzerdefiniert. </td>
      </tr>
      <tr>
        <td><code>autoscalingGroups.maxSize</code></td>
        <td> Die maximale Anzahl an Workerknoten, auf die Ihr Cluster skalieren sollte. </td>
        <td> Kein Standardwert, benutzerdefiniert. </td>
      </tr>
      <tr>
        <td><code>api_route</code></td>
        <td> Die Route zur {{site.data.keyword.containershort_notm}}-API für die Region, in der Ihr Cluster ausgeführt wird. </td>
        <td> https://containers.bluemix.net/swagger-api/ </td>
      </tr>
      <tr>
        <td><code>resources.limits.cpu</code></td>
        <td> Die Menge an CPU, auf die der Autoscaler-Pod begrenzt ist. </td>
        <td> 300 m </td>
      </tr>
      <tr>
        <td><code>resources.limits.memory</code></td>
        <td> Die Speichermenge, auf die der Autoscaler-Pod begrenzt ist. </td>
        <td> 300 Mi </td>
      </tr>
      <tr>
        <td><code>resources.requests.cpu</code></td>
        <td> Die Menge an CPU, mit der der Autoscaler-Pod beginnen soll. </td>
        <td> 100 m </td>
      </tr>
      <tr>
        <td><code>resources.requests.memory</code></td>
        <td> Die Speicherkapazität, mit der der Autoscaler-Pod beginnen soll. </td>
        <td> 100 Mi </td>
      </tr>
      <tr>
        <td><code>scale_down_delay</code></td>
        <td> Die Zeitspanne, die der Scaler nach dem Absetzen einer Scale-up-Anforderung auf das Scale-down warten soll. </td>
        <td> 10 m </td>
      </tr>
      <tr>
        <td><code>scale_down_unneeded_time</code></td>
        <td> Die Dauer, die ein Knoten als nicht benötigt gekennzeichnet sein sollte, bevor er wieder für einen Scale-down ausgewählt werden kann. </td>
        <td> 10 m </td>
      </tr>
      <tr>
        <td><code>skipNodes.withLocalStorage</code></td>
        <td> Wenn dieser Wert auf 'true' gesetzt ist, werden Knoten, die lokalen Speicher enthalten, übersprungen und nicht abwärts skaliert. </td>
        <td> true </td>
      </tr>
      <tr>
        <td><code>skipNodes.withSystemPods</code></td>
        <td> Wenn dieser Wert auf 'true' gesetzt ist, werden Knoten, die kube-system-Pods enthalten, übersprungen und nicht abwärts skaliert. </td>
        <td> true </td>
      </tr>
      <tr>
        <td><code>image.repository</code></td>
        <td> Das Docker-Image für das Cluster Autoscaler-Tool. </td>
        <td> registry.bluemix.net/iccs-cluster-autoscaler </td>
      </tr>
      <tr>
        <td><code>image.tag</code></td>
        <td> Die Version des Images, das Sie extrahieren möchten. </td>
        <td> 12 </td>
      </tr>
      <tr>
        <td><code>image.pullPolicy</code></td>
        <td> Gibt an, wann das Docker-Image extrahiert werden soll. </td>
        <td> Immer. </td>
      </tr>
    </tbody>
  </table>
  
  Das Image 'iccs-cluster-autoscaler' unterstützt verschiedene Konfigurationsparameter. Weitere Informationen zur Funktionsweise finden Sie in [den häufig gestellten Fragen zu Kubernetes Cluster Autoscaler](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md).

4. Installieren Sie den Helm Chart für Ihren Cluster im Namensbereich `kube-system`.

  ```
  helm install incubator/helm/autoscaler -f config.yaml --namespace kube-system
  ```
  {: pre}

    Die Ausgabe sollte ähnlich wie die folgende sein:
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

## Installation verifizieren

1. Stellen Sie sicher, dass der Cluster, der den Autoscaler-Pod enthält, aktiv ist.

  ```
  kubectl get pods --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    Beispielausgabe:
  ```
    cluster-autoscaler-4289984493-zldfk        1/1       Running   0          3m
  ```
  {: screen}

2. Stellen Sie sicher, dass der Cluster Autoscaler ausführt wird.

  ```
  kubectl get service --namespace=kube-system | grep cluster-autoscaler
  ```
  {: pre}

    Beispielausgabe:
  ```
  cluster-autoscaler       10.10.10.204   <none>         8085/TCP                     4m
  ```
  {: screen}


## Autoscaler deinstallieren

Sie können die Cluster Autoscaler-Bereitstellung deinstallieren.

1. Melden Sie sich bei {{site.data.keyword.Bluemix_notm}} und geben Sie Ihren Cluster durch Ausführen von `ibmcloud ks cluster-config <cluster_name>` als Ziel an.

2. Rufen Sie den Namen Ihrer Bereitstellung ab.

  ```
  helm ls | grep autoscaler
  <Bereitstellungsname>	1       	Mon Dec 18 13:45:09 2017	DEPLOYED	autoscaler-1.0.3	kube-system
  ```
  {: pre}

3. Löschen Sie die Bereitstellung.

  ```
  helm delete <Bereitstellungsname>
  ```
  {: pre}
