---

copyright:
  years: 2017
lastupdated: "2017-10-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Initiation à l'image **ibmnode** {: #getting_started_node}

L'image {{site.data.keyword.IBM}} Node (**ibmnode**) publique est fournie pour {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

**Remarque :** L'image {{site.data.keyword.IBM_notm}} Node (**ibmnode**) n'inclut pas d'application échantillon Node.js. Lorsque vous déployez un conteneur depuis cette image, votre conteneur s'arrête immédiatement après sa construction
vu qu'il ne contient aucun processus de longue haleine en exécution. Pour éviter cette situation, utilisez l'image {{site.data.keyword.IBM_notm}} Node (**ibmnode**) comme parent et créez votre propre image à laquelle vous pourrez ajouter votre propre code d'application Node.js. Pour plus d'informations, voir [Création d'une image à partir de cette image fournie](#creating_image).

# Fonctionnement {: #how_it_works}

Utilisez l'image {{site.data.keyword.IBM_notm}} Node (**ibmnode**) comme parent pour construire votre propre image avec votre propre code d'application Node.js.
{:shortdesc}

**Remarque :** Avant de commencer, consultez les conditions d'utilisation des images certifiées IBM à la section Utilisation de la [bibliothèque websphere-liberty Docker ![External link icon](../../../icons/launch-glyph.svg "External link icon:")](https://github.com/docker-library/docs/tree/master/websphere-liberty){: new_window}.

# Eléments inclus {: #included}

* Ubuntu 14.04
* Python, Git et modules apt-get indispensables pour la génération
* SDK {{site.data.keyword.IBM_notm}} Node

    **Remarque :** L'image ibmnode est disponible avec des étiquettes différentes pour pouvoir exécuter différentes versions du SDK IBM Node.

    <table>
    <caption> Tableau 1. Etiquettes des différentes versions SDK </caption>
      <tr>
        <th> Etiquette</th>
        <th> Description </th>
      </tr>
      <tr>
        <td> ibmnode:latest </td>
        <td> Version la plus récente d'IBM Node, équivalente à l'utilisation de ibmnode:v4 </td>
      </tr>
      <tr>
        <td> ibmnode:v4 </td>
        <td> IBM Node 4.4.0 </td>
      </tr>
      <tr>
        <td> ibmnode:v1.2 </td>
        <td> IBM Node 1.2.0.8 (0.12.10) </td>
      </tr>
      <tr>
        <td> ibmnode:v1.1 </td>
        <td> IBM Node 1.1.0.18 (0.10.43) </td>
      </tr>
    </table>


# Création d'une image à partir de cette image fournie {: #creating_image}

Vous pouvez utiliser l'image **ibmnode** comme parent pour créer une image enfant incluant votre propre
code d'application. Utilisez l'exemple de conteneur d'app `Hello World` avec
**ibmnode** et le module Express Node pour générer une image sur votre ordinateur. Ajoutez l'image au registre {{site.data.keyword.registrylong_notm}} privé de votre organisation et créez un conteneur à l'aide de celle-ci. Vous pouvez ensuite utiliser le fichier Dockerfile de l'exemple Hello World pour créer une autre image
avec votre propre app.
{:shortdesc}

Avant de commencer, prenez en compte les points suivants.

* Vous devez installer localement les outils suivants :
  * Plug-in [{{site.data.keyword.registrylong_notm}}
pour Cloud Foundry et interface de ligne de commande Docker](/docs/containers/container_cli_cfic_install.html)
  * Code source de [Node.js ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://nodejs.org/en/download/){: new_window}
  * [npm ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://github.com/npm/npm){: new_window}
* [Vous devez connaître l'espace de nom du registre {{site.data.keyword.registryshort_notm}} privé de votre organisation.](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__namespace)

    Exemple

    registry.DomainName/<var class="keyword varname">namespace</var>

* [Vous devez disposer d'une
adresse IP.](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__ip_request)

Créez une image dérivée de l'image **ibmnode** et incluant une application Hello World.
1.  Téléchargez le fichier [hellonode.zip ![External link icon](../../../icons/launch-glyph.svg "External link icon")](ftp://public.dhe.ibm.com/cloud/bluemix/containers/hellonode.zip){: new_window} et extrayez-le.
1.  Ouvrez le fichier Dockerfile dans un éditeur de texte et, dans l'instruction FROM, mettez à jour l'URL du registre d'après la région que vous désirez
utiliser.

    <ul>
    <li>Sud des Etats-Unis

        ```
		registry.ng.bluemix.net/ibmnode
		```
		{: codeblock}

    </li>
    <li>Royaume-Uni

        ```
		registry.eu-gb.bluemix.net/ibmnode
		```
		{: codeblock}

      </li>
    </ul>

1.  Depuis l'interface de ligne de commande, accédez au répertoire **app**.
1.  Depuis le répertoire **app**, exécutez les commandes suivantes.
    1.  Créez un fichier package.json.

        ```
        npm init
        ```
        {: pre}

        **Astuce :** A chaque invite, appuyez sur la touche Entrée pour accepter la valeur par défaut.

    2.  Installez l'infrastructure Node.js Express. Le fichier package.json est mis à jour avec les informations de version pour Express dans la section dependencies.

        ```
        npm install express -save
        ```
        {: pre}

1.  Repositionnez-vous dans le répertoire **hellonode**.
1.  Générez votre image et transférez-la dans votre registre {{site.data.keyword.registryshort_notm}} privé.*

    ```
    bx ic build -t registry.<DomainName>/<namespace>/hellonode .
    ```
    {: pre}

    **Astuce :** Exécutez `bx ic namespace get` pour obtenir les informations de l'espace de nom.  Définissez _registry.DomainName_ sur la même région que celle que vous avez utilisée précédemment dans Dockerfile.
    **Remarque :** \*Dans cette commande, vous pouvez remplacer `bx ic` par `docker lorsque vous êtes [connecté à {{site.data.keyword.containershort_notm}}](/docs/containers/container_cli_cfic_install.html#container_cli_login) et définir vos variables d'environnement afin qu'elles utilisent les commandes Docker natives. Vous pouvez utiliser des commandes Docker natives dans toutes les étapes de cette rubrique accompagnées d'un astérisque (*).

1.  Créez un conteneur depuis votre image.*

    ```
    bx ic run -p 3000 --name helloworld registry.<DomainName>/<namespace>/hellonode
    ```
    {: pre}

1.  Liez une adresse IP publique à votre conteneur.

    ```
    bx ic ip bind <IPaddress> helloworld
    ```
    {: pre}

    **Astuce :** Vous pouvez voir vos adresses IP publiques en exécutant la commande `bx ic ip list`. Pour en demander une nouvelle, exécutez la commande `bx ic ip request`.

1. Dans un navigateur, testez l'application en ouvrant l'URL **<var class="varname">IPaddress</var>:3000**. Le message Hello World! s'affiche.

Maintenant que vous avez créé l'image hellonode, vous pouvez modifier le fichier Dockerfile pour y inclure à la place votre propre application et créer une
autre image pour cette application.

1.  Pour l'instruction ADD, ajoutez votre propre code d'application.
1.  Pour l'instruction EXPOSE, mettez à jour les informations de port.
1.  Pour l'instruction CMD, créez une commande pour démarrer votre application.


# **ibmnode** Dockerfile reference {: #reference_dockerfile}

Ce fichier Dockerfile a été utilisé pour créer l'image **ibmnode** dans le catalogue {{site.data.keyword.Bluemix_notm}}. Ces informations sont fournies pour référence uniquement. Les autres fichiers inclus dans la version générée de cette image ne sont pas fournis.
{:shortdesc}

```
FROM ubuntu:14.04

# Update base OS
RUN apt-get update &&\
DEBIAN_FRONTEND=noninteractive apt-get -y install \
git \
build-essential \
python && \
DEBIAN_FRONTEND=noninteractive apt-get upgrade -y &&\
apt-get clean &&\
rm -Rf /var/cache/*

# Enhance default password rules
RUN sed -i 's/^PASS_MAX_DAYS.*/PASS_MAX_DAYS 90/' /etc/login.defs
RUN sed -i 's/sha512/sha512 minlen=8/' /etc/pam.d/common-password

# Install Node SDK
ONBUILD COPY ./prereq/node.tar.gz /tmp/prereq/
ONBUILD RUN cd /usr/local &&\
tar --strip-components 1 -xzf /tmp/prereq/node.tar.gz &&\
rm -rf /tmp/prereq

#Documentation path for URL
LABEL doc.url="/docs/services/RegistryImages/ibmnode/index.html"
```
{: screen}
