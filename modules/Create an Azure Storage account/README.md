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

The storage account settings we've already covered apply to the data services in the account. Here, we will discuss the three settings that apply to the account itself, rather than to the data stored in the account:

- Name
- Deployment model
- Account kind

These settings impact how you manage your account and the cost of the services within it.

## Name

Each storage account has a name. The name must be globally unique within Azure, use only lowercase letters and digits and be between 3 and 24 characters.

## Deployment model

A _deployment model_ is the system Azure uses to organize your resources. The model defines the API that you use to create, configure, and manage those resources. Azure provides two deployment models:

- **Resource Manager**: the current model that uses the Azure Resource Manager API
- **Classic**: a legacy offering that uses the Azure Service Management API

Most Azure resources only work with Resource Manager, and makes it easy to decide which model to choose. However, storage accounts, virtual machines, and virtual networks support both, so you must choose one or the other when you create your storage account.

The key feature difference between the two models is their support for grouping. The Resource Manager model adds the concept of a resource group, which is not available in the classic model. A resource group lets you deploy and manage a collection of resources as a single unit.

Microsoft recommends that you use Resource Manager for all new resources.

## Account kind

Storage account _kind_ is a set of policies that determine which data services you can include in the account and the pricing of those services. There are three kinds of storage accounts:

- **StorageV2 (general purpose v2)**: the current offering that supports all storage types and all of the latest features
- **Storage (general purpose v1)**: a legacy kind that supports all storage types but may not support all features
- **Blob storage**: a legacy kind that allows only block blobs and append blobs

Microsoft recommends that you use the **General-purpose v2** option for new storage accounts.

There are a few special cases that can be exceptions to this rule. For example, **pricing for transactions is lower in general purpose v1, which would allow you to slightly reduce costs if that matches your typical workload.**

The core advice here is to choose the **Resource Manager** deployment model and the **StorageV2 (general purpose v2)** account kind for all your storage accounts. The other options still exist primarily to allow existing resources to continue operation. For new resources, there are few reasons to consider the other choices.

# Choose an account creation tool

There are several tools that create a storage account. Your choice is typically based on if you want a GUI and whether you need automation.

## Available tools

The available tools are:

- Azure Portal
- Azure CLI (Command-line interface)
- Azure PowerShell
- Management client libraries

The portal provides a GUI with explanations for each setting. This makes the portal easy to use and helpful for learning about the options.

The other tools in the above list all support automation. The Azure CLI and Azure PowerShell let you write scripts, while the management libraries allow you to incorporate the creation into a client app.

## How to choose a tool

Storage accounts are typically based on an analysis of your data, so they tend to be relatively stable. As a result, storage-account creation is usually a one-time operation done at the start of a project. For one-time activities, the portal is the most common choice.

In the rare cases where you need automation, the decision is between a programmatic API or a scripting solution. Scripts are typically faster to create and less work to maintain because there is no need for an IDE, NuGet packages, or build steps. If you have an existing client application, the management libraries might be an attractive choice; otherwise, scripts will likely be a better option.

# Exercise - Create a storage account using the Azure portal

In this unit, you'll use the Azure portal to create a storage account that is appropriate for a fictitious southern California surf report web app.

The surf report site lets users upload photos and videos of local beach conditions. Viewers will use the content to help them choose the beach with the best surfing conditions. Your list of design and feature goals is:

- Video content must load quickly.
- The site must handle unexpected spikes in upload volume.
- Outdated content must be removed as surf conditions change so the site always shows current conditions.

To fulfill these requirements, **you decide to buffer uploaded content in an Azure Queue for processing and then transfer it to an Azure Blob for persistent storage**. You need a storage account that can hold both queues and blobs while delivering low-latency access to your content.

## Use the Azure portal to create a storage account

Sign into the Azure portal using the same account you activated the sandbox with.

On the Azure portal menu or from the **Home** page, select **Create a resource**.

In the selection panel that appears, select **Storage**.

On the right side of that pane, select **Storage account**.

![https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/5-portal-storage-select.png](https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/5-portal-storage-select.png)

### Configure the basic options

Under PROJECT DETAILS:

- Select the _Concierge Subscription_ from the **Subscription** drop-down list.
- Select the existing Resource Group ("**learn-b745e228-d148-4e5a-b543-deee583cc718**") from the drop-down list.

