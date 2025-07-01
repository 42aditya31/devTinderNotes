
# APIs and CRUD Operations in MongoDB with Express & Mongoose

---

## 1. API Overview

An **API (Application Programming Interface)** in backend development provides a contract for how clients interact with server functionality over HTTP. In a RESTful architecture, the API defines:

* **Endpoints**: Paths like `/signup`, `/user`, `/feed`
* **Methods**: HTTP verbs (GET, POST, PATCH, DELETE)
* **Requests**: Structured input in URL, headers, body
* **Responses**: HTTP status code + payload in JSON
* **Data Flow**: Request parsed → business logic → database interaction → response

**Purposes** of the API include:

* Exposing application features in a modular way
* Enabling frontend-backend decoupling
* Abstracting data storage and logic from clients
* Standardizing communication and error handling

A **Web API** or **REST API** is a subtype accessed via HTTP(S). While all REST APIs are Web APIs, not all Web APIs are RESTful. **GraphQL APIs** offer an alternative approach—clients specify exactly what data they want in a single flexible query.

---

## 2. JavaScript vs JSON

* **JavaScript** is a scripting language capable of logic, types, and functions
* **JSON (JavaScript Object Notation)** is a data serialization format—structured but static
* Internally, Express uses `express.json()` middleware to convert JSON strings in HTTP bodies into JavaScript objects in `req.body`. Without it, `req.body` remains undefined.

JSON is ideal for APIs due to its universal support and structure.

---

## 3. CRUD Operations: Detailed Breakdown

### A. Create (POST)

```javascript
app.post("/signup", express.json(), async (req, res) => {
  const userObject = req.body;
  const user = new User(userObject);
  try {
    await user.save();
    res.status(201).send("User created successfully");
  } catch (err) {
    console.error(err);
    res.status(500).send("Internal Server Error");
  }
});
```

* `express.json()` parses the request body
* `new User()` constructs a document against the schema
* `.save()` validates and writes to the database
* Return `201 Created` if successful; `500` otherwise

### B. Read (GET)

#### 1. Find multiple users by query:

```javascript
app.get("/user", express.json(), async (req, res) => {
  const { email } = req.body;
  try {
    const users = await User.find({ email });
    return users.length ? res.send(users) : res.status(404).send("User not found");
  } catch (err) {
    console.error(err);
    res.status(500).send("Internal Server Error");
  }
});
```

#### 2. Retrieve all users for a feed:

```javascript
app.get("/feed", async (req, res) => {
  try {
    const users = await User.find({});
    res.send(users.length ? users : []);
  } catch (err) {
    console.error(err);
    res.status(500).send("Internal Server Error");
  }
});
```

### C. Delete (DELETE)

```javascript
app.delete("/user", express.json(), async (req, res) => {
  const { userId } = req.body;
  try {
    await User.findByIdAndDelete(userId);
    res.send("User deleted successfully");
  } catch (err) {
    console.error(err);
    res.status(500).send("Internal Server Error");
  }
});
```

* Accepts `userId`
* Deletes the corresponding document
* Return `200 OK` or `500 Internal Error`

### D. Update (PATCH / PUT)

```javascript
app.patch("/user", express.json(), async (req, res) => {
  const { userId, ...data } = req.body;
  try {
    await User.findByIdAndUpdate(userId, data, {
      new: true,
      runValidators: true,
      omitUndefined: true
    });
    res.send("User updated successfully");
  } catch (err) {
    console.error(err);
    res.status(500).send("Internal Server Error");
  }
});
```

Key differences:

* **PATCH**: partial update (useful for single-field modifications)
* **PUT**: full replacement (requires full payload)
* Common options:

  * `new: true` – return updated document
  * `runValidators: true` – enforce schema rules on update
  * `omitUndefined: true` – skip fields not provided
  * `upsert: true` – create document if not existing
  * `select: 'field1 field2'` – return selective fields only

---

## 4. `find()` vs `findOne()`

* **`find(query)`** returns an array of all matched documents
* **`findOne(query)`** returns the first matched document or `null`
* **`findById(id)`** shorthand for `_id` query
* Method selection depends on whether you expect a single resource or a list

---

## 5. Advanced Query Options and Modifiers

* `limit(n)` – restrict number of documents
* `skip(n)` – pagination offset
* `sort({ field: 1 or -1 })` – ascending or descending order
* `select('fieldA fieldB')` – return a subset of fields
* `populate('referenceField')` – auto-join related collections

Example:

```javascript
User.find({ age: { $gte: 18 } })
  .limit(10)
  .sort({ createdAt: -1 })
  .select('firstName lastName email');
```

---

## 6. Best Practices for CRUD Endpoints

1. Validate input using a validation library (Joi, express-validator)
2. Use explicit HTTP statuses (201, 200, 404, 500)
3. Log full error details server-side; return minimal client errors
4. Ensure exactly one response per request path
5. Only modify necessary fields with PATCH and options
6. Avoid side effects in GET requests — treat them as read-only
7. Return structured JSON with data and optional metadata

---

## 7. Error Handling Strategies

* Wrap all async operations in `try/catch`
* Log full error details (`err.message`, `err.stack`) on the server
* Return sanitized error messages to clients
* Consider an error-handling middleware to normalize responses

---

## 8. Boolean flags and Options in `findOneAndUpdate()` / `findByIdAndUpdate()`

| Option          | Description                                      |
| --------------- | ------------------------------------------------ |
| `new`           | Return the updated document                      |
| `runValidators` | Enforce schema validation                        |
| `omitUndefined` | Ignore undefined fields in updates               |
| `upsert`        | Create document if it does not exist             |
| `select`        | Return specified fields only                     |
| `lean`          | Return plain JS objects instead of Mongoose docs |

Use appropriate options for consistent and safe updates.

---

## 9. HTTP Method Clarification: PUT vs PATCH

* **PUT** = full replacement; idempotent
* **PATCH** = partial update; may not be idempotent if applying incremental changes

Example:

```bash
PATCH /user
{ "bio": "New bio" }
```

---

## Summary

This guide lays out every layer of CRUD implementation using Express and Mongoose—API design, schema-to-model mapping, query strategies, method behaviors, and production-ready practices. You now have a complete reference to draft reliable, scalable, and well-structured backend CRUD functionality.

