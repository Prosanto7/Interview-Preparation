
# How Internet Works!


---

## 🌍 **1. User Enters a URL**

For example:
`https://www.example.com`

---

## 🌐 **2. DNS Lookup (Domain Name System)**

The browser doesn’t understand names like `www.example.com`; it needs an IP address (like `93.184.216.34`).

* The browser checks its cache or contacts a **DNS server** to resolve the domain to an IP.
* If not cached, the request goes up the DNS hierarchy (local → ISP → root → TLD → authoritative server).

🧠 Think of DNS like the internet’s phone book.

---

## 📡 **3. Browser Sends an HTTP(S) Request**

Once the IP is known:

* The browser opens a connection (often over HTTPS/TCP) to the web server.
* It sends a **GET request** for the page:

  ```
  GET / HTTP/1.1
  Host: www.example.com
  ```

---

## 🖥️ **4. Server Receives the Request**

The server (running software like Apache, Nginx, or Node.js) receives the request and decides how to handle it:

* If it's a static website, it might return an `index.html` file.
* If it's dynamic (e.g., PHP/Laravel, Node.js), it may:

  * Route the request
  * Query a database
  * Run some business logic
  * Generate an HTML or JSON response

---

## 💾 **5. Server Sends Back a Response**

The server sends a response like:

```
HTTP/1.1 200 OK
Content-Type: text/html

<html>
  <body>Welcome to Example.com</body>
</html>
```

Or for an API:

```json
{
  "message": "Welcome!",
  "data": [...]
}
```

---

## 📥 **6. Browser Receives the Response**

* The browser parses the HTML.
* It then makes additional requests for CSS, JavaScript, and images.
* These assets are also requested using the same process (DNS → TCP → HTTP).

---

## 🧠 **7. Browser Renders the Page**

* HTML builds the page structure (DOM).
* CSS styles the layout and design.
* JavaScript adds interactivity.

This is where the user finally **sees the website**!

---

## 🧵 Summary: Step-by-Step

```
User types URL
    ↓
Browser resolves DNS → IP
    ↓
Sends HTTP request to server
    ↓
Server processes request and returns HTML/JSON
    ↓
Browser receives response, downloads assets
    ↓
Browser renders and displays web page
```

## 📚 References

    - MDN Web Docs: HTTP — https://developer.mozilla.org/en-US/docs/Web/HTTP
    - RFC 7231: HTTP/1.1 Semantics — https://datatracker.ietf.org/doc/html/rfc7231
    - RFC 9110–9114: Updated HTTP Semantics and HTTP/2 — https://www.rfc-editor.org/rfc/rfc9110
    - DNS (RFC 1034/1035) — https://www.rfc-editor.org/rfc/rfc1034 and https://www.rfc-editor.org/rfc/rfc1035

---

## 🎯 Internet Interview Questions — Critical Scenarios

---

### 🌐 HTTP & Protocols

**Q1. What are the key differences between HTTP/1.1, HTTP/2, and HTTP/3?**

**Answer:**

| Feature | HTTP/1.1 | HTTP/2 | HTTP/3 |
|---------|---------|--------|--------|
| **Transport** | TCP | TCP | QUIC (UDP-based) |
| **Multiplexing** | ❌ Head-of-line blocking | ✅ Multiple streams per connection | ✅ Independent streams (no TCP HOL blocking) |
| **Header compression** | None (repeated headers waste bandwidth) | HPACK compression | QPACK compression |
| **Connection count** | 6 parallel connections per host | 1 connection, multiple streams | 1 QUIC connection, multiple streams |
| **Server Push** | ❌ | ✅ Server can push resources proactively | ✅ |
| **Latency** | High (TCP + TLS handshake per connection) | Medium (1 connection, TLS 1.2) | Low (0-RTT connection for repeat visits) |
| **Adoption** | Universal | ~60% of web | ~30% of web (growing) |

**HTTP/2 multiplexing — solving HOL blocking:**
```
HTTP/1.1: 6 connections, each handles 1 request at a time
  Connection 1: [Request A ----DONE]
  Connection 2: [Request B waiting for A]

HTTP/2: 1 connection, streams interleaved
  Stream 1: [Header][Data chunk 1][Data chunk 3]
  Stream 2: [Header][Data chunk 2][Data chunk 4]
  (No waiting — both streams progress simultaneously)
```

