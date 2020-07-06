[Store application data with Azure Blob storage](https://docs.microsoft.com/en-us/learn/modules/store-app-data-with-azure-blob-storage/) (~50 mins)

# Introduction to Blob storage

Blobs give you file storage in the cloud and an API that lets you build apps to access the data.

Suppose you work at an augmented-reality gaming company. Your game runs on every mobile platform. You want to add a new feature to let users record video clips of their gameplay and upload the clips to your servers. Users will be able to watch clips directly in the game or through your website. You plan to log every upload and viewing for use in analytics and for traceability.

You need a storage solution that can handle thousands of simultaneous uploads, massive amounts of video data, and constantly growing log files. You also need to add the viewing functionality to all your mobile apps and your website, so you want API access from multiple platforms and languages.

Here, you will see how Azure Blob storage could be appropriate for this application.

## Learning objectives

In this module, you will:

- Organize your data with Azure Blob storage
- Create storage resources to hold blobs
- Store and retrieve data from Azure Blob storage

# What are blobs?

Blobs are "files for the cloud". Apps work with blobs in much the same way as they would work with files on a disk, like reading and writing data. However, unlike a local file, blobs can be reached from anywhere with an internet connection.

Azure Blob storage is _unstructured_, meaning that there are no restrictions on the kinds of data it can hold. For example, a blob can hold a PDF document, a JPG image, a JSON file, video content, etc. **Blobs aren't limited to common file formats** — a blob could contain gigabytes of binary data streamed from a scientific instrument, an encrypted message for another application, or data in a custom format for an app you're developing.

**Blobs are usually not appropriate for structured data that needs to be queried frequently**. They have higher latency than memory and local disk and don't have the indexing features that make databases efficient at running queries. However, blobs are **frequently used in combination with databases to store non-queryable data**. For example, an app with a database of user profiles could store profile pictures in blobs. Each user record in the database would include the name or URL of the blob containing the user's picture.

Blobs are used for data storage in many ways across all kinds of applications and architectures:

- Apps that need to transmit large amounts of data using messaging system that supports only small messages. These apps can store data in blobs and send the blob URLs in messages.
- Blob storage can be used like a file system for storing and sharing documents and other personal data.
- Static web assets like images can be stored in blobs and made available for public download as if they were files on a web server.
- Many Azure components use blobs behind the scenes. For example, Azure Cloud Shell stores your files and configuration in blobs, and Azure Virtual Machines uses blobs for hard-disk storage.

Some apps will constantly create, update, and delete blobs as part of their work. Others will use a small set of blobs and rarely change them.

## Storage accounts, containers, and metadata

In Blob storage, every blob lives inside a _blob container_. Y**ou can store an unlimited number of blobs in a container** and **an unlimited number of containers in a storage account**. **Containers are "flat"** — they can only store blobs, not other containers.

Blobs and containers support metadata in the form of name-value string pairs. Your apps can use metadata for anything you like: a human-readable description of a blob's contents to be displayed by the application, a string that your app uses to determine how to process the blob's data, etc.

TIP: Blob storage does not provide any mechanism for searching or sorting blobs by metadata. See the Further Reading section at the end of this module for information about using Azure Cognitive Search to achieve this.

## The Blob storage API and client libraries

The Blob storage API is REST-based and supported by client libraries in many popular languages. It lets you write apps that create and delete blobs and containers, upload and download blob data, and list the blobs in a container.

# Design a storage organization strategy

When designing an app that needs to store data, it's important to think about how the app is going to organize data across storage accounts, containers, and blobs.

## Storage accounts

A single storage account is flexible enough to organize your blobs however you like, but you should use additional storage accounts as necessary to logically separate costs and control access to data.

## Containers and blobs

The nature of your application and the data that it stores should drive your strategy for naming and organizing containers and blobs.

Apps using blobs as part of a storage scheme that includes a database often don't need to rely heavily on organization, naming, or metadata to indicate anything about their data. Such apps commonly use identifiers like GUIDs as blob names and reference these identifiers in database records. The app will use the database to determine where blobs are stored and the kind of data they contain.

Other apps may use Azure Blob storage more like a personal file system, where container and blob names are used to indicate meaning and structure. Blob names in these kinds of apps will often look like traditional file names and include file name extensions like .jpg to indicate what kind of data they contain. They'll use virtual directories (see below) to organize blobs and will frequently use metadata tags to store information about blobs and containers.

There are a few key things to consider when deciding how to organize and store blobs and containers.

### Naming limitations

Container and blob names must conform to a set of rules, including length limitations and character restrictions. See the Further Reading section at the end of this module for more specific information about naming rules.

### Public access and containers as security boundaries

By default, all blobs require authentication to access. However, individual containers can be configured to allow public downloading of their blobs without authentication. This feature supports many use cases, such as hosting static website assets and sharing files. This is because downloading blob contents works the same way as reading any other kind of data over the web: you just point a browser or anything that can make a GET request at the blob URL.

**Enabling public access is important for scalability because data downloaded directly from Blob storage doesn't generate any traffic in your server-side app**. Even if you don't immediately take advantage of public access or if you will use a database to control data access via your application, plan on using separate containers for data you want to be publicly available.

CAUTION: Blobs in a container configured for public access can be downloaded without any kind of authentication or auditing by anyone who knows their storage URLs. Never put blob data in a public container that you don't intend to share publicly.

In addition to public access, Azure has a shared access signature feature that allows fine-grained permissions control on containers. Precision access control enables scenarios that further improve scalability, so thinking about containers as security boundaries in general is a helpful guideline.

### Blob name prefixes (virtual directories)

Technically, **containers are "flat" and do not support any kind of nesting or hierarchy**. But if you give your blobs hierarchical names that look like file paths (such as `finance/budgets/2017/q1.xls`), the A**PI's listing operation can filter results to specific prefixes**. This allows you to navigate the list as if it was a hierarchical system of files and folders.

This feature is often called _virtual directories_ because some tools and client libraries use it to visualize and navigate Blob storage as if it was a file system. Each folder navigation triggers a separate call to list the blobs in that folder.

Using names that are like file names for blobs is a common technique for organizing and navigating complex blob data.

### Blob types

There are three different kinds of blobs you can store data in:

- **Block blobs** are composed of blocks of different sizes that can be uploaded independently and in parallel. Writing to a block blob involves uploading data to blocks and committing them to the blob.
- **Append blobs** are specialized block blobs that support only appending new data (not updating or deleting existing data), but they're very efficient at it. Append blobs are great for **scenarios like storing logs or writing streamed data**.
- **Page blobs** are designed for scenarios that involve random-access reads and writes. **Page blobs are used to store the virtual hard disk (VHD) files** used by Azure Virtual Machines, but they're great for any scenario that involves random access.

Block blobs are the best choice for most scenarios that don't specifically call for append or page blobs. Their block-based structure supports very fast uploads and downloads and efficient access to individual pieces of a blob. The process of managing and committing blocks is automatically handled by most client libraries, and some will also handle parallel uploads and downloads to maximize performance.

## Check your knowledge

1. Suppose you need to store profile and order information about your customers. You need to query the data to answer questions like "who are my top 100 customers?" and "how many customers live in a given geographic region?". True or false: blob storage is a good choice for this data?

[] True
[ x ] False

2. Blobs provide unstructured data storage. What does unstructured mean?

[] Blobs can't be organized or named.
[ x ] There are no restrictions on the type of data you can store in blobs.
[] Blobs can't contain structured data, like JSON or XML.

# Exercise - Create Azure storage resources

Once we have an idea of how we're going to store data across storage accounts, containers and blobs, we can think about the Azure resources we need to create to support the app.

## Storage accounts

Storage account creation is an administrative/management activity that takes place prior to deploying and running your app. Accounts are usually created by a deployment or environment setup script, an Azure Resource Manager template, or manually by an administrator. Applications other than administrative tools generally should not have permissions to create storage accounts.

## Containers

Unlike storage account creation, container creation is a lightweight activity that makes sense to perform from within an app. It's not uncommon for apps to create and delete containers as part of their work.

For apps that rely on a known set of containers with hard-coded or preconfigured names, typical practice is to let the app create the containers it needs on startup or first usage if they don't already exist.

**Letting your app create containers instead of doing it as part of your app's deployment eliminates the need for both your application and your deployment process to know the names of the containers the app uses.**

## Exercise

We're going to complete an unfinished ASP.NET Core app by adding code to use Azure Blob storage. This exercise is more about exploring the Blob storage API than it is about designing an organization and naming scheme, but here's a quick overview of the app and how it stores data.

![https://docs.microsoft.com/en-us/learn/modules/store-app-data-with-azure-blob-storage/media/4-fileuploader-with-files.png](https://docs.microsoft.com/en-us/learn/modules/store-app-data-with-azure-blob-storage/media/4-fileuploader-with-files.png)

Our app works like a shared folder that accepts file uploads and makes them available for download. It doesn't use a database for organizing blobs — instead, it sanitizes the names of uploaded files and uses them as blob names directly. All uploaded files are stored in a single container.

The code we'll start with compiles and runs, but the parts responsible for storing and loading data are empty. After we complete the code, we'll deploy the app to Azure App Service and test it.

Let's set up the storage infrastructure for our app.

### Storage account

We'll use the Azure Cloud Shell with the Azure CLI to create a storage account. You'll need to provide a unique name for the storage account — make a note of it for later.
Execute the following command to create the storage account.

```sh
az storage account create \
  --kind StorageV2 \
  --resource-group learn-899129fd-7565-40c6-bccb-927b5e0f0c7f \
  --location centralus \
  --name rbblobapp405
```

NOTE: Why `--kind StorageV2`? There are a few different kinds of storage accounts. For most scenarios, you should use general-purpose v2 accounts. The only reason you need to explicitly specify `--kind StorageV2` is that general-purpose v2 accounts have not yet been made the default kind in the Azure CLI.

Sample output:

```json
{
  "accessTier": "Hot",
  "azureFilesIdentityBasedAuthentication": null,
  "blobRestoreStatus": null,
  "creationTime": "2020-07-06T23:06:05.159075+00:00",
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
        "lastEnabledTime": "2020-07-06T23:06:05.221534+00:00"
      },
      "file": {
        "enabled": true,
        "keyType": "Account",
        "lastEnabledTime": "2020-07-06T23:06:05.221534+00:00"
      },
      "queue": null,
      "table": null
    }
  },
  "failoverInProgress": null,
  "geoReplicationStats": null,
  "id": "/subscriptions/f500dc0d-177d-4393-87c4-047c5c9ec078/resourceGroups/learn-899129fd-7565-40c6-bccb-927b5e0f0c7f/providers/Microsoft.Storage/storageAccounts/rbblobapp405",
  "identity": null,
  "isHnsEnabled": null,
  "kind": "StorageV2",
  "largeFileSharesState": null,
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "rbblobapp405",
  "networkRuleSet": {
    "bypass": "AzureServices",
    "defaultAction": "Allow",
    "ipRules": [],
    "virtualNetworkRules": []
  },
  "primaryEndpoints": {
    "blob": "https://rbblobapp405.blob.core.windows.net/",
    "dfs": "https://rbblobapp405.dfs.core.windows.net/",
    "file": "https://rbblobapp405.file.core.windows.net/",
    "internetEndpoints": null,
    "microsoftEndpoints": null,
    "queue": "https://rbblobapp405.queue.core.windows.net/",
    "table": "https://rbblobapp405.table.core.windows.net/",
    "web": "https://rbblobapp405.z19.web.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "privateEndpointConnections": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "learn-899129fd-7565-40c6-bccb-927b5e0f0c7f",
  "routingPreference": null,
  "secondaryEndpoints": {
    "blob": "https://rbblobapp405-secondary.blob.core.windows.net/",
    "dfs": "https://rbblobapp405-secondary.dfs.core.windows.net/",
    "file": null,
    "internetEndpoints": null,
    "microsoftEndpoints": null,
    "queue": "https://rbblobapp405-secondary.queue.core.windows.net/",
    "table": "https://rbblobapp405-secondary.table.core.windows.net/",
    "web": "https://rbblobapp405-secondary.z19.web.core.windows.net/"
  },
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_RAGRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

### Container

The app we'll be working with in this module uses a single container. We're going to follow the best practice of letting the app create the container at startup. However, container creation can be done from the Azure CLI: run `az storage container create -h` in the Cloud Shell terminal if you'd like to see the documentation.

# Exercise - Configure and initialize the client library

The following is the typical workflow for apps that use Azure Blob storage:

- **Retrieve configuration**: At startup, load the storage account configuration. This is typically a storage account connection string.
- **Initialize client**: Use the connection string to initialize the Azure Storage client library. This creates the objects the app will use to work with the Blob storage API.
- **Use**: Make API calls with the client library to operate on containers and blobs.

## Configure your connection string

Before running your app, you'll need the connection string for the storage account you will use. You can use any Azure management interface to get it, including the Azure portal, the Azure CLI or Azure PowerShell. When we set up the web app to run our code near the end of this module, we'll use the Azure CLI to get the connection string for the storage account you created earlier.

Storage account connection strings include the account key. The account key is considered a secret and should be stored securely. Here, we will store the connection string in an App Service application setting.

**App Service application settings are a secure place for application secrets, but this design does not support local development and is not a robust, end-to-end solution on its own.**

WARNING: Do not place storage account keys in code or in unprotected configuration files. Storage account keys enable full access to your storage account. Leaking a key can result in unrecoverable damage and large bills. See the Further Reading section at the end of this module for storage guidance and advice about how to recover from a leaked key.

## Initialize the Blob storage object model

In the Azure Storage SDK for .NET Core, the standard pattern for using Blob storage consists of the following steps:

- Call `CloudStorageAccount.Parse` (or `TryParse`) with your connection string to get a `CloudStorageAccount`.
- Call `CreateCloudBlobClient` on the `CloudStorageAccount` to get a `CloudBlobClient`.
- Call `GetContainerReference` on the `CloudBlobClient` to get a `CloudBlobContainer`.
- Use methods on the container to get a list of blobs and/or get references to individual blobs to upload and download data.

In code, steps 1–3 look like this:

```csharp
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); // or TryParse()
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference(containerName);
```

None of this initialization code makes calls over the network. This means that some exceptions that occur because of incorrect information won't be thrown until later. For example, the call to `CloudStorageAccount.Parse` will throw an exception immediately if the connection string is formatted incorrectly, but no exception will be thrown if the storage account that a connection string points to doesn't exist.

## Create containers at startup

Calling `CreateIfNotExistsAsync` on a `CloudBlobContainer` is the best way to create a container when your application starts or when it first tries to use it.

`CreateIfNotExistsAsync` won't throw an exception if the container already exists, but it does make a network call to Azure Storage. **Call it once during initialization, not every time you try to use a container.**

## Exercise

### Clone and explore the unfinished app

First, let's clone the starter app from GitHub. In the Cloud Shell terminal, run the following command to get a copy of the source code and open it in the editor:

```sh
git clone https://github.com/MicrosoftDocs/mslearn-store-data-in-azure.git
cd mslearn-store-data-in-azure/store-app-data-with-azure-blob-storage/src/start
code .
```

Open the file `Controllers/FilesController.cs` in the editor. There's no work to do here, but we're going to have a quick look at what the app does.

This controller implements an API with three actions:

- **Index** (GET /api/Files) returns a list of URLs, one for each file that's been uploaded. The app front end calls this method to build a list of hyperlinks to the uploaded files.
- **Upload** (POST /api/Files) receives an uploaded file and saves it.
- **Download** (GET /api/Files/{filename}) downloads an individual file by its name.

Each method uses an `IStorage` instance called `storage` to do its work. There is an incomplete implementation of `IStorage` in `Models/BlobStorage.cs` that we're going to fill in.

### Add the NuGet package

### Configure

### Initialize

# Exercise - Get blob references

# Exercise - Blob uploads and downloads

# Clean up and knowledge check