
# Resultパターン (Result型)
通常、エラー処理には例外（`throw` / `try-catch`）を使うことが一般的です。
例外は処理の失敗を明確に伝え、異常系の対応に向いています。
しかし、すべてを例外で処理するとコードが複雑になったり、ビジネス上の予測可能な失敗（在庫切れやバリデーションエラーなど）を扱いづらいこともあります。

また `null`(または`undefined`) を返す方法もよく見られます。
オプショナルチェーン(`?.` 構文) やnull合体演算子 (??) と相性が良いですが、どんなエラーかは示せません。

そこで、**Resultパターン (Result型)** というデザインパターンがあります。
Result型は、成功時の値か失敗時のエラーを明示的に持つ型で、関数型言語や最近の言語では組み込み型として提供されていることがあります。
例えば、Rust、Swift、KotlinにはResult型があります。意外なところではC++にもResult型 (`std::expected`) があります。

PHPやJavaScript、C#のような言語では標準機能としては無いため自分でResult型を実装する必要がありますが、実装は難しくありません。

---

## ✅ 特徴と利点

* 通常の制御フローでエラーを処理できる
  （`try-catch` を使わず、`if` や `switch` で分岐処理できる）ためコードが見やすくなる
* 例外と違って**無視できてしまう**ことに注意（Resultを返してもチェックしないと無視される）
* ビジネス例外（バリデーション失敗、在庫切れなど）と相性が良い

---

## 🔧 PHPでのResult型実装例

以下のコードはPHPでのResult型の例です。
エラーをエラークラスのインスタンスにしていますが、単純な実装では文字列の場合もあります。
とはいえ大した手間ではないのでエラークラスを使うのをおすすめします。

```php
abstract class ResultError {
    public function __construct(
        public readonly string $message
    ) {}
}

/**
 * @template T
 * @template E of ResultError
 */
class Result {
    private function __construct(
        public readonly bool $success,
        public readonly mixed $value = null,
        public readonly ?ResultError $error = null
    ) {}

    /**
     * 成功時のResultを生成
     * @param T $value
     * @return Result<T, null>
     */
    public static function ok(mixed $value): self {
        return new self(true, $value, null);
    }

    /**
     * 失敗時のResultを生成
     * @param E $error
     * @return Result<null, E>
     */
    public static function fail(ResultError $error): self {
        return new self(false, null, $error);
    }
}
```

---

## 🧪 例：購入処理の実装（PHP）

上記で示したResult型の使用例です。

```php
class OutOfStockError extends ResultError {}
class PaymentFailedError extends ResultError {}

/** dummy */
function isInStock() {
    return false; // 適宜変更
}
/** dummy */
function processPayment(string $itemId) {
    return (object)['success' => true, 'ordNo' => 'ord-' . date('Ymdhis')]; // 適宜変更
}

/**
 * 購入処理を行う
 *
 * @param string $itemId
 * @return Result<array{success: bool, paymentId?: string, message?: string}, ResultError>
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

echo "購入が完了しました。オーダー番号:{$result->value->ordNo}";
```

---

## ⚖️ Result型・例外・nullの比較

| 観点              | Result型               | 例外 (`throw`)            | `null` / `undefined`                  |
| --------------- | --------------------- | ----------------------- | ------------------------------------- |
| **エラーの伝播方法**    | 明示的に返す（関数の戻り値で表現）     | **暗黙的に伝播（catchされるまで）**      | 明示的に返す（戻り値に `null` / `undefined` を使う） |
| **エラーハンドリング方法** | `if` / `switch` で分岐可能 | `try-catch` による例外処理が必要  | `if (!値)` や `??`, `?:`, `?.` で処理            |
| **エラー情報の表現力**   | 高い（型やエラーオブジェクトで詳細に表現） | 非常に高い（例外 + スタックトレース）    | 低い（理由がわからない、nullかどうかしか判断できない）         |
| **バグの混入リスク**    | 中（Resultを無視できてしまう）    | 低（未catchだと即エラーで気づける）    | 高（nullチェック漏れで実行時エラー）                  |
| **可読性・保守性**     | 高（成功・失敗が型で明確に区別できる）   | 中（try-catchが多いと散らばりやすい） | 低〜中（nullチェックが散らばり、意図が読みにくい。ただしnull合体演算子(`??`)やオプショナルチェーン(`?.`)との相性が良い）           |

---

## 🎯 まとめ：適切な使い分け

| シチュエーション                      | 推奨される手法                |
| ----------------------------- | ---------------------- |
| **ビジネス例外（在庫切れ、バリデーション失敗など）** | ✅ Result型              |
| **システム例外（通信失敗、システム障害、関数の使い方誤りなど）**        | ✅ 例外（throw）            |
| **失敗理由が不要で、`??` や `?.` を使った簡潔な処理を行いたい場合**     | ✅ `null` / `undefined` |


## 参考：Result型についての記事

- https://medium.com/@emrecantopaloglu/the-result-pattern-in-net-a-simple-guide-73a8f1b89d73
- https://medium.com/@aseem2372005/the-result-pattern-in-c-a-smarter-way-to-handle-errors-c6dee28a0ef0

