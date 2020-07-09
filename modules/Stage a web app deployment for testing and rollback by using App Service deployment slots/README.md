[Stage a web app deployment for testing and rollback by using App Service deployment slots](https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/) (~44 mins)

# Introduction

When you have a successful or business-critical web app, you need to update it to respond to business changes, user demands, or security issues. But you can't allow service interruptions.

Suppose you work for a company that runs a popular social media web platform. The user interface for this platform is set up as an ASP.NET Core MVC web app that's hosted in Azure App Service. You regularly update the app's source code and roll out the updates to production. These updates occasionally cause problems when testers fail to catch bugs. Also, service is briefly interrupted when you roll out an update, and then responsiveness is slow while the code is deployed and compiled.

You want a way to deploy a new version of the app without downtime or a service interruption. You also want to be able to rapidly roll back a new deployment to the previous version if it causes problems.

## Learning objectives

In this module, you will:

- Create a deployment slot as a staging environment in App Service.
- Use git to deploy a new version of a web app to a slot.
- Configure which app settings are swapped and which are not swapped when you deploy a slot.
- Swap slots to deploy a web app or roll back a deployment.

## Prerequisites

- Basic experience deploying apps to App Service

# Create deployment slots

Organizations often need to run web apps in isolated environments to test them before deployment. They also need to deploy quickly and without affecting users.

Suppose you're trying to decide whether to use slots as a streamlined way to deploy a web app in your social media system. You want to find out if deployment slots will reduce downtime during deployments, if they'll ease rollbacks, and if you can set them up in Azure.

Here you'll learn how deployment slots ease the testing and rollout of new code.

## Use a deployment slot

Within a single Azure App Service web app, you can create multiple deployment slots. **Each slot is a separate instance of that web app, and it has a separate host name**. You can deploy a different version of your web app into each slot.

One slot is the production slot. This slot is the web app that users see when they connect. Make sure that the app deployed to this slot is stable and well tested.

Use additional slots to host new versions of your web app. Against these instances, you can run tests such as integration tests, acceptance tests, and capacity tests. Fix any problems before you move the code to the production slot. Additional slots behave like their own App Service instances, so you can have confidence that your tests show how the app will run in production.

After you're satisfied with the test results for a new app version, deploy it by swapping its slot with the production slot. Unlike a code deployment, a slot swap is instantaneous. When you swap slots, the slot host names are exchanged, immediately sending production traffic to the new version of the app.

**When you use slot swaps to deploy, your app is never exposed to the public web in a partially deployed state.**

If you find that, in spite of your careful testing, the new version has a problem, you can roll back the version by swapping the slots back.

## Understand slots as separate Azure resources

When you use more than one deployment slot for a web app, those slots are treated as separate instances of that web app. For example, they're listed separately on the **All resources** page in the Azure portal. They each have their own URL. However, each slot shares the resources of the App Service plan, including virtual machine memory and CPU as well as disk space.

## Create deployment slots and tiers

Deployment slots are available only when your web app uses an App Service plan in the Standard, Premium, or Isolated tier. The following table shows the maximum number of slots you can create:

- Free - 0 maximum staging slots
- Shared - 0 maximum staging slots
- Basic - 0 maximum staging slots
- Standard - 5 maximum staging slots
- Premium - 20 maximum staging slots
- Isolated - 20 maximum staging slots

## Avoid a cold start during swaps

Many of the technologies that developers use to create web apps require final compilation and other actions on the server before they deliver a page to a user. Many of these tasks are completed when the app starts up and receives a request. For example, if you use ASP.NET to build your app, code is compiled and views are completed when the first user requests a page. Subsequent requests for that page receive a faster response because the code is already compiled.

The initial delay is called a _cold start_. You can avoid a cold start by using slot swaps to deploy to production. When you swap a slot into production, you "warm up" the app because your action sends a request to the root of the site. The warm-up request ensures that all compilation and caching tasks finish. After the swap, the site responds as fast as if it had been deployed for days.

## Create a deployment slot

Before you create a slot, make sure your web app is running in the Standard, Premium, or Isolated tier:

