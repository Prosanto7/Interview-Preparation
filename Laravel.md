<a href="https://dev.to/thedevricha/understanding-the-laravel-request-lifecycle-laravel-11-63p">Blog</a>

## **What is the Laravel Request Lifecycle?**

The **Laravel request lifecycle** is the sequence of steps Laravel follows **from the moment a user makes an HTTP request** (like visiting a URL) to **sending back a response** (like rendering a view or JSON).

---

## **Step-by-Step Breakdown**

### **1. `public/index.php` – Entry Point**
- Every request to your Laravel app starts here.
- It loads the Composer autoloader and boots the Laravel framework.

```php
require __DIR__.'/../vendor/autoload.php';
$app = require_once __DIR__.'/../bootstrap/app.php';
```

**Analogy:** Think of this as the **main door** of a building. All visitors (requests) enter through this gate.

---

### **2. Kernel Handles the Request**
- Laravel uses **`App\Http\Kernel`** for web routes and **`App\Console\Kernel`** for CLI.
- It defines global and route-specific **middleware**.

```php
$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);
$response = $kernel->handle(
    $request = Illuminate\Http\Request::capture()
);
```

**Analogy:** This is like **reception**, where visitors are filtered (authenticated, rate-limited, etc.) before they can proceed.

---

### **3. Middleware Processing**
- Middleware checks/filters requests.
- Examples:
  - `VerifyCsrfToken`
  - `Authenticate`
  - `TrimStrings`

**Global Middleware:** Runs for every request.  
**Route Middleware:** Applied to specific routes using `->middleware('auth')`.

**Analogy:** Security checks like **ID verification, metal detector** before allowing you in.

---

### **4. Route Resolution**
- Laravel checks the route in **`routes/web.php`** or `api.php`.
- It matches the incoming URL and HTTP verb (GET, POST, etc.) to a route.
- Routes can return views, data, or trigger controllers.

```php
Route::get('/dashboard', [DashboardController::class, 'index']);
```

**Analogy:** After security, the receptionist tells the visitor **where to go** — accounts, HR, etc.

---

### **5. Controller or Closure**
- If the route maps to a controller, Laravel resolves dependencies (using the Service Container) and calls the method.
- Dependency Injection is used here for services/models.

```php
public function index(ReportService $service)
{
    return view('dashboard', ['data' => $service->get()]);
}
```

**Analogy:** You’re guided to the **respective office or department** based on your need.

---

### **6. Business Logic and Models**
- The controller may interact with Models or Services.
- Eloquent ORM handles database queries.

```php
$users = User::where('active', true)->get();
```

**Analogy:** Your request is **processed by the internal staff** (business logic), accessing **files or databases**.

---

### **7. Response is Prepared**
- Once the controller returns something, Laravel wraps it in a **response object**.

```php
return response()->json($users);
```

- Laravel supports JSON, views, file downloads, and redirects.

---

### **8. Sending the Response**
- The response is returned back through the middleware stack (outbound).
- Final response is sent back to the browser/client.

```php
$response->send();
```

**Analogy:** After processing, you receive **documents, instructions, or feedback** and exit the building.

---

### **9. Termination (Optional Cleanup)**
- The `terminate()` method of middleware is called (if defined).
- Useful for logging, analytics, or session cleanup.

---

## **Diagram of Laravel Request Lifecycle**

```
Browser
  ↓
public/index.php (entry point)
  ↓
HTTP Kernel (App\Http\Kernel)
  ↓
Global Middleware
  ↓
Routing (routes/web.php)
  ↓
Route Middleware
  ↓
Controller → Business Logic → Models/DB
  ↓
Response (HTML, JSON, View)
  ↓
Response Sent to Browser
  ↓
terminate() method for cleanup
```

---

## **Summary Table**

| Stage                  | Purpose                            |
|------------------------|------------------------------------|
| `index.php`            | Starts the app                     |
| HTTP Kernel            | Handles request lifecycle          |
| Middleware             | Filters/modifies requests/responses |
| Routing                | Matches request to route/controller |
| Controller             | Contains logic for handling request |
| Model/Services         | Fetch/process data                 |
| Response               | Prepares and sends output          |
| Termination            | Executes cleanup logic             |

---

