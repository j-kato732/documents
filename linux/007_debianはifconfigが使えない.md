# debian 11はifconfigが標準でnot found
```
$ cat /etc/*release
PRETTY_NAME="Debian GNU/Linux 11 (bullseye)"
NAME="Debian GNU/Linux"
VERSION_ID="11"
VERSION="11 (bullseye)"
VERSION_CODENAME=bullseye
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"
```
```
$ ifconfig
bash: ifconfig: command not found
```

# ip aを使いましょう
ifconfigとかnetstatとか色々あるけどipコマンドに行こうする流れらしい
```
$ ip a
```
もしくは
```
$ ip address
```
## ipv4だけ表示することも可能
```
$ ip -4 a
```
