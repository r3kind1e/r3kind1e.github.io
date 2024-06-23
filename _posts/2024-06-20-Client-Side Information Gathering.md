---
layout: post
title: "Client-Side Information Gathering"
date: "2024-06-20"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Professional
    - Resource Development & Initial Access
    - Client-Side Attacks
---

# Client-Side Information Gathering 客户端信息收集
## Client-Side Information Gathering 客户端信息收集
● When performing a client-side attack, the success of the attack will come down to the accuracy of the information you gather about your target(s) and the client-side software and configuration running on the target system(s).

● In order for you to successfully exploit a client-side vulnerability, or misconfiguration, you must first know what client-side software is running on a target system(s).

● This is where client-side information gathering and fingerprinting comes into play.

● 在执行客户端攻击时，攻击的成功将取决于你收集的有关目标及其系统上运行的客户端软件和配置的准确性。

● 为了成功利用客户端漏洞或配置错误，你首先必须知道目标系统上运行的是哪些客户端软件。

● 这就是客户端信息收集和指纹识别发挥作用的地方。

● Just like any traditional penetration test, information gathering can be broken down into to categories, depending on the nature of the interaction with the target(s):

+ Passive Client Information Gathering: Passive client information gathering involves collecting data about target users, systems, or networks without directly interacting with them. This approach aims to gather information passively from publicly available sources through techniques like OSINT.
+ Active Client Information Gathering: Active client information gathering involves interacting directly with target systems, applications, or users to gather data about their client-side configurations, vulnerabilities, or behaviors. This approach aims to gather information through direct interaction, such as client/browser fingerprinting, banner grabbing, and social engineering.

● 就像任何传统的渗透测试一样，信息收集可以根据与目标的互动性质分为两类：

+ 被动客户端信息收集：被动客户端信息收集涉及在不直接与目标用户、系统或网络互动的情况下收集数据。这种方法旨在通过诸如OSINT（开源情报）之类的技术从公开可用的来源被动地收集信息。
+ 主动客户端信息收集：主动客户端信息收集涉及直接与目标系统、应用程序或用户互动，以收集有关其客户端配置、漏洞或行为的数据。这种方法旨在通过直接互动收集信息，例如客户端/浏览器指纹识别、横幅抓取和社会工程学。

