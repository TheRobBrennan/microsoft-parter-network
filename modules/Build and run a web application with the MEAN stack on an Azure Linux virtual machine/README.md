[Build and run a web application with the MEAN stack on an Azure Linux virtual machine](https://docs.microsoft.com/en-us/learn/modules/build-a-web-app-with-mean-on-a-linux-vm/) (~42 mins)

# Introduction

You're a software developer at an online retail store that specializes in children's books. You're building a new web site for your business. You haven't yet fully defined the exact needs for your site, but you'll be responsible for the site from start to finish. You have lots of experience in JavaScript, so you want to find a solution that plays well to your strengths.

You've heard about the MEAN stack (MongoDB, Express.js, AngularJS, and Node.js) and you know it uses JavaScript. So you decide to try it out by building a MEAN stack and a basic web application that stores information about books. You can use what you learned to get a jump-start on your new web site.

## Learning objectives

In this module, you will:

- Decide if the MEAN web stack is right for you
- Create an Ubuntu Linux VM to host your web app
- Install the MEAN stack components on your VM
- Create a basic web app on your MEAN stack

## Prerequisites

- Experience using Bash from the command line
- Familiarity or interest in using JavaScript to build web applications

# Decide if MEAN is right for you

MEAN is a development stack for building and hosting web applications. MEAN is an acronym for its component parts: MongoDB, Express, AngularJS, and Node.js.

The main reason you might consider MEAN is if you're familiar with JavaScript. Here are some other reasons you might want to choose MEAN, or choose a different development stack for your next web application.

## Why would I pick MEAN?

All of the components of the MEAN stack are reliable, well-understood, and open source, but so are many other development stacks. Here are some reasons you might choose MEAN over other development stacks.

### Your data isn't highly structured

MongoDB is what's called a **NoSQL** database. A NoSQL database doesn't require data to be structured in a pre-defined way as it would with a relational database like Microsoft SQL Server or MySQL. Instead, MongoDB stores its data in JSON-like documents that don't require the rigid data structures that MySQL or other relational databases require.

### MEAN is well documented

The components of the MEAN stack are all popular right now. Resources for working with MongoDB, Express, AngularJS, and Node.js are easy to find.

### MEAN runs almost anywhere

You can also develop MEAN stack applications from your favorite development environment – whether that's Windows, macOS, or Linux.

## Why might MEAN not be right for me?

Here are some reasons you might want to choose a development stack other than MEAN.

Even if you decide that MEAN is not right for you, you might still be interested in this module. Many of the patterns you'll see apply to other kinds of web application frameworks.

### Your data is highly structured

If your data is highly structured, you might benefit from putting your data in a relational database such as Microsoft SQL Server or MySQL.

### JavaScript is not your strongest skill

If you prefer another language over JavaScript, there may be an alternative framework out there for you.

For example, the LAMP stack, which consists of Linux, Apache, MySQL, and PHP (sometimes with Perl or Python instead of PHP), might better align to your strengths and experience.

# Exercise - Create a VM to host your web application

Like most application frameworks, you can run your MEAN stack application in many different environments. You can run your application on a physical computer in your server room, on a virtual machine, or in containers.

Here you'll run your application on a VM running on Azure. MEAN supports many different operating systems. For learning purposes, here you'll use Ubuntu Linux.

## Create an Ubuntu Linux VM

Normally, you create a _resource group_ before you create other resources on Azure. A resource group is a container that holds the resources that are related for an Azure solution. For this exercise, the Azure sandbox provides a resource group for you. However, when you are working in your own Azure subscription, you would use the following command to create a resource group in a location near you.

```sh
az group create \
  --name <resource-group-name> \
  --location <resource-group-location>
```

From Cloud Shell, run the `az vm create` command to create an Ubuntu VM.

```sh
az vm create \
  --resource-group learn-bae85f89-002e-410e-a7ca-3a258e6408f6 \
  --name MeanStack \
  --image Canonical:UbuntuServer:16.04-LTS:latest \
  --admin-username azureuser \
  --generate-ssh-keys
```

The command takes about two minutes to complete. When the command finishes, you'll see output similar to this.

```json
{
  "fqdns": "",
  "id": "/subscriptions/f897b4e2-0504-4cf2-989a-7c363660153d/resourceGroups/learn-bae85f89-002e-410e-a7ca-3a258e6408f6/providers/Microsoft.Compute/virtualMachines/MeanStack",
  "location": "westus",
  "macAddress": "00-22-48-06-DC-CB",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "104.209.46.71",
  "resourceGroup": "learn-bae85f89-002e-410e-a7ca-3a258e6408f6",
  "zones": ""
}
```

The VM's name is "MeanStack". You'll use this name in future commands to identify the VM you want to work with.

Open port 80 on the VM to allow incoming HTTP traffic to the web application you'll later create.

```sh
az vm open-port \
  --port 80 \
  --resource-group learn-bae85f89-002e-410e-a7ca-3a258e6408f6 \
  --name MeanStack
```

Create an SSH connection to your VM.

Although the output from the `az vm create` command displays your VM's public IP address, you may find it useful to store the address in a Bash variable.

Start by running `az vm show`. This command saves the IP address in a Bash variable named `ipaddress`.

```sh
ipaddress=$(az vm show \
  --name MeanStack \
  --resource-group learn-bae85f89-002e-410e-a7ca-3a258e6408f6 \
  --show-details \
  --query [publicIps] \
  --output tsv)
```

Connect to your VM like this.

```sh
ssh azureuser@$ipaddress
```

When prompted, answer "yes" to save the VM's identity locally so future connections are trusted.

You'll use the SSH connection to configure software on the virtual machine in the next parts.

## Summary

With your Ubuntu VM ready to go, you're ready to install each component of the MEAN stack. You'll start by installing MongoDB.

# Exercise - Install MongoDB

Many applications require a database. Here you'll install MongoDB, the "M" in the MEAN stack. It's a popular NoSQL database solution that's free and open source. A NoSQL database doesn't require data to be structured in a pre-defined way as it would with a relational database like SQL Server or MySQL.

MongoDB stores its data in JSON-like documents that don't require rigid data structures. You interact with MongoDB using queries and commands sent using JavaScript Object Notation, or JSON.

## What MongoDB editions are available?

MongoDB provides two editions:

- MongoDB Community Server
- MongoDB Enterprise Server

Here you'll install MongoDB Community Server. Later, you'll use MongoDB to store information about books.

## How do I install MongoDB?

You can install MongoDB on Linux, macOS, and Windows. For learning purposes, here you'll install MongoDB on Ubuntu using Ubuntu's `apt` package manager.

The installation process varies depending on your operating system. If you're not familiar with Ubuntu, you can still follow along to get a sense for how things work.

Later, you can [check out the installation guide](https://docs.mongodb.com/manual/administration/install-community) to learn more.

## Install MongoDB

Here you'll install MongoDB with just a few commands. You'll work from the SSH connection to the Ubuntu VM that you created in the previous unit.

First, we'll make sure all current packages are up to date.

```sh
sudo apt update && sudo apt upgrade -y
```

Remember - we're using SSH to connect with our `azureuser` account. We need `sudo` to run the above commands with administrative privileges.

Install the MongoDB package.

```sh
sudo apt-get install -y mongodb
```

Once the installation completes, the service should automatically start up. Let's confirm this by running this command.

```sh
sudo systemctl status mongodb
```

You should see the service running.

```sh
azureuser@MeanStack:~$ sudo systemctl status mongodb
● mongodb.service - An object/document-oriented database
   Loaded: loaded (/lib/systemd/system/mongodb.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2020-07-07 23:02:09 UTC; 8s ago
     Docs: man:mongod(1)
 Main PID: 22154 (mongod)
   CGroup: /system.slice/mongodb.service
           └─22154 /usr/bin/mongod --config /etc/mongodb.conf

Jul 07 23:02:09 MeanStack systemd[1]: Started An object/document-oriented database.
```

Run `mongod --version` to verify the installation.

```sh
mongod --version
```

Keep your SSH connection open for the next part.

# Exercise - Install Node.js

# Exercise - Create a basic web application

# Summary
