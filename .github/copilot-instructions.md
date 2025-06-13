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
- Reporting and analytics automation
- Database maintenance and cleanup tasks

**Target Users**: Backend developers, DevOps engineers, and automation specialists who want to build reliable, testable console applications without the overhead of web application infrastructure.

**Key Benefits**:
- Rapid setup of console-focused Laravel applications (not web apps)
- Built-in GitHub Actions integration for cloud-based scheduling and CI/CD
- Multi-channel notification system (Email, Slack, Discord) with repository secrets management
- Comprehensive testing and quality assurance workflows with Laravel Pint
- Access to Laravel's dependency injection, ORM, and service ecosystem
- Focus on custom Artisan commands as core entry points

## Project Organization

### Core Systems and Services

The project is organized around several key systems:

1. **Artisan Command System** (`app/Console/Commands/`)
    - Primary interface for all application logic
    - Custom command classes extending Laravel's base Command class
    - Generated via `php artisan make:command CommandName --command=signature`
    - Commands are the core entry point for console applications

2. **GitHub Actions Automation** (`.github/workflows/`)
    - External scheduling system replacing traditional cron jobs
    - Automated CI/CD pipeline for testing and deployment
    - Secure environment variable management through repository secrets
    - Workflows: cron.yml (scheduling), tests.yml (testing), lint.yml (code quality), update.yml (dependencies)

3. **Notification Infrastructure** (`app/Notifications/`)
    - Multi-channel alert system for command execution results
    - Support for Email, Slack, and Discord notifications
    - Generated via `php artisan make:notification NotificationName`
    - Recommended for error reporting and task completion alerts

4. **Configuration Management** (`config/`, `.env`)
    - Environment-specific settings for different deployment contexts
    - Service credentials and API key management via repository secrets
    - Laravel's standard configuration system with `config/mail.php` and `config/services.php`

### Main Files and Directories

```
laravel-console-starter/
├── app/Console/Commands/          # Custom Artisan command classes (created on-demand)
├── app/Notifications/             # Notification classes for alerts (created on-demand)
├── .github/workflows/             # GitHub Actions automation
│   ├── cron.yml                  # Scheduled command execution
│   ├── tests.yml                 # Automated testing pipeline
│   ├── lint.yml                  # Code quality checks (Pint)
│   └── update.yml                # Dependency updates
├── docs/                         # Comprehensive tutorials
│   ├── tutorial.md               # English tutorial with examples
│   └── tutorial_ja.md            # Japanese tutorial
├── config/                       # Laravel configuration files
│   ├── mail.php                  # Email configuration
│   └── services.php              # Third-party service settings
├── routes/console.php            # Console route definitions
├── tests/                        # PHPUnit tests for commands
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
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use Illuminate\Support\Facades\Log;

class ExampleCommand extends Command
{
    protected $signature = 'example:command {--option=default}';
    protected $description = 'Command description';
    
    public function handle(): int
    {
        try {
            // Command logic here
            $this->info('Command executed successfully');
            
            return Command::SUCCESS;
        } catch (\Exception $e) {
            Log::error('Command failed: ' . $e->getMessage());
            $this->error('Command failed: ' . $e->getMessage());
            
            return Command::FAILURE;
        }
    }
}
```

**Notification Pattern**:
```php
// app/Notifications/TaskCompleted.php
<?php

namespace App\Notifications;

use Illuminate\Notifications\Notification;
use Illuminate\Notifications\Messages\MailMessage;

class TaskCompleted extends Notification
{
    public function via($notifiable): array
    {
        return ['mail', 'slack', 'discord-webhook'];
    }
    
    public function toMail($notifiable): MailMessage
    {
        return (new MailMessage)
            ->subject('Task Completed')
            ->line('Your scheduled task has completed successfully.');
    }
}
```

**GitHub Actions Integration**:
```yaml
# .github/workflows/cron.yml
name: cron

on:
  schedule:
    - cron: '0 0 * * *'  # Daily execution at midnight UTC

jobs:
  cron:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: 8.4
      - name: Install Dependencies
        run: composer install --no-dev -q
      - name: Copy Environment File
        run: cp .env.example .env
      - name: Generate Application Key
        run: php artisan key:generate
      - name: Run Command
        run: php artisan your:command
        env:
          API_KEY: ${{ secrets.API_KEY }}
```

## Best Practices and Development Guidelines

### Command Development Best Practices

1. **Use Laravel's Dependency Injection**
   ```php
   public function handle(UserService $userService, EmailService $emailService): int
   {
       // Laravel automatically injects dependencies
       $users = $userService->getActiveUsers();
       return Command::SUCCESS;
   }
   ```

