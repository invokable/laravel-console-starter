# Laravel Console Starter

A starter kit for Laravel console applications.

This starter kit is designed to accelerate the development of command-line applications using the Laravel framework. It provides a streamlined foundation by focusing on console-specific features, offering a clean slate without the typical web-related scaffolding. It's an ideal choice for developers looking to build cron jobs, task runners, data processing scripts, or any other CLI tools that can benefit from Laravel's robust components like its powerful Artisan command structure, task scheduling, and other core utilities, but without the overhead of a full web application setup.

## Key Features
- **Focus on Console Applications:** Streamlined for building CLI tools, removing web-specific overhead.
- **Artisan Command Ready:** Quickly generate and organize your console commands using `php artisan make:command`.
- **Scheduled Tasks with GitHub Actions:** Includes a pre-configured example (`.github/workflows/cron.yml`) for running your commands on a schedule using GitHub Actions.
- **Laravel Framework Power:** Leverage familiar Laravel features like its robust dependency injection container, event system, configuration management, and application testing tools for your console applications.

## Requirements
- PHP ^8.2
- Laravel Framework ^12.0
- Laravel Installer ^5.14

## Installation

```shell
laravel new my-app --using=revolution/laravel-console-starter --no-interaction
```

When asked about "npm install", select **"No"**.

## Usage

### Make a new command

```shell
php artisan make:command Hello --command=hello
```
This will create a new command class in `app/Console/Commands/Hello.php`. The `--command=hello` option sets the invokable name of your command, so you can run it later using `php artisan hello`.

### Task Scheduling in GitHub Actions

[cron.yml](./.github/workflows/cron.yml) is an example of how to run the command in GitHub Actions.
This workflow file demonstrates how to set up a cron-like schedule to execute your Artisan commands automatically. You'll need to customize it with the specific commands you want to run and their desired frequency. Remember to configure repository secrets for any sensitive information your commands might need (e.g., API keys, database credentials).

## Notifications

Laravel's built-in notification system provides a convenient way to send notifications from your console commands. This is particularly useful for:

-   Alerting you when a long-running task completes.
-   Reporting errors or issues encountered during command execution.
-   Sending updates or summaries to email, Slack, or other chat platforms.

To use this feature, you'll typically create a notification class (e.g., using `php artisan make:notification TaskCompleted`) and then send it using the `Notifiable` trait in your User model or via the `Notification` facade. You will need to configure your desired notification channels (like mail, Slack, etc.) in your Laravel application. When configuring notification channels, especially those relying on external services or specific mail drivers, you may need to publish the relevant configuration files if they don't already exist in your `config` directory. You can do this using the following Artisan commands:

```shell
php artisan config:publish mail
php artisan config:publish services
```

The `config/mail.php` file allows you to configure your mailer settings, while `config/services.php` is used to store credentials and settings for various third-party services that Laravel can integrate with for notifications (e.g., Slack, Vonage). For detailed setup and usage, please refer to the official [Laravel Notification documentation](https://laravel.com/docs/notifications).

## Application Ideas

Here are some ideas for applications that can be built using this starter kit:

### モニタリングと分析
- ウェブサイトのアップタイム監視とSlackへのアラート送信
- サーバーリソース使用状況のメール報告
- SSL証明書の有効期限チェックとメール通知
- 競合他社の価格変動追跡とDiscord通知
- APIレスポンスタイムのモニタリングとアラート
- データベースサイズの成長レポート
- ウェブサイトのパフォーマンススコア（Lighthouse）の定期チェック

### 財務とビジネス
- Google AdSenseの収益をメールで送信
- AWSコストをDiscordに通知
- 暗号通貨ポートフォリオの日次更新をTelegramに送信
- 株価アラートをSlackチャンネルに通知
- 請求書支払い期限リマインダーの送信
- 月次経費レポートの生成と送信
- サブスクリプション更新アラート

### データ処理とレポート
- データベースバックアップと完了状態通知
- 古いログファイルのクリーンアップと保存容量レポート
- 異なるAPI間のデータ同期と結果レポート
- CSVデータのインポートと処理結果の通知
- データベースの整合性チェックと問題レポート
- キャッシュクリーンアップと最適化レポート
- 定期的なデータエクスポートとクラウドストレージへのアップロード

### コンテンツとマーケティング
- ウェブサイトの壊れたリンクチェックとレポート
- SEOキーワードランキングのモニタリングと変動通知
- ソーシャルメディアフォロワー数の変動レポート
- ブログ投稿パフォーマンス指標の週次レポート
- コンテンツ公開スケジュールのリマインダー
- RSSフィードから新しいコンテンツを集約して通知
- メールマーケティングキャンペーンの結果レポート

### 開発とDevOps
- GitHubリポジトリの依存関係セキュリティアラート
- コードベースの静的解析レポート
- テストカバレッジレポートの生成と通知
- デプロイ後のアプリケーションヘルスチェック
- 未使用のクラウドリソースの検出と通知
- APIドキュメントの変更検出と通知
- コードベースのTODOコメント集計とリマインダー

### 個人の生産性
- 天気予報の毎朝通知
- カレンダーイベントの日次サマリー
- 習慣トラッキングとリマインダー
- 購読サービスの更新日通知
- 重要な日付や記念日のリマインダー
- 定期的なバックアップリマインダー
- 健康データの集計と傾向レポート

## LICENSE
MIT    
