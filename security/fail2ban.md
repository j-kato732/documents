fail2banは、不正アクセスからサーバを守るソフト  
ログを監視して、何度もアクセスに失敗しているIPをブロックするなどして、
不正アクセスを禁止できる。

# fail2ban インストール
まずはepel-releaseのインストール  
epelは、エンタープライズ向けのLinux用のソフトウェアを集積したリポジトリの一つ  
CentOSなどの公式パッケージに含まれないが、有用なソフトを提供している
```
$ dnf -y install epel-release
```

fail2banのインストール
```
$ dnf -y install fail2ban
```

# fail2banの起動
```
$ fail2ban-client start
Server ready
```

# 設定方法
具体的な設定（何sのうちにn回ssh接続に失敗するとブロックなど）は、`/etc/fail2ban/jail.local`に記述する。  
全てのアクセスに関する設定は、`DEFAULT`に記述  
sshアクセスに関する設定は、sshdに記述（これは`/etc/fail2ban/filer.d/`配下にsshdのフィルターが存在しているから）
```
$ vim /etc/fail2ban/jail.local
[DEFAULT]
ignoreip = 127.0.0.1/8 xxx.xxx.xxx.xxx xxx.xxx.xxx.xxx
banaction = firewallcmd-ipset
bantime = 600
findtime = 10
maxRetry = 2
destemail = example@test.com
action = %(action_mwl)s

[sshd]
enabled = true
bantime = 600000
findtime = 10
maxretry = 1
```

fail2ban-clientの再起動
```
$ fail2ban-client restart
```

# banされたIPアドレスの確認
```
$ fail2ban-client status sshd
Status for the jail: sshd
|- Filter
|  |- Currently failed:	0
|  |- Total failed:	0
|  `- File list:	/var/log/auth.log
`- Actions
   |- Currently banned:	1
   |- Total banned:	1
   `- Banned IP list:	150.109.114.49
```

# もし自分のIPがブロックされてしまったら
別のPCから頑張ってSSH接続して、以下のコマンドで解除できる
```
$ fail2ban-client set sshd unbanip xxx.xxx.xxx.xxx
```
自分のPCのIPは[IP確認くん](https://www.ugtop.com/spill.shtml)で確認しよう


