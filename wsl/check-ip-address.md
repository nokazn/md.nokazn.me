# WSL に割り当てられる IP アドレスを確認する

```bash
cat /etc/resolv.conf | grep nameserver | awk '{print $2}'
```

## 参考

[Can't use X-Server in WSL 2 · Issue #4106 · microsoft/WSL](https://github.com/microsoft/WSL/issues/4106#issuecomment-501885675)
