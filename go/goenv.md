# goenv で Go のバージョンを管理する

システムに直接 Go をインストールするのではなく、goenv 経由でインストールする。

## goenv のインストール

```bash
$ git clone https://github.com/syndbg/goenv.git ~/.goenv
```

パスを通す

```bash
# Ubuntu Desktop or WSL
$ echo 'export GOENV_ROOT="$HOME/.goenv"' >> ~/.bashrc
$ echo 'export PATH="$GOENV_ROOT/bin:$PATH"' >> ~/.bashrc

# bash
$ echo 'export GOENV_ROOT="$HOME/.goenv"' >> ~/.bash_profile
$ echo 'export PATH="$GOENV_ROOT/bin:$PATH"' >> ~/.bash_profile
```

goenv をセットアップする。

```bash
$ ~/.goenv/bin/goenv init
# シェルを再起動
$ exec $SHELL -l
```

`~/.bashrc` に記述しておくとシェル起動時に実行される。`GOOROOT` と `GOPATH` もここで設定しておくと便利。

```bash
if [ -e "$HOME/.goenv" ]; then
    export GOENV_ROOT="$HOME/.goenv"
    export PATH="$GOENV_ROOT/bin:$PATH"
    # goenv コマンドが存在する場合
    if type "goenv" >/dev/null 2>&1; then
        eval "$(goenv init -)"
    fi
    export PATH="$GOROOT/bin:$PATH"
    export PATH="$PATH:$GOPATH/bin"
fi
```

## Go のインストール

```bash
# インストール可能なパッケージの一覧を表示
$ goenv install -l
# 最新のバージョンをインストール
$ goenv install $(goenv install -l | tail -n 1)

# グローバルの Go を最新版に設定
$ goenv global $(goenv install -l | tail -n 1)
$ go version
go version go1.15.5 linux/amd64

$ goenv install 1.14.12
# ローカルの Go を v1.14.12 に設定
$ goenv local 1.14.12
```

## Go のアンインストール

goenv を削除するには以下のようにする。

```bash
$ goenv uninstall 1.6.2
```

`~/.goenv/versions` 配下にインストールした Go が格納されているおり、`goenv uninstall` は対応するバージョンのディレクトリを削除するのと同義である。

## goenv のアンインストール

関連するファイルはすべて `GOENV_ROOT` 配下にあるので、これらを削除すればよい。

```bash
$ echo $(goenv root)
/home/nokazn/.goenv
$ rm -rf $(goenv root)
```

`.bashrc` から goenv に関する部分を削除しておく。

## 参考

[syndbg/goenv: Like pyenv and rbenv, but for Go.](https://github.com/syndbg/goenv)