# WSL 上で sudo コマンドの認証を Windows Hello で行う

## インストール

[Release latest version · nullpo-head/WSL-Hello-sudo](https://github.com/nullpo-head/WSL-Hello-sudo/releases/latest) からダウンロードできる。

```bash
wget http://github.com/nullpo-head/WSL-Hello-sudo/releases/latest/download/release.tar.gz -O wsl-windows-sudo
mkdir wsl-windows-sudo && tar -xvf wsl-windows-sudo.tar.gz -C wsl-windows-sudo --strip-components 1
cd wsl-windows-hello
./install.sh
```

## 設定

```diff
# /etc/pam.d/sudo
+ auth sufficient pam_wsl_hello.so
session    required   pam_env.so readenv=1 user_readenv=0
session    required   pam_env.so readenv=1 envfile=/etc/default/locale user_readenv=0
@include common-auth
@include common-account
@include common-session-noninteractive
```

## 参考

[nullpo-head/WSL-Hello-sudo: Let's sudo by face recognition of Windows Hello on Windows Subsystem for Linux (WSL). It runs on both WSL 1 and WSL 2. This is a PAM module for Linux on WSL.](https://github.com/nullpo-head/WSL-Hello-sudo)
