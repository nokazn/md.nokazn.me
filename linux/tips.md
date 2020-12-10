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