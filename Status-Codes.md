# 🔢 HTTP Status Codes: Complete Guide

## 📖 Introduction

HTTP status codes are three-digit numbers returned by web servers in response to HTTP requests. They indicate the result of the request and help clients understand what happened. Status codes are defined in RFC 7231 and other HTTP specifications.

---

## 🏗️ Status Code Categories

### 1xx - Informational Responses
Temporary responses indicating the request is being processed.

### 2xx - Successful Responses
The request was successfully received, understood, and accepted.

### 3xx - Redirection Messages
Further action needs to be taken to complete the request.

### 4xx - Client Error Responses
The request contains bad syntax or cannot be fulfilled.

### 5xx - Server Error Responses
The server failed to fulfill a valid request.

---

## 📊 Detailed Status Codes

### 1xx Informational

#### 100 Continue
The server has received the request headers and the client should proceed to send the request body.

**Use Case:** Large file uploads where client waits for server approval before sending data.

```php
// Laravel example - handling 100 Continue
public function uploadLargeFile(Request $request)
{
    // Server sends 100 Continue automatically for large uploads
    // Client receives this and continues sending data
    $file = $request->file('large_file');
    // Process file...
    return response()->json(['message' => 'File uploaded'], 200);
}
```

**Real-life Example:** Uploading a large video file to YouTube - browser waits for server confirmation before sending the full file.

#### 101 Switching Protocols
The server is switching protocols as requested by the client.

**Use Case:** Upgrading from HTTP to WebSocket.

```php
// WebSocket upgrade example
if ($request->header('Upgrade') === 'websocket') {
    return response('', 101)
        ->header('Upgrade', 'websocket')
        ->header('Connection', 'Upgrade');
}
```

**Real-life Example:** Chat applications switching from HTTP polling to WebSocket connections.

#### 102 Processing (WebDAV)
The server has received and is processing the request, but no response is available yet.

**Use Case:** Long-running operations in WebDAV.

---

### 2xx Success

#### 200 OK
The request has succeeded.

**Use Case:** Standard successful response.

```php
// Laravel API response
public function getUser($id)
{
    $user = User::find($id);
    if ($user) {
        return response()->json($user, 200);
    }
    return response()->json(['error' => 'User not found'], 404);
}
```

**Real-life Example:** Successfully retrieving user profile data from a REST API.

#### 201 Created
The request has been fulfilled and a new resource has been created.

**Use Case:** After creating a new record.

```php
public function createPost(Request $request)
{
    $post = Post::create($request->validated());
    return response()->json($post, 201)
        ->header('Location', route('posts.show', $post->id));
}
```

**Real-life Example:** Creating a new tweet on Twitter or posting on Facebook.

#### 202 Accepted
The request has been accepted for processing, but the processing has not been completed.

**Use Case:** Asynchronous operations, background jobs.

```php
public function processReport(Request $request)
{
    // Queue the report generation
    ProcessReport::dispatch($request->all());
    return response()->json([
        'message' => 'Report processing started',
        'job_id' => $jobId
    ], 202);
}
```

**Real-life Example:** Submitting a complex analytics report that will be processed in the background.

#### 204 No Content
The server successfully processed the request but is not returning any content.

**Use Case:** DELETE operations, successful updates with no response body.

```php
public function deletePost($id)
{
    Post::findOrFail($id)->delete();
    return response()->noContent(); // 204
}
```

**Real-life Example:** Deleting an email from Gmail - successful deletion with no content returned.

#### 205 Reset Content
The server successfully processed the request, but is instructing the client to reset the view.

**Use Case:** After form submission, tell browser to reset the form.

#### 206 Partial Content
The server is delivering only part of the resource due to a range header.

**Use Case:** Video streaming, large file downloads with resume capability.

```php
// Laravel range request handling
public function streamVideo(Request $request, $filename)
{
    $file = Storage::path($filename);
    $size = filesize($file);

    $range = $request->header('Range');
    if ($range) {
        // Parse range and return partial content
        return response()->file($file, 206)
            ->header('Content-Range', "bytes $start-$end/$size");
    }

    return response()->file($file);
}
```

**Real-life Example:** Watching a YouTube video - browser requests specific byte ranges for smooth streaming.

---

### 3xx Redirection

#### 301 Moved Permanently
The resource has been permanently moved to a new URL.

**Use Case:** SEO-friendly permanent redirects.

