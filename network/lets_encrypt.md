# Lets encryptでサーバ証明を発行する
## 事前準備
- 発行対象のドメインを取得していること
- HTTP（80番）でウェブサーバへの接続が可能であることを確認済み

## certbotをインストールする
Lets encryptでサーバー証明書を発行する時はcertbotコマンドをつかうのでインストールする

epelをインストールしたことがない人はこれから
```
$ sudo apt -y install epel-release
```

certbotのインストール
```
$ sudo apt -y install certbot
```

## 証明書を発行する
サーバを停止して以下のコマンドを実行
```
$ certbot certonly --standalone -d www.example.com
```

サブドメインやSAN対応のサーバー証明書にする場合は-dオプションを追加
```
$ certbot certonly --standalone -d www.example.com -d www2.example.com -d example.com
```

正常に発行できた場合  
`/etc/letsencrypt/archive/www.example.com`に証明書の実ファイル、`/etc/letsencrypt/live/www.example.com/`に証明書へのシンボリックリンクが作成される
```
$ /etc/letsencrypt/live/www.example.com
cert.pem  chain.pem  fullchain.pem  privkey.pem
```
ので、シンボリックリンクをWEBサーバーに設定する

cert.pem: 証明書
chain.pem: 中間証明書
fullchain.pem: 証明書と中間証明書を連結したファイル
privkey.pem: 秘密鍵
