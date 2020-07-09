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

Now, use the Azure portal to apply tags to resources so you can identify and locate them later.

In the Azure portal, on the **Home** page, select **Resource groups**.

Select the **production-learn-rg** resource group.

Select the check box next to the **Name** field for both storage accounts you created.

![https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/media/3-select-dev-resources.png](https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/media/3-select-dev-resources.png)

Select **Assign tags**.

Enter the following values:

- NAME - environment
- VALUE - development

![https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/media/3-assign-tags.png](https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/media/3-assign-tags.png)

Select **Save**. Your resources have now been tagged with "development".

Repeat these steps to assign the following tag to the remaining resources.

- NAME - environment
- VALUE - production

## Filter and identify your development resources

Now you'll filter your resources and find your development tagged resources.

At the top of the Azure portal, search on **tags**.

![https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/media/3-search-tags.png](https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/media/3-search-tags.png)

Select **Tags**.

Select the _environment:development_ tag.

All your tagged development resources are listed in one place.

Keep your portal instance open to use later.

# Assess resources that can move

You've identified the development resources that were added to the production resource group. Now, you want to assess whether you can move those development resources to a dedicated resource group. Some resources can't be moved, while it's OK to move others with certain restrictions.

In this unit, you'll learn:

- How to identify the resource types in the resource group.
- How to check resource types against an approved move list, and check for limitations on moving identified resource types.

## Identify your resource types

First you need to identify the resource type of the resources you want to move. In the Azure portal, you can see the resource type for each resource on the **All resources** page. The same **Type** column is also included in the list of resources in a resource group.

![https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/media/4-view-resource-types.png](https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/media/4-view-resource-types.png)

## Check the limitations on resource types

