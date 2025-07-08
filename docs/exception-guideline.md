# 例外のガイドライン
## 例外の分類

例外は次の3種類に分類すると整理しやすい。

* システム例外(違反)：事前条件の違反。クラスやメソッドの使い方やデータの誤り。この例外が発生する場合は原則として誤りを修正すべき
    * 例：ArgumentException(.NET)、InvalidArgumentException(PHP)
* システム例外(環境)：実行時におけるプログラム外の環境要因の不確実性 (ネットワーク、外部API、ファイルシステムなど)。発生自体を制御できない。
    * 例：HttpClientException、OutOfMemoryException(.NET)
    * この種の例外の中には、言語によっては例外とは別枠のエラーとして扱われるものもある (例：メモリ不足)
* ビジネス例外：在庫切れや利用制限、支払残高不足、認証・認可エラーのような例外。多くの場合アプリケーション固有の例外をスローする。
    * 例：特になし (アプリケーションごとに定義されるため)

なお、検査例外／非検査例外という分類があるが、これはJavaでしか採用されておらず、例外を考えるうえであまり重要ではない。


## 例外スローの方針

### システム例外(違反)

次の5つでだいたい対応できる。

* メソッドの引数が誤り : 
    * .NET: ArgumentException or その派生
    * PHP: InvalidArgumentException
* 実装していない処理 : 
    * .NET: NotImplementedException
    * PHP: LogicException or 派生のカスタム例外
* サポートしていない処理 : 
    * .NET: NotSupportedException
    * PHP: LogicException or 派生のカスタム例外
* メソッド実行時にオブジェクトの状態が不適切 : 
    * .NET: InvalidOperationException
    * PHP: UnexpectedValueException
* メソッド実行時に想定外の値 : 
    * .NET: .NETにはこれに相当する例外は無いが「{想定外の値}はサポートされていない」と考えればNotSupportedExceptionが妥当
    * PHP: UnexpectedValueException
    * ただし外部データ(プログラム外から得たデータ)由来の値であればプログラム側で誤りを修正できないため呼び出し元でエラー処理が必要

NULL参照やインデックス範囲外例外は、基本的に言語ランタイムやライブラリがスローするものでありアプリケーションコードでスローする必要はない。

### システム例外(環境)

* 基本的に言語ランタイムやライブラリがスローするものでありアプリケーションコードでスローする必要はない
* ただし、利用しているライブラリがエラーを検出しても例外をスローしない場合など、必要に応じてアプリケーション側でスローしてもよい
    * 例：API実行でエラー(ステータス500)のResponseクラスのインスタンスが返ってくる場合

### ビジネス例外

業務ロジックに基づいて、状況に応じたカスタム例外を定義・スローする。
ただし、認証や認可など汎用的な機能については、フレームワークやライブラリであらかじめ定義された例外 (例：`AuthenticationException`) を利用できる場合もある。


* 在庫切れ : カスタム例外(例：OutOfStockException)をスローする
* 利用制限 : カスタム例外(例：UsageRestrictedException)をスローする
* 支払残高不足 : カスタム例外(例：InsufficientBalanceException)をスローする
* 認証エラー : AuthenticationException をスローする
* その他、業務要件に応じた例外を適宜定義

### 例外メッセージの記述指針

* 原因がすぐわかる具体的な情報を含める（例：入力値、状態、識別子など）
    * 具体的で原因が明確なメッセージを書く場合 (例：「The file report-20250101.pdf is not found.」)
    * コンテキストデータをJSON化して含める場合 (例：「The file is not found. - {"filename" : "report-20250101.pdf"}」)
* 例外に持たせるメッセージは多言語化は不要。英語あるいはチーム共通の言語を使用する

### 再スロー

* 他の例外をラップして再スローする場合は元の例外を内部例外として含めるべき (例外のコンストラクタのパラメータで指定可能)
* ラップせずに再スローする場合は再スローの構文 (例：`throw;` )を推奨 (言語に再スローの構文がある場合)

## 例外キャッチの方針

* 基本的にアプリケーションコードではキャッチしない（共通エラー処理で一括ハンドリング)
* リソースの開放が必要であればキャッチして再スロー (finallyがある言語であればfinallyでリソース開放推奨)
* システム例外であれば、共通エラー処理(グローバル例外ハンドラ)でキャッチしてロギング、通知、エラーレスポンス生成を行う
* ビジネス例外であれば、コントローラでキャッチしてロギング、通知、適切なユーザー向けレスポンス生成を行う

