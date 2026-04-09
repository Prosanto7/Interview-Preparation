
## 🔰 What is PHP?

**PHP** is a widely-used open-source **server-side scripting language** primarily used for web development. It’s embedded in HTML and is known for generating dynamic page content, handling forms, managing databases, and building full-scale web applications.

- First created by **Rasmus Lerdorf** in 1994.
- PHP files have `.php` extension.
- Executes on the **server**, not on the browser.

---

## ⚙️ Basic Features of PHP

| Feature             | Description                                                                 |
|---------------------|-----------------------------------------------------------------------------|
| Server-side         | Runs on web server like Apache or NGINX                                     |
| Easy to learn       | C-like syntax makes it accessible for beginners                             |
| Platform Independent| Works on Windows, Linux, macOS, etc.                                        |
| Database Support    | Works with MySQL, PostgreSQL, SQLite, Oracle, etc.                          |
| Open Source         | Free to use with a strong community                                         |

---

## 🚀 Advanced Features of PHP

Now let’s explore **advanced capabilities** that modern PHP (especially versions 7 and 8) offers:

---

### 1. ✅ **Object-Oriented Programming (OOP)**

PHP supports full **OOP concepts** like:

- Classes & Objects
- Inheritance
- Polymorphism
- Encapsulation
- Interfaces
- Traits

**Example:**
```php
class Car {
    public $brand;
    function __construct($brand) {
        $this->brand = $brand;
    }
    function drive() {
        echo "Driving a $this->brand car";
    }
}
```

---

### 2. 🧠 **Type Declarations (PHP 7+)**

- Helps catch bugs early and improve code readability.

**Example:**
```php
function sum(int $a, int $b): int {
    return $a + $b;
}
```

---

### 3. 🧪 **Error Handling with Exceptions**

- Replaces old-style error messages.

**Example:**
```php
try {
    throw new Exception("Something went wrong!");
} catch (Exception $e) {
    echo $e->getMessage();
}
```

---

### 4. 🧰 **Namespaces**

- Organize large codebases and avoid name conflicts.

```php
namespace App\Controllers;

class UserController {
    // ...
}
```

---

### 5. 🏗️ **Anonymous Functions & Closures**

- Useful for callbacks and functional programming.

```php
$greet = function($name) {
    return "Hello $name!";
};
echo $greet("World");
```

---

### 6. 🧵 **Generators**

- Yield values instead of returning everything at once – memory efficient for large datasets.

```php
function countUpTo($limit) {
    for ($i = 1; $i <= $limit; $i++) {
        yield $i;
    }
}
```

---

### 7. 📦 **Composer and Autoloading**

- PHP uses **Composer** to manage dependencies.

```bash
composer require monolog/monolog
```

- Autoloading ensures classes are loaded only when needed.

---

### 8. 🔐 **Security Features**

- **Password hashing:** `password_hash()` and `password_verify()`
- **Input sanitization:** `filter_input()`, `htmlspecialchars()`
- **Sessions & tokens** for authentication and CSRF protection

---

### 9. 🧠 **Reflection and Metadata**

- Inspect classes, methods, and properties at runtime.

```php
$reflector = new ReflectionClass('Car');
$methods = $reflector->getMethods();
```

---

### 10. 🧬 **Attributes (Annotations) – PHP 8+**

Attributes offer native support for metadata.

```php
#[Route("/home", methods: ["GET"])]
function homePage() {
    // ...
}
```

---

### 11. 🪄 **Match Expressions – PHP 8+**

Better alternative to `switch`.

```php
echo match ($statusCode) {
    200 => 'OK',
    404 => 'Not Found',
    500 => 'Server Error',
};
```

---

### 12. 🪝 **JIT Compiler (PHP 8)**

- **JIT (Just-In-Time)** compilation improves performance significantly for CPU-heavy tasks (not so much for web requests though).

---

### 13. 📈 **Performance Optimizations**

