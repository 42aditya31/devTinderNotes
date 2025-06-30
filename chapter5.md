
# Middleware and Error Handling in Express.js

Middleware and error handling form the backbone of any robust Express.js application. Understanding how requests are processed, how middleware functions chain, and how to handle errors gracefully is essential to writing maintainable and secure backends.

---

## 1. Route Handlers and Request Lifecycle

### 1.1 Route Handler Function

A **route handler** is a function that matches a specific path and method. It has access to:

* `req`: the incoming request object (URL, headers, body, etc.)
* `res`: the response object, used to send data back
* `next`: optional function to pass control to the next handler

A typical route handler:

```js
app.get('/user', (req, res) => {
  res.send('User data');
});
```

### 1.2 Handling No Response or Missing `next()`

If a handler neither sends a response nor calls `next()`, the request will remain pending and eventually time out. Every route handler must either produce a response or call `next()`.

### 1.3 Multiple Handlers for a Single Route

Express allows chaining multiple handlers:

```js
app.use('/user',
  (req, res, next) => {
    console.log('First handler');
    next();
  },
  (req, res) => {
    res.send('Second handler response');
  }
);
```

The first handler calls `next()` to pass execution to the second, which sends the response.

### 1.4 Sending Response and Calling `next()`

Calling `next()` after `res.send()` causes an error: **"Cannot set headers after they are sent."** This occurs because Express attempts to process more handlers after the response is already finalized. To avoid this:

* Either send a response without calling `next()`
* Or call `next()` without sending a response

### 1.5 Handlers Without `res.send()`

If none of the chained handlers send a response and all call `next()`, the request falls through to the default Express handler, usually returning a **404 Not Found**.

### 1.6 Multiple `res.send()` in a Chain

Only the first `res.send()` will work successfully. Subsequent calls will result in header-setting errors. Ensure exactly one response per request pathway.

### 1.7 Purpose of Multiple Handlers

Chaining handlers enables:

* Separation of concerns (logging, validation, authentication)
* Reusability of middleware functions
* Modular design and clear flow of execution

---

## 2. Middleware Functions

### 2.1 Definition and Role

**Middleware** is a function that processes a request before it reaches the final route handler:

```js
function logMiddleware(req, res, next) {
  console.log(req.method, req.url);
  next();
}
```

It can modify `req`, perform tasks, or terminate the request with a response or error.

### 2.2 Middleware vs Route Handler

| Middleware                     | Route Handler                        |
| ------------------------------ | ------------------------------------ |
| Executes before matching route | Executes when route and method match |
| Handles cross-cutting concerns | Implements specific business logic   |
| Must call `next()` to continue | Usually sends a response             |

---

## 3. Applying Middleware

### 3.1 Global Middleware

Runs on every request. Should be defined early:

```js
app.use(express.json());
app.use(logMiddleware);
```

### 3.2 Route-Specific Middleware

Assigned only to particular paths:

```js
app.get('/user',
  authMiddleware,
  dataValidation,
  (req, res) => { res.send('User profile'); }
);
```

### 3.3 Middleware Chaining

Multiple middlewares can be chained for a single route:

```js
app.get('/user',
  (req, res, next) => { /* handler 1 */ next(); },
  (req, res, next) => { /* handler 2 */ next(); },
  (req, res) => { res.send('Handler 3 response'); }
);
```

Execution flows in order; final handler completes the response.

---

## 4. HTTP Status Codes

Express uses HTTP status codes to communicate request results:

* **2xx** for success (e.g., 200 OK, 201 Created)
* **3xx** for redirection
* **4xx** for client errors (e.g., 400, 401, 404)
* **5xx** for server errors (e.g., 500 Internal Server Error)

Set status and response:

```js
res.status(401).send('Unauthorized request');
```

---

## 5. Authorization Example

Without middleware:

```js
app.get('/admin/alldata', (req, res) => {
  const token = req.body.token;
  if (token === 'xyz') {
    res.send('All userData');
  } else {
    res.status(401).send('Unauthorized request');
  }
});
```

Rewriting with middleware:

```js
function adminAuth(req, res, next) {
  const token = req.headers.authorization;
  if (token === 'xyz') {
    next();
  } else {
    res.status(401).send('Unauthorized request');
  }
}

app.use('/admin', express.json(), adminAuth);
app.get('/admin/alldata', (req, res) => {
  res.send('All userData');
});
```

Authorization is now centralized and reusable.

---

## 6. `app.use()` vs `app.all()`

* `app.use(path, middleware)`: Mounts middleware for every HTTP method at `path`.
* `app.all(path, handler)`: Invokes handler for all HTTP methods at `path`.

Use `app.use()` for middleware, `app.all()` for catch-all routes.

---

## 7. Error-Handling Middleware

### 7.1 Definition

Special middleware with four arguments:

```js
function errorHandler(err, req, res, next) {
  console.error(err.stack);
  res.status(500).send('Something went wrong!');
}
```

This should be added after all route handlers and regular middleware.

### 7.2 Forwarding Errors

Use `next(error)` in handlers:

```js
app.get('/error', (req, res, next) => {
  next(new Error('Simulated error'));
});
app.use(errorHandler);
```

Errors are passed to your final error handler.

### 7.3 Purpose of `next` in Error Handler

* If defined with `err`, Express recognizes it for error handling.
* `next` allows chaining to default handlers or additional logging layers.

### 7.4 Order Is Crucial

Place error-handling middleware *after* all routes and middlewares to ensure it catches all forwarded errors.

---

## 8. Summary and Best Practices

* Use middleware to separate concerns like logging, auth, validation.
* Chain handlers carefully to ensure exactly one response is sent.
* Define middleware and authorization logic outside routes for reusability.
* Use error-handling middleware to capture and respond to any errors.
* Maintain proper ordering: global middleware → route definitions → error middleware.

