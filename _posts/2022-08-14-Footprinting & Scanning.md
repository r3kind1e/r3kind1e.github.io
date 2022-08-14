---
layout: post
title: "Footprinting & Scanning"
date: "2022-08-14"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Assessment Methodologies
    - Footprinting & Scanning
---

# Footprinting & Scanning（足迹和扫描）
## Mapping a Network（映射网络）
### Purpose（目的）
* Scope（范围）
* Discovery（发现）
### Process（过程）
* Physical Access（物理访问）
    * Physical Security（物理安全）
    * OSINT
    * Social Engineering（社会工程学）
* Sniffing（嗅探）
    * Passive Reconnaissance（被动侦察）
    * Watch network traffic（观察网络流量）
* ARP
    * Address Resolution Protocol (RFC 826)（地址解析协议）
        * Resolve IP to MAC（将 IP 解析为 MAC）
* ICMP
    * Internet Control Message Protocol(RFC 792)（互联网控制消息协议）
    * Traceroute（跟踪路由）
    * Ping
        * Type 8 - echo request（类型 8 - 回显请求）

### Tools（工具）
* WIRESHARK
* ARP-SCAN
* PING
* FPING
* NMAP
* ZENMAP

#### WIRESHARK
#### ARP-SCAN

```
sudo arp-scan -I eth1 -g 10.10.16.0/24
```

```
root@attackdefense:~# sudo arp-scan -I eth1 -g 10.10.16.0/24
sudo: setrlimit(RLIMIT_CORE): Operation not permitted
Interface: eth1, type: EN10MB, MAC: 02:42:0a:0a:10:04, IPv4: 10.10.16.4
Starting arp-scan 1.9.7 with 256 hosts (https://github.com/royhills/arp-scan)
10.10.16.1	02:42:e6:22:d0:bd	(Unknown: locally administered)
10.10.16.2	02:42:0a:0a:10:02	(Unknown: locally administered)

2 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.9.7: 256 hosts scanned in 2.022 seconds (126.61 hosts/sec). 2 responded
```

```
sudo arp-scan -I eth0 -g 10.1.0.0/16
```

```
root@attackdefense:~# sudo arp-scan -I eth0 -g 10.1.0.0/16
sudo: setrlimit(RLIMIT_CORE): Operation not permitted
Interface: eth0, type: EN10MB, MAC: 02:42:0a:01:00:03, IPv4: 10.1.0.3
Starting arp-scan 1.9.7 with 65536 hosts (https://github.com/royhills/arp-scan)
10.1.0.1	02:42:f1:b5:4b:71	(Unknown: locally administered)
10.1.0.2	02:42:0a:01:00:02	(Unknown: locally administered)
10.1.0.4	02:42:0a:01:00:04	(Unknown: locally administered)
10.1.0.5	02:42:0a:01:00:05	(Unknown: locally administered)
10.1.0.6	02:42:0a:01:00:06	(Unknown: locally administered)
10.1.0.7	02:42:0a:01:00:07	(Unknown: locally administered)
10.1.0.8	02:42:0a:01:00:08	(Unknown: locally administered)
10.1.0.9	02:42:0a:01:00:09	(Unknown: locally administered)

10 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.9.7: 65536 hosts scanned in 263.123 seconds (249.07 hosts/sec). 8 responded
```

#### PING
#### FPING
```
fping -I eth1 -g 10.10.16.0/24 -a
```

```
fping -I eth0 -g 10.1.0.0/16 -a 2>/dev/null
```

#### NMAP
```
nmap -sn 10.10.16.0/24
```

```
root@attackdefense:~# nmap -sn 10.10.16.0/24
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-13 10:34 IST
Nmap scan report for linux (10.10.16.1)
Host is up (0.000039s latency).
MAC Address: 02:42:E6:22:D0:BD (Unknown)
Nmap scan report for dwmc73izhcaiwbux1nc1x.aws-bridge (10.10.16.2)
Host is up (0.000023s latency).
MAC Address: 02:42:0A:0A:10:02 (Unknown)
Nmap scan report for attackdefense.com (10.10.16.4)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 2.07 seconds
```

#### ZENMAP
GUI版的nmap。

## Windows Recon: Zenmap（Windows 侦察：Zenmap）
```
ipconfig /all
```

```
IPv4 Address    10.4.25.251(Preferred)
Subnet Mask     255.255.240.0
Default Gateway 10.4.16.1
```

可用的主机IP地址范围是：10.4.16.1 - 10.4.31.254。网络地址是10.4.16.0。CIDR表示法是`/20`。

所以将Zenmap的Target设置为`10.4.16.0/20`，Profile选择`Ping scan`。得到的Command为`nmap -sn 10.4.16.0/20`。

Profile选择`Intense scan`，得到的Command为`nmap -T4 -A -v 10.4.16.0/20`。

扫描完成后，在Hosts列表中可以看到扫描的主机，在`Topology->Fisheye`可以看到关系图。在`Host Details`可以看到主机详细信息。在`Ports/Hosts`可以看到端口、协议、状态、服务、版本。

## Windows Recon: Zenmap（Windows 侦察：Zenmap）
### 概述
为您提供 Windows 机器（Server 2012）。

您的任务是使用提供的 Zenmap 工具发现实时主机。您需要关注的子网掩码是“255.255.240.0”和 CIDR 20。 