- **Opcache** support
- **Preloading** of classes
- **Improved memory management**

---

## 🛠️ Real World Use Cases

| Use Case            | PHP Feature Used                                    |
|---------------------|-----------------------------------------------------|
| User Authentication | Sessions, password hashing, Laravel Auth            |
| REST APIs           | Laravel/Lumen Framework, Type Declarations          |
| CMS Development     | OOP, Database, Composer, File Handling              |
| E-Commerce          | Traits, Namespaces, Secure Transactions             |
| Large Apps          | Interfaces, Abstract Classes, Dependency Injection  |

---

## 🧾 Summary: Why Learn Advanced PHP?

- Build **robust, secure** applications
- Scale your application better with **OOP**
- Integrate with modern **APIs** and tools (Stripe, PayPal, etc.)
- Create reusable and maintainable code
- Leverage frameworks like **Laravel**, **Symfony**, etc.

---


## 🔷 What is a Trait in PHP?

A **Trait** in PHP is a **mechanism for code reuse** in single inheritance languages like PHP.

> It allows you to **reuse sets of methods** across multiple classes, without using inheritance.

You can think of it as a **“mixin”** — a way to include methods in a class **without** extending a parent class.

---

## 🧠 Why Use Traits?

PHP supports **single inheritance**, meaning a class can only extend **one parent class**. But what if you need functionality from **multiple sources**?

That’s where **Traits** come in:
- Break down reusable behaviors into traits
- Add those traits to multiple unrelated classes

---

## 🛠️ Syntax of a Trait

```php
trait Logger {
    public function log($message) {
        echo "Log: $message\n";
    }
}

class User {
    use Logger;
}

class Product {
    use Logger;
}

$user = new User();
$user->log("User created");

$product = new Product();
$product->log("Product added");
```

✅ Both `User` and `Product` can now use the `log()` method without extending a common parent class.

---

## 🧩 Traits with Multiple Methods

```php
trait Helper {
    public function upper($text) {
        return strtoupper($text);
    }

    public function lower($text) {
        return strtolower($text);
    }
}
```

Use in a class:
```php
class Formatter {
    use Helper;
}
```

---

## 🔄 Multiple Traits in One Class

```php
trait A {
    public function sayA() { echo "A "; }
}

trait B {
    public function sayB() { echo "B "; }
}

class Test {
    use A, B;
}

$t = new Test();
$t->sayA(); // A
$t->sayB(); // B
```

---

## ⚔️ Conflicts and Resolutions in Traits

If two traits define the **same method**, PHP will throw a fatal error **unless** you resolve the conflict.

### 🛠 `insteadof` and `as`

```php
trait First {
    public function greet() {
        echo "Hello from First\n";
    }
}

trait Second {
    public function greet() {
        echo "Hello from Second\n";
    }
}

class Test {
    use First, Second {
        First::greet insteadof Second;     // Use greet from First
        Second::greet as greetSecond;      // Alias Second's greet
    }
}

$obj = new Test();
$obj->greet();        // Hello from First
$obj->greetSecond();  // Hello from Second
```

---

## 📚 Traits with Properties (PHP 7+)

Traits can define **properties**, but conflicts still need to be resolved manually if same-named properties appear.

```php
trait Config {
    public $version = "1.0";
}

class App {
    use Config;
}
```

---

## ✅ Traits vs Inheritance vs Interfaces

| Feature         | Trait                        | Inheritance                   | Interface                           |
|------------------|------------------------------|--------------------------------|--------------------------------------|
| Purpose          | Code reuse                   | IS-A relationship              | Contract enforcement                 |
| Can have methods | Yes                          | Yes                            | Only public method declarations      |
| Can have properties | Yes                      | Yes                            | ❌ (not allowed)                     |
| Multiple allowed | Yes (use multiple traits)    | ❌ Single inheritance only      | Yes (multiple interfaces allowed)    |
| Abstract methods | Yes                          | Yes                            | Yes (all are abstract by default)    |

