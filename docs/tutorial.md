# Laravel Console Starter Tutorial

## Introduction

This document is a tutorial for developing Laravel console applications using `revolution/laravel-console-starter`. This starter kit is designed to quickly build CLI (Command Line Interface) applications by leveraging the powerful features of Laravel.

Benefits of using `revolution/laravel-console-starter`:

*   **Quick Setup**: Start developing console applications immediately without complex configurations.
*   **Laravel Ecosystem**: Utilize Laravel's rich features (task scheduling, database, notifications, etc.) in your console application.
*   **Artisan Commands**: Leverage Laravel's powerful Artisan command system to easily create and manage your own commands.
*   **Testability**: Write tests for your console commands using Laravel's testing framework.

## Prerequisites

Please ensure the following software is installed before proceeding with this tutorial.

*   **PHP**: Version `^8.2` (or later)
*   **Composer**: PHP dependency management tool ([https://getcomposer.org/](https://getcomposer.org/))
*   **Laravel Installer**: Tool for easily creating Laravel projects (`composer global require laravel/installer`)

## Installation

To create a new Laravel console application project, execute the following command in your terminal:

```bash
laravel new my-app --using=revolution/laravel-console-starter --no-interaction
```

This will create a new directory named `my-app` and set up the basic structure for your console application.

Main directory structure after installation:

*   `app/Console/Commands/`: Your custom Artisan commands will be placed here.
*   `config/`: Stores application configuration files.
*   `.env`: File for environment-specific settings.
*   `routes/console.php`: File for registering Artisan commands.

## Environment Configuration

During the project installation process, several initial settings are automatically configured.

*   **`.env` File**: Based on the `.env.example` file, an `.env` file is automatically copied and created. This file contains environment-specific settings such as database connection information and mail settings.
*   **Application Key**: The `php artisan key:generate` command is automatically executed, and the necessary key for application encryption is set to `APP_KEY` in the `.env` file.

By default, several settings are configured as follows:

*   **Mail Settings**: Mail sending is configured to be logged (`MAIL_MAILER=log`). If you need to send actual emails, please configure services like Mailgun, Postmark, or SES.

## Creating a Console Command

To create a new Artisan command, use the `make:command` Artisan command.

```bash
php artisan make:command YourCommandName --command=your:command
```

*   `YourCommandName`: The class name of the command to be created (e.g., `SendDailyReport`).
*   `your:command`: The actual command name used to call the command (e.g., `report:send-daily`).

Executing this command will generate a file named `app/Console/Commands/YourCommandName.php`.

Below is an example of a simple command. This command outputs a message to the log when executed.

Edit `app/Console/Commands/HelloWorldCommand.php` as follows:

```php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use Illuminate\Support\Facades\Log;

class HelloWorldCommand extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'hello:world'; // The command signature (how it's called)

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = 'Displays a hello world message in the log'; // Description of the command

    /**
     * Execute the console command.
     *
     * @return int
     */
    public function handle()
    {
        Log::info('Hello, World from Artisan Command!'); // Output message to the log
        $this->info('Hello, World message has been logged.'); // Also output message to the console
        return Command::SUCCESS;
    }
}
```

In the example above:
1.  The command was created with `make:command HelloWorldCommand --command=hello:world`.
2.  The `$signature` property defines the command's call name as `hello:world`.
3.  The `$description` property defines a short description of the command. This is displayed when `php artisan list` is executed.
4.  The command's logic is written within the `handle` method. Here, `Log::info()` is used to write a message to `storage/logs/laravel.log`, and `$this->info()` displays a message in the console.

## Running the Command

To run the created command, use the following Artisan command in your terminal:

```bash
php artisan your:command
```

For example, to run the `HelloWorldCommand` above, execute the following command:

```bash
php artisan hello:world
```

After execution, you can confirm that the message "Hello, World from Artisan Command!" is outputted to the `storage/logs/laravel.log` file and the console.

## Next Steps

You have now learned how to create and run a basic console command using `revolution/laravel-console-starter`. Here are some tips for further development.

*   **Task Scheduling (GitHub Actions)**:
    If you want to run your commands periodically, you can easily set up cron-like scheduled execution by combining Laravel's powerful task scheduler with GitHub Actions. For details, refer to the "Task Scheduling with GitHub Actions" section in the project's `README.md`.

*   **Notification Feature**:
    If you want to be notified of command execution results or errors, you can use Laravel's notification system. For information on how to send notifications through various channels such as email, Slack, SMS, etc., refer to the "Notifications" section in the `README.md` or Laravel's official documentation.

*   **Further Ideas**:
    The `README.md` file contains several ideas for applications that can be built using this starter kit (e.g., data processing batches, API clients, system administration scripts, etc.). Use these as inspiration to try developing your own console applications.

Laravel's extensive documentation ([https://laravel.com/docs](https://laravel.com/docs)) will also be very helpful as you proceed with development.
Happy coding!
