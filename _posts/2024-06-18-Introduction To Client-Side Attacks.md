---
layout: post
title: "Introduction To Client-Side Attacks"
date: "2024-06-18"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Professional
    - Resource Development & Initial Access
    - Client-Side Attacks
---

# Client-Side Attacks
This course equips penetration testers with the skills necessary to exploit client-side vulnerabilities and gain initial access by targeting employee systems and behaviors, bypassing more secure public-facing servers. You will begin with an overview of client-side attacks, learning to gather information and fingerprint to uncover exploitable client-side configurations. The course delves into social engineering, teaching the principles, various attack methods, and the implementation of phishing campaigns using tools like GoPhish. Additionally, you'll learn to develop and weaponize VBA macros, create macro-enabled MS Office documents, and use ActiveX Controls for macro execution. Advanced techniques will include creating droppers and using HTML Applications in your attacks. Practical training on payload delivery methods like HTML Smuggling will also be covered. Ideal for penetration testers and red teamers, this course combines theoretical knowledge, real-world scenarios, and hands-on lab exercises to proficiently target and exploit the weakest link in an organization’s security—the employees.

**客户端攻击**

这门课程为渗透测试人员提供必要的技能，以利用客户端漏洞并通过针对员工系统和行为获得初始访问权限，绕过更加安全的面向公众的服务器。你将从客户端攻击的概览开始，学习收集信息和指纹识别，以发现可利用的客户端配置。课程深入探讨社会工程学，教授原理、各种攻击方法以及使用GoPhish等工具实施钓鱼活动。此外，你将学习开发和武器化VBA宏，创建启用宏的MS Office文档，并使用ActiveX控件执行宏。高级技巧将包括创建投放器和在攻击中使用HTML应用程序。还将涵盖诸如HTML走私之类的有效载荷传递方法的实践训练。这门课程适合渗透测试人员和红队成员，结合了理论知识、现实世界场景和动手实验室练习，熟练地针对和利用组织安全中的最薄弱环节——员工。

## Course Topic Overview
+ Introduction to Client-Side Attacks
  + Client-Side Attack Vectors
+ Client-Side Information Gathering & Fingerprinting
+ Introduction To Social Engineering
  + Social Engineering Techniques
  + Pretexting
+ Phishing with GoPhish
+ Resource Development & Weaponization
+ VBA Macro Development
+ Generating Malicious MS Word Documents
+ HTML Application (HTA) Attacks
+ HTML Smuggling
+ Browser-Based Attacks

**课程主题概览**

+ 客户端攻击简介
  + 客户端攻击向量
+ 客户端信息收集与指纹识别
+ 社会工程学简介
  + 社会工程技术
  + 借口设置
+ 使用GoPhish进行钓鱼
+ 资源开发与武器化
+ VBA宏开发
+ 生成恶意MS Word文档
+ HTML应用程序（HTA）攻击
+ HTML走私
+ 基于浏览器的攻击

## Prerequisites
+ Knowledge of the penetration testing lifecycle
+ Basic familiarity with Windows & Linux
+ Basic familiarity with the Metasploit Framework

**先决条件**

+ 熟悉渗透测试生命周期
+ 基本了解Windows和Linux
+ 基本熟悉Metasploit框架

## Learning Objectives:
+ You will have an understanding of what Client-Side attacks are and the various types of client-side attacks utilized for initial access.
+ You will be able to perform client-side information and fingerprinting in order to identify key info regarding a target’s client-side configuration (browser, OS etc).
+ You will have a solid understanding of what Social Engineering is, the types of Social Engineering attacks used and the role of pretexting in successful social engineering campaigns.
+ You will be able to plan, deploy and manage phishing exercises/campaigns with tools like GoPhish.
+ You will have an understanding of what resource development and weaponization are in terms of client-side attacks.
+ You will be able to develop your own VBA macros for initial access.
+ You will have the ability to leverage functionality like ActiveX Controls to control/facilitate macro execution in documents.
+ You will be able to develop and customize your own Macro enabled MS Office documents for use in obtaining initial access.
+ You will be able to leverage HTML Applications for initial access.

