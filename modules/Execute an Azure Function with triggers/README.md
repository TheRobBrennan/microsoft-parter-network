## Execute an Azure Function with triggers

[Execute an Azure Function with triggers](https://docs.microsoft.com/en-us/learn/modules/execute-azure-function-with-triggers/) (~1 hr 23 mins)

A trigger is responsible for executing an Azure function and there are dozens of triggers to choose from. This module will you show you some of the most common types of triggers and how to configure them to execute your logic.

In this module, you will:

- Determine which trigger works best for your business needs
- Create a timer trigger to invoke a function on a consistent schedule
- Create an HTTP trigger to invoke a function when an HTTP request is received
- Create a blob trigger to invoke a function when a blob is created or updated in Azure Storage

# Notes and key takeaways

What is a CRON expression?

A CRON expression is a string that consists of six fields that represent a set of times.

The order of the six fields in Azure is: {second} {minute} {hour} {day} {month} {day of the week}.

For example, to schedule a job to run every five minutes:

`0 */5 * * * *`

The value "/5" contains two special characters. First, the asterisk () means "select every value within the field." Because this field represents minutes, the possible values are 0-59. The second special character is the slash (/), which represents an increment. When you combine these characters together, it means for all values 0-59, select every fifth value. An easier way to say that is simply "every five minutes."

The remaining four fields represent the hour, day, month, and weekday of the week. An asterisk for these fields means to select every possible value. In this example, we select "every hour of every day of every month."

When you put all the fields together, the expression is read as "on the first second, of every fifth minute of every hour, of every day, of every month".

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec852bddd83c80c3c63edc6/x/a6dd3925059291053a4fc1056af7ee1a/Screen_Shot_2020-05-22_at_15.08.24_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec852bddd83c80c3c63edc6/x/a6dd3925059291053a4fc1056af7ee1a/Screen_Shot_2020-05-22_at_15.08.24_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec852bddd83c80c3c63edc6/x/f426f60dc3d258c75113c0845e417f15/Screen_Shot_2020-05-22_at_15.11.45_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec852bddd83c80c3c63edc6/x/f426f60dc3d258c75113c0845e417f15/Screen_Shot_2020-05-22_at_15.11.45_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec852bddd83c80c3c63edc6/x/00e3871c7a7c51e8b0428dc47b668b5c/Screen_Shot_2020-05-22_at_15.29.48_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec852bddd83c80c3c63edc6/x/00e3871c7a7c51e8b0428dc47b668b5c/Screen_Shot_2020-05-22_at_15.29.48_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec84be29c17951b047a55b2/82a93da950a35e8c33b36120b8e7e696/Screen_Shot_2020-05-29_at_13.33.42_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec84be29c17951b047a55b2/82a93da950a35e8c33b36120b8e7e696/Screen_Shot_2020-05-29_at_13.33.42_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec84be29c17951b047a55b2/0c5c497dff997b510ae0935db03a7725/Screen_Shot_2020-05-29_at_13.34.46_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec84be29c17951b047a55b2/0c5c497dff997b510ae0935db03a7725/Screen_Shot_2020-05-29_at_13.34.46_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec84be29c17951b047a55b2/f642cdf28e59e0a9e002b546b9bc7aa7/Screen_Shot_2020-05-29_at_13.35.28_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec84be29c17951b047a55b2/f642cdf28e59e0a9e002b546b9bc7aa7/Screen_Shot_2020-05-29_at_13.35.28_PM.png)

When creating a blob trigger, the workflow is:

- Create a storage account
- Create a new blob container (e.g. samples-workitems)

Fuck it. The sandbox is not triggering this as expected.

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec84be29c17951b047a55b2/f227ff53e1bf3d811cb1acae60660f6e/Screen_Shot_2020-05-29_at_14.45.09_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec84be29c17951b047a55b2/f227ff53e1bf3d811cb1acae60660f6e/Screen_Shot_2020-05-29_at_14.45.09_PM.png)
