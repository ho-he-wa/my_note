# 値オブジェクト
## 値オブジェクトとは

値オブジェクト(Value Object)は値そのもののオブジェクトです。

ドメイン駆動設計(DDD)の構成要素の1つでもありますが、値オブジェクト単独でも有用なデザインパターンです。

値オブジェクトは次の特徴を持ちます。

- 不変 (イミュータブル)
- 交換可能 (値オブジェクトの中身を変更しない。値オブジェクトを差し替える)
- 等価性で比較 (値オブジェクトが持つ属性が同じであれば同じ)

凝集度を高めていくと自然とこの形になると考えます。リファクタリングを繰り返していると自然発生してることも多いと思います。

データベースの定義域(ドメイン)は値オブジェクトと似た概念です。

値オブジェクトは次のようなメリットがあります。

* 正しくない(データの仕様に反した)オブジェクトの生成を回避できる
* 凝集度が高くなる
  * コードの読みやすくなる
  * コードを再利用しやすくなる
  * 変更範囲が局所化する
 
## プリミティブ型より細かな制約

値オブジェクトは、プリミティブ型より細かな制約を設けることにより、正しくない(データの仕様に反した)オブジェクトの生成を回避できます。

値オブジェクトとプリミティブ型 (`int`, `float`, `string` など) を比べると、プリミティブ型では次の面で劣ります。

* 値が取りうる範囲が広すぎる ... (ex)100点満点のテストの点数で101点
* ありえない計算が可能 ... (ex)金額 × 金額

例えば次のようにコンストラクタで値を検証することで、正しい値の場合だけオブジェクトを生成するようにできます。

テストの点数：

```ts
class ExamScore {
    private value;
    constructor(value: number) {
        if (value < 0 || value > 100) {
            throw new Error('値が0～100ではありません');
        }
        this.value = Math.floor(value);
    }
    // 略
}
```

IPアドレス(v4)：

```ts
class IpV4Address {
    private value;
    constructor(value: string) {
        const regex = /^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/;
        if (!regex.test(value)) {
            throw new Error('xx.xx.xx.xxではありません');
        }
        this.value = value;
    }
    // 略
}
```

メソッドで値オブジェクトをパラメータにとることで、そのメソッドでパラメータの形式に関する検証を行う必要が無くなります。

```ts
function ping(address: IpV4Address) {
    // 略
}
```

## 値オブジェクトの適用

実際にコードを用いて値オブジェクトの適用の見本を示します。

以下に示すコードは実行すると、生年月日を入力して、年齢・飲酒の可否・選挙権の有無を表示します。未来日の生年月日はありえないので例外が発生するようになっています。

### 値オブジェクトなし

次のコードはまだ値オブジェクトを適用していないコードです。

```ts
class DateUtility {
    static calcAge(birthDay: Date, now: Date = new Date()) {
        const diffMs = now.getTime() - birthDay.getTime();
        const wkAge = new Date(diffMs);
        // Calculate the difference in years between the age Date object and the year 1970 (UNIX epoch)
        return Math.abs(wkAge.getUTCFullYear() - 1970);
    }
}

const input = prompt('生年月日(yyyy-mm-dd)を入力してください：', '2000-01-01');
if (!input) {
    throw new Error('生年月日の入力がありません。');
}
const birthDay = new Date(input);
if (birthDay > new Date()) {
    throw new Error('生年月日が未来です。');
}
const age = DateUtility.calcAge(birthDay);
console.log(`今日: ${(new Date()).toLocaleDateString()}`);
console.log(`生年月日: ${birthDay.toLocaleDateString()}`);
console.log(`年齢: ${age}歳`);
if (age >= 20) {
    console.log(`飲酒: 可`);
} else {
    console.log(`飲酒: 不可`);
}
if (age >= 18) {
    console.log(`選挙権: あり`);
} else {
    console.log(`選挙権: なし`);
}
```

### 生年月日の値オブジェクトを追加

ユーティリティクラス `DateUtility` を生年月日の値オブジェクト `BirthDay` にします。

ユーティリティクラスのメソッドは値オブジェクトにすることは簡単です。

とはいえ、今の状態だと `DateUtility.calcAge` の引数の1つがインスタンス変数になっただけです。これだけでは値オブジェクトにするメリットは感じられません。

