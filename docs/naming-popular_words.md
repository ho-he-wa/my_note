# メソッド名で使う定番の単語

* Table of Content  
  {\:toc}

---

メソッド名で使う定番の単語。定番すぎて書くまでもないものから類義語の使い分けで悩むものまで。

## 接頭辞（Prefix）

### 状態・可能性・意図（boolean を返すもの）

* is : 状態を示す  
  * 例 : `user.isEnabled()`, `form.isValid()`
* has : 所有・存在を示す  
  * 例 : `node.hasChildren()`, `response.hasError()`
* can : 実行可能か(能力や条件を満たしているか)を示す  
  * 例 : `command.canExecute()`, `file.canAccessFile()`
* may : 操作が許可されているかを示す  
  * 例 : `session.mayAccessResource()`
* will : 将来的に**そうなる意図・予定**を示す  
  * 例 : `user.willSendEmail()`, `connection.willCloseSoon()`
* should : **推奨される・あるべき状態**を示す  
  * 例 : `request.shouldRetry()`, `ui.shouldShowWarning()`
* must : **必須であること**を示す（制約の強調）  
  * 例 : `user.mustBeLoggedIn()`, `user.mustChangePassword()`
* needs : **必要があるか**を示す  
  * 例 : `session.needsRefresh()`, `client.needsAuthToken()`

---

### データ参照関連

* get : 値を取得する。参照用アクセサとしてよく使われる。  
  * 例 : `user.getName()`
* find : 条件に合う1件のデータ取得 (実装によっては、識別子を使った検索や、条件に合うすべてを取得するケースもある)
  * 例 : `repo.findUserById()`
* findAll : 条件に合う全てのデータ取得  
  * 例 : `repo.findAllUsers()`
* retrieve : 保管されたデータをidやトークンのような識別子を用いて取得
  * 例 : `auth.retrieveSession()`
* fetch : 外部から取得する（API等）  
  * 例 : `api.fetchData()`
* load : データの読み込み  
  * 例 : `app.loadConfig()`
* read : ファイルやストリームの読み取り  
  * 例 : `file.readFile()`

---

### データ更新関連

* set : 値を設定する。変更用アクセサとしてよく使われる。  
  * 例 : `user.setAge()`
* add : 追加する  
  * 例 : `cart.addItem()`
* append : 末尾に追加する  
  * 例 : `logger.appendLog()`
* insert : 指定位置に挿入  
  * 例 : `list.insertRecord()`
* delete / remove : データを削除する  
  * 例 : `cart.removeItem()`
* update : データを更新する  
  * 例 : `user.updateProfile()`
* mark : 状態に印をつける  
  * 例 : `message.markAsRead()`
* toggle : フラグの切り替えを行う  
  * 例 : `section.toggleVisibility()`

---

### 初期化・リセット

* clear : 全て削除  
  * 例 : `cache.clearCache()`
* cleanup : 全て削除。片付けや掃除のニュアンスが強い。
  * 例 : `workingDirectory.cleanup()`
* reset : 状態を初期に戻す  
  * 例 : `form.reset()`
* init / initialize : 初期化して使用可能な状態にする。
  * 例 : `app.initLogger()`

---

### 生成・変換関連

* with : 指定値を変更した新しいインスタンスを返す。 **イミュータブルな set**。  
  * 例 : `user.withName("Alice")`
* create : 新規作成  
  * 例 : `userFactory.create()`
* gen(generate) : 動的生成  
  * 例 : `auth.generateToken()`
* build : 複雑な文字列やオブジェクトを生成する。主に Builder パターンで使われる
  * 例 : `sqlBuilder.build()`, `requestBuilder.build()`
* from : 他形式からの生成。変換。of より複雑。  
  * 例 : `User.fromJson(json)`
* of : 特定データからの生成（Factory 的）。from よりも単純。  ※fromとofの間に厳密な境界があるわけではない
  * 例 : `User.ofList(list)`
* parse : 文字列を構造化データへ変換  
  * 例 : `DateParser.parse("2025-01-01")`
* hydrate : 標準オブジェクト(object)やその配列からオブジェクトを生成する。  
  * 例 : `User.hydrate(obj)`, `User.hydrate(objArray)`
* as : 型変換やラップ。見方を変更。キャスト。  
  * 例 : `token.asString()`
* to : 他形式へ変換。実体（データ）の変換。  
  * 例 : `user.toMap()`
* clone / copy : 複製処理  
  * 例 : `user.clone()`, `list.copy()`

---

### 検証

* validate : データが期待される形式・ルールに合っているかを検証する  
  * 例 : `form.validateInput()`, `form.validateForm()`
* verify : 有効性や故障がないことを検証する  
  * 例 : `document.verifySignature()`

---

### 例外の制御

* try : 処理に失敗する可能性があるが、例外をスローしない。成功/失敗は戻り値（boolean や Result 型など）で返す。  
  * 例 : `parser.tryParse(text)`

---

## 接尾辞（Suffix）

### 代替値・代替処理

* orNull : 代替値としてnullを返す 
  * 例 : `user.getJobOrNull()`
* orElse : 代替値として指定の値を返す  
  * 例 : `user.getNameOrElse("default")`
* orDefault : 代替値としてデフォルト値を返す  
  * 例 : `app.loadConfigOrDefault()`
* orFail / orThrow : 失敗時に例外などを返すことを明示  
  * 例 : `repo.findUserOrFail()`
* or : 2つの処理のどちらか一方を行う。`aaOrBb` の形で「aa をする。該当しなければ bb をする」
  * 例 : `user.updateOrCreate()`

### 組合せ処理

* and : 2つの処理の両方を行う。`aaAndBb` の形で「aa と bb を両方する」
  * 例 : `form.validateAndSave()`

### コレクション操作・繰り返し処理

* each / forEach : 各要素への処理  
  * 例 : `list.resetEach()`
* map : 各要素を別の値に変換する  
  * 例 : `list.mapToNames()`
* filter : 条件を満たす要素のみを抽出する  
  * 例 : `users.filterActive()`
* reduce : コレクションを畳み込む・集約する。合計や最大値などの集計処理に使われる。
  * 例 : `orders.reduceTotalAmount()`, `scores.reduceMax()`

### コンテキスト指定・条件付与

* if : 条件付きで処理を行う。`aaIfXxxx` の形で「もし Xxxx であれば aa する」
  * 例 : `app.initializeIfNeeded()`, `callback.executeIfPresent()`, `form.saveIfRequired()`
* by : 条件やアルゴリズム指定  
  * 例 : `items.sortByDate()`, `dom.getElementById()`
* using : 特定リソースやコンテキストを使う  
  * 例 : `handler.executeUsingSession()`

### 非同期

* async : 非同期処理  
  * 例 : `repo.loadDataAsync()`

