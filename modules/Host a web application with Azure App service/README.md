[Host a web application with Azure App service](https://docs.microsoft.com/en-us/learn/modules/host-a-web-app-with-azure-app-service/) (~58 mins)

# Introduction

Imagine you're building a website for a new business, or you're running an existing web app on an aging on-premises server. Setting up a new server can be challenging. You need appropriate hardware, likely a server-level operating system, and a web hosting stack.

And once it's running, you need to maintain the server. And what happens if your website traffic increases? You may need to invest in additional hardware.

Hosting your web application using Azure App Service makes deploying and managing a web app much easier when compared to managing a physical server. In this module, we'll implement and deploy a web app to App Service.

## Learning objectives

In this module, you will:

- Use the Azure portal to create an Azure App Service web app
- Use developer tools to create the code for a starter web application
- Deploy your code to App Service

# Create a web app in the Azure portal

In this unit, you'll learn how to create an Azure App Service web app using the Azure portal.

## Why use the Azure portal?

The first step in hosting your web application is to create a web app (an App Service app) inside your Azure subscription.

There are several ways you can create a web app. You can use the Azure portal, the Azure Command Line Interface (CLI), a script, or an IDE.

The information presented below will discuss how to use the Azure portal to create a web app, and in the next exercise you will use this information to create a web app. For this module, we will demonstrate using the Azure portal because it's a graphical experience, which makes it a great learning tool. The portal helps you discover available features, add additional resources, and customize existing resources.

## What is Azure App Service?

Azure App Service is a fully managed web application hosting platform. This platform as a service (PaaS) offered by Azure allows you to focus on designing and building your app while Azure takes care of the infrastructure to run and scale your applications.

### Deployment slots

Using the Azure portal, you can easily add **deployment slots** to an App Service web app. For instance, you can create a **staging** deployment slot where you can push your code to test on Azure. Once you are happy with your code, you can easily **swap** the staging deployment slot with the production slot. You do all this with a few simple mouse clicks in the Azure portal.

![https://docs.microsoft.com/en-us/learn/modules/host-a-web-app-with-azure-app-service/media/2-deployment-slots.png](https://docs.microsoft.com/en-us/learn/modules/host-a-web-app-with-azure-app-service/media/2-deployment-slots.png)

### Continuous integration/deployment support

The Azure portal provides out-of-the-box continuous integration and deployment with Azure DevOps, GitHub, Bitbucket, FTP, or a local Git repository on your development machine. Connect your web app with any of the above sources and App Service will do the rest for you by automatically syncing your code and any future changes on the code into the web app. Furthermore, with Azure DevOps, you can define your own build and release process that compiles your source code, runs the tests, builds a release, and finally deploys the release into your web app every time you commit the code. All that happens implicitly without any need to intervene.

![https://docs.microsoft.com/en-us/learn/modules/host-a-web-app-with-azure-app-service/media/2-continuous-integration.png](https://docs.microsoft.com/en-us/learn/modules/host-a-web-app-with-azure-app-service/media/2-continuous-integration.png)

### Integrated Visual Studio publishing and FTP publishing

In addition to being able to set up continuous integration/deployment for your web app, you can always benefit from the tight integration with Visual Studio to publish your web app to Azure via Web Deploy technology. App Service also supports FTP-based publishing for more traditional workflows.

### Built-in auto scale support (automatic scale-out based on real-world load)

Baked into the web app is the ability to scale up/down or scale out. Depending on the usage of the web app, you can scale your app up/down by increasing/decreasing the resources of the underlying machine that is hosting your web app. Resources can be number of cores or the amount of RAM available.

Scaling out, on the other hand, is the ability to increase the number of machine instances that are running your web app.

## Creating a web app

When you're ready to run a web app on Azure, you visit the Azure portal and create a **Web App** resource. Creating a web app allocates a set of hosting resources in App Service, which you can use to host any web-based application that is supported by Azure, whether it be ASP.NET Core, Node.js, Java, Python, etc.

The Azure portal provides a wizard to create a web app. This wizard requires the following fields:

- Subscription - A valid and active Azure subscription.
- Resource group - A valid resource group.
- App name - The name of the web app. This name becomes part of the app's URL, so it **must be unique among all Azure App Service web apps**.
- Publish - You can deploy your application to App Service as **code** or as a ready-to-run **Docker image**. Selecting **Docker image** will activate the Docker tab of the wizard, where you provide information about the Docker registry from which App Service will retrieve your image.
- Runtime stack - If you choose to deploy your application as code, App Service needs to know what runtime your application uses (examples include Node.js, Python, Java, and .NET). If you deploy your application as a Docker image, you will not need to choose a runtime stack, since your image will include it.
- Operating system - App Service can host applications on Windows or Linux servers. See below for additional information.
- Region - The Azure region from which your application will be served.
- App Service Plan - See below for information about App Service plans.

### Operating systems

If you are deploying your app as code, many of the available runtime stacks are limited to one operating system or the other. After choosing a runtime stack, the toggle will indicate whether or not you have a choice of operating system. If your target runtime stack is available on both operating systems, select the one that you use to develop and test your application.

If your application is packaged as a Docker image, choose the operating system on which your image is designed to run.

Selecting **Windows** activates the Monitoring tab, where you have the option to enable **Application Insights**. Enabling this feature will configure your app to automatically send detailed performance telemetry to the Application Insights monitoring service without requiring any changes to your code. **Application Insights can be used from Linux-hosted apps as well, but this turnkey, no-code option is only available on Windows.**

### App Service plans

An **App Service plan** is a set of virtual server resources that run App Service apps. A plan's **size** (sometimes referred to as its **sku** or **pricing tier**) determines the performance characteristics of the virtual servers that run the apps assigned to the plan and the App Service features that those apps have access to. Every App Service web app you create must be assigned to a single App Service plan that runs it.

A single App Service plan can host multiple App Service web apps. In most cases, the number of apps you can run on a single plan will be limited by the performance characteristics of the apps and the resource limitations of the plan.

App Service plans are the unit of billing for App Service. The size of each App Service plan in your subscription, in addition to the bandwidth resources used by the apps deployed to those plans, determines the price that you pay.

**The number of web apps deployed to your App Service plans has no effect on your bill.**

You can use any of the available Azure management tools to create an App Service plan. When you create a web app via the Azure portal, the wizard will help you to create a new plan at the same time if you don't already have one.

# Exercise - Create a web app in the Azure portal

In this unit, you will use the Azure portal to create a web app.

## Create a web app

Sign into the Azure portal using the same account you activated the sandbox with.

On the Azure portal menu or from the **Home** page, select **Create a resource**. Everything you create on Azure is a resource.

The portal navigates you to the **Marketplace** page. From here, you can search for the resource you want to create or select one of the popular resources that people create in the Azure portal.

Select **Web** > **Web App** to display the web app creation wizard.

Fill out the wizard with the following values:

- **Subscription** - Concierge Subscription - The web app you are creating must belong to a resource group. Here, you select the Azure subscription to which the resource group belongs (or will belong, if you are creating it within the wizard).
- **Resource group** - Select learn-6af2cfea-2d87-4679-b320-384cdb8b80d9 from the menu - The resource group to which the web app will belong. All Azure resources must belong to a resource group.
- **Name** - Enter a unique name `demorb802` - The name of your web app. This name will be part of the app's URL: _appname_.azurewebsites.net. The name you choose **must be unique among all Azure web apps**.
- **Publish** - Code - The method you will use to publish your application. When publishing your application as code, you also must configure Runtime stack to prepare your App Service resources to run your app.
- **Runtime stack** - Node LTS - The platform on which your application runs. Your choice may affect whether you have a choice of operating system - for some runtime stacks, App Service supports only one operating system.
- **Operating system** - Linux - The operating system used on the virtual servers that run your app.
- **Region** - Select West US or Central US - The geographical region from which your app will be hosted.
- **Linux Plan** - Leave default - The name of the App Service plan that will power your app. By default, the wizard will create a new plan in the same region as the web app.
- **Sku and size** - F1 - The pricing tier of the plan being created. This determines the performance characteristics of the virtual servers that power your app, and the features it has access to. To select the F1 tier, select **Change size** to open the Spec Picker wizard. On the **Dev / Test** tab, select **F1** from the list, then select **Apply**.
- Select **Review and Create** to navigate to the review page, then select **Create** to create the app.

![https://docs.microsoft.com/en-us/learn/modules/host-a-web-app-with-azure-app-service/media/3-create-web-app-node.png](https://docs.microsoft.com/en-us/learn/modules/host-a-web-app-with-azure-app-service/media/3-create-web-app-node.png)

NOTE: It can take a few seconds to get your web app created and ready for your use.

The portal will display the deployment page, where you can view the status of your deployment. Once the app is ready, navigate to the new app in the Azure portal:

On the Azure portal menu or from the **Home** page, select **All resources**.

Select the App Service for your web app from the list. Make sure to select the App Service, and not the App Service plan.

![https://docs.microsoft.com/en-us/learn/modules/host-a-web-app-with-azure-app-service/media/3-web-app.png](https://docs.microsoft.com/en-us/learn/modules/host-a-web-app-with-azure-app-service/media/3-web-app.png)

The portal displays the web app overview page.

![https://docs.microsoft.com/en-us/learn/modules/host-a-web-app-with-azure-app-service/media/3-web-app-home.png](https://docs.microsoft.com/en-us/learn/modules/host-a-web-app-with-azure-app-service/media/3-web-app-home.png)

To preview your new web app's default content, select its **URL** at the top right. The placeholder page that loads indicates that your web app is up and running and ready to receive deployment of your app's code.

![https://docs.microsoft.com/en-us/learn/modules/host-a-web-app-with-azure-app-service/media/3-web-app-online-node.png](https://docs.microsoft.com/en-us/learn/modules/host-a-web-app-with-azure-app-service/media/3-web-app-online-node.png)

# Exercise - Write code to implement a web application

In this unit, you will use developer tools to create the code for a starter web application.

## Create a new web project

```sh

```

```sh

```

```sh

```

```sh

```

```sh

```

# Deploy code to App Service

# Exercise - Deploy your code to App Service

# Summary