---
layout: post
title: "HTTP Apache"
date: "2022-08-25"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Assessment Methodologies
    - Enumeration
---

# HTTP Apache
```
ip a
eth1 192.32.62.2/24
```

```
nmap 192.32.62.3

PORT    STATE   SERVICE
80/tcp  open    http
```

```
nmap 192.32.62.3 -p 80 -sV

PORT    STATE   SERVICE VERSION
80/tcp  open    http    Apache httpd 2.4.18 ((Ubuntu))
```

```
nmap 192.32.62.3 -p 80 -sV -script banner

PORT    STATE   SERVICE VERSION
80/tcp  open    http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache httpd 2.4.18 ((Ubuntu))
```

```
msfconsole

use auxiliary/scanner/http/http_version
set rhosts 192.32.62.3
options
run
exit
```

```
curl 192.32.62.3 | more
```

```
wget "http://192.32.62.3/index"
```

```
cat index | more
```

```
browsh --startup-url 192.32.62.3
```

```
lynx http://192.32.62.3
```

```
msfconsole

use auxiliary/scanner/http/brute_dirs
show options
set rhosts 192.32.62.3
options
exploit
exit
```

```
dirb http://192.32.62.3 /usr/share/metasploit-framework/data/wordlists/directory.txt
```

```
metasploit

use auxiliary/scanner/http/robots_txt
set rhosts 192.32.62.3
options
run
```

`/cgi-bin/` exist

```
curl http://192.32.62.3/cgi-bin/ | more
```

# Apache Recon: Basics（阿帕奇侦察：基础）
## 概述
在这个挑战中，我们将了解 Apache 服务器侦察的基础知识。请开始实验并回答以下问题：

**问题**

1. 目标服务器上正在运行哪些 Web 服务器软件，还可以使用 nmap 找出版本。
2. 目标服务器上正在运行哪个 Web 服务器软件，还可以使用合适的 metasploit 模块找出版本。
3. 使用 curl 命令检查 Web 服务器上托管的 Web 应用程序。
4. 使用 wget 命令检查 Web 服务器上托管的 Web 应用程序。
5. 使用基于浏览器 CLI 的浏览器检查 Web 服务器上托管的 Web 应用程序。
6. 使用基于 lynx CLI 的浏览器检查 Web 服务器上托管的 Web 应用程序。
7. 对网络服务器目录执行暴力破解并列出找到的目录名称。使用 brute_dirs metasploit 模块。
8. 使用带有 tool/usr/share/metasploit-framework/data/wordlists/directory.txt 字典的目录 buster (dirb) 来检查 Web 服务器的根文件夹中是否存在任何目录。列出找到的目录的名称。
9. 哪个机器人被明确禁止访问特定目录？

**指示：**

* 这个实验室是献给你的！此网络上没有其他用户 :)
* 开始实验室后，您将可以访问 Kali 实例的根终端
* 你的 Kali 有一个 IP 地址为 192.XYZ 的接口运行“ip addr”来知道 X 和 Y 的值。
* 目标机器应位于 IP 地址 192.XY3。
* 不要攻击位于 IP 地址 192.XY1 的网关

## 我自己的思路
In this challenge we will look at the basics of Apache server reconnaissance. Please start the lab and answer the following questions:

```
root@attackdefense:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
31061: eth0@if31062: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:0d brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.13/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
31064: eth1@if31065: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:17:3e:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.23.62.2/24 brd 192.23.62.255 scope global eth1
       valid_lft forever preferred_lft forever
```

```
root@attackdefense:~# nmap 192.23.62.3
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-25 03:18 UTC
Nmap scan report for target-1 (192.23.62.3)
Host is up (0.000014s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
80/tcp open  http
MAC Address: 02:42:C0:17:3E:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.33 seconds
```

Questions

Which web server software is running on the target server and also find out the version using nmap.

`Apache/2.4.18 (Ubuntu)`

