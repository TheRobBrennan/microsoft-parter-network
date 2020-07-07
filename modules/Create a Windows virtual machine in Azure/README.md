[Create a Windows virtual machine in Azure](https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/) (~51 mins)

# Introduction

Imagine you work for a company that does video data processing and pattern analysis. You are building a new prototype platform to process the video from traffic cameras, analyze trends, and provide actionable data for traffic and road improvements.

To improve your algorithms, you have made arrangements with several new cities to collect their traffic camera data. However not all of the video data is in the same format, and many of the formats only have Windows codecs to decode the data. Because of this, you have decided to use Virtual Machines (VMs) to do the initial processing and then push the data onto Azure Functions that will process a standard format. This approach will allow you to bring on new data formats dynamically without stopping the entire system.

Azure provides a robust virtual machine hosting solution that can meet your needs. Let's explore how to create and work with Windows virtual machines in Azure.

## Learning objectives

In this module, you will:

- Understand the options that are available for virtual machines in Azure.
- Create a Windows virtual machine using the Azure portal.
- Connect to a running Windows virtual machine using Remote Desktop.
- Install software and change the network configuration on a VM using the Azure portal.

## Prerequisites

- Basic understanding of Azure Virtual Machines from Introduction to Azure Virtual Machines
- Remote Desktop client

# Create a Windows virtual machine in Azure

Your company has decided to manage the video data from their traffic cameras in Azure using VMs. In order to run the multiple codecs, we first need to create the VMs. We also need to connect and interact with the VMs. In this unit, you will learn how to create a VM using the Azure portal. You will configure the VM for remote access, select a VM image, and choose the proper storage option.

## Introduction to Windows virtual machines in Azure

Azure VMs are an on-demand scalable cloud computing resource. They're similar to virtual machines that are hosted in Windows Hyper-V. They include processor, memory, storage, and networking resources. You can start and stop virtual machines at will, just like with Hyper-V, and manage them from the Azure portal or with the Azure CLI. You can also use a Remote Desktop Protocol (RDP) client to connect directly to the Windows desktop user interface (UI) and use the VM as if you were signed in to a local Windows computer.

## Creating an Azure VM

VMs can be defined and deployed on Azure in several ways: the Azure portal, a script (using the Azure CLI or Azure PowerShell), or through an Azure Resource Manager template. In all cases, you will need to supply several pieces of information, which we'll cover shortly.

The Azure Marketplace also provides pre-configured images that include both an OS and popular software tools installed for specific scenarios.

![https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/2-marketplace-vm-choices.png](https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/2-marketplace-vm-choices.png)

## Resources used in a Windows VM

When creating a Windows VM in Azure, you also create resources to host the VM. These resources work together to virtualize a computer and run the Windows operating system. These must either exist (and be selected during VM creation), or they will be created with the VM.

- A virtual machine that provides CPU and memory resources.
- An Azure Storage account to hold the virtual hard disks.
- Virtual disks to hold the OS, applications, and data.
- Virtual network (VNet) to connect the VM to other Azure services or your own on-premises hardware.
- A network interface to communicate with the VNet.
- A public IP address so you can access the VM. This is optional.

Like other Azure services, you'll need a **Resource Group** to contain the VM (and optionally group these resources together for administration). When you create a new VM, you can either use an existing resource group or create a new one.

## Choose the VM image

Selecting an image is one of the first and most important decisions you'll make when creating a VM. An image is a template that's used to create a VM. These templates include an OS and often other software, such as development tools or web hosting environments.

Any application that can be supported by the computer can be included in the VM image. You can create a VM from an image that's pre-configured to exactly match your requirements, such as hosting an ASP.NET Core app.

You can also create and upload your own images, check the documentation for more information.

## Sizing your VM

Just as a physical machine has a certain amount of memory and CPU power, so does a virtual machine. Azure offers a range of VMs of differing sizes at different price points. The size that you choose will determine the VMs processing power, memory, and max storage capacity.

