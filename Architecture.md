
## 🧱 **1. What is Monolithic Architecture?**

A **Monolithic Architecture** is a single unified unit — all features, components, and logic are bundled together into one codebase and deployed as a single application.

### 🔧 Components in a Monolithic App:
- UI Layer
- Business Logic
- Data Access Layer
- All APIs
- Authentication, Logging, etc.

> ✅ All of them reside in one place and are compiled/deployed together.

### ✅ **Advantages of Monolithic Architecture**
- Simple to develop and deploy (especially for small teams)
- Easier debugging and testing (all in one place)
- No inter-service communication overhead
- Straightforward to manage initially

### ❌ **Disadvantages**
- Difficult to scale individual components
- Tight coupling — changing one module might affect others
- Slower development over time as codebase grows
- Difficult to adopt new technologies incrementally
- Deployment risk: one bug = entire app down

### 📘 **Example**
An e-commerce site where:
- Login system
- Product catalog
- Order processing
- Payment module
- Admin dashboard  
All are in **one project** (`ecommerce.jar`, `ecommerce.war` etc.).

---

## 🧩 **2. What is Microservices Architecture?**

**Microservices** breaks an application into smaller, independent services that:
- Run in isolation
- Communicate via APIs (mostly HTTP/REST or messaging queues)
- Can be developed and deployed independently

Each service is responsible for a specific business capability.

### 🔧 Example Services:
- User Service
- Product Service
- Order Service
- Inventory Service
- Payment Service

### ✅ **Advantages of Microservices**
- Independent scaling of services
- Easier to adopt different technologies per service
- Fault isolation — failure in one service won’t crash the whole system
- Faster development and deployment
- Easier for distributed teams to work simultaneously

### ❌ **Disadvantages**
- Increased complexity (DevOps, communication, monitoring)
- More effort in testing (integration testing required)
- Requires service discovery, API gateway, etc.
- Possible network latency due to inter-service calls
- Data consistency is harder to maintain

---

## 🔁 **Monolithic vs Microservices — Side-by-Side Comparison**

| Feature                     | **Monolithic**                             | **Microservices**                          |
|-----------------------------|--------------------------------------------|--------------------------------------------|
| Deployment                 | Single unit                                 | Multiple independent units                 |
| Scalability                | Whole app scaled together                   | Individual services can scale independently|
| Technology Stack           | Mostly uniform                              | Polyglot (different tech per service)      |
| Development Speed          | Faster initially                            | Faster long-term (parallel teams)          |
| Code Base                  | One large codebase                          | Many small repositories or modules         |
| Failures                   | One failure can crash all                   | Isolated failures                          |
| Testing                    | Easier unit testing                         | Complex integration testing                |
| DevOps Complexity          | Simple                                      | Complex (needs CI/CD, containers, etc.)    |
| Deployment Risk            | High (all or nothing)                       | Low (can deploy one service at a time)     |
| Data Sharing               | Direct access (shared DB)                   | Separate databases per service             |

---

## 🔍 **When to Use Monolithic Architecture**

✅ Choose Monolithic when:
- You're building a **small or medium app**
- Your team is small
- Rapid initial development is more important
- You don’t need to scale individual parts differently
- You want **simple deployment and debugging**

**Example Use Cases:**
- Startup MVP
- Internal tools
- University projects
- Admin panels

---

## 🕸️ **When to Use Microservices Architecture**

✅ Choose Microservices when:
- Your app is **large and complex**
- You have **multiple development teams**
- You need to scale certain modules independently
- You require **high availability**
- You want to use different languages/tech for different modules

**Example Use Cases:**
- E-commerce platforms
- Streaming platforms (Netflix)
- Banking and financial applications
- SaaS platforms (e.g., project management, CRM)

---

## 🧠 Real-Life Examples

| Company    | Architecture Used         | Description |
|------------|---------------------------|-------------|
| **Netflix**  | Microservices              | Hundreds of services for video, recommendation, billing, etc. |
| **Amazon**   | Microservices              | Handles separate services for product catalog, payments, reviews, etc. |
| **Small Retail Store** | Monolithic | A single web app for login, product management, and orders |

---

## 🧪 Pro Tip

Many systems **start as Monolithic** and **evolve into Microservices** as they grow in complexity and scale.

---
## 📚 References

- Fowler: Microservices — https://martinfowler.com/articles/microservices.html
- Fowler: Monolith First — https://martinfowler.com/bliki/MonolithFirst.html
- Nginx Microservices Guide — https://www.nginx.com/learn/microservices/
- Twelve-Factor App — https://12factor.net/

---

## 🎯 Architecture Interview Questions — Critical Scenarios

---

### 🏗️ Monolith to Microservices

**Q1. How do you migrate a monolithic application to microservices without downtime?**

**Answer:**
Use the **Strangler Fig Pattern** — incrementally replace parts of the monolith while keeping it running:

