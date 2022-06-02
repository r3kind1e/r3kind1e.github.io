---
layout: post
title: "Open-Source Intelligence"
date: "2022-06-02"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Information Gathering
---

# Introduction - Study Guide
欢迎来到课程的**信息收集**部分！
我们从讨论渗透测试的所有阶段开始这个模块，重点介绍专业人员用于执行渗透测试的**工具、技术和方法**。

信息收集是参与的第一个也是最关键的阶段之一。
正如您在先决条件部分已经看到的那样，该过程的这一步可帮助您了解目标组织，扩大攻击面并发起有效且有针对性的攻击。

与黑帽黑客类似，渗透测试人员不能不遗余力。
在本模块中，您将看到如何利用公共信息，从业务和基础设施的角度更深入地了解客户的组织。

# Open-Source Intelligence - Study Guide
这如何支持我的渗透测试生涯？
* 扩大攻击面
* 安装有针对性的攻击
* 锐化你的工具，为下一阶段做准备

直到几年前，收集有关一家公司的信息：
* 从媒体获取信息
* 在电话簿中查找地址和电话号码
* 访问公司网站以获取有关系统和产品的信息

如今，您可以通过利用**社交网络**、**公共网站**上的可用信息以及**访问公司网站**来更有效地做同样的事情。

## Social Networks Information Gathering
在下面的幻灯片中，您将看到如何使用社交网络进行信息收集。 事实上，许多成功的安全漏洞都利用了安全链中最薄弱的环节：人类！
每天，人们开始工作并积极使用公司系统和服务，因此他们在 Internet 上的个人安全状况严重影响了他们工作的公司的安全。

随着社交网络的出现，黑客现在可以访问几年前还很难找到的人员和产品信息。
犯罪分子现在可以（并且实际上）利用这些有价值的信息来发动**复杂的攻击**。

当一家公司的员工在社交网络上发布有关当前项目、会议行程、电话号码和电子邮件地址的信息时，他们实际上是在向犯罪分子提供金矿。
这不仅有助于犯罪分子发动网络钓鱼和假冒攻击，还为他们提供了创建公司使用的**系统和技术**的**技术**地图的手段。

作为专业的渗透测试人员，您也可以通过在 Facebook、Twitter、LinkedIn、Google+ 等常见社交网络上执行查询来做到这一点。
鉴于那里有大量的社交网络，为您的参与选择合适的社交网络取决于特定的客户和参与度。

虽然 LinkedIn、Twitter 和 Facebook 在“通用”镜头方面做得很好，但您必须通过了解目标公司行业以及其员工的一些兴趣来在其他社交网络之间进行选择。
如果客户是一家广告公司，那么查看 Instagram 将是一个好主意。

现在让我们看看如何使用 LinkedIn 的高级搜索来查找 Google 在美国的所有员工。
登录后，您可以通过点击搜索栏右侧的放大镜图标打开高级搜索。

然后，额外的搜索栏出现在底部：
如果您选择“所有过滤器”，您会注意到出现了更多搜索条件：

搜索最终将返回数百个个人资料，以帮助您加深对目标公司的了解。

许多LinkedIn用户还提供个人和工作**电话号码**或**电子邮件**地址，这些号码或电子邮件地址可能被利用来发起社会工程攻击。

许多社交网络允许用户**集成**不同的帐户。
例如，您可以将您的Twitter和LinkedIn帐户链接;此功能会自动将社交网络上的更新发布给其他人，并可以利用它来**查找丢失的信息**。

您可以通过在LinkedIn上执行**网络搜索**来查找匹配的更新，从而找到拥有**Twitter**帐户的人的**真实姓名**。
LinkedIn强制其用户使用其真实姓名。

## Public Sites Information Gathering(公共网站信息收集)
社交网络并不是公司信息的唯一公共来源。还有许多其他有趣的网站和数据库“泄露”有价值的信息。
让我们来看看吧！

### Crunch Base
[CrunchBase](http://www.crunchbase.com/)是一个IT创业公司数据库，您可以在其中找到有关创始人，投资者，员工，收购和收购的详细信息。
您可以按公司名称或人员姓名执行查找。

### Government Sites 政府网站
在互联网上，您可以获得有关与政府合作或目前正在与政府合作的公司的各种信息。
存储在这些网站上的信息因政府而异，因州而异。

例如，美国政府提供了[奖励管理系统](https://sam.gov/content/home)（SAM）和[GSA电子图书馆](https://www.gsaelibrary.gsa.gov/ElibMain/home.do)，您可以使用它们来查找有关私营公司与美国政府之间合同的信息。

### Whois
另一个宝贵的资源是Whois数据库。您可以使用它来获取信息，例如：
* 所有者名称
* 街道地址
* Email地址
* 技术联系人
关于互联网域名。

您可以在 Linux 和 OSX 上使用 whois 命令查询数据库。
如果你运行的是MS Windows，你可以安装[Sysinternal's Whois](https://docs.microsoft.com/zh-cn/sysinternals/downloads/whois)。

在这里，我们看到了您可以通过执行 whois 查找 apple.com 来获取的一些信息。您还可以找到技术联系人。
```
whois apple.com

Domain Name: apple.com
Registry Registrant ID: 
Registrant Name: Domain Administrator
Registrant Organization: Apple Inc.
Registrant Street: One Apple Park Way
Registrant City: Cupertino
Registrant State/Province: CA
Registrant Postal Code: 95014
Registrant Country: US
Registrant Phone: +1.4089961010
Registrant Phone Ext: 
Registrant Fax: +1.4089741560
Registrant Fax Ext: 
Registrant Email: domains@apple.com
```

### Browsing Client's Sites
社交网络，公共网站和whois服务都为您提供碎片化的信息，您可以将这些信息放在一起，以更好地了解客户的业务。

最后，不要忘记，浏览客户的实际网站将为您提供有关以下内容的大量信息：
* 产品
* 服务
* 技术
* 企业文化

许多公司的网站都包含有关董事会简介，当前和未来产品，合作伙伴，供应商，职位空缺等的信息。
您对自己的目标了解得越多，渗透测试的后期阶段就越容易。

### Discovering Email Pattern 发现电子邮件模式
如果没有直接数据库或任何其他公司电子邮件来源（通常也可以用作公司服务的登录名），则可能需要尝试自己识别它们。
您应该记住，公司倾向于为每个员工使用一个特定的电子邮件模式;这使得公司的内部沟通变得更加容易。

通常，没有复杂的模式。电子邮件地址的结构应该是直观的，因此其他员工只需知道他们的同事姓名和/或姓氏即可轻松相互沟通。
以下是典型公司电子邮件地址格式的一些示例：
(注释：surname是姓，name是名字)
* name.surname@company.com
* surname.name@company.com
* [first letter of name]surname@company.com

如果您能够找到公司的员工（即使用他们的官方网站，LinkedIn或其他社交网站），您可能能够猜到他们的电子邮件地址。
许多邮件系统倾向于通知发件人邮件未送达，因为它不存在。
对于渗透测试人员来说，这是猜测企业电子邮件格式的绝佳机会。

您可以先尝试：
收集合理数量的员工数据（姓名/姓氏）
尝试构建一些可能的电子邮件格式，并将它们应用于每个姓名/姓氏对
尝试发送一封不会提醒潜在受害者的电子邮件（例如，不要在主题中放置“网络钓鱼测试”，而是选择一些棘手的东西，例如尝试假装它只是一个广告）

如果您开始收到与以下类似的电子邮件：

您应该仔细检查您发送的哪些电子邮件没有生成此类邮件，这将是具有正确公司邮件架构的电子邮件。