WARNING: There are quota limits on each subscription that can impact VM creation. By default, you cannot have more than 20 virtual cores across all VMs within a region. You can either split up VMs across regions or file an [online request](https://docs.microsoft.com/en-us/azure/azure-supportability/resource-manager-core-quotas-request) to increase your limits.

VM sizes are grouped into categories, starting with the B-series for basic testing and running up to the H-series for massive computing tasks. You should select the size of the VM based on the workload you want to perform. It is possible to change the size of a VM after it's been created, but the VM must be stopped first so it's best to size it appropriately from the start if possible.

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

By default, two virtual hard disks (VHDs) will be created for your Windows VM:

- The **Operating System disk**. This is your primary or **C:** drive and has a **maximum capacity of 2048 GB**.
- A **Temporary disk**. This provides temporary storage for the OS or any apps. It is configured as the **D:** drive by default and is sized based on the VM size, making it an ideal location for the Windows paging file.

#### What about data?

You can store data on the **C:** drive along with the OS, but a better approach is to create dedicated _data disks_. You can create and attach additional disks to the VM. Each data disk **can hold up to 32,767 gibibytes (GiB) of data**, with the maximum amount of storage determined by the VM size you select.

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

We'll look more at virtual networks a bit later in this module. Let's apply some of this knowledge and create a VM in Azure.

# Exercise - Create a Windows virtual machine

Recall that our company processes video content on Windows VMs. A new city has contracted us to process their traffic cameras, but it's a model we've not worked with before. We need to create a new Windows VM and install some proprietary codecs so we can begin processing and analyzing their images.

## Create a new Windows virtual machine

We can create Windows VMs with the Azure portal, Azure CLI, or Azure PowerShell. The easiest approach is the portal because it walks you through the required information and provides hints and helpful messages during the creation of the VM.

Sign into the Azure portal using the same account you activated the sandbox with.

On the Azure portal menu or from the **Home** page, select **Create a resource**.

Select the **See all** link next to the Azure Marketplace heading. This option displays all available images on the Azure Marketplace.

Select **Microsoft** as the filter value from the _Publisher_ filter options. In the search box, enter **Windows Server** and then action the search by pressing enter or return.

![https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/3-marketplace-search.png](https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/3-marketplace-search.png)

The result will show several Windows Server related images. Choose the **Windows Server** image.

![https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/3-marketplace-windows-server.png](https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/3-marketplace-windows-server.png)

There are several Windows Server versions we can select from to create our VM. In the _Windows Server_ image overview panel, click on the **Select a software plan** dropdown list and find the **[smalldisk] Windows Server 2019 Datacenter** option.

Click the **Create** button to start configuring the VM.

## Configure the VM settings

The VM creation experience in the portal is presented in a "wizard" format to walk you through all the configuration areas for the VM. Clicking the "Next" button will take you to the next configurable section. However, you can move between the sections at will with the tabs running across the top that identify each section.

![https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/3-azure-portal-create-vm.png](https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/3-azure-portal-create-vm.png)

Once you fill in all the required options (identified with red stars), you can skip the remainder of the wizard experience and start creating the VM through the **Review + Create** button at the bottom.

We'll start with the **Basics** section.

### Configure basic VM settings

Select the **Subscription** that should be billed for VM hours.

For **Resource** group, choose "learn-388b6a0b-7439-4fbe-9b54-260d8fdb27d1".

In the **Instance Details** section, enter a name for your VM, such as `test-vp-vm2` (for Test Video Processor VM #2).

- It's best practice to standardize your resource names so you can easily identify their purpose. Windows VM names are a bit limited - they must be between 1 and 15 characters, cannot contain non-ASCII or special characters, and must be unique in the current resource group.

The free sandbox allows you to create resources in a subset of the Azure global regions. Select a region from the following list when you create resources:
West US 2

Leave **Availability options** as "No Infrastructure redundancy required". This option is used to ensure the VM is highly available by grouping multiple VMs together a set to deal with planned or unplanned maintenance events or outages.

Ensure the image is set to "[smalldisk] Windows Server 2019 Datacenter". You can open the drop-down list to see all the options available.

The **Size** field is not directly editable and has a `DS1` default size. Click the **Change size** link to explore other VM sizes. The resulting dialog allows you to filter based on # of CPUs, Name, and Disk Type. Select "**Standard DS1 v2**" (normally the default) when you are done. That will give the VM 1 CPU and 3.5 GB of memory.

In the **Administrator Account** section, set the **Username** field to a username you will use to sign in to the VM.

In the **Password** field, enter a password - `ViVKUwPtAdW2` - that's at least 12 characters long. It must have three of the following: one lower case character, one uppercase character, one number, and one special character that is not '\' or '-'. Use something you will remember or write it down, you will need it later.

Confirm the **password**.

In the **Inbound Port Rules section**, open the list and choose _Allow selected ports_. Since this is a Windows VM, we want to be able to access the desktop using RDP. Scroll the list if necessary until you find RDP (3389) and select it. As the note in the UI indicates, we can also adjust the network ports after we create the VM.

![https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/3-open-ports.png](https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/3-open-ports.png)

## Configure Disks for the VM

Click **Next** to move to the Disks section.

![https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/3-configure-disks.png](https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/3-configure-disks.png)

Choose "Premium SSD" for the **OS disk type**.

Use managed disks so we don't have to work with storage accounts. You can flip the switch in the GUI to see the difference in information that Azure needs if you like.

### Create a data disk

Recall we will get an OS disk (C:) and Temporary disk (D:). Let's add a data disk as well.

Click the **Create and attach a new disk** link in the **DATA DISKS** section.

![https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/3-add-data-disk.png](https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/3-add-data-disk.png)

You can take all the defaults: Premium SSD, 1023 GB, and None (empty disk); although notice that here is where we could use a snapshot, or Storage Blob to create a VHD.

Click **OK** to create the disk and go back to the **DATA DISKS** section.

There should now be a new disk in the first row.

![https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/3-new-disk.png](https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/3-new-disk.png)

## Configure the Network

Click **Next** to move to the Networking section.

In a production system, where we already have other components, we'd want to utilize an existing virtual network. That way our VM can communicate with the other cloud services in our solution. If there isn't one defined in this location yet, we can create it here and configure the:

- **Address space**: the overall IPV4 space available to this network.
- **Subnet range**: the first subnet to subdivide the address space - it must fit within the defined address space. Once the VNet is created, you can add additional subnets.

Let's change the default ranges to use the `172.xxx` IP address space. Click **Create New** under Virtual Network.

- Change the **Address space** field to be `172.16.0.0/16` to **give it the full range (65536) of addresses**
- Change the **Subnet range** field to be `172.16.1.0/24` to **give it 256 IP addresses of the space.**

Click **OK**.

By default, Azure will create a virtual network, network interface, and public IP for your VM. It's not trivial to change the networking options after the VM has been created so always double-check the network assignments on services you create in Azure.

## Finish configuring the VM and create the image

The rest of the options have reasonable defaults and there's no need to change any of them. You can explore the other tabs if you like. The individual options have an (i) icon next to them that will show a help bubble to explain the option. This is a great way to learn about the various options you can use to configure the VM.

Click the **Review + create** button at the bottom of the panel.

The system will validate your options and give you details about the VM being created.

Click **Create** to create and deploy the VM. The Azure dashboard will show the VM that's being deployed. This may take several minutes.

While that's deploying, let's look at what we can do with this VM.

# Use RDP to connect to Windows Azure virtual machines

Now that we have a Windows VM in Azure, the next thing you’ll do is put your applications and data on those VMs to process our traffic videos.

However, unless you’ve set up a site-to-site VPN to Azure, your Azure VMs won’t be accessible from your local network. If you’re just getting started with Azure, it’s unlikely that you have a working site-to-site VPN. So how can you transfer files to Azure VMs? One easy way is to use Azure’s Remote Desktop Connections feature to share your local drives with your new Azure VMs.

Now that we have a new Windows virtual machine, we need to install our custom software onto it. There are several options to choose from:

- Remote Desktop Protocol (RDP)
- Custom scripts
- Custom VM images (with the software preinstalled)

Let's look at the simplest approach for Windows VMs: Remote Desktop.

## What is the Remote Desktop Protocol?

Remote Desktop (RDP) provides remote connectivity to the UI of Windows-based computers. RDP enables you to sign in to a remote physical or virtual Windows computer and control that computer as if you were seated at the console. An RDP connection enables you to carry out the vast majority of operations that you can do from the console of a physical computer, with the exception of some power and hardware-related functions.

An RDP connection requires an RDP client. Microsoft provides RDP clients for the following operating systems:

- Windows (built-in)
- macOS
- iOS
- Android

The following screenshot displays the Remote Desktop Protocol client in Windows 10.

![https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/4-rdp-client.png](https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/media/4-rdp-client.png)

There are also open-source Linux clients, such as Remmina that enable you to connect to a Windows PC from an Ubuntu distribution.

## Connecting to an Azure VM

As we saw a moment ago, Azure VMs communicate on a virtual network. They can also have an optional public IP address assigned to them. With a public IP, we can communicate with the VM over the Internet. Alternatively, we can set up a virtual private network (VPN) that connects our on-premises network to Azure - letting us securely connect to the VM without exposing a public IP. This approach is covered in another module and is fully documented if you are interested in exploring that option.

One thing to be aware of with public IP addresses in Azure is they're often dynamically allocated. That means the IP address can change over time - for VMs this happens when the VM is restarted. You can pay more to assign static addresses if you want to connect directly to an IP address instead of a name and need to ensure that the IP address won't change.

### How do you connect to a VM in Azure using RDP?

Connecting to a VM in Azure using RDP is a simple process. In the Azure portal, you go to the properties of your VM, and at the top, click **Connect**. This will show you the IP addresses assigned to the VM and give you the option to download a **preconfigured.rdp** file that Windows then opens in the RDP client. You can choose to connect over the public IP address of the VM in the RDP file. Instead, if you're connecting over VPN or ExpressRoute, you can select the internal IP address. You can also select the port number for the connection.

If you're using a static public IP address for the VM, you can save the **.rdp** file to your desktop. If you're using dynamic IP addressing, the **.rdp** file only remains valid while the VM is running. If you stop and restart the VM, you must download another **.rdp** file.

You can also enter the public IP address of the VM into the Windows RDP client and click **Connect**.

When you connect, you'll typically receive two warnings. These are:

- Publisher warning - caused by the .rdp file not being publicly signed.
- Certificate warning - caused by the machine certificate not being trusted.

In test environments, these warnings can be ignored. In production environments, the **.rdp** file can be signed using **RDPSIGN.EXE** and the machine certificate placed in the client's **Trusted Root Certification Authorities** store.

Let's try using RDP to connect to our VM.

# Exercise - Connect to a Windows virtual machine using RDP

We have our Windows VM deployed and running, but it's not configured to do any work.

Recall our scenario is a video processing system. Our platform receives files through FTP. The traffic cameras upload video clips to a known URL, which is mapped to a folder on the server. The custom software on each Windows VM runs as a service and watches the folder and processes each uploaded clip. It then passes the normalized video to our algorithms running on other Azure services.

There are a few things we would need to configure to support this scenario:

- Install FTP and open the ports it needs to communicate.
- Install the proprietary video codec unique to the city's camera system.
- Install our transcoding service that processes uploaded videos.

Many of these are typical administrative tasks we won't actually cover here, and we don't have software to install. Instead, we will walk through the steps and show you how you could install custom or third-party software using Remote Desktop. Let's start by getting the connection information.

## Connect to the VM with Remote Desktop Protocol

To connect to an Azure VM with an RDP client, you will need:

- The public IP address of the VM (or private if the VM is configured to connect to your network).
- The port number.

You can enter this information into the RDP client, or download a pre-configured **RDP** file.

NOTE: An **RDP** file is a text file that contains a set of name/value pairs that define the connection parameters for an RDP client to connect to a remote computer using the Remote Desktop Protocol.

### Download the RDP file

In the Azure portal , ensure the **Overview** panel for the virtual machine that you created earlier is open. You can find the VM under **All Resources** if you need to open it. The overview panel has a lot of information about the VM.

- You can see whether the VM is running.
- Stop or restart it.
- Get the public IP address to connect to the VM.
- See the activity of the CPU, disk, and network.

Click the **Connect** button at the top of the pane.

In the **Connect to virtual machine** pane, note the **IP address** and **Port number** settings, then click **Download RDP File** and save it to your computer.

Before we connect, let's adjust a few settings. On Windows, find the file using Explorer, right-click and select Edit. On MacOS you will need to open the file first with the RDP client and then right-click on the item in the displayed list and select **Edit**.

You can adjust a variety of settings to control the experience in connecting to the Azure VM. The settings you will want to examine are:

- Display: By default, it will be full screen. You can change this to a lower resolution, or use all your monitors if you have more than one.
- Local Resources: You can share local drives with the VM - allowing you to copy files from your PC to the VM. Click the More button under Local devices and resources to select what is shared.
- Experience: Adjust the visual experience based on your network quality.

Share your Local C: drive so it will be visible to the VM.

Switch back to the **General** tab and click **Save** to save the changes. You can always come back and edit this file later to try other settings.

### Connect to the Windows VM

Click the **Connect** button to start the connection to the VM.

In the **Remote Desktop Connection** dialog box, note the security warning and the remote computer IP address, then click **Connect**.

In the **Windows Security** dialog box, enter your username and password that you used in steps 6 and 7.

NOTE: If you are using a Windows client to connect to the VM, it will default to known identities on your machine. You can click the **More choices** option and select "Use a different account" to let you enter a different username/password combination.

In the second **Remote Desktop Connection** dialog box, note the certificate errors, then click **Yes**.

### Install worker roles

The first time you connect to a Windows server VM, it will launch Server Manager. This allows you to assign a worker role for common web or data tasks. You can also launch the Server Manager through the Start Menu.

This is where we would add the Web Server role to the server. This will install IIS and as part of the configuration you would turn off HTTP requests and enable the FTP server. Or, we could ignore IIS and install a third-party FTP server. We'd then configure the FTP server to allow access to a folder on our big data drive we added to the VM.

Since we aren't going to actually configure that here, just close Server Manager.

## Install custom software

## Initialize data disks

# Configure Azure virtual machine network settings

# Summary
