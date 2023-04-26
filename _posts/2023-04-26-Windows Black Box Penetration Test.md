---
layout: post
title: "Windows Black Box Penetration Test"
date: "2023-04-26"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# Windows Black Box Penetration Test
## Black Box Pentest
A Black box penetration test is a security assessment whereby the penetration tester is not provided with any information regarding the target system or network (NO IP ranges, system information or default credentials are provided).

The objective of a Black box penetration test is to accurately test the security of a system or network as an external unprivileged adversary.

This approach is very useful as it demonstrates how an external attacker with no inside knowledge would compromise a company's systems or networks.

# Windows 黑盒渗透测试
## 黑盒渗透测试
黑盒渗透测试是一种安全评估，渗透测试人员不向其提供有关目标系统或网络的任何信息（不提供 IP 范围、系统信息或默认凭证）。

黑盒渗透测试的目的是准确测试系统或网络作为外部非特权对手的安全性。

这种方法非常有用，因为它展示了不了解内部知识的外部攻击者如何破坏公司的系统或网络。

## Penetration Testing Phases
The following diagram outlines the various phases involved in a typical penetration test.

![penetration-testing-phases-2.png](/img/in-post/ine/penetration-testing-phases-2.png)

Black Box Methodology:
* Host discovery
* Port scanning & enumeration
* Vulnerability detection/scanning
* Exploitation
	* Manual
	* Automated
* Post Exploitation
	* Privilege Escalation
	* Persistence
	* Dumping Hashes

Penetration Testing Phases
* Information Gathering
* Enumeration
* Exploitation
* Post Exploitation
	* Privilege Escalation
	* Maintaining Persistent Access
	* Clearing Tracks
		
## 渗透测试阶段
下图概述了典型渗透测试中涉及的各个阶段。

![penetration-testing-phases-2.png](/img/in-post/ine/penetration-testing-phases-2.png)

黑盒方法论：
* 主机发现
* 端口扫描和枚举
* 漏洞检测/扫描
* 利用
	* 手动
	* 自动化
* 后期利用
	* 提权
	* 持久化
	* 转储哈希

渗透测试阶段
* 信息收集
* 枚举
* 利用
* 后期利用
	* 提权
	* 保持持久访问
	* 清理痕迹

## Scenario & Scope
You have just begun your first job as a Junior Penetration Tester and have been assigned to assist in performing a penetration test on a client's network.

The pentest lead  has assigned you to gain access/exploit a host running Windows Server 2008.

Your primary objectives are:
* Identify services running on the target
* Identify vulnerabilities within the services
* Exploit these vulnerabilities to obtain an initial foothold

Note: You are permitted to use the Metasploit Framework

## 场景和范围
您刚刚开始第一份初级渗透测试员的工作，并被指派协助在客户的网络上执行渗透测试。

渗透测试负责人已指派您访问/利用运行 Windows Server 2008 的主机。

您的主要目标是：
* 识别目标上运行的服务
* 识别服务中的漏洞
* 利用这些漏洞获得初步立足点

注意：您被允许使用 Metasploit 框架