[Deploy and run a containerized web app with Azure App Service](https://docs.microsoft.com/en-us/learn/modules/deploy-run-container-app-service/) (~46 mins)

# Introduction

You can build and deploy Azure-based web apps by using Docker containers. This approach enables you to roll out a web app quickly. Support for continuous delivery ensures that users see the latest build of the app while minimizing administrative overhead.

Suppose that you work for an already-successful company that's testing a new software as a service (SaaS) product. The small team working on it is moving quickly. When they began, they weren’t sure where they were going to host the app. So, they chose to package the app into a Docker container to improve the efficiency of their development process and maximize their options for deployment. They’re ready to deploy the first version, and they expect to be iterating quickly.

The team decided to deploy the new app as a web app that uses Azure App Service, a platform for hosting web apps. The code for a web app running in App Service can be supplied as a Docker image.

This module shows you how to create and store Docker images in Azure Container Registry. You'll see how to use these images to deploy a web app. Then, you'll learn how to configure continuous deployment so that the web app is redeployed whenever a new version of the image is released.

By the end of this module, you'll be able to create and maintain web apps that use Docker images that are stored in Container Registry.

## Learning objectives

In this module, you will:

- Create Docker images and store them in a repository in Container Registry.
- Use App Service to run web apps that are based on Docker images held in Container Registry.
- Use webhooks to configure continuous deployment of a web app that's based on a Docker image.

## Prerequisites

- Basic experience with Docker, including the use of Docker commands to store and retrieve Docker images.
- Experience with creating web apps by using App Service.

# Build and store images by using Azure Container Registry

Azure Container Registry enables you to store Docker images in the cloud, in an Azure storage account.

In the example scenario, the team has decided to use Container Registry to host their Docker images. They can use Container Registry to create a Docker image registry in Azure, alongside their other Azure resources, and store their Docker images securely.

In this unit, you'll learn more about Container Registry and the advantages it provides for storing Docker images.

## What is Container Registry?

Container Registry is an Azure service that you can use to create your own private Docker registries. Like Docker Hub, Container Registry is organized around repositories that contain one or more images. Container Registry also lets you automate tasks such as redeploying an app when an image is rebuilt.

Security is an important reason to choose Container Registry instead of Docker Hub:

- You have much more control over who can see and use your images.
- You can sign images to increase trust and reduce the chances of an image becoming accidentally (or intentionally) corrupted or otherwise infected.
- All images stored in a container registry are encrypted at rest.

Working with images in Container Registry is like working with Docker Hub, but offers a few unique benefits:

- Container Registry runs in Azure. The registry can be replicated to store images near where they're likely to be deployed.
- Container Registry is highly scalable, providing enhanced throughput for Docker pulls that can span many nodes concurrently. The Premium SKU of Container Registry includes 500 GiB of storage.

## Using Container Registry

You create a registry by using either the Azure portal or the Azure CLI `acr create` command. In the following code example, the name of the new registry is _myregistry_:

```sh
az acr create --name myregistry --resource-group mygroup --sku standard --admin-enabled true
```

In addition to storing and hosting images, you can also use Container Registry to build images. Instead of building an image yourself and pushing it to Container Registry, use the CLI to upload the Docker file and other files that make up your image. Container Registry will then build the image for you. Use the `acr build` command to run a build:

```sh
az acr build --file Dockerfile --registry myregistry --image myimage .
```

Additional information about Azure Container Registry as well as a link to supported CLI commands to manage private registries are available in the learn more section of this module.

# Exercise - Build and store an image by using Azure Container Registry

Azure Container Registry provides storage for Docker images in the cloud.

In the example scenario, the team needs to create a registry to store the images for their web apps.

In this unit, you'll use the Azure portal to create a new registry in Azure Container Registry. You'll build a Docker image from the source code for a web app and upload it to a repository in your registry. Finally, you'll examine the contents of the registry and the repository.

## Create a registry in Azure Container Registry

Sign in to the Azure portal with your Azure subscription.

Choose **Create a resource**, select **Containers**, and then select **Container Registry**.

![https://docs.microsoft.com/en-us/learn/modules/deploy-run-container-app-service/media/3-search-container-registry-annotated.png](https://docs.microsoft.com/en-us/learn/modules/deploy-run-container-app-service/media/3-search-container-registry-annotated.png)

Specify the values in the following table for each of the properties:

- Registry name - Enter a unique name and make a note of it for later.
- Subscription - Select your default Azure subscription in which you are allowed to create and manage resources.
- Resource Group - Create a new resource group with the name **learn-deploy-container-acr-rg** so that it will be easier to clean up these resources when you're finished with the module. If you choose a different resource group name, remember it for the rest of the exercises in this module.
- Location - Select a location that is close to you.
- SKU - **Standard**

Click **Create**. Wait until the container registry has been created before you continue.

## Build a Docker image and upload it to Azure Container Registry

In the Azure Cloud Shell in the portal, run the following command to download the source code for the sample web app. This web app is simple. It presents a single page that contains static text and a carousel control that rotates through a series of images.

```sh
git clone https://github.com/MicrosoftDocs/mslearn-deploy-run-container-app-service.git
```

Move to the source folder:

```sh
cd mslearn-deploy-run-container-app-service/node
```

Run the following command. This command sends the folder's contents to Azure Container Registry, which uses the instructions in the Docker file to build the image and store it. Replace `<container_registry_name>` with the name of the registry you created earlier. Take care not to leave out the `.` character at the end of the command.

```sh
az acr build --registry <container_registry_name> --image webimage .
```

The Docker file contains the step-by-step instructions for building a Docker image from the source code for the web app. Azure Container Registry runs these steps to build the image, and as each step completes a message is generated. The build process should finish after a couple of minutes without any errors or warnings.

## Examine the container registry

# Deploy a web app by using an image from an Azure Container Registry repository

# Exercise - Create and deploy a web app from a Docker image

# Update the image and automatically redeploy the web app

# Exercise - Modify the image and redeploy the web app

# Summary
