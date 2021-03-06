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

Here you'll install Node.js, the N in the MEAN acronym. Like MongoDB, Node.js is open source.

Node.js will act as the server-side host for your web application and handle inbound HTTP traffic. Node.js also provides you with a way to communicate with your MongoDB installation, which you'll see later.

## What versions of Node.js are available?

You can get Node.js in two ways:

- **Long Term Support (LTS)** - LTS is generally considered to be more stable and is recommended for most users and for production environments.
- **Current** - Current is for those who want to experiment with the latest features. Because it can introduce breaking changes between releases, it's not recommended for production environments.

Here you'll use Node.js LTS.

## How do I install Node.js?

Like MongoDB, you can run Node.js on Windows, macOS, and Linux. Node.js also supports Unix-based operating systems such as SunOS and AIX.

Just as with MongoDB, here you'll register the Node.js repository so that `apt` can locate the package.

Recall that you're working with an Ubuntu VM. Later, you can [check out the installation guide](https://nodejs.org/en/download/package-manager) to learn how to install Node.js on your favorite platform.

## Install Node.js

Here you'll install Node.js. As with MongoDB, the process involves registering the Node.js repository so that `apt` can locate the package

IMPORTANT: Here, you'll work from the SSH connection to the Ubuntu VM that you created earlier in this module.

Register the Node.js repository so the package manager can locate the packages, like this.

```sh
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
```

Install the Node.js package.

```sh
sudo apt-get install -y nodejs
```

Run `node -v` to verify the installation.

```sh
node -v
```

The output shows that you have the latest LTS version of Node.js.

## Exit your SSH session

```sh
exit
```

# Exercise - Create a basic web application

So far, you have MongoDB and Node.js installed on your Ubuntu VM. Now it's time to create a basic web application to see things in action. Along the way, you'll see how AngularJS and Express fit in.

A great way to learn is by example. The web application you'll build implements a basic book database. The web application enables you to list information about books, add new books, and delete existing books.

The web application you'll see here demonstrates many concepts that apply to most MEAN stack web applications. Based on your needs and interests, you can explore the features you need to build your own MEAN stack applications.

Here's what the Books web application will look like.