## 学习目标：
+ 你将理解什么是客户端攻击以及用于初始访问的各种类型的客户端攻击。
+ 你将能够执行客户端信息收集和指纹识别，以识别有关目标客户端配置（浏览器、操作系统等）的关键信息。
+ 你将对社会工程学是什么、使用的社会工程攻击类型以及借口设置在成功的社会工程活动中的作用有扎实的理解。
+ 你将能够使用GoPhish等工具计划、部署和管理钓鱼练习/活动。
+ 你将了解客户端攻击中的资源开发和武器化是什么。
+ 你将能够开发自己的VBA宏以获取初始访问权限。
+ 你将具备利用ActiveX控件功能来控制/促进文档中宏执行的能力。
+ 你将能够开发和自定义自己的启用宏的MS Office文档，用于获取初始访问权限。
+ 你将能够利用HTML应用程序获取初始访问权限。

# Introduction To Client-Side Attacks 客户端攻击简介
## Client-Side Attacks 客户端攻击
● Client-side attacks refer to techniques and tactics used by attackers to exploit vulnerabilities or misconfigurations in client-side software or the systems accessed by users/employees of a target organization.

● The objectives of these attacks involve compromising end-user devices, applications or behaviours in order to gain initial access to target system(s).

● In the context of penetration testing and red teaming, client-side attacks aim to simulate/emulate real-world threats and assess an organization’s security posture by targeting the weakest link in the security chain: employees.

● 客户端攻击指的是攻击者用来利用客户端软件或目标组织的用户/员工所访问的系统中的漏洞或配置错误的技术和策略。

● 这些攻击的目标涉及危害终端用户设备、应用程序或行为，以便获得对目标系统的初始访问权限。

● 在渗透测试和红队操作的背景下，客户端攻击旨在模拟/仿真现实世界的威胁，并通过针对安全链中最薄弱的环节：员工，评估组织的安全状况。

● Client-side attacks typically involve/require some form of user/employee interaction and user deception or manipulation in order to get the clientside software to execute the malicious code/payload.

● These attacks are substantially more dangerous than server-side attacks as they do not require direct access to the target system or target network.

● In client-side attacks, attackers deliver the malicious code/payloads via standard(trusted) delivery mechanisms like email, USBs, compromised websites etc.

● 客户端攻击通常涉及/需要某种形式的用户/员工互动，并且需要通过用户欺骗或操纵来使客户端软件执行恶意代码/有效载荷。

● 这些攻击比服务器端攻击更加危险，因为它们不需要直接访问目标系统或目标网络。

● 在客户端攻击中，攻击者通过标准（可信）的传递机制（如电子邮件、USB、被篡改的网站等）传送恶意代码/有效载荷。

## Advantages of Client-Side Attacks 客户端攻击的优势
● Client-side attacks are attractive to attackers for the following reasons:

+ Larger/Wider Attack Surface: End-user devices, such as desktop computers, laptops, smartphones, and tablets, are ubiquitous in modern organizations, providing a large attack surface for exploitation.

+ User Interaction: Client-side attacks leverage human vulnerabilities, such as curiosity, trust, or ignorance, to trick users into executing malicious code or divulging sensitive information.

+ Less Stringent Security Controls: End-user devices may have less robust security measures compared to servers or network infrastructure, making them more susceptible to exploitation.

+ Potential for Lateral Movement: Once initial access is achieved through a client-side attack, attackers may pivot to other systems or resources within the network to escalate privileges, achieve persistence, or exfiltrate data.


● 客户端攻击对攻击者具有吸引力的原因如下：

+ 更大/更广的攻击面：在现代组织中，终端用户设备如桌面电脑、笔记本电脑、智能手机和平板电脑无处不在，提供了广泛的可利用攻击面。

+ 用户互动：客户端攻击利用人类的弱点，如好奇心、信任或无知，诱使用户执行恶意代码或泄露敏感信息。

+ 安全控制不严格：与服务器或网络基础设施相比，终端用户设备的安全措施可能不够强大，使其更容易被利用。

+ 潜在的横向移动：通过客户端攻击获得初始访问权限后，攻击者可能转向网络内的其他系统或资源，以升级权限、实现持久化或窃取数据。


## Client-Side vs Server-Side Attacks 客户端攻击与服务器端攻击对比

