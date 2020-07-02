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

The GitHub Gollum event enables you to listen for wiki updates. When this event occurs, and our Azure Functions function is triggered, you can parse the payload to retrieve and process the data sent.

You've shown the IT department that you can listen for Gollum events on your company's GitHub repository, by setting up a webhook in GitHub. You've also demonstrated how Azure Functions enables you to run code when the function receives a webhook request. In this unit, we'll examine the payload from the Gollum event so we can update our function to parse it correctly.

### Gollum event payload

The payload for the Gollum event contains the following items:

- `pages`\*\*` The pages that were updated. Each page includes the following information:
- `page_name` The name of the page.
- `title` The current page title.
- `action` The action that was performed on the page. Can be created or edited.
- `html_url` The HTML wiki page. repository Information about the repository containing the wiki page, including:
- `name` The name of the repository.
- `owner` Details of the owner of the repository.
- `html_url` The address of the repository. sender Information about the user that raised the event that caused the webhook to fire.

For example, a payload might look like this:

```js
"pages": [
    {
        "page_name": "Home",
        "title": "Home",
        "summary": null,
        "action": "edited",
        "sha": "562362bc141b9e2db1fb971e1ecb4fd0b7457f68",
        "html_url": "https://github.com/testrepo/Hello-World/wiki/Home"
    }
],
"repository": {
    ...
    "name": "testrepo",
    ...
    "owner": {
        ...
    },
    "html_url": "https://github.com/...",
    ...
},
"sender": {
    "login": "..."
    ...
}
```

This information is passed as the body of an HTTP POST request. We'll need to update our function logic to be able to parse and process this information correctly.

### Parsing information from the Gollum event

Recall that the webhook runs when a specific event occurs. The webhook then sends a request to the URL on which your Azure Functions code is set up to listen, using the URL for your function. The payload is passed to the Azure Function. Your function can parse the request body to extract the fields from the payload and take the appropriate actions.

The following example retrieves the repository name from the payload. The event type is available in the x-github-event request header. This data is output in the function response:

```js
if (req.body.repository.name) {
  context.res = {
    body:
      "Repository is: " +
      req.body.repository.name +
      ", Event Type is: " +
      req.headers["x-github-event"],
  }
}
```

In the next exercise, we'll update our function code to be able to handle incoming Gollum event correctly.

## Exercise - Trigger an Azure Function with a GitHub event

In this exercise, you'll update your function to parse information from the GitHub webhook payload, and display the results.

### Update our function to parse the webhook payload

In the Azure portal, navigate to the function app that you created earlier.

Expand Functions and select the function that you created.

Select View files on the far right of the portal UI, and then select index.js.

Update the index.js file by editing the code in the main window for the function:

```js
module.exports = async function (context, req) {
  context.log("JavaScript HTTP trigger function processed a request.")

  if (req.body.pages[0].title) {
    context.res = {
      body:
        "Page is " +
        req.body.pages[0].title +
        ", Action is " +
        req.body.pages[0].action +
        ", Event Type is " +
        req.headers["x-github-event"],
    }
  } else {
    context.res = {
      status: 400,
      body: "Invalid payload for Wiki event",
    }
  }
}
```

This code retrieves the event type from the request header, and the title and action fields from the message body. This information indicates that page has changed, and whether it has been edited or newly created. The code constructs a response that summarizes the action.

Select Save.

### Trigger your Azure Function with a Gollum event

Return to your GitHub account.

Select the repository you are using for this module.

Select the Settings tab.

Select Webhooks in the navigation panel.

Select the Edit button next to your webhook.

Scroll down to the Recent Deliveries section.

Select the latest delivery entry by clicking the ellipsis button (...).

Select Redeliver.

In the message box that appears, select Yes, redeliver this payload. This action simulates you changing your Wiki page again.

Select the Response tab. You'll see how the webhook, has triggered your function, which then parsed the information and sent back a response similar to the following text:

## Secure Webhook payloads with a secret

Once your function is configured to receive payloads, it will listen for any payload sent to the endpoint you configured. For security reasons, you might want to limit requests to those coming from GitHub. There are a few ways to go about this. For example, you could opt to approve requests from GitHub's IP address. An easier method is to set up a secret token and validate the request using this token.

In the example scenario, your IT Department's management are happy with the webhook triggered function that you've created in an Azure Functions app. All of the information regarding updates to the company wiki are being parsed by that function and sent to the business, each time the Gollum event is triggered. The management has asked how secure the information passed from GitHub is. They've asked you to find a way to secure the information, and verify it's GitHub that is sending updates.

In this unit, you'll learn how to secure your webhook payload with a secret and validate payloads from GitHub.

### Webhook secrets

Setting a webhook secret allows you to ensure that POST requests sent to the payload URL are from GitHub. When you set a secret, you'll receive the `x-hub-signature` header in the webhook POST request.

In GitHub, you can set the _secret_ field by going to the repository where you have setup your webhook, and then editing the webhook. We'll show you how to do that for our example in the next exercise.

### Validating payloads from GitHub

When your secret token is set, GitHub uses it to create a hash signature for each payload. This hash signature is passed along with each request in the headers as `x-hub-signature`.

When your function receives a request, you need to compute the hash using your secret, and ensure that it matches the hash in the request header. GitHub uses an HMAC SHA1 hex digest to compute the hash, so you must calculate your hash in this same way, using the key of your secret and your payload body. The hash signature starts with the text sha1=.

## Exercise - Secure webhook payloads with a secret

In this exercise, you'll protect your webhook payload with a secret, and learn how to validate payloads from GitHub inside an Azure Function.

### Get Key for your Azure Function

In the Azure portal, return to your function app that you created from the first exercise in the module.

Expand Functions.

Select the function that you created.

In your function's index.js JavaScript file, add a reference to the crypto-js library at the start of the file, above the module.exports statement:

In the side menu, select Manage.

In the Function Keys section, select Click to show next to the default key.

Under Actions, select Copy and save this key. You will paste it where `<default key>` appears. Your new Azure Function should look like:

```js
var Crypto = require("crypto")

