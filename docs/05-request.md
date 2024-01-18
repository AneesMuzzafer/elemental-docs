# Request

The `Core\Request\Request` class in Elemental offers an object-oriented approach for engaging with the present HTTP request managed by your application. It facilitates the retrieval of input, cookies, and files submitted along with the request.

#### Accessing the Request

To acquire the current HTTP request instance through dependency injection, you can utilize type-hinting for the `Core\Request\Request` class in your route closure or controller method. The service container will automatically inject the incoming request instance.
```php
<?php

namespace App\Controllers;

use App\Models\Category;
use Core\Request\Request;

class CategoryController
{
    public function store(Request $request)
    {
        $name = $request->data()["name"];
        $category = Category::where(["name" => $name]);

        if ($category) {
            return view("Category", ["categories" => Category::all(), "msg" => "Category already exists!"])->withLayout("layouts.DashboardLayout");
        }

        Category::create($request->data());

        return redirect("/category");
    }
}
```
The service container will automatically inject the incoming request into the route closure as well.

#### Dependency Injection and Route Parameters

If your controller method anticipates input from a route parameter, you have the flexibility to list your arguments in any order. For instance, consider the following route definition:

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
        // Update the story.

        return redirect("/story/$story->id");
    }
}
```

### Request Data

You may obtain all of the incoming request's input data as an `array` using the `data()` method. This method may be used regardless of whether the incoming request is from an HTML form or is an XHR request:

```php
$data =  $request->data();
```
#### Retrieving an Input Value
You may access all of the user input from your `Request` instance without worrying about which HTTP verb was used for the request. Regardless of the HTTP verb, the `data` method may be used to retrieve user input:

```php
$name=  $request->data()["name"];
```

### Request URI, Method and Headers

The `Core\Request\Request` instance provides a variety of methods for examining the incoming HTTP request. Let's discuss a few of the most important methods below.

 #### Request Headers
You may retrieve the request headers from the `Core\Request\Request` instance using the `headers` method.
```php
$headers = $request->headers();
```
 #### Request Method
You may retrieve the request method  by calling `method` on `Core\Request\Request` instance.
```php
$method = $request->method();
```
 #### Request URI
You may retrieve the request uri from the `Core\Request\Request` instance using the `uri` method.
```php
$uri = $request->uri();
```
 #### Request Cookies
You may retrieve the request cookies from the `Core\Request\Request` instance using the `cookies` method.
```php
$cookies = $request->cookies();
```

 #### Raw Content
You may retrieve the raw content from the `Core\Request\Request` instance using the `rawContent` method.
```php
$content = $request->rawContent();
```

Be careful when dealing with the raw content of a request.

 #### Files
You may retrieve the files from the `Core\Request\Request` instance using the `files` method.
```php
$files = $request->files();
```
 #### Request IP Address
The `ip` method may be used to retrieve the IP address of the client that made the request to your application:
```php
$ipAddress = $request->ip();
```

 #### Port
The `port` method may be used to retrieve the Port of the client that made the request to your application:
```php
$port = $request->port();
```

 #### Content Type
You may retrieve the content-type from the `Core\Request\Request` instance using the `contentType` method.
```php
$contentType = $request->contentType();
```

 #### Query String
You may retrieve the query string of the request using the `queryString` method.
```php
$query = $request->queryString();
```
 #### Specific Content Types

 ##### Text
You may retrieve the text content of the request using the `text` method provided the content-type is set to `text/plain`
```php
$text = $request->text();
```

 ##### Javascript
You may retrieve the JS content of the request using the `js` method provided the content-type is set to `application/javascript`
```php
$js = $request->js();
```
 ##### HTML
You may retrieve the HTML content of the request using the `html` method provided the content-type is set to `text/html`
```php
$js = $request->html();
```

 ##### JSON
You may retrieve the JSON content of the request using the `json` method provided the content-type is set to `application/json`
The `$request->data()` returns all the JSON data passed to the request. However,
```php
$jsonData = $request->json();
```

The `$request->data()` contains all the JSON data along with the inputs passed through the query params in the request. However, `$request->json()` can be used to retrieve only the JSON content.

 ##### XML
You may retrieve the XML content of the request using the `xml` method provided the content-type is set to `application/json`
```php
$xmlData = $request->xml();
```
