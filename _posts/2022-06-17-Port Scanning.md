---
layout: post
title: "Port Scanning"
date: "2022-06-17"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Footprinting & Scanning
---

# Port Scanning（端口扫描）
这如何支持我的渗透测试生涯？
有能力：
* 为漏洞评估阶段做准备
* 进行隐身侦察
* 检测防火墙

一旦执行了网络映射，我们就知道哪些节点和相关的操作系统在目标网络上处于活动状态。
我们现在需要一种方法来发现在这些节点上运行的守护进程和服务：**端口扫描！**

端口扫描是用于确定目标主机上**打开了**哪些 TCP 和 UDP 端口的过程。此外，它可以让您知道哪个守护程序（就**软件和版本**而言）正在**侦听**特定端口。
使用此技术，您可以创建一个潜在弱点和漏洞列表，以便在接下来的漏洞评估阶段进行检查。

您可以从网络模块中回忆起，**守护程序**是在服务器上运行以提供给定**服务**的软件。守护程序还侦听特定端口。
端口扫描/服务检测的最终**目标**是找到每个主机上运行的守护进程的软件名称和版本。

## Under the Hood of a Port Scanner（在端口扫描仪的引擎盖下）
端口扫描，如操作系统指纹，通过向目标发送探测并分析响应来工作。
端口扫描实用程序或**端口扫描器**可自动进行探测请求和响应分析。这些是强大的工具，不仅可以为您提供有关目标的信息，还可以让您检测您和目标之间是否存在防火墙。

要充分利用这些工具，您必须了解它们的工作原理！
在下面的幻灯片中，您将看到 **TCP 端口扫描器**的工作原理。

### TCP Three Way Handshake（TCP 三次握手）
在网络部分，您看到了 TCP 3 次握手的工作原理。

![TCP3-wayhandshake](/img/in-post/ine/TCP3-wayhandshake.png)

当客户端想要连接到服务器时，它首先发送一个启用了 SYN 标志的数据包。

![SYN-flag-enabled](/img/in-post/ine/SYN-flag-enabled.png)

然后服务器通过发送一个启用了 SYN 和 ACK 标志的数据包进行响应。

![SYNandACK](/img/in-post/ine/SYNandACK.png)

最后，客户端通过发送一个启用了 ACK 标志的数据包进行回复，并且可以开始实际的数据传输。

但是如果客户端试图连接到一个**关闭**的端口会发生什么？
换句话说，如果客户端试图联系一个**没有运行或在另一个端口上运行**的守护进程会发生什么？

服务器将回复一个设置了重置 (RST) 和 ACK 标志的数据包。此行为告诉客户端该端口**已关闭**。

![RSTandACK](/img/in-post/ine/RSTandACK.png)

您将在以下幻灯片中找到的技术利用这些事实来执行端口扫描。
您还将了解为什么一种特定的扫描方法可能比另一种更隐秘。

### TCP Connect Scan（TCP 连接扫描）
执行端口扫描的最简单方法是尝试连接到每个端口。
如果扫描仪收到一个 RST 数据包，则端口关闭。
如果扫描仪可以完成 3 次握手，那么端口是开放的。连接后，扫描器会向目标主机发送一个 RST 数据包以突然关闭连接。

下图总结了针对单个**开放**端口运行的 TCP Connect 扫描。

![portisopen](/img/in-post/ine/portisopen.png)

此图总结了针对单个**关闭**端口运行的 TCP 连接扫描。

![portisclosed](/img/in-post/ine/portisclosed.png)

**每个 TCP 连接扫描探测都会记录在守护程序日志中**，因为从应用程序的角度来看，探测看起来像是一个合法连接。
系统管理员可以很容易地**检测**到扫描，因为他们会看到与在一台机器上运行的所有服务的大量连接；为了防止这种情况，发明了 TCP SYN 扫描。

### TCP SYN Scan（TCP SYN 扫描）
**TCP SYN 扫描**在设计上是**隐蔽的**。
在 SYN 扫描期间，扫描器不会执行完整的握手，它只是**发送一个 SYN 数据包**并分析来自目标机器的响应。

