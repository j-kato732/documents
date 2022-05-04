# はじめに
サーバーは日夜、攻撃に晒されている。

aimoサーバーは、rootユーザーでのログインは禁止しているので、
攻撃者はログインできるユーザを推測してログインを試みる。

そのため「存在しないユーザ」を打ち込みログインしようとしたログが大量にある。
アクセスに関するログは
debian: `/var/log/auth.log`
CentOS: `/var/log/secure`
に吐き出される。

「存在しないユーザ」を打ち込みログインしようとしたログ`Disconnect from invalid user`の件数を確認してみると1128件
```
# cat /var/log/auth.log  | grep  "Disconnected from invalid user" | wc -l
1128
```

ちなみに攻撃者のIPの数は、688
つまり688ものPCから不正アクセスしようとされていた
```
# cat /var/log/auth.log | grep 'Disconnected from invalid user' | cut -d ' ' -f 12 |  sort | uniq | wc -l
688
```

ネットワークはこえー
てことで、そもそもアクセスできるIPを指定しておいて、
それ以外のIPからのアクセスはrefusedするようにする。

# 要約
アクセスを許可するIPを登録する
```
$ vim /etc/hosts.allow
sshd: xxx.xxx.xxx.xxx xxx.xxxx.xxx.xxx
```
アクセスを遮断するIPを登録する
以下の場合は全てのIPからのアクセスを遮断する設定
```
$ vim /etc/hosts.deny
sshd: all
```

# アクセスを許可するIPを登録する
Linuxには、「君はアクセスしてもいいよ！」なPCを記述できるファイル`/etc/hosts.allow`がある。

`/etc/hosts.allow`にIPを記述することで、対象のPCからのアクセスは許可される。
```
$ vim /etc/hosts.allow
sshd: xxx.xxx.xxx.xxx xxx.xxx.xxx.xxx
```

IPがわからない場合は、[確認くん](https://www.ugtop.com/spill.shtml)で確認しよう。


ちなみに、サブネットマスク指定やドメイン指定なども可能。

# アクセスを遮断するIPを登録する
Linuxには「君はアクセスしてはダメだよ！」な記述できるファイル`/etc/hosts.deny`もある。

このファイルにIPを記述することで、対象のPCからのアクセスは遮断（refused）される。

今回は、全てのIPを登録するのは面倒なので全ての通信を遮断する設定を行う
以下のように記述する
```
$ vim /etc/hosts.deny
ssld: all
```

設定を保存するとすぐに適用されるので、ログインができなくならないように注意  
全てのIPに対してアクセスを遮断するように設定しているが、`hosts.allow`の記述が優先されるので大丈夫。


設定が完了すると、アクセスがrefusedされているのか確認できる
```
$ cat /var/log/auth.log
May  4 08:44:08 localhost sshd[190386]: rexec line 125: Deprecated option RSAAuthentication
May  4 08:44:08 localhost sshd[190386]: refused connect from 58.229.13.59 (58.229.13.59)
```

# 最後に
これで、安心して眠れるね  

aimoのドメインは`aimo.tokyo`で、サーバーのユーザはaimoを使っている。
ドメインからIPはわかるしドメインからユーザー名を類推されやすいので、全く違うユーザー名で運用するのがセキュリティ的には良さそう。