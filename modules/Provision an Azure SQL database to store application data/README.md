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

When you create your first Azure SQL database, you also create an _Azure SQL logical server_. Think of a logical server as an administrative container for your databases.

You can control logins, firewall rules, and security policies through the logical server. You can also override these policies on each database within the logical server.

For now, you need just one database. But a logical server enables you to add more later and tune performance among all your databases.

## Choose performance: DTUs versus vCores

Azure SQL Database has two purchasing models: DTU and vCore.

What are DTUs?

DTU stands for _Database Transaction Unit_, and is a combined measure of compute, storage, and IO resources. Think of the DTU model as a simple, preconfigured purchase option.

Because your logical server can hold more than one database, there's also the idea of eDTUs, or elastic Database Transaction Units. This option enables you to choose one price, but allow each database in the pool to consume fewer or greater resources depending on current load.

What are vCores?

vCores are _Virtual cores_, which give you greater control over the compute and storage resources that you create and pay for.

While the DTU model provides fixed combinations of compute, storage, and IO resources, the vCore model enables you to configure resources independently.

For example, with the vCore model you can increase storage capacity but keep the existing amount of compute and IO throughput.

Your transportation and logistics prototype only needs one Azure SQL Database instance. You decide on the DTU option because it provides a good balance of compute, storage, and IO performance and is less expensive to get started.

## What are SQL elastic pools?

When you create your Azure SQL database, you can create a _SQL elastic pool_.

SQL elastic pools relate to eDTUs. They enable you to buy a set of compute and storage resources that are shared among all the databases in the pool. Each database can use the resources they need, within the limits you set, depending on current load.

For your prototype, you won't need a SQL elastic pool because you need only one SQL database.

## What is collation?

Collation refers to the rules that sort and compare data. Collation helps you define sorting rules when case sensitivity, accent marks, and other language characteristics are important.

Let's take a moment to consider what the default collation, **SQL_Latin1_General_CP1_CI_AS**, means.

