# nodenv で Node.js のバージョンを管理する 

システムに直接 Node.js をインストールするのではなく、nodenv 経由でインストールする。

## nodenv のインストール

```bash
$ git clone https://github.com/nodenv/nodenv.git ~/.nodenv
$ cd ~/.nodenv && src/configure && make -C src
```

パスを通す。

```bash
# Ubuntu Desktop or WSL
$ echo 'export NODENV_ROOT="${HOME}/.nodenv"' >> ~/.bashrc
$ echo 'export PATH="${NODENV_ROOT}/bin:${PATH}"' >> ~/.bashrc

# bash
$ echo 'export NODENV_ROOT="${HOME}/.nodenv"' >> ~/.bash_profile
$ echo 'export PATH="${NODENV_ROOT}/bin:${PATH}"' >> ~/.bash_profile
```

nodenv をセットアップする。

```bash
$ ~/.nodenv/bin/nodenv init
# シェルを再起動
$ exec $SHELL
# nodenv が適切にインストールされているかチェック
$ curl -fsSL https://github.com/nodenv/nodenv-installer/raw/master/bin/nodenv-doctor | bash
$ nodenv --version
nodenv 1.4.0+3.631d0b6
```

`~/.bashrc` に記述しておくとシェル起動時に実行される。

```bash
if [ -e "${HOME}/.nodenv" ]; then
    export NODENV_ROOT="${HOME}/.nodenv"
    export PATH="${NODENV_ROOT}/bin:${PATH}"
    # nodenv コマンドが存在する場合
    if type "nodenv" >/dev/null 2>&1; then
        eval "$(nodenv init -)"
    fi
fi
```

## プラグインのインストール

```bash
$ mkdir -p "$(nodenv root)"/plugins
# Node.js のインストールを行えるようにする
$ git clone https://github.com/nodenv/node-build.git "$(nodenv root)"/plugins/node-build
# `nodenv update` コマンドで nodenv とプラグインの更新を行う
$ git clone https://github.com/nodenv/nodenv-update.git "$(nodenv root)"/plugins/nodenv-update
```

## Node.js のインストール

```bash
# インストール可能なパッケージの一覧を表示
$ nodenv install -l
$ nodenv install 12.16.3
$ nodenv versions
12.16.3

# グローバルの Node.js を v12.16.3 に設定
$ nodenv global 12.16.3
$ node --version
v12.16.3

$ node install 12.18.0
# ローカルの Node.js を v12.18.0 に設定
$ nodenv local 12.18.0
```

yarn をインストールする。

```bash
$ npm i yarn -g
$ nodenv rehash
$ which yarn
/home/nokazn/.nodenv/shims/yarn
```

`npm i -g` でインストールしたパッケージはバージョンごとで独立しているため、Node.js のバージョンを切り替えた場合は再度インストールする必要がある。



## Node.js  のアンインストール

nodenve で特定のバージョンを削除する場合は以下のようにする。

```bash
$ nodenv uninstall 12.16.3
```

`~/.nodenv/versions` 配下にインストールした Node.js が格納されているおり、`nodenv uninstall` は対応するバージョンのディレクトリを削除するのと同義である。

## nodenv のアンインストール

関連するファイルはすべて `NODENV_ROOT` 配下にあるので、これらを削除すればよい。

```bash
$ echo $(nodenv root)
/home/nokazn/.nodenv
$ rm -rf $(nodenv root)
```

## 参考

[nodenv/nodenv: Manage multiple NodeJS versions.](https://github.com/nodenv/nodenv)  
[nodenvの環境構築 - Qiita](https://qiita.com/282Haniwa/items/a764cf7ef03939e4cbb1)