## **When is This Useful?**
- **Debugging**: Know where something failed.
- **Optimization**: Use middleware for caching or throttling.
- **Testing**: Test specific stages like route logic, controller behavior, etc.
- **Customization**: Inject your own middleware or service bindings.

---


## 🔐 1. CSRF Tokens (Cross-Site Request Forgery)

### ✅ Purpose:
To protect your web application from **malicious requests**.

### 🔧 How It Works:
Laravel automatically generates a CSRF token for each active user session. This token is required for any **POST, PUT, PATCH, DELETE** requests made via forms.

### 🧪 Example:
In Blade forms:

```blade
<form method="POST" action="/submit">
    @csrf
    <input type="text" name="name">
    <button type="submit">Submit</button>
</form>
```

Laravel automatically checks if the submitted token matches the one in the session.

---

## 🔑 2. API Tokens (Laravel Sanctum or Passport)

Laravel provides two common tools for API token-based authentication:

---

### 🔸 Laravel Sanctum (Recommended for SPAs and simple APIs)

**✅ Features:**
- Lightweight
- Supports SPA authentication
- Token abilities/scopes
- Easy to use

**🔧 How It Works:**
Sanctum allows each user to generate multiple API tokens.

### 🧪 Example:

```bash
composer require laravel/sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
php artisan migrate
```

In your model (`User.php`):

```php
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable {
    use HasApiTokens, Notifiable;
}
```

To generate a token:

```php
$token = $user->createToken('API Token')->plainTextToken;
```

To use in headers:

```
Authorization: Bearer your_token_here
```

---

### 🔸 Laravel Passport (OAuth2 Authentication)

**✅ Features:**
- Full OAuth2 server
- Ideal for large or third-party API authentication
- Includes token expiration and refresh tokens

**🔧 How It Works:**
Uses OAuth2 protocol. More secure and powerful but heavier to configure.

---

## 🎯 3. JWT (JSON Web Tokens – via third-party)

Laravel also supports **JWT** through packages like `tymon/jwt-auth` for stateless API authentication (token does not need to be stored in DB).

---

## 📌 Where Tokens Are Stored

- **CSRF Tokens**: Stored in session (on server-side)
- **Sanctum Tokens**: Stored in `personal_access_tokens` table
- **JWT Tokens**: Stored on the client side (usually localStorage or cookie)

---

## 🛡️ Token Expiration & Revocation

- **Sanctum**: Tokens can be revoked by calling `$user->tokens()->delete()`.
- **Passport**: Uses database and has expiry settings in config.
- **JWT**: Includes expiry time in the token itself.

---

## 🔄 Real-Life Examples

| Use Case                     | Token Type    | Example                                                                 |
|-----------------------------|---------------|-------------------------------------------------------------------------|
| Form submission             | CSRF Token    | Protects against unwanted form submissions from other sites             |
| Mobile or SPA authentication| Sanctum Token | User logs in once, token is stored and reused for all future API calls |
| 3rd-party integrations      | Passport Token| An app connects to your service using OAuth2                           |
| Stateless REST API          | JWT           | Token is signed and verified with secret key without DB lookup         |

---

## ✅ When to Use What

| Use Case                              | Use       |
|---------------------------------------|-----------|
| Basic web form protection             | CSRF      |
| SPA or mobile app                     | Sanctum   |
| OAuth2 or third-party access          | Passport  |
| Stateless API with custom logic      | JWT       |

---


### 🟢 **Beginner-Level Laravel Interview Questions with Answers**

---

**1. What is Laravel?**  
**Answer:**  
Laravel is a PHP web application framework based on the MVC (Model-View-Controller) architecture. It provides a clean and elegant syntax and includes built-in tools for routing, authentication, sessions, caching, and more.

---

**2. What are the main features of Laravel?**  
**Answer:**  
- Eloquent ORM  
- Blade templating engine  
- Artisan CLI  
- Routing  
- Middleware  
- Authentication & Authorization  
- Task Scheduling  
- Migrations and Seeders

---

**3. What is MVC architecture?**  
**Answer:**  
MVC stands for Model-View-Controller:
- **Model**: Manages data and business logic.
- **View**: Handles the UI.
- **Controller**: Handles user input and updates the model/view.

---