## Passive Client Information Gathering Techniques 被动客户端信息收集技术
**Open Source Intelligence (OSINT):**
+ Examples: Searching social media platforms (e.g., LinkedIn, Twitter) for employee profiles, company information, or job postings.
+ Browsing public forums or websites for discussions about the organization or its technologies.
+ Tools: Google Dorks for advanced search queries, [Maltego](https://www.maltego.com/) for data visualization and link analysis, [theHarvester](https://github.com/laramies/theHarvester) for email harvesting.

**开源情报（OSINT）：**
+ 示例：搜索社交媒体平台（例如 LinkedIn、Twitter）以获取员工资料、公司信息或招聘信息。
+ 浏览公共论坛或网站以获取有关该组织或其技术的讨论。
+ 工具：使用Google Dorks进行高级搜索查询，使用[Maltego](https://www.maltego.com/)进行数据可视化和链接分析，使用[theHarvester](https://github.com/laramies/theHarvester)进行电子邮件收集。

**Search Engine Reconnaissance:**
+ Examples: Using advanced search queries on search engines like Google to discover publicly available information about target individuals, organizations, or systems.
+ Tools: Google Search operators, [Shodan search engine](https://www.shodan.io/), [DuckDuckGo](https://duckduckgo.com/).

**搜索引擎侦察：**
+ 示例：在谷歌等搜索引擎上使用高级搜索查询来发现有关目标个人、组织或系统的公开可用信息。
+ 工具：谷歌搜索操作符、[Shodan搜索引擎](https://www.shodan.io/)、[DuckDuckGo](https://duckduckgo.com/)。

## Active Client Information Gathering Techniques 主动客户端信息收集技术
**Client Fingerprinting:**
+ Client fingerprinting is a technique used to gather information about a user's web browser and software stack in order to aid in the development of tailor made (client specific) payloads for initial access.
+ In the context of client-side information gathering, client fingerprinting can be used to identify key information about the client-side software running on the target(s) system. For example, browser and browser version, OS and system architecture etc.

**客户端指纹识别：**
+ 客户端指纹识别是一种用于收集有关用户的网络浏览器和软件栈的信息的技术，以帮助开发针对性强（客户端特定的）有效载荷，用于初始访问。
+ 在客户端信息收集的背景下，客户端指纹识别可用于识别目标系统上运行的客户端软件的关键信息。例如，浏览器及其版本、操作系统和系统架构等。

**Social Engineering:**
+ Examples: Engaging with target individuals or employees through phone calls, emails, or other communication channels to gather sensitive information, credentials, or access permissions.
+ Tools: Social engineering toolkits like [SET (Social-Engineer Toolkit)](https://github.com/trustedsec/social-engineer-toolkit?tab=readme-ov-file), PhishMe, [BeEF (Browser Exploitation Framework)](https://beefproject.com/).

**社会工程：**
+ 示例：通过电话、电子邮件或其他通信渠道与目标个人或员工交流，以收集敏感信息、凭证或访问权限。
+ 工具：社会工程工具包，如 [SET（社会工程师工具包）](https://github.com/trustedsec/social-engineer-toolkit?tab=readme-ov-file)、PhishMe、[BeEF（浏览器利用框架）](https://beefproject.com/)。

## Active Client Information Gathering Example 主动客户端信息收集示例
Scenario: Leveraging Social Engineering for Active Client Information Gathering
+ Alice, a penetration tester, is targeting a specific company, Acme Corporation, to gather information about their internal systems and software configurations.
+ She has decided to use client-side attacks to gain initial access to the target network.
+ Alice plans to use social engineering techniques to extract valuable information from the company's employees regarding their client-side software without raising suspicion.

情景：利用社会工程进行主动客户端信息收集
+ Alice是一名渗透测试人员，她的目标是特定公司Acme Corporation，以收集有关其内部系统和软件配置的信息。
+ 她决定使用客户端攻击来获得对目标网络的初始访问权限。
+ Alice计划使用社会工程技术从公司员工那里提取有关其客户端软件的宝贵信息，同时不引起怀疑。

Scenario: Leveraging Social Engineering for Active Client Information Gathering

**1. Research and Preparation:**
+ Alice conducts reconnaissance on Acme Corporation's website and discovers a job opening for a position relevant to her cover story. She learns that the company has an online resume upload functionality for job applications.

**2. Initiating Contact:**
+ Alice creates a fictitious persona named Sarah Johnson and submits a resume to Acme Corporation's website using the resume upload feature.
+ The resume contains an embedded macro that triggers an error when opened, simulating a corrupted document.

情景：利用社会工程进行主动客户端信息收集

**1. 研究和准备：**
+ Alice在Acme Corporation的网站上进行侦察，发现了一个与她掩护故事相关的职位空缺。她了解到公司有一个在线简历上传功能用于职位申请。

**2. 发起联系：**
+ Alice创建了一个名为Sarah Johnson的虚构人物，并使用简历上传功能向Acme Corporation的网站提交简历。
+ 简历中嵌入了一个宏，该宏在打开时触发错误，模拟了一个损坏的文档。

Scenario: Leveraging Social Engineering for Active Client Information Gathering

**3. Response from the Company:**
+ Acme Corporation's HR department receives Sarah Johnson's resume but encounters an issue when attempting to open the document.
+ Suspecting a technical problem, they reach out to Sarah via email, informing her of the issue and requesting a re-submission or clarification.

情景：利用社会工程进行主动客户端信息收集

**3. 公司的回应：**
+ Acme Corporation的人力资源部收到了Sarah Johnson的简历，但在尝试打开文档时遇到问题。
+ 怀疑是技术问题，他们通过电子邮件联系Sarah，告知她问题并请求重新提交或澄清。

Scenario: Leveraging Social Engineering for Active Client Information Gathering

**4. Exploiting the Opportunity:**
+ Alice, posing as Sarah, receives the email from Acme Corporation's HR department. Seizing the opportunity, she responds promptly, expressing concern and offering assistance.
+ She then asks a seemingly innocuous question: "Could you please let me know the version of Microsoft Word your team is using? I want to ensure compatibility with future submissions."

情景：利用社会工程进行主动客户端信息收集

**4. 利用机会：**
+ Alice扮演Sarah，收到了Acme Corporation人力资源部的电子邮件。抓住这个机会，她迅速回复，表达关心并提供帮助。
+ 随后，她提出了一个看似无害的问题：“您能告诉我贵团队使用的Microsoft Word版本吗？我想确保与未来提交的兼容性。”

Scenario: Leveraging Social Engineering for Active Client Information Gathering

**5. Information Gathering:**
+ Acme Corporation's HR representative, unaware of the malicious intent, responds to Sarah's inquiry, providing details about the company's Microsoft Word version.

情景：利用社会工程进行主动客户端信息收集

**5. 信息收集：**
+ Acme Corporation的人力资源代表没有意识到恶意意图，回应了Sarah的询问，提供了公司使用的Microsoft Word版本的详细信息。

Scenario: Leveraging Social Engineering for Active Client Information Gathering

**6. Analysis & Resource Development:**
+ Alice, now equipped with information about Acme Corporation's Microsoft Word version, uses this information to identify potential vulnerabilities or compatibility issues.
+ She may also use it to tailor future social engineering attacks or craft malicious payloads targeting specific software versions (Malicious Word documents).

情景：利用社会工程进行主动客户端信息收集

**6. 分析与资源开发：**
+ Alice现在掌握了有关Acme Corporation使用的Microsoft Word版本的信息，使用这些信息来识别潜在的漏洞或兼容性问题。
+ 她还可以利用这些信息来定制未来的社会工程攻击或制作针对特定软件版本的恶意有效载荷（恶意Word文档）。



