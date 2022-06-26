---
layout: post
title: "Web Server Fingerprinting"
date: "2022-06-25"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Web Attacks
---

# Introduction - Study Guide
与桌面应用程序相比，Web 应用程序使用不同的技术和编程范例。
因此，本模块将向您介绍 Web 应用程序渗透测试世界，从技术信息收集到 Web 利用阶段！

与其他模块一样，您将在此处找到应用于真实 IP 地址和主机的工具和技术示例。
切勿针对这些地址运行任何这些工具和技术。

# Web Server Fingerprinting - Study Guide（Web 服务器指纹 - 学习指南）
这如何支持我的渗透测试生涯？
* 目标知识
* 能够充分利用利用工具
* 能够搜索正确的公共漏洞利用

## Web Server Fingerprinting
Web 应用程序通常构成了面向 Internet 的攻击面的绝大部分。
如您所知，Web 应用程序在 Web 服务器上运行，因此测试 **Web 服务器**是否安全免受外部或内部攻击至关重要。

许多人倾向于忽略 Web 服务器的安全性，但配置错误的 Web 服务器可能会打开整个网络基础设施的大门。
与往常一样，收集有关目标的信息是成功测试和利用阶段的关键。
在以下幻灯片中，您将看到如何**手动**和使用**自动工具**对 Web 服务器进行**指纹识别**。

对 Web 服务器进行指纹识别意味着检测：
* 提供 Web 服务器服务的守护进程，例如 IIS、Apache、nginx 等。
* 它的版本
* 托管服务器的计算机的操作系统。

让我们来看看如何手动对 Web 服务器进行指纹识别。 然后我们将介绍自动检测。

### Fingerprinting with Netcat
**Netcat** 是一个非常流行的工具，也被称为 **“TCP/IP 瑞士军刀”**。 您可以通过多种不同的方式使用 Netcat； 它既可以是服务器，也可以是客户端。
要对 Web 服务器进行指纹识别，您可以使用 Netcat 作为客户端**手动**向服务器发送请求。

以下活动称为**横幅抓取**。
要获取横幅，您只需连接到一个监听守护程序，然后读取它发送回您的客户端的横幅。

要连接到 HTTP 服务器，您必须将目标主机和目标端口传递给 Netcat。 大多数时候，您只会使用默认的 HTTP 端口 (80)。

```
nc <target address> 80
```

连接后，您必须发送**有效的 HTTP 请求**，您可以使用 **HEAD** HTTP 动词来完成。 这个动词请求资源的标题（例如网页）。
请记住，每个 HTTP 请求在请求本身的标头和正文之间都有两个空行，因此在发送像 HEAD 这样的无正文请求时，您仍然需要附加两个空行。

一旦你运行 Netcat，它就会连接到服务器； 然后您可以发送请求消息：

```
HEAD / HTTP/1.0
```

并按两次返回。

```
nc <target address> 80
HEAD / HTTP/1.0
```

发送完这两个空行后，目标服务器将处理您的请求并返回响应消息。
大多数情况下，响应包含一个 `Server:` 标头，其中包含有关 Web 服务器的信息，有时还包含服务器操作系统的信息。

#### Fingerprinting with Netcat Examples
在这里，我们看到了在 Debian Linux 机器上运行的 Apache 服务器的指纹。

```
nc target.site 80
HEAD / HTTP/1.0

HTTP/1.1 200 OK
Date: Mon, 26 Jan 2015 11:56:08 GMT
Server: Apache/2.2.22 (Debian)
Vary: Accept-Encoding
Connection: close
Content-Type: text/html;charset=UTF-8
```

这是在 Red Hat Linux 服务器上运行的 Apache 服务器的指纹。

```
nc target.site 80
HEAD / HTTP/1.0

HTTP/1.1 302 Found
Date: Mon, 26 Jan 2015 13:30:50 GMT
Server: Apache/2.2.3 (Red Hat)
Connection: close
Content-Type: text/html; charset=iso-8859-1
```

这是运行在 MS Windows 化身上的 MS IIS 服务器的指纹。

```
nc target.site 80
HEAD / HTTP/1.0

HTTP/1.1 200 OK
Content-Length: 0
Content-Type: text/html
Server: Microsoft-IIS/7.5
Date: Mon, 26 Jan 2015 13:47:05 GMT
```
#### Common Mistakes
使用 Netcat 对 Web 服务器进行指纹识别时，请注意几个常见错误：
* 您必须以大写形式编写请求。
* Netcat 连接服务器后不会通知您； 您必须在运行命令后编写您的请求。 您可以使用详细 (`-v`) 命令行开关更改此行为。

Netcat 不执行任何类型的加密，因此您不能使用它连接到 HTTPS 守护程序。
例如，如果您尝试使用 Netcat 连接到 HTTPS Web 服务器，您只会在输入请求后看到连接断开。

### Fingerprinting with OpenSSL
如果 Web 服务器只监听 HTTPS 连接并且您想要执行一些手动指纹识别会发生什么？
您可以使用 **OpenSSL** 命令行工具！

openssl 命令是一个命令行界面，用于手动使用 [OpenSSL SSL/TLS 工具包](https://www.openssl.org/)的各种功能。
您可以使用它来建立到 HTTPS 服务的连接，然后发送通常的 HEAD HTTP 动词。

```
openssl s_client -connect target.site:443
HEAD / HTTP/1.0
```
### Limits of Manual Fingerprinting（手动指纹的限制）
在执行指纹识别时，需要注意的一点是系统管理员可以**自定义** Web 服务器横幅； 这是为了使攻击者的指纹识别活动更加困难。
自动工具超越了横幅抓取。 他们通过检查与实现相关的小细节来识别 Web 服务器，例如：
* 响应消息中的标头排序
* 错误处理

### Fingerprinting with Httprint
Httprint 是一种 Web 服务器指纹识别工具，它使用基于签名的技术来识别 Web 服务器。
最常用的语法非常简单：

```
httprint -P0 -h <target hosts> -s <signature file>
```
前面的命令行使用以下选项：
* `-P0` 避免 ping 主机（大多数 Web 服务器不响应 ping 回显请求）
* `-h <target hosts>` 告诉该工具对主机列表进行指纹识别。 建议使用您要测试的主机的 IP 地址。 您还可以提供一系列 IP 地址
* `-s` 设置要使用的签名文件

这里我们看一个在服务器上运行 Httprint 的例子：

```
httprint -P0 -h 1.2.3.4 -s /usr/share/httprint/signatures.txt
```
![httprint](/img/in-post/ine/httprint.png)

## References
[OpenSSL SSL/TLS toolkit](https://www.openssl.org/)
