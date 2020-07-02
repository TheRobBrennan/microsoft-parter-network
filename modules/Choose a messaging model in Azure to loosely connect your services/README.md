[Choose a messaging model in Azure to loosely connect your services](https://docs.microsoft.com/en-us/learn/modules/choose-a-messaging-model-in-azure-to-connect-your-services/) (~45 mins)

When you have an application that consists of components running on different computers, servers, and mobile devices, reliable communications between those components can be difficult and unreliable. Azure provides several technologies that you can use to communicate more reliably, including Storage queues, Event Hubs, Event Grid, and Service Bus. This module shows you how to choose the best technology for your communication task.

# Introduction

Many applications consist of programs that run on several different computers or devices. In such distributed applications, messages must be sent between the components across networks and long distances. Even on the same server or in the same data center, loosely coupled architectures require mechanisms for components to communicate. Reliable messaging is often a critical problem.

Suppose you work at a software company that develops a music-sharing application. Musicians can upload music they create to your platform by using a web front end or a mobile app. They can listen to and comment on other members' work. The application consists of a website that runs at your ISP, a mobile app that runs on users' mobile devices, a web API that runs in Azure, and an Azure SQL Database where data is stored.

You have observed that at times of high demand, some music files are not successfully uploaded, and some comments are not posted. Your testing shows that these issues are caused by dropped messages between front-end components and the web API. You plan to solve these issues by using one or more of the following technologies: Azure Storage queues, Azure Event Hubs, Azure Event Grid, and Azure Service Bus.

Here, you will learn how to choose the right messaging technology in Azure for each communication task in a distributed application.

## Learning objectives

In this module, you will:

- Describe events and messages, and the challenges you can use them to solve in a distributed application.
- Identify scenarios in which Storage queue is the best messaging technology for an application.
- Identify scenarios in which Event Grid is the best messaging technology for an application.
- Identify scenarios in which Event Hubs is the best messaging technology for an application.
- Identify scenarios in which Service Bus is the best messaging technology for an application.

# Choose whether to use messages or events

Suppose you are planning the architecture of a distributed music-sharing application. You want to ensure that the application is as reliable and scalable as possible, and you intend to use Azure technologies to build a robust communication infrastructure.

Before you can choose the right Azure technology, you must understand each individual communication that the components of the application exchange. For each communication, you can choose a different Azure technology.

The first thing to understand about a communication is whether it sends **messages** or **events**. This knowledge will help you choose the appropriate Azure service to use.

[VIDEO: Communication strategies in Azure (APIs) ~3:20](https://www.microsoft.com/en-us/videoplayer/embed/RE2yuaw?pid=RE2yuaw-ax-86-id-oneplayer&postJsllMsg=true&autoplay=false&mute=false&loop=false&market=en-us&playFullScreen=false)

## What is a message?

In the terminology of distributed applications, **messages** have the following characteristics:

- A message contains raw data, produced by one component, that will be consumed by another component.
- A message contains the data itself, not just a reference to that data.
- The sending component expects the message content to be processed in a certain way by the destination component. The integrity of the overall system may depend on both sender and receiver doing a specific job.

For example, suppose a user uploads a new song by using the mobile music-sharing app. The mobile app must send that song to the web API that runs in Azure. The song media file itself must be sent, not just an alert that indicates that a new song has been added. The mobile app expects that the web API will store the new song in the database and make it available to other users. This is an example of a message.

## What is an event?

**Events** are lighter weight than messages, and are most often used for broadcast communications. The components sending the event are known as **publishers**, and receivers are known as **subscribers**.

With events, receiving components will generally decide in which communications they are interested, and will "subscribe" to those events. The subscription is managed by an intermediary, like Azure Event Grid or Azure Event Hubs. When publishers send an event, the intermediary will route that event to interested subscribers. This pattern is known as a "publish-subscribe architecture." It's not the only way to deal with events, but it is the most common.

Events have the following characteristics:

- An event is a lightweight notification that indicates that something happened.
- The event may be sent to multiple receivers, or to none at all.
- Events are often intended to "fan out," or have a large number of subscribers for each publisher.
- The publisher of the event has no expectation about the action a receiving component takes.
- Some events are discrete units and unrelated to other events.
- Some events are part of a related and ordered series.

For example, suppose the music file upload has been completed, and the new song has been added to the database. In order to inform users of the new file, the web API must inform the web front end and mobile app users of the new file. The users can choose whether to listen to the new song, so the initial notification does not include the music file but only notifies users that the song exists. The sender does not have a specific expectation that the event receivers will do anything particular in responsiveness of receiving this event.

This scenario is an example of a discrete event.

## How to choose messages or events

A single application is likely to use events for some purposes and messages for others. Before you choose, you must analyze your application's architecture and all its use cases, to identify all the different purposes where its components have to communicate with each other.

Events are more likely to be used for broadcasts and are often ephemeral, meaning a communication might not be handled by any receiver if none is currently subscribing. Messages are more likely to be used where the distributed application requires a guarantee that the communication will be processed.

For each communication, consider the following question: **Does the sending component expect the communication to be processed in a particular way by the destination component?**

If the answer is yes, choose to use a message. If the answer is no, you may be able to use events.

Understanding how your components need to communicate will help you to choose how your components will communicate. Let's start with messages.

## Check your knowledge

# Choose a message-based delivery with queues

Suppose you are planning the architecture for your music-sharing application. You want to ensure that music files are uploaded to the web API reliably from the mobile app - we then want to deliver the details about new songs directly to the app when an artist adds new music to their collection. This is a perfect use of a message-based system and Azure offers two solutions to this problem:

- Azure Queue Storage
- Azure Service Bus

## What is Azure Queue Storage?

Queue storage is a service that uses Azure Storage to store large numbers of messages that can be securely accessed from anywhere in the world using a simple REST-based interface. Queues can contain millions of messages, limited only by the capacity of the storage account that owns it.

## What is Azure Service Bus Queues?

Service Bus is a message broker system intended for enterprise applications. These apps often utilize multiple communication protocols, have different data contracts, higher security requirements, and can include both cloud and on-premises services. Service Bus is built on top of a dedicated messaging infrastructure designed for exactly these scenarios.

Both of these services are based on the idea of a "queue" which holds sent messages until the target is ready to receive them.

## What are Azure Service Bus Topics?

Azure Service Bus topics are like queues, but can have multiple subscribers. When a message is sent to a topic instead of a queue multiple components can be triggered to do their work. Imagine in a music sharing application, a user listening to a song. The mobile app might send a message to the "Listened" topic. That topic will have a subscription for "UpdateUserListenHistory" and a different subscription for "UpdateArtistsFanList". Each of those functions is handled by a different component that receives its own copy of the message.

Internally, topics use queues. When you post to a topic, the message is copied and dropped into the queue for each subscription. The queue means that the message copy will stay around to be processed **by each subscription branch** even if the component processing that subscription is too busy to keep up.

## Benefits of queues

Queue infrastructures can support many advanced features that make them very useful in the following ways.

### Increased reliability

Queues are used by distributed applications as a temporary storage location for messages pending delivery to a destination component. The source component can add a message to the queue and destination components can retrieve the message at the front of the queue for processing.

**Queues increase the reliability of the message exchange because, at times of high demand, messages can simply wait until a destination component is ready to process them.**

### Message delivery guarantees

Queuing systems usually guarantee delivery of each message in the queue to a destination component. However, these guarantees can take different approaches:

- **At-Least-Once Delivery**: In this approach, each message is guaranteed to be delivered to at least one of the components that retrieve messages from the queue. Note, however, that in certain circumstances, it is possible that the same message may be delivered more than once. For example, if there are two instances of a web app retrieving messages from a queue, ordinarily each message goes to only one of those instances. However, if one instance takes a long time to process the message, and a time-out expires, the message may be sent to the other instance as well. Your web app code should be designed with this possibility in mind.
- **At-Most-Once Delivery**: In this approach, each message is not guaranteed to be delivered, and there is a very small chance that it may not arrive. However, unlike At-Least-Once delivery, there is no chance that the message will be delivered twice. This is sometimes referred to as **"automatic duplicate detection"**.
- **First-In-First-Out (FIFO)**: In most messaging systems, messages usually leave the queue in the same order in which they were added, but you should consider whether this order is guaranteed. If your distributed application requires that messages are processed in precisely the correct order, you must choose a queue system that includes a FIFO guarantee.

### Transactional support

Some closely related groups of messages may cause problems when delivery fails for one message in the group.

For example, consider an e-commerce application. When the user clicks the Buy button, a series of messages might be generated and sent off to various processing destinations:

- A message with the order details is sent to a fulfillment center
- A message with the total and payment details is sent to a credit card processor.
- A message with the receipt information is sent to a database to generate an invoice for the customer

In this case, we want to make sure all messages get processed, or none of them are processed. We won't be in business long if the credit card message is not delivered, and all our orders are fulfilled without payment! You can avoid these kinds of problems by grouping the two messages into a transaction. Message transactions succeed or fail as a single unit - just like in the database world. If the credit card details message delivery fails, then so will the order details message.

## Which service should I choose?

Having understood that the communication strategy for this architecture should be a message, you must choose whether to use Azure Storage queues or Azure Service Bus, both of which can be used to store and deliver messages between your components. Each has a slightly different feature set, which means you can choose one or the other, or use both, depending on the problem you are solving.

Choose Service Bus Topics if

- you need multiple receivers to handle each message

Choose Service Bus queues if:

- You need an At-Most-Once delivery guarantee.
- You need a FIFO guarantee.
- You need to group messages into transactions.
- You want to receive messages without polling the queue.
- You need to provide a role-based access model to the queues.
- You need to handle messages larger than 64 KB but less than 256 KB.
- Your queue size will not grow larger than 80 GB.
- You would like to be able to publish and consume batches of messages.

Queue storage isn't quite as feature-rich, but if you don't need any of those features, it can be a simpler choice. In addition, it's the best solution if your app has any of the following requirements.

Choose Queue storage if:

- You need an audit trail of all messages that pass through the queue.
- You expect the queue to exceed 80 GB in size.
- You want to track progress for processing a message inside of the queue.

A queue is a simple, temporary storage location for messages sent between the components of a distributed application. Use a queue to organize messages and gracefully handle unpredictable surges in demand.

Use Storage queues when you want a simple and easy-to-code queue system. For more advanced needs, use Service Bus queues. If you have multiple destinations for a single message, but need queue-like behavior, use topics.

# Choose Azure Event Grid

Many applications use a publish-subscribe model to notify distributed components that something happened, or that some object changed. Suppose you have a music-sharing application with a Web API that runs in Azure. When a user uploads a new song, you need to notify all the mobile apps installed on user devices around the world who are interested in that genre.

In this architecture, the publisher of the sound file doesn't need to know about any of the subscribers interested in the shared music. Also, we want to have a one-to-many relationship where we can have multiple subscribers who can optionally decide whether they are interested in this new song. Azure Event Grid is a perfect solution for this sort of architecture.

## What is Azure Event Grid?

Azure Event Grid is a fully-managed event routing service running on top of Azure Service Fabric. Event Grid distributes events from different sources, such as Azure Blob storage accounts or Azure Media Services, to different handlers, such as Azure Functions or Webhooks. Event Grid was created to make it easier to build event-based and serverless applications on Azure.

Event Grid supports most Azure services as a publisher or subscriber and can be used with third-party services. It provides a dynamically scalable, low-cost, messaging system that allows publishers to notify subscribers about a status change. The following illustration shows Azure Event Grid receiving messages from multiple sources and distributing them to event handlers based on subscription.

There are several concepts in Azure Event Grid that connect a source to a subscriber:

- **Events**: What happened.
- **Event sources**: Where the event took place.
- **Topics**: The endpoint where publishers send events.
- **Event subscriptions**: The endpoint or built-in mechanism to route events, sometimes to multiple handlers. Subscriptions are also used by handlers to filter incoming events intelligently.
- **Event handlers**: The app or service reacting to the event.

The following illustration shows an Azure Event Grid positioned between multiple event sources and multiple event handlers. The event sources send events to the Event Grid and the Event Grid forwards relevant events to the subscribers. Event Grid use topics to decide which events to send to which handlers. Events sources tag each event with one or more topics, and event handlers subscribe to the topics they are interested in.

![https://docs.microsoft.com/en-us/learn/modules/choose-a-messaging-model-in-azure-to-connect-your-services/media/4-event-grid.png](https://docs.microsoft.com/en-us/learn/modules/choose-a-messaging-model-in-azure-to-connect-your-services/media/4-event-grid.png)

### What is an event?

Events are the data messages passing through Event Grid that describe what has taken place. Each event is self-contained, can be up to 64 KB, and contains several pieces of information based on a schema defined by Event Grid:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

| Field           | Description                                                                                                                                                                                                                                                                          |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| topic           | The full resource path to the event source. Event Grid provides this value.                                                                                                                                                                                                          |
| subject         | Publisher-defined path to the event subject.                                                                                                                                                                                                                                         |
| id              | The unique identifier for event.                                                                                                                                                                                                                                                     |
| eventType       | One of the registered event types for this event source. This is a value you can create filters against, e.g. `CustomerCreated`, `BlobDeleted`, `HttpRequestReceived`, etc.                                                                                                          |
| eventTime       | The time the event was generated based on the provider's UTC time.                                                                                                                                                                                                                   |
| data            | Specific information that is relevant to the type of event. For example, an event about a new file being created in Azure Storage has details about the file, such as the `lastTimeModified` value. Or, an Event Hubs event has the URL of the Capture file. This field is optional. |
| dataVersion     | The schema version of the data object. The publisher defines the schema version.                                                                                                                                                                                                     |
| metadataVersion | The schema version of the event metadata. Event Grid defines the schema of the top-level properties. Event Grid provides this value.                                                                                                                                                 |

Event Grid sends an event to indicate something has happened or changed. However, the actual object that was changed is not part of the event data. Instead, a URL or identifier is often passed to reference the changed object.

### What is an event source?

Event sources are responsible for sending events to Event Grid. Each event source is related to one or more event types. For example, Azure Storage is the event source for blob created events. IoT Hub is the event source for device created events. Your application is the event source for custom events that you define. We'll look at event sources in more detail in a moment.

Azure Event Hub has the concept of an event publisher which is often confused with the event source. A publisher to Event Hub is the user or organization that decides to send events to Event Grid. For example, Microsoft publishes events for several Azure services. You can publish events from your own application. Organizations that host services outside of Azure can publish events through Event Grid. The event source is the publisher and the specific service generating the event for that publisher. In this unit, we use "publisher" and "event source" interchangeably to represent the entity sending the message to Event Hub.

### What is an event topic?

Event topics categorize events into groups. **Topics are represented by a public endpoint** and are where the event source sends events _to_. When designing your application, you can decide how many topics to create. Larger solutions will create a custom topic for each category of related events, while smaller solutions might send all events to a single topic. For example, consider an application that sends events related to modifying user accounts and processing orders. It's unlikely any event handler wants both categories of events. Create two custom topics and let event handlers subscribe to the one that interests them. Event subscribers can filter for the event types they want from a specific topic.

Topics are divided into **system** topics, and **custom** topics.

#### System topics

System topics are built-in topics provided by Azure services. You don't see system topics in your Azure subscription because the publisher owns the topics, but you can subscribe to them. To subscribe, you provide information about the resource you want to receive events from.

**As long as you have access to the resource, you can subscribe to its events.**

#### Custom topics

Custom topics are application and third-party topics. When you create or are assigned access to a custom topic, you see that custom topic in your subscription.

### What is an event subscription?

Event Subscriptions define which events on a topic an event handler wants to receive. A subscription can also filter events by their type or subject, so you can ensure an event handler only receives relevant events.

### What is an event handler?

An event handler (sometimes referred to as an event "**subscriber**") is any component (application or resource) that can receive events from Event Grid. For example, Azure Functions can execute code in response to the new song being added to the Blob storage account. Subscribers can decide which events they want to handle and Event Grid will efficiently notify each interested subscriber when a new event is available - no polling required.

## Types of event sources

Events can be generated by the following Azure resource types:

- Azure Subscriptions and Resource Groups: Subscriptions and resource groups generate events related to management operations in Azure. For example, when a user creates a virtual machine, this source generates an event.
- Container registry: The Azure Container Registry service generates events when images in the registry are added, removed, or changed.
- Event Hub: Event Hub can be used to process and store events from a variety of data sources - typically logging or telemetry related. Event Hub can generate events to Event Grid when files are captured.
- Service Bus: Service bus can generate events to Event Grid when there are active messages with no active listeners.
- Storage accounts: Storage accounts can generate events when users add blobs, files, table entries, or queue messages. You can use both blob accounts and General-purpose V2 accounts as event sources.
- Media Services: Media Services hosts video and audio media and provides advanced management features for media files. Media Services can generate events when an encoding job is started or completed on a video file.
- Azure IoT Hub: IoT Hub communicates with and gathers telemetry from IoT devices. It can generate events whenever such communications arrive.
- Custom events: Custom events can be generated using the REST API, or with the Azure SDK on Java, GO, .NET, Node, Python, and Ruby. For example, you could create a custom event in the Web Apps feature of Azure App Service. This can happen in the worker role when it picks up a message from a storage queue.

This deep integration with diverse event sources within Azure ensures that Event Grid can distribute events that relate to almost any Azure resource.

## Event handlers

The following object types in Azure can receive and handle events from Event Grid:

- Azure Functions: Custom code that runs in Azure, without the need for explicit configuration of a host virtual server or container. Use an Azure function as an event handler when you want to code a custom response to the event.
- Webhooks: A webhook is a web API that implements a push architecture.
- Azure Logic Apps: An Azure logic app hosts a business process as a workflow.
- Microsoft Flow: Flow also hosts workflows, but it is easier for non-technical staff to use.

## Should you use Event Grid?

Use Event Grid when you need these features:

- Simplicity: It is straightforward to connect sources to subscribers in Event Grid.
- Advanced filtering: Subscriptions have close control over the events they receive from a topic.
- Fan-out: You can subscribe to an unlimited number of endpoints to the same events and topics.
- Reliability: Event Grid retries event delivery for up to 24 hours for each subscription.
- Pay-per-event: Pay only for the number of events that you transmit.

Event Grid is a simple but versatile event distribution system. Use it to deliver discrete events to subscribers, which will receive those events reliably and quickly.

We have one more messaging model to examine - what if we want to deliver a large stream of events? In this scenario, Event Grid isn't a great solution because it's designed for one-event-at-a-time delivery. Instead, we need to turn to another Azure service: Event Hubs.

# Choose Azure Event Hubs

There are certain applications that produce a massive number of events from almost as many sources. We often hear the term "Big Data" applied to these situations, and they require unique infrastructure to handle them.

Imagine you work for Contoso Aircraft Engines. The engines your employer manufactures have hundreds of sensors. Before an aircraft can be flown each morning, its engines are connected to a test harness and put through their paces. Additionally, cached in-flight data is streamed when the aircraft is connected to ground equipment.

You want to use historic sensor data to find patterns in the sensor readings that indicate engine failure is likely to happen soon. You want the real-time sensor readings to be compared against these failure patterns. You can then warn users in near real time if an engine is showing worrisome readings.

## What is Azure Event Hubs?

Event Hubs is an intermediary for the publish-subscribe communication pattern. Unlike Event Grid, however, **it is optimized for extremely high throughput, a large number of publishers, security, and resiliency**.

### What is an Event Hub?

[VIDEO: ~3:07](https://www.microsoft.com/en-us/videoplayer/embed/RE2yuat?pid=RE2yuat-ax-111-id-oneplayer&postJsllMsg=true&autoplay=false&mute=false&loop=false&market=en-us&playFullScreen=false)

Whereas Event Grid fits perfectly into the publish-subscribe pattern in that it simply manages subscriptions and routes communications to those subscribers, Event Hubs performs quite a few additional services. These additional services make it look more like a service bus or message queue, than a simple event broadcaster.

### Partitions

As Event Hubs receives communications, it divides them into partitions. Partitions are buffers into which the communications are saved. Because of the event buffers, events are not completely ephemeral, and an event isn't missed just because a subscriber is busy or even offline. The subscriber can always use the buffer to "catch up." By default, events stay in the buffer for 24 hours before they automatically expire.

The buffers are called partitions because the data is divided amongst them. **Every event hub has at least two partitions, and each partition has a separate set of subscribers.**

### Capture

Event Hubs can send all your events immediately to Azure Data Lake or Azure Blob storage for inexpensive, permanent persistence.

### Authentication

All publishers are authenticated and issued a token. This means Event Hubs can accept events from external devices and mobile apps, without worrying that fraudulent data from pranksters could ruin our analysis.

## Using Event Hubs

Event Hubs has support for pipelining event streams to other Azure services. Using it with Azure Stream Analytics, for instance, allows complex analysis of data in near real time, with the ability to correlate multiple events and look for patterns. In this case, Stream Analytics would be considered a subscriber.

For our aircraft engines, we'll set up our architecture so that Event Hubs will authenticate the communications from our engines. We will then have it use capture to save all the data to Data Lake. Later, we can use all that data to retrain and improve our machine learning models. Finally, our event streams will be picked up by Stream Analytics subscribers. Stream Analytics will use our machine learning model to look for patterns in the sensor data that might indicate problems.

**Because we have several partitions, and each engine sends all its data to only one partition, each instance of our Stream Analytics subscriber only needs to deal with a subset of our overall data. It does not have to filter and correlate over all of it.**

## Which service should I choose?

Just like our queue choice, selecting between these two event delivery services can seem tricky at first.

**Both support At Least Once semantics.**

Choose Event Hubs if:

- You need to support authenticating a large number of publishers.
- You need to save a stream of events to Data Lake or Blob storage.
- You need aggregation or analytics on your event stream.
- You need reliable messaging or resiliency.

**If you need a simple event publish-subscribe infrastructure, with trusted publishers (for instance, your own web server), you should choose Event Grid.**

Event Hubs lets you build a big data pipeline capable of processing millions of events per second with low latency. It can handle data from concurrent sources and route it to a variety of stream-processing infrastructures and analytics services. It enables real-time processing and supports repeated replay of stored raw data.

# Summary

In this module, you have explored four different Azure services that allow you to create reliable and resilient distributed applications. Choosing between them is a matter of deciding the type of data that needs to be passed between components (messages or events), and then what features you need to deliver and process the data.

Continue learning with the other Microsoft Learn modules that will show you how to use each of these messaging components in Azure!
