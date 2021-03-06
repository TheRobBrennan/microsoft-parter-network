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

Microsoft provides several tools that you can use to upload data to your SQL database:

- SQL Server Integration Services (SSIS)
- The SQL `BULK INSERT` statement
- The Bulk Copy Program (bcp) utility

The `bcp` utility is often used because it's convenient and can be easily scripted to import data into multiple tables. The `bcp` utility is a command-line tool that you can use to import and export data from a database. To import data, `bcp` requires these three things:

- The source data to upload
- An existing table in the destination database
- A _format file_ that defines the format of the data and how to map the data to columns in the destination table

The `bcp` utility is flexible. The source data can be in almost any structured format. The format file indicates the layout of the data, whether it's binary or character-based, the type and length of each item, how the data is separated, and so on. The format file also specifies how to map each item in the file to a column in the table. It's important to define the contents of this file correctly. Otherwise, your data might not be imported, or the data might be read into the wrong columns.

Suppose that you have the following data in the file _mydata.csv_, and you want to import this data into the _MyTable_ table that we created earlier.

```csv
Column1,Column2
some text,99
some more text,101
another bit of text,97
yet more text,87
a final bit of text,33
```

The first line contains field names that aren't the same as the columns in the table. The data is comma-separated, and each row is terminated by a newline character. Keep in mind that the order of the columns in the file might be different from the table. In this example, the first column in the table is numeric, and the second column is a string, as follows:

```sql
CREATE TABLE MyTable
(
    MyColumn1 INT NOT NULL PRIMARY KEY,
    MyColumn2 VARCHAR(50) NOT NULL
);
```

You can use the `bcp` command to create a format file for import. The `bcp` command can create a format file based on the schema of the target table in the database. You can then edit the file to match the data in the source file.

Run the following command to create a format file. Replace the items in angle brackets with the values for your database, server, user name, and password:

```sh
bcp <database>.dbo.mytable format nul -c -f mytable.fmt -t, -S <server>.database.windows.net -U <username> -P <password>
```

The `bcp` utility has several parameters that control the functionality of the utility. You can specify:

- The target table (`<database>`.`<schema>`.`<table>`)
- The data to be imported and details about the data (`format nul -c -f mytable.fmt -t,`)
- The connection details for your database (`-S <server>.database.windows.net -U <username> -P <password>`)

