
# Database, Schema & Models with Mongoose and MongoDB

---

## 1. Database: Foundation of Persistent Storage

A **database** is a structured system where collections of data are stored and managed. In MongoDB, a database contains multiple **collections**, which in turn contain **documents** (JSON-like records). Databases allow for durable and organized storage of application data.

### Characteristics

* Identified by a name (e.g., `devtinder`)
* Can contain multiple collections (e.g., `users`, `connectionRequests`)
* Enables persistent storage beyond application runtime

---

## 2. Schema: Blueprint for Data Structure

A **schema** defines the shape and constraints of documents within a collection. In Mongoose, schemas cover:

* Field definitions and data types (`String`, `Number`, `Date`, etc.)
* Validation rules (e.g., `required`, `min`, `match`)
* Default values, custom getters and setters
* Virtual fields and middleware hooks

Schemas ensure consistency and enforce structure in otherwise schema-free MongoDB.

---

## 3. Model: Interface to Database Collections

A **model** in Mongoose is a compiled representation of a schema, linked to a specific MongoDB collection. Models serve as constructors for creating and reading documents. They provide an API to perform CRUD operations.

Example:

```javascript
const UserModel = mongoose.model("User", userSchema);
```

Here, `UserModel` is associated with the `users` collection.

---

## 4. Schema vs Model

| Aspect        | Schema                            | Model                                      |
| ------------- | --------------------------------- | ------------------------------------------ |
| Definition    | Data structure, types, validation | Interface to collection built from schema  |
| Usage         | Defines rules and shape of data   | Performs operations (find, create, update) |
| Instantiation | Used for building models          | Used to create documents                   |

---

## 5. Mongoose: ORM for MongoDB

Mongoose is an Object Data Modeling (ODM) library providing:

* Schemas and models
* Data validation
* Lifecycle middleware (e.g., `pre`, `post` hooks)
* Built-in query and aggregation helpers

It simplifies MongoDB operations by layering structured JavaScript objects over untyped documents.

---

## 6. Differences: Mongoose vs MongoDB

* **MongoDB** is the database engine; operations on collections and documents happen at the driver level.
* **Mongoose** is a library that wraps MongoDB operations with schema enforcement, object mapping, validation, and middleware, making development more structured and predictable.

---

## 7. Integrating Mongoose into the Project

### Installation:

```bash
npm install mongoose
```

### Connection Pattern:

```javascript
// config/database.js
const mongoose = require("mongoose");

const connectDatabase = async () => {
  await mongoose.connect(MongoDBURL);
};

module.exports = connectDatabase;
```

### App Initialization:

```javascript
// app.js
const express = require("express");
const connectDatabase = require("./config/database");

const app = express();

connectDatabase()
  .then(() => {
    console.log("Database connected successfully");
    app.listen(3000, () => {
      console.log("Server is running on port 3000");
    });
  })
  .catch(err => {
    console.error("Database connection failed", err);
  });
```

This pattern ensures the Express server starts only after a successful database connection.

---

## 8. Handling Connection Errors

* Catch exceptions thrown during `mongoose.connect()`.
* Log details and avoid starting the server without a valid database.
* Use process exit or retry logic after persistent failures.

---

## 9. Defining a Mongoose Schema

Schema creation:

```javascript
const mongoose = require("mongoose");

const userSchema = new mongoose.Schema({
  firstName: { type: String, required: true },
  lastName: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  password: { type: String, required: true },
  age: { type: Number, min: 0 },
  gender: { type: String, enum: ["male", "female", "other"] }
}, {
  timestamps: true
});
```

This defines a consistent shape, validation rules, defaults, and timestamps for created/updated fields.

---

## 10. Defining a Mongoose Model

Create and export a model:

```javascript
const UserModel = mongoose.model("User", userSchema);
module.exports = UserModel;
```

The model provides CRUD methods like:

* `UserModel.create(data)`
* `UserModel.find(query)`
* `UserModel.findByIdAndUpdate(id, update)`
* `UserModel.deleteOne(query)`

---

## 11. End-to-End Process Overview

1. **Design schema** – define structure, types, validation.
2. **Compile model** – bind schema to collection.
3. **Instantiate document** – `const user = new UserModel(data);`
4. **Save document** – `await user.save();`
5. **Query data** – `await UserModel.find({ /* filters */ });`

---

## 12. Automatic `_id` and `__v` Fields

* `_id` is the unique identifier automatically generated for each document.
* `__v` tracks document version, especially during updates. Both fields are included even if not defined in schema.

To disable `__v`:

```javascript
new mongoose.Schema({...}, { versionKey: false });
```

Defining `_id` explicitly:

```javascript
const userSchema = new Schema({
  _id: Schema.Types.ObjectId,
  ...
});
```

---

## 13. Understanding Database, Collections, Documents

* **Database**: Contains multiple collections.
* **Collection**: Groups documents with the same schema.
* **Document**: Individual record adhering to a schema, stored in a collection.

Example:

* `devtinder` database

  * `users` collection → collections of user documents
  * `connectionRequests` collection → collections of request documents

---

## 14. Purpose of `_id` and `__v`

* `_id` serves as a unique primary key for each document.
* `__v` tracks versioning to facilitate safe concurrent updates.
* Both support MongoDB’s internal mechanisms and Mongoose’s document tracking features.

---

## 15. Schema and Model Recap

A well-defined schema ensures data consistency and supports validation, while models provide a streamlined interface for operations. Together with automatic fields and proper infrastructure, they enable structured and reliable data interaction in your application.

