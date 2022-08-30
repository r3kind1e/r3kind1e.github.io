---
layout: post
title: "Assessment Methodologies"
date: "2022-08-29"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Assessment Methodologies
---

# Assessment Methodologies（评估方法）
信息收集是任何渗透测试的第一步，并且可以说是最重要的一步，因为所有其他阶段都依赖于在信息收集阶段获得的有关目标的信息。本课程将向您介绍信息收集，并将涵盖通过利用各种工具和技术执行被动和主动信息收集的过程，以便从目标获取尽可能多的信息。

## Assessment Methodologies: Information Gathering（评估方法：信息收集）
**什么是信息收集**

信息收集是任何渗透测试的第一步，涉及收集或收集有关您所针对的个人、公司、网站或系统的信息。
您对目标的信息越多，您在渗透测试的后期阶段就会越成功。
信息收集通常分为两种类型：
* 被动信息收集——包括在不主动与目标接触的情况下收集尽可能多的信息。
* 主动信息收集——通过积极参与目标系统来收集尽可能多的信息。 （您需要授权才能进行主动信息收集）

**我们在寻找什么信息**

**被动信息收集**
* 识别 IP 地址和 DNS 信息。
* 识别域名和域名所有权信息。
* 识别电子邮件地址和社交媒体资料。
* 识别目标站点上使用的网络技术。
* 识别子域。

**主动信息收集**
* 发现目标系统上的开放端口。
* 了解目标网络/组织的内部基础设施。
* 枚举来自目标系统的信息。

