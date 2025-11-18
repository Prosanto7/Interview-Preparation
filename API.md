## 🔍 **What is an API?**

An **API (Application Programming Interface)** is a set of rules and protocols that allows one piece of software (client) to interact with another (server or service).

> 🔁 **APIs are messengers** — they take requests and bring back data or actions from a server.

---

## 💡 **Real-Life Analogy**

Think of a **restaurant**:
- **You (client)** order food (make a request)
- **Waiter (API)** takes your order to the kitchen and brings food back
- **Kitchen (server)** prepares your order (processes the request)

---

## 🛠️ **Why Use APIs?**

- Enable communication between systems/applications
- Allow third-party access to services (e.g., Google Maps API)
- Enable modular architecture (front-end and back-end separation)
- Integrate external features (payment gateways, social login, etc.)

---

## 🔧 **Types of APIs**

| API Type        | Description | When to Use | Example |
|-----------------|-------------|-------------|---------|
| **REST API**    | Stateless, uses HTTP methods (GET, POST, PUT, DELETE). Data in JSON/XML | Web apps, mobile apps, easy integration | Twitter API, GitHub API |
| **SOAP API**    | Protocol-based, strict structure, XML-based | Enterprise systems needing high security | Payment systems, Banking |
| **GraphQL**     | Query language for APIs, client specifies needed data | Apps needing precise data (reduce over-fetching) | Facebook, GitHub |
| **WebSockets**  | Full-duplex communication, keeps a persistent connection | Real-time apps (chat, live updates) | WhatsApp, Trading apps |
| **gRPC**        | Uses Protocol Buffers (Protobuf), very fast, binary data | Microservices, high-performance apps | Google internal services |

---

## 🔍 **Detailed Comparison: REST vs SOAP vs GraphQL vs gRPC**

| Feature         | REST        | SOAP        | GraphQL     | gRPC        |
|-----------------|-------------|-------------|-------------|-------------|
| Protocol        | HTTP        | HTTP, SMTP  | HTTP        | HTTP/2      |
| Data Format     | JSON, XML   | XML         | JSON        | Protobuf    |
| Flexibility     | Medium      | Low         | High        | Medium      |
| Speed           | Fast        | Slower      | Fast        | Very Fast   |
| Use Case        | General web apps | Enterprise, banking | Complex data needs | Microservices, real-time |
| Learning Curve  | Easy        | Complex     | Medium      | Medium      |
| Browser-Friendly| Yes         | No          | Yes         | No          |
| Versioning      | Through URLs| Built-in    | Not needed (flexible queries) | Manual |

---

## 🧭 **When to Use Which API?**

### ✅ **Use REST API When:**
- You’re building standard web/mobile applications
- You want simplicity and scalability
- You need support for caching, status codes, and stateless operations

🔧 *Example:* Building a weather app using OpenWeather REST API.

---

### ✅ **Use SOAP API When:**
- You need **high security**, **ACID transactions**
- Your project involves **financial institutions or telecoms**
- You work in a **legacy enterprise system**

🔧 *Example:* Integrating with a bank’s payment gateway (e.g., SWIFT)

---

### ✅ **Use GraphQL When:**
- You need **precise data fetching**
- You have a **complex frontend** requiring flexible queries
- Your app needs to reduce network calls

🔧 *Example:* A dashboard with dynamic widgets using different types of data.

---

### ✅ **Use WebSockets When:**
- You’re building **real-time applications**
- You need **bi-directional** data flow (server-client and client-server)

🔧 *Example:* Chat app, live auction, stock trading platform

---

### ✅ **Use gRPC When:**
- You have **many microservices** needing fast, lightweight communication
- You’re building **IoT, backend-heavy, or high-performance** systems

🔧 *Example:* Communication between services in a ride-sharing app like Uber.

---

## 🌐 **Examples of Popular APIs**

| Service        | API Example            |
|----------------|------------------------|
| Google Maps    | REST API               |
| Stripe         | REST API (payments)    |
| Facebook       | GraphQL API            |
| Twilio         | REST/SOAP for messaging|
| Slack          | Webhooks + REST API    |
| WhatsApp       | WebSockets API         |

---

## 🧪 Bonus: Internal vs External APIs

| Type         | Description | Example |
|--------------|-------------|---------|
| **Internal** | APIs used within your app/microservices | Auth service talks to Order service |
| **External** | APIs exposed to outside clients/users | Stripe’s payment API, Facebook Login |

---

## 📦 API in a Full Stack Web App

1. **Frontend (React/Vue)** — calls REST API
2. **Backend (Laravel/Node.js/Spring Boot)** — handles the logic
3. **Database (MySQL/PostgreSQL)** — stores data
4. **External APIs** — e.g., SMS via Twilio, maps via Google

---



A **PUT** request is one of the standard HTTP methods used in web development and RESTful APIs. It is mainly used to **update** or **replace** a resource on the server.

---

### 🔑 **Key Characteristics of PUT**

| Feature                 | Description                                                                                   |
| ----------------------- | --------------------------------------------------------------------------------------------- |
| **HTTP Method**         | PUT                                                                                           |
| **Purpose**             | Create or **replace** a resource at a specific URI                                            |
| **Idempotent**          | ✅ Yes — sending the same PUT request multiple times has the same effect as sending it once    |
| **Request Body**        | Contains the full representation of the resource to update or create                          |
| **Common Status Codes** | `200 OK`, `201 Created`, `204 No Content`, `400 Bad Request`, `404 Not Found`, `409 Conflict` |

---

### 📘 **How PUT Works**

A PUT request is used when:

