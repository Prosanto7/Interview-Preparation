
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

---

## 🎯 Tokens Interview Questions — Critical Scenarios

---

### 🔐 JWT Deep Dive

**Q1. What are the security vulnerabilities in JWT and how do you mitigate each?**

**Answer:**

| Attack | Vulnerability | Mitigation |
|--------|--------------|-----------|
| **alg:none attack** | Server accepts unsigned token if `alg` is set to `none` | Whitelist allowed algorithms — never accept `none` |
| **Algorithm confusion** | RS256 token verified as HS256 using public key as secret | Pin algorithm server-side, don't read `alg` from token header |
| **Weak secret** | HS256 secret brute-forced (short/guessable) | Use 256-bit+ random secret; prefer RS256 for distributed systems |
| **Missing expiry** | Tokens never expire | Always set `exp` claim; use short-lived access tokens (15min) |
| **No token revocation** | Compromised tokens remain valid until expiry | Maintain token denylist in Redis; use short TTL |
| **Sensitive data in payload** | JWT payload is base64 decoded, not encrypted | Never store PII in JWT unless using JWE (encrypted JWT) |
| **Replay attacks** | Token intercepted and reused | Use HTTPS always; short `exp`; bind token to client fingerprint |

```php
// ❌ Vulnerable — reads algorithm from token header
$decoded = JWT::decode($token, $key, $token->header->alg);

// ✅ Safe — pin algorithm server-side
$decoded = JWT::decode($token, new Key($publicKey, 'RS256'));
```

**"alg:none" attack:**
```
# Attacker modifies header to {"alg":"none","typ":"JWT"}
# Changes payload claims: {"sub":1,"role":"admin"}
# Removes signature
# Vulnerable servers accept: header.payload. (empty signature)
```

---

**Q2. When should you use JWT vs Opaque tokens vs Session tokens?**

**Answer:**

| Token Type | How It Works | Validation | Storage |
|-----------|-------------|-----------|---------|
| **JWT (stateless)** | Self-contained, contains claims | Signature verification (no DB hit) | Client-side (cookie/localStorage) |
| **Opaque token** | Random string, data in server-side store | DB/Redis lookup required | Server-side (Redis/DB) |
| **Session token** | Session ID mapped to server-side session data | DB/file lookup | Server-side + cookie |

**Choose JWT when:**
- Distributed/microservice systems (no shared session store)
- Short-lived tokens (15min access tokens)
- Stateless auth across services (API-to-API)
- Mobile apps / SPAs consuming REST APIs

**Choose Opaque tokens when:**
- Need instant revocation (logout must work immediately)
- Token must carry no readable data
- OAuth 2.0 resource server pattern (introspection endpoint)

**Choose Sessions when:**
- Traditional web apps with server-side rendering
- Need to store large amounts of server-side state
- Strong revocation requirement (admin can terminate any session)

**Hybrid approach (recommended for most apps):**
```
Access Token:  JWT, expires in 15 minutes (stateless, fast)
Refresh Token: Opaque, expires in 30 days (stored in DB, revocable)

Flow:
  Login → issue both tokens
  Request → use JWT (no DB hit)
  JWT expires → use Refresh token → get new JWT
  Logout → invalidate Refresh token in DB
```

---

**Q3. Explain token rotation and refresh token reuse detection.**

**Answer:**

Token rotation means issuing a new refresh token on every use. The old refresh token is invalidated. This prevents refresh token theft from going undetected.

```
Normal flow:
  Client → [refresh_token_1] → Server
  Server → [new_access_token + refresh_token_2] → Client
  refresh_token_1 is now invalid

Attack detection:
  Attacker steals refresh_token_1 (before rotation used it)
  Legitimate client already got refresh_token_2
  Attacker tries to use refresh_token_1:
    Server detects "already rotated" → invalidate ENTIRE token family
    Forces re-login for security
```

```php
// Laravel implementation
class RefreshTokenController
{
    public function refresh(Request $request)
    {
        $token = PersonalAccessToken::findToken($request->refresh_token);

        if (!$token || $token->type !== 'refresh') {
            // Token doesn't exist — possible theft, invalidate family
            $this->revokeTokenFamily($request->refresh_token);
            return response()->json(['error' => 'Invalid token'], 401);
        }

        if ($token->isExpired()) {
            $token->delete();
            return response()->json(['error' => 'Token expired'], 401);
        }

        DB::transaction(function () use ($token) {
            $token->delete();  // Invalidate used refresh token
            return $this->issueNewTokenPair($token->tokenable);
        });
    }
}
```

