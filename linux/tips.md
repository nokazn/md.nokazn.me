# Linux 小技集

よく使うコマンドとか諸々。



## パスを見やすく表示する

```bash
$ echo $PATH | sed "s/:/\n/g"
```



## コマンドの存在チェック

```bash
type "command" > /dev/null 2>&1
```

`2>&1` では標準エラー出力を標準出力に変更させている。
`/dev/null` は unix 上では空白ファイルを意味し、これにリダイレクトさせることで、コマンドの出力結果を `/dev/null` に捨てている。

### 参考

[typeコマンド - Bash入門](https://bash.open-code.club/Linux%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89/T/type.html)  
[OSに付属するシェルスクリプトを読んで技術を盗む (2/2)：スマートな紳士のためのシェルスクリプト（3）- ＠IT](https://www.atmarkit.co.jp/ait/articles/1201/27/news113_2.html)  
[いい加減覚えよう。 `command > /dev/null 2>&1`の意味 - Qiita](https://qiita.com/ritukiii/items/b3d91e97b71ecd41d4ea)  



## `sed` コマンドの `-r` オプション

マッチした箇所のキャプチャは `-r` オプションをつけないと有効にならない。

マッチした順番に 1, 2, 3, ... と番号が振られ、`\1`, `\2`, `\3`, ... とすると参照できる。

```bash
$ echo "foo" | sed -e "s/(^.*$)/warning: \1/g"
sed: -e expression #1, char 22: invalid reference \1 on `s' command's RHS

$ echo "foo" | sed -r -e "s/(^.*$)/warning: \1/g"
warning: foo
```



## `sed` コマンドの `-e` オプション

> `sed` では `-e` オプションを省略することができます。
>
> ただ、`-e` オプションを省略した場合は、`sed` の第一引数がコマンド、第二引数以降は入力ファイルと解釈されます。

```bash
$ sed -e "s/before/after/g" ./in.txt
$ sed "s/before/after/g" ./in.txt
```

### 参考

[sedの-eオプションは省略もできるが、引数の設定順に注意](https://it-ojisan.tokyo/sed-e-option/)



## ディレクトリの容量を調べる

```bash
$ du ./DefinitelyTyped -sm
1021    ./DefinitelyTyped/
```

- `-s` - サブディレクトリの情報を表示しない
- `-m` - 1Mb 単位で表示する (`-block-size=1M` と同義)



## 特定のポート番号を使用しているプロセスを調べる

### `ss` / `netstat`

Ubuntu Desktop では `netstat` を含む net-tools が標準でインストールされなくなったり、CentOS7 では非推奨となったりで、現在は `ss` コマンドの利用が推奨されている。

- `-a` - 全ソケットを表示
- `-n` - サービス名に変換せず表示
- `-t` - TCP 情報のみ表示
- `-u` - UDP 情報のみ表示

| name | description |
| --- | --- |
| Netid | Socketのタイプ<br/>  `u_str`：UNIX Domainソケット<br/>  `tcp`： TCPソケット<br/>   `udp`： UDPソケット |
| State | 通信の状態 |
| Recv-Q | 受信キューの数 |
| Send-Q | 送信キューの数 |
| Local Address:Port | サーバー側のIP又はソケットファイル、ポートを表示 |
| Peer Address:Port | 通信している側のIP又はソケットファイル、ポートを表示 |

```bash
$ ss -atn
State    Recv-Q    Send-Q    Local Address:Port    Peer Address:Port    Process   
LISTEN   0         128       127.0.0.1:3000        0.0.0.0:*
```

```bash
netstat -atn
State    Recv-Q    Send-Q    Local Address:Port    Peer Address:Port    Process   
LISTEN   0         128       127.0.0.1:3000        0.0.0.0:*
```

### `lsof -i`

プロセスがアクセスしているポートを調べる。

- `-i` -  select IPv[46] files

```bash
$ lsof -i
COMMAND  PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
node    4607 nokazn   18u  IPv6 447269      0t0  TCP *:35677 (LISTEN)
node    4607 nokazn   20u  IPv6 447273      0t0  TCP 172.30.216.228:35677->DESKTOP-20RV1U6.mshome.net:60511 (ESTABLISHED)
node    4837 nokazn   19u  IPv6 447274      0t0  TCP 172.30.216.228:35677->DESKTOP-20RV1U6.mshome.net:60512 (ESTABLISHED)

# ポート番号を指定
$ lsof -i:80
```

### 参考

[Linuxでプロセスが何のポート使っているかを調べる - Qiita](https://qiita.com/sonoshou/items/cc2b740147ba1b8da1f3)
[Ubuntu 17.04 その148 - netstatからssへ移行しよう・ソケットの情報を表示するコマンド - kledgeb](https://kledgeb.blogspot.com/2017/07/ubuntu-1704-148-netstatss.html)
[Linuxのポート状況を確認する](https://www.linuxmaster.jp/linux_skill/2009/02/linux-4.html)

