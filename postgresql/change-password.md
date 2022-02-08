# パスワードを変更する

```bash
$ psql -U postgres

postgres=$ \password
postgres=$ ALTER ROLE postgres with password 'password';
postgres=$ \q
```
