---
layout: post
title: "Banner Grabbing"
date: "2023-03-30"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# Banner Grabbing
Banner grabbing is an information gathering technique used by penetration testers to enumerate information regarding the target operating system as well as the services that are running on its open ports.

The primary objective of banner grabbing is to identify the service running on a specific port as well as the service version.

Banner grabbing can be performed through various techniques:
* Performing a service version detection scan with Nmap.
* Connecting to the open port with Netcat.
* Authenticating with the service (If the service supports authentication), for example; SSH, FTP, Telnet etc.

# 横幅抓取
横幅抓取是渗透测试人员使用的一种信息收集技术，用于枚举有关目标操作系统以及在其开放端口上运行的服务的信息。

横幅抓取的主要目的是识别在特定端口上运行的服务以及服务版本。

可以通过多种技术执行横幅抓取：
* 使用 Nmap 执行服务版本检测扫描。
* 使用 Netcat 连接到开放端口。
* 例如，使用服务进行身份验证（如果服务支持身份验证）； SSH、FTP、远程登录等。

# Demo: Banner Grabbing（演示：横幅抓取）
You start off by performing an Nmap scan to identify open ports. However, your next logical step, would be to identify these services that are running on those open ports, as well as those service versions. The next logical step would be to utilize the service, as well as the service version information, to identify vulnerabilities or exploits pertinent to that specific version of whatever service is running on a particular port.

```
ifconfig
eth1: 192.8.94.2
```

Target IP Address: 192.8.94.3

The first tool we'll be taking a loot at is Nmap.

`-sV`: Perform the standard service-version detection scan, which will not only tell us what services are running on the open ports on the target, it'll also tell us the service version.

`-O`: Perform operating system scan, and operating system scan, that will give us a rough estimate as to what is running on the target, whether it's running Windows, whether it's running Linux, and in the case of Linux, it could give us the distribution release name.

```
nmap -sV -O 192.8.94.3
```

One really cool thing that you can do with Nmap is the utilization of Nmap scripts or the Nmap scripting engine. In terms of banner grabbing, there exists a very cool script that can be found under the default storage directory for Nmap scripts.

List out the contents of `/usr/share/nmap/scripts`, and we can then pipe out the output. And we can then grep for a specific script that we're looking for. In this case, the name of the script is banner.

```
ls -al /usr/share/nmap/scripts/ | grep banner
```

The banner.nsc or the banner Nmap script can be used to identify the service banner. A banner is used for logistical purposes in that it tells a user or anyone who is trying to access that particular service, what is running on that particular port, as well as the service version. Banners can be changed, but by default that information should be obtainable.

If we wanted to use this particular Nmap script.

`-sV`: Perform a service-version detection scan.

`--script=`: Use the script. We want to use the banner script. We don't need to provide the extension, which is the `.nsc`.

```
nmap -sV --script=banner 192.8.94.3
```

And for the script results, this is the banner script. It gives us pretty much the same information that we would have gotten if we had performed the scan with a typical service-version detection parameters specified.

This is just a really quick and cool Nmap script that can be used to perform banner grabbing on particular services that might not necessarily reveal what service version is running when you perform a standard service-version detection scan. You'll come across quite a few services that you're not be able to identify what is running, as well as the service version, and this script can be quite helpful.

That is how to perform banner grabbing with Nmap.

The next tool is Netcat.

```
whatis nc
man nc
```

For example, we know that the target is running SSH on port 22. We can perform banner grabbing with Netcat by saying:

```
nc 192.8.94.3 22
```

However, many penetration testers still rely on Netcat, as in certain scenarios, Nmap might not be able to give you an accurate idea of what service is running, as well as the service version.

Why is this information important? A penetration tester will typically take this information and utilize a tool like `searchsploit`.

```
searchsploit openssh 7.2
```

That is how to perform banner grabbing with Netcat.

In certain scenarios, a particular service may support authentication. And in this case, SSH does support authentication. You can also retrieve the banner by authenticating with the target system via SSH, and you don't need legitimate credentials.

```
ssh root@192.8.94.3
```

However, for other services that support authentication, like FTP or Telnet, you can also try and utilize the standard authentication client to identify a banner or information that can be useful later on during exploitation.

