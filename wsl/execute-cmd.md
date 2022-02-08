# WSL からコマンドプロンプトを実行する

Windows のプログラムをWSL から呼び出すには WSL から見た Windows 上の実行ファイルへの絶対パスを指定して直接呼び出せばよい。WSL から呼び出す場合は `.exe` は省略できない。`cmd.exe` へはパスが既に通っているので直接実行可能。

コマンドプロンプトにパスを渡す場合は Windows で有効な形式に変換する必要がある。スクリプトの実行終了後にコマンドプロンプトを終了させたい場合は `/c` オプションを付与する。

```bash
cmd.exe /c $(wslpath -w ./batch.cmd)
```

## 参考

[Windows 10のLinux互換環境WSLからコマンドプロンプトのプログラムを呼び出す（バージョン1803対応版）：Tech TIPS - ＠IT](https://www.atmarkit.co.jp/ait/articles/1805/31/news052.html)
