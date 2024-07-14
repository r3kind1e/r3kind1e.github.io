---
layout: post
title: "Resource Development & Weaponization"
date: "2024-07-12"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Professional
    - Resource Development & Initial Access
    - Client-Side Attacks
---

# Resource Development & Weaponization 资源开发与武器化
## Resource Development & Weaponization 资源开发与武器化
In the context of red teaming and penetration testing, resource development and weaponization are two crucial phases that occur during the lifecycle of an attack/pentest.

The terms "resource development" and "weaponization" originated in military strategy, they have been adapted and applied in cybersecurity to describe key phases in the lifecycle of cyberattacks.

Resource development focuses on acquiring or building the necessary resources for an attack, while weaponization involves turning those resources into effective cyber weapons.

在红队行动和渗透测试的背景下，资源开发和武器化是攻击/渗透测试生命周期中的两个关键阶段。

“资源开发”和“武器化”这两个术语起源于军事战略，它们已被适应并应用于网络安全，用以描述网络攻击生命周期中的关键阶段。

资源开发专注于获取或构建攻击所需的资源，而武器化则涉及将这些资源转变为有效的网络武器。

The two terms are sometimes used interchangeably given their overlapping objectives, however, it is very important to understand the distinction between the two.

This can be done by understanding how these terms have been used and implemented into modern-day cybersecurity frameworks and kill chains like, The MITRE ATT&CK Framework and the Cyber Kill Chain (Lockheed Martin).

Both the MITRE ATT&CK Framework and the Cyber Kill Chain are widely used methodologies in cybersecurity, and they provide structured approaches to understanding and analyzing cyber threats.

这两个术语有时会因为它们重叠的目标而被交替使用，然而，理解这两者之间的区别非常重要。

这可以通过了解这些术语是如何被使用并整合到现代网络安全框架和击杀链中来完成，例如MITRE ATT&CK框架和网络击杀链（洛克希德·马丁公司）。

MITRE ATT&CK框架和网络击杀链都是在网络安全领域广泛使用的方法论，它们提供了结构化的方式来理解和分析网络威胁。

## The MITRE ATT&CK Framework MITRE ATT&CK框架
The MITRE ATT&CK framework is a globally-accessible knowledge base of adversary tactics and techniques based on real world threats and threat actors (APT groups). It was developed to improve the understanding of how cyber attacks are performed.

ATT&CK is an abbreviation for Adversarial Tactics, Techniques and Common Knowledge.

The MITRE ATT&CK Framework is typically employed/used as a baseline and model for adversarial behavior and highlights the various phases of an adversary/threat attack lifecycle, what software they employ and the OS’s they target.

MITRE ATT&CK框架是一个基于现实世界威胁和威胁行为者（APT组）的全球可访问的敌手战术和技术知识库。它的开发旨在提高对网络攻击执行方式的理解。

ATT&CK是“Adversarial Tactics, Techniques and Common Knowledge”（对抗性战术、技术和常识）的缩写。

MITRE ATT&CK框架通常被用作对抗行为的基线和模型，并突出显示敌手/威胁攻击生命周期的各个阶段，他们使用的软件以及他们针对的操作系统。

It is mostly used by Red/Blue Teamers to plan, implement and orchestrate engagements based on specific threat actors/APTs. (adversary emulation/simulation)

It is also a valuable resource for blue teamers as it details the various TTPs used by specific threat actors and provides companies with valuable cyber threat intelligence (CTI) that can consequently be used to implement defenses and mitigations.

MITRE ATT&CK categorizes adversarial techniques into a collection tactics further organized into techniques, sub-techniques and procedures (TTPs).

## Resource Development 资源开发
In the context of red teaming and the MITRE ATT&CK Framework, Resource development involves gathering, creating, or acquiring the necessary tools, techniques, and knowledge to execute an attack or penetration test effectively.

This phase typically involves researching vulnerabilities, understanding the target environment, and identifying potential attack vectors.

Resources could include exploit code, custom scripts, reconnaissance tools, social engineering tactics, or any other assets needed to carry out the simulated attack.

The goal of resource development is to equip the red team or penetration tester with everything they need to successfully breach the target's defenses.

在红队行动和MITRE ATT&CK框架的背景下，资源开发涉及收集、创建或获取执行攻击或渗透测试所需的工具、技术和知识。

这一阶段通常包括研究漏洞、理解目标环境和识别潜在的攻击途径。

资源可能包括利用代码、自定义脚本、侦察工具、社会工程策略或任何其他执行模拟攻击所需的资产。

资源开发的目标是为红队或渗透测试人员配备他们成功突破目标防御所需的一切。

Tactics categorize each step of the adversary’s attack methodology.

Tactics represent the adversary’s tactical goal or objective.

Techniques are used to outline how each tactic is orchestrated.

