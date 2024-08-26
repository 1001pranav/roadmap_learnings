# Mongo DB
## **1. Introduction to MongoDB**

**MongoDB** is a NoSQL database that provides high performance, high availability, and easy scalability. Unlike traditional relational databases, MongoDB stores data in flexible, JSON-like documents, making it easier to manage and scale.

- **Document-Oriented**: Data is stored in documents (similar to JSON) with a dynamic schema.
- **Schema-less**: Collections in MongoDB do not enforce a schema, allowing for a flexible data structure.
- **Scalability**: MongoDB is designed to scale horizontally across many servers.
- **High Performance**: Indexing, replication, and sharding enhance MongoDB's performance.

## **2. Basic Concepts**

1. **Database**
   - A database contains collections, which in turn contain documents.
   - Command to create/use a database:
     ```js
     use myDatabase;
     ```

2. **Collection**
   - A collection is a group of MongoDB documents, similar to tables in relational databases.
   - Example of creating a collection:
     ```js
     db.createCollection("myCollection");
     ```

3. **Document**
   - A document is a set of key-value pairs, analogous to rows in a table. Documents can have different fields.
   - Example of a document:
     ```json
     {
       "_id": ObjectId("507f1f77bcf86cd799439011"),
       "name": "John Doe",
       "age": 29,
       "email": "john.doe@example.com",
       "address": {
         "street": "123 Main St",
         "city": "New York",
         "zip": "10001"
       }
     }
     ```

## **3. CRUD Operations**

1. **Create**
   - Insert a single document:
     ```js
     db.myCollection.insertOne({
       name: "Jane Doe",
       age: 27,
       email: "jane.doe@example.com"
     });
     ```
   - Insert multiple documents:
     ```js
     db.myCollection.insertMany([
       { name: "Alice", age: 30 },
       { name: "Bob", age: 25 }
     ]);
     ```

2. **Read**
   - Find all documents:
     ```js
     db.myCollection.find();
     ```
   - Find documents with a filter:
     ```js
     db.myCollection.find({ age: { $gt: 25 } });
     ```
   - Find a single document:
     ```js
     db.myCollection.findOne({ name: "Alice" });
     ```

3. **Update**
   - Update a single document:
     ```js
     db.myCollection.updateOne(
       { name: "Alice" },
       { $set: { age: 31 } }
     );
     ```
   - Update multiple documents:
     ```js
     db.myCollection.updateMany(
       { age: { $lt: 30 } },
       { $set: { status: "Under 30" } }
     );
     ```

4. **Delete**
   - Delete a single document:
     ```js
     db.myCollection.deleteOne({ name: "Bob" });
     ```
   - Delete multiple documents:
     ```js
     db.myCollection.deleteMany({ age: { $lt: 30 } });
     ```

## **4. Indexing**

- **Indexes** support the efficient execution of queries in MongoDB.
- Create an index on a field:
  ```js
  db.myCollection.createIndex({ name: 1 });
  ```
- Compound Index:
  ```js
  db.myCollection.createIndex({ name: 1, age: -1 });
  ```
- **Widely Used**: Indexing is critical for optimizing query performance, especially in large datasets.

## **5. Aggregation Framework**

- Aggregation operations process data records and return computed results.
- Example of a basic aggregation:
  ```js
  db.orders.aggregate([
    { $match: { status: "A" } },
    { $group: { _id: "$cust_id", total: { $sum: "$amount" } } },
    { $sort: { total: -1 } }
  ]);
  ```
- **Complex Example**: Using multiple stages (match, group, sort) to calculate total sales by customer and sort them in descending order.
  
## **6. Schema Design**

- **Embedding vs. Referencing**:
  - Embedding: Storing related data in a single document.
  - Referencing: Storing data in separate documents and using references.

  **Example**:
  - Embedding:
    ```json
    {
      "name": "John Doe",
      "orders": [
        { "product": "Laptop", "price": 1200 },
        { "product": "Phone", "price": 800 }
      ]
    }
    ```
  - Referencing:
    ```json
    {
      "name": "John Doe",
      "orders": [ObjectId("507f1f77bcf86cd799439012"), ObjectId("507f1f77bcf86cd799439013")]
    }
    ```

## **7. Transactions**

- MongoDB supports multi-document ACID transactions.
- Example of a transaction:
  ```js
  const session = db.getMongo().startSession();
  session.startTransaction();
  try {
    db.myCollection.updateOne({ _id: 1 }, { $set: { qty: 5 } }, { session });
    db.myOtherCollection.updateOne({ _id: 1 }, { $inc: { count: -1 } }, { session });
    session.commitTransaction();
  } catch (error) {
    session.abortTransaction();
  } finally {
    session.endSession();
  }
  ```
