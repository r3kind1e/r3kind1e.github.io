---
layout: post
title: "Dirbuster and Dirb"
date: "2022-06-27"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Web Attacks
---

# Dirbuster
`Applications->Kali Linux->Web Applications->Web Crawlers->dirbuster`

Target URL: `http://dirbuster.site`

List based brute force，点击Browse，选择`/usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-small.txt`

File extension: `bak,old`

`Options->Advanced Options->Scan Options` 取消勾选 `Limit number of requests per second`

Results - List View，点击Type进行排序，查看File。

看见一个名为`/users.bak`的文件，响应码200，这说明资源存在且可访问。右键点击该文件，选择`View Response`。

另一个有趣的文件是`/include/config.old`，右键点击，选择`View Response`，我们可以看见一些数据库连接信息。

一旦Dirbuster完成扫描，或者我们点击`Stop`，我们可以点击`Report`，将结果保存为不同的形式。

选择保存的路径：`/root/Desktop`，点击`Generate Report`。这里，我们可以看到Dirbuster发现的文件和目录的列表。

# Dirb
```
dirb https://www.bilibili.com/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
```

```
cd /usr/share/dirb/wordlists
                                                                                                                                                                                             
ls -la
total 268
drwxr-xr-x 5 root root   4096 May 12 11:14 .
drwxr-xr-x 3 root root   4096 May 12 11:14 ..
-rw-r--r-- 1 root root 184073 Jan 24  2012 big.txt
-rw-r--r-- 1 root root   1292 Jan 27  2012 catala.txt
-rw-r--r-- 1 root root  35849 Nov 17  2014 common.txt
-rw-r--r-- 1 root root   1492 May 23  2012 euskera.txt
-rw-r--r-- 1 root root    142 Dec 29  2005 extensions_common.txt
-rw-r--r-- 1 root root     75 Mar 16  2012 indexes.txt
-rw-r--r-- 1 root root    244 Dec 29  2005 mutations_common.txt
drwxr-xr-x 2 root root   4096 May 12 11:14 others
-rw-r--r-- 1 root root   6561 Mar  4  2014 small.txt
-rw-r--r-- 1 root root   3731 Nov 12  2014 spanish.txt
drwxr-xr-x 2 root root   4096 May 12 11:14 stress
drwxr-xr-x 2 root root   4096 May 12 11:14 vulns
```

```
dirb https://www.bilibili.com/ /usr/share/dirb/wordlists/small.txt
```

可以搜索以下关键词
```
github wordlist discovery
```

```
dirb https://www.zhihu.com/ /usr/share/dirb/wordlists/big.txt
```

```
======================== OPTIONS ========================
 -a <agent_string> : Specify your custom USER_AGENT.
 -b : Use path as is.
 -c <cookie_string> : Set a cookie for the HTTP request.
 -E <certificate> : path to the client certificate.
 -f : Fine tunning of NOT_FOUND (404) detection.
 -H <header_string> : Add a custom header to the HTTP request.
 -i : Use case-insensitive search.
 -l : Print "Location" header when found.
 -N <nf_code>: Ignore responses with this HTTP code.
 -o <output_file> : Save output to disk.
 -p <proxy[:port]> : Use this proxy. (Default port is 1080)
 -P <proxy_username:proxy_password> : Proxy Authentication.
 -r : Don't search recursively.
 -R : Interactive recursion. (Asks for each directory)
 -S : Silent Mode. Don't show tested words. (For dumb terminals)
 -t : Don't force an ending '/' on URLs.
 -u <username:password> : HTTP Authentication.
 -v : Show also NOT_FOUND pages.
 -w : Don't stop on WARNING messages.
 -X <extensions> / -x <exts_file> : Append each word with this extensions.
 -z <millisecs> : Add a milliseconds delay to not cause excessive Flood.

======================== 选项========================
 -a <agent_string> ：指定您的自定义 USER_AGENT。
 -b ：按原样使用路径。
 -c <cookie_string> ：为 HTTP 请求设置一个 cookie。
 -E <certificate> ：客户端证书的路径。
 -f ：微调 NOT_FOUND (404) 检测。
 -H <header_string> ：向 HTTP 请求添加自定义标头。
 -i ：使用不区分大小写的搜索。
 -l ：找到时打印“Location”标头。
 -N <nf_code>：忽略带有此 HTTP 代码的响应。
 -o <output_file> ：将输出保存到磁盘。
 -p <proxy[:port]> ：使用这个代理。 （默认端口为 1080）
 -P <proxy_username:proxy_password> ：代理身份验证。
 -r ：不要递归搜索。
 -R：交互式递归。 （要求每个目录）
 -S：静音模式。不要显示经过测试的单词。 （对于哑终端）
 -t ：不要在 URL 上强制使用结尾“/”。
 -u <username:password>：HTTP 身份验证。
 -v ：也显示 NOT_FOUND 页面。
 -w ：不要停止警告消息。
 -X <extensions> / -x <exts_file> ：用这个扩展名附加每个单词。
 -z <millisecs> ：添加毫秒延迟，以免造成过多的洪水。
```

