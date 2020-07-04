[Scale multiple Azure SQL Databases with SQL elastic pools](https://docs.microsoft.com/en-us/learn/modules/scale-sql-databases-elastic-pools/) (~39 mins)

SQL elastic pools allow you to manage performance and cost for a collection of SQL databases.

# Introduction

SQL database elastic pools are a cost-effective service that can manage and scale multiple Azure SQL databases that have varying and unpredictable resource requirements.

Imagine you work for an innovative fitness company that uses Azure Cognitive Services to visually analyze hundreds of physical attributes and metrics of your customers. Customers come to your location to workout, and you use the intelligent systems to track their fitness progress and make health recommendations every time they visit. You designed your system to store customer data in an Azure SQL database.

The business became successful. As the business grew, franchises were sold globally and there are now thousands of locations. However, your company continues to develop, maintain, and run the intelligent systems. What started as gym has become a software as a service (SaaS) company. You now need to maintain and scale separate Azure SQL databases for each location. However the data resource requirements for each location are unique, some serve larger areas than others, and they run promotions at different times of year. As result, the rates of growth vary and load is unpredictable.

In this module, you'll learn how to use SQL Elastic pools to scale, manage performance, and manage costs for collections of Azure SQL databases.

## Learning objectives

In this module, you will:

- Create an elastic pool
- Add databases to an elastic pool
- Configure database performance in an elastic pool

## Prerequisites

- Basic knowledge of the Azure Portal
- Basic knowledge of Azure SQL Server

# Create a SQL elastic pool

Software as a Service (SaaS) providers like the fitness company often need to provision a SQL database for each customer, in our case, for each location. As a service provider, you'll need to react to unpredictable workloads, especially when storing customer data. You may not have visibility as to how quickly each client will grow or when demand will spike.

## How are database resources traditionally provisioned?

Traditionally SaaS providers would either need to over-provision resources to ensure they have capacity for peak demand, which adds cost, or under-provision, which could impact performance during high demand.

## What is a SQL elastic pool?

SQL elastic pools are a resource allocation service used to scale and manage the performance and cost of a group of Azure SQL databases. Elastic pools allow you to purchase resources for the group. You set the amount of resources available to the pool, add databases to the pool, and set minimum and maximum resource limits for the databases within the pool.

The pool resource requirements are set based on the overall needs of the group. The pool allows the databases within the pool to share the allocated resources. SQL elastic pools are used to manage the budget and performance of multiple SQL databases.

## When to use an elastic pool?

SQL elastic pools are ideal when you have several SQL databases that have a low average utilization, but have infrequent, high utilization spikes. In this scenario, you can allocate enough capacity in the pool to manage the spikes for the group, but the total resources can be less than the sum of all of the peak demand of all of the databases. Since the spikes are infrequent, a spike from one database will be unlikely to impact the capacity of the other databases in the pool.

In our fitness company scenario, the individual locations may run promotions at different times of year or see spikes in demand during regional holidays.

### How many databases to add to a pool?

The general guidance is, if the combined resources you would need for individual databases to meet capacity spikes is more than 1.5 times the capacity required for the elastic pool, then the pool will be cost effective.

At a minimum, it is recommended to add at least two S3 databases or fifteen S0 databases to a single pool for it to have potential cost savings.

Depending on the performance tier, you can add up to 100 or 500 databases to a single pool.

## Create an elastic pool

SQL elastic pools must be hosted in a SQL server. You'll specify an existing server or create a new server when creating an elastic pool.

Like many Azure resources, elastic pools can be created from the Azure portal, or the Azure CLI using the `az sql elastic-pools create` command, or via PowerShell using the `New-AzSqlElasticPool` command.

In the next unit, you'll complete an exercise to create a SQL elastic pool.

## Add databases to an elastic pool

Databases can be added using the Azure portal, the Azure CLI, or PowerShell.

When using the portal, you can add a new pool to an existing SQL server. Or you can create a new SQL elastic pool resource and specify the server.

When using the CLI, call `az sql db create` and specify the pool name using the `--elastic-pool-name` parameter. This command can move an existing database into the pool or create a new one if it doesn't exist.

When using PowerShell, you can assign new databases to a pool using `New-AzSqlDatabase` and move existing databases using `Set-AzSqlDatabase`.

You can add existing Azure SQL databases from your Azure SQL server into the pool or create new databases. And you can mix service tiers within the same pool.

# Exercise - Create a SQL elastic pool

In this exercise, we'll create two Azure SQL databases to represent two fitness locations, and then create a SQL elastic pool to manage two databases.

## Create SQL servers

We'll start by creating a SQL server for our fitness databases and we'll add databases for two locations. The steps use Vancouver and Paris but feel free to use other location names.

First declare a few helper variables. SERVERNAME variable will have a random number after the `'-'`. Replace `<location>` with `westus2` and replace `<password>` with `ViVKUwPtAdW2`

```sh
ADMIN_LOGIN="ServerAdmin"
RESOURCE_GROUP=learn-760338ef-317a-41a7-9e0f-8a8b6cb255c9
SERVERNAME=FitnessSQLServer-$RANDOM
LOCATION=westus2
PASSWORD=ViVKUwPtAdW2
```

Create a server named **FitnessSQLServer-nnnn**.

```sh
az sql server create \
--name $SERVERNAME \
--resource-group $RESOURCE_GROUP \
--location $LOCATION \
--admin-user $ADMIN_LOGIN \
--admin-password $PASSWORD
```

This generates output like:

```json
{
  "administratorLogin": "ServerAdmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "fitnesssqlserver-30460.database.windows.net",
  "id": "/subscriptions/eae641a0-d5a6-4cc1-af57-0dfdb436a3aa/resourceGroups/learn-760338ef-317a-41a7-9e0f-8a8b6cb255c9/providers/Microsoft.Sql/servers/fitnesssqlserver-30460",
  "identity": null,
  "kind": "v12.0",
  "location": "westus2",
  "minimalTlsVersion": null,
  "name": "fitnesssqlserver-30460",
  "privateEndpointConnections": [],
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "learn-760338ef-317a-41a7-9e0f-8a8b6cb255c9",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

Add a database named **FitnessVancouverDB** to **FitnessSQLServer-nnnn**.

```sh
az sql db create \
--resource-group $RESOURCE_GROUP \
--server $SERVERNAME \
--name FitnessVancouverDB
```

This will give output like:

```json
{
  "autoPauseDelay": null,
  "catalogCollation": "SQL_Latin1_General_CP1_CI_AS",
  "collation": "SQL_Latin1_General_CP1_CI_AS",
  "createMode": null,
  "creationDate": "2020-07-04T06:15:58.137000+00:00",
  "currentServiceObjectiveName": "GP_Gen5_2",
  "currentSku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "databaseId": "b525f1ce-07a0-474c-89df-d12fa81e520b",
  "defaultSecondaryLocation": "westcentralus",
  "earliestRestoreDate": "2020-07-04T06:45:58.137000+00:00",
  "edition": "GeneralPurpose",
  "elasticPoolId": null,
  "elasticPoolName": null,
  "failoverGroupId": null,
  "id": "/subscriptions/eae641a0-d5a6-4cc1-af57-0dfdb436a3aa/resourceGroups/learn-760338ef-317a-41a7-9e0f-8a8b6cb255c9/providers/Microsoft.Sql/servers/fitnesssqlserver-30460/databases/FitnessVancouverDB",
  "kind": "v12.0,user,vcore",
  "licenseType": "LicenseIncluded",
  "location": "westus2",
  "longTermRetentionBackupResourceId": null,
  "managedBy": null,
  "maxLogSizeBytes": 10307502080,
  "maxSizeBytes": 34359738368,
  "minCapacity": null,
  "name": "FitnessVancouverDB",
  "pausedDate": null,
  "readReplicaCount": 0,
  "readScale": "Disabled",
  "recoverableDatabaseId": null,
  "recoveryServicesRecoveryPointId": null,
  "requestedServiceObjectiveName": "GP_Gen5_2",
  "resourceGroup": "learn-760338ef-317a-41a7-9e0f-8a8b6cb255c9",
  "restorableDroppedDatabaseId": null,
  "restorePointInTime": null,
  "resumedDate": null,
  "sampleName": null,
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sourceDatabaseDeletionDate": null,
  "sourceDatabaseId": null,
  "status": "Online",
  "tags": null,
  "type": "Microsoft.Sql/servers/databases",
  "zoneRedundant": false
}
```

Add a database named **FitnessParisDB** to **FitnessSQLServer-nnnn**.

```sh
az sql db create \
--resource-group $RESOURCE_GROUP \
--server $SERVERNAME \
--name FitnessParisDB
```

This will give output like:

```json
{
  "autoPauseDelay": null,
  "catalogCollation": "SQL_Latin1_General_CP1_CI_AS",
  "collation": "SQL_Latin1_General_CP1_CI_AS",
  "createMode": null,
  "creationDate": "2020-07-04T06:17:55.340000+00:00",
  "currentServiceObjectiveName": "GP_Gen5_2",
  "currentSku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "databaseId": "88ecce41-e31e-4996-a417-5577da2cd791",
  "defaultSecondaryLocation": "westcentralus",
  "earliestRestoreDate": "2020-07-04T06:47:55.340000+00:00",
  "edition": "GeneralPurpose",
  "elasticPoolId": null,
  "elasticPoolName": null,
  "failoverGroupId": null,
  "id": "/subscriptions/eae641a0-d5a6-4cc1-af57-0dfdb436a3aa/resourceGroups/learn-760338ef-317a-41a7-9e0f-8a8b6cb255c9/providers/Microsoft.Sql/servers/fitnesssqlserver-30460/databases/FitnessParisDB",
  "kind": "v12.0,user,vcore",
  "licenseType": "LicenseIncluded",
  "location": "westus2",
  "longTermRetentionBackupResourceId": null,
  "managedBy": null,
  "maxLogSizeBytes": 10307502080,
  "maxSizeBytes": 34359738368,
  "minCapacity": null,
  "name": "FitnessParisDB",
  "pausedDate": null,
  "readReplicaCount": 0,
  "readScale": "Disabled",
  "recoverableDatabaseId": null,
  "recoveryServicesRecoveryPointId": null,
  "requestedServiceObjectiveName": "GP_Gen5_2",
  "resourceGroup": "learn-760338ef-317a-41a7-9e0f-8a8b6cb255c9",
  "restorableDroppedDatabaseId": null,
  "restorePointInTime": null,
  "resumedDate": null,
  "sampleName": null,
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sourceDatabaseDeletionDate": null,
  "sourceDatabaseId": null,
  "status": "Online",
  "tags": null,
  "type": "Microsoft.Sql/servers/databases",
  "zoneRedundant": false
}
```

## Create a SQL elastic pool

We're ready to set up the resources for the SQL elastic pool. We'll switch to the portal to create the elastic pool.

From the Azure portal menu or the **Home** page, select **Create a resource**. Search for and select **SQL Elastic database pool**.

Select the **SQL Elastic database pool** result and then press the **Create** button on the next screen.

Give your new elastic pool a meaningful name such as `FitnessSQLPool`, and ensure the learn-760338ef-317a-41a7-9e0f-8a8b6cb255c9 resource group is selected.

In the **Server** section, ensure your existing **FitnessSQLServer-nnnn** server is selected

In the **Compute + storage** section, click **Configure elastic pool**.

Click **Basic**, and then click **Apply**.

Click **Review + create**. Review your information, and then click **Create**. The SQL elastic pool may take several minutes to provision.

## Add existing databases to the elastic pool

# Manage SQL elastic pools performance and cost

# Exercise - Manage SQL elastic pools

# Knowledge Check

# Summary
