- Table of Content
{:toc}

# 論理削除レコードの悩み
## 論理削除

論理削除したレコードで次のような悩みが湧くことがあります。

1. 🐌 「削除」したレコードなのにユニーク制約として影響力を持っている 💦
2. 🐝 ユニーク制約が期待通り機能するように「削除」したレコードの値をnullに書き換えればよい 🤩
3. 🐌 けど「削除」したレコードにある値を削除した時の状態から変更するのは気持ち悪い 😨
4. 🐝 削除する時に値をnullに書き換えればよい 🤩
5. 🐌 レコードの削除(論理削除)だけでなく別に値の削除するのは妙な感じがする 😨
6. 🐝 うーん。
7. 🐌 論理削除はもしもの時の復活を想定している。仮にレコードを復活させる時にnullにしたカラムの値が復活しない 😨
8. 🐝 ユニーク制約やめればいい 🤩
9. 🐌 データベースの役割として制約の削除はなるべく避けたい 😨

「いや気にせず書き換えればいい」、「ユニーク制約つけなければよいのでは？」と考える人もいるでしょうが、ひとまず私は悩んでいます。

なんとなく、データベースはデータの整合性を守るものである、という考えがあるからかもしれません。


## 無効

では、「論理削除」ではなくデータが「有効ではない/利用できない」状態を表す言葉を考えてみます。

例えば無効(disabled)という言葉が思い当たります。

前述の論理削除の悩みを「論理削除」ではなく「無効」に置き換えると次のようになります。

1. 🐌 「無効」にしたレコードなのにユニーク制約として影響力を持っている 💦
2. 🐝 ユニーク制約が期待通り機能するように「無効」したレコードの値をnullに書き換えればよい 🤩
3. 🐌 「無効」にしたレコードにある値を無効にした時の状態から変更するのは気持ち悪い 😨
4. 🐝 無効にする時に値をnullに書き換えればよい 🤩
5. 🐌 無効にする際に(ステータスを変える際に)値をクリアするのは違和感少ないかも 😀
6. 🐝 ok。
7. 🐌 しかし論理削除もしもの時の復活を想定している。仮に復活を想定した場合クリアは困るかも 😨
8. 🐝 もしも復活させたいならアーカイブテーブルを設けよう 🤩 履歴型データ構造を採用するのもあり 🤩
9.  🐌 なるほど😀

> [!NOTE] CHECK制約を用いることで、無効の場合は特定のカラムの値クリアを必須にすることが可能です。

「削除」よりも「無効」の方が違和感が少ないような気がします。


### ユニークな部分インデックス

本題からは少し外れますが、整合性に関して言えば部分インデックスを使う方法も考えられます。、

1. 🐌 「無効」にしたレコードなのにユニーク制約として影響力を持っている 💦
2. 🐝 ユニーク制約が期待通り機能するように「無効」したレコードの値をnullに書き換えればよい 🤩
3. 🐌 「無効」にしたレコードにある値を無効にした時の状態から変更するのは気持ち悪い 😨
4. 🐝 無効にする時に値をnullに書き換えればよい 🤩
5. 🐌 削除と比べると、無効にする際に値をクリアするのはそれほど違和感少ない 😀
6. 🐝 ok。
7. 🐌 しかし論理削除もしもの時の復活を想定している。仮に復活を想定した場合クリアは困るかも 😨
8. 🐝 もしも復活させたいならアーカイブテーブルを設けよう 🤩 履歴型データ構造を採用するのもあり 🤩
9. 🐌 アーカイブテーブルや履歴型データ構造はめんどう 😨
10.  🐝 整合性のためであればユニークな部分インデックスを使えばいい。特定の条件の時に機能するユニーク制約を設定できる 🤩
11.  🐌 なるほど、いいかも 😀

> [!NOTE] OracleやMySQLでは部分インデックスはありません。代わりにOracleではファンクション索引、MySQLではGenerated column＋ユニークインデックスを用います。ちなみにこれは論理削除でも機能します。


## 「有効ではない/利用できない」状態を表す言葉

「論理削除」ではなくデータが「有効ではない/利用できない」状態を表す言葉を「無効」以外でも考えてみます。

ニュアンスに違いはありますが、次のような言葉が思い当たります。

- 非活性(inactive)
- 待機(standby)
- 取消(canceled)
- 無効(disabled)

これらの言葉と削除を比べると次のようなニュアンスの違いがあると思います。

- 非活性(inactive)
  - 見える。
  - 一部の人は閲覧・編集できる。
  - 集計の対象にできる。
- 待機(standby)
  - 見える。
  - 一部の人は閲覧・編集できる。
  - 集計の対象にできる。
  - これから有効・活性になりそう。
-  取消(canceled)
   - 見える。
   - 編集できない。閲覧はできたりできなかったり。
   - 取消にする時にリソースを解放するのは自然。
   - 集計の対象にできる。
   - 一度は有効だった
-  無効(disabled)
   - 見えない。
   - 編集できない。閲覧はできたりできなかったり。
   - 無効にする時にリソースを解放するのは自然。
   - 集計の対象にできる。
- 削除(deleted)
  - 見えない。削除したか元々なかったのかわからない。
  - 存在しないから編集しようがない。
  - 集計の対象にできない。
