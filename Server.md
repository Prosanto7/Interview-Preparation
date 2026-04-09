

## 🌐 **Introduction**

| Feature         | **Apache**                               | **Nginx**                                      |
|-----------------|------------------------------------------|------------------------------------------------|
| Full Name       | Apache HTTP Server                       | Engine-X (pronounced “Engine-X”)               |
| Initial Release | 1995                                     | 2004                                           |
| Developer       | Apache Software Foundation               | Igor Sysoev / Now F5 Inc.                      |
| Written In      | C                                        | C                                              |
| License         | Apache License 2.0                       | 2-clause BSD license                           |

---

## ⚙️ **Architecture**

| Feature             | Apache                                       | Nginx                                           |
|---------------------|----------------------------------------------|-------------------------------------------------|
| Request Handling    | Process/thread-based                         | Event-driven, asynchronous                      |
| Concurrency Model   | `prefork`, `worker`, or `event` modules      | Non-blocking, single-threaded event loop        |
| Static File Serving | Slower under high load                       | Extremely fast and efficient                    |
| Dynamic Content     | Handles directly with modules                | Uses FastCGI or proxy to app servers            |

---

## 🚀 **Performance**

| Scenario                    | Apache                              | Nginx                                       |
|-----------------------------|--------------------------------------|--------------------------------------------|
| Static File Performance     | Slower                              | Much faster due to event-based architecture |
| Dynamic Content (PHP, etc.) | Direct or via mod_php               | FastCGI (external PHP processor)            |
| High Concurrent Users       | Higher memory usage per request     | Lightweight, handles thousands of requests  |

---

## 🔧 **Configuration & Flexibility**

| Feature                    | Apache                                 | Nginx                                   |
|----------------------------|----------------------------------------|-----------------------------------------|
| `.htaccess` Support        | ✅ Yes                                 | ❌ No                                   |
| Dynamic Module Loading     | ✅ Yes                                 | ✅ Yes (since version 1.9+)             |
| Granular Directory Config  | ✅ Yes (`.htaccess`, per-directory)    | ❌ No, centralized config               |
| Ease of Use                | Easier for beginners (familiar syntax) | Slightly steeper learning curve         |

---

## 🛡️ **Security**

| Security Feature         | Apache                          | Nginx                                       |
|--------------------------|---------------------------------|---------------------------------------------|
| TLS/SSL Support          | ✅ Yes                          | ✅ Yes                                      |
| Rate Limiting            | Basic                           | Advanced built-in rate limiting features    |
| Web Application Firewall | ModSecurity supported           | ModSecurity via Nginx + ModSecurity combo   |

---

## 🔄 **Reverse Proxy & Load Balancing**

| Capability               | Apache                             | Nginx                                          |
|--------------------------|-------------------------------------|------------------------------------------------|
| Reverse Proxy            | ✅ Available with `mod_proxy`        | ✅ Designed as a reverse proxy from the start   |
| Load Balancing           | ✅ Yes                              | ✅ Yes (least_conn, ip_hash, round-robin, etc.) |
| Caching                  | Basic                              | Advanced and high-performance caching          |

---

## 🔌 **Modules & Ecosystem**

| Modules/Extensions       | Apache                                | Nginx                                       |
|--------------------------|----------------------------------------|---------------------------------------------|
| Community Modules        | Huge ecosystem                         | Smaller but growing                         |
| Third-Party Integrations | Easy to find plugins                   | Requires recompilation for some extensions  |

---

## 📊 **Use Cases and Real-Life Examples**

| Scenario                        | Recommended Server | Why?                                                      |
|----------------------------------|--------------------|------------------------------------------------------------|
| Hosting CMS (WordPress, Joomla) | Apache             | .htaccess compatibility, simple setup                      |
| High-traffic static site         | Nginx              | Handles thousands of concurrent connections efficiently     |
| Reverse Proxy + App Server      | Nginx              | Optimized for proxying to Node.js, PHP-FPM, etc.            |
| Legacy .htaccess needs          | Apache             | Only Apache supports `.htaccess` files                      |
| Microservices/Container setups  | Nginx              | Lightweight, fast to spin up and configure                  |

