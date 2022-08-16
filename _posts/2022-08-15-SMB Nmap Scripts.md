---
layout: post
title: "SMB Nmap Scripts"
date: "2022-08-15"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Assessment Methodologies
    - Enumeration
---

# SMB Nmap Scripts
```
Target IP Address: 10.4.31.90
ping 10.4.31.90
```

```
nmap 10.4.31.90
```

```
Nmap scan report for 10.4.31.90
Host is up.
PORT        STATE   SERVICE
135/tcp     open    msrpc
139/tcp     open    netbios-ssn
445/tcp     open    microsoft-ds
3389/tcp    open    ms-wbt-server
```

[Script smb-protocols](https://nmap.org/nsedoc/scripts/smb-protocols.html)

```
nmap -p445 --script smb-protocols 10.4.31.90
```

[smb-security-mode](https://nmap.org/nsedoc/scripts/smb-security-mode.html)

```
nmap -p445 --script smb-security-mode 10.4.31.90
```

How is the guest account useful?

[smb-enum-sessions](https://nmap.org/nsedoc/scripts/smb-enum-sessions.html)

```
nmap -p445 --script smb-enum-sessions 10.4.31.90
```

```
nmap -p445 --script smb-enum-sessions --script-args smbusername=administrator,smbpassword=smbserver_771 10.4.31.90
```

```
ip a
```

[smb-enum-shares](https://nmap.org/nsedoc/scripts/smb-enum-shares.html)

```
nmap -p445 --script smb-enum-shares 10.4.31.90
```

```
nmap -p445 --script smb-enum-shares --script-args smbusername=administrator,smbpassword=smbserver_771 10.4.31.90
```

[smb-enum-users](https://nmap.org/nsedoc/scripts/smb-enum-users.html)

```
nmap -p445 --script smb-enum-users --script-args smbusername=administrator,smbpassword=smbserver_771 10.4.31.90
```

[smb-server-stats](https://nmap.org/nsedoc/scripts/smb-server-stats.html)

```
nmap -p445 --script smb-server-stats --script-args smbusername=administrator,smbpassword=smbserver_771 10.4.31.90
```

[smb-enum-domains](https://nmap.org/nsedoc/scripts/smb-enum-domains.html)

```
nmap -p445 --script smb-enum-domains --script-args smbusername=administrator,smbpassword=smbserver_771 10.4.31.90
```

[smb-enum-groups](https://nmap.org/nsedoc/scripts/smb-enum-groups.html) 

```
nmap -p445 --script smb-enum-groups --script-args smbusername=administrator,smbpassword=smbserver_771 10.4.31.90
```

[smb-enum-services](https://nmap.org/nsedoc/scripts/smb-enum-services.html)

```
nmap -p445 --script smb-enum-services --script-args smbusername=administrator,smbpassword=smbserver_771 10.4.31.90
```

[smb-enum-shares](https://nmap.org/nsedoc/scripts/smb-enum-shares.html)

[smb-ls](https://nmap.org/nsedoc/scripts/smb-ls.html)

```
nmap -p445 --script smb-enum-shares,smb-ls --script-args smbusername=administrator,smbpassword=smbserver_771 10.4.31.90
```

# Windows 侦察：SMB Nmap 脚本
## 概述
为您提供了一台 Kali GUI 机器和一台运行 SMB 服务的目标机器。目标机器的 IP 地址在 Kali 机器桌面 (/root/Desktop/target) 上名为 target 的文本文件中提供。 

你的任务是使用 Kali 机器上可用的工具对服务进行指纹识别，并运行 Nmap 脚本来枚举 Windows 目标机器 SMB 服务。

**目标：**

* 识别 SMB 协议方言
* 查找 SMB 安全级别信息
* 枚举活动会话、共享、Windows 用户、域、服务等。
* 以下用户名和密码可用于访问服务：

`| 用户名 | 密码 | | administrator | smbserver_771 |`

**指示：**

* 你的 Kali 机器有一个 IP 地址为 10.10.XY 的接口运行“ip addr”以了解 X 和 Y 的值。
* 目标机器的 IP 地址在文件“/root/Desktop/target”中提到
* 不要攻击位于 IP 地址 192.V.W.1 和 10.10.X.1 的网关

## 我自己的思路
```
Target IP Address : 10.0.19.14
```

```
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
25143: eth0@if25144: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:06 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.6/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
25145: eth1@if25146: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:0a:10:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.10.16.3/24 brd 10.10.16.255 scope global eth1
       valid_lft forever preferred_lft forever

```

```
root@attackdefense:~# ping 10.0.19.14 -c 4
PING 10.0.19.14 (10.0.19.14) 56(84) bytes of data.
64 bytes from 10.0.19.14: icmp_seq=1 ttl=125 time=4.05 ms
64 bytes from 10.0.19.14: icmp_seq=2 ttl=125 time=3.07 ms
64 bytes from 10.0.19.14: icmp_seq=3 ttl=125 time=2.98 ms
64 bytes from 10.0.19.14: icmp_seq=4 ttl=125 time=3.01 ms

--- 10.0.19.14 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3003ms
rtt min/avg/max/mdev = 2.980/3.276/4.046/0.445 ms
```

```
root@attackdefense:~# nmap 10.0.19.14
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-16 13:45 IST
Nmap scan report for 10.0.19.14
Host is up (0.0030s latency).
Not shown: 992 closed ports
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 2.59 seconds
```

```
root@attackdefense:~# nmap -p445 --script smb-protocols 10.0.19.14
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-16 13:48 IST
Nmap scan report for 10.0.19.14
Host is up (0.0033s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-protocols: 
|   dialects: 
|     NT LM 0.12 (SMBv1) [dangerous, but default]
|     2.02
|     2.10
|     3.00
|_    3.02

Nmap done: 1 IP address (1 host up) scanned in 6.49 seconds
```

```
root@attackdefense:~# nmap -p445 --script smb-security-mode 10.0.19.14
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-16 13:50 IST
Nmap scan report for 10.0.19.14
Host is up (0.0035s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

Nmap done: 1 IP address (1 host up) scanned in 1.48 seconds
```

```
root@attackdefense:~# nmap -p445 --script smb-enum-sessions 10.0.19.14
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-16 13:54 IST
Nmap scan report for 10.0.19.14
Host is up (0.0033s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-sessions: 
|   Users logged in
|_    WIN-OMCNBKR66MN\bob since <unknown>

Nmap done: 1 IP address (1 host up) scanned in 3.90 seconds
```

```
root@attackdefense:~# nmap -p445 --script smb-enum-sessions --script-args smbusername=administrator,smbpassword=smbserver_771 10.0.19.14
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-16 13:57 IST
Nmap scan report for 10.0.19.14
Host is up (0.0034s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-sessions: 
|   Users logged in
|     WIN-OMCNBKR66MN\bob since 2022-08-16T08:13:06
|   Active SMB sessions
|_    ADMINISTRATOR is connected from \\10.10.16.3 for [just logged in, it's probably you], idle for [not idle]

Nmap done: 1 IP address (1 host up) scanned in 3.69 seconds
```

```
root@attackdefense:~# nmap -p445 --script smb-enum-shares 10.0.19.14
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-16 14:01 IST
Nmap scan report for 10.0.19.14
Host is up (0.0031s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-shares: 
|   account_used: guest
|   \\10.0.19.14\ADMIN$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Remote Admin
|     Anonymous access: <none>
|     Current user access: <none>
|   \\10.0.19.14\C: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.19.14\C$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Anonymous access: <none>
|     Current user access: <none>
|   \\10.0.19.14\D$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Anonymous access: <none>
|     Current user access: <none>
|   \\10.0.19.14\Documents: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.19.14\Downloads: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.19.14\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: Remote IPC
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.19.14\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Anonymous access: <none>
|_    Current user access: READ

Nmap done: 1 IP address (1 host up) scanned in 45.50 seconds

```

```
root@attackdefense:~# nmap -p445 --script smb-enum-shares --script-args smbusername=administrator,smbpassword=smbserver_771 10.0.19.14
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-16 14:07 IST
Nmap scan report for 10.0.19.14
Host is up (0.0030s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-shares: 
|   account_used: administrator
|   \\10.0.19.14\ADMIN$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Remote Admin
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Windows
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.19.14\C: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.19.14\C$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.19.14\D$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Users: 0
|     Max Users: <unlimited>
|     Path: D:\
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.19.14\Documents: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Users\Administrator\Documents
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.19.14\Downloads: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Users\Administrator\Downloads
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.19.14\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: Remote IPC
|     Users: 1
|     Max Users: <unlimited>
|     Path: 
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.19.14\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Windows\system32\spool\drivers
|     Anonymous access: <none>
|_    Current user access: READ/WRITE

Nmap done: 1 IP address (1 host up) scanned in 48.58 seconds
```

```
root@attackdefense:~# nmap -p445 --script smb-enum-users --script-args smbusername=administrator,smbpassword=smbserver_771 10.0.19.14
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-16 14:14 IST
Nmap scan report for 10.0.19.14
Host is up (0.0033s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-users: 
|   WIN-OMCNBKR66MN\Administrator (RID: 500)
|     Description: Built-in account for administering the computer/domain
|     Flags:       Password does not expire, Normal user account
|   WIN-OMCNBKR66MN\bob (RID: 1010)
|     Flags:       Password does not expire, Normal user account
|   WIN-OMCNBKR66MN\Guest (RID: 501)
|     Description: Built-in account for guest access to the computer/domain
|_    Flags:       Password does not expire, Password not required, Normal user account

Nmap done: 1 IP address (1 host up) scanned in 4.54 seconds

```

```
root@attackdefense:~# nmap -p445 --script smb-server-stats --script-args smbusername=administrator,smbpassword=smbserver_711 10.0.19.14
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-16 14:20 IST
Nmap scan report for 10.0.19.14
Host is up (0.0031s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Nmap done: 1 IP address (1 host up) scanned in 1.49 seconds

```

```
root@attackdefense:~# nmap -p445 --script smb-enum-domains --script-args smbusername=administrator,smbpassword=smbserver_771 10.0.19.14
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-16 14:22 IST
Nmap scan report for 10.0.19.14
Host is up (0.0034s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-domains: 
|   WIN-OMCNBKR66MN
|     Groups: WinRMRemoteWMIUsers__
|     Users: Administrator, bob, Guest
|     Creation time: 2013-08-22T14:47:57
|     Passwords: min length: n/a; min age: n/a days; max age: 42 days; history: n/a passwords
|     Properties: Complexity requirements exist
|     Account lockout disabled
|   Builtin
|     Groups: Access Control Assistance Operators, Administrators, Backup Operators, Certificate Service DCOM Access, Cryptographic Operators, Distributed COM Users, Event Log Readers, Guests, Hyper-V Administrators, IIS_IUSRS, Network Configuration Operators, Performance Log Users, Performance Monitor Users, Power Users, Print Operators, RDS Endpoint Servers, RDS Management Servers, RDS Remote Access Servers, Remote Desktop Users, Remote Management Users, Replicator, Users
|     Users: n/a
|     Creation time: 2013-08-22T14:47:57
|     Passwords: min length: n/a; min age: n/a days; max age: 42 days; history: n/a passwords
|_    Account lockout disabled

Nmap done: 1 IP address (1 host up) scanned in 3.67 seconds
```

```
root@attackdefense:~# nmap -p445 --script smb-enum-groups --script-args smbusername=administrator,smbpassword=smbserver_771 10.0.19.14
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-16 14:29 IST
Nmap scan report for 10.0.19.14
Host is up (0.0032s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-groups: 
|   Builtin\Administrators (RID: 544): Administrator, bob
|   Builtin\Users (RID: 545): bob
|   Builtin\Guests (RID: 546): Guest
|   Builtin\Power Users (RID: 547): <empty>
|   Builtin\Print Operators (RID: 550): <empty>
|   Builtin\Backup Operators (RID: 551): <empty>
|   Builtin\Replicator (RID: 552): <empty>
|   Builtin\Remote Desktop Users (RID: 555): bob
|   Builtin\Network Configuration Operators (RID: 556): <empty>
|   Builtin\Performance Monitor Users (RID: 558): <empty>
|   Builtin\Performance Log Users (RID: 559): <empty>
|   Builtin\Distributed COM Users (RID: 562): <empty>
|   Builtin\IIS_IUSRS (RID: 568): <empty>
|   Builtin\Cryptographic Operators (RID: 569): <empty>
|   Builtin\Event Log Readers (RID: 573): <empty>
|   Builtin\Certificate Service DCOM Access (RID: 574): <empty>
|   Builtin\RDS Remote Access Servers (RID: 575): <empty>
|   Builtin\RDS Endpoint Servers (RID: 576): <empty>
|   Builtin\RDS Management Servers (RID: 577): <empty>
|   Builtin\Hyper-V Administrators (RID: 578): <empty>
|   Builtin\Access Control Assistance Operators (RID: 579): <empty>
|   Builtin\Remote Management Users (RID: 580): <empty>
|_  WIN-OMCNBKR66MN\WinRMRemoteWMIUsers__ (RID: 1000): <empty>

Nmap done: 1 IP address (1 host up) scanned in 3.04 seconds
```

```
root@attackdefense:~# nmap -p445 --script smb-enum-services --script-args smbusername=administrator,smbpassword=smbserver_771 10.0.19.14
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-16 14:35 IST
Nmap scan report for 10.0.19.14
Host is up (0.0032s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
| smb-enum-services: 
|   AmazonSSMAgent: 
|     display_name: Amazon SSM Agent
|     state: 
|       SERVICE_CONTINUE_PENDING
|       SERVICE_PAUSE_PENDING
|       SERVICE_RUNNING
|       SERVICE_PAUSED
|     type: 
|       SERVICE_TYPE_WIN32
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|     controls_accepted: 
|       SERVICE_CONTROL_NETBINDENABLE
|       SERVICE_CONTROL_INTERROGATE
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_STOP
|   AWSLiteAgent: 
|     display_name: AWS Lite Guest Agent
|     state: 
|       SERVICE_CONTINUE_PENDING
|       SERVICE_PAUSE_PENDING
|       SERVICE_RUNNING
|       SERVICE_PAUSED
|     type: 
|       SERVICE_TYPE_WIN32
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|     controls_accepted: 
|       SERVICE_CONTROL_NETBINDENABLE
|       SERVICE_CONTROL_INTERROGATE
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_STOP
|   DiagTrack: 
|     display_name: Diagnostics Tracking Service
|     state: 
|       SERVICE_CONTINUE_PENDING
|       SERVICE_PAUSE_PENDING
|       SERVICE_RUNNING
|       SERVICE_PAUSED
|     type: 
|       SERVICE_TYPE_WIN32
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|     controls_accepted: 
|       SERVICE_CONTROL_NETBINDENABLE
|       SERVICE_CONTROL_INTERROGATE
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_STOP
|   Ec2Config: 
|     display_name: Ec2Config
|     state: 
|       SERVICE_CONTINUE_PENDING
|       SERVICE_PAUSE_PENDING
|       SERVICE_RUNNING
|       SERVICE_PAUSED
|     type: 
|       SERVICE_TYPE_WIN32
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|     controls_accepted: 
|       SERVICE_CONTROL_NETBINDENABLE
|       SERVICE_CONTROL_INTERROGATE
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_STOP
|   MSDTC: 
|     display_name: Distributed Transaction Coordinator
|     state: 
|       SERVICE_CONTINUE_PENDING
|       SERVICE_PAUSE_PENDING
|       SERVICE_RUNNING
|       SERVICE_PAUSED
|     type: 
|       SERVICE_TYPE_WIN32
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|     controls_accepted: 
|       SERVICE_CONTROL_NETBINDENABLE
|       SERVICE_CONTROL_INTERROGATE
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_STOP
|   Spooler: 
|     display_name: Print Spooler
|     state: 
|       SERVICE_CONTINUE_PENDING
|       SERVICE_PAUSE_PENDING
|       SERVICE_RUNNING
|       SERVICE_PAUSED
|     type: 
|       SERVICE_TYPE_WIN32
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|     controls_accepted: 
|       SERVICE_CONTROL_NETBINDENABLE
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|_      SERVICE_CONTROL_STOP

Nmap done: 1 IP address (1 host up) scanned in 1.50 seconds

```

```
root@attackdefense:~# nmap -p445 --script smb-enum-shares,smb-ls --script-args smbusername=administrator,smbpassword=smbserver_771 10.0.25.230
Starting Nmap 7.91 ( https://nmap.org ) at 2022-08-16 15:04 IST
Nmap scan report for 10.0.25.230
Host is up (0.0043s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-shares: 
|   account_used: administrator
|   \\10.0.25.230\ADMIN$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Remote Admin
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Windows
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.25.230\C: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.25.230\C$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.25.230\D$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Users: 0
|     Max Users: <unlimited>
|     Path: D:\
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.25.230\Documents: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Users\Administrator\Documents
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.25.230\Downloads: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Users\Administrator\Downloads
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.0.25.230\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: Remote IPC
|     Users: 1
|     Max Users: <unlimited>
|     Path: 
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.0.25.230\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Windows\system32\spool\drivers
|     Anonymous access: <none>
|_    Current user access: READ/WRITE
| smb-ls: Volume \\10.0.25.230\ADMIN$
|   maxfiles limit reached (10)
| SIZE   TIME                 FILENAME
| <DIR>  2013-08-22T13:36:16  .
| <DIR>  2013-08-22T13:36:16  ..
| <DIR>  2013-08-22T15:39:31  ADFS
| <DIR>  2013-08-22T15:39:31  ADFS\ar
| <DIR>  2013-08-22T15:39:31  ADFS\bg
| <DIR>  2013-08-22T15:39:31  ADFS\cs
| <DIR>  2013-08-22T15:39:31  ADFS\da
| <DIR>  2013-08-22T15:39:31  ADFS\de
| <DIR>  2013-08-22T15:39:31  ADFS\el
| <DIR>  2013-08-22T15:39:31  ADFS\en
| 
| 
| Volume \\10.0.25.230\C
|   maxfiles limit reached (10)
| SIZE   TIME                 FILENAME
| <DIR>  2013-08-22T15:39:30  PerfLogs
| <DIR>  2013-08-22T13:36:16  Program Files
| <DIR>  2014-05-17T10:36:57  Program Files\Amazon
| <DIR>  2013-08-22T13:36:16  Program Files\Common Files
| <DIR>  2014-10-15T05:58:49  Program Files\DIFX
| <DIR>  2013-08-22T15:39:31  Program Files\Internet Explorer
| <DIR>  2014-07-10T18:40:15  Program Files\Update Services
| <DIR>  2020-08-12T04:13:47  Program Files\Windows Mail
| <DIR>  2013-08-22T15:39:31  Program Files\Windows NT
| <DIR>  2013-08-22T15:39:31  Program Files\WindowsPowerShell
| 
| 
| Volume \\10.0.25.230\C$
|   maxfiles limit reached (10)
| SIZE   TIME                 FILENAME
| <DIR>  2013-08-22T15:39:30  PerfLogs
| <DIR>  2013-08-22T13:36:16  Program Files
| <DIR>  2014-05-17T10:36:57  Program Files\Amazon
| <DIR>  2013-08-22T13:36:16  Program Files\Common Files
| <DIR>  2014-10-15T05:58:49  Program Files\DIFX
| <DIR>  2013-08-22T15:39:31  Program Files\Internet Explorer
| <DIR>  2014-07-10T18:40:15  Program Files\Update Services
| <DIR>  2020-08-12T04:13:47  Program Files\Windows Mail
| <DIR>  2013-08-22T15:39:31  Program Files\Windows NT
| <DIR>  2013-08-22T15:39:31  Program Files\WindowsPowerShell
| 
| 
| Volume \\10.0.25.230\Documents
| SIZE   TIME                 FILENAME
| <DIR>  2020-09-10T09:50:27  .
| <DIR>  2020-09-10T09:50:27  ..
| 
| 
| Volume \\10.0.25.230\Downloads
| SIZE   TIME                 FILENAME
| <DIR>  2020-09-10T09:50:27  .
| <DIR>  2020-09-10T09:50:27  ..
| 
| 
| Volume \\10.0.25.230\print$
|   maxfiles limit reached (10)
| SIZE    TIME                 FILENAME
| <DIR>   2013-08-22T15:39:31  .
| <DIR>   2013-08-22T15:39:31  ..
| <DIR>   2013-08-22T15:39:31  color
| 1058    2013-08-22T06:54:44  color\D50.camp
| 1079    2013-08-22T06:54:44  color\D65.camp
| 797     2013-08-22T06:54:44  color\Graphics.gmmp
| 838     2013-08-22T06:54:44  color\MediaSim.gmmp
| 786     2013-08-22T06:54:44  color\Photo.gmmp
| 822     2013-08-22T06:54:44  color\Proofing.gmmp
| 218103  2013-08-22T06:54:44  color\RSWOP.icm
|_

Nmap done: 1 IP address (1 host up) scanned in 56.16 seconds
```

## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-2222.pdf

