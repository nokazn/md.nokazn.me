# データベース

## データを操作する言語の分類

- DDL (Data Define Language)
  - `CREATE`
  - `DROP`
- DML (Data Manipulation Language)
  - `SELECT`
  - `INSERT`
  - `DELETE`
- DCL (Data Control Language)

## 結合

内部結合と外部結合があり、外部結合にも更に左側外部結合、右側外部結合、完全外部結合の3種類が存在する。

### 内部結合

結合に用いる列の値がいずれのテーブルにも存在している場合に結合する。`INNER` は明示的に指定しなくてもよい。

```sql
SELECT * from diaries INNER JOIN comments on diaries.id = comments.diary_id ORDER BY id;
```

### 外部結合

#### 左側外部結合

結合した左側のテーブルに結合に用いる値が存在する行を結果に含める。

```sql
SELECT * FROM diaries LEFT JOIN comments on diaries.id = comments.diary_id ORDER BY id;
```

#### 右側外部結合

結合した右側のテーブルに結合に用いる値が存在する行を結果に含める。左右のテーブルを入れ替えると左側外部結合と同義で通常は`LEFT OUTER JOIN` を使う。

```sql
SELECT * FROM diaries RIGHT JOIN comments on diaries.id = comments.diary_id ORDER BY id;
```

#### 完全外部結合

結合に用いる列の値がいずれかのテーブルにも存在している場合に結合する。値が存在しない場合は `NULL` で拡張される。

```sql
SELECT * FROM diaries FULL JOIN comments on diaries.id = comments.diary_id ORDER BY id;
```

### 交差結合 `CROSS JOIN`

複数のテーブルのデータの直積 (すべての組み合わせ) を結果として返す。
`WHERE` 句を用いることで上記の結合はすべて交差結合で書き直すことができる。`INNER JOIN <table_name> ON (TRUE)` と等価。

## SELECT 文内での句の優先順位

- `FROM`
  - 参照するテーブル名を1つ以上指定する
  - 複数のテーブルが指定された場合は直積を生成
- `ON`
  - 一致する行を決定するための条件式を返す
  - `JOIN` とともに `JOIN <table_name> ON <condition>` のように用いる
- `[ INNER | LEFT | RIGHT | FULL | CROSS ] JOIN`
  - 結合するテーブル名を指定する
  - `[ INNER ] JOIN`, `LEFT [ OUTER ] JOIN`, `RIGHT [ OUTER ] JOIN`, `FULL [ OUTER ] JOIN`, `CROSS JOIN` などがある
  - `JOIN` とともに `JOIN <table_name> ON <condition>` のように用いる

- `WHERE`
  - boolean 型を返す条件式を満たす行を抽出する
  - `WHERE <condition>` のように用いる
- `GROUP BY`
  - 列名を返す任意の式を指定し、値ごとにグループ化する
  - `HAVING`句や`SELECT`リストのどのスカラー式よりも先に評価される
  - `GROUP BY <column_name>` のように用いる

- `HAVING`
  - boolean 型を返す条件式を満たす行を抽出する
  - `GROUP BY` 適用後の各行から抽出するために用いる
  - `HAVING <condition>` のように用いる
- `WINDOW`
- `SELECT`
  - 出力行を指定し、すべての行を出力させたい場合は `*` で指定する
- `DISTINCT ON`
  - 重複する行を結果から除く
  - `DISTINCT ON (<column_name>)` のように用いる
- `UNION`
- `INTERSECT`
- `EXCEPT`
- `ORDER BY`
  - 1つ以上の出力列を指定した式に従ってソートする
  - `ORDER BY <expression> [ ASC | DESC | USING operator ]` のように用いる
- `LIMIT` / `OFFSET`
  - 行の最大値と行のスタート位置を指定する
  - `LIMIT` / `OFFSET` 句内で `NULL` と評価された場合はそれぞれ `ALL` / `0` として扱う
  - `LIMIT 50 OFFSET 100` のように用いる

## PostgreSQL ではキャメルケースは用いない

テーブル名やカラム名はダブルクォーテーションでくくられない場合すべて小文字に正規化されるので、スネークケースで統一したほうがいい。

```sql
select schedule."scheduleName", comments.comment, users.userName from comments left join schedule on schedule."scheduleId" = comments."scheduleId" join users on users."userId" = comments."userId";
```

## join は複数回適用させることができる

```sql
select
  schedule."scheduleName",
  comments.comment,
  users.userName
from comments
left join schedule on schedule."scheduleId" = comments."scheduleId"
join users on users."userId" = comments."userId";

```

## サブクエリの例

```sql
delete from candidate where candidate."scheduleId" in (select candidate."scheduleId" from candidate left join schedule on schedule."scheduleId" = candidate."scheduleId" where schedule."scheduleId" is null);
```

## 参考

[SELECT](https://www.postgresql.jp/document/9.4/html/sql-select.html)
