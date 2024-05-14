---
layout: post
title: "Getting Comfortable With PowerShell"
date: "2024-05-14"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Professional
    - Resource Development & Initial Access
    - PowerShell for Pentesters
---

# Getting Comfortable With PowerShell
## Demo
`Win+E` -> This PC -> Properties

系统类型：64 位操作系统, 基于 x64 的处理器

Launch Powershell

search for Powershell on toolbox

Right click -> Run as Administrator

```
PS C:\Windows\system32>
```

Find the PowerShell executable by navegative `C:\Windows\System32\WindowsPowerShell\v1.0`

```
PS C:\Windows\System32\WindowsPowerShell\v1.0>
```

Check whether we are running a 32-bit or 64-bit PowerShell environment.

Readability: Properties -> Font

```
PS C:\Windows\System32\WindowsPowerShell\v1.0> [Environment]::Is64BitProcess
True
```

Search PowerShell and launch the 32 bit version.

```
PS C:\Users\Lenovo> [Environment]::Is64BitProcess
False
```

Bring up the help menu.

```
PS C:\Windows\System32\WindowsPowerShell\v1.0> powershell /?
PS C:\Windows\System32\WindowsPowerShell\v1.0> powershell -Help
PS C:\Windows\System32\WindowsPowerShell\v1.0> powershell -?
```

```
PS C:\Windows\System32\WindowsPowerShell\v1.0> powershell.exe -ExecutionPolicy Bypass cmd.exe
Microsoft Windows [版本 10.0.19045.4291]
(c) Microsoft Corporation。保留所有权利。

C:\Windows\System32\WindowsPowerShell\v1.0>
```

```
C:\Windows\System32\WindowsPowerShell\v1.0>powershell
Windows PowerShell
版权所有 (C) Microsoft Corporation。保留所有权利。

尝试新的跨平台 PowerShell https://aka.ms/pscore6

PS C:\Windows\System32\WindowsPowerShell\v1.0>
```

```
PS C:\Windows\System32\WindowsPowerShell\v1.0> powershell.exe -ExecutionPolicy Bypass calc.exe
PS C:\Windows\System32\WindowsPowerShell\v1.0> powershell.exe -ExecutionPolicy Unrestricted calc.exe
```

Create a Powershell script that launches calculator.

calc.ps1

```
powershell.exe calc.exe
```

It brings up the blue powershell Windows when executing the script. We want to make sure that that is hidden.

在32位的PowerShell中可以运行64位的计算器，但是需要设置ExecutionPolicy为Bypass。执行下面命令，会隐藏PowerShell窗口，并弹出计算机。

```
PS C:\Users\Lenovo> [Environment]::Is64BitProcess
False
PS C:\Users\Lenovo> cd .\Desktop\
PS C:\Users\Lenovo\Desktop> powershell.exe -ep Bypass .\calc.ps1
PS C:\Users\Lenovo\Desktop> powershell.exe -ep Bypass -WindowStyle Hidden .\calc.ps1
```

在64位的PowerShell中可以运行64位的计算器。执行下面命令，隐藏PowerShell窗口，并弹出计算机。注意，这与管理员权限无关，此时并没有以管理员权限运行。

```
PS C:\Users\Lenovo> [Environment]::Is64BitProcess
True
PS C:\Users\Lenovo> powershell.exe -WindowStyle Hidden .\Desktop\calc.ps1
```

在命令行或者是在ps1文件中指定`-WindowStyle Hidden`，都能达到相同的效果，隐藏PowerShell窗口。

```
PS C:\Users\Lenovo> [Environment]::Is64BitProcess
True
PS C:\Users\Lenovo> powershell.exe -Command Get-Process
```

```
PS C:\Users\Lenovo> powershell.exe -Command "& { Get-EventLog -LogName security }"
Get-EventLog : 不允许所请求的注册表访问权。
所在位置 行:1 字符: 5
+ & { Get-EventLog -LogName security }
+     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Get-EventLog], SecurityException
    + FullyQualifiedErrorId : System.Security.SecurityException,Microsoft.PowerShell.Commands.GetEventLogCommand
```

That could ever mean that we don't have the correct privileges.

```
PS C:\Users\Lenovo> powershell.exe -ep Unrestricted -Command "& { Get-EventLog -LogName security }"
Get-EventLog : 不允许所请求的注册表访问权。
```

Run as Administrator

```
PS C:\Windows\system32> powershell.exe -ep Unrestricted -Command "& { Get-EventLog -LogName security }"
```

```
PS C:\Users\Lenovo> $command = 'dir "c:\program files" '
PS C:\Users\Lenovo> $bytes = [System.Text.Encoding]::Unicode.GetBytes($command)
PS C:\Users\Lenovo> $bytes
100
0
105
0
114
0
32
0
34
0
99
0
58
0
92
0
112
0
114
0
111
0
103
0
114
0
97
0
109
0
32
0
102
0
105
0
108
0
101
0
115
0
34
0
32
0
PS C:\Users\Lenovo> $encodedCommand = [Convert]::ToBase64String($bytes)
PS C:\Users\Lenovo> $encodedCommand
ZABpAHIAIAAiAGMAOgBcAHAAcgBvAGcAcgBhAG0AIABmAGkAbABlAHMAIgAgAA==
PS C:\Users\Lenovo> powershell.exe -encodedCommand $encodedCommand


    目录: C:\program files


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         2023/5/24     16:58                Common Files
d-----         2024/5/13      0:12                Internet Explorer
d-----         2024/5/10     16:38                Microsoft Update Health Tools
d-----         2019/12/7     17:14                ModifiableWindowsApps
d-----         2024/5/12     11:04                RUXIM
d-----         2023/5/24     16:58                VMware
d-----         2024/5/13      0:12                Windows Defender
d-----         2024/5/13      0:12                Windows Defender Advanced Threat Protection
d-----         2024/5/13      0:12                Windows Mail
d-----         2024/5/13      0:12                Windows Media Player
d-----         2019/12/7     22:47                Windows Multimedia Platform
d-----         2023/5/24     16:56                Windows NT
d-----         2024/5/13      0:12                Windows Photo Viewer
d-----         2019/12/7     22:47                Windows Portable Devices
d-----         2019/12/7     17:31                Windows Security
d-----         2019/12/7     17:31                WindowsPowerShell
```

Get all functions related to firewall functionality.

```
PS C:\Users\Lenovo> Get-Command -Name *Firewall*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Function        Copy-NetFirewallRule                               2.0.0.0    NetSecurity
Function        Disable-NetFirewallRule                            2.0.0.0    NetSecurity
Function        Enable-NetFirewallRule                             2.0.0.0    NetSecurity
Function        Get-NetFirewallAddressFilter                       2.0.0.0    NetSecurity
```

