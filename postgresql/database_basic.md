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

結合した右側のテーブルに結合に用いる値が存在する行を結果に含める。

```sql
SELECT * FROM diaries RIGHT JOIN comments on diaries.id = comments.diary_id ORDER BY id;
```

#### 完全外部結合

結合に用いる列の値がいずれかのテーブルにも存在している場合に結合する。

```sql
SELECT * FROM diaries FULL JOIN comments on diaries.id = comments.diary_id ORDER BY id;
```

