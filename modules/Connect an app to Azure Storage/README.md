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

# Connect to your Azure storage account

# Exercise - Add Azure Storage configuration to your app

# Initialize the storage account model

# Exercise - Connect with your Azure Storage configuration

# Knowledge check

# Summary