**Refresh token reuse detection strategy:**
1. Store refresh tokens with a `family_id` (UUID per login)
2. If a used/revoked refresh token is presented → revoke the entire family
3. Force re-authentication

---

**Q4. Where should you store JWTs in the browser and what are the trade-offs?**

**Answer:**

| Storage | XSS Risk | CSRF Risk | Accessible by JS | Recommendation |
|---------|----------|----------|-----------------|---------------|
| **localStorage** | ❌ High (JS can read it) | ✅ Low (not auto-sent) | ✅ Yes | Avoid for sensitive tokens |
| **sessionStorage** | ❌ High (JS can read it) | ✅ Low | ✅ Yes | Better than localStorage, but still XSS risk |
| **HttpOnly Cookie** | ✅ Low (JS cannot read) | ❌ High (auto-sent with requests) | ❌ No | Best — pair with CSRF protection |
| **Memory (JS variable)** | ✅ Low (not persisted) | ✅ Low | ✅ Yes | Secure but lost on refresh |

**Recommended approach:**
```
Access Token: Store in memory (JS variable)
  - Cleared on tab close/refresh
  - Not accessible after XSS payload runs
  - Re-acquired from refresh token automatically

Refresh Token: HttpOnly, Secure, SameSite=Strict cookie
  - Cannot be read by JS (XSS-safe)
  - SameSite=Strict prevents CSRF
  - Auto-sent to your domain only
```

```php
// Set refresh token as secure cookie
return response()->json(['access_token' => $accessToken])
    ->cookie(
        'refresh_token',
        $refreshToken,
        60 * 24 * 30,   // 30 days
        '/',
        null,
        true,            // Secure (HTTPS only)
        true,            // HttpOnly
        false,
        'Strict'         // SameSite
    );
```

---

**Q5. How does OAuth 2.0 token introspection work, and when is it needed?**

**Answer:**

Token introspection (RFC 7662) lets a resource server validate an opaque token by asking the authorization server.

```
Without introspection (JWT):
  Resource Server receives token
  → Verifies signature locally (no network call)
  → Extracts claims from payload

With introspection (opaque token):
  Resource Server receives token
  → POST /oauth/introspect {token: "abc123"}
  → Authorization Server responds:
    {"active": true, "sub": "user_123", "scope": "read write", "exp": 1700000000}
  → Resource Server trusts the response
```

**Trade-offs:**

| Aspect | JWT | Opaque + Introspection |
|--------|-----|----------------------|
| **Network calls** | Zero (local verification) | One per request (unless cached) |
| **Revocation** | Not immediate (wait for expiry) | Immediate |
| **Payload visibility** | Claims in token | Claims from introspection endpoint |
| **Scalability** | Better (no introspection bottleneck) | Introspection endpoint becomes bottleneck |

**Caching introspection responses:**
```php
// Cache introspection result for token's remaining TTL
$cacheKey = 'introspect:' . hash('sha256', $token);
$tokenData = Cache::remember($cacheKey, $remainingTtl, function () use ($token) {
    return $this->authServer->introspect($token);
});
```

---

**Q6. Explain CSRF tokens — how do they work and when are they unnecessary?**

**Answer:**

CSRF (Cross-Site Request Forgery) exploits the fact that browsers automatically send cookies with cross-origin requests.

```
Attack:
  1. User logged into bank.com (session cookie auto-sent)
  2. User visits evil.com
  3. evil.com runs: <form action="https://bank.com/transfer" method="POST">
  4. Browser submits form WITH bank.com cookie → transfer succeeds

CSRF Token Defense:
  1. Server generates random token, stores in session
  2. Token embedded in every HTML form
  3. Form submission includes token in POST body
  4. Server validates body token matches session token
  5. Attacker cannot read the token from another origin (SOP)
```

**When CSRF protection is unnecessary:**
- **SameSite=Strict cookies** — browser won't send cookie in cross-site requests at all
- **JWT in Authorization header** — `Authorization: Bearer <token>` not auto-sent by browsers; only your JS sends it
- **API-only backends** (no session cookies) — no cookies = no CSRF attack vector
- **Stateless tokens in localStorage** — JS-controlled, not auto-sent

```php
// Laravel automatically protects web routes
// For API routes using Sanctum SPA auth, use SameSite cookies instead:
// config/sanctum.php
'stateful' => explode(',', env('SANCTUM_STATEFUL_DOMAINS', 'localhost')),

// In Session config
'same_site' => 'strict',
```
