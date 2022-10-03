---
layout: post
title: "Windows Password Hashes"
date: "2022-10-03"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - System/Host Based Attacks
---

# Windows Password Hashes（Windows 密码哈希）
## Windows 密码哈希
Windows 操作系统将散列的用户帐户密码本地存储在 SAM（安全帐户管理器）数据库中。

散列是将一段数据转换为另一个值的过程。 散列函数或算法用于生成新值。 散列算法的结果称为散列或散列值。

本地安全机构 (LSA) 促进了用户凭据的身份验证和验证。

直到 Windows Server 2003 的 Windows 版本使用两种不同类型的哈希：
* LM
* NTLM

Windows 禁用 LM 散列并从 Windows Vista 开始使用 NTLM 散列。

## SAM数据库
SAM（安全帐户管理器）是一个数据库文件，负责管理 Windows 上的用户帐户和密码。 存储在 SAM 数据库中的所有用户帐户密码都经过哈希处理。

操作系统运行时无法复制 SAM 数据库文件。

Windows NT 内核保持 SAM 数据库文件锁定，因此，攻击者通常利用内存技术和工具从 LSASS 进程中转储 SAM 哈希。

在现代版本的 Windows 中，SAM 数据库使用 syskey 加密。

注意：需要提升/管理权限才能访问 LSASS 进程并与之交互。

## LM (LanMan)
LM 是在 NT4.0 之前的 Windows 操作系统中实现的默认散列算法。

该协议用于对用户密码进行哈希处理，哈希处理过程可以分解为以下步骤：

* 密码分为两个七个字符的块。

* 然后将所有字符转换为大写。

* 然后使用 DES 算法对每个块进行单独散列。

LM 散列通常被认为是一个弱协议并且很容易被破解，主要是因为密码散列不包含盐，因此使得暴力和彩虹表攻击对 LM 散列有效。

![LM(LanMan).png](/img/in-post/ine/LM(LanMan).png)

## NTLM (NTHash)
NTLM 是 Windows 中用于促进计算机之间身份验证的身份验证协议的集合。 身份验证过程涉及使用有效的用户名和密码来成功进行身份验证。

从 Windows Vista 开始，Windows 禁用 LM 散列并利用 NTLM 散列。

创建用户帐户时，会使用 MD4 哈希算法对其进行加密，而原始密码会被丢弃。

NTLM 通过以下方式改进了 LM：

* 不将散列分成两个块。
* 区分大小写。
* 允许使用符号和 Unicode 字符。

![NTLM(NTHash).png](/img/in-post/ine/NTLM(NTHash).png)