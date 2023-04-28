---
layout: post
title: "Port Scanning & Enumeration - Windows"
date: "2023-04-27"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# Windows Black Box Penetration Test（Windows 黑盒渗透测试）
# Demo: Port Scanning & Enumeration - Windows（演示：端口扫描和枚举 - Windows）
We're going to be taking a look at performing port scanning on the target system, identifying open ports as well as the respective services that are running on those ports. And we'll take a look at how to sort all of the data that we're getting from the port scans, and how to import them into the Metasploit Framework for ease of access. And also, we'll take a look at some basic enumeration. The objective here is to learn as much information as possible from the services that are running on the target system, as well as more information regarding the exact version of Windows Server 2008 that is running on the target system.

Given the fact that this lab environment is running in the clound, the quickest way of identifying the target IP address is to check your hosts file, because a URL that corresponds to the target IP address has already been added as an record there.

```
cat /etc/hosts

10.0.22.85 demo.ine.local
```

```
cd Desktop/
mkdir Win2k8
cd Win2k8/
```

```
ping 10.0.22.85
```

Perform a simple nmap scan on the default port range, which is 1,000 of the most commonly used ports.

`-sV`: Perform a service version detection scan.

```
nmap -sV 10.0.22.85
```

Set the timing template to `T4`. So that's a much faster scan than the default one.

`-PA`: Perform ACK scan.

`-sC`: Perform the default script scan.

`-sV`: service version detection.

Set the port range to 1 to 10,000, so 10,000 ports. I'm not scanning the entire TCP range yet.

Output the scan results, because you always want to make sure that you output all of your scan results. Output it into an XML format, so that we can import it into the Metasploit Framework.

```
nmap -T4 -PA -sC -sV -p 1-10000 10.0.22.85 -oX nmap_10k
```

We can begin trying to learn more about some of the services that can be accessed.

```
http://10.0.22.85
```

We can also perform some banner-grabbing to learn more about a specific service whose version has not been enumerated with nmap.

```
nc -nv 10.0.22.85 21
```

Try and access some of the other web applications.

```
https://10.0.22.85:4848
http://10.0.22.85:8080
http://10.0.22.85:9200
http://10.0.22.85:5985
http://10.0.22.85:8484
http://10.0.22.85:8585
```

The smb-security-mod, we can see that message_signing is disabled, so that's quite important.

```
ls
```

Always make sure to output your scan results into a file. In this case the output file is in XML format. And the reason I did that is so that we can import it into the Metasploit Framework.

Start up the PostgreSQL database service. Because in order to save or import results into the Metasploit Framework and have them saved or persisted throughout various sessions or uses, we need to start up the PostgreSQL database service.

```
service postgresql start
msfconsole
```

Create a workspace for our current assessment. So that we can switch between engagements and systems without messing up the data in the back end.

```
workspace -a Win2k8
workspace
```

Use the `db_import` command to import the scan results.

```
db_import /root/Desktop/Win2k8/nmap_10k
```

We have the host added there.

```
hosts
```

We can also access the services performed with the default nmap scan where we scanned 10,000 ports.

If you want the operating system to be highlighted here accordingly, a quick trick to do that is to use a module called `smb_version` (This is in the context of Windows).

```
search smb_version
use 0
show options
set RHOSTS 10.0.22.85
run
```

This will tell us the exact operating system version.

```
hosts
```

I personally recommend that you import all your scan results into the Metasploit Framework. Because it provides you with a very cool way of viewing all hosts that you've scanned and the services pertinent to that host.

This only will work well if you separate your engagements or your hosts based on the workspace.

Whenever you're performing a new pen test, just create another workspace so that the data is separated. 

```
workspace
```

We have been able to enumerate the services running on the target system with nmap, and we've imported the scan results into the Metasploit Framework. We've also identified the exact operating system version. 

```
hosts
services
exit
```

Perform a scan on the entire TCP port range.

```
nmap -T4 -PA -sC -sV -p 1-65535 10.0.22.85 -oX nmap_all
```

How to scan UDP ports.

`-sU` for UDP.

`-sV`: for service version detection.

This is something that you should also do during a standard engagement. Be as comprehensive as possible with your port scans.

```
nmap -sU -sV 10.0.22.85
```

# Port Scanning & Enumeration - Windows
## Overview
Goal

This lab provides you with a structured guide on how to perform port scanning and enumeration on a Windows target.

The objective is to identify the open ports on the system and identify any potentially interesting services that can be exploited to gain access to the system.

## Tasks
Pre-requisites

1. Basic familiarity with Nmap.
2. Basic familiarity with the Metasploit Framework.

Requirements

This task does not have any requirements.

# 端口扫描和枚举 - Windows
## 概述
目标

本实验为您提供有关如何在 Windows 目标上执行端口扫描和枚举的结构化指南。

目的是识别系统上的开放端口，并识别任何可被利用来获取系统访问权限的潜在有趣服务。

## 任务
先决条件

1. 基本熟悉 Nmap。
2. 基本熟悉 Metasploit 框架。

要求

此任务没有任何要求。

# 复现视频内容

