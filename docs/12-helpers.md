# Helpers

Elemental includes a variety of global helper functions. You can use these functions in any way that is convenient to you.

#### app()
The `app` function returns the `Application` instance:

```php
$app = app();
```
This is pretty useful when you want to register your own services as well as resolve any framework or custom service.

```php
app()->bind(CustomService::class, function () {
	return new CustomService(new anotherService());
});

$service = app()-make(CustomService::class);
```

#### dump()
The `dump` function dumps the variable passed as the first argument. You can also pass an additional second argument that can serve as the identifier on screen:
```php
dump($value);
dump($user, "user");
```

#### dd()
The `dd` function dumps the given variable and ends the execution of the script:
```php
dd($value);
dd($user, "user");
```

#### console_log()
The `console_log` function serves as a unique tool for logging variables, distinct from the `dump` function. Notably, it doesn't return output to the browser; instead, it directs information to the console initiated by the script. You can pass any variable number of arguments to the `console_log` function.

```php
console_log($value);
console_log($user, $post, $image, $comment);
```

#### router()
The `router` function returns the returns the `Router` instance.

#### view()
The `view` function is used to return a view from the controller method:
```php
return view("Login");
```

#### component()
The `component` function is used to return a view as a component to be used inside another view:
```php
<body>
	<?= component("Logo")?>
	//...
</body>
```

#### redirect()
The `redirect` function returns a redirect HTTP response and is used to redirect to any other route.
```php
return redirect("/home");
```

#### abort()
The `abort` function throws an Exception which returns an error page with the set code and status.
```php
abort(400, "Bad Request");
```

#### abort_if()
The `abort_if` function throws an Exception which returns an error page with the set status code and message if the passed condition is `true`.
```php
abort_if($conditionBad, 400, "Bad Request");
```

#### abort_unless()
The `abort_unless` function throws an Exception which returns an error page with the set status code and message if the passed condition is `false`.
```php
abort_unless($conditionGood, 400, "Bad Request");
```
