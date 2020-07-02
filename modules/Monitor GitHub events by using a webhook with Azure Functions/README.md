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

In the left-hand pane of the portal, select All resources, and then select the function app you just created.

On the function app page, expand your new function app, and then select the + button next to Functions.

Select **HTTP Trigger**, and then select **Create**.

```js
module.exports = async function (context, req) {
  context.log("JavaScript HTTP trigger function processed a request.")

  if (req.query.name || (req.body && req.body.name)) {
    context.res = {
      // status: 200, /* Defaults to 200 */
      body: "Hello " + (req.query.name || req.body.name),
    }
  } else {
    context.res = {
      status: 400,
      body: "Please pass a name on the query string or in the request body",
    }
  }
}
```

This is the default JavaScript function generated by the portal. It reads a name either passed in as a query string or as part of the request body, and responds with a "Hello" message.

On the right-hand side of the screen, click View files, and then click function.json. This file contains the binding information for the function:

The binding specifies that the function is triggered by HTTP GET and POST requests directed towards the URL of the function app.

### Test triggering your function

In your new function, select </> Get function URL at the top.

In the Get function URL message box, select default (Function key), and then select Copy. The URL will look something like this [https://rbtriggerapp.azurewebsites.net/api/HttpTrigger1](https://rbtriggerapp.azurewebsites.net/api/HttpTrigger1)

Paste your function URL into your browser's address bar. Add the query string value `&name=Rob%20Brennan` to the end of this URL and press the Enter key on your keyboard to execute the request - [https://rbtriggerapp.azurewebsites.net/api/HttpTrigger1?name=Rob%20Brennan](https://rbtriggerapp.azurewebsites.net/api/HttpTrigger1?name=Rob%20Brennan)

Congratulations! You now have a function that can be triggered by a URL!

## Set up a webhook for a GitHub repository

In GitHub, webhooks can be set up on an organization or a specific repository. The webhook will be triggered each time one or more subscribed events occurs. For example, the Gollum event allows you to listen for wiki updates; specifically creation and updates for a wiki page.

In this unit, you'll learn how to set up a webhook listening for a change event from your company's GitHub wiki.

### Setting up a webhook

Setting up a webhook is a two-step process. You specify how you want your webhook to behave through GitHub and what events it should listen to. Then you set up your function in Azure Functions to receive and manage the payload received from the webhook.

In GitHub, you can install webhooks for an organization or a repository. In the example scenario case, we're setting up a webhook on a specific repository.

To set up the webhook, go to the settings page of your repository in the GitHub portal. Click Webhooks, and then click Add webhook. Alternatively, you can build and manage a webhook through the Webhooks API, but this module doesn't cover this approach.

Webhooks require a couple of configuration options before you can use them. We'll go through each of these settings next.

#### Payload URL

The payload URL is the URL of the server that will receive the webhook POST requests.

Each event type has a specific payload format. That payload contains information about the event that triggered the webhook.

#### Content Type

Webhooks can be delivered using two different content types:

- The application/json content type delivers the JSON payload directly as the body of the POST request.
- The application/x-www-form-urlencoded content type sends the JSON payload as a form parameter, called payload.

The Add webhook page also enables you to specify how to protect the information sent by the webhook by using a Secret. This topic is covered later in this module.

### Events

Events are at the center of webhooks. Events occur whenever actions are taken in the repository. When the event occurs, the webhook fires off and calls the URL that you specify, sending along the payload and event information to your URL.

A full list of webhook events, and when they can run, can be found in the [Webhooks events reference](https://developer.github.com/webhooks/#events). For example, to respond whenever an issue is raised in a repository, click **Let me select individual events**, and then select **Issues**. Make sure you select **Active** to receive issue events for triggered webhooks.

To listen for updates to the wiki for the repository, select the _Wiki_ checkbox; this event is the _Gollum_ event mentioned earlier. In the next exercise, we'll walk you through the process of setting up a webhook for a GitHub repository.

## Exercise - Set up a webhook for a GitHub repository

In this exercise, you'll set up a webhook for a GitHub repository. You'll learn how to listen for specific events, in this case the Gollum event, and how to make your webhook callback to your function when the event is triggered.

### Setup

Using your web browser, sign in to your [GitHub account](https://www.github.com/)

Create a new public repository by clicking the New button and give it a meaningful name such as **WebhookWiki**.

Select the Wiki tab.

Select Create the first page.

Add some text, and then select Save Page.

### Add a webhook for the Gollum Event

The **Gollum** event is the name of the event in GitHub that is fired whenever a page in the repo's wiki is created or updated.

Go back to main page for your repository.

Select the Settings tab.

Select Webhooks in the navigation panel to the left of the page.

Select the Add webhook button on the top-right.

Set the payload to the URL for your function app's function from the previous exercise. Remember that it looks similar to this - `https://rbtriggerapp.azurewebsites.net/api/HttpTrigger1`

Set the content type to `application/json`.

In the section Which events would you like to trigger this webhook?, select the radio button labeled Let me select individual events.

Select the Wiki checkbox. Make sure no other check boxes are selected.

At the bottom of the page, ensure Active is checked and select Add webhook.

Verify that your new webhook appears when the Webhooks page is displayed.

### Test the webhook

In the GitHub portal for your repository, click the Wiki tab.

Select the page that you created earlier.

Select Edit.

In the text area of the page, replace the text with something.

Select Save Page to save your update.

Select the Settings tab.

Click Webhooks in the navigation panel on the left.

The webhook will indicate that the message was not processed correctly; it will generate an HTTP 400 error. The webhook is providing a payload that your function app wasn't expecting, and doesn't include a name parameter. You will learn how to parse the payload for a Gollum event in the next unit.

Select Edit next to your webhook.

Scroll down to the Recent Deliveries section.

Select the latest delivery entry by clicking on the ellipsis (...).

You'll also see that the payload contains information indicating that your wiki page was edited. The payload contains pages, repository, and sender sections, which should look something like the example shown below:

Select the Response tab.

You'll see the response message generated by the Azure function. For this example, the body should contain the message. Please pass a name on the query string or in the request body.

## Trigger an Azure Function with a GitHub event

## Exercise - Trigger an Azure Function with a GitHub event

## Secure Webhook payloads with a secret

## Exercise - Secure webhook payloads with a secret

## Summary
