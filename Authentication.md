# 🔐 Authentication Systems: Evolution, Types, and Implementation

## 📖 Introduction

Authentication is the process of verifying the identity of a user, device, or system. It ensures that only authorized entities can access resources. This document covers the evolution of authentication systems, key concepts, real-life examples, and PHP/Laravel implementations.

---

## 🕰️ Evolution of Authentication Systems

### 1. **Basic Authentication (Early Web - 1990s)**
- **How it works**: Username and password sent in HTTP headers (Base64 encoded)
- **Pros**: Simple to implement
- **Cons**: Credentials sent with every request, no encryption by default
- **Real-life example**: Early HTTP servers, FTP
- **PHP Code**:
```php
// Basic Auth middleware
if (!isset($_SERVER['PHP_AUTH_USER'])) {
    header('WWW-Authenticate: Basic realm="My Realm"');
    header('HTTP/1.0 401 Unauthorized');
    echo 'Authentication required';
    exit;
}

$username = $_SERVER['PHP_AUTH_USER'];
$password = $_SERVER['PHP_AUTH_PW'];

// Verify credentials
if ($username !== 'admin' || $password !== 'password') {
    header('HTTP/1.0 401 Unauthorized');
    echo 'Invalid credentials';
    exit;
}
```

### 2. **Session-Based Authentication (2000s)**
- **How it works**: Server creates session after login, stores session ID in cookie
- **Pros**: Secure, server-controlled
- **Cons**: Doesn't scale well for distributed systems
- **Real-life example**: Traditional web applications like forums, e-commerce sites
- **Laravel Code**:
```php
// Login controller
public function login(Request $request)
{
    $credentials = $request->only('email', 'password');
    
    if (Auth::attempt($credentials)) {
        $request->session()->regenerate();
        return redirect()->intended('dashboard');
    }
    
    return back()->withErrors(['email' => 'Invalid credentials']);
}

// Logout
public function logout(Request $request)
{
    Auth::logout();
    $request->session()->invalidate();
    $request->session()->regenerateToken();
    return redirect('/');
}
```

### 3. **Token-Based Authentication (2010s)**
- **How it works**: Client receives token after authentication, sends it with subsequent requests
- **Pros**: Stateless, scalable, works well with APIs
- **Cons**: Tokens can be stolen, harder to revoke
- **Real-life example**: REST APIs, mobile apps, SPAs
- **Laravel with JWT**:
```php
// Using tymon/jwt-auth package
public function login(Request $request)
{
    $credentials = $request->only('email', 'password');
    
    if (!$token = JWTAuth::attempt($credentials)) {
        return response()->json(['error' => 'Unauthorized'], 401);
    }
    
    return response()->json(compact('token'));
}

public function getUser()
{
    try {
        $user = JWTAuth::parseToken()->authenticate();
        return response()->json(compact('user'));
    } catch (JWTException $e) {
        return response()->json(['error' => 'Token invalid'], 401);
    }
}
```

### 4. **OAuth 2.0 (2012+)**
- **How it works**: Third-party authorization framework allowing apps to access resources without sharing credentials
- **Pros**: Secure delegated access, widely adopted
- **Cons**: Complex implementation
- **Real-life example**: "Login with Google/Facebook", API integrations
- **Laravel with Socialite**:
```php
// routes/web.php
Route::get('/auth/redirect', function () {
    return Socialite::driver('google')->redirect();
});

Route::get('/auth/callback', function () {
    $user = Socialite::driver('google')->user();
    
    // Find or create user
    $authUser = User::updateOrCreate([
        'email' => $user->getEmail(),
    ], [
        'name' => $user->getName(),
        'google_id' => $user->getId(),
    ]);
    
    Auth::login($authUser);
    return redirect('/dashboard');
});
```

### 5. **Single Sign-On (SSO)**
- **How it works**: One login grants access to multiple applications
- **Pros**: Improved user experience, centralized management
- **Cons**: Single point of failure
- **Real-life example**: Enterprise environments, Google Workspace, Microsoft 365
- **Laravel with SAML**:
```php
// Using onelogin/php-saml package
require_once 'vendor/autoload.php';

$settings = new OneLogin_Saml2_Settings($samlSettings);
$auth = new OneLogin_Saml2_Auth($settings);

if (isset($_GET['sso'])) {
    $auth->login();
} elseif (isset($_GET['slo'])) {
    $auth->logout();
} elseif (isset($_POST['SAMLResponse'])) {
    $auth->processResponse();
    $errors = $auth->getErrors();
    
    if (empty($errors)) {
        $attributes = $auth->getAttributes();
        // Authenticate user based on SAML attributes
        $user = User::where('email', $attributes['email'][0])->first();
        Auth::login($user);
        return redirect('/dashboard');
    }
}
```

### 6. **Modern Authentication (2020s)**
- **Multi-Factor Authentication (MFA)**: Combines multiple verification methods
- **Biometric Authentication**: Fingerprint, facial recognition
- **Passwordless Authentication**: Magic links, WebAuthn
- **Zero Trust**: Continuous verification, micro-segmentation

#### MFA in Laravel:
```php
// Using laravel-google2fa package
public function enableMfa(Request $request)
{
    $user = Auth::user();
    $google2fa = app('pragmarx.google2fa');
    
    $user->google2fa_secret = $google2fa->generateSecretKey();
    $user->save();
    
    $qrCodeUrl = $google2fa->getQRCodeUrl(
        'My App',
        $user->email,
        $user->google2fa_secret
    );
    
    return response()->json(['qr_code_url' => $qrCodeUrl]);
}

public function verifyMfa(Request $request)
{
    $google2fa = app('pragmarx.google2fa');
    $secret = $request->user()->google2fa_secret;
    
    $valid = $google2fa->verifyKey($secret, $request->otp);
    
    if ($valid) {
        // Proceed with authentication
        return response()->json(['message' => 'MFA verified']);
    }
    
    return response()->json(['error' => 'Invalid OTP'], 401);
}
```

