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

You can use the Visual Debugger to build and test the Functions App locally. Press _F5_, or select _Start Debugging_ on the _Debug_ menu. The local version of the Function Runtime will start. Your functions will be available for testing. The example shows the runtime hosting the _Function1_. This is the function triggered by an HTTP event. The URL indicates the endpoint to which the function is currently attached.

![https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/media/2-function-runtime.png](https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/media/2-function-runtime.png)

If you open a web browser and visit this URL, you'll trigger the function. The image below shows the response generated by an HTTP GET request that doesn't include a body. You can see the message generated by the code that returns the _BadRequestObjectResult_ object from the function:

![https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/media/2-bad-request.png](https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/media/2-bad-request.png)

If you provide a query string that includes a name parameter, the value is read and processed by the function:

![https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/media/2-name-request.png](https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/media/2-name-request.png)

While the code is running, you'll see trace messages appearing in the Function Runtime window. You can use standard debugging features in Visual Studio if you need to set breakpoints and examine the flow of control in a function.

## Exercise - Create and test a simple Azure Function locally with Visual Studio

Visual Studio provides an excellent environment for creating and testing an Azure Functions app. You can develop an Azure Function locally, and verify that it operates correctly, before deploying it to the cloud.

In the luxury watch online website scenario, there's a requirement for a function that returns information about a type of watch when given the model number of the watch. The information includes data such as the manufacturer, the type of case back (solid, transparent, engraved), the bezel, the style of the dial, the finishing of the case, the number of jewels, and so on.

In this exercise, you'll implement a version of this function locally, and test it using Visual Studio.

### Setup

If you've installed Visual Studio 2019 on macOS, this entire section is unnecessary.