**4. What is Artisan in Laravel?**  
**Answer:**  
Artisan is Laravel’s command-line interface (CLI) that helps automate repetitive tasks like creating models, controllers, migrations, etc.  
Example:  
```bash
php artisan make:controller UserController
```

---

**5. What is Eloquent ORM?**  
**Answer:**  
Eloquent is Laravel’s built-in ORM that allows interaction with the database using an expressive syntax. It represents each table as a model.

---

**6. What is a migration in Laravel?**  
**Answer:**  
Migrations are version control for your database. They allow you to define schema changes using PHP instead of SQL.

```bash
php artisan make:migration create_users_table
```

---

### 🟡 **Intermediate-Level Laravel Interview Questions with Answers**

---

**7. What is middleware in Laravel?**  
**Answer:**  
Middleware filters HTTP requests entering the application. Example: `auth` middleware checks if the user is authenticated.

```php
Route::middleware(['auth'])->group(function () {
    Route::get('/dashboard', 'DashboardController@index');
});
```

---

**8. What are relationships in Eloquent?**  
**Answer:**
- `hasOne`
- `hasMany`
- `belongsTo`
- `belongsToMany`
- `hasManyThrough`

Example:
```php
public function posts() {
    return $this->hasMany(Post::class);
}
```

---

**9. What is a service provider in Laravel?**  
**Answer:**  
Service providers are the central place to configure and bootstrap application services. Most service providers are stored in the `app/Providers` directory.

---

**10. What is the use of route model binding?**  
**Answer:**  
It allows you to inject model instances directly into your routes.

```php
Route::get('/users/{user}', function (User $user) {
    return $user;
});
```

---

**11. What is a FormRequest class in Laravel?**  
**Answer:**  
A custom request class that handles validation and authorization logic separately.

```bash
php artisan make:request StoreUserRequest
```

---

**12. What are accessors and mutators in Eloquent?**  
**Answer:**  
Accessors format Eloquent attributes when retrieving, and mutators format data before saving.

```php
// Accessor
public function getFullNameAttribute() {
    return $this->first_name . ' ' . $this->last_name;
}
```

---

### 🔴 **Advanced-Level Laravel Interview Questions with Answers**

---

**13. What is the difference between `@include`, `@yield`, and `@section`?**  
**Answer:**
- `@include` includes a blade file.
- `@section` defines a section.
- `@yield` renders the content of a section.

---

**14. What is the Laravel service container?**  
**Answer:**  
The service container is a powerful tool for managing class dependencies and performing dependency injection.

```php
app()->make(MyClass::class);
```

---

**15. How does Laravel handle API authentication?**  
**Answer:**  
Laravel uses:
- **Sanctum** for simple token-based APIs.
- **Passport** for full OAuth2-based APIs.

---

**16. How do queues work in Laravel?**  
**Answer:**  
Queues allow you to defer time-consuming tasks (e.g., sending emails) to be processed in the background.

```php
php artisan queue:work
```

---

**17. What are events and listeners in Laravel?**  
**Answer:**  
They allow you to decouple various parts of your application.

```php
Event::listen(UserRegistered::class, SendWelcomeEmail::class);
```

---

**18. What is the Repository pattern in Laravel?**  
**Answer:**  
A design pattern that separates the logic that retrieves data from the database, helping in better code organization and testing.

---

### ✅ **Bonus: Scenario-Based Laravel Questions**

---

**19. How would you handle multi-language (i18n) support in Laravel?**  
**Answer:**  
Using Laravel localization:
```php
__('messages.welcome')
```
And language files in `resources/lang/{lang}/`.

---

**20. How would you structure a large Laravel application?**  
**Answer:**
- Use service classes, repository pattern.
- Create modules/domains.
- Use form requests and policies.
- Use job queues, event-listener separation.

---

## 📚 References

- Laravel Docs (latest): https://laravel.com/docs
- Middleware: https://laravel.com/docs/middleware
- Eloquent ORM: https://laravel.com/docs/eloquent
- Validation & Form Requests: https://laravel.com/docs/validation
- Queues: https://laravel.com/docs/queues
- Events & Listeners: https://laravel.com/docs/events
- Sanctum: https://laravel.com/docs/sanctum
- Passport: https://laravel.com/docs/passport

---

## 🎯 Laravel Interview Questions — Critical Scenarios

