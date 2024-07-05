---
layout: post
title: "Introduction To Social Engineering"
date: "2024-07-02"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Professional
    - Resource Development & Initial Access
    - Client-Side Attacks
---

# Introduction To Social Engineering 社交工程简介
## What is Social Engineering? 什么是社交工程？
● In the context of penetration testing and red teaming, social engineering is a technique used to manipulate individuals or employees within an organization to gain unauthorized access to sensitive information, systems, or facilities.

● It exploits human psychology, trust, and vulnerabilities to deceive targets into performing actions that compromise security, either through information disclosure or by performing specific actions that may seem innocuous at first glance.

● Social engineering attacks aim to bypass technical controls by targeting the weakest link in the security chain: the human element.

● 在渗透测试和红队行动的背景下，社交工程是一种用于操纵组织内的个人或员工以获取敏感信息、系统或设施的未授权访问的技术。

● 它利用人类心理、信任和弱点来欺骗目标，使其执行威胁安全的行为，这些行为可能通过泄露信息或执行看似无害的特定动作。

● 社交工程攻击的目标是绕过技术控制，通过针对安全链中最薄弱的环节：人的因素。

● The premise of social engineering is to exploit the human element, in other words, putting people or employees in situations where they will rely on their base instincts and most common forms of social interaction like:

+ The desire to be helpful
+ The tendency to trust people
+ The desire for approval
+ The fear of getting in trouble
+ Avoiding conflict or arguments

● 社交工程的基本前提是利用人的因素，换句话说，就是将人或员工置于依赖于他们基本本能和最常见的社交互动形式的情境中，例如：

+ 想要帮助别人的愿望
+ 信任他人的倾向
+ 对获得认可的渴望
+ 害怕惹麻烦的恐惧
+ 避免冲突或争论

● By preying on the human element of system access, most times, attackers do not have to navigate around the security perimeter of an organization.

● Attackers/Pentesters just need to engage with employees inside the company to do their bidding for them.

● Instead of spending countless hours trying to infiltrate systems/networks through traditional server-side attacks like brute-force attacks, attackers can leverage social engineering to yield information or facilitate the execution of malware inside the company network in a matter of minutes.

● 通过利用系统访问的人为因素，攻击者大多数情况下无需绕过组织的安全边界。

● 攻击者/渗透测试者只需要与公司内部的员工交流，让他们为自己服务。

● 攻击者可以利用社交工程在几分钟内获得信息或促进恶意软件在公司网络内的执行，而不是花费无数小时尝试通过传统的服务器端攻击（如暴力破解）渗透系统/网络。

## Social Engineering & Social Media 社交工程与社交媒体
● The advent and adoption of Social Networking as a form of communication has vastly improved the ability and effectiveness of attackers (likewise pentesters) to perform social engineering attacks as employees/targets can be easily contacted by anyone in the world with ease.

● Furthermore, Social Networks have also led to the rise of employees advertently/inadvertently exposing a lot of private information that can be used by attackers in aid of their social engineering attacks (Emails, phone numbers, addresses etc).

● 社交网络作为一种通信形式的出现和采用，极大地提高了攻击者（同样也包括渗透测试者）执行社交工程攻击的能力和效果，因为员工/目标可以很容易地被世界上任何人轻松联系到。

● 此外，社交网络还导致员工有意或无意地暴露了许多可以被攻击者用来辅助其社交工程攻击的私人信息（如电子邮件、电话号码、地址等）。

## History of Social Engineering 社交工程的历史
● While many cybersecurity professionals think of social engineering as a technique exclusive to offensive security, that couldn’t be farther from the truth.

● Social engineering is a practice that is as old as time. As long as there has been coveted information, there have been people seeking to exploit it.

● The term social engineering was first coined by Dutch industrialist J.C. Van Marken in 1894. Van Marken suggested that specialists were needed to attend to human challenges in addition to technical ones.

● Social Engineering was defined as a way to encourage people to handle social relations similarly to how they approach machines/mechanical systems.

● 尽管许多网络安全专业人士认为社交工程是一种专属于进攻性安全的技术，但事实远非如此。

● 社交工程是一项古老的实践。只要有令人垂涎的信息，就会有人试图利用它。

● “社交工程”这一术语最早由荷兰实业家J.C. Van Marken在1894年提出。Van Marken建议除了技术问题外，还需要专家来应对人类挑战。

● 社交工程被定义为一种鼓励人们以类似对待机器/机械系统的方式处理社会关系的方法。

