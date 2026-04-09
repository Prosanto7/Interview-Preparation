## 🔴 What is Redis?

**Redis** stands for **Remote Dictionary Server**. It is an **in-memory, key-value data store** used as:

- A **database**
- A **cache**
- A **message broker**

It is **blazing fast**, written in **C**, and stores data primarily in memory, with optional persistence to disk.

---

## 📦 Core Features of Redis

| Feature               | Description                                                                 |
|------------------------|-----------------------------------------------------------------------------|
| 🔑 **Key-Value Store** | Stores data as key-value pairs (keys are strings, values can be strings, lists, sets, etc.) |
| ⚡ **In-Memory**       | Super-fast data access (sub-millisecond latency)                            |
| 📂 **Data Structures** | Strings, Lists, Sets, Hashes, Sorted Sets, Bitmaps, HyperLogLogs, Streams   |
| 💾 **Persistence**     | Optionally saves data to disk (RDB or AOF)                                  |
| 🌐 **Pub/Sub**         | Supports publish-subscribe messaging pattern                                |
| 🔁 **Replication**     | Master-slave replication for high availability                              |
| ☁️ **Cluster Support** | Scalable across multiple nodes in Redis Cluster                             |
| 🧠 **Lightweight**     | Very efficient with minimal resource usage                                  |

---

## 🧩 Redis Data Structures

| Data Type    | Description                                                                 | Example Use Case                        |
|--------------|-----------------------------------------------------------------------------|-----------------------------------------|
| **String**   | Simple string value                                                         | Caching HTML, counters, tokens          |
| **List**     | Ordered list of strings                                                     | Message queue, timeline                 |
| **Set**      | Unordered collection of unique strings                                      | Tags, followers                         |
| **Hash**     | Map of key-value pairs                                                      | User objects                            |
| **Sorted Set**| Like Set but ordered by score                                              | Leaderboards, ranking                   |
| **Stream**   | Append-only logs of messages                                                | Real-time analytics, event sourcing     |

---

## 🛠️ Redis Commands (Cheat Sheet)

### 🔤 String Commands
```bash
SET key value        # Set a key
GET key              # Get the value
INCR key             # Increment integer value
APPEND key value     # Append to string
DEL key              # Delete key
EXISTS key           # Key exists or not
KEYS *               # List all keys
FLUSHALL             # Deletes all keys
```

### 📝 List Commands
```bash
LPUSH list value     # Push to head
RPUSH list value     # Push to tail
LPOP list            # Pop from head
RPOP list            # Pop from tail
LRANGE list 0 -1     # Get all elements
```

### 🎯 Set Commands
```bash
SADD set member      # Add to set
SMEMBERS set         # Get all members
SISMEMBER set member # Check if exists
```

### 🧰 Hash Commands
```bash
HSET user name John  # Set field in hash
HGET user name       # Get field
HGETALL user         # Get entire hash
```

### 🧮 Sorted Set
```bash
ZADD scores 100 Alice
ZRANGE scores 0 -1 WITHSCORES
```

### 📨 Pub/Sub
```bash
PUBLISH channel message
SUBSCRIBE channel
```

---

## 💾 Redis Persistence Options

| Mode     | Description                                      |
|----------|--------------------------------------------------|
| **RDB**  | Snapshotting: saves data at intervals             |
| **AOF**  | Append Only File: logs every write command        |
| **Hybrid** | Both RDB and AOF for safety and performance     |

---

## ⚙️ Redis Use Cases

| Use Case           | How Redis Helps                              |
|--------------------|-----------------------------------------------|
| ⚡ Caching          | Speeds up apps by storing frequently-used data|
| 🔄 Session Store   | Tracks user sessions (in-memory + TTL)        |
| 🔄 Queue System     | Lists used for reliable messaging             |
| 📈 Real-time Analytics| Fast counters and stats                     |
| 🎮 Leaderboards     | Sorted sets store ranks & scores             |
| 🔔 Pub/Sub System   | Real-time messaging                          |
| 📦 Distributed Lock| Prevent race conditions in microservices     |

---

## 🚀 Performance

- **Extremely fast** (millions of ops/sec)
- **Sub-millisecond latency**
- Can **handle high throughput** applications with ease
- Best used with **limited memory footprints** (since it’s in-memory)

---

## 🧪 Sample Redis Setup (Linux)

