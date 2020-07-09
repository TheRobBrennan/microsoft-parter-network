[Predict costs and optimize spending for Azure](https://docs.microsoft.com/en-us/learn/modules/predict-costs-and-optimize-spending/) (~1 hr 14 mins)

# Introduction

When planning a solution in the cloud, there's always the challenge of balancing cost against performance. It can feel like a guessing game whether your selected options will stay within budget or if you'll have a surprise on your next bill.

You need to be able to confidently answer several questions:

- What will this solution cost this fiscal year?
- Is there an alternate configuration you could use to save money?
- Can you estimate how a change would impact your cost and performance _without_ putting it into a production system?

In this module, we'll explore the tools you can use to answer these questions and more.

## Learning objectives

In this module, you will:

- Learn the different options you have to purchase Azure services
- Estimate costs with the Azure pricing calculator
- Predict and optimize costs with Azure Cost Management and Azure Advisor
- Apply best practices for saving on infrastructure costs
- Apply best practices for saving on licensing costs

# Purchase Azure products and services

Products and services in Azure are arranged by category, with various resources that you can provision. You select the Azure products and services that fit your requirements, and your account is billed according to Azure's pay-for-what-you-use model.

![https://docs.microsoft.com/en-us/learn/modules/predict-costs-and-optimize-spending/media/1a-azure-products-overview.png](https://docs.microsoft.com/en-us/learn/modules/predict-costs-and-optimize-spending/media/1a-azure-products-overview.png)

## Usage meters

When you provision an Azure resource, Azure creates one or more meter instances for that resource. The meters track the resources' usage, and generate a usage record that is used to calculate your bill.

For example, a single virtual machine that you provision in Azure might have the following meters tracking its usage:

- Compute Hours
- IP Address Hours
- Data Transfer In
- Data Transfer Out
- Standard Managed Disk
- Standard Managed Disk Operations
- Standard IO-Disk
- Standard IO-Block Blob Read
- Standard IO-Block Blob Write
- Standard IO-Block Blob Delete

The meters and pricing vary per product and often have different pricing tiers based on the size or capacity of the resource. Check the documentation for specific details on what each service area costs.

At the end of each monthly billing cycle, the usage values will be charged to your payment method and the meters are reset. You can check the billing page in the Azure portal at any time to get a quick summary of your current usage and see any invoices from past billing cycles.

The key takeaway is that resources are always charged _based on usage_. For example, if you de-allocate a VM then you will not be billed for compute hours, I/O reads or writes or the private IP address since the VM is not running and has no allocated compute resources. However you will incur storage costs for the disks.

NOTE: De-allocating a VM is not the same as deleting a VM. De-allocation means the VM is not assigned to a CPU or network in a datacenter. However, your persistent disks remain, and the resource is present in your subscription. It's similar to turning off your physical computer.

# Factors affecting costs

Just like your on-premises equipment costs, there are several elements that will affect your monthly costs when using Azure services. Let's look at a few of the primary factors including resource type, services, the user's location, and the billing zone.

## Resource type

Costs are resource-specific, so the usage that a meter tracks and the number of meters associated with a resource depend on the resource type.

NOTE: Each meter tracks a particular kind of usage. For example, a meter might track bandwidth usage (ingress or egress network traffic in bits-per-second), the number of operations, size (storage capacity in bytes), or similar items.

The usage that a meter tracks correlates to a number of billable units. The rate per billable unit depends on the resource type you are using. Those units are charged to your account for each billing period.

## Services

Azure usage rates and billing periods can differ between Enterprise, Web Direct, and Cloud Solution Provider (CSP) customers. Some subscription types also include usage allowances, which affect costs.

The Azure team develops and offers first-party products and services, while products and services from third-party vendors are available in the Azure Marketplace . Different billing structures apply to each of these categories.

## Location

Azure has datacenters all over the world. Usage costs vary between locations that offer particular Azure products, services, and resources based on popularity, demand, and local infrastructure costs.

For example, you might want to build your Azure solution by provisioning resources in locations that offer the lowest prices. This approach, though, would require transferring data between locations if any dependent resources and their users are located in different parts of the world. If there are meters tracking the volume of data moving between the resources you provision, any potential savings you make from choosing the cheapest location could be offset by the additional cost of transferring data between those resources.

## Azure billing zones

Bandwidth refers to data moving in and out of Azure datacenters. Most of the time inbound data transfers (data going into Azure datacenters) are free. For outbound data transfers (data going out of Azure datacenters), the data transfer pricing is based on **Billing Zones**.

A **Zone** is a geographical grouping of Azure Regions for billing purposes. The following zones exist and include the listed countries (regions).

- Zone 1 - United States, US Government, Europe, Canada, UK, France, Switzerland
- Zone 2 - East Asia, Southeast Asia, Japan, Australia, India, Korea
- Zone 3 - Brazil, South Africa, UAE
- DE Zone 1 - Germany

In most zones, the first outbound 5 gigabytes (GB) per month are free. After that amount, you are billed a fixed price per GB.

NOTE: Billing zones aren't the same as an _Availability Zone_. In Azure, the term _zone_ is for billing purposes only, and the full term _Availability Zone_ refers to the failure protection that Azure provides for datacenters.

# Exercise - Estimate costs with the Azure pricing calculator

# Exercise - Predict and optimize with Cost Management and Azure Advisor

# Exercise - Estimate the Total Cost of Ownership with the Azure TCO calculator

# Save on infrastructure costs

# Save on licensing costs

# Summary
