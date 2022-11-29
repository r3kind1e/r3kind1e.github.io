---
layout: post
title: "Port Scanning & Enumeration With Nmap"
date: "2022-11-29"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Port Scanning & Enumeration With Nmap
Nmap is a free and open-source network scanner that can be used to discover hosts on a network as well as scan targets for open ports.

It can also be used to enumerate the services running on open ports as well as the operating system running on the target system.

We can output the results of our Nmap scan in to a format that can be imported into MSF for vulnerability detection and exploitation.

# 使用 Nmap 进行端口扫描和枚举
Nmap 是一种免费的开源网络扫描器，可用于发现网络上的主机以及扫描开放端口的目标。

它还可用于枚举在开放端口上运行的服务以及在目标系统上运行的操作系统。

我们可以将 Nmap 扫描的结果输出为可以导入 MSF 以进行漏洞检测和利用的格式。

# Demo: Port Scanning & Enumeration With Nmap（演示：使用 Nmap 进行端口扫描和枚举）
```
nmap 10.4.22.173
```

The target might be up. However, it is blocking the ping probes being sent by Nmap. This is very common when you're dealing with a Windows target as Windows operating system really prevents pinging. We will need to use the `-Pn` option to prevent Nmap from sending ping probes and instead just perform the port scanning.

Perform a default scan. However in this case, we are not going to be pinging.

```
nmap -Pn 10.4.22.173
```

```
nmap -Pn -sV -O 10.4.22.173
```

Export these results into a file that we will then import into the Metasploit Framework.

We can output the scan results into various file formats. Now in order to import the scan results into the Metasploit Framework, we need to output the scan into XML, which can be done by using the `-oX` option.

`-oX`: to output it into an XML file. 

Run the scan and then output all the results into an XML file called windows_server_2012. We will then import this XML file into the MSF.

``` 
nmap -Pn -sV -O 10.4.22.173 -oX windows_server_2012
ls
```

# Windows Recon: Nmap Host Discovery
## Overview
A Kali GUI machine and a target machine are provided to you. The target machine is running a Windows Firewall. 

Your task is to discover available live hosts and their open ports using Nmap and identify the running services and applications.

Instructions:

Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y.

The IP address of the target machine is mentioned in the file “/root/Desktop/target”

Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1

## Solutions
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-2219.pdf

```
Target IP Address : 10.0.28.65
```

```
root@attackdefense:~# ping -c 5 10.0.28.65
PING 10.0.28.65 (10.0.28.65) 56(84) bytes of data.

--- 10.0.28.65 ping statistics ---
5 packets transmitted, 0 received, 100% packet loss, time 4097ms
```

```
root@attackdefense:~# nmap 10.0.28.65
Starting Nmap 7.70 ( https://nmap.org ) at 2022-11-29 08:23 IST
Note: Host seems down. If it is really up, but blocking our ping probes, try -Pn
Nmap done: 1 IP address (0 hosts up) scanned in 3.12 seconds
```

Nmap also could not detect the host, it's up or not. Many security tools first ping the host before it starts scanning or exploiting the target. In that case, one has to use advanced Nmap options i.e `-A` or `-T5`, etc. in order to get the correct output.

In the nmap there is one option i.e `-Pn` (Treat all hosts as online -- skip host discovery) this option will force the scanning even if it has detected the target as down in host discovery.

```
root@attackdefense:~# nmap -Pn 10.0.28.65
Starting Nmap 7.70 ( https://nmap.org ) at 2022-11-29 08:26 IST
Nmap scan report for 10.0.28.65
Host is up (0.0027s latency).
Not shown: 992 filtered ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49154/tcp open  unknown
49155/tcp open  unknown
49175/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 4.57 seconds
```

```
root@attackdefense:~# nmap -Pn -sV -O 10.0.28.65
Starting Nmap 7.70 ( https://nmap.org ) at 2022-11-29 08:28 IST
Nmap scan report for 10.0.28.65
Host is up (0.0028s latency).
Not shown: 992 filtered ports
PORT      STATE SERVICE            VERSION
80/tcp    open  http               HttpFileServer httpd 2.3
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
49175/tcp open  msrpc              Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Microsoft Windows 2012
OS CPE: cpe:/o:microsoft:windows_server_2012
OS details: Microsoft Windows Server 2012
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 81.95 seconds
```

```
root@attackdefense:~# ls
Desktop  thinclient_drives
root@attackdefense:~# nmap -Pn -sV -O 10.0.28.65 -oX windows_server_2012
Starting Nmap 7.70 ( https://nmap.org ) at 2022-11-29 08:34 IST
Nmap scan report for 10.0.28.65
Host is up (0.0027s latency).
Not shown: 992 filtered ports
PORT      STATE SERVICE            VERSION
80/tcp    open  http               HttpFileServer httpd 2.3
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
49175/tcp open  msrpc              Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Microsoft Windows 2012
OS CPE: cpe:/o:microsoft:windows_server_2012
OS details: Microsoft Windows Server 2012
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 84.24 seconds
root@attackdefense:~# ls
Desktop  thinclient_drives  windows_server_2012
```