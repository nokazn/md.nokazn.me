# Keybase で GPG キーを管理する

## 環境

- [Keybase クライアント](https://github.com/keybase/client)
- gpg

## 手順

### 1. CLI から Keybase にログインする

```bash
# 現在の状態を確認
$ keybase status

# 未ログインの場合はログイン
$ keybase login
```

### 2. 鍵を作成する

```bash
# すでに鍵が存在する場合は --multi オプションを付与する
$ keybase pgp gen  --multi
```

- Email は複数紐づけることができる
- Keybase.io のサーバー上に秘密鍵のコピーを保持する場合はパスフレーズで暗号化する

### 3. 鍵をインポートする

```bash
$ export GPG_TTY=$(tty)

# 公開鍵のインポート
$ keybase pgp export | gpg --import

# 秘密鍵のインポートでパスフレーズを入力できない場合は以下のように設定を変更
$ keybase config set -b pinentry.disabled 1

# 秘密鍵のインポート
$ keybase pgp export --secret | gpg --allow-secret-key-import --import

# インポートした秘密鍵が登録されているのが確認できる
$ gpg --list-secret-keys
```

### 4. Git の各コミットを署名する

```bash
# 鍵の ID が取得できる
$ gpg --list-secret-keys --keyid-format LONG | grep -E "^sec" | awk '{ print $2 }' | sed -E "s/^.+\///"

$ git config --global user.signingkey "$(gpg --list-secret-keys --keyid-format LONG | grep -E "^sec" | awk '{ print $2 }' | sed -E "s/^.+\///")"
# -Sオプションをつけなくても常に署名
$ git config --global commit.gpgsign true
```

### 5. GitHub に GPG キーを登録する

```bash
# 公開鍵をコピー
$ keybase pgp export -q <GPG キーの ID>
```

[設定画面](https://github.com/settings/gpg/new) から GPG キーを登録する。

## 参考

- [Keybase の鍵で GitHub のコミットに Verified バッジをつける - Qiita](https://qiita.com/HelloRusk/items/bcb0246b42d12195c6d0)
- [Unable to login in Linux terminal · Issue #6333 · keybase/client](https://github.com/keybase/client/issues/6333)
- [gpg-agent forwarding: inappropriate ioctl for device - Stack Overflow](https://stackoverflow.com/questions/51504367/gpg-agent-forwarding-inappropriate-ioctl-for-device)
