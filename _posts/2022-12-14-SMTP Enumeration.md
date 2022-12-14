---
layout: post
title: "SMTP Enumeration"
date: "2022-12-14"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# SMTP Enumeration
SMTP (Simple Mail Transfer Protocol) is a communication protocol that is used for the transmission of email.

SMTP uses TCP port 25 by default. It is can also be configured to run on TCP port 465 and 587.

We can utilize auxiliary modules to enumerate the version of SMTP as well as user accounts on the target system.

# SMTP 枚举
SMTP（简单邮件传输协议）是一种用于传输电子邮件的通信协议。

SMTP 默认使用 TCP 端口 25。 它还可以配置为在 TCP 端口 465 和 587 上运行。

It can also be configured to run on TCP port 465 and 587 if an SSL or TLS certificate has been set up for encryption.

我们可以利用辅助模块来枚举 SMTP 的版本以及目标系统上的用户帐户。

We can also utilize auxiliary modules to perform a brute force attack on SMTP in order to obtain or to find various user accounts on the target system. So that's one of the great things with SMTP is that through the various auxiliary modules, we can also identify users on the target system. And this information can be very useful is we have `SSH` open on the target. And we can then use that username to perform a brute force on SSH and then gain access via SSH. 

# Demo: SMTP Enumeration（演示：SMTP 枚举）
```
ifconfig
eth1: 192.108.85.2
```

```
service postgresql start
msfconsole -q
workspace -a SMTP_Enum
setg RHOSTS 192.108.85.3
```

```
search type:auxiliary name:smtp
```

```
use auxiliary/scanner/smtp/smtp_version
show options
run
```

It also gives us the email address, or rather the domain for this particular SMTP server.

Performs user enumeration. Enumerate user accounts.

```
use auxiliary/scanner/smtp/smtp_enum
info
run
```

All of this information can be very useful in staging other attacks. For example, if we had SSH running on this server, then we could use these usernames to perform a brute force. And having a username narrows down your brute force attack and makes it more efficient.

# Postfix Recon: Basics
## Overview
In this challenge we will look at the basics of Postfix SMTP server reconnaissance. Please start the lab and answer the following questions:

Questions

1. What is the SMTP server name and banner.
2. Connect to SMTP service using netcat and retrieve the hostname of the server (domain name).
3. Does user “admin” exist on the server machine? Connect to SMTP service using netcat and check manually.
4. Does user “commander” exist on the server machine? Connect to SMTP service using netcat and check manually.
5. What commands can be used to check the supported commands/capabilities? Connect to SMTP service using telnet and check.
6. How many of the common usernames present in the dictionary /usr/share/commix/src/txt/usernames.txt exist on the server. Use smtp-user-enum tool for this task.
7. How many common usernames present in the dictionary /usr/share/metasploit-framework/data/wordlists/unix_users.txt exist on the server. Use suitable metasploit module for this task.
8. Connect to SMTP service using telnet and send a fake mail to root user.
9. Send a fake mail to root user using sendemail command.

Instructions: 
* This lab is dedicated to you! No other users are on this network :)
* Once you start the lab, you will have access to a root terminal of a Kali instance
* Your Kali has an interface with IP address 192.X.Y.Z. Run "ip addr" to know the values of X and Y.
* The Target machine should be located at the IP address 192.X.Y.3.
* Do not attack the gateway located at IP address 192.X.Y.1


## Solutions
The solution for this lab can be found in the following manual:  https://assets.ine.com/labs/ad-manuals/walkthrough-516.pdf

# Postfix侦察：基础知识
## 概述
在这个挑战中，我们将了解 Postfix SMTP 服务器侦察的基础知识。请开始实验并回答以下问题：

问题

1. 什么是 SMTP 服务器名称和横幅。
2. 使用 netcat 连接到 SMTP 服务并检索服务器的主机名（域名）。
3. 服务器计算机上是否存在用户“admin”？使用 netcat 连接到 SMTP 服务并手动检查。
4. 服务器计算机上是否存在用户“commander”？使用 netcat 连接到 SMTP 服务并手动检查。
5. 可以使用哪些命令来检查支持的命令/功能？使用 telnet 连接到 SMTP 服务并检查。
6. 服务器上存在字典 /usr/share/commix/src/txt/usernames.txt 中存在的常见用户名的数量。使用 smtp-user-enum 工具来完成这个任务。
7. 服务器上存在字典 /usr/share/metasploit-framework/data/wordlists/unix_users.txt 中存在的常见用户名数量。为此任务使用合适的 metasploit 模块。
8. 使用 telnet 连接到 SMTP 服务并向 root 用户发送虚假邮件。
9. 使用 sendemail 命令向 root 用户发送虚假邮件。

