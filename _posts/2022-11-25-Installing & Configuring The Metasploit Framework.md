---
layout: post
title: "Installing & Configuring The Metasploit Framework"
date: "2022-11-25"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Installing & Configuring The Metasploit Framework
## Installing The Metasploit Framework
The MSF is distributed by Rapid7 and can be downloaded and installed as a standalone package on both Windows & Linux.

In this course we will be utilizing the Metasploit Framework on Linux and our preferred distribution of choice is Kali Linux.

MSF and its required dependencies come pre-packaged with Kali Linux which saves us from the tedious process of installing MSF manually.

# 安装和配置 Metasploit 框架
## 安装 Metasploit 框架
MSF 由 Rapid7 分发，可以作为独立包下载并安装在 Windows 和 Linux 上。

在本课程中，我们将在 Linux 上使用 Metasploit 框架，我们首选的发行版是 Kali Linux。

MSF 及其所需的依赖项已预先打包在 Kali Linux 中，这使我们免于手动安装 MSF 的繁琐过程。

## The Metasploit Framework Database
The Metasploit Framework Database (msfdb) is an integral part of the Metasploit Framework and is used to keep track of all your assessments, host data scans etc.

The Metasploit Framework uses PostgreSQL as the primary database server, as a result, we will also need to ensure that the PostgreSQL database service is running and configured correctly.

The Metasploit Framework Database also facilitates the importation and storage of scan results from various third party tools like Nmap and Nessus.

## Metasploit 框架数据库
Metasploit 框架数据库 (msfdb) 是 Metasploit 框架不可或缺的一部分，用于跟踪您的所有评估、主机数据扫描等。

Metasploit 框架使用 PostgreSQL 作为主要数据库服务器，因此，我们还需要确保 PostgreSQL 数据库服务正在运行并正确配置。

Metasploit 框架数据库还有助于从各种第三方工具（如 Nmap 和 Nessus）导入和存储扫描结果。

## Installation Steps
Update our repositories and upgrade our Metasploit Framework to the latest version.

Start and enable the PostgreSQL database service.

Initialize the Metasploit Framework Database (msfdb).

Launch MSFconsole!

## 安装步骤
更新我们的存储库并将我们的 Metasploit 框架升级到最新版本。

启动并启用 PostgreSQL 数据库服务。

初始化 Metasploit 框架数据库 (msfdb)。

启动 MSF 控制台！

# Demo: Installing & Configuring The Metasploit Framework（演示：安装和配置 Metasploit 框架）
Make sure you have your repositories updated and you have the MSF upgraded to the latest version.

```
sudo apt-get update && sudo apt-get install metasploit-framework -y
```

Enable and start the postgreSQL service.

Given that this is a Debian distribution, the tool or utility that we will be using to interact with the services, and essentially setting them up, is the system control or `systemctl`.

We want to enable the postgreSQL service to start on system startup.

```
sudo systemctl enable postgresql
sudo systemctl start postgresql
sudo systemctl status postgresql
```

```
sudo msfdb
```

Initialize the database.

```
sudo msfdb init
```

If you ever encounter any issues with your MSF database, you can always reinitialize the database. This will delete and reinitialize the database, so you will lose all your data.

```
sudo msfdb reinit
```

Check the status of the database.

```
sudo msfdb status
```

```
msfconsole
```

We can confirm that the database is connected to the MSF console

```
msf6 > db_
msf6 > db_status
```

# Home Lab

```
┌──(root㉿kali)-[~]
└─# sudo apt-get update && sudo apt-get install metasploit-framework -y
Hit:1 http://mirrors.aliyun.com/kali kali-rolling InRelease
Reading package lists... Done
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
metasploit-framework is already the newest version (6.2.26-0kali1).
The following packages were automatically installed and are no longer required:
  libarmadillo10 libcharls2 libdav1d5 libdrm-intel1 libgdal30 libgeos3.10.2 liblttng-ust-ctl4 liblttng-ust0 python3-iptools
Use 'sudo apt autoremove' to remove them.
0 upgraded, 0 newly installed, 0 to remove and 1413 not upgraded.

```

