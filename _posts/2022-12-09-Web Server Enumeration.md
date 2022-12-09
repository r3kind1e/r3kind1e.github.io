---
layout: post
title: "Web Server Enumeration"
date: "2022-12-09"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Web Server Enumeration
A web server is software that is used to serve website data on the web.

Web servers utilize HTTP (Hypertext Transfer Protocol) to facilitate the communication between clients and the web server.

HTTP is an application layer protocol that utilizes TCP port 80 for communication.

We can utilize auxiliary modules to enumerate the web server version, HTTP headers, brute-force directories and much more.

Examples of popular web servers are; Apache, Nginx and Microsoft IIS.

# Web 服务器枚举
Web 服务器是用于在 Web 上提供网站数据的软件。

Web 服务器利用 HTTP（超文本传输协议）来促进客户端和 Web 服务器之间的通信。

HTTP 是一种应用层协议，它利用 TCP 端口 80 进行通信。

我们可以利用辅助模块来枚举 Web 服务器版本、HTTP 标头、暴力破解目录等等。

流行的网络服务器的例子是; Apache、Nginx 和微软 IIS。

# Demo: Web Server Enumeration（演示：Web 服务器枚举）
```
ifconfig
eth1: 192.140.160.2
```

```
service postgresql start
msfconsole -q
```

```
workspace -a Web_Enum
```

Set up a few global variables.

```
setg RHOSTS 192.140.160.3
setg RHOST 192.140.160.3
```

```
search type:auxiliary name:http
```

Print out the version of the web server.

