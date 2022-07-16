---
layout: ../../layouts/index.astro
---

# ターミナル起動時に `-bash: [: missing ]`  と表示される

`bash --login -x` (`bash -lx`) とすると bash 起動時に実行されるスクリプトを出力してくれる。

```bash
$ bash --login -x
# ...
++ [[ -s /home/nokazn/.sdkman/bin/sdkman-init.sh ]]
+ '[' -f '/home/nokazn/.bash_profile]'
bash: [: missing `]'
```

条件式を評価して真偽値を返すコマンドである `test` のエイリアスである `[]` 内の式の前後にはスペースを挿入する必要がある。

```diff
- if [ -f "$HOME/.bash_profile"]; then
+ if [ -f "$HOME/.bash_profile" ]; then
```
