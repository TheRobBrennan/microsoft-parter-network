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

In this unit, you will install PowerShell on your local machine.

NOTE: This exercise guides you through installing the PowerShell tools locally. The remainder of the module will use the Azure Cloud Shell so you can leverage the free subscription support in Microsoft Learn. You can consider this exercise as an optional activity and just review the instructions if you prefer.

## MacOS

On macOS, the first step is to install **PowerShell Core**. This is done using the Homebrew package manager.

IMPORTANT: If the brew command is unavailable, you may need to install the Homebrew package manager. For details see the [Homebrew website](https://brew.sh/).

Install Homebrew-Cask to obtain more packages, including the PowerShell Core package:

```sh
brew tap homebrew/cask
```

Install PowerShell Core:

```sh
brew cask install powershell
```

Start PowerShell Core to verify that it installed successfully:

```sh
pwsh
```

You have setup your local machine(s) to support PowerShell. Next, we will talk about additional commands you can add including the Azure module.

# Create an Azure Resource using scripts in Azure PowerShell

PowerShell lets you write commands and execute them immediately. This is known as **interactive mode**.

Recall that the overall goal in the Customer Relationship Management (CRM) example is to create three test environments containing Virtual Machines. You will use resource groups to ensure the VMs are organized into separate environments: one for unit testing, one for integration testing, and one for acceptance testing. You only need to create the resource groups once, which means using the interactive mode of PowerShell is a good choice.

When you type a command into PowerShell, it matches it to a _cmdlet_ which then performs the requested action. We're going to look at some of the common commands you can use, and then look at installing the Azure support for PowerShell.

## What are PowerShell cmdlets?

A PowerShell command is called a **cmdlet** (pronounced "command-let"). A cmdlet is a command that manipulates a single feature. The term **cmdlet** is intended to imply "small command". By convention, cmdlet authors are encouraged to keep cmdlets simple and single-purpose.

The base PowerShell product ships with cmdlets that work with features such as sessions and background jobs. You add modules to your PowerShell installation to get cmdlets that manipulate other features. For example, there are third-party modules to work with ftp, administer your operating system, access the file system, and so on.

**Cmdlets follow a verb-noun naming convention**; for example, **Get-Process**, **Format-Table**, and **Start-Service**. There is also a convention for verb choice: "**get**" to retrieve data, "**set**" to insert or update data, "**format**" to format data, "**out**" to direct output to a destination, and so on.

Cmdlet authors are encouraged to include a help file for each cmdlet. The **Get-Help** cmdlet displays the help file for any cmdlet. For example, we could get help on the `Get-ChildItem` cmdlet with the following statement:

```sh
Get-Help Get-ChildItem -detailed
```

## What is a PowerShell module?

Cmdlets are shipped in _modules_. A PowerShell Module is a DLL that includes the code to process each available cmdlet. You load cmdlets into PowerShell by loading the module they are contained in. You can get a list of loaded modules using the `Get-Module` command:

```sh
Get-Module
```

This will output something like:

```sh
PS /Users/rob/repos/microsoft-parter-network> Get-Module

ModuleType Version    PreRelease Name                                ExportedCommands
---------- -------    ---------- ----                                ----------------
Script     2.0.0                 PSReadLine                          {Get-PSReadLineKeyHandler, Get-PSReadLineOption, Remove-PSReadLineKeyHandler, S…
```

## What is the Az module?

**Az** is the formal name for the Azure PowerShell module containing cmdlets to work with Azure features. It contains hundreds of cmdlets that let you control nearly every aspect of every Azure resource. You can work with resource groups, storage, virtual machines, Azure Active Directory, containers, machine learning, and so on. This module is an open source component [available on GitHub](https://github.com/Azure/azure-powershell).

NOTE: You may have seen or used Azure PowerShell commands that used a `-AzureRM` format. In October 2018 we announced the replacement of the **AzureRM** module with the **Az** module. This new module has several features, notably a shortened cmdlet noun prefix of `-Az` instead of `-AzureRM`. The **Az** module ships with backwards compatibility with the **AzureRM** module so the `-AzureRM` cmdlet format will work, but you should transition to the **Az** module and use the `-Az` commands going forward.

### Install the Az module

The Az module is available from a global repository called the PowerShell Gallery. You can install the module onto your local machine through the `Install-Module` command. You need an elevated PowerShell shell to install modules from the PowerShell Gallery.

We use the same commands to install the Azure PowerShell on either Linux or macOS.

In a terminal, type the following command to launch PowerShell Core with elevated privileges.

```sh
sudo pwsh
```

Run the following command at the PowerShell Core prompt to install Azure PowerShell.

```sh
Install-Module Az -AllowClobber
```

If you are asked whether you trust modules from **PSGallery**, answer **Yes** or **Yes to All**.

### Update a module

If you get a warning or error message indicating that a version of the Azure PowerShell module is already installed, you can update to the latest version by issuing the command:

```sh
Update-Module -Name Az
```

As with the `Install-Module` command, answer **Yes** or **Yes to All** when prompted to trust the module. You can also use the `Update-Module` command to re-install a module if you are having trouble with it.

## Example: How to create a resource group with Azure PowerShell

Once you have the Azure module loaded, you can begin working with Azure. Let's do a common task - create a Resource Group. As you know, we use resource groups to administer related resources together. Creating a new resource group is one of the first tasks you'll do when starting a new Azure solution.

There are four steps we need to perform:

- Import the Azure cmdlets.
- Connect to your Azure subscription.
- Create the resource group.
- Verify that creation was successful (see below).

The following illustration shows an overview of these steps.

![https://docs.microsoft.com/en-us/learn/modules/automate-azure-tasks-with-powershell/media/5-create-resource-overview.png](https://docs.microsoft.com/en-us/learn/modules/automate-azure-tasks-with-powershell/media/5-create-resource-overview.png)

Each step corresponds to a different cmdlet.

### Import the Azure cmdlets

At startup, PowerShell loads only the core cmdlets by default. This means the cmdlets you need to work with Azure won't be loaded. The most reliable way to load the cmdlets you need is to import them manually at the start of your PowerShell session.

You use the **Import-Module** cmdlet to load modules. This cmdlet has many parameters to handle a variety of situations. For example, it can load multiple modules, a specific module version, part of a module, and so on.

For example, we can load all the cmdlets for Az with the following command **in an elevated PowerShell session**:

```sh
Import-Module Az
```

TIP: If you find that you work with Azure PowerShell frequently, there are two ways you can automate the module-loading process. You can add an entry to your PowerShell profile to import the Azure module at startup or use the latest versions of PowerShell, which loads the containing module automatically when you use a cmdlet.

### Connect

When you are working with a local install of Azure PowerShell, you will need to authenticate before you can execute Azure commands. The **Connect-AzAccount** cmdlet prompts for your Azure credentials and then connects to your Azure subscription. It has many optional parameters, but if all you need is an interactive prompt, no parameters are needed:

```sh
Connect-AzAccount
```

You'll need to repeat these steps for every new PowerShell session you start since this module is not part of the core set.

### Working with subscriptions

If you are new to Azure, you probably only have a single subscription. But if you have been using Azure for a while, you may have created multiple Azure subscriptions. You can configure Azure PowerShell to execute commands against a particular subscription.

You can only be in one subscription at a time. Use the `Get-AzContext` cmdlet to determine which subscription is active. If it's not the correct one, you can change it.

- Get a list of all subscription names in your account with the `Get-AzSubscription` command.
- Change the subscription by passing the name of the one to select.

```sh
Select-AzSubscription -Subscription "Visual Studio Enterprise"
```

### Get a list of all Resource Groups

You can retrieve a list of all Resource Groups in the active subscription:

```sh
Get-AzResourceGroup
```

To get a more concise view, you can send the output from the `Get-AzResourceGroup` to the `Format-Table` cmdlet using a pipe '|'.

```sh
Get-AzResourceGroup | Format-Table
```

This will output something like:

```sh
ResourceGroupName                  Location       ProvisioningState Tags TagsTable ResourceId
-----------------                  --------       ----------------- ---- --------- ----------
cloud-shell-storage-southcentralus southcentralus Succeeded                        /subscriptions/xxxxxxxx-d3ce-4172...
ExerciseResources                  eastus         Succeeded                        /subscriptions/xxxxxxxx-d3ce-4172...
```

### Create a Resource Group

As you know, when you are creating resources in Azure, you will always place them into a resource group for management purposes. A resource group is often one of the first things you will create when starting a new application.

You can create resource groups with the `New-AzResourceGroup` cmdlet. You must specify a name and location. **The name must be unique within your subscription**. The location determines where the metadata for your resource group will be stored (which may be important to you for compliance reasons). You use strings like "West US", "North Europe", or "West India" to specify the location. As with most of the Azure cmdlets, `New-AzResourceGroup` has many optional parameters; however, the core syntax is:

```sh
New-AzResourceGroup -Name <name> -Location <location>
```

NOTE: Remember, we will be working in the Azure sandbox which creates the Resource Group for you. The above command would be used if you work in your own subscription.

### Verify the resources

The `Get-AzResource` lists your Azure resources. This is useful here to verify whether creation of the resource group was successful.

```sh
Get-AzResource
```

Like the `Get-AzResourceGroup` command, you can get a more concise view through the `Format-Table` cmdlet. Here we will use a shorthand version `ft`:

```sh
Get-AzResource | ft
```

You can also filter it to specific resource groups to only list resources associated with that group:

```sh
Get-AzResource -ResourceGroupName ExerciseResources
```

### Creating an Azure Virtual Machine

Another common task that could be done with PowerShell is to create VMs.

Azure PowerShell provides the `New-AzVm` cmdlet to create a virtual machine. The cmdlet has many parameters to let it handle the large number of VM configuration settings. Most of the parameters have reasonable default values so we only need to specify five things:

- **ResourceGroupName**: The resource group into which the new VM will be placed.
- **Name**: The name of the VM in Azure.
- **Location**: Geographic location where the VM will be provisioned.
- **Credential**: An object containing the username and password for the VM admin account. We will use the Get-Credential cmdlet. This cmdlet will prompt for a username and password and package it into a credential object.
- **Image**: The operating system image to use for the VM. This is often a Linux distribution, or Windows Server.

```sh
New-AzVm
       -ResourceGroupName <resource group name>
       -Name <machine name>
       -Credential <credentials object>
       -Location <location>
       -Image <image name>
```

You can supply these parameters directly to the cmdlet as shown above. Alternatively, other cmdlets can be used to configure the virtual machine, such as `Set-AzVMOperatingSystem`, `Set-AzVMSourceImage`, `Add-AzVMNetworkInterface`, and `Set-AzVMOSDisk`.

Here's an example that strings the `Get-Credential` cmdlet together with the `-Credential` parameter:

```sh
New-AzVM -Name MyVm -ResourceGroupName ExerciseResources -Credential (Get-Credential) ...
```

The AzVM suffix is specific to VM-based commands in PowerShell. There are several others you can use:

- `Remove-AzVM` - Deletes an Azure VM.
- `Start-AzVM` - Start a stopped VM.
- `Stop-AzVM` - Stop a running VM.
- `Restart-AzVM` - Restart a VM.
- `Update-AzVM` - Updates the configuration for a VM.

#### Example: getting the information for a VM

You can list the VMs in your subscription with the `Get-AzVM -Status` command. This can also specify a VM with the `-Name` property. Here we assign it to a PowerShell variable:

```sh
$vm = Get-AzVM  -Name MyVM -ResourceGroupName ExerciseResources
```

The interesting thing is this is an object you can interact with. For example, you can take that object, make changes and then push changes back to Azure with the `Update-AzVM` command:

```sh
$ResourceGroupName = "ExerciseResources"
$vm = Get-AzVM  -Name MyVM -ResourceGroupName $ResourceGroupName
$vm.HardwareProfile.vmSize = "Standard_DS3_v2"

Update-AzVM -ResourceGroupName $ResourceGroupName  -VM $vm
```

PowerShell's interactive mode is appropriate for one-off tasks. In our example, we'll likely use the same resource group for the lifetime of the project, which means creating it interactively is reasonable. Interactive mode is often quicker and easier for this task than writing a script and executing that script exactly once.

# Exercise - Create an Azure Resource using scripts in Azure PowerShell

Recall our original scenario - creating VMs to test our CRM software. When a new build is available, we want to spin up a new VM so we can test the full install experience from a clean image. Then when we are finished, we want to delete the VM.

Let's try the commands you would use to create a VM.

## Create a Linux VM with Azure PowerShell

Since we are using the Azure sandbox, you won't have to create a Resource Group. Instead, use the Resource Group **learn-dde4d5aa-1327-4dbd-86fb-fb4d02537708**. In addition, be aware of the location restrictions.

Let's create a new Azure VM with PowerShell.

Use the `New-AzVm` cmdlet to create a VM.

- Use the Resource Group **learn-dde4d5aa-1327-4dbd-86fb-fb4d02537708**.
- Give the VM a name - typically you want to use something meaningful that identifies the purposes of the VM, location, and (if there is more than one) instance number. We'll use "testvm-wus2-01" for "Test VM in West US 2, instance 1". Come up with your own name based on where you place the VM. - `testvm-wus2-01`
- Select a location close to you from the following list available in the Azure sandbox - `westus2`
- Use "UbuntuLTS" for the image - this is Ubuntu Linux.
- Use the `Get-Credential` cmdlet and feed the results into the `Credential` parameter. - `ViVKUwPtAdW2`
- Add the `-OpenPorts` parameter and pass "22" as the port - this will let us SSH into the machine.

```sh
New-AzVm -ResourceGroupName learn-dde4d5aa-1327-4dbd-86fb-fb4d02537708 -Name "testvm-wus2-01" -Credential (Get-Credential) -Location "West US 2" -Image UbuntuLTS -OpenPorts 22
```

Output:

```sh
ResourceGroupName        : learn-dde4d5aa-1327-4dbd-86fb-fb4d02537708Id                       : /subscriptions/c19e4c79-7623-4b63-990c-698ba45a4d44/resourceGroups/learn-dde4d5aa-1327-4dbd-86fb-fb4d02537708/providers/Microsoft.Compute/virtualMachines/testvm-wus2-01VmId                     : 18ea9716-a944-4546-8b93-06e2e27ca4d0Name                     : testvm-wus2-01Type                     : Microsoft.Compute/virtualMachinesLocation                 : westus2Tags                     : {}HardwareProfile          : {VmSize}NetworkProfile           : {NetworkInterfaces}OSProfile                : {ComputerName, AdminUsername,LinuxConfiguration, Secrets, AllowExtensionOperations,RequireGuestProvisionSignal}ProvisioningState        : SucceededStorageProfile           : {ImageReference, OsDisk, DataDisks}FullyQualifiedDomainName : testvm-wus2-01-02b79d.West US
2.cloudapp.azure.com
```

This will take a few minutes to complete. Once it does, you can query it and assign the VM object to a variable (\$vm).

```sh
$vm = Get-AzVM -Name "testvm-wus2-01" -ResourceGroupName learn-dde4d5aa-1327-4dbd-86fb-fb4d02537708
```

Then query the value to dump out the information about the VM:

```sh
$vm
```

You should see something like:

```sh
ResourceGroupName : learn-dde4d5aa-1327-4dbd-86fb-fb4d02537708
Id                : /subscriptions/c19e4c79-7623-4b63-990c-698ba45a4d44/res
ourceGroups/learn-dde4d5aa-1327-4dbd-86fb-fb4d02537708/providers/Microsoft.
Compute/virtualMachines/testvm-wus2-01
VmId              : 18ea9716-a944-4546-8b93-06e2e27ca4d0
Name              : testvm-wus2-01
Type              : Microsoft.Compute/virtualMachines
Location          : westus2
Tags              : {}
HardwareProfile   : {VmSize}
NetworkProfile    : {NetworkInterfaces}
OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration,
Secrets, AllowExtensionOperations, RequireGuestProvisionSignal}
ProvisioningState : Succeeded
StorageProfile    : {ImageReference, OsDisk, DataDisks}
```

You can reach into complex objects through a dot (".") syntax. For example, to see the properties in the `VMSize` object associated with the HardwareProfile section you can type:

```sh
$vm.HardwareProfile
```

Or to get information on one of the disks:

```sh
$vm.StorageProfile.OsDisk
```

You can even pass the VM object into other cmdlets. For example, this will retrieve the public IP address of your VM:

```sh
$vm | Get-AzPublicIpAddress
```

With the IP address, you can connect to the VM with SSH. For example, if you used the username "bob", and the IP address is "205.22.16.5", then this command would connect to the Linux machine:

```sh
ssh bob@205.22.16.5
```

## Delete a VM

Just to try out some more commands, let's delete the VM. We'll shut it down first.

```sh
Stop-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroupName
```

Now, let's delete the VM with the `Remove-AzVM` cmdlet:

```sh
Remove-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroupName
```

Try this command to list all the resources in your resource group:

```sh
Get-AzResource -ResourceGroupName $vm.ResourceGroupName | ft
```

You should see a bunch of resources (disks, virtual networks, etc.) that all still exist.

This is because the `Remove-AzVM` command _just deletes the VM_. It doesn't cleanup any of the other resources! At this point, we'd likely just delete the Resource Group itself and be done with it. However, let's just run through the exercise to clean it up manually. You should see a pattern in the commands.

Delete the Network Interface.

```sh
$vm | Remove-AzNetworkInterface –Force
```

Delete the managed OS disks and storage account

```sh
Get-AzDisk -ResourceGroupName $vm.ResourceGroupName -DiskName $vm.StorageProfile.OSDisk.Name | Remove-AzDisk -Force
```

Next, delete the virtual network.

```sh
Get-AzVirtualNetwork -ResourceGroup $vm.ResourceGroupName | Remove-AzVirtualNetwork -Force
```

Delete the network security group.

```sh
Get-AzNetworkSecurityGroup -ResourceGroup $vm.ResourceGroupName | Remove-AzNetworkSecurityGroup -Force
```

And finally, the public IP address.

```sh
Get-AzPublicIpAddress -ResourceGroup $vm.ResourceGroupName | Remove-AzPublicIpAddress -Force
```

We should have caught all the created resources; check the resource group just to be sure.

```sh
Get-AzResource -ResourceGroupName $vm.ResourceGroupName | ft
```

Sample output:

```sh
Name                 ResourceGroupName                          ResourceTyp
                                                                e
----                 -----------------                          -----------
cloudshell1386646635 learn-dde4d5aa-1327-4dbd-86fb-fb4d02537708 Microsoft.…
```

We did a lot of manual commands here but a better approach would have been to write a script so we could reuse this logic later to create or delete a VM. Let's look at scripting with PowerShell.

# Create and save scripts in Azure PowerShell

Complex or repetitive tasks often take a great deal of administrative time. Organizations prefer to automate these tasks to reduce costs and avoid errors.

This is important in the Customer Relationship Management (CRM) company example. There, you test your software on multiple Linux Virtual Machines (VMs) that you need to continuously delete and recreate. You want to use a PowerShell script to automate the creation of the VMs vs. creating them manually each time like we just did.

Beyond the core operation of creating a VM you have a few additional requirements for your script.

- You will create multiple VMs, so you want to put the creation inside a loop
- You need to create VMs in three different resource groups, so the name of the resource group should be passed to the script as a parameter

In this section, you will see how to write and execute an Azure PowerShell script that meets these requirements.

## What is a PowerShell script?

A PowerShell script is a text file containing commands and control constructs. The commands are invocations of cmdlets. The control constructs are programming features like loops, variables, parameters, comments, etc. supplied by PowerShell.

PowerShell script files have a **.ps1** file extension. You can create and save these files with any text editor.

TIP: If you’re writing PowerShell scripts under Windows, you can use the Windows PowerShell Integrated Scripting Environment (ISE). This editor provides features such as syntax coloring and a list of available cmdlets.

The following screenshot shows the Windows PowerShell Integrated Scripting Environment (ISE) with a sample script to connect to Azure and create a virtual machine in Azure.

![https://docs.microsoft.com/en-us/learn/modules/automate-azure-tasks-with-powershell/media/7-windows-powershell-ise-screenshot.png](https://docs.microsoft.com/en-us/learn/modules/automate-azure-tasks-with-powershell/media/7-windows-powershell-ise-screenshot.png)

Once you have written the script, execute it from the PowerShell command line by passing the name of the file preceded by a dot and a backslash:

```sh
.\myScript.ps1
```

## PowerShell techniques

PowerShell has many features found in typical programming languages. You can define variables, use branches and loops, capture command-line parameters, write functions, add comments, and so on. We will need three features for our script: variables, loops, and parameters.

### Variables

As you saw in the last unit, PowerShell supports variables. Use **\$** to declare a variable and = to assign a value. For example:

```sh
$loc = "East US"
$iterations = 3
```

Variables can hold objects. For example, the following definition sets the **adminCredential** variable to the object returned by the **Get-Credential** cmdlet.

```sh
$adminCredential = Get-Credential
```

To obtain the value stored in a variable, use the **\$** prefix and its name as shown below:

```sh
$loc = "East US"
New-AzResourceGroup -Name "MyResourceGroup" -Location $loc
```

### Loops

PowerShell has several loops: **For**, **Do...While**, **For...Each**, and so on. The **For** loop is the best match for our needs because we will execute a cmdlet a fixed number of times.

The core syntax is shown below; the example runs for two iterations and prints the value of **i** each time. The comparison operators are written **-lt** for "less than", **-le** for "less than or equal", **eq** for "equal", **ne** for "not equal", etc.

```sh
For ($i = 1; $i -lt 3; $i++)
{
    $i
}
```

### Parameters

When you execute a script, you can pass arguments on the command line. You can provide names for each parameter to help the script extract the values. For example:

```sh
.\setupEnvironment.ps1 -size 5 -location "East US"
```

Inside the script, you capture the values into variables. In this example, the parameters are matched by name:

```sh
param([string]$location, [int]$size)
```

You can omit the names from the command line. For example:

```sh
.\setupEnvironment.ps1 5 "East US"
```

Inside the script, you rely on position for matching when the parameters are unnamed:

```sh
param([int]$size, [string]$location)
```

We could take these parameters as input, and use a loop to create a set of VMs from the given parameters. We'll try that next.

The combination of PowerShell and Azure PowerShell gives you all the tools you need to automate Azure. In our CRM example, we will be able to create multiple Linux VMs using a parameter to keep the script generic and a loop to avoid repeated code. This means that a formerly complex operation can now be executed in a single step.

# Exercise - Create and save scripts in Azure PowerShell

# Summary
