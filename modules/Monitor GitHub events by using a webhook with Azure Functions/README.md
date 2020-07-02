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

## Exercise - Create an Azure function triggered by a webhook

## Set up a webhook for a GitHub repository

## Exercise - Set up a webhook for a GitHub repository

## Trigger an Azure Function with a GitHub event

## Exercise - Trigger an Azure Function with a GitHub event

## Secure Webhook payloads with a secret

## Exercise - Secure webhook payloads with a secret

## Summary
