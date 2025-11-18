
## 🔐 **What is a Token?**

A **token** is a small piece of data that represents a user or application identity and is often used for authentication and authorization.

---

## 🧩 **Types of Tokens**

| Token Type | Description | Used For |
|------------|-------------|----------|
| **Session Token** | Token generated after user login, stored on the server | User sessions (web apps) |
| **CSRF Token** | Random token used to prevent Cross-Site Request Forgery | Web form security |
| **JWT (JSON Web Token)** | Compact, signed token that carries data (claims) | API authentication |
| **OAuth Token** | Authorization token from a third party (e.g., Google) | Delegated access |
| **Refresh Token** | Long-lived token used to obtain new JWTs | Token renewal |
| **API Key** | Simple string used to identify the calling app | Service-to-service or API calls |
| **Personal Access Token** | Like an API key but user-specific | Auth with developer apps or APIs |

---

## 🧠 **1. Session Token**

- **Where Used:** Web applications
- **Stored:** On server (usually in memory or database)
- **Example in Laravel:**
  - Laravel uses **session ID cookies** (like `laravel_session`) to manage user sessions.

```php
// session-based login
session(['user_id' => $user->id]);
```

- ✅ **Pros:** Secure, server-controlled  
- ❌ **Cons:** Doesn’t scale well for APIs; tied to server state

---

## 🛡️ **2. CSRF Token (Cross-Site Request Forgery Token)**

- **Where Used:** HTML forms and AJAX requests
- **Goal:** Prevent attackers from forging requests from authenticated users

```html
<form method="POST">
    @csrf
</form>
```

- Laravel auto-generates it and verifies it on POST, PUT, DELETE, etc.

- ✅ **Pros:** Crucial for web form security  
- ❌ **Cons:** Not useful for APIs (for that, use CORS + tokens)

---

## 🔑 **3. JWT (JSON Web Token)**

- **Structure:** `Header.Payload.Signature` (Base64 encoded)
- **Self-contained:** Contains user data (claims) and expiry

```json
{
  "sub": 12345,
  "name": "John Doe",
  "exp": 1712345678
}
```

- Laravel packages like `tymon/jwt-auth` provide JWT support.

- ✅ **Pros:** Stateless, ideal for APIs, scalable  
- ❌ **Cons:** Cannot be easily revoked unless stored in a blocklist

---

## 🔄 **4. OAuth Token**

- **Used in:** Login via Google, Facebook, GitHub etc.
- **Types:**
  - Access Token – for accessing data
  - Refresh Token – to renew expired access token

Example:
```bash
Bearer eyJhbGciOi...
```

- ✅ **Pros:** Delegated access, widely adopted  
- ❌ **Cons:** Complex flow (especially for beginners)

---

## 🔁 **5. Refresh Token**

- Used to **refresh an access token** without logging in again
- Longer expiry (sometimes months)

```json
{
  "access_token": "short-lived",
  "refresh_token": "long-lived"
}
```

- Common in JWT-based auth systems

- ✅ **Pros:** Better user experience  
- ❌ **Cons:** Must be securely stored (e.g., in HttpOnly cookies)

---

## 🧪 **6. API Key**

- Simple string used to identify & authenticate a project or app
- Passed via headers or query params

```bash
GET /data?api_key=abc123
```

- ✅ **Pros:** Easy to implement  
- ❌ **Cons:** No user context; can be stolen if exposed

---

## 👤 **7. Personal Access Token (PAT)**

- Used for programmatic access (e.g., GitHub, Laravel Sanctum)
- Represents a user, not an app

```bash
Authorization: Bearer YOUR_PERSONAL_ACCESS_TOKEN
```

- ✅ **Pros:** Fine-grained access; user-specific  
- ❌ **Cons:** Should be treated like a password

---

## 📊 **Comparison Table**

| Feature                | Session Token | CSRF Token | JWT          | OAuth Token | Refresh Token | API Key      | PAT            |
|------------------------|----------------|------------|--------------|--------------|----------------|---------------|----------------|
| **Stateless**          | ❌             | ❌         | ✅           | ✅           | ✅             | ✅            | ✅             |
| **Stored On**          | Server         | Server     | Client       | Client       | Client         | Client        | Client         |
| **Used For**           | Auth (Web)     | Form Security | API Auth  | 3rd Party Login | Token Renewal | Service Auth | Developer APIs |
| **Revocable**          | ✅             | ✅         | ❌ (unless stored) | ✅     | ✅             | ✅            | ✅             |
| **Security**           | High (server-side) | High    | Medium (signed) | High    | High           | Low           | High           |
| **Bearer Format**      | ❌             | ❌         | ✅           | ✅           | ✅             | Optional      | ✅             |
| **Laravel Usage**      | Built-in       | Built-in   | JWT Auth Lib | Socialite     | JWT Auth Lib   | Custom Auth   | Laravel Sanctum |

---

## 🧠 Real-Life Examples

- **Session Token**: After login, Laravel stores your session to keep you logged in.
- **CSRF Token**: Laravel generates CSRF tokens in forms to prevent fake submissions.
- **JWT**: Mobile app sends JWT to authenticate user with API.
- **OAuth Token**: Logging into an app via Google/Facebook.
- **Refresh Token**: Mobile app refreshes session silently in the background.
- **API Key**: Weather APIs like OpenWeatherMap use API keys.
- **PAT**: GitHub uses Personal Access Tokens for Git over HTTPS.

---

## 🧾 Summary

- Use **Session Tokens** for traditional web apps.
- Use **JWT + Refresh Token** for modern REST APIs or SPAs.
- Use **OAuth Tokens** when delegating login to external providers.
- Use **CSRF Tokens** to protect your forms.
- Use **API Keys** for internal or public APIs with simple access needs.
- Use **PATs** for developer integrations.

---

## 📚 References

- JWT (RFC 7519): https://datatracker.ietf.org/doc/html/rfc7519
- OAuth 2.0 (RFC 6749): https://datatracker.ietf.org/doc/html/rfc6749
- OAuth 2.0 Bearer Token (RFC 6750): https://datatracker.ietf.org/doc/html/rfc6750
- OWASP CSRF Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html
- OWASP Session Management: https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html
- Laravel Sanctum: https://laravel.com/docs/sanctum
- Laravel Passport: https://laravel.com/docs/passport
