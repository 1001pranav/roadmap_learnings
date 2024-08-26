# Mongo DB
#### **1. Introduction to MongoDB**

**MongoDB** is a NoSQL database that provides high performance, high availability, and easy scalability. Unlike traditional relational databases, MongoDB stores data in flexible, JSON-like documents, making it easier to manage and scale.

- **Document-Oriented**: Data is stored in documents (similar to JSON) with a dynamic schema.
- **Schema-less**: Collections in MongoDB do not enforce a schema, allowing for a flexible data structure.
- **Scalability**: MongoDB is designed to scale horizontally across many servers.
- **High Performance**: Indexing, replication, and sharding enhance MongoDB's performance.

#### **2. Basic Concepts**

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

#### **3. CRUD Operations**

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

#### **4. Indexing**

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

#### **5. Aggregation Framework**

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
  
#### **6. Schema Design**

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

#### **7. Transactions**

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

#### **8. Replication and Sharding**

1. **Replication**: 
   - MongoDB replicates data across multiple servers for redundancy and high availability.
   - Example: Setting up a replica set.
   
2. **Sharding**:
   - Distributes data across multiple servers to handle large datasets.
   - Example: Enabling sharding on a collection.

#### **9. Interview Questions**

1. **What is MongoDB? Explain its advantages over relational databases.**
2. **Describe the difference between embedded and referenced documents in MongoDB.**
3. **How do you handle transactions in MongoDB? Provide an example.**
4. **What is the purpose of indexing in MongoDB, and how do you create an index?**
5. **Explain the aggregation framework in MongoDB and its use cases.**
6. **What are the key differences between SQL and NoSQL databases?**
7. **Can you explain the concept of sharding in MongoDB?**
8. **How does MongoDB ensure high availability?**
9. **When would you use MongoDB over a traditional SQL database?**
10. **Describe a scenario where you would prefer a NoSQL database over an SQL database.**

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

This guide should give you a strong foundation in MongoDB, highlighting key concepts and practices widely used in the industry.
