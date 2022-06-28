---
layout: post
title: "Google Hacking"
date: "2022-06-28"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Web Attacks
---

# Google Hacking
这如何支持我的渗透测试生涯？
* 在不联系目标的情况下进行信息收集
* 寻找隐藏资源的能力

在网站上查找文件和目录的另一种方法是使用高级搜索引擎功能。
渗透测试人员可以使用 Google 的高级查询命令（也称为 **Google Dorks**）来查找特定资源。

Google dorks 是用于查找特定资源或网页的 Google 命令的特殊组合。

在下图中，您可以看到如何找到一些管理区域登录页面。

```
inurl: admin intitle:login
```

|命令|含义|
|---|---|
|site:|您可以使用此命令仅包含给定主机名的结果。|
|intitle:|此命令根据页面标题进行过滤。|
|inurl:|与 intitle 类似，但适用于资源的 URL。|
|filetype:|这使用资源的文件扩展名进行过滤。例如 .pdf 或 .xls。|
|AND, OR, &, \||您可以使用逻辑运算符来组合您的表达式。例如：site:example.com OR site:another.com|
|-|您可以使用此字符从查询中过滤掉关键字或命令的结果。|

组合命令可能会导致一些非常复杂的表达式。

```
-inurl: (htm|html|php|asp|jsp) intitle: "index of" "last modified" "parent directory" txt OR doc OR pdf
```

上述命令从搜索结果中排除常见的网页扩展名，并查找包含 txt、doc 或 pdf 文件的打开目录索引。

您可以通过阅读[本书](https://www.amazon.com/Google-Hacking-Penetration-Testers-Johnny/dp/1597491764/ref=sr_1_1?ie=UTF8&qid=1302083660&sr=8-1)、查看[官方文档](https://developers.google.com/custom-search/docs/xml_results)或查询 [Google Hacking Database](https://www.exploit-db.com/google-hacking-database) 找到有关如何使用 Google 发现隐藏资源的更多信息。

## References
[Google Hacking for Penetration Testers](https://www.amazon.com/Google-Hacking-Penetration-Testers-Johnny/dp/1597491764/ref=sr_1_1?ie=UTF8&qid=1302083660&sr=8-1)

[Advanced Google Commands](https://developers.google.com/custom-search/docs/xml_results)

[Google Hacking Database](https://www.exploit-db.com/google-hacking-database)