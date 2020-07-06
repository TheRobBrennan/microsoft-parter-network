[Store application data with Azure Blob storage](https://docs.microsoft.com/en-us/learn/modules/store-app-data-with-azure-blob-storage/) (~50 mins)

# Introduction to Blob storage

Blobs give you file storage in the cloud and an API that lets you build apps to access the data.

Suppose you work at an augmented-reality gaming company. Your game runs on every mobile platform. You want to add a new feature to let users record video clips of their gameplay and upload the clips to your servers. Users will be able to watch clips directly in the game or through your website. You plan to log every upload and viewing for use in analytics and for traceability.

You need a storage solution that can handle thousands of simultaneous uploads, massive amounts of video data, and constantly growing log files. You also need to add the viewing functionality to all your mobile apps and your website, so you want API access from multiple platforms and languages.

Here, you will see how Azure Blob storage could be appropriate for this application.

## Learning objectives

In this module, you will:

- Organize your data with Azure Blob storage
- Create storage resources to hold blobs
- Store and retrieve data from Azure Blob storage

# What are blobs?

Blobs are "files for the cloud". Apps work with blobs in much the same way as they would work with files on a disk, like reading and writing data. However, unlike a local file, blobs can be reached from anywhere with an internet connection.

Azure Blob storage is _unstructured_, meaning that there are no restrictions on the kinds of data it can hold. For example, a blob can hold a PDF document, a JPG image, a JSON file, video content, etc. **Blobs aren't limited to common file formats** — a blob could contain gigabytes of binary data streamed from a scientific instrument, an encrypted message for another application, or data in a custom format for an app you're developing.

**Blobs are usually not appropriate for structured data that needs to be queried frequently**. They have higher latency than memory and local disk and don't have the indexing features that make databases efficient at running queries. However, blobs are **frequently used in combination with databases to store non-queryable data**. For example, an app with a database of user profiles could store profile pictures in blobs. Each user record in the database would include the name or URL of the blob containing the user's picture.

Blobs are used for data storage in many ways across all kinds of applications and architectures:

- Apps that need to transmit large amounts of data using messaging system that supports only small messages. These apps can store data in blobs and send the blob URLs in messages.
- Blob storage can be used like a file system for storing and sharing documents and other personal data.
- Static web assets like images can be stored in blobs and made available for public download as if they were files on a web server.
- Many Azure components use blobs behind the scenes. For example, Azure Cloud Shell stores your files and configuration in blobs, and Azure Virtual Machines uses blobs for hard-disk storage.

Some apps will constantly create, update, and delete blobs as part of their work. Others will use a small set of blobs and rarely change them.

## Storage accounts, containers, and metadata

In Blob storage, every blob lives inside a _blob container_. Y**ou can store an unlimited number of blobs in a container** and **an unlimited number of containers in a storage account**. **Containers are "flat"** — they can only store blobs, not other containers.

Blobs and containers support metadata in the form of name-value string pairs. Your apps can use metadata for anything you like: a human-readable description of a blob's contents to be displayed by the application, a string that your app uses to determine how to process the blob's data, etc.

TIP: Blob storage does not provide any mechanism for searching or sorting blobs by metadata. See the Further Reading section at the end of this module for information about using Azure Cognitive Search to achieve this.

## The Blob storage API and client libraries

The Blob storage API is REST-based and supported by client libraries in many popular languages. It lets you write apps that create and delete blobs and containers, upload and download blob data, and list the blobs in a container.

# Design a storage organization strategy

When designing an app that needs to store data, it's important to think about how the app is going to organize data across storage accounts, containers, and blobs.

## Storage accounts

A single storage account is flexible enough to organize your blobs however you like, but you should use additional storage accounts as necessary to logically separate costs and control access to data.

## Containers and blobs

The nature of your application and the data that it stores should drive your strategy for naming and organizing containers and blobs.

Apps using blobs as part of a storage scheme that includes a database often don't need to rely heavily on organization, naming, or metadata to indicate anything about their data. Such apps commonly use identifiers like GUIDs as blob names and reference these identifiers in database records. The app will use the database to determine where blobs are stored and the kind of data they contain.

Other apps may use Azure Blob storage more like a personal file system, where container and blob names are used to indicate meaning and structure. Blob names in these kinds of apps will often look like traditional file names and include file name extensions like .jpg to indicate what kind of data they contain. They'll use virtual directories (see below) to organize blobs and will frequently use metadata tags to store information about blobs and containers.

There are a few key things to consider when deciding how to organize and store blobs and containers.

### Naming limitations

Container and blob names must conform to a set of rules, including length limitations and character restrictions. See the Further Reading section at the end of this module for more specific information about naming rules.

### Public access and containers as security boundaries

By default, all blobs require authentication to access. However, individual containers can be configured to allow public downloading of their blobs without authentication. This feature supports many use cases, such as hosting static website assets and sharing files. This is because downloading blob contents works the same way as reading any other kind of data over the web: you just point a browser or anything that can make a GET request at the blob URL.

**Enabling public access is important for scalability because data downloaded directly from Blob storage doesn't generate any traffic in your server-side app**. Even if you don't immediately take advantage of public access or if you will use a database to control data access via your application, plan on using separate containers for data you want to be publicly available.

CAUTION: Blobs in a container configured for public access can be downloaded without any kind of authentication or auditing by anyone who knows their storage URLs. Never put blob data in a public container that you don't intend to share publicly.

In addition to public access, Azure has a shared access signature feature that allows fine-grained permissions control on containers. Precision access control enables scenarios that further improve scalability, so thinking about containers as security boundaries in general is a helpful guideline.

### Blob name prefixes (virtual directories)

Technically, **containers are "flat" and do not support any kind of nesting or hierarchy**. But if you give your blobs hierarchical names that look like file paths (such as `finance/budgets/2017/q1.xls`), the A**PI's listing operation can filter results to specific prefixes**. This allows you to navigate the list as if it was a hierarchical system of files and folders.

This feature is often called _virtual directories_ because some tools and client libraries use it to visualize and navigate Blob storage as if it was a file system. Each folder navigation triggers a separate call to list the blobs in that folder.

Using names that are like file names for blobs is a common technique for organizing and navigating complex blob data.

### Blob types

There are three different kinds of blobs you can store data in:

- **Block blobs** are composed of blocks of different sizes that can be uploaded independently and in parallel. Writing to a block blob involves uploading data to blocks and committing them to the blob.
- **Append blobs** are specialized block blobs that support only appending new data (not updating or deleting existing data), but they're very efficient at it. Append blobs are great for **scenarios like storing logs or writing streamed data**.
- **Page blobs** are designed for scenarios that involve random-access reads and writes. **Page blobs are used to store the virtual hard disk (VHD) files** used by Azure Virtual Machines, but they're great for any scenario that involves random access.

Block blobs are the best choice for most scenarios that don't specifically call for append or page blobs. Their block-based structure supports very fast uploads and downloads and efficient access to individual pieces of a blob. The process of managing and committing blocks is automatically handled by most client libraries, and some will also handle parallel uploads and downloads to maximize performance.

## Check your knowledge

1. Suppose you need to store profile and order information about your customers. You need to query the data to answer questions like "who are my top 100 customers?" and "how many customers live in a given geographic region?". True or false: blob storage is a good choice for this data?

[] True
[ x ] False

2. Blobs provide unstructured data storage. What does unstructured mean?

[] Blobs can't be organized or named.
[ x ] There are no restrictions on the type of data you can store in blobs.
[] Blobs can't contain structured data, like JSON or XML.

# Exercise - Create Azure storage resources

# Exercise - Configure and initialize the client library

# Exercise - Get blob references

# Exercise - Blob uploads and downloads

# Clean up and knowledge check
