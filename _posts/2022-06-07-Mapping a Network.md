---
layout: post
title: "Mapping a Network"
date: "2022-06-07"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Footprinting & Scanning
---

# Disclaimer
在本模块中，您将看到用于真实 IP 地址和主机的工具和技术示例。
切勿在未经适当授权的情况下在这些地址或任何机器和网络上运行任何这些工具和技术。

# Mapping a Network - Study Guide
这如何支持我的渗透测试生涯？
* 执行有效渗透测试的能力
* 了解您的范围内目标
* 创建技术地图的能力

在信息收集阶段收集有关目标组织的信息后，渗透测试人员继续对客户端网络上运行的节点进行指纹识别和枚举；这是信息收集的基础设施部分。
您将看到的技术适用于本地和远程网络。

如您所知，每台连接到 Internet 或专用网络的主机都必须有一个唯一的 IP 地址来标识它。
渗透测试人员如何确定位于范围内网络中的哪些主机已启动并正在运行？

## Why Map a (Remote) Network?（为什么要映射（远程）网络？）
例子

某公司要求进行渗透测试，范围内考虑以下地址块：200.200.0.0/16。
16 位长的网络掩码意味着网络最多可以包含 2^16 (65536) 个 IP 地址在 200.200.0.0 - 200.200.255.255 范围内的主机。
渗透测试人员需要一种方法来找出 65536 个 IP 地址中的哪一个被分配给了一个节点。

我们需要一种将未知网络转换为有用地图的方法，从以下内容开始：

![unknown network](/img/in-post/ine/unknown-network.png)

我们会得到这样的东西！

![get-something-like-this](/img/in-post/ine/get-something-like-this.png)


## Ping Sweeping（ping扫描）
前面的示例显示了映射远程网络如何完全改变您对它的理解。
试图盲目地利用 65535 主机将是对时间和资源的巨大浪费！
Q: 您如何确定范围内的哪些 IP 地址分配给主机！
A: 最简单的方法是通过ping扫描！

您可能已经知道 ping 命令；它是一个实用程序，旨在测试机器是否在网络上处于活动状态。
您可以使用命令行在每个主要操作系统中运行 ping：

```
ping www.baidu.com

正在 Ping www.a.shifen.com [14.215.177.39] 具有 32 字节的数据:
来自 14.215.177.39 的回复: 字节=32 时间=54ms TTL=53
来自 14.215.177.39 的回复: 字节=32 时间=82ms TTL=53
来自 14.215.177.39 的回复: 字节=32 时间=85ms TTL=53
来自 14.215.177.39 的回复: 字节=32 时间=74ms TTL=53

14.215.177.39 的 Ping 统计信息:
    数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 54ms，最长 = 85ms，平均 = 73ms
```

Ping 通过向主机发送一个或多个特殊的 ICMP 数据包（类型 8 - **echo request回显请求**）来工作。如果目标主机使用 ICMP **echo reply回显应答**数据包进行回复，则该主机处于活动状态。
ICMP (RFC792) 是一种用于携带诊断消息的协议。 ICMP 使用 IP 提供的服务，但它实际上是 Internet 协议的一部分。

Ping 扫描工具会自动对子网或 IP 范围内的每台主机执行相同的操作，从而使您免于输入数百或数千个 ping 命令。
在下文中，您将看到两个用于映射网络的实用程序。

### fping
fping 是一个 Linux 工具，它是标准 ping 实用程序的改进版本。您可以使用 Fping 执行 ping 扫描。
它默认安装在 Kali Linux 上，您可以使用以下语法从命令行运行它：
`fping -a -g IPRANGE`

`-a` 选项强制工具仅显示活动主机，而 `-g` 选项告诉工具我们要执行 ping 扫描而不是标准 ping。
您可以使用 CIDR 表示法或指定扫描的开始和结束地址来定义 IP 范围。

```
fping -a -g 10.54.12.0/24
fping -a -g 10.54.12.0 10.54.12.255
```

在您直接连接的 **LAN** 上运行 fping 时，即使您使用 `-a` 选项，您也会收到一些关于离线主机的警告消息（ICMP Host Unreachable）。

要抑制这些消息，您可以将进程标准错误重定向到 `/dev/null`。

```
fping -a -g 192.168.82.0 192.168.82.255 2>/dev/null
192.168.82.1
192.168.82.11
192.168.82.112
192.168.82.171
192.168.82.202
```

ping 主机只是查看它是否存活的一种方法。
在接下来的幻灯片中，您将看到一个非常强大的工具，能够执行高级主机发现等等：nmap！

* https://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO-3.html
* https://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO.html
* https://tldp.org/

