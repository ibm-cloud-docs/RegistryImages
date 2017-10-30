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

# Getting started with the **ibmnode** image {: #getting_started_node}

The public {{site.data.keyword.IBM}} Node (**ibmnode**) image is supplied for {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

**Note:** The {{site.data.keyword.IBM_notm}} Node (**ibmnode**) image does not include a sample Node.js app. When you deploy a container from this image, your container shuts down immediately after it is built as no long running process exists. To avoid that, use the {{site.data.keyword.IBM_notm}} Node (**ibmnode**) image as a parent and create your own image where you can add your own Node.js app code. For more information, see [Creating an image from this provided image](#creating_image).

# How it works {: #how_it_works}

Use the {{site.data.keyword.IBM_notm}} Node (**ibmnode**) image as a parent to build your own image with your own Node.js app code.
{:shortdesc}

**Note:** Before you begin, review the terms of use for IBM certified images in the Usage section of the [websphere-liberty Docker library ![External link icon](../../../icons/launch-glyph.svg "External link icon:")](https://github.com/docker-library/docs/tree/master/websphere-liberty){: new_window}.

# What is included {: #included}

* Ubuntu 14.04
* Python, Git, and build-essentials apt-get packages
* {{site.data.keyword.IBM_notm}} Node SDK

    **Note:** The ibmnode image is available with different tags so that you can run different versions of the IBM Node SDK.

    <table>
    <caption> Table 1. Tags for different SDK versions </caption>
      <tr>
        <th> Tag </th>
        <th> Description </th>
      </tr>
      <tr>
        <td> ibmnode:latest </td>
        <td> The latest version of IBM Node, equivalent to using ibmnode:v4 </td>
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


# Creating an image from this provided image {: #creating_image}

You can use the **ibmnode** image as a parent for creating a child image that includes your own app code. Use the sample `Hello World` app container with **ibmnode** and the Express Node module to build an image on your computer. Add the image to your organization's private {{site.data.keyword.registrylong_notm}} and create a container with it. Then, you can use the Dockerfile from the Hello World sample to create another image with your own app.
{:shortdesc}

Before you begin, consider the following steps.

* You must install the following tools locally:
  * [{{site.data.keyword.registrylong_notm}} plug in for Cloud Foundry and the Docker CLI](/docs/containers/container_cli_cfic_install.html)
  * [Node.js source code ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://nodejs.org/en/download/){: new_window}
  * [npm ![External link icon](../../../icons/launch-glyph.svg "External link icon")](https://github.com/npm/npm){: new_window}
* [You must know the namespace from your organization's private {{site.data.keyword.registryshort_notm}}.](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__namespace)

    Example

    registry.DomainName/<var class="keyword varname">namespace</var>

* [You must have an IP address.](/docs/containers/container_cli_reference_cfic.html#container_cli_reference_cfic__ip_request)

Create an image that is based on the **ibmnode** image and includes a Hello World app.
1.  Download the [hellonode.zip ![External link icon](../../../icons/launch-glyph.svg "External link icon")](ftp://public.dhe.ibm.com/cloud/bluemix/containers/hellonode.zip){: new_window} and extract it.
1.  Open the Dockerfile with a text editor and in the FROM instruction, update the registry URL for the region you want to use.

    <ul>
    <li>US South

        ```registry.ng.bluemix.net/ibmnode```

    </li>
    <li>United Kingdom

        ```registry.eu-gb.bluemix.net/ibmnode```

      </li>
    </ul>

1.  From the CLI, navigate into the **app** directory.
1.  In the **app** directory, run the following commands.
    1.  Create a package.json file.

        ```
        npm init
        ```
        {: pre}

        **Tip:** Press return to accept the default value for each of the prompts.

    2.  Install Express, a framework for Node.js. The package.json is updated with the version information for Express in the dependencies section.

        ```
        npm install express -save
        ```
        {: pre}

1.  Navigate back up to the **hellonode** directory.
1.  Build your image and push it to your private {{site.data.keyword.registryshort_notm}}.*

    ```
    bx ic build -t registry.<DomainName>/<namespace>/hellonode .
    ```
    {: pre}

    **Tip:** Run `bx ic namespace get` to retrieve your namespace information.  Set the _registry.DomainName_ to the same region you used previously in the Dockerfile.

    **Note:** \*In this command, you can replace `bx ic` with `docker` when you are [logged in to {{site.data.keyword.containershort_notm}}](/docs/containers/container_cli_cfic_install.html#container_cli_login) and set your environment variables to use native Docker commands. You can use native Docker commands in all steps that are marked with an asterisk (*) in this topic.

1.  Create a container from your image.*

    ```
    bx ic run -p 3000 --name helloworld registry.<DomainName>/<namespace>/hellonode
    ```
    {: pre}

1.  Bind a public IP address to your container.

    ```
    bx ic ip bind <IPaddress> helloworld
    ```
    {: pre}

    **Tip:** You can see your public IP addresses by running `bx ic ip list`. To request a new one, run `bx ic ip request`.

1. In a browser, test the app by opening **<var class="varname">IPaddress</var>:3000**. The Hello World! message is displayed.

Now that you created the hellonode image, you can modify the Dockerfile to include your own app instead and create another image for that app.

1.  For the ADD instruction, add your own application code.
1.  For the EXPOSE instruction, update the port information.
1.  For the CMD instruction, create a command to start your app.


# **ibmnode** Dockerfile reference {: #reference_dockerfile}

This Dockerfile was used to create the **ibmnode** image in the {{site.data.keyword.Bluemix_notm}} catalog. This information is for reference only. The other files that are included in the built version of this image are not provided.
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
