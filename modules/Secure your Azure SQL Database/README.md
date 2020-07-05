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

These rules allow access to an individual database on a logical server and are stored in the database itself. **For database-level rules, only IP address rules can be configured.** They function the same as when applied at the server-level, but are scoped to the database only.

The benefits of database-level rules are their portability. When replicating a database to another server, the database-level rules will be replicated, since they are stored in the database itself.

The downside to database-level rules is that you can only use IP address rules. This may limit the flexibility you have and can increase administrative overhead.

Lastly, database-level firewall rules can be created and manipulated only through T-SQL.

## Restricting network access in practice

Whenever possible, as a best practice, use database-level IP firewall rules to enhance security and to make your database more portable. Use server-level IP firewall rules for administrators and when you have several databases with the same access requirements, and you don't want to spend time configuring each database individually.

Let's take a look at how these work in practice, and how you can secure network access to only allow what is necessary. Recall that we created an Azure SQL Database logical server, a database, and the _appServer_ Linux VM acting as an application server. This scenario is often seen when a database has been migrated to Azure SQL Database and resources inside of a virtual network need to access it. The firewall feature of Azure SQL Database can be used in many scenarios, but this is an example that has practical applicability and demonstrates how each of the rules functions.

Let's go through the firewall settings and see how they work. We'll use both the cloud shell and the portal for these exercises.

The database we created currently does not allow access from any connections. This is by design based on the commands that we ran to create the logical server and database. Let's confirm this.

In the cloud shell, SSH into your Linux VM if you aren't already connected.

```sh
ssh nnn.nnn.nnn.nnn

# Example where nnn.nnn.nnn.nnn is the value from the publicIpAddress output in the previous step.
ssh 40.112.170.37
```

Recall the `sqlcmd` command we retrieved earlier. Go ahead and run it to attempt to connect to the database. Make sure you replace [username] and [password] with the ADMINUSER credentials you specified in the previous unit. Make sure to keep the single quotes around the username and password so that any special characters aren't misinterpreted by the shell.

```sh
sqlcmd -S tcp:server18714.database.windows.net,1433 -d marketplaceDb -U 'rbadmin' -P 'ViVKUwPtAdW2' -N -l 30
```

You should receive an error when trying to connect. This is expected since we've not allowed any access to the database.

```sh
Sqlcmd: Error: Microsoft ODBC Driver 17 for SQL Server : Cannot open server 'server18714' requested by the login. Client with IP address '40.112.170.37' is not allowedto access the server.  To enable access, use the Windows Azure Management Portal orrun sp_set_firewall_rule on the master database to create a firewall rule for this IP address or address range.  It may take up to five minutes for this change to takeeffect..
```

Let's grant access so we can connect.

### Use the server-level allow access to Azure services rule

Since our VM has outbound internet access, we can use the **Allow access to Azure services** rule to allow access from our VM.

Sign into the Azure portal using the same account you activated the sandbox with.

In the **Search resources, services, and docs** box at the top, search for your database server name, `serverNNNN`. Select the SQL server.

In the SQL server panel, in the **Security** section in the left menu, select **Firewalls and virtual networks**.

Set **Allow access to Azure services** to **ON** and click **Save**.

Back in your SSH session, let's try to connect to your database again.

```sh
sqlcmd -S tcp:server18714.database.windows.net,1433 -d marketplaceDb -U 'rbadmin' -P 'ViVKUwPtAdW2' -N -l 30
```

At this point, you should be able to connect. If it's successful, you should see a `sqlcmd` prompt.

So we've opened up connectivity, but this setting currently allows access from _any_ Azure resource, **including resources outside of our subscription**. Let's restrict this further to limit network access to only resources that are within our control.

### Use a database-level IP address rule

Recall that database-level IP address rules allow only access to an individual database on a logical server. We'll use one here to grant access to the static IP of our _appServer_ VM.

To create a database-level IP rule, we'll need to run some T-SQL commands. You'll create a database rule using the following convention, where you pass in the rule name, the starting IP address, and the ending IP address. By specifying the start and end IP to be the same, we're limiting access to a single IP, though we could expand the range if we had a larger block of addresses that required access.

