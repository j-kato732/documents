# dockerのコンテナはfirewalldの設定を無視してアクセスできる
firewalldに以下のような設定をしている場合は22番以外の通信は遮断するはずなのであるが、、、
dockerの`-p 8080:8080`で立ち上げたコンテナに対してはアクセスできてしまうのだ
```
public
  target: default
  icmp-block-inversion: no
  interfaces:
  sources:
  services: ssh
  ports:
  protocols:
  forward: no
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```  

# 原因
> Docker version 20.10.0 またはそれ以上の利用にあたって、--iptablesを有効にして firewalld を利用する場合、Docker は自動的にfirewalldのゾーンとしてdockerを生成し、生成するネットワークインターフェース（たとえばdocker0など）すべてをdockerゾーンに加えることでシームレスなネットワークを実現します。

dockerはコンテナ間と通信を行うために、docker0というイーサネット（正確には仮想ブリッジ）を作成するのであるが
そのdocker0がfirewallのdockerというゾーンに登録されているため、
publicの設定とは別に通信が筒抜けになっているのである。
```
$ firewall-cmd --list-all --zone=docker
docker (active)
  target: ACCEPT
  icmp-block-inversion: no
  interfaces: br-08054e1acac2 br-14591961b49d br-177123efd6c5 br-29b4a5a96936 br-35af49bdc17f br-4da1961fe589 br-81260660fdd3 br-9021d402b261 br-a11a96aa64f9 br-bd26d5967e38 br-c3c057dc7771 docker0
  sources:
  services:
  ports:
  protocols:
  forward: no
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

かつ

# 対策
docker0のインターフェースをfirewallのdockerゾーンから削除
```
ゾーンと Docker インターフェースは適切なものに書き換えてください。
firewall-cmd --zone=trusted --remove-interface=docker0 --permanent
firewall-cmd --reload
```

その後、使いたいゾーンにdocker0を登録しよう