Techniques describe actions taken by adversaries to achieve their objective.

Sub-Techniques outline the implementation of a specific technique in detail.

Procedures outline all known implementations of a technique or sub-technique

战术将对手攻击方法论的每一步进行分类。

战术代表了对手的战术目标或目的。

技术用于概述如何协调每一战术。

技术描述对手为实现其目标所采取的行动。

子技术详细概述了特定技术的实施。

程序概述了一种技术或子技术的所有已知实现方式。

## The Cyber Kill Chain 网络击杀链
The Cyber Kill Chain, developed by Lockheed Martin, is a framework used to describe the stages of a cyber attack from the initial reconnaissance to the exfiltration of data.

It consists of several sequential stages, each representing a phase that an attacker must go through to achieve their objectives.

Red teaming often involves the use of the Cyber Kill Chain framework to simulate and assess an organization's defensive capabilities.

网络击杀链由洛克希德·马丁公司开发，是一个用于描述从最初的侦察到数据泄露的网络攻击各个阶段的框架。

它由几个连续的阶段组成，每个阶段代表攻击者为实现其目标必须经过的一个阶段。

红队行动常常涉及使用网络击杀链框架来模拟和评估一个组织的防御能力。

![The-Cyber-Kill-Chain](/img/in-post/ine/The-Cyber-Kill-Chain.png)


- **Weaponization**
  - Coupling exploit with backdoor into deliverable payload.
- **Reconnaissance**
  - Harvesting email addresses, conference information, etc.
- **Delivery**
  - Delivering weaponized bundle to the victim via email, web, USB, etc.
- **Exploitation**
  - Exploiting a vulnerability to execute code on victim's system.
- **Installation**
  - Installing malware on the asset.
- **Command & Control**
  - Command channel for remote manipulation of victim.
- **Actions On Objectives**
  - With 'Hands on Keyboard' access, intruders accomplish their original goals.


- 武器化
  - 将利用代码与后门结合成可交付的有效载荷。
- 侦察
  - 收集电子邮件地址、会议信息等。
- 交付
  - 通过电子邮件、网络、USB等向受害者交付武器化的包。
- 利用
  - 利用漏洞在受害者系统上执行代码。
- 安装
  - 在资产上安装恶意软件。
- 命令与控制
  - 为远程操纵受害者建立命令通道。
- 对目标的行动
  - 通过“键盘上手”的访问，入侵者实现他们的初衷目标。


## Weaponization 武器化
Weaponization is the process of taking the resources developed in the previous phase and converting them into actual weapons that can be used to exploit vulnerabilities and compromise the target system.

This phase involves crafting payloads, creating malicious files, or configuring exploit code to deliver the intended impact, such as gaining unauthorized access, exfiltrating data, or disrupting services.

Weaponization often involves combining various techniques and tools in a way that maximizes their effectiveness while minimizing the risk of detection.

武器化是指将前一阶段开发的资源转化为可以用来利用漏洞和破坏目标系统的实际武器的过程。

这一阶段涉及制作有效载荷、创建恶意文件或配置利用代码以产生预期的影响，如获得未授权访问、数据泄露或中断服务。

武器化通常涉及以最大化效果和最小化被检测风险的方式结合各种技术和工具。

## Resource Development vs Weaponization 资源开发与武器化
Focus: Resource development focuses on acquiring the necessary tools and knowledge, whereas weaponization focuses on turning those resources into active attack payloads or techniques.

Stage in the Attack Lifecycle: Resource development typically precedes weaponization. Once the necessary resources are developed, they are then weaponized for use in the attack.

Nature of Activities: Resource development activities may include research, reconnaissance, and tool development. Weaponization involves creating and configuring attack payloads, crafting malicious files, and preparing exploit code.

Output: Resource development outputs tools, knowledge, and information about the target environment. Weaponization outputs actual attack payloads or techniques ready for deployment.

焦点：资源开发专注于获取必要的工具和知识，而武器化则专注于将这些资源转化为活跃的攻击有效载荷或技术。

攻击生命周期中的阶段：资源开发通常在武器化之前进行。一旦开发出必要的资源，接着就会对它们进行武器化，以便在攻击中使用。

活动性质：资源开发活动可能包括研究、侦察和工具开发。武器化涉及创建和配置攻击有效载荷、制作恶意文件和准备利用代码。

输出：资源开发输出工具、知识和有关目标环境的信息。武器化输出实际的攻击有效载荷或准备部署的技术。

## Adapted Client-Side Attack Methodology 适应性客户端攻击方法论
![Adapted-Client-Side-Attack-Methodology](/img/in-post/ine/Adapted-Client-Side-Attack-Methodology.png)

- **Reconnaissance**
  - Gather information about the target organization's systems, networks, and potential vulnerabilities.
