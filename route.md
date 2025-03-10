# Laravel Routing Guide

## Introduction
Laravel provides a powerful and flexible routing system that allows developers to define how incoming requests are handled. This guide covers basic routing concepts, HTTP methods, route parameters, named routes, route groups, middleware, and resource controllers.

## 1. Defining Basic Routes
Routes are defined in `routes/web.php` for web applications and `routes/api.php` for API endpoints.

### Example: Basic Route Returning a View
```php
use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    return view('welcome');
});
```

### Example: Route Returning a String
```php
Route::get('/hello', function () {
    return "Hello, Laravel!";
});
```

## 2. Available HTTP Methods
Laravel supports multiple HTTP request methods:

| Method  | Description |
|---------|------------|
| `Route::get()` | Retrieve data |
| `Route::post()` | Store new data |
| `Route::put()` | Update existing data (full update) |
| `Route::patch()` | Update existing data (partial update) |
| `Route::delete()` | Delete a resource |
| `Route::match()` | Match multiple request types |
| `Route::any()` | Match all request types |

### Example: Different HTTP Methods
```php
Route::post('/products', function () {
    return "Product saved!";
});

Route::put('/products/{id}', function ($id) {
    return "Product $id updated!";
});

Route::delete('/products/{id}', function ($id) {
    return "Product $id deleted!";
});
```

## 3. Route Parameters
Dynamic values can be passed in routes.

### Required Parameter
```php
Route::get('/products/{id}', function ($id) {
    return "Product ID: $id";
});
```

### Optional Parameter
```php
Route::get('/user/{name?}', function ($name = "Guest") {
    return "User: $name";
});
```

### Regular Expression Constraints
```php
Route::get('/order/{id}', function ($id) {
    return "Order ID: $id";
})->where('id', '[0-9]+');
```

## 4. Named Routes
Named routes allow easier referencing of routes.

### Defining a Named Route
```php
Route::get('/dashboard', function () {
    return "Welcome to Dashboard";
})->name('dashboard');
```

### Generating a URL for a Named Route
```php
$url = route('dashboard');
```

### Redirecting to a Named Route
```php
return redirect()->route('dashboard');
```

## 5. Route Groups
Grouping routes together allows applying common attributes like middleware, namespaces, or prefixes.

### Route Group with Prefix
```php
Route::prefix('admin')->group(function () {
    Route::get('/dashboard', function () {
        return "Admin Dashboard";
    });

    Route::get('/settings', function () {
        return "Admin Settings";
    });
});
```

## 6. Middleware
Middleware filters requests before they reach the controller.

### Applying Middleware to a Route
```php
Route::get('/profile', function () {
    return "User Profile";
})->middleware('auth');
```

### Applying Middleware to a Route Group
```php
Route::middleware(['auth', 'verified'])->group(function () {
    Route::get('/dashboard', function () {
        return "Secure Dashboard";
    });
});
```

## 7. Controller Routes
Instead of defining logic in routes, use controllers.

### Defining a Controller Route
```php
use App\Http\Controllers\ProductController;

Route::get('/products', [ProductController::class, 'index']);
```

### Resource Controller for CRUD Operations
```php
Route::resource('products', ProductController::class);
```
This automatically generates:
| Method | Route | Controller Method |
|--------|-------|------------------|
| GET | `/products` | `index()` |
| GET | `/products/create` | `create()` |
| POST | `/products` | `store()` |
| GET | `/products/{id}` | `show()` |
| GET | `/products/{id}/edit` | `edit()` |
| PUT/PATCH | `/products/{id}` | `update()` |
| DELETE | `/products/{id}` | `destroy()` |



