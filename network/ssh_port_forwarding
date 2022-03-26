# sshのポートフォーワードを追加する

localの8080番からremote xxx.xxx.xxx.xxxの80番へフォワードする場合
```
$ ssh -L 8080:xxx.xxx.xxx.xxx:80
```

-g:ローカル外からのアクセスを許可する場合

# configへの設定
```
$ cd 
$ vim .ssh/config
HOST
  HostName xxx.xxx.xxx.xxx
  User hoge
  LocalForward   8080 localhost:8080 　<- 追記
```
