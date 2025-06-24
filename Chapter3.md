
# Node.js Project Setup with Express.js – Backend Initialization Notes

---

## 1. Why We Start with Backend

In a full-stack application like **DevTinder**, the backend is responsible for all critical business operations. Before building any frontend, we must have a solid backend foundation. The backend handles:

* Data storage and retrieval (MongoDB)
* User authentication and authorization (login, signup)
* API endpoints (create profile, send connection requests, etc.)
* Input validation and security checks
* Communication with the frontend via HTTP APIs
* Business logic (checking matches, request status updates)

This makes the backend the **core engine** of your application. Starting with backend also allows frontend developers to use mock data or real APIs early in development.

---

## 2. Initialize the Node Project Using `npm`

### Command to Run:

```bash
npm init -y
```

### What This Does:

* Initializes a new Node.js project in your current directory
* Creates a file called `package.json`
* The `-y` flag accepts all default options automatically, so you don’t need to manually type project name, version, etc.

### What Is `package.json`?

`package.json` is the **metadata file** for your Node.js project. It contains information like:

* Project name and version
* Main entry file (e.g., `app.js`)
* Dependencies (like Express)
* Scripts (like `start`, `dev`, `test`)
* Optional configurations (author, license, repository link, etc.)

This file allows you and other developers to install exact dependencies and run predefined commands across machines.

---

## 3. Installing Express.js

### Why Express?

Express.js is a **web application framework** for Node.js. It simplifies:

* Creating HTTP servers
* Defining routes (GET, POST, PUT, DELETE)
* Handling incoming requests and outgoing responses
* Using middleware functions for tasks like parsing JSON, authentication, logging, etc.
* Structuring scalable applications

### Install Command:

```bash
npm install express
```

This adds `express` as a dependency and updates your `package.json` and `package-lock.json` files.

---

## 4. Understanding `package.json` vs `package-lock.json`

| File                | Purpose                                                                                       |
| ------------------- | --------------------------------------------------------------------------------------------- |
| `package.json`      | Manually written and version-controlled file that declares dependencies and scripts           |
| `package-lock.json` | Auto-generated file that locks down **exact versions** of every dependency and sub-dependency |

This helps teams avoid version conflicts and ensures **predictable installs** across different machines and environments.

---

## 5. Semantic Versioning (`semver`) in Dependencies

Each dependency version follows the format: `MAJOR.MINOR.PATCH`

Example:

```
"express": "^4.17.1"
```

### Breakdown:

* **Major version**: Breaking changes (e.g., 4 → 5 might change the entire API)
* **Minor version**: New features that don’t break existing code (e.g., 4.17 → 4.18)
* **Patch version**: Bug fixes or minor improvements (e.g., 4.17.1 → 4.17.2)

### Version Modifiers:

| Symbol | Meaning                                                      |
| ------ | ------------------------------------------------------------ |
| `^`    | Allows updates to any minor or patch version (but not major) |
| `~`    | Allows updates to any patch version (but not minor or major) |
| None   | Locks the version exactly                                    |

---

## 6. NPM Install Flags

### `--save-dev`

```bash
npm install nodemon --save-dev
```

* Adds the package to `devDependencies`
* Used for development-only tools like linters, test runners, or file watchers
* These dependencies are **not installed** in production environments

### `--save`

```bash
npm install express --save
```

* Adds the package to `dependencies`
* These are essential for the app to run in production
* Since npm v5, this is the default behavior, so you can omit `--save`

### `--global` or `-g`

```bash
npm install -g nodemon
```

* Installs the package system-wide
* Useful for tools you use in the terminal across projects (e.g., `nodemon`, `eslint`, `create-react-app`)

### `--no-save`

```bash
npm install express --no-save
```

* Installs a package **without saving** it to `package.json`
* Useful when you want to test a package without committing to using it long-term

### `--force`

```bash
npm install express --force
```

* Forces npm to install a package even if it causes conflicts
* Use only if you're certain you understand the risk

### `--legacy-peer-deps`

```bash
npm install express --legacy-peer-deps
```

* Ignores peer dependency conflicts
* Useful when installing older packages that aren’t compatible with newer ecosystems

---

## 7. Creating the Main Server File: `app.js`

This is the **entry point** of your backend application.

### Code Example:

```js
// app.js

const express = require('express');
const app = express();

// A default route handler
app.use((req, res) => {
    res.end("Hello from the server side!");
});

// Start the server
app.listen(3000, () => {
    console.log("Server is running on port 3000");
});
```

### Explanation:

* `express()` creates an instance of the Express app.
* `app.use()` adds a middleware function that handles all requests.
* `res.end()` sends a simple response and ends the request.
* `app.listen()` binds the server to a port and starts listening.

---

## 8. What is a Request Handler Function?

In Express, a **request handler function** is what executes when a request is received.

### Syntax:

```js
(req, res) => {
    // Do something with req and res
}
```

* `req`: The request object (contains URL, headers, params, body)
* `res`: The response object (used to send data back to the client)

### Example:

```js
app.use((req, res) => {
    res.status(200).send("Welcome to DevTinder API");
});
```

This function handles **all routes** because it is used with `app.use()` without a specific path.

---

## 9. Routing in Express.js

### What is Routing?

Routing is the mechanism that **maps an HTTP request to a specific function**.

Express provides HTTP method functions such as:

* `app.get()` – handles GET requests
* `app.post()` – handles POST requests
* `app.put()` – handles full updates
* `app.patch()` – handles partial updates
* `app.delete()` – handles deletions

### Example:

```js
app.get('/test', (req, res) => {
    res.send("This is a GET request to /test");
});
```

In this example, only a GET request to the `/test` route will match this handler.

### Advanced Tip:

Instead of putting all routes in `app.js`, we will later:

* Use **`express.Router()`** to modularize routes
* Create **route folders** for user, connection, etc.
* Connect those routers to `app.js`

---

## 10. Summary of Concepts Covered

| Topic                     | Key Insights                                   |
| ------------------------- | ---------------------------------------------- |
| npm init                  | Initializes project and creates `package.json` |
| package.json vs lock file | Lock ensures consistent dependency versions    |
| Semantic versioning       | Understands breaking vs minor vs patch updates |
| Version modifiers (^, \~) | Controls upgrade scope                         |
| Install flags             | Fine-tune which dependencies go where          |
| Installing express        | Adds the server framework                      |
| Entry point (`app.js`)    | Sets up the server to handle requests          |
| Request handler           | Function that processes incoming requests      |
| Express routing           | Maps URL + HTTP method to specific handlers    |

---

## Next Steps

We are now ready to:

1. Modularize our routes using Express Router
2. Set up MongoDB with Mongoose
3. Design the `/signup`, `/login`, and `/profile` APIs
4. Implement connection request logic and store it in the database