---

### 🔴 High Traffic & Scaling

**Q1. Your Laravel app is struggling under high traffic. Walk through your optimization strategy.**

**Answer:**

**Layer 1 — PHP/Laravel level:**
- Enable OPcache in `php.ini`
- Use `php artisan config:cache`, `route:cache`, `view:cache` to precompile Laravel bootstrap files
- Use `php artisan optimize` (bundles config, routes, views)
- Increase PHP-FPM worker pool size

**Layer 2 — Caching:**
```php
// Cache expensive queries
$users = Cache::remember('active-users', 3600, function () {
    return User::where('active', true)->get();
});

// Cache entire routes with ResponseCache package or middleware
```

**Layer 3 — Database:**
- Add missing indexes (EXPLAIN slow queries)
- Use **eager loading** to eliminate N+1 queries
- Use **read replicas** — configure in `config/database.php` with `read`/`write` array
- Use database connection pooling (PgBouncer for PostgreSQL)

**Layer 4 — Infrastructure:**
- Horizontal scaling behind a load balancer
- Centralized session storage (Redis) — file sessions break on multiple servers
- Centralized cache (Redis) — array cache breaks on multiple servers
- CDN for static assets

**Layer 5 — Async processing:**
- Move heavy work (emails, notifications, reports) to queues with Laravel Horizon

---

**Q2. What is the N+1 problem in Eloquent and how do you detect and fix it?**

**Answer:**
N+1 occurs when you loop over a collection and trigger a new query per item.

```php
// ❌ N+1 — 1 query for posts + N queries for each post's author
$posts = Post::all();
foreach ($posts as $post) {
    echo $post->author->name;  // Triggers SELECT for each post
}

// ✅ Eager loading — 2 queries total
$posts = Post::with('author')->get();

// ✅ Nested eager loading
$posts = Post::with(['author', 'comments.user'])->get();

// ✅ Lazy eager loading (when you already have collection)
$posts->load('author');
```

**Detection:**
```php
// In AppServiceProvider::boot() for dev only
DB::listen(function ($query) {
    Log::debug($query->sql, $query->bindings);
});

// Or use Laravel Debugbar / Telescope
```

**Strict mode (Laravel 10+):**
```php
// Throws exception if lazy loading occurs — great for catching N+1 in dev
Model::preventLazyLoading(app()->isLocal());
```

---

**Q3. How does Laravel handle horizontal scaling, and what must you configure?**

**Answer:**
Laravel is stateless by design but uses server-local defaults that break on multiple servers:

| Component | Default (Breaks) | Scalable Alternative |
|-----------|-----------------|---------------------|
| **Sessions** | File-based | `CACHE_DRIVER=redis` or database |
| **Cache** | Array/File | `CACHE_DRIVER=redis` |
| **Queue** | Sync/Database | Redis + Laravel Horizon |
| **Locks** | Cache-based | Redis atomic locks |
| **File Storage** | Local disk | S3 or compatible (via Flysystem) |

```php
// config/session.php
'driver' => env('SESSION_DRIVER', 'redis'),

// .env
SESSION_DRIVER=redis
CACHE_DRIVER=redis
QUEUE_CONNECTION=redis
FILESYSTEM_DISK=s3
```

---

### 🔐 Security

**Q4. What are the most common security vulnerabilities in Laravel apps and how do you prevent them?**

**Answer:**

| Vulnerability | Laravel Protection | Custom Action Needed |
|--------------|-------------------|---------------------|
| **SQL Injection** | Eloquent parameterized queries | Avoid `DB::statement()` with raw user input |
| **XSS** | Blade `{{ }}` auto-escapes | Never use `{!! !!}` with untrusted input |
| **CSRF** | `@csrf` token + `VerifyCsrfToken` middleware | Exclude only trusted API routes |
| **Mass Assignment** | `$fillable` / `$guarded` on models | Never set `$guarded = []` without thought |
| **Auth Bypass** | Policies + Gates | Always authorize, don't rely on route hiding |
| **Sensitive Data Exposure** | `.env` never in version control | Use secrets manager in production |

