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