That is how to perform banner grabbing with Nmap, Netcat, as well as the relevant authentication clients pertinent to the service that's running on a target system. 

# 复现视频内容
```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.146.171.2  netmask 255.255.255.0  broadcast 192.146.171.255
        ether 02:42:c0:92:ab:02  txqueuelen 0  (Ethernet)
```

Target IP Address: 192.146.171.3

```
root@attackdefense:~# nmap -sV -O 192.146.171.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-03-29 14:05 UTC
Nmap scan report for target-1 (192.146.171.3)
Host is up (0.000031s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
MAC Address: 02:42:C0:92:AB:03 (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.70%E=4%D=3/29%OT=22%CT=1%CU=30130%PV=N%DS=1%DC=D%G=Y%M=0242C0%T
OS:M=642445B3%P=x86_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=10E%TI=Z%CI=Z%II=I
OS:%TS=A)OPS(O1=M5B4ST11NW7%O2=M5B4ST11NW7%O3=M5B4NNT11NW7%O4=M5B4ST11NW7%O
OS:5=M5B4ST11NW7%O6=M5B4ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6
OS:=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M5B4NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O
OS:%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=
OS:0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%
OS:S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(
OS:R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=
OS:N%T=40%CD=S)

Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.14 seconds
```

```
root@attackdefense:~# ls -al /usr/share/nmap/scripts/ | grep banner
-rw-r--r-- 1 root root  6129 Jan  9  2019 banner.nse
```

```
root@attackdefense:~# nmap -sV --script=banner 192.146.171.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-03-29 14:11 UTC
Nmap scan report for target-1 (192.146.171.3)
Host is up (0.000010s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
|_banner: SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
MAC Address: 02:42:C0:92:AB:03 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.55 seconds
```

```
root@attackdefense:~# whatis nc
nc (1)               - TCP/IP swiss army knife
root@attackdefense:~# man nc
```

```
root@attackdefense:~# nc 192.146.171.3 22
SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6

Protocol mismatch.
```

```
root@attackdefense:~# searchsploit openssh 7.2
-------------------------------------------------------------------------------------------- ----------------------------------------
 Exploit Title                                                                              |  Path
                                                                                            | (/usr/share/exploitdb/)
-------------------------------------------------------------------------------------------- ----------------------------------------
OpenSSH 7.2 - Denial of Service                                                             | exploits/linux/dos/40888.py
OpenSSH 7.2p1 - (Authenticated) xauth Command Injection                                     | exploits/multiple/remote/39569.py
OpenSSH 7.2p2 - Username Enumeration                                                        | exploits/linux/remote/40136.py
OpenSSHd 7.2p2 - Username Enumeration                                                       | exploits/linux/remote/40113.txt
-------------------------------------------------------------------------------------------- ----------------------------------------
Shellcodes: No Result
```

```
root@attackdefense:~# ssh root@192.146.171.3
The authenticity of host '192.146.171.3 (192.146.171.3)' can't be established.
ECDSA key fingerprint is SHA256:dxlBXgBb0Iv5/LmemZ2Eikb5+GLl9CSLf/B854fUeV8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.146.171.3' (ECDSA) to the list of known hosts.
Welcome to attack defense ssh recon lab!!
root@192.146.171.3's password: 
Permission denied, please try again.
root@192.146.171.3's password: 
Permission denied, please try again.
root@192.146.171.3's password: 
root@192.146.171.3: Permission denied (publickey,password).
```

# SSH Recon: Basic
## Overview
In this challenge we will look at the basics of SSH server reconnaissance. Please start the lab and answer the following questions:

Questions
1. What is the version of SSH server.
2. Fetch the banner using netcat and check the version of SSH server.
3. Fetch pre-login SSH banner.
4. How many “encryption_algorithms” are supported by the SSH server.
5. What is the ssh-rsa host key being used by the SSH server.
6. Which authentication method is being used by the SSH server for user “student”.
7. Which authentication method is being used by the SSH server for user “admin”.
8. Fetch the flag from /home/student/FLAG by using nmap ssh-run script.

