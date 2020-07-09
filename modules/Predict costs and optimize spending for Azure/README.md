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

Imagine that you've been asked to build a system on Azure, and you've been asked for an estimate of what it might cost to run over the next 12 months. You already know that Azure pricing is fully transparent and that you're billed monthly for only the services that you use. How would you get that estimate without deploying and running those services or without manually pricing out each service from the Azure service pricing pages?

## Introducing the Azure pricing calculator

To make estimates easy for customers to create, Microsoft developed the Azure pricing calculator. The Azure pricing calculator is a free web-based tool that allows you to input Azure services and modify properties and options of the services. It outputs the costs per service and total cost for the full estimate.
The options that you can configure in the pricing calculator vary between products, but basic configuration options include:

- Region - Lists the regions from which you can provision a product. Southeast Asia, central Canada, the western United States, and northern Europe are among the possible regions available for some resources.
- Tier - Sets the type of tier you wish to allocate to a selected resource, such as Free Tier, Basic Tier, etc.
- Billing Options - Highlights the billing options available to different types of customers and subscriptions for a chosen product.
- Support Options - Allows you to pick from included or paid support pricing options for a selected product.
- Programs and Offers - Allows you to choose from available price offerings according to your customer or subscription type.
- Azure Dev/Test Pricing - Lists the available development and test prices for a product. Dev/Test pricing applies only when you run resources within an Azure subscription that is based on a Dev/Test offer.

### Try out the Azure pricing calculator

In another browser window or tab, open the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/). On the pricing calculator page, you'll see several tabs:

- **Products**. This tab is where you'll do most of your activity. This tab has all the Azure services listed and is where you'll add or remove services to put together your estimate.
- **Example Scenarios** This tab has several examples of infrastructure involved in common cloud-based solutions. You can add all the components of the entire scenario to estimate the cost.
- **Saved Estimates**. This tab has all of your previously saved estimates. We'll go through this process in a moment.
- **FAQ**. Just as it says, this tab has answers to some frequently asked questions.

Let's start with the **Products** tab. You'll see the full listing of service categories down the left-hand side. Clicking on any of the categories will display the services in that category. There's also a search box where you can search through all services for the service you're looking for. Clicking on the service will add it to your estimate. You can add just one service, or you can add as many as you need, including multiples of the same service. For example, you can add multiple virtual machines.

After you've added the services, you'll want to price them. Scrolling down on the Pricing Calculator page will show you customizable details for that service that apply to pricing. For example, on virtual machines, you can select details such as the region, operating system, and instance size. These options all impact the pricing for the VM. You'll see a subtotal for the service. Further down the page, you'll see the full total for all services included in the estimate. Along with the total, you'll see buttons where you can export, save, and share the estimate.

## Estimate a solution

From our original scenario, let's imagine that this system will run on two Azure VMs and will connect to an Azure SQL Database instance. We also want to have a layer 7 load balancer in place to ensure we have enhanced load-balancing capabilities. The following illustration shows an application gateway connected to two virtual machines that are connected to a single Azure SQL Database instance.

