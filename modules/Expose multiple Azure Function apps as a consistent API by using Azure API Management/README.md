[Expose multiple Azure Function apps as a consistent API by using Azure API Management](https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/) (~41 mins)

Combine multiple Azure Functions apps into a unified interface by importing them into a single Azure API Management instance.

In this module, you will:

- Identify the value of Azure API Management in a serverless application
- Import an Azure Functions app as an API in Azure API Management
- Import multiple Azure Functions apps as a single API in Azure API Management

Prerequisites:

- Basic knowledge of Azure API Management and Azure Functions

# Notes and key takeaways

## Introduction

You can use Azure Functions and Azure API Management to build complete APIs with a microservices architecture.

Suppose you work for an online store with a successful and busy web site. Your developers have written the business logic for the site as microservices in the form of Azure Functions. Now, you want to enable partners to interact with your online store from their own code by creating a web API that they can call over HTTP. You want to find an easy way to assemble your functions into a single API.

In this module, you will learn how to use API Management to assemble multiple microservices, implemented as Azure Functions, into an integrated web API.

## Create a new API in API Management from a function app

The Azure API Management (APIM) service enables you to construct an API from a set of disparate microservices.

In your online store, you have implemented each part of the application as a microservice - one for the product details, one for order details, and so on. A separate team manages each microservice and each team uses continuous development and delivery to update and deploy their code on a regular basis. You want to find a way to assemble these microservices into a single product and then manage that product centrally.

In this unit, you'll learn how Azure API Management is useful in a serverless architecture, by building single APIs from individual microservices.

### Serverless architecture and microservices

Microservices has become a popular approach to the architecture of distributed applications in recent years. When you build an application as a collection of microservices, you create many different small services. Each service has a defined domain of responsibility, and is developed, deployed, and scaled independently. This modular architecture results in an application that is easier to understand, improve, and test. It also makes continuous delivery easier because, when you deploy a microservice, you change only a small part of the whole application.

Another complimentary trend in distributed software development is serverless architecture. In this approach, a host organization publishes a set of services that developers can use to run their code. The developers do not have to concern themselves with the supporting hardware, operating systems, underlying software, and other infrastructure. Instead the code is run in stateless computing resources that are triggered by requests. Costs are only incurred when the services execute so you don't pay much for services that are rarely used.

### Azure Functions

Azure Functions is a service that enables serverless architectures in Azure. You can write functions, without worrying about the supporting infrastructure, in many different languages, including C#, Java, JavaScript, PowerShell, and Python. You can also use libraries from NuGet and the Node Package Manager (NPM) and authenticate users with the OAuth standard from providers such as Active Directory, Facebook, Google, and Microsoft Account.

When you write a function, you choose a template to use, depending on how you want to trigger your code. For example, if you want to execute the function in response to an HTTP request, use the `HTTPTrigger` template. You can use other templates to execute when there are new messages in a queue, a Blob storage container, or on a predefined schedule.

When you use Azure Functions in a Consumption Plan, you are charged only for the time that your code runs.

### Azure API Management

Azure API Management (APIM) is a fully managed cloud service that you can use to publish, secure, transform, maintain, and monitor APIs. It helps organizations publish APIs to external, partner, and internal developers to unlock the potential of their data and services. API Management handles all the tasks involved in mediating API calls, including request authentication and authorization, rate limit and quota enforcement, request and response transformation, logging and tracing, and API version management. APIM enables you to create and manage modern API gateways for existing backend services no matter where they are hosted.

Because you can publish Azure Functions through API Management, you can use them to implement a microservices architecture: Each function implements a microservice. By adding many functions to a single API Management product, you can build those microservices into an integrated distributed application. Once the application is built, you can use API Management policies to implement caching or ensure security requirements.

#### APIM Consumption Tier

When you choose a usage plan for API Management, you can choose the consumption tier, because it's especially suited to microservice-based architectures and event-driven systems. For example, it would be a great choice for our online store web API.

The consumption tier uses the same underlying service components as the previous tiers but employs an entirely different architecture based on shared, dynamically allocated resources. It aligns perfectly with serverless computing models. There is no infrastructure to manage, no idle capacity, high-availability, automatic scaling, and usage-based pricing, all of which make it an especially good choice for solutions that involve exposing serverless resources as APIs.

In the next unit, we'll create an API Management instance and add a function to it.

## Exercise - Create a new API in API Management from a function app

## The benefits of using Azure API Management to compose your API

## Exercise - Add another Azure Functions app to an existing API

## Summary
