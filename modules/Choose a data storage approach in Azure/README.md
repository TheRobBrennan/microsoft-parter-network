[Choose a data storage approach in Azure](https://docs.microsoft.com/en-us/learn/modules/choose-storage-approach-in-azure/) (~30 mins)

# Introduction

Data comes in different shapes and sizes, and no single storage solution fits all data. For example, an online retail website has a number of distinct data sets that are all used to run the business: product catalog data, media files like photos and videos, and financial business data. Each data set has different requirements, and it's your job to figure out which storage solution is best. The key factors to consider in deciding on the optimal storage solution are: how to classify your data, how your data will be used, and how you can get the best performance for your application.

## Learning objectives

In this module, you will:

- Classify your data as structured, semi-structured, or unstructured
- Determine how your data will be used
- Determine whether your data requires transactions

# Classify your data

An online retail business has different types of data. Each type of data may benefit from a different storage solution.

Application data can be classified in one of three ways: structured, semi-structured, and unstructured. Here, you'll learn how to classify your data so that you can choose the appropriate storage solution.

[VIDEO: Approaches to storing data in the cloud ~2:46](https://www.microsoft.com/en-us/videoplayer/embed/RE2yEuY?pid=RE2yEuY-ax-86-id-oneplayer&postJsllMsg=true&autoplay=false&mute=false&loop=false&market=en-us&playFullScreen=false)

## Structured data

Structured data, sometimes referred to as _relational data_, is data that adheres to a strict schema, so all of the data has the same fields or properties. The shared schema allows this type of data to be easily searched with query languages such as SQL (Structured Query Language). This capability makes this data style perfect for applications such as CRM systems, reservations, and inventory management.

Structured data is often stored in database tables with rows and columns with key columns to indicate how one row in a table relates to data in another row of another table. The below image shows data about students and classes with a relationship to grades that ties them together.

![https://docs.microsoft.com/en-us/learn/modules/choose-storage-approach-in-azure/media/2-relational-db.png](https://docs.microsoft.com/en-us/learn/modules/choose-storage-approach-in-azure/media/2-relational-db.png)

Structured data is straightforward in that it's easy to enter, query, and analyze. All of the data follows the same format. However, forcing a consistent structure also means evolution of the data is more difficult as each record has to be updated to conform to the new structure.

## Semi-structured data

## Unstructured data

## Product catalog data

## Photos and videos

## Business data

## Summary

## Check your knowledge

1. A JSON file is an example of which type of data?

[] Structured
[] Semi-structured
[] Unstructured

2. A video is an example of which type of data?

[] Structured
[] Semi-structured
[] Unstructured

# Determine operational needs

# Group multiple operations in a transaction

# Choose a storage solution on Azure

# Summary
