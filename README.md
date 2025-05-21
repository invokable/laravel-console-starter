# Laravel Console Starter

A starter kit for Laravel console applications.

This starter kit is designed to accelerate the development of command-line applications using the Laravel framework. It provides a streamlined foundation by focusing on console-specific features, offering a clean slate without the typical web-related scaffolding. It's an ideal choice for developers looking to build cron jobs, task runners, data processing scripts, or any other CLI tools that can benefit from Laravel's robust components like its powerful Artisan command structure, task scheduling, and other core utilities, but without the overhead of a full web application setup.

## Key Features
- **Focus on Console Applications:** Streamlined for building CLI tools, removing web-specific overhead.
- **Artisan Command Ready:** Quickly generate and organize your console commands using `php artisan make:command`.
- **Scheduled Tasks with GitHub Actions:** Includes a pre-configured example (`.github/workflows/cron.yml`) for running your commands on a schedule using GitHub Actions.
- **Laravel Framework Power:** Leverage familiar Laravel features like its robust dependency injection container, event system, configuration management, and application testing tools for your console applications.

## Requirements
- PHP >= 8.2
- Laravel Framework ^12.0
- Laravel Installer >= 5.14

## Installation

```shell
laravel new my-app --using=revolution/laravel-console-starter
```

When asked about "npm install", select **"No"**.

## Usage

### Make a new command

```shell
php artisan make:command Test --command=app:test
```
This will create a new command class in `app/Console/Commands/Test.php`. The `--command=app:test` option sets the invokable name of your command, so you can run it later using `php artisan app:test`.

### Task Scheduling in GitHub Actions

[cron.yml](./.github/workflows/cron.yml) is an example of how to run the command in GitHub Actions.
This workflow file demonstrates how to set up a cron-like schedule to execute your Artisan commands automatically. You'll need to customize it with the specific commands you want to run and their desired frequency. Remember to configure repository secrets for any sensitive information your commands might need (e.g., API keys, database credentials).

## LICENSE
MIT  
