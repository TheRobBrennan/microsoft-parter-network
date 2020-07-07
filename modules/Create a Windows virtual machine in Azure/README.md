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

## Configure the VM settings

### Configure basic VM settings

## Configure Disks for the VM

### Create a data disk

## Configure the Network

## Finish configuring the VM and create the image

# Use RDP to connect to Windows Azure virtual machines

# Exercise - Connect to a Windows virtual machine using RDP

# Configure Azure virtual machine network settings

# Summary