**HTTP/3 advantage — eliminating TCP HOL blocking:**
```
HTTP/2 problem: TCP packet loss stalls ALL streams (TCP-level HOL)
HTTP/3 QUIC: Packet loss only stalls the affected stream
  Other streams continue unaffected
```

**When to care:** HTTP/3 benefits are most visible on lossy networks (mobile, satellite). Datacenters with reliable fiber may see minimal HTTP/2 vs HTTP/3 difference.

---

**Q2. What happens during a TLS handshake and how does TLS 1.3 improve upon 1.2?**

**Answer:**

**TLS 1.2 handshake (2 round trips):**
```
Client → Server: ClientHello (supported ciphers, TLS version, random)
Server → Client: ServerHello + Certificate + ServerHelloDone
Client → Server: ClientKeyExchange + ChangeCipherSpec + Finished
Server → Client: ChangeCipherSpec + Finished
--- Encrypted data begins ---
(2 full round trips = 2× network latency before first byte)
```

**TLS 1.3 handshake (1 round trip):**
```
Client → Server: ClientHello + Key Share (guesses server's preferred algorithm)
Server → Client: ServerHello + Key Share + Certificate + Finished
Client → Server: Finished
--- Encrypted data begins ---
(1 round trip — data flows 1 RTT sooner)
```

**TLS 1.3 0-RTT (zero round trip for repeat connections):**
```
Client has session ticket from previous connection
Client → Server: ClientHello + Early Data (0-RTT data) immediately
(Risk: 0-RTT data is vulnerable to replay attacks — use only for idempotent requests)
```

**TLS 1.3 security improvements:**
- Removed weak cipher suites (RC4, DES, 3DES, MD5, SHA-1)
- Removed RSA key exchange (no forward secrecy)
- All key exchanges use ephemeral Diffie-Hellman (perfect forward secrecy always)
- Encrypted TLS handshake metadata (certificate is now encrypted)

---

**Q3. Explain DNS record types and how DNS resolution works step by step.**

**Answer:**

**DNS Record Types:**

| Record | Purpose | Example |
|--------|---------|---------|
| **A** | Domain → IPv4 address | `example.com → 93.184.216.34` |
| **AAAA** | Domain → IPv6 address | `example.com → 2606:2800:220:1:...` |
| **CNAME** | Alias to another domain | `www.example.com → example.com` |
| **MX** | Mail server for domain | `example.com → mail.example.com (priority 10)` |
| **TXT** | Arbitrary text (SPF, DKIM, domain verification) | `v=spf1 include:_spf.google.com ~all` |
| **NS** | Name servers for domain | `example.com → ns1.registrar.com` |
| **SOA** | Start of Authority — zone metadata | TTL, admin email, serial number |
| **PTR** | Reverse DNS (IP → domain) | `34.216.184.93.in-addr.arpa → example.com` |
| **SRV** | Service location | `_http._tcp.example.com → server port` |
| **CAA** | Allowed Certificate Authorities | Only Let's Encrypt can issue for this domain |

**Resolution flow:**
```
1. Browser cache (previously resolved? TTL not expired?)
2. OS cache (/etc/hosts, systemd-resolved)
3. Recursive Resolver (ISP or 8.8.8.8)
   a. Root nameserver (.): "com. is at 192.5.6.30"
   b. TLD nameserver (com.): "example.com is at ns1.registrar.com"
   c. Authoritative nameserver (ns1.registrar.com): "example.com = 93.184.216.34"
4. Response cached with TTL
5. IP returned to browser
```

**DNS propagation delays:** When you change DNS records, old TTL must expire across all resolvers worldwide. Reduce TTL before planned changes (e.g., from 86400s to 300s).

---

**Q4. How does CDN (Content Delivery Network) work and what are its limitations?**

**Answer:**

```
Without CDN:
  User in Australia → Origin server in USA
  Latency: 150-200ms per request × multiple assets = slow page load

With CDN:
  User in Australia → Edge server in Sydney
  Latency: 5-10ms
  If cache miss → Sydney edge → USA origin → cache in Sydney for future requests
```

