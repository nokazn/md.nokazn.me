# WSL2 でメモリサイズとスワップ領域を設定する

デフォルトではホストマシンに搭載せれているメモリの 80% が割り当てられる。

```bash
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          23953         187       23697           0          68       23525
Swap:          6144           0        6144
```

ユーザーフォルダ配下に `.wslconfig` を追加することでメモリサイズとスワップ領域の割り当てを変更できる。

```
[wsl2]
memory=12GB
swap=6GB
```

WSL を再起動させると設定の変更が反映されている。

```bash
$ wsl --shutdown
```

```bash
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          11979         180       11730           0          68       11619
Swap:          6144           0        6144
```
