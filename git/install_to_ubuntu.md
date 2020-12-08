# Github に ssh 接続する

## SSH Key を生成する

OpenSSH 付属の`ssh-keygen`で SSH key を生成する。

安全性を考慮して、鍵長は4096bitとする。ただ、闇雲に鍵長を長くしても処理が重くなったりするのでその辺はトレードオフ。

参考: [GitHubユーザーのSSH鍵6万個を調べてみた](https://hnw.hatenablog.com/entries/2014/07/05)

```bash
# コメントには GitHub に登録しているメールアドレスを記載する
$ ssh-keygen -t rsa -b 4096 -C "nokazn@gmail.com"
Enter file in which to save the key (/Users/you/.ssh/id_rsa): [Press enter]
Enter passphrase (empty for no passphrase): [Type a passphrase]
Enter same passphrase again: [Type passphrase again]

# SSH Key の鍵長を確認
$ ssh-keygen -l -f ~/.ssh/id_rsa.pub
4096 SHA256:Zo7elJWugXLzl9vpF55gc6J+bRJ9/u7bjb5t/5yTZmA baleariclemon@gmail.com (RSA)
```

## GitHub に SSH Key を登録

[GitHubの SSH Key を管理するページ](https://github.com/settings/keys)で、新しく作成した SSH Keys を登録する。

- Title - 鍵を識別できる名前をつける
- Key - `~/.ssh/id_rsa.pub`の内容を貼り付ける

## `~/.ssh/config`に追加

以下のような設定ファイルを追加する。

```diff:~/.ssh/confg
+ Host github.com
+   HostName github.com
+   User git
+   IdentityFile "/home/nokazn/.ssh/id_rsa_github"
```

Host は以下の`git@github.com:nokazn...`の`github.com`と揃える必要がある。
また、権限を変更しておく。

```bash
$ ls -l ~/.ssh/config
-rw-rw-r--. 1 nokazn nokazn 99 Sep 17 14:51 /home/nokazn/.ssh/config
$ chmod 600 ~/.ssh/config
$ ls -l ~/.ssh/config
-rw-------. 1 nokazn nokazn 99 Sep 17 14:51 /home/nokazn/.ssh/config
```

## GitHub へ SSH 接続する

```bash
$ ssh -T git@github.com
The authenticity of host 'github.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
RSA key fingerprint is MD5:16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
Are you sure you want to continue connecting (yes/no)? 
yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
Enter passphrase for key '/home/nokazn/.ssh/id_rsa': 
Hi nokazn! You've successfully authenticated, but GitHub does not provide shell access.
```

`~/.ssh\config`の設定項目で接続するにはホストを指定する。

```bash
$ ssh github.com
Warning: Permanently added the RSA host key for IP address '192.30.255.112' to the list of known hosts.
PTY allocation request failed on channel 0
Hi nokazn! You've successfully authenticated, but GitHub does not provide shell access.
Connection to github.com closed.
```

## GitHub への push/pull を SSH で行う

```bash
# リモートリポジトリが設定されてない場合
$ git remote add origin git@github.com:nokazn/[リポジトリ名].git

# リモートリポジトリが設定済の場合
$ git remote set-url origin git@github.com:nokazn/[リポジトリ名].git

$ git remote -v
origin  git@github.com:nokazn/[リポジトリ名].git (fetch)
origin  git@github.com:nokazn/[リポジトリ名].git (push)
```

## 参考

[GitHubでssh接続する手順～公開鍵・秘密鍵の生成から～ - Qiita](https://qiita.com/shizuma/items/2b2f873a0034839e47ce)
[git パスワード を毎回聞かれる問題の解決方法 - Qiita](https://qiita.com/rorensu2236/items/df7d4c2cf621eeddd468)

