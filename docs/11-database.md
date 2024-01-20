# Database

In the modern web applications, database interaction is a fundamental aspect. Elemental has been designed to streamline this interaction seamlessly across a diverse range of supported databases, leveraging the inherent capabilities of PHP PDO. With Elemental, you have the flexibility to execute any complex query or transaction using the `Core\Database\Database` class.

Elemental offers a robust Object-Relational Mapper (ORM) that effectively abstracts away many intricacies, proving invaluable for the majority of database queries. However, the `Core\Database\Database` can be used to run more advanced SQL queries.

All the configurations for your Elemental App are located in your application's `app/config/config.php` configuration file. Here you may define all of your database connections, as well as specify which connection should be used by default. Most of the configuration options within this file are driven by the values of your application's environment variables.

### Database Configuration

All the configurations for your Elemental App are located in your application's `app/config/config.php` configuration file. Here you may define all of your database connections, as well as specify which connection should be used by default. Most of the configuration options within this file are driven by the values of your application's environment variables.

```php
<?php

return [
    "db" => [
        "driver" => getenv("DB_DRIVER") ?? "mysql",
        "host" => getenv("DB_HOST") ?? $_SERVER['SERVER_ADDR'],
        "port" => getenv("DB_PORT") ?? "3306",
        "database" => getenv("DB_DATABASE") ?? "elemental",
        "username" => getenv("DB_USERNAME") ?? "root",
        "password" => getenv("DB_PASSWORD") ?? "",
    ],
];
```

Elemental uses PDO as the underlying database handling class. All PDO functions are directly available on the `Core\Database\Database` class. You can inject an instance of `Core\Database\Database` into any constructor or controller method to call PDO methods. The default configuration for Elemental is set up for MySQL databases, but you can change the driver inside the config file.

### Running a Query

Here's an example of running a query through the `Database` instance:

```php
public function tokens(Database $db) {
    $user_id = 1;

    $sql = "SELECT * FROM access_tokens WHERE user_id = :user_id";

    $stmt = $db->prepare($sql);

    $stmt->bindValue(":user_id", $user_id);
    $stmt->execute();

    $tokens = $stmt->fetchAll();
}
```

For more information on PDO, you can refer to [PHP's PDO documentation](https://www.php.net/manual/en/book.pdo.php)
