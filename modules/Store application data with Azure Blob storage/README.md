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

# Exercise - Create Azure storage resources

# Exercise - Configure and initialize the client library

# Exercise - Get blob references

# Exercise - Blob uploads and downloads

# Clean up and knowledge check
