
# **1. `.gitignore` File Explained**

* A `.gitignore` file tells Git which files or directories to skip when staging commits.
* Common items to ignore:

  * Build artifacts (`/dist`, `/build`)
  * Dependency folders (`node_modules/`)
  * Environment files containing secrets (`.env`)
  * Generated logs (`*.log`)
* Example `.gitignore`:

  ```
  node_modules/
  .env
  *.log
  dist/
  ```
* Benefits:

  * Keeps repository clean
  * Prevents accidental commit of secrets
  * Improves repository performance

---

# **2. Express.js: Routing and Request Handlers**

## 2.1 Why `app.use("/", ...)` Matches All Routes

* `app.use(path, handler)` without an HTTP method means “match any request method”.
* `"/"` matches all incoming request URL paths.
* As a result, this handler runs before any other route handlers:

  ```js
  app.use("/", (req, res) => {
    res.send("Hello World");
  });
  ```
* To avoid this, define specific routes first, then general handlers.

## 2.2 Route Order Matters

* Express checks routes in the order they are defined.
* A general route (like `"/"`) defined before specific ones will catch all requests:

  ```js
  app.use("/", ...)          // catches all routes
  app.get("/test", ...)      // will be unreachable
  ```
* Define specific routes first, then more general ones, then fallbacks.

---

## 2.3 Detailed Overview of Express Routes

### 1. Route Definition

* Use methods like `app.get()`, `app.post()`, etc.

  ```js
  app.get('/users', (req, res) => {
    res.send('List of users');
  });
  ```

### 2. Route Parameters

* Define dynamic paths with `:paramName`

  ```js
  app.get('/users/:id', (req, res) => {
    const id = req.params.id;
    res.send(`User ID: ${id}`);
  });
  ```

### 3. Query Parameters

* Pass optional values via URL (`?key=value`)
* Access via `req.query`

  ```js
  app.get('/search', (req, res) => {
    const q = req.query.q;
    res.send(`Search for: ${q}`);
  });
  ```

### 4. Middleware

* Middleware functions run before reaching route handlers.

  ```js
  app.use((req, res, next) => {
    console.log(req.url);
    next();
  });
  ```

### 5. Route Groups with Router

* Create modular routes using `express.Router`

  ```js
  const router = express.Router();
  router.get('/users', ...);
  app.use('/api', router);
  ```

### 6. Error Handling

* Defined after all other routes

  ```js
  app.use((err, req, res, next) => {
    console.error(err);
    res.status(500).send('Error occurred');
  });
  ```

### 7. Route Extensions / Chaining

* Handle multiple methods on the same route

  ```js
  app.route('/users/:id')
    .get(...)
    .put(...)
    .delete(...);
  ```

### 8. Serving Static Files

* Use `express.static` middleware

  ```js
  app.use('/static', express.static('public'));
  ```

### 9. Wildcards

* Use `*` to match any path

  ```js
  app.get('*', (req, res) => {
    res.send('Catch all route');
  });
  ```

### 10. Route Aliases / Redirects

* Provide alternate routes

  ```js
  app.get('/all-users', (req, res) => {
    res.redirect('/users');
  });
  ```

### 11. API Versioning

* Support multiple API versions

  ```js
  app.get('/v1/users', ...);
  app.get('/v2/users', ...);
  ```

### 12. Documentation (Swagger / JSDoc)

/\*\*

* @route GET /users
* @returns {Array} 200 - user objects
  \*/
  app.get('/users', (req, res) => { ... });

### 13. Route Testing

* Use test libraries like Supertest

  ```js
  request(app).get('/users').expect(200, done);
  ```

### 14. Security (Auth/Permission)

* Protect routes with custom middleware

  ```js
  const auth = (req, res, next) => { /* check token */ };
  app.get('/protected', auth, (req, res) => { ... });
  ```

### 15. Caching

* Example cache middleware

  ```js
  app.get('/cached', cache, (req, res) => { res.send(data); });
  ```

### 16. Rate Limiting

```js
const limiter = rateLimit({ windowMs:900000, max:100 });
app.use('/api', limiter);
```

---

## 3. Understanding HTTP Methods in Simple Terms

### GET

* Retrieves data
* Safe (does not modify server state)
* Example: Fetch user profile

### POST

* Creates new data
* Example: Register new user

### PUT

* Replaces entire resource
* Example: Replace full user profile

### PATCH

* Updates only specified fields
* Example: Update only `bio` or `location`

### DELETE

* Removes resource
* Example: Delete user account

---

## 4. Testing Routing with Example Code

```js
const express = require('express');
const app = express();

app.get("/user", (req, res) => {
  res.json({ firstName: "Aditya", lastName: "Sharma" });
});

app.post("/user", (req, res) => {
  res.send("Data saved successfully");
});

app.use((req, res) => {
  res.status(404).send("Default response for unmatched routes");
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

### Expected Behavior:

* `GET /user` → returns JSON user object
* `POST /user` → returns success message
* Any other path or method → returns default 404 response

---

## 5. Special Route Patterns and Their Behavior

* `+` in route: one or more characters wildcard
* `?`: zero or one occurrence, optional segment
* `*`: matches any sequence of characters
* `()`: groups parts of route
* `/a/`: the path literal `/a/`
* `$`: end-of-string anchor
* `/user/:userId`: dynamic segment for userId

Express uses path-to-regexp syntax: patterns like `+`, `?`, `*`, `?`, etc.

Example:

```js
app.get('/file-*.txt', ..., )
app.get('/user/:id(\\d+)', ...)
```

---

## 6. Reading Query vs URL Parameters

* `req.params` extracts url segments declared in route path:

  ```js
  GET /user/123 → req.params = { userId: "123" }
  ```

* `req.query` reads values after `?`:

  ```js
  GET /search?q=node → req.query = { q: "node" }
  ```

Use `params` for required identifiers in URL; use `query` for optional parameters.

---

## 7. Using Regular Expressions in Routes

Routes can use regex to match patterns:

```js
app.get(/^\/file\/(\d+)\.txt$/, (req, res) => {
  const id = req.params[0];
  res.send(`File ID: ${id}`);
});
```

Use this for precise control over route matching.

---

## Summary

* `.gitignore` ensures repository cleanliness.
* Express routing requires deliberate ordering.
* Routes support path params, query params, middleware, and regex.
* HTTP methods (`GET`, `POST`, `PUT`, `PATCH`, `DELETE`) match intended CRUD operations.
* Tools like Postman can be used to test routes manually and debug.
* This layer sets the foundation for building APIs (signup, login, feed, connections).

