# 初めに
一般ユーザでログインするとタブ補完が効かないので、  
調べてみるとログインシェルがshになっていたことが原因でした。

なのでログインシェルをbashに変更したい

# ログインシェルの変更コマンドは「chsh」
ログインシェルを変更したいユーザになり、chshコマンドを打つ

パスワードを聞かれるので入力し、その後ログインシェルを入力する
```
$ chsh
Password:
Changing the login shell for aimo
Enter the new value, or press ENTER for the default
        Login Shell [/bin/sh]: /bin/bash
```