```ts
class BirthDay {
    private date: Date;
    constructor(date: Date) {
        this.date = date;
    }
    calcAge(now: Date = new Date()) {
        const diffMs = now.getTime() - this.date.getTime();
        const wkAge = new Date(diffMs);
        // Calculate the difference in years between the age Date object and the year 1970 (UNIX epoch)
        return wkAge.getUTCFullYear() - 1970;
    }
}

const input = prompt('生年月日(yyyy-mm-dd)を入力してください：', '2000-01-01');
if (!input) {
    throw new Error('生年月日の入力がありません。');
}
const birthDay = new Date(input);
if (birthDay > new Date()) {
    throw new Error('生年月日が未来です。');
}
const age = new BirthDay(birthDay).calcAge();
console.log(`今日: ${(new Date()).toLocaleDateString()}`);
console.log(`生年月日: ${birthDay.toLocaleDateString()}`);
console.log(`年齢: ${age}歳`);
if (age >= 20) {
    console.log(`飲酒: 可`);
} else {
    console.log(`飲酒: 不可`);
}
if (age >= 18) {
    console.log(`選挙権: あり`);
} else {
    console.log(`選挙権: なし`);
}
```

### 生年月日のバリデーションを生年月日の値オブジェクトに移動

未来日の生年月日の検証を値オブジェクト `BirthDay` に移します。

これで `BirthDay` は正しい値でないとインスタンスが生成されません。

インスタンスは正しい値の時だけ生成されるという考え方は「Always-Valid Domain Model」と呼ばれます。

```ts
class BirthDay {
    private date: Date;
    constructor(date: Date) {
        if (date > new Date()) {
            throw new Error('生年月日が未来です。');
        }
        this.date = date;
    }
    calcAge(now: Date = new Date()) {
        const diffMs = now.getTime() - this.date.getTime();
        const wkAge = new Date(diffMs);
        // Calculate the difference in years between the age Date object and the year 1970 (UNIX epoch)
        return wkAge.getUTCFullYear() - 1970;
    }
}

const input = prompt('生年月日(yyyy-mm-dd)を入力してください：', '2000-01-01');
if (!input) {
    throw new Error('生年月日の入力がありません。');
}
const birthDay = new Date(input);
const age = new BirthDay(birthDay).calcAge();
console.log(`今日: ${(new Date()).toLocaleDateString()}`);
console.log(`生年月日: ${birthDay.toLocaleDateString()}`);
console.log(`年齢: ${age}歳`);
if (age >= 20) {
    console.log(`飲酒: 可`);
} else {
    console.log(`飲酒: 不可`);
}
if (age >= 18) {
    console.log(`選挙権: あり`);
} else {
    console.log(`選挙権: なし`);
}
```

### 年齢の値オブジェクトを追加

年齢の値オブジェクトを追加します。

年齢表示の時の `${age}歳` を `toString()` としてメソッドにします。

これで年齢を表示する時に単位を気にする必要が無くなりました。

```ts
class BirthDay {
    private date: Date;
    constructor(date: Date) {
        if (date > new Date()) {
            throw new Error('生年月日が未来です。');
        }
        this.date = date;
    }
    calcAge(now: Date = new Date()) {
        const diffMs = now.getTime() - this.date.getTime();
        const wkAge = new Date(diffMs);
        // Calculate the difference in years between the age Date object and the year 1970 (UNIX epoch)
        return wkAge.getUTCFullYear() - 1970;
    }
}

class Age {
    private age: number;
    constructor(age: number) {
        this.age = age;
    }
    toString(suffix = '歳') {
        return `${this.age}歳`
    }
    value() {
        return this.age;
    }
}

const input = prompt('生年月日(yyyy-mm-dd)を入力してください：', '2000-01-01');
if (!input) {
    throw new Error('生年月日の入力がありません。');
}
const birthDay = new Date(input);
const age = new Age(new BirthDay(birthDay).calcAge());
console.log(`今日: ${(new Date()).toLocaleDateString()}`);
console.log(`生年月日: ${birthDay.toLocaleDateString()}`);
console.log(`年齢: ${age.toString()}`);
if (age.value() >= 20) {
    console.log(`飲酒: 可`);
} else {
    console.log(`飲酒: 不可`);
}
if (age.value() >= 18) {
    console.log(`選挙権: あり`);
} else {
    console.log(`選挙権: なし`);
}
```