|  | Client-Side Attacks | Server-Side Attacks |
| ----------- | ----------- | ----------- |
| Target | Target end-user devices, applications, or behaviors. These attacks exploit vulnerabilities in software or systems accessed by users, such as web browsers, email clients, or office applications. | Target servers, network infrastructure, or backend systems. These attacks focus on exploiting vulnerabilities in servers, databases, web applications, or services hosted on remote servers. |
| Objective | Aim to compromise end-user devices, steal sensitive information, or establish a foothold within an organization's network. These attacks often leverage social engineering tactics to trick users into performing actions that facilitate the attack. | Aim to gain unauthorized access to servers or backend systems, exfiltrate sensitive data, or disrupt services. These attacks may exploit vulnerabilities in server software, misconfigurations, or insecure server-side scripting. |
| Execution | Typically involve the delivery of malicious content or payloads to end-user devices through channels such as phishing emails, malicious websites, or infected documents. | Exploit vulnerabilities or weaknesses in server-side software, services, or configurations. |
| Examples | Phishing, drive-by downloads, social engineering, malicious attachments, exploit kits targeting vulnerabilities in clientside software. | SQL injection, cross-site scripting (XSS), server-side request forgery (SSRF), remote code execution (RCE), server misconfigurations, brute-force attacks against server authentication mechanisms. |


|  | 客户端攻击 | 服务器端攻击 |
| ----------- | ----------- | ----------- |
| 目标 | 针对终端用户设备、应用程序或行为。这些攻击利用用户访问的软件或系统中的漏洞，如网络浏览器、电子邮件客户端或办公应用程序。 | 针对服务器、网络基础设施或后端系统。这些攻击专注于利用服务器、数据库、网络应用程序或远程服务器上托管的服务中的漏洞。 |
| 目的 | 旨在危害终端用户设备、窃取敏感信息或在组织网络内建立立足点。这些攻击通常利用社会工程技巧诱使用户执行有助于攻击的操作。 | 旨在未经授权访问服务器或后端系统、窃取敏感数据或中断服务。这些攻击可能利用服务器软件的漏洞、配置错误或不安全的服务器端脚本。 |
| 执行 | 通常涉及通过钓鱼电子邮件、恶意网站或感染文档等渠道将恶意内容或有效载荷传送到终端用户设备。 | 利用服务器端软件、服务或配置中的漏洞或弱点。 |
| 示例 | 钓鱼、驱动下载、社会工程、恶意附件、针对客户端软件漏洞的利用工具包。 | SQL注入、跨站脚本（XSS）、服务器端请求伪造（SSRF）、远程代码执行（RCE）、服务器配置错误、针对服务器认证机制的暴力破解攻击。 |

## How Client-Side Attacks Work 客户端攻击的工作原理
● Let's consider a fictitious example of a client-side attack targeting an organization called "Acme Corp."

● In this fictitious example, the client-side attack begins with reconnaissance and target identification, followed by payload development, delivery, and execution.

● The attacker leverages social engineering tactics and exploits vulnerabilities in client-side software to gain unauthorized access to Acme Corp.'s network, ultimately achieving their malicious objectives.

● 让我们考虑一个针对名为“Acme Corp.”的组织的虚构客户端攻击示例。

● 在这个虚构示例中，客户端攻击始于侦察和目标识别，随后是有效载荷的开发、传递和执行。

● 攻击者利用社会工程策略并利用客户端软件中的漏洞，未经授权地访问Acme Corp.的网络，最终实现其恶意目标。

Here's how the attack might unfold from reconnaissance to payload delivery:

**Step 1: Reconnaissance**
+ The attacker begins by conducting reconnaissance on Acme Corp. using publicly available information, social media profiles, company websites, and job postings.
+ The attacker identifies employees, their roles, and potential targets within the organization.
+ The attacker gathers information about Acme Corp.'s technology stack, email domains, and common software applications used by employees.

以下是从侦察到有效载荷传递，攻击可能如何展开的步骤：

**步骤 1：侦察**
+ 攻击者首先使用公开可用的信息、社交媒体资料、公司网站和招聘信息对Acme Corp.进行侦察。
+ 攻击者识别员工、他们的角色和组织内的潜在目标。
+ 攻击者收集关于Acme Corp.的技术栈、电子邮件域和员工常用的软件应用程序的信息。

