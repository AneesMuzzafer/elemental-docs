# Response

Every route and controller is expected to produce a response for delivery to the user's browser. Elemental offers various methods for generating responses. The simplest form of response involves returning a string directly from a route or controller. The framework will seamlessly convert this string into a complete HTTP response.
```php
Route::get('/', function  () {
	return  'Hello World';
});
```

In addition to returning strings from your routes and controllers, you may also return arrays or objects. The framework will automatically convert them into a JSON response:

```php
Route::get('/', function  () {
	return [1, 2, 3];
});
```
#### Response Objects

Usually, you won't merely return straightforward strings or arrays from your route actions. Instead, you'll often return complete instances of `Core\Response\Response` or views.

Returning a full `Response` instance allows you to customize the response's HTTP status code and headers. You can inject the Response instance by type-hinting the Response instance inside your controller or route closure.

```php

use Core\Response\Response;

Route::get('/home', function(Response $response) {
	$response->setHeader("content-type", "text/plain")
			->setStatusCode(200)
			->setContent("Hello World");

	return  response;
});
```
You can ofcourse return a `view` from a controller. However,  If you need control over the response's status and headers but also need to return a `view` as the response's content, you can do that as following:

```php

use Core\Response\Response;

class UserController {
	public function register(Response $response){
		$response->setHeader("x-is_register", "true");
		return view("Register");
	}
}
```

This will automatically set the header on the view response that will be sent to the browser.

#### Response Content
Keep in mind that most response methods are chainable, allowing for the fluent construction of response instances.

You may set the content of the response by using `setContent` method on the response instance.
```php
	$response->setContent("...");
```

However, if you want to append to the content of the response, you can do so by using `appendContent` method on the response instance.
```php
	$response->appendContent("...");
```

#### Response Header

You may set a header on the response instance by using `setHeader` method
```php
	$response->setHeader("content-type", "text/plain");
```
However, if you want to set several headers simultaneously, you can do so by using `setHeaders` method and passing an array of headers.
```php
	$response->setHeaders(["content-type" => "text/html", ...]);
```
#### Response Status Code
You may directly set the status code of the response by using `setHeader` method on the response instance.
```php
	$response->setStatusCode(301);
```
A status text will be set by default for the common status codes.

### Redirects
You can generate a redirect response that contains the proper headers needed to redirect the user to another URL by invoking static method `redirect` on the `Core\Response\Response` class.
```php
use Core\Response\Response;

Route::get('/dashboard', function  () {
	return Response::redirect('home/dashboard');
});
```
However, for simplicity a helper method `redirect()` is also available globally to achieve the same functionality.
```php
use Core\Response\Response;

Route::post('/story/create', function  () {
	if(!some condition)
		return redirect('/story', 204);
});
```

#### JSON Response

You can also generate a JSON response by calling the static method `JSON` on the `Core\Response\Response` class. The data passed to the method will be converted to proper JSON. You can also optionally pass the status code and headers array as the second and third argument to the function.

```php
use Core\Response\Response;

Route::post('/post', function  () {
	$post = (...);
	return Response::JSON($post, 201, ["header"=>"value"]);
});
```
