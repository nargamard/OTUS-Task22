# Задание: VPN

Что нужно сделать?

Между двумя виртуалками поднять vpn в режимах
- tun;
- tap;

Прочуствовать разницу.
Поднять RAS на базе OpenVPN с клиентскими сертификатами, подключиться с локальной машины на виртуалку.
Самостоятельно изучить, поднять ocserv и подключиться с хоста к виртуалке*

## Решение

### Поднимаем vpn в режимах (tap/tun)
Подключаемся к серверу, проверяем, поднялся ли интерфейс `tap`, проверяем доступность клиента, запускаем `iperf3` для измерения скорости:
```bash
[andrej@home-srv OTUS-Task22]$ vagrant ssh server
Last login: Fri May  3 10:56:38 2024 from 192.168.56.1
[vagrant@server ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 52:54:00:27:8b:50 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute eth0
       valid_lft 85907sec preferred_lft 85907sec
    inet6 fe80::5054:ff:fe27:8b50/64 scope link 
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:e7:1d:ad brd ff:ff:ff:ff:ff:ff
    inet 192.168.12.1/30 brd 192.168.12.3 scope global noprefixroute eth1
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fee7:1dad/64 scope link 
       valid_lft forever preferred_lft forever
4: eth2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:2a:8a:ae brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.10/24 brd 192.168.56.255 scope global noprefixroute eth2
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe2a:8aae/64 scope link 
       valid_lft forever preferred_lft forever
5: tap0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UNKNOWN group default qlen 100
    link/ether c6:6a:df:a5:2f:f3 brd ff:ff:ff:ff:ff:ff
    inet 10.10.10.1/24 brd 10.10.10.255 scope global tap0
       valid_lft forever preferred_lft forever
    inet6 fe80::c46a:dfff:fea5:2ff3/64 scope link 
       valid_lft forever preferred_lft forever
[vagrant@server ~]$ ping 10.10.10.2
PING 10.10.10.2 (10.10.10.2) 56(84) bytes of data.
64 bytes from 10.10.10.2: icmp_seq=1 ttl=64 time=1.42 ms
64 bytes from 10.10.10.2: icmp_seq=2 ttl=64 time=0.768 ms
^C
--- 10.10.10.2 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 3ms
rtt min/avg/max/mdev = 0.768/1.094/1.420/0.326 ms
[vagrant@server ~]$ iperf3 -s &
[1] 24944
[vagrant@server ~]$ -----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
Accepted connection from 10.10.10.2, port 42934
[  5] local 10.10.10.1 port 5201 connected to 10.10.10.2 port 42936
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-1.00   sec  11.1 MBytes  93.1 Mbits/sec                  
[  5]   1.00-2.00   sec  12.8 MBytes   107 Mbits/sec                  
[  5]   2.00-3.00   sec  12.4 MBytes   104 Mbits/sec                  
[  5]   3.00-4.00   sec  12.3 MBytes   103 Mbits/sec                  
[  5]   4.00-5.00   sec  11.7 MBytes  98.0 Mbits/sec                  
[  5]   5.00-6.00   sec  11.9 MBytes  99.8 Mbits/sec                  
[  5]   6.00-7.01   sec  12.1 MBytes   101 Mbits/sec                  
[  5]   7.01-8.00   sec  12.0 MBytes   101 Mbits/sec                  
[  5]   8.00-9.00   sec  12.0 MBytes   101 Mbits/sec                  
[  5]   9.00-10.00  sec  11.9 MBytes  99.5 Mbits/sec                  
[  5]  10.00-11.00  sec  11.9 MBytes  99.5 Mbits/sec                  
[  5]  11.00-12.00  sec  12.9 MBytes   108 Mbits/sec                  
[  5]  12.00-13.00  sec  12.3 MBytes   103 Mbits/sec                  
[  5]  13.00-14.00  sec  11.9 MBytes  99.7 Mbits/sec                  
[  5]  14.00-15.00  sec  12.2 MBytes   102 Mbits/sec                  
[  5]  15.00-16.00  sec  12.2 MBytes   102 Mbits/sec                  
[  5]  16.00-17.00  sec  12.1 MBytes   102 Mbits/sec                  
[  5]  17.00-18.00  sec  12.0 MBytes   101 Mbits/sec                  
[  5]  18.00-19.00  sec  12.1 MBytes   102 Mbits/sec                  
[  5]  19.00-20.00  sec  12.3 MBytes   103 Mbits/sec                  
[  5]  20.00-21.00  sec  11.0 MBytes  92.1 Mbits/sec                  
[  5]  21.00-22.00  sec  12.0 MBytes   100 Mbits/sec                  
[  5]  22.00-23.00  sec  12.1 MBytes   101 Mbits/sec                  
[  5]  23.00-24.00  sec  12.0 MBytes   101 Mbits/sec                  
[  5]  24.00-25.00  sec  12.2 MBytes   102 Mbits/sec                  
[  5]  25.00-26.00  sec  11.4 MBytes  95.7 Mbits/sec                  
[  5]  26.00-27.00  sec  12.3 MBytes   103 Mbits/sec                  
[  5]  27.00-28.00  sec  12.5 MBytes   105 Mbits/sec                  
[  5]  28.00-29.00  sec  11.7 MBytes  98.8 Mbits/sec                  
[  5]  29.00-30.00  sec  12.7 MBytes   106 Mbits/sec                  
[  5]  30.00-31.00  sec  12.5 MBytes   105 Mbits/sec                  
[  5]  31.00-32.00  sec  12.6 MBytes   106 Mbits/sec                  
[  5]  32.00-33.00  sec  12.4 MBytes   104 Mbits/sec                  
[  5]  33.00-34.00  sec  12.6 MBytes   105 Mbits/sec                  
[  5]  34.00-35.00  sec  12.4 MBytes   104 Mbits/sec                  
[  5]  35.00-36.00  sec  11.8 MBytes  99.2 Mbits/sec                  
[  5]  36.00-37.00  sec  12.1 MBytes   101 Mbits/sec                  
[  5]  37.00-38.00  sec  11.7 MBytes  97.9 Mbits/sec                  
[  5]  38.00-39.00  sec  12.4 MBytes   104 Mbits/sec                  
[  5]  39.00-40.00  sec  11.9 MBytes   100 Mbits/sec                  
[  5]  40.00-40.06  sec   820 KBytes   104 Mbits/sec                  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-40.06  sec   485 MBytes   102 Mbits/sec                  receiver
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
[vagrant@server ~]$ 
```

