# Exception Handler

Elemental provides a convenient way to handle all the exceptions thrown by the app.

The `handle` method of `App\Exceptions\Handler` class is where all exceptions thrown by your application pass through before being rendered to the user. By default, exceptions thrown by the app will be formatted, and a structured response will be sent back to the browser. However, inside the handle method, you can intercept any exception and perform custom logic before the response is sent back.

You can even send back a custom view or a response.

### Handler Class

```php
<?php

namespace App\Exceptions;

use Core\Exception\ExceptionHandler;

class Handler extends ExceptionHandler
{
    public function handle($e)
    {
        // Perform some processing here

        // You can customize the handling of exceptions based on your requirements
    }
}
```

#### Handling Specific Exceptions

Elemental has defined some specific exception classes by default:

- `AppException`
- `ModelNotFoundException`
- `RouteNotFoundException`
- `RouterException`
- `ViewNotFoundException`

If you need to handle different types of exceptions in different ways, you can modify the `handle` method accordingly:

```php
<?php

class Handler extends ExceptionHandler
{
    public function handle($e)
    {
        if ($e instanceof ModelNotFoundException || $e instanceof RouteNotFoundException) {
            return view("404")->withLayout("layouts.DashboardLayout");
        }

        if ($e instanceof ViewNotFoundException) {
            return view("Home");
        }

        // Handle other specific exceptions as needed
    }
}
```

You are free to create your own exception classes by extending from the base `Exception` class, which can then be handled as required.

Feel free to customize the `handle` method based on your application's specific needs.
