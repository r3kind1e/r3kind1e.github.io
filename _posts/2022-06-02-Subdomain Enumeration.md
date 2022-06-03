---
layout: post
title: "Subdomain Enumeration"
date: "2022-06-03"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Information Gathering
---

# Subdomain Enumeration
我们之前已经讨论过扩大攻击面。另一种方法是发现公司拥有的尽可能多的网站。

同一家公司的网站共享相同的顶级域名是很常见的。例如，careers.company.com、mail.company.com 或 business.company.com
通过子域枚举，渗透测试人员可以识别目标的其他资源。

这些资源很可能包含过时的、有缺陷的软件、敏感信息，甚至是不安全的管理界面。
如果你看一下一些错误赏金程序的文章，你很快就会得出结论，即使在有声望的公司中，这种被遗忘的资源也很常见。

在本课程中，我们将重点介绍被动子域枚举。
这意味着我们将尝试在不直接与目标交互的情况下，而是通过开源来识别子域。

这样的开源，可以是搜索引擎。
例如，当 Google 执行其索引活动时，它可能会索引不用于索引的页面。渗透测试人员可以利用它来被动地识别一些目标子域。
您可以通过在 Google 的搜索引擎中输入以下内容来尝试此操作
`site: company.com`

通过键入主要公司域，您将看到为该公司找到的任何子域。

另一个可以促进被动子域枚举的开源是 [dnsdumpster.com](https://dnsdumpster.com/)，它利用来自 google 索引的子域的数据，但也检查 Bing 或 [virustotal](https://www.virustotal.com/gui/home/search) 等网站以获取类似信息。

[crt.sh](https://crt.sh/)，它收集有关用于某些域和子域的 SSL 证书的信息。这些证书可以被搜索和查看。

![crt.sh](/img/in-post/ine/crtsh.png)

如果您遇到子域并在浏览器中访问它，您可以尝试使用**查看证书功能**从中提取另一个子域。

![Certificate Subject Alt Name](/img/in-post/ine/CertificateSubjectAltName.png)


还有一个工具可以扩展 DNS 枚举的功能，叫做 sublist3r。
它预装在最新的 Kali Linux 版本上，但也可以从其 [github 存储库](https://github.com/aboul3la/Sublist3r)下载。

在其基本用法中，sublist3r 将从各种来源收集 DNS 数据。
但是，您应该知道它很容易被谷歌阻止（它的搜索引擎不喜欢自动化工具）。因此，您应该明智地使用它。

```
python3 sublist3r.py -h    

usage: sublist3r.py [-h] -d DOMAIN [-b [BRUTEFORCE]] [-p PORTS] [-v [VERBOSE]] [-t THREADS] [-e ENGINES] [-o OUTPUT] [-n]

OPTIONS:
  -h, --help            show this help message and exit
  -d DOMAIN, --domain DOMAIN
                        Domain name to enumerate it's subdomains
  -b [BRUTEFORCE], --bruteforce [BRUTEFORCE]
                        Enable the subbrute bruteforce module
  -p PORTS, --ports PORTS
                        Scan the found subdomains against specified tcp ports
  -v [VERBOSE], --verbose [VERBOSE]
                        Enable Verbosity and display results in realtime
  -t THREADS, --threads THREADS
                        Number of threads to use for subbrute bruteforce
  -e ENGINES, --engines ENGINES
                        Specify a comma-separated list of search engines
  -o OUTPUT, --output OUTPUT
                        Save the results to text file
  -n, --no-color        Output without color

Example: python sublist3r.py -d google.com

```

```
locate subbrute
```

```
cat subbrute/names.txt | wc -l
129407
```
使用 `-d [domain]` 标志运行 sublist3r 后，它开始在各种来源中搜索子域。

`python3 sublist3r.py -v -d google.com -b`

`-v`: Enable the verbose mode and display results in realtime

`-d`: Domain name to enumerate subdomains of

`-b`: Enable the subbrute bruteforce module

在Google中搜索关键词：`domain wordlist`，你可以看到很多子域名字典：[rbsec/dnscan](https://github.com/rbsec/dnscan)

您应该注意通配符 DNS，通配符 DNS 是服务器的行为，或者任何名称都被解析为特定的 IP 地址。例如，即使输入像garbage.domain.com 这样的随机内容也会将您带到公司的主页，这通常会导致误报。在找到一个域的数百万个子域之前，请记住这一点。

还有一个枚举子域名的项目：amass，为了在kali linux上安装它，我们首先需要安装snap包管理器，它是能最简单的让amass运行的方法。

```
apt install snapd
service snapd start
snap install amass
snap run amass或者amass
```
下面简单介绍一下amass的部分选项：

`-ip`: Show the IP addresses for discovered names	`amass enum -ip -d example.com`

```
amass enum -ip -d yahoo.com
```

上述子域枚举技术将帮助您识别公开可用的目标资产。
它们中的每一个都可能容易受到攻击，应在渗透测试期间进行彻底检查。

## References
[dnsdumpster.com](https://dnsdumpster.com/)

[Sublist3r](https://github.com/aboul3la/Sublist3r)

# The Importance of Information Gathering
在关闭这个模块之前，我们想强调一下信息收集的重要性！

信息收集的强大阶段决定了渗透测试人员的好坏。
一个优秀的渗透测试人员会花费 90% 的时间来扩大攻击面，因为他知道这就是它的全部意义所在。剩下的 10% 只是使用适当的工具以高成功率启动正确命令的问题。

假设你在玩飞镖。
您是希望对微观目标进行 1000 次射击，还是对不可能错过的大目标进行一次射击。

最后，由于渗透测试是循环过程，每次您都会更深入地了解客户的基础设施，从而获得更多信息。

每个信息收集阶段都需要与第一个阶段相同的重点和奉献精神。
您的渗透测试将**与您最弱的技能一样强大**，所以不要低估信息收集！