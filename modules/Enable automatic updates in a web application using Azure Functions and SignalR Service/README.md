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

For the app to run, you need to add the connection settings for your cloud services to the local settings file:

```sh
STORAGE_CONNECTION_STRING=$(az storage account show-connection-string \
--name $(az storage account list \
  --resource-group learn-20d0f9bb-e322-4a6f-b83c-66d970a878df \
  --query [0].name -o tsv) \
--resource-group learn-20d0f9bb-e322-4a6f-b83c-66d970a878df \
--query "connectionString" -o tsv)

COSMOSDB_ACCOUNT_NAME=$(az cosmosdb list \
    --resource-group learn-20d0f9bb-e322-4a6f-b83c-66d970a878df \
    --query [0].name -o tsv)

COSMOSDB_CONNECTION_STRING=$(az cosmosdb list-connection-strings  \
  --name $COSMOSDB_ACCOUNT_NAME \
  --resource-group learn-20d0f9bb-e322-4a6f-b83c-66d970a878df \
  --query "connectionStrings[?description=='Primary SQL Connection String'].connectionString" -o tsv)

COSMOSDB_MASTER_KEY=$(az cosmosdb list-keys \
--name $COSMOSDB_ACCOUNT_NAME \
--resource-group learn-20d0f9bb-e322-4a6f-b83c-66d970a878df \
--query primaryMasterKey -o tsv)

printf "\n\nReplace <STORAGE_CONNECTION_STRING> with:\n$STORAGE_CONNECTION_STRING\n\nReplace <COSMOSDB_CONNECTION_STRING> with:\n$COSMOSDB_CONNECTION_STRING\n\nReplace <COSMOSDB_MASTER_KEY> with:\n$COSMOSDB_MASTER_KEY\n\n"
```

In my case, this generated the following output:

```sh
Replace <STORAGE_CONNECTION_STRING> with:
DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName=cloudshell1435347499;AccountKey=SfyilGApt+9oc3qbzZPfAcza9KWl3eDwuGCPqLzZ2ACIW43SRt0OvseL6cMB/1cnwW4VPS1vqN3PnyzS9CPX2w==

Replace <COSMOSDB_CONNECTION_STRING> with:
AccountEndpoint=https://msl-sigr-cosmos-13d13a2f2d.documents.azure.com:443/;AccountKey=fI7Di0EY6juGLp47NyB6FamiP5BFhrXs6Eoz8Iey9ZaCuspwUYlnhDSa77c97jFKvxJV4xVxxkgiKihhsJ4Mag==;

Replace <COSMOSDB_MASTER_KEY> with:
fI7Di0EY6juGLp47NyB6FamiP5BFhrXs6Eoz8Iey9ZaCuspwUYlnhDSa77c97jFKvxJV4xVxxkgiKihhsJ4Mag==
```

Navigate to where you cloned the application and open the start folder in Visual Studio Code. Open `local.settings.json` in the editor so you can update the file.

In `local.settings.json`, update the variables `AzureWebJobsStorage`, `AzureCosmosDBConnectionString`, and `AzureCosmosDBMasterKey` with the values listed in the Cloud Shell and save the file. The `local.settings.json` file should only exist on your local computer.

### Run the application

In the Visual Studio Code terminal window, run the following command to install dependencies and set up the database:

```sh
# Make sure you are in the start folder of the serverless-demo directory
$ npm install
```

Press F5 to start debugging the function app. The function app startup is shown in a terminal window.

To run the web application on your machine, open a second integrated terminal instance and run the following command to start the web app:

```sh
$ npm start
```

The script automatically opens the browser and navigates to http://localhost:8080. If the browser fails to open automatically, you can navigate to http://localhost:8080 manually.

Return to Visual Studio Code, open a third terminal instance, and enter the following command to update the stock prices. Immediately return to the browser and observe that the values for stock ABC change within a few seconds.

```sh
$ npm run update-data
```

When you're done, stop the running processes.

