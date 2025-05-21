# Laravel Console Starter

A starter kit for Laravel console applications.

This starter kit is designed to accelerate the development of command-line applications using the Laravel framework. It provides a streamlined foundation by focusing on console-specific features, offering a clean slate without the typical web-related scaffolding. It's an ideal choice for developers looking to build cron jobs, task runners, data processing scripts, or any other CLI tools that can benefit from Laravel's robust components like its powerful Artisan command structure, task scheduling, and other core utilities, but without the overhead of a full web application setup.

## Key Features
- **Focus on Console Applications:** Streamlined for building CLI tools, removing web-specific overhead.
- **Artisan Command Ready:** Quickly generate and organize your console commands using `php artisan make:command`.
- **Scheduled Tasks with GitHub Actions:** Includes a pre-configured example (`.github/workflows/cron.yml`) for running your commands on a schedule using GitHub Actions.
- **Laravel Framework Power:** Leverage familiar Laravel features like its robust dependency injection container, event system, configuration management, and application testing tools for your console applications.

## Requirements
- PHP ^8.2
- Laravel Framework ^12.0
- Laravel Installer ^5.14

## Installation

```shell
laravel new my-app --using=revolution/laravel-console-starter
```

When asked about "npm install", select **"No"**.

## Usage

### Make a new command

```shell
php artisan make:command Hello --command=app:hello
```
This will create a new command class in `app/Console/Commands/Hello.php`. The `--command=app:hello` option sets the invokable name of your command, so you can run it later using `php artisan app:hello`.

### Task Scheduling in GitHub Actions

[cron.yml](./.github/workflows/cron.yml) is an example of how to run the command in GitHub Actions.
This workflow file demonstrates how to set up a cron-like schedule to execute your Artisan commands automatically. You'll need to customize it with the specific commands you want to run and their desired frequency. Remember to configure repository secrets for any sensitive information your commands might need (e.g., API keys, database credentials).

## Notifications

Laravel's built-in notification system provides a convenient way to send notifications from your console commands. This is particularly useful for:

-   Alerting you when a long-running task completes.
-   Reporting errors or issues encountered during command execution.
-   Sending updates or summaries to email, Slack, or other chat platforms.

To use this feature, you'll typically create a notification class (e.g., using `php artisan make:notification TaskCompleted`) and then send it using the `Notifiable` trait in your User model or via the `Notification` facade. You will need to configure your desired notification channels (like mail, Slack, etc.) in your Laravel application. When configuring notification channels, especially those relying on external services or specific mail drivers, you may need to publish the relevant configuration files if they don't already exist in your `config` directory. You can do this using the following Artisan commands:

```shell
php artisan config:publish mail
php artisan config:publish services
```

The `config/mail.php` file allows you to configure your mailer settings, while `config/services.php` is used to store credentials and settings for various third-party services that Laravel can integrate with for notifications (e.g., Slack, Vonage). For detailed setup and usage, please refer to the official [Laravel Notification documentation](https://laravel.com/docs/notifications).

## LICENSE
MIT  
