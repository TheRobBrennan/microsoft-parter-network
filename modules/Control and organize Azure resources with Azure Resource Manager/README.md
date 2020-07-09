[Control and organize Azure resources with Azure Resource Manager](https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/) (~46 mins)

# Introduction

Imagine you've joined a company who has been moving to the cloud. This movement happened organically across different departments, and resulted in a lack of awareness of what's already been created and where everything is. There's no ability to easily determine who owns which resources. There's no enforcement of standards for things like resource names, resource sizes, and geographic locations. There's also been several instances where critical resources were inadvertently deleted, causing business-critical outages.

Your manager has asked you to head up an effort to put some order into the chaos, but you're new to Azure and aren't entirely sure what you can do to make this better.

Azure Resource Manager has a number of features that you can use to organize resources, enforce standards, and protect critical Azure resources from accidental deletion. You'll take a tour through these features, and show how you can use them to your advantage.

## Learning objectives

In this module, you will:

- Use resource groups to organize Azure resources
- Use tags to organize resources
- Apply policies to enforce standards in your Azure environments
- Use resource locks to protect critical Azure resources from accidental deletion

NOTE: For this module, you will need to use your own subscription to follow along. You'll be working with resources that will have no cost associated with them, so a trial subscription or a subscription you already have access to will work to follow along with these exercises.

# Principles of resource groups

In your first week on your new job, you've looked through the existing resources in your company's Azure subscription. There are a number of resource groups that contain many different resources, but they aren't organized into a coherent structure. You've worked on Azure before, but aren't entirely sure how resource groups work and what their role is. You've guessed (correctly) that they can play a role in how you organize your resources. Let's look at what they are, and how they can be used.

## What are resource groups?

Resource groups are a fundamental element of the Azure platform. A resource group is a logical container for resources deployed on Azure. These resources are anything you create in an Azure subscription like virtual machines, Application Gateways, and CosmosDB instances. All resources must be in a resource group and a resource can only be a member of a single resource group. Many resources can be moved between resource groups with some services having specific limitations or requirements to move. Resource groups can't be nested. Before any resource can be provisioned, you need a resource group for it to be placed in.

### Logical grouping

Resource groups exist to help manage and organize your Azure resources. By placing resources of similar usage, type, or location, you can provide some order and organization to resources you create in Azure. Logical grouping is the aspect that you're most interested in here, since there's a lot of disorder among our resources.

![https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/media/2-rg.png](https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/media/2-rg.png)

### Life cycle

If you delete a resource group, all resources contained within are also deleted. Organizing resources by life cycle can be useful in non-production environments, where you might try an experiment, but then dispose of it when done. Resource groups make it easy to remove a set of resources at once.

### Authorization

Resource groups are also a scope for applying role-based access control (RBAC) permissions. By applying RBAC permissions to a resource group, you can ease administration and limit access to allow only what is needed.

## Create a Resource Group

Resource groups can be created by using the following methods:

- Azure portal
- Azure PowerShell
- Azure CLI
- Templates
- Azure SDKs (like .NET, Java)

Let's walk through the steps you'd take to create a resource group in the Azure portal. If you'd like to follow along in your own subscription, you may.

Open a web browser and sign into the Azure portal

IMPORTANT: Make sure to use your own subscription. When you are in the free sandbox environment, it will not allow you to create resource groups. You can tell which subscription you are using by looking at the tenant name under your profile picture. You can switch tenants by selecting your profile picture and selecting **Switch Directory** from the options menu.

On the Azure portal menu or from the **Home** page, select **Create a resource**.

Type **Resource group** in the search box and hit Enter. If this doesn't go immediately to the resource group creation, select **Resource group** from the search results and select the **Create** button.

Select the subscription it should be in, and select the region for the resource group.

Enter your resource group name, let's use **msftlearn-core-infrastructure-rg**.

![https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/media/2-create-resource-group.png](https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/media/2-create-resource-group.png)

Select **Review + Create** and then, once it is validated, select **Create** to create the resource group.

That's it, you've created a resource group that you can now use when you deploy Azure resources. Let's take a closer look at this resource group and some important things to consider.

## Explore a resource group and add a resource

On the Azure portal menu or from the **Home** page, select **Resource groups**, and select your newly created resource group. Note that you may also see a resource group called **NetworkWatcherRG**. You can ignore this resource group, it's created automatically to enable Network Watcher in Azure virtual networks.

![https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/media/2-rg-overview.png](https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/media/2-rg-overview.png)

On the Overview panel, there's the basic information about the resource group like the subscription it's in, the subscription ID, any tags that are applied, and a history of the deployments to this resource group. You'll cover tags in the next unit. The deployments link takes you to a new panel with the history of all deployments to this resource group. Anytime you create a resource, it's a deployment, and you see that history for the resource group here.

