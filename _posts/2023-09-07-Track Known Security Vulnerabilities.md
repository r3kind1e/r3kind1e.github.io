---
layout: post
title: "Track Known Security Vulnerabilities"
date: "2023-09-07"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Career
---

# Track Known Security Vulnerabilities
来源：[Five Key Websites That Track Known Security Vulnerabilities](https://www.hcltech.com/blogs/five-key-websites-track-known-security-vulnerabilities)

[麻省理工学院(MITRE)](https://cve.mitre.org/)
由美国国土安全部国家网络安全司(DHS)提供资金支持的MITRE组织协调CVE数据库。这使得上面的链接成为获取最新准确的CVE信息的可靠资源，但可能需要一些时间来学习如何访问所需的信息。

[美国国家标准与技术研究院(NIST) National Institute for Standards and Technology (NIST)](https://nvd.nist.gov/vuln/full-listing)
该网站由美国国家标准与技术研究院(NIST)托管。该链接指向NIST国家漏洞数据库(NVD)，提供了一个按时间顺序列出CVE的完整列表，从最新到最旧。通过深入研究，您可以看到，截至12月7日，我们已经有160个官方列出的2020年12月CVE。进一步浏览首页提供了许多选项，但与MITRE的网站一样，其导航和使用可能一开始并不直观。

[CVE](https://www.cvedetails.com/index.php)
在我看来，这个链接提供了一个更加用户友好的CVE信息界面。左边边缘的菜单提供了按供应商、产品、日期和类型轻松浏览CVE的选项。然而，这个网站由一家私人安全顾问维护，可能不总是像前两个列出的网站那样及时或准确。

[微软安全更新指南 Microsoft Security Update Guide](https://msrc.microsoft.com/update-guide/vulnerability)
这个链接指向微软的安全更新指南漏洞列表，每列都可以反向排序以方便浏览。导航到更高一级提供了一个与相关的Microsoft KB文章链接到CVE的列表，这对于修补非常有价值。

[苹果 Apple](https://support.apple.com/)
尽管这并不是正式的安全漏洞资源，但搜索功能对获取有关苹果产品的信息非常方便。与普遍观念不同，苹果产品仍然容易受到漏洞利用，因此这个资源不应被忽视，因为它是查找特定信息的快速方法。您可以在搜索对话框中输入特定的条件以找到相关信息。例如，您可以输入搜索条件IOS 9 CVE，然后选择有关IOS 9 CVE的安全内容。

## bonus links
[MITRE ATT&CK](https://attack.mitre.org/)
是的，负责维护官方CVE数据库的麻省理工学院(MITRE)组织也是一个出色的附加安全信息资源。MITRE维护着MITRE ATT&CK（对抗性战术、技术与共同知识）框架，您可以在此链接找到相关信息。该网站还有一个关于ATT&CK矩阵不同部分的缓解措施的部分。请抽出时间仔细阅读这个网站。

[Zero Day Initiative](https://www.zerodayinitiative.com/advisories/upcoming/)
Zero Day Initiative网站提供了有关安全警报的有用信息，还附带了关于新报告问题的信息。您获取的越早，您就越能有效地保护企业。

[Threatpost](https://threatpost.com/category/vulnerabilities/)
Threatpost是一个很好的网络安全信息来源，包括云安全、恶意软件和漏洞等多个主题的报道。此链接以新闻发布的方式提供了选择性的当前安全漏洞信息，是您在有"空闲"时间时浏览的好网站。

# What are the most useful tools and resources for staying updated on vulnerability trends and threats?
来源：[What are the most useful tools and resources for staying updated on vulnerability trends and threats?](https://www.linkedin.com/advice/0/what-most-useful-tools-resources-staying-updated)

## Vulnerability databases
the National Vulnerability Database (NVD), the Common Vulnerabilities and Exposures (CVE) list, and the Open Vulnerability and Assessment Language (OVAL) repository

## Vulnerability scanners
Nmap, Nessus, OpenVAS, and Qualys

## Vulnerability feeds and newsletters
the US-CERT Cybersecurity Bulletin, the SANS @RISK newsletter, the Threatpost feed, and the Secunia Research blog

## Vulnerability forums and communities
the Stack Exchange Network Security site, the Reddit r/netsec subreddit, the SecurityFocus Bugtraq mailing list, and the OWASP community