```bash
# Install Redis (Ubuntu)
sudo apt update
sudo apt install redis

# Start Redis server
redis-server

# Connect to Redis CLI
redis-cli

# Try it!
SET name "Redis"
GET name
```

---

## 🧰 Redis in Node.js Example

```js
const redis = require("redis");
const client = redis.createClient();

client.set("username", "john", redis.print);
client.get("username", (err, reply) => {
  console.log(reply); // Output: john
});
```

---

## ❌ Redis Limitations

| Limitation              | Detail                                             |
|-------------------------|----------------------------------------------------|
| 🧠 Memory-based         | All data is stored in RAM — can be expensive       |
| 🔒 Single-threaded core | One command at a time (though very fast)           |
| 🛑 Not ideal for complex queries | No joins, no full-text search              |
| 💾 Persistence optional | If not configured, data can be lost on crash       |

---

## 📌 When to Use Redis

✅ Use Redis when:
- You need **low-latency** data access
- Your data fits in memory
- You’re implementing **caching**, **sessions**, **queues**, **real-time analytics**

❌ Avoid Redis when:
- You need **full ACID compliance**
- You’re storing **very large datasets** that exceed memory
- You need **complex querying** or reporting

---

## 🔚 Summary

| Feature       | Redis                             |
|---------------|------------------------------------|
| Type          | In-memory key-value store          |
| Speed         | Ultra-fast (microseconds)          |
| Data Size     | Fits best in RAM                   |
| Persistence   | Optional (RDB & AOF)               |
| Use Cases     | Cache, sessions, leaderboards, queues |

---

Here's a simple **Node.js** project that uses redis to make queries faster:

1. **Fetching data from an external API** (e.g., JSONPlaceholder).
2. **Storing the data in Redis** the first time.
3. **Serving from Redis** on subsequent requests (i.e., caching).

---

## 🧱 Project Structure

```
redis-api-cache/
├── server.js
├── package.json
```

---

## 📦 Step 1: `package.json`

```json
{
  "name": "redis-api-cache",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "axios": "^1.6.0",
    "express": "^4.18.2",
    "redis": "^4.6.7"
  }
}
```

---

## 🚀 Step 2: `server.js`

```js
const express = require('express');
const axios = require('axios');
const redis = require('redis');

const app = express();
const PORT = 3000;

// Redis Client Setup
const client = redis.createClient();

client.on('error', (err) => {
  console.error('Redis error:', err);
});

// Connect to Redis
(async () => {
  await client.connect();
  console.log("🔌 Connected to Redis");
})();

// API Route
app.get('/posts', async (req, res) => {
  try {
    const cacheKey = 'posts';

    // Check if data is in Redis cache
    const cachedData = await client.get(cacheKey);

    if (cachedData) {
      console.log('🧠 Serving from Redis cache');
      return res.status(200).json(JSON.parse(cachedData));
    }

    // If not in cache, fetch from API
    console.log('🌐 Fetching from API...');
    const { data } = await axios.get('https://jsonplaceholder.typicode.com/posts');

    // Save to Redis (with 60 seconds expiration)
    await client.setEx(cacheKey, 60, JSON.stringify(data));

    res.status(200).json(data);
  } catch (err) {
    console.error(err.message);
    res.status(500).json({ error: 'Something went wrong' });
  }
});

// Start Server
app.listen(PORT, () => {
  console.log(`🚀 Server running on http://localhost:${PORT}`);
});
```

---

## 🧪 Step 3: Run the App

```bash
# 1. Install dependencies
npm install

# 2. Start your Redis server
redis-server

# 3. Start the app
npm start
```

---

## 🧠 How It Works

- The first time `/posts` is hit, data is fetched from the **external API** and saved in Redis.
- Subsequent requests within 60 seconds will get the data **from Redis cache**, making it much faster.

---

## ✅ Output Example

```bash
GET http://localhost:3000/posts

# First request:
🌐 Fetching from API...

