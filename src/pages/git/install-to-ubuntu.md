# git  のソースインストール (Ubuntu)

## 必要なパッケージのインストール

```bash
$ sudo apt install libcurl4-gnutls-dev \
  libexpat1-dev gettext \
  libz-dev libssl-dev
```

ドキュメントをdoc, html, info 形式等でインストールしたい場合は以下の依存ライブラリも必要になる。

```bash
sudo apt install asciidoc xmlto docbook2x
```

## ソースをインストール

```bash
$ cd /usr/local/src
$ wget https://github.com/git/git/archive/v2.27.0.tar.gz
$ tar -zxf git-2.0.0.tar.gz
$ cd git-2.0.0
$ make configure
$ ./configure --prefix=/usr
# リンクの設定をしないとコンパイルが通らない
$ ln -s /bin/db2x_docbook2texi /bin/docbook2x-texi
$ sudo make all doc info
$ sudo make install install-doc install-html install-info
```

## 参考

- [Git - Gitのインストール](https://git-scm.com/book/ja/v2/%E4%BD%BF%E3%81%84%E5%A7%8B%E3%82%81%E3%82%8B-Git%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)
