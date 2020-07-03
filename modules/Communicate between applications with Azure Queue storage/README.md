[Communicate between applications with Azure Queue storage](https://docs.microsoft.com/en-us/learn/modules/communicate-between-apps-with-azure-queue-storage/) (~56 mins)

Implement the publish-subscribe pattern in the cloud using Azure Queue storage.

# Introduction

Imagine you work for a major news organization that reports breaking news alerts. Our company employs a worldwide network of journalists that are constantly sending updates through a web portal and a mobile app. A middle tier web service layer then takes those alert updates and publishes them online through several channels.

However, it's been noticed the system is missing alerts when globally significant events occur. This is a huge problem because we're being "scooped" by our competition! You've been hand-selected as the company's top developer to identify and fix the problem.

The middle tier provides plenty of capacity to handle normal loads. However, a look at the server logs revealed the system was overloaded when several journalists tried to upload larger breaking stories at the same time. Some writers complained the portal became unresponsive, and others said they lost their stories altogether. You've spotted a direct correlation between the reported issues and the spike in demand on the middle tier servers.

Clearly, you need a way to handle these unexpected peaks. You don't want to add more instances of the website and middle tier web service because they're expensive and, under normal conditions, redundant. We could dynamically spin up instances, but this takes time and we'd have the issue waiting for new servers to come online.

You can solve this problem by using Azure Queue storage. A storage queue is a high-performance message buffer that can act as a broker between the front-end components (the "producers") and the middle tier (the "consumer").

Our front-end components place a message for each new alert into a queue. The middle tier then retrieves these messages one at a time from the queue for processing. At times of high-demand, the queue may grow in length, but no stories will be lost, and the application will remain responsive. When demand drops back to normal levels, the web service will catch up by working through the queue backlog.

Let's learn how to use Azure Queue storage to handle high demand and improve resilience in your distributed applications.

## Learning objectives

- Create an Azure Storage account that supports queues.
- Create a queue using C# and the Azure Storage Client Library for .NET.
- Add, retrieve, and remove messages from a queue using C# and the Azure Storage Client Library for .NET.

# Create the Azure storage infrastructure

Direct communication between the components of a distributed application can be problematic because it might be disrupted when network bandwidth is low or when demand is high.

We've seen this in our system: the web portal calls a web service, which works great if the service responds in a timely manner. High traffic causes problems and so the plan is to use a queue to eliminate the direct link between the front-end apps and your middle-tier web service.

## What is Azure Queue storage?

Azure Queue storage is an Azure service that implements cloud-based queues. Each queue maintains a list of messages. Application components access a queue using a REST API or an Azure-supplied client library. Typically, you will have one or more _sender_ components and one or more _receiver_ components. Sender components add messages to the queue. Receiver components retrieve messages from the front of the queue for processing. The following illustration shows multiple sender applications adding messages to the Azure Queue and one receiver application retrieving the messages.

![https://docs.microsoft.com/en-us/learn/modules/communicate-between-apps-with-azure-queue-storage/media/2-queue-overview.png](https://docs.microsoft.com/en-us/learn/modules/communicate-between-apps-with-azure-queue-storage/media/2-queue-overview.png)

Pricing is based on queue size and number of operations. Larger message queues cost more than smaller queues. Charges are also incurred for each operation, such as adding a message and deleting a message. For pricing details, see [Azure Queue storage pricing](https://azure.microsoft.com/pricing/details/storage/queues/).

## Why use queues?

A queue increases resiliency by temporarily storing waiting messages. At times of low or normal demand, the size of the queue remains small because the destination component removes messages from the queue faster than they are added. At times of high demand, the queue may increase in size, but messages are not lost. The destination component can catch up and empty the queue as demand returns to normal.

A single queue can be up to 500 TB in size, so it can potentially store millions of messages.

**The target throughput for a single queue is 2000 messages per second**, allowing it to handle high-volume scenarios.

Queues let your application scale automatically and immediately when demand changes. This makes them useful for critical business data that would be damaging to lose. Azure offers many other services that scale automatically. For example, the **Autoscale** feature is available on Azure virtual machine scale sets, cloud services, Azure App Service plans, and App Service environments. This lets you define rules that Azure uses to identify periods of high demand and automatically add capacity without involving an administrator.

**Autoscaling responds to demand quickly, but not instantaneously. By contrast, Azure Queue storage instantaneously handles high demand by storing messages until processing resources are available.**

## What is a message?

A message in a queue is a byte array of **up to 64 KB**. Message contents are not interpreted at all by any Azure component.

If you want to create a structured message, you could format the message content using XML or JSON. Your code is responsible for generating and interpreting your custom format. For example, you could make a custom JSON message that looks like the following:

```json
{
  "Message": {
    "To": "news@contoso.com",
    "From": "writer@contoso.com",
    "Subject": "Support request",
    "Body": "Send me a photographer!"
  }
}
```

## Creating a storage account

A queue must be part of a storage account. You can create a storage account using the Azure CLI (or PowerShell), or Azure portal. The portal is easiest because it's all guided and prompts you for each piece of information.

The following screenshot shows the location of the Storage accounts category:

![https://docs.microsoft.com/en-us/learn/modules/communicate-between-apps-with-azure-queue-storage/media/2-create-storage-account-1.png](https://docs.microsoft.com/en-us/learn/modules/communicate-between-apps-with-azure-queue-storage/media/2-create-storage-account-1.png)

There are several options you can supply when you create the account, most of which you can use the default selection. We covered these options in a previous module, but you can hover over the (i) tip associated with each option to get a reminder of what it does. Here's an example of filling out the portal pane.

The following screenshot displays the Create storage account pane and the information required to create a storage account.

![https://docs.microsoft.com/en-us/learn/modules/communicate-between-apps-with-azure-queue-storage/media/2-create-storage-account-2.png](https://docs.microsoft.com/en-us/learn/modules/communicate-between-apps-with-azure-queue-storage/media/2-create-storage-account-2.png)

### Settings for queues

When you create a storage account that will contain queues, you should consider the following settings:

- Queues are only available as part of Azure general-purpose storage accounts (v1 or v2). **You cannot add Queues to Blob storage accounts.**
- The **Access tier** setting which is shown for StorageV2 accounts applies only to Blob storage and does not affect queues.
- You should choose a location that is close to either the source components or destination components or (preferably) both.
- Data is always replicated to multiple servers to guard against disk failures and other hardware problems. You have a choice of replication strategies: **Locally Redundant Storage (LRS)** is low-cost but vulnerable to disasters that affect an entire data center while **Geo-Redundant Storage (GRS)** replicates data to other Azure data centers. Choose the replication strategy that meets your redundancy needs.
- The performance tier determines how your messages are stored: **Standard** uses magnetic drives while **Premium** uses solid-state drives. Choose Standard if you expect peaks in demand to be short. Consider Premium if queue length sometimes becomes long and you need to minimize the time to access messages.
- Require secure transfer if sensitive information may pass through the queue. This setting ensures that all connections to the queue are encrypted using Secure Sockets Layer (SSL).

# Exercise - Create a storage account

You've discovered that spikes in traffic can overwhelm our middle-tier. To deal with this, you've decided to add a queue between the front-end and the middle tier in your article-upload application.

The first step in creating a queue is to create the Azure Storage Account that will store our data.

## Create a Storage Account with the Azure CLI

Normally, you'd start a new project by creating a resource group to hold all the associated resources. In this case, we'll be using the Azure sandbox which provides a resource group named learn-a6fa1b6e-59c5-4994-9d57-d081a3715af0.

The command needs several parameters:

| Parameter | Value                                                                                                                                                                                                                                                                                                                                           |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| --name    | Sets the name. Remember that storage accounts use the name to generate a public URL - so it has to be unique. In addition, the account name must be between 3 and 24 characters, and be composed of numbers and lowercase letters only. We recommend you use the prefix articles with a random number suffix but you can use whatever you like. |
| -g        | Supplies the Resource Group, use learn-a6fa1b6e-59c5-4994-9d57-d081a3715af0 as the value.                                                                                                                                                                                                                                                       |
| --kind    | Sets the Storage Account type - use StorageV2 to create a general-purpose V2 account.                                                                                                                                                                                                                                                           |
| --sku     | Sets the Replication and Storage type, it defaults to Standard_RAGRS. Let's use Standard_LRS which means it's only locally redundant within the data center.                                                                                                                                                                                    |
| -l        | Sets the Location independent of the resource group owner. It's optional, but you can use it to place the queue in a different region than the resource group. Place it close to you, choosing from the below list of available regions in the sandbox.                                                                                         |

Here's an example command line that uses the above parameters. Make sure to change the `--name` parameter.

```sh
az storage account create --name rbstoragedemo732 -g learn-a6fa1b6e-59c5-4994-9d57-d081a3715af0 --kind StorageV2 --sku Standard_LRS
```

# Identify a queue

Now that we have a storage account, let's look at how we work with the queue that it will hold.

To access a queue, you need three pieces of information:

- Storage account name
- Queue name
- Authorization token

This information is used by both applications that talk to the queue (the web front end that adds messages and the mid-tier that processes them).

## Queue identity

Every queue has a name that you assign during creation.

**The name must be unique within your storage account but doesn't need to be globally unique** (unlike the storage account name).

The combination of your storage account name and your queue name uniquely identifies a queue.

## Access authorization

Every request to a queue must be authorized and there are several options to choose from.

| Authorization Type      | Description                                                                                                                                                                                                                                                                                                                   |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Azure Active Directory  | You can use role-based authentication and identify specific clients based on AAD credentials.                                                                                                                                                                                                                                 |
| Shared Key              | Sometimes referred to as an account key, this is an encrypted key signature associated with the storage account. Every storage account has two of these keys that can be passed with each request to authenticate access. Using this approach is like using a root password - it provides full access to the storage account. |
| Shared access signature | A shared access signature (SAS) is a generated URI that grants limited access to objects in your storage account to clients. You can restrict access to specific resources, permissions, and scope to a data range to automatically turn off access after a period of time.                                                   |

We will use the account key authorization because it is the simplest way to get started working with queues, however it's recommended that you either use shared access signature (SAS) or Azure Active Directory (AAD) in production apps.

### Retrieving the account key

Your account key is available in the Settings > Access keys section of your storage account in the Azure portal, or you can retrieve it through the command line:

```sh
# Azure CLI
az storage account keys list ...

# PowerShell
Get-AzStorageAccountKey ...
```

## Accessing queues

You access a queue using a REST API. To do this, you'll use a URL that combines the name you gave the storage account with the domain queue.core.windows.net and the path to the queue you want to work with. For example: `http://<storage account>.queue.core.windows.net/<queue name>`. An `Authorization` header must be included with every request. The value can be any of the three authorization styles.

### Using the Azure Storage Client Library for .NET

The Azure Storage Client Library for .NET is a library provided by Microsoft that formulates REST requests and parses REST responses for you. This greatly reduces the amount of code you need to write. Access using the client library still requires the same pieces of information (storage account name, queue name, and account key); however, they are organized differently.

The client library uses a **connection string** to establish your connection. Your connection string is available in the **Settings** section of your Storage Account in the Azure portal, or through the Azure CLI and PowerShell.

A connection string is a string that combines a storage account name and account key and must be known to the application to access the storage account. The format looks like this:

```csharp
string connectionString = "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
```

This string value should be stored in a secure location since anyone who has access to this connection string would be able to manipulate the queue.

Notice that the connection string doesn't include the queue name. The queue name is supplied in your code when you establish a connection to the queue.

Let's get our connection string from Azure and set up a new application to use it.

# Exercise - Identify a queue

Let's create a client application to work with a queue. Then we'll add our connection string to the code.

## Create the application

We'll create a .NET Core application that you can run on Linux, macOS, or Windows. Let's name it **QueueApp**. For simplicity, we'll use a single app that will both send and receive messages through our queue.

Use the `dotnet new` command to create a new console app with the name **QueueApp**. You can type commands into the Cloud Shell on the right, or if you are working locally, in a terminal/console window. This command creates a simple app with a single source file: `Program.cs`

```sh
dotnet new console -n QueueApp
```

Switch to the newly created QueueApp folder and build the app to verify that all is well.

```sh
cd QueueApp
dotnet build
```

## Get your connection string

Recall that your connection string is available in the Azure portal **Settings > Access keys** section of your storage account.

You can also retrieve it through the Azure CLI or PowerShell tools. Let's use the Azure CLI command. Remember to replace the <name> with the specific name of the storage account you created - `rbstoragedemo732`.

You can use `az storage account list` if you need a reminder.

```sh
az storage account show-connection-string --name rbstoragedemo732 --resource-group learn-a6fa1b6e-59c5-4994-9d57-d081a3715af0
```

This command will return a JSON block with your connection string. It will include the storage account name and the account key:

```json
{
  "connectionString": "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName=rbstoragedemo732;AccountKey=EHtcfoUf9PXwgw80KKWXA9HEm1takeyTYO5Bz2f1pwDqZ3dIleIiNXiWB9vBRxAvHJ4UnANuGfbyz9w+egdHiw=="
}
```

## Add the connection string to the application

Finally, let's add the connection string into our app so it can access the storage account.

For simplicity, you will place the connection string in the **Program.cs** file. In a production application, you should store it in a secure location. For server side use, we recommend using Azure Key Vault.

Type `code .` in the terminal to open the online code editor. Alternatively, if you are working on your own you can use the IDE of your choice. We recommend Visual Studio Code, which is an excellent cross-platform IDE.

Open the `Program.cs` source file in the project.

In the Program class, add a const string value to hold the connection string. You only need the value (it starts with the text DefaultEndpointsProtocol).

Save the file. You can click the ellipse "..." in the right corner of the cloud editor, or use the accelerator key (Ctrl+S on Windows and Linux, Cmd+S on macOS).

Your code should look something like this (the string value will be unique to your account):

```csharp
using System;

namespace QueueApp
{
    class Program
    {
        private const string ConnectionString = "DefaultEndpointsProtocol=https; ...";

        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

Now that we have this starter project setup, let's look at how to work with a queue in code. It all starts with _messages_.

# Programmatically access a queue

Queues hold messages - packets of data whose shape is known to the sender application and receiver application. The sender creates the queue and adds a message. The receiver retrieves a message, processes it, and then deletes the message from the queue. The following illustration shows a typical flow of this process.

![https://docs.microsoft.com/en-us/learn/modules/communicate-between-apps-with-azure-queue-storage/media/6-message-flow.png](https://docs.microsoft.com/en-us/learn/modules/communicate-between-apps-with-azure-queue-storage/media/6-message-flow.png)

Notice that `get` and `delete` are separate operations. This arrangement handles potential failures in the receiver and implements a concept called _at-least-once delivery_.

After the receiver gets a message, that message remains in the queue but is invisible for 30 seconds. If the receiver crashes or experiences a power failure during processing, then it will never delete the message from the queue. After 30 seconds, the message will reappear in the queue and another instance of the receiver can process it to completion.

## The Azure Storage Client Library for .NET

The **Azure Storage Client Library for .NET** provides types to represent each of the objects you need to interact with:

- `CloudStorageAccount` represents your Azure storage account.
- `CloudQueueClient` represents Azure Queue storage.
- `CloudQueue` represents one of your queue instances.
- `CloudQueueMessage` represents a message.

You will use these classes to get programmatic access to your queue. The library has both synchronous and asynchronous methods; you should prefer to use the asynchronous versions to avoid blocking the client app.

The Azure Storage Client Library for .NET is available in the **WindowsAzure.Storage** NuGet package. You can install it through an IDE, Azure CLI, or through PowerShell `Install-Package WindowsAzure.Storage`

## How to connect to a queue

To connect to a queue, you first create a `CloudStorageAccount` with your connection string. The resulting object can then create a `CloudQueueClient`, which in turn can open a `CloudQueue` instance. The basic code flow is shown below.

```csharp
CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);

CloudQueueClient client = account.CreateCloudQueueClient();

CloudQueue queue = client.GetQueueReference("myqueue");
```

Creating a `CloudQueue` doesn't necessarily mean the _actual_ storage queue exists. However, you can use this object to create, delete, and check for an existing queue. As mentioned above, all methods support both synchronous and asynchronous versions, but we will only be using the `Task`-based asynchronous versions.

## How to create a queue

You will use a common pattern for queue creation: **the sender application should always be responsible for creating the queue**. This keeps your application more self-contained and less dependent on administrative set-up.

To make the creation simple, the client library exposes a `CreateIfNotExistsAsync` method that will create the queue if necessary, or return `false` if the queue already exists.

Typical code is shown below.

```csharp
CloudQueue queue;
//...

await queue.CreateIfNotExistsAsync();
```

You must have `Write` or `Create` permissions for the storage account to use this API. This is always true if you use the **Access Key** security model, but you can lock down permissions to the account with other approaches that will only allow read operations against the queue.

## How to send a message

To send a message, you instantiate a `CloudQueueMessage` object. The class has a few overloaded constructors that load your data into the message. We will use the constructor that takes a `string`. After creating the message, you use a `CloudQueue` object to send it.

Here's a typical example:

```csharp
var message = new CloudQueueMessage("your message here");

CloudQueue queue;
//...

await queue.AddMessageAsync(message);
```

While the **total queue size can be up to 500 TB**, the **individual messages in it can only be up to 64 KB in size** (**48 KB when using Base64 encoding**).

If you need a larger payload you can combine queues and blobs – passing the URL to the actual data (stored as a Blob) in the message. This approach would allow you to enqueue up to 200 GB for a single item.

## How to receive and delete a message

In the receiver, you get the next message, process it, and then delete it after processing succeeds. Here's a simple example:

```csharp
CloudQueue queue;
//...

CloudQueueMessage message = await queue.GetMessageAsync();

if (message != null)
{
    // Process the message
    //...

    await queue.DeleteMessageAsync(message);
}
```

Let's now apply this new knowledge to our application!

# Exercise - Add a message to the queue

## Add the client library for Azure Storage

```sh

```

## Add a method to send a news alert

```csharp

```

```csharp

```

```csharp

```

## Add code to send a message

```csharp

```

## Execute the application

```sh

```

## Check your results

```sh

```

```json

```

## Optional - Use the async versions of the methods

### Switch to C# 7.1

```xml

```

### Apply the async keyword

# Exercise - Retrieve a message from the queue

# Summary