- To stop the web server, click the kill process (trash can icon) on the terminal window that is running the web server.
- To stop the functions app, click the Stop button or press Shift + F5.

## Enable automatic updates in a web application using SignalR Service

Next, we'll move away from polling and toward an app that pushes data updates (as they occur) to connected clients. This new design reduces traffic and makes a more efficient UI by only updating as data changes. The three technologies that we'll use to deliver this updated solution are **Azure Cosmos DB**, **Azure Functions**, and **SignalR**.

As data changes in the database, **Azure Cosmos DB** exposes a "change feed". The change feed support in Azure Cosmos DB works by listening to a database container for changes. It then outputs the sorted list of changed documents in the order in which they were modified.

**By listening to the change feed, your application can automatically respond to data changes.**

The key difference between this function and the original `getStocks` function is that the function is now triggered based on changes to our data. In the preceding exercise, we triggered our function based on requests from the client and pulled back all data through an Azure Cosmos DB input binding. Using the Azure Cosmos DB trigger automatically makes our data retrieval more efficient

**Azure Functions** features a binding that runs code anytime data is updated in an Azure Cosmos DB change feed. Once a function is listening to the change feed, then you can work with a subset of your data that just represents data changes. When paired with a persistent connection to the client, the function can contact individual clients on-demand, which is the foundation for a real-time application architecture.

### SignalR and persistent connections

In contrast to polling, a more favorable design features persistent connections between the client and server. Establishing a persistent connection allows the server to push data to the client at will. The on-demand nature of the connection reduces network traffic and load on the server. SignalR allows you to easily add this type of architecture to your application.

**SignalR** is an abstraction for a series of technologies that allows your app to enjoy two-way communication between the client and server. SignalR handles connection management automatically, and lets you broadcast messages to all connected clients simultaneously, like a chat room. You can also send messages to specific clients. The connection between the client and server is persistent, unlike a classic HTTP connection, which is re-established for each communication.

A key benefit of the abstraction provided by SignalR is the way it supports "transport" fallbacks. A transport is method of communicating between the client and server. SignalR connections begin with a standard HTTP request. As the server evaluates the connection, the most appropriate communication method (transport) is selected. Transports are chosen depending on the APIs available on the client.

**For clients that support HTML 5, the WebSockets API transport is used by default. If the client doesn't support WebSockets, then SignalR falls back to Server Sent Events (also known as EventSource). For older clients, Ajax long polling or Forever Frame (IE only) is used to mimic a two-way connection.**

The abstraction layer offered by SignalR provides two benefits to your application.

The first advantage is future-proofing your app. As the web evolves and APIs superior to WebSockets become available, your application doesn't need to change. You could update to a version of SignalR that supports any new APIs and your application code won't need an overhaul.

The second benefit is that SignalR allows your application to gracefully degrade depending on supported technologies of the client. If it doesn't support WebSockets, then Server Sent Events are used. If the client can't handle Server Sent Events, then it uses Ajax long polling, and so on.

Let's look at how to use SignalR to broadcast information from function that reads the Azure Cosmos DB change feed.

## Exercise – Enable automatic updates in a web application using SignalR Service

To support the new functionality, you need to create a few new functions and update the JavaScript on the client.

### Create a SignalR account

You'll need to add a SignalR account to your sandbox subscription.

The first step is to run the following command in the Cloud Shell to create a new SignalR account in the sandbox resource group. This command can take a couple of minutes to complete, so please wait for it to finish before proceeding to the next step.

```sh
SIGNALR_SERVICE_NAME=msl-sigr-signalr$(openssl rand -hex 5)
az signalr create \
  --name $SIGNALR_SERVICE_NAME \
  --resource-group learn-20d0f9bb-e322-4a6f-b83c-66d970a878df \
  --sku Free_DS2 \
  --unit-count 1
```

For SignalR Service to work properly with Azure Functions, you need to set its service mode to Serverless. Configure the service mode using the following command.

