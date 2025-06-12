# Laravel Console Starter - Onboarding Guide

## Overview

The Laravel Console Starter is a specialized framework for building Laravel applications that are primarily focused on command-line interface (CLI) functionality rather than traditional web applications. This system enables developers to quickly create console-based tools that leverage the full Laravel ecosystem while providing automated scheduling through GitHub Actions.

**Purpose**: This project serves developers who need to build:
- Scheduled background tasks and automation scripts
- Data processing workflows and ETL pipelines
- Website monitoring and health check systems
- API integration and data synchronization tools
- Cryptocurrency portfolio trackers and price monitoring
- Web scraping and content aggregation systems

**Target Users**: Backend developers, DevOps engineers, and automation specialists who want to build reliable, testable console applications without the overhead of web application infrastructure.

**Key Benefits**:
- Rapid setup of console-focused Laravel applications
- Built-in GitHub Actions integration for cloud-based scheduling
- Multi-channel notification system (Email, Slack, Discord)
- Comprehensive testing and quality assurance workflows
- Access to Laravel's dependency injection, ORM, and service ecosystem

## Project Organization

### Core Systems and Services

The project is organized around several key systems:

1. **Artisan Command System** (`app/Console/Commands/`)
    - Primary interface for all application logic
    - Custom command classes extending Laravel's base Command class
    - Generated via `php artisan make:command` with `--command` option

2. **GitHub Actions Automation** (`.github/workflows/`)
    - External scheduling system replacing traditional cron jobs
    - Automated CI/CD pipeline for testing and deployment
    - Secure environment variable management through repository secrets

3. **Notification Infrastructure** (`app/Notifications/`)
    - Multi-channel alert system for command execution results
    - Support for Email, Slack, and Discord notifications
    - Generated via `php artisan make:notification`

4. **Configuration Management** (`config/`, `.env`)
    - Environment-specific settings for different deployment contexts
    - Service credentials and API key management
    - Laravel's standard configuration system

### Main Files and Directories

```
laravel-console-starter/
├── app/Console/Commands/          # Custom Artisan command classes
├── app/Notifications/             # Notification classes for alerts
├── .github/workflows/             # GitHub Actions automation
│   ├── cron.yml                  # Scheduled command execution
│   ├── tests.yml                 # Automated testing pipeline
│   ├── lint.yml                  # Code quality checks
│   └── update.yml                # Dependency updates
├── docs/                         # Comprehensive tutorials
│   ├── tutorial.md               # English tutorial with examples
│   └── tutorial_ja.md            # Japanese tutorial
├── config/                       # Laravel configuration files
│   ├── mail.php                  # Email configuration
│   └── services.php              # Third-party service settings
├── bootstrap/app.php             # Application bootstrapping
├── composer.json                 # PHP dependency management
├── .env.example                  # Environment template
├── .gitignore                    # Version control exclusions
└── README.md                     # Primary project documentation
```

### Main Functions and Classes

**Core Command Structure**:
```php
// app/Console/Commands/ExampleCommand.php
class ExampleCommand extends Command
{
    protected $signature = 'command:name';
    protected $description = 'Command description';
    
    public function handle(): int
    {
        // Command logic here
        return Command::SUCCESS;
    }
}
```

**Notification Pattern**:
```php
// app/Notifications/TaskCompleted.php
class TaskCompleted extends Notification
{
    public function via($notifiable): array
    {
        return ['mail', 'slack', 'discord-webhook'];
    }
}
```

**GitHub Actions Integration**:
```yaml
# .github/workflows/cron.yml
on:
  schedule:
    - cron: '0 0 * * *'  # Daily execution
jobs:
  cron:
    runs-on: ubuntu-latest
    steps:
      - run: php artisan your:command
```

## Glossary of Codebase-Specific Terms

### **Artisan Commands**
*Location*: `app/Console/Commands/`  
Core building blocks of console applications. PHP classes extending `Illuminate\Console\Command`.

### **Console Starter**
*Location*: Project root concept  
The framework itself - a Laravel skeleton optimized for CLI-focused applications.

### **GitHub Actions Scheduling**
*Location*: `.github/workflows/cron.yml`  
External cron replacement using GitHub's automation platform for command execution.

### **Command Signature**
*Code*: `protected $signature = 'command:name'`  
Defines how commands are invoked from the terminal via `php artisan`.

### **Handle Method**
*Code*: `public function handle(): int`  
Primary execution method in Artisan commands where business logic resides.

### **Notification Channels**
*Location*: `app/Notifications/`, `config/services.php`  
Multi-channel alert system supporting mail, Slack, and Discord webhooks.

### **Repository Secrets**
*Location*: GitHub Actions workflows  
Secure environment variables passed to workflows via `secrets.SECRET_NAME`.

### **Laravel Console Application**
*Concept*: Non-web Laravel app  
Application focused on CLI operations rather than HTTP request handling.

### **Command::SUCCESS / Command::FAILURE**
*Code*: Return values from `handle()`  
Status codes indicating command execution results.

### **php artisan make:command**
*Usage*: `--command=signature`  
Generator command for creating new Artisan command classes.

### **Cron Expression**
*Location*: `.github/workflows/cron.yml`  
Schedule syntax like `'0 0 * * *'` defining when workflows execute.

### **Notification Facade**
*Code*: `Notification::route('channel', 'recipient')`  
Laravel service for dispatching notifications across channels.

### **GitHub Actions Runner**
*Location*: Workflow files  
Virtual machine environment (`ubuntu-latest`) executing scheduled commands.

### **Environment Configuration**
*Location*: `.env`, `.env.example`  
Runtime settings loaded by Laravel for database, mail, and service connections.

### **Composer Dependencies**
*Location*: `composer.json`, `vendor/`  
PHP package management system for Laravel framework and extensions.

### **Hot Module Replacement (HMR)**
*Location*: `.gitignore` (`public/hot`)  
Development feature for instant frontend updates without page reloads.

### **Laravel Homestead**
*Location*: `Homestead.json`, `Homestead.yaml`  
Pre-configured Vagrant development environment for consistent local setup.

### **PHPUnit Testing**
*Location*: `.github/workflows/tests.yml`  
Automated testing framework integrated with Laravel's `artisan test` command.

### **Pint Linter**
*Location*: `.github/workflows/lint.yml`  
PHP code style fixer ensuring consistent formatting via `vendor/bin/pint`.

### **Storage Directory**
*Location*: `storage/`, `public/storage`  
Application data persistence layer for logs, uploads, and generated files.

### **Laravel Tinker**
*Location*: `composer.json` dependency  
Interactive REPL environment for debugging and testing application code.

### **Application Key**
*Code*: `php artisan key:generate`  
Unique encryption key required for Laravel security features and sessions.

### **External API Integration**
*Code*: `Http::get()`, `Http::timeout()`  
Laravel's HTTP client facade for making requests to external services.

### **Webhook Integration**
*Location*: Notification classes  
URL endpoints for sending notifications to Slack and Discord channels.

### **Command Line Interface (CLI)**
*Concept*: Terminal-based interaction  
Primary user interface for this console-focused application framework.

### **Scheduled Tasks**
*Concept*: Automated execution  
Commands configured to run automatically via GitHub Actions cron schedules.
