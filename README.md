# Laravel Console Starter

A starter template for Laravel console applications.

## Requirements
- PHP >= 8.2
- Laravel >= 12.x
- Laravel Installer >= 5.14

## Installation

```shell
laravel new my-app --using=revolution/laravel-console-starter
```

## Usage

### Make a new command

```shell
php artisan make:command Test --command=app:test
```

### Re-add config file

```shell
php artisan config:publish services
```

### Task Scheduling in GitHub Actions

[cron.yml](./.github/workflows/cron.yml) is an example of how to run the command in GitHub Actions.

## LICENSE
MIT  
