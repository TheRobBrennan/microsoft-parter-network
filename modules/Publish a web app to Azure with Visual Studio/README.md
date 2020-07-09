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

## Build and test on your local machine

# Explore the Azure App Service

# Exercise - Publish an ASP.NET app from Visual Studio

# Explore your Visual Studio App Project

# Exercise - Publish an update to your site

# Summary
