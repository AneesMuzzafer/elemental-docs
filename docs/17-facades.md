# Facades

Elemental introduces a Facade system inspired by Laravel, providing a convenient and expressive static interface to classes within the application's Dependency Injection (DI) container. Facades act as static proxies to classes in the service container, offering a balance between a concise syntax and the testability and flexibility of traditional static methods.

In Elemental, the `Core\Facade\Route` serves as a Facade, offering a static interface to the application's Router instance enabling you to use it like this in the `routes.php` file:

```php
// routes.php

<?php
use Core\Facade\Route;

Route::get("/register", [AuthController::class, "showRegister"]);
Route::get("/login", [AuthController::class, "showLogin"]);
Route::get("/logout", [AuthController::class, "logout"]);
Route::post("/register", [AuthController::class, "register"]);
```

### Creating Your Own Facade

To create a custom Facade for any class, follow these steps:

1. Create a `FacadeClass` that extends the `Core\Facade\Facade` class.
2. Inside this class, implement a static method named `getFacadeAccessor`, returning the class string for the associated instance in the DI container.

Here's an example of creating a `PaymentGateway` Facade:

```php
<?php

use Core\Facade\Facade;
use Core\Services\PaymentGateway;

class PaymentGatewayFacade extends Facade
{
    protected static function getFacadeAccessor()
    {
        return PaymentGateway::class;
    }
}
```

Now, you can access the instance methods of your custom class by calling static methods on the corresponding `FacadeClass`.
