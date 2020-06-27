[Create serverless logic with Azure Functions](https://docs.microsoft.com/en-us/learn/modules/create-serverless-logic-with-azure-functions/) (~36 mins)

Azure Functions allow developers to host business logic that can be executed without managing or provisioning infrastructure.

In this module, you will:

- Decide if serverless computing is right for your business need
- Create an Azure Function app in the Azure portal
- Execute a function using triggers
- Monitor and test your Azure Function from the Azure portal

# Notes and key takeaways

While scaling, only one function app instance can be created every 10 seconds, for up to 200 total instances. Keep in mind, each instance can service multiple concurrent executions, so there is no set limit on how much traffic a single instance can handle.

Choosing a service plan:

- Function apps may use one of two types of service plans. The first service plan is the Consumption service plan. This is the plan that you choose when using the Azure serverless application platform. The Consumption service plan provides automatic scaling and bills you when your functions are running. The Consumption plan comes with a configurable timeout period for the execution of a function. By default, it is 5 minutes, but may be configured to have a timeout as long as 10 minutes.
- The second plan is called the Azure App Service plan. This plan allows you to avoid timeout periods by having your function run continuously on a VM that you define. When using an App Service plan, you are responsible for managing the app resources the function runs on, so this is technically not a serverless plan. However, it may be a better choice if your functions are used continuously or if your functions require more processing power or execution time than the Consumption plan can provide.

When you create a function app, it must be linked to a storage account. You can select an existing account or create a new one. The function app uses this storage account for internal operations such as logging function executions and managing execution triggers. On the Consumption service plan, this is also where the function code and configuration file are stored.

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec840a711352b2a7b2e5100/65837a28d49a3bb8d33b5093fe4f73cb/Screen_Shot_2020-05-22_at_14.30.12_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec840a711352b2a7b2e5100/65837a28d49a3bb8d33b5093fe4f73cb/Screen_Shot_2020-05-22_at_14.30.12_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec840a711352b2a7b2e5100/4353fd2cd5272e37fcfa0316d1176a8d/Screen_Shot_2020-05-22_at_14.35.55_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec840a711352b2a7b2e5100/4353fd2cd5272e37fcfa0316d1176a8d/Screen_Shot_2020-05-22_at_14.35.55_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec840a711352b2a7b2e5100/b75b10458ffed4a7d5aad089f102494c/Screen_Shot_2020-05-22_at_14.40.36_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec840a711352b2a7b2e5100/b75b10458ffed4a7d5aad089f102494c/Screen_Shot_2020-05-22_at_14.40.36_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec840a711352b2a7b2e5100/86a4106e1cba4ad3d69a95162251beee/Screen_Shot_2020-05-22_at_14.44.00_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec840a711352b2a7b2e5100/86a4106e1cba4ad3d69a95162251beee/Screen_Shot_2020-05-22_at_14.44.00_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec840a711352b2a7b2e5100/82a7db0b3c648941a0f1ff7e04564525/Screen_Shot_2020-05-22_at_14.58.10_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec840a711352b2a7b2e5100/82a7db0b3c648941a0f1ff7e04564525/Screen_Shot_2020-05-22_at_14.58.10_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec840a711352b2a7b2e5100/abad89d235fdda61c7ab08b8f4733ae0/Screen_Shot_2020-05-22_at_14.59.45_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ec840a711352b2a7b2e5100/abad89d235fdda61c7ab08b8f4733ae0/Screen_Shot_2020-05-22_at_14.59.45_PM.png)
