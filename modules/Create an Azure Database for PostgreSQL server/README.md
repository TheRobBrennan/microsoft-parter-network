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

This unit serves to familiarize you with the steps you'll take in the next unit when you actually perform the exercise to create a server. Refer back to this section if you get stuck in the next unit.

## Scenario

Let's assume you're using an on-premises PostgreSQL database. Your company is now looking at expanding device support, availability, data tracking, and processing features by moving your server into Azure. You'll investigate how much effort it takes to automate the creation of an Azure Database for PostgreSQL server.

Creating a single Azure Database for PostgreSQL server using the Azure portal is easy. Creating more than one database and running on-going maintenance using only the portal may become tedious. You'll use the Azure CLI to create scripts when you want to automate management tasks.

Creating almost any resource within Microsoft Azure can be automated using the Azure CLI. In this unit, you'll learn how to automate management of your Azure Database for PostgreSQL servers using the Azure CLI.

## What is the Azure CLI?

The [Azure CLI](https://docs.microsoft.com/en-us/cli/azure) is Microsoft’s cross-platform command-line environment for managing Azure resources. You can use the Azure CLI from your browser with Azure Cloud Shell, or you can install the Azure CLI locally on macOS, Linux, or Windows. The Azure CLI is run from a local command line using bash or PowerShell. Running the Azure CLI locally requires additional setup. We'll use Azure Cloud Shell for executing the Azure CLI commands.

## What is Azure Cloud Shell?

Azure Cloud Shell is a browser-based shell experience that's hosted in the cloud and allows you to connect to Azure using an authenticated session. You can execute the Azure CLI commands to automate the management of an Azure Database for PostgreSQL server. Common Azure CLI tools are pre-installed and configured in Cloud Shell for you to use with your account.

Cloud Shell requires an Azure storage resource to persist any files you create while working in Cloud Shell. On first launch, Cloud Shell prompts to create a resource group, storage account, and Azure Files share on your behalf. This is a one-time step and will be automatically attached for all future Cloud Shell sessions.

## Create an Azure Database for PostgreSQL server using the Azure CLI

You'll use the Azure Cloud Shell terminal to create an Azure Database for PostgreSQL server using Azure CLI.

The Azure CLI server creation command provides usage help that shows all available parameters. Here is an example of the command:

```sh
az postgres server create [-h] [--verbose] [--debug]
                            [--output {json,jsonc,table,tsv}]
                            [--query JMESPATH]
                            --resource-group RESOURCE_GROUP_NAME --name SERVER_NAME
                            --sku-name SKU_NAME [--location LOCATION]
                            --admin-user ADMINISTRATOR_LOGIN
                            [--admin-password ADMINISTRATOR_LOGIN_PASSWORD]
                            [--backup-retention BACKUP_RETENTION]
                            [--geo-redundant-backup GEO_REDUNDANT_BACKUP]
                            [--ssl-enforcement {Enabled,Disabled}]
                            [--storage-size STORAGE_MB]
                            [--tags [TAGS [TAGS ...]]]
                            [--version VERSION]
                            [--subscription _SUBSCRIPTION]
```

The optional parameters are surrounded in brackets. Let's examine a few of the common parameters.

### Parameters

The `--resource-group <resource\*group_name>` parameter specifies the resource group within which to create the server.

The server `admin-user` and `admin-password` that you specify is required to sign in to the server and its databases. Remember or record this information for later when interacting with the new server.

You use the `--sku-name` parameter to specify part of the pricing tier, in this case, compute resource. The value follows the convention `{pricing tier}\*{compute generation}\_{vCores}`.

Examples:

- `--sku-name B_Gen4_4` maps to Basic, Gen 4, and 4 vCores.
- `--sku-name GP_Gen5_32` maps to General Purpose, Gen 5, and 32 vCores.
- `--sku-name MO_Gen5_2` maps to Memory Optimized, Gen 5, and 2 vCores.

Recall that we discussed the three pricing tiers in the unit where we created the server using the portal.

Let's assume you want to use a Basic, Gen 5, and 1 vCore compute resource. You'll specify the parameter as `--sku-name B_Gen5_1`.

You use the `--storage-size` parameter to specify part of the pricing tier. If the value isn't specified, then it defaults to 5,120 MB. Valid storage sizes range from 5,120 MB and increases in increments of 1,024 MB up to 1,048,576 MB.

The `--backup-retention` parameter is used when you need to specify the retention period for backups, which is specified in days. If the value isn't specified, then it **defaults to seven days**.

You use the `--version` parameter to specify the major version of PostgreSQL that you'd like to use.

You've now seen the steps to create an Azure Database for PostgreSQL server using the Azure CLI. In the next unit, you'll create an Azure Database for PostgreSQL server using the Azure CLI.

# Exercise - Create an Azure Database for PostgreSQL server via the Azure CLI

You decide to create an Azure Database for PostgreSQL server to store routes captured from runners' fitness devices. Based on historic captured data volumes, you know your server storage requirements should be set at 20 GB. To support your processing requirements, you need compute Gen 5 support with 1 vCore. You also know that you require a retention period of 15 days for data backups.

## Create an Azure PostgreSQL database server with the Azure CLI

Keep in mind you want to set your server storage size at 20 GB, compute Gen 5 support with 1 vCore and a retention period of 15 days for data backups.

Set the name of our new database server into an environment variable so we can reuse it.

```sh
serverName=wingtiptoys-$RANDOM
```

Set the username of the user that is allowed to access the server into an environment variable so we can reuse it.

```sh
userName=azureuser
```

Use the `az postgres server create` method to create a new database using the following syntax.

```sh
az postgres server create \
   --name [unique_server_name] \
   --resource-group [resource_group_name] \
   --location [your_region] \
   --sku-name [sku_name] \
   --storage-size [size_in_mb] \
   --backup-retention [number_days] \
   --version [server_version] \
   --admin-user [admin_user_name] \
   --admin-password [server_admin_password]
```

Where:

- --name - Specify a unique server name using lowercase letters 'a'-'z', the numbers 0-9, and the hyphen.
- --resource-group - Specify your resource group; use `learn-2d954eb7-fcdc-4e83-a37e-cf0286a555c0` for this exercise.
- --location - Specify a location from the following list: - `westus2` - southcentralus - centralus - eastus - westeurope - southeastasia - japaneast - brazilsouth - australiasoutheast - centralindia
- --admin-user - Specify the admin username for your server.
- --admin-password - Specify the admin password for your server.
- --sku-name - For this exercise, specify `B_Gen5_1` for pricing tier B, generation 5 hardware, and 1 vCore.
- --storage-size - Specify the storage capacity of the server in megabytes.
- --backup-retention - Specify the number of days a backup is retained.
- --version - Specify the major version of the server.

Run the following command. Remember to replace the `<location>` value with one of the regions suggested above - `westus2`

```sh
az postgres server create \
   --name $serverName \
   --resource-group learn-2d954eb7-fcdc-4e83-a37e-cf0286a555c0 \
   --location westus2 \
   --sku-name B_Gen5_1 \
   --storage-size 20480 \
   --backup-retention 15 \
   --version 11 \
   --admin-user $userName \
   --admin-password "P@ssw0rd"
```

The system will take a few minutes to process the information when executed. Go ahead and wait for the command to complete.

Once the command has completed, a JavaScript Object Notation (JSON) string that describes the server is returned. If there was a failure, an error message is displayed. You can use this error information to review and fix your command parameters and try again.

The JSON object will look something like:

```json
{
  "administratorLogin": "azureuser",
  "byokEnforcement": "Disabled",
  "earliestRestoreDate": "2020-07-04T05:23:53.927000+00:00",
  "fullyQualifiedDomainName": "wingtiptoys-21753.postgres.database.azure.com",
  "id": "/subscriptions/1f1aa1cf-6365-422a-8504-a1acdf9bc529/resourceGroups/learn-2d954eb7-fcdc-4e83-a37e-cf0286a555c0/providers/Microsoft.DBforPostgreSQL/servers/wingtiptoys-21753",
  "identity": null,
  "infrastructureEncryption": "Disabled",
  "location": "westus2",
  "masterServerId": "",
  "minimalTlsVersion": "TLSEnforcementDisabled",
  "name": "wingtiptoys-21753",
  "privateEndpointConnections": [],
  "publicNetworkAccess": "Enabled",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "learn-2d954eb7-fcdc-4e83-a37e-cf0286a555c0",
  "sku": {
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 15,
    "geoRedundantBackup": "Disabled",
    "storageAutogrow": "Enabled",
    "storageMb": 20480
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "11"
}
```

You've successfully created a PostgreSQL server using the Azure CLI. In the next unit, you'll see how to configure your server's security settings.

# Server security considerations

Let's assume you're using an on-premises PostgreSQL database. You're managing all security aspects and you've locked down all access to your servers using the standard PostgreSQL server-level firewall rules. Now you want to make sure that you can configure the same server-level firewall rules in Azure.

## Server security considerations and connection methods

You have a number of options to restrict access to your Azure Database for PostgreSQL server and databases. Network access can be restricted at a network, server, or database level.

You can use any of the following options:

- User accounts to restrict database access
- Virtual networks to restrict network access
- Firewall rules to restrict server access

### Authentication and authorization

The Azure Database for PostgreSQL server supports native PostgreSQL authentication. You can connect and authenticate to the server with the server's admin login. You'll also create users to connect to specific databases to limit access.

### What is a virtual network?

A virtual network is a logically isolated network that's created within the Azure network. You can use a virtual network to control what Azure resources can connect to other resources.

Imagine you're running a web application that connects to a database. You'll use subnets to isolate different parts of the network. A subnet is a part of a network that's based on a range of IP addresses.

To configure these subnets, you'll create a virtual network and then subdivide the network into subnets. The web application will operate on one subnet and the database on another subnet. Each subnet will have its own rules for communicating to and from the other network. These rules give you the ability to restrict access from the database to the web application.

### What is a firewall?

A firewall is a service that grants server access based on the originating IP address of each request. You create firewall rules that specify ranges of IP addresses. Only clients from these granted IP addresses will be allowed to access the server. Firewall rules, generally speaking, also include specific network protocol and port information. For example, a PostgreSQL server by default listens to TCP requests on port 5432.

### Azure Database for PostgreSQL server firewall

The Azure Database for PostgreSQL server firewall prevents all access to your database server until you specify which computers have permission. The firewall configuration allows you to specify a range of IP addresses that are allowed to connect to the server. The server always uses the default PostgreSQL connection information.

![https://docs.microsoft.com/en-us/learn/modules/create-azure-db-for-postgresql-server/media/6-firewall-diagram.png](https://docs.microsoft.com/en-us/learn/modules/create-azure-db-for-postgresql-server/media/6-firewall-diagram.png)

### Azure Database for PostgreSQL server SSL connections

Azure Database for PostgreSQL prefers that your client applications connect to the PostgreSQL service using the Secure Sockets Layer (SSL). Enforcing SSL connections between your database server and your client applications helps protect against "man in the middle" and similar attacks by encrypting the data between the server and client. Enabling SSL requires the exchange of keys and strict authentication between client and server for the connection to work. Details about using SSL are beyond the scope of this learning module.

## Configure connection security

Let's look at the decisions and steps you make to configure an Azure Database for PostgreSQL server firewall. You'll also see how to connect to the server that you created earlier.

Sign into the Azure portal using the same account you activated the sandbox with. Navigate to the server resource for which you'd like to create a firewall rule.

Then, you'll select the **Connection Security** option to open the connection security pane to the right.

![https://docs.microsoft.com/en-us/learn/modules/create-azure-db-for-postgresql-server/media/6-db-security-settings.png](https://docs.microsoft.com/en-us/learn/modules/create-azure-db-for-postgresql-server/media/6-db-security-settings.png)

On this screen, you have several options. You can:

- Add the IP address that you use to access the portal as a firewall entry by clicking on the Add client IP button.
- Allow access to Azure services. By default, all Azure services **don't** have access to the PostgreSQL server.
- Add firewall rules by entering ranges of IP addresses.
- Enforce SSL connections. This option forces your client to connect to the server using an SSL certificate.

Always remember to click on the **Save** icon above the entry fields to save the updated configuration after you've made changes.

### Allow access to Azure services

To use Azure Cloud Shell to access or configure your server, make sure to enable **Allow Access to Azure Services**. This step is going to add a firewall rule to the server configuration to allow access from Cloud Shell. This rule won't show as one of the custom rules that you add.

You also need to disable **Enforce SSL connection**. PowerShell can't connect to the server if SSL is required for client connections.

Both of these options will result in an error message that's displayed on the command line if not configured correctly.

For example, if access is not allowed to Azure services and enforce SSL connections is enabled, then you'll see something similar to this error when the firewall is blocking access:

```sh
psql: FATAL: no pg_hba.conf entry for host "123.45.67.89", user "adminuser", database "postgres", SSL on FATAL:  SSL connection is required. Please specify SSL options and retry.
```

### Create a firewall rule using the portal

Let's say you want to create a firewall rule that provides access from any IP address.

WARNING: Creating this firewall rule will allow any IP address on the Internet to attempt to connect to your server. Even though clients won't be able access the server without the username and password, enable this rule with caution and make sure you understand the security implications.

You create a new firewall rule by entering the following data in the labeled fields:

- Rule Name: `AllowAll`
- Start IP: 0.0.0.0
- End IP: 255.255.255.255

To remove a firewall rule, you'll click the ellipsis (...) at the end of the rule that you want to delete. Click the **Delete** button to delete the rule.

Click on the **Save** icon above the entry fields to commit the deletion of the rule.

### Create a firewall rule using the Azure CLI

You can use the Azure CLI to add firewall rules to your server with the `az postgres server firewall-rule create` command. Here's an example that creates the rule from above.

```sh
az postgres server firewall-rule create \
  --resource-group learn-2d954eb7-fcdc-4e83-a37e-cf0286a555c0 \
  --server <server-name> \
  --name AllowAll \
  --start-ip-address 0.0.0.0 \
  --end-ip-address 255.255.255.255
```

You remove firewall rules from your server with the command `az postgres server firewall-rule delete`. Here's an example:

```sh
az postgres server firewall-rule delete \
  --name AllowAll \
  --resource-group learn-2d954eb7-fcdc-4e83-a37e-cf0286a555c0 \
  --server-name <server-name>
```

## Connecting to your server

Like any modern database, PostgreSQL requires regular server administration to achieve best performance. You have a number of options to connect and manage your Azure Database for PostgreSQL server. We'll use `psql` to connect to the server.

### What is psql?

The command-line tool called `psql` is the PostgreSQL distributed interactive terminal for working with PostgreSQL servers and databases. `psql` works with Azure Database for PostgreSQL the same as with any other PostgreSQL implementation and is included with Azure Cloud Shell. The `psql` tool allows you to manage databases as well as execute structure queries against these databases.

Using `psql` requires a successful connection to a PostgreSQL server. There are a number of command-line parameters available for use when working with psql.

- `--host` - The host to which you'd like to connect.
- `--username` - The user name/ID with which to connect.
- `--dbname` - The name of the database to connect to.

TIP: You'll typically connect to the `postgres` management database when managing your server access and database configurations.

Here is the complete command:

```sh
psql --host=<server-name>.postgres.database.azure.com \
  --username=<admin-user>@<server-name> \
  --dbname=<database>
```

After you're connected, you'll be presented with a command prompt and can execute commands to your server and databases.

You've now seen the steps that you take to configure Azure Database for PostgreSQL security settings. In the next unit, you'll configure Azure Database for PostgreSQL security settings. You'll also connect to the server using Cloud Shell.

# Exercise - Connect to an Azure Database for PostgreSQL server

## Allow Azure service access

Before we begin, you'll have to allow access to Azure services if you want to use PowerShell and `psql` to connect to your server. Recall that you can allow access in two ways.

Your first option is to enable **Allow access to Azure services**. Allowing access will create a firewall rule even though the rule isn't entered in the list of custom rules you create.

Your second option is to create a firewall rule that allows access to all IP addresses. The rule will include the IP address for the client running PowerShell that you'll use to execute `psql` from.

You also need to disable the **Enforce SSL connection** option.

### Create a firewall rule

Sign into the Azure portal using the same account you activated the sandbox with.

Navigate to the server resource for which you would like to create a firewall rule.

Select the **Connection Security** option to open the connection security pane to the right.

![https://docs.microsoft.com/en-us/learn/modules/create-azure-db-for-postgresql-server/media/6-db-security-settings.png](https://docs.microsoft.com/en-us/learn/modules/create-azure-db-for-postgresql-server/media/6-db-security-settings.png)

Recall that you want to allow access to PowerShell clients running psql.

You can choose to either:

- Set **Allow access to Azure services** to **ON**
- Set **Enforce SSL connection** to **DISABLED**
- Click the **Save** button to save your changes

Or, you can add a firewall rule to allow access to all IP addresses by adding a firewall rule. Use the following values:

- Rule Name: `AllowAll`
- Start IP: 0.0.0.0
- End IP: 255.255.255.255
- Set **Enforce SSL connection** to **DISABLED**
- Click the **Save** button to save your changes

WARNING: Creating this firewall rule will allow any IP address on the Internet to attempt to connect to your server. In production environments, you'll want to restrict access to specific IP addresses.

### Connect to the database with psql

In the Azure Cloud Shell on the right, connect `psql` to your server using the following command:

```sh
psql --host=wingtiptoys-21753.postgres.database.azure.com --username=azureuser@wingtiptoys-21753 --dbname=postgres

# Password - P@ssw0rd
```

Use the values you chose for the `server-name`, and `admin-user`.

**postgres** is the default management database every PostgreSQL server is created with. You'll be prompted for the password you provided when you created the server.

Once successfully connected, execute the `\l` command to list all databases. This command will result in two or more default databases returned.

Press `q` to exit the list.

Create a new database with the following SQL command:

```sql
CREATE DATABASE "Adventureworks";
```

Run the `psql` command `\c Adventureworks` to connect to the database.

Add some data to the database with the following SQL commands that add data to two tables:

```sql
CREATE TABLE PEOPLE(NAME TEXT NOT NULL, AGE INT NOT NULL);
INSERT INTO PEOPLE(NAME, AGE) VALUES ('Bob', 35);
INSERT INTO PEOPLE(NAME, AGE) VALUES ('Sarah', 28);
CREATE TABLE LOCATIONS(CITY TEXT NOT NULL, STATE TEXT NOT NULL);
INSERT INTO LOCATIONS(CITY, STATE) VALUES ('New York', 'NY');
INSERT INTO LOCATIONS(CITY, STATE) VALUES ('Flint', 'MI');
```

Retrieve the data you added using the following SQL commands:

```sql
SELECT * FROM PEOPLE;
SELECT * FROM LOCATIONS;
```

You can try other psql commands.

- `\?` to get help.
- `\dt` to list the tables.

When you're finished executing `psql` operations on your server, execute the command `\q` to quit psql.

# Summary

In this module, you've seen what the Azure Database for PostgreSQL offering looks like. You saw how to create an Azure Database for PostgreSQL server using the Azure portal as well as the Azure CLI.

You looked at the configuration options available to configure a PostgreSQL server-level firewall rule and enforce SSL connections. Finally, you saw how to connect to an Azure Database for PostgreSQL server using _psql_ in Azure Cloud Shell.

## Check your knowledge

1. True or false: The Azure Database for PostgreSQL server firewall permits connections from any remote computer by default.

[] True
[x] False

2. Suppose you would like to set a retention period of 30 days for the data backups of your PostgreSQL database. How do you enable that?

[x] Specify the retention period when you create your server.
[] Install the retention extension.
[] Run a script each day that removes backups that are older than 30 days.

3. Say you need 10 PostgreSQL databases to support a number of projects. Which is the easiest way to create and manage all of them?

[] Using the portal.
[x] Using the Azure CLI.
