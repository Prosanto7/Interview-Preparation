# Interview Topics — Detailed Guide

> Comprehensive explanations and interview Q&A covering backend, PHP/Laravel, system design, and software engineering concepts.

---

## Table of Contents

1. [Rate Limiting](#1-rate-limiting)
2. [CORS](#2-cors-cross-origin-resource-sharing)
3. [Autoload & Namespace in PHP/Laravel](#3-autoload--namespace-in-phplaravel)
4. [Database Sharding](#4-database-sharding)
5. [Database Indexing — How It Works](#5-database-indexing--how-it-works)
6. [Which Fields to Index](#6-which-fields-to-index)
7. [Cookies — SameSite & Lax](#7-cookies--samesite--lax)
8. [SSO — Single Sign-On](#8-sso--single-sign-on)
9. [OAuth 2.0](#9-oauth-20)
10. [JWT — JSON Web Token](#10-jwt--json-web-token)
11. [Laravel Sanctum — Is It Stateless?](#11-laravel-sanctum--is-it-stateless)
12. [Basic Token Authentication](#12-basic-token-authentication)
13. [N+1 Problem in Laravel](#13-n1-problem-in-laravel)
14. [Debugging a Slow API](#14-debugging-a-slow-api)
15. [Interface](#15-interface)
16. [Abstract Class](#16-abstract-class)
17. [Trait](#17-trait)
18. [Log Monitoring](#18-log-monitoring)
19. [Service Container](#19-service-container)
20. [Message Broker](#20-message-broker)
21. [Singleton Pattern](#21-singleton-pattern)
22. [SQL Joins](#22-sql-joins)
23. [Laravel Request Lifecycle](#23-laravel-request-lifecycle)
24. [Service Provider in Laravel](#24-service-provider-in-laravel)
25. [SOLID Principles](#25-solid-principles)
26. [Form Security](#26-form-security)
27. [File Upload Security](#27-file-upload-security)
28. [API Security](#28-api-security)
29. [System Design for 1M Users / 100K req/s](#29-system-design-for-1m-users--100k-reqs)
30. [Software Principles vs Design Patterns](#30-software-principles-vs-design-patterns)
31. [Race Condition](#31-race-condition)

---

## 1. Rate Limiting

### What Is Rate Limiting?

Rate limiting controls **how many requests a client can make to an API within a given time window**. It protects systems from abuse, DDoS attacks, and overload.

### Common Strategies

| Strategy | How It Works |
|---|---|
| **Fixed Window** | Count requests per fixed interval (e.g., 100 req/min). Resets at window end. |
| **Sliding Window** | Uses a rolling time window to smooth bursts. |
| **Token Bucket** | Tokens are added at a fixed rate. Each request consumes a token. Allows bursting. |
| **Leaky Bucket** | Requests drip out at a fixed rate, queue absorbs bursts. |

### In Laravel

```php
// routes/api.php
Route::middleware('throttle:60,1')->group(function () {
    Route::get('/user', [UserController::class, 'index']);
});

// Custom rate limiter in AppServiceProvider
RateLimiter::for('api', function (Request $request) {
    return Limit::perMinute(60)->by($request->user()?->id ?: $request->ip());
});
```

### HTTP Headers

```
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 45
Retry-After: 30          // when 429 Too Many Requests is returned
```

### Interview Questions

1. **What is rate limiting and why is it important?**
   - Controls request frequency per client to prevent abuse, ensure fair usage, and protect server resources.

2. **Difference between Token Bucket and Leaky Bucket?**
   - Token Bucket allows short bursts (unused tokens accumulate). Leaky Bucket enforces a strict constant output rate.

3. **What HTTP status code is returned when rate limit is exceeded?**
   - `429 Too Many Requests`

4. **How would you implement rate limiting per user vs per IP?**
   - Per user: use authenticated user ID as the key. Per IP: use `$request->ip()`. Combine both with a fallback for unauthenticated users.

5. **How do you implement distributed rate limiting across multiple servers?**
   - Use a shared store like Redis so all application nodes read/write the same counter.

6. **What is the difference between rate limiting and throttling?**
   - Rate limiting blocks excess requests immediately. Throttling may queue them or slow processing.

7. **How does Laravel's throttle middleware work internally?**
   - It uses the cache driver (typically Redis) to track hit counts per key within a time window.

8. **What happens to burst traffic in a fixed window rate limiter?**
   - A user can send 100 req at 11:59 and 100 req at 12:00, effectively sending 200 req in 2 seconds — the "burst at boundary" problem.

---

## 2. CORS (Cross-Origin Resource Sharing)

### What Is CORS?

CORS is a **browser security mechanism** that controls how web pages can request resources from a different origin (domain, protocol, or port) than the one that served the page.

### Same-Origin Policy

A browser blocks JavaScript from making requests to `api.other.com` from a page on `app.mysite.com` unless the server explicitly permits it.

### How CORS Works

```
Browser → Preflight OPTIONS request → Server
Server  → Access-Control-Allow-Origin: https://app.mysite.com
Browser → Sends actual request if header matches
```

**Simple Requests** (GET, POST with certain content types) skip the preflight.  
**Preflighted Requests** (PUT, DELETE, custom headers) trigger a preflight OPTIONS first.

### Key Headers

| Header | Purpose |
|---|---|
| `Access-Control-Allow-Origin` | Which origins are allowed |
| `Access-Control-Allow-Methods` | Allowed HTTP methods |
| `Access-Control-Allow-Headers` | Allowed request headers |
| `Access-Control-Allow-Credentials` | Allow cookies/auth headers |
| `Access-Control-Max-Age` | Cache preflight duration |

### In Laravel

```php
// config/cors.php
return [
    'paths' => ['api/*'],
    'allowed_methods' => ['*'],
    'allowed_origins' => ['https://app.mysite.com'],
    'allowed_headers' => ['Content-Type', 'Authorization'],
    'supports_credentials' => true,
];
```

### Interview Questions

1. **What is CORS and why does it exist?**
   - Browser security policy to prevent malicious sites from making unauthorized cross-origin requests on behalf of users.

2. **What is a preflight request?**
   - An HTTP OPTIONS request sent by the browser before the actual request to check if the server allows it.

3. **What does `Access-Control-Allow-Origin: *` mean? Is it safe?**
   - Allows any origin. Not safe for authenticated endpoints — cannot be combined with `credentials: true`.

4. **Does CORS protect the server or the browser user?**
   - The browser user. CORS is enforced only by browsers; server-to-server requests ignore it.

5. **Can you bypass CORS?**
   - On the server side, yes (proxy, missing CORS headers). CORS is a browser-enforced policy, not a server firewall.

6. **What is the difference between CORS and CSRF?**
   - CORS controls which origins can read responses. CSRF exploits the browser's automatic cookie sending. They solve different problems.

7. **What happens if `supports_credentials` is true but `allowed_origins` is `*`?**
   - The browser will reject it. When credentials are included, a specific origin must be set, not wildcard.

---

## 3. Autoload & Namespace in PHP/Laravel

### Autoloading

Autoloading automatically loads PHP class files **without manually writing `require` or `include`**.

**PSR-4 Autoloading** (standard):

```json
// composer.json
"autoload": {
    "psr-4": {
        "App\\": "src/"
    }
}
```

Run `composer dump-autoload` to regenerate the classmap.

**How it works internally:**
1. Composer generates a classmap or directory map.
2. When a class is first used, PHP calls registered autoload functions.
3. Composer's autoloader resolves the namespace to a file path and includes it.

### Namespace

A namespace is a **logical grouping** of classes, functions, and constants to avoid naming conflicts.

```php
namespace App\Services\Payment;

class StripeService
{
    // ...
}
```

Usage:
```php
use App\Services\Payment\StripeService;

$stripe = new StripeService();
```

### Laravel Autoload Convention

`App\Http\Controllers\UserController` → `app/Http/Controllers/UserController.php`

The `App\` prefix maps to the `app/` directory via `composer.json`.

### Creating a Custom Autoloaded Package

```json
"autoload": {
    "psr-4": {
        "MyPackage\\": "packages/mypackage/src/"
    }
}
```

Then run `composer dump-autoload`.

### Interview Questions

1. **What is PSR-4 autoloading?**
   - A PHP standard that maps namespace prefixes to filesystem directories, enabling automatic class loading.

2. **What does `composer dump-autoload` do?**
   - Regenerates the Composer autoloader classmap so newly added classes are recognized.

3. **What is the difference between PSR-0 and PSR-4?**
   - PSR-0 required underscores in class names to represent directory separators and had strict directory nesting. PSR-4 is simpler and more flexible.

4. **Can you have multiple namespaces in one file?**
   - Yes, but it's strongly discouraged. One class per file is the PSR-1 standard.

5. **What happens if you forget to run `composer dump-autoload` after adding a new class?**
   - PHP will throw a `Class not found` error because the autoloader doesn't know about the new file.

6. **How does Laravel resolve `App\Models\User` to a file?**
   - Via `composer.json` PSR-4 mapping: `App\` → `app/`. So `App\Models\User` → `app/Models/User.php`.

---

## 4. Database Sharding

### What Is Sharding?

Sharding is a **horizontal scaling** technique that **splits a large database into smaller pieces (shards)** distributed across multiple servers, each holding a subset of the data.

### Sharding vs Partitioning

| | Sharding | Partitioning |
|---|---|---|
| Scope | Across multiple servers/nodes | Within a single server |
| Purpose | Scale-out | Organize data |

### Sharding Strategies

| Strategy | How | Pros | Cons |
|---|---|---|---|
| **Range-based** | Shard by value range (e.g., user ID 1–1M on Shard 1) | Simple | Hotspots if range is uneven |
| **Hash-based** | `shard = hash(key) % N` | Even distribution | Resharding is painful |
| **Directory-based** | Lookup table maps key to shard | Flexible | Lookup table becomes bottleneck |
| **Geographic** | Shard by region | Low latency per region | Data sovereignty complexity |

### Challenges

- **Cross-shard queries**: JOINs across shards are complex/expensive.
- **Rebalancing**: Adding shards requires data migration.
- **Transactions**: Distributed transactions (2PC) are complex and slow.
- **Hotspots**: One shard may receive disproportionate traffic.

### Interview Questions

1. **What is database sharding and when would you use it?**
   - Splitting data across multiple database servers to handle scale beyond a single instance's capacity.

2. **What is the difference between sharding and replication?**
   - Sharding splits data across nodes (different data on each). Replication copies the same data across nodes (for redundancy/read scaling).

3. **What are the trade-offs of hash-based sharding?**
   - Even distribution but very hard to add/remove shards without rehashing and migrating data.

4. **How do you handle a JOIN across shards?**
   - Denormalize data, use application-level joins, or use a distributed query engine. True cross-shard JOINs are expensive.

5. **What is consistent hashing and why is it used in sharding?**
   - A hashing approach that minimizes data movement when shards are added/removed by mapping both keys and shards onto a ring.

6. **What are hotspots in sharding and how do you prevent them?**
   - When one shard receives disproportionate load. Prevent with good shard key selection, adding virtual nodes, or using consistent hashing.

7. **How does sharding affect ACID transactions?**
   - Distributed transactions across shards require 2PC which adds latency and complexity. Most systems relax isolation or avoid cross-shard transactions.

---

## 5. Database Indexing — How It Works

### What Is a Database Index?

An index is a **data structure** (typically a B-Tree or Hash) that allows the database to find rows **without scanning the entire table**.

### B-Tree Index (Default in MySQL/PostgreSQL)

```
         [50]
        /    \
    [25]      [75]
   /    \    /    \
 [10] [30] [60] [90]
```

- Balanced tree, O(log n) lookup.
- Supports range queries, ORDER BY, and LIKE prefix searches.
- Automatically maintained on INSERT/UPDATE/DELETE.

### How a Query Uses an Index

```sql
-- Without index: full table scan
SELECT * FROM users WHERE email = 'a@b.com';

-- With index on email: B-Tree lookup → O(log n)
CREATE INDEX idx_users_email ON users(email);
```

### Types of Indexes

| Type | Description |
|---|---|
| **Primary Key** | Clustered index; data physically sorted by this key |
| **Unique** | Enforces uniqueness + provides index |
| **Composite** | Index on multiple columns |
| **Full-Text** | For `LIKE '%word%'` type searches |
| **Partial** | Index on a subset of rows (PostgreSQL) |
| **Covering** | Index contains all columns needed by a query |

### Composite Index — Column Order Matters

```sql
CREATE INDEX idx_name_age ON users(last_name, first_name);
-- Efficient: WHERE last_name = 'Smith'
-- Efficient: WHERE last_name = 'Smith' AND first_name = 'John'
-- NOT efficient: WHERE first_name = 'John' (skips leftmost column)
```

### Interview Questions

1. **What is a database index and how does it speed up queries?**
   - A B-Tree (or hash) data structure that lets the DB locate rows without a full table scan.

2. **What is the difference between a clustered and non-clustered index?**
   - Clustered: actual table data is physically ordered by this index (one per table). Non-clustered: a separate structure pointing back to the actual rows.

3. **What is a covering index?**
   - An index that contains all columns referenced in a query so the DB never needs to read the actual table rows.

4. **Why do indexes slow down writes?**
   - Every INSERT/UPDATE/DELETE must also update the index data structure, adding overhead.

5. **What is index cardinality?**
   - The number of unique values in the indexed column. High cardinality (e.g., email) makes indexes more effective. Low cardinality (e.g., boolean) usually doesn't benefit.

6. **What is an index scan vs a full table scan?**
   - Index scan traverses the B-Tree to find matching rows. Full table scan reads every row. Index scan is faster when selectivity is high.

7. **When would you NOT use an index?**
   - Small tables, low-cardinality columns, columns rarely used in WHERE/JOIN/ORDER BY, or write-heavy tables where write overhead outweighs read benefit.

8. **What is the `EXPLAIN` statement and how do you use it?**
   - Shows the query execution plan so you can see whether indexes are being used, and how.

---

## 6. Which Fields to Index

### General Rules

- **Primary keys** — always indexed automatically.
- **Foreign keys** — always index them to speed up JOINs.
- **Columns used in `WHERE` clauses** — high-cardinality columns benefit most.
- **Columns used in `ORDER BY` / `GROUP BY`** — avoid filesorts.
- **Columns used in `JOIN` conditions**.
- **Columns used in `LIKE 'prefix%'`** — B-Tree index helps; `LIKE '%suffix'` does not.

### Fields to Avoid Indexing

- Low-cardinality columns (e.g., `is_active` boolean, `gender`).
- Columns rarely used in queries.
- Very large text/blob columns (use full-text index instead).
- Tables with very few rows.

### Composite Index Column Order

Place the **most selective column first**, and the **range/sort column last**.

```sql
-- Good for: WHERE status = 'active' ORDER BY created_at
CREATE INDEX idx ON orders(status, created_at);
```

### Interview Questions

1. **How do you decide which columns to index?**
   - Analyze slow query logs, use EXPLAIN, and index columns in WHERE, JOIN, ORDER BY with high cardinality.

2. **Should you index every column?**
   - No. Each index has write overhead and storage cost. Over-indexing degrades write performance.

3. **Why is cardinality important when choosing index columns?**
   - High-cardinality columns (many unique values) allow the index to filter down to very few rows, making the index effective.

4. **What is a composite index and when is it better than two separate indexes?**
   - A composite index covers multi-column queries efficiently. Two separate indexes may both be scanned and then merged, which is less efficient.

---

## 7. Cookies — SameSite & Lax

### What Are Cookies?

Cookies are **small key-value pairs** stored in the browser and automatically sent with HTTP requests to the matching domain.

### SameSite Attribute

Controls whether cookies are sent with **cross-site requests**. Prevents CSRF attacks.

| Value | Behavior |
|---|---|
| `Strict` | Cookie sent ONLY for same-site requests. Never on cross-site navigation. |
| `Lax` | Cookie sent on same-site + top-level GET navigation (e.g., clicking a link). NOT on cross-site POST. |
| `None` | Cookie sent on all requests. **Requires `Secure` flag** (HTTPS only). |

```http
Set-Cookie: session=abc123; SameSite=Lax; Secure; HttpOnly; Path=/
```

### Other Cookie Flags

| Flag | Purpose |
|---|---|
| `HttpOnly` | Prevents JavaScript from accessing the cookie (`document.cookie`) |
| `Secure` | Cookie sent only over HTTPS |
| `Expires/Max-Age` | When the cookie expires |
| `Domain` | Which domain receives the cookie |
| `Path` | Which path the cookie applies to |

### Interview Questions

1. **What is the SameSite cookie attribute?**
   - Controls if cookies are sent on cross-site requests. Used to prevent CSRF attacks.

2. **What is the difference between `Lax` and `Strict`?**
   - Strict: never sent cross-site. Lax: sent on top-level same-site navigations (GET) but not on cross-site POST forms.

3. **What does `HttpOnly` prevent?**
   - Prevents JavaScript (`document.cookie`) from reading the cookie, protecting against XSS-based cookie theft.

4. **Why does `SameSite=None` require the `Secure` flag?**
   - Browsers enforce this to prevent insecure transmission of cross-site cookies over HTTP.

5. **How does SameSite=Lax protect against CSRF?**
   - A malicious site cannot make a cross-origin POST request that includes the session cookie, so the server rejects it.

6. **What is a session cookie vs a persistent cookie?**
   - Session cookie: no `Expires/Max-Age`, deleted when browser closes. Persistent: has an expiry date.

---

## 8. SSO — Single Sign-On

### What Is SSO?

SSO allows users to **log in once** and access **multiple applications** without re-entering credentials.

### How SSO Works (Simplified)

```
User → App A → Redirect to Identity Provider (IdP)
IdP → Authenticates user → Issues token/ticket
User → Redirected back to App A with token
App A → Validates token → User is authenticated
User → Goes to App B → App B checks IdP → Already logged in → Access granted
```

### Common SSO Protocols

| Protocol | Description |
|---|---|
| **SAML 2.0** | XML-based, enterprise-focused. Common in B2B. |
| **OpenID Connect (OIDC)** | Built on OAuth 2.0. JSON/JWT-based. Modern web/mobile. |
| **CAS** | Central Authentication Service, older protocol. |

### Components

- **Identity Provider (IdP)**: Authenticates the user (e.g., Okta, Auth0, Google Workspace).
- **Service Provider (SP)**: The application that trusts the IdP.
- **Token/Assertion**: Proof of authentication passed from IdP to SP.

### Interview Questions

1. **What is SSO and what problem does it solve?**
   - Allows one login to access multiple apps, reducing password fatigue and centralizing authentication management.

2. **What is the difference between SSO and OAuth?**
   - SSO is a user experience concept (log in once, access many). OAuth is an authorization framework. SSO is often implemented using OAuth + OIDC.

3. **What is the difference between SAML and OIDC?**
   - SAML: XML-based, older, enterprise. OIDC: JSON/JWT-based, modern, mobile-friendly.

4. **What is an Identity Provider?**
   - A trusted system that authenticates users and issues tokens/assertions to service providers (e.g., Okta, Azure AD).

5. **What are the security risks of SSO?**
   - Single point of failure: if IdP is compromised, all apps are at risk. Requires strong IdP security.

6. **How does logout work in SSO (Single Logout)?**
   - SLO (Single Log Out): logging out from one app notifies the IdP, which propagates the logout to all connected apps.

---

## 9. OAuth 2.0

### What Is OAuth 2.0?

OAuth 2.0 is an **authorization framework** that allows third-party applications to access resources on behalf of a user **without sharing the user's password**.

### Roles

| Role | Description |
|---|---|
| **Resource Owner** | The user who owns the data |
| **Client** | The third-party app requesting access |
| **Authorization Server** | Issues access tokens (e.g., Google's OAuth server) |
| **Resource Server** | API that holds the data (accepts access tokens) |

### Grant Types

| Grant Type | Use Case |
|---|---|
| **Authorization Code** | Web apps (most secure, uses PKCE for SPAs/mobile) |
| **Client Credentials** | Machine-to-machine (no user) |
| **Implicit** | Deprecated. Was used for SPAs. |
| **Resource Owner Password** | Legacy only; avoid — shares user credentials with client |

### Authorization Code Flow

```
1. User clicks "Login with Google"
2. Client redirects to: GET /authorize?client_id=...&redirect_uri=...&scope=...&state=...
3. User authenticates and grants consent
4. Auth server redirects back: /callback?code=AUTH_CODE&state=...
5. Client exchanges code: POST /token {code, client_id, client_secret}
6. Auth server returns: { access_token, refresh_token, expires_in }
7. Client uses access_token to call the Resource Server API
```

### Interview Questions

1. **What is OAuth 2.0 and what problem does it solve?**
   - An authorization framework letting users grant apps limited access to their data without sharing passwords.

2. **What is the difference between OAuth and OpenID Connect?**
   - OAuth handles authorization (what you can do). OIDC adds authentication (who you are) on top of OAuth using an ID token.

3. **Why is the Authorization Code flow preferred over Implicit?**
   - The authorization code is exchanged server-side for a token, never exposing the token in the URL. Implicit put tokens in URLs (browser history, logs).

4. **What is PKCE and why is it needed?**
   - Proof Key for Code Exchange. Protects against authorization code interception in public clients (SPAs, mobile apps) that cannot store a client secret.

5. **What is the difference between an access token and a refresh token?**
   - Access token: short-lived, used to access APIs. Refresh token: long-lived, used to get new access tokens without re-login.

6. **What are OAuth scopes?**
   - Scopes define what level of access is granted (e.g., `read:email`, `write:calendar`).

7. **What happens when an access token expires?**
   - The client uses the refresh token to request a new access token from the Authorization Server.

---

## 10. JWT — JSON Web Token

### What Is a JWT?

A JWT is a **compact, URL-safe token** containing encoded claims that can be **verified without a database lookup**.

### Structure

```
header.payload.signature

eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9   ← Base64 encoded header
.eyJzdWIiOiIxMjM0IiwibmFtZSI6IkpvaG4ifQ  ← Base64 encoded payload
.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQ  ← HMAC signature
```

**Header**: `{ "alg": "HS256", "typ": "JWT" }`  
**Payload**: `{ "sub": "1234", "name": "John", "iat": 1516239022 }`  
**Signature**: `HMACSHA256(base64(header) + "." + base64(payload), secret)`

### Stateless Authentication Flow

```
1. User logs in → server creates JWT with user data, signs it
2. Server returns JWT to client (stored in memory or HttpOnly cookie)
3. Client sends JWT in Authorization header: Bearer <token>
4. Server verifies signature (no DB lookup needed) → authenticates user
```

### JWT vs Session Token

| | JWT | Session Token |
|---|---|---|
| State | Stateless | Stateful (stored in DB/cache) |
| Scalability | Scales easily | Requires shared session store |
| Revocation | Hard (until expiry) | Easy (delete from DB) |
| Size | Larger | Small opaque string |

### Interview Questions

1. **What is a JWT and how does it work?**
   - A self-contained token with encoded user data and a cryptographic signature. The server verifies the signature without a DB lookup.

2. **What are the three parts of a JWT?**
   - Header (algorithm), Payload (claims/data), Signature (verification).

3. **Is the payload of a JWT encrypted?**
   - No, it is Base64 encoded (not encrypted). Anyone can decode it. Never store sensitive data in the payload unless you use JWE (JSON Web Encryption).

4. **How do you revoke a JWT?**
   - JWTs cannot be revoked before expiry by default. Solutions: short expiry + refresh tokens, token blacklist in Redis, or rotating signing secrets.

5. **What is the difference between HS256 and RS256?**
   - HS256: symmetric (one shared secret). RS256: asymmetric (private key signs, public key verifies). RS256 allows the resource server to verify without the secret.

6. **What are JWT claims?**
   - Standard registered claims: `iss` (issuer), `sub` (subject), `exp` (expiry), `iat` (issued at). Custom claims carry app-specific data.

7. **Why should JWTs have a short expiry?**
   - If a token is stolen, the damage window is limited. Short-lived tokens are combined with refresh tokens.

8. **Where should you store a JWT in a browser?**
   - HttpOnly cookie (preferred, XSS-safe) or in-memory. Avoid `localStorage` — vulnerable to XSS.

---

## 11. Laravel Sanctum — Is It Stateless?

### What Is Laravel Sanctum?

Sanctum provides authentication for SPAs, mobile apps, and simple token-based APIs.

### Two Authentication Mechanisms

| Mechanism | How It Works | Stateless? |
|---|---|---|
| **SPA Cookie Authentication** | Uses Laravel's session + CSRF cookies | **Stateful** |
| **API Token Authentication** | Issues tokens stored in `personal_access_tokens` table | **Stateful** (token in DB) |

### Answer: Sanctum Is NOT Purely Stateless

- **SPA Auth**: Session-based → stateful.
- **Token Auth**: Token is hashed and stored in database → stateful (DB lookup on every request).

For true stateless JWT authentication in Laravel, use **Laravel Passport with JWTs** or a dedicated JWT library (`tymon/jwt-auth`).

### Interview Questions

1. **What is Laravel Sanctum used for?**
   - Lightweight authentication for SPAs, mobile apps, and simple API tokens.

2. **Is Laravel Sanctum stateless?**
   - No. SPA auth uses sessions (stateful). API tokens are stored in the database and looked up on every request (stateful).

3. **What is the difference between Sanctum and Passport?**
   - Sanctum: simple, lightweight, session/token auth for first-party apps. Passport: full OAuth 2.0 server for third-party authorization.

4. **How does Sanctum's SPA authentication work?**
   - It uses Laravel's session cookie after a CSRF cookie handshake. The SPA must be on the same top-level domain or a subdomain.

5. **What is the `personal_access_tokens` table in Sanctum?**
   - Stores hashed API tokens. Sanctum hashes the token before storage and compares hashes on each request.

---

## 12. Basic Token Authentication

### What Is Basic Authentication?

HTTP Basic Auth sends credentials as a **Base64-encoded `username:password`** string in the `Authorization` header.

```
Authorization: Basic dXNlcjpwYXNzd29yZA==
// Decoded: user:password
```

### What Is a Bearer Token?

A Bearer token is a simple opaque string (or JWT) sent in the `Authorization` header.

```
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
```

### Comparison

| | Basic Auth | Bearer Token | JWT |
|---|---|---|---|
| Credential exposure | Password each request | Token only | Self-contained |
| Revocation | Via password change | Token blacklist/expiry | Difficult |
| Stateless | No | Can be | Yes |
| Security | Requires HTTPS | Requires HTTPS | Requires HTTPS |

### Interview Questions

1. **What is HTTP Basic Authentication?**
   - Sends `username:password` Base64-encoded in every request. Simple but requires HTTPS to be secure.

2. **Is Base64 encoding the same as encryption?**
   - No. Base64 is encoding (reversible, not secret). It must be used over HTTPS to prevent interception.

3. **What is the difference between Basic Auth and Bearer Token?**
   - Basic Auth sends credentials every request. Bearer Token sends a pre-issued token, keeping credentials away from the transport layer.

4. **How are API keys different from Bearer tokens?**
   - API keys are typically long-lived opaque strings for machine-to-machine auth. Bearer tokens (often JWTs) are short-lived user auth tokens.

---

## 13. N+1 Problem in Laravel

### What Is the N+1 Problem?

When fetching N records and executing 1 additional query **per record** to get related data — total: **N+1 queries**.

### Example

```php
// N+1 Problem
$posts = Post::all(); // 1 query
foreach ($posts as $post) {
    echo $post->author->name; // 1 query per post = N queries
}
// Total: 1 + N queries

// Solution: Eager Loading
$posts = Post::with('author')->get(); // 2 queries total
foreach ($posts as $post) {
    echo $post->author->name; // No extra query
}
```

### Eager Loading Methods

```php
Post::with('author')->get();                    // eager load
Post::with(['author', 'comments'])->get();      // multiple
Post::with('comments.likes')->get();            // nested
Post::withCount('comments')->get();             // count only
Post::load('author');                           // lazy eager loading on existing collection
```

### Detection

```php
// In AppServiceProvider (development only)
DB::listen(function ($query) {
    logger($query->sql);
});

// Or use Laravel Debugbar / Telescope
```

### Interview Questions

1. **What is the N+1 query problem?**
   - Loading a collection and then firing one additional query per item for a relationship, resulting in N+1 total queries.

2. **How does eager loading solve N+1?**
   - It loads all related data in a single query (or batched queries) upfront, rather than per-item.

3. **What is the difference between `with()` and `load()`?**
   - `with()`: eager loading at query time. `load()`: lazy eager loading on an already-retrieved collection.

4. **How do you detect N+1 issues in Laravel?**
   - Laravel Debugbar, Laravel Telescope, `DB::listen()`, or `preventLazyLoading()` in development.

5. **What is `Model::preventLazyLoading()`?**
   - A development helper in Laravel that throws an exception when lazy loading is attempted, forcing developers to use eager loading.

6. **Can you still get N+1 with `with()`?**
   - Yes, if you access a deeply nested relationship that wasn't included in the `with()` chain.

---

## 14. Debugging a Slow API

### Step-by-Step Approach

#### 1. Measure and Identify

- Check response time with APM tools (New Relic, Datadog, Laravel Telescope).
- Use `EXPLAIN` on slow queries.
- Check slow query log in MySQL.

```sql
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 1; -- log queries > 1 second
```

#### 2. Common Causes and Fixes

| Cause | Detection | Fix |
|---|---|---|
| N+1 queries | Debugbar query count | Eager loading |
| Missing index | `EXPLAIN` shows full table scan | Add index |
| Unoptimized query | Slow query log | Rewrite query, add composite index |
| Inefficient code | Profiling (Blackfire, Xdebug) | Refactor algorithms |
| External API calls | Trace logs | Add caching, async processing |
| Memory issues | PHP logs, OOM errors | Chunking, streaming |
| No caching | Cache miss metrics | Add Redis/Memcached caching |
| Lack of pagination | Large result sets | Paginate, cursor pagination |

#### 3. Laravel-Specific Debugging

```php
// Profile query time
DB::enableQueryLog();
// ... your code
dd(DB::getQueryLog());

// Use Telescope
// Use Debugbar in local
```

#### 4. Optimization Techniques

- **Database**: indexes, query optimization, read replicas.
- **Caching**: cache expensive queries/computations in Redis.
- **Queues**: move slow tasks to background jobs.
- **CDN**: cache static assets and API responses.
- **Chunking**: process large datasets in chunks.
- **Pagination**: never return unbounded result sets.

### Interview Questions

1. **How do you approach debugging a slow API?**
   - Reproduce the issue, measure with APM, identify bottleneck (DB, code, external), apply targeted fix, validate improvement.

2. **What tools do you use to profile Laravel applications?**
   - Laravel Telescope, Debugbar, Blackfire, Xdebug, New Relic.

3. **How do you optimize a slow database query?**
   - Run EXPLAIN, add indexes, rewrite query, use covering index, avoid SELECT *, paginate results.

4. **When would you use caching vs a database index?**
   - Index: speed up reads from the DB. Cache: avoid hitting the DB at all for repeated data.

5. **What is a read replica and how does it help?**
   - A copy of the primary DB that handles SELECT queries, reducing load on the write instance.

6. **How do you handle slow third-party API calls?**
   - Cache the response, use background jobs/queues, add timeouts, implement circuit breakers.

7. **What is chunking and when do you use it?**
   - Processing a large dataset in smaller batches to avoid memory exhaustion.
   ```php
   User::chunk(1000, function ($users) { /* process */ });
   ```

---

## 15. Interface

### What Is an Interface?

An interface defines a **contract** — a set of method signatures that a class **must implement**. It contains no implementation.

```php
interface PaymentGateway
{
    public function charge(float $amount): bool;
    public function refund(string $transactionId): bool;
}

class StripeGateway implements PaymentGateway
{
    public function charge(float $amount): bool { /* ... */ }
    public function refund(string $transactionId): bool { /* ... */ }
}
```

### Key Points

- A class can implement **multiple interfaces**.
- All methods must be `public`.
- No properties, no implementations.
- Cannot be instantiated.

### Interview Questions

1. **What is an interface in OOP?**
   - A contract defining method signatures that implementing classes must fulfill. No implementation is provided.

2. **Can a class implement multiple interfaces in PHP?**
   - Yes: `class Foo implements InterfaceA, InterfaceB {}`

3. **What is the difference between an interface and an abstract class?**
   - Interface: only method signatures, no implementation, multiple inheritance allowed. Abstract class: can have implementations, properties, single inheritance only.

4. **When would you use an interface vs an abstract class?**
   - Interface: when multiple unrelated classes need to conform to a contract. Abstract class: when sharing common implementation among related classes.

5. **What is "programming to an interface"?**
   - Coding against the interface type, not a concrete class, so implementations can be swapped without changing the calling code.

---

## 16. Abstract Class

### What Is an Abstract Class?

An abstract class is a **partially implemented class** that cannot be instantiated. It may define both abstract methods (must be implemented by subclasses) and concrete methods.

```php
abstract class BaseRepository
{
    abstract protected function getModel(): string;

    public function findById(int $id)
    {
        $model = $this->getModel();
        return $model::find($id);
    }
}

class UserRepository extends BaseRepository
{
    protected function getModel(): string
    {
        return User::class;
    }
}
```

### Interface vs Abstract Class

| | Interface | Abstract Class |
|---|---|---|
| Implementation | None allowed | Can have concrete methods |
| Properties | Not allowed | Allowed |
| Inheritance | Multiple | Single |
| Constructor | Not allowed | Allowed |
| Access modifiers | Only public | Any |

### Interview Questions

1. **What is an abstract class?**
   - A class that cannot be instantiated and may contain abstract methods that subclasses must implement.

2. **Can an abstract class have a constructor?**
   - Yes. It's called when the subclass is instantiated via `parent::__construct()`.

3. **Can a class extend multiple abstract classes?**
   - No. PHP only supports single class inheritance.

4. **What happens if a subclass doesn't implement all abstract methods?**
   - PHP throws a fatal error: the subclass must also be declared abstract.

5. **Give a real-world example of when to use an abstract class.**
   - A `BaseController` or `BaseRepository` with shared logic (authentication checks, common queries) while leaving specific methods to subclasses.

---

## 17. Trait

### What Is a Trait?

A Trait is a **reusable code inclusion mechanism** in PHP. It allows sharing methods across multiple classes without inheritance.

```php
trait Auditable
{
    public function createdBy(): string
    {
        return auth()->user()->name;
    }

    public function logChange(string $action): void
    {
        Log::info("{$this->createdBy()} performed {$action}");
    }
}

class Post extends Model
{
    use Auditable;
}
```

### Key Points

- Solves the **multiple inheritance problem** in PHP.
- A class can `use` multiple traits.
- Traits can have properties and methods (but not constants in older PHP).
- Conflict resolution: `insteadof` and `as`.

```php
class MyClass
{
    use TraitA, TraitB {
        TraitA::hello insteadof TraitB;
        TraitB::hello as helloB;
    }
}
```

### Interview Questions

1. **What is a Trait in PHP?**
   - A mechanism for code reuse that allows adding methods to classes without inheritance.

2. **What is the difference between a Trait and an Interface?**
   - Interface: contract (no code). Trait: actual implementation to be included in classes.

3. **What is the difference between a Trait and inheritance?**
   - Inheritance creates an is-a relationship. Traits are horizontal code sharing — no relationship implied.

4. **How do you resolve conflicts when two traits have the same method name?**
   - Use `insteadof` to choose one, and `as` to alias the other.

5. **Can a Trait have properties?**
   - Yes. But if a class and a trait both define the same property, there must be compatibility.

6. **What happens if a Trait and the using class define the same method?**
   - The class method takes precedence over the trait method.

---

## 18. Log Monitoring

### Why Log Monitoring?

Logs are the **observability backbone** of a system. They help detect errors, trace requests, audit actions, and debug incidents.

### Log Levels (RFC 5424 / PSR-3)

| Level | Use |
|---|---|
| `emergency` | System unusable |
| `alert` | Immediate action needed |
| `critical` | Critical conditions |
| `error` | Runtime errors |
| `warning` | Non-critical issues |
| `notice` | Normal but significant |
| `info` | General information |
| `debug` | Verbose debug info |

### In Laravel

```php
Log::info('User logged in', ['user_id' => $user->id]);
Log::error('Payment failed', ['order_id' => $order->id, 'error' => $e->getMessage()]);
```

### Tools

| Tool | Purpose |
|---|---|
| **ELK Stack** (Elasticsearch + Logstash + Kibana) | Centralized log aggregation and visualization |
| **Grafana + Loki** | Log aggregation with Grafana dashboards |
| **Datadog** | APM + log management (SaaS) |
| **Sentry** | Error tracking with context |
| **Laravel Telescope** | In-app request/query/job logging |
| **Papertrail** | Simple cloud logging |

### Best Practices

- Use structured logging (JSON format) for easy parsing.
- Include context: user ID, request ID, trace ID.
- Never log sensitive data (passwords, tokens, PII).
- Set up alerts on error rate spikes.
- Use log rotation to avoid disk fill-up.

### Interview Questions

1. **What is the difference between logging and monitoring?**
   - Logging: recording events. Monitoring: watching metrics and alerting on anomalies. Logs feed into monitoring.

2. **What are PSR-3 log levels?**
   - emergency, alert, critical, error, warning, notice, info, debug.

3. **What is structured logging?**
   - Logging in a machine-parseable format (JSON) with consistent fields, enabling easy searching and alerting.

4. **How would you track a bug that only happens in production?**
   - Use structured logs with request/trace IDs, error tracking (Sentry), and APM to reproduce the exact execution path.

5. **What is log rotation?**
   - Periodically archiving and deleting old log files to prevent disk exhaustion.

6. **What is distributed tracing?**
   - Tracking a request across multiple services using a shared trace ID (tools: Jaeger, Zipkin, Datadog APM).

---

## 19. Service Container

### What Is a Service Container?

Laravel's service container is an **IoC (Inversion of Control) container** — a powerful tool for managing class dependencies and performing dependency injection.

### How It Works

```php
// Binding
app()->bind(PaymentGateway::class, StripeGateway::class);

// Singleton binding (same instance every time)
app()->singleton(PaymentGateway::class, StripeGateway::class);

// Resolving
$gateway = app(PaymentGateway::class); // returns StripeGateway instance

// Auto-resolution via type hints
class OrderController
{
    public function __construct(private PaymentGateway $gateway) {}
}
// Laravel automatically resolves PaymentGateway from the container
```

### Binding Types

| Type | Description |
|---|---|
| `bind()` | New instance every resolution |
| `singleton()` | Same instance every resolution |
| `instance()` | Bind an existing object |
| `scoped()` | New instance per request lifecycle |

### Interview Questions

1. **What is the Service Container in Laravel?**
   - An IoC container that manages class instantiation and dependency injection automatically.

2. **What is Inversion of Control?**
   - Instead of a class creating its own dependencies, the container injects them — inverting control of instantiation.

3. **What is the difference between `bind()` and `singleton()`?**
   - `bind()` creates a new instance each time. `singleton()` creates once and returns the same instance.

4. **How does automatic dependency resolution work in Laravel?**
   - Laravel uses PHP's Reflection API to inspect constructor type hints and resolves them from the container automatically.

5. **What is the difference between the Service Container and a Service Provider?**
   - Service Container: the actual IoC container. Service Provider: bootstrap code that registers bindings into the container.

6. **What is `app()->make()` vs `app()`?**
   - Both resolve from the container. `app(PaymentGateway::class)` is shorthand for `app()->make(PaymentGateway::class)`.

---

## 20. Message Broker

### What Is a Message Broker?

A message broker is **middleware** that enables asynchronous communication between services by routing messages from producers to consumers.

### Core Concepts

| Concept | Description |
|---|---|
| **Producer** | Sends messages to the broker |
| **Consumer** | Receives and processes messages |
| **Queue** | FIFO buffer; each message consumed by ONE consumer |
| **Topic/Exchange** | Pub/Sub; message broadcast to MANY consumers |
| **Dead Letter Queue** | Where failed messages go |

### Popular Brokers

| Broker | Best For |
|---|---|
| **RabbitMQ** | Complex routing, reliable delivery, task queues |
| **Redis (Queue)** | Simple fast queues, already using Redis |
| **Amazon SQS** | AWS-native, serverless queue |
| **Apache Kafka** | High-throughput event streaming, log replay |
| **Laravel Horizon** | Monitoring Redis queues in Laravel |

### In Laravel

```php
// Dispatch a job
ProcessPayment::dispatch($order)->onQueue('payments');

// Job class
class ProcessPayment implements ShouldQueue
{
    public function handle(): void
    {
        // async processing
    }
}
```

### Interview Questions

1. **What is a message broker and why is it useful?**
   - Middleware enabling async communication between services, decoupling producers from consumers.

2. **What is the difference between a queue and a pub/sub topic?**
   - Queue: each message delivered to one consumer. Pub/Sub topic: message broadcast to all subscribers.

3. **What is a Dead Letter Queue?**
   - A queue where messages are sent after repeated processing failures, for inspection and retry.

4. **What is the difference between RabbitMQ and Kafka?**
   - RabbitMQ: traditional message broker, complex routing, messages deleted after consumed. Kafka: event log, messages retained, consumers track offset — great for event replay and streaming.

5. **How do Laravel queues relate to a message broker?**
   - Laravel queues use drivers (database, Redis, SQS) as the broker. Workers consume jobs from the queue asynchronously.

6. **What is at-least-once delivery vs exactly-once?**
   - At-least-once: message may be redelivered; consumers must be idempotent. Exactly-once: harder to guarantee; requires distributed transactions.

7. **What is backpressure in message queues?**
   - When consumers are slower than producers, messages accumulate. Backpressure mechanisms slow producers to prevent queue overflow.

---

## 21. Singleton Pattern

### What Is Singleton?

The Singleton pattern ensures a class has **only one instance** and provides a global access point to it.

```php
class DatabaseConnection
{
    private static ?DatabaseConnection $instance = null;

    private function __construct(private \PDO $pdo) {}

    public static function getInstance(): static
    {
        if (static::$instance === null) {
            static::$instance = new static(new \PDO('mysql:host=localhost', 'user', 'pass'));
        }
        return static::$instance;
    }

    // Prevent cloning
    private function __clone() {}
}

$db = DatabaseConnection::getInstance();
```

### In Laravel

Laravel's container `singleton()` implements this pattern:

```php
app()->singleton(DatabaseManager::class, fn() => new DatabaseManager());
```

### When to Use Singleton

- Database connection pools
- Configuration objects
- Logger instances
- Cache managers

### Interview Questions

1. **What is the Singleton pattern?**
   - Ensures a class has only one instance and provides a global access point to it.

2. **What are the downsides of Singleton?**
   - Global state (hard to test), hidden dependencies, tight coupling, concurrency issues.

3. **How do you make a Singleton thread-safe in PHP?**
   - PHP is mostly request-isolated (one thread per request in traditional setup), but in async contexts (Swoole), use locks/mutexes.

4. **What is the difference between Singleton and static class?**
   - Singleton is an object (can implement interfaces, be injected). Static class is a collection of static methods (cannot be injected).

5. **How does Laravel's `singleton()` binding relate to the Singleton pattern?**
   - It registers a class to be instantiated once per container lifetime, effectively implementing the Singleton for that binding.

---

## 22. SQL Joins

### Types of Joins

```sql
-- INNER JOIN: only matching rows in both tables
SELECT u.name, o.amount
FROM users u
INNER JOIN orders o ON u.id = o.user_id;

-- LEFT JOIN: all rows from left + matching from right (NULL if no match)
SELECT u.name, o.amount
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;

-- RIGHT JOIN: all rows from right + matching from left
SELECT u.name, o.amount
FROM users u
RIGHT JOIN orders o ON u.id = o.user_id;

-- FULL OUTER JOIN: all rows from both (MySQL uses UNION)
SELECT u.name, o.amount FROM users u LEFT JOIN orders o ON u.id = o.user_id
UNION
SELECT u.name, o.amount FROM users u RIGHT JOIN orders o ON u.id = o.user_id;

-- CROSS JOIN: cartesian product (every combination)
SELECT u.name, p.name FROM users u CROSS JOIN products p;

-- SELF JOIN: join a table with itself
SELECT e.name, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;
```

### Visual Representation

```
INNER JOIN       LEFT JOIN        RIGHT JOIN      FULL OUTER
  A ∩ B           A + (A∩B)       B + (A∩B)       A ∪ B
```

### Interview Questions

1. **What is the difference between INNER JOIN and LEFT JOIN?**
   - INNER: only rows with matches in both tables. LEFT: all rows from left table, NULLs for missing right matches.

2. **What is a self join?**
   - Joining a table to itself. Common for hierarchical data (employees and their managers in the same table).

3. **What is a cross join?**
   - Returns the Cartesian product — every combination of rows from both tables.

4. **How do you find users who have no orders?**
   ```sql
   SELECT u.* FROM users u LEFT JOIN orders o ON u.id = o.user_id WHERE o.id IS NULL;
   ```

5. **What is the difference between `WHERE` and `HAVING`?**
   - `WHERE` filters before grouping. `HAVING` filters after `GROUP BY`.

6. **How does indexing affect JOIN performance?**
   - Indexing the JOIN column (especially the foreign key) dramatically reduces the rows scanned during the join.

7. **What is a hash join vs a nested loop join?**
   - Nested loop: iterate outer rows, probe inner for each — good for small datasets with indexes. Hash join: build hash table from one table, probe with other — good for large unindexed joins.

---

## 23. Laravel Request Lifecycle

### Step-by-Step Flow

```
HTTP Request
    │
    ▼
public/index.php          ← Entry point, loads autoloader + app
    │
    ▼
Bootstrap (bootstrap/app.php)
    │
    ▼
HTTP Kernel (App\Http\Kernel)
    │
    ├── Global Middleware (e.g., TrustProxies, HandleCors)
    │
    ▼
Service Providers (boot all registered providers)
    │
    ▼
Router (routes/web.php or api.php)
    │
    ├── Route Middleware (auth, throttle, etc.)
    │
    ▼
Controller / Closure
    │
    ▼
Response
    │
    ▼
Kernel::terminate() (terminable middleware)
```

### Key Components

- **`public/index.php`**: Single entry point.
- **`bootstrap/app.php`**: Creates the Application (IoC container) and binds Kernel.
- **Kernel**: Defines middleware stacks.
- **Service Providers**: Register bindings, event listeners, routes.
- **Router**: Matches request URL to handler.

### Interview Questions

1. **What is the entry point of a Laravel application?**
   - `public/index.php` — every HTTP request goes through this file.

2. **What happens in `bootstrap/app.php`?**
   - Creates the Laravel Application (IoC container) and binds the HTTP Kernel.

3. **What is the role of the HTTP Kernel?**
   - Runs global middleware and delegates the request to the router.

4. **When do Service Providers run?**
   - During the bootstrap phase, before the request reaches the router.

5. **What is the difference between `register()` and `boot()` in a Service Provider?**
   - `register()`: bind things into the container. `boot()`: run after all providers are registered; use bindings here.

6. **What is terminable middleware?**
   - Middleware with a `terminate()` method that runs after the response is sent to the browser.

---

## 24. Service Provider in Laravel

### What Is a Service Provider?

Service Providers are the **central place to bootstrap** your application — registering container bindings, event listeners, middleware, routes, and more.

```php
class PaymentServiceProvider extends ServiceProvider
{
    public function register(): void
    {
        // Bind interface to implementation
        $this->app->bind(PaymentGateway::class, StripeGateway::class);
    }

    public function boot(): void
    {
        // Register routes, events, views, etc.
        Route::prefix('payments')->group(base_path('routes/payment.php'));
    }
}
```

Register in `config/app.php` under `providers`.

### Deferred Providers

Providers can be **deferred** (lazy-loaded) — only bootstrapped when their binding is first resolved.

```php
class ReportServiceProvider extends ServiceProvider
{
    protected $defer = true;

    public function provides(): array
    {
        return [ReportGenerator::class];
    }
}
```

### Interview Questions

1. **What is a Service Provider in Laravel?**
   - Bootstrap classes where you register container bindings, routes, events, and other application services.

2. **What is the difference between `register()` and `boot()`?**
   - `register()`: bind into container (no other bindings available yet). `boot()`: run after all providers registered.

3. **What is a deferred service provider?**
   - Loaded lazily — only when the binding it provides is first requested. Improves performance.

4. **When would you create a custom service provider?**
   - When packaging reusable functionality (payment, notifications), registering complex bindings, or bootstrapping third-party services.

5. **How does Laravel know which providers to load?**
   - Via the `providers` array in `config/app.php` (or auto-discovery via `composer.json`'s `extra.laravel.providers`).

---

## 25. SOLID Principles

### S — Single Responsibility Principle

> A class should have only **one reason to change**.

```php
// Bad: UserController handles auth, profile, email
// Good: separate AuthController, ProfileController, MailService
```

### O — Open/Closed Principle

> Open for extension, **closed for modification**.

```php
// Bad: if/else for each payment type
// Good: PaymentGateway interface, new class per method
```

### L — Liskov Substitution Principle

> Subclasses must be **substitutable** for their base class.

```php
// Bad: Square extends Rectangle but breaks setWidth() semantics
// Good: shape hierarchy with separate implementations
```

### I — Interface Segregation Principle

> Clients should not be forced to depend on interfaces they **don't use**.

```php
// Bad: one giant UserInterface with 20 methods
// Good: Authenticatable, Notifiable, Billable separate interfaces
```

### D — Dependency Inversion Principle

> Depend on **abstractions**, not concrete implementations.

```php
// Bad: new StripeGateway() inside OrderService
// Good: inject PaymentGateway interface via constructor
class OrderService {
    public function __construct(private PaymentGateway $gateway) {}
}
```

### Interview Questions

1. **What does Single Responsibility mean in practice?**
   - Each class does one job. A User model shouldn't send emails — delegate to a MailService.

2. **How does Open/Closed prevent bugs?**
   - Adding new behavior via new classes (not modifying existing ones) avoids breaking existing functionality.

3. **Can you give an example of Liskov Substitution violation?**
   - A `ReadOnlyFile` extends `File` but throws on `write()` — violates the contract of `File`.

4. **What is the problem with fat interfaces?**
   - Classes are forced to implement methods they don't need, creating meaningless stubs.

5. **How does Dependency Inversion improve testability?**
   - You can inject a mock implementation (e.g., `FakePaymentGateway`) in tests without changing the class under test.

6. **What is the difference between Dependency Injection and Dependency Inversion?**
   - DI is a technique (passing dependencies in). DIP is a principle (depend on abstractions). DI is a common way to implement DIP.

---

## 26. Form Security

### Key Threats and Mitigations

#### CSRF (Cross-Site Request Forgery)

A malicious site tricks a user's browser into submitting a form to your server.

```php
// Laravel: add @csrf to all forms
<form method="POST">
    @csrf
    ...
</form>

// Laravel verifies the token in VerifyCsrfToken middleware
```

#### XSS (Cross-Site Scripting)

Injecting malicious scripts through form inputs.

```php
// Always escape output
{{ $user->name }}          // Blade auto-escapes HTML
{!! $user->bio !!}         // Raw — dangerous if user-controlled

// Validate and sanitize input
$request->validate(['name' => 'string|max:255']);
```

#### SQL Injection

```php
// Bad
DB::select("SELECT * FROM users WHERE email = '$email'");

// Good: parameterized queries
User::where('email', $email)->first();
DB::select('SELECT * FROM users WHERE email = ?', [$email]);
```

#### Mass Assignment

```php
// Guard your models
protected $fillable = ['name', 'email'];
// or
protected $guarded = ['is_admin'];
```

### Interview Questions

1. **What is CSRF and how does Laravel prevent it?**
   - Cross-Site Request Forgery — a malicious form submission using the user's session. Laravel generates and validates a token per session.

2. **What is the difference between XSS and CSRF?**
   - XSS injects code that runs in the victim's browser. CSRF tricks the browser into making requests using the victim's credentials.

3. **What is SQL injection and how do you prevent it?**
   - Injecting SQL via input fields. Prevent with parameterized queries/prepared statements or an ORM.

4. **What is mass assignment vulnerability?**
   - Allowing user input to fill any model attribute, potentially setting `is_admin = true`. Guard with `$fillable`.

5. **Why is `{!! !!}` dangerous in Blade?**
   - It outputs raw HTML without escaping, allowing XSS if the content is user-controlled.

---

## 27. File Upload Security

### Key Risks

- **Malicious file execution**: uploading `.php` or `.sh` files.
- **Directory traversal**: `../../etc/passwd` in filename.
- **MIME type spoofing**: renaming `malware.php` to `image.jpg`.
- **Oversized files**: denial of service via large uploads.

### Secure Upload Checklist

```php
$request->validate([
    'file' => 'required|file|mimes:jpg,png,pdf|max:2048', // 2MB
]);

// Store outside web root
$path = $request->file('file')->store('uploads', 'private');

// Generate a random filename — never trust user filename
$filename = Str::random(40) . '.' . $request->file('file')->extension();

// Validate actual MIME type (not just extension)
$mimeType = $request->file('file')->getMimeType();

// Serve files via controller, not directly
return response()->file(storage_path('app/private/' . $filename));
```

### Best Practices

- Store uploads **outside the public directory**.
- Never use the original filename.
- Scan uploads with antivirus (e.g., ClamAV).
- Set proper `Content-Disposition` and `X-Content-Type-Options` headers.
- Limit file size in both application and web server config.
- Disable script execution in the upload directory (`php_flag engine off` in Apache).

### Interview Questions

1. **What is the most dangerous type of file upload attack?**
   - Uploading a PHP/shell script that gets executed by the web server.

2. **Why shouldn't you trust the MIME type sent by the browser?**
   - The browser-supplied MIME type can be spoofed. Validate the actual file content (magic bytes) server-side.

3. **How do you prevent directory traversal in file uploads?**
   - Generate a new random filename; never use user-provided filenames for storage paths.

4. **Why should uploaded files be stored outside the web root?**
   - Files outside the public directory cannot be accessed directly via URL, preventing direct execution.

5. **What does `mimes` validation check in Laravel?**
   - It checks the file extension AND the actual MIME type based on file content, not just the extension.

---

## 28. API Security

### Key Security Concerns

| Threat | Mitigation |
|---|---|
| Broken authentication | Strong tokens, short expiry, token rotation |
| Excessive data exposure | Return only necessary fields |
| Broken authorization | Check ownership, use policies |
| Injection | Parameterized queries, input validation |
| Rate limiting abuse | Throttling, IP blocking |
| Insecure direct object reference (IDOR) | Authorize each resource access |
| Sensitive data exposure | HTTPS, mask PII in logs |
| Mass assignment | Whitelist allowed fields |

### Practical Measures

```php
// Always use HTTPS
// Validate all inputs
$request->validate(['amount' => 'required|numeric|min:0']);

// Authorization check (IDOR prevention)
$this->authorize('view', $order); // checks ownership via Policy

// Use API versioning
Route::prefix('api/v1')->group(...);

// Return minimal data (API Resources)
return new UserResource($user);

// Security headers
'X-Content-Type-Options' => 'nosniff'
'X-Frame-Options' => 'DENY'
'Content-Security-Policy' => "default-src 'self'"
```

### OWASP API Security Top 10

1. Broken Object Level Authorization (BOLA/IDOR)
2. Broken Authentication
3. Broken Object Property Level Authorization
4. Unrestricted Resource Consumption
5. Broken Function Level Authorization
6. Unrestricted Access to Sensitive Business Flows
7. Server Side Request Forgery (SSRF)
8. Security Misconfiguration
9. Improper Inventory Management
10. Unsafe Consumption of APIs

### Interview Questions

1. **What is IDOR and how do you prevent it?**
   - Insecure Direct Object Reference — accessing another user's resource by changing an ID. Prevent with authorization checks on every request.

2. **What is the OWASP API Security Top 10?**
   - A list of the most critical API security risks (BOLA, broken auth, rate limiting, etc.).

3. **How do you secure an API endpoint that returns sensitive data?**
   - HTTPS, authentication, authorization (ownership check), field filtering (API Resources), rate limiting, audit logging.

4. **What is SSRF?**
   - Server-Side Request Forgery: tricking the server into making requests to internal/external resources the client shouldn't access.

5. **What is the difference between authentication and authorization?**
   - Authentication: who are you? Authorization: what are you allowed to do?

6. **Why should you version your API?**
   - To introduce breaking changes without disrupting existing consumers.

---

## 29. System Design for 1M Users / 100K req/s

### High-Level Architecture

```
Clients
  │
  ▼
CDN (CloudFront / Cloudflare)  ← Static assets, edge caching
  │
  ▼
Load Balancer (AWS ALB / Nginx)  ← Distribute traffic
  │
  ├── App Server 1
  ├── App Server 2   ← Horizontal scaling (auto-scaling group)
  └── App Server N
       │
       ├── Cache (Redis Cluster)    ← Session, hot data
       ├── Message Queue (SQS/Kafka) ← Async tasks
       │
       ▼
  Primary DB (RDS/PostgreSQL)
       │
       ├── Read Replica 1   ← Read scaling
       └── Read Replica 2
```

### Scaling Strategies

| Problem | Solution |
|---|---|
| Single server bottleneck | Horizontal scaling + load balancer |
| DB read overload | Read replicas + Redis cache |
| DB write overload | Sharding + CQRS |
| Slow background tasks | Message queues + workers |
| Global latency | CDN + multi-region deployment |
| Session sharing across servers | Redis session store |
| File storage | S3-compatible object storage |

### Back-of-Envelope: 100K req/s

- 100K req/s = 8.64B req/day
- With 10ms avg response: need ~1000 concurrent connections
- Single server: ~1K–5K req/s → need 20–100 servers
- DB: ~50K queries/s → need connection pooling (PgBouncer) + read replicas + caching

### Interview Questions

1. **How would you design a system to handle 1 million users?**
   - CDN + Load Balancer + Stateless app servers (horizontal scaling) + Redis cache + DB read replicas + message queues for async tasks.

2. **How do you make your application stateless for horizontal scaling?**
   - Store sessions in Redis, use JWT for auth, store files on S3, use a shared DB — no local state on app servers.

3. **What is a load balancer and what algorithms does it use?**
   - Distributes traffic across servers. Algorithms: round-robin, least connections, IP hash, weighted.

4. **How does caching reduce load at scale?**
   - Serves repeated reads from memory (Redis) instead of hitting the DB, multiplying effective throughput.

5. **What is the CAP theorem?**
   - A distributed system can guarantee only two of: Consistency, Availability, Partition Tolerance.

6. **What is CQRS?**
   - Command Query Responsibility Segregation — separate read and write models/stores for independent scaling.

7. **What is the difference between vertical and horizontal scaling?**
   - Vertical: bigger machine. Horizontal: more machines. Horizontal is preferred for large scale (no single point of failure).

8. **How do you handle session management with multiple application servers?**
   - Use a centralized session store (Redis) so any server can validate any session.

9. **What is a CDN and how does it help?**
   - Content Delivery Network — caches content at edge nodes globally, reducing latency and server load.

10. **What is connection pooling and why is it important?**
    - Reusing database connections instead of creating a new one per request. Dramatically reduces DB connection overhead at scale.

---

## 30. Software Principles vs Design Patterns

### Software Principles

Software principles are **high-level guidelines** for writing good code — they tell you **what to aim for** but not exactly how.

**Examples:**
- SOLID (SRP, OCP, LSP, ISP, DIP)
- DRY (Don't Repeat Yourself)
- KISS (Keep It Simple, Stupid)
- YAGNI (You Aren't Gonna Need It)
- Law of Demeter (talk only to immediate neighbors)
- Separation of Concerns

### Design Patterns

Design patterns are **proven, reusable solutions** to commonly occurring problems in software design — they tell you **exactly how** to structure code for a specific situation.

**Categories:**

| Category | Patterns |
|---|---|
| **Creational** | Singleton, Factory, Abstract Factory, Builder, Prototype |
| **Structural** | Adapter, Decorator, Facade, Proxy, Composite |
| **Behavioral** | Observer, Strategy, Command, Iterator, Template Method |

### Key Differences

| | Principles | Design Patterns |
|---|---|---|
| Level | Abstract guidelines | Concrete solutions |
| Scope | Entire codebase | Specific problem |
| Prescription | What to aim for | How to implement |
| Examples | SOLID, DRY | Singleton, Observer |

### Interview Questions

1. **What is the difference between a principle and a design pattern?**
   - Principles are abstract guidelines (what to aim for). Patterns are concrete, proven solutions to specific problems.

2. **Is Singleton a principle or a pattern?**
   - A design pattern. It solves the specific problem of ensuring one instance.

3. **What is the DRY principle?**
   - Don't Repeat Yourself — avoid code duplication by extracting common logic into reusable abstractions.

4. **What is YAGNI?**
   - You Aren't Gonna Need It — don't implement features speculatively; only build what is currently needed.

5. **When does following a principle lead to applying a pattern?**
   - Following DIP (principle) often leads to applying the Strategy or Factory pattern (patterns) to inject dependencies.

6. **What is the Law of Demeter?**
   - A module should only interact with its immediate dependencies — "don't talk to strangers." Reduces coupling.

---

## 31. Race Condition

### What Is a Race Condition?

A race condition occurs when **two or more processes/threads access shared data concurrently**, and the final outcome depends on the **timing** of their execution.

### Classic Example

```
User A: reads balance = 100
User B: reads balance = 100
User A: subtracts 80, writes 20
User B: subtracts 80, writes 20  ← Balance should be -60 but is 20!
```

### Solutions

#### 1. Database Locking

```php
// Pessimistic Lock (lock row for update)
DB::transaction(function () use ($userId, $amount) {
    $wallet = Wallet::where('user_id', $userId)->lockForUpdate()->first();
    if ($wallet->balance >= $amount) {
        $wallet->decrement('balance', $amount);
    }
});

// Optimistic Lock (check version, retry if stale)
$wallet = Wallet::find($userId);
$affected = Wallet::where('id', $wallet->id)
    ->where('version', $wallet->version)
    ->update(['balance' => $wallet->balance - $amount, 'version' => $wallet->version + 1]);

if (!$affected) {
    throw new OptimisticLockException('Concurrent update detected');
}
```

#### 2. Atomic Operations (Redis)

```php
// Redis INCR/DECR are atomic
Redis::decr('user:1:balance');

// Redis transaction
Redis::multi();
Redis::get('balance');
Redis::exec();
```

#### 3. Database Transactions

```php
DB::transaction(function () {
    // All or nothing — serializable if isolation level set
});
```

#### 4. Queue / Serialization

```php
// Process one job at a time per user
ProcessPayment::dispatch($userId)->onQueue("user-{$userId}");
```

### Interview Questions

1. **What is a race condition?**
   - When concurrent processes read/write shared state in an unpredictable order, leading to incorrect results.

2. **What is the difference between optimistic and pessimistic locking?**
   - Pessimistic: lock the row before reading (prevents concurrent access). Optimistic: read without locking, check version on write, retry if changed.

3. **When would you use optimistic vs pessimistic locking?**
   - Optimistic: low contention (conflicts are rare). Pessimistic: high contention (conflicts are common, data integrity critical).

4. **How do database transactions help with race conditions?**
   - Transactions with appropriate isolation levels (SERIALIZABLE) prevent dirty reads, non-repeatable reads, and phantom reads.

5. **What is `lockForUpdate()` in Laravel?**
   - Adds `FOR UPDATE` to the SQL query, placing a pessimistic write lock on the selected rows until the transaction ends.

6. **How can Redis help prevent race conditions?**
   - Redis operations like INCR/DECR/SETNX are atomic. Redis also supports Lua scripting for complex atomic operations.

7. **What are database transaction isolation levels?**

| Level | Dirty Read | Non-Repeatable Read | Phantom Read |
|---|---|---|---|
| READ UNCOMMITTED | Yes | Yes | Yes |
| READ COMMITTED | No | Yes | Yes |
| REPEATABLE READ | No | No | Yes |
| SERIALIZABLE | No | No | No |

8. **What is a deadlock and how is it different from a race condition?**
   - Race condition: incorrect result due to timing. Deadlock: two processes each waiting for the other's lock — neither can proceed.
