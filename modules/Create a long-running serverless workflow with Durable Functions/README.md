[Create a long-running serverless workflow with Durable Functions](https://docs.microsoft.com/en-us/learn/modules/create-long-running-serverless-workflow-with-durable-functions/) (~46 mins)

Learn how to orchestrate a long-running workflow as a set of activities using Durable Functions.

In this module, you will:

- Explore Durable Functions
- Design a long-running approval process workflow
- Implement a long-running approval process workflow using Durable Functions

# Notes and key takeaways

## What are Durable Functions?

Durable Functions is an extension of Azure Functions that enables you to perform long-lasting, stateful operations in Azure. Azure provides the infrastructure for maintaining state information. You can use Durable Functions to orchestrate a long-running workflow. Using this approach, you get all the benefits of a serverless hosting model, while letting the Durable Functions framework take care of activity monitoring, synchronization, and runtime concerns.

By the end of this module, you'll learn how to orchestrate a long-running workflow as a set of activities using Durable Functions.

In the example scenario, your company currently follows a manual approval process for project design proposals. The process has multiple steps, and each step in the process can vary in duration. Implementing an automated process in-house is complex and costly. Coordinating each step takes effort. In addition, you must be able to incorporate custom logic into the workflow.

In this unit, you'll learn about the benefits of Durable Functions. You'll learn about the different function types and key concepts associated with Durable Functions.

Azure Functions operate in a stateless environment, Durable Functions can retain state between function calls. This approach enables you to simplify complex stateful executions in a serverless-environment.

Durable Functions scales as needed, and provides a cost effective means of implementing complex workflows in the cloud. Some benefits of using Durable Functions include:

- They enable you to write event driven code. A durable function can wait asynchronously for one or more external events, and then perform a series of tasks in response to these events.
- You can chain functions together. You can implement common patterns such as fan-out/fan-in, which uses one function to invoke others in parallel, and then accumulate the results.
- You can orchestrate and coordinate functions, and specify the order in which functions should execute.
- The state is managed for you. You don't have to write your own code to save state information for a long-running function.

Durable functions allows you to define stateful workflows using an `Orchestration` function. An orchestration function provides these extra benefits:

- You can define the workflows in code. You don't need to write a JSON description or use a workflow design tool.
- Functions can be called both synchronously and asynchronously. Output from the called functions is saved locally in variables and used in subsequent function calls.
- Azure checkpoints the progress of a function automatically when the function awaits. Azure may choose to dehydrate the function and save its state while the function waits, to preserve resources and reduce costs. When the function starts running again, Azure will rehydrate it and restore its state.

You can use three durable function types: `Client`, `Orchestrator`, and `Activity`:

- Client functions are the entry point for creating an instance of a Durable Functions orchestration. They can run in response to an event from many sources, such as a new HTTP request arriving, a message being posted to a message queue, an event arriving in an event stream. You can write them in any of the supported languages.
- Orchestrator functions describe how actions are executed, and the order in which they are run. You write the orchestration logic in code (C# or JavaScript).
- Activity functions are the basic units of work in a durable function orchestration. An activity function contains the actual work performed by the tasks being orchestrated.

You can use Durable Functions to implement many common workflow patterns. These patterns include:

- Function chaining - In this pattern, the workflow executes a sequence of functions in a specified order. The output of one function is applied to the input of the next function in the sequence. The output of the final function is used to generate a result.
  ![https://docs.microsoft.com/en-us/learn/modules/create-long-running-serverless-workflow-with-durable-functions/media/function-chaining.png](https://docs.microsoft.com/en-us/learn/modules/create-long-running-serverless-workflow-with-durable-functions/media/function-chaining.png)
- Fan out/fan in - This pattern runs multiple functions in parallel and then waits for all the functions to finish. The results of the parallel executions can be aggregated or used to compute a final result.
  ![https://docs.microsoft.com/en-us/learn/modules/create-long-running-serverless-workflow-with-durable-functions/media/fan-out-fan-in.png](https://docs.microsoft.com/en-us/learn/modules/create-long-running-serverless-workflow-with-durable-functions/media/fan-out-fan-in.png)
- Async HTTP APIs - This pattern addresses the problem of coordinating state of long-running operations with external clients. An HTTP call can trigger the long-running action. Then, it can redirect the client to a status endpoint. The client can learn when the operation is finished by polling this endpoint.
  ![https://docs.microsoft.com/en-us/learn/modules/create-long-running-serverless-workflow-with-durable-functions/media/async-http-api.png](https://docs.microsoft.com/en-us/learn/modules/create-long-running-serverless-workflow-with-durable-functions/media/async-http-api.png)
- Monitor - This pattern implements a recurring process in a workflow, possibly looking for a change in state. For example, you could use this pattern to poll until specific conditions are met.
  ![https://docs.microsoft.com/en-us/learn/modules/create-long-running-serverless-workflow-with-durable-functions/media/monitor.png](https://docs.microsoft.com/en-us/learn/modules/create-long-running-serverless-workflow-with-durable-functions/media/monitor.png)
- Human interaction - This pattern combines automated processes that also involve some human interaction. A manual process within an automated process is tricky because people aren't as highly available and as responsive as most computers. Human interaction can be incorporated using timeouts and compensation logic that runs if the human fails to interact correctly within a specified response time. An approval process is an example of a process that involves human interaction.
  ![https://docs.microsoft.com/en-us/learn/modules/create-long-running-serverless-workflow-with-durable-functions/media/approval.png](https://docs.microsoft.com/en-us/learn/modules/create-long-running-serverless-workflow-with-durable-functions/media/approval.png)

Comparison with Logic Apps

Durable Functions and Logic Apps are both Azure services that enable serverless workload. Azure Durable Functions is intended as a powerful serverless compute option to run custom logic. Azure Logic Apps is better suited for integrating Azure services and components. You can use either technology to create complex orchestrations. With Azure Durable Functions, you develop orchestrations by writing code and using the Durable Functions extension. With Logic Apps, you create orchestrations by using the design surface or editing configuration files.

The following table lists some of the key differences between Azure Durable Functions and Azure Logic Apps:

|                   | Azure Durable Functions                                                        | Azure Logic Apps                                                                                          |     |     |
| ----------------- | ------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | --- | --- |
| Development       | Code-first (imperative)                                                        | Design-first (declarative)                                                                                |     |     |
| Connectivity      | About a dozen built-in binding types. You can write code for custom bindings.  | Large collection of connectors. Enterprise Integration Pack for B2B.You can also build custom connectors. |     |     |
| Actions           | Each activity is an Azure Function. You write the code for activity functions. | Large collection of ready-made actions. You integrate custom logic through custom connectors.             |     |     |
| Monitoring        | Azure Application Insights                                                     | Azure portal, Azure Monitor logs                                                                          |     |     |
| Management        | REST API, Visual Studio                                                        | Azure portal, REST API, PowerShell, Visual Studio                                                         |     |     |
| Execution context | Can run locally or in the cloud                                                | Runs only in the cloud                                                                                    |     |     |

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed19aef9465f34448ec379a/8618b56eaa5f42231ef9ae08bc741b9d/Screen_Shot_2020-07-01_at_11.02.36_AM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed19aef9465f34448ec379a/8618b56eaa5f42231ef9ae08bc741b9d/Screen_Shot_2020-07-01_at_11.02.36_AM.png)

## Design a workflow based on Durable Functions

You can use Durable Functions to orchestrate a long-running workflow as a set of activities. You can map each step in the process to a function type, and each task to an activity. Having an automated process means you don't have to worry about manually monitoring or escalating a task if it isn't done.

As we continue to think about automating our proposal approval process, we need to consider the cases when a step in the process does not complete in time and needs to be escalated. For example: what if we need a manager's approval for a particular size of proposal, but the manager is late with a response?

Escalation steps are useful to the business, as they move along a task when a deadline has been reached. They ensure tasks are completed, and not forgotten. An escalation step could involve sending out reminders or even reassigning a task to someone higher up the managerial hierarchy.

In this unit, you'll design a workflow based on Durable Functions. In the next exercise, you'll apply this knowledge to create an app with Azure Durable Functions.

### Description of the design approval process

Our workflow begins when a project design is submitted for approval. The proposal is assigned as an approval task to a manager. The manager will either approve or reject the proposal. In the real world, this event would probably generate and send a notification to the proposal author, to let them know the outcome of the approve/reject request. In this example, you'll just change the status of the task to either `approved` or `rejected`.

The workflow steps are as follows:

- A project design is submitted.
- An approval task is allocated to a manager, so they can review the project design proposal.
- The project design proposal is rejected or approved.
- An escalation task is allocated if the approval task isn't completed within a pre-defined time limit.

![https://docs.microsoft.com/en-us/learn/modules/create-long-running-serverless-workflow-with-durable-functions/media/approval.png](https://docs.microsoft.com/en-us/learn/modules/create-long-running-serverless-workflow-with-durable-functions/media/approval.png)

The following table shows how the workflow steps can be mapped to the function types we use in a Durable Functions workflow.

| Workflow function                                   | Durable Function Type  |
| --------------------------------------------------- | ---------------------- |
| Submitting a project design proposal for approval   | Client Function        |
| Assign an Approval task to relevant member of staff | Orchestration Function |
| Approval task                                       | Activity Function      |
| Escalation task                                     | Activity Function      |

The `Orchestration` function will manage a rule in the workflow that starts the escalation activity if the approval activity doesn't return within a specified time.
Now that we understand what's needed for our workflow, let's write it in code in the next unit!

## Exercise - Create a workflow using Durable Functions

[https://docs.microsoft.com/en-us/learn/modules/create-long-running-serverless-workflow-with-durable-functions/4-exercise-create-a-workflow-using-durable-functions](https://docs.microsoft.com/en-us/learn/modules/create-long-running-serverless-workflow-with-durable-functions/4-exercise-create-a-workflow-using-durable-functions)

### Create a Function App

![assets/durable%20functions/cfa01.png](assets/durable%20functions/cfa01.png)
![assets/durable%20functions/cfa02.png](assets/durable%20functions/cfa02.png)
![assets/durable%20functions/cfa03.png](assets/durable%20functions/cfa03.png)
![assets/durable%20functions/cfa04.png](assets/durable%20functions/cfa04.png)

### Install the durable-functions npm package

Key piece - navigate to the App Service Editor for your function app and install durable functions - `npm install durable-functions` - an install that took over two and a half minutes in the sandbox environment.

![assets/durable%20functions/dfi01.png](assets/durable%20functions/dfi01.png)
![assets/durable%20functions/dfi02.png](assets/durable%20functions/dfi02.png)
![assets/durable%20functions/dfi03.png](assets/durable%20functions/dfi03.png)
![assets/durable%20functions/dfi04.png](assets/durable%20functions/dfi04.png)
![assets/durable%20functions/dfi05.png](assets/durable%20functions/dfi05.png)

### Create the client function for submitting a design proposal

![assets/durable%20functions/client-func-01.png](assets/durable%20functions/client-func-01.png)
![assets/durable%20functions/client-func-02.png](assets/durable%20functions/client-func-02.png)
![assets/durable%20functions/client-func-03.png](assets/durable%20functions/client-func-03.png)
![assets/durable%20functions/client-func-04.png](assets/durable%20functions/client-func-04.png)
![assets/durable%20functions/client-func-05.png](assets/durable%20functions/client-func-05.png)

### Create the orchestrator function

![assets/durable%20functions/orch-func-01.png](assets/durable%20functions/orch-func-01.png)
![assets/durable%20functions/orch-func-02.png](assets/durable%20functions/orch-func-02.png)

```js
/* OrchFunction - index.js
 * This function is not intended to be invoked directly. Instead it will be
 * triggered by an HTTP starter function.
 *
 * Before running this sample, please:
 * - create a Durable activity function (default name is "Hello")
 * - create a Durable HTTP starter function
 * - run 'npm install durable-functions' from the wwwroot folder of your
 *    function app in Kudu
 */

const df = require("durable-functions")

module.exports = df.orchestrator(function* (context) {
  const outputs = []

  /*
   * We will call the approval activity with a reject and an approved to simulate both
   */

  outputs.push(yield context.df.callActivity("Approval", "Approved"))
  outputs.push(yield context.df.callActivity("Approval", "Rejected"))

  /*
    This code calls an Activity function named Approval, which you'll create shortly. The code in the orchestrator function invokes the Approval function twice. The first time simulates accepting the proposal, and the second time tests the proposal rejection logic.

    The value returned by each call is combined together, and passed back to the client function. In a production environment, your orchestration function would call a series of activity functions that make the accept/reject decision, and return the result of these activities.
    */

  return outputs
})
```

### Create the activity function

![assets/durable%20functions/act-func-01.png](assets/durable%20functions/act-func-01.png)
![assets/durable%20functions/act-func-02.png](assets/durable%20functions/act-func-02.png)

### Enable Azure Functions version 2 compatibility mode

![assets/durable%20functions/v2-compat-mode-01.png](assets/durable%20functions/act-func-01.png)

### Verify that the durable functions workflow starts

![assets/durable%20functions/verify-httpstart-workflow-01.png](assets/durable%20functions/verify-httpstart-workflow-01.png)

A sample URL for the `HttpStart` function will look something like `rbdurablefunctions.azurewebsites.net/api/orchestrators/{functionName}?code=216M1fyOGkZj7xyfk2KzkmiBjoZKFqJRavRkt2vdYw1ORw0YHOVd/A==`

Replace the `{functionName}` placeholder with `OrchFunction`:

`rbdurablefunctions.azurewebsites.net/api/orchestrators/OrchFunction?code=216M1fyOGkZj7xyfk2KzkmiBjoZKFqJRavRkt2vdYw1ORw0YHOVd/A==`

Browsing to this URL will give you a set of URI endpoints that you can use to monitor and manage the execution:

```json
{
  "id": "6fde8d235ab947bb817c035c1fc3b0da",
  "statusQueryGetUri": "http://rbdurablefunctions.azurewebsites.net/runtime/webhooks/durabletask/instances/6fde8d235ab947bb817c035c1fc3b0da?taskHub=DurableFunctionsHub&connection=Storage&code=w/NFoZoRsvGwCSo6L/2G2lHa6PX7KYyqDgsa6sE1gpR2TnBlDD2zOg==",
  "sendEventPostUri": "http://rbdurablefunctions.azurewebsites.net/runtime/webhooks/durabletask/instances/6fde8d235ab947bb817c035c1fc3b0da/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=w/NFoZoRsvGwCSo6L/2G2lHa6PX7KYyqDgsa6sE1gpR2TnBlDD2zOg==",
  "terminatePostUri": "http://rbdurablefunctions.azurewebsites.net/runtime/webhooks/durabletask/instances/6fde8d235ab947bb817c035c1fc3b0da/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=w/NFoZoRsvGwCSo6L/2G2lHa6PX7KYyqDgsa6sE1gpR2TnBlDD2zOg==",
  "rewindPostUri": "http://rbdurablefunctions.azurewebsites.net/runtime/webhooks/durabletask/instances/6fde8d235ab947bb817c035c1fc3b0da/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=w/NFoZoRsvGwCSo6L/2G2lHa6PX7KYyqDgsa6sE1gpR2TnBlDD2zOg==",
  "purgeHistoryDeleteUri": "http://rbdurablefunctions.azurewebsites.net/runtime/webhooks/durabletask/instances/6fde8d235ab947bb817c035c1fc3b0da?taskHub=DurableFunctionsHub&connection=Storage&code=w/NFoZoRsvGwCSo6L/2G2lHa6PX7KYyqDgsa6sE1gpR2TnBlDD2zOg=="
}
```

Copy the `statusQueryGetUri` value, and use the web browser to navigate to this URL. You should see a response message that resembles the following example:

```json
{
  "name": "OrchFunction",
  "instanceId": "6fde8d235ab947bb817c035c1fc3b0da",
  "runtimeStatus": "Completed",
  "input": null,
  "customStatus": null,
  "output": [
    "Your project design proposal has been -  Approved!",
    "Your project design proposal has been -  Rejected!"
  ],
  "createdTime": "2020-07-01T18:55:59Z",
  "lastUpdatedTime": "2020-07-01T18:56:01Z"
}
```

Recall that the orchestration function runs the activity function twice. The first time, the activity function indicates that the project proposal has been accepted. The second time, the proposal is rejected. The messages from both function calls are combined by the orchestration function and returned to the client function.

## Control long-running tasks using timers

When working with a long-running workflow, we need to consider some scenarios. For example, what should happen if a task isn't completed within an acceptable period of time? How can you check the status of a task? You can address these concerns with timeouts and escalation paths.

In this unit you'll learn how to control long running tasks using durable timers, and how to add an escalation path based on the timer.

### Timers in Durable Functions

Durable Functions provides timers for use in the orchestrator functions. They can implement delays or set up timeouts for asynchronous actions. Use durable timers in orchestrator functions instead of the `setTimeout()` and `setInterval()` functions.

You create a durable timer by calling the `createTimer` method of the `DurableOrchestrationContext`. This method returns a task that resumes on a specified date and time.

### Using timers for delay

The following example illustrates how to use durable timers for delay. The example sends a reminder every day for 10 days:

```js
const df = require("durable-functions")
const moment = require("moment")

module.exports = df.orchestrator(function* (context) {
  for (let i = 0; i < 10; i++) {
    const dayOfMonth = context.df.currentUtcDateTime.getDate()
    const deadline = moment.utc(context.df.currentUtcDateTime).add(1, "d")
    yield context.df.createTimer(deadline.toDate())
    yield context.df.callActivity("SendReminder")
  }
})
```

Always use currentUtcDateTime to obtain the current date and time, instead of Date.now or Date.UTC.

### Using timers for timeout

The following example illustrates how to use durable timers for timeout. Executing a different path if a timeout occurs. In this example, the function waits until either the `GetQuote` activity function completes or the `deadline*` timer expires.

If the activity function completes the code follows the success case, otherwise it follows the timeout case:

```js
const df = require("durable-functions")
const moment = require("moment")

module.exports = df.orchestrator(function* (context) {
  const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s")

  const activityTask = context.df.callActivity("GetQuote")
  const timeoutTask = context.df.createTimer(deadline.toDate())

  const winner = yield context.df.Task.any([activityTask, timeoutTask])
  if (winner === activityTask) {
    // success case
    timeoutTask.cancel()
    return true
  } else {
    // timeout case
    return false
  }
})
```

## Exercise - Add a durable timer to manage a long-running task

[Exercise - Add a durable timer to manage a long-running task](https://docs.microsoft.com/en-us/learn/modules/create-long-running-serverless-workflow-with-durable-functions/6-exercise-add-a-durable-timer-to-manage-a-long-running-task)

Using the sandbox you've already created, navigate to the debug console so you can install the [Moment.js](https://momentjs.com) library in `site/wwwroot` by running: `npm install moment`

### Add an escalation activity to your function app

- Expand your function app and select the + button next to Functions.
- Select the `Durable Functions activity` template.
- Name the function `Escalation`, and then select `Create`.
- When the function has been created, replace the code in `index.js` for this function with the following example:

```js
module.exports = async function (context) {
  return `ESCALATION : You have not approved the project design proposal - reassigning to your Manager! ${context.bindings.name}!`
}
```

This code returns a message indicating that the workflow has been escalated. In a production system, this function would contain the logic to remind the recipient, or reassign the task.

- Select Save to save your new function.

### Update the orchestration function to use the escalation function

1. In your function app, select the orchestration function that you created in the previous exercise, which you named `OrchFunction`.

2. In the `index.js` code file, add a reference to the `moment` library:

```js
const moment = require("moment")
```

3. Replace the body of the function with the following code, which will test whether the deadline for approval has passed:

```js
module.exports = df.orchestrator(function* (context) {
  const outputs = []
  const deadline = moment.utc(context.df.currentUtcDateTime).add(20, "s")
  const activityTask = context.df.waitForExternalEvent("Approval")
  const timeoutTask = context.df.createTimer(deadline.toDate())

  const winner = yield context.df.Task.any([activityTask, timeoutTask])
  if (winner === activityTask) {
    outputs.push(yield context.df.callActivity("Approval", "Approved"))
  } else {
    outputs.push(
      yield context.df.callActivity("Escalation", "Head of department")
    )
  }

  if (!timeoutTask.isCompleted) {
    // All pending timers must be complete or canceled before the function exits.
    timeoutTask.cancel()
  }

  return outputs
})
```

To keep things brief for the purposes of this exercise, if the `Approval` function doesn't respond within 20 seconds, the `Escalation` function is called. The code also changes the call to `Approval` to wait for an external input. This way we can control when the response comes back for testing purposes.

4. Select `Save`

### Verify that the Durable Functions workflow starts

1. Select the `HttpStart` function you created in the preceding exercise.
2. Select </> Get function URL, and copy the URL.
3. Open another browser window and navigate to the URL that you copied, replacing `{functionName}` with `OrchFunction`.

The response message contains a set of URI endpoints that you can use to monitor and manage the execution, which should resemble the following example:

```json
{
  "id": "f0e1d2c3b4a5968778695a4b3c2d1e0f",
  "statusQueryGetUri": "https://example.azurewebsites.net/...",
  "sendEventPostUri": "https://example.azurewebsites.net/...",
  "terminatePostUri": "https://example.azurewebsites.net/...",
  "rewindPostUri": "https://example.azurewebsites.net/...",
  "purgeHistoryDeleteUri": "https://example.azurewebsites.net/..."
}
```

Copy the statusQueryGetUri value, and use the web browser to navigate to this URL. You should see a response message that shows the instance is pending as it is waiting for the timer to countdown to 20 seconds, which should resemble the following example:

```json
{
  "name": "OrchFunction",
  "instanceId": "f0e1d2c3b4a5968778695a4b3c2d1e0f",
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": null,
  "output": null,
  "createdTime": "2019-04-14T13:17:26Z",
  "lastUpdatedTime": "2019-04-14T13:17:27Z"
}
```

If you wait for 20 seconds and refresh the browser window, the timeout should have been reached and the workflow will call the `Escalate` activity. You'll see a response that should resemble the following example:

```json
{
  "name": "OrchFunction",
  "instanceId": "f0e1d2c3b4a5968778695a4b3c2d1e0f",
  "runtimeStatus": "Completed",
  "input": null,
  "customStatus": null,
  "output": [
    "ESCALATION : You have not approved the project design proposal - reassigning to your Manager! Head of department!"
  ],
  "createdTime": "2019-04-14T13:43:09Z",
  "lastUpdatedTime": "2019-04-14T13:43:31Z"
}
```

## Summary

Durable Functions enables you to implement long-running workflows without requiring that you maintain state information manually. Azure provides the infrastructure in which Durable Functions run. You focus on the logic for the functions that perform the tasks in your workflow.

In this module we learned about Durable Functions and how to orchestrate our simple design proposal approvals process. Using durable timers, we can add an escalation path to our workflows, especially for those long-running, indeterminate tasks.

### Learn more

[Durable Functions patterns and technical concepts](https://docs.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-overview?tabs=javascript)
