# CSRF Protection in Laravel

## Introduction
Cross-Site Request Forgery (CSRF) is an attack where a malicious website tricks users into performing unintended actions on a trusted website. Laravel provides built-in CSRF protection by requiring a CSRF token for each request.

## Preventing CSRF Requests
Laravel includes CSRF protection in the `web` middleware group. It generates a CSRF token for every user session and verifies it with each request.

### Example Form with CSRF Token
```blade
<form method="POST" action="/submit">
    @csrf
    <input type="text" name="name" placeholder="Enter your name">
    <button type="submit">Submit</button>
</form>
```

## Excluding URIs from CSRF Protection
Sometimes, certain routes (e.g., APIs, webhooks) need to be excluded from CSRF protection.

### Steps to exclude routes:
- Open `app/Http/Middleware/VerifyCsrfToken.php`
- Add the URIs to the `$except` array.

```php
namespace App\Http\Middleware;

use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as Middleware;

class VerifyCsrfToken extends Middleware
{
    protected $except = [
        'webhook/*',  // Excludes all URIs under 'webhook/'
        'api/payment', // Excludes 'api/payment'
    ];
}
```

## X-CSRF-Token Header
For AJAX requests, Laravel provides the `X-CSRF-Token` header, which must be included in requests.

### Example AJAX Request with CSRF Token
```js
fetch('/submit', {
    method: 'POST',
    headers: {
        'X-CSRF-Token': document.querySelector('meta[name="csrf-token"]').getAttribute('content'),
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({ name: "John Doe" })
});
```

## X-XSRF-Token
Laravel automatically stores the CSRF token in an `XSRF-TOKEN` cookie, which can be used for JavaScript frameworks like Vue.js and Axios.

### Example Usage in Axios
```js
axios.defaults.headers.common['X-XSRF-TOKEN'] = document.cookie.split('; ')
    .find(row => row.startsWith('XSRF-TOKEN='))
    ?.split('=')[1];
```

## Key Takeaways
✅ Laravel automatically includes CSRF protection in forms.  
✅ CSRF tokens are required for POST, PUT, PATCH, and DELETE requests.  
✅ You can exclude certain URIs from CSRF protection in `VerifyCsrfToken.php`.  
✅ AJAX requests should send the `X-CSRF-Token` header.  
✅ Laravel stores the CSRF token in a cookie (`XSRF-TOKEN`) for JavaScript frameworks.  