---

## 🛍️ Real-Life Use Cases for Traits

| Use Case                          | Trait Example                             |
|----------------------------------|--------------------------------------------|
| Logging across multiple classes  | `LoggerTrait`                              |
| Soft deletes                     | `SoftDeleteTrait`                          |
| Timestamp management             | `HasTimestampsTrait`                       |
| Notification handling            | `NotifiableTrait`                          |
| Image processing methods         | `ImageHelperTrait`                         |

---

## ⚠️ Best Practices When Using Traits

1. 🔎 **Avoid overusing traits**: They can lead to “spaghetti” code if not structured well.
2. 🧪 **Test trait methods independently** when possible.
3. 📄 **Document your traits** clearly so you know what behavior is being included.
4. 🚫 Avoid using **stateful** (i.e., variable-heavy) logic in traits unless necessary.

---

## 💬 Summary

| Trait Feature     | Benefit                              |
|------------------|---------------------------------------|
| Reusability       | Add methods to multiple classes       |
| Conflict Handling | Use `insteadof` and `as`              |
| Cleaner Codebase  | Avoid duplication of logic            |
| Alternative to Inheritance | Bypasses single inheritance restriction |

---


## 🔍 **What Are Cookies and Sessions?**

| Feature    | Cookie | Session |
|------------|--------|---------|
| **Definition** | Small pieces of data stored **on the client’s browser**. | Server-side storage to keep user data **on the server**. |
| **Storage** | Client-side | Server-side |
| **Lifetime** | Can persist even after browser is closed if expiration is set. | Typically lasts until the browser is closed or user logs out. |

---

## 🧠 **Basic Use Cases**

### 🍪 Cookie Example:
```php
// Set cookie
setcookie("user", "JohnDoe", time() + (86400 * 30), "/"); // 30 days

// Access cookie
echo $_COOKIE["user"];
```

### 📦 Session Example:
```php
// Start session
session_start();

// Set session
$_SESSION["user"] = "JohnDoe";

// Access session
echo $_SESSION["user"];
```

---

## 🪜 **Key Differences Between Cookie and Session**

| Criteria                   | Cookie                                | Session                                  |
|---------------------------|----------------------------------------|------------------------------------------|
| **Storage Location**       | Stored in the **browser**             | Stored in the **server**                 |
| **Security**               | Less secure, as it's accessible via client | More secure, not exposed to client        |
| **Size Limit**             | Around 4KB                             | Larger (limited by server memory)        |
| **Lifespan**               | Can persist for a long time (settable) | Temporary by default (until browser closes) |
| **Performance**            | Slightly better (no server hit)       | Slightly slower (server access needed)   |
| **Access via JavaScript** | Yes (`document.cookie`)                | No, inaccessible from JS                 |
| **Best For**               | Remembering small, non-sensitive data | Handling secure or sensitive information |

---

## 🔐 **Security Concerns**

| Concern            | Cookie                             | Session                             |
|--------------------|------------------------------------|-------------------------------------|
| Tampering          | Can be manipulated by user         | Not directly accessible             |
| Hijacking          | Susceptible (unless secured)       | Session hijacking possible via ID   |
| Encryption Needed? | Recommended for sensitive data     | Session ID should be protected      |

---

## 📦 **Real-World Examples**

### 1. **Login System**

- **Session**: To store authenticated user's ID securely.
- **Cookie**: To "remember me" functionality (store username or token).

### 2. **Shopping Cart**

- Use **Session** to temporarily store cart items.
- Optionally, use **Cookie** to persist cart if user returns later.

---

## 🧰 **When to Use What?**

| Scenario                                | Best Choice | Why? |
|----------------------------------------|-------------|------|
| Login info / authentication            | Session     | More secure (server side) |
| "Remember Me" functionality            | Cookie      | Persists after closing browser |
| Shopping cart (temporary)              | Session     | Stored securely on server |
| Tracking user preferences (e.g. theme) | Cookie      | Doesn't require server storage |
| Form auto-fill                         | Cookie      | Can retain data for later visits |