- Open your web app in the Azure portal.
- Select the **Deployment Slots** page.
- Select **Add Slot**.
- Name the slot.
- Choose whether to clone settings from another slot. If you choose to clone, settings are copied to your new slot from the slot you specify.

![https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/2-add-a-slot.png](https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/2-add-a-slot.png)

NOTE: Although you can clone settings to a new slot, you can't clone content. New slots always begin with no content. You must deploy content by using git or another deployment strategy. The clone operation copies the configuration to the new slot. After you clone the settings, the configuration of the two slots can be changed independently.

Select **Add** to create the new slot. You now see the new slot in the list on the **Deployment Slots** page. Select the slot to view its management page.

![https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/2-list-of-slots.png](https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/2-list-of-slots.png)

## Access a slot

The new slot's host name is derived from the web app name and the name of the slot. You see this host name when you select the slot on the **Deployment Slots** page:

![https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/2-finding-slot-url.png](https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/2-finding-slot-url.png)

You can deploy your code to the new slot the same way you deploy it for the production slot. Just substitute the new slot's name or URL in the configuration of the deployment tool you use. If you use FTP to deploy, you'll see the FTP host name and username just under the slot's URL.

The new slot is effectively a separate web app with a different host name. That's why anyone on the internet can access it if they know that host name. Unless you register the slot with a search engine or link to it from a crawled page, the slot won't appear in search engine indexes. It will remain obscure to the general internet user.

**You can control access to a slot by using IP address restrictions**. Create a list of IP address ranges that you'll allow to access the slot or a list of ranges that you'll deny access to the slot. These lists are like the allow ranges and deny ranges that you can set up on a firewall. Use this list to permit access only to computers that belong to your company or development team.

## Create deployment slots

1. How does Azure App Service ensure that production performance doesn't drop just after a swap?

[ x ] App Service warms up the app by sending a request to the root of the site.
[] App Service uses the same virtual machines for all of the slots for a web app.
[] App Service uses the same disk quota for all of the slots for a web app.

2. Which of the following are NOT shared between all of the deployment slots for a web app?

[] Virtual machines
[ x ] Host names
[] Deployment plans

# Exercise - Create deployment slots

A deployment slot is an instance of a web app in which you can test a new app version before you deploy it. By swapping slots, you can deploy a new version of an app without any downtime.

Suppose you've chosen to use Azure App Service deployment slots to streamline the deployment of new versions of your social media web app. Now you want to set up the web app in Azure and configure the deployment slots.

Here you'll set up a web app and add a new deployment slot to it for staging. You'll also deploy different versions of the web app to those slots.

## Create a web app

Start by creating a new web app resource in the Azure portal:

Sign in to the Azure portal .

On the Azure portal menu or from the **Home** page, select **Create a resource**.

Select **Web** > **Web App**.

Fill out the wizard using information like the following examples:

- **Subscription** - Select the subscription you'd like to use to complete the exercise
- **Resource Group** - Create a new resource group named **mslearn-slots**
- **Name** - Enter a unique name
- **Publish** - Code
- **Runtime stack** - ASP.NET V4.7
- **Operating System** - Windows
- **Region** - Select a region near you
- **Windows Plan** - Leave default
- **Sku and size** - Leave default

Navigate to the **Monitoring** tab at the top of the page and toggle **Enable Application Insights** to **No**.

Select **Review and create**, then select **Create** on the resulting page. Wait while Azure creates the web app.

## Configure git deployment

Use any of the usual deployment tools for your web app and its deployment slots. In this exercise, you'll use a local git repository for deployment. Set up the web app to use git by following these steps:

- On the Azure portal menu or from the **Home** page, select **All resources** and then select the web app you created.
- On the **Overview** page, under **Deployment**, select **Deployment Center**.
- Select **Local Git** > **Continue**.
- Select **App Service build service** > **Continue** > **Finish**.
- On the resulting **Deployment Center** page, select **FTP/Credentials** at the top, and then select the **User Credentials** tab.
- Enter a new username and password of your choice and select **Save Credentials**. Make a note of the username and password for later.

## Configure the git client and clone the web app source code

