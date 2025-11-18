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

