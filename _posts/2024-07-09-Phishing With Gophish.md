---
layout: post
title: "Phishing With Gophish"
date: "2024-07-09"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Professional
    - Resource Development & Initial Access
    - Client-Side Attacks
---

# Phishing With Gophish 使用Gophish进行网络钓鱼
## Gophish
GoPhish is an open-source phishing framework designed for penetration testers and security professionals to simulate phishing attacks against their own organizations.

It provides a user-friendly platform to create, execute, and analyze phishing campaigns, allowing users to assess their organization's susceptibility to phishing attacks and improve their security posture.

GoPhish is a powerful tool for penetration testers and security professionals to conduct phishing assessments, educate employees about phishing risks, and strengthen the organization's defenses against social engineering attacks.

GoPhish是一个为渗透测试人员和安全专家设计的开源网络钓鱼框架，用于模拟对自己组织的钓鱼攻击。

它提供了一个用户友好的平台，用于创建、执行和分析钓鱼活动，允许用户评估他们的组织对钓鱼攻击的敏感性，并提高其安全态势。

GoPhish是一个强大的工具，供渗透测试人员和安全专业人员进行钓鱼评估，教育员工了解钓鱼风险，并加强组织对社会工程攻击的防御。

## Gophish Features Gophish功能
Campaign Creation: GoPhish allows users to create customized phishing campaigns tailored to their specific objectives and targets. Users can create multiple campaigns with different templates, email content, and target lists.

Email Template Editor: The platform provides a built-in email template editor with a WYSIWYG (What You See Is What You Get) interface, making it easy to design professional-looking phishing emails that mimic legitimate communications.

活动创建：GoPhish允许用户创建定制的钓鱼活动，这些活动针对他们的具体目标和对象。用户可以使用不同的模板、电子邮件内容和目标列表创建多个活动。

电子邮件模板编辑器：该平台提供了一个内置的电子邮件模板编辑器，配备了所见即所得（WYSIWYG）界面，使得设计看起来专业的钓鱼电子邮件变得简单，这些电子邮件模仿了合法通信。

Target Management: Users can manage their target lists and segment them based on various criteria, such as department, role, or location. This allows for targeted phishing campaigns that closely mirror real-world attack scenarios.

Landing Page Creation: GoPhish enables users to create phishing landing pages that mimic legitimate login portals or websites. These landing pages can be customized to capture credentials, personal information, or other sensitive data from targets.

目标管理：用户可以管理他们的目标列表，并根据不同的标准（如部门、角色或位置）进行细分。这允许针对性的钓鱼活动，更加贴近真实世界的攻击场景。

登陆页面创建：GoPhish使用户能够创建模仿合法登录门户或网站的钓鱼登陆页面。这些登陆页面可以自定义，以捕获目标的凭据、个人信息或其他敏感数据。

Tracking and Reporting: The platform provides comprehensive tracking and reporting capabilities, allowing users to monitor the progress of their phishing campaigns in real-time. Users can track email opens, link clicks, and submitted data, and generate detailed reports for analysis.

Scheduling and Automation: GoPhish supports campaign scheduling and automation, allowing users to schedule campaign launches at specific dates and times or set up recurring campaigns for ongoing testing and assessment.

追踪和报告：该平台提供全面的追踪和报告功能，允许用户实时监控他们的钓鱼活动的进展。用户可以追踪电子邮件的打开情况、链接点击和提交的数据，并生成详细的报告进行分析。

计划和自动化：GoPhish支持活动的计划和自动化，允许用户在特定的日期和时间安排活动启动，或设置重复活动，以进行持续的测试和评估。

## References & Resources
Gophish Website: https://getgophish.com/

Gophish GitHub Repo: https://github.com/gophish/gophish

Gophish Installation Guide: https://docs.getgophish.com/userguide/installation

## Lab Demo: Phishing With Gophish
gophish.exe: the executable that'll start up the gofish server.

Mozilla Thunderbird: that's configured with a working email of a target employee.

Password Reset Email.txt: the email template that we're going to be using.

targets.csv: A list of targets.

GoPhish -> templates:

base.html, login.html, : get rid of the link of googleapis fonts.

When configuring Go Phish, you obviously have the ability to set up an SSL certificate.

config.json:

You can also disable TLS. Set use TLS for the admin_server to false.

"admin_server" -> "use_tls": false

"phish_server":

We'll be hosting any malicious login or landing pages.

```
netstat -ano
```

`netstat -ano` 是一个在 Windows PowerShell 中运行的命令，用于显示网络连接、路由表以及每个连接的协议类型、状态、IP 地址、端口以及相关进程的ID。这个命令的各个选项意义如下：

- **`-a`** （all）: 显示所有活动的 TCP 连接及监听的端口。
- **`-n`** （numeric）: 以数字形式显示地址和端口号，而不是尝试解析为名称。
- **`-o`** （owning process ID）: 显示每个连接的拥有进程ID。

使用这条命令，可以帮助管理员或安全专家监控和诊断网络连接问题，以及检查系统中可能存在的未授权或异常连接。

