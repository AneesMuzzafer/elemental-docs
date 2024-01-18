# Middleware

Middleware offers a convenient mechanism to examine and filter incoming HTTP requests to your application. For instance, you can develop middleware to validate the authentication status of your application's user. If the user is not authenticated, the middleware will redirect them to the login screen. Conversely, if the user is authenticated, the middleware will permit the request to advance deeper into the application.

You have the flexibility to create additional middleware to execute diverse tasks beyond authentication. As an illustration, a logging middleware could record all incoming requests to your application. These middleware components are housed within the `app/middlewares` directory.

### Creating Middleware

To create a new middleware, use the `build:middleware` Candle command:

```bash
php candle build:middleware IsAuthenticated
```

Executing this command will generate a fresh middleware class named "IsAuthenticated" in the `app/middlewares` directory. Within this class, a method named `handle` is created where you can articulate the logic for the middleware.

Here, we will only allow access to the route if the user is authenticated, otherwise, we will redirect the users back to the `login` URI:

```php
<?php

namespace App\Middlewares;

use App\Services\Auth;
use Closure;
use Core\Request\Request;

class IsAuthenticated
{
    public function handle(Request $request, Closure $next)
    {
        if (!(/* authentication logic */)) {
            return redirect("/login");
        }

        return $next($request);
    }
}
```

To pass the request deeper into the application, you should call the `$next` callback with the `$request`.

Consider middleware as a sequence of "layers" that HTTP requests traverse before reaching your application. Each layer has the capability to scrutinize the request and potentially reject it.

Of course, a middleware can perform tasks before or after passing the request deeper into the application. For example, this middleware would perform its task **after** the request is handled by the application:

```php
<?php

namespace App\Middlewares;

use Closure;
use Core\Request\Request;

class AfterMiddleware
{
    public function handle(Request $request, Closure $next)
    {
        $response = $next($request);

        // Middleware logic

        return $response;
    }
}
```

### Assigning Middleware to Routes

If you would like to assign middleware to specific routes, you may invoke the `middleware` method when defining the route:

```php
Route::get('/profile', function () {
    // ...
})->middleware(IsAuthenticated::class);
```

You may assign multiple middleware to the route by passing an array of middleware names to the `middleware` method:

```php
Route::get('/', function () {
    // ...
})->middleware([First::class, Second::class]);
```

### Assigning Middleware to Route Groups

You may assign middlewares to a route group by passing an array of middleware names to the attribute `middlewares` when defining the group:

```php
Route::group(["middleware" => [HasSession::class]], function () {
    Route::get("/", [StoryController::class, "index"]);
    Route::get("/story/{story}", [StoryController::class, "show"]);
});
```

You can use nested route groups to combine middlewares with their parent group. In the subsequent example, the "HasSession" middleware is applied to the `"/"` and `"/story/{story}"` routes, whereas "HasSession," "IsAuth," and "Log" middlewares get applied to the rest of the routes:

```php
Route::group(["middleware" => [HasSession::class]], function () {
    Route::get("/", [StoryController::class, "index"]);
    Route::get("/story/{story}", [StoryController::class, "show"]);

    Route::group(["middleware" => [IsAuth::class, Log::class]], function () {
        Route::get("/compose", [StoryController::class, "compose"]);
        Route::post("/compose", [StoryController::class, "create"]);
    });
});
```