## カスタム例外

* システム例外であればカスタム例外を設ける必要はあまりない (基本的に言語、フレームワーク、ライブラリで提供されている例外を使う)
* ビジネス例外であればその状況に応じた例外を定義するべき
* ビジネス例外は、ビジネス例外の大元となる例外クラス(例：「BusinessException」)を定義し、個々のビジネス例外はそのクラスを継承するとよい

## 例外の代替
### アサーションによる代替

* 対象: システム例外(違反)のうち関数・メソッドの使い方誤り
* `assert()` を用いて事前条件を明示する
* 開発／テスト時のみ有効（リリース時は無効）
* 例外と表明のどちらを使うか迷ったら例外を使えばOK

例：

```php
assert($userId !== null, 'ユーザーIDはnullではありません。');
```

https://qiita.com/SP-ya/items/a97e58aaf44badf91114

### Result型による代替

* 対象: 主にビジネス例外
* 通常の制御フローでエラーを処理できる (`try-catch` を使わず、`if` や `switch` で分岐処理できる) ためコードが見やすくなる
* 例外と違って無視できてしまうことに注意
* ビジネス例外と相性が良い

例(詳細省略)：

```php

class OutOfStockError extends AppError {}
class PaymentFailedError extends AppError {}

/**
 * 購入処理を行う
 */
function purchaseItem(string $itemId): Result {
    if (!isInStock($itemId)) {
        return Result::fail(new OutOfStockError("Item $itemId is out of stock."));
    }
    $paymentResult = processPayment($itemId);
    if (!$paymentResult->success) {
        return Result::fail(new PaymentFailedError("Payment failed for $itemId."));
    }
    return Result::ok($paymentResult);
}

$result = purchaseItem("ABC123");
if (!$result->success) {
    if ($result->error instanceof OutOfStockError) {
        echo "在庫がありません。後ほど再度お試しください。";
    } elseif ($result->error instanceof PaymentFailedError) {
        echo "支払いに失敗しました。別の支払い方法をお試しください。";
    } else {
        echo "予期しないエラーが発生しました。";
    }
    exit;
}
echo "購入が完了しました。購入番号は {$result->value->orderNo} です。";
```




# Appendix：Result型のコード例(全文)

例：

```php

class OutOfStockError extends AppError {}
class PaymentFailedError extends AppError {}

/**
 * 購入処理を行う
 *
 * @param string $itemId
 * @return Result<array{success: bool, paymentId?: string, message?: string}, AppError>
 *  - success: 支払いが成功したかどうか
 *  - paymentId: 支払いID（成功時のみ）
 *  - message: 処理結果メッセージ（失敗時など）
 */
function purchaseItem(string $itemId): Result {
    if (!isInStock($itemId)) {
        return Result::fail(new OutOfStockError("Item $itemId is out of stock."));
    }
    $paymentResult = processPayment($itemId);
    if (!$paymentResult->success) {
        return Result::fail(new PaymentFailedError("Payment failed for $itemId."));
    }
    return Result::ok($paymentResult);
}

$result = purchaseItem("ABC123");
if (!$result->success) {
    if ($result->error instanceof OutOfStockError) {
        echo "在庫がありません。後ほど再度お試しください。";
    } elseif ($result->error instanceof PaymentFailedError) {
        echo "支払いに失敗しました。別の支払い方法をお試しください。";
    } else {
        echo "予期しないエラーが発生しました。";
    }
    exit;
}
echo "購入が完了しました.";
```

Result型およびエラーの共通部分：

```php
/**
 * @template E of AppError
 */
abstract class AppError {
    public function __construct(
        public readonly string $message
    ) {}
}

/**
 * @template T
 * @template E of AppError
 */
class Result {
    /**
     * @param bool $success
     * @param T|null $value
     * @param E|null $error
     */
    private function __construct(
        public readonly bool $success,
        public readonly mixed $value = null,
        public readonly ?AppError $error = null
    ) {}

    /**
     * @param T $value
     * @return Result<T, null>
     */
    public static function ok(mixed $value): self {
        return new self(true, $value, null);
    }

    /**
     * @param E $error
     * @return Result<null, E>
     */
    public static function fail(AppError $error): self {
        return new self(false, null, $error);
    }
}

```