Please see [https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/3-exercise-develop-and-test-azure-functions-locally](https://docs.microsoft.com/en-us/learn/modules/develop-test-deploy-azure-functions-with-visual-studio/3-exercise-develop-and-test-azure-functions-locally) if you want to see how to install the `Azure Functions and Web Jobs Tools` extension in Visual Studio 2017.

### Create an Azure Functions app

1. Follow the prompts inside Visual Studio 2019 to create a new `Cloud` > `General` > `Azure Functions` app.
2. In the **Name** box type _WatchPortalFunction_, select a convenient location on your computer to store the project, and then click **OK**.
3. In the **New Project - WatchPortalFunction** dialog box, select **Azure Functions v2 (.NET Core)**, and then click **Http Trigger**. Leave the **Storage Account** set to **Storage Emulator** because you'll be running the function app locally to start with. Under **Access rights**, select **Anonymous**, and then click **OK**.
4. Wait while Visual Studio creates and configures the Azure Functions app. When it's complete, you'll see the code for a class named Function1 in the code window. This code contains the boilerplate code for an HTTP trigger. The Run method is annotated with the [FunctionName ("Function1")] attribute. Recall from the previous unit that the parameters to the Run method are an HttpRequest object containing the details of the request that triggered the function, and a reference to a trace log that you can use for recording trace information:

```csharp
namespace WatchPortalFunction
{
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
}
```

### Create the WatchInfo Azure Function

1. In the **Solution Explorer** window, right-click the **WatchPortalFunction** project, click **Add**, and then click **New Azure Function**.
2. In the **Name** box, type **WatchInfo**. In the window, click **Http trigger**. Under Access rights, click **Anonymous**, and then click OK.
3. Visual Studio creates a new Azure Function and adds it to the Azure Function app. This function has the same boilerplate code as `Function1`, except that the class is named `WatchInfo`, and the Run method is annotated with the [FunctionName("WatchInfo")] attribute.
4. Delete the code from the body of the Run function apart from the first statement that writes to the trace log. The method should look like this:

```csharp
namespace WatchPortalFunction
{
    public static class WatchInfo
    {
        [FunctionName("WatchInfo")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
        }
    }
}
```

5. Add the following code to the body of the method, after the statement that writes to the trace log. This code reads the `model` parameter from the query string from the URL of the HTTP request. The code then retrieves the details for this model of watch; in this example, the function simply returns some dummy data. Finally, the function returns a response that contains these details. If the initial request doesn't include a query string with the `model` parameter, the function returns an error message.

```csharp
namespace WatchPortalFunction
{
    public static class WatchInfo
    {
        [FunctionName("WatchInfo")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            // Retrieve the model id from the query string
            string model = req.Query["model"];

            // If the user specified a model id, find the details of the model of watch
            if (model != null)
            {
                // Use dummy data for this example
                dynamic watchinfo = new { Manufacturer = "Abc", CaseType = "Solid", Bezel = "Titanium", Dial = "Roman", CaseFinish = "Silver", Jewels = 15 };

                return (ActionResult)new OkObjectResult($"Watch Details: {watchinfo.Manufacturer}, {watchinfo.CaseType}, {watchinfo.Bezel}, {watchinfo.Dial}, {watchinfo.CaseFinish}, {watchinfo.Jewels}");
            }
            return new BadRequestObjectResult("Please provide a watch model in the query string");
        }
    }
}
```

### Test the Azure Function locally

1. On the **Debug** menu, click **Start Debugging**.

Visual Studio builds the Azure Function app and starts the Azure Functions runtime. You'll see a window appear displaying messages as the runtime starts up. When the runtime is ready, you'll see a list of the HTTP functions available, and the URL that you can use to trigger each function.

2. Open a web browser, and enter the URL http://localhost:7071/api/WatchInfo?model=abc. This request triggers the _WatchInfo_ function and passes the model _abc_ as the query string parameter. The web browser should display the dummy details generated by the Azure Function.

3. Enter the URL http://localhost:7071/api/WatchInfo. This request doesn't include a query string. The trigger returns the error response and the web browser displays the message Please provide a watch model in the query string.

4. Close the web browser, but leave the Azure Functions app running.

5. In Visual Studio, set a breakpoint on the line of code that retrieves the model from the query string.

6. Restart the web browser, and enter the URL without the query string again, http://localhost:7071/api/WatchInfo. You'll drop into Visual Studio at the breakpoint.

7. In Visual Studio, press F10 to step over the statement at the breakpoint.

8. In the Autos window, verify that the model variable is null. This is because the query string doesn't contain a model parameter.

9. Press F10 again, and verify that control jumps to the statement that returns a BadRequestObjectResult object.

10. Press F5 to continue running the method and return to the web browser. It should display the same error message as before.

11. In the web browser, enter the URL with a query string and a model parameter. Step through the Azure Function in the debugger, and verify that the model is retrieved correctly. The model variable should be populated with the value of the parameter, and the details of the model returned as an OkObjectResult object.

12. On the Debug menu, click Stop Debugging.

You've now seen how the Azure Functions Tools extension simplifies the experience of creating an Azure Functions app, and enables you to use familiar tools to build and debug your code.

## Publish a simple Azure Function

An Azure Function runs in the cloud in the context of an Azure Function App. A function app is a container that specifies the operating system for running an Azure Function, together with the resources available, such as the memory, computing power, and disk space. The Azure Function App also provides the public URL for running your functions. Behind the scenes, an Azure Function App is a collection of one or more virtual machines, running a web server. When you publish an Azure Function, you deploy it to these virtual machines.

In the luxury watch online website scenario, you'll deploy the Azure Functions you create to the cloud. You need to create an Azure Function App, and then publish your functions to this app. You want to determine the best way to do this.

There are several of options available for publishing an Azure Function. In this unit, you'll learn about some of these options.

### Deploy from Visual Studio

Azure Functions tools for Visual Studio enable you to deploy an Azure Function directly from Visual Studio. The Azure Functions template provides a Publish wizard. Using this wizard, you connect to your Azure account, and either specify an existing Azure Function App, or create a new one. The functions in your project are rebuilt and then deployed to the Azure Function App.

The **Publish** wizard requires that you either have access to the Azure Function App that will host your functions, or you have an Azure subscription that you can use to create an Azure Function App as part of the publication process. You'll investigate the **Publish** wizard in more detail in the exercise that follows this unit.

Deployment from Visual Studio is great feature for developers. If developers have access to an Azure subscription, they can create an Azure Function App and publish code to Azure, to perform testing in an environment that is similar to production environment. Once a function is stable, it can be deployed to an Azure Function App providing the production environment.

### Continuous deployment

Azure Functions makes it easy to deploy your function app using App Service continuous integration. Azure Functions integrates with BitBucket, Dropbox, GitHub, and Azure DevOps. This enables a workflow where function code updates made by using one of these integrated services triggers deployment to Azure.

Continuous deployment is a great option for projects where multiple and frequent contributions are being integrated. It also lets you maintain source control on your functions code.

The following deployment sources are currently supported:

- Bitbucket
- Dropbox
- External repository (Git or Mercurial)
- Git local repository
- GitHub
- OneDrive
- Azure DevOps

You can configure continuous deployment from the Azure portal, using the **Deployment Center** feature of an Azure Function App. Deployment is configured on a per-function app basis.

### Zip deployment

Azure Function can be deployed from a zip file using the push deployment technique. You can do this with the Azure CLI, or by using the REST interface.

The zip file contains the executable code for your functions. Zip deployment copies these files to the `wwwwroot` folder in the function app. You can perform zip deployment using the `functionapp deployment` command in the Azure CLI.

```sh
az functionapp deployment source config-zip \
-g <resource-group> \
-n <function-app-name> \
--src <zip-file>
```

## Exercise - Publish a simple Azure Function

The **Publish** wizard in the Azure Functions template provides the most straightforward way to deploy Azure Functions, especially for testing purposes.

In the luxury watch online website scenario, you decide to use Visual Studio to deploy your functions to an Azure Functions App in the cloud. First, you'll create an Azure Function App, and you'll use the **Publish** wizard in Visual Studio to deploy your functions.

In this exercise, you'll see how to use the Azure portal to create an Azure Functions App. Then, you'll deploy the **WatchInfo** function. Finally, you'll test the function to verify that it has been deployed correctly.

### Create an Azure Functions App using the Azure portal

1. Sign into the [Azure portal](https://portal.azure.com/learn.docs.microsoft.com) using the same account you activated the sandbox with.
2. From the Azure portal menu, select **Create a resource**.
3. Select **Compute**, and then select **Function App**
4. In the Function App window, specify the following settings:

| Property       | Value                                                                      |
| -------------- | -------------------------------------------------------------------------- |
| Name           | rbwatchfunctions                                                           |
| Subscription   | Concierge Subscription                                                     |
| Resource Group | Use the existing resource group learn-c28fcc89-f560-44a5-8d27-244be69814b3 |
| OS             | Windows                                                                    |
| Hosting Plan   | Consumption                                                                |
| Location       | Accept the default location                                                |
| Runtime Stack  | .NET                                                                       |
| Storage        | Create new, accept the default name                                        |

The function app must have a unique name. I will use `rbwatchfunctions` wherever I need to substitute a value <your-function-app-name> in this exercise.

5. Click **Create**, and wait while the function app is created before continuing.

### Deploy the WatchInfo function to the Azure Functions App

1. On your desktop, return to Visual Studio.
2. In the **Solution Explorer** window, right-click the **WatchPortalFunction** project, and then click **Publish**.
3. In the **Pick a publish target** window, under **Azure App Service**, click **Select Existing**, and then click **Publish**
4. In the **App Service** window, select your Azure account. If you aren't already signed in to Azure, then click **Add an account** first and sign in.
5. After signing in, enter the following details in the **App Service** window.

| Property     | Value                  |
| ------------ | ---------------------- |
| Subscription | Concierge Subscription |
| View         | Resource Group         |

6. In the lower **Search** box, expand the resource group that is displayed, click your Azure Functions App, and then click **OK**.
7. Wait for your functions to be deployed. The **Output** window in Visual Studio shows the progress of the build and publish processes. If everything works, the final two messages should be **Publish Succeeded** and **Publish completed**.

### Verify the functions have been deployed

1. Return to the Azure portal.
2. On the Azure portal menu or from the Home page, select All resources.
3. Select <your-function-app-name>
4. In the <your-function-app-name> window, expand Functions, and verify that the **Function1** and **WatchInfo** functions are listed. In the right pane, check that the status of the Azure Functions App is Running. The functions are marked as Read Only. You cannot modify their settings or configuration from the Azure portal because you published them from Visual Studio. If you need to update either of these functions, make the changes in Visual Studio and publish them again.
5. In the right pane, click the URL (highlighted in the image above). A browser window will open, and confirm that the functions app is up and running - [http://rbwatchfunctions.azurewebsites.net](http://rbwatchfunctions.azurewebsites.net)
6. Append the URL of the watchinfo function, `api/watchinfo` to the URL in the browser and refresh the display. This invokes the **WatchInfo** function without a query string. You'll see the error response with the message `Please provide a watch model in the query string`.
7. Add the querystring `?model=abc` to the URL and refresh the browser window. This time, you'll see the details of the watch model returned by the function.

You've now created an Azure Functions App in the cloud, and published your functions to this app from Visual Studio.

## Exercise - Unit test an Azure Function

Unit testing is a fundamental part of an Agile methodology. Visual Studio provides the Test project template. You use this template to create the unit tests for your applications, and you can apply the same technique to testing for Azure Functions.

In the luxury watch online website scenario, your development team has a policy to `achieve at least 80% coverage of code` in unit testing. You would like to implement the same policy for Azure Functions.

In this unit, you'll see how to use the `xUnit` test framework with Visual Studio to test Azure Functions.

### Create a unit test project

The first step is to create a project that contains your unit tests, and add it to the solution holding your Azure functions app. Use the following steps to create a unit test project for testing the _WatchInfo_ function.

1. In Visual Studio, in the Solution Explorer window, right-click the **WatchPortalFunction** solution, click **Add**, and then click **New Project**.
2. In the Add New Project dialog box, under Visual C#, click Test. Select the xUnit Test Project (.NET Core) template. Specify the name WatchFunctionsTests, and then click OK.
3. When the project has been added, right-click the WatchFunctionTests project in the Solution Explorer window, and then click Manage NuGet Packages.
4. In the NuGet: WatchFunctionTests window, click the Browse tab. In the Search box, type Microsoft.AspNetCore.Mvc. Click the Microsoft.AspNetCore.Mvc package, and then click Install.
5. Wait while the package in installed. If the Preview Changes message box appears, click OK. In the License Acceptance message box, click I Accept.
6. Once the package has been added, in the Solution Explorer window, under the WatchFunctionsTest project, right-click the UnitTest1.cs file and then click Rename. Change the name of the file to WatchFunctionUnitTests.cs. In the message box that appears, click Yes to rename all references of UnitTest1 to WatchFunctionUnitTests.
7. In the Solution Explorer window, under the WatchFunctionsTest project, right-click Dependencies, and then click Add Reference.
8. In the Reference Manager window, select the WatchPortalFunction project, and then click OK.

### Add unit tests for the WatchInfo function

You can now add unit tests to the test project. In the luxury watch scenario, you want to ensure the WatchInfo function always returns an OK response when a model is provided in the query string of a request, and a Bad response if the query string is empty or doesn't contain the model parameter.

You'll add a pair of Fact tests to the WatchFunctionsTests to verify this behavior.

1. In the Solution Explorer window, double-click the WatchFunctionUnitTests.cs file to display it in the code window.
2. Add the following using directives to the list at the top of the file.

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Microsoft.Extensions.Logging.Abstractions;
```

3. Change the name of the Test1 method to TestWatchFunctionSuccess.
4. In the body of the TestWatchFunctionSuccess method, add the following code. This statement creates a mock HTTP context and an HTTP request. The request includes a query string that includes the model parameter, which is set to abc.

```csharp
var httpContext = new DefaultHttpContext();
var queryStringValue = "abc";
var request = new DefaultHttpRequest(new DefaultHttpContext())
{
    Query = new QueryCollection
    (
        new System.Collections.Generic.Dictionary<string, StringValues>()
        {
            { "model", queryStringValue }
        }
    )
};
```

5. Add the statement shown below to the method. This statement creates a dummy logger.

```csharp
var logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
```

6. Add the following code to the method. These statements invoke the WatchInfo function, passing in the dummy request and logger as parameters.

```csharp
var response = WatchPortalFunction.WatchInfo.Run(request, logger);
response.Wait();
```

7. Add the code shown below to the method. This code checks that the response from the Azure function is correct. In this case, the function should return an OK response, containing the expected body data.

```csharp
// Check that the response is an "OK" response
Assert.IsAssignableFrom<OkObjectResult>(response.Result);

// Check that the contents of the response are the expected contents
var result = (OkObjectResult)response.Result;
dynamic watchinfo = new { Manufacturer = "Abc", CaseType = "Solid", Bezel = "Titanium", Dial = "Roman", CaseFinish = "Silver", Jewels = 15 };
string watchInfo = $"Watch Details: {watchinfo.Manufacturer}, {watchinfo.CaseType}, {watchinfo.Bezel}, {watchinfo.Dial}, {watchinfo.CaseFinish}, {watchinfo.Jewels}";
Assert.Equal(watchInfo, result.Value);
```

The complete method should look like this:

```csharp
[Fact]
public void TestWatchFunctionSuccess()
{
    var httpContext = new DefaultHttpContext();
    var queryStringValue = "abc";
    var request = new DefaultHttpRequest(new DefaultHttpContext())
    {
        Query = new QueryCollection
        (
            new System.Collections.Generic.Dictionary<string, StringValues>()
            {
                { "model", queryStringValue }
            }
        )
    };

    var logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");

    var response = WatchPortalFunction.WatchInfo.Run(request, logger);
    response.Wait();

    // Check that the response is an "OK" response
    Assert.IsAssignableFrom<OkObjectResult>(response.Result);

    // Check that the contents of the response are the expected contents
    var result = (OkObjectResult)response.Result;
    dynamic watchinfo = new { Manufacturer = "Abc", CaseType = "Solid", Bezel = "Titanium", Dial = "Roman", CaseFinish = "Silver", Jewels = 15 };
    string watchInfo = $"Watch Details: {watchinfo.Manufacturer}, {watchinfo.CaseType}, {watchinfo.Bezel}, {watchinfo.Dial}, {watchinfo.CaseFinish}, {watchinfo.Jewels}";
    Assert.Equal(watchInfo, result.Value);
}
```

8. Add two more methods named TestWatchFunctionFailureNoQueryString and TestWatchFunctionFailureNoModel. The TestWatchFunctionFailureNoQueryString verifies that the WatchInfo function fails gracefully if it isn't given a query string. The TestWatchFunctionFailureNoModel checks for the same failure if the function is passed a query string that doesn't contain a model parameter.

```csharp
[Fact]
public void TestWatchFunctionFailureNoQueryString()
{
    var httpContext = new DefaultHttpContext();
    var request = new DefaultHttpRequest(new DefaultHttpContext());
    var logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");

    var response = WatchPortalFunction.WatchInfo.Run(request, logger);
    response.Wait();

    // Check that the response is an "Bad" response
    Assert.IsAssignableFrom<BadRequestObjectResult>(response.Result);

    // Check that the contents of the response are the expected contents
    var result = (BadRequestObjectResult)response.Result;
    Assert.Equal("Please provide a watch model in the query string", result.Value);
}

[Fact]
public void TestWatchFunctionFailureNoModel()
{
    var httpContext = new DefaultHttpContext();
    var queryStringValue = "abc";
    var request = new DefaultHttpRequest(new DefaultHttpContext())
    {
        Query = new QueryCollection
        (
            new System.Collections.Generic.Dictionary<string, StringValues>()
            {
                { "not-model", queryStringValue }
            }
        )
    };

    var logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");

    var response = WatchPortalFunction.WatchInfo.Run(request, logger);
    response.Wait();

    // Check that the response is an "Bad" response
    Assert.IsAssignableFrom<BadRequestObjectResult>(response.Result);

    // Check that the contents of the response are the expected contents
    var result = (BadRequestObjectResult)response.Result;
    Assert.Equal("Please provide a watch model in the query string", result.Value);
}
```

### Run the tests

1. On the Test menu, click Run, and then click All Tests.
2. In the Test Explorer window, all three tests should complete successfully.
3. In the Solution Explorer window, under the WatchPortalFunction project, double-click WatchInfo.cs to display the file in the code editor.
4. Find the following code:

```csharp

```

5. Change the statement that sets the model variable as follows. This change simulates the developer making a mistake in the code:

```csharp

```

6. On the Test menu, click Run, and then click All Tests to repeat the tests. This time, the TestWatchFunctionSuccess test should fail. This failure occurs because the WatchInfo function hasn't found the parameter named modelll in the query string, and so the function has returned a Bad response.

You've now seen how to create a unit test project, and implement unit tests for an Azure Function.

## Summary

In this module, you learned about Azure Functions. You've seen how to use Visual Studio to create an Azure Function and deploy it to the cloud. You've also seen how to create and run unit tests for an Azure Function.

Learn more

- [An introduction to Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview)
- [Develop Azure Functions using Visual Studio](https://docs.microsoft.com/en-us/azure/azure-functions/functions-develop-vs)
- [Create your first function using Visual Studio](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-your-first-function-visual-studio)
- [Strategies for testing your code in Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-test-a-function)