---

## ✅ **Advantages**

### Apache

- ✅ Mature and well-documented
- ✅ `.htaccess` for per-directory overrides
- ✅ Wide module support
- ✅ Great PHP integration (mod_php)
- ✅ Easy to use and configure for beginners

### Nginx

- ✅ Extremely fast, especially for static content
- ✅ Handles 10k+ concurrent connections with minimal resources
- ✅ Built-in reverse proxy and load balancing
- ✅ Superior for high-performance needs
- ✅ Low memory footprint

---

## ❌ **Disadvantages**

### Apache

- ❌ Consumes more memory under heavy load
- ❌ Process/thread-based model doesn’t scale well
- ❌ Slower than Nginx for static files

### Nginx

- ❌ No `.htaccess` (central config only)
- ❌ Slightly more complex to configure for dynamic content
- ❌ Some modules require recompilation

---

## 🤔 **When to Use What?**

| Use Case                                  | Use Apache If…                          | Use Nginx If…                               |
|-------------------------------------------|-----------------------------------------|---------------------------------------------|
| You need `.htaccess` support              | ✅ Yes                                  | ❌ No                                        |
| You're running shared hosting             | ✅ Apache is dominant                    | ❌ Less common                               |
| You're serving tons of static content     | ❌ Might struggle                        | ✅ Blazing fast                              |
| You’re setting up a reverse proxy         | ❌ Can work, but not optimized           | ✅ Nginx is purpose-built                    |
| You need best PHP-FPM support             | ❌ mod_php limits concurrency            | ✅ Works great with PHP-FPM                  |
| You want a small memory footprint         | ❌ More overhead                         | ✅ Nginx is lightweight                      |

---

## 🛠️ **Best of Both Worlds: Apache + Nginx Combo**

Many production setups use **Nginx as a reverse proxy** in front of Apache:

- Nginx handles static files and initial routing
- Apache handles dynamic content (PHP, etc.)

This gives you performance + compatibility benefits.

---

## 🧠 Final Verdict

- **Choose Apache** if:
  - You need `.htaccess`
  - You want simplicity and compatibility with shared hosting
  - You're already comfortable with Apache's ecosystem

- **Choose Nginx** if:
  - You want high performance and efficiency
  - You’re building scalable, containerized, or microservice-based apps
  - You’re working with Node.js, Python, or PHP-FPM backends

---

## 📚 References

- Apache HTTP Server Docs: https://httpd.apache.org/docs/
- Nginx Documentation: https://nginx.org/en/docs/
- Nginx Load Balancing: https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/
- Apache mod_proxy: https://httpd.apache.org/docs/2.4/mod/mod_proxy.html

---

## 🎯 Server Interview Questions — Critical Scenarios

---

### ⚡ Performance & High Traffic

**Q1. How does Nginx handle 10,000 concurrent connections differently from Apache?**

**Answer:**
The core difference is the **concurrency model**:

| Aspect | Apache (prefork/worker) | Nginx |
|--------|------------------------|-------|
| **Model** | Process/thread per connection | Event-driven, async non-blocking |
| **Memory** | ~8MB per process (prefork) | ~2.5MB total for all workers |
| **Concurrency** | Struggles above ~1000 concurrent | Handles 10k+ with ease |
| **Blocking I/O** | Worker blocked waiting for disk/network | Event loop continues handling other requests |
| **Static files** | Slower | Extremely fast (sendfile syscall) |
| **Dynamic content** | mod_php runs in-process (fast) | Must proxy to PHP-FPM (slight overhead) |

**Nginx event loop:**
```
Master Process → forks Worker Processes (= CPU cores)
Each Worker runs an event loop:
  accept() → read request → proxy to backend → wait for response
  While waiting → handle OTHER connections simultaneously
  (no thread/process blocked)
```

