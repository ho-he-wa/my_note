- Table of Content
{:toc}

# PHPの各バージョンでの変化

PHPの各バージョンでの変化について示す。

## PHP 4.4以前

* スーパーグローバル変数 ($_GET, $_POST, $_SESSION, etc.)
* class
* スコープ定義演算子 (`::`) 

## PHP 5.0

* Zend Engine 2。オブジェクト指向構文の刷新。
* 型宣言でクラス/インタフェースを指定可
* `array_combine()` 追加
* `mkdir()` に再起オプション追加
* 定義済みの定数として `PHP_INT_MAX` と `PHP_INT_SIZE` を追加
* `instanceof` 演算子追加。`is_a` 非推奨。
* おそらくこのバージョンから
    * オブジェクトはデフォルトで参照渡し
    * プロパティにアクセス修飾子
    * メソッドにアクセス修飾子
    * abstractクラスとabstractメソッド
    * interface
    * 静的メソッド(クラスメソッド)
        * 補足: 厳密にはPHP 4でも静的メソッドは利用可。`static` キーワードは使えないが [`ClassName::methodName()` で静的メソッドを参照できる](https://php-legacy-docs.zend.com/manual/php4/en/keyword.paamayim-nekudotayim)。
    * 静的変数(クラス変数)
    * マジックメソッド `__set()` 、`__get()` (アクセス不能なプロパティ)
    * マジックメソッド `__call()` (アクセス不能なメソッド)
    * マジックメソッド `__construct` (コンストラクタ)
    * 例外処理 `try` `catch`

## PHP 5.1

* 型宣言で `array` を指定可
* インタフェース `Serializable` を追加
* internal classのクラス定数と静的メンバー (internal classが何を指しているか不明。[dded support for class constants and static members for internal classes.](https://www.php.net/ChangeLog-5.php#PHP_5_1)」という記述がある)

> [!NOTE] PHPに内部クラスは無いのでinternal classが何か不明。https://stackoverflow.com/questions/16961085/how-to-tell-if-a-class-is-an-internal-class-or-a-user-class では `internal class (such as DateTime)` という記述あり。

## PHP 5.2

* JSONサポートを標準化 (`json_encode`, `json_decode`)
* `DateTime` クラスを追加
* `DateTimeZone` クラスを追加
* Zip機能

## PHP 5.3

* 名前空間のサポート (`namespace`)
* 三項演算子の短縮形 `?:` 演算子を追加
* クロージャを追加 (無名関数) (≒ラムダ関数)
* `array_replace()` 関数と `array_replace_recursive()` 関数を追加
* `const` を追加。グローバルな定数とクラス定数を `const` で定義可。
* マジックメソッド `__callStatic()` (アクセス不能なクラスメソッド)
* マジックメソッド `__invoke()` (オブジェクトを関数としてコールしようとした際にコールされる)
* `DateInterval` クラスを追加
* `DatePeriod` クラスを追加

## PHP 5.4

* トレイトのサポート (`trait`)
* 配列の短縮構文: `[]`
* 呼び出し可能なタイプヒントを追加
* 型宣言で `callable` を指定可
* クロージャが `$this` をサポート

## PHP 5.5

* 例外処理に於ける `finally` ブロックの導入
* ジェネレータとコルーチンを追加しました。(`yield`)
* `DateTimeImmutable` クラスを追加

## PHP 5.6

* 定数式の構文を追加。定数だけの計算式を `const` で指定できる。 (定数スカラー式)
* 可変長引数関数の構文を追加(`...`)
* 引数アンパックのサポートが追加。引数の展開演算子(`...`)
* 累乗演算子(`**`)
* 定数と関数に対する `use` 文
* `use function` と `use const` を追加
* クラスの `::class` 追加。`ClassName::class` でクラスの完全修飾名を取得できる。

## PHP 7.0

* Zend Engine 3
* 型宣言にスカラー型 (`string`, `int`, `float`, `bool`) を追加
* 戻り値の型宣言を追加
* 無名クラス
* 定数 `PHP_INT_MIN` を追加
* Null合体演算子 (`??`) 演算子を追加
* 宇宙船演算子 (`<=>`) 演算子を追加
* `use` 宣言のグループ化

## PHP 7.1

* 型宣言にnull 許容型を追加
* 型宣言に戻り値の `void` 型を追加
* `list()` におけるキーのサポート (以前は数値添字の配列でのみ動作だった)
* [list()の短縮構文](https://www.php.net/manual/ja/function.list.php#120463) : (ex) `list($a, $b) = {配列}` -> `[$a, $b] = {配列}`
* クラス定数にアクセス修飾子を指定できる
* 文字列操作関数で負の文字列オフセットのサポート

## PHP 7.2

* 型宣言に `object` 型を追加
* オブジェクト引数と戻り値の型宣言
* 抽象メソッドのオーバーライド
* パラメータの型の拡大変換
* 定数 `PHP_FLOAT_MIN` を追加
* 定数 `PHP_FLOAT_MAX` を追加

## PHP 7.3

* ヒアドキュメントとNowdoc構文がより柔軟に
* `list()` での参照と配列の分割代入のサポートが追加
* `array_key_first()` の追加
* `array_key_last()` の追加
* `is_countable()` の追加

## PHP 7.4

* クラスのプロパティが型宣言をサポート
* アロー関数 (`fn (argument_list) => expr`)
* Null合体代入演算子 (`??=`)
* 配列内での値のアンパック。[`...` を使った配列のアンパック](https://www.php.net/manual/ja/language.types.array.php#language.types.array.unpacking)
* 配列要素と文字列オフセットにアクセスするための中括弧構文を廃止(非推奨)

## PHP 8.0

* アトリビュート (`#[Attr]`)
* 名前付き引数
* [match式](http://php.adamharvey.name/manual/ja/control-structures.match.php)
* [コンストラクタ引数のプロパティ昇格機能](http://php.adamharvey.name/manual/ja/language.oop5.decon.php#language.oop5.decon.constructor.promotion)。コンストラクタの引数にアクセス修飾子をつけることでプロパティの宣言と代入を省略できる。
* 型宣言にunion型のサポートが追加 (`|`)
* 型宣言にmixed型のサポートが追加
* 戻り値としてのstatic型
* nullsafe演算 (`?->`)

## PHP 8.1

* 列挙型 `enum`
* `readonly` プロパティ
* 初期化時の `new` 式の許可 (パラメータデフォルト値などで)
* 型宣言に交差型のサポートが追加 (`&`)
* 戻り値の宣言に `never` 型を追加
* クラス定数の `final` 指定 (子クラスで再定義できない)
* 文字列をキーとして持つ配列のアンパック
* [ファイバー](http://php.adamharvey.name/manual/ja/language.fibers.php)をサポート。停止可能な関数。

## PHP 8.2

* `readonly class` (クラスのすべてのインスタンスプロパティが暗黙的に読み取り専用としてマークされる。さらに動的プロパティの作成が防止される)
* `null`, `false`, `true` が独立した型になった
* トレイトでの定数を定義
* 動的プロパティ廃止(非推奨)。 (stdClass で動的なプロパティを使うことはOK。マジックメソッドには影響なし)

## PHP 8.3

* 型付きクラス定数
* `[\Override]` アトリビュート
* `json_validate()` を追加
* クラス定数への動的なアクセス構文 (`C::{$name}`)
* 静的変数の初期化。静的変数を初期化する際に任意の式が使える。


## 参考文献

### 総合
* PHP (プログラミング言語) - 歴史, https://ja.wikipedia.org/wiki/PHP_(%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E8%A8%80%E8%AA%9E)#%E6%AD%B4%E5%8F%B2
* PHP バージョン別 新機能 メモ, https://qiita.com/tsuuuuu_san/items/a5e0abcde7287ba4110c

###  PHP 5.x

* PHP 5 ChangeLog, https://www.php.net/ChangeLog-5.php#PHP_5_0
* PHP 4 から PHP 5.0.x への移行, http://php.adamharvey.name/manual/ja/migration5.php
* PHP Manual (PHP 5), https://php-legacy-docs.zend.com/manual/php5/en/index
* オブジェクト指向言語に生まれ変わるPHP5[前編], https://atmarkit.itmedia.co.jp/fcoding/articles/php5/01/php501a.html
* https://atmarkit.itmedia.co.jp/fcoding/articles/php5/02/php502a.html
