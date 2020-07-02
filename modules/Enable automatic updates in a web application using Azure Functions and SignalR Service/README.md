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

The application’s current architecture reports stock information by fetching changes from the server based on a timer. This design is often called a polling-based design.

Before we analyze any limitations, let's review the current architecture. The server is responsible for storing stock information and the client renders data in the browser.

We'll set up the current solution on your local machine in the next unit.

### Server

The stock price information is stored on the server in an Azure Cosmos DB database. When triggered by an HTTP request, the function uses bindings to return content from the database.

The function named `getStocks` is responsible for reading the stock information from the database.

As mentioned, the connection to the Azure Cosmos DB database is achieved by using an input binding. This binding is configured in the `function.json` file, as shown in the following snippet.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "direction": "in",
      "name": "req",
      "methods": ["get"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "cosmosDB",
      "direction": "in",
      "name": "stocks",
      "ConnectionStringSetting": "AzureCosmosDBConnectionString",
      "databaseName": "stocksdb",
      "collectionName": "stocks"
    }
  ]
}
```

For a detailed explanation of what these properties mean in our bindings, please refer to [https://docs.microsoft.com/en-us/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr/2-analyze-limitations-of-polling-in-a-web-app](https://docs.microsoft.com/en-us/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr/2-analyze-limitations-of-polling-in-a-web-app)

With these bindings, GET requests to `getStocks` make data available through the `stocks` parameter. As you can see in the following code snippet, the function code to retrieve stock information is trivial thanks to the power of Azure Functions bindings.

```js
module.exports = async function (context, req, stocks) {
  context.res.body = stocks
}
```

### Client

The sample client uses Vue.js to compose the UI and the axios HTTP client to handle requests to the function.

The page uses a timer to send a request to the server every five seconds to request stocks. The response returns an array of stocks, which are then displayed to the user.

```js
const LOCAL_BASE_URL = "http://localhost:7071"

const app = new Vue({
  el: "#app",
  interval: null,
  data() {
    return {
      stocks: [],
    }
  },
  methods: {
    async update() {
      try {
        const apiUrl = `${LOCAL_BASE_URL}/api/getStocks`
        const response = await axios.get(apiUrl)
        app.stocks = response.data
      } catch (ex) {
        console.error(ex)
      }
    },
    startPoll() {
      this.interval = setInterval(this.update, 5000)
    },
  },
  created() {
    this.update()
    this.startPoll()
  },
})
```

The `update` method is called every five seconds once polling is started by the `startPoll` method. Inside the update method, a `GET` request is sent to the `getStocks` function and the result is set to `app.stocks` which updates the UI.

The server and client code is relatively straightforward but, as we'll see, this simplicity brings with it some limitations.

### Supporting CORS

In the `local.settings.json` file, the `Host` section includes the following settings:

```json
"Host" : {
    "LocalHttpPort": 7071,
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
```

This configuration allows a web application running at _localhost:8080_ to make requests to the function app running at _localhost:7071_. The property `CORSCredentials` tells function app to accept credential cookies from the request.

### Analysis of current solution

## Exercise - Analyze the limitations of a polling-based web app

## Enable automatic updates in a web application using SignalR Service

## Exercise – Enable automatic updates in a web application using SignalR Service

## Use a storage account to host a static website

## Exercise - Use a storage account to host a static website

## Summary
