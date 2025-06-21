# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Laravel Console Starter kit designed specifically for building console applications with artisan commands. It's a streamlined Laravel setup focused on command-line tools rather than web applications, leveraging Laravel's full ecosystem (dependency injection, notifications, scheduling, testing) without web overhead.

## Key Commands

### Development Commands
- `php artisan make:command CommandName --command=command-name` - Create new console commands
- `php artisan inspire` - Example command that displays inspiring quotes
- `composer test` - Run tests (runs config:clear then php artisan test)
- `vendor/bin/pint` - Run code formatting (Laravel Pint)
- `vendor/bin/pint --test` - Check code formatting without fixing

### Testing
- `php artisan test` - Run all PHPUnit tests
- Tests are located in `tests/Feature/` and `tests/Unit/`
- Uses SQLite in-memory database for testing

### Environment Setup
- Copy `.env.example` to `.env`
- Run `php artisan key:generate` to generate application key

## Architecture

### Console Commands
- Custom commands are created in `app/Console/Commands/`
- Commands can be registered in `routes/console.php` using `Artisan::command()`
- Example command registration is shown with the 'inspire' command

### Project Structure
- **Console-focused**: No web routes, controllers, or views
- **Commands**: Primary functionality through artisan commands
- **Notifications**: Built-in Laravel notification system for alerts (email, Slack, Discord)
- **Scheduling**: Designed to work with GitHub Actions cron workflows
- **Testing**: Full PHPUnit testing support with in-memory SQLite

### GitHub Actions Integration
- `.github/workflows/cron.yml` - Example scheduled command execution
- `.github/workflows/tests.yml` - Automated testing on push/PR
- `.github/workflows/lint.yml` - Code style checking with Pint
- Supports environment variables and secrets for production deployments

### Configuration
- **PHP**: ^8.2 required
- **Laravel**: ^12.0
- **Pint Config**: Laravel preset with `no_unused_imports: false`
- **Testing Environment**: Uses array drivers for cache, mail, sessions

## Typical Use Cases
This starter is ideal for building console applications like monitoring tools, data processors, schedulers, notification systems, and automated maintenance scripts that benefit from Laravel's architecture without web application overhead.