Now you'll set up the git client in Cloud Shell and use it to clone a sample web app. Follow these steps:

In the Azure portal, open the Cloud Shell. Enter the following commands to set up your git username and email address. These commands aren't associated with any account or sign-up, and you can use whatever values you like.

```sh
git config --global user.name "<your name>"
git config --global user.email "<your email address>"
```

To create a folder for the source code, enter the following commands:

```sh
mkdir demoapp
cd demoapp
```

To clone the source for the web app, enter the following commands:

```sh
git clone https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git
cd app-service-web-dotnet-get-started
```

## Configure a git remote to deploy the app to production

To use git to deploy the source code to the web app's production slot, set up your app's git URL as a remote repository. Follow these steps:

In the Azure portal, on the web app's **Overview** page, next to **Git clone url**, select the **Copy** button. Note that the URL contains your deployment username.

![https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/3-copy-git-clone-url.png](https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/3-copy-git-clone-url.png)

In Cloud Shell, run the following command to configure the URL as a git remote named "production". Replace `git-clone-url` with the URL from the previous step.

```sh
git remote add production <git-clone-url>
```

To deploy the web app to the production slot, enter the following command. When you're prompted for the password, enter your deployment password you created previously.

```sh
git push production
```

When the deployment finishes, in the Azure portal, go to the web app's Overview page and then select Browse. Azure displays the web app:

![https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/3-web-app-production-slot.png](https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/3-web-app-production-slot.png)

Close the browser tab that displays the web app.

## Create a new staging slot

For the new the web app, you created only one slot: the production slot. You deployed source code to this slot.

Next you'll create a deployment slot where you can stage new versions of the web app:

On the Azure portal menu or from the **Home** page, select **All resources** and then select the web app.

Under **Deployment**, select **Deployment slots**.

![https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/3-access-deployment-slots.png](https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/3-access-deployment-slots.png)

On the **Deployment slots** page, select **Add slot**.

In the **Name** box, enter **Staging** and then select **Add**.

After the deployment slot is added, select **Close**.

## Set up git deployment for the staging slot

Set up the new slot to use git deployment, just like you did for the production slot. Follow these steps:

- On the Azure portal menu or from the **Home** page, select **All resources**. In the list of all resources, you'll now see two web app entries: deployment slots are represented as separate apps in the portal. Select the entry representing the staging slot to go to its **Overview** page.
- Under **Deployment**, select **Deployment Center**.
- Select **Local Git** > **Continue**.
- Select **App Service build service** > **Continue** > **Finish**.

## Set up git to deploy the app to the staging slot

To use the git client to deploy source code to the new slot, add an extra remote to the git configuration. Follow these steps:

In the Azure portal, go to the **Overview** page of the staging slot. Near the top of the page, next to **Git clone url**, select the **Copy** button.

To add the remote for the staging slot, run the following command in the Cloud Shell. Replace `git-clone-url` with the URL from the previous step.

```sh
git remote add staging <git-clone-url>
```

## Modify the app source code and deploy the app to the staging slot

Next, make a small change to the web app and then use git to deploy the new version to the staging slot:

In Cloud Shell, enter the following command:

```sh
code .
```

In the list of **Files**, expand **aspnet-get-started** > **Views** > **Home**.

Select **Index.cshtml**.

Locate the following code:

```html
<h1>ASP.NET</h1>
```

Replace that code with this code:

```html
<h1>Web App Version 2</h1>
```

To save your changes, press CTRL+S.

In the Cloud Shell, enter the following commands to commit the new version of the app to git and deploy it to the staging slot. Enter your deployment password when you're prompted.

```sh
git add .
git commit -m "New version of web app."
git push staging
```

## Browse the staging slot

Now you can view the new version of the web app by browsing to the staging deployment slot's URL.

In the Azure portal, go to the **Overview** page for the staging slot. Near the top of the page, select the **Browse** button. The new version of the web app appears in a browser tab:

![https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/3-web-app-staging-slot.png](https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/3-web-app-staging-slot.png)

# Deploy a web app by swapping deployment slots

When you swap slots, you can precisely control the behavior and configuration of web apps.

