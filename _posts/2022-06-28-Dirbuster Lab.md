---
layout: post
title: "Dirbuster Lab"
date: "2022-06-28"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Web Attacks
---

# 概述
在本实验中，您将学习使用 Dirbuster 工具执行目录枚举攻击。

# 任务
## 实验室环境
在这个实验室环境中，用户将获得对 Kali GUI 实例的访问权限。可以使用安装在 Kali 上的工具访问 Mutillidae Web 应用程序的实例，网址为 `http://demo.ine.local`

目标：使用 Dirbuster 执行目录枚举！

![DirbusterLab.png](/img/in-post/ine/DirbusterLab.png)

## 指示
使用目录词表： `/usr/share/wordlists/dirb/common.txt`

## 工具
该实验室的最佳工具是：

* Dirbuster

* 网络浏览器

# 我自己的思路
## Dirbuster

```
Dirs found with a 200 response:

/
/ajax/
/webservices/
/webservices/soap/
/webservices/rest/
/classes/
/documentation/
/includes/
/webservices/soap/lib/
/webservices/test/
/images/
/javascript/
/javascript/ddsmoothmenu/
/javascript/jQuery/
/webservices/test/testoutput/
/javascript/jQuery/colorbox/
/javascript/gritter/
/config/
/javascript/jQuery/colorbox/images/
/includes/hints/
/javascript/jQuery/colorbox/images/ie6/
/data/
/index.php/
/passwords/
/phpinfo.php/
/phpmyadmin/
```

发现了一些惊喜：

```
http://demo.ine.local/phpinfo.php
http://demo.ine.local/data/accounts.xml
http://demo.ine.local/passwords/accounts.txt
http://demo.ine.local/phpmyadmin/
```

![Secret-PHP-Server-Configuration-Page.png](/img/in-post/ine/Secret-PHP-Server-Configuration-Page.png)

尝试了两次，都是爆破到一半就卡住了，原因不明。

## Dirb

```
dirb http://demo.ine.local/
dirb http://demo.ine.local/ /usr/share/dirb/wordlists/big.txt
dirb http://demo.ine.local -a "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:101.0) Gecko/20100101 Firefox/101.0"
dirb http://demo.ine.local -p http://127.0.0.1:8080
dirb http://demo.ine.local -p http://127.0.0.1:8080 -c "COOKIE:XYZ"
dirb http://demo.ine.local -p http://127.0.0.1:8080 -u "admin:password"
dirb http://demo.ine.local -z 1000
dirb http://demo.ine.local -S
dirb http://demo.ine.local -X ".php,.bak"
dirb http://demo.ine.local -x extensions.txt -z 1000
dirb http://demo.ine.local -x extensions.txt -o results.txt
```

访问：`http://demo.ine.local/robots.txt`
```
User-agent: *
Disallow: passwords/
Disallow: config.inc
Disallow: classes/
Disallow: javascript/
Disallow: owasp-esapi-php/
Disallow: documentation/
Disallow: phpmyadmin/
Disallow: includes/
```
# 解决方案
第 1 步：打开实验室链接以访问 Kali GUI 实例。

第 2 步：检查机器/域是否可达。

命令

```
ping demo.ine.local
```
```
root@INE:~# ping demo.ine.local
PING demo.ine.local (192.214.149.3) 56(84) bytes of data.
64 bytes from demo.ine.local (192.214.149.3): icmp_seq=1 ttl=64 time=0.085 ms
64 bytes from demo.ine.local (192.214.149.3): icmp_seq=2 ttl=64 time=0.057 ms
64 bytes from demo.ine.local (192.214.149.3): icmp_seq=3 ttl=64 time=0.065 ms
64 bytes from demo.ine.local (192.214.149.3): icmp_seq=4 ttl=64 time=0.065 ms
^C
--- demo.ine.local ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3054ms
rtt min/avg/max/mdev = 0.057/0.068/0.085/0.010 ms
```
机器可达。

第 3 步：检查机器上打开的端口。

命令

```
nmap demo.ine.local
```

```
root@INE:~# nmap demo.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-28 09:05 IST
Nmap scan report for demo.ine.local (192.214.149.3)
Host is up (0.000012s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
3306/tcp open  mysql
MAC Address: 02:42:C0:D6:95:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.31 seconds
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
        inet 10.1.0.14  netmask 255.255.0.0  broadcast 10.1.255.255
        ether 02:42:0a:01:00:0e  txqueuelen 0  (Ethernet)
        RX packets 5465  bytes 452383 (441.7 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4369  bytes 2447237 (2.3 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.214.149.2  netmask 255.255.255.0  broadcast 192.214.149.255
        ether 02:42:c0:d6:95:02  txqueuelen 0  (Ethernet)
        RX packets 1025  bytes 56014 (54.7 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1010  bytes 58668 (57.2 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 19671  bytes 27326619 (26.0 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 19671  bytes 27326619 (26.0 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

机器上有两个接口（不包括环回接口），即 eth0 和 eth1。

第 5 步：打开网络浏览器并浏览到demo.ine.local。

第 6 步：启动 Dirbuster。

命令

```
dirbuster
```

第七步：输入目标网址`http://demo.ine.local/`，在工作方式中选择自动切换`Auto Switch (HEAD and GET)`

第 8 步：单击“浏览”按钮选择单词表。

第九步：点击下拉菜单，选择根目录（/）

第十步：选择“usr”目录。

第十一步：点击“share”目录。

第12步：点击“Wordlists”目录。

第13步：点击“dirb”目录。

第 14 步：选择“common.txt”作为词表。

`/usr/share/wordlists/dirb/common.txt`

词表被选中。

第十五步：点击开始按钮

等待一段时间以显示结果。

第 16 步：单击结果 - 列表视图按钮以查看结果。

第 17 步：单击结果 - 树视图以树格式获取结果。

当前正在运行的线程数为 10

```
Current number of running threads: 10
```

第 18 步：将线程增加到 30 以更快地获得结果。在“当前线程数”中输入 30。

单击更改按钮。

线程数从 10 修改为 30。

第 19 步：在结果 - 树视图中找到data目录。

注意：如果数据目录不可用，请稍等片刻或增加线程数。

第 20 步：单击下拉按钮以列出数据目录中的内容。

我们可以观察到 account.xml 存在于数据目录中。

第 21 步：右键单击account.xml，然后单击在浏览器中打开

`Open in Browser`

这将显示存储在此文件中的所有用户名和纯文本密码。

这就是我们如何使用 Dirbuster 来执行目录枚举攻击并发现包含敏感信息的目录/文件！