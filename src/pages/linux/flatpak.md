# Flatpak を導入する

## 環境

- Manjaro Linux
- Linux Kernel 5.15.12

## インストール

```bash
pamac install flatpak libpamac-flatpak-plugin
```

[flathub](https://flathub.org/home) にパッケージがあるので探して下記のようにインストールする。

```bash
flatpak install flathub com.spotify.Client
```

## トラブルシューティング

### TLS 証明書のエラーが出る

`Unable to load summary from remote flathub: Unacceptable TLS certificate` というメッセージが出る場合、 `ca-certificates` をインストールすれば直る。

```bash
pacman -Syu ca-certificates
```
