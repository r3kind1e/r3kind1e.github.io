---
layout: post
title: "HTTP IIS Nmap Scripts"
date: "2022-08-23"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Assessment Methodologies
    - Enumeration
---

# HTTP IIS Nmap Scripts
```
Target IP Address: 10.4.16.92
```

```
nmap 10.4.16.92

PORT        STATE   SERVICE         
80/tcp      open    http            
135/tcp     open    msrpc           
139/tcp     open    netbios-ssn     
445/tcp     open    microsoft-ds?
3306/tcp    open    mysql         
3389/tcp    open    ms-wbt-server
```

```
nmap 10.4.16.92 -sV

PORT        STATE   SERVICE         VERSION
80/tcp      open    http            Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
135/tcp     open    msrpc           Microsoft Windows RPC
139/tcp     open    netbios-ssn     Microsoft Windows netbios-ssn
445/tcp     open    microsoft-ds?
3306/tcp    open    mysql           MySQL (unauthorized)
3389/tcp    open    ms-wbt-server   Microsoft Terminal Services

Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

[http-enum](https://nmap.org/nsedoc/scripts/http-enum.html)

```
nmap 10.4.16.92 -sV -p 80 --script http-enum
```

[http-headers](https://nmap.org/nsedoc/scripts/http-headers.html)

```
nmap 10.4.16.92 -sV -p 80 --script http-headers
```

[http-methods](https://nmap.org/nsedoc/scripts/http-methods.html)

```
nmap 10.4.16.92 -sV -p 80 --script http-methods --script-args http-methods.url.path=/webdav/
```

[http-webdav-scan](https://nmap.org/nsedoc/scripts/http-webdav-scan.html)

```
nmap 10.4.16.92 -sV -p 80 --script http-webdav-scan --script-args http-methods.url-path=/webdav/
```

# Windows Recon: IIS: Nmap Scripts（Windows 侦察：IIS：Nmap 脚本）
## 概述
为您提供了一台 Kali GUI 机器和一台运行 IIS 服务的目标机器。目标机器的 IP 地址在 Kali 机器桌面 (/root/Desktop/target) 上名为 target 的文本文件中提供。 

你的任务是使用 Kali 机器上可用的工具对服务进行指纹识别，并运行 Nmap 脚本来枚举 Windows 目标机器的 IIS 服务。

目标： 

1. 识别 IIS 服务器
2. 获取网络服务器标头详细信息
3. 枚举 HTTP 方法
4. 检测 WebDAV 配置等。

指示：

* 你的 Kali 机器有一个 IP 地址为 10.10.XY 的接口运行“ip addr”以了解 X 和 Y 的值。
* 目标机器的 IP 地址在文件“/root/Desktop/target”中提到
* 不要攻击位于 IP 地址 192.VW1 和 10.10.X.1 的网关

## 我自己的思路

Your task is to fingerprint the service using the tools available on the Kali machine and run Nmap scripts to enumerate the Windows target machine IIS service.

```
Target IP Address : 10.0.31.165
```

```
root@attackdefense:~# nmap 10.0.31.165
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-23 19:06 IST
Nmap scan report for 10.0.31.165
Host is up (0.0038s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3306/tcp open  mysql
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 2.61 seconds
```

```
root@attackdefense:~# nmap 10.0.31.165 -sV
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-23 19:07 IST
Nmap scan report for 10.0.31.165
Host is up (0.0034s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3306/tcp open  mysql         MySQL (unauthorized)
3389/tcp open  ms-wbt-server Microsoft Terminal Services
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.11 seconds

```

Objective: 

Identify IIS Server

`Microsoft IIS httpd 10.0`

```
root@attackdefense:~# nmap 10.0.31.165 -p 80 --script http-enum
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-23 19:13 IST
Nmap scan report for 10.0.31.165
Host is up (0.0032s latency).

PORT   STATE SERVICE
80/tcp open  http
| http-enum: 
|   /content/: Potentially interesting folder
|   /downloads/: Potentially interesting folder
|_  /webdav/: Potentially interesting folder

Nmap done: 1 IP address (1 host up) scanned in 13.01 seconds
```

Get webserver header details

```
root@attackdefense:~# nmap 10.0.31.165 -p 80 --script http-headers
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-23 19:15 IST
Nmap scan report for 10.0.31.165
Host is up (0.0033s latency).

PORT   STATE SERVICE
80/tcp open  http
| http-headers: 
|   Cache-Control: private
|   Content-Type: text/html; charset=utf-8
|   Location: /Default.aspx
|   Server: Microsoft-IIS/10.0
|   Set-Cookie: ASP.NET_SessionId=ia1cdhbwgzajmxderfrcqneq; path=/; HttpOnly; SameSite=Lax
|   X-AspNet-Version: 4.0.30319
|   Set-Cookie: Server=RE9UTkVUR09BVA==; path=/
|   X-XSS-Protection: 0
|   X-Powered-By: ASP.NET
|   Date: Tue, 23 Aug 2022 13:45:30 GMT
|   Connection: close
|   Content-Length: 130
|   
|_  (Request type: GET)

Nmap done: 1 IP address (1 host up) scanned in 0.69 seconds
```

Enumerated HTTP methods

```
root@attackdefense:~# nmap 10.0.31.165 -p 80 --script http-methods --script-args http-methods.url.path=/webdav/
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-23 19:19 IST
Nmap scan report for 10.0.31.165
Host is up (0.0033s latency).

PORT   STATE SERVICE
80/tcp open  http
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST COPY PROPFIND LOCK UNLOCK PROPPATCH MKCOL PUT DELETE MOVE
|_  Potentially risky methods: TRACE COPY PROPFIND LOCK UNLOCK PROPPATCH MKCOL PUT DELETE MOVE

Nmap done: 1 IP address (1 host up) scanned in 0.50 seconds

```

Detect WebDAV configuration - etc.

```
root@attackdefense:~# nmap 10.0.31.165 -p 80 --script http-webdav-scan --script-args http-methods.url-path=/webdav/
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-23 19:24 IST
Nmap scan report for 10.0.31.165
Host is up (0.0033s latency).

PORT   STATE SERVICE
80/tcp open  http
| http-webdav-scan: 
|   Server Date: Tue, 23 Aug 2022 13:54:33 GMT
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, POST, COPY, PROPFIND, LOCK, UNLOCK
|   Public Options: OPTIONS, TRACE, GET, HEAD, POST, PROPFIND, PROPPATCH, MKCOL, PUT, DELETE, COPY, MOVE, LOCK, UNLOCK
|   WebDAV type: Unknown
|_  Server Type: Microsoft-IIS/10.0

Nmap done: 1 IP address (1 host up) scanned in 0.51 seconds
```

## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-2312.pdf