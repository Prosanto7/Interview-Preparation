# 🏗️ System Design Guide

> A comprehensive guide to designing scalable, reliable, and maintainable software systems.

---

## 📚 Table of Contents

1. [Fundamentals](#-fundamentals)
2. [Key Concepts](#-key-concepts)
3. [System Design Principles](#-system-design-principles)
4. [Scalability Patterns](#-scalability-patterns)
5. [Database Design](#-database-design)
6. [Caching Strategies](#-caching-strategies)
7. [Load Balancing](#-load-balancing)
8. [Message Queues & Event-Driven Architecture](#-message-queues--event-driven-architecture)
9. [Common System Design Questions](#-common-system-design-questions)
10. [Resources](#-resources)

---

## 🎯 Fundamentals

### What is System Design?

**System Design** is the process of defining the architecture, components, modules, interfaces, and data for a system to satisfy specified requirements.

**Key Goals:**
- **Scalability** - Handle growing amounts of work
- **Reliability** - System works correctly even when things fail
- **Availability** - System is operational and accessible
- **Maintainability** - Easy to modify and extend
- **Performance** - Fast response times and high throughput

---

## 🔑 Key Concepts

### 1. **Vertical vs Horizontal Scaling**

| Type | Description | Pros | Cons | Example |
|------|-------------|------|------|---------|
| **Vertical (Scale Up)** | Add more power (CPU, RAM) to existing machine | Simple, No code changes | Hardware limits, Single point of failure | Upgrading from 8GB to 32GB RAM |
| **Horizontal (Scale Out)** | Add more machines | Unlimited scaling, Better fault tolerance | Complex, Requires load balancing | Adding more servers |

---

### 2. **CAP Theorem**

In distributed systems, you can only guarantee **2 out of 3**:

- **C**onsistency - All nodes see the same data at the same time
- **A**vailability - Every request gets a response (success/failure)
- **P**artition Tolerance - System continues despite network failures

**Examples (with nuance):**
- **CP systems (favor Consistency under partition):** HBase, MongoDB (with majority write concern), ZooKeeper, etcd, Consul
- **AP systems (favor Availability under partition):** Cassandra, DynamoDB, Riak, CouchDB
- **CA systems:** Single-node RDBMS like PostgreSQL/MySQL when not distributed (CAP assumes partitions may occur; true distributed systems must choose CP or AP when P happens)

Notes:
- There is no practical “CA” choice in a partition-tolerant distributed system; CA applies when there is no partition (e.g., single-node).
- MongoDB can be tuned: with majority write/read concern it behaves CP; with relaxed concerns it may show more AP-like behavior.
- Redis: Standalone Redis is CA (not partition-tolerant). Redis Cluster behavior depends on configuration (failover, coverage settings); treat classification as configuration-dependent rather than purely CP/AP.

---

### 3. **ACID vs BASE**

#### ACID (Traditional Databases)
- **A**tomicity - All or nothing
- **C**onsistency - Data is valid
- **I**solation - Concurrent transactions don't interfere
- **D**urability - Committed data persists

#### BASE (NoSQL Databases)
- **B**asically **A**vailable - System available most of the time
- **S**oft state - State may change over time
- **E**ventual consistency - System becomes consistent eventually

---

### 4. **Latency vs Throughput**

| Metric | Definition | Example |
|--------|------------|---------|
| **Latency** | Time to complete a single operation | 50ms to load a web page |
| **Throughput** | Number of operations per time unit | 10,000 requests/second |

**Goal**: Low latency + High throughput

---

## 🏛️ System Design Principles

### 1. **Single Responsibility Principle (SRP)**
Each component should have one reason to change.

### 2. **Separation of Concerns**
Divide the system into distinct features with minimal overlap.

### 3. **Don't Repeat Yourself (DRY)**
Avoid code/logic duplication.

### 4. **KISS (Keep It Simple, Stupid)**
Simplicity should be a key goal; avoid unnecessary complexity.

### 5. **YAGNI (You Aren't Gonna Need It)**
Don't add functionality until necessary.

### 6. **Fail Fast**
Detect and report failures immediately.

### 7. **Design for Failure**
Assume components will fail and design accordingly.

---

## 📈 Scalability Patterns

### 1. **Database Sharding**

Splitting data across multiple databases based on a shard key.

```
Users with ID 1-1000 → Shard 1
Users with ID 1001-2000 → Shard 2
Users with ID 2001-3000 → Shard 3
```

**Sharding Strategies:**
- **Range-based**: By ID, date, etc.
- **Hash-based**: Hash function determines shard
- **Geographic**: By location

---

### 2. **Database Replication**

| Type | Description | Use Case |
|------|-------------|----------|
| **Master-Slave** | One master (write), multiple slaves (read) | Read-heavy applications |
| **Master-Master** | Multiple masters (write/read) | High availability |
| **Multi-Region** | Replicas in different geographic regions | Global applications |

---

### 3. **Partitioning**

Dividing data into smaller, manageable pieces.

- **Horizontal Partitioning (Sharding)**: Splitting rows
- **Vertical Partitioning**: Splitting columns
- **Functional Partitioning**: By business function

---

### 4. **Microservices Architecture**

Breaking monolith into small, independent services.

**Benefits:**
- Independent deployment
- Technology diversity
- Fault isolation
- Easier scaling

**Challenges:**
- Distributed system complexity
- Network latency
- Data consistency
- Testing complexity

---

## 💾 Database Design

### Choosing the Right Database

| Database Type | When to Use | Examples |
|---------------|-------------|----------|
| **Relational (SQL)** | Structured data, ACID transactions | PostgreSQL, MySQL |
| **Document Store** | Flexible schema, JSON data | MongoDB, CouchDB |
| **Key-Value** | Simple data, high-speed reads/writes | Redis, DynamoDB |
| **Column-Family** | Time-series, analytics | Cassandra, HBase |
| **Graph** | Complex relationships | Neo4j, Amazon Neptune |
| **Search Engine** | Full-text search | Elasticsearch, Solr |

---

### Database Normalization

**Purpose**: Reduce data redundancy and improve data integrity.

| Normal Form | Rule | Example |
|-------------|------|---------|
| **1NF** | Atomic values, no repeating groups | Each cell has single value |
| **2NF** | 1NF + No partial dependencies | Non-key attributes depend on entire primary key |
| **3NF** | 2NF + No transitive dependencies | Non-key attributes depend only on primary key |

**When to Denormalize**: For read-heavy systems to improve query performance.

---

## 🚀 Caching Strategies

### Cache Levels

```
Browser Cache → CDN → Application Cache → Database Cache
```

### Caching Patterns

#### 1. **Cache-Aside (Lazy Loading)**
```python
def get_user(user_id):
    # Check cache first
    user = cache.get(user_id)
    if user is None:
        # Cache miss - fetch from DB
        user = db.query(user_id)
        cache.set(user_id, user)
    return user
```

#### 2. **Write-Through**
- Write to cache and database simultaneously
- Ensures data consistency
- Higher write latency

#### 3. **Write-Behind (Write-Back)**
- Write to cache immediately
- Asynchronously write to database
- Better write performance
- Risk of data loss

#### 4. **Read-Through**
- Cache automatically loads data from database on miss

---

### Cache Eviction Policies

| Policy | Description | Use Case |
|--------|-------------|----------|
| **LRU** (Least Recently Used) | Evict oldest accessed item | General purpose |
| **LFU** (Least Frequently Used) | Evict least accessed item | When frequency matters |
| **FIFO** (First In First Out) | Evict oldest added item | Simple scenarios |
| **TTL** (Time To Live) | Evict after expiration time | Time-sensitive data |

---

### Popular Caching Solutions

- **Redis** - In-memory data structure store
- **Memcached** - Distributed memory caching
- **Varnish** - HTTP accelerator
- **CDN** - Cloudflare, CloudFront, Fastly

---

## ⚖️ Load Balancing

### Load Balancing Algorithms

| Algorithm | How It Works | Best For |
|-----------|--------------|----------|
| **Round Robin** | Requests distributed sequentially | Equal server capacity |
| **Least Connections** | Route to server with fewest connections | Varying request times |
| **IP Hash** | Hash client IP to determine server | Session persistence |
| **Weighted Round Robin** | Servers with higher weights get more requests | Different server capacities |
| **Least Response Time** | Route to fastest responding server | Performance optimization |

---

### Load Balancer Types

#### Layer 4 (Transport Layer)
- Routes based on IP and TCP/UDP port
- Faster, less resource-intensive
- Example: AWS Network Load Balancer

#### Layer 7 (Application Layer)
- Routes based on HTTP headers, cookies, URL
- Content-based routing
- Example: AWS Application Load Balancer, Nginx

---

## 📨 Message Queues & Event-Driven Architecture

### Why Use Message Queues?

- **Decoupling** - Services don't need to know about each other
- **Asynchronous Processing** - Handle tasks in background
- **Load Leveling** - Absorb traffic spikes
- **Fault Tolerance** - Messages persisted if consumer fails
- **Scalability** - Add more consumers as needed

---

### Message Queue Patterns

#### 1. **Point-to-Point (Queue)**
- One producer → One consumer
- Message consumed once
- Example: Order processing

#### 2. **Publish-Subscribe (Topic)**
- One producer → Multiple consumers
- All subscribers receive message
- Example: Notifications

#### 3. **Request-Reply**
- Request sent to queue
- Reply sent back to requester
- Example: RPC-style communication

---

### Popular Message Brokers

| Tool | Type | Use Case |
|------|------|----------|
| **RabbitMQ** | Traditional message broker | Complex routing, AMQP protocol |
| **Apache Kafka** | Distributed event streaming | High throughput, event sourcing |
| **Amazon SQS** | Managed queue service | Simple queuing on AWS |
| **Redis Pub/Sub** | In-memory messaging | Real-time, low latency |
| **Apache Pulsar** | Cloud-native messaging | Multi-tenancy, geo-replication |

---

## 🎯 Common System Design Questions

### 1. **Design a URL Shortener (like bit.ly)**

**Requirements:**
- Shorten long URLs
- Redirect to original URL
- Track analytics

**Key Components:**
- URL encoding (Base62)
- Database (SQL or NoSQL)
- Cache (Redis)
- Rate limiting

**Database Schema:**
```sql
CREATE TABLE urls (
    short_code VARCHAR(10) PRIMARY KEY,
    original_url TEXT NOT NULL,
    created_at TIMESTAMP,
    clicks INTEGER DEFAULT 0
);
```

---

### 2. **Design a Rate Limiter**

**Algorithms:**

#### Token Bucket
- Tokens added at fixed rate
- Request consumes token
- Reject if no tokens available

#### Leaky Bucket
- Requests added to queue
- Processed at fixed rate
- Reject if queue full

#### Fixed Window Counter
- Count requests in time window
- Reset counter at window boundary

#### Sliding Window Log
- Store timestamp of each request
- Count requests in sliding window

---

### 3. **Design a Notification System**

**Components:**
- **Service Layer**: Receives notification requests
- **Message Queue**: Kafka/RabbitMQ
- **Workers**: Process and send notifications
- **Templates**: Email/SMS/Push templates
- **Delivery Tracking**: Status and analytics

**Channels:**
- Email (SendGrid, AWS SES)
- SMS (Twilio, SNS)
- Push Notifications (FCM, APNs)
- In-app notifications

---

### 4. **Design a Chat Application (like WhatsApp)**

**Requirements:**
- One-to-one messaging
- Group chat
- Online status
- Message delivery status

**Key Components:**
- **WebSocket Server**: Real-time communication
- **Message Queue**: Kafka for message persistence
- **Database**: Cassandra for messages, Redis for online status
- **Media Storage**: S3 for images/videos
- **CDN**: Fast media delivery

---

### 5. **Design a Video Streaming Service (like YouTube)**

**Components:**
- **Upload Service**: Video processing, transcoding
- **Storage**: Object storage (S3)
- **CDN**: CloudFront, Akamai
- **Metadata Database**: Video info, user data
- **Recommendation Engine**: ML-based suggestions
- **Search Service**: Elasticsearch

**Video Processing Pipeline:**
```
Upload → Transcoding (multiple resolutions) → Thumbnail generation
→ Storage → CDN distribution
```

---

### 6. **Design a Distributed Cache**

**Requirements:**
- Fast read/write
- Scalable
- Fault-tolerant

**Strategy:**
- **Consistent Hashing**: Distribute keys across nodes
- **Replication**: Multiple copies for availability
- **Sharding**: Partition data
- **Leader Election**: Coordination (Zookeeper, etcd)

---

### 7. **Design an E-Commerce Platform**

**Core Services:**
- User Service (Authentication, Profile)
- Product Catalog Service
- Inventory Service
- Order Service
- Payment Service
- Notification Service
- Search Service (Elasticsearch)

**Challenges:**
- **Inventory Management**: Prevent overselling
- **Payment Processing**: ACID transactions
- **Flash Sales**: Handle traffic spikes
- **Search**: Fast product discovery

**Solutions:**
- Use distributed locks for inventory
- Implement saga pattern for distributed transactions
- Queue system for order processing
- Redis for cart management

---

## 📊 System Design Metrics

### Availability

```
Availability = (Uptime / Total Time) × 100%
```

| Availability | Downtime per Year |
|--------------|-------------------|
| 99% (2 nines) | 3.65 days |
| 99.9% (3 nines) | 8.76 hours |
| 99.99% (4 nines) | 52.56 minutes |
| 99.999% (5 nines) | 5.26 minutes |

---

### SLA vs SLO vs SLI

- **SLA** (Service Level Agreement): Contract with customers
- **SLO** (Service Level Objective): Internal goal
- **SLI** (Service Level Indicator): Actual measurement

---

## 🛠️ Design Approach Framework

### Step 1: Understand Requirements

**Functional Requirements:**
- What should the system do?
- Core features?

**Non-Functional Requirements:**
- Scalability needs?
- Performance expectations?
- Consistency vs Availability?

---

### Step 2: Capacity Estimation

**Traffic Estimates:**
- Daily Active Users (DAU)
- Requests per second (QPS)
- Read/Write ratio

**Storage Estimates:**
- Data size per user/request
- Total storage needed
- Bandwidth requirements

**Example:**
```
100M users
1M daily active users (1% DAU)
Average 10 requests/day/user
= 10M requests/day
= ~115 requests/second
```

---

### Step 3: System Interface (API Design)

Define RESTful APIs:

```
POST /api/v1/users
GET /api/v1/users/{id}
PUT /api/v1/users/{id}
DELETE /api/v1/users/{id}
```

---

### Step 4: Database Schema

Design tables, relationships, and indexes.

---

### Step 5: High-Level Design

Draw architecture diagram:
- Client → Load Balancer → App Servers → Database
- Include cache, queue, CDN

---

### Step 6: Detailed Design

Deep dive into:
- Data flow
- Algorithms
- Trade-offs
- Bottlenecks

---

### Step 7: Identify Bottlenecks

- Single point of failure?
- Database bottleneck?
- Network bandwidth?
- How to scale?

---

## 📚 Resources

### Books
- **"Designing Data-Intensive Applications"** by Martin Kleppmann
- **"System Design Interview"** by Alex Xu (Volumes 1 & 2)
- **"Building Microservices"** by Sam Newman
- **"The Art of Scalability"** by Martin Abbott

### Online Resources
- [System Design Primer (GitHub)](https://github.com/donnemartin/system-design-primer)
- [ByteByteGo](https://bytebytego.com/)
- [High Scalability Blog](http://highscalability.com/)
- [AWS Architecture Center](https://aws.amazon.com/architecture/)
- [Google Cloud Architecture](https://cloud.google.com/architecture)

### Courses
- [Grokking the System Design Interview](https://www.educative.io/courses/grokking-the-system-design-interview)
- [System Design by Gaurav Sen (YouTube)](https://www.youtube.com/c/GauravSensei)
- [Hussein Nasser (YouTube)](https://www.youtube.com/c/HusseinNasser-software-engineering)

### Practice Platforms
- [LeetCode System Design](https://leetcode.com/discuss/interview-question/system-design)
- [Pramp](https://www.pramp.com/)
- [Exponent](https://www.tryexponent.com/)

---

## 🎓 Interview Tips

1. **Clarify Requirements** - Ask questions before diving in
2. **Think Out Loud** - Explain your reasoning
3. **Start High-Level** - Then drill into details
4. **Discuss Trade-offs** - Every decision has pros/cons
5. **Consider Constraints** - Time, money, resources
6. **Be Honest** - Say "I don't know" if unsure, then reason through it
7. **Practice** - Mock interviews with peers

---

## ✅ Checklist for System Design Interview

- [ ] Understand functional requirements
- [ ] Identify non-functional requirements
- [ ] Calculate capacity and bandwidth
- [ ] Define system APIs
- [ ] Design database schema
- [ ] Create high-level architecture diagram
- [ ] Identify and address bottlenecks
- [ ] Discuss scalability strategies
- [ ] Consider failure scenarios
- [ ] Estimate costs (if applicable)

---

**Remember**: There's no single "correct" design. Focus on demonstrating your thought process, trade-off analysis, and ability to handle complexity.

---

## 🎯 System Design Interview Questions — Critical Scenarios

---

### 📊 Back-of-Envelope Estimation

**Q1. How do you estimate system capacity requirements? Design a URL shortener that handles 100M URLs.**

**Answer:**

**Step 1: Clarify requirements**
- Write: 100M new URLs/day → read ratio 10:1 → 1B reads/day
- URLs expire after 5 years
- Short URL length: 7 characters (62^7 = 3.5 trillion combinations)

**Step 2: Capacity estimation**
```
Write QPS:  100M / 86400 = ~1,160 writes/sec
Read QPS:   1B / 86400 = ~11,600 reads/sec
Peak QPS:   ×2 = 2,320 writes, 23,200 reads

Storage:
  URL entry: ~500 bytes (short URL + long URL + metadata)
  Per day: 100M × 500 bytes = 50 GB/day
  5 years: 50 GB × 365 × 5 = ~90 TB

Bandwidth:
  Read: 11,600 req/s × 500 bytes = ~5.8 MB/s (trivial)
  Write: 1,160 req/s × 500 bytes = ~580 KB/s

Cache:
  80/20 rule: 20% of URLs = 80% of traffic
  Daily cache: 11,600 reads/s × 86400 × 20% × 500 bytes = ~100 GB
  → Multiple cache servers (Redis cluster)
```

**Step 3: High-level design**
```
Write path:
  Client → API Gateway → App Server → ID Generator (Snowflake) → DB (MySQL)
                                   → Cache (Redis) invalidation

Read path:
  Client → CDN (cache 301/302 redirect) → if miss → App Server → Redis → MySQL
```

**Step 4: Key decisions**
- **ID generation:** Snowflake (distributed, monotonic 64-bit IDs) → base62 encode → 7 chars
- **Redirection:** 301 (permanent, CDN caches) vs 302 (temporary, tracks clicks)
- **DB sharding:** Shard by short URL hash (consistent hashing across 5-10 shards)
- **Cache strategy:** Cache-aside, TTL = 24h for popular URLs

---

**Q2. Design a distributed rate limiter that works across multiple server instances.**

**Answer:**

**Problem with local rate limiting:**
```
10 app servers, limit: 100 req/min per user
Server 1: User makes 100 requests → allowed (limit hit)
User switches to Server 2: 100 more requests → allowed again!
Local counters don't share state
```

**Solution: Centralized counter (Redis) with Lua atomicity:**
```lua
-- Sliding window log algorithm in Redis Lua (atomic)
local key = KEYS[1]          -- "ratelimit:user:123"
local window = tonumber(ARGV[1])  -- 60 seconds
local limit = tonumber(ARGV[2])   -- 100 requests
local now = tonumber(ARGV[3])     -- Current timestamp ms

-- Remove entries outside the window
redis.call('ZREMRANGEBYSCORE', key, '-inf', now - window * 1000)

-- Count requests in window
local count = redis.call('ZCARD', key)

if count < limit then
    -- Add current request
    redis.call('ZADD', key, now, now)
    redis.call('EXPIRE', key, window)
    return 1  -- Allowed
else
    return 0  -- Rate limited
end
```

**Rate limiting algorithms comparison:**

| Algorithm | Accuracy | Memory | Burst handling | Implementation |
|-----------|---------|--------|---------------|----------------|
| **Fixed Window** | Low (boundary bursts) | O(1) | Allows 2× at window boundary | Counter + TTL |
| **Sliding Window Log** | Perfect | O(n) per user | Smooth | Sorted Set in Redis |
| **Sliding Window Counter** | Good approximation | O(1) | Smooth | 2 counters + interpolation |
| **Token Bucket** | Perfect | O(1) | Configurable burst | Tokens + timestamp |
| **Leaky Bucket** | Perfect | O(n) | None (smooths output) | Queue + timer |

**Token Bucket in Redis:**
```lua
local tokens = tonumber(redis.call('HGET', key, 'tokens') or capacity)
local last_refill = tonumber(redis.call('HGET', key, 'last_refill') or now)
local elapsed = (now - last_refill) / 1000  -- seconds

-- Refill tokens based on elapsed time
local new_tokens = math.min(capacity, tokens + elapsed * refill_rate)

if new_tokens >= 1 then
    redis.call('HMSET', key, 'tokens', new_tokens - 1, 'last_refill', now)
    return 1  -- Allowed
else
    return 0  -- Denied
end
```

---

**Q3. Design a notification system that can deliver 10M push notifications per day.**

**Answer:**

**Requirements clarification:**
- Channels: Push (mobile), Email, SMS, in-app
- Delivery: Best-effort (push) vs guaranteed (email)
- Priority: Transactional (OTP) > Marketing

**Architecture:**
```
Trigger Sources:
  User action → API event → NotificationService
  Scheduled jobs → CronTrigger → NotificationService

NotificationService:
  1. Validate user preferences (don't send if unsubscribed)
  2. Rate limit per user (max 10 notifications/hour)
  3. Template rendering
  4. Route to appropriate channel queue

Channel Queues (separate priority queues):
  - transactional:push    (SQS FIFO, high priority)
  - transactional:email   (SQS FIFO, high priority)
  - marketing:push        (SQS standard, low priority)

Channel Workers:
  PushWorker → FCM (Android) / APNs (iOS) → Handle failures → retry/deadletter
  EmailWorker → SES/SendGrid → bounce/complaint handling
  SMSWorker → Twilio → delivery receipt tracking
```

**Scaling to 10M/day:**
```
10M notifications/day = 116/sec average = ~350/sec peak

Push notifications (FCM batch API): 1000 per request → 0.35 FCM req/sec (trivial)
Email (SES): 14 emails/sec → SES handles 100k+/sec — fine
SMS: Most expensive — batch by provider for cost optimization

Worker scaling:
  Auto-scaling based on queue depth
  SQS → CloudWatch alarm → scale out ECS tasks
  Target: < 5 min queue depth for transactional
```

**Handling failures:**
```
FCM returns failure codes per token:
  - NotRegistered: Token expired → delete from DB
  - Unavailable: Device offline → retry with exponential backoff
  - InvalidRegistration: Bad token → delete

Dead letter queue for failed after 3 retries:
  Alert on-call → manual investigation or drop (marketing)
```

---

**Q4. What consistency patterns should you use when designing a distributed e-commerce checkout?**

**Answer:**

**The problem:** Checkout spans multiple services — inventory must be decremented, payment charged, order created. These must either all succeed or all fail.

**Saga Pattern (distributed transactions without 2PC):**
```
Choreography-based Saga:
  OrderService creates order (status: PENDING)
    → publishes OrderCreated event
  InventoryService receives event → reserves items
    → publishes InventoryReserved (or InventoryInsufficient)
  PaymentService receives event → charges card
    → publishes PaymentCompleted (or PaymentFailed)
  OrderService receives completion → status: CONFIRMED
                                  → or triggers compensating transactions

Compensating transactions (rollback):
  PaymentFailed → InventoryService releases reservation
                → OrderService marks order FAILED
```

**2PC vs Saga:**
| Aspect | 2PC | Saga |
|--------|-----|------|
| **Consistency** | Strong (ACID) | Eventual |
| **Coupling** | Tight (coordinator knows all) | Loose (events) |
| **Performance** | Slow (blocking locks) | Fast (async) |
| **Failure recovery** | Complex (coordinator single point) | Per-service compensating transactions |
| **Use when** | Same DB, financial ledger | Microservices, cross-service |

**Idempotency keys for payment:**
```php
// Client generates UUID before calling payment API
$idempotencyKey = Str::uuid();
$payment = $paymentService->charge(
    amount: 9999,
    idempotencyKey: $idempotencyKey  // Safe to retry with same key
);
// If network fails and client retries → same key → same result, no double charge
```

---

**Q5. How would you design a real-time collaborative document editor (like Google Docs)?**

**Answer:**

**Core challenge:** Multiple users editing the same document simultaneously → conflicts.

**Operational Transformation (OT) — traditional approach:**
```
User A: Delete char at position 5
User B simultaneously: Insert "hello" at position 3

Without OT: Operations applied in different orders → divergent state
With OT: Transform B's operation relative to A's
  After A deletes position 5, B's insert becomes position 3 (unchanged if insert was before delete)
  After B inserts at 3, A's delete shifts to position 8 (shifted by 5 chars)
```

**CRDT (Conflict-free Replicated Data Type) — modern approach:**
```
Each character has: unique ID + position + tombstone flag
Operations are commutative (order doesn't matter) and idempotent
No central coordinator needed — peers can sync directly
Used by: Figma, Notion (Y.js), Apple Notes
```

**Architecture:**
```
Client ──WebSocket──► Doc Server (WebSocket)
                        │
                        ├── Operation log (Kafka — ordered, durable)
                        │
                        ├── State store (Redis — current doc state for fast sync)
                        │
                        └── DB (PostgreSQL — periodic snapshots)

Sync flow:
  Client connects → server sends current version + doc snapshot
  Client makes edit → operation sent to server
  Server applies OT/CRDT → broadcasts to all connected clients
  Server writes operation to Kafka (durable log)
  Periodic snapshot to DB (for new client cold start)
```

**Presence (who is editing where):**
```
WebSocket heartbeat every 2s:
  {userId: 123, cursor: {line: 5, col: 12}, selection: {...}}
Server broadcasts to all session participants
Store in Redis with 5s TTL (auto-expires if client disconnects)
```
