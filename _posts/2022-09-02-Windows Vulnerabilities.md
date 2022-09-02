---
layout: post
title: "Windows Vulnerabilities"
date: "2022-09-02"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - System/Host Based Attacks
---

# Host & Network Penetration Testing: System/Host Based Attacks（主机和网络渗透测试：基于系统/主机的攻击）
利用基于主机的漏洞的能力是您在成为熟练的渗透测试人员的过程中掌握的一项重要技能。 在本课程中，您将了解 Windows 和 Linux 系统中的各种服务、协议和漏洞以及如何利用它们。 您还将了解 Windows 和 Linux 系统中的各种固有文件系统和权限提升漏洞以及如何利用它们。 此外，您还将了解 Windows 和 Linux 系统如何存储密码哈希，以及如何将它们转储以用于密码破解或在 Windows 的情况下进行哈希传递攻击。 本课程的目的是为您提供利用 Windows 和 Linux 系统所需的深入实践技能。 在本课程结束时，您将具备利用 Windows 和 Linux 系统中的固有漏洞所必需的技能。

## 课程主题概述
基于系统/主机的攻击简介

* Windows 漏洞概述
* 利用 Windows 漏洞
* Windows 权限提升
* Windows 文件系统漏洞
* Windows 凭据转储
* Windows横向移动
* Linux 漏洞概述
* 利用 Linux 漏洞
* Linux 权限提升
* Linux 文件系统漏洞
* Linux 凭证转储

## 学习目标
学生将了解影响 Windows 和 Linux 的各种漏洞。

学生将能够识别 Windows 和 Linux 系统上的漏洞利用。

学生将了解如何在 Windows 和 Linux 系统上执行权限提升。

学生将能够识别和利用 Windows 和 Linux 上的文件系统漏洞。

学生将了解如何在 Windows 和 Linux 系统上存储凭据，以及如何转储密码哈希。

# Introduction To System/Host Based Attacks（基于系统/主机的攻击简介）
## 什么是基于系统/主机的攻击？
基于系统/主机的攻击是针对特定系统或运行特定操作系统（例如 Windows 或 Linux）的主机的攻击。

网络服务并不是渗透测试中唯一可以作为攻击目标的攻击媒介。

基于系统/主机的攻击通常在您获得对目标网络的访问权后开始发挥作用，因此，您将需要利用内部网络上的服务器、工作站或笔记本电脑。

## 基于系统/主机的攻击

基于系统/主机的攻击主要集中在利用目标操作系统的固有漏洞。

与基于网络的攻击不同，基于主机的攻击更加专业，需要了解目标操作系统以及影响所述操作系统的漏洞。

基于系统/主机的攻击涉及利用目标操作系统中的错误配置和固有漏洞。

在本课程中，我们将主要关注 Windows 和 Linux 漏洞以及如何利用它们。

# Overview Of Windows Vulnerabilities（Windows 漏洞概述）
## Windows 漏洞简史
Microsoft Windows 是全球占主导地位的操作系统，截至 2021 年其市场份额 >= 70%。

鉴于威胁面，个人和公司对 Windows 的普及和部署使其成为攻击者的主要目标。

在过去的 15 年中，Windows 出现了相当多的服务器漏洞，从 MS08-067（Conflicker）到 MS17-010（EternalBlue）。

鉴于 Windows 的流行，这些漏洞中的大多数都有可公开访问的利用代码，这使得它们相对容易被利用。

## Windows 漏洞
Microsoft Windows 具有各种操作系统版本和版本，这使得威胁面在漏洞方面变得支离破碎。 例如，Windows 7 中存在的漏洞在 Windows 10 中不存在。

无论版本和发行版如何，所有 Windows 操作系统的开发模型和理念都相似：

* Windows 操作系统是用 C 编程语言开发的，这使得它们容易受到缓冲区溢出、任意代码执行等的影响。
* 默认情况下，Windows 未配置为安全运行，需要主动实施安全实践才能将 Windows 配置为安全运行。
* 微软并未立即修补新发现的漏洞，而且鉴于 Windows 的碎片化性质，许多系统都未打补丁。

新版本 Windows 的频繁发布也是漏洞利用的一个促成因素，因为许多公司花费大量时间将其系统升级到最新版本的 Windows，并选择使用可能受到越来越多的漏洞影响的旧版本Windows。
* 除了固有漏洞外，Windows 还容易受到跨平台漏洞的攻击，例如 SQL 注入攻击。
* 运行 Windows 的系统/主机也容易受到物理攻击，例如； 盗窃、恶意外围设备等。

## Windows 漏洞的类型
信息泄露 - 允许攻击者访问机密数据的漏洞。

缓冲区溢出 - 由编程错误引起，允许攻击者将数据写入缓冲区并溢出分配的缓冲区，从而将数据写入分配的内存地址。

远程代码执行 - 允许攻击者在目标系统上远程执行代码的漏洞。

特权升级 - 允许攻击者在初始利用后提升其特权的漏洞。

拒绝服务 (DOS) - 允许攻击者消耗系统/主机资源（CPU、RAM、网络等）从而阻止系统正常运行的漏洞。

# Frequently Exploited Windows Services（经常被利用的 Windows 服务）
Microsoft Windows 具有各种可配置为在主机上运行的本机服务和协议。

这些服务为攻击者提供了一个访问向量，他们可以利用该向量访问目标主机。

充分了解这些服务是什么、它们如何工作以及它们的潜在漏洞是作为渗透测试人员至关重要的技能。

|协议/服务	|端口	| 用途|
|---|---|---|
|Microsoft IIS（Internet 信息服务）	 |TCP 端口 80/443 	|由 Microsoft 开发的专有 Web 服务器软件，可在 Windows 上运行。|
|WebDAV（Web 分布式创作和版本控制）	|TCP 端口 80/443 	|HTTP 扩展，允许客户端更新、删除、移动和复制 Web 服务器上的文件。 WebDAV 用于使 Web 服务器充当文件服务器。|
|SMB/CIFS（服务器消息块协议）	|TCP 端口 445 	|网络文件共享协议，用于促进本地网络 (LAN) 上计算机之间的文件和外围设备共享。|
|RDP(Remote Desktop Protocol)	 |TCP 端口 3389	 |Microsoft 开发的专有 GUI 远程访问控制，用于远程验证和与 Windows 系统交互。|
|WinRM（Windows 远程管理协议） 	|TCP 端口 5986/443	 |Windows 远程管理协议，可用于促进与 Windows 系统的远程访问。|