```
┌──(root㉿kali)-[~]
└─# sudo systemctl enable postgresql                                   
Synchronizing state of postgresql.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable postgresql
Created symlink /etc/systemd/system/multi-user.target.wants/postgresql.service → /lib/systemd/system/postgresql.service.
                                                                                                                                                                                             
┌──(root㉿kali)-[~]
└─# sudo systemctl start postgresql 
                                                                                                                                                                                             
┌──(root㉿kali)-[~]
└─# sudo systemctl status postgresql
● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: disabled)
     Active: active (exited) since Thu 2022-11-24 23:06:16 EST; 13s ago
    Process: 5108 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 5108 (code=exited, status=0/SUCCESS)
        CPU: 1ms

Nov 24 23:06:16 kali systemd[1]: Starting PostgreSQL RDBMS...
Nov 24 23:06:16 kali systemd[1]: Finished PostgreSQL RDBMS.
```

```
┌──(root㉿kali)-[~]
└─# sudo msfdb                                                         

Manage the metasploit framework database

You can use an specific port number for the
PostgreSQL connection setting the PGPORT variable
in the current shell.

Example: PGPORT=5433 msfdb init

  msfdb init     # start and initialize the database
  msfdb reinit   # delete and reinitialize the database
  msfdb delete   # delete database and stop using it
  msfdb start    # start the database
  msfdb stop     # stop the database
  msfdb status   # check service status
  msfdb run      # start the database and run msfconsole
```

```
┌──(root㉿kali)-[~]
└─# sudo msfdb init
[i] Database already started
[+] Creating database user 'msf'
[+] Creating databases 'msf'
[+] Creating databases 'msf_test'
[+] Creating configuration file '/usr/share/metasploit-framework/config/database.yml'
[+] Creating initial database schema

```

```
┌──(root㉿kali)-[~]
└─# sudo msfdb reinit
[i] Database already started
[+] Dropping databases 'msf'
[+] Dropping databases 'msf_test'
[+] Dropping database user 'msf'
[+] Deleting configuration file /usr/share/metasploit-framework/config/database.yml
[+] Stopping database
[+] Starting database
[+] Creating database user 'msf'
[+] Creating databases 'msf'
[+] Creating databases 'msf_test'
[+] Creating configuration file '/usr/share/metasploit-framework/config/database.yml'
[+] Creating initial database schema

```

```
┌──(root㉿kali)-[~]
└─# sudo msfdb status
● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: disabled)
     Active: active (exited) since Thu 2022-11-24 23:16:14 EST; 1min 28s ago
    Process: 9171 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 9171 (code=exited, status=0/SUCCESS)
        CPU: 1ms

Nov 24 23:16:14 kali systemd[1]: Starting PostgreSQL RDBMS...
Nov 24 23:16:14 kali systemd[1]: Finished PostgreSQL RDBMS.

COMMAND   PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
postgres 9141 postgres    5u  IPv6  34264      0t0  TCP localhost:5432 (LISTEN)
postgres 9141 postgres    6u  IPv4  34265      0t0  TCP localhost:5432 (LISTEN)

UID          PID    PPID  C STIME TTY      STAT   TIME CMD
postgres    9141       1  0 23:16 ?        Ss     0:00 /usr/lib/postgresql/14/bin/postgres -D /var/lib/postgresql/14/main -c config_file=/etc/postgresql/14/main/postgresql.conf

[+] Detected configuration file (/usr/share/metasploit-framework/config/database.yml)

```

```
┌──(root㉿kali)-[~]
└─# msfconsole -q           
msf6 > db_
db_connect        db_disconnect     db_export         db_import         db_nmap           db_rebuild_cache  db_remove         db_save           db_status         
msf6 > db_status
[*] Connected to msf. Connection type: postgresql.
```