```php
// ❌ Dangerous: unguarded mass assignment
User::create($request->all());

// ✅ Safe: explicit fillable list
class User extends Model {
    protected $fillable = ['name', 'email', 'password'];
    protected $hidden = ['password', 'remember_token'];
}

// ✅ Safe: authorization with policy
$this->authorize('update', $post); // Throws 403 if unauthorized
```

---

**Q5. How do you implement rate limiting in Laravel for an API?**

**Answer:**
Laravel provides `ThrottleRequests` middleware and named rate limiters.

```php
// routes/api.php
Route::middleware(['auth:sanctum', 'throttle:60,1'])->group(function () {
    Route::get('/user', [UserController::class, 'show']);
});

// Custom named limiter (Laravel 8+) — in RouteServiceProvider
RateLimiter::for('api', function (Request $request) {
    return $request->user()
        ? Limit::perMinute(60)->by($request->user()->id)
        : Limit::perMinute(10)->by($request->ip());
});

// Apply named limiter
Route::middleware(['throttle:api'])->group(...);
```

**Dynamic limits by user plan:**
```php
RateLimiter::for('api', function (Request $request) {
    return Limit::perMinute($request->user()?->api_rate_limit ?? 30)
        ->by($request->user()?->id ?: $request->ip());
});
```

---

### ⚡ Caching

**Q6. Explain caching strategies in Laravel with Redis. What is cache tagging?**

**Answer:**

```php
// Basic cache
Cache::put('key', $value, now()->addHour());
Cache::get('key', $default);
Cache::remember('key', 3600, fn() => expensiveQuery());
Cache::rememberForever('key', fn() => staticData());

// Cache tags — group related items for bulk invalidation
Cache::tags(['users', 'posts'])->put("post:{$id}", $post, 600);

// Invalidate all posts tagged cache
Cache::tags(['posts'])->flush();

// Invalidate single user's cache
Cache::tags(["user:{$userId}"])->flush();
```

**When to use tags:** When you need to invalidate related items together (e.g., all posts by a user when the user is deleted).

**Note:** Cache tags require a driver that supports them — **Redis** or **Memcached** (not file or database cache).

---

**Q7. What is the difference between `Cache::remember()` and `Cache::rememberForever()`? What are the risks?**

**Answer:**
- `remember($key, $ttl, $callback)` — caches for a specified duration. Safe default.
- `rememberForever($key, $callback)` — no expiry. **Risk:** stale data if the underlying data changes.

**Best practices:**
- Use `rememberForever` only for genuinely static data (country list, currency codes).
- Always provide a way to bust the cache on data changes:
  ```php
  // In User model observer
  static::updated(function ($user) {
      Cache::forget("user:{$user->id}");
  });
  ```
- Consider using **event-driven cache invalidation** over TTL for consistency.

---

### 🧵 Queues & Jobs

**Q8. How do you handle job failures in Laravel queues?**

**Answer:**

```php
class ProcessPayment implements ShouldQueue {
    public $tries = 3;              // Max attempts
    public $backoff = [10, 30, 60]; // Exponential backoff in seconds
    public $timeout = 120;          // Seconds before considered timed out

    public function handle(): void {
        // process payment...
    }

    public function failed(Throwable $exception): void {
        // Notify admin, log, refund if partially processed
        Log::error('Payment failed', ['order' => $this->order->id]);
        Notification::send($this->user, new PaymentFailedNotification());
    }
}
```

**Monitoring failed jobs:**
```bash
php artisan queue:failed          # List failed jobs
php artisan queue:retry all       # Retry all failed jobs
php artisan queue:forget {id}     # Delete specific failed job
```

**Laravel Horizon** (Redis) provides real-time dashboard for monitoring queue throughput, job counts, and failure rates.

---

**Q9. What is the difference between `ShouldQueue`, `ShouldBeUnique`, and `ShouldBeUniqueUntilProcessing`?**

**Answer:**

| Interface | Behavior |
|-----------|---------|
| `ShouldQueue` | Dispatches job to queue (async) |
| `ShouldBeUnique` | Only one instance of this job in queue at a time. Duplicate dispatches are discarded until job completes. |
| `ShouldBeUniqueUntilProcessing` | Unique only while waiting. Once picked up by worker, another can be queued. |