Under INSTANCE DETAILS:

- Enter a **Storage account name**. The name will be used to generate the public URL used to access the data in the account. The name must be unique across all existing storage account names in Azure. Names must be 3 to 24 characters long and can contain only lowercase letters and numbers.
- Select a **Location** near to you from the list above.
- Select _Standard_ for the **Performance** option. This decides the type of disk storage used to hold the data in the Storage account. **Standard** uses traditional hard disks, and Premium uses solid-state drives (SSD) for faster access. However, remember that **Premium only supports page blobs**. You'll need block blobs for your videos, and a queue for buffering - both of which are only available with the **Standard** option.
- Select **StorageV2 (general purpose v2)** for the **Account kind**. This provides access to the latest features and pricing. In particular, Blob storage accounts have more options available with this account type. You need a mix of blobs and a queue, so the Blob storage option will not work. For this application, there would be no benefit to choosing a Storage (general purpose v1) account, since that would limit the features you could access and would be unlikely to reduce the cost of your expected workload.
- Select **Locally-redundant storage (LRS)** for the **Replication** option. Data in Azure storage accounts are always replicated to ensure high availability - this option lets you choose how far away the replication occurs to match your durability requirements. In our case, the images and videos quickly become out-of-date and are removed from the site. As a result, there is little value to paying extra for global redundancy. If a catastrophic event results in data loss, you can restart the site with fresh content from your users.
- Set the **Access tier** to _Hot_. This setting is only used for Blob storage. The Hot Access Tier is ideal for frequently accessed data, and the Cool Access Tier is better for infrequently accessed data. Note that this only sets the default value - when you create a Blob, you can set a different value for the data. In our case, we want the videos to load quickly, so you'll use the high-performance option for your blobs.

The following screenshot shows the completed settings for the Basics tab. Note that the resource group, subscription, and name will have different values.

![https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/5-create-storage-account-basics.png](https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/5-create-storage-account-basics.png)

### Configure the networking options

Click the **Next: Networking >** button to move to the **Networking** tab, or select the **Networking** tab at the top of the screen.

Set the **Connectivity** method option to _Public endpoint (all networks)_. We want to allow public Internet access. Our content is public facing and you need to allow access from public clients.

### Configure the advanced options

Click the **Next: Advanced >** button to move to the **Advanced** tab, or select the **Advanced** tab at the top of the screen.

Set **Secure transfer required** to _Enabled_. The Secure transfer required setting controls whether HTTP can be used for the REST APIs used to access data in the Storage account. Setting this option to Enabled will force all clients to use SSL (**HTTPS**). Most of the time you'll want to set this to Enabled as using HTTPS over the network is considered a best practice.

WARNING: If this option is enabled, it will enforce some additional restrictions. Azure files service connections without encryption will fail, including scenarios using SMB 2.1 or 3.0 on Linux. **Because Azure storage doesn't support SSL for custom domain names, this option cannot be used with a custom domain name.**

Leave the **Large file shares** option set to _Disabled_. **Large file shares provides support up to a 100TiB**, however this type of storage account can't convert to a Geo-redundant storage offering and upgrades are permanent.

Leave the **Blob Soft delete** option set to _Disabled_. Soft delete lets you recover your blob data in many cases where blobs or blob snapshots are deleted accidentally or overwritten.

Leave the **Data Lake Storage Gen2** option as _Disabled_. This is for big-data applications that aren't relevant to this module.

The following screenshot shows the completed settings for the Advanced tab.

![https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/5-create-storage-account-advanced.png](https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/5-create-storage-account-advanced.png)

### Create

You can explore the **Tags** settings if you like. This lets you associate key/value pairs to the account for your categorization and is a feature available to any Azure resource.

Click **Review + create** to review the settings. This will do a quick validation of your options to make sure all the required fields are selected. If there are issues, they'll be reported here. Once you've reviewed the settings, click **Create** to provision the storage account.

It will take a few minutes to deploy the account.

### Verify

Select the **Storage accounts** link in the left sidebar.

Locate the new storage account in the list to verify that creation succeeded.

You created a storage account with settings driven by your business requirements. For example, you might have selected a West US datacenter because your customers were primarily located in southern California. This is a typical flow: first analyze your data and goals, and then configure the storage account options to match.

# Knowledge check - Create a storage account

# Summary
