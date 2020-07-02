[Develop, test, and deploy an Azure Function with Visual Studio](https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/) (~43 mins)

Create test and deploy Azure Functions using Visual studio and how to manage Azure Function code.

In this module, you will:

- Develop an Azure Function in Visual Studio
- Test and debug an Azure Function in Visual Studio
- Deploy an Azure Functions directly from Visual Studio to Azure
- Manage Azure Function code

Prerequisites:

- Basic knowledge of Azure Functions
- Experience developing solutions in Visual Studio or a similar integrated development environment
- The exercises in this module require a local installation of Visual Studio 2019.

# Notes and key takeaways

## Introduction

Serverless architecture makes use of infrastructure provided by the cloud. You don't have to provision, manage, scale, and maintain any machinery or networks. Azure Functions is a fully managed PaaS service offered by Microsoft Azure to implement serverless architecture. Azure Functions is a fully scalable, resilient, reliable, and secure service.

Imagine that you're a senior web developer in a research role for an online luxury watch dealer, building a website based on Azure Web Apps. You've been asked to provide new functionality by writing and deploying individual methods that can be deployed, manually executed, scheduled, or even triggered by other Azure Services. The company follows a test-driven development process, so you're also required to write automated unit tests for the new services. You have a team of developers well-versed with Microsoft technologies including the .NET framework and Visual Studio. You decide to investigate the use of Azure Functions to meet your requirements.

By the end of this module, you'll be able to build and test an Azure Function locally using Visual Studio. You'll then see how to deploy the function to Azure from Visual Studio.

## Create and test a simple Azure Function locally with Visual Studio

You can write, debug, and deploy an Azure Function from within the Azure portal. However, there are many scenarios when writing functions directly in the production, staging, or test environments might not be suitable. For example, you could be writing automated unit tests for Azure Functions, or using on-demand deployment of Azure Functions to Azure Function Apps in Azure. Additionally, many developers prefer to use their favorite code editor and development tools rather than the environment provided by the Azure portal. Developing functions with Visual Studio enables you to manage Azure Functions code with code of other services inside the same projects.

In the luxury watch online website scenario, the developers are already familiar with Visual Studio. So, you decide to use Visual Studio as the primary development environment for creating Azure Functions. Additionally, Visual Studio provides an excellent environment for testing your functions locally before deploying them to Azure.

In this unit, you'll learn about the tools available with Visual Studio for building Azure Functions. You'll see how to install these tools, and use them to build and test an Azure Function locally.

### Install the Azure Functions tools extension for Visual Studio

The Azure Functions tools are a Visual Studio extension that allows you to create, test, and deploy Azure Functions in your local development environment. This extension provides a template that you can use to quickly create a new Azure Functions app. You can then deploy an Azure Function directly to Azure from Visual Studio.

You install the Azure Functions tools from the Extensions and Updates command in the Tools menu in Visual Studio. Search for Azure Functions, and then download and install the Azure Functions and Web Job Tools extension.

![https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/media/2-azure-functions-and-web-jobs-extension.png](https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/media/2-azure-functions-and-web-jobs-extension.png)

### Create an Azure Functions app

### Structure of an Azure Function

### Test an Azure Functions app locally

## Exercise - Create and test a simple Azure Function locally with Visual Studio

## Publish a simple Azure Function

## Exercise - Publish a simple Azure Function

## Exercise - Unit test an Azure Function

## Summary
