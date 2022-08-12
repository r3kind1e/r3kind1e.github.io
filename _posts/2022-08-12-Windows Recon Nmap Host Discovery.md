---
layout: post
title: "Windows Recon Nmap Host Discovery"
date: "2022-08-12"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Assessment Methodologies
    - Information Gathering
    - Active Information Gathering
---

# Windows Recon: Nmap Host Discovery（Windows 侦察：Nmap 主机发现）
## 概述
为您提供了 Kali GUI 机器和目标机器。目标计算机正在运行 Windows 防火墙。 

您的任务是使用 Nmap 发现可用的活动主机及其开放端口，并识别正在运行的服务和应用程序。

指示：

* 你的 Kali 机器有一个 IP 地址为 10.10.X.Y 的接口运行“ip addr”以了解 X 和 Y 的值。
* 目标机器的 IP 地址在文件“/root/Desktop/target”中提到
* 不要攻击位于 IP 地址 192.V.W.1 和 10.10.X.1 的网关

## 我自己的思路
```
Target IP Address : 10.0.17.79
```

```
root@attackdefense:~# ip a s
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
22166: eth0@if22167: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:06 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.6/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
22168: eth1@if22169: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:0a:10:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.10.16.2/24 brd 10.10.16.255 scope global eth1
       valid_lft forever preferred_lft forever
```

Kali机器的eth0接口的IP地址为10.1.0.6/16，eth1接口的IP地址为10.10.16.2/24。

根据计算，eth0所在网络的可用的主机IP范围是：10.1.0.1 - 10.1.255.254。eth1所在网络的可用的主机IP范围是：10.10.16.1 - 10.10.16.254。目标似乎与eth0或eth1不在同一个网络中。

```
root@attackdefense:~# nmap 10.0.20.7
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-12 19:29 IST
Note: Host seems down. If it is really up, but blocking our ping probes, try -Pn
Nmap done: 1 IP address (0 hosts up) scanned in 3.13 seconds
```


Nmap 也无法检测到主机，它是否启动。许多安全工具在开始扫描或利用目标之前先 ping 主机。在这种情况下，必须使用高级 Nmap 选项，即 -A 或 -T5 等，以获得正确的输出。
在 nmap 中有一个选项，即 -Pn（将所有主机视为在线 - 跳过主机发现）此选项将强制扫描，即使它在主机发现中检测到目标已关闭。

```
root@attackdefense:~# nmap -Pn 10.0.20.7
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-12 19:31 IST
Nmap scan report for 10.0.20.7
Host is up (0.0033s latency).
Not shown: 993 filtered ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49154/tcp open  unknown
49155/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 4.90 seconds
```

`-p-`：扫描所有TCP端口。

```
root@attackdefense:~# nmap -Pn -p- 10.0.20.7
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-12 19:33 IST
Nmap scan report for 10.0.20.7
Host is up (0.0033s latency).
Not shown: 65527 filtered ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49154/tcp open  unknown
49155/tcp open  unknown
49172/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 1707.01 seconds
```

`-F`：快速扫描，扫描常用的100个端口。

```
root@attackdefense:~# nmap -Pn -F 10.0.20.7
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-12 20:04 IST
Nmap scan report for 10.0.20.7
Host is up (0.0032s latency).
Not shown: 93 filtered ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49154/tcp open  unknown
49155/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 3.57 seconds
```

`-sU` 执行UDP端口扫描，默认情况下，Nmap执行TCP端口扫描。

```
root@attackdefense:~# nmap -Pn -sU 10.0.20.7
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-12 20:10 IST
Nmap scan report for 10.0.20.7
Host is up.
All 1000 scanned ports on 10.0.20.7 are open|filtered

Nmap done: 1 IP address (1 host up) scanned in 201.35 seconds
```

`-sV` 识别在开放端口上运行的特定服务和服务版本。执行服务版本探测扫描。

```
root@attackdefense:~# nmap -Pn -F -sV 10.0.20.7
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-12 20:17 IST
Nmap scan report for 10.0.20.7
Host is up (0.0041s latency).
Not shown: 93 filtered ports
PORT      STATE SERVICE            VERSION
80/tcp    open  http               HttpFileServer httpd 2.3
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 77.31 seconds
```

`-O` 执行操作系统探测扫描。这将会尝试识别在目标系统上正在运行什么操作系统。

```
nmap -Pn -F -sV -O 10.0.17.79
```

