---
layout: post
title: "ARP Poisoning"
date: "2022-07-22"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# ARP Poisoning - Study Guide（ARP 中毒 - 学习指南）
这如何支持我的渗透测试生涯？
有能力：
* 执行中间人攻击
* 执行高级攻击
* 嗅探交换网络上的流量

**ARP 中毒**是一种强大的攻击，可用于**拦截**交换网络上的流量。
正如您在网络模块中学习的那样，要发送 IP 数据包，主机需要知道下一跳的 MAC 地址。 下一跳可以是路由器、交换机或目标主机。

为了识别主机的 MAC 地址，计算机使用地址解析协议。

![arp-broadcast.png](/img/in-post/ine/arp-broadcast.png)

MAC 地址解析完成后，主机将目的地址保存在其 **ARP 缓存表**中。

![arp-response.png](/img/in-post/ine/arp-response.png)

如果攻击者找到了操纵 ARP 缓存的方法，他们也将能够接收发往其他 IP 地址的流量！！！
发生这种情况的原因是，只要目标 MAC 地址在 ARP 缓存表中，发送方就无需运行 ARP 即可到达该目标主机。

如果攻击者操纵通信涉及的双方的 ARP 表，它将能够嗅探整个通信，从而执行**中间人 (MITM) 攻击**！ 这可以通过发送**免费的 ARP 回复**来完成。
让我们看看这种攻击是如何工作的。

## ARP Poisoning Actors（ARP中毒演员）
在 ARP 中毒攻击期间，涉及三个参与者：
* 两个网络节点（客户端、服务器、路由器、打印机……）
* 攻击者

![three-actors-of-arp-poisoning-attack.png](/img/in-post/ine/three-actors-of-arp-poisoning-attack.png)

## Gratuitous ARP Replies（无偿 ARP 回复）
攻击者可以通过发送**无偿的 ARP 回复**来操纵其他主机的 ARP 缓存表。
免费 ARP 回复是未经请求的 ARP 回复消息。
换句话说，攻击者无需等待主机执行请求即可发送回复。

攻击者利用免费的 ARP 消息告诉受害者他们可以到达攻击者机器 MAC 地址的特定 IP 地址。

![exploits-gratuitous-arp-messages.png](/img/in-post/ine/exploits-gratuitous-arp-messages.png)

必须对每个受害者执行此操作。

![this-operation-must-be-performed-on-every-victim.png](/img/in-post/ine/this-operation-must-be-performed-on-every-victim.png)

一旦 ARP 缓存表包含虚假信息，中毒节点之间每次通信的**每个数据包**都会发送到攻击者的机器。
攻击者可以通过每 30 秒左右发送一次免费的 ARP 回复来防止中毒条目过期。

## Forwarding and Mangling Packets（转发和处理数据包）
一旦攻击者的机器收到数据包，它必须将它们**转发到正确的目的地**。 否则，受害主机之间的通信将无法正常工作。
即使机器位于交换网络上，此操作也可以让黑客嗅探中毒主机之间的流量。

此活动可以走得更远，因为攻击者还可以**更改数据包的内容**，从而操纵双方交换的信息！

## Local to Remote Man in the Middle（本地到远程的中间人攻击）
这种攻击甚至可以在**整个网络**和**路由器**上使用，让攻击者拦截 LAN 和 Internet 之间的通信！
在以下幻灯片中，您将学习如何在 Linux 中配置和使用 ARP 欺骗工具。

## Dsniff Arpspoof
Dsniff 是一组用于网络审计和渗透测试的工具。 它包括 **arpspoof**，一种旨在拦截交换 LAN 上的流量的实用程序。
手册说：
“arpspoof 通过伪造 ARP 回复将来自 LAN 上的目标主机（或所有主机）的数据包重定向到 LAN 上的另一台主机”

在运行该工具之前，您必须启用 Linux Kernel IP Forwarding，这是一项将 Linux 机器转换为路由器的功能。
通过启用 IP 转发，您可以告诉您的机器将您拦截的数据包转发到真正的目标主机。

```
echo 1 > /proc/sys/net/ipv4/ip_forward
```

然后您可以运行 arpspoof：

```
arpspoof -i <interface> -t <target> -r <host>
```

**接口**是您要使用的 NIC，例如，用于本地 LAN 的 eth0 或连接到 Hera Lab 时的 tap0。
Target 和 Host 是受害者的 IP 地址。

### Example - Using Arpspoof
要拦截 192.168.4.11 和 192.168.4.16 之间的流量，您必须使用以下命令：

```
echo 1 > /proc/sys/net/ipv4/ip_forward
arpspoof -i eth0 -t 192.168.4.11 -r 192.168.4.16
```

然后，您可以运行 Wireshark 并拦截流量！
