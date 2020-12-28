# goenv で Go のバージョンを管理する

システムに直接 Go をインストールするのではなく、goenv 経由でインストールする。

## goenv のインストール

```bash
$ git clone https://github.com/syndbg/goenv.git ~/.goenv
```

パスを通す

```bash
# Ubuntu Desktop or WSL
$ echo 'export GOENV_ROOT="${HOME}/.goenv"' >> ~/.bashrc
$ echo 'export PATH="${GOENV_ROOT}/bin:${PATH}"' >> ~/.bashrc

# bash
$ echo 'export GOENV_ROOT="${HOME}/.goenv"' >> ~/.bash_profile
$ echo 'export PATH="${GOENV_ROOT}/bin:${PATH}"' >> ~/.bash_profile
```

goenv をセットアップする。

```bash
$ ~/.goenv/bin/goenv init
# シェルを再起動
$ exec $SHELL -l
```

`~/.bashrc` に記述しておくとシェル起動時に実行される。~~`GOOROOT`~~ と `GOPATH` もここで設定しておくと便利。goenv 2.0 系では `GOPATH`をよしなに設定してくれるので自分で設定する必要はない。

```bash
if [ -e "${HOME}/.goenv" ]; then
    export GOENV_ROOT="${HOME}/.goenv"
    export PATH="${GOENV_ROOT}/bin:${PATH}"
    # goenv コマンドが存在する場合
    if type "goenv" >/dev/null 2>&1; then
        eval "$(goenv init -)"
    fi
    export PATH="${GOROOT}/bin:${PATH}"
    export PATH="${PATH}:${GOPATH}/bin"
fi
```



## Go のインストール

```bash
# インストール可能なパッケージの一覧を表示
$ goenv install -l
# 最新のバージョンをインストール
$ goenv install $(goenv install -l | tail -n 1)
$ goenv versions
* 1.15.5 (set by /home/nokazn/.goenv/version)

# グローバルの Go を最新版に設定
$ goenv global $(goenv install -l | tail -n 1)
$ go version
go version go1.15.5 linux/amd64

$ goenv install 1.14.12
# ローカルの Go を v1.14.12 に設定
$ goenv local 1.14.12
```

vscode-go で [`Go: Locate Configured Go Tools`](https://github.com/golang/vscode-go/blob/master/docs/commands.md#go-locate-configured-go-tools) としたときに、うまく `GOROOT` を読み込んでくれない場合は Remote WSL の接続をやり直すとうまくいった。

[golang/vscode-go: Go extension for VS Code # install go](https://github.com/golang/vscode-go#install-go)



## Go のアンインストール

goenv で特定のバージョンを削除するには以下のようにする。

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



## WSL で vscode-go を使うときの注意点

WSL で [vscode-go](https://github.com/golang/vscode-go) を使い、Remote WSL で接続する際には環境変数をうまく読み込ませる必要がある。
VSCode Remote が WSL 内で起動する際にはシェルの起動スクリプトは実行されておらず、vscode-go が `GOROOT` 等の環境変数を読み込む段階では環境変数が設定されていないっぽい。

`~/.vscode-server/server-env-setup` 内に VSCode Remote が起動する前に実行してほしいスクリプトを置いておくことができる。

```
# vscode-go で環境変数を認識させる
if [ -d "${HOME}/.goenv" ]; then
    export GOENV_ROOT="${HOME}/.goenv"
    export PATH="${GOENV_ROOT}/bin:${PATH}"
    # goenv コマンドが存在する場合
    if type "goenv" >/dev/null 2>&1; then
        eval "$(goenv init -)"
        # GOROOT はよしなに設定してくれる
        export GOPATH="${HOME}/go"
        export PATH="${PATH}:${GOPATH}/bin"
        # Go Modules を有効にする
        export 'GO111MODULE=on'
    fi
fi
```



## 参考

[syndbg/goenv: Like pyenv and rbenv, but for Go.](https://github.com/syndbg/goenv)
[Developing in the Windows Subsystem for Linux with Visual Studio Code](https://code.visualstudio.com/docs/remote/wsl#_advanced-environment-setup-script)