2. **Return Proper Exit Codes**
   - Always return `Command::SUCCESS` (0) for successful execution
   - Return `Command::FAILURE` (1) for errors
   - Use `Command::INVALID` (2) for invalid arguments/options

3. **Handle Exceptions with Logging and Notifications**
   ```php
   public function handle(): int
   {
       try {
           // Command logic
           return Command::SUCCESS;
       } catch (\Exception $e) {
           Log::error('Command failed', [
               'command' => $this->signature,
               'error' => $e->getMessage(),
               'trace' => $e->getTraceAsString()
           ]);
           
           // Send failure notification
           Notification::route('mail', 'admin@example.com')
               ->notify(new CommandFailed($this->signature, $e->getMessage()));
               
           return Command::FAILURE;
       }
   }
   ```

4. **Write Tests for Commands**
   ```php
   // tests/Feature/ExampleCommandTest.php
   public function test_example_command_executes_successfully(): void
   {
       $this->artisan('example:command')
           ->assertExitCode(0)
           ->assertSuccessful();
   }
   ```

### Notification Best Practices

1. **Multi-Channel Configuration**
   - Configure `config/mail.php` for email notifications
   - Set up `config/services.php` for Slack and Discord webhooks
   - Use repository secrets for sensitive credentials

2. **Recommended Usage Patterns**
   - Send notifications for long-running task completion
   - Alert on command failures with error details
   - Provide summary reports for scheduled tasks
   - Include relevant context and actionable information

3. **Example Multi-Channel Notification**
   ```php
   public function via($notifiable): array
   {
       return ['mail', 'slack'];
   }
   
   public function toSlack($notifiable)
   {
       return (new SlackMessage)
           ->success()
           ->content('Daily report completed successfully')
           ->attachment(function ($attachment) {
               $attachment->title('Summary')
                         ->fields([
                             'Processed' => '1,234 records',
                             'Duration' => '2.5 minutes'
                         ]);
           });
   }
   ```

### Code Quality and Contribution Guidelines

1. **Use Laravel Pint for Code Style Consistency**
   ```bash
   vendor/bin/pint        # Fix code style
   vendor/bin/pint --test # Check code style
   ```

2. **Write Comprehensive Tests**
   - Test command execution success/failure scenarios
   - Mock external dependencies and services
   - Test notification sending
   - Use Laravel's built-in testing helpers

3. **Follow Laravel Conventions**
   - Use proper namespace conventions
   - Follow PSR-12 coding standards (enforced by Pint)
   - Use Laravel's service container and facades appropriately
   - Implement proper error handling and logging

### GitHub Actions and Scheduling

1. **Secrets Management**
   - Store sensitive data in repository secrets (Settings > Secrets and variables > Actions)
   - Access via `${{ secrets.SECRET_NAME }}` in workflows
   - Never commit secrets to code

2. **Cron Expression Examples**
   - `'0 0 * * *'`: Daily at midnight UTC
   - `'0 */6 * * *'`: Every 6 hours
   - `'0 0 * * 1'`: Weekly on Monday
   - `'*/15 * * * *'`: Every 15 minutes

## Practical Application Examples

### Monitoring and Automation
- **Website Uptime Monitoring**: Check site availability and send Slack alerts on downtime
- **API Health Checks**: Monitor external API endpoints and response times
- **Database Cleanup**: Remove old records, optimize tables, send summary reports
- **Log Analysis**: Parse application logs, detect patterns, alert on anomalies

### Data Processing and Reporting
- **Daily Sales Reports**: Generate and email business metrics
- **Data Synchronization**: Sync data between systems with error notifications
- **ETL Pipelines**: Extract, transform, load data with progress notifications
- **Analytics Aggregation**: Process user analytics and generate insights

### Financial and Business Intelligence
- **Cryptocurrency Portfolio Tracking**: Monitor holdings, send Discord updates
- **Invoice Processing**: Generate invoices, send payment reminders
- **Expense Report Automation**: Process receipts, categorize expenses
- **Market Data Collection**: Fetch stock prices, analyze trends

Each example should include:
- Error handling with proper logging
- Notifications for success/failure scenarios
- Scheduled execution via GitHub Actions
- Repository secrets for API keys and credentials

## Glossary of Codebase-Specific Terms

### **Artisan Commands**
*Location*: `app/Console/Commands/`  
Core building blocks of console applications. PHP classes extending `Illuminate\Console\Command`.

### **Console Starter**
*Location*: Project root concept  
The framework itself - a Laravel skeleton optimized for CLI-focused applications, not web apps.

### **GitHub Actions Scheduling**
*Location*: `.github/workflows/cron.yml`  
External cron replacement using GitHub's automation platform for command execution in the cloud.

### **Command Signature**
*Code*: `protected $signature = 'command:name {argument} {--option=default}'`  
Defines how commands are invoked from the terminal via `php artisan`, including arguments and options.

