# Go を CentOS にインストールする

## 環境

- VirtualBox 6.0.8
- Vagrant 2.2.4
- CentOS 7.7

## 手順

### `yum`でインストール

```bash
$ yum install epel-release
$ yum install golang -y
$ go version
go version go1.13.6 linux/amd64
```

### 公式サイトからバイナリをインストール

```bash
$ cd /usr/local/src
$ curl -O https://dl.google.com/go/go1.14.2.linux-amd64.tar.gz
$ tar -C /usr/local -xzvf /usr/local/src/go1.14.2.linux-amd64.tar.gz
$ echo "export PATH=$PATH:/usr/local/go/bin" >> ~/.bash_profile
$ go version
go version go1.14.2 linux/amd64
```

## `$GOPATH`を設定する

> Go言語では、環境変数GOPATHに保存されたパス（以降、単にGOPATHと呼びます）を開発時の作業ディレクトリとして扱います。 GOPATHを利用することで、外部ライブラリの導入やビルド作業を非常に簡単に行うことができます。

[GOPATHについて - はじめてのGo言語](http://cuto.unirita.co.jp/gostudy/post/gopath/)

```bash
echo "export GOPATH=/usr/local/go" >> ~/.bash_profile
```
