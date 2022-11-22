---
layout: post
title: "Metasploit Framework Architecture"
date: "2022-11-22"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Metasploit Framework Architecture
## MSF Architecture
A module in the context of MSF, is a piece of code that can be utilized by the MSF.

The MSF libraries facilitate the execution of modules without having to write the code necessary in order to execute them.

![MSF-Architecture.png](/img/in-post/ine/MSF-Architecture.png)

# Metasploit 框架架构
## MSF架构
MSF 上下文中的模块是 MSF 可以使用的一段代码。

MSF 库有助于模块的执行，而无需编写执行它们所需的代码。

## MSF Modules
Exploit - A module that is used to take advantage of vulnerability and is typically paired with a payload.

Payload - Code that is delivered by MSF and remotely executed on the target after successful exploitation. An example of a payload is a reverse shell that initiates a connection from the target system back to the attacker.

Encoder - Used to encode payloads in order to avoid AV detection. For example, shikata_ga_nai is used to encode Windows payloads.

NOPS - Used to ensure that payloads sizes are consistent and ensure the stability of a payload when executed.

Auxiliary - A module that is used to perform additional functionality like port scanning and enumeration.

## MSF 模块
Exploit - 用于利用漏洞的模块，通常与有效负载配对。

Payload - 由 MSF 交付并在成功利用后在目标上远程执行的代码。 有效负载的一个示例是反向 shell，它启动从目标系统返回到攻击者的连接。

Encoder - 用于对有效载荷进行编码以避免 AV 检测。 例如，shikata_ga_nai 用于对 Windows 有效负载进行编码。

NOPS - 用于确保负载大小一致，并确保负载在执行时的稳定性。

Auxiliary - 用于执行端口扫描和枚举等附加功能的模块。

## MSF Payload Types
When working with exploits, MSF provides you with two types of payloads that can be paired with an exploit:

Non-Staged Payload - Payload that is sent to the target system as is along with the exploit.

Staged Payload - A staged payload is sent to the target in two parts, whereby:

The first part (stager) contains a payload that is used to establish a reverse connection back to the attacker, download the second part of the payload (stage) and execute it.

## MSF 负载类型
在处理漏洞利用时，MSF 为您提供了两种类型的可与漏洞利用配对的有效负载：

Non-Staged Payload - 与漏洞利用一起发送到目标系统的有效负载。

分阶段有效载荷 - 分阶段有效载荷分两部分发送到目标，其中：

第一部分（stager）包含一个有效载荷，用于建立反向连接返回给攻击者，下载有效载荷的第二部分（stage）并执行它。

## Stagers & Stages
Stagers - Stagers are typically used to establish a stable communication channel between the attacker and target, after which a stage payload is downloaded and executed on the target system.

Stage - Payload components that are downloaded by the stager.

## Stagers & Stages
Stagers - Stagers 通常用于在攻击者和目标之间建立稳定的通信通道，之后在目标系统上下载并执行stage有效负载。

Stage - 由 stager 下载的有效负载组件。

## Meterpreter Payload
The Meterpreter (Meta-Interpreter) payload is an advanced multi-functional payload that is executed in memory on the target system making it difficult to detect.

It communicates over a stager socket and provides an attacker with an interactive command interpreter on the system that facilitates the execution of system commands, file system navigation, keylogging and much more.

## Meterpreter 载荷
Meterpreter (Meta-Interpreter) 有效载荷是一种高级多功能有效载荷，它在目标系统的内存中执行，因此难以检测。

它通过 stager 套接字进行通信，并为攻击者提供系统上的交互式命令解释器，以便于执行系统命令、文件系统导航、键盘记录等。

## MSF File System Structure
The MSF file system is organized in a simple and easy to understand format and is organized into various directories.

![msf-file-system-structure.png](/img/in-post/ine/msf-file-system-structure.png)

## MSF 文件系统结构
MSF 文件系统以简单易懂的格式组织，并组织到各种目录中。

## MSF Module Locations
MSF stores modules under the following directory on Linux systems:

`/usr/share/metasploit-framework/modules`

User specified modules are stored under the following directory on Linux systems:

`~/.ms4/modules`

## MSF 模块位置
MSF 将模块存储在 Linux 系统的以下目录下：

`/usr/share/metasploit-framework/modules`

用户指定的模块在 Linux 系统上存储在以下目录下：

`~/.ms4/modules`

# Demo: MSF File System Structure（演示：MSF 文件系统结构）
Navigate to MSF default stored directory:  

```
kali@kali:/usr/share/metasploit-framework$
```

File Browser: `File System->usr->share->metasploit-framework->modules`

```
/usr/share/metasploit-framework/modules/auxiliary/
```

Perform infomation gathering:

```
/usr/share/metasploit-framework/modules/auxiliary/scanner/
/usr/share/metasploit-framework/modules/auxiliary/scanner/ftp/
/usr/share/metasploit-framework/modules/auxiliary/scanner/discovery/
/usr/share/metasploit-framework/modules/auxiliary/scanner/smb/
```

```
/usr/share/metasploit-framework/modules/exploits/
/usr/share/metasploit-framework/modules/exploits/linux/
/usr/share/metasploit-framework/modules/exploits/linux/mysql/
/usr/share/metasploit-framework/modules/exploits/linux/ftp/
```

```
/usr/share/metasploit-framework/modules/exploits/windows/
/usr/share/metasploit-framework/modules/exploits/windows/mssql/
```

```
/usr/share/metasploit-framework/modules/nops/
/usr/share/metasploit-framework/modules/encoders/
/usr/share/metasploit-framework/modules/encoders/x86/
/usr/share/metasploit-framework/modules/encoders/x86/shikata_ga_nai.rb # used to encode Windows payload
```

Load your own customer module. `View->Show Hidden Files (Ctrl+H)`

```
/home/kali/.msf4/modules/
```

```
kali@kali:/usr/share/metasploit-framework$ cd modules/
kali@kali:/usr/share/metasploit-framework/modules$ ls
```