```php
class SyncUserToSearch implements ShouldQueue, ShouldBeUnique {
    public string $uniqueId;   // Unique per user

    public function __construct(private User $user) {
        $this->uniqueId = $user->id;
    }

    public int $uniqueFor = 3600; // Lock expires after 1 hour if job crashes
}
```

---

### 🏗️ Architecture & Design Patterns

**Q10. What is the Repository pattern in Laravel and when is it appropriate?**

**Answer:**
The Repository pattern abstracts data access behind an interface, decoupling controllers/services from Eloquent.

```php
// Interface
interface UserRepositoryInterface {
    public function findById(int $id): User;
    public function getActive(): Collection;
    public function create(array $data): User;
}

// Eloquent implementation
class EloquentUserRepository implements UserRepositoryInterface {
    public function findById(int $id): User {
        return User::findOrFail($id);
    }

    public function getActive(): Collection {
        return User::where('active', true)->orderBy('name')->get();
    }

    public function create(array $data): User {
        return User::create($data);
    }
}

// Bind in ServiceProvider
$this->app->bind(UserRepositoryInterface::class, EloquentUserRepository::class);
```

**When to use:**
- ✅ You need to switch data sources (Eloquent → API → cache)
- ✅ Complex testability requirements (mock the interface)
- ❌ Simple CRUD apps — adds unnecessary indirection

**Laravel's stance:** Use it when justified. Many Laravel apps don't need it — Eloquent models are already an Active Record abstraction.

---

**Q11. Explain Laravel's Service Container and Dependency Injection.**

**Answer:**
The Service Container is Laravel's IoC (Inversion of Control) container. It resolves class dependencies automatically.

```php
// Automatic resolution — no binding needed for concrete classes
class UserController extends Controller {
    public function __construct(
        private UserService $service,    // Auto-resolved
        private LoggerInterface $logger  // Needs binding (interface)
    ) {}
}

// Binding interface to implementation
// In AppServiceProvider::register()
$this->app->bind(LoggerInterface::class, FileLogger::class);

// Singleton — same instance throughout request
$this->app->singleton(PaymentGateway::class, function ($app) {
    return new StripeGateway(config('services.stripe.key'));
});

// Contextual binding — different implementations per class
$this->app->when(StripeController::class)
    ->needs(PaymentGateway::class)
    ->give(StripeGateway::class);

$this->app->when(PayPalController::class)
    ->needs(PaymentGateway::class)
    ->give(PayPalGateway::class);
```

---

**Q12. What is the Observer pattern in Laravel and when should you use it vs Events/Listeners?**

**Answer:**

**Model Observers** — hook into Eloquent model lifecycle:
```php
class UserObserver {
    public function created(User $user): void {
        // Send welcome email
    }
    public function deleting(User $user): void {
        // Clean up related data
    }
}

// Register in AppServiceProvider
User::observe(UserObserver::class);
```

**Events/Listeners** — for decoupled, application-level events:
```php
event(new UserRegistered($user)); // Dispatch event

class SendWelcomeEmail implements ShouldQueue {
    public function handle(UserRegistered $event): void {
        Mail::to($event->user)->send(new WelcomeMail());
    }
}
```

| Aspect | Observer | Events/Listeners |
|--------|---------|-----------------|
| Scope | Model lifecycle (created, updated, deleted) | Any application event |
| Coupling | Coupled to model | Decoupled |
| Async | No (sync only) | Yes (ShouldQueue) |
| Best for | Model-specific side effects | Cross-cutting concerns |

---

### 🆕 Latest Laravel Features (Laravel 11+)

**Q13. What are the major changes in Laravel 11?**

**Answer:**

| Change | Description |
|--------|-------------|
| **Slimmed skeleton** | Removed `Http/Kernel.php`, `Console/Kernel.php`, many config files merged |
| **bootstrap/app.php** | Single place to configure middleware, routing, exceptions |
| **Per-route middleware** | More granular middleware registration |
| **Health check route** | Built-in `/up` health endpoint |
| **Dumpable trait** | `->dd()`, `->dump()` chainable on any object |
| **Once helper** | `once(fn() => expensiveOperation())` — memoizes within request |
| **SQLite default** | Default database for fresh installs (development convenience) |
| **Pest default** | New projects ship with Pest instead of PHPUnit |