**Apache C10K problem:** Each thread/process waits for slow clients. At 10,000 concurrent slow clients → 10,000 threads → RAM exhaustion.

**Nginx solution:** Asynchronous I/O — one worker handles thousands of connections. Slow clients don't consume threads.

**When Apache still wins:**
- `.htaccess` per-directory config (dynamic config without restart)
- mod_php (no FPM overhead) in low-concurrency legacy apps
- Shared hosting environments

---

**Q2. How do you configure Nginx for optimal performance under high load?**

**Answer:**

```nginx
# /etc/nginx/nginx.conf — Production-optimized

worker_processes auto;              # One per CPU core
worker_rlimit_nofile 65535;         # Max open files per worker

events {
    worker_connections 4096;        # Max connections per worker
    use epoll;                      # Linux: most efficient I/O model
    multi_accept on;                # Accept all pending connections at once
}

http {
    gzip on;
    gzip_comp_level 2;              # Level 6+ wastes CPU, minimal gain
    gzip_types text/plain text/css application/json application/javascript;
    gzip_min_length 1000;

    open_file_cache max=10000 inactive=30s;
    open_file_cache_valid 60s;

    keepalive_timeout 65;
    keepalive_requests 1000;        # Reuse connections for 1000 requests
    sendfile on;                    # Zero-copy file transfer
    tcp_nopush on;                  # Batch TCP packets (with sendfile)
    tcp_nodelay on;                 # Disable Nagle for small packets

    client_body_buffer_size 10K;
    client_header_buffer_size 1k;
    client_max_body_size 8m;

    client_body_timeout 12;
    client_header_timeout 12;
    send_timeout 10;

    limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;
    limit_conn_zone $binary_remote_addr zone=addr:10m;
}
```

**PHP-FPM upstream pool:**
```nginx
upstream php-fpm {
    server unix:/run/php/php8.3-fpm.sock;  # Unix socket — faster than TCP
    keepalive 32;                            # Reuse FPM connections
}
```

---

**Q3. Explain load balancing algorithms — when would you use each?**

**Answer:**

| Algorithm | How It Works | Best For |
|-----------|-------------|---------|
| **Round Robin** | Requests distributed equally in sequence | Stateless apps, similar server specs |
| **Weighted Round Robin** | More requests to more powerful servers | Mixed server hardware |
| **Least Connections** | Send to server with fewest active connections | Long-lived connections (WebSockets) |
| **IP Hash** | Client IP → always same server | Session affinity (legacy apps) |
| **Least Time** | Combine least connections + fastest response | Heterogeneous backends |
| **Random** | Random server selection | Large clusters |

```nginx
# Least Connections — better than Round Robin for variable-length requests
upstream backend {
    least_conn;
    server 10.0.0.1:8080 weight=3;
    server 10.0.0.2:8080 weight=1;
    server 10.0.0.3:8080 backup;   # Only used when others fail
}
```

**Avoid IP Hash when:** You're behind a NAT/proxy — all users appear as same IP → one server overloaded.

---

### 🔐 Security

**Q4. How do you harden an Nginx server for production?**

**Answer:**

```nginx
# Security headers
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
add_header Content-Security-Policy "default-src 'self'; object-src 'none'" always;
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;

server_tokens off;  # Hide Nginx version

# Block access to hidden files (.git, .env, etc.)
location ~ /\. {
    deny all;
    return 404;
}

# Rate limiting on sensitive endpoints
location /api/login {
    limit_req zone=api burst=5 nodelay;
    limit_conn addr 10;
    proxy_pass http://backend;
}
```

**SSL/TLS hardening:**
```nginx
ssl_protocols TLSv1.2 TLSv1.3;
ssl_prefer_server_ciphers off;
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 1d;
ssl_session_tickets off;     # Disable — forward secrecy
ssl_stapling on;             # OCSP stapling
ssl_stapling_verify on;
```

