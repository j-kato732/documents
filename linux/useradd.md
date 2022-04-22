# はじめに
linuxのユーザ追加手順が一生覚えられん

# user用のディレクトリ追加
```
$ mkdir /home/user_name
```

# user用グループ作成
```
$ groupadd group_name
```

# ユーザーの追加
```
$ useradd -g group_name -d /home/user_name user_name
```

# ユーザーのパスワード設定
```
$ passwd user_name
```

# ユーザにグループを追加
-aを設定しないと、追加じゃなくて所属グループのセカンダリグループが置き換えられるだけになるので要注意
```
usermod -aG group_name user_name
```

sudoグループをユーザkatoに追加する場合
```
$ usermod -aG sudo kato
```