```php
// Laravel 11 bootstrap/app.php approach
return Application::configure(basePath: dirname(__DIR__))
    ->withRouting(
        web: __DIR__.'/../routes/web.php',
        api: __DIR__.'/../routes/api.php',
        health: '/up',
    )
    ->withMiddleware(function (Middleware $middleware) {
        $middleware->api(append: [EnsureTokenIsValid::class]);
    })
    ->withExceptions(function (Exceptions $exceptions) {
        $exceptions->render(fn (ModelNotFoundException $e) => response()->json(['error' => 'Not found'], 404));
    })
    ->create();
```

---

**Q14. What is Laravel Reverb and how does it compare to Pusher?**

**Answer:**
**Laravel Reverb** (introduced 2024) is a **first-party, self-hosted WebSocket server** built in PHP.

| Feature | Reverb | Pusher |
|---------|--------|--------|
| Hosting | Self-hosted | Cloud SaaS |
| Cost | Free (server cost only) | Free tier, then paid |
| Protocol | WebSocket + HTTP | WebSocket |
| Scaling | Horizontal via Redis pub/sub | Managed |
| Setup | `composer require laravel/reverb` | Account + credentials |
| Laravel integration | Native | Via Pusher SDK |

```bash
composer require laravel/reverb
php artisan reverb:install
php artisan reverb:start
```

**When to choose Reverb:** Cost-sensitive apps, data privacy requirements (no third-party), already running Redis.  
**When to choose Pusher:** Fastest time to market, managed scaling, less ops overhead.

---

**Q15. How does Laravel Octane improve performance?**

**Answer:**
Laravel Octane runs your app on **Swoole** or **RoadRunner** — keeping the app **booted in memory** between requests, eliminating the bootstrap cost per request.

```bash
composer require laravel/octane
php artisan octane:install --server=swoole
php artisan octane:start --workers=4 --task-workers=2
```

**Performance gains:** 5–10x fewer milliseconds per request because framework boot (service providers, config loading) happens **once**, not per request.

**Critical gotchas (Octane-specific issues):**
- **Memory leaks** — static properties persist between requests. Must be reset.
- **Stale singleton instances** — singletons bound in AppServiceProvider persist.
- **Request object contamination** — never store request data in singleton services.

```php
// ✅ Octane-safe: resolve fresh instance per request
$this->app->scoped(CurrentUser::class, function ($app) {
    return new CurrentUser($app['request']->user());
});
// scoped() = fresh per request in Octane, singleton in FPM
```

---

### 📊 Comparison & Trade-off Questions

**Q16. Sanctum vs Passport — when do you choose each?**

**Answer:**

| Feature | Sanctum | Passport |
|---------|---------|---------|
| Protocol | Simple token (Bearer) | Full OAuth2 |
| Complexity | Low | High |
| Setup time | Minutes | Hours |
| Refresh tokens | Manual | Built-in |
| Third-party grants | No | Yes (auth code, client credentials) |
| SPA authentication | Yes (cookie-based) | Overkill |
| Mobile APIs | Yes | Yes |
| Token abilities/scopes | Basic | Full OAuth2 scopes |

**Choose Sanctum when:** Your own SPA/mobile app consumes your API.  
**Choose Passport when:** Third parties need OAuth2 access (e.g., "Login with MyApp"), or you need client credentials flow for server-to-server auth.

---

**Q17. When would you use Events vs Jobs vs Scheduled Commands in Laravel?**

**Answer:**

| Tool | Use Case | Example |
|------|---------|---------|
| **Events + Listeners** | Reacting to something that just happened | `UserRegistered` → send welcome email, log audit |
| **Jobs (Queue)** | Deferred, potentially retryable work | Resize image, send 1000 emails, process payment |
| **Scheduled Commands** | Recurring time-based work | Daily report, weekly cleanup, cache warm-up |
| **Batches** | Parallel processing with completion callback | Import CSV (batch), then notify when done |

```php
// Batch — process with success/failure hooks
$batch = Bus::batch([
    new ProcessRow($chunk1),
    new ProcessRow($chunk2),
])->then(function (Batch $batch) {
    // All jobs succeeded
})->catch(function (Batch $batch, Throwable $e) {
    // First batch failure
})->dispatch();
```

---

**Q18. How would you implement API versioning in Laravel?**

**Answer:**