[Zenmap](https://nmap.org/zenmap/)

Nmap 工具的 GUI 版本
* 易于初学者使用
* 能够将扫描数据存储到数据库中。
* 根据扫描结果（即服务、主机名等）绘制网络图。
* 目标：发现所有可用的存活主机。

**指示：**

* 您可以通过在命令提示符下运行“ipconfig”命令来检查机器的 IP 地址，即 cmd.exe
* 不要攻击位于 IP 地址 10.0.0.1 的网关

### 我自己的思路
```
PS C:\Users\Administrator> ipconfig /all

Windows IP Configuration

   Host Name . . . . . . . . . . . . : AttackDefense
   Primary Dns Suffix  . . . . . . . :
   Node Type . . . . . . . . . . . . : Hybrid
   IP Routing Enabled. . . . . . . . : No
   WINS Proxy Enabled. . . . . . . . : No
   DNS Suffix Search List. . . . . . : ap-southeast-1.ec2-utilities.amazonaws.com
                                       ap-southeast-1.compute.internal

Ethernet adapter Ethernet:

   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
   Description . . . . . . . . . . . : AWS PV Network Device #0
   Physical Address. . . . . . . . . : 06-5E-13-3B-8D-A4
   DHCP Enabled. . . . . . . . . . . : Yes
   Autoconfiguration Enabled . . . . : Yes
   Link-local IPv6 Address . . . . . : fe80::2107:74cb:d44a:ce4e%4(Preferred)
   IPv4 Address. . . . . . . . . . . : 10.0.18.8(Preferred)
   Subnet Mask . . . . . . . . . . . : 255.255.240.0
   Lease Obtained. . . . . . . . . . : Saturday, August 13, 2022 11:01:21 AM
   Lease Expires . . . . . . . . . . : Saturday, August 13, 2022 12:01:20 PM
   Default Gateway . . . . . . . . . : 10.0.16.1
   DHCP Server . . . . . . . . . . . : 10.0.16.1
   DHCPv6 IAID . . . . . . . . . . . : 118418632
   DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-2A-89-3C-D8-06-5E-13-3B-8D-A4
   DNS Servers . . . . . . . . . . . : 10.0.0.2
   NetBIOS over Tcpip. . . . . . . . : Enabled
```

当前机器的IP地址是10.0.18.8，子网掩码是255.255.240.0。根据计算，网络地址是10.0.16.0。可用的主机IP范围是10.0.16.1 - 10.0.31.254。CIDR表示法是`/20`。观察到默认网关的地址是可用的主机IP中的第一个。

将Target设置为`10.0.16.0/20`，Profile选择`Ping scan`，得到的命令是`nmap -sn 10.0.16.0/20`。

```
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-13 11:10 Coordinated Universal Time
Nmap scan report for ip-10-0-16-1.ap-southeast-1.compute.internal (10.0.16.1)
Host is up (0.00s latency).
MAC Address: 06:73:41:C8:CA:42 (Unknown)
Nmap scan report for ip-10-0-17-202.ap-southeast-1.compute.internal (10.0.17.202)
Host is up (0.00s latency).
MAC Address: 06:18:68:D6:C4:5E (Unknown)
Nmap scan report for ip-10-0-17-231.ap-southeast-1.compute.internal (10.0.17.231)
Host is up (0.00s latency).
MAC Address: 06:F5:51:A6:67:20 (Unknown)
Nmap scan report for ip-10-0-18-97.ap-southeast-1.compute.internal (10.0.18.97)
Host is up (0.00s latency).
MAC Address: 06:5D:32:AD:56:00 (Unknown)
Nmap scan report for ip-10-0-18-109.ap-southeast-1.compute.internal (10.0.18.109)
Host is up (0.00s latency).
MAC Address: 06:5D:9C:A1:14:DC (Unknown)
Nmap scan report for ip-10-0-20-5.ap-southeast-1.compute.internal (10.0.20.5)
Host is up (0.00s latency).
MAC Address: 06:46:D2:3A:64:C6 (Unknown)
Nmap scan report for ip-10-0-21-82.ap-southeast-1.compute.internal (10.0.21.82)
Host is up (0.00s latency).
MAC Address: 06:AC:65:4D:47:E4 (Unknown)
Nmap scan report for ip-10-0-22-246.ap-southeast-1.compute.internal (10.0.22.246)
Host is up (0.00s latency).
MAC Address: 06:3F:C9:51:12:76 (Unknown)
Nmap scan report for ip-10-0-23-102.ap-southeast-1.compute.internal (10.0.23.102)
Host is up (0.00s latency).
MAC Address: 06:D3:33:78:CE:B8 (Unknown)
Nmap scan report for ip-10-0-24-47.ap-southeast-1.compute.internal (10.0.24.47)
Host is up (0.00s latency).
MAC Address: 06:6D:76:FA:CD:9E (Unknown)
Nmap scan report for ip-10-0-24-176.ap-southeast-1.compute.internal (10.0.24.176)
Host is up (0.00s latency).
MAC Address: 06:20:14:AB:88:8C (Unknown)
Nmap scan report for ip-10-0-30-59.ap-southeast-1.compute.internal (10.0.30.59)
Host is up (0.00s latency).
MAC Address: 06:D0:09:0F:A1:A8 (Unknown)
Nmap scan report for ip-10-0-31-103.ap-southeast-1.compute.internal (10.0.31.103)
Host is up (0.00s latency).
MAC Address: 06:4D:7A:33:38:C2 (Unknown)
Nmap scan report for ip-10-0-18-8.ap-southeast-1.compute.internal (10.0.18.8)
Host is up.
Nmap done: 4096 IP addresses (14 hosts up) scanned in 19.24 seconds
```

Profile选择`Ping scan`，得到的命令是`nmap -T4 -A -v 10.0.16.0/20`。

![10-0-18-8-ports-hosts.png](/img/in-post/ine/10-0-18-8-ports-hosts.png)

![10-0-18-8-hosts-details.png](/img/in-post/ine/10-0-18-8-hosts-details.png)

![10-0-23-102-ports-hosts.png](/img/in-post/ine/10-0-23-102-ports-hosts.png)

![10-0-23-102-host-details.png](/img/in-post/ine/10-0-23-102-host-details.png)

![10-0-24-47-ports-hosts.png](/img/in-post/ine/10-0-24-47-ports-hosts.png)

![10-0-24-47-host-details.png](/img/in-post/ine/10-0-24-47-host-details.png)

### 解决方案
此实验室的解决方案可在以下手册中找到： https://assets.ine.com/labs/ad-manuals/walkthrough-2218.pdf

## Scan the Server 1
```
ip a 
```

eth1的IP地址是192.148.40.2/24

```
ping 192.148.40.3
nmap 192.148.40.3
nmap 192.148.40.3 -p-
nmap 192.148.40.3 -p 6421,41288,55413 -sV
```

## Scan the Server 1 Lab
```
root@attackdefense:~# ip a 
22784: eth1@if22785: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:76:8a:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.118.138.2/24 brd 192.118.138.255 scope global eth1
       valid_lft forever preferred_lft forever
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
22781: eth0@if22782: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:08 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.8/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
```

```
root@attackdefense:~# nmap -sn 192.118.138.0/24
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-13 14:31 UTC
Nmap scan report for linux (192.118.138.1)
Host is up (0.000056s latency).
MAC Address: 02:42:27:10:6D:F7 (Unknown)
Nmap scan report for target-1 (192.118.138.3)
Host is up (0.000012s latency).
MAC Address: 02:42:C0:76:8A:03 (Unknown)
Nmap scan report for attackdefense.com (192.118.138.2)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 2.02 seconds
```

```
root@attackdefense:~# nmap 192.118.138.3
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-13 14:32 UTC
Nmap scan report for target-1 (192.118.138.3)
Host is up (0.0000090s latency).
All 1000 scanned ports on target-1 (192.118.138.3) are closed
MAC Address: 02:42:C0:76:8A:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.24 seconds
```

```
root@attackdefense:~# nmap 192.118.138.3 -p-
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-13 14:33 UTC
Nmap scan report for target-1 (192.118.138.3)
Host is up (0.0000090s latency).
Not shown: 65532 closed ports
PORT      STATE SERVICE
6421/tcp  open  nim-wan
41288/tcp open  unknown
55413/tcp open  unknown
MAC Address: 02:42:C0:76:8A:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 1.15 seconds
```

```
root@attackdefense:~# nmap 192.118.138.3 -p 6421,41288,55413 -sV -O
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-13 14:35 UTC
Nmap scan report for target-1 (192.118.138.3)
Host is up (0.000038s latency).

PORT      STATE SERVICE   VERSION
6421/tcp  open  mongodb   MongoDB 2.6.10
41288/tcp open  memcached Memcached
55413/tcp open  ftp       vsftpd 3.0.3
MAC Address: 02:42:C0:76:8A:03 (Unknown)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 2.6.32 (96%), Linux 3.2 - 4.9 (96%), Linux 2.6.32 - 3.10 (96%), Linux 3.4 - 3.10 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), Synology DiskStation Manager 5.2-5644 (94%), Netgear RAIDiator 4.2.28 (94%), Linux 2.6.32 - 2.6.35 (94%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop
Service Info: OS: Unix

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 15.27 seconds
```

## Scan the Server 2
```
ip a
```

我们的IP是192.206.172.2，目标是192.206.172.3。

```
ping 192.206.172.3
```

```
nmap 192.206.172.3 -p 1-250
```

```
nmap 192.206.172.3 -p 177 -A
```

根据扫描结果，TCP端口177开放，服务是domain，版本是ISC BIND 9.10.3-P4 (Ubuntu Linux)。ISC BIND是DNS服务器。所以猜测这个服务也有UDP。

```
nmap 192.206.172.3 -p 1-250 -sU
```

```
PORT    STATE           SERVICE
134/udp open|filtered   ingres-net
177/udp open|filtered   xdmcp
234/udp open|filtered   unknown
```

```
nmap 192.206.172.3 -p 134,177,234 -sUV
```

```
PORT    STATE           SERVICE         VERSION
134/udp open|filtered   ingres-net
177/udp open            domain          ISC BIND 9.10.3-P4 (Ubuntu Linux)
234/udp open            snmp            SNMPv1 server; net-snmp SNMPv3 server (public)
```

```
nmap 192.206.172.3 -p 134 -sUVC
nmap 192.206.172.3 -p 134 -sUV --script=discovery
```

```
tftp 192.206.172.3 134
tftp> status
```

## Scan the Server 2 Lab（扫描服务器 2）
```
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
23262: eth0@if23263: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:04 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.4/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
23265: eth1@if23266: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:75:f3:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.117.243.2/24 brd 192.117.243.255 scope global eth1
       valid_lft forever preferred_lft forever
```

```
root@attackdefense:~# nmap -sn 192.117.243.0/24
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-14 08:09 UTC
Nmap scan report for Tonys-IGLD-243-1.inter.net.il (192.117.243.1)
Host is up (0.000046s latency).
MAC Address: 02:42:F2:EB:06:E7 (Unknown)
Nmap scan report for target-1 (192.117.243.3)
Host is up (0.000019s latency).
MAC Address: 02:42:C0:75:F3:03 (Unknown)
Nmap scan report for attackdefense.com (192.117.243.2)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 2.04 seconds
```

```
root@attackdefense:~# ping 192.117.243.3 -c 4
PING 192.117.243.3 (192.117.243.3) 56(84) bytes of data.
64 bytes from 192.117.243.3: icmp_seq=1 ttl=64 time=0.063 ms
64 bytes from 192.117.243.3: icmp_seq=2 ttl=64 time=0.036 ms
64 bytes from 192.117.243.3: icmp_seq=3 ttl=64 time=0.045 ms
64 bytes from 192.117.243.3: icmp_seq=4 ttl=64 time=0.054 ms

--- 192.117.243.3 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 57ms
rtt min/avg/max/mdev = 0.036/0.049/0.063/0.012 ms
```

```
root@attackdefense:~# nmap 192.117.243.3 -p 1-250
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-14 08:12 UTC
Nmap scan report for target-1 (192.117.243.3)
Host is up (0.000010s latency).
Not shown: 249 closed ports
PORT    STATE SERVICE
177/tcp open  xdmcp
MAC Address: 02:42:C0:75:F3:03 (Unknown)
```

在TCP 177端口上运行着domain服务，版本是ISC BIND 9.10.3-P4 (Ubuntu Linux)。

```
root@attackdefense:~# nmap 192.117.243.3 -p 177 -sV
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-14 08:14 UTC
Nmap scan report for target-1 (192.117.243.3)
Host is up (0.000066s latency).

PORT    STATE SERVICE VERSION
177/tcp open  domain  ISC BIND 9.10.3-P4 (Ubuntu Linux)
MAC Address: 02:42:C0:75:F3:03 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 31.65 seconds
```

DNS常用的端口是UDP 53，所以对UDP端口进行扫描。

```
root@attackdefense:~# nmap 192.117.243.3 -p1-250 -sU
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-14 08:22 UTC
Nmap scan report for target-1 (192.117.243.3)
Host is up (0.000077s latency).
Not shown: 247 closed ports
PORT    STATE         SERVICE
134/udp open|filtered ingres-net
177/udp open|filtered xdmcp
234/udp open|filtered unknown
MAC Address: 02:42:C0:75:F3:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 266.22 seconds
```

UDP 177端口上运行domain服务，版本是ISC BIND 9.10.3-P4 (Ubuntu Linux)。UDP 234端口上运行snmp服务，版本是SNMPv1 server; net-snmp SNMPv3 server (public)。

```
root@attackdefense:~# nmap 192.117.243.3 -p 134,177,234 -sUV
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-14 08:28 UTC
Nmap scan report for target-1 (192.117.243.3)
Host is up (0.000061s latency).

PORT    STATE         SERVICE    VERSION
134/udp open|filtered ingres-net
177/udp open          domain     ISC BIND 9.10.3-P4 (Ubuntu Linux)
234/udp open          snmp       SNMPv1 server; net-snmp SNMPv3 server (public)
MAC Address: 02:42:C0:75:F3:03 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 100.42 seconds
```

```
nmap 192.117.243.3 -p 134 -sUVC
```

```
root@attackdefense:~# nmap 192.117.243.3 -p 134 -sUV --script=discovery
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-14 08:37 UTC
Pre-scan script results:
| broadcast-igmp-discovery: 
|   10.1.0.1
|     Interface: eth0
|     Version: 2
|     Group: 224.0.0.106
|     Description: All-Snoopers (rfc4286)
|   192.117.243.1
|     Interface: eth1
|     Version: 2
|     Group: 224.0.0.106
|     Description: All-Snoopers (rfc4286)
|_  Use the newtargets script-arg to add the results as targets
| targets-asn: 
|_  targets-asn.asn is a mandatory parameter
Nmap scan report for target-1 (192.117.243.3)
Host is up (0.000038s latency).

PORT    STATE         SERVICE    VERSION
134/udp open|filtered ingres-net
MAC Address: 02:42:C0:75:F3:03 (Unknown)

Host script results:
| asn-query: 
| BGP: 192.117.240.0/21 and 192.117.240.0/20 | Country: IL
|   Origin AS: 12400 - PARTNER-AS, IL
|_    Peer AS: 9116
|_dns-brute: Can't guess domain of "target-1"; use dns-brute.domain script argument.
|_fcrdns: PASS (7wrhuc6nx5ahd48u12kr1.temp-network_a-117-243)
| hostmap-crtsh: 
|_  subdomains: Error: could not GET http://crt.sh/?q=%.target-1&output=json
| hostmap-ip2hosts: 
|_  hosts: Error: could not GET http://www.ip2hosts.com/csv.php?ip=192.117.243.3
|_hostmap-robtex: ERROR: Script execution failed (use -d to debug)
|_ip-geolocation-geoplugin: bad argument #2 to 'lpeg.match' (string expected, got nil)
|_sniffer-detect: Likely in promiscuous mode (tests: "11111111")
|_whois-domain: You should provide a domain name.
|_whois-ip: ERROR: Script execution failed (use -d to debug)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 153.92 seconds
```

根据提示，运行 TFTP 服务器的端口很可能是udp 134。

```
root@attackdefense:~# tftp 192.117.243.3 134
tftp> status
Connected to 192.117.243.3.
Mode: netascii Verbose: off Tracing: off
Rexmt-interval: 5 seconds, Max-timeout: 25 seconds
tftp> quit
```

## Scan the Server 3
```
ip a
```
Kali的IP是192.57.232.2/24。

```
ping 192.57.232.3
```

`-T4` for speed.

```
nmap 192.57.232.3 -T4
nmap 192.57.232.3 -T4 -p-
```

```
nmap 192.57.232.3 -T4 -sU
```

```
PORT    STATE   SERVICE
161/udp open    snmp
```

```
nmap 192.57.232.3 -T4 -sU -p 161 -A
```
## Scan the Server 3

```
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
23372: eth0@if23373: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:05 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.5/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
23375: eth1@if23376: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:d8:15:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.216.21.2/24 brd 192.216.21.255 scope global eth1
       valid_lft forever preferred_lft forever
```

```
root@attackdefense:~# nmap -sn 192.216.21.0/24
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-14 10:37 UTC
Nmap scan report for linux (192.216.21.1)
Host is up (0.000056s latency).
MAC Address: 02:42:BB:BB:70:BF (Unknown)
Nmap scan report for target-1 (192.216.21.3)
Host is up (0.000022s latency).
MAC Address: 02:42:C0:D8:15:03 (Unknown)
Nmap scan report for attackdefense.com (192.216.21.2)
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 10.68 seconds
```

```
root@attackdefense:~# ping 192.216.21.3 -c 4
PING 192.216.21.3 (192.216.21.3) 56(84) bytes of data.
64 bytes from 192.216.21.3: icmp_seq=1 ttl=64 time=0.101 ms
64 bytes from 192.216.21.3: icmp_seq=2 ttl=64 time=0.074 ms
64 bytes from 192.216.21.3: icmp_seq=3 ttl=64 time=0.073 ms
64 bytes from 192.216.21.3: icmp_seq=4 ttl=64 time=0.078 ms

--- 192.216.21.3 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 70ms
rtt min/avg/max/mdev = 0.073/0.081/0.101/0.014 ms
```

```
root@attackdefense:~# nmap 192.216.21.3 -T4 -p-
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-14 10:40 UTC
Nmap scan report for target-1 (192.216.21.3)
Host is up (0.0000090s latency).
All 65535 scanned ports on target-1 (192.216.21.3) are closed
MAC Address: 02:42:C0:D8:15:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 1.28 seconds
```

没有开放的TCP端口。尝试对UDP端口进行扫描。

```
root@attackdefense:~# nmap 192.216.21.3 -T4 -sU
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-14 10:41 UTC
Nmap scan report for target-1 (192.216.21.3)
Host is up (0.000073s latency).
Not shown: 955 closed ports, 44 open|filtered ports
PORT    STATE SERVICE
161/udp open  snmp
MAC Address: 02:42:C0:D8:15:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 1031.94 seconds
```

```
root@attackdefense:~# nmap 192.216.21.3 -T4 -sU -p 161 -A 
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-14 11:01 UTC
Nmap scan report for target-1 (192.216.21.3)
Host is up (0.000038s latency).

PORT    STATE SERVICE VERSION
161/udp open  snmp    SNMPv1 server; net-snmp SNMPv3 server (public)
| snmp-info: 
|   enterprise: net-snmp
|   engineIDFormat: unknown
|   engineIDData: 8de76845eecff86200000000
|   snmpEngineBoots: 1
|_  snmpEngineTime: 26m05s
| snmp-interfaces: 
|   lo
|     IP address: 127.0.0.1  Netmask: 255.0.0.0
|     Type: softwareLoopback  Speed: 10 Mbps
|     Traffic stats: 11.20 Kb sent, 11.20 Kb received
|   eth0
|     IP address: 192.216.21.3  Netmask: 255.255.255.0
|     MAC address: 02:42:c0:d8:15:03 (Unknown)
|     Type: ethernetCsmacd  Speed: 4 Gbps
|_    Traffic stats: 7.21 Mb sent, 7.76 Mb received
| snmp-netstat: 
|   TCP  127.0.0.1:22         0.0.0.0:0
|   TCP  127.0.0.1:22         127.0.0.1:60206
|   TCP  127.0.0.1:1313       0.0.0.0:0
|   TCP  127.0.0.1:60206      127.0.0.1:22
|   TCP  127.0.0.11:36059     0.0.0.0:0
|   UDP  0.0.0.0:161          *:*
|   UDP  0.0.0.0:47506        *:*
|_  UDP  127.0.0.11:41997     *:*
| snmp-processes: 
|   1: 
|     Name: supervisord
|     Path: /usr/bin/python
|     Params: /usr/bin/supervisord -n
|   11: 
|     Name: snmpd
|     Path: snmpd
|     Params: -c /etc/snmp/snmpd.conf
|   24: 
|     Name: sshd
|     Path: /usr/sbin/sshd
|   27: 
|     Name: listener
|     Path: /listener
|   28: 
|     Name: ssh
|     Path: ssh
|     Params: -i /opt/david_key -o StrictHostKeyChecking=no david@127.0.0.1
|   29: 
|     Name: sshd
|     Path: sshd: david [priv]
|   38: 
|     Name: sshd
|     Path: sshd: david@notty
|   39: 
|     Name: bash
|_    Path: -bash
| snmp-sysdescr: Linux victim-1 5.4.0-121-generic #137-Ubuntu SMP Wed Jun 15 13:33:07 UTC 2022 x86_64
|_  System uptime: 26m5.02s (156502 timeticks)
| snmp-win32-software: 
|   adduser-3.113+nmu3ubuntu4; 0-01-01T00:00:00
|   apt-1.2.29ubuntu0.1; 0-01-01T00:00:00
|   autotools-dev-20150820.1; 0-01-01T00:00:00
|   base-files-9.4ubuntu4.8; 0-01-01T00:00:00
|   base-passwd-3.5.39; 0-01-01T00:00:00
|   bash-4.3-14ubuntu1.2; 0-01-01T00:00:00
|   binutils-2.26.1-1ubuntu1~16.04.8; 0-01-01T00:00:00
|   bsdutils-1:2.27.1-6ubuntu3.6; 0-01-01T00:00:00
|   build-essential-12.1ubuntu2; 0-01-01T00:00:00
|   bzip2-1.0.6-8ubuntu0.1; 0-01-01T00:00:00
|   ca-certificates-20170717~16.04.2; 0-01-01T00:00:00
|   coreutils-8.25-2ubuntu3~16.04; 0-01-01T00:00:00
|   cpp-4:5.3.1-1ubuntu1; 0-01-01T00:00:00
|   cpp-5-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   dash-0.5.8-2.1ubuntu2; 0-01-01T00:00:00
|   debconf-1.5.58ubuntu1; 0-01-01T00:00:00
|   debianutils-4.7; 0-01-01T00:00:00
|   dh-python-2.20151103ubuntu1.1; 0-01-01T00:00:00
|   diffutils-1:3.3-3; 0-01-01T00:00:00
|   dpkg-1.18.4ubuntu1.5; 0-01-01T00:00:00
|   dpkg-dev-1.18.4ubuntu1.5; 0-01-01T00:00:00
|   e2fslibs-1.42.13-1ubuntu1; 0-01-01T00:00:00
|   e2fsprogs-1.42.13-1ubuntu1; 0-01-01T00:00:00
|   fakeroot-1.20.2-1ubuntu1; 0-01-01T00:00:00
|   file-1:5.25-2ubuntu1.2; 0-01-01T00:00:00
|   findutils-4.6.0+git+20160126-2; 0-01-01T00:00:00
|   g++-4:5.3.1-1ubuntu1; 0-01-01T00:00:00
|   g++-5-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   gcc-4:5.3.1-1ubuntu1; 0-01-01T00:00:00
|   gcc-5-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   gcc-5-base-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   gcc-6-base-6.0.1-0ubuntu1; 0-01-01T00:00:00
|   gnupg-1.4.20-1ubuntu3.3; 0-01-01T00:00:00
|   gpgv-1.4.20-1ubuntu3.3; 0-01-01T00:00:00
|   grep-2.25-1~16.04.1; 0-01-01T00:00:00
|   gzip-1.6-4ubuntu1; 0-01-01T00:00:00
|   hostname-3.16ubuntu2; 0-01-01T00:00:00
|   ifupdown-0.8.10ubuntu1.4; 0-01-01T00:00:00
|   init-1.29ubuntu4; 0-01-01T00:00:00
|   init-system-helpers-1.29ubuntu4; 0-01-01T00:00:00
|   initscripts-2.88dsf-59.3ubuntu2; 0-01-01T00:00:00
|   insserv-1.14.0-5ubuntu3; 0-01-01T00:00:00
|   iproute2-4.3.0-1ubuntu3.16.04.5; 0-01-01T00:00:00
|   isc-dhcp-client-4.3.3-5ubuntu12.10; 0-01-01T00:00:00
|   isc-dhcp-common-4.3.3-5ubuntu12.10; 0-01-01T00:00:00
|   krb5-locales-1.13.2+dfsg-5ubuntu2.1; 0-01-01T00:00:00
|   libacl1-2.2.52-3; 0-01-01T00:00:00
|   libalgorithm-diff-perl-1.19.03-1; 0-01-01T00:00:00
|   libalgorithm-diff-xs-perl-0.04-4build1; 0-01-01T00:00:00
|   libalgorithm-merge-perl-0.08-3; 0-01-01T00:00:00
|   libapparmor1-2.10.95-0ubuntu2.10; 0-01-01T00:00:00
|   libapt-pkg5.0-1.2.29ubuntu0.1; 0-01-01T00:00:00
|   libasan2-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   libatm1-1:2.5.1-1.5; 0-01-01T00:00:00
|   libatomic1-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   libattr1-1:2.4.47-2; 0-01-01T00:00:00
|   libaudit-common-1:2.4.5-1ubuntu2.1; 0-01-01T00:00:00
|   libaudit1-1:2.4.5-1ubuntu2.1; 0-01-01T00:00:00
|   libblkid1-2.27.1-6ubuntu3.6; 0-01-01T00:00:00
|   libbsd0-0.8.2-1; 0-01-01T00:00:00
|   libbz2-1.0-1.0.6-8ubuntu0.1; 0-01-01T00:00:00
|   libc-bin-2.23-0ubuntu11; 0-01-01T00:00:00
|   libc-dev-bin-2.23-0ubuntu11; 0-01-01T00:00:00
|   libc6-2.23-0ubuntu11; 0-01-01T00:00:00
|   libc6-dev-2.23-0ubuntu11; 0-01-01T00:00:00
|   libcap2-1:2.24-12; 0-01-01T00:00:00
|   libcap2-bin-1:2.24-12; 0-01-01T00:00:00
|   libcc1-0-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   libcilkrts5-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   libcomerr2-1.42.13-1ubuntu1; 0-01-01T00:00:00
|   libcryptsetup4-2:1.6.6-5ubuntu2.1; 0-01-01T00:00:00
|   libdb5.3-5.3.28-11ubuntu0.1; 0-01-01T00:00:00
|   libdebconfclient0-0.198ubuntu1; 0-01-01T00:00:00
|   libdevmapper1.02.1-2:1.02.110-1ubuntu10; 0-01-01T00:00:00
|   libdns-export162-1:9.10.3.dfsg.P4-8ubuntu1.14; 0-01-01T00:00:00
|   libdpkg-perl-1.18.4ubuntu1.5; 0-01-01T00:00:00
|   libedit2-3.1-20150325-1ubuntu2; 0-01-01T00:00:00
|   libexpat1-2.1.0-7ubuntu0.16.04.4; 0-01-01T00:00:00
|   libfakeroot-1.20.2-1ubuntu1; 0-01-01T00:00:00
|   libfdisk1-2.27.1-6ubuntu3.6; 0-01-01T00:00:00
|   libffi6-3.2.1-4; 0-01-01T00:00:00
|   libfile-fcntllock-perl-0.22-3; 0-01-01T00:00:00
|   libgcc-5-dev-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   libgcc1-1:6.0.1-0ubuntu1; 0-01-01T00:00:00
|   libgcrypt20-1.6.5-2ubuntu0.5; 0-01-01T00:00:00
|   libgdbm3-1.8.3-13.1; 0-01-01T00:00:00
|   libgmp10-2:6.1.0+dfsg-2; 0-01-01T00:00:00
|   libgomp1-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   libgpg-error0-1.21-2ubuntu1; 0-01-01T00:00:00
|   libgpm2-1.20.4-6.1; 0-01-01T00:00:00
|   libgssapi-krb5-2-1.13.2+dfsg-5ubuntu2.1; 0-01-01T00:00:00
|   libidn11-1.32-3ubuntu1.2; 0-01-01T00:00:00
|   libisc-export160-1:9.10.3.dfsg.P4-8ubuntu1.14; 0-01-01T00:00:00
|   libisl15-0.16.1-1; 0-01-01T00:00:00
|   libitm1-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   libk5crypto3-1.13.2+dfsg-5ubuntu2.1; 0-01-01T00:00:00
|   libkeyutils1-1.5.9-8ubuntu1; 0-01-01T00:00:00
|   libkmod2-22-1ubuntu5.2; 0-01-01T00:00:00
|   libkrb5-3-1.13.2+dfsg-5ubuntu2.1; 0-01-01T00:00:00
|   libkrb5support0-1.13.2+dfsg-5ubuntu2.1; 0-01-01T00:00:00
|   liblsan0-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   libltdl-dev-2.4.6-0.1; 0-01-01T00:00:00
|   libltdl7-2.4.6-0.1; 0-01-01T00:00:00
|   liblz4-1-0.0~r131-2ubuntu2; 0-01-01T00:00:00
|   liblzma5-5.1.1alpha+20120614-2ubuntu2; 0-01-01T00:00:00
|   libmagic1-1:5.25-2ubuntu1.2; 0-01-01T00:00:00
|   libmnl0-1.0.3-5; 0-01-01T00:00:00
|   libmount1-2.27.1-6ubuntu3.6; 0-01-01T00:00:00
|   libmpc3-1.0.3-1; 0-01-01T00:00:00
|   libmpdec2-2.4.2-1; 0-01-01T00:00:00
|   libmpfr4-3.1.4-1; 0-01-01T00:00:00
|   libmpx0-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   libncurses5-6.0+20160213-1ubuntu1; 0-01-01T00:00:00
|   libncursesw5-6.0+20160213-1ubuntu1; 0-01-01T00:00:00
|   libpam-modules-1.1.8-3.2ubuntu2.1; 0-01-01T00:00:00
|   libpam-modules-bin-1.1.8-3.2ubuntu2.1; 0-01-01T00:00:00
|   libpam-runtime-1.1.8-3.2ubuntu2.1; 0-01-01T00:00:00
|   libpam0g-1.1.8-3.2ubuntu2.1; 0-01-01T00:00:00
|   libpcre3-2:8.38-3.1; 0-01-01T00:00:00
|   libperl-dev-5.22.1-9ubuntu0.6; 0-01-01T00:00:00
|   libperl5.22-5.22.1-9ubuntu0.6; 0-01-01T00:00:00
|   libprocps4-2:3.3.10-4ubuntu2.4; 0-01-01T00:00:00
|   libpython-stdlib-2.7.12-1~16.04; 0-01-01T00:00:00
|   libpython2.7-minimal-2.7.12-1ubuntu0~16.04.4; 0-01-01T00:00:00
|   libpython2.7-stdlib-2.7.12-1ubuntu0~16.04.4; 0-01-01T00:00:00
|   libpython3-stdlib-3.5.1-3; 0-01-01T00:00:00
|   libpython3.5-3.5.2-2ubuntu0~16.04.5; 0-01-01T00:00:00
|   libpython3.5-minimal-3.5.2-2ubuntu0~16.04.5; 0-01-01T00:00:00
|   libpython3.5-stdlib-3.5.2-2ubuntu0~16.04.5; 0-01-01T00:00:00
|   libquadmath0-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   libreadline6-6.3-8ubuntu2; 0-01-01T00:00:00
|   libseccomp2-2.3.1-2.1ubuntu2~16.04.1; 0-01-01T00:00:00
|   libselinux1-2.4-3build2; 0-01-01T00:00:00
|   libsemanage-common-2.3-1build3; 0-01-01T00:00:00
|   libsemanage1-2.3-1build3; 0-01-01T00:00:00
|   libsepol1-2.4-2; 0-01-01T00:00:00
|   libsmartcols1-2.27.1-6ubuntu3.6; 0-01-01T00:00:00
|   libsqlite3-0-3.11.0-1ubuntu1.2; 0-01-01T00:00:00
|   libss2-1.42.13-1ubuntu1; 0-01-01T00:00:00
|   libssl1.0.0-1.0.2g-1ubuntu4.15; 0-01-01T00:00:00
|   libstdc++-5-dev-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   libstdc++6-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   libsystemd0-229-4ubuntu21.16; 0-01-01T00:00:00
|   libtinfo5-6.0+20160213-1ubuntu1; 0-01-01T00:00:00
|   libtool-2.4.6-0.1; 0-01-01T00:00:00
|   libtsan0-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   libubsan0-5.4.0-6ubuntu1~16.04.11; 0-01-01T00:00:00
|   libudev1-229-4ubuntu21.16; 0-01-01T00:00:00
|   libusb-0.1-4-2:0.1.12-28; 0-01-01T00:00:00
|   libustr-1.0-1-1.0.4-5; 0-01-01T00:00:00
|   libuuid1-2.27.1-6ubuntu3.6; 0-01-01T00:00:00
|   libwrap0-7.6.q-25; 0-01-01T00:00:00
|   libx11-6-2:1.6.3-1ubuntu2.1; 0-01-01T00:00:00
|   libx11-data-2:1.6.3-1ubuntu2.1; 0-01-01T00:00:00
|   libxau6-1:1.0.8-1; 0-01-01T00:00:00
|   libxcb1-1.11.1-1ubuntu1; 0-01-01T00:00:00
|   libxdmcp6-1:1.1.2-1.1; 0-01-01T00:00:00
|   libxext6-2:1.3.3-1; 0-01-01T00:00:00
|   libxmuu1-2:1.1.2-2; 0-01-01T00:00:00
|   libxtables11-1.6.0-2ubuntu3; 0-01-01T00:00:00
|   linux-libc-dev-4.4.0-154.181; 0-01-01T00:00:00
|   login-1:4.2-3.1ubuntu5.3; 0-01-01T00:00:00
|   lsb-base-9.20160110ubuntu0.2; 0-01-01T00:00:00
|   make-4.1-6; 0-01-01T00:00:00
|   makedev-2.3.1-93ubuntu2~ubuntu16.04.1; 0-01-01T00:00:00
|   manpages-4.04-2; 0-01-01T00:00:00
|   manpages-dev-4.04-2; 0-01-01T00:00:00
|   mawk-1.3.3-17ubuntu2; 0-01-01T00:00:00
|   mime-support-3.59ubuntu1; 0-01-01T00:00:00
|   mount-2.27.1-6ubuntu3.6; 0-01-01T00:00:00
|   multiarch-support-2.23-0ubuntu11; 0-01-01T00:00:00
|   ncurses-base-6.0+20160213-1ubuntu1; 0-01-01T00:00:00
|   ncurses-bin-6.0+20160213-1ubuntu1; 0-01-01T00:00:00
|   ncurses-term-6.0+20160213-1ubuntu1; 0-01-01T00:00:00
|   net-tools-1.60-26ubuntu1; 0-01-01T00:00:00
|   netbase-5.3; 0-01-01T00:00:00
|   netcat-1.10-41; 0-01-01T00:00:00
|   netcat-traditional-1.10-41; 0-01-01T00:00:00
|   openssh-client-1:7.2p2-4ubuntu2.8; 0-01-01T00:00:00
|   openssh-server-1:7.2p2-4ubuntu2.8; 0-01-01T00:00:00
|   openssh-sftp-server-1:7.2p2-4ubuntu2.8; 0-01-01T00:00:00
|   openssl-1.0.2g-1ubuntu4.15; 0-01-01T00:00:00
|   passwd-1:4.2-3.1ubuntu5.3; 0-01-01T00:00:00
|   patch-2.7.5-1ubuntu0.16.04.1; 0-01-01T00:00:00
|   perl-5.22.1-9ubuntu0.6; 0-01-01T00:00:00
|   perl-base-5.22.1-9ubuntu0.6; 0-01-01T00:00:00
|   perl-modules-5.22-5.22.1-9ubuntu0.6; 0-01-01T00:00:00
|   procps-2:3.3.10-4ubuntu2.4; 0-01-01T00:00:00
|   python-2.7.12-1~16.04; 0-01-01T00:00:00
|   python-meld3-1.0.2-2; 0-01-01T00:00:00
|   python-minimal-2.7.12-1~16.04; 0-01-01T00:00:00
|   python-pkg-resources-20.7.0-1; 0-01-01T00:00:00
|   python2.7-2.7.12-1ubuntu0~16.04.4; 0-01-01T00:00:00
|   python2.7-minimal-2.7.12-1ubuntu0~16.04.4; 0-01-01T00:00:00
|   python3-3.5.1-3; 0-01-01T00:00:00
|   python3-chardet-2.3.0-2; 0-01-01T00:00:00
|   python3-minimal-3.5.1-3; 0-01-01T00:00:00
|   python3-pkg-resources-20.7.0-1; 0-01-01T00:00:00
|   python3-requests-2.9.1-3ubuntu0.1; 0-01-01T00:00:00
|   python3-six-1.10.0-3; 0-01-01T00:00:00
|   python3-urllib3-1.13.1-2ubuntu0.16.04.3; 0-01-01T00:00:00
|   python3.5-3.5.2-2ubuntu0~16.04.5; 0-01-01T00:00:00
|   python3.5-minimal-3.5.2-2ubuntu0~16.04.5; 0-01-01T00:00:00
|   readline-common-6.3-8ubuntu2; 0-01-01T00:00:00
|   rename-0.20-4; 0-01-01T00:00:00
|   sed-4.2.2-7; 0-01-01T00:00:00
|   sensible-utils-0.0.9ubuntu0.16.04.1; 0-01-01T00:00:00
|   ssh-import-id-5.5-0ubuntu1; 0-01-01T00:00:00
|   supervisor-3.2.0-2ubuntu0.2; 0-01-01T00:00:00
|   systemd-229-4ubuntu21.16; 0-01-01T00:00:00
|   systemd-sysv-229-4ubuntu21.16; 0-01-01T00:00:00
|   sysv-rc-2.88dsf-59.3ubuntu2; 0-01-01T00:00:00
|   sysvinit-utils-2.88dsf-59.3ubuntu2; 0-01-01T00:00:00
|   tar-1.28-2.1ubuntu0.1; 0-01-01T00:00:00
|   tcpd-7.6.q-25; 0-01-01T00:00:00
|   ubuntu-keyring-2012.05.19; 0-01-01T00:00:00
|   util-linux-2.27.1-6ubuntu3.6; 0-01-01T00:00:00
|   vim-2:7.4.1689-3ubuntu1.3; 0-01-01T00:00:00
|   vim-common-2:7.4.1689-3ubuntu1.3; 0-01-01T00:00:00
|   vim-runtime-2:7.4.1689-3ubuntu1.3; 0-01-01T00:00:00
|   wget-1.17.1-1ubuntu1.5; 0-01-01T00:00:00
|   xauth-1:1.0.9-1ubuntu2; 0-01-01T00:00:00
|   xz-utils-5.1.1alpha+20120614-2ubuntu2; 0-01-01T00:00:00
|_  zlib1g-1:1.2.8.dfsg-2ubuntu4.1; 0-01-01T00:00:00
MAC Address: 02:42:C0:D8:15:03 (Unknown)
Too many fingerprints match this host to give specific OS details
Network Distance: 1 hop
Service Info: Host: victim-1

TRACEROUTE
HOP RTT     ADDRESS
1   0.04 ms target-1 (192.216.21.3)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 2.57 seconds
```