```sh
az resource update \
  --resource-type Microsoft.SignalRService/SignalR \
  --name $SIGNALR_SERVICE_NAME \
  --resource-group learn-20d0f9bb-e322-4a6f-b83c-66d970a878df \
  --set properties.features[flag=ServiceMode].value=Serverless
```

### Update local settings

For the app to run, you need to add the SignalR connection string saved to your local settings.

Run the following commands in the Cloud Shell to get the connection strings for the resources we created in this exercise:

```sh
SIGNALR_CONNECTION_STRING=$(az signalr key list \
  --name $(az signalr list \
    --resource-group learn-20d0f9bb-e322-4a6f-b83c-66d970a878df \
    --query [0].name -o tsv) \
  --resource-group learn-20d0f9bb-e322-4a6f-b83c-66d970a878df \
  --query primaryConnectionString -o tsv)

printf "\n\nReplace <SIGNALR_CONNECTION_STRING> with:\n$SIGNALR_CONNECTION_STRING\n\n"
```

Navigate to where you cloned the application and open the start folder in Visual Studio Code. Open **local.settings.json** in the editor so you can update the file.

In **local.settings.json**, update the variable `AzureSignalRConnectionString` with the value listed in the Cloud Shell and save the file.

### Manage client connections

The web client uses the SignalR client SDK to establish a connection to the server. The SDK retrieves the connection via a function named **negotiate** (by convention) to connect to the service.

Be sure to have your Azure Function app - `serverless-demo` - with the `start` folder open and your **local.settings.json** defined as described above.

Open the Visual Studio Code command palette by pressing F1.

Search for and select the `Azure Functions: Create Function` command and create an anonymouse function named `negotiate`

Refresh the Explorer window in Visual Studio Code to see the updates. A folder named negotiate is now available in your function app.

Open negotiate/function.json and add the following SignalR binding definition to the bindings array:

```json
{
  "type": "signalRConnectionInfo",
  "name": "connectionInfo",
  "hubName": "stocks",
  "direction": "in",
  "connectionStringSetting": "AzureSignalRConnectionString"
}
```

This configuration allows the function to return the connection information to the server, which is used to identify connected clients.

Next, open negotiate/index.js and replace the existing function code with the following code:

```js
module.exports = async function (context, req, connectionInfo) {
  context.res.body = connectionInfo
}
```

As the function is called, the SignalR connection is returned as the response to the function.

Now that the function to return the SignalR connection info is implemented, you can create a function responsible for pushing changes to the client.

### Detect and broadcast database changes

First, you need to create a new function that listens for changes in the database. This function uses an Azure Cosmos DB trigger that connects to the change feed of the database.

Be sure to have your Azure Function app - `serverless-demo` - with the `start` folder open and your **local.settings.json** defined as described above.

Open the Visual Studio Code command palette by pressing F1.

Search for and select the `Azure Functions: Create Function` command.

When prompted, provide the following information:

| Name                                         | Value                         |
| -------------------------------------------- | ----------------------------- |
| Template                                     | Azure Cosmos DB Trigger       |
| Name                                         | stocksChanged                 |
| App setting for your Azure Cosmos DB account | AzureCosmosDBConnectionString |
| Database name                                | stocksdb                      |
| Collection name                              | stocks                        |
| Collection name for leases                   | leases                        |
| Create lease collection if not exists        | true                          |

Now a folder named stocksChanged is created and contains the files for the new function.

Open `stocksChanged/function.json` in Visual Studio Code

Append the property `"feedPollDelay": 500` to the existing trigger binding definition. This setting tells Azure Cosmos DB how long to wait before checking for changes in the database. The application you're building is built around a push-based architecture. However behind the scenes, Azure Cosmos DB is continually monitoring the change feed to detect changes. The feedPollDelay refers to how the internals of Azure Cosmos DB recognize changes, not how your web application exposes changes to the data.

The Azure Cosmos DB binding for your function should now look like the following code.

