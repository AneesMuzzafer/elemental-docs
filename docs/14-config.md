# Configuration

All configuration settings for the application are centralized in the `app\config\config.php` file. These configurations cover various aspects such as database connection information and other core settings essential for your app.

### Environment-specific Configuration

To cater to different environments where the application might run, a `.env.example` file is provided in the root directory. This file outlines common environment variables that can be configured. If you are working in a team, it's recommended to include the `.env.example` file with placeholder values. This makes it clear to other developers which environment variables are required to run the application.

When your application receives a request, all the variables listed in the `.env` file will be loaded into the `$_ENV` PHP super-global. You can then use the `getenv` function to retrieve values from these variables in your configuration files.

```php
$appName = getenv("APP_NAME");
```

### Accessing Configuration Values

To access configuration values, you can use type-hinting and inject the `Core\Config\Config` class into your constructors, controller methods, or route closures.

```php
use Core\Config\Config;

class YourClass {

    public function __construct(Config $config) {
        $driver = $config->db["driver"];
        $host = $config->db["host"];
        $port = $config->db["port"];
    }

    // Your other methods or code here
}
```

By doing this, you have a clean and organized way to retrieve configuration values within your application.

This approach keeps your configuration centralized and allows for easy changes based on the environment. It also promotes a clean and maintainable codebase.
