---
layout: post
title: "SMB Windows Discover & Mount"
date: "2022-08-14"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Assessment Methodologies
    - Enumeration
---

# Enumeration
本课程涵盖主机和网络服务枚举。在本课程中，您将了解 Nmap 脚本和 Metasploit 辅助模块，以及系统工具，以了解在网络资源上运行的服务和软件。这些服务和软件存在潜在的漏洞。为了管理这些漏洞，必须对服务进行全面调查和了解。这对所有安全专业人员来说都是至关重要的知识。

* Servers and Services（服务器和服务）
* SMB
* FTP
* SSH
* HTTP
* SQL

Learning Objectives:
* Students will know purpose of service enumeration.
* Students will identify common services and protocols.
* Students will perform service enumeration on common services and protocols.
* Students will understand process for enumerating unfamiliar protocols and services.

学习目标：
* 学生将了解服务枚举的目的。
* 学生将确定常见的服务和协议。
* 学生将对常用服务和协议进行服务枚举。
* 学生将了解列举不熟悉的协议和服务的过程。

# SMB
## SMB: Windows Discover & Mount（SMB：Windows 发现和装载）
我们的Windows机器：

```
ipconfig

IPv4 Address    10.4.20.56
Subnet Mask     255.255.240.0
Default Gateway 10.4.16.1
```

```
nmap -T4 10.4.20.0/20 --open
```

```
Nmap scan report for ip-10-4-17-133.ec2.internal (10.4.17.133)
Host is up
PORT        STATE   SERVICE
135/tcp     open    msrpc
139/tcp     open    netbios-ssn
445/tcp     open    microsoft-ds
3389/tcp    open    ms-wbt-server
```

```
nmap 10.4.17.133 -sV -O
```

```
PORT        STATE   SERVICE         VERSION
135/tcp     open    msrpc           Microsoft Windows RPC
139/tcp     open    netbios-ssn     Microsoft Windows netbios-ssn
445/tcp     open    microsoft-ds    Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp    open    ssl/ms-wbt-server?

Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
```

```
nmap 10.4.17.133 -sV -sC
```

```
PORT        STATE   SERVICE         VERSION
135/tcp     open    msrpc           Microsoft Windows RPC
139/tcp     open    netbios-ssn     Microsoft Windows netbios-ssn
445/tcp     open    microsoft-ds    Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp    open    ssl/ms-wbt-server?
| rdp-ntlm-info:
|   NetBIOS_Domain_Name: WIN-OMCNBKR66MN
|   NetBIOS_Computer_Name: WIN-OMCNBKR66MN

Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
```

Open File Explore, `Network->Map network drive...`. Drive select `Z:`, Folder write: `\\10.4.17.133`, then click `Browse`, go with `c` drive. Now Folder is `\\10.4.17.133\c`.

Network locations, `Z:` drive which is map to `c` dirve on 10.4.17.133.

```
This PC > c (\\10.4.17.133)(Z:) > Users > bob
```

This is different on our own machine. 

```
This PC > Local Disk (C:) > Users
```

```
net use * /delete
y
y
```

It's removed.

Reconnect it with command:

```
net use Z: \\10.4.17.133\C$ smbserver_771 /user:administrator
```

## Windows Recon: SMB Discover and Mount（Windows Recon：SMB 发现和装载）
### 概述
为您提供 Windows 机器（Server 2012）。

学习使用 Nmap 扫描目标机器并使用 Windows 文件资源管理器以及使用命令提示符挂载目标机器的 SMB 共享。

目标：发现 SMB 共享并挂载它

以下用户名和密码可用于访问服务：

`| 用户名 | 密码 | | administrator | smbserver_771 |`

### 我自己的思路

```
PS C:\Users\Administrator> ipconfig

Windows IP Configuration


Ethernet adapter Ethernet 2:

   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
   Link-local IPv6 Address . . . . . : fe80::5028:ab7a:5249:1bb5%12
   IPv4 Address. . . . . . . . . . . : 10.0.29.191
   Subnet Mask . . . . . . . . . . . : 255.255.240.0
   Default Gateway . . . . . . . . . : 10.0.16.1

Tunnel adapter isatap.ap-southeast-1.compute.internal:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
```