---

## 🔄 **Can They Work Together?**

Yes! Often **sessions and cookies are used together** in real-world apps:

1. **Session stores login** info securely.
2. A **cookie stores a token** to auto-login next time (e.g., "remember me").

---

## 🧪 Advanced Notes

- **PHP Session uses Cookies under the hood** to store the `PHPSESSID`.
- If cookies are disabled, session can fall back to **URL parameters** (not recommended).
- Sessions require calling `session_start()` **at the top** of every PHP file that uses them.

---

## 📝 Summary Table

| Feature                 | Cookie                      | Session                      |
|------------------------|-----------------------------|------------------------------|
| Stored In              | Browser (client)            | Server (linked via cookie)   |
| Size Limit             | ~4KB                        | Server memory limit          |
| Accessible by JS       | ✅ Yes                      | ❌ No                        |
| Secure for Sensitive Data | ❌ No                   | ✅ Yes                       |
| Lifespan               | Customizable                | Ends with browser/session    |
| Use Case               | Preferences, light data     | Auth, cart, user sessions    |

---

## 📚 References

- PHP Manual: https://www.php.net/manual/en/
- PHP Type Declarations: https://www.php.net/manual/en/functions.arguments.php#functions.arguments.type-declaration
- PHP Exceptions: https://www.php.net/manual/en/language.exceptions.php
- PHP Namespaces: https://www.php.net/manual/en/language.namespaces.php
- PHP Attributes (PHP 8): https://www.php.net/manual/en/language.attributes.php
- PHP JIT: https://wiki.php.net/rfc/jit
- PSR Standards (PHP-FIG): https://www.php-fig.org/psr/

---

## 🎯 PHP Interview Questions — Critical Scenarios

---

### 🔴 High Traffic & Performance

**Q1. Your PHP app is serving 10,000 concurrent users and response times are degrading. What steps do you take?**

**Answer:**
1. **Profile first** — use Xdebug or Blackfire to find bottlenecks. Don't guess.
2. **Enable OPcache** — eliminates repeated script parsing. Most impactful single change for PHP performance.
3. **Implement caching layers:**
   - **Redis/Memcached** for query results, session storage, and computed data.
   - **HTTP caching** (Cache-Control headers, ETags) at the reverse proxy (Nginx/Varnish).
4. **Optimize database queries** — add indexes, use EXPLAIN, avoid N+1 problems.
5. **Horizontal scaling** — add more PHP-FPM workers behind a load balancer. PHP is stateless by nature, making this straightforward.
6. **Use a CDN** for static assets.
7. **Async processing** — offload heavy jobs (emails, PDF generation) to queues.

**Key insight:** OPcache alone can give 3–5x speedup. Always enable it in production.

---

**Q2. What is OPcache and why is it critical for PHP performance?**

**Answer:**
OPcache stores **precompiled PHP bytecode** in shared memory. Without it, PHP compiles every `.php` file on every request.

```ini
; php.ini
opcache.enable=1
opcache.memory_consumption=256
opcache.max_accelerated_files=10000
opcache.validate_timestamps=0  ; Disable in production for max speed
```

**Advantage:** Eliminates compilation overhead — reduces response time by 30–70%.  
**Disadvantage:** Code changes require OPcache reset (`opcache_reset()` or PHP-FPM reload) in production.

---

**Q3. What is PHP-FPM and how does it differ from mod_php?**

**Answer:**
| Feature | PHP-FPM | mod_php |
|---------|---------|---------|
| Process management | Separate pool of workers | Embedded in Apache |
| Memory | Only PHP workers use memory | Every Apache process loads PHP |
| Scalability | Independent scaling | Tied to Apache workers |
| Webserver support | Nginx, Apache, any | Apache only |
| Restart required | PHP restart only | Full Apache restart |

