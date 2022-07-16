---
layout: ../../layouts/index.astro
---

# 再起動してもセッションを維持する

[tmux-plugins/tmux-resurrect](https://github.com/tmux-plugins/tmux-resurrect#configuration) を入れるとウィンドウやペインの状態を保存・復元することができる。

## tpm でプラグインを管理する

```bash
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

```diff
+ set -g @plugin 'tmux-plugins/tpm'
+ set -g @plugin 'tmux-plugins/tmux-resurrect'

+ run-shell '~/.tmux/plugins/tpm'
```

```bash
$ tmux source ~/.tmux.conf
$ tmux
# tmux のセッションに入って <prefix> + I (大文字) で .tmux.conf に記載されているプラグインをインストールする
$ ls ~/.tmux/plugins
tmux-resurrect  tpm
```

- `<prefix> + I` - `.tmux.conf` の一覧にあるプラグインをインストール
- `<plugin> + u` - .`tmux.conf` の一覧にあるプラグインを更新
- `<plugin> + alt +  u - .`tmux.conf` の一覧にないプラグインを削除

## セッションの保存・復元

- `<prefix> + Ctrl + s` - 保存
- `<prefix> + Ctrl + r` - 復元

## 参考

[tmux-plugins/tpm: Tmux Plugin Manager](https://github.com/tmux-plugins/tpm)
[tmux-plugins/tmux-resurrect: Persists tmux environment across system restarts.](https://github.com/tmux-plugins/tmux-resurrect#configuration)