[HTTP Version Detection](https://www.rapid7.com/db/modules/auxiliary/scanner/http/http_version/)

HTTP 版本检测

显示每个系统的版本信息。

```
use auxiliary/scanner/http/http_version
show options
run
```

[HTTP Header Detection](https://www.rapid7.com/db/modules/auxiliary/scanner/http/http_header/)

HTTP 标头检测

此模块显示扫描系统返回的 HTTP 标头。

```
search http_header
use auxiliary/scanner/http/http_header
run
```

The robots.txt file is a TXT file that stored at the root of a web server and is used to prevent search engines from indexing specific directories and files that are hosted on that web server. For example, if I had a website and I wanted to prevent Google and any other search engines from indexing a specific directory within that web server or within my website, I would add it to the robots.txt file. This is important because the developers of website may add directories there that they don't want anyone else to find or they don't want search engines to index.

[HTTP Robots.txt Content Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/http/robots_txt/)

HTTP Robots.txt 内容扫描程序

检测 robots.txt 文件并分析其内容

```
search robots_txt
use auxiliary/scanner/http/robots_txt
show options
run
```

```
curl http://192.140.160.3/data/
```

This directory has directory listing enabled. Now, directory listing is a configuration that is native to Apache as well as a few other web servers. It allows you to store files within a directory. And they can be indexed and accessed within that directory. You can have a list of files. And directory listing is typically used for download pages. So if you ever want to host files for download to the public, you typically have them in a directory, and then enable directory listing. We know that directory listing is enabled for the data folder.

```
curl http://192.140.160.3/secure/
```

[HTTP Directory Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/http/dir_scanner/)

HTTP 目录扫描器

该模块识别给定目录路径中是否存在感兴趣的目录。

```
search dir_scanner
use auxiliary/scanner/http/dir_scanner
run 
```

Perform file brute forcing. And file brute forcing is the opposite of directory brute forcing in that instead of performing a brute force to find directories, we're performing a brute force to find files.

`EXT`: We can specify the extensions that we want to limit our results to. So if we only wanted to perform a file brute forcing for PHP files, we can specify the extension there.

[HTTP Interesting File Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/http/files_dir/)

HTTP 有趣的文件扫描器

该模块识别给定目录路径中是否存在感兴趣的文件。

```
search files_dir
use auxiliary/scanner/http/files_dir
show options
run
```

[HTTP Login Utility](https://www.rapid7.com/db/modules/auxiliary/scanner/http/http_login/)

HTTP 登录实用程序

此模块尝试对 HTTP 服务进行身份验证。

```
search http_login
use auxiliary/scanner/http/http_login
show options
set AUTH_URI /secure/
```

Unset the `USERPASS_FILE`. And the reason we don't need it is because we already have a `USER_FILE` specified, and a `PASS_FILE` specified. 

```
unset USERPASS_FILE
```

```
show options
run
set USER_FILE /usr/share/metasploit-framework/data/wordlists/namelist.txt
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
run
set VERBOSE false
run
```

Enumerate users. Identify specific usernames that will help us narrow down the brute force.

[Apache "mod_userdir" User Enumeration](https://www.rapid7.com/db/modules/auxiliary/scanner/http/apache_userdir_enum/)

Apache“mod_userdir”用户枚举

启用了 UserDir 指令的 Apache 在用户名存在且不存在 public_html 目录以及用户名不存在时生成不同的错误代码，这可能允许远程攻击者确定服务器上的有效用户名。

```
search apache_userdir_enum
use auxiliary/scanner/http/apache_userdir_enum
show option
info
```

This will help up identify valid users on the server.

```
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
run
search http_login
use auxiliary/scanner/http/http_login
echo "rooty" > user.txt
set USER_FILE /root/user.txt
run
set VERBOSE true
run
```

# Apache Enumeration
## Overview
In this lab, run the following auxiliary modules again the target:
* auxiliary/scanner/http/apache_userdir_enum
* auxiliary/scanner/http/brute_dirs
* auxiliary/scanner/http/dir_scanner
* auxiliary/scanner/http/dir_listing
* auxiliary/scanner/http/http_put
* auxiliary/scanner/http/files_dir
* auxiliary/scanner/http/http_login
* auxiliary/scanner/http/http_header
* auxiliary/scanner/http/http_version
* auxiliary/scanner/http/robots_txt

Instructions: 
* This lab is dedicated to you! No other users are on this network :)
* Once you start the lab, you will have access to a root terminal of a Kali instance
* Your Kali has an interface with IP address 192.X.Y.2. Run "ip addr" to know the values of X and Y.
* The target server should be located at the IP address 192.X.Y.3.
* Do not attack the gateway located at IP address 192.X.Y.1
* postgresql is not running by default so Metasploit may give you an error about this when starting

## Solutions
The solution for this lab can be found in the following manual:  https://assets.ine.com/labs/ad-manuals/walkthrough-118.pdf

## 我自己的解决方案
[Apache "mod_userdir" User Enumeration](https://www.rapid7.com/db/modules/auxiliary/scanner/http/apache_userdir_enum/)

Apache“mod_userdir”用户枚举

启用了 UserDir 指令的 Apache 在用户名存在且不存在 public_html 目录以及用户名不存在时生成不同的错误代码，这可能允许远程攻击者确定服务器上的有效用户名。

[HTTP Directory Brute Force Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/http/brute_dirs/)

HTTP 目录暴力扫描器

该模块通过在给定目录路径中强制名称来识别有趣目录的存在。

[HTTP Directory Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/http/dir_scanner/)

HTTP 目录扫描器

该模块识别给定目录路径中是否存在感兴趣的目录。

[HTTP Directory Listing Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/http/dir_listing/)

HTTP 目录列表扫描器

该模块识别给定目录路径中的目录列表漏洞。

[HTTP Writable Path PUT/DELETE File Access](https://www.rapid7.com/db/modules/auxiliary/scanner/http/http_put/)

HTTP 可写路径 PUT/DELETE 文件访问

该模块可以滥用配置错误的 Web 服务器，通过 PUT 和 DELETE HTTP 请求上传和删除 Web 内容。将 ACTION 设置为 PUT 或 DELETE。PUT 是默认值。如果未指定文件名，模块将为您生成一个随机字符串作为 .txt 文件。如果使用 DELETE，则需要文件名。

[HTTP Interesting File Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/http/files_dir/)

HTTP 有趣的文件扫描程序

该模块识别给定目录路径中是否存在感兴趣的文件。

[HTTP Login Utility](https://www.rapid7.com/db/modules/auxiliary/scanner/http/http_login/)

HTTP 登录实用程序

此模块尝试对 HTTP 服务进行身份验证。

[HTTP Header Detection](https://www.rapid7.com/db/modules/auxiliary/scanner/http/http_header/)

HTTP 标头检测

此模块显示扫描系统返回的 HTTP 标头。

[HTTP Version Detection](https://www.rapid7.com/db/modules/auxiliary/scanner/http/http_version/)

HTTP 版本检测

显示每个系统的版本信息。

[HTTP Robots.txt Content Scanner](https://www.rapid7.com/db/modules/auxiliary/scanner/http/robots_txt/)

HTTP Robots.txt 内容扫描程序

检测 robots.txt 文件并分析其内容

```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.148.30.2  netmask 255.255.255.0  broadcast 192.148.30.255
        ether 02:42:c0:94:1e:02  txqueuelen 0  (Ethernet)
```

```
msf5 > setg RHOSTS 192.148.30.3
RHOSTS => 192.148.30.3
msf5 > setg RHOST 192.148.30.3
RHOST => 192.148.30.3
msf5 > workspace -a Web_Enum
[*] Added workspace: Web_Enum
[*] Workspace: Web_Enum
msf5 > workspace
  default
* Web_Enum
```

```
msf5 > use auxiliary/scanner/http/http_version
msf5 auxiliary(scanner/http/http_version) > run

[+] 192.148.30.3:80 Apache/2.4.18 (Ubuntu)
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/http/http_version) > use auxiliary/scanner/http/http_header
msf5 auxiliary(scanner/http/http_header) > show options

Module options (auxiliary/scanner/http/http_header):

   Name         Current Setting                                                        Required  Description
   ----         ---------------                                                        --------  -----------
   HTTP_METHOD  HEAD                                                                   yes       HTTP Method to use, HEAD or GET (Accepted: GET, HEAD)
   IGN_HEADER   Vary,Date,Content-Length,Connection,Etag,Expires,Pragma,Accept-Ranges  yes       List of headers to ignore, seperated by comma
   Proxies                                                                             no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS       192.148.30.3                                                           yes       The target address range or CIDR identifier
   RPORT        80                                                                     yes       The target port (TCP)
   SSL          false                                                                  no        Negotiate SSL/TLS for outgoing connections
   TARGETURI    /                                                                      yes       The URI to use
   THREADS      1                                                                      yes       The number of concurrent threads
   VHOST                                                                               no        HTTP server virtual host

msf5 auxiliary(scanner/http/http_header) > run

[+] 192.148.30.3:80      : CONTENT-TYPE: text/html
[+] 192.148.30.3:80      : LAST-MODIFIED: Wed, 27 Feb 2019 04:21:01 GMT
[+] 192.148.30.3:80      : SERVER: Apache/2.4.18 (Ubuntu)
[+] 192.148.30.3:80      : detected 3 headers
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf5 auxiliary(scanner/http/http_header) > set TARGETURI /secure
TARGETURI => /secure
msf5 auxiliary(scanner/http/http_header) > run

[+] 192.36.13.3:80       : CONTENT-TYPE: text/html; charset=iso-8859-1
[+] 192.36.13.3:80       : SERVER: Apache/2.4.18 (Ubuntu)
[+] 192.36.13.3:80       : WWW-AUTHENTICATE: Basic realm="Restricted Content"
[+] 192.36.13.3:80       : detected 3 headers
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/http/http_header) > use auxiliary/scanner/http/robots_txt
msf5 auxiliary(scanner/http/robots_txt) > run

[*] [192.148.30.3] /robots.txt found
[+] Contents of Robots.txt:
# robots.txt for attackdefense 
User-agent: test                     
# Directories
Allow: /webmail

User-agent: *
# Directories
Disallow: /data
Disallow: /secure

[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

访问`http://192.148.30.3/secure/`，需要进行身份验证。

```
msf5 auxiliary(scanner/http/robots_txt) > curl http://192.148.30.3/secure/
[*] exec: curl http://192.148.30.3/secure/

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   459  100   459    0     0   224k      0 --:--:-- --:--:-- --:--:--  224k
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>401 Unauthorized</title>
</head><body>
<h1>Unauthorized</h1>
<p>This server could not verify that you
are authorized to access the document
requested.  Either you supplied the wrong
credentials (e.g., bad password), or your
browser doesn't understand how to supply
the credentials required.</p>
<hr>
<address>Apache/2.4.18 (Ubuntu) Server at 192.148.30.3 Port 80</address>
</body></html>
```

```
msf5 auxiliary(scanner/http/dir_scanner) > use auxiliary/scanner/http/brute_dirs
msf5 auxiliary(scanner/http/brute_dirs) > run

[*] Using code '404' as not found.
[+] Found http://192.148.30.3:80/doc/ 200
[+] Found http://192.148.30.3:80/pro/ 200
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/http/brute_dirs) > use auxiliary/scanner/http/dir_scanner
msf5 auxiliary(scanner/http/dir_scanner) > show options

Module options (auxiliary/scanner/http/dir_scanner):

   Name        Current Setting                                          Required  Description
   ----        ---------------                                          --------  -----------
   DICTIONARY  /usr/share/metasploit-framework/data/wmap/wmap_dirs.txt  no        Path of word dictionary to use
   PATH        /                                                        yes       The path  to identify files
   Proxies                                                              no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS      192.148.30.3                                             yes       The target address range or CIDR identifier
   RPORT       80                                                       yes       The target port (TCP)
   SSL         false                                                    no        Negotiate SSL/TLS for outgoing connections
   THREADS     1                                                        yes       The number of concurrent threads
   VHOST                                                                no        HTTP server virtual host

msf5 auxiliary(scanner/http/dir_scanner) > run

[*] Detecting error code
[*] Using code '404' as not found for 192.148.30.3
[+] Found http://192.148.30.3:80/cgi-bin/ 404 (192.148.30.3)
[+] Found http://192.148.30.3:80/data/ 404 (192.148.30.3)
[+] Found http://192.148.30.3:80/downloads/ 404 (192.148.30.3)
[+] Found http://192.148.30.3:80/doc/ 404 (192.148.30.3)
[+] Found http://192.148.30.3:80/icons/ 404 (192.148.30.3)
[+] Found http://192.148.30.3:80/manual/ 404 (192.148.30.3)
[+] Found http://192.148.30.3:80/secure/ 404 (192.148.30.3)
[+] Found http://192.148.30.3:80/users/ 404 (192.148.30.3)
[+] Found http://192.148.30.3:80/uploads/ 404 (192.148.30.3)
[+] Found http://192.148.30.3:80/web_app/ 200 (192.148.30.3)
[+] Found http://192.148.30.3:80/webadmin/ 200 (192.148.30.3)
[+] Found http://192.148.30.3:80/view/ 200 (192.148.30.3)
[+] Found http://192.148.30.3:80/webdav/ 404 (192.148.30.3)
[+] Found http://192.148.30.3:80/webmail/ 404 (192.148.30.3)
[+] Found http://192.148.30.3:80/webdb/ 404 (192.148.30.3)
[+] Found http://192.148.30.3:80/~nobody/ 404 (192.148.30.3)
[+] Found http://192.148.30.3:80/~admin/ 404 (192.148.30.3)
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/http/dir_scanner) > curl http://192.148.30.3:80/icons/
[*] exec: curl http://192.148.30.3:80/icons/

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   293  100   293    0     0   286k      0 --:--:-- --:--:-- --:--:--  286k
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>403 Forbidden</title>
</head><body>
<h1>Forbidden</h1>
<p>You don't have permission to access /icons/
on this server.<br />
</p>
<hr>
<address>Apache/2.4.18 (Ubuntu) Server at 192.148.30.3 Port 80</address>
</body></html>
```

```
msf5 auxiliary(scanner/http/dir_scanner) > curl http://192.148.30.3:80/secure/
[*] exec: curl http://192.148.30.3:80/secure/

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   459  100   459    0     0   448k      0 --:--:-- --:--:-- --:--:--  448k
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>401 Unauthorized</title>
</head><body>
<h1>Unauthorized</h1>
<p>This server could not verify that you
are authorized to access the document
requested.  Either you supplied the wrong
credentials (e.g., bad password), or your
browser doesn't understand how to supply
the credentials required.</p>
<hr>
<address>Apache/2.4.18 (Ubuntu) Server at 192.148.30.3 Port 80</address>
</body></html>
```

```
msf5 auxiliary(scanner/http/dir_scanner) > curl http://192.148.30.3:80/webdav/
[*] exec: curl http://192.148.30.3:80/webdav/

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   459  100   459    0     0   448k      0 --:--:-- --:--:-- --:--:--  448k
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>401 Unauthorized</title>
</head><body>
<h1>Unauthorized</h1>
<p>This server could not verify that you
are authorized to access the document
requested.  Either you supplied the wrong
credentials (e.g., bad password), or your
browser doesn't understand how to supply
the credentials required.</p>
<hr>
<address>Apache/2.4.18 (Ubuntu) Server at 192.148.30.3 Port 80</address>
</body></html>
```

```
msf5 auxiliary(scanner/http/dir_scanner) > curl http://192.148.30.3:80/~nobody/
[*] exec: curl http://192.148.30.3:80/~nobody/

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   295  100   295    0     0   288k      0 --:--:-- --:--:-- --:--:--  288k
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>403 Forbidden</title>
</head><body>
<h1>Forbidden</h1>
<p>You don't have permission to access /~nobody/
on this server.<br />
</p>
<hr>
<address>Apache/2.4.18 (Ubuntu) Server at 192.148.30.3 Port 80</address>
</body></html>
```

```
msf5 auxiliary(scanner/http/dir_scanner) > curl http://192.148.30.3:80/~admin/
[*] exec: curl http://192.148.30.3:80/~admin/

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   294  100   294    0     0   287k      0 --:--:-- --:--:-- --:--:--  287k
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>403 Forbidden</title>
</head><body>
<h1>Forbidden</h1>
<p>You don't have permission to access /~admin/
on this server.<br />
</p>
<hr>
<address>Apache/2.4.18 (Ubuntu) Server at 192.148.30.3 Port 80</address>
</body></html>
```

测试该目录是否允许列目录。

```
msf5 auxiliary(scanner/http/dir_scanner) > use auxiliary/scanner/http/dir_listing
msf5 auxiliary(scanner/http/dir_listing) > run

[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf5 auxiliary(scanner/http/dir_listing) > set PATH /data
PATH => /data
msf5 auxiliary(scanner/http/dir_listing) > run

[+] Found Directory Listing http://192.148.30.3:80/data/
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf5 auxiliary(scanner/http/dir_listing) > set PATH /secure
PATH => /secure
msf5 auxiliary(scanner/http/dir_listing) > run

[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf5 auxiliary(scanner/http/dir_listing) > set PATH /webadmin
PATH => /webadmin
msf5 auxiliary(scanner/http/dir_listing) > run

[+] Found Directory Listing http://192.148.30.3:80/webadmin/
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/http/dir_listing) > use auxiliary/scanner/http/files_dir
msf5 auxiliary(scanner/http/files_dir) > show options

Module options (auxiliary/scanner/http/files_dir):

   Name        Current Setting                                           Required  Description
   ----        ---------------                                           --------  -----------
   DICTIONARY  /usr/share/metasploit-framework/data/wmap/wmap_files.txt  no        Path of word dictionary to use
   EXT                                                                   no        Append file extension to use
   PATH        /                                                         yes       The path  to identify files
   Proxies                                                               no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS      192.148.30.3                                              yes       The target address range or CIDR identifier
   RPORT       80                                                        yes       The target port (TCP)
   SSL         false                                                     no        Negotiate SSL/TLS for outgoing connections
   THREADS     1                                                         yes       The number of concurrent threads
   VHOST                                                                 no        HTTP server virtual host

msf5 auxiliary(scanner/http/files_dir) > run

[*] Using code '404' as not found for files with extension .null
[*] Using code '404' as not found for files with extension .backup
[+] Found http://192.148.30.3:80/file.backup 200
[*] Using code '404' as not found for files with extension .bak
[*] Using code '404' as not found for files with extension .c
[+] Found http://192.148.30.3:80/code.c 200
[*] Using code '404' as not found for files with extension .cfg
[+] Found http://192.148.30.3:80/code.cfg 200
[*] Using code '404' as not found for files with extension .class
[*] Using code '404' as not found for files with extension .copy
[*] Using code '404' as not found for files with extension .conf
[*] Using code '404' as not found for files with extension .exe
[*] Using code '404' as not found for files with extension .html
[+] Found http://192.148.30.3:80/index.html 200
[*] Using code '404' as not found for files with extension .htm
[*] Using code '404' as not found for files with extension .ini
[*] Using code '404' as not found for files with extension .log
[*] Using code '404' as not found for files with extension .old
[*] Using code '404' as not found for files with extension .orig
[*] Using code '404' as not found for files with extension .php
[+] Found http://192.148.30.3:80/test.php 200
[*] Using code '404' as not found for files with extension .tar
[*] Using code '404' as not found for files with extension .tar.gz
[*] Using code '404' as not found for files with extension .tgz
[*] Using code '404' as not found for files with extension .tmp
[*] Using code '404' as not found for files with extension .temp
[*] Using code '404' as not found for files with extension .txt
[*] Using code '404' as not found for files with extension .zip
[*] Using code '404' as not found for files with extension ~
[*] Using code '404' as not found for files with extension 
[+] Found http://192.148.30.3:80/cgi-bin 301
[+] Found http://192.148.30.3:80/data 301
[+] Found http://192.148.30.3:80/doc 301
[+] Found http://192.148.30.3:80/downloads 301
[+] Found http://192.148.30.3:80/manual 301
[+] Found http://192.148.30.3:80/secure 401
[+] Found http://192.148.30.3:80/uploads 301
[+] Found http://192.148.30.3:80/users 301
[+] Found http://192.148.30.3:80/view 301
[+] Found http://192.148.30.3:80/webadmin 301
[+] Found http://192.148.30.3:80/webdav 401
[+] Found http://192.148.30.3:80/webmail 301
[+] Found http://192.148.30.3:80/~admin 403
[+] Found http://192.148.30.3:80/~bin 403
[+] Found http://192.148.30.3:80/~mail 403
[+] Found http://192.148.30.3:80/~sys 403
[*] Using code '404' as not found for files with extension 
[+] Found http://192.148.30.3:80/cgi-bin 301
[+] Found http://192.148.30.3:80/data 301
[+] Found http://192.148.30.3:80/downloads 301
[+] Found http://192.148.30.3:80/doc 301
[+] Found http://192.148.30.3:80/manual 301
[+] Found http://192.148.30.3:80/secure 401
[+] Found http://192.148.30.3:80/users 301
[+] Found http://192.148.30.3:80/uploads 301
[+] Found http://192.148.30.3:80/webdav 401
[+] Found http://192.148.30.3:80/webadmin 301
[+] Found http://192.148.30.3:80/view 301
[+] Found http://192.148.30.3:80/webmail 301
[+] Found http://192.148.30.3:80/~admin 403
[+] Found http://192.148.30.3:80/~mail 403
[+] Found http://192.148.30.3:80/~bin 403
[+] Found http://192.148.30.3:80/~sys 403
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

`/data`目录允许使用PUT上传文件，也允许使用DELETE删除文件。

```
msf5 auxiliary(scanner/http/files_dir) > use auxiliary/scanner/http/http_put
msf5 auxiliary(scanner/http/http_put) > show options

Module options (auxiliary/scanner/http/http_put):

   Name      Current Setting        Required  Description
   ----      ---------------        --------  -----------
   ACTION    PUT                    yes       PUT or DELETE
   FILEDATA  msf test file          no        The data to upload into the file
   FILENAME  msf_http_put_test.txt  yes       The file to attempt to write or delete
   PATH      /                      yes       The path to attempt to write or delete
   Proxies                          no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS    192.148.30.3           yes       The target address range or CIDR identifier
   RPORT     80                     yes       The target port (TCP)
   SSL       false                  no        Negotiate SSL/TLS for outgoing connections
   THREADS   1                      yes       The number of concurrent threads
   VHOST                            no        HTTP server virtual host


Auxiliary action:

   Name  Description
   ----  -----------
   PUT   


msf5 auxiliary(scanner/http/http_put) > run

[-] 192.148.30.3: File doesn't seem to exist. The upload probably failed
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf5 auxiliary(scanner/http/http_put) > set PATH /data
PATH => /data
msf5 auxiliary(scanner/http/http_put) > run

[+] File uploaded: http://192.148.30.3:80/data/msf_http_put_test.txt
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf5 auxiliary(scanner/http/http_put) > set PATH /data
PATH => /data
msf5 auxiliary(scanner/http/http_put) > set ACTION DELETE
ACTION => DELETE
msf5 auxiliary(scanner/http/http_put) > run

[+] File deleted: http://192.148.30.3:80/data/msf_http_put_test.txt
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf5 auxiliary(scanner/http/http_put) > use auxiliary/scanner/http/apache_userdir_enum
msf5 auxiliary(scanner/http/apache_userdir_enum) > show options

Module options (auxiliary/scanner/http/apache_userdir_enum):

   Name              Current Setting                                                Required  Description
   ----              ---------------                                                --------  -----------
   BRUTEFORCE_SPEED  5                                                              yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS      false                                                          no        Try each user/password couple stored in the current database
   DB_ALL_PASS       false                                                          no        Add all passwords in the current database to the list
   DB_ALL_USERS      false                                                          no        Add all users in the current database to the list
   Proxies                                                                          no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS            192.148.30.3                                                   yes       The target address range or CIDR identifier
   RPORT             80                                                             yes       The target port (TCP)
   SSL               false                                                          no        Negotiate SSL/TLS for outgoing connections
   TARGETURI         /                                                              yes       The path to users Home Page
   THREADS           1                                                              yes       The number of concurrent threads
   USERNAME                                                                         no        A specific username to authenticate as
   USER_FILE         /usr/share/metasploit-framework/data/wordlists/unix_users.txt  yes       File containing users, one per line
   VERBOSE           true                                                           yes       Whether to print output for all attempts
   VHOST                                                                            no        HTTP server virtual host
msf5 auxiliary(scanner/http/apache_userdir_enum) > set verbose false
verbose => false
msf5 auxiliary(scanner/http/apache_userdir_enum) > run

[+] http://192.148.30.3/ - Apache UserDir: 'admin' found 
[+] http://192.148.30.3/ - Apache UserDir: 'backup' found 
[+] http://192.148.30.3/ - Apache UserDir: 'bin' found 
[+] http://192.148.30.3/ - Apache UserDir: 'daemon' found 
[+] http://192.148.30.3/ - Apache UserDir: 'dbadmin' found 
[+] http://192.148.30.3/ - Apache UserDir: 'games' found 
[+] http://192.148.30.3/ - Apache UserDir: 'gnats' found 
[+] http://192.148.30.3/ - Apache UserDir: 'irc' found 
[+] http://192.148.30.3/ - Apache UserDir: 'list' found 
[+] http://192.148.30.3/ - Apache UserDir: 'lp' found 
[+] http://192.148.30.3/ - Apache UserDir: 'mail' found 
[+] http://192.148.30.3/ - Apache UserDir: 'man' found 
[+] http://192.148.30.3/ - Apache UserDir: 'news' found 
[+] http://192.148.30.3/ - Apache UserDir: 'nobody' found 
[+] http://192.148.30.3/ - Apache UserDir: 'proxy' found 
[+] http://192.148.30.3/ - Apache UserDir: 'rooty' found 
[+] http://192.148.30.3/ - Apache UserDir: 'sync' found 
[+] http://192.148.30.3/ - Apache UserDir: 'sys' found 
[+] http://192.148.30.3/ - Apache UserDir: 'ultra' found 
[+] http://192.148.30.3/ - Apache UserDir: 'uucp' found 
[+] http://192.148.30.3/ - Users found: admin, backup, bin, daemon, dbadmin, games, gnats, irc, list, lp, mail, man, news, nobody, proxy, rooty, sync, sys, ultra, uucp
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
root@attackdefense:~# pwd
/root
root@attackdefense:~# vim users.txt
root@attackdefense:~# cat users.txt 
admin
backup
bin
daemon
dbadmin
games
gnats
irc
list
lp
mail
man
news
nobody
proxy
rooty
sync
sys
ultra
uucp
```

```
msf5 > use auxiliary/scanner/http/http_login
msf5 auxiliary(scanner/http/http_login) > show options

Module options (auxiliary/scanner/http/http_login):

   Name              Current Setting                                                           Required  Description
   ----              ---------------                                                           --------  -----------
   AUTH_URI                                                                                    no        The URI to authenticate against (default:auto)
   BLANK_PASSWORDS   false                                                                     no        Try blank passwords for all users
   BRUTEFORCE_SPEED  5                                                                         yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS      false                                                                     no        Try each user/password couple stored in the current database
   DB_ALL_PASS       false                                                                     no        Add all passwords in the current database to the list
   DB_ALL_USERS      false                                                                     no        Add all users in the current database to the list
   PASS_FILE         /usr/share/metasploit-framework/data/wordlists/http_default_pass.txt      no        File containing passwords, one per line
   Proxies                                                                                     no        A proxy chain of format type:host:port[,type:host:port][...]
   REQUESTTYPE       GET                                                                       no        Use HTTP-GET or HTTP-PUT for Digest-Auth, PROPFIND for WebDAV (default:GET)
   RHOSTS                                                                                      yes       The target address range or CIDR identifier
   RPORT             80                                                                        yes       The target port (TCP)
   SSL               false                                                                     no        Negotiate SSL/TLS for outgoing connections
   STOP_ON_SUCCESS   false                                                                     yes       Stop guessing when a credential works for a host
   THREADS           1                                                                         yes       The number of concurrent threads
   USERPASS_FILE     /usr/share/metasploit-framework/data/wordlists/http_default_userpass.txt  no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS      false                                                                     no        Try the username as the password for all users
   USER_FILE         /usr/share/metasploit-framework/data/wordlists/http_default_users.txt     no        File containing users, one per line
   VERBOSE           true                                                                      yes       Whether to print output for all attempts
   VHOST                                                                                       no        HTTP server virtual host

msf5 auxiliary(scanner/http/http_login) > set AUTH_URI /secure
AUTH_URI => /secure
msf5 auxiliary(scanner/http/http_login) > set RHOSTS 192.148.30.3
RHOSTS => 192.148.30.3
msf5 auxiliary(scanner/http/http_login) > unset USERPASS_FILE
Unsetting USERPASS_FILE...
msf5 auxiliary(scanner/http/http_login) > set USER_FILE /root/users.txt
USER_FILE => /root/users.txt
msf5 auxiliary(scanner/http/http_login) > set VERBOSE false
VERBOSE => false
msf5 auxiliary(scanner/http/http_login) > run

[*] Attempting to login to http://192.148.30.3:80/secure
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf5 auxiliary(scanner/http/http_login) > set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
PASS_FILE => /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
msf5 auxiliary(scanner/http/http_login) > run
[*] Attempting to login to http://192.148.30.3:80/secure
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```





