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

---

## 🎯 API Interview Questions — Critical Scenarios

---

### 🏗️ REST Design Principles

**Q1. What makes an API truly RESTful? What are the 6 constraints?**

**Answer:**
REST (Representational State Transfer) has 6 architectural constraints defined by Roy Fielding:

| Constraint | Description | Violation Example |
|-----------|-------------|------------------|
| **Client-Server** | Separate UI from data storage | Server rendering HTML with business logic mixed |
| **Stateless** | Each request contains all info needed | Server-side sessions between requests |
| **Cacheable** | Responses must declare if cacheable | No Cache-Control headers |
| **Uniform Interface** | Consistent resource identification | `/getUser` instead of `/users/{id}` |
| **Layered System** | Client can't tell if connected to origin or intermediary | CORS issues breaking CDN caching |
| **Code on Demand** (optional) | Server can send executable code | JS served from API |

**Most violated in practice:** Statelessness (session-based APIs) and Uniform Interface (RPC-style naming like `/createUser`, `/deletePost`).

---

**Q2. What is idempotency and why does it matter in API design?**

**Answer:**
An operation is **idempotent** if calling it multiple times produces the same result as calling it once.

| Method | Idempotent? | Safe? | Notes |
|--------|------------|-------|-------|
| GET | ✅ Yes | ✅ Yes | No side effects |
| HEAD | ✅ Yes | ✅ Yes | Same as GET, no body |
| PUT | ✅ Yes | ❌ No | Replaces entire resource |
| DELETE | ✅ Yes | ❌ No | Deleting twice = same result (already gone) |
| PATCH | ❌ No* | ❌ No | Depends on operation (add vs set) |
| POST | ❌ No | ❌ No | Creates new resource each time |

**Why it matters:**
- Networks are unreliable — clients retry on timeout
- If POST is not idempotent and the client retries, you create duplicate orders/payments
- **Idempotency keys** solve this for POST:

```http
POST /payments HTTP/1.1
Idempotency-Key: uuid-12345-unique-per-request

{ "amount": 100, "currency": "USD" }
```

Server stores the key + result. Duplicate request with same key returns the same response without re-processing.

**Real use:** Stripe, Braintree, and all major payment APIs require idempotency keys for payment creation.

---

**Q3. How do you design API versioning, and what are the trade-offs of each approach?**

**Answer:**

| Strategy | Example | Pros | Cons |
|---------|---------|------|------|
| **URI versioning** | `/api/v1/users` | Explicit, cacheable, easy to debug | "Dirty" URLs, route proliferation |
| **Header versioning** | `Accept: application/vnd.api+json;version=1` | Clean URLs, proper REST | Hard to test in browser, can't cache per version |
| **Query param** | `/api/users?version=1` | Easy to test | Easy to ignore, cache issues |
| **Content negotiation** | `Accept: application/vnd.company.v1+json` | RFC-compliant | Complex, low adoption |

**Industry practice:** URI versioning is overwhelmingly used (GitHub, Twitter, Stripe use `/v1/`, `/v2/`). It's explicit, CDN-friendly, and easy to communicate to API consumers.

**When to version:** Only version when you're making breaking changes (removing fields, changing types, changing behavior). Adding optional fields is not a breaking change.

---

### ⚖️ Comparison & Trade-offs

**Q4. When would you choose GraphQL over REST, and what are its hidden costs?**

**Answer:**

**Choose GraphQL when:**
- Client needs precise control over response shape (mobile apps with bandwidth constraints)
- Multiple clients need different data shapes from same endpoint
- Rapid frontend iteration without backend changes
- Complex nested data relationships (social graph, content hierarchy)

**Choose REST when:**
- Simple CRUD operations
- Team not familiar with GraphQL
- Heavy HTTP caching needed (GraphQL POST requests are not cached by default)
- File upload/download operations
- Simple public APIs

**Hidden GraphQL costs:**

