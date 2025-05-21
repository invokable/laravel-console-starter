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

1. Create a notification class:

```shell
php artisan make:notification CommandExecuted
```

2. Edit the generated notification class:

```php
<?php

namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Messages\SlackMessage;
use Illuminate\Notifications\Notification;

class CommandExecuted extends Notification implements ShouldQueue
{
    use Queueable;

    protected $command;
    protected $output;
    protected $success;

    public function __construct(string $command, string $output, bool $success = true)
    {
        $this->command = $command;
        $this->output = $output;
        $this->success = $success;
    }

    public function via($notifiable)
    {
        // Return the channels through which the notification should be sent
        return ['mail', 'slack']; // Choose channels you need
    }

    public function toMail($notifiable)
    {
        $status = $this->success ? 'succeeded' : 'failed';
        
        return (new MailMessage)
            ->subject("Command {$this->command} {$status}")
            ->greeting('Command Execution Report')
            ->line("The command '{$this->command}' has {$status}.")
            ->line('Output:')
            ->line($this->output)
            ->line('Thank you for using our application!');
    }

    public function toSlack($notifiable)
    {
        $status = $this->success ? 'succeeded' : 'failed';
        
        return (new SlackMessage)
            ->success($this->success)
            ->content("The command '{$this->command}' has {$status}.")
            ->attachment(function ($attachment) {
                $attachment->title('Command Output')
                           ->content($this->output);
            });
    }
}
```

3. Implement the notification in your command:

```php
<?php

namespace App\Console\Commands;

use App\Notifications\CommandExecuted;
use Illuminate\Console\Command;
use Illuminate\Support\Facades\Notification;

class YourCommand extends Command
{
    protected $signature = 'app:your-command';
    protected $description = 'Your command description';

    public function handle()
    {
        // Start output buffering to capture console output
        ob_start();
        
        $success = true;
        try {
            // Your command logic here
            $this->info('Command executed successfully!');
        } catch (\Exception $e) {
            $this->error('Command failed: ' . $e->getMessage());
            $success = false;
        }
        
        // Get the buffered output
        $output = ob_get_clean();
        
        // Send notification with command results
        Notification::route('mail', config('mail.admin_address'))
                   ->route('slack', config('services.slack.webhook_url'))
                   ->notify(new CommandExecuted($this->signature, $output, $success));
        
        return $success ? Command::SUCCESS : Command::FAILURE;
    }
}
```

#### Configuration

1. Make sure your mail settings are properly configured in `.env`:

```
MAIL_MAILER=smtp
MAIL_HOST=your-mail-host
MAIL_PORT=your-mail-port
MAIL_USERNAME=your-username
MAIL_PASSWORD=your-password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=your-email@example.com
MAIL_FROM_NAME="${APP_NAME}"
```

2. For Slack notifications, install the Slack notification channel:

```shell
composer require laravel/slack-notification-channel
```

3. Configure your Slack webhook URL in `config/services.php`:

```php
'slack' => [
    'notifications' => [
        'webhook_url' => env('SLACK_WEBHOOK_URL'),
        'channel' => env('SLACK_CHANNEL', null),
        'username' => env('SLACK_USERNAME', 'Laravel Console'),
        'icon' => env('SLACK_ICON', null),
    ],
],
```

4. Add your webhook URL to `.env`:

```
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/your-webhook-url
SLACK_CHANNEL=#your-channel
```

With this configuration, you can easily send notifications about the results of your console commands to stay informed about execution status, especially for scheduled commands running via GitHub Actions.

## LICENSE
MIT        
