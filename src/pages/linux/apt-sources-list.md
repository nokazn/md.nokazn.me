---
layout: ../../layouts/index.astro
---

# apt のソースリスト

`apt install` や `apt update` を実行する際には

- `/etc/apt/sources.list`
- `/etc/apt/sources.list.d/*.list`

が参照される。`sources.list.d` にはパッケージごとのソースのリポジトリを記録したファイルが置かれる。

## `sources.list` のフォーマット

```txt
deb http://apt.postgresql.org/pub/repos/apt focal-pgdg main
```

| name | description |
| ---- | ----------- |
| Archive type | `deb` - バイナリのパッケージ<br />`dev-src` - ソースファイルを含むパッケージ |
| Repository URL | パッケージがホストされているリポジトリの URL |
| Distribution | リリース用のコードネームかリリースの状態 (`oldstate`, `stable`, `testing`, `unstable` など) |
| Component | `main` - [DFSG](http://www.debian.org/social_contract#guidelines) 準拠のパッケージ<br />`contrib` - DFSG 準拠だが `main` に依存しない<br />`non-free` - DFSG 準拠でないパッケージ |

### 参考

[SourcesList - Debian Wiki](https://wiki.debian.org/SourcesList)
