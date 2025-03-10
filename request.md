# Laravel HTTP Requests – Reviewer Guide

## Introduction
In Laravel, HTTP requests represent the data coming into your application. The `Request` object provides methods to access and manipulate request data efficiently.

---

## Interacting With The Request

### Accessing the Request
Laravel automatically injects the `Illuminate\Http\Request` instance into controller methods:

```php
use Illuminate\Http\Request;

class UserController extends Controller
{
    public function store(Request $request)
    {
        return $request->all();
    }
}
```
You can retrieve request data using:

```php
$request->input('name');  // Get a specific input  
$request->all();          // Get all request data  
```

---

## Request Path, Host, and Method

- **Path:** `$request->path();` – Returns the request URI (e.g., `products/create`).  
- **Full URL:** `$request->fullUrl();` – Returns the full URL with query parameters.  
- **Method:** `$request->method();` – Returns the request method (`GET`, `POST`, etc.).  
- **Checking Method:** `$request->isMethod('post');` – Checks if the request method is POST.  

---

## Request Headers

Retrieve headers using:

```php
$header = $request->header('User-Agent');
$hasHeader = $request->hasHeader('Authorization'); // Check if exists
```

---

## Request IP Address

Get the client’s IP address:

```php
$ip = $request->ip();
```

---

## Content Negotiation

- **Check the request’s content type:**  

```php
$request->expectsJson();  // Returns true if the request expects JSON
```

---

## PSR-7 Requests

Laravel supports PSR-7 request handling using `symfony/psr-http-message-bridge`.  

To convert a request to a PSR-7 request:

```php
use Nyholm\Psr7\Factory\Psr17Factory;
use Symfony\Bridge\PsrHttpMessage\Factory\PsrHttpFactory;

$psr17Factory = new Psr17Factory();
$psrHttpFactory = new PsrHttpFactory($psr17Factory, $psr17Factory, $psr17Factory, $psr17Factory);

$psrRequest = $psrHttpFactory->createRequest($request);
```

---

## Input

### Retrieving Input

- **Get a single input:** `$request->input('name');`  
- **Default value if input is missing:** `$request->input('name', 'Guest');`  
- **Get multiple inputs:** `$request->only(['name', 'email']);`  
- **Get all input:** `$request->all();`  

### Input Presence

Check if an input field exists:

```php
if ($request->has('name')) {
    // Do something
}
```

---

## Merging Additional Input

You can dynamically add input data to the request:

```php
$request->merge(['role' => 'admin']);
```

---

## Old Input

Old input is used for repopulating forms after validation failures:

```php
<input type="text" name="name" value="{{ old('name') }}">
```

Store old input in the session:

```php
$request->flash();
```

---

## Cookies

Laravel simplifies cookie handling:

```php
$cookie = $request->cookie('user_id');
```

Set cookies:

```php
$response->cookie('user_id', '123', 60);
```

---

## Input Trimming and Normalization

Laravel automatically trims input values to prevent unnecessary spaces and anomalies.

---

## Files

### Retrieving Uploaded Files

```php
$file = $request->file('avatar');
$fileName = $file->getClientOriginalName();
$extension = $file->extension();
```

### Storing Uploaded Files

```php
$path = $request->file('avatar')->store('avatars');
```

For public storage:

```php
$path = $request->file('avatar')->store('avatars', 'public');
```

---

## Configuring Trusted Proxies

If your app is behind a proxy, you may configure trusted proxies in **app/Http/Middleware/TrustProxies.php**:

```php
protected $proxies = '*';
protected $headers = Request::HEADER_X_FORWARDED_ALL;
```

---

## Configuring Trusted Hosts

To restrict allowed hostnames, configure them in **app/Http/Middleware/TrustHosts.php**:

```php
protected function hosts()
{
    return [
        'example.com',
        '*.example.com',
    ];
}
```

---

This guide provides an easy-to-understand reference for Laravel HTTP requests. 🚀 Let me know if you need modifications!