```sql
EXECUTE sp_set_database_firewall_rule N'My Firewall Rule', '40.112.128.214', '40.112.128.214'
```

While still at the `sqlcmd` prompt, run the following command, replacing the public IP address of your _appServer_ VM in both locations below.

TIP: When running T-SQL commands such as the following, the `GO` on the second line may not copy through to the `sqlcmd` prompt, so you will likely need to type this out. The T-SQL command won't execute without it, so make sure to run the `GO` command.

```sql
EXECUTE sp_set_database_firewall_rule N'Allow appServer database level rule', '40.112.170.37', '40.112.170.37';
GO
```

Once the command completes, type `exit` to exit `sqlcmd`. Remain connected via SSH.

In the portal, on the **Firewalls and virtual networks** panel for your SQL server, set **Allow access to Azure services** to **OFF** and click **Save**. This will disable access from all Azure services, but we'll still be able to connect since we have a database-level IP rule for our server.

Back in cloud shell, in the VM you are connected via SSH to, try connecting to your database again.

```sh
sqlcmd -S tcp:server18714.database.windows.net,1433 -d marketplaceDb -U 'rbadmin' -P 'ViVKUwPtAdW2' -N -l 30
```

Using a database-level rule allows access to be isolated specifically to the database. This can be useful if you'd like to keep your network access configured per database. If multiple databases share the same level of network access, you can simplify administration by using a server-level rule to apply the same access to all databases on the server.

#### Use a server-level IP address rule

Database-level rules are a great option, but what if we had multiple databases on the same server that our _appServer_ VM needed to connect to? We could add a database-level rule to each database, this can take more work as we add more databases. It would reduce our administration efforts to allow access with a server-level rule, which would apply to all databases on the server.

Let's now use a server-level IP rule to restrict the systems that can connect.

While still at the sqlcmd prompt, run the following command to delete the database-level IP address rule.

```sql
EXECUTE sp_delete_database_firewall_rule N'Allow appServer database level rule';
GO
```

Once the command completes, type exit to exit sqlcmd. Remain connected via SSH.

Back in the portal, on the **Firewalls and virtual networks** panel for your SQL server, add a new rule with a **RULE NAME** of **Allow appServer** and with the **START IP** and **END IP** set to the public IP address of the _appServer_ VM - which is `40.112.170.37` in my sandbox example.

Back in cloud shell, on your appServer VM, try connecting to your database again.

```sh
sqlcmd -S tcp:server18714.database.windows.net,1433 -d marketplaceDb -U 'rbadmin' -P 'ViVKUwPtAdW2' -N -l 30
```

So we've isolated connectivity to only the IP address we specified in the rule. This works great, but can still be an administrative challenge as you add more systems that need to connect. It also requires a static IP or an IP from a defined IP address range; if the IP is dynamic and changes, we'd have to update the rule to ensure connectivity. The appServer VM is currently configured with a dynamic IP address, so this IP address is likely to change at some point, breaking our access as soon as that happens. Let's now look at how virtual network rules can be beneficial in our configuration.

#### Use a server-level virtual network rule

In this case, since our VM is running in Azure, we can use a server-level virtual network rule to isolate access and make it easy to enable future services to gain access to the database.

Back in the portal and still on the **Firewalls and virtual networks** panel, in the **Virtual networks** section click the **+ Add existing virtual network** option.

The Create/Update virtual network rule dialog will show. Set the following values:

| Setting                      | Value                         |
| ---------------------------- | ----------------------------- |
| Name                         | Leave the default value       |
| Subscription                 | Concierge Subscription        |
| Virtual network              | appServerVNET                 |
| Subnet name / Address prefix | appServerSubnet / 10.0.0.0/24 |

Click **Enable** to enable the service endpoint on the subnet, then **OK** once the endpoint is enabled to create the rule.

Now, let's remove the IP address rule. Click the ... next to your **Allow appServer** rule and click **Delete**, then click **Save**.

Back in cloud shell, on your _appServer_ VM, try connecting to your database again.

```sh
sqlcmd -S tcp:server18714.database.windows.net,1433 -d marketplaceDb -U 'rbadmin' -P 'ViVKUwPtAdW2' -N -l 30
```

