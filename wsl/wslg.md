# WSLg で日本語化

## フォントのインストール

Windows 側のフォントを WSL 側でも読み込ませるよう、以下を実行する。

```bash
$ cat << EOF | sudo tee /etc/fonts/local.conf
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
    <dir>/mnt/c/Windows/Fonts</dir>
</fontconfig>
EOF
```

Arch Linux では `otf-ipaexfont` が公式からインストールできる。Firefox を開くにはこのフォントをインストールしないと日本語がうまく表示されなかった。

```bash
$ sudo pacman -S otf-ipaexfont
$ fc-list : file | grep ipaexg
/usr/share/fonts/OTF/ipaexg.ttf: IPAexGothic,IPAexゴシック:style=Regular
```

## 日本語入力の環境

Fcitx  (インプットメソッドフレームワーク) と Mozc (IME) で入力する。

Arch Linux では `fcitx-mozc` を入れればよい。

```bash
sudo pacman -S fcitx-mozc
```

`~/.bashrc` や `~/.zshrc` 内に以下のように環境変数を設定し、fcitx を起動する文を追加する。

```bash
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
export DefaultIMModule=fcitx
fcitx-autostart &> /dev/null
```

これで GUI アプリケーションを開いて、`ctrl` + `space` で日本語入力に切り替えることができる。

## OS の言語を日本語に変更する

`/etc/locale.gen` 内の言語を以下のようにしてコメントアウトを解除する。

```diff
- # ja_JP.UTF-8 UTF-8
+ ja_JP.UTF-8 UTF-8
```

ファイルを変更後、`locale-gen` コマンドを実行する。

```bash
sudo locale-gen
```

その後、`~/.bashrc` や `~/.zshrc` に `LOCALE` という環境変数を設定する文を追加する。

```diff
+ export LANG=ja_JP.UTF-8
```

## 参考

- [Win11のWSL2 (WSLg)を日本語化 & Mozcで日本語入力 | AsTechLog](https://astherier.com/blog/2021/07/windows11-wsl2-wslg-japanese/)
- [Arch Linux （Xfce4導入済み）に日本語環境を構築する - Qiita](https://qiita.com/HayatoAoi/items/7ef87c37ecb41f8ca14e)
