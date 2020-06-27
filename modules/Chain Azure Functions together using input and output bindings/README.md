[Chain Azure Functions together using input and output bindings](https://docs.microsoft.com/en-us/learn/modules/chain-azure-functions-data-using-bindings/) (~1 hr 10 mins)

Azure Functions makes it easy for your function code to integrate with data and services. Through the power of bindings, you declare the data sources to read and write, and let Azure Functions take care of the rest.

In this module, you will:

- Explore what types of data sources can be accessed through bindings
- Read data from Azure Cosmos DB using Azure Functions
- Store data in Azure Cosmos DB using Azure Functions
- Send messages to Azure Queue Storage using Azure Functions

# Notes and key takeaways

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/c81823906e5a4c99c16947de960555c9/Screen_Shot_2020-05-29_at_14.50.15_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/c81823906e5a4c99c16947de960555c9/Screen_Shot_2020-05-29_at_14.50.15_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/6c351c73b1ec4eec25b8b71cf6e15d61/Screen_Shot_2020-05-29_at_14.50.59_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/6c351c73b1ec4eec25b8b71cf6e15d61/Screen_Shot_2020-05-29_at_14.50.59_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/97acb7122b6d4d6043a5c91ae5621cf0/Screen_Shot_2020-05-29_at_14.51.54_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/97acb7122b6d4d6043a5c91ae5621cf0/Screen_Shot_2020-05-29_at_14.51.54_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/4356523a95b0e36042051ec37dd76226/Screen_Shot_2020-05-29_at_15.01.46_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/4356523a95b0e36042051ec37dd76226/Screen_Shot_2020-05-29_at_15.01.46_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/495343cbd9fc320019cc37a592d22a62/Screen_Shot_2020-05-29_at_15.02.05_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/495343cbd9fc320019cc37a592d22a62/Screen_Shot_2020-05-29_at_15.02.05_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/c763fd210dc68ea25bfb3a96e842361f/Screen_Shot_2020-05-29_at_15.03.22_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/c763fd210dc68ea25bfb3a96e842361f/Screen_Shot_2020-05-29_at_15.03.22_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/ed8923235122f202bb5dd07bfdc12dca/Screen_Shot_2020-05-29_at_15.05.18_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/ed8923235122f202bb5dd07bfdc12dca/Screen_Shot_2020-05-29_at_15.05.18_PM.png)

Wow. Estimated ~10 mins to provision my Cosmos DB...wound up taking ~16 minutes.

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/5c220f85cb5ed451bcabc79fef415ad3/Screen_Shot_2020-05-29_at_15.23.29_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/5c220f85cb5ed451bcabc79fef415ad3/Screen_Shot_2020-05-29_at_15.23.29_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/3781bed04c501b017d359d69e32b6e17/Screen_Shot_2020-05-29_at_15.24.45_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/3781bed04c501b017d359d69e32b6e17/Screen_Shot_2020-05-29_at_15.24.45_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/4bc5ca7a9da84562a7c4b35f4126949c/Screen_Shot_2020-05-29_at_15.25.21_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/4bc5ca7a9da84562a7c4b35f4126949c/Screen_Shot_2020-05-29_at_15.25.21_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/2726f95b09f85f140b55491c0f97c720/Screen_Shot_2020-05-29_at_15.28.16_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/2726f95b09f85f140b55491c0f97c720/Screen_Shot_2020-05-29_at_15.28.16_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/8249af7dcb4c4d8593fcc83ba7285bab/Screen_Shot_2020-05-29_at_15.29.18_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/8249af7dcb4c4d8593fcc83ba7285bab/Screen_Shot_2020-05-29_at_15.29.18_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/a3c3b9d3b96d66476f1d32ec29990dbd/Screen_Shot_2020-05-29_at_15.30.27_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/a3c3b9d3b96d66476f1d32ec29990dbd/Screen_Shot_2020-05-29_at_15.30.27_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/7a40f8b65a5aef3e578138777ad9cb9a/Screen_Shot_2020-05-29_at_15.31.46_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/7a40f8b65a5aef3e578138777ad9cb9a/Screen_Shot_2020-05-29_at_15.31.46_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/d3d4312bf8a6657502db361f12b50280/Screen_Shot_2020-05-29_at_15.36.32_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/d3d4312bf8a6657502db361f12b50280/Screen_Shot_2020-05-29_at_15.36.32_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/a723338a3c049165bb797f27e4e84d54/Screen_Shot_2020-05-29_at_15.37.41_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/a723338a3c049165bb797f27e4e84d54/Screen_Shot_2020-05-29_at_15.37.41_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/4d0bdc09e89c42d86179218489bedba2/Screen_Shot_2020-05-29_at_15.38.30_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/4d0bdc09e89c42d86179218489bedba2/Screen_Shot_2020-05-29_at_15.38.30_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/d2c9bac775303ae865a60f1f3eeff4fb/Screen_Shot_2020-05-29_at_15.39.05_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/d2c9bac775303ae865a60f1f3eeff4fb/Screen_Shot_2020-05-29_at_15.39.05_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/23c587354b4d5f0c6ba246799ec4bca8/Screen_Shot_2020-05-29_at_15.41.34_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/23c587354b4d5f0c6ba246799ec4bca8/Screen_Shot_2020-05-29_at_15.41.34_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/cf109dd3de3eb8fbe9296bc07b007c2e/Screen_Shot_2020-05-29_at_15.41.37_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/cf109dd3de3eb8fbe9296bc07b007c2e/Screen_Shot_2020-05-29_at_15.41.37_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/1aaee07f18d4b20eafd525821e27e5aa/Screen_Shot_2020-05-29_at_15.41.55_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/1aaee07f18d4b20eafd525821e27e5aa/Screen_Shot_2020-05-29_at_15.41.55_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/d3c91b22abc764ffb0cd47141db75926/Screen_Shot_2020-05-29_at_15.43.52_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/d3c91b22abc764ffb0cd47141db75926/Screen_Shot_2020-05-29_at_15.43.52_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/2452f5beb3c3ab5788bfa2276ad71b8e/Screen_Shot_2020-05-29_at_15.43.53_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/2452f5beb3c3ab5788bfa2276ad71b8e/Screen_Shot_2020-05-29_at_15.43.53_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/152d516def80cbebbb10da31c7768122/Screen_Shot_2020-05-29_at_15.44.51_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/152d516def80cbebbb10da31c7768122/Screen_Shot_2020-05-29_at_15.44.51_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/f9e068d48feaa7edb016c26f7a3ebbc3/Screen_Shot_2020-05-29_at_15.44.55_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/f9e068d48feaa7edb016c26f7a3ebbc3/Screen_Shot_2020-05-29_at_15.44.55_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/feb328fa5fa012c00af307ef942d48c3/Screen_Shot_2020-05-29_at_15.45.33_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/feb328fa5fa012c00af307ef942d48c3/Screen_Shot_2020-05-29_at_15.45.33_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/4aeb43b0fc37408e994c4340e5fc8fa0/Screen_Shot_2020-05-29_at_15.45.40_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/4aeb43b0fc37408e994c4340e5fc8fa0/Screen_Shot_2020-05-29_at_15.45.40_PM.png)

![https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/0a9153f64061cf110e125e3a25893908/Screen_Shot_2020-05-29_at_16.28.11_PM.png](https://trello-attachments.s3.amazonaws.com/5c4f25b1c779d01647ad94d6/5ed182f29aa8d62f74e6118e/0a9153f64061cf110e125e3a25893908/Screen_Shot_2020-05-29_at_16.28.11_PM.png)

The experience with the sandbox on macOS is fucking garbage.