**PHP-FPM** is the modern standard — it decouples the web server from PHP, allows per-pool configuration, and handles process management (idle timeout, max requests before restart to prevent memory leaks).

---

**Q4. How does PHP handle memory leaks, and how do you prevent them?**

**Answer:**
PHP uses **reference counting** with a **cycle collector** for garbage collection. Memory leaks occur when:
- Circular references are not broken
- Long-running CLI scripts accumulate objects
- `static` variables in functions accumulate state

**Prevention:**
```php
// Unset large variables when done
unset($largeArray);

// Force garbage collection in long-running scripts
gc_collect_cycles();

// Use generators instead of loading entire datasets into memory
function readLargeFile($file) {
    $handle = fopen($file, 'r');
    while (!feof($handle)) {
        yield fgets($handle);
    }
    fclose($handle);
}
```

**PHP 8 improvement:** Fibers (lightweight coroutines) allow better memory control in async contexts.

---

### 🔐 Security

**Q5. What are the most critical PHP security vulnerabilities and how do you mitigate them?**

**Answer:**

| Vulnerability | How It Happens | Mitigation |
|--------------|---------------|------------|
| **SQL Injection** | Unsanitized input in raw queries | Prepared statements with PDO |
| **XSS** | Unescaped user output in HTML | `htmlspecialchars()`, CSP headers |
| **CSRF** | Forged requests from other sites | CSRF tokens per session |
| **RCE via file upload** | Executing uploaded PHP files | Validate MIME, store outside webroot |
| **Path Traversal** | `../` in filenames | `basename()`, whitelist paths |
| **Session Hijacking** | Stolen session ID | `session_regenerate_id(true)`, HttpOnly/Secure cookies |

```php
// ✅ Correct: Prepared statements
$stmt = $pdo->prepare("SELECT * FROM users WHERE email = ?");
$stmt->execute([$_POST['email']]);

// ✅ Correct: Output escaping
echo htmlspecialchars($userInput, ENT_QUOTES, 'UTF-8');

// ✅ Correct: Password hashing
$hash = password_hash($password, PASSWORD_ARGON2ID); // Prefer Argon2id over bcrypt
```

---

**Q6. What is the difference between `password_hash()` with `PASSWORD_BCRYPT` vs `PASSWORD_ARGON2ID`?**

**Answer:**
- **bcrypt**: Time-tested, CPU-hard, widely supported. Default cost factor is 10 (2^10 iterations).
- **Argon2id**: Winner of the Password Hashing Competition (2015). **Memory-hard** (resists GPU/ASIC attacks), configurable memory cost, time cost, and parallelism. Preferred for new applications.

```php
// Argon2id (PHP 7.3+, recommended)
$hash = password_hash($password, PASSWORD_ARGON2ID, [
    'memory_cost' => 65536,  // 64MB
    'time_cost'   => 4,
    'threads'     => 2,
]);
```

---

### ⚖️ PHP 7 vs PHP 8 — Key Differences

**Q7. What are the most impactful features introduced in PHP 8.x?**

**Answer:**

| Feature | PHP Version | Impact |
|---------|------------|--------|
| **JIT Compiler** | 8.0 | 3x faster for CPU-bound tasks (math, algorithms). Minimal gain for DB-heavy web apps. |
| **Named Arguments** | 8.0 | `array_slice(array: $arr, offset: 1, length: 2)` — clearer function calls |
| **Match Expression** | 8.0 | Type-safe switch alternative, no fallthrough, throws `UnhandledMatchError` |
| **Nullsafe Operator `?->`** | 8.0 | `$user?->getAddress()?->city` — replaces nested null checks |
| **Union Types** | 8.0 | `function foo(int\|string $id): bool\|null` |
| **Fibers** | 8.1 | Lightweight coroutines for async code |
| **Enums** | 8.1 | Native enumerations replacing class constants |
| **Readonly Properties** | 8.1 | Immutable after initialization |
| **Intersection Types** | 8.1 | `Countable&Iterator $value` |
| **readonly Classes** | 8.2 | All properties readonly by default |
| **Disjunctive Normal Form Types** | 8.2 | `(A&B)\|null` |
| **Property Hooks** | 8.4 | Get/set accessors directly on properties |
| **`#[\Deprecated]` Attribute** | 8.4 | Mark your own code as deprecated |

