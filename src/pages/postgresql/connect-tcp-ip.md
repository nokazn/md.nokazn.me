# PostgreSQL に TCP/IP で接続する

デフォルトでは UNIX ドメインソケットで接続する。
コマンドライン引数 `-h` でホスト名が指定されるか、環境変数 `PGHOST` が設定された場合は TCP/IP で接続しようとする。

設定で TCP/IP での接続が許可されていないと

```txt
connect ECONNREFUSED 127.0.0.1:5432
```

となる。`/etc/postgresql/12/main/postgresql.conf` を編集する必要がある。

```diff
+ listen_addresses = '*'
+ port = 5432
```