| Problem | Description | Solution |
|---------|-------------|---------|
| **N+1 queries** | Resolving nested fields triggers N DB queries | DataLoader (batching) |
| **No HTTP caching** | All queries hit server (POST) | Persisted queries, CDN with cache keys |
| **Query complexity** | Malicious deeply nested query can DoS server | Query depth limiting, cost analysis |
| **Schema versioning** | No standard versioning | Deprecate fields, schema stitching |
| **Overfetching at DB level** | Even though client requests few fields, resolver may fetch all | Field-level DB projections |

```javascript
// DataLoader solves N+1 in GraphQL
const userLoader = new DataLoader(async (ids) => {
    const users = await User.findAll({ where: { id: ids } });
    return ids.map(id => users.find(u => u.id === id));
});

// Resolver uses loader — batches all user lookups into ONE query
const resolvers = {
    Post: {
        author: (post) => userLoader.load(post.authorId)
    }
};
```

---

**Q5. WebSockets vs Server-Sent Events (SSE) vs Long Polling — when to use each?**

**Answer:**

| Feature | WebSockets | SSE | Long Polling |
|---------|-----------|-----|-------------|
| **Direction** | Bidirectional | Server → Client only | Server → Client |
| **Protocol** | ws:// (separate from HTTP) | HTTP | HTTP |
| **Browser support** | Universal | Universal (not IE) | Universal |
| **Reconnect** | Manual | Automatic | Manual |
| **Overhead** | Low (after handshake) | Low | High (new HTTP request each time) |
| **Load balancer friendly** | Requires sticky sessions | ✅ Yes | ✅ Yes |
| **Firewall/proxy friendly** | Sometimes blocked | ✅ Yes | ✅ Yes |

**Choose WebSockets:** Chat, multiplayer gaming, collaborative editing (Google Docs), trading platforms — bidirectional, low-latency required.

**Choose SSE:** Live dashboards, news feeds, notifications, stock tickers — server pushes updates, client doesn't need to send data.

**Choose Long Polling:** Legacy systems, firewall-restricted environments, simple notifications where SSE/WS are blocked.

---

### 🔐 API Security

**Q6. What are the most critical API security vulnerabilities and how do you prevent them?**

**Answer:**

| Vulnerability | Attack | Prevention |
|--------------|--------|-----------|
| **Broken Object Level Auth (BOLA/IDOR)** | `GET /orders/12345` where 12345 belongs to another user | Always validate resource ownership, not just authentication |
| **Mass Assignment** | POST with `{"role": "admin"}` injected into user update | Whitelist allowed fields, never bind request body directly to model |
| **Excessive Data Exposure** | API returns full user object including SSN, password hash | Explicit response serializers, never serialize full model |
| **Rate Limiting Missing** | Brute-force login, credential stuffing | Per-IP and per-user rate limiting |
| **Security Misconfiguration** | CORS allows `*`, debug endpoints in production | Strict CORS, remove debug routes |
| **Injection** | SQL/NoSQL/command injection via API params | Parameterized queries, input validation |

**BOLA is #1 in OWASP API Security Top 10** — it's the most common and most impactful:

```php
// ❌ Vulnerable — only checks authentication, not authorization
$order = Order::find($request->id);
return $order;

// ✅ Secure — validates ownership
$order = Order::where('id', $request->id)
              ->where('user_id', auth()->id())  // Ownership check
              ->firstOrFail();
return $order;
```

---

**Q7. How would you implement API rate limiting at scale?**

**Answer:**
Different rate limiting algorithms suit different scenarios:

**Token Bucket (most flexible):**
- Tokens added at rate R, bucket holds max B tokens
- Request consumes 1 token, rejected if bucket empty
- Allows bursting up to B requests

**Fixed Window Counter:**
- Count requests per window (e.g., 100/minute)
- Reset counter at window boundary
- **Problem:** 200 requests possible at window boundary (99 at end of window 1 + 101 at start of window 2)

**Sliding Window Log (most accurate, most memory):**
- Store timestamp of each request
- Count requests in past 60 seconds
- **Problem:** O(requests) memory per user

**Sliding Window Counter (best balance):**
```
current_window_count + prev_window_count * (1 - elapsed/window_size)
```

