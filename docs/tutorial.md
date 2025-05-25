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

## Task Scheduling with GitHub Actions

If you want to run your commands periodically, you can easily set up cron-like scheduled execution using GitHub Actions. This approach eliminates the need for traditional server-based cron jobs and allows you to run your scheduled tasks in the cloud.

### Setting Up Task Scheduling in GitHub Actions

Laravel Console Starter includes a pre-configured example workflow file (`.github/workflows/cron.yml`) that demonstrates how to set up scheduled task execution. Here's a detailed explanation of this file:

```yaml
name: cron                      # Name of the workflow

on:
  schedule:
    - cron: '0 0 * * *' #UTC    # Schedule expression (runs at midnight UTC daily)

jobs:
  cron:
    name: cron                  # Name of the job
    runs-on: ubuntu-latest      # Runner environment

    steps:
      - name: Checkout          # Step 1: Check out the repository
        uses: actions/checkout@v4

      - name: Setup PHP         # Step 2: Set up PHP environment
        uses: shivammathur/setup-php@v2
        with:
          php-version: 8.4
          coverage: none

      - name: Install Dependencies  # Step 3: Install Composer dependencies
        run: composer install --no-dev -q

      - name: Copy Environment File # Step 4: Set up environment
        run: cp .env.example .env

      - name: Generate Application Key # Step 5: Generate app key
        run: php artisan key:generate

      - name: Run Command       # Step 6: Run your Artisan command
        run: php artisan inspire
```

### Customizing the Workflow for Your Commands

To customize this workflow for your own commands:

1. **Adjust the Schedule**: Modify the `cron` expression in the `schedule` section to set your desired frequency. The format follows the standard cron syntax: `minute hour day-of-month month day-of-week`. For example:
   - `'0 0 * * *'`: Daily at midnight UTC
   - `'0 */6 * * *'`: Every 6 hours
   - `'0 0 * * 1'`: Weekly on Monday at midnight

2. **Change the Command**: Replace `php artisan inspire` in the "Run Command" step with your own command:
   ```yaml
   - name: Run Command
     run: php artisan your:command
   ```

3. **Run Multiple Commands**: To run multiple commands, you can add additional steps or use shell operators:
   ```yaml
   - name: Run Commands
     run: |
       php artisan first:command
       php artisan second:command
   ```

### Handling Sensitive Information with Repository Secrets

When your commands require access to sensitive information like API keys, database credentials, or service tokens, you should use GitHub repository secrets:

1. **Store Secrets in GitHub**:
   - Go to your repository on GitHub
   - Navigate to Settings > Secrets and variables > Actions
   - Click "New repository secret"
   - Add your secrets (e.g., `API_KEY`, `DATABASE_PASSWORD`)

2. **Access Secrets in Workflow**:
   ```yaml
   - name: Run Command with Secrets
     run: php artisan your:command
     env:
       API_KEY: ${{ secrets.API_KEY }}
       DB_PASSWORD: ${{ secrets.DATABASE_PASSWORD }}
   ```

3. **Use Secrets in Environment File**:
   ```yaml
   - name: Update Environment File
     run: |
       echo "API_KEY=${{ secrets.API_KEY }}" >> .env
       echo "DB_PASSWORD=${{ secrets.DATABASE_PASSWORD }}" >> .env
   ```

By using GitHub Actions for task scheduling, you can run your Laravel console commands on a regular schedule without maintaining a server with cron jobs. This approach is especially useful for tasks like data processing, reporting, monitoring, and sending notifications.

## Notification Feature

If you want to be notified of command execution results or errors, you can use Laravel's notification system. Laravel's built-in notification system provides a convenient way to send notifications from your console commands. This is particularly useful for:

- Alerting you when a long-running task completes
- Reporting errors or issues encountered during command execution
- Sending updates or summaries to email, Slack, or other chat platforms

### Creating Notification Classes

To create a notification class, use the `make:notification` Artisan command:

```bash
php artisan make:notification TaskCompleted
```

This will generate a new notification class in the `app/Notifications` directory. You can customize this class to include the information you want to send in your notification.

### Configuring Notification Channels

Laravel supports various notification channels out of the box, including:
- Mail
- Slack
- SMS (via services like Vonage)
- Database

To configure these channels, you may need to publish the relevant configuration files if they don't already exist in your `config` directory:

```bash
php artisan config:publish mail
php artisan config:publish services
```

The `config/mail.php` file allows you to configure your mailer settings, while `config/services.php` is used to store credentials and settings for various third-party services that Laravel can integrate with for notifications.

