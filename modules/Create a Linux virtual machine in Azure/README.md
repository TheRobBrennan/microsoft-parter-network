[Create a Linux virtual machine in Azure](https://docs.microsoft.com/en-us/learn/modules/create-linux-virtual-machine-in-azure/) (~1 hr 26 mins)

# Introduction

You have been hired by a global auto racing company to modernize their entire monitoring and web platform. They have decided to replace existing Linux servers with a variety of cloud-based infrastructure that leverages the latest in architectural trends. Part of the system will run on the Azure serverless platform using Azure Functions to process real-time race data, pushing statistics, race data, and other relevant bits of analyzed information into clusters of databases. They want to keep their existing website, which was just rewritten last year, but have it connect into this modern data stream.

The website is running on Apache with Linux, and since it's already up and running, you decide to move it directly into Azure by leveraging an Azure virtual machine. This will give the website access to the data with a minimal amount of work on your part.

## Learning objectives

In this module, you will:

- Understand the options that are available for virtual machines in Azure
- Create a Linux virtual machine using the Azure portal
- Connect to a running Linux virtual machine using SSH
- Install software and change the network configuration on a VM using the Azure portal

# Create a Linux virtual Machine in Azure

We have an existing website running on a local Ubuntu Linux server. Our goal is to create an Azure virtual machine (VM) using the latest Ubuntu image and then migrate the site to the cloud. In this unit, you will learn about the options you will need to evaluate when creating a virtual machine in Azure.

## Introduction to Azure Virtual Machines

Azure Virtual Machines is an on-demand, scalable cloud-computing resource. They include processors, memory, storage, and networking resources. You can start and stop virtual machines at will and manage them from the Azure portal or with the Azure CLI. You can also use a remote Secure Shell (SSH) to connect directly to the running VM and execute commands as if you were on a local computer.

### Running Linux in Azure

Creating Linux-based VMs in Azure is easy. Microsoft has partnered with prominent Linux vendors to ensure their distributions are optimized for the Azure platform. You can create virtual machines from prebuilt images for a variety of popular Linux distributions, such as SUSE, Red Hat, and Ubuntu, or build your own Linux distribution to run in the cloud.

## Creating an Azure VM

VMs can be defined and deployed on Azure in several ways: the Azure portal, a script (using the Azure CLI or Azure PowerShell), or an Azure Resource Manager template. In all cases, you will need to supply several pieces of information that we'll cover shortly.

The Azure Marketplace also provides preconfigured images that include both an OS and favorite software tools installed for specific scenarios.

![https://docs.microsoft.com/en-us/learn/modules/create-linux-virtual-machine-in-azure/media/2-marketplace-vm-choices.png](https://docs.microsoft.com/en-us/learn/modules/create-linux-virtual-machine-in-azure/media/2-marketplace-vm-choices.png)

## Resources used in a Linux VM

When creating a Linux VM in Azure, you also create resources to host the VM. These resources work together to virtualize a computer and run the Linux operating system. These must exist (and be selected during VM creation), or they will be created with the VM:

- A virtual machine that provides CPU and memory resources
- An Azure Storage account to hold the virtual hard disks
- Virtual disks to hold the OS, applications, and data
- A virtual network (VNet) to connect the VM to other Azure services or your on-premises hardware
- A network interface to communicate with the VNet
- An optional public IP address so you can access the VM

Like other Azure services, you'll need a **Resource Group** to contain the VM (and optionally group these resources for administration). When you create a new VM, you can either use an existing resource group or create a new one.

## Choose the VM image

Selecting an image is one of the first and most important decisions you'll make when creating a VM. An image is a template that's used to create a VM. These templates include an OS and often other software, such as development tools or web hosting environments.

Anything that a computer can have installed can be included in an image. You can create a VM from an image that's preconfigured to precisely the tasks you need, such as hosting a web app on the Apache HTTP Server.

You can also create and upload custom disk images.

## Sizing your VM

Just as a physical machine has a certain amount of memory and CPU power, so does a virtual machine. Azure offers a range of VMs of differing sizes at different price points. The size that you choose will determine the VM's processing power, memory, and maximum storage capacity.

There are quota limits on each subscription that can impact VM creation. If you run into these quota limits you can [open an online customer support request](https://docs.microsoft.com/en-us/azure/azure-supportability/resource-manager-core-quotas-request) to increase your limits.

VM sizes are grouped into categories, **starting with the B-series for basic testing and running up to the H-series for massive computing tasks**. You should select the size of the VM based on the workload you want to perform. It is possible to change the size of a VM after it's been created, but the VM must be stopped first. So, it's best to size it appropriately from the start if possible.

Here are some guidelines based on the scenario you are targeting:

- **General use computing/web**: Testing and development, small to medium databases, or low to medium traffic web servers.
  - Consider these sizes - `B, Dsv3, Dv3, DSv2, Dv2`
- **Heavy computational tasks**: Medium traffic web servers, network appliances, batch processes, and application servers.
  - Consider these sizes - `Fsv2, Fs, F`
- **Large memory usage**: Relational database servers, medium to large caches, and in-memory analytics.
  - Consider these sizes - `Esv3, Ev3, M, GS, G, DSv2, Dv2`
- **Data storage and processing**: Big data, SQL, and NoSQL databases that need high disk throughput and I/O.
  - Consider these sizes - `Ls`
- **Heavy graphics rendering or video editing**, as well as model training and inferencing (ND) with deep learning.
  - Consider these sizes - `NV, NC, NCv2, NCv3, ND`
- **High-performance computing (HPC)**: Your workloads need the fastest and most powerful CPU virtual machines with optional high-throughput network interfaces.
  - Consider these sizes - `H`

## Choosing storage options

The next set of decisions revolves around storage. First, you can choose the disk technology. Options include a traditional platter-based hard disk drive (HDD) or a more modern solid-state drive (SSD). Just like the hardware you purchase, SSD storage costs more but provides better performance.

There are two levels of SSD storage available: standard and premium. Choose Standard SSD disks if you have normal workloads but want better performance. Choose Premium SSD disks if you have I/O intensive workloads or mission-critical systems that need to process data very quickly.

### Mapping storage to disks

Azure uses virtual hard disks (VHDs) to represent physical disks for the VM. VHDs replicate the logical format and data of a disk drive but are stored as page blobs in an Azure Storage account. You can choose on a per disk basis what type of storage it should use (SSD or HDD). This allows you to control the performance of each disk, likely based on the I/O you plan to perform on it.

By default, two virtual hard disks (VHDs) will be created for your Linux VM:

- The **operating system disk**: This is your primary drive, and it **has a maximum capacity of 2048 GB**. It will be labeled as `/dev/sda` by default.
- A **temporary disk**: This provides temporary storage for the OS or any apps. On Linux virtual machines, the disk is `/dev/sdb` and is formatted and mounted to `/mnt` by the Azure Linux Agent. It is sized based on the VM size and is used to store the swap file.

#### What about data?

You can store data on the primary drive along with the OS, but a better approach is to create dedicated data disks. You can create and attach additional disks to the VM.

**Each disk can hold up to 32,767 gibibytes (GiB) of data**, with the maximum amount of storage determined by the VM size you select.

Azure virtual disk sizes are measured in **Gibibytes (GiB)**, which are not the same as Gigabytes (GB); **one GiB is approximately 1.074 GB**.

Therefore, **to obtain an approximate equivalent of your virtual disk size in GB, multiply the size in GiB by 1.074**, and that will return a size in GB that is relatively close. For example, 32,767 GiB would be approximately 35,183 GB.

**An interesting capability is to create a VHD image from a real disk.** This allows you to easily migrate existing information from an on-premises computer to the cloud.

### Unmanaged vs. managed disks

The final storage choice you'll make is whether to use **unmanaged** or **managed** disks.

With unmanaged disks, you are responsible for the storage accounts that are used to hold the VHDs that correspond to your VM disks. You pay the storage account rates for the amount of space you use. A single storage account has a fixed rate limit of 20,000 I/O operations/sec. This means that a single storage account is capable of supporting 40 standard virtual hard disks at full throttle. If you need to scale out, then you need more than one storage account, which can get complicated.

Managed disks are the newer and recommended disk storage model. They elegantly solve this complexity by putting the burden of managing the storage accounts onto Azure. You specify the disk type (Premium or Standard) and the size of the disk, and **Azure creates and manages both the disk and the storage it uses**. You don't have to worry about storage account limits, which makes them easier to scale out. They also offer several other benefits:

- **Increased reliability**: Azure ensures that VHDs associated with high-reliability VMs will be placed in different parts of Azure Storage to provide similar levels of resilience.
- **Better security**: Managed disks are real managed resources in the resource group. This means they can use role-based access control to restrict who can work with the VHD data.
- **Snapshot support**: Snapshots can be used to create a read-only copy of a VHD. We recommend that you shut down the VM to clear out any processes that are in progress. Creating the snapshot only takes a few seconds. Once it's done, you can power on the VM and use the snapshot to create a duplicate VM to troubleshoot a production issue or roll back the VM to the point in time that the snapshot was taken.
- **Backup support**: Managed disks can be automatically backed up to different regions for disaster recovery with Azure Backup without affecting the service of the VM.

## Network communication

Virtual machines communicate with external resources using a virtual network (VNet). The VNet represents a private network in a single region that your resources communicate on. A virtual network is just like the networks you manage on-premises. You can divide them up with subnets to isolate resources, connect them to other networks (including your on-premises networks), and apply traffic rules to govern inbound and outbound connections.

### Planning your network

When you create a new VM, you will have the option of creating a new virtual network or using an existing VNet in your region.

Having Azure create the network together with the VM is simple, but it's likely not ideal for most scenarios. It's better to plan your network requirements up front for all the components in your architecture and create the VNet structure separately. Then, create the VMs and place them into the already-created VNets. We'll look more at virtual networks later in this module.

Before we create a virtual machine, we need to decide how we'd like to administer the VM. Let's look at our options.

# Exercise - Decide an authentication method for SSH

Before we can create a Linux virtual machine in Azure, we will need to think about remote access. We want to be able to sign in to our Linux web server to configure the software and perform maintenance. The default approach to administering Linux VMs hosted in Azure is SSH.

## What is SSH?

Secure Shell (SSH) is an encrypted connection protocol that allows secure sign-ins over unsecured connections. SSH allows you to connect to a terminal shell from a remote location using a network connection.

There are two approaches we can use to authenticate an SSH connection: **username and password**, or an **SSH key pair**.

Although SSH provides an encrypted connection, using passwords with SSH connections leaves the VM vulnerable to brute-force attacks of passwords. A more secure and preferred method of connecting to a Linux VM with SSH is a public-private key pair, also known as SSH keys.

With an SSH key pair, you can sign in to Linux-based Azure virtual machines without a password. This is a more secure approach if you only plan to sign in to the VM from a few computers. If you need to be able to access the Linux VM from a variety of locations, a username and password combination might be a better approach. There are two parts to an SSH key pair: a public key and a private key.

- The public key is placed on your Linux VM or any other service that you wish to use with public-key cryptography. This can be shared with anyone.
- The **private key** is what you present to verify your identity to your Linux VM when you make an SSH connection. Consider this confidential information and protect this like you would a password or any other private data.

You can use the same single public-private key pair to access multiple Azure VMs and services.

## Create the SSH key pair

On Linux, Windows 10, and macOS, you can use the built-in `ssh-keygen` command to generate the SSH public and private key files.

Windows 10 includes an SSH client with the **Fall Creators Update**. Earlier versions of Windows require additional software to use SSH; [check the documentation for full details](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows). Alternatively, you can install the Linux subsystem for Windows and get the same functionality.

**We will use Azure Cloud Shell, which stores the generated keys in Azure in your private storage account.** You can also type these commands directly into your local shell if you prefer. You will need to adjust the instructions throughout this module to reflect a local session if you take this approach.

Here is the minimum command necessary to generate the key pair for an Azure VM. This creates an SSH protocol 2 (SSH-2) RSA public-private key pair. The minimum length is 2048, but for the sake of this learning module we will use 4096.

Copy this command into Cloud Shell:

```sh
ssh-keygen -t rsa -b 4096
```

Select the _Enter_ key to accept the default location. The command creates two files: `id_rsa` and `id_rsa.pub` in the `~/.ssh` directory. The files are overwritten if they exist.

Enter a passphrase that you'll remember. You'll need this passphrase when you use the SSH key to access the VM.

There are various options you can use to provide the file name or a passphrase to avoid the prompts.

### Private key passphrase

You can provide a passphrase while generating your private key. This is a password you must enter when you use the key. This passphrase is used to access the private SSH key file and is not the user account password.

When you add a passphrase to your SSH key, it encrypts the private key using 128-bit AES so that the private key is useless without the passphrase to decrypt it.

We strongly recommended that you add a passphrase. If an attacker stole your private key and that key did not have a passphrase, they would be able to use that private key to log in to any servers that have the corresponding public key. If a passphrase protects a private key, it cannot be used by that attacker. This provides an additional layer of security for your infrastructure on Azure.

## Use the SSH key pair with an Azure Linux VM

Once you have the key pair generated, you can use it with a Linux VM in Azure. You can supply the public key during the VM creation or add it after the VM has been created.

You can view the contents of the file in Azure Cloud Shell with the following command:

```sh
cat ~/.ssh/id_rsa.pub
```

It'll look something like the following output:

```sh
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCw52la2vrgJSe3MzZy9WG96HxXRPG6JoOUXnMHD6H+1h+XH5YqG0UMs5hhqhk+S54rqR1lXQscUrznqibFwOxHhjmz5E8gXrKPb9lKlS/MyWh5dpukBsbkv2j/G4px5yUQe6hi9FSxhFJNHfn+zmtmX/4ie8AxF8i9CEXR8mtvPYBTsazqmZiRJ4xh5acEIHe8SIPKMD+qDhdgJ86JNQ+s8k+yjKOk9KYk7UkdORlR9/W8i0Iq6E/pB0Nz21jwYpGX8xILAnorlKN8kUcEGR3VHKlmNjNyKHQFaJ3TEdpXvjrcpDxfSEQnYOIliHKnyCqQhRhbX6fk+L4O4yNWzKOScK9FqMoZntuN9sKQKHB64SsJUVCvCSuBtlPD3rtwlRG5ux/ii7hzu7KzTyMucKoTT3hlCHunyubD8RxVrfRZ8Qnc86Dn+QYeAjguGll9FTfXGVW2t1sqqJh00vTA0NJyLbC4rF7xoTQErqUuTD0CAb1a0kb483O/zVgcXJSpQtJfyVM1ckVpa1HMXVPNIfeR67rKdK/9Qb0oaX6DnucUoL6M0JQX2CMTbjikP9ayAUAOFmqHeI1I9MAdTMbBtik4eF7874nMY6GkgKZvc/RJq85ikJB8piK22Rurp3NZzlhCzDfw/TSSkKKr+uz306Q6+fZkR8gTyyZL7m/DJwJ/kw== rob@cc-2f5ed202-778bb59c6-td9xd
```

Copy this value, so you can use it in the next exercise.

### Use the SSH key when creating a Linux VM

To apply the SSH key while creating a new Linux VM, you will need to copy the contents of the public key and supply it to the Azure portal, or supply the public key file to the Azure CLI or Azure PowerShell command. We'll use this approach when we create our Linux VM.

### Add the SSH key to an existing Linux VM

If you have already created a VM, you can install the public key onto your Linux VM with the `ssh-copy-id` command. Once the key has been authorized for SSH, it grants access to the server without a password, though you will still be prompted for the passphrase on the key if you set one.

For example, if we had a Linux VM named _myserver_ with a user _azureuser_, we could use the following command to install the public key file and authorize the user with the key:

```sh
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

Now that we have our public key, let's switch to the Azure portal and create a Linux VM.

# Exercise - Create a Linux virtual machine with the Azure portal

Recall that our goal is to move an existing Linux server running Apache to Azure. We'll start by creating an Ubuntu Linux server.

## Create a new Linux virtual machine

We can create Linux VMs with the Azure portal, the Azure CLI, or Azure PowerShell. The easiest approach when you are starting with Azure is to use the portal because it walks you through the required information and provides hints and helpful messages during the creation:

Sign into the Azure portal using the same account you activated the sandbox with.

On the Azure portal menu or from the **Home** page, select **Create a resource**.

In the search box, enter **Ubuntu Server**.

Select **Ubuntu Server 18.04 LTS Canonical** from the list.

Click the **Create** button to start configuring the VM.

## Configure the VM settings

The VM creation experience in the portal is presented in a wizard format to walk you through all the configuration areas for the VM. Clicking the **Next** button will take you to the next configurable section. However, you can move between the sections at will with the tabs running across the top that identify each part.

![https://docs.microsoft.com/en-us/learn/modules/create-linux-virtual-machine-in-azure/media/3-azure-portal-create-vm.png](https://docs.microsoft.com/en-us/learn/modules/create-linux-virtual-machine-in-azure/media/3-azure-portal-create-vm.png)

Once you fill in all the required options (identified with red asterisks), you can skip the remainder of the wizard experience and start creating the VM through the **Review + Create** button at the bottom.

We'll start with the **Basics** section. These instructions are for the Sandbox portal. If you are using another Azure portal account, you may need to adapt some details accordingly.

### Configure basic VM settings

For **Subscription**, the sandbox subscription should be selected for you by default.

For **Resource group**, the resource group with the name learn-7124dd2f-c7c1-44a8-9694-a0258933d0a0 should be selected for you by default.

In the **Instance details** section, enter a name for your web server VM, such as **test-web-wus2-vm1**. This indicates the environment (test), the role (web), location (West US 2), service (vm), and instance number (1). It's considered best practice to standardize your resource names, so you can quickly identify their purpose. Linux VM names must be between 1 and 64 characters and be comprised of numbers, letters, and dashes.

Select a location.

- West US 2

Set **Availability options** to _No infrastructure redundancy required_. This option can be used to ensure the VM is highly available by grouping multiple VMs together as a set to deal with planned or unplanned maintenance events or outages. For this exercise we will not need this service.

Ensure that the image is set to **Ubuntu Server 18.04 LTS**. You can open the drop-down list to see all the options available.

Leave the **Size** field with the default of **D2s v3** choice, which **gives you two vCPUs with 8 GB of RAM**.

Moving on to the **Administrator account** section, for **Authentication type** select the **SSH public key** option.

Enter a **username** you'll use to sign in with SSH. Choose something you can remember or write it down. - `testuser`

Paste the SSH key from your public key file you created in the previous unit into the **SSH public key** field. It should look similar to the example shown in unit 3 with no additional whitespace or line-feed characters.

In the **INBOUND PORT RULES** section, first select **Allow selected ports**. Since this is a Linux VM, we want to be able to access the VM using SSH remotely. Scroll the **Select inbound ports** list if necessary until you find **SSH (22)** and **enable** it.

![https://docs.microsoft.com/en-us/learn/modules/create-linux-virtual-machine-in-azure/media/3-open-ports.png](https://docs.microsoft.com/en-us/learn/modules/create-linux-virtual-machine-in-azure/media/3-open-ports.png)

## Configure disks for the VM

Click **Next: Disks >** to move to the **Disks** section.

Choose **Premium SSD** for the **OS disk type**.

### Create a data disk

Recall that we will get an OS disk (`/dev/sda`) and a temporary disk (`/dev/sdb`). Let's `add a data disk` as well:

Click the **Create and attach a new disk** link in the **Data disks** section.

![https://docs.microsoft.com/en-us/learn/modules/create-linux-virtual-machine-in-azure/media/3-add-data-disk.png](https://docs.microsoft.com/en-us/learn/modules/create-linux-virtual-machine-in-azure/media/3-add-data-disk.png)

You can take all the defaults: **Premium SSD**, the auto-generated name, size of **1023 GiB**, and **None (empty disk)** for **Source type**, although notice that source type is where you could use a snapshot or Azure Blob storage to create a VHD.

Click **OK** to create the disk and go back to the **Data disks** section.

There should now be a new disk in the first row.

![https://docs.microsoft.com/en-us/learn/modules/create-linux-virtual-machine-in-azure/media/3-new-disk.png](https://docs.microsoft.com/en-us/learn/modules/create-linux-virtual-machine-in-azure/media/3-new-disk.png)

## Configure the network

Click **Next: Networking >** to move to the **Networking** section.

In a production environment where we already have other components, you'd want to utilize an _existing_ virtual network. That way, your VM can communicate with the other cloud services in your solution. If there isn't one defined in this location yet, you can create it here and configure the:

- **Address space**: The overall IPV4 space available to this network.
- **Subnets**: The first subnet to subdivide the address space - it must fit within the defined address space. Once the VNet is created, you can add additional subnets.

By default, Azure creates a virtual network, network interface, and public IP for your VM.

**It's not trivial to change the networking options after the VM has been created, so always double-check the network assignments on services you create in Azure.**

For this exercise, the defaults should work fine.

## Finish configuring the VM and create the image

The rest of the options have reasonable defaults, and there's no need to change any of them. You can explore the other tabs if you like. The individual options have an `(i)` icon next to them that will show a help tip to explain the option. This is a great way to learn about the various options you can use to configure the VM:

- Click the **Review + create** button at the bottom of the panel.
- The system will validate your options and give you details about the VM being created.
- Click **Create** to create and deploy the VM. The Azure dashboard will show the VM that's being deployed. This may take several minutes.

While that's deploying, let's look at what we can do with this VM.

# Azure virtual machines IP addresses and SSH options

You have created a Linux VM in Azure. The next thing you’ll do is configure it for the tasks we want to move to Azure.

Unless you’ve set up a site-to-site VPN to Azure, your Azure VMs won’t be accessible from your local network. If you’re just getting started with Azure, it’s unlikely that you have a working site-to-site VPN. So how can you connect to the virtual machine?

## Azure VM IP addresses

As we saw a moment ago, Azure VMs communicate on a virtual network. They can also have an optional public IP address assigned to them. With a public IP, we can interact with the VM over the Internet. Alternatively, we can set up a virtual private network (VPN) that connects our on-premises network to Azure - letting us securely connect to the VM without exposing a public IP. This approach is covered in another module and is fully documented if you are interested in exploring that option.

**Public IP addresses in Azure are dynamically allocated** by default. That means **the IP address can change over time** - for VMs the IP address assignment happens when the VM is restarted. You can pay more to assign static addresses, if you want to connect directly to an IP address and need to ensure that the IP address will not change.

Acknowledging these restrictions, and the alternatives described above, we will use the public IP address of the VM in this module.

## Connecting to the VM with SSH

To connect to the VM via SSH, you need:

- the public IP address of the VM
- the username of the local account on the VM
- a public key configured in that account
- access to the corresponding private key
- port 22 open on the VM

Previously, you generated an SSH key pair, and added the public key to the VM configuration, and ensured that port 22 was open.

In the next unit, you'll use this information to open a secure terminal on the VM using SSH.

Once the terminal is open, you have access to all of the standard Linux command-line tools.

Next, let's connect to the VM using SSH.

# Exercise - Connect to a Linux virtual machine with SSH

Let's connect to our Linux VM with SSH, and configure Apache, so we have a running web server.

## Get the public IP address of the VM

In the Azure portal, ensure the **Overview** panel for the virtual machine that you created earlier is open. You can find the VM under **All Resources** if you need to open it. The overview panel allows you to:

- See if the VM is running
- Stop or restart the VM
- Get the public IP address of the VM
- See the activity of the CPU, disk, and network

Click the **Connect > SSH** at the top of the pane.

Under step 4, copy the command to the clipboard.

![https://docs.microsoft.com/en-us/learn/modules/create-linux-virtual-machine-in-azure/media/5-connect-ssh.png](https://docs.microsoft.com/en-us/learn/modules/create-linux-virtual-machine-in-azure/media/5-connect-ssh.png)

We used the default SSH private key file path when we created the SSH key pair. So we don't need to specify the private key path in the command by using the flag -i with the private key path. But if you entered a different path when you created the SSH key pair, you'd add that path to the command.

```sh
ssh azureuser@13.66.213.152
```

## Connect with SSH

```sh

```

```sh

```

## Initialize data disks

```sh

```

```sh

```

```sh

```

## Install software onto the VM

### Install the Apache web server

```sh

```

```sh

```

```sh

```

```sh

```

# Network and security settings

# Exercise - Configure network settings

# Summary
