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

*   **タスクスケジューリング (GitHub Actions)**:
    作成したコマンドを定期的に実行したい場合、Laravel の強力なタスクスケジューラと GitHub Actions を組み合わせることで、cron のような定期実行を簡単に設定できます。詳細については、プロジェクトの `README.md` の「Task Scheduling with GitHub Actions」セクションを参照してください。

*   **通知機能**:
    コマンドの実行結果やエラーを通知したい場合、Laravel の通知システムを利用できます。メール、Slack、SMS など、さまざまなチャネルを通じて通知を送信する方法については、`README.md` の「Notifications」セクションや Laravel の公式ドキュメントを参照してください。

*   **さらなるアイデア**:
    `README.md` には、このスターターキットを使用して構築できるアプリケーションのアイデアがいくつか記載されています（例: データ処理バッチ、API クライアント、システム管理スクリプトなど）。これらを参考に、独自のコンソールアプリケーション開発に挑戦してみてください。

Laravel の豊富なドキュメント ([https://laravel.com/docs](https://laravel.com/docs)) も、開発を進める上で非常に役立ちます。
ハッピーコーディング！