---

**Q5. What is the difference between a Reverse Proxy, Forward Proxy, and API Gateway?**

**Answer:**

```
Forward Proxy:
  Client → [Forward Proxy] → Internet
  (Client side — hides client identity)
  Use: Corporate proxy, anonymity, content filtering

Reverse Proxy:
  Client → [Reverse Proxy] → Backend Servers
  (Server side — hides backend identity)
  Use: Load balancing, SSL termination, caching

API Gateway:
  Client → [API Gateway] → Microservices
  (Advanced reverse proxy with business logic)
  Use: Auth, rate limiting, routing, request transformation, monitoring
```

| Feature | Reverse Proxy | API Gateway |
|---------|--------------|------------|
| **Load balancing** | ✅ | ✅ |
| **SSL termination** | ✅ | ✅ |
| **Authentication** | Basic (IP allow-list) | Advanced (JWT, OAuth2, API keys) |
| **Rate limiting** | Basic (Nginx limit_req) | Per-user, per-endpoint, per-plan |
| **Request transformation** | ❌ | ✅ |
| **Analytics** | ❌ | ✅ (per-consumer metrics) |
| **Examples** | Nginx, HAProxy | Kong, AWS API Gateway, Traefik |

---

**Q6. PHP-FPM tuning — how do you set pool size for a high-traffic server?**

**Answer:**

```ini
; /etc/php/8.3/fpm/pool.d/www.conf

[www]
pm = dynamic                      ; Prefer over static for variable traffic
pm.max_children = 50              ; Formula: RAM / per-process RAM
                                  ; 4GB RAM / 80MB per PHP process = 50
pm.start_servers = 10             ; 20% of max
pm.min_spare_servers = 5          ; Keep 10% ready
pm.max_spare_servers = 20         ; Kill idle above this
pm.max_requests = 500             ; Restart worker after 500 requests (memory leak prevention)

pm.status_path = /status
slowlog = /var/log/php-fpm-slow.log
request_slowlog_timeout = 2s
```

**Calculation formula:**
- Check per-process memory: `ps -ylC php-fpm8.3 | awk '{if (NR!=1) sum+=$8} END {print sum/NR/1024, "MB"}'`
- `max_children = Available RAM * 0.8 / avg process memory`

**`pm` modes compared:**
| Mode | Behavior | Best For |
|------|----------|---------|
| **static** | Fixed number of children always running | Consistent high traffic, no spawn latency |
| **dynamic** | Scales between min and max | Variable traffic |
| **ondemand** | Spawn on request, kill when idle | Low-traffic/dev — bad for production (spawn latency) |

---

**Q7. How does Nginx proxy caching work and how do you handle cache invalidation?**

**Answer:**

```nginx
proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=app_cache:10m
                 max_size=1g inactive=60m use_temp_path=off;

server {
    location /api/ {
        proxy_cache app_cache;
        proxy_cache_key "$scheme$request_method$host$request_uri";
        proxy_cache_valid 200 10m;
        proxy_cache_valid 404 1m;
        proxy_cache_bypass $http_cache_control;  # Honor Cache-Control: no-cache
        add_header X-Cache-Status $upstream_cache_status;  # HIT/MISS/BYPASS

        proxy_pass http://backend;
    }
}
```

**Cache invalidation strategies:**

| Method | Mechanism | Use Case |
|--------|-----------|---------|
| **TTL expiry** | `proxy_cache_valid 200 10m` | Static/semi-static content |
| **Cache-Control headers** | Backend sets `max-age`, `no-cache` | Fine-grained control per endpoint |
| **URL versioning** | `/api/v2/users?v=20240101` | Bust cache by changing URL |
| **Purge module** | nginx-cache-purge module | Immediate invalidation on update |

**`$upstream_cache_status` values:** `HIT`, `MISS`, `BYPASS`, `EXPIRED`, `STALE`, `UPDATING`, `REVALIDATED`