# Docker コンテナから WSL2 で動いているサーバーを参照したい話

## 何をしたいか

例えば、[こういう](https://github.com/nokazn/access-wsl2-from-docker-container) リポジトリのような構成のアプリがあって、Docker Desktop for Windows で動いているコンテナから WSL2 で動いているサーバーへリバースプロキシしたい。

## 環境

- Windows 10 バージョン 2004・ビルド19041.928
- WSL2
  - Ubuntu 20.04.2 LTS on Windows 10 x86_64
  - カーネルバージョン 5.4.72-microsoft-standard-WSL2
- Docker Desktop for Windows 3.3.3 (ビルド 64133)
  - Docker Engine 20.10.6

基本的に開発で使用するものは WSL2 の中で完結させる。
Docker Desktop は[公式サイト](https://hub.docker.com/editions/community/docker-ce-desktop-windows)から Windows 側でインストールし、WSL2 integration を有効にすることで、WSL2 内からも `docker` コマンドが使えるようになる。

## 問題

nginx のコンテナから WSL2 内で動いているサーバーに `host.docker.internal:3000` へ向けてリバースプロキシする際、ホストOS (Windows) 側の IP アドレスで解決され、WSL2 内のサーバーへ到達しない。

Docker Desktop は `host.docker.internal` というドメインからホストOSへの解決ができるよう、起動時に `C:\Windows\System32\drivers\etc\hosts` 内に以下のようにホストOS (Windows) の IP アドレスを追加してくれる。

```txt
192.168.xxx.xxx host.docker.internal
192.168.xxx.xxx gateway.docker.internal
```

WSL2 起動時には、この `hosts` ファイルを基にして WSL2 内の `/etc/hosts` に同じ内容のものが自動生成される。
WSL2 内のコンテナから `host.docker.internal` をアクセスしようとすると、`/etc/hosts`を参照して、 WSL2 の IP アドレスではなくホスト OS (Windows) に向いてしまう。

## 結局どうすればよいか

nginx コンテナは `コンテナ → ホスト (Windows)` とアクセスしようとするので、`コンテナ → ホスト (Windows) → WSL2` という経路でアクセスできれば WSL2 に到達できる。
ポートフォワードの設定をして、`ホスト (Windows) → WSL2` とアクセスできるようにすれば解決する。

[こちら](https://rcmdnk.com/blog/2021/03/01/computer-windows-network/) で上げられているスクリプトをほぼそのまま実行させると解決した。`netsh interface portproxy add` でTCPプロトコルで指定されたポートにて通信をする際、WSL2 にポートフォワードさせている。

デフォルトでは Powershell スクリプトの実行が制限されているため、実行ポリシーを変更する必要がある。Powershell を管理者権限で開き、以下を実行する。

```txt
PS $ Set-ExecutionPolicy RemoteSigned
PS $ Get-ExecutionPolicy
RemoteSigned
```

以下のスクリプトを管理者権限で開いた Powershell から実行する。ポート番号は適宜動かすアプリケーションによって変更させる。

```powershell
# See also https://rcmdnk.com/blog/2021/03/01/computer-windows-network/
if (!([Security.Principal.WindowsPrincipal][Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole("Administrators")) {
  Start-Process powershell.exe "-File `"$PSCommandPath`"" -Verb RunAs;
  exit;
}

# WSL2 の IP アドレス
$ip = bash.exe -c "ip r | grep 'eth0 proto' | cut -d ' ' -f9"
if( ! $ip ){
  Write-Output "The Script Exited, the ip address of WSL 2 cannot be found";
  exit;
}

# 転送するポート
$ports=@(3000);
$ports_a = $ports -join ",";

# 既存のファイアウォールのルールがあれば削除
Invoke-Expression "Remove-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' ";
# 送受信の際、ポート転送をファイアウォールでブロックしない
Invoke-Expression "New-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' -Direction Outbound -LocalPort $ports_a -Action Allow -Protocol TCP";
Invoke-Expression "New-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' -Direction Inbound -LocalPort $ports_a -Action Allow -Protocol TCP";

for( $i = 0; $i -lt $ports.length; $i++ ){
  $port = $ports[$i];
  # ポート転送
  Invoke-Expression "netsh interface portproxy add v4tov4 listenport=$port listenaddress=* connectport=$port connectaddress=$ip";
}

Invoke-Expression "netsh interface portproxy show v4tov4";
```

WSL2 に割り当てられる IP アドレスは起動時ごとに変動するため、WSL2 が起動するごとにスクリプトを実行する必要がある。

```bash
#!/usr/bin/env bash
/mnt/c/Windows/System32/WindowsPowerShell/v1.0//powershell.exe /home/nokazn/port_forward.ps1
```

のようなスクリプトを[WSL2 のスタートアップスクリプトを実行するハック](https://qiita.com/amenoyoya/items/41a2334cbc1facb87864) を使って実行できるか試してみたがうまくいかなかったため、Windows 内のタスクスケジューラでシステム起動時にスクリプトを実行させる。

```powershell
PS $ wsl --shutdown
```

のようにして WSL2 を再起動させると、割り当てられる IP アドレスも変動してしまうため、手動でスクリプトを実行する必要がある。

## おわりに

- Windows での開発体験は向上してきたとはいえ、まだつらい部分がある
- WSL2 の中身は Hyper-V 上で動いている軽量な VM なので、仮想化とかネットワークの知識とかが求められる場面もある
- `host.docker.internal` が WSL2 の IP アドレスに直接解決してくれればよいので、`/etc/wsl.conf` で `hosts` ファイルの自動生成をやめる設定を書いて、自前で `/etc/hosts` ファイルを書けばよいのではと思ったがうまくいかなかった (謎)

## 参考

- [Windows WSL2に外部から直接アクセスするための設定](https://rcmdnk.com/blog/2021/03/01/computer-windows-network/)
- [[WSL 2] NIC Bridge mode 🖧 (Has TCP Workaround🔨) · Issue #4150 · microsoft/WSL](https://github.com/microsoft/WSL/issues/4150#issuecomment-504209723)
