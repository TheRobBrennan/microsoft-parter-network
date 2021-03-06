[Connect an app to Azure Storage](https://docs.microsoft.com/en-us/learn/modules/connect-an-app-to-azure-storage/) (~1 hr 15 mins)

# Introduction

Imagine you're building a photo-sharing application. Every day, thousands of users take pictures and rely on your application to keep them safe and make them accessible across all their devices. Storing these photos is critical to your business, and you would like to ensure that the system used in your application is fast, reliable, and secure. Ideally, this would be done without you having to build all these aspects into the app.

Azure Storage provides such power, resiliency, and performance in the cloud. This module will provide you with the knowledge to be able to utilize the power of Azure Storage to achieve the reliability and performance in your app that you require. This knowledge can then be applied to any application.

## Learning objectives

In this module, you will:

- Explore the data types and services supported by an Azure storage account.
- Create a new Azure storage account using the Azure portal.
- Create a new application and connect the app to an Azure Storage account.

## Prerequisites

Some programming experience in either C# or JavaScript is assumed.

# Explore Azure storage services

Let's start by taking a quick look at Azure storage services, data styles, and accounts.

Microsoft Azure Storage is a managed service that provides durable, secure, and scalable storage in the cloud. Let's break these terms down.

|          |                                                                                                                                                                                                                                                                                                                                |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Durable  | Redundancy ensures that your data is safe in the event of transient hardware failures. You can also replicate data across datacenters or geographical regions for additional protection from local catastrophe or natural disaster. Data replicated in this way remains highly available in the event of an unexpected outage. |
| Secure   | All data written to Azure Storage is encrypted by the service. Azure Storage provides you with fine-grained control over who has access to your data.                                                                                                                                                                          |
| Scalable | Azure Storage is designed to be massively scalable to meet the data storage and performance needs of today's applications.                                                                                                                                                                                                     |
| Managed  | Microsoft Azure handles maintenance and any critical problems for you.                                                                                                                                                                                                                                                         |

A **single Azure subscription can host up to 200 storage accounts**, **each of which can hold 500 TB of data**. If you have a business case, you can talk to the Azure Storage team and **get approval for up to 250 storage accounts** in a subscription, which **pushes your max storage up to 125 Petabytes**!

## Azure data services

Azure storage includes four types of data:

- Blobs: A massively scalable object store for text and binary data. Can include support for Azure Data Lake Storage Gen2.
- Files: Managed file shares for cloud or on-premises deployments.
- Queues: A messaging store for reliable messaging between application components.
- Table Storage: A NoSQL store for schemaless storage of structured data. Table Storage is not covered in this module.

All of these data types in Azure Storage are accessible from anywhere in the world over HTTP or HTTPS. Microsoft provides SDKs for Azure Storage in a variety of languages, as well as a REST API. You can also visually explore your data right in the Azure portal.

### Blob storage

Azure Blob storage is an object storage solution optimized for storing massive amounts of unstructured data, such as text or binary data. Blob storage is ideal for:

- Serving images or documents directly to a browser, including full static websites.
- Storing files for distributed access.
- Streaming video and audio.
- Storing data for backup and restoration, disaster recovery, and archiving.
- Storing data for analysis by an on-premises or Azure-hosted service.

Azure Storage supports three kinds of blobs:

- Block blobs: Block blobs are used to hold text or binary **files up to ~5 TB** (50,000 blocks of 100 MB) in size. The **primary use case for block blobs** is the storage of **files that are read from beginning to end, such as media files** or image files for websites. They are named block blobs because **files larger than 100 MB must be uploaded as small blocks**, which are then consolidated (or committed) into the final blob.
- Page blobs: Page blobs are used to hold **random-access files up to 8 TB** in size. Page blobs are used primarily as the backing storage for the VHDs used to provide durable disks for Azure Virtual Machines (Azure VMs). They are named page blobs because **they provide random read/write access to 512-byte pages**.
- Append blobs: Append blobs are made up of blocks like block blobs, but they are optimized for append operations. **These are frequently used for logging information from one or more sources into the same blob**. For example, you might write all of your trace logging to the same append blob for an application running on multiple VMs. **A single append blob can be up to 195 GB**.

### Files

Azure Files enables you to set up highly available network file shares that can be **accessed by using the standard Server Message Block (SMB) protocol**. This means that multiple VMs can share the same files with both read and write access. You can also read the files using the REST interface or the storage client libraries. You can also associate a unique URL to any file to allow fine-grained access to a private file for a set period of time. File shares can be used for many common scenarios:

- Storing shared configuration files for VMs, tools, or utilities so that everyone is using the same version.
- Log files such as diagnostics, metrics, and crash dumps.
- Shared data between on-premises applications and Azure VMs to allow migration of apps to the cloud over a period of time.

### Queues

The Azure Queue service is used to store and retrieve messages. Queue **messages can be up to 64 KB** in size, and a queue can contain millions of messages. Queues are generally used to store lists of messages to be processed asynchronously.

You can use queues to loosely connect different parts of your application together. For example, we could perform image processing on the photos uploaded by our users. Perhaps we want to provide some sort of face detection or tagging capability, so people can search through all the images they have stored in our service. We could use queues to pass messages to our image processing service to let it know that new images have been uploaded and are ready for processing. This sort of architecture would allow you to develop and update each part of the service independently.

## Azure storage accounts

To access any of these services from an application, you have to create a _storage account_. The storage account provides a unique namespace in Azure to store and access your data objects. A storage account contains any blobs, files, queues, tables, and VM disks that you create under that account.

### Creating a storage account

You can create an Azure storage account using the Azure portal, Azure PowerShell, or Azure CLI. Azure Storage provides three distinct account options with different pricing and features supported.

- **General-purpose v2 (GPv2)** - General-purpose v2 (GPv2) accounts are storage accounts that support all of the latest features for blobs, files, queues, and tables. Pricing for GPv2 accounts has been designed to deliver the lowest per gigabyte prices.
- **General-purpose v1 (GPv1)** - General-purpose v1 (GPv1) accounts provide access to all Azure Storage services but may not have the latest features or the lowest per gigabyte pricing. For example, cool storage and archive storage are not supported in GPv1. Pricing is lower for GPv1 transactions, so workloads with high churn or high read rates may benefit from this account type.
- **Blob storage accounts** - A legacy account type, blob storage accounts support all the same block blob features as GPv2, but they are limited to supporting only block and append blobs. Pricing is broadly similar to pricing for general-purpose v2 accounts.

If you are interested in learning more about creating storage accounts, make sure to go through the **Create an Azure storage account** module in the learning portal.

# Exercise - Create a new app to work with Azure storage

Recall that we are working on a photo-sharing application that will use Azure Storage to manage pictures and other bits of data we store on behalf of our users.

To simplify our scenario so that we can focus on the Storage APIs, we will create a new Node.js application that can run from the console. We will also assume it always has network connectivity. However, you should always harden your app to ensure network failures will not impact the user experience, or result in a failure of the application itself.

## Create a Node.js application

Node.js is a popular framework for running JavaScript apps. It is most commonly used for web apps, but you can use it to run logic from the command line as well. If you have the tools installed locally, you can run the following steps from a command line. Alternatively, you can use the Cloud Shell on the right side of the window to execute the below steps.

Sign into the Cloud Shell or open a command-line session and create a new folder named "PhotoSharingApp".

```sh
mkdir PhotoSharingApp
```

Change into the new folder and use `npm` to initialize a new Node.js app. This will create a **package.json** file containing metadata that describes the app.

```sh
cd PhotoSharingApp
npm init -y
```

Create a new source file **index.js**, which is where our code will go.

```sh
touch index.js
```

Open the **index.js** file with an editor. If you are using the Cloud Shell, you can type `code .` to open an editor.

Paste the following program into the **index.js** file. You can use the Ctrl+V keyboard shortcut or right-click to paste.

```sh
#!/usr/bin/env node

function main() {
    console.log('Hello, World!');
}

main();
```

Save the file—you can use the "..." menu on the top right corner of the Cloud Shell editor.

Run the app to make sure it executes correctly. It should display "Hello, World!" to the console.

```sh
node index.js
```

# Exercise - Create an Azure storage account

Now that we have an app, we need an Azure storage account to work with.

## Use the Azure CLI to create an Azure storage account

We will use the `az storage account create` command to create a new storage account. There are several parameters to control the configuration of the storage account.

- `--name` - A **Storage account name**. The name will be used to generate the public URL used to access the data in the account. It **must be unique across all existing storage account names in Azure**. It must be 3 to 24 characters long and can contain only lowercase letters and numbers.
- `--resource-group` - Use **learn-e068bf6e-5da2-4b7f-aac4-0f1dc623e629** to place the storage account into the free sandbox.
- `--location` - `westus2`
- `--kind` - This determines the storage account _type_. Options include `BlobStorage`, `Storage`, and `StorageV2`.
- `--sku` - This decides the storage account performance and replication model. Options include `Premium_LRS`, `Standard_GRS`, `Standard_LRS`, `Standard_RAGRS`, and `Standard_ZRS`.
- `--access-tier` - The **Access tier** is only used for Blob storage, available options are [`Cool` | `Hot`]. The **Hot Access Tier** is ideal for frequently accessed data, and the **Cool Access Tier** is better for infrequently accessed data. Note that this only sets the _default_ value — **when you create a Blob, you can set a different value for the data**.

Use the above table to craft a command line in the Cloud Shell on the right to create the account.

- Use a unique name. We recommend something like `photostorerb222` with your initials and a random number. You will get an error if it's not unique.
- Normally you would create a new resource group to hold your app resources, but in this case, use the sandbox resource group "**learn-e068bf6e-5da2-4b7f-aac4-0f1dc623e629**".
- Use "Standard_LRS" for the **sku**. This will use standard storage with local replication, which is fine for this example.
- Use "Cool" for the **Access Tier**.

### Example command

You can use the following example command to create a storage account. Remember to replace `<name>` with a unique value.

```sh
az storage account create \
        --resource-group learn-e068bf6e-5da2-4b7f-aac4-0f1dc623e629 \
        --kind StorageV2 \
        --sku Standard_LRS \
        --access-tier Cool \
        --name photostorerb222
```

It will take a few minutes to deploy the account. While Azure is working on that, let's look at how we interact with the Azure Storage APIs.

Sample output:

```json
{
  "accessTier": "Cool",
  "azureFilesIdentityBasedAuthentication": null,
  "blobRestoreStatus": null,
  "creationTime": "2020-07-06T21:23:47.495666+00:00",
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
        "lastEnabledTime": "2020-07-06T21:23:47.573768+00:00"
      },
      "file": {
        "enabled": true,
        "keyType": "Account",
        "lastEnabledTime": "2020-07-06T21:23:47.573768+00:00"
      },
      "queue": null,
      "table": null
    }
  },
  "failoverInProgress": null,
  "geoReplicationStats": null,
  "id": "/subscriptions/45b351fe-c7e8-4cb4-82e4-ff3615698d8d/resourceGroups/learn-e068bf6e-5da2-4b7f-aac4-0f1dc623e629/providers/Microsoft.Storage/storageAccounts/photostorerb222",
  "identity": null,
  "isHnsEnabled": null,
  "kind": "StorageV2",
  "largeFileSharesState": null,
  "lastGeoFailoverTime": null,
  "location": "westus",
  "name": "photostorerb222",
  "networkRuleSet": {
    "bypass": "AzureServices",
    "defaultAction": "Allow",
    "ipRules": [],
    "virtualNetworkRules": []
  },
  "primaryEndpoints": {
    "blob": "https://photostorerb222.blob.core.windows.net/",
    "dfs": "https://photostorerb222.dfs.core.windows.net/",
    "file": "https://photostorerb222.file.core.windows.net/",
    "internetEndpoints": null,
    "microsoftEndpoints": null,
    "queue": "https://photostorerb222.queue.core.windows.net/",
    "table": "https://photostorerb222.table.core.windows.net/",
    "web": "https://photostorerb222.z22.web.core.windows.net/"
  },
  "primaryLocation": "westus",
  "privateEndpointConnections": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "learn-e068bf6e-5da2-4b7f-aac4-0f1dc623e629",
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
```

# Interact with the Azure Storage APIs

Azure Storage provides a REST API to work with the containers and data stored in each account. There are independent APIs available to work with each type of data you can store. Recall that we have four specific data types:

- **Blobs** for unstructured data such as binary and text files.
- **Queues** for persistent messaging.
- **Tables** for structured storage of key/values.
- **Files** for traditional SMB file shares.

## Using the REST API

The Storage REST APIs are accessible from anywhere on the Internet, by any application that can send an HTTP/HTTPS request and receive an HTTP/HTTPS response.

For example, if you wanted to list all the blobs in a container, you would send something like:

```sh
GET https://[url-for-service-account]/?comp=list&include=metadata
```

This would return an XML block with data specific to the account:

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults AccountName="https://[url-for-service-account]/">
  <Containers>
    <Container>
      <Name>container1</Name>
      <Url>https://[url-for-service-account]/container1</Url>
      <Properties>
        <Last-Modified>Sun, 24 Sep 2018 18:09:03 GMT</Last-Modified>
        <Etag>0x8CAE7D0C4AF4487</Etag>
      </Properties>
      <Metadata>
        <Color>orange</Color>
        <ContainerNumber>01</ContainerNumber>
        <SomeMetadataName>SomeMetadataValue</SomeMetadataName>
      </Metadata>
    </Container>
    <Container>
      <Name>container2</Name>
      <Url>https://[url-for-service-account]/container2</Url>
      <Properties>
        <Last-Modified>Sun, 24 Sep 2018 17:26:40 GMT</Last-Modified>
        <Etag>0x8CAE7CAD8C24928</Etag>
      </Properties>
      <Metadata>
        <Color>pink</Color>
        <ContainerNumber>02</ContainerNumber>
        <SomeMetadataName>SomeMetadataValue</SomeMetadataName>
      </Metadata>
    </Container>
    <Container>
      <Name>container3</Name>
      <Url>https://[url-for-service-account]/container3</Url>
      <Properties>
        <Last-Modified>Sun, 24 Sep 2018 17:26:40 GMT</Last-Modified>
        <Etag>0x8CAE7CAD8EAC0BB</Etag>
      </Properties>
      <Metadata>
        <Color>brown</Color>
        <ContainerNumber>03</ContainerNumber>
        <SomeMetadataName>SomeMetadataValue</SomeMetadataName>
      </Metadata>
    </Container>
  </Containers>
  <NextMarker>container4</NextMarker>
</EnumerationResults>
```

However, this approach requires a lot of manual parsing and the creation of HTTP packets to work with each API. For this reason, Azure provides pre-built _client libraries_ that make working with the service easier for common languages and frameworks.

## Using a client library

Client libraries can save a significant amount of work for application developers because the API is tested and it often provides nicer wrappers around the data models sent and received by the REST API.

Microsoft has Azure client libraries that support a number of languages and frameworks including:

- .NET
- Java
- Python
- Node.js
- Go

For example, to retrieve the same list of blobs in C#, we could use the following code snippet:

```csharp
CloudBlobDirectory directory = ...;
foreach (IEnumerable<IListBlobItem> blob in directory.ListBlobs(
                useFlatBlobListing: true,
                blobListingDetails: BlobListingDetails.All))
{
    // Work with blob item .. could be page blob, block blob, etc.
}
```

Or in JavaScript:

```js
const containerName = "..."
const blobService = storage.createBlobService()

blobService.listBlobsSegmented(containerName, null, function (error, results) {
  if (results) {
    for (var i = 0, blob; (blob = results.entries[i]); i++) {
      // Work with blob item .. could be page blob, block blob, etc.
    }
  }
})
```

NOTE: The client libraries are just thin wrappers over the REST API. They are doing exactly what you would do if you used the web services directly. These libraries are also open source, making them very transparent. See the Additional Resources section at the end of this module for links to the source code for these libraries on GitHub.

Let's add client library support to our application.

# Exercise - Add the storage client library to your app

Let's integrate the **Microsoft Azure Storage Client Library for Node.js and JavaScript** into your application.

The client library for Node.js is available through the Node Package manager (NPM). You will want to add the **azure-storage** package to your **packages.json** file.

NOTE: **The Microsoft Azure Storage Client Library for Node.js and JavaScript** is **intended for server** applications. If you are doing **client-side JavaScript**, you will want to use the **Azure Storage Client Library for JavaScript**, which provides the same functionality but is tailored to running in a browser.

## Add the Azure Storage package

In Cloud Shell, cd to the PhotoSharingApp directory if you aren't already there.

Add the **azure-storage** package to the application. Make sure to supply the `--save` option so it persists to **packages.json**.

```sh
npm install azure-storage --save
```

This should result in some console activity while the client library and all the required dependencies are downloaded. Once it's done, go ahead and build and run the app again to make sure everything is ready to go.

```sh
node index.js
```

Now that we have the necessary libraries in place, let's look at the common tasks you'll do in your code to work with Azure storage.

# Connect to your Azure storage account

You have added the required client libraries to your application and are ready to connect to your Azure storage account.

To work with data in a storage account, your app will need two pieces of data:

- An access key
- The REST API endpoint

## Security access keys

Each storage account has two unique _access_ keys that are used to secure the storage account. If your app needs to connect to multiple storage accounts, then your app will require an access key for each storage account.

![https://docs.microsoft.com/en-us/learn/modules/connect-an-app-to-azure-storage/media/6-multiple-accounts.png](https://docs.microsoft.com/en-us/learn/modules/connect-an-app-to-azure-storage/media/6-multiple-accounts.png)

## REST API endpoint

In addition to access keys for authentication to storage accounts, your app will need to know the storage service endpoints to issue the REST requests.

The REST endpoint is a combination of your storage account _name_, the data type, and a known domain. For example:

- Blobs - `https://[name].blob.core.windows.net/`
- Queues - `https://[name].queue.core.windows.net/`
- Table - `https://[name].table.core.windows.net/`
- Files - `https://[name].file.core.windows.net/`

If you have a custom domain tied to Azure, then you can also create a custom domain URL for the endpoint.

## Connection strings

The simplest way to handle access keys and endpoint URLs within applications is to use **storage account connection strings**. A connection string provides all needed connectivity information in a single text string.

Azure Storage connection strings look similar to the example below but with the access key and account name of your specific storage account:

```sh
DefaultEndpointsProtocol=https;AccountName={your-storage};
   AccountKey={your-access-key};
   EndpointSuffix=core.windows.net
```

## Security

Access keys are critical to providing access to your storage account and as a result, should not be given to any system or person that you do not wish to have access to your storage account. Access keys are the equivalent of a username and password to access your computer.

**Typically, storage account connectivity information is stored within an environment variable, database, or configuration file.**

IMPORTANT: It is important to note that storing this information in a configuration file can be dangerous if you include that file in source control and store it in a public repository. This is a common mistake and means that anyone can browse your source code in the public repository and see your storage account connection information.

Each storage account has two access keys. The reason for this is to allow keys to be rotated (regenerated) periodically as part of security best practice in keeping your storage account secure. This process can be done from the Azure portal or the Azure CLI / PowerShell command line tool.

Rotating a key will invalidate the original key value immediately and will revoke access to anyone who obtained the key inappropriately. With support for two keys, you can rotate keys without causing downtime in your applications that use them.

Your app can switch to using the alternate access key while the other key is regenerated. If you have multiple apps using this storage account, they should all use the same key to support this technique. Here's the basic idea:

- Update the connection strings in your application code to reference the secondary access key of the storage account.
- Regenerate the primary access key for your storage account using the Azure portal or command line tool.
- Update the connection strings in your code to reference the new primary access key.
- Regenerate the secondary access key in the same manner.

TIP: It's highly recommended that you periodically rotate your access keys to ensure they remain private, just like changing your passwords. If you are using the key in a server application, you can use an **Azure Key Vault** to store the access key for you. Key Vaults include support to synchronize directly to the Storage Account and automatically rotate the keys periodically.

**Using a Key Vault provides an additional layer of security, so your app never has to work directly with an access key.**

### Shared access signatures (SAS)

Access keys are the easiest approach to authenticating access to a storage account. However they provide full access to anything in the storage account, similar to a root password on a computer.

Storage accounts offer a separate authentication mechanism called _shared access signatures_ that support expiration and limited permissions for scenarios where you need to grant limited access. You should use this approach when you are allowing other users to read and write data to your storage account. There are links to our documentation on this advanced topic at the end of the module.

# Exercise - Add Azure Storage configuration to your app

Let's add support to our Node.js application to retrieve a connection string from a configuration file. We'll start by adding the necessary plumbing to manage configuration from our JavaScript file.

## Create a .env configuration file

Make sure you are in the correct working directory for your project.

Use the touch tool on the command line to create a file named **.env**.

```sh
touch .env
```

Open the project in the Cloud Shell editor.

```sh
code .
```

Select the **.env** file in the editor and add the following text.

```sh
AZURE_STORAGE_CONNECTION_STRING=<value>
```

The **AZURE_STORAGE_CONNECTION_STRING** value is a hard-coded environment variable used for Storage APIs to look up access keys. You can use your own name if you prefer, but you must supply the name when you create the `BlobService` object in your Node.js app.

Save the file.

Now we need to get the storage account connection string and place it into the configuration for our app. In Cloud Shell run the following command.

```sh
az storage account show-connection-string \
    --resource-group learn-e068bf6e-5da2-4b7f-aac4-0f1dc623e629 \
    --query connectionString \
    --name photostorerb222
```

Copy the connection string that is returned from that command, minus the quotes, and replace `<value>` in the **.env** file with this connection string.

Save the file.

## Add support to read an environment configuration file

Node.js apps can include support to read from the **.env** file by adding the **dotenv** package.

In the command prompt section of the window, add a dependency to the **dotenv** package using `npm`.

```sh
npm install dotenv --save
```

## Add code to read the configuration file

Now that we have added the required libraries to enable reading configuration, we need to enable that functionality within our application.

Select **index.js** in the editor.

At the top of the file, a `#!/usr/bin/env node` line is present. Underneath that line, add a `require` statement to load the **dotenv** package. This will make environment variables defined in our **.env** file available to the program.

```js
#!/usr/bin/env node
require("dotenv").config()
// ... more code follows
```

Now that we have that all wired up, we can start adding code to use our storage account.

# Initialize the storage account model

The Azure Storage client library provides an object model that is used to interact with Azure storage accounts. It's used to quickly connect to an Azure storage account and use the Azure Storage service APIs.

## Azure Storage client library object model

The foundation of the storage account object model in the **Microsoft Azure Storage Client Library for Node.js and JavaScript** is the `azurestorage` object. This is created by adding the **azure-storage** module to your app through a `require` statement.

```js
const storage = require("azure-storage")
```

This object provides a series of _factory_ methods that create specific objects to work with each facet of Azure storage. You call `createXXX` methods to create each object.

| Type  | Method               | Returns        |
| ----- | -------------------- | -------------- |
| Blob  | `createBlobService`  | `BlobService`  |
| Table | `createTableService` | `TableService` |
| Queue | `createQueueService` | `QueueService` |
| File  | `createFileService`  | `FileService`  |

The client library will not attempt to connect until an operation is invoked that requires it. Each of these `create` methods return a lightweight object representing access to the storage type — it does not validate the connection or the access key being used.

Once you have a service object to a specific storage type, you can use methods to perform actual work. Methods that make network calls are intentionally asynchronous. The library currently supports _callbacks_ to return asynchronous results. For example, here is code that creates a blob container.

```js
var azure = require("azure-storage")
var blobService = azure.createBlobService()

blobService.createContainerIfNotExists("myblobcontainer", function (
  err,
  result,
  response
) {
  if (!err) {
    // if result.created = true, container was created.
    // if result.created = false, container already existed.
  }
})
```

This approach works fine, but it tends to lead to a lot of code being added into the callbacks, which can get unmanageable. A better approach in JavaScript is to use _promises_ to work with these methods. There are several great libraries which will convert callback-style methods into promises—you can pick the one you prefer.

Here, we'll use the `util.promisify` feature from Node and use the `BlobService` to create the container and upload a file to blob storage. In addition, we'll use the `async` and `await` keywords to work with the promises a bit more naturally.

```js
const util = require("util")
const storage = require("azure-storage")

const blobService = storage.createBlobService()
const createContainerAsync = util
  .promisify(blobService.createContainerIfNotExists)
  .bind(blobService)
const uploadBlobAsync = util
  .promisify(blobService.createBlockBlobFromLocalFile)
  .bind(blobService)

async function main() {
  try {
    // This makes an actual service call to the Azure Storage service.
    // Unless this call fails, the container will have been created.
    await createContainerAsync(containerName)

    // This transfers data in the file to the blob on the service.
    var uploadResult = await uploadBlobAsync(
      containerName,
      "myphoto",
      "photo.png"
    )
    if (uploadResult) {
      console.log("blob uploaded")
    }
  } catch (err) {
    console.log(err.message)
  }
}

main()
```

Let's try this in our app.

# Exercise - Connect with your Azure Storage configuration

Let's add code to connect to the Azure storage account using our stored connection string. **The Azure client library will automatically use the AZURE_STORAGE_CONNECTION_STRING environment variable** to get the connection string.

## Create a blob client

Open **index.js** in the code editor.

Start by including the **azure-storage** module. Store the module in a variable named **storage**.

```js
#!/usr/bin/env node
require("dotenv").config()

const storage = require("azure-storage")
```

Next, right after that, use the **storage** object to create the `BlobService` object and store it in a global named `blobService`. Remember, these are lightweight objects representing access to the storage account.

```js
const blobService = storage.createBlobService()
```

Add a constant to represent the container we want to create. We'll name the container "photoblobs".

```js
const containerName = "photoblobs"
```

## Create a container

We can use the `BlobService` object to work with blob APIs in Azure storage. As mentioned before, all the APIs that make network calls are asynchronous to keep the app responsive. The `createContainerIfNotExists` method is one such method. We'll use _promises_ to handle the callback that contains the response.

Add a `util` constant at the top of the file (after the initial require you added) to load the `util` package.

```js
#!/usr/bin/env node
require("dotenv").config()

const util = require("util")
const storage = require("azure-storage")
const blobService = storage.createBlobService()
const containerName = "photoblobs"
```

Next, after all the constants, use `util.promisify` to take the callback version of `createContainerIfNotExists` and turn it into a promise-returning method.

- **Since the callback method is on an object, make sure to add a bind call at the end to connect it to that context.**
- Assign the return value to a constant at the top of the file named `createContainerAsync`, as shown below.
- You'll also need a `require` for the `util` module near the top of the file.

```js
const util = require("util")
const storage = require("azure-storage")
const blobService = storage.createBlobService()
const containerName = "photoblobs"

const createContainerAsync = util
  .promisify(blobService.createContainerIfNotExists)
  .bind(blobService)
```

Remove the "Hello, World!" output from `main()`.

Call your new `createContainerAsync` promise.

- Pass it the `containerName` constant.
- Apply the `await` keyword to the call.
- Wrap the call in a `try / catch` construct and output any error.

```js
try {
  await createContainerAsync(containerName)
} catch (err) {
  console.log(err.message)
}
```

The `createContainerAsync` promise returns the first value from the underlying `createContainerIfNotExists`, which is the container result. This includes information on whether the container was created or not. Capture the result in a variable, and output whether the container was created based on the `result.created` property.

```js
try {
  var result = await createContainerAsync(containerName)
  if (result.created) {
    console.log(`Blob container ${containerName} created`)
  } else {
    console.log(`Blob container ${containerName} already exists.`)
  }
} catch (err) {
  console.log(err.message)
}
```

Finally, decorate the `main` function with the `async` keyword.

Save the file.

The final file should look like this, if you'd like to check your work.

```js
#!/usr/bin/env node

require("dotenv").config()

const util = require("util")

const storage = require("azure-storage")
const blobService = storage.createBlobService()
const createContainerAsync = util
  .promisify(blobService.createContainerIfNotExists)
  .bind(blobService)

const containerName = "photoblobs"

async function main() {
  try {
    var result = await createContainerAsync(containerName)
    if (result.created) {
      console.log(`Blob container ${containerName} created`)
    } else {
      console.log(`Blob container ${containerName} already exists.`)
    }
  } catch (err) {
    console.log(err.message)
  }
}

main()
```

## Run the app

Build and run the application. Note: make sure you're in the **PhotoSharingApp** directory.

```sh
node index.js
```

TIP: If you get an error about the use of the await keyword, make sure you have added the async keyword to the main function definition per the final step in the instructions above.

It should report that the blob container was created. If you run it a second time, it should tell you it already exists.

Sample output:

```sh
rob@Azure:~/PhotoSharingApp$ node index.js
Blob container photoblobs created
rob@Azure:~/PhotoSharingApp$ node index.js
Blob container photoblobs already exists.
```

To verify the container:

- Sign into the Azure portal using the same account you activated the sandbox with.
- Navigate to your storage account. You can use the **All Resources** section to find the storage account or search by name from the search box at the top of the portal window.
- Select the **Blobs** entry of the storage account in the **Blob services** section.
- You should see your **photoblobs** container in the Blobs panel. You can delete the container through the "..." menu on the right-hand side of the entry to try recreating it with your app.

## Delete the app

If you decide you don't want to keep the application source code in your Cloud Shell environment, you can use the following command to remove the folder and all the contents.

```sh
rm -r PhotoSharingApp/
```

# Knowledge check

## Check your knowledge

1. How many access keys are provided for accessing your Azure storage account?

[] 1
[ x ] 2
[] 3
[] 4

2. You can use either the REST API or the Azure client library to programmatically access a storage account. What is the primary advantage of using the client library?

[] Cost
[] Availability
[] Localization
[ x ] Convenience

3. Which of the following is a good analogy for the access keys of a storage account?

[] IP Address
[] REST Endpoint
[ x ] Username and password
[] Cryptographic algorithm

# Summary

You have learned the basics of creating and connecting to an Azure storage account. You wrote a simple application to connect to a storage account and create a blob container. Other modules in this Learning Path will build on that knowledge to show you how to use Blob Storage and Queues to store data and connect apps together.

We only used examples of JavaScript and C#, but Azure supports a variety of other languages. Check the official [SDK Tools documentation page](https://docs.microsoft.com/en-us/azure/sdks/) to find the official list of links to the client libraries for all the currently supported languages.

Additional resources

- [Azure Storage Services REST API Reference](https://docs.microsoft.com/en-us/rest/api/storageservices/)
- [Using shared access signatures to provide limited access to a storage account](https://docs.microsoft.com/en-us/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Using Azure Key Vault Storage Account Keys with server apps](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-ovw-storage-keys)
- [Source code for the .NET Azure SDKs](https://github.com/Azure/azure-sdk-for-net)
- [Azure Storage Client Library for JavaScript](https://github.com/Azure/azure-storage-node#azure-storage-javascript-client-library-for-browsers)
