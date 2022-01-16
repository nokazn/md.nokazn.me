# WSLg で日本語化

Windows 側のフォントを WSL 側でも読み込ませるよう、以下を実行すればよい。

```bash
cat << EOF | sudo tee /etc/fonts/local.conf
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
    <dir>/mnt/c/Windows/Fonts</dir>
</fontconfig>
EOF
```

## 参考

- [Win11のWSL2 (WSLg)を日本語化 & Mozcで日本語入力 | AsTechLog](https://astherier.com/blog/2021/07/windows11-wsl2-wslg-japanese/)