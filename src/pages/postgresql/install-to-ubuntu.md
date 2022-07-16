---
layout: ../../layouts/index.astro
---

# PostgreSQL を Ubuntu 20.04 にインストールする

## インストール

```bash
# リポジトリの設定ファイルを作成する
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
cat /etc/apt/sources.list.d/pgdg.list
deb http://apt.postgresql.org/pub/repos/apt focal-pgdg main

# Import the repository signing key:
$ wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
# パッケージのリストを更新
$ sudo apt update
$ sudo apt install -y postgresql-12
```

## データベースクラスタを作成

PostgreSQL が管理するデータベースの格納領域をデータベースクラスタという。
通常は `postgres` ユーザーで以下のコマンドを叩くとデータ領域が作成される。

```bash
initdb -W -D  /var/lib/postgresql/12/data --locale=C --encoding=UTF8
```

Ubuntu の PostgreSQL のパッケージではインストールした際に `/var/lib/postgresql/<version>/main/`にデータ領域が作られる。

## OS 内の postgres ユーザーのパスワードを設定

`postgres` ユーザーでログインすると、`/var/lib/postgresql` にログインする。

```bash
$ sudo su - postgres
$ pwd
/var/lib/postgresql
```

PostgreSQL をインストールした直後の `postgres` ユーザーはパスワードが未設定の状態のままである。このままでは root ユーザーから `su` コマンドでスイッチする以外にログインする方法がないので、パスワードを設定しておく。

```bash
# root か sudoers なユーザーで実行
$ sudo passwd postgres
New password:
Retype new password:
passwd: password updated successfully
```

## 起動

`pg_ctl` で PostgreSQL のサービスを起動させる際、通常は `postgres` ユーザーで以下のコマンドを叩く。

```bash
pg_ctl start -D /var/lib/postgresql/12/main
```

`-D` オプションでデータ領域に指定されているディレクトリを渡す。

`pg_ctl start` は以下と同じ意味。

```bash
$ service postgresql status
12/main (port 5432): down
$ sudo service postgresql start
$ service postgresql status
12/main (port 5432): online
```

## PostgreSQL 内のユーザーのパスワードを設定

PostgreSQL 自体が管理する `postgres` ユーザーのパスワードも設定しておく。

```bash
$ sudo su - postgres
$ psql

# psql 内のコマンド
postgres=$ ALTER ROLE postgres WITH PASSWORD 'postgres';
ALTER ROLE
postgres=$ \q
```

## 参考

[PostgreSQL: Linux downloads (Ubuntu)](https://www.postgresql.org/download/linux/ubuntu/)
[ユーザーとパスワードの設定 ｜ PostgreSQLではじめるDB入門](http://db-study.com/archives/121)
