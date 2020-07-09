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

## Set up git deployment for the staging slot

## Set up git to deploy the app to the staging slot

```sh

```

## Modify the app source code and deploy the app to the staging slot

```sh

```

```sh

```

```sh

```

```sh

```

## Browse the staging slot

# Deploy a web app by swapping deployment slots

# Exercise - Deploy a web app by using deployment slots

# Summary