IP Address:	10.0.29.191

Network Address:	10.0.16.0

Usable Host IP Range:	10.0.16.1 - 10.0.31.254

Subnet Mask:	255.255.240.0

CIDR Notation:	/20


```
PS C:\Users\Administrator> nmap -sn 10.0.16.0/20
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-14 15:02 Coordinated Universal Time
Nmap scan report for ip-10-0-16-1.ap-southeast-1.compute.internal (10.0.16.1)
Host is up (0.00s latency).
MAC Address: 06:73:41:C8:CA:42 (Unknown)
Nmap scan report for ip-10-0-17-231.ap-southeast-1.compute.internal (10.0.17.231)
Host is up (0.00s latency).
MAC Address: 06:F5:51:A6:67:20 (Unknown)
Nmap scan report for ip-10-0-19-11.ap-southeast-1.compute.internal (10.0.19.11)
Host is up (0.00s latency).
MAC Address: 06:46:C9:0E:6E:1C (Unknown)
Nmap scan report for ip-10-0-19-45.ap-southeast-1.compute.internal (10.0.19.45)
Host is up (0.00s latency).
MAC Address: 06:A3:43:91:5D:E4 (Unknown)
Nmap scan report for ip-10-0-19-195.ap-southeast-1.compute.internal (10.0.19.195)
Host is up (0.00s latency).
MAC Address: 06:73:AA:C7:07:80 (Unknown)
Nmap scan report for ip-10-0-19-199.ap-southeast-1.compute.internal (10.0.19.199)
Host is up (0.00s latency).
MAC Address: 06:40:F2:3B:0B:82 (Unknown)
Nmap scan report for ip-10-0-20-42.ap-southeast-1.compute.internal (10.0.20.42)
Host is up (0.00s latency).
MAC Address: 06:1A:7B:9D:FF:34 (Unknown)
Nmap scan report for ip-10-0-20-175.ap-southeast-1.compute.internal (10.0.20.175)
Host is up (0.00s latency).
MAC Address: 06:4B:60:D1:D8:9C (Unknown)
Nmap scan report for ip-10-0-21-82.ap-southeast-1.compute.internal (10.0.21.82)
Host is up (0.00s latency).
MAC Address: 06:AC:65:4D:47:E4 (Unknown)
Nmap scan report for ip-10-0-22-246.ap-southeast-1.compute.internal (10.0.22.246)
Host is up (0.00s latency).
MAC Address: 06:3F:C9:51:12:76 (Unknown)
Nmap scan report for ip-10-0-24-47.ap-southeast-1.compute.internal (10.0.24.47)
Host is up (0.00s latency).
MAC Address: 06:6D:76:FA:CD:9E (Unknown)
Nmap scan report for ip-10-0-24-176.ap-southeast-1.compute.internal (10.0.24.176)
Host is up (0.00s latency).
MAC Address: 06:20:14:AB:88:8C (Unknown)
Nmap scan report for ip-10-0-24-230.ap-southeast-1.compute.internal (10.0.24.230)
Host is up (0.00s latency).
MAC Address: 06:DD:51:66:EA:2E (Unknown)
Nmap scan report for ip-10-0-26-239.ap-southeast-1.compute.internal (10.0.26.239)
Host is up (0.00s latency).
MAC Address: 06:A0:2D:DD:EA:5E (Unknown)
Nmap scan report for ip-10-0-27-251.ap-southeast-1.compute.internal (10.0.27.251)
Host is up (0.00s latency).
MAC Address: 06:DE:14:48:2C:F6 (Unknown)
Nmap scan report for ip-10-0-29-3.ap-southeast-1.compute.internal (10.0.29.3)
Host is up (0.00s latency).
MAC Address: 06:CC:61:73:BB:CA (Unknown)
Nmap scan report for ip-10-0-29-111.ap-southeast-1.compute.internal (10.0.29.111)
Host is up (0.00011s latency).
MAC Address: 06:7C:A2:BE:5E:50 (Unknown)
Nmap scan report for ip-10-0-31-103.ap-southeast-1.compute.internal (10.0.31.103)
Host is up (0.00s latency).
MAC Address: 06:4D:7A:33:38:C2 (Unknown)
Nmap scan report for ip-10-0-29-191.ap-southeast-1.compute.internal (10.0.29.191)
Host is up.
Nmap done: 4096 IP addresses (19 hosts up) scanned in 14.75 seconds
PS C:\Users\Administrator>
```