```json
{
  "type": "cosmosDBTrigger",
  "name": "documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "AzureCosmosDBConnectionString",
  "databaseName": "stocksdb",
  "collectionName": "stocks",
  "createLeaseCollectionIfNotExists": "true",
  "feedPollDelay": 500
}
```

Next, append the following SignalR output binding definition to the bindings array.

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "connectionString": "AzureSignalRConnectionString",
  "hubName": "stocks",
  "direction": "out"
}
```

This binding allows the function to broadcast changes to clients.

Update the `stocksChanged/index.js` file to reflect the following code. The beauty of all the configuration is that the function code is simple.

```js
module.exports = async function (context, documents) {
  const updates = documents.map((stock) => ({
    target: "updated",
    arguments: [stock],
  }))

  context.bindings.signalRMessages = updates
  context.done()
}
```

An array of changes is prepared by creating an object formatted to be read by SignalR. Every updated stock is provided to the `arguments` array along with a target property set to updated.

The value of the target property is used on the client when listening for specific messages broadcast by SignalR.

### Update the web application

Open public/index.html and paste the following code in place of the current DIV with the ID of app:

```html
<div id="app" class="container">
  <h1 class="title">Stocks</h1>
  <div id="stocks">
    <div v-for="stock in stocks" class="stock">
      <transition name="fade" mode="out-in">
        <div class="list-item" :key="stock.price">
          <div class="lead">{{ stock.symbol }}: ${{ stock.price }}</div>
          <div class="change">
            Change:
            <span
              :class="{ 'is-up': stock.changeDirection === '+', 'is-down': stock.changeDirection === '-' }"
            >
              {{ stock.changeDirection }}{{ stock.change }}
            </span>
          </div>
        </div>
      </transition>
    </div>
  </div>
</div>
```

This markup adds a transition element, which allows Vue.js to run a subtle animation as stock data changes. When a stock is updated, the tile fades out and quickly back in to view. This way if the page is full of stock data, users can easily see which stocks have changed.

Next, add the following script block just above the reference to _index.html.js_

```html
<script src="https://cdn.jsdelivr.net/npm/@aspnet/signalr@1.1.0/dist/browser/signalr.js"></script>
```

This script adds a reference to the SignalR SDK.

Now open `public/index.html.js` and replace the file with the following code:

```js
const LOCAL_BASE_URL = "http://localhost:7071"
const REMOTE_BASE_URL = "<FUNCTION_APP_ENDPOINT>"

const getAPIBaseUrl = () => {
  const isLocal = /localhost/.test(window.location.href)
  return isLocal ? LOCAL_BASE_URL : REMOTE_BASE_URL
}

const app = new Vue({
  el: "#app",
  data() {
    return {
      stocks: [],
    }
  },
  methods: {
    async getStocks() {
      try {
        const apiUrl = `${getAPIBaseUrl()}/api/getStocks`
        const response = await axios.get(apiUrl)
        app.stocks = response.data
      } catch (ex) {
        console.error(ex)
      }
    },
  },
  created() {
    this.getStocks()
  },
})

const connect = () => {
  const connection = new signalR.HubConnectionBuilder()
    .withUrl(`${getAPIBaseUrl()}/api`)
    .build()

  connection.onclose(() => {
    console.log("SignalR connection disconnected")
    setTimeout(() => connect(), 2000)
  })

  connection.on("updated", (updatedStock) => {
    const index = app.stocks.findIndex((s) => s.id === updatedStock.id)
    app.stocks.splice(index, 1, updatedStock)
  })

  connection.start().then(() => {
    console.log("SignalR connection established")
  })
}

connect()
```

The changes you just made accomplished two goals: removed all polling logic from the client and added handlers to listen for messages coming from the server.

A new helper function is introduced which makes it easy for the application to work in local and deployed contexts:

```js
const LOCAL_BASE_URL = "http://localhost:7071"
const REMOTE_BASE_URL = "<FUNCTION_APP_ENDPOINT>"