```
root@attackdefense:~# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
10.1.0.7        attackdefense.com attackdefense
127.0.0.1 AttackDefense-Kali
10.10.16.3      attackdefense.com attackdefense
10.0.18.65    demo.ine.local
```

10.0.18.65    demo.ine.local

```
root@attackdefense:~# cd Desktop/
root@attackdefense:~/Desktop# mkdir Win2k8
root@attackdefense:~/Desktop# cd Win2k8/
root@attackdefense:~/Desktop/Win2k8# 
```

```
root@attackdefense:~/Desktop/Win2k8# ping -c 4 10.0.18.65
PING 10.0.18.65 (10.0.18.65) 56(84) bytes of data.
64 bytes from 10.0.18.65: icmp_seq=1 ttl=125 time=3.35 ms
64 bytes from 10.0.18.65: icmp_seq=2 ttl=125 time=5.78 ms
64 bytes from 10.0.18.65: icmp_seq=3 ttl=125 time=2.46 ms
64 bytes from 10.0.18.65: icmp_seq=4 ttl=125 time=2.42 ms

--- 10.0.18.65 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 2.416/3.503/5.784/1.368 ms
```

```
root@attackdefense:~/Desktop/Win2k8# nmap -sV 10.0.18.65
Starting Nmap 7.92 ( https://nmap.org ) at 2023-04-28 14:27 IST
Nmap scan report for demo.ine.local (10.0.18.65)
Host is up (0.0032s latency).
Not shown: 983 closed tcp ports (reset)
PORT      STATE SERVICE              VERSION
21/tcp    open  ftp                  Microsoft ftpd
22/tcp    open  ssh                  OpenSSH 7.1 (protocol 2.0)
80/tcp    open  http                 Microsoft IIS httpd 7.5
135/tcp   open  msrpc                Microsoft Windows RPC
139/tcp   open  netbios-ssn          Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds         Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3306/tcp  open  mysql                MySQL 5.5.20-log
3389/tcp  open  tcpwrapped
4848/tcp  open  ssl/http             Oracle Glassfish Application Server
7676/tcp  open  java-message-service Java Message Service 301
8080/tcp  open  http                 Sun GlassFish Open Source Edition  4.0
8181/tcp  open  ssl/intermapper?
9200/tcp  open  wap-wsp?
49152/tcp open  msrpc                Microsoft Windows RPC
49153/tcp open  msrpc                Microsoft Windows RPC
49154/tcp open  msrpc                Microsoft Windows RPC
49155/tcp open  msrpc                Microsoft Windows RPC
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port8181-TCP:V=7.92%T=SSL%I=7%D=4/28%Time=644B8AAF%P=x86_64-pc-linux-gn
SF:u%r(GetRequest,128C,"HTTP/1\.1\x20200\x20OK\r\nDate:\x20Fri,\x2028\x20A
SF:pr\x202023\x2008:58:23\x20GMT\r\nContent-Type:\x20text/html\r\nConnecti
SF:on:\x20close\r\nContent-Length:\x204626\r\n\r\n<!DOCTYPE\x20HTML\x20PUB
SF:LIC\x20\"-//W3C//DTD\x20HTML\x204\.01\x20Transitional//EN\">\n<html\x20
SF:lang=\"en\">\n<!--\nDO\x20NOT\x20ALTER\x20OR\x20REMOVE\x20COPYRIGHT\x20
SF:NOTICES\x20OR\x20THIS\x20HEADER\.\n\nCopyright\x20\(c\)\x202010,\x20201
SF:3\x20Oracle\x20and/or\x20its\x20affiliates\.\x20All\x20rights\x20reserv
SF:ed\.\n\nUse\x20is\x20subject\x20to\x20License\x20Terms\n-->\n<head>\n<s
SF:tyle\x20type=\"text/css\">\n\tbody{margin-top:0}\n\tbody,td,p,div,span,
SF:a,ul,ul\x20li,\x20ol,\x20ol\x20li,\x20ol\x20li\x20b,\x20dl,h1,h2,h3,h4,
SF:h5,h6,li\x20{font-family:geneva,helvetica,arial,\"lucida\x20sans\",sans
SF:-serif;\x20font-size:10pt}\n\th1\x20{font-size:18pt}\n\th2\x20{font-siz
SF:e:14pt}\n\th3\x20{font-size:12pt}\n\tcode,kbd,tt,pre\x20{font-family:mo
SF:naco,courier,\"courier\x20new\";\x20font-size:10pt;}\n\tli\x20{padding-
SF:bottom:\x208px}\n\tp\.copy,\x20p\.copy\x20a\x20{font-family:geneva,helv
SF:etica,arial,\"lucida\x20sans\",sans-serif;\x20font-size:8pt}\n\tp\.copy
SF:\x20{text-align:\x20center}\n\ttable\.grey1,tr\.grey1,td\.g")%r(HTTPOpt
SF:ions,7A,"HTTP/1\.1\x20405\x20Method\x20Not\x20Allowed\r\nAllow:\x20GET\
SF:r\nDate:\x20Fri,\x2028\x20Apr\x202023\x2008:58:23\x20GMT\r\nConnection:
SF:\x20close\r\nContent-Length:\x200\r\n\r\n")%r(RTSPRequest,76,"HTTP/1\.1
SF:\x20505\x20HTTP\x20Version\x20Not\x20Supported\r\nDate:\x20Fri,\x2028\x
SF:20Apr\x202023\x2008:58:23\x20GMT\r\nConnection:\x20close\r\nContent-Len
SF:gth:\x200\r\n\r\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port9200-TCP:V=7.92%I=7%D=4/28%Time=644B8AA2%P=x86_64-pc-linux-gnu%r(Ge
SF:tRequest,196,"HTTP/1\.0\x20200\x20OK\r\nContent-Type:\x20application/js
SF:on;\x20charset=UTF-8\r\nContent-Length:\x20319\r\n\r\n{\r\n\x20\x20\"st
SF:atus\"\x20:\x20200,\r\n\x20\x20\"name\"\x20:\x20\"Spider\x20Doppelgange
SF:r\",\r\n\x20\x20\"version\"\x20:\x20{\r\n\x20\x20\x20\x20\"number\"\x20
SF::\x20\"1\.1\.1\",\r\n\x20\x20\x20\x20\"build_hash\"\x20:\x20\"f1585f096
SF:d3f3985e73456debdc1a0745f512bbc\",\r\n\x20\x20\x20\x20\"build_timestamp
SF:\"\x20:\x20\"2014-04-16T14:27:12Z\",\r\n\x20\x20\x20\x20\"build_snapsho
SF:t\"\x20:\x20false,\r\n\x20\x20\x20\x20\"lucene_version\"\x20:\x20\"4\.7
SF:\"\r\n\x20\x20},\r\n\x20\x20\"tagline\"\x20:\x20\"You\x20Know,\x20for\x
SF:20Search\"\r\n}\n")%r(HTTPOptions,4F,"HTTP/1\.0\x20200\x20OK\r\nContent
SF:-Type:\x20text/plain;\x20charset=UTF-8\r\nContent-Length:\x200\r\n\r\n"
SF:)%r(RTSPRequest,4F,"HTTP/1\.1\x20200\x20OK\r\nContent-Type:\x20text/pla
SF:in;\x20charset=UTF-8\r\nContent-Length:\x200\r\n\r\n")%r(FourOhFourRequ
SF:est,A9,"HTTP/1\.0\x20400\x20Bad\x20Request\r\nContent-Type:\x20text/pla
SF:in;\x20charset=UTF-8\r\nContent-Length:\x2080\r\n\r\nNo\x20handler\x20f
SF:ound\x20for\x20uri\x20\[/nice%20ports%2C/Tri%6Eity\.txt%2ebak\]\x20and\
SF:x20method\x20\[GET\]")%r(SIPOptions,4F,"HTTP/1\.1\x20200\x20OK\r\nConte
SF:nt-Type:\x20text/plain;\x20charset=UTF-8\r\nContent-Length:\x200\r\n\r\
SF:n");
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 113.94 seconds
```

