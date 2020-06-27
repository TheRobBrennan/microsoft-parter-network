[Choose the best Azure service to automate your business processes](https://docs.microsoft.com/en-us/learn/modules/choose-azure-service-to-integrate-and-automate-business-processes/) (~44 mins)

Microsoft Azure provides several different ways to host and execute code or workflows without using Virtual Machines (VMs) including Azure Functions, Microsoft Power Automate, Azure Logic Apps, and Azure WebJobs. In this module, you will learn about these technologies and how to choose the right one for a given scenario.

Upon completion of this module, you'll be able to:

- Evaluate Azure services for integration and process automation scenarios

Prerequisites:

- Familiarity with Azure
- Basic knowledge of REST and APIs

# Notes and key takeaways

Azure includes four different technologies that you can use to build and implement workflows that integrate multiple systems:

- Logic Apps
- Microsoft Power Automate
- WebJobs
- Azure Functions

When business analysts discuss and plan a business process, they may draw a flow diagram on paper. With Logic Apps and Microsoft Power Automate, you can take a similar approach to designing a workflow.

One reason why Logic Apps is so good at integration is that over 200 connectors are included. A connector is a Logic Apps component that provides an interface to an external service.

Microsoft Power Automate is a service that you can use to create workflows even when you have no development or IT Pro experience.

Under the hood, Microsoft Power Automate is built on Logic Apps. This fact means that Power Automate supports the same range of connectors and actions. You can also use custom connectors in Microsoft Power Automate.

![https://trello-attachments.s3.amazonaws.com/5ec837ee31f7fa31fcca2a10/877x354/15f02fdbd18fb57d8dee49cd7f098f45/Screen_Shot_2020-05-22_at_13.51.23_PM.png](https://trello-attachments.s3.amazonaws.com/5ec837ee31f7fa31fcca2a10/877x354/15f02fdbd18fb57d8dee49cd7f098f45/Screen_Shot_2020-05-22_at_13.51.23_PM.png)

![https://trello-attachments.s3.amazonaws.com/5ec837ee31f7fa31fcca2a10/889x653/9d004d6b522c4a6ef4f9a71be51522ff/Screen_Shot_2020-05-22_at_13.54.49_PM.png](https://trello-attachments.s3.amazonaws.com/5ec837ee31f7fa31fcca2a10/889x653/9d004d6b522c4a6ef4f9a71be51522ff/Screen_Shot_2020-05-22_at_13.54.49_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec837ee31f7fa31fcca2a10/e5883dd266734a14f9d95074f3706390/3-service-choice-flow-diagram.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec837ee31f7fa31fcca2a10/e5883dd266734a14f9d95074f3706390/3-service-choice-flow-diagram.png)

They keep hammering this idea in WebJobs - You have specific customizations that you want to make to the JobHost that are not supported by Azure Functions. For example, in a WebJob, you can create a custom retry policy for calls to external systems. This kind of policy can't be configured in an Azure Function.

![https://trello-attachments.s3.amazonaws.com/5ec837ee31f7fa31fcca2a10/907x1134/218a46e180b52a04d9803b5d262ca4b1/4-bike-hire-workflow.png](https://trello-attachments.s3.amazonaws.com/5ec837ee31f7fa31fcca2a10/907x1134/218a46e180b52a04d9803b5d262ca4b1/4-bike-hire-workflow.png)

Cost: With Web Jobs, you pay for the entire VM or App Service Plan that hosts the job. Azure Function can run on a consumption plan, so you only pay when the function runs. Since this process only kicks off when a bike is returned, we might stand to save by selecting Azure Functions.

Oof. Missed two out of the four questions in the knowledge check.

Here's what I need to keep in mind:

- WebJobs are the only technology that permits developers to control retry policies.
- Azure Logic Apps is the only one of the four technologies that provides a design-first approach intended for developers.