![https://docs.microsoft.com/en-us/learn/modules/build-a-web-app-with-mean-on-a-linux-vm/media/6-book-page.png](https://docs.microsoft.com/en-us/learn/modules/build-a-web-app-with-mean-on-a-linux-vm/media/6-book-page.png)

Here's how each component of the MEAN stack fits in.

- MongoDB stores information about books.
- Express routes each HTTP request to the appropriate handler.
- AngularJS connects the user interface with the program's business logic.
- Node.js hosts the server-side application.

IMPORTANT: For learning purposes, here you're building a basic web application. Its purpose is to test out your MEAN stack and give you a sense of how it works. The application is not sufficiently secure or ready for production use.

## What about Express?

So far, you've installed MongoDB and Node.js on your VM. What about Express, the E in the MEAN acronym?

Express is a web server framework that's built for Node.js that simplifies the process for building web applications.

The main purpose of Express is to handle request routing. _Routing_ refers to how the application responds to a request to a specific endpoint. An endpoint is made up of a path, or URI, and a request method, such as GET or POST. For example, you might respond to a GET request to the /book endpoint by providing the list of all books in the database. You might respond to a POST request to the same endpoint by adding an entry to the database based on fields the user entered into a web form.

In the web application you'll build shortly, you'll use Express to route HTTP requests and to return web content to your user. Express can also help your web applications work with HTTP cookies and process query strings.

Express is a Node.js package. You use the `npm` utility, which comes with Node.js, to install and manage Node.js packages. Later in this part, you'll create a file named **package.json** to define Express and other dependencies and then run the `npm install` command to install these dependencies.

## What about AngularJS?

Like Express, you haven't yet installed AngularJS, the A in the MEAN acronym.

AngularJS makes web applications easier to write and test because it enables you to better separate the _appearance_ of your web page, your HTML code, from how your web page behaves. If you're familiar with the model–view–controller (MVC) pattern or the concept of data binding, AngularJS will be familiar to you.

AngularJS is what's called a front-end JavaScript framework, which means it needs to only be available on the client that accesses the application. In other words, AngularJS runs in your user's web browser, not on your web server. And because AngularJS is JavaScript, you can use it to easily fetch data from your web server to show on the page.

You don't really _install_ AngularJS. Instead, you add a reference to the JavaScript file in your HTML page, just as you do with other JavaScript libraries. There are several ways to include AngularJS in your web pages. Here you'll load AngularJS from a content delivery network, or CDN. A CDN is a way to distribute images, video, and other content geographically to improve download speeds.

Don't add this code quite yet, but here's an example that loads AngularJS from a CDN. You would typically add this code to the `<head>` section of your HTML page.

```html
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.7.2/angular.min.js"></script>
```

NOTE: Don't confuse AngularJS with Angular. While many of the concepts are similar between the two, AngularJS is the predecessor to Angular. AngularJS is still commonly used for building web applications. While AngularJS is based on JavaScript, Angular is based on TypeScript, a programming language that makes it easier to write JavaScript programs.

## How will I build the application?

Here you'll use a basic process. You'll write application code from Cloud Shell and then use SCP, or secure copy protocol, to copy the files to your VM. Then you'll start the Node.js application and see the results in your browser.

In practice, you would typically write and test your web application in a more local environment, such as from your laptop or from a virtual machine you run locally. You might then store your code in a revision control system such as Git and use a continuous integration and continuous delivery, or CI/CD, system such as Azure DevOps to test your changes and upload them to your VM. We'll point you to more resources at the end of this module.

## Create the Books web application

Here you'll create all the code, script, and HTML files that make up your web application. For brevity, we'll highlight the important parts of each file but won't go into complete details.

### Create the files

From Cloud Shell, run these commands to create the folders and files for your web application.

```sh
cd ~
mkdir Books
touch Books/server.js
touch Books/package.json
mkdir Books/app
touch Books/app/model.js
touch Books/app/routes.js
mkdir Books/public
touch Books/public/script.js
touch Books/public/index.html
```

Here's what's included:

- `Books` – the project's root directory.
  - `server.js` defines the entry point to the web application. It loads the required Node.js packages, specifies the port to listen on, and begins listening for incoming HTTP traffic.
  - `package.json` provides information about your application, including its name, description, and what Node.js packages your application needs to run.
- `Books/app` – contains code that runs on the server.
  - `model.js` defines the database connection and schema. Think of it as the data model for your application.
  - `routes.js` handles request routing. For example, it defines GET requests to the /book endpoint by providing the list of all books in the database.
- `Books/public` – contains files that are served directly to the client's browser.
  - `index.html` contains the index page. It contains a web form that enables the user to submit information about books. It also displays all books in the database and enables you to delete entries from the database.
  - `script.js` contains JavaScript code that runs in your user's browser. It can send requests to the server to list books, add books to the database, and delete books from the database.

Run the `code` command to open your files through the Cloud Shell editor.

```sh
code Books
```

### Create the data model

From the editor, open `app/model.js` and add the following.

```js
var mongoose = require("mongoose")
var dbHost = "mongodb://localhost:27017/Books"
mongoose.connect(dbHost, { useNewUrlParser: true })
mongoose.connection
mongoose.set("debug", true)
var bookSchema = mongoose.Schema({
  name: String,
  isbn: { type: String, index: true },
  author: String,
  pages: Number,
})
var Book = mongoose.model("Book", bookSchema)
module.exports = Book
```

This code uses Mongoose to simplify the process of transferring data in and out of MongoDB. Mongoose is a schema-based system for modeling data. The code defines a database document called "Book" with the provided schema. The schema defines four fields that describe a single book:

- The book's name, or title
- Its International Standard Book Number, or ISBN, which uniquely identifies the book
- Its author
- The number of pages it contains

Next, you'll create HTTP handlers that map GET, POST, and DELETE requests to database operations.

### Create the Express routes that handle HTTP requests

From the editor, open `app/routes.js` and add the following code.

```js
var path = require("path")
var Book = require("./model")
var routes = function (app) {
  app.get("/book", function (req, res) {
    Book.find({}, function (err, result) {
      if (err) throw err
      res.json(result)
    })
  })
  app.post("/book", function (req, res) {
    var book = new Book({
      name: req.body.name,
      isbn: req.body.isbn,
      author: req.body.author,
      pages: req.body.pages,
    })
    book.save(function (err, result) {
      if (err) throw err
      res.json({
        message: "Successfully added book",
        book: result,
      })
    })
  })
  app.delete("/book/:isbn", function (req, res) {
    Book.findOneAndRemove(req.query, function (err, result) {
      if (err) throw err
      res.json({
        message: "Successfully deleted the book",
        book: result,
      })
    })
  })
  app.get("*", function (req, res) {
    res.sendFile(path.join(__dirname + "/public", "index.html"))
  })
}
module.exports = routes
```

We have routes defined for:

- GET /book - Retrieves all books from the database.
- POST /book - Creates a `Book` object based on the fields the user provided on the web form and writes that object to the database.
- DELETE /book/:isbn - Deletes the book as identified by its ISBN from the database.
- GET \* - Returns the index page when no other route is matched.

Express can serve up HTTP responses directly in the route handling code or it can serve up static content from files. This code shows both. The first three routes return JSON data for book API requests. The fourth route (the default case) returns the contents of the index file, `index.html`.

### Create the client-side JavaScript application

From the editor, open `public/script.js` and add this code:

```sh
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
    var getData = function() {
        return $http( {
            method: 'GET',
            url: '/book'
        }).then(function successCallback(response) {
            $scope.books = response.data;
        }, function errorCallback(response) {
            console.log('Error: ' + response);
        });
    };
    getData();
    $scope.del_book = function(book) {
        $http( {
            method: 'DELETE',
            url: '/book/:isbn',
            params: {'isbn': book.isbn}
        }).then(function successCallback(response) {
            console.log(response);
            return getData();
        }, function errorCallback(response) {
            console.log('Error: ' + response);
        });
    };
    $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name +
        '", "isbn": "' + $scope.Isbn +
        '", "author": "' + $scope.Author +
        '", "pages": "' + $scope.Pages + '" }';
        $http({
            method: 'POST',
            url: '/book',
            data: body
        }).then(function successCallback(response) {
            console.log(response);
            return getData();
        }, function errorCallback(response) {
            console.log('Error: ' + response);
        });
    };
});
```

Notice how this code defines a module named "myApp" and a controller named "myCtrl". We won't go into full details about how module and controllers work here, but you'll use these names in the next step to bind the user interface (HTML code) with the application's business logic.

Earlier, you created four routes that handle various GET, POST, and DELETE operations on the server. This code resembles those same operations, but from the client side (the user's web browser).

The `getData` function, for example, sends a GET request to the `/book` endpoint. Recall that the server handles this request by retrieving information about all books from the database and returning that information as JSON data. Notice how the resulting JSON data is assigned to the `\$scope.books` variable. You'll see how this affects what the user sees on the web page in the next step.

This code calls the `getData` function when the page loads. You can examine the `del_book` and `add_book` functions to get a sense for how they work. You don't need client-side code to match the server's default handler because the default handler returns the index page and not JSON data.

### Create the user interface

From the editor, open `public/index.html` and add this code:

```html
<!DOCTYPE html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.7.2/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name" /></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn" /></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author" /></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages" /></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr />
    <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>
        </tr>
        <tr ng-repeat="book in books">
          <td>
            <input
              type="button"
              value="Delete"
              data-ng-click="del_book(book)"
            />
          </td>
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>
        </tr>
      </table>
    </div>
  </body>
</html>
```

This code creates a basic HTML form with four fields to submit book data and a table that displays all the books stored in the database.

Although this is standard HTML code, the `ng-` HTML attributes may be unfamiliar to you. These HTML attributes wire up the AngularJS code to the user interface. For example, when the user clicks the **Add** button, AngularJS calls the `add_book` function, which sends the form data to the server.

You can examine the code here to get a sense of how each of the `ng-` attributes relate to application's business logic.

### Create the Express server to host the application

From the editor, open `server.js` and add this code:

```js
var express = require("express")
var bodyParser = require("body-parser")
var app = express()
app.use(express.static(__dirname + "/public"))
app.use(bodyParser.json())
require("./app/routes")(app)
app.set("port", 80)
app.listen(app.get("port"), function () {
  console.log("Server up: http://localhost:" + app.get("port"))
})
```

This code creates the web application itself. It serves static files from the public directory and uses the routes you defined in previously to handle requests.

### Define package information and dependencies

Recall that `package.json` provides information about your application, including its name, description, and what Node.js packages your application needs to run.
From the editor, open `package.json` and add this code:

```json
{
  "name": "books",
  "description": "Sample web app that manages book information.",
  "license": "MIT",
  "repository": {
    "type": "git",
    "url": "https://github.com/MicrosoftDocs/mslearn-build-a-web-app-with-mean-on-a-linux-vm"
  },
  "main": "server.js",
  "dependencies": {
    "express": "~4.16",
    "mongoose": "~5.3",
    "body-parser": "~1.18"
  }
}
```

You see information, or metadata, about your application including its name, description, and license.

The `repository` field specifies where the code is maintained. For reference, you can later review the code on GitHub at the URL shown here.

The `main` field defines the application's entry point. It's provided here for completeness but it's not important because you're not planning to publish your application as a Node.js package for others to download and use.

The `dependencies` field is important. It defines the Node.js packages your application needs. Shortly, you'll connect to your VM a second time and run the `npm install` command to install these packages.

Node packages typically use the [Semantic Versioning](https://semver.org/) versioning scheme. The version number contains three components: major version, minor version, and patch. The tilde `~` notation here tells npm to install the latest patch version under the provided major and minor versions. The versions you see here are the latest this module was tested with. In practice, you can increment the version over time as you update and test your application to use the latest features each dependent package provides.

### Copy the files to your VM

You're all done editing files. Ensure that you saved changes to each file and then close the editor.

To close the editor, click the ellipses in the corner and then select **Close Editor**.

Run the following `scp` command to copy the contents of the `~/Books` directory in your Cloud Shell session to the same directory name on your VM.

```sh
scp -r ~/Books azureuser@$ipaddress:~/Books
```

Sample output:

```sh
model.js                                              100%  392     0.4KB/s   00:00
routes.js                                             100% 1134     1.1KB/s   00:00
server.js                                             100%  334     0.3KB/s   00:00
index.html                                            100% 1184     1.2KB/s   00:00
script.js                                             100% 1347     1.3KB/s   00:00
package.json                                          100%  366     0.4KB/s   00:00
```

## Install additional Node packages

Let's say that during the development process, you identified additional Node packages that you want to use. For example, recall that `app/model.js` starts with this line.

```js
var mongoose = require("mongoose")
```

Recall that the application uses Mongoose to help transfer data in and out of your MongoDB database.

The application also requires Express and the body-parser packages. body-parser is a plugin that enables Express to work with data from the web form sent by the client.

Let's connect to your VM and install the packages you specified in package.json.

Before you connect to your VM, make sure you have your VM's IP address handy. If you don't have it, run these commands from Cloud Shell to retrieve it.

```sh
ipaddress=$(az vm show \
  --name MeanStack \
  --resource-group learn-bae85f89-002e-410e-a7ca-3a258e6408f6 \
  --show-details \
  --query [publicIps] \
  --output tsv)
```

```sh
echo $ipaddress
```

Like you did earlier, create an SSH connection to your VM.

```sh
ssh azureuser@$ipaddress
```

Move to the Books directory under the home directory.

```sh
cd ~/Books
```

Run `npm install` to install the dependent packages.

```sh
npm install
```

Keep your SSH connection open for the next part.

## Test the application

You're now ready to test out your Node.js web application!

From the `~/Books` directory, run this command to start the web application.

```sh
sudo node server.js
```

This command starts the application by listening on port 80 for incoming HTTP requests.

From a separate browser tab, navigate to your VM's public IP address - [http://104.209.46.71](http://104.209.46.71)

You see the index page, which includes a web form.

![https://docs.microsoft.com/en-us/learn/modules/build-a-web-app-with-mean-on-a-linux-vm/media/6-book-page.png](https://docs.microsoft.com/en-us/learn/modules/build-a-web-app-with-mean-on-a-linux-vm/media/6-book-page.png)

Try adding a few books to the database. Each time you add a book, the page updates the complete list of books.

![https://docs.microsoft.com/en-us/learn/modules/build-a-web-app-with-mean-on-a-linux-vm/media/6-book-sample-entries.png](https://docs.microsoft.com/en-us/learn/modules/build-a-web-app-with-mean-on-a-linux-vm/media/6-book-sample-entries.png)

You can also click the **Delete** button to delete a book from the database.

# Summary

MEAN is a development stack for building and hosting web applications. Recall that MEAN is an acronym for its component parts: MongoDB, Express, AngularJS, and Node.js.

In this module, you learned when the MEAN stack is a good choice for web development and when you might want to choose something else. The main reason you might consider MEAN is if you're familiar with JavaScript.

To see the MEAN stack in action, you created an Ubuntu virtual machine on Azure and installed the MEAN stack on it for web development.

With your MEAN stack in place, you created a basic book inventory web application. To summarize, the web application uses:

- MongoDB to store information about books.
- Express to route each HTTP request to the appropriate handler.
- AngularJS to connect the user interface with the program's business logic.
- Node.js to host the server-side application.

You can [find the source code for the web application](https://github.com/MicrosoftDocs/mslearn-build-a-web-app-with-mean-on-a-linux-vm) on GitHub.

## Learn more

In this module, you got a sense of how the MEAN stack works and brought up a basic web application that uses it. The next step is to start building applications that solve your own business challenges. You can then deploy your applications to Azure and use automated processes to monitor your applications and make them better. Here are some resources where you can learn more.

### Learn more about MEAN stack application development

### Learn about the Azure Web Apps service

In this module, you used a VM to host your web application. A VM gives you more control over the environment and might best fit how you currently manage your deployments. But there are other ways to host web applications. Check out [Create a Node.js web app in Azure](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-get-started-nodejs) to learn how to use the Azure Web Apps service to simplify your deployments.

### Automate your deployments

Also in this module, you used a mainly manual process to configure your VM and run your app. As your process matures, you can use a more automated process to deploy changes more quickly and more reliably. Check out [Create a CI/CD pipeline for Node.js with the Azure DevOps Project](https://docs.microsoft.com/en-us/azure/devops-project/azure-devops-project-nodejs) to learn how to use Azure DevOps to deploy your Node.js application as part of a continuous integration and continuous delivery (CI/CD) pipeline.

## Check your knowledge

1. Which is a good reason to pick MEAN for your development stack?

[] You're working with highly structured, relational data.
[] MEAN apps run faster than ones created with other development stacks.
[ x ] Everyone on your development team is an expert with JavaScript.

2. What's a NoSQL database?

[] It's a term used to describe any free, open-source database software.
[ x ] It's a database that doesn't require data to be structured in a pre-defined way.
[] It's a kind of relational database, but doesn't use the SQL language to access and manipulate data.

3. What's the role of AngularJS in the MEAN stack?

[ x ] AngularJS implements data binding for HTML and JavaScript. For example, you can use AngularJS to retrieve items from a database and display those items in the UI.
[] It's a browser plugin that gets installed on your user's computer.
[] It helps run the web application from the server side.
