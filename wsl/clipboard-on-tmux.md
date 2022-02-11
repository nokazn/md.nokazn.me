# WSL 上の tmux でクリップボードを有効にする

## X Server をインストールして `xsel` を使えるようにする

Linux では GUI の描画はほとんどの場合 X-Server (S Window System) が採用されている。
フリーの X Server として VcXsrv を利用する。

[VcXsrv Windows X Server download | SourceForge.net](https://sourceforge.net/projects/vcxsrv/)  から VcXsrv をインストールする。

chocolatey を使う場合は以下のようにする。

```powershell
choco install vcxsrv
```

インストールが完了したら XLaunch を実行し、デフォルトで選択されている項目のまま設定する。
最後に設定を xml ファイルとして保存できるので適当な場所に保存する。

Windows のスタートアップ時に実行させてもよいが、今回は WSL 内の `~/.bashrc` からシェルログイン時に起動スクリプトを実行させる。

```bash
# ~/.bashrc

# wsl.exe が存在する場合
if type wsl.exe > /dev/null 2>&1; then
    # WSL に割り当てられる IP アドレスを取得して設定
    export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}'):0.0
    # 実行中のプロセス一覧から vcxsrv を抽出した結果が空文字列の場合
    if [ -z "$(tasklist.exe | grep vcxsrv)" ]; then
        # wsl 内のパスを windows で有効なパスに変換してコマンドプロンプトから xlaunch を実行
        cmd.exe /c "$(wslpath -w ./windows/config.xlaunch)"
    fi
fi
```

WSL から `cmd.exe` を実行する際は /c オプションをつけるとコマンド実行後にコマンドプロンプトが終了する。
引数として渡すパスは Windows 上で有効なフォーマットにする必要があるので、`wslpath -ws` で変換させている。

これで X Server が使え、xsel が動く。
WSL 内で xsel もインストールする。

```bash
$ sudo apt install -y xsel
$ xsel --version
xsel version 1.2.0 by Conrad Parker <conrad@vergenet.net>
```

## tmux を vim のキーバインドで使う

`~/.tmux.conf` に以下を追加する。

```bash
# setw としても同義
set-window-option -g mode-keys vi

# v で選択を開始
bind-key -T copy-mode-vi v send-keys -X begin-selection
# y キーで選択を終了
bind-key -T copy-mode-vi y send-keys -X copy-pipe-and-cancel "xsel -ip && xsel -op | xsel -ib"
# Enter キーで選択を終了
unbind -T copy-mode-vi Enter
bind-key -T copy-mode-vi Enter send-keys -X copy-pipe-and-cancel "xsel -ip && xsel -op | xsel -ib"
# マウスカーソルでドラッグして選択を終了
bind-key -T copy-mode-vi MouseDragEnd1Pane send-keys -X copy-pipe-and-cancel "xsel -ip && xsel -op | xsel -ib"
```

## vim でクリップボードを有効にする

```bash
$ vim --version | grep clipboard
-clipboard         +keymap            +printer           +vertsplit
+emacs_tags        +mouse_gpm         -sun_workshop      +xterm_clipboard
```

として、`-clipboard` となっていれば無効、`+clipboard` となっていれば有効である。

新たにインストールする場合、

- GUI あり
  - ~~`vim-gnome`~~ - Ubuntu では 19.10 より削除されている
  - `vim-gtk3` - vim-gnome に相当するらしい
  - `vim-gtk` - GNOME 関連のパッケージに依存せず、GNOME以外のウィンドウマネージャを使ってる場合は有用
- GUI なし
  - `vim-nox`

などがある。`apt list vim-*` とすれば確認できる。
今回は `vim-gtk` を使う。

```bash
$ sudo apt install -y vim-gtk
$ vim --version | grep clipboard
+clipboard         +keymap            +printer           +vertsplit
+emacs_tags        +mouse_gpm         -sun_workshop      +xterm_clipboard
```

`~/.vimrc` に以下の内容を追加すると、vim でコピーした文字列を外部に張り付けることができるようになっている。

```bash
echo "set clipboard=unnamedplus" > ~/dotfiles/.vimrc
```

## 参考

- [WSL2におけるVcXsrvの設定 - Qiita](https://qiita.com/ryoi084/items/0dff11134592d0bb895c)
- [Can't use X-Server in WSL 2 · Issue #4106 · microsoft/WSL](https://github.com/microsoft/WSL/issues/4106)
- [WSLコンソールのコピペ方法まとめ（Vim,Tmux) - Qiita](https://qiita.com/kenji0x02/items/f77008985818583bf32b)
- [WSL2におけるVcXsrvの設定 - Qiita](https://qiita.com/ryoi084/items/0dff11134592d0bb895c)
- [vim-gnomeとvim-gtkの違い | うなすけとあれこれ](https://blog.unasuke.com/2014/difference-of-vim-gnome-and-vim-gtk/)
- [Vimでクリップボードからのペーストを可能にする – MY ROBOTICS](https://sy-base.com/myrobotics/vim/vim_use_clipboard/)