```php
// Laravel permanent redirect
Route::get('/old-url', function () {
    return redirect('/new-url', 301);
});
```

**Real-life Example:** Company rebranding - old domain redirects to new domain permanently.

#### 302 Found (Moved Temporarily)
The resource is temporarily located at a different URL.

**Use Case:** Temporary redirects.

```php
// Temporary redirect for maintenance
Route::get('/admin', function () {
    if (config('app.maintenance_mode')) {
        return redirect('/maintenance', 302);
    }
    return view('admin.dashboard');
});
```

**Real-life Example:** Website under maintenance - temporarily redirect users to a maintenance page.

#### 303 See Other
The response to the request can be found under another URI using a GET method.

**Use Case:** After POST/PUT/DELETE operations, redirect to a different page.

```php
public function updateProfile(Request $request)
{
    $request->user()->update($request->validated());
    return redirect('/profile')->setStatusCode(303);
}
```

**Real-life Example:** After updating profile, redirect to profile view page.

#### 304 Not Modified
The resource has not been modified since the version specified by the request headers.

**Use Case:** Caching - client has current version.

```php
public function getPosts(Request $request)
{
    $posts = Post::all();
    $lastModified = $posts->max('updated_at');

    if ($request->header('If-Modified-Since') === $lastModified->toRfc7231String()) {
        return response()->noContent(304);
    }

    return response()->json($posts)
        ->header('Last-Modified', $lastModified->toRfc7231String());
}
```

**Real-life Example:** Browser caching - checking if CSS/JS files have changed since last visit.

#### 307 Temporary Redirect
The request should be repeated with another URI, but future requests should still use the original URI.

**Use Case:** Temporary redirects that preserve request method.

#### 308 Permanent Redirect
The resource has been permanently moved, and future requests should use the new URI.

**Use Case:** Permanent redirects that preserve request method.

---

### 4xx Client Errors

#### 400 Bad Request
The server cannot or will not process the request due to an apparent client error.

**Use Case:** Malformed request data, validation errors.

```php
public function createUser(Request $request)
{
    $validator = Validator::make($request->all(), [
        'email' => 'required|email|unique:users',
        'password' => 'required|min:8',
    ]);

    if ($validator->fails()) {
        return response()->json([
            'error' => 'Bad Request',
            'messages' => $validator->errors()
        ], 400);
    }

    // Create user...
}
```

**Real-life Example:** Submitting a form with invalid email format or missing required fields.

#### 401 Unauthorized
The request lacks valid authentication credentials.

**Use Case:** Missing or invalid authentication token.

```php
public function getProtectedData(Request $request)
{
    if (!$request->user()) {
        return response()->json([
            'error' => 'Unauthorized',
            'message' => 'Authentication required'
        ], 401)->header('WWW-Authenticate', 'Bearer');
    }

    return response()->json(['data' => 'protected content']);
}
```

**Real-life Example:** Trying to access a private GitHub repository without authentication.

#### 403 Forbidden
The server understood the request but refuses to authorize it.

**Use Case:** Insufficient permissions, IP blocking.

```php
public function deleteUser($id)
{
    $user = User::findOrFail($id);

    if ($user->id !== auth()->id() && !auth()->user()->isAdmin()) {
        return response()->json([
            'error' => 'Forbidden',
            'message' => 'You cannot delete this user'
        ], 403);
    }

    $user->delete();
    return response()->noContent();
}
```

**Real-life Example:** Regular user trying to access admin panel in a web application.

#### 404 Not Found
The server can't find the requested resource.

**Use Case:** Resource doesn't exist.

```php
public function getUser($id)
{
    $user = User::find($id);

    if (!$user) {
        return response()->json([
            'error' => 'Not Found',
            'message' => 'User not found'
        ], 404);
    }

    return response()->json($user);
}
```

**Real-life Example:** Typing a wrong URL in the browser address bar.

#### 405 Method Not Allowed
The request method is known by the server but is not supported by the target resource.

**Use Case:** API endpoint doesn't support the HTTP method used.

```php
// Laravel automatically handles this for routes that don't exist
// But you can customize:
Route::resource('posts', PostController::class)->only(['index', 'show']);
// This will return 405 for POST, PUT, DELETE requests
```

**Real-life Example:** Trying to POST to a read-only API endpoint.