Подключаемся к клиенту, проверяем, поднялся ли интерфейс `tap`, проверяем доступность клиента, запускаем `iperf3` для измерения скорости:

```bash
[andrej@home-srv OTUS-Task22]$ vagrant ssh client
Last login: Fri May  3 10:56:38 2024 from 192.168.56.1
[vagrant@client ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 52:54:00:27:8b:50 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute eth0
       valid_lft 85883sec preferred_lft 85883sec
    inet6 fe80::5054:ff:fe27:8b50/64 scope link 
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:2d:d2:eb brd ff:ff:ff:ff:ff:ff
    inet 192.168.12.2/30 brd 192.168.12.3 scope global noprefixroute eth1
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe2d:d2eb/64 scope link 
       valid_lft forever preferred_lft forever
4: eth2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:5b:d1:a7 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.11/24 brd 192.168.56.255 scope global noprefixroute eth2
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe5b:d1a7/64 scope link 
       valid_lft forever preferred_lft forever
5: tap0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UNKNOWN group default qlen 100
    link/ether e2:38:79:98:00:ff brd ff:ff:ff:ff:ff:ff
    inet 10.10.10.2/24 brd 10.10.10.255 scope global tap0
       valid_lft forever preferred_lft forever
    inet6 fe80::e038:79ff:fe98:ff/64 scope link 
       valid_lft forever preferred_lft forever
[vagrant@client ~]$ iperf3 -c 10.10.10.1 -t 40 -i 5
Connecting to host 10.10.10.1, port 5201
[  5] local 10.10.10.2 port 42936 connected to 10.10.10.1 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-5.01   sec  61.5 MBytes   103 Mbits/sec   22   95.5 KBytes       
[  5]   5.01-10.00  sec  59.4 MBytes  99.8 Mbits/sec   25   83.9 KBytes       
[  5]  10.00-15.01  sec  61.6 MBytes   103 Mbits/sec   15    115 KBytes       
[  5]  15.01-20.01  sec  60.3 MBytes   101 Mbits/sec   22    104 KBytes       
[  5]  20.01-25.00  sec  59.1 MBytes  99.3 Mbits/sec   29    110 KBytes       
[  5]  25.00-30.01  sec  60.9 MBytes   102 Mbits/sec   30    107 KBytes       
[  5]  30.01-35.00  sec  62.4 MBytes   105 Mbits/sec   25    111 KBytes       
[  5]  35.00-40.01  sec  60.0 MBytes   101 Mbits/sec   26   96.8 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-40.01  sec   485 MBytes   102 Mbits/sec  194             sender
[  5]   0.00-40.06  sec   485 MBytes   102 Mbits/sec                  receiver

iperf Done.
[vagrant@client ~]$ 
```