#### Passwordless Authentication:
```php
// Magic link login
public function sendMagicLink(Request $request)
{
    $request->validate(['email' => 'required|email']);
    
    $user = User::where('email', $request->email)->first();
    
    if ($user) {
        $token = Str::random(64);
        DB::table('password_resets')->insert([
            'email' => $user->email,
            'token' => Hash::make($token),
            'created_at' => now(),
        ]);
        
        // Send email with link
        Mail::to($user->email)->send(new MagicLinkMail($token));
    }
    
    return response()->json(['message' => 'If the email exists, a magic link has been sent']);
}

public function loginWithMagicLink(Request $request, $token)
{
    $reset = DB::table('password_resets')
        ->where('token', Hash::make($token))
        ->first();
    
    if ($reset && now()->diffInMinutes($reset->created_at) < 60) {
        $user = User::where('email', $reset->email)->first();
        Auth::login($user);
        DB::table('password_resets')->where('email', $reset->email)->delete();
        return redirect('/dashboard');
    }
    
    return redirect('/login')->withErrors(['token' => 'Invalid or expired token']);
}
```

---

## 🔑 Key Authentication Concepts

### Authentication vs Authorization
- **Authentication**: Verifying identity ("Who are you?")
- **Authorization**: Granting permissions ("What can you do?")

### Authentication Factors
- **Something you know**: Password, PIN
- **Something you have**: Phone, hardware token
- **Something you are**: Biometrics

### Common Attack Vectors and Mitigations
- **Brute Force**: Rate limiting, account lockout
- **Credential Stuffing**: Password policies, MFA
- **Session Hijacking**: Secure cookies, token rotation
- **Man-in-the-Middle**: HTTPS, certificate pinning

---

## 🏢 Real-Life Examples

### E-commerce Platform
- Uses session-based auth for web users
- JWT for mobile app API
- OAuth for social login
- MFA for admin accounts

### SaaS Application
- SSO integration with enterprise identity providers
- API key authentication for integrations
- Passwordless login for end users

### Banking Application
- Multi-factor authentication required
- Biometric login on mobile apps
- Session timeouts and continuous monitoring

### API-First Company
- OAuth 2.0 for third-party integrations
- JWT with refresh tokens for client apps
- API key authentication for internal services

---

## 🛠️ Laravel Authentication Best Practices

### 1. Use Built-in Features
```php
// Laravel Breeze for simple auth
php artisan breeze:install

// Laravel Jetstream for advanced features
php artisan jetstream:install
```

### 2. Secure Password Storage
```php
// Laravel automatically hashes passwords
$user = User::create([
    'email' => $request->email,
    'password' => Hash::make($request->password),
]);
```

### 3. Implement Rate Limiting
```php
// In routes/web.php
Route::middleware(['throttle:login'])->group(function () {
    Route::post('/login', [AuthController::class, 'login']);
});
```

### 4. Use HTTPS
```php
// Force HTTPS in middleware
if (!$request->secure()) {
    return redirect()->secure($request->getRequestUri());
}
```

### 5. Implement Proper Logout
```php
public function logout(Request $request)
{
    Auth::logout();
    $request->session()->invalidate();
    $request->session()->regenerateToken();
    return redirect('/');
}
```

---

## 📚 References

- OAuth 2.0 RFC: https://tools.ietf.org/html/rfc6749
- JWT RFC: https://tools.ietf.org/html/rfc7519
- Laravel Authentication: https://laravel.com/docs/authentication
- OWASP Authentication Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html

---

## 🎯 Authentication Interview Questions

### Q1. Explain the difference between authentication and authorization with examples.

**Answer:**
Authentication verifies identity ("Are you who you claim to be?"), while authorization determines permissions ("What are you allowed to do?").

Example: Logging into a bank app (authentication) vs. being allowed to transfer money (authorization).

### Q2. How does OAuth 2.0 work? Explain the authorization code flow.

**Answer:**
OAuth 2.0 allows third-party applications to access resources without sharing credentials.

Authorization Code Flow:
1. Client redirects user to authorization server
2. User authenticates and grants permission
3. Authorization server redirects back with authorization code
4. Client exchanges code for access token
5. Client uses access token to access resources

### Q3. What are the security considerations for JWT?

**Answer:**
- Use strong signing algorithms (RS256 over HS256)
- Set appropriate expiration times
- Store tokens securely (HttpOnly cookies for refresh tokens)
- Implement token rotation
- Validate all claims (iss, aud, exp, etc.)

### Q4. How would you implement SSO in a microservices architecture?

**Answer:**
- Use a centralized identity provider (IdP)
- Implement OAuth 2.0 or SAML
- Use JWT for inter-service communication
- Implement token validation middleware
- Use refresh tokens for long-lived sessions

### Q5. What are the advantages and disadvantages of passwordless authentication?

**Answer:**
Advantages:
- No passwords to remember or steal
- Better user experience
- Reduced support costs

Disadvantages:
- Reliance on email/SMS delivery
- Potential for account takeover if email is compromised
- Implementation complexity

### Q6. How do you handle authentication in a distributed system?

**Answer:**
- Use stateless tokens (JWT)
- Implement centralized token validation
- Use API gateways for authentication
- Implement proper token rotation
- Use HTTPS for all communications
- Implement rate limiting and monitoring