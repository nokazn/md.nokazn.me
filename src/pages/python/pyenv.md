# pyenv で Python のバージョンを管理する

システムに直接 Python をインストールするのではなく、pyenv 経由でインストールする。

## pyenv のインストール

```bash
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
cd ~/.pyenv && src/configure && make -C src
```

パスを通す。

```bash
# Ubuntu Desktop or WSL
$ echo 'export PYENV_ROOT="${HOME}/.pyenv"' >> ~/.bashrc
$ echo 'export PATH="${PYENV_ROOT}/bin:${PATH}"' >> ~/.bashrc

# bash
$ echo 'export PYENV_ROOT="${HOME}/.pyenv"' >> ~/.bash_profile
$ echo 'export PATH="${PYENV_ROOT}/bin:${PATH}"' >> ~/.bash_profile
```

pyenv をセットアップする。

```bash
$ ~/.pyenv/bin/pyenv init
# シェルを再起動
$ exec "$SHELL"
$ pyenv --version
pyenv 1.4.0+3.631d0b6
```

`~/.bashrc` に記述しておくとシェル起動時に実行される。

```bash
if [ -e "$HOME/.pyenv" ]; then
    export PYENV_ROOT="${HOME}/.pyenv"
    export PATH="${PYENV_ROOT}/bin:${PATH}"
    # pyenv コマンドが存在する場合
    if type "pyenv" >/dev/null 2>&1; then
        eval "$(pyenv init -)"
    fi
fi
```

システムの依存ファイルが原因でコンパイルが失敗する場合があるので、各種ビルドツールをインストールしておく。

```bash
sudo apt update
sudo apt install --no-install-recommends make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev
```

## Python のインストール

```bash
# 3系の最新バージョンをインストール
$ pyenv install $(pyenv install --list | grep -E "^\s+3(\.[0-9]{1,2}){2}" | tail -n 1)
$ pyenv versions
3.9.1

# グローバルの Python を 3.9.1 に設定
$ pyenv global 3.9.1
$ python --version
Python 3.9.1

# 2系の最新バージョンをインストール
$ pyenv install $(pyenv install --list | grep -E "^\s+2(\.[0-9]{1,2}){2}" | tail -n 1)
$ pyenv versions
3.9.1
2.7.18
$ pyenv local 2.7.18
```

pipenv もインストールしておく。システム依存になるのを防ぐため、`--user` オプションを付与する。

```bash
$ pip install --user pipenv
$ pipenv --version
pipenv, version 11.9.0
```

pipenv のパスを通す。

```bash
if [ -e "${HOME}/.local/bin/pipenv" ]; then
    export PATH="${HOME}/.local/bin:${PATH}"
fi
```

pipenv で環境にインストールされていないバージョンが指定された場合は pyenv によって指定されたバージョンの Python がインストールされる。

```bash
$ pipenv --python 3.6
Warning: Python 3.6 was not found on your system…
Would you like us to install CPython 3.6.7 with pyenv? [Y/n]: y
Installing CPython 3.6.7 with pyenv (this may take a few minutes)…
⠦ Installing python...
```

## Python  のアンインストール

pyenv で特定のバージョンを削除する場合は以下のようにする。

```bash
pyenv uninstall 12.16.3
```

`~/.pyenv/versions` 配下にインストールした Python が格納されているおり、`pyenv uninstall` は対応するバージョンのディレクトリを削除するのと同義である。

## pyenv のアンインストール

関連するファイルはすべて `pyenv_ROOT` 配下にあるので、これらを削除すればよい。

```bash
$ echo $(pyenv root)
/home/nokazn/.pyenv
$ rm -rf $(pyenv root)
```

## 参考

[pyenv/pyenv: Simple Python version management](https://github.com/pyenv/pyenv)
[Suggested build environment· pyenv/pyenv Wiki](https://github.com/pyenv/pyenv/wiki#suggested-build-environment)
[Pipenvを使ったPython開発まとめ - Qiita](https://qiita.com/y-tsutsu/items/54c10e0b2c6b565c887a)
