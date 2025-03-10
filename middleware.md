# Laravel Middleware

## Introduction
Middleware in Laravel provides a mechanism for filtering HTTP requests entering your application. It acts as a bridge between a request and a response, allowing you to execute code before or after the request is handled by the application.

---

## Defining Middleware
Middleware in Laravel is defined as a class inside the `app/Http/Middleware` directory. You can create a middleware using the following Artisan command:

```sh
php artisan make:middleware CheckUserRole
```

This command generates a middleware file in `app/Http/Middleware/CheckUserRole.php`.

Inside the middleware, you can define logic in the `handle` method to modify the request or response:

```php
public function handle(Request $request, Closure $next)
{
    if (!$request->user() || $request->user()->role !== 'admin') {
        return redirect('/');
    }
    return $next($request);
}
```

---

## Registering Middleware
Once a middleware is created, it must be registered in `app/Http/Kernel.php`. Middleware can be registered in three ways:

1. **Global Middleware**
2. **Route Middleware**
3. **Middleware Groups**

---

## Global Middleware
Global middleware is applied to all incoming HTTP requests. To register a middleware globally, add it to the `$middleware` array in `Kernel.php`:

```php
protected $middleware = [
    \App\Http\Middleware\CheckMaintenanceMode::class,
    \App\Http\Middleware\ValidatePostSize::class,
];
```

---

## Assigning Middleware to Routes
Middleware can be assigned to specific routes using the `middleware` method in the route definition:

```php
Route::get('/dashboard', function () {
    return view('dashboard');
})->middleware('auth');
```

Alternatively, when using controllers:

```php
class DashboardController extends Controller
{
    public function __construct()
    {
        $this->middleware('auth');
    }
}
```

---

## Middleware Groups
Middleware groups allow multiple middleware to be assigned to a set of routes. These are defined in `Kernel.php`:

```php
protected $middlewareGroups = [
    'web' => [
        \App\Http\Middleware\EncryptCookies::class,
        \Illuminate\Session\Middleware\StartSession::class,
    ],
    'api' => [
        \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
        'throttle:api',
    ],
];
```

Routes can then use these groups:

```php
Route::middleware(['web'])->group(function () {
    Route::get('/', function () {
        return view('welcome');
    });
});
```

---

## Middleware Aliases
Instead of using full class names, Laravel allows defining middleware aliases in `Kernel.php`:

```php
protected $routeMiddleware = [
    'auth' => \App\Http\Middleware\Authenticate::class,
    'role' => \App\Http\Middleware\CheckUserRole::class,
];
```

This allows routes to reference the middleware by its alias:

```php
Route::get('/admin', function () {
    return view('admin.dashboard');
})->middleware('role');
```

---

## Sorting Middleware
Laravel executes middleware in the order they are listed in `Kernel.php`. If multiple middleware are applied to a route, they will run in sequence.

For example, if you apply `auth` and `role` middleware:

```php
Route::get('/admin', function () {
    return view('admin.dashboard');
})->middleware(['auth', 'role']);
```

- First, `auth` will check if the user is logged in.
- Then, `role` will check if the user has admin privileges.

This ensures proper security handling.

---

## Middleware Parameters
Middleware can accept parameters, which can be passed in the route definition.

Example of a middleware that accepts a role parameter:

```php
public function handle(Request $request, Closure $next, $role)
{
    if (!$request->user() || $request->user()->role !== $role) {
        return redirect('/');
    }
    return $next($request);
}
```

To use it in a route:

```php
Route::get('/admin', function () {
    return view('admin.dashboard');
})->middleware('role:admin');
```

---

## Terminable Middleware
Terminable middleware allows performing actions after the response has been sent to the browser. This is useful for logging, auditing, or cleanup tasks.

Example:

```php
public function terminate($request, $response)
{
    Log::info('Response sent to user:', ['user' => $request->user()]);
}
```

To register a terminable middleware, add it to `Kernel.php` like normal.

---

## Summary
- Middleware filters requests before or after they reach the application.
- It can be **global**, **route-specific**, or grouped into **middleware groups**.
- Middleware can accept **parameters** and be **terminable** for post-response actions.
- Laravel **executes middleware in order**, ensuring a structured request handling process.

This covers the essential topics of Laravel middleware. Let me know if you need more explanations or examples! 🚀