![https://docs.microsoft.com/en-us/learn/modules/predict-costs-and-optimize-spending/media/2-estimate-costs-architecture.png](https://docs.microsoft.com/en-us/learn/modules/predict-costs-and-optimize-spending/media/2-estimate-costs-architecture.png)

We can use the Azure pricing calculator to figure out what the solution will cost and export our estimate to share with the team. From the **Example Scenarios** tab, you can add all the resources involved with a common solution to the problem you are trying to solve to estimate all the potential costs. You can also add individual products from the **Products** tab to create an estimate for your custom solution.

TIP: Make sure you have a clean calculator with nothing listed in the estimate. If you have anything present in your estimate, click the trash can icon on each item to reset the estimate.

In the **Products** tab of the Azure pricing calculator, add the following services to the estimate by clicking on them:

- **Virtual Machines** in the **Compute** category
- **Azure SQL Database** in the **Databases** category
- **Application Gateway** in the **Networking** category

We can configure the details of each, on the **Saved Estimates** tab, to get a solid estimate of our costs. Use the **West US** region for all resources.

- **Virtual Machines**. This project is an ASP.NET application, so we'll need to use a **Windows** operating system VM. This application doesn't require a massive amount of computing power, so select the **D2 v3** instance size. We'll need **two** virtual machines, and they will run all the time (730 hours/month). We're going to use **Standard SSD** storage for these VMs and will require just one disk per VM of size **E10**, for a total of **two** disks. Keep the storage transactions set to the default of 100.
- **SQL Database**. For the database, we're going to provision a **Single Database** type using the **vCore** model. We want a **General Purpose, Gen 5** database with **8 vCores**. We'll need **32 GB** of storage.
- **Application Gateway**. For Application Gateway, we're going to use the **Web Application Firewall** tier, so we have some protection for our environment. And we're going to go with just **2** instances and **Medium** size, as our load isn't going to be high. We expect to process **1 TB** of data per month. We don't expect to process any data in the North America, Europe (Zone 1).

Looking through your estimate, you should see a summary cost for each service you've added and a full total for the entire estimate. You can try playing with some of the options - particularly the location you place these resources in - to see the estimate go up or down.

TIP: If you have resources that are not location-sensitive, you can save a lot of money by locating them in less expensive regions. Checking the pricing calculator can help you determine the most cost-effective place to put these services.

## Share and save your estimate

We now have an estimate for our solution. We can save this estimate, so we can come back to it later and adjust it if necessary. We can also export it to Excel for further analysis or share the estimate via a URL.

To export the estimate, select **Export** at the bottom of the estimate. Exporting will download your estimate in Excel (**.xlsx**) format and will include all the services you added to your estimate.

We can either share the Excel spreadsheet, or we can click on the `Share` button in the calculator. Sharing will give you a URL that you can use to share this estimate. Anyone with this link will be able to access it, making it easy to share with your team.

If you are logged in with your Azure account, you can save the estimate, so you can come back to it later. Go ahead and click the **Save** button. If you are signed in, you should see a notification that your estimate was saved. If you aren't signed in, you'll see a message to sign in to save your estimate. After you've saved the estimate, scroll back up to the top of the page and select the Saved Estimates tab. You will see your estimate there. You can then select it to pull it back up, or delete it if you no longer need it.

We have arrived at a cost estimate for a set of Azure services without spending any money. We didn't create anything, and we have a fully sharable estimate that we can do further analysis or modifications on in the future. You can use this estimate not only to create estimates for systems where you know the specific services you plan to use but also to compare how different services might impact your overall costs. An example is Microsoft SQL Server on a VM instead of an Azure SQL Database.

## Check your knowledge

1. Which tab of the Azure pricing calculator will you use to put together your estimate?

[] Estimate
[ x ] Products

2. True or false: You can share your estimate through an Excel spreadsheet or through a URL.

[ x ] True
[] False

# Exercise - Predict and optimize with Cost Management and Azure Advisor

We learned how to estimate costs before you deploy services on Azure, but what if you already have resources deployed? How do you get visibility into the costs you're already accruing? If we had deployed our previous solution to Azure and now want to make sure that we've sized the virtual machines properly and predict how much our bill will be, how can we do this review? Let's look at a few tools on Azure that you can use to help you solve this problem.

## What is Azure Advisor?

**Azure Advisor** is a free service built into Azure that **provides recommendations on high availability, security, performance, operational excellence, and cost**. Advisor analyzes your deployed services and looks for ways to improve your environment across each of these areas. We'll focus on the cost recommendations, but you'll want to take some time to review the other recommendations as well.

Advisor makes cost recommendations in the following areas:

- **Reduce costs by eliminating unprovisioned Azure ExpressRoute circuits.** This recommendation identifies ExpressRoute circuits that have been in the provider status of _Not Provisioned_ for more than one month. Advisor recommends deleting the circuit if you aren't planning to provision the circuit with your connectivity provider.
- **Buy reserved instances to save money over pay-as-you-go.** Advisor will review your virtual machine usage over the last 30 days and determine if you could save money in the future by purchasing reserved instances. Advisor will show you the regions and sizes where you potentially have the most savings and will show you the estimated savings you might achieve from purchasing reserved instances.
- **Right-size or shutdown underutilized virtual machines.** This analysis monitors your virtual machine usage for 14 days and then identifies underutilized virtual machines. Virtual machines whose average CPU utilization is 5 percent or less and network usage is 7 MB or less for four or more days are considered underutilized virtual machines. The average CPU utilization threshold is adjustable up to 20 percent. By identifying these virtual machines, you can decide to resize them to a smaller instance type, reducing your costs.

Let's take a look at where you can find Azure Advisor in the portal.

- Sign in to the Azure portal using your Microsoft account.
- Expand the left-hand navigation from the top-left menu and click on **All Services**.
- Click on the **Management + governance** category and find **Advisor**. You can also type `Advisor` in the services filter box to filter on just that name.
- Click on Advisor, and you'll be taken to the Advisor recommendations dashboard where you can see all the recommendations for your subscription. You'll see a box for each category of recommendations.

NOTE: You might not have any recommendations on cost in Advisor. Assessments may not have completed yet or Advisor may not have recommendations.

![https://docs.microsoft.com/en-us/learn/modules/predict-costs-and-optimize-spending/media/3-advisor-recommendations.png](https://docs.microsoft.com/en-us/learn/modules/predict-costs-and-optimize-spending/media/3-advisor-recommendations.png)

Clicking on the **Cost** box will take you to detailed recommendations where you can see the recommendations that Advisor has.

![https://docs.microsoft.com/en-us/learn/modules/predict-costs-and-optimize-spending/media/3-advisor-cost-recommendations.png](https://docs.microsoft.com/en-us/learn/modules/predict-costs-and-optimize-spending/media/3-advisor-cost-recommendations.png)

Clicking on any recommendation will take you to the details for that specific recommendation. Then you'll be able to take a specific action, such as resizing virtual machines to reduce spending.

![https://docs.microsoft.com/en-us/learn/modules/predict-costs-and-optimize-spending/media/3-advisor-resize-vm.png](https://docs.microsoft.com/en-us/learn/modules/predict-costs-and-optimize-spending/media/3-advisor-resize-vm.png)

These recommendations are all places where you might be inefficiently spending money. They're a great place to start and continue to revisit when looking for places to reduce costs. In our example, there's an opportunity for us to save around \$700 per month if we take these recommendations. This savings adds up, so be sure to review these recommendations periodically across all areas.

## Azure Cost Management

Azure Cost Management is another free, built-in Azure tool that can be used to gain greater insights into where your cloud money is going. You can see historical breakdowns of what services you are spending your money on and how it is tracking against budgets that you have set. You can set budgets, schedule reports, and analyze your cost areas.

![https://docs.microsoft.com/en-us/learn/modules/predict-costs-and-optimize-spending/media/3-cost-management.png](https://docs.microsoft.com/en-us/learn/modules/predict-costs-and-optimize-spending/media/3-cost-management.png)

As you can see, Azure offers tools at no additional cost that you can use to track and predict your cloud spend and identify where your environment may be inefficient from a cost perspective. You'll want to make sure you make it a regular practice to review the reports and recommendations that these tools make available, so you can unlock savings across your cloud footprint.

## Check your knowledge

1. Azure Advisor provides recommendations for \***\*\_\*\***.

[] Costs only
[ x ] High availability, security, performance, operational excellence, and cost
[] High availability, performance, and cost

2. Azure Cost Management allows you to \***\*\_\*\***.

[ x ] See historical breakdowns of what services you are spending your money on.
[] See estimates of what your services might cost if you make a change.

# Exercise - Estimate the Total Cost of Ownership with the Azure TCO calculator

# Save on infrastructure costs

# Save on licensing costs

# Summary