### Nmap Ping Scan
**Nmap**（Network Mapper）是一个用于网络探索和安全审计的开源工具。它默认安装在 Kali 上，但您也可以将它[安装](https://nmap.org/download.html)在几乎所有操作系统上。
在本课程中，我们将看到 Nmap 的一些功能，其中之一是 **ping 扫描**。

您可以使用 `-sn` 命令行开关执行 ping 扫描。您可以在命令行上以 CIDR 格式指定目标作为范围并使用通配符表示法。

```bash
nmap -sn 200.200.0.0/16
nmap -sn 200.200.123.1-12
nmap -sn 172.16.12.*
nmap -sn 200.200.12-13.*
```

此外，您可以将主机列表保存在文件中并使用输入列表 `-iL` 命令行开关。
您可以通过创建包含以下内容的文件来实现与上一个示例相同的结果：

```
200.200.0.0/16
200.200.123.1-12
172.16.12.*
200.200.12-13.*
```

您可以保存您的主机列表并使用 Nmap 调用它，方法是将输入命令行开关与 `-sn` 结合使用。这样，您将对给定列表中的每个主机执行 ping 扫描。
请注意，在此示例中，我们将保存的文件命名为 hostslist.txt

```
nmap -sn -iL hostslist.txt
```

Nmap 有很多主机发现功能；我们强烈建议您在家中或在 Hera Lab 中尽可能多地测试该工具。
要检查可以使用哪些主机发现方法，可以参考 nmap [手册页](https://nmap.org/book/man.html)（在 Linux 上可以使用 man nmap 命令）或不带选项的 nmap 命令的简要输出。

事实上，扫描器不只是使用 ping 数据包来查找活动主机。这是 nmap 命令输出的摘录。

```
主机发现：
    -sL：列表扫描 - 简单地列出要扫描的目标
    -sn：Ping 扫描 - 禁用端口扫描
    -Pn：将所有主机视为在线——跳过主机发现
    -PS/PA/PU/PY[portlist]：到给定端口的 TCP SYN/ACK、UDP 或 SCTP 发现
    -PE/PP/PM：ICMP 回显、时间戳和网络掩码请求发现探测
    -PO[协议列表]：IP 协议 Ping
```

## OS Fingerprinting
映射网络后，您最终会得到一个**活动主机列表**。这些是**响应 ping 或 nmap 探测的主机**，但您仍然不知道主机正在运行什么软件以及它们在网络中的角色。
它们是路由器、服务器还是客户端？在这个阶段，您只能说它们已启动并运行连接到网络的主机。

要将实时 IP 地址列表转换为更有用的东西，我们需要知道哪些软件（即，什么操作系统）响应了我们的探测。
**操作系统指纹识别**是确定网络上主机使用的操作系统的过程。

要对操作系统进行指纹识别，您必须向主机**发送网络请求**，然后**分析**您返回的**响应**。
这是可能的，因为各种操作系统的**网络堆栈实现**存在一些微小差异。

指纹识别工具向目标主机发送一系列特制的请求。

![osfingerprinting](/img/in-post/ine/osfingerprinting.png)

然后他们检查响应中的每一位，创建主机行为的签名。

![osfingerprinting1](/img/in-post/ine/osfingerprinting1.png)

最后，将签名与已知操作系统签名的**数据库**进行比较。
此过程利用了网络堆栈实现的差异，因此存在一些**猜测**；但是，使用最好的工具并以经验支持它们，您可以获得非常可靠的结果！

在渗透测试期间，您必须在每个网络节点上执行此侦察步骤，包括：
* 路由器
* 服务器
* 防火墙
* 打印机
* 主机
* 等等

此阶段的目标是编写如下表：

|IP Address|OS|Confidence|
|---|---|---|
|200.200.3.1|PAN-OS|85%|
|200.200.3.10|Linux 3.7|100%|
|200.200.3.78|Linux 2.6.19-2.6.36|90%|
|200.200.4.12|Windows 7 SP1|100%|
|200.200.4.16|Windows 7 SP1|75%|
|200.200.4.18|FreeBSD|85%|
|200.200.4.19|HP-OS|78%|

您可以对记录的流量捕获（被动）或使用我们刚刚看到的技术（主动）执行操作系统指纹识别。
可以使用 [p0f](https://lcamtuf.coredump.cx/p0f3/) 完成离线操作系统指纹识别。在本课程中，我们将专注于使用 nmap 进行主动指纹识别。

### OS Fingerprinting with Nmap
要使用 nmap 执行操作系统指纹识别，您必须使用 `-O` 命令行选项并指定您的目标。
如果您已经知道目标处于活动状态，您还可以添加 `-Pn` 开关以跳过 ping 扫描。

```
nmap -Pn -O <target(s)>
```

您可以使用以下选项微调操作系统指纹识别过程：

```
操作系统检测：
-O：启用操作系统检测
--osscan-limit：将操作系统检测限制为有希望的目标
--osscan-guess：更积极地猜测操作系统
```

选择更轻或更激进的操作系统检测取决于参与度。

例子

如果您确实需要检测您知道是活动的机器的操作系统，但没有响应 ping 探测，您可以运行：

```
nmap -Pn -O <target>
```

另一方面，如果您必须扫描数千台主机，您可以首先将操作系统侦察限制在有希望的主机上。

```
nmap -O --osscan-limit <targets>
```

## References
[RFC792](https://datatracker.ietf.org/doc/html/rfc792)

[BASH Redirection](https://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO-3.html)

[BASH Programming - Introduction HOW-TO](https://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO.html)

[The Linux Documentation Project](https://tldp.org/)

[Nmap](https://nmap.org/download.html)

[Nmap Network Scanning](https://nmap.org/book/)

[p0f](https://lcamtuf.coredump.cx/p0f3/)