At this point, you should be able to connect. If it's successful, you should see a sqlcmd prompt.

What we've done here effectively removes any public access to the SQL server, and only permits access from the specific subnet in the Azure VNet we defined. If we were to add additional app servers in that subnet, no additional configuration would be necessary, as any server in that subnet would have the ability to connect to the SQL server. This limits our exposure to services outside of our scope of control, and eases administration if we were to add additional servers. This is an effective method of securing network access to an Azure SQL Database.

# Exercise - Control who can access your database

Even though we may be able to connect to the database over the network, that doesn't mean we can actually gain access to the data itself. Following a layered approach, we'll want to ensure that only users who need access to the data can actually access it. This is where authentication and authorization come in to play.

## Authentication

Authentication is the process of verifying an identity. This identity could be a user, a service running on a system, or a system itself (such as a virtual machine). Through the process of authentication, we ensure that the person or system is who they claim to be. SQL Database supports two types of authentication: SQL authentication and Azure Active Directory authentication.

### SQL authentication

SQL authentication method uses a username and password. User accounts can be created in the master database and can be granted permissions in all databases on the server, or they can be created in the database itself (called **contained users**) and given access to only that database. When you created the logical server for your database, you specified a "server admin" login with a username and password. Using these credentials, you can authenticate to any database on that server as the database owner, or "dbo".

### Azure Active Directory authentication

This authentication method uses identities managed by Azure Active Directory (AD) and is supported for managed and integrated domains. Use Azure AD authentication (integrated security) whenever possible. With Azure AD authentication, you can centrally manage the identities of database users and other Microsoft services in one central location. Central ID management provides a single place to manage database users and simplifies permission management. If you want to use Azure AD authentication, you must create another server admin called the "Azure AD admin," which is allowed to administer Azure AD users and groups. This admin can also perform all operations that a regular server admin can.

## Authorization

Authorization refers to what an identity can do within an Azure SQL Database. This is controlled by permissions granted directly to the user account and/or database role memberships. A database role is used to group permissions together to ease administration, and a user is added to a role to be granted the permissions the role has. These permissions can grant things such as the ability to log in to the database, the ability to read a table, and the ability to add and remove columns from a database. As a best practice, you should grant users the least privileges necessary. The process of granting authorization to both SQL and Azure AD users is the same.

In our example here, the server admin account you are connecting with is a member of the db_owner role, which has authority to do anything within the database.

## Authentication and authorization in practice