- **Widely Used**: Transactions are crucial when maintaining consistency across multiple documents.

## **8. Replication and Sharding**

1. **Replication**: 
   - MongoDB replicates data across multiple servers for redundancy and high availability.
   - Example: Setting up a replica set.
   
2. **Sharding**:
   - Distributes data across multiple servers to handle large datasets.
   - Example: Enabling sharding on a collection.

## **9. Interview Questions**

### What is MongoDB? Explain its advantages over relational databases.

**MongoDB** is a NoSQL database that stores data in a flexible, JSON-like format. Unlike traditional relational databases, MongoDB does not require a fixed schema, allowing you to store complex data structures and perform quick operations.

**Advantages over relational databases**:
1. **Flexible Schema**: MongoDB allows for a flexible, schema-less design, making it easier to evolve your data structure over time without downtime.
2. **Horizontal Scalability**: MongoDB can scale horizontally by sharding data across multiple servers, making it ideal for applications with large-scale data and high traffic.
3. **High Performance**: It is optimized for read and write operations, providing faster data retrieval and throughput compared to traditional databases.
4. **Document-Oriented**: MongoDB stores data as documents, which can easily represent complex hierarchical relationships without the need for JOIN operations.
5. **Ease of Use with JSON**: Since MongoDB uses BSON (Binary JSON) for storage, it integrates smoothly with JavaScript/Node.js applications, which commonly use JSON for data interchange.

### Describe the difference between embedded and referenced documents in MongoDB.

- **Embedded Documents**: These are documents stored within other documents. This approach is beneficial for storing related data together, reducing the need for multiple queries and providing faster read performance.

  *Example*:
  ```javascript
  {
    _id: 1,
    name: "John Doe",
    address: {
      street: "123 Main St",
      city: "Springfield",
      zip: "12345"
    }
  }
  ```

- **Referenced Documents**: These involve storing related data in separate documents and linking them through references (usually via the `_id` field). This is useful for normalizing data and reducing redundancy.

  *Example*:
  ```javascript
  {
    _id: 1,
    name: "John Doe",
    address_id: 101
  }
  ```
  ```javascript
  {
    _id: 101,
    street: "123 Main St",
    city: "Springfield",
    zip: "12345"
  }
  ```

### How do you handle transactions in MongoDB? Provide an example.

In MongoDB, transactions allow you to execute multiple operations atomically, ensuring that either all operations succeed, or none do.

**Example**:
```javascript
const { MongoClient } = require('mongodb');
const uri = "mongodb://localhost:27017";
const client = new MongoClient(uri);

async function runTransaction() {
    const session = client.startSession();
    const db = client.db("exampleDB");

    try {
        session.startTransaction();

        await db.collection("accounts").updateOne(
            { _id: 1 },
            { $inc: { balance: -100 } },
            { session }
        );

        await db.collection("accounts").updateOne(
            { _id: 2 },
            { $inc: { balance: 100 } },
            { session }
        );

        await session.commitTransaction();
        console.log("Transaction committed.");
    } catch (error) {
        await session.abortTransaction();
        console.error("Transaction aborted due to an error: ", error);
    } finally {
        session.endSession();
    }
}

runTransaction().catch(console.error);
```
This code demonstrates a transaction that transfers funds between two accounts atomically.

### What is the purpose of indexing in MongoDB, and how do you create an index?

**Purpose of Indexing**:
Indexes in MongoDB are used to speed up the search and retrieval of documents from a collection. They work by creating a data structure that MongoDB can quickly search through, avoiding the need to scan every document in a collection.

**Creating an Index**:
```javascript
const { MongoClient } = require('mongodb');
const uri = "mongodb://localhost:27017";
const client = new MongoClient(uri);

async function createIndex() {
    const db = client.db("exampleDB");
    await db.collection("users").createIndex({ name: 1 });
    console.log("Index created on 'name' field.");
}

createIndex().catch(console.error);
```
This code creates an ascending index on the `name` field in the `users` collection.

### Explain the aggregation framework in MongoDB and its use cases.

The **Aggregation Framework** in MongoDB allows you to perform complex data processing and transformation operations directly within the database. It’s similar to SQL's `GROUP BY` and `JOIN` operations but more powerful and flexible.

**Use Cases**:
- **Data Aggregation**: Summarizing large datasets, such as calculating totals, averages, and other statistics.
- **Data Transformation**: Modifying the shape of your data, such as flattening nested documents or grouping results.
- **Complex Querying**: Performing multi-step queries, including filtering, sorting, and projecting data.