#### 406 Not Acceptable
The server cannot produce a response matching the list of acceptable values defined in the request's proactive content negotiation headers.

**Use Case:** API versioning, content type negotiation.

```php
public function getData(Request $request)
{
    $accept = $request->header('Accept');

    if ($accept === 'application/xml') {
        return response()->xml($data);
    } elseif ($accept === 'application/json') {
        return response()->json($data);
    } else {
        return response()->json([
            'error' => 'Not Acceptable',
            'message' => 'Supported formats: JSON, XML'
        ], 406);
    }
}
```

**Real-life Example:** Client requesting XML format but API only supports JSON.

#### 409 Conflict
The request conflicts with the current state of the server.

**Use Case:** Resource already exists, version conflicts.

```php
public function createUser(Request $request)
{
    if (User::where('email', $request->email)->exists()) {
        return response()->json([
            'error' => 'Conflict',
            'message' => 'User with this email already exists'
        ], 409);
    }

    $user = User::create($request->validated());
    return response()->json($user, 201);
}
```

**Real-life Example:** Trying to create a user account with an email that already exists.

#### 410 Gone
The target resource is no longer available at the origin server and this condition is likely to be permanent.

**Use Case:** Deleted resources that won't return.

```php
public function getDeletedResource($id)
{
    // Resource was permanently deleted
    return response()->json([
        'error' => 'Gone',
        'message' => 'This resource has been permanently removed'
    ], 410);
}
```

**Real-life Example:** Accessing a deleted social media post that won't be restored.

#### 413 Payload Too Large
The request entity is larger than limits defined by server.

**Use Case:** File upload size limits.

```php
// Laravel handles this automatically, but you can customize
public function uploadFile(Request $request)
{
    if ($request->file('file')->getSize() > 10 * 1024 * 1024) { // 10MB
        return response()->json([
            'error' => 'Payload Too Large',
            'message' => 'File size exceeds 10MB limit'
        ], 413);
    }

    // Process file...
}
```

**Real-life Example:** Uploading a file larger than the server's maximum allowed size.

#### 415 Unsupported Media Type
The server refuses to accept the request because the payload format is in an unsupported format.

**Use Case:** Wrong content type in request.

```php
public function createPost(Request $request)
{
    if ($request->header('Content-Type') !== 'application/json') {
        return response()->json([
            'error' => 'Unsupported Media Type',
            'message' => 'Content-Type must be application/json'
        ], 415);
    }

    // Process JSON data...
}
```

**Real-life Example:** Sending form data to an API that only accepts JSON.

#### 422 Unprocessable Entity
The server understands the content type and syntax but was unable to process the contained instructions.

**Use Case:** Semantic validation errors.

```php
public function updateUser(Request $request, $id)
{
    $user = User::findOrFail($id);

    try {
        $user->update($request->validated());
        return response()->json($user);
    } catch (\Exception $e) {
        return response()->json([
            'error' => 'Unprocessable Entity',
            'message' => 'Unable to process the request data'
        ], 422);
    }
}
```

**Real-life Example:** API validation errors that aren't syntax issues but logical problems.

#### 429 Too Many Requests
The user has sent too many requests in a given amount of time.

**Use Case:** Rate limiting.

```php
// Laravel Throttle middleware automatically handles this
// Custom implementation:
public function getData(Request $request)
{
    $key = 'api_calls_' . $request->ip();
    $calls = Cache::get($key, 0);

    if ($calls >= 100) { // 100 requests per hour
        return response()->json([
            'error' => 'Too Many Requests',
            'message' => 'Rate limit exceeded'
        ], 429)->header('Retry-After', 3600);
    }

    Cache::put($key, $calls + 1, 3600);
    return response()->json(['data' => 'success']);
}
```

**Real-life Example:** Twitter API rate limiting when making too many requests per hour.

---

### 5xx Server Errors

#### 500 Internal Server Error
The server has encountered a situation it doesn't know how to handle.

**Use Case:** Unexpected errors, database connection failures.

```php
public function getUsers()
{
    try {
        $users = User::all();
        return response()->json($users);
    } catch (\Exception $e) {
        // Log the error
        Log::error('Failed to fetch users: ' . $e->getMessage());

        return response()->json([
            'error' => 'Internal Server Error',
            'message' => 'Something went wrong on our end'
        ], 500);
    }
}
```

**Real-life Example:** Database server crashes while processing a request.