## Social Engineering & Pentesting 社交工程与渗透测试
● While social engineering has been a very viable attack vector for attackers, it has often been overlooked by penetration testers until recently.

● Contextualizing and operationalizing social engineering as a valid attack vector in penetration testing is a vital skill set to possess as a modern penetration tester.

● In penetration testing and red teaming exercises, phishing simulations are valuable for assessing an organization's susceptibility to social engineering attacks and identifying areas for improvement in security awareness and controls.

● 尽管社交工程一直是攻击者的一个非常可行的攻击途径，但直到最近，它常常被渗透测试者忽视。

● 将社交工程作为渗透测试中一个有效的攻击向量进行情境化和操作化，是现代渗透测试者必须具备的重要技能。

● 在渗透测试和红队演练中，网络钓鱼模拟对于评估组织对社交工程攻击的易感性及识别安全意识和控制措施的改进领域非常有价值。

## Types of Social Engineering
| Technique       | Description                                                                                                                                                                   |
|-----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Phishing        | Deceptive emails, messages, or websites designed to trick recipients into revealing confidential information, such as passwords, account credentials, or financial data.        |
| Spear Phishing  | Targeted phishing attacks that are customized for specific individuals or groups within an organization, often using personalized information or context to increase credibility. |
| Vishing         | Phishing attacks conducted over phone calls or voice messages, where attackers impersonate legitimate entities (e.g., IT support, bank representatives) to extract sensitive information or manipulate victims into taking specific actions. |
| Smishing        | Phishing attacks conducted via SMS or text messages, where recipients are tricked into clicking on malicious links or providing sensitive information by impersonating trusted entities. |
| Pretexting      | Creating a false pretext or scenario to gain the trust of targets and extract sensitive information. This may involve impersonating authority figures, colleagues, or service providers to manipulate victims into divulging confidential data. |
| Baiting         | Luring targets into performing a specific action (e.g., clicking on a malicious link, opening a malicious file) by offering enticing incentives or rewards, such as free software, prizes, or job opportunities. |
| Tailgating      | Physically following authorized individuals into restricted areas or facilities without proper authentication. Attackers exploit social norms or courtesy to gain unauthorized access to secure locations.                           |



| 技术              | 描述                                                                                                                                                                                     |
|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 网络钓鱼（Phishing） | 设计用来欺骗收件人的欺诈性电子邮件、信息或网站，目的是让收件人透露机密信息，如密码、账户凭证或财务数据。                                                                                               |
| 针对性网络钓鱼（Spear Phishing） | 针对组织内特定个人或团体的定制化网络钓鱼攻击，常使用个人化信息或上下文来增加可信度。                                                                                                               |
| 语音钓鱼（Vishing） | 通过电话通话或语音消息进行的网络钓鱼攻击，攻击者冒充合法实体（例如，IT支持、银行代表），以提取敏感信息或操纵受害者执行特定行为。                                                                                    |
| 短信钓鱼（Smishing） | 通过短信或文本消息进行的网络钓鱼攻击，受害者被诱导点击恶意链接或提供敏感信息，攻击者通过冒充可信实体来实施欺骗。                                                                                                   |
| 虚构事实（Pretexting） | 创建虚假的前提或情景来获得目标的信任并提取敏感信息。这可能涉及冒充权威人物、同事或服务提供者，以操纵受害者泄露机密数据。                                                                                               |
| 诱饵（Baiting）      | 通过提供诱人的激励或奖励（例如免费软件、奖品或工作机会）诱使目标执行特定行为（例如点击恶意链接、打开恶意文件）。                                                                                                 |
| 尾随（Tailgating）   | 在没有适当身份验证的情况下，物理上跟随授权个体进入限制区域或设施。攻击者利用社会规范或礼貌来获得进入安全位置的未授权访问。                                                                                           |


## Phishing 网络钓鱼
● Phishing is one of the most prevalent and effective social engineering attacks used in penetration testing and red teaming. It typically involves the following steps:

1. Planning & Reconnaissance: Attackers research the target organization to identify potential targets, gather information about employees, and understand the organization's communication channels and protocols.
2. Message Crafting: Attackers create deceptive emails or messages designed to mimic legitimate communications from trusted sources, such as colleagues, IT departments, or financial institutions. These messages often include urgent or compelling language to evoke a sense of urgency or fear.
3. Delivery: Attackers send phishing emails or messages to targeted individuals within the organization, using techniques to bypass spam filters and security controls. They may also leverage social engineering tactics to increase the likelihood of recipients opening the messages.
4. Deception & Manipulation: The phishing messages contain malicious links, attachments, or requests for sensitive information. Recipients are deceived into clicking on links, downloading attachments, or providing login credentials under false pretenses.
5. Exploitation: Once the victim interacts with the phishing message, attackers exploit vulnerabilities in the target's systems or applications to gain unauthorized access, install malware, or steal sensitive information.