# Second request:
🧠 Serving from Redis cache
```

## 📚 References

- Redis Documentation: https://redis.io/docs/latest/
- Redis Commands Reference: https://redis.io/docs/latest/commands/
- Redis Data Types: https://redis.io/docs/latest/develop/data-types/
- Redis Persistence (RDB/AOF): https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/

---

## 🎯 Redis Interview Questions — Critical Scenarios

---

### ⚡ High Traffic & Scaling

**Q1. Redis Sentinel vs Redis Cluster — what's the difference and when do you use each?**

**Answer:**

| Feature | Redis Sentinel | Redis Cluster |
|---------|---------------|--------------|
| **Purpose** | High availability (failover) | Horizontal scaling + HA |
| **Data distribution** | All data on primary (replicas are copies) | Data sharded across 16384 slots |
| **Max dataset size** | Limited by single node RAM | Sum of all node RAM |
| **Number of primaries** | 1 primary | 3+ primaries (minimum) |
| **Failover** | Automatic via Sentinel quorum | Automatic |
| **Client routing** | Client uses Sentinel to get current primary | Cluster-aware client handles routing |
| **Complexity** | Moderate | Higher |

**Redis Sentinel architecture:**
```
Sentinel 1  Sentinel 2  Sentinel 3
     \          |          /
      \         |         /
       ┌────────────────┐
       │   Primary      │──► Replica 1
       └────────────────┘──► Replica 2
```

**Redis Cluster architecture:**
```
Primary A (slots 0-5460)    ──► Replica A
Primary B (slots 5461-10922) ──► Replica B
Primary C (slots 10923-16383)──► Replica C
```

**Choose Sentinel when:** Dataset fits in one node, you want simpler setup, need automatic failover only.  
**Choose Cluster when:** Dataset exceeds single node memory, need horizontal write scaling, millions of ops/sec.

---

**Q2. How does Redis handle persistence and what are the trade-offs of RDB vs AOF?**

**Answer:**

| Feature | RDB (Snapshot) | AOF (Append Only File) |
|---------|---------------|----------------------|
| **How** | Point-in-time snapshot to disk | Logs every write command |
| **Performance** | Better (snapshot is async fork) | Slightly slower writes (fsync overhead) |
| **Recovery speed** | Fast (load snapshot) | Slower (replay all commands) |
| **Data loss on crash** | Up to last snapshot interval (minutes) | Configurable: 0s, 1s, or per-write |
| **File size** | Compact | Larger (grows over time, compacted by rewrite) |
| **Use case** | Backups, acceptable data loss | Financial, audit trail, minimal data loss |

**Configuration:**
```bash
# RDB — save every 60 seconds if at least 1000 keys changed
save 60 1000

# AOF — fsync every second (at most 1 second data loss)
appendonly yes
appendfsync everysec

# Hybrid (recommended for most): AOF for recovery, RDB for backups
aof-use-rdb-preamble yes
```

**AOF rewrite:** When AOF grows too large, Redis rewrites it in background (replaces sequence of commands with minimal equivalent).

**Production recommendation:** Use both — AOF with `everysec` for crash recovery, RDB for backups and fast restarts.

---

**Q3. How do you implement a distributed lock with Redis?**

**Answer:**
The **Redlock algorithm** provides reliable distributed locking across multiple Redis nodes.

**Simple lock (single node):**
```php
// SET with NX (only if not exists) + EX (expiry) is atomic
$acquired = $redis->set(
    "lock:resource_name",
    $uniqueLockId,      // Unique ID to prevent releasing another's lock
    ['NX', 'EX' => 30]  // Expire after 30 seconds
);

if (!$acquired) {
    throw new LockNotAcquiredException();
}

try {
    // Critical section
    doWork();
} finally {
    // Release ONLY if we own the lock (Lua ensures atomicity)
    $script = <<<LUA
    if redis.call("GET", KEYS[1]) == ARGV[1] then
        return redis.call("DEL", KEYS[1])
    else
        return 0
    end
    LUA;
    $redis->eval($script, ['lock:resource_name', $uniqueLockId], 1);
}
```

**Why unique ID matters:** If your process times out, lock expires, another process acquires it, then your process resumes — without unique ID check, you'd release the new owner's lock.

**Laravel implementation:**
```php
use Illuminate\Support\Facades\Cache;

$lock = Cache::lock('processing-order-' . $orderId, 10); // 10 second lock
if ($lock->get()) {
    try {
        processOrder($orderId);
    } finally {
        $lock->release();
    }
}
// Or with blocking:
$lock->block(5); // Wait up to 5 seconds to acquire
```

---

### 🗂️ Data Structures & Use Cases

**Q4. When would you use a Redis Sorted Set vs a regular Set?**

**Answer:**
Sorted Sets maintain members with a floating-point score, enabling ranked retrieval.

```bash
# Sorted Set — leaderboard with scores
ZADD leaderboard 9500 "Alice"
ZADD leaderboard 8700 "Bob"
ZADD leaderboard 9800 "Charlie"