```
root@attackdefense:~# nmap 192.23.62.3 -p 80 -sV
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-25 03:19 UTC
Nmap scan report for target-1 (192.23.62.3)
Host is up (0.000039s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
MAC Address: 02:42:C0:17:3E:03 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.66 seconds
```

[banner](https://nmap.org/nsedoc/scripts/banner.html)

```
root@attackdefense:~# nmap -p 80 -sV --script=banner 192.23.62.3
Starting Nmap 7.70 ( https://nmap.org ) at 2022-08-25 03:31 UTC
Nmap scan report for target-1 (192.23.62.3)
Host is up (0.000057s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
MAC Address: 02:42:C0:17:3E:03 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 21.61 seconds
```

Which web server software is running on the target server and also find out the version using suitable metasploit module.

`Apache/2.4.18 (Ubuntu)`

[http_version](https://www.rapid7.com/db/modules/auxiliary/scanner/http/http_version/)

显示每个系统的版本信息。

```
msf5 > use auxiliary/scanner/http/http_version
msf5 auxiliary(scanner/http/http_version) > show options

Module options (auxiliary/scanner/http/http_version):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                    yes       The target address range or CIDR identifier
   RPORT    80               yes       The target port (TCP)
   SSL      false            no        Negotiate SSL/TLS for outgoing connections
   THREADS  1                yes       The number of concurrent threads
   VHOST                     no        HTTP server virtual host

msf5 auxiliary(scanner/http/http_version) > set RHOSTS 192.23.62.3
RHOSTS => 192.23.62.3
msf5 auxiliary(scanner/http/http_version) > run

[+] 192.23.62.3:80 Apache/2.4.18 (Ubuntu)
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Check what web app is hosted on the web server using curl command.

`Apache2 Ubuntu Default Page: It works`

```
root@attackdefense:~# curl 192.23.62.3 | more
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 11320    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
1 <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
 1<html xmlns="http://www.w3.org/1999/xhtml">
0  <!--
0 113    Modified from the Debian original for Ubuntu
21     Last updated: 2014-03-19
      See: https://launchpad.net/bugs/1288690
   -->
0  <head>
      <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
      <title>Apache2 Ubuntu Default Page: It works</title>
 0    <style type="text/css" media="screen">
   * {
     margin: 0px 0px 0px 0px;
1    padding: 0px 0px 0px 0px;
0  }
.
7  body, html {
M    padding: 3px 3px 3px 3px;
 
     background-color: #D8DBE2;
 
     font-family: Verdana, sans-serif;
     font-size: 11pt;
 0    text-align: center;
   }
-
-  div.main_page {
:    position: relative;
-    display: table;
-
:    width: 800px;
-
-    margin-bottom: 3px;
     margin-left: auto;
-    margin-right: auto;
-    padding: 0px 0px 0px 0px;
:
----More--:-- --:--:-- 10.7M
```

Check what web app is hosted on the web server using wget command.

```
root@attackdefense:~# wget "http://192.23.62.3/index"
--2022-08-25 03:43:27--  http://192.23.62.3/index
Connecting to 192.23.62.3:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 11321 (11K) [text/html]
Saving to: 'index'

index                                          100%[===================================================================================================>]  11.06K  --.-KB/s    in 0s      

2022-08-25 03:43:27 (196 MB/s) - 'index' saved [11321/11321]
```

```
root@attackdefense:~# cat index | more

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
  <!--
    Modified from the Debian original for Ubuntu
    Last updated: 2014-03-19
    See: https://launchpad.net/bugs/1288690
  -->
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Apache2 Ubuntu Default Page: It works</title>
    <style type="text/css" media="screen">
```

Check what web app is hosted on the web server using browsh CLI based browser.

```
root@attackdefense:~# browsh --startup-url 192.23.62.3
```

`Ctrl+Q` Exit Browsh

Check what web app is hosted on the web server using lynx CLI based browser.

```
root@attackdefense:~# lynx http://192.23.62.3
```

Perform bruteforce on webserver directories and list the names of directories found. Use brute_dirs metasploit module.

`/dir/, /src/`

[HTTP Directory Brute Force Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/http/brute_dirs/)

该模块通过在给定的目录路径中强制使用名称来识别感兴趣的目录的存在。

```
msf5 > use auxiliary/scanner/http/brute_dirs
msf5 auxiliary(scanner/http/brute_dirs) > show options

Module options (auxiliary/scanner/http/brute_dirs):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   FORMAT   a,aa,aaa         yes       The expected directory format (a alpha, d digit, A upperalpha)
   PATH     /                yes       The path to identify directories
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                    yes       The target address range or CIDR identifier
   RPORT    80               yes       The target port (TCP)
   SSL      false            no        Negotiate SSL/TLS for outgoing connections
   THREADS  1                yes       The number of concurrent threads
   VHOST                     no        HTTP server virtual host

msf5 auxiliary(scanner/http/brute_dirs) > set RHOSTS 192.23.62.3
RHOSTS => 192.23.62.3
msf5 auxiliary(scanner/http/brute_dirs) > run

[*] Using code '404' as not found.
[+] Found http://192.23.62.3:80/dir/ 200
[+] Found http://192.23.62.3:80/src/ 200
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Use the directory buster (dirb) with tool/usr/share/metasploit-framework/data/wordlists/directory.txt dictionary to check if any directory is present in the root folder of the web server. List the names of found directories.

`/data, /dir`

```
root@attackdefense:~# dirb http://192.23.62.3/ /usr/share/metasploit-framework/data/wordlists/directory.txt 

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Thu Aug 25 04:01:36 2022
URL_BASE: http://192.23.62.3/
WORDLIST_FILES: /usr/share/metasploit-framework/data/wordlists/directory.txt

-----------------

GENERATED WORDS: 24                                                            

---- Scanning URL: http://192.23.62.3/ ----
+ http://192.23.62.3//data (CODE:301|SIZE:309)                                                                                                                                            
+ http://192.23.62.3//dir (CODE:301|SIZE:308)                                                                                                                                             
                                                                                                                                                                                          
-----------------
END_TIME: Thu Aug 25 04:01:37 2022
DOWNLOADED: 24 - FOUND: 2
```

Which bot is specifically banned from accessing a specific directory?

`BadBot`

[HTTP Robots.txt Content Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/http/robots_txt/)

检测 robots.txt 文件并分析其内容

```
msf5 > use auxiliary/scanner/http/robots_txt
msf5 auxiliary(scanner/http/robots_txt) > show options

Module options (auxiliary/scanner/http/robots_txt):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   PATH     /                yes       The test path to find robots.txt file
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                    yes       The target address range or CIDR identifier
   RPORT    80               yes       The target port (TCP)
   SSL      false            no        Negotiate SSL/TLS for outgoing connections
   THREADS  1                yes       The number of concurrent threads
   VHOST                     no        HTTP server virtual host

msf5 auxiliary(scanner/http/robots_txt) > set RHOSTS 192.23.62.3
RHOSTS => 192.23.62.3
msf5 auxiliary(scanner/http/robots_txt) > run

[*] [192.23.62.3] /robots.txt found
[+] Contents of Robots.txt:
User-agent: *
Disallow: /cgi-bin/
Disallow: Disallow: /junk/

User-agent: BadBot
Disallow: /no-badbot-dir/

[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
root@attackdefense:~# curl http://192.23.62.3/cgi-bin/
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>403 Forbidden</title>
</head><body>
<h1>Forbidden</h1>
<p>You don't have permission to access /cgi-bin/
on this server.<br />
</p>
<hr>
<address>Apache/2.4.18 (Ubuntu) Server at 192.23.62.3 Port 80</address>
</body></html>
```

## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-538.pdf

[Apache Web Server](https://httpd.apache.org/)

[Browsh](https://www.brow.sh/)

[Lynx](https://lynx.invisible-island.net/)

[Metasploit Module: HTTP Version Detection](https://www.rapid7.com/db/modules/auxiliary/scanner/http/http_version/)

[HTTP Directory Brute Force Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/http/brute_dirs/)

[HTTP Robots.txt Content Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/http/robots_txt/)