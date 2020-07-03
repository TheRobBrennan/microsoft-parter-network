[Enable reliable messaging for Big Data applications using Azure Event Hubs](https://docs.microsoft.com/en-us/learn/modules/enable-reliable-messaging-for-big-data-apps-using-event-hubs/) (~56 mins)

Connect sending and receiving applications with Event Hubs so you can handle extremely high loads without losing data.

# Introduction

Big data applications must be able to process increased throughput by scaling out to meet increased transaction volumes.

Suppose you work in the credit card department of a bank. You're part of a team that manages the system responsible for fraud testing to determine whether to approve or decline each transaction. Your system receives a stream of transactions and needs to process them in real time.

The load on your system can spike during weekends and holidays. The system must handle the increased throughput efficiently and accurately. Given the sensitive nature of the transactions, even the slightest error can have a considerable impact.

**Azure Event Hubs can receive and process a large number of transactions.** It can also be configured to scale dynamically, when required, to handle increased throughput. In this module, you'll learn how to connect Event Hubs to your application and reliably process large transaction volumes.

## Learning objectives

In this module, you will:

- Create an Event Hub using the Azure CLI.
- Configure applications to send or receive messages through an Event Hub.
- Evaluate Event Hub performance using the Azure portal.

# Create an Event Hub using the Azure CLI

Your team has decided to use the capabilities of Azure Event Hubs to manage and process the increasing transaction volumes coming through your system.

An Event Hub is an Azure resource, so your first step is to create a new hub in Azure and configure it to meet the specific requirements of your applications.

## What is an Azure Event Hub?

Azure Event Hubs is a cloud-based, event-processing service **can receive and process millions of events per second**. Event Hubs acts as a front door for an event pipeline, to receive incoming data and stores this data until processing resources are available.

An entity that sends data to the Event Hubs is called a _publisher_, and an entity that reads data from the Event Hubs is called a _consumer_ or a _subscriber_. Azure Event Hubs sits between these two entities to divide the production (from the publisher) and consumption (to a subscriber) of an event stream. This decoupling helps to manage scenarios where the rate of event production is much higher than the consumption. The following illustration shows the role of an Event Hub.

![https://docs.microsoft.com/en-us/learn/modules/enable-reliable-messaging-for-big-data-apps-using-event-hubs/media/2-event-hub-overview.png](https://docs.microsoft.com/en-us/learn/modules/enable-reliable-messaging-for-big-data-apps-using-event-hubs/media/2-event-hub-overview.png)

### Events

An **event** is a small packet of information (a datagram) that contains a notification. Events can be published individually, or in batches, but **a single publication (individual or batch) can't exceed 1 MB**.

### Publishers and subscribers

Event publishers are any application or device that can send out events using either HTTPS or **Advanced Message Queuing Protocol (AMQP) 1.0**.

For publishers that send data frequently, AMQP has better performance. However, it has a higher initial session overhead, because a persistent bidirectional socket and transport-level security (TLS) or SSL/TLS has to be set up first.

For more intermittent publishing, HTTPS is the better option. Though HTTPS requires additional overhead for each request, there isn't the session initialization overhead.

Existing Kafka-based clients, using Apache Kafka 1.0 and newer client versions, can also act as Event Hubs publishers.

Event subscribers are applications that use one of two supported programmatic methods to receive and process events from an Event Hub.

- **EventHubReceiver** - A simple method that provides limited management options.
- **EventProcessorHost** - An efficient method that we'll use later in this module.

### Consumer groups

An Event Hub **consumer group** represents **a specific view of an Event Hub data stream**. By using separate consumer groups, multiple subscriber applications can process an event stream independently, and without affecting other applications. However, the use of many consumer groups isn't a requirement, and for many applications, the single default consumer group is sufficient.

### Pricing

There are three pricing tiers for Azure Event Hubs: Basic, Standard, and Dedicated. The tiers differ in terms of supported connections, the number of available Consumer groups, and throughput. When using Azure CLI to create an Event Hubs namespace, if you don't specify a pricing tier, the default of **Standard** (20 Consumer groups, 1000 Brokered connections) is assigned.

## Creating and configuring a new Azure Event Hubs

There are two main steps when creating and configuring new Azure Event Hubs. The first step is to define the Event Hubs **namespace**. The second step is to create an Event Hub in that namespace.

### Defining an Event Hubs namespace

An Event Hubs namespace is a containing entity for managing one or more Event Hubs. Creating an Event Hubs namespace typically involves the following configuration:

1. Define namespace-level settings.

Certain settings such as namespace capacity (configured using **throughput units**), pricing tier, and performance metrics are defined at the namespace level. These settings are applicable for all the Event Hubs within that namespace.

**If you don't define these settings, a default value is used: 1 for capacity and Standard for pricing tier.**

Keep the following aspects in mind:

- You can't change the throughput unit once you set it.
- You must balance your configuration against your Azure budget expectations.
- You might consider configuring different Event Hubs for different throughput requirements. For example, if you have a sales data application and you're planning for two Event Hubs, it would make sense to use a separate namespace for each hub.

You'll configure one namespace for high throughput collection of real-time sales data telemetry and one namespace for infrequent event log collection. This way, you only need to configure (and pay for) high throughput capacity on the telemetry hub.

2. Select a unique name for the namespace. The namespace is accessible through this URL: _namespace.servicebus.windows.net_

3. Defining the following optional properties:

- Enable Kafka. This option enables Kafka applications to publish events to the Event Hub.
- Make this namespace zone redundant. Zone-redundancy replicates data across separate data centers with their independent power, networking, and cooling infrastructures.
- Enable Auto-Inflate and Auto-Inflate Maximum Throughput Units. Auto-Inflate provides an automatic scale-up option by increasing the number of throughput units up to a maximum value. This option is useful to avoid throttling in situations when incoming or outgoing data rates exceed the currently set number of throughput units.

### Azure CLI commands for creating an Event Hubs namespace

To create a new Event Hubs namespace, you'll use the `az eventhubs namespace` commands. Here's a brief description of the subcommands we'll use in the exercise.

| Command            | Description                                                                                                                                                                                                                                                                        |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| create             | Create the Event Hubs namespace.                                                                                                                                                                                                                                                   |
| authorization-rule | All Event Hubs within the same Event Hubs namespace share common connection credentials. You'll need these credentials when you configure applications to send and receive messages using the Event Hub. This command returns the connection string for your Event Hubs namespace. |

### Configuring a new Event Hub

After you create the Event Hubs namespace, you can create an Event Hub. When creating a new Event Hub, there are several mandatory parameters.

The following parameters are required to create an Event Hub:

- **Event Hub name** - Event Hub name that is unique within your subscription and:
  - Is between 1 and 50 characters long
  - Contains only letters, numbers, periods, hyphens, and underscores
  - Starts and ends with a letter or number
- **Partition Count** - The number of partitions required in an Event Hub (between 2 and 32). The partition count should be directly related to the expected number of concurrent consumers and can't be changed after the hub has been created. The partition separates the message stream so that consumer or receiver applications only need to read a specific subset of the data stream. If not defined, this value defaults to `4`.
- **Message Retention** - The number of days (between 1 and 7) that messages will remain available if the data stream needs to be replayed for any reason. If not defined, this value defaults to `7`.

You can also optionally configure an Event Hub to stream data to an Azure Blob storage or Azure Data Lake Store account.

### Azure CLI commands for creating an Event Hub

To create a new Event Hub with the Azure CLI, you'll use the az eventhubs eventhub command set. Here's a brief description of the subcommands we'll be using:

| Command | Description                                     |
| ------- | ----------------------------------------------- |
| create  | Creates the Event Hub in a specified namespace. |
| show    | Displays the details of your Event Hub.         |

## Summary

To deploy Azure Event Hubs, you must configure an Event Hubs namespace and then configure the Event Hub itself. In the next section, you'll go through the detailed configuration steps to create a new namespace and Event Hub.

# Exercise - Use the Azure CLI to Create an Event Hub

You're now ready to create a new Event Hub. After creating the Event Hub, you'll use the Azure portal to view your new hub.

## Create an Event Hubs namespace

Let's create an Event Hubs namespace using Bash shell supported by Azure Cloud shell.

First, set default values for the Azure CLI in the Cloud Shell. This will keep you from having to type these in every time. In particular, let's set the resource group and location. Type the following command into the Azure CLI, feel free to replace the location with one close to you.

```sh
az configure --defaults group=learn-b5bddcad-0f44-49fb-9f09-72219e09d13b location=westus2
```

Create the Event Hubs namespace using the az eventhubs namespace create command. Use the following parameters.

| Parameter                   | Description                                                                                                                                                                                        |
| --------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| --name (required)           | Enter a 6-50 characters-long unique name for your Event Hubs namespace. The name should contain only letters, numbers, and hyphens. It should start with a letter and end with a letter or number. |
| --resource-group (required) | This will be the pre-created Azure sandbox resource group supplied from the defaults.                                                                                                              |
| --l (optional)              | Enter the location of your nearest Azure datacenter, this will use your default.                                                                                                                   |
| --sku (optional)            | The pricing tier for the namespace [Basic / Standard], defaults to Standard. This determines the connections and consumer thresholds.                                                              |

Set the name into an environment variable so we can reuse it.

```sh
NS_NAME=ehubns-$RANDOM
az eventhubs namespace create --name $NS_NAME
```

Fetch the connection string for your Event Hubs namespace using the following command. You'll need this to configure applications to send and receive messages using your Event Hub.

```sh
az eventhubs namespace authorization-rule keys list \
    --name RootManageSharedAccessKey \
    --namespace-name $NS_NAME
```

| Parameter                   | Description                                                                           |
| --------------------------- | ------------------------------------------------------------------------------------- |
| --resource-group (required) | This will be the pre-created Azure sandbox resource group supplied from the defaults. |
| --namespace-name (required) | Enter the name of the namespace you created.                                          |

This command returns a JSON block with the connection string for your Event Hubs namespace that you'll use later to configure your publisher and consumer applications. Save the value of the following keys for later use.

- primaryConnectionString
- primaryKey

```sh
{
  "aliasPrimaryConnectionString": null,
  "aliasSecondaryConnectionString": null,
  "keyName": "RootManageSharedAccessKey",
  "primaryConnectionString": "Endpoint=sb://ehubns-14870.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yRVVI2CvBERAtrGLNY6tAgEzg7srtwRgrdwHCoXcco4=",
  "primaryKey": "yRVVI2CvBERAtrGLNY6tAgEzg7srtwRgrdwHCoXcco4=",
  "secondaryConnectionString": "Endpoint=sb://ehubns-14870.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=AsCbCI/BeETHNJIQyRKYjySfrJiTapmoErp7W2Jk4CM=",
  "secondaryKey": "AsCbCI/BeETHNJIQyRKYjySfrJiTapmoErp7W2Jk4CM="
}
```

## Create an Event Hub

Now let's create your new Event Hub.

Create a new Event Hub using the `eventhub create` command. It needs the following parameters:

| Parameter                   | Description                      |
| --------------------------- | -------------------------------- |
| --name (required)           | Enter a name for your Event Hub. |
| --resource-group (required) | Resource group owner.            |
| --namespace-name (required) | Enter the namespace you created. |

Let's define the Event Hub name in an environment variable first.

```sh
HUB_NAME=hubname-$RANDOM

# Create the event hub
az eventhubs eventhub create --name $HUB_NAME --namespace-name $NS_NAME
```

View the details of your Event Hub using the eventhub show command. It takes:

| Parameter                   | Description                      |
| --------------------------- | -------------------------------- |
| --resource-group (required) | Resource group owner.            |
| --namespace-name (required) | Enter the namespace you created. |
| --name (required)           | Name of the Event Hub.           |

```sh
az eventhubs eventhub show --namespace-name $NS_NAME --name $HUB_NAME
```

This will generate output similar to:

```json
{
  "captureDescription": null,
  "createdAt": "2020-07-03T05:25:21.307000+00:00",
  "id": "/subscriptions/c33c9fed-a8e5-40ab-a49a-fe578004f6f3/resourceGroups/learn-b5bddcad-0f44-49fb-9f09-72219e09d13b/providers/Microsoft.EventHub/namespaces/ehubns-14870/eventhubs/hubname-6540",
  "location": "West US 2",
  "messageRetentionInDays": 7,
  "name": "hubname-6540",
  "partitionCount": 4,
  "partitionIds": ["0", "1", "2", "3"],
  "resourceGroup": "learn-b5bddcad-0f44-49fb-9f09-72219e09d13b",
  "status": "Active",
  "type": "Microsoft.EventHub/Namespaces/EventHubs",
  "updatedAt": "2020-07-03T05:25:21.777000+00:00"
}
```

## View the Event Hub in the Azure portal

Next, let's see what this looks like in the Azure portal.

Find your Event Hubs namespace using the Search bar at the top of portal.

Select your namespace to open it.

Select **Event Hubs namespace** under the **ENTITIES** section.

Click **Event Hubs**.

Your Event Hub displays with a status of **Active**, and default values for **Message Retention** (7) and **Partition Count** of (4).

![https://docs.microsoft.com/en-us/learn/modules/enable-reliable-messaging-for-big-data-apps-using-event-hubs/media/3-event-hub.png](https://docs.microsoft.com/en-us/learn/modules/enable-reliable-messaging-for-big-data-apps-using-event-hubs/media/3-event-hub.png)

## Summary

You've now created a new Event Hub, and you've all the necessary information ready to configure your publisher and consumer applications.

# Configure applications to send or receive messages through an Event Hub

After you've created and configured your Event Hub, you'll need to configure applications to send and receive event data streams.

For example, a payment processing solution will use some form of sender application to collect customer's credit card data and a receiver application to verify that the credit card is valid.

Although there are differences in how a Java application is configured, compared to a .NET application, the principles are the same for enabling applications to connect to an Event Hub, and to successfully send or receive messages.

## What are the minimum Event Hub application requirements?

## Azure CLI commands for creating a general-purpose standard storage account

## Shell command for cloning an application GitHub repository

## Editing files in the Cloud Shell

## Summary

# Exercise - Configure applications to send or receive messages through an Event Hub

# Evaluate the performance of the deployed Event Hub using the Azure portal

# Exercise - Evaluate the performance of the deployed Event Hub using the Azure portal

# Summary
