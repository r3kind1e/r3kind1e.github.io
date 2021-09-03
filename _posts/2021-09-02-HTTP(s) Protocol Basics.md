---
layout: post
title: "HTTP(s) Protocol Basics"
date: "2021-08-26"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Penetration Testing Prerequisites
---
## HTTP(s) Protocol Basics

```bash
┌──(root💀kali)-[~]
└─# netcat           
Cmd line: ^C
                                                                                                                                                                                                                                             
┌──(root💀kali)-[~]
└─# nc                                                                                                                                                                                                                                   1 ⨯
Cmd line: -h
[v1.10-46]
connect to somewhere:   nc [-options] hostname port[s] [ports] ... 
listen for inbound:     nc -l -p port [-options] [hostname] [port]
options:
        -c shell commands       as `-e'; use /bin/sh to exec [dangerous!!]
        -e filename             program to exec after connect [dangerous!!]
        -b                      allow broadcasts
        -g gateway              source-routing hop point[s], up to 8
        -G num                  source-routing pointer: 4, 8, 12, ...
        -h                      this cruft
        -i secs                 delay interval for lines sent, ports scanned
        -k                      set keepalive option on socket
        -l                      listen mode, for inbound connects
        -n                      numeric-only IP addresses, no DNS
        -o file                 hex dump of traffic
        -p port                 local port number
        -r                      randomize local and remote ports
        -q secs                 quit after EOF on stdin and delay of secs
        -s addr                 local source address
        -T tos                  set Type Of Service
        -t                      answer TELNET negotiation
        -u                      UDP mode
        -v                      verbose [use twice to be more verbose]
        -w secs                 timeout for connects and final net reads
        -C                      Send CRLF as line-ending
        -z                      zero-I/O mode [used for scanning]
port numbers can be individual or ranges: lo-hi [inclusive];
hyphens in port names must be backslash escaped (e.g. 'ftp\-data').
```

```bash
nc -v www.ferrari.com 80
...
GET / HTTP/1.1
Host: www.ferrari.com


```

如果得到响应状态码为302：

```http
HTTP/1.1 302 Moved Temporarily
...
Location: /en_en/
...
```

向Location后的资源手动发起GET请求：

```http
GET /en_en/ HTTP/1.1
Host: www.ferrari.com


```

得到200响应码：

```http
HTTP/1.1 200 OK
```



也可以使用HEAD只获取请求头：

```http
HEAD / HTTP/1.1
Host: www.ferrari.com


```

若得到的响应状态码为302：

```http
HTTP/1.1 302 Moved Temporarily
...
Location: /en_en/
...
```

则用HEAD获取Location后的资源：

```http
HEAD /en_en/ HTTP/1.1
Host: www.ferrari.com


```

得到200响应码：

```http
HTTP/1.1 200 OK
```

在Burp Suite的`Repeater`中，点击右上角的铅笔图标，弹出Configure target details窗口，填写Host：`www.ferrari.com`和Port：80，点击OK。

在`Request`中，输入和上述相同的GET请求（两个回车换行）：

```http
GET / HTTP/1.1
Host: www.ferrari.com


```

得到响应码302：

```http
HTTP/1.1 302 Moved Temporarily
...
Location: /en-en/
```

点击`Follow redirection`，可以看到，GET请求的资源路径变成了Location中的路径。

```http
GET /en-en/ HTTP/1.1
Host: www.ferrari.com


```

这次得到的响应码为200：

```http
HTTP/1.1 200 OK
```

点击`<`箭头，在下拉菜单中可以选择重定向前的请求。点击`>`箭头，在下拉菜单中可以选择重定向后的请求。



```bash
nc -v hack.me 80
...
GET / HTTP/1.1
Host: hack.me
```

得到301状态码响应：

```http
HTTP/1.1 301 Moved Permanently
Content-Type: text/html; charset=UTF-8
Location: https://hack.me/
Server: Microsoft-IIS/7.5
X-Powered-By: Hack.me Team
X-XSS-Protection: 1;mode=block
X-Frame-Options: SAMEORIGIN
Strict-Transport-Security: max-age=31536000
Date: Thu, 02 Sep 2021 13:56:57 GMT
Content-Length: 139

