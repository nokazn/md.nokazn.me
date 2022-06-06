# Arch Linux に Docker をインストールする

## インストール

```bash
sudo pacman -S docker
```

## デーモンを実行

```bash
# Docker のデーモンを起動
$ sudo systemctl start docker
$ sudo systemctl status docker
● docker.service - Docker Application Container Engine
     Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
     Active: active (running) since Sat 2021-12-18 17:31:40 JST; 10h ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 12165 (dockerd)
      Tasks: 30 (limit: 14364)
     Memory: 80.8M
     CGroup: /system.slice/docker.service
             ├─12165 /usr/bin/dockerd -H fd://
             └─12176 containerd --config /var/run/docker/containerd/containerd.toml --log-level info

# 起動時に Docker を起動するよう設定
$ sudo systemctl enable docker
```

## `sudo` なしで実行できるようにする

```bash
# docker というグループが存在しなければ作成
$ sudo newgroup docker
$ sudo chmod 666 /var/run/docker.sock
$ sudo usermod -aG docker ${USER}
```

`docker` というグループに現在のユーザーを追加し、`/var/run/docker.sock` にそのユーザーがアクセスできるようにすればよい。

## 疑問

- コマンドの補完が効かない
  - nix で入れた docker の方でコマンドの補完は一応補える
