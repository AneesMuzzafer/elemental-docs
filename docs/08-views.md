# Views, Layouts, and Components

### Views

In Elemental PHP framework, it is not practical to return entire HTML document strings directly from routes and controllers. Views provide a convenient way to place all HTML in separate files.

Views play a crucial role in separating controller/application logic from presentation concerns and are stored in the `app/views` directory. These view files, written in PHP, encapsulate the markup. Consider a basic example of a view:

```php
<html>
<body>
  <h1>Hello, <?= $name ?></h1>
</body>
</html>
```

If this view is stored at `app/views/Welcome.php`, it can be returned using the global `view` helper in a route:

```php
Route::get('/', function () {
  return view('Welcome', ['name' => 'Ahmed']);
});
```

The first argument passed to the `view` helper corresponds to the name of the view file in the `resources/views` directory. The second argument can be an array of key-value pairs passed to the view. For example, in the above code, `$name` will be directly accessible and contain the value 'Ahmed'.

Views can also be returned using the static method `make` on the `Core\View\View` class:

```php
Route::get('/', function () {
  return View::make("Post", $params);
});
```

#### Nested View Directories

Views may be nested within subdirectories of the `app/views` directory. "Dot" notation may be used to reference nested views. For example, if your view is stored at `app/views/layouts/MainLayout.php`, you may return it from a route/controller like so:

```php
return view('layouts.MainLayout', $data);
```

### Layouts

Elemental provides a convenient way to maintain the same layout across multiple views, reducing code duplication. A layout is itself a view file containing a placeholder `{{ content }}`. When a view is returned with the layout, the final view is compiled by putting the view inside the layout's content.


Elemental provides a convenient way to maintain the same layout across multiple views, reducing code duplication. A layout is a view file that incorporates a designated placeholder, denoted by `{{ content }}`. When a view is returned using a specific layout, the composition is achieved by embedding the content of the view within the designated placeholder in the layout. This approach streamlines the organization of views and enhances code maintainability by centralizing common layout elements.

Below is a basic example:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!-- Head content -->
</head>
<body>
  <?= component("components.Navbar") ?>
  <div style="min-height: calc(100vh - 140px);">
    {{ content }}
  </div>
</body>
</html>
```

A view can be returned with a layout like this:

```php
public function compose()
{
  return view("Compose")->withLayout("layouts.DashboardLayout");
}
```

### Components

Elemental offers a powerful approach to crafting views. Every view is essentially a component, and any view can be assembled from other components. It's a symphony of composition, where each piece contributes to the creation of a harmonious and dynamic whole.

Example component file (`views/components/Logo.php`):

```php
<a class="logo" href="/">
  <span class="logo-img">
    <img src="logo.png" class="logo-text">
    LOGO
  </span>
</a>
```

This component can be used inside any other view file. For example, in `views/Login.php`:

```php
<div>
  <?= component("components.Logo") ?>
  <p>Welcome Back!</p>
  <!-- Other login form elements -->
</div>
```

Thus, Elemental empowers you with both layout and component constructs, allowing you to compose your views with the finesse of both top-down and bottom-up approaches. This flexibility enables a seamless fusion, where you can effortlessly mix and combine elements to craft an elegant and sophisticated user interface for your application.