#### 501 Not Implemented
The request method is not supported by the server and cannot be handled.

**Use Case:** Unsupported HTTP methods.

```php
public function handleRequest(Request $request)
{
    $method = $request->method();

    if (!in_array($method, ['GET', 'POST', 'PUT', 'DELETE'])) {
        return response()->json([
            'error' => 'Not Implemented',
            'message' => "Method $method is not supported"
        ], 501);
    }

    // Handle request...
}
```

**Real-life Example:** Server doesn't support PATCH method for partial updates.

#### 502 Bad Gateway
The server, while working as a gateway to get a response needed to handle the request, got an invalid response.

**Use Case:** Proxy/load balancer issues.

**Real-life Example:** API gateway receives malformed response from backend service.

#### 503 Service Unavailable
The server is not ready to handle the request (maintenance, overload).

**Use Case:** Maintenance mode, server overload.

```php
public function getData(Request $request)
{
    if (config('app.maintenance_mode')) {
        return response()->json([
            'error' => 'Service Unavailable',
            'message' => 'Server is under maintenance'
        ], 503)->header('Retry-After', 3600);
    }

    return response()->json(['data' => 'available']);
}
```

**Real-life Example:** Website showing maintenance page during updates.

#### 504 Gateway Timeout
The server is acting as a gateway and cannot get a response in time.

**Use Case:** Slow backend services, network timeouts.

**Real-life Example:** API gateway times out waiting for database query to complete.

#### 505 HTTP Version Not Supported
The HTTP version used in the request is not supported by the server.

**Use Case:** Outdated client using old HTTP version.

---

## 🛠️ Laravel Status Code Helpers

```php
// Common status code responses
return response()->noContent();           // 204
return response()->created($data);        // 201
return response()->accepted();            // 202
return response()->notFound();            // 404
return response()->forbidden();           // 403
return response()->unauthorized();        // 401
return response()->badRequest();          // 400
return response()->serverError();         // 500
return response()->serviceUnavailable();  // 503
```

---

## 📊 Status Code Usage Statistics

Based on real-world data:
- **200 OK**: ~50-60% of all responses
- **404 Not Found**: ~10-15%
- **301/302 Redirects**: ~5-10%
- **500 Internal Server Error**: ~2-5%
- **401 Unauthorized**: ~2-3%
- **403 Forbidden**: ~1-2%
- Other codes: <1% each

---

## 🎯 Status Codes Interview Questions

---

### Q1. What is the difference between 401 Unauthorized and 403 Forbidden?

**Answer:**

| Aspect | 401 Unauthorized | 403 Forbidden |
|--------|------------------|---------------|
| **Meaning** | Authentication required/missing | Authentication present but insufficient permissions |
| **Retry** | Can retry with valid credentials | Cannot retry with same credentials |
| **Headers** | Often includes WWW-Authenticate | No special headers |
| **Example** | No API token provided | User authenticated but not admin |

```php
// 401 - No authentication
public function getPrivateData(Request $request)
{
    if (!$request->bearerToken()) {
        return response()->json(['error' => 'Authentication required'], 401);
    }
}

// 403 - Insufficient permissions
public function deleteUser($id)
{
    if (!auth()->user()->isAdmin()) {
        return response()->json(['error' => 'Admin access required'], 403);
    }
}
```

---

### Q2. When should you use 201 Created vs 200 OK?

**Answer:**

**Use 201 Created when:**
- A new resource was successfully created
- The response should include a Location header pointing to the new resource
- The operation resulted in resource creation

**Use 200 OK when:**
- The request was successful but no new resource was created
- Retrieving existing data
- Updating existing resources

```php
// 201 Created - new resource
public function createPost(Request $request)
{
    $post = Post::create($request->validated());
    return response()->json($post, 201)
        ->header('Location', route('posts.show', $post->id));
}

// 200 OK - existing resource
public function updatePost(Request $request, $id)
{
    $post = Post::findOrFail($id);
    $post->update($request->validated());
    return response()->json($post, 200);
}
```

---

### Q3. Explain the difference between 301, 302, 307, and 308 redirects.

**Answer:**