```
PS C:\Users\Administrator> nmap -T4 10.0.16.0/20 --open
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-14 15:13 Coordinated Universal Time
Nmap scan report for ip-10-0-24-47.ap-southeast-1.compute.internal (10.0.24.47)
Host is up (0.00s latency).
Not shown: 999 filtered ports
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT    STATE SERVICE
443/tcp open  https
MAC Address: 06:6D:76:FA:CD:9E (Unknown)

Nmap scan report for ip-10-0-24-176.ap-southeast-1.compute.internal (10.0.24.176)
Host is up (0.00s latency).
Not shown: 999 filtered ports
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT    STATE SERVICE
443/tcp open  https
MAC Address: 06:20:14:AB:88:8C (Unknown)

Nmap scan report for ip-10-0-26-239.ap-southeast-1.compute.internal (10.0.26.239)
Host is up (0.00s latency).
Not shown: 992 closed ports
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown
MAC Address: 06:A0:2D:DD:EA:5E (Unknown)

Nmap scan report for ip-10-0-31-103.ap-southeast-1.compute.internal (10.0.31.103)
Host is up (0.00s latency).
Not shown: 999 filtered ports
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT    STATE SERVICE
443/tcp open  https
MAC Address: 06:4D:7A:33:38:C2 (Unknown)

Nmap scan report for ip-10-0-29-191.ap-southeast-1.compute.internal (10.0.29.191)
Host is up (0.000051s latency).
Not shown: 990 closed ports
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
1025/tcp open  NFS-or-IIS
1026/tcp open  LSA-or-nterm
1027/tcp open  IIS
1028/tcp open  unknown
1037/tcp open  ams
1053/tcp open  remote-as
3389/tcp open  ms-wbt-server

Nmap done: 4096 IP addresses (20 hosts up) scanned in 41.50 seconds
```

可以确定，我们的目标是：10.0.26.239。

```
Nmap scan report for ip-10-0-26-239.ap-southeast-1.compute.internal (10.0.26.239)
Host is up (0.00s latency).
Not shown: 992 closed ports
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown
MAC Address: 06:A0:2D:DD:EA:5E (Unknown)
```

```
PS C:\Users\Administrator> nmap 10.0.26.239 -sV -O
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-14 15:24 Coordinated Universal Time
Nmap scan report for ip-10-0-26-239.ap-southeast-1.compute.internal (10.0.26.239)
Host is up (0.00s latency).
Not shown: 992 closed ports
PORT      STATE SERVICE            VERSION
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
MAC Address: 06:A0:2D:DD:EA:5E (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.91%E=4%D=8/14%OT=135%CT=1%CU=33001%PV=Y%DS=1%DC=D%G=Y%M=06A02D%
OS:TM=62F91403%P=i686-pc-windows-windows)SEQ(SP=100%GCD=1%ISR=107%TI=I%CI=I
OS:%II=I%SS=S%TS=7)OPS(O1=M2301NW8ST11%O2=M2301NW8ST11%O3=M2301NW8NNT11%O4=
OS:M2301NW8ST11%O5=M2301NW8ST11%O6=M2301ST11)WIN(W1=2000%W2=2000%W3=2000%W4
OS:=2000%W5=2000%W6=2000)ECN(R=Y%DF=Y%T=80%W=2000%O=M2301NW8NNS%CC=Y%Q=)T1(
OS:R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=
OS:%RD=0%Q=)T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T4(R=Y%DF=Y%T=80%W
OS:=0%S=A%A=O%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
OS:T6(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S
OS:+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUC
OS:K=G%RUD=G)IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 1 hop
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 82.87 seconds
```