**How CDN caches content:**
```
1. User requests resource: GET https://cdn.example.com/logo.png
2. Edge server checks local cache
   HIT: Serve immediately, update freshness timestamp
   MISS: Fetch from origin, store with Cache-Control headers, serve
3. Cache-Control: max-age=86400 → cached for 24 hours at edge
4. CDN honors: no-cache, no-store, private (won't cache private responses)
```

**CDN use cases:**
- Static assets: images, CSS, JS, fonts
- Video streaming (HLS/DASH segments cached at edge)
- API acceleration (cache read-heavy API responses)
- DDoS mitigation (edge absorbs attack traffic before origin)
- GeoDNS (route users to nearest edge)

**CDN limitations:**
- **Dynamic/personalized content:** Can't cache user-specific responses (use ESI or micro-caching)
- **Cache invalidation latency:** Propagating cache purge to all edge nodes takes time
- **Cost:** High egress bandwidth is expensive
- **Privacy:** Third-party CDN sees all traffic (use your own CDN or GDPR-compliant provider)
- **Origin protection:** DDoS attackers who know origin IP can bypass CDN

---

**Q5. What is the difference between HTTP status codes 301, 302, 307, and 308?**

**Answer:**

| Code | Name | Method preserved? | Permanent? | Use Case |
|------|------|------------------|-----------|---------|
| **301** | Moved Permanently | ❌ POST→GET | ✅ Yes | Permanent redirect, SEO-friendly |
| **302** | Found (temp) | ❌ POST→GET | ❌ No | Temporary redirect, login flows |
| **307** | Temporary Redirect | ✅ POST stays POST | ❌ No | Temporary API endpoint change |
| **308** | Permanent Redirect | ✅ POST stays POST | ✅ Yes | Permanent API endpoint change |

**Key distinction:**
- 301/302: Browser may change POST → GET on redirect (historical browsers)
- 307/308: Browser **must** preserve method — POST stays POST

```
Login form POST /login → 302 → GET /dashboard  (correct: method changes)
API POST /v1/orders → 308 → POST /v2/orders  (correct: keeps POST method)
```

**301 SEO implication:** Search engines transfer "link juice" (PageRank) from old URL to new. Use 301 when permanently moving pages. 302 signals temporary → search engine keeps indexing old URL.

---

**Q6. What is CORS and how does the preflight mechanism work?**

**Answer:**

CORS (Cross-Origin Resource Sharing) allows or restricts web applications at one origin from making requests to a different origin.

**Same-origin policy:** By default, browsers block cross-origin AJAX requests. CORS is the mechanism to selectively relax this.

**Simple requests** (GET, POST with form-data) — no preflight:
```
Browser → GET https://api.other.com/data
Server → Access-Control-Allow-Origin: https://myapp.com
Browser → Allows JS to read response ✅
```

**Preflight for non-simple requests** (custom headers, PUT/DELETE, JSON body):
```
Browser → OPTIONS https://api.other.com/data
          Origin: https://myapp.com
          Access-Control-Request-Method: DELETE
          Access-Control-Request-Headers: Authorization

Server  → 204 No Content
          Access-Control-Allow-Origin: https://myapp.com
          Access-Control-Allow-Methods: GET, POST, DELETE
          Access-Control-Allow-Headers: Authorization
          Access-Control-Max-Age: 86400  (cache preflight for 24h)

Browser → DELETE https://api.other.com/data  (actual request)
```

**Nginx CORS configuration:**
```nginx
location /api/ {
    if ($request_method = 'OPTIONS') {
        add_header 'Access-Control-Allow-Origin' 'https://myapp.com';
        add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, DELETE, OPTIONS';
        add_header 'Access-Control-Allow-Headers' 'Authorization, Content-Type';
        add_header 'Access-Control-Max-Age' 86400;
        return 204;
    }
    add_header 'Access-Control-Allow-Origin' 'https://myapp.com' always;
    proxy_pass http://backend;
}
```

**`Access-Control-Allow-Origin: *`** — allows all origins but **blocks cookies/credentials**. Use specific origin for authenticated APIs.