| Code | Type | Method Preservation | Cacheable | Use Case |
|------|------|-------------------|-----------|----------|
| **301** | Permanent | No (changes to GET) | Yes | SEO redirects, permanent URL changes |
| **302** | Temporary | No (changes to GET) | No | Temporary redirects |
| **307** | Temporary | Yes | No | Preserve method for temporary redirects |
| **308** | Permanent | Yes | Yes | Preserve method for permanent redirects |

```php
// 301 - Permanent redirect (SEO friendly)
Route::get('/old-blog', function () {
    return redirect('/blog', 301);
});

// 302 - Temporary redirect
Route::get('/maintenance', function () {
    if (config('app.maintenance')) {
        return redirect('/maintenance-page', 302);
    }
});

// 307 - Temporary redirect preserving method
Route::post('/api/v1/process', function () {
    return redirect('/api/v2/process', 307);
});

// 308 - Permanent redirect preserving method
Route::post('/old-endpoint', function () {
    return redirect('/new-endpoint', 308);
});
```

---

### Q4. What status code should you return for validation errors?

**Answer:**

**400 Bad Request** for syntax/validation errors in the request data.

**422 Unprocessable Entity** for semantic/business logic validation errors.

```php
// 400 Bad Request - malformed data
public function createUser(Request $request)
{
    $validator = Validator::make($request->all(), [
        'email' => 'required|email',
        'password' => 'required|min:8',
    ]);

    if ($validator->fails()) {
        return response()->json([
            'error' => 'Bad Request',
            'validation_errors' => $validator->errors()
        ], 400);
    }
}

// 422 Unprocessable Entity - business logic errors
public function transferMoney(Request $request)
{
    $fromAccount = Account::find($request->from_account_id);
    $toAccount = Account::find($request->to_account_id);

    if ($fromAccount->balance < $request->amount) {
        return response()->json([
            'error' => 'Unprocessable Entity',
            'message' => 'Insufficient funds'
        ], 422);
    }
}
```

---

### Q5. When should you use 202 Accepted vs 200 OK?

**Answer:**

**Use 202 Accepted when:**
- Request accepted but processing not complete
- Asynchronous operations
- Background job processing
- Long-running tasks

**Use 200 OK when:**
- Request completed successfully and synchronously
- Response data is immediately available

```php
// 202 Accepted - async processing
public function generateReport(Request $request)
{
    $job = dispatch(new GenerateReportJob($request->all()));

    return response()->json([
        'message' => 'Report generation started',
        'job_id' => $job->getJobId(),
        'status_url' => route('job.status', $job->getJobId())
    ], 202);
}

// 200 OK - immediate response
public function getUserProfile($id)
{
    $user = User::findOrFail($id);
    return response()->json($user, 200);
}
```

---

### Q6. How do you handle rate limiting with appropriate status codes?

**Answer:**

Use **429 Too Many Requests** with Retry-After header.

```php
class RateLimitMiddleware
{
    public function handle($request, Closure $next)
    {
        $key = 'requests:' . $request->ip();
        $requests = Cache::get($key, 0);

        if ($requests >= 100) { // 100 requests per minute
            $retryAfter = Cache::get($key . ':reset', 60);

            return response()->json([
                'error' => 'Too Many Requests',
                'message' => 'Rate limit exceeded',
                'retry_after_seconds' => $retryAfter
            ], 429)->header('Retry-After', $retryAfter);
        }

        Cache::increment($key);
        Cache::put($key . ':reset', 60, 60); // Reset in 60 seconds

        return $next($request);
    }
}
```

---

### Q7. What status code should be returned for partial content delivery?

**Answer:**

**206 Partial Content** for range requests and partial content delivery.

```php
public function streamVideo(Request $request, $filename)
{
    $filePath = storage_path('videos/' . $filename);
    $fileSize = filesize($filePath);

    $range = $request->header('Range');

    if ($range) {
        // Parse range header (e.g., "bytes=0-1023")
        $range = str_replace('bytes=', '', $range);
        list($start, $end) = explode('-', $range);

        $start = (int) $start;
        $end = $end ? (int) $end : $fileSize - 1;

        $length = $end - $start + 1;

        $file = fopen($filePath, 'rb');
        fseek($file, $start);

        return response()->stream(function () use ($file, $length) {
            echo fread($file, $length);
            fclose($file);
        }, 206)
        ->header('Content-Type', 'video/mp4')
        ->header('Content-Length', $length)
        ->header('Content-Range', "bytes $start-$end/$fileSize")
        ->header('Accept-Ranges', 'bytes');
    }

    return response()->file($filePath);
}
```