**Redis-based implementation:**

```lua
-- Lua script for atomic token bucket in Redis
local key = KEYS[1]
local capacity = tonumber(ARGV[1])
local refill_rate = tonumber(ARGV[2])
local now = tonumber(ARGV[3])

local bucket = redis.call('HMGET', key, 'tokens', 'last_refill')
local tokens = tonumber(bucket[1]) or capacity
local last_refill = tonumber(bucket[2]) or now

-- Refill tokens based on elapsed time
local elapsed = now - last_refill
local new_tokens = math.min(capacity, tokens + elapsed * refill_rate)

if new_tokens >= 1 then
    redis.call('HMSET', key, 'tokens', new_tokens - 1, 'last_refill', now)
    return 1  -- Allowed
else
    return 0  -- Rate limited
end
```

**Distributed rate limiting:** Use Redis with Lua scripts for atomicity. A single Redis INCR + EXPIRE is NOT atomic — use Lua or Redis modules (redis-cell).

---

### 🆕 Modern API Concepts

**Q8. What is an API Gateway and what problems does it solve?**

**Answer:**
An API Gateway is a single entry point for all clients, handling cross-cutting concerns:

```
Clients → API Gateway → Microservices
          (auth, rate limit,
           routing, transform,
           logging, caching)
```

**Problems it solves:**

| Problem | Without Gateway | With Gateway |
|---------|----------------|-------------|
| Authentication | Every service implements auth | Centralized, single point |
| Rate limiting | Per service configuration | Unified policy |
| SSL termination | Each service handles TLS | Gateway handles, services use HTTP internally |
| Request routing | Clients know all service URLs | Single endpoint, gateway routes |
| API composition | Client makes N calls | Gateway aggregates (Backend for Frontend pattern) |
| Observability | Distributed logging | Centralized access logs |

**Popular API Gateways:** AWS API Gateway, Kong, Nginx, Traefik, Envoy.

**Backend for Frontend (BFF) pattern:** Different API Gateway instances per client type (mobile BFF, web BFF) — each optimizes response for its client's needs.

---

**Q9. What is the difference between synchronous and asynchronous API patterns?**

**Answer:**

**Synchronous (request-response):** Client waits for response.

```
Client → POST /process → Server (processes) → Response (done/failed)
```

**Asynchronous (job queue):** Client submits job, polls or gets callback.

```
Client → POST /jobs → Server (queues) → 202 Accepted + job_id
Client → GET /jobs/{id} → pending/processing/done
     OR
Server → POST client_callback_url → done (webhook)
```

**When to use async APIs:**
- Processing takes >5 seconds (file conversion, ML inference, bulk operations)
- Processing is unreliable (may need retries)
- Long-running workflows

**Webhook design best practices:**
```http
POST /your-callback-url HTTP/1.1
X-Webhook-Signature: sha256=abc123  # HMAC signature for verification
Content-Type: application/json

{ "event": "payment.completed", "data": {...} }
```

Always verify webhook signatures to prevent spoofed events.

---

**Q10. What HTTP status codes are commonly misused and what's correct?**

**Answer:**

| Misuse | Wrong Code | Correct Code | Reason |
|--------|-----------|-------------|--------|
| Validation error | 500 | 422 Unprocessable Entity | Client sent invalid data, not server error |
| Unauthorized (not logged in) | 403 | 401 Unauthorized | Not authenticated at all |
| Forbidden (logged in, no permission) | 401 | 403 Forbidden | Authenticated but not authorized |
| Resource not found | 200 with `{"error": "not found"}` | 404 Not Found | Never put errors in 200 response |
| Empty collection | 404 | 200 with `[]` | Empty list is a valid response |
| Business rule violation | 500 | 409 Conflict or 422 | "Out of stock" is not a server error |
| Rate limited | 403 | 429 Too Many Requests | Distinct semantic meaning |

**Key rule:** 4xx = client error (fix your request), 5xx = server error (try again later). Mixing these breaks retry logic and monitoring alerts.
