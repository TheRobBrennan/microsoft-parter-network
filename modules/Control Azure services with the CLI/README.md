[Control Azure services with the CLI](https://docs.microsoft.com/en-us/learn/modules/control-azure-services-with-cli/) (~34 mins)

# Introduction

The Azure portal is great for performing single tasks, and to see a quick overview of the state of your resources. But for tasks that need to be repeated daily, or even hourly, using the command line and a set of tested commands or scripts can help get your work done more quickly and avoid errors.

Suppose you work at a company that develops Azure Web Apps. These are applications hosted in Azure, with all the benefits of automatically configured security, load balancing, management, and so on. You're currently testing a web app that generates sales forecasts, based on a range of inputs from different databases and other data sources. Your developers use Windows, Linux, and Mac computers, and use a GitHub repository for daily builds of the applications.

As part of the testing, you want to compare app performance for different data sources, and for different types of data connections. You've noticed that when your development team uses the Azure portal to create a new test instance of the app, they don't always use exactly the same parameters. You plan to solve this problem by using a set of standard deployment commands for each app test, which can be automated if required, and which will work in the same way across all the computers used by your software team.

In this module, you'll see how to manage Azure resources using the Azure CLI.

## Learning objectives

In this module, you will:

- Install the Azure CLI on Linux, macOS, and/or Windows
- Connect to an Azure subscription using the Azure CLI
- Create Azure resources using the Azure CLI

## Prerequisites

- Experience with a command-line interface, such as PowerShell or Bash
- Knowledge of basic Azure concepts, such as resource groups
- Experience administering Azure resources using the Azure portal

# What is the Azure CLI?

The Azure CLI is a command-line program to connect to Azure and execute administrative commands on Azure resources. It runs on Linux, macOS, and Windows and allows administrators and developers to execute their commands through a terminal or command-line prompt (or script!) instead of a web browser. For example, to restart a virtual machine (VM), you would use a command like the following:

```sh
az vm restart -g MyResourceGroup -n MyVm
```

The Azure CLI provides cross-platform command-line tools for managing Azure resources, and can be installed locally on Linux, Mac, or Windows computers. The Azure CLI can also be used from a browser through the Azure Cloud Shell. In both cases, it can be used interactively or scripted. For interactive use, you first launch a shell such as cmd.exe on Windows or Bash on Linux or macOS and then issue the command at the shell prompt. To automate repetitive tasks, you assemble the CLI commands into a shell script using the script syntax of your chosen shell and then execute the script.

## How to install the Azure CLI

On both Linux and macOS, you use a package manager to install the Azure CLI. The recommended package manager differs by OS and distribution:

- Linux: **apt-get** on Ubuntu, **yum** on Red Hat, and **zypper** on OpenSUSE
- Mac: **Homebrew**

The Azure CLI is available in the Microsoft repository, so you'll first need to add that repository to your package manager.

- On Windows, you install the Azure CLI by downloading and running an MSI file.

## Using the Azure CLI in scripts

If you want to use the Azure CLI commands in scripts, you need to be aware of any issues around the "shell" or environment used for running the script. For example, in a bash shell, the following syntax is used when setting variables:

```sh
variable="value"
variable=integer
```

If you use a PowerShell environment for running Azure CLI scripts, you'll need to use this syntax for variables:

```sh
$variable="value"
$variable=integer
```

The Azure CLI must be installed before it can be used to manage Azure resources from a local computer. The installation steps vary for Windows, Linux, and macOS, but once installed, the commands are common across platforms.

# Exercise - Install and run the Azure CLI

Let's install the Azure CLI on your local machine, and then run a command to verify your installation. The method you use for installing the Azure CLI depends on the operating system of your computer. Choose the steps for your operating system.

NOTE: This exercise guides you through installing the Azure CLI tool locally. The remainder of the module will use the Azure Cloud Shell so you can leverage the free subscription support in Microsoft Learn. You can consider this exercise as an optional activity and just review the instructions if you prefer.

## macOS

Here you will install the Azure CLI on macOS using the Homebrew package manager.

If the **brew** command is unavailable, you may need to install the Homebrew package manager. For details see the [Homebrew website](https://brew.sh/).

Update your brew repository to make sure you get the latest Azure CLI package.

```sh
brew update
```

Install the Azure CLI.

```sh
brew install azure-cli
```

## Running the Azure CLI

You run the Azure CLI by opening a bash shell (Linux and macOS), or from the command prompt or PowerShell (Windows).

Start the Azure CLI and verify your installation by running the version check.

```sh
az --version
```

Sample output:

```sh
azure-cli                          2.8.0

command-modules-nspkg              2.0.3
core                               2.8.0
nspkg                              3.0.4
telemetry                          1.0.4

Python location '/usr/local/Cellar/azure-cli/2.8.0/libexec/bin/python'
Extensions directory '/Users/rob/.azure/cliextensions'

Python (Darwin) 3.8.3 (default, Jul  7 2020, 13:04:12)
[Clang 11.0.3 (clang-1103.0.32.62)]

Legal docs and information: aka.ms/AzureCliLegal


Your CLI is up-to-date.

Please let us know how we are doing: https://aka.ms/azureclihats
and let us know if you're interested in trying out our newest features: https://aka.ms/CLIUXstudy
```

# Work with the Azure CLI

The Azure CLI lets you type commands and execute them immediately from the command line. Recall that the overall goal in the software development example is to deploy new builds of a web app for testing. Let's talk about the sorts of tasks you can do with the Azure CLI.

## What Azure resources can be managed using the Azure CLI?

The Azure CLI lets you control nearly every aspect of every Azure resource. You can work with resource groups, storage, virtual machines, Azure Active Directory (Azure AD), containers, machine learning, and so on.

Commands in the CLI are structured in _groups_ and _subgroups_. Each group represents a service provided by Azure, and the subgroups divide commands for these services into logical groupings. For example, the `storage` group contains subgroups including **account**, **blob**, and **queue**.

So, how do you find the particular commands you need? One way is to use `az find`, the AI robot that uses the Azure documentation to tell you more about commands, the CLI and more.

Example - find the most popular commands related to the word blob.

```sh
az find blob
```

Example - Show me the most popular commands for an Azure CLI command group, such as `az vm`.

```sh
az find "az vm"
```

Example - Show me the most popular parameters and subcommands for an Azure CLI command.

```sh
az find "az vm create"
```

If you already know the name of the command you want, the `--help` argument for that command will get you more detailed information on the command, and for a command group, a list of the available subcommands. So, with our storage example, here's how you can get a list of the subgroups and commands for managing blob storage:

```sh
az storage blob --help
```

## How to create an Azure resource

When creating a new Azure resource, there are typically three steps: connect to your Azure subscription, create the resource, and verify that creation was successful. The following illustration shows a high-level overview of the process.

![https://docs.microsoft.com/en-us/learn/modules/control-azure-services-with-cli/media/4-create-resources-overview.png](https://docs.microsoft.com/en-us/learn/modules/control-azure-services-with-cli/media/4-create-resources-overview.png)

Each step corresponds to a different Azure CLI command.

### Connect

Since you're working with a local install of the Azure CLI, you'll need to authenticate before you can execute Azure commands, by using the Azure CLI **login** command.

```sh
az login
```

The Azure CLI will typically launch your default browser to open the Azure sign-in page. If this doesn't work, follow the command-line instructions and enter an authorization code at [https://aka.ms/devicelogin](https://aka.ms/devicelogin).

After a successful sign in, you'll be connected to your Azure subscription.

### Create

You'll often need to create a new resource group before you create a new Azure service, so we'll use resource groups as an example to show how to create Azure resources from the CLI.

The Azure CLI **group create** command creates a resource group. You must specify a name and location. The name must be unique within your subscription. The location determines where the metadata for your resource group will be stored. You use strings like "West US", "North Europe", or "West India" to specify the location; alternatively, you can use single word equivalents, such as westus, northeurope, or westindia. The core syntax is:

```sh
az group create --name <name> --location <location>
```

IMPORTANT: You do not need to create a resource group when using the free Azure sandbox. Instead, you will use a pre-created resource group.

### Verify

For many Azure resources, the Azure CLI provides a **list** subcommand to view resource details. For example, the Azure CLI **group list** command lists your Azure resource groups. This is useful here to verify whether creation of the resource group was successful:

```sh
az group list
```

To get a more concise view, you can format the output as a simple table:

```sh
az group list --output table
```

# Exercise - Create an Azure website using the CLI

Next, let's use the Azure CLI to create a resource group, and then to deploy a web app into this resource group.

## Using a resource group

When you're working with your own machine and Azure subscription, you'll need to first sign in to Azure using the `az login` command. However, signing in is unnecessary when you are using the browser-based Cloud Shell environment.

Next, you would normally create a resource group for all your related Azure resources with an `az group create` command, but for this exercise the following resource group has been created for you: **learn-1c517341-e13e-4262-a546-0ff623d299ed**.

Your first step in this exercise will be to create several variables that you will use in later commands.

```sh
export RESOURCE_GROUP=learn-1c517341-e13e-4262-a546-0ff623d299ed
export AZURE_REGION=centralus
export AZURE_APP_PLAN=popupappplan-$RANDOM
export AZURE_WEB_APP=popupwebapp-$RANDOM
```

You can ask the Azure CLI to list all your resource groups in a table. There should just be one while you are in the free Azure sandbox.

```sh
az group list --output table

# Output
Name                                        Location    Status
------------------------------------------  ----------  ---------
learn-1c517341-e13e-4262-a546-0ff623d299ed  westus      Succeeded
```

As you do more Azure development, you can end up with several resource groups. If you have several items in the group list, you can filter the return values by adding a `--query` option. Try the following command:

```sh
az group list --query "[?name == '$RESOURCE_GROUP']"

# Output
[
  {
    "id": "/subscriptions/e21efc6c-c28e-4d99-bfdb-ec8e6d22cac5/resourceGroups/learn-1c517341-e13e-4262-a546-0ff623d299ed",
    "location": "westus",
    "managedBy": null,
    "name": "learn-1c517341-e13e-4262-a546-0ff623d299ed",
    "properties": {
      "provisioningState": "Succeeded"
    },
    "tags": {
      "x-created-by": "freelearning",
      "x-created-for": "1c83738b-6805-4d18-8922-a144892c839b",
      "x-module-id": "learn.control-azure-services-with-cli"
    },
    "type": "Microsoft.Resources/resourceGroups"
  }
]
```

The query is formatted using **JMESPath**, which is a standard query language for JSON requests. You can learn more about this powerful filter language at [http://jmespath.org/](http://jmespath.org/). We also cover queries in more depth in the **Manage VMs with the Azure CLI** module.

## Steps to create a service plan

When you run Web Apps using the Azure App Service, you pay for the Azure compute resources that are used by the app, and the resource costs depend on the App Service plan associated with your Web Apps. Service plans determine the region used for the app datacenter, number of VMs used, and pricing tier.

Create an App Service plan to run your app. The following command specifies the free pricing tier, but you can run `az appservice plan create --help` to see the other pricing tiers.

NOTE: The **name of the app and plan must be unique in all of Azure**. The variables that you created earlier will assign random values as suffixes to make sure they're unique. However, if you receive an error when you are creating any resources, you should run the commands listed earlier to reset all of the variables with new random values.

If you receive an error about the resource group, run the commands listed earlier with a different resource group value.

```sh
az appservice plan create --name $AZURE_APP_PLAN --resource-group $RESOURCE_GROUP --location $AZURE_REGION --sku FREE
```

This command can take several minutes to complete.

Verify that the service plan was created successfully by listing all your plans in a table.

```sh
az appservice plan list --output table
```

You'll see a response like the following example.

```sh
Kind    Location    MaximumNumberOfWorkers    Name                NumberOfSites    ResourceGroup                               Status
------  ----------  ------------------------  ------------------  ---------------  ------------------------------------------  --------
app     Central US  3                         popupappplan-54321  0                Learn-12345678-1234-1234-1234-123456789abc  Ready
```

## Steps to create a web app

Next, you'll create the web app in your service plan. You can deploy the code at the same time, but for our example, we'll create the web app and deploy the code as separate steps.

To create the web app, you'll supply web app name and the name of the app plan you created above. Just like the app plan name, the web app name must be unique, and the variables that you created earlier will assign random values that should be sufficient for this exercise.

```sh
az webapp create --name $AZURE_WEB_APP --resource-group $RESOURCE_GROUP --plan $AZURE_APP_PLAN
```

Verify that the app was created successfully by listing all your apps in a table.

```sh
az webapp list --output table
```

You'll see a response like the following example.

```sh
Name               Location    State    ResourceGroup                               DefaultHostName                      AppServicePlan
-----------------  ----------  -------  ------------------------------------------  -----------------------------------  ------------------
popupwebapp-12345  Central US  Running  Learn-12345678-1234-1234-1234-123456789abc  popupwebapp-12345.azurewebsites.net  popupappplan-54321
```

Make a note of the **DefaultHostName** listed in the table; this address is the URL for the new website. Azure will make your website available through the unique app name in the `azurewebsites.net` domain. For example, if my app name was "popupwebapp-mslearn123", then my website URL would be: http://popupwebapp-mslearn123.azurewebsites.net.

Your site has a "quickstart" page created by Azure that you can see either in a browser, or with CURL, just use the **DefaultHostName**:

```sh
curl $AZURE_WEB_APP.azurewebsites.net
```

## Steps to deploy code from GitHub

The final step is to deploy code from a GitHub repository to the web app. Let's use a simple PHP page available in the Azure Samples GitHub repository that displays "Hello World!" when it executes. Make sure to use the web app name you created.

(!) This is a great example to manually deploy a GitHub project to Azure (!)

```sh
az webapp deployment source config --name $AZURE_WEB_APP --resource-group $RESOURCE_GROUP --repo-url "https://github.com/Azure-Samples/php-docs-hello-world" --branch master --manual-integration
```

Once it's deployed, hit your site again with a browser or CURL.

```sh
curl $AZURE_WEB_APP.azurewebsites.net
```

The page displays "Hello World!"

```sh
Hello World!
```

This exercise demonstrated a typical pattern for an interactive Azure CLI session. You first used a standard command to create a new resource group. You then used a set of commands to deploy a resource (in this example, a web app) into this resource group. This set of commands could easily be combined into a shell script, and executed every time you need to create the same resource.

# Summary

The Azure CLI is a good choice for anyone new to Azure command line and scripting. Its simple syntax and cross-platform compatibility help reduce the risk of errors when performing regular and repetitive tasks. In this module, you used the Azure CLI commands to create a resource group, and deploy a web app by using a small set of commands. These commands could be combined into a shell script as part of automation solution.

## Check your knowledge

1. What do you need to install on your machine to let you execute Azure CLI commands locally?

[] The Azure cloud shell
[] The Azure CLI and Azure PowerShell
[ x ] Only the Azure CLI

2. True or false: The Azure CLI can be installed on Linux, macOS, and Windows, and the CLI commands you use are the same in all platforms.

[ x ] True
[] False

3. Which parameter can you add to most CLI commands to get concise, formatted output?

[] list
[ x ] table
[] group
