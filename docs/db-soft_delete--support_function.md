- Table of Content
{:toc}

# 論理削除をサポートする機能
## ORM提供の論理削除

ORM (オブジェクトリレーショナルマッパー) が論理削除の機能を持っている場合があります。

例えばPHPのフレームワークLaravelのORMであるEloquentでは論理削除の機能があります。

`SoftDeletes` が有効なモデルでは `delete()` を実行すると論理削除になります。通常のデータの参照では論理削除されたデータは見えなくなります。

論理削除されたデータを見たい場合は `withTrashed()` を用います。


## データベース提供の論理削除：In-Database Archiving

データベースが論理削除の機能を持っている場合があります。それはOracle 12cからある `In-Database Archiving` 機能です。

In-Database Archiving機能では、データベースの内部的に論理削除を行います。

具体的にはテーブルに `ORA_ARCHIVE_STATE` という隠しカラムが存在し、通常のテーブルアクセスは論理削除されたレコードは見えなくなります。

論理削除されたレコードを見たい場合は `ALTER SESSION SET ROW ARCHIVAL VISIBILITY = ALL` を実行することで見えるようになります。

注意として、データベース提供の論理削除とはいえ、一般的な論理削除と同じようにレコードとしては実際に存在します。あくまで普通のテーブルアクセスで見えなくなるだけです。

論理削除されて非表示になったレコードもユニーク制約や主キー制約では存在感を残しています。

また `DELETE` が論理削除操作になるのではなく、`UPDATE` を使って論理削除することは変わりません。


## DELETE操作を論理削除に変更

INSTEAD OFトリガーを用いることで `DELETE` 操作を論理削除にすることができます。 

> [!NOTE] `INSTEAD OF` は対象にしたトリガーでは、対象にした `DELETE` や `INSERT` のデータ操作は実行されず、トリガーで設定した操作のみが実行されます。


# 参考文献
## DELETE操作を論理削除に変更
* What Are Soft Deletes, and How Are They Implemented?, https://www.brentozar.com/archive/2020/02/what-are-soft-deletes-and-how-are-they-implemented/

## In-Database Archiving
* インデータベースアーカイブの使用, https://docs.oracle.com/database/121/VLDBG/GUID-5A76B6CE-C96D-49EE-9A89-0A2CB993A933.htm
* Oracle Database 12c の論理削除機能を試す, https://qiita.com/plusultra/items/0c9dcb9992bbb664f6c0
* Oracle Database 12cリリース1 (12.1)でのインデータベース・アーカイブ, https://oracle-base.com/articles/12c/in-database-archiving-12cr1

## Laravelのソフトデリート
* ソフトデリート, https://readouble.com/laravel/10.x/ja/eloquent.html#soft-deleting
