---
layout: post
title: "Null Session"
date: "2022-07-19"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# Null Session
In this lesson, we will learn how to get accessing information from systems where shares are not seucre against null session. Null session allows anonymous connection to the network shares around Windows-based systems. This means that an attacker may be get read and write access to the victim machine, without knowing any username or password on the system itself. Note that this connection cannot be established to any typical share, but only against inter-processes communication, also known as IPC, which allow Windows proccesses to communicate with other processes on the network.

Let's use a SYN scan with nmap on specific ports used for Windows printer and file sharing.

```
nmap -sS -p 135,139,445 192.168.102.0-255
```

![nmap-syn-135139445.png](/img/in-post/ine/nmap-syn-135139445.png)

## enum4linux
It allows to enumerate shares with null sessions.

```
└─# enum4linux
enum4linux v0.9.1 (http://labs.portcullis.co.uk/application/enum4linux/)
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
              This option is enabled if you don't provide any other options.
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
    -A        Aggressive. Do write checks on shares etc

RID cycling should extract a list of users from Windows (or Samba) hosts 
which have RestrictAnonymous set to 1 (Windows NT and 2000), or "Network 
access: Allow anonymous SID/Name translation" enabled (XP, 2003).

NB: Samba servers often seem to have RIDs in the range 3000-3050.

Dependancy info: You will need to have the samba package installed as this 
script is basically just a wrapper around rpcclient, net, nmblookup and 
smbclient.  Polenum from http://labs.portcullis.co.uk/application/polenum/ 
is required to get Password Policy info.

```

`-L, -N, -D, -f` are not used in the Linux version.

`-n`: Do an nmblookup (similar to nbtstat)

```
enum4linux -n 192.168.102.151
```

In the result, we saw the `20` flag, which means that File Server Service is open, meaning the user has opened shares.

![20-file-server-service-is-open.png](/img/in-post/ine/20-file-server-service-is-open.png)

In order to test against null sessions, we will move forward and use `-P` option and try to enumerate the password policy on the remote system.

```
enum4linux -P 192.168.102.151
```

![enumerate-the-password-policy.png](/img/in-post/ine/enumerate-the-password-policy.png)

If we want to use the same information on the brute force tool, we know the parameter we can use.

The remote system does not use the password complexity file, or a password complexity policy. And the reset account lockout counter is 30 minutes, and the locked account duration is 30 minutes.

```
[+] Minimum password length: None
[+] Password history length: None
[+] Password Complexity Flags: 000000

[+] Reset Account Lockout Counter: 30 minutes
[+] Locked Account Duration: 30 minutes
```

`-S` to enumerate the shares available on the remote machine.

```
enum4linux -S 192.168.102.151
```

According to the result, the victim machine shares the C driver itself.

![available-shares.png](/img/in-post/ine/available-shares.png)

If for some reason you don't get enough information from Enum, it has the ability to use a brute force file to try to enumerate any share available on the remote machine. These name generally contains some username and password combinations. We use this by using the `-s` command, with the path of the brute force list.

```
enum4linux -s /usr/share/enum4linux/share-list.txt 192.168.102.151
```

![brute-force-share-list.png](/img/in-post/ine/brute-force-share-list.png)

Enum also has the ablility to run all the available commands in a single prompt using the `-a`.

```
enum4linux -a 192.168.102.151
```

![nbtstat-information.png](/img/in-post/ine/nbtstat-information.png)


![users-available-on-the-remote-machine.png](/img/in-post/ine/users-available-on-the-remote-machine.png)


![share-enumeration.png](/img/in-post/ine/share-enumeration.png)

![password-policy.png](/img/in-post/ine/password-policy.png)

![the-groups.png](/img/in-post/ine/the-groups.png)


## samrdump.py
在教程中的路径是：`/usr/share/doc/python-impacket-doc/examples`。

```
cd /usr/share/doc/python-impacket-doc/examples
ls
python samrdump.py
```

在kali linux 2022.2上的路径为：`/usr/share/doc/python3-impacket/examples`。