```
Phase 1: Route new traffic through API Gateway (monolith still serves all)
         Client → API Gateway → Monolith

Phase 2: Extract one service, redirect specific routes
         Client → API Gateway → User Service (new)
                             → Monolith (everything else)

Phase 3: Progressively extract more services
         Client → API Gateway → User Service
                             → Order Service
                             → Monolith (remaining legacy)

Phase 4: Monolith fully replaced
```

**Practical steps:**
1. **Identify seams** — find natural domain boundaries (users, orders, payments)
2. **Start with greenfield services** — new features go into new services
3. **Extract read-heavy services first** — less transaction complexity
4. **Event-driven strangler** — monolith publishes events, new service consumes them
5. **Database per service** — gradually separate shared DB (most complex step)

**What to avoid:** "Big bang" rewrites. They fail 70% of the time. Netflix, Uber, Amazon all used incremental migration.

---

**Q2. What is the CQRS pattern and when does it justify the added complexity?**

**Answer:**
**CQRS (Command Query Responsibility Segregation)** separates read (Query) and write (Command) models.

```
┌──────────┐   Commands    ┌────────────────┐
│          │ ──────────────►   Write Model   │──► Write DB
│  Client  │               └────────────────┘
│          │   Queries     ┌────────────────┐
│          │ ◄──────────── │   Read Model   │◄── Read DB (optimized view)
└──────────┘               └────────────────┘
```

**Without CQRS problem:** Complex reads and writes share the same model. To optimize reads (denormalized views), you compromise write integrity.

**Justified when:**
- Read and write loads are dramatically different (read-heavy: 100:1 ratio)
- Read model needs different structure than write model (reporting vs transactional)
- You need separate scaling of read vs write infrastructure

**Not justified when:**
- Simple CRUD applications
- Small team — CQRS adds significant complexity (separate models, eventual consistency)
- Low traffic applications

**Often paired with Event Sourcing:**
```
Command → Domain Event → Event Store
                       → Read Model Projections (multiple views)
```

---

**Q3. What is Event Sourcing and what are its advantages and disadvantages?**

**Answer:**
Instead of storing current state, **Event Sourcing** stores the sequence of events that led to that state.

```
Traditional:  orders table → { id: 1, status: "shipped", amount: 100 }

Event Sourcing:
events table → { id: 1, order_id: 1, type: "OrderCreated", amount: 100 }
             → { id: 2, order_id: 1, type: "PaymentConfirmed" }
             → { id: 3, order_id: 1, type: "OrderShipped" }
```

**Advantages:**
- Complete audit trail — replay history for debugging, compliance
- Temporal queries — "what was the state of order #1 at 2pm yesterday?"
- Event-driven integration — other services subscribe to events
- Zero data loss — no UPDATE/DELETE, only INSERT
- Time travel — rebuild any snapshot at any point in time

**Disadvantages:**
- Complexity — requires projections/snapshots for current state queries
- Eventual consistency — read models may lag
- Schema evolution — old events must be handled when domain model changes
- Storage growth — events accumulate (use snapshots for performance)
- Steep learning curve

**Snapshot pattern** (performance optimization):
```
Events 1-1000 → Snapshot (state at event 1000)
Events 1001-present → Apply to snapshot
```

---

**Q4. What is a Service Mesh and when do you need one?**

**Answer:**
A **Service Mesh** is an infrastructure layer for service-to-service communication, handling cross-cutting concerns without code changes.

```
Without Service Mesh:
Service A → HTTP call → Service B
(A handles: retry, timeout, auth, encryption, tracing)

With Service Mesh (Sidecar Proxy):
Service A → Sidecar Proxy → Sidecar Proxy → Service B
           (mesh handles: retry, timeout, mTLS, tracing, circuit breaking)
```

**What it provides:**
| Feature | Without Mesh | With Mesh |
|---------|-------------|-----------|
| **mTLS** | Implement in each service | Automatic |
| **Circuit breaking** | Each service uses Hystrix/Resilience4j | Automatic |
| **Retry/timeout** | Application-level configuration | Centralized policy |
| **Observability** | Per-service instrumentation | Automatic distributed tracing |
| **Traffic splitting** | Application code changes | Config file (blue-green, canary) |

**Popular: Istio, Linkerd, Envoy (standalone proxy)**

**When you need it:** 10+ microservices, polyglot environment (different languages), need for zero-trust networking (mTLS everywhere), advanced traffic management (canary deployments, A/B testing).

**When it's overkill:** Small number of services, single language stack, team not ready for operational complexity.

---

**Q5. What is the Circuit Breaker pattern and how does it prevent cascading failures?**

**Answer:**
The Circuit Breaker prevents a failing downstream service from taking down your entire system.

```
States:
CLOSED (normal) → requests pass through
    │
    │ (threshold failures exceeded)
    ▼
OPEN (failing) → requests fail fast without calling downstream
    │
    │ (after timeout period)
    ▼
HALF-OPEN → allow one request through to test recovery
    │
    ├─ success → CLOSED
    └─ failure → OPEN
```