### 飲酒可可否、選挙権有無を年齢の値オブジェクトに移動

飲酒可否の判定と選挙権有無の判定は年齢の値と密接なロジックです。

なので `age.value() >= 20` と `age.value() >= 18` を `Age` のメソッドにします。 

```ts
class BirthDay {
    private date: Date;
    constructor(date: Date) {
        if (date > new Date()) {
            throw new Error('生年月日が未来です。');
        }
        this.date = date;
    }
    calcAge(now: Date = new Date()) {
        const diffMs = now.getTime() - this.date.getTime();
        const wkAge = new Date(diffMs);
        // Calculate the difference in years between the age Date object and the year 1970 (UNIX epoch)
        return wkAge.getUTCFullYear() - 1970;
    }
}

class Age {
    private age: number;
    constructor(age: number) {
        this.age = age;
    }
    toString(suffix = '歳') {
        return `${this.age}歳`
    }
    value() {
        return this.age;
    }
    alcoholAllowed() {
        return this.age >= 20;
    }
    votingAllowed() {
        return this.age >= 18;
    }
}

const input = prompt('生年月日(yyyy-mm-dd)を入力してください：', '2000-01-01');
if (!input) {
    throw new Error('生年月日の入力がありません。');
}
const birthDay = new Date(input);
const age = new Age(new BirthDay(birthDay).calcAge());
console.log(`今日: ${(new Date()).toLocaleDateString()}`);
console.log(`生年月日: ${birthDay.toLocaleDateString()}`);
console.log(`年齢: ${age.toString()}`);
if (age.alcoholAllowed()) {
    console.log(`飲酒: 可`);
} else {
    console.log(`飲酒: 不可`);
}
if (age.votingAllowed()) {
    console.log(`選挙権: あり`);
} else {
    console.log(`選挙権: なし`);
}
```

### 生年月日の値オブジェクトから年齢を数値ではなく値オブジェクトとして返す

現在の `BirthDay.calcAge` は年齢の数値を返しています。

既に年齢の値オブジェクト `Age` があるので、プリミティブな数値を返さなければならない理由は特にありません。

なので値オブジェクトとして返すようにします。合わせてメソッド名も `age` に変更します。

```ts
class BirthDay {
    private date: Date;
    constructor(date: Date) {
        if (date > new Date()) {
            throw new Error('生年月日が未来です。');
        }
        this.date = date;
    }
    age(now: Date = new Date()) {
        const diffMs = now.getTime() - this.date.getTime();
        const wkAge = new Date(diffMs);
        // Calculate the difference in years between the age Date object and the year 1970 (UNIX epoch)
        return new Age(wkAge.getUTCFullYear() - 1970);
    }
}

class Age {
    private age: number;
    constructor(age: number) {
        this.age = age;
    }
    toString(suffix = '歳') {
        return `${this.age}歳`
    }
    value() {
        return this.age;
    }
    alcoholAllowed() {
        return this.age >= 20;
    }
    votingAllowed() {
        return this.age >= 18;
    }
}

const input = prompt('生年月日(yyyy-mm-dd)を入力してください：', '2000-01-01');
if (!input) {
    throw new Error('生年月日の入力がありません。');
}
const birthDay = new Date(input);
const age = new BirthDay(birthDay).age();
console.log(`今日: ${(new Date()).toLocaleDateString()}`);
console.log(`生年月日: ${birthDay.toLocaleDateString()}`);
console.log(`年齢: ${age.toString()}`);
if (age.alcoholAllowed()) {
    console.log(`飲酒: 可`);
} else {
    console.log(`飲酒: 不可`);
}
if (age.votingAllowed()) {
    console.log(`選挙権: あり`);
} else {
    console.log(`選挙権: なし`);
}
```

### Date型と置き換えやすくする

変数 `birthDay` が `Date` 型になっています。これを `BirthDay` 型にします。

`BirthDay` 型にするだけだと `birthDay.toLocaleDateString()` がエラーになります。エラーを回避するため、 `BirthDay` を `Date` に変換する `asDate` を追加します。

> [!NOTE] 今回は変換メソッド `asDate` を加えましたが、`BirthDay` を `Date` の派生クラスにしても良いと思います。