### Sending Notifications from Console Commands

Here's an example of how to send a notification from a console command:

```php
<?php

namespace App\Console\Commands;

use App\Notifications\TaskCompleted;
use Illuminate\Console\Command;
use Illuminate\Support\Facades\Notification;

class ProcessDataCommand extends Command
{
    protected $signature = 'data:process';
    protected $description = 'Process data and send notification when complete';

    public function handle()
    {
        // Your command logic here
        $this->info('Processing data...');
        
        // Process data...
        
        // Send notification when complete
        Notification::route('mail', 'admin@example.com')
            ->notify(new TaskCompleted('Data processing completed successfully'));
            
        return Command::SUCCESS;
    }
}
```

For detailed setup and usage, please refer to the official [Laravel Notification documentation](https://laravel.com/docs/notifications).

## Practical Application Examples

The following examples demonstrate how to build practical applications using Laravel Console Starter. Each example includes detailed implementation steps and shows how to configure necessary services and notifications.

### Example 1: Website Uptime Monitoring with Slack Alerts

This example demonstrates how to create a command that checks if your websites are online and sends alerts to Slack when issues are detected.

**Step 1: Create the Command**

```bash
php artisan make:command MonitorWebsites --command=monitor:websites
```

**Step 2: Create a Notification**

```bash
php artisan make:notification WebsiteDown
```

**Step 3: Configure Slack**

First, publish the services configuration:

```bash
php artisan config:publish services
```

Then, add your Slack webhook URL to the `.env` file:

```
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/YOUR/WEBHOOK/URL
```

And update the `config/services.php` file:

```php
'slack' => [
    'webhook_url' => env('SLACK_WEBHOOK_URL'),
],
```

**Step 4: Implement the Notification**

Edit `app/Notifications/WebsiteDown.php`:

```php
<?php

namespace App\Notifications;

use Illuminate\Notifications\Messages\SlackMessage;
use Illuminate\Notifications\Notification;

class WebsiteDown extends Notification
{
    protected $website;
    protected $error;

    public function __construct($website, $error)
    {
        $this->website = $website;
        $this->error = $error;
    }

    public function via($notifiable)
    {
        return ['slack'];
    }

    public function toSlack($notifiable)
    {
        return (new SlackMessage)
            ->error()
            ->content('Website Down Alert!')
            ->attachment(function ($attachment) {
                $attachment->title($this->website)
                           ->content("Error: {$this->error}")
                           ->timestamp(now());
            });
    }
}
```

**Step 5: Implement the Command**

Edit `app/Console/Commands/MonitorWebsites.php`:

```php
<?php

namespace App\Console\Commands;

use App\Notifications\WebsiteDown;
use Illuminate\Console\Command;
use Illuminate\Support\Facades\Http;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Facades\Notification;

class MonitorWebsites extends Command
{
    protected $signature = 'monitor:websites {--timeout=10}';
    protected $description = 'Check if websites are online and send alerts if they are down';

    protected $websites = [
        'https://example.com',
        'https://yourwebsite.com',
        // Add more websites as needed
    ];

    public function handle()
    {
        $timeout = $this->option('timeout');
        $this->info("Checking " . count($this->websites) . " websites (timeout: {$timeout}s)...");
        
        foreach ($this->websites as $website) {
            $this->info("Checking {$website}...");
            
            try {
                $response = Http::timeout($timeout)->get($website);
                
                if ($response->successful()) {
                    $this->info("{$website} is up and running!");
                    Log::info("Website {$website} is up", ['status' => $response->status()]);
                } else {
                    $this->error("{$website} returned status code: " . $response->status());
                    $this->sendAlert($website, "HTTP status code: " . $response->status());
                }
            } catch (\Exception $e) {
                $this->error("{$website} is down: " . $e->getMessage());
                $this->sendAlert($website, $e->getMessage());
            }
        }
        
        return Command::SUCCESS;
    }
    
    protected function sendAlert($website, $error)
    {
        $this->info("Sending alert for {$website}...");
        Log::error("Website {$website} is down", ['error' => $error]);
        
        // Send notification to Slack
        Notification::route('slack', config('services.slack.webhook_url'))
            ->notify(new WebsiteDown($website, $error));
    }
}
```

**Step 6: Schedule in GitHub Actions**

Update your `.github/workflows/cron.yml` file to run the command every hour:

```yaml
name: Website Monitoring

on:
  schedule:
    - cron: '0 * * * *' # Run hourly

jobs:
  monitor:
    runs-on: ubuntu-latest
    
    steps:
      # ... standard setup steps ...
      
      - name: Monitor Websites
        run: php artisan monitor:websites --timeout=30
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

### Example 2: Daily Cryptocurrency Portfolio Updates to Telegram

This example shows how to create a command that fetches cryptocurrency prices and sends a daily portfolio summary to Telegram.

**Step 1: Create the Command**

```bash
php artisan make:command CryptoPortfolio --command=crypto:portfolio
```

**Step 2: Create a Notification**

```bash
php artisan make:notification CryptoPortfolioUpdate
```

**Step 3: Configure Telegram**

First, publish the services configuration:

```bash
php artisan config:publish services
```

Then, add your Telegram bot token and chat ID to the `.env` file:

```
TELEGRAM_BOT_TOKEN=your_bot_token
TELEGRAM_CHAT_ID=your_chat_id
```

And update the `config/services.php` file:

```php
'telegram-bot-api' => [
    'token' => env('TELEGRAM_BOT_TOKEN'),
],
```

**Step 4: Install the Telegram Notification Channel**

```bash
composer require laravel-notification-channels/telegram
```

**Step 5: Implement the Notification**

Edit `app/Notifications/CryptoPortfolioUpdate.php`:

```php
<?php

namespace App\Notifications;

use Illuminate\Notifications\Notification;
use NotificationChannels\Telegram\TelegramMessage;

class CryptoPortfolioUpdate extends Notification
{
    protected $portfolio;
    protected $totalValue;

    public function __construct($portfolio, $totalValue)
    {
        $this->portfolio = $portfolio;
        $this->totalValue = $totalValue;
    }

    public function via($notifiable)
    {
        return ['telegram'];
    }

    public function toTelegram($notifiable)
    {
        $message = "💰 *Daily Crypto Portfolio Update* 💰\n\n";
        $message .= "Total Portfolio Value: $" . number_format($this->totalValue, 2) . "\n\n";
        
        foreach ($this->portfolio as $coin) {
            $change = $coin['change_24h'] > 0 ? "↗️ +" : "↘️ ";
            $message .= "*{$coin['symbol']}*: $" . number_format($coin['price'], 2) . " ({$change}{$coin['change_24h']}%)\n";
            $message .= "Holdings: {$coin['amount']} ({$coin['value']})\n\n";
        }
        
        return TelegramMessage::create()
            ->content($message);
    }
}
```

**Step 6: Implement the Command**

Edit `app/Console/Commands/CryptoPortfolio.php`:

```php
<?php

namespace App\Console\Commands;

use App\Notifications\CryptoPortfolioUpdate;
use Illuminate\Console\Command;
use Illuminate\Support\Facades\Http;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Facades\Notification;

class CryptoPortfolio extends Command
{
    protected $signature = 'crypto:portfolio';
    protected $description = 'Fetch cryptocurrency prices and send portfolio update';

    // Define your portfolio here
    protected $holdings = [
        'BTC' => 0.5,
        'ETH' => 5,
        'SOL' => 20,
        // Add more coins as needed
    ];

    public function handle()
    {
        $this->info('Fetching cryptocurrency prices...');
        
        try {
            // Using CoinGecko API (free tier)
            $response = Http::get('https://api.coingecko.com/api/v3/coins/markets', [
                'vs_currency' => 'usd',
                'ids' => 'bitcoin,ethereum,solana', // Match with your holdings
                'order' => 'market_cap_desc',
                'per_page' => 100,
                'page' => 1,
            ]);
            
            if (!$response->successful()) {
                throw new \Exception('API request failed: ' . $response->status());
            }
            
            $data = $response->json();
            $portfolio = [];
            $totalValue = 0;
            
            foreach ($data as $coin) {
                $symbol = strtoupper($coin['symbol']);
                
                if (isset($this->holdings[$symbol])) {
                    $amount = $this->holdings[$symbol];
                    $value = $amount * $coin['current_price'];
                    $totalValue += $value;
                    
                    $portfolio[] = [
                        'name' => $coin['name'],
                        'symbol' => $symbol,
                        'price' => $coin['current_price'],
                        'change_24h' => $coin['price_change_percentage_24h'],
                        'amount' => $amount,
                        'value' => '$' . number_format($value, 2),
                    ];
                    
                    $this->info("{$symbol}: \${$coin['current_price']} ({$coin['price_change_percentage_24h']}%)");
                }
            }
            
            $this->info('Total portfolio value: $' . number_format($totalValue, 2));
            
            // Send notification
            $chatId = env('TELEGRAM_CHAT_ID');
            Notification::route('telegram', $chatId)
                ->notify(new CryptoPortfolioUpdate($portfolio, $totalValue));
            
            $this->info('Portfolio update sent to Telegram!');
            
        } catch (\Exception $e) {
            $this->error('Error: ' . $e->getMessage());
            Log::error('Crypto portfolio update failed', ['error' => $e->getMessage()]);
            return Command::FAILURE;
        }
        
        return Command::SUCCESS;
    }
}
```

**Step 7: Schedule in GitHub Actions**

Update your `.github/workflows/cron.yml` file to run the command daily:

```yaml
name: Crypto Portfolio Update

on:
  schedule:
    - cron: '0 8 * * *' # Run daily at 8 AM UTC

jobs:
  crypto-update:
    runs-on: ubuntu-latest
    
    steps:
      # ... standard setup steps ...
      
      - name: Update Crypto Portfolio
        run: php artisan crypto:portfolio
        env:
          TELEGRAM_BOT_TOKEN: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          TELEGRAM_CHAT_ID: ${{ secrets.TELEGRAM_CHAT_ID }}
```

### Example 3: Database Backup and Notification

This example demonstrates how to create a command that performs database backups and sends completion status notifications.

**Step 1: Create the Command**

```bash
php artisan make:command DatabaseBackup --command=db:backup
```

**Step 2: Create a Notification**

```bash
php artisan make:notification BackupCompleted
```

**Step 3: Configure Email**

First, publish the mail configuration:

```bash
php artisan config:publish mail
```

Then, update your `.env` file with your mail settings:

```
MAIL_MAILER=smtp
MAIL_HOST=smtp.mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=your_username
MAIL_PASSWORD=your_password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=your-app@example.com
MAIL_FROM_NAME="${APP_NAME}"
```

**Step 4: Implement the Notification**

Edit `app/Notifications/BackupCompleted.php`:

```php
<?php

namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;

class BackupCompleted extends Notification
{
    use Queueable;

    protected $success;
    protected $filename;
    protected $size;
    protected $error;

    public function __construct($success, $filename = null, $size = null, $error = null)
    {
        $this->success = $success;
        $this->filename = $filename;
        $this->size = $size;
        $this->error = $error;
    }

    public function via($notifiable)
    {
        return ['mail'];
    }

    public function toMail($notifiable)
    {
        $message = (new MailMessage)
            ->subject($this->success ? 'Database Backup Completed Successfully' : 'Database Backup Failed');
        
        if ($this->success) {
            $message->line('The database backup has completed successfully.')
                    ->line('Backup file: ' . $this->filename)
                    ->line('Size: ' . $this->formatBytes($this->size))
                    ->line('Date: ' . now()->format('Y-m-d H:i:s'))
                    ->success();
        } else {
            $message->line('The database backup has failed.')
                    ->line('Error: ' . $this->error)
                    ->line('Date: ' . now()->format('Y-m-d H:i:s'))
                    ->error();
        }
        
        return $message;
    }
    
    protected function formatBytes($bytes, $precision = 2)
    {
        $units = ['B', 'KB', 'MB', 'GB', 'TB'];
        
        $bytes = max($bytes, 0);
        $pow = floor(($bytes ? log($bytes) : 0) / log(1024));
        $pow = min($pow, count($units) - 1);
        
        $bytes /= (1 << (10 * $pow));
        
        return round($bytes, $precision) . ' ' . $units[$pow];
    }
}
```

**Step 5: Implement the Command**

Edit `app/Console/Commands/DatabaseBackup.php`:

```php
<?php

namespace App\Console\Commands;

use App\Notifications\BackupCompleted;
use Illuminate\Console\Command;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Facades\Notification;
use Illuminate\Support\Facades\Storage;
use Symfony\Component\Process\Process;

class DatabaseBackup extends Command
{
    protected $signature = 'db:backup {--database=mysql} {--email=admin@example.com}';
    protected $description = 'Create a database backup and send notification';

    public function handle()
    {
        $database = $this->option('database');
        $email = $this->option('email');
        
        $this->info("Starting backup of {$database} database...");
        
        // Create backup directory if it doesn't exist
        $backupPath = storage_path('app/backups');
        if (!file_exists($backupPath)) {
            mkdir($backupPath, 0755, true);
        }
        
        // Generate backup filename
        $filename = $database . '_' . date('Y-m-d_H-i-s') . '.sql';
        $filepath = $backupPath . '/' . $filename;
        
        try {
            // Get database configuration
            $host = config("database.connections.{$database}.host");
            $port = config("database.connections.{$database}.port");
            $dbName = config("database.connections.{$database}.database");
            $username = config("database.connections.{$database}.username");
            $password = config("database.connections.{$database}.password");
            
            // Build mysqldump command
            $command = "mysqldump --host={$host} --port={$port} --user={$username} --password={$password} {$dbName} > {$filepath}";
            
            // Execute the command
            $process = Process::fromShellCommandline($command);
            $process->setTimeout(3600); // 1 hour timeout
            $process->run();
            
            if (!$process->isSuccessful()) {
                throw new \Exception($process->getErrorOutput());
            }
            
            // Check if backup file exists and get its size
            if (!file_exists($filepath)) {
                throw new \Exception("Backup file was not created");
            }
            
            $size = filesize($filepath);
            
            $this->info("Backup completed successfully: {$filepath} (" . $this->formatBytes($size) . ")");
            Log::info("Database backup completed", ['file' => $filename, 'size' => $size]);
            
            // Send success notification
            Notification::route('mail', $email)
                ->notify(new BackupCompleted(true, $filename, $size));
            
            return Command::SUCCESS;
            
        } catch (\Exception $e) {
            $this->error("Backup failed: " . $e->getMessage());
            Log::error("Database backup failed", ['error' => $e->getMessage()]);
            
            // Send failure notification
            Notification::route('mail', $email)
                ->notify(new BackupCompleted(false, null, null, $e->getMessage()));
            
            return Command::FAILURE;
        }
    }
    
    protected function formatBytes($bytes, $precision = 2)
    {
        $units = ['B', 'KB', 'MB', 'GB', 'TB'];
        
        $bytes = max($bytes, 0);
        $pow = floor(($bytes ? log($bytes) : 0) / log(1024));
        $pow = min($pow, count($units) - 1);
        
        $bytes /= (1 << (10 * $pow));
        
        return round($bytes, $precision) . ' ' . $units[$pow];
    }
}
```

**Step 6: Schedule in GitHub Actions**

Update your `.github/workflows/cron.yml` file to run the command weekly:

```yaml
name: Database Backup

on:
  schedule:
    - cron: '0 0 * * 0' # Run weekly on Sunday at midnight UTC

jobs:
  backup:
    runs-on: ubuntu-latest
    
    steps:
      # ... standard setup steps ...
      
      - name: Install MySQL client
        run: sudo apt-get install -y mysql-client
      
      - name: Backup Database
        run: php artisan db:backup --email=${{ secrets.ADMIN_EMAIL }}
        env:
          DB_HOST: ${{ secrets.DB_HOST }}
          DB_PORT: ${{ secrets.DB_PORT }}
          DB_DATABASE: ${{ secrets.DB_DATABASE }}
          DB_USERNAME: ${{ secrets.DB_USERNAME }}
          DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
          MAIL_HOST: ${{ secrets.MAIL_HOST }}
          MAIL_PORT: ${{ secrets.MAIL_PORT }}
          MAIL_USERNAME: ${{ secrets.MAIL_USERNAME }}
          MAIL_PASSWORD: ${{ secrets.MAIL_PASSWORD }}
```

These examples demonstrate how to build practical applications using Laravel Console Starter. You can adapt and combine these examples to create your own custom solutions for various use cases.

## Next Steps

Congratulations! You've now learned the fundamentals of building Laravel console applications with `revolution/laravel-console-starter`. You've created your first command, learned how to run it, set up task scheduling with GitHub Actions, implemented notifications, and explored practical examples.

To continue your journey with Laravel console applications:

* **Explore Laravel's Documentation**: Dive deeper into Laravel's features that can enhance your console applications at [Laravel's official documentation](https://laravel.com/docs).

* **Implement Testing**: Write tests for your commands using Laravel's testing framework to ensure reliability.

* **Explore Package Development**: Consider packaging your console commands as reusable Laravel packages if you find yourself creating similar functionality across projects.

* **Contribute to the Community**: Share your experiences, tools, and solutions with the Laravel community through blog posts or open-source contributions.

* **Stay Updated**: Laravel and its ecosystem evolve rapidly. Follow Laravel News, the official Laravel blog, and relevant GitHub repositories to stay current with best practices.

Remember that console applications can be powerful tools for automation, data processing, and system maintenance. The skills you've learned here can be applied to a wide range of use cases beyond the examples provided.

Happy coding!