- **Resource Development**
  - Acquire or develop the necessary tools, knowledge, and infrastructure to conduct the simulated attack.
- **Weaponization**
  - Prepare the acquired or developed resources for use in the simulated attack.
- **Delivery**
  - Deliver the weaponized payloads or social engineering tactics to initiate the attack.

此外，每个阶段都标记了相关的关键词：
- **PLANNING** 在 "Reconnaissance" 阶段
- **ACTIONABLE INTEL** 在 "Resource Development" 阶段
- **PAYLOAD DEVELOPMENT** 在 "Weaponization" 阶段
- **WEAPONIZED PAYLOAD** 在 "Delivery" 阶段


- **侦察**
  - 收集有关目标组织的系统、网络和潜在漏洞的信息。
- **资源开发**
  - 获取或开发进行模拟攻击所需的工具、知识和基础设施。
- **武器化**
  - 准备获得或开发的资源以用于模拟攻击。
- **交付**
  - 交付武器化的有效载荷或社会工程策略以启动攻击。

此外，每个阶段都标记了相关的关键词：
- **计划** 在 "侦察" 阶段
- **可操作情报** 在 "资源开发" 阶段
- **有效载荷开发** 在 "武器化" 阶段
- **武器化有效载荷** 在 "交付" 阶段


![Adapted-Client-Side-Attack-Methodology-table.png](/img/in-post/ine/Adapted-Client-Side-Attack-Methodology-table.png)

| Phase              | Objectives                                                                          | Activities                                                                                                                  | Outputs                                                                                                                                     |
|--------------------|-------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| **Reconnaissance** | Gather information about the target organization's systems, networks, and potential vulnerabilities. | Conduct open-source intelligence (OSINT) gathering to collect publicly available information about the target organization, including its employees, technologies used, partners, etc. | Comprehensive reconnaissance report detailing the findings, including identified assets, potential attack surfaces, and areas of weakness. |
| **Resource Development** | Acquire or develop the necessary tools, knowledge, and infrastructure to conduct the simulated attack. | Identify and acquire tools and exploits relevant to the target environment based on reconnaissance findings. Develop custom scripts, malware, or other payloads tailored to exploit identified vulnerabilities. | Identify and acquire tools and exploits relevant to the target environment based on reconnaissance findings. Develop custom scripts, malware, or other payloads tailored to exploit identified vulnerabilities. |
| **Weaponization**  | Prepare the acquired or developed resources for use in the simulated attack.         | Configure and customize acquired exploits, payloads, or malware to ensure compatibility with the target environment. Craft phishing emails or other social engineering tactics to deliver malicious payloads. Develop or modify exploit code to take advantage of identified vulnerabilities. | Configure and customize acquired exploits, payloads, or malware to ensure compatibility with the target environment. Craft phishing emails or other social engineering tactics to deliver malicious payloads. Develop or modify exploit code to take advantage of identified vulnerabilities. |


| 阶段         | 目标                                                                                       | 活动                                                                                                                             | 输出                                                                                                            |
|------------|------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|
| **侦察**      | 收集有关目标组织的系统、网络和潜在漏洞的信息。                                                  | 进行开源情报（OSINT）收集，收集关于目标组织的公开信息，包括其员工、使用的技术、合作伙伴等。                                              | 详尽的侦察报告，详细记录了发现的内容，包括已识别的资产、潜在的攻击面和弱点区域。                                          |
| **资源开发**   | 获取或开发进行模拟攻击所需的工具、知识和基础设施。                                             | 根据侦察发现，识别并获取与目标环境相关的工具和漏洞。开发定制脚本、恶意软件或其他为利用已识别漏洞而定制的有效载荷。                     | 根据侦察发现，识别并获取与目标环境相关的工具和漏洞。开发定制脚本、恶意软件或其他为利用已识别漏洞而定制的有效载荷。          |
| **武器化**    | 准备获取或开发的资源以用于模拟攻击。                                                           | 配置和定制获取的漏洞、有效载荷或恶意软件，以确保与目标环境的兼容性。制作钓鱼电子邮件或其他社交工程策略来传递恶意有效载荷。开发或修改利用代码以利用已识别的漏洞。 | 配置和定制获取的漏洞、有效载荷或恶意软件，以确保与目标环境的兼容性。制作钓鱼电子邮件或其他社交工程策略来传递恶意有效载荷。开发或修改利用代码以利用已识别的漏洞。 |


## References & Resources
Cyber Kill Chain (Lockheed Martin): https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html

MITRE ATT&CK - https://attack.mitre.org/

Unified Cyber Kill Chain - https://www.unifiedkillchain.com/

![Unified-Cyber-Kill-Chain.png](/img/in-post/ine/Unified-Cyber-Kill-Chain.png)
