---
layout: post
title: "FTP Anonymous Login"
date: "2022-08-22"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Assessment Methodologies
    - Enumeration
---

# FTP Anonymous Login（FTP匿名登录）
```
ip a

eth1 192.13.191.2/24
```

```
ping 192.13.191.3
```

```
nmap 192.13.191.3

PORT    STATE   SERVICE
21/tcp  open    ftp
```

```
nmap 192.13.191.3 -p 21 -sV

PORT    STATE   SERVICE VERSION
21/tcp  open    ftp     vsftpd 3.0.3
```

[ftp-anon](https://nmap.org/nsedoc/scripts/ftp-anon.html)

检查 FTP 服务器是否允许匿名登录。

如果允许匿名，则获取根目录的目录列表并突出显示可写文件。

```
nmap 192.13.191.3 -p 21 --script ftp-anon
```

```
ftp 192.13.191.3
Name (192.13.191.3:root): anonymous
ftp> ls
ftp> get flag
ftp> bye
```

```
cat flag
```

# VSFTPD Recon: Basics（VSFTPD 侦察：基础知识）
## 概述
在这个挑战中，我们将了解 VSFTPD 服务器侦察的基础知识。请开始实验并回答以下问题：

问题

1. 查找 vsftpd 服务器的版本。
2. 使用 nmap 脚本检查 ftp 服务器是否允许匿名登录。
3. 从 FTP 服务器获取标志。

指示： 

* 这个实验室是献给你的！此网络上没有其他用户 :)
* 开始实验室后，您将可以访问 Kali 实例的根终端
* 你的 Kali 有一个 IP 地址为 192.XYZ 的接口运行“ip addr”来知道 X 和 Y 的值。
* 目标机器应位于 IP 地址 192.XY3。
* 不要攻击位于 IP 地址 192.XY1 的网关

## 我自己的思路
In this challenge we will look at the basics of VSFTPD server reconnaissance.  Please start the lab and answer the following questions:

Questions

```
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
27817: eth0@if27818: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:08 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.8/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
27820: eth1@if27821: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:87:49:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.135.73.2/24 brd 192.135.73.255 scope global eth1
       valid_lft forever preferred_lft forever
```

```
root@attackdefense:~# nmap 192.135.73.3
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-22 02:01 UTC
Nmap scan report for target-1 (192.135.73.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
21/tcp open  ftp
MAC Address: 02:42:C0:87:49:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.19 seconds
```

Find the version of vsftpd server.

`vsftpd 3.0.3`

```
root@attackdefense:~# nmap 192.135.73.3 -p 21 -sV
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-22 02:02 UTC
Nmap scan report for target-1 (192.135.73.3)
Host is up (0.000050s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
MAC Address: 02:42:C0:87:49:03 (Unknown)
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.49 seconds
```

Check whether anonymous login is allowed on the ftp server using nmap script.

`Anonymous FTP login allowed`

```
root@attackdefense:~# nmap 192.135.73.3 -p 21 --script ftp-anon
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-22 02:04 UTC
Nmap scan report for target-1 (192.135.73.3)
Host is up (0.000046s latency).

PORT   STATE SERVICE
21/tcp open  ftp
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 ftp      ftp            33 Dec 18  2018 flag
|_drwxr-xr-x    2 ftp      ftp          4096 Dec 18  2018 pub
MAC Address: 02:42:C0:87:49:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.36 seconds
```

Fetch the flag from FTP server.

`4267bdfbff77d7c2635e4572519a8b9c`

```
root@attackdefense:~# ftp 192.135.73.3
Connected to 192.135.73.3.
220 (vsFTPd 3.0.3)
Name (192.135.73.3:root): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp            33 Dec 18  2018 flag
drwxr-xr-x    2 ftp      ftp          4096 Dec 18  2018 pub
226 Directory send OK.
ftp> get flag
local: flag remote: flag
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for flag (33 bytes).
226 Transfer complete.
33 bytes received in 0.00 secs (413.1611 kB/s)
ftp> cd pub
250 Directory successfully changed.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
226 Directory send OK.
ftp> bye
221 Goodbye.
root@attackdefense:~# cat flag 
4267bdfbff77d7c2635e4572519a8b9c
```

## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-519.pdf

参考：

[vsftpd](https://security.appspot.com/vsftpd.html)

[ftp](https://linux.die.net/man/1/ftp)

[ftp-anon](https://nmap.org/nsedoc/scripts/ftp-anon.html)