# firewalldのdockerゾーンを削除した
すると、以下のような影響があった
- 新しいコンテナがたち上がらない
- コンテナないから`git push`できない、pingのダウンロードもできない

これはやばいと思って、publicゾーンにdocker0のインターフェースを追加してみたが、
特に変化なし

なのでdockerdを再起動しようとした
```
aimo@localhost:~/aimo$ systemctl restart docker
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to restart 'docker.service'.
Authenticating as: aimo
Password:
==== AUTHENTICATION COMPLETE ===
Job for docker.service failed because the control process exited with error code.
See "systemctl status docker.service" and "journalctl -xe" for details.
aimo@localhost:~/aimo$ channel 5: open failed: connect failed: Connection refused
channel 5: open failed: connect failed: Connection refused
channel 5: open failed: connect failed: Connection refused
channel 5: open failed: connect failed: Connection refused
channel 5: open failed: connect failed: Connection refused
channel 5: open failed: connect failed: Connection refused
channel 5: open failed: connect failed: Connection refused
channel 5: open failed: connect failed: Connection refused
channel 5: open failed: connect failed: Connection refused
channel 5: open failed: connect failed: Connection refused
channel 5: open failed: connect failed: Connection refused
channel 5: open failed: connect failed: Connection refused
channel 5: open failed: connect failed: Connection refused
channel 5: open failed: connect failed: Connection refused
channel 5: open failed: connect failed: Connection refused
channel 5: open failed: connect failed: Connection refused
```
するとたち上がらなくなった  
どういうことだってばよ  
## systemctl で再度dockerを立ち上げてみよう
```
aimo@localhost:~$ systemctl start docker
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to start 'docker.service'.
Authenticating as: aimo
Password:
==== AUTHENTICATION COMPLETE ===
Job for docker.service failed because the control process exited with error code.
See "systemctl status docker.service" and "journalctl -xe" for details.
```

systemctl status docker.serviceとjournalctl -xeを見ろと言うテオル
```
aimo@localhost:~$ systemctl status docker.service
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: failed (Result: exit-code) since Sat 2022-05-14 06:35:20 UTC; 41s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
    Process: 243168 ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock (code=exited, status=1/FAILURE)
   Main PID: 243168 (code=exited, status=1/FAILURE)
        CPU: 157ms
```

systemctlのログ先のjournalctlを確認してみた


```
$ sudo journalctl -u docker
May 14 06:35:17 localhost dockerd[243168]: time="2022-05-14T06:35:17.519367509Z" level=info msg="Starting up"
May 14 06:35:17 localhost dockerd[243168]: time="2022-05-14T06:35:17.520993852Z" level=info msg="parsed scheme: \"unix\"" module=grpc
May 14 06:35:17 localhost dockerd[243168]: time="2022-05-14T06:35:17.521024292Z" level=info msg="scheme \"unix\" not registered, fallback to default scheme" module=grpc
May 14 06:35:17 localhost dockerd[243168]: time="2022-05-14T06:35:17.521060062Z" level=info msg="ccResolverWrapper: sending update to cc: {[{unix:///run/containerd/containerd.sock  <nil> 0 <nil>}] <nil> >
May 14 06:35:17 localhost dockerd[243168]: time="2022-05-14T06:35:17.521083412Z" level=info msg="ClientConn switching balancer to \"pick_first\"" module=grpc
May 14 06:35:17 localhost dockerd[243168]: time="2022-05-14T06:35:17.522591794Z" level=info msg="parsed scheme: \"unix\"" module=grpc
May 14 06:35:17 localhost dockerd[243168]: time="2022-05-14T06:35:17.522621204Z" level=info msg="scheme \"unix\" not registered, fallback to default scheme" module=grpc
May 14 06:35:17 localhost dockerd[243168]: time="2022-05-14T06:35:17.522652084Z" level=info msg="ccResolverWrapper: sending update to cc: {[{unix:///run/containerd/containerd.sock  <nil> 0 <nil>}] <nil> >
May 14 06:35:17 localhost dockerd[243168]: time="2022-05-14T06:35:17.522672374Z" level=info msg="ClientConn switching balancer to \"pick_first\"" module=grpc
May 14 06:35:17 localhost dockerd[243168]: time="2022-05-14T06:35:17.535412293Z" level=info msg="[graphdriver] using prior storage driver: overlay2"
May 14 06:35:17 localhost dockerd[243168]: time="2022-05-14T06:35:17.553629251Z" level=info msg="Loading containers: start."
May 14 06:35:17 localhost dockerd[243168]: time="2022-05-14T06:35:17.601184862Z" level=info msg="Firewalld: docker zone already exists, returning"
May 14 06:35:17 localhost dockerd[243168]: time="2022-05-14T06:35:17.844230666Z" level=warning msg="could not create bridge network for id 06f054512e6f2c0c6453d87b0ab797b677c7a7bf99fcda3f3a386c4931e405ee>
May 14 06:35:17 localhost dockerd[243168]: time="2022-05-14T06:35:17.882746174Z" level=info msg="Firewalld: interface br-a11a96aa64f9 already part of docker zone, returning"
May 14 06:35:17 localhost dockerd[243168]: time="2022-05-14T06:35:17.909502024Z" level=info msg="Firewalld: interface br-a11a96aa64f9 already part of docker zone, returning"
May 14 06:35:17 localhost dockerd[243168]: time="2022-05-14T06:35:17.978114847Z" level=info msg="Default bridge (docker0) is assigned with an IP address 172.17.0.0/16. Daemon option --bip can be used to >
May 14 06:35:18 localhost dockerd[243168]: time="2022-05-14T06:35:18.016620695Z" level=info msg="stopping event stream following graceful shutdown" error="<nil>" module=libcontainerd namespace=moby
May 14 06:35:18 localhost dockerd[243168]: failed to start daemon: Error initializing network controller: Error creating default "bridge" network: Failed to program NAT chain: ZONE_CONFLICT: 'docker0' al>
May 14 06:35:18 localhost systemd[1]: docker.service: Main process exited, code=exited, status=1/FAILURE
May 14 06:35:18 localhost systemd[1]: docker.service: Failed with result 'exit-code'.
May 14 06:35:18 localhost systemd[1]: Failed to start Docker Application Container Engine.
May 14 06:35:20 localhost systemd[1]: docker.service: Scheduled restart job, restart counter is at 3.
May 14 06:35:20 localhost systemd[1]: Stopped Docker Application Container Engine.
May 14 06:35:20 localhost systemd[1]: docker.service: Start request repeated too quickly.
May 14 06:35:20 localhost systemd[1]: docker.service: Failed with result 'exit-code'.
May 14 06:35:20 localhost systemd[1]: Failed to start Docker Application Container Engine.
```

