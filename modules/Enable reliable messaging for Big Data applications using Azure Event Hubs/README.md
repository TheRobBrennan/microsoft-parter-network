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

### Defining an Event Hubs namespace

### Azure CLI commands for creating an Event Hubs namespace

### Configuring a new Event Hub

### Azure CLI commands for creating an Event Hub

## Summary

# Exercise - Use the Azure CLI to Create an Event Hub

# Configure applications to send or receive messages through an Event Hub

# Exercise - Configure applications to send or receive messages through an Event Hub

# Evaluate the performance of the deployed Event Hub using the Azure portal

# Exercise - Evaluate the performance of the deployed Event Hub using the Azure portal

# Summary
