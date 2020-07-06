[Create an Azure Storage account](https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/) (~30 mins)

# Introduction

Most organizations have diverse requirements for their cloud-hosted data. For example, storing data in a specific region, or needing separate billing for different data categories. Azure storage accounts let you formalize these types of policies and apply them to your Azure data.

Suppose you work at a chocolate manufacturer that produces baking ingredients such as cocoa powder and chocolate chips. You market your products to grocery stores who then sell them to consumers.

Your formulations and manufacturing processes are trade secrets. The spreadsheets, documents, and instructional videos that capture this information are critical to your business and require geographically-redundant storage. This data is primarily accessed from your main factory, so you would like to store it in a nearby datacenter. The expense for this storage needs to be billed to the manufacturing department.

You also have a sales group that creates cookie recipes and baking videos to promote your products to consumers. Your priority for this data is low cost, rather than redundancy or location. This storage must be billed to the sales team.

Here, you will see how to handle these types of business requirements by creating multiple Azure storage accounts. Each storage account will have the appropriate settings for the data it holds.

## Learning objectives

In this module, you will:

- Decide how many storage accounts you need for your project
- Determine the appropriate settings for each storage account
- Create a storage account using the Azure portal

# Decide how many storage accounts you need

Organizations often have multiple storage accounts to let them implement different sets of requirements. In the chocolate-manufacturer example, there would be **one storage account for the private business data and one for the consumer-facing files**. Here, you will learn the policy factors that are controlled by a storage account, which will help you decide how many accounts you need.

## What is Azure Storage?

Azure provides many ways to store your data. There are multiple database options like Azure SQL Server, Azure Cosmos DB, and Azure Table Storage. Azure offers multiple ways to store and send messages, such as Azure Queues and Event Hubs. You can even store loose files using services like Azure Files and Azure Blobs.

Azure selected four of these data services and placed them together under the name Azure Storage.

**The four services are Azure Blobs, Azure Files, Azure Queues, and Azure Tables**. The following illustration shows the elements of Azure Storage.

![https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/2-azure-storage.png](https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/2-azure-storage.png)

These four were given special treatment because they are all primitive, cloud-based storage services and are often used together in the same application.

## What is a storage account?

A _storage account_ is a container that groups a set of Azure Storage services together. Only data services from Azure Storage can be included in a storage account (Azure Blobs, Azure Files, Azure Queues, and Azure Tables). The following illustration shows a storage account containing several data services.

![https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/2-what-is-a-storage-account.png](https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/2-what-is-a-storage-account.png)

Combining data services into a storage account lets you manage them as a group. The settings you specify when you create the account, or any that you change after creation, are applied to everything in the account. Deleting the storage account deletes all of the data stored inside it.

A storage account is an Azure resource and is included in a resource group. The following illustration shows an Azure subscription containing multiple resource groups, where each group contains one or more storage accounts.

![https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/2-resource-groups-and-storage-accounts.png](https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/2-resource-groups-and-storage-accounts.png)

Other Azure data services like Azure SQL and Azure Cosmos DB are managed as independent Azure resources and cannot be included in a storage account. The following illustration shows a typical arrangement: Blobs, Files, Queues, and Tables are inside storage accounts, while other services are not.

![https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/2-typical-subscription-organization.png](https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/2-typical-subscription-organization.png)

## Storage account settings

A storage account defines a policy that applies to all the storage services in the account.

**For example, you could specify that all the contained services will be stored in the West US datacenter, accessible only over https, and billed to the sales department's subscription.**

The settings that are controlled by a storage account are:

- **Subscription**: The Azure subscription that will be billed for the services in the account.
- **Location**: The datacenter that will store the services in the account.
- **Performance**: Determines the data services you can have in your storage account and the type of hardware disks used to store the data. **Standard** allows you to have any data service (Blob, File, Queue, Table) and uses magnetic disk drives. **Premium** introduces additional services for storing data. For example, storing unstructured object data as block blobs or append blobs, and specialized file storage used to store and create premium file shares. These storage accounts use solid-state drives (SSD) for storage.
- **Replication**: Determines the strategy used to make copies of your data to protect against hardware failure or natural disaster. **At a minimum, Azure will automatically maintain three copies of your data within the data center associated with the storage account**. This is called **locally-redundant storage (LRS)**, and guards against hardware failure but does not protect you from an event that incapacitates the entire datacenter. You can upgrade to one of the other options such as geo-redundant storage (GRS) to get replication at different datacenters across the world.
- **Access tier**: Controls how quickly you will be able to access the blobs in this storage account. Hot gives quicker access than Cool, but at increased cost. This applies only to blobs, and serves as the default value for new blobs.
- **Secure transfer required**: A security feature that determines the supported protocols for access. Enabled requires HTTPs, while disabled allows HTTP.
- **Virtual networks**: A security feature that allows inbound access requests only from the virtual network(s) you specify.

## How many storage accounts do you need?

A storage account represents a collection of settings like location, replication strategy, and subscription owner. You need one storage account for every group of settings that you want to apply to your data. The following illustration shows two storage accounts that differ in one setting; that one difference is enough to require separate storage accounts.

![https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/2-multiple-storage-accounts.png](https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/2-multiple-storage-accounts.png)

The number of storage accounts you need is typically determined by your data diversity, cost sensitivity, and tolerance for management overhead.

### Data diversity

Organizations often generate data that differs in where it is consumed, how sensitive it is, which group pays the bills, etc. Diversity along any of these vectors can lead to multiple storage accounts. Let's consider two examples:

- Do you have data that is specific to a country or region? If so, you might want to locate it in a data center in that country for performance or compliance reasons. You will need one storage account for each location.
- Do you have some data that is proprietary and some for public consumption? If so, you could enable virtual networks for the proprietary data and not for the public data. This will also require separate storage accounts.

**In general, increased diversity means an increased number of storage accounts.**

### Cost sensitivity

A storage account by itself has no financial cost; however, the settings you choose for the account do influence the cost of services in the account. Geo-redundant storage costs more than locally-redundant storage. Premium performance and the Hot access tier increase the cost of blobs.

You can use multiple storage accounts to reduce costs. For example, you could partition your data into critical and non-critical categories. You could place your critical data into a storage account with geo-redundant storage and put your non-critical data in a different storage account with locally-redundant storage.

### Tolerance for management overhead

Each storage account requires some time and attention from an administrator to create and maintain. It also increases complexity for anyone who adds data to your cloud storage; everyone in this role needs to understand the purpose of each storage account so they add new data to the correct account.

Storage accounts are a powerful tool to help you get the performance and security you need while minimizing costs.

**A typical strategy is to start with an analysis of your data and create partitions that share characteristics like location, billing, and replication strategy, and then create one storage account for each partition.**

# Choose your account settings

# Choose an account creation tool

# Exercise - Create a storage account using the Azure portal

# Knowledge check - Create a storage account

# Summary
