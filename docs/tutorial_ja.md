# Laravel Console Starter チュートリアル

## はじめに (Introduction)

このドキュメントは、`revolution/laravel-console-starter` を使用して Laravel コンソールアプリケーションを開発するためのチュートリアルです。このスターターキットは、Laravel の強力な機能を活用して、CLI (コマンドラインインターフェース) アプリケーションを迅速に構築するために設計されています。

`revolution/laravel-console-starter` を利用するメリット：

*   **迅速なセットアップ**: 複雑な設定なしに、すぐにコンソールアプリケーションの開発を開始できます。
*   **Laravel のエコシステム**: Laravel の豊富な機能（タスクスケジューリング、データベース、通知など）をコンソールアプリケーションで利用できます。
*   **Artisan コマンド**: Laravel の強力な Artisan コマンドシステムを活用して、独自のコマンドを簡単に作成、管理できます。
*   **テスト容易性**: Laravel のテストフレームワークを使用して、コンソールコマンドのテストを記述できます。

## 前提条件 (Prerequisites)

このチュートリアルを進めるにあたり、以下のソフトウェアがインストールされていることを確認してください。

*   **PHP**: バージョン `^8.2` (またはそれ以降)
*   **Composer**: PHP の依存関係管理ツール ([https://getcomposer.org/](https://getcomposer.org/))
*   **Laravel Installer**: Laravel プロジェクトを簡単に作成するためのツール (`composer global require laravel/installer`)

## インストール (Installation)

新しい Laravel コンソールアプリケーションプロジェクトを作成するには、ターミナルで以下のコマンドを実行します。

```bash
laravel new my-app --using=revolution/laravel-console-starter --no-interaction
```

これにより、`my-app` という名前の新しいディレクトリが作成され、コンソールアプリケーションの基本的な構造がセットアップされます。

インストール後の主なディレクトリ構造：

*   `app/Console/Commands/`: 作成したカスタム Artisan コマンドが配置されます。
*   `config/`: アプリケーションの設定ファイルが格納されます。
*   `.env`: 環境固有の設定を行うためのファイルです。
*   `routes/console.php`: Artisan コマンドを登録するためのファイルです。

## 環境設定 (Environment Configuration)

プロジェクトのインストールプロセス中に、いくつかの初期設定が自動的に行われます。

*   **`.env` ファイル**: `.env.example` ファイルを元に、`.env` ファイルが自動的にコピー・作成されます。このファイルには、データベース接続情報やメール設定など、環境に応じた設定を記述します。
*   **アプリケーションキー**: `php artisan key:generate` コマンドが自動的に実行され、アプリケーションの暗号化に必要なキーが `.env` ファイルの `APP_KEY` に設定されます。

デフォルトでは、以下のようないくつかの設定がされています。

*   **メール設定**: メール送信はログに出力するように設定されています (`MAIL_MAILER=log`)。実際のメール送信が必要な場合は、Mailgun、Postmark、SES などのサービスを設定してください。

## コンソールコマンドの作成 (Creating a Console Command)

新しい Artisan コマンドを作成するには、`make:command` Artisan コマンドを使用します。

```bash
php artisan make:command YourCommandName --command=your:command
```

*   `YourCommandName`: 作成するコマンドのクラス名です（例: `SendDailyReport`）。
*   `your:command`: コマンドを呼び出す際の実際のコマンド名です（例: `report:send-daily`）。

このコマンドを実行すると、`app/Console/Commands/YourCommandName.php` というファイルが生成されます。

以下は、簡単なコマンドの例です。このコマンドは、実行されるとログにメッセージを出力します。

`app/Console/Commands/HelloWorldCommand.php` を以下のように編集します。

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
    protected $signature = 'hello:world'; // コマンドのシグネチャ（呼び出し名）

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = 'Displays a hello world message in the log'; // コマンドの説明

    /**
     * Execute the console command.
     *
     * @return int
     */
    public function handle()
    {
        Log::info('Hello, World from Artisan Command!'); // ログにメッセージを出力
        $this->info('Hello, World message has been logged.'); // コンソールにもメッセージを出力
        return Command::SUCCESS;
    }
}
```

上記の例では：
1.  `make:command HelloWorldCommand --command=hello:world` でコマンドを作成しました。
2.  `$signature` プロパティでコマンドの呼び出し名を `hello:world` と定義しています。
3.  `$description` プロパティでコマンドの短い説明を定義しています。これは `php artisan list` を実行した際に表示されます。
4.  `handle` メソッド内にコマンドのロジックを記述します。ここでは `Log::info()` を使って `storage/logs/laravel.log` にメッセージを書き込み、`$this->info()` でコンソールにメッセージを表示しています。

## コマンドの実行 (Running the Command)

作成したコマンドを実行するには、ターミナルで以下の Artisan コマンドを使用します。

```bash
php artisan your:command
```

例えば、上記の `HelloWorldCommand` を実行するには、以下のコマンドを実行します。

```bash
php artisan hello:world
```

実行後、`storage/logs/laravel.log` ファイルとコンソールに "Hello, World from Artisan Command!" というメッセージが出力されていることを確認できます。

## 次のステップ (Next Steps)

これで、`revolution/laravel-console-starter` を使用して基本的なコンソールコマンドを作成し、実行する方法を学びました。ここからさらに発展させるためのヒントをいくつか紹介します。

*   **GitHub Actions によるタスクスケジューリング**:
    作成したコマンドを定期的に実行したい場合、GitHub Actions を使用して cron のような定期実行を簡単に設定できます。このアプローチにより、従来のサーバーベースの cron ジョブが不要になり、クラウドで定期タスクを実行できます。

    **GitHub Actions でのタスクスケジューリングの設定**

    Laravel Console Starter には、定期的なタスク実行の設定方法を示す事前設定済みの例（`.github/workflows/cron.yml`）が含まれています。以下はこのファイルの詳細な説明です：

    ```yaml
    name: cron                      # ワークフローの名前

    on:
      schedule:
        - cron: '0 0 * * *' #UTC    # スケジュール式（毎日 UTC 午前 0 時に実行）

    jobs:
      cron:
        name: cron                  # ジョブの名前
        runs-on: ubuntu-latest      # 実行環境

        steps:
          - name: Checkout          # ステップ 1: リポジトリのチェックアウト
            uses: actions/checkout@v4

          - name: Setup PHP         # ステップ 2: PHP 環境のセットアップ
            uses: shivammathur/setup-php@v2
            with:
              php-version: 8.4
              coverage: none

          - name: Install Dependencies  # ステップ 3: Composer 依存関係のインストール
            run: composer install --no-dev -q

          - name: Copy Environment File # ステップ 4: 環境のセットアップ
            run: cp .env.example .env

          - name: Generate Application Key # ステップ 5: アプリケーションキーの生成
            run: php artisan key:generate

          - name: Run Command       # ステップ 6: Artisan コマンドの実行
            run: php artisan inspire
    ```

    **独自のコマンド用にワークフローをカスタマイズする**

    このワークフローを独自のコマンド用にカスタマイズするには：

    1. **スケジュールの調整**: `schedule` セクションの `cron` 式を変更して、希望の頻度を設定します。形式は標準的な cron 構文に従います：`分 時 日 月 曜日`。例えば：
       - `'0 0 * * *'`: 毎日 UTC 午前 0 時
       - `'0 */6 * * *'`: 6 時間ごと
       - `'0 0 * * 1'`: 毎週月曜日の午前 0 時

    2. **コマンドの変更**: "Run Command" ステップの `php artisan inspire` を独自のコマンドに置き換えます：
       ```yaml
       - name: Run Command
         run: php artisan your:command
       ```

    3. **複数のコマンドを実行**: 複数のコマンドを実行するには、追加のステップを追加するか、シェル演算子を使用します：
       ```yaml
       - name: Run Commands
         run: |
           php artisan first:command
           php artisan second:command
       ```

    **リポジトリシークレットを使用した機密情報の取り扱い**

    コマンドが API キー、データベース認証情報、サービストークンなどの機密情報にアクセスする必要がある場合は、GitHub リポジトリシークレットを使用する必要があります：

    1. **GitHub にシークレットを保存**:
       - GitHub のリポジトリに移動
       - Settings > Secrets and variables > Actions に移動
       - "New repository secret" をクリック
       - シークレットを追加（例：`API_KEY`、`DATABASE_PASSWORD`）

    2. **ワークフローでシークレットにアクセス**:
       ```yaml
       - name: Run Command with Secrets
         run: php artisan your:command
         env:
           API_KEY: ${{ secrets.API_KEY }}
           DB_PASSWORD: ${{ secrets.DATABASE_PASSWORD }}
       ```

    3. **環境ファイルでシークレットを使用**:
       ```yaml
       - name: Update Environment File
         run: |
           echo "API_KEY=${{ secrets.API_KEY }}" >> .env
           echo "DB_PASSWORD=${{ secrets.DATABASE_PASSWORD }}" >> .env
       ```

    GitHub Actions を使用したタスクスケジューリングにより、サーバーで cron ジョブを維持することなく、定期的なスケジュールで Laravel コンソールコマンドを実行できます。このアプローチは、データ処理、レポート作成、監視、通知の送信などのタスクに特に役立ちます。

*   **通知機能**:
    コマンドの実行結果やエラーを通知したい場合、Laravel の通知システムを利用できます。Laravel の組み込み通知システムは、コンソールコマンドから通知を送信するための便利な方法を提供します。これは特に以下のような場合に役立ちます：

    - 長時間実行されるタスクが完了したときの通知
    - コマンド実行中に発生したエラーや問題の報告
    - メール、Slack、その他のチャットプラットフォームへの更新や概要の送信

    **通知クラスの作成**

    通知クラスを作成するには、`make:notification` Artisan コマンドを使用します：

    ```bash
    php artisan make:notification TaskCompleted
    ```

    これにより、`app/Notifications` ディレクトリに新しい通知クラスが生成されます。このクラスをカスタマイズして、通知に含めたい情報を追加できます。

    **通知チャネルの設定**

    Laravel は、以下のような様々な通知チャネルをすぐに使用できます：
    - メール
    - Slack
    - SMS（Vonage などのサービスを介して）
    - データベース

    これらのチャネルを設定するには、関連する設定ファイルが `config` ディレクトリにまだ存在しない場合、それらを公開する必要があるかもしれません：

    ```bash
    php artisan config:publish mail
    php artisan config:publish services
    ```

    `config/mail.php` ファイルではメーラー設定を構成でき、`config/services.php` は通知用に Laravel が統合できる様々なサードパーティサービスの認証情報と設定を保存するために使用されます。

    **コンソールコマンドからの通知の送信**

    以下は、コンソールコマンドから通知を送信する例です：

    ```php
    <?php

    namespace App\Console\Commands;

    use App\Notifications\TaskCompleted;
    use Illuminate\Console\Command;
    use Illuminate\Support\Facades\Notification;

    class ProcessDataCommand extends Command
    {
        protected $signature = 'data:process';
        protected $description = 'データを処理し、完了時に通知を送信';

        public function handle()
        {
            // コマンドのロジックをここに記述
            $this->info('データを処理中...');
            
            // データ処理...
            
            // 完了時に通知を送信
            Notification::route('mail', 'admin@example.com')
                ->notify(new TaskCompleted('データ処理が正常に完了しました'));
                
            return Command::SUCCESS;
        }
    }
    ```

    詳細なセットアップと使用方法については、公式の [Laravel 通知ドキュメント](https://laravel.com/docs/notifications) を参照してください。

*   **実践的なアプリケーション例**:
    以下の例は、Laravel Console Starter を使用して実用的なアプリケーションを構築する方法を示しています。各例には、詳細な実装手順と、必要なサービスや通知の設定方法が含まれています。

    ### 例 1: Slack アラート付きウェブサイト稼働監視

    この例では、ウェブサイトがオンラインかどうかを確認し、問題が検出された場合に Slack にアラートを送信するコマンドを作成する方法を示します。

    **ステップ 1: コマンドの作成**

    ```bash
    php artisan make:command MonitorWebsites --command=monitor:websites
    ```

    **ステップ 2: 通知の作成**

    ```bash
    php artisan make:notification WebsiteDown
    ```

    **ステップ 3: Slack の設定**

    まず、services 設定を公開します：

    ```bash
    php artisan config:publish services
    ```

    次に、Slack webhook URL を `.env` ファイルに追加します：

    ```
    SLACK_WEBHOOK_URL=https://hooks.slack.com/services/YOUR/WEBHOOK/URL
    ```

    そして、`config/services.php` ファイルを更新します：

    ```php
    'slack' => [
        'webhook_url' => env('SLACK_WEBHOOK_URL'),
    ],
    ```

    **ステップ 4: 通知の実装**

    `app/Notifications/WebsiteDown.php` を編集します：

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
                ->content('ウェブサイトダウンアラート！')
                ->attachment(function ($attachment) {
                    $attachment->title($this->website)
                               ->content("エラー: {$this->error}")
                               ->timestamp(now());
                });
        }
    }
    ```

    **ステップ 5: コマンドの実装**

    `app/Console/Commands/MonitorWebsites.php` を編集します：

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
        protected $description = 'ウェブサイトがオンラインかどうかを確認し、ダウンしている場合はアラートを送信';

        protected $websites = [
            'https://example.com',
            'https://yourwebsite.com',
            // 必要に応じてウェブサイトを追加
        ];

        public function handle()
        {
            $timeout = $this->option('timeout');
            $this->info("チェック中: " . count($this->websites) . " ウェブサイト (タイムアウト: {$timeout}秒)...");
            
            foreach ($this->websites as $website) {
                $this->info("{$website} をチェック中...");
                
                try {
                    $response = Http::timeout($timeout)->get($website);
                    
                    if ($response->successful()) {
                        $this->info("{$website} は正常に稼働中です！");
                        Log::info("ウェブサイト {$website} は稼働中", ['status' => $response->status()]);
                    } else {
                        $this->error("{$website} はステータスコード " . $response->status() . " を返しました");
                        $this->sendAlert($website, "HTTP ステータスコード: " . $response->status());
                    }
                } catch (\Exception $e) {
                    $this->error("{$website} はダウンしています: " . $e->getMessage());
                    $this->sendAlert($website, $e->getMessage());
                }
            }
            
            return Command::SUCCESS;
        }
        
        protected function sendAlert($website, $error)
        {
            $this->info("{$website} のアラートを送信中...");
            Log::error("ウェブサイト {$website} はダウンしています", ['error' => $error]);
            
            // Slack に通知を送信
            Notification::route('slack', config('services.slack.webhook_url'))
                ->notify(new WebsiteDown($website, $error));
        }
    }
    ```

    **ステップ 6: GitHub Actions でのスケジュール設定**

    `.github/workflows/cron.yml` ファイルを更新して、コマンドを毎時実行するようにします：

    ```yaml
    name: Website Monitoring

    on:
      schedule:
        - cron: '0 * * * *' # 毎時実行

    jobs:
      monitor:
        runs-on: ubuntu-latest
        
        steps:
          # ... 標準的なセットアップステップ ...
          
          - name: Monitor Websites
            run: php artisan monitor:websites --timeout=30
            env:
              SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
    ```

    ### 例 2: Telegram への日次暗号資産ポートフォリオ更新

    この例では、暗号資産の価格を取得し、日次ポートフォリオの概要を Telegram に送信するコマンドを作成する方法を示します。

    **ステップ 1: コマンドの作成**

    ```bash
    php artisan make:command CryptoPortfolio --command=crypto:portfolio
    ```

    **ステップ 2: 通知の作成**

    ```bash
    php artisan make:notification CryptoPortfolioUpdate
    ```

    **ステップ 3: Telegram の設定**

    まず、services 設定を公開します：

    ```bash
    php artisan config:publish services
    ```

    次に、Telegram ボットトークンとチャット ID を `.env` ファイルに追加します：

    ```
    TELEGRAM_BOT_TOKEN=your_bot_token
    TELEGRAM_CHAT_ID=your_chat_id
    ```

    そして、`config/services.php` ファイルを更新します：

    ```php
    'telegram-bot-api' => [
        'token' => env('TELEGRAM_BOT_TOKEN'),
    ],
    ```

    **ステップ 4: Telegram 通知チャネルのインストール**

    ```bash
    composer require laravel-notification-channels/telegram
    ```

    **ステップ 5: 通知の実装**

    `app/Notifications/CryptoPortfolioUpdate.php` を編集します：

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
            $message = "💰 *日次暗号資産ポートフォリオ更新* 💰\n\n";
            $message .= "ポートフォリオ総額: $" . number_format($this->totalValue, 2) . "\n\n";
            
            foreach ($this->portfolio as $coin) {
                $change = $coin['change_24h'] > 0 ? "↗️ +" : "↘️ ";
                $message .= "*{$coin['symbol']}*: $" . number_format($coin['price'], 2) . " ({$change}{$coin['change_24h']}%)\n";
                $message .= "保有量: {$coin['amount']} ({$coin['value']})\n\n";
            }
            
            return TelegramMessage::create()
                ->content($message);
        }
    }
    ```

    **ステップ 6: コマンドの実装**

    `app/Console/Commands/CryptoPortfolio.php` を編集します：

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
        protected $description = '暗号資産の価格を取得し、ポートフォリオ更新を送信';

        // ここでポートフォリオを定義
        protected $holdings = [
            'BTC' => 0.5,
            'ETH' => 5,
            'SOL' => 20,
            // 必要に応じてコインを追加
        ];

        public function handle()
        {
            $this->info('暗号資産の価格を取得中...');
            
            try {
                // CoinGecko API を使用（無料枠）
                $response = Http::get('https://api.coingecko.com/api/v3/coins/markets', [
                    'vs_currency' => 'usd',
                    'ids' => 'bitcoin,ethereum,solana', // 保有コインと一致させる
                    'order' => 'market_cap_desc',
                    'per_page' => 100,
                    'page' => 1,
                ]);
                
                if (!$response->successful()) {
                    throw new \Exception('API リクエストが失敗しました: ' . $response->status());
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
                
                $this->info('ポートフォリオ総額: $' . number_format($totalValue, 2));
                
                // 通知を送信
                $chatId = env('TELEGRAM_CHAT_ID');
                Notification::route('telegram', $chatId)
                    ->notify(new CryptoPortfolioUpdate($portfolio, $totalValue));
                
                $this->info('ポートフォリオ更新が Telegram に送信されました！');
                
            } catch (\Exception $e) {
                $this->error('エラー: ' . $e->getMessage());
                Log::error('暗号資産ポートフォリオの更新に失敗しました', ['error' => $e->getMessage()]);
                return Command::FAILURE;
            }
            
            return Command::SUCCESS;
        }
    }
    ```

    **ステップ 7: GitHub Actions でのスケジュール設定**

    `.github/workflows/cron.yml` ファイルを更新して、コマンドを毎日実行するようにします：

    ```yaml
    name: Crypto Portfolio Update

    on:
      schedule:
        - cron: '0 8 * * *' # 毎日 UTC 午前 8 時に実行

    jobs:
      crypto-update:
        runs-on: ubuntu-latest
        
        steps:
          # ... 標準的なセットアップステップ ...
          
          - name: Update Crypto Portfolio
            run: php artisan crypto:portfolio
            env:
              TELEGRAM_BOT_TOKEN: ${{ secrets.TELEGRAM_BOT_TOKEN }}
              TELEGRAM_CHAT_ID: ${{ secrets.TELEGRAM_CHAT_ID }}
    ```

    ### 例 3: データベースバックアップと通知

    この例では、データベースバックアップを実行し、完了ステータスの通知を送信するコマンドを作成する方法を示します。

    **ステップ 1: コマンドの作成**

    ```bash
    php artisan make:command DatabaseBackup --command=db:backup
    ```

    **ステップ 2: 通知の作成**

    ```bash
    php artisan make:notification BackupCompleted
    ```

    **ステップ 3: メールの設定**

    まず、mail 設定を公開します：

    ```bash
    php artisan config:publish mail
    ```

    次に、`.env` ファイルをメール設定で更新します：

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

    **ステップ 4: 通知の実装**

    `app/Notifications/BackupCompleted.php` を編集します：

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
                ->subject($this->success ? 'データベースバックアップが正常に完了しました' : 'データベースバックアップが失敗しました');
            
            if ($this->success) {
                $message->line('データベースバックアップが正常に完了しました。')
                        ->line('バックアップファイル: ' . $this->filename)
                        ->line('サイズ: ' . $this->formatBytes($this->size))
                        ->line('日時: ' . now()->format('Y-m-d H:i:s'))
                        ->success();
            } else {
                $message->line('データベースバックアップが失敗しました。')
                        ->line('エラー: ' . $this->error)
                        ->line('日時: ' . now()->format('Y-m-d H:i:s'))
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

    **ステップ 5: コマンドの実装**

    `app/Console/Commands/DatabaseBackup.php` を編集します：

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
        protected $description = 'データベースバックアップを作成し、通知を送信';

        public function handle()
        {
            $database = $this->option('database');
            $email = $this->option('email');
            
            $this->info("{$database} データベースのバックアップを開始...");
            
            // バックアップディレクトリが存在しない場合は作成
            $backupPath = storage_path('app/backups');
            if (!file_exists($backupPath)) {
                mkdir($backupPath, 0755, true);
            }
            
            // バックアップファイル名の生成
            $filename = $database . '_' . date('Y-m-d_H-i-s') . '.sql';
            $filepath = $backupPath . '/' . $filename;
            
            try {
                // データベース設定の取得
                $host = config("database.connections.{$database}.host");
                $port = config("database.connections.{$database}.port");
                $dbName = config("database.connections.{$database}.database");
                $username = config("database.connections.{$database}.username");
                $password = config("database.connections.{$database}.password");
                
                // mysqldump コマンドの構築
                $command = "mysqldump --host={$host} --port={$port} --user={$username} --password={$password} {$dbName} > {$filepath}";
                
                // コマンドの実行
                $process = Process::fromShellCommandline($command);
                $process->setTimeout(3600); // 1 時間のタイムアウト
                $process->run();
                
                if (!$process->isSuccessful()) {
                    throw new \Exception($process->getErrorOutput());
                }
                
                // バックアップファイルが存在するか確認し、サイズを取得
                if (!file_exists($filepath)) {
                    throw new \Exception("バックアップファイルが作成されませんでした");
                }
                
                $size = filesize($filepath);
                
                $this->info("バックアップが正常に完了しました: {$filepath} (" . $this->formatBytes($size) . ")");
                Log::info("データベースバックアップが完了しました", ['file' => $filename, 'size' => $size]);
                
                // 成功通知の送信
                Notification::route('mail', $email)
                    ->notify(new BackupCompleted(true, $filename, $size));
                
                return Command::SUCCESS;
                
            } catch (\Exception $e) {
                $this->error("バックアップが失敗しました: " . $e->getMessage());
                Log::error("データベースバックアップが失敗しました", ['error' => $e->getMessage()]);
                
                // 失敗通知の送信
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

    **ステップ 6: GitHub Actions でのスケジュール設定**

    `.github/workflows/cron.yml` ファイルを更新して、コマンドを毎週実行するようにします：

    ```yaml
    name: Database Backup

    on:
      schedule:
        - cron: '0 0 * * 0' # 毎週日曜日の UTC 午前 0 時に実行

    jobs:
      backup:
        runs-on: ubuntu-latest
        
        steps:
          # ... 標準的なセットアップステップ ...
          
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

    これらの例は、Laravel Console Starter を使用して実用的なアプリケーションを構築する方法を示しています。これらの例を応用・組み合わせて、独自のカスタムソリューションを作成することができます。

Laravel の豊富なドキュメント ([https://laravel.com/docs](https://laravel.com/docs)) も、開発を進める上で非常に役立ちます。
ハッピーコーディング！
