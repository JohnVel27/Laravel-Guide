# Laravel Controllers – Reviewer Guide

## Introduction
Controllers in Laravel handle HTTP requests and logic, making the application more organized and reusable. Instead of writing all logic inside routes, controllers keep things structured.

## Writing Controllers
To create a controller, use Artisan:
```sh
php artisan make:controller ProductController
```
This generates `app/Http/Controllers/ProductController.php`, where you can define methods to handle requests.

## Basic Controllers
A basic controller looks like this:
```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class ProductController extends Controller
{
    public function index()
    {
        return "List of Products";
    }
}
```
You can then define a route to call this method:
```php
Route::get('/products', [ProductController::class, 'index']);
```

## Single Action Controllers
If a controller has only one action, use `__invoke()`:
```php
class SingleActionController extends Controller
{
    public function __invoke()
    {
        return "This is a single action controller!";
    }
}
```
Register it in routes like this:
```php
Route::get('/single-action', SingleActionController::class);
```

## Controller Middleware
Middleware can be applied to controllers to filter requests.
Example: Protecting routes for authenticated users only:
```php
class ProductController extends Controller
{
    public function __construct()
    {
        $this->middleware('auth');
    }
}
```
Now, all methods inside this controller require authentication.

## Resource Controllers
Instead of manually creating methods, Laravel provides a resource controller to handle CRUD operations:
```sh
php artisan make:controller ProductController --resource
```
This generates a controller with methods like `index`, `store`, `show`, `update`, and `destroy`.

Register it in routes:
```php
Route::resource('products', ProductController::class);
```

## Partial Resource Routes
If you don’t need all resource routes, use `only` or `except`:
```php
Route::resource('products', ProductController::class)->only(['index', 'show']);
Route::resource('products', ProductController::class)->except(['destroy']);
```

## Nested Resources
For related resources, use nested routes. Example: Comments belonging to a Post:
```php
Route::resource('posts.comments', CommentController::class);
```
This creates routes like:
```sh
GET /posts/{post}/comments
POST /posts/{post}/comments
```

## Naming Resource Routes
By default, resource routes have names like `products.index`. You can customize them:
```php
Route::resource('products', ProductController::class)->names([
    'index' => 'products.list',
    'show' => 'products.detail',
]);
```

## Naming Resource Route Parameters
To change the parameter name (`id` to `product_id`):
```php
Route::resource('products', ProductController::class)->parameters([
    'products' => 'product_id'
]);
```
Now the route looks like:
```sh
/products/{product_id}
```

## Scoping Resource Routes
If a model is bound to a route, Laravel automatically finds the corresponding record. Example:
```php
Route::resource('users.posts', PostController::class)->scoped([
    'post' => 'slug'
]);
```
Now, `/users/{user}/posts/{post}` will find a post by `slug` instead of `id`.

## Localizing Resource URIs
To change resource URLs to a different language, configure the localization in routes:
```php
Route::resource('productos', ProductController::class)->names('productos');
```
Now, instead of `/products`, the URL will be `/productos`.

## Supplementing Resource Controllers
If you need additional actions, define them manually:
```php
Route::get('/products/discounted', [ProductController::class, 'discounted']);
```

## Singleton Resource Controllers
If a resource should only have one instance per user (like a profile), use singleton:
```php
Route::singleton('profile', ProfileController::class);
```
This generates routes like:
```sh
GET /profile
PUT /profile
DELETE /profile
```
without `index` or `create`.

## Dependency Injection and Controllers
Controllers can accept dependencies in their methods, and Laravel automatically resolves them. Example:
```php
class ProductController extends Controller
{
    public function show(Product $product)
    {
        return $product;
    }
}
```
Laravel will automatically fetch the product from the database based on the route parameter.

