---
layout: post
title: "ARP Spoofing"
date: "2022-07-22"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# ARP Spoofing
受害者机器Debian：10.70.10.7
受害者机器Windows7：10.70.10.6
Windows文件服务器：10.70.10.100
内部Web服务器：10.70.10.200

Debian机器在Web服务器执行Web浏览。Window7从文件服务器上传和下载数据。

在真正执行攻击之前，看看能在Wireshark中嗅探到什么。

切换到Debian，加载内部Web服务器的页面：`10.70.10.200`。

回到kali，只能看到广播消息，比如ARP请求。这是因为我们的机器在switched network（交换网络）上。

```
Source: 08:00:27:ca:ce:a6
Destination: ff:ff:ff:ff:ff:ff
Protocol: ARP
Info: Who has 10.70.10.200? Tell 10.70.10.7
```

现在拦截ARP攻击的流量：

首先，配置攻击者机器正确的转发拦截的数据包。

```
echo 1 > /proc/sys/net/ipv4/ip_forward
```

现在运行ARP欺骗：

```
arpspoof -i eth0 -t 10.70.10.7 -r 10.70.10.200
```

它开始发送未经请求的ARP回复。

切换到Debian，尝试输入有效的凭据，登录Web应用程序。

看看Wireshark捕获到了什么：

```
Filter: http
```

在HTTP POST消息上，右键`Follow TCP Stream`。

由于ARP欺骗，我们捕获了HTTP请求和响应的所有内容。可以看到用于登录管理员区域的用户和密码。

切换到终端，停止ARP欺骗。按`Ctrl+C`，等待工具发送具有正确MAC地址的未经请求的ARP响应。这防止目标将数据包发送到攻击者机器。

现在执行Windows7客户端的攻击，

```
arpspoof -i eth0 -t 10.70.10.6 -r 10.70.10.100
```

工具开始向目标发送未经请求的ARP响应。

切换到Window7，我们可以通过访问文件服务器的数据共享，来产生一些流量。

在文件管理器的地址栏中输入：

```
\\fileserver\data
```

共享包含一些目录，和一个图片。当打开共享中的文件，客户端下载文件，以允许用户打开和修改它。

看看在Wireshark会话中发生了什么。关闭HTTP流量窗口，点击`Clear`，清除自动显示过滤器。

在显示过滤器中输入：`smb`。这将这显示和Windows共享相关的流量。

Wireshark有一个发现我们在会话中捕获的对象功能：`File->Export Objects->SMB/SMB2`。

SMB对象列表窗口包含在捕获会话中从服务器下载和上传的文件。

点击其中一个完全捕获的文件，点击`Save as`，将文件使用默认名称保存在root家目录，我们已经知道文件是图片。但是我们可以使Linux实用工具`file`来测试它。

在我们保存的文件上运行`file`实用程序。

```
file __FILEID_16396

__FILEID_16396: PNG image data, 299 x 100, 8-bit colormap, non-interlaced
```

是一个PNG图像，可以使用文件管理器打开它。





