# Resultパターン (Result型)

通常、エラー処理には例外（`throw` / `try-catch`）が使われる。例外は処理の失敗を明確に伝える手段として有効で、システム異常や予期しないバグの対応に向いている。

ただし、すべてを例外で処理するとコードが複雑になる。特に、在庫切れやバリデーションエラーなどビジネス上予測可能な失敗に対しては、例外よりも別の表現の方が扱いやすいことがある。

もう一つの選択肢として `null`（または `undefined`）を返す方法もある。オプショナルチェーン（`?.`）や null 合体演算子（`??`）と相性は良いが、どんなエラーが発生したのかは示せない。

このような課題を解決するために、「Resultパターン（Result型）」がある。

Result型は、成功時の値または失敗時のエラーを明示的に持つ型。関数型言語や近年の言語では標準で組み込まれていることが多い。たとえば、Rust、Swift、Kotlin、C++（`std::expected`）などが対応している。

一方で、PHPやJavaScript、C#など一部の言語では標準では存在しないため、自前で実装する必要がある。とはいえ実装はシンプル。

---

## ✅ 特徴と利点

* `try-catch` を使わず、通常の制御フロー（`if` / `switch`）でエラー処理ができる
* 例外とは異なり、Resultを返しても無視できてしまう点には注意
* バリデーション失敗や在庫切れなど、ビジネス例外との相性が良い

---

## 🔧 PHPでのResult型実装例

以下はPHPでのResult型の簡易実装。
エラーをエラークラスのインスタンスにしているが、単純な実装では文字列の場合もある。
とはいえ大した手間ではないのでエラークラスを使うのをおすすめする。

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

上記で示したResult型の使用例。

```php
class OutOfStockError extends ResultError {}
class PaymentFailedError extends ResultError {}

function isInStock(string $itemId): bool {
    return false; // ダミー実装。適宜変更
}

function processPayment(string $itemId): object {
    return (object)['success' => true, 'ordNo' => 'ord-' . date('YmdHis')]; // ダミー実装。適宜変更
}

/**
 * 購入処理を行う
 *
 * @param string $itemId
 * @return Result<array{success:bool,ordNo?:string}, ResultError>
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

| 観点        | Result型               | 例外 (`throw`)         | `null` / `undefined`         |
| --------- | --------------------- | -------------------- | ---------------------------- |
| エラーの伝播方法  | 明示的に戻り値で伝播            | 暗黙的に伝播（catchされるまで）   | 明示的に返す（戻り値に `null` を使う）      |
| ハンドリング方法  | `if` / `switch` で分岐可能 | `try-catch` が必要      | `if (!値)` や `??`, `?.` などで処理 |
| エラー情報の表現力 | 中〜高（型やエラーオブジェクト）      | 高（例外 + スタックトレース）     | 低（nullかどうかしかわからない）           |
| バグ混入リスク   | 中（Resultを無視できる）       | 低（未catch時に即エラー）      | 高（nullチェック漏れで実行時エラー）         |
| 可読性・保守性   | 高（成功・失敗が型で明確に分かる）     | 中（try-catchが散らばりやすい） | 低〜中（nullチェックが散らばり、意図が読みにくい。ただしnull合体演算子(`??`)やオプショナルチェーン(`?.`)との相性が良い）           |

---

## 🎯 適切な使い分け

| シチュエーション                         | 推奨手法             |
| -------------------------------- | ---------------- |
| ビジネス例外（在庫切れ、バリデーションエラーなど）        | Result型          |
| システム例外（通信失敗、関数の使い方誤りなどのバグ、予期しない状態）          | 例外（throw）        |
| 単に存在チェックだけしたい・`??` や `?.` で十分な場面 | null / undefined |

---

## 参考：Result型についての記事

- https://medium.com/@emrecantopaloglu/the-result-pattern-in-net-a-simple-guide-73a8f1b89d73
- https://medium.com/@aseem2372005/the-result-pattern-in-c-a-smarter-way-to-handle-errors-c6dee28a0ef0