扫描器向目标 `<host>:<port>` 对发送启用了 `SYN` 标志的 TCP 数据包，并且：
如果它收到一个 `RST` 数据包，那么它会将端口标记为**关闭**。
如果扫描仪收到一个 `ACK`​​ 数据包，那么端口是**打开的**。将端口标记为打开后，扫描器会向目标主机发送一个 RST 数据包以停止握手。

在这里，我们看到了一个图表，它总结了针对单个**开放**端口运行的 TCP SYN 扫描。

![TCPSYNScan](/img/in-post/ine/TCPSYNScan.png)

此图总结了针对单个**关闭**端口运行的 TCP SYN 扫描。

![TCPSYNScanclosed](/img/in-post/ine/TCPSYNScanclosed.png)

由于与目标守护程序没有真正的连接，因此**无法通过查看守护程序日志来检测 SYN 扫描。**
随着课程的进行，您将看到如何使用 Nmap 执行所有这些扫描类型！

## Scanning with Nmap（使用 Nmap 扫描）
您已经了解了如何使用 Nmap 执行主机发现和操作系统指纹识别。
现在让我们看一下如何使用 Nmap 以及如何使用不同的方法执行扫描。

Nmap 语法非常简单，但功能强大：

```
nmap [Scan Type (s)] [Options] {target specification}
```

您必须指定一种或多种扫描类型、一些选项和您的目标。

例子

要在命令行上传递扫描类型或选项，您必须使用破折号“-”后跟一个或多个字母来指定您的选项。在以下幻灯片中，您将看到一些可用于执行不同扫描活动的常用命令行开关。

```
nmap -sS -sV -O --osscan-limit 192.168.1.0/24
```

