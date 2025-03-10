# Laravel HTTP Responses

## Creating Responses

In Laravel, responses are returned from routes or controllers using the `response()` helper:

```php
Route::get('/example', function () {
    return response('Hello, Laravel!');
});
```
This will return a simple HTTP response with a **200 OK** status.

To return a response with a specific status code:
```php
return response('Unauthorized', 401);
```

## Attaching Headers to Responses

Headers provide metadata about the response. You can attach them using the `header()` method:

```php
return response('Hello')->header('Content-Type', 'text/plain');
```

For multiple headers:
```php
return response('Hello')
    ->header('Content-Type', 'text/plain')
    ->header('X-Custom-Header', 'MyHeaderValue');
```

## Attaching Cookies to Responses

Cookies can be attached to responses using the `cookie()` method:

```php
return response('Welcome')->cookie('user_id', '12345', 60);
```
Here, `'user_id'` is set to `'12345'` and will expire in **60 minutes**.

## Cookies and Encryption

Laravel encrypts cookies by default. To disable encryption for certain cookies, modify `app/Http/Middleware/EncryptCookies.php`.

To create an **unencrypted cookie**:
```php
use Illuminate\Support\Facades\Cookie;

$cookie = Cookie::make('theme', 'dark', 60);
return response('Theme set')->cookie($cookie);
```

## Redirects

Laravel provides helper functions for **redirects**.

### Redirecting to Named Routes

```php
return redirect()->route('dashboard');
```
This redirects to the route named `'dashboard'`.

### Redirecting to Controller Actions

```php
return redirect()->action([HomeController::class, 'index']);
```
Redirects to the `index()` method inside `HomeController`.

### Redirecting to External Domains

```php
return redirect()->away('https://example.com');
```
Redirects the user to an **external website**.

### Redirecting With Flashed Session Data

Flash session data stores temporary data for the next request.

```php
return redirect()->route('dashboard')->with('success', 'Profile updated!');
```
On the redirected page, you can retrieve it using:
```blade
@if(session('success'))
    <p>{{ session('success') }}</p>
@endif
```

## Other Response Types

### View Responses

Returning a **Blade view**:
```php
return response()->view('welcome', ['name' => 'John']);
```

### JSON Responses

APIs commonly use JSON responses:
```php
return response()->json(['message' => 'Success', 'status' => 200]);
```

Returning JSON with a specific status:
```php
return response()->json(['error' => 'Unauthorized'], 401);
```

### File Downloads

To force a file **download**:
```php
return response()->download(public_path('files/sample.pdf'));
```

To provide a **custom filename**:
```php
return response()->download(public_path('files/sample.pdf'), 'NewName.pdf');
```

### File Responses

If you just want to **display** the file instead of downloading:
```php
return response()->file(public_path('files/sample.pdf'));
```

### Streamed Responses

Useful for large files or real-time content:

```php
return response()->stream(function () {
    echo "Streaming data...\n";
    sleep(1);
    echo "More data...\n";
}, 200, ['Content-Type' => 'text/plain']);
```

### Response Macros

**Macros** allow reusable response logic:

```php
use Illuminate\Http\Response;

Response::macro('success', function ($message) {
    return response()->json(['status' => 'success', 'message' => $message]);
});
```

Now, you can use:
```php
return response()->success('Operation completed.');
```

---

This guide covers Laravel HTTP Responses in detail. Let me know if you need modifications! 🚀