* You want to **update an existing resource**.
* Or, if the resource does not exist, to **create it** at the specified URI (depends on the API design).

---

### 📦 **Example: Updating a User Resource**

#### ✅ Request:

---

## 🧭 HTTP Methods Deep Dive

This section clarifies when to use PUT vs PATCH in RESTful APIs and how they differ.

### PUT

A **PUT** request is one of the standard HTTP methods used in web development and RESTful APIs. It is mainly used to **update** or **replace** a resource on the server.

{
  "id": 123,
  "name": "John Doe",
  "email": "john.doe@example.com"
}
```

#### 🔄 Server Behavior:
* Checks if user with ID `123` exists.
* If exists, replaces the entire resource with new data.
* If not (depending on design), may create a new user with ID `123`.

#### ✅ Response:

```http
HTTP/1.1 200 OK
```

or if created:

```http
HTTP/1.1 201 Created
Location: /users/123
```

---

### 🆚 PUT vs PATCH

| Feature         | PUT                                | PATCH                     |
| --------------- | ---------------------------------- | ------------------------- |
| **Purpose**     | Full update                        | Partial update            |
| **Idempotent**  | ✅ Yes                              | ✅ Yes (typically)         |
| **Payload**     | Full representation of resource    | Only the fields to update |
| **Performance** | Can be heavier due to full payload | Lighter                   |

---

### 🔐 Security Considerations

* PUT requests often modify resources and **should be protected** using authentication/authorization.
* They **should not be cached** unless explicitly allowed with headers like `Cache-Control`.

---

### ✅ Best Practices

1. **Use PUT to update or create** a resource at a specific URI.
2. **Make PUT idempotent** — same result even if sent multiple times.
3. Send the **complete resource**, not just the fields you want to change.
4. Validate and sanitize input to avoid malicious changes.
5. Respond with appropriate status codes (e.g., `204` for success without content, `400` for bad input).

---

### 🔧 Common Use Case in REST APIs

```bash
# Update a product
PUT /products/45
{
  "id": 45,
  "name": "Smart Watch",
  "price": 129.99
}
```

---

### PATCH

A **PATCH** request is an HTTP method used to **partially update** a resource on the server. It is commonly used in RESTful APIs when you only want to modify a subset of a resource’s properties, **without replacing the entire object**, which you would do with a `PUT` request.

---

### 🔑 **Key Characteristics of PATCH**

| Feature                 | Description                                                                                |
| ----------------------- | ------------------------------------------------------------------------------------------ |
| **HTTP Method**         | PATCH                                                                                      |
| **Purpose**             | Partial update of an existing resource                                                     |
| **Idempotent**          | ✅ Typically Yes (sending same PATCH request repeatedly results in the same resource state) |
| **Request Body**        | Only the fields that need to be updated                                                    |
| **Common Status Codes** | `200 OK`, `204 No Content`, `400 Bad Request`, `404 Not Found`, `409 Conflict`             |

---

### 📘 **How PATCH Works**

* The client sends **only the changed fields** in the request body.
* The server updates those fields **without touching** the rest of the resource.
* PATCH does **not replace** the resource; it **modifies** it.

---

### 📦 **Example: Updating a User Email**

#### ✅ Request:

```http
PATCH /users/123 HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "email": "new.email@example.com"
}
```

#### 🔄 Server Behavior:

* Finds user with ID `123`.
* Updates only the `email` field.
* Leaves other fields (`name`, `address`, etc.) untouched.

#### ✅ Response:

```http
HTTP/1.1 200 OK
```

or if no content is returned:

```http
HTTP/1.1 204 No Content
```

---

### 🆚 PATCH vs PUT

| Feature         | PATCH                   | PUT                          |
| --------------- | ----------------------- | ---------------------------- |
| **Update Type** | Partial                 | Full (replace entire object) |
| **Idempotent**  | ✅ Typically (should be) | ✅ Yes                        |
| **Use Case**    | Update specific fields  | Update all fields            |
| **Payload**     | Only changed fields     | Full object data             |

---

### 🔐 Security Considerations

* Like other modifying requests (`PUT`, `DELETE`), **authentication** and **authorization** are required.
* Input should be **validated** to avoid unwanted changes.

---

### ✅ Best Practices

1. Use PATCH when updating **only a few fields**.
2. Ensure the request is **idempotent** if your system requires reliability.
3. Validate the fields in the request — don't allow arbitrary field updates.
4. Return a proper HTTP status code:

   * `200 OK` with updated resource
   * `204 No Content` if successful but no body returned
   * `400` for bad request
   * `404` if the resource does not exist

---

### 🔧 Example in REST API

#### Endpoint:

```http
PATCH /products/45
```

#### Payload:

```json
{
  "price": 119.99
}
```

> This will only update the `price` of product `45`, keeping `name`, `description`, `stock`, etc., unchanged.

---

### 🔧 Example with Axios in JavaScript

```js
axios.patch('/users/123', {
  email: 'updated@email.com'
})
.then(response => console.log('Updated:', response.data))
.catch(error => console.error(error));
```

---

Would you like examples for **Laravel**, **Node.js (Express)**, or **Python (Flask/FastAPI)**?

---

## 📚 References

- MDN: HTTP request methods — https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods
- RFC 7231 (HTTP/1.1 Semantics): PUT — https://datatracker.ietf.org/doc/html/rfc7231#section-4.3.4
- RFC 5789 (PATCH Method) — https://datatracker.ietf.org/doc/html/rfc5789
- GraphQL Official — https://graphql.org/
- gRPC — https://grpc.io/docs/
- WebSockets (RFC 6455) — https://datatracker.ietf.org/doc/html/rfc6455
