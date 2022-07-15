---
layout: post
title: "Bruteforce and Password cracking Offline"
date: "2022-07-15"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# 暴力破解和密码破解离线
在本实验中，您将学习对受保护的 Microsoft Office 文档 (.docx) 文件的 Linux 密码哈希和密码发起字典攻击。

## 实验室环境
在这个实验室环境中，用户可以访问 Kali GUI 实例。这台机器上有John the Ripper (JTR)和Hashcat工具。

目标：执行以下活动：

1. John The Ripper/Hashcat 破解用户“admin”的哈希并恢复密码。 

2. 使用 John The Ripper/Hashcat 查找受保护的 Microsoft Office .docx 文件的密码。

使用密码字典：`/root/Desktop/wordlists/1000000-password-seclists.txt`

## 工具
该实验室的最佳工具是：

* John the Ripper
* Hashcat

## 我自己的思路
首先，查看`/etc/passwd`：

### 破解用户“admin”的哈希并恢复密码
**参考：** [Crack Shadow Hashes After Getting Root on a Linux System](https://null-byte.wonderhowto.com/how-to/crack-shadow-hashes-after-getting-root-linux-system-0186386/)

#### John the Ripper

```
root@INE:~# cat /etc/passwd
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
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:103:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:104:105::/nonexistent:/usr/sbin/nologin
rtkit:x:105:106:RealtimeKit,,,:/proc:/usr/sbin/nologin
xrdp:x:106:112::/run/xrdp:/usr/sbin/nologin
usbmux:x:107:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
avahi:x:108:115:Avahi mDNS daemon,,,:/run/avahi-daemon:/usr/sbin/nologin
pulse:x:109:116:PulseAudio daemon,,,:/run/pulse:/usr/sbin/nologin
sddm:x:110:118:Simple Desktop Display Manager:/var/lib/sddm:/bin/false
geoclue:x:111:119::/var/lib/geoclue:/usr/sbin/nologin
tomcat:x:1000:1000::/opt/tomcat:/bin/false
mysql:x:112:120:MySQL Server,,,:/nonexistent:/bin/false
stunnel4:x:113:122::/var/run/stunnel4:/usr/sbin/nologin
_rpc:x:114:65534::/run/rpcbind:/usr/sbin/nologin
dnsmasq:x:115:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
sslh:x:116:124::/nonexistent:/usr/sbin/nologin
ntp:x:117:125::/nonexistent:/usr/sbin/nologin
arpwatch:x:118:127:ARP Watcher,,,:/var/lib/arpwatch:/bin/sh
Debian-exim:x:119:128::/var/spool/exim4:/usr/sbin/nologin
debian-tor:x:120:129::/var/lib/tor:/bin/false
redsocks:x:121:130::/var/run/redsocks:/usr/sbin/nologin
rwhod:x:122:65534::/var/spool/rwho:/usr/sbin/nologin
freerad:x:123:131::/etc/freeradius:/usr/sbin/nologin
iodine:x:124:65534::/run/iodine:/usr/sbin/nologin
tcpdump:x:125:132::/nonexistent:/usr/sbin/nologin
miredo:x:126:65534::/var/run/miredo:/usr/sbin/nologin
statd:x:127:65534::/var/lib/nfs:/usr/sbin/nologin
redis:x:128:134::/var/lib/redis:/usr/sbin/nologin
postgres:x:129:135:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
inetsim:x:130:138::/var/lib/inetsim:/usr/sbin/nologin
sshd:x:131:65534::/run/sshd:/usr/sbin/nologin
Debian-snmp:x:132:139::/var/lib/snmp:/bin/false
_gvm:x:133:141::/var/lib/openvas:/usr/sbin/nologin
saned:x:134:142::/var/lib/saned:/usr/sbin/nologin
king-phisher:x:135:143::/var/lib/king-phisher:/usr/sbin/nologin
_caldera:x:136:144::/var/lib/caldera:/usr/sbin/nologin
dradis:x:137:145::/var/lib/dradis:/usr/sbin/nologin
beef-xss:x:138:146::/var/lib/beef-xss:/usr/sbin/nologin
uuidd:x:139:147::/run/uuidd:/usr/sbin/nologin
memcache:x:140:148:Memcached,,,:/nonexistent:/bin/false
arangodb:x:999:999:ArangoDB Application User:/usr/share/arangodb3:/bin/false
admin:x:1001:1001:,,,:/home/admin:/bin/bash
```

值得关注的用户有root和admin：

```
root:x:0:0:root:/root:/bin/bash
admin:x:1001:1001:,,,:/home/admin:/bin/bash
```

然后，查看`/etc/shadow`：

```
root@INE:~# cat /etc/shadow
root:$6$iF3IRYx/LG3fxScL$spZt1Ltgnu4zyhnD2nYDZ9FSSKKcV0fRwnA3JNIGveciJQTWkPgv3qhE.URwgSakGvrJFNdHrfwYc9JLEZiGF/:18604:0:99999:7:::
daemon:*:19002:0:99999:7:::
bin:*:19002:0:99999:7:::
sys:*:19002:0:99999:7:::
sync:*:19002:0:99999:7:::
games:*:19002:0:99999:7:::
man:*:19002:0:99999:7:::
lp:*:19002:0:99999:7:::
mail:*:19002:0:99999:7:::
news:*:19002:0:99999:7:::
uucp:*:19002:0:99999:7:::
proxy:*:19002:0:99999:7:::
www-data:*:19002:0:99999:7:::
backup:*:19002:0:99999:7:::
list:*:19002:0:99999:7:::
irc:*:19002:0:99999:7:::
gnats:*:19002:0:99999:7:::
nobody:*:19002:0:99999:7:::
_apt:*:19002:0:99999:7:::
systemd-network:*:19004:0:99999:7:::
systemd-resolve:*:19004:0:99999:7:::
systemd-timesync:*:19004:0:99999:7:::
messagebus:*:19004:0:99999:7:::
rtkit:*:19004:0:99999:7:::
xrdp:!:19004:0:99999:7:::
usbmux:*:19004:0:99999:7:::
avahi:*:19004:0:99999:7:::
pulse:*:19004:0:99999:7:::
sddm:*:19004:0:99999:7:::
geoclue:*:19004:0:99999:7:::
tomcat:!:19004:0:99999:7:::
mysql:!:19004:0:99999:7:::
stunnel4:!:19004:0:99999:7:::
_rpc:*:19004:0:99999:7:::
dnsmasq:*:19004:0:99999:7:::
sslh:!:19004:0:99999:7:::
ntp:*:19004:0:99999:7:::
arpwatch:!:19004:0:99999:7:::
Debian-exim:!:19004:0:99999:7:::
debian-tor:*:19004:0:99999:7:::
redsocks:!:19004:0:99999:7:::
rwhod:*:19004:0:99999:7:::
freerad:*:19004:0:99999:7:::
iodine:*:19004:0:99999:7:::
tcpdump:*:19004:0:99999:7:::
miredo:*:19004:0:99999:7:::
statd:*:19004:0:99999:7:::
redis:*:19004:0:99999:7:::
postgres:*:19004:0:99999:7:::
inetsim:*:19004:0:99999:7:::
sshd:*:19004:0:99999:7:::
Debian-snmp:!:19004:0:99999:7:::
_gvm:*:19004:0:99999:7:::
saned:*:19004:0:99999:7:::
king-phisher:*:19004:0:99999:7:::
_caldera:*:19004:0:99999:7:::
dradis:*:19004:0:99999:7:::
beef-xss:*:19004:0:99999:7:::
uuidd:*:19004:0:99999:7:::
memcache:!:19004:0:99999:7:::
arangodb:!:19004::::::
admin:$6$2PjhBcvO4tMWKi5W$k/UUyb5mb3qTJ6Fr15cReTb0n/DQ9isy7knhpskIEQG.s9eB8auxVqrroksib7uQyiCtrJIgr48XmR8o7Pa7O/:18945:0:99999:7:::/
```

发现用户root和admin有哈希散列：

```
root:$6$iF3IRYx/LG3fxScL$spZt1Ltgnu4zyhnD2nYDZ9FSSKKcV0fRwnA3JNIGveciJQTWkPgv3qhE.URwgSakGvrJFNdHrfwYc9JLEZiGF/:18604:0:99999:7:::
admin:$6$2PjhBcvO4tMWKi5W$k/UUyb5mb3qTJ6Fr15cReTb0n/DQ9isy7knhpskIEQG.s9eB8auxVqrroksib7uQyiCtrJIgr48XmR8o7Pa7O/:18945:0:99999:7:::/
```

使用unshadow合并`/etc/passwd`和`/etc/shadow`：

```
unshadow /etc/passwd /etc/shadow > hashes
```

查看合并后的文件：

```
root@INE:~# cat hashes
root:$6$iF3IRYx/LG3fxScL$spZt1Ltgnu4zyhnD2nYDZ9FSSKKcV0fRwnA3JNIGveciJQTWkPgv3qhE.URwgSakGvrJFNdHrfwYc9JLEZiGF/:0:0:root:/root:/bin/bash
daemon:*:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:*:2:2:bin:/bin:/usr/sbin/nologin
sys:*:3:3:sys:/dev:/usr/sbin/nologin
sync:*:4:65534:sync:/bin:/bin/sync
games:*:5:60:games:/usr/games:/usr/sbin/nologin
man:*:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:*:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:*:8:8:mail:/var/mail:/usr/sbin/nologin
news:*:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:*:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:*:13:13:proxy:/bin:/usr/sbin/nologin
www-data:*:33:33:www-data:/var/www:/usr/sbin/nologin
backup:*:34:34:backup:/var/backups:/usr/sbin/nologin
list:*:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:*:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:*:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:*:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:*:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:*:101:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:*:102:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:*:103:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:*:104:105::/nonexistent:/usr/sbin/nologin
rtkit:*:105:106:RealtimeKit,,,:/proc:/usr/sbin/nologin
xrdp:!:106:112::/run/xrdp:/usr/sbin/nologin
usbmux:*:107:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
avahi:*:108:115:Avahi mDNS daemon,,,:/run/avahi-daemon:/usr/sbin/nologin
pulse:*:109:116:PulseAudio daemon,,,:/run/pulse:/usr/sbin/nologin
sddm:*:110:118:Simple Desktop Display Manager:/var/lib/sddm:/bin/false
geoclue:*:111:119::/var/lib/geoclue:/usr/sbin/nologin
tomcat:!:1000:1000::/opt/tomcat:/bin/false
mysql:!:112:120:MySQL Server,,,:/nonexistent:/bin/false
stunnel4:!:113:122::/var/run/stunnel4:/usr/sbin/nologin
_rpc:*:114:65534::/run/rpcbind:/usr/sbin/nologin
dnsmasq:*:115:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
sslh:!:116:124::/nonexistent:/usr/sbin/nologin
ntp:*:117:125::/nonexistent:/usr/sbin/nologin
arpwatch:!:118:127:ARP Watcher,,,:/var/lib/arpwatch:/bin/sh
Debian-exim:!:119:128::/var/spool/exim4:/usr/sbin/nologin
debian-tor:*:120:129::/var/lib/tor:/bin/false
redsocks:!:121:130::/var/run/redsocks:/usr/sbin/nologin
rwhod:*:122:65534::/var/spool/rwho:/usr/sbin/nologin
freerad:*:123:131::/etc/freeradius:/usr/sbin/nologin
iodine:*:124:65534::/run/iodine:/usr/sbin/nologin
tcpdump:*:125:132::/nonexistent:/usr/sbin/nologin
miredo:*:126:65534::/var/run/miredo:/usr/sbin/nologin
statd:*:127:65534::/var/lib/nfs:/usr/sbin/nologin
redis:*:128:134::/var/lib/redis:/usr/sbin/nologin
postgres:*:129:135:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
inetsim:*:130:138::/var/lib/inetsim:/usr/sbin/nologin
sshd:*:131:65534::/run/sshd:/usr/sbin/nologin
Debian-snmp:!:132:139::/var/lib/snmp:/bin/false
_gvm:*:133:141::/var/lib/openvas:/usr/sbin/nologin
saned:*:134:142::/var/lib/saned:/usr/sbin/nologin
king-phisher:*:135:143::/var/lib/king-phisher:/usr/sbin/nologin
_caldera:*:136:144::/var/lib/caldera:/usr/sbin/nologin
dradis:*:137:145::/var/lib/dradis:/usr/sbin/nologin
beef-xss:*:138:146::/var/lib/beef-xss:/usr/sbin/nologin
uuidd:*:139:147::/run/uuidd:/usr/sbin/nologin
memcache:!:140:148:Memcached,,,:/nonexistent:/bin/false
arangodb:!:999:999:ArangoDB Application User:/usr/share/arangodb3:/bin/false
admin:$6$2PjhBcvO4tMWKi5W$k/UUyb5mb3qTJ6Fr15cReTb0n/DQ9isy7knhpskIEQG.s9eB8auxVqrroksib7uQyiCtrJIgr48XmR8o7Pa7O/:1001:1001:,,,:/home/admin:/bin/bash
```

其中的有效信息为：

```
root:$6$iF3IRYx/LG3fxScL$spZt1Ltgnu4zyhnD2nYDZ9FSSKKcV0fRwnA3JNIGveciJQTWkPgv3qhE.URwgSakGvrJFNdHrfwYc9JLEZiGF/:0:0:root:/root:/bin/bash
admin:$6$2PjhBcvO4tMWKi5W$k/UUyb5mb3qTJ6Fr15cReTb0n/DQ9isy7knhpskIEQG.s9eB8auxVqrroksib7uQyiCtrJIgr48XmR8o7Pa7O/:1001:1001:,,,:/home/admin:/bin/bash
```

使用密码字典`/root/Desktop/wordlists/1000000-password-seclists.txt`，仅加载root和admin用户：

```
john --wordlist=/root/Desktop/wordlists/1000000-password-seclists.txt --users=root,admin hashes
```

```
root@INE:~# john --wordlist=/root/Desktop/wordlists/1000000-password-seclists.txt --users=root,admin hashes
Using default input encoding: UTF-8
Loaded 2 password hashes with 2 different salts (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 48 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
foxtrot          (admin)     
password         (root)     
2g 0:00:00:08 DONE (2022-07-15 07:22) 0.2392g/s 734.9p/s 1469c/s 1469C/s 123456..786786
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

显示所有已破解的密码：`root:password`，`admin:foxtrot`

```
john --show hashes
```

```
root@INE:~# john --show hashes
root:password:0:0:root:/root:/bin/bash
admin:foxtrot:1001:1001:,,,:/home/admin:/bin/bash

2 password hashes cracked, 0 left
```

`--show`展示的内容其实来自于`/root/.john/john.pot`文件：

```
root@INE:~# cat /root/.john/john.pot
$6$2PjhBcvO4tMWKi5W$k/UUyb5mb3qTJ6Fr15cReTb0n/DQ9isy7knhpskIEQG.s9eB8auxVqrroksib7uQyiCtrJIgr48XmR8o7Pa7O/:foxtrot
$6$iF3IRYx/LG3fxScL$spZt1Ltgnu4zyhnD2nYDZ9FSSKKcV0fRwnA3JNIGveciJQTWkPgv3qhE.URwgSakGvrJFNdHrfwYc9JLEZiGF/:password
```

#### hashcat
与 John 不同，使用 Hashcat 的最简单方法是只提供密码哈希本身。将我们想要破解的任何哈希复制到一个新的文本文件中，我们将其称为hashes.txt：

```
$6$iF3IRYx/LG3fxScL$spZt1Ltgnu4zyhnD2nYDZ9FSSKKcV0fRwnA3JNIGveciJQTWkPgv3qhE.URwgSakGvrJFNdHrfwYc9JLEZiGF/
$6$2PjhBcvO4tMWKi5W$k/UUyb5mb3qTJ6Fr15cReTb0n/DQ9isy7knhpskIEQG.s9eB8auxVqrroksib7uQyiCtrJIgr48XmR8o7Pa7O/
```

Hashcat 包含许多可以运行的模式，具体取决于所使用的哈希类型。我们之前看到 John 将我们的影子哈希标识为 `sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x]`，因此我们可以键入`hashcat --help`以显示此工具的所有选项以及可用的不同模式。在列表中，我们发现 `sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x]` 是模式 1800：

```
root@INE:~# hashcat --help | grep sha512
   1770 | sha512(utf16le($pass))                              | Raw Hash
   1710 | sha512($pass.$salt)                                 | Raw Hash salted and/or iterated
   1720 | sha512($salt.$pass)                                 | Raw Hash salted and/or iterated
   1740 | sha512($salt.utf16le($pass))                        | Raw Hash salted and/or iterated
   1730 | sha512(utf16le($pass).$salt)                        | Raw Hash salted and/or iterated
   6500 | AIX {ssha512}                                       | Operating System
   1800 | sha512crypt $6$, SHA512 (Unix)                      | Operating System
  21600 | Web2py pbkdf2-sha512                                | Framework
  20200 | Python passlib pbkdf2-sha512                        | Framework
  21000 | BitShares v0.x - sha512(sha512_bin(pass))           | Cryptocurrency Wallet
