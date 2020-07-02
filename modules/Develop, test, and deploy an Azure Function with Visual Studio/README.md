[Develop, test, and deploy an Azure Function with Visual Studio](https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/) (~43 mins)

Create test and deploy Azure Functions using Visual studio and how to manage Azure Function code.

In this module, you will:

- Develop an Azure Function in Visual Studio
- Test and debug an Azure Function in Visual Studio
- Deploy an Azure Functions directly from Visual Studio to Azure
- Manage Azure Function code

Prerequisites:

- Basic knowledge of Azure Functions
- Experience developing solutions in Visual Studio or a similar integrated development environment
- The exercises in this module require a local installation of Visual Studio 2019.

# Notes and key takeaways

## Introduction

Serverless architecture makes use of infrastructure provided by the cloud. You don't have to provision, manage, scale, and maintain any machinery or networks. Azure Functions is a fully managed PaaS service offered by Microsoft Azure to implement serverless architecture. Azure Functions is a fully scalable, resilient, reliable, and secure service.

Imagine that you're a senior web developer in a research role for an online luxury watch dealer, building a website based on Azure Web Apps. You've been asked to provide new functionality by writing and deploying individual methods that can be deployed, manually executed, scheduled, or even triggered by other Azure Services. The company follows a test-driven development process, so you're also required to write automated unit tests for the new services. You have a team of developers well-versed with Microsoft technologies including the .NET framework and Visual Studio. You decide to investigate the use of Azure Functions to meet your requirements.

By the end of this module, you'll be able to build and test an Azure Function locally using Visual Studio. You'll then see how to deploy the function to Azure from Visual Studio.

## Create and test a simple Azure Function locally with Visual Studio

You can write, debug, and deploy an Azure Function from within the Azure portal. However, there are many scenarios when writing functions directly in the production, staging, or test environments might not be suitable. For example, you could be writing automated unit tests for Azure Functions, or using on-demand deployment of Azure Functions to Azure Function Apps in Azure. Additionally, many developers prefer to use their favorite code editor and development tools rather than the environment provided by the Azure portal. Developing functions with Visual Studio enables you to manage Azure Functions code with code of other services inside the same projects.

In the luxury watch online website scenario, the developers are already familiar with Visual Studio. So, you decide to use Visual Studio as the primary development environment for creating Azure Functions. Additionally, Visual Studio provides an excellent environment for testing your functions locally before deploying them to Azure.

In this unit, you'll learn about the tools available with Visual Studio for building Azure Functions. You'll see how to install these tools, and use them to build and test an Azure Function locally.

### Install the Azure Functions tools extension for Visual Studio

The Azure Functions tools are a Visual Studio extension that allows you to create, test, and deploy Azure Functions in your local development environment. This extension provides a template that you can use to quickly create a new Azure Functions app. You can then deploy an Azure Function directly to Azure from Visual Studio.

You install the Azure Functions tools from the Extensions and Updates command in the Tools menu in Visual Studio. Search for Azure Functions, and then download and install the Azure Functions and Web Job Tools extension.

![https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/media/2-azure-functions-and-web-jobs-extension.png](https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/media/2-azure-functions-and-web-jobs-extension.png)

### Create an Azure Functions app

When you've installed the extension, you create an Azure Functions app using the Azure Functions template. You'll find this template in the Cloud folder under Visual C# in the New Project dialog.

![https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/media/2-create-project.png](https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/media/2-create-project.png)

An Azure Function app hosts one or more Azure Functions. It provides the environment and runtime for the functions.

An Azure Function is triggered by an event rather than being called directly from an application. You specify the type of event that will trigger the functions in your function app. The events available include:

- **Blob trigger**. This type of function runs when a file is uploaded or modified in Azure Blob storage.
- **Event Hub Trigger**. An Event Hubs trigger runs the function when an Event Hub receives a message.
- **Azure Cosmos DB Trigger**. This trigger runs when a document is added to, or modified in, an Azure Cosmos DB database. You can use this trigger to integrate Azure Cosmos DB with other services. For example, if a document representing a customer's order is added to a database, you could use a trigger to send a copy of the order to a queue for processing.
- **Http Trigger**. An HTTP trigger runs the function when an HTTP request occurs in a web app. You can also use this trigger to respond to webhooks. A webhook is a callback that occurs when an item hosted by a website is modified. For example, you can create an Azure Function that is fired by a webhook from a GitHub repository when an item in the repository changes.
- **Queue Trigger**. This trigger fires the function when a new item is added to an Azure Storage Queue.
- **Service Bus Queue Trigger**. Use this trigger to run the function when a new item is added to an Azure Service Bus Queue.
- **Service Bus Topic Trigger**. This trigger runs the function in response to a new message arriving on a Service Bus Topic.
- **Timer Trigger**. Use this event to run the Azure Function at regular intervale, following a schedule that you define.

