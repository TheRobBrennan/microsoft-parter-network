[Scale an App Service web app to efficiently meet demand with App Service scale up and scale out](https://docs.microsoft.com/en-us/learn/modules/app-service-scale-up-scale-out/) (~46 mins)

# Introduction

It's important to be able to scale a web app for these reasons:

- It enables the app to remain responsive during periods of high demand.
- It helps to save you money by reducing the resources required when demand drops.

Azure App Service enables you to meet these goals by providing scale up and down, and scale in and out.

Imagine that you work for a large chain of hotels. You have a website that customers can visit to make bookings and to view the details of bookings that they've previously made. At certain times of the year, the volume of traffic grows because customers are browsing hotels for summer vacations. At other times, traffic declines. These patterns are predictable.

In this module, you'll use Azure App Service to scale a web app to match planned seasonal throughput requirements and also meet demand during short-term peak events. This module also describes how to scale up a web app onto more powerful hardware to meet future requirements.

## Learning objectives

In this module, you will:

- Scale a web app in and out manually.
- Scale a web app up and down.

## Prerequisites

- Navigate the Azure portal.
- Use the Azure portal to create a new App Service web app.

# Scale a web app manually

By manually scaling out and back in again, you can to respond to expected increases and decreases in traffic. Scaling out has the additional benefit of increasing availability because of the increased number of instances of the web app. A failure of one instance doesn't make the web app unavailable.

In the hotel reservation system, you can scale out before an anticipated seasonal influx. You can scale back in when the season is over and the number of booking requests is reduced.

In this unit, you'll learn how to manually scale out a web app and how to scale it back in.

## App Service plans and scalability

A web app running in Azure typically uses Azure App Service to provide the hosting environment. App Service can arrange for multiple instances of the web app to run and will load balance incoming requests across these instances. Each instance runs on a virtual machine.

The resources available to each instance are defined by an App Service plan. The App Service plan specifies the operating system (Windows or Linux), the hardware (memory, CPU processing capacity, disk storage, and so on), and the availability of services like automatic backup and restore.

Azure provides a series of well-defined App Service plan tiers. This list summarizes each of these tiers, in increasing order of capacity (and cost):

- The Free tier provides 1 GB of disk space and support for up to 10 apps, but only a single shared instance and no SLA for availability. Each app has a compute quota of 60 minutes per day. The Free service plan is mainly suitable for app development and testing rather than production deployments.
- The Shared tier provides support for more apps (up to 100) also running on a single shared instance. Apps have a compute quota of 240 minutes per day. There is no availability SLA.
- The Basic tier supports an unlimited number of apps and provides more disk space. Apps can be scaled out to three dedicated instances. This tier provides an SLA of 99.95% availability. There are three levels in this tier that offer varying amounts of compute power, memory, and disk storage.
- The Standard tier also supports an unlimited number of apps. This tier can scale to 10 dedicated instances and has an availability SLA of 99.95%. Like the Basic tier, this tier has three levels that offer an increasingly powerful set of compute, memory, and disk options.
- The Premium tier gives you up to 20 dedicated instances, an availability SLA of 99.95%, and multiple levels of hardware.
- The Isolated tier runs in a dedicated Azure virtual network, which gives you network and compute isolation. This tier can scale out to 100 instances and has an availability SLA of 99.95%.

NOTE: Some tiers aren't available for all operating systems. For example, there is currently no Shared tier for Linux.

## Monitor and scale a web app

When you create a web app, you can either create a new App Service plan or use an existing one. If you select an existing plan, any other web apps that use the same plan will share resources with your web app. They'll all scale together, so they need to have the same scaling requirements. If your apps have different requirements, use a separate App Service plan for each one.

You scale out by adding more instances to an App Service plan, up to the limit available for your selected tier. If you're not using the Free tier, you're charged for each instance by the hour. You can perform this task in the Azure portal.

The key to scaling effectively is knowing when to scale, and by how much. You monitor the performance of a web app by using the metrics available for the App Service. The simplest way to do this is to use the Azure portal. If you notice a steady increase in resource use, such as CPU utilization, memory occupancy, or disk queue length, you should consider scaling out before these metrics hit a critical point. You should also monitor the average response time of requests and the number of failing requests. If both of these figures are high, the system might be running close to (or beyond) capacity. You might need to scale out immediately.

If the metrics indicate that your system is lightly loaded and has plenty of spare capacity, you might want to scale back in to reduce costs.

In both cases, you should continue to monitor the statistics for the web app. Allow the system to stabilize. If the metrics indicate that the app is still underpowered (or overpowered), add or remove instances as needed.

# Exercise - Scale a web app manually

You should scale out a system when you expect an increase in traffic. You might also scale out in response to declining performance.

Remember that, in the hotel reservation system example, you increase the number of instances of the web app when you anticipate extra traffic because of a special event, a special offer, or because of seasonal fluctuations. You scale the system back when the demand drops.

In this exercise, you'll create an App Service plan and deploy a web app using this plan. You'll monitor the performance of the web app under load. You'll then scale out the app and verify that its performance has improved as a result.

The exercise uses a sample web app that implements a web API. The web API exposes HTTP POST and GET operations that create and retrieve customer bookings for a hotel reservations web site. The bookings aren't actually saved, and the GET operation simply retrieves dummy data.

The exercise also runs a client app that simulates a number of users issuing POST and GET operations simultaneously. This app provides the workload that's used to test the performance of the web app before and after scaling.

## Create an App Service plan and web app

Sign in to the Azure portal .

On the Azure portal menu or from the **Home** page, select **Create a resource**.

Select **Web** > **Web App**.

On the **Web App** page, enter the values in the following table.

- App name - <your-webapp-name> - `rbhotelsystem`
- Subscription - Select the Azure subscription you'd like to use for this exercise
- Resource Group - Create a new resource group called **mslearn-scale**
- OS - Windows
- Publish - Code
- App Service plan/Location - Leave default

Select **Create** and wait for the web app to be created.

## Build and deploy the web app

Open the Cloud Shell in the Azure portal. Run this command to download the source code for the hotel reservation system:

```sh
git clone https://github.com/MicrosoftDocs/mslearn-hotel-reservation-system.git
```

Go to the _mslearn-hotel-reservation-system/src_ folder:

```sh
cd mslearn-hotel-reservation-system/src
```

Build the apps for the hotel system. There are two apps: a web app that implements the web API for the system and a client app that you'll use to load test the web app.

```sh
dotnet build
```

Prepare the HotelReservationSystem web app for publishing:

```sh
cd HotelReservationSystem
dotnet publish -o website
```

Go to the website folder, which contains the published files. Zip the files and deploy them to the web app that you created in the previous task. Replace `<your-webapp-name>` with the name of your web app.

```sh
cd website
zip website.zip *
az webapp deployment source config-zip --src website.zip --name <your-webapp-name> --resource-group mslearn-scale
```

Use your web browser to go to `http://<your-webapp-name>.azurewebsites.net/api/reservations/1`. You should see a JSON document that contains the details for reservation number 1:

![https://docs.microsoft.com/en-us/learn/modules/app-service-scale-up-scale-out/media/3-web-app.png](https://docs.microsoft.com/en-us/learn/modules/app-service-scale-up-scale-out/media/3-web-app.png)

## Monitor the performance of the web app before scaling out

Return to the Cloud Shell and go to the _~/mslearn-hotel-reservation-system/src/HotelReservationSystemTestClient_ folder:

```sh
cd ~/mslearn-hotel-reservation-system/src/HotelReservationSystemTestClient
```

Edit the App.config file in this folder by using the code editor:

```sh
code App.config
```

Uncomment the line that specifies the `ReservationsServiceURI` and replace the value with the URL of your web app. The file should look like this example:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="NumClients" value="100" />
        <add key="ReservationsServiceURI" value="https://<your-webapp-name>.azurewebsites.net/"/>
        <add key="ReservationsServiceCollection" value="api/reservations"/>
    </appSettings>
</configuration>
```

NOTE: The `NumClients` setting in this file specifies the number of clients that will simultaneously try to connect to the web app and perform work. The work consists of creating a reservation and then running a query to fetch the details of the reservation. All the data used is fake. It's not actually persisted anywhere. Leave this value set to `100`.

Save the file and close the code editor.

Rebuild the test client app with the new configuration:

```sh
dotnet build
```

Run the client app. You'll see a number of messages appear as the clients start running, make reservations, and run queries. Allow the system to run for a couple of minutes. The responses will be slow, and soon the client requests will start to fail with HTTP 408 (Timeout) errors.

```sh
dotnet run
```

![https://docs.microsoft.com/en-us/learn/modules/app-service-scale-up-scale-out/media/3-web-client.png](https://docs.microsoft.com/en-us/learn/modules/app-service-scale-up-scale-out/media/3-web-client.png)

In the Azure portal, go to the pane for your web app (not the service plan). Under **Monitoring**, select **Metrics**.

Add the following metrics to the chart, set the time range to **Last 30 minutes**, and then pin the chart to the current dashboard.

- CPU Time. Select the Sum aggregation.
- Http Server Errors. Select the Sum aggregation.
- Http 4.xx. Select the Sum aggregation.
- Average Response Time. Select the Avg aggregation.

Allow the system to run for five minutes to stabilize, and then note the CPU Time, the number of HTTP 4.xx errors, and the average response time. You should see a significant number of HTTP 4xx errors (these are HTTP 408 Timeout errors), and that the average response time is several seconds. You might see the occasional HTTP server error, depending on how the web server is coping with the burden.

![https://docs.microsoft.com/en-us/learn/modules/app-service-scale-up-scale-out/media/3-web-app-chart-before-scaling-out.png](https://docs.microsoft.com/en-us/learn/modules/app-service-scale-up-scale-out/media/3-web-app-chart-before-scaling-out.png)

Leave the client app running while you perform the next task.

## Scale out the web app and verify the performance improvement

In the Azure portal, in the pane for your web app, under **Settings**, select **Scale out (App Service Plan)**.

On the **Configure** page, set the **Instance count** to **5**, and then select **Save**.

![https://docs.microsoft.com/en-us/learn/modules/app-service-scale-up-scale-out/media/3-scale-out-to-5.png](https://docs.microsoft.com/en-us/learn/modules/app-service-scale-up-scale-out/media/3-scale-out-to-5.png)

Switch to the Cloud Shell that's running the client app. You should see fewer requests failing with errors, though you'll still see some that time out.

Run the app for another five minutes. Then go to the chart that shows the metrics for the app on the dashboard in the Azure portal. You should see that the CPU time has increased dramatically because there's now five times more CPU power available. The average response time should have dropped, and the number of HTTP 4xx errors should also have decreased. The following chart shows a typical set of results. The point at which scale out occurred is noted.

![https://docs.microsoft.com/en-us/learn/modules/app-service-scale-up-scale-out/media/3-web-app-chart-after-scaling-out.png](https://docs.microsoft.com/en-us/learn/modules/app-service-scale-up-scale-out/media/3-web-app-chart-after-scaling-out.png)

If you want to experiment some more, try increasing the instance count for the App Service plan to 10. Ten is the maximum number of instances supported by the S1 tier. You should notice a further increase in CPU time, and a corresponding drop in response time and HTTP 4xx errors.

Return to the Cloud Shell that's running the client app. Select Enter to stop the app.

In the Azure portal, set the instance count for the App Service plan back to 1.

# Scale up a web app

Scaling out enables you to run more instances of a web app, but the resources available to each instance are determined by the pricing tier used by the App Service plan that hosts the web service. Each pricing tier specifies the computing power provided, together with the memory and maximum number of instances that can be created.

If you initially deploy a web app using a relatively cheap pricing tier, you might find the resources are sufficient to start with. But the resources might become too limited if demand for your web service grows, or if you add features that require more power. In this case, you can scale up to a more powerful pricing tier.

In the hotel reservation system, you've noticed a steady increase in the number of visitors, beyond the variations caused by special offers or events. And your company is adding more features to the web app that require additional resources. You're nearing the scale-out limits of your current App Service plan pricing tier, so you need to scale up to a tier that provides more instances and more powerful hardware.

In this unit, you'll learn how to scale up the web app to meet the increasing resource requirements.

## App Service plan pricing tiers and hardware levels

The different pricing tiers available for App Service plans offer various levels or resources. The Basic, Standard, and Premium tiers are based on _A-Series_ VMs that have different amounts of memory and IO capacity. The PremiumV2 and Isolated tiers are based on _Dv2-Series_ VMs. Each of these tiers has three hardware levels, roughly corresponding to 1, 2, and 4 CPUs. For detailed information about the pricing tiers and hardware levels, see [App Service pricing](https://azure.microsoft.com/pricing/details/app-service/windows/).

## Scale up a web app

**You scale an App Service plan up and down by changing the pricing tier and hardware level that it runs on.** You can start with the Free tier and scale up as needed according to your requirements. This process is manual. You can also scale down again if you no longer need the resources associated with a particular tier.

Scaling up can cause an interruption in service to client apps running at the time. They might need to disconnect from the service and reconnect if the scale-up occurs during an active call to the web app. And new connections might be rejected until scaling finishes. Also, scaling up can cause the outgoing IP addresses for the web app to change. If your web app depends on other services that have firewalls restricting incoming traffic, you'll need to reconfigure these services.

As with scale-out, you should monitor the performance of your system to ensure that scaling up (or down) has the desired effect. It's also important to understand that scale up and scale out can work cooperatively together. If you have scaled out to the maximum number of instances available for your pricing tier, you must scale up before you can scale out further.

# Exercise - Scale up a web app

Scaling up provides more powerful resources for running a web app. It also increases the number of instances available for scaling out.

In the hotel reservation system, you should scale out to handle the increasing number of visitors to the web app. Scaling up will enable you to scale out further. Scaling up will also likely be necessary to support the new functionality that will be added to the web app.

In this exercise, you'll scale up the hotel reservation system web app that you deployed earlier. You'll run the same test client application that you used before, and monitor the performance of the web app.

## Examine the current pricing tier for the web app

Sign in to the Azure portal .

On the Azure portal menu or from the **Home** page, select **All resources**, and then go to your App Service plan.

Under **Settings**, select **Scale up (App Service plan)**. You should see details of the pricing tier for your App Service plan. The pricing tier is S1, which provides 100 Azure Compute Units and 1.75 GB of memory running on an A-Series virtual machine.

![https://docs.microsoft.com/en-us/learn/modules/app-service-scale-up-scale-out/media/5-pricing-tier.png](https://docs.microsoft.com/en-us/learn/modules/app-service-scale-up-scale-out/media/5-pricing-tier.png)

## Run the test client app

In the Cloud Shell window on the right side of the screen, go to the _~/mslearn-hotel-reservation-system/src/HotelReservationSystemTestClient_ folder:

```sh
cd ~/mslearn-hotel-reservation-system/src/HotelReservationSystemTestClient
```

Run the client app. Allow the system to run for a couple minutes. As at the start of the previous exercise, the responses will be slow, and the client requests will soon start to fail with HTTP 408 (Timeout) errors.

```sh
dotnet run
```

Keep running the app. Wait for another five minutes. Then go to the chart that shows the metrics for the web app on the dashboard in the Azure portal. As in the previous exercise, you should see that the statistics indicate a relatively slow response time and many HTTP 4xx errors.

## Scale up the web app and monitor the results

# Summary