Меняем в конфигурации openvpn на сервере и клиенте `tap` на `tun`, перезапускаем сервисы и снова измеряем скорость:
```bash
[vagrant@client ~]$ sudo -i
[root@client ~]# vi /etc/openvpn/server.conf 
[root@client ~]# systemctl restart openvpn@server.service 
[root@client ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 52:54:00:27:8b:50 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute eth0
       valid_lft 85320sec preferred_lft 85320sec
    inet6 fe80::5054:ff:fe27:8b50/64 scope link 
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:2d:d2:eb brd ff:ff:ff:ff:ff:ff
    inet 192.168.12.2/30 brd 192.168.12.3 scope global noprefixroute eth1
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe2d:d2eb/64 scope link 
       valid_lft forever preferred_lft forever
4: eth2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:5b:d1:a7 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.11/24 brd 192.168.56.255 scope global noprefixroute eth2
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe5b:d1a7/64 scope link 
       valid_lft forever preferred_lft forever
6: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UNKNOWN group default qlen 100
    link/none 
    inet 10.10.10.2/24 brd 10.10.10.255 scope global tun0
       valid_lft forever preferred_lft forever
    inet6 fe80::2df2:be2:150b:ba92/64 scope link stable-privacy 
       valid_lft forever preferred_lft forever
[root@client ~]# iperf3 -c 10.10.10.1 -t 40 -i 5
Connecting to host 10.10.10.1, port 5201
[  5] local 10.10.10.2 port 42956 connected to 10.10.10.1 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-5.01   sec  62.7 MBytes   105 Mbits/sec   33    110 KBytes       
[  5]   5.01-10.01  sec  61.7 MBytes   103 Mbits/sec   24   97.8 KBytes       
[  5]  10.01-15.00  sec  60.2 MBytes   101 Mbits/sec   25    115 KBytes       
[  5]  15.00-20.01  sec  59.7 MBytes   100 Mbits/sec   20    100 KBytes       
[  5]  20.01-25.00  sec  60.5 MBytes   102 Mbits/sec   33    111 KBytes       
[  5]  25.00-30.00  sec  61.3 MBytes   103 Mbits/sec   31   93.8 KBytes       
[  5]  30.00-35.00  sec  61.9 MBytes   104 Mbits/sec   32   29.1 KBytes       
[  5]  35.00-40.01  sec  61.9 MBytes   104 Mbits/sec   24    115 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-40.01  sec   490 MBytes   103 Mbits/sec  222             sender
[  5]   0.00-40.06  sec   490 MBytes   103 Mbits/sec                  receiver

iperf Done.
```

Как видно, в режиме tun скорость чуть больше. Разница между режимами в том, что они работают на разныхт уровнях сети. `tap` - второй уровень, а `tun` - третий. 

---

### Поднимаем RAS на базе OpenVPN с клиентскими сертификатами, подключаемся с локальной машины на виртуалку

В `Vagrantfile` оставляем только `server`, а в `provision` меняем `provision.yaml` на `provisionRAS.yaml`.

Плейбук выполняет настройку сервера и забирает оттуда файлы ключей.

При подключении с хоста возникла ошибка при добавлении маршрута:
```
Fri May  3 23:51:36 2024 /usr/bin/ip route add 192.168.56.0/24 via 10.10.10.5
RTNETLINK answers: File exists
Fri May  3 23:51:36 2024 ERROR: Linux route add command failed: external program exited with error status: 2
```

Решение: в файле `client.conf` изменил строку `route 192.168.56.0 255.255.255.0` на `route 192.168.56.0/24`.

