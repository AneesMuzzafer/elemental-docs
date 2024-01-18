# Candle (Command Line Engine)

Candle is the command line engine of the Elemental. Candle exists at the root of your application as the `candle` script and provides a number of helpful commands designed to aid you in the development process of your application. To view a list of all available Candle commands, you may use the `help` command:

```php
php candle help
```
This will also display the custom commands that you may have created yourself.

### Development Server
By now, you must have already `ignited` the the Elemental's candle to run your app.  This `ignite`  command serves the app at the IP Address 127.0.0.1, searching for a free port starting from 8000. If Port 8000 is occupied, Elemental automatically attempts to bind to the next available port (e.g., 8001) and so forth.

```bash
php candle ignite
```

You have the flexibility to customize the server setup according to your requirements.

#### Custom Host
Specify a specific IP address using the `--host` argument. For instance:
```bash
php candle ingite --host=192.168.1.10
```

#### Custom Port
If you prefer binding to a specific port, use the `--port` argument:
```bash
php candle ingite --port=8080
```

To serve your application at a custom IP and port simultaneously, provide both the `--host` and `--port` arguments:

```bash
php candle ingite --host=192.168.1.10 --port=8080
```
The `--host` and `--port`	arguments can be placed in any order.

### List all Routes

To obtain a comprehensive view of all registered routes within your application, utilize the `route:list` command provided by Candle:

bash

```bash
php candle route:list
```

### Generating Files

You can use the Candle `build` command to generate files for your models, controllers, middleware and commands.

##### Generate a Model

To create a model, execute the following command:

```bash
php candle build:model Story
```
This command will generate a file named `Story.php` within the `app\models` directory, containing the `Story` class.

##### Generate a Controller

For generating a controller, the `build` command is similarly employed:

```bash Copy code
php candle build:controller StoryController
```

Executing this command will generate a file named `StoryController.php` in the `app\controllers` directory, featuring the `MyController` class.

##### Generate Middleware

To generate a middleware, utilize the `build` command as follows:

```bash
php candle build:middleware HasSession
```

This will create a file named `HasSession.php` within the `app\middleware` directory, housing the `handle` method.

##### Generate Command

For command generation, execute the `build` command with the appropriate arguments:

```bash
php candle build:command Migration
```

Executing this command will generate a file named `Migration.php` in the `app\commands` directory, containing the `Migration` class and the `handle` method.

### Custom Commands in Candle

Generating custom commands is where the Candle's power can be experienced. Commands are stored in the `app/commands` directory, and it's essential to load them inside the array returned in `app\commands\Commands.php` for proper registration within the app.

#### Command Structure

After generating a command, define values for the `key` and `description` properties of the class. The `key` is used as the argument for the command, while `description` will be displayed in the help screen. The `handle` method will be called when the command is executed, and you can place your command logic in this method.

You can type-hint any dependencies required for your command handling. Elemental's DI Container will automatically inject all dependencies type-hinted in the `handle` method's signature.

Let's take a look at an example command:

```php
<?php

namespace App\Commands;

use App\Models\User;
use App\Service\MailService;
use Core\Console\Command;

class SendEmails extends Command
{
    protected $key = 'mail:send';
    protected $description = 'Send mails to all users';

    public function handle(MailService $mailService): void
    {
        $mailService->send(User::all());
    }
}
```

To execute the command in the command line:

```php
php candle mail:send
```

#### Retrieving Input Args

You can use Elemental's `Core\Console\Commander` to retrieve any inputs passed through the command line. The `Core\Console\Commander` provides a method named `getArgs` that returns an array of inputs passed from the command line. The Commander instance can be type-hinted through the handler method and used as required.

A concrete example will make it clear:

```php
<?php

namespace App\Commands;

use Core\Console\Command;
use Core\Console\Commander;

class Migration extends Command
{
    protected $key = "migrate";
    protected $description = "Custom migration handler.";

    private $commander;

    public function handle(Commander $commander, Database $db)
    {
        $args = $commander->getArgs();

        if (!isset($args[1])) {
            $this->up();
            return;
        }

        switch ($args[1]) {
            case "fresh":
                $this->downThenUp();
                break;
            case "delete":
                $this->down();
                break;
            default:
                $this->up();
        }
    }

    public function up()
    {
        $sql = "CREATE TABLE IF NOT EXISTS users (
            id INT PRIMARY KEY AUTO_INCREMENT,
            username VARCHAR(255) NOT NULL,
            email VARCHAR(255) NOT NULL UNIQUE,
            password VARCHAR(255) NOT NULL,
            bio TEXT,
            image VARCHAR(255),
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
        )";

        try {
            $db->exec($sql);
            console_log("Table 'users' created successfully!");
        } catch (\PDOException $e) {
            console_log("Table creation error: " . $e->getMessage());
        }
    }

    public function down()
    {
        $sql = "DROP TABLE IF EXISTS users";

        try {
            $db->exec($sql);
            console_log("Table 'users' deleted successfully!");
        } catch (\PDOException $e) {
            console_log("Table deletion error: " . $e->getMessage());
        }
    }

    public function downThenUp()
    {
        $this->down();
        $this->up();
    }
}

```

It is recommended to type-hint dependencies inside the handle method as opposed to inside the constructor of the command class.

To execute these migration commands in the command line:
```bash
php candle migrate
php candle migrate fresh
php candle migrate delete
```

As you can see, generating commands are very powerful and can be helpful to achieve a variety of functionalities. Here, a custom migration handler has been built. You can expand and organize the above structure or create a custom Migration Service that can handle your migration logic.

Commands can also be used for handling task scheduling. You may create a command that executes some logic and then pass the command to your operating systems CRON handler.