**Example**:
```javascript
const { MongoClient } = require('mongodb');
const uri = "mongodb://localhost:27017";
const client = new MongoClient(uri);

async function aggregateData() {
    const db = client.db("exampleDB");
    const pipeline = [
        { $match: { status: "active" } },
        { $group: { _id: "$category", totalSales: { $sum: "$sales" } } },
        { $sort: { totalSales: -1 } }
    ];

    const result = await db.collection("orders").aggregate(pipeline).toArray();
    console.log(result);
}

aggregateData().catch(console.error);
```
This aggregation pipeline filters active orders, groups them by category, and sorts the categories by total sales in descending order.

### What are the key differences between SQL and NoSQL databases?

- **Data Model**:
  - **SQL**: Structured, table-based with predefined schemas.
  - **NoSQL**: Flexible, document-based, key-value, wide-column, or graph-based with dynamic schemas.

- **Scalability**:
  - **SQL**: Typically scales vertically (increasing resources of a single server).
  - **NoSQL**: Scales horizontally (adding more servers to handle larger data loads).

- **Transactions**:
  - **SQL**: Supports ACID transactions with strong consistency.
  - **NoSQL**: Supports transactions but often emphasizes availability and partition tolerance, which might lead to eventual consistency.

- **Data Relationships**:
  - **SQL**: Uses JOIN operations to manage relationships between tables.
  - **NoSQL**: Manages relationships through embedded documents or references, often avoiding JOINs.

- **Use Cases**:
  - **SQL**: Ideal for applications with complex queries, structured data, and strong transactional requirements.
  - **NoSQL**: Suitable for applications with unstructured or semi-structured data, requiring high scalability and flexibility.

### Can you explain the concept of sharding in MongoDB?

**Sharding** is a method of distributing data across multiple servers, or shards, to ensure the database can scale horizontally. This is crucial for handling large volumes of data and high traffic.

**Key Concepts**:
- **Shard**: Each shard is a MongoDB instance that stores a subset of the total data.
- **Shard Key**: The field used to distribute data across shards.
- **Config Servers**: Store metadata about the sharded cluster.
- **Mongos**: The query router that directs operations to the correct shard based on the shard key.

**Use Case**:
Sharding is used when a single MongoDB instance cannot handle the volume of data or load, allowing the data to be partitioned and distributed across multiple servers.

### How does MongoDB ensure high availability?

MongoDB ensures high availability through **Replica Sets**. A replica set is a group of MongoDB servers that maintain the same data, providing redundancy and failover support.

**Key Components**:
- **Primary**: The server that handles all write operations.
- **Secondary**: Servers that replicate data from the primary and can be promoted to primary if the current primary fails.
- **Arbiter**: A server that does not store data but helps in electing a new primary if the primary fails.

**High Availability**:
- If the primary node fails, a new primary is elected from the secondaries, ensuring continuous availability.
- Clients can read from secondaries, ensuring read availability even during failover scenarios.

### When would you use MongoDB over a traditional SQL database?

You would use MongoDB over a traditional SQL database when:
- **Schema Flexibility**: You need a flexible schema to accommodate evolving data structures without downtime.
- **High Scalability**: Your application requires horizontal scalability to handle large datasets and high traffic.
- **Document-Oriented Data**: The data is naturally represented as documents, with potentially complex, nested structures.
- **Rapid Development**: The project requires quick iterations and the ability to store and retrieve data without the overhead of schema migrations.
- **Large, Unstructured Data**: You are dealing with unstructured or semi-structured data that doesn't fit neatly into tables and columns.

### Describe a scenario where you would prefer a NoSQL database over an SQL database.

A scenario where you would prefer a NoSQL database over an SQL database could be:
- **Real-Time Data Processing**: An application that ingests and processes large volumes of unstructured data in real-time, such as a social media platform or IoT system.
- **Content Management**: A content management system (CMS) that needs to store and retrieve varying types of content, such as blog posts, videos, and user comments, where the data structure can vary significantly.
- **Big Data Applications**: An application that requires handling and analyzing large datasets that are distributed across multiple servers, such as a big data analytics platform.
- **Global Distribution**: An application that needs to distribute data across multiple geographic locations,
### **Important Concepts**
- **Indexing**: Essential for query optimization.
- **Aggregation**: Powerful tool for data analysis.
- **Transactions**: Critical for maintaining data consistency.
- **Schema Design**: Deciding between embedding and referencing impacts performance and scalability.
  
### **Complex Examples**
- **Aggregation Pipeline with Lookup**: 
  ```js
  db.orders.aggregate([
    {
      $lookup: {
        from: "customers",
        localField: "cust_id",
        foreignField: "_id",
        as: "customerDetails"
      }
    },
    { $unwind: "$customerDetails" },
    { $match: { "customerDetails.status": "Active" } },
    { $group: { _id: "$cust_id", total: { $sum: "$amount" } } },
    { $sort: { total: -1 } }
  ]);
  ```