---

### Q8. Explain the difference between 410 Gone and 404 Not Found.

**Answer:**

| Aspect | 404 Not Found | 410 Gone |
|--------|---------------|----------|
| **Meaning** | Resource doesn't exist (temporary) | Resource no longer exists (permanent) |
| **Caching** | Can be cached | Should not be cached |
| **SEO Impact** | Negative for SEO | Tells search engines to remove from index |
| **Use Case** | Wrong URL, temporary unavailability | Permanently deleted content |

```php
// 404 - Resource might exist later
public function getPost($id)
{
    $post = Post::find($id);
    if (!$post) {
        return response()->json(['error' => 'Post not found'], 404);
    }
    return response()->json($post);
}

// 410 - Resource permanently removed
public function getDeletedPost($id)
{
    // Post was permanently deleted and won't return
    return response()->json([
        'error' => 'Gone',
        'message' => 'This post has been permanently deleted'
    ], 410);
}
```

---

### Q9. When should you use 503 Service Unavailable vs 500 Internal Server Error?

**Answer:**

| Aspect | 500 Internal Server Error | 503 Service Unavailable |
|--------|--------------------------|------------------------|
| **Cause** | Unexpected server error | Planned downtime/maintenance |
| **Retry** | May or may not work | Will work after maintenance |
| **Headers** | No special headers | Include Retry-After |
| **Logging** | Log for debugging | Don't log as error |

```php
// 500 - Unexpected error
public function getUsers()
{
    try {
        return response()->json(User::all());
    } catch (\Exception $e) {
        Log::error('Database error: ' . $e->getMessage());
        return response()->json(['error' => 'Internal server error'], 500);
    }
}

// 503 - Planned maintenance
Route::get('/api/*', function () {
    if (config('app.maintenance_mode')) {
        return response()->json([
            'error' => 'Service Unavailable',
            'message' => 'System is under maintenance'
        ], 503)->header('Retry-After', 3600);
    }
});
```

---

### Q10. How do you implement conditional requests with 304 Not Modified?

**Answer:**

Use **304 Not Modified** for caching based on ETags or Last-Modified headers.

```php
public function getPosts(Request $request)
{
    $posts = Post::all();
    $lastModified = $posts->max('updated_at');

    // Check If-Modified-Since header
    if ($request->header('If-Modified-Since') === $lastModified->toRfc7231String()) {
        return response()->noContent(304);
    }

    // Generate ETag based on content
    $etag = md5(serialize($posts));

    // Check If-None-Match header
    if ($request->header('If-None-Match') === $etag) {
        return response()->noContent(304);
    }

    return response()->json($posts)
        ->header('Last-Modified', $lastModified->toRfc7231String())
        ->header('ETag', $etag)
        ->header('Cache-Control', 'public, max-age=300');
}
```

---

## 📚 References

- RFC 7231: Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content
- RFC 7230: Hypertext Transfer Protocol (HTTP/1.1): Message Syntax and Routing
- RFC 7232: Hypertext Transfer Protocol (HTTP/1.1): Conditional Requests
- RFC 7233: Hypertext Transfer Protocol (HTTP/1.1): Range Requests
- RFC 7234: Hypertext Transfer Protocol (HTTP/1.1): Caching
- RFC 7235: Hypertext Transfer Protocol (HTTP/1.1): Authentication

---

## 🎯 Quick Reference Table

| Code | Name | Meaning |
|------|------|---------|
| 200 | OK | Success |
| 201 | Created | Resource created |
| 202 | Accepted | Request accepted for processing |
| 204 | No Content | Success, no content |
| 301 | Moved Permanently | Permanent redirect |
| 302 | Found | Temporary redirect |
| 304 | Not Modified | Resource not changed |
| 400 | Bad Request | Invalid request |
| 401 | Unauthorized | Authentication required |
| 403 | Forbidden | Access denied |
| 404 | Not Found | Resource not found |
| 405 | Method Not Allowed | HTTP method not allowed |
| 409 | Conflict | Request conflicts with current state |
| 410 | Gone | Resource permanently removed |
| 422 | Unprocessable Entity | Validation failed |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Server error |
| 502 | Bad Gateway | Gateway error |
| 503 | Service Unavailable | Service temporarily unavailable |
| 504 | Gateway Timeout | Gateway timeout |