Suppose you've set up deployment slots for production and staging. You've tested a new version of your social media web app in the staging slot. Now it's time to deploy that new version to production. You want to deploy the app smoothly and in the correct configuration.

Here you'll learn the correct configuration to swap the web app into production.

## Manage the configuration for a swap

When you swap two slots, the app's configuration travels to the new slot along with the app. You can override this behavior for individual application settings and configuration strings by configuring them as **slot settings**.

Suppose, for example, you have two databases. You use one for production and the other for acceptance testing. You always want the app version in the staging slot to use the testing database. The app version in the production slot should always use the production database. To achieve this, you can configure the database connection string as a slot setting.

### Configure slot settings

To view and configure settings for the swap, go to the web app resource and follow these steps:

On the Azure portal menu or from the **Home** page, select **All resources**, and select the deployment slot you want to configure.

Navigate to the **Configuration** page.

On the **Application settings** tab, observe whether or not the settings you're interested in have a checkmark in the **deployment slot setting** field. To set or unset the checkmark on a given setting, select the setting's pencil button to edit it, then toggle the deployment slot setting checkbox to the desired value and select **OK**.

![https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/4-configure-slot-settings.png](https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/4-configure-slot-settings.png)

Select **Save** on the Configuration page when you are finished to save your settings.

### Swap slots in the Azure portal

To swap two slots in the Azure portal:

On the Azure portal menu or from the **Home** page, select **All resources**, and navigate to any of the deployment slots for the web app and select the **Deployment Slots** page.

Select **Swap**.

In the **Swap** dialog box, you can select the source and target slots and see a summary of the settings that will be applied to the swapped slots:

![https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/4-swap-slots.png](https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/4-swap-slots.png)

## Understand the slot-swapping preview

When you swap slots, the settings in the target slot (which is typically the production slot) are applied to the app version in the source slot before the host names are swapped. You might discover problems at this point. For example, if the database connection string is configured as a slot setting, the new version of the web app will use the existing production database. If you forgot to upgrade the database schema in the production database before the swap, you could see errors and exceptions when the new app version attempts to use the old schema.

To help you discover problems before your app goes live into production, Azure App Service offers a swap-with-preview feature. When you choose this option, the swap proceeds in two phases:

- **Phase 1**: Slot settings from the target slot are applied to the web app in the source slot. Then Azure warms up the staging slot. At this point, the swap operation pauses so you can test the app in the source slot to make sure it works with the target slot configuration. If you find no problems, begin the next phase.
- **Phase 2**: The host names for the two sites are swapped. The version of the app now in the source slot receives its slot settings.

IMPORTANT: Test your web app thoroughly while it's in the staging slot. Eliminate code bugs and problems with non-slot settings. The swap-with-preview feature can only help you spot and eliminate problems caused by the production slot settings. Make sure everything else is sound before you start any kind of swap into production.

### Preview slot swapping

To use the swap-with-preview feature, select **Perform swap with preview**, review the settings, and then select **Start Swap**.

![https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/4-swap-slots-with-preview.png](https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/4-swap-slots-with-preview.png)

Follow the link to preview the new version of the site. In the preview, the slot settings from the destination slot are applied. If you want to continue, select **Complete Swap**.

## Auto swap

Auto swap brings the zero-downtime and easy rollback benefits of swap-based deployment to automated deployment pipelines. When you configure a slot for auto swap, Azure automatically swaps it whenever you push code or content into that slot.

When you use auto swap, you can't test the new app version in the staging slot before the swap. Auto swap mainly benefits users who want zero-downtime deployments and simple automated deployment pipelines.

If you want to be able to test before you swap, you'll need a more complex deployment pipeline that requests the slot swap itself. Alternatively, you can deploy to a separate slot that's dedicated for testing.

NOTE: Auto swap is not available in App Service on Linux.

### Configure auto swap

To configure auto swap for a slot, navigate to the **Configuration** > **General settings** page for the slot in the Azure portal. Under **Deployment Slot**, set **Auto swap enabled** to **On**, select the target slot from the dropdown menu, then select **Save** at the top.

![https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/4-configure-auto-swap.png](https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/4-configure-auto-swap.png)

