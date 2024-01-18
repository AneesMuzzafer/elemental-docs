# Routing

Routes are defined in the `app\routes.php` file, allowing developers to easily register various routes to handle different HTTP requests.

### Route Registration

Routes are registered by invoking the relevant method on the Route Facade, such as `Route::get()`, and involve specifying a URI pattern as the first argument. The second argument can either be a closure or an array that defines the controller and method responsible for handling the request.

For instance:

```php
<?php

use App\Controllers\AuthController;
use Core\Facade\Route;

Route::get("/settings", function () {
    // handling logic goes here
});

Route::post("/register", [AuthController::class, "register"]);
```

Whenever a request URI is matched, the corresponding closure or controller method is executed, and a response is generated and sent back to the browser.

#### Available Router Methods

You can register routes that respond to any HTTP verb using the following methods:
- `Route::get($uri, $callback);`
- `Route::post($uri, $callback);`
- `Route::put($uri, $callback);`
- `Route::patch($uri, $callback);`
- `Route::delete($uri, $callback);`
- `Route::options($uri, $callback);`

#### Route Parameters

Sometimes you will need to capture segments of the URI within your route. For example, you may need to capture a user's ID from the URL. You may do so by defining route parameters:

```php
Route::get("/user/{id}", function (string $id) {
    return "User " . $id;
});

Route::get("/story/{id}", function ($id) {/*...*/});
```

You may define as many route parameters as required by your route:

```php

Route::post("story/edit/{id}", [StoryController::class, "edit"]);

Route::get("story/{story_id}/comment/{comment_id}", [StoryController::class, "comment"]);
```

These will be passed into the controller method as well.

#### Dependency Injection

Elemental seamlessly handles the injection of necessary dependencies for your controller methods. This allows you to specify any dependencies required by your route in the callback signature using type-hinting. Elemental takes care of automatically resolving and injecting the declared dependencies into the callback.

For instance, if you type-hint `Core\Request\Request` within the callback, Elemental ensures that the current HTTP request is automatically injected into your route callback:

```php
<?php

use Core\Request\Request;

Route::get("/users", function (Request $request) {
    // ...
});
```

You can put the typed dependencies and route parameters in any order.

#### Route Model Binding

When you pass a model ID as a parameter to a route or controller action, the typical approach involves querying the database to fetch the corresponding model based on that ID. Elemental simplifies this process through route model binding, offering a convenient way to automatically inject model instances directly into your routes.

For instance, instead of injecting just the ID of a user into your route, you have the option to inject the entire User model instance that corresponds to the given ID.

In the context of routes or controller actions, models are defined using type-hinted variable names that match a specific segment in the route. For example:

```php
use App\Models\User;

Route::get("/users/{user}", function  (User  $user) {

return  $user->email;

});
```

#### Customizing the Key

Sometimes you may wish to resolve models using a column other than `id`. To do so, you may specify the column in the route parameter definition:

```php
use App\Models\User;

Route::get("/users/{user:email}", function  (User $user) {
    return  $user;
});
```
In this scenario, Elemental will seamlessly inject the model instance that possesses an email matching the corresponding value from the request URI.

Of course, route-model-binding also works with controller methods.

##### Model Not Found

If a matching model instance is not found in the database, a `ModelNotFoundException` will be thrown by the app. You can handle such exceptions and control the behavior of any such and other exceptions thrown by the app in the `ExceptionsHandler` class. More on that later.

#### Fallback Routes

Using the `Route::fallback` method, you may define a route that will be executed when no other route matches the incoming request.

```php
Route::fallback(function () {
    // ...
});
```

#### Route List

The `route:list` Candle command will provide the list of all the routes defined in the application:

```bash
php candle route:list
```
