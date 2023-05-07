---
layout: ../../layouts/index.astro
---

# Ubuntu と CentOS でのパッケージの update と upgrade

## Ubuntu などの Debian 系 (apt / apt-get)

```bash
sudo apt update
sudo apt upgrade
```

### update

- パッケージのリストを更新
- パッケージ自体の更新は行わない

### upgrade

- 更新されたパッケージのリストに基づいてインストール済みのパッケージを更新
- 新規のパッケージのインストールやインストール済みのパッケージの削除は行わない

### dist-upgrade

- 更新されたパッケージのリストに基づいてインストール済みのパッケージを更新
- 新規のパッケージのインストールやインストール済みのパッケージの削除を**行う**

## CentOS などの RedHat 系 (yum)

```bash
sudo yum update
sudo yum upgrade
```

### update

- インストール済みのパッケージを更新
- `/etc/yum.conf` 内の `obsoletes` が `1` の場合は `update --obsoletes` と同義で、不要なパッケージの削除も行う
  - デフォルトでは `obsoletes=1` となっている

### upgrade

- インストール済みのパッケージを更新
- `update --obsoletes` と同義で、不要なパッケージの削除も行う
