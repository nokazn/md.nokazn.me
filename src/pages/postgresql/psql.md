---
layout: ../../layouts/index.astro
---

# psql

PostgreSQL に接続するためのCLIツール。

```sh
# データベース名で指定
$ psql database_name

# ホスト名、ポート番号、ユーザー名、データベース名をオプションで指定
$ psql -h localhost -p 5432 -U postgres -d database_name

# URI で指定
# http で Basic 認証をして接続するのと似ている
$ psql "postgres://postgres:postgres@localhost:5432/database_name"
```