const getAPIBaseUrl = () => {
  const isLocal = /localhost/.test(window.location.href)
  return isLocal ? LOCAL_BASE_URL : REMOTE_BASE_URL
}
```

The `getAPIBaseUrl` function returns the appropriate URL depending on whether the app is running locally or deployed to Azure. The placeholder <REMOTE_BASE_URL> is replaced by the storage account endpoint in a coming exercise when you deploy this application to the cloud.

The Vue.js-related code is streamlined now that changes are pushed to the client. Consider this segment of the code you pasted in to the script file:

```js
const app = new Vue({
  el: "#app",
  data() {
    return {
      stocks: [],
    }
  },
  methods: {
    async getStocks() {
      try {
        const apiUrl = `${getAPIBaseUrl()}/api/getStocks`
        const response = await axios.get(apiUrl)
        app.stocks = response.data
      } catch (ex) {
        console.error(ex)
      }
    },
  },
  created() {
    this.getStocks()
  },
})
```

The same stocks array is used here as in the previous implementation, but all the polling code is removed and the logic for getStocks remains unchanged. The getStocks function is still called as the component is created.

Next, consider this segment of the client code:

```js
const connect = () => {
  const connection = new signalR.HubConnectionBuilder()
    .withUrl(`${getAPIBaseUrl()}/api`)
    .build()

  connection.onclose(() => {
    console.log("SignalR connection disconnected")
    setTimeout(() => connect(), 2000)
  })

  connection.on("updated", (updatedStock) => {
    const index = app.stocks.findIndex((s) => s.id === updatedStock.id)
    app.stocks.splice(index, 1, updatedStock)
  })

  connection.start().then(() => {
    console.log("SignalR connection established")
  })
}

connect()
```

When the page loads, the `connect` function is called. In the body of the `connect` function, the first action is to use the SignalR SDK to create a connection by calling `HubConnectionBuilder`. The result is a SignalR connection to the server.

To gracefully recover after the server has timed out, the `onclose` handler reestablishes a connection two seconds after the connection has closed by calling `connect` again.

As the client receives messages from the server, it listens for messages via the `on('updated',...` syntax. Once an update is received, the following actions take place:

- The changed stock is located in the array.
- The old version is removed.
- The new version is inserted at the same index position in the array.

Manipulating the array this way allows Vue to detect changes in the data and trigger animation effects to notify users of changes.

### Run the application

Now you can see the new version of the app running locally.

Press F5 to start debugging the functions app.

Next, open a new terminal window in Visual Studio Code and run `npm start`

The script automatically opens the browser and navigates to http://localhost:8080. If the browser fails to open automatically, you can navigate to http://localhost:8080 manually.

### Observe automatic updates

Now you can change the application's data and observe how to the UI automatically updates.

Arrange Visual Studio Code on one side of the screen and the web browser on the other. This way you can see the UI update as changes are made to the database.

Return to Visual Studio Code and enter the following command in a new integrated terminal. Again, watch as the application automatically updates the stock ABC:

```sh
$ npm run update-data
```

When you're done, stop the running processes:

- To stop the web server, click the kill process (trash can icon) on the terminal window that is running the web server.
- To stop the functions app, click the Stop button or press Shift + F5.

## Use a storage account to host a static website

Now that we've tested the application on your local machine, it's time to publish it to the cloud. There are two aspects of your application that require attention before publishing. First, you need to deploy the local functions into Azure and then you need to make the static HTML and JavaScript files available on the web.

Azure Storage includes a feature where you can place files in a specific storage container, which makes them available for HTTP requests. This feature, known as static website support makes hosting publicly available web pages a simple process.

When you copy files to a storage container named `\$web`, those files are available to web browsers via a secure server using the `https://<ACCOUNT_NAME>.<ZONE_NAME>.web.core.windows.net/<FILE_NAME>` URI scheme.

## Exercise - Use a storage account to host a static website

## Summary
