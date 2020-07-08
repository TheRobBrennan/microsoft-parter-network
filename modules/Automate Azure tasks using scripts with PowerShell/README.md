[Automate Azure tasks using scripts with PowerShell](https://docs.microsoft.com/en-us/learn/modules/automate-azure-tasks-with-powershell/) (~1 hr 11 mins)

# Introduction

Creating administration scripts is a powerful way to optimize your work flow. You can automate common, repetitive tasks, and once a script has been verified, it will run consistently, likely reducing errors.

Suppose you work at a company that uses Azure Virtual Machines (VMs) to test your Customer Relationship Management (CRM) software. The VMs are built from images that include a web front-end, a web service that implements business logic, and a SQL database.

You have been executing multiple rounds of tests on a single VM but noticed that changes in the database and configuration files can cause inconsistent results. In one case, a bug created a phone call record with no corresponding customer in the database. The orphaned record caused subsequent integration tests to fail even after the bug was fixed. You plan to solve this problem by using a fresh VM deployment for each testing cycle. You want to automate the VM creation setup because it will be executed many times per week.
Here, you will see how to manage Azure resources using Azure PowerShell. You will use Azure PowerShell interactively for one-off tasks and write scripts to automate repeated tasks.

## Learning objectives

In this module, you will:

- Decide if Azure PowerShell is the right tool for your Azure administration tasks
- Install Azure PowerShell on Linux, macOS, and/or Windows
- Connect to an Azure subscription using Azure PowerShell
- Create Azure resources using Azure PowerShell

## Prerequisites

- Experience with a command-line interface such as PowerShell or Bash
- Knowledge of basic Azure concepts such as resource groups and Virtual Machines
- Experience administering Azure resources using the Azure portal

# Decide if Azure PowerShell is right for your tasks

Suppose you need to choose a tool to administer the Azure resources used to test your Customer Relationship Management (CRM) system. Your tests require you to create resource groups and provision virtual machines (VMs).

You want something that is easy for administrators to learn, but powerful enough to automate the installation and setup of multiple virtual machines or script a full application environment. There are multiple tools available; you need to find the best one for your people and your tasks.

## What tools are available?

Azure provides three administration tools to choose from:

- The Azure portal
- The Azure CLI
- Azure PowerShell

They all offer approximately the same amount of control; any task that you can do with one of the tools, you can likely do with the other two. All three are cross-platform, running on Windows, macOS, and Linux. They differ in syntax, setup requirements, and whether they support automation.

Here, we will describe each of the three options and give some guidance on how to decide among them.

## What is the Azure portal?

The Azure portal is a website that lets you create, configure, and alter the resources in your Azure subscription. The portal is a Graphical User Interface (GUI) that makes it convenient to locate the resource you need and execute any required changes. It also guides you through complex administrative tasks by providing wizards and tooltips.

The portal does not provide any way to automate repetitive tasks. For example, to set up 15 VMs, you would need to create them one-by-one by completing the wizard for each VM. This can be time-consuming and error-prone for complex tasks.

## What is the Azure CLI?

The Azure CLI is a cross-platform command-line program to connect to Azure and execute administrative commands on Azure resources. For example, to create a VM, you would use a command like the following:

```sh
az vm create \
  --resource-group CrmTestingResourceGroup \
  --name CrmUnitTests \
  --image UbuntuLTS
  ...
```

The Azure CLI is available two ways: inside a browser via the Azure Cloud Shell or with a local install on Linux, Mac, or Windows. In both cases, it can be used interactively or scripted. For interactive use, you first launch a shell such as `cmd.exe` on Windows or Bash on Linux or macOS and then issue the command at the shell prompt. To automate repetitive tasks, you assemble the commands into a shell script using the script syntax of your chosen shell and then execute the script.

## What is Azure PowerShell?

Azure PowerShell is a module that you add to Windows PowerShell or PowerShell Core to let you connect to your Azure subscription and manage resources. Azure PowerShell requires PowerShell to function. PowerShell provides services like the shell window, command parsing, and so on. Azure PowerShell adds the Azure-specific commands.

For example, Azure PowerShell provides the **New-AzVM** command that creates a virtual machine for you inside your Azure subscription. To use it, you would launch the PowerShell application and then issue a command like the following:

```sh
New-AzVm `
    -ResourceGroupName "CrmTestingResourceGroup" `
    -Name "CrmUnitTests" `
    -Image "UbuntuLTS"
    ...
```

Azure PowerShell is also available two ways: inside a browser via the Azure Cloud Shell or with a local install on Linux, Mac, or Windows. In both cases, you have two modes to choose from. You can use it in interactive mode, in which you manually issue one command at a time, or in scripting mode, where you execute a script that consists of multiple commands.

## How to Choose an administrative tool

There is approximate parity between the portal, the Azure CLI, and Azure PowerShell with respect to the Azure objects they can administer and the configurations they can create. They are also all cross-platform. This means you will typically consider several other factors when making your choice:

- **Automation**: Do you need to automate a set of complex or repetitive tasks? Azure PowerShell and the Azure CLI support this while the portal does not.
- **Learning curve**: Do you need to complete a task quickly without learning new commands or syntax? The Azure portal does not require you to learn syntax or memorize commands. In Azure PowerShell and the Azure CLI, you must know the detailed syntax for each command you use.
- **Team skillset**: Does your team have existing expertise? For example, your team may have used PowerShell to administer Windows. If so, they will quickly become comfortable using Azure PowerShell.

## Example

Recall that you are choosing an administrative tool to create the test environments for your CRM application. Your administrators have two specific Azure tasks they will need to do:

- Create one resource group for each category of testing (unit, integration, and acceptance).
- Create multiple VMs in each resource group before every round of testing.

To create the resource groups, the Azure portal is a reasonable choice. These are one-off tasks, so you don't need scripts to do them.

Finding the best tool to create the VMs is a more challenging decision. You need to create several of them and you need to do it repeatedly, likely several times each week. This means you will want automation, so the Azure portal is not a good choice. In this case, either Azure PowerShell or the Azure CLI will meet your needs. If your team members have some existing PowerShell knowledge, Azure PowerShell will likely be the best match. Azure PowerShell is available on the operating systems your admin team uses, it supports automation, and should be quick for your team to learn.

Most administrators' first experience with Azure is in the Portal. It's a great place to start as it provides a clean, well-structured graphical interface but provided limited options for automation. When you need automation, Azure gives you two options: Azure PowerShell for admins with PowerShell experience and the Azure CLI for everyone else.

In practice, businesses typically have a mix of one-off and repetitive tasks. This means it is common to use both the Portal and a scripting solution. In our CRM example, it's appropriate to create the resource groups via the Portal and automate the VM creation with PowerShell.
The rest of this module focuses on installing and using Azure PowerShell.

# Install PowerShell

Suppose you have chosen Azure PowerShell as your automation solution. Your administrators prefer to run their scripts locally rather than in the Azure Cloud Shell. The team uses machines that run Linux, macOS, and Windows. You need to get Azure PowerShell working on all their devices.

## What must be installed?

We'll go through the actual installation instructions in the next unit, but let's look at the two components which make up Azure PowerShell.

- The **base PowerShell product** This comes in two variants: PowerShell on Windows, and PowerShell Core on macOS and Linux.
- The **Azure PowerShell module** This extra module must be installed to add the Azure-specific commands to PowerShell.

PowerShell is included with Windows (but might have an update available). You will need to install PowerShell Core on Linux and macOS.

Once the base product is installed, you then add the Azure PowerShell module to your installation.

## How to install PowerShell Core

On both Linux and macOS, you use a package manager to install PowerShell Core. The recommended package manager differs by OS and distribution.

PowerShell Core is available in the Microsoft repository, so you'll first need to add that repository to your package manager.

### Linux

On Linux, the package manager will change based on the Linux distribution you choose. See [https://docs.microsoft.com/en-us/learn/modules/automate-azure-tasks-with-powershell/3-install-azure-powershell](https://docs.microsoft.com/en-us/learn/modules/automate-azure-tasks-with-powershell/3-install-azure-powershell) for the appropriate package manager for your Linux distribution.

### Mac

On macOS, you will use `Homebrew` to install PowerShell Core.

In the next section, you will go through the detailed installation steps for some common platforms.

# Exercise - Install Azure PowerShell

# Create an Azure Resource using scripts in Azure PowerShell

# Exercise - Create an Azure Resource using scripts in Azure PowerShell

# Create and save scripts in Azure PowerShell

# Exercise - Create and save scripts in Azure PowerShell

# Summary
