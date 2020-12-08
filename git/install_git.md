# Ubuntu 20.04 にgit をソースインストールする

## 関連ツールをインストール

```bash
$ sudo apt update
$ sudo apt install git
$ git --version
git version 2.25.1
```

```bash
$ sudo apt update
$ sudo apt install make libssl-dev libghc-zlib-dev libcurl4-gnutls-dev libexpat1-dev gettext unzip -y
```

## git をインストール

GitHub に[ソースコードのミラー](https://github.com/git/git/releases)があるので、そこからダウンロードする。

2020年12月8日時点での最新版は `v2.29.2` となっている。

```bash
$ cd /usr/local/src
    $ sudo wget https://github.com/git/git/archive/v2.29.2.tar.gz -O git-v2.29.2.tar.gz
$ sudo tar -xvzf ./git-v2.29.2.tar.gz
$ cd git-2.29.2
$ sudo make prefix=/usr/local all
$ sudo make prefix=/usr/local install
```

## git の設定

```bash
$ git config --global user.name "nokazn"
$ git config --global user.email "nokazn@gmail.com"
$ git config --list
user.name=nokazn
user.email=nokazn@gmail.com
```