```
root@attackdefense:~# nmap -Pn -F -sV -O 10.0.17.79
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-12 20:29 IST
Nmap scan report for 10.0.17.79
Host is up (0.0034s latency).
Not shown: 93 filtered ports
PORT      STATE SERVICE            VERSION
80/tcp    open  http               HttpFileServer httpd 2.3
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2012 (98%)
OS CPE: cpe:/o:microsoft:windows_server_2012
Aggressive OS guesses: Microsoft Windows Server 2012 (98%), Microsoft Windows Server 2012 or Windows Server 2012 R2 (89%), Microsoft Windows Server 2012 R2 (89%)
No exact OS matches for host (test conditions non-ideal).
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 81.63 seconds
```

`-sC` 执行默认的Nmap脚本扫描。这将会在当前开放的端口上运行一个列表的nmap脚本，以从这些开放端口中识别或枚举更多的信息。

```
nmap -Pn -F -sV -O -sC 10.0.17.79
```

```
root@attackdefense:~# nmap -Pn -F -sV -O -sC 10.0.17.79
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-12 20:39 IST
Nmap scan report for 10.0.17.79
Host is up (0.0032s latency).
Not shown: 93 filtered ports
PORT      STATE SERVICE            VERSION
80/tcp    open  http               HttpFileServer httpd 2.3
|_http-server-header: HFS 2.3
|_http-title: HFS /
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
| ssl-cert: Subject: commonName=http-server
| Not valid before: 2022-08-11T14:56:26
|_Not valid after:  2023-02-10T14:56:26
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Microsoft Windows 2012
OS CPE: cpe:/o:microsoft:windows_server_2012
OS details: Microsoft Windows Server 2012
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2022-08-12 20:41:09
|_  start_date: 2022-08-12 20:26:22

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 119.46 seconds
```

激烈的扫描。它将`-sV`服务版本探测，`-O`操作系统探测和`-sC`默认Nmap脚本扫描结合为一个，它可以用`-A`指定。

```
nmap -Pn -F -A 10.0.17.79
```

```
root@attackdefense:~# nmap -Pn -F -A 10.0.17.79
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-12 20:49 IST
Nmap scan report for 10.0.17.79
Host is up (0.0033s latency).
Not shown: 93 filtered ports
PORT      STATE SERVICE            VERSION
80/tcp    open  http               HttpFileServer httpd 2.3
|_http-server-header: HFS 2.3
|_http-title: HFS /
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
| ssl-cert: Subject: commonName=http-server
| Not valid before: 2022-08-11T14:56:26
|_Not valid after:  2023-02-10T14:56:26
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2012 (98%)
OS CPE: cpe:/o:microsoft:windows_server_2012
Aggressive OS guesses: Microsoft Windows Server 2012 (98%), Microsoft Windows Server 2012 or Windows Server 2012 R2 (89%), Microsoft Windows Server 2012 R2 (89%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 3 hops
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2022-08-12 20:50:54
|_  start_date: 2022-08-12 20:26:22

TRACEROUTE (using port 135/tcp)
HOP RTT     ADDRESS
1   0.02 ms linux (10.10.16.1)
2   ...
3   3.77 ms 10.0.17.79

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 124.84 seconds
```

默认情况下，Nmap会基于网络基础架构自动探测使用什么时间模板。
不建议在生产环境中使用`-T5`，因为它将会增加发送的数据包，从而可能潜在地导致在像路由器或交换机这样的网络设备上拒绝服务。

```
root@attackdefense:~# nmap -Pn -F -T5 -sV -O -sC 10.0.17.79
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-12 21:08 IST
Nmap scan report for 10.0.17.79
Host is up (0.0033s latency).
Not shown: 93 filtered ports
PORT      STATE SERVICE            VERSION
80/tcp    open  http               HttpFileServer httpd 2.3
|_http-server-header: HFS 2.3
|_http-title: HFS /
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
| ssl-cert: Subject: commonName=http-server
| Not valid before: 2022-08-11T14:56:26
|_Not valid after:  2023-02-10T14:56:26
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2012 (98%)
OS CPE: cpe:/o:microsoft:windows_server_2012
Aggressive OS guesses: Microsoft Windows Server 2012 (98%), Microsoft Windows Server 2012 or Windows Server 2012 R2 (89%), Microsoft Windows Server 2012 R2 (89%)
No exact OS matches for host (test conditions non-ideal).
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2022-08-12 21:09:25
|_  start_date: 2022-08-12 20:26:22

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 121.34 seconds

```

