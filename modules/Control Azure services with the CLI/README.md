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

# Work with the Azure CLI

# Exercise - Create an Azure website using the CLI

# Summary