dockerのネットワークブリッジであるdocker0を消してしまったことが原因か

publicにdokcer0追加したんやけどな...

でもdockerd起動時にdockerゾーンを参照してそうやけ、docker　networkの設定を変える必要があるのか

それともiptableを確認するべきか

でもcode-server内の困るけ、一旦dockerの再インストールが早いのかもしれない

# 原因
よくわからない...

再インストールしたほうが早いのかもしれない

# 解決
dockerを再インストールしてみました

すると新たにfirewalldにdockerゾーンが追加されて、
dockerdを起動できた

良かったー

以下手順
```
$ sudo apt remove docker docker-engine docker.io containerd runc
$ sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

```
$ firewall-cmd --list-all --zone=docker
docker (active)
  target: ACCEPT
  icmp-block-inversion: no
  interfaces: docker docker0
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
```
$ sudo firewall-cmd --change-interface=docker0 --zone=docker
sucesses
```

```
$ sudo systemctl start docker
$ sudo systemctl status docker 
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Sat 2022-05-14 11:24:48 UTC; 13min ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 244246 (dockerd)
      Tasks: 11
     Memory: 41.0M
        CPU: 305ms
     CGroup: /system.slice/docker.service
             └─244246 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

May 14 11:24:47 localhost dockerd[244246]: time="2022-05-14T11:24:47.971975195Z" level=info msg="Loading containers: start."
May 14 11:24:48 localhost dockerd[244246]: time="2022-05-14T11:24:48.018222905Z" level=info msg="Firewalld: docker zone already exists, returning"
May 14 11:24:48 localhost dockerd[244246]: time="2022-05-14T11:24:48.188536810Z" level=info msg="Default bridge (docker0) is assigned with an IP address 172.17.0.0/16. Daemon option --bip can be used to >
May 14 11:24:48 localhost dockerd[244246]: time="2022-05-14T11:24:48.247037828Z" level=info msg="Firewalld: interface docker0 already part of docker zone, returning"
May 14 11:24:48 localhost dockerd[244246]: time="2022-05-14T11:24:48.272885787Z" level=info msg="Firewalld: interface docker0 already part of docker zone, returning"
May 14 11:24:48 localhost dockerd[244246]: time="2022-05-14T11:24:48.386841137Z" level=info msg="Loading containers: done."
May 14 11:24:48 localhost dockerd[244246]: time="2022-05-14T11:24:48.477450933Z" level=info msg="Docker daemon" commit=87a90dc graphdriver(s)=overlay2 version=20.10.14
May 14 11:24:48 localhost dockerd[244246]: time="2022-05-14T11:24:48.478596885Z" level=info msg="Daemon has completed initialization"
May 14 11:24:48 localhost systemd[1]: Started Docker Application Container Engine.
May 14 11:24:48 localhost dockerd[244246]: time="2022-05-14T11:24:48.508576400Z" level=info msg="API listen on /run/docker.sock"
```


```
