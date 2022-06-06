# tty と pts

## tty

- 標準入出力を担っている端末デバイス
- かつて1つのホストマシンに複数の teletypewriter (入出力を行うキーボード) を接続して操作していたものの名残

```sh
# Returns terminal name
$ tty
/dev/pts/1

# Display who is logged in and their processes
$ w
```

## pts

- tty を仮想的に実行したもの
- SSH や telnet で接続されている仮想端末
- ログイン端末ごとに `/dev/pts/` 配下に特殊ファイルが作成される

```sh
$ ls -l /dev/pts
crw--w---- 1 nokazn tty  136, 0 Jan 31 02:45 0
crw--w---- 1 root   tty  136, 1 Jan 31 02:45 1
crw--w---- 1 nokazn tty  136, 2 Jan 31 20:04 2
crw--w---- 1 nokazn tty  136, 3 Jan 31 19:43 3
crw--w---- 1 nokazn tty  136, 4 Jan 31 20:04 4
crw--w---- 1 nokazn tty  136, 5 Jan 31 14:44 5
c--------- 1 root   root   5, 2 Jan 31 02:45 ptmx

$ tty
/dev/pts/4

# 標準入出力が現在接続中の端末へのシンボリックリンクになっている
$ ls -l /dev/fd/
lrwx------ 1 nokazn nokazn 64 Jan 31 20:08 0 -> /dev/pts/4
lrwx------ 1 nokazn nokazn 64 Jan 31 20:08 1 -> /dev/pts/4
lrwx------ 1 nokazn nokazn 64 Jan 31 20:08 2 -> /dev/pts/4
lr-x------ 1 nokazn nokazn 64 Jan 31 20:08 3 -> /proc/29391/fd

# 他の端末に文字を表示
$ echo "おい！" > /dev/pts/5
```
