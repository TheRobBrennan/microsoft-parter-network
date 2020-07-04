[Provision an Azure SQL database to store application data](https://docs.microsoft.com/en-us/learn/modules/provision-azure-sql-db/) (~50 mins)

Use the Azure Portal and Cloud Shell to provision, connect, and manage Azure SQL databases.

In this module, you will learn:

- Why Azure SQL Database is a good choice for running your relational database
- What configuration and pricing options are available for your Azure SQL database
- How to create an Azure SQL database from the portal
- How to use Azure Cloud Shell to connect to your Azure SQL database, add a table, and work with data

# Introduction

Managing data is a critical component of any business. Relational databases, and specifically Microsoft SQL Server, have been among the most common tools for handling that data for decades.

If we want to manage our data using the cloud, we can just use Azure virtual machines to host our own Microsoft SQL Server instances. Sometimes that's the right solution, but Azure offers another way that is often much easier and more cost effective. Azure SQL databases are a Platform-as-a-Service (PaaS) offering, meaning much less infrastructure and maintenance to manage yourself.

To understand better, let's consider a scenario: you're a software development lead at a transportation logistics company, Contoso Transport.

The transportation industry requires tight coordination among everyone involved: schedulers, dispatchers, drivers, and even customers.

Your current process involves piles of paper forms and hours on the phone to coordinate shipments. You find that paperwork is often missing signatures and dispatchers are frequently unavailable. These holdups leave drivers sitting idle; and as a result, some important shipments arrive late.

Customer satisfaction and repeat business are crucial to your bottom line, so your team decides to move from paper forms and phone calls to digital documents and online communication. Going digital will enable everyone to coordinate and track shipment times through their web browser or mobile app.

You want to quickly prototype something to share with your team. Your prototype will include a database to hold driver, customer, and order information. Your prototype will be the basis for your production app, so the technology choices you make now should carry over to what your team delivers.

## Learning objectives

In this module, you'll learn:

- Why Azure SQL Database is a good choice for running your relational database
- Which configuration and pricing options are available for your Azure SQL database
- How to create an Azure SQL database from the portal
- How to use Azure Cloud Shell to connect to your Azure SQL database, add a table, and work with data

# Plan your Azure SQL database

Azure provides Platform as a Service (PaaS) services to help you manage all kinds of data, from highly structured relational data to unstructured data.

Here you'll learn why Azure SQL Database is a convenient, cost-effective, and secure way to host your relational databases.

## Why choose Azure SQL Database?

Your transportation logistics application requires stored procedures that run basic CRUD (Create, Read, Update, and Delete) operations. You have experience working with SQL Server and other relational databases.

You consider two choices for your database:

- Host SQL Server on-premises. Your IT team runs a small in-house data center to support the finance department and a few other teams. You can work with IT to host a SQL Server deployment in their data center.
- Host Azure SQL Database in the cloud. Azure SQL Database is based on SQL Server and provides the relational database functionality you need.

You've decided to build the web and application tiers for your logistics app on Azure. So it makes sense to also host your database there. But there are some other reasons why Azure SQL Database is a smart choice, and why it's even easier than using virtual machines.

### Convenience

Setting up SQL Server on a VM or on physical hardware requires you to know about hardware and software requirements. You'll need to understand the latest security best practices and manage operating system and SQL Server patches on a routine basis. You also need to manage backup and data retention issues yourself.

With Azure SQL Database, we manage the hardware, software updates, and OS patches for you. All you specify is the name of your database and a few options. You'll have a running SQL database in minutes.

You can bring up and tear down Azure SQL Database instances at your convenience. Azure SQL Database comes up fast and is easy to configure. You can focus less on configuring software and more on making your app great.

### Cost

Because we manage things for you, there are no systems for you to buy, provide power for, or otherwise maintain.

Azure SQL Database has several pricing options. These pricing options enable you to balance performance versus cost. You can start for just a few dollars a month.

### Scale

You find that the amount of transportation logistics data you must store doubles every year. When running on-premises, how much excess capacity should you plan for?

With Azure SQL Database, you can adjust the performance and size of your database on the fly when your needs change.

### Security

Azure SQL Database comes with a firewall that's automatically configured to restrict connections from the Internet.

You can allow access to specific IP addresses that you trust. Doing so allows you to use Visual Studio, SQL Server Management Studio, or other tools to manage your Azure SQL database.

## To recap

To recap - with Azure SQL Database, Microsoft Azure manages the hardware, software updates, and OS patches for you. We provide buying options to help you get the performance you need at a predictable cost. Azure SQL Database also comes with a firewall so that you can control access to your data.

Although you don't need to be a DBA to use Azure SQL Database, there are a few concepts you should understand before you start. We'll cover these concepts in the next unit.

# Exercise - Create your Azure SQL database

Your transportation company wants to set itself apart from other companies, without breaking the bank. You must have a good handle on how to set up the database to provide the best service while controlling costs.

Here, you'll learn:

- What considerations you need to make when creating an Azure SQL database, including:
  - How a logical server acts as an administrative container for your databases.
  - The differences between purchasing models.
  - How elastic pools enable you to share processing power among databases.
  - How collation rules affect how data is compared and sorted.
- How to bring up Azure SQL Database from the portal.
- How to add firewall rules so that your database is accessible from only trusted sources.

Let's take a quick look at some things you need to consider when you create an Azure SQL database.

## One server, many databases

## Choose performance: DTUs versus vCores

## What are SQL elastic pools?

## What is collation?

## Create your Azure SQL database

## Set the server firewall

# Exercise - Connect to your database and add sample data

# Summary and cleanup