```ts
class BirthDay {
    private date: Date;
    constructor(date: Date) {
        if (date > new Date()) {
            throw new Error('生年月日が未来です。');
        }
        this.date = date;
    }
    age(now: Date = new Date()) {
        const diffMs = now.getTime() - this.date.getTime();
        const wkAge = new Date(diffMs);
        // Calculate the difference in years between the age Date object and the year 1970 (UNIX epoch)
        return new Age(wkAge.getUTCFullYear() - 1970);
    }
    asDate() {
        return new Date(this.date);
    }
}

class Age {
    private age: number;
    constructor(age: number) {
        this.age = age;
    }
    toString(suffix = '歳') {
        return `${this.age}歳`
    }
    value() {
        return this.age;
    }
    alcoholAllowed() {
        return this.age >= 20;
    }
    votingAllowed() {
        return this.age >= 18;
    }
}

const input = prompt('生年月日(yyyy-mm-dd)を入力してください：', '2000-01-01');
if (!input) {
    throw new Error('生年月日の入力がありません。');
}
const birthDay = new BirthDay(new Date(input));
const age = birthDay.age();
console.log(`今日: ${(new Date()).toLocaleDateString()}`);
console.log(`生年月日: ${birthDay.asDate().toLocaleDateString()}`);
console.log(`年齢: ${age.toString()}`);
if (age.alcoholAllowed()) {
    console.log(`飲酒: 可`);
} else {
    console.log(`飲酒: 不可`);
}
if (age.votingAllowed()) {
    console.log(`選挙権: あり`);
} else {
    console.log(`選挙権: なし`);
}
```

### 変更前と変更後

変更前と変更後を比べてみます。

全体のコード量は目に見えて増えました。

ではメインの処理の部分に注目するとどうでしょうか。

変更前：

```ts
const input = prompt('生年月日(yyyy-mm-dd)を入力してください：', '2000-01-01');
if (!input) {
    throw new Error('生年月日の入力がありません。');
}
const birthDay = new Date(input);
if (birthDay > new Date()) {
    throw new Error('生年月日が未来です。');
}
const age = DateUtility.calcAge(birthDay);
console.log(`今日: ${(new Date()).toLocaleDateString()}`);
console.log(`生年月日: ${birthDay.toLocaleDateString()}`);
console.log(`年齢: ${age}歳`);
if (age >= 20) {
    console.log(`飲酒: 可`);
} else {
    console.log(`飲酒: 不可`);
}
if (age >= 18) {
    console.log(`選挙権: あり`);
} else {
    console.log(`選挙権: なし`);
}
```

変更後：

```ts
const input = prompt('生年月日(yyyy-mm-dd)を入力してください：', '2000-01-01');
if (!input) {
    throw new Error('生年月日の入力がありません。');
}
const birthDay = new BirthDay(new Date(input));
const age = birthDay.age();
console.log(`今日: ${(new Date()).toLocaleDateString()}`);
console.log(`生年月日: ${birthDay.asDate().toLocaleDateString()}`);
console.log(`年齢: ${age.toString()}`);
if (age.alcoholAllowed()) {
    console.log(`飲酒: 可`);
} else {
    console.log(`飲酒: 不可`);
}
if (age.votingAllowed()) {
    console.log(`選挙権: あり`);
} else {
    console.log(`選挙権: なし`);
}
```

次のような変化があります。

* (a)未来日の生年月日のバリデーションが無くなった : `BirthDay` クラスに移った
* (b)飲酒可否と選挙権有無の条件式が無くなった : `Age` クラスに移った
* (c)年齢表示の単位が無くなった : `Age` クラスに移った

最初に示したメリットが実現できています。

> * 正しくない(データの仕様に反した)オブジェクトの生成を回避できる
> * 凝集度が高くなる
>   * コードの読みやすくなる
>   * コードを再利用しやすくなる
>   * 変更範囲が局所化する

> [!NOTE] もしも永続化のために値オブジェクトの中身の参照が必要であればアクセサを追加しましょう。 `Age` は、アクセサ `value` があります。

## おわりに

特定の値に対するユーティリティクラスやヘルパークラスを作ろうとしている場合は、値オブジェクトを作れば良いと思いました。
