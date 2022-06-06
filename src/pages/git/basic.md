# git

## リポジトリとインデックスとワークツリー

- リポジトリ - Git でコミットしたファイル群の置き場
  - コミット ファイルの変更箇所ごとにバーションを作成して保存する。
- インデックス - コミットする前のファイル群の置き場
  - ステージ - ファイルの変更箇所を選択する。コミットするところを選んで仮置きする
- ワークツリー - 実際にGitで管理するファイル群

## gitの設定

```bash
git config --list / git config -l
git config --global user.name "my name"
git config --global user.email "my email"
```

`~/.gitconfig`が作成され、その中に設定が書かれる。`git config`の設定は以下の3段階。

- システム全体 (system)
- ユーザー全体 (global)
- 対象リポジトリのみ (local)

system → global → localの順で上書きされる。

## gitの一連の流れ

### コミットまで

ローカルレポジトリにcdして、

```bash
$ git init
Initialize empty Git repository in ~/test/.git
$ git status
Untracked files:
  2.rb
$ git add 2.rb
$ git status
Changes to be committed:
  new file: 2.rb
$ git commit -m "コミットメッセージ"
```

インデックスとローカルリポジトリの領域が確保されたあと、`2.rb`をステージして、コミットしている。

### リモートリポジトリの作成

ここでは`git`という名前のリポジトリを作成した後、ローカルリポジトリにリモートリポジトリを登録する。ローカルリポジトリ内では`origin`という名前で認識される。

```bash
$ git remote add origin https://github.com/nokazn/git.git
$ git remote -v
origin  https://github.com/nokazn/git.git (fetch)
origin  https://github.com/nokazn/git.git (push)
```

`origin`という名前をつけたのは、`git clone`コマンドでリモートリポジトリをローカルリポジトリへコピーしてくると、自動的にリモートリポジトリの名前が`origin`になるため。
`origin`は GitHub のリモートリポジトリの URL や、リモートリポジトリのコピーを持っているに過ぎない。

### ローカルリポジトリをリモートリポジトリに反映

```bash
git push origin master
git push origin master:master
```

`git push <リモートリポジトリ> <ローカルブランチ>:<リモートブランチ>`の意

### リモートリポジトリからローカルリポジトリへコピー

```bash
git clone
git pull
git fetch
```

- `git clone` - リモートリポジトリ自体をコピー
  - 内部的には`git remote add origin [repo]`+`git pull origin master`
- `git pull` - リモートリポジトリの更新をローカルリポジトリにコピー
  - 内部的には`git fetch`+`git merge`
- `git fetch` - リモートリポジトリの更新分を取得するのみでローカルリポジトリに取り込むまでは実行しない

### .gitignore

`.git`と同じ階層下に設置した`.gitignore`に git で管理しないファイル名を書く

- 外部に公開するとセキュリティ的に問題のあるファイル(パスワードなど)
- 二次的に生成されるファイル(キャッシュファイルやビルドファイルなど)
- その他外部で管理する必要のないもの