# Get top 3 players (highest to lowest score)
ZREVRANGE leaderboard 0 2 WITHSCORES
# → Charlie 9800, Alice 9500, Bob 8700

# Get Alice's rank (0-indexed)
ZREVRANK leaderboard "Alice"  → 1

# Players with score between 9000-10000
ZRANGEBYSCORE leaderboard 9000 10000
```

**Use cases for Sorted Set:**
- Leaderboards (gaming, sales rankings)
- Rate limiting (timestamp as score, member as request ID)
- Priority queues (score = priority)
- Scheduling (score = unix timestamp, pop items due for processing)
- Session expiry tracking (score = expiry timestamp)

**Rate limiting with Sorted Set:**
```php
// Sliding window rate limiter — allows 100 requests per minute
$key = "rate:user:{$userId}";
$now = microtime(true);
$window = 60;

$redis->multi()
    ->zRemRangeByScore($key, 0, $now - $window)  // Remove old requests
    ->zAdd($key, $now, $now . rand())             // Add current request
    ->zCard($key)                                 // Count in window
    ->expire($key, $window)
    ->exec();
```

---

**Q5. What are common Redis anti-patterns that cause performance problems?**

**Answer:**

| Anti-pattern | Problem | Solution |
|-------------|---------|---------|
| **KEYS \*** in production | Blocks Redis (single-threaded) for seconds on large keyspaces | Use `SCAN` cursor-based iteration |
| **Huge values** | Serializing large objects increases network bandwidth + memory | Keep values small, store IDs not full objects |
| **No expiry on keys** | Memory fills up, eviction kicks in unexpectedly | Always set TTL on cache keys |
| **N individual GETs** | N network round-trips | Use `MGET`, pipeline, or Lua script |
| **Storing large lists without size limit** | Unbounded memory growth | Use `LTRIM` to cap list size |
| **Hot keys** | One key handles all traffic, single-threaded bottleneck | Shard hot keys with prefix + random suffix |

```bash
# ❌ Bad — blocks server
KEYS user:*

# ✅ Good — non-blocking cursor scan
SCAN 0 MATCH user:* COUNT 100
# Returns cursor + results, repeat until cursor = 0

# ❌ Bad — N round-trips
for id in user_ids:
    redis.GET(f"user:{id}")

# ✅ Good — single round-trip
redis.MGET(*[f"user:{id}" for id in user_ids])

# Pipeline — batch commands, one network round-trip
pipe = redis.pipeline()
for id in user_ids:
    pipe.get(f"user:{id}")
results = pipe.execute()
```

---

**Q6. How would you cache database query results in Redis with proper invalidation?**

**Answer:**

**Strategy 1: TTL-based (simplest)**
```php
$cacheKey = "products:category:{$categoryId}:page:{$page}";
$products = Cache::remember($cacheKey, 3600, function () use ($categoryId, $page) {
    return Product::where('category_id', $categoryId)->paginate(20);
});
// Stale data possible for up to 1 hour
```

**Strategy 2: Event-based invalidation (most accurate)**
```php
// Observer invalidates cache when product changes
class ProductObserver {
    public function saved(Product $product): void {
        Cache::forget("products:category:{$product->category_id}:*");
        Cache::tags(['products', "category:{$product->category_id}"])->flush();
    }
}

// Using cache tags (requires Redis driver)
$products = Cache::tags(['products', "category:{$categoryId}"])
    ->remember("list-page-{$page}", 3600, fn() => Product::paginate(20));
```

**Strategy 3: Write-through**
```php
// Always write to cache AND DB together
public function updateProduct(Product $product, array $data): Product {
    $product->update($data);
    Cache::put("product:{$product->id}", $product, 3600);  // Update cache
    return $product;
}
```

**Strategy 4: Cache-aside with warming**
```php
// Pre-populate cache on deploy for hot data
php artisan cache:warm --products --categories
```

**Trade-off summary:** TTL = simple but stale. Event-based = accurate but complex. Write-through = consistent but write overhead. Choose based on how stale data affects your use case.

