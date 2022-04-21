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