---

**Q8. What is a PHP Fiber and when would you use it?**

**Answer:**
Fibers (PHP 8.1) are **pausable functions** — lightweight coroutines that can suspend and resume execution.

```php
$fiber = new Fiber(function (): void {
    $value = Fiber::suspend('first suspension');
    echo "Value received: " . $value . "\n"; // prints "hello"
});

$value = $fiber->start();       // Runs until suspend, $value = 'first suspension'
$fiber->resume('hello');        // Resumes fiber with 'hello'
```

**Use cases:** Building async I/O libraries (like ReactPHP), cooperative multitasking, implementing async/await patterns.  
**Not for:** CPU parallelism (PHP is still single-threaded per process for that use `pcntl_fork` or `parallel` extension).

---

### 🧵 Caching Strategies

**Q9. Explain different caching strategies in PHP applications.**

**Answer:**

| Strategy | What is Cached | Tools | Best For |
|---------|---------------|-------|---------|
| **Opcode cache** | Compiled PHP bytecode | OPcache | Every PHP app |
| **Object cache** | Query results, computed values | Redis, Memcached | Frequent reads |
| **Fragment cache** | Parts of rendered HTML | Redis + custom logic | Heavy view rendering |
| **Full-page cache** | Entire HTTP response | Varnish, Nginx proxy | Public, rarely-changing pages |
| **HTTP cache** | Browser-side caching | Cache-Control headers | Static or semi-static content |

**Cache-aside pattern (most common):**
```php
function getUser(int $id): array {
    $cacheKey = "user:{$id}";
    $cached = $redis->get($cacheKey);
    if ($cached) {
        return json_decode($cached, true);
    }
    $user = $db->query("SELECT * FROM users WHERE id = ?", [$id]);
    $redis->setex($cacheKey, 3600, json_encode($user)); // TTL: 1 hour
    return $user;
}
```

---

**Q10. What is cache stampede and how do you prevent it?**

**Answer:**
A **cache stampede** (dog-pile effect) happens when many requests simultaneously find an expired cache entry and all query the database at once.

**Solutions:**
1. **Mutex/Lock:** Only one process rebuilds the cache, others wait.
   ```php
   if ($redis->set("lock:{$key}", 1, ['NX', 'EX' => 5])) {
       $data = expensiveQuery();
       $redis->set($key, serialize($data), ['EX' => 3600]);
       $redis->del("lock:{$key}");
   }
   ```
2. **Probabilistic early expiration** (XFetch algorithm): Stochastically refresh before expiry.
3. **Background refresh:** Serve stale data while rebuilding asynchronously (stale-while-revalidate).

---

### 🔄 OOP & Design

**Q11. What is the difference between an abstract class and an interface in PHP? When do you use each?**

**Answer:**

| Feature | Abstract Class | Interface |
|---------|---------------|-----------|
| Methods | Can have concrete methods | Only method signatures (PHP 8 allows `default` in interface via abstract class workaround) |
| Properties | Yes | No (only constants) |
| Constructor | Yes | No |
| Extends | Single only | Multiple allowed |
| Use when | Sharing implementation between related classes | Defining a contract for unrelated classes |

```php
// Interface: contract — "CAN DO"
interface Cacheable {
    public function getCacheKey(): string;
    public function getCacheTtl(): int;
}

// Abstract class: base behavior — "IS A"
abstract class BaseModel {
    abstract public function validate(): bool;

    public function save(): void {
        if ($this->validate()) {
            // common save logic
        }
    }
}
```