As a best practice, your application should use a dedicated account to authenticate. This way, you can limit the permissions granted to the application and reduce the risks of malicious activity in case the application code is vulnerable to a SQL injection attack. The recommended approach is to create a contained database user, which allows your app to authenticate directly to the database. For more information, see [Contained Database Users - Making Your Database Portable](https://docs.microsoft.com/en-us/sql/relational-databases/security/contained-database-users-making-your-database-portable?view=sql-server-2017).

Use Azure Active Directory authentication to centrally manage identities of database users and as an alternative to SQL Server authentication.

Let's now take a look at how to set up a user and grant them access to a database. In this case we'll use SQL authentication for our user, but the process would be essentially the same if we were using Azure AD authentication.

### Create a database user

Let's go ahead and create a new user that we can use to grant access to.

In cloud shell, on your _appServer_ VM, connect to your database again as your ADMINUSER.

```sh
sqlcmd -S tcp:server18714.database.windows.net,1433 -d marketplaceDb -U 'rbadmin' -P 'ViVKUwPtAdW2' -N -l 30
```

Run the following command to create a new user. This will be a _contained user_ and will only allow access to the _marketplace_ database. Feel free to adjust the password as necessary, but be sure and note it as we'll need it for a future step.

```sql
CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
GO
```

With these credentials, the user will be able to authenticate to the database, but they aren't authorized to access any data. Let's grant this user access.

### Grant permissions to a user

Let's make the user a member of the `db_datareader` and `db_datawriter` roles, granting access to read and write to the database, respectively. We also want to prevent this user from accessing a table with addresses.

While still connected to sqlcmd on _appServer_, run the following T-SQL to grant the `db_datareader` and `db_datawriter` roles to the user we just created.

```sql
ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
GO
```

We can narrow the scope of access further. We could deny a user's access to other elements within the database using the DENY operator. Run the following T-SQL to deny the user _ApplicationUser_ the ability to select data from the `SalesLT.Address` table.

```sh
DENY SELECT ON SalesLT.Address TO ApplicationUser;
GO
```

Let's now log in as that user and take a look at this in action.

While still at the T-SQL prompt, type exit to exit your session.

Now let's log back in to the database, but as the user we just created.

```sh
sqlcmd -S tcp:server18714.database.windows.net,1433 -d marketplaceDb -U 'ApplicationUser' -P 'YourStrongPassword1' -N -l 30
```

Run the following query. This is pulling data from a table that the user is authorized to access.

```sql
SELECT FirstName, LastName, EmailAddress, Phone FROM SalesLT.Customer;
GO
```

You should get back a listing of customers.

```sh
FirstName      LastName       EmailAddress                    Phone
-------------- -------------- ------------------------------- ------------
Orlando        Gee            orlando0@adventure-works.com    245-555-0173
Keith          Harris         keith0@adventure-works.com      170-555-0127
Donna          Carreras       donna0@adventure-works.com      279-555-0130
Janet          Gates          janet1@adventure-works.com      710-555-0173
...
```

Now let's see what happens when we try to query a table that we don't have access to.

```sql
SELECT * FROM SalesLT.Address;
GO
```

You should get a message that you don't have access to this table.

```sh
Msg 229, Level 14, State 5, Server server-22942, Line 1
The SELECT permission was denied on the object 'Address', database 'marketplace', schema 'SalesLT'.
```

As you can see here, even though we've granted read/write access to the database, we can further secure access to data by explicitly denying access to tables. If you had multiple users who shared similar access, you could create custom roles with the proper permissions and simplify your administration.

It's important to properly secure your database, and only grant access where necessary. Azure SQL Database provides the built-in ability to fully control the ability to authenticate and authorize identities to access the data in your database.

# Exercise - Secure your data in transit, at rest, and on display

The _marketplaceDb_ database stores information that is sensitive, such as physical addresses, email addresses, and phone numbers. If exposed, this information could be used by malicious attackers to harm our business or our customers. Let's look at how we can use encryption and data masking to enhance the security of our database.

## TLS network encryption

Azure SQL Database enforces Transport Layer Security (TLS) encryption at all times for all connections, which ensures all data is encrypted "in transit" between the database and the client. By using TLS encryption, you can ensure that anyone who may have intercepted the traffic between the app server and database would not be able to read the data. TLS encryption is a standard of securing traffic over the internet, and in this case ensures your network traffic to and from your Azure SQL database is secure by default.

## Transparent data encryption

Azure SQL Database **protects your data at rest** using transparent data encryption (TDE). TDE performs real-time encryption and decryption of the database, associated backups, and transaction log files at rest without requiring changes to the application. Using a database encryption key, transparent data encryption performs real-time I/O encryption and decryption of the data at the page level. Each page is decrypted when it's read into memory and then encrypted before being written to disk.

By default, TDE is enabled for all newly deployed Azure SQL databases. It's important to check that data encryption hasn’t been turned off, and older Azure SQL Server databases may not have TDE enabled.

Let's take a look in the portal at where TDE is configured on our _marketplaceDb_ database.

Sign into the Azure portal using the same account you activated the sandbox with.

In the search bar at the top of the portal, search for **marketplaceDb**, then select the database in the portal.

In the left menu, in the **Security** section, select the **Transparent data encryption** option.

In the data encryption option, verify that **Data encryption** is set to **On**. You should also see an encryption status of **Encrypted**.

Since new databases are encrypted by default, we can be sure that our data is encrypted on disk from as soon as we create the database.

Azure includes a built in service called Azure Security Center that gives you visibility into the security of your environment, including Azure SQL databases. Azure Security Center will flag any databases that don't have TDE enabled on them, giving you the ability to report and take action to secure your data.

## Dynamic data masking

You might have noticed when we ran our query in the previous unit that some of the information in the database is sensitive; there are phone numbers, email addresses, and other information that we may not want to fully display to everyone with access to the data.

Maybe we don't want our users to be able to see the full phone number or email address, but we'd still like to make a portion of the data available for customer service representatives to identify a customer. By using the dynamic data masking feature of Azure SQL Database, we can limit the data that is displayed to the user. Dynamic data masking is a policy-based security feature that hides the sensitive data in the result set of a query over designated database fields, while the data in the database is not changed.

Data masking rules consist of the column to apply the mask to, and how the data should be masked. You can create your own masking format, or use one of the standard masks such as:

- Default value, which displays the default value for that data type instead.
- Credit card value, which only shows the last four digits of the number, converting all other numbers to lower case x’s.
- Email, which hides the domain name and all but the first character of the email account name.
- Number, which specifies a random number between a range of values. For example, on the credit card expiry month and year, you could select random months from 1 to 12 and set the year range from 2018 to 3000.
- Custom string, which allows you to set the number of characters exposed from the start of the data, the number of characters exposed from the end of the data, and the characters to repeat for the remainder of the data.

When querying the columns, database administrators will still see the original values, but non-administrators will see the masked values. You can allow other users to see the non-masked versions by adding them to the SQL users excluded from masking list.

Let's take a look at how data masking would work in our _marketplaceDb_ database.

While still in the portal on the _marketplaceDb_ database panel, in the **Security** section in the left menu select **Dynamic Data Masking**.

The Masking rules screen shows a list of existing dynamic data masks, and recommendations for columns that should potentially have a dynamic data mask applied.

![https://docs.microsoft.com/en-us/learn/modules/secure-your-azure-sql-database/media/4-view-recommended-masked-columns.png](https://docs.microsoft.com/en-us/learn/modules/secure-your-azure-sql-database/media/4-view-recommended-masked-columns.png)

Let's add a mask for the phone number that only displays the last four digits. Click the **+ Add mask** button at the top to open the **Add masking rule** dialog.

Select the following values.

| Setting              | Value                                   |
| -------------------- | --------------------------------------- |
| Schema               | SalesLT                                 |
| Table                | Customer                                |
| Column               | Phone (nvarchar)                        |
| Masking field format | Custom string (prefix [padding] suffix) |
| Exposed Prefix       | 0                                       |
| Padding String       | XXX-XXX-                                |
| Exposed Suffix       | 4                                       |

Click **Add** to add the masking rule.

![https://docs.microsoft.com/en-us/learn/modules/secure-your-azure-sql-database/media/4-add-masking-rule.png](https://docs.microsoft.com/en-us/learn/modules/secure-your-azure-sql-database/media/4-add-masking-rule.png)

Let's add one more for the email address. Click the **+ Add mask** button at the top again to open up the **Add masking rule** dialog.

| Setting              | Value                   |
| -------------------- | ----------------------- |
| Schema               | SalesLT                 |
| Table                | Customer                |
| Column               | EmailAddress (nvarchar) |
| Masking field format | Email (aXXX@XXX.com)    |

Click **Add** to add the masking rule.

Each new mask will be added to the masking rules list. Click the **Save** button to apply the masks.

Let's take a look at how data masking changes our query.

Now let's log back in to the database, but as the _ApplicationUser_ user.

```sh
sqlcmd -S tcp:serverNNNN.database.windows.net,1433 -d marketplaceDb -U 'ApplicationUser' -P '[password]' -N -l 30
```

Run the following query.

```sql
SELECT FirstName, LastName, EmailAddress, Phone FROM SalesLT.Customer;
GO
```

Look at how the output has been masked.

```sh
FirstName     LastName      EmailAddress         Phone
------------- ------------- -------------------- ------------
Orlando       Gee           oXXX@XXXX.com        XXX-XXX-0173
Keith         Harris        kXXX@XXXX.com        XXX-XXX-0127
Donna         Carreras      dXXX@XXXX.com        XXX-XXX-0130
Janet         Gates         jXXX@XXXX.com        XXX-XXX-0173
...
```

With the masking rules we created, our data is masked with format that we've specified. These rules allow our customer service reps to verify a customer with the last four digits of their phone number, but hides the full number and the customer's email address from reps view.

# Exercise - Monitor your database

Imagine you receive an alert from your company's security administrator that a potential security breach has been detected on your network. It's suspected that an unauthorized individual may have accessed your database through malicious activity. How would you track this down? You know you need to be actively monitoring your database for suspicious activity, but what can you do to not only gain visibility into what's happening in your database, but to also prevent malicious activity from occurring?

Azure SQL Database has built-in features that can help you track what's happening in your database, and will monitor and alert you if malicious activity is identified.

## Azure SQL Database auditing

By enabling auditing, operations that occur on the database are stored for later inspection or to have automated tools analyze them. Auditing is also used for compliance management or understanding how your database is used. Auditing is also required if you wish to use Azure threat detection on your Azure SQL database.

You can use SQL database auditing to:

- Retain an audit trail of selected events. You can define categories of database actions to be audited.
- Report on database activity. You can use pre-configured reports and a dashboard to get started quickly with activity and event reporting.
- Analyze reports. You can find suspicious events, unusual activity, and trends.

Audit logs are written to Append Blobs in an Azure Blob storage account that you designate. Audit policies can be applied at the server-level or database-level. Once enabled, you can use the Azure portal to view the logs, or send them to Log Analytics or Event Hub for further processing and analysis.

## Auditing in practice

As a best practice, **avoid enabling both server blob auditing and database blob auditing together**, unless:

- You want to use a different storage account or retention period for a specific database.
- You want to audit event types or categories for a specific database that differs from the rest of the databases on the server. For example, you might have table inserts that need to be audited but only for a specific database.

Otherwise, **it's recommended you enable only server-level blob auditing and leave the database-level auditing disabled for all databases**.

Let's look at the steps you take to set up auditing on your system.

Sign into the Azure portal using the same account you activated the sandbox with.

In the search bar at the top of the portal, search for **serverNNNN** (replacing `NNNN` with the number from your server name), then select the server in the portal.

In the left menu, in the **Security** section, select the **Auditing** option.

Auditing is turned off by default. To enable it on your database server, tap the **ON** button.

Once the ON button is selected, select the **Storage** checkbox, then click **Storage details** to define the storage account.

In the **Storage settings** dialog, you can select an existing storage account or create a new storage account to store your audits. The storage account must be configured to use the same region as your server. In this case, we'll define a new storage account. Click **Storage account**, which will then open up the **Create storage account** dialog. Name the storage account `serverNNNNauditing`, replacing the `NNNN` with the number from your logical server name. Leave the rest of the options at their defaults and select **OK**. Back in the **Storage settings** dialog, leave the defaults and click **OK**.

Click the **Save** button in the toolbar to save your changes and enable auditing on your database server.

Now let's generate some audit records and take a look at what you can expect.

Let's log back in to the database as the _ApplicationUser_ user.

```sh
sqlcmd -S tcp:serverNNNN.database.windows.net,1433 -d marketplaceDb -U 'ApplicationUser' -P '[password]' -N -l 30
```

Run the following query.

```sql
SELECT FirstName, LastName, EmailAddress, Phone FROM SalesLT.Customer;
GO
```

Back in the portal on your SQL server, select **SQL databases** in the left menu and select the _marketplace_ database.

In the left menu on your _marketplace_ database, in the **Security** section select **Auditing**.

Since we enabled auditing at the server-level, you should see that it's enabled here. Select **View audit logs** in the top menu bar to view the logs.

You should see one or more audit records with **PRINCIPAL NAME** of _ApplicationUser_ and **EVENT TYPE** of **BATCH COMPLETED**. One of them should contain the details of the query you just executed. You might also see other events such as authentication failures and success. Select any record to see the full details of the event.

![https://docs.microsoft.com/en-us/learn/modules/secure-your-azure-sql-database/media/5-audit-log.png](https://docs.microsoft.com/en-us/learn/modules/secure-your-azure-sql-database/media/5-audit-log.png)

These actions configure the audits at the database server level and will apply to all databases on the server. You can also configure auditing at a database level.

Let's take a look at another feature that leverages these logs to increase the security of your database.

## Advanced Data Security for Azure SQL Database

### Setup and configuration

### Data Discovery & Classification

### Vulnerability Assessment

### Threat Detection

# Knowledge check

# Summary