指示： 

* 这个实验室是献给你的！该网络上没有其他用户:)
* 开始实验后，您将可以访问 Kali 实例的根终端
* 你的 Kali 有一个 IP 地址为 192.XYZ 的接口运行“ip addr”来了解 X 和 Y 的值。
* 目标机器应位于 IP 地址 192.XY3。
* 不要攻击位于 IP 地址 192.XY1 的网关

## 解决方案
本实验室的解决方案可在以下手册中找到： https://assets.ine.com/labs/ad-manuals/walkthrough-516.pdf

## 我自己的思路
```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.89.216.2  netmask 255.255.255.0  broadcast 192.89.216.255
        ether 02:42:c0:59:d8:02  txqueuelen 0  (Ethernet)
```

What is the SMTP server name and banner.

`server name: ESMTP Postfix`

`banner: 220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.`

[Script banner](https://nmap.org/nsedoc/scripts/banner.html)

```
root@attackdefense:~# nmap -sV --script=banner 192.89.216.3
Starting Nmap 7.70 ( https://nmap.org ) at 2022-12-14 08:12 UTC
Nmap scan report for target-1 (192.89.216.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
|_banner: 220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
MAC Address: 02:42:C0:59:D8:03 (Unknown)
Service Info: Host:  openmailbox.xyz

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.62 seconds
```

```
msf5 > setg RHOSTS 192.251.178.3
RHOSTS => 192.251.178.3
msf5 > use auxiliary/scanner/smtp/smtp_version
msf5 auxiliary(scanner/smtp/smtp_version) > workspace -a SMTP_Enum
[*] Added workspace: SMTP_Enum
[*] Workspace: SMTP_Enum
msf5 auxiliary(scanner/smtp/smtp_version) > workspace
  default
* SMTP_Enum
msf5 auxiliary(scanner/smtp/smtp_version) > run

[+] 192.251.178.3:25      - 192.251.178.3:25 SMTP 220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.\x0d\x0a
[*] 192.251.178.3:25      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Connect to SMTP service using netcat and retrieve the hostname of the server (domain name).

`openmailbox.xyz`

```
root@attackdefense:~# nc 192.89.216.3 25
220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
```

Does user “admin” exist on the server machine? Connect to SMTP service using netcat and check manually.

`Yes, user "admin" exists on the server machine.`

[SMTP Commands](https://www.geeksforgeeks.org/smtp-commands/)

```
7.	VRFY	VRFY<SP><string><CRLF>	It is use to confirm or verify the user name.	Highly recommended
```

[SMTP errors and reply codes](https://serversmtp.com/smtp-error/)

252 服务器无法验证用户，但无论如何都会尝试传递消息。收件人的电子邮件帐户有效，但无法验证。通常，服务器将消息中继到另一个能够检查它的服务器。

```
root@attackdefense:~# nc 192.89.216.3 25
220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
VRFY admin
252 2.0.0 admin
```

Does user “commander” exist on the server machine? Connect to SMTP service using netcat and check manually.

`No, user "commander" doesn't exist on the server machine.`

550 未采取请求的操作：邮箱不可用（例如，找不到邮箱、无法访问或出于策略原因拒绝命令）

[550 Blocked 错误的原因是什么？](https://www.crazydomains.sg/help/550-blocked-error-explained/#:~:text=550%20Blocked%20error%20or%20550,server%2C%20and%20returned%20to%20you.)

找不到或不存在收件人的电子邮件地址。

```
root@attackdefense:~# nc 192.89.216.3 25
220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
VRFY commander
550 5.1.1 <commander>: Recipient address rejected: User unknown in local recipient table
```

What commands can be used to check the supported commands/capabilities? Connect to SMTP service using telnet and check.

[Simple Mail Transfer Protocol](https://www.ibm.com/docs/en/i/7.1?topic=information-smtp)

```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.251.178.2  netmask 255.255.255.0  broadcast 192.251.178.255
        ether 02:42:c0:fb:b2:02  txqueuelen 0  (Ethernet)
```

```
root@attackdefense:~# telnet 192.251.178.3 25
Trying 192.251.178.3...
Connected to 192.251.178.3.
Escape character is '^]'.
220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
HELP
502 5.5.2 Error: command not recognized
```

How many of the common usernames present in the dictionary /usr/share/commix/src/txt/usernames.txt exist on the server. Use smtp-user-enum tool for this task.

`8`

[smtp-user-enum](https://www.kali.org/tools/smtp-user-enum/)

smtp 用户枚举

主要用于针对默认 Solaris SMTP 服务的用户名猜测工具。可以使用 EXPN、VRFY 或 RCPT TO。

```
smtp-user-enum v1.2 ( http://pentestmonkey.net/tools/smtp-user-enum )

用法：smtp-user-enum [选项]（-u 用户名|-U 用户名文件）（-t 主机|-T 目标文件）

选项是：
         -m n 最大进程数（默认值：5）
-M mode 用于用户名猜测 EXPN、VRFY 或 RCPT 的方法（默认值：VRFY）
-u user 检查用户是否存在于远程系统
-f addr MAIL FROM 电子邮件地址。 仅用于“RCPT TO”模式（默认：user@example.com）
         -D dom 域附加到提供的用户列表以创建电子邮件地址（默认值：无）
                当您想猜测有效的电子邮件地址而不仅仅是用户名时，请使用此选项，例如 “-D example.com”会猜测 foo@example.com、bar@example.com 等。而不仅仅是用户名 foo 和 bar。
-U file 要通过 smtp 服务检查的用户名文件
-t host 运行smtp服务的服务器主机
-T file 运行 smtp 服务的主机名文件
-p port smtp 服务运行的 TCP 端口（默认值：25）
-d 调试输出
-w n 最多等待 n 秒回复（默认值：5）
-v 详细
-h 这个帮助信息

另请参阅 smtp-user-enum tar ball 中的 smtp-user-enum-user-docs.pdf。

例子：

$ smtp-user-enum -M VRFY -U users.txt -t 10.0.0.1
$ smtp-user-enum -M EXPN -u admin1 -t 10.0.0.1
$ smtp-user-enum -M RCPT -U users.txt -T mail-server-ips.txt
$ smtp-user-enum -M EXPN -D example.com -U users.txt -t 10.0.0.1
```

```
root@attackdefense:~# smtp-user-enum -M VRFY -U /usr/share/commix/src/txt/usernames.txt -t 192.251.178.3
Starting smtp-user-enum v1.2 ( http://pentestmonkey.net/tools/smtp-user-enum )

 ----------------------------------------------------------
|                   Scan Information                       |
 ----------------------------------------------------------

Mode ..................... VRFY
Worker Processes ......... 5
Usernames file ........... /usr/share/commix/src/txt/usernames.txt
Target count ............. 1
Username count ........... 123
Target TCP port .......... 25
Query timeout ............ 5 secs
Target domain ............ 

######## Scan started at Wed Dec 14 11:10:45 2022 #########
 exists.178.3: admin
 exists.178.3: administrator
 exists.178.3: mail
 exists.178.3: postmaster
 exists.178.3: root
 exists.178.3: sales
 exists.178.3: support
 exists.178.3: www-data
######## Scan completed at Wed Dec 14 11:10:46 2022 #########
8 results.

123 queries in 1 seconds (123.0 queries / sec)
```

How many common usernames present in the dictionary /usr/share/metasploit-framework/data/wordlists/unix_users.txt exist on the server. Use suitable metasploit module for this task.

`20`

```
msf5 > search type:auxiliary name:smtp

Matching Modules
================

   #  Name                                     Disclosure Date  Rank    Check  Description
   -  ----                                     ---------------  ----    -----  -----------
   1  auxiliary/client/smtp/emailer                             normal  No     Generic Emailer (SMTP)
   2  auxiliary/dos/smtp/sendmail_prescan      2003-09-17       normal  No     Sendmail SMTP Address prescan Memory Corruption
   3  auxiliary/fuzzers/smtp/smtp_fuzzer                        normal  Yes    SMTP Simple Fuzzer
   4  auxiliary/scanner/smtp/smtp_enum                          normal  Yes    SMTP User Enumeration Utility
   5  auxiliary/scanner/smtp/smtp_ntlm_domain                   normal  Yes    SMTP NTLM Domain Extraction
   6  auxiliary/scanner/smtp/smtp_relay                         normal  Yes    SMTP Open Relay Detection
   7  auxiliary/scanner/smtp/smtp_version                       normal  Yes    SMTP Banner Grabber
   8  auxiliary/server/capture/smtp                             normal  No     Authentication Capture: SMTP
```

[SMTP User Enumeration Utility](https://www.rapid7.com/db/modules/auxiliary/scanner/smtp/smtp_enum/)

SMTP 用户枚举实用程序

SMTP 服务有两个允许用户枚举的内部命令：VRFY（确认有效用户的名称）和 EXPN（显示用户别名和电子邮件列表（邮件列表）的实际地址）。通过执行这些 SMTP 命令可以揭示有效用户列表。

```
msf5 auxiliary(scanner/smtp/smtp_version) > use auxiliary/scanner/smtp/smtp_enum
msf5 auxiliary(scanner/smtp/smtp_enum) > show options

Module options (auxiliary/scanner/smtp/smtp_enum):

   Name       Current Setting                                                Required  Description
   ----       ---------------                                                --------  -----------
   RHOSTS     192.251.178.3                                                  yes       The target address range or CIDR identifier
   RPORT      25                                                             yes       The target port (TCP)
   THREADS    1                                                              yes       The number of concurrent threads
   UNIXONLY   true                                                           yes       Skip Microsoft bannered servers when testing unix users
   USER_FILE  /usr/share/metasploit-framework/data/wordlists/unix_users.txt  yes       The file that contains a list of probable users accounts.

msf5 auxiliary(scanner/smtp/smtp_enum) > run

[*] 192.251.178.3:25      - 192.251.178.3:25 Banner: 220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
[+] 192.251.178.3:25      - 192.251.178.3:25 Users found: , admin, administrator, backup, bin, daemon, games, gnats, irc, list, lp, mail, man, news, nobody, postmaster, proxy, sync, sys, uucp, www-data
[*] 192.251.178.3:25      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Connect to SMTP service using telnet and send a fake mail to root user.

[Send an SMTP email using Telnet](https://docs.sendgrid.com/for-developers/sending-email/getting-started-smtp#send-an-smtp-email-using-telnet)

```
root@attackdefense:~# telnet 192.251.178.3 25
Trying 192.251.178.3...
Connected to 192.251.178.3.
Escape character is '^]'.
220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
MAIL FROM:openmailbox.xyz
250 2.1.0 Ok
RCPT TO:root
250 2.1.5 Ok
DATA
354 End data with <CR><LF>.<CR><LF>
"This is a fake mail."
.
250 2.0.0 Ok: queued as 8C66DC2F2D2
quit
221 2.0.0 Bye
Connection closed by foreign host.
```

Send a fake mail to root user using sendemail command.

[sendemail](https://www.kali.org/tools/sendemail/)

SendEmail 是一个轻量级的、完全基于命令行的 SMTP 电子邮件代理。它被设计用于 bash 脚本、Perl 程序和网站，但它在许多其他上下文中也非常有用。

SendEmail 是用 Perl 编写的，它的独特之处在于它不需要特殊的模块。它有一个直接的界面，使它非常容易使用。

```
root@kali:~# sendEmail -h

Brandon Zehm <caspian@dotconf.net> 的 sendEmail-1.56

概要：sendEmail -f ADDRESS [选项]

   必需的：
     -f 来自（发件人）电子邮件地址的地址
     * 至少需要一个收件人通过 -t、-cc 或 -bcc
     * 消息正文需要通过 -m、STDIN 或 -o message-file=FILE

   常见的：
     -t ADDRESS [ADDR ...] 到电子邮件地址
     -u SUBJECT 邮件主题
     -m MESSAGE 消息体
     -s SERVER[:PORT] smtp 邮件中继，默认为 localhost:25
     -S [SENDMAIL_PATH] 使用本地 sendmail 实用程序（默认：/usr/bin/sendmail）而不是网络 MTA

   可选的：
     -a FILE [FILE ...] 文件附件
     -cc ADDRESS [ADDR ...] 抄送电子邮件地址
     -bcc ADDRESS [ADDR ...] bcc 电子邮件地址
     -xu USERNAME 用于 SMTP 身份验证的用户名
     -xp PASSWORD 用于SMTP认证的密码

   超自然现象：
     -b BINDADDR[:PORT] 本地主机绑定地址
     -l LOGFILE 记录到指定文件
     -v 冗长，多次使用以获得更好的效果
     -q 保持安静（即没有 STDOUT 输出）
     -o NAME=VALUE 高级选项，详情请尝试：--help misc
         -o 消息内容类型=<自动|文本|html>
         -o 消息文件=FILE -o 消息格式=raw
         -o message-header=HEADER -o message-charset=字符集
         -o 回复=ADDRESS -o timeout=SECONDS
         -o 用户名=用户名 -o 密码=密码
         -o tls=<自动|是|否> -o fqdn=FQDN


   帮助：
     --help 您正在阅读的有用概述
     --help addressing 解释寻址和相关选项
     --help message 解释消息正文输入及相关选项
     --help networking 解释-s、-b等
     --help 输出解释日志记录和其他输出选项
     --help misc explain -o options, TLS, SMTP auth, and more
```

```
root@attackdefense:~# sendEmail -f administrator@openmailbox.xyz -t root@openmailbox.xyz -u "fake email" -m "this is a fake email."
Dec 14 12:22:17 attackdefense sendEmail[385]: ERROR => Connection attempt to localhost:25 failed: IO::Socket::INET6: connect: Connection refused
```

## 参考答案
In this challenge we will look at the basics of Postfix SMTP server reconnaissance. Please start the lab and answer the following questions:

Questions

```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.229.175.2  netmask 255.255.255.0  broadcast 192.229.175.255
        ether 02:42:c0:e5:af:02  txqueuelen 0  (Ethernet)
```

What is the SMTP server name and banner.

`server name: Postfix`

`banner: openmailbox.xyz ESMTP Postfix: Welcome to our mail server.`

```
root@attackdefense:~# nmap -sV -script banner 192.229.175.3
Starting Nmap 7.70 ( https://nmap.org ) at 2022-12-14 14:17 UTC
Nmap scan report for target-1 (192.229.175.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
|_banner: 220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
MAC Address: 02:42:C0:E5:AF:03 (Unknown)
Service Info: Host:  openmailbox.xyz

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.60 seconds
```

Connect to SMTP service using netcat and retrieve the hostname of the server (domain name).

`openmailbox.xyz`

```
root@attackdefense:~# nc 192.229.175.3 25
220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
```

Does user “admin” exist on the server machine? Connect to SMTP service using netcat and check manually.

`Yes`

```
VRFY admin@openmailbox.xyz
252 2.0.0 admin@openmailbox.xyz
```

Does user “commander” exist on the server machine? Connect to SMTP service using netcat and check manually.

`No`

```
VRFY commander@openmailbox.xyz
550 5.1.1 <commander@openmailbox.xyz>: Recipient address rejected: User unknown in local recipient table
```

What commands can be used to check the supported commands/capabilities? Connect to SMTP service using telnet and check.

HELO命令：识别发送主机的域名给SMTP

目的：在您发出 MAIL FROM 命令之前，使用 HELO 命令来识别发送主机到 SMTP 的域名。

格式：`HELO domain_name`

参数：域名：指定发送主机的域名。

用法：HELO 命令在 MAIL FROM 命令之前发送一次。

EHLO命令：识别发送主机的域名给SMTP

目的：在发出 MAIL FROM 命令之前，使用 EHLO 命令来识别发送主机到 SMTP 的域名。

格式：EHLO domain_name

参数：域名：指定发送主机的域名。

```
HELO attacker.xyz
250 openmailbox.xyz
EHLO attacker.xyz
250-openmailbox.xyz
250-PIPELINING
250-SIZE 10240000
250-VRFY
250-ETRN
250-STARTTLS
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250 SMTPUTF8
HELP
502 5.5.2 Error: command not recognized
```

How many of the common usernames present in the dictionary /usr/share/commix/src/txt/usernames.txt exist on the server. Use smtp-user-enum tool for this task.

`8`

```
smtp-user-enum -U /usr/share/commix/src/txt/usernames.txt -t 192.229.175.3
```

```
root@attackdefense:~# smtp-user-enum -M RCPT -U /usr/share/commix/src/txt/usernames.txt -t 192.229.175.3
Starting smtp-user-enum v1.2 ( http://pentestmonkey.net/tools/smtp-user-enum )

 ----------------------------------------------------------
|                   Scan Information                       |
 ----------------------------------------------------------

Mode ..................... RCPT
Worker Processes ......... 5
Usernames file ........... /usr/share/commix/src/txt/usernames.txt
Target count ............. 1
Username count ........... 123
Target TCP port .......... 25
Query timeout ............ 5 secs
Target domain ............ 

######## Scan started at Wed Dec 14 14:50:05 2022 #########
 exists.175.3: administrator
 exists.175.3: admin
 exists.175.3: mail
 exists.175.3: postmaster
 exists.175.3: root
 exists.175.3: sales
 exists.175.3: support
 exists.175.3: www-data
######## Scan completed at Wed Dec 14 14:50:05 2022 #########
8 results.

123 queries in 1 seconds (123.0 queries / sec)
```

How many common usernames present in the dictionary /usr/share/metasploit-framework/data/wordlists/unix_users.txt exist on the server. Use suitable metasploit module for this task.

`20`

```
msf5 > use auxiliary/scanner/smtp/smtp_enum
msf5 auxiliary(scanner/smtp/smtp_enum) > show options

Module options (auxiliary/scanner/smtp/smtp_enum):

   Name       Current Setting                                                Required  Description
   ----       ---------------                                                --------  -----------
   RHOSTS                                                                    yes       The target address range or CIDR identifier
   RPORT      25                                                             yes       The target port (TCP)
   THREADS    1                                                              yes       The number of concurrent threads
   UNIXONLY   true                                                           yes       Skip Microsoft bannered servers when testing unix users
   USER_FILE  /usr/share/metasploit-framework/data/wordlists/unix_users.txt  yes       The file that contains a list of probable users accounts.

msf5 auxiliary(scanner/smtp/smtp_enum) > set RHOSTS 192.229.175.3
RHOSTS => 192.229.175.3
msf5 auxiliary(scanner/smtp/smtp_enum) > run

[*] 192.229.175.3:25      - 192.229.175.3:25 Banner: 220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
[+] 192.229.175.3:25      - 192.229.175.3:25 Users found: , admin, administrator, backup, bin, daemon, games, gnats, irc, list, lp, mail, man, news, nobody, postmaster, proxy, sync, sys, uucp, www-data
[*] 192.229.175.3:25      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Connect to SMTP service using telnet and send a fake mail to root user.

```
root@attackdefense:~# telnet 192.229.175.3 25
Trying 192.229.175.3...
Connected to 192.229.175.3.
Escape character is '^]'.
220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
HELO attacker.xyz
250 openmailbox.xyz
MAIL FROM:admin@attacker.xyz
250 2.1.0 Ok
RCPT TO:root@openmailbox.xyz
250 2.1.5 Ok
DATA
354 End data with <CR><LF>.<CR><LF>
From: "admin" <admin@attacker.xyz>
To: "root" <root@openmailbox.xyz>
Subject: A fake mail

"This is a fake mail from admin to root user."
.
250 2.0.0 Ok: queued as 34F02C95909
```

```
root@attackdefense:~# telnet 192.229.175.3 25
Trying 192.229.175.3...
Connected to 192.229.175.3.
Escape character is '^]'.
220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
HELO attacker.xyz
250 openmailbox.xyz
MAIL FROM:admin@attacker.xyz
250 2.1.0 Ok
RCPT TO:root@openmailbox.xyz
250 2.1.5 Ok
DATA
354 End data with <CR><LF>.<CR><LF>
Hello,

This is a fake mail sent using telnet command.

From,
Admin

.
250 2.0.0 Ok: queued as 29BD7C95903
```

Send a fake mail to root user using sendemail command.

```
root@attackdefense:~# sendEmail -f admin@attacker.xyz -t root@openmailbox.xyz -u "A fake mail" -m "This is a fake mail." -s 192.229.175.3 -o tls=no
Dec 14 15:19:53 attackdefense sendEmail[242]: Email was sent successfully!
```

```
root@attackdefense:~# sendemail -f admin@attacker.xyz -t root@openmailbox.xyz -s 192.229.175.3 -u Fakemail -m "Hi root, a fake from admin" -o tls=no
Dec 14 15:22:17 attackdefense sendemail[248]: Email was sent successfully!
```

### References（其实没什么用）
[Postfix](http://www.postfix.org/)

[Smtp-User-Enum](https://www.kali.org/tools/smtp-user-enum/)

[sendmail](http://www.postfix.org/sendmail.1.html)

[Metasploit Module: SMTP User Enumeration Utility](https://www.rapid7.com/db/modules/auxiliary/scanner/smtp/smtp_enum/)