以下内容来自[Options Summary](https://nmap.org/book/man-briefoptions.html)：

```
SCAN TECHNIQUES:
  -sS/sT/sA/sW/sM: TCP SYN/Connect()/ACK/Window/Maimon scans
SERVICE/VERSION DETECTION(服务/版本检测):
  -sV: Probe open ports to determine service/version info(探测开放端口以确定服务/版本信息)
OS DETECTION(操作系统检测):
  -O: Enable OS detection(启用操作系统检测)
  --osscan-limit: Limit OS detection to promising targets(将操作系统检测限制在有希望的目标上)
```
### Nmap Scan Types（Nmap 扫描类型）
最常用的扫描类型是：
* `-sT` 执行 **TCP 连接扫描**
* `-sS` 执行 **SYN 扫描**
* `-sV` 执行**版本检测扫描**

虽然 **TCP 连接扫描**和 **SYN 扫描**如您之前看到的那样工作，但版本检测扫描**做得更多。**
让我们看看如何使用 Nmap 来执行这些扫描！

### TCP Connect Scan with Nmap（使用 Nmap 进行 TCP 连接扫描）
要执行 **TCP 连接扫描**，您可以使用命令行开关 `-sT`。
请记住，这种类型的扫描会**记录在目标系统上的应用程序日志中**，因为每个守护程序都会收到来自扫描机器的连接。

```
nmap -sT <target>
```
### TCP SYN Scan with Nmap（使用 Nmap 进行 TCP SYN 扫描）
要执行 **TCP SYN 扫描**，您可以使用命令行开关 `-sS`。这种类型的扫描也称为**隐形扫描**，因为没有（完全）连接到目标守护程序。
请注意，配置良好的 IDS 仍会检测到扫描！

```
nmap -sS <target>
```

### Version Detection Scan with Nmap（使用 Nmap 进行版本检测扫描）
要执行**版本检测扫描**，您可以使用命令行开关 `-sV`。
这种类型的扫描将 TCP 连接扫描与一些探针混合在一起，这些探针用于检测哪些应用程序正在侦听特定端口；这不是隐身但非常有用。让我们看看为什么。

```
nmap -sV <target>
```

在版本检测扫描期间，Nmap 执行 TCP 连接扫描并从服务器读取监听端口的守护进程的**横幅(banner)**。

![bannerdetection](/img/in-post/ine/bannerdetection.png)

如果守护进程自己不发送横幅，Nmap 会发送一些探测来了解监听应用程序是什么。这背后的想法是通过研究其行为来猜测应用程序及其版本。
如果它看起来像狗，走路像狗，吠叫像狗，那么它就是狗。

您收集的有关网络、操作系统和在其上运行的守护程序的知识对于设置和执行成功的漏洞评估至关重要；这将防止您盲目地执行漏洞扫描和手动调查。

## Specifying the Targets（指定目标）
我们刚刚看到了如何使用不同的扫描方法，但是在没有有效设置目标的情况下选择正确的扫描类型不会很有用，对吧？
Nmap 有一个非常灵活的语法来指定你的目标。您可以使用 **DNS 名称、IP 地址列表、CIDR 表示法、通配符、范围、八位字节列表，**甚至**输入文件。**

### By DNS Name（按 DNS 名称）
通过**DNS 名称**指定目标只需在命令行上编写它们。

```
nmap <scan type> target1.domain.com target2.otherdomain.com
```

### With an IP Addresses List（使用 IP 地址列表）
同样，您可以在命令行上编写 **IP 地址列表**。

```
nmap <scan type> 192.168.1.45 200.200.14.56 10.10.1.1 10.10.1.3
```

### By Using CIDR Notation（通过使用 CIDR 表示法）
如果您必须扫描一个或多个网络，也可以使用 CIDR 表示法。

```
nmap <scan type> 192.168.1.0/24 200.200.1.0/16 10.0.0.0/8
```

### By Using Wildcards（通过使用通配符）
还有一些非常有用的功能可以非常灵活地组合目标 IP 地址。
其中之一是使用**通配符**，其中星号 * 转换为 0-255 范围。

```
nmap <scan type> 192.168.1.*
```

例如，如果您必须映射网络并且您知道路由器地址的最后一个八位字节是 1，但您正在使用 /16 网络，这将非常有用。

```
nmap <scan type> 10.10.*.1
```

如果要扫描 200.200.0.0/16，可以使用以下语法而不是 CIDR 语法。

```
nmap <scan type> 200.200.*.*
```

### Specifying Ranges（指定范围）
继续，您可以为每个八位字节指定一个间隔。例如，如果您只想扫描 /16 网络的一部分，您可以使用：

```
nmap <scan type> 200.200.6-12.*
```

或者，您可以在扫描 /24 网络时忽略主机。

```
nmap <scan type> 10.14.33.0-112 10.14.33.114-255
```
### Octets Lists（八位组列表）
如果您需要扫描有限数量的与 IP 相关的目标，您可以使用逗号指定八位字节列表。

例如，您可以使用以下命令仅扫描 IP 地址以 1,3 和 17 结尾的主机：

```
nmap <scan type> 10.14.33.1,3,17
```

您可以在每个八位字节上使用此功能，甚至可以多次使用。在以下示例中，Nmap 将扫描八台主机：

```
nmap <scan type> 10.14,20.3.1,3,17,233
```
### Combining the Previous Methods（结合之前的方法）
您可以结合我们刚刚看到的方法来指定扫描目标。例如，您可以将 CIDR 表示法与八位组列表表示法混合使用。

```
nmap <scan type> 10.100.45.0/24 200.200.4.1,7,5,99
```

但是，如果你有一个很长的目标列表，在命令行上写它是不切实际的。
正如我们之前看到的，您可以改为将它们写入文件中，每行一个，然后使用 `-iL` 命令行开关。

```
nmap <scan type> -iL HostToScan.txt
```
## Choosing the Ports to Scan（选择要扫描的端口）
当您指定一个或多个目标时，默认情况下，Nmap 会扫描 Internet 上**最常用的端口**。
如果要指定自定义端口，可以使用 `-p` 选项。

您可以将端口指定为**逗号分隔列表**或**端口间隔**。

```
nmap -p 21,22,139,445,443,80 <target>
nmap -p 100-1000 <target>
```
## Nmap Examples
让我们看看 Nmap 调用示例！

```
nmap 10.11.12.0/24
```
* 这将执行 SYN 扫描（nmap 默认）
* 目标是 /24 网络

```
nmap -sT 192.168.12.33,45
```
* 这将执行 TCP 连接扫描
* 目标是 192.168.12.33 和 192.168.12.45

```
nmap -sV 10.11.12.0/24 10.200.0.1
```
* 这将执行服务检测扫描
* 目标是 /24 网络加上单个主机

```
nmap -sS 1.2.3.4 4.5.6-9.7
```

这将执行 SYN 扫描
目标是：

```
1.2.3.4
4.5.6.7
4.5.7.7
4.5.8.7
4.5.9.7
```

```
nmap -p 80 10.11.12.0/24
```

* 这仅在端口 80 上执行 SYN 扫描
* 目标是 /24 网络

```
nmap -sT -p 1-100,443 192.168.12.33,45
```

* 这将对前 100 个端口和端口 443 执行 TCP 连接扫描
* 目标是 192.168.12.33 和 192.168.12.45

## Discovering Network with Port Scanning（使用端口扫描发现网络）
在您的渗透测试生涯中，您可能会遇到受防火墙保护且 ping 被阻止的网络。
对于这种情况，您应该至少准备一个备份计划。

遇到不响应 ping 但有许多 TCP 或 UDP 端口打开的服务器的情况并不少见。
使用 nmap 时，您可以使用 `-Pn` 开关强制对此类服务器进行扫描。 （跳过 ping 扫描并将其视为活着）
但这对发现潜在目标有何帮助？

您应该知道，在某些系统上，有些端口几乎总是打开的。
如果您想找到一个“活动”主机，您可以扫描典型端口而不是执行 ping 扫描。

通常，打开的端口是以下服务运行的标志：

|Protocol|Port Numbers|Service|
|---|---|---|
|TCP|22|SSH|
|TCP|80, 443|HTTP/HTTPs web server|
|TCP|445|Windows shares (SMB), also Linux equivalent - Samba service|
|TCP|25|SMTP (Simple Mail Transfer Protocol)|
|TCP|21|FTP (File Transfer Protocol)|
|TCP|137-139|Windows NetBIOS services|
|TCP|1433-1434|1433-1434 MSSQL Database|
|TCP|3306|MySQL Database|
|TCP|8080, 8443|HTTP(s) web server, HTTP proxy|
|UDP|53|DNS|

您现在可以使用上述技术作为 ping 扫描的替代方法，以识别“活动”主机。
四个最基本的 TCP 端口（**22、445、80、443**）可以用作网络中“活动”主机的指标。

## Spotting a Firewall（发现防火墙）
在尝试通过端口扫描发现活动主机时，您应该为潜在的误报和防火墙的存在做好准备。

在网络侦察期间，有时很难说是否有防火墙。
您应该记住，网络越大，实施一些额外的网络保护和访问控制机制的可能性就越大。

但是，有一些方法可以确定防火墙是否到位。
首先，您要注意不完整的 nmap 结果。 在开放的网络上，如果 TCP 扫描成功地针对众所周知的服务（如 **Web 服务器**），nmap 尝试使用 `-sV` 开关对其进行指纹识别应该没有任何困难。

但是您可能经常会看到无论打开的 http 端口如何，都无法识别版本：

![notrecognizedregardlessoftheopenhttpport](/img/in-post/ine/notrecognizedregardlessoftheopenhttpport.png)

或者，甚至无法识别服务类型：

![tcpwrapped](/img/in-post/ine/tcpwrapped.png)

**tcpwrapped**表示 **TCP 握手**完成，但是远程主机关闭了连接，没有收到任何数据。

这表明某些东西正在阻止与目标主机的连接。
此外，您可能希望使用 nmap `--reason` 开关，它会显示端口标记为打开或关闭的原因的解释。

例如，您可能从中了解到远程主机在 **TCP 握手**期间发送了一个**RST**数据包，这意味着某些东西阻止了 **TCP 握手**的完成。 应该是防火墙。。

## Masscan
在结束本模块之前，另一个可以帮助您通过探测 TCP 端口发现网络的有趣工具是 Masscan。
Masscan 旨在处理大型网络并一次扫描数千个 IP 地址。
它类似于 nmap，但要快得多； 但是，它可能不太准确。 您将在渗透测试评估中使用哪一个取决于您。
您可以使用masscan 执行主机发现，然后使用nmap 对某些感兴趣的主机进行详细扫描。

## References
[Remote OS Detection with Nmap](https://nmap.org/book/osdetect.html)



