- Table of Content
{:toc}

# Laravel のディレクトリ構造

Laravelの各ディレクトリは公式のディレクトリ情報として説明が示されている。

- https://laravel.com/docs/10.x/structure
- https://readouble.com/laravel/10.x/ja/structure.html

ただし、これら以外でもサンプルコードの中で出現するディレクトリがある。それらも含めてディレクトリをまとめてみた。

例えばServiceディレクトリは任意に配置できるディレクトリである。しかし同じようにサービスクラスを作成するのであれば、公式のサンプルコードで示されている位置は真似すると分かりやすくなると考える。


## appディレクトリ

* app
  * Broadcasting アプリケーションの全ブロードキャストチャンネルクラス
  * Cast カスタムキャスト。https://readouble.com/laravel/10.x/ja/eloquent-mutators.html
  * Console すべてのArtisanコマンドを格納
    * Commands
  * Enums 列挙体。https://readouble.com/laravel/10.x/ja/routing.html#implicit-enum-binding , https://readouble.com/laravel/10.x/ja/validation.html (*1)
  * Events イベントクラス
  * Exceptions アプリケーションのカスタム例外
  * Features Laravel Pennantの機能クラス。https://readouble.com/laravel/10.x/ja/pennant.html
  * Http コントローラ、ミドルウェア、リクエストを格納
    * Controllers
    * Middleware
    * Requests
  * Jobs アプリケーションのキュー投入可能なジョブ
  * Listeners eventsイベントを処理するクラス
  * Mail アプリケーションから送信されるすべてのメールを表すクラス
  * Models すべてのEloquentモデルクラス
  * Notifications アプリケーションが送信するすべての「トランザクション」的な通知
  * Policies アプリケーションの認可ポリシークラス。ポリシーは、ユーザーがリソースに対して特定のアクションを実行できるかどうかを判断するために使用される。
  * Providers アプリケーションのサービスプロバイダ
  * Repositories リポジトリ。https://readouble.com/laravel/10.x/ja/container.html (*1)
  * Rules アプリケーションが使用するバリデーションルールオブジェクト
  * Services サービス。https://readouble.com/laravel/10.x/ja/container.html (*1)  ベストプラクティス https://github.com/alexeymezenin/laravel-best-practices/blob/master/japanese.md でも記述あり。
  * Validation 追加バリデーションのクラス。https://readouble.com/laravel/10.x/ja/validation.html  (*1)
  * ValueObjects 値オブジェクト。https://readouble.com/laravel/10.x/ja/eloquent-mutators.html  (*1)
  * View
    * Components ビューコンポーネント。https://readouble.com/laravel/10.x/ja/blade.html  (*1)
    * Composers ビューコンポーザ。https://readouble.com/laravel/10.x/ja/views.html#view-composers  (*1)
    * Creators ビュークリエータ。https://readouble.com/laravel/10.x/ja/views.html#view-composers (*1)

(*1) ... サンプルコードで利用あり。任意のディレクトリ配置であるが特に理由がなければ真似るほうが無難であろうディレクトリ


サンプルコード内にあるが特に真似する必要がなさそうなディレクトリ：

* app ※この配下のディレクトリ名は先頭大文字
  * Administration https://readouble.com/laravel/10.x/ja/eloquent-factories.html
  * Domain https://readouble.com/laravel/11.x/ja/artisan.html
  * Support https://readouble.com/laravel/10.x/ja/artisan.html

これらのディレクトリはサンプルコード内で出現するが特に説明もなく、また出現頻度も少ない。そのため真似する必要は特にないと考える。

## app以外のディレクトリ

app以外のディレクトリはおおよそ公式のディレクトリ情報で示されているとおりである。

- https://laravel.com/docs/10.x/structure
- https://readouble.com/laravel/10.x/ja/structure.html
- 
* bootstrap フレームワークの初期起動処理
  * cache キャッシュファイル
* config アプリケーションの設定ファイル
* database
  * factories Eloquentモデルファクトリ。https://readouble.com/laravel/10.x/ja/eloquent-factories.html
  * migrations
  * seeders
* public
* resources 、ビューや、CSS、JavaScriptなど
  * css
  * js
  * views
* routes アプリケーションのすべてのルート定義
* storage
  * app アプリケーションが作成したファイルを保存
    * public 一般にアクセス可能である必要のあるユーザー生成ファイルを保存するために使用。このディレクトリを指すシンボリックリンクをpublic/storageに作成する必要があり。
  * framework フレームワークが作成したファイルとキャッシュを保存
  * logs アプリケーションのログファイルを保存
* tests 自動テスト。https://readouble.com/laravel/10.x/ja/testing.html
  * Features 機能テスト
  * Units 単体テスト
* vendor Composerによる依存パッケージ
* lang 言語ファイル