![https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/media/2-function-triggers.png](https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/media/2-function-triggers.png)

Azure currently provides two versions of the runtime environment required to run Azure Functions. **Version 1 (v1) uses the .NET Framework 4.7, whereas version 2 (v2) runs using .NET Core 2**. Using v2 triggers gives you the ability to develop and host the trigger in different environments. Version 1 triggers can only be created using Windows.

**Use v2 triggers where ever possible.**

An Azure Function app stores management information, code, and logs in Azure Storage. Create a Storage Account to hold this data. The storage account must support Azure Blob, Queue, Files, and Table storage; use a general Azure Storage account for this purpose. You specify which storage account to use for the function using the dialog box shown above.

An Azure Function can perform privileges or sensitive operations. An Azure Function triggered by an HTTP request could be exposed publicly. You might need to limit the ability to run this function to selected groups of users. You protect an Azure Function by specifying the access rights required to trigger the function. An Azure Function triggered by an HTTP request supports three levels of access rights:

- Anonymous. No authentication is required, and any user can trigger the function.
- Function. The HTTP request must provide a key that enables the Azure Function runtime to authorize the request. You create this key separately, and you can maintain it using the Azure portal.
- Admin. This is similar to Function inasmuch as the user must specify a key with the HTTP request that triggers the function. The difference is that the key is an admin key. This key can be used to access any function in the function app. As with a function key, you create this key separately.

If you're creating a function triggered by events other than HTTP requests, you're required to provide a connection string and other details necessary for the function app to access the resource triggering the event. For example, if you're writing a function triggered by a Blob storage event, you must specify the connection string for the corresponding Blob storage account.

### Structure of an Azure Function

An Azure Function is implemented as a static class. The class provides a static, asynchronous method named `Run`, which acts as the entry point for the function.

The parameters passed to the `Run` method provide the context for the trigger. In the case of an HTTP trigger, the function receives an _HttpRequest_ object. This object contains the header and body of the request. You can access the data in the request using the same techniques available in any HTTP application. The attributes applied to this attribute specify the authorization requirements (_Anonymous_ in this case), and the HTTP operations to which the Azure function responds (_GET_ and _POST_).

The example code generated by Visual Studio (shown below) examines the query string provided as part of the URL for the request and looks for a parameter called _name_. The code also uses a _StreamReader_ to deserialize the body of the request and attempts to read the value of a property also called _name_ from the request. If _name_ is found either in the query string or the body of the request, then it's returned in the response, otherwise the function generates an error response with the message _Please pass a name on the query string or in the request body._

```csharp
public static class Function1
{
    [FunctionName("Function1")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
        ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string name = req.Query["name"];

        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
        name = name ?? data?.name;

        return name != null
            ? (ActionResult)new OkObjectResult($"Hello, {name}")
            : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
    }
}
```

The function returns a value containing any output data and results, wrapped in an _IActionResult_ object. The value is returned in the body of the HTTP response for the request.

Different types of trigger receive different input parameters and return types. The next example shows the code generated for a Blob trigger. In this example, the contents of the blob is made accessible through a _Stream_ object, and the name of the blob is also provided. No data is returned by the trigger; its purpose is to read and process the data in the named blob:

```csharp
public static class Function2
{
    [FunctionName("Function2")]
    public static void Run([BlobTrigger("samples-workitems/{name}", Connection = "xxxxxxxxxxxxxxxxxxxxxxx")]Stream myBlob, string name, ILogger log)
    {
        log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
    }
}
```

In all cases, an Azure Function is passed an _ILogger_ parameter. The function can use this parameter to write log messages, which the function app will write to storage for later analysis.

An Azure Function also contains metadata that specify the type of the trigger and any other specific information and security requirements. You can modify this metadata using the _HttpTrigger_, _BlobTrigger_, or other trigger attributes, as shown in the examples. The _FunctionName_ attribute that precedes the function is an identifier for the function used by the function app. This name doesn't have to be the same as the name of the function, but it's good practice to keep them synchronized to avoid confusion.

### Test an Azure Functions app locally

## Exercise - Create and test a simple Azure Function locally with Visual Studio

## Publish a simple Azure Function

## Exercise - Publish a simple Azure Function

## Exercise - Unit test an Azure Function

## Summary
