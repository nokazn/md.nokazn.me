# Docker Desktop によってマウントされたファイルは Docker を停止しないと削除できない

`docker-compose up` 等で生成した `node_modules` などがあるとき、Docker Desktop を起動中に `node_module` を削除しようとすると、

```bash
$ rm -rf ./node_modules
rm: cannot remove 'node_modules/': Device or resource busy
```

となり、削除できない。

```bash
$ lsof +D ./node_modules
lsof: WARNING: can't stat() overlay file system /mnt/wsl/docker-desktop-data/tarcache/entries/docker.tar/fac0a6fb496d0f723bf182e710af73e2e6c62259abd59b9326b57ac3a07100db/containers/services/docker/rootfs
      Output information may be incomplete.

$ ps -ef | grep node_modules
# no valid output
```

Docker Desktop を停止させると普通に削除できる。



## 参考

[files - How to get over "device or resource busy"? - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/11238/how-to-get-over-device-or-resource-busy)