Instructions: 
* This lab is dedicated to you! No other users are on this network
* Once you start the lab, you will have access to a root terminal of a Kali instance
* Your Kali has an interface with IP address 192.X.Y.Z. Run "ip addr" to know the values of X and Y.
* The Target machine should be located at the IP address 192.X.Y.3.
* Do not attack the gateway located at IP address 192.X.Y.1

## Tasks
Pre-requisites
1. Basic familiarity with TCP & UDP.

Requirements

This task does not have any requirements.

## Solutions
The solution for this lab can be found in the following manual:  https://assets.ine.com/labs/ad-manuals/walkthrough-526.pdf

# SSH 侦察：基本
## 概述
在这个挑战中，我们将了解 SSH 服务器侦察的基础知识。请开始实验并回答以下问题：

问题
1. SSH服务器的版本是多少。
2. 使用 netcat 获取横幅并检查 SSH 服务器的版本。
3. 获取登录前 SSH 横幅。
4. SSH 服务器支持多少“encryption_algorithms”。
5. SSH 服务器使用的 ssh-rsa 主机密钥是什么。
6. SSH 服务器对用户“student”使用哪种身份验证方法。
7. SSH 服务器对用户“admin”使用哪种身份验证方法。
8. 使用 nmap ssh-run 脚本从 /home/student/FLAG 获取标志。

指示： 
* 这个实验室是献给你的！该网络上没有其他用户
* 开始实验后，您将可以访问 Kali 实例的根终端
* 你的 Kali 有一个 IP 地址为 192.X.Y.Z 的接口运行“ip addr”来了解 X 和 Y 的值。
* 目标机器应位于 IP 地址 192.X.Y.3。
* 不要攻击位于 IP 地址 192.X.Y.1 的网关

## 任务
先决条件
1. 基本熟悉 TCP 和 UDP。

要求

此任务没有任何要求。

## 解决方案
本实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-526.pdf

# 我自己的思路
```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.73.228.2  netmask 255.255.255.0  broadcast 192.73.228.255
        ether 02:42:c0:49:e4:02  txqueuelen 0  (Ethernet)
```

Target IP Address: 192.73.228.3

What is the version of SSH server.

`OpenSSH 7.2p2 Ubuntu 4ubuntu2.6`

```
root@attackdefense:~# nmap -sV --script=banner 192.73.228.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-03-30 02:41 UTC
Nmap scan report for target-1 (192.73.228.3)
Host is up (0.000010s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
|_banner: SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
MAC Address: 02:42:C0:49:E4:03 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.54 seconds
```

Fetch the banner using netcat and check the version of SSH server.

`SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6`

```
root@attackdefense:~# nc 192.73.228.3 22
SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6

Protocol mismatch.
```

Fetch pre-login SSH banner.

`Welcome to attack defense ssh recon lab!!`

```
root@attackdefense:~# ssh root@192.73.228.3
Welcome to attack defense ssh recon lab!!
root@192.73.228.3's password: 
Permission denied, please try again.
```

How many “encryption_algorithms” are supported by the SSH server.

`6`