**Step 2: Target Identification**
+ Based on reconnaissance findings, the attacker identifies specific individuals within Acme Corp. who are likely to have access to sensitive information or valuable assets.
+ The attacker selects potential targets for the client-side attack, such as employees in finance, human resources, or executive positions.

**Step 3: Payload/Resource Development**
+ The attacker develops a malicious document containing a payload, such as a Microsoft Word document with an embedded macro or a PDF file with a JavaScript exploit.
+ The payload is designed to exploit vulnerabilities in common software applications used by Acme Corp., such as Microsoft Office or Adobe Reader.

**步骤 2：目标识别**
+ 基于侦察发现，攻击者识别出在Acme Corp.中可能有权访问敏感信息或宝贵资产的特定个人。
+ 攻击者选择客户端攻击的潜在目标，例如财务部、人力资源部或执行职位的员工。

**步骤 3：有效载荷/资源开发**
+ 攻击者开发一个包含有效载荷的恶意文档，例如带有嵌入式宏的Microsoft Word文档或带有JavaScript漏洞的PDF文件。
+ 有效载荷设计用于利用Acme Corp.员工常用的软件应用程序中的漏洞，如Microsoft Office或Adobe Reader。

**Step 4: Payload Preparation**
+ The attacker creates a convincing pretext for the payload delivery, such as crafting a phishing email masquerading as an important document from a trusted source.
+ The attacker sets up infrastructure to host the malicious document or payload, such as a compromised website or a temporary file-sharing service.

**Step 5: Payload Delivery**
+ The attacker sends phishing emails to selected employees within Acme Corp., urging them to review the attached document or click on a link to access important information.
+ The phishing email may contain social engineering tactics to increase the likelihood of success, such as urgency, fear, or curiosity.

**步骤 4：有效载荷准备**
+ 攻击者为有效载荷传递创建一个令人信服的借口，例如伪装成来自可信来源的重要文件的钓鱼电子邮件。
+ 攻击者设置基础设施来托管恶意文档或有效载荷，如被侵入的网站或临时文件共享服务。

**步骤 5：有效载荷传递**
+ 攻击者向Acme Corp.内选定的员工发送钓鱼电子邮件，敦促他们查看附件的文档或点击链接访问重要信息。
+ 钓鱼电子邮件可能包含社会工程策略以增加成功的可能性，例如紧迫感、恐惧或好奇心。

**Step 6: Payload Execution**
+ A targeted employee at Acme Corp. receives the phishing email and opens the malicious document or clicks on the provided link.
+ The embedded payload executes, exploiting vulnerabilities in the client-side software (e.g., Microsoft Office) to gain a foothold on the employee's device.
+ The attacker's payload establishes communication with a command-and-control (C2) server controlled by the attacker, enabling further interaction and control.

**Step 7: Post-Exploitation**
+ With access to the employee's device, the attacker may perform post-exploitation activities, such as privilege escalation, lateral movement, or data exfiltration.
+ The attacker may escalate privileges to gain higher-level access within Acme Corp.'s network, moving laterally to compromise additional systems or resources.

**步骤 6：有效载荷执行**
+ Acme Corp.的一名目标员工收到钓鱼电子邮件并打开恶意文档或点击提供的链接。
+ 嵌入的有效载荷执行，利用客户端软件（例如，Microsoft Office）中的漏洞在员工的设备上获得立足点。
+ 攻击者的有效载荷与攻击者控制的指挥与控制（C2）服务器建立通信，实现进一步的交互和控制。

**步骤 7：后期利用**
+ 访问了员工的设备后，攻击者可能执行后期利用活动，如权限提升、横向移动或数据窃取。
+ 攻击者可能提升权限以在Acme Corp.的网络内获得更高级别的访问权限，横向移动以侵害更多系统或资源。

## Client-Side Attack Methodology
Reconnaissance -> Target Identification -> Payload/Resource Development -> Delivery -> Execution -> Post-Exploitation

侦察 -> 目标识别 -> 有效载荷/资源开发 -> 传递 -> 执行 -> 后期利用