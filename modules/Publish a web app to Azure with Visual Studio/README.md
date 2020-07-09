[Publish a web app to Azure with Visual Studio](https://docs.microsoft.com/en-us/learn/modules/publish-azure-web-app-with-visual-studio/) (~48 mins)

# Introduction

Imagine you're a software developer working for a ski resort. You are launching a new application, which will let users view trail maps and purchase lift tickets on your website or from their mobile phones. You want to create an ASP.NET Core web application to complement and advertise the app. Being a Visual Studio developer, you want to use Visual Studio 2019 to create, deploy, and manage your new site.

Visual Studio and Azure App Service provide a powerful mechanism for creating, publishing, and maintaining web apps in Azure. Here, you will learn how to use the publishing features built in to Visual Studio to deploy and manage ASP.NET Core web applications hosted on Azure.

## Learning objectives

In this module, you will:

- Create an ASP.NET Core web app in Visual Studio
- Publish a web app to Azure using Visual Studio
- Update a web app in Visual Studio and publish the changes to Azure

## Prerequisites

- Knowledge of the Azure cloud environment
- Familiarity with Visual Studio 2019
- Familiarity with web app concepts
- Basic programming knowledge
- Basic HTML knowledge
- A local installation of [Visual Studio 2019 16.6 or a later version](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

# Install the necessary workloads

The first step in getting your new site ready is to prepare your development environment. Creating and deploying ASP.NET Core web applications requires that you have the necessary tools installed on your local machine. Here, we will cover the development tools you need and how to install them.

## Configure your Development environment

We'll need to install additional tools within Visual Studio to build, debug, and deploy ASP.NET Core web applications to Azure. To do this, we'll install two Visual Studio workloads.

### What are Visual Studio workloads?

A _workload_ is a pre-configured bundle of tools within Visual Studio that are grouped to enable developers to build certain types of applications, use certain development languages, or develop for specific platforms.

For example, the _Desktop development with C++ workload_ includes Visual Studio features that let you run and debug C++ console applications, the _Mobile development with .NET workload_ installs all of the tools needed to create mobile applications with .NET.

### Visual Studio workloads for ASP.NET Core development and publishing

Visual Studio 2019 has two workloads that you need to create, publish, and deploy your website to Azure. These workloads include the templates for your ASP.NET Core site and provides the ability to connect and deploy your site to Azure.

With Visual Studio 2019 installed, you need to make sure you have the following Visual Studio workloads installed:

- **ASP.NET and web development**: The web development workload in Visual Studio is designed to maximize your productivity in developing web applications using ASP.NET and standards-based technologies like HTML and JavaScript.
- **Azure development**: The Azure development workload in Visual Studio installs the latest Azure SDK for .NET and tools for Visual Studio. Once these items are installed, you can view resources in Cloud Explorer, create resources using Azure Resource Manager tools, build applications for Azure web and Cloud Services, and perform big data operations using Azure Data Lake tools.

## Installing Visual Studio workloads

You use the Visual Studio Installer to modify the components installed as part of Visual Studio including workloads.

To launch the Installer, from your Windows Start menu, scroll down to **V**, and then click **Visual Studio Installer**. Alternatively, while the Start menu is open, you can just type `Visual Studio Installer` to find the Installer link. Then select **Enter**.

The Visual Studio Installer window appears. Click the **Modify** button.

Ensure the **ASP.NET and web development** and **Azure development** workloads are selected under the **Web & Cloud** section of the Workloads tab.

Next, click the **Modify** button on the bottom right of the Installer. The Visual Studio Installer will download and install the necessary components.

When the install completes, click **Launch** to open Visual Studio.

Workloads are added to your local Visual Studio installation and only need to be installed once. You can launch the Visual Studio Installer in the future to add additional workloads, customize installed workloads, or remove them.

To complete the exercises in this module, you'll need the ASP.NET and web development and Azure development workloads installed. If you haven't already, install these workloads before moving onto the next exercise.

# Exercise - Create a new ASP.NET Core app

In this unit, you'll create, build, and run a new ASP.NET Core web application on your local machine. You'll need Visual Studio 2019 installed with the ASP.NET and web development and Azure development workloads installed.

## Create an ASP.NET Core project

Open Visual Studio 2019 on your local machine.

From the Visual Studio landing page under **Get started**, click **Create a new project**.

In the search box, enter **Web**.

From the search results, select **ASP.NET Core Web Application**.

Select the **Next** button.

In the new project dialog, set the **Name** field to `AlpineSkiHouse`.

Select a **Location** for your new solution.

Click the **Create** button to create your project.

In the **Create a new ASP.NET Core web application** dialog box, you'll see a selection of starting templates. For this exercise, select **Web Application**, and then click **Create** to create your project.

![https://docs.microsoft.com/en-us/learn/modules/publish-azure-web-app-with-visual-studio/media/3-aspnet-templates.png](https://docs.microsoft.com/en-us/learn/modules/publish-azure-web-app-with-visual-studio/media/3-aspnet-templates.png)

## Build and test on your local machine

Now, let's build and test your application on your local machine before deploying to Azure.

Run the app

Press `F5` to build the project and run in debug mode.

Press `Ctrl+F5` to build the project and run without attaching the debugger.

**Launching the app in non-debug mode allows you to make code changes, save the file, refresh the browser, and see the code changes. Many developers prefer to use non-debug mode to quickly launch the app and view changes.**

Visual Studio starts the IIS Express web browser and loads the app.

![https://docs.microsoft.com/en-us/learn/modules/publish-azure-web-app-with-visual-studio/media/3-webapp-launch-windows.png](https://docs.microsoft.com/en-us/learn/modules/publish-azure-web-app-with-visual-studio/media/3-webapp-launch-windows.png)

When Visual Studio creates a web project, a random port is used for the web server. In the preceding image, the port number is 44381. When you run the app, you'll likely see a different port number.

IMPORTANT: You might notice the section at the top of the web page that provides a place for your privacy and cookie use policy. Select Accept to consent to tracking. This app doesn't track personal information. The template-generated code includes assets to help meet General Data Protection Regulation (GDPR).

You've now created a web application from the sample template and it's running locally. The next step is to deploy it to Azure

# Explore the Azure App Service

You created a new site, your next step is to deploy it to Azure. We need to consider which Azure services to leverage. Azure App Service provides a highly scalable, self-patching web hosting service for your applications.

Here, we look at how to use Visual Studio to publish your ASP.NET Core web application to an Azure App Service plan.

## What is the Azure App Service?

Azure App Service is a service for hosting web applications, REST APIs, and backend services. App Service supports code written in .NET Core, .NET Framework, Java, Ruby, Node.js, PHP, and Python. App Service is ideal for most websites, particularly if you don't need tight control over the hosting infrastructure.

## What is the App Service plan?

The App Service plan defines the compute resources your app will consume, where those resources are located, how many additional resources the plan can consume, and the pricing tier. **These compute resources are analogous to the server farm in conventional web hosting**. One or more apps can be configured to run on the same App Service plan.

When you deploy your apps, you can create an App Service plan or you can continue to add apps to an existing plan. However, apps in the same App Service plan share the same compute resources. To determine whether the new app has the necessary resources, you need to understand the capacity of the existing App Service plan, and the expected load for the new app. Overloading an App Service plan can cause reduced performance or downtime for your new and existing apps.

You can define an App Service plan in advance in the Azure portal with PowerShell or the Azure CLI, or set one up as you publish your application in Visual Studio.

Each App Service plan defines:

- Region (West US, East US, and so on)
- Number of VM instances
- Size of VM instances (small, medium, large)
- Pricing tier (Free, Shared, Basic, Standard, Premium, Premium V2, Isolated)

## Select a region

When creating an App Service plan, you have to define a region or location where that plan will be hosted. Typically, you would choose a region geographically close to your expected customers.

## Pricing and reliability levels

**Shared compute**: **Free** and **Shared**, the two base tiers, **run an app on the same Azure VM as other App Service apps, including apps of other customers**. These tiers allocate CPU quotas to each app that runs on the shared resources, and the resources cannot scale-out.

**Free and Shared plans are best for small-scale personal projects with limited traffic demands, with a set limit of 165 MB of outbound data every 24 hours.**

**Dedicated compute**: The **Basic**, **Standard**, **Premium**, and **Premium V2** tiers run apps on dedicated Azure VMs. **Only apps in the same App Service plan share the same compute resources.** The higher the tier, the more VM instances are available to you for scale out.

The Standard service plan is best suited for live production workloads, where you are publishing commercial applications to customers.

The Premium service plans support high-capacity web apps where you do not want the additional costs of a dedicated (isolated) plan.

**Isolated**: This tier runs dedicated Azure VMs on dedicated Azure virtual networks, which **provide network isolation on top of compute isolation** to your apps. It provides the maximum scale-out capabilities. You would only select an Isolated service plan when you have a specific requirement for the highest levels of security and performance.

Isolate your app into a new App Service plan when:

- The app is resource-intensive
- You want to scale the app independently from the other apps in the existing plan
- The app needs resources in a different geographical region

Your App Service plan can be scaled up and down at any time. You can choose a lower pricing tier at first and scale up later when you need more App Service features.

## Specify the resource group

A resource group is a logical container into which Azure resources like web apps, databases, and storage accounts are deployed and managed. It is a mechanism for organizing resources for the purpose of management, monitoring and billing. You can use an existing resource group or create one directly from Visual Studio.

# Exercise - Publish an ASP.NET app from Visual Studio

You have an ASP.NET Core web application running locally. In this exercise, you'll publish your application to Azure App Service.

## Publish your ASP.NET Core web app to Azure

In Solution Explorer, right-click on the **AlpineSkiHouse** project and select **Publish**.

In the dialog box that appears, choose **Azure** as your publish target, and then select the **Next** button to continue.

Select **Azure App Service (Windows)**, and then select the Next button to continue.

Near the bottom edge of the dialog, select the **Create a new Azure App Service** link to open the **App Service (Windows)** dialog.

### Configure your new Azure App Service

If you're not already signed in, sign into Visual Studio with the account you're using with Microsoft Learn.

Enter the required information about your App Service plan.

- Name: the name of your application. The name determines the URL of the published application, which will be https://<AppName>.azurewebsites.net. It must be a unique value. You may have to try out some different names to find one that is unique.
- Subscription: The Azure subscription you wish to deploy the app to. Choose **Concierge Subscription**, which we provide to you through the sandbox.
- Resource Group: Select the existing learn-533fc26e-806d-428e-8593-44d20f20e330 resource group.
- Hosting Plan: The hosting plan specifies the location, size, and features of the web server farm that hosts your app. For this exercise, create a new hosting plan.

Click the New button next to the hosting plan. In the Configure Hosting Plan window that appears, change the **Size** to Shared and click OK.

Click the **Create button** to create your App Service resource in Azure. This action will take several seconds to complete.

After several seconds, the App Service (Windows) dialog window will disappear. Your new App Service displays in the list of App Service resources on the Publish dialog window. Press the Finish button to finish the creation of the publishing profile. The Publish dialog disappears.

Your new publishing profile appears in the dropdown near the top of the property page. Click Publish to publish the web app to App Service.

Congratulations! Your ASP.NET Core web application is now published and live. The final URL for your site is in the build output and also on the publishing page in Visual Studio.

To test your website, go to the URL indicated.

![https://docs.microsoft.com/en-us/learn/modules/publish-azure-web-app-with-visual-studio/media/5-webpagelive.png](https://docs.microsoft.com/en-us/learn/modules/publish-azure-web-app-with-visual-studio/media/5-webpagelive.png)

You now have a live web app! Your Azure App Service plan has been created and the app is running and ready to accept updates.

# Explore your Visual Studio App Project

You've successfully created your web app and published it to Azure, but what happens when you want to make changes? Visual Studio will remember where the app is published, which makes updating and changing your app a two-click process.

## Explore the project structure

We've created an ASP.NET Core web app in Visual Studio, and now you will need to edit and customize your website. Let's explore the project structure to see what Visual Studio has created for us.

### Dependencies

Dependencies include the ASP.NET Core internals to get your app up and running. Unless you are adding specific third-party packages, you won't need to spend much time in this folder.

### Properties

The properties folder contains configuration data for where you are hosting your web app. If you expand the **PublishProfiles** folder now, you should see the URL for the Alpine Ski Hill site. Each publishing profile is an .xml file containing publishing configuration information, such as the Azure address that Visual Studio uses to upload your files.

### wwwroot

The wwwroot file contains all of your static assets for your site, such as the css, js, images, and lib files. When you are ready to style and add more functionality to your site, you will work in here.

### Pages

The **Pages** folder includes Razor templates for the pages of your site. Razor is a syntax that is built up around HTML, which has special conventions for displaying data and executing logic on your site.

Each page in your site is represented with two code files:

- The first is a `.cshtml` file, which is the Razor markup file. This file includes your display HTML and some C# logic.
- The second file is a `.cs` file, which is the C# code-behind that includes `PageModel` class. This file allows you to intercept HTTP requests and do some processing on that request before passing off any data to the Razor file.

### appsetting.json

This is a configuration file for ASP.NET Core.

### Program.cs and Startup.cs

Program.cs and Startup.cs configure and launch your web host for your site.

## Introduction to Razor templates

We will want to make some basic changes to our website. You will need to have a basic understanding of how to leverage the Razor templates to customize your web app.

## What is Razor?

Razor is an ASP.NET syntax used to create dynamic web pages with C#. When a server reads a Razor page, the C# code is run before it renders the HTML. This allows you to generate dynamic content quickly.

Razor uses `@` directives to tell ASP.NET how to process a page.

For example, take a look at the code in the `Privacy.cshtml` page:

```csharp
@page
@model PrivacyModel
@{
    ViewData["Title"] = "Privacy Policy";
}
<h1>@ViewData["Title"]</h1>

<p>Use this page to detail your site's privacy policy.</p>
```

- The `@page` directive is telling ASP.NET to process this file as a Razor page.
- The `@model` directive is telling ASP.NET to tie this Razor page with a C# class called `PrivacyModel`.

Razor also uses the `@` symbol to transition between code and HTML. If you look at the preceding snippet, you'll notice @{ ... }`. This is a **Razor code block**. It's code that **is executed but not rendered**.

To render the output of a code statement, use the `@` in front of a C# expression. There's one example in the preceding code block in the `<h1>` tag.

Creating and publishing a website are just the first steps in creating a good website. Once you start to add content, you'll need to update your site. Once you've initially published your site to Azure, you can update at any time.

# Exercise - Publish an update to your site

Your Alpine Ski House web app is up and running, but now you need to show it to your boss. You're going to have to update the site and publish those updates to Azure.

## Update your web app

### Replace the boilerplate code

In the **Pages** folder, open the **Privacy.cshtml** file.

At the bottom of the code, locate `<p>Use this page to detail your site's privacy policy.</p>`.

Replace this boilerplate text with `<p>Welcome to the Alpine Ski House!</p>`.

Save the file.

Open the **Index.cshtml** file.

Replace the content within the `<p>` tags to say **Alpine Ski House is the premier ski hill in Northeast!**

Save the file.

### Publish your updates

In Solution Explorer, right-click the project.

Select **Publish**. A new tab named **AlpineSkiHouse** should open.

Select the **Publish** button to deploy the latest changes.

### View your changes

Once you've published your changes, Visual Studio will open the site in your browser. You should now see your new messages on the home and privacy pages.

Congratulations, you have successfully updated your web app from within Visual Studio 2019.

# Summary