**Option 1: URI versioning (most common)**
```php
// routes/api.php
Route::prefix('v1')->namespace('App\Http\Controllers\Api\V1')->group(function () {
    Route::apiResource('users', UserController::class);
});

Route::prefix('v2')->namespace('App\Http\Controllers\Api\V2')->group(function () {
    Route::apiResource('users', UserController::class);
});
```

**Option 2: Header versioning**
```php
// Middleware
class ApiVersion {
    public function handle($request, $next, $version) {
        $acceptedVersion = $request->header('Accept-Version', 'v1');
        if ($acceptedVersion !== $version) {
            return response()->json(['error' => 'Version mismatch'], 400);
        }
        return $next($request);
    }
}
```

**Best practice:** URI versioning is explicit and cacheable (CDN can cache `/api/v1/` responses). Header versioning is "cleaner" REST but harder to test in browsers and cache.

---

**Q19. How do you test a Laravel application effectively?**

**Answer:**

```php
// Feature test — tests full HTTP stack
class UserRegistrationTest extends TestCase {
    use RefreshDatabase;

    public function test_user_can_register(): void {
        $response = $this->postJson('/api/register', [
            'name' => 'Alice',
            'email' => 'alice@example.com',
            'password' => 'password',
        ]);

        $response->assertCreated()
                 ->assertJsonStructure(['data' => ['id', 'name', 'email']]);

        $this->assertDatabaseHas('users', ['email' => 'alice@example.com']);
    }
}

// Unit test — tests single class
class UserServiceTest extends TestCase {
    public function test_deactivates_inactive_users(): void {
        $repo = Mockery::mock(UserRepositoryInterface::class);
        $repo->shouldReceive('getInactiveUsers')->once()->andReturn(collect([...]));
        $repo->shouldReceive('deactivate')->times(3);

        $service = new UserService($repo);
        $service->deactivateInactiveUsers();
    }
}
```

**Testing pyramid for Laravel:**
- **Unit tests:** Models, Services, Helpers — fast, no DB
- **Feature tests:** HTTP endpoints — use `RefreshDatabase`
- **Browser tests:** Critical user flows via Laravel Dusk (Selenium)

**`RefreshDatabase` vs `DatabaseTransactions`:**
- `RefreshDatabase` — runs migrations fresh each test class. Slower but clean.
- `DatabaseTransactions` — wraps each test in a transaction, rolled back after. Fast but doesn't work with queue tests.

---

**Q20. What is the difference between `query()`, `newQuery()`, and `newModelQuery()` in Eloquent?**

**Answer:**

```php
User::query()           // Returns a new Eloquent Builder — applies global scopes
User::newQuery()        // Same as query() — applies global scopes
User::newModelQuery()   // Returns builder WITHOUT global scopes

// Example: if User has SoftDeletes (global scope)
User::query()->get()         // Excludes soft-deleted (global scope applied)
User::newModelQuery()->get() // Includes soft-deleted (bypasses global scopes)
User::withTrashed()->get()   // Explicitly includes soft-deleted (convenience method)
```

**When matters:** When writing repositories, traits, or base classes that should not have global scopes applied (e.g., admin panel showing all records including soft-deleted).

---

**Q21. How does Eloquent's `chunk()` differ from `lazy()` and `cursor()`?**

**Answer:**

| Method | Memory | DB Queries | Best For |
|--------|--------|-----------|---------|
| `all()` / `get()` | All records in memory | 1 | Small datasets |
| `chunk(1000, fn)` | 1000 records at a time | N/1000 | Large datasets, stable ordering |
| `lazy()` | One record at a time | Multiple (uses cursor) | Moderate datasets |
| `cursor()` | One record at a time | 1 (streaming) | Large datasets, read-only |
| `chunkById()` | 1000 at a time | N/1000 | Safest for mutating data |

```php
// cursor() — uses PHP generators, single query with server-side cursor
User::where('active', true)->cursor()->each(function ($user) {
    $user->sendNewsletter();
});

// chunk() — multiple queries with LIMIT/OFFSET
// ⚠️ Don't modify records during chunk() — use chunkById() instead
User::where('active', true)->chunkById(1000, function ($users) {
    foreach ($users as $user) {
        $user->update(['notified' => true]);
    }
});
```