```
┌──(root㉿kali)-[~]
└─# cd /usr/share/doc/python3-impacket/examples   
                                                                                                                                                                                             
┌──(root㉿kali)-[/usr/share/doc/python3-impacket/examples]
└─# ls                               
addcomputer.py  findDelegation.py   getST.py          kintercept.py    mssqlinstance.py      ping.py           reg.py          services.py   sniffer.py          wmipersist.py
atexec.py       GetADUsers.py       getTGT.py         lookupsid.py     netview.py            psexec.py         rpcdump.py      smbclient.py  sniff.py            wmiquery.py
dcomexec.py     getArch.py          GetUserSPNs.py    machine_role.py  nmapAnswerMachine.py  raiseChild.py     rpcmap.py       smbexec.py    split.py
dpapi.py        Get-GPPPassword.py  goldenPac.py      mimikatz.py      ntfs-read.py          rbcd.py           sambaPipe.py    smbpasswd.py  ticketConverter.py
esentutl.py     GetNPUsers.py       karmaSMB.py       mqtt_check.py    ntlmrelayx.py         rdp_check.py      samrdump.py     smbrelayx.py  ticketer.py
exchanger.py    getPac.py           keylistattack.py  mssqlclient.py   ping6.py              registry-read.py  secretsdump.py  smbserver.py  wmiexec.py
                                                                                                                                                                                             
┌──(root㉿kali)-[/usr/share/doc/python3-impacket/examples]
└─# python3 samrdump.py
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

usage: samrdump.py [-h] [-csv] [-ts] [-debug] [-dc-ip ip address] [-target-ip ip address] [-port [destination port]] [-hashes LMHASH:NTHASH] [-no-pass] [-k] [-aesKey hex key] target

This script downloads the list of users for the target system.

positional arguments:
  target                [[domain/]username[:password]@]<targetName or address>

options:
  -h, --help            show this help message and exit
  -csv                  Turn CSV output
  -ts                   Adds timestamp to every logging output
  -debug                Turn DEBUG output ON

connection:
  -dc-ip ip address     IP Address of the domain controller. If ommited it use the domain part (FQDN) specified in the target parameter
  -target-ip ip address
                        IP Address of the target machine. If ommited it will use whatever was specified as target. This is useful when target is the NetBIOS name and you cannot resolve it
  -port [destination port]
                        Destination port to connect to SMB Server

authentication:
  -hashes LMHASH:NTHASH
                        NTLM hashes, format is LMHASH:NTHASH
  -no-pass              don't ask for password (useful for -k)
  -k                    Use Kerberos authentication. Grabs credentials from ccache file (KRB5CCNAME) based on target parameters. If valid credentials cannot be found, it will use the ones
                        specified in the command line
  -aesKey hex key       AES key to use for Kerberos Authentication (128 or 256 bits)
```

```
Impacket v0.10.0 - 版权所有 2022 SecureAuth Corporation

用法：samrdump.py [-h] [-csv] [-ts] [-debug] [-dc-ip ip address] [-target-ip ip address] [-port [destination port]] [-hashes LMHASH: NTHASH] [-no-pass] [-k] [-aesKey hex key] 目标

此脚本下载目标系统的用户列表。

位置参数：
  目标 [[域/]用户名[:密码]@]<目标名称或地址>

选项：
  -h, --help 显示此帮助信息并退出
  -csv 转CSV输出
  -ts 为每个日志输出添加时间戳
  -debug 打开调试输出

联系：
  -dc-ip ip address 域控制器的 IP 地址。如果省略，则使用目标参数中指定的域部分 (FQDN)
  -target-ip ip地址
                        目标机器的 IP 地址。如果省略，它将使用指定为目标的任何内容。当目标是 NetBIOS 名称并且您无法解析它时，这很有用
  -port [目标端口]
                        连接到 SMB 服务器的目标端口

验证：
  -hashes LMHASH:NTHASH
                        NTLM 哈希，格式为 LMHASH:NTHASH
  -no-pass 不询问密码（对 -k 有用）
  -k 使用 Kerberos 身份验证。根据目标参数从 ccache 文件 (KRB5CCNAME) 中获取凭据。如果找不到有效的凭据，它将使用那些
                        在命令行中指定
  -aesKey 十六进制密钥 用于 Kerberos 身份验证的 AES 密钥（128 或 256 位）
```

Since we do not have username or password, we will simply supply the IP address of the vitime machine. samrdump gives us some information from the SAM account, such as the username and the uid.

```
python3 samrdump.py 192.168.102.151
```

![samrdump.png](/img/in-post/ine/samrdump.png)

We can further exploit these information with brute force tool by knowing the password complexity as well as the user id. So we will have much better chance to exploit the remote system.

## nmap checks on null sessions

```
nmap -script=smb-enum-shares 192.168.102.151
```

We have anonymous accesss to IPC$ share, in other words, the system is vulnerable to null sessions.

![nmap-script-smb-enum-shares.png](/img/in-post/ine/nmap-script-smb-enum-shares.png)

```
nmap -script=smb-enum-users 192.168.102.151
```

![nmap-script-smb-enum-users.png](/img/in-post/ine/nmap-script-smb-enum-users.png)

```
nmap -script=smb-brute 192.168.102.151
```

![nmap-script-smb-brute.png](/img/in-post/ine/nmap-script-smb-brute.png)