This option is only available on slots other than the production slot.

# Exercise - Deploy a web app by using deployment slots

When you're ready to swap two slots, make sure you've applied the correct configuration to the swapped slots.

Suppose you've finished testing version 2 of your social media web app. Now you want to deploy that version to production. You want to further streamline deployment by automatically swapping future versions of the app.

Here you'll learn how to swap manually and automatically.

## Configure a slot setting

Before you deploy version 2 of the web app, configure a slot setting. The settings you'll configure here won't affect your demo app. The purpose of this exercise is just to see how the configurations work when you swap slots.

To configure slot settings:

- From the **All resources** view in the Azure portal, navigate to the **Overview** page of the production slot of the web app.
- Navigate to the **Configuration** page for the deployment slot.
- Select **New application setting**. Add a new setting with the name **ENVIRONMENT_NAME** and a value of **production**. Check the deployment slot setting box to make this a slot setting.
- Add another setting called **APP_VERSION**, and enter the value **1**. Don't make this a slot setting.
- Near the top of the page, select the **Save** button.
- Repeat the preceding steps on the **Staging** slot, but use the following values:
  - ENVIRONMENT_NAME - staging - Deployment slot setting Yes
  - APP_VERSION - 2 - Deployment slot setting No

## Swap the slots

Now that you've tested version 2 of the web app in the staging slot, you can deploy it by swapping the slots. Follow these steps:

To make sure you're configuring the production slot, select **All resources** and then select the production slot of the web app.

Under **Deployment**, select **Deployment slots** > **Swap**.

![https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/5-swap-staging-slot.png](https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/5-swap-staging-slot.png)

Make sure you're about to swap the staging and production slots. Notice how the swap will affect settings. The value of the APP_VERSION setting will be exchanged between the slots, but the value of the ENVIRONMENT slot setting won't be swapped. Select **Swap**.

![https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/5-simple-swap.png](https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/5-simple-swap.png)

When the swap is complete, go to the **Overview** page of the production slot's web app and select **Browse**. The web app appears on a new browser tab. Notice that version 2 of the web app is now in production.

Close the browser tab.

## Configure auto swap for the staging slot

Suppose that now that you're using deployment slots, you want to enable continuous deployment. You'll do this by using the auto swap feature for your web app. In a system that uses auto swap, when you deploy new code to the staging slot, Azure automatically warms it up and deploys it to production by swapping the staging and production slots.

To configure auto swap, follow these steps:

- Go to the **Configuration** page of the staging slot's web app and navigate to the **General settings** tab.
- Set **Auto swap enabled** to **On**.
- In the **Auto swap deployment slot** list, select **production**, then select **Save**.

![https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/5-configure-auto-swap.png](https://docs.microsoft.com/en-us/learn/modules/stage-deploy-app-service-deployment-slots/media/5-configure-auto-swap.png)

## Deploy new code and auto swap it into production

Now you'll modify the code to create version 3 of the web app. When you deploy it to the staging slot, you'll see auto swap in action. Follow these steps:

On the right side of the Cloud Shell window, restart the editor if it's not already running.

```sh
cd ~/demoapp/app-service-web-dotnet-get-started/
code .
```

In the code editor, in the **File list** on the left, expand **aspnet-get-started** > **Views** > **Home**. Then select **Index.cshtml**.

Locate the following code:

```html
<h1>Web App Version 2</h1>
```

Replace that code with this code:

```html
<h1>Web App Version 3</h1>
```

To save your changes, press CTRL+S.

In Cloud Shell, enter the following commands. Enter your deployment password when you're prompted.

```sh
git add .
git commit -m "Third version of web app."
git push staging
```

Wait for the deployment to finish. Near the end of the text output, you'll see a message that indicates that the deployment has requested an auto swap to the production slot.

In the Azure portal, navigate to the **Overview** page for the production slot's web app and select **Browse**. The third version of the web app appears on a new browser tab. If the old version is shown, you may need to wait briefly and then refresh the page - the swap operation is atomic and occurs instantly, but it takes App Service a few moments to prepare the swap operation before it's executed.

## Roll back the new version

# Summary
