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

[VIDEO: Approaches to storing data in the cloud (~2:46)](https://www.microsoft.com/en-us/videoplayer/embed/RE2yEuY?pid=RE2yEuY-ax-86-id-oneplayer&postJsllMsg=true&autoplay=false&mute=false&loop=false&market=en-us&playFullScreen=false)

## Structured data

Structured data, sometimes referred to as _relational data_, is data that adheres to a strict schema, so all of the data has the same fields or properties. The shared schema allows this type of data to be easily searched with query languages such as SQL (Structured Query Language). This capability makes this data style perfect for applications such as CRM systems, reservations, and inventory management.

Structured data is often stored in database tables with rows and columns with key columns to indicate how one row in a table relates to data in another row of another table. The below image shows data about students and classes with a relationship to grades that ties them together.

![https://docs.microsoft.com/en-us/learn/modules/choose-storage-approach-in-azure/media/2-relational-db.png](https://docs.microsoft.com/en-us/learn/modules/choose-storage-approach-in-azure/media/2-relational-db.png)

Structured data is straightforward in that it's easy to enter, query, and analyze. All of the data follows the same format. However, forcing a consistent structure also means evolution of the data is more difficult as each record has to be updated to conform to the new structure.

## Semi-structured data

Semi-structured data is less organized than structured data, and is not stored in a relational format, as the fields do not neatly fit into tables, rows, and columns. Semi-structured data contains tags that make the organization and hierarchy of the data apparent - for example, key/value pairs. Semi-structured data is also referred to as non-relational or _NoSQL data_. The expression and structure of the data in this style is defined by a _serialization language_.

For software developers, data serialization languages are important because they can be used to write data stored in memory to a file, sent to another system, parsed and read. The sender and receiver don’t need to know details about the other system, as long as the same serialization language is used, the data can be understood by both systems.

[VIDEO: Data Serialization Languages (~2:14)](https://sec.ch9.ms/ch9/8fcb/f9263931-05e4-4767-8168-69cfb5358fcb/DataSerializationLang_mid.mp4)

### Common formats

Today, there are three common serialization languages you're likely to encounter:

**XML**, or extensible markup language, was one of the first data languages to receive widespread support. It's text-based, which makes it easily human and machine-readable. In addition, parsers for it can be found for almost all popular development platforms. XML allows you to express relationships and has standards for schema, transformation, and even displaying on the web.

Here's an example of a person with hobbies expressed in XML.

```xml
<Person Age="23">
    <FirstName>John</FirstName>
    <LastName>Smith</LastName>
    <Hobbies>
        <Hobby Type="Sports">Golf</Hobby>
        <Hobby Type="Leisure">Reading</Hobby>
        <Hobby Type="Leisure">Guitar</Hobby>
   </Hobbies>
</Person>
```

XML expresses the shape of the data using tags. These tags come in two forms: elements such as `<FirstName>` and \_attributes that can be expressed in text like `Age="23"`. Elements can have child elements to express relationships - such as the `<Hobbies>` tag above which is expressing a collection of `Hobby` elements.

XML is flexible and can express complex data easily. However it tends to be more verbose making it larger to store, process, or pass over a network. As a result, other formats have become more popular.

**JSON** – or JavaScript Object Notation, has a lightweight specification and relies on curly braces to indicate data structure. Compared to XML, it is less verbose and easier to read by humans. JSON is frequently used by web services to return data.

Here's the same person expressed in JSON.

```json
{
  "firstName": "John",
  "lastName": "Doe",
  "age": "23",
  "hobbies": [
    { "type": "Sports", "value": "Golf" },
    { "type": "Leisure", "value": "Reading" },
    { "type": "Leisure", "value": "Guitar" }
  ]
}
```

Notice that this format isn't as formal as XML. It's closer to a key/value pair model than a formal data expression. As you might guess from the name, JavaScript has built-in support for this format - making it very popular for web development. Like XML, other languages have parsers you can use to work with this data format. The downside to JSON is that it tends to be more programmer-oriented making it harder for non-technical people to read and modify.

**YAML** – or YAML Ain’t Markup Language, is a relatively new data language that’s growing quickly in popularity in part due to its human-friendliness. The data structure is defined by line separation and indentation, and reduces the dependency on structural characters like parentheses, commas and brackets.

Here's the same person data expressed in YAML.

```yml
firstName: John
lastName: Doe
age: 23
hobbies:
  - type: Sports
    value: Golf
  - type: Leisure
    value: Reading
  - type: Leisure
    value: Guitar
```

[VIDEO: What is NoSQL / semi-structured data? (~2:20)](https://www.microsoft.com/en-us/videoplayer/embed/RE2yEvd?pid=RE2yEvd-ax-87-id-oneplayer&postJsllMsg=true&autoplay=false&mute=false&loop=false&market=en-us&playFullScreen=false)

## Unstructured data

The organization of unstructured data is ambiguous. Unstructured data is often delivered in files, such as photos or videos. The video file itself may have an overall structure and come with semi-structured metadata, but the data that comprises the video itself is unstructured. Therefore, photos, videos, and other similar files are classified as unstructured data.

Examples of unstructured data include:

- Media files, such as photos, videos, and audio files
- Office files, such as Word documents
- Text files
- Log files

Now that you know the differences between each kind of data, let's look at the data sets used in an online retail business, and classify them.

## Product catalog data

Product catalog data for an online retail business is fairly structured in nature, as each product has a product SKU, a description, a quantity, a price, size options, color options, a photo, and possibly a video. So, this data appears relational to start with, as it all has the same structure. However, as you introduce new products or different kinds of products, you may want to add different fields as time goes on. For example, new tennis shoes you're carrying are Bluetooth-enabled, to relay sensor data from the shoe to a fitness app on the user’s phone. This appears to be a growing trend, and you want to enable customers to filter on "Bluetooth-enabled" shoes in the future. You don't want to go back and update all your existing shoe data with a Bluetooth-enabled property, you simply want to add it to new shoes.

With the addition of the Bluetooth-enabled property, your shoe data is no longer homogenous, as you've introduced differences in the schema. If this is the only exception you expect to encounter, you can go back and normalize the existing data so that all products included a "Bluetooth-enabled" field to maintain a structured, relational organization. However, if this is just one of many specialty fields that you envision supporting in the future, then the classification of the data is semi-structured. The data is organized by tags, but each product in the catalog can contain unique fields.

Data classification: Semi-structured

## Photos and videos

The photos and videos displayed on product pages are unstructured data. Although the media file may contain metadata, the body of the media file is unstructured.

Data classification: Unstructured

## Business data

Business analysts want to implement business intelligence to perform inventory pipeline evaluations and sales data reviews. In order to perform these operations, data from multiple months needs to be aggregated together, and then queried. Because of the need to aggregate similar data, this data must be structured, so that one month can be compared against the next.

Data classification: Structured

## Summary

Data may be classified in one of three ways: structured, semi-structured, and unstructured. Understanding the differences so that you can classify your own data will help you choose the correct storage solution.

To recap, structured data is organized data that neatly fits into rows and columns in tables. Semi-structured data is still organized and has clear properties and values, but there's variety to the data. Unstructured data doesn't fit neatly into tables, nor does it have a schema.

## Check your knowledge

1. A JSON file is an example of which type of data?

[] Structured
[ x ] Semi-structured
[] Unstructured

2. A video is an example of which type of data?

[] Structured
[] Semi-structured
[ x ] Unstructured

# Determine operational needs

Once you've identified the kind of data you're dealing with (structured, semi-structured, or unstructured), the next step is to determine how you'll use the data. For example, as an online retailer you know customers need quick access to product data, and business users need to run complex analytical queries. As you work through these requirements, taking your data classification into account, you can start to plan your data storage solution.

Here, you'll go through some of the questions to ask when determining what to do with your data.

## Operations and latency

What are the main operations you'll be completing on each data type, and what are the performance requirements?

Ask yourself these questions:

- Will you be doing simple lookups using an ID?
- Do you need to query the database for one or more fields?
- How many create, update, and delete operations do you expect?
- Do you need to run complex analytical queries?
- How quickly do these operations need to complete?

Let’s walk through each of the data sets with these questions in mind and discuss the requirements.

## Product catalog data

For product catalog data in the online retail scenario, customer needs are the highest priority. Customers will want to query the product catalog to find, for example, all men's shoes, then men's shoes on sale, and then men's shoes on sale in a particular size. Customer needs may require lots of read operations, with the ability to query on certain fields.

In addition, when customers place orders, the application must update product quantities. The update operations need to happen just as quickly as the read operations so that users don't put an item in their shopping carts when that item has just sold out. This will not only result in a large number of read operations, but will also require increased write operations for product catalog data. Be sure to determine the priorities for all the users of the database, not just the primary ones.

## Photos and videos

The photos and videos that are displayed on product pages have different requirements. They need fast retrieval times so that they are displayed on the site at the same time as product catalog data, but they don't need to be queried independently. Instead, you can rely on the results of the product query, and include the video ID or URL as a property on the product data. So, photos and videos need only be retrieved by their ID.

In addition, users will not make updates to existing photos or videos. They may, however, add new photos for product reviews. For example, a user might upload an image of themselves showing off their new shoes. As an employee, you also upload and delete product photos from your vendor, but that update doesn't need to happen as fast as your other product data updates.

In summary, photos and videos can be queried by ID to return the whole file, but creates and updates will be less frequent and are less of a priority.

## Business data

For business data, all the analysis is happening on historical data. No original data is updated based on the analysis, so business data is read-only. Users don't expect their complex analytics to run instantly, so having some latency in the results is okay. In addition, business data will be stored in multiple data sets, as users will have different access to write to each data set. However, business analysts will be able to read from all databases.

## Summary

When deciding what storage solution to use, think about how your data will be used. How often will your data be accessed? Is your data read-only? Does query time matter? The answers to these questions will help you decide on the best storage solution for your data.

# Group multiple operations in a transaction

Applications may need to group a series of data updates together, because a change to one piece of data needs to result in a change to another piece of data. Transactions enable you to group these updates so that if one event in a series of updates fails, the entire series can be rolled back, or undone.

For example, as an online retailer you might use a transaction for the placement of an order and payment verification. The grouping of the related events ensures that you don't reduce your inventory levels until an approved form of payment is received.

Here, you'll learn about transactions and whether they're required for your data.

## What is a transaction?

A transaction is a logical group of database operations that execute together.

Here's the question to ask yourself regarding whether you need to use transactions in your application: Will a change to one piece of data in your dataset impact another? If the answer is yes, then you'll need support for transactions in your database service.

Transactions are often defined by a set of four requirements, referred to as ACID guarantees. ACID stands for Atomicity, Consistency, Isolation, and Durability:

- **Atomicity** means a transaction must execute exactly once and must be atomic; either all of the work is done, or none of it is. Operations within a transaction usually share a common intent and are interdependent.
- **Consistency** ensures that the data is consistent both before and after the transaction.
- **Isolation** ensures that one transaction is not impacted by another transaction.
- **Durability** means that the changes made due to the transaction are permanently saved in the system. Committed data is saved by the system so that even in the event of a failure and system restart, the data is available in its correct state.

When a database offers ACID guarantees, these principles are applied to any transactions in a consistent manner.

## OLTP vs OLAP

Transactional databases are often called OLTP (**Online Transaction Processing**) systems. OLTP systems commonly support lots of users, have quick response times, and handle large volumes of data. They are also highly available (meaning they have very minimal downtime), and typically handle small or relatively simple transactions.

On the contrary, OLAP (**Online Analytical Processing**) systems commonly support fewer users, have longer response times, can be less available, and typically handle large and complex transactions.

The terms OLTP and OLAP aren't used as frequently as they used to be, but understanding them makes it easier to categorize the needs of your application.

Now that you're familiar with transactions, OLTP, and OLAP, let's walk through each of the data sets in the online retail scenario, and determine the need for transactions.

## Product catalog data

Product catalog data should be stored in a transactional database. When users place an order and the payment is verified, the inventory for the item should be updated. Likewise, if the customer's credit card is declined, the order should be rolled back, and the inventory should not be updated. These relationships all require transactions.

## Photos and videos

Photos and videos in a product catalog don't require transactional support. These files are changed only when an update is made or new files are added. Even though there is a relationship between the image and the actual product data, it's not transactional in nature.

## Business data

For the business data, because all of the data is historical and unchanging, transactional support is not required. The business analysts working with the data also have unique needs in that they often require working with aggregates in their queries, so that they can work with the totals of other smaller data points.

## Summary

Ensuring that your data is in the correct state is not always an easy task. Transactions can help by enforcing data integrity requirements on your data. If your data benefits from ACID principles, then choose a storage solution that supports transactions.

## Check your knowledge

1. Which type of transactional database system would work best for product data?

[] OLAP
[ x ] OLTP

2. Suppose the operations to update inventory and process payments are in the same transaction. A user is attempting to apply store credit for the full amount of an order, and submitted the exact same order (for the full amount) using their phone and laptop at the same time - so two identical orders are received. The database behind the scenes is an ACID-compliant database, what would happen?

[] Both orders would be processed and use the in-store credit.
[] One order would be processed and use the in-store credit, and the other order would update the remaining inventory for the items in the basket, but would not complete the order.
[ x ] One order would be processed and use the in-store credit, and the other order would not be processed.

# Choose a storage solution on Azure

# Summary