module.exports = async function (context, req) {
  context.log("JavaScript HTTP trigger function processed a request.")

  var hmac = Crypto.createHmac("sha1", "<default key>")
  var signature = hmac.update(JSON.stringify(req.body)).digest("hex")
  var shaSignature = `sha1=${signature}`
  var gitHubSignature = req.headers["x-hub-signature"]

  if (!shaSignature.localeCompare(gitHubSignature)) {
    if (req.body.pages[0].title) {
      context.res = {
        body:
          "Page is " +
          req.body.pages[0].title +
          ", Action is " +
          req.body.pages[0].action +
          ", Event Type is " +
          req.headers["x-github-event"],
      }
    } else {
      context.res = {
        status: 400,
        body: "Invalid payload for Wiki event",
      }
    }
  } else {
    context.res = {
      status: 401,
      body: "Signatures don't match",
    }
  }
}
```

### Update the webhook secret

Switch to your GitHub account in the GitHub portal.

Select your repository.

Select the Settings tab.

Select Webhooks in the navigation panel.

Select the Edit button next to your webhook.

In the Secret text box, enter the default key from your function that you saved earlier in this exercise.

At the bottom of the page, select Update webhook.

### Test the webhook and the Azure Function

On the webhooks page, scroll down to the Recent Deliveries section.

Select the latest delivery entry by clicking the ellipsis (...) button.

Click Redeliver, and then select Yes, redeliver this payload.

This action simulates you changing your Wiki page again.

In the header, you'll see the `x-hub-signature`. You'll also see that the response code is 200, indicating that the request was processed successfully.

### Test an invalid signature

In the GitHub portal, on the webhooks page, scroll up to the Secret test box and then select Edit.

Enter a random string, scroll down, and then select Update webhook.

The key used by the webhook should no longer match that expected by the Azure function.

Scroll down to the Recent Deliveries section.

Select the latest delivery entry by selecting the ellipsis (...) button.

Select Redeliver, and then Yes, redeliver this payload.

This time, you'll see that the response code is 401, indicating that the request was not authorized.

Select the Response tab, and verify that the message "Signatures don't match" appears as the body of the response.

## Summary
