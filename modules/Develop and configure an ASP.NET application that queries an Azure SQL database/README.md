[Develop and configure an ASP.NET application that queries an Azure SQL database](https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/) (~50 mins)

# Introduction

Azure SQL Database is a service that you use to create a relational database in the cloud that's compatible with Microsoft SQL Server. SQL Database is highly scalable. It can support many thousands of concurrent requests. In SQL Database, you can define tables and then insert, update, delete, and query data.

Imagine that you work as a developer for a university. The university is creating a web application that academic advisors will use to discuss courses and plans of study with students. The development team wants to use SQL Database to store their data, which is currently in comma-delimited text files. They're planning to create a .NET Core app for advisors to use initially. They need to know how to create and manipulate a database and how to connect an ASP.NET application to query the database.

You'll create a single database by using the SQL Database service. You'll see how to import data into the database and query this data. Finally, you'll configure a web application to connect to and query the database.

SQL Database currently supports three deployment options: single, elastic pool, and managed instance. We'll focus on the single-database deployment option.

## Learning objectives

In this module, you will:

- Create, configure, and populate a single database in SQL Database
- Configure an ASP.NET application to query the database

## Prerequisites

- Basic familiarity with relational databases
- Basic knowledge of C#

# Create tables, bulk import, and query data

The university needs a location to store data that's currently in text files. They want to make the data relational to improve the ability to access it. They've selected a single database in Azure SQL Database as the storage service for this data. Let's take a look at SQL Database and see how to upload and query data.

## Create a single database by using the Azure portal

SQL Database is a relational database service that's based on the latest stable version of Microsoft SQL Server Database Engine. SQL Database is an easy-to-use, high-performance database that's reliable and highly secure. You can use SQL Database to build new apps, websites, and microservices in the programming language of your choice, and you won't have to manage infrastructure.

You can create a single database through the Azure portal or by using Azure PowerShell or CLI.

From the Azure portal menu, select **Create a resource**.

![https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/2-create-a-resource.png](https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/2-create-a-resource.png)

Select **Databases**, and then select **SQL Database**

![https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/2-databases-sql-database.png](https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/2-databases-sql-database.png)

To use CLI, run the `az sql server create` and `az sql db create` commands.

To use PowerShell, run the `New-AzSqlServer` and `New-AzSqlDatabase` commands.

When you create a single database, you're prompted to specify the server to manage it. You can create a new server or use an existing server.

When you create a new server, you're prompted to specify a server admin user name and password. You'll use these credentials to connect to the server to do administrative tasks and to access the databases that the server controls. SQL Database also supports Azure Active Directory (Azure AD) authentication. But you must always create an admin account when you create a new server and then grant access to accounts that are stored in Azure AD.

Each database server is protected by a firewall to block potentially malicious processes. You can open the firewall to other Azure services. And you can selectively enable access to other computers based on their IP address or address range. SQL Database also provides advanced data security that enables you to:

- Specify the sensitivity of data in individual columns in tables.
- Assess the vulnerability of your databases and take necessary remediation steps.
- Send alerts when a threat is detected.

You provision resources by using the virtual core (vCore) model, which specifies the resources (memory, I/O, and CPU) to allocate. You can scale the compute and storage resources independently. Or, you can assign resources in terms of database transaction units (DTUs). A DTU is a measure of the calibrated cost of the resources that you need to perform a benchmarked transaction.

If you have multiple databases and the resource needs of the databases fluctuate, you can use SQL elastic pool. This feature enables sharing a pool of resources among pooled databases as demand requires.

When you create a database, you also specify how the data will be collated. A _collation_ defines the rules that the database uses for sorting and comparing data. It also specifies the character set to use for text data. You can change the collation after you've created the database, but it's best not to change it after the database contains data.

## Create tables

You can use any of these tools to create tables:

- The query editor in the Azure portal
- The `sqlcmd` utility and Cloud Shell
- SQL Server Management Studio

Whichever tool you choose, you define the table by using the `CREATE TABLE` SQL command. SQL Database supports primary keys, foreign keys, indexes, and triggers on tables. The following sample code creates a pair of related tables and a non-clustered index. You can run these commands as a batch in the query editor or in the `sqlcmd` utility.

```sql
CREATE TABLE MyTable
(
    MyColumn1 INT NOT NULL PRIMARY KEY,
    MyColumn2 VARCHAR(50) NOT NULL
);

CREATE TABLE MyTable2
(
    AnotherColumn1 INT NOT NULL,
    AnotherColumn2 INT NOT NULL REFERENCES MyTable,
    AnotherColumn3 VARCHAR(50) NULL,
    PRIMARY KEY (AnotherColumn1, AnotherColumn2)
);

CREATE INDEX cci ON MyTable2(AnotherColumn3);
```

To access the query editor in the Azure portal, go to the page for your database and select **Query editor**. You'll be prompted for credentials. You can set the **Authorization type** to **SQL Server authentication** and enter the user name and password that you set up when you created the database. Or you can select **Active Directory password authentication** and provide the credentials of an authorized user in Azure AD. If Active Directory single sign-on is enabled, you can connect by using your Azure identity.

![https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/2-sign-in-annotated.png](https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/2-sign-in-annotated.png)

You enter your SQL code in the query pane and then click Run to execute it. If the SQL statement is a query, any rows that are returned appear in the Results pane. The Messages pane displays information like the number of rows returned or any errors that occurred:

![https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/2-query-editor-annotated.png](https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/2-query-editor-annotated.png)

To use the `sqlcmd` utility, go to Cloud Shell and run the following command. Replace `<server>` with the name of the database server that you created, `<database>` with the name of your database, and `<user name>` and `<password>` with your credentials.

```sh
sqlcmd -S <server>.database.windows.net -d <database> -U <username> -P <password>
```

If the sign-in command succeeds, you'll see a `1>` prompt. You can enter SQL commands on several lines and then type `GO` to run them.

## Bulk import data with bcp

```sh

```

```sh

```

```sh

```

```sh

```

```sh

```

```sh

```

```sh

```

## Query data

```sh

```

# Exercise - Create tables, bulk import, and query data

# Connect an ASP.NET application to Azure SQL Database

# Exercise - Connect an ASP.NET application to Azure SQL Database

# Summary