```

```
hashcat -m 1800 -a 0 -o cracked.txt hashes.txt /root/Desktop/wordlists/1000000-password-seclists.txt
```

* `-m`：指定使用的模式。
* `-a`：确定攻击类型，0表示默认的直接模式。
* `-o`：指定输出文件为cracked.txt，输入文件是包含哈希的hashes.txt，使用字典`/root/Desktop/wordlists/1000000-password-seclists.txt`。

```
root@INE:~# hashcat -m 1800 -a 0 -o cracked.txt hashes.txt /root/Desktop/wordlists/1000000-password-seclists.txt
hashcat (v6.2.5) starting

OpenCL API (OpenCL 2.0 pocl 1.8  Linux, None+Asserts, RELOC, LLVM 11.1.0, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
=====================================================================================================================================
* Device #1: pthread-AMD EPYC 7642 48-Core Processor, 47227/94518 MB (16384 MB allocatable), 48MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 2 digests; 2 unique digests, 2 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Uses-64-Bit

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 0 MB


Dictionary cache built:
* Filename..: /root/Desktop/wordlists/1000000-password-seclists.txt
* Passwords.: 1000003
* Bytes.....: 8529147
* Keyspace..: 1000003
* Runtime...: 0 secs

                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 1800 (sha512crypt $6$, SHA512 (Unix))
Hash.Target......: hashes.txt
Time.Started.....: Fri Jul 15 08:09:14 2022 (6 secs)
Time.Estimated...: Fri Jul 15 08:09:20 2022 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/root/Desktop/wordlists/1000000-password-seclists.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:      548 H/s (42.64ms) @ Accel:1024 Loops:128 Thr:1 Vec:4
Recovered........: 2/2 (100.00%) Digests, 2/2 (100.00%) Salts
Progress.........: 4096/2000006 (0.20%)
Rejected.........: 0/4096 (0.00%)
Restore.Point....: 1024/1000003 (0.10%)
Restore.Sub.#1...: Salt:1 Amplifier:0-1 Iteration:4992-5000
Candidate.Engine.: Device Generator
Candidates.#1....: sports -> ball

Started: Fri Jul 15 08:08:06 2022
Stopped: Fri Jul 15 08:09:21 2022
```

显示cracked.txt的内容，查看明文形式的密码：

```
root@INE:~# cat cracked.txt
$6$iF3IRYx/LG3fxScL$spZt1Ltgnu4zyhnD2nYDZ9FSSKKcV0fRwnA3JNIGveciJQTWkPgv3qhE.URwgSakGvrJFNdHrfwYc9JLEZiGF/:password
$6$2PjhBcvO4tMWKi5W$k/UUyb5mb3qTJ6Fr15cReTb0n/DQ9isy7knhpskIEQG.s9eB8auxVqrroksib7uQyiCtrJIgr48XmR8o7Pa7O/:foxtrot
```
### 查找受保护的 Microsoft Office .docx 文件的密码
#### John the Ripper
**参考：** [破解受密码保护的 Microsoft Office 文件，包括 Word 文档和 Excel 电子表格](https://null-byte.wonderhowto.com/how-to/crack-password-protected-microsoft-office-files-including-word-docs-excel-spreadsheets-0193959/)

首先安装[office2john](https://github.com/openwall/john/blob/bleeding-jumbo/run/office2john.py)

提取受密码保护的 Office 文件的哈希值

```
python office2john.py Desktop/MS_Word_Document.docx > hash.txt
cat hash.txt
```

保存的哈希对应 Microsoft Office 2013

```
MS_Word_Document.docx:$office$*2013*100000*256*16*ff2563844faca58a12fc42c5036f9cf8*ffaf52db903dbcb6ac2db4bab6d343ab*c237403ec97e5f68b7be3324a8633c9ff95e0bb44b1efcf798c70271a54336a2
```

```
john --wordlist=/root/Desktop/wordlists/1000000-password-seclists.txt hash.txt
```

```
root@INE:~# john --wordlist=/root/Desktop/wordlists/1000000-password-seclists.txt hash.txt
Created directory: /root/.john
Using default input encoding: UTF-8
Loaded 1 password hash (Office, 2007/2010/2013 [SHA1 256/256 AVX2 8x / SHA512 256/256 AVX2 4x AES])
Cost 1 (MS Office version) is 2013 for all loaded hashes
Cost 2 (iteration count) is 100000 for all loaded hashes
Will run 48 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
muenchen         (MS_Word_Document.docx)     
1g 0:00:05:08 DONE (2022-07-15 11:20) 0.003240g/s 77.14p/s 77.14c/s 77.14C/s shake..963741
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

```
cat /root/.john/john.pot
john --show hash.txt
```

文档MS_Word_Document.docx的密码是`muenchen`。

```
root@INE:~# cat /root/.john/john.pot
$office$*2013*100000*256*16*ff2563844faca58a12fc42c5036f9cf8*ffaf52db903dbcb6ac2db4bab6d343ab*c237403ec97e5f68b7be3324a8633c9ff95e0bb44b1efcf798c70271a54336a2:muenchen
root@INE:~# john --show hash.txt
MS_Word_Document.docx:muenchen

1 password hash cracked, 0 left
```

#### hashcat
**参考：** [通过本地或云中的 hashcat 破解 Microsoft Office 密码保护](https://tinyapps.org/docs/hashcat.html)

查看MS_Word_Document.docx的文件属性，文件类型是Word 2007 document。

使用[office2hashcat.py](https://github.com/stricture/hashstack-server-plugin-hashcat/blob/master/scrapers/office2hashcat.py)检索受密码保护的word文档的加密哈希，将其保存到hash.txt：

```
./office2hashcat.py Desktop/MS_Word_Document.docx > hash.txt
cat hash.txt
```

哈希标识为 MS Office 2013

```
$office$*2013*100000*256*16*ff2563844faca58a12fc42c5036f9cf8*ffaf52db903dbcb6ac2db4bab6d343ab*c237403ec97e5f68b7be3324a8633c9ff95e0bb44b1efcf798c70271a54336a2
```

由 hashcat 为其分配了哈希模式编号 9600

```
root@INE:~# hashcat --help | grep "MS Office"
   9400 | MS Office 2007                                      | Document
   9500 | MS Office 2010                                      | Document
   9600 | MS Office 2013                                      | Document
  25300 | MS Office 2016 - SheetProtection                    | Document
   9700 | MS Office <= 2003 $0/$1, MD5 + RC4                  | Document
   9710 | MS Office <= 2003 $0/$1, MD5 + RC4, collider #1     | Document
   9720 | MS Office <= 2003 $0/$1, MD5 + RC4, collider #2     | Document
   9810 | MS Office <= 2003 $3, SHA1 + RC4, collider #1       | Document
   9820 | MS Office <= 2003 $3, SHA1 + RC4, collider #2       | Document
   9800 | MS Office <= 2003 $3/$4, SHA1 + RC4                 | Document
```

将以下参数传递给hashcat：
* `-a 0`：将攻击模式设置为直接/字典攻击
* `-m 9600`：将哈希模式设置为MS Office 2013
* `--status`：自动更新状态屏幕
* `-o found.txt`：将恢复的密码输出到found.txt
* `hash.txt`：在上一步中保存的哈希
* `/root/Desktop/wordlists/1000000-password-seclists.txt`：密码字典

```
hashcat -a 0 -m 9600 --status -o found.txt hash.txt /root/Desktop/wordlists/1000000-password-seclists.txt
```

```
root@INE:~# hashcat -a 0 -m 9600 --status -o found.txt hash.txt /root/Desktop/wordlists/1000000-password-seclists.txt 
hashcat (v6.2.5) starting

OpenCL API (OpenCL 2.0 pocl 1.8  Linux, None+Asserts, RELOC, LLVM 11.1.0, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
=====================================================================================================================================
* Device #1: pthread-AMD EPYC 7642 48-Core Processor, 47227/94518 MB (16384 MB allocatable), 48MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Single-Hash
* Single-Salt
* Slow-Hash-SIMD-LOOP
* Uses-64-Bit

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 0 MB

Dictionary cache built:
* Filename..: /root/Desktop/wordlists/1000000-password-seclists.txt
* Passwords.: 1000003
* Bytes.....: 8529147
* Keyspace..: 1000003
* Runtime...: 0 secs

[s]tatus [p]ause [b]ypass [c]heckpoint [f]inish [q]uit => 

Session..........: hashcat
Status...........: Running
Hash.Mode........: 9600 (MS Office 2013)
Hash.Target......: $office$*2013*100000*256*16*ff2563844faca58a12fc42c...4336a2
Time.Started.....: Fri Jul 15 10:21:09 2022 (7 secs)
Time.Estimated...: Fri Jul 15 13:46:06 2022 (3 hours, 24 mins)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/root/Desktop/wordlists/1000000-password-seclists.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:       81 H/s (6.91ms) @ Accel:1024 Loops:64 Thr:1 Vec:4
Recovered........: 0/1 (0.00%) Digests
Progress.........: 0/1000003 (0.00%)
Rejected.........: 0/0 (0.00%)
Restore.Point....: 0/1000003 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:56448-56512
Candidate.Engine.: Device Generator
Candidates.#1....: 123456 -> time

[s]tatus [p]ause [b]ypass [c]heckpoint [f]inish [q]uit =>

Session..........: hashcat
Status...........: Running
Hash.Mode........: 9600 (MS Office 2013)
Hash.Target......: $office$*2013*100000*256*16*ff2563844faca58a12fc42c...4336a2
Time.Started.....: Fri Jul 15 10:21:09 2022 (4 mins, 19 secs)
Time.Estimated...: Fri Jul 15 13:28:41 2022 (3 hours, 3 mins)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/root/Desktop/wordlists/1000000-password-seclists.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:       89 H/s (6.10ms) @ Accel:1024 Loops:64 Thr:1 Vec:4
Recovered........: 0/1 (0.00%) Digests
Progress.........: 22528/1000003 (2.25%)
Rejected.........: 0/22528 (0.00%)
Restore.Point....: 22528/1000003 (2.25%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:51200-51264
Candidate.Engine.: Device Generator
Candidates.#1....: sandeep -> luckyboy

Session..........: hashcat                                
Status...........: Cracked
Hash.Mode........: 9600 (MS Office 2013)
Hash.Target......: $office$*2013*100000*256*16*ff2563844faca58a12fc42c...4336a2
Time.Started.....: Fri Jul 15 10:21:09 2022 (4 mins, 24 secs)
Time.Estimated...: Fri Jul 15 10:25:33 2022 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/root/Desktop/wordlists/1000000-password-seclists.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:       89 H/s (6.16ms) @ Accel:1024 Loops:64 Thr:1 Vec:4
Recovered........: 1/1 (100.00%) Digests
Progress.........: 23552/1000003 (2.36%)
Rejected.........: 0/23552 (0.00%)
Restore.Point....: 22528/1000003 (2.25%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: sandeep -> luckyboy
Started: Fri Jul 15 10:19:58 2022
Stopped: Fri Jul 15 10:25:36 2022
```

文档MS_Word_Document.docx的密码是`muenchen`。

```
root@INE:~# cat found.txt
$office$*2013*100000*256*16*ff2563844faca58a12fc42c5036f9cf8*ffaf52db903dbcb6ac2db4bab6d343ab*c237403ec97e5f68b7be3324a8633c9ff95e0bb44b1efcf798c70271a54336a2:muenchen
```

## 解决方案
### 破解 Linux 用户哈希

第 1 步：打开实验室链接以访问 Kali GUI 实例。

第 2 步：在 Linux 操作系统中，用户的密码哈希存储在 `/etc/shadow` 文件中。使用命令`cat`读取文件并检查“admin”用户的条目。

**命令**

```
cat /etc/shadow
```

```
root@INE:~# cat /etc/shadow
root:$6$iF3IRYx/LG3fxScL$spZt1Ltgnu4zyhnD2nYDZ9FSSKKcV0fRwnA3JNIGveciJQTWkPgv3qhE.URwgSakGvrJFNdHrfwYc9JLEZiGF/:18604:0:99999:7:::
daemon:*:19002:0:99999:7:::
bin:*:19002:0:99999:7:::
sys:*:19002:0:99999:7:::
sync:*:19002:0:99999:7:::
games:*:19002:0:99999:7:::
man:*:19002:0:99999:7:::
lp:*:19002:0:99999:7:::
mail:*:19002:0:99999:7:::
news:*:19002:0:99999:7:::
uucp:*:19002:0:99999:7:::
proxy:*:19002:0:99999:7:::
www-data:*:19002:0:99999:7:::
backup:*:19002:0:99999:7:::
list:*:19002:0:99999:7:::
irc:*:19002:0:99999:7:::
gnats:*:19002:0:99999:7:::
nobody:*:19002:0:99999:7:::
_apt:*:19002:0:99999:7:::
systemd-network:*:19004:0:99999:7:::
systemd-resolve:*:19004:0:99999:7:::
systemd-timesync:*:19004:0:99999:7:::
messagebus:*:19004:0:99999:7:::
rtkit:*:19004:0:99999:7:::
xrdp:!:19004:0:99999:7:::
usbmux:*:19004:0:99999:7:::
avahi:*:19004:0:99999:7:::
pulse:*:19004:0:99999:7:::
sddm:*:19004:0:99999:7:::
geoclue:*:19004:0:99999:7:::
tomcat:!:19004:0:99999:7:::
mysql:!:19004:0:99999:7:::
stunnel4:!:19004:0:99999:7:::
_rpc:*:19004:0:99999:7:::
dnsmasq:*:19004:0:99999:7:::
sslh:!:19004:0:99999:7:::                                                                                                                                                                   
ntp:*:19004:0:99999:7:::                                                                                                                                                                    
arpwatch:!:19004:0:99999:7:::                                                                                                                                                               
Debian-exim:!:19004:0:99999:7:::                                                                                                                                                            
debian-tor:*:19004:0:99999:7:::                                                                                                                                                             
redsocks:!:19004:0:99999:7:::
rwhod:*:19004:0:99999:7:::
freerad:*:19004:0:99999:7:::
iodine:*:19004:0:99999:7:::
tcpdump:*:19004:0:99999:7:::
miredo:*:19004:0:99999:7:::
statd:*:19004:0:99999:7:::
redis:*:19004:0:99999:7:::
postgres:*:19004:0:99999:7:::
inetsim:*:19004:0:99999:7:::
sshd:*:19004:0:99999:7:::
Debian-snmp:!:19004:0:99999:7:::
_gvm:*:19004:0:99999:7:::
saned:*:19004:0:99999:7:::
king-phisher:*:19004:0:99999:7:::
_caldera:*:19004:0:99999:7:::
dradis:*:19004:0:99999:7:::
beef-xss:*:19004:0:99999:7:::
uuidd:*:19004:0:99999:7:::
memcache:!:19004:0:99999:7:::
arangodb:!:19004::::::
admin:$6$2PjhBcvO4tMWKi5W$k/UUyb5mb3qTJ6Fr15cReTb0n/DQ9isy7knhpskIEQG.s9eB8auxVqrroksib7uQyiCtrJIgr48XmR8o7Pa7O/:18945:0:99999:7:::/
```

管理员用户的哈希值如下所述：

管理员用户哈希

```
admin:$6$2PjhBcvO4tMWKi5W$k/UUyb5mb3qTJ6Fr15cReTb0n/DQ9isy7knhpskIEQG.s9eB8auxVqrroksib7uQyiCtrJIgr48XmR8o7Pa7O/:18945:0:99999:7:::
```

第 3 步：检查用于散列影子文件中存在的密码的散列算法，方法是检查`/etc/login.defs`

**命令**

```
grep -A 18 ENCRYPT_METHOD /etc/login.defs
```

```
root@INE:~# grep -A 18 ENCRYPT_METHOD /etc/login.defs
# This variable is deprecated. You should use ENCRYPT_METHOD.
#
#MD5_CRYPT_ENAB no

#
# If set to MD5 , MD5-based algorithm will be used for encrypting password
# If set to SHA256, SHA256-based algorithm will be used for encrypting password
# If set to SHA512, SHA512-based algorithm will be used for encrypting password
# If set to DES, DES-based algorithm will be used for encrypting password (default)
# Overrides the MD5_CRYPT_ENAB option
#
# Note: It is recommended to use a value consistent with
# the PAM modules configuration.
#
ENCRYPT_METHOD SHA512

#
# Only used if ENCRYPT_METHOD is set to SHA256 or SHA512.
#
# Define the number of SHA rounds.
# With a lot of rounds, it is more difficult to brute forcing the password.
# But note also that it more CPU resources will be needed to authenticate
# users.
#
# If not specified, the libc will choose the default number of rounds (5000).
# The values must be inside the 1000-999999999 range.
# If only one of the MIN or MAX values is set, then this value will be used.
# If MIN > MAX, the highest value will be used.
#
# SHA_CRYPT_MIN_ROUNDS 5000
# SHA_CRYPT_MAX_ROUNDS 5000

################# OBSOLETED BY PAM ##############
#                                               #
# These options are now handled by PAM. Please  #
# edit the appropriate file in /etc/pam.d/ to   #
```

使用哈希算法`SHA512`。

第 4 步：复制“admin”用户的条目并将其保存到一个新文件中。

**命令**

```
tail -n 1 /etc/shadow > admin.hash
cat admin.hash
```

`-n 1`: 读取/etc/shadow文件的最后一行（admin entry是shadow文件的最后一行）

```
root@INE:~# tail -n 1 /etc/shadow > admin.hash
root@INE:~# cat admin.hash 
admin:$6$2PjhBcvO4tMWKi5W$k/UUyb5mb3qTJ6Fr15cReTb0n/DQ9isy7knhpskIEQG.s9eB8auxVqrroksib7uQyiCtrJIgr48XmR8o7Pa7O/:18945:0:99999:7:::/
```

第 5 步：我们必须修改条目以与工具的输入兼容。删除“admin:”和“:18945:0:99999:7:::”

```
admin:$6$2PjhBcvO4tMWKi5W$k/UUyb5mb3qTJ6Fr15cReTb0n/DQ9isy7knhpskIEQG.s9eB8auxVqrroksib7uQyiCtrJIgr48XmR8o7Pa7O/:18945:0:99999:7:::/
```

```
$6$2PjhBcvO4tMWKi5W$k/UUyb5mb3qTJ6Fr15cReTb0n/DQ9isy7knhpskIEQG.s9eB8auxVqrroksib7uQyiCtrJIgr48XmR8o7Pa7O/
```

修改后，条目将如下所示：

最终哈希

```
$6$2PjhBcvO4tMWKi5W$k/UUyb5mb3qTJ6Fr15cReTb0n/DQ9isy7knhpskIEQG.s9eB8auxVqrroksib7uQyiCtrJIgr48XmR8o7Pa7O/
```

#### 使用Hashcat
第六步：使用Hashcat工具破解哈希

**命令**

```
hashcat -m 1800 -a 0 admin.hash /root/Desktop/wordlists/1000000-password-seclists.txt
```

Hashcat:

* 开源、强大的密码破解工具
* 支持多操作系统（Linux、Windows 和 macOS）
* 支持 350 多种算法

```
root@INE:~# hashcat -m 1800 -a 0 admin.hash /root/Desktop/wordlists/1000000-password-seclists.txt 
hashcat (v6.2.5) starting

OpenCL API (OpenCL 2.0 pocl 1.8  Linux, None+Asserts, RELOC, LLVM 11.1.0, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
=====================================================================================================================================
* Device #1: pthread-AMD EPYC 7642 48-Core Processor, 47227/94518 MB (16384 MB allocatable), 48MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Single-Hash
* Single-Salt
* Uses-64-Bit

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 0 MB

Dictionary cache built:
* Filename..: /root/Desktop/wordlists/1000000-password-seclists.txt
* Passwords.: 1000003
* Bytes.....: 8529147
* Keyspace..: 1000003
* Runtime...: 0 secs

$6$2PjhBcvO4tMWKi5W$k/UUyb5mb3qTJ6Fr15cReTb0n/DQ9isy7knhpskIEQG.s9eB8auxVqrroksib7uQyiCtrJIgr48XmR8o7Pa7O/:foxtrot
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 1800 (sha512crypt $6$, SHA512 (Unix))
Hash.Target......: $6$2PjhBcvO4tMWKi5W$k/UUyb5mb3qTJ6Fr15cReTb0n/DQ9is...7Pa7O/
Time.Started.....: Fri Jul 15 17:47:07 2022 (4 secs)
Time.Estimated...: Fri Jul 15 17:47:11 2022 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/root/Desktop/wordlists/1000000-password-seclists.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:      538 H/s (9.40ms) @ Accel:1024 Loops:32 Thr:1 Vec:4
Recovered........: 1/1 (100.00%) Digests
Progress.........: 2048/1000003 (0.20%)
Rejected.........: 0/2048 (0.00%)
Restore.Point....: 1024/1000003 (0.10%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:4992-5000
Candidate.Engine.: Device Generator
Candidates.#1....: sports -> ball

Started: Fri Jul 15 17:45:57 2022
Stopped: Fri Jul 15 17:47:13 2022
```

“admin”用户的密码是foxtrot。

#### 使用John The Ripper
第 7 步：使用 John The Ripper (JTR) 破解哈希

我们可以直接在影子文件上运行 JTR。

**命令**

```
john /etc/shadow --wordlist=/root/Desktop/wordlists/1000000-password-seclists.txt
```

John the Ripper

* 免费开源，密码破解工具
* 支持多操作系统（Linux、Windows 和 macOS）

```
root@INE:~# john /etc/shadow --wordlist=/root/Desktop/wordlists/1000000-password-seclists.txt 
Created directory: /root/.john
Using default input encoding: UTF-8
Loaded 2 password hashes with 2 different salts (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 48 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
foxtrot          (admin)     
password         (root)     
2g 0:00:00:07 DONE (2022-07-15 17:52) 0.2531g/s 777.7p/s 1555c/s 1555C/s 123456..786786
Use the "--show" option to display all of the cracked passwords reliably
Session completed.

root@INE:~# john --show /etc/shadow
root:password:18604:0:99999:7:::
admin:foxtrot:18945:0:99999:7:::/

2 password hashes cracked, 0 left
```

用户“root”和“admin”的密码分别是password和foxtrot 。

### 破解Microsoft Word文件的密码
第 8 步：我们将使用 John The Ripper 和 Hashcat 破解“MS_Word_Document.docx”文件。

MS_Word_Document.docx 文件位置`/root/Desktop/MS_Word_Document.docx`

#### 使用John the Ripper
首先，我们需要使用 `office2john` python 脚本从文件中提取可破解信息。

**命令**

```
cd /root/Desktop/
/usr/share/john/office2john.py MS_Word_Document.docx > hash
cat hash
```

`office2john.py`是一个 python 脚本，用于从 Microsoft Office .docx 文件中提取可破解信息。

```
root@INE:~# cd /root/Desktop/
root@INE:~/Desktop# /usr/share/john/office2john.py MS_Word_Document.docx > hash
root@INE:~/Desktop# cat hash
MS_Word_Document.docx:$office$*2013*100000*256*16*ff2563844faca58a12fc42c5036f9cf8*ffaf52db903dbcb6ac2db4bab6d343ab*c237403ec97e5f68b7be3324a8633c9ff95e0bb44b1efcf798c70271a54336a2
```

我们可以观察到该文件是从 MS Office 2013 版本保存的。

使用 John The Ripper 工具破解哈希。

**命令**

```
john --wordlist=/root/Desktop/wordlists/1000000-password-seclists.txt hash
```

```
root@INE:~/Desktop# john --wordlist=/root/Desktop/wordlists/1000000-password-seclists.txt hash
Using default input encoding: UTF-8
Loaded 1 password hash (Office, 2007/2010/2013 [SHA1 256/256 AVX2 8x / SHA512 256/256 AVX2 4x AES])
Cost 1 (MS Office version) is 2013 for all loaded hashes
Cost 2 (iteration count) is 100000 for all loaded hashes
Will run 48 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
muenchen         (MS_Word_Document.docx)     
1g 0:00:04:53 DONE (2022-07-15 18:09) 0.003405g/s 81.08p/s 81.08c/s 81.08C/s shake..963741
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

用于加密docx文件的密码是**muenchen**

#### 使用Hashcat
第9步：同样，我们将使用Hashcat工具来恢复密码。

我们需要使提取的可破解信息与Hashcat工具兼容。从文件中删除`MS_Word_Document.docx:`值。

```
MS_Word_Document.docx:$office$*2013*100000*256*16*ff2563844faca58a12fc42c5036f9cf8*ffaf52db903dbcb6ac2db4bab6d343ab*c237403ec97e5f68b7be3324a8633c9ff95e0bb44b1efcf798c70271a54336a2
```

删除突出显示的区域。

```
$office$*2013*100000*256*16*ff2563844faca58a12fc42c5036f9cf8*ffaf52db903dbcb6ac2db4bab6d343ab*c237403ec97e5f68b7be3324a8633c9ff95e0bb44b1efcf798c70271a54336a2
```

最终内容

```
$office$*2013*100000*256*16*ff2563844faca58a12fc42c5036f9cf8*ffaf52db903dbcb6ac2db4bab6d343ab*c237403ec97e5f68b7be3324a8633c9ff95e0bb44b1efcf798c70271a54336a2
```

使用 Hashcat 破解哈希

**命令**

```
hashcat -a 0 -m 9600 --status hash /root/Desktop/wordlists/1000000-password-seclists.txt --force
```

命令说明：

`-a 0`: 将攻击模式设置为字典

`-m 9600`: 将方法设置为 MS Office 2013

`--status`：启用状态屏幕的自动更新

`hash`: 包含可破解信息的文件

`/root/Desktop/wordlists/1000000-password-seclists.txt`: 密码词表

```
$office$*2013*100000*256*16*ff2563844faca58a12fc42c5036f9cf8*ffaf52db903dbcb6ac2db4bab6d343ab*c237403ec97e5f68b7be3324a8633c9ff95e0bb44b1efcf798c70271a54336a2:muenchen
Session..........: hashcat                                
Status...........: Cracked
Hash.Mode........: 9600 (MS Office 2013)
Hash.Target......: $office$*2013*100000*256*16*ff2563844faca58a12fc42c...4336a2
Time.Started.....: Fri Jul 15 18:22:48 2022, (4 mins, 23 secs)
Time.Estimated...: Fri Jul 15 18:27:11 2022, (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/root/Desktop/wordlists/1000000-password-seclists.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:       89 H/s (13.95ms) @ Accel:1024 Loops:128 Thr:1 Vec:4
Recovered........: 1/1 (100.00%) Digests
Progress.........: 23552/1000003 (2.36%)
Rejected.........: 0/23552 (0.00%)
Restore.Point....: 22528/1000003 (2.25%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: sandeep -> luckyboy
Started: Fri Jul 15 18:22:07 2022
Stopped: Fri Jul 15 18:27:14 2022
```

用于加密docx文件的密码是`muenchen`

这就是我们如何使用 Hashcat 和 JTR 破解哈希并恢复加密的 Microsoft 文档文件的密码