**Rule of thumb:** Use interfaces for cross-cutting capabilities (Loggable, Cacheable, Serializable). Use abstract classes for shared implementation (BaseController, BaseRepository).

---

**Q12. Explain late static binding in PHP.**

**Answer:**
`static::` resolves to the **class that was called at runtime**, not where the method was defined. This is important in inheritance chains.

```php
class Base {
    public static function create(): static {
        return new static(); // Returns the actual called class
    }
    public static function className(): string {
        return static::class; // Late static binding
    }
}

class Child extends Base {}

$obj = Child::create();    // Returns Child instance (not Base)
echo Child::className();   // "Child" (not "Base")
```

Without `static::`, using `self::` would always return `Base`.

---

**Q13. What is the PSR standards and which ones are most important to know?**

**Answer:**

| PSR | Name | Importance |
|-----|------|-----------|
| **PSR-1** | Basic Coding Standard | File encoding, class names, methods |
| **PSR-2/12** | Coding Style | Indentation, braces, line length |
| **PSR-3** | Logger Interface | `$logger->info()`, `warning()`, etc. |
| **PSR-4** | Autoloading | Namespace → directory mapping used by Composer |
| **PSR-7** | HTTP Message Interfaces | Request/Response objects (used by Symfony, Slim) |
| **PSR-11** | Container Interface | `$container->get('ServiceName')` |
| **PSR-15** | HTTP Handlers | Middleware pipeline (used by Laravel, Slim) |

PSR-4 and PSR-3 are the most commonly referenced in interviews.

---

### 📊 Comparison Questions

**Q14. When would you choose PHP over Node.js or Python for a backend?**

**Answer:**

| Criterion | PHP | Node.js | Python |
|-----------|-----|---------|--------|
| **Web focus** | Native web language, batteries included | General purpose with web libs | General purpose |
| **Learning curve** | Low — designed for web from day one | Medium | Low-Medium |
| **Async I/O** | Via Swoole/ReactPHP (not native) | Native event loop | asyncio |
| **Ecosystem** | Composer, Laravel, WordPress | npm, Express, NestJS | pip, Django, FastAPI |
| **Performance** | Good with OPcache, PHP 8 JIT | Excellent for I/O bound | Good, slower for CPU |
| **Hiring pool** | Very large (WordPress market) | Large | Large |

**Choose PHP when:** Building traditional web apps, CMS, e-commerce (Magento, WooCommerce), team has PHP expertise, or using Laravel's productivity benefits.

---

**Q15. What is the N+1 query problem in PHP and how do you solve it?**

**Answer:**
N+1 occurs when you fetch N records then make 1 additional query per record — resulting in N+1 total queries.

```php
// ❌ N+1 Problem
$users = fetchAll("SELECT * FROM users");   // 1 query
foreach ($users as $user) {
    $orders = fetchAll("SELECT * FROM orders WHERE user_id = ?", [$user['id']]); // N queries
}

// ✅ Solution 1: JOIN
$data = fetchAll("
    SELECT users.*, orders.* FROM users
    LEFT JOIN orders ON orders.user_id = users.id
");

// ✅ Solution 2: Eager loading pattern
$userIds = array_column($users, 'id');
$orders = fetchAll("SELECT * FROM orders WHERE user_id IN (?)", [implode(',', $userIds)]);
// Group orders by user_id in PHP
```

---

### 🆕 Latest PHP Concepts (PHP 8.3 / 8.4)

**Q16. What are PHP 8.4 Property Hooks?**

**Answer:**
Property hooks (PHP 8.4) bring **get/set accessors** directly onto properties, eliminating boilerplate getter/setter methods.

```php
class User {
    public string $name {
        get => strtoupper($this->name);
        set(string $value) {
            if (strlen($value) < 2) throw new ValueError("Name too short");
            $this->name = $value;
        }
    }
}

$user = new User();
$user->name = 'alice';      // calls set hook
echo $user->name;           // "ALICE" — calls get hook
```