After identifying the resource types of your resources, you must investigate whether they can be moved, and the restrictions that might be in place. Check your resource types against the [move support for resources](https://docs.microsoft.com/en-us/azure/azure-resource-manager/move-support-resources) list. The list shows whether each resource type can be moved between resource groups or between subscriptions. For example, these resources can be moved:

- Azure Storage accounts
- Azure virtual machines
- Azure virtual networks

These resources can't be moved:

- Azure Active Directory domain services
- Azure Backup vaults
- Azure App Service gateways

In your case, you'll need to move a pair of storage accounts that support the development version of an application. According to the move operation support for resources list, you can move these storage accounts between resource groups and between subscriptions, without any limitations.

**The list also gives links to guidance on moving specific resource types. For example, in the list you'll see that Azure App Service resources have some limitations. The move guidance for App Service states that, if you're moving a web app to another resource group in the same subscription, you can't automatically move third-party SSL certificates.**

Virtual machines have their own limitations you must keep in mind. Here's a summary of limitations for virtual machines:

- If you want to move a virtual machine, all of its dependants must go with it.
- You can't move virtual machines with certificates in Azure Key Vault between subscriptions.
- You can't move virtual machine scale sets with standard load balancers or a standard public IP.
- You can't move any managed disks that are in availability zones to different subscriptions.

Consider all the relevant limitations before attempting to move your resources. You can then decide which resources are candidates for a move.

## Check your knowledge

1. Which one of these resource types can't be moved between resource groups?

[ x ] Azure Active Directory Domain Services
[] Azure Storage accounts
[] Azure virtual machines

2. Which one of these resource types can be moved across resource groups?

[] Azure Backup vault
[] Azure Application Gateway
[ x ] Azure virtual networks

3. When can you move a virtual machine?

[] You can move virtual machines, but you have to first remove any network security groups.
[ x ] You can move virtual machines, but you have to first make sure that you can move all of its dependent resources along with it.
[] You can move virtual machines, but you have to first make sure that you can remove any SSD disks before attempting to move them.

# Validate resources in Azure

You've assessed all the development resources that are in the incorrect production resource group. You think they'll move without problems, but you'd like to test it first.

In this unit, you'll learn how to validate that a move would be successful. You'll also see how to use the Azure REST API `validate move` operation to test and validate your moves.

## Prepare to test your move

Before attempting to move a resource, you can test whether it will be successful by calling the `validate move` operation from the Azure REST API. This test is especially useful if you're trying to move resources by using, for example, Azure PowerShell or the Azure CLI. You can use these tools to script moves, with minimal human interaction. Testing a move doesn't affect your resources. The operation only tests whether your move operation would succeed, based on the options you provide.

If you're trying to move resources through the Azure portal, you don't need to validate the move before attempting it. The Azure portal does an automatic validation before allowing you to move resources.

A REST API is a programmatic interface that you can call by sending HTTP requests. Programmers often call REST APIs in their custom code from clients such as mobile apps. To call a specific Azure REST method, such as the `validate move` operation, you can use the Azure CLI:

```sh
az rest --method post --uri <enter the correct REST operation URI here>
```

To formulate the correct REST URI to call, and to provide the other necessary details, you must obtain the following information:

- Your Azure subscription ID.
- The name of the resource group currently holding your resources.
- The resource ID for each of the resources in your original resource group.
- The resource ID for the destination resource group where you want to move your resources.
- Your account access token.

**When you use the Azure CLI to call an Azure REST API operation, you don't have to provide a subscription ID or an access token. The CLI includes these values automatically.**

## Test the validity of your move

Let's have a high-level look at the process for validating a move by using the REST API.

You can send a POST request with the following details:

```sh
POST https://management.azure.com/subscriptions/<your-subscription-id>/resourceGroups/<your-source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <your-access-token>
Content-type: application/json
```

The body of your POST request must contain the following information:

```json
{
  "resources": [
    "<your-resource-id-1>",
    "<your-resource-id-2>",
    "<your-resource-id-3>"
  ],
  "targetResourceGroup": "/subscriptions/<your-subscription-id>/resourceGroups/<your-target-group>"
}
```

To submit this POST request with the correct body by using the Azure CLI, run this command:

```sh
az rest --method post /
   --uri https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/<your-source-group>/validateMoveResources?api-version=2019-05-10 /
   --body "{\"resources\": [\"<your-resource-id-1>\", \"<your-resource-id-2>\", \"<your-resource-id-3>\"], \"targetResourceGroup\": \"/subscriptions/<your-subscription-id>/resourceGroups/<your-target-group>\"}"
```

If your request is accepted, the API returns a status code of 202:

```sh
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<your-subscription-id>/operationresults/<your-operation-id>?api-version=2018-02-01
retry-after: 15
```

At this stage, the API has only validated your request. It hasn't yet validated whether your move will be successful. This response gives you a location URL. You use this location URL to test your move. Wait for the amount of time shown in the retry-after value in the request validation, before attempting to test your validation. In this example, the value is 15 seconds.

You then send a GET request to the location URL:

```sh
GET <location-url>
Authorization: Bearer <your-access-token>
```

To submit this GET request by using the Azure CLI, run this command:

```sh
az rest --method get --uri <location-url>
```

If your move validates as successful, you'll get a 204 status code. Otherwise, you'll receive the following error message, indicating that your move won't be successful:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## Check your knowledge

1. In which of the following situations would a validation test happen automatically?

[ x ] When you use the Azure portal to move resources to a new resource group.
[] When you use the PowerShell to move resources to a new resource group.
[] When a mobile app calls the moveResources REST API method.

2. In which of the following situations do you need to obtain an access token before you can validate a move?

[] When you use the Azure portal to move the resources.
[] When you use the Azure CLI to validate a move.
[ x ] When you use custom code to call the validateMoveResources REST API.

# Identify steps to move resources between Azure resource groups

In this unit, you'll learn what steps you might need to take and what tools you can use to move resources between resource groups.

## Understand resource moves

When you start a move operation, the resource group holding your resources and the new destination resource group are locked. You can't do write or delete operations on the resource groups until the move operation ends. Your resources aren't affected, but you can't add, delete, or update any resources in these resource groups.

**Your moved resources don't change location.** For example, if you have a storage account in the East US region, and you move it to another resource group, it keeps its East US region location.

## Move resources between subscriptions

Depending on the resource type, you can move your resources between subscriptions, or between resource groups within the same subscription.

For our scenario, we just need to move a pair of storage accounts to a different resource group within the same subscription. If, for billing reasons, we needed to move something like an Azure web app and all its dependent resources under a different subscription, that might include more steps. As mentioned in unit 2, you'd need to move all dependent resources into one resource group. Then, you can move those resources into a new resource group under a different subscription. The following diagram shows the steps you might need to take. This example includes a resource with one dependent resource in a separate resource group.

![https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/media/6-cross-subscription-move-scenario.png](https://docs.microsoft.com/en-us/learn/modules/move-azure-resources-another-resource-group/media/6-cross-subscription-move-scenario.png)

- Move the dependent resources into one resource group with the resource.
- Move the resource and dependent resources together from the source subscription to the target subscription.
- Optionally, redistribute the dependent resources to different resource groups within the target subscription.

## How to move resources

After you've identified the resources you want to move and verified they can be moved, you create a resource group and move the resources into that resource group.

You can use the Azure portal, the Azure CLI, PowerShell, or Azure REST API to move your resources. In the next unit, you'll use the Azure portal to move storage accounts to a new resource group. So, in this unit, let's see what commands you'd use to move resources with Azure CLI or PowerShell.

### Move resources by using the Azure CLI

The following commands show you how to move a resource by using the Azure CLI.

Create a resource group.

```sh
az group create --name <destination resource group name> --location <location name>
```

Get the resource.

```sh
yourResource=$(az resource show --resource-group <resource group name> --name <resource name> --resource-type <resource type> --query id --output tsv)
```

Move the resource to another resource group by using the resource ID.

```sh
az resource move --destination-group <destination resource group name> --ids $yourResource
```

Return all the resources in your resource group to verify your resource moved.

```sh
az group show --name <destination resource group name>
```

Update the resource IDs in any tools and scripts that reference your resources.

### Move resources by using Azure PowerShell

The following commands show you how to move a resource by using Azure PowerShell.

Create a resource group.

```sh
New-AzResourceGroup -Name <destination resource group name> -Location <location name>
```

Get the resource.

```sh
$yourResource = Get-AzResource -ResourceGroupName <resource group name> -ResourceName <resource name>
```

Move the resource to another resource group by using the resource ID.

```sh
Move-AzResource -DestinationResourceGroupName <destination resource group name> -ResourceId $yourResource.ResourceId
```

Return all the resources in your resource group to verify your resource moved.

```sh
Get-AzResource -ResourceGroupName <destination resource group name> | ft
```

Update the resource IDs in any tools and scripts that reference your resources.

# Exercise - Move and verify resources between Azure resource groups

You've identified the development resources that are in the production resource group and you've checked that they'll move successfully. Now, you want to move your development resources from the production resource group into the dedicated development resource group. You can use the Azure portal to do so.

## Create a resource group for development resources

## Move development resources to the new group

# Summary
