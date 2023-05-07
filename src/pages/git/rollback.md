---
layout: ../../layouts/index.astro
---

# 元に戻す系まとめ

## ステージしてない(`git add`してない)ワークツリーの変更を元に戻す

```bash
git checkout ([ブランチ名]) --[ファイル名]
```

`git checkout`はブランチの状態を戻すのにも使われる。

## ステージしたファイルをワークツリーに戻す

```bash
git reset HEAD [ファイル名]
```

## ステージした新規追加ファイルをワークツリーに戻す

```bash
git rm --cached [ファイル名]
git rm --cached -r [フォルダ名]
```

`--hard`オプションをつけると，変更を完全に削除する。

## 直前コミットをやり直す

```bash
git commit --amend -m "[書き直したメッセージ]"
git commit --amend
```

## 過去のコミットに戻る

```bash
git reset
git reset HEAD^^ [ファイル名] # 2つ前の変更に戻る
git reset HEAD^5 [ファイル名] # 5つ前の変更に戻る
git reset [リビジョン番号]
```

## 過去のコミットを打ち消す

過去のコミットはそのままで、新たに打ち消したコミットが作成される。

```bash
git revert [リビジョン番号]
```