For full syntax and command-line parameters for the utility, see the [help documentation](https://docs.microsoft.com/en-us/sql/tools/bcp-utility

The contents of the _mytable.fmt_ format file that's generated by this command look like this:

```
14.0
2
1       SQLCHAR             0       12      ","    1     MyColumn1                                ""
2       SQLCHAR             0       50      "\n"   2     MyColumn2                                SQL_Latin1_General_CP1_CI_AS
```

The first line shows the internal version number of SQL Database. The second line shows the number of columns in the source table. The final two lines indicate how to map data in the source file to those columns.

Both lines start with a number. These are the column numbers in the table. The second field (SQLCHAR) specifies that when we use this format file to import data, each field in the source file contains character data. The `bcp` utility will try to convert this data to the appropriate type for the corresponding column in the table. The next field (12 and 50) is the length of the data in each column in the database. **Don't change this**! The following items ("," and "\n") are the field terminator in the source file and the newline character, respectively. The next column is the field number in the source file. The second-last field (MyColumn1 and MyColumn2) is the name of the column in the database. The last field is the collation to use, which only applies to character data in the database.

Remember that the fields in the source file are in a different order than the columns in the database. So you should edit the format file and change the field numbers, as shown here:

```
14.0
2
1       SQLCHAR             0       12      ","    2     MyColumn1                                ""
2       SQLCHAR             0       50      "\n"   1     MyColumn2                                SQL_Latin1_General_CP1_CI_AS
```

The data in field 2 in the source file will be mapped to the first column in the database. Field 1 will be mapped to the second column.

You can now use the bcp command to import the data, as follows:

```sh
bcp <database>.dbo.mytable in mydata.csv -f mytable.fmt -S <server>.database.windows.net -U <username> -P <password> -F 2
```

The `in` flag specifies that we're using `bcp` to import data. You can use `out` to transfer data from a database to a file. The `-F 2` flag indicates that the import operation should start at line 2 of the source file. Remember that the first line contains headers rather than data.

The command should run successfully and return messages similar to these examples:

```sh
Starting copy...

5 rows copied.
Network packet size (bytes): 4096
Clock Time (ms.) Total     : 46     Average : (108.7 rows per sec.)
```

The important line is "5 rows copied." This reports the number of lines in the source file that contain data that was imported. If this number is different (or 0), your format file may be incorrect.

## Query data

Verify that the import was successful by querying the data. You can use the query editor from the Azure portal. Or you can use the `sqlcmd` utility to connect to the database from a command line. In either case, you run a `SELECT` statement such as this:

```sql
SELECT *
FROM MyTable;
```

You should see the following results.

![https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/2-query-results-annotated.png](https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/2-query-results-annotated.png)

# Exercise - Create tables, bulk import, and query data

The university currently stores their data in a series of comma-delimited files. You need to migrate this data to Azure SQL Database.

In this exercise, you'll create a database server and a single database by using the SQL Database service. Next, you'll create tables and import data into the database. Finally, you'll use the query editor and the `sqlcmd` utility to query the data.

## Examine the existing comma-delimited data

In the Azure Cloud Shell window on the right side of your screen, run the following command to download the data files and application code for the university system.

```sh
git clone https://github.com/MicrosoftDocs/mslearn-develop-app-that-queries-azure-sql education
```

Run these commands to move the sample data to its own folder and list the files in the folder.

```sh
mv ~/education/data ~/educationdata
cd ~/educationdata
ls
```

This folder contains three files: courses.csv, modules.csv, and studyplans.csv.

View the contents of the courses.csv file.

```sh
cat courses.csv
```

This file contains the following comma-separated data. It includes a course name and ID for each course that the university offers.

```sh
ID,Course
1,Computer Science
2,Maths with Computing
3,Maths with Physics
4,Computer Science with Physics
5,Maths with Chemistry
6,Physics with Chemistry
7,Maths
8,Physics
9,Chemistry
```

View the contents of the modules.csv file.

```sh
cat modules.csv
```

This file lists the various modules that students can take to fulfill the requirements of the courses. Each module has an identifying code and a name.

```sh
Module Code,Title
CS101,Introduction to Computer Science
CS102,Java Programming
CS103,Distributed Applications
CS104,Cloud-based systems
MA101,Foundations of Applied Maths
MA102,Advanced Calculus
MA103,Number Theory
MA104,String Theory
PH101,Foundations of Physics
PH102,Basic Experimental Phyics
PH103,Basic Theoretical Physics
PH104,Subatomic Physics
CH101,Elements of Chemistry
CH102,Basic Inorganic Chemistry
CH103,Basic Organic Chemistry
CH104,Chemical Engineering
```

View the contents of the studyplans.csv file.

```sh
cat studyplans.csv
```

This file contains the data that specifies which modules a student must pass to complete a course successfully. The Sequence column shows the order in which the student should take each module. For example, for course 1 (Computer Science) the student must take module CS101 before module MA101. Part of the data is shown here.

```sh
Course ID,Module Code,Sequence
1,CS101,1
1,MA101,2
1,CS102,3
1,CS103,4
1,CS104,5
2,MA101,1
2,MA102,2
2,CS101,3
2,CS102,4
2,CS103,5
3,MA101,1
3,MA102,2
3,PH101,3
3,PH102,4
3,PH103,5
...
```

## Create a database server and database by using SQL Database

Let's create the database and server to store the data for the app.

Sign in to the Azure portal by using the account that you used to activate the sandbox.

From the Azure portal menu, select **Create a resource**.

![https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/2-create-a-resource.png](https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/2-create-a-resource.png)

Select **Databases**, and then select **SQL Database**.

![https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/2-databases-sql-database.png](https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/2-databases-sql-database.png)

On the **Create SQL Database** page, specify the values in the following table for the database properties.

| Property                      | Value                                                                                                                    |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| Subscription                  | Concierge Subscription                                                                                                   |
| Resource Group                | learn-2d6169d9-f68b-44b8-a6cd-c1d7fbd0e6f7                                                                               |
| Database name                 | The database must have a unique name. We suggest using something like `coursedatabase523`, where NNN is a random number. |
| Server                        | Select Create new, and enter the details that are in the following table.                                                |
| Want to use SQL elastic pool? | No                                                                                                                       |
| Compute + storage             | General purpose                                                                                                          |

For the server, specify the following details in the New server pane, and then click Select.

| Property           | Value                                                                              |
| ------------------ | ---------------------------------------------------------------------------------- |
| Server name        | `courseserver523`, where NNN is the same number that you selected for the database |
| Server admin login | `azuresql`                                                                         |
| Password           | `ViVKUwPtAdW2`                                                                     |
| Location           | `Central US`                                                                       |

Select **Next : Networking >**.

On the **Networking** pane, configure the following values:

| Property                                                 | Value           |
| -------------------------------------------------------- | --------------- |
| Connectivity method                                      | Public endpoint |
| Allow Azure services and resources to access this server | Yes             |
| Add current client IP address                            | Yes             |

Select **Review + create**.

Select **Create**, and wait for the server and database to be created before you continue.

## Create the tables

You can now create the tables to store the data from the .csv files.

On the Azure portal menu, select **SQL databases**.

On the **SQL databases** page, select **coursedatabase523**.

On the **coursedatabase523** page, select **Query editor**.

![https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/3-query-editor-annotated.png](https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/3-query-editor-annotated.png)

On the **coursedatabaseNNN - Query editor** page, enter the following details, and then select **OK** to connect to the database service.

| Property           | Value                     |
| ------------------ | ------------------------- |
| Authorization type | SQL server authentication |
| Login              | `azuresql`                |
| Password           | `ViVKUwPtAdW2`            |

In the **Query 1** pane, enter the following SQL statement, and then select **Run**. This statement creates a new table to hold the course information. Verify that the statement runs without any errors.

```sql
CREATE TABLE Courses
(
    CourseID INT NOT NULL PRIMARY KEY,
    CourseName VARCHAR(50) NOT NULL
)
```

![https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/3-create-table-courses-annotated.png](https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/3-create-table-courses-annotated.png)

Overwrite the existing statement with the following statement that creates a table to hold the modules. Select **Run**, and then verify that the statement runs without any errors.

```sql
CREATE TABLE Modules
(
    ModuleCode VARCHAR(5) NOT NULL PRIMARY KEY,
    ModuleTitle VARCHAR(50) NOT NULL
)
```

Change the statement to create a table that's named **StudyPlans**, and then select **Run**.

```sql
CREATE TABLE StudyPlans
(
    CourseID INT NOT NULL,
    ModuleCode VARCHAR(5) NOT NULL,
    ModuleSequence INT NOT NULL,
    PRIMARY KEY(CourseID, ModuleCode)
)
```

In the database window, select the **Refresh** button on the toolbar. Expand **Tables**, and then expand each table in turn. You should see the three tables (**dbo.Courses**, **dbo.Modules**, and **dbo.StudyPlans**), together with the columns and primary key for each table.

NOTE: _dbo_ stands for _database owner_. It's the default schema in the database. All three tables were created in this schema.

![https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/3-tables-and-columns-annotated.png](https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/3-tables-and-columns-annotated.png)

## Import the data

Return to the Cloud Shell window, and make sure that you're in the **educationdata** folder.

```sh
cd ~/educationdata
```

Create the variables that you will use in the later steps. Replace `NNN` with `523` the number that you used for your database and server.

```sh
export DATABASE_NAME=coursedatabase523
export DATABASE_SERVER=courseserver523
export AZURE_USER=azuresql
export AZURE_PASSWORD=ViVKUwPtAdW2
```

Run the `bcp` utility to create a format file from the schema of the **Courses** table in the database. The format file specifies that the data will be in character format (`-c`) and separated by commas (`-t,`).

```sh
bcp "$DATABASE_NAME.dbo.courses" format nul -c -f courses.fmt -t, -S "$DATABASE_SERVER.database.windows.net" -U $AZURE_USER -P $AZURE_PASSWORD
```

In the code editor, open the format file, **courses.fmt**, that was generated by the previous command.

```sh
code courses.fmt
```

The file should look like this:

```
14.0
2
1       SQLCHAR             0       12      ","    1     CourseID                                     ""
2       SQLCHAR             0       50      "\n"   2     CourseName                                   SQL_Latin1_General_CP1_CI_AS
```

Review the file. The data in the first column of the comma-separated file will go into the **CourseID** column of the **Courses** table. The second field will go into the **CourseName** column. The second column is character-based and has a collation that's associated with it. The fields separator in the file is expected to be a comma. The row terminator (after the second field) should be a newline character. In a real-world scenario, your data might not be organized this neatly. You might have different field separators and fields in a different order from the columns. In that situation, you can edit the format file to change these items on a field-by-field basis. Press Ctrl+q to close the editor.

Run the following command to import the data in the **courses.csv** file in the format that's specified by the amended **courses.fmt** file. The `-F 2` flag directs the `bcp` utility to start importing data from line 2 in the data file. The first line contains headers.

```sh
bcp "$DATABASE_NAME.dbo.courses" in courses.csv -f courses.fmt -S "$DATABASE_SERVER.database.windows.net" -U $AZURE_USER -P $AZURE_PASSWORD -F 2
```

Verify that `bcp` utility imports 9 rows and doesn't report any errors.

Run the following sequence of operations to import the data for the **dbo.Modules** table from the **modules.csv** file.

Generate a format file.

```sh
bcp "$DATABASE_NAME.dbo.modules" format nul -c -f modules.fmt -t, -S "$DATABASE_SERVER.database.windows.net" -U $AZURE_USER -P $AZURE_PASSWORD
```

Import the data from the **modules.csv** file into the **Modules** table in the database.

```sh
bcp "$DATABASE_NAME.dbo.modules" in modules.csv -f modules.fmt -S "$DATABASE_SERVER.database.windows.net" -U $AZURE_USER -P $AZURE_PASSWORD -F 2
```

Verify that this command imports 16 rows.

Perform the following sequence of operations to import the data for the **dbo.StudyPlans** table from the **studyplans.csv** file.

Generate a format file.

```sh
bcp "$DATABASE_NAME.dbo.studyplans" format nul -c -f studyplans.fmt -t, -S "$DATABASE_SERVER.database.windows.net" -U $AZURE_USER -P $AZURE_PASSWORD
```

Import the data from the **studyplans.csv** file into the **StudyPlans** table in the database.

```sh
bcp "$DATABASE_NAME.dbo.studyplans" in studyplans.csv -f studyplans.fmt -S "$DATABASE_SERVER.database.windows.net" -U $AZURE_USER -P $AZURE_PASSWORD -F 2
```

Verify that this command imports 45 rows.

## Query the data in the database

Return to the Azure portal.

On the Azure portal menu, select **SQL databases**.

On the **SQL databases** page, select **coursedatabaseNNN**.

On the **coursedatabaseNNN** page, under **Overview**, select _Query editor_.

On the **coursedatabaseNNN - Query editor** page, enter the following details, and then select **OK** to connect to the database service.

| Property           | Value                     |
| ------------------ | ------------------------- |
| Authorization type | SQL server authentication |
| Login              | `azuresql`                |
| Password           | `ViVKUwPtAdW2`            |

In the **Query 1** pane, enter the following SQL statement, and then select **Run**.

```sql
SELECT * FROM dbo.Courses
```

This statement retrieves the data from the **Courses** table. The results window should display nine rows.

![https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/3-query-results-annotated.png](https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/3-query-results-annotated.png)

Change the query as follows, and then select **Run**.

```sql
SELECT * FROM dbo.Modules
```

This time you should see the modules in the **Results** window. There are 16 rows.

Switch back to Cloud Shell, and run the following command to connect to the database.

```sh
sqlcmd -S "$DATABASE_SERVER.database.windows.net" -d "$DATABASE_NAME" -U $AZURE_USER -P $AZURE_PASSWORD
```

At the `1>` prompt, enter the following SQL command to fetch the data from the **StudyPlans** table.

```sql
SELECT * FROM StudyPlans;
GO
```

This query should return 45 rows.

At the `1>` prompt, type `exit` to close the `sqlcmd` utility.

You created a single database by using SQL Database. Next, you used the query editor in the Azure portal to create tables. You then used the `bcp` utility to upload data from a series of comma-delimited data files. Finally, you ran queries against the tables in the database from the query editor in the Azure portal and from the `sqlcmd` utility in Cloud Shell.

# Connect an ASP.NET application to Azure SQL Database

There are various ways to connect to databases within the Azure SQL Database service from an application. For .NET apps, you can use the `System.Data.SqlClient` library.

The web app for the university must fetch and display the data that you uploaded to your SQL database. In this unit, you will learn how to connect to a database from a web app and use the `System.Data.SqlClient` library to process data.

## System.Data.SqlClient library overview

The `System.Data.SqlClient` library is a collection of types and methods that you can use to connect to a SQL Server database that's running on-premises or in the cloud on SQL Database. The library provides a generalized interface for retrieving and maintaining data. You can use the `System.Data.SqlClient` library to run SQL commands and transactional operations and to retrieve data. You can parameterize these operations to avoid problems that are associated with SQL-injection attacks. If an operation fails, the `System.Data.SqlClient` library provides error information through specialized exception and error classes. You handle these exceptions just like any other type of exception in a .NET application.

The `System.Data.SqlClient` library is available in the _System.Data.SqlClient_ NuGet package.

## Connect to a single database

You use an `SqlConnection` object to create a database connection. You provide a connection string that specifies the name and location of the database, the credentials to use, and other connection-related parameters. A typical connection string to a single database looks like this:

```
Server=tcp:myserver.database.windows.net,1433;Initial Catalog=mydatabase;Persist Security Info=False;User ID=myusername;Password=mypassword;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

You can find the connection string for your single database on the **Connection strings** page for your database in the Azure portal.

The following code example shows how to create an `SqlConnection` object:

```csharp
using System.Data.SqlClient;

...

string connectionString = "Server=tcp:myserver.database.windows.net,...";
SqlConnection con = new SqlConnection(connectionString);
```

The database connection isn't established until you open the connection. You typically open the connection immediately before you run an SQL command or query.

```csharp
con.Open();
```

Some databases only support a finite number of concurrent connections. So, after you finish running a command and retrieving any results, it's good practice to close the connection and release any resources that were held.

```csharp
con.Close();
```

Another common approach is to create the connection in a using statement. This strategy automatically closes the connection when the using statement completes. But you can also explicitly call the `Close` method.

```csharp
using (SqlConnection con = new SqlConnection(connectionString))
{
    // Open and Use the connection here
    con.Open();
    ...
}
// Connection is now closed
```

## Define an SQL command or query

Create an `SqlCommand` object to specify an SQL command or query to run. The following example shows an SQL **DELETE** statement that removes rows for a given customer from an **Orders** table. You can parameterize commands. This example uses a parameter that's named _CustID_ for the **CustomerID** value. The line that sets the `CommandType` property of the `SqlCommand` object to `Text` indicates that the command is an SQL statement. You can also run a stored procedure rather than an SQL statement. In that case, you set the `CommandType` to `StoredProcedure`.

```csharp
SqlCommand deleteOrdersForCustomer = new SqlCommand("DELETE FROM Orders WHERE CustomerID = @custID", con);
deleteOrdersForCustomer.CommandType = CommandType.Text;
string customerID = <prompt the user for a customer to delete>;
deleteOrdersForCustomer.Parameters.Add(new SqlParameter("custID", customerID));
```

The final parameter to the `SqlCommand` constructor in this example is the connection that's used to run the command.

The next example shows a query that joins the **Customers** and **Orders** tables together to produce a list of customer names and their orders.

```csharp
SqlCommand queryCmd = new SqlCommand(
                    @"SELECT c.FirstName, c.LastName, o.OrderID
                      FROM Customers c JOIN Orders o
                      ON c.CustomerID = o.CustomerID", con);
queryCmd.CommandType = CommandType.Text;
```

## Run a command

If your `SqlCommand` object references an SQL statement that doesn't return a result set, run the command by using the `ExecuteNonQuery` method. If the command succeeds, it returns the number of rows that are affected by the operation. The next example shows how to run the **deleteOrdersForCustomer** command that was shown earlier.

```csharp
int numDeleted = deleteOrdersForCustomer.ExecuteNonQuery();
```

If you expect the command to take a while to run, you can use the `ExecuteNonQueryAsync` method to perform the operation asynchronously.

## Execute a query and fetch data

If your `SqlCommand` contains an SQL SELECT statement, you run it by using the `ExecuteReader` method. This method returns an `SqlDataReader` object that you can use to iterate through the results and process each row in turn. You retrieve the data from an `SqlReader` object by using the `Read` method. This method returns _true_ if a row is found and _false_ if there are no more rows left to read. After a row is read, the data for that row is available in the fields in the `SqlReader` object. Each field has the same name as the corresponding column in the original SELECT statement. However, the data in each field is retrieved as an untyped `object`, so you must convert it to the appropriate type before you can use it. The following code shows how to run the `queryCmd` command that we illustrated earlier to fetch the data one row at a time.

```csharp
SqlDataReader rdr = queryCmd.ExecuteReader();

// Read the data a row at a time
while (rdr.Read())
{
    string firstName = rdr["FirstName"].ToString();
    string lastName = rdr["LastName"].ToString();
    int orderID = Convert.ToInt32(rdr["OrderID"]);

    // Process the data
    ...
}
```

## Handle exceptions and errors

Exceptions and errors can occur for various reasons when you're using a database. For example, you might try to access a table that no longer exists. You can catch SQL errors by using the `SqlException` type.

An exception might be triggered by various events or problems in the database. An `SqlException` object has a property `Errors` that contains a collection of `SqlError` objects. These objects provide the details for each error. The following example shows how to catch an `SqlException` and process the errors that it contains.

```csharp
...
using (SqlConnection con = new SqlConnection(connectionString))
{
    SqlCommand command = new SqlCommand("DELETE FROM ...", con);
    try
    {
        con.Open();
        command.ExecuteNonQuery();
    }
    catch (SqlException ex)
    {
        for (int i = 0; i < ex.Errors.Count; i++)
        {
            Console.WriteLine($"Index # {i} Error: {ex.Errors[i].ToString()}");
        }
    }
}
```

# Exercise - Connect an ASP.NET application to Azure SQL Database

Your database is created. Now you will configure and deploy a web application that academic advisors can use to discuss courses and plans of study with students. The app will use the `System.Data.SqlClient` library to retrieve and display the details of courses and modules that a student must pass to complete a course.

To save time, you'll work with a pre-existing web application. You'll add the code that connects this app to your database. The following diagram shows the primary components of this app:

![https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/5-diagram.svg](https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/5-diagram.svg)

You'll do the following things:

- Create a _class_ that holds the course name, module title, and sequence for each module in the database.
- Create a _data access controller class_ that retrieves the information from the database.
- Edit the code that's behind the index page in the web app to create a _data access controller object_ and fetch the data.
- Edit the index page to display the data.

## Deploy and run the pre-existing web app

Change your working directory to the **education** folder.

```sh
cd ~/education
```

Run the following commands to build and deploy the initial web app.

```sh
WEBAPPNAME=educationapp-$RANDOM
az webapp up \
    --resource-group learn-2d6169d9-f68b-44b8-a6cd-c1d7fbd0e6f7 \
    --location centralus \
    --sku F1 \
    --name $WEBAPPNAME
```

When the web application has been deployed, the output will show an _App_url_ with the URL of the web site - [https://educationapp-6606.azurewebsites.net](https://educationapp-6606.azurewebsites.net). Open this site in a new tab.

```sh
You can launch the app at http://educationapp-6606.azurewebsites.net
{
  "URL": "http://educationapp-6606.azurewebsites.net",
  "appserviceplan": "rob_asp_Windows_centralus_0",
  "location": "centralus",
  "name": "educationapp-6606",
  "os": "Windows",
  "resourcegroup": "learn-2d6169d9-f68b-44b8-a6cd-c1d7fbd0e6f7",
  "runtime_version": "dotnetcore|2.1",
  "runtime_version_detected": "2.1",
  "sku": "FREE",
  "src_path": "//home//rob//education"
}

```

![https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/5-web-app-no-data.png](https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/5-web-app-no-data.png)

You want the web app to display a list of courses and the modules that make up each course. Currently, the app doesn't retrieve or display this data. So, you need to update the code to get the data from the database and display it.

## Add code to the web app to retrieve data

Now let's add to the application the code to retrieve course data from the database.

In Cloud Shell, go to the **education/Models** folder.

```sh
cd ~/education/Models
```

This folder contains two files, **CoursesAndModules.cs** and **DataAccessController.cs**.

Use the code editor to open the **CoursesAndModules.cs** file.

```sh
code CoursesAndModules.cs
```

This file contains an empty class that's named `CoursesAndModules`.

```csharp
namespace CoursesWebApp.Models
{
    public class CoursesAndModules
    {
        // TODO: Define the CourseName, ModuleTitle, and Sequence read-only properties

        // TODO: Create a constructor that initializes the fields behind the properties
    }
}
```

Replace the comment `// TODO: Define the CourseName, ModuleTitle, and Sequence read-only properties` with the following code.

```csharp
public string CourseName { get; }
public string ModuleTitle { get; }
public int Sequence { get; }
```

This code defines a set of read-only fields that will contain the data for each row that's displayed by the web app.

Replace the comment /`/ TODO: Create a constructor that initializes the fields behind the properties` with the following constructor.

```csharp
public CoursesAndModules(string courseName, string moduleTitle, int sequence)
{
    this.CourseName = courseName;
    this.ModuleTitle = moduleTitle;
    this.Sequence = sequence;
}
```

This constructor populates the fields with the data to display. The complete file should contain the following code.

```csharp
namespace CoursesWebApp.Models
{
    public class CoursesAndModules
    {
        public string CourseName { get; }
        public string ModuleTitle { get; }
        public int Sequence { get; }

        public CoursesAndModules(string courseName, string moduleTitle, int sequence)
        {
            this.CourseName = courseName;
            this.ModuleTitle = moduleTitle;
            this.Sequence = sequence;
        }
    }
}
```

Save the file, and close the code editor.

Use the code editor to open the **DataAccessController.cs** file.

```sh
code DataAccessController.cs
```

This file contains a class that's named `DataAccessController`. This class will contain the data access logic to connect to the database and retrieve the course and module data. It will populate a list of `CoursesAndModules` objects with this data.

Leave the code editor open, and switch to the Azure portal.

On the Azure portal menu, select **SQL databases**, and select your database.

Under **Settings**, select **Connection strings**. Copy the **ADO.NET** connection string to the clipboard.

![https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/5-connection-string-annotated.png](https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/5-connection-string-annotated.png)

```
Server=tcp:courseserver523.database.windows.net,1433;Initial Catalog=coursedatabase523;Persist Security Info=False;User ID=azuresql;Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Return to the code editor. Replace the value of the `connectionString` variable with the value from the clipboard. In the connection string, replace the text {your_username} with the value `azuresql`. And replace the text {your_password} with the password `ViVKUwPtAdW2` for this account.

```csharp
private string connectionString = "Server=tcp:courseserver523.database.windows.net,1433;Initial Catalog=coursedatabase523;Persist Security Info=False;User ID=azuresql;Password=ViVKUwPtAdW2;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;";
```

After the comment `//TODO: Connect to the database`, replace the commented-out using statement with the following code.

```csharp
using (SqlConnection con = new SqlConnection(connectionString))
```

This code creates a new `SqlConnection` object that uses your connection string to connect to the database.

Replace the comment `// TODO: Specify the SQL query to run` with the following statements.

```csharp
SqlCommand cmd = new SqlCommand(
    @"SELECT c.CourseName, m.ModuleTitle, s.ModuleSequence
    FROM dbo.Courses c JOIN dbo.StudyPlans s
    ON c.CourseID = s.CourseID
    JOIN dbo.Modules m
    ON m.ModuleCode = s.ModuleCode
    ORDER BY c.CourseName, s.ModuleSequence", con);
cmd.CommandType = CommandType.Text;
```

The `SqlCommand` object contains an SQL statement that retrieves the data for all courses and modules. It joins them by using the information in the **StudyPlan** table.

Replace the comment `// TODO: Execute the query` with the following code.

```csharp
con.Open();
SqlDataReader rdr = cmd.ExecuteReader();
```

These statements open the connection to the database and run the SQL statement. You can use the `SqlDataReader` object to fetch the results one row at a time.

Replace the comment `// TODO: Read the data a row at a time` with the following block of code.

```csharp
while (rdr.Read())
{
    string courseName = rdr["CourseName"].ToString();
    string moduleTitle = rdr["ModuleTitle"].ToString();
    int moduleSequence = Convert.ToInt32(rdr["ModuleSequence"]);
    CoursesAndModules course = new CoursesAndModules(courseName, moduleTitle, moduleSequence);
    courseList.Add(course);
}
```

This block iterates through the rows that are returned in the `SqlDataReader` object. The code extracts the data in the fields in each row and uses them to populate a new `CoursesAndModules` object. This object is then added to a list.

Replace the comment `// TODO: Close the database connection` with the following statement.

```csharp
con.Close();
```

This statement closes the connection to the database and releases any resources that were held.

The completed class should contain the following code, which includes the connection string for your database.

```csharp
using Microsoft.Extensions.Options;
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Linq;
using System.Threading.Tasks;

namespace CoursesWebApp.Models
{
    public class DataAccessController
    {
        // TODO: Add your connection string in the following statements
        private string connectionString = "Server=tcp:courseserver523.database.windows.net,1433;Initial Catalog=coursedatabase523;Persist Security Info=False;User ID=azuresql;Password=ViVKUwPtAdW2;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;";

        // Retrieve all details of courses and their modules
        public IEnumerable<CoursesAndModules> GetAllCoursesAndModules()
        {
            List<CoursesAndModules> courseList = new List<CoursesAndModules>();

            // TODO: Connect to the database
            using (SqlConnection con = new SqlConnection(connectionString))
            {
                // TODO: Specify the SQL query to run
                SqlCommand cmd = new SqlCommand(
                    @"SELECT c.CourseName, m.ModuleTitle, s.ModuleSequence
                    FROM dbo.Courses c JOIN dbo.StudyPlans s
                    ON c.CourseID = s.CourseID
                    JOIN dbo.Modules m
                    ON m.ModuleCode = s.ModuleCode
                    ORDER BY c.CourseName, s.ModuleSequence", con);
                cmd.CommandType = CommandType.Text;

                // TODO: Execute the query
                con.Open();
                SqlDataReader rdr = cmd.ExecuteReader();

                // TODO: Read the data a row at a time
                while (rdr.Read())
                {
                    string courseName = rdr["CourseName"].ToString();
                    string moduleTitle = rdr["ModuleTitle"].ToString();
                    int moduleSequence = Convert.ToInt32(rdr["ModuleSequence"]);
                    CoursesAndModules course = new CoursesAndModules(courseName, moduleTitle, moduleSequence);
                    courseList.Add(course);
                }

                // TODO: Close the database connection
                con.Close();
            }
            return courseList;
        }
    }
}
```

Save the file, and close the **Code** editor.

## Add code to the web app to display the data

The application can now retrieve the course data. Now, update the app to display the data to the user.

In Cloud Shell, move to the **education/Pages** folder.

```sh
cd ~/education/Pages
```

This folder contains the .cshtml pages and code files that the web app uses to display information.

Use the code editor to open the **Index.cshtml.cs** file.

```sh
code Index.cshtml.cs
```

This file contains code that the index page runs when it's displayed. The code defines a class `CoursesAndModulesModel`. The index page will use this model to display the details of courses and modules. In this file, you need to add the code that uses a `DataAccessController` object to fetch that data.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using CoursesWebApp.Models;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace CoursesWebApp.Pages
{
    public class CoursesAndModulesModel : PageModel
    {
        // TODO: Create a DataAccessController object

        // TODO: Create a collection for holding CoursesAndModules object

        public void OnGet()
        {
            // TODO: Retrieve the data using the DataAccessController object and populate the CoursesAndModules object
        }
    }
}
```

In **Index.cshtml.cs**, replace the comment `// TODO: Create a DataAccessController object` with the following code to create a new `DataAccessController` object.

```csharp
DataAccessController dac = new DataAccessController();
```

Replace the comment `// TODO: Create a collection for holding CoursesAndModules object` with the following code.

```csharp
public List<CoursesAndModules> CoursesAndModules;
```

In the `OnGet` method, replace the comment `// TODO: Retrieve the data using the DataAccessController object` and populate the `CoursesAndModules` object with the following code. This code uses the `DataAcessController` object to populate the list with the data from the database.

```csharp
CoursesAndModules = dac.GetAllCoursesAndModules().ToList();
```

The completed file should contain the following code. Save the file, and close the code editor.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using CoursesWebApp.Models;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace CoursesWebApp.Pages
{
    public class CoursesAndModulesModel : PageModel
    {
        // TODO: Create a DataAccessController object
        DataAccessController dac = new DataAccessController();

        // TODO: Create a collection for holding CoursesAndModules object
        public List<CoursesAndModules> CoursesAndModules;

        public void OnGet()
        {
            // TODO: Retrieve the data using the DataAccessController object and populate the CoursesAndModules object
            CoursesAndModules = dac.GetAllCoursesAndModules().ToList();
        }
    }
}
```

Use the code editor to open the file **Index.cshtml**.

```sh
code Index.cshtml
```

This file contains the display logic for the index page. It specifies `CoursesAndModulesModel` as the data source. The code that we've added creates and populates this model.

The page uses HTML data to display the data from the model. Currently, the page just displays the table headings. The table body (<tbody>) is empty.

```html
<h2>Courses and Modules</h2>
<div>
  <table class="table">
    <thead>
      <tr>
        <th>
          Course Name
        </th>
        <th>
          Modules
        </th>
        <th>
          Sequence
        </th>
      </tr>
    </thead>
    <tbody>
      <!-- TODO: Display the data from the CoursesAndModules collection -->
    </tbody>
  </table>
</div>
```

Replace the comment <!-- TODO: Display the data from the CoursesAndModules collection --> with the following markup.

```html
@foreach(var courseAndModule in Model.CoursesAndModules) {
<tr>
  <td>
    @Html.DisplayFor(courseName => courseAndModule.CourseName)
  </td>
  <td>
    @Html.DisplayFor(moduleTitle => courseAndModule.ModuleTitle)
  </td>
  <td>
    @Html.DisplayFor(sequence => courseAndModule.Sequence)
  </td>
</tr>
}
```

This code iterates through the rows in the model and outputs the data in each field.

The completed **Index.cshtml** file should contain the following code.

```html
@page @model CoursesAndModulesModel @{ ViewData["Title"] = "Home page"; }

<h2>Courses and Modules</h2>
<div>
  <table class="table">
    <thead>
      <tr>
        <th>
          Course Name
        </th>
        <th>
          Modules
        </th>
        <th>
          Sequence
        </th>
      </tr>
    </thead>
    <tbody>
      @foreach(var courseAndModule in Model.CoursesAndModules) {
      <tr>
        <td>
          @Html.DisplayFor(courseName => courseAndModule.CourseName)
        </td>
        <td>
          @Html.DisplayFor(moduleTitle => courseAndModule.ModuleTitle)
        </td>
        <td>
          @Html.DisplayFor(sequence => courseAndModule.Sequence)
        </td>
      </tr>
      }
    </tbody>
  </table>
</div>
```

Save the file, and close the code editor.

## Deploy and test the updated web app

With the application fully configured to retrieve and display the course data to the user, you can deploy the updated version.

In Cloud Shell, return to the **education** folder.

```sh
cd ~/education
```

Run the following commands to build and deploy the updated web app.

```sh
WEBAPPNAME=educationapp-6606

az webapp up \
    --resource-group learn-2d6169d9-f68b-44b8-a6cd-c1d7fbd0e6f7 \
    --name $WEBAPPNAME
```

After the new web app is deployed, select the link for the app. It should now display a list of courses and modules with the data that's stored in the database.

![https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/5-web-app-with-data.png](https://docs.microsoft.com/en-us/learn/modules/develop-app-that-queries-azure-sql/media/5-web-app-with-data.png)

# Summary

You helped the university switch their application from storing data in raw text files to instead using Azure SQL Database. You set up the database and schema, imported data, and implemented code to query the data and display it to users. Now you know how to create, manipulate, and load a single SQL database. And you can create an ASP.NET app to query the database.

## Learn more

- [Tutorial: Design a relational database in a single database within Azure SQL Database using SSMS](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-design-first-database)
- [Quickstart: Use .NET Core (C#) to query an Azure SQL database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-connect-query-dotnet-core)
- [Tutorial: Build an ASP.NET app in Azure with SQL Database](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-dotnet-sqldatabase)
- [Choose among the vCore service tiers and migrate from the DTU service tiers](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-service-tiers-vcore)
- [Service tiers in the DTU-based purchase model](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-service-tiers-dtu)
