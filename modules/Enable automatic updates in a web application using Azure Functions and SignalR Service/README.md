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

Let's think about some of the drawbacks of this timer-based polling approach.

In the timer-based polling prototype, the client application contacts the server whether or not changes exist to the underlying data. Once data is returned from the server the entire list of stocks is updated on the web page - again - regardless of any changes in the data. This polling mechanism is an inefficient solution.

Selecting the best polling interval for your scenario is also a challenging. Polling forces you to make a choice between how much each call to the backend costs and how quickly you want your app to respond to new data. Delays also often exist between when new data becomes available and when it's detected by the app. The following illustration shows the issue.

![https://docs.microsoft.com/en-us/learn/advocates/automatic-update-of-a-webapp-using-azure-functions-and-signalr/media/polling-example.png](https://docs.microsoft.com/en-us/learn/advocates/automatic-update-of-a-webapp-using-azure-functions-and-signalr/media/polling-example.png)

In the worst case, the potential delay for detecting new data is equal to the polling interval. So why not use a smaller interval?

As the application scales, the amount of data exchanged between the client and server will become a problem. Each HTTP request header includes hundreds of bytes of data along with the session's cookie. All this overhead, especially when under heavy load, creates wasted resources and unnecessarily taxes the server.

Now that you're more familiar with the starting point of the application, it's time to get the application running on your machine.

## Exercise - Analyze the limitations of a polling-based web app

### Prerequisites

Before you begin, make sure you have the following software installed on your machine:

- [Node.js](https://nodejs.org/download/)
- [Visual Studio Code](https://code.visualstudio.com/download)
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) (min. version 2.6.666)
  - For macOS, you can use Homebrew and install v3 of Azure Functions Core Tools

```sh
$ brew tap azure/functions
$ brew install azure-functions-core-tools@3
```

- [Azure Functions extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Azure Storage extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)

### Download sample app code

```sh
# Clone the repo from GitHub
$ git clone https://github.com/MicrosoftDocs/mslearn-advocates.azure-functions-and-signalr.git serverless-demo

# Navigate to the new directory
$ cd serverless-demo

# Launch VS Code using the start folder of the app (the end folder contains the finished version for comparison)
$ code start
```

### Create a Storage account

Azure Functions requires a storage account, and you'll need it when you deploy the web app to the cloud later in the module.

Execute the following command in the Cloud Shell to define a name for your Azure Storage account:

```sh
rob@Azure:~$ export STORAGE_ACCOUNT_NAME=mslsigrstorage$(openssl rand -hex 5)
rob@Azure:~$ echo "Storage Account Name: $STORAGE_ACCOUNT_NAME"
Storage Account Name: mslsigrstoragea814da8c2a
```

Run the following az storage account create command in the Cloud Shell to create a storage account for your function and static website:

```sh
rob@Azure:~$ az storage account create \
>   --name $STORAGE_ACCOUNT_NAME \
>   --resource-group learn-20d0f9bb-e322-4a6f-b83c-66d970a878df \
>   --kind StorageV2 \
>   --sku Standard_LRS

{- Finished ..
  "accessTier": "Hot",
  "azureFilesIdentityBasedAuthentication": null,
  "blobRestoreStatus": null,
  "creationTime": "2020-07-02T04:34:14.475675+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": true,
  "encryption": {
    "keySource": "Microsoft.Storage",
    "keyVaultProperties": null,
    "requireInfrastructureEncryption": null,
    "services": {
      "blob": {
        "enabled": true,
        "keyType": "Account",
        "lastEnabledTime": "2020-07-02T04:34:14.553831+00:00"
      },
      "file": {
        "enabled": true,
        "keyType": "Account",
        "lastEnabledTime": "2020-07-02T04:34:14.553831+00:00"
      },
      "queue": null,
      "table": null
    }
  },
  "failoverInProgress": null,
  "geoReplicationStats": null,
  "id": "/subscriptions/2508a8ca-0ade-47af-8754-3b95fed7a14c/resourceGroups/learn-20d0f9bb-e322-4a6f-b83c-66d970a878df/providers/Microsoft.Storage/storageAccounts/mslsigrstoragea814da8c2a",
  "identity": null,
  "isHnsEnabled": null,
  "kind": "StorageV2",
  "largeFileSharesState": null,
  "lastGeoFailoverTime": null,
  "location": "westus",
  "name": "mslsigrstoragea814da8c2a",
  "networkRuleSet": {
    "bypass": "AzureServices",
    "defaultAction": "Allow",
    "ipRules": [],
    "virtualNetworkRules": []
  },
  "primaryEndpoints": {
    "blob": "https://mslsigrstoragea814da8c2a.blob.core.windows.net/",
    "dfs": "https://mslsigrstoragea814da8c2a.dfs.core.windows.net/",
    "file": "https://mslsigrstoragea814da8c2a.file.core.windows.net/",
    "internetEndpoints": null,
    "microsoftEndpoints": null,
    "queue": "https://mslsigrstoragea814da8c2a.queue.core.windows.net/",
    "table": "https://mslsigrstoragea814da8c2a.table.core.windows.net/",
    "web": "https://mslsigrstoragea814da8c2a.z22.web.core.windows.net/"
  },
  "primaryLocation": "westus",
  "privateEndpointConnections": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "learn-20d0f9bb-e322-4a6f-b83c-66d970a878df",
  "routingPreference": null,
  "secondaryEndpoints": null,
  "secondaryLocation": null,
  "sku": {
    "name": "Standard_LRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": null,
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
rob@Azure:~$
```

### Create an Azure Cosmos DB account

You store stock prices in an Azure Cosmos DB database, so you'll set that up in the sandbox account.

Run the following `az cosmosdb create` command in the Cloud Shell to create a new Azure Cosmos DB account in your sandbox resource group:

```sh
rob@Azure:~$ az cosmosdb create  \
>   --name msl-sigr-cosmos-$(openssl rand -hex 5) \
>   --resource-group learn-20d0f9bb-e322-4a6f-b83c-66d970a878df
{
  "capabilities": [],
  "connectorOffer": null,
  "consistencyPolicy": {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "disableKeyBasedMetadataWriteAccess": false,
  "documentEndpoint": "https://msl-sigr-cosmos-13d13a2f2d.documents.azure.com:443/",
  "enableAutomaticFailover": false,
  "enableCassandraConnector": null,
  "enableMultipleWriteLocations": false,
  "failoverPolicies": [
    {
      "failoverPriority": 0,
      "id": "msl-sigr-cosmos-13d13a2f2d-westus",
      "locationName": "West US"
    }
  ],
  "id": "/subscriptions/2508a8ca-0ade-47af-8754-3b95fed7a14c/resourceGroups/learn-20d0f9bb-e322-4a6f-b83c-66d970a878df/providers/Microsoft.DocumentDB/databaseAccounts/msl-sigr-cosmos-13d13a2f2d",
  "ipRangeFilter": "",
  "isVirtualNetworkFilterEnabled": false,
  "keyVaultKeyUri": null,
  "kind": "GlobalDocumentDB",
  "location": "West US",
  "locations": [
    {
      "documentEndpoint": "https://msl-sigr-cosmos-13d13a2f2d-westus.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "msl-sigr-cosmos-13d13a2f2d-westus",
      "isZoneRedundant": false,
      "locationName": "West US",
      "provisioningState": "Succeeded"
    }
  ],
  "name": "msl-sigr-cosmos-13d13a2f2d",
  "privateEndpointConnections": null,
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "readLocations": [
    {
      "documentEndpoint": "https://msl-sigr-cosmos-13d13a2f2d-westus.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "msl-sigr-cosmos-13d13a2f2d-westus",
      "isZoneRedundant": false,
      "locationName": "West US",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "learn-20d0f9bb-e322-4a6f-b83c-66d970a878df",
  "tags": {},
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "virtualNetworkRules": [],
  "writeLocations": [
    {
      "documentEndpoint": "https://msl-sigr-cosmos-13d13a2f2d-westus.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "msl-sigr-cosmos-13d13a2f2d-westus",
      "isZoneRedundant": false,
      "locationName": "West US",
      "provisioningState": "Succeeded"
    }
  ]
}
```

NOTE: This will take many minutes to spin up. During this particular run, it took approximately ten minutes or so to spin up fully. Be patient. Grab a cold beverage.

### Update local settings

### Run the application

## Enable automatic updates in a web application using SignalR Service

## Exercise – Enable automatic updates in a web application using SignalR Service

## Use a storage account to host a static website

## Exercise - Use a storage account to host a static website

## Summary