<head><title>Document Moved</title></head>
<body><h1>Object Moved</h1>This document may be found <a HREF="https://hack.me/">here</a></body>
```

如果我们输入如下命令：

```bash
nc -v hack.me 443
...
GET / HTTP/1.1
Host: hack.me
```

什么也不会发生，因为netcat不建立HTTPS连接。

`openssl`是可以分析HTTPS的命令行工具。

```bash
openssl s_client -connect hack.me:443
```

在我们可以和服务器通信之前，我们需要交换使用的证书的信息，然后在客户端和服务器之间进行握手。

如果你想分析握手，和其它加密通信，你可以使用`-debug`参数，使用`-debug`是很好的分析方式：

```bash
openssl s_client -connect hack.me:443 -debug
```

也可以使用`-state`参数来打印握手的阶段：

```bash
openssl s_client -connect hack.me:443 -state
```

`-quiet`参数来减少输出信息：

```bash
openssl s_client -connect hack.me:443 -quiet
```

一旦与服务器的连接建立，我们可以使用上述相同的方法：

```bash
┌──(root💀kali)-[~]
└─# openssl s_client -connect hack.me:443 -quiet
depth=2 C = US, ST = Arizona, L = Scottsdale, O = "GoDaddy.com, Inc.", CN = Go Daddy Root Certificate Authority - G2
verify return:1
depth=1 C = US, ST = Arizona, L = Scottsdale, O = "GoDaddy.com, Inc.", OU = http://certs.godaddy.com/repository/, CN = Go Daddy Secure Certificate Authority - G2
verify return:1
depth=0 C = US, ST = California, L = Santa Clara, O = "Caendra, Inc.", CN = *.hack.me
verify return:1
GET / HTTP/1.1
Host: hack.me

HTTP/1.1 200 OK
Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Pragma: no-cache
Content-Type: text/html; charset=UTF-8
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Server: Microsoft-IIS/7.5
Set-Cookie: SID=skhgcinm0ka3jstoc0cg6iccfnh2tc90h10rk7dmjhcusbkoo3q72mn5b9jcbggvne8tologi6q7da2ndh6lt479g79ond3i7vblae3; expires=Fri, 03-Sep-2021 14:06:47 GMT; Max-Age=86400; path=/; secure; HttpOnly
X-Powered-By: Hack.me Team
X-XSS-Protection: 1;mode=block
X-Frame-Options: SAMEORIGIN
Strict-Transport-Security: max-age=31536000
Date: Thu, 02 Sep 2021 14:06:47 GMT
Content-Length: 16490



<Content>

OPTIONS / HTTP/1.1
Host: hack.me

HTTP/1.1 200 OK
Allow: OPTIONS, TRACE, GET, HEAD, POST
Server: Microsoft-IIS/7.5
Public: OPTIONS, TRACE, GET, HEAD, POST
X-Powered-By: Hack.me Team
X-XSS-Protection: 1;mode=block
X-Frame-Options: SAMEORIGIN
Strict-Transport-Security: max-age=31536000
Date: Thu, 02 Sep 2021 14:08:48 GMT
Content-Length: 0

```

所以基本如你所见，分析HTTP连接和分析HTTPS连接的主要区别，在会话的开始的SSL握手。所以在同一个会话中，我们可以产生流量而不需要每次都建立握手。

如果我们关闭会话，我们需要重新建立握手，保存新的会话密钥。

在Burp Suite的`Repeater`模块中，点击右上角的铅笔图标，弹出Configure target details窗口，输入Host：hack.me，Port：443，勾选`Use HTTPS`，在Request中，正常进行HTTP请求即可。

```http
GET / HTTP/1.1
Host: hack.me


```

响应中将得到200状态码：

```http
HTTP/1.1 200 OK
```



