---
layout: ../../layouts/index.astro
---

# AUR からパッケージをインストールする

## 準備

```bash
# 全体を最新の状態にする
$ sudo pacman -Ssy

# makepkg を使用するのに必要なパッケージをインストール
$ sudo pacman -S --needed base-devel glibc
```

## インストール

[aur/libwebp052](https://aur.archlinux.org/packages/libwebp052) をインストールする場合を考える。

### 1. [AUR の公式サイト](https://aur.archlinux.org/) からパッケージを検索する

### 2. 右メニューの Download snapshot のリンクから、`.tgz` ファイルをダウンロードし、解凍する

```bash
$ cd <ダウンロードするディレクトリ>

# libwebp052 の場合
$ wget https://aur.archlinux.org/packages/libwebp052
$ tar xvzf libwebp052
$ cd libwebp052
```

### 3. `makepkg` コマンドでパッケージの作成・インストールを行う

```bash
# PKGBUILD ファイルがあるディレクトリ内で実行
$ makepkg -Si

# 上記は以下と同義
$ makepkg -s
$ pacman -U  libwebp052-0.5.2-2-x86_64.pkg.xz
```

## 参考

- [AURからのインストール - とある社畜の頭脳整理](https://knowledge.rinpress.com/index.php/AUR%E3%81%8B%E3%82%89%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)
- [AUR (Arch User Repository) を活用する方法 | クロの思考ノート](http://note.kurodigi.com/post-0-13/)
