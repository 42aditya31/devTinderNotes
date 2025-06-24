
# **DevTinder – Node.js Project Design and Architecture Notes**

---

## 1. **Project Requirements – Core Features (MVP)**

The following features will be developed first:

* **Create an Account** – User registration
* **Login** – User authentication
* **Update Profile** – Modify personal details
* **Feed Page** – View profiles of other developers
* **Send Connection Request** – Request to collaborate
* **View Matches** – See accepted connections
* **Connection Requests Page** – View sent and received requests

Additional features like chat, notifications, etc. will be added later.

---

## 2. **High Level Design (HLD)**

### **Definition:**

High Level Design describes the **overall architecture** of the system:

* Focuses on **components**, **modules**, and their **interactions**.
* Created **after requirements gathering**.
* **Does not include internal logic or code structure.**

### **What it includes:**

* Service boundaries (User Service, Match Service, etc.)
* API flows
* Database for each service
* Communication protocols (HTTP REST APIs)
* System interaction flow (e.g., Frontend → Gateway → Microservices)

### **Purpose in our project:**

* Help define **service responsibilities**.
* Establish how components like user service and frontend interact.
* Blueprint before jumping to coding.

---

## 3. **Frontend System Architecture**

### **Definition:**

Frontend system architecture defines how the **client-side application** is structured, including:

* UI component structure
* State management flow
* Routing and API integration
* Communication with backend services

### **In DevTinder (React.js):**

* Reusable functional components
* React Router for page routing
* Context API or local state for initial version
* Axios/fetch for HTTP requests to backend
* UI states (loading, error, data) handled per feature

---

## 4. **Difference: HLD vs LLD vs Planning**

| Category         | HLD (High-Level Design)                   | LLD (Low-Level Design)                      | Planning                                        |
| ---------------- | ----------------------------------------- | ------------------------------------------- | ----------------------------------------------- |
| **Focus**        | Architecture, components, service design  | Internal logic, code structure, DB schemas  | Goals, scope, timeline, risks, responsibilities |
| **Detail Level** | Abstract view                             | In-depth technical details                  | Non-technical                                   |
| **Output**       | Architecture diagram, module interactions | Class diagrams, DB design, function details | Gantt charts, task lists, resource planning     |
| **Purpose**      | Guide LLD and development decisions       | Guide coding phase                          | Ensure timely and efficient execution           |

---

## 5. **Tech Stack Planning**

| Layer        | Technology Used      |
| ------------ | -------------------- |
| **Frontend** | React.js             |
| **Backend**  | Node.js (Express.js) |
| **Database** | MongoDB              |

Each backend service will use **Node.js + MongoDB**.

---

## 6. **Low Level Design (LLD)**

### **Definition:**

LLD focuses on:

* Internal logic of each module/service
* Database design (collections, fields, structure)
* API routes and validations
* Algorithms, utility functions, etc.

### **In DevTinder – LLD for User & Connection Services**

---

### **Database Design**

#### **User Collection:**

| Field            | Type   | Description             |
| ---------------- | ------ | ----------------------- |
| `username`       | String | Developer's public name |
| `email`          | String | User’s login credential |
| `password`       | String | Hashed password         |
| `profilePicture` | String | URL of the image        |
| `bio`            | String | Short user intro        |
| `age`            | Number | User’s age              |
| `location`       | String | User's city or country  |
| `gender`         | String | User’s gender           |

#### **ConnectionRequest Collection:**

| Field        | Type     | Description                                     |
| ------------ | -------- | ----------------------------------------------- |
| `senderId`   | ObjectId | User who sent the request (referencing `users`) |
| `receiverId` | ObjectId | User who received the request                   |
| `status`     | String   | `pending` / `accepted` / `rejected` / `ignored` |

---

## 7. **REST API – Concepts and Design**

### **What is REST API?**

REST (Representational State Transfer) is an architectural style that:

* Uses **HTTP** methods for communication.
* Treats data as **resources** (e.g., users, connections).
* Is **stateless** – each request is independent.

### **HTTP Methods in REST APIs:**

| Method | Purpose                           |
| ------ | --------------------------------- |
| GET    | Retrieve data                     |
| POST   | Create new data                   |
| PUT    | Replace entire data of a resource |
| PATCH  | Partially update data             |
| DELETE | Remove a resource                 |

---

### **PUT vs PATCH – Main Difference**

| Aspect     | PUT                                        | PATCH                                 |
| ---------- | ------------------------------------------ | ------------------------------------- |
| Update     | Replaces the **entire** resource           | Updates **only the specified** fields |
| Idempotent | Yes – multiple same requests = same result | May or may not be idempotent          |
| Use Case   | Replace full user profile                  | Update only one field (e.g., bio)     |

---

## 8. **API Endpoints – User and Connection Features**

### **User APIs:**

| Endpoint   | Method | Description                |
| ---------- | ------ | -------------------------- |
| `/signup`  | POST   | Create a new user          |
| `/login`   | POST   | Authenticate user          |
| `/profile` | GET    | Get current user's profile |
| `/profile` | PUT    | Update entire profile      |
| `/profile` | PATCH  | Partially update profile   |

---

### **Connection APIs:**

| Endpoint                     | Method | Description                                     |
| ---------------------------- | ------ | ----------------------------------------------- |
| `/send-connection-request`   | POST   | Send a request to another user or ignore        |
| `/review-connection-request` | POST   | Accept or reject a request                      |
| `/connection-requests`       | GET    | View all requests sent and received by the user |
| `/connections`               | GET    | View all accepted connections (matches)         |

---