```
PS C:\Users\Administrator> nmap 10.0.26.239 -sV -sC
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-14 15:29 Coordinated Universal Time
Nmap scan report for ip-10-0-26-239.ap-southeast-1.compute.internal (10.0.26.239)
Host is up (0.00s latency).
Not shown: 992 closed ports
PORT      STATE SERVICE            VERSION
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Windows Server 2012 R2 Standard 9600 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
| rdp-ntlm-info:
|   Target_Name: WIN-OMCNBKR66MN
|   NetBIOS_Domain_Name: WIN-OMCNBKR66MN
|   NetBIOS_Computer_Name: WIN-OMCNBKR66MN
|   DNS_Domain_Name: WIN-OMCNBKR66MN
|   DNS_Computer_Name: WIN-OMCNBKR66MN
|   Product_Version: 6.3.9600
|_  System_Time: 2022-08-14T15:31:03+00:00
| ssl-cert: Subject: commonName=WIN-OMCNBKR66MN
| Not valid before: 2022-08-13T14:54:50
|_Not valid after:  2023-02-12T14:54:50
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
MAC Address: 06:A0:2D:DD:EA:5E (Unknown)
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_nbstat: NetBIOS name: WIN-OMCNBKR66MN, NetBIOS user: <unknown>, NetBIOS MAC: 06:a0:2d:dd:ea:5e (unknown)
| smb-os-discovery:
|   OS: Windows Server 2012 R2 Standard 9600 (Windows Server 2012 R2 Standard 6.3)
|   OS CPE: cpe:/o:microsoft:windows_server_2012::-
|   Computer name: WIN-OMCNBKR66MN
|   NetBIOS computer name: WIN-OMCNBKR66MN\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2022-08-14T15:31:03+00:00
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode:
|   2.02:
|_    Message signing enabled but not required
| smb2-time:
|   date: 2022-08-14T15:31:03
|_  start_date: 2022-08-14T14:54:43

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 105.36 seconds
```

![map-network-drive.png](/img/in-post/ine/map-network-drive.png)

实验室被关闭了，重开之后，IP地址变了。

```
PS C:\Users\Administrator> ipconfig

Windows IP Configuration


Ethernet adapter Ethernet 2:

   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
   Link-local IPv6 Address . . . . . : fe80::6c8a:c175:6705:3818%12
   IPv4 Address. . . . . . . . . . . : 10.0.20.239
   Subnet Mask . . . . . . . . . . . : 255.255.240.0
   Default Gateway . . . . . . . . . : 10.0.16.1

Tunnel adapter isatap.ap-southeast-1.compute.internal:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
```

重新确定目标：10.0.19.126

```
PS C:\Users\Administrator> nmap -T4 10.0.16.0/20 --open
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-14 16:09 Coordinated Universal Time
Nmap scan report for ip-10-0-19-126.ap-southeast-1.compute.internal (10.0.19.126)
Host is up (0.00s latency).
Not shown: 991 closed ports
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown
49165/tcp open  unknown
MAC Address: 06:A2:F7:72:25:B6 (Unknown)
```

![map-network-drive-specify.png](/img/in-post/ine/map-network-drive-specify.png)

![z-map-c-users-bob.png](/img/in-post/ine/z-map-c-users-bob.png)

![this-pc-local-disk-c-users.png](/img/in-post/ine/this-pc-local-disk-c-users.png)

![network-locations.png](/img/in-post/ine/network-locations.png)

```
PS C:\Users\Administrator> net use * /delete
You have these remote connections:

    Z:              \\10.0.19.126\c
                    \\10.0.19.126\IPC$
Continuing will cancel the connections.

Do you want to continue this operation? (Y/N) [N]: y
The command completed successfully.
```

```
PS C:\Users\Administrator> net use Z: \\10.0.19.126\C$ smbserver_771 /user:administrator
The command completed successfully.
```

### 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-2220.pdf