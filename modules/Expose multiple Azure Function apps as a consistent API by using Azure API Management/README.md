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

You can add Functions to Azure API Management, to present them to users as parts of a single API.

In your online store company, your developers have created multiple Azure Functions as microservices. Each function implements a small part of the store's functionality. You want to assemble these functions into single API.

Here, you will create a new API Management instance and then add a Product Details function to it.

### Create functions

In this exercise, you will add an Azure Functions app to Azure API Management. Later you will add a second function app to the same API Management instance in order to create a single serverless API from multiple functions. Let's start by using a script to create the functions:

```sh
# Clone the repo to the Cloud Shell in our sandbox
rob@Azure:~$ git clone https://github.com/MicrosoftDocs/mslearn-apim-and-functions.git ~/OnlineStoreFuncs

# Run the following commands in the Cloud Shell to set up the necessary Azure resources we need for this exercise
rob@Azure:~$ cd ~/OnlineStoreFuncs
rob@Azure:~/OnlineStoreFuncs$ bash setup.sh
```

The `setup.sh` script creates the two function apps in the sandbox resource group that we've activated for this module. As the following graphic illustrates, each app hosts a single function - `OrderDetails` and `ProductDetails`. The script also sets up a storage account for the functions. The functions both have URLs in the **azurewebsites.net** domain. The function names include random numbers for uniqueness. The script takes a few minutes to complete.

![https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/media/3-script-results.png](https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/media/3-script-results.png)

### Test the product details function

Now, let's test the ProductDetails function, to see how it behaves before we add it to API Management:

Sign into the [Azure portal](https://portal.azure.com/learn.docs.microsoft.com) using the same account with which you activated the sandbox.

On the Azure portal menu or from the Home page, select All resources.

Choose the App Service whose name begins with **ProductFunction**. The Function Apps UI for this app opens in the portal.

Select Functions in the menu, then select **ProductDetails**.

![https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/media/3-select-function.png](https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/media/3-select-function.png)

Select **Code + Test**, then select **Test**, as highlighted in the following screenshot:

![https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/media/3-code-test-product-details-function.png](https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/media/3-code-test-product-details-function.png)

In the HTTP method drop-down list, select GET, and then select Add parameter.

In the name textbox, type `id` and in the value textbox, type `3`.

Select Run and examine the results in the Output box, then select Close.

![https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/media/3-test-output.png](https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/media/3-test-output.png)

The output pane displays the details of a product in JSON format. You can also test the function with IDs 1 and 2 for different products.

At the top of the page, select `Get function URL`. Notice that the URL is the name of the function within the azurewebsites.net domain. Make a note of this URL for later comparison.

You can use this URL to test the function in your browser. Append the query string `&id=1` to request a product.

### Expose our function app as an API using Azure API Management

Now that we have our function app deployed and tested, we'll expose the functionality as an API using Azure API Management so that the function can be called from other apps and services.

On the Azure portal menu or from the Home page, select All resources, and select the App Service whose name begins with **ProductFunction**.

Select **API Management**, and select **Create new** under API Management and use the following settings:

![https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/media/3-api-details.png](https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/media/3-api-details.png)

Choose **Create** to create the API Management instance, which may take several minutes, then select **Link API**.

![https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/media/3-link-api.png](https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/media/3-link-api.png)

The Import Azure Functions dialog opens with the ProductDetails function highlighted. Choose Select to continue.

![https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/media/3-import-azure-functions.png](https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/media/3-import-azure-functions.png)

In the Create from Function App page, change API URL suffix to products and select Create. The API is now created for the ProductDetails function.

![https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/media/3-create-from-function-app.png](https://docs.microsoft.com/en-us/learn/modules/build-serverless-api-with-functions-api-management/media/3-create-from-function-app.png)

Note that we were able to define our API all from within the function app portal experience.

### Test the OnlineStore products endpoint

## The benefits of using Azure API Management to compose your API

## Exercise - Add another Azure Functions app to an existing API

## Summary