Across the top you can add more resources, change the columns in the list, move the resource group to another subscription, or delete it entirely.

On the left menu, there are a number of options

You don't have any resources in this resource group yet, so the list at the bottom is empty. Let's create a couple resources inside the resource group.

- Select **+ Add** at the top or select the **Create resources**; either will work.
- Search for **Virtual Network**. The first result should be the virtual network resource. Select it, and on the next screen select **Create**.
- Name the virtual network `msftlearn-vnet1`. For the **Resource group** drop-down, select the resource group that you created earlier.
- Select **Review + create** and then select **Create** to add the virtual network to your resource group.
- Repeat the virtual network creation steps again to create one more virtual network. Name the network `msftlearn-vnet2` and make sure to place the virtual network in the resource group that you created earlier.
- Go back to your resource group, and on the **Overview** panel you should see the two virtual networks you created.

![https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/media/2-rg-with-vnet.png](https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/media/2-rg-with-vnet.png)

Our resource group now contains two virtual network resources because you specified in our deployment (when you created the resources) which resource group you wanted the virtual network to be placed in. You could create additional resources inside this resource group, or you could create additional resource groups in the subscription to deploy resources into.

When creating resources, you usually have the option to create a new resource group as an alternative to using an existing resource group. This simplifies the process a bit, but as you see in your new organization, can lead to resources spread across resource groups with little thought as to how to organize them.

## Use resource groups for organization

So how can you use resource groups to your advantage in your new organization? There are some guidelines and best practices that can help with the organization.

### Consistent naming convention

You can start with using an understandable naming convention. You named our resource group **msftlearn-core-infrastructure-rg**. You've given some indication of what it's used for (**msftlearn**), the types of resources contained within (**core-infrastructure**), and the type of resource it is itself (**rg**).

This descriptive name gives us a better idea of what it is. If you had named it **my-resource-group** or **rg1**, you have no idea on a glance of what the usage may be. In this case, you can deduce that there are probably core pieces of infrastructure contained within. If you created additional virtual networks, storage accounts, or other resources the company may consider core infrastructure, you could place them here as well, to improve the organization of our resources. Naming conventions can vary widely between and even within companies, but some planning can help.

### Organizing principles

Resource groups can be organized in a number of ways, let's take a look at a few examples. You might put all resources that are core infrastructure into this resource group. But you could also organize them strictly by resource type. For example, put all virtual networks in one resource group, all virtual machines in another resource group, and all Azure Cosmos DB instances in yet another resource group.

![https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/media/2-resource-type-rg.png](https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/media/2-resource-type-rg.png)

You could organize them by environment (prod, qa, dev). In this case, all production resources are in one resource group, all test resources are in another resource group, and so on.

![https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/media/2-environment-rg.png](https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/media/2-environment-rg.png)

You could organize them by department (marketing, finance, human resources). Marketing resources go in one resource group, finance in another resource group, and HR in a third resource group.

![https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/media/2-department-rg.png](https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/media/2-department-rg.png)

You could even use a combination of these strategies and organize by environment and department. Put production finance resources in one resource group, dev finance resources in another, and the same for the marketing resources.

![https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/media/2-env-dept-rg.png](https://docs.microsoft.com/en-us/learn/modules/control-and-organize-with-azure-resource-manager/media/2-env-dept-rg.png)

There are a few factors that can play into the strategy you use to organize resources: authorization, resource life cycle, and billing.

#### Organizing for authorization

Since resource groups are a scope of RBAC, you can organize resources by _who_ needs to administer them. If your database administration team is responsible for managing all of your Azure SQL Database instances, putting them in the same resource group would simplify administration. You could give them the proper permissions at the resource group level to administer the databases within the resource group. Similarly, the database administration team could be denied access to the resource group with virtual networks, so they don't inadvertently make changes to resources outside the scope of their responsibility.

#### Organizing for life cycle

We mentioned earlier that resource groups serve as the life cycle for the resources within it. If you delete a resource group, you delete all the resources in it. Use this to your advantage, especially in areas where resources are more disposable, like non-production environments. If you deploy 10 servers for a project that you know will only last a couple of months, you might put them all in a single resource group. One resource group is easier to clean up than 10 or more resource groups.

#### Organizing for billing

Lastly, placing resources in the same resource group is a way to group them for usage in billing reports. If you're trying to understand how your costs are distributed in your Azure environment, grouping them by resource group is one way to filter and sort the data to better understand where costs are allocated.

## Summary

The bottom line is that you have flexibility in how to organize resources in your resource groups. Put some thought into it so that you have a coherent approach to how you use resource groups in your Azure environment.

# Use tagging to organize resources

# Use policies to enforce standards

# Secure resources with role-based access control

# Use resource locks to protect resources

# Check your Knowledge

# Summary
