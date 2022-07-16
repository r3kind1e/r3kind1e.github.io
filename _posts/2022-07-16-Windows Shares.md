---
layout: post
title: "Windows Shares"
date: "2022-07-16"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# Windows Shares - Study Guide（Windows 共享 - 学习指南）
这如何支持我的渗透测试生涯？
有能力：
* 枚举网络资源
* 攻击 Windows 会话
* 获得对 Windows 资源的未经授权的访问

Microsoft Windows 是企业网络上最常用的操作系统之一。 它可以在客户端和服务器上使用，以提供身份验证、文件共享、打印机管理和其他功能。
在本模块中，您将了解 Windows 文件共享的工作原理以及如何利用其某些功能。

## NetBIOS
为了理解 Windows 共享攻击，我们必须首先了解共享是如何工作的。
在接下来的幻灯片中，我们将讨论 NetBIOS 及其在 MS Windows 网络中的作用； 此外，您将了解什么是共享以及如何使用它们。

**NetBIOS** 代表网络基本输入输出系统。 服务器和客户端在查看局域网上的网络共享时使用 NetBIOS。

![computer-media-devices.png](/img/in-post/ine/computer-media-devices.png)

查询计算机时，NetBIOS 可以提供以下一些信息：
* 主机名
* NetBIOS 名称
* 域名
* **网络共享**

此框图表示 NetBIOS 的结构。
如您所见，NetBIOS 层位于应用层和 IP 层之间。

* NetBIOS 会话
* NetBIOS 数据报
* NetBIOS 名称
* 基于 TCP/IP 的 NetBIOS
* NetBIOS API
* NetBIOS 应用程序

![structure-of-netbios.png](/img/in-post/ine/structure-of-netbios.png)

UDP 用于执行 NetBIOS **名称解析**并承载其他基于数据报的**一对多**通信。
通过使用 NetBIOS 数据报，主机可以向许多其他主机发送小消息。

繁重的流量（例如文件复制）通过使用 NetBIOS **会话**依赖于 TCP。

请注意不同的功能如何使用不同的端口和传输协议。

当 MS Windows 机器浏览网络时，它使用 NetBIOS：
* 列出共享和机器的**数据报**
* 查找工作组的**名称**
* 将数据传输到 **Windows 共享**和从 Windows 共享传输数据的**会话**

## Shares（共享）
Windows 机器可以在网络上共享文件或目录； 这允许本地和远程用户访问资源，并可能修改它。
例子：
办公室中的文件服务器允许用户打开和编辑本部门的文件，同时它允许每个人阅读但不能修改公共信息文件。

此功能在网络环境中非常有用。 共享资源和文件的能力减少了冗余并可以提高公司的工作效率。
如果使用得当，共享可能非常有用，如果配置不当，共享可能会非常危险。

在基于 Windows 的环境中创建网络共享相当容易。 一般情况下，用户只需打开文件和打印机共享服务，就可以开始选择要共享的目录或文件。
用户还可以对共享设置权限，选择谁可以执行读取、写入和修改权限等操作。

从 Windows Vista 开始，用户可以选择共享单个文件或使用公共目录。 共享单个文件时，他们可以选择本地或远程用户来共享文件。
使用公共目录时，可以选择哪些本地用户可以访问共享上的文件，但只能允许网络中的所有人或任何人都不能访问共享。

## UNC Paths（UNC路径）
授权用户可以使用通用命名约定路径（UNC 路径）访问共享。
UNC 路径的格式为：

```
\\ServerName\ShareName\file.nat
\\服务器名称\共享名\file.nat
```

## Administrative Shares（管理共享）
还有一些特殊的默认**管理共享**供系统管理员和 Windows 本身使用：
* `\\ComputerName\C$` 允许管理员访问本地计算机上的卷。 每个卷都有一个共享（C$、D$、E$ 等）
* `\\ComputerName\admin$` 指向windows安装目录
* `\\ComputerName\ipc$` 用于进程间通信。 您无法通过 Windows 资源管理器浏览它。

您可以通过在 Windows 资源管理器地址栏中输入以下内容来测试计算机上的卷共享和 admin$ 共享：`\\localhost\<sharename>`

```
\\localhost\admin$
\\localhost\d$
```

## Badly Configured Shares（配置错误的共享）
访问共享意味着可以访问托管它的计算机的资源。 因此，配置不当的共享利用可能会导致：
* 信息披露
* 未经授权的文件访问
* 用于发起针对性攻击的信息泄露

在下一章中，我们将讨论一个历史性的 Windows 漏洞：空会话。