[Secure your Azure SQL Database](https://docs.microsoft.com/en-us/learn/modules/secure-your-azure-sql-database/) (~1 hr 7 mins)

# Introduction

Data security is critical when protecting your customer's privacy and your organization’s reputation. Businesses have closed due to financial damage or ruined reputations. Customers have had their data accessed unlawfully because of security breaches exposing their personal details, and again, possibly causing financial harm.

Suppose you work at an online retailer. To support the online marketplace your company provides, you store an assortment of data from your customers, such as phone numbers, addresses, and credit cards. You also store data that is critical to the health of your business, such as financial account balances and intellectual property. If exposed or maliciously accessed it could jeopardize the health of your business and the trust your customers place in you. It's your responsibility to make sure this data stored in your databases is as secure as possible, to protect both your customer data and your business data.

Put yourself in the shoes of an attacker. If you were trying to maliciously attack a system, would a single layer of protection or multiple layers of protection make it more difficult to gain access to the data? Defense in depth is a strategy that employs a layered approach to slow the advance of an attack aimed at acquiring unauthorized access to information. Each layer provides protection so that if one layer is breached, a subsequent layer is already in place to prevent further exposure.

Azure SQL Database is a great service for the storage of relational data, and there are a number of built-in capabilities you can use to ensure your data is secure and practice defense in depth. We'll look at ways to secure your SQL database by configuring the database firewall, securing access, encrypting communication, and other techniques for database security. With this layered approach, you can help ensure your data is secure.

## Learning objectives

In this module, you will:

- Control network access to your Azure SQL Database using firewall rules
- Control user access to your Azure SQL Database using authentication and authorization
- Protect your data in transit and at rest
- Audit and monitor your Azure SQL Database for access violations

# Exercise - Set up sandbox environment

Here you'll set up the resources you'll need for use throughout this module. Let's envision a basic architecture consisting of a server hosting an application that your customers use, which connects to a database for the storage of its data. The application runs on a virtual machine, and the database has been recently migrated from a SQL Server database running on a VM to a database on the Azure SQL Database service. To show how you can secure your database, we're going to set up the following resources for use throughout this module:

- A Linux VM named _appServer_. This server will act as the application server that users would be connecting to, and will need to connect to the database. We'll install `sqlcmd` on the VM to simulate an application running on _appServer_ making connections to the database.
- An Azure SQL Database logical server. This logical server is needed to host one or more databases.
- A database on our logical server called _marketplaceDb_. We'll create it using the _AdventureWorksLT_ demo database so we have some tables and data to work with. This will include some sensitive data, such as email addresses and phone numbers that we'll want to make sure are properly secured.

Let's get things set up!

## Create an Azure SQL Database

First, let's set up some variables. Replace values below that are shown in `[]` with values of your choice. Note that the `[password]` must have at least eight characters and contain characters from at least three of these categories: uppercase characters, lowercase characters, numbers, and non-alphanumeric characters. Save the login for use later.

```sh
# Set an admin login and password for your database
export ADMINLOGIN='rbadmin'
export PASSWORD='ViVKUwPtAdW2'
# Set the logical SQL server name. We'll add a random string as it needs to be globally unique.
export SERVERNAME=server$RANDOM
export RESOURCEGROUP=learn-de1b0057-e21d-46a6-a43e-44b6e36fc45e
# Set the location, we'll pull the location from our resource group.
export LOCATION=$(az group show --name $RESOURCEGROUP | jq -r '.location')
```

Run the following command to create a new Azure SQL Database logical server.

```sh
az sql server create \
    --name $SERVERNAME \
    --resource-group $RESOURCEGROUP \
    --location $LOCATION \
    --admin-user $ADMINLOGIN \
    --admin-password "$PASSWORD"
```

This will generate output:

```json
{
  "administratorLogin": "rbadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "server18714.database.windows.net",
  "id": "/subscriptions/179dd20b-e2ea-45a0-8003-473940f3d4f1/resourceGroups/learn-de1b0057-e21d-46a6-a43e-44b6e36fc45e/providers/Microsoft.Sql/servers/server18714",
  "identity": null,
  "kind": "v12.0",
  "location": "westus",
  "minimalTlsVersion": null,
  "name": "server18714",
  "privateEndpointConnections": [],
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "learn-de1b0057-e21d-46a6-a43e-44b6e36fc45e",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

Now run the following to create the database called **marketplaceDb** on the logical server you just created. This will use the _AdventureWorksLT_ database as a template so we'll have some pre-populated tables to work with.

```sh
az sql db create --resource-group $RESOURCEGROUP \
    --server $SERVERNAME \
    --name marketplaceDb \
    --sample-name AdventureWorksLT \
    --service-objective Basic
```

This will generate output like:

```json
{
  "autoPauseDelay": null,
  "catalogCollation": "SQL_Latin1_General_CP1_CI_AS",
  "collation": "SQL_Latin1_General_CP1_CI_AS",
  "createMode": null,
  "creationDate": "2020-07-05T21:33:17.347000+00:00",
  "currentServiceObjectiveName": "Basic",
  "currentSku": {
    "capacity": 5,
    "family": null,
    "name": "Basic",
    "size": null,
    "tier": "Basic"
  },
  "databaseId": "fa80c550-d707-4d8d-abed-9183872f88b3",
  "defaultSecondaryLocation": "eastus",
  "earliestRestoreDate": "2020-07-05T22:03:17.347000+00:00",
  "edition": "Basic",
  "elasticPoolId": null,
  "elasticPoolName": null,
  "failoverGroupId": null,
  "id": "/subscriptions/179dd20b-e2ea-45a0-8003-473940f3d4f1/resourceGroups/learn-de1b0057-e21d-46a6-a43e-44b6e36fc45e/providers/Microsoft.Sql/servers/server18714/databases/marketplaceDb",
  "kind": "v12.0,user",
  "licenseType": null,
  "location": "westus",
  "longTermRetentionBackupResourceId": null,
  "managedBy": null,
  "maxLogSizeBytes": null,
  "maxSizeBytes": 2147483648,
  "minCapacity": null,
  "name": "marketplaceDb",
  "pausedDate": null,
  "readReplicaCount": 0,
  "readScale": "Disabled",
  "recoverableDatabaseId": null,
  "recoveryServicesRecoveryPointId": null,
  "requestedServiceObjectiveName": "Basic",
  "resourceGroup": "learn-de1b0057-e21d-46a6-a43e-44b6e36fc45e",
  "restorableDroppedDatabaseId": null,
  "restorePointInTime": null,
  "resumedDate": null,
  "sampleName": null,
  "sku": {
    "capacity": 5,
    "family": null,
    "name": "Basic",
    "size": null,
    "tier": "Basic"
  },
  "sourceDatabaseDeletionDate": null,
  "sourceDatabaseId": null,
  "status": "Online",
  "tags": null,
  "type": "Microsoft.Sql/servers/databases",
  "zoneRedundant": false
}
```

Let's do one last thing and get the connection string for this database.

```sh
az sql db show-connection-string --client sqlcmd --name marketplaceDb --server $SERVERNAME | jq -r
```

Your output resembles this. Keep this handy, you'll need this command to connect to your database later in this module. Note the `[username]` and `[password]` placeholders in the command that you will want to replace with the `ADMINLOGIN` and `PASSWORD` credentials you specified in variables earlier.

```sh
sqlcmd -S tcp:server18714.database.windows.net,1433 -d marketplaceDb -U <username> -P <password> -N -l 30

# Example command for my Azure sandbox to obtain the connection string will be
sqlcmd -S tcp:server18714.database.windows.net,1433 -d marketplaceDb -U rbadmin -P ViVKUwPtAdW2 -N -l 30
```

## Create and configure a Linux virtual machine

Now let's create the Linux VM that we'll use through some examples.

Run the following command to create the VM; this might take several minutes to complete.

```sh
az vm create \
  --resource-group $RESOURCEGROUP \
  --name appServer \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --generate-ssh-keys
```

When this command completes, you should see output that resembles the following example:

```json
{
  "fqdns": "",
  "id": "/subscriptions/179dd20b-e2ea-45a0-8003-473940f3d4f1/resourceGroups/learn-de1b0057-e21d-46a6-a43e-44b6e36fc45e/providers/Microsoft.Compute/virtualMachines/appServer",
  "location": "westus",
  "macAddress": "00-22-48-04-01-B6",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.112.170.37",
  "resourceGroup": "learn-de1b0057-e21d-46a6-a43e-44b6e36fc45e",
  "zones": ""
}
```

Once your VM is successfully created, connect to its public IP address using SSH.

```sh
ssh nnn.nnn.nnn.nnn

# Example where nnn.nnn.nnn.nnn is the value from the publicIpAddress output in the previous step.
ssh 40.112.170.37
```

Two things to note. First, we don't need a password because we generated an SSH key pair as part of the VM creation. Second, on the initial shell connection into the VM it will give you a prompt about the authenticity of the host. This occurs because we are connecting to an IP address instead of a host name. Answering "yes" will save the IP as a valid host for connection and allow the connection to proceed.

Now let's finish things up by installing mssql-tools on the Linux VM so we'll be able to connect to our database through sqlcmd.

```sh
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
source ~/.bashrc
curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list | sudo tee /etc/apt/sources.list.d/msprod.list
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install -y mssql-tools unixodbc-dev
```

A lot of text will scroll by for some of these commands, so make sure you hit enter after the final command to ensure it runs.

We've created an Azure SQL Database logical server, a database on that logical server, and a virtual machine called _appServer_ that we'll use to simulate network connectivity from an application server. Let's take a look at how we can properly secure our database.

# Exercise - Restrict network access

Users will connect to our app server to enter orders, update their account, and perform similar activities, which will in turn update the database with these changes. Because we have personal data stored in the database it's critical to ensure that we only allow access from trusted and necessary resources. Let's take a look at a number of ways you can control access to your SQL database over the network.

## Firewall rules

Azure SQL Database has a built-in firewall that is used to allow and deny network access to both the database server itself, as well as individual databases. Initially, all public access to your Azure SQL Database is blocked by the SQL Database firewall. To access a database server, you must specify one or more server-level IP firewall rules that enable access to your Azure SQL Database. You use the IP firewall rules to specify which IP address ranges from the Internet are allowed, and whether Azure applications can attempt to connect to your Azure SQL Database.

Firewall rules are configured at the server and/or database level, and will specifically state which network resources are allowed to establish a connection to the database. Depending on the level, the rules you can apply will be as follows:

- Server-level firewall rules
  - Allow access to Azure services
  - IP address rules
  - Virtual network rules
- Database-level firewall rules
  - IP address rules

SQL Data Warehouse only supports server-level IP firewall rules, and not database-level IP firewall rules.

Let's take a closer look at how these rules work.

### Server-level firewall rules

These rules enable clients to access your entire Azure SQL server, that is, all the databases within the same logical server. There are three types of rules that can be applied at the server level.

The **Allow access to Azure services** rule allows services within Azure to connect to your Azure SQL Database. When enabled, this setting allows communications from all Azure public IP addresses. This includes all Azure Platform as a Service (PaaS) services, such as Azure App Service and Azure Container Service, as well as Azure VMs that have outbound Internet access. This rule can be configured through the **ON/OFF** option in the firewall pane in the portal, or by an IP rule that has 0.0.0.0 as the start and end IP addresses.

![https://docs.microsoft.com/en-us/learn/modules/secure-your-azure-sql-database/media/2-allow-azure-services.png](https://docs.microsoft.com/en-us/learn/modules/secure-your-azure-sql-database/media/2-allow-azure-services.png)

This rule is used when you have applications running on PaaS services in Azure, such as Azure Logic Apps or Azure Functions, that need to access your Azure SQL Database. Many of these services don't have a static IP address, so this rule is needed to ensure they are able to connect to the database.

**IP address rules** are rules that are based on specific public IP address ranges. IP addresses connecting from an allowed public IP range will be permitted to connect to the database.

![https://docs.microsoft.com/en-us/learn/modules/secure-your-azure-sql-database/media/2-server-ip-rule-1.png](https://docs.microsoft.com/en-us/learn/modules/secure-your-azure-sql-database/media/2-server-ip-rule-1.png)

These rules can be used when you have a static public IP address that needs to access your database. Note that **unless you allow an IP rule to have 0.0.0.0 as the start and end IP addresses, Azure Services will not have access by default**.

**Virtual network rules** allow you to explicitly allow connection from specified subnets inside one or more Azure virtual networks (VNets). Virtual network rules can provide greater access control to your databases and can be a preferred option depending on your scenario. Since Azure VNet address spaces are private, you can effectively eliminate exposure to public IP addresses and secure connectivity to those addresses you control.

![https://docs.microsoft.com/en-us/learn/modules/secure-your-azure-sql-database/media/2-vnet-rule.png](https://docs.microsoft.com/en-us/learn/modules/secure-your-azure-sql-database/media/2-vnet-rule.png)

Virtual network rules are used when you have Azure VMs that need to access your database.

For server-level rules, all of the above can be created and manipulated through the portal, PowerShell, the CLI and through Transact-SQL (T-SQL).

### Database-level firewall rules

## Restricting network access in practice

### Use the server-level allow access to Azure services rule

### Use a database-level IP address rule

#### Use a server-level IP address rule

#### Use a server-level virtual network rule

# Exercise - Control who can access your database

# Exercise - Secure your data in transit, at rest, and on display

# Exercise - Monitor your database

# Knowledge check

# Summary
