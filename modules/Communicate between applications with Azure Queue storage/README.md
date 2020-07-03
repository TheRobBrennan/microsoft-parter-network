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

# Exercise - Identify a queue

# Programmatically access a queue

# Exercise - Add a message to the queue

# Exercise - Retrieve a message from the queue

# Summary