### 被动信息收集
参考：[Passive Information Gathering](https://r3kind1e.github.io/2022/08/10/Passive-Information-Gathering/)

#### Website Recon & Footprinting（网站侦察和足迹）
**What are we looking for（我们在找什么）**
* IP 地址
* 对搜索引擎隐藏的目录
* 姓名
* 电子邮件地址
* 电话号码
* 物理地址
* 正在使用的Web技术

**工具**

`whatis`获取关于工具的简介。

`host`获取托管网站的 Web 服务器的 IP 地址。

Mozilla插件：

[BuiltWith](https://addons.mozilla.org/en-US/firefox/addon/builtwith/?utm_source=addons.mozilla.org&utm_medium=referral&utm_content=search)

BuiltWith 是一个网站分析工具。在查找页面时，BuiltWith 返回一个列表，该列表可以找到该页面上正在使用的所有技术。

[Wappalyzer](https://addons.mozilla.org/en-US/firefox/addon/wappalyzer/?utm_source=addons.mozilla.org&utm_medium=referral&utm_content=search)

识别网站上的技术。

`whatweb`下一代网络扫描仪。

[HTTrack](https://www.httrack.com/)

HTTrack 是一个离线浏览器实用程序。它允许您将万维网站点从 Internet 下载到本地目录，递归地构建所有目录，将 HTML、图像和其他文件从服务器获取到您的计算机。 HTTrack 安排原始站点的相对链接结构。只需在浏览器中打开“镜像”网站的一个页面，您就可以从一个链接到另一个链接浏览该站点，就好像您在在线查看它一样。

#### Whois Enumeration（Whois 枚举）
WHOIS（发音为“who is”）是一种查询和响应协议，广泛用于查询存储注册用户或Internet资源（例如域名、IP 地址块或自治系统）的受让人的数据库, 但也用于更广泛的其他信息。该协议以人类可读的格式存储和交付数据库内容。

Whois 也是大多数UNIX系统上用于进行 WHOIS 协议查询的命令行实用程序的名称。此外，WHOIS 有一个姊妹协议，称为推荐 Whois ( RWhois )。

`whois`：在类 Unix操作系统上，whois命令是 WHOIS 目录服务的客户端。

[who.is](https://who.is/)：WHOIS 搜索、域名、网站和 IP 工具。

#### Website Footprinting With Netcraft（使用 Netcraft 进行网站足迹）
[netcraft](https://www.netcraft.com/)

`Services->Internet Data Mining->What's that site running?`：使用我们的互联网数据挖掘结果，找出任何网站的技术和基础设施。

#### DNS Recon（DNS侦察）
[Dnsrecon](https://www.kali.org/tools/dnsrecon/)

DNSRecon 是一个 Python 脚本，提供执行以下功能：

* 检查区域转移的所有 NS 记录。
* 枚举给定域（MX、SOA、NS、A、AAAA、SPF 和 TXT）的一般 DNS 记录。
* 执行常见的 SRV 记录枚举。
* 顶级域 (TLD) 扩展。
* 检查通配符解析。
* 给定域和单词表的蛮力子域和主机 A 和 AAAA 记录。
* 对给定的 IP 范围或 CIDR 执行 PTR 记录查找。
* 检查 DNS 服务器缓存的 A、AAAA 和 CNAME 记录
* Records 在文本文件中提供了要检查的主机记录列表。
* 使用 Google 枚举主机和子域

[DNSdumpster.com](https://dnsdumpster.com/)

DNSdumpster.com 是一个免费的域研究工具，可以发现与域相关的主机。从攻击者的角度寻找可见的主机是安全评估过程的重要组成部分。

#### WAF With wafw00f
[wafw00f](https://github.com/EnableSecurity/wafw00f)

Web 应用程序防火墙指纹识别工具。

#### Subdomain Enumeration With Sublist3r（使用 Sublist3r 进行子域枚举）
[Sublist3r](https://github.com/aboul3la/Sublist3r)

Sublist3r 是一个 python 工具，旨在使用 OSINT 枚举网站的子域。它可以帮助渗透测试人员和漏洞猎手为他们所针对的域收集和收集子域。Sublist3r 使用许多搜索引擎（如 Google、Yahoo、Bing、Baidu 和 Ask）枚举子域。Sublist3r 还使用 Netcraft、Virustotal、ThreatCrowd、DNSdumpster 和 ReverseDNS 枚举子域。

#### Google Dorks
[Advanced Search](https://www.google.com/advanced_search)

谷歌高级搜索。

[Wayback Machine](https://archive.org/web/)

Wayback Machine 是存储所有站点版本的站点。

[Google Hacking Database](https://www.exploit-db.com/google-hacking-database)

#### Email Harvesting With theHarvester（使用 theHarvester 进行电子邮件收割）
[theHarvester](https://github.com/laramies/theHarvester)

theHarvester 是一个简单易用但功能强大的工具，设计用于红队评估或渗透测试的侦察阶段。它执行开源情报 (OSINT) 收集，以帮助确定域的外部威胁形势。该工具使用多个公共资源收集姓名、电子邮件、IP、子域和 URL。

#### Leaked Password Databases（泄露的密码数据库）
[have i been pwned?](https://haveibeenpwned.com/)

检查您的电子邮件或电话是否存在数据泄露。

### DNS Zone Transfers（DNS 区域传输）

参考：[DNS Zone Transfers](https://r3kind1e.github.io/2022/08/11/DNS-Zone-Transfers/)

**DNS**

域名系统 (DNS) 是一种用于将域名/主机名解析为 IP 地址的协议。
在互联网的早期，用户必须记住他们想要访问的站点的 IP 地址，DNS 通过将域名（更容易记住）映射到他们各自的 IP 地址来解决这个问题。
DNS 服务器（名称服务器）就像一个电话簿，其中包含域名及其对应的 IP 地址。
Cloudflare (1.1.1.1) 和 Google (8.8.8.8) 等公司已经建立了大量的公共 DNS 服务器。 这些 DNS 服务器包含 Internet 上几乎所有域的记录。

**DNS Records**

A - 将主机名或域解析为 IPv4 地址。
AAAA - 将主机名或域解析为 IPv6 地址。
NS - 对域名服务器的引用。
MX - 将域解析为邮件服务器。
CNAME - 用于域别名。
TXT - 文本记录。
HINFO - 主机信息。
SOA - 域权限。
SRV - 服务记录。
PTR - 将 IP 地址解析为主机名。

**DNS Interrogation（DNS 询问）**

DNS 询问是枚举特定域的 DNS 记录的过程。
DNS 询问的目的是探测 DNS 服务器，为我们提供特定域的 DNS 记录。
此过程可以提供重要信息，例如域的 IP 地址、子域、邮件服务器地址等。

**DNS Zone Transfer**

在某些情况下，DNS 服务器管理员可能希望将区域文件从一台 DNS 服务器复制或传输到另一台。 这个过程被称为区域传输。
如果配置错误且不安全，攻击者可能会滥用此功能将区域文件从主 DNS 服务器复制到另一个 DNS 服务器。
DNS 区域传输可以为渗透测试人员提供组织网络布局的整体视图。
此外，在某些情况下，可能会在组织的 DNS 服务器上找到内部网络地址。

#### 工具
[DNSdumpster.com](https://dnsdumpster.com/)

DNSdumpster.com 是一个免费的域研究工具，可以发现与域相关的主机。从攻击者的角度寻找可见的主机是安全评估过程的重要组成部分。

[dnsenum](https://www.kali.org/tools/dnsenum/)

查看DNS域名枚举文件：`/usr/share/dnsenum/dns.txt`。

Dnsenum 是一个多线程 perl 脚本，用于枚举域的 DNS 信息并发现非连续 IP 块。Dnsenum 的主要目的是尽可能多地收集有关域的信息。该程序当前执行以下操作：

1. 获取主机地址（A 记录）。
2. 获取 namservers（线程化）。
3. 获取 MX 记录（线程化）。
4. 在名称服务器上执行 axfr 查询并获取 BIND 版本（线程）。
5. 通过 google 抓取获取额外的名称和子域（google query = “allinurl: -www site:domain”）。
6. 来自文件的蛮力子域，也可以对具有 NS 记录（所有线程）的子域执行递归。
7. 计算 C 类域网络范围并对它们执行 whois 查询（线程）。
8. 对网络范围（C 类或/和 whois 网络范围）（线程）执行反向查找。
9. 写入 domain_ips.txt 文件 ip-blocks。

该程序对渗透测试者、道德黑客和取证专家很有用。它也可以用于安全测试。

`dig`命令代表Domain Information Groper。它用于检索有关 DNS 名称服务器的信息。它基本上由网络管理员使用。它用于验证和解决 DNS 问题以及执行 DNS 查找。Dig 命令取代了旧工具，例如nslookup和host。

[Dnsrecon](https://www.kali.org/tools/dnsrecon/)

[Fierce](https://www.kali.org/tools/fierce/)

Fierce 是一款半轻量级扫描器，可帮助针对指定域定位不连续的 IP 空间和主机名。它实际上是作为 nmap、unicornscan、nessus、nikto 等的前导，因为所有这些都要求您已经知道要查找的 IP 空间。这不会执行漏洞利用，也不会不加选择地扫描整个互联网。它专门用于定位公司网络内部和外部的可能目标。因为它主要使用 DNS，所以您经常会发现配置错误的网络会泄漏内部地址空间。

### Host Discovery and Port Scanning With Nmap（使用 Nmap 进行主机发现和端口扫描）
参考：[Host Discovery and Port Scanning With Nmap](https://r3kind1e.github.io/2022/08/12/Host-Discovery-and-Port-Scanning-With-Nmap/)

#### 主机发现

[主机发现](https://nmap.org/book/man-host-discovery.html)

`-sn`：在旧版本的Nmap中，也被称为`-sP`。(Ping扫描) 该选项告诉Nmap仅仅进行ping扫描 (主机发现)，然后打印出对扫描做出响应的那些主机。

#### 端口扫描

 `-Pn`：禁用主机发现。

[端口规格和扫描顺序](https://nmap.org/book/man-port-specification.html)

 `-F`：快速（有限端口）扫描

指定您希望扫描的端口数少于默认值。通常 Nmap 会为每个扫描的协议扫描最常见的 1,000 个端口。随着-F，这减少到 100。

[端口扫描技术](https://nmap.org/book/man-port-scanning-techniques.html)

`-sU`：UDP 扫描

虽然 Internet 上最流行的服务运行在 TCP 协议上，但UDP服务被广泛部署。DNS、SNMP 和 DHCP（注册端口 53、161/162 和 67/68）是最常见的三个。

`-v`：增加输出的详细程度。

[服务和版本检测](https://nmap.org/book/man-version-detection.html)

`-sV`：版本检测

启用版本检测。或者，您可以使用`-A`，它可以启用版本检测等。

[操作系统检测](https://nmap.org/book/man-os-detection.html)

`-O`：启用操作系统检测

启用操作系统检测。或者，您可以使用`-A`启用操作系统检测以及其他功能。

[Nmap 脚本引擎 (NSE)](https://nmap.org/book/man-nse.html)

`-sC`：使用默认脚本集执行脚本扫描。它相当于`--script=default`。

[时间和性能](https://nmap.org/book/man-performance.html)

`-T paranoid|sneaky|polite|normal|aggressive|insane` （设置时序模板）

`-T3`是默认模式。建议使用`-T4`。

[Nmap 输出格式](https://nmap.org/book/man-output.html)

`-oN <filespec>`（正常输出）

请求将正常输出定向到给定文件名。如上所述，这与interactive output略有不同。

## Assessment Methodologies: Footprinting & Scanning（评估方法：足迹和扫描）
参考：[Footprinting & Scanning](https://r3kind1e.github.io/2022/08/14/Footprinting-&-Scanning/)

本课程涵盖主机和网络指纹识别和扫描。在本课程中，您将了解端点检测和识别的不同方法。这是渗透测试者开始访问网络的第一步。它模仿对手使用的战术和技术。它对于资源清单审计和漏洞管理操作也很有价值。所有网络安全专业人员都应该熟悉这些步骤，因为他们对缓解和管理资源有一定的了解。

Learning Objectives（学习目标）
* Know the purpose of network mapping and port scanning with reference to an engagement.（参考约定了解网络映射和端口扫描的目的。）
* Perform network host discovery.（执行网络主机发现）
* Perform host port scanning.（执行主机端口扫描。
）

### Mapping a Network（映射网络）
#### Purpose（目的）
* Scope（范围）
* Discovery（发现）

#### Process（过程）
* Physical Access（物理访问）
    * Physical Security（物理安全）
    * OSINT
    * Social Engineering（社会工程学）
* Sniffing（嗅探）
    * Passive Reconnaissance（被动侦察）
    * Watch network traffic（观察网络流量）
* ARP
    * Address Resolution Protocol (RFC 826)（地址解析协议）
        * Resolve IP to MAC（将 IP 解析为 MAC）
* ICMP
    * Internet Control Message Protocol(RFC 792)（互联网控制消息协议）
    * Traceroute（跟踪路由）
    * Ping
        * Type 8 - echo request（类型 8 - 回显请求）

#### Tools（工具）
[wireshark](https://www.wireshark.org/)

Wireshark是世界上最重要和广泛使用的网络协议分析仪。

[arp-scan](https://www.kali.org/tools/arp-scan/)

arp-scan 是一个命令行工具，它使用 ARP 协议来发现和识别本地网络上的 IP 主机。

`ping`

[Fping](https://www.kali.org/tools/fping/)

fping 是一个类似 ping 的程序，它使用 Internet 控制消息协议 (ICMP) 回显请求来确定目标主机是否正在响应。fping 与 ping 的不同之处在于您可以在命令行上指定任意数量的目标，或者指定一个包含要 ping 的目标列表的文件。fping 不会在超时或回复之前发送到一个目标，而是发送一个 ping 数据包并以循环方式移动到下一个目标。

`nmap`

[Zenmap](https://nmap.org/zenmap/)

Zenmap 是官方的 Nmap 安全扫描器 GUI。

### Port Scanning（端口扫描）
#### Purpose
* Identify Operating System（识别操作系统）
    * Revealed by Signatures（签名揭示）
    * Revealed by Services（服务揭示）
* Identify Services（识别服务）
    * Try to connect to ports（尝试连接到端口）
    * Check for responses（检查响应）

#### Process
##### Identify Operating System（识别操作系统）
* Identify Operating System（识别操作系统）
    * Send requests（发送请求）
    * Examine response（检查响应）
    * Compare to signature database（与签名数据库比较）

![identify-operating-system-send-requests.png](/img/in-post/ine/identify-operating-system-send-requests.png)

![examine-response-compare-to-signature-database.png](/img/in-post/ine/examine-response-compare-to-signature-database.png)

##### Scanning for Services（扫描服务）
**Connect to TCP**

![connect-to-tcp.png](/img/in-post/ine/connect-to-tcp.png)

![connect-to-tcp-1.png](/img/in-post/ine/connect-to-tcp-1.png)

![connect-to-tcp-stealty.png](/img/in-post/ine/connect-to-tcp-stealty.png)

![connect-to-tcp-service-version.png](/img/in-post/ine/connect-to-tcp-service-version.png)

**Connect to UDP**
* Slower
* Open|filtered
* Can be speed up

#### Other Tools
[Zenmap - GUI NMAP](https://nmap.org/zenmap/)

[NMAP Automator](https://github.com/21y4d/nmapAutomator)

[masscan](https://github.com/robertdavidgraham/masscan)

[RustScan](https://github.com/RustScan/RustScan)

[AutoRecon](https://github.com/Tib3rius/AutoRecon)

## Assessment Methodologies: Enumeration（评估方法：枚举）
* Servers and Services（服务器和服务）
* SMB
* FTP
* SSH
* HTTP
* SQL

**Learning Objectives（学习目标）**

* Know purpose of service enumeration.（了解服务枚举的目的）
* Identify common services and protocols.（识别常见的服务和协议）
* Perform service enumeration on common services and protocols.（对常用服务和协议执行服务枚举）
* Understand process for enumerating unfamiliar protocols and services.（了解枚举不熟悉的协议和服务的过程）

### SMB
[SMB Windows Discover & Mount](https://r3kind1e.github.io/2022/08/14/SMB-Windows-Discover-&-Mount/)

[SMB Nmap Scripts](https://r3kind1e.github.io/2022/08/15/SMB-Nmap-Scripts/)

[SMB SMBMap](https://r3kind1e.github.io/2022/08/18/SMB-SMBMap/)

[SMB Dictionary Attack](https://r3kind1e.github.io/2022/08/20/SMB-Dictionary-Attack/)

[SMB Samba 1](https://r3kind1e.github.io/2022/08/18/SMB-Samba-1/)

[SMB Samba 2](https://r3kind1e.github.io/2022/08/19/SMB-Samba-2/)

[SMB Samba 3](https://r3kind1e.github.io/2022/08/19/SMB-Samba-3/)

### FTP
[FTP Dictionary Attack](https://r3kind1e.github.io/2022/08/21/FTP/)

[FTP Anonymous Login](https://r3kind1e.github.io/2022/08/22/FTP-Anonymous-Login/)

### SSH
[SSH](https://r3kind1e.github.io/2022/08/22/SSH/)

[SSH Dictionary Attack](https://r3kind1e.github.io/2022/08/22/SSH-Dictionary-Attack/)


### HTTP
[HTTP IIS](https://r3kind1e.github.io/2022/08/23/HTTP-IIS/)

[HTTP IIS Nmap Scripts](https://r3kind1e.github.io/2022/08/23/HTTP-IIS-Nmap-Scripts/)

[HTTP Apache](https://r3kind1e.github.io/2022/08/25/HTTP-Apache/)

### SQL
[MySQL](https://r3kind1e.github.io/2022/08/26/MySQL/)

[MySQL Dictionary Attack](https://r3kind1e.github.io/2022/08/26/MySQL-Dictionary-Attack/)

[MSSQL Nmap Scripts](https://r3kind1e.github.io/2022/08/27/MSSQL-Nmap-Scripts/)

[MSSQL Metasploit](https://r3kind1e.github.io/2022/08/28/MSSQL-Metasploit/)

## Assessment Methodologies: Vulnerability Assessment（评估方法：漏洞评估）
[Vulnerability Assessment](https://r3kind1e.github.io/2022/06/21/Vulnerability-Assessment/)

[Vulnerability Research Lab](https://r3kind1e.github.io/2022/08/29/Vulnerability-Research-Lab/)