**Without Circuit Breaker:** Service A calls Service B which is slow/down → A's threads/connections pile up waiting → A becomes unresponsive → Services C, D, E that call A also fail → cascading failure.

**With Circuit Breaker:**
```php
class CircuitBreaker {
    private $failures = 0;
    private $threshold = 5;
    private $state = 'CLOSED';
    private $lastFailureTime;

    public function call(callable $service) {
        if ($this->state === 'OPEN') {
            if (time() - $this->lastFailureTime > 30) {
                $this->state = 'HALF_OPEN';
            } else {
                throw new CircuitOpenException('Service unavailable');
            }
        }

        try {
            $result = $service();
            $this->onSuccess();
            return $result;
        } catch (Exception $e) {
            $this->onFailure();
            throw $e;
        }
    }
}
```

**Real-world:** Netflix Hystrix (now deprecated, replaced by Resilience4j), AWS App Mesh, Istio have built-in circuit breaking. Laravel's Retry facade, Guzzle retry middleware provide simpler retry-only patterns.

---

### 🆕 Modern Architecture Concepts

**Q6. What is the Twelve-Factor App methodology and why does it matter for cloud deployments?**

**Answer:**
Twelve-Factor is a methodology for building software-as-a-service apps that are scalable, portable, and maintainable.

| Factor | Principle | Example |
|--------|-----------|---------|
| **Codebase** | One codebase, multiple deploys | Single Git repo per service |
| **Dependencies** | Explicitly declare and isolate | composer.json, package.json |
| **Config** | Store config in environment | `.env` files, not hardcoded |
| **Backing Services** | Treat as attached resources | DB, Redis as URLs in config |
| **Build/Release/Run** | Strict separation | Docker build → tag → deploy |
| **Processes** | Stateless, share-nothing | No local file storage, no sticky sessions |
| **Port Binding** | Export services via port binding | App serves its own HTTP |
| **Concurrency** | Scale out via process model | PHP-FPM workers, multiple containers |
| **Disposability** | Fast startup, graceful shutdown | Handle SIGTERM, process queued jobs |
| **Dev/Prod Parity** | Keep environments as similar as possible | Same Docker image in dev and prod |
| **Logs** | Treat as event streams | stdout/stderr → log aggregator |
| **Admin Processes** | Run as one-off processes | Migrations as separate job |

**Most violated:** Config (hardcoded credentials), Processes (sessions in local files, local file uploads), Dev/Prod Parity (different DB engines in dev vs prod).

---

**Q7. Compare event-driven architecture with request-driven architecture.**

**Answer:**

```
Request-Driven (Synchronous):
Order Service → calls → Payment Service → calls → Notification Service
(Tight coupling, if Payment Service is down, Order Service fails)

Event-Driven (Asynchronous):
Order Service → publishes → "OrderCreated" event → Message Broker
                           Payment Service (subscribes, processes payment)
                           Notification Service (subscribes, sends email)
(Loose coupling, services independent)
```

| Aspect | Request-Driven | Event-Driven |
|--------|---------------|-------------|
| **Coupling** | Tight — service A knows about B | Loose — services know only about events |
| **Availability** | Dependent on downstream | Independent — broker absorbs failures |
| **Consistency** | Easier (synchronous) | Harder (eventual consistency) |
| **Debugging** | Easier (trace follows call) | Harder (distributed, async) |
| **Complexity** | Lower | Higher (broker, consumers, idempotency) |
| **Scale** | Harder (coupled scaling) | Better (each consumer scales independently) |

**Hybrid approach (Saga pattern):** Use events for cross-service coordination but local synchronous calls within a service.

---

**Q8. What is eventual consistency and how do you handle it in distributed systems?**

**Answer:**
Eventual consistency means data will become consistent across all nodes, but not immediately. The system doesn't guarantee you'll always read the latest write.

**Real scenario:**
```
User updates profile photo:
T=0: Write to Primary DB ✅
T=0.1: Read from Replica → returns old photo ❌ (replication lag)
T=2: Read from Replica → returns new photo ✅ (eventually consistent)
```

**Strategies to handle:**

1. **Read-your-own-writes consistency:** After a write, route that user's reads to the primary temporarily
   ```php
   // After user updates profile, set a flag for 30 seconds
   Cache::put("user_{$id}_read_primary", true, 30);

   // In read middleware
   if (Cache::get("user_{$userId}_read_primary")) {
       $db->useConnection('primary');
   }
   ```

2. **Optimistic UI:** Update UI immediately, sync in background (Twitter likes)

3. **Version vectors/timestamps:** Client includes last-seen version, server returns latest or error if stale

4. **Monotonic reads:** Once you've seen a value, you won't see an older value (sticky session to replica)

5. **Design for idempotency:** Operations can be retried safely (use idempotency keys)