● 网络钓鱼是渗透测试和红队行动中使用最广泛和最有效的社交工程攻击方式之一。它通常包括以下步骤：

1. 计划与侦察：攻击者研究目标组织，以识别潜在目标，收集有关员工的信息，并了解组织的通信渠道和协议。
2. 信息制作：攻击者创建设计精巧的电子邮件或信息，模仿来自受信任来源的合法通信，如同事、IT部门或金融机构。这些信息通常包含紧急或引人注目的语言，以引发紧迫感或恐惧。
3. 投递：攻击者向组织内的目标个体发送网络钓鱼电子邮件或信息，使用技术绕过垃圾邮件过滤器和安全控制。他们也可能利用社交工程策略增加收件人打开信息的可能性。
4. 欺骗与操纵：钓鱼信息包含恶意链接、附件或对敏感信息的请求。收件人在虚假前提下被欺骗点击链接、下载附件或提供登录凭证。
5. 利用：一旦受害者与钓鱼信息互动，攻击者就会利用目标系统或应用中的漏洞获得未授权的访问权限，安装恶意软件或窃取敏感信息。

## Spear-Phishing 针对性网络钓鱼
● Spear phishing is a targeted form of phishing attack that tailors malicious emails or messages to specific individuals or groups within an organization.

● Unlike traditional phishing attacks, which cast a wide net and aim to deceive as many recipients as possible, spear phishing attacks are highly personalized and customized to exploit the unique characteristics, interests, and relationships of the intended targets.

● 针对性网络钓鱼是一种针对组织内特定个人或团体定制恶意电子邮件或信息的网络钓鱼攻击形式。

● 与传统的网络钓鱼攻击不同，传统网络钓鱼攻击广撒网，旨在欺骗尽可能多的收件人，而针对性网络钓鱼攻击则高度个性化和定制化，利用目标的独特特征、兴趣和关系进行攻击。

## Spear-Phishing Process 针对性网络钓鱼流程
1. Target Selection & Research:

+ Attackers carefully select their targets based on specific criteria, such as job roles, departments, or organizational hierarchies.
+ Extensive reconnaissance is conducted to gather information about the targets, including names, job titles, roles, responsibilities, work relationships, and personal interests.
+ Publicly available sources, social media profiles, corporate directories, and leaked data may be mined to compile detailed profiles of the targets.

1. 目标选择与研究：

+ 攻击者根据特定标准仔细选择目标，如职位、部门或组织层级。
+ 进行广泛的侦察，收集有关目标的信息，包括姓名、职位、角色、职责、工作关系和个人兴趣。
+ 公开可用的来源、社交媒体档案、公司目录和泄露的数据可能被挖掘，以编制目标的详细档案。

2. Message Tailoring:

+ Using the gathered information, attackers craft highly personalized and convincing emails or messages designed to appear legitimate and trustworthy.
+ The content of the messages may reference recent events, projects, or activities relevant to the target's role or interests to enhance credibility.
+ Attackers may impersonate trusted individuals, such as colleagues, supervisors, or external partners, to increase the likelihood of the targets opening the messages and taking the desired actions.

2. 信息定制：

+ 利用收集到的信息，攻击者制作高度个性化且令人信服的电子邮件或信息，设计成看起来合法且可信的样子。
+ 信息的内容可能提及与目标的角色或兴趣相关的最近事件、项目或活动，以增强可信度。
+ 攻击者可能冒充受信任的个人，如同事、上级或外部合作伙伴，以增加目标打开信息并执行期望行动的可能性。

3. Delivery:

+ Spear phishing messages are delivered to the targeted individuals via email, social media, instant messaging platforms, or other communication channels.
+ Attackers employ tactics to bypass email security filters and antiphishing mechanisms, such as using compromised or spoofed email accounts, exploiting zero-day vulnerabilities, or leveraging trusted thirdparty services.

3. 投递：

+ 针对性网络钓鱼信息通过电子邮件、社交媒体、即时通讯平台或其他通信渠道发送给目标个人。
+ 攻击者采用策略绕过电子邮件安全过滤器和反钓鱼机制，如使用被攻破或伪造的电子邮件账户，利用零日漏洞，或借助受信任的第三方服务。