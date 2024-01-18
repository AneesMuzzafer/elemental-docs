# Controllers

Rather than consolidating all request handling logic within closures in your route files, consider structuring this behavior through "controller" classes. Controllers allow you to organize related request handling logic into a cohesive class. For instance, a `UserController` class could manage various incoming requests related to users, such as displaying, creating, updating, and deleting users. These controller classes are conventionally stored in the `app/Controllers` directory.

### Basic Controllers

To generate a new controller, you may run the `build:controller` Candle command.

```bash
php candle build:controller UserController
```

This will generate a new file named "UserController.php" inside the `app/Controllers` directory.

A controller may have any number of public methods which will respond to incoming HTTP requests:

```php
<?php
use App\Services\Auth;

namespace App\Controllers;

class AuthController
{
    public function showRegister()
    {
        return view("Register")->withLayout("layouts.DashboardLayout");
    }

    public function logout()
    {
        Auth::logout();
        redirect("/");
    }
}
```

After creating a controller class and its methods, you can define a route to the controller method as follows:

```php
use App\Controllers\UserController;

Route::get("/register", [AuthController::class, "showRegister"]);
```

When a received request matches with the designated route URI, the `showRegister` method within the `App\Controllers\UserController` class will be called, and the method will receive the corresponding route parameters.

### Dependency Injection and Controllers

#### Constructor Injection

The Elemental service container is responsible for resolve instances of all controllers. Consequently, you can use type-hinting in the constructor of your controller to specify any dependencies it may require. The stated dependencies will be automatically resolved and injected into the controller instance

```php
<?php

namespace App\Controllers;

use Core\Database\Database;

class UserController
{
    /**
    * Create a new controller instance.
    */
    public function __construct(
        public Database $db,
    ) {}
}
```

#### Method Injection

Apart from injecting dependencies through the constructor, you can also use type-hinting for dependencies in your controller's methods. A common use-case for method injection is injecting the `Core\Request\Request` or any service instance into your controller methods:

Create and manage controllers to handle requests effectively.

```php
<?php

namespace App\Controllers;

use Core\Request\Request;
use App\Services\Auth;

class StoryController
{
    public function create(Request $request)
    {
        $data = $request->data();
        $user = Auth::user();
        $story = Story::create([...]);

        return redirect("/story/$story->id");
    }
}
```

f your controller method anticipates input from a route parameter, you have the flexibility to list your arguments in any order. For instance, consider the following route definition:

```php
Route::post("story/update/{id}", [StoryController::class, "update"]);
```

You may still type-hint the `Core\Request\Request` and access your `id` parameter by defining your controller method as follows:

```php
<?php

namespace App\Controllers;
use Core\Request\Request;

class StoryController
{
    public function update(string $id, Request $request)
    {
        // Update the story...

        return redirect("/story/$story->id");
    }
}
```
