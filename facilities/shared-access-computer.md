## 共有マシン

### 概要

融合メディア研究室に共有マシンが配備されています。
研究室メンバであれば誰でもそこそこ強いマシンをリモートで利用することができます。

### 利用開始手順

共有マシンを利用するためにはCloudflareアカウントが必要です。
持っていない方は作成してください。

共有マシンに対する設定はすべてオフラインで行います。

1. loginというユーザにログインします。パスワードはありません。
2. ターミナルを開きます。以降、カレントディレクトリは `~` 即ち `/home/login/` に保ちます。
3. `./add-user.sh <user-name> <password>` を実行します。ただし、 `<user-name>` には追加するUNIXユーザ名を、 `<password>` にはそのユーザのパスワードを指定します。
4. `./login-cloudflare-tunnel.sh <user-name> <password>`を実行します。コマンドラインが停止します。
5. 別のターミナルを開き、 `cat ~/login.log` を実行し、表示されたログイン用URLをブラウザで開いてCloudflare Tunnelにログインします。初めてCloudflareにログインする場合は、Cloudflareにログインした後、もう一度同じURLを開いてCloudflare Tunnelにログインしてください。成功すると4.で停止したコマンドラインが復活します。以降、ここで開いたターミナルは不要です。また、共有マシン上のブラウザを用いた場合はCloudflareからログアウトしてください(シークレットウィンドウであっても)。
6. `./create-cloudflare-tunnel.sh <user-name> <password> <tunnel-name> <domain>` を実行します。ただし、 `<tunnel-name>` はトンネルに付けたい名前を、 `<domain>` にはトンネルに付けたいドメインを指定します。独自のドメインを持っていない場合、 `<subdomain>.cfargotunnel.com` を指定すると良いでしょう。ただし、 `<subdomain>` はトンネルに付けたいサブドメインを指定します。

Cloudflare上の管理画面でトンネルが作成されている・かつ状態がActiveであることを確認してください。共有マシンでの設定は以上です。

次に、ssh接続元となるPCの設定を行います。

1. `cloudflared` をインストールします。
2. `cloudflared tunnel login` でCloudflare Tunnelにログインします。
3. `~/.ssh/config` に次を追記します。ただし、 `<host-name>` は任意の文字列を、 `<path>/<to>` には `cloudflared` コマンドへの絶対パスを指定します。

```
Host <host-name>
	HostName <domain>
	User <user-name>
	ProxyCommand <path>/<to>/cloudflared access ssh --hostname %h
```

PCの設定は以上です。例えば `<host-name>` を `fm-lab` にした場合、次のように共有マシンへ接続します。

```
$ ssh fm-lab
```
