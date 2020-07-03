# Мосты, туннели и VPN 

Для того, чтобы развернуть стенд в режиме _tun_ или _tap_ между виртуальными машинами client и server, необходимо изменить _mode_ в [provision.yml](https://github.com/awesomenmi/vpn/blob/master/provision.yml) (по умолчанию выствлен режим _tun_).

Вывод команды ```ip a``` для вирутальной машины **client**:
```
[root@client vagrant]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 52:54:00:8a:fe:e6 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic eth0
       valid_lft 79359sec preferred_lft 79359sec
    inet6 fe80::5054:ff:fe8a:fee6/64 scope link 
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:8a:57:98 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.2/24 brd 192.168.0.255 scope global noprefixroute eth1
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe8a:5798/64 scope link 
       valid_lft forever preferred_lft forever
4: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 100
    link/none 
    inet 10.10.10.2 peer 10.10.10.1/32 scope global tun0
       valid_lft forever preferred_lft forever
    inet6 fe80::464:bf64:a84:19d6/64 scope link flags 800 
       valid_lft forever preferred_lft forever
```

Вывод команды ```ip a```  и ```ping``` для вирутальной машины **server**:
```
[root@server vagrant]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 52:54:00:8a:fe:e6 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic eth0
       valid_lft 79266sec preferred_lft 79266sec
    inet6 fe80::5054:ff:fe8a:fee6/64 scope link 
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:4f:4b:2f brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.1/24 brd 192.168.0.255 scope global noprefixroute eth1
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe4f:4b2f/64 scope link 
       valid_lft forever preferred_lft forever
4: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 100
    link/none 
    inet 10.10.10.1 peer 10.10.10.2/32 scope global tun0
       valid_lft forever preferred_lft forever
    inet6 fe80::3149:7722:e57a:8728/64 scope link flags 800 
       valid_lft forever preferred_lft forever
```

```
[root@server vagrant]# ping -c 5 10.10.10.2
PING 10.10.10.2 (10.10.10.2) 56(84) bytes of data.
64 bytes from 10.10.10.2: icmp_seq=1 ttl=64 time=0.662 ms
64 bytes from 10.10.10.2: icmp_seq=2 ttl=64 time=0.665 ms
64 bytes from 10.10.10.2: icmp_seq=3 ttl=64 time=0.670 ms
64 bytes from 10.10.10.2: icmp_seq=4 ttl=64 time=0.689 ms
64 bytes from 10.10.10.2: icmp_seq=5 ttl=64 time=0.655 ms

--- 10.10.10.2 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4003ms
rtt min/avg/max/mdev = 0.655/0.668/0.689/0.020 ms
```

Проверка скорости соединения через _iperf_ для _tun_:
```
[vagrant@client ~]$ iperf -c 10.10.10.1 -t 20 -i 5
------------------------------------------------------------
Client connecting to 10.10.10.1, TCP port 5001
TCP window size:  117 KByte (default)
------------------------------------------------------------
[  3] local 10.10.10.2 port 55262 connected with 10.10.10.1 port 5001
[ ID] Interval       Transfer     Bandwidth
[  3]  0.0- 5.0 sec   151 MBytes   253 Mbits/sec
[  3]  5.0-10.0 sec   157 MBytes   263 Mbits/sec
[  3] 10.0-15.0 sec   156 MBytes   261 Mbits/sec
[  3] 15.0-20.0 sec   156 MBytes   261 Mbits/sec
[  3]  0.0-20.0 sec   619 MBytes   260 Mbits/sec
```

Проверка скорости соединения через _iperf_ для _tap_:
```
[vagrant@client ~]$ iperf -c 10.10.10.1 -t 20 -i 5 -p 5000
------------------------------------------------------------
Client connecting to 10.10.10.1, TCP port 5000
TCP window size: 90.0 KByte (default)
------------------------------------------------------------
[  3] local 10.10.10.2 port 55666 connected with 10.10.10.1 port 5000
[ ID] Interval       Transfer     Bandwidth
[  3]  0.0- 5.0 sec   150 MBytes   251 Mbits/sec
[  3]  5.0-10.0 sec   138 MBytes   231 Mbits/sec
[  3] 10.0-15.0 sec   145 MBytes   243 Mbits/sec
[  3] 15.0-20.0 sec   146 MBytes   244 Mbits/sec
[  3]  0.0-20.0 sec   578 MBytes   243 Mbits/sec
```
___
Для подключения к машине **ovpnserver** необходимо скопировать с нее файлы _ca.crt, client.crt и client.key_ из каталога _/etc/openvpn_ и положить их рядом с _client.conf_ из корневого каталога стенда.

В client.conf необходимо заменить ip-адрес сервера в опции ```remote``` на ip-адрес интерфейса _eth2_:
```
[root@ovpnserver vagrant]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 52:54:00:8a:fe:e6 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic eth0
       valid_lft 84740sec preferred_lft 84740sec
    inet6 fe80::5054:ff:fe8a:fee6/64 scope link 
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:5e:ee:9f brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.102/24 brd 192.168.0.255 scope global noprefixroute dynamic eth1
       valid_lft 5541sec preferred_lft 5541sec
    inet6 fe80::a00:27ff:fe5e:ee9f/64 scope link 
       valid_lft forever preferred_lft forever
4: eth2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:fa:76:85 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.5/24 brd 192.168.0.255 scope global noprefixroute eth2
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fefa:7685/64 scope link 
       valid_lft forever preferred_lft forever
5: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 100
    link/none 
    inet 10.0.0.1 peer 10.0.0.2/32 scope global tun0
       valid_lft forever preferred_lft forever
    inet6 fe80::52b8:71bb:9d9a:d73b/64 scope link flags 800 
       valid_lft forever preferred_lft forever
```
Затем необходимо открыть client.conf с помощью openvpn-приложения, например: ```openvpn --config client.conf```

![alt-текст](https://github.com/awesomenmi/vpn/blob/master/Screenshot%20from%202020-07-03%2023-30-23.png)

![alt-текст](https://github.com/awesomenmi/vpn/blob/master/Screenshot%20from%202020-07-03%2023-30-40.png)
