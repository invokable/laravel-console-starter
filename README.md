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

#### Benefits of GitHub Actions for Scheduling Commands

GitHub Actions offers several advantages for scheduling Laravel console commands:

- **No Server Cron Configuration**: Eliminates the need to set up and maintain cron jobs on your server
- **Version-Controlled Schedules**: Your command schedules are tracked in version control alongside your code
- **Built-in Logging**: Execution history and logs are automatically stored and viewable in the GitHub UI
- **Simplified Infrastructure**: Run scheduled tasks without maintaining dedicated servers
- **Secret Management**: Securely manage environment variables and secrets through GitHub's interface
- **Failure Notifications**: Get notified automatically when scheduled tasks fail
- **Conditional Execution**: Run tasks only when specific conditions are met (e.g., only on certain branches)
- **Scalability**: GitHub's infrastructure handles the execution, scaling resources as needed

### Using Laravel Notifications for Command Results

Laravel's notification system allows you to send the results of your command executions via various channels like email, Slack, or SMS. This is particularly useful for monitoring long-running commands or reporting on scheduled task outcomes.

#### Setting Up Notifications

Laravel provides a powerful notification system that can be integrated with your console commands to send execution results through various channels. Here's how to set it up:

1. **Create a notification class**: Use the `make:notification` Artisan command to generate a new notification class. This class will define how your notifications are formatted and sent through different channels.

2. **Configure notification channels**: Laravel supports multiple notification channels including:
   - Email: Send detailed command results via email
   - Slack: Post command execution status to Slack channels
   - SMS: Send brief status updates via SMS
   - Database: Store notifications for display in your application's UI

3. **Implement in your commands**: Add notification logic to your command classes to send results after execution. You can capture command output, execution time, and success/failure status to include in your notifications.

#### Configuration

To use Laravel notifications with your console commands, you'll need to configure the appropriate channels:

1. **Email Configuration**: Configure your mail settings in the `.env` file to enable email notifications. Laravel supports various mail drivers including SMTP, Mailgun, Postmark, and Amazon SES.

2. **Slack Configuration**: For Slack notifications, install the Slack notification channel package and configure your webhook URL in the services configuration file.

3. **Notification Recipients**: Define who should receive notifications about command execution. You can send notifications to specific email addresses, Slack channels, or phone numbers.

With this configuration, you can easily send notifications about the results of your console commands to stay informed about execution status, especially for scheduled commands running via GitHub Actions.

## LICENSE
MIT            
