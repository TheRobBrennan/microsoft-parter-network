[Move Azure resources to another resource group](https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/) (~42 mins)

# Introduction

You're the subscription owner for the marketing department at your company. Some resources created for development work were put into the same resource group as a production website. Your department recently had an incident where someone deleted a resource group that they thought was used only for development resources. However, the person inadvertently deleted some production resources in that resource group. You now need to move development resources out of the resource group that has the production resources.

In this module, you'll identify incorrectly assigned resources in Azure. You'll assess whether resources can be moved, and validate a resource move operation. Then you'll move and verify resources between Azure resource groups.

## Learning objectives

- Identify whether you can move resources
- Move resources to another resource group

## Prerequisites

- Familiarity with Azure resource groups

# Identify incorrectly assigned resources in Azure

Your organization has a resource group that contains both production resources and development resources. You're going to move the development resources out of that group into their own resource group, which is dedicated to development.

In this unit, you'll learn how to go to your production resource group and identify the development resources that don't belong there.

## Organize Azure resources into resource groups

You use resource groups to logically group resources together. Each resource must be part of a single resource group. A resource group can hold many different types of resources in different regions.

Generally, resource groups hold resources that are related. For example, you can have a resource group for testing and development, and a separate one for production. A production resource group might hold resources that support a live website. These resources can include several virtual machines, storage accounts, virtual networks, and Azure SQL Database instances.

Make sure that all the resources in your resource group have a single lifecycle. You want to update, delete, or deploy these resources together.

**If you believe a resource doesn't fit into the lifecycle of other resources, move it to another resource group.**

You can control which resources are available to different types of users. In this way, resource groups help you protect your resources and control costs.

You might need to change who's billed for resources in resource groups. You can move resources from one resource group to another resource group in a different subscription. In this case, you'd need to put any dependent resources into one resource group. Then move the resources from that resource group into a new resource group in the other subscription.

## Use a consistent naming convention

To help identify and organize your resource groups, use a consistent naming convention. For example, you might have a resource group named "**project-infrastructure-resource-group**" to denote that it's a resource group for a certain project's infrastructure.

## Find resource groups

Find resource groups in the Azure portal by selecting **Resource Groups** from the left-hand side. You'll see a list of all your resource groups.

![https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/media/2-view-resource-groups.png](https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/media/2-view-resource-groups.png)

When you select a resource group, you see all the resources in it. Within the resource group, you can:

- Search for specific resources.
- Filter resource based on type.
- Create a resource.
- Move resources to another resource group.

## Use tags to find resources

**Tags are name and value pairs that you apply to resources**. For example, you can set the name "environment" and its value as "development" for all resources that aren't meant for production. In this way, you ensure that you can easily find related resources and keep them organized.

Many resource types have a **Tags** page in the portal that you can use to add tags.

![https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/media/2-add-tags.png](https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/media/2-add-tags.png)

Both users and custom code can use tags to find resources. In the portal, to see a list of all the tags that have been assigned, search on "Tags".

![https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/media/2-list-of-tags.png](https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/media/2-list-of-tags.png)

If you select a tag in the list, you see a list of all the resources with that tag. Or, on the **All resources** page, you can filter the resources by tags.

Tags have limitations:

- Always check whether your Azure resource supports tags. For example, **you can't set tags on generalized VMs**.
- You can apply tags on resource groups, but tags aren't automatically inherited by resources from their resource group. If you want all the resources in a resource group to have a tag, you must do this manually.
- You can apply a **maximum of 50 tags** to a single resource or resource group in Azure.

Carefully consider how you assign your tags so that they help your organization locate and identify resources.

# Exercise - Identify incorrectly assigned resources

Your organization uses different resources for production and development environments. Some resources that were created for development work were put into the same resource group as production resources. You need to be able to identify which resources belong to a development resource group.

In this unit, you'll create both development and production resources, and put them in a production resource group. You'll tag your resources appropriately. You'll then filter the resources to identify all your development resources from a single location.

You need an Azure subscription to complete these exercises. If you don't have an Azure subscription, create a free account and add a subscription before you begin. If you are a student, you can take advantage of the Azure for students offer.

## Create resources

Create a resource group to act as the production resource group.

- Sign in to the Azure portal.
- Select **Resource groups** > **Add**.
- Enter **production-learn-rg** as the name for the resource group.
- Select **Review + create** > **Create**.
- Use the following quickstart template to create a virtual machine (VM) in your production resource group: [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).
- Select **Deploy to Azure** on the template page.
- For **Resource group**, select **production-learn-rg**.
- Enter a username, password, and **DNS Label Prefix**.
- Select **Purchase**.

Now you need to create some additional resources that you'll eventually move to a development resource group.

- Select **Create a resource** on the left side of the Azure portal.
- Search for **Storage account**, and select **Create**.
- Select the resource group **production-learn-rg**.
- Enter a unique storage account name that starts with the prefix **dev**.
- Select **Review + create** > **Create**.
- Create another storage account by repeating steps 1 to 5 again.

You now have a production resource group with two additional storage accounts that you'll move to a development resource group.

## Apply tags to your resources

## Filter and identify your development resources

# Assess resources that can move

# Validate resources in Azure

# Identify steps to move resources between Azure resource groups

# Exercise - Move and verify resources between Azure resource groups

# Summary
