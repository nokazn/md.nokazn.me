# MySQL 8.0 を Ubuntu 20.04 にインストールする

## インストール

```bash
$ sudo apt update
$ sudo apt install -y mysql-server

# 最低限のセキュリティ設定を行う
$ sudo mysql_secure_installation
```

## 起動

```bash
# 起動
$ sudo service mysql status
 * MySQL is stopped.
$ sudo service mysql start
$ sudo service mysql status
 * /usr/bin/mysqladmin  Ver 8.0.23-0ubuntu0.20.04.1 for Linux on x86_64 ((Ubuntu))
Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Server version          8.0.23-0ubuntu0.20.04.1
Protocol version        10
Connection              Localhost via UNIX socket
UNIX socket             /var/run/mysqld/mysqld.sock
Uptime:                 15 min 25 sec

Threads: 2  Questions: 39  Slow queries: 0  Opens: 469  Flush tables: 3  Open tables: 60  Queries per second avg: 0.042
```

## ログイン

```bash
$ sudo mysql -u root -p
mysql>
```

`CREATE USER '<user>'@'<host>' IDENTIFIED BY 'password';` のようにしてユーザーを作成することができる。

```bash
mysql> CREATE USER 'user1'@'localhost'IDENTIFIED WITH mysql_native_password BY 'password';
```

`IDENTIFIED WITH` の後に指定するユーザーの認証を行うためのプラグインはいくつか選択肢がある。

- caching_sha2_password
  - MySQL のデフォルト
  - パスワード認証
- mysql_native_password
  - caching_sha2_password は PHP アプリケーションとの互換性に問題があるため、その回避策として有効

作成したユーザーに権限を与えるには、`GRANT <PRIVILEGEs> ON <database>.<table> TO '<user>';` のようなコマンドを発行する。

```bash
mysql> GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES, RELOAD on *.* TO 'user1'@'localhost' WITH GRANT OPTION;
# CREATE USER や GRANT のキャッシュを削除
mysql> FLUSH PRIVILEGES;
mysql> exit

作成したユーザーで再ログイン
$ mysql -u user1 -p
mysql>
```

## 参考

[How To Install MySQL on Ubuntu 20.04 | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-20-04)