- **Latin1_General** refers to the family of Western European languages.
- **CP1** refers to [code page 1252](https://en.wikipedia.org/wiki/Windows-1252), a popular character encoding of the Latin alphabet.
- **CI** means that comparisons are case insensitive. For example, "HELLO" compares equally to "hello".
- **AS** means that comparisons are accent sensitive. For example, "résumé" doesn't compare equally to "resume".

Because you don't have specific requirements around how data is sorted and compared, you choose the default collation.

## Create your Azure SQL database

Here you'll set up your database, which includes creating your logical server. You'll choose settings that support your transportation logistics application. In practice, you would choose settings that support the kind of app you're building.

Over time if you realize you need additional compute power to keep up with demand, you can adjust performance options or even switch between the DTU and vCore performance models.

From the Azure portal menu or the **Home** page, select **Create a resource**. Select **Databases**, then select **SQL Database**.

Use these values to fill out the rest of the form:

| Setting                       | Value                                      |
| ----------------------------- | ------------------------------------------ |
| Subscription                  | Concierge Subscription                     |
| Resource group                | learn-0dfeebf6-6c92-46bb-b140-848f1e6d39f3 |
| Database name                 | Logistics                                  |
| Server                        | [See below]                                |
| Want to use SQL elastic pool? | No                                         |
| Compute + storage             | [See below]                                |

Under **Server**, click **Create new**, fill out the form, then click **OK**. Here's more information on how to fill out the form:

| Setting                               | Value                                                                                                                                                                                               |
| ------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Server name                           | A globally unique [server name](https://docs.microsoft.com/en-us/azure/architecture/best-practices/naming-conventions).                                                                             |
| Server admin login                    | A [database identifier](https://docs.microsoft.com/en-us/sql/relational-databases/databases/database-identifiers) that serves as your primary administrator login name.                             |
| Password                              | Any valid password that has at least eight characters and contains characters from three of these categories: uppercase characters, lowercase characters, numbers, and non-alphanumeric characters. |
| Location                              | Any valid location from the available list below.                                                                                                                                                   |
| Allow Azure services to access server | This checkbox should be checked.                                                                                                                                                                    |

Under **Compute + storage**, click **configure database**, then use the following steps:

- To configure your database to use DTUs, click **Looking for basic, standard, premium?**
- Depending on your application needs, choose **Basic**, **Standard**, or **Premium**.
- Click **Apply**.

Click **Additional settings**, then use these values to fill out the form.

| Setting                | Value                        |
| ---------------------- | ---------------------------- |
| Data source            | None                         |
| Database Collation     | SQL_Latin1_General_CP1_CI_AS |
| Advanced Data Security | Not now                      |

IMPORTANT: Remember your server name, admin login, and password for later.

```sh
# Credentials (temporary and random)
Server name - `rbsqldb837`
Server admin login - `rbadmin`
Server admin password - `ViVKUwPtAdW2`
```

Click **Review + Create** and then **Create** to create your Azure SQL database.

On the toolbar, click **Notifications** to monitor the deployment process.

When the process completes, click **Pin to dashboard** to pin your database server to the dashboard so that you have quick access when you need it later.

![https://docs.microsoft.com/en-us/learn/modules/provision-azure-sql-db/media/3-notifications-complete.png](https://docs.microsoft.com/en-us/learn/modules/provision-azure-sql-db/media/3-notifications-complete.png)

## Set the server firewall

Your Azure SQL database is now up and running. You have many options to further configure, secure, monitor, and troubleshoot your new database. You can also specify which systems can access your database through the firewall. Initially, the firewall prevents all access to your database server from outside of Azure.

For your prototype, you only need to access the database from your laptop. Later, you can add additional systems, such as your mobile app.

For now, let's enable your development computer to access the database through the firewall.

Go to the overview pane of the Logistics database. If you pinned the database earlier, you can click the **Logistics** tile on the dashboard to get there.

Click **Set server firewall**.

![https://docs.microsoft.com/en-us/learn/modules/provision-azure-sql-db/media/3-set-server-firewall.png](https://docs.microsoft.com/en-us/learn/modules/provision-azure-sql-db/media/3-set-server-firewall.png)

Click **Add client IP**, this will automatically add the IP address for your development computer.

![https://docs.microsoft.com/en-us/learn/modules/provision-azure-sql-db/media/3-add-client-ip.png](https://docs.microsoft.com/en-us/learn/modules/provision-azure-sql-db/media/3-add-client-ip.png)

Click **Save**.

In the next unit, you'll get some hands-on practice with your new database and with Azure Cloud Shell. You'll connect to the database, create a table, add some sample data, and execute a few SQL statements.

# Exercise - Connect to your database and add sample data

Before you connect the database to your app, you want to verify that you can connect to it, add a basic table, and work with sample data.

We maintain the infrastructure, software updates, and patches for your Azure SQL database. Beyond that, you can treat your Azure SQL database like you would any other SQL Server installation. For example, **you can use Visual Studio, SQL Server Management Studio, Azure Data Studio, or other tools to manage your Azure SQL database**.

How you access your database and connect it to your app is up to you. But to get some experience working with your database, here you'll connect to it directly from the portal, create a table, and run a few basic CRUD operations. You'll learn:

- What Cloud Shell is and how to access it from the portal.
- How to access information about your database from the Azure CLI, including connection strings.
- How to connect to your database using `sqlcmd`.
- How to initialize your database with a basic table and some sample data.

## What is Azure Cloud Shell?

Azure Cloud Shell is a browser-based shell experience to manage and develop Azure resources. Think of Cloud Shell as an interactive console that runs in the cloud.

Behind the scenes, Cloud Shell runs on Linux. But depending on whether you prefer a Linux or Windows environment, you have two experiences to choose from: Bash and PowerShell.

Cloud Shell is accessible from anywhere. Besides the portal, you can also access Cloud Shell from [shell.azure.com](https://shell.azure.com/), the Azure mobile app, or from Visual Studio Code. The panel on the right is a Cloud Shell terminal for you to use during this exercise.

Cloud Shell includes popular tools and text editors. Here's a brief look at the `az`, `jq`, and `sqlcmd` utilities, which are three tools that you'll use for this exercise.

- `az` is also known as the Azure CLI. It's the command-line interface for working with Azure resources. You'll use this to get information about your database, including the connection string.
- `jq` is a command-line JSON parser. You'll pipe output from `az` commands to this tool to extract important fields from JSON output.
- `sqlcmd` enables you to execute statements on SQL Server. You'll use `sqlcmd` to create an interactive session with your Azure SQL database.

TIP: When running the T-SQL commands in this module using `sqlcmd`, the `GO` on the second line may not copy through to the `sqlcmd` prompt, so you will likely need to type this out. The T-SQL command won't execute without it, so make sure to run the `GO` command.

## Get information about your Azure SQL database

Before you connect to your database, it's a good idea to verify that it exists and is online.

Here, you use the `az` utility to list your databases and show some information about the **Logistics** database, including its maximum size and status.

The `az` commands you'll run require the name of your resource group and the name of your Azure SQL logical server. To save typing, run this `azure configure` command to specify them as default values.

REMEMBER: We defined our credentials earlier

```sh
# Credentials (temporary and random)
Server name - `rbsqldb837`
Server admin login - `rbadmin`
Server admin password - `ViVKUwPtAdW2`
```

Replace [server-name] with the name of your Azure SQL logical server.

```sh
az configure --defaults group=learn-0dfeebf6-6c92-46bb-b140-848f1e6d39f3 sql-server=rbsqldb837
```

Depending on the pane you are on in the portal, your SQL server name may be displayed as a FQDN (e.g. servername.database.windows.net). However, for this command, you only need the logical name without the .database.windows.net suffix.

Run `az sql db list` to list all databases on your Azure SQL logical server.

```sh
az sql db list
```

You see a large block of JSON as output:

```json
[
  {
    "autoPauseDelay": null,
    "catalogCollation": "SQL_Latin1_General_CP1_CI_AS",
    "collation": "SQL_Latin1_General_CP1_CI_AS",
    "createMode": null,
    "creationDate": "2020-07-04T03:46:41.620000+00:00",
    "currentServiceObjectiveName": "S0",
    "currentSku": {
      "capacity": 10,
      "family": null,
      "name": "Standard",
      "size": null,
      "tier": "Standard"
    },
    "databaseId": "fe4c6c66-adc9-47f8-81b8-01b5798fcb32",
    "defaultSecondaryLocation": "westcentralus",
    "earliestRestoreDate": "2020-07-04T03:55:49+00:00",
    "edition": "Standard",
    "elasticPoolId": null,
    "elasticPoolName": null,
    "failoverGroupId": null,
    "id": "/subscriptions/b6c97cdb-3ae5-40a9-ac7d-efec7b85dc3b/resourceGroups/learn-0dfeebf6-6c92-46bb-b140-848f1e6d39f3/providers/Microsoft.Sql/servers/rbsqldb837/databases/Logistics",
    "kind": "v12.0,user",
    "licenseType": null,
    "location": "westus2",
    "longTermRetentionBackupResourceId": null,
    "managedBy": null,
    "maxLogSizeBytes": null,
    "maxSizeBytes": 268435456000,
    "minCapacity": null,
    "name": "Logistics",
    "pausedDate": null,
    "readReplicaCount": 0,
    "readScale": "Disabled",
    "recoverableDatabaseId": null,
    "recoveryServicesRecoveryPointId": null,
    "requestedServiceObjectiveName": "S0",
    "resourceGroup": "learn-0dfeebf6-6c92-46bb-b140-848f1e6d39f3",
    "restorableDroppedDatabaseId": null,
    "restorePointInTime": null,
    "resumedDate": null,
    "sampleName": null,
    "sku": {
      "capacity": 10,
      "family": null,
      "name": "Standard",
      "size": null,
      "tier": "Standard"
    },
    "sourceDatabaseDeletionDate": null,
    "sourceDatabaseId": null,
    "status": "Online",
    "tags": {},
    "type": "Microsoft.Sql/servers/databases",
    "zoneRedundant": false
  },
  {
    "autoPauseDelay": null,
    "catalogCollation": "SQL_Latin1_General_CP1_CI_AS",
    "collation": "SQL_Latin1_General_CP1_CI_AS",
    "createMode": null,
    "creationDate": "2020-07-04T03:45:46.723000+00:00",
    "currentServiceObjectiveName": "System2",
    "currentSku": {
      "capacity": 0,
      "family": null,
      "name": "System",
      "size": null,
      "tier": "System"
    },
    "databaseId": "12beecf8-e32c-4b79-b31e-625eddfcc2ce",
    "defaultSecondaryLocation": "westcentralus",
    "earliestRestoreDate": null,
    "edition": "System",
    "elasticPoolId": null,
    "elasticPoolName": null,
    "failoverGroupId": null,
    "id": "/subscriptions/b6c97cdb-3ae5-40a9-ac7d-efec7b85dc3b/resourceGroups/learn-0dfeebf6-6c92-46bb-b140-848f1e6d39f3/providers/Microsoft.Sql/servers/rbsqldb837/databases/master",
    "kind": "v12.0,system",
    "licenseType": null,
    "location": "westus2",
    "longTermRetentionBackupResourceId": null,
    "managedBy": "/subscriptions/b6c97cdb-3ae5-40a9-ac7d-efec7b85dc3b/resourceGroups/learn-0dfeebf6-6c92-46bb-b140-848f1e6d39f3/providers/Microsoft.Sql/servers/rbsqldb837",
    "maxLogSizeBytes": null,
    "maxSizeBytes": 32212254720,
    "minCapacity": null,
    "name": "master",
    "pausedDate": null,
    "readReplicaCount": 0,
    "readScale": "Disabled",
    "recoverableDatabaseId": null,
    "recoveryServicesRecoveryPointId": null,
    "requestedServiceObjectiveName": "System2",
    "resourceGroup": "learn-0dfeebf6-6c92-46bb-b140-848f1e6d39f3",
    "restorableDroppedDatabaseId": null,
    "restorePointInTime": null,
    "resumedDate": null,
    "sampleName": null,
    "sku": {
      "capacity": 0,
      "family": null,
      "name": "System",
      "size": null,
      "tier": "System"
    },
    "sourceDatabaseDeletionDate": null,
    "sourceDatabaseId": null,
    "status": "Online",
    "tags": null,
    "type": "Microsoft.Sql/servers/databases",
    "zoneRedundant": false
  }
]
```

Since we want to see only the database names, run the command a second time. But this time, pipe the output to `jq` to print out only the name fields.

```sh
az sql db list | jq '[.[] | {name: .name}]'
```

You should see this output:

```json
[
  {
    "name": "Logistics"
  },
  {
    "name": "master"
  }
]
```

**Logistics** is your database. Like SQL Server, **master** includes server metadata, such as sign-in accounts and system configuration settings.

Run this `az sql db show` command to get details about the _Logistics_ database.

```sh
az sql db show --name Logistics
```

As before, you see a large block of JSON as output:

```json
{
  "autoPauseDelay": null,
  "catalogCollation": "SQL_Latin1_General_CP1_CI_AS",
  "collation": "SQL_Latin1_General_CP1_CI_AS",
  "createMode": null,
  "creationDate": "2020-07-04T03:46:41.620000+00:00",
  "currentServiceObjectiveName": "S0",
  "currentSku": {
    "capacity": 10,
    "family": null,
    "name": "Standard",
    "size": null,
    "tier": "Standard"
  },
  "databaseId": "fe4c6c66-adc9-47f8-81b8-01b5798fcb32",
  "defaultSecondaryLocation": "westcentralus",
  "earliestRestoreDate": "2020-07-04T03:55:49+00:00",
  "edition": "Standard",
  "elasticPoolId": null,
  "elasticPoolName": null,
  "failoverGroupId": null,
  "id": "/subscriptions/b6c97cdb-3ae5-40a9-ac7d-efec7b85dc3b/resourceGroups/learn-0dfeebf6-6c92-46bb-b140-848f1e6d39f3/providers/Microsoft.Sql/servers/rbsqldb837/databases/Logistics",
  "kind": "v12.0,user",
  "licenseType": null,
  "location": "westus2",
  "longTermRetentionBackupResourceId": null,
  "managedBy": null,
  "maxLogSizeBytes": null,
  "maxSizeBytes": 268435456000,
  "minCapacity": null,
  "name": "Logistics",
  "pausedDate": null,
  "readReplicaCount": 0,
  "readScale": "Disabled",
  "recoverableDatabaseId": null,
  "recoveryServicesRecoveryPointId": null,
  "requestedServiceObjectiveName": "S0",
  "resourceGroup": "learn-0dfeebf6-6c92-46bb-b140-848f1e6d39f3",
  "restorableDroppedDatabaseId": null,
  "restorePointInTime": null,
  "resumedDate": null,
  "sampleName": null,
  "sku": {
    "capacity": 10,
    "family": null,
    "name": "Standard",
    "size": null,
    "tier": "Standard"
  },
  "sourceDatabaseDeletionDate": null,
  "sourceDatabaseId": null,
  "status": "Online",
  "tags": {},
  "type": "Microsoft.Sql/servers/databases",
  "zoneRedundant": false
}
```

Run the command a second time. This time, pipe the output to `jq` to limit output to only the name, maximum size, and status of the Logistics database:

```sh
az sql db show --name Logistics | jq '{name: .name, maxSizeBytes: .maxSizeBytes, status: .status}'
```

You see that the database is online and can hold around 2 GB of data:

```sh
{
  "name": "Logistics",
  "maxSizeBytes": 268435456000,
  "status": "Online"
}
```

## Connect to your database

Now that you understand a bit about your database, let's connect to it using `sqlcmd`, create a table that holds information about transportation drivers, and perform a few basic CRUD operations.

Remember that CRUD stands for Create, Read, Update, and Delete. These terms refer to operations you perform on table data and are the four basic operations you need for your app. Now's a good time to verify you can perform each of them.

Run this `az sql db show-connection-string` command to get the connection string to the **Logistics** database in a format that `sqlcmd` can use:

```sh
az sql db show-connection-string --client sqlcmd --name Logistics
```

Your output resembles this:

```sh
"sqlcmd -S tcp:rbsqldb837.database.windows.net,1433 -d Logistics -U <username> -P <password> -N -l 30"
```

REMEMBER: We defined our credentials earlier

```sh
# Credentials (temporary and random)
Server name - `rbsqldb837`
Server admin login - `rbadmin`
Server admin password - `ViVKUwPtAdW2`
```

Run the `sqlcmd` statement from the output of the previous step to create an interactive session. Remove the surrounding quotes and replace `<username>` and `<password>` with the username and password you specified when you created your database. Here's an example.

```sh
sqlcmd -S tcp:rbsqldb837.database.windows.net,1433 -d Logistics -U rbadmin -P "ViVKUwPtAdW2" -N -l 30
```

TIP: Place your password in quotes so that "&" and other special characters aren't interpreted as processing instructions.

IMPORTANT: You may see an error message that is similar to the following example:

```sh
Sqlcmd: Error: Microsoft ODBC Driver 17 for SQL Server : Cannot open server 'rbsqldb837' requested by the login. Client with IP address '168.61.18.156' is not allowed to access the server.  To enable access, use the Windows Azure Management Portal or run sp_set_firewall_rule on the master database to create a firewall rule for this IP address or address range.  It may take up to five minutes for this change to take effect..
r
```

If this happens, you will need to add another firewall rule for your client. To do so, use the following steps:

- Sign into the Azure portal using the same account you activated the sandbox with.
- Search for and select your database.
- Click **Set server firewall**.
- Specify a unique Rule name, then enter your IP address from the error message for both the Start IP and End IP fields.
- Click Save.

From your `sqlcmd` session, run the following T-SQL statements to create a table named `Drivers`.

```sql
CREATE TABLE Drivers (DriverID int, LastName varchar(255), FirstName varchar(255), OriginCity varchar(255));
GO
```

The table contains four columns: a unique identifier, the driver's last and first name, and the driver's city of origin.

Run the following T-SQL statements to verify that the Drivers table exists.

```sh
SELECT name FROM sys.tables;
GO
```

You should see this output.

```sh
name
--------------------------------------------------------------------------------------------------------------------------------
Drivers

(1 rows affected)
```

Run the following T-SQL statements to add a sample row to the table. This is the **create** operation.

```sql
INSERT INTO Drivers (DriverID, LastName, FirstName, OriginCity) VALUES (123, 'Zirne', 'Laura', 'Springfield');
GO
```

You see this to indicate the operation succeeded.

```sh
(1 rows affected)
```

Run the following T-SQL statements to list the `DriverID` and `OriginCity` columns from all rows in the table. This is the **read** operation.

```sh
SELECT DriverID, OriginCity FROM Drivers;
GO
```

You see one result with the `DriverID` and `OriginCity` for the row you created in the previous step.

```sh
DriverID    OriginCity
----------- --------------------------
        123 Springfield

(1 rows affected)
```

Run the following T-SQL statements to change the city of origin from "Springfield" to "Boston" for the driver with a `DriverID` of 123. This is the **update** operation.

```sql
UPDATE Drivers SET OriginCity='Boston' WHERE DriverID=123;
GO
```

Run the following T-SQL statements to list the `DriverID` and `OriginCity` columns again.

```sql
SELECT DriverID, OriginCity FROM Drivers;
GO
```

You should now see the following output. Notice how the `OriginCity` reflects the update to Boston.

```sh
DriverID    OriginCity
----------- --------------------------
        123 Boston

(1 rows affected)
```

Run the following T-SQL statements to delete the record. This is the **delete** operation.

```sql
DELETE FROM Drivers WHERE DriverID=123;
GO
```

```sh
(1 rows affected)
```

Run the following T-SQL statements to verify the `Drivers` table is empty.

```sh
SELECT COUNT(*) FROM Drivers;
GO
```

You see that the table contains no rows.

```sh
-----------
          0

(1 rows affected)
```

Now that you have a general idea for working with Azure SQL Database from Cloud Shell, you can get the connection string for your favorite SQL management tool – whether that's from SQL Server Management Studio, Visual Studio, or something else.

Cloud Shell makes it easy to access and work with your Azure resources. Because Cloud Shell is browser-based, you can access it from Windows, macOS, or Linux – essentially any system with a web browser.

You gained some hands-on experience running Azure CLI commands to get information about your Azure SQL database. As a bonus, you practiced your T-SQL skills.

In the next unit, we'll wrap up this module and discuss how to tear down your database.

# Summary and cleanup
