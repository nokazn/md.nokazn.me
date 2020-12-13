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



## システム情報を出力

```bash
$ uname -a
Linux DESKTOP-20RV1U6 4.19.128-microsoft-standard #1 SMP Tue Jun 23 12:58:10 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
```



## Ubuntu でディストリビューションの情報を出力

### `lsb_release`

`lsb_release` コマンドがない場合はインストールする必要がある。

```bash
sudo apt install lsb_release
```

```bash
$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 20.04.1 LTS
Release:        20.04
Codename:       focal
$ lsb_release -cs
focal
```

### `/etc/lsb_release` を参照する

```bash
$ cat /etc/lsb_release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=20.04
DISTRIB_CODENAME=focal
DISTRIB_DESCRIPTION="Ubuntu 20.04.1 LTS"
```



## CentOS でディストリビューションのバージョン情報を出力

### `lsb_release`

`lsb_release` コマンドがない場合はインストールする必要がある。

```bash
sudo yum install redhat-lsb
```

```bash
$ lsb_release -a
LSB Version:    :core-4.1-amd64:core-4.1-noarch:cxx-4.1-amd64:cxx-4.1-noarch:desktop-4.1-amd64:desktop-4.1-noarch:languages-4.1-amd64:languages-4.1-noarch:printing-4.1-amd64:printing-4.1-noarch
Distributor ID: CentOS
Description:    CentOS Linux release 7.6.1810 (Core)
Release:        7.6.1810
Codename:       Core
```

### `/etc/redhat-release` を参照する

```bash
$ cat /etc/lsb_release
CentOS Linux release 7.6.1810 (Core)
```



## `apt` でインストールしたパッケージの履歴を確認する

```bash
# apt-get install / apt install とした履歴
$ cat /var/log/apt/history.log | grep install
Commandline: apt install make libssl-dev libghc-zlib-dev libcurl4-gnutls-dev libexpat1-dev gettext unzip -y
Commandline: apt install libnss3-tools
Commandline: apt-get install -y apt-transport-https
Commandline: apt-get install -y heroku
Commandline: apt install xsel
Commandline: apt install -y vim-gtk

# 依存関係のためインストールされたパッケージも含む
$ sudo apt list --installed
```



## `apt` で upgrade できるパッケージを確認する

```bash
# upgrade できるパッケージ
$ sudo apt list --upgradable
# install したパッケージ
$ sudo apt list --installed
```





## ホストマシンのアーキテクチャを出力

```bash
$ arch
x86_64
```