[ssh2-enum-algos](https://nmap.org/nsedoc/scripts/ssh2-enum-algos.html)

Script ssh2-enum-algos

Script Summary

Reports the number of algorithms (for encryption, compression, etc.) that the target SSH2 server offers. If verbosity is set, the offered algorithms are each listed by type.

If the "client to server" and "server to client" algorithm lists are identical (order specifies preference) then the list is shown only once under a combined type.

报告目标 SSH2 服务器提供的算法数量（用于加密、压缩等）。如果设置了详细程度，则提供的算法均按类型列出。

如果“客户端到服务器”和“服务器到客户端”算法列表相同（顺序指定首选项），则该列表在组合类型下仅显示一次。

```
root@attackdefense:~# nmap --script=ssh2-enum-algos 192.73.228.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-03-30 02:47 UTC
Nmap scan report for target-1 (192.73.228.3)
Host is up (0.000020s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
| ssh2-enum-algos: 
|   kex_algorithms: (6)
|       curve25519-sha256@libssh.org
|       ecdh-sha2-nistp256
|       ecdh-sha2-nistp384
|       ecdh-sha2-nistp521
|       diffie-hellman-group-exchange-sha256
|       diffie-hellman-group14-sha1
|   server_host_key_algorithms: (5)
|       ssh-rsa
|       rsa-sha2-512
|       rsa-sha2-256
|       ecdsa-sha2-nistp256
|       ssh-ed25519
|   encryption_algorithms: (6)
|       chacha20-poly1305@openssh.com
|       aes128-ctr
|       aes192-ctr
|       aes256-ctr
|       aes128-gcm@openssh.com
|       aes256-gcm@openssh.com
|   mac_algorithms: (10)
|       umac-64-etm@openssh.com
|       umac-128-etm@openssh.com
|       hmac-sha2-256-etm@openssh.com
|       hmac-sha2-512-etm@openssh.com
|       hmac-sha1-etm@openssh.com
|       umac-64@openssh.com
|       umac-128@openssh.com
|       hmac-sha2-256
|       hmac-sha2-512
|       hmac-sha1
|   compression_algorithms: (2)
|       none
|_      zlib@openssh.com
MAC Address: 02:42:C0:49:E4:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.43 seconds
```

What is the ssh-rsa host key being used by the SSH server.

```
AAAAB3NzaC1yc2EAAAADAQABAAABAQC1fkJK7F8yxf3vewEcLYHljBnKTAiRqzFxkFo6lqyew73ATL2Abyh6at/oOmBSlPI90rtAMA6jQGJ+0HlHgf7mkjz5+CBo9j2VPu1bejYtcxpqpHcL
5Bp12wgey1zup74fgd+yOzILjtgbnDOw1+HSkXqN79d+4BnK0QF6T9YnkHvBhZyjzIDmjonDy92yVBAIoB6Rdp0w7nzFz3aN9gzB5MW/nSmgc4qp7R6xtzGaqZKp1H3W3McZO3RELjGzvHOdRkAKL7n2kyVA
raSUrR0Oo5m5e/sXrITYi9y0X6p2PTUfYiYvgkv/3xUF+5YDDA33AJvv8BblnRcRRZ74BxaD
```

[ssh-hostkey](https://nmap.org/nsedoc/scripts/ssh-hostkey.html)

Script ssh-hostkey

Script Summary

Shows SSH hostkeys.

Shows the target SSH server's key fingerprint and (with high enough verbosity level) the public key itself. It records the discovered host keys in nmap.registry for use by other scripts. Output can be controlled with the ssh_hostkey script argument.

You may also compare the retrieved key with the keys in your known-hosts file using the known-hosts argument.

The script also includes a postrule that check for duplicate hosts using the gathered keys.

显示 SSH 主机密钥。

显示目标 SSH 服务器的密钥指纹和（具有足够高的详细级别）公钥本身。它记录发现的主机密钥以nmap.registry供其他脚本使用。可以使用脚本参数控制输出ssh_hostkey。

您还可以使用参数将检索到的密钥与已知主机文件中的密钥进行比较known-hosts。

该脚本还包括一个使用收集到的密钥检查重复主机的规则。

```
root@attackdefense:~# nmap --script ssh-hostkey 192.73.228.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-03-30 02:58 UTC
Nmap scan report for target-1 (192.73.228.3)
Host is up (0.000010s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
| ssh-hostkey: 
|   2048 b0:2b:8d:3e:4c:de:f9:ab:7f:86:47:fd:c8:d2:e0:a8 (RSA)
|   256 75:6f:7d:4e:d7:f7:6d:ec:23:d1:8e:39:76:94:86:8a (ECDSA)
|_  256 58:16:43:a6:65:2c:0b:0a:22:60:86:31:6f:59:38:12 (ED25519)
MAC Address: 02:42:C0:49:E4:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.48 seconds
```

```
root@attackdefense:~# nmap --script ssh-hostkey --script-args ssh_hostkey=full 192.73.228.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-03-30 03:46 UTC
Nmap scan report for target-1 (192.73.228.3)
Host is up (0.000010s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
| ssh-hostkey: 
|   ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC1fkJK7F8yxf3vewEcLYHljBnKTAiRqzFxkFo6lqyew73ATL2Abyh6at/oOmBSlPI90rtAMA6jQGJ+0HlHgf7mkjz5+CBo9j2VPu1bejYtcxpqpHcL
5Bp12wgey1zup74fgd+yOzILjtgbnDOw1+HSkXqN79d+4BnK0QF6T9YnkHvBhZyjzIDmjonDy92yVBAIoB6Rdp0w7nzFz3aN9gzB5MW/nSmgc4qp7R6xtzGaqZKp1H3W3McZO3RELjGzvHOdRkAKL7n2kyVA
raSUrR0Oo5m5e/sXrITYi9y0X6p2PTUfYiYvgkv/3xUF+5YDDA33AJvv8BblnRcRRZ74BxaD
|   ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBB0cJ/kSOXBWVIBA2QH4UB6r7nFL5l7FwHubbSZ9dIs2JSmn/oIgvvQvxmI5YJxkdxRkQlF01KLDmVgE
SYXyDT4=
|_  ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKuZlCFfTgeaMC79zla20ZM2q64mjqWhKPw/2UzyQ2W/
MAC Address: 02:42:C0:49:E4:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.50 seconds
```

Which authentication method is being used by the SSH server for user “student”.

`none_auth`

[Script ssh-auth-methods](https://nmap.org/nsedoc/scripts/ssh-auth-methods.html)

Script Summary

Returns authentication methods that a SSH server supports.

This is in the "intrusive" category because it starts an authentication with a username which may be invalid. The abandoned connection will likely be logged.

返回 SSH 服务器支持的身份验证方法。

这属于“侵入式”类别，因为它使用可能无效的用户名启动身份验证。被放弃的连接可能会被记录下来。

```
root@attackdefense:~# nmap -p 22 --script ssh-auth-methods --script-args="ssh.user=student" 192.73.228.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-03-30 03:04 UTC
Nmap scan report for target-1 (192.73.228.3)
Host is up (0.00022s latency).

PORT   STATE SERVICE
22/tcp open  ssh
| ssh-auth-methods: 
|_  Supported authentication methods: none_auth
MAC Address: 02:42:C0:49:E4:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.46 seconds
```

```
root@attackdefense:~# ssh student@192.73.228.3
Welcome to attack defense ssh recon lab!!
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 5.4.0-125-generic x86_64)

student@victim-1:~$ whoami
student
student@victim-1:~$ id
uid=999(student) gid=999(student) groups=999(student)
```

Which authentication method is being used by the SSH server for user “admin”.

`publickey, password`

```
root@attackdefense:~# nmap -p 22 --script ssh-auth-methods --script-args="ssh.user=admin" 192.73.228.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-03-30 03:07 UTC
Nmap scan report for target-1 (192.73.228.3)
Host is up (0.000042s latency).

PORT   STATE SERVICE
22/tcp open  ssh
| ssh-auth-methods: 
|   Supported authentication methods: 
|     publickey
|_    password
MAC Address: 02:42:C0:49:E4:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 2.74 seconds
```

Fetch the flag from /home/student/FLAG by using nmap ssh-run script.

`e1e3c0c9d409f594afdb18fe9ce0ffec`

[Script ssh-run](https://nmap.org/nsedoc/scripts/ssh-run.html)

脚本ssh-run

Script Summary

Runs remote command on ssh server and returns command output.

在 ssh 服务器上运行远程命令并返回命令输出。

```
root@attackdefense:~# nmap -p 22 --script=ssh-run --script-args="ssh-run.cmd=cat /home/student/FLAG,ssh-run.username=student,ssh-run.password=''" 192.73.228
.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-03-30 03:13 UTC
NSE: [ssh-run] Authenticated
NSE: [ssh-run] Running command: cat /home/student/FLAG
NSE: [ssh-run] Output of command: e1e3c0c9d409f594afdb18fe9ce0ffec

Nmap scan report for target-1 (192.73.228.3)
Host is up (0.000057s latency).

PORT   STATE SERVICE
22/tcp open  ssh
| ssh-run: 
|   output: 
|_    e1e3c0c9d409f594afdb18fe9ce0ffec\x0D
MAC Address: 02:42:C0:49:E4:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.45 seconds
```