Результат: пинг идёт
```
[andrej@home-srv OTUS-Task22]$ ping 10.10.10.1
PING 10.10.10.1 (10.10.10.1) 56(84) bytes of data.
64 bytes from 10.10.10.1: icmp_seq=1 ttl=64 time=0.446 ms
64 bytes from 10.10.10.1: icmp_seq=2 ttl=64 time=0.382 ms
```

и маршрут в таблице появился
```
[andrej@home-srv OTUS-Task22]$ ip r
default via 192.168.20.1 dev eth0 proto static metric 100 
10.10.10.0/24 via 192.168.56.1 dev vboxnet0 
10.10.10.5 dev tun0 proto kernel scope link src 10.10.10.6 
192.168.20.0/24 dev eth0 proto kernel scope link src 192.168.20.30 metric 100 
192.168.56.0/24 dev vboxnet0 proto kernel scope link src 192.168.56.1 
192.168.100.0/24 dev virbr1 proto kernel scope link src 192.168.100.1 linkdown 
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1 linkdown
```

### Самостоятельно изучить, поднять ocserv и подключиться с хоста к виртуалке

Хорошая штука, траффик выглядит как https.  
В `Vagrantfile` добавляем проброс портов и меняем имя плейбука на `provisionOCserv.yaml`.
Ansible устанавливает ocserv, создаёт сертификаты, копирует [файл конфигурации](ansible/templates/ocserv.conf) и запускает сервис. 

Проверяем его работу:

Подключаемся с хостовой машины к vpn ocserv, соглашаемся доверять серверу, вводим имя пользователя и пароль.
```bash
[andrej@home-srv caservclient]$ sudo openconnect 127.0.0.1:4443
POST https://127.0.0.1:4443/
Connected to 127.0.0.1:4443
SSL negotiation with 127.0.0.1
Server certificate verify failed: signer not found

Certificate from VPN server "127.0.0.1" failed verification.
Reason: signer not found
To trust this server in future, perhaps add this to your command line:
    --servercert pin-sha256:Sh/WdGzTqJnIwgsHWblbshuqdjYrlJnKMYOgV6PhCuw=
Enter 'yes' to accept, 'no' to abort; anything else to view: yes
Connected to HTTPS on 127.0.0.1 with ciphersuite (TLS1.3)-(ECDHE-SECP256R1)-(RSA-PSS-RSAE-SHA256)-(AES-256-GCM)
XML POST enabled
Please enter your username.
Username:vagrant
POST https://127.0.0.1:4443/auth
Please enter your password.
Password:
POST https://127.0.0.1:4443/auth
Got CONNECT response: HTTP/1.1 200 CONNECTED
CSTP connected. DPD 90, Keepalive 32400
DTLS handshake failed: Error in the push function.
(Is a firewall preventing you from sending UDP packets?)
Set up UDP failed; using SSL instead
Connected as 10.10.50.6, using SSL, with DTLS disabled
Error: any valid prefix is expected rather than "dev".
```
Соединение установлено. Не понял, что за ошибка такая, с этим надо ещё разбираться, однако интерфейс создался:
```bash
5: vpns0: <POINTOPOINT,UP,LOWER_UP> mtu 1434 qdisc fq_codel state UNKNOWN group default qlen 500
    link/none 
    inet 10.10.50.1 peer 10.10.50.6/32 scope global vpns0
       valid_lft forever preferred_lft forever
    inet6 fe80::fe84:93f2:bc6a:af9a/64 scope link stable-privacy 
       valid_lft forever preferred_lft forever
```

и связь с сервером по 10.10.50.1 есть:

```bash
[andrej@home-srv OTUS-Task22]$ ping -c 4 10.10.50.1
PING 10.10.50.1 (10.10.50.1) 56(84) bytes of data.
64 bytes from 10.10.50.1: icmp_seq=1 ttl=64 time=0.854 ms
64 bytes from 10.10.50.1: icmp_seq=2 ttl=64 time=0.656 ms
64 bytes from 10.10.50.1: icmp_seq=3 ttl=64 time=0.912 ms
64 bytes from 10.10.50.1: icmp_seq=4 ttl=64 time=0.674 ms

--- 10.10.50.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3053ms
rtt min/avg/max/mdev = 0.656/0.774/0.912/0.111 ms
[andrej@home-srv OTUS-Task22]$ 
```
