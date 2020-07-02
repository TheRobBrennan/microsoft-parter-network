[Implement message-based communication workflows with Azure Service Bus](https://docs.microsoft.com/en-us/learn/modules/implement-message-workflows-with-service-bus/) (~54 mins)

Write C# code in a custom application that sends and receives messages using Azure Service Bus topics and queues.

# Introduction

Modern applications frequently consist of multiple parts running on separate computers and devices, which are in locations around the world. Complex networks with varying reliability and speed exist between these components. A fundamental challenge with these distributed applications is how to communicate reliably between the components.

Suppose you are a cloud developer for Contoso Slices, a global pizza delivery chain. Your employer is upgrading their technology so that users can place orders from the web or their mobile apps. Those orders will be sent to the user's preferred storefront location, where employees will make the pizza. As the dough is rolled out, pizza put in oven, boxed, and put on a delivery vehicle, updates are sent to the user's mobile app. The users even receive location updates as the delivery driver heads toward them.

Contoso Slices previously created an online ordering system that immediately stored order data in a SQL Server database. Each store had to remember to manually refresh the "web orders" page to find out if they had new orders. In addition, during peak pizza times, such as televised sporting events, the system would frequently get deadlock exceptions and timeouts. Finally, the previous system lacked central payment processing or any kind of status updates for the user.

For this new, more ambitious project, Contoso has hired a cloud architect and plans to use a decoupled architecture.

In this module, we'll learn how Azure Service Bus can help build an application that stays reliable during peak demand. We'll also see how Azure Service Bus make it easy to add functionality to our applications. Along the way, we'll be writing the C# code necessary to put these lessons to work. Here, you will see how to use Azure Service Bus topics and queues in a distributed architecture to ensure reliable communications even at times of high demand. You will also write C# code that communicates through Service Bus.

## Learning objectives

In this module, you will:

- Choose whether to use Service Bus queues, topics, or relays to communicate in a distributed application
- Configure an Azure Service Bus namespace in an Azure subscription
- Create a Service Bus topic and use it to send and receive messages
- Create a Service Bus queue and use it to send and receive messages

# Choose a messaging platform

There are many communications platforms that can help improve the reliability of a distributed application, including several within Azure. Each of these tools serves a different purpose; let's review each tool in Azure to help choose the right one.

The architecture of our pizza ordering and tracking application requires several components: a website, data storage, back-end service, etc. We can bind the components of our application together in many different ways, and a single application can take advantage of multiple techniques.

We need to decide which techniques to use in the Contoso Slices application. The first step is to evaluate each place where there is communication between multiple parts. Some components must run in a timely manner for our application to be doing its job at all. Some may be important, but not time-critical. Finally, other components, like our mobile app notifications, are a bit more optional.

Here, you will learn about the communications platforms available in Azure so that you can choose the right one for each requirement in your application.

## Decide between messages and events

Messages and events are both **datagrams**: packages of data sent from one component to another. They are different in ways that at first seem subtle, but can make significant differences in how you architect your application.

### Messages

In the terminology of distributed applications, **the defining characteristic of a message is that the overall integrity of the application may rely on messages being received**. You can think of sending a message as one component passing the baton of a workflow to a different component. The entire workflow may be a vital business process, and the message is the mortar that holds the components together.

A message generally contains the data itself, not just a reference (like an ID or URL) to data. Sending the data as part of the datagram is less brittle than sending a reference. The messaging architecture guarantees delivery of the message, and because no additional lookups are required, the message is reliably handled. However, the sending application needs to know exactly what data to include to avoid sending too much data, which requires the receiving component to do unnecessary work. In this sense, the sender and receiver of a message are often coupled by a strict data contract.

In Contoso Slices' new architecture, when a pizza order is entered, the company would likely use messages. The web front end or mobile app would send a message to the back-end processing components. In the back end, steps like routing to the store near the customer and charging the credit card would take place.

### Events

An event triggers a notification that something has occurred. Events are "lighter" than messages and are most often used for broadcast communications.

Events have the following characteristics:

- The event may be sent to multiple receivers, or to none at all
- Events are often intended to "fan out," or have a large number of subscribers for each publisher
- The publisher of the event has no expectation about the action a receiving component takes

Our pizza chain would **likely use events for notifications to users about status changes**. Status change events could be sent to Azure Event Grid, then on to Azure Functions, and to Azure Notification Hubs for a completely serverless solution.

This difference between events and messages is fundamental because communications platforms are generally designed to handle one or the other.

**Service Bus is designed to handle messages. If you want to send events, you would likely choose Event Grid.**

Azure also has Azure Event Hubs, but it is most often used for a specific type of high-flow stream of communications used for analytics. For example, if we had networked sensors on our pizza ovens, we could use Event Hubs coupled with Azure Stream Analytics to watch for patterns in the temperature changes that might indicate an unwanted fire or component wear.

## Service Bus topics, queues, and relays

Azure Service Bus can exchange messages in three different ways: queues, topics, and relays.

### What is a queue?

A queue is a simple temporary storage location for messages. A sending component adds a message to the queue. A destination component picks up the message at the front of the queue. Under ordinary circumstances, each message is received by only one receiver.

![https://docs.microsoft.com/en-us/learn/modules/implement-message-workflows-with-service-bus/media/2-service-bus-queue.png](https://docs.microsoft.com/en-us/learn/modules/implement-message-workflows-with-service-bus/media/2-service-bus-queue.png)

Queues decouple the source and destination components to insulate destination components from high demand.

During peak times, messages may come in faster than destination components can handle them. Because source components have no direct connection to the destination, the source is unaffected and the queue will grow. Destination components will remove messages from the queue as they are able to handle them. When demand drops, destination components can catch up and the queue shortens.

A queue responds to high demand like this without needing to add resources to the system. However, for messages that need to be handled relatively quickly, adding additional instances of your destination component can allow them to share the load. Each message would be handled by only one instance. This is an effective way to scale your entire application while only adding resources to the components that actually need it.

### What is a topic?

A topic is similar to a queue but can have multiple subscriptions. This means that multiple destination components can subscribe to a single topic, so each message is delivered to multiple receivers. Subscriptions can also filter the messages in the topic to receive only messages that are relevant. Subscriptions provide the same decoupled communications as queues and respond to high demand in the same way. Use a topic if you want each message to be delivered to more than one destination component.

Topics are not supported in the Basic pricing tier.

![https://docs.microsoft.com/en-us/learn/modules/implement-message-workflows-with-service-bus/media/2-service-bus-topic.png](https://docs.microsoft.com/en-us/learn/modules/implement-message-workflows-with-service-bus/media/2-service-bus-topic.png)

### What is a relay?

A relay is an object that performs synchronous, two-way communication between applications. Unlike queues and topics, it is not a temporary storage location for messages. Instead, **it provides bidirectional, unbuffered connections across network boundaries such as firewalls**.

Use a relay when you want direct communications between components as if they were located on the same network segment but separated by network security devices.

## Service Bus queues and storage queues

There are two Azure features that include message queues: **Service Bus** and **Azure Storage** accounts. As a general guide, storage queues are simpler to use but are less sophisticated and flexible than Service Bus queues.

Key advantages of Service Bus queues include:

- Supports larger messages sizees of 256 KB (standard tier) or 1MB (premium tier) per message versus 64 KB
- Supports both at-least-once and at-most-once delivery - choose between a very small chance that a message is lost or a very small chance it is handled twice
- Guarantees first-in-first-out (FIFO) order - messages are handled in the same order they are added (although FIFO is the normal operation of a queue, it is not guaranteed for every message)
- Can group multiple messages into a transaction - if one message in the transaction fails to be delivered, all messages in the transaction will not be delivered
- Supports role-based security
- Does not require destination components to continuously poll the queue

Advantages of storage queues:

- Supports unlimited queue size (versus 80-GB limit for Service Bus queues)
- Maintains a log of all messages

## How to choose a communications technology

We've seen the different concepts and the implementations Azure provides. Let's discuss what our decision process should look like for each of our communications.

**Consider the following questions**

- Is the communication an event? If so, consider using Event Grid or Event Hubs.
- Should a single message be delivered to more than one destination? If so, use a Service Bus topic. Otherwise, use a queue.

If you decide that you need a queue...

Choose Service Bus queues if:

- You need an at-most-once delivery guarantee
- You need a FIFO guarantee
- You need to group messages into transactions
- You want to receive messages without polling the queue
- You need to provide role-based access to the queues
- You need to handle messages larger than 64 KB but smaller than 256 KB
- Your queue size will not grow larger than 80 GB
- You would like to be able to publish and consume batches of messages

Choose queue storage if:

- You need a simple queue with no particular additional requirements
- You need an audit trail of all messages that pass through the queue
- You expect the queue to exceed 80 GB in size
- You want to track progress for processing a message inside of the queue

Although the components of a distributed application can communicate directly, you can often increase the reliability of that communication by using an intermediate communication platform such as Azure Service Bus or Azure Event Grid.

Event Grid is designed for events, which notify recipients only of an event and do not contain the raw data associated with that event. Azure Event Hubs is designed for high-flow analytics types of events. Azure Service Bus and storage queues are for messages, which can be used for binding the core pieces of any application workflow.

If your requirements are simple, if you want to send each message to only one destination, or if you want to write code as quickly as possible, a storage queue may be the best option. Otherwise, Service Bus queues provide many more options and flexibility.

If you want to send messages to multiple subscribers, use a Service Bus topic.

# Exercise - Implement a Service Bus topic and queue

Suppose you have an application for the sales team in your global company. Each team member has a mobile phone where your app will be installed. A web service hosted in Azure implements the business logic for your application and stores information in Azure SQL Database. There is one instance of the web service for each geographical region. You have identified the following purposes for sending messages between the mobile app and the web service:

- Messages that relate to individual sales must be sent only to the web service instance in the user's region.
- Messages that relate to sales performance must be sent to all instances of the web service.

You have decided to implement a Service Bus queue for the first use case and the Service Bus topic for the second use case.

In this exercise, you will create a Service Bus namespace, which will contain both a queue and a topic with subscriptions.

## Create a Service Bus namespace

In Azure Service Bus, a namespace is a container, with a unique fully qualified domain name, for queues, topics, and relays. You must start by creating the namespace.

Each namespace has primary and secondary shared access signature encryption keys. To gain access to the objects within the namespace, a sending or receiving component must provide these keys when it connects.

On the Azure portal menu, let's create a new `Service Bus`

In the Subscription drop-down list, select `Concierge subscription`

Under Resource group, select `learn-9edcb786-618c-45b8-a063-51d51761bd0d`

In the Name text box, type a unique name for the namespace. For example: `salesteamapprb`

In the Location drop-down list, select a location near you from the below list.

In the Pricing tier drop-down list, select `Standard`.

Click `Create` to create the Service Bus namespace.

## Create a Service Bus queue

Now that you have a namespace, you can create a queue for messages about individual sales.

Find the `Service Bus` namespace you just created.

In the top left of the namespace pane, click `+ Queue`.

In the `Create queue` pane, in the `Name` text box, type `salesmessages`, and then click `Create`. Azure creates the queue in your namespace.

![https://docs.microsoft.com/en-us/learn/modules/implement-message-workflows-with-service-bus/media/3-create-queue.png](https://docs.microsoft.com/en-us/learn/modules/implement-message-workflows-with-service-bus/media/3-create-queue.png)

## Create a Service Bus topic and subscriptions

You also want to create a topic that will be used for messages that relate to sales performance. Multiple instances of the business logic web service will subscribe to this topic from different countries. Each message will be delivered to multiple instances.

Find the `Service Bus` namespace you just created, click `+ Topic`.

In the `Create topic` pane, in the `Name` text box, type `salesperformancemessages`, ensure `Enable partitioning` is `checked`, and then click `Create`.

![https://docs.microsoft.com/en-us/learn/modules/implement-message-workflows-with-service-bus/media/3-create-topic.png](https://docs.microsoft.com/en-us/learn/modules/implement-message-workflows-with-service-bus/media/3-create-topic.png)

When the topic has been created, in the `Service Bus Namespace` pane, under `Entities`, click `Topics`.

In the list of topics, click `salesperformancemessages`, and then click `+ Subscription`.

In the `Name` text box type `Americas`, in `Max delivery count` type `1`, and then click `Create`.

Click `+ Subscription`, and in the `Name` text box type `EuropeAndAfrica`, in `Max delivery count` type `1`, and then click `Create`.

You have built the infrastructure required to use Service Bus to increase the resilience of your sales force distributed application. You have created a queue for messages about individual sales and a topic for messages about sales performance. The topic includes multiple subscriptions because messages sent to that topic can be delivered to multiple recipient web services around the world.

# Write code that uses Service Bus queues

Distributed applications use queues, such as Service Bus queues, as temporary storage locations for messages that are awaiting delivery to a destination component. To send and receive messages through a queue, you must write code in the source and destination components.

Consider the Contoso Slices application. The customer places the order through a website or mobile app. Because websites and mobile apps run on customer devices, there is really no limit to how many orders could come in at once. By having the mobile app and website deposit the orders in a queue, we can allow the back-end component (a web app) to process orders from that queue at its own pace.

The Contoso Slices application actually has several steps to handle a new order. All the steps are dependent on first authorizing payment, so we decide to use a queue. Our receiving component's first job will be processing the payment.

In the mobile app and website, Contoso needs to write code that adds a message to the queue. In the back-end web app, they'll write code that picks up messages from the queue.

Here, you will learn how to write that code.

## The Microsoft.Azure.ServiceBus NuGet package

To make it easy to write code that sends and receives messages through Service Bus, Microsoft provides a library of .NET classes, which you can use in any .NET Framework language to interact with a Service Bus queue, topic, or relay. You can include this library in your application by adding the **Microsoft.Azure.ServiceBus** NuGet package.

The most important class in this library for queues is the `QueueClient` class. You must start by instantiating this class both in sending and receiving components.

## Connection strings and keys

Source components and destination components both need two pieces of information to connect to a queue in a Service Bus namespace:

- The **location of the Service Bus namespace**, also known as an **endpoint**. The location is specified as a fully qualified domain name within the **servicebus.windows.net** domain. For example: **pizzaService.servicebus.windows.net**.
- An access key. Service Bus restricts access to queues, topics, and relays by requiring an access key.

Both of these pieces of information are provided to the `QueueClient` object in the form of a connection string. You can obtain the correct connection string for your namespace from the Azure portal.

## Calling methods asynchronously

The queue in Azure may be located thousands of miles away from sending and receiving components. Even if it is physically close, slow connections and bandwidth contention may cause delays when a component calls a method on the queue. For this reason, the Service Bus client library makes `async` methods available for interacting with the queues. We'll use these methods to avoid blocking a thread while waiting for calls to complete.

When sending a message to a queue, for example, use the `QueueClient.SendAsync()` method with the `await` keyword.

## Write code that sends to queues

In any sending or receiving component, you should add the following `using` statements to any code file that calls a Service Bus queue:

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.ServiceBus;
```

Next, create a new `QueueClient` object and pass it the connection string and the name of the queue:

```csharp
queueClient = new QueueClient(TextAppConnectionString, "PrivateMessageQueue");
```

You can send a message to the queue by calling the `QueueClient.SendAsync()` method and passing the message in the form of a UTF-8 encoded string:

```csharp
string message = "Sure would like a large pepperoni!";
var encodedMessage = new Message(Encoding.UTF8.GetBytes(message));
await queueClient.SendAsync(encodedMessage);
```

## Receive messages from the queue

To receive messages, you must first register a message handler - this is the method in your code that will be invoked when a message is available on the queue.

```csharp
queueClient.RegisterMessageHandler(MessageHandler, messageHandlerOptions);
```

Do your processing work. Then, within the message handler, call the `QueueClient.CompleteAsync()` method to remove the message from the queue:

```csharp
await queueClient.CompleteAsync(message.SystemProperties.LockToken);
```

# Exercise - Write code that uses Service Bus queues

You've chosen to use a Service Bus queue to exchange messages about individual sales between the mobile app that your sales personnel use and the web service, hosted in Azure, that will store details about each sale in an Azure SQL Database instance.

You've already implemented the necessary objects in your Azure subscription. Now, you want to write code that sends messages to that queue and retrieves messages.

## Clone and open the starter application

In this unit, you'll build two console applications. The first application places messages into a Service Bus queue and the second retrieves them. The applications are part of a single .NET Core solution.

Start by cloning the solution: run the following commands in the Cloud Shell:

```sh
cd ~
git clone https://github.com/MicrosoftDocs/mslearn-connect-services-together.git
```

Next, change directories into the starter folder and open the Cloud Shell editor:

```sh
cd mslearn-connect-services-together/implement-message-workflows-with-service-bus/src/start
code .
```

## Configure a connection string to a Service Bus namespace

In order to access a Service Bus namespace and use a queue, you must configure two pieces of information in your console apps:

- The endpoint for your namespace
- The shared access key for authentication

Both of these values can be obtained from the Azure portal in the form of a complete connection string.

For simplicity, you will hard-code the connection string in the `Program.cs` file of both console applications. In a production application, you might use a configuration file or even Azure Key Vault to store the connection string.

Run the following command in the CloudShell to display the primary connection string for your Service Bus namespace. Replace <namespace-name> with the name of your Service Bus namespace:

```sh
az servicebus namespace authorization-rule keys list \
    --resource-group learn-9edcb786-618c-45b8-a063-51d51761bd0d \
    --name RootManageSharedAccessKey \
    --query primaryConnectionString \
    --output tsv \
    --namespace-name salesteamapprb

Endpoint=sb://salesteamapprb.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=95c/nn1CjiexHWCPzAKG7dOgVaqlLRrziTtPdhrlc4g=
```

You'll need this connection string multiple times throughout this module, so you might want to paste it somewhere handy.

Copy the key from Cloud Shell. In the editor, open `privatemessagesender/Program.cs` and locate the following line of code:

```csharp
const string ServiceBusConnectionString = "";
```

Paste the connection string between the quotation marks. Save the file using the Ctrl+S keys.

Repeat the previous step in `privatemessagereceiver/Program.cs`, pasting in the same connection string value. Save the file either through the "..." menu, or the accelerator key (Ctrl+S on Windows and Linux, Cmd+S on macOS).

## Write code that sends a message to the queue

To complete the component that sends messages about sales, open `privatemessagesender/Program.cs` in the editor.

Locate the `SendSalesMessageAsync()` method.

Within that method, locate the following line of code:

```csharp
// Create a queue client here
```

To create a queue client, replace that line of code with the following code:

```csharp
queueClient = new QueueClient(ServiceBusConnectionString, QueueName);
```

Within the try...catch block, locate the following line of code:

```csharp
// Create and send a message here
```

To create and format a message for the queue, replace that line of code with the following code:

```csharp
string messageBody = $"$10,000 order for bicycle parts from retailer Adventure Works.";
var message = new Message(Encoding.UTF8.GetBytes(messageBody));
```

To display the message in the console, on the next line, add the following code:

```csharp
Console.WriteLine($"Sending message: {messageBody}");
```

To send the message to the queue, on the next line, add the following code:

```csharp
await queueClient.SendAsync(message);
```

Locate the following line of code:

```csharp
// Close the connection to the queue here
```

To close the connection to the Service Bus, replace that line of code with the following code:

```csharp
await queueClient.CloseAsync();
```

Save the file.

## Send a message to the queue

## Write code that receives a message from the queue

## Retrieve a message from the queue

# Write code that uses Service Bus topics

# Exercise - Write code that uses Service Bus topics

# Summary
