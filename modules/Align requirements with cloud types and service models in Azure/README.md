[Align requirements with cloud types and service models in Azure](https://docs.microsoft.com/en-us/learn/modules/align-requirements-in-azure/) (~35 mins)

# Introduction

Cloud computing provides configurable computing resources and services that can be quickly provisioned with minimal management effort. There are Azure services for almost any business computing need. Do you need an interactive website? A back end for a mobile app? Secure storage for your client data? All of these and more are available. And these resources are available when you need them, and you're typically only charged for the amount you use.

Hopefully, the cloud is an attractive option, but what if you already have existing on-premises infrastructure? Or you have highly sensitive data that you're not willing or able to store off-site?

Azure provides several configurations allowing you to blend or transition between on-premises and a solution hosted completely in the cloud.

## Learning objectives

In this module, you will:

- Compare each cloud computing deployment model (public, private, and hybrid).
- Understand the advantages of each cloud computing service model.
- Decide which deployment and service model you should use for your services.

# Azure solutions for public, private, and hybrid cloud

Imagine you work at a healthcare company. You have legacy systems, line of business systems, and future plans for new systems. You've heard there are advantages to using cloud computing. How do you choose the best deployment model for different solutions: public, private, or hybrid cloud?

## What is cloud computing?

Cloud computing is the provisioning of services and applications on demand over the internet. Servers, applications, data, and other resources are provided as a service.

To the user, the details of the services are abstracted. You can quickly provision computing resources and use the service with minimal management. You shouldn't think of cloud computing as a datacenter available through the internet. Cloud computing uses virtualization, commodity hardware, and automated processes to provide a self-service user experience to customers similar to a public utility.

There are three deployment models for cloud computing: public cloud, private cloud, and hybrid cloud. The following illustration shows an overview of these deployment models.

![https://docs.microsoft.com/en-us/learn/modules/align-requirements-in-azure/media/2-cloud-deployment.png](https://docs.microsoft.com/en-us/learn/modules/align-requirements-in-azure/media/2-cloud-deployment.png)

[VIDEO: Public versus Private versus Hybrid (~2:35)](https://www.microsoft.com/en-us/videoplayer/embed/RE2yEv7?pid=RE2yEv7-ax-86-id-oneplayer&postJsllMsg=true&autoplay=false&mute=false&loop=false&market=en-us&playFullScreen=false)

## Public cloud

Public clouds are the most common way of deploying cloud computing. Services are offered over the public internet and available to anyone who wants to purchase them. The cloud resources such as servers and storage are owned and operated by a third-party cloud service provider and delivered over the internet. Services may be free or sold on demand, allowing customers to pay only per usage for the CPU cycles, storage, or bandwidth they consume. Microsoft Azure is an example of a public cloud.

Let's imagine your healthcare company needs an enrollment website. The site needs to scale and be responsive during peak enrollment various times during the year. Your customers access the site from global locations. You can use the public cloud to automatically scale up to meet demand at peak enrollment. When site traffic is low, your site can scale down to save costs. Your site is responsive at peak demand, and you only pay for more resources when needed. You can also deploy your website in multiple geographic regions to increase reliability and responsiveness.

During the development of your website, developers want to create multiple development environments to speed their development process. Developers can use the public cloud to quickly provision virtual machines for sandboxed environments to develop a solution. When the developers no longer need an environment, they can delete it.

### Why public cloud?

Public clouds can be deployed faster than on-premises infrastructures and with an almost infinitely scalable platform. Every employee of a company can use the same application from any office or branch using their device of choice as long as they can access the internet.

Examples of why you would use public cloud:

- **Service consumption through on-demand or subscription model**: The on-demand or subscription model allows you to pay for the portion of CPU, storage, and other resources that you use or reserve.
- **No up-front investment of hardware**: No requirement to purchase, manage, and maintain on-premises hardware and application infrastructure. The cloud service provider is held responsible for all management and maintenance of the system.
- **Automation**: Quickly provision infrastructure resources using a web portal, scripts, or via automation.
- **Geographic dispersity**: Store data near your users, or in desired locations without having to maintain your own datacenters.
- **Reduced hardware maintenance**: The service provider is responsible for hardware maintenance.

## Private cloud

A private cloud consists of computing resources used exclusively by users from one business or organization. It can be physically located at your organization’s on-site datacenter, or it can be hosted by a third-party service provider. The term private cloud should not be considered a rebranding of traditional on-premises datacenters. A private cloud uses on-premises infrastructure and services to provide similar benefits of the public cloud. It uses an abstraction platform to provide cloud-like services such as Kubernetes clusters or a complete cloud environment like Azure Stack. The organization is responsible for the purchase, configuration, and maintenance of the hardware. Communication between the systems is usually on the network infrastructure that the business owns and maintains. For example, a private internal network or a dedicated fiber optic connection between buildings.

Imagine you work at healthcare company and you have an application that is in use at one of your datacenters. The operating environment can't be replicated in the public cloud. You have a new requirement to access data at another one of your datacenters. The database containing the data needs to remain at the other site because of regulatory compliance. This scenario is a private cloud. You have two datacenters your organization owns. You could use a public cloud VPN over the internet to connect the datacenters. However, the scenario would be considered a private cloud since the solution is private to the organization.

### Why private cloud?

A private cloud can provide more flexibility to an organization. Your organization can customize its cloud environment to meet specific business needs. Since resources are not shared with others, high levels of control and security are possible. Also, private clouds can provide a level of scalability and efficiency.

Examples of why you would use private cloud:

- **Pre-existing environment**: An existing operating environment that can't be replicated in the public cloud. A large investment in hardware and employees with solution expertise. A large organization may choose to commoditize their computing resources.
- **Legacy applications**: Business-critical legacy applications that can't easily be physically relocated.
- **Data sovereignty and security**: Political borders and legal requirements may dictate where data can physically exist.
- **Regulatory compliance / certification**: PCI or HIPAA compliance. Certified on-premises datacenter.

## Hybrid cloud

A hybrid cloud is a computing environment that combines a public cloud and a private cloud by allowing data and applications to be shared between them. When computing and processing demand fluctuates, hybrid cloud computing gives businesses the ability to seamlessly scale their on-premises infrastructure up to the public cloud to handle any overflow - without giving third-party datacenters access to the entirety of their data. Organizations gain the flexibility and computing power of the public cloud for basic and non-sensitive computing tasks, while keeping business-critical applications and data on-premises, safely behind a company firewall.

Using a hybrid cloud helps eliminate the need to make up-front capital expenditures to handle short-term spikes in demand. It also has the flexibility to manage which resources are local versus resources in the cloud. Companies pay only for resources they temporarily use instead of having to purchase, program, and maintain additional resources and equipment that could remain idle over long periods of time. Integration is generally through a secure VPN between cloud providers like Azure and on-premises datacenters.

Imagine you work at healthcare company and you have an application where customers can access their healthcare information. A regulation requires that the data needs to remain at a physical location. The customer website needs to be responsive to their many global users. As a solution, the database could be hosted in an on-premises datacenter and the website could be hosted in the public cloud. A VPN is used between the on-premises datacenter and the public cloud. This scenario would be considered a hybrid cloud.

### Why hybrid cloud?

Hybrid cloud allows your organization to control and maintain a private infrastructure for sensitive assets. It also gives you the flexibility to take advantage of additional resources in the public cloud when you need them. With the ability to scale to the public cloud, you pay for extra computing power only when needed. It can also ease transitioning to the cloud. You can migrate gradually by phasing in workloads over time.

Examples of why you would use hybrid cloud:

- **Existing hardware investment**: Business reasons require that you use an existing operating environment and hardware.
- **Regulatory requirements**: Regulation requires that the data needs to remain at a physical location.
- **Unique operating environment**: Public cloud can't replicate a legacy operating environment.
- **Migration**: Move workloads to the cloud over time.

## Check your knowledge

1. Which cloud deployment model would be the best match for the following situation? A SQL server database is needed for a short-term project. The IT department does not have available hardware that meets the performance requirements or resources to deploy it. The project starts next week. Once the project is over, the database is no longer needed.

[ x ] Public cloud
[] Private cloud
[] Hybrid cloud

2. Which cloud deployment model is described in the following scenario? You create several virtual machines in the cloud. The VMs are networked together using a virtual network. The VMs have access to x-ray image files in cloud storage. One of the VMs is a web server that host a website exposed to the Internet for customers to access their records. There is a VPN that connects the solution to your on-premises datacenter for customer information to display with the image files.

[] Public cloud
[] Private cloud
[ x ] Hybrid cloud

3. Which cloud deployment model is described in the following scenario? You have two datacenters in your organization. One datacenter has a database that can only reside in one of the datacenters because of a regulatory requirement. You want to access the database from the other datacenter. You create a VPN using a cloud provider to connect the two datacenters.

[] Public cloud
[ x ] Private cloud
[] Hybrid cloud

# Cloud service models

Cloud computing resources are delivered using three different service models.

- **Infrastructure-as-a-service (IaaS)** provides instant computing infrastructure that you can provision and manage over the Internet.
- **Platform as a service (PaaS)** provides ready-made development and deployment environments that you can use to deliver your own cloud services.
- **Software as a service (SaaS)** delivers applications over the Internet as a web-based service.

[VIDEO: IaaS versus SaaS versus PaaS (~2:32)](https://www.microsoft.com/en-us/videoplayer/embed/RE2yEbs?pid=RE2yEbs-ax-87-id-oneplayer&postJsllMsg=true&autoplay=false&mute=false&loop=false&market=en-us&playFullScreen=false)

When choosing a service model, consider which party should be responsible for the computing resource. Based on your scenario, you can decide how much shared management responsibility you want. The following illustration shows a list of resources that you manage and that your service provider manages in each cloud service category.

![https://docs.microsoft.com/en-us/learn/modules/align-requirements-in-azure/media/3-shared-responsibility.png](https://docs.microsoft.com/en-us/learn/modules/align-requirements-in-azure/media/3-shared-responsibility.png)

# Infrastructure as a service

Infrastructure as a service (IaaS) is an instant computing infrastructure, provisioned and managed over the Internet. IaaS enables you to quickly scale resources to meet demand and only pay for what you use. IaaS avoids the expense and complexity of buying and managing your own physical servers and other datacenter infrastructure. Each resource is offered as a separate service component, and you rent the resource as long as you need it. As a result, IaaS is flexible. You can provision common infrastructure such as VMs, storage, virtual subnets, firewalls, and VPNs to build a solution. You don't need to manage physical servers and appliances. However, you are responsible for configuring and managing the components. For example, configuring firewalls, updating VM OS's, updating DBMS's, and runtimes.

## Common scenarios

Let's imagine your healthcare company has a need to run a special version of desktop software. The software is only supported on a specific version of an operating system and only one user license is required. You can create a virtual machine with the required software. The user can use a remote desktop connection to connect to the virtual machine to use the software.

Let's imagine another scenario. Your development teams need several unique development environments. Through the development cycle, they need to test various versions of the product. The developers can provision environments when needed. When an environment is no longer needed, it can be easily deleted.

Some other common scenarios include:

- **Website hosting**: If you want more control of hosting a website, running websites using IaaS may be a better option than traditional web hosting.
- **Web apps**: IaaS provides all the infrastructure to support web apps, including storage, web and application servers, and networking resources. Organizations can quickly deploy web apps on IaaS and easily scale infrastructure up and down when demand for the apps is unpredictable.
- **Storage, backup, and recovery**: Storage management can be complex, requiring a large capital investment and skilled staff to manage data and meet legal and compliance requirements. IaaS can help simplify planning, management, unpredictable demand, and steadily growing storage needs.
- **High-performance computing**: If you have a workload that requires high-performance computing, you can run the workload in the cloud avoiding the up-front cost of the hardware and only pay for the usage when needed.
- **Big data analysis**: If you have large data sets that contain potentially valuable patterns, trends, and associations, IaaS can provide the processing power to mine data sets to locate patterns.

## Advantages

**Eliminates capital expense and reduces ongoing cost**: IaaS sidesteps the upfront expense of setting up and managing an on-site datacenter, making it an economical option for start-ups and businesses testing new ideas. As soon as you’ve decided to launch a new product or initiative, the necessary computing infrastructure can be ready in minutes or hours, rather than the days or weeks—and sometimes months—it could take to set up internally.

**Improves business continuity and disaster recovery**: Achieving high availability, business continuity, and disaster recovery is expensive, since it requires a significant amount of technology and staff. But with the right service level agreement (SLA) in place, IaaS can reduce this cost and access applications and data as usual during a disaster or outage.

**Respond quicker to shifting business conditions**: IaaS enables you to quickly scale up resources to accommodate spikes in demand for your application— during the holidays, for example—then scale resources back down again when activity decreases to save money. Because you don’t need to first set up the infrastructure before you can develop and deliver apps, you can get them to users faster with IaaS.

**Increase stability, reliability, and supportability**: With IaaS, there’s no need to maintain and upgrade hardware or troubleshoot equipment problems. With the appropriate agreement in place, the service provider assures that your infrastructure is reliable and meets SLAs.

# Platform as a service

Platform as a service (PaaS) is a complete development and deployment environment in the cloud. With PaaS, you can build and deploy everything from simple cloud-based apps to sophisticated, cloud-enabled enterprise applications. You purchase the resources from a cloud service provider on a pay-as-you-go basis and access them over a secure Internet connection. Like IaaS, PaaS includes infrastructure such as servers, storage, and networking. In addition, it also includes middleware, development tools, and other services. PaaS supports the complete web application lifecycle: building, testing, deploying, managing, and updating. PaaS removes the need to manage software licenses, middleware, and infrastructure of the services. You manage the applications and services you develop, and the cloud service provider typically manages everything else.

## Common scenarios

Let's imagine your healthcare company needs a website to describe a product. Your developers want to use PHP. Using PaaS, your developers have the option to create a web app. The infrastructure details such as creating a virtual machine, installing a web server, and installing middleware are abstracted away. You don't need to care what operating system it runs on or what physical hardware is required. Your developers deploy the website files to the cloud and your website is available on the Internet.

Let's imagine another scenario. Your company needs a SQL database to support data analysts for a special project. You don't have infrastructure to accommodate the request. You can quickly provision a SQL Server in the cloud that meets the need of the project. The data analysts can connect to the server. The SQL Server database is provided as a service. Therefore, you don't worry about updates, security patches, or optimizing physical storage for reads and writes.

Some other common scenarios include:

**Development framework**: PaaS provides a framework that developers can build upon to develop or customize cloud-based applications. Similar to the way you create an Excel macro, PaaS lets developers create applications using built-in software components. Cloud features such as scalability, high-availability, and multi-tenant capability are included, reducing the amount of coding that developers must do.

**Analytics or business intelligence**: Analysis tools provided as a service allows you to analyze and mine data. Organizations can find insights and patterns to predict outcomes to improve forecasting, product design decisions, investment returns, and other business decisions.

## Advantages

By delivering infrastructure as a service, PaaS **has similar advantages as IaaS**. But its **additional features including middleware, development tools, and other business tools** provide additional advantages:

**Reduced development time**: PaaS development tools can reduce development time for new applications. Developers can use pre-coded application components built into the platform, such as workflow, directory services, security features, and search. Platform as a service components can give your development team new capabilities without you needing to add staff having the required skills.

**Develop for multiple platforms**: Some service providers give you development options for multiple platforms, such as desktop, mobile devices, and browsers making cross-platform apps quicker and easier to develop.

**Use sophisticated tools affordably**: A pay-as-you-go model makes it possible for individuals or organizations to use sophisticated development software and business intelligence and analytics tools that they could not afford to purchase outright.

**Support geographically distributed development teams**: Because the development environment is accessed over the Internet, development teams can work together on projects even when team members are at remote locations.

**Efficiently manage the application lifecycle**: PaaS provides all of the capabilities that you need to support the complete web application lifecycle: building, testing, deploying, managing, and updating within the same integrated environment.

# Software as a service

Software as a service (SaaS) allows users to connect to and use cloud-based apps over the Internet. Common examples are email, calendaring, and office tools such as Microsoft Office 365. SaaS provides a complete software solution that you purchase on a pay-as-you-go basis from a cloud service provider. You can rent the use of an application for your organization. Your users connect to the service over the Internet, usually with a web browser. All of the underlying infrastructure, middleware, app software, and app data are located in the service provider’s data center. The service provider manages the hardware and software, and with the appropriate service agreement, will ensure the availability and the security of the app and your data as well. SaaS allows your organization to get quickly up and running with an app at minimal upfront cost.

If you’ve used a web-based email service such as Outlook, Hotmail, or Yahoo! Mail, then you’ve already used a form of SaaS. With these services, you log into your account over the Internet, often from a web browser. The email software is located on the service provider’s network, and your messages are stored there too. You can access your email and stored messages from a web browser on any computer or Internet-connected device.

## Common scenarios

Let's imagine your healthcare company requires a customer relationship management (CRM) solution for its sales team. The team is global. You can use a SaaS CRM provider to quickly implement a solution to your organization's sales team.

For organizational use, you can rent productivity apps, such as email, collaboration, and calendaring; and sophisticated business applications such as customer relationship management (CRM), enterprise resource planning (ERP), and document management. You pay for the use of these apps by subscription or according to the level of use.

## Advantages

**Gain access to sophisticated applications**: To provide SaaS apps to users, you don’t need to purchase, install, update, or maintain any hardware, middleware, or software. SaaS makes even sophisticated enterprise applications, such as ERP and CRM, affordable for organizations that lack the resources to buy, deploy, and manage the required infrastructure and software themselves. Pay only for what you use. You also save money because the SaaS service automatically scales up and down according to the level of usage.

**Use free client software**: Users can run most SaaS apps directly from their web browser without needing to download and install any software. You don't need to purchase or deploy client software for your users.

**Access app data from anywhere**: With data stored in the cloud, users can access their information from any Internet-connected computer or mobile device. And as app data is stored in the cloud, no data is lost if a user’s computer or device fails.

# Knowledge check - IaaS, PaaS & SaaS

## Check your knowledge

1. Which cloud service model would be the best match for the following situation? A SQL Server database is needed for a short-term project. The IT department does not have available hardware that meets the performance requirements or resources to deploy it. The project starts next week. Once the project is over, the database is no longer needed.

[] IaaS
[ x ] PaaS
[] SaaS

2. Which cloud service model is described in the following scenario? You create several virtual machines in the cloud. The VMs are networked together using a virtual network. The VMs have access to x-ray image files in cloud storage. One virtual machine is a web server that hosts a website exposed to the internet for customers to access their records. There is a VPN that connects the solution to your on-premises datacenter for customer information to display with the image files.

[ x ] IaaS
[] PaaS
[] SaaS

3. Which cloud service model would be the best match for the following situation? You work at a start-up company with a small number of employees who need to collaborate. They need email, calendar scheduling, and somewhere to store documents. The team is technical but do not have the time or hardware to implement and manage a solution.

[] IaaS
[] PaaS
[ x ] SaaS

# Summary

In this module, you explored the concepts of cloud computing. Cloud computing is the provisioning of services and applications on demand over the internet. Servers, applications, data, and other resources are provided as a service.

Cloud computing resources are delivered using three different service models:

- Infrastructure-as-a-service (IaaS) provides instant computing infrastructure that you can provision and manage over the Internet.
- Platform as a service (PaaS) provides ready-made development and deployment environments that you can use to deliver your own cloud services.
- Software as a service (or SaaS) delivers applications over the Internet as a web-based service.
