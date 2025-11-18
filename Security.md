# 🔒 Security Best Practices for Developers

> Essential security concepts every backend engineer should know.

---

## 📚 Table of Contents

1. [Security Fundamentals](#-security-fundamentals)
2. [Authentication & Authorization](#-authentication--authorization)
3. [Common Vulnerabilities (OWASP Top 10)](#-common-vulnerabilities-owasp-top-10)
4. [Cryptography Basics](#-cryptography-basics)
5. [Secure Coding Practices](#-secure-coding-practices)
6. [API Security](#-api-security)
7. [Database Security](#-database-security)
8. [Infrastructure Security](#-infrastructure-security)
9. [Security Tools & Resources](#-security-tools--resources)

---

## 🎯 Security Fundamentals

### CIA Triad

The foundation of information security:

| Principle | Description | Example |
|-----------|-------------|---------|
| **Confidentiality** | Data accessible only to authorized users | Encryption, access controls |
| **Integrity** | Data cannot be modified without detection | Checksums, digital signatures |
| **Availability** | Data accessible when needed | Redundancy, DDoS protection |

---

### Defense in Depth

**Multiple layers of security controls** throughout a system.

```
User → Firewall → Load Balancer → WAF → Application 
→ Authentication → Authorization → Database Encryption
```

**Philosophy**: If one layer fails, others provide protection.

---

### Principle of Least Privilege

Users/processes should have only the **minimum permissions** needed.

**Examples:**
- Database user with read-only access for reporting
- API key with limited scopes
- Docker containers running as non-root users

---

## 🔐 Authentication & Authorization

### Authentication

**Verifying identity** - "Who are you?"

#### Types of Authentication Factors

| Factor | Example |
|--------|---------|
| **Something you know** | Password, PIN |
| **Something you have** | Phone, hardware token |
| **Something you are** | Fingerprint, face recognition |

#### Multi-Factor Authentication (MFA)

Combining 2+ factors for stronger security.

```
Password (know) + SMS code (have) = 2FA
Password + Fingerprint + Security key = 3FA
```

---

### Password Security Best Practices

#### ❌ Never Do This:
```php
// WRONG - Plain text storage
$query = "INSERT INTO users (password) VALUES ('$password')";
```

#### ✅ Do This Instead:
```php
// RIGHT - Hash with bcrypt
$hashedPassword = password_hash($password, PASSWORD_BCRYPT);

// Verification
if (password_verify($inputPassword, $hashedPassword)) {
    // Login successful
}
```

**Key Points:**
- Use bcrypt, Argon2, or PBKDF2
- Add salt (automatic in modern functions)
- Never store plain text passwords
- Use sufficient work factor (cost)

---

### Session Management

#### Secure Session Cookies

```php
session_set_cookie_params([
    'lifetime' => 0,
    'path' => '/',
    'domain' => 'example.com',
    'secure' => true,      // HTTPS only
    'httponly' => true,    // No JavaScript access
    'samesite' => 'Strict' // CSRF protection
]);
```

**Best Practices:**
- Regenerate session ID after login
- Set appropriate expiration times
- Clear sessions on logout
- Use secure, random session IDs

---

### Authorization

**Determining permissions** - "What can you do?"

#### Access Control Models

##### 1. **Role-Based Access Control (RBAC)**

```php
class User {
    public function hasRole($role) {
        return in_array($role, $this->roles);
    }
}

// Usage
if ($user->hasRole('admin')) {
    // Allow admin actions
}
```

##### 2. **Attribute-Based Access Control (ABAC)**

More granular - based on user attributes, resource attributes, and environment.

```php
if ($user->department === 'finance' && 
    $document->type === 'invoice' && 
    $time->isBusinessHours()) {
    // Allow access
}
```

##### 3. **Permission-Based Access Control**

```php
if ($user->hasPermission('posts.delete')) {
    // Allow deletion
}
```

---

### JSON Web Tokens (JWT)

#### Structure
```
Header.Payload.Signature
```

#### Best Practices
- Use HTTPS to prevent token interception
- Set short expiration times
- Store securely (HttpOnly cookies, not localStorage)
- Implement token refresh mechanism
- Sign with strong secret (HS256) or use RSA (RS256)
- Validate all claims (exp, iss, aud)

#### Common JWT Mistakes

❌ **Don't:**
- Store sensitive data in payload (it's base64, not encrypted)
- Use weak signing secrets
- Store in localStorage (vulnerable to XSS)
- Accept unsigned tokens

✅ **Do:**
- Validate signature
- Check expiration
- Use refresh tokens
- Implement token blacklisting for logout

---

## 🚨 Common Vulnerabilities (OWASP Top 10)

### 1. **Broken Access Control**

**Problem**: Users can access resources they shouldn't.

❌ **Vulnerable Code:**
```php
// Insecure Direct Object Reference (IDOR)
$userId = $_GET['id'];
$user = DB::find($userId); // No authorization check!
```

✅ **Secure Code:**
```php
$userId = $_GET['id'];
if ($userId !== Auth::user()->id && !Auth::user()->isAdmin()) {
    abort(403, 'Unauthorized');
}
$user = DB::find($userId);
```

---

### 2. **Cryptographic Failures**

**Problem**: Weak encryption, exposed sensitive data.

❌ **Don't:**
```php
// Weak encryption
$encrypted = base64_encode($data);
```

✅ **Do:**
```php
// Strong encryption (AES-256)
$key = random_bytes(32);
$nonce = random_bytes(SODIUM_CRYPTO_SECRETBOX_NONCEBYTES);
$encrypted = sodium_crypto_secretbox($data, $nonce, $key);
```

**Critical Data to Encrypt:**
- Passwords (hash, don't encrypt)
- Credit card numbers
- Social security numbers
- Personal health information
- API keys and secrets

---

### 3. **Injection Attacks**

#### SQL Injection

❌ **Vulnerable:**
```php
$query = "SELECT * FROM users WHERE email = '" . $_POST['email'] . "'";
// Attacker input: ' OR '1'='1
```

✅ **Safe:**
```php
// Prepared statements
$stmt = $pdo->prepare("SELECT * FROM users WHERE email = ?");
$stmt->execute([$_POST['email']]);
```

#### Command Injection

❌ **Vulnerable:**
```php
system("ping -c 4 " . $_GET['host']);
// Attacker: 8.8.8.8; rm -rf /
```

✅ **Safe:**
```php
$host = escapeshellarg($_GET['host']);
system("ping -c 4 " . $host);
// Better: Use native PHP functions instead
```

#### LDAP Injection, XML Injection, etc.
**Principle**: Never trust user input. Always validate and sanitize.

---

### 4. **Insecure Design**

**Problem**: Lack of security controls in design phase.

**Solutions:**
- Threat modeling
- Security requirements from day one
- Secure development lifecycle
- Regular security reviews

---

### 5. **Security Misconfiguration**

**Common Mistakes:**
- Default credentials
- Unnecessary features enabled
- Detailed error messages in production
- Missing security headers
- Unpatched systems

✅ **Fixes:**
```php
// .env file (never commit to Git)
APP_DEBUG=false
APP_ENV=production

// Disable directory listing in .htaccess
Options -Indexes

// Security headers
header("X-Frame-Options: DENY");
header("X-Content-Type-Options: nosniff");
header("X-XSS-Protection: 1; mode=block");
header("Strict-Transport-Security: max-age=31536000");
```

---

### 6. **Vulnerable and Outdated Components**

**Problem**: Using libraries with known vulnerabilities.

**Solutions:**
- Regularly update dependencies
- Use dependency checkers:
  - `npm audit`
  - `composer audit`
  - Snyk, Dependabot

```bash
# Check for vulnerabilities
composer audit
npm audit

# Update safely
composer update
npm update
```

---

### 7. **Identification and Authentication Failures**

**Common Issues:**
- Weak password requirements
- No brute force protection
- Session fixation
- Insecure password recovery

**Solutions:**

```php
// Password requirements
- Minimum 12 characters
- Mix of uppercase, lowercase, numbers, symbols
- Check against common passwords
- Implement account lockout after failed attempts

// Rate limiting
use Illuminate\Support\Facades\RateLimiter;

RateLimiter::attempt(
    'login:' . $request->ip(),
    $maxAttempts = 5,
    function() { /* Login logic */ },
    $decaySeconds = 60
);
```

---

### 8. **Software and Data Integrity Failures**

**Problem**: Unsigned updates, untrusted sources, insecure CI/CD.

**Solutions:**
- Verify signatures of downloads
- Use Subresource Integrity (SRI) for CDN resources
- Secure CI/CD pipelines
- Code signing

```html
<!-- Subresource Integrity -->
<script 
  src="https://cdn.example.com/lib.js" 
  integrity="sha384-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/uxy9rx7HNQlGYl1kPzQho1wx4JwY8wC"
  crossorigin="anonymous">
</script>
```

---

### 9. **Security Logging and Monitoring Failures**

**Problem**: Can't detect or respond to breaches.

**What to Log:**
- Authentication attempts (success/failure)
- Authorization failures
- Input validation failures
- Critical operations (delete, permission changes)

❌ **Don't Log:**
- Passwords
- Session tokens
- Credit card numbers
- Personal identifiable information (PII)

```php
// Good logging
Log::warning('Failed login attempt', [
    'email' => $email,
    'ip' => $request->ip(),
    'timestamp' => now()
]);

// Bad logging
Log::info('Login attempt', [
    'password' => $password // NEVER LOG PASSWORDS
]);
```

**Tools:**
- ELK Stack (Elasticsearch, Logstash, Kibana)
- Splunk
- CloudWatch (AWS)
- Sentry for error tracking

---

### 10. **Server-Side Request Forgery (SSRF)**

**Problem**: Application fetches remote resources without validation.

❌ **Vulnerable:**
```php
$url = $_GET['url'];
$content = file_get_contents($url);
// Attacker: ?url=file:///etc/passwd
```

✅ **Safe:**
```php
$url = $_GET['url'];

// Whitelist allowed domains
$allowedDomains = ['api.example.com', 'cdn.example.com'];
$host = parse_url($url, PHP_URL_HOST);

if (!in_array($host, $allowedDomains)) {
    throw new Exception('Invalid URL');
}

// Blacklist private IPs
$ip = gethostbyname($host);
if (filter_var($ip, FILTER_VALIDATE_IP, FILTER_FLAG_NO_PRIV_RANGE)) {
    $content = file_get_contents($url);
}
```

---

## 🔑 Cryptography Basics

### Hashing vs Encryption

| Feature | Hashing | Encryption |
|---------|---------|------------|
| **Reversible** | No | Yes (with key) |
| **Purpose** | Verify integrity | Protect confidentiality |
| **Output** | Fixed-size digest | Variable size |
| **Use Case** | Passwords, checksums | Sensitive data storage |

---

### Common Algorithms

#### Hashing
- **MD5** - ❌ Broken, don't use
- **SHA-1** - ❌ Broken, don't use
- **SHA-256** - ✅ Good for integrity
- **bcrypt** - ✅ Best for passwords
- **Argon2** - ✅ Best for passwords (newer)

#### Symmetric Encryption
- **AES-256** - ✅ Industry standard
- **ChaCha20** - ✅ Modern alternative

#### Asymmetric Encryption
- **RSA** - ✅ Widely used (2048+ bits)
- **ECC** - ✅ Faster, smaller keys

---

### Salt and Pepper

**Salt**: Random data added to password before hashing (prevents rainbow table attacks)
```php
// Built-in with password_hash()
$hash = password_hash($password, PASSWORD_BCRYPT);
// Salt is automatically generated and stored in hash
```

**Pepper**: Secret value added to password (stored separately from database)
```php
$peppered = hash_hmac('sha256', $password, env('PEPPER_SECRET'));
$hash = password_hash($peppered, PASSWORD_BCRYPT);
```

---

## 💻 Secure Coding Practices

### Input Validation

**Whitelist over Blacklist**

❌ **Blacklist (BAD):**
```php
// Trying to block malicious input
if (strpos($input, '<script>') !== false) {
    // Reject
}
// Easily bypassed: <Script>, <img onerror=...>
```

✅ **Whitelist (GOOD):**
```php
// Only allow expected format
if (!preg_match('/^[a-zA-Z0-9]+$/', $input)) {
    // Reject
}
```

---

### Output Encoding

**Prevent XSS** by encoding output based on context.

```php
// HTML context
echo htmlspecialchars($userInput, ENT_QUOTES, 'UTF-8');

// JavaScript context
echo json_encode($userInput);

// URL context
echo urlencode($userInput);

// In Laravel Blade
{{ $userInput }} // Automatically escaped
{!! $userInput !!} // NOT escaped - dangerous
```

---

### Cross-Site Scripting (XSS)

**Types:**
1. **Stored XSS**: Malicious script stored in database
2. **Reflected XSS**: Script in URL parameters
3. **DOM-based XSS**: Client-side JavaScript vulnerability

**Prevention:**
- Escape all user input
- Use Content Security Policy (CSP)
- HttpOnly cookies
- Input validation

```php
// Content Security Policy header
header("Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline'");
```

---

### Cross-Site Request Forgery (CSRF)

**Attack**: Trick user into submitting unwanted requests.

**Prevention:**

```html
<!-- CSRF token in forms -->
<form method="POST">
    @csrf
    <button type="submit">Delete Account</button>
</form>
```

```php
// Laravel automatically verifies CSRF token
// In routes/web.php - CSRF protection enabled by default
```

---

### XML External Entity (XXE)

❌ **Vulnerable:**
```php
$xml = simplexml_load_string($_POST['xml']);
```

✅ **Safe:**
```php
libxml_disable_entity_loader(true);
$xml = simplexml_load_string($_POST['xml'], 'SimpleXMLElement', LIBXML_NOENT);
```

---

## 🌐 API Security

### 1. **Authentication**

**Options:**
- API Keys (simple but less secure)
- OAuth 2.0 (delegated access)
- JWT (stateless)
- mTLS (mutual TLS)

---

### 2. **Rate Limiting**

Prevent abuse and DDoS attacks.

```php
// Laravel example
Route::middleware('throttle:60,1')->group(function () {
    Route::get('/api/users', [UserController::class, 'index']);
});
```

**Headers:**
```
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 55
X-RateLimit-Reset: 1640000000
```

---

### 3. **Input Validation**

```php
// Use validation libraries
$validated = $request->validate([
    'email' => 'required|email',
    'age' => 'required|integer|min:18|max:120',
]);
```

---

### 4. **HTTPS Only**

```php
// Force HTTPS
if (!$request->secure()) {
    return redirect()->secure($request->getRequestUri());
}
```

---

### 5. **CORS Configuration**

```php
// Configure allowed origins
header("Access-Control-Allow-Origin: https://trusted-site.com");
header("Access-Control-Allow-Methods: GET, POST");
header("Access-Control-Allow-Headers: Content-Type, Authorization");
```

---

### 6. **API Versioning**

```
/api/v1/users
/api/v2/users
```

Allows security updates without breaking clients.

---

## 🗄️ Database Security

### 1. **Use Prepared Statements**

Always use parameterized queries to prevent SQL injection.

---

### 2. **Principle of Least Privilege**

```sql
-- Don't give app full access
-- Create limited user
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'password';
GRANT SELECT, INSERT, UPDATE ON mydb.* TO 'app_user'@'localhost';
```

---

### 3. **Encryption**

**Encryption at Rest:**
- Encrypt database files
- Use Transparent Data Encryption (TDE)

**Encryption in Transit:**
- Use SSL/TLS for database connections

```php
// MySQL SSL connection
$pdo = new PDO(
    "mysql:host=localhost;dbname=mydb",
    "user",
    "password",
    [PDO::MYSQL_ATTR_SSL_CA => '/path/to/ca.pem']
);
```

---

### 4. **Backup Security**

- Encrypt backups
- Store in secure location
- Test restore procedures
- Access control on backups

---

## 🏗️ Infrastructure Security

### 1. **Firewall Configuration**

Only expose necessary ports:
```bash
# Allow HTTP/HTTPS
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Allow SSH from specific IP
sudo ufw allow from 203.0.113.0/24 to any port 22

# Enable firewall
sudo ufw enable
```

---

### 2. **Keep Systems Updated**

```bash
# Regular updates
sudo apt update && sudo apt upgrade -y

# Automate security updates
sudo apt install unattended-upgrades
```

---

### 3. **SSH Hardening**

```bash
# /etc/ssh/sshd_config
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
Port 2222  # Change default port
```

---

### 4. **Container Security**

```dockerfile
# Use specific version tags
FROM php:8.2-fpm

# Don't run as root
RUN useradd -m appuser
USER appuser

# Scan for vulnerabilities
# docker scan myimage
```

---

### 5. **Environment Variables**

Never hardcode secrets:

```bash
# .env file (add to .gitignore)
DB_PASSWORD=secret
API_KEY=xyz123

# Use in code
$password = env('DB_PASSWORD');
```

---

## 🛠️ Security Tools & Resources

### Scanning Tools

| Tool | Purpose |
|------|---------|
| **OWASP ZAP** | Web app vulnerability scanner |
| **Burp Suite** | Penetration testing |
| **Nmap** | Network scanner |
| **Nikto** | Web server scanner |
| **SQLMap** | SQL injection testing |
| **Wireshark** | Network protocol analyzer |

---

### Code Analysis

| Tool | Language |
|------|----------|
| **SonarQube** | Multiple languages |
| **Bandit** | Python |
| **Brakeman** | Ruby/Rails |
| **PHPStan** | PHP |
| **ESLint** | JavaScript |

---

### Dependency Checking

```bash
# PHP
composer audit

# Node.js
npm audit
npm audit fix

# Python
pip-audit

# Use Snyk for comprehensive checks
snyk test
```

---

### Resources

#### Learning Platforms
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [HackTheBox](https://www.hackthebox.com/)
- [TryHackMe](https://tryhackme.com/)

#### Books
- **"The Web Application Hacker's Handbook"** by Dafydd Stuttard
- **"Hacking: The Art of Exploitation"** by Jon Erickson
- **"Security Engineering"** by Ross Anderson

#### Standards & Guidelines
- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [CWE Top 25](https://cwe.mitre.org/top25/)

---

## ✅ Security Checklist

### Development
- [ ] Use HTTPS everywhere
- [ ] Validate and sanitize all input
- [ ] Use prepared statements for database queries
- [ ] Hash passwords with bcrypt/Argon2
- [ ] Implement CSRF protection
- [ ] Set secure session cookie flags
- [ ] Use Content Security Policy headers
- [ ] Implement rate limiting
- [ ] Keep dependencies updated
- [ ] Use environment variables for secrets

### Deployment
- [ ] Disable debug mode in production
- [ ] Configure security headers
- [ ] Set up firewall rules
- [ ] Enable automatic security updates
- [ ] Use strong TLS configuration
- [ ] Implement monitoring and logging
- [ ] Set up intrusion detection
- [ ] Regular security audits
- [ ] Backup and disaster recovery plan
- [ ] Incident response plan

---

**Remember**: Security is not a one-time task but an ongoing process. Stay updated with latest vulnerabilities and best practices!
