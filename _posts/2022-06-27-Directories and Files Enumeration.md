---
layout: post
title: "Directories and Files Enumeration"
date: "2022-06-27"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Web Attacks
---

# Directories and Files Enumeration（目录和文件枚举）
这如何支持我的渗透测试生涯？
有能力：
* 查找和利用测试功能
* 利用备份或旧文件中保存的信息
* 寻找隐藏资源

用户或搜索引擎无法在 Internet 上找到未通过网页链接的资源。
如果网站管理员在 /new 子目录中创建了一个新版本的网站，那么在网站管理员发布指向该网站的链接之前，没有人会找到它。

但即使是未链接的文件和目录，任何知道其 URL 的人都可以访问。
参考前面的示例，Beta 测试人员可以通过在 Web 浏览器中打开 `http://site.com/new` 来访问新版本的网站。

枚举（Enumeration）可帮助您找到那些通常包含以下内容的“隐藏”资源：
* 新的和未经测试的功能
* 备份文件
* 测试信息
* 开发人员说明

以及许多其他类型的信息留在那里，因为“没有人知道他们的 URL”。

发现未发布的旧文件或备份文件可以为您提供大量信息，有时还可以访问非常敏感的文件。
事实上，程序员经常将备份文件留在服务器上。这些可能包含敏感信息，例如后端数据库服务器的 IP 地址或用于测试功能的凭据。

在下面的幻灯片中，您将看到如何枚举和访问这些资源并利用这些信息来执行您的测试。
枚举资源有两种方式：
* 纯粹的蛮力
* 字典攻击

## Brute-force Enumeration（蛮力枚举）
纯**暴力破解**非常简单；你必须尝试所有可能的字符组合；这是测试**每个可能**的资源名称的唯一方法。另一方面，这种方法效率很低，因为您将测试大量不存在的资源。
您需要 **287979** 次试验才能通过仅使用小写字符的蛮力测试找到字符串 **“home”**。

## Dictionary-based Enumeration（基于字典的枚举）
测试每个可能的字符组合以枚举资源是耗时的。
通过了解人们倾向于给文件和目录起哪些常用名称，以及它们可能具有哪些常见扩展名，我们可以优化我们的搜索。

因此，另一种更快的枚举资源的方法是使用常见文件名、目录名和文件扩展名的列表。
一些常见的备份文件名是：`.bak`、`.old`、`.txt` 和 `.xxx`。

使用字典枚举 Web 资源比使用纯暴力更有效。
我们在实验室进行了测试，并且：
* 对**整个网站**执行基于字典的递归枚举大约需要 **2000 个请求**。
* 通过蛮力枚举像 /score 这样的**单个资源**需要超过**八千个请求！**

## Enumerating Web Resources with Dirbuster（使用 Dirbuster 枚举 Web 资源）
即使您可以使用字典，手动测试所有常见资源名称和扩展名也是不切实际的。幸运的是，这个测试过程可以自动化。
执行 Web 枚举的一个非常常用的工具是 **OWASP Dirbuster。**

Dirbuster 是一个可以执行 Web 资源枚举的 java 应用程序。
你可以在[这里](https://owasp.org/projects/)下载 Dirbuster。

要使用它，您必须设置**目标**（即您要测试的站点的 URL）。

然后，您可以选择是要执行纯暴力破解还是基于字典的暴力破解。

通过单击“浏览”按钮，您可以指定要用于测试的列表。

然后，您可以设置测试选项。

## Enumerating Web Resources with Dirb（使用 Dirb 枚举 Web 资源）
Dirbuster 有一个 Linux 替代品，称为 Dirb。
Dirb 是一个命令行工具，它还有助于枚举应用程序中的 Web 资源。

## References
[Dirbuster](https://owasp.org/projects/)