`-oN <filespec>` (标准输出) 要求将标准输出直接写入指定的文件。如上所述，这个格式与交互式输出略有不同。

```
root@attackdefense:~# nmap -Pn -F 10.0.17.79 -oN test.txt
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-12 21:16 IST
Nmap scan report for 10.0.17.79
Host is up (0.0035s latency).
Not shown: 93 filtered ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49154/tcp open  unknown
49155/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 1.94 seconds
```

```
root@attackdefense:~# cat test.txt
# Nmap 7.70 scan initiated Fri Aug 12 21:16:58 2022 as: nmap -Pn -F -oN test.txt 10.0.17.79
Nmap scan report for 10.0.17.79
Host is up (0.0035s latency).
Not shown: 93 filtered ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49154/tcp open  unknown
49155/tcp open  unknown

# Nmap done at Fri Aug 12 21:17:00 2022 -- 1 IP address (1 host up) scanned in 1.94 seconds
```

`-oX` XML格式。这种输出可以被导入到像Metasploit这样的框架。

```
nmap -Pn -F 10.0.17.79 -oX test.txt
```

```
root@attackdefense:~# cat test.txt
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE nmaprun>
<?xml-stylesheet href="file:///usr/bin/../share/nmap/nmap.xsl" type="text/xsl"?>
<!-- Nmap 7.70 scan initiated Fri Aug 12 21:21:34 2022 as: nmap -Pn -F -oX test.txt 10.0.17.79 -->
<nmaprun scanner="nmap" args="nmap -Pn -F -oX test.txt 10.0.17.79" start="1660319494" startstr="Fri Aug 12 21:21:34 2022" version="7.70" xmloutputversion="1.04">
<scaninfo type="syn" protocol="tcp" numservices="100" services="7,9,13,21-23,25-26,37,53,79-81,88,106,110-111,113,119,135,139,143-144,179,199,389,427,443-445,465,513-515,543-544,548,554,587,631,646,873,990,993,995,1025-1029,1110,1433,1720,1723,1755,1900,2000-2001,2049,2121,2717,3000,3128,3306,3389,3986,4899,5000,5009,5051,5060,5101,5190,5357,5432,5631,5666,5800,5900,6000-6001,6646,7070,8000,8008-8009,8080-8081,8443,8888,9100,9999-10000,32768,49152-49157"/>
<verbose level="0"/>
<debugging level="0"/>
<host starttime="1660319494" endtime="1660319496"><status state="up" reason="user-set" reason_ttl="0"/>
<address addr="10.0.17.79" addrtype="ipv4"/>
<hostnames>
</hostnames>
<ports><extraports state="filtered" count="93">
<extrareasons reason="no-responses" count="93"/>
</extraports>
<port protocol="tcp" portid="80"><state state="open" reason="syn-ack" reason_ttl="125"/><service name="http" method="table" conf="3"/></port>
<port protocol="tcp" portid="135"><state state="open" reason="syn-ack" reason_ttl="125"/><service name="msrpc" method="table" conf="3"/></port>
<port protocol="tcp" portid="139"><state state="open" reason="syn-ack" reason_ttl="125"/><service name="netbios-ssn" method="table" conf="3"/></port>
<port protocol="tcp" portid="445"><state state="open" reason="syn-ack" reason_ttl="125"/><service name="microsoft-ds" method="table" conf="3"/></port>
<port protocol="tcp" portid="3389"><state state="open" reason="syn-ack" reason_ttl="125"/><service name="ms-wbt-server" method="table" conf="3"/></port>
<port protocol="tcp" portid="49154"><state state="open" reason="syn-ack" reason_ttl="125"/><service name="unknown" method="table" conf="3"/></port>
<port protocol="tcp" portid="49155"><state state="open" reason="syn-ack" reason_ttl="125"/><service name="unknown" method="table" conf="3"/></port>
</ports>
<times srtt="3709" rttvar="1259" to="100000"/>
</host>
<runstats><finished time="1660319496" timestr="Fri Aug 12 21:21:36 2022" elapsed="1.92" summary="Nmap done at Fri Aug 12 21:21:36 2022; 1 IP address (1 host up) scanned in 1.92 seconds" exit="success"/><hosts up="1" down="0" total="1"/>
</runstats>
</nmaprun>
```

## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-2219.pdf


