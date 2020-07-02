[Enable automatic updates in a web application using Azure Functions and SignalR Service](https://docs.microsoft.com/en-us/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr/) (~55 mins)

Update a web app's notification mechanism from polling to push-based architecture with SignalR Service, Azure Cosmos DB and Azure Functions.

In this module, you will:

- Evaluate an existing project that implements polling
- Update an application architecture to support push technologies with SignalR Service and Azure Functions
- Host a static website in an Azure Storage account
- Modify a Vue.js and JavaScript web application to use SignalR

Prerequisites:

- Knowledge of JavaScript
- Familiarity with Azure Functions, Azure Cosmos DB and Visual Studio Code

# Notes and key takeaways

## Introduction

Consider the following scenario. Your company is planning to launch a new website that provides stock price information to customers. Recently, an intern created a website prototype for the new application and the Lead Architect has now asked you to step in and improve the solution.

Your objective is to update the app to implement automatic updates of the stock price information, but ensure communication between the client and server happens only when data changes on the server.

### Learning objectives

In this module, you will:

- Implement a function in Azure Functions that runs only when data changes in an Azure Cosmos DB.
- Implement a function in Azure Functions to broadcast changes to connected clients using SignalR Service.
- Update the client web application to respond to SignalR messages.
- Modify a Vue.js and JavaScript web application to use SignalR

### Prerequisites

- Ability to read JavaScript.
- Familiarity with Azure Functions, Azure Cosmos DB, Azure Storage, and Visual Studio Code.

## Analyze the limitations of a polling-based web app

## Exercise - Analyze the limitations of a polling-based web app

## Enable automatic updates in a web application using SignalR Service

## Exercise – Enable automatic updates in a web application using SignalR Service

## Use a storage account to host a static website

## Exercise - Use a storage account to host a static website

## Summary
