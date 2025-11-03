# Enabling Database Functionality

> **Note:** The database feature is disabled by default in Laravel Console Starter to keep the framework lightweight for console-focused applications.

This guide explains how to enable database functionality when your console application needs to interact with databases.

## Overview

While console applications may not frequently use local databases, it's common to utilize remote databases like AWS RDS when running them in GitHub Actions. This guide covers:

1. Adding database files from the official Laravel repository
2. Updating Composer autoload configuration
3. Configuring database connections with repository secrets

## Step 1: Download Required Files from Laravel Repository

You need to download the following files and directories from the [official Laravel repository](https://github.com/laravel/laravel):

### 1.1 Download the Database Directory

Download the entire `database/` directory structure:

```
database/
├── factories/
│   └── UserFactory.php
├── migrations/
│   ├── 0001_01_01_000000_create_users_table.php
│   ├── 0001_01_01_000001_create_cache_table.php
│   └── 0001_01_01_000002_create_jobs_table.php
└── seeders/
    └── DatabaseSeeder.php
```

Create the directory structure in your project:

```bash
mkdir -p database/factories database/migrations database/seeders
```

Download and place files:
- `database/factories/UserFactory.php`
- `database/migrations/*` (all migration files)
- `database/seeders/DatabaseSeeder.php`

### 1.2 Download Database Configuration

Download `config/database.php` from the Laravel repository and place it in your `config/` directory:

```bash
# Download to your project
curl -o config/database.php https://raw.githubusercontent.com/laravel/laravel/12.x/config/database.php
```

### 1.3 Download User Model

Download `app/Models/User.php` from the Laravel repository:

```bash
# Create Models directory if it doesn't exist
mkdir -p app/Models

# Download User model
curl -o app/Models/User.php https://raw.githubusercontent.com/laravel/laravel/12.x/app/Models/User.php
```

## Step 2: Update Composer Autoload Configuration

Edit your `composer.json` file to add autoload paths for database factories and seeders:

```json
{
    "autoload": {
        "psr-4": {
            "App\\": "app/",
            "Database\\Factories\\": "database/factories/",
            "Database\\Seeders\\": "database/seeders/"
        }
    }
}
```

After updating `composer.json`, regenerate the autoload files:

```bash
composer dump-autoload
```

## Step 3: Configure Database Connection

### 3.1 Local Development

Update your `.env` file with database credentials:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=your_database_name
DB_USERNAME=your_username
DB_PASSWORD=your_password
```

For SQLite (simpler for local development):

```env
DB_CONNECTION=sqlite
# DB_DATABASE=database/database.sqlite
```

If `DB_DATABASE` is not set, `database/database.sqlite` will be used.

### 3.2 GitHub Actions Configuration

When using databases with GitHub Actions, configure database credentials using repository secrets for security.

#### Setting Up Repository Secrets

1. Go to your GitHub repository
2. Navigate to **Settings** > **Secrets and variables** > **Actions**
3. Click **New repository secret**
4. Add the following secrets:
   - `APP_KEY` - Your Laravel application key
   - `DB_HOST` - Database host address
   - `DB_DATABASE` - Database name
   - `DB_USERNAME` - Database username
   - `DB_PASSWORD` - Database password

#### Example Workflow Configuration

Update your `.github/workflows/cron.yml` or create a new workflow with database configuration:

```yaml
name: cron

on:
  schedule:
    - cron: '0 0 * * *'  # Daily at midnight UTC

jobs:
  cron:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: 8.4
          extensions: pdo, pdo_mysql
          
      - name: Install Dependencies
        run: composer install --no-dev -q
        
      - name: Copy Environment File
        run: cp .env.example .env
        
      - name: Generate Application Key
        run: php artisan key:generate
        
      - name: Run Migrations
        run: php artisan migrate --force
        env:
          APP_KEY: ${{ secrets.APP_KEY }}
          DB_HOST: ${{ secrets.DB_HOST }}
          DB_DATABASE: ${{ secrets.DB_DATABASE }}
          DB_USERNAME: ${{ secrets.DB_USERNAME }}
          DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
        
      - name: Run Command
        run: php artisan your:command
        env:
          APP_KEY: ${{ secrets.APP_KEY }}
          DB_HOST: ${{ secrets.DB_HOST }}
          DB_DATABASE: ${{ secrets.DB_DATABASE }}
          DB_USERNAME: ${{ secrets.DB_USERNAME }}
          DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
```

## Step 4: Run Migrations

Once database configuration is complete, run migrations to create tables:

```bash
php artisan migrate
```

For fresh installations with seeders:

```bash
php artisan migrate:fresh --seed
```

## Using Database in Commands

After enabling database functionality, you can use Eloquent models and database queries in your console commands:

```php
<?php

namespace App\Console\Commands;

use App\Models\User;
use Illuminate\Console\Command;
use Illuminate\Support\Facades\DB;

class ProcessUsers extends Command
{
    protected $signature = 'users:process';
    protected $description = 'Process user data';

    public function handle(): int
    {
        // Using Eloquent
        $users = User::where('active', true)->get();
        
        foreach ($users as $user) {
            $this->info("Processing user: {$user->email}");
            // Your processing logic here
        }
        
        // Using Query Builder
        $count = DB::table('users')
            ->where('created_at', '>', now()->subDays(7))
            ->count();
            
        $this->info("New users this week: {$count}");
        
        return Command::SUCCESS;
    }
}
```

## Database Connections for Different Environments

You can configure multiple database connections in `config/database.php`:

```php
'connections' => [
    'local' => [
        'driver' => 'mysql',
        'host' => env('DB_HOST', '127.0.0.1'),
        // ... local configuration
    ],
    
    'production' => [
        'driver' => 'mysql',
        'host' => env('PROD_DB_HOST'),
        // ... production configuration
    ],
],
```

Use specific connections in your commands:

```php
$users = DB::connection('production')->table('users')->get();
```

## Testing with Database

For testing commands that use databases, Laravel provides database testing utilities:

```php
<?php

namespace Tests\Feature;

use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class ProcessUsersTest extends TestCase
{
    use RefreshDatabase;

    public function test_process_users_command(): void
    {
        // Create test data
        User::factory()->count(5)->create();
        
        // Run command
        $this->artisan('users:process')
            ->assertExitCode(0)
            ->assertSuccessful();
    }
}
```

## Common Database Use Cases

### Remote Database Access
- AWS RDS MySQL/PostgreSQL
- Google Cloud SQL
- Azure Database
- PlanetScale
- Supabase

### Local Development Options
- SQLite (lightweight, file-based)
- MySQL (via Docker or local installation)
- PostgreSQL (via Docker or local installation)
- Laravel Sail (Docker development environment)

## Security Best Practices

1. **Never commit credentials** to version control
2. **Use repository secrets** for GitHub Actions workflows
3. **Rotate database passwords** regularly
4. **Use read-only connections** when write access isn't needed
5. **Implement connection pooling** for high-frequency tasks
6. **Enable SSL/TLS** for remote database connections

## Troubleshooting

### Migration Errors
```bash
# Check migration status
php artisan migrate:status

# Rollback last migration
php artisan migrate:rollback

# Reset all migrations
php artisan migrate:reset
```

### Connection Issues
```bash
# Test database connection
php artisan tinker
>>> DB::connection()->getPdo();
```

### GitHub Actions Database Issues
- Ensure all secrets are properly configured
- Verify database host is accessible from GitHub Actions runners
- Check firewall rules allow connections from GitHub Actions IP ranges
- Enable SSL if required by your database provider

## Additional Resources

- [Laravel Database Documentation](https://laravel.com/docs/database)
- [Laravel Migrations Documentation](https://laravel.com/docs/migrations)
- [Laravel Eloquent ORM Documentation](https://laravel.com/docs/eloquent)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
