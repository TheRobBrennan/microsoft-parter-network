[Monitor GitHub events by using a webhook with Azure Functions](https://docs.microsoft.com/en-us/learn/modules/monitor-github-events-with-a-function-triggered-by-a-webhook/) (~53 mins)

Webhooks offer a lightweight mechanism for your app to be notified by another service when something of interest happens. In this module. you'll learn how to trigger an Azure function with a GitHub webhook and parse the payload for insights.

In this module, you will:

- Trigger your function with a webhook
- Analyze the message from the webhook
- Secure webhook payloads with a secret

Prerequisites:

- Beginner-level knowledge of Azure Functions
- A GitHub account

# Notes and key takeaways

## Introduction

Webhooks offer a lightweight mechanism for apps to be notified by another service when something of interest happens via an HTTP endpoint. You can use a webhook to trigger an Azure function, and then analyze the message, to determine what happened and how to respond.

Suppose you are a developer in an IT department. Management wants a regular report on updates to the company's technology Wiki. They'd like to know who contributes, how often it is updated and what pages are updated most often. The Wiki is hosted on GitHub as a GitHub Wiki on one of the company's git repositories. You've heard that GitHub supports webhooks and you can receive notifications for all kinds of events. You are also familiar with Azure Functions and decide to use a function as the listener for Wiki update events.
By the end of this module, you'll learn how to create a function triggered by a webhook and how to secure a webhook message, or payload, with a secret.

## Create a function that is triggered by a webhook

You have decided on using Azure Functions to monitor the company GitHub wiki for updates. Each time the wiki is updated, you want to be able to trigger a function that can process details about the update, such as updated date, contributor name, and so on. The first step is to create a Azure function app that contains a function triggered by a webhook.

In this unit, you'll learn about the benefits of using webhooks, and how you can trigger an Azure Function using webhooks.

### What is a webhook?

Webhooks are user-defined HTTP callbacks. They're triggered by some event, such as pushing code to a repo or updating a wiki page. When the event occurs, the source site makes an HTTP request to the URL configured for the webhook. With Azure Functions, we can define logic in a function that can be run when a webhook message is received.

One common use of webhooks in a DevOps environment is to notify an Azure function that the code or configuration for an application has changed in GitHub. The payload of the message sent through the webhook contains the details of the event. You can use the webhook with a function to perform a task such as deploying the updated version of the application.

### What is Azure Functions?

Azure Functions is a serverless compute service. It enables you to run code without having to explicitly provision or manage any infrastructure. You can use Azure Functions to run script or code in response to a variety of events.

A _Trigger_ causes a function to run. A trigger defines how a function is invoked. A function must have exactly one trigger. Triggers have associated data, which is often provided as the payload of the function.

A _Binding_ is used to connect a resource to a function. You can define input bindings and output bindings. Data from a binding is provided to the function as parameters.

**For example, you can connect a database to your Azure Functions code by using a binding. Then you don’t need to wire up the database to the function with any connection code.**

Bindings are optional and a function might have one or multiple input and/or output bindings.

## Exercise - Create an Azure function triggered by a webhook

In this first exercise, you'll create your code to parse the GitHub wiki event messages in Azure Functions. You'll configure your function to run when a webhook message is received.

### Create a Function App

Sign into the Azure Portal and create a Function App named `rbtriggerapp` with the following settings as defined [here](https://docs.microsoft.com/en-us/learn/modules/monitor-github-events-with-a-function-triggered-by-a-webhook/3-exercise-create-function-triggered-by-webhook):

Runtime Stack Node.js
Version 12
Region West US 2

### Create a webhook triggered function

### Test triggering your function

## Set up a webhook for a GitHub repository

## Exercise - Set up a webhook for a GitHub repository

## Trigger an Azure Function with a GitHub event

## Exercise - Trigger an Azure Function with a GitHub event

## Secure Webhook payloads with a secret

## Exercise - Secure webhook payloads with a secret

## Summary