### **Handle Method**
*Code*: `public function handle(): int`  
Primary execution method in Artisan commands where business logic resides. Must return exit codes.

### **Notification Channels**
*Location*: `app/Notifications/`, `config/services.php`  
Multi-channel alert system supporting mail, Slack, and Discord webhooks for task notifications.

### **Repository Secrets**
*Location*: GitHub repository settings  
Secure environment variables passed to workflows via `${{ secrets.SECRET_NAME }}` for API keys and credentials.

### **Laravel Console Application**
*Concept*: Non-web Laravel application  
Application focused on CLI operations rather than HTTP request handling, using custom Artisan commands.

### **Command::SUCCESS / Command::FAILURE**
*Code*: Return values from `handle()` method  
Status codes indicating command execution results (0 for success, 1 for failure, 2 for invalid).

### **php artisan make:command**
*Usage*: `php artisan make:command CommandName --command=signature`  
Generator command for creating new Artisan command classes in `app/Console/Commands/`.

### **Cron Expression**
*Location*: `.github/workflows/cron.yml`  
Schedule syntax like `'0 0 * * *'` defining when workflows execute (minute hour day month weekday).

### **Notification Facade**
*Code*: `Notification::route('channel', 'recipient')`  
Laravel service for dispatching notifications across multiple channels from commands.

### **GitHub Actions Runner**
*Location*: Workflow files  
Virtual machine environment (`ubuntu-latest`) executing scheduled commands in the cloud.

### **Environment Configuration**
*Location*: `.env`, `.env.example`, repository secrets  
Runtime settings loaded by Laravel for database, mail, and service connections.

### **Composer Dependencies**
*Location*: `composer.json`, `vendor/`  
PHP package management system for Laravel framework and extensions (PHP ^8.2, Laravel ^12.0).

### **PHPUnit Testing**
*Location*: `tests/`, `.github/workflows/tests.yml`  
Automated testing framework integrated with Laravel's `artisan test` command for command testing.

### **Laravel Pint**
*Location*: `.github/workflows/lint.yml`, `vendor/bin/pint`  
PHP code style fixer ensuring consistent PSR-12 formatting across the codebase.

### **Laravel Tinker**
*Location*: `composer.json` dependency  
Interactive REPL environment for debugging and testing application code in development.

### **Application Key**
*Code*: `php artisan key:generate`  
Unique encryption key required for Laravel security features, generated during setup.

### **External API Integration**
*Code*: `Http::get()`, `Http::timeout()`, `Http::retry()`  
Laravel's HTTP client facade for making requests to external services with error handling.

### **Webhook Integration**
*Location*: Notification classes, `config/services.php`  
URL endpoints for sending notifications to Slack and Discord channels from commands.

### **Command Line Interface (CLI)**
*Concept*: Terminal-based interaction  
Primary user interface for this console-focused application framework.

### **Scheduled Tasks**
*Concept*: Automated execution via GitHub Actions  
Commands configured to run automatically on defined schedules without server maintenance.

### **Dependency Injection**
*Concept*: Laravel's service container  
Automatic injection of services and dependencies into command constructors and handle methods.

### **Task Scheduling**
*Location*: GitHub Actions workflows  
Cloud-based scheduling system replacing traditional server cron jobs for command execution.

### **Multi-Channel Notifications**
*Concept*: Notification system  
Ability to send alerts via multiple channels (email, Slack, Discord) from a single notification class.

### **Repository Structure**
*Concept*: Project organization  
Standardized directory layout optimized for console applications with on-demand directory creation.

## Documentation and Resources

For comprehensive usage instructions, practical examples, and detailed tutorials, refer to:

- **Primary Tutorial**: [docs/tutorial.md](../docs/tutorial.md) - Complete English guide with examples
- **Japanese Tutorial**: [docs/tutorial_ja.md](../docs/tutorial_ja.md) - Full Japanese documentation
- **Laravel Documentation**: [Laravel Console Commands](https://laravel.com/docs/artisan) - Official Laravel command documentation
- **Laravel Notifications**: [Laravel Notifications](https://laravel.com/docs/notifications) - Official notification system guide
- **GitHub Actions**: [GitHub Actions Documentation](https://docs.github.com/en/actions) - Workflow and scheduling reference

## Contributing and Community

- **Code Style**: Use Laravel Pint (`vendor/bin/pint`) to maintain PSR-12 consistency
- **Testing**: Write tests for all commands using Laravel's testing framework
- **Documentation**: Update tutorials and examples when adding new features
- **Issue Reporting**: Use GitHub issues for bug reports and feature requests
- **Pull Requests**: Follow Laravel conventions and include comprehensive tests
