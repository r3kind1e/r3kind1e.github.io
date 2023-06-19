---
layout: post
title: "Setting Up A Web Server With Python"
date: "2023-06-19"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Setting Up A Web Server With Python
## Transferring Files To Target Systems
After obtaining initial access to a target system, you will need to transfer files to the target system.

In some cases, you will not have access to the target system via a Meterpreter session, and as a result, you will need to use the inbuilt OS specific utilities to facilitate the transfer of files from your system to the target system.

This process utilizes a two-step approach, where you will need to host the files you want to transfer on a web server and download the files hosted on the web server to the target system.

## Setting Up A Web Server With Python
Python comes with a built-in module known as SimpleHTTPServer(python2) and http.server (python3), that can be used to facilitate a simple HTTP server that gives you standard GET and HEAD request handlers.

This module can be used to host files in any directory of your system. And can be implemented through a single command in your terminal.

# Demo: Setting Up A Web Server With Python

# 使用Python设置Web服务器
## 将文件传输到目标系统
在获取目标系统的初始访问权限后，您需要将文件传输到目标系统。

在某些情况下，您将无法通过Meterpreter会话访问目标系统，因此，您需要使用内置的特定于操作系统的实用程序来促进从您的系统到目标系统的文件传输。

此过程采用两步方法，您需要在Web服务器上托管要传输的文件，并下载托管在Web服务器上的文件到目标系统。

## 使用Python设置Web服务器
Python带有一个内置模块，称为SimpleHTTPServer（python2）和http.server（python3），可用于促进简单的HTTP服务器，它提供标准的GET和HEAD请求处理程序。

该模块可用于在您系统的任何目录中托管文件。并可以通过终端中的单个命令来实现。

# 演示：使用Python设置Web服务器

```
ls -al /usr/share/windows-resources/mimikatz/x64/
cp /usr/share/windows-resources/mimikatz/x64/mimikatz.exe .
ls
```

I wanted to host this particular executable, and then download it onto a target system.

python2

```
python -m SimpleHTTPServer 80
```

Kali Linux : 10.10.0.2

```
http://10.10.0.2
```

python3

```
python3 -m http.server 80
```

```
cd Desktop/tools/
python3 -m http.server 80
```

# Setting Up A Web Server With Python
## Overview
Goal

This lab covers the process of setting up a web server with Python for the purpose of facilitating file from an attacker's system to the target system.

# 使用 Python 设置 Web 服务器
## 概述
目标

本实验涵盖了使用 Python 设置 Web 服务器的过程，目的是促进文件从攻击者的系统传输到目标系统。

## 复现视频内容
Kali Linux : 10.10.16.3

```
root@attackdefense:~# ls -al /usr/share/windows-resources/mimikatz/x64/
total 1304
drwxr-xr-x 2 root root    4096 Feb 19  2020 .
drwxr-xr-x 4 root root    4096 Feb 19  2020 ..
-rw-r--r-- 1 root root   36696 Jan 22  2013 mimidrv.sys
-rw-r--r-- 1 root root 1240840 Feb  8  2020 mimikatz.exe
-rw-r--r-- 1 root root   46856 Feb  8  2020 mimilib.dll
root@attackdefense:~# pwd
/root
root@attackdefense:~# cp /usr/share/windows-resources/mimikatz/x64/mimikatz.exe .
root@attackdefense:~# ls
Desktop  mimikatz.exe  thinclient_drives
```

```
root@attackdefense:~# python -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...
```

```
http://10.10.16.3/
```

```
root@attackdefense:~# python -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...
10.10.16.3 - - [19/Jun/2023 19:26:17] "GET / HTTP/1.1" 200 -
10.10.16.3 - - [19/Jun/2023 19:26:18] code 404, message File not found
10.10.16.3 - - [19/Jun/2023 19:26:18] "GET /favicon.ico HTTP/1.1" 404 -
```

```
root@attackdefense:~# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```

```
http://10.10.16.3/
```

```
root@attackdefense:~# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.10.16.3 - - [19/Jun/2023 19:28:34] "GET / HTTP/1.1" 200 -
```

```
root@attackdefense:~# cd Desktop/tools
root@attackdefense:~/Desktop/tools# ls
Delorean  JohnTheRipper  firepwd  ircsnapshot  known_hosts-hashcat  portable  reGeorg  scripts	srtp-decrypt  steganography
root@attackdefense:~/Desktop/tools# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.10.16.3 - - [19/Jun/2023 19:30:44] "GET / HTTP/1.1" 200 -
10.10.16.3 - - [19/Jun/2023 19:30:47] "GET /Delorean/ HTTP/1.1" 200 -
```