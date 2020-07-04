[Create an Azure Database for PostgreSQL server](https://docs.microsoft.com/en-us/learn/modules/create-azure-db-for-postgresql-server/) (~44 mins)

Azure Database for PostgreSQL provides fully managed, enterprise-ready community PostgreSQL database as a service. Learn how to create and deploy this service and connect it to your applications.

# Introduction to Azure Database for PostgreSQL

Suppose you work for a company that processes fitness tracker data for runners. Your company tracks the runners' routes and performance using a relational database hosted on an on-premises server.

The company wants to expand by tracking additional fitness tracking models and types. To do the expansion, the company will need to scale its database for both capacity and performance. You want to move away from your current on-premises database servers without compromising security.

When you consider cloud-hosted database offerings, some of the questions that you may have might be aspects like availability, scalability, and security. Azure Database for PostgreSQL allows you to take advantage of built-in security, fault tolerance, and data protection that you otherwise have to buy or design separately, build, and manage.

## Learning objectives

By the end of this module, you will:

- Create an Azure Database for PostgreSQL using the Azure portal
- Create an Azure Database for PostgreSQL using the Azure CLI
- Configure a PostgreSQL server-level firewall rule
- Connect to the PostgreSQL server using the `psql` command in Azure Cloud Shell

# The Azure Database for PostgreSQL offering

As a PostgreSQL user, you may already know the benefits of PostgreSQL. You plan to use **Azure Database for PostgreSQL** and you want to make sure you'll have access to the same benefits as your on-premises server before moving to the cloud.

Azure Database for PostgreSQL is a relational database service in the Microsoft cloud. The server software is based on the community version of the open-source PostgreSQL database engine. Your familiarity with tools and expertise with PostgreSQL is applicable when using Azure Database for PostgreSQL.

Moreover, Azure Database for PostgreSQL delivers the following benefits:

- Built-in high availability compared to on-premises resources. There is no additional configuration, replication, or cost required to make sure your applications are always available.
- Simple and flexible pricing. You have predictable performance based on a selected pricing tier choice that includes software patching, automatic backups, monitoring, and security.
- Scale up or down as needed within seconds. You can scale compute or storage independently as needed, to make sure you adapt your service to match usage.
- Adjustable automatic backups and point-in-time-restore for up to 35 days.
- Enterprise-grade security and compliance to protect sensitive data at-rest and in-motion that covers data encryption on disk and SSL encryption between client and server communication.

These benefits should help make your decision to go from on-premises PostgreSQL to Azure Database for PostgreSQL simple.

# Create an Azure Database for PostgreSQL server using the Azure portal

Let's assume that you're currently using an on-premises PostgreSQL relational database using flexible data types and geospatial support. Your company is looking at expanding, which requires the database to scale. As an alternative to investing in additional hardware, you're tasked with finding an optimal cloud-hosted database offering. You've decided to use an Azure Database for PostgreSQL server.

## What is an Azure Database for PostgreSQL server?

The PostgreSQL server is a central administration point for one or more databases. The PostgreSQL service in Azure is a managed resource that provides performance guarantees, and provides access and features at the server level.

An **Azure Database for PostgreSQL server **is the parent _resource_ for a database. A _resource_ is a manageable item that's available through Azure. Creating this resource allows you to configure your server instance.

### What is an Azure Database for PostgreSQL server resource?

An Azure Database for PostgreSQL server resource is a container with strong lifetime implications for your server and databases. If the server resource is deleted, all databases are also deleted. Keep in mind that all resources belonging to the parent are hosted in the same region.

The server resource name is used to define the server endpoint name. For example, if the resource name is **mypgsqlserver**, then the server name becomes **mypgsqlserver.postgres.database.azure.com**.

The server resource also provides the **connection scope** for management policies that apply to its database. For example: login, firewall, users, roles, and configuration.

Just like the open-source version of PostgreSQL, the server is available in several versions and allows for the installation of extensions. You'll choose which server version to install.

NOTE: Extensions allow for bundling multiple SQL objects together in a single package that can be loaded or removed with a single command. An example of an extension is `chkpass`, which provides a data type for auto-encrypted passwords.

## Pricing tiers

Azure Database for PostgreSQL provides you with the option to choose pricing tiers based on parameters like compute power and storage. For more details, see [Azure Database for PostgreSQL pricing tiers](https://docs.microsoft.com/en-us/azure/postgresql/concepts-pricing-tiers).

## Steps to create an Azure Database for PostgreSQL server

You'll typically create an Azure Database for PostgreSQL server using the Azure portal. Let's look at the steps you'd take. This is not an exercise, but serves to familiarize you with the steps if you choose to use the Azure portal.

First, you'll sign in to the Azure portal, and then you'll click **Create a resource**.

You'll select **Databases** and **Azure Database for PostgreSQL**. You can also use the **Search** functionality to find this category.

The portal will display a PostgreSQL server configuration screen, also called a pane, and you make your selection.

You'll need to give a value to all the items in the pane, so let's have a look at each.

### Server name

Earlier we mentioned that you'll create a server resource. The server name is the item that specifies this resource. As a result, you'll have to choose a unique name for the server. The server name must be all lowercase and can have numbers and hyphens.

Let's say you want to name the server Adventure Works Tracking. You would then set up the name as `adventure-works-tracking`. If you try to create a server with a name that already exists, you'll get an error to that effect.

### Subscription

The subscription field is used for billing. You'll pick a specific subscription in case you have more than one subscription available.

### Resource group

You'll use a resource group to manage all the resources related to your server. You can create a new resource group or reuse an existing resource group.

### Source

You can create a server either from scratch by choosing the _Blank_ default option or from an existing backup. The **Backup** option will give you the opportunity restore a geo-backup of an existing Azure Database for PostgreSQL server.

### Server admin login name

You create the server admin user. Choose a login name to use as an administrator login for the new server. The admin login name can't be azure*superuser, azure_pg_admin, admin, administrator, root, guest, or public. It can't start with pg*. Remember or write down this name for future use.

### Password

You choose a password to use with the above administrator login name. Your password must include characters from three of the following categories:

- English uppercase letters
- English lowercase letters
- Numbers (0 through 9)
- Non-alphanumeric characters (!, \$, #, %, and so on)

Remember or write down this password for future use.

### Confirm password

Retype the password to confirmation.

### Location

The location option allows you to specify where the server is created physically. Choose the geographic location closest to you. In a real-world scenario, the location should be the location closest to the majority of your users.

### Version

You can specify the version of PostgreSQL to use. Microsoft aims to support the current version and two previous versions of PostgreSQL. You'll choose a version that matches your production environment.

For more information, see the following sources:

- [Supported PostgreSQL database versions](https://docs.microsoft.com/en-us/azure/postgresql/concepts-supported-versions)
- [Versioning policy](https://www.postgresql.org/support/versioning/)

### Pricing tier

You'll choose a pricing tier that will support your server's workload. Recall that you have different tiers to choose from. As we saw earlier, each of these tiers allows you to configure the compute and storage options.

All that's left now is to review the values that you entered, make any notes you may need for later, and click **Create** to create the server.

Deploying the server takes a couple of minutes. You'll receive a notification when the deployment is complete. From the notification, you can navigate to the newly created server.

# Create an Azure Database for PostgreSQL server using the Azure CLI

# Exercise - Create an Azure Database for PostgreSQL server via the Azure CLI

# Server security considerations

# Exercise - Connect to an Azure Database for PostgreSQL server

# Summary