```
root@attackdefense:~/Desktop/Win2k8# nmap -T4 -PA -sC -sV -p 1-10000 10.0.18.65 -oX nmap_10k
Starting Nmap 7.92 ( https://nmap.org ) at 2023-04-28 14:33 IST
Nmap scan report for demo.ine.local (10.0.18.65)
Host is up (0.0028s latency).
Not shown: 9980 closed tcp ports (reset)
PORT     STATE SERVICE              VERSION
21/tcp   open  ftp                  Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
22/tcp   open  ssh                  OpenSSH 7.1 (protocol 2.0)
| ssh-hostkey: 
|   2048 83:56:2e:69:b5:2d:b4:9a:e4:7f:97:86:d8:bc:ae:7b (RSA)
|_  521 c0:e2:da:9d:e2:1e:58:09:7e:80:07:ec:3e:b4:57:f5 (ECDSA)
80/tcp   open  http                 Microsoft IIS httpd 7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: Site doesn't have a title (text/html).
135/tcp  open  msrpc                Microsoft Windows RPC
139/tcp  open  netbios-ssn          Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds         Windows Server 2008 R2 Standard 7601 Service Pack 1 microsoft-ds
1617/tcp open  java-rmi             Java RMI
| rmi-dumpregistry: 
|   jmxrmi
|     javax.management.remote.rmi.RMIServerImpl_Stub
|     @10.0.18.65:49216
|     extends
|       java.rmi.server.RemoteStub
|       extends
|_        java.rmi.server.RemoteObject
3306/tcp open  mysql                MySQL 5.5.20-log
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.20-log
|   Thread ID: 6
|   Capabilities flags: 63487
|   Some Capabilities: ODBCClient, ConnectWithDatabase, Support41Auth, Speaks41ProtocolOld, SupportsTransactions, IgnoreSigpipes, LongPassword, InteractiveClient, SupportsCompression, SupportsLoadDataLocal, Speaks41ProtocolNew, LongColumnFlag, IgnoreSpaceBeforeParenthesis, DontAllowDatabaseTableColumn, FoundRows, SupportsMultipleResults, SupportsAuthPlugins, SupportsMultipleStatments
|   Status: Autocommit
|   Salt: V:`Uh+<,gD[~(J}VvoKf
|_  Auth Plugin Name: mysql_native_password
3389/tcp open  ms-wbt-server?
| rdp-ntlm-info: 
|   Target_Name: VAGRANT-2008R2
|   NetBIOS_Domain_Name: VAGRANT-2008R2
|   NetBIOS_Computer_Name: VAGRANT-2008R2
|   DNS_Domain_Name: vagrant-2008R2
|   DNS_Computer_Name: vagrant-2008R2
|   Product_Version: 6.1.7601
|_  System_Time: 2023-04-28T09:05:32+00:00
| ssl-cert: Subject: commonName=vagrant-2008R2
| Not valid before: 2023-04-27T08:53:45
|_Not valid after:  2023-10-27T08:53:45
|_ssl-date: 2023-04-28T09:05:42+00:00; 0s from scanner time.
3700/tcp open  giop                 CORBA naming service
4848/tcp open  ssl/http             Oracle GlassFish 4.0 (Servlet 3.1; JSP 2.3; Java 1.8)
| ssl-cert: Subject: commonName=localhost/organizationName=Oracle Corporation/stateOrProvinceName=California/countryName=US
| Not valid before: 2013-05-15T05:33:38
|_Not valid after:  2023-05-13T05:33:38
|_http-server-header: GlassFish Server Open Source Edition  4.0 
|_http-title: Login
|_ssl-date: 2023-04-28T09:05:41+00:00; -1s from scanner time.
5985/tcp open  http                 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
7676/tcp open  java-message-service Java Message Service 301
8080/tcp open  http                 Oracle GlassFish 4.0 (Servlet 3.1; JSP 2.3; Java 1.8)
|_http-title: GlassFish Server - Server Running
|_http-server-header: GlassFish Server Open Source Edition  4.0 
| http-methods: 
|_  Potentially risky methods: PUT DELETE TRACE
8181/tcp open  ssl/http             Oracle GlassFish 4.0 (Servlet 3.1; JSP 2.3; Java 1.8)
| http-methods: 
|_  Potentially risky methods: PUT DELETE TRACE
|_http-title: Site doesn't have a title (text/html).
| ssl-cert: Subject: commonName=localhost/organizationName=Oracle Corporation/stateOrProvinceName=California/countryName=US
| Not valid before: 2013-05-15T05:33:38
|_Not valid after:  2023-05-13T05:33:38
|_ssl-date: 2023-04-28T09:05:41+00:00; -1s from scanner time.
|_http-server-header: GlassFish Server Open Source Edition  4.0 
8484/tcp open  http                 Jetty winstone-2.8
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Jetty(winstone-2.8)
|_http-title: Dashboard [Jenkins]
8585/tcp open  http                 Apache httpd 2.2.21 ((Win64) PHP/5.3.10 DAV/2)
|_http-server-header: Apache/2.2.21 (Win64) PHP/5.3.10 DAV/2
|_http-title: WAMPSERVER Homepage
8686/tcp open  java-rmi             Java RMI
| rmi-dumpregistry: 
|   10.0.18.65/7676/jmxrmi
|     javax.management.remote.rmi.RMIServerImpl_Stub
|     @10.0.18.65:49289
|     extends
|       java.rmi.server.RemoteStub
|       extends
|         java.rmi.server.RemoteObject
|   jmxrmi
|     javax.management.remote.rmi.RMIServerImpl_Stub
|     @10.0.18.65:8686
|     extends
|       java.rmi.server.RemoteStub
|       extends
|_        java.rmi.server.RemoteObject
9200/tcp open  wap-wsp?
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 400 Bad Request
|     Content-Type: text/plain; charset=UTF-8
|     Content-Length: 80
|     handler found for uri [/nice%20ports%2C/Tri%6Eity.txt%2ebak] and method [GET]
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Content-Type: application/json; charset=UTF-8
|     Content-Length: 319
|     "status" : 200,
|     "name" : "Spider Doppelganger",
|     "version" : {
|     "number" : "1.1.1",
|     "build_hash" : "f1585f096d3f3985e73456debdc1a0745f512bbc",
|     "build_timestamp" : "2014-04-16T14:27:12Z",
|     "build_snapshot" : false,
|     "lucene_version" : "4.7"
|     "tagline" : "You Know, for Search"
|   HTTPOptions: 
|     HTTP/1.0 200 OK
|     Content-Type: text/plain; charset=UTF-8
|     Content-Length: 0
|   RTSPRequest, SIPOptions: 
|     HTTP/1.1 200 OK
|     Content-Type: text/plain; charset=UTF-8
|_    Content-Length: 0
9300/tcp open  vrace?
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port9200-TCP:V=7.92%I=7%D=4/28%Time=644B8BFF%P=x86_64-pc-linux-gnu%r(Ge
SF:tRequest,196,"HTTP/1\.0\x20200\x20OK\r\nContent-Type:\x20application/js
SF:on;\x20charset=UTF-8\r\nContent-Length:\x20319\r\n\r\n{\r\n\x20\x20\"st
SF:atus\"\x20:\x20200,\r\n\x20\x20\"name\"\x20:\x20\"Spider\x20Doppelgange
SF:r\",\r\n\x20\x20\"version\"\x20:\x20{\r\n\x20\x20\x20\x20\"number\"\x20
SF::\x20\"1\.1\.1\",\r\n\x20\x20\x20\x20\"build_hash\"\x20:\x20\"f1585f096
SF:d3f3985e73456debdc1a0745f512bbc\",\r\n\x20\x20\x20\x20\"build_timestamp
SF:\"\x20:\x20\"2014-04-16T14:27:12Z\",\r\n\x20\x20\x20\x20\"build_snapsho
SF:t\"\x20:\x20false,\r\n\x20\x20\x20\x20\"lucene_version\"\x20:\x20\"4\.7
SF:\"\r\n\x20\x20},\r\n\x20\x20\"tagline\"\x20:\x20\"You\x20Know,\x20for\x
SF:20Search\"\r\n}\n")%r(HTTPOptions,4F,"HTTP/1\.0\x20200\x20OK\r\nContent
SF:-Type:\x20text/plain;\x20charset=UTF-8\r\nContent-Length:\x200\r\n\r\n"
SF:)%r(RTSPRequest,4F,"HTTP/1\.1\x20200\x20OK\r\nContent-Type:\x20text/pla
SF:in;\x20charset=UTF-8\r\nContent-Length:\x200\r\n\r\n")%r(FourOhFourRequ
SF:est,A9,"HTTP/1\.0\x20400\x20Bad\x20Request\r\nContent-Type:\x20text/pla
SF:in;\x20charset=UTF-8\r\nContent-Length:\x2080\r\n\r\nNo\x20handler\x20f
SF:ound\x20for\x20uri\x20\[/nice%20ports%2C/Tri%6Eity\.txt%2ebak\]\x20and\
SF:x20method\x20\[GET\]")%r(SIPOptions,4F,"HTTP/1\.1\x20200\x20OK\r\nConte
SF:nt-Type:\x20text/plain;\x20charset=UTF-8\r\nContent-Length:\x200\r\n\r\
SF:n");
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-04-28T09:05:34
|_  start_date: 2023-04-28T08:53:40
|_nbstat: NetBIOS name: VAGRANT-2008R2, NetBIOS user: <unknown>, NetBIOS MAC: 06:08:fa:4f:5b:dc (unknown)
| smb-os-discovery: 
|   OS: Windows Server 2008 R2 Standard 7601 Service Pack 1 (Windows Server 2008 R2 Standard 6.1)
|   OS CPE: cpe:/o:microsoft:windows_server_2008::sp1
|   Computer name: vagrant-2008R2
|   NetBIOS computer name: VAGRANT-2008R2\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2023-04-28T02:05:35-07:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 1h00m00s, deviation: 2h38m45s, median: 0s
| smb2-security-mode: 
|   2.1: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 119.17 seconds
```

```
root@attackdefense:~# nc -nv 10.0.18.65 21
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Connected to 10.0.18.65:21.
220 Microsoft FTP Service
Ncat: Connection reset by peer.
```

```
https://10.0.18.65:4848/
http://10.0.18.65:8080/
http://10.0.18.65:9200/
http://10.0.18.65:5985/
http://10.0.18.65:8484/
http://10.0.18.65:8585/
```

```
root@attackdefense:~/Desktop/Win2k8# nmap -T4 -PA -sC -sV -p 1-65535 10.0.18.65 -oX nmap_all
Starting Nmap 7.92 ( https://nmap.org ) at 2023-04-28 14:41 IST
Nmap scan report for demo.ine.local (10.0.18.65)
Host is up (0.0036s latency).
Not shown: 65498 closed tcp ports (reset)
PORT      STATE SERVICE              VERSION
21/tcp    open  ftp                  Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
22/tcp    open  ssh                  OpenSSH 7.1 (protocol 2.0)
| ssh-hostkey: 
|   2048 83:56:2e:69:b5:2d:b4:9a:e4:7f:97:86:d8:bc:ae:7b (RSA)
|_  521 c0:e2:da:9d:e2:1e:58:09:7e:80:07:ec:3e:b4:57:f5 (ECDSA)
80/tcp    open  http                 Microsoft IIS httpd 7.5
|_http-server-header: Microsoft-IIS/7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Site doesn't have a title (text/html).
135/tcp   open  msrpc                Microsoft Windows RPC
139/tcp   open  netbios-ssn          Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds         Windows Server 2008 R2 Standard 7601 Service Pack 1 microsoft-ds
1617/tcp  open  java-rmi             Java RMI
| rmi-dumpregistry: 
|   jmxrmi
|     javax.management.remote.rmi.RMIServerImpl_Stub
|     @10.0.18.65:49216
|     extends
|       java.rmi.server.RemoteStub
|       extends
|_        java.rmi.server.RemoteObject
3306/tcp  open  mysql                MySQL 5.5.20-log
3389/tcp  open  ssl/ms-wbt-server?
|_ssl-date: 2023-04-28T09:14:44+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=vagrant-2008R2
| Not valid before: 2023-04-27T08:53:45
|_Not valid after:  2023-10-27T08:53:45
| rdp-ntlm-info: 
|   Target_Name: VAGRANT-2008R2
|   NetBIOS_Domain_Name: VAGRANT-2008R2
|   NetBIOS_Computer_Name: VAGRANT-2008R2
|   DNS_Domain_Name: vagrant-2008R2
|   DNS_Computer_Name: vagrant-2008R2
|   Product_Version: 6.1.7601
|_  System_Time: 2023-04-28T09:14:11+00:00
3700/tcp  open  giop                 CORBA naming service
3820/tcp  open  ssl/giop             CORBA naming service
| ssl-cert: Subject: commonName=localhost/organizationName=Oracle Corporation/stateOrProvinceName=California/countryName=US
| Not valid before: 2013-05-15T05:33:38
|_Not valid after:  2023-05-13T05:33:38
|_ssl-date: 2023-04-28T09:14:44+00:00; 0s from scanner time.
3920/tcp  open  ssl/exasoftport1?
|_ssl-date: 2023-04-28T09:14:44+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=localhost/organizationName=Oracle Corporation/stateOrProvinceName=California/countryName=US
| Not valid before: 2013-05-15T05:33:38
|_Not valid after:  2023-05-13T05:33:38
4848/tcp  open  ssl/http             Oracle GlassFish 4.0 (Servlet 3.1; JSP 2.3; Java 1.8)
|_ssl-date: 2023-04-28T09:14:44+00:00; 0s from scanner time.
|_http-server-header: GlassFish Server Open Source Edition  4.0 
| ssl-cert: Subject: commonName=localhost/organizationName=Oracle Corporation/stateOrProvinceName=California/countryName=US
| Not valid before: 2013-05-15T05:33:38
|_Not valid after:  2023-05-13T05:33:38
|_http-title: Login
5985/tcp  open  http                 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Site doesn't have a title (text/html; charset=us-ascii).
7676/tcp  open  java-message-service Java Message Service 301
8080/tcp  open  http                 Oracle GlassFish 4.0 (Servlet 3.1; JSP 2.3; Java 1.8)
|_http-open-proxy: Proxy might be redirecting requests
|_http-title: Site doesn't have a title (text/html).
| http-methods: 
|_  Potentially risky methods: PUT DELETE TRACE
|_http-server-header: GlassFish Server Open Source Edition  4.0 
8181/tcp  open  ssl/http             Oracle GlassFish 4.0 (Servlet 3.1; JSP 2.3; Java 1.8)
| ssl-cert: Subject: commonName=localhost/organizationName=Oracle Corporation/stateOrProvinceName=California/countryName=US
| Not valid before: 2013-05-15T05:33:38
|_Not valid after:  2023-05-13T05:33:38
|_ssl-date: 2023-04-28T09:14:44+00:00; 0s from scanner time.
|_http-title: GlassFish Server - Server Running
| http-methods: 
|_  Potentially risky methods: PUT DELETE TRACE
8484/tcp  open  http                 Jetty winstone-2.8
|_http-title: Dashboard [Jenkins]
|_http-server-header: Jetty(winstone-2.8)
| http-robots.txt: 1 disallowed entry 
|_/
8585/tcp  open  http                 Apache httpd 2.2.21 ((Win64) PHP/5.3.10 DAV/2)
|_http-server-header: Apache/2.2.21 (Win64) PHP/5.3.10 DAV/2
|_http-title: WAMPSERVER Homepage
8686/tcp  open  java-rmi             Java RMI
| rmi-dumpregistry: 
|   10.0.18.65/7676/jmxrmi
|     javax.management.remote.rmi.RMIServerImpl_Stub
|     @10.0.18.65:49289
|     extends
|       java.rmi.server.RemoteStub
|       extends
|         java.rmi.server.RemoteObject
|   jmxrmi
|     javax.management.remote.rmi.RMIServerImpl_Stub
|     @10.0.18.65:8686
|     extends
|       java.rmi.server.RemoteStub
|       extends
|_        java.rmi.server.RemoteObject
9200/tcp  open  wap-wsp?
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 400 Bad Request
|     Content-Type: text/plain; charset=UTF-8
|     Content-Length: 80
|     handler found for uri [/nice%20ports%2C/Tri%6Eity.txt%2ebak] and method [GET]
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Content-Type: application/json; charset=UTF-8
|     Content-Length: 319
|     "status" : 200,
|     "name" : "Spider Doppelganger",
|     "version" : {
|     "number" : "1.1.1",
|     "build_hash" : "f1585f096d3f3985e73456debdc1a0745f512bbc",
|     "build_timestamp" : "2014-04-16T14:27:12Z",
|     "build_snapshot" : false,
|     "lucene_version" : "4.7"
|     "tagline" : "You Know, for Search"
|   HTTPOptions: 
|     HTTP/1.0 200 OK
|     Content-Type: text/plain; charset=UTF-8
|     Content-Length: 0
|   RTSPRequest, SIPOptions: 
|     HTTP/1.1 200 OK
|     Content-Type: text/plain; charset=UTF-8
|_    Content-Length: 0
9300/tcp  open  vrace?
47001/tcp open  http                 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49152/tcp open  msrpc                Microsoft Windows RPC
49153/tcp open  msrpc                Microsoft Windows RPC
49154/tcp open  msrpc                Microsoft Windows RPC
49155/tcp open  msrpc                Microsoft Windows RPC
49211/tcp open  msrpc                Microsoft Windows RPC
49214/tcp open  msrpc                Microsoft Windows RPC
49216/tcp open  java-rmi             Java RMI
49220/tcp open  tcpwrapped
49261/tcp open  ssh                  Apache Mina sshd 0.8.0 (protocol 2.0)
49263/tcp open  jenkins-listener     Jenkins TcpSlaveAgentListener
49289/tcp open  java-rmi             Java RMI
49292/tcp open  unknown
49293/tcp open  unknown
49294/tcp open  unknown
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port9200-TCP:V=7.92%I=7%D=4/28%Time=644B8DCB%P=x86_64-pc-linux-gnu%r(Ge
SF:tRequest,196,"HTTP/1\.0\x20200\x20OK\r\nContent-Type:\x20application/js
SF:on;\x20charset=UTF-8\r\nContent-Length:\x20319\r\n\r\n{\r\n\x20\x20\"st
SF:atus\"\x20:\x20200,\r\n\x20\x20\"name\"\x20:\x20\"Spider\x20Doppelgange
SF:r\",\r\n\x20\x20\"version\"\x20:\x20{\r\n\x20\x20\x20\x20\"number\"\x20
SF::\x20\"1\.1\.1\",\r\n\x20\x20\x20\x20\"build_hash\"\x20:\x20\"f1585f096
SF:d3f3985e73456debdc1a0745f512bbc\",\r\n\x20\x20\x20\x20\"build_timestamp
SF:\"\x20:\x20\"2014-04-16T14:27:12Z\",\r\n\x20\x20\x20\x20\"build_snapsho
SF:t\"\x20:\x20false,\r\n\x20\x20\x20\x20\"lucene_version\"\x20:\x20\"4\.7
SF:\"\r\n\x20\x20},\r\n\x20\x20\"tagline\"\x20:\x20\"You\x20Know,\x20for\x
SF:20Search\"\r\n}\n")%r(HTTPOptions,4F,"HTTP/1\.0\x20200\x20OK\r\nContent
SF:-Type:\x20text/plain;\x20charset=UTF-8\r\nContent-Length:\x200\r\n\r\n"
SF:)%r(RTSPRequest,4F,"HTTP/1\.1\x20200\x20OK\r\nContent-Type:\x20text/pla
SF:in;\x20charset=UTF-8\r\nContent-Length:\x200\r\n\r\n")%r(FourOhFourRequ
SF:est,A9,"HTTP/1\.0\x20400\x20Bad\x20Request\r\nContent-Type:\x20text/pla
SF:in;\x20charset=UTF-8\r\nContent-Length:\x2080\r\n\r\nNo\x20handler\x20f
SF:ound\x20for\x20uri\x20\[/nice%20ports%2C/Tri%6Eity\.txt%2ebak\]\x20and\
SF:x20method\x20\[GET\]")%r(SIPOptions,4F,"HTTP/1\.1\x20200\x20OK\r\nConte
SF:nt-Type:\x20text/plain;\x20charset=UTF-8\r\nContent-Length:\x200\r\n\r\
SF:n");
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-04-28T09:14:15
|_  start_date: 2023-04-28T08:53:40
| smb2-security-mode: 
|   2.1: 
|_    Message signing enabled but not required
|_clock-skew: mean: 46m40s, deviation: 2h20m00s, median: 0s
| smb-os-discovery: 
|   OS: Windows Server 2008 R2 Standard 7601 Service Pack 1 (Windows Server 2008 R2 Standard 6.1)
|   OS CPE: cpe:/o:microsoft:windows_server_2008::sp1
|   Computer name: vagrant-2008R2
|   NetBIOS computer name: VAGRANT-2008R2\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2023-04-28T02:14:11-07:00
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_nbstat: NetBIOS name: VAGRANT-2008R2, NetBIOS user: <unknown>, NetBIOS MAC: 06:08:fa:4f:5b:dc (unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 218.16 seconds
```

```
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.1: 
|_    Message signing enabled but not require
```

```
root@attackdefense:~/Desktop/Win2k8# ls
nmap_10k  nmap_all
root@attackdefense:~/Desktop/Win2k8# pwd
/root/Desktop/Win2k8
```

```
root@attackdefense:~/Desktop/Win2k8# service postgresql start && msfconsole -q
Starting PostgreSQL 14 database server: main.
msf6 > workspace -a Win2k8
[*] Added workspace: Win2k8
[*] Workspace: Win2k8
msf6 > workspace
  default
* Win2k8
msf6 > db_import /root/Desktop/Win2k8/nmap_all
[*] Importing 'Nmap XML' data
[*] Import: Parsing with 'Nokogiri v1.13.0'
[*] Importing host 10.0.18.65
[*] Successfully imported /root/Desktop/Win2k8/nmap_all
```

```
msf6 > hosts

Hosts
=====

address     mac  name            os_name  os_flavor  os_sp  purpose  info  comments
-------     ---  ----            -------  ---------  -----  -------  ----  --------
10.0.18.65       demo.ine.local  Unknown                    device
```

```
msf6 > search smb_version

Matching Modules
================

   #  Name                               Disclosure Date  Rank    Check  Description
   -  ----                               ---------------  ----    -----  -----------
   0  auxiliary/scanner/smb/smb_version                   normal  No     SMB Version Detection


Interact with a module by name or index. For example info 0, use 0 or use auxiliary/scanner/smb/smb_version

msf6 > use 0
msf6 auxiliary(scanner/smb/smb_version) > show options

Module options (auxiliary/scanner/smb/smb_version):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   RHOSTS                    yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   THREADS  1                yes       The number of concurrent threads (max one per host)

msf6 auxiliary(scanner/smb/smb_version) > set RHOSTS 10.0.18.65
RHOSTS => 10.0.18.65
msf6 auxiliary(scanner/smb/smb_version) > run

[*] 10.0.18.65:445        - SMB Detected (versions:1, 2) (preferred dialect:SMB 2.1) (signatures:optional) (uptime:46m 40s) (guid:{dda6326f-83c4-4420-b5e2-969e37956c52}) (authentication domain:VAGRANT-2008R2)
[+] 10.0.18.65:445        -   Host is running Windows 2008 R2 Standard SP1 (build:7601) (name:VAGRANT-2008R2) (workgroup:WORKGROUP)
[*] 10.0.18.65:           - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf6 auxiliary(scanner/smb/smb_version) > hosts

Hosts
=====

address     mac  name            os_name          os_flavor  os_sp  purpose  info  comments
-------     ---  ----            -------          ---------  -----  -------  ----  --------
10.0.18.65       VAGRANT-2008R2  Windows 2008 R2  Standard   SP1    server
```

```
msf6 auxiliary(scanner/smb/smb_version) > services
Services
========

host        port   proto  name                  state  info
----        ----   -----  ----                  -----  ----
10.0.18.65  21     tcp    ftp                   open   Microsoft ftpd
10.0.18.65  22     tcp    ssh                   open   OpenSSH 7.1 protocol 2.0
10.0.18.65  80     tcp    http                  open   Microsoft IIS httpd 7.5
10.0.18.65  135    tcp    msrpc                 open   Microsoft Windows RPC
10.0.18.65  139    tcp    netbios-ssn           open   Microsoft Windows netbios-ssn
10.0.18.65  445    tcp    smb                   open   Windows 2008 R2 Standard SP1 (build:7601) (name:VAGRANT-2008R2) (workgroup:WORKGROUP)
10.0.18.65  1617   tcp    java-rmi              open   Java RMI
10.0.18.65  3306   tcp    mysql                 open   MySQL 5.5.20-log
10.0.18.65  3389   tcp    ssl/ms-wbt-server     open
10.0.18.65  3700   tcp    giop                  open   CORBA naming service
10.0.18.65  3820   tcp    ssl/giop              open   CORBA naming service
10.0.18.65  3920   tcp    ssl/exasoftport1      open
10.0.18.65  4848   tcp    ssl/http              open   Oracle GlassFish 4.0 Servlet 3.1; JSP 2.3; Java 1.8
10.0.18.65  5985   tcp    http                  open   Microsoft HTTPAPI httpd 2.0 SSDP/UPnP
10.0.18.65  7676   tcp    java-message-service  open   Java Message Service 301
10.0.18.65  8080   tcp    http                  open   Oracle GlassFish 4.0 Servlet 3.1; JSP 2.3; Java 1.8
10.0.18.65  8181   tcp    ssl/http              open   Oracle GlassFish 4.0 Servlet 3.1; JSP 2.3; Java 1.8
10.0.18.65  8484   tcp    http                  open   Jetty winstone-2.8
10.0.18.65  8585   tcp    http                  open   Apache httpd 2.2.21 (Win64) PHP/5.3.10 DAV/2
10.0.18.65  8686   tcp    java-rmi              open   Java RMI
10.0.18.65  9200   tcp    wap-wsp               open
10.0.18.65  9300   tcp    vrace                 open
10.0.18.65  47001  tcp    http                  open   Microsoft HTTPAPI httpd 2.0 SSDP/UPnP
10.0.18.65  49152  tcp    msrpc                 open   Microsoft Windows RPC
10.0.18.65  49153  tcp    msrpc                 open   Microsoft Windows RPC
10.0.18.65  49154  tcp    msrpc                 open   Microsoft Windows RPC
10.0.18.65  49155  tcp    msrpc                 open   Microsoft Windows RPC
10.0.18.65  49211  tcp    msrpc                 open   Microsoft Windows RPC
10.0.18.65  49214  tcp    msrpc                 open   Microsoft Windows RPC
10.0.18.65  49216  tcp    java-rmi              open   Java RMI
10.0.18.65  49220  tcp    tcpwrapped            open
10.0.18.65  49261  tcp    ssh                   open   Apache Mina sshd 0.8.0 protocol 2.0
10.0.18.65  49263  tcp    jenkins-listener      open   Jenkins TcpSlaveAgentListener
10.0.18.65  49289  tcp    java-rmi              open   Java RMI
10.0.18.65  49292  tcp                          open
10.0.18.65  49293  tcp                          open
10.0.18.65  49294  tcp                          open
```