`-a <agent_string>` ：指定您的自定义 USER_AGENT。

可以从[User Agent String](http://useragentstring.com/)获取USER_AGENT。
```
http://useragentstring.com/
http://useragentstring.com/pages/useragentstring.php

Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:101.0) Gecko/20100101 Firefox/101.0

dirb https://www.bilibili.com/ -a "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:101.0) Gecko/20100101 Firefox/101.0"
```

在Burp Suite的`Proxy->Options`中，可以看到Proxy Listener是`127.0.0.1:8080`。

`-p <proxy[:port]>` ：使用这个代理。 （默认端口为 1080）

```
dirb https://www.bilibili.com/ -p http://127.0.0.1:8080
```

`-c <cookie_string>` ：为 HTTP 请求设置一个 cookie。
```
dirb https://www.bilibili.com/ -p http://127.0.0.1:8080 -c "COOKIE:XYZ"
```

在`Proxy->HTTP history`中可以看到
```
GET /11 HTTP/2
Host: www.bilibili.com
Cookie: COOKIE:XYZ
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)
Accept: */*
```

`-u <username:password>`：HTTP 身份验证。
```
dirb https://www.bilibili.com/ -p http://127.0.0.1:8080 -u "admin:password"
```
在`Proxy->HTTP history`中可以看到

```
GET /3rdparty HTTP/2
Host: www.bilibili.com
Authorization: Basic YWRtaW46cGFzc3dvcmQ=
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)
Accept: */*
```
在`Decoder`中，将"YWRtaW46cGFzc3dvcmQ=" Decode as `Base64`。


`-H <header_string>` ：向 HTTP 请求添加自定义标头。

```
dirb https://www.bilibili.com/ -p http://127.0.0.1:8080 -H "MyHeader: MyContent"
```

在`Proxy->HTTP history`中可以看到

```
GET /11 HTTP/2
Host: www.bilibili.com
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)
Accept: */*
Myheader: MyContent
```

`-z <millisecs>` ：添加毫秒延迟，以免造成过多的洪水。

一秒发送一个请求：
```
dirb https://www.bilibili.com -z 1000
```

`-S`：静音模式。不要显示经过测试的单词。 

```
dirb https://www.bilibili.com -S     
```

`-X <extensions> / -x <exts_file>` ：用这个扩展名附加每个单词。
```
dirb https://www.bilibili.com -X ".php,.bak"

EXTENSIONS_LIST: (.php,.bak) | (.php)(.bak) [NUM = 2]

                                                       
https://www.bilibili.com/.svn/entries.php (CODE:412|SIZE:3496)                                                                                                                            
https://www.bilibili.com/.svn/entries.bak (CODE:412|SIZE:3496) 
```
                                                                                                                           
```
gedit extensions.txt 

.php
.bak
.txt
/
```

```
dirb https://www.bilibili.com -x extensions.txt -z 1000

EXTENSIONS_FILE: extensions.txt | (.php)(.bak)(.txt)(/) [NUM = 4]
SPEED_DELAY: 1000 milliseconds
```

`-o <output_file> `：将输出保存到磁盘。

```
dirb https://www.bilibili.com -x extensions.txt -o results.txt
cat results.txt
```






