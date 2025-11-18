
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