# Git のコミット履歴に

`~/.gitconfig` の設定漏れとかで、コミット履歴の author が適切に設定されず、GitHub 等でコミット履歴を見たときに author のアイコンが正しく表示されなかったりする場合の対処法。

## commiter の変更

```bash
$ git config --global user.name nokazn
$ git config --global user.email your_email@mail.com
```

## author の変更をして時刻を合わせる

```bash
# commit した時刻と author date を合わせるオプションを付与する
$ git rebase -i HEAD~2 --committer-date-is-author-date
# コミットのコメントと時刻はそのままにして author を変更する
$ git commit -m "$(git log --format="%s" -n 1)" \
  --author="nokazn <your_email@mail.com>" \
  --date="$(git log --format"%cd" -n 1)"\
  --amend; git rebase --continue;
```



## 参考

[git logのフォーマットを指定する - Qiita](https://qiita.com/harukasan/items/9149542584385e8dea75)