**Advantage:** Reduces boilerplate, keeps properties as first-class citizens.  
**Disadvantage:** New syntax — requires PHP 8.4, team familiarity needed.

---

**Q17. What are PHP Enums (8.1) and how do they compare to class constants?**

**Answer:**
```php
// Old approach: class constants — no type safety
class Status {
    const ACTIVE = 'active';
    const INACTIVE = 'inactive';
}
// Can be passed as plain string, no validation

// PHP 8.1 Backed Enum — type-safe, serializable
enum Status: string {
    case Active = 'active';
    case Inactive = 'inactive';

    public function label(): string {
        return match($this) {
            Status::Active => 'Active User',
            Status::Inactive => 'Inactive User',
        };
    }
}

$status = Status::Active;
$status->value;     // 'active'
Status::from('active');  // Status::Active
Status::tryFrom('unknown'); // null (no exception)
```

**Advantages over constants:** Type safety, IDE autocompletion, can implement interfaces, can have methods, compatible with `match`.

---

### ⚡ Advanced Scenarios

**Q18. How would you handle a PHP script that needs to process 1 million records from a database?**

**Answer:**
Never load all records into memory at once. Use chunking or streaming:

```php
// ✅ Option 1: Chunked processing with LIMIT/OFFSET
$offset = 0;
$chunkSize = 1000;
while (true) {
    $records = $db->query("SELECT * FROM records LIMIT ? OFFSET ?", [$chunkSize, $offset]);
    if (empty($records)) break;
    foreach ($records as $record) {
        processRecord($record);
    }
    $offset += $chunkSize;
    gc_collect_cycles(); // Prevent memory buildup
}

// ✅ Option 2: Cursor-based (more efficient — no OFFSET cost)
$lastId = 0;
while (true) {
    $records = $db->query("SELECT * FROM records WHERE id > ? ORDER BY id LIMIT 1000", [$lastId]);
    if (empty($records)) break;
    foreach ($records as $record) {
        processRecord($record);
        $lastId = $record['id'];
    }
}

// ✅ Option 3: Generator-based PDO streaming
function streamRecords(PDO $pdo): Generator {
    $stmt = $pdo->query("SELECT * FROM records");
    while ($row = $stmt->fetch(PDO::FETCH_ASSOC)) {
        yield $row;
    }
}
```

**Cursor-based pagination** is preferred over OFFSET for large datasets — OFFSET N scans and discards N rows.

---

**Q19. Explain the difference between `include`, `require`, `include_once`, and `require_once`.**

**Answer:**

| Function | File not found | Duplicate include |
|---------|---------------|-------------------|
| `include` | Warning, continues | Re-includes file |
| `require` | Fatal error, stops | Re-includes file |
| `include_once` | Warning, continues | Skips if already included |
| `require_once` | Fatal error, stops | Skips if already included |

**Modern PHP:** Composer autoloading (`require_once __DIR__ . '/vendor/autoload.php'`) replaces manual includes entirely. Manual `include`/`require` should be rare in modern codebases.

---

**Q20. What is the difference between `==` and `===` in PHP, and what are the dangerous type juggling edge cases?**

**Answer:**
- `==` performs **type coercion** — compares values after conversion.
- `===` checks **value AND type** — no coercion.

**Dangerous edge cases (PHP 7 and below):**
```php
0 == "foo"      // true (string "foo" coerced to 0)
0 == "0"        // true
"1" == "01"     // true
"" == null      // true
false == []     // true
"100" == "1e2"  // true (scientific notation)
```

**PHP 8 fix:** `0 == "foo"` is now **false** in PHP 8 — non-numeric strings no longer coerce to 0. Always use `===` in security-sensitive comparisons (token comparison, etc.).

```php
// ✅ Safe: hash_equals for timing-attack-safe comparison
hash_equals($knownToken, $userToken);
```
