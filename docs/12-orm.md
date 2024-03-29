# Object Relational Mapper (Model)

Elemental includes a custom-made object-relational mapper (ORM) that makes it enjoyable to interact with the database. When using the ORM, each database table has a corresponding "Model" that is used to interact with that table. In addition to retrieving records from the database table, models allow you to insert, update, and delete records from the table as well.

### Creating Models

Models are present in the `app/models` directory and extend the `Core\Model\Model` class. You can generate a new model by using the `build:model` Candle command.

```bash
php candle build:model Post
```

### Conventions

Models generated by the `build:model` command will be placed in the `app/Models` directory. A very basic model has the following structure:

```php
<?php

namespace App\Models;

use Core\Model\Model;

class Post extends Model
{
    // ...
}
```

**Table Name:**
By convention, the "snake case," plural name of the class will be used as the table name unless another name is explicitly specified. So, in this case, Elemental will assume the `Post` model stores records in the `posts` table.

You may manually specify the model's table name by defining a `tableName` property on the model:

```php
<?php

namespace App\Models;

use Core\Model\Model;

class Post extends Model
{
    protected $tableName = 'elemental_posts';
}
```

**Primary Key:**

Elemental will also assume that each model's corresponding database table has a primary key column named `id`. If necessary, you may define a protected `$primaryKey` property on your model to specify a different column that serves as your model's primary key:

```php
<?php

namespace App\Models;

use Core\Model\Model;

class Post extends Model
{
    protected $primaryKey = 'elemental_id';
}
```

### Model Operations

You can think of each model as a powerful query builder allowing you to fluently query the database table associated with the model.

### All

The model's `all` method will retrieve all of the records from the model's associated database table:

```php
use App\Models\Story;

foreach (Story::all() as $story) {
    echo $story["content"];
}
```

By default, the records that are fetched are represented as an array. However, you can pass a mode argument which controls how each record is represented. The mode argument takes any of PDO Fetch modes. For instance,

```php
use App\Models\Story;

foreach (Story::all() as $story) {
    echo $story->content;
}
```

### All Where

The `allWhere` method is a powerful abstraction in the model that allows executing complex queries. This method takes three arguments: `conditions`, `options`, and `fetchMode`.

#### Method Signature

```php
public static function allWhere(array $conditions, array $options = [], int $fetchMode = PDO::FETCH_ASSOC)
```

**Conditions:**
The `conditions` parameter is an array of clauses that the record must satisfy to be fetched. Each condition can be either a `[key => value]` pair or a `[key => [operator, value]]` pair.

- The `key` corresponds to a specific column in the table.
- If the condition is in the form of `[key => value]`, the default operator is `=` and the `value` is the data inside that column for the record.
- If the condition is in the form of `[key => [operator, value]]`, you can specify the operator for each condition. The supported operators are:
  - `['=', '!=', '<', '>', '<=', '>=', 'LIKE', 'IS NULL', 'IS NOT NULL']`.

**Options:**
The `options` parameter is an array that determines additional query arguments, such as `order by`, `limit`, etc. Supported constructs in the options argument include:
- `"orderBy"`
- `"limit"`
- `"offset"`
- `"sortDir"`

**FetchMode:**
The `fetchMode` parameter controls how each fetched record is represented. The mode argument takes any of the PDO Fetch modes:
- `PDO::FETCH_ASSOC`
- `PDO::FETCH_NUM`
- `PDO::FETCH_BOTH`
- `PDO::FETCH_OBJ`
- `PDO::FETCH_CLASS`
- `PDO::FETCH_INTO`
- `PDO::FETCH_LAZY`
- `PDO::FETCH_KEY_PAIR`

An example will make it more clear:

```php
use Core\Request\Request;

class StoryController {

    const PAGE_SIZE = 10;

    public function index(Request $request)
    {
        $search = $request->search;
        $categoryId = $request->category_id;

        $sortBy = $request->sort_by; // ASC or DESC, Default = ASC
        $page = $request->page;
        $orderBy = $request->order_by;

        return Story::allWhere(
            [
                "category_id" => $categoryId,
                "title" => ["LIKE", "%$search$"],
            ],
            [
                "limit" => static::PAGE_SIZE,
                "orderBy" => $orderBy,
                "sortDir" => $sortBy,
                "offset" => ($page - 1) * static::PAGE_SIZE,
            ],
            PDO::FETCH_OBJ
        );
    }
}
```

### Fetching Single Record

In addition to retrieving all of the records matching a given query, you may also retrieve single records using the `find` and `where` method. Instead of returning an array of records, these methods return a single model instance:

**Find:**
This will fetch the first record that matches the Primary key of the table.

```php
$flight = Story::find(1);
```

**Where:**
The where method takes an array of conditions that the record must satisfy to be fetched. Each condition can be either a `[key => value]` pair or a `[key => [operator, value]]` pair.

- The `key` corresponds to a specific column in the table.
- If the condition is in the form of `[key => value]`, the default operator is `=` and the `value` is the data inside that column for the record.
- If the condition is in the form of `[key => [operator, value]]`, you can specify the operator for each condition. The supported operators are:
  - `['=', '!=', '<', '>', '<=', '>=', 'LIKE', 'IS NULL', 'IS NOT NULL']`.

For Example
```php
$user = User::where(["email" => $email]);
$liked = Like::where(["user_id" => $user->id, "story_id" => $story_id]);
```

### Create

To insert a new record into the database, you can instantiate a new model instance and set attributes on the model. Then, call the `save` method on the model instance:

```php
<?php

namespace App\Controllers;

use App\Models\Story;
use Core\Request\Request;

class StoryController
{
    public function store(Request $request)
    {
        $story = new Story;
        $story->name = $request->name;
        $story->save();
        return redirect('/story');
    }
}
```

In this example, we assign the `name` field from the incoming HTTP request to the `name` attribute of the `App\Models\Story` model instance. When we call the `save` method, a record will be inserted into the database. The model's `created_at` timestamp will automatically be set when the `save` method is called, so there is no need to set it manually.

Alternatively, you may use the static `create` method to "save" a new model using a single PHP statement. The inserted model instance will be returned to you by the `create` method:

```php
use App\Models\Story;

$story = Story::create([
    'name' => 'A tale of elemental magic',
]);
```

### Update

The `save` method may also be used to update models that already exist in the database. To update a model, you should retrieve it and set any attributes you wish to update. Then, you should call the model's `save` method.

```php
use App\Models\Story;

$story = Story::find(10);
$story->name = 'An elemental tale of magic';
$story->save();
```

Alternatively, you may use the static `update` method to update a model instance. The first argument is the id of the model, and the second argument needs to be the array of column value pair.

```php
use App\Models\Story;

$story = Story::update(10, ["name" => "A tale", "content" => "Once upon a time ...."]);
```

### Delete

To delete a model, you may call the `destroy` method on the model instance:

```php
use App\Models\Story;

$story = Story::find(12);
$story->destroy();
```

However, if you know the primary key of the model, you may delete the model without explicitly retrieving it by calling the `delete` method. The `id` of the deleted record is returned.

```php
use App\Models\Story;

Story::delete(12);
```

### Data

You may call the `data` method on the model to retrieve all the attributes of a modal instance in an array form.

```php
$user = User::find(